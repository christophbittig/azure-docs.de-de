---
title: Bewährte Methoden zur Verbesserung der Leistung mit Azure Service Bus
description: Beschreibt, wie Service Bus verwendet wird, um die Leistung beim Austausch von Brokernachrichten zu optimieren.
ms.topic: article
ms.date: 08/30/2021
ms.openlocfilehash: 246b9deedb9385b671bf89a27d666798c703a93a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130232137"
---
# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>Bewährte Methoden für Leistungsoptimierungen mithilfe von Service Bus Messaging

In diesem Artikel erfahren Sie, wie Sie mithilfe von Azure Service Bus die Leistung beim Austausch von im Broker gespeicherten Nachrichten optimieren. Im ersten Teil dieses Artikels werden die verschiedenen Mechanismen zur Leistungssteigerung beschrieben. Der zweite Teil bietet eine Anleitung zur Verwendung von Service Bus auf eine Weise, die die beste Leistung in einem bestimmten Szenario ermöglichen kann.

Im Rahmen dieses Artikels bezieht sich der Begriff „Client“ auf eine Entität, die auf Service Bus zugreift. Ein Client kann die Rolle eines Absenders oder eines Empfängers annehmen. Der Begriff „Absender“ wird für einen Service Bus-Warteschlangenclient oder für einen Service Bus-Themaclient verwendet, der Nachrichten an eine Service Bus-Warteschlange oder an ein Service Bus-Thema sendet. Der Begriff „Empfänger“ bezieht sich auf einen Service Bus-Warteschlangenclient oder einen auf einen Service Bus-Abonnementclient, der Nachrichten von einer Service Bus-Warteschlange oder einem Service Bus-Abonnement empfängt.

## <a name="resource-planning-and-considerations"></a>Ressourcenplanung und Überlegungen

Wie bei jeder technischen Ressource ist umsichtige Planung der Schlüssel, um sicherzustellen, dass Azure Service Bus die Leistung erbringt, die Ihre Anwendung erwartet. Die richtige Konfiguration oder Topologie für Ihre Service Bus-Namespaces hängt von einer Vielzahl von Faktoren ab, die Ihre Anwendungsarchitektur und die Art und Weise betreffen, wie die einzelnen Service Bus-Funktionen verwendet werden.

### <a name="pricing-tier"></a>Tarif

Service Bus bietet verschiedene Tarife. Es wird empfohlen, den geeigneten Tarif für Ihre Anwendungsanforderungen auszuwählen.

   * **Standard-Tarif:** Geeignet für Entwickler-/Testumgebungen oder Szenarien mit geringem Durchsatz, in denen Anwendungen **nicht negativ** auf Drosselung reagieren.

   * **Premium-Tarif:** Geeignet für Produktionsumgebungen mit unterschiedlichen Durchsatzanforderungen, in denen vorhersagbare Latenz und vorhersagbarer Durchsatz erforderlich sind. Darüber hinaus können Service Bus Premium-Namespaces [automatisch skaliert](automate-update-messaging-units.md) und aktiviert werden, um Durchsatzspitzen zu bewältigen.

> [!NOTE]
> Wenn nicht der richtige Tarif ausgewählt wird, besteht die Gefahr, dass der Service Bus-Namespace überfordert wird, was zu [Drosselung](service-bus-throttling.md) führen kann.
>
> Drosselung führt nicht zu Datenverlusten. Anwendungen, die das Service Bus SDK nutzen, können die Standardwiederholungsrichtlinie verwenden, um sicherzustellen, dass die Daten schließlich von Service Bus akzeptiert werden.
>

### <a name="calculating-throughput-for-premium"></a>Berechnen des Durchsatzes für den Premium-Tarif

Daten, die an Service Bus gesendet werden, werden in binäre Daten serialisiert und dann deserialisiert, wenn sie vom Empfänger empfangen werden. Während Anwendungen **Nachrichten** als atomare Arbeitseinheiten betrachten, misst Service Bus den Durchsatz in Byte (oder Megabyte).

Berücksichtigen Sie bei der Berechnung der Durchsatzanforderung die Daten, die an Service Bus (Eingang) gesendet werden, und die Daten, die von Service Bus (Ausgang) empfangen werden.

Wie erwartet ist der Durchsatz für kleinere Nachrichtennutzdaten höher, die in Batches zusammengefasst werden können.

#### <a name="benchmarks"></a>Vergleichstests

