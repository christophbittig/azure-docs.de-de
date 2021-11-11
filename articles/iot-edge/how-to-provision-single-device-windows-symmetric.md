---
title: Erstellen und Bereitstellen eines IoT Edge-Geräts unter Windows mithilfe von symmetrischen Schlüsseln – Azure loT Edge | Microsoft-Dokumentation
description: Erstellen und Bereitstellen eines einzelnen Windows IoT Edge-Geräts in IoT Hub mithilfe der manuellen Bereitstellung mit symmetrischen Schlüsseln
author: v-tcassi
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 09/30/2021
ms.author: v-tcassi
monikerRange: iotedge-2018-06
ms.openlocfilehash: c9ae8eea18225fca6634d55b48b80ec29e713135
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270565"
---
# <a name="create-and-provision-an-iot-edge-device-on-windows-using-symmetric-keys"></a>Erstellen und Bereitstellen eines IoT Edge-Geräts unter Windows mithilfe von symmetrischen Schlüsseln

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Dieser Artikel enthält End-to-End-Anweisungen zum Registrieren und Bereitstellen eines Windows IoT Edge-Geräts.

>[!NOTE]
>Azure IoT Edge mit Windows-Containern wird ab Version 1.2 von Azure IoT Edge nicht mehr unterstützt.
>
>Ziehen Sie die neue Methode [Azure IoT Edge für Linux unter Windows](iot-edge-for-linux-on-windows.md) zum Ausführen von IoT Edge auf Windows-Geräten in Betracht.

Jedes Gerät, das eine Verbindung mit einem IoT-Hub herstellt, verfügt über eine Geräte-ID, die zum Nachverfolgen der Cloud-zu-Gerät- oder Gerät-zu-Cloud-Kommunikation verwendet wird. Sie konfigurieren ein Gerät mit den Verbindungsinformationen, einschließlich des IoT Hub-Hostnamens, der Geräte-ID und der Informationen, die das Gerät zum Authentifizieren bei IoT Hub verwendet.

Die Schritte in diesem Artikel führen Sie durch die sogenannte manuelle Bereitstellung, bei der Sie ein einzelnes Gerät mit dem entsprechenden IoT-Hub verbinden. Bei der manuellen Bereitstellung haben Sie zwei Optionen zum Authentifizieren von IoT Edge-Geräten:

* **Symmetrische Schlüssel**: Wenn Sie in IoT Hub eine neue Geräteidentität erstellen, erstellt der Dienst zwei Schlüssel. Ein Schlüssel befindet sich auf dem Gerät, den es bei der Authentifizierung in IoT Hub vorzeigt.

  Diese Authentifizierungsmethode kann schneller eingerichtet werden, ist dafür aber weniger sicher.

* **X.509, selbstsigniert**: Sie erstellen zwei X. 509-Identitätszertifikate und bewahren diese auf dem Gerät auf. Wenn Sie in IoT Hub eine neue Geräteidentität erstellen, geben Sie Fingerabdrücke aus beiden Zertifikaten an. Wenn sich das Gerät bei IoT Hub authentifiziert, übergibt es ein Zertifikat, und IoT Hub überprüft, ob das Zertifikat mit dem Fingerabdruck übereinstimmt.

  Diese Authentifizierungsmethode ist sicherer und wird für Produktionsszenarien empfohlen.

In diesem Artikel wird die Verwendung von symmetrischen Schlüsseln als Authentifizierungsmethode behandelt. Wenn Sie X.509-Zertifikate verwenden möchten, lesen Sie [Erstellen und Bereitstellen eines IoT Edge-Geräts unter Windows mithilfe von X.509-Zertifikaten](how-to-provision-single-device-windows-x509.md).

