---
title: Verwenden von automatisiertem ML zum Erkennen kleiner Objekte in Bildern
description: Richten Sie das automatisierte ML in Azure Machine Learning zum Trainieren von Erkennungsmodellen für kleine Objekte ein.
author: PhaniShekhar
ms.author: phmantri
ms.service: machine-learning
ms.subservice: automl
ms.topic: how-to
ms.date: 10/13/2021
ms.openlocfilehash: 2fb5dfc77a15e1f9fcd5b90a058e2cc03f81b910
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007326"
---
# <a name="train-a-small-object-detection-model-with-automl-preview"></a>Trainieren eines Erkennungsmodells für kleine Objekte mit automatisiertem ML (Vorschau)

> [!IMPORTANT]
> Dieses Feature ist zurzeit als öffentliche Preview verfügbar. Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

In diesem Artikel erfahren Sie, wie Sie ein Objekterkennungsmodell trainieren, um kleine Objekte in Bildern mit hoher Auflösung mit [automatisiertem ML](concept-automated-ml.md) in Azure Machine Learning zu erkennen.

In der Regel funktionieren Modelle des maschinellen Sehens zur Objekterkennung gut für Datasets mit relativ großen Objekten. Aufgrund von Arbeitsspeicher- und Berechnungseinschränkungen sind diese Modelle jedoch tendenziell unzureichend, wenn es um die Erkennung kleiner Objekte in hochauflösenden Bildern geht. Da hochauflösende Bilder in der Regel groß sind, werden sie vor der Eingabe in das Modell verkleinert, was ihre Fähigkeit, kleinere Objekte zu erkennen, im Verhältnis zur ursprünglichen Bildgröße einschränkt.

