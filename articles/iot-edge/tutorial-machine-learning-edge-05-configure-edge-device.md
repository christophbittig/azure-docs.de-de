---
title: 'Tutorial: Konfigurieren eines Azure IoT Edge-Geräts: Machine Learning in IoT Edge'
description: In diesem Tutorial konfigurieren Sie einen virtuellen Azure-Computer unter Linux als Azure IoT Edge-Gerät, das als transparentes Gateway fungiert.
author: kgremban
ms.author: kgremban
ms.date: 2/5/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: amqp
ms.openlocfilehash: 445163b0d33f404845a66b7d885815d6336dac46
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121726362"
---
# <a name="tutorial-configure-an-azure-iot-edge-device"></a>Tutorial: Konfigurieren eines Azure IoT Edge-Geräts

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

In diesem Artikel konfigurieren wir einen virtuellen Azure-Computer unter Linux als Azure IoT Edge-Gerät, das als transparentes Gateway fungiert. Die Konfiguration eines transparenten Gateways ermöglicht es Geräten, eine Verbindung mit Azure IoT Hub über das Gateway herzustellen, ohne zu wissen, dass das Gateway vorhanden ist. Gleichzeitig ist auch ein Benutzer, der mit den Geräten in IoT Hub interagiert, nicht über das zwischengeschaltete Gatewaygerät informiert. Am Ende fügen wir unserem System eine Edgeanalyse hinzu, indem wir dem transparenten Gateway IoT Edge-Module hinzufügen.

>[!NOTE]
>Die Konzepte in diesem Tutorial gelten für alle Versionen von IoT Edge. Auf dem Beispielgerät, das Sie zum Ausprobieren des Szenarios erstellen, wird jedoch Version 1.1. von IoT Edge ausgeführt.

Die Schritte in diesem Artikel werden normalerweise von einem Cloudentwickler ausgeführt.

In diesem Abschnitt des Tutorials lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Erstellen von Zertifikaten, um Ihrem Gatewaygerät das Herstellen einer sicheren Verbindung mit den nachgeschalteten Geräten zu ermöglichen
> * Erstellen eines IoT Edge-Geräts
> * Erstellen eines virtuellen Azure-Computers zum Simulieren des IoT Edge-Geräts

## <a name="prerequisites"></a>Voraussetzungen

Dieser Artikel ist Teil einer Tutorialreihe zur Verwendung von Azure Machine Learning für IoT Edge. Jeder Artikel in der Reihe baut auf der Arbeit im vorherigen Artikel auf. Wenn Sie diesen Artikel direkt aufgerufen haben, wechseln Sie zum [ersten Artikel](tutorial-machine-learning-edge-01-intro.md) in der Reihe.

## <a name="create-certificates"></a>Erstellen von Zertifikaten

Damit ein Gerät als Gateway fungieren kann, muss es eine sichere Verbindung mit nachgeschalteten Geräten herstellen können. Mit IoT Edge können Sie mithilfe der Public Key-Infrastruktur (PKI) sichere Verbindungen zwischen Geräten einrichten. In diesem Fall lassen Sie zu, dass ein nachgeschaltetes IoT-Gerät eine Verbindung mit einem IoT Edge-Gerät herstellt, das als transparentes Gateway fungiert. Um eine angemessene Sicherheit zu gewährleisten, sollte das nachgeschaltete Gerät die Identität des IoT Edge-Geräts bestätigen. Weitere Informationen dazu, wie IoT Edge-Geräte Zertifikate verwenden, finden Sie unter [Details zur Verwendung von Azure IoT Edge-Zertifikaten](iot-edge-certs.md).

In diesem Abschnitt erstellen wir die selbstsignierten Zertifikate mithilfe eines Docker-Images, das wir anschließend erstellen und ausführen. Wir haben uns bei diesem Schritt für ein Docker-Image entschieden, weil sich hierdurch der Aufwand zum Erstellen der Zertifikate auf dem Windows-Entwicklungscomputer verringert. Informationen zu den mit dem Docker-Image automatisierten Komponenten finden Sie unter [Erstellen von Demozertifikaten zum Testen der Features von IoT Edge-Geräten](how-to-create-test-certificates.md).

1. Melden Sie sich bei Ihrem virtuellen Entwicklungscomputer an.
1. Erstellen Sie folgenden neuen Ordner: **c:\edgeCertificates**.

