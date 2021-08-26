---
title: Datei einfügen
description: C#
services: azure-communication-services
author: ravithanneeru
manager: joseys
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: joseys
ms.openlocfilehash: 1dae62ae0ab1fa03dd4c5e1128699cd43a241984
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121802097"
---
## <a name="prerequisites"></a>Voraussetzungen

Führen Sie die folgenden Schritte aus, bevor Sie beginnen:

- Erstellen Sie ein Azure-Konto mit einem aktiven Abonnement. Details finden Sie auf der [Seite zum Erstellen eines kostenloses Azure-Kontos](https://azure.microsoft.com/free/).
- [Visual Studio (2019 und höher)](https://visualstudio.microsoft.com/vs/).
- [.NET Framework 4.7.2](https://dotnet.microsoft.com/download/dotnet-framework/net472) (Stellen Sie sicher, dass Sie die Version installieren, die Ihrer Visual Studio-Instanz entspricht (32 oder 64 Bit)).
- Erstellen Sie eine Azure Communication Services-Ressource. Ausführlichere Informationen hierzu finden Sie unter [Schnellstart: Erstellen und Verwalten einer Communication Services-Ressource](../../../create-communication-resource.md). Sie müssen die **Verbindungszeichenfolge** Ihrer Ressource für dieses Beispiel aufzeichnen.
- Beschaffen Sie sich eine Telefonnummer für Ihre neue Azure Communication Services Ressource. Weitere Informationen finden Sie unter [Abrufen einer Telefonnummer](../../../telephony-sms/get-phone-number.md?pivots=platform-azp).
- Laden Sie [ngrok](https://www.ngrok.com/download) herunter und installieren Sie es. Da das Beispiel lokal ausgeführt wird, ermöglicht ngrok den Empfang aller Ereignisse.
- (Optional) Erstellen Sie eine Azure Speech-Ressource zum Erstellen benutzerdefinierter Nachrichten, die von der Anwendung abgespielt werden sollen. Zum Erstellen der Ressource befolgen Sie [diesen Leitfaden](../../../../../cognitive-services/speech-service/overview.md#try-the-speech-service-for-free).

> [!NOTE]
> Den fertigen Code für diese Schnellstartanleitung finden Sie auf [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/OutboundCallReminder). In diesem Beispiel wird das Microsoft Cognitive Services Speech SDK verwendet. Durch das Herunterladen des Microsoft Cognitive Services Speech SDKs stimmen Sie den zugehörigen [Lizenzbedingungen](https://aka.ms/csspeech/license201809) zu.

## <a name="object-model"></a>Objektmodell

Die folgenden Klassen behandeln einige der wichtigsten Funktionen des Azure Communication Calling Server SDK für C#.

| Name                                  | BESCHREIBUNG                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallingServerClient | Diese Klasse wird für die Anruffunktionalität benötigt. Sie erstellen eine Instanz von CallingServerClient mithilfe ihrer Communication Services-Ressourcenverbindungszeichenfolge und verwenden sie zum Starten/Beenden eines Anrufs, zum Wiedergeben und Abbrechen von Audiodaten und zum Hinzufügen/Entfernen von Teilnehmern |
| CommunicationIdentityClient | Diese Klasse ist erforderlich, um eine Kommunikationsbenutzeridentität zu erstellen oder zu löschen. |

## <a name="create-a-call-client"></a>Erstellen eines Aufrufclients

Um einen anrufenden Serverclient zu erstellen, verwenden Sie Ihre Communication Services Verbindungszeichenfolge und übergeben sie an Ihr Serverclient-Objekt.

```csharp
this.CallClient = new CallingServerClient("<Connection_String>");
```

## <a name="create-user-identity"></a>Erstellen einer Benutzeridentität

Verwenden Sie die Communication Services-Ressourcenverbindungszeichenfolge, um ein `CommunicationIdentityClient` Objekt zu erstellen, und verwenden Sie dann die Methode `CreateUserAsync`, um eine Quellbenutzeridentität zu erstellen.

```csharp
var client = new CommunicationIdentityClient("<Connection_String>");
var user = await client.CreateUserAsync().ConfigureAwait(false);
```

## <a name="delete-user-identity"></a>Löschen einer Benutzeridentität

Verwenden Sie die Methode `DeleteUserAsync` des von Objekt `CommunicationIdentityClient`, um eine Benutzeridentität zu löschen:

```csharp
var client = new CommunicationIdentityClient("<Connection_String>");
await client.DeleteUserAsync(new CommunicationUserIdentifier(source)).ConfigureAwait(false);
```

## <a name="create-a-call"></a>Erstellen eines Aufrufs

Verwenden Sie die Methode `CreateCallConnectionAsync` von Objekt `CallingServerClient`, um einen Anruf zu starten. Im Folgenden sind die Parameter für die `CreateCallConnectionAsync`-Methode angegeben:
- `source` ist die Quellbenutzeridentität des Aufrufers vom Typ `CommunicationIdentifier`.
- `targets` ist eine Liste der Zielidentitäten vom Typ `IEnumerable<CommunicationIdentifier>`.
- `options` ist die Option für den Aufruf des Typs `CreateCallOptions`. Im Folgenden sind die Parameter für die `CreateCallOptions`-Methode angegeben:

    - `callbackUri` ist der Rückruf-URI der Anwendung.
    - `requestedModalities` ist die Modalität zur Anforderung eines Aufrufs. Der Typ ist `IEnumerable<MediaType>`. Die Werte können Audio oder Video sein.
    - `requestedCallEvents` sind die angeforderten Rückrufereignisse. Dies ist der Typ von `IEnumerable<EventSubscriptionType>`. Verwenden Sie diesen Parameter, um die Ereignisse `EventSubscriptionType.ParticipantsUpdated` und `EventSubscriptionType.DtmfReceived` zu abonnieren.

- `cancellationToken` ist das Abbruchtoken. Der Typ ist `CancellationToken`.

```csharp
var source = new CommunicationUserIdentifier(callConfiguration.SourceIdentity);
var target = new PhoneNumberIdentifier(targetPhoneNumber);
var createCallOption = new CreateCallOptions(
    new Uri(callConfiguration.AppCallbackUrl),
    new List<MediaType> { MediaType.Audio },
    new List<EventSubscriptionType> { EventSubscriptionType.ParticipantsUpdated, EventSubscriptionType.DtmfReceived }
    );
createCallOption.AlternateCallerId = new PhoneNumberIdentifier(callConfiguration.SourcePhoneNumber);

Logger.LogMessage(Logger.MessageType.INFORMATION, "Performing CreateCall operation");
var call = await callClient.CreateCallConnectionAsync(source,
    new List<CommunicationIdentifier>() { target },
    createCallOption, reportCancellationToken)
    .ConfigureAwait(false);
```

Die `CreateCallConnectionAsync`-Methode gibt ein `CallConnection`-Objekt zurück, das für andere Aufrufvorgänge wie Audio wiedergeben, Audio abbrechen und beenden verwendet werden kann.

## <a name="play-audio"></a>Audiowiedergabe

Nachdem eine Anrufverbindung erstellt wurde, können Sie die `PlayAudioAsync`-Methode des `CallConnection`-Objekts verwenden, um eine Audionachricht für den Angerufenen wiederzugeben. Von Methode `PlayAudioAsync` werden die folgenden Parameter unterstützt:

- `options` (erforderlich) ist für die Wiedergabe von Audiooptionen. Der Typ ist `PlayAudioOptions`. Das `PlayAudioOptions`-Objekt unterstützt die folgenden Parameter:
    - `AudioFileUri` (erforderlich) ist URI für die Audiodatei, in der die abzuspielende Nachricht angezeigt wird.
    - `OperationContext` (erforderlich) ist die eindeutige ID für den Anforderungskontext.
    - `Loop` (optional) legen Sie auf TRUE fest, um die Audionachricht zu wiederholen.
- `cancellationToken` ist das Abbruchtoken. Der Typ ist `CancellationToken`.

```csharp
// Preparing data for request
var playAudioRequest = new PlayAudioOptions()
{
    AudioFileUri = new Uri(callConfiguration.AudioFileUrl),
    OperationContext = Guid.NewGuid().ToString(),
    Loop = true,
};

Logger.LogMessage(Logger.MessageType.INFORMATION, "Performing PlayAudio operation");
var response = await callConnection.PlayAudioAsync(playAudioRequest, reportCancellationToken).ConfigureAwait(false);
```

Methode `PlayAudioAsync` gibt `PlayAudioResponse` zurück, was verwendet werden kann, um `OperationStatus` zu erhalten. Die `OperationStatus` kann die folgenden Werte aufweisen: `NotStarted`, `Running`, `Completed` oder `Failed`.

```csharp
Logger.LogMessage(Logger.MessageType.INFORMATION, $"Play Audio state: {response.Value.Status}");
```

## <a name="add-a-participant-to-the-call"></a>Hinzufügen eines Teilnehmers zu einem Aufruf

Verwenden Sie Methode `AddParticipantAsync` von Objekt `CallConnection`, um dem Aufruf einen Teilnehmer hinzuzufügen. Methode `AddParticipantAsync` unterstützt die folgenden Parameter:

- `participant` ist der Teilnehmerbezeichner vom Typ `CommunicationUserIdentifier` oder `PhoneNumberIdentifier`.
- `alternateCallerId` ist die Quellaufrufer-ID.
- `operationContext` ist die eindeutige ID für den Anforderungskontext.
- `cancellationToken` (optional) ist das Abbruchtoken.


```csharp
// Preparing data for request
var operationContext = Guid.NewGuid().ToString();

if (identifierKind == CommunicationIdentifierKind.UserIdentity)
{
    var response = await callConnection.AddParticipantAsync(new CommunicationUserIdentifier(addedParticipant), null, operationContext).ConfigureAwait(false);
    Logger.LogMessage(Logger.MessageType.INFORMATION, $"PlayAudioAsync response --> {response}");
}
else if (identifierKind == CommunicationIdentifierKind.PhoneIdentity)
{
    var response = await callConnection.AddParticipantAsync(new PhoneNumberIdentifier(addedParticipant), callConfiguration.SourcePhoneNumber, operationContext).ConfigureAwait(false);
    Logger.LogMessage(Logger.MessageType.INFORMATION, $"PlayAudioAsync response --> {response}");
}
```

## <a name="cancel-media-processing"></a>Abbrechen der Medienverarbeitung

Verwenden Sie Methode `CancelAllMediaOperationsAsync` des `CallConnection`-Objekts, um die Audiowiedergabe abzubrechen. Im Folgenden sind die unterstützten Parameter für die `CancelAllMediaOperationsAsync`-Methode angegeben:

- `operationContext` ist die eindeutige ID für den Anforderungskontext.
- `reportCancellationToken` ist das Abbruchtoken. Der Typ ist `CancellationToken`.

```csharp
var operationContext = Guid.NewGuid().ToString();
var response = await callConnection.CancelAllMediaOperationsAsync(operationContext, reportCancellationToken).ConfigureAwait(false);
```

## <a name="hang-up-the-call"></a>Beenden des Aufrufs

Verwenden Sie die `HangupAsync`-Methode von `CallConnection`-Objekt, um den Aufruf zu beenden. Im Folgenden sind die unterstützten Parameter für die `HangupAsync`-Methode angegeben:

- `reportCancellationToken` ist das Abbruchtoken. Der Typ ist `CancellationToken`.

```csharp
var hangupResponse = await callConnection.HangupAsync(reportCancellationToken).ConfigureAwait(false);
```