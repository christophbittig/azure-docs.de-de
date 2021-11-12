---
title: 'Cross Validate Model: Komponenten-Referenz'
titleSuffix: Azure Machine Learning
description: Verwenden Sie die Komponente Cross-Validate Model im Azure Machine Learning Designer, um Parameterschätzungen für Klassifizierungs- oder Regressionsmodelle kreuzweise zu validieren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: cf8979178731bce20902f74ac8b8b1a694024837
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566685"
---
# <a name="cross-validate-model"></a>Durchführen einer Kreuzvalidierung für ein Modell

Dieser Artikel beschreibt, wie Sie die Komponente Cross Validate Model im Azure Machine Learning Designer verwenden. Die *Kreuzvalidierung* ist ein Verfahren, das häufig beim maschinellen Lernen verwendet wird, um gleichzeitig die Variabilität eines Datasets und die Zuverlässigkeit eines mit diesen Daten trainierten Modells zu bewerten.  

Die Komponente Cross Validate Model (Kreuzvalidierung des Modells) nimmt als Eingabe einen markierten Datensatz zusammen mit einem untrainierten Klassifizierungs- oder Regressionsmodell. Das Dataset wird in eine bestimmte Anzahl von Teilmengen (*Folds*) aufgeteilt, für jeden Fold wird ein Modell erstellt, und anschließend wird ein Satz von Genauigkeitsstatistiken für jeden Fold zurückgegeben. Indem Sie die Genauigkeitsstatistiken für alle Folds vergleichen, können Sie die Qualität des Datasets interpretieren. Sie können dann ermitteln, ob das Modell anfällig für Datenabweichungen ist.  

Beim Modul „Cross Validate Model“ werden auch vorhergesagte Ergebnisse und Wahrscheinlichkeiten für das Dataset zurückgegeben, damit Sie die Zuverlässigkeit der Vorhersagen beurteilen können.  

### <a name="how-cross-validation-works"></a>Funktionsweise der Kreuzvalidierung

1. Bei der Kreuzvalidierung werden Trainingsdaten nach dem Zufallsprinzip in Folds unterteilt. 

   Der Algorithmus ist standardmäßig auf 10 Folds eingestellt, wenn Sie das Dataset zuvor nicht partitioniert haben. Um den Datensatz in eine andere Anzahl von Faltungen aufzuteilen, können Sie die Komponenten [Partition und Sample](partition-and-sample.md) verwenden und angeben, wie viele Faltungen verwendet werden sollen.  

2.  Die Komponente stellt die Daten in Falte 1 für die Validierung zur Seite. (Dies wird manchmal als *Holdout-Faltung* bezeichnet.) Die Komponente verwendet die verbleibenden Faltungen, um ein Modell zu trainieren. 

    Wenn Sie z. B. fünf Faltungen erstellen, erzeugt die Komponente während der Kreuzvalidierung fünf Modelle. Die Komponente trainiert jedes Modell anhand von vier Fünfteln der Daten. Jedes Modell wird mit dem verbleibenden Fünftel getestet.  

3.  Während der Prüfung des Modells für jede Falte wertet die Komponente mehrere Genauigkeitsstatistiken aus. Welche Statistiken die Komponente verwendet, hängt von der Art des Modells ab, das Sie auswerten wollen. Zum Auswerten von Klassifizierungs- und Regressionsmodellen werden unterschiedliche Statistiken verwendet.  

4.  Wenn der Erstellungs- und Evaluierungsvorgang für alle Folds abgeschlossen ist, generiert „Cross Validate Model“ einen Satz mit Leistungsmetriken und bewerteten Ergebnissen für alle Daten. Überprüfen Sie diese Metriken, um festzustellen, ob ein einzelner Fold eine hohe oder niedrige Genauigkeit aufweist. 

### <a name="advantages-of-cross-validation"></a>Vorteile der Kreuzvalidierung

Eine andere gängige Methode zum Auswerten eines Modells besteht darin, die Daten mithilfe von [Split Data](split-data.md) in einen Trainings- und einen Testsatz zu unterteilen und das Modell dann mit den Trainingsdaten zu überprüfen. Die Kreuzvalidierung bietet aber einige Vorteile:  

