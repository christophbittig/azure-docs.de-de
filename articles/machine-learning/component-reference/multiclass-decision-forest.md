---
title: 'Gesamtstruktur-Entscheidungswald: Komponenten-Referenz'
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie die Gesamtstruktur Decision Forest Komponente in Azure Machine Learning verwenden, um ein maschinelles Lernmodell zu erstellen, das auf dem *Entscheidungswald* Algorithmus basiert.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: c55c7e168e29eb2b57154cee361db8cd56f5a96b
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566629"
---
# <a name="multiclass-decision-forest-component"></a>Gesamtstruktur-Entscheidungswald-Komponente

Dieser Artikel beschreibt eine Komponente im Azure Machine Learning Designer.

Verwenden Sie diese Komponente, um ein maschinelles Lernmodell zu erstellen, das auf dem *Entscheidungswald*-Algorithmus basiert. Ein Entscheidungswald ist ein Ensemblemodell, das in kurzer Zeit eine Reihe von Entscheidungsbäumen erstellt und dabei aus mit Tags versehenen Daten lernt.

## <a name="more-about-decision-forests"></a>Weitere Informationen zu Entscheidungswäldern

Der Entscheidungswald-Algorithmus ist eine Ensemble-Lernmethode für die Klassifizierung. Der Algorithmus funktioniert dergestalt, dass mehrere Entscheidungsbäume erstellt werden und dann für die beliebteste Ausgabeklasse *abstimmt* wird. Die Abstimmung ist eine Form der Aggregation, bei der jeder Baum in einem Entscheidungswald zur Klassifizierung ein nicht normalisiertes Histogramm der Häufigkeit von Bezeichnungen ausgibt. Der Aggregationsprozess summiert diese Histogramme und normalisiert das Ergebnis, um die „Wahrscheinlichkeiten“ für jede Bezeichnung abzurufen. Die Bäume mit hoher Vorhersagesicherheit haben bei der endgültigen Entscheidung des Ensembles eine größere Gewichtung.

Entscheidungsbäume sind im Allgemeinen nichtparametrische Modelle, die demnach Daten mit unterschiedlichen Verteilungen unterstützen. In jedem Baum erfolgt für jede Klasse eine Abfolge einfacher Tests, wobei die Ebenen einer Baumstruktur erhöht werden, bis ein Blattknoten (die Entscheidung) erreicht ist.

Entscheidungsbäume haben zahlreiche Vorteile:

+ Sie können nicht lineare Entscheidungsgrenzen darstellen.
+ Sie sind während des Trainings und der Vorhersage effizient bei der Berechnung und der Arbeitsspeicherauslastung.
+ Sie führen eine integrierte Featureauswahl und -klassifizierung durch.
+ Sie sind resilient gegen störende Features.