1. Wenn **Docker für Windows** noch nicht ausgeführt wird, starten Sie die Anwendung über das Windows-Startmenü.

1. Öffnen Sie Visual Studio Code.

1. Klicken Sie auf **Datei** > **Ordner öffnen**, und wählen Sie **C:\\source\\IoTEdgeAndMlSample\\CreateCertificates** aus.

1. Klicken Sie im **Explorer**-Bereich mit der rechten Maustaste auf **dockerfile**, und wählen Sie **Image erstellen** aus.

1. Übernehmen Sie im Dialogfeld den Standardwert für Imagename und Tag: **createcertificates: latest**.

    ![Screenshot: Erstellen von Zertifikaten in Visual Studio Code](media/tutorial-machine-learning-edge-05-configure-edge-device/create-certificates.png)

1. Warten Sie, bis der Buildvorgang abgeschlossen wird.

    > [!NOTE]
    > Möglicherweise wird eine Warnung zu einem fehlenden öffentlichen Schlüssel angezeigt. Sie können diese Warnung ignorieren. Sie sehen wahrscheinlich auch eine Sicherheitswarnung mit dem Hinweis, dass Sie die Berechtigungen auf Ihrem Image überprüfen bzw. zurücksetzen sollten. Sie können diesen Hinweis für dieses Image ignorieren.

1. Führen Sie im Visual Studio Code-Terminalfenster den Container „createcertificates“ aus.

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

1. Docker fordert Sie auf, Zugriff auf das Laufwerk **c:\\** zu gewähren. Wählen Sie **Teilen**.

1. Geben Sie Ihre Anmeldeinformationen ein, wenn Sie dazu aufgefordert werden.

1. Überprüfen Sie nach Abschluss der Containerausführung, ob die folgenden Dateien unter **c:\\edgeCertificates** vorhanden sind:

    * c:\\edgeCertificates\\certs\\azure-iot-test-only.root.ca.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device-full-chain.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pfx
    * c:\\edgeCertificates\\private\\new-edge-device.key.pem

## <a name="upload-certificates-to-azure-key-vault"></a>Hochladen von Zertifikaten in Azure Key Vault

