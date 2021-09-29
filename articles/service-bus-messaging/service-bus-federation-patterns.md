---
title: 'Muster für Nachrichtenreplikationsaufgaben: Azure Service Bus | Microsoft-Dokumentation'
description: Dieser Artikel enthält eine ausführliche Anleitung zur Implementierung spezifischer Muster für Nachrichtenreplikationsaufgaben.
ms.topic: article
ms.date: 09/28/2021
ms.openlocfilehash: 4effcb9f51532cb2ef87b18b264789c526b57585
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129211789"
---
# <a name="message-replication-tasks-patterns"></a>Muster für Nachrichtenreplikationsaufgaben

Unter [Übersicht über Verbund](service-bus-federation-overview.md) und [Übersicht über Replikatorfunktionen](service-bus-federation-replicator-functions.md) werden die Gründe für die Nutzung von Replikationsaufgaben und deren wesentliche Bestandteile beschrieben. Wir empfehlen Ihnen, sich hiermit vertraut zu machen, bevor Sie mit diesem Artikel fortfahren.

In diesem Artikel finden Sie eine ausführliche Anleitung zur Implementierung einiger der in der Übersicht erwähnten Muster. 

## <a name="replication"></a>Replikation 

Beim Replikationsmuster werden Nachrichten zwischen Warteschlangen oder Themen oder aus einer Warteschlange bzw. einem Thema an ein anderes Ziel, z. B. einen Event Hub, kopiert. Die Nachrichten werden weitergeleitet, ohne dass Änderungen an der Nachrichtennutzlast vorgenommen werden. 

Die Implementierung dieses Musters wird im Beispiel für die [Nachrichtenreplikation für Azure Service Bus](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy) veranschaulicht.

### <a name="sequences-and-order-preservation"></a>Sequenzen und Beibehaltung der Reihenfolge

Das Ziel des Replikationsmodells besteht nicht darin, die absolute Reihenfolge einer Quellwarteschlange oder eines Quellthemas für eine Zielwarteschlange oder ein Zielthema beizubehalten. Stattdessen wird bei Bedarf darauf geachtet, dass die relative Reihenfolge der Nachrichten beibehalten wird, sofern dies für die Anwendung erforderlich ist. Die Anwendung ermöglicht dies, indem die Sitzungsunterstützung für die Quellentität aktiviert wird und verwandte Nachrichten unter demselben [Sitzungsschlüssel](message-sessions.md) gruppiert werden.

Mit den sitzungsabhängigen vordefinierten Replikationsfunktionen wird sichergestellt, dass Nachrichtensequenzen mit der gleichen Sitzungs-ID aus einer Quellentität als Batch an die Zielwarteschlange oder das Zielthema übermittelt werden, indem die ursprüngliche Sequenz und die gleiche Sitzungs-ID verwendet werden. 

### <a name="service-assigned-metadata"></a>Vom Dienst zugewiesene Metadaten 

Die vom Dienst zugewiesenen Metadaten einer Nachricht, die aus der Quellwarteschlange oder dem Quellthema abgerufen wurden, der Zeitpunkt der ursprünglichen Einreihung in die Warteschlange und die Sequenznummer werden in der Zielwarteschlange bzw. im Zielthema durch neue vom Dienst zugewiesene Werte ersetzt. Mit den Standardaufgaben für die Replikation, die in unseren Beispielen enthalten sind, werden die ursprünglichen Werte in den Benutzereigenschaften aber beibehalten: `repl-enqueue-time` (ISO8601-Zeichenfolge) und `repl-sequence`.

Diese Eigenschaften haben den Typ „string“ und enthalten den Zeichenfolgenwert der entsprechenden ursprünglichen Eigenschaften.  Wenn die Meldung mehrfach weitergeleitet wird, werden die vom Dienst zugewiesenen Metadaten der unmittelbaren Quelle an die bereits vorhandenen Eigenschaften angefügt, wobei die Werte per Semikolon getrennt sind. 

### <a name="failover"></a>Failover

Wenn Sie die Replikation für die Notfallwiederherstellung, als Schutz bei Nachrichten zur regionalen Verfügbarkeit im Service Bus-Dienst oder als Schutz vor Netzwerkunterbrechungen einsetzen, muss in einem solchen Ausfallszenario ein Failover von einer Warteschlange bzw. einem Thema zum nächsten erfolgen, wobei Producer und/oder Consumer angewiesen werden, den sekundären Endpunkt zu verwenden.

