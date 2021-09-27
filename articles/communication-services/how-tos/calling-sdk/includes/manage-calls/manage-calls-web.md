---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 3ab9331dddb90803867f6cfb5118aa33bdd313d0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "128700127"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-web.md)]

## <a name="place-a-call"></a>Tätigen eines Anrufs

Um einen Anruf zu erstellen und zu starten, verwenden Sie eine der APIs für `callAgent`, und geben Sie einen Benutzer an, den Sie über das Communication Services Identity SDK erstellt haben.

Erstellung und Start des Anrufs erfolgen synchron. Die `call`-Instanz ermöglicht Ihnen das Abonnieren von Anrufereignissen.

### <a name="place-a-1n-call-to-a-user-or-pstn"></a>Tätigen eines 1:n-Anrufs an einen Benutzer oder ein Telefonfestnetz

Um einen anderen Communication Services-Benutzer anzurufen, verwenden Sie die Methode `startCall` für `callAgent`, und übergeben Sie den `CommunicationUserIdentifier`, den Sie [mit der Communication Services-Verwaltungsbibliothek](../../../../quickstarts/access-tokens.md) erstellt haben.

Verwenden Sie für einen 1:1-Anruf bei einem Benutzer den folgenden Code:

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const oneToOneCall = callAgent.startCall([userCallee]);
```

Um einen Anruf an ein PSTN (Public Switched Telephone Network) zu tätigen, verwenden Sie die Methode `startCall` für `callAgent`, und übergeben Sie den `PhoneNumberIdentifier` des Empfängers. Die Communication Services-Ressource muss so konfiguriert werden, dass Anrufe über das Telefonfestnetz möglich sind.

Wenn Sie eine Telefonfestnetznummer anrufen, geben Sie Ihre alternative Anrufer-ID an. Eine alternative Anrufer-ID bezieht sich auf eine Telefonnummer (basierend auf dem E.164-Standard), die den Anrufer in einem PSTN-Anruf identifiziert. Dies ist die Telefonnummer, die dem Anrufempfänger bei einem eingehenden Anruf angezeigt wird.

> [!NOTE]
> Anrufe über das Telefonfestnetz sind derzeit in der Phase „Private Vorschau“. Um auf diese Funktion zuzugreifen, [bewerben Sie sich für das Early Adopter-Programm](https://aka.ms/ACS-EarlyAdopter).

Verwenden Sie für einen 1:1-Anruf einer PSTN-Nummer den folgenden Code:
```js
const pstnCallee = { phoneNumber: '<ACS_USER_ID>' }
const alternateCallerId = {alternateCallerId: '<ALTERNATE_CALLER_ID>'};
const oneToOneCall = callAgent.startCall([pstnCallee], {alternateCallerId});
```

Verwenden Sie für einen 1:N-Aufruf eines Benutzers und einer PSTN-Nummer den folgenden Code:

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const pstnCallee = { phoneNumber: '<PHONE_NUMBER>'};
const alternateCallerId = {alternateCallerId: '<ALTERNATE_CALLER_ID>'};
const groupCall = callAgent.startCall([userCallee, pstnCallee], {alternateCallerId});
```

### <a name="join-a-group-call"></a>Teilnehmen an einem Gruppenanruf

> [!NOTE]
> Der `groupId`-Parameter wird als Systemmetadaten angesehen und kann von Microsoft für Vorgänge verwendet werden, die zum Ausführen des Systems erforderlich sind. Fügen Sie keine personenbezogenen Daten in den `groupId`-Wert ein. Microsoft behandelt diesen Parameter nicht wie personenbezogene Daten. Seine Inhalte sind möglicherweise für Microsoft-Mitarbeiter sichtbar oder werden langfristig gespeichert.
>
> Der `groupId`-Parameter erfordert, dass Daten im GUID-Format vorliegen. Es wird empfohlen, zufällig generierte GUIDs zu verwenden, die in Ihren Systemen nicht als personenbezogene Daten betrachtet werden.
>

Um einen neuen Gruppenanruf zu starten oder an einem aktuellen Gruppenanruf teilzunehmen, müssen Sie die Methode `join` aufrufen und ein Objekt mit einer `groupId`-Eigenschaft übergeben. Der `groupId`-Wert muss eine GUID sein.

