---
title: Erstellen von Azure Service Bus-Warteschlangen und -Themen | Microsoft-Dokumentation
description: Es wird beschrieben, wie Service Bus-Warteschlangen und -Themen mit mehreren Nachrichtenbrokern partitioniert werden.
ms.topic: article
ms.date: 09/21/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: b6e0c0473ea5bf50ed64bf7abee2c66ab5789840
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128616078"
---
# <a name="partitioned-queues-and-topics"></a>Partitionierte Warteschlangen und Themen

Für Azure Service Bus werden mehrere Nachrichtenbroker verwendet, um Nachrichten zu verarbeiten, sowie mehrere Nachrichtenspeicher, um Nachrichten zu speichern. Eine herkömmliche Warteschlange oder ein Thema werden von einem einzelnen Nachrichtenbroker verarbeitet und in einem Nachrichtenspeicher gespeichert. Service Bus-*Partitionen* ermöglichen das Partitionieren von Warteschlangen und Themen oder *Nachrichtenentitäten* über mehrere Nachrichtenbroker und -speicher. Eine Partitionierung bedeutet, dass der Gesamtdurchsatz einer partitionierten Entität nicht mehr durch die Leistung eines einzelnen Nachrichtenbrokers oder Nachrichtenspeichers beschränkt wird. Außerdem führt ein vorübergehender Ausfall eines Nachrichtenspeichers nicht dazu, dass eine partitionierte Warteschlange oder ein Thema nicht verfügbar ist. Partitionierte Warteschlangen und Themen können alle erweiterten Service Bus-Features enthalten, z. B. die Unterstützung von Transaktionen und Sitzungen.

> [!NOTE]
> Die Partitionierung ist bei der Erstellung der Entität für alle Warteschlangen und Themen in den SKUs „Basic“ oder „Standard“ verfügbar. Für die Premium Messaging-SKU ist sie nicht mehr verfügbar, aber alle bereits vorhandenen partitionierten Entitäten aus der Zeit, als sie in Premium-Namespaces unterstützt wurden, funktionieren weiterhin wie erwartet.
 
Es ist nicht möglich, die Partitionierungsoption für eine vorhandene Warteschlange oder ein Thema zu ändern. Sie können die Option nur bei der Erstellung der Entität festlegen.

## <a name="how-it-works"></a>Funktionsweise

Jede partitionierte Warteschlange bzw. jedes Thema besteht aus mehreren Partitionen. Jede Partition wird in einem anderen Nachrichtenspeicher gespeichert und von einem anderen Nachrichtenbroker verarbeitet. Wenn eine Nachricht an eine partitionierte Warteschlange bzw. ein partitioniertes Thema gesendet wird, weist Service Bus die Nachricht einem der Partitionen zu. Service Bus nimmt die Auswahl willkürlich oder mithilfe eines Partitionsschlüssels vor, den der Absender angeben kann.

Wenn ein Client eine Nachricht von einer partitionierten Warteschlange oder von einem Abonnement eines partitionierten Themas empfangen möchte, fragt Service Bus alle Partitionen auf Nachrichten ab. Anschließend wird die erste Nachricht zurückgegeben, die von einem der Nachrichtenspeicher an den Empfänger gesendet wird. Service Bus speichert die anderen Nachrichten zwischen und gibt sie zurück, wenn mehr Empfangsanforderungen eingehen. Ein empfangender Client ist sich der Partitionierung nicht bewusst. Das Verhalten einer partitionierten Warteschlange oder eines Themas (z. B. „read“, „complete“, „defer“, „deadletter“, „prefetching“) dem Client gegenüber ist mit dem Verhalten einer normalen Entität identisch.

Der Peekvorgang auf einer nicht partitionierten Entität gibt immer die älteste Nachricht zurück. Dies ist jedoch auf einer partitionierten Entität nicht der Fall. Stattdessen wird hier die älteste Nachricht in einer der Partitionen zurückgegeben, deren Nachrichtenbroker zuerst reagiert hat. Es gibt keine Garantie, dass es sich bei der zurückgegebenen Nachricht um die älteste auf allen Partitionen handelt. 

