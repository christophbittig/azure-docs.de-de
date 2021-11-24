---
title: Entitätskomponenten in Conversational Language Understanding
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Conversational Language Understanding Entitäten aus Text extrahiert
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: c42028b10a71a039a208f82cfacb58e5ba8e07cc
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347725"
---
# <a name="entity-components"></a>Entitätskomponenten

Bei Conversational Language Understanding sind Entitäten relevante Informationselemente, die aus Ihren Äußerungen extrahiert werden. Eine Entität kann mit verschiedenen Methoden extrahiert werden. Sie kann über den Kontext gelernt, aus einer Liste abgeglichen oder von einer vordefinierten erkannten Entität erkannt werden. Jede Entität in Ihrem Projekt besteht aus einer oder mehreren dieser Methoden, die als Komponenten Ihrer Entität definiert sind. Wenn eine Entität von mehreren Komponenten definiert wird, können sich ihre Vorhersagen überlappen. Sie können das Verhalten einer Entitätsvorhersage bestimmen, wenn sich ihre Komponenten überlappen, indem Sie einen festen Satz von Optionen in der **Überlappungsmethode²** verwenden.

## <a name="component-types"></a>Komponententypen

Eine Entitätskomponente bestimmt, wie Sie die Entität extrahieren können. Eine Entität kann einfach eine Komponente enthalten, die die einzige Methode bestimmen würde, die zum Extrahieren der Entität verwendet wird, oder mehrere Komponenten, um die Möglichkeiten zu erweitern, mit denen die Entität definiert und extrahiert wird.

### <a name="learned-component"></a>„Gelernt“-Komponente

Die „Gelernt“-Komponente verwendet die Entitätstags, mit der Sie Ihre Äußerungen bezeichnen, um ein durch maschinelles Lernen trainiertes Modell zu trainieren. Das Modell lernt, basierend auf dem Kontext innerhalb der Äußerung vorherzusagen, wo sich die Entität befindet. Ihre Etiketten enthalten Beispiele dafür, wo die Entität in einer Äußerung vorhanden sein sollte, basierend auf der Bedeutung der Wörter um sie herum und als die Wörter, die etikettiert wurden. Diese Komponente wird nur definiert, wenn Sie Etiketten hinzufügen, indem Sie Äußerungen für die Entität markieren. Wenn Sie keine Äußerungen mit der Entität markieren, so erhält sie nicht die „Gelernt“-Komponente.

:::image type="content" source="../media/learned-component.png" alt-text="Ein Screenshot, der ein Beispiel für „Gelernt“-Komponenten für Entitäten zeigt." lightbox="../media/learned-component.png":::

### <a name="list-component"></a>Listenkomponente

Die Listenkomponente stellt einen festen, abgeschlossenen Satz verwandter Wörter zusammen mit ihren Synonymen dar. Die Komponente führt einen genauen Textabgleich mit der Liste der Werte aus, die Sie als Synonyme angeben. Jedes Synonym gehört zu einem „Listenschlüssel“, der als normalisierter Standardwert für das Synonym verwendet werden kann, welches in der Ausgabe zurückgegeben wird, wenn die Listenkomponente abgeglichen wird. Listenschlüssel werden **nicht** für den Abgleich verwendet.


:::image type="content" source="../media/list-component.png" alt-text="Ein Screenshot, der ein Beispiel für Listenkomponenten für Entitäten zeigt." lightbox="../media/list-component.png":::

### <a name="prebuilt-component"></a>Vordefinierte Komponente

Mit der vordefinierten Komponente können Sie aus einer Bibliothek allgemeiner Typen wie Zahlen, Datetimes und Namen auswählen. Beim Hinzufügen wird automatisch eine vordefinierte Komponente erkannt. Sie können bis zu 5 vordefinierte Komponenten pro Entität haben. Weitere Informationen finden Sie in der [Liste mit den unterstützten vordefinierten Komponenten](../prebuilt-component-reference.md).


:::image type="content" source="../media/prebuilt-component.png" alt-text="Ein Screenshot, der ein Beispiel für vordefinierte Komponenten für Entitäten zeigt." lightbox="../media/prebuilt-component.png":::


## <a name="overlap-methods"></a>Überlappungsmethoden

Wenn mehrere Komponenten für eine Entität definiert sind, können sich ihre Vorhersagen überlappen. Wenn eine Überlappung auftritt, wird die endgültige Vorhersage jeder Entität durch eine der folgenden Optionen bestimmt.

### <a name="longest-overlap"></a>Längste Überlappung