Der Entscheidungswaldklassifizierer in Azure Machine Learning besteht aus einem Ensemble von Entscheidungsbäumen. Im Allgemeinen bieten Ensemblemodelle eine bessere Abdeckung und Genauigkeit als einzelne Entscheidungsbäume. Weitere Informationen finden Sie unter [Entscheidungsbäume](https://go.microsoft.com/fwlink/?LinkId=403677).

## <a name="how-to-configure-multiclass-decision-forest"></a>Konfigurieren des Moduls „Multiclass Decision Forest“

1. Fügen Sie die Komponente **Gesamtstruktur Entscheidungswald** zu Ihrer Pipeline im Designer hinzu. Sie finden diese Komponente unter **Maschinelles Lernen**, **Modell initialisieren**, und **Klassifizierung**.

2. Doppelklicken Sie auf die Komponente, um das Fenster **Eigenschaften** zu öffnen.

3. Wählen Sie für **Resampling method** (Methode für Wiederholungsprobennahme) die Methode aus, mit der die einzelnen Bäume erstellt wurden.  Sie können zwischen Bagging oder Replikation wählen.

    + **Bagging**: Bagging ist die Abkürzung von *Bootstrap aggregating*. Bei dieser Methode basiert jeder Baum auf einer neuen Stichprobe, die mithilfe von Zufallsstichproben des ursprünglichen Datasets mit Austausch erstellt wird, bis Sie ein Dataset mit der Größe des Originals erhalten. Die Ausgaben der Modelle werden durch *Abstimmung* kombiniert, was eine Form von Aggregation ist. Weitere Informationen finden Sie im Wikipedia-Eintrag zu „Bootstrap aggregating“.

    + **Replikation**: Bei der Replikation wird jede Struktur mit genau denselben Eingabedaten trainiert. Die Bestimmung, welches Aufteilungsprädikat für jeden Baumknoten verwendet wird, bleibt zufällig, wodurch verschiedene Bäume entstehen.

   

4. Geben Sie an, wie das Modell trainiert werden soll, indem Sie die Option **Create trainer mode** (Trainermodus erstellen) aktivieren.

    + **Single Parameter** (Einzelner Parameter): Wählen Sie diese Option, wenn Sie wissen, wie Sie das Modell konfigurieren möchten, und geben Sie eine bestimmte Menge von Werten als Argumente an.

    + **Parameter Range** (Parameterbereich): Wählen Sie diese Option, wenn Sie nicht sicher sind, welche Parameter am besten geeignet sind, und einen Parametersweep ausführen möchten. Wählen Sie einen Wertebereich aus, über den iteriert werden soll. Anschließend iteriert das Modul [Tune Model Hyperparameters](tune-model-hyperparameters.md) über alle möglichen Kombinationen der von Ihnen angegebenen Einstellungen, um die Hyperparameter zur Erzielung der optimalen Ergebnisse zu bestimmen.   

5. **Number of decision trees** (Anzahl von Entscheidungsbäumen): Geben Sie die maximale Anzahl von Entscheidungsbäumen ein, die im Ensemble erstellt werden können. Mit einer höheren Anzahl von Entscheidungsbäumen erzielen Sie möglicherweise eine bessere Abdeckung, allerdings verlängert sich dadurch ggf. die Trainingsdauer.

    Wenn Sie den Wert auf 1 festlegen, kann allerdings nur eine einzelne Struktur (die Struktur mit dem anfänglichen Parametersatz) generiert werden, und es finden keine weiteren Iterationen statt.

6. **Maximum depth of the decision trees** (Maximale Tiefe der Entscheidungsbäume): Geben Sie einen Wert ein, um die maximale Tiefe der Entscheidungsbäume zu begrenzen. Eine größere Tiefe des Baums kann die Genauigkeit erhöhen, wobei das Risiko einer Überanpassung und einer längeren Trainingsdauer besteht.

7. **Number of random splits per node** (Anzahl zufälliger Aufteilungen pro Knoten): Geben Sie die Anzahl der Aufteilungen ein, die beim Erstellen der einzelnen Knoten des Baums verwendet werden sollen. Eine *Aufteilung* bedeutet, dass Features auf jeder Ebene des Baums (Knotens) zufällig aufgeteilt werden.

8. **Minimum number of samples per leaf node** (Minimale Anzahl der Stichproben pro Blattknoten): Geben Sie unter „Minimum number of samples per leaf node“ die Anzahl von Fällen an, die zum Erstellen eines Endknotens (Blatts) in einem Baum erforderlich sind. Wenn Sie diesen Wert heraufsetzen, erhöht sich der Schwellenwert für die Erstellung neuer Regeln.

    Bei Verwendung des Standardwerts „1“ reicht für die Erstellung einer neuen Regel beispielsweise bereits ein einzelner Fall aus. Wenn Sie den Wert auf „5“ erhöhen, müssen die Trainingsdaten mindestens fünf Fälle enthalten, die die gleichen Bedingungen erfüllen.



10. Verbinden Sie ein bezeichnetes Dataset, und trainieren Sie das Modell:

    + Wenn Sie **Trainermodus** auf **Einzelparameter** setzen, verbinden Sie einen getaggten Datensatz und die Komponente [Trainmodell](train-model.md).  
  
    + Wenn Sie **Create trainer mode** (Trainermodus erstellen) auf **Parameter Range** (Parameterbereich) festlegen, verbinden Sie ein mit Tags versehenes Dataset, und trainieren Sie das Modell mithilfe von [Tune Model Hyperparameters](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Wenn Sie einen Parameterbereich an [Train Model](train-model.md) übergeben, wird nur der Standardwert in der Liste der Einzelparameter verwendet.  
    > 
    > Wenn Sie einen einzelnen Satz von Parameterwerten an die Komponente [Tune Model Hyperparameters](tune-model-hyperparameters.md) übergeben, ignoriert sie die Werte, wenn sie einen Bereich von Einstellungen für jeden Parameter erwartet, und verwendet die Standardwerte für den Lerner.  
    > 
    > Wenn Sie die Option **Parameter Range** (Parameterbereich) auswählen und einen einzelnen Wert für einen beliebigen Parameter eingeben, wird dieser angegebene einzelne Wert während des gesamten Löschvorgangs verwendet, auch wenn andere Parameter in einem Wertebereich geändert werden.

11. Übermitteln Sie die Pipeline.



## <a name="next-steps"></a>Nächste Schritte

Siehe die [Sammlung der für Azure Machine Learning verfügbaren Komponenten](component-reference.md). 