Wir laden die Zertifikate in Azure Key Vault hoch, um sie sicher zu speichern und den Zugriff darauf von mehreren Geräten aus zu ermöglichen. Wie Sie in der Liste oben sehen, gibt es zwei Arten von Zertifikatdateien: PFX und PEM. Wir behandeln die PFX-Dateien als Key Vault-Zertifikate, die in Key Vault hochgeladen werden sollen. Die PEM-Dateien sind Nur-Text-Dateien und werden als Key Vault-Geheimnisse behandelt. Wir verwenden die Key Vault-Instanz, die dem Azure Machine Learning-Arbeitsbereich zugeordnet ist, den wir durch Ausführen der [Jupyter-Notebooks](tutorial-machine-learning-edge-04-train-model.md#run-the-jupyter-notebooks) erstellt haben.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem Azure Machine Learning-Arbeitsbereich.

1. Suchen Sie auf der Übersichtsseite des Azure Machine Learning-Arbeitsbereichs nach dem Namen der **Key Vault-Instanz**.

    ![Screenshot: Kopieren des Namens der Key Vault-Instanz](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

1. Laden Sie auf Ihrem Entwicklungscomputer die Zertifikate in Key Vault hoch. Ersetzen Sie **\<subscriptionId\>** und **\<keyvaultname\>** durch Ihre Ressourceninformationen.

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

1. Melden Sie sich bei entsprechender Aufforderung bei Azure an.

1. Das Skript wird einige Minuten lang ausgeführt, und in der Ausgabe sind die neuen Key Vault-Einträge aufgelistet.

    ![Screenshot: Ausgabe des Key Vault-Skripts](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="register-an-iot-edge-device"></a>Registrieren eines IoT Edge-Geräts

Zum Verbinden eines Azure IoT Edge-Geräts mit einem IoT-Hub registrieren wir zuerst ein Gerät im Hub. Wir verwenden die Verbindungszeichenfolge aus der Geräteidentität in der Cloud und nutzen sie zum Konfigurieren der Runtime auf unserem IoT Edge-Gerät. Nachdem ein konfiguriertes Gerät eine Verbindung mit dem Hub hergestellt hat, können wir Module bereitstellen und Nachrichten senden. Wir können auch die Konfiguration des physischen IoT Edge-Geräts ändern, indem wir die entsprechende Geräteidentität in IoT Hub ändern.

Für dieses Tutorial registrieren wir die neue Geräteidentität mithilfe von Visual Studio Code. Sie können diese Schritte auch im Azure-Portal oder in der Azure CLI ausführen. Sorgen Sie unabhängig von der ausgewählten Methode dafür, dass Sie die Geräteverbindungszeichenfolge für Ihr IoT Edge-Gerät abrufen. Die Geräteverbindungszeichenfolge finden Sie im Azure-Portal auf der Detailseite für das Gerät.

1. Öffnen Sie Visual Studio Code auf Ihrem Entwicklungscomputer.

1. Erweitern Sie in der **Explorer**-Ansicht von Visual Studio Code den Bereich **Azure IoT Hub**.

1. Klicken Sie auf die Auslassungspunkte, und wählen Sie **IoT Edge-Gerät erstellen** aus.

1. Geben Sie dem Gerät einen Namen. Der Einfachheit halber verwenden wir den Namen **aaTurbofanEdgeDevice**, da diesen an den Anfang der Geräteliste sortiert wird.

1. Das neue Gerät wird in der Geräteliste angezeigt.

    ![Screenshot: Ansicht des Geräts im Visual Studio Code-Explorer](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-an-azure-virtual-machine"></a>Bereitstellen eines virtuellen Azure-Computers

Wir verwenden einen virtuellen Ubuntu 18.04 LTS-Computer mit installierter und konfigurierter Azure IoT Edge-Runtime. Die Bereitstellung verwendet eine [Azure Resource Manager-Vorlage](../azure-resource-manager/templates/overview.md), die im Projektrepository [iotedge-vm-deploy](https://github.com/Azure/iotedge-vm-deploy) verwaltet wird. Sie stellt das IoT Edge-Gerät bereit, das Sie im vorherigen Schritt registriert haben, und verwendet dafür die von Ihnen in der Vorlage angegebene Verbindungszeichenfolge.

Sie können den virtuellen Computer über das Azure-Portal oder Azure CLI bereitstellen. Wir zeigen die Azure-Portalschritte an. Weitere Informationen finden Sie unter [Ausführen von Azure IoT Edge auf virtuellen Ubuntu-Computern](how-to-install-iot-edge-ubuntuvm.md).

### <a name="deploy-using-deploy-to-azure-button"></a>Bereitstellen mithilfe der Schaltfläche „In Azure bereitstellen“

1. Wenn Sie die ARM-Vorlage `iotedge-vm-deploy` zum Bereitstellen Ihres virtuellen Ubuntu 18.04 LTS-Computers verwenden möchten, klicken Sie auf die folgende Schaltfläche:

    [![Schaltfläche „In Azure bereitstellen“ für „iotedge-vm-deploy“](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)

1. Füllen Sie im neu gestarteten Fenster die verfügbaren Formularfelder aus.

   | Feld | BESCHREIBUNG |
   | - | - |
   | **Abonnement** | Das aktive Azure-Abonnement, in dem der virtuelle Computer bereitgestellt werden soll. |
   | **Ressourcengruppe** | Eine vorhandene oder neu erstellte Ressourcengruppe, die den virtuellen Computer und die ihm zugeordneten Ressourcen enthalten soll. |
   | **Präfix der DNS-Bezeichnung** | Ein erforderlicher Wert Ihrer Wahl, der dem Hostnamen des virtuellen Computers vorangestellt werden soll. |
   | **Benutzername des Administrators** | Ein Benutzername, der Rootberechtigungen für die Bereitstellung erhält. |
   | **Verbindungszeichenfolge für das Gerät** | Eine [Geräte-Verbindungszeichenfolge](./how-to-register-device.md) für ein Gerät, das in Ihrem vorgesehenen [IoT Hub](../iot-hub/about-iot-hub.md) erstellt wurde. |
   | **VM-Größe** | Die [Größe](../cloud-services/cloud-services-sizes-specs.md) des bereitzustellenden virtuellen Computers
   | **Ubuntu-Betriebssystemversion** | Die Version des Ubuntu-Betriebssystems, die auf dem virtuellen Basiscomputer installiert werden soll. |
   | **Location** | Die [geografische Region](https://azure.microsoft.com/global-infrastructure/locations/), in der der virtuelle Computer bereitgestellt werden soll. Dieser Wert wird standardmäßig auf den Speicherort der ausgewählten Ressourcengruppe festgelegt. |
   | **Authentifizierungstyp** | Wählen Sie je nach Ihrer Einstellung **sshPublicKey** oder **password** aus. |
   | **Administratorkennwort oder Schlüssel** | Der Wert des öffentlichen SSH-Schlüssels oder der Wert des Kennworts, je nach der Auswahl des Authentifizierungstyps. |

1. Wenn alle Felder ausgefüllt sind, aktivieren Sie unten auf der Seite das Kontrollkästchen zum Akzeptieren der Bedingungen. Wählen Sie dann **Überprüfen + erstellen** und **Erstellen** aus, um mit der Bereitstellung zu beginnen.

1. Navigieren Sie im Azure-Portal zu Ihrem virtuellen Computer. Sie finden ihn über Ihre Ressourcengruppe oder, indem Sie auf der Landing Page des Portals unter **Azure-Dienste** die Option **Virtuelle Computer** auswählen.

1. Notieren Sie sich den **DNS-Namen** Ihres virtuellen Computers. Sie benötigen ihn für die Anmeldung bei Ihrem virtuellen Computer.

## <a name="connect-to-your-iot-edge-device"></a>Herstellen einer Verbindung mit Ihrem IoT Edge-Gerät

1. Öffnen Sie eine Eingabeaufforderung, und melden Sie sich mit dem folgenden Befehl bei Ihrem virtuellen Computer an. Geben Sie ihre eigenen Informationen für „Benutzername“ und „DNS-Name“ ein, die auf dem vorherigen Abschnitt basieren.

    ```bash
    ssh <adminUsername>@<DNS_name>
    ```

1. Geben Sie **yes** ein, und drücken Sie die **EINGABETASTE**, wenn Sie zur Bestätigung der Echtheit des Hosts aufgefordert werden.

1. Geben Sie bei entsprechender Aufforderung Ihr Kennwort an.

1. Ubuntu zeigt eine Begrüßungsnachricht und dann eine Aufforderung wie diese `<username>@<machinename>:~$` an.

## <a name="download-key-vault-certificates"></a>Herunterladen von Key Vault-Zertifikaten

Weiter oben in diesem Artikel haben wir Zertifikate in Key Vault hochgeladen, um sie für unser IoT Edge-Gerät und für unser Blattgerät verfügbar zu machen. Das Blattgerät ist ein nachgeschaltetes Gerät, das das IoT Edge-Gerät als Gateway für die Kommunikation mit IoT Hub verwendet.

Um das Blattgerät kümmern wir uns später in diesem Tutorial. In diesem Abschnitt laden Sie die Zertifikate auf das IoT Edge-Gerät herunter.

1. Melden Sie sich über die SSH-Sitzung auf dem virtuellen Linux-Computer mit der Azure CLI an Azure an.

    ```azurecli
    az login
    ```

1. Sie werden aufgefordert, die Seite einer [Microsoft-Geräteanmeldung](https://microsoft.com/devicelogin) in einem Browser zu öffnen und einen eindeutigen Code anzugeben. Sie können diese Schritte auf Ihrem lokalen Computer ausführen. Schließen Sie das Browserfenster, wenn Sie die Authentifizierung abgeschlossen haben.

1. Wenn die Authentifizierung erfolgreich war, wird der virtuelle Linux-Computer angemeldet, und Ihre Azure-Abonnements werden aufgelistet.

1. Legen Sie das Azure-Abonnement fest, das Sie für Azure CLI-Befehle verwenden möchten.

    ```azurecli
    az account set --subscription <subscriptionId>
    ```

1. Erstellen Sie auf der VM ein Verzeichnis für die Zertifikate.

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. Laden Sie die Zertifikate herunter, die Sie im Schlüsseltresor gespeichert haben: „new-edge-device-full-chain.cert.pem“, „new-edge-device.key.pem“ und „azure-iot-test-only.root.ca.cert.pem“.

    ```azurecli
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>Aktualisieren der Konfiguration für das IoT Edge-Gerät

Für die IoT Edge-Runtime wird die Datei „/etc/iotedge/config.yaml“ genutzt, um die Konfiguration zu speichern. Wir müssen in dieser Datei zwei Arten von Informationen aktualisieren:

* **Zertifikate**: die Zertifikate, die für die Verbindungsherstellung mit nachgeschalteten Geräten verwendet werden
* **Hostname**: der vollqualifizierte Domänenname (FQDN) des IoT Edge-Geräts der VM

Aktualisieren Sie die Zertifikate und den Hostnamen durch direkte Bearbeitung der Datei „config.yaml“.

1. Öffnen Sie die Datei „config.yaml“.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Aktualisieren Sie den Abschnitt „certificates“ der Datei „config.yaml“, indem Sie das vorangestellte Zeichen **#** entfernen und den Pfad festlegen, sodass die Datei wie im folgenden Beispiel aussieht:

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    Stellen Sie sicher, dass der Zeile **certificates:** kein Leerzeichen vorangestellt ist und dass die geschachtelten Zertifikate jeweils um zwei Leerstellen eingerückt sind.

    Wenn Sie in Nano mit der rechten Maustaste klicken, wird der Inhalt Ihrer Zwischenablage an der aktuellen Cursorposition eingefügt. Verwenden Sie die Pfeiltasten auf der Tastatur, um zu der Zeichenfolge zu navigieren, die ersetzt werden soll. Löschen Sie die Zeichenfolge, und klicken Sie dann mit der rechten Maustaste, um den Inhalt aus dem Puffer einzufügen.

1. Navigieren Sie im Azure-Portal zu Ihrem virtuellen Computer. Kopieren Sie den DNS-Namen (FQDN des Computers) aus dem Abschnitt **Übersicht**.

1. Fügen Sie den FQDN in den Abschnitt „hostname“ der Datei „config.yaml“ ein. Stellen Sie sicher, dass der Name ausschließlich Kleinbuchstaben enthält.

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

1. Speichern und schließen Sie die Datei mit **STRG+X**, **Y** und **EINGABETASTE**.

1. Starten Sie den IoT Edge-Daemon neu.

    ```bash
    sudo systemctl restart iotedge
    ```

1. Überprüfen Sie den Status des IoT Edge-Daemons. Geben Sie nach Ausführung des Befehls zum Beenden **:q** ein.

    ```bash
    systemctl status iotedge
    ```

## <a name="troubleshooting"></a>Problembehandlung

Falls im Statusbereich Fehler angezeigt werden (farbiger Text mit dem Präfix „\[ERROR\]“), untersuchen Sie die Daemonprotokolle, um ausführliche Fehlerinformationen zu erhalten.

   ```bash
   journalctl -u iotedge --no-pager --no-full
   ```

Weitere Informationen zum Beheben von Fehlern finden Sie auf der Seite [Problembehandlung](troubleshoot.md).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Dieses Tutorial ist Teil einer Reihe, in der jeder Artikel auf den Schritten aufbaut, die jeweils im vorherigen Artikel ausgeführt wurden. Warten Sie mit dem Bereinigen von Ressourcen, bis Sie das letzte Tutorial abgeschlossen haben.

## <a name="next-steps"></a>Nächste Schritte

Wir haben die Konfiguration einer Azure-VM als transparentes IoT Edge-Gateway abgeschlossen. Zunächst haben wir Testzertifikate generiert, die wir in Key Vault hochgeladen haben. Anschließend haben wir ein Skript und eine Resource Manager-Vorlage verwendet, um die VM mit dem Image „Ubuntu Server 16.04 LTS + Azure IoT Edge-Runtime“ aus dem Azure Marketplace bereitzustellen. Als die VM eingerichtet und betriebsbereit war, haben wir über SSH eine Verbindung hergestellt. Danach haben wir uns bei Azure angemeldet und Zertifikate aus Key Vault heruntergeladen. Wir haben die Datei „config.yaml“ aktualisiert, um verschiedene Aspekte der IoT Edge-Runtimekonfiguration zu aktualisieren.

Fahren Sie mit dem nächsten Artikel fort, um IoT Edge-Module zu erstellen.

> [!div class="nextstepaction"]
> [Erstellen und Bereitstellen von benutzerdefinierten IoT Edge-Modulen](tutorial-machine-learning-edge-06-custom-modules.md)
