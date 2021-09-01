---
title: 'Problembehandlung: Azure IoT Edge | Microsoft-Dokumentation'
description: In diesem Artikel werden grundlegende Diagnoseverfahren für Azure IoT Edge erläutert, beispielsweise das Abrufen des Komponentenstatus und von Protokollen.
author: kgremban
ms.author: kgremban
ms.date: 05/04/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3bca470114b5fb22409d86c333e92f93155759f6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339473"
---
# <a name="troubleshoot-your-iot-edge-device"></a>Behandeln von Problemen bei Ihrem IoT Edge-Gerät

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Wenn in Ihrer Umgebung Probleme bei der Ausführung von Azure IoT Edge auftreten, nutzen Sie diesen Artikel als Leitfaden zur Problembehandlung und Diagnose.

## <a name="run-the-check-command"></a>Ausführen des Befehls „check“

Ihr erster Schritt im Rahmen der Problembehandlung bei IoT Edge sollte die Verwendung des Befehls `check` sein, mit dem eine Reihe von Konfigurations- und Konnektivitätstests zur Behandlung allgemeiner Probleme durchgeführt wird. Der Befehl `check` ist ab [Release 1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) verfügbar.

>[!NOTE]
>Das Problembehandlungstool kann keine Konnektivitätsprüfungen durchführen, wenn sich das IoT Edge-Gerät hinter einem Proxyserver befindet.

Sie können den Befehl `check` wie folgt ausführen oder das Flag `--help` einbinden, um alle Optionen anzuzeigen:

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Unter Linux:

```bash
sudo iotedge check
```

Unter Windows:

```powershell
iotedge check
```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.1 -->
:::moniker range=">=iotedge-2020-11"

```bash
sudo iotedge check
```

:::moniker-end
<!-- end 1.2 -->

Das Problembehandlungstool führt viele Überprüfungen durch, die in folgende drei Kategorien eingeteilt werden:

* *Konfigurationsüberprüfungen* – Untersucht Details, die verhindern könnten, dass IoT Edge-Geräte eine Verbindung mit der Cloud herstellen, einschließlich Problemen bei der Konfigurationsdatei und der Container-Engine.
* *Verbindungsüberprüfungen* – Überprüft, ob IoT Edge Runtime Zugriff auf Ports am Hostgerät hat und ob alle IoT Edge-Komponenten eine Verbindung mit dem IoT Hub herstellen können. Diese Gruppe von Überprüfungen gibt Fehler zurück, wenn sich das IoT Edge-Gerät hinter einem Proxy befindet.
* *Überprüfungen auf die Produktionsbereitschaft* – Sucht nach empfohlenen Best Practices für die Produktion, z. B. nach Zertifikaten der Zertifizierungsstelle (Certificate Authority, CA) für den Gerätestatus und nach der Konfiguration der Modulprotokolldatei.