Für alle Failoverszenarien wird davon ausgegangen, dass die erforderlichen Elemente der Namespaces strukturell identisch sind. Dies bedeutet, dass Warteschlangen und Themen identisch benannt und Regeln für SAS (Shared Access Signature) und/oder die rollenbasierte Zugriffssteuerung auf die gleiche Weise eingerichtet sind. Sie können einen sekundären Namespace erstellen (und aktualisieren), indem Sie die [Anleitung zum Verschieben von Namespaces](move-across-regions.md) befolgen und den Bereinigungsschritt auslassen.

Um Producer und Consumer zum Umstieg zu zwingen, müssen Sie die Informationen darüber, welcher Namespace verwendet werden soll, an einem leicht zu erreichenden und zu aktualisierenden Ort zum Lookup bereitstellen. Wenn Producer oder Consumer auf häufige oder anhaltende Fehler stoßen, sollten sie diesen Ort konsultieren und ihre Konfiguration anpassen. Es gibt zahlreiche Möglichkeiten zur Freigabe dieser Konfiguration. Nachstehend stellen wir zwei davon vor: DNS und Dateifreigaben.

#### <a name="dns-based-failover-configuration"></a>Auf DNS basierende Failoverkonfiguration

Ein möglicher Ansatz besteht darin, die Informationen in DNS SRV-Einträgen in einem von Ihnen kontrollierten DNS vorzuhalten und auf die entsprechenden Warteschlangen- oder Themenendpunkte zu verweisen. Beachten Sie, dass Nachrichtenhubs nicht zulassen, dass ihre Endpunkte direkt mit CNAME-Einträgen als Alias versehen werden. Dies bedeutet, dass Sie DNS als resilienten Lookupmechanismus für Endpunktadressen nutzen können, aber nicht zur direkten Auflösung von IP-Adressinformationen. 

Angenommen, Sie besitzen die Domäne `example.com` und für Ihre Anwendung die Zone `test.example.com`. Für zwei alternative Service Bus-Instanzen erstellen Sie nun zwei weitere geschachtelte Zonen mit je einem SRV-Eintrag. 

Die SRV-Einträge haben der üblichen Konvention folgend das Präfix `_azure_servicebus._amqp` und enthalten zwei Endpunkteinträge: Einen für AMQP-over-TLS an Port 5671 und einen für AMQP-over-WebSockets an Port 443, die beide auf den Service Bus-Endpunkt des der Zone entsprechenden Namespace verweisen.

| Zone                 | SRV-Eintrag
|----------------------|-------------------------------------------------------------
| `sb1.test.example.com` | **`_azure_servicebus._amqp.sb1.test.example.com`**<br>`1 1 5671 sb1-test-example-com.servicebus.windows.net`<br>`2 2 443 sb1-test-example-com.servicebus.windows.net`
| `sb2.test.example.com` | **`_azure_servicebus._amqp.sb1.test.example.com`**<br>`1 1 5671 sb2-test-example-com.servicebus.windows.net`<br>`2 2 443 sb2-test-example-com.servicebus.windows.net`

In der Zone Ihrer Anwendung erstellen Sie dann einen CNAME-Eintrag mit einem Verweis auf die untergeordnete Zone, die Ihrer primären Warteschlange bzw. Ihrem Thema entspricht:

| CNAME-Eintrag                 | Alias
|------------------------------|-------------------------------------------------------------
| `servicebus.test.example.com`  | `sb1.test.example.com`