> [!NOTE]
> Wenn Sie über sehr viele Geräte verfügen, die Sie nicht einzeln manuell bereitstellen möchten, informieren Sie sich in einem der folgenden Artikel über die Integration von IoT Edge in IoT Hub Device Provisioning Service:
>
> * [Bedarfsgerechtes Erstellen und Bereitstellen von IoT Edge-Geräten mithilfe von X.509-Zertifikaten](how-to-provision-devices-at-scale-windows-x509.md)
> * [Bedarfsgerechtes Erstellen und Bereitstellen von IoT Edge-Geräten mit einem TPM](how-to-auto-provision-simulated-device-windows.md)
> * [Bedarfsgerechtes Erstellen und Bereitstellen von IoT Edge-Geräten mithilfe von symmetrischen Schlüsseln](how-to-provision-devices-at-scale-windows-symmetric.md)

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird beschrieben, wie Sie Ihr IoT Edge-Gerät registrieren und darauf IoT Edge installieren. Bei diesen Aufgaben gibt es unterschiedliche Voraussetzungen und Hilfsprogramme zu deren Erledigung. Stellen Sie sicher, dass alle Voraussetzungen erfüllt sind, bevor Sie den Vorgang fortsetzen.

### <a name="device-registration"></a>Geräteregistrierung

Sie können die Schritte zum Registrieren Ihres Geräts über das **Azure-Portal**, mithilfe von **Visual Studio Code** oder über die **Azure CLI** ausführen. Bei jedem Hilfsprogramm gibt es eigene Voraussetzungen:

# <a name="portal"></a>[Portal](#tab/azure-portal)

Ein [IoT-Hub](../iot-hub/iot-hub-create-through-portal.md) in Ihrem Azure-Abonnement im Tarif „Free“ oder „Standard“.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Ein [IoT-Hub](../iot-hub/iot-hub-create-through-portal.md) in Ihrem Azure-Abonnement im Tarif „Free“ oder „Standard“
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure IoT-Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) für Visual Studio Code

> [!NOTE]
> Derzeit unterstützt die Azure IoT-Erweiterung für Visual Studio Code die Geräteregistrierung mit X.509-Zertifikaten nicht.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

* Ein [IoT-Hub](../iot-hub/iot-hub-create-using-cli.md) in Ihrem Azure-Abonnement im Tarif „Free“ oder „Standard“.
* Die [Azure CLI](/cli/azure/install-azure-cli) ist in Ihrer Umgebung vorhanden. Ihre Azure CLI-Version muss mindestens 2.0.70 oder höher lauten. Verwenden Sie `az --version`, um dies zu überprüfen. Diese Version unterstützt az-Erweiterungsbefehle, und das Framework für Knack-Befehle wird eingeführt.

---

### <a name="iot-edge-installation"></a>IoT Edge-Installation

Ein Windows-Gerät

