---
title: 'Permutation der Featurerelevanz: Komponentenreferenz'
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie mit der Komponente „Permutation Feature Importance“ (Permutation der Featurerelevanz) im Designer Bewertungen der Permutation der Featurerelevanz für Featurevariablen berechnen können.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/24/2020
ms.openlocfilehash: 256425a672b3eb491ca49097601a1755a7944c03
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566684"
---
# <a name="permutation-feature-importance"></a>Permutation Feature Importance (PFI)

In diesem Artikel wird beschrieben, wie die Komponente „Permutation Feature Importance“ (Permutation der Featurerelevanz) im Azure Machine Learning-Designer eingesetzt wird, um Scores der Featurerelevanz für Ihr Dataset zu berechnen. Sie können anhand dieser Bewertungen die besten Features zur Verwendung in einem Modell bestimmen.

In dieser Komponente werden Featurewerte Spalte für Spalte nach dem Zufallsprinzip neu angeordnet. Vor und nach diesem Vorgang wird die Leistung des Modells gemessen. Sie können hierbei eine der Standardmetriken zum Messen der Leistung auswählen.

Die von der Komponente zurückgegebenen Scores repräsentieren die *Änderung* in der Leistung eines trainierten Modells nach der Permutation. Bei wichtigen Features hat die Neuanordnung in der Regel eine größere Auswirkung und führt deshalb zu höheren Relevanzbewertungen. 

Dieser Artikel enthält eine Übersicht über das Permutationsfeature, die zugrunde liegende Theorie und seine Anwendung beim maschinellen Lernen: [Permutation Feature Importance](/archive/blogs/machinelearning/permutation-feature-importance).  

## <a name="how-to-use-permutation-feature-importance"></a>Verwenden des PFI-Moduls

Um einen Satz mit Featurebewertungen zu generieren, müssen Sie bereits über ein trainiertes Modell sowie ein Testdataset verfügen.  

1.  Fügen Sie Ihrer Pipeline die Komponente „Permutation Feature Importance“ (Permutation der Featurerelevanz) hinzu. Sie finden diese Komponente in der Kategorie **Feature Selection** (Featureauswahl). 

2.  Verbinden Sie ein trainiertes Modul mit der linken Eingabe. Es muss sich bei dem Modell um ein Regressions- oder Klassifizierungsmodell handeln.  

3.  Verbinden Sie ein Dataset mit der rechten Eingabe. Wählen Sie nach Möglichkeit nicht das Dataset aus, das Sie zum Trainieren des Modells verwendet haben, sondern ein anderes. Dieses Dataset wird für Bewertungen basierend auf dem trainierten Modell verwendet. Es wird auch zum Auswerten des Modells nach einer Änderung der Featurewerte verwendet.  

4.  Geben Sie für **Zufälliger Ausgangswert** einen Wert ein, der als Ausgangswert für die zufällige Anordnung verwendet werden soll. Wenn Sie den Wert 0 (den Standardwert) angeben, wird basierend auf der Systemzeit eine Zahl generiert.

     Der Ausgangswert ist optional, aber Sie sollten einen Wert angeben, wenn Sie eine Reproduzierbarkeit für die Ausführungen derselben Pipeline erzielen möchten.  

5.  Wählen Sie für **Metrik zur Leistungsmessung** eine einzelne Metrik aus, die zum Berechnen der Modellqualität nach Abschluss der Permutation verwendet werden soll.  

     Der Azure Machine Learning-Designer unterstützt abhängig davon, ob Sie ein Klassifizierungs- oder ein Regressionsmodell auswerten, die folgenden Metriken:  

    -   **Klassifizierung**

        Accuracy, Precision, Recall  

    -   **Regression**

        Precision, Recall, Mean Absolute Error, Root Mean Squared Error, Relative Absolute Error, Relative Squared Error, Coefficient of Determination  

     Eine ausführlichere Beschreibung dieser Metriken für die Auswertung und deren Berechnung finden Sie im Artikel zur [Modellauswertung](evaluate-model.md).  

6.  Übermitteln Sie die Pipeline.  

7.  Die Komponente gibt eine Liste mit Featurespalten und den zugeordneten Scores aus. Die Liste ist in absteigender Reihenfolge nach den Ergebnissen sortiert.  


##  <a name="technical-notes"></a>Technische Hinweise

Das PFI-Modul führt eine zufällige Änderung der Werte für jede Featurespalte durch (jeweils eine pro Vorgang). Anschließend wird das Modell ausgewertet. 

Die von der Komponente bereitgestellten Rangfolgen unterscheiden sich häufig von denen, die Sie über [Filter Based Feature Selection](filter-based-feature-selection.md) (Filterbasierte Featureauswahl) erhalten. Bei „Filter Based Feature Selection“ werden die Bewertungen *vor* der Erstellung eines Modells berechnet. 

Der Grund für den Unterschied ist, dass bei „Permutation Feature Importance“ die Zuordnung zwischen einem Feature und einem Zielwert nicht gemessen wird. Stattdessen wird erfasst, wie viel Einfluss die einzelnen Features auf Vorhersagen des Modells haben.
  
## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die [verfügbaren Komponenten](component-reference.md) für Azure Machine Learning an.