-   Bei der Kreuzvalidierung werden mehr Testdaten verwendet.

    Bei der Kreuzvalidierung wird die Leistung des Modells mit den angegebenen Parametern in einem größeren Datenraum gemessen. Das heißt, dass bei der Kreuzvalidierung das gesamte Trainingsdataset für Training und Auswertung verwendet wird (und nicht nur ein Teil). Wenn Sie ein Modell dagegen mithilfe von Daten validieren, die aus einer zufälligen Unterteilung generiert werden, beruht die Auswertung des Modells in der Regel nur auf höchstens 30 Prozent der verfügbaren Daten.  

    Da das Modell bei der Kreuzvalidierung aber mehrere Male mit einem größeren Dataset trainiert wird, ist der Computeaufwand deutlich höher. Dieser Vorgang dauert wesentlich länger als die Validierung bei einer zufälligen Aufteilung.  

-   Bei der Kreuzvalidierung werden sowohl das Dataset als auch das Modell ausgewertet.

    Die Kreuzvalidierung umfasst nicht nur die Messung der Genauigkeit eines Modells. Sie erhalten auch einen Eindruck, wie repräsentativ das Dataset ist und wie sensibel das Modell ggf. auf Datenabweichungen reagiert.  

## <a name="how-to-use-cross-validate-model"></a>Verwenden von Cross Validate Model

Die Kreuzvalidierung kann viel Zeit in Anspruch nehmen, wenn Ihr Dataset groß ist.  Es kann also ratsam sein, das Modul „Cross Validate Model“ in der anfänglichen Erstellungs- und Testphase Ihres Modells zu verwenden. In dieser Phase können Sie die Eignung der Modellparameter auswerten (vorausgesetzt, die Computedauer ist akzeptabel). Mit den Komponenten [Modell trainieren](train-model.md) und [Modell auswerten](evaluate-model.md) können Sie dann Ihr Modell unter Verwendung der festgelegten Parameter trainieren und auswerten.

In diesem Szenario verwenden Sie „Cross Validate Model“, um das Modell zu trainieren und zu testen.

1. Fügen Sie die Komponente Cross Validate Model zu Ihrer Pipeline hinzu. Sie finden es im Azure Machine Learning-Designer in der Kategorie **Model Scoring & Evaluation** (Modellbewertung und -auswertung). 

2. Verbinden Sie die Ausgabe eines beliebigen Klassifizierungs- oder Regressionsmodells. 

    Wenn Sie beispielsweise **Two Class Boosted Decision Tree** für die Klassifizierung verwenden, sollten Sie das Modell mit den gewünschten Parametern konfigurieren. Ziehen Sie anschließend einen Connector vom Port **Untrainiertes Modell** zum Klassifizierer des passenden Ports von „Cross Validate Model“. 

    > [!TIP] 
    > Sie müssen das Modell nicht trainieren, weil dies von „Cross Validate Model“ im Rahmen der Auswertung automatisch durchgeführt wird.  
3.  Verbinden Sie den Port **Dataset** von „Cross Validate Model“ mit einem gekennzeichneten Trainingsdataset.  

4.  Klicken Sie im rechten Bereich des Kreuzvalidierungsmodells auf **Spalte bearbeiten**. Wählen Sie die einzelne Spalte mit der Klassenbezeichnung oder dem vorhersagbaren Wert aus. 

5. Legen Sie einen Wert für den Parameter **Zufälliger Ausgangswert** fest, wenn Sie die Ergebnisse der Kreuzvalidierung über aufeinanderfolgende Ausführungen für dieselben Daten hinweg wiederholen möchten.  

6. Übermitteln Sie die Pipeline.

