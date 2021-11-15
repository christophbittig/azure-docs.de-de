---
title: Skripts für Azure IoT Edge mit Windows-Containern | Microsoft-Dokumentation
description: Referenzinformationen für IoT Edge PowerShell-Skripts zum Installieren, Deinstallieren oder Aktualisieren auf Windows-Geräten
author: kgremban
ms.author: kgremban
ms.date: 10/06/2020
ms.topic: reference
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: bd450569298dfe8782f1a37b6c565daff0ae6175
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130259165"
---
# <a name="powershell-scripts-for-iot-edge-with-windows-containers"></a>PowerShell-Skripts für IoT Edge mit Windows-Containern

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Grundlegendes über die PowerShell-Skripts, mit denen IoT Edge auf Windows-Geräten installiert, aktualisiert oder deinstalliert wird.

Die in diesem Artikel beschriebenen Befehle stammen aus der `IoTEdgeSecurityDaemon.ps1`-Datei, die zusammen mit jeder [IoT Edge-Version](https://github.com/Azure/azure-iotedge/releases) veröffentlicht wird. Die neueste Version des Skripts ist jeweils unter aka.ms/iotedge-win verfügbar.

Sie können jeden der Befehle mithilfe des `Invoke-WebRequest`-Cmdlets aufrufen, um auf die aktuellste Skriptversion zuzugreifen. Beispiel:

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge
```

Sie können dieses Skript oder eine Version des Skripts aus einer bestimmten Version auch herunterladen, um die Befehle auszuführen. Beispiel:

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Deploy-IoTEdge
```

Das bereitgestellte Skript ist signiert, um die Sicherheit zu erhöhen. Sie können die Signatur überprüfen, indem Sie das Skript auf Ihr Gerät herunterladen und dann den folgenden PowerShell-Befehl ausführen:

```powershell
Get-AuthenticodeSignature "C:\<path>\IotEdgeSecurityDaemon.ps1"
```

Der Ausgabestatus lautet **Gültig**, wenn die Signatur überprüft wird.

## <a name="deploy-iotedge"></a>Deploy-IoTEdge

Der Befehl Deploy-IoTEdge lädt den IoT Edge-Sicherheitsdaemon und die zugehörigen Abhängigkeiten herunter und stellt sie bereit. Der Bereitstellungsbefehl akzeptiert diese allgemeinen Parametern neben anderen. Mit dem Befehl `Get-Help Deploy-IoTEdge -full` können Sie die vollständige Liste anzeigen.  

| Parameter | Zulässige Werte | Kommentare |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** oder **Linux** | Wenn kein Containerbetriebssystem angegeben wird, ist „Windows“ der Standardwert.<br><br>Für Windows-Container verwendet IoT Edge die in der Installation enthaltene Moby-Containerengine. Für Linux-Container müssen Sie eine Container-Engine installieren, bevor Sie mit der Installation beginnen. |
| **Proxy** | Proxy-URL | Verwenden Sie diesen Parameter, wenn Ihr Gerät die Internetverbindung über einen Proxyserver herstellen muss. Weitere Informationen finden Sie unter [Konfigurieren eines IoT Edge-Geräts für die Kommunikation über einen Proxyserver](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Verzeichnispfad | Wenn dieser Parameter verwendet wird, überprüft das Installationsprogramm das aufgelistete Verzeichnis auf die IoT Edge-CAB- und die VC-Runtime-MSI-Dateien, die für die Installation erforderlich sind. Alle im Verzeichnis nicht gefundenen Dateien werden heruntergeladen. Wenn sich beide Dateien im Verzeichnis befinden, können Sie IoT Edge ohne Internetverbindung installieren. Sie können diesen Parameter auch verwenden, um eine bestimmte Version zu verwenden. |
| **InvokeWebRequestParameters** | Hashtabelle mit Parametern und Werten | Während der Installation werden mehrere Webanforderungen durchgeführt. Verwenden Sie dieses Feld, um die Parameter für diese Webanforderungen festzulegen. Dieser Parameter ist bei der Konfiguration der Anmeldeinformationen für Proxyserver hilfreich. Weitere Informationen finden Sie unter [Konfigurieren eines IoT Edge-Geräts für die Kommunikation über einen Proxyserver](how-to-configure-proxy-support.md). |
| **RestartIfNeeded** | none | Dieses Flag ermöglicht dem Bereitstellungsskript, den Computer bei Bedarf ohne Eingabeaufforderung neu zu starten. |

## <a name="initialize-iotedge"></a>Initialize-IoTEdge

Der Initialize-IoTEdge-Befehl konfiguriert IoT Edge mit Ihrer Geräte-Verbindungszeichenfolge und Ihren Betriebsdetails. Ein Großteil der durch diesen Befehl generierten Informationen wird dann in der Datei „iotedge\config.yaml“ gespeichert. Der Initialisierungsbefehl akzeptiert diese allgemeinen Parametern neben anderen. Mit dem Befehl `Get-Help Initialize-IoTEdge -full` können Sie die vollständige Liste anzeigen.

| Parameter | Zulässige Werte | Kommentare |
| --------- | --------------- | -------- |
| **ManualConnectionString** | Keine | **Switch-Parameter:** **Standardwert**. Wenn kein Bereitstellungstyp angegeben wird, ist die manuelle Bereitstellung mithilfe einer Verbindungszeichenfolge der Standardwert.<br><br>Gibt an, dass Sie eine Geräteverbindungszeichenfolge angeben, um das Gerät manuell bereitzustellen. |
| **ManualX509** | Keine | **Switch-Parameter:** Wenn kein Bereitstellungstyp angegeben wird, ist die manuelle Bereitstellung mithilfe einer Verbindungszeichenfolge der Standardwert.<br><br>Gibt an, dass Sie ein Identitätszertifikat und einen privaten Schlüssel bereitstellen werden, um das Gerät manuell bereitzustellen.
| **DpsTpm** | Keine | **Switch-Parameter:** Wenn kein Bereitstellungstyp angegeben wird, ist die manuelle Bereitstellung mithilfe einer Verbindungszeichenfolge der Standardwert.<br><br>Gibt an, dass Sie eine Bereichs-ID von einem Gerätebereitstellungsdienst (Device Provisioning Service, DPS) und die Registrierungs-ID Ihres Geräts angeben, damit die Bereitstellung über einen Gerätebereitstellungsdienst durchgeführt wird.  |
| **DpsSymmetricKey** | Keine | **Switch-Parameter:** Wenn kein Bereitstellungstyp angegeben wird, ist die manuelle Bereitstellung mithilfe einer Verbindungszeichenfolge der Standardwert.<br><br>Gibt an, dass Sie eine Bereichs-ID von einem Device Provisioning Service (DPS) und die Registrierungs-ID Ihres Geräts zusammen mit einem symmetrischen Schlüssel für den Nachweis angeben, damit die Bereitstellung über DPS durchgeführt wird. |
| **DpsX509** | Keine | **Switch-Parameter:** Wenn kein Bereitstellungstyp angegeben wird, ist die manuelle Bereitstellung mithilfe einer Verbindungszeichenfolge der Standardwert.<br><br>Gibt an, dass Sie eine Bereichs-ID von einem Device Provisioning Service (DPS) und die Registrierungs-ID Ihres Geräts zusammen mit einem X.509-Identitätszertifikat und einem privaten Schlüssel zum Nachweis angeben, damit die Bereitstellung über DPS durchgeführt wird.  |
| **DeviceConnectionString** | Eine Verbindungszeichenfolge, die von einem IoT Edge-Gerät stammt, das in einem IoT-Hub registriert ist (in einfachen Anführungszeichen). | **Erforderlich** für die manuelle Bereitstellung mit einer Verbindungszeichenfolge. Wenn Sie in den Skriptparametern keine Verbindungszeichenfolge angeben, werden Sie dazu aufgefordert. |
| **IotHubHostName** | Der Hostname des IoT-Hubs, mit dem ein Gerät eine Verbindung herstellt. | **Erforderlich** für die manuelle Bereitstellung mit X.509-Zertifikaten. Weist das Format *{Hubname}.azure-devices.net* auf. |
| **DeviceId** | Die Geräte-ID einer in IoT Hub registrierten Geräteidentität. | **Erforderlich** für die manuelle Bereitstellung mit X.509-Zertifikaten. |
| **ScopeId** | Eine Bereichs-ID, die aus der Instanz des Gerätebereitstellungsdiensts stammt, die Ihrem IoT-Hub zugeordnet ist. | **Erforderlich** für die DPS-Bereitstellung. Wenn Sie in den Skriptparametern keine Bereichs-ID angeben, werden Sie dazu aufgefordert. |
| **RegistrationId** | Eine Registrierungs-ID, die von Ihrem Gerät generiert wurde. | **Erforderlich** für die DPS-Bereitstellung, wenn TPM oder der Nachweis des symmetrischen Schlüssels verwendet wird. **Optional**, wenn der X.509-Zertifikatnachweis verwendet wird. |
| **X509IdentityCertificate** | Der URI-Pfad zum X.509-Geräteidentitätszertifikat auf dem Gerät. | **Erforderlich** für die manuelle Bereitstellung oder die Bereitstellung mit einem Device Provisioning Service, wenn ein X.509-Zertifikatnachweis verwendet wird. |
| **X509IdentityPrivateKey** | Der URI-Pfad zum Schlüssel für das X.509-Geräteidentitätszertifikat auf dem Gerät. | **Erforderlich** für die manuelle Bereitstellung oder die Bereitstellung mit einem Device Provisioning Service, wenn ein X.509-Zertifikatnachweis verwendet wird. |
| **SymmetricKey** | Der symmetrische Schlüssel, der bei der Verwendung von IoT Hub Device Provisioning Service für die Bereitstellung der IoT Edge-Geräteidentität verwendet wird. | **Erforderlich** für die DPS-Bereitstellung, wenn der Nachweis des symmetrischen Schlüssels verwendet wird. |
| **ContainerOs** | **Windows** oder **Linux** | Wenn kein Containerbetriebssystem angegeben wird, ist „Windows“ der Standardwert.<br><br>Für Windows-Container verwendet IoT Edge die in der Installation enthaltene Moby-Containerengine. Für Linux-Container müssen Sie eine Container-Engine installieren, bevor Sie mit der Installation beginnen. |
| **DeviceCACertificate** | Der URI-Pfad zum X.509-Gerätezertifikat der Zertifizierungsstelle auf dem Gerät. | Kann auch in der `C:\ProgramData\iotedge\config.yaml`-Datei konfiguriert werden. Weitere Informationen finden Sie unter [Verwalten von Zertifikaten auf einem IoT Edge-Gerät](how-to-manage-device-certificates.md). |
| **DeviceCAPrivateKey** | Der URI-Pfad zum Schlüssel für das X.509-Gerätezertifikat einer Zertifizierungsstelle auf dem Gerät. | Kann auch in der `C:\ProgramData\iotedge\config.yaml`-Datei konfiguriert werden. Weitere Informationen finden Sie unter [Verwalten von Zertifikaten auf einem IoT Edge-Gerät](how-to-manage-device-certificates.md). |
| **InvokeWebRequestParameters** | Hashtabelle mit Parametern und Werten | Während der Installation werden mehrere Webanforderungen durchgeführt. Verwenden Sie dieses Feld, um die Parameter für diese Webanforderungen festzulegen. Dieser Parameter ist bei der Konfiguration der Anmeldeinformationen für Proxyserver hilfreich. Weitere Informationen finden Sie unter [Konfigurieren eines IoT Edge-Geräts für die Kommunikation über einen Proxyserver](how-to-configure-proxy-support.md). |
| **AgentImage** | URI des IoT Edge-Agent-Images | Wenn Sie IoT Edge neu installieren, wird standardmäßig das neueste fortlaufende Tag für das IoT Edge-Agent-Image verwendet. Verwenden Sie diesen Parameter, um ein bestimmtes Tag für die Imageversion festzulegen, oder stellen Sie ein eigenes Agent-Image bereit. Weitere Informationen finden Sie unter [Grundlagen von IoT Edge-Tags](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Benutzername** | Benutzername der Containerregistrierung | Verwenden Sie diesen Parameter nur, wenn Sie den Parameter „-AgentImage“ auf einen Container in einer privaten Registrierung festlegen. Geben Sie einen Benutzernamen an, der auf die Registrierung zugreifen kann. |
| **Kennwort** | Sichere Kennwortzeichenfolge | Verwenden Sie diesen Parameter nur, wenn Sie den Parameter „-AgentImage“ auf einen Container in einer privaten Registrierung festlegen. Geben Sie das Kennwort an, mit dem auf die Registrierung zugegriffen werden kann. |

## <a name="update-iotedge"></a>Update-IoTEdge

| Parameter | Zulässige Werte | Kommentare |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** oder **Linux** | Wenn kein Containerbetriebssystem angegeben wird, wird der Standardwert „Windows“ verwendet. Für Windows-Container wird eine Container-Engine im Rahmen der Installation installiert. Für Linux-Container müssen Sie eine Container-Engine installieren, bevor Sie mit der Installation beginnen. |
| **Proxy** | Proxy-URL | Verwenden Sie diesen Parameter, wenn Ihr Gerät die Internetverbindung über einen Proxyserver herstellen muss. Weitere Informationen finden Sie unter [Konfigurieren eines IoT Edge-Geräts für die Kommunikation über einen Proxyserver](how-to-configure-proxy-support.md). |
| **InvokeWebRequestParameters** | Hashtabelle mit Parametern und Werten | Während der Installation werden mehrere Webanforderungen durchgeführt. Verwenden Sie dieses Feld, um die Parameter für diese Webanforderungen festzulegen. Dieser Parameter ist bei der Konfiguration der Anmeldeinformationen für Proxyserver hilfreich. Weitere Informationen finden Sie unter [Konfigurieren eines IoT Edge-Geräts für die Kommunikation über einen Proxyserver](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Verzeichnispfad | Wenn dieser Parameter verwendet wird, überprüft das Installationsprogramm das aufgelistete Verzeichnis auf die IoT Edge-CAB- und die VC-Runtime-MSI-Dateien, die für die Installation erforderlich sind. Alle im Verzeichnis nicht gefundenen Dateien werden heruntergeladen. Wenn sich beide Dateien im Verzeichnis befinden, können Sie IoT Edge ohne Internetverbindung installieren. Sie können diesen Parameter auch verwenden, um eine bestimmte Version zu verwenden. |
| **RestartIfNeeded** | none | Dieses Flag ermöglicht dem Bereitstellungsskript, den Computer bei Bedarf ohne Eingabeaufforderung neu zu starten. |

## <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| Parameter | Zulässige Werte | Kommentare |
| --------- | --------------- | -------- |
| **Force** | none | Dieses Flag erzwingt die Deinstallation für den Fall, dass der vorherige Versuch der Deinstallation nicht erfolgreich war.
| **RestartIfNeeded** | none | Dieses Flag ermöglicht dem Deinstallationsskript, den Computer bei Bedarf ohne Eingabeaufforderung neu zu starten. |

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Verwendung dieser Befehle finden Sie im folgenden Artikel:

* [Installieren oder Deinstallieren von Azure IoT Edge für Windows mithilfe von symmetrischen Schlüsseln](how-to-provision-single-device-windows-symmetric.md) oder [mithilfe von X.509-Zertifikaten](how-to-provision-single-device-windows-x509.md)
