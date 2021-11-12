---
title: 'Multiclass Logistic Regression: Komponenten-Referenz'
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie die Komponente Multiclass Logistic Regression im Azure Machine Learning Designer verwenden, um mehrere Werte vorherzusagen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: a5c823b2fc2464907ef224bc3a34d5150dfe3ba2
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566578"
---
# <a name="multiclass-logistic-regression-component"></a>Komponente Multiclass Logistic Regression

Dieser Artikel beschreibt eine Komponente im Azure Machine Learning Designer.

Verwenden Sie diese Komponente, um ein logistisches Regressionsmodell zu erstellen, das zur Vorhersage mehrerer Werte verwendet werden kann.

Die Klassifizierung mit logistischer Regression entspricht einer überwachten Lernmethode, die folglich ein bezeichnetes Dataset erfordert. Sie trainieren das Modell, indem Sie das Modell und den beschrifteten Datensatz als Eingabe für eine Komponente wie [Train Model](./train-model.md) bereitstellen. Das trainierte Modell kann anschließend verwendet werden, um Werte für neue Eingabenbeispiele vorherzusagen.

Azure Machine Learning bietet auch eine [Two-Class Logistic Regression](./two-class-logistic-regression.md) Komponente, die für die Klassifizierung von binären oder dichotomen Variablen geeignet ist.

## <a name="about-multiclass-logistic-regression"></a>Informationen zur logistischen Regression mit mehreren Klassen

Die logistische Regression ist eine bekannte Statistikmethode, die zur Vorhersage der Wahrscheinlichkeit eines Ergebnisses verwendet wird und für Klassifizierungsaufgaben geeignet ist. Der Algorithmus prognostiziert die Wahrscheinlichkeit des Vorkommens eines Ereignisses, indem er Daten an eine logistische Funktion anpasst. 

Bei der logistischen Regression mit mehreren Klassen kann der Klassifizierer zur Vorhersage mehrerer Ergebnisse verwendet werden.

## <a name="configure-a-multiclass-logistic-regression"></a>Konfigurieren einer logistischen Regression mit mehreren Klassen

1. Fügen Sie die Komponente **Multiclass Logistic Regression** in die Pipeline ein.

2. Geben Sie an, wie das Modell trainiert werden soll, indem Sie die Option **Create trainer mode** (Trainermodus erstellen) aktivieren.

    + **Single Parameter** (Einzelner Parameter): Verwenden Sie diese Option, wenn Sie wissen, wie Sie das Modell konfigurieren möchten, und geben Sie einen bestimmten Satz von Werten als Argumente an.

    + **Parameter Range** (Parameterbereich): Wählen Sie diese Option, wenn Sie nicht sicher sind, welche Parameter am besten geeignet sind, und einen Parametersweep ausführen möchten. Wählen Sie einen Wertebereich aus, über den iteriert werden soll. Anschließend iteriert das Modul [Tune Model Hyperparameters](tune-model-hyperparameters.md) über alle möglichen Kombinationen der von Ihnen angegebenen Einstellungen, um die Hyperparameter zur Erzielung der optimalen Ergebnisse zu bestimmen.  

3. **Optimization tolerance** (Optimierungstoleranz), geben Sie den Schwellenwert für die Optimierungskonvergenz an. Wenn die Verbesserung zwischen Iterationen kleiner als der Schwellenwert ist, wird der Algorithmus beendet und das aktuelle Modell zurückgegeben.

4. **L1 regularization weight** (L1-Regularisierungsgewichtung), **L2 regularization weight** (L2-Regularisierungsgewichtung): Geben Sie einen Wert für die Regularisierungsparameter „L1“ und „L2“ an. Für beide wird ein Wert ungleich 0 (null) empfohlen.

    Regularisierung ist eine Methode zur Vermeidung von Überpassung durch Zuordnung von Straftermen zu Modellen mit extremen Koeffizientenwerten. Regularisierung funktioniert, indem die Strafterme, die mit Koeffizientenwerten verbunden sind, zum Fehler der Hypothese hinzufügt werden. Ein genaues Modell mit extremen Koeffizientenwerten würde stärker mit Straftermen belegt, während ein weniger genaues Modell mit konservativeren Werten weniger mit Straftermen belegt würde.

     Die L1- und L2-Regularisierung haben unterschiedliche Auswirkungen und Zwecke. L1 kann auf spärliche Modelle angewendet werden, was bei der Arbeit mit Daten mit hoher Dimensionalität nützlich ist. Im Gegensatz dazu ist die L2-Regularisierung für Daten vorzuziehen, die nicht spärlich sind.  Dieser Algorithmus unterstützt eine lineare Kombination von L1- und L2-Regularisierungswerten, d.h. wenn `x = L1` und `y = L2`, dann definiert `ax + by = c` die lineare Spanne der Regularisierungsbegriffe.

     Für logistische Regressionsmodelle wurden verschiedene lineare Kombinationen von L1- und L2-Begriffen entwickelt, z.B. [Regularisierung mit elastischem Netz](https://wikipedia.org/wiki/Elastic_net_regularization).

6. **Random number seed** (Zufälliger Startwert): Geben Sie einen ganzzahligen Wert als Ausgangswert für den Algorithmus ein, wenn die Ergebnisse in den Ausführungen wiederholbar sein sollen. Andernfalls wird ein Systemtaktwert als Startwert verwendet. Dies kann in den Ausführungen derselben Pipeline zu geringfügig unterschiedlichen Ergebnissen führen.

8. Verbinden Sie ein bezeichnetes Dataset, und trainieren Sie das Modell:

    + Wenn Sie **Trainermodus** auf **Einzelparameter** setzen, verbinden Sie einen getaggten Datensatz und die Komponente [Trainmodell](train-model.md).  
  
    + Wenn Sie **Create trainer mode** (Trainermodus erstellen) auf **Parameter Range** (Parameterbereich) festlegen, verbinden Sie ein mit Tags versehenes Dataset, und trainieren Sie das Modell mithilfe von [Tune Model Hyperparameters](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Wenn Sie einen Parameterbereich an [Train Model](train-model.md) übergeben, wird nur der Standardwert in der Liste der Einzelparameter verwendet.  
    > 
    > Wenn Sie einen einzelnen Satz von Parameterwerten an die Komponente [Tune Model Hyperparameters](tune-model-hyperparameters.md) übergeben, ignoriert sie die Werte, wenn sie einen Bereich von Einstellungen für jeden Parameter erwartet, und verwendet die Standardwerte für den Lerner.  
    > 
    > Wenn Sie die Option **Parameter Range** (Parameterbereich) auswählen und einen einzelnen Wert für einen beliebigen Parameter eingeben, wird dieser angegebene einzelne Wert während des gesamten Löschvorgangs verwendet, auch wenn andere Parameter in einem Wertebereich geändert werden.

9. Übermitteln Sie die Pipeline.



## <a name="next-steps"></a>Nächste Schritte

Siehe die [Sammlung der für Azure Machine Learning verfügbaren Komponenten](component-reference.md). 