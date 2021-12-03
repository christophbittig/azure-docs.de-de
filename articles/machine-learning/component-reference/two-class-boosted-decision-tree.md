---
title: 'Verstärkte Entscheidungsstruktur mit zwei Klassen: Komponentenreferenz'
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie die Komponente der verstärkten Entscheidungsstruktur mit zwei Klassen im Designer verwenden, um einen binären Klassifizierer zu erstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/24/2020
ms.openlocfilehash: 339b09fb516520129dabb7ea8bec6e8e5727c63c
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566526"
---
# <a name="two-class-boosted-decision-tree-component"></a>Komponente der verstärkten Entscheidungsstruktur mit zwei Klassen

In diesem Artikel wird eine Komponente im Azure Machine Learning-Designer beschrieben.

Verwenden Sie diese Komponente, um ein Machine Learning-Modell zu erstellen, das auf dem Algorithmus für verstärkte Entscheidungsstrukturen (Boosted Decision Tree) basiert. 

Ein Boosted Decision Tree ist eine Ensemble-basierte Lernmethode. Die zweite Struktur korrigiert dabei die Fehler der ersten Struktur, die dritte Struktur korrigiert die Fehler der ersten und zweiten Struktur und so weiter. Vorhersagen basieren auf dem Strukturensemble, das die Vorhersage generiert.
  
Mit ordnungsgemäß konfigurierten Boosted Decision Trees lässt sich für gewöhnlich am einfachsten eine optimale Leistung bei einem breiten Spektrum von Machine Learning-Aufgaben erzielen. Sie zählen allerdings auch zu den arbeitsspeicherintensiveren Lernmodellen, und bei der aktuellen Implementierung wird alles im Arbeitsspeicher gespeichert. Aus diesem Grund ist ein Boosted Decision Tree-Modell möglicherweise nicht in der Lage, die umfangreichen Datasets zu verarbeitet, die von einigen linearen Lernmodulen verarbeitet werden können.

Diese Komponente basiert auf dem LightGBM-Algorithmus.

## <a name="how-to-configure"></a>Vorgehensweise zur Konfiguration

Diese Komponente erstellt ein untrainiertes Klassifizierungsmodell. Da es sich bei der Klassifizierung um eine beaufsichtigte Lernmethode handelt, benötigen Sie zum Trainieren des Modells ein *mit Tags versehenes Dataset*, das eine Bezeichnungsspalte mit einem Wert für alle Zeilen enthält.

Diese Art von Modell kann mithilfe von [Train Model](././train-model.md) (Modell trainieren) trainiert werden. 

1.  Fügen Sie Ihrer Pipeline in Azure Machine Learning die Komponente **Boosted Decision Tree** (Verstärkte Entscheidungsstruktur) hinzu.
  
2.  Geben Sie an, wie das Modell trainiert werden soll, indem Sie die Option **Create trainer mode** (Trainermodus erstellen) aktivieren.
  
    + **Single Parameter** (Einzelner Parameter): Wenn Sie wissen, wie Sie das Modell konfigurieren möchten, können Sie einen bestimmten Satz von Werten als Argumente angeben.
  
    + **Parameter Range** (Parameterbereich): Wenn Sie sich hinsichtlich der besten Parameter nicht sicher sind, können Sie optimale Parameter mithilfe der Komponente [Tune Model Hyperparameters](tune-model-hyperparameters.md) (Modellhyperparameter optimieren) finden. Sie geben einen Wertebereich an, woraufhin das Training über mehrere Einstellungskombinationen iteriert, um die Wertekombination zu bestimmen, die das beste Ergebnis liefert.
  
3.  Geben Sie unter **Maximum number of leaves per tree** (Maximale Anzahl von Blättern pro Struktur) die maximale Anzahl von Endknoten (Blätter) an, die in einer Struktur erstellt werden können.
  
     Eine Erhöhung dieses Werts führt zu einem potenziell größeren Baum und zu einer höheren Genauigkeit, kann aber auch eine Überanpassung und eine längere Trainingsdauer zur Folge haben.
  
