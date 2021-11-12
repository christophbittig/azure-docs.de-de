---
title: Ausführen einer Azure-Funktion als Reaktion auf ein Blobaktivierungsereignis
titleSuffix: Azure Storage
description: Erfahren Sie, wie Sie eine Azure-Funktion mit .NET entwickeln und dann Azure Event Grid so konfigurieren, dass die Funktion als Reaktion auf ein Ereignis ausgeführt wird, das ausgelöst wird, wenn ein Blob von der Archivebene rehydriert wird.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/25/2021
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.openlocfilehash: beaf5e72c74e066a0fc517d12100cb1703928b71
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131441512"
---
# <a name="run-an-azure-function-in-response-to-a-blob-rehydration-event"></a>Ausführen einer Azure-Funktion als Reaktion auf ein Blobaktivierungsereignis

Um einen Blob zu lesen, der sich in der Archivschicht befindet, müssen Sie den Blob zunächst in die Heiß- oder Kühlschicht rehydrieren. Der Aktivierungsvorgang kann einige Stunden dauern. Statt den Status des Aktivierungsvorgangs wiederholt abzurufen, können Sie [Azure Event Grid](../../event-grid/overview.md) so konfigurieren, dass ein Ereignis ausgelöst wird, wenn der Blobaktivierungsvorgang abgeschlossen ist, und dieses Ereignis in Ihrer Anwendung behandeln.

Wenn ein Ereignis eintritt, sendet Event Grid das Ereignis über einen Endpunkt an einen Ereignishandler. Als Ereignishandler können mehrere Azure-Dienste fungieren, z. B. [Azure Functions](../../azure-functions/functions-overview.md). Eine Azure-Funktion ist ein Codeblock, der als Reaktion auf ein Ereignis ausgeführt werden kann. In dieser Anleitung werden Sie durch die Schritte zum Entwickeln einer Azure-Funktion und zum anschließenden Konfigurieren von Event Grid geführt, um die Funktion als Reaktion auf ein Ereignis auszuführen, das bei Aktivierung eines Blobs eintritt.

In diesem Artikel erfahren Sie, wie Sie eine Azure-Funktion mit .NET in Visual Studio erstellen und testen. Sie können Azure Functions mit verschiedenen lokalen Entwicklungsumgebungen und in einer Reihe von unterschiedlichen Programmiersprachen entwickeln. Weitere Informationen zu den Sprachen, die für Azure Functions unterstützt werden, finden Sie unter [In Azure Functions unterstützte Sprachen](../../azure-functions/supported-languages.md). Weitere Informationen zu den Entwicklungsoptionen für Azure Functions finden Sie unter [Lokales Codieren und Testen von Azure Functions](../../azure-functions/functions-develop-local.md).