Um dieses Problem zu lösen, unterstützt das automatisierte ML die Kachelung als Teil der öffentlichen Vorschaufunktionen für maschinelles Sehen. Die Kachelungsfunktion beim automatisierten ML basiert auf den Konzepten unter [The Power of Tiling for Small Object Detection](https://openaccess.thecvf.com/content_CVPRW_2019/papers/UAVision/Unel_The_Power_of_Tiling_for_Small_Object_Detection_CVPRW_2019_paper.pdf) (Die Leistungsfähigkeit der Kachelung für die Erkennung kleiner Objekte).

Bei der Kachelung wird jedes Bild in ein Raster von Kacheln unterteilt. Angrenzende Kacheln überlappen sich in Breite und Höhe. Die Kacheln werden, wie in der folgenden Abbildung gezeigt, aus dem Original zugeschnitten. 

![Kachelgenerierung](./media/how-to-use-automl-small-object-detect/tiles-generation.png)

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure Machine Learning-Arbeitsbereich. Informationen zum Erstellen des Arbeitsbereichs finden Sie unter [Erstellen eines Azure Machine Learning-Arbeitsbereichs](how-to-manage-workspace.md).

* Dieser Artikel setzt voraus, dass Sie mit der Konfiguration eines [automatisierten Experiments zum maschinellen Lernen für Aufgaben des maschinellen Sehens](how-to-auto-train-image-models.md) vertraut sind. 

[!INCLUDE [automl-sdk-version](../../includes/machine-learning-automl-sdk-version.md)]

## <a name="supported-models"></a>Unterstützte Modelle

Die Erkennung kleiner Objekte mithilfe der Kachelung wird derzeit für die folgenden Modelle unterstützt:

* fasterrcnn_resnet18_fpn
* fasterrcnn_resnet50_fpn
* fasterrcnn_resnet34_fpn
* fasterrcnn_resnet101_fpn
* fasterrcnn_resnet152_fpn
* retinanet_resnet50_fpn

## <a name="enable-tiling-during-training"></a>Aktivieren der Kachelung während des Trainings

Um die Kachelung zu aktivieren, können Sie den `tile_grid_size`-Parameter auf einen Wert wie (3, 2) festlegen, wobei 3 für die Anzahl der Kacheln in der Breite und 2 für die Anzahl der Kacheln in der Höhe steht. Wenn dieser Parameter auf (3, 2) festgelegt ist, wird jedes Bild in ein Raster von 3 x 2 Kacheln aufgeteilt. Jede Kachel überschneidet sich mit den angrenzenden Kacheln, sodass alle Objekte, die auf den Kachelrand fallen, vollständig in einer der Kacheln enthalten sind. Diese Überlappung kann durch den `tile_overlap_ratio`-Parameter gesteuert werden, der standardmäßig auf 25 % eingestellt ist.

Wenn die Kachelung aktiviert ist, werden das gesamte Bild und die daraus erzeugten Kacheln durch das Modell geleitet. Diese Bilder und Kacheln werden vor der Zuführung zum Modell entsprechend den Parametern `min_size` und `max_size` skaliert. Die Berechnungszeit erhöht sich proportional durch die Verarbeitung dieser zusätzlichen Daten. 

Wenn der `tile_grid_size`-Parameter z. B. (3, 2) ist, beträgt die Berechnungszeit ungefähr das Siebenfache im Vergleich zur deaktivierten Kachelung.

Sie können den Wert für `tile_grid_size` in Ihrem Hyperparameterraum als Zeichenfolge angeben.

```python
parameter_space = {
    'model_name': choice('fasterrcnn_resnet50_fpn'),
    'tile_grid_size': choice('(3, 2)'),
    ...
}
```

Der Wert des `tile_grid_size`-Parameters hängt von den Bildabmessungen und der Größe der Objekte im Bild ab. Eine größere Anzahl von Kacheln wäre z. B. hilfreich, wenn sich kleinere Objekte in den Bildern befinden.

Um den optimalen Wert für diesen Parameter für Ihr Dataset zu wählen, können Sie die Hyperparametersuche verwenden. Hierzu können Sie eine Auswahl von Werten für diesen Parameter in Ihrem Hyperparameterbereich angeben.

```python
parameter_space = {
    'model_name': choice('fasterrcnn_resnet50_fpn'),
    'tile_grid_size': choice('(2, 1)', '(3, 2)', '(5, 3)'),
    ...
}
```
## <a name="tiling-during-inference"></a>Kachelung während des Rückschließens

Wenn ein mit Kacheln trainiertes Modell bereitgestellt wird, erfolgt die Kachelung auch während des Rückschließens. Das automatisierte ML verwendet den `tile_grid_size`-Wert aus dem Training, um die Kacheln während des Rückschließens zu generieren. Das gesamte Bild und die entsprechenden Kacheln werden durch das Modell geleitet, und die Objektvorschläge werden zusammengeführt, um endgültige Vorhersagen zu erstellen, wie im folgenden Bild zu sehen.

![Zusammenführen von Objektvorschlägen](./media/how-to-use-automl-small-object-detect/tiles-merge.png)

> [!NOTE] 
> Es ist möglich, dass dasselbe Objekt von mehreren Kacheln aus erkannt wird. Um solche Duplikate zu entfernen, wird eine Duplikatserkennung durchgeführt.
>
> Die Erkennung von Duplikaten erfolgt, indem NMS auf die Vorschläge aus den Kacheln und dem Bild angewendet wird. Wenn sich mehrere Vorschläge überlappen, wird derjenige mit dem höchsten Score ausgewählt und die anderen werden als Duplikate verworfen. Zwei Vorschläge gelten als überlappend, wenn IoU (Intersection over Union) zwischen ihnen größer ist als der `tile_predictions_nms_thresh`-Parameter.

Sie haben auch die Möglichkeit, die Kachelung nur während des Rückschließens zu aktivieren, ohne sie im Training zu aktivieren. Legen Sie den `tile_grid_size`-Parameter daher nur für den Rückschluss, nicht für das Training fest. 

Dadurch kann die Leistung für einige Datasets verbessert werden, und es entstehen keine zusätzlichen Kosten, die durch die Kachelung zum Zeitpunkt des Trainings entstehen. 

## <a name="tiling-hyperparameters"></a>Hyperparameter für die Kachelung 

Im Folgenden sind die Parameter aufgeführt, die Sie zur Steuerung des Kachelungsfeatures verwenden können.

| Parametername    | BESCHREIBUNG   | Standard |
| --------------- |-------------| -------|
| `tile_grid_size` |  Die Rastergröße, die für die Kachelung der einzelnen Bilder verwendet werden soll. Kann beim Training, bei der Validierung und beim Rückschließen verwendet werden.<br><br>Ein Tupel mit zwei ganzen Zahlen, die als Zeichenfolge übergeben werden, z. B. `'(3, 2)'`.<br><br> *Hinweis: Die Einstellung dieses Parameters erhöht die Berechnungszeit proportional, da alle Kacheln und Bilder vom Modell verarbeitet werden.*| Kein Standardwert |
| `tile_overlap_ratio` | Steuert das Überlappungsverhältnis zwischen angrenzenden Kacheln in den einzelnen Dimensionen. Wenn die Objekte, die auf die Kachelgrenze fallen, zu groß sind, um vollständig in eine der Kacheln zu passen, erhöhen Sie den Wert dieses Parameters, sodass die Objekte in mindestens eine der Kacheln vollständig passen.<br> <br>  Muss ein Gleitkommawert in [0, 1] sein.| 0,25 |
| `tile_predictions_nms_thresh` | Der IoU-Schwellenwert (Intersection over Union), der verwendet wird, um die nicht maximale Unterdrückung (NMS) bei der Zusammenführung von Vorhersagen aus Kacheln und Bild durchzuführen. Verfügbar während der Validierung und des Rückschließens. Ändern Sie diesen Parameter, wenn in den endgültigen Vorhersagen mehrere Felder pro Objekt erkannt werden.  <br><br> Muss ein Gleitkommawert in [0, 1] sein. | 0,25 |


## <a name="example-notebooks"></a>Beispielnotebooks

Im [Beispielnotebook zur Objekterkennung](https://github.com/Azure/azureml-examples/tree/main/python-sdk/tutorials/automl-with-azureml/image-object-detection/auto-ml-image-object-detection.ipynb) finden Sie ausführliche Beispiele für die Einrichtung und das Training eines Objekterkennungsmodells.

>[!NOTE]
> Alle Bilder in diesem Artikel werden in Übereinstimmung mit dem Abschnitt über die erlaubte Verwendung des [MIT-Lizenzvertrags](https://choosealicense.com/licenses/mit/) zur Verfügung gestellt.  
> Copyright © 2020 Roboflow, Inc.

## <a name="next-steps"></a>Nächste Schritte

* Lernen Sie, [wie und wo Sie Modelle bereitstellen](how-to-deploy-and-where.md) können.
* Definitionen und Beispiele zu den Leistungsdiagrammen und -Metriken, die für jede Ausführung angegeben werden, finden Sie unter [Auswerten der Ergebnisse von Experimenten des automatisierten maschinellen Lernens](how-to-understand-automated-ml.md). 
