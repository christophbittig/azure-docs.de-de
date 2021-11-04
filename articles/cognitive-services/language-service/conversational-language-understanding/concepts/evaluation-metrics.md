---
title: Auswertungsmetriken für Conversational Language Understanding
titleSuffix: Azure Cognitive Services
description: Erfahren Sie mehr über Auswertungsmetriken in Conversational Language Understanding
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: b81273e3955ab866648a1d70f01991272ecba787
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131100335"
---
# <a name="evaluation-metrics-for-conversational-language-understanding-models"></a>Auswertungsmetriken für Conversational Language Understanding-Modelle

Die Modellauswertung in Language Understanding für Unterhaltungen verwendet die folgenden Metriken:


|Metric |BESCHREIBUNG  |Berechnung  |
|---------|---------|---------|
|Precision     |  Das Verhältnis von erfolgreichen Erkennungen zu allen versuchten Erkennungen. Dies zeigt, wie oft die Entitätserkennung des Modells wirklich eine gute Erkennung darstellt.       | `Precision = #True_Positive / (#True_Positive + #False_Positive)`        |
|Recall     | Das Verhältnis von erfolgreichen Erkennungen zur tatsächlichen Anzahl vorhandener Entitäten.        | `Recall = #True_Positive / (#True_Positive + #False_Negatives)`        |
|F1-Score    |  Steht für die Kombination aus Genauigkeit und Abruf.       |  `F1 Score = 2 * Precision * Recall / (Precision + Recall)`       |

## <a name="model-level-and-entity-level-evaluation-metrics"></a>Auswertungsmetriken auf Modell- und Entitätsebene

Genauigkeit, Abruf und F1-Score werden für jede Entität separat (Auswertung auf Entitätsebene) und für das Modell insgesamt (Auswertung auf Modellebene) berechnet.

Die Definitionen für Genauigkeit, Abruf und Auswertung sind für Auswertungen auf Entitäts- und Modellebene identisch. Die Anzahl von *True Positives*, *False Positives* und *False Negatives* kann aber variieren. Sehen Sie sich beispielsweise den folgenden Text an:

### <a name="example"></a>Beispiel

*Die erste Partei dieses Vertrags ist John Smith, 5678 Main Rd., Frederick, Bundesstaat Nebraska. Die zweite Partei ist Forrest Ray, 123 - 345 Integer Rd., Corona, Bundesstaat New Mexico. Eine weitere Partei ist Fannie Thomas, 7890 River Road, Colorado Springs, Bundesstaat Colorado.*

Das Modell, mit dem Entitäten aus diesem Text extrahiert werden, könnte beispielsweise die folgenden Vorhersagen aufweisen:

| Entität | Vorhergesagt als | Tatsächlicher Typ |
|--|--|--|
| Johan Lorenz | Person | Person |
| Frederick | Person | City |
| Forrest | City | Person |
| Fannie Thomas | Person | Person |
| Colorado Springs | City | City |

### <a name="entity-level-evaluation-for-the-person-entity"></a>Auswertung auf Entitätsebene für die Entität *Person* 

Das Modell würde für die Entität *Person* die folgende Auswertung auf Entitätsebene aufweisen:

| Schlüssel | Anzahl | Erklärung |
|--|--|--|
| Richtig positiv | 2 | *John Smith* und *Fannie Thomas* wurden richtig als *Person* vorhergesagt. |
| Falsch positiv | 1 | *Frederick* wurde falsch als *Person* vorhergesagt und hätte *Stadt* lauten sollen. |
| Falsch negativ | 1 | *Forrest* wurde falsch als *Stadt* vorhergesagt und hätte *Person* lauten sollen. |

* **Genauigkeit**: `#True_Positive / (#True_Positive + #False_Positive)` = `2 / (2 + 1) = 0.67`
* **Abruf**: `#True_Positive / (#True_Positive + #False_Negatives)` = `2 / (2 + 1) = 0.67`
* **F1-Score**: `2 * Precision * Recall / (Precision + Recall)` = `(2 * 0.67 * 0.67) / (0.67 + 0.67) = 0.67`

### <a name="entity-level-evaluation-for-the-city-entity"></a>Auswertung auf Entitätsebene für die Entität *Stadt*

Das Modell würde für die Entität *Stadt* die folgende Auswertung auf Entitätsebene aufweisen:

| Schlüssel | Anzahl | Erklärung |
|--|--|--|
| Richtig positiv | 1 | *Colorado Springs* wurde richtig als *Stadt* vorhergesagt. |
| Falsch positiv | 1 | *Forrest* wurde falsch als *Stadt* vorhergesagt und hätte *Person* lauten sollen. |
| Falsch negativ | 1 | *Frederick* wurde falsch als *Person* vorhergesagt und hätte *Stadt* lauten sollen. |

* **Genauigkeit** = `#True_Positive / (#True_Positive + #False_Positive)` = `2 / (2 + 1) = 0.67`
* **Abruf** = `#True_Positive / (#True_Positive + #False_Negatives)` = `2 / (2 + 1) = 0.67`
* **F1-Score** = `2 * Precision * Recall / (Precision + Recall)` =  `(2 * 0.67 * 0.67) / (0.67 + 0.67) = 0.67`

