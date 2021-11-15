---
title: Schnelle gesamtstrukturbasierte Quantilregression Modulreferenz
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie die Komponente „Fast Forest Quantile Regression“ (Schnelle gesamtstrukturbasierte Quantilregression) verwenden können, um ein Regressionsmodell zu erstellen, das Werte für eine angegebene Anzahl von Quantilen vorausberechnen kann.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/13/2020
ms.openlocfilehash: b6478b1c666f6aee593473f8cdf2a6acd3e1e8c9
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566433"
---
# <a name="fast-forest-quantile-regression"></a>Schnelle gesamtstrukturbasierte Quantilregression

In diesem Artikel wird ein Modul im Azure Machine Learning-Designer beschrieben.

Verwenden Sie diese Komponente zur Erstellung eines Modells für die schnelle gesamtstrukturbasierte Quantilregression in einer Pipeline. Die schnelle gesamtstrukturbasierte Quantilregression ist nützlich, wenn Sie mehr über die Verteilung des vorausberechneten Werts wissen möchten und keinen einzelnen mittleren Vorhersagewert erhalten möchten. Diese Methode hat zahlreiche Anwendungsmöglichkeiten, so z. B.:  
  
- Vorhersagen von Preisen  
  
- Einschätzen der Schülerleistung oder Nutzen von Wachstumsgrafiken zum Bewerten der Kindesentwicklung  
  
- Erkennen prädiktiver Beziehungen in Fällen, bei denen es nur eine schwache Beziehung zwischen Variablen gibt  
  
Dieser Regressionsalgorithmus ist eine **überwachte** Lernmethode, d. h. sie erfordert ein mit Tags versehenes Dataset, das eine Bezeichnungsspalte enthält. Da es sich um einen Regressionsalgorithmus handelt, darf die Bezeichnungsspalte nur numerische Werte enthalten.

## <a name="more-about-quantile-regression"></a>Weitere Informationen zur Quantilregression

Es gibt viele verschiedene Arten von Regression. Einfach gesagt bedeutet Regression das Anpassen eines Modells an ein Ziel, das als numerischer Vektor ausgedrückt ist. Allerdings haben Statistiker zunehmend elaboriertere Methoden für die Regression entwickelt.

Die einfachste Definition von *Quantil* ist ein Wert, der eine Datenmenge in gleich große Gruppen unterteilt. Somit markieren die Quantilwerte die Grenzen zwischen Gruppen. Statistisch gesehen sind Quantile Werte, die in regelmäßigen Abständen anhand des Kehrwerts der kumulativen Verteilungsfunktion einer zufälligen Variable bestimmt werden.

Während lineare Regressionsmodelle versuchen, den Wert einer numerischen Variablen mit einer einzelnen Schätzung, dem *Mittel*, vorherzusagen, ist es mitunter erforderlich, den Bereich oder die Gesamtverteilung der Zielvariablen vorauszuberechnen. Zu diesem Zweck wurden Techniken wie die Bayessche Regression und die Quantilregression entwickelt.

Die Quantilregression hilft Ihnen, die Verteilung des vorhergesagten Werts zu verstehen. Strukturbasierte Quantilregressionsmodelle, wie das in dieser Komponente verwendete Modell, haben den zusätzlichen Vorteil, dass sie zum Vorausberechnen nicht parametrischer Verteilungen verwendet werden können.

  
## <a name="how-to-configure-fast-forest-quantile-regression"></a>Konfigurieren der schnellen gesamtstrukturbasierten Quantilregression

1. Fügen Sie die Komponente **Fast Forest Quantile Regression** (Schnelle gesamtstrukturbasierte Quantilregression) zu Ihrer Pipeline im Designer hinzu. Sie finden diese Komponente unter den **Machine Learning-Algorithmen** in der Kategorie **Regression**.

2. Geben Sie im rechten Bereich der Komponente **Fast Forest Quantile Regression** (Schnelle gesamtstrukturbasierte Quantilregression) an, wie das Modell trainiert werden soll, indem Sie die Option **Create trainer mode** (Trainermodus erstellen) festlegen.  
  
    - **Single Parameter** (Einzelner Parameter): Wenn Sie wissen, wie Sie das Modell konfigurieren möchten, geben Sie eine bestimmte Menge von Werten als Argumente an. Wenn Sie das Modell trainieren, verwenden Sie [Train Model](train-model.md) (Modell trainieren).
  
    - **Parameter Range** (Parameterbereich): Wenn Sie sich der besten Parameter nicht sicher sind, führen Sie eine Parametersuche mit der Komponente [Tune Model Hyperparameters](tune-model-hyperparameters.md) (Abstimmen der Hyperparameter des Modells) durch. Der Trainer durchläuft mehrere von Ihnen angegebene Werte, um die optimale Konfiguration zu finden.

