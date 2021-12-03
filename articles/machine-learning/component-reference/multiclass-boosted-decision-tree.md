---
title: 'Multiclass Boosted Decision Tree: Komponenten-Referenz'
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie die Multiclass Boosted Decision Tree-Komponente in Azure Machine Learning verwenden, um einen Klassifikator mit markierten Daten zu erstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: 2c354e5a77adcb9910bf7f277950868ee96e1104
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566676"
---
# <a name="multiclass-boosted-decision-tree"></a>Verstärkte Entscheidungsstruktur mit mehreren Klassen

Dieser Artikel beschreibt eine Komponente im Azure Machine Learning Designer.

Verwenden Sie diese Komponente, um ein Modell für maschinelles Lernen zu erstellen, das auf dem Algorithmus der verstärkten Entscheidungsbäume basiert.

Ein Boosted Decision Tree ist eine Ensemble-basierte Lernmethode. Die zweite Struktur korrigiert dabei die Fehler der ersten Struktur, die dritte Struktur korrigiert die Fehler der ersten und zweiten Struktur und so weiter. Vorhersagen basieren auf der Zusammenstellung der Strukturen.

## <a name="how-to-configure"></a>Vorgehensweise zur Konfiguration 

Diese Komponente erstellt ein untrainiertes Klassifikationsmodell. Da es sich bei der Klassifizierung um eine beaufsichtigte Lernmethode handelt, benötigen Sie ein *mit Bezeichnungen versehenes Dataset*, das eine Bezeichnungsspalte mit einem Wert für alle Zeilen enthält.

Diese Art von Modell kann mithilfe von [Modell trainieren](././train-model.md) trainiert werden. 

1.  Fügen Sie die Komponente **Multiclass Boosted Decision Tree** zu Ihrer Pipeline hinzu.

1.  Geben Sie mit der Option **Create trainer mode** (Trainermodus erstellen) an, wie das Modell trainiert werden soll.

    + **Single Parameter** (Einzelner Parameter): Wenn Sie wissen, wie Sie das Modell konfigurieren möchten, können Sie einen bestimmten Satz von Werten als Argumente angeben.
    
    + **Parameter Range** (Parameterbereich): Wählen Sie diese Option, wenn Sie nicht sicher sind, welche Parameter am besten geeignet sind, und einen Parametersweep ausführen möchten. Wählen Sie einen Wertebereich aus, über den iteriert werden soll. Anschließend iteriert das Modul [Tune Model Hyperparameters](tune-model-hyperparameters.md) über alle möglichen Kombinationen der von Ihnen angegebenen Einstellungen, um die Hyperparameter zur Erzielung der optimalen Ergebnisse zu bestimmen.  

1. **Maximum number of leaves per tree** (Maximale Anzahl von Blättern pro Struktur) begrenzt die maximale Anzahl von Endknoten (Blättern), die in einer Struktur erstellt werden können.
    
    Eine Erhöhung dieses Werts führt zu einer potenziell größeren Struktur und zu einer höheren Genauigkeit, kann aber auch eine Überanpassung und eine längere Trainingsdauer zur Folge haben.
  
1. **Minimum number of samples per leaf node** (Minimale Anzahl von Stichproben pro Blattknoten) gibt die Anzahl von Fällen an, die zum Erstellen eines Endknotens (Blatt) in einer Struktur erforderlich sind.  

    Wenn Sie diesen Wert heraufsetzen, erhöht sich der Schwellenwert für die Erstellung neuer Regeln. Bei Verwendung des Standardwerts „1“ reicht für die Erstellung einer neuen Regel beispielsweise bereits ein einzelner Fall aus. Wenn Sie den Wert auf „5“ erhöhen, müssen die Trainingsdaten mindestens fünf Fälle enthalten, die die gleichen Bedingungen erfüllen.

1. **Lerngeschwindigkeit** definiert die Schrittgröße beim Lernen. Geben Sie eine Zahl zwischen 0 und 1 ein.

    Die Lernrate bestimmt, wie schnell bzw. langsam sich das Lernmodell einer optimalen Lösung annähert. Ist die Schrittgröße zu groß, wird die optimale Lösung unter Umständen verfehlt. Ist die Schrittgröße zu klein, dauert die Annäherung an die beste Lösung länger.

1. Geben Sie unter **Number of trees constructed** (Anzahl erstellter Strukturen) die Gesamtanzahl von Entscheidungsstrukturen an, die im Ensemble erstellt werden sollen. Mit einer höheren Anzahl von Entscheidungsbäumen erzielen Sie unter Umständen eine bessere Abdeckung, allerdings verlängert sich dadurch die Trainingsdauer.

1. Geben Sie unter **Random number seed** (Zufälliger Ausgangswert) eine nicht negative ganze Zahl ein, die als zufälliger Ausgangswert verwendet werden soll. Die Angabe eines Startwerts gewährleistet Reproduzierbarkeit in verschiedenen Ausführungen, die auf den gleichen Daten und Parametern basieren.  

    Der zufällige Ausgangswert ist standardmäßig auf 42 festgelegt. Nachfolgende Ausführungen mit anderen zufälligen Ausgangswerten können abweichende Ergebnisse aufweisen.

1. Trainieren des Modells:

    + Wenn Sie **Trainermodus** auf **Einzelparameter** setzen, verbinden Sie einen getaggten Datensatz und die Komponente [Trainmodell](train-model.md).  
  
    + Wenn Sie **Create trainer mode** (Trainermodus erstellen) auf **Parameter Range** (Parameterbereich) festlegen, verbinden Sie ein mit Tags versehenes Dataset, und trainieren Sie das Modell mithilfe von [Tune Model Hyperparameters](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Wenn Sie einen Parameterbereich an [Train Model](train-model.md) übergeben, wird nur der Standardwert in der Liste der Einzelparameter verwendet.  
    > 
    > Wenn Sie einen einzelnen Satz von Parameterwerten an die Komponente [Tune Model Hyperparameters](tune-model-hyperparameters.md) übergeben, ignoriert sie die Werte, wenn sie einen Bereich von Einstellungen für jeden Parameter erwartet, und verwendet die Standardwerte für den Lerner.  
    > 
    > Wenn Sie die Option **Parameter Range** (Parameterbereich) auswählen und einen einzelnen Wert für einen beliebigen Parameter eingeben, wird dieser angegebene einzelne Wert während des gesamten Löschvorgangs verwendet, auch wenn andere Parameter in einem Wertebereich geändert werden.

## <a name="next-steps"></a>Nächste Schritte

Siehe die [Sammlung der für Azure Machine Learning verfügbaren Komponenten](component-reference.md). 
