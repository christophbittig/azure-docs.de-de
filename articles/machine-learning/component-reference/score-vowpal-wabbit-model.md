---
title: Score Vowpal Wabbit-Modell
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie mit der Komponente „Score Vowpal Wabbit Model“ (Vowpal Wabbit-Modell bewerten) Bewertungen für Eingabedaten mithilfe eines trainierten Vowpal Wabbit-Modells generieren können.
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/02/2020
ms.openlocfilehash: c673b01e48b9a20648b0d5fb3fa05511ee64f49b
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566572"
---
# <a name="score-vowpal-wabbit-model"></a>Bewerten von Vowpal Wabbit-Modellen
In diesem Artikel wird beschrieben, wie Sie die Komponente **Score Vowpal Wabbit Model** im Azure Machine Learning-Designer verwenden, um mithilfe eines vorhandenen trainierten Vowpal Wabbit-Modells Bewertungen für Eingabedaten zu generieren.  

Diese Komponente bietet die neueste Version des „Vowpal Wabbit“-Frameworks (Version 8.8.1). Verwenden Sie diese Komponente, um Daten mithilfe eines trainierten Modells zu bewerten, das im VW-Format der Version 8 gespeichert ist.  

## <a name="how-to-configure-score-vowpal-wabbit-model"></a>Konfigurieren eines Score Vowpal Wabbit-Modells

1.  Fügen Sie Ihrem Experiment die **Score Vowpal Wabbit Model**-Komponente hinzu.  
  
2.  Fügen Sie ein trainiertes Vowpal Wabbit-Modell hinzu, und verbinden Sie es mit dem linken Eingabeport. Sie können ein trainiertes Modell verwenden, das im gleichen Experiment erstellt wurde, oder ein gespeichertes Modell in der **Datasets**-Kategorie des linken Navigationsbereichs des Designers suchen. Das Modell muss jedenfalls im Azure Machine Learning-Designer verfügbar sein.  
  
    > [!NOTE]
    > Nur Vowpal Wabbit 8.8.1-Modelle werden unterstützt. Sie können keine gespeicherten Modelle verbinden, die mithilfe anderer Algorithmen trainiert wurden.
  
3.  Fügen Sie das Testdataset hinzu, und verbinden Sie es mit dem rechten Eingabeport. Wenn es sich bei dem Testdataset um ein Verzeichnis handelt, das die Testdatendatei enthält, geben Sie den Namen der Testdatendatei unter **Name of the test data file** (Name der Testdatendatei) an. Wenn das Testdataset eine einzelne Datei ist, lassen Sie das Feld **Name of the test data file** (Name der Testdatendatei) leer.

4. Geben Sie im Textfeld **VW arguments** (VW-Argumente) eine Gruppe gültiger Befehlszeilenargumente für die ausführbare Datei für Vowpal Wabbit an.  

    Informationen darüber, welche Vowpal Wabbit-Argumente in Azure Machine Learning unterstützt werden und welche nicht, finden Sie im Abschnitt [Technische Hinweise](#technical-notes).  

5.  **Name of the test data file** (Name der Testdatendatei): Geben Sie den Namen der Datei an, die die Eingabedaten enthält. Dieses Argument wird nur verwendet, wenn es sich bei dem Testdataset um ein Verzeichnis handelt.

6. **Specify file type** (Dateityp angeben): Geben Sie an, welches Format für Ihre Trainingsdaten verwendet wird. Vowpal Wabbit unterstützt die folgenden beiden Eingabedateiformate:  

   - **VW** ist das von Vowpal Wabbit verwendete interne Format. Ausführliche Informationen finden Sie auf der [Vowpal Wabbit-Wikiseite](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format). 
   - **SVMLight** ist ein von einigen anderen Machine-Learning-Tools verwendetes Format. 

7. Wählen Sie die Option **Include an extra column containing labels** (Eine zusätzliche Spalte mit Bezeichnungen einschließen) aus, wenn Sie Bezeichnungen mit den Bewertungen ausgeben möchten.  

   Bei der Verarbeitung von Textdaten setzt Vowpal Wabbit in der Regel keine Bezeichnungen voraus, und es werden nur die Ergebnisse für jede Datenzeile zurückgegeben.  

8. Wählen Sie die Option **Include an extra column containing raw scores** (Eine zusätzliche Spalte mit Rohergebnissen einschließen) aus, wenn Sie Rohergebnisse mit den Bewertungen ausgeben möchten.  

9. Übermitteln Sie die Pipeline.

## <a name="results"></a>Ergebnisse

Nach Abschluss des Trainings:

+ Klicken Sie auf die Ausgabe der [Score Vowpal Wabbit Model](score-vowpal-wabbit-model.md)-Komponente, um die Ergebnisse zu visualisieren. Die Ausgabe gibt an, dass ein Vorhersageergebnis von 0 bis 1 normalisiert wurde. 

+ Um die Ergebnisse auszuwerten, sollte das Ausgabedataset bestimmte Bewertungsspaltennamen enthalten, die die Anforderungen der „Evaluate Model“-Komponente (Modell auswerten) erfüllen.

  + Für die Regressionsaufgabe muss das auszuwertende Dataset über eine Spalte mit dem Namen `Regression Scored Labels` verfügen, die bewertete Bezeichnungen darstellt.
  + Für die binäre Klassifizierungsaufgabe muss das auszuwertende Dataset über zwei Spalten mit den Namen `Binary Class Scored Labels` und `Binary Class Scored Probabilities` verfügen, die bewertete Bezeichnungen bzw. Wahrscheinlichkeiten darstellen.
  + Für die Mehrfachklassifizierungsaufgabe muss das auszuwertende Dataset über eine Spalte mit dem Namen `Multi Class Scored Labels` verfügen, die bewertete Bezeichnungen darstellt.

  Beachten Sie, dass die Ergebnisse der „Score Vowpal Wabbit Model“-Komponente nicht direkt ausgewertet werden können. Vor der Auswertung sollte das Dataset gemäß den oben aufgeführten Anforderungen geändert werden.

##  <a name="technical-notes"></a>Technische Hinweise

Dieser Abschnitt enthält Implementierungsdetails, Tipps und Antworten auf häufig gestellte Fragen.

### <a name="parameters"></a>Parameter

Vowpal Wabbit verfügt über viele Befehlszeilenoptionen zum Auswählen und Optimieren von Algorithmen. Eine vollständige Erläuterung dieser Optionen ist hier nicht möglich. Sie sollten die [Wiki-Seite von Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments) besuchen.  

Die folgenden Parameter werden in Azure Machine Learning Studio (klassisch) nicht unterstützt.  

-   Die unter [https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments) aufgeführten Eingabe-/Ausgabeoptionen.  
  
     Diese Eigenschaften werden bereits automatisch von der Komponente konfiguriert.  
  
-   Außerdem sind alle Optionen, die mehrere Ausgaben generieren oder mehrere Eingaben erfordern, nicht zulässig. Dazu zählen *`--cbt`* , *`--lda`* und *`--wap`* .  
  
-   Nur überwachte Lernalgorithmen werden unterstützt. Dies lässt diese Optionen nicht zu: *`–active`* , `--rank`, *`--search`* usw.  

Alle anderen Argumente als die oben beschriebenen sind zulässig.

## <a name="next-steps"></a>Nächste Schritte

Hier finden Sie die für Azure Machine Learning [verfügbaren Komponenten](component-reference.md). 