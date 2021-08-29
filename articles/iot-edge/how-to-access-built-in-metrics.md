---
title: 'Zugreifen auf integrierte Metriken: Azure IoT Edge'
description: Remotezugriff auf integrierte Metriken aus den IoT Edge-Runtimekomponenten
author: v-tcassi
ms.author: v-tcassi
ms.date: 06/25/2021
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ee4c39b7dfc4097480588620465eedd40eba53f6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346933"
---
# <a name="access-built-in-metrics"></a>Zugreifen auf integrierte Metriken

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Die IoT Edge-Runtimekomponenten, IoT Edge Hub und IoT Edge Agent, erzeugen integrierte Metriken im [Prometheus-Offenlegungsformat](https://prometheus.io/docs/instrumenting/exposition_formats/). Fügen Sie diese Metriken remote hinzu, um die Integrität eines IoT Edge-Geräts zu überwachen und zu verstehen.

Sie können mit Ihrer eigenen Lösung auf diese Metriken zugreifen. Oder Sie können das [Metrikensammler-Modul](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.metrics-collector) verwenden, das die integrierten Metriken sammelt und an Azure Monitor oder Azure IoT Hub sendet. Weitere Informationen finden Sie unter [Sammeln und Transportieren von Metriken](how-to-collect-and-transport-metrics.md).

Ab Version 1.0.10 werden Metriken automatisch standardmäßig an **Port 9600** des **edgeHub**- und des **edgeAgent**-Moduls verfügbar gemacht (`http://edgeHub:9600/metrics` und `http://edgeAgent:9600/metrics`). Standardmäßig sind die Ports auf dem Host nicht zugeordnet.

Sie können vom Host aus auf die Metriken zugreifen, indem Sie den Metrikport in den `createOptions` des Moduls verfügbar machen und zuordnen. Im Beispiel unten wird der Standardmetrikport dem Port 9601 auf dem Host zugeordnet:

```json
{
  "ExposedPorts": {
    "9600/tcp": {}
  },
  "HostConfig": {
    "PortBindings": {
      "9600/tcp": [
        {
          "HostPort": "9601"
        }
      ]
    }
  }
}
```

Wählen Sie verschiedene und eindeutige Hostportnummern, wenn Sie die Metrikendpunkte von edgeHub ebenso wie die auch edgeAgent zuordnen.

> [!NOTE]
> Die Umgebungsvariable `httpSettings__enabled` sollte nicht auf `false` festgelegt werden, damit integrierte Metriken für die Sammlung verfügbar sind.
>
> Umgebungsvariablen, die zum Deaktivieren von Metriken verwendet werden können, finden Sie in der Dokumentation zum Repository [azure/iotedge](https://github.com/Azure/iotedge/blob/master/doc/EnvironmentVariables.md).

## <a name="available-metrics"></a>Verfügbare Metriken

Metriken enthalten Tags, um die Art der erfassten Metrik besser erkennbar zu machen. Alle Metriken enthalten die folgenden Tags:

| Tag | BESCHREIBUNG |
|-|-|
| iothub | Der Hub, mit dem das Gerät kommuniziert |
| edge_device | Die ID des aktuellen Geräts |
| instance_number | Eine GUID, die die aktuelle Runtime darstellt. Beim Neustart werden alle Metriken zurückgesetzt. Diese GUID erleichtert das Abstimmen von Neustarts. |

Im Prometheus-Offenlegungsformat gibt es vier grundlegende Metriktypen: Zähler, Messgerät, Histogramm und Zusammenfassung. Weitere Informationen zu den verschiedenen Metriktypen finden Sie in der [Dokumentation zu den Prometheus-Metriktypen](https://prometheus.io/docs/concepts/metric_types/).

Die für die integrierten Histogramm- und Zusammenfassungsmetriken bereitgestellten Quantile sind 0,1, 0,5, 0,9 und 0,99.

Das **edgeHub**-Modul erzeugt die folgenden Metriken:

| Name | Dimensionen | BESCHREIBUNG |
|-|-|-|
| `edgehub_gettwin_total` | `source` (Vorgangsquelle)<br> `id` (Modul-ID) | Typ: Zähler<br> Gesamtzahl von GetTwin-Aufrufen |
| `edgehub_messages_received_total` | `route_output` (Ausgabe, von der die Nachricht gesendet wurde)<br> `id` | Typ: Zähler<br> Gesamtzahl der von Clients empfangenen Nachrichten |
| `edgehub_messages_sent_total` | `from` (Nachrichtenquelle)<br> `to` (Nachrichtenziel)<br>`from_route_output`<br> `to_route_input` (Nachrichtenzieleingabe)<br> `priority` (Nachrichtenpriorität zum Ziel) | Typ: Zähler<br> Gesamtzahl der an Clients oder Upstream gesendeten Nachrichten<br> `to_route_input` ist leer, wenn `to` $Upstream ist |
| `edgehub_reported_properties_total` | `target` (Updateziel)<br> `id` | Typ: Zähler<br> Gemeldete Gesamtzahl der Aufrufe für Eigenschaftsupdates |
| `edgehub_message_size_bytes` | `id`<br> | Typ: Zusammenfassung<br> Nachrichtengröße von Clients<br> Die Werte werden möglicherweise als `NaN` gemeldet, wenn für einen bestimmtem Zeitraum (aktuell 10 Minuten) keine neuen Messungen gemeldet wurden; für den Typ `summary` werden die entsprechenden Zähler `_count` und `_sum` ausgegeben. |
| `edgehub_gettwin_duration_seconds` | `source` <br> `id` | Typ: Zusammenfassung<br> Beanspruchte Zeit für das Abrufen von Zwillingsvorgängen |
| `edgehub_message_send_duration_seconds` | `from`<br> `to`<br> `from_route_output`<br> `to_route_input` | Typ: Zusammenfassung<br> Beanspruchte Zeit zum Senden einer Nachricht |
| `edgehub_message_process_duration_seconds` | `from` <br> `to` <br> `priority` | Typ: Zusammenfassung<br> Beanspruchte Zeit zum Verarbeiten einer Nachricht aus der Warteschlange |
| `edgehub_reported_properties_update_duration_seconds` | `target`<br> `id` | Typ: Zusammenfassung<br> Beanspruchte Zeit zum Aktualisieren gemeldeter Eigenschaften |
| `edgehub_direct_method_duration_seconds` | `from` (Aufrufer)<br> `to` (Empfänger) | Typ: Zusammenfassung<br> Beanspruchte Zeit zum Auflösen einer direkten Nachricht |
| `edgehub_direct_methods_total` | `from`<br> `to` | Typ: Zähler<br> Gesamtzahl der gesendeten direkten Nachrichten |
| `edgehub_queue_length` | `endpoint` (Nachrichtenquelle)<br> `priority` (Warteschlangenpriorität) | Typ: Messgerät<br> Aktuelle Länge der edgeHub-Warteschlange für eine bestimmte Priorität |
| `edgehub_messages_dropped_total` | `reason` (no_route, ttl_expiry)<br> `from` <br> `from_route_output` | Typ: Zähler<br> Gesamtzahl der wegen des Grunds entfernten Nachrichten |
| `edgehub_messages_unack_total` | `reason` (storage_failure)<br> `from`<br> `from_route_output` | Typ: Zähler<br> Gesamtzahl der wegen eines Speicherfehlers nicht bestätigten Nachrichten |
| `edgehub_offline_count_total` | `id` | Typ: Zähler<br> Gesamtzahl der Übergänge von edgeHub in den Offlinemodus |
| `edgehub_offline_duration_seconds`| `id` | Typ: Zusammenfassung<br> Die Zeit, die edgeHub offline war |
| `edgehub_operation_retry_total` | `id`<br> `operation` (Vorgangsname) | Typ: Zähler<br> Die Gesamtzahl der Wiederholungen von edgeHub-Vorgängen |
| `edgehub_client_connect_failed_total` | `id` <br> `reason` (nicht authentifiziert)<br> | Typ: Zähler<br> Gesamtzahl der fehlgeschlagenen Verbindungsversuche von Clients mit edgeHub |

Das **edgeAgent**-Modul erzeugt die folgenden Metriken:

| Name | Dimensionen | BESCHREIBUNG |
|-|-|-|
| `edgeAgent_total_time_running_correctly_seconds` | `module_name` | Typ: Messgerät<br> Die Menge der Zeit, für die das Modul in der Bereitstellung angegeben wurde und sich im Ausführungszustand befand |
| `edgeAgent_total_time_expected_running_seconds` | `module_name` | Typ: Messgerät<br> Der Zeitraum, für den das Modul in der Bereitstellung angegeben wurde |
| `edgeAgent_module_start_total` | `module_name`, `module_version` | Typ: Zähler<br> Die Anzahl der Aufforderungen von edgeAgent an Docker, das Modul zu starten |
| `edgeAgent_module_stop_total` | `module_name`, `module_version` | Typ: Zähler<br> Die Anzahl der Aufforderungen von edgeAgent an Docker, das Modul zu beenden |
| `edgeAgent_command_latency_seconds` | `command` | Typ: Messgerät<br> Die von Docker zum Ausführen des angegebenen Befehls beanspruchte Zeit. Die möglichen Befehle sind: create, update, remove, start, stop, restart. |
| `edgeAgent_iothub_syncs_total` | | Typ: Zähler<br> Die Anzahl der Versuche von edgeAgent, seinen Zwilling mit iotHub zu synchronisieren, sowohl erfolgreiche als auch Fehlversuche. Diese Zahl schließt sowohl die Anforderung eines Zwillings durch den Agent als auch die Benachrichtigung zu einem Zwillingsupdate durch den Hub ein. |
| `edgeAgent_unsuccessful_iothub_syncs_total` | | Typ: Zähler<br> Die Anzahl der Fehlversuche von edgeAgent, seinen Zwilling mit iotHub zu synchronisieren. |
| `edgeAgent_deployment_time_seconds` | | Typ: Zähler<br> Sie beanspruchte Zeit für das Abschließen einer neuen Bereitstellung nach dem Empfang einer Änderung. |
| `edgeagent_direct_method_invocations_count` | `method_name` | Typ: Zähler<br> Die Anzahl der Aufrufe einer integrierten direkten Methode von edgeAgent, wie etwa „Ping“ oder „Restart“. |
| `edgeAgent_host_uptime_seconds` | | Typ: Messgerät<br> Kontinuierliche Betriebszeit des Hosts |
| `edgeAgent_iotedged_uptime_seconds` | | Typ: Messgerät<br> Die kontinuierliche Ausführungszeit von iotedged |
| `edgeAgent_available_disk_space_bytes` | `disk_name`, `disk_filesystem`, `disk_filetype` | Typ: Messgerät<br> Die Menge des auf dem Datenträger verbleibenden Speicherplatzes |
| `edgeAgent_total_disk_space_bytes` | `disk_name`, `disk_filesystem`, `disk_filetype`| Typ: Messgerät<br> Größe des Datenträgers |
| `edgeAgent_used_memory_bytes` | `module_name` | Typ: Messgerät<br> Die Menge des von allen Prozessen verwendeten Arbeitsspeichers |
| `edgeAgent_total_memory_bytes` | `module_name` | Typ: Messgerät<br> Verfügbares RAM |
| `edgeAgent_used_cpu_percent` | `module_name` | Typ: Histogramm<br> Der Prozentsatz der CPU-Auslastung durch alle Prozesse |
| `edgeAgent_created_pids_total` | `module_name` | Typ: Messgerät<br> Die Anzahl der Prozesse oder Threads, die der Container erstellt hat |
| `edgeAgent_total_network_in_bytes` | `module_name` | Typ: Messgerät<br> Die Anzahl der vom Netzwerk empfangenen Bytes |
| `edgeAgent_total_network_out_bytes` | `module_name` | Typ: Messgerät<br> Die Anzahl der an das Netzwerk gesendeten Bytes |
| `edgeAgent_total_disk_read_bytes` | `module_name` | Typ: Messgerät<br> Die Anzahl der vom Datenträger gelesenen Bytes |
| `edgeAgent_total_disk_write_bytes` | `module_name` | Typ: Messgerät<br> Die Anzahl der auf den Datenträger geschriebenen Bytes |
| `edgeAgent_metadata` | `edge_agent_version`, `experimental_features`, `host_information` | Typ: Messgerät<br> Allgemeine Metadaten zum Gerät. Der Wert ist immer 0, die Informationen sind in den Tags codiert. Beachten Sie, dass es sich bei `experimental_features` und `host_information` um JSON-Objekte handelt. `host_information` weist diese Form auf: ```{"OperatingSystemType": "linux", "Architecture": "x86_64", "Version": "1.0.10~dev20200803.4", "Provisioning": {"Type": "dps.tpm", "DynamicReprovisioning": false, "AlwaysReprovisionOnStartup": true}, "ServerVersion": "19.03.6", "KernelVersion": "5.0.0-25-generic", "OperatingSystem": "Ubuntu 18.04.4 LTS", "NumCpus": 6, "Virtualized": "yes"}```. Beachten Sie, dass `ServerVersion` die Docker-Version und `Version` die Version des IoT Edge-Sicherheitsdaemons ist. |

## <a name="next-steps"></a>Nächste Schritte

* [Sammeln und Transportieren von Metriken](how-to-collect-and-transport-metrics.md)
* [Grundlegendes zur Azure IoT Edge-Runtime und ihrer Architektur](iot-edge-runtime.md)
* [Eigenschaften der Modulzwillinge von IoT Edge-Agent und IoT Edge-Hub](module-edgeagent-edgehub.md)