Wenn im Text zwei oder mehr Komponenten gefunden werden und die Überlappungsmethode verwendet wird, wird die Komponente mit dem **längsten Zeichensatz** zurückgegeben.

Diese Option wird am besten verwendet, wenn Sie daran interessiert sind, die möglichst längste Vorhersage durch die verschiedenen Komponenten zu extrahieren. Mit dieser Methode wird sichergestellt, dass die zurückgegebene Komponente immer dann am längsten ist, wenn Verwirrung (Überlappung) vorliegt.

#### <a name="examples"></a>Beispiele

Wenn „Palm Beach“ von der Listenkomponente abgeglichen und die „Palm Beach-Erweiterung“ von der „Gelernt“-Komponente vorhergesagt wurde, dann wird die „**Palm Beach-Erweiterung**“ zurückgegeben, da sie der längste Zeichensatz in dieser Überlappung ist.

:::image type="content" source="../media/return-longest-overlap-example-1.svg" alt-text="Ein Screenshot, der ein Beispiel für die längsten Überlappungsergebnisse für Komponenten zeigt." lightbox="../media/return-longest-overlap-example-1.svg":::

Wenn „Palm Beach“ von der Listenkomponente abgeglichen und „Beach-Erweiterung“ von der „Gelernt“-Komponente vorhergesagt wurde, dann wird die „**Beach-Erweiterung**“ zurückgegeben, da sie der längste Zeichensatz in dieser Überlappung ist.

:::image type="content" source="../media/return-longest-overlap-example-2.svg" alt-text="Ein Screenshot, der ein zweites Beispiel für die längsten Überlappungsergebnisse für Komponenten zeigt." lightbox="../media/return-longest-overlap-example-2.svg":::

Wenn „Palm Beach“ aus der Listenkomponente abgeglichen wurde und „Erweiterung“ von der „Gelernt“-Komponente vorhergesagt wurde, werden 2 separate Instanzen der Entitäten zurückgegeben, da es keine Überlappung zwischen ihnen gibt: eine für „**Palm Beach**“ und eine für „**Erweiterung**“, da in dieser Instanz keine Überlappung aufgetreten ist.

:::image type="content" source="../media/return-longest-overlap-example-3.svg" alt-text="Ein Screenshot, der ein drittes Beispiel für die längsten Überlappungsergebnisse für Komponenten zeigt." lightbox="../media/return-longest-overlap-example-3.svg":::

### <a name="exact-overlap"></a>Genaue Überlappung

Alle Komponenten müssen sich mit genau den **gleichen Zeichen** im Text überlappen, damit die Entität zurückgegeben werden kann. Wenn eine der definierten Komponenten nicht abgeglichen oder vorhergesagt wird, wird die Entität nicht zurückgegeben.

Diese Option ist am besten geeignet, wenn Sie über eine strenge Entität verfügen, die mehrere Komponenten gleichzeitig erkennen muss, um extrahiert zu werden.

#### <a name="examples"></a>Beispiele

Wenn „Palm Beach“ von der Listenkomponente abgeglichen wurde und „Palm Beach“ von der „Gelernt“-Komponente vorhergesagt wurde und dies die einzigen beiden in der Entität definierten Komponenten waren, wird „**Palm Beach**“ zurückgegeben, da sich alle Komponenten mit genau den gleichen Zeichen überlappen.

:::image type="content" source="../media/require-exact-overlap-example-1.svg" alt-text="Ein Screenshot, der ein Beispiel für die genauen Überlappungsergebnisse für Komponenten zeigt." lightbox="../media/require-exact-overlap-example-1.svg":::

Wenn „Palm Beach“ von der Listenkomponente abgeglichen und „Beach-Erweiterung“ von der „Gelernt“-Komponente vorhergesagt wurde, dann wird Entität **nicht** zurückgegeben, da sich alle Komponenten nicht mit genau den gleichen Zeichen überlappt haben.

:::image type="content" source="../media/require-exact-overlap-example-2.svg" alt-text="Ein Screenshot, der ein zweites Beispiel für die genauen Überlappungsergebnisse für Komponenten zeigt." lightbox="../media/require-exact-overlap-example-2.svg":::

Wenn „Palm Beach“ von der Listenkomponente abgeglichen und „Erweiterung“ von der „Gelernt“-Komponente vorhergesagt wurde, dann wird Entität **nicht** zurückgegeben, da für diese Instanz keine Überlappung aufgetreten ist.

