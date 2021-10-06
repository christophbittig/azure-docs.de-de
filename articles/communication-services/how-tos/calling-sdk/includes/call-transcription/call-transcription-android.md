---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: b880f8a8f84247c14a0f1e81577d9e252b2b78c8
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129585173"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-android.md)]

> [!WARNING]
> Bis zur Version 1.1.0 und der Betaversion 1.1.0-beta.1 des ACS Calling Android SDK wird `isTranscriptionActive` verwendet und `addOnIsTranscriptionActiveChangedListener` ist Teil des `Call`-Objekts. Für die neuen Betaversionen wurden diese APIs als erweitertes Feature von `Call` verschoben, wie unten beschrieben.

> [!NOTE]
> Diese API wird als Vorschau für Entwickler bereitgestellt. Je nachdem, welches Feedback wir dazu erhalten, werden möglicherweise Änderungen vorgenommen. Verwenden Sie diese API nicht in einer Produktionsumgebung. Um diese API einzusetzen, verwenden Sie das Beta-Release des ACS Calling Android SDK.

Die Anruftranskription ist ein erweitertes Feature der zentralen `Call`-API. Sie müssen zunächst das API-Objekt des Transkriptionsfeatures abrufen:

```java
TranscriptionCallFeature callTranscriptionFeature = call.api(Features.TRANSCRIPTION);
```

Um anschließend herauszufinden, ob der Anruf transkribiert wird, prüfen Sie die Eigenschaft `isTranscriptionActive` von `callTranscriptionFeature`. Er gibt `boolean` zurück.

```java
boolean isTranscriptionActive = callTranscriptionFeature.isTranscriptionActive();
```

Sie können auch Transkriptionsänderungen abonnieren:

```java
private void handleCallOnIsTranscriptionChanged(PropertyChangedEvent args) {
    boolean isTranscriptionActive = callTranscriptionFeature.isTranscriptionActive();
}

callTranscriptionFeature.addOnIsTranscriptionActiveChangedListener(handleCallOnIsTranscriptionChanged);
```
