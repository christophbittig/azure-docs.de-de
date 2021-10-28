---
title: Konfigurieren von Geräten für Netzwerkproxys – Azure IoT Edge | Microsoft-Dokumentation
description: Erläutert das Konfigurieren der Azure IoT Edge-Runtime und der IoT Edge-Module mit Internetverbindung für die Kommunikation über einen Proxyserver.
author: kgremban
ms.author: kgremban
ms.date: 09/03/2020
ms.topic: how-to
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- contperf-fy21q1
ms.openlocfilehash: 3bef3700cd6bdaf000f222736b63e8fda24e1602
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130226203"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>Konfigurieren eines IoT Edge-Geräts für die Kommunikation über einen Proxyserver

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

IoT Edge-Geräte senden HTTPS-Anforderungen, um mit IoT Hub zu kommunizieren. Wenn Ihr Gerät mit einem Netzwerk verbunden ist, das einen Proxyserver verwendet, müssen Sie die IoT Edge-Runtime für die Kommunikation über den Server konfigurieren. Proxyserver können auch einzelne IoT Edge-Module beeinflussen, wenn sie HTTP- oder HTTPS-Anforderungen senden, die nicht über den IoT Edge-Hub weitergeleitet werden.

Dieser Artikel geht die folgenden vier Schritte zum Konfigurieren und anschließenden Verwalten eines IoT Edge-Geräts durch, das sich hinter einem Proxyserver befindet:

