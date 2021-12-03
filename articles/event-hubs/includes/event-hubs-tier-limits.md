---
Titel: include file Beschreibung: include file Dienste: event-hubs Autor: spelluru ms.service: event-hubs ms.topic: include ms.date: 11/04/2021 ms.author: spelluru ms.custom: "datei einschließen", "fasttrack-edit", "iot", "event-hubs"

---

Die folgende Tabelle enthält die Grenzwerte, die sich für die Tarife „Basic“, „Standard“ und „Dedicated“ unterscheiden können. In der Tabelle steht CU für [Kapazitätseinheit](../event-hubs-dedicated-overview.md) (Capacity Unit), PU für [Verarbeitungseinheit](../event-hubs-scalability.md#processing-units) (Processing Unit) und TU für [Durchsatzeinheit](../event-hubs-scalability.md#throughput-units) (Throughput Unit). 

| Begrenzung | Basic | Standard | Premium |  Dediziert |
| ----- | ----- | -------- | -------- | --------- | 
| Maximale Größe der Event Hubs-Veröffentlichung | 256 KB | 1 MB | 1 MB |  1 MB |
| Anzahl von Consumergruppen pro Event Hub | 1 | 20 | 100 | 1000<br/>Kein Grenzwert pro CU  |
| Anzahl vermittelter Verbindungen pro Namespace | 100 | 5\.000 | 10000 pro PU | 100000 pro CU |
| Maximale Aufbewahrungsdauer von Ereignisdaten | 1 Tag | 7 Tage | 90 Tage<br/>1 TB pro PU | 90 Tage<br/>10 TB pro CU |
| Maximale Anzahl von TUs, PUs oder CUs | 40 TUs | 40 TUs | 16 PUs | 20 CUs |
| Anzahl von Partitionen pro Event Hub | 32 | 32 | 100 pro Veranstaltungszentrum, 200 pro PU | 1\.024 pro Event Hub<br/> 2000 pro CU |
| Anzahl von Namespaces pro Abonnement | 1000 | 1000 | 1000 | 1000 (50 pro CU) |
| Anzahl von Event Hubs pro Namespace | 10 | 10 | 100 pro PU | 1000 |
| Erfassung | – | Bezahlung pro Stunde | Enthalten | Enthalten |
| Größe der Schemaregistrierung (Namespace) in Megabytes | – | 25 | 100 | 1024 |
| Anzahl von Schemagruppen in einer Schemaregistrierung oder einem Namespace | – | 1 (ohne Standardgruppe) | 100 <br/>1 MB pro Schema | 1000<br/>1 MB pro Schema |
| Anzahl von Schemaversionen in allen Schemagruppen | – | 25 | 1000 | 10000 |
| Durchsatz pro Einheit | Eingehend: 1 MB/s oder 1000 Ereignisse pro Sekunde<br/>Ausgehend: 2 MB/s oder 4096 Ereignisse pro Sekunde | Eingehend: 1 MB/s oder 1000 Ereignisse pro Sekunde<br/>Ausgehend: 2 MB/s oder 4096 Ereignisse pro Sekunde | Keine Grenzwerte pro PU * | Keine Grenzwerte pro CU * |

\* Hängt von verschiedenen Faktoren wie Ressourcenzuordnung, Anzahl von Partitionen, Speicher und Ähnlichem ab. 
 

> [!NOTE]
> Sie können Ereignisse einzeln oder als Batch veröffentlichen. Der Veröffentlichungsgrenzwert gilt (je nach SKU) unabhängig davon, ob es sich um ein einzelnes Ereignis oder einen Batch handelt. Das Veröffentlichen von Ereignissen, die größer als der maximale Schwellenwert sind, wird abgelehnt.