```js
const context = { groupId: '<GUID>'};
const call = callAgent.join(context);
```

## <a name="receive-an-incoming-call"></a>Empfangen eines eingehenden Anrufs

Die `callAgent`-Instanz gibt das Ereignis `incomingCall` aus, wenn die angemeldete Identität einen eingehenden Anruf empfängt. Um auf dieses Ereignis zu lauschen, abonnieren Sie es mithilfe einer der folgenden Optionen:

```js
const incomingCallHandler = async (args: { incomingCall: IncomingCall }) => {
    const incomingCall = args.incomingCall; 

    // Get incoming call ID
    var incomingCallId = incomingCall.id

    // Get information about this Call. This API is provided as a preview for developers
    // and may change based on feedback that we receive. Do not use this API in a production environment.
    // To use this api please use 'beta' release of ACS Calling Web SDK
    var callInfo = incomingCall.info;

    // Get information about caller
    var callerInfo = incomingCall.callerInfo

    // Accept the call
    var call = await incomingCall.accept();

    // Reject the call
    incomingCall.reject();

    // Subscribe to callEnded event and get the call end reason
     incomingCall.on('callEnded', args => {
        console.log(args.callEndReason);
    });

    // callEndReason is also a property of IncomingCall
    var callEndReason = incomingCall.callEndReason;
};
callAgentInstance.on('incomingCall', incomingCallHandler);
```

Das `incomingCall`-Ereignis umfasst eine `incomingCall`-Instanz, die Sie akzeptieren oder ablehnen können.

Wenn das angegebene Videokameragerät von einem anderen Prozess verwendet wird oder im System deaktiviert ist, wird der Anruf mit deaktiviertem Video gestartet und eine Anrufdiagnose „cameraStartFailed: true“ ausgelöst, wenn ein Anruf mit aktiviertem Video gestartet/beigetreten/angenommen wird.

## <a name="mute-and-unmute"></a>Stummschalten und Aufheben der Stummschaltung

Zum Stummschalten oder Aufheben der Stummschaltung des lokalen Endpunkts können Sie die asynchronen APIs `mute` und `unmute` verwenden:

```js
//mute local device
await call.mute();

//unmute local device
await call.unmute();
```

## <a name="manage-remote-participants"></a>Verwalten von Remoteteilnehmern

Alle Remoteteilnehmer werden durch den Typ `RemoteParticipant` dargestellt und sind über die `remoteParticipants`-Sammlung für eine Anrufinstanz verfügbar.

### <a name="list-the-participants-in-a-call"></a>Auflisten der Teilnehmer an einem Anruf

Die `remoteParticipants`-Sammlung gibt eine Liste der Remoteteilnehmer eines Anrufs zurück:

```js
call.remoteParticipants; // [remoteParticipant, remoteParticipant....]
```

### <a name="add-a-participant-to-a-call"></a>Hinzufügen eines Teilnehmers zu einem Anruf

Wenn Sie einem Anruf einen Teilnehmer (einen Benutzer oder eine Telefonnummer) hinzufügen möchten, können Sie `addParticipant` verwenden. Geben Sie einen der `Identifier`-Typen an. Dadurch wird die Instanz `remoteParticipant` synchron zurückgegeben. Das Ereignis `remoteParticipantsUpdated` des Anrufs wird ausgelöst, wenn ein Teilnehmer erfolgreich dem Aufruf hinzugefügt wurde.

```js
const userIdentifier = { communicationUserId: '<ACS_USER_ID>' };
const pstnIdentifier = { phoneNumber: '<PHONE_NUMBER>' }
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier, {alternateCallerId: '<ALTERNATE_CALLER_ID>'});
```

### <a name="remove-a-participant-from-a-call"></a>Entfernen eines Teilnehmers aus einem Anruf