Das Prüftool für IoT Edge verwendet einen Container, um die Diagnosen durchzuführen. Das Containerimage (`mcr.microsoft.com/azureiotedge-diagnostics:latest`) steht über [Microsoft Container Registry](https://github.com/microsoft/containerregistry) zur Verfügung. Wenn Sie eine Überprüfung auf einem Gerät ohne Direktzugriff auf das Internet ausführen möchten, benötigen die Geräte Zugriff auf das Containerimage.

<!-- <1.2> -->
:::moniker range=">=iotedge-2020-11"

In einem Szenario mit geschachtelten IoT Edge Geräten können Sie Zugriff auf das Diagnoseimage auf untergeordneten Geräten erhalten, indem Sie die Imageübertragung über die übergeordneten Geräte weiterleiten.

```bash
sudo iotedge check --diagnostics-image-name <parent_device_fqdn_or_ip>:<port_for_api_proxy_module>/azureiotedge-diagnostics:1.2
```

<!-- </1.2> -->
:::moniker-end

Informationen zu den einzelnen Diagnoseprüfungen, die von diesem Tool durchgeführt werden – einschließlich der Vorgehensweise beim Auftreten eines Fehlers oder einer Warnung – finden Sie unter [IoT Edge-Problembehandlung bei Überprüfungen](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md).

## <a name="gather-debug-information-with-support-bundle-command"></a>Sammeln von Debuginformationen mit dem Befehl „support-bundle“

Wenn Sie Protokolle von einem IoT Edge-Gerät sammeln müssen, verwenden Sie dazu am einfachsten den Befehl `support-bundle`. Dieser Befehl sammelt standardmäßig Protokolle von Modulen, IoT Edge Security Manager und Container-Engine, die JSON-Ausgabe `iotedge check` und weitere nützliche Debuginformationen. Er komprimiert sie zur einfachen Freigabe in einer einzigen Datei. Der Befehl `support-bundle` steht ab [Release 1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) zur Verfügung.

Führen Sie den Befehl `support-bundle` mit dem Flag `--since` aus, um anzugeben, wie alt die Protokolle sein sollen, die Sie abrufen möchten. So werden beispielsweise mit `6h` die Protokolle der letzten sechs Stunden, mit `6d` die der letzten Tage und mit `6m` die der letzten sechs Minuten usw. abgerufen. Beziehen Sie das Flag `--help` mit ein, damit eine vollständige Liste der Optionen angezeigt wird.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Unter Linux:

```bash
sudo iotedge support-bundle --since 6h
```

Unter Windows:

```powershell
iotedge support-bundle --since 6h
```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

```bash
sudo iotedge support-bundle --since 6h
```

:::moniker-end
<!-- end 1.2 -->

Standardmäßig erstellt der Befehl `support-bundle` in dem Verzeichnis, in dem er aufgerufen wird, die ZIP-Datei **support_bundle.zip**. Wenn Sie einen anderen Pfad oder Dateinamen für die Ausgabe angeben möchten, verwenden Sie das Flag `--output`.

Weitere Informationen zum Befehl können Sie in den zugehörigen Hilfeinformationen anzeigen.

```bash/cmd
iotedge support-bundle --help
```

Sie können auch die Ausgabe des Befehls „support-bundle“ mithilfe des integrierten direkten Methodenaufrufs [UploadSupportBundle](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics) in Azure Blob Storage hochladen.

> [!WARNING]
> Die Ausgabe des Befehls `support-bundle` kann Host-, Geräte- und Modulnamen, von ihren Modulen protokollierte Informationen usw. enthalten. Beachten Sie dies bitte, wenn Sie die Ausgabe in einem öffentlichen Forum freigeben.

## <a name="review-metrics-collected-from-the-runtime"></a>Überprüfen der von der Runtime gesammelten Metriken

Die IoT Edge-Runtimemodule erzeugen Metriken, mit deren Hilfe Sie die Integrität Ihrer IoT Edge-Geräte überwachen und verstehen können. Fügen Sie Ihren Bereitstellungen das Modul **Metrikensammler** hinzu, um diese Metriken zu erfassen und zur einfacheren Überwachung an die Cloud zu senden.

Weitere Informationen finden Sie unter [Sammeln und Transportieren von Metriken](how-to-collect-and-transport-metrics.md).

## <a name="check-your-iot-edge-version"></a>Überprüfen Ihrer IoT Edge-Version

Wenn Sie eine ältere Version von IoT Edge ausführen, löst ein Upgrade möglicherweise Ihr Problem. Das Tool `iotedge check` überprüft, ob der IoT Edge-Sicherheitsdaemon die neueste Version ist, überprüft aber nicht die Versionen der IoT Edge Hub- und Agentmodule. Wenn Sie die Version der Runtimemodule auf Ihrem Gerät überprüfen möchten, verwenden Sie die Befehle `iotedge logs edgeAgent` und `iotedge logs edgeHub`. Die Versionsnummer wird in den Protokollen beim Starten des Moduls deklariert.

Anleitungen zum Aktualisieren Ihres Geräts finden Sie unter [Aktualisieren des IoT Edge-Sicherheitsdaemons und der Runtime](how-to-update-iot-edge.md).

## <a name="verify-the-installation-of-iot-edge-on-your-devices"></a>Überprüfen der Installation von IoT Edge auf Ihren Geräten

Sie können die Installation von IoT Edge auf Ihren Geräten überprüfen, indem Sie [den edgeAgent-Modulzwilling überwachen](./how-to-monitor-module-twins.md).

Führen Sie den folgenden Befehl in [Azure Cloud Shell](https://shell.azure.com/) aus, um den neuesten edgeAgent-Modulzwilling abzurufen:

   ```azurecli-interactive
   az iot hub module-twin show --device-id <edge_device_id> --module-id '$edgeAgent' --hub-name <iot_hub_name>
   ```

Mit diesem Befehl werden alle [gemeldeten edgeAgent-Eigenschaften](./module-edgeagent-edgehub.md) ausgegeben. Hier einige nützliche Eigenschaften zum Überwachen des Gerätestatus:

* runtime status
* runtime start time
* runtime last exit time
* runtime restart count

## <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Überprüfen des Status von IoT Edge Security Manager und der zugehörigen Protokolle

[IoT Edge Security Manager](iot-edge-security-manager.md) ist für Vorgänge wie das Initialisieren des IoT Edge-Systems beim Starten und Bereitstellen von Geräten verantwortlich. Wenn IoT Edge nicht gestartet wird, können die Security Manager-Protokolle nützliche Informationen enthalten.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Unter Linux:

* Zeigen Sie den Status von IoT Edge Security Manager so an:

   ```bash
   sudo systemctl status iotedge
   ```

* Zeigen Sie die Protokolle von IoT Edge Security Manager so an:

   ```bash
   sudo journalctl -u iotedge -f
   ```

* Zeigen Sie ausführlichere Protokolle von IoT Edge Security Manager so an:

  1. Bearbeiten Sie so die Einstellungen für IoT Edge-Daemon:

     ```bash
     sudo systemctl edit iotedge.service
     ```

  2. Aktualisieren Sie die folgenden Zeilen:

     ```bash
     [Service]
     Environment=IOTEDGE_LOG=debug
     ```

  3. Starten Sie den Daemon für Azure IoT Edge-Sicherheit neu:

     ```bash
     sudo systemctl cat iotedge.service
     sudo systemctl daemon-reload
     sudo systemctl restart iotedge
     ```

Unter Windows:

* Zeigen Sie den Status von IoT Edge Security Manager so an:

   ```powershell
   Get-Service iotedge
   ```

* Zeigen Sie die Protokolle von IoT Edge Security Manager so an:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

* Zeigen Sie nur die letzten 5 Minuten der IoT Edge Security Manager-Protokolle so an:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog -StartTime ([datetime]::Now.AddMinutes(-5))
   ```

* Zeigen Sie ausführlichere Protokolle von IoT Edge Security Manager so an:

  1. Fügen Sie eine Umgebungsvariable auf Systemebene hinzu:

     ```powershell
     [Environment]::SetEnvironmentVariable("IOTEDGE_LOG", "debug", [EnvironmentVariableTarget]::Machine)
     ```

  2. Starten Sie den Daemon für die IoT Edge-Sicherheit neu:

     ```powershell
     Restart-Service iotedge
     ```

:::moniker-end
<!--end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

* Zeigen Sie den Status der IoT Edge-Systemdienste an:

   ```bash
   sudo iotedge system status
   ```

* Zeigen Sie die Protokolle der IoT Edge-Systemdienste an:

   ```bash
   sudo iotedge system logs -- -f
   ```

* Aktivieren Sie Protokolle auf Debugebene, um ausführlichere Protokolle der IoT Edge-Systemdienste anzuzeigen:

  1. Aktivieren Sie Protokolle auf Debugebene.

     ```bash
     sudo iotedge system set-log-level debug
     sudo iotedge system restart
     ```

  1. Wechseln Sie nach dem Debuggen zu den Standardprotokollen auf Infoebene zurück.

     ```bash
     sudo iotedge system set-log-level info
     sudo iotedge system restart
     ```

:::moniker-end
<!-- end 1.2 -->

## <a name="check-container-logs-for-issues"></a>Überprüfen von Containerprotokollen auf Probleme

Sobald der IoT Edge-Sicherheits-Daemon ausgeführt wird, sehen Sie sich die Protokolle der Container an, um Probleme zu erkennen. Beginnen Sie mit den bereitgestellten Containern, und sehen Sie sich dann die Container der IoT Edge-Runtime an: „edgeAgent“ und „edgeHub“. Die IoT Edge-Agent-Protokolle bieten in der Regel Informationen zum Lebenszyklus der einzelnen Container. Die IoT Edge-Hub-Protokolle bieten Informationen zu Messaging und Routing.

Sie können die Containerprotokolle von mehreren Stellen abrufen:

* Führen Sie auf dem IoT Edge-Gerät den folgenden Befehl zum Anzeigen von Protokollen aus:

  ```cmd
  iotedge logs <container name>
  ```

* Verwenden Sie im Azure-Portal das integrierte Problembehandlungstool. [Überwachen und Beheben von Problemen bei IoT Edge-Geräten über das Azure-Portal](troubleshoot-in-portal.md)

* Sie können die Protokolle eines Moduls in Azure Blob Storage mithilfe der [direkten Methode „UploadModuleLogs“](how-to-retrieve-iot-edge-logs.md#upload-module-logs) in Azure Blob Storage hochladen.

## <a name="clean-up-container-logs"></a>Bereinigen von Containerprotokollen

Standardmäßig legt die Moby-Containerengine keine Grenzwerte für die Größe des Containerprotokolls fest. Im Laufe der Zeit kann dies dazu führen, dass das Gerät mit Protokollen überfüllt wird und nicht genügend Speicherplatz auf dem Datenträger zur Verfügung steht. Wenn sich große Containerprotokolle auf die Leistung Ihres IoT Edge-Geräts auswirken, können Sie mit dem folgenden Befehl das Entfernen des Containers zusammen mit den zugehörigen Protokollen erzwingen.

Wenn Sie noch die Problembehandlung durchführen, warten Sie, bis Sie die Containerprotokolle überprüft haben, bevor Sie diesen Schritt ausführen.

>[!WARNING]
>Wenn Sie erzwingen, dass der edgeHub-Container entfernt wird, während er ein nicht zugestelltes Nachrichtenbacklog enthält und kein [Hostspeicher](how-to-access-host-storage-from-module.md) eingerichtet wurde, gehen die nicht zugestellten Nachrichten verloren.

```cmd
docker rm --force <container name>
```

Deshalb sollten Sie bei laufenden Protokollwartungs- und Produktionsszenarien [Grenzwerte für die Protokollgröße festlegen](production-checklist.md#place-limits-on-log-size).

## <a name="view-the-messages-going-through-the-iot-edge-hub"></a>Anzeigen der Nachrichten, die den IoT Edge-Hub durchlaufen

<!--1.1 -->
:::moniker range="iotedge-2018-06"

Sie können die Nachrichten anzeigen, die den IoT Edge-Hub durchlaufen, und Erkenntnisse aus den ausführlichen Protokollen der Runtimecontainer gewinnen. Legen Sie die `RuntimeLogLevel` in Ihrer YAML-Konfigurationsdartei fest, um für diese Container ausführliche Protokolle zu aktivieren. So öffnen Sie die Datei

Unter Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Unter Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

Das Element `agent` sieht standardmäßig wie im folgenden Beispiel aus:

   ```yaml
   agent:
     name: edgeAgent
     type: docker
     env: {}
     config:
       image: mcr.microsoft.com/azureiotedge-agent:1.1
       auth: {}
   ```

Ersetzen Sie `env: {}` durch:

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

   > [!WARNING]
   > YAML-Dateien können keine Tabstopps für Einzüge enthalten. Verwenden Sie stattdessen zwei Leerzeichen. Elemente der obersten Ebene dürfen keine führenden Leerzeichen enthalten.

Speichern Sie die Datei, und starten Sie den IoT Edge-Sicherheits-Manager neu.

<!-- end 1.1 -->
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Sie können die Nachrichten anzeigen, die den IoT Edge-Hub durchlaufen, und aus den ausführlichen Protokollen der Runtimecontainer Erkenntnisse gewinnen. Wenn Sie für diese Container ausführliche Protokolle aktivieren möchten, legen Sie im Bereitstellungsmanifest die Umgebungsvariable `RuntimeLogLevel` fest.

Zum Anzeigen von Nachrichten, die den IoT Edge-Hub durchlaufen, legen Sie die `RuntimeLogLevel` Umgebungsvariable `debug` für das edgeHub-Modul fest.

Die Module edgeHub und edgeAgent enthalten diese Laufzeitprotokoll-Umgebungsvariable, bei der der Standardwert auf `info` festgelegt wurde. Diese Umgebungsvariable kann die folgenden Werte haben:

* fatal
* error
* warning
* info
* debug
* Ausführlich

<!-- end 1.2 -->
:::moniker-end

Sie können auch die Nachrichten überprüfen, die zwischen IoT Hub und den IoT-Geräten gesendet werden. Zeigen Sie diese Nachrichten mithilfe der [Azure IoT Hub-Erweiterung für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) an. Weitere Informationen finden Sie unter [Praktisches Tool bei der Entwicklung mit Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/).

## <a name="restart-containers"></a>Neustarten von Containern

Nachdem Sie die Protokolle und Nachrichten auf Informationen untersucht haben, können Sie einen Neustart der Container versuchen.

Verwenden Sie auf dem IoT Edge-Gerät die folgenden Befehle zum Neustart von Modulen:

```cmd
iotedge restart <container name>
```

Starten Sie die IoT Edge-Runtime-Container neu:

```cmd
iotedge restart edgeAgent && iotedge restart edgeHub
```

Sie können Module auch remote über das Azure-Portal neu starten. Weitere Informationen finden Sie unter [Überwachen und Beheben von Problemen bei IoT Edge-Geräten über das Azure-Portal](troubleshoot-in-portal.md).

## <a name="check-your-firewall-and-port-configuration-rules"></a>Überprüfen der Konfigurationsregeln für Firewall und Port

Azure IoT Edge ermöglicht die Kommunikation eines lokalen Servers mit der Azure-Cloud über unterstützte IoT Hub-Protokolle (siehe [Referenz – Auswählen eines Kommunikationsprotokolls](../iot-hub/iot-hub-devguide-protocols.md)). Aus Sicherheitsgründen sind Kommunikationskanäle zwischen Azure IoT Edge und Azure IoT Hub immer als „Ausgehend“ konfiguriert. Diese Konfiguration basiert auf dem [von Diensten unterstützten Kommunikationsmuster](/archive/blogs/clemensv/service-assisted-communication-for-connected-devices), das die Angriffsfläche für böswillige Entitäten minimiert, die das System durchsuchen. Die eingehende Kommunikation ist nur für bestimmte Szenarien erforderlich, in denen Azure IoT Hub Nachrichten mithilfe von Push an das Azure IoT Edge-Gerät übertragen muss. Cloud-zu-Gerät-Nachrichten werden mithilfe von sicheren TLS-Kanälen geschützt und können mithilfe von X. 509-Zertifikaten und TPM-Gerätemodulen weiter gesichert werden. Der Azure IoT Edge-Sicherheits-Manager steuert, wie diese Kommunikation zustande kommt (siehe [Azure IoT Edge-Sicherheits-Manager](../iot-edge/iot-edge-security-manager.md)).

IoT Edge bietet zwar erweiterte Konfigurationsmöglichkeiten zum Schutz der Azure IoT Edge-Runtime und der bereitgestellten Module, hängt aber dennoch von der zugrunde liegenden Computer- und Netzwerkkonfiguration ab. Für eine sichere Kommunikation zwischen Edge und Cloud müssen daher unbedingt geeignete Netzwerk- und Firewallregeln vorhanden sein. Beim Konfigurieren von Firewallregeln für die zugrunde liegenden Server, auf denen die Azure IoT Edge-Runtime gehostet wird, können Sie sich an folgender Tabelle orientieren:

|Protocol|Port|Eingehend|Ausgehend|Anleitungen|
|--|--|--|--|--|
|MQTT|8883|BLOCKIERT (Standard)|BLOCKIERT (Standard)|<ul> <li>Konfigurieren Sie eingehende Verbindungen als „Offen“, wenn Sie MQTT als Kommunikationsprotokoll verwenden.<li>1883 für MQTT wird von IoT Edge nicht unterstützt. <li>Eingehende Verbindungen sollten blockiert werden.</ul>|
|AMQP|5671|BLOCKIERT (Standard)|OFFEN (Standard)|<ul> <li>Standardkommunikationsprotokoll für IoT Edge. <li> Muss als „Offen“ konfiguriert werden, wenn Azure IoT Edge nicht für andere unterstützte Protokolle konfiguriert oder AMQP das gewünschte Kommunikationsprotokoll ist.<li>5672 für AMQP wird von IoT Edge nicht unterstützt.<li>Blockieren Sie diesen Port, wenn Azure IoT Edge ein anderes von IoT Hub unterstütztes Protokoll verwendet.<li>Eingehende Verbindungen sollten blockiert werden.</ul></ul>|
|HTTPS|443|BLOCKIERT (Standard)|OFFEN (Standard)|<ul> <li>Konfigurieren Sie ausgehende Verbindungen als „Offen“ (Port 443) für die IoT Edge-Bereitstellung. Diese Konfiguration ist bei Verwendung manueller Skripts oder des Azure IoT Device Provisioning-Diensts (Device Provisioning Service, DPS) erforderlich. <li>Die eingehende Verbindung sollte nur für bestimmte Szenarien geöffnet werden: <ul> <li>  Wenn Sie über ein transparentes Gateway mit Blattknotengeräten verfügen, die ggf. Methodenanforderungen senden. In diesem Fall muss der Port 443 nicht für externe Netzwerke geöffnet werden, um eine Verbindung mit IoT Hub herzustellen oder IoT Hub-Dienste über Azure IoT Edge bereitzustellen. Die eingehende Regel kann somit darauf beschränkt werden, nur eingehende Verbindungen aus dem internen Netzwerk zu öffnen. <li> In C2D-Szenarien.</ul><li>80 für HTTP wird von IoT Edge nicht unterstützt.<li>Falls im Unternehmen keine HTTP-fremden Protokolle (z. B. AMQP oder MQTT) konfiguriert werden können, haben Sie die Möglichkeit, die Nachrichten über WebSockets zu senden. In diesem Fall wird der Port 443 für die WebSocket-Kommunikation verwendet.</ul>|

## <a name="next-steps"></a>Nächste Schritte

Sind Sie der Meinung, dass Sie in der IoT Edge-Plattform einen Fehler gefunden haben? [Melden Sie ein Problem](https://github.com/Azure/iotedge/issues), damit wir die Plattform weiter verbessern können.

Wenn Sie weitere Fragen haben, erstellen Sie eine [Supportanfrage](https://portal.azure.com/#create/Microsoft.Support), um Hilfe zu erhalten.
