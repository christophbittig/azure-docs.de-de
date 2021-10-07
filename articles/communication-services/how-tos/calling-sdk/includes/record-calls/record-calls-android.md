---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: a12f5717d927e8358a503a50f314c6b80f577952
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129585239"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-android.md)]

## <a name="record-calls"></a>Aufzeichnen von Anrufen
> [!WARNING]
> Bis zur Version 1.1.0 und der Betaversion 1.1.0-beta.1 des ACS Calling Android SDK wird `isRecordingActive` verwendet und `addOnIsRecordingActiveChangedListener` ist Teil des `Call`-Objekts. Für die neuen Betaversionen wurden diese APIs als erweitertes Feature von `Call` verschoben, wie unten beschrieben.

> [!NOTE]
> Diese API wird als Vorschau für Entwickler bereitgestellt. Je nachdem, welches Feedback wir dazu erhalten, werden möglicherweise Änderungen vorgenommen. Verwenden Sie diese API nicht in einer Produktionsumgebung. Um diese API einzusetzen, verwenden Sie das Beta-Release des ACS Calling Android SDK.

Die Anrufaufzeichnung ist ein erweitertes Feature der zentralen `Call`-API. Sie müssen zunächst das API-Objekt der Aufzeichnungsfunktion abrufen:

```java
RecordingCallFeature callRecordingFeature = call.api(Features.RECORDING);
```

Um anschließend herauszufinden, ob der Anruf aufgezeichnet wird, prüfen Sie die Eigenschaft `isRecordingActive` von `callRecordingFeature`. Er gibt `boolean` zurück.

```java
boolean isRecordingActive = callRecordingFeature.isRecordingActive();
```

Sie können auch Aufzeichnungsänderungen abonnieren:

```java
private void handleCallOnIsRecordingChanged(PropertyChangedEvent args) {
    boolean isRecordingActive = callRecordingFeature.isRecordingActive();
}

callRecordingFeature.addOnIsRecordingActiveChangedListener(handleCallOnIsRecordingChanged);
```

Wenn Sie die Aufzeichnung aus Ihrer Anwendung heraus starten möchten, folgen Sie bitte zunächst der [Übersicht über die Anrufaufzeichnung](../../../../concepts/voice-video-calling/call-recording.md), um die Schritte zur Einrichtung der Aufzeichnung von Anrufen zu erfahren.

Sobald Sie die Anrufaufzeichnung auf Ihrem Server eingerichtet haben, müssen Sie von Ihrer Android-Anwendung aus den `ServerCallId`-Wert des Anrufs abrufen und ihn dann an Ihren Server senden, um den Aufzeichnungsprozess zu starten. Der `ServerCallId`-Wert kann mithilfe von `getServerCallId()` der `CallInfo`-Klasse verwendet werden, die im Klassenobjekt mit `getInfo()` gefunden werden kann.

```java
try {
    String serverCallId = call.getInfo().getServerCallId().get();
    // Send serverCallId to your recording server to start the call recording.
} catch (ExecutionException | InterruptedException e) {

} catch (UnsupportedOperationException unsupportedOperationException) {

}
```

Wenn die Aufzeichnung vom Server aus gestartet wird, wird das Ereignis `handleCallOnIsRecordingChanged` ausgelöst und der Wert von `callRecordingFeature.isRecordingActive()` wird `true` sein.

Genau wie beim Starten der Anrufaufzeichnung müssen Sie, wenn Sie die Aufzeichnung beenden möchten, die `ServerCallId` abrufen und an Ihren Aufzeichnungsserver senden, sodass dieser die Aufzeichnung beenden kann.

```java
try {
    String serverCallId = call.getInfo().getServerCallId().get();
    // Send serverCallId to your recording server to stop the call recording.
} catch (ExecutionException | InterruptedException e) {

} catch (UnsupportedOperationException unsupportedOperationException) {

}
```

Wenn die Aufzeichnung vom Server aus beendet wird, wird das Ereignis `handleCallOnIsRecordingChanged` ausgelöst und der Wert von `callRecordingFeature.isRecordingActive()` wird `false` sein.