3. **Number of Trees** (Strukturanzahl): Geben Sie die maximale Anzahl von Strukturen ein, die im Ensemble erstellt werden können. Wenn Sie weitere Strukturen erstellen, führt dies im Allgemeinen zu einer höheren Genauigkeit, jedoch auch zu längeren Trainingszeiten.  

4. **Number of Leaves** (Blattanzahl): ,Geben Sie die maximale Anzahl von Blättern oder Endknoten ein, die in jeder Struktur erstellt werden können.  

5. **Minimum number of training instances required to form a leaf** (Mindestanzahl von Trainingsinstanzen, die für die Erstellung eines Blatts erforderlich sind): Geben Sie die Mindestanzahl von Beispielen an, die erforderlich sind, um einen beliebigen Endknoten (Blatt) in einer Struktur zu erstellen.  
  
     Wenn Sie diesen Wert heraufsetzen, erhöht sich der Schwellenwert für die Erstellung neuer Regeln. Bei Verwendung des Standardwerts „1“ reicht für die Erstellung einer neuen Regel beispielsweise bereits ein einzelner Fall aus. Wenn Sie den Wert auf „5“ erhöhen, müssen die Trainingsdaten mindestens fünf Fälle enthalten, die die gleichen Bedingungen erfüllen.

6. **Bagging fraction** (Bagginganteil): Geben Sie eine Zahl zwischen 0 und 1 an, die den Anteil der Beispiele angibt, der beim Erstellen jeder Quantilengruppe verwendet werden soll. Beispiele werden nach dem Zufallsprinzip ausgewählt und ersetzt.

7. **Split fraction** (Aufteilungsanteil): Geben Sie eine Zahl zwischen 0 und 1 ein, die den Anteil der Merkmale angibt, die in jeder Teilung der Struktur verwendet werden sollen. Die Merkmale werden stets nach dem Zufallsprinzip ausgewählt.

8. **Quantiles to be estimated** (Zu schätzende Quantilen): Geben Sie eine durch Semikolon getrennte Liste der Quantilen ein, für die das Modell trainieren und Vorhersagen erstellen soll.
  
     Wenn Sie z. B. ein Modell erstellen möchten, das Quartile schätzt, geben Sie `0.25; 0.5; 0.75` ein.  

9. Geben Sie optional einen Wert für **Random number seed** (zufälliger Startwert) ein, um für den vom Modell verwendeten Zufallszahlengenerator einen Startwert festzulegen.  Der Standardwert 0 ist, was heißt, dass ein zufälliger Seed gewählt wird.
  
     Sie sollten einen Wert angeben, wenn Sie Ergebnisse über nachfolgende Ausführungen mit denselben Daten reproduzieren müssen.  

10. Stellen Sie die Verbindung des Trainingsdatasets und des untrainierten Modells mit einem der Trainingskomponenten her: 

    - Wenn Sie **Create trainer mode** (Trainermodus erstellen) auf **Single Parameter** (Einzelner Parameter) festlegen, müssen Sie die Komponente [Train Model](train-model.md) (Trainieren des Modells) verwenden.

    - Wenn Sie **Create trainer mode** (Trainermodus erstellen) auf **Parameter Range** (Parameterbereich) festlegen, verwenden Sie die Komponente [Tune Model Hyperparameters](tune-model-hyperparameters.md) (Abstimmen der Hyperparameter des Modells).

    > [!WARNING]
    > 
    > - Wenn Sie einen Parameterbereich an [Train Model](train-model.md) übergeben, wird nur der erste Wert in der Parameterbereichsliste verwendet.
    > 
    > - Wenn Sie einen einzelnen Satz von Parameterwerten an die Komponente [Tune Model Hyperparameters](tune-model-hyperparameters.md) übergeben und ein Bereich von Einstellungen für jeden Parameter erwartet wird, werden die Werte ignoriert und stattdessen die Standardwerte für den Lerner verwendet.
    > 
    > - Wenn Sie die Option **Parameter Range** (Parameterbereich) auswählen und einen einzelnen Wert für einen beliebigen Parameter eingeben, wird dieser angegebene einzelne Wert während des gesamten Löschvorgangs verwendet, auch wenn andere Parameter in einem Wertebereich geändert werden.

11. Übermitteln Sie die Pipeline.

## <a name="results"></a>Ergebnisse

Nach Abschluss des Trainings:

+ Um eine Momentaufnahme des trainierten Modells zu speichern, wählen Sie die Trainingskomponente aus und wechseln Sie dann im rechten Bereich zur Registerkarte **Outputs+logs** (Ausgaben und Protokolle). Klicken Sie auf das Symbol für **Dataset registrieren**.  Sie finden das gespeicherte Modell als Komponente in der Komponentenstruktur.

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die [Sammlung der verfügbaren Komponenten](component-reference.md) für Azure Machine Learning an.
