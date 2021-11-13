---
title: Transformieren von Daten für Azure IoT Central | Microsoft-Dokumentation
description: IoT-Geräte senden Daten in verschiedenen Formaten, die Sie ggf. transformieren müssen. In diesem Artikel wird beschrieben, wie Sie Daten sowohl auf dem Weg in als auch aus IoT Central heraus transformieren. In den beschriebenen Szenarien werden IoT Edge und Azure Functions verwendet.
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: e05cbd17dbfe4f61562856f9244f5b4d0fa65dc9
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131577382"
---
# <a name="transform-data-externally-for-iot-central"></a>Externe Datenumwandlung für IoT Central

IoT-Geräte senden Daten in verschiedenen Formaten. Um die Gerätedaten mit Ihrer IoT Central-Anwendung zu verwenden, müssen Sie möglicherweise eine Transformation durchführen, um Folgendes zu ermöglichen:

- Das Format der Daten mit Ihrer IoT Central-Anwendung kompatibel zu machen.
- Einheiten zu konvertieren.
- Neue Metriken zu berechnen.
- Daten aus anderen Quellen anzureichern.

In diesem Artikel erfahren Sie, wie Sie Gerätedaten außerhalb IoT Central beim ein- oder ausgehenden Datenverkehr transformieren.

Das folgende Diagramm zeigt drei Routen für Daten, die Transformationen enthalten:

:::image type="content" source="media/howto-transform-data/transform-data.png" alt-text="Zusammenfassung der Datentransformationsrouten für den ein- und ausgehenden Datenverkehr" border="false":::

Die folgende Tabelle zeigt drei Beispiele für Transformationstypen:

| Transformation | Beschreibung | Beispiel  | Hinweise |
|------------------------|-------------|----------|-------|
| Nachrichtenformat         | Konvertieren in oder Bearbeiten von JSON-Nachrichten. | CSV in JSON  | Eingehend. IoT Central akzeptiert nur JSON-Nachrichten mit Wert. Weitere Informationen finden Sie unter [Telemetrie-, Eigenschaften- und Befehlsnutzlasten](concepts-telemetry-properties-commands.md). |
| Berechnungen           | Mathematische Funktionen, die durch [Azure Functions](../../azure-functions/index.yml) ausgeführt werden können. | Einheitenkonvertierung von Fahrenheit in Celsius.  | Nutzen Sie das Ausgangsmuster zur Transformierung, um den skalierbaren Geräteeingang über eine direkte Verbindung mit IoT Central zu nutzen. Durch Transformierung der Daten können Sie IoT Central-Features wie Visualisierungen und Aufträge verwenden. |
| Nachrichtenerweiterung     | Anreicherungen aus externen Datenquellen wurden in Geräteeigenschaften oder Telemetriedaten nicht gefunden. Weitere Informationen zu internen Anreicherungen finden Sie unter [Exportieren von IoT-Daten in Cloudziele mithilfe des Datenexports](howto-export-data.md) | Hinzufügen von Wetterinformationen zu Nachrichten mithilfe von [Standortdaten](howto-use-location-data.md) von Geräten | Nutzen Sie das Ausgangsmuster zur Transformierung, um den skalierbaren Geräteeingang über eine direkte Verbindung mit IoT Central zu nutzen. |

## <a name="prerequisites"></a>Voraussetzungen

Zum Ausführen der Schritte in dieser Anleitung ist Folgendes erforderlich:

[!INCLUDE [iot-central-prerequisites-basic](../../../includes/iot-central-prerequisites-basic.md)]

## <a name="data-transformation-at-ingress"></a>Datentransformation beim eingehenden Datenverkehr

Zum Transformieren von eingehenden Gerätedaten stehen zwei Optionen zur Verfügung:

- **IoT Edge**: Verwenden Sie ein IoT Edge-Modul, um Daten von nachgeschalteten Geräten zu transformieren, bevor Sie die Daten an Ihre IoT Central senden.

- **IoT Central-Geräte-Bridge**: Die [IoT Central-Gerätebrücke](howto-build-iotc-device-bridge.md) verbindet andere IoT-Geräte-Clouds wie Sigfox, Particle und The Things Network mit IoT Central. Die Gerätebrücke verwendet eine Azure-Funktion, um die Daten weiter zu übertragen, und Sie können die Funktion anpassen, um die Gerätedaten zu transformieren.

### <a name="use-iot-edge-to-transform-device-data"></a>Verwenden von IoT Edge zum Transformieren von Gerätedaten

:::image type="content" source="media/howto-transform-data/transform-ingress.png" alt-text="Datentransformation bei eingehendem Datenverkehr mithilfe von IoT Edge" border="false":::

In diesem Szenario transformiert ein IoT Edge-Modul die Daten von nachgeschalteten Geräten, bevor sie an Ihre IoT Central weitergeleitet werden. Die allgemeinen Schritte zum Konfigurieren dieses Szenarios lauten wie folgt:

1. **Einrichten eines IoT Edge Geräts**: Installieren Sie ein IoT Edge-Gerät und stellen Sie es als Gateway bereit und verbinden Sie das Gateway mit der IoT Central-Anwendung.

1. **Verbinden Sie das nachgeschaltete Gerät mit dem IoT Edge-Gerät**: Verbinden Sie nachgeschaltete Geräte mit dem IoT Edge-Gerät und stellen Sie es für Ihre IoT Central-Anwendung zur Verfügung.

1. **Transformieren von Gerätedaten in IoT Edge**: Erstellen Sie ein IoT Edge-Modul, um die Daten zu transformieren. Stellen Sie das Modul auf dem IoT Edge-Gateway-Gerät bereit, das die transformierten Gerätedaten an Ihre IoT Central weiterleitet.

