---
title: Erstellen und Bereitstellen eines IoT Edge-Geräts unter Windows mithilfe von symmetrischen Schlüsseln – Azure loT Edge | Microsoft-Dokumentation
description: Erstellen und Bereitstellen eines einzelnen Windows IoT Edge-Geräts in IoT Hub mithilfe der manuellen Bereitstellung mit symmetrischen Schlüsseln
author: kgremban
ms.reviewer: v-tcassi
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/28/2021
ms.author: kgremban
monikerRange: iotedge-2018-06
ms.openlocfilehash: 4c831e12c7660727c966d1fdc9d0079d8b236fa5
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853356"
---
# <a name="create-and-provision-an-iot-edge-device-on-windows-using-symmetric-keys"></a>Erstellen und Bereitstellen eines IoT Edge-Geräts unter Windows mithilfe von symmetrischen Schlüsseln

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Dieser Artikel enthält End-to-End-Anweisungen zum Registrieren und Bereitstellen eines Windows IoT Edge-Geräts.

>[!NOTE]
>Azure IoT Edge mit Windows-Containern wird ab Version 1.2 von Azure IoT Edge nicht mehr unterstützt.
>
>Ziehen Sie die neue Methode [Azure IoT Edge für Linux unter Windows](iot-edge-for-linux-on-windows.md) zum Ausführen von IoT Edge auf Windows-Geräten in Betracht.
>
>Wenn Sie Azure IoT Edge für Linux unter Windows verwenden möchten, können Sie die Schritte in der [entsprechenden Schrittanleitung](how-to-provision-single-device-linux-on-windows-symmetric.md) ausführen.

Jedes Gerät, das eine Verbindung mit einem IoT-Hub herstellt, verfügt über eine Geräte-ID, die zum Nachverfolgen der Cloud-zu-Gerät- oder Gerät-zu-Cloud-Kommunikation verwendet wird. Sie konfigurieren ein Gerät mit den Verbindungsinformationen, einschließlich des IoT Hub-Hostnamens, der Geräte-ID und der Informationen, die das Gerät zum Authentifizieren bei IoT Hub verwendet.

Die Schritte in diesem Artikel führen Sie durch die sogenannte manuelle Bereitstellung, bei der Sie ein einzelnes Gerät mit dem entsprechenden IoT-Hub verbinden. Bei der manuellen Bereitstellung haben Sie zwei Optionen zum Authentifizieren von IoT Edge-Geräten:

* **Symmetrische Schlüssel**: Wenn Sie in IoT Hub eine neue Geräteidentität erstellen, erstellt der Dienst zwei Schlüssel. Ein Schlüssel befindet sich auf dem Gerät, den es bei der Authentifizierung in IoT Hub vorzeigt.

  Diese Authentifizierungsmethode kann schneller eingerichtet werden, ist dafür aber weniger sicher.

* **X.509, selbstsigniert**: Sie erstellen zwei X. 509-Identitätszertifikate und bewahren diese auf dem Gerät auf. Wenn Sie in IoT Hub eine neue Geräteidentität erstellen, geben Sie Fingerabdrücke aus beiden Zertifikaten an. Wenn sich das Gerät bei IoT Hub authentifiziert, übergibt es ein Zertifikat, und IoT Hub überprüft, ob das Zertifikat mit dem Fingerabdruck übereinstimmt.

  Diese Authentifizierungsmethode ist sicherer und wird für Produktionsszenarien empfohlen.

In diesem Artikel wird die Verwendung von symmetrischen Schlüsseln als Authentifizierungsmethode behandelt. Wenn Sie X.509-Zertifikate verwenden möchten, lesen Sie [Erstellen und Bereitstellen eines IoT Edge-Geräts unter Windows mithilfe von X.509-Zertifikaten](how-to-provision-single-device-windows-x509.md).