4.  Geben Sie unter **Minimum number of samples per leaf node** (Minimale Anzahl von Stichproben pro Blattknoten) die Anzahl von Fällen an, die zum Erstellen eines Endknotens (Blatt) in einer Struktur erforderlich sind.  
  
     Wenn Sie diesen Wert heraufsetzen, erhöht sich der Schwellenwert für die Erstellung neuer Regeln. Bei Verwendung des Standardwerts „1“ reicht für die Erstellung einer neuen Regel beispielsweise bereits ein einzelner Fall aus. Wenn Sie den Wert auf „5“ erhöhen, müssen die Trainingsdaten mindestens fünf Fälle enthalten, die die gleichen Bedingungen erfüllen.
  
5.  Geben Sie unter **Learning rate** (Lernrate) eine Zahl zwischen 0 und 1 ein, um die Schrittgröße beim Lernen zu definieren.  
  
     Die Lernrate bestimmt, wie schnell bzw. langsam sich das Lernmodell der optimalen Lösung annähert. Ist die Schrittgröße zu groß, wird die optimale Lösung unter Umständen verfehlt. Ist die Schrittgröße zu klein, dauert die Annäherung an die beste Lösung länger.
  
6.  Geben Sie unter **Number of trees constructed** (Anzahl erstellter Strukturen) die Gesamtzahl von Entscheidungsstrukturen an, die im Ensemble erstellt werden sollen. Mit einer höheren Anzahl von Entscheidungsbäumen erzielen Sie unter Umständen eine bessere Abdeckung, allerdings verlängert sich dadurch die Trainingsdauer.
  
     Wenn Sie den Wert auf 1 festlegen, wird nur eine einzelne Struktur (die Struktur mit dem anfänglichen Parametersatz) generiert, und es finden keine weiteren Iterationen statt.
  
7.  Geben Sie unter **Random number seed** (Zufällig gewählter Startwert) eine nicht negative ganze Zahl ein, die als zufälliger Startwert verwendet werden soll. Die Angabe eines Startwerts gewährleistet Reproduzierbarkeit in verschiedenen Ausführungen, die auf den gleichen Daten und Parametern basieren.  
  
     Der zufällig gewählte Startwert ist standardmäßig auf „0“ festgelegt, was bedeutet, dass der ursprüngliche Startwert von der Systemuhr abgerufen wird.  Bei nachfolgenden Ausführungen wird ein zufällig gewählter Startwert verwendet, was zu abweichenden Ergebnissen führen kann.
  

9. Trainieren des Modells:

    + Wenn Sie **Create trainer mode** (Trainermodus erstellen) auf **Single Parameter** (Einzelner Parameter) festlegen, müssen Sie ein mit Tags versehenes Dataset und die Komponente [Train Model](train-model.md) (Modell trainieren) verbinden.  
  
    + Wenn Sie **Create trainer mode** (Trainermodus erstellen) auf **Parameter Range** (Parameterbereich) festlegen, verbinden Sie ein mit Tags versehenes Dataset, und trainieren Sie das Modell mithilfe von [Tune Model Hyperparameters](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Wenn Sie einen Parameterbereich an [Train Model](train-model.md) übergeben, wird nur der Standardwert in der Liste der Einzelparameter verwendet.  
    > 
    > Wenn Sie eine einzelne Reihe bestimmter Parameterwerte an die Komponente [Tune Model Hyperparameters](tune-model-hyperparameters.md) (Modellhyperparameter optimieren) übergeben und ein Bereich von Einstellungen für jeden Parameter erwartet wird, werden die Werte ignoriert und stattdessen die Standardwerte für den Learner verwendet.  
    > 
    > Wenn Sie die Option **Parameter Range** (Parameterbereich) auswählen und einen einzelnen Wert für einen beliebigen Parameter eingeben, wird dieser angegebene einzelne Wert während des gesamten Löschvorgangs verwendet, auch wenn andere Parameter in einem Wertebereich geändert werden.  
   
## <a name="results"></a>Ergebnisse

Nach Abschluss des Trainings:

+ Um eine Momentaufnahme des trainierten Modells zu speichern, wählen Sie die Registerkarte **Ausgaben** im rechten Bereich der Komponente **Train model** (Modell trainieren) aus. Wählen Sie das Symbol **Register dataset** (Dataset registrieren) aus, um das Modell als wiederverwendbare Komponente zu speichern.

+ Um das Modell für die Bewertung zu verwenden, fügen Sie einer Pipeline die Komponente **Score Model** (Modell bewerten) hinzu.

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die [verfügbaren Komponenten](component-reference.md) für Azure Machine Learning an. 