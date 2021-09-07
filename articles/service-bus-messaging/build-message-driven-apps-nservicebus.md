---
title: Erstellen von nachrichtengesteuerten Anwendungen mit NServiceBus und Azure Service Bus
description: Erfahren Sie, wie Sie komplexe Probleme mit verteilten Systemen in Azure Service Bus unter Verwendung des NServiceBus-Frameworks lösen.
author: kbaley
ms.author: spelluru
ms.service: service-bus-messaging
ms.topic: how-to
ms.date: 07/26/2021
ms.custom: template-how-to
ms.openlocfilehash: c9f503cb600c87e1dac590dcbbe7edf46bc7be76
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122338835"
---
# <a name="build-message-driven-business-applications-with-nservicebus-and-azure-service-bus"></a>Erstellen von nachrichtengesteuerten Geschäftsanwendungen mit NServiceBus und Azure Service Bus
NServiceBus ist ein von Particular Software bereitgestelltes kommerzielles Messagingframework. Es baut auf Azure Service Bus auf und hilft Entwicklern, sich auf die Geschäftslogik zu konzentrieren, indem Infrastrukturprobleme abstrahiert werden. In diesem Leitfaden erstellen wir eine Lösung, mit der Nachrichten zwischen zwei Diensten ausgetauscht werden. Außerdem wird gezeigt, wie Sie für fehlerhafte Nachrichten automatisch Wiederholungsversuche durchführen, und es werden Optionen zum Hosten dieser Dienste in Azure vorgestellt.

