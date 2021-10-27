---
title: 'Schnellstart: Übermitteln eines Auftrags für Warteschlangen und Routing'
titleSuffix: An Azure Communication Services quickstart
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie einen Auftragsrouterclient, eine Verteilungsrichtlinie, eine Warteschlange und einen Auftrag in Ihrer Azure Communication Services-Ressource erstellen.
author: jasonshave
manager: phans
services: azure-communication-services
ms.author: jassha
ms.date: 10/18/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: ffad9a33c932300dbf52989536663215f8abb8f9
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130164766"
---
# <a name="quickstart-submit-a-job-for-queuing-and-routing"></a>Schnellstart: Übermitteln eines Auftrags für Warteschlangen und Routing

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

Führen Sie erste Schritte mit einem Azure Communication Services-Auftragsrouter aus, indem Sie Ihren Client einrichten. Anschließend können Kernfunktionen wie Warteschlangen, Richtlinien, Worker und Aufträge konfiguriert werden. Weitere Informationen zu Auftragsrouterkonzepten finden Sie in der [konzeptionellen Dokumentation zu Auftragsroutern](../../concepts/router/concepts.md).

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Eine aktive Communication Services-Ressource und eine Verbindungszeichenfolge. [Erstellen Sie eine Communication Services-Ressource](../create-communication-resource.md).

## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-c-application"></a>Erstellen einer neuen C#-Anwendung

Verwenden Sie in einem Konsolenfenster (z. B. cmd, PowerShell oder Bash) den Befehl `dotnet new` zum Erstellen einer neuen Konsolen-App mit dem Namen `JobRouterQuickstart`. Dieser Befehl erstellt ein einfaches „Hallo Welt“-C#-Projekt mit einer einzigen Quelldatei: **Program.cs**.

```console
dotnet new console -o JobRouterQuickstart
```

Wechseln Sie zum neu erstellten App-Ordner, und verwenden Sie den Befehl `dotnet build`, um Ihre Anwendung zu kompilieren.

```console
cd JobRouterQuickstart
dotnet build
```

### <a name="install-the-package"></a>Installieren des Pakets

