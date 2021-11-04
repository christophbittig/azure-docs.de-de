---
title: Auswertungsmetriken für die benutzerdefinierte Klassifizierung
titleSuffix: Azure Cognitive Services
description: Erfahren Sie mehr über Auswertungsmetriken bei der Extraktion benutzerdefinierter Entitäten.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 53765ebe1862b4c46a1b4ad8caf75f64f6203e37
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131100416"
---
# <a name="evaluation-metrics"></a>Auswertungsmetriken

Ihr [Dataset ist aufgeteilt](../how-to/train-model.md#data-splits), nämlich in zwei Sätze, einen Satz für das Training und einen Satz für das Testen. Der Trainingssatz wird beim Erstellen des Modells verwendet, während der Testsatz als blinder Satz dient, um die Leistung des Modells nach Abschluss des Trainings zu bewerten.

Die Modellauswertung wird ausgelöst, nachdem das Training erfolgreich abgeschlossen wurde. Der Auswertungsprozess beginnt damit, dass das trainierte Modell zum Vorhersagen benutzerdefinierter Klassen für Modelle im Testsatz und für deren Vergleich mit den bereitgestellten Datentags verwendet wird (wodurch eine Wahrheitsbaseline gebildet wird). Die Ergebnisse werden zurückgegeben, sodass Sie die Leistung des Modells überprüfen können. Zur Auswertung verwendet die benutzerdefinierte Textklassifizierung die folgenden Metriken:

* **Genauigkeit:** Misst, wie präzise/genau Ihr Modell ist. Dies ist das Verhältnis zwischen den richtig identifizierten positiven (True Positives) und allen identifizierten positiven Ergebnissen. Die Genauigkeitsmetrik gibt an, wie viele der vorhergesagten Klassen richtig gekennzeichnet wurden. 

    `Precision = #True_Positive / (#True_Positive + #False_Positive)`

* **Abruf**: Misst die Fähigkeit des Modells, tatsächliche positive Klassen vorherzusagen. Dies ist das Verhältnis zwischen den vorhergesagten True Positives und den tatsächlich gekennzeichneten Klassen. Die Abrufmetrik gibt an, wie viele der vorhergesagten Klassen richtig sind.

    `Recall = #True_Positive / (#True_Positive + #False_Negatives)`

* **F1-Bewertung**: Die F1-Bewertung ist eine Funktion von Genauigkeit und Abruf. Sie ist erforderlich, wenn Sie ein Gleichgewicht zwischen Genauigkeit und Abruf suchen.

    `F1 Score = 2 * Precision * Recall / (Precision + Recall)` <br> 

>[!NOTE]
> Genauigkeit, Abruf und F1-Bewertung werden für jede Klasse separat (Auswertung auf *Klassenebene*) und für das Modell im Ganzen (Auswertung auf *Modellebene*) berechnet.

## <a name="model-level-and-class-level-evaluation-metrics"></a>Auswertungsmetriken auf Modell- und Klassenebene

Die Definitionen von Genauigkeit, Abruf und Auswertung sind für Auswertungen auf Klassen- und Modellebene gleich. Die Anzahl von *True Positives*, *False Positives* und *False Negatives* weicht jedoch ab, wie im folgenden Beispiel gezeigt.

In den Abschnitten unten wird das folgende Beispieldataset verwendet:

| Datei | Tatsächliche Klassen | Vorhergesagte Klassen |
|--|--|--|
| 1 | Action, Comedy | Comedy|
| 2 | action | action |
| 3 | Romanze | Romanze |
| 4 | Romanze, Comedy | Romanze |
| 5 | Comedy | action |

### <a name="class-level-evaluation-for-the-action-class"></a>Auswertung auf Klassenebene für die Klasse *Action* 

| Schlüssel | Anzahl | Erklärung |
|--|--|--|
| Richtig positiv | 1 | Datei 2 wurde richtig als *Action* klassifiziert. |
| Falsch positiv | 1 | Datei 5 wurde fälschlich als *Action* klassifiziert. |
| Falsch negativ | 1 | Datei 1 wurde nicht als *Action* klassifiziert, hätte aber so klassifiziert werden sollen. |

**Genauigkeit** = `#True_Positive / (#True_Positive + #False_Positive) = 1 / (1 + 1) = 0.5`

**Abruf** = `#True_Positive / (#True_Positive + #False_Negatives) = 1 / (1 + 1) = 0.5`

**F1-Bewertung** = `2 * Precision * Recall / (Precision + Recall) =  (2 * 0.5 * 0.5) / (0.5 + 0.5) = 0.5`

### <a name="class-level-evaluation-for-the-comedy-class"></a>Auswertung auf Klassenebene für die Klasse *Comedy* 

| Schlüssel | Anzahl | Erklärung |
|--|--|--|
| Richtig positiv | 1 | Datei 1 wurde richtig als *Comedy* klassifiziert. |
| Falsch positiv | 0 | Es wurden keine Dateien fälschlicherweise als *Comedy* klassifiziert. |
| Falsch negativ | 2 | Die Dateien 5 und 4 wurden nicht als *Comedy* klassifiziert, hätten aber so klassifiziert werden sollen. |

**Genauigkeit** = `#True_Positive / (#True_Positive + #False_Positive) = 1 / (1 + 0) = 1`

**Abruf** = `#True_Positive / (#True_Positive + #False_Negatives) = 1 / (1 + 2) = 0.67`

**F1-Bewertung** = `2 * Precision * Recall / (Precision + Recall) =  (2 * 1 * 0.67) / (1 + 0.67) = 0.8`

### <a name="model-level-evaluation-for-the-collective-model"></a>Auswertung auf Modellebene für das gemeinsame Modell

| Schlüssel | Anzahl | Erklärung |
|--|--|--|
| Richtig positiv | 4 | Den Dateien 1, 2, 3 und 4 wurden bei der Vorhersage die richtigen Klassen zugewiesen. |
| Falsch positiv | 1 | Datei 5 wurde bei der Vorhersage eine falsche Klasse zugewiesen. |
| Falsch negativ | 2 | Die Dateien 1 und 4 wurden bei der Vorhersage nicht allen richtigen Klassen zugewiesen. |

**Genauigkeit** = `#True_Positive / (#True_Positive + #False_Positive) = 4 / (4 + 1) = 0.8`

**Abruf** = `#True_Positive / (#True_Positive + #False_Negatives) = 4 / (4 + 2) = 0.67`

**F1-Bewertung** = `2 * Precision * Recall / (Precision + Recall) =  (2 * 0.8 * 0.67) / (0.8 + 0.67) = 0.12`

> [!NOTE] 
> Bei Klassifizierungsmodellen mit nur einer Bezeichnung ist die Anzahl falsch negativer und falsch positiver Ergebnisse immer gleich. Benutzerdefinierte Klassifizierungsmodelle mit nur einer Bezeichnung prognostizieren immer eine Klasse für jede Datei. Wenn die Vorhersage nicht richtig ist, erhöht sich die FP-Anzahl der vorhergesagten Klasse um eins und die FN-Anzahl der realen Klasse ebenfalls um eins, sodass die Summe von FP und die Summe von FN für das Modell immer gleich ist. Dies ist bei der Klassifizierung mit mehreren Bezeichnungen nicht der Fall, da die verfehlte Vorhersage einer der Klassen einer Datei als falsch negativ gezählt wird. 

## <a name="interpreting-class-level-evaluation-metrics"></a>Interpretieren von Auswertungsmetriken auf Klassenebene

Was bedeutet also eine hohe Genauigkeit oder ein hoher Abruf für eine bestimmte Klasse?

| Recall | Precision | Interpretation |
|--|--|--|
| High | High | Diese Klasse wird vom Modell perfekt verarbeitet. |
| Niedrig | High | Das Modell kann diese Klasse nicht immer vorhersagen, aber wenn sie dies tut, geschieht dies mit hoher Konfidenz. Dies kann daran liegt, dass diese Klasse im Dataset unterrepräsentiert ist. Erwägen Sie daher einen Ausgleich ihrer Datenverteilung.|
| High | Niedrig | Das Modell sagt diese Klasse gut vorher, jedoch mit geringer Konfidenz. Dies kann daran liegt, dass diese Klasse im Dataset überrepräsentiert ist. Erwägen Sie daher einen Ausgleich ihrer Datenverteilung. |
| Niedrig | Niedrig | Diese Klasse wird vom Modell schlecht verarbeitet, das sie normalerweise nicht vorhersagt, und wenn dies doch geschieht, dann mit geringer Konfidenz. |

Bei benutzerdefinierten Klassifizierungsmodellen ist damit zu rechnen, dass sowohl falsch negative als auch falsch positive Ergebnisse auftreten. Sie müssen die Auswirkungen von beiden auf das Gesamtsystem berücksichtigen und Szenarien, in denen das Modell richtige Vorhersagen ignoriert und falsche Vorhersagen als richtig erkennt, sorgfältig durchdenken. Je nach Szenario kann entweder die *Genauigkeit* oder der *Abruf* besser geeignet sein, um die Leistung Ihres Modells zu bewerten.  

Wenn Ihr Szenario beispielsweise die Verarbeitung von Tickets für den technischen Support beinhaltet, kann die Vorhersage der falschen Klasse dazu führen, dass sie an die falsche Abteilung/das falsche Team weitergeleitet werden. In diesem Beispiel sollten Sie erwägen, ihr System empfindlicher gegenüber falsch positiven Werten zu machen, dann wäre die Genauigkeit die relevantere Metrik für die Auswertung. 

Wenn in einem anderen Beispiel Ihr Szenario das Kategorisieren von E-Mails als „*wichtig*“ oder „*spam*“ umfasst, könnte eine falsche Vorhersage dazu führen, dass Ihnen eine nützliche E-Mail entgeht, wenn sie als „*spam*“ gekennzeichnet wird. Wenn eine Spam-E-Mail jedoch als *wichtig* bezeichnet wird, können Sie sie einfach ignorieren. In diesem Beispiel sollten Sie erwägen, ihr System empfindlicher gegenüber falsch negativen Werten zu machen, dann wäre der Abruf die relevantere Metrik für die Auswertung. 

Wenn Sie für allgemeine Szenarien optimieren möchten oder wenn Genauigkeit und Abruf beide wichtig sind, können Sie die F1-Bewertung verwenden. Auswertungsbewertungen sind subjektiv, ausgehend von Ihrem Szenario und Ihren Akzeptanzkriterien. Es gibt keine absolute Metrik, die für jedes Szenario funktioniert. 

## <a name="see-also"></a>Siehe auch

* [Anzeigen der Modellauswertung](../how-to/view-model-evaluation.md)
* [Trainieren eines Modells](../how-to/train-model.md)