> [!NOTE]
> Der Code für dieses Tutorial ist auf der [Dokumentationswebsite von Particular Software](https://docs.particular.net/samples/azure-service-bus-netstandard/send-receive-with-nservicebus/) verfügbar. 

## <a name="prerequisites"></a>Voraussetzungen

Im Beispiel wird davon ausgegangen, dass Sie bereits einen [Azure Service Bus-Namespace erstellt](service-bus-create-namespace-portal.md) haben.

> [!IMPORTANT]
> Für NServiceBus wird mindestens der Standard-Tarif benötigt. Der Basic-Tarif kann nicht verwendet werden.

## <a name="download-and-prepare-the-solution"></a>Herunterladen und Vorbereiten der Lösung
1. Laden Sie den Code von der [Dokumentationswebsite von Particular Software](https://docs.particular.net/samples/azure-service-bus-netstandard/send-receive-with-nservicebus/) herunter. Die Lösung `SendReceiveWithNservicebus.sln` umfasst drei Projekte:

    - **Sender**: Eine Konsolenanwendung, die Nachrichten sendet.
    - **Receiver**: Eine Konsolenanwendung, die Nachrichten vom Sender empfängt und auf diese antwortet.
    - **Shared**: Eine Klassenbibliothek, die die Nachrichtenverträge enthält, die von Sender und Empfänger gemeinsam verwendet werden.
    
    Das folgende, von [ServiceInsight](https://particular.net/serviceinsight) (einem Tool für Visualisierung und Debugging von Particular Software) generierte Diagramm zeigt den Nachrichtenfluss:
    
    :::image type="content" source="./media/nservicebus/sequence-diagram.png" alt-text="Abbildung des Sequenzdiagramms":::    
1. Öffnen Sie `SendReceiveWithNservicebus.sln` in dem von Ihnen bevorzugten Code-Editor (z. B. Visual Studio 2019). 
1. Öffnen Sie `appsettings.json` sowohl im Receiver- als auch im Sender-Projekt, und legen Sie `AzureServiceBusConnectionString` auf die Verbindungszeichenfolge für Ihren Azure Service Bus-Namespace fest.



## <a name="define-the-shared-message-contracts"></a>Definieren der gemeinsam genutzten Nachrichtenverträge

In der Klassenbibliothek „Shared“ definieren Sie die Verträge, die zum Senden unserer Nachrichten verwendet werden. Sie enthält einen Verweis auf das NuGet-Paket `NServiceBus`, das Schnittstellen zur Identifizierung unserer Nachrichten enthält. Die Schnittstellen sind nicht erforderlich, bieten jedoch eine zusätzliche Validierung über NServiceBus und ermöglichen es, den Code selbsterklärend zu gestalten.

Zuerst überprüfen wir die Klasse `Ping.cs`.

```csharp
public class Ping : NServiceBus.ICommand
{
    public int Round { get; set; }
}
```

Die Klasse `Ping` definiert eine Nachricht, die der Absender (Sender) an den Empfänger (Receiver) sendet. Es handelt sich um eine einfache C#-Klasse, die `NServiceBus.ICommand` implementiert, eine Schnittstelle aus dem NServiceBus-Paket. Diese Nachricht signalisiert dem Leser und NServiceBus, dass es sich um einen Befehl handelt, wenngleich es andere Möglichkeiten gibt, Nachrichten [ohne Verwendung von Schnittstellen](https://docs.particular.net/nservicebus/messaging/conventions) zu identifizieren.

Die andere Nachrichtenklasse in den Shared-Projekten ist `Pong.cs`:

```csharp
public class Pong : NServiceBus.IMessage
{
    public string Acknowledgement { get; set; }
}
```

`Pong` ist ebenfalls ein einfaches C#-Objekt, das allerdings `NServiceBus.IMessage` implementiert. Die Schnittstelle `IMessage` repräsentiert eine generische Nachricht, die weder ein Befehl noch ein Ereignis ist und häufig für Antworten verwendet wird. In unserem Beispiel ist es eine Antwort, die der Empfänger zurück an den Sender sendet, um anzugeben, dass eine Nachricht empfangen wurde.

`Ping` und `Pong` sind die beiden Nachrichtentypen, die Sie verwenden werden. Der nächste Schritt besteht darin, den Sender für die Verwendung von Azure Service Bus und das Senden einer `Ping`-Nachricht zu konfigurieren.

## <a name="set-up-the-sender"></a>Einrichten des Senders

Der Sender ist ein Endpunkt, der unsere `Ping`-Nachricht sendet. Hier konfigurieren Sie den Sender für die Verwendung von Azure Service Bus als Transportmechanismus, erstellen dann eine `Ping`-Instanz und senden diese.

In der Methode `Main` von `Program.cs` konfigurieren Sie den Sender-Endpunkt:

```csharp
var host = Host.CreateDefaultBuilder(args)
    // Configure a host for the endpoint
    .ConfigureLogging((context, logging) =>
    {
        logging.AddConfiguration(context.Configuration.GetSection("Logging"));

        logging.AddConsole();
    })
    .UseConsoleLifetime()
    .UseNServiceBus(context =>
    {
        // Configure the NServiceBus endpoint
        var endpointConfiguration = new EndpointConfiguration("Sender");

        var transport = endpointConfiguration.UseTransport<AzureServiceBusTransport>();
        var connectionString = context.Configuration.GetConnectionString("AzureServiceBusConnectionString");
        transport.ConnectionString(connectionString);

        transport.Routing().RouteToEndpoint(typeof(Ping), "Receiver");

        endpointConfiguration.EnableInstallers();
        endpointConfiguration.AuditProcessedMessagesTo("audit");

        return endpointConfiguration;
    })
    .ConfigureServices(services => services.AddHostedService<SenderWorker>())
    .Build();

await host.RunAsync();
```

Es gibt hier einiges zu entpacken, deshalb gehen wir Schritt für Schritt vor.

### <a name="configure-a-host-for-the-endpoint"></a>Konfigurieren eines Hosts für den Endpunkt

Hosting und Protokollierung werden mit den standardmäßigen [Optionen für den generischen Microsoft-Host](/dotnet/core/extensions/generic-host) generiert. Vorerst ist der Endpunkt zur Ausführung als Konsolenanwendung konfiguriert, kann aber mithilfe minimaler Änderungen in Azure Functions ausgeführt werden. Dies wird später in diesem Artikel erläutert.

### <a name="configure-the-nservicebus-endpoint"></a>Konfigurieren des NServiceBus-Endpunkts

Als Nächstes weisen Sie den Host an, NServiceBus mit der Erweiterungsmethode `.UseNServiceBus(…)` zu verwenden. Die Methode verwendet eine Rückruffunktion zur Rückgabe eines Endpunkts, der bei Ausführung des Hosts gestartet wird.

Legen Sie in der Endpunktkonfiguration `AzureServiceBus` für unseren Transport fest, und geben Sie eine Verbindungszeichenfolge aus `appsettings.json` an. Als Nächstes richten Sie das Routing so ein, dass Nachrichten vom Typ `Ping` an einen Endpunkt mit dem Namen „Receiver“ gesendet werden. So kann NServiceBus den Prozess des Nachrichtenversands an das Ziel automatisieren, ohne dass die Adresse des Empfängers benötigt wird.

Der Aufruf von `EnableInstallers` richtet unsere Topologie im Azure Service Bus-Namespace ein, wenn der Endpunkt gestartet wird, und erstellt bei Bedarf die erforderlichen Warteschlangen. In Produktionsumgebungen stellt die [operative Skripterstellung](https://docs.particular.net/transports/azure-service-bus/operational-scripting) eine weitere Option zum Erstellen der Topologie dar.

### <a name="set-up-background-service-to-send-messages"></a>Einrichten des Hintergrunddiensts zum Senden von Nachrichten

Der letzte Teil des Senders ist `SenderWorker`, ein Hintergrunddienst, der so konfiguriert ist, dass jede Sekunde eine Nachricht an `Ping` wird.

```csharp
public class SenderWorker : BackgroundService
{
    private readonly IMessageSession messageSession;
    private readonly ILogger<SenderWorker> logger;

    public SenderWorker(IMessageSession messageSession, ILogger<SenderWorker> logger)
    {
        this.messageSession = messageSession;
        this.logger = logger;
    }

    protected override async Task ExecuteAsync(CancellationToken stoppingToken)
    {
        try
        {
            var round = 0;
            while (!stoppingToken.IsCancellationRequested)
            {
                await messageSession.Send(new Ping { Round = round++ })
                    .ConfigureAwait(false);

                logger.LogInformation($"Message #{round}");

                await Task.Delay(1_000, stoppingToken)
                    .ConfigureAwait(false);
            }
        }
        catch (OperationCanceledException)
        {
            // graceful shutdown
        }
    }
}
```

Das in `IMessageSession` verwendete `ExecuteAsync` wird in `SenderWorker` eingefügt und ermöglicht es, mithilfe von NServiceBus Nachrichten außerhalb eines Meldungshandlers zu senden. Das in `Sender` konfigurierte Routing gibt das Ziel der `Ping`-Nachrichten an. Die Topologie des Systems (welche Nachrichten werden an welche Adressen weitergeleitet) ist vom Geschäftscode getrennt.

Die Sender-Anwendung enthält außerdem einen `PongHandler`. Auf diesen kommen wir zurück, nachdem wir im nächsten Abschnitt den Empfänger besprochen haben.

## <a name="set-up-the-receiver"></a>Einrichten des Empfängers

Der Empfänger ist ein Endpunkt, der auf eine `Ping`-Nachricht lauscht, den Empfang einer Nachricht protokolliert und dem Sender antwortet. In diesem Abschnitt gehen wir kurz auf die Endpunktkonfiguration ein, die der des Senders ähnelt, und wenden uns dann dem Meldungshandler zu.

Richten Sie den Senden ebenso wie den Empfänger mithilfe des generischen Microsoft-Hosts als Konsolenanwendung ein. Sie verwendet die gleiche Protokollierungs- und Endpunktkonfiguration (mit Azure Service Bus als Nachrichtentransport), aber mit einem anderen Namen, um sie vom Sender zu unterscheiden:

```csharp
var endpointConfiguration = new EndpointConfiguration("Receiver");
```

Da dieser Endpunkt nur dem Ursprung antwortet und keine neuen Unterhaltungen beginnt, wird keine Routingkonfiguration benötigt. Es ist auch kein Hintergrundworker wie beim Sender erforderlich, weil der Endpunkt nur antwortet, wenn er eine Nachricht empfängt.

### <a name="the-ping-message-handler"></a>Ping-Meldungshandler

Das Receiver-Projekt enthält einen _Meldungshandler_ mit dem Namen `PingHandler`:

```csharp
public class PingHandler : NServiceBus.IHandleMessages<Ping>
{
    private readonly ILogger<PingHandler> logger;

    public PingHandler(ILogger<PingHandler> logger)
    {
        this.logger = logger;
    }

    public async Task Handle(Ping message, IMessageHandlerContext context)
    {
        logger.LogInformation($"Processing Ping message #{message.Round}");

        // throw new Exception("BOOM");

        var reply = new Pong { Acknowledgement = $"Ping #{message.Round} processed at {DateTimeOffset.UtcNow:s}" };

        await context.Reply(reply);
    }
}
```

Ignorieren wir vorerst den auskommentierten Code. Wir kommen später auf diesen zurück, wenn wir über die Wiederherstellung nach einem Fehler sprechen.

Die Klasse implementiert `IHandleMessages<Ping>`, die eine Methode definiert: `Handle`. Diese Schnittstelle weist NServiceBus an, dass der Endpunkt beim Empfang einer Nachricht vom Typ `Ping` diese über die `Handle`-Methode in diesem Handler verarbeiten soll. Die Methode `Handle` verwendet die Nachricht selbst als Parameter sowie `IMessageHandlerContext`, um weitere Nachrichtenvorgänge zu unterstützen, z. B. das Senden von Antworten oder Befehlen oder das Veröffentlichen von Ereignissen.

Unser `PingHandler` ist simpel: Wenn eine `Ping`-Nachricht empfangen wird, werden die Nachrichtendetails protokolliert, und dem Sender wird mit einer neuen `Pong`-Nachricht geantwortet.

> [!NOTE]
> In der Sender-Konfiguration haben Sie angegeben, dass `Ping`-Nachrichten an den Empfänger weitergeleitet werden sollen. NServiceBus fügt den Nachrichten Metadaten hinzu, die unter anderem den Ursprung der Nachricht angeben. Aus diesem Grund müssen Sie keine Routingdaten für die `Pong`-Antwortnachricht angeben. Sie werden automatisch zurück an ihren Ursprung weitergeleitet: den Sender.
>

Wenn Sender und Empfänger richtig konfiguriert sind, können Sie jetzt die Lösung ausführen.

## <a name="run-the-solution"></a>Ausführen der Lösung

Um die Lösung zu starten, müssen Sie sowohl das Sender- als auch das Receiver-Projekt ausführen. Wenn Sie Visual Studio Code verwenden, starten Sie die Konfiguration "Alle debuggen". Bei Verwendung von Visual Studio konfigurieren Sie die Lösung so, dass sowohl das Sender- als auch das Receiver-Projekt gestartet wird:

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf die Lösung.
1. Wählen Sie „Startprojekte festlegen“ aus.
1. Wählen Sie **Mehrere Startprojekte** aus.
1. Wählen Sie sowohl für das Sender- als auch für das Receiver-Projekt in der Dropdownliste „Starten“ aus.

Starten Sie die Lösung. Es werden zwei Konsolenanwendungen angezeigt: eine für den Sender und eine für den Empfänger

# <a name="sender"></a>[Sender](#tab/Sender)

:::image type="content" source="./media/nservicebus/sender.png" alt-text="Abbildung des Endpunkts, der Ping-Nachrichten an den Empfänger sendet":::

# <a name="receiver"></a>[Empfänger](#tab/Receiver)

:::image type="content" source="./media/nservicebus/receiver.png" alt-text="Abbildung des Endpunkts, der Ping-Nachrichten vom Sender empfängt":::

---

Beachten Sie, dass in der Sender-Anwendung aufgrund des Hintergrundauftrags `Ping`jede Sekunde eine Nachricht gesendet wird`SenderWorker`. Die Receiver-Anwendung zeigt die Details jeder empfangenen `Ping`-Nachricht an, und der Sender protokolliert die Details jeder `Pong`-Nachricht, die er als Antwort erhält.

Nun, da alles funktioniert, fügen Sie ein paar Fehler ein.

## <a name="resilience-in-action"></a>Resilienz in Aktion

Fehler gehören zum Lebenszyklus von Softwaresystemen. Das Auftreten von Codefehlern ist unvermeidlich und kann auf verschiedene Gründe zurückzuführen sein – z. B. auf Netzwerkausfälle, Datenbanksperren, Änderungen in einer Drittanbieter-API oder auf ganz normale Programmierfehler.

NServiceBus verfügt über robuste Wiederherstellbarkeitsfeatures für die Behandlung von Fehlern. Wenn ein Meldungshandler fehlschlägt, werden Nachrichten basierend auf einer vordefinierten Richtlinie automatisch erneut gesendet. Es gibt zwei Arten von Wiederholungsrichtlinien: sofortige Wiederholungen und verzögerte Wiederholungen. Ihre Funktionsweise lässt sich am besten beschreiben, wenn man sie in Aktion sieht. Fügen wir dem Receiver-Endpunkt eine Wiederholungsrichtlinie hinzu:

1. Öffnen Sie `Program.cs` im Sender-Projekt.
1. Fügen Sie nach der Zeile `.EnableInstallers` den folgenden Code hinzu:

```csharp
endpointConfiguration.SendFailedMessagesTo("error");
var recoverability = endpointConfiguration.Recoverability();
recoverability.Immediate(
    immediate =>
    {
        immediate.NumberOfRetries(3);
    });
recoverability.Delayed(
    delayed =>
    {
        delayed.NumberOfRetries(2);
        delayed.TimeIncrease(TimeSpan.FromSeconds(5));
    });
```

Bevor wir die Funktionsweise dieser Richtlinie besprechen, sehen wir sie uns in Aktion an. Um die Wiederherstellbarkeitsrichtlinie testen zu können, müssen wir einen Fehler simulieren. Öffnen Sie den `PingHandler`-Code im Receiver-Projekt, und heben Sie die Auskommentierung dieser Zeile auf:

```csharp
throw new Exception("BOOM");
```

Wenn der Empfänger jetzt eine `Ping`-Nachricht verarbeitet, tritt ein Fehler auf. Starten Sie die Lösung neu, und lassen Sie uns sehen, was im Receiver-Projekt passiert. 

Mit unserem weniger zuverlässigen `PingHandler` schlagen alle unsere Nachrichten fehl. Sie können sehen, dass die Wiederholungsrichtlinie für diese Nachrichten greift. Wenn eine Nachricht zum ersten Mal fehlschlägt, wird sie sofort bis zu dreimal wiederholt:

:::image type="content" source="./media/nservicebus/immediate-retries.png" alt-text="Abbildung der Richtlinie für die sofortige Wiederholung, die Nachrichten bis zu 3-mal erneut sendet":::

Natürlich tritt weiterhin ein Fehler auf. Wenn die drei Versuche zur sofortigen Wiederholung aufgebraucht sind, wird die Richtlinie für die verzögerte Wiederholung aktiviert, und die Nachricht wird für 5 Sekunden zurückgestellt:

:::image type="content" source="./media/nservicebus/delayed-retries.png" alt-text="Abbildung der Richtlinie für verzögerten Wiederholung, die Nachrichten in Schritten von jeweils 5 Sekunden zurückstellt, bevor eine weitere Runde sofortiger Wiederholungsversuche durchgeführt wird":::

 Nachdem diese 5 Sekunden vergangen sind, werden für die Nachricht drei neue Wiederholungsversuche ausgeführt (d. h. eine weitere Iteration der Richtlinie für die sofortige Wiederholung). Diese schlagen ebenfalls fehl, und NServiceBus stellt die Nachricht erneut zurück (dieses Mal für 10 Sekunden), bevor ein neuer Versuch erfolgt.

Wenn `PingHandler` nach Ausführung der vollständigen Wiederholungsrichtlinie weiterhin nicht erfolgreich ist, wird die Nachricht in einer _zentralen_ Fehlerwarteschlange namens `error` platziert, wie durch den Aufruf von `SendFailedMessagesTo` definiert. 

:::image type="content" source="./media/nservicebus/failed-message.png" alt-text="Abbildung der Fehlermeldung":::

Das Konzept einer zentralen Fehlerwarteschlange unterscheidet sich vom Mechanismus für unzustellbare Nachrichten in Azure Service Bus, der für jede Verarbeitungswarteschlange eine Warteschlange für unzustellbare Nachrichten umfasst. Mit NServiceBus fungieren die Warteschlangen für unzustellbare Nachrichten in Azure Service Bus als echte Warteschlangen für nicht verarbeitbare Nachrichten, während Nachrichten, die in die zentrale Fehlerwarteschlange eingereiht werden, bei Bedarf zu einem späteren Zeitpunkt erneut verarbeitet werden können.

Die Wiederholungsrichtlinie trägt dazu bei [verschiedene Arten von Fehlern](https://particular.net/blog/but-all-my-errors-are-severe) zu beheben, die oft vorübergehender Natur sind oder nur kurzzeitig auftreten. Anders ausgedrückt: Fehler sind häufig temporär und können durch das erneute Verarbeiten der Nachricht nach einer kurzen Verzögerung beseitigt werden. Beispiele hierfür sind Netzwerkfehler, Datenbanksperren und Ausfälle von Drittanbieter-APIs.

Sobald sich eine Nachricht in der Fehlerwarteschlange befindet, können Sie die Nachrichtendetails im Tool Ihrer Wahl untersuchen und dann entscheiden, wie Sie weiter vorgehen möchten. Wenn wir z. B. [ServicePulse](https://particular.net/servicepulse) verwenden, ein Überwachungstool von Particular Software, können wir Details zur Nachricht und den Grund für den Fehler anzeigen:

:::image type="content" source="./media/nservicebus/servicepulse.png" alt-text="Abbildung von ServicePulse von Particular Software":::

Nachdem Sie die Details untersucht haben, können Sie die Nachricht zur Verarbeitung zurück an die ursprüngliche Warteschlange senden. Sie können die Nachricht auch bearbeiten, bevor Sie dies tun. Wenn in der Fehlerwarteschlange mehrere Nachrichten vorhanden sind, bei denen aus demselben Grund ein Fehler aufgetreten ist, können diese als Batch an ihre ursprünglichen Ziele zurückgesendet werden.

Als Nächstes sollten wir bestimmen, wo in Azure wir unsere Lösung bereitstellen.

## <a name="where-to-host-the-services-in-azure"></a>Wo sollten die Dienste in Azure gehostet werden?

In diesem Beispiel werden die Sender- und Receiver-Endpunkte so konfiguriert, dass sie als Konsolenanwendungen ausgeführt werden. Die Endpunkte können auch in verschiedenen Azure-Diensten gehostet werden, beispielsweise in Azure Functions, Azure-App Services, Azure Container Instances, Azure Kubernetes Services und Azure-VMs. Nachstehend wird beispielsweise gezeigt, wie der Sender-Endpunkt zur Ausführung als Azure-Funktion konfiguriert werden kann:

```csharp
[assembly: FunctionsStartup(typeof(Startup))]
[assembly: NServiceBusEndpointName("Sender")]

public class Startup : FunctionsStartup
{
    public override void Configure(IFunctionsHostBuilder builder)
    {
        builder.UseNServiceBus(() =>
        {
            var configuration = new ServiceBusTriggeredEndpointConfiguration("Sender");
            var transport = configuration.AdvancedConfiguration.Transport;
            transport.Routing().RouteToEndpoint(typeof(Ping), "Receiver");

            return configuration;
        });
    }
}
```

Weitere Informationen zur Verwendung von NServiceBus mit Functions finden Sie unter [Azure Functions mit Azure Service Bus](https://docs.particular.net/nservicebus/hosting/azure-functions/service-bus) in der NServiceBus-Dokumentation.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Verwendung von NServiceBus in Azure finden Sie in den folgenden Artikeln:

- [Azure Service Bus-Beispiel zum Senden/Antworten](https://docs.particular.net/samples/azure-service-bus-netstandard/send-reply/)
- [Verwenden von NServiceBus mit Azure Functions](https://docs.particular.net/nservicebus/hosting/azure-functions/service-bus)
- [Azure Service Bus-Transport in NServiceBus](https://docs.particular.net/transports/azure-service-bus/)
- [NServiceBus und Azure](https://docs.particular.net/nservicebus/azure/)
- [NServiceBus](https://particular.net/nservicebus)
- [NServiceBus-Schnellstarttutorial](https://docs.particular.net/tutorials/quickstart)