Hier sehen Sie ein [GitHub-Beispiel](https://github.com/Azure-Samples/service-bus-dotnet-messaging-performance), das Sie ausführen können, um den erwarteten Durchsatz zu ermitteln, den Sie für Ihren SB-Namespace erhalten. In unseren [Benchmarktests](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Premium-Messaging-How-fast-is-it/ba-p/370722) haben wir ungefähr 4 MB/Sekunde pro Messagingeinheit (Messaging Unit, MU) von ein- und ausgehenden Daten beobachtet.

Im Benchmarkbeispiel werden keine erweiterten Features verwendet, sodass der Durchsatz, den Ihre Anwendungen erzielen, je nach Szenario unterschiedlich ist.

#### <a name="compute-considerations"></a>Computeaspekte

Die Verwendung bestimmter Service Bus-Features erfordert möglicherweise eine Computeauslastung, die den erwarteten Durchsatz verringern kann. Einige dieser Features betreffen Folgendes:

1. Sitzungen.
2. Auffächern in mehrere Abonnements für ein einzelnes Thema.
3. Ausführen zahlreicher Filter für ein einzelnes Abonnement.
4. Geplante Nachrichten.
5. Verzögerte Nachrichten.
6. Transaktionen
7. Deduplizierung und Rückschauzeitfenster.
8. Weiterleiten (Weiterleitung von einer Entität an eine andere).

Wenn Ihre Anwendung eines der oben genannten Features nutzt und Sie nicht den erwarteten Durchsatz erzielen, können Sie die **CPU-Nutzungsmetriken** überprüfen und erwägen, Ihren Service Bus Premium-Namespace zentral hochzuskalieren.

Sie können auch Azure Monitor verwenden, um den [Service Bus-Namespace automatisch zu skalieren](automate-update-messaging-units.md).

### <a name="sharding-across-namespaces"></a>Namespaceübergreifendes Sharding

Das Hochskalieren von Computeressourcen (Messagingeinheiten), die dem Namespace zugeordnet sind, ist zwar eine einfachere Lösung, **führt jedoch möglicherweise nicht** zu einer linearen Erhöhung des Durchsatzes. Dies liegt an Service Bus-Einstellungen (Speicher, Netzwerk usw.), die den Durchsatz möglicherweise einschränken.

Die bessere Lösung besteht in diesem Fall in Sharding Ihrer Entitäten (Warteschlangen und Themen) über verschiedene Service Bus Premium-Namespaces hinweg. Sie können auch Sharding für verschiedene Namespaces in verschiedenen Azure-Regionen in Betracht ziehen.

## <a name="protocols"></a>Protokolle
Service Bus ermöglicht Clients das Senden und Empfangen von Nachrichten über eins von drei Protokollen:

1. Advanced Message Queuing Protocol (AMQP)
2. Service Bus Messaging Protocol (SBMP)
3. Hypertext Transfer-Protokoll (HTTP)

AMQP ist am effizientesten, weil hierbei die Verbindung mit Service Bus aufrechterhalten wird. Außerdem implementiert es die [Batchverarbeitung](#batching-store-access) und [Vorabrufe](#prefetching). Sofern nicht anders angegeben, wird für alle Inhalte in diesem Artikel AMQP oder SBMP verwendet.

> [!IMPORTANT]
> SBMP ist nur für .NET Framework verfügbar. AMQP ist die Standardeinstellung für .NET Standard.

## <a name="choosing-the-appropriate-service-bus-net-sdk"></a>Auswählen des geeigneten Service Bus .NET SDK

Das Paket `Azure.Messaging.ServiceBus` ist das neueste Azure Service Bus .NET SDK und ab November 2020 verfügbar. Es gibt zwei ältere .NET SDKs, die weiterhin kritische Fehlerbehebungen erhalten, aber wir empfehlen dringend, stattdessen das neueste SDK zu verwenden. Ausführliche Informationen zum Wechsel von den älteren SDKs finden Sie im [Migrationsleitfaden](https://aka.ms/azsdk/net/migrate/sb).

| NuGet-Paket | Primäre Namespaces | Mindestplattform | Protokolle |
|---------------|----------------------|---------------------|-------------|
| [Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus) (**aktuelle Version**) | `Azure.Messaging.ServiceBus`<br>`Azure.Messaging.ServiceBus.Administration` | .NET Core 2.0<br>.NET Framework 4.6.1<br>Mono 5.4<br>Xamarin.iOS 10.14<br>Xamarin.Mac 3.8<br>Xamarin.Android 8.0<br>Universelle Windows-Plattform 10.0.16299 | AMQP<br>HTTP |
| [Microsoft.Azure.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) | `Microsoft.Azure.ServiceBus`<br>`Microsoft.Azure.ServiceBus.Management` | .NET Core 2.0<br>.NET Framework 4.6.1<br>Mono 5.4<br>Xamarin.iOS 10.14<br>Xamarin.Mac 3.8<br>Xamarin.Android 8.0<br>Universelle Windows-Plattform 10.0.16299 | AMQP<br>HTTP |
| [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus) (**Legacyversion**) | `Microsoft.ServiceBus`<br>`Microsoft.ServiceBus.Messaging` | .NET Framework 4.6.1 | AMQP<br>SBMP<br>HTTP |

Weitere Informationen zu den Mindestplattformen für die Unterstützung von .NET Standard finden Sie unter [Unterstützung der .NET-Implementierung](/dotnet/standard/net-standard#net-implementation-support).

## <a name="reusing-factories-and-clients"></a>Wiederverwenden von Factorys und Clients
# <a name="azuremessagingservicebus-sdk"></a>[Azure.Messaging.ServiceBus SDK](#tab/net-standard-sdk-2)
Die Service Bus-Objekte, die mit dem Dienst interagieren (z. B. [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient), [ServiceBusSender](/dotnet/api/azure.messaging.servicebus.servicebussender), [ServiceBusReceiver](/dotnet/api/azure.messaging.servicebus.servicebusreceiver) und [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor)), sollten für die Abhängigkeitsinjektion als Singletons registriert (oder einmal instanziiert und freigegeben) werden. ServiceBusClient kann für die Abhängigkeitsinjektion mit den [ServiceBusClientBuilderExtensions](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/servicebus/Azure.Messaging.ServiceBus/src/Compatibility/ServiceBusClientBuilderExtensions.cs) registriert werden. 

Es wird empfohlen, diese Objekte nach dem Senden oder Empfangen einzelner Nachrichten nicht zu schließen oder zu verwerfen. Durch das Schließen oder Verwerfen der entitätsspezifischen Objekte (ServiceBusSender/Receiver/Processor) wird die Verknüpfung zum Service Bus-Dienst getrennt. Das Verwerfen von ServiceBusClient führt dazu, dass die Verbindung mit dem Service Bus-Dienst beendet wird. 

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

> Beachten Sie, dass ab November 2020 ein neueres Paket für Azure.Messaging.ServiceBus verfügbar ist. Obwohl für das Microsoft.Azure.ServiceBus-Paket weiterhin kritische Fehlerbehebungen bereitgestellt werden, empfehlen wir Ihnen dringend, ein Upgrade durchzuführen. Weitere Informationen finden Sie im [Migrationsleitfaden](https://aka.ms/azsdk/net/migrate/sb).

Service Bus-Clientobjekte, z. B. Implementierungen von [`IQueueClient`][QueueClient] oder [`IMessageSender`][MessageSender], sollten für die Abhängigkeitsinjektion als Singletons registriert werden (oder einmal instanziiert und dann freigegeben werden). Es empfiehlt sich, Messagingfactorys oder Warteschlangen-, Themen- oder Abonnementclients nicht zu schließen, nachdem Sie eine Nachricht gesendet haben, und dann erneut zu erstellen, wenn Sie die nächste Nachricht senden. Durch das Schließen einer Messagingfactory wird die Verbindung mit dem Service Bus-Dienst gelöscht. Beim erneuten Erstellen der Factory wird eine neue Verbindung hergestellt. 

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

> Beachten Sie, dass ab November 2020 ein neueres Paket für Azure.Messaging.ServiceBus verfügbar ist. Obwohl für das WindowsAzure.ServiceBus-Paket weiterhin kritische Fehlerbehebungen bereitgestellt werden, empfehlen wir Ihnen dringend, ein Upgrade durchzuführen. Weitere Informationen finden Sie im [Migrationsleitfaden](https://aka.ms/azsdk/net/migrate/sb).

Service Bus-Clientobjekte, z. B. `QueueClient` oder `MessageSender`, werden über ein [MessagingFactory][MessagingFactory]-Objekt erstellt, das die interne Verwaltung von Verbindungen ermöglicht. Es empfiehlt sich, Messagingfactorys oder Warteschlangen-, Themen- oder Abonnementclients nicht zu schließen, nachdem Sie eine Nachricht gesendet haben, und dann erneut zu erstellen, wenn Sie die nächste Nachricht senden. Durch Schließen einer Messagingfactory wird die Verbindung mit dem Service Bus-Dienst gelöscht, und eine neue Verbindung wird beim erneuten Erstellen der Factory hergestellt. 

---

Der folgende Hinweis gilt für alle SDKs:

> [!NOTE]
> Das Herstellen einer Verbindung ist ein aufwendiger Vorgang, den Sie vermeiden können, indem Sie die Factory oder die Clientobjekte für mehrere Vorgänge verwenden. Sie können diese Clientobjekte sicher für gleichzeitige asynchrone Vorgänge und von mehreren Threads verwenden.

## <a name="concurrent-operations"></a>Parallele Vorgänge
Vorgänge wie Senden, Empfangen, Löschen usw. nehmen einige Zeit in Anspruch. Dieser Zeitraum umfasst die Zeit, die der Service-Bus-Dienst für die Bearbeitung des Vorgangs benötigt, sowie die Latenzzeit der Anforderung und der Antwort. Die Vorgänge müssen parallel ausgeführt werden, um die Anzahl von Vorgängen pro Zeitraum zu erhöhen.

Der Client plant gleichzeitige Vorgänge durch Ausführen von **asynchronen** Vorgängen. Die nächste Anforderung wird gestartet, bevor die vorherige Anforderung abgeschlossen ist. Im folgenden Codeausschnitt sehen Sie ein Beispiel für einen asynchronen Sendevorgang:

# <a name="azuremessagingservicebus-sdk"></a>[Azure.Messaging.ServiceBus SDK](#tab/net-standard-sdk-2)
```csharp
var messageOne = new ServiceBusMessage(body);
var messageTwo = new ServiceBusMessage(body);

var sendFirstMessageTask =
    sender.SendMessageAsync(messageOne).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #1");
    });
var sendSecondMessageTask =
    sender.SendMessageAsync(messageTwo).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #2");
    });

await Task.WhenAll(sendFirstMessageTask, sendSecondMessageTask);
Console.WriteLine("All messages sent");

```

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

```csharp
var messageOne = new Message(body);
var messageTwo = new Message(body);

var sendFirstMessageTask =
    queueClient.SendAsync(messageOne).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #1");
    });
var sendSecondMessageTask =
    queueClient.SendAsync(messageTwo).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #2");
    });

await Task.WhenAll(sendFirstMessageTask, sendSecondMessageTask);
Console.WriteLine("All messages sent");
```

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

```csharp
var messageOne = new BrokeredMessage(body);
var messageTwo = new BrokeredMessage(body);

var sendFirstMessageTask =
    queueClient.SendAsync(messageOne).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #1");
    });
var sendSecondMessageTask =
    queueClient.SendAsync(messageTwo).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #2");
    });

await Task.WhenAll(sendFirstMessageTask, sendSecondMessageTask);
Console.WriteLine("All messages sent");
```

---

Im folgenden Code sehen Sie ein Beispiel für einen asynchronen Empfangsvorgang.

# <a name="azuremessagingservicebus-sdk"></a>[Azure.Messaging.ServiceBus SDK](#tab/net-standard-sdk-2)

```csharp
var client = new ServiceBusClient(connectionString);
var options = new ServiceBusProcessorOptions 
{

      AutoCompleteMessages = false,
      MaxConcurrentCalls = 20
};
await using ServiceBusProcessor processor = client.CreateProcessor(queueName,options);
processor.ProcessMessageAsync += MessageHandler;
processor.ProcessErrorAsync += ErrorHandler;

static Task ErrorHandler(ProcessErrorEventArgs args)
{
    Console.WriteLine(args.Exception);
    return Task.CompletedTask;
};

static async Task MessageHandler(ProcessMessageEventArgs args)
{
    Console.WriteLine("Handle message");
    await args.CompleteMessageAsync(args.Message);
}

await processor.StartProcessingAsync();
```

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

Vollständige Quellcodebeispiele finden Sie im [GitHub-Repository](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/SendersReceiversWithQueues). 

```csharp
var receiver = new MessageReceiver(connectionString, queueName, ReceiveMode.PeekLock);

static Task LogErrorAsync(Exception exception)
{
    Console.WriteLine(exception);
    return Task.CompletedTask;
};

receiver.RegisterMessageHandler(
    async (message, cancellationToken) =>
    {
        Console.WriteLine("Handle message");
        await receiver.CompleteAsync(message.SystemProperties.LockToken);
    },
    new MessageHandlerOptions(e => LogErrorAsync(e.Exception))
    {
        AutoComplete = false,
        MaxConcurrentCalls = 20
    });
```

Das `MessageReceiver`-Objekt wird mit der Verbindungszeichenfolge, dem Warteschlangennamen und einem Peek-Lock-Empfangsmodus instanziiert. Als Nächstes wird die `receiver`-Instanz verwendet, um den Meldungshandler zu registrieren.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

Vollständige Quellcodebeispiele finden Sie im [GitHub-Repository](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/SendersReceiversWithQueues).

```csharp
var factory = MessagingFactory.CreateFromConnectionString(connectionString);
var receiver = await factory.CreateMessageReceiverAsync(queueName, ReceiveMode.PeekLock);

// Register the handler to receive messages asynchronously
receiver.OnMessageAsync(
    async message =>
    {
        Console.WriteLine("Handle message");
        await message.CompleteAsync();
    },
    new OnMessageOptions
    {
        AutoComplete = false,
        MaxConcurrentCalls = 20
    });
```

Von `MessagingFactory` wird aus der Verbindungszeichenfolge ein `factory`-Objekt erstellt. Mit der `factory`-Instanz wird ein `MessageReceiver` instanziiert. Als Nächstes wird die `receiver`-Instanz verwendet, um den OnMessage-Handler zu registrieren.

---

## <a name="receive-mode"></a>Empfangsmodus

Beim Erstellen eines Warteschlangen- oder Abonnementclients können Sie einen Empfangsmodus angeben: *PeekLock* oder *ReceiveAndDelete*. Der Standardempfangsmodus ist `PeekLock`. Beim Betrieb im Standardmodus sendet der Client eine Anforderung zum Empfangen einer Nachricht von Service Bus. Nachdem der Client die Nachricht empfangen hat, sendet er eine Anforderung zum Abschließen der Nachricht.

Wenn der Empfangsmodus auf `ReceiveAndDelete` festgelegt wird, werden beide Schritte in einer Anforderung kombiniert. Dieser Schritt reduziert die Anzahl der Vorgänge und kann den Gesamtnachrichtendurchsatz verbessern. Diese Leistungssteigerung kann zu Nachrichtenverlusten führen.

Service Bus unterstützt keine Transaktionen für ReceiveAndDelete-Vorgänge. Außerdem ist PeekLock-Semantik für alle Szenarien erforderlich, in denen der Client eine Nachricht zurückstellen oder [in eine Warteschlange für unzustellbare Nachrichten](service-bus-dead-letter-queues.md) verschieben möchte.

## <a name="client-side-batching"></a>Clientseitige Batchverarbeitung

Durch die clientseitige Batchverarbeitung kann ein Warteschlangen- oder Themenclient das Senden einer Nachricht für einen bestimmten Zeitraum verzögern. Wenn der Client während dieses Zeitraums weitere Nachrichten sendet, werden die Nachrichten in einem einzigen Batch übertragen. Die clientseitige Batchverarbeitung bewirkt auch, dass ein Warteschlangen- oder Abonnementclient mehrere Anforderungen zum **Abschließen** als Batch in eine einzelne Anforderung aufnehmen kann. Die Batchverarbeitung ist nur für asynchrone Vorgänge zum **Senden** und **Abschließen** verfügbar. Synchrone Vorgänge werden sofort an den Service Bus-Dienst gesendet. Die Batchverarbeitung erfolgt nicht für Vorschau- oder Empfangsvorgänge und auch nicht clientübergreifend.

# <a name="azuremessagingservicebus-sdk"></a>[Azure.Messaging.ServiceBus SDK](#tab/net-standard-sdk-2)
Bei der Batchverarbeitung für das .NET Standard SDK wird keine Eigenschaft für die Bearbeitung verfügbar gemacht.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

Bei der Batchverarbeitung für das .NET Standard SDK wird keine Eigenschaft für die Bearbeitung verfügbar gemacht.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

Ein Client verwendet standardmäßig ein Batchintervall von 20 ms. Sie können das Batchintervall ändern, indem Sie die Eigenschaft [BatchFlushInterval][BatchFlushInterval] vor dem Erstellen der Messagingfactory festlegen. Diese Einstellung wirkt sich auf alle Clients aus, die von dieser Factory erstellt werden.

Legen Sie zum Deaktivieren der Batchverarbeitung die Eigenschaft [BatchFlushInterval][BatchFlushInterval] auf **TimeSpan.Zero** fest. Beispiel:

```csharp
var settings = new MessagingFactorySettings
{
    NetMessagingTransportSettings =
    {
        BatchFlushInterval = TimeSpan.Zero
    }
};
var factory = MessagingFactory.Create(namespaceUri, settings);
```

Die Batchverarbeitung wirkt sich nicht auf die Anzahl der abrechenbaren Messagingvorgänge aus und ist nur für das Service Bus-Clientprotokoll unter Verwendung der [Microsoft.ServiceBus.Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/)-Bibliothek verfügbar. Das HTTP-Protokoll unterstützt keine Batchverarbeitung.

> [!NOTE]
> Durch das Festlegen von `BatchFlushInterval` wird sichergestellt, dass die Batchverarbeitung aus Perspektive der Anwendung implizit erfolgt. Dies bedeutet, dass die Anwendung keine spezifischen Batchaufrufe durchführt, sondern `SendAsync` und `CompleteAsync`.
>
> Die explizite clientseitige Batchverarbeitung kann mithilfe des folgenden Methodenaufrufs implementiert werden:
> ```csharp
> Task SendBatchAsync(IEnumerable<BrokeredMessage> messages);
> ```
> Hier muss die kombinierte Größe der Nachrichten kleiner sein als die maximale Größe, die vom Tarif unterstützt wird.

---

## <a name="batching-store-access"></a>Batchverarbeitung des Speicherzugriffs

Um den Durchsatz einer Warteschlange, eines Themas oder eines Abonnements zu erhöhen, verarbeitet Service Bus beim Schreiben in den internen Speicher mehrere Nachrichten als Batch. 

- Wenn Sie Batchverarbeitung für eine Warteschlange aktivieren, wird das Schreiben von Nachrichten in den Speicher und das Löschen von Nachrichten aus dem Speicher als Batch verarbeitet. 
- Wenn Sie Batchverarbeitung für ein Thema aktivieren, wird das Schreiben von Nachrichten in den Speicher als Batch verarbeitet. 
- Wenn Sie Batchverarbeitung für ein Abonnement aktivieren, wird das Löschen von Nachrichten aus dem Speicher als Batch verarbeitet. 
- Wenn für eine Entität Speicherzugriff als Batch aktiviert ist, verzögert Service Bus einen Schreibvorgang in den Speicher für diese Entität für bis zu 20 ms.

> [!NOTE]
> Es besteht keine Gefahr, dass bei der Batchverarbeitung Nachrichten verloren gehen, auch wenn am Ende eines Batchintervalls von 20 ms ein Service Bus-Fehler auftritt.

Weitere Speichervorgänge, die während dieses Intervalls auftreten, werden dem Batch hinzugefügt. Speicherzugriff als Batch wirkt sich nur auf Vorgänge zum **Senden** und **Abschließen** aus, Empfangsvorgänge sind nicht betroffen. Speicherzugriff als Batch ist eine Eigenschaft einer Entität. Die Batchverarbeitung erfolgt für alle Entitäten, die Speicherzugriff als Batch ermöglichen.

Beim Erstellen neuer Warteschlangen, Themen oder Abonnements ist der Speicherzugriff als Batch standardmäßig aktiviert.


# <a name="azuremessagingservicebus-sdk"></a>[Azure.Messaging.ServiceBus SDK](#tab/net-standard-sdk-2)
Zum Deaktivieren des Speicherzugriffs als Batch benötigen Sie eine `ServiceBusAdministrationClient`-Instanz. Erstellen Sie `CreateQueueOptions` aus einer Warteschlangenbeschreibung, wobei die `EnableBatchedOperations`-Eigenschaft auf `false` festgelegt wird.

```csharp
var options = new CreateQueueOptions(path)
{
    EnableBatchedOperations = false
};
var queue = await administrationClient.CreateQueueAsync(options);
```


# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

Zum Deaktivieren des Speicherzugriffs als Batch benötigen Sie eine `ManagementClient`-Instanz. Erstellen Sie eine Warteschlange aus einer Warteschlangenbeschreibung, wobei die `EnableBatchedOperations`-Eigenschaft auf `false` festgelegt wird.

```csharp
var queueDescription = new QueueDescription(path)
{
    EnableBatchedOperations = false
};
var queue = await managementClient.CreateQueueAsync(queueDescription);
```

Weitere Informationen finden Sie in den folgenden Artikeln:
- [Eigenschaft QueueDescription.EnableBatchedOperations](/dotnet/api/microsoft.azure.servicebus.management.queuedescription.enablebatchedoperations)
- [Eigenschaft SubscriptionDescription.EnabledBatchedOperations](/dotnet/api/microsoft.azure.servicebus.management.subscriptiondescription.enablebatchedoperations)
* [TopicDescription.EnableBatchedOperations](/dotnet/api/microsoft.azure.servicebus.management.topicdescription.enablebatchedoperations)

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

Zum Deaktivieren des Speicherzugriffs als Batch benötigen Sie eine `NamespaceManager`-Instanz. Erstellen Sie eine Warteschlange aus einer Warteschlangenbeschreibung, wobei die `EnableBatchedOperations`-Eigenschaft auf `false` festgelegt wird.

```csharp
var queueDescription = new QueueDescription(path)
{
    EnableBatchedOperations = false
};
var queue = namespaceManager.CreateQueue(queueDescription);
```

Weitere Informationen finden Sie in den folgenden Artikeln:
* [`Microsoft.ServiceBus.Messaging.QueueDescription.EnableBatchedOperations`](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablebatchedoperations)
* [`Microsoft.ServiceBus.Messaging.SubscriptionDescription.EnableBatchedOperations`](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.enablebatchedoperations)
* [`Microsoft.ServiceBus.Messaging.TopicDescription.EnableBatchedOperations`](/dotnet/api/microsoft.servicebus.messaging.topicdescription.enablebatchedoperations).

---

Batchspeicherzugriff wirkt sich nicht auf die Anzahl der abrechenbaren Messagingvorgänge aus. Dabei handelt es sich um eine Eigenschaft einer Warteschlange, eines Themas oder eines Abonnements. Sie ist unabhängig vom Empfangsmodus und dem Protokoll, das zwischen einem Client und dem Service Bus-Dienst verwendet wird.

## <a name="prefetching"></a>Vorabrufe

[Vorabrufe](service-bus-prefetch.md) ermöglichen es dem Warteschlangen- oder Abonnementclient, weitere Nachrichten aus dem Dienst zu laden, wenn Nachrichten empfangen werden. Der Client speichert diese Nachrichten in einem lokalen Cache. Die Größe des Caches wird durch die Eigenschaft `QueueClient.PrefetchCount` bzw. `SubscriptionClient.PrefetchCount` bestimmt. Jeder Client, der Vorabrufe ermöglicht, verwaltet seinen eigenen Cache. Ein Cache wird nicht für andere Clients freigegeben. Wenn der Client einen Empfangsvorgang startet und sein Cache leer ist, überträgt der Dienst ein Nachrichtenbatch. Die Größe des Batches entspricht der Größe des Caches oder 256 KB, je nachdem, welcher Wert kleiner ist. Wenn der Client einen Empfangsvorgang startet und der Cache eine Nachricht enthält, wird die Nachricht aus dem Cache abgerufen.

Wenn eine Nachricht vorab abgerufen wird, sperrt der Dienst die vorab abgerufene Nachricht. Durch die Sperre kann die vorabgerufene Nachricht nicht von einem anderen Empfänger empfangen werden. Wenn der Empfänger die Nachricht nicht abschließen kann, bevor die Sperre abläuft, wird die Nachricht für andere Empfänger verfügbar gemacht. Die vorab abgerufene Kopie der Nachricht verbleibt im Cache. Der Empfänger, der die abgelaufene Kopie aus dem Cache verarbeitet, empfängt eine Ausnahme, wenn er versucht, diese Nachricht abzuschließen. Standardmäßig läuft die Nachrichtensperrre nach 60 Sekunden ab. Dieser Wert kann auf 5 Minuten erhöht werden. Um die Nutzung abgelaufener Nachrichten zu verhindern, legen Sie die Cachegröße kleiner als die Anzahl der Nachrichten fest, die ein Client innerhalb des Sperrtimeoutintervalls nutzen kann.

Wenn der Standardsperrenablauf von 60 Sekunden verwendet wird, gilt als geeigneter Wert für `PrefetchCount` das 20-fache der maximalen Verarbeitungsraten aller Empfänger der Factory. Beispiel: Eine Factory erstellt 10 Empfänger, und jeder Empfänger kann bis zu 10 Nachrichten pro Sekunde verarbeiten. Der Wert für den Vorabruf sollte 20 × 3 × 10 = 600 nicht überschreiten. Standardmäßig ist `PrefetchCount` auf „0“ festgelegt. Dies bedeutet, dass keine weiteren Nachrichten aus dem Dienst abgerufen werden.

Der Vorabruf von Nachrichten vergrößert den Gesamtdurchsatz für eine Warteschlange oder ein Abonnement, da sich dadurch die Gesamtzahl von Nachrichtenvorgängen bzw. Roundtrips verringert. Das Abrufen der ersten Nachricht dauert jedoch länger (aufgrund der gestiegenen Nachrichtengröße). Das Empfangen vorab abgerufener Nachrichten aus dem Cache ist schneller, weil diese Nachrichten bereits vom Client heruntergeladen wurden.

Die Eigenschaft für die Gültigkeitsdauer (Time-to-Live, TTL) einer Nachricht wird vom Server zu dem Zeitpunkt überprüft, wenn der Server die Nachricht an den Client sendet. Der Client überprüft die TTL-Eigenschaft der Nachricht nicht, wenn die Nachricht empfangen wird. Stattdessen kann die Nachricht auch dann empfangen werden, wenn die Gültigkeitsdauer der Nachricht abgelaufen ist, während sich die Nachricht im Cache des Clients befunden hat.

Der Vorabruf wirkt sich nicht auf die Anzahl der abrechenbaren Messagingvorgänge aus und ist nur für das Service Bus-Clientprotokoll verfügbar. Das HTTP-Protokoll unterstützt keinen Vorabruf. Vorabrufe sind für synchrone und asynchrone Empfangsvorgänge verfügbar.

# <a name="azuremessagingservicebus-sdk"></a>[Azure.Messaging.ServiceBus SDK](#tab/net-standard-sdk-2)
Weitere Informationen finden Sie unter den folgenden `PrefetchCount`-Eigenschaften:

- [ServiceBusReceiver.PrefetchCount](/dotnet/api/azure.messaging.servicebus.servicebusreceiver.prefetchcount)
- [ServiceBusProcessor.PrefetchCount](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.prefetchcount)

Sie können Werte für diese Eigenschaften in [ServiceBusReceiverOptions](/dotnet/api/azure.messaging.servicebus.servicebusreceiveroptions) oder [ServiceBusProcessorOptions](/dotnet/api/azure.messaging.servicebus.servicebusprocessoroptions) festlegen.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

Weitere Informationen finden Sie unter den folgenden `PrefetchCount`-Eigenschaften:

* [`Microsoft.Azure.ServiceBus.QueueClient.PrefetchCount`](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount)
* [`Microsoft.Azure.ServiceBus.SubscriptionClient.PrefetchCount`](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.prefetchcount)

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

Weitere Informationen finden Sie unter den folgenden `PrefetchCount`-Eigenschaften:

* [`Microsoft.ServiceBus.Messaging.QueueClient.PrefetchCount`](/dotnet/api/microsoft.servicebus.messaging.queueclient.prefetchcount)
* [`Microsoft.ServiceBus.Messaging.SubscriptionClient.PrefetchCount`](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.prefetchcount)

---

## <a name="prefetching-and-receivebatch"></a>Vorabruf und „ReceiveBatch“
Die beim gemeinsamen Vorabrufen mehrerer Nachrichten angewendeten Konzepte ähneln hinsichtlich der Semantik der Nachrichtenverarbeitung in einem Batch (`ReceiveBatch`). Es gibt aber einige geringfügige Unterschiede, die Sie beachten müssen, wenn Sie diese beiden Optionen gemeinsam verwenden.

Der Vorabruf ist eine Konfiguration (bzw. ein Modus) auf dem Client (`QueueClient` und `SubscriptionClient`), und `ReceiveBatch` ist ein Vorgang (mit Anforderung/Antwort-Semantik).

Bedenken Sie bei der gemeinsamen Verwendung dieser beiden Ansätze Folgendes:

* Die Vorabrufanzahl muss größer oder gleich der Anzahl von Nachrichten sein, die Sie voraussichtlich mit `ReceiveBatch` empfangen.
* Die Vorabrufanzahl kann bis zu n-/dreimal so groß sein wie die Anzahl von verarbeiteten Nachrichten pro Sekunde, wobei „n“ die Standardsperrdauer ist.

Ein „gieriger“ Ansatz (also das Aufrechterhalten einer sehr hohen Vorabrufanzahl) bringt einige Probleme mit sich, da er voraussetzt, dass die Nachricht auf einen bestimmten Empfänger beschränkt ist. Es wird empfohlen, Vorabrufwerte zwischen den oben genannten Schwellenwerte auszuprobieren und empirisch zu ermitteln, welcher Wert geeignet ist.

## <a name="multiple-queues"></a>Mehrere Warteschlangen

Wenn eine einzelne Warteschlange oder ein Thema die erwartete Menge nicht verarbeiten kann, verwenden Sie mehrere Messagingentitäten. Wenn Sie mehrere Entitäten verwenden, erstellen Sie einen dedizierten Client für jede Entität, statt den gleichen Client für alle Entitäten zu nutzen.

## <a name="development-and-testing-features"></a>Entwicklungs- und Testfunktionen

> [!NOTE]
> Dieser Abschnitt gilt nur für das WindowsAzure.ServiceBus SDK, da diese Funktionalität von Microsoft.Azure.ServiceBus und Azure.Messaging.ServiceBus nicht verfügbar gemacht wird.

Service Bus verfügt über ein Feature, das speziell für die Entwicklung verwendet wird und **nie in Produktionskonfigurationen eingesetzt werden sollte**[`TopicDescription.EnableFilteringMessagesBeforePublishing`][TopicDescription.EnableFiltering].

Wenn dem Thema neue Regeln oder Filter hinzugefügt werden, können Sie mit [`TopicDescription.EnableFilteringMessagesBeforePublishing`][TopicDescription.EnableFiltering] überprüfen, ob der neue Filterausdruck wie erwartet funktioniert.

## <a name="scenarios"></a>Szenarien

In den folgenden Abschnitten werden normale Messagingszenarien und die bevorzugten Service Bus-Einstellungen beschrieben. Durchsatzraten werden als klein (weniger als 1 Nachricht/Sekunde), mittel (1 Nachricht/Sekunde oder mehr, aber weniger als 100 Nachrichten/Sekunde) und hoch (100 Nachrichten/Sekunde oder mehr) klassifiziert. Die Anzahl der Clients wird als klein (5 oder weniger), mittel (mehr als 5, aber weniger als oder gleich 20) und groß (mehr als 20) klassifiziert.

### <a name="high-throughput-queue"></a>Warteschlange mit hohem Durchsatz

Zielsetzung: Maximieren des Durchsatzes einer einzelnen Warteschlange. Die Anzahl der Absender und Empfänger ist klein.

* Verwenden Sie zum Erhöhen der Gesamtsenderate an die Warteschlange mehrere Messagingfactorys, um Absender zu erstellen. Verwenden Sie für jeden Absender asynchrone Vorgänge oder mehrere Threads.
* Verwenden Sie zum Erhöhen der Gesamtempfangsrate von der Warteschlange mehrere Messagingfactorys, um Empfänger zu erstellen.
* Verwenden Sie asynchrone Vorgänge, um die clientseitige Batchverarbeitung zu nutzen.
* Legen Sie das Batchintervall auf 50 ms fest, um die Anzahl der Service Bus-Clientprotokollübertragungen zu verringern. Wenn mehrere Absender verwendet werden, erhöhen Sie das Batchintervall auf 100 ms.
* Lassen Sie den Speicherzugriff als Batch aktiviert. Dies erhöht die Gesamtrate, mit der Nachrichten in die Warteschlange geschrieben werden können.
* Legen Sie den Wert für Vorabrufe auf das 20-fache der maximalen Verarbeitungsraten aller Empfänger einer Factory fest. Dies verringert die Anzahl der Service Bus-Clientprotokollübertragungen.

### <a name="multiple-high-throughput-queues"></a>Mehrere Warteschlangen mit hohem Durchsatz

Zielsetzung: Maximieren des Gesamtdurchsatzes mehrerer Warteschlangen. Der Durchsatz einer einzelnen Warteschlange ist mittel oder hoch.

Um den maximalen Durchsatz für mehrere Warteschlangen zu erhalten, verwenden Sie die beschriebenen Einstellungen, um den Durchsatz einer einzelnen Warteschlange zu maximieren. Verwenden Sie außerdem unterschiedliche Factorys zum Erstellen von Clients, die an verschiedene Warteschlangen senden bzw. von ihnen empfangen.

### <a name="low-latency-queue"></a>Warteschlange mit niedriger Latenz

Zielsetzung: Minimieren der Latenz einer Warteschlange oder eines Themas. Die Anzahl der Absender und Empfänger ist klein. Der Durchsatz der Warteschlange ist klein oder mittel.

* Deaktivieren Sie die clientseitige Batchverarbeitung. Der Client sendet eine Nachricht sofort.
* Deaktivieren Sie den Speicherzugriff als Batch. Der Dienst schreibt die Nachricht sofort in den Speicher.
* Wenn Sie einen einzelnen Client verwenden, legen Sie den Wert für den Vorabruf auf das 20-fache der Verarbeitungsrate des Empfängers fest. Wenn mehrere Nachrichten gleichzeitig in der Warteschlange ankommen, überträgt das Service Bus-Clientprotokoll sie alle zur gleichen Zeit. Wenn der Client die nächste Nachricht empfängt, befindet sich diese Nachricht bereits im lokalen Cache. Der Cache sollte klein sein.
* Wenn mehrere Clients verwendet werden, legen Sie den Wert für den Vorabruf auf 0 fest. Auf diese Weise kann der zweite Client die zweite Nachricht empfangen, während der erste Client noch die erste Nachricht verarbeitet.

### <a name="queue-with-a-large-number-of-senders"></a>Warteschlange mit einer großen Anzahl von Absendern

Zielsetzung: Maximieren des Durchsatzes einer Warteschlange oder eines Themas mit einer großen Anzahl von Absendern. Jeder Absender sendet Nachrichten mit einer mittleren Rate. Die Anzahl der Empfänger ist klein.

Service Bus ermöglicht bis zu 1.000 gleichzeitige Verbindungen mit einer Messagingentität. Dieser Grenzwert wird auf Namespaceebene erzwungen, und Warteschlangen, Themen oder Abonnements werden durch den Grenzwert für gleichzeitige Verbindungen pro Namespace begrenzt. Bei Warteschlangen wird diese Anzahl zwischen Absendern und Empfängern aufgeteilt. Wenn alle 1000 Verbindungen für Absender benötigt werden, ersetzen Sie die Warteschlange durch ein Thema und ein einzelnes Abonnement. Ein Thema akzeptiert bis zu 1.000 gleichzeitige Verbindungen von Absendern. Das Abonnement nimmt weitere 1.000 gleichzeitige Verbindungen von Empfängern an. Wenn mehr als 1000 gleichzeitige Absender erforderlich sind, sollten die Absender über HTTP Nachrichten an das Service Bus-Protokoll senden.

Gehen Sie folgendermaßen vor, um den Durchsatz zu maximieren:

* Wenn sich jeder Absender in einem anderen Prozess befindet, verwenden Sie nur eine Factory pro Prozess.
* Verwenden Sie asynchrone Vorgänge, um die clientseitige Batchverarbeitung zu nutzen.
* Verwenden Sie das standardmäßige Batchintervall von 20 ms, um die Anzahl der Service Bus-Clientprotokollübertragungen zu verringern.
* Lassen Sie den Speicherzugriff als Batch aktiviert. Dies erhöht die Gesamtrate, mit der Nachrichten in die Warteschlange oder in das Thema geschrieben werden können.
* Legen Sie den Wert für Vorabrufe auf das 20-fache der maximalen Verarbeitungsraten aller Empfänger einer Factory fest. Dies verringert die Anzahl der Service Bus-Clientprotokollübertragungen.

### <a name="queue-with-a-large-number-of-receivers"></a>Warteschlange mit einer großen Anzahl von Empfängern

Zielsetzung: Maximieren der Empfangsrate einer Warteschlange oder eines Abonnements mit einer großen Anzahl von Empfängern. Jeder Empfänger empfängt Nachrichten mit einer mittleren Rate. Die Anzahl der Absender ist klein.

Service Bus ermöglicht bis zu 1000 gleichzeitige Verbindungen mit einer Messagingentität. Wenn für eine Warteschlange mehr als 1000 Empfänger erforderlich sind, ersetzen Sie die Warteschlange durch ein Thema und mehrere Abonnements. Jedes Abonnement kann bis zu 1000 gleichzeitige Verbindungen unterstützen. Alternativ können Empfänger über das HTTP-Protokoll auf die Warteschlange zugreifen.

Verwenden Sie die folgenden Richtlinien, um den Durchsatz zu maximieren:

* Wenn sich jeder Empfänger in einem anderen Prozess befindet, verwenden Sie nur eine Factory pro Prozess.
* Empfänger können synchrone oder asynchrone Vorgänge verwenden. Bei der mittleren Empfangsrate eines einzelnen Empfängers wirkt sich die clientseitige Batchverarbeitung einer Anforderung zum Abschließen nicht auf den Empfängerdurchsatz aus.
* Lassen Sie den Speicherzugriff als Batch aktiviert. Dies verringert die Gesamtlast der Entität. Es verringert zudem die Gesamtrate, mit der Nachrichten in die Warteschlange oder in das Thema geschrieben werden können.
* Legen Sie den Wert für den Vorabruf auf einen kleinen Wert fest (z. B. "PrefetchCount" = 10). Dadurch wird verhindert, dass Empfänger über freie Kapazitäten verfügen, während andere Empfänger eine große Anzahl von Nachrichten zwischengespeichert haben.

### <a name="topic-with-a-few-subscriptions"></a>Thema mit einigen Abonnements

Zielsetzung: Maximieren des Durchsatzes eines Themas mit einer geringen Anzahl von Abonnements. Eine Nachricht wird von zahlreichen Abonnements empfangen, was bedeutet, dass die kombinierte Empfangsrate aller Abonnements größer als die Senderate ist. Die Anzahl der Absender ist klein. Die Empfängeranzahl pro Abonnement ist gering.

Verwenden Sie die folgenden Richtlinien, um den Durchsatz zu maximieren:

* Verwenden Sie zur Erhöhung der Gesamtsenderate für das Thema mehrere Messagingfactorys, um Absender zu erstellen. Verwenden Sie für jeden Absender asynchrone Vorgänge oder mehrere Threads.
* Verwenden Sie zur Erhöhung der Gesamtempfangsrate aus einem Abonnement mehrere Messagingfactorys, um Empfänger zu erstellen. Verwenden Sie für jeden Empfänger asynchrone Vorgänge oder mehrere Threads.
* Verwenden Sie asynchrone Vorgänge, um die clientseitige Batchverarbeitung zu nutzen.
* Verwenden Sie das standardmäßige Batchintervall von 20 ms, um die Anzahl der Service Bus-Clientprotokollübertragungen zu verringern.
* Lassen Sie den Speicherzugriff als Batch aktiviert. Dies erhöht die Gesamtrate, mit der Nachrichten in das Thema geschrieben werden können.
* Legen Sie den Wert für Vorabrufe auf das 20-fache der maximalen Verarbeitungsraten aller Empfänger einer Factory fest. Dies verringert die Anzahl der Service Bus-Clientprotokollübertragungen.

### <a name="topic-with-a-large-number-of-subscriptions"></a>Thema mit einer großen Anzahl von Abonnements

Zielsetzung: Maximieren des Durchsatzes eines Themas mit einer großen Anzahl von Abonnements. Eine Nachricht wird von zahlreichen Abonnements empfangen, was bedeutet, dass die kombinierte Empfangsrate aller Abonnements viel größer als die Senderate ist. Die Anzahl der Absender ist klein. Die Empfängeranzahl pro Abonnement ist gering.

Themen mit einer großen Anzahl von Abonnements weisen normalerweise einen geringen Gesamtdurchsatz auf, wenn alle Nachrichten an alle Abonnements weitergeleitet werden. Dies liegt daran, dass jede Nachricht mehrmals empfangen wird, und alle Nachrichten in einem Thema sowie dessen Abonnements werden im gleichen Speicher gespeichert. Die Annahme besteht hier darin, dass von einer geringen Anzahl von Absendern und Empfängern pro Abonnement ausgegangen wird. Service Bus unterstützt bis zu 2000 Abonnements pro Thema.

Probieren Sie die folgenden Schritte aus, um den Durchsatz zu maximieren:

* Verwenden Sie asynchrone Vorgänge, um die clientseitige Batchverarbeitung zu nutzen.
* Verwenden Sie das standardmäßige Batchintervall von 20 ms, um die Anzahl der Service Bus-Clientprotokollübertragungen zu verringern.
* Lassen Sie den Speicherzugriff als Batch aktiviert. Dies erhöht die Gesamtrate, mit der Nachrichten in das Thema geschrieben werden können.
* Legen Sie den Wert für den Vorabruf auf das 20-fache der erwarteten Empfangsrate in Sekunden fest. Dies verringert die Anzahl der Service Bus-Clientprotokollübertragungen.

<!-- .NET Standard SDK, Microsoft.Azure.ServiceBus -->
[QueueClient]: /dotnet/api/microsoft.azure.servicebus.queueclient
[MessageSender]: /dotnet/api/microsoft.azure.servicebus.core.messagesender

<!-- .NET Framework SDK, Microsoft.Azure.ServiceBus -->
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[BatchFlushInterval]: /dotnet/api/microsoft.servicebus.messaging.messagesender.batchflushinterval
[ForcePersistence]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage.forcepersistence
[EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnableFiltering]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablefilteringmessagesbeforepublishing

<!-- Local links -->
[Partitioned messaging entities]: service-bus-partitioning.md