Mit einem DNS-Client, der das Abfragen von CNAME- und SRV-Einträgen explizit zulässt (die integrierten Clients von Java und .NET lassen nur die einfache Auflösung von Namen in IP-Adressen zu), können Sie dann den gewünschten Endpunkt auflösen. Bei [DnsClient.NET](https://dnsclient.michaco.net/) ist die Lookupfunktion beispielsweise wie folgt:

``` C#
static string GetServiceBusName(string aliasName)
{
    const string SrvRecordPrefix = "_azure_servicebus._amqp.";
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

Dieses Verfahren ähnelt der Funktionsweise der [georedundanten Notfallwiederherstellung von Service Bus](service-bus-geo-dr.md), aber Sie haben die vollständige Kontrolle darüber. Dies funktioniert auch mit Aktiv/Aktiv-Szenarien.

#### <a name="file-share-based-failover-configuration"></a>Auf Dateifreigaben basierende Failoverkonfiguration

Die einfachste Alternative zu DNS für die Freigabe von Endpunktinformationen ist, den Namen des primären Endpunkts in eine Klartextdatei einzugeben und die Datei über eine Infrastruktur bereitzustellen, die resistent gegen Ausfälle ist und dennoch Aktualisierungen erlaubt. 

Falls Sie bereits eine hoch verfügbare Infrastruktur für Websites mit globaler Verfügbarkeit und Inhaltsreplikation betreiben, können Sie dieser eine solche Datei hinzufügen und dann erneut veröffentlichen, wenn ein Umstieg erforderlich ist.

## <a name="merge"></a>Merge

Beim Zusammenführungsmuster verweisen eine oder mehrere Replikationsaufgaben auf ein Ziel. Dies erfolgt möglicherweise gleichzeitig mit regulären Producern, die ebenfalls Nachrichten an dasselbe Ziel senden. 

Varianten dieses Musters:
- Zwei oder mehr Replikationsfunktionen, die gleichzeitig Nachrichten aus verschiedenen Quellen erfassen und an dasselbe Ziel senden.
- Eine weitere Replikationsfunktion, die Nachrichten einer Quelle erfasst, während das Ziel auch direkt von Producern verwendet wird. 
- Vorheriges Muster, aber Nachrichten werden zwischen zwei oder mehr Themen gespiegelt. Dies führt dazu, dass diese Themen unabhängig davon, wo Nachrichten produziert werden, die gleichen Nachrichten enthalten.

Die ersten beiden Mustervarianten sind eher einfach und unterscheiden sich nicht von gewöhnlichen Replikationsaufgaben.

Das letzte Szenario erfordert das Ausschließen bereits replizierter Nachrichten aus einer erneuten Replikation. Dieses Verfahren wird anhand des Aktiv/Aktiv-Beispiels veranschaulicht und beschrieben.

## <a name="editor"></a>Editor

Das Editor-Muster baut auf dem Muster [Replikation](#replication) auf, wobei Nachrichten jedoch geändert werden, ehe sie weitergeleitet werden. Beispiele für solche Änderungen:

- ***Transcodierung:*** Wenn der Inhalt der Nachricht (auch als „Text“ oder „Nutzdaten“ bezeichnet) von der Quelle im *Apache Avro*-Format oder mit Codierung in einem anderen proprietären Serialisierungsformat eingeht, das Zielsystem aber erwartet, dass der Inhalt mit *JSON* codiert ist, werden mithilfe einer Replikationsaufgabe für die Transcodierung zunächst die Nutzdaten von *Apache Avro* in einen In-Memory-Objektgraphen deserialisiert. Anschließend wird dieser Graph in das *JSON*-Format für die weiterzuleitende Nachricht serialisiert. Die Transcodierung umfasst auch Aufgaben zur **Komprimierung** und Dekomprimierung des Inhalts.
- ***Transformation:*** Nachrichten mit strukturierten Daten müssen möglicherweise umgestaltet werden, damit sie von nachgeschalteten Consumern leichter verarbeitet werden können. Dies kann Aufgaben wie das Abflachen geschachtelter Strukturen, das Ausschneiden überflüssiger Datenelemente oder das Anpassen der Nutzdaten an ein bestimmtes Schema bedeuten.
- ***Batchverarbeitung***: Nachrichten können in Batches (mehrere Nachrichten in einer einzelnen Übertragung) von einer Quelle empfangen werden, müssen aber einzeln an ein Ziel weitergeleitet werden (oder umgekehrt). Eine Aufgabe kann daher mehrere Nachrichten basierend auf nur einer Übertragung einer Eingangsnachricht weiterleiten oder eine Reihe von Nachrichten aggregieren, die dann gemeinsam übertragen werden. 
- ***Validierung***: Nachrichtendaten aus externen Quellen müssen oft daraufhin überprüft werden, ob sie mit einer Reihe von Regeln übereinstimmen, ehe sie weitergeleitet werden dürfen. Die Regeln können mithilfe von Schemas oder Code ausgedrückt werden. Nicht konforme Nachrichten können verworfen werden, wobei das Problem in den Protokollen vermerkt wird, oder zur weiteren Behandlung an ein spezielles Ziel weitergeleitet werden.   
- ***Anreicherung***: Aus bestimmten Quellen stammende Nachrichtendaten müssen möglicherweise mit weiterem Kontext angereichert werden, damit sie in Zielsystemen verwendet werden können. Hierfür kann Folgendes erforderlich sein: das Nachschlagen von Referenzdaten und Einbetten dieser Daten in die Nachricht oder das Hinzufügen von Informationen zur Quelle, die der Replikationsaufgabe bekannt sind, aber nicht in den Nachrichten enthalten sind. 
- ***Filterung***: Bestimmte aus einer Quelle eingehende Nachrichten müssen dem Ziel möglicherweise aufgrund einer Regel vorenthalten werden. Ein Filter testet die Nachricht anhand einer Regel und verwirft die Nachricht, falls sie nicht mit der Regel übereinstimmt. Eine Form der Filterung ist das Herausfiltern duplizierter Nachrichten, indem bestimmte Kriterien berücksichtigt werden und nachfolgende Nachrichten mit den gleichen Werten verworfen werden.
- ***Routing und Partitionierung***: Bei einigen Replikationsaufgaben sind ggf. zwei oder mehr alternative Ziele zulässig. Basierend auf den Metadaten oder dem Inhalt der Nachricht werden Regeln dazu definiert, welches Replikationsziel für eine bestimmte Nachricht ausgewählt wird. Eine spezielle Form des Routings ist die Partitionierung. Hierbei werden Partitionen von der Aufgabe anhand von Regeln explizit einem Replikationsziel zugewiesen.
- ***Kryptografie***: Eine Replikationsaufgabe muss möglicherweise von der Quelle eingehende Inhalte entschlüsseln bzw. an ein Ziel weitergeleitete Inhalte verschlüsseln und/oder die Integrität von Inhalten und Metadaten in Bezug auf eine in der Nachricht übertragene Signatur überprüfen oder eine solche Signatur anfügen. 
- ***Nachweis***: Eine Replikationsaufgabe kann Metadaten, die möglicherweise durch eine digitale Signatur geschützt sind, an eine Nachricht anfügen. Hiermit wird nachgewiesen, dass die Nachricht über einen bestimmten Kanal oder zu einem bestimmten Zeitpunkt empfangen wurde.     
- ***Verkettung:*** Von einer Replikationsaufgabe können Signaturen so auf Nachrichtensequenzen angewendet werden, dass die Integrität der Sequenz geschützt wird und fehlende Nachrichten erkannt werden können.

Alle diese Muster können mit Azure Functions implementiert werden, wobei der [Trigger für Nachrichtenhubs](../azure-functions/functions-bindings-service-bus-trigger.md) für das Erfassen von Nachrichten und die [Ausgabebindung für Warteschlangen/Themen](../azure-functions/functions-bindings-service-bus-output.md) für deren Zustellung verwendet wird. 

## <a name="routing"></a>Routing

Das Routingmuster baut auf dem Muster [Replikation](#replication) auf, aber statt einer Quelle und einem Ziel hat die Replikationsaufgabe mehrere Ziele, wie hier in C# dargestellt:

``` csharp
[FunctionName("SBRouter")]
public static async Task Run(
    [ServiceBusTrigger("source", Connection = "serviceBusConnectionAppSetting")] Message[] messages,
    [ServiceBus("dest1", Connection = "serviceBusConnectionAppSetting")] QueueClient output1,
    [ServiceBus("dest2", Connection = "serviceBusConnectionAppSetting")] QueueClient output2,
    ILogger log)
{
    foreach (Message messageData in messages)
    {
        // send to output1 or output2 based on criteria 
    }
}
```

Die Routingfunktion berücksichtigt die Metadaten der Nachricht und/oder deren Nutzdaten und wählt dann eines der verfügbaren Ziele für den Sendevorgang aus.

## <a name="next-steps"></a>Nächste Schritte

- [Anwendungen für die Nachrichtenreplikation in Azure Functions][1]
- [Replizieren von Nachrichten zwischen Service Bus-Instanzen][2]
- [Replizieren von Nachrichten in Azure Event Hubs][3]

[1]: service-bus-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopyToEventHub
