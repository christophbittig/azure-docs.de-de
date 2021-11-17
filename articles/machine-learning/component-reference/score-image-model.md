---
title: Verwenden der Komponente „Score Image Model“ (Bewerten eines Bildmodells)
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie die Komponente „Score Image Model“ in Azure Machine Learning verwenden können, um Vorhersagen mithilfe eines trainierten Bildmodells zu generieren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: 57cd0fb2b4c64010955f189b9cf28ced54883637
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566508"
---
# <a name="score-image-model"></a>Bewerten eines Bildmodells

In diesem Artikel wird eine Komponente im Azure Machine Learning-Designer beschrieben.

Verwenden Sie diese Komponente, um mithilfe eines trainierten Bildmodells Vorhersagen zu Eingabebilddaten zu generieren.

## <a name="how-to-configure-score-image-model"></a>Konfigurieren des Moduls „Score Image Model“ (Bildmodell bewerten)

1. Fügen Sie Ihrer Pipeline die Komponente **Score Image Model** hinzu.

2. Fügen Sie ein trainiertes Bildmodell und ein Dataset mit neuen Eingabebilddaten an. 

    Die Daten sollten den Typ „ImageDirectory“ aufweisen. Weitere Informationen zum Abrufen eines Bildverzeichnisses finden Sie im Artikel zur Komponente [Convert to Image Directory](convert-to-image-directory.md) (Konvertieren in ein Bildverzeichnis). Das Schema des Eingabedatasets sollte auch generell mit dem Schema der zum Trainieren des Modells verwendeten Daten übereinstimmen.

3. Übermitteln Sie die Pipeline.

## <a name="results"></a>Ergebnisse

Nachdem Sie mithilfe der Komponente [Score Image Model](score-image-model.md) eine Reihe von Bewertungen generiert haben, um Metriken zum Auswerten der Genauigkeit des Modells (Leistung) zu generieren, können Sie diese Komponente und das bewertete Dataset mit [Evaluate Model](evaluate-model.md) (Modell auswerten) verbinden. 

### <a name="publish-scores-as-a-web-service"></a>Veröffentlichen eines Modells als Webdienst

Eine häufige Verwendung der Bewertung ist die Rückgabe der Ausgabe als Teil eines Vorhersagewebdiensts. Weitere Informationen finden Sie in [diesem Tutorial](../tutorial-designer-automobile-price-deploy.md) zum Bereitstellen eines Echtzeitendpunkts basierend auf einer Pipeline im Azure Machine Learning-Designer.

## <a name="next-steps"></a>Nächste Schritte

Hier finden Sie die für Azure Machine Learning [verfügbaren Komponenten](component-reference.md).