1. **Überprüfen**: Senden Sie Daten von einem nachgeschalteten Gerät an das Gateway, und überprüfen Sie, ob die transformierten Gerätedaten Ihre IoT Central erreichen.

Im Beispiel, das in den folgenden Abschnitten beschriebenen wird, sendet das nachgeschaltete Gerät CSV-Daten im folgenden Format an das IoT Edge Gateway-Gerät:

```csv
"<temperature >, <pressure>, <humidity>"
```

Sie möchten ein IoT Edge-Modul verwenden, um die Daten in das folgende JSON-Format zu transformieren, bevor sie an IoT Central gesendet werden:

```json
{
  "device": {
      "deviceId": "<downstream-deviceid>"
  },
  "measurements": {
    "temp": <temperature>,
    "pressure": <pressure>,
    "humidity": <humidity>,
  }
}
```

[![Code durchsuchen](media/common/browse-code.svg)](https://github.com/iot-for-all/iot-central-transform-with-iot-edge)

Die folgenden Schritte zeigen, wie Sie dieses Szenario einrichten und konfigurieren:

### <a name="build-the-custom-module"></a>Erstellen Sie das benutzerdefinierte Modul

In diesem Szenario führt das IoT Edge-Gerät ein benutzerdefiniertes Modul aus, das die Daten des nachgeschalteten Geräts transformiert. Bevor Sie das IoT Edge-Gerät bereitstellen und konfigurieren, müssen Sie Folgendes durchführen:

- Erstellen Sie das benutzerdefinierte Modul.
- Fügen Sie das benutzerdefinierte Modul einer Containerregistrierung hinzu.

Die IoT Edge-Runtime lädt benutzerdefinierte Module aus einer Containerregistrierung herunter, z. B. einer Azure-Containerregistrierung oder Docker Hub. Die [Azure Cloud Shell](../../cloud-shell/overview.md) verfügt über alle Tools, die Sie zum Erstellen einer Containerregistrierung, zum Erstellen des Moduls und zum Hochladen des Moduls in die Registrierung benötigen:

Wie eine Containerregistrierung erstellt wird:

1. Öffnen Sie die [Azure Cloud Shell](https://shell.azure.com/) und melden Sie sich bei Ihrem Azure-Abonnement an.

1. Führen Sie die folgenden Befehle aus, um eine Azure Container Registry-Instanz zu erstellen:

    ```azurecli
    REGISTRY_NAME="{your unique container registry name}"
    az group create --name ingress-scenario --location eastus
    az acr create -n $REGISTRY_NAME -g ingress-scenario --sku Standard --admin-enabled true
    az acr credential show -n $REGISTRY_NAME
    ```

    Notieren Sie sich die `username`- und `password`-Werte, Sie werden sie später verwenden.

So erstellen Sie das benutzerdefinierte Modul in [Azure Cloud Shell](https://shell.azure.com/):

1. Navigieren Sie in [Azure Cloud Shell](https://shell.azure.com/) zu einem geeigneten Ordner.
1. Führen Sie den folgenden Befehl aus, um das GitHub-Repository zu klonen, das den Quellcode des Moduls enthält:

    ```azurecli
    git clone https://github.com/iot-for-all/iot-central-transform-with-iot-edge
    ```

1. Führen Sie zum Erstellen des benutzerdefinierten Moduls die folgenden Befehle in Azure Cloud Shell aus:

    ```azurecli
    cd iot-central-transform-with-iot-edge/custommodule/transformmodule
    az acr build --registry $REGISTRY_NAME --image transformmodule:0.0.1-amd64 -f Dockerfile.amd64 .
    ```

    Die Ausführung der genannten Befehle kann mehrere Minuten dauern.

### <a name="set-up-an-iot-edge-device"></a>Einrichten eines IoT Edge-Geräts

In diesem Szenario wird ein IoT Edge Gateway-Gerät verwendet, um die Daten von allen nachgeschalteten Geräten zu transformieren. In diesem Abschnitt wird beschrieben, wie Sie IoT Central-Gerätevorlagen für das Gateway und nachgeschaltete Geräte in Ihrer IoT Central-Anwendung erstellen. IoT Edge-Geräte verwenden ein Bereitstellungsmanifest, um ihre Module zu konfigurieren.

Um eine Gerätevorlage für das nachgeschaltete Gerät zu erstellen, wird in diesem Szenario ein einfaches Thermostat-Gerätemodell verwendet:

1. Laden Sie das [Gerätemodell für das Thermostat-Gerät](https://raw.githubusercontent.com/Azure/iot-plugandplay-models/main/dtmi/com/example/thermostat-2.json) auf Ihren lokalen Computer herunter.

1. Melden Sie sich bei Ihrer IoT Central-Anwendung an, und navigieren Sie zur Seite mit den **Gerätevorlagen**.

1. Wählen Sie **+ Neu** aus, wählen Sie **IoT-Gerät** und dann **Weiter: Anpassen** aus.

1. Geben Sie *Thermostat* als Vorlagennamen ein und wählen Sie **Weiter: Überprüfen** aus. Klicken Sie anschließend auf **Erstellen**.

1. Wählen Sie **ein Modell importieren** aus und importieren Sie die zuvor heruntergeladene Datei *thermostat-2.json*.

1. Wählen Sie **Veröffentlichen** aus, um die neue Gerätevorlage zu veröffentlichen.

Wie eine Gerätevorlage für ein IoT Edge-Gateway-Gerät erstellt wird:

1. Speichern Sie eine Kopie des Bereitstellungsmanifests auf Ihrem lokalen Entwicklungscomputer: [moduledeployment.json](https://raw.githubusercontent.com/iot-for-all/iot-central-transform-with-iot-edge/main/edgemodule/moduledeployment.json).

1. Öffnen Sie ihre lokale Kopie der Manifestdatei *moduledeployment.json* in einem Text-Editor.

1. Suchen Sie nach dem Abschnitt `registryCredentials` und ersetzen Sie die Platzhalter durch die Werte, die Sie sich beim Erstellen der Azure-Containerregistrierung notiert haben. Der `address`-Wert lautet `<username>.azurecr.io`.

1. Suchen Sie den `settings`-Abschnitt für die `transformmodule`. Ersetzen Sie `<acr or docker repo>` durch denselben `address`-Wert, den Sie im vorherigen Schritt verwendet haben. Speichern Sie die Änderungen.

1. Navigieren Sie in Ihrer IoT Central-Anwendung zur Seite **Gerätevorlagen**.

1. Wählen Sie **+ Neu** und dann **Azure IoT Edge** aus und wählen Sie dann **Weiter: Anpassen** aus.

1. Geben *IoT Edge Gateway-Gerät* als Name für die Gerätevorlage ein. Wählen Sie **Dies ist ein Gatewaygerät** aus. Wählen Sie **Durchsuchen** aus, um die Bereitstellungsmanifestdatei *moduledeployment.json* hochzuladen, die Sie zuvor bearbeitet haben.

1. Wenn das Bereitstellungsmanifest überprüft wird, wählen Sie **Weiter: Überprüfen** und dann **Erstellen** aus.

1. Wählen Sie unter **Modell** die Option **Beziehungen** aus. Wählen Sie **+ Beziehung hinzufügen** aus. Geben Sie *Nachgeschaltetes Gerät* als Anzeigenamen ein und wählen Sie **Thermostat** als Ziel aus. Wählen Sie **Speichern** aus.

1. Wählen Sie **Veröffentlichen** aus, um die Gerätevorlage zu veröffentlichen.

Sie verfügen nun über zwei Gerätevorlagen in Ihrer IoT Central-Anwendung. Die **IoT Edge Gateway-Gerätevorlage** und die **Thermostatvorlage** als nachgeschaltetes Gerät.

Wie ein Gateway-Gerät in IoT Central registriert wird:

1. Navigieren Sie in Ihrer IoT Central-Anwendung zur Seite **Geräte**.

1. Wählen Sie **IoT Edge-Gateway-Gerät** und dann **Gerät erstellen** aus. Geben Sie *IoT Edge-Gateway-Gerät* als Gerätenamen und *gateway-01* als Geräte-ID ein und stellen Sie sicher, dass **IoT Edge Gateway-Gerät** als Gerätevorlage ausgewählt ist. Klicken Sie auf **Erstellen**.

1. Klicken Sie in der Liste der Geräte auf das **IoT Edge-Gateway-Gerät** und wählen Sie dann **Verbinden** aus.

1. Notieren Sie sich die Werte für den **ID-Bereich**, die **Geräte-ID** und den **Primärschlüssel** für das **IoT Edge-Gateway-Gerät**. Sie werden sie später noch benötigen.

Wie ein nachgeschaltetes Gerät in IoT Central registriert wird:

1. Navigieren Sie in Ihrer IoT Central-Anwendung zur Seite **Geräte**.

1. Wählen Sie **Thermostat** und dann **Gerät erstellen** aus. Geben Sie *Thermostat* als Gerätenamen und *downstream-01* als Geräte-ID ein. Stellen Sie sicher, dass **Thermostat** als Gerätevorlage ausgewählt ist. Klicken Sie auf **Erstellen**.

1. Wählen Sie in der Liste der Geräte das **Thermostat** und dann **An Gateway anfügen** aus. Wählen Sie die **Vorlage IoT Edge-Gateway-Geräts** und die **IoT Edge-Gateway-Geräteinstanz** aus. Wählen Sie **Anfügen** aus.

1. Klicken Sie in der Liste der Geräte auf **Thermostat** und wählen Sie dann **Verbinden** aus.

1. Notieren Sie sich die Werte für den **ID-Bereich**, die **Geräte-ID** und den **Primärschlüssel** für das **Thermostat-Gerät**. Sie werden sie später noch benötigen.

### <a name="deploy-the-gateway-and-downstream-devices"></a>Bereitstellen des Gateways und der nachgeschalteten Geräte

Der Einfachheit halber werden in diesem Artikel virtuelle Azure-Computer verwendet, um das Gateway und nachgeschaltete Geräte auszuführen. Um die beiden virtuellen Azure-Computer zu erstellen, wählen Sie unten die Schaltfläche **In Azure bereitstellen** aus und verwenden Sie die Informationen in der folgenden Tabelle, um das Formular für die **Benutzerdefinierte Bereitstellung** auszufüllen:

| Feld | Wert |
| ----- | ----- |
| Resource group | `ingress-scenario` |
| DNS Label Prefix Gateway | Ein eindeutiger DNS-Name für diesen Computer, z. B. `<your name>edgegateway` |
| DNS Label Prefix Downstream | Ein eindeutiger DNS-Name für diesen Computer, z. B. `<your name>downstream` |
| Bereichs-ID | Der zuvor notierte ID-Bereich |
| Geräte-ID IoT Edge-Gateway | `gateway-01` |
| Geräteschlüssel IoT Edge-Gateway | Der Wert für den Primärschlüssel, den Sie sich zuvor notiert hatten |
| Authentifizierungstyp | Kennwort |
| Administratorkennwort oder Schlüssel | Wählen Sie das Kennwort für das **AzureUser**-Konto auf beiden virtuellen Computern aus. |

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fiot-central-docs-samples%2Fmaster%2Ftransparent-gateway%2FDeployGatewayVMs.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png" alt="Deploy to Azure button" />
</a>

Wählen Sie **Überprüfen + erstellen** und danach **Erstellen** aus. Die Erstellung der virtuellen Computer in der Ressourcengruppe **Eingangsszenario** dauert einige Minuten.

So überprüfen Sie, ob IoT Edge-Gerät ordnungsgemäß ausgeführt wird:

1. Öffnen Sie Ihre IoT Central-Anwendung. Navigieren Sie dann zum **IoT Edge-Gateway-Gerät** auf der Liste der Geräte auf der Seite **Geräte**.

1. Wählen Sie die Registerkarte **Module** aus und überprüfen Sie den Status der drei Module. Es dauert einige Minuten, bis die IoT Edge-Runtime auf dem virtuellen Computer gestartet wird. Wenn sie gestartet wird, lautet der Status der drei Module **Wird ausgeführt**. Falls die IoT Edge-Runtime nicht gestartet wird, helfen Ihnen die Informationen unter [Behandeln von Problemen bei Ihrem IoT Edge-Gerät](../../iot-edge/troubleshoot.md) weiter.

Damit Ihr IoT Edge-Gerät als Gateway fungieren kann, benötigt es einige Zertifikate, um den untergeordneten Geräten seine Identität nachzuweisen. In diesem Artikel werden Demozertifikate verwendet. Verwenden Sie in einer Produktionsumgebung Zertifikate von Ihrer Zertifizierungsstelle.

So können Sie die Demozertifikate generieren und auf dem Gatewaygerät installieren:

1. Verwenden Sie Secure Shell (SSH), um eine Verbindung mit Ihrem virtuellen Computer für das Gatewaygerät herzustellen und sich bei diesem anzumelden. Sie können den DNS-Namen für diesen virtuellen Computer im Azure-Portal finden. Navigieren Sie in der Ressourcengruppe **Eingangsszenario** zum virtuellen Computer **edgegateway**.

    > [!TIP]
    > Möglicherweise müssen Sie Port 22 für den SSH-Zugriff auf beiden virtuellen Computern öffnen, bevor Sie SSH verwenden können, um eine Verbindung von Ihrem lokalen Computer oder dem lokalen Computer Azure Cloud Shell herzustellen.

1. Führen Sie die folgenden Befehle aus, um das IoT Edge-Repository zu klonen und die Demozertifikate zu generieren:

    ```bash
    # Clone the repo
    cd ~
    git clone https://github.com/Azure/iotedge.git

    # Generate the demo certificates
    mkdir certs
    cd certs
    cp ~/iotedge/tools/CACertificates/*.cnf .
    cp ~/iotedge/tools/CACertificates/certGen.sh .
    ./certGen.sh create_root_and_intermediate
    ./certGen.sh create_edge_device_ca_certificate "mycacert"
    ```

    Nachdem Sie die obigen Befehle ausgeführt haben, können Sie in den nächsten Schritten die folgenden Dateien verwenden:

    - *~/certs/certs/azure-iot-test-only.root.ca.cert.pem* – Dies ist das Zertifikat der Stammzertifizierungsstelle, das zum Erstellen aller anderen Demozertifikate für das Testen eines IoT Edge-Szenarios verwendet wird.
    - *~/certs/certs/iot-edge-device-mycacert-full-chain.cert.pem* – Dies ist ein Zertifikat der Gerätezertifizierungsstelle, auf das in der Datei *config.yaml* verwiesen wird. In einem Gatewayszenario bestätigt das IoT Edge-Gerät den untergeordneten Geräten seine Identität mit diesem Zertifikat der Zertifizierungsstelle.
    - *~/certs/private/iot-edge-device-mycacert.key.pem* – Dies ist der private Schlüssel, der dem Zertifikat der Gerätezertifizierungsstelle zugeordnet ist.

    Weitere Informationen zu diesen Demozertifikaten finden Sie unter [Erstellen von Demozertifikaten zum Testen der Features von IoT Edge-Geräten](../../iot-edge/how-to-create-test-certificates.md).

1. Öffnen Sie die Datei *config.yaml* in einem Text-Editor. Beispiel:

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Suchen Sie die `Certificate settings`-Einstellungen. Heben Sie die Auskommentierung auf, und ändern Sie die Zertifikateinstellungen wie folgt:

    ```text
    certificates:
      device_ca_cert: "file:///home/AzureUser/certs/certs/iot-edge-device-ca-mycacert-full-chain.cert.pem"
      device_ca_pk: "file:///home/AzureUser/certs/private/iot-edge-device-ca-mycacert.key.pem"
      trusted_ca_certs: "file:///home/AzureUser/certs/certs/azure-iot-test-only.root.ca.cert.pem"
    ```

    Im obigen Beispiel wird davon ausgegangen, dass Sie als **AzureUser** angemeldet sind und ein Zertifikat der Gerätezertifizierungsstelle namens „mycacert“ erstellt haben.

1. Speichern Sie die Änderungen, und starten Sie die IoT Edge-Runtime neu:

    ```bash
    sudo systemctl restart iotedge
    ```

Wenn die IoT Edge-Runtime nach Ihren Änderungen erfolgreich gestartet wird, wird der Status der Module **$edgeAgent** und **$edgeHub** in **Wird ausgeführt** geändert. Sie können diese Statuswerte auf der Seite **Module** für Ihr Gatewaygerät in IoT Central sehen.

Überprüfen Sie die in *config.yaml* vorgenommenen Änderungen, und lesen Sie den Artikel [Behandeln von Problemen bei Ihrem IoT Edge-Gerät](../../iot-edge/troubleshoot.md), wenn die Runtime nicht gestartet wird.

### <a name="connect-downstream-device-to-iot-edge-device"></a>Ein nachgeschaltetes Gerät mit einem IoT Edge-Gerät verbinden

So verbinden Sie ein nachgeschaltetes Gerät mit einem Azure IoT Edge-Gerät:

1. Verwenden Sie Secure Shell (SSH), um eine Verbindung mit Ihrem virtuellen Computer für das nachgeschaltete Gerät herzustellen und sich bei diesem anzumelden. Sie können den DNS-Namen für diesen virtuellen Computer im Azure-Portal finden. Navigieren Sie in der Ressourcengruppe **Eingangsszenario** zum virtuellen Computer **leafdevice**.

    > [!TIP]
    > Möglicherweise müssen Sie Port 22 für den SSH-Zugriff auf beiden virtuellen Computern öffnen, bevor Sie SSH verwenden können, um eine Verbindung von Ihrem lokalen Computer oder dem lokalen Computer Azure Cloud Shell herzustellen.

1. Führen Sie den folgenden Befehl aus, um das GitHub-Repository mit dem Quellcode für das nachgeschaltete Beispielgerät zu klonen:

    ```bash
    cd ~
    git clone https://github.com/iot-for-all/iot-central-transform-with-iot-edge
    ```

1. Führen Sie die folgenden `scp`-Befehle aus, um das erforderliche Zertifikat vom Gatewaygerät zu kopieren. Dieser `scp`-Befehl verwendet den Hostnamen `edgegateway`, um den virtuellen Gatewaycomputer zu identifizieren. Sie werden zur Eingabe des Kennworts aufgefordert:

    ```bash
    cd ~/iot-central-transform-with-iot-edge
    scp AzureUser@edgegateway:/home/AzureUser/certs/certs/azure-iot-test-only.root.ca.cert.pem leafdevice/certs
    ```

1. Navigieren Sie zum *leafdevice*-Ordner und installieren Sie die benötigten Pakete. Führen Sie dann die `build`- und `start`-Skripte aus, um das Gerät mit dem Gateway zu verbinden:

    ```bash
    cd ~/iot-central-transform-with-iot-edge/leafdevice
    sudo apt update
    sudo apt install nodejs npm node-typescript
    npm install
    npm run-script build
    npm run-script start
    ```

1. Geben Sie die Geräte-ID, die Bereichs-ID und den SAS-Schlüssel für das zuvor erstellte nachgeschaltete Gerät ein. Geben Sie als Hostnamen `edgegateway` ein. Die Ausgabe des Befehls sieht wie folgt aus:

    ```output
    Registering device downstream-01 with scope 0ne00284FD9
    Registered device downstream-01.
    Connecting device downstream-01
    Connected device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    ```

### <a name="verify"></a>Überprüfung

Um zu überprüfen, ob das Szenario ausgeführt wird, navigieren Sie zu Ihrem **IoT Edge Gateway-Gerät** in IoT Central:

:::image type="content" source="media/howto-transform-data/transformed-data.png" alt-text="Screenshot der Seite „Transformierte Daten auf Geräten“.":::

- Wählen Sie **Module** aus. Überprüfen Sie, ob die drei IoT Edge Module **$edgeAgent**, **$edgeHub** und **transformmodule** ausgeführt werden.
- Wählen Sie **nachgeschaltete Geräte** aus und überprüfen Sie, ob das nachgeschaltete Gerät bereitgestellt wurde.
- Wählen Sie **Rohdaten** aus. Die Telemetriedaten in der Spalte **Nicht modellierte Daten** sehen wie folgt aus:

    ```json
    {"device":{"deviceId":"downstream-01"},"measurements":{"temperature":85.21208,"pressure":59.97321,"humidity":77.718124,"scale":"farenheit"}}
    ```

Da das IoT Edge-Gerät die Daten des nachgeschalteten Geräts transformiert, werden die Telemetriedaten dem Gateway-Gerät in IoT Central zugeordnet. Um die Telemetriedaten zu visualisieren, erstellen Sie eine neue Version der **IoT Edge-Gateway-Gerätevorlage** mit Definitionen für die Telemetrietypen.

## <a name="data-transformation-at-egress"></a>Datentransformation beim ausgehenden Datenverkehr

Sie können Ihre Geräte mit IoT Central verbinden, die Gerätedaten in eine Compute-Engine exportieren, um sie zu transformieren, und die transformierten Daten dann zur Geräteverwaltung und -analyse an IoT Central zurücksenden. Beispiel:

- Ihre Geräte senden Standortdaten an IoT Central.
- IoT Central exportiert die Daten in eine Compute-Engine, die die Standortdaten mit Wetterinformationen erweitert.
- Die Compute-Engine sendet die erweiterten Daten zurück an IoT Central.

Sie können die [IoT Central-Gerätebrücke](https://github.com/Azure/iotc-device-bridge) als Compute-Engine verwenden, um aus IoT Central exportierte Daten zu transformieren.

Ein Vorteil der Transformation bei ausgehendem Datenverkehr besteht darin, dass Ihre Geräte direkt mit IoT Central verbunden sind, was das Senden von Befehlen an Geräte oder das Aktualisieren von Geräteeigenschaften erleichtert. Mit dieser Methode können Sie jedoch mehr Meldungen als ihre monatliche Zuteilung verwenden und die Kosten für die Verwendung von Azure IoT Central erhöhen.

### <a name="use-the-iot-central-device-bridge-to-transform-device-data"></a>Verwenden der IoT Central-Gerätebrücke zum Transformieren von Gerätedaten

:::image type="content" source="media/howto-transform-data/transform-egress.png" alt-text="Datentransformation bei ausgehendem Datenverkehr mithilfe von IoT Edge" border="false":::

In diesem Szenario transformiert eine Compute-Engine aus IoT Central exportierte Gerätedaten, bevor sie an Ihre IoT Central zurücksenden. Die allgemeinen Schritte zum Konfigurieren dieses Szenarios lauten wie folgt:

1. **Richten Sie die Compute-Engine ein:** Erstellen Sie eine IoT Central-Gerätebrücke, die als Compute-Engine für die Datentransformation fungieren soll.

1. **Transformieren von Gerätedaten in der Gerätebrücke:** Transformieren Sie Daten in der Gerätebrücke, indem Sie den Funktionscode der Gerätebrücke für Ihren Datentransformations-Einsatzfall ändern.

1. **Aktivieren Sie den Datenfluss von IoT Central zur Gerätebrücke:** Exportieren Sie die Daten aus IoT Central zur Transformation in die Gerätebrücke. Anschließend werden die transformierten Daten an die IoT Central weitergeleitet. Wenn Sie den Datenexport erstellen, verwenden Sie Nachrichteneigenschaftsfilter, um nur nicht übersetzte Daten zu exportieren.

1. **Überprüfen**: Verbinden Sie Ihr Gerät mit der IoT Central-App, und überprüfen Sie sowohl rohe Gerätedaten als auch transformierte Daten in IoT Central.

<!-- To Do - doesn't the device send JSON data? -->
Im Beispiel, das in den folgenden Abschnitten beschriebenen wird, sendet das Gerät CSV-Daten im folgenden Format an das IoT Edge Gateway-Gerät:

```csv
"<temperature in degrees C>, <humidity>, <latitude>, <longitude>"
```

Sie verwenden die Gerätebrücke, um die Gerätedaten zu transformieren, indem Sie:

- Die Temperatureinheit von Centigrade in Fahrenheit ändern.
- Die Gerätedaten mit Wetterdaten anreichern, die dem [Open Weather](https://openweathermap.org/)-Dienst für die Werte für Breiten- und Längengrad entnommen werden.

Die Gerätebrücke sendet dann die transformierten Daten im folgenden Format an IoT Central:

```json
{
  "temp": <temperature in degrees F>,
  "humidity": <humidity>,
  "lat": <latitude>,
  "lon": <logitude>,
  "weather": {
    "weather_temp": <temperature at lat/lon>,
    "weather_humidity": <humidity at lat/lon>,
    "weather_pressure": <pressure at lat/lon>,
    "weather_windspeed": <wind speed at lat/lon>,
    "weather_clouds": <cloud cover at lat/lon>,
    "weather_uvi": <UVI at lat/lon>
  }
}
```

Die folgenden Schritte zeigen, wie Sie dieses Szenario einrichten und konfigurieren:

### <a name="retrieve-your-iot-central-connection-settings"></a>Abrufen ihrer IoT Central-Verbindungseinstellungen

Bevor Sie dieses Szenario einrichten, müssen Sie einige Verbindungseinstellungen aus Ihrer IoT Central abrufen:

1. Melden Sie sich bei Ihrer IoT Central-Anwendung an.

1. Navigieren Sie zu **Verwaltung > Geräteverbindung**.

1. Notieren Sie sich den Wert für den **ID-Bereich**. Sie werden diesen Wert später noch verwenden.

1. Wählen Sie die Registrierungsgruppe **SaS-IoT-Devices** aus. Notieren Sie sich den SAS-Primärschlüssel (Shared Access Signature). Sie werden diesen Wert später noch verwenden.

### <a name="set-up-a-compute-engine"></a>Einrichten einer Compute-Engine

In diesem Szenario wird die gleiche Azure Functions-Bereitstellung wie für die IoT Central-Gerätebrücke verwendet. Um die Gerätebrücke bereitzustellen, wählen Sie unten die Schaltfläche **In Azure bereitstellen** aus und verwenden Sie die Informationen in der folgenden Tabelle, um das Formular für die **Benutzerdefinierte Bereitstellung** auszufüllen:

| Feld | Wert |
| ----- | ----- |
| Resource group | Erstellen Sie eine neue Ressourcengruppe namens `egress-scenario` |
| Region | Wählen Sie die Ihnen am nächsten gelegene Region aus. |
| Bereichs-ID | Verwenden Sie den zuvor notierten **ID-Bereich**. |
| IoT Central SAS-Schlüssel | Verwenden Sie den SAS-Primärschlüssel für die Registrierungsgruppe **SaS-IoT-Devices**. Sie haben sich diesen Wert zuvor notiert. |

[![In Azure bereitstellen](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fiotc-device-bridge%2Fmaster%2Fazuredeploy.json).

Wählen Sie **Überprüfen + erstellen** und danach **Erstellen** aus. Es dauert einige Minuten, die Azure-Funktion und die zugehörigen Ressourcen in der Ressourcengruppe für das **Ausgangsszenario** zu erstellen.

### <a name="transform-device-data-in-the-device-bridge"></a>Transformieren von Gerätedaten in der Gerätebrücke

So konfigurieren Sie die Gerätebrücke zum Transformieren der exportierten Gerätedaten:

1. Rufen Sie einen Anwendungs-API-Schlüssel aus dem Open Weather-Dienst ab. Das Konto ist kostenlos mit eingeschränkter Nutzung des Diensts. Legen Sie zum Erstellen eines Anwendungs-API-Schlüssels im [Open Weather Dienstportal](https://openweathermap.org/) ein Konto an und befolgen Sie die Anweisungen. Sie verwenden Ihren Open Weather-API-Schlüssel später.

1. Navigieren Sie im Azure-Portal zur Funktions-App in der Ressourcengruppe für das **Ausgangsszenario**.

1. Wählen Sie im linken Navigationsbereich unter **Entwicklungstools** die Option **App Service-Editor (Vorschau)** aus.

1. Wählen Sie  **Los &rarr;** aus, um die Seite **App Service-Editor** zu öffnen. Nehmen Sie die folgenden Änderungen vor:

    1. Öffnen Sie die *wwwroot/IoTCIntegration/index.js*-Datei. Ersetzen Sie den gesamten Code in dieser Datei durch den Code in [index.js](https://raw.githubusercontent.com/iot-for-all/iot-central-compute/main/Azure_function/index.js).

    1. Aktualisieren Sie im neuen *index.js* die `openWeatherAppId`-Variablendatei mit dem Open Weather-API-Schlüssel, den Sie zuvor abgerufen haben.

        ```javascript
        const openWeatherAppId = '<Your Open Weather API Key>'
        ```

    1. Fügen Sie den Daten, die von der Funktion an IoT Central gesendet werden, eine Nachrichteneigenschaft hinzu. IoT Central verwendet diese Eigenschaft, um das Exportieren der transformierten Daten zu verhindern. Öffnen Sie die Datei *wwwroot/IoTCIntegration/lib/engine.js*, um diese Änderung vorzunehmen. Suchen Sie den folgenden Code:

        ```javascript
        if (timestamp) {
          message.properties.add('iothub-creation-time-utc', timestamp);
        }
        ```

        Fügen Sie den folgenden Code direkt nach dem Code im vorherigen Schritt hinzu:

        ```javascript
        // add a message property that we can look for in data export to not re-compute computed telemetry
        message.properties.add('computed', true);
        ```

        Als Referenz können Sie ein vollständiges Beispiel für die [engine.js-Datei ](https://raw.githubusercontent.com/iot-for-all/iot-central-compute/main/Azure_function/lib/engine.js) anzeigen lassen.

1. Wählen Sie im **App Service-Editor** im linken Navigationsbereich **Konsole** aus. Führen Sie die folgenden Befehle aus, um die erforderlichen Pakete zu installieren:

    ```bash
    cd IoTCIntegration
    npm install
    ```

    Die Ausführung dieses Befehls kann einige Minuten in Anspruch nehmen.

1. Kehren Sie zur Übersichtsseite der **Azure-Funktion** zurück, und starten Sie die Funktion neu:

    :::image type="content" source="media/howto-transform-data/azure-function.png" alt-text="Neustarten der Funktion":::

1. Wählen Sie im linken Navigationsbereich **Funktionen** aus. Wählen Sie dann **IoTCIntegration** aus. Wählen Sie **Code + Test** aus.

1. Notieren Sie sich die Funktions-URL, Sie benötigen diesen Wert später:

    :::image type="content" source="media/howto-transform-data/get-function-url.png" alt-text="Erhalten Sie die Funktions-URL":::

### <a name="enable-data-flow-from-iot-central-to-the-device-bridge"></a>Aktivieren des Datenflusses von IoT Central zur Gerätebrücke

In diesem Abschnitt wird beschrieben, wie Sie die Azure IoT Central-Anwendung einrichten.

Speichern Sie zunächst die [Gerätemodell](https://raw.githubusercontent.com/iot-for-all/iot-central-compute/main/model.json)-Datei auf Ihrem lokalen Computer.

Navigieren Sie zum Hinzufügen einer Gerätevorlage zu Ihrer IoT Central-Anwendung selbiger und gehen Sie dann wie hier vor:

1. Melden Sie sich bei Ihrer IoT Central-Anwendung an, und navigieren Sie zur Seite mit den **Gerätevorlagen**.

1. Wählen Sie **+ Neu** aus, wählen Sie **IoT-Gerät** aus, wählen Sie **Weiter: Anpassen** aus und geben Sie *Computemodell* als Name für die Vorlage ein. Klicken Sie auf **Weiter: Review** (Weiter: Überprüfen). Klicken Sie anschließend auf **Erstellen**.

1. Wählen Sie **Modell importieren** aus und navigieren Sie zur Datei *model.json*, die Sie zuvor heruntergeladen haben.

1. Nachdem das Modell importiert wurde, wählen Sie **Veröffentlichen** aus, um die Gerätevorlage **Computemodell** zu veröffentlichen.

So richten Sie den Datenexport zum Senden von Daten an Ihre Gerätebrücke ein:

1. Wählen Sie in Ihrer IoT Central-Anwendung **Datenexport** aus.

1. Wählen Sie **+ Neues Ziel** aus, um ein Ziel für die Verwendung mit der Geräte-Bridge zu erstellen. Rufen Sie die *Compute-Zielfunktion* auf und wählen Sie für den **Zieltyp** die Option **Webhook** aus. Fügen Sie als Rückruf-URL die Funktions-URL ein, die Sie sich zuvor notiert haben. Lassen Sie die **Autorisierung** als **No Auth (Keine Authentifizierung)** bestehen.

1. Speichern Sie die Änderungen.

1. Wählen Sie **+ Neuer Export** aus und erstellen Sie einen Datenexport namens *Compute-Export*.

1. Fügen Sie einen Filter hinzu, um nur Gerätedaten für die von Ihnen verwendete Gerätevorlage zu exportieren. Wählen Sie **+ Filter** aus, wählen Sie das Element **Gerätevorlage** aus, wählen Sie den Operator **Gleich** aus und wählen Sie die soeben erstellte Gerätevorlage **Computemodell** aus.

1. Fügen Sie einen Meldungsfilter hinzu, um zwischen transformierten und nicht transformierten Daten zu unterscheiden. Dieser Filter verhindert, dass transformierte Werte zurück an die Gerätebrücke gesendet werden. Wählen Sie **+ Nachrichteneigenschaftenfilter** aus, geben Sie den *berechneten* Namenswert ein und wählen Sie dann den Operator **Nicht vorhanden** aus. Die Zeichenfolge `computed` wird als Schlüsselwort im Beispielcode der Gerätebrücke verwendet.

1. Wählen Sie als Ziel das **Computefunktionsziel** aus, das Sie zuvor erstellt haben.

1. Speichern Sie die Änderungen. Nach etwa einer Minute wird der **Exportstatus** als **Fehlerfrei** angezeigt.

### <a name="verify"></a>Überprüfung

Das Beispielgerät, das Sie zum Testen des Szenarios verwenden, ist in Node.js geschrieben. Stellen Sie sicher, dass auf Ihrem Computer Node.js und NPM installiert sind. Wenn Sie diese Voraussetzungen nicht installieren möchten, verwenden Sie die [Azure Cloud Shell](https://shell.azure.com/), in der sie vorinstalliert sind.

So führen Sie ein Beispielgerät aus, das das Szenario testet:

1. Führen Sie den folgenden Befehl aus, um das GitHub-Repository zu klonen, das den Beispielcode des Moduls enthält:

    ```bash
    git clone https://github.com/iot-for-all/iot-central-compute
    ```

1. Um das Beispielgerät mit Ihrer IoT Central-Anwendung zu verbinden, bearbeiten Sie die Verbindungseinstellungen in der Datei *iot-central-compute/device/device.js*. Ersetzen Sie die Bereichs-ID und den SAS-Gruppenschlüssel durch die Werte, die Sie sich zuvor notiert haben:

    ```javascript
    // These values need to be filled in from your Azure IoT Central application
    //
    const scopeId = "<IoT Central Scope Id value>";
    const groupSasKey = "<IoT Central Group SAS key>";
    //
    ```

    Speichern Sie die Änderungen.

1. Führen Sie die folgenden Befehle aus, um die erforderlichen Pakete zu installieren und das Gerät auszuführen:

    ```bash
    cd ~/iot-central-compute/device
    npm install
    node device.js
    ```

1. Das Ergebnis dieses Befehls sieht wie die folgende Ausgabe aus:

    ```output
    registration succeeded
    assigned hub=iotc-2bd611b0....azure-devices.net
    deviceId=computeDevice
    Client connected
    send status: MessageEnqueued [{"data":"33.23, 69.09, 30.7213, -61.1192"}]
    send status: MessageEnqueued [{"data":"2.43, 75.86, -2.6358, 162.935"}]
    send status: MessageEnqueued [{"data":"6.19, 76.55, -14.3538, -82.314"}]
    send status: MessageEnqueued [{"data":"33.26, 48.01, 71.9172, 48.6606"}]
    send status: MessageEnqueued [{"data":"40.5, 36.41, 14.6043, 14.079"}]
    ```

1. Navigieren Sie in Ihrer IoT Central-Anwendung zum Gerät namens **computeDevice**. In der **Rohdaten**-Ansicht gibt es zwei verschiedene Telemetriedatenströme, die etwa alle fünf Sekunden angezeigt werden. Der Stream mit nicht modellierten Daten ist die ursprüngliche Telemetrie, der Stream mit modellierten Daten besteht aus den Daten, die die Funktion transformiert hat:

    :::image type="content" source="media/howto-transform-data/egress-telemetry.png" alt-text="Screenshot der ursprünglichen und transformierte Rohdaten.":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die erstellten Azure-Ressourcen nicht mehr benötigen, während Sie die Schritte in diesem Leitfaden ausführen, löschen Sie die [Ressourcengruppen im Azure-Portal](https://portal.azure.com/?r=1#blade/HubsExtension/BrowseResourceGroups).

Die beiden Ressourcengruppen, die Sie in diesem Leitfaden verwendet haben, sind **Eingangsszenario** und **Ausgangsszenario**.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie die verschiedenen Optionen zum Transformieren von Gerätedaten für IoT Central sowohl beim eingehenden als auch beim ausgehenden Datenverkehr kennengelernt. Der Artikel enthält exemplarische Vorgehensweisen für zwei spezifische Szenarien:

- Verwenden Sie ein IoT Edge-Modul, um Daten von nachgeschalteten Geräten zu transformieren, bevor Sie die Daten an Ihre IoT Central gesendet werden.
- Verwenden Azure Functions zum Transformieren von Daten außerhalb von IoT Central. In diesem Szenario verwendet IoT Central einen Datenexport, um eingehende Daten an eine zu transformierte Azure-Funktion zu senden. Die Funktion sendet die transformierten Daten zurück an Ihre IoT Central-Anwendung.

Nachdem Sie nun gelernt haben, wie Sie Gerätedaten außerhalb Ihrer Azure IoT Central-Anwendung transformieren, können mehr zum Thema [Analysieren von Gerätedaten mithilfe von Analytics](howto-create-analytics.md) erfahren.