Weitere Informationen zur Rehydrierung von Blobs aus der Archivebene finden Sie unter [Übersicht über die Rehydrierung von Blobs aus der Archivebene](archive-rehydrate-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel erfahren Sie, wie Sie eine Azure-Funktion mit .NET in [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) entwickeln. Sie können Visual Studio Community kostenlos installieren. Vergewissern Sie sich, dass Sie [Visual Studio für die Azure-Entwicklung mit .NET konfigurieren](/dotnet/azure/configure-visual-studio).

Zum lokalen Debuggen der Azure-Funktion benötigen Sie ein Tool, das HTTP-Anforderungen senden kann, z. B. Postman.

Ein [Azure-Abonnement](../../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing) ist erforderlich. Wenn Sie noch nicht über ein Konto verfügen, [erstellen Sie ein kostenloses Konto](https://azure.microsoft.com/free/dotnet/), bevor Sie beginnen.

## <a name="create-an-azure-function-app"></a>Erstellen einer Azure Function-App

Eine Funktions-App ist eine Azure-Ressource, die als Container für Azure Functions fungiert. Sie können eine neue oder eine vorhandene Funktions-App verwenden, um die in diesem Artikel beschriebenen Schritte auszuführen.

Führen Sie die folgenden Schritte aus, um eine Funktions-App im Azure-Portal zu erstellen:

1. Suchen Sie im Azure-Portal nach *Funktions-App*. Wählen Sie das Symbol **Funktions-App** aus, um die Liste der Funktions-Apps in Ihrem Abonnement aufzurufen.
1. Wählen Sie die Schaltfläche **Erstellen** aus, um eine neue Funktions-App zu erstellen.
1. Geben Sie auf der Registerkarte **Grundlagen** eine Ressourcengruppe an, und geben Sie einen eindeutigen Namen für die neue Funktions-App ein.
1. Stellen Sie sicher, dass die Option **Veröffentlichen** auf *Code* festgelegt ist.
1. Wählen Sie in der Dropdownliste **Runtimestapel** den Eintrag *.NET* aus. Im Feld **Version** wird automatisch die aktuelle Version von .NET Core eingetragen.
1. Wählen Sie die Region für die neue Funktions-App aus.

    :::image type="content" source="media/archive-rehydrate-handle-event/create-function-app-basics-tab.png" alt-text="Screenshot zum Erstellen einer neuen Funktions-App in Azure, Registerkarte „Grundlagen“":::

1. Nachdem Sie die Registerkarte **Grundlagen** ausgefüllt haben, wechseln Sie zur Registerkarte **Hosting**.
1. Wählen Sie auf der Registerkarte **Hosting** das Speicherkonto aus, in dem Ihre Azure-Funktion gespeichert werden soll. Sie können ein vorhandenes Speicherkonto auswählen oder ein neues erstellen.
1. Stellen Sie sicher, dass das Feld **Betriebssystem** auf *Windows* festgelegt ist.
1. Wählen Sie im Feld **Plantyp** den Eintrag *Verbrauch (serverlos)* aus. Weitere Informationen zu diesem Plan finden Sie unter [Hosting des Azure Functions-Verbrauchstarifs](../../azure-functions/consumption-plan.md).

    :::image type="content" source="media/archive-rehydrate-handle-event/create-function-app-hosting-tab.png" alt-text="Screenshot zum Erstellen einer neuen Funktions-App in Azure, Registerkarte „Hosting“":::

1. Wählen Sie zum Erstellen der neuen Funktions-App **Überprüfen + erstellen** aus.

Weitere Informationen zum Konfigurieren der Funktions-App finden Sie in der Azure Functions-Dokumentation unter [Verwalten Ihrer Funktions-App](../../azure-functions/functions-how-to-use-azure-function-app-settings.md).

## <a name="create-an-azure-function-as-an-event-grid-trigger"></a>Erstellen einer Azure-Funktion als Event Grid-Trigger

Als Nächstes erstellen Sie eine Azure-Funktion, die ausgeführt wird, wenn ein Blob in einem bestimmten Speicherkonto aktiviert wird. Führen Sie die folgenden Schritte aus, um eine Azure-Funktion in Visual Studio mit C# und .NET Core zu erstellen:

1. Starten Sie Visual Studio 2019, und erstellen Sie ein neues Azure Functions-Projekt. Weitere Informationen finden Sie in der Anleitung unter [Erstellen eines Funktionen-App-Projekts](../../azure-functions/functions-create-your-first-function-visual-studio.md#create-a-function-app-project).
1. Wählen Sie im Schritt **Neue Azure Functions-Anwendung erstellen** die folgenden Werte aus:
    - Die Azure Functions-Runtime ist standardmäßig auf **Azure Functions v3 (.NET Core)** festgelegt. Microsoft empfiehlt, diese Version der Azure Functions-Runtime zu verwenden.
    - Wählen Sie in der Liste der möglichen Trigger **Event Grid-Trigger** aus. Unter [Verwenden einer Funktion als Ereignishandler für Event Grid-Ereignisse](../../event-grid/handler-functions.md) erfahren Sie, was für einen Event Grid-Trigger als Trigger für die Behandlung eines Blob Storage-Ereignisses mit einer Azure-Funktion spricht.
    - Die Einstellung **Speicherkonto** gibt an, wo Ihre Azure-Funktion gespeichert wird. Sie können ein vorhandenes Speicherkonto auswählen oder ein neues erstellen.
1. Wählen Sie **Erstellen** aus, um das neue Projekt in Visual Studio zu erstellen.
1. Benennen Sie dann die Klasse und die Azure-Funktion wie unter [Umbenennen der Funktion](../../azure-functions/functions-create-your-first-function-visual-studio.md#rename-the-function) beschrieben um. Wählen Sie einen für Ihr Szenario geeigneten Namen.
1. Wählen Sie in Visual Studio **Tools** | **NuGet-Paket-Manager** | **Paket-Manager-Konsole**, und installieren Sie dann die folgenden Pakete über die Konsole:

    ```powershell
    Install-Package Azure.Storage.Blobs
    Install-Package Microsoft.ApplicationInsights.WorkerService
    Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights
    ```

1. Fügen Sie in der Klassendatei für Ihre Azure-Funktion die folgenden using-Anweisungen ein:

    ```csharp
    using System;
    using System.IO;
    using System.Text;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.EventGrid.Models;
    using Microsoft.Azure.WebJobs.Extensions.EventGrid;
    using Microsoft.Extensions.Logging;
    using Azure;
    using Azure.Storage.Blobs;
    using Azure.Storage.Blobs.Models;
    ```

1. Suchen Sie in der Klassendatei nach der **Run**-Methode. Diese Methode wird bei Eintritt eines Ereignisses ausgeführt. Fügen Sie den folgenden Code in den Methodenkörper der **Run**-Methode ein. Denken Sie daran, die Platzhalterwerte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

    ```csharp
    // When either Microsoft.Storage.BlobCreated or Microsoft.Storage.BlobTierChanged
    // event occurs, write the event details to a log blob in the same container
    // as the event subject (the blob for which the event occurred).

    // Create a unique name for the log blob.
    string logBlobName = string.Format("function-log-{0}.txt", DateTime.UtcNow.Ticks);

    // Populate connection string with your Shared Key credentials.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net";

    // Get data from the event.
    dynamic data = eventGridEvent.Data;
    string eventBlobUrl = Convert.ToString(data.url);
    string eventApi = Convert.ToString(data.api);

    // Build string containing log information.
    StringBuilder eventInfo = new StringBuilder();
    eventInfo.AppendLine(string.Format("{0} operation occurred.", eventApi));
    eventInfo.AppendLine(string.Format("Blob URL: {0}", eventBlobUrl));
    eventInfo.AppendLine($@"Additional event details:
        Id=[{eventGridEvent.Id}]
        EventType=[{eventGridEvent.EventType}]
        EventTime=[{eventGridEvent.EventTime}]
        Subject=[{eventGridEvent.Subject}]
        Topic=[{eventGridEvent.Topic}]");

    // If event was BlobCreated and API call was CopyBlob, respond to the event.
    bool copyBlobEventOccurred = (eventGridEvent.EventType == "Microsoft.Storage.BlobCreated") &&
                                 (eventApi == "CopyBlob");

    // If event was BlobTierChanged and API call was SetBlobTier, respond to the event.
    bool setTierEventOccurred = (eventGridEvent.EventType == "Microsoft.Storage.BlobTierChanged") &&
                                (eventApi == "SetBlobTier");

    // If one of these two events occurred, write event info to a log blob.
    if (copyBlobEventOccurred | setTierEventOccurred)
    {
        // Create log blob in same account and container.
        BlobUriBuilder logBlobUriBuilder = new BlobUriBuilder(new Uri(eventBlobUrl))
        {
            BlobName = logBlobName
        };

        BlobClient logBlobClient = new BlobClient(ConnectionString,
                                                  logBlobUriBuilder.BlobContainerName,
                                                  logBlobName);

        byte[] byteArray = Encoding.ASCII.GetBytes(eventInfo.ToString());

        try
        {
            // Write the log info to the blob.
            // Overwrite if the blob already exists.
            using (MemoryStream memoryStream = new MemoryStream(byteArray))
            {
                BlobContentInfo blobContentInfo =
                    logBlobClient.Upload(memoryStream, overwrite: true);
            }
        }
        catch (RequestFailedException e)
        {
            Console.WriteLine(e.Message);
            throw;
        }
    }
    ```

Weitere Informationen zum Entwickeln von Azure Functions finden Sie im [Leitfaden zur Entwicklung von Azure Functions](../../azure-functions/functions-reference.md).

Unter [Azure Blob Storage als Event Grid-Quelle](../../event-grid/event-schema-blob-storage.md) erfahren Sie, welche Informationen beim Veröffentlichen eines Blob Storage-Ereignisses für einen Ereignishandler enthalten sind.

## <a name="run-the-azure-function-locally-in-the-debugger"></a>Lokales Ausführen der Azure-Funktion im Debugger

Um den Code der Azure-Funktion lokal zu testen, müssen Sie manuell eine HTTP-Anforderung senden, die das Ereignis auslöst. Sie können die Anforderung mit einem Tool wie Postman senden.

Am Anfang der Klassendatei für Ihre Azure-Funktion finden Sie einen URL-Endpunkt, den Sie zum Testen in einer lokalen Umgebung verwenden können. Durch Senden der Anforderung mithilfe dieser URL wird das Ereignis in der lokalen Umgebung ausgelöst, sodass Sie den Code debuggen können. Die URL besitzt das folgende Format:

```http
http://localhost:7071/runtime/webhooks/EventGrid?functionName={functionname}
```

Bei der Anforderung, die Sie an diesen Endpunkt senden, handelt es sich um eine simulierte Anforderung. Es werden keine Daten aus Ihrem Azure Storage-Konto gesendet oder empfangen.

Führen Sie die folgenden Schritte aus, um eine Anforderung zu erstellen und an diesen Endpunkt zu senden. Dieses Beispiel zeigt, wie Sie die Anforderung mit Postman senden.

1. Erstellen Sie in Postman eine neue Anforderung.
1. Fügen Sie die oben angegebene URL in das Feld für die Anforderungs-URL ein, wobei Sie `{functionname}` durch den Namen Ihrer Funktion ersetzen und die geschweiften Klammern entfernen. Stellen Sie sicher, dass das Anforderungsverb auf GET festgelegt ist.

    :::image type="content" source="media/archive-rehydrate-handle-event/trigger-azure-function-postman-url.png" alt-text="Screenshot zum Angeben der lokalen URL für den Ereignistrigger in Postman ":::

1. Fügen Sie den Header **Content-Type** hinzu, und legen Sie ihn auf *application/json* fest.
1. Fügen Sie den Header **aeg-event-type** hinzu, und legen Sie ihn auf *Notification* fest.

    :::image type="content" source="media/archive-rehydrate-handle-event/trigger-azure-function-postman-headers.png" alt-text="Screenshot der Headerkonfiguration für die lokale Anforderung zum Auslösen des Ereignisses":::

1. Geben Sie in Postman den Anforderungstext an, wobei Sie den Texttyp auf *JSON* und das Format auf *raw* festlegen. Im folgenden Beispiel wird die Anforderung **Blob kopieren** simuliert. Ersetzen Sie die Platzhalterwerte in spitzen Klammern durch Ihre eigenen Werte. Beachten Sie, dass es nicht notwendig ist, die Werte für Datum, Uhrzeit und Bezeichner zu ändern, da es sich um eine simulierte Anforderung handelt:

    ```json
    [{
      "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>",
      "subject": "/blobServices/default/containers/<container-name>/blobs/<blob-name>",
      "eventType": "Microsoft.Storage.BlobCreated",
      "id": "2bfb587b-501e-0094-2746-8b2884065d32",
      "data": {
        "api": "CopyBlob",
        "clientRequestId": "3d4dedc7-6c27-4816-9405-fdbfa806b00c",
        "requestId": "2bfb587b-501e-0094-2746-8b2884000000",
        "eTag": "0x8D9595DCA505BDF",
        "contentType": "text/plain",
        "contentLength": 48,
        "blobType": "BlockBlob",
        "url": "https://<storage-account>.blob.core.windows.net/<container-name>/<blob-name>",
        "sequencer": "0000000000000000000000000000201B00000000004092a5",
        "storageDiagnostics": {
          "batchId": "8a92736a-6006-0026-0046-8bd7f5000000"
        }
      },
      "dataVersion": "",
      "metadataVersion": "1",
      "eventTime": "2021-08-07T04:42:41.0730463Z"
    }]
    ```

1. Setzen Sie in Visual Studio ggf. Breakpoints in Ihrem Code, und drücken Sie **F5**, um den Debugger auszuführen.
1. Wählen Sie in Postman die Schaltfläche **Send**, um die Anforderung an den Endpunkt zu senden.

Wenn Sie die Anforderung senden, ruft Event Grid die Azure-Funktion auf, und Sie können sie wie gewohnt debuggen. Weitere Informationen und Beispiele finden Sie in der Azure Functions-Dokumentation unter [Manuelles Bereitstellen der Anforderung](../../azure-functions/functions-bindings-event-grid-trigger.md#manually-post-the-request).

Die Anforderung, die das Ereignis auslöst, wird simuliert, die Azure-Funktion, die ausgeführt wird, wenn das Ereignis ausgelöst wird, schreibt jedoch Protokollinformationen in einen neuen Blob in Ihrem Speicherkonto. Im Azure-Portal können Sie den Inhalt des Blobs überprüfen und das Datum der letzten Änderung sehen, wie die folgende Abbildung zeigt:

:::image type="content" source="media/archive-rehydrate-handle-event/log-blob-contents-portal.png" alt-text="Screenshot des Inhalts des Protokollblobs im Azure-Portal":::

## <a name="publish-the-azure-function"></a>Veröffentlichen der Azure-Funktion

Nachdem Sie die Azure-Funktion lokal getestet haben, veröffentlichen Sie die Azure-Funktion im nächsten Schritt in der zuvor erstellten Azure-Funktions-App. Die Funktion muss veröffentlicht werden, damit Sie Event Grid für das Senden von Ereignissen im Speicherkonto an den Funktionsendpunkt konfigurieren können.

Führen Sie diese Schritte aus, um die Funktion zu veröffentlichen:

1. Klicken Sie im Projektmappen-Explorer auf das Azure Functions-Projekt und halten Sie die Maustaste gedrückt (oder klicken Sie mit der rechten Maustaste darauf), und wählen Sie **Veröffentlichen** aus.
1. Wählen Sie im Fenster **Veröffentlichen** als Ziel **Azure** und anschließend **Weiter** aus.
1. Wählen Sie als genaues Ziel **Azure-Funktions-App (Windows)** und dann **Weiter** aus.
1. Wählen Sie auf der Registerkarte **Functions-Instanz** im Dropdownmenü Ihr Azure-Abonnement aus, und suchen Sie dann in der Liste der verfügbaren Funktions-Apps nach Ihrer Azure-Funktions-App.
1. Stellen Sie sicher, dass das Kontrollkästchen **Aus Paketdatei ausführen** aktiviert ist.
1. Wählen Sie **Fertig stellen** aus, um die Veröffentlichung der Funktion vorzubereiten.
1. Vergewissern Sie sich auf der Seite **Veröffentlichen**, dass die Konfiguration korrekt ist. Wenn eine Warnung angezeigt wird, dass die Dienstabhängigkeit mit Application Insights nicht konfiguriert ist, können Sie sie auf dieser Seite konfigurieren.
1. Wählen Sie die Schaltfläche **Veröffentlichen** aus, um die Veröffentlichung der Azure-Funktion in der zuvor erstellten Azure-Funktions-App zu starten.

    :::image type="content" source="media/archive-rehydrate-handle-event/visual-studio-publish-azure-function.png" alt-text="Screenshot der Seite zum Veröffentlichen der Azure-Funktion in Visual Studio":::

Immer, wenn Sie Änderungen am Code in Ihrer Azure-Funktion vornehmen, müssen Sie die aktualisierte Funktion in Azure veröffentlichen.

## <a name="subscribe-to-blob-rehydration-events-from-a-storage-account"></a>Abonnieren von Blobaktivierungsereignissen aus dem Speicherkonto

Jetzt verfügen Sie über eine Funktions-App mit einer Azure-Funktion, die als Reaktion auf ein Ereignis ausgeführt werden kann. Als nächsten Schritt erstellen Sie ein Ereignisabonnement für Ihr Speicherkonto. Mit dem Ereignisabonnement wird das Speicherkonto so konfiguriert, dass als Reaktion auf einen Blobvorgang in Ihrem Speicherkonto ein Ereignis über Event Grid veröffentlicht wird. Event Grid sendet das Ereignis an den von Ihnen angegebenen Ereignishandlerendpunkt. In diesem Fall ist die im vorhergehenden Abschnitt erstellte Azure-Funktion der Ereignishandler.

Wenn Sie das Ereignisabonnement erstellen, können Sie filtern, welche Ereignisse an den Ereignishandler gesendet werden. Die zu erfassenden Ereignisse bei der Rehydrierung eines Blobs aus der Archivebene sind **Microsoft.Storage.BlobTierChanged**, was einem Vorgang [Set Blob Tier](/rest/api/storageservices/set-blob-tier) entspricht, und **Microsoft.Storage.BlobCreated**, was einem Vorgang [Copy Blob](/rest/api/storageservices/copy-blob) entspricht. Je nach Szenario behandeln Sie nur eines dieser Ereignisse.

Führen Sie zum Erstellen des Ereignisabonnements die folgenden Schritte aus:

1. Navigieren Sie im Azure-Portal zu dem Speicherkonto, das die zu rehydrierenden Blobs aus der Archivebene enthält.
1. Wählen Sie im linken Navigationsbereich die Einstellung **Ereignisse** aus.
1. Klicken Sie auf der Seite **Ereignisse** auf **Weitere Optionen**.
1. Wählen Sie **Ereignisabonnement erstellen** aus.
1. Geben Sie auf der Seite **Ereignisabonnement erstellen** im Abschnitt **Details zum Ereignisabonnement** einen Namen für das Ereignisabonnement ein.
1. Geben Sie im Abschnitt **Themendetails** einen Namen für das Systemthema ein. Das Systemthema stellt ein oder mehrere Ereignisse dar, die von Azure Storage veröffentlicht werden. Weitere Informationen zu Systemthemen finden Sie unter [Systemthemen in Azure Event Grid](../../event-grid/system-topics.md).
1. Wählen Sie im Abschnitt **Ereignistypen** die Ereignisse **Blob Created** (Blob erstellt) und **Blob Tier Changed** (Blobtarif geändert) aus. Je nachdem, wie Sie einen Blob aus dem Archiv-Tier rehydrieren wollen, wird eines dieser beiden Ereignisse ausgelöst.

    :::image type="content" source="media/archive-rehydrate-handle-event/select-event-types-portal.png" alt-text="Screenshot zur Auswahl der Ereignistypen für die Blobaktivierungsereignisse im Azure-Portal":::

1. Wählen Sie im Abschnitt **Endpunktdetails** im Dropdownmenü *Azure-Funktion* aus.
1. Wählen Sie **Endpunkt auswählen** aus, um die im vorherigen Abschnitt erstellte Funktion anzugeben. Wählen Sie im Dialogfeld **Azure-Funktion auswählen** das Abonnement, die Ressourcengruppe und die Funktions-App für Ihre Azure-Funktion aus. Wählen Sie schließlich im Dropdownmenü den Funktionsnamen und dann **Auswahl bestätigen** aus.

    :::image type="content" source="media/archive-rehydrate-handle-event/select-azure-function-endpoint-portal.png" alt-text="Screenshot zur Auswahl einer Azure-Funktion als Endpunkt für ein Event Grid-Abonnement":::

1. Wählen Sie die Schaltfläche **Erstellen** aus, um das Ereignisabonnement zu erstellen, und beginnen Sie damit, Ereignisse an den Ereignishandler der Azure-Funktion zu senden.

Weitere Informationen zu Ereignisabonnements finden Sie unter [Azure Event Grid – Begriffe](../../event-grid/concepts.md#event-subscriptions).

## <a name="test-the-azure-function-event-handler"></a>Testen des Ereignishandlers der Azure-Funktion

Zum Testen der Azure-Funktion können Sie ein Ereignis in dem Speicherkonto mit dem Ereignisabonnement auslösen. Das zuvor erstellte Ereignisabonnement filtert nach den beiden Ereignissen **Microsoft.Storage.BlobCreated** und **Microsoft.Storage.BlobTierChanged**. Wenn eines dieser Ereignisse ausgelöst wird, wird Ihre Azure-Funktion ausgelöst.

Mit der Azure-Funktion aus diesem Artikel wird in zwei Szenarien ein Protokollblob geschrieben:

- Wenn das Ereignis **Microsoft.Storage.BlobCreated** ist und der API-Vorgang **Blob kopieren** lautet.
- Wenn das Ereignis **Microsoft.Storage.BlobTierChanged** ist und der API-Vorgang **Blobtarif festlegen** lautet.

Unter diesen beiden Verfahren finden Sie Informationen zum Testen der Funktion durch Aktivieren eines Blobs:

- [Aktivieren eines Blobs mit einem Kopiervorgang](archive-rehydrate-to-online-tier.md#rehydrate-a-blob-with-a-copy-operation)
- [Aktivieren eines Blobs durch Ändern seiner Ebene](archive-rehydrate-to-online-tier.md#rehydrate-a-blob-by-changing-its-tier)

Nach Abschluss der Aktivierung wird das Protokollblob in den gleichen Container geschrieben, in dem sich auch das aktivierte Blob befindet. Nachdem Sie beispielsweise einen Blob mit einem Kopiervorgang rehydriert haben, können Sie im Azure-Portal sehen, dass der ursprüngliche Quell-Blob im Archive-Tier verbleibt, der vollständig rehydrierte Ziel-Blob im gezielten Online-Tier erscheint und der Protokoll-Blob, der von der Azure-Funktion erstellt wurde, ebenfalls in der Liste erscheint.

:::image type="content" source="media/archive-rehydrate-handle-event/copy-blob-archive-tier-rehydrated-with-log-blob.png" alt-text="Screenshot mit dem ursprünglichen Blob in der Archivebene, dem rehydrierten Blob in der Hot-Tier-Ebene und dem vom Event-Handler geschriebenen Log-Blob":::

Beachten Sie, dass das Kopieren eines Blobs je nach ausgewählter Einstellung für die Aktivierungspriorität bis zu 15 Stunden dauern kann. Wenn Sie die Aktivierungspriorität auf **Hoch** festlegen, wird die Aktivierung bei Blobs, die kleiner als 10 GB sind, möglicherweise in weniger als einer Stunde abgeschlossen. Aktivierungsvorgänge mit hoher Priorität verursachen allerdings höhere Kosten. Weitere Informationen finden Sie unter [Übersicht über die Rehydrierung von Klecksen aus der Archivebene](archive-rehydrate-overview.md).

> [!TIP]
> Obwohl das Ziel dieser Anleitung darin besteht, diese Ereignisse im Zusammenhang mit der Rehydrierung von BLOBs zu behandeln, kann es zu Testzwecken auch hilfreich sein, diese Ereignisse als Reaktion auf das Hochladen eines BLOBs oder die Änderung der Stufe eines Online-Blob (*i.e.* , von Hot zu Cool) zu beobachten, da das Ereignis sofort ausgelöst wird.

Weitere Informationen zum Filtern von Ereignissen in Event Grid finden Sie unter [Filtern von Ereignissen für Azure Event Grid](../../event-grid/how-to-filter-events.md).

## <a name="see-also"></a>Weitere Informationen

- [Heiße, kühle und Archiv-Zugriffsebenen für Blobdaten](access-tiers-overview.md)
- [Übersicht über die Rehydrierung von Klecksen aus der Archivebene](archive-rehydrate-overview.md)
- [Aktivieren eines archivierten Blobs auf einer Onlineebene](archive-rehydrate-to-online-tier.md)
- [Reacting to Blob storage events (preview)](storage-blob-event-overview.md) (Reagieren auf Blob Storage-Ereignisse (Vorschauversion))