> [!NOTE]
> Wenn Sie viele Geräte einrichten müssen und sie nicht einzeln manuell bereitstellen möchten, informieren Sie sich in einem der folgenden Artikel, wie IoT Edge beim IoT Hub Device Provisioning Service (Gerätebereitstellungsdienst) funktioniert:
>
> * [Bedarfsgerechtes Erstellen und Bereitstellen von IoT Edge-Geräten mithilfe von X.509-Zertifikaten](how-to-provision-devices-at-scale-windows-x509.md)
> * [Bedarfsgerechtes Erstellen und Bereitstellen von IoT Edge-Geräten mit einem TPM](how-to-provision-devices-at-scale-windows-tpm.md)
> * [Bedarfsgerechtes Erstellen und Bereitstellen von IoT Edge-Geräten mithilfe von symmetrischen Schlüsseln](how-to-provision-devices-at-scale-windows-symmetric.md)

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird beschrieben, wie Sie Ihr IoT Edge-Gerät registrieren und darauf IoT Edge installieren. Bei diesen Aufgaben gibt es unterschiedliche Voraussetzungen und Hilfsprogramme zu deren Erledigung. Stellen Sie sicher, dass alle Voraussetzungen erfüllt sind, bevor Sie den Vorgang fortsetzen.

<!-- Device registration prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-register-device.md](../../includes/iot-edge-prerequisites-register-device.md)]

### <a name="iot-edge-installation"></a>IoT Edge-Installation

Ein Windows-Gerät.

IoT Edge erfordert in Kombination mit Windows-Containern die Windows-Version 1809/Build 17763. Dies ist der neueste [Windows Long Term Support-Build](/windows/release-information/). Sehen Sie unbedingt die [Liste der unterstützten Systeme](support.md#operating-systems) mit der Liste der unterstützten SKUs durch.

<!-- Register your device and View provisioning information H2s and content -->
[!INCLUDE [iot-edge-register-device-symmetric.md](../../includes/iot-edge-register-device-symmetric.md)]

<!-- Install IoT Edge on Windows H2 and content -->
[!INCLUDE [install-iot-edge-windows.md](../../includes/iot-edge-install-windows.md)]

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

## <a name="verify-successful-configuration"></a>Überprüfen der erfolgreichen Konfiguration

Vergewissern Sie sich, dass die Runtime erfolgreich auf Ihrem IoT Edge-Gerät installiert und konfiguriert wurde.

Überprüfen Sie den Status des IoT Edge-Diensts.

```powershell
Get-Service iotedge
```

Untersuchen Sie die Dienstprotokolle.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Führen Sie ausgeführte Module auf.

```powershell
iotedge list
```

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
   . path_to_powershell_module_here\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath path_to_file_directory_here
   ```

   Der Bereitstellungsbefehl verwendet alle Komponenten, die im angegebenen lokalen Dateiverzeichnis gefunden werden. Wenn die CAB-Datei oder der Visual C++-Installer fehlt, versucht der Befehl, diese Komponenten herunterzuladen.

## <a name="uninstall-iot-edge"></a>Deinstallieren von IoT Edge

Wenn Sie IoT Edge von Ihrem Windows-Gerät entfernen möchten, sollten Sie den Befehl [Uninstall-IoTEdge](reference-windows-scripts.md#uninstall-iotedge) in einem PowerShell-Fenster ausführen, das mit Administratorrechten geöffnet wurde. Dieser Befehl entfernt die IoT Edge-Runtime, die vorhandenen Konfigurationen und die Daten der Moby-Engine.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

Um weitere Informationen zu den Deinstallationsoptionen zu erhalten, verwenden Sie den Befehl `Get-Help Uninstall-IoTEdge -full`.

## <a name="next-steps"></a>Nächste Schritte

Sie können nun mit dem Artikel zum [Bereitstellen von IoT Edge-Modulen](how-to-deploy-modules-portal.md) fortfahren, um zu erfahren, wie Sie Module auf Ihrem Gerät bereitstellen.
