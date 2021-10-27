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
ms.openlocfilehash: b71ae256329d9fe8d28f0b5154f0b732de61009f
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130050550"
---
## <a name="sample-code"></a>Beispielcode
Den fertigen Code für diese Schnellstartanleitung finden Sie auf [GitHub](https://github.com/Azure-Samples/communication-services-java-quickstarts/tree/main/ServerRecording).

## <a name="prerequisites"></a>Voraussetzungen

- Erstellen Sie ein Azure-Konto mit einem aktiven Abonnement. Details finden Sie auf der [Seite zum Erstellen eines kostenloses Azure-Kontos](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js (12.18.4 und höher)](https://nodejs.org/en/download/)
- [Java Development Kit (JDK)](/azure/developer/java/fundamentals/java-jdk-install), Version 11 oder höher.
- [Apache Maven](https://maven.apache.org/download.cgi).
- [Spring Boot Framework v- 2.5.0](https://spring.io/projects/spring-boot)
- Erstellen Sie eine Azure Communication Services-Ressource. Ausführlichere Informationen hierzu finden Sie unter [Schnellstart: Erstellen und Verwalten einer Communication Services-Ressource](../../../create-communication-resource.md). Sie müssen die **Verbindungszeichenfolge** Ihrer Ressource für diese Schnellstartanleitung aufzeichnen.
- Ausführliche Informationen zu einem Azure-Speicherkonto und -Container finden Sie unter [Erstellen eines Speicherkontos](../../../../../storage/common/storage-account-create.md?tabs=azure-portal). Sie müssen die **Verbindungszeichenfolge** und den **Containernamen** für diese Schnellstartanleitung aufzeichnen.
- Ein [Azure Event Grid](../../../../../event-grid/overview.md) Webhook.

## <a name="object-model"></a>Objektmodell

Mit den folgenden Klassen werden einige Hauptfunktionen der aufzeichnenden Server-Apps von Java verarbeitet.

| Name                                  | Beschreibung                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallingServerClientBuilder | Diese Klasse wird verwendet, um eine Instanz des CallingServerClient zu erstellen.|
| CallingServerClient | Diese Klasse wird für die Aufruffunktionalität benötigt. Sie rufen eine Instanz über CallingServerClientBuilder ab und verwenden sie zum Starten/Beenden eines Aufrufs, zum Wiedergeben/Abbrechen von Audiodaten und zum Hinzufügen/Entfernen von Teilnehmern. |

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

Um einen aufrufenden Serverclient zu erstellen, verwenden Sie Ihre Communication Services Verbindungszeichenfolge und übergeben sie an das aufrufende Serverclientobjekt.

```java
NettyAsyncHttpClientBuilder httpClientBuilder = new NettyAsyncHttpClientBuilder();
CallingServerClientBuilder builder = new CallingServerClientBuilder().httpClient(httpClientBuilder.build())
 .connectionString(connectionString);
 callingServerClient = builder.buildClient();
```

## <a name="start-recording-session-using-startrecordingwithresponse-server-api"></a>Starten der Aufzeichnungssitzung mithilfe der Server-API „startRecordingWithResponse“

Verwenden Sie die Serveraufruf-ID, die während der Initiierung des Aufrufs empfangen wurde.

```java
URI recordingStateCallbackUri = new URI("<CallbackUri>");

Response<StartCallRecordingResult> response = this.callingServerClient.initializeServerCall("<serverCallId>")
.startRecordingWithResponse(String.valueOf(recordingStateCallbackUri), null, null);
```
Die `startRecordingWithResponse`-API-Antwort enthält die Aufzeichnungs-ID der Aufzeichnungssitzung.

## <a name="start-recording-session-with-startrecordingoptions-using-startrecordingwithresponse-server-api"></a>Starten der Aufzeichnungssitzung mit „StartRecordingOptions“ mithilfe der Server-API „startRecordingWithResponse“

Verwenden Sie die Serveraufruf-ID, die während der Initiierung des Aufrufs empfangen wurde.

- „RecordingContent“ wird zum Übergeben des Inhaltstyp der Aufzeichnung verwendet. Beispiel: AUDIO/AUDIO_VIDEO.
- „RecordingChannel“ wird zum Übergeben des Kanaltyps der Aufzeichnung verwendet. Beispiel: MIXED/UNMIXED.
- „RecordingFormat“ wird zum Übergeben des Formats der Aufzeichnung verwendet. Beispiel: MP4/MP3/WAV.

```java
URI recordingStateCallbackUri = new URI("<CallbackUri>");
StartRecordingOptions recordingOptions = new StartRecordingOptions();
recordingOptions.setRecordingContent(RecordingContent.AUDIO_VIDEO);
recordingOptions.setRecordingChannel(RecordingChannel.MIXED);
recordingOptions.setRecordingFormat(RecordingFormat.MP4);
Response<StartCallRecordingResult> response = this.callingServerClient.initializeServerCall("<serverCallId>")
.startRecordingWithResponse(String.valueOf(recordingStateCallbackUri), recordingOptions, null);
```

## <a name="stop-recording-session-using-stoprecordingwithresponse-server-api"></a>Beenden der Aufzeichnungssitzung mithilfe der Server-API „stopRecordingWithResponse“

Verwenden Sie die Aufzeichnungs-ID, die als Antwort auf `startRecordingWithResponse` empfangen wurde.

```java
Response<Void> response = this.callingServerClient.initializeServerCall(serverCallId)
.stopRecordingWithResponse(recordingId, null);
```

## <a name="pause-recording-session-using-pauserecordingwithresponse-server-api"></a>Anhalten der Aufzeichnungssitzung mithilfe der Server-API „pauseRecordingWithResponse“

Verwenden Sie die Aufzeichnungs-ID, die als Antwort auf `startRecordingWithResponse` empfangen wurde.

```java
Response<Void> response = this.callingServerClient.initializeServerCall(serverCallId)
.pauseRecordingWithResponse(recordingId, null);
```

## <a name="resume-recording-session-using-resumerecordingwithresponse-server-api"></a>Fortsetzen der Aufzeichnungssitzung mithilfe der Server-API „resumeRecordingWithResponse“

Verwenden Sie die Aufzeichnungs-ID, die als Antwort auf `startRecordingWithResponse` empfangen wurde.

```java
Response<Void> response = this.callingServerClient.initializeServerCall(serverCallId)
.resumeRecordingWithResponse(serverCallId, null);
```

## <a name="download-recording-file-using-downloadtowithresponse-server-api"></a>Herunterladen der Aufzeichnungsdatei mithilfe der Server-API „downloadToWithResponse“

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
Verwenden Sie die `downloadToWithResponse`-Methode der `CallingServerClient`-Klasse zum Herunterladen der aufgezeichneten Medien. Im Folgenden sind die unterstützten Parameter für die `downloadToWithResponse`-Methode angegeben:

- `contentLocation`: ACS-URL, in der sich der Inhalt befindet.
- `destinationPath`: Dateispeicherort
- `parallelDownloadOptionss`: Ein optionales ParallelDownloadOptions-Objekt, um die Funktionsweise des parallelen Downloads zu ändern.
- `overwrite`: True, um die Datei zu überschreiben, sofern vorhanden.
- `context`: Ein Kontext, der den Anforderungskontext darstellt.

```Java
Boolean overwrite = true;
ParallelDownloadOptions parallelDownloadOptions = null;
Context context = null;

String filePath = String.format(".\\%s.%s", documentId, fileType);
Path destinationPath = Paths.get(filePath);

Response<Void> downloadResponse = callingServerClient.downloadToWithResponse(contentLocation, destinationPath, parallelDownloadOptions, overwrite, context);
```
Der Inhaltsspeicherort und die Dokument-IDs für die Aufzeichnungsdateien können für jedes `recordingChunk` aus den Feldern `contentLocation` bzw. `documentId` abgerufen werden.
