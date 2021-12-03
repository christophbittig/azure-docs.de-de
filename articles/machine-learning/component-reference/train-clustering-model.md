---
title: 'Trainieren eines Clusteringmodells: Komponentenreferenz'
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie die Komponente „Train Clustering Model“ (Clusteringmodell trainieren) in Azure Machine Learning verwenden, um Clusteringmodelle zu trainieren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/17/2021
ms.openlocfilehash: 16d20e71dba5c83093ec572aad4910ec9a89ba06
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566566"
---
# <a name="train-clustering-model"></a>Trainieren eines Clusteringmodells

Dieser Artikel beschreibt eine Komponente im Azure Machine Learning-Designer.

Verwenden Sie diese Komponente, um ein Clusteringmodell zu trainieren.

In dieser Komponente wird ein untrainiertes Clusteringmodell verwendet, das Sie bereits mithilfe der Komponente [K-Means Clustering](k-means-clustering.md) konfiguriert haben. Die Komponente trainiert das Modell mit einem bezeichneten oder nicht bezeichneten Dataset. Die Komponente erstellt sowohl ein trainiertes Modell, das Sie für Vorhersagen verwenden können, als auch eine Reihe von Clusterzuweisungen für jeden Fall in den Trainingsdaten.

> [!NOTE]
> Ein Clusteringmodell kann nicht mit der Komponente [Train Model](train-model.md) (Modell trainieren) trainiert werden, bei der es sich um die generische Komponente zum Training von Machine Learning-Modellen handelt. Das liegt daran, dass [Train Model](train-model.md) nur mit überwachten Lernalgorithmen funktioniert. k-Means- und andere Clusteringalgorithmen ermöglichen unüberwachtes Lernen, was bedeutet, dass der Algorithmus aus unbezeichneten Daten lernen kann.  
  
## <a name="how-to-use-train-clustering-model"></a>Verwenden des Moduls „Train Clustering Model“  

1.  Fügen Sie die Komponente **Train Clustering Model** (Clusteringmodell trainieren) Ihrer Pipeline im Designer hinzu. Sie finden die Komponente unter den **Machine Learning-Komponenten** in der Kategorie **Train** (Trainieren).  
  
2. Fügen Sie die Komponente [K-Means Clustering](k-means-clustering.md) oder eine andere benutzerdefinierte Komponente hinzu, mit der ein kompatibles Clusteringmodell erstellt wird, und legen Sie dessen Parameter fest.  
    
3.  Fügen Sie das Trainingsdataset an die rechte Eingabe von **Train Clustering Model** an.
  
5.  Wählen Sie unter **Column Set** (Spaltengruppe) die Spalten aus dem Dataset aus, die zum Erstellen von Clustern verwendet werden sollen. Achten Sie darauf, Spalten auszuwählen, die gute Features bieten. Vermeiden Sie z.B. die Verwendung von IDs oder anderen Spalten mit eindeutigen oder identischen Werten.

    Wenn eine Bezeichnung verfügbar ist, können Sie sie entweder als Feature verwenden oder weglassen.  
  
6. Aktivieren Sie die Option **Check for Append or Uncheck for Result Only** (Für Anfügen aktivieren oder für reines Ergebnis deaktivieren), wenn Sie die Trainingsdaten zusammen mit der neuen Clusterbezeichnung ausgeben möchten.

    Wenn Sie diese Option deaktivieren, werden nur die Clusterzuweisungen ausgegeben. 

7. Übermitteln Sie die Pipeline, oder klicken Sie auf die Komponente **Train Clustering Model** (Clusteringmodell trainieren) und dann auf **Auswahl ausführen**.  
  
### <a name="results"></a>Ergebnisse

Nach Abschluss des Trainings:

+ Um eine Momentaufnahme des trainierten Modells zu speichern, wählen Sie die Registerkarte **Ausgaben** im rechten Bereich der Komponente **Train model** (Modell trainieren) aus. Wählen Sie das Symbol **Dataset registrieren** aus, um das Modell als wiederverwendbare Komponente zu speichern.

+ Verwenden Sie zum Generieren von Ergebnissen aus dem Modell [Assign Data to Clusters](assign-data-to-clusters.md) (Zuweisen von Daten zu Clustern).

> [!NOTE]
> Wenn Sie das trainierte Modell im Designer bereitstellen müssen, vergewissern Sie sich, dass in der Rückschlusspipeline die Option [Assign Data to Clusters](assign-data-to-clusters.md) (Clustern Daten zuweisen) anstelle von **Score Model** (Modell bewerten) mit der Eingabe der Komponente [Web Service Output](web-service-input-output.md) (Webdienstausgabe) verbunden ist.

## <a name="next-steps"></a>Nächste Schritte

Siehe die [Sammlung der für Azure Machine Learning verfügbaren Komponenten](component-reference.md). 