Es fallen keine zusätzlichen Kosten an, wenn eine Nachricht an eine partitionierte Warteschlange oder ein Thema gesendet oder von dort empfangen wird.

> [!NOTE]
> Der Peekvorgang gibt die älteste Nachricht aus der Partition basierend auf ihrer Sequenznummer zurück. Bei partitionierten Entitäten wird die Sequenznummer relativ zur Partition ausgegeben. Weitere Informationen finden Sie unter [Nachrichtensequenzierung und Zeitstempel](../service-bus-messaging/message-sequencing.md).

## <a name="use-of-partition-keys"></a>Verwenden von Partitionsschlüsseln

Wenn eine Nachricht in eine partitionierte Warteschlange oder ein Thema eingereiht wird, wird von Service Bus das Vorhandensein eines Partitionsschlüssels überprüft. Wenn ein Schlüssel gefunden wird, wird die Partition basierend auf diesem Schlüssel ausgewählt. Falls kein Partitionsschlüssel gefunden wird, wird die Partition basierend auf einem internen Algorithmus ausgewählt.

### <a name="using-a-partition-key"></a>Verwendung eines Partitionsschlüssels

Einige Szenarien, z. B. Sitzungen oder Transaktionen, erfordern es, dass Nachrichten in einer bestimmten Partition gespeichert werden. Für all diese Szenarien ist die Verwendung eines Partitionsschlüssels erforderlich. Alle Nachrichten, für die der gleiche Partitionsschlüssel verwendet wird, sind derselben Partition zugewiesen. Wenn die Partition vorübergehend nicht verfügbar ist, wird von Service Bus ein Fehler zurückgegeben.

Je nach Szenario werden verschiedene Nachrichteneigenschaften als Partitionsschlüssel verwendet:

**SessionId**: Wenn für eine Nachricht die Eigenschaft „Sitzungs-ID“ festgelegt ist, verwendet Service Bus sie als Partitionsschlüssel. Auf diese Weise werden alle Nachrichten, die zu derselben Sitzung gehören, von demselben Nachrichtenbroker verarbeitet. Durch Sitzungen kann Service Bus sowohl die Nachrichtensortierung als auch die Einheitlichkeit von Sitzungszuständen garantieren.

**PartitionKey**: Wenn für eine Nachricht die Partitionsschlüssel-Eigenschaft festgelegt ist, aber nicht die Sitzungs-ID-Eigenschaft, verwendet Service Bus den Partitionsschlüssel-Eigenschaftswert als Partitionsschlüssel. Wenn für die Nachricht sowohl die Sitzungs-ID- als auch die Partitionsschlüssel-Eigenschaft festgelegt ist, müssen beide Eigenschaften identisch sein. Falls die Partitionsschlüssel-Eigenschaft auf einen anderen Wert als die Sitzungs-ID-Eigenschaft festgelegt ist, gibt Service Bus eine Ausnahme für einen ungültigen Vorgang zurück. Die PartitionKey-Eigenschaft sollte verwendet werden, wenn ein Absender nicht sitzungsorientierte Transaktionsnachrichten sendet. Mit dem Partitionsschlüssel wird sichergestellt, dass alle Nachrichten, die innerhalb einer Transaktion gesendet werden, von demselben Nachrichtenbroker verarbeitet werden.

**MessageId:** Wenn die Warteschlange oder das Thema mit der [Duplikaterkennungsfunktion](duplicate-detection.md) erstellt wurde und die Eigenschaften der Sitzungs-ID oder des Partitionsschlüssels nicht festgelegt wurden, wird der Wert der Nachrichten-ID-Eigenschaft als Partitionsschlüssel verwendet. (Die Microsoft Client-Bibliotheken weisen automatisch eine Nachrichten-ID zu, wenn dies von der sendenden Anwendung nicht übernommen wird.) In diesem Fall werden alle Kopien einer Nachricht von demselben Nachrichtenbroker verarbeitet. Mit dieser ID kann Service Bus doppelte Nachrichten erkennen und verwerfen. Wenn das Feature zur Duplikaterkennung nicht aktiviert ist, betrachtet Service Bus die Nachrichten-ID-Eigenschaft nicht als Partitionsschlüssel.

