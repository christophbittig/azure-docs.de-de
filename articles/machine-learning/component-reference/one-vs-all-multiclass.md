---
title: One-vs-All-Multiklasse
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie die Komponente „One-vs-All-Multiklasse“ im Azure Machine Learning-Designer verwenden, um eine Gruppe aus Binärklassifizierungsmodellen zu erstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/13/2020
ms.openlocfilehash: 7d935c2b3b5342b8a3407e607a15fcafd7371ebb
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566529"
---
# <a name="one-vs-all-multiclass"></a>One-vs-All-Multiklasse

In diesem Artikel erfahren Sie, wie Sie die Komponente „One-vs-All-Multiklasse“ im Azure Machine Learning-Designer verwenden. Das Ziel besteht darin, ein Klassifizierungsmodell zu erstellen, das mehrere Klassen unter Verwendung des *One-vs-All*-Ansatzes vorhersagen kann.

Diese Komponente ist nützlich zum Erstellen von Modellen, mit denen drei oder mehr mögliche Ergebnisse vorhergesagt werden, wenn das Ergebnis von kontinuierlichen oder kategorialen Vorhersagevariablen abhängt. Mit dieser Methode können Sie auch Binärklassifizierungsmethoden für Probleme verwenden, die mehrere Ausgabeklassen erfordern.

### <a name="more-about-one-versus-all-models"></a>Weitere Informationen zu One-vs-All-Modellen

Einige Klassifizierungsalgorithmen sind so gestaltet, dass sie die Verwendung von mehr als zwei Klassen ermöglichen. Bei anderen sind die möglichen Ergebnisse auf einen von zwei Werten beschränkt (binäres Modell oder Zweiklassenmodell). Allerdings gibt es auch für Binärklassifizierungsalgorithmen verschiedene Strategien, um sie an Multiklassen-Klassifizierungsaufgaben anzupassen. 

In dieser Komponente wird die „One-vs-All“-Methode implementiert, in der ein binäres Modell für jede der Ausgabeklassen erstellt wird. Die Komponente bewertet jedes dieser binären Modelle für die einzelnen Klassen anhand seines Komplements (alle anderen Klassen im Modell), als wäre es ein Binärklassifizierungsproblem. Dieser Ansatz bietet nicht nur mehr Effizienz in Bezug auf Berechnungen (es werden nur `n_classes`-Klassifizierer benötigt), sondern zudem den Vorteil einer hohen Interpretierbarkeit. Da jede Klasse durch nur einen einzigen Klassifizierer dargestellt wird, ist es möglich, durch Untersuchen des entsprechenden Klassifizierers Informationen über die Klasse zu erhalten. Dies ist die gängigste Strategie für die Multiklassenklassifizierung und eine sinnvolle Standardauswahl. Für die Vorhersage führt die Komponente dann diese binären Klassifizierer aus und wählt die Vorhersage mit dem höchsten Konfidenzscore. 

Im Wesentlichen erstellt die Komponente eine Zusammenstellung einzelner Modelle und führt die Ergebnisse anschließend zusammen, um ein einzelnes Modell zu erstellen, das alle Klassen vorhersagt. Jeder binäre Klassifizierer kann als Grundlage für ein One-vs-All-Modell verwendet werden.  

Ein Beispiel: Angenommen, Sie konfigurieren ein Modell vom Typ [Two-Class Support Vector Machine](two-class-support-vector-machine.md) (2-Klassen-Support Vector Machine) und stellen dieses Modell als Eingabe für die Komponente „One-vs-All-Multiklasse“ bereit. Die Komponente erstellt daraufhin Modelle vom Typ „Two-Class Support Vector Machine“ (2-Klassen-Support Vector Machine) für alle Elemente der Ausgabeklasse. Anschließend wendet es die One-vs-All-Methode an, um die Ergebnisse für alle Klassen zu kombinieren.  

Die Komponente verwendet „OneVsRest“-Klassifizierer von „sklearn“. Weitere Informationen finden Sie [hier](https://scikit-learn.org/stable/modules/generated/sklearn.multiclass.OneVsRestClassifier.html).

## <a name="how-to-configure-the-one-vs-all-multiclass-classifier"></a>Konfigurieren des Klassifizierers „One-vs-All-Multiklasse“  

Diese Komponente erstellt eine Zusammenstellung von Binärklassifizierungsmodellen, um mehrere Klassen zu analysieren. Wenn Sie diese Komponente verwenden möchten, müssen Sie zunächst ein Modell vom Typ *Binärklassifizierung* konfigurieren und trainieren. 

Das binäre Modell wird mit der Komponente „One-vs-All-Multiklasse“ verbunden. Anschließend trainieren Sie die Zusammenstellung von Modellen, indem Sie [Train Model](train-model.md) (Modell trainieren) mit einem bezeichneten Trainingsdataset verwenden.

Wenn Sie die Modelle kombinieren, erstellt „One-vs-All-Multiklasse“ mehrere Binärklassifizierungsmodelle, optimiert den Algorithmus für jede Klasse und führt die Modelle anschließend zusammen. Die Komponente führt diese Aufgaben aus, obwohl das Trainingsdataset mehrere Klassenwerte enthalten kann.

1. Fügen Sie die Komponente „One-vs-All-Multiklasse“ Ihrer Pipeline im Designer hinzu. Sie finden diese Komponente unter **Machine Learning – Initialisieren** in der Kategorie **Klassifizierung**.

   Der Klassifizierer „One-vs-All-Multiklasse“ hat keine eigenen konfigurierbaren Parameter. Alle Anpassungen müssen im Binärklassifizierungsmodell vorgenommen werden, das als Eingabe bereitgestellt wird.

2. Fügen Sie ein Binärklassifizierungsmodell zur Pipeline hinzu, und konfigurieren Sie dieses Modell. Sie können beispielsweise [Two-Class Support Vector Machine](two-class-support-vector-machine.md) (2-Klassen-Support Vector Machine) oder [Two-Class Boosted Decision Tree](two-class-boosted-decision-tree.md) (Verstärkter Entscheidungsbaum mit zwei Klassen) verwenden.

3. Fügen Sie Ihrer Pipeline die Komponente [Train Model](train-model.md) (Modell trainieren) hinzu. Stellen Sie eine Verbindung mit dem untrainierten Klassifizierer her, der von „One-vs-All-Multiklasse“ ausgegeben wird.

4. Stellen Sie für die andere Eingabe von [Train Model](train-model.md) (Modell trainieren) eine Verbindung mit einem bezeichneten Trainingsdataset her, das über mehrere Klassenwerte verfügt.

5. Übermitteln Sie die Pipeline.

## <a name="results"></a>Ergebnisse

Nachdem das Training abgeschlossen ist, können Sie das Modell verwenden, um Multiklassenvorhersagen zu treffen.

Alternativ können Sie den untrainierten Klassifizierer an [Cross-Validate Model](cross-validate-model.md) (Kreuzvalidierung für Modell ausführen) übergeben, um eine Kreuzvalidierung anhand eines bezeichneten Validierungsdatasets auszuführen.


## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die [Sammlung der verfügbaren Komponenten](component-reference.md) für Azure Machine Learning an. 
