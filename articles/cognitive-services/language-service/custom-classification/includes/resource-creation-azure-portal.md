---
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 93ee37d6ef607a65f5b8be68d71c46fc258387f4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095025"
---
### <a name="create-a-new-resource-from-the-azure-portal"></a>Erstellen einer neuen Ressource im Azure-Portal

Wechseln Sie zum [Azure-Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics), um eine neue Azure-Sprachressource zu erstellen. Wenn Sie aufgefordert werden, zusätzliche Features auszuwählen, wählen Sie **Diesen Schritt überspringen** aus. Stellen Sie beim Erstellen Ihrer Ressource sicher, dass sie die folgenden Parameter aufweist.  

|Anforderung  |Erforderlicher Wert  |
|---------|---------|
|Ort | „USA, Westen 2“ oder „Europa, Westen“         |
|Tarif     | Tarif „Standard“ (**S**)        |

> [!IMPORTANT]
> Achten Sie darauf, im Abschnitt **Benutzerdefinierte Erkennung benannter Entitäten (NER) & Benutzerdefinierte Klassifizierung (Vorschau)** ein vorhandenes Speicherkonto auszuwählen, oder erstellen Sie ein neues. Zum Verwenden der benutzerdefinierten Textklassifizierung ist ein Speicherkonto erforderlich. Sie können zwar auch später ein Speicherkonto angeben, dies ist jetzt jedoch einfacher. 
