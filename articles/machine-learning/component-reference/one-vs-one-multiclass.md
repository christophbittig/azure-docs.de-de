---
title: One-vs-One Multiclass
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie die One-vs-One Multiclass-Komponente in Azure Machine Learning verwenden, um ein Multiklassen-Klassifizierungsmodell aus einem Ensemble von binären Klassifizierungsmodellen zu erstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/13/2020
ms.openlocfilehash: 176b605de08e705571aea1b0e391192d2c18532b
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566675"
---
# <a name="one-vs-one-multiclass"></a>One-vs-One Multiclass

Dieser Artikel beschreibt die Verwendung der Komponente One-vs-One Multiclass im Azure Machine Learning Designer. Das Ziel besteht darin, ein Klassifizierungsmodell mit dem Ansatz *One-vs-One* zu erstellen, das mehrere Klassen vorhersagen kann.

Diese Komponente ist nützlich für die Erstellung von Modellen, die drei oder mehr mögliche Ergebnisse vorhersagen, wenn das Ergebnis von kontinuierlichen oder kategorialen Prädiktorvariablen abhängt. Mit dieser Methode können Sie auch Binärklassifizierungsmethoden für Probleme verwenden, die mehrere Ausgabeklassen erfordern.

### <a name="more-about-one-versus-one-models"></a>Weitere Informationen zu One-vs-One-Modellen

Einige Klassifizierungsalgorithmen sind so gestaltet, dass sie die Verwendung von mehr als zwei Klassen ermöglichen. Bei anderen sind die möglichen Ergebnisse auf einen von zwei Werten beschränkt (binäres Modell oder Zweiklassenmodell). Allerdings gibt es auch für Binärklassifizierungsalgorithmen verschiedene Strategien, um sie an Multiklassen-Klassifizierungsaufgaben anzupassen. 

Diese Komponente implementiert die Eins-gegen-Eins-Methode, bei der pro Klassenpaar ein binäres Modell erstellt wird. Zum Zeitpunkt der Vorhersage wird die Klasse ausgewählt, die die meisten Stimmen erhalten hat. Da sie `n_classes * (n_classes - 1) / 2` Klassifizierer aufnehmen muss, ist diese Methode aufgrund ihrer O(n_classes^2)-Komplexität in der Regel langsamer als die One-vs-All-Methode. Für Algorithmen wie z. B. Kernelalgorithmen, die sich mit `n_samples` nicht gut skalieren lassen, kann diese Methode allerdings Vorteile bringen. Das liegt daran, dass jedes einzelne Lernproblem nur eine kleine Teilmenge der Daten betrifft, wohingegen bei One-vs-All das vollständige Dataset so häufig verwendet wird wie mit `n_classes` angegeben.

Im Wesentlichen erstellt die Komponente ein Ensemble von Einzelmodellen und führt dann die Ergebnisse zusammen, um ein einziges Modell zu erstellen, das alle Klassen vorhersagt. Als Grundlage für ein One-vs-One-Modell kann jeder Binärklassifizierer verwendet werden.  

Nehmen wir zum Beispiel an, Sie konfigurieren ein [Zwei-Klassen-Support-Vector-Machine](two-class-support-vector-machine.md)-Modell und stellen dieses als Eingabe für die One-vs-One Multiclass-Komponente bereit. Die Komponente würde Zwei-Klassen-Support-Vector-Machine-Modelle für alle Mitglieder der Ausgabeklasse erstellen. Anschließend wendet es die One-vs-One-Methode an, um die Ergebnisse für alle Klassen zu kombinieren.  

Die Komponente verwendet OneVsOneClassifier von sklearn, und Sie können mehr Details [hier](https://scikit-learn.org/stable/modules/generated/sklearn.multiclass.OneVsOneClassifier.html) erfahren.

## <a name="how-to-configure-the-one-vs-one-multiclass-classifier"></a>Konfigurieren des Klassifizierers für „One-vs-One Multiclass“  

Diese Komponente erstellt ein Ensemble von binären Klassifikationsmodellen, um mehrere Klassen zu analysieren. Um diese Komponente zu verwenden, müssen Sie zunächst ein *binäres Klassifikationsmodell* konfigurieren und trainieren. 

Sie verbinden das Binärmodell mit der Komponente One-vs-One Multiclass. Anschließend trainieren Sie die Zusammenstellung von Modellen, indem Sie [Train Model](train-model.md) (Modell trainieren) mit einem bezeichneten Trainingsdataset verwenden.

Wenn Sie die Modelle kombinieren, erstellt „One-vs-One Multiclass“ mehrere Binärklassifizierungsmodelle, optimiert den Algorithmus für jede Klasse und führt die Modelle anschließend zusammen. Die Komponente erledigt diese Aufgaben, obwohl der Trainingsdatensatz mehrere Klassenwerte enthalten kann.

1. Fügen Sie die Komponente One-vs-One Multiclass im Designer zu Ihrer Pipeline hinzu. Sie finden diese Komponente unter **Maschinelles Lernen - Initialisieren**, in der Kategorie **Klassifikation**.

   Der Klassifizierer für „One-vs-One Multiclass“ besitzt selbst keine konfigurierbaren Parameter. Alle Anpassungen müssen im Binärklassifizierungsmodell vorgenommen werden, das als Eingabe bereitgestellt wird.

2. Fügen Sie ein Binärklassifizierungsmodell zur Pipeline hinzu, und konfigurieren Sie dieses Modell. Sie können beispielsweise [Two-Class Support Vector Machine](two-class-support-vector-machine.md) (2-Klassen-Support Vector Machine) oder [Two-Class Boosted Decision Tree](two-class-boosted-decision-tree.md) (Verstärkter Entscheidungsbaum mit zwei Klassen) verwenden.

3. Fügen Sie die Komponente [Train Model](train-model.md) zu Ihrer Pipeline hinzu. Stellen Sie eine Verbindung mit dem untrainierten Klassifizierer her, der von „One-vs-One Multiclass“ ausgegeben wird.

4. Stellen Sie für die andere Eingabe von [Train Model](train-model.md) (Modell trainieren) eine Verbindung mit einem bezeichneten Trainingsdataset her, das über mehrere Klassenwerte verfügt.

5. Übermitteln Sie die Pipeline.

## <a name="results"></a>Ergebnisse

Nachdem das Training abgeschlossen ist, können Sie das Modell verwenden, um Multiklassenvorhersagen zu treffen.

Alternativ können Sie den untrainierten Klassifizierer an [Cross-Validate Model](cross-validate-model.md) (Kreuzvalidierung für Modell ausführen) übergeben, um eine Kreuzvalidierung anhand eines bezeichneten Validierungsdatasets auszuführen.


## <a name="next-steps"></a>Nächste Schritte

Siehe die [Sammlung der für Azure Machine Learning verfügbaren Komponenten](component-reference.md). 
