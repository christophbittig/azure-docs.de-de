---
title: Überwachen von Azure Cache for Redis
description: Erfahren Sie, wie Sie die Stabilität und Integrität Ihrer Azure Cache for Redis-Instanzen überwachen.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 64234292051c5f780c33fd55fabf3305f45b756d
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129538411"
---
# <a name="monitor-azure-cache-for-redis"></a>Überwachen von Azure Cache for Redis

Azure Cache for Redis bietet über [Azure Monitor](../azure-monitor/index.yml) verschiedene Optionen zum Überwachen Ihrer Cache-Instanzen. Diese Tools ermöglichen es Ihnen, die Integrität Ihrer Azure Cache for Redis-Instanzen zu überwachen, und unterstützen Sie beim Verwalten Ihrer Cachinganwendungen.

Verwenden Sie Azure Monitor für folgende Zwecke:

- Anzeigen von Metriken
- Anheften von Metrikdiagrammen an das Startmenü
- Anpassen des Datums- und Uhrzeitbereichs von Überwachungsdiagrammen
- Hinzufügen und Entfernen von Metriken in Diagrammen
- Festlegen von Warnungen, wenn bestimmte Bedingungen erfüllt sind

Metriken für Azure Cache for Redis-Instanzen werden über den Redis-Befehl [INFO](https://redis.io/commands/info) gesammelt. Metriken werden etwa zweimal pro Minute gesammelt und automatisch 30 Tage lang gespeichert, sodass sie in den Metrikdiagrammen angezeigt und durch Warnungsregeln ausgewertet werden können.

Informationen zum Konfigurieren einer anderen Aufbewahrungsrichtlinie finden Sie unter [Exportieren von Cachemetriken](#export-cache-metrics).  

Weitere Informationen zu den verschiedenen für die Cachemetriken verwendeten INFO-Werte finden Sie unter [Verfügbare Metriken und Berichtsintervalle](#available-metrics-and-reporting-intervals).

<a name="view-cache-metrics"></a>

Zum Anzeigen von Cachemetriken klicken Sie im [Azure-Portal](https://portal.azure.com) auf [Durchsuchen](cache-configure.md#configure-azure-cache-for-redis-settings), um zu Ihrer Cache-Instanz zu navigieren.  Azure Cache for Redis bietet auf der linken Seite mit **Übersicht** und **Redis-Metriken** einige integrierte Diagramme. Sie können jedes Diagramm benutzerdefiniert anpassen, indem Sie Metriken hinzufügen oder entfernen oder das Berichtsintervall ändern.

![Es werden sechs Graphen angezeigt. Einer von Ihnen ist „Cachetreffer und Cachefehler in der letzten Stunde“.](./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png)

## <a name="view-pre-configured-metrics-charts"></a>Anzeigen von vorkonfigurierten Metrikdiagrammen

In der **Übersicht** auf der linken Seite sind die folgenden vorkonfigurierten Überwachungsdiagramme verfügbar:

- [Überwachungsdiagramme](#monitoring-charts)
- [Nutzungsdiagramme](#usage-charts)

### <a name="monitoring-charts"></a>Überwachungsdiagramme

Im Abschnitt **Überwachung** in der **Übersicht** auf der linken Seite finden Sie die Diagramme **Treffer und Fehler**, **Abrufe und Sets**, **Verbindungen** sowie **Befehle gesamt**.

![Überwachungsdiagramme](./media/cache-how-to-monitor/redis-cache-monitoring-part.png)

### <a name="usage-charts"></a>Nutzungsdiagramme

Der Abschnitt **Nutzung** in der **Übersicht** auf der linken Seite umfasst die Diagramme **Redis-Serverlast**, **Speicherauslastung**, **Netzwerkbandbreite** und **CPU-Auslastung** und zeigt auch den **Tarif** für die Cache-Instanz an.

![Nutzungsdiagramme](./media/cache-how-to-monitor/redis-cache-usage-part.png)

Der Bereich **Tarif** zeigt den Tarif für den Cache an und kann zum [Skalieren](cache-how-to-scale.md) des Caches in einen anderen Tarif verwendet werden.

## <a name="view-metrics-charts-for-all-your-caches-with-azure-monitor-for-azure-cache-for-redis"></a>Anzeigen von Metrikdiagrammen für alle Ihre Caches mit Azure Monitor für Azure Cache for Redis

Verwenden Sie [Azure Monitor für Azure Cache for Redis](../azure-monitor/insights/redis-cache-insights-overview.md)(Vorschau), um eine Übersicht über die Gesamtleistung, Fehler, Kapazität und Betriebsintegrität aller Ihrer Azure Cache for Redis-Ressourcen zu erhalten. Zeigen Sie Metriken auf einer anpassbaren, einheitlichen und interaktiven Benutzeroberfläche an, mit der Sie Details für einzelne Ressourcen abrufen können. Azure Monitor für Azure Cache for Redis basiert auf dem [Arbeitsmappenfeature von Azure Monitor](../azure-monitor/visualize/workbooks-overview.md), das umfassende Visualisierungen für Metriken und andere Daten bereitstellt. Weitere Informationen finden Sie im Artikel [Erkunden von Azure Monitor für Azure Cache for Redis](../azure-monitor/insights/redis-cache-insights-overview.md).

## <a name="view-metrics-with-azure-monitor-metrics-explorer"></a>Anzeigen von Metriken mit dem Metrik-Explorer von Azure Monitor

In Szenarien, in denen Sie nicht die volle Flexibilität von Azure Monitor für Azure Cache for Redis benötigen, können Sie stattdessen mithilfe des Azure Monitor Metrik-Explorers Metriken anzeigen und benutzerdefinierte Diagramme erstellen. Wählen Sie im **Ressourcenmenü** die Option **Metriken** aus, und passen Sie das Diagramm mit Ihren bevorzugten Metriken, dem Berichtsintervall, dem Diagrammtyp usw. an.

![Im linken Navigationsbereich von contoso55 ist „Metriken“ eine Option unter „Überwachung“, die hervorgehoben ist. Unter „Metriken“ gibt es eine Liste von Metriken. Cachetreffer und Cachefehler sind ausgewählt.](./media/cache-how-to-monitor/redis-cache-monitor.png)

Weitere Informationen zum Verwenden von Metriken mit Azure Monitor finden Sie unter [Übersicht über Metriken in Microsoft Azure](../azure-monitor/data-platform.md).

<a name="enable-cache-diagnostics"></a>

## <a name="export-cache-metrics"></a>Exportieren von Cachemetriken

Cachemetriken werden in Azure Monitor standardmäßig [30 Tage lang gespeichert](../azure-monitor/essentials/data-platform-metrics.md) und anschließend gelöscht. Um Cachemetriken länger als 30 Tage beizubehalten, können Sie [ein Speicherkonto festlegen](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) und eine Richtlinie **Aufbewahrung (Tage)** für die Cachemetriken angeben.

So konfigurieren Sie ein Speicherkonto für die Cachemetriken

1. Wählen Sie auf der Seite **Azure Cache for Redis** unter der Überschrift **Überwachung** die Option **Diagnose** aus.
1. Wählen Sie **+Diagnoseeinstellung hinzufügen**  aus.
1. Benennen Sie die Einstellungen.
1. Aktivieren Sie **In einem Speicherkonto archivieren**. Ihnen werden normale Datenraten für Speicherung und Transaktionen in Rechnung gestellt, wenn Sie Diagnoseinformationen an ein Speicherkonto senden.
1. Wählen Sie **Konfigurieren** aus, um das Speicherkonto auszuwählen, in dem die Cachemetriken gespeichert werden sollen.
1. Aktivieren Sie unter der Tabellenüberschrift **Metrik** das Kontrollkästchen neben den einzelnen Elementen, die Sie speichern möchten, z. B. **AllMetrics**. Geben Sie eine Richtlinie **Aufbewahrung (Tage)** an. Die maximale Anzahl von Tagen, die Sie für die Aufbewahrung angeben können, beträgt **365 Tage**. Wenn Sie die Metrikdaten aber unbegrenzt aufbewahren möchten, legen Sie die **Aufbewahrung (Tage)** auf **0** fest.
1. Wählen Sie **Speichern** aus.

![Redis-Diagnose](./media/cache-how-to-monitor/redis-cache-diagnostics.png)

>[!NOTE]
>Neben dem Archivieren der Cachemetriken im Speicher können Sie sie auch [an einen Event Hub streamen oder an Azure Monitor-Protokolle senden](../azure-monitor/essentials/rest-api-walkthrough.md#retrieve-metric-values).
>

Zum Zugreifen auf die Metriken können Sie sie im Azure-Portal anzeigen, wie es oben in diesem Artikel beschrieben ist. Sie können auch über die [Azure Monitor-REST-API für Metriken](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md) darauf zugreifen.

> [!NOTE]
> Wenn Sie das Speicherkonto ändern, sind die Daten im zuvor konfigurierten Speicherkonto weiterhin zum Download verfügbar, werden im Azure-Portal jedoch nicht angezeigt.  
>

## <a name="available-metrics-and-reporting-intervals"></a>Verfügbare Metriken und Berichtsintervalle

Cachemetriken werden in verschiedenen Berichtsintervallen gemeldet, z.B. **Letzte Stunde**, **Heute**, **Letzte Woche** und **Benutzerdefiniert**. Auf der linken Seite befindet sich die Auswahlmöglichkeit **Metrik** für jedes Metrikdiagramm, mit der die Durchschnitts-, Minimal- und Maximalwerte für jede Metrik im Diagramm angezeigt werden. Einige Metriken zeigen einen Gesamtwert für das Berichtsintervall an.

Jede Metrik umfasst zwei Versionen. Eine Metrik misst die Leistung für den gesamten Cache. Eine zweite Version der Metrik, deren Name `(Shard 0-9)` enthält, misst für Caches, die [Clustering](cache-how-to-premium-clustering.md) verwenden, die Leistung für einen einzelnen Shard in einem Cache. Wenn z. B. ein Cache vier Shards enthält, ist `Cache Hits` der Gesamtanzahl der Treffer für den gesamten Cache, und `Cache Hits (Shard 3)` gibt lediglich die Treffer für dieses Shard des Caches an.

> [!NOTE]
> Bei Anzeige des Aggregationstyps gilt:
>
> - „Count“ mit dem Wert „2“ bedeutet, dass die Metrik zwei Datenpunkte für die Zeitgranularität (1 Minute) empfangen hat.
> - „Max“ zeigt den Maximalwert eines Datenpunkts in der Zeitgranularität.
> - „Min“ zeigt den Minimalwert eines Datenpunkts in der Zeitgranularität.
> - „Average“ zeigt den Durchschnittswert aller Datenpunkte in der Zeitgranularität.
> - „Sum“ zeigt die Summe aller Datenpunkte in der Zeitgranularität und kann je nach Metrik irreführend sein.
> Unter normalen Umständen sind „Average“ und „Max“ sehr ähnlich, da nur ein Knoten diese Metriken ausgibt (der Masterknoten). In einem Szenario, in dem sich die Anzahl der verbundenen Clients schnell ändert, zeigen „Max“, „Average“ und „Min“ sehr unterschiedliche Werte, und dies ist auch das erwartete Verhalten.
>
> Im Allgemeinen zeigt „Average“ ein gleichmäßiges Diagramm der gewünschten Metrik und reagiert gut auf Änderungen der Zeitgranularität. „Max“ und „Min“ können große Änderungen in der Metrik verbergen, wenn die Zeitgranularität groß ist, können aber mit einer kleinen Zeitgranularität verwendet werden, um genaue Zeitpunkte zu ermitteln, zu denen große Änderungen in der Metrik auftreten.
>
> „Count“ und „Sum“ können bei bestimmten Metriken (einschließlich verbundener Clients) irreführend sein.
>
> Daher wird empfohlen, sich die Metriken „Average“ und nicht die Metriken „Sum“ anzusehen.

> [!NOTE]
> Selbst wenn keine der mit dem Cache verbundenen Clientanwendungen aktiv ist, wird möglicherweise Cacheaktivität angezeigt, wie z. B. verbundene Clients, Arbeitsspeicherauslastung und Vorgänge, die gerade ausgeführt werden. Diese Aktivität ist beim Betrieb einer Azure Cache for Redis-Instanz normal.
>
>

| Metrik | BESCHREIBUNG |
| --- | --- |
| Cachetreffer |Die Anzahl der erfolgreichen Schlüsselsuchen während des angegebenen Berichtsintervalls. Diese Zahl ist `keyspace_hits` aus dem Redis-Befehl [INFO](https://redis.io/commands/info) zugeordnet. |
| Cachelatenz (Vorschau) | Die anhand der Latenz des Caches zwischen Knoten berechnete Cachelatenz. Diese Metrik wird in Mikrosekunden gemessen und verfügt über drei Dimensionen: `Avg`, `Min` und `Max`. Die Dimensionen stellen die durchschnittliche, minimale bzw. maximale Latenz des Caches während des angegebenen Berichtsintervalls dar. |
| Cachefehler |Die Anzahl der fehlerhaften Schlüsselsuchen während des angegebenen Berichtsintervalls. Diese Zahl ist `keyspace_misses` aus dem Redis-Befehl INFO zugeordnet. Cachefehler bedeuten nicht unbedingt, dass ein Problem mit dem Cache vorliegt. Bei Verwendung des cachefremden Programmierschemas sucht eine Anwendung zuerst im Cache nach einem Element. Wenn das gesuchte Element nicht vorhanden ist (Cachefehler), wird es aus der Datenbank abgerufen und dem Cache für das nächste Mal hinzugefügt. Cachefehler sind ein normales Verhalten für das cachefremde Programmierschema. Wenn die Anzahl der Cachefehler höher ist als erwartet, untersuchen Sie die Anwendungslogik, die den Cache auffüllt und aus dem Cache liest. Wenn Elemente aufgrund von ungenügendem Arbeitsspeicher aus dem Cache entfernt werden, können Cachefehler auftreten. Zur Überwachung der Arbeitsspeicherauslastung eignen sich jedoch die Metriken `Used Memory` oder `Evicted Keys` besser. |
| Cache-Lesevorgänge |Die Menge an Daten in Megabyte pro Sekunde (MB/s), die während des angegebenen Berichtsintervalls im Cache gelesen wurden. Dieser Wert wird von den Netzwerkschnittstellenkarten für den virtuellen Computer abgeleitet, der den Cache hostet, und ist nicht Redis-spezifisch. **Dieser Wert entspricht der von diesem Cache verwendeten Netzwerkbandbreite. Wenn Sie Warnungen für serverseitige Grenzwerte bei der Netzwerkbandbreite einrichten möchten, erstellen Sie sie mithilfe des `Cache Read`-Leistungsindikators. Die beobachteten Bandbreitengrenzwerte für verschiedene Cachetarife und -größen finden Sie in [dieser Tabelle](./cache-planning-faq.yml#azure-cache-for-redis-performance).** |
| Cache-Schreibvorgänge |Die Menge an Daten in Megabyte pro Sekunde (MB/s), die während des angegebenen Berichtsintervalls in den Cache geschrieben wurden. Dieser Wert wird von den Netzwerkschnittstellenkarten für den virtuellen Computer abgeleitet, der den Cache hostet, und ist nicht Redis-spezifisch. Dieser Wert entspricht der Netzwerkbandbreite der vom Client an den Cache gesendeten Daten. |
| Verbundene Clients |Die Anzahl von Clientverbindungen mit dem Cache während des angegebenen Berichtsintervalls. Diese Zahl ist `connected_clients` aus dem Redis-Befehl INFO zugeordnet. Sobald der [Grenzwert für Verbindungen](cache-configure.md#default-redis-server-configuration) erreicht ist, treten bei späteren Verbindungsversuchen mit dem Cache Fehler auf. Auch wenn keine Clientanwendungen aktiv sind, können aufgrund interner Prozesse und Verbindungen dennoch einige Instanzen verbundener Clients vorhanden sein. |
| CPU |Die CPU-Auslastung des Azure Cache for Redis-Servers in Prozent während des angegebenen Berichtsintervalls. Dieser Wert ist dem Betriebssystem-Leistungsindikator `\Processor(_Total)\% Processor Time` zugeordnet, |
| Errors | Bestimmte Fehler und Leistungsprobleme, mit denen der Cache während eines angegebenen Berichtsintervalls konfrontiert werden könnte. Diese Metrik hat acht Dimensionen, die verschiedene Fehlertypen darstellen, könnte jedoch zukünftig mit weiteren ergänzt werden. Derzeit werden folgende Fehlertypen dargestellt: <br/><ul><li>**Failover**: Wenn ein Failover für einen Cache erfolgt (untergeordneter Cache wird zum primären Cache heraufgestuft)</li><li>**Dataloss**: Wenn Datenverlust auf dem Cache auftritt</li><li>**UnresponsiveClients**: Wenn die Clients nicht schnell genug Daten vom Server lesen</li><li>**AOF**: Wenn ein Problem im Zusammenhang mit der AOF-Persistenz auftritt</li><li>**RDB**: Wenn ein Problem im Zusammenhang mit der RDB-Persistenz auftritt</li><li>**Import**: Wenn ein Problem im Zusammenhang mit „Import RDB“ auftritt</li><li>**Export**: Wenn ein Problem im Zusammenhang mit „Export RDB“ auftritt</li></ul> |
| Entfernte Schlüssel |Die Anzahl von Elementen, die aufgrund des `maxmemory`-Grenzwerts während des angegebenen Berichtsintervalls aus dem Cache entfernt wurden. Diese Zahl ist `evicted_keys` aus dem Redis-Befehl INFO zugeordnet. |
| Abgelaufene Schlüssel |Die Anzahl von Elementen, die während des angegebenen Berichtsintervalls im Cache abgelaufen sind. Dieser Wert ist `expired_keys` aus dem Redis-Befehl „INFO“ zugeordnet.|
| get-Vorgänge |Die Anzahl von get-Vorgängen im Cache während des angegebenen Berichtsintervalls. Dieser Wert ist die Summe der folgenden Werte aus dem Redis-INFO-Befehl "all": `cmdstat_get`, `cmdstat_hget`, `cmdstat_hgetall`, `cmdstat_hmget`, `cmdstat_mget`, `cmdstat_getbit` und `cmdstat_getrange` und entspricht der Summe aller Cachetreffer und Cachefehler während des angegebenen Berichtsintervalls. |
| Vorgänge pro Sekunde | Die Gesamtzahl aller Befehle, die während des angegebenen Berichtsintervalls vom Cacheserver pro Sekunde verarbeitet wurden.  Dieser Wert wird „instantaneous_ops_per_sec“ aus dem Redis-INFO-Befehl zugeordnet. |
| Redis-Serverlast |Der Prozentsatz der Zyklen, in denen der Redis-Server mit der Verarbeitung beschäftigt ist und nicht auf Nachrichten wartet. Wenn dieser Zähler den Wert 100 erreicht, bedeutet dies, dass der Redis-Server die Leistungsobergrenze erreicht hat und die CPU nicht schneller arbeiten kann. Wenn eine hohe Redis-Serverlast angezeigt wird, bedeutet dies auch Timeoutausnahmen auf dem Client. In diesem Fall sollten Sie erwägen, den Cache zentral hochzuskalieren oder Ihre Daten in mehrere Caches zu partitionieren. |
| Sets |Die Anzahl von set-Vorgängen im Cache während des angegebenen Berichtsintervalls. Dieser Wert ist die Summe der folgenden Werte aus dem Redis-INFO-Befehl "all": `cmdstat_set`, `cmdstat_hset`, `cmdstat_hmset`, `cmdstat_hsetnx`, `cmdstat_lset`, `cmdstat_mset`, `cmdstat_msetnx`, `cmdstat_setbit`, `cmdstat_setex`, `cmdstat_setrange` und `cmdstat_setnx`. |
| Schlüssel insgesamt  | Die maximale Anzahl der Schlüssel im Cache während des letzten Berichtszeitraums. Diese Zahl ist `keyspace` aus dem Redis-Befehl INFO zugeordnet. Aufgrund einer Einschränkung im zugrunde liegenden Metriksystem für Caches mit aktiviertem Clustering gibt „Schlüssel insgesamt“ die maximale Anzahl der Schlüssel des Shards zurück, der im Berichtsintervall die maximale Anzahl der Schlüssel aufwies.  |
| Vorgänge gesamt |Die Gesamtzahl aller Befehle, die während des angegebenen Berichtsintervalls vom Cacheserver verarbeitet wurden. Dieser Wert ist `total_commands_processed` aus dem Redis-Befehl „INFO“ zugeordnet. Wenn Azure Cache for Redis ausschließlich für pub/sub-Vorgänge verwendet wird, sind keine Metriken für `Cache Hits`, `Cache Misses`, `Gets` oder `Sets` vorhanden. Stattdessen sind `Total Operations`-Metriken vorhanden, die die Cachenutzung für pub/sub-Vorgänge widerspiegeln. |
| Verwendeter Arbeitsspeicher |Die Menge an Cachespeicher in MB, die während des angegebenen Berichtsintervalls für Schlüssel-Wert-Paare im Cache verwendet wurde. Dieser Wert ist `used_memory` aus dem Redis-Befehl „INFO“ zugeordnet. Dieser Wert enthält keine Metadaten oder Fragmentierung. |
| Prozentsatz der Arbeitsspeicherverwendung | Der prozentuale Anteil des gesamten Arbeitsspeichers, der während des angegebenen Berichtsintervalls verwendet wird.  Dieser Wert verweist auf den Wert `used_memory` aus dem Redis-INFO-Befehl zum Berechnen des Prozentsatzes. |
| Verwendeter Arbeitsspeicher (RSS) |Die Menge an verwendetem Cachearbeitsspeicher in MB während des angegebenen Berichtsintervalls, einschließlich Fragmentierung und Metadaten. Dieser Wert ist `used_memory_rss` aus dem Redis-Befehl „INFO“ zugeordnet. |

<a name="operations-and-alerts"></a>

## <a name="alerts"></a>Alerts

Sie können die Konfiguration so durchführen, dass Warnungen basierend auf Metriken und Aktivitätsprotokollen empfangen werden. Mit Azure Monitor können Sie eine Warnung so konfigurieren, dass Folgendes erfolgt, wenn sie ausgelöst wird:

- Senden einer E-Mail-Benachrichtigung
- Aufrufen eines Webhooks
- Aufrufen einer Azure-Logik-App

Zum Konfigurieren von Warnungsregeln für den Cache wählen Sie im **Ressourcenmenü** die Option **Warnungsregeln** aus.

![Überwachung](./media/cache-how-to-monitor/redis-cache-monitoring.png)

Weitere Informationen zum Konfigurieren und Verwenden von Warnungen finden Sie unter [Übersicht über Warnungen](../azure-monitor/alerts/alerts-classic-portal.md).

<!-- 
## Activity Logs

Activity logs provide insight into the operations that completed on your Azure Cache for Redis instances. It was previously known as "audit logs" or "operational logs". Using activity logs, you can determine the "what, who, and when" for any write operations (PUT, POST, DELETE) taken on your Azure Cache for Redis instances.

> [!NOTE]
> Activity logs do not include read (GET) operations.
> 

To view activity logs for your cache, select **Activity logs** from the **Resource menu**.

For more information about Activity logs, see [Overview of the Azure Activity Log](../azure-monitor/essentials/platform-logs-overview.md).
-->