### <a name="not-using-a-partition-key"></a>Keine Verwendung eines Partitionsschlüssels

Wenn kein Partitionsschlüssel vorhanden ist, verteilt Service Bus Nachrichten nach einem Roundrobinverfahren auf alle Partitionen der partitionierten Warteschlange bzw. des partitionierten Themas. Wenn die ausgewählte Partition nicht verfügbar ist, weist Service Bus die Nachricht einer anderen Partition zu. Auf diese Weise kann der Sendevorgang erfolgreich durchgeführt werden, obwohl ein Nachrichtenspeicher vorübergehend nicht verfügbar ist. Sie erzielen jedoch nicht die garantierte Reihenfolge, die ein Partitionsschlüssel liefert.

Eine ausführlichere Erläuterung des Kompromisses zwischen Verfügbarkeit (kein Partitionsschlüssel) und Konsistenz (Verwendung eines Partitionsschlüssels) finden Sie unter [Verfügbarkeit und Konsistenz in Event Hubs](../event-hubs/event-hubs-availability-and-consistency.md). Mit Ausnahme der Partitions-ID, die nicht für Benutzer verfügbar gemacht wird, gelten diese Informationen gleichermaßen für partitionierte Service Bus-Entitäten.

Um Service Bus ausreichend Zeit zum Einreihen der Nachricht in eine andere Partition zu geben, muss der Timeout-Wert des Clients, der die Nachricht sendet, größer als 15 Sekunden sein. Der Standardwert von 60 Sekunden wird empfohlen.

Ein Partitionsschlüssel „heftet“ eine Nachricht an eine bestimmte Partition an. Wenn der Nachrichtenspeicher, der diese Partition enthält, nicht verfügbar ist, wird von Service Bus ein Fehler zurückgegeben. Wenn kein Partitionsschlüssel vorhanden ist, kann Service Bus eine andere Partition auswählen, damit der Vorgang erfolgreich ist. Aus diesem Grund wird empfohlen, nur dann einen Partitionsschlüssel anzugeben, wenn dies erforderlich ist.

## <a name="advanced-topics"></a>Weiterführende Themen

### <a name="use-transactions-with-partitioned-entities"></a>Verwenden von Transaktionen mit partitionierten Entitäten

