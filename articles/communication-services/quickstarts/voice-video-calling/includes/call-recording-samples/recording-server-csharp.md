---
title: Datei einfügen
description: Datei einfügen
services: azure-communication-services
author: ravithanneeru
manager: joseys
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: joseys
ms.openlocfilehash: 3f3b673da2809e45af88ae887eb289edb4e5c65f
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130050808"
---
## <a name="sample-code"></a>Beispielcode
Den fertigen Code für diese Schnellstartanleitung finden Sie auf [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/ServerRecording).

## <a name="prerequisites"></a>Voraussetzungen

- Erstellen Sie ein Azure-Konto mit einem aktiven Abonnement. Details finden Sie auf der [Seite zum Erstellen eines kostenloses Azure-Kontos](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js (12.18.4 und höher)](https://nodejs.org/en/download/).
- [Visual Studio (2019 und höher)](https://visualstudio.microsoft.com/vs/).
- [.NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1) (Stellen Sie sicher, dass Sie die Version installieren, die Ihrer Visual Studio-Instanz entspricht (32 oder 64 Bit)).
- Erstellen Sie eine Azure Communication Services-Ressource. Ausführlichere Informationen hierzu finden Sie unter [Schnellstart: Erstellen und Verwalten einer Communication Services-Ressource](../../../create-communication-resource.md). Sie müssen die **Verbindungszeichenfolge** Ihrer Ressource für diese Schnellstartanleitung aufzeichnen.
- Ausführliche Informationen zu einem Azure-Speicherkonto und -Container finden Sie unter [Erstellen eines Speicherkontos](../../../../../storage/common/storage-account-create.md?tabs=azure-portal). Sie müssen die **Verbindungszeichenfolge** Ihres Speichers und den **Containernamen** für diese Schnellstartanleitung notieren.
- Ein [Azure Event Grid](../../../../../event-grid/overview.md) Webhook.

## <a name="object-model"></a>Objektmodell

Mit den folgenden Klassen werden einige Hauptfunktionen der aufzeichnenden Server-Apps von C# verarbeitet.

| Name                                  | BESCHREIBUNG                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallingServerClient | Diese Klasse wird für die Aufzeichnungsfunktionalität benötigt. Sie erstellen eine Instanz des CallingServerClient mithilfe der ACS-Ressourcenverbindungszeichenfolge und verwenden sie zum Starten/Beenden und Anhalten/Fortsetzen einer Aufrufaufzeichnung. |

## <a name="getting-servercallid-as-a-requirement-for-call-recording-server-apis"></a>Abrufen der serverCallId als Anforderung für Aufrufaufzeichnungsserver-APIs

> [!NOTE]
> Diese API wird als Vorschau für Entwickler bereitgestellt. Je nachdem, welches Feedback wir dazu erhalten, werden möglicherweise Änderungen vorgenommen. Verwenden Sie diese API nicht in einer Produktionsumgebung. Um diese API einzusetzen, verwenden Sie das Beta-Release des ACS Calling Web SDK. Ein Clientbeispiel mit Aufzeichnungsflows finden Sie unter [GitHub](https://github.com/Azure-Samples/communication-services-web-calling-hero/tree/public-preview).

Die Aufrufaufzeichnung ist ein erweitertes Feature der zentralen Aufruf-API. Sie müssen zunächst das API-Objekt der Aufzeichnungsfunktion abrufen:

```JavaScript
const callRecordingApi = call.api(Features.Recording);
```

Abonnieren von Aufzeichnungsänderungen:

```JavaScript
const isRecordingActiveChangedHandler = () => {
  console.log(callRecordingApi.isRecordingActive);
};

callRecordingApi.on('isRecordingActiveChanged', isRecordingActiveChangedHandler);
```

Rufen Sie die Serveraufruf-ID ab, die zum Starten/Beenden/Anhalten/Fortsetzen von Aufzeichnungssitzungen verwendet werden kann:

Nachdem der Aufruf verbunden wurde, verwenden Sie die `getServerCallId`-Methode, um die Serveraufruf-ID abzurufen.

```JavaScript
callAgent.on('callsUpdated', (e: { added: Call[]; removed: Call[] }): void => {
    e.added.forEach((addedCall) => {
        addedCall.on('stateChanged', (): void => {
            if (addedCall.state === 'Connected') {
                addedCall.info.getServerCallId().then(result => {
                    dispatch(setServerCallId(result));
                }).catch(err => {
                    console.log(err);
                });
            }
        });
    });
});
```

## <a name="create-a-calling-server-client"></a>Erstellen eines aufrufenden Serverclients

Um einen aufrufenden Serverclient zu erstellen, verwenden Sie Ihre Azure Communication Services Verbindungszeichenfolge und übergeben sie an das aufrufende Serverclientobjekt.

```csharp
CallingServerClient callingServerClient = new CallingServerClient("<Resource_Connection_String>");
```

## <a name="start-recording-session-using-startrecordingasync-server-api"></a>Starten der Aufzeichnungssitzung mithilfe der Server-API „StartRecordingAsync“

Verwenden Sie die Serveraufruf-ID, die während der Initiierung eines Aufrufs empfangen wurde.

```csharp
var startRecordingResponse = await callingServerClient.InitializeServerCall("<servercallid>").StartRecordingAsync("<callbackuri>").ConfigureAwait(false);
```
Die `StartRecordingAsync` API-Antwort enthält die Aufzeichnungs-ID der Aufzeichnungssitzung.

## <a name="start-recording-session-with-options-using-startrecordingasync-server-api"></a>Starten der Aufzeichnungssitzung mit Optionen mithilfe der Server-API „StartRecordingAsync“

Verwenden Sie die Serveraufruf-ID, die während der Initiierung eines Aufrufs empfangen wurde.

- „RecordingContent“ wird zum Übergeben des Inhaltstyp der Aufzeichnung verwendet. Beispiel: audio/audiovideo
- „RecordingChannel“ wird zum Übergeben des Kanaltyps der Aufzeichnung verwendet. Beispiel: mixed/unmixed
- „RecordingFormat“ wird zum Übergeben des Formats der Aufzeichnung verwendet. Beispiel: mp4/mp3/wav

```csharp
var startRecordingResponse = await callingServerClient.InitializeServerCall("<servercallid>").StartRecordingAsync("<callbackuri>","<RecordingContent>","<RecordingChannel>","<RecordingFormat>").ConfigureAwait(false);
```
Die `StartRecordingAsync` API-Antwort enthält die Aufzeichnungs-ID der Aufzeichnungssitzung.

## <a name="stop-recording-session-using-stoprecordingasync-server-api"></a>Beenden der Aufzeichnungssitzung mithilfe der Server-API „StopRecordingAsync“

Verwenden Sie die Aufzeichnungs-ID, die als Antwort auf `StartRecordingAsync` empfangen wurde.

```csharp
 var stopRecording = await callingServerClient.InitializeServerCall("<servercallid>").StopRecordingAsync("<recordingid>").ConfigureAwait(false);
```

## <a name="pause-recording-session-using-pauserecordingasync-server-api"></a>Anhalten der Aufzeichnungssitzung mithilfe der Server-API „PauseRecordingAsync“

Verwenden Sie die Aufzeichnungs-ID, die als Antwort auf `StartRecordingAsync` empfangen wurde.

```csharp
var pauseRecording = await callingServerClient.InitializeServerCall("<servercallid>").PauseRecordingAsync("<recordingid>");
```

## <a name="resume-recording-session-using-resumerecordingasync-server-api"></a>Fortsetzen der Aufzeichnungssitzung mithilfe der Server-API „ResumeRecordingAsync“

Verwenden Sie die Aufzeichnungs-ID, die als Antwort auf `StartRecordingAsync` empfangen wurde.

```csharp
var resumeRecording = await callingServerClient.InitializeServerCall("<servercallid>").ResumeRecordingAsync("<recordingid>");
```

## <a name="download-recording-file-using-downloadstreamingasync-server-api"></a>Herunterladen der Aufzeichnungsdatei mithilfe der Server-API „DownloadStreamingAsync“

Verwenden Sie einen [Azure Event Grid](../../../../../event-grid/overview.md) Webhook oder eine andere ausgelöste Aktion, um Ihre Dienste zu benachrichtigen, wenn die aufgezeichneten Medien zum Download bereit stehen.

Im Folgenden finden Sie ein Beispiel für das Ereignisschema.

```
{
    "id": string, // Unique guid for event
    "topic": string, // Azure Communication Services resource id
    "subject": string, // /recording/call/{call-id}
    "data": {
        "recordingStorageInfo": {
            "recordingChunks": [
                {
                    "documentId": string, // Document id for retrieving from AMS storage
                    "contentLocation": string, //ACS URL where the content is located
                    "metadataLocation": string, // ACS URL where the metadata for this chunk is located
                    "index": int, // Index providing ordering for this chunk in the entire recording
                    "endReason": string, // Reason for chunk ending: "SessionEnded", "ChunkMaximumSizeExceeded”, etc.
                }
            ]
        },
        "recordingStartTime": string, // ISO 8601 date time for the start of the recording
        "recordingDurationMs": int, // Duration of recording in milliseconds
        "sessionEndReason": string // Reason for call ending: "CallEnded", "InitiatorLeft”, etc.
    },
    "eventType": string, // "Microsoft.Communication.RecordingFileStatusUpdated"
    "dataVersion": string, // "1.0"
    "metadataVersion": string, // "1"
    "eventTime": string // ISO 8601 date time for when the event was created
}
```

Verwenden Sie die `DownloadStreamingAsync` API zum Herunterladen der aufgezeichneten Medien.

```csharp
var recordingDownloadUri = new Uri(downloadLocation);
var response = callingServerClient.DownloadStreamingAsync(recordingDownloadUri);
```
Der Ablageort zum Herunterladen der Aufzeichnung kann aus dem `contentLocation`-Attribut von `recordingChunk` abgerufen werden. Die `DownloadStreamingAsync`-Methode gibt eine Antwort vom Typ `Response<Stream>` zurück, die den heruntergeladenen Inhalt enthält.