Um einen Teilnehmer (einen Benutzer oder eine Telefonnummer) aus einem Anruf zu entfernen, können Sie `removeParticipant` aufrufen. Sie müssen einen der `Identifier`-Typen übergeben. Diese Methode wird asynchron aufgelöst, nachdem der Teilnehmer aus dem Anruf entfernt wurde. Der Teilnehmer wird auch aus der `remoteParticipants`-Sammlung entfernt.

```js
const userIdentifier = { communicationUserId: '<ACS_USER_ID>' };
const pstnIdentifier = { phoneNumber: '<PHONE_NUMBER>' }
await call.removeParticipant(userIdentifier);
await call.removeParticipant(pstnIdentifier);
```

### <a name="access-remote-participant-properties"></a>Zugreifen auf Eigenschaften von Remoteteilnehmern

Remoteteilnehmern ist ein Satz von Eigenschaften und Sammlungen zugeordnet:

- `CommunicationIdentifier`: Ruft den Bezeichner eines Remoteteilnehmers ab. „Identity“ ist einer der `CommunicationIdentifier`-Typen:

    ```js
    const identifier = remoteParticipant.identifier;
    ```

Mögliche `CommunicationIdentifier`-Typen:

- `{ communicationUserId: '<ACS_USER_ID'> }`: Objekt, das den ACS-Benutzer darstellt.
- `{ phoneNumber: '<E.164>' }`: Objekt, das die Telefonnummer im E.164-Format darstellt.
- `{ microsoftTeamsUserId: '<TEAMS_USER_ID>', isAnonymous?: boolean; cloud?: "public" | "dod" | "gcch" }`: Objekt, das den Teams-Benutzer darstellt.
- `{ id: string }`: Objekt, das den Bezeichner darstellt, der keinem der anderen Bezeichnertypen entspricht.

- `state`: Ruft den Zustand eines Remoteteilnehmers ab.

    ```js
    const state = remoteParticipant.state;
    ```

Mögliche Zustände:

- `Idle`: Anfangszustand.
- `Connecting`: Übergangszustand, während sich der Teilnehmer mit dem Anruf verbindet.
- `Ringing`: Für den Teilnehmeranruf ertönt ein Klingeln.
- `Connected`: Der Teilnehmer ist mit dem Anruf verbunden.
- `Hold`: Der Teilnehmer wird gehalten.
- `EarlyMedia`: Ansage, die vor dem Verbinden eines Teilnehmers mit dem Anruf wiedergegeben wird.
- `InLobby`: Gibt an, dass sich der Remoteteilnehmer im Wartebereich befindet.
- `Disconnected`: Abschließender Zustand. Der Teilnehmer wird vom Anruf getrennt. Wenn der Remoteteilnehmer seine Netzwerkkonnektivität verliert, ändert sich der Zustand nach zwei Minuten in `Disconnected`.

- `callEndReason`: Um zu erfahren, warum ein Teilnehmer den Anruf verlassen hat, prüfen Sie die Eigenschaft `callEndReason`:

    ```js
    const callEndReason = remoteParticipant.callEndReason;
    const callEndReasonCode = callEndReason.code // (number) code associated with the reason
    const callEndReasonSubCode = callEndReason.subCode // (number) subCode associated with the reason
    ```

- `isMuted`-Status: Um herauszufinden, ob ein Remoteteilnehmer stummgeschaltet wurde, überprüfen Sie die Eigenschaft `isMuted`. Er gibt `Boolean` zurück.

    ```js
    const isMuted = remoteParticipant.isMuted;
    ```

- `isSpeaking`-Status: Um herauszufinden, ob ein Remoteteilnehmer gerade spricht, überprüfen Sie die Eigenschaft `isSpeaking`. Er gibt `Boolean` zurück.

    ```js
    const isSpeaking = remoteParticipant.isSpeaking;
    ```

- `videoStreams`: Um alle Videostreams zu prüfen, die ein bestimmter Teilnehmer im Rahmen dieses Anrufs sendet, sehen Sie sich die `videoStreams`-Sammlung an. Sie enthält `RemoteVideoStream`-Objekte.

    ```js
    const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]
    ```
- `displayName`: Um den Anzeigenamen für diesen Remoteteilnehmer zu erhalten, überprüfen Sie die zurückgegebene Zeichenfolge der Eigenschaft `displayName`. 

    ```js
    const displayName = remoteParticipant.displayName;
    ```