Nachrichten, die als Teil einer Transaktion gesendet werden, müssen einen Partitionsschlüssel angeben. Beim Schlüssel kann es sich um eine der folgenden Eigenschaften handeln: Sitzungs-ID, Partitionsschlüssel oder Nachrichten-ID. Für alle Nachrichten, die als Teil derselben Transaktion gesendet werden, muss derselbe Partitionsschlüssel angegeben werden. Wenn Sie versuchen, eine Nachricht innerhalb einer Transaktion ohne Partitionsschlüssel zu senden, wird von Service Bus eine Ausnahme für einen ungültigen Vorgang zurückgegeben. Wenn Sie versuchen, mehrere Nachrichten mit verschiedenen Partitionsschlüsseln innerhalb derselben Transaktion zu senden, gibt Service Bus eine Ausnahme für einen ungültigen Vorgang zurück. Beispiel:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    ServiceBusMessage msg = new ServiceBusMessage("This is a message");
    msg.PartitionKey = "myPartitionKey";
    await sender.SendMessageAsync(msg); 
    await ts.CompleteAsync();
}
committableTransaction.Commit();
```

Wenn Eigenschaften, die als Partitionsschlüssel dienen, festgelegt werden, heftet Service Bus die Nachricht an eine bestimmte Partition an. Dieses Verhalten tritt unabhängig davon auf, ob eine Transaktion verwendet wird. Es wird empfohlen, keinen Partitionsschlüssel anzugeben, wenn dies nicht erforderlich ist.

### <a name="use-transactions-in-sessions-with-partitioned-entities"></a>Verwenden von Transaktionen in Sitzungen mit partitionierten Entitäten

Um eine Transaktionsnachricht an ein sitzungsorientiertes Thema bzw. eine sitzungsorientierte Warteschlange zu senden, muss für die Nachricht die Sitzungs-ID-Eigenschaft festgelegt sein. Wenn die Partitionsschlüssel-Eigenschaft ebenfalls angegeben wird, muss diese mit der Sitzungs-ID-Eigenschaft identisch sein. Falls sie sich unterscheiden, gibt Service Bus eine Ausnahme für einen ungültigen Vorgang aus.

Im Gegensatz zu normalen (nicht partitionierten) Warteschlangen oder Themen ist es nicht möglich, eine einzelne Transaktion zu verwenden, um mehrere Nachrichten an verschiedene Sitzungen zu senden. Bei einem entsprechenden Versuch gibt Service Bus eine Ausnahme für einen ungültigen Vorgang aus. Beispiel:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    ServiceBusMessage msg = new ServiceBusMessage("This is a message");
    msg.SessionId = "mySession";
    await sender.SendMessageAsync(msg); 
    await ts.CompleteAsync();
}
committableTransaction.Commit();
```

### <a name="automatic-message-forwarding-with-partitioned-entities"></a>Automatische Nachrichtenweiterleitung mit partitionierten Entitäten

Service Bus unterstützt die automatische Nachrichtenweiterleitung von und zwischen partitionierten Entitäten sowie an diese. Sie können dieses Feature aktivieren, wenn Sie Warteschlangen und Abonnements erstellen oder aktualisieren. Weitere Informationen finden Sie unter [Aktivieren der Nachrichtenweiterleitung](enable-auto-forward.md). Wenn für die Nachricht ein Partitionsschlüssel (Sitzungs-ID, Partitionsschlüssel oder Nachrichten-ID) angegeben ist, wird dieser Partitionsschlüssel für die Zielentität verwendet.