Laden Sie das SDK während der privaten Vorschau von [GitHub](https://github.com/Azure/communication-preview/releases/tag/communication-job-router-net-v1.0.0-alpha.20211012.1) herunter.

> [!NOTE]
> Sie müssen Mitglied der privaten Vorschaugruppe sein, um die SDKs herunterladen zu können.

Fügen Sie am Anfang von **Program.cs** die folgenden `using`-Anweisungen hinzu, um die JobRouter-Namespaces einzuschließen.

```csharp
using Azure.Communication.JobRouter;
```

Ändern Sie die Signatur der Hauptfunktion (`Main`) so, dass sie asynchron (`async`) ist und eine Aufgabe (`Task`) zurückgibt.

```csharp
static async Task Main(string[] args)
{
  ...
}
```

## <a name="authenticate-the-job-router-client"></a>Authentifizieren des Auftragsrouterclients

Auftragsrouterclients können mithilfe Ihrer Verbindungszeichenfolge authentifiziert werden, die von einer Azure Communication Services-Ressource im Azure-Portal abgerufen wird.

```csharp
// Get a connection string to our Azure Communication Services resource.
var connectionString = "your_connection_string";
var client = new RouterClient(connectionString);
```

## <a name="create-a-distribution-policy"></a>Erstellen einer Verteilungsrichtlinie

Der Auftragsrouter verwendet eine Verteilungsrichtlinie, um zu entscheiden, wie Worker über verfügbare Aufträge und die Gültigkeitsdauer für die Benachrichtigungen informiert werden. Dies wird als **Angebote** bezeichnet. Erstellen Sie die Richtlinie, indem Sie die **ID**, einen **Namen**, einen Wert für **offerTTL** und einen **Verteilungsmodus** angeben.

```csharp
var distributionPolicy = await client.SetDistributionPolicyAsync(
    id: "Longest_Idle_45s_Min1Max10",
    name: "Longest Idle matching with a 45s offer expiration; min 1, max 10 offers",
    offerTTL: TimeSpan.FromSeconds(45),
    mode: new LongestIdleMode(
        minConcurrentOffers: 1,
        maxConcurrentOffers: 10)
);
```

## <a name="create-a-queue"></a>Erstellen einer Warteschlange

Aufträge werden in einer logischen Warteschlange organisiert. Erstellen Sie die Warteschlange, indem Sie eine **ID**, einen **Namen** und die ID des Objekts für die **Verteilungsrichtlinie** angeben, die Sie oben erstellt haben.

```csharp
var queue = await client.SetQueueAsync(
    id: "XBOX_Queue",
    name: "XBOX Queue",
    distributionPolicyId: distributionPolicy.Value.Id
);
```

## <a name="submit-a-job"></a>Übermitteln eines Auftrags
Die schnellste Einstiegsmöglichkeit besteht in der Angabe der ID der Warteschlange, der Priorität und der Workeranforderungen beim Übermitteln eines Auftrags. Im folgenden Beispiel wird ein Auftrag direkt an die **XBOX-Warteschlange** übermittelt. Dabei erfordern die Worker in dieser Warteschlange eine `Location`-Bezeichnung, die mit dem Namen `Edmonton` übereinstimmt.

```csharp
var job = await client.CreateJobAsync(
    channelId: ManagedChannels.AcsChatChannel,
    channelReference: "12345",
    queueId: queue.Value.Id,
    priority: 1,
    workerSelector: new List<LabelSelector>
    {
        new (
            key: "Location", 
            @operator: LabelOperator.Equal, 
            value: "Edmonton")
    });
```

## <a name="register-a-worker"></a>Registrieren eines Workers
Registrieren Sie einen Worker, indem Sie auf die zuvor erstellte Warteschlangen-ID sowie einen Wert für **capacity**, **labels** und die **Kanalkonfiguration** verweisen, um sicherzustellen, dass `EdmontonWorker` „XBOX_Queue“ zugewiesen wird.

```csharp
var edmontonWorker = await client.RegisterWorkerAsync(
    id: "EdmontonWorker",
    queueIds: new []{ queue.Value.Id },
    totalCapacity: 100,
    labels: new LabelCollection()
    {
        {"Location", "Edmonton"}
    },
    channelConfigurations: new List<ChannelConfiguration>
    {
        new (
            channelId: ManagedChannels.AcsVoiceChannel,
            capacityCostPerJob: 100)
    }
);
```

## <a name="query-the-worker-to-observe-the-job-offer"></a>Abfragen des Workers, um das Auftragsangebot zu beobachten
Verwenden Sie die Auftragsrouter-Clientverbindung, um den Worker abzufragen und die ID des Auftrags mit der ID zu vergleichen. 

```csharp
    // wait 500ms for the Job Router to offer the Job to the Worker
    Task.Delay(500).Wait();

    var result = await client.GetWorkerAsync(edmontonWorker.Value.Id);

    Console.WriteLine(
        $"Job ID: {job.Value.Id} offered to {edmontonWorker.Value.Id} " +
        $"should match Job ID attached to worker: {result.}");
```

Führen Sie die Anwendung mithilfe von `dotnet run` aus, und sehen Sie sich die Ergebnisse an.

```console
dotnet run

Job 6b83c5ad-5a92-4aa8-b986-3989c791be91 offered to EdmontonWorker should match Job ID from offer attached to worker: 6b83c5ad-5a92-4aa8-b986-3989c791be91
```

> [!NOTE]
> Wenn Sie die Anwendung mehrmals ausführen, wird jedes Mal ein neuer Auftrag in die Warteschlange eingereiht. Dies kann dazu führen, dass dem Worker ein anderer Auftrag als der angeboten wird, der beim Ausführen des obigen Codes erstellt wurde. Da dies die Anforderung verzerren kann, sollten Sie Aufträge in der Warteschlange ggf. jedes Mal entfernen. Informationen zum Verwalten einer Warteschlange oder eines Auftrags finden Sie in der SDK-Dokumentation.