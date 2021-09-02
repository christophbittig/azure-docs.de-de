---
title: Verwenden mehrerer Slots bei der Personalisierung
description: Erfahren Sie, wie Sie bei der Personalisierung mehrere Slots verwenden, um die vom Dienst bereitgestellten Inhaltsempfehlungen zu verbessern.
services: cognitive-services
author: jeffmend
ms.author: jeffme
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 05/24/2021
zone_pivot_groups: programming-languages-set-six
ms.openlocfilehash: ea1f244b43becd65b3bc85f2e40dc2c029ccbf43
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829263"
---
# <a name="get-started-with-multi-slot-for-azure-personalizer"></a>Erste Schritte mit mehreren Slots bei der Azure-Personalisierung

Mit der Personalisierung mit mehreren Slots (Vorschau) können Sie Inhalte in Weblayouts, Rotationen und Listen als Ziel verwenden, in denen Ihren Benutzern mehr als eine Aktion (z. B. ein Produkt oder ein Inhaltsteil) angezeigt wird. Mit Personalizer-APIs mit mehreren Slots können Sie die KI-Modelle in Personalizer lernen lassen, welche Benutzerkontexte und Produkte bestimmte Verhaltensweisen beeinflussen, und dabei die Platzierung in Ihrer Benutzeroberfläche berücksichtigen und daraus lernen. Zum Beispiel kann Personalizer feststellen, dass bestimmte Produkte oder Inhalte als Seitenleiste oder Fußzeile mehr Klicks bringen als als Haupt-Highlight auf einer Seite. 

In diesem Leitfaden erfahren Sie, wie Sie die Personalisierungs-APIs mit mehreren Slots verwenden.

::: zone pivot="programming-language-csharp"

[!INCLUDE [Try multi-slot with C#](./includes/quickstart-multislot-csharp.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [Try multi-slot with Node.js](./includes/quickstart-multislot-nodejs.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Try multi-slot with Python](./includes/quickstart-multislot-python.md)]

::: zone-end

## <a name="next-steps"></a>Nächste Schritte

* [Weitere Informationen zur Verwendung mehrerer Slots](concept-multi-slot-personalization.md)