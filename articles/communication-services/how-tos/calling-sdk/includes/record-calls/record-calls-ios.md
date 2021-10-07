---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 029e4ce2f699eb66f0807f47cc80a893052f7a57
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129585043"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-ios.md)]

## <a name="record-calls"></a>Aufzeichnen von Anrufen
> [!WARNING]
> Bis zur Version 1.1.0 und der Betaversion 1.1.0-beta.1 des ACS Calling iOS SDK wird `isRecordingActive` als Teil des `Call`-Objekts verwendet und `didChangeRecordingState` ist Teil des `CallDelegate`-Delegaten. Für die neuen Betaversionen wurden diese APIs als erweitertes Feature von `Call` verschoben, wie unten beschrieben.
> [!NOTE]
> Diese API wird als Vorschau für Entwickler bereitgestellt. Je nachdem, welches Feedback wir dazu erhalten, werden möglicherweise Änderungen vorgenommen. Verwenden Sie diese API nicht in einer Produktionsumgebung. Um diese API einzusetzen, verwenden Sie das Beta-Release des ACS Calling iOS SDK.

Die Anrufaufzeichnung ist ein erweitertes Feature der zentralen `Call`-API. Sie müssen zunächst das API-Objekt der Aufzeichnungsfunktion abrufen:

```swift
let callRecordingFeature = call.api(Features.recording)
```

Um anschließend herauszufinden, ob der Anruf aufgezeichnet wird, prüfen Sie die Eigenschaft `isRecordingActive` von `callRecordingFeature`. Er gibt `Bool` zurück.

```swift
let isRecordingActive = callRecordingFeature.isRecordingActive;
```

Sie können die Aufzeichnung von Änderungen auch abonnieren, indem Sie den `RecordingCallFeatureDelegate`-Delegaten für Ihre Klasse mit dem Ereignis `didChangeRecordingState` implementieren:

```swift
callRecordingFeature.delegate = self

// didChangeRecordingState is a member of RecordingCallFeatureDelegate
public func recordingCallFeature(_ recordingCallFeature: RecordingCallFeature, didChangeRecordingState args: PropertyChangedEventArgs) {
    let isRecordingActive = recordingFeature.isRecordingActive
}
```

Wenn Sie die Aufzeichnung aus Ihrer Anwendung heraus starten möchten, folgen Sie bitte zunächst der [Übersicht über die Anrufaufzeichnung](../../../../concepts/voice-video-calling/call-recording.md), um die Schritte zur Einrichtung der Aufzeichnung von Anrufen zu erfahren.

Sobald Sie die Anrufaufzeichnung auf Ihrem Server eingerichtet haben, müssen Sie von Ihrer iOS-Anwendung aus den `ServerCallId`-Wert des Anrufs abrufen und ihn dann an Ihren Server senden, um den Aufzeichnungsprozess zu starten. Der `ServerCallId`-Wert kann mithilfe von `getServerCallId()` der `CallInfo`-Klasse verwendet werden, die im Klassenobjekt mit `getInfo()` gefunden werden kann.

```swift
// Send serverCallId to your recording server to start the call recording.
let serverCallId = call.info.getServerCallId(){ (serverId, error) in }
```

Wenn die Aufzeichnung vom Server aus gestartet wird, wird das Ereignis `didChangeRecordingState` ausgelöst und der Wert von `recordingFeature.isRecordingActive` wird `true` sein.

Genau wie beim Starten der Anrufaufzeichnung müssen Sie, wenn Sie die Aufzeichnung beenden möchten, die `ServerCallId` abrufen und an Ihren Aufzeichnungsserver senden, sodass dieser die Aufzeichnung beenden kann.

```swift
// Send serverCallId to your recording server to stop the call recording.
let serverCallId = call.info.getServerCallId(){ (serverId, error) in }
```

Wenn die Aufzeichnung vom Server aus beendet wird, wird das Ereignis `didChangeRecordingState` ausgelöst und der Wert von `recordingFeature.isRecordingActive` wird `false` sein.