IoT Edge erfordert in Kombination mit Windows-Containern die Windows-Version 1809/Build 17763. Dies ist der neueste [Windows Long Term Support-Build](/windows/release-information/). Sehen Sie unbedingt die [Liste der unterstützten Systeme](support.md#operating-systems) mit der Liste der unterstützten SKUs durch.

## <a name="register-your-device"></a>Registrieren Ihres Geräts

Je nach Bedarf können Sie Ihr Gerät über das **Azure-Portal**, mithilfe von **Visual Studio Code** oder über die **Azure CLI** registrieren.

# <a name="portal"></a>[Portal](#tab/azure-portal)

In Ihrem IoT-Hub im Azure-Portal werden IoT Edge-Geräte separat von IoT-Geräten erstellt und verwaltet, die nicht mit Edge verwendet werden können.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrem IoT Hub.

1. Klicken Sie im linken Bereich auf **IoT Edge**, und klicken Sie dann auf **IoT Edge Geräte hinzufügen**.

   ![Hinzufügen eines IoT Edge-Geräts im Azure-Portal](./media/how-to-provision-single-device-windows-symmetric/portal-add-iot-edge-device.png)

1. Geben Sie auf der Seite **Gerät erstellen** die folgenden Informationen ein:

   * Erstellen Sie eine eindeutige Geräte-ID. Notieren Sie sich die Geräte-ID, da Sie sie später verwenden werden.
   * Wählen Sie als Authentifizierungstyp **Symmetrischer Schlüssel** aus.
   * Verwenden Sie die Standardeinstellungen, um Authentifizierungsschlüssel automatisch zu generieren und das neue Gerät mit Ihrem Hub zu verbinden.

1. Wählen Sie **Speichern**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="sign-in-to-access-your-iot-hub"></a>Anmeldung zum Zugreifen auf Ihren IoT Hub

Sie können die Azure IoT-Erweiterungen für Visual Studio Code verwenden, um Vorgänge mit Ihren IoT Hub auszuführen. Damit diese Vorgänge funktionieren, müssen Sie sich bei Ihrem Azure-Konto anmelden und Ihren Hub auswählen.

1. Öffnen Sie in Visual Studio Code die **Explorer**-Ansicht.
1. Erweitern Sie im unteren Bereich des Explorers den Abschnitt **Azure IoT Hub**.

   ![Erweitern des Abschnitts „Azure IoT Hub Devices“ (Azure IoT Hub-Geräte)](./media/how-to-provision-single-device-windows-symmetric/azure-iot-hub-devices.png)

1. Klicken Sie auf **...** in der Kopfzeile des Abschnitts **Azure IoT Hub**. Wenn die Ellipse nicht angezeigt wird, zeigen Sie auf den Header, oder klicken Sie darauf.
1. Wählen Sie **Select IoT Hub** (IoT Hub auswählen) aus.
1. Wenn Sie nicht bei Ihrem Azure-Konto angemeldet sind, befolgen Sie die angezeigten Aufforderungen.
1. Wählen Sie Ihr Azure-Abonnement.
1. Wählen Sie Ihren IoT Hub aus.

### <a name="register-a-new-device-with-visual-studio-code"></a>Registrieren eines neuen Geräts mit Visual Studio Code

1. Erweitern Sie im Visual Studio Code-Explorer den Abschnitt **Azure IoT Hub**.
1. Klicken Sie auf **...** in der Kopfzeile des Abschnitts **Azure IoT Hub**. Wenn die Ellipse nicht angezeigt wird, zeigen Sie auf den Header, oder klicken Sie darauf.
1. Wählen Sie **Create IoT Edge Device** (IoT Edge-Gerät erstellen) aus.
1. Geben Sie im geöffneten Textfeld eine ID für Ihr Gerät an.

Auf dem Ausgabebildschirm wird das Ergebnis des Befehls angezeigt. Die Geräteinformationen werden ausgegeben, einschließlich der **deviceID**, die Sie angegeben haben, und der Zeichenfolge **connectionString**, mit der Sie Ihr physisches Gerät mit Ihrem IoT Hub verbinden können.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie den Befehl [az iot hub device-identity create](/cli/azure/iot/hub/device-identity), um eine neue Geräteidentität in Ihrem IoT-Hub zu erstellen. Beispiel:

   ```azurecli
   az iot hub device-identity create --device-id [device_id] --hub-name [hub_name] --edge-enabled
   ```

Dieser Befehl weist drei Parameter auf:

* `--device-id` oder `-d`: Geben Sie einen beschreibenden Namen an, der in Ihrem IoT-Hub eindeutig ist.
* `--hub-name` oder `-n`: Geben Sie den Namen Ihres IoT Hub an.
* `--edge-enabled` oder `--ee`: Deklarieren Sie, dass das Gerät ein IoT Edge-Gerät ist.

   ![az iot hub device-identity create output](./media/how-to-provision-single-device-windows-symmetric/create-edge-device-cli.png)

---

Nachdem Sie ein Gerät in IoT Hub registriert haben, rufen Sie jetzt die Verbindungszeichenfolge ab, mit der Sie die Installation und Bereitstellung der IoT Edge-Runtime abschließen.

## <a name="view-registered-devices-and-retrieve-provisioning-information"></a>Anzeigen registrierter Geräte und Abrufen von Bereitstellungsinformationen

Für Geräte, die die Authentifizierung mit symmetrischen Schlüsseln verwenden, wird eine eigene Verbindungszeichenfolge benötigt, um die Installation und Bereitstellung der IoT Edge-Runtime abzuschließen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Alle auf die Verwendung mit Edge ausgelegten Geräte, die eine Verbindung mit Ihrem IoT-Hub herstellen, sind auf der Seite **IoT Edge** aufgeführt.

![Anzeigen aller IoT Edge-Geräte in Ihrem IoT-Hub mit dem Azure-Portal](./media/how-to-provision-single-device-windows-symmetric/portal-view-devices.png)

Wenn Sie bereit für die Einrichtung Ihres Geräts sind, benötigen Sie die Verbindungszeichenfolge, die Ihr physisches Gerät mit seiner Identität in IoT Hub verknüpft.

Sie können die Verbindungszeichenfolgen von Geräten, die sich mit symmetrischen Schlüsseln authentifizieren, im Portal kopieren.

1. Klicken Sie im Portal auf der Seite **IoT Edge** in der Liste der IoT Edge-Geräte auf die Geräte-ID.
2. Kopieren Sie entweder den Wert der **primären Verbindungszeichenfolge** oder der **sekundären Verbindungszeichenfolge**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Alle Geräte, die Verbindungen mit Ihrem IoT Hub herstellen, werden im Abschnitt **Azure IoT Hub** des Visual Studio Code-Explorers aufgeführt. IoT Edge-Geräte können von Nicht-Edge-Geräten unterschieden werden, da sie ein anderes Symbol aufweisen, und aufgrund der Tatsache, dass die Module **$edgeAgent** und **$edgeHub** auf jedem IoT Edge-Gerät bereitgestellt sind.

![Anzeigen aller IoT Edge-Geräte in Ihrem IoT-Hub mit VS Code](./media/how-to-provision-single-device-windows-symmetric/view-devices.png)

Wenn Sie bereit für die Einrichtung Ihres Geräts sind, benötigen Sie die Verbindungszeichenfolge, die Ihr physisches Gerät mit seiner Identität in IoT Hub verknüpft.

1. Klicken Sie mit der rechten Maustaste auf die ID des Geräts im Abschnitt **Azure IoT Hub**.
1. Wählen Sie **Copy Device Connection String** (Verbindungszeichenfolge des Geräts kopieren) aus.

   Die Verbindungszeichenfolge wird in die Zwischenablage kopiert.

Sie können auch im Kontextmenü **Get Device Info** (Geräteinformationen abrufen) auswählen, um alle Geräteinformationen einschließlich der Verbindungszeichenfolge im Ausgabefenster anzuzeigen.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie den Befehl [az iot hub device-identity list](/cli/azure/iot/hub/device-identity), um alle Geräte in Ihrem IoT-Hub anzuzeigen. Beispiel:

   ```azurecli
   az iot hub device-identity list --hub-name [hub_name]
   ```

Alle als IoT Edge-Gerät registrierten Geräte weisen die **capabilities.iotEdge**-Eigenschaft mit dem Wert **true** auf.

Wenn Sie bereit für die Einrichtung Ihres Geräts sind, benötigen Sie die Verbindungszeichenfolge, die Ihr physisches Gerät mit seiner Identität in IoT Hub verknüpft. Verwenden Sie den Befehl [az iot hub device-identity connection-string show](/cli/azure/iot/hub/device-identity/connection-string), um die Verbindungszeichenfolge für ein einzelnes Gerät zurückzugeben:

   ```azurecli
   az iot hub device-identity connection-string show --device-id [device_id] --hub-name [hub_name]
   ```

>[!TIP]
>Der Befehl `connection-string show` wurde in Version 0.9.8 der Azure IoT-Erweiterung eingeführt und ersetzt den veralteten Befehl `show-connection-string`. Wenn Sie beim Ausführen dieses Befehls einen Fehler erhalten, stellen Sie sicher, dass die Erweiterung mindestens auf Version 0.9.8 aktualisiert wurde. Weitere Informationen und die neuesten Updates finden Sie unter der [Microsoft Azure IoT-Erweiterung für die Azure-Befehlszeilenschnittstelle](https://github.com/Azure/azure-iot-cli-extension).

Beim Wert für den Parameter `device-id` muss die Groß-/Kleinschreibung beachtet werden.

Achten Sie darauf, beim Kopieren der Verbindungszeichenfolge für die Verwendung auf einem Gerät nicht die Anführungszeichen um die Verbindungszeichenfolge herum mit zu kopieren.

---

## <a name="install-iot-edge"></a>Installieren von IoT Edge

In diesem Abschnitt bereiten Sie Ihr Windows-Gerät für IoT Edge vor. Anschließend installieren Sie IoT Edge.

Azure IoT Edge basiert auf einer OCI-kompatiblen Containerruntime. [Moby](https://github.com/moby/moby), eine Moby-basierte Engine, ist im Installationsskript enthalten. Dies bedeutet, dass es keine zusätzlichen Schritte zum Installieren der Engine gibt.

So installieren Sie die IoT Edge-Runtime

1. Führen Sie PowerShell als Administrator aus.

   Verwenden Sie eine AMD64-Sitzung von PowerShell, nicht PowerShell(x86). Wenn Sie nicht sicher sind, welchen Sitzungstyp Sie verwenden, führen Sie den folgenden Befehl aus:

   ```powershell
   (Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   ```

2. Führen Sie den Befehl [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) aus, der die folgenden Aufgaben ausführt:

   * Überprüft, ob Ihr Windows-Computer mit einer unterstützten Version läuft.
   * Aktiviert das Containerfeature.
   * Lädt die Moby-Engine und die IoT Edge-Runtime herunter.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

3. Starten Sie das Gerät neu, wenn Sie dazu aufgefordert werden.

Wenn Sie IoT Edge auf einem Gerät installieren, können Sie zusätzliche Parameter verwenden, um den Prozess folgendermaßen anzupassen:

* Sie können direkten Datenverkehr über einen Proxyserver leiten.
* Legen Sie im Installationsprogramm ein lokales Verzeichnis für die Offlineinstallation fest.

Weitere Informationen zu diesen zusätzlichen Parametern finden Sie unter [PowerShell-Skripts für IoT Edge mit Windows-Containern](reference-windows-scripts.md).

## <a name="provision-the-device-with-its-cloud-identity"></a>Bereitstellen des Geräts mit seiner Cloud-Identität

Nachdem die Containerengine und die IoT Edge-Runtime auf Ihrem Gerät installiert wurden, sind Sie nun für den nächsten Schritt bereit, nämlich das Einrichten des Geräts mit seinen Cloudidentitäts- und Authentifizierungsinformationen.

1. Führen Sie PowerShell auf dem IoT Edge-Gerät als Administrator aus.

2. Verwenden Sie den Befehl [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge), um die IoT Edge-Runtime auf Ihrem Computer zu konfigurieren. Standardmäßig wird für den Befehl die manuelle Bereitstellung mit Windows-Containern verwendet.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualConnectionString -ContainerOs Windows
   ```

   * Wenn Sie das Skript „IoTEdgeSecurityDaemon.ps1“ auf Ihr Gerät heruntergeladen haben, um eine Offlineinstallation durchzuführen oder eine bestimmte Version zu installieren, müssen Sie auf die lokale Kopie des Skripts verweisen.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualConnectionString -ContainerOs Windows
      ```

3. Geben Sie bei Aufforderung die Geräteverbindungszeichenfolge ein, die Sie im vorherigen Abschnitt abgerufen haben. Die Verbindungszeichenfolge des Geräts ordnet das physische Gerät in IoT Hub einer Geräte-ID zu und bietet Authentifizierungsinformationen.

   Die Verbindungszeichenfolge hat das folgende Format und darf keine Anführungszeichen enthalten: `HostName={IoT_hub_name}.azure-devices.net;DeviceId={device_name};SharedAccessKey={key}`

Wenn Sie ein Gerät manuell bereitstellen, können Sie zusätzliche Parameter verwenden, um den Prozess folgendermaßen anzupassen:

* Sie können direkten Datenverkehr über einen Proxyserver leiten.
* Sie können ein bestimmtes edgeAgent-Containerimage deklarieren und Anmeldeinformationen bereitstellen, wenn dieses sich in einer privaten Registrierung befindet.

Weitere Informationen zu diesen zusätzlichen Parametern finden Sie unter [PowerShell-Skripts für IoT Edge mit Windows-Containern](reference-windows-scripts.md).

## <a name="offline-or-specific-version-installation-optional"></a>Offlineinstallation oder Installation einer bestimmten Version (optional)

Die in diesem Abschnitt aufgeführten Schritte sind für Szenarien vorgesehen, die von den Schritten der Standardinstallation nicht abgedeckt werden. Das können beispielsweise sein:

* Die Offlineinstallation von IoT Edge
* Die Installation einer Release Candidate-Version
* Installation einer anderen als der aktuellsten Version

Während der Installation werden drei Dateien heruntergeladen:

* Ein PowerShell-Skript, das die Installationsanweisungen enthält
* Eine Microsoft Azure IoT Edge-CAB-Datei, die IoT Edge-Sicherheitsdaemon (iotedged), Moby-Container-Engine und Moby-CLI enthält
* Ein Installationsprogramm für das Visual C++ Redistributable Package (VC-Runtime)

Wenn Ihr Gerät während der Installation offline ist oder wenn Sie eine bestimmte Version von IoT Edge installieren möchten, können Sie diese Dateien im Voraus auf das Gerät herunterladen. Wenn Sie die Installation durchführen möchten, verweisen Sie im Installationsskript auf das Verzeichnis, in dem die heruntergeladenen Dateien gespeichert sind. Das Installationsprogramm überprüft dieses Verzeichnis zuerst und lädt dann nur Komponenten herunter, die nicht gefunden werden konnten. Wenn alle Dateien offline verfügbar sind, können Sie die Installation ohne Internetverbindung durchführen.

1. Die aktuellen IoT Edge-Installationsdateien und die früheren Versionen finden Sie auf der Seite für [Azure IoT Edge-Releases](https://github.com/Azure/azure-iotedge/releases).

2. Suchen Sie die Version, die Sie installieren möchten, und laden Sie die folgenden Dateien aus dem Abschnitt **Assets** der Versionshinweise auf Ihr IoT-Gerät herunter:

   * IoTEdgeSecurityDaemon.ps1
   * Microsoft-Azure-IoTEdge-amd64.cab aus dem Releasekanal 1.1.

   Es ist wichtig, das PowerShell-Skript für dasselbe Release zu verwenden, zu dem die CAB-Datei gehört, weil die Unterstützung für Features sich in jedem Release ändert.

3. Wenn die von Ihnen heruntergeladene CAB-Datei ein Suffix der Architektur enthält, benennen Sie die Datei in **Microsoft-Azure-IoTEdge.cab** um.

4. Optional können Sie auch ein Installationsprogramm für Visual C++ Redistributable herunterladen. Das PowerShell-Skript verwendet beispielsweise diese Version: [vc_redist.x64.exe](https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe). Speichern Sie das Installationsprogramm auf Ihrem IoT-Gerät im selben Ordner, in dem sich die IoT Edge-Dateien befinden.

5. Für eine Installation mit Offlinekomponenten führen Sie eine [DOT-Quellentnahme](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing) in der lokalen Kopie des PowerShell-Skripts durch.

6. Führen Sie den Befehl [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) mit dem Parameter `-OfflineInstallationPath` aus. Geben Sie den absoluten Pfad zum Dateiverzeichnis an. Beispiel:

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath <path>
   ```

   Der Bereitstellungsbefehl verwendet alle Komponenten, die im angegebenen lokalen Dateiverzeichnis gefunden werden. Wenn die CAB-Datei oder der Visual C++-Installer fehlt, versucht der Befehl, diese Komponenten herunterzuladen.

## <a name="uninstall-iot-edge"></a>Deinstallieren von IoT Edge

Verwenden Sie die folgenden Befehle, wenn Sie die IoT Edge-Installationen von Ihrem Gerät entfernen möchten.

Wenn Sie IoT Edge von Ihrem Windows-Gerät entfernen möchten, sollten Sie den Befehl [Uninstall-IoTEdge](reference-windows-scripts.md#uninstall-iotedge) in einem PowerShell-Fenster ausführen, das mit Administratorrechten geöffnet wurde. Dieser Befehl entfernt die IoT Edge-Runtime, die vorhandenen Konfigurationen und die Daten der Moby-Engine.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

Um weitere Informationen zu den Deinstallationsoptionen zu erhalten, verwenden Sie den Befehl `Get-Help Uninstall-IoTEdge -full`.

## <a name="next-steps"></a>Nächste Schritte

Sie können nun mit dem Artikel zum [Bereitstellen von IoT Edge-Modulen](how-to-deploy-modules-portal.md) fortfahren, um zu erfahren, wie Sie Module auf Ihrem Gerät bereitstellen.