7. Eine Beschreibung der Berichte finden Sie im Abschnitt [Ergebnisse](#results).

## <a name="results"></a>Ergebnisse

Nach Abschluss aller Iterationen werden von „Cross Validate Model“ Bewertungen für das gesamte Dataset erstellt. Außerdem werden Leistungsmetriken erstellt, die Sie zum Bewerten der Qualität des Modells verwenden können.

### <a name="scored-results"></a>Bewertete Ergebnisse

Die erste Ausgabe der Komponente liefert die Quelldaten für jede Zeile, zusammen mit einigen vorhergesagten Werten und den dazugehörigen Wahrscheinlichkeiten. 

Um die Ergebnisse anzuzeigen, klicken Sie in der Pipeline mit der rechten Maustaste auf die Komponente Cross Validate Model. Wählen Sie **Visualize Scored results** (Bewertete Ergebnisse visualisieren) aus.

| Neuer Spaltenname      | BESCHREIBUNG                              |
| -------------------- | ---------------------------------------- |
| Scored Labels (Bewertete Bezeichnungen)        | Diese Spalte wird am Ende des Datasets hinzugefügt. Sie enthält den vorhergesagten Wert für jede Zeile. |
| Scored Probabilities (Bewertete Wahrscheinlichkeiten) | Diese Spalte wird am Ende des Datasets hinzugefügt. Sie gibt die geschätzte Wahrscheinlichkeit des Werts in **Scored Labels** (Bewertete Bezeichnungen) an. |
| Fold Number (Foldnummer)          | Gibt den bei Null beginnenden Index des Folds an, dem die jeweiligen Datenzeilen bei der Kreuzvalidierung zugewiesen wurden. |

 ### <a name="evaluation-results"></a>Auswertung der Ergebnisse

Der zweite Bericht wird nach Folds gruppiert. Denken Sie daran, dass „Cross Validate Model“ die Trainingsdaten bei der Ausführung nach dem Zufallsprinzip in *n* Folds unterteilt (standardmäßig 10). Bei jeder Iteration des Datasets wird von „Cross Validate Model“ ein Fold als Validierungsdataset genutzt. Die verbleibenden *n-1* Folds werden zum Trainieren eines Modells verwendet. Jedes der *n* Modelle wird mit den Daten in allen anderen Folds getestet.

In diesem Bericht werden die Folds nach Indexwert in aufsteigender Reihenfolge aufgelistet.  Wenn Sie nach einer anderen Spalte sortieren möchten, können Sie die Ergebnisse als Dataset speichern.

Um die Ergebnisse anzuzeigen, klicken Sie in der Pipeline mit der rechten Maustaste auf die Komponente Cross Validate Model. Wählen Sie **Visualize Evaluation results by fold** (Auswertungsergebnisse nach Fold anzeigen) aus.


|Spaltenname| BESCHREIBUNG|
|----|----|
|Fold number (Foldnummer)| Ein Bezeichner für jeden Fold. Wenn Sie fünf Folds erstellt haben, sind fünf Teilmengen mit Daten vorhanden (nummeriert von 0 bis 4).
|Number of examples in fold (Anzahl von Beispielen im Fold)|Die Anzahl der jedem Fold zugewiesenen Zeilen. Diese sollten ungefähr gleich sein. |


Die Komponente enthält auch die folgenden Metriken für jede Falte, abhängig von der Art des Modells, das Sie auswerten: 

+ **Klassifizierungsmodelle:** Präzision, Rückruf, F-Score, AUC, Genauigkeit  

+ **Regressionsmodelle:** Mittlerer absoluter Fehler, effektiver absoluter Fehler, relativer absoluter Fehler, relativer quadratischer Fehler und Bestimmungskoeffizient


## <a name="technical-notes"></a>Technische Hinweise  

+ Es empfiehlt sich, Datasets vor der Verwendung für die Kreuzvalidierung zu normalisieren. 

+ Für „Cross Validate Model“ fällt ein deutlich höherer Computeaufwand an, und der Vorgang dauert länger, als wenn Sie das Modell mit einem Dataset mit zufälliger Unterteilung validieren. Der Grund ist, dass das Modell mit „Cross Validate Model“ mehrfach trainiert und validiert wird.

+ Das Dataset muss nicht in Trainings- und Testsätze aufgeteilt werden, wenn Sie die Genauigkeit des Modells mithilfe der Kreuzvalidierung messen. 


## <a name="next-steps"></a>Nächste Schritte

Siehe die [Sammlung der für Azure Machine Learning verfügbaren Komponenten](component-reference.md). 
