---
title: 'Muster für Ereignisreplikationsaufgaben: Azure Event Hubs | Microsoft-Dokumentation'
description: Dieser Artikel enthält ausführliche Anleitungen zur Implementierung spezifischer Muster für Ereignisreplikationsaufgaben.
ms.topic: article
ms.date: 09/28/2021
ms.openlocfilehash: f4fb2a166a5dfd50f3035e5efe5462313716daef
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129217408"
---
# <a name="event-replication-tasks-patterns"></a>Muster für Ereignisreplikationsaufgaben

In der [Übersicht über Verbund](event-hubs-federation-overview.md) und [Übersicht über Replikatorfunktionen](event-hubs-federation-replicator-functions.md) werden die Gründe für und die wesentlichen Elemente von Replikationsaufgaben erläutert. Es wird empfohlen, dass Sie sich damit vertraut machen, ehe Sie mit diesem Artikel fortfahren.

In diesem Artikel finden Sie eine ausführliche Anleitung zur Implementierung einiger der in der Übersicht erwähnten Muster.

## <a name="replication"></a>Replikation

Das Replikationsmuster kopiert Ereignisse von einer Event Hub-Instanz zur nächsten oder von einer -Instanz in ein anderes Ziel, z. B. eine Service Bus-Warteschlange. Die Ereignisse werden weitergeleitet, ohne dass Änderungen an den Ereignisnutzdaten vorgenommen werden.

Die Implementierung dieses Musters wird durch die Beispiele zur [Ereignisreplikation zwischen Event Hubs](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy) und [Ereignisreplikation zwischen Event Hubs und Service Bus](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus) und das Tutorial [Verwenden von Apache Kafka MirrorMaker mit Event Hubs](event-hubs-kafka-mirror-maker-tutorial.md) für den spezifischen Fall des Replizierens von Daten von einem Apache Kafka-Broker in Event Hubs abgedeckt.

### <a name="streams-and-order-preservation"></a>Datenströme und Beibehaltung der Reihenfolge

