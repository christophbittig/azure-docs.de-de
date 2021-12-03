---
title: 'Notenmodell: Komponente Referenz'
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie die Score Model-Komponente in Azure Machine Learning verwenden, um Vorhersagen mithilfe eines trainierten Klassifizierungs- oder Regressionsmodells zu erstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 83e466e7218b327bbd871c6693466a10744c9b79
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566402"
---
# <a name="score-model"></a>Bewerten eines Modells

Dieser Artikel beschreibt eine Komponente im Azure Machine Learning Designer.

Verwenden Sie diese Komponente, um Vorhersagen anhand eines trainierten Klassifizierungs- oder Regressionsmodells zu erstellen.

## <a name="how-to-use"></a>Verwendung

1. Fügen Sie die Komponente **Score Model** zu Ihrer Pipeline hinzu.

2. Fügen Sie ein trainiertes Modell und ein Dataset mit neuen Eingabedaten an. 

    Die Daten sollten in einem Format vorliegen, dass mit dem Typ des trainierten Modells kompatibel ist, das Sie verwenden. Das Schema des Eingabedatasets sollte auch generell mit dem Schema der zum Trainieren des Modells verwendeten Daten übereinstimmen.

3. Übermitteln Sie die Pipeline.

## <a name="results"></a>Ergebnisse

Nachdem Sie eine Reihe von Bewertungen mit dem [Bewertungsmodell](./score-model.md) generiert haben:

+ Um einen Satz von Metriken für die Auswertung der Modellgenauigkeit (Leistung) zu generieren, können Sie das bewertete Dataset mit dem [Bewertungsmodell](./evaluate-model.md) verbinden, indem Sie 
+ Klicken Sie mit der rechten Maustaste auf die Komponente und wählen Sie **Visualisieren**, um ein Beispiel für die Ergebnisse zu sehen.
<!-- + To Save the results to a dataset. -->

Das Ergebnis, oder der vorhergesagte Wert, kann je nach Modell und Eingabedaten in vielen verschiedenen Formaten vorliegen:

- Für Klassifizierungsmodelle gibt [Bewertungsmodell](./score-model.md) einen vorhergesagten Wert für die Klasse sowie die Wahrscheinlichkeit des vorhergesagten Werts aus.
- Für Regressionsmodelle generiert [Bewertungsmodell](./score-model.md) lediglich den vorhergesagten numerischen Wert.


## <a name="publish-scores-as-a-web-service"></a>Veröffentlichen eines Modells als Webdienst

Eine häufige Verwendung der Bewertung ist die Rückgabe der Ausgabe als Teil eines Vorhersagewebdiensts. Weitere Informationen finden Sie in [diesem Tutorial](../tutorial-designer-automobile-price-deploy.md) zum Bereitstellen eines Echtzeitendpunkts basierend auf einer Pipeline im Azure Machine Learning-Designer.

## <a name="next-steps"></a>Nächste Schritte

Siehe die [Sammlung der für Azure Machine Learning verfügbaren Komponenten](component-reference.md).