## <a name="considerations-and-guidelines"></a>Aspekte und Richtlinien
* **Features für hohe Konsistenz**: Wenn eine Entität Features wie Sitzungen, Duplikaterkennung oder die explizite Steuerung eines Partitionierungsschlüssels verwendet, werden die Messagingvorgänge immer an bestimmte Partitionen geleitet. Wenn für eine Partition hoher Datenverkehr auftritt oder der zugrunde liegende Speicher fehlerhaft ist, schlagen diese Vorgänge fehl, und die Verfügbarkeit wird reduziert. Insgesamt ist die Konsistenz trotzdem deutlich höher als bei nicht partitionierten Entitäten: Nur für eine Teilmenge des Datenverkehrs treten Probleme auf, und nicht für den gesamten Datenverkehr. Weitere Informationen hierzu finden Sie in dieser [Erläuterung von Verfügbarkeit und Konsistenz](../event-hubs/event-hubs-availability-and-consistency.md).
* **Verwaltung**: Vorgänge wie das Erstellen, Aktualisieren und Löschen müssen für alle Partitionen der Entität durchgeführt werden. Wenn eine Partition fehlerhaft ist, kann dies zu Fehlern bei diesen Vorgängen führen. Für den Get-Vorgang müssen Informationen aus allen Partitionen aggregiert werden, z. B. die Nachrichtenanzahl. Wenn eine Partition einen Fehler aufweist, wird der Verfügbarkeitsstatus der Entität als „Eingeschränkt“ gemeldet.
* **Nachrichtenszenarios mit geringem Volumen**: Für diese Szenarios, vor allem bei Verwendung des HTTP-Protokolls, müssen Sie ggf. mehrere Empfangsvorgänge durchführen, um alle Nachrichten zu erhalten. Für Empfangsanforderungen führt das Front-End den Empfangsvorgang für alle Partitionen durch und speichert alle empfangenen Antworten zwischen. Eine nachfolgende Empfangsanforderung derselben Verbindung profitiert von dieser Zwischenspeicherung, und die Latenzen für den Empfang sind niedriger. Wenn Sie über mehrere Verbindungen verfügen oder HTTP verwenden, wird für jede Anforderung eine neue Verbindung hergestellt. Es besteht also keine Garantie, dass sie auf demselben Knoten eintrifft. Wenn alle vorhandenen Nachrichten gesperrt sind und auf einem anderen Front-End zwischengespeichert werden, gibt der Empfangsvorgang **NULL** zurück. Nachrichten laufen nach einer gewissen Zeit ab und können von Ihnen dann erneut empfangen werden. Hierfür wird HTTP-Keep-Alive empfohlen. Wenn Partitionierung in Szenarien mit geringem Volumen verwendet wird, können Empfangsvorgänge länger als erwartet dauern. Daher wird empfohlen, in diesen Szenarien keine Partitionierung zu verwenden. Löschen Sie alle vorhandenen partitionierten Entitäten, und erstellen Sie sie mit deaktivierter Partitionierung neu.
* **Durchsuchen/Einsehen von Nachrichten**: Der Peekvorgang gibt nicht immer die angeforderte Anzahl von Nachrichten zurück. Für dieses Verhalten gibt es zwei häufige Ursachen. Ein Grund ist, dass die aggregierte Größe der Sammlung mit Nachrichten die maximale Größe von 256 KB übersteigt. Ein weiterer Grund ist, dass eine Partition in partitionierten Warteschlangen oder Themen möglicherweise nicht über genügend Nachrichten zum Zurückgeben der angeforderten Anzahl von Nachrichten verfügen kann. Wenn eine Anwendung eine bestimmte Anzahl von Nachrichten einsehen/durchsuchen möchte, sollte sie im Allgemeinen mehrfach den Peekvorgang aufrufen, bis die benötigte Anzahl von Nachrichten erreicht ist oder keine einzusehenden Nachrichten mehr vorhanden sind. Weitere Informationen, einschließlich Codebeispielen, finden Sie unter [Durchsuchen von Nachrichten](message-browsing.md).

## <a name="partitioned-entities-limitations"></a>Einschränkungen partitionierter Entitäten
Derzeit gelten bei Service Bus die folgenden Einschränkungen für partitionierte Warteschlangen und Themen:

* Partitionierte Warteschlangen und Themen werden im Premium Messaging-Tarif nicht unterstützt. Sitzungen werden im Premium-Tarif mithilfe von SessionId unterstützt. 
* Für partitionierte Warteschlangen und Themen wird das Senden von Nachrichten nicht unterstützt, die unterschiedlichen Sitzungen einer einzelnen Transaktion angehören.
* Service Bus erlaubt derzeit bis zu 100 partitionierte Warteschlangen oder Themen pro Namespace. Jede partitionierte Warteschlange bzw. jedes partitionierte Thema wird in das zulässige Kontingent von 10.000 Entitäten pro Namespace eingerechnet (gilt nicht für Premium-Tarif).


## <a name="next-steps"></a>Nächste Schritte
Sie können die Partitionierung mit dem Azure-Portal, PowerShell, der CLI, einer Resource Manager-Vorlage, .NET, Java, Python und JavaScript aktivieren. Weitere Informationen finden Sie unter [Aktivieren der Partitionierung für eine Warteschlange oder ein Thema von Azure Service Bus](enable-partitions.md). 

Informieren Sie sich im [AMQP 1.0-Protokollhandbuch](service-bus-amqp-protocol-guide.md) über die grundlegenden Konzepte der Messagingspezifikation von AMQP 1.0.

[Azure portal]: https://portal.azure.com
[QueueDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablepartitioning
[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto
[AMQP 1.0 support for Service Bus partitioned queues and topics]: ./service-bus-amqp-protocol-guide.md