:::image type="content" source="../media/require-exact-overlap-example-3.svg" alt-text="Ein Screenshot, der ein drittes Beispiel für die genauen Überlappungsergebnisse für Komponenten zeigt." lightbox="../media/require-exact-overlap-example-3.svg":::

### <a name="union-overlap"></a>Vereinigungsüberlappung

Wenn im Text zwei oder mehr Komponenten gefunden werden und sich überlappen, wird die **Vereinigung** der Komponentenspannen zurückgegeben.

Diese Option eignet sich am besten, wenn Sie für den Abruf optimieren und versuchen, die längste mögliche Übereinstimmung zu erhalten, die kombiniert werden kann.

#### <a name="examples"></a>Beispiele

Wenn „Palm Beach“ von der Listenkomponente abgeglichen wurde und „Palm Beach-Erweiterung“ von der „Gelernt“-Komponente vorhergesagt wurde, wird „**Palm Beach Extension**“ zurückgegeben, da das erste Zeichen am Anfang der Überlappung das „P“ in „Palm“ und der letzte Buchstabe am Ende der überlappenden Komponenten das „g“ in „Erweiterung“ ist.

:::image type="content" source="../media/return-union-example-1.svg" alt-text="Ein Screenshot, der ein Beispiel für die Vereinigungsüberlappungsergebnisse für Komponenten zeigt." lightbox="../media/return-union-example-1.svg":::

Wenn „Palm Beach“ von der Listenkomponente abgeglichen wurde und „Beach-Erweiterung“ von der „Gelernt“-Komponente vorhergesagt wurde, wird „**Palm Beach Extension**“ zurückgegeben, da das erste Zeichen am Anfang der Überlappung das „P“ in „Palm“ und der letzte Buchstabe am Ende der überlappenden Komponenten das „g“ in „Erweiterung“ ist.

:::image type="content" source="../media/return-union-example-2.svg" alt-text="Ein Screenshot, der ein zweites Beispiel für die Vereinigungsüberlappungsergebnisse für Komponenten zeigt." lightbox="../media/return-union-example-2.svg":::

Wenn „New York“ von der vordefinierten Komponente abgeglichen wurde, „York Beach“ von der Listenkomponente abgeglichen wurde und „Beach-Erweiterung“ von der „Gelernt“-Komponente vorhergesagt wurde, wird „__**New York Beach-Erweiterung**__“ zurückgegeben, da das erste Zeichen am Anfang der Überlappung das „N“ in „New“ und der letzte Buchstabe am Ende der überlappenden Komponenten das „g“ in „Erweiterung“ ist.

:::image type="content" source="../media/return-union-example-3.svg" alt-text="Ein Screenshot, der ein drittes Beispiel für die Vereinigungsüberlappungsergebnisse für Komponenten zeigt." lightbox="../media/return-union-example-3.svg":::

### <a name="return-all-separately"></a>Alle separat zurückgeben

Die Abgleichung oder Vorhersage jeder Komponente wird als **separate Instanz** der Entität zurückgegeben.

Diese Option eignet sich am besten, wenn Sie nach der Vorhersage Ihre eigene Überlappungslogik für die Entität anwenden möchten.

#### <a name="examples"></a>Beispiele

Wenn „Palm Beach“ von der Listenkomponente abgeglichen und die „Palm Beach-Erweiterung“ von der „Gelernt“-Komponente vorhergesagt wurde, dann gibt die Entität zwei Instanzen zurück: eine für „**Palm Beach**“ und eine andere für „**Palm Beach-Erweiterung**“.

:::image type="content" source="../media/return-all-overlaps-example-1.svg" alt-text="Ein Screenshot, der ein Beispiel für die Rückgabe aller Überlappungsergebnisse für Komponenten zeigt." lightbox="../media/return-all-overlaps-example-1.svg":::

Wenn „New York“ von der vordefinierten Komponente vorhergesagt wurde, „York Beach“ von der Listenkomponente abgeglichen wurde und „Beach-Erweiterung“ von der „Gelernt“-Komponente vorhergesagt wurde, gibt die Entität 3 Instanzen zurück: eine für „**New York**“, eine für „**York Beach**“ und eine für „**Beach-Erweiterung**“.

:::image type="content" source="../media/return-all-overlaps-example-2.svg" alt-text="Ein Screenshot, der ein zweites Beispiel für die Rückgabe aller Überlappungsergebnisse für Komponenten zeigt." lightbox="../media/return-all-overlaps-example-2.svg":::

## <a name="next-steps"></a>Nächste Schritte

[Unterstützte vordefinierte Komponenten](../prebuilt-component-reference.md)
