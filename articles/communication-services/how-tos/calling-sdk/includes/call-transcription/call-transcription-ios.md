---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: d2011720b203493b5b17fabb3d65ca912e7ec37d
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129584984"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-ios.md)]

> [!WARNING]
> Bis zur Version 1.1.0 und der Betaversion 1.1.0-beta.1 des ACS Calling iOS SDK wird `isTranscriptionActive` als Teil des `Call`-Objekts verwendet und `didChangeTranscriptionState` ist Teil des `CallDelegate`-Delegaten. Für die neuen Betaversionen wurden diese APIs als erweitertes Feature von `Call` verschoben, wie unten beschrieben.
> [!NOTE]
> Diese API wird als Vorschau für Entwickler bereitgestellt. Je nachdem, welches Feedback wir dazu erhalten, werden möglicherweise Änderungen vorgenommen. Verwenden Sie diese API nicht in einer Produktionsumgebung. Um diese API einzusetzen, verwenden Sie das Beta-Release des ACS Calling iOS SDK.

Die Anruftranskription ist ein erweitertes Feature der zentralen `Call`-API. Sie müssen zunächst das API-Objekt des Transkriptionsfeatures abrufen:

```swift
let callTranscriptionFeature = call.api(Features.transcription)
```

Um anschließend herauszufinden, ob der Anruf transkribiert wird, prüfen Sie die Eigenschaft `isTranscriptionActive` von `callTranscriptionFeature`. Er gibt `Bool` zurück.

```swift
let isTranscriptionActive = callTranscriptionFeature.isTranscriptionActive;
```

Sie können die Transkriptionsänderungen auch abonnieren, indem Sie den `TranscriptionCallFeatureDelegate`-Delegaten für Ihre Klasse mit dem Ereignis `didChangeTranscriptionState` implementieren:

```swift
callTranscriptionFeature.delegate = self

// didChangeTranscriptionState is a member of TranscriptionCallFeatureDelegate
public func transcriptionCallFeature(_ transcriptionCallFeature: TranscriptionCallFeature, didChangeTranscriptionState args: PropertyChangedEventArgs) {
    let isTranscriptionActive = callTranscriptionFeature.isTranscriptionActive
}
```