## <a name="check-call-properties"></a>Überprüfen von Anrufeigenschaften

Abrufen der eindeutigen ID (Zeichenfolge) für einen Anruf:

```js
const callId: string = call.id;
```
Abrufen von Informationen zum Anruf:
> [!NOTE]
> Diese API wird als Vorschau für Entwickler bereitgestellt. Je nachdem, welches Feedback wir dazu erhalten, werden möglicherweise Änderungen vorgenommen. Verwenden Sie diese API nicht in einer Produktionsumgebung. Um diese API einzusetzen, verwenden Sie das Betarelease des ACS Calling Web SDK.
```js
const callInfo = call.info;
```

Erhalten Sie Informationen zu anderen Anrufteilnehmern, indem Sie sich die `remoteParticipants`-Sammlung für die call-Instanz ansehen:

```js
const remoteParticipants = call.remoteParticipants;
```

Identifizieren des Anrufers eines eingehenden Anrufs:
```js
const callerIdentity = call.callerInfo.identifier;
```

`identifier` ist einer der `CommunicationIdentifier`-Typen.

Abrufen des Anrufzustands:

```js
const callState = call.state;
```

Gibt eine Zeichenfolge zurück, die den aktuellen Zustand eines Anrufs darstellt:

- `None`: anfänglicher Anrufzustand.
- `Connecting`: anfänglicher Übergangszustand, wenn ein Anruf getätigt oder angenommen wird.
- `Ringing`: Weist bei einem ausgehenden Anruf darauf hin, dass für den Anruf Remoteteilnehmer ein Klingeln ertönt. Auf deren Seite lautet der Status `Incoming`.
- `EarlyMedia`: Gibt einen Zustand an, in dem vor dem Verbinden des Anrufs eine Ansage wiedergegeben wird.
- `Connected`: Gibt an, dass der Anruf verbunden wurde.
- `LocalHold`: Gibt an, dass der Anruf von einem lokalen Teilnehmer gehalten wird. Zwischen dem lokalen Endpunkt und den Remoteteilnehmern werden keine Medien übertragen.
- `RemoteHold`: Gibt an, dass der Anruf von einem Remoteteilnehmer gehalten wird. Zwischen dem lokalen Endpunkt und den Remoteteilnehmern werden keine Medien übertragen.
- `InLobby`: Gibt an, dass sich der Benutzer im Wartebereich befindet.
- `Disconnecting`: Übergangszustand, bevor der Anruf in einen `Disconnected`-Zustand wechselt.
- `Disconnected`: Abschließender Anrufzustand. Wenn die Netzwerkverbindung unterbrochen wird, ändert sich der Zustand nach zwei Minuten in `Disconnected`.

Ermitteln Sie, warum der Anruf beendet wurde, indem Sie die Eigenschaft `callEndReason` überprüfen:

```js
const callEndReason = call.callEndReason;
const callEndReasonCode = callEndReason.code // (number) code associated with the reason
const callEndReasonSubCode = callEndReason.subCode // (number) subCode associated with the reason
```

Erfahren Sie, ob der aktuelle Anruf ein- oder ausgeht, indem Sie die Eigenschaft `direction` überprüfen. Er gibt `CallDirection` zurück.

```js
const isIncoming = call.direction == 'Incoming';
const isOutgoing = call.direction == 'Outgoing';
```

Stellen Sie fest, ob das aktuelle Mikrofon stummgeschaltet ist. Er gibt `Boolean` zurück.

```js
const muted = call.isMuted;
```

Ermitteln Sie, ob der Bildschirmübertragungsstream von einem bestimmten Endpunkt aus gesendet wird, indem Sie die Eigenschaft `isScreenSharingOn` überprüfen. Er gibt `Boolean` zurück.

```js
const isScreenSharingOn = call.isScreenSharingOn;
```

Untersuchen Sie aktive Videostreams, indem Sie sich die `localVideoStreams`-Sammlung ansehen. Sie gibt `LocalVideoStream`-Objekte zurück.

```js
const localVideoStreams = call.localVideoStreams;
```