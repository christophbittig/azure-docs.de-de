---
title: Verarbeiten des Änderungsfeeds in Azure Blob Storage | Microsoft-Dokumentation
description: Erfahren Sie, wie Änderungsfeedprotokolle in einer .NET-Clientanwendung verarbeitet werden.
author: tamram
ms.author: tamram
ms.date: 09/08/2020
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.custom: devx-track-csharp
ms.openlocfilehash: 9d43b91fcebff017d6d18ee736cfddc858650fc7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128620196"
---
# <a name="process-change-feed-in-azure-blob-storage"></a>Verarbeiten des Änderungsfeeds in Azure Blob Storage

Der Änderungsfeed stellt Transaktionsprotokolle für alle Änderungen bereit, die in den Blobs und den Blobmetadaten in Ihrem Speicherkonto auftreten. In diesem Artikel wird gezeigt, wie Sie Änderungsfeeddatensätze mithilfe der Change Feed Processor-Bibliothek für Blobs lesen.

Weitere Informationen zum Änderungsfeed finden Sie unter [Änderungsfeed in Azure Blob Storage](storage-blob-change-feed.md).

## <a name="get-the-blob-change-feed-processor-library"></a>Anfordern der Change Feed Processor-Bibliothek

1. Öffnen Sie ein Befehlsfenster (z. B. Windows PowerShell).
2. Installieren Sie in Ihrem Projektverzeichnis das NuGet-Paket [**Azure.Storage.Blobs.Changefeed**](https://www.nuget.org/packages/Azure.Storage.Blobs.ChangeFeed/).

```console
dotnet add package Azure.Storage.Blobs --version 12.5.1
dotnet add package Azure.Storage.Blobs.ChangeFeed --version 12.0.0-preview.4
```


## <a name="read-records"></a>Lesen von Datensätzen

> [!NOTE]
> Der Änderungsfeed ist eine unveränderliche und schreibgeschützte Entität in Ihrem Speicherkonto. Mehrere Anwendungen können den Änderungsfeed gleichzeitig und unabhängig voneinander nach Belieben lesen und verarbeiten. Datensätze werden nicht aus dem Änderungsfeed entfernt, wenn sie von einer Anwendung gelesen werden. Der Lese- oder Iterationszustand jedes Readers ist unabhängig und wird nur von der jeweiligen Anwendung verwaltet.

In diesem Beispiel werden alle Datensätze im Änderungsfeed durchlaufen und einer Liste hinzugefügt. Anschließend wird diese Liste an den Aufrufer zurückgegeben.

```csharp
public async Task<List<BlobChangeFeedEvent>> ChangeFeedAsync(string connectionString)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();

    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    // Get all the events in the change feed. 
    await foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedClient.GetChangesAsync())
    {
        changeFeedEvents.Add(changeFeedEvent);
    }

    return changeFeedEvents;
}
```

In diesem Beispiel werden einige Werte von jedem Datensatz in der Liste an die Konsole ausgegeben.

```csharp
public void showEventData(List<BlobChangeFeedEvent> changeFeedEvents)
{
    foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedEvents)
    {
        string subject = changeFeedEvent.Subject;
        string eventType = changeFeedEvent.EventType.ToString();
        string api = changeFeedEvent.EventData.Api;

        Console.WriteLine("Subject: " + subject + "\n" +
        "Event Type: " + eventType + "\n" +
        "Api: " + api);
    }
}
```

## <a name="resume-reading-records-from-a-saved-position"></a>Fortsetzen des Lesens von Datensätzen ab einer gespeicherten Position

Sie können Ihre Leseposition im Änderungsfeed speichern und dann das Durchlaufen der Datensätze zu einem späteren Zeitpunkt fortsetzen. Sie können die Leseposition speichern, indem Sie den Änderungsfeedcursor abrufen. Dieser Cursor ist eine **Zeichenfolge**, die Ihre Anwendung entsprechend dem Design der Anwendung (z. B. in einer Datei oder Datenbank) speichern kann.

In diesem Beispiel werden alle Datensätze im Änderungsfeed durchlaufen, einer Liste hinzugefügt, und der Cursor wird gespeichert. Die Liste und der Cursor werden an den Aufrufer zurückgegeben.

```csharp
public async Task<(string, List<BlobChangeFeedEvent>)> ChangeFeedResumeWithCursorAsync
    (string connectionString,  string cursor)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();
    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    IAsyncEnumerator<Page<BlobChangeFeedEvent>> enumerator = changeFeedClient
        .GetChangesAsync(continuation: cursor)
        .AsPages(pageSizeHint: 10)
        .GetAsyncEnumerator();

    await enumerator.MoveNextAsync();

    foreach (BlobChangeFeedEvent changeFeedEvent in enumerator.Current.Values)
    {

        changeFeedEvents.Add(changeFeedEvent);             
    }

    // Update the change feed cursor.  The cursor is not required to get each page of events,
    // it is intended to be saved and used to resume iterating at a later date.
    cursor = enumerator.Current.ContinuationToken;
    return (cursor, changeFeedEvents);
}
```

## <a name="stream-processing-of-records"></a>Streamverarbeitung von Datensätzen

Sie können Änderungsfeeddatensätze verarbeiten, wenn sie an den Änderungsfeed commitet werden. Weitere Informationen finden Sie unter [Spezifikationen](storage-blob-change-feed.md#specifications). Änderungsereignisse werden im Durchschnitt nach 60 Sekunden im Änderungsfeed veröffentlicht. Wir empfehlen, bei der Festlegung des Abrufintervalls und des Abrufs neuer Änderungen diesen Zeitraum zu berücksichtigen.

In diesem Beispiel werden regelmäßig Änderungen abgerufen.  Wenn Änderungsdatensätze vorhanden sind, verarbeitet dieser Code die Datensätze und speichert den Änderungsfeedcursor. Auf diese Weise kann die Anwendung, wenn der Vorgang angehalten und anschließend neu gestartet wird, mit dem Cursor die Verarbeitung der Datensätze an der Stelle fortsetzen, an der sie unterbrochen wurde. In diesem Beispiel wird der Cursor in einer lokalen Anwendungskonfigurationsdatei gespeichert. Ihre Anwendung kann den Cursor jedoch in jeder Form speichern, die für Ihr Szenario am sinnvollsten ist.

```csharp
public async Task ChangeFeedStreamAsync
    (string connectionString, int waitTimeMs, string cursor)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();

    while (true)
    {
        IAsyncEnumerator<Page<BlobChangeFeedEvent>> enumerator = changeFeedClient
        .GetChangesAsync(continuation: cursor).AsPages().GetAsyncEnumerator();

        while (true) 
        {
            var result = await enumerator.MoveNextAsync();

            if (result)
            {
                foreach (BlobChangeFeedEvent changeFeedEvent in enumerator.Current.Values)
                {
                    string subject = changeFeedEvent.Subject;
                    string eventType = changeFeedEvent.EventType.ToString();
                    string api = changeFeedEvent.EventData.Api;

                    Console.WriteLine("Subject: " + subject + "\n" +
                        "Event Type: " + eventType + "\n" +
                        "Api: " + api);
                }

                // helper method to save cursor. 
                SaveCursor(enumerator.Current.ContinuationToken);
            }
            else
            {
                break;
            }

        }
        await Task.Delay(waitTimeMs);
    }

}

public void SaveCursor(string cursor)
{
    System.Configuration.Configuration config = 
        ConfigurationManager.OpenExeConfiguration
        (ConfigurationUserLevel.None);

    config.AppSettings.Settings.Clear();
    config.AppSettings.Settings.Add("Cursor", cursor);
    config.Save(ConfigurationSaveMode.Modified);
}
```

## <a name="reading-records-within-a-time-range"></a>Lesen von Datensätzen innerhalb eines Zeitbereichs

Sie können Datensätze lesen, die in einem bestimmten Zeitbereich liegen. In diesem Beispiel werden alle Datensätze im Änderungsfeed im Zeitraum vom 2. März 2020, 15:00 Uhr, bis 7. August 2020, 2:00 Uhr, durchlaufen und einer Liste hinzugefügt. Anschließend wird diese Liste an den Aufrufer zurückgegeben.

### <a name="selecting-segments-for-a-time-range"></a>Auswählen von Segmenten für einen Zeitbereich

```csharp
public async Task<List<BlobChangeFeedEvent>> ChangeFeedBetweenDatesAsync(string connectionString)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();
    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    // Create the start and end time.  The change feed client will round start time down to
    // the nearest hour, and round endTime up to the next hour if you provide DateTimeOffsets
    // with minutes and seconds.
    DateTimeOffset startTime = new DateTimeOffset(2020, 3, 2, 15, 0, 0, TimeSpan.Zero);
    DateTimeOffset endTime = new DateTimeOffset(2020, 8, 7, 2, 0, 0, TimeSpan.Zero);

    // You can also provide just a start or end time.
    await foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedClient.GetChangesAsync(
        start: startTime,
        end: endTime))
    {
        changeFeedEvents.Add(changeFeedEvent);
    }

    return changeFeedEvents;
}
```

Die von Ihnen angegebene Startzeit wird auf die volle Stunde abgerundet, und die Endzeit wird auf die volle Stunde aufgerundet. Möglicherweise werden Benutzern Ereignisse angezeigt, die vor der Startzeit und nach der Endzeit aufgetreten sind. Es ist auch möglich, das einige Ereignisse, die zwischen Start- und Endzeit liegen, nicht angezeigt werden. Das liegt daran, dass Ereignisse möglicherweise in der Stunde vor der Startzeit oder in der Stunde nach der Endzeit aufgezeichnet wurden.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Änderungsfeedprotokolle. Sehen Sie sich den Artikel [Änderungsfeed in Azure Blob Storage](storage-blob-change-feed.md) an.
