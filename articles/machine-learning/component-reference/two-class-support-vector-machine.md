---
title: 'Support Vector Machine mit zwei Klassen: Komponentenreferenz'
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie mit der Komponente „Two-Class Support Vector Machine“ (Support Vector Machine mit zwei Klassen) in Azure Machine Learning einen binären Klassifizierer erstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: d0d6b8f54bf17d83a35c9c30482a873d750b5aed
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566524"
---
# <a name="two-class-support-vector-machine-component"></a>Komponente „Two-Class Support Vector Machine“ (Support Vector Machine mit zwei Klassen)

In diesem Artikel wird eine Komponente im Azure Machine Learning-Designer beschrieben.

Verwenden Sie diese Komponente, um ein Modell zu erstellen, das auf dem Support Vector Machine-Algorithmus basiert. 

Support Vector Machines (SVMs) sind eine gut erforschte Klasse von überwachten Learning-Methoden. Diese spezielle Implementierung eignet sich zur Vorhersage von zwei möglichen Ergebnissen, die entweder auf kontinuierlichen oder kategorischen Variablen basieren.

Nachdem Sie die Modellparameter definiert haben, trainieren Sie das Modell unter Verwendung der Trainingskomponenten, und stellen Sie ein *markiertes Dataset* bereit, das eine Bezeichnung oder eine Ergebnisspalte enthält.

## <a name="about-support-vector-machines"></a>Über Support Vector Machines

Support Vector Machines gehören zu den frühesten Machine Learning-Algorithmen, und SVM-Modelle wurden in vielen Anwendungen verwendet – von der Informationsbeschaffung bis zur Text- und Bildklassifizierung. SVMs können für sowohl Klassifizierungs-als auch Regressionsaufgaben verwendet werden.

Diese SVM-Modell ist ein überwachtes Learning-Modell, für das bezeichnete Daten erforderlich sind. Im Trainingsprozess analysiert der Algorithmus Eingabedaten und erkennt Muster in einem mehrdimensionalen Featureraum namens *Hyperebene*.  Alle Eingabebeispiele werden in diesem Raum als Punkte dargestellt und auf Ausgabekategorien so abgebildet, dass Kategorien durch eine möglichst große Breite geteilt werden und eine Lücke schließen.

Für die Vorhersage ordnet der SVM-Algorithmus neue Beispiele der einen oder anderen Kategorie zu und ordnet sie dem gleichen Raum zu. 

## <a name="how-to-configure"></a>Vorgehensweise zur Konfiguration 

Für diesen Modelltyp wird empfohlen, dass Sie den Datensatz normalisieren, bevor Sie ihn zum Trainieren des Klassifikators verwenden.
  
1.  Fügen Sie Ihrer Pipeline die Komponente **Two-Class Support Vector Machine** (Support Vector Machine mit zwei Klassen) hinzu.  
  
2.  Geben Sie an, wie das Modell trainiert werden soll, indem Sie die Option **Create trainer mode** (Trainermodus erstellen) aktivieren.  
  
    -   **Single Parameter** (Einzelner Parameter): Wenn Sie wissen, wie Sie das Modell konfigurieren möchten, können Sie einen bestimmten Satz von Werten als Argumente angeben.  

    -   **Parameter Range** (Parameterbereich): Wenn Sie sich hinsichtlich der besten Parameter nicht sicher sind, können Sie optimale Parameter mithilfe der Komponente [Tune Model Hyperparameters](tune-model-hyperparameters.md) (Modellhyperparameter optimieren) finden. Sie geben einen Wertebereich an, woraufhin das Training über mehrere Einstellungskombinationen iteriert, um die Wertekombination zu bestimmen, die das beste Ergebnis liefert.

3.  Geben Sie für **Anzahl der Iterationen** eine Zahl ein, die die Anzahl der beim Erstellen des Modells verwendeten Iterationen angibt.  
  
     Dieser Parameter kann verwendet werden, um den Kompromiss zwischen Trainingsgeschwindigkeit und Genauigkeit zu steuern.  
  
4.  Geben Sie für **Lambda** einen Wert ein, der als Gewichtung für die L1-Regularisierung verwendet werden soll.  
  
     Dieser Regularisierungskoeffizient kann zur Optimierung des Modells verwendet werden. Größere Werte führen zu komplexeren Modellen.  
  
5.  Wählen Sie die Option **Features normalisieren**, wenn Sie Features vor dem Training normalisieren möchten.
  
     Wenn Sie die Normalisierung anwenden, werden die Datenpunkte vor dem Training auf den Mittelwert zentriert und auf eine Einheit der Standardabweichung skaliert.
  
6.  Wählen Sie die Option **In Einheitssphäre projizieren**, um die Koeffizienten zu normalisieren.
  
     Das Projizieren von Werten zum Einheitsraum bedeutet, dass die Datenpunkte vor dem Training auf 0 zentriert und auf eine Einheit der Standardabweichung skaliert werden.
  
7.  Geben Sie unter **Zufälliger Ausgangswert** einen ganzzahligen Wert ein, der als Ausgangswert verwendet werden soll, wenn Sie die Reproduzierbarkeit über mehrere Ausführungen hinweg sicherstellen möchten.  Andernfalls wird ein von der Systemuhr bereitgestellter Wert als Ausgangswert verwendet, was bei jeder Ausführung zu geringfügig unterschiedlichen Ergebnissen führen kann.
  
9. Verbinden Sie ein bezeichnetes Dataset, und trainieren Sie das Modell:

    + Wenn Sie **Create trainer mode** (Trainermodus erstellen) auf **Single Parameter** (Einzelner Parameter) festlegen, müssen Sie ein mit Tags versehenes Dataset und die Komponente [Train Model](train-model.md) (Modell trainieren) verbinden.  
  
    + Wenn Sie **Create trainer mode** (Trainermodus erstellen) auf **Parameter Range** (Parameterbereich) festlegen, verbinden Sie ein mit Tags versehenes Dataset, und trainieren Sie das Modell mithilfe von [Tune Model Hyperparameters](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Wenn Sie einen Parameterbereich an [Train Model](train-model.md) übergeben, wird nur der Standardwert in der Liste der Einzelparameter verwendet.  
    > 
    > Wenn Sie eine einzelne Reihe bestimmter Parameterwerte an die Komponente [Tune Model Hyperparameters](tune-model-hyperparameters.md) (Modellhyperparameter optimieren) übergeben und ein Bereich von Einstellungen für jeden Parameter erwartet wird, werden die Werte ignoriert und stattdessen die Standardwerte für den Learner verwendet.  
    > 
    > Wenn Sie die Option **Parameter Range** (Parameterbereich) auswählen und einen einzelnen Wert für einen beliebigen Parameter eingeben, wird dieser angegebene einzelne Wert während des gesamten Löschvorgangs verwendet, auch wenn andere Parameter in einem Wertebereich geändert werden.
  
10. Übermitteln Sie die Pipeline.

## <a name="results"></a>Ergebnisse

Nach Abschluss des Trainings:

+ Um eine Momentaufnahme des trainierten Modells zu speichern, wählen Sie die Registerkarte **Ausgaben** im rechten Bereich der Komponente **Train model** (Modell trainieren) aus. Wählen Sie das Symbol **Register dataset** (Dataset registrieren) aus, um das Modell als wiederverwendbare Komponente zu speichern.

+ Um das Modell für die Bewertung zu verwenden, fügen Sie einer Pipeline die Komponente **Score Model** (Modell bewerten) hinzu.


## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die [verfügbaren Komponenten](component-reference.md) für Azure Machine Learning an. 