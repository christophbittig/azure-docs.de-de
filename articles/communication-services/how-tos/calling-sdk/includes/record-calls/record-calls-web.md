---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 7c739c65e638657cffbfade0c2a491cdefd6b626
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "128700137"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-web.md)]

### <a name="record-calls"></a>Aufzeichnen von Anrufen
> [!NOTE]
> Diese API wird als Vorschau für Entwickler bereitgestellt. Je nachdem, welches Feedback wir dazu erhalten, werden möglicherweise Änderungen vorgenommen. Verwenden Sie diese API nicht in einer Produktionsumgebung. Um diese API einzusetzen, verwenden Sie das Betarelease des ACS Calling Web SDK.

Die Anrufaufzeichnung ist ein erweitertes Feature der zentralen `Call`-API. Sie müssen zunächst das API-Objekt der Aufzeichnungsfunktion abrufen:

```js
const callRecordingApi = call.api(Features.Recording);
```

Um anschließend herauszufinden, ob der Anruf aufgezeichnet wird, prüfen Sie die Eigenschaft `isRecordingActive` von `callRecordingApi`. Er gibt `Boolean` zurück.

```js
const isRecordingActive = callRecordingApi.isRecordingActive;
```

Sie können auch Aufzeichnungsänderungen abonnieren:

```js
const isRecordingActiveChangedHandler = () => {
    console.log(callRecordingApi.isRecordingActive);
};

callRecordingApi.on('isRecordingActiveChanged', isRecordingActiveChangedHandler);
```