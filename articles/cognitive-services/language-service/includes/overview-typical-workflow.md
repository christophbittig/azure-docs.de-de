---
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2502698a95671bf2b6ca9960a7f006a12d3a7ce8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029543"
---
## <a name="typical-workflow"></a>Typischer Workflow

Um dieses Feature zu verwenden, übermitteln Sie Daten zur Analyse und verarbeiten die API-Ausgabe in Ihrer Anwendung. Die Analyse wird ohne zusätzliche Anpassung des Modells durchgeführt, das für Ihre Daten verwendet wird.

1. Erstellen Sie eine Azure-Sprachressource, die Ihnen den Zugriff auf die Features von Azure Cognitive Services for Language ermöglicht. Hierbei werden ein Kennwort (als „Schlüssel“ bezeichnet) und eine Endpunkt-URL generiert, die Sie zum Authentifizieren von API-Anforderungen verwenden können.

2. Erstellen Sie eine Anforderung, indem Sie entweder die REST-API oder die Clientbibliothek für C#, Java, JavaScript oder Python verwenden. Sie können auch asynchrone Aufrufe per Batchanforderung senden, um API-Anforderungen für mehrere Features in einem gemeinsamen Aufruf zu kombinieren.

3. Senden Sie die Anforderung, die Ihre Daten enthält, als unformatierten, unstrukturierten Text. Ihr Schlüssel und Endpunkt werden für die Authentifizierung verwendet.

4. Streamen oder speichern Sie die Antwort lokal. 
