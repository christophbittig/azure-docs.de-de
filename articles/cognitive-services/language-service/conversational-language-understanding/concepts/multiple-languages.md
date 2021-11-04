---
title: Mehrsprachige Projekte
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie mehrsprachige Projekte in Language Understanding für Unterhaltungen verwenden.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 4bbad9e51e26d643ff121e9b80b5da3fbafd89b9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095150"
---
# <a name="multilingual-projects"></a>Mehrsprachige Projekte

Language Understanding für Unterhaltungen erleichtert es Ihnen, Ihr Projekt auf mehrere Sprachen gleichzeitig zu erweitern. Wenn Sie mehrere Sprachen in Projekten aktivieren, können Sie Ihrem Projekt sprachspezifische Äußerungen und Synonyme hinzufügen und mehrsprachige Vorhersagen für Ihre Absichten und Entitäten abrufen. 

## <a name="multilingual-intent-and-learned-entity-components"></a>Mehrsprachige Absichts- und erlernte Entitätskomponenten

Wenn Sie mehrere Sprachen in einem Projekt aktivieren, können Sie das Projekt hauptsächlich in einer Sprache trainieren und sofort Vorhersagen in anderen Sprachen abrufen. 

Beispielsweise können Sie Ihr Projekt vollständig mit englischen Äußerungen trainieren und es dann in folgenden Sprachen abfragen: Französisch, Deutsch, Mandarin, Japanisch, Koreanisch und weiteren. Mithilfe von Language Understanding für Unterhaltungen können Sie Ihre Projekte auf einfache Weise auf mehrere Sprachen skalieren, indem Sie Mehrsprachentechnologie zum Trainieren Ihrer Modelle verwenden.

Wenn Sie feststellen, dass eine bestimmte Sprache nicht so gut funktioniert wie andere Sprachen, können Sie Ihrem Projekt Äußerungen für diese Sprache hinzufügen. Auf der Seite [tag utterances](../how-to/tag-utterances.md) (Kennzeichnen von Äußerungen) in Language Studio können Sie die Sprache der hinzugefügten Äußerung auswählen. Wenn Sie Beispiele für die betreffende Sprache in das Modell einführen, wird dem Modell die Syntax dieser Sprache besser verständlich, und es lernt, sie besser vorherzusagen.

Es wird nicht von Ihnen erwartet, für jede Sprache die gleiche Anzahl von Äußerungen hinzuzufügen. Sie sollten den Großteil Ihres Projekts in einer Sprache erstellen und nur wenige Äußerungen in Sprachen hinzufügen, für die Sie eine nicht zufrieden stellende Leistung beobachten. Wenn Sie ein Projekt erstellen, das hauptsächlich englisch ist, und damit beginnen, es in Französisch, Deutsch und Spanisch zu testen, stellen Sie möglicherweise fest, dass Deutsch nicht so gut funktioniert wie die anderen beiden Sprachen. Ziehen Sie in diesem Fall in Betracht, 5 % Ihrer ursprünglichen englischen Beispiele auf Deutsch hinzuzufügen, ein neues Modell zu trainieren und es erneut auf Deutsch zu testen. Für deutsche Abfragen sollten dann bessere Ergebnisse erzielt werden. Je mehr Äußerungen Sie hinzufügen, desto größer ist die Wahrscheinlichkeit, dass die Ergebnisse besser werden. 

Wenn Sie Daten in einer weiteren Sprache hinzufügen, brauchen Sie in der Regel nicht mit negativen Auswirkungen auf die anderen Sprachen zu rechnen. 

## <a name="list-and-prebuilt-components-in-multiple-languages"></a>Listenkomponenten und vordefinierte Komponenten in mehreren Sprachen

Projekte, für die mehrere Sprachen aktiviert sind, ermöglichen ihnen das Angeben von Synonymen für jeden Listenschlüssel **pro Sprache**. Abhängig von der Sprache, mit der Sie Ihr Projekt abfragen, erhalten Sie nur Übereinstimmungen für die Listenkomponente mit Synonymen in dieser Sprache. Wenn Sie Ihr Projekt abfragen, können Sie die Sprache im Anforderungstext angeben:

```json
"query": "{query}"
"language": "{language code}"
```

Wenn Sie keine Sprache angeben, greift das Projekt auf seine Standardsprache zurück. Eine Liste der verschiedenen Sprachcodes finden Sie im Artikel zur [Sprachunterstützung](../language-support.md).

Vordefinierte Komponenten sind ähnlich, und Sie sollten damit rechnen, Vorhersagen für vordefinierte Komponenten zu erhalten, die in bestimmten Sprachen verfügbar sind. Die Sprache der Anforderung bestimmt auch in diesem Fall, für welche Komponenten die Vorhersage versucht wird. Informationen zur Sprachunterstützung der einzelnen vordefinierten Komponenten finden Sie im Referenzartikel zu [vordefinierten Komponenten](../prebuilt-component-reference.md).

## <a name="next-steps"></a>Nächste Schritte

* [Tag-Äußerungen](../how-to/tag-utterances.md) 
* [Trainieren eines Modells](../how-to/train-model.md)