1. [**Installieren Sie die IoT Edge-Runtime auf Ihrem Gerät.**](#install-iot-edge-through-a-proxy)

   Die IoT Edge-Installationsskripts pullen Pakete und Dateien aus dem Internet, weshalb Ihr Gerät über den Proxyserver kommunizieren können muss, um diese Anforderungen auszuführen. Windows-Geräten bietet das Installationsskript außerdem eine Option für die Offlineinstallation.

   Dieser Schritt ist ein einmaliger Prozess zum Konfigurieren des IoT Edge-Geräts, wenn Sie es zum ersten Mal einrichten. Dieselben Verbindungen sind ebenfalls erforderlich, wenn Sie IoT Edge-Runtime aktualisieren.

2. [**Konfigurieren von IoT Edge und der Containerruntime auf Ihrem Gerät**](#configure-iot-edge-and-moby)

   IoT Edge ist für die Kommunikation mit IoT Hub zuständig. Die Containerruntime ist für die Containerverwaltung zuständig, kommuniziert also mit Containerregistrierungen. Beide Komponenten müssen Webanforderungen über den Proxyserver senden.

   Dieser Schritt ist ein einmaliger Prozess zum Konfigurieren des IoT Edge-Geräts, wenn Sie es zum ersten Mal einrichten.

3. [**Konfigurieren der IoT Edge-Agenteigenschaften in der Konfigurationsdatei auf Ihrem Gerät**](#configure-the-iot-edge-agent)

   Der IoT Edge-Daemon startet zunächst das edgeAgent-Modul. Dann ruft das edgeAgent-Modul das Bereitstellungsmanifest aus IoT Hub ab und startet alle anderen Module. Damit der IoT Edge-Agent die anfängliche Verbindung mit dem IoT Hub herstellen kann, konfigurieren Sie die Umgebungsvariablen des edgeAgent-Moduls manuell auf dem Gerät selbst. Nach der ersten Verbindung können Sie das edgeAgent-Modul remote konfigurieren.

   Dieser Schritt ist ein einmaliger Prozess zum Konfigurieren des IoT Edge-Geräts, wenn Sie es zum ersten Mal einrichten.

4. [**Legen Sie für alle zukünftigen Modulbereitstellungen Umgebungsvariablen für jedes Modul fest, das über den Proxy kommuniziert.**](#configure-deployment-manifests)

   Nachdem Ihr IoT Edge-Gerät eingerichtet und durch den Proxyserver mit IoT Hub verbunden ist, müssen Sie die Verbindung in allen zukünftigen Modulbereitstellungen beibehalten.

   Dieser Schritt ist ein fortlaufender Prozess, der remote ausgeführt wird, sodass jedes neue Modul oder Bereitstellungsupdate die Fähigkeit des Geräts beibehält, über den Proxyserver zu kommunizieren.

## <a name="know-your-proxy-url"></a>Ermitteln der Webproxy-URL

Bevor Sie mit einem der Schritte in diesem Artikel beginnen, müssen Sie Ihre Proxy-URL kennen.

Proxy-URLs haben das folgende Format: **Protokoll**://**Proxyhost**:**Proxyport**.

* Das **Protokoll** ist entweder HTTP oder HTTPS. Der Docker-Daemon kann abhängig von den Einstellungen Ihrer Containerregistrierung beide Protokolle verwenden, aber für den IoT Edge-Daemon und die Runtimecontainer sollte für Verbindungen mit dem Proxy immer HTTP verwendet werden.

* Der **Proxyhost** ist eine Adresse für den Proxyserver. Wenn Ihr Proxyserver eine Authentifizierung erfordert, können Sie Ihre Anmeldeinformationen als Teil von „Proxyhost“ mit dem folgenden Format eingeben: **Benutzer**:**Kennwort**\@**Proxyhost**.

* Der **Proxyport** ist der Netzwerkport, an dem der Proxy auf Netzwerkdatenverkehr antwortet.

## <a name="install-iot-edge-through-a-proxy"></a>Installieren von IoT Edge über einen Proxy

Ob Ihr IoT Edge-Gerät unter Windows oder Linux ausgeführt wird, Sie müssen über den Proxyserver auf die Installationspakete zugreifen. Befolgen Sie in Abhängigkeit von Ihrem Betriebssystem die Schritte zum Installieren der IoT Edge-Runtime durch einen Proxyserver.

### <a name="linux-devices"></a>Linux-Geräte

Wenn Sie IoT Edge-Runtime auf einem Linux-Gerät installieren, konfigurieren Sie den Paket-Manager so, dass er über den Proxyserver auf das Installationspaket zugreift. Beispiel: [Richten Sie „apt-get“ zur Verwendung eines http-Proxys ein](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy). Nachdem Sie Ihren Paket-Manager konfiguriert haben, befolgen Sie die Anweisungen unter [Installieren oder Deinstallieren von Azure IoT Edge-Runtime](how-to-provision-single-device-linux-symmetric.md) wie gewohnt.

### <a name="windows-devices-using-iot-edge-for-linux-on-windows"></a>Windows-Geräte mit IoT Edge für Linux unter Windows

Wenn Sie die IoT Edge Runtime mit IoT Edge für Linux unter Windows installieren, wird IoT Edge auf Ihrem virtuellen Linux-Computer standardmäßig installiert. Es sind keine zusätzlichen Installations- oder Aktualisierungsschritte erforderlich.

### <a name="windows-devices-using-windows-containers"></a>Windows-Geräte mit Windows-Containern

Wenn Sie IoT Edge-Runtime auf einem Windows-Gerät installieren, müssen Sie den Proxyserver zwei Mal durchlaufen. Mit der ersten Verbindung wird die Installationsskriptdatei heruntergeladen, und die zweite Verbindung erfolgt während der Installation, um die erforderlichen Komponenten herunterzuladen. Sie können Proxyinformationen in den Windows-Einstellungen konfigurieren oder Ihre Proxyinformationen direkt in die PowerShell-Befehle aufnehmen.

Die folgenden Schritte zeigen ein Beispiel für eine Windows-Installation mithilfe des `-proxy`-Arguments:

1. Der Befehl Invoke-WebRequest benötigt Proxyinformationen, um auf das Installationsprogrammskript zuzugreifen. Anschließend benötigt der Befehl Deploy-IoTEdge die Proxyinformationen, um die Installationsdateien herunterzuladen.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. Der Befehl Initialize-IoTEdge muss nicht über den Proxyserver gehen, daher benötigt der zweite Schritt nur Proxyinformationen für Invoke-WebRequest.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge
   ```

Wenn Sie komplexe Anmeldeinformationen für den Proxyserver verwenden, die nicht in die URL eingeschlossen werden können, verwenden Sie den Parameter `-ProxyCredential` innerhalb von `-InvokeWebRequestParameters`. Beispiel:

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

Weitere Informationen zu Proxyparametern finden Sie unter [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest). Weitere Informationen zu Windows-Installationsparametern finden Sie unter [PowerShell-Skripts für IoT Edge unter Windows](reference-windows-scripts.md).

## <a name="configure-iot-edge-and-moby"></a>Konfigurieren von IoT Edge und Moby

IoT Edge basiert auf zwei Daemons, die auf dem IoT Edge-Gerät ausgeführt werden. Der Moby-Daemon ruft mit Webanforderungen Containerimages aus Containerregistrierungen ab. Der IoT Edge-Daemon kommuniziert über Webanforderungen mit IoT Hub.

Sowohl der Moby- als auch der IoT Edge-Daemon muss für die Verwendung des Proxyservers konfiguriert sein, damit das Gerät fortwährend funktionieren kann. Dieser Schritt erfolgt auf dem IoT Edge-Gerät während der anfänglichen Geräteeinrichtung.

### <a name="moby-daemon"></a>Moby-Daemon

Da Moby auf Docker basiert, finden Sie in der Docker-Dokumentation Informationen, wie der Moby-Daemon mit Umgebungsvariablen konfiguriert wird. Die meisten Containerregistrierungen (einschließlich DockerHub und Azure Container Registry-Instanzen) unterstützen HTTPS-Anforderungen, daher sollten Sie den Parameter **HTTPS_PROXY** festlegen. Wenn Sie Images aus einer Registrierung abrufen, die Transport Layer Security (TLS) nicht unterstützt, sollten Sie den Parameter **HTTP_PROXY** festlegen.

Wählen Sie den Artikel aus, der für Ihr IoT Edge-Gerätebetriebssystem gilt:

* [Konfigurieren des Docker-Daemons unter Linux](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy) Der Moby-Daemon auf Linux-Geräten behält den Namen „Docker“.
* [Konfigurieren des Docker-Daemons unter Windows](/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration) Der Moby-Daemon auf Windows-Geräten wird als „iotedge-moby“ bezeichnet. Die Namen sind verschieden, da es möglich ist, Docker Desktop und Moby gleichzeitig auf einem Windows-Gerät auszuführen.

### <a name="iot-edge-daemon"></a>IoT Edge-Daemon

Der IoT Edge-Daemon wird in ähnlicher Weise wie der Moby-Daemon konfiguriert. Verwenden Sie je nach Betriebssystem die folgenden Schritte, um eine Umgebungsvariable für den Dienst festzulegen.

Der IoT Edge-Daemon verwendet immer HTTPS, um Anforderungen an IoT Hub zu senden.

#### <a name="linux"></a>Linux

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Öffnen Sie einen Editor im Terminal, um den IoT Edge-Daemon zu konfigurieren.

```bash
sudo systemctl edit iotedge
```

Geben Sie den folgenden Text ein, und ersetzen Sie dabei **\<proxy URL>** durch die Adresse und den Port Ihres Proxyservers. Wählen Sie dann „Speichern“ und „Beenden“ aus.

```ini
[Service]
Environment=https_proxy=<proxy URL>
```

Aktualisieren Sie den Dienst-Manager, um die neue Konfiguration für IoT Edge zu übernehmen.

```bash
sudo systemctl daemon-reload
```

Starten Sie IoT Edge neu, damit die Änderungen wirksam werden.

```bash
sudo systemctl restart iotedge
```

Stellen Sie sicher, dass die Umgebungsvariable erstellt wurde und die neue Konfiguration geladen wurde.

```bash
systemctl show --property=Environment iotedge
```
:::moniker-end
<!--end 1.1-->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Öffnen Sie einen Editor im Terminal, um den IoT Edge-Daemon zu konfigurieren.

```bash
sudo systemctl edit aziot-edged
```

Geben Sie den folgenden Text ein, und ersetzen Sie dabei **\<proxy URL>** durch die Adresse und den Port Ihres Proxyservers. Wählen Sie dann „Speichern“ und „Beenden“ aus.

```ini
[Service]
Environment=https_proxy=<proxy URL>
```

Ab Version 1.2 verwendet IoT Edge den IoT-Identitätsdienst zur Durchführung der Gerätebereitstellung mit IoT Hub oder dem IoT Hub-Gerätebereitstellungsdienst (IoT Hub Device Provisioning Service). Öffnen Sie einen Editor im Terminal, um den IoT-Daemon für den Identitätsdienst-zu konfigurieren.

```bash
sudo systemctl edit aziot-identityd
```

Geben Sie den folgenden Text ein, und ersetzen Sie dabei **\<proxy URL>** durch die Adresse und den Port Ihres Proxyservers. Wählen Sie dann „Speichern“ und „Beenden“ aus.

```ini
[Service]
Environment=https_proxy=<proxy URL>
```

Aktualisieren Sie den Dienst-Manager, um die neuen Konfigurationen zu übernehmen.

```bash
sudo systemctl daemon-reload
```

Starten Sie die IoT Edge-Systemdienste neu, damit die Änderungen an beiden Daemons wirksam werden.

```bash
sudo iotedge system restart
```

Vergewissern Sie sich, dass Ihre Umgebungsvariablen erstellt wurden und die neue Konfiguration geladen wurde.

```bash
systemctl show --property=Environment aziot-edged
systemctl show --property=Environment aziot-identityd
```
:::moniker-end
<!--end 1.2-->

#### <a name="windows-using-iot-edge-for-linux-on-windows"></a>Windows mit IoT Edge für Linux unter Windows

Melden Sie sich auf dem virtuellen Windows-Computer bei IoT Edge für Linux an:

```powershell
Connect-EflowVm
```

Führen Sie dieselben Schritte wie im vorstehenden Linux-Abschnitt aus, um den IoT Edge Daemon zu konfigurieren.

#### <a name="windows-using-windows-containers"></a>Windows mit Windows-Containern

Öffnen Sie ein PowerShell-Fenster als Administrator, und führen Sie den folgenden Befehl zum Bearbeiten der Registrierung mit der neuen Umgebungsvariable aus. Ersetzen Sie **\<proxy url>** durch die Adresse und den Port Ihres Proxyservers.

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

Starten Sie IoT Edge neu, damit die Änderungen wirksam werden.

```powershell
Restart-Service iotedge
```

## <a name="configure-the-iot-edge-agent"></a>Konfigurieren des IoT Edge-Agents

Der IoT Edge-Agent ist das erste Modul, das auf einem IoT Edge-Gerät gestartet wird. Es wird zum ersten Mal basierend auf den Informationen in der IoT Edge-Konfigurationsdatei gestartet. Der IoT Edge-Agent stellt anschließend eine Verbindung mit IoT Hub her, um Bereitstellungsmanifeste abzurufen, die deklarieren, welche weiteren Module auf dem Gerät bereitgestellt werden sollen.

Dieser Schritt erfolgt einmalig auf dem IoT Edge-Gerät während der anfänglichen Geräteeinrichtung.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Öffnen Sie die Datei „config.yaml“ auf Ihrem IoT Edge-Gerät. In Linux-Systemen befindet sich diese Datei unter **/etc/iotedge/config.yaml**. In Windows-Systemen befindet sich diese Datei unter **C:\ProgramData\iotedge\config.yaml**. Die Konfigurationsdatei ist geschützt. Sie benötigen also Administratorrechte, um darauf zuzugreifen. Auf Linux-Systemen verwenden Sie den Befehl `sudo`, bevor Sie die Datei in Ihrem bevorzugten Text-Editor öffnen. Unter Windows öffnen Sie einen Text-Editor wie Editor als Administrator und öffnen dann die Datei.

2. Suchen Sie in der Datei „config.yaml“ nach dem Abschnitt **Edge Agent module spec**. Die IoT Edge-Agent-Definition enthält einen **env**-Parameter, dem Sie Umgebungsvariablen hinzufügen können.

3. Entfernen Sie die geschweiften Klammern, die Platzhalter für den env-Parameter darstellen, und fügen Sie die neue Variable in einer neuen Zeile hinzu. Denken Sie daran, dass Einzüge in YAML zwei Leerzeichen sind.

   ```yaml
   https_proxy: "<proxy URL>"
   ```

4. Die IoT Edge-Runtime verwendet standardmäßig AMQP für die Kommunikation mit IoT Hub. Einige Proxyserver blockieren AMQP-Ports. Wenn dies der Fall ist, müssen Sie „edgeAgent“ auch so konfigurieren, dass AMQP über WebSocket verwendet wird. Fügen Sie eine zweite Umgebungsvariable hinzu.

   ```yaml
   UpstreamProtocol: "AmqpWs"
   ```

   ![edgeAgent-Definition mit Umgebungsvariablen](./media/how-to-configure-proxy-support/edgeagent-edited.png)

5. Speichern Sie die Änderungen an „config.yaml“, und schließen Sie den Editor. Starten Sie IoT Edge neu, damit die Änderungen wirksam werden.

   * Linux und IoT Edge für Linux unter Windows:

      ```bash
      sudo systemctl restart iotedge
      ```

   * Windows mit Windows-Containern:

      ```powershell
      Restart-Service iotedge
      ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Öffnen Sie auf Ihrem IoT Edge-Gerät die Konfigurationsdatei `/etc/aziot/config.toml`. Die Konfigurationsdatei ist geschützt. Sie benötigen also Administratorrechte, um darauf zuzugreifen. Auf Linux-Systemen verwenden Sie den Befehl `sudo`, bevor Sie die Datei in Ihrem bevorzugten Text-Editor öffnen.

2. Suchen Sie in der Konfigurationsdatei den Abschnitt `[agent]` mit allen Konfigurationsinformationen für das edgeAgent-Modul, das beim Start verwendet werden soll. Die IoT Edge-Agentdefinition enthält einen `[agent.env]`-Unterabschnitt, in dem Sie Umgebungsvariablen hinzufügen können.

3. Fügen Sie im Abschnitt mit den Umgebungsvariablen den Parameter **https_proxy** hinzu, und legen Sie Ihre Proxy-URL als Wert fest.

   ```toml
   [agent.env]
   # "RuntimeLogLevel" = "debug"
   # "UpstreamProtocol" = "AmqpWs"
   "https_proxy" = "<proxy URL>"
   ```

4. Die IoT Edge-Runtime verwendet standardmäßig AMQP für die Kommunikation mit IoT Hub. Einige Proxyserver blockieren AMQP-Ports. Wenn dies der Fall ist, müssen Sie „edgeAgent“ auch so konfigurieren, dass AMQP über WebSocket verwendet wird. Heben Sie die Auskommentierung des Parameters `UpstreamProtocol` auf.

   ```toml
   [agent.env]
   # "RuntimeLogLevel" = "debug"
   "UpstreamProtocol" = "AmqpWs"
   "https_proxy" = "<proxy URL>"
   ```

5. Speichern Sie die Änderungen, und schließen Sie den Editor. Wenden Sie Ihre neuesten Änderungen an.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

## <a name="configure-deployment-manifests"></a>Konfigurieren von Bereitstellungsmanifesten  

Nachdem Ihr IoT Edge-Gerät für die Verwendung Ihres Proxyservers konfiguriert wurde, müssen Sie die Umgebungsvariable „HTTPS_PROXY“ auch in allen zukünftigen Bereitstellungsmanifesten deklarieren. Sie können Bereitstellungsmanifeste entweder mit dem Azure-Portal-Assistenten bearbeiten, oder indem Sie eine JSON-Bereitstellungsmanifestdatei bearbeiten.

Konfigurieren Sie die beiden Runtimemodule („edgeAgent“ und „edgeHub“) immer so, dass sie über den Proxyserver kommunizieren, damit sie eine Verbindung mit IoT Hub aufrechterhalten können. Wenn Sie die Proxyinformationen aus dem edgeAgent-Modul entfernen, können Sie die Verbindung nur durch Bearbeiten der Konfigurationsdatei auf dem Gerät wiederherstellen, wie im vorhergehenden Abschnitt beschrieben wird.

Zusätzlich zu den Modulen „edgeAgent“ und „edgeHub“ benötigen andere Module möglicherweise die Proxykonfiguration. Für Module, die neben dem IoT-Hub Zugriff auf Azure-Ressourcen wie Blob Storage erfordern, muss in der Bereitstellungsmanifestdatei die HTTPS_PROXY-Variable angegeben sein.

Das folgende Verfahren gilt für die gesamte Lebensdauer des IoT Edge-Geräts.

### <a name="azure-portal"></a>Azure-Portal

Wenn Sie den Assistenten **Module festlegen** zum Erstellen von Bereitstellungen für IoT Edge-Geräte verwenden, verfügt jedes Modul über einen Abschnitt **Umgebungsvariablen**, in dem Sie Proxyserververbindungen konfigurieren können.

Wählen Sie zum Konfigurieren der IoT Edge-Agent- und der IoT Edge-Hubmodule **Laufzeiteinstellungen** im ersten Schritt des Assistenten aus.

![Erweiterte Einstellungen für die Edge-Laufzeit konfigurieren](./media/how-to-configure-proxy-support/configure-runtime.png)

Fügen Sie die Umgebungsvariable **https_proxy** den Definitionen der IoT Edge-Agent- und IoT Edge-Hub-Module hinzu. Wenn Sie die Umgebungsvariable **UpstreamProtocol** in die Konfigurationsdatei auf Ihrem IoT Edge-Gerät eingefügt haben, fügen Sie sie auch der Definition des IoT Edge-Agentmoduls hinzu.

![Festlegen der https_proxy-Umgebungsvariable](./media/how-to-configure-proxy-support/edgehub-environmentvar.png)

Alle anderen Module, die Sie einem Bereitstellungsmanifest hinzufügen, folgen dem gleichen Muster.

### <a name="json-deployment-manifest-files"></a>JSON-Bereitstellungsmanifestdateien

Wenn Sie Bereitstellungen für IoT Edge-Geräte mithilfe von Vorlagen in Visual Studio Code oder durch das manuelle Erstellen von JSON-Dateien erstellen, können Sie die Umgebungsvariablen direkt jeder Moduldefinition hinzufügen.

Verwenden Sie das folgende JSON-Format:

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    }
}
```

Mit den Umgebungsvariablen sollte Ihre Moduldefinition wie das folgende edgeHub-Beispiel aussehen:

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
        "createOptions": "{}"
    },
    "env": {
        "https_proxy": {
            "value": "http://proxy.example.com:3128"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

Wenn Sie die Umgebungsvariable **UpstreamProtocol** in die Datei „confige.yaml“ auf Ihrem IoT Edge-Gerät eingefügt haben, fügen Sie sie auch der IoT Edge-Agent-Moduldefinition hinzu.

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    },
    "UpstreamProtocol": {
        "value": "AmqpWs"
    }
}
```

## <a name="working-with-traffic-inspecting-proxies"></a>Arbeiten mit Proxys für Datenverkehrsüberprüfung

Wenn der Proxy, den Sie verwenden möchten, eine Datenverkehrsüberprüfung für TLS-gesicherte Verbindungen durchführt, ist es wichtig zu beachten, dass die Authentifizierung mit X.509-Zertifikaten nicht funktioniert. IoT Edge richtet einen End-to-End-verschlüsselten TLS-Kanal mit dem angegebenen Zertifikat und Schlüssel ein. Wenn dieser Kanal wegen einer Datenverkehrsüberprüfung gestört ist, kann ihn der Proxy mit den richtigen Anmeldeinformationen nicht wiederherstellen, und IoT Hub und der IoT Hub-Gerätebereitstellungsdienst (Device Provisioning Service) geben einen `Unauthorized`-Fehler zurück.

Wenn Sie einen Proxy verwenden möchten, der eine Datenverkehrsüberprüfung durchführt, müssen Sie entweder die SAS-Authentifizierung (Shared Access Signature) verwenden oder IoT Hub und den IoT Hub-Gerätebereitstellungsdienst einer Positivliste hinzugefügt haben, um die Überprüfung zu vermeiden.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die Rollen der [IoT Edge-Runtime](iot-edge-runtime.md).

Behandeln Sie Installations- und Konfigurationsfehler mit [Häufig auftretende Probleme und Lösungen für Azure IoT Edge](troubleshoot.md).