Die Replikation, entweder über Azure Functions oder Azure Stream Analytics, zielt nicht darauf ab, exakte 1:1-Klone einer Event Hub-Quellinstanz in einer Event Hub-Zielinstanz zu erstellen, sondern darauf ab, die relative Reihenfolge der Ereignisse beizubehalten, wenn die Anwendung dies erfordert. Die Anwendung kommuniziert dies, indem sie zusammengehörige Ereignisse anhand desselben Partitionsschlüssels gruppiert. [Event Hubs ordnet Nachrichten mit demselben Partitionsschlüssel in derselben Partition sequenziell an](event-hubs-features.md#partitions).

> [!IMPORTANT] 
> Die Informationen zum „Versatz“ sind für jede Event Hub-Instanz eindeutig. Der Versatz für dieselben Ereignisse unterscheidet sich je nach Event Hub-Instanz. Um eine Position in einem kopierten Ereignisdatenstrom zu bestimmen, verwenden Sie den zeitbasierten Versatz und verweisen auf die [weitergegebenen, vom Dienst zugewiesenen Metadaten](#service-assigned-metadata).
>
> Bei einem zeitbasierten Versatz wird Ihr Empfänger zu einem bestimmten Zeitpunkt gestartet:
> - *EventPosition.FromStart()* : alle beibehaltenen Daten erneut lesen.
> - *EventPosition.FromEnd()* : alle neuen Daten ab dem Zeitpunkt der Verbindungsherstellung lesen.
> - *EventPosition.FromEnqueuedTime(dateTime)* : alle Daten ab einem bestimmten Datum und einer bestimmten Uhrzeit lesen.
>
> In EventProcessor legen Sie die Position über InitialOffsetProvider in EventProcessorOptions fest. Bei den anderen Empfänger-APIs wird die Position über den Konstruktor übergeben. 


Die vordefinierten Replikationshilfsfunktionen, die [als Beispiele bereitgestellt](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) und in der auf Azure Functions basierenden Anleitung verwendet werden, stellen Folgendes sicher: Ereignisdatenströme mit demselben Partitionsschlüssel, die aus einer Quellpartition abgerufen werden, werden als Batch im Originaldatenstrom und mit demselben Partitionsschlüssel an die Event Hub-Zielpartition übermittelt.

Wenn die Anzahl der Partitionen für die Event Hub-Quell- und Zielinstanz identisch ist, werden alle Datenströme in der Zielinstanz denselben Partitionen wie in der Quellinstanz zugeordnet.
Wenn die Anzahl der Partitionen unterschiedlich ist, was bei einigen der im Folgenden beschriebenen weiteren Muster eine Rolle spielt, unterscheidet sich die Zuordnung, aber die Datenströme werden stets zusammen und in der richtigen Reihenfolge gehalten.

Die relative Reihenfolge von Ereignissen, die zu verschiedenen Datenströmen gehören, oder von unabhängigen Ereignissen ohne Partitionsschlüssel in einer Zielpartition kann sich stets von der Quellpartition unterscheiden.

### <a name="service-assigned-metadata"></a>Vom Dienst zugewiesene Metadaten

Die aus der Event Hub-Quellinstanz abgerufenen vom Dienst zugewiesenen Metadaten eines Ereignisses, die ursprüngliche Einreihungszeit in die Warteschlange, die Sequenznummer und der Versatz, werden in der Event Hub-Zielinstanz durch neue vom Dienst zugewiesene Werte ersetzt. Aber dank der [Hilfsfunktionen](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) für Replikationsaufgaben in unseren Beispielen bleiben die ursprünglichen Werte in den Benutzereigenschaften erhalten: `repl-enqueue-time` (ISO8601-Zeichenfolge), `repl-sequence`, `repl-offset`.

Diese Eigenschaften haben den Typ „string“ und enthalten den Zeichenfolgenwert der entsprechenden ursprünglichen Eigenschaften. Wenn das Ereignis mehrfach weitergeleitet wird, werden die vom Dienst zugewiesenen Metadaten der unmittelbaren Quelle an die bereits vorhandenen Eigenschaften angefügt, wobei die Werte durch Semikolon getrennt sind.

### <a name="failover"></a>Failover

Wenn Sie die Replikation zum Zweck der Notfallwiederherstellung, zum Schutz vor einem regionalen Ausfall von Verfügbarkeit im Event Hubs-Dienst oder vor Netzwerkunterbrechungen einsetzen, muss in einem solchen Ausfallszenario ein Failover von einer Event Hub-Instanz zur nächsten erfolgen, wobei Producer und/oder Consumer angewiesen werden, den sekundären Endpunkt zu verwenden.

Für alle Failoverszenarien wird davon ausgegangen, dass die erforderlichen Elemente der Namespaces strukturell identisch sind. Das bedeutet, dass Event Hub-Instanzen und Consumergruppen identisch benannt und Regeln für SAS (Shared Access Signature) und/oder rollenbasierte Zugriffssteuerung auf gleiche Weise eingerichtet sind. Sie können einen sekundären Namespace erstellen (und aktualisieren), indem Sie die [Anleitung zum Verschieben von Namespaces](move-across-regions.md) befolgen und den Bereinigungsschritt auslassen.

Um Producer und Consumer zum Umstieg zu zwingen, müssen Sie die Informationen darüber, welcher Namespace verwendet werden soll, an einem leicht zu erreichenden und zu aktualisierenden Ort zum Lookup bereitstellen. Wenn Producer oder Consumer auf häufige oder anhaltende Fehler stoßen, sollten sie diesen Ort konsultieren und ihre Konfiguration anpassen. Es gibt zahlreiche Möglichkeiten zur Freigabe dieser Konfiguration. Nachstehend stellen wir zwei davon vor: DNS und Dateifreigaben.

#### <a name="dns-based-failover-configuration"></a>Auf DNS basierende Failoverkonfiguration

Ein möglicher Ansatz besteht darin, die Informationen in DNS SRV-Einträgen in einem von Ihnen kontrollierten DNS vorzuhalten und auf die entsprechenden Event Hub-Endpunkte zu zeigen. 

> [!IMPORTANT] 
> Beachten Sie, dass Event Hubs nicht zulässt, dass seine Endpunkte direkt mit CNAME-Einträgen als Alias versehen werden. Das bedeutet, dass Sie DNS als resilienten Lookupmechanismus für Endpunktadressen und nicht zur direkten Auflösung von IP-Adressinformationen nutzen können.

Angenommen, Sie besitzen die Domäne `example.com` und für Ihre Anwendung die Zone `test.example.com`. Für zwei alternative Event Hubs-Instanzen erstellen Sie nun zwei weitere geschachtelte Zonen mit je einem SRV-Eintrag.

Die SRV-Einträge haben der üblichen Konvention folgend das Präfix `_azure_eventhubs._amqp` und enthalten zwei Endpunkteinträge: Einen für AMQP über TLS an Port 5671 und einen für AMQP über WebSockets an Port 443, die beide auf den Event Hubs-Endpunkt des der Zone entsprechenden Namespace verweisen.

| Zone                   | SRV-Eintrag                                                                                                                                                            |
| ---------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `eh1.test.example.com` | **`_azure_servicebus._amqp.eh1.test.example.com`**<br>`1 1 5671 eh1-test-example-com.servicebus.windows.net`<br>`2 2 443 eh1-test-example-com.servicebus.windows.net` |
| `eh2.test.example.com` | **`_azure_servicebus._amqp.eh2.test.example.com`**<br>`1 1 5671 eh2-test-example-com.servicebus.windows.net`<br>`2 2 443 eh2-test-example-com.servicebus.windows.net` |

In der Zone Ihrer Anwendung erstellen Sie dann einen CNAME-Eintrag, der auf die untergeordnete Zone verweist, die Ihrer primären Event Hub-Instanz entspricht:

| CNAME-Eintrag                | Alias                    |
| --------------------------- | ------------------------ |
| `eventhub.test.example.com` | `eh1.test.example.com`   |

Mit einem DNS-Client, der das Abfragen von CNAME- und SRV-Einträgen explizit zulässt (die integrierten Clients von Java und .NET lassen nur die einfache Auflösung von Namen in IP-Adressen zu), können Sie dann den gewünschten Endpunkt auflösen. Bei [DnsClient.NET](https://dnsclient.michaco.net/) ist die Lookupfunktion beispielsweise wie folgt:

```C#
static string GetEventHubName(string aliasName)
{
    const string SrvRecordPrefix = "_azure_eventhub._amqp.";
    LookupClient lookup = new LookupClient();

    return (from CNameRecord alias in (lookup.Query(aliasName, QueryType.CNAME).Answers)
            from SrvRecord srv in lookup.Query(SrvRecordPrefix + alias.CanonicalName, QueryType.SRV).Answers
            where srv.Port == 5671
            select srv.Target).FirstOrDefault()?.Value.TrimEnd('.');
}
```

Die Funktion gibt den Namen des Zielhosts zurück, der für Port 5671 der Zone registriert ist, die derzeit als Alias den CNAME hat, wie oben gezeigt.

Um ein Failover durchzuführen, müssen Sie den CNAME-Eintrag so bearbeiten, dass er auf die alternative Zone verweist.

Der Vorteil von DNS und speziell von [Azure DNS](../dns/dns-overview.md) ist, dass Azure DNS-Informationen global repliziert werden und daher resilient gegen Ausfälle einzelner Regionen sind.

Dieses Verfahren ähnelt der Funktionsweise der [georedundanten Notfallwiederherstellung von Event Hubs](event-hubs-geo-dr.md), das jedoch vollständig unter Ihrer Kontrolle bleibt und auch mit Aktiv/Aktiv-Szenarien funktioniert.

#### <a name="file-share-based-failover-configuration"></a>Auf Dateifreigaben basierende Failoverkonfiguration

Die einfachste Alternative zu DNS für die Freigabe von Endpunktinformationen ist, den Namen des primären Endpunkts in eine Klartextdatei einzugeben und die Datei über eine Infrastruktur bereitzustellen, die resistent gegen Ausfälle ist und dennoch Aktualisierungen erlaubt.

Wenn Sie bereits eine hochverfügbare Infrastruktur für Websites mit globaler Verfügbarkeit und Inhaltsreplikation betreiben, fügen Sie dieser eine solche Datei hinzu, und veröffentlichen Sie die Datei erneut, wenn ein Umstieg erforderlich ist.

> [!CAUTION]
> Sie sollten nur den Endpunktnamen auf diese Weise veröffentlichen, nicht eine vollständige Verbindungszeichenfolge einschließlich Geheimnissen.

#### <a name="extra-considerations-for-failing-over-consumers"></a>Weitere Überlegungen zum Failover von Consumern

Für Event Hub-Consumer hängen weitere Aspekte der Failoverstrategie von den Anforderungen des Ereignisprozessors ab.

Wenn ein Notfall eintritt, der die Wiederherstellung eines Systems, einschließlich Datenbanken, aus Sicherungsdaten erfordert, und die Datenbanken direkt oder über eine Zwischenverarbeitung aus den in der Event Hub-Instanz gespeicherten Ereignissen eingefügt werden, stellen Sie die Sicherung wieder her. Sie beginnen dann mit der Wiedergabe von Ereignissen in das System ab dem Zeitpunkt, an dem die Sicherung der Datenbank erstellt wurde und nicht ab dem Zeitpunkt, an dem das ursprüngliche System beschädigt wurde.

Wenn ein Ausfall nur einen Teil eines Systems oder sogar nur eine einzelne Event Hub-Instanz betrifft, die nicht mehr erreichbar ist, möchten Sie die Verarbeitung von Ereignissen wahrscheinlich ungefähr an der Stelle fortsetzen, an der die Verarbeitung unterbrochen wurde.

Um eines der beiden Szenarien zu verwirklichen und den Ereignisprozessor des jeweiligen Azure SDK zu verwenden, [erstellen Sie einen neuen Prüfpunktspeicher](event-processor-balance-partition-load.md#checkpointing) und geben eine anfängliche Partitionsposition an, die auf dem _Zeitstempel_ basiert, ab dem Sie die Verarbeitung fortsetzen möchten.

Wenn Sie nach wie vor Zugriff auf den Prüfpunktspeicher der Event Hub-Instanz haben, von der Sie weg wechseln, helfen Ihnen die oben besprochenen [weitergegebenen Metadaten](#service-assigned-metadata) beim Überspringen von Ereignissen, die bereits behandelt wurden, und beim Fortsetzen genau an der Stelle, wo Sie zuletzt aufgehört haben.

## <a name="merge"></a>Zusammenführen

Beim Zusammenführungsmuster zeigen eine oder mehrere Replikationsaufgaben auf ein Ziel, möglicherweise gleichzeitig mit regulären Producern, die ebenfalls Ereignisse an dasselbe Ziel senden.

Es folgen Variationen dieser Muster:

- Zwei oder mehr Replikationsfunktionen, die gleichzeitig Ereignisse aus verschiedenen Quellen erfassen und an das gleiche Ziel senden.
- Eine weitere Replikationsfunktion, die Ereignisse einer Quelle erfasst, während das Ziel auch direkt von Producern verwendet wird.
- Das vorherige Muster, jedoch gespiegelt zwischen zwei oder mehr Event Hub-Instanzen, was dazu führt, dass diese Event Hub-Instanzen die gleichen Datenströme unabhängig davon enthalten, wo Ereignisse produziert werden.

Die ersten beiden Mustervarianten sind eher einfach und unterscheiden sich nicht von gewöhnlichen Replikationsaufgaben.

Das letzte Szenario erfordert das Ausschließen bereits replizierter Ereignisse von einer erneuten Replikation. Die Vorgehensweise wird im Beispiel [EventHubToEventHubMerge](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/code/EventHubMerge) veranschaulicht und erläutert.

## <a name="editor"></a>Editor

Das Editor-Muster baut auf dem Muster [Replikation](#replication) auf, wobei Nachrichten jedoch geändert werden, ehe sie weitergeleitet werden. 

Beispiele für solche Änderungen:

- **_Transcodierung_**: Wenn der Inhalt des Ereignisses (auch als „Textkörper“ oder „Nutzdaten“ bezeichnet) von der Quelle im _Apache Avro_-Format oder einem anderen proprietären Serialisierungsformat codiert eingeht, das Zielsystem aber erwartet, dass der Inhalt mit _JSON_ codiert ist, werden mithilfe einer Replikationsaufgabe für die Transcodierung zunächst die Nutzdaten von _Apache Avro_ in einen In-Memory-Objektgraphen deserialisiert. Anschließend wird dieser Graph in das _JSON_-Format für das Ereignis serialisiert, das weitergeleitet wird. Die Transcodierung umfasst auch Aufgaben zur **Komprimierung** und Dekomprimierung des Inhalts.
- **_Transformation_**: Ereignisse mit strukturierten Daten müssen möglicherweise umgestaltet werden, damit sie von nachgeschalteten Consumern einfacher verarbeitet werden können. Dies kann Aufgaben wie das Abflachen geschachtelter Strukturen, das Ausschneiden überflüssiger Datenelemente oder das Anpassen der Nutzdaten an ein bestimmtes Schema bedeuten.
- **_Batchverarbeitung_**: Ereignisse können in Batches (mehrere Ereignisse in einer einzelnen Übertragung) von einer Quelle empfangen werden, müssen jedoch einzeln an ein Ziel weitergeleitet werden (oder umgekehrt). Eine Aufgabe kann daher mehrere Ereignisse auf Grundlage einer einzigen Eingangsereignisübertragung weiterleiten oder eine Reihe von Ereignissen aggregieren, die dann gemeinsam übertragen werden.
- **_Validierung_**: Ereignisdaten aus externen Quellen müssen oft daraufhin überprüft werden, ob sie mit einer Reihe von Regeln übereinstimmen, ehe sie weitergeleitet werden dürfen. Die Regeln können mithilfe von Schemas oder Code ausgedrückt werden. Nicht konforme Ereignisse können verworfen werden, wobei das Problem in den Protokollen vermerkt wird, oder an ein spezielles Ziel weitergeleitet werden, um sie weiter zu behandeln.
- **_Anreicherung_**: Aus bestimmten Quellen stammende Ereignisdaten müssen möglicherweise mit weiterem Kontext angereichert werden, damit sie in Zielsystemen genutzt werden können. Dies kann das Nachschlagen von Referenzdaten und Einbetten dieser Daten in das Ereignis oder das Hinzufügen von Informationen zur Quelle umfassen, die der Replikationsaufgabe zwar bekannt, aber nicht in den Ereignissen enthalten sind.
- **_Filterung_**: Bestimmte aus einer Quelle eingehende Ereignisse müssen dem Ziel möglicherweise aufgrund einer Regel vorenthalten werden. Ein Filter gleicht das Ereignis mit einer Regel ab und verwirft das Ereignis, wenn es nicht mit der Regel übereinstimmt. Eine Form der Filterung ist das Herausfiltern duplizierter Ereignisse, indem bestimmte Kriterien berücksichtigt werden und nachfolgende Ereignisse mit den gleichen Werten verworfen werden.
- **_Kryptografie_**: Eine Replikationsaufgabe muss möglicherweise von der Quelle eingehende Inhalte entschlüsseln und/oder an ein Ziel weitergeleitete Inhalte verschlüsseln und/oder die Integrität von Inhalten und Metadaten in Bezug auf eine im Ereignis mitgeführte Signatur überprüfen oder eine solche Signatur anfügen.
- **_Nachweis_**: Eine Replikationsaufgabe kann Metadaten, die möglicherweise durch eine digitale Signatur geschützt sind, an ein Ereignis anfügen. Damit wird nachgewiesen, dass das Ereignis über einen bestimmten Kanal oder zu einem bestimmten Zeitpunkt empfangen wurde.
- **_Verkettung_**: Eine Replikationsaufgabe kann Signaturen auf Ereignisdatenströme anwenden, sodass die Integrität des Datenstroms geschützt ist und fehlende Ereignisse erkannt werden können.

Die Transformations-, Batch- und Anreicherungsmuster werden im Allgemeinen am besten mithilfe von [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md)-Aufträgen implementiert. 

Alle diese Muster können mit Azure Functions implementiert werden, wobei der [Event Hubs-Auslöser](../azure-functions/functions-bindings-event-hubs-trigger.md) für das Erfassen von Ereignissen und die [Event Hub-Ausgabebindung](../azure-functions/functions-bindings-event-hubs-output.md) für deren Zustellung verwendet wird.

## <a name="routing"></a>Routing

Das Routingmuster baut auf dem Muster [Replikation](#replication) auf, aber statt einer Quelle und einem Ziel hat die Replikationsaufgabe mehrere Ziele, wie hier in C# dargestellt:

```csharp
[FunctionName("EH2EH")]
public static async Task Run(
    [EventHubTrigger("source", Connection = "EventHubConnectionAppSetting")] EventData[] events,
    [EventHub("dest1", Connection = "EventHubConnectionAppSetting")] EventHubClient output1,
    [EventHub("dest2", Connection = "EventHubConnectionAppSetting")] EventHubClient output2,
    ILogger log)
{
    foreach (EventData eventData in events)
    {
        // send to output1 and/or output2 based on criteria
        EventHubReplicationTasks.ConditionalForwardToEventHub(input, output1, log, (eventData) => {
            return ( inputEvent.SystemProperties.SequenceNumber%2==0 ) ? inputEvent : null;
        });
        EventHubReplicationTasks.ConditionalForwardToEventHub(input, output2, log, (eventData) => {
            return ( inputEvent.SystemProperties.SequenceNumber%2!=0 ) ? inputEvent : null;
        });
    }
}
```

Die Routingfunktion berücksichtigt die Metadaten der Nachricht und/oder deren Nutzdaten und wählt dann für den Sendevorgang eines der verfügbaren Ziele aus.

In Azure Stream Analytics können Sie dasselbe erreichen, indem Sie mehrere Ausgaben definieren und dann eine Abfrage pro Ausgabe ausführen.

```sql
select * into dest1Output from inputSource where Info = 1
select * into dest2Output from inputSource where Info = 2
```


## <a name="log-projection"></a>Protokollprojektion

Das Protokollprojektionsmuster flacht den Ereignisdatenstrom auf eine indizierte Datenbank ab, wobei Ereignisse zu Datensätzen in der Datenbank werden. In der Regel werden Ereignisse derselben Sammlung oder Tabelle hinzugefügt, und der Event Hub-Partitionsschlüssel wird Teil des Primärschlüssels, um den Datensatz eindeutig zu gestalten.

Die Protokollprojektion kann einen Zeitreihenverlauf Ihrer Ereignisdaten oder eine verdichtete Ansicht generieren, wobei nur das letzte Ereignis für jeden Partitionsschlüssel beibehalten wird. Die Form der Zieldatenbank hängt letztlich von Ihnen und den Anforderungen Ihrer Anwendung ab. Dieses Muster wird auch als „Ereignissourcing“ bezeichnet.

> [!TIP]
> In Azure Stream Analytics können Sie in [Azure SQL-Datenbank](../stream-analytics/sql-database-output.md) und [Azure Cosmos DB](../stream-analytics/azure-cosmos-db-output.md) problemlos Protokollprojektionen erstellen und sollten diese Option bevorzugen.

Die folgende Azure-Funktion projiziert den Inhalt einer Event Hub-Instanz verdichtet in eine Azure Cosmos DB-Sammlung.

```C#
[FunctionName("Eh1ToCosmosDb1Json")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static async Task Eh1ToCosmosDb1Json(
    [EventHubTrigger("eh1", ConsumerGroup = "Eh1ToCosmosDb1", Connection = "Eh1ToCosmosDb1-source-connection")] EventData[] input,
    [CosmosDB(databaseName: "SampleDb", collectionName: "foo", ConnectionStringSetting = "CosmosDBConnection")] IAsyncCollector<object> output,
    ILogger log)
{
    foreach (var ev in input)
    {
        if (!string.IsNullOrEmpty(ev.SystemProperties.PartitionKey))
        {
            var record = new
            {
                id = ev.SystemProperties.PartitionKey,
                data = JsonDocument.Parse(ev.Body),
                properties = ev.Properties
            };
            await output.AddAsync(record);
        }
    }
}
```

## <a name="next-steps"></a>Nächste Schritte

- [Ereignisreplikatoranwendungen in Azure Functions][1]
- [Replizieren von Ereignissen zwischen Event Hub-Instanzen][2]
- [Replizieren von Ereignissen in Azure Service Bus][3]

[1]: event-hubs-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus
