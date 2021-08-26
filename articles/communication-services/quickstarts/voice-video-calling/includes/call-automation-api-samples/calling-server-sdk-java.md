---
title: Datei einfügen
description: Java-Aufrufautomatisierung
services: azure-communication-services
author: ravithanneeru
manager: joseys
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: joseys
ms.openlocfilehash: 7253be0e9d7723bdb018b53e3ee655188bb3d679
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121802629"
---
## <a name="prerequisites"></a>Voraussetzungen
Führen Sie die folgenden Schritte aus, bevor Sie beginnen:
- Erstellen Sie ein Azure-Konto mit einem aktiven Abonnement. Details finden Sie auf der [Seite zum Erstellen eines kostenloses Azure-Kontos](https://azure.microsoft.com/free/).
- [Java Development Kit (JDK)](/azure/developer/java/fundamentals/java-jdk-install), Version 11 oder höher.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Erstellen Sie eine Azure Communication Services-Ressource. Ausführlichere Informationen hierzu finden Sie unter [Schnellstart: Erstellen und Verwalten einer Communication Services-Ressource](../../../create-communication-resource.md). Sie müssen die **Verbindungszeichenfolge** Ihrer Ressource für dieses Beispiel aufzeichnen.
- Beschaffen Sie sich eine Telefonnummer für Ihre neue Azure Communication Services Ressource. Weitere Informationen finden Sie unter [Abrufen einer Telefonnummer](../../../telephony-sms/get-phone-number.md?pivots=platform-azp).
- Laden Sie [ngrok](https://www.ngrok.com/download) herunter und installieren Sie es. Da das Beispiel lokal ausgeführt wird, ermöglicht ngrok den Empfang aller Ereignisse.
- (Optional) Erstellen Sie eine Azure Speech-Ressource zum Erstellen benutzerdefinierter Nachrichten, die von der Anwendung abgespielt werden sollen. Zum Erstellen der Ressource befolgen Sie [diesen Leitfaden](../../../../../cognitive-services/speech-service/overview.md#try-the-speech-service-for-free).

> [!NOTE]
> Den fertigen Code für diese Schnellstartanleitung finden Sie auf [GitHub](https://github.com/Azure-Samples/communication-services-java-quickstarts/tree/main/OutboundCallReminder). In diesem Beispiel wird das Microsoft Cognitive Services Speech SDK verwendet. Durch das Herunterladen des Microsoft Cognitive Services Speech SDKs stimmen Sie den zugehörigen [Lizenzbedingungen](https://aka.ms/csspeech/license201809) zu.

## <a name="add-the-package-references-for-the-calling-server-sdk"></a>Hinzufügen der Paketverweise für das Aufrufserver-SDK

Verweisen Sie in Ihrer POM-Datei auf das Paket `azure-communication-callingserver` mit den Aufruf-APIs:

```java
<dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-communication-callingserver</artifactId>
      <version>1.0.0-beta.2</version>
</dependency>
```

Zum Erstellen der Benutzeridentität muss Ihre Anwendung auf das `azure-communication-identity` Paket verweisen:

```java
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-communication-identity</artifactId>
      <version>1.1.1</version>
      <exclusions>
        <exclusion>
          <groupId>com.azure</groupId>
          <artifactId>azure-communication-common</artifactId>
        </exclusion>
      </exclusions>
    </dependency>
```

## <a name="object-model"></a>Objektmodell

Die folgenden Klassen behandeln einige der wichtigsten Funktionen des Azure Communication Aufrufserver-SDK für Java.

| Name                                  | Beschreibung                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallingServerClientBuilder | Diese Klasse wird verwendet, um eine Instanz des CallingServerClient zu erstellen.|
| CallingServerClient | Diese Klasse wird für die Aufruffunktionalität benötigt. Sie rufen eine Instanz über CallingServerClientBuilder ab und verwenden sie zum Starten/Beenden eines Aufrufs, zum Wiedergeben/Abbrechen von Audiodaten und zum Hinzufügen/Entfernen von Teilnehmern. |
| CommunicationIdentityClient | Diese Klasse ist erforderlich, um eine Kommunikationsbenutzeridentität zu erstellen oder zu löschen. |

## <a name="create-a-call-client"></a>Erstellen eines Aufrufclients

Eine Communication Services Verbindungszeichenfolge und ein httpClient-Objekt müssen über die Funktionen `connectionString()` bzw. `httpClient()` an `CallingServerClientBuilder` übergeben werden. Verwenden Sie zum Erstellen eines Clientaufrufobjekts die Funktion `buildClient()` von Objekt `CallingServerClientBuilder`.

```java
NettyAsyncHttpClientBuilder httpClientBuilder = new NettyAsyncHttpClientBuilder();
CallingServerClientBuilder callClientBuilder = new CallingServerClientBuilder().httpClient(httpClientBuilder.build())
        .connectionString(this.callConfiguration.ConnectionString);

this.callingServerClient = callClientBuilder.buildClient();
```

## <a name="create-user-identity"></a>Erstellen einer Benutzeridentität

Verwenden Sie das Objekt `CommunicationIdentityClientBuilder` und legen Sie die Communication Services Ressourcenverbindungszeichenfolge über die Funktion `connectionString()` fest, um ein Objekt `CommunicationIdentityClient` zu erstellen, und verwenden Sie dann die Funktion `createUser`, um eine Quellbenutzeridentität zu erstellen.

```java
CommunicationIdentityClient client = new CommunicationIdentityClientBuilder()
        .connectionString("<Connection_String>")
        .buildClient();
CommunicationUserIdentifier user = client.createUser();
```

## <a name="delete-user-identity"></a>Löschen einer Benutzeridentität

Verwenden Sie die Funktion `deleteUser` von Objekt `CommunicationIdentityClient`, um eine Benutzeridentität zu löschen:

```java
CommunicationIdentityClient client = new CommunicationIdentityClientBuilder()
        .connectionString("<Connection_String>")
        .buildClient();
client.deleteUser(new CommunicationUserIdentifier(source));    
```

## <a name="create-a-call"></a>Erstellen eines Aufrufs

Verwenden Sie die Methode `createCallConnectionWithResponse` von Objekt `CallingServerClient`, um einen Aufruf zu starten. Von Methode `createCallConnectionWithResponse` werden die folgenden Parameter unterstützt:
- `source` ist die Quellbenutzeridentität des Aufrufers vom Typ `CommunicationUserIdentifier`.
- `targets` ist eine Liste der Zielidentitäten vom Typ `Iterable<CommunicationIdentifier>`.
- `options` ist die Option für den Aufruf des Typs `CreateCallOptions`. Im Folgenden sind die Parameter für die `CreateCallOptions`-Methode angegeben:

    - `callbackUri` ist der Rückruf-URI der Anwendung.
    - `requestedModalities` ist die Modalität zur Anforderung eines Aufrufs. Der Typ ist `Iterable<MediaType>`. Die Werte können Audio oder Video sein.
    - `requestedCallEvents` ist die angeforderten Rückrufereignisse vom Typ `Iterable<EventSubscriptionType>`. Verwenden Sie diesen Parameter, um die Ereignisse `EventSubscriptionType.PARTICIPANTS_UPDATED` und `EventSubscriptionType.DTMF_RECEIVED` zu abonnieren.

- Legen Sie die Quellaufrufer-ID mithilfe der Funktion `setAlternateCallerId()` von Objekt `CreateCallOptions` als alternative Aufrufer-ID fest.

```java
CommunicationUserIdentifier source = new CommunicationUserIdentifier(this.callConfiguration.SourceIdentity);
PhoneNumberIdentifier target = new PhoneNumberIdentifier(targetPhoneNumber);
List<MediaType> callModality = new ArrayList<>() { {add(MediaType.AUDIO);} };
List<EventSubscriptionType> eventSubscriptionType = new ArrayList<>() {
        {add(EventSubscriptionType.PARTICIPANTS_UPDATED); add(EventSubscriptionType.DTMF_RECEIVED);}};

CreateCallOptions createCallOption = new CreateCallOptions(this.callConfiguration.appCallbackUrl,
        callModality, eventSubscriptionType);
createCallOption.setAlternateCallerId(new PhoneNumberIdentifier(this.callConfiguration.sourcePhoneNumber));

Logger.logMessage(Logger.MessageType.INFORMATION,"Performing CreateCall operation");

List<CommunicationIdentifier> targets = new ArrayList<>() { {add(target);} };

Response<CallConnection> response = this.callingServerClient.createCallConnectionWithResponse(source, targets, createCallOption, null);
callConnection = response.getValue();
```

Die Methode `createCallConnectionWithResponse` gibt ein Objekt von `Response<CallConnection>` zurück, das für andere Aufrufvorgänge wie Audio wiedergeben, Audio abbrechen und beenden verwendet werden kann.

## <a name="play-audio"></a>Audiowiedergabe

Nachdem ein Aufruf erstellt wurde, können Sie Methode `playAudioWithResponse` von Objekt `CallConnection` verwenden, um eine Audionachricht für den Aufgerufenen abzuspielen. Methode `playAudioWithResponse` unterstützt die folgenden Parameter:

- `audioFileUri` ist der URI für die Audiodatei, in der die abzuspielende Nachricht angezeigt wird.
- `playAudioOptions` ist die Wiedergabe von Audiooptionen. Der Typ ist `PlayAudioOptions`. Im Folgenden sind die unterstützten Parameter für Objekt `PlayAudioOptions` angegeben:
    - `loop` legen Sie auf TRUE fest, um die Audionachricht zu wiederholen.
    - `audioFileId` eine ID für die Medien im AudioFileUri, mit der die Medien zwischengespeichert werden.
    - `operationContext` ist die eindeutige ID für den Anforderungskontext.

```java
// Preparing data for request
String audioFileUri = callConfiguration.audioFileUrl;
Boolean loop = true;
String operationContext = UUID.randomUUID().toString();
String audioFileId = UUID.randomUUID().toString();
PlayAudioOptions playAudioOptions = new PlayAudioOptions();
playAudioOptions.setLoop(loop);
playAudioOptions.setAudioFileId(audioFileId);
playAudioOptions.setOperationContext(operationContext);

Logger.logMessage(Logger.MessageType.INFORMATION, "Performing PlayAudio operation");
Response<PlayAudioResult> playAudioResponse = this.callConnection.playAudioWithResponse(audioFileUri, playAudioOptions, null);

PlayAudioResult response = playAudioResponse.getValue();
```

Methode `playAudio` gibt `Response<PlayAudioResult>` zurück, die verwendet werden kann, um mithilfe von Methode `getStatus()` `OperationStatus` zu erhalten. Die `OperationStatus` kann die folgenden Werte aufweisen: `NotStarted`, `Running`, `Completed` oder `Failed`.

```java
Logger.logMessage(Logger.MessageType.INFORMATION, "playAudioWithResponse -- > " + GetResponse(playAudioResponse) +
", Id: " + response.getOperationId() + ", OperationContext: " + response.getOperationContext() + ", OperationStatus: " +
response.getStatus().toString());
```

## <a name="add-a-participant-to-the-call"></a>Hinzufügen eines Teilnehmers zu einem Aufruf

Verwenden Sie Methode `addParticipantWithResponse` von Objekt `CallConnection`, um dem Aufruf einen Teilnehmer hinzuzufügen. Methode `addParticipantWithResponse` unterstützt die folgenden Parameter:

- `participant` ist der Teilnehmerbezeichner vom Typ `CommunicationUserIdentifier` oder `PhoneNumberIdentifier`.
- `alternateCallerId` ist die Quellaufrufer-ID.
- `operationContext` ist die eindeutige ID für den Anforderungskontext.

```java
// Preparing data for request
CommunicationIdentifier participant = null;
String operationContext = UUID.randomUUID().toString();

if (identifierKind == CommunicationIdentifierKind.UserIdentity) {
    participant = new CommunicationUserIdentifier(addedParticipant);

} else if (identifierKind == CommunicationIdentifierKind.PhoneIdentity) {
    participant = new PhoneNumberIdentifier(addedParticipant);
}

Response<AddParticipantResult> response = callConnection.addParticipantWithResponse(participant, this.callConfiguration.sourcePhoneNumber, operationContext, null);
```

## <a name="cancel-media-processing"></a>Abbrechen der Medienverarbeitung

Verwenden Sie Methode `cancelAllMediaOperationsWithResponse` von Objekt `CallConnection`, um die Audiowiedergabe abzubrechen. Methode `cancelAllMediaOperationsWithResponse` unterstützt die folgenden Parameter:

- `operationContext` ist die eindeutige ID für den Anforderungskontext.

```java
String operationContext = UUID.randomUUID().toString();
Response<CancelAllMediaOperationsResult> cancelmediaresponse = this.callConnection.cancelAllMediaOperationsWithResponse(operationContext, null);
```

## <a name="hang-up-the-call"></a>Beenden des Aufrufs

Verwenden Sie Methode `hangupWithResponse` von Objekt `CallConnection`, um den Aufruf zu beenden.

```java
Response<Void> response = this.callConnection.hangupWithResponse(null);
```