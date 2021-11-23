---
title: Tune Model Hyperparameters
titleSuffix: Azure Machine Learning
description: Verwenden Sie die Komponente Tune Model Hyperparameters (Hyperparameter abstimmen) im Designer, um einen Parameter-Sweep zur Abstimmung von Hyperparametern durchzuführen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2020
ms.openlocfilehash: f11800c1501c0efad6dec18542ecaa912527689a
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491657"
---
# <a name="tune-model-hyperparameters"></a>Tune Model Hyperparameters

Dieser Artikel beschreibt, wie Sie die Komponente Tune Model Hyperparameters im Azure Machine Learning Designer verwenden. Das Ziel besteht darin, die optimalen Hyperparameter für ein Machine Learning-Modell zu ermitteln. Die Komponente erstellt und testet mehrere Modelle unter Verwendung verschiedener Kombinationen von Einstellungen. Die Metriken werden über alle Modelle hinweg verglichen, um die Kombinationen der Einstellungen zu erhalten. 

Die Begriffe *Parameter* und *Hyperparameter* können verwirrend sein. Die *Parameter* des Modells werden im rechten Fensterbereich der Komponente festgelegt. Im Wesentlichen führt diese Komponente einen *Parameter-Sweep* über die angegebenen Parametereinstellungen durch. Es wird ein optimaler Satz mit _Hyperparametern_ erlernt. Diese können sich für jede spezifische Entscheidungsstruktur, jedes Dataset oder jede Regressionsmethode unterscheiden. Der Prozess zum Finden der optimalen Konfiguration wird manchmal als *Optimierung* bezeichnet. 

Die Komponente unterstützt die folgende Methode zur Ermittlung der optimalen Einstellungen für ein Modell: *integriertes Trainieren und Tunen.* Bei dieser Methode konfigurieren Sie einen Satz mit Parametern zur späteren Verwendung. Sie lassen die Komponente dann über mehrere Kombinationen iterieren. Die Komponente misst die Genauigkeit, bis sie ein "bestes" Modell gefunden hat. Bei den meisten Lernkomponenten können Sie wählen, welche Parameter während des Lernprozesses geändert werden sollen und welche unverändert bleiben sollen.

Abhängig davon, wie lange der Optimierungsprozess ausgeführt werden soll, treffen Sie unter Umständen die Entscheidung, alle Kombinationen eingehend zu testen. Sie können den Prozess auch verkürzen, indem Sie ein Raster mit Parameterkombinationen einrichten und eine zufällig ausgewählte Teilmenge des Parameterrasters testen.

Diese Methode generiert ein trainiertes Modell, das Sie zur Wiederverwendung speichern können.  

> [!TIP] 
> Sie können eine entsprechende Aufgabe ausführen. Vor Beginn einer Optimierung sollten Sie eine Auswahl von Merkmalen (Featureauswahl) anwenden, um die Spalten oder Variablen zu bestimmen, die den größten Informationswert haben.

## <a name="how-to-configure-tune-model-hyperparameters"></a>Konfigurieren von „Tune Model Hyperparameters“  

Zum Erlernen der optimalen Hyperparameter für ein Machine Learning-Modell ist eine Pipelinenutzung in erheblichem Maße erforderlich.

### <a name="train-a-model-by-using-a-parameter-sweep"></a>Trainieren eines Modells mit einer Parameterbereinigung  

In diesem Abschnitt wird beschrieben, wie ein grundlegender Parameter-Sweep durchgeführt wird, bei dem ein Modell mit Hilfe der Komponente Tune Model Hyperparameters trainiert wird.

1.  Fügen Sie die Komponente Tune Model Hyperparameters zu Ihrer Pipeline im Designer hinzu.

2.  Verbinden Sie ein untrainiertes Modell mit der am weitesten links befindlichen Eingabe. 

    > [!NOTE] 
    > **Modell-Hyperparameter abstimmen** kann nur mit eingebauten Algorithmuskomponenten für maschinelles Lernen verbunden werden und unterstützt keine angepassten Modelle, die in **Python-Modell erstellen** erstellt wurden.


3.  Fügen Sie das Dataset hinzu, das Sie für das Training verwenden möchten, und verbinden Sie es mit der mittleren Eingabe von „Tune Model Hyperparameters“.  

    Wenn Sie ein mit Tags versehenes Dataset haben, können Sie es optional mit dem am weitesten rechts befindlichen Eingabeport (**Optional validation dataset**, „Optionales Validierungsdataset“) verbinden. Dies ermöglicht es Ihnen, die Genauigkeit beim Training und bei der Optimierung zu messen.

4.  Wählen Sie im rechten Bereich von „Tune Model Hyperparameters“ einen Wert für **Parameter sweeping mode** (Parameterbereinigungsmodus) aus. Mit dieser Option wird gesteuert, wie die Parameter ausgewählt werden.

    - **Ganzes Raster**: Wenn Sie diese Option wählen, durchläuft die Komponente ein vom System vorgegebenes Raster, um verschiedene Kombinationen auszuprobieren und den besten Lernenden zu ermitteln. Diese Option ist nützlich, falls Sie die besten Parametereinstellungen nicht kennen und alle möglichen Kombinationen von Werten ausprobieren möchten.

    - **Zufallsabtastung**: Wenn Sie diese Option wählen, wählt die Komponente zufällig Parameterwerte über einen vom System definierten Bereich aus. Sie müssen die maximale Anzahl von Läufen angeben, die die Komponente ausführen soll. Diese Option ist nützlich, wenn Sie die Modellleistung mit den von Ihnen gewünschten Metriken steigern, aber weiterhin Computeressourcen sparen möchten.    

5.  Öffnen Sie für **Label column** (Bezeichnungsspalte) die Spaltenauswahl, um eine einzelne Bezeichnungsspalte auszuwählen.

6.  Auswählen der Anzahl von Ausführungen:

    - **Maximum number of runs on random sweep**: Wenn Sie eine zufällige Bereinigung auswählen, können Sie angeben, wie oft das Modell mit einer zufälligen Kombination von Parameterwerten trainiert werden soll.

7.  Wählen Sie unter **Rang** eine Metrik aus, die zum Zuweisen eines Rangs für die Modelle verwendet werden soll.

    Wenn Sie einen Parameter-Sweep durchführen, berechnet die Komponente alle anwendbaren Metriken für den Modelltyp und gibt sie im Bericht **Sweep-Ergebnisse** zurück. Die Komponente verwendet separate Metriken für Regressions- und Klassifikationsmodelle.

    Die von Ihnen gewählte Metrik bestimmt aber, wie die Modelle eingestuft werden. Nur das oberste Modell, eingestuft entsprechend der gewählten Metrik, wird als trainiertes Modell ausgegeben, das für die Bewertung verwendet werden soll.

8.  Geben Sie für **Random seed**(Zufälliger Ausgangswert) eine ganze Zahl als Pseudozustand für den Zufallszahlengenerator ein, der zum zufälligen Auswählen von Parameterwerten aus einem vordefinierten Bereich verwendet wird. Dieser Parameter ist nur wirksam, wenn der **Parameter sweeping mode** (Parameterbereinigungsmodus) den Wert **Random sweep** (Zufällige Bereinigung) hat.

9. Übermitteln der Pipeline

## <a name="results-of-hyperparameter-tuning"></a>Ergebnisse einer Hyperparameteroptimierung

Nach Abschluss des Trainings:

+ Um die Sweep-Ergebnisse zu betrachten, können Sie entweder mit der rechten Maustaste auf die Komponente klicken und dann **Visualisieren** wählen oder mit der rechten Maustaste auf den linken Ausgangsanschluss der Komponente klicken, um sie zu visualisieren.

    Die **Bereinigungsergebnisse** enthält die gesamte Parameterbereingung und alle Genauigkeitsmetriken, die für den Modelltyp zutreffen. Und anhand der Metrik, die Sie für die Einstufung ausgewählt haben, wird bestimmt, welches Modell als das „beste“ angesehen wird.

+ Um einen Schnappschuss des trainierten Modells zu speichern, wählen Sie die Registerkarte **Ausgaben+Protokolle** im rechten Feld der Komponente **Modell trainieren**. Wählen Sie das Symbol **Datensatz registrieren**, um das Modell als wiederverwendbare Komponente zu speichern.


## <a name="technical-notes"></a>Technische Hinweise

Dieser Abschnitt enthält Details und Tipps zur Implementierung.

### <a name="how-a-parameter-sweep-works"></a>Funktionsweise einer Parameterbereingung

Beim Einrichten einer Parameterbereinigung definieren Sie den Bereich Ihrer Suche. Bei der Suche kann eine endliche Anzahl von zufällig ausgewählten Parametern verwendet werden. Es kann auch eine umfassende Suche in einem von Ihnen definierten Parameterbereich durchgeführt werden.

+ **Random sweep** (Zufällige Bereinigung): Mit dieser Option wird ein Modell mit einer festgelegten Anzahl von Iterationen trainiert. 

  Sie geben einen Wertebereich an, über den iteriert werden soll, und die Komponente verwendet eine zufällig ausgewählte Teilmenge dieser Werte. Werte werden mit Ersetzung ausgewählt, d. h., dass zuvor zufällig ausgewählte Zahlen nicht aus dem Pool der verfügbaren Zahlen entfernt werden. Daher bleibt die Wahrscheinlichkeit für die Auswahl eines Werts in allen Durchläufen gleich.  

+ **Entire grid** (Gesamtes Raster): Die Option zum Verwenden des gesamten Rasters bedeutet, dass jede mögliche Kombination getestet wird. Diese Option ist die gründlichste, benötigt aber auch die meiste Zeit. 

### <a name="controlling-the-length-and-complexity-of-training"></a>Steuern der Länge und Komplexität eines Trainings

Das Iterieren über viele Einstellungskombinationen kann zeitaufwändig sein, daher bietet die Komponente mehrere Möglichkeiten, den Prozess einzuschränken:

+ Begrenzen der Anzahl von Iterationen, die zum Testen eines Modells verwendet werden
+ Begrenzen des Parameterbereichs
+ Begrenzen der Anzahl von Iterationen und des Parameterbereichs

Es empfiehlt sich, mit den Einstellungen zu experimentieren, um die effizienteste Methode des Trainings für ein bestimmtes Dataset und Modell zu ermitteln.

### <a name="choosing-an-evaluation-metric"></a>Auswählen einer Auswertungsmetrik

Am Ende des Testvorgangs wird vom Modell ein Bericht angezeigt, in dem die Genauigkeit für jedes Modell angegeben ist, damit Sie die Metrikergebnisse überprüfen können:

- Für alle Modelle für die Binärklassifizierung wird ein einheitlicher Metriksatz verwendet.
- Die Genauigkeit wird für alle Klassifizierungsmodelle mit mehreren Klassen verwendet.
- Für Regressionsmodelle wird ein anderer Metriksatz genutzt. 

Während eines Trainings müssen Sie jedoch eine *einzige* Metrik auswählen, die für die Einstufung der Modelle verwendet werden soll, die während des Optimierungsprozesses generiert werden. Unter Umständen stellen Sie fest, dass die beste Metrik je nach Ihrem Geschäftsproblem und dem Aufwand für falsch positive und falsche negative Ergebnisse variiert.

#### <a name="metrics-used-for-binary-classification"></a>Metriken, die für Binärklassifizierung verwendet werden

-   **Accuracy** (Treffergenauigkeit): Anteil von richtigen Ergebnissen an den Gesamtfällen  

-   **Precision** (Genauigkeit): Anteil von richtigen Ergebnisse an allen positiven Ergebnissen  

-   **Recall** (Trefferquote): Anteil aller richtigen Ergebnisse an allen Ergebnissen  

-   **F-score** (F-Maß): Maß, das Genauigkeit und Trefferquote abgleicht  

-   **AUC**: Wert, der den Bereich unter der Kurve darstellt, wenn falsch positive Ergebnisse auf der x-Achse geplottet und richtig positive Ergebnisse auf der y-Achse geplottet werden  

-   **Average Log Loss** (Logarithmische Durchschnittsdämpfung): Differenz zwischen zwei Wahrscheinlichkeitsverteilungen – der tatsächlichen und der im Modell  

#### <a name="metrics-used-for-regression"></a>Metriken, die für Regression verwendet werden

-   **Mean absolute error** (Mittlerer absoluter Fehler): Mittelt den gesamten Fehler im Modell, wobei mit *Fehler* der Abstand des vorhergesagten Werts zum wahren Wert gemeint ist. Wird häufig als *MAE* abgekürzt.  

-   **Root mean squared error** (Standardfehler der Regression) misst den Durchschnitt der Quadrate der Fehler und berechnet dann die Quadratwurzel dieses Werts. Wird häufig als *RMSE* abgekürzt.  

-   **Relative absolute error** (Relativer absoluter Fehler) entspricht dem Fehler als Prozentsatz des richtigen Werts.  

-   **Relative squared error** (Relativer quadratischer Fehler): Normalisiert den gesamten quadratischen Fehler, indem dieser durch den gesamten quadratischen Fehler der vorhergesagten Werte dividiert wird.  

-   **Coefficient of determination** (Bestimmtheitsmaß): Einzelne Zahl, die angibt, wie gut Daten zu einem Modell passen. Der Wert „1“ bedeutet, dass das Modell genau mit den Daten übereinstimmt. Der Wert „0“ bedeutet, dass die Daten zufälliger Art sind oder aus anderen Gründen nicht an das Modell angepasst werden können. Häufig lautet die Bezeichnung hierfür *r <sup>2</sup>* , *R <sup>2</sup>* oder *r-squared* (R zum Quadrat).  

### <a name="components-that-dont-support-a-parameter-sweep"></a>Komponenten, die keinen Parameter-Sweep unterstützen

Fast alle Lernmodule in Azure Machine Learning unterstützen Kreuzvalidierung mit einer integrierten Parameterbereinigung, mit der Sie die Parameter auswählen können, mit denen experimentiert werden soll. Ist es nicht möglich, für das Lernmodul einen Wertebereich festzulegen, können Sie es trotzdem in Kreuzvalidierungen verwenden. In diesem Fall wird ein Bereich mit zulässigen Werten für die Bereinigung ausgewählt. 


## <a name="next-steps"></a>Nächste Schritte

Hier finden Sie die für Azure Machine Learning [verfügbaren Komponenten](component-reference.md). 