### <a name="model-level-evaluation-for-the-collective-model"></a>Auswertung auf Modellebene für das gesamte Modell

Das Modell würde die folgende Auswertung für das gesamte Modell aufweisen:

| Schlüssel | Anzahl | Erklärung |
|--|--|--|
| Richtig positiv | 3 | *John Smith* und *Fannie Thomas* wurden richtig als *Person* vorhergesagt. *Colorado Springs* wurde richtig als *Stadt* vorhergesagt. Dies ist die Summe der True Positives für alle Entitäten. |
| Falsch positiv | 2 | *Forrest* wurde falsch als *Stadt* vorhergesagt und hätte *Person* lauten sollen. *Frederick* wurde falsch als *Person* vorhergesagt und hätte *Stadt* lauten sollen. Dies ist die Summe der False Positives für alle Entitäten. |
| Falsch negativ | 2 | *Forrest* wurde falsch als *Stadt* vorhergesagt und hätte *Person* lauten sollen. *Frederick* wurde falsch als *Person* vorhergesagt und hätte *Stadt* lauten sollen. Dies ist die Summe der False Negatives für alle Entitäten. |

* **Genauigkeit** = `#True_Positive / (#True_Positive + #False_Positive)` = `3 / (3 + 2) = 0.6`
* **Abruf** = `#True_Positive / (#True_Positive + #False_Negatives)` = `3 / (3 + 2) = 0.6`
* **F1-Score** = `2 * Precision * Recall / (Precision + Recall)` =  `(2 * 0.6 * 0.6) / (0.6 + 0.6) = 0.6`

## <a name="interpreting-entity-level-evaluation-metrics"></a>Interpretieren von Auswertungsmetriken auf Entitätsebene

Was genau bedeutet also ein hoher Genauigkeitswert oder ein hoher Abrufwert für eine bestimmte Entität?

| Recall | Precision | Interpretation |
|--|--|--|
| High | High | Diese Entität wird vom Modell gut verarbeitet. |
| Niedrig | High | Das Modell kann diese Entität nicht immer extrahieren, aber wenn es möglich ist, erfolgt dies mit hoher Konfidenz. |
| High | Niedrig | Das Modell kann diese Entität gut extrahieren, aber die Konfidenz ist niedrig, da manchmal die Extraktion als anderer Typ erfolgt. |
| Niedrig | Niedrig | Dieser Entitätstyp wird vom Modell nicht gut verarbeitet, da er in der Regel nicht extrahiert wird. Gelingt die Extraktion trotzdem, ist die Konfidenz niedrig. |

## <a name="confusion-matrix"></a>Konfusionsmatrix

Eine Konfusionsmatrix ist eine n x n-Matrix, die für die Auswertung der Modellleistung verwendet wird, wobei „n“ die Anzahl von Entitäten ist.
Die Matrix vergleicht die tatsächlichen Tags mit den vom Modell vorhergesagten Tags.
Dadurch erhalten Sie einen ganzheitlichen Überblick darüber, wie gut das Modell funktioniert und welche Arten von Fehlern es macht.

Sie können die Konfusionsmatrix verwenden, um Entitäten zu identifizieren, die zu nah beieinander liegen und häufig verwechselt werden (Mehrdeutigkeit). Erwägen Sie in diesem Fall, die betreffenden Entitätstypen zusammenzuführen. Wenn dies nicht möglich ist, sollten Sie erwägen, weitere Beispiele mit Tags für beide Entitäten hinzuzufügen. So können Sie es für das Modell einfacher machen, dazwischen zu unterscheiden.

Die hervorgehobene Diagonale in der Abbildung steht für die richtig vorhergesagten Entitäten, wobei das vorhergesagte Tag mit dem tatsächlichen Tag identisch ist.

:::image type="content" source="../media/confusion-matrix-example.png" alt-text="Screenshot: Beispiel für Konfusionsmatrix" lightbox="../media/confusion-matrix-example.png":::

Anhand der Konfusionsmatrix können Sie die Auswertungsmetriken auf Entitäts- und Modellebene berechnen:

* Die Werte auf der Diagonalen sind die *True Positive*-Werte der einzelnen Entitäten.
* Die Summe der Werte in den Entitätszeilen (ausschließlich der Diagonalen) sind die *False Positive*-Werte des Modells.
* Die Summe der Werte in den Entitätsspalten (ausschließlich der Diagonalen) sind die *False Negative*-Werte des Modells.

Ebenso gilt:

* Der *True Positive*-Wert des Modells ist die Summe der *True Positive*-Werte für alle Entitäten.
* Der *False Positive*-Wert des Modells ist die Summe der *False Positive*-Werte für alle Entitäten.
* Der *False Negative*-Wert des Modells ist die Summe der *False Negative*-Werte für alle Entitäten.

## <a name="next-steps"></a>Nächste Schritte

[Trainieren eines Modells in Language Studio](../how-to/train-model.md)
