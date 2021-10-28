---
title: Einrichten von AutoML für das maschinelle Sehen
titleSuffix: Azure Machine Learning
description: Richten Sie automatisiertes ML in Azure Machine Learning ein, um Modelle für maschinelles Sehen mit dem Python-SDK (Vorschauversion) für Azure Machine Learning zu trainieren.
services: machine-learning
author: swatig007
ms.author: swatig
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: automl
ms.date: 10/06/2021
ms.openlocfilehash: 312eea28371de6726bca5d1a89e305024e552dad
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130254363"
---
# <a name="set-up-automl-to-train-computer-vision-models-with-python-preview"></a>Einrichten von AutoML für das Trainieren von Modellen für maschinelles Sehen mit Python (Vorschauversion)

> [!IMPORTANT]
> Dieses Feature ist zurzeit als öffentliche Preview verfügbar. Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

In diesem Artikel erfahren Sie, wie Sie Modelle für maschinelles Sehen mit Bilddaten mit automatisiertem ML im [Python-SDK für Azure Machine Learning](/python/api/overview/azure/ml/) trainieren.

Automatisiertes ML unterstützt das Modelltraining für Aufgaben für maschinelles Sehen wie Bildklassifizierung, Objekterkennung und Instanzsegmentierung. Das Erstellen von AutoML-Modellen für Aufgaben des maschinellen Sehens wird derzeit über das Python-SDK für Azure Machine Learning unterstützt. Die resultierenden Versuchsausführungen, Modelle und Ergebnisse sind über die Benutzeroberfläche von Azure Machine Learning Studio zugänglich. [Erfahren Sie mehr über automatisiertes ML für Aufgaben des maschinellen Sehens auf Bilddaten](concept-automated-ml.md).

> [!NOTE]
> Aufgaben des automatisierten ML für maschinelles Sehen sind nur über das Python-SDK für Azure Machine Learning verfügbar. 

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure Machine Learning-Arbeitsbereich. Informationen zum Erstellen des Arbeitsbereichs finden Sie unter [Erstellen eines Azure Machine Learning-Arbeitsbereichs](how-to-manage-workspace.md).

* Installation des Azure Machine Learning Python SDK,
    zum Installieren des SDK können Sie wie folgt vorgehen: 
    * Erstellen Sie eine Compute-Instanz, wodurch das SDK automatisch installiert und für ML-Workflows vorkonfiguriert wird. Weitere Informationen finden Sie unter [Erstellen und Verwalten einer Azure Machine Learning-Compute-Instanz](how-to-create-manage-compute-instance.md).

    * [Installieren Sie das Paket `automl` selbst](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md#setup-using-a-local-conda-environment). Es enthält die [Standardinstallation](/python/api/overview/azure/ml/install#default-install) des SDK.
    
    > [!NOTE]
    > Nur Python 3.6 und 3.7 sind mit der Unterstützung für automatisiertes maschinelles Lernen für Aufgaben des maschinellen Sehens kompatibel. 

## <a name="select-your-task-type"></a>Auswählen des Aufgabentyps
Automatisiertes maschinelles Lernen für Bilder unterstützt die folgenden Aufgabentypen:


Aufgabentyp | AutoMLImage-Konfigurationssyntax
---|---
 Bildklassifizierung | `ImageTask.IMAGE_CLASSIFICATION`
Bildklassifizierung mit mehreren Beschriftungen | `ImageTask.IMAGE_CLASSIFICATION_MULTILABEL`
Bildobjekterkennung | `ImageTask.IMAGE_OBJECT_DETECTION`
Bildinstanzsegmentierung| `ImageTask.IMAGE_INSTANCE_SEGMENTATION`

Dieser Aufgabentyp ist ein erforderlicher Parameter und wird mithilfe des `task`-Parameters in `AutoMLImageConfig` übergeben. Zum Beispiel:

```python
from azureml.train.automl import AutoMLImageConfig
from azureml.automl.core.shared.constants import ImageTask
automl_image_config = AutoMLImageConfig(task=ImageTask.IMAGE_OBJECT_DETECTION)
```

## <a name="training-and-validation-data"></a>Trainings- und Überprüfungsdaten

Um Modelle für maschinelles Sehen zu erstellen, müssen Sie beschriftete Bilddaten als Eingabe für das Modelltraining in Form eines [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) von Azure Machine Learning bereitstellen. Sie können entweder ein `TabularDataset` verwenden, das Sie aus einem [Datenbeschriftungsprojekt exportiert](./how-to-create-image-labeling-projects.md#export-the-labels) haben, oder ein neues `TabularDataset` mit Ihren beschrifteten Trainingsdaten erstellen. 

Wenn Ihre Trainingsdaten in einem anderen Format vorliegen (z. B. Pascal VOC oder COCO), können Sie die in den Beispielnotebooks enthaltenen Hilfsskripts anwenden, um die Daten in JSONL zu konvertieren. Erfahren Sie mehr darüber, wie Sie [Daten für Aufgaben des maschinellen Sehens mit automatisiertem maschinellen Lernen vorbereiten](how-to-prepare-datasets-for-automl-images.md). 

> [!Warning]
> Die Erstellung von TabularDatasets wird nur unterstützt, indem das SDK verwendet wird, um Datasets aus Daten im JSONL-Format für diese Funktion zu erstellen. Das Erstellen des Datasets über die Benutzeroberfläche wird derzeit nicht unterstützt.


### <a name="jsonl-schema-samples"></a>JSONL-Schemabeispiele

Die Struktur von TabularDataset hängt von der Aufgabe ab. Bei den Aufgabentypen des maschinellen Sehens besteht sie aus den folgenden Feldern:

Feld| BESCHREIBUNG
---|---
`image_url`| Enthält den Dateipfad als StreamInfo-Objekt
`image_details`|Die Informationen der Bildmetadaten bestehen aus Höhe, Breite und Format. Dieses Feld ist optional und daher möglicherweise nicht vorhanden.
`label`| Eine JSON-Darstellung der Bildbeschriftung basierend auf dem Aufgabentyp.

Im Folgenden finden Sie eine JSONL-Beispieldatei für die Bildklassifizierung:

```python
{
      "image_url": "AmlDatastore://image_data/Image_01.png",
      "image_details":
      {
          "format": "png",
          "width": "2230px",
          "height": "4356px"
      },
      "label": "cat"
  }
  {
      "image_url": "AmlDatastore://image_data/Image_02.jpeg",
      "image_details":
      {
          "format": "jpeg",
          "width": "3456px",
          "height": "3467px"
      },
      "label": "dog"
  }
  ```

  Der folgende Code ist ein Beispiel für eine JSONL-Datei zur Objekterkennung:

  ```python
  {
      "image_url": "AmlDatastore://image_data/Image_01.png",
      "image_details":
      {
          "format": "png",
          "width": "2230px",
          "height": "4356px"
      },
      "label":
      {
          "label": "cat",
          "topX": "1",
          "topY": "0",
          "bottomX": "0",
          "bottomY": "1",
          "isCrowd": "true",
      }
  }
  {
      "image_url": "AmlDatastore://image_data/Image_02.png",
      "image_details":
      {
          "format": "jpeg",
          "width": "1230px",
          "height": "2356px"
      },
      "label":
      {
          "label": "dog",
          "topX": "0",
          "topY": "1",
          "bottomX": "0",
          "bottomY": "1",
          "isCrowd": "false",
      }
  }
  ```


### <a name="consume-data"></a>Nutzen von Daten

Sobald Ihre Daten im JSONL-Format vorliegen, können Sie mit dem folgenden Code ein TabularDataset erstellen:

```python
from azureml.core import Dataset

training_dataset = Dataset.Tabular.from_json_lines_files(
        path=ds.path('odFridgeObjects/odFridgeObjects.jsonl'),
        set_column_types={'image_url': DataType.to_stream(ds.workspace)})
training_dataset = training_dataset.register(workspace=ws, name=training_dataset_name)
```

Bei Aufgaben des maschinellen Sehens unterliegt die Größe der Trainings- oder Überprüfungsdaten keinerlei Beschränkungen durch das automatisierte maschinelle Lernen. Die maximale Größe des Datasets wird nur durch die Ebene hinter dem Dataset (d. h. den Blobspeicher) begrenzt. Es gibt keine Mindestanzahl von Bildern oder Bezeichnungen. Wir empfehlen jedoch, mit einem Minimum von 10-15 Stichproben pro Bezeichnung zu beginnen, um sicherzustellen, dass das Ausgabemodell ausreichend trainiert ist. Je höher die Gesamtzahl der Bezeichnungen/Klassen ist, desto mehr Stichproben benötigen Sie pro Bezeichnung.



Trainingsdaten sind erforderlich und werden mithilfe des `training_data`-Parameters übergeben. Sie können optional ein anderes TabularDataset als Überprüfungsdataset angeben, das Sie für Ihr Modell mit dem `validation_data`-Parameter der AutoMLImageConfig verwenden. Wenn kein Dataset für die Prüfung angegeben wird, werden standardmäßig 20 % der Trainingsdaten für die Prüfung verwendet, es sei denn, Sie übergeben ein `split_ratio`-Argument mit einem anderen Wert.

Zum Beispiel:

```python
from azureml.train.automl import AutoMLImageConfig
automl_image_config = AutoMLImageConfig(training_data=training_dataset)
```

## <a name="compute-to-run-experiment"></a>Computeziel zum Ausführen des Experiments

Stellen Sie ein [Computeziel](concept-azure-machine-learning-architecture.md#compute-targets) für automatisiertes ML zur Durchführung des Modelltrainings bereit. Automatisierte ML-Modelle für Aufgaben des maschinellen Sehens erfordern GPU-SKUs und unterstützen NC- und ND-Familien. Für ein schnelleres Training wird die NCsv3-Serie (mit v100-GPUs) empfohlen. Ein Computeziel mit einer VM-SKU für mehrere GPUs nutzt mehrere GPUs, um das Training ebenfalls zu beschleunigen. Wenn Sie ein Computeziel mit mehreren Knoten einrichten, können Sie außerdem das Training des Modells durch Parallelität bei der Abstimmung der Hyperparameter für Ihr Modell beschleunigen.

Das Computeziel ist ein obligatorischer Parameter und wird mithilfe des `compute_target`-Parameters von `AutoMLImageConfig` übergeben. Zum Beispiel:

```python
from azureml.train.automl import AutoMLImageConfig
automl_image_config = AutoMLImageConfig(compute_target=compute_target)
```

## <a name="configure-model-algorithms-and-hyperparameters"></a>Konfigurieren von Modellalgorithmen und Hyperparametern

Mit der Unterstützung für Aufgaben des maschinellen Sehens können Sie den Modellalgorithmus steuern und Hyperparameter bereinigen. Diese Modellalgorithmen und Hyperparameter werden als Parameterraum für den Sweep übergeben.

Der Modellalgorithmus ist erforderlich und wird als `model_name`-Parameter übergeben. Sie können entweder eine einzelne `model_name`-Instanz angeben oder zwischen mehreren Instanzen auswählen. Zusätzlich zur Steuerung des Modellalgorithmus können Sie auch Hyperparameter optimieren, die für das Modelltraining verwendet werden. Während viele der verfügbar gemachten Hyperparameter modellunabhängig sind, gibt es Instanzen, in denen Hyperparameter aufgaben- oder modellspezifisch sind.

### <a name="supported-model-algorithms"></a>Unterstützte Modellalgorithmen

In der folgenden Tabelle sind die unterstützten Modelle für jede Aufgabe des maschinellen Sehens zusammengefasst. 

Aufgabe |  Modellalgorithmen | Syntax des Zeichenfolgenliterals<br> ***`default_model`\**** bezeichnet mit \*
---|----------|----------
Bildklassifizierung<br> (mehrere Klassen und mehrere Bezeichnungen)| **MobileNet**: Einfache Modelle für mobile Anwendungen <br> **ResNet**: Verbleibende Netzwerke<br> **ResNeSt**: Netzwerke mit geteilter Aufmerksamkeit<br> **SE-ResNeXt50**: Squeeze-and-Excitation-Netzwerke<br> **ViT**: Vision-Transformer-Netzwerke| `mobilenetv2`   <br>`resnet18` <br>`resnet34` <br> `resnet50`  <br> `resnet101` <br> `resnet152`    <br> `resnest50` <br> `resnest101`  <br> `seresnext`  <br> `vits16r224` (klein) <br> **_`vitb16r224`\_*** (Basis) <br>`vitl16r224` (groß)|
Objekterkennung | **YOLOv5**: Einstufiges Objekterkennungsmodell   <br>  **Faster RCNN ResNet FPN**: Zweistufige Objekterkennungsmodelle  <br> **RetinaNet ResNet FPN**: Klassenungleichgewicht mit Fokusverlust beheben <br> <br>*Hinweis: YOLOv5-Modellgrößen finden Sie unter [`model_size`-Hyperparameter](#model-specific-hyperparameters).*| ***`yolov5`\**** <br> `fasterrcnn_resnet18_fpn` <br> `fasterrcnn_resnet34_fpn` <br> `fasterrcnn_resnet50_fpn` <br> `fasterrcnn_resnet101_fpn` <br> `fasterrcnn_resnet152_fpn` <br> `retinanet_resnet50_fpn` 
Instanzsegmentierung | **MaskRCNN ResNet FPN**| `maskrcnn_resnet18_fpn` <br> `maskrcnn_resnet34_fpn` <br> **_`maskrcnn_resnet50_fpn`\_***  <br> `maskrcnn_resnet101_fpn` <br> `maskrcnn_resnet152_fpn` <br>`maskrcnn_resnet50_fpn`


### <a name="model-agnostic-hyperparameters"></a>Modellunabhängige Hyperparameter

In der folgenden Tabelle werden die modellunabhängigen Hyperparameter beschrieben.

| Parametername | BESCHREIBUNG | Standard|
| ------------ | ------------- | ------------ |
| `number_of_epochs` | Anzahl der Trainingsepochen <br>Der Wert muss eine positive ganze Zahl sein. |  15 <br> (mit Ausnahme von `yolov5`: 30) |
| `training_batch_size` | Trainingsbatchgröße.<br> Der Wert muss eine positive ganze Zahl sein.  | Mehrere Klassen/mehrere Bezeichnungen: 78 <br>(mit Ausnahme von *vit-variants*: <br> `vits16r224`: 128 <br>`vitb16r224`: 48 <br>`vitl16r224`:10)<br><br>Objekterkennung: 2 <br>(mit Ausnahme von `yolov5`: 16) <br><br> Instanzsegmentierung: 2  <br> <br> *Hinweis: Die Standardwerte sind die größte Batchgröße, die für 12 GiB GPU-Arbeitsspeicher verwendet werden kann*.|
| `validation_batch_size` | Überprüfungsbatchgröße.<br> Der Wert muss eine positive ganze Zahl sein. | Mehrere Klassen/mehrere Bezeichnungen: 78 <br>(mit Ausnahme von *vit-variants*: <br> `vits16r224`: 128 <br>`vitb16r224`: 48 <br>`vitl16r224`:10)<br><br>Objekterkennung: 1 <br>(mit Ausnahme von `yolov5`: 16) <br><br> Instanzsegmentierung: 1  <br> <br> *Hinweis: Die Standardwerte sind die größte Batchgröße, die für 12 GiB GPU-Arbeitsspeicher verwendet werden kann*.|
| `grad_accumulation_step` | Gradientenakkumulation bedeutet, dass eine konfigurierte Anzahl von `grad_accumulation_step` durchgeführt wird, ohne die Modellgewichtungen zu aktualisieren, während die Gradienten dieser Schritte akkumuliert werden, und dann die akkumulierten Gradienten verwendet werden, um die Gewichtsaktualisierungen zu berechnen. <br> Der Wert muss eine positive ganze Zahl sein. | 1 |
| `early_stopping` | Logik zum frühzeitigen Beenden während des Trainings aktivieren. <br> Der Wert muss 0 oder 1 sein.| 1 |
| `early_stopping_patience` | Mindestanzahl von Epochen oder Validierungsauswertungen<br>ohne Verbesserung der primären Metrik, bevor die Ausführung abgebrochen wird.<br> Der Wert muss eine positive ganze Zahl sein. | 5 |
| `early_stopping_delay` | Mindestanzahl der Epochen oder Validierungsauswertungen, die abgewartet werden müssen,<br>bevor die Verbesserung der primären Metrik für ein frühzeitiges Beenden verfolgt wird.<br> Der Wert muss eine positive ganze Zahl sein. | 5 |
| `learning_rate` | Anfängliche Lernrate. <br>Muss ein Gleitkommawert im Bereich [0, 1] sein. | Mehrere Klassen: 0.01 <br>(mit Ausnahme von *vit-variants*: <br> `vits16r224`: 0.0125<br>`vitb16r224`: 0.0125<br>`vitl16r224`: 0.001) <br><br> Mehrere Bezeichnungen: 0.035 <br>(mit Ausnahme von *vit-variants*:<br>`vits16r224`: 0.025<br>`vitb16r224`: 0.025 <br>`vitl16r224`: 0.002) <br><br> Objekterkennung: 0.005 <br>(mit Ausnahme von `yolov5`: 0.01) <br><br> Instanzsegmentierung: 0.005  |
| `lr_scheduler` | Typ des Lernratenplaners. <br> Muss `warmup_cosine` oder `step` sein. | `warmup_cosine` |
| `step_lr_gamma` | Der Wert von Gamma, wenn der Lernratenplaner `step` ist.<br> Muss ein Gleitkommawert im Bereich [0, 1] sein. | 0.5 |
| `step_lr_step_size` | Der Wert der Schrittgröße, wenn der Lernratenplaner `step` ist.<br> Der Wert muss eine positive ganze Zahl sein. | 5 |
| `warmup_cosine_lr_cycles` | Der Wert des Kosinuszyklus, wenn der Lernratenplaner `warmup_cosine` ist. <br> Muss ein Gleitkommawert im Bereich [0, 1] sein. | 0.45 |
| `warmup_cosine_lr_warmup_epochs` | Der Wert der Aufwärmepochen, wenn der Lernratenplaner `warmup_cosine` ist. <br> Der Wert muss eine positive ganze Zahl sein. | 2 |
| `optimizer` | Typ des Optimierers. <br> Muss entweder `sgd`, `adam` oder `adamw` sein.  | `sgd` |
| `momentum` | Der Wert der Dynamik, wenn der Optimierer `sgd` ist. <br> Muss ein Gleitkommawert im Bereich [0, 1] sein. | 0.9 |
| `weight_decay` | Der Wert des Gewichtungsverfalls, wenn der Optimierer `sgd`, `adam` oder `adamw` ist. <br> Muss ein Gleitkommawert im Bereich [0, 1] sein. | 1e-4 |
|`nesterov`| Aktivieren Sie `nesterov`, wenn der Optimierer `sgd` ist. <br> Der Wert muss 0 oder 1 sein.| 1 |
|`beta1` | Der Wert von `beta1`, wenn der Optimierer `adam` oder `adamw` ist. <br> Muss ein Gleitkommawert im Bereich [0, 1] sein. | 0.9 |
|`beta2` | Der Wert von `beta2`, wenn der Optimierer `adam` oder `adamw` ist.<br> Muss ein Gleitkommawert im Bereich [0, 1] sein. | 0.999 |
|`amsgrad` | Aktivieren Sie `amsgrad`, wenn der Optimierer `adam` oder `adamw` ist.<br> Der Wert muss 0 oder 1 sein. | 0 |
|`evaluation_frequency`| Häufigkeit zum Auswerten des Validierungsdatasets, um Metrikergebnisse zu erhalten. <br> Der Wert muss eine positive ganze Zahl sein. | 1 |
|`split_ratio`| Wenn keine Validierungsdaten definiert sind, wird hier das Aufteilungsverhältnis für die Aufteilung der Trainingsdaten in zufällige Trainings- und Validierungsteilmengen angegeben. <br> Muss ein Gleitkommawert im Bereich [0, 1] sein.| 0.2 |
|`checkpoint_frequency`| Häufigkeit zum Speichern von Modellprüfpunkten. <br> Der Wert muss eine positive ganze Zahl sein. | Prüfpunkt bei der Epoche mit der besten primären Metrik bei der Validierung.|
|`layers_to_freeze`| Wie viele Ebenen für Ihr Modell eingefroren werden. Die Übergabe von 2 als Wert für `seresnext` bedeutet z. B. das Einfrieren von Ebene0 und Ebene1 unter Bezugnahme auf die nachfolgend unterstützten Modellebenen. <br> Der Wert muss eine positive ganze Zahl sein. <br><br>`'resnet': [('conv1.', 'bn1.'), 'layer1.', 'layer2.', 'layer3.', 'layer4.'],`<br>`'mobilenetv2': ['features.0.', 'features.1.', 'features.2.', 'features.3.', 'features.4.', 'features.5.', 'features.6.', 'features.7.', 'features.8.', 'features.9.', 'features.10.', 'features.11.', 'features.12.', 'features.13.', 'features.14.', 'features.15.', 'features.16.', 'features.17.', 'features.18.'],`<br>`'seresnext': ['layer0.', 'layer1.', 'layer2.', 'layer3.', 'layer4.'],`<br>`'vit': ['patch_embed', 'blocks.0.', 'blocks.1.', 'blocks.2.', 'blocks.3.', 'blocks.4.', 'blocks.5.', 'blocks.6.','blocks.7.', 'blocks.8.', 'blocks.9.', 'blocks.10.', 'blocks.11.'],`<br>`'yolov5_backbone': ['model.0.', 'model.1.', 'model.2.', 'model.3.', 'model.4.','model.5.', 'model.6.', 'model.7.', 'model.8.', 'model.9.'],`<br>`'resnet_backbone': ['backbone.body.conv1.', 'backbone.body.layer1.', 'backbone.body.layer2.','backbone.body.layer3.', 'backbone.body.layer4.']` | Kein Standard  |


### <a name="task-specific-hyperparameters"></a>Aufgabenspezifische Hyperparameter

In der folgenden Tabelle sind Hyperparmeter für Bildklassifizierungsaufgaben (mit mehreren Klassen und mehreren Bezeichnungen) zusammengefasst.


| Parametername       | BESCHREIBUNG           | Standard  |
| ------------- |-------------|-----|
| `weighted_loss` | 0 für keinen gewichteten Verlust.<br>1 für gewichteten Verlust mit sqrt (class_weights). <br> 2 für gewichteten Verlust mit class_weights. <br> Muss 0, 1 oder 2 sein. | 0 |
| `valid_resize_size` | Bildgröße, auf die die Größe vor dem Zuschneiden für das Validierungsdataset geändert werden soll. <br> Der Wert muss eine positive ganze Zahl sein. <br> <br> *Hinweise: <li> `seresnext` nimmt keine beliebige Größe an. <li> Bei der Trainingsausführung kann es zu CUDA OOM kommen, wenn die Größe zu groß ist*.  | 256  |
| `valid_crop_size` | Größe des Bildausschnitts, der in Ihr neuronales Netzwerk für das Validierungsdataset eingegeben wird.  <br> Der Wert muss eine positive ganze Zahl sein. <br> <br> *Hinweise: <li> `seresnext` nimmt keine beliebige Größe an. <li> *ViT-Varianten* sollten dieselben Werte für `valid_crop_size` und `train_crop_size` aufweisen. <li> Bei der Trainingsausführung kann es zu CUDA OOM kommen, wenn die Größe zu groß ist*. | 224 |
| `train_crop_size` | Größe des Bildausschnitts, der in Ihr neuronales Netzwerk für das Trainingsdataset eingegeben wird.  <br> Der Wert muss eine positive ganze Zahl sein. <br> <br> *Hinweise: <li> `seresnext` nimmt keine beliebige Größe an. <li> *ViT-Varianten* sollten dieselben Werte für `valid_crop_size` und `train_crop_size` aufweisen. <li> Bei der Trainingsausführung kann es zu CUDA OOM kommen, wenn die Größe zu groß ist*. | 224 |


Die folgenden Hyperparameter sind für Aufgaben der Objekterkennung und der Instanzsegmentierung bestimmt.

> [!Warning]
> Diese Parameter werden von dem `yolov5`-Algorithmus nicht unterstützt.

| Parametername       | BESCHREIBUNG           | Standard  |
| ------------- |-------------|-----|
| `validation_metric_type` | Metrikberechnungsmethode, die für Validierungsmetriken zu verwenden ist.  <br> Muss `none`, `coco`, `voc` oder `coco_voc` sein. | `voc` |
| `min_size` | Mindestgröße des Bilds, das neu skaliert werden soll, bevor es an den Backbone weitergeleitet wird. <br> Der Wert muss eine positive ganze Zahl sein. <br> <br> *Hinweis: Bei der Trainingsausführung kann es zu CUDA OOM kommen, wenn die Größe zu groß ist*.| 600 |
| `max_size` | Maximale Größe des Bilds, das neu skaliert werden soll, bevor es an den Backbone weitergeleitet wird. <br> Der Wert muss eine positive ganze Zahl sein.<br> <br> *Hinweis: Bei der Trainingsausführung kann es zu CUDA OOM kommen, wenn die Größe zu groß ist*. | 1333 |
| `box_score_thresh` | Geben Sie während des Rückschlusses nur Vorschläge mit einem Klassifizierungswert zurück, der größer als `box_score_thresh` ist. <br> Muss ein Gleitkommawert im Bereich [0, 1] sein.| 0,3 |
| `box_nms_thresh` | Schwellenwert für nicht maximale Unterdrückung (NMS) für den Vorhersagekopf. Wird während des Rückschlusses verwendet.  <br>Muss ein Gleitkommawert im Bereich [0, 1] sein. | 0.5 |
| `box_detections_per_img` | Maximale Anzahl von Erkennungen pro Bild für alle Klassen. <br> Der Wert muss eine positive ganze Zahl sein.| 100 |
| `tile_grid_size` | Die Rastergröße, die für die Kachelung der einzelnen Bilder verwendet werden soll. <br>*Hinweis: tile_grid_size darf nicht „None“ (Keine) sein, um die Logik der [Erkennung kleiner Objekte](how-to-use-automl-small-object-detect.md) zu aktivieren*<br> Ein Tupel mit zwei ganzen Zahlen, die als Zeichenfolge übergeben werden. Beispiel: --tile_grid_size "(3, 2)" | Kein Standardwert |
| `tile_overlap_ratio` | Überlappungsverhältnis zwischen angrenzenden Kacheln in den einzelnen Dimensionen. <br> Muss ein Gleitkommawert im Bereich [0, 1] sein. | 0,25 |
| `tile_predictions_nms_thresh` | Der IOU-Schwellenwert, der zum Ausführen von NMS beim Zusammenführen von Vorhersagen aus Kacheln und Bildern verwendet werden soll. Wird bei der Validierung bzw. beim Rückschließen verwendet. <br> Muss ein Gleitkommawert im Bereich [0, 1] sein. | 0,25 |

### <a name="model-specific-hyperparameters"></a>Modellspezifische Hyperparameter

In dieser Tabelle werden die für den `yolov5`-Algorithmus spezifischen Hyperparameter zusammengefasst.

| Parametername       | BESCHREIBUNG           | Standard  |
| ------------- |-------------|----|
| `validation_metric_type` | Metrikberechnungsmethode, die für Validierungsmetriken zu verwenden ist.  <br> Muss `none`, `coco`, `voc` oder `coco_voc` sein. | `voc` |
| `img_size` | Bildgröße für Training und Validierung. <br> Der Wert muss eine positive ganze Zahl sein. <br> <br> *Hinweis: Bei der Trainingsausführung kann es zu CUDA OOM kommen, wenn die Größe zu groß ist*. | 640 |
| `model_size` | Modellgröße. <br> Muss `small`, `medium`, `large` oder `xlarge` sein. <br><br> *Hinweis: Bei der Trainingsausführung kann es zu CUDA OOM kommen, wenn die Modellgröße zu groß ist*.  | `medium` |
| `multi_scale` | Aktivieren von Bildern mit mehreren Skalierungen durch variierende Bildgröße um +/- 50 % <br> Der Wert muss 0 oder 1 sein. <br> <br> *Hinweis: Bei der Trainingsausführung kann es zu CUDA OOM kommen, wenn nicht ausreichend GPU-Arbeitsspeicher verfügbar ist*. | 0 |
| `box_score_thresh` | Geben Sie während des Rückschlusses nur Vorschläge mit einem Wert zurück, der größer als `box_score_thresh` ist. Die Punktzahl ergibt sich aus der Multiplikation von Objektivitätswert und Klassifizierungswahrscheinlichkeit. <br> Muss ein Gleitkommawert im Bereich [0, 1] sein. | 0,1 |
| `box_iou_thresh` | IoU-Schwellenwert, der während des Rückschlusses bei der Nachbearbeitung mit nicht maximaler Unterdrückung verwendet wird. <br> Muss ein Gleitkommawert im Bereich [0, 1] sein. | 0.5 |


### <a name="data-augmentation"></a>Datenerweiterung 

Im Allgemeinen kann die Leistung von Deep-Learning-Modellen mit mehr Daten verbessert werden. Die Datenerweiterung ist ein praktisches Verfahren zur Vergrößerung des Datenumfangs und der Variabilität eines Datasets, das dazu beiträgt, eine Überanpassung zu verhindern und die Generalisierungsfähigkeit des Modells auf unbekannte Daten zu verbessern. Automatisiertes ML wendet je nach Aufgabe des maschinellen Sehens verschiedene Verfahren zur Datenerweiterung an, bevor die Eingabebilder dem Modell zugeführt werden. Derzeit gibt es keine Hyperparameter zur Steuerung von Datenerweiterungen. 

|Aufgabe | Betroffenes Dataset | Angewendete Verfahren zur Datenerweiterung |
|-------|----------|---------|
|Bildklassifizierung (mehrere Klassen und mehrere Beschriftungen) | Training <br><br><br> Validierung und Test| Zufällige Größenänderung und Zuschnitt, horizontale Spiegelung, Farbjitter (Helligkeit, Kontrast, Sättigung und Farbton), Normalisierung mithilfe der mittleren und standardmäßigen Abweichung von ImageNet im Kanal <br><br><br>Größenänderung, zentraler Zuschnitt, Normalisierung |
|Objekterkennung, Instanzsegmentierung| Training <br><br> Validierung und Test |Zufälliger Zuschnitt um Begrenzungsrahmen, Erweiterung, horizontale Spiegelung, Normalisierung, Größenänderung <br><br><br>Normalisierung, Größenänderung
|Objekterkennung mit yolov5| Training <br><br> Validierung und Test  |Mosaik, zufällige Affinität (Rotation, Übersetzung, Skalierung, Neigung), horizontale Spiegelung <br><br><br> Größenänderung für Letterbox|

## <a name="configure-your-experiment-settings"></a>Konfigurieren der Experimenteinstellungen

Bevor Sie einen großen Sweep durchführen, um die optimalen Modelle und Hyperparameter zu finden, empfehlen wir Ihnen, die Standardwerte auszuprobieren, um eine erste Baseline zu erhalten. Als nächstes können Sie mehrere Hyperparameter für dasselbe Modell untersuchen, bevor Sie über mehrere Modelle und deren Parameter einen Sweep durchführen. Auf diese Weise können Sie einen iterativen Ansatz auswählen, denn bei mehreren Modellen und mehreren Hyperparametern für jedes Modell wächst der Suchraum exponentiell und Sie benötigen mehr Iterationen, um optimale Konfigurationen zu finden.

Wenn Sie die standardmäßigen Hyperparameterwerte für einen bestimmten Algorithmus (z. B. yolov5) verwenden möchten, können Sie die Konfiguration für Ihre AutoML-Bildausführungen wie folgt festlegen:

```python
from azureml.train.automl import AutoMLImageConfig
from azureml.train.hyperdrive import GridParameterSampling, choice
from azureml.automl.core.shared.constants import ImageTask

automl_image_config_yolov5 = AutoMLImageConfig(task=ImageTask.IMAGE_OBJECT_DETECTION,
                                               compute_target=compute_target,
                                               training_data=training_dataset,
                                               validation_data=validation_dataset,
                                               hyperparameter_sampling=GridParameterSampling({'model_name': choice('yolov5')}),
                                               iterations=1)
```

Sobald Sie ein Baselinemodell erstellt haben, möchten Sie vielleicht die Modellleistung optimieren, um einen Sweep über den Modellalgorithmus und den Hyperparameterraum durchzuführen. Sie können die folgende Beispielkonfiguration verwenden, um einen Sweep über die Hyperparameter für jeden Algorithmus durchzuführen und aus einer Reihe von Werten für learning_rate, optimizer, lr_scheduler usw. auszuwählen, um ein Modell mit der optimalen primären Metrik zu erstellen. Ohne Angabe von Hyperparameterwerten werden für den angegebenen Algorithmus Standardwerte verwendet.

### <a name="primary-metric"></a>Primary metric (Primäre Metrik)

Die primäre Metrik, die für die Modelloptimierung und die Abstimmung der Hyperparameter verwendet wird, hängt von der Art der Aufgabe ab. Die Verwendung anderer primärer Metrikwerte wird derzeit nicht unterstützt. 

* `accuracy` für IMAGE_CLASSIFICATION
* `iou` für IMAGE_CLASSIFICATION_MULTILABEL
* `mean_average_precision` für IMAGE_OBJECT_DETECTION
* `mean_average_precision` für IMAGE_INSTANCE_SEGMENTATION
    
### <a name="experiment-budget"></a>Experimentbudget

Sie können optional das maximale Zeitbudget für Ihr AutoML Vision-Experiment angeben, indem Sie `experiment_timeout_hours` verwenden – die Zeitspanne in Stunden, bevor das Experiment beendet wird. Ohne eine Angabe beträgt der Standardtimeout für Experimente sieben Tage (maximal 60 Tage).

## <a name="sweeping-hyperparameters-for-your-model"></a>Durchführen von Sweeps für Hyperparameter für Ihr Modell

Beim Training von Modellen für maschinelles Sehen hängt die Leistung des Modells stark von den gewählten Hyperparametern ab. Häufig möchten Sie vielleicht die Hyperparameter abstimmen, um eine optimale Leistung zu erzielen.
Mit der Unterstützung für Aufgaben des maschinellen Sehens in automatisiertem ML können Sie für Hyperparameter einen Sweep durchführen, um die optimalen Einstellungen für Ihr Modell zu finden. Dieses Feature wendet Abstimmungsfunktionen für Hyperparameter in Azure Machine Learning an. [Erfahren Sie, wie Sie Hyperparameter abstimmen](how-to-tune-hyperparameters.md).

### <a name="define-the-parameter-search-space"></a>Definieren des Suchbereichs für Parameter

Sie können die Modellalgorithmen und Hyperparameter definieren, die im Parameterraum durchsucht werden sollen. Unter [Konfigurieren von Modellalgorithmen und Hyperparametern](#configure-model-algorithms-and-hyperparameters) finden Sie die Liste der unterstützten Modellalgorithmen und Hyperparameter für jeden Aufgabentyp. Weitere Informationen finden Sie unter [Details zu unterstützten Verteilungen für diskrete und kontinuierliche Hyperparameter](how-to-tune-hyperparameters.md#define-the-search-space).


### <a name="sampling-methods-for-the-sweep"></a>Samplingmethoden für den Sweep

Beim Sweepen von Hyperparametern müssen Sie die Samplingmethode angeben, die für das Sweepen über den definierten Parameterraum verwendet werden soll. Derzeit werden die folgenden Samplingmethoden mit dem `hyperparameter_sampling`-Parameter unterstützt:

* [Zufallssampling](how-to-tune-hyperparameters.md#random-sampling)
* [Rastersampling](how-to-tune-hyperparameters.md#grid-sampling) 
* [Bayessches Sampling](how-to-tune-hyperparameters.md#bayesian-sampling) 
    
Es ist zu beachten, dass derzeit nur das zufällige Sampling bedingte Hyperparameterräume unterstützt.


### <a name="early-termination-policies"></a>Richtlinien zum vorzeitigen Beenden

Sie können Ausführungen mit schlechter Leistung mit einer Richtlinie für vorzeitige Beendigung automatisch beenden. Eine vorzeitige Beendigung verbessert die Effizienz der Berechnungen und spart Computeressourcen, die sonst für weniger vielversprechende Konfigurationen verwendet worden wären. Automatisiertes ML für Bilder unterstützt die folgenden Richtlinien zur vorzeitigen Beendigung mithilfe des `early_termination_policy`-Parameters. Wenn keine Richtlinie für die Beendigung angegeben wird, werden alle Konfigurationen bis zum Ende ausgeführt.

* [Banditenrichtlinie](how-to-tune-hyperparameters.md#bandit-policy)
* [Medianstopprichtlinie](how-to-tune-hyperparameters.md#median-stopping-policy)
* [Kürzungsauswahlrichtlinie](how-to-tune-hyperparameters.md#truncation-selection-policy)

Erfahren Sie mehr darüber, wie Sie die [Richtlinie für die vorzeitige Beendigung Ihres Hyperparameter-Sweeps konfigurieren](how-to-tune-hyperparameters.md#early-termination).

### <a name="resources-for-the-sweep"></a>Ressourcen für den Sweep

Sie können die für Ihren Hyperparameter-Sweep verwendeten Ressourcen steuern, indem Sie `iterations` und `max_concurrent_iterations` für den Sweep angeben.

Parameter | Detail
-----|----
`iterations` |  Erforderlicher Parameter für die maximale Anzahl der Konfigurationen, für die der Sweep durchgeführt wird. Muss eine ganze Zahl zwischen 1 und 1000 sein. Wenn Sie nur die Standardhyperparameter für einen bestimmten Modellalgorithmus untersuchen, legen Sie diesen Parameter auf 1 fest.
`max_concurrent_iterations`| Maximale Anzahl von Ausführungen, die gleichzeitig ausgeführt werden können. Wenn dieser Wert nicht angegeben wird, werden alle Ausführungen parallel gestartet. Wenn dieser Wert angegeben wird, muss es sich dabei um eine ganze Zahl zwischen 1 und 100 handeln.  <br><br> **HINWEIS:** Die Anzahl der gleichzeitigen Ausführungen wird durch die im angegebenen Computeziel verfügbaren Ressourcen beschränkt. Stellen Sie sicher, dass das Computeziel die verfügbaren Ressourcen für die gewünschte Parallelität aufweist.


> [!NOTE]
> Ein vollständiges Beispiel für die Sweep-Konfiguration finden Sie in diesem [Tutorial](tutorial-auto-train-image-models.md#hyperparameter-sweeping-for-image-tasks).

### <a name="arguments"></a>Argumente

Sie können feste Einstellungen oder Parameter, die sich während der Durchführung des Sweeps für den Parameterraum nicht ändern, als Argumente übergeben. Argumente werden in Name-Wert-Paaren übergeben, wobei dem Namen ein Doppelstrich vorangestellt werden muss. 

```python
from azureml.train.automl import AutoMLImageConfig
arguments = ["--early_stopping", 1, "--evaluation_frequency", 2]
automl_image_config = AutoMLImageConfig(arguments=arguments)
```

## <a name="submit-the-run"></a>Übermitteln der Ausführung

Wenn das `AutoMLImageConfig`-Objekt bereit ist, können Sie das Experiment übermitteln.

```python
ws = Workspace.from_config()
experiment = Experiment(ws, "Tutorial-automl-image-object-detection")
automl_image_run = experiment.submit(automl_image_config)
```
## <a name="outputs-and-evaluation-metrics"></a>Ausgaben und Auswertungsmetriken

Die AutoML-Trainingsausführungen generieren Ausgabemodelldateien, Bewertungsmetriken, Protokolle und Bereitstellungsartefakte wie die Bewertungsdatei und die Umgebungsdatei, die auf der Registerkarte für Ausgaben, Protokolle und Metriken der untergeordneten Ausführungen angezeigt werden können.

> [!TIP]
> Prüfen Sie im Abschnitt [Anzeigen von Ausführungsergebnissen](how-to-understand-automated-ml.md#view-run-results), wie Sie zu den Ausführungsergebnissen navigieren können.

Definitionen und Beispiele für die Leistungsdiagramme und Metriken, die für jede Ausführung bereitgestellt werden, finden Sie unter [Auswerten der Ergebnisse von Experimenten des automatisierten maschinellen Lernens](how-to-understand-automated-ml.md#metrics-for-image-models-preview).

## <a name="register-and-deploy-model"></a>Registrieren und Bereitstellen von Modellen

Nach Abschluss der Ausführung können Sie das Modell registrieren, das aus der besten Ausführung erstellt wurde (Konfiguration, die zur besten primären Metrik führte).

```Python
best_child_run = automl_image_run.get_best_child()
model_name = best_child_run.properties['model_name']
model = best_child_run.register_model(model_name = model_name, model_path='outputs/model.pt')
```

Nachdem Sie das Modell registriert haben, das Sie verwenden möchten, können Sie es als Webdienst in [Azure Container Instances (ACI)](how-to-deploy-azure-container-instance.md) oder [Azure Kubernetes Service (AKS)](how-to-deploy-azure-kubernetes-service.md) bereitstellen. ACI eignet sich perfekt zum Testen von Bereitstellungen, während AKS besser zur umfangreichen Nutzung in der Produktion geeignet ist.

In diesem Beispiel wird das Modell als Webdienst in AKS bereitgestellt. Erstellen Sie zum Bereitstellen in AKS zunächst einen AKS-Computecluster, oder verwenden Sie einen vorhandenen AKS-Cluster. Sie können entweder GPU- oder CPU-VM-SKUs für Ihren Bereitstellungscluster verwenden. 

```python

from azureml.core.compute import ComputeTarget, AksCompute
from azureml.exceptions import ComputeTargetException

# Choose a name for your cluster
aks_name = "cluster-aks-gpu"

# Check to see if the cluster already exists
try:
    aks_target = ComputeTarget(workspace=ws, name=aks_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    # Provision AKS cluster with GPU machine
    prov_config = AksCompute.provisioning_configuration(vm_size="STANDARD_NC6", 
                                                        location="eastus2")
    # Create the cluster
    aks_target = ComputeTarget.create(workspace=ws, 
                                      name=aks_name, 
                                      provisioning_configuration=prov_config)
    aks_target.wait_for_completion(show_output=True)
```

Als nächstes können Sie die Rückschlusskonfiguration definieren, um die Einrichtung des Webdiensts zu beschreiben, der Ihr Modell enthält. In Ihrer Rückschlusskonfiguration können Sie das Bewertungsskript und die Umgebung aus der Trainingsausführung verwenden.

```python
from azureml.core.model import InferenceConfig

best_child_run.download_file('outputs/scoring_file_v_1_0_0.py', output_file_path='score.py')
environment = best_child_run.get_environment()
inference_config = InferenceConfig(entry_script='score.py', environment=environment)
```

Anschließend können Sie das Modell als AKS-Webdienst bereitstellen.

```python
# Deploy the model from the best run as an AKS web service
from azureml.core.webservice import AksWebservice
from azureml.core.webservice import Webservice
from azureml.core.model import Model
from azureml.core.environment import Environment

aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True,                                                    
                                                cpu_cores=1,
                                                memory_gb=50,
                                                enable_app_insights=True)

aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target,
                           name='automl-image-test',
                           overwrite=True)
aks_service.wait_for_deployment(show_output=True)
print(aks_service.state)
```

Alternativ können Sie das Modell über die [Azure Machine Learning Studio-Benutzeroberfläche](https://ml.azure.com/) bereitstellen. Navigieren Sie auf der Registerkarte **Modelle** der automatisierten ML-Ausführung zu dem Modell, das Sie bereitstellen möchten, und wählen Sie die Option **Bereitstellen** aus.  

![Auswählen des Modells aus den AutoML-Ausführungen auf der Studio-Benutzeroberfläche  ](./media/how-to-auto-train-image-models/select-model.png)

Sie können den Namen des Modellbereitstellungsendpunkts und den Rückschlusscluster für Ihre Modellimplementierung im Bereich **Modell bereitstellen** konfigurieren.

![Bereitstellen der Konfiguration](./media/how-to-auto-train-image-models/deploy-image-model.png)

### <a name="update-inference-configuration"></a>Aktualisieren der Rückschlusskonfiguration

Im vorangegangenen Schritt haben wir die Bewertungsdatei `outputs/scoring_file_v_1_0_0.py` des besten Modells in eine lokale `score.py`-Datei heruntergeladen und sie zum Erstellen eines `InferenceConfig`-Objekts verwendet. Dieses Skript kann bei Bedarf geändert werden, um die modellspezifischen Einstellungen für den Rückschluss zu ändern, nachdem es heruntergeladen wurde und bevor die `InferenceConfig` erstellt wurde. Dies ist beispielsweise der Codeabschnitt, der das Modell in der Bewertungsdatei initialisiert:
    
```
...
def init():
    ...
    try:
        logger.info("Loading model from path: {}.".format(model_path))
        model_settings = {...}
        model = load_model(TASK_TYPE, model_path, **model_settings)
        logger.info("Loading successful.")
    except Exception as e:
        logging_utilities.log_traceback(e, logger)
        raise
...
```

Jede der Aufgaben (und einige Modelle) verfügt über einen Satz von Parametern im `model_settings`-Wörterbuch. Standardmäßig verwenden wir dieselben Werte für die Parameter, die während des Trainings und der Validierung verwendet wurden. Abhängig vom Verhalten, das beim Verwenden des Modells für Rückschlüsse benötigt wird, können wir diese Parameter ändern. Im Folgenden finden Sie eine Liste der Parameter für jeden Aufgabentyp und jedes Modell.  

| Aufgabe | Parametername | Standard  |
|--------- |------------- | --------- |
|Bildklassifizierung (mehrere Klassen und mehrere Beschriftungen) | `valid_resize_size`<br>`valid_crop_size` | 256<br>224 |
|Objekterkennung, Instanzsegmentierung| `min_size`<br>`max_size`<br>`box_score_thresh`<br>`box_nms_thresh`<br>`box_detections_per_img` | 600<br>1333<br>0,3<br>0.5<br>100 |
|Objekterkennung mit `yolov5`| `img_size`<br>`model_size`<br>`box_score_thresh`<br>`box_iou_thresh` | 640<br>mittel<br>0,1<br>0.5 |

Eine ausführliche Beschreibung dieser Parameter finden Sie im obigen Abschnitt zu [aufgabenspezifischen Hyperparametern](#task-specific-hyperparameters).
    
Wenn Sie Kacheln verwenden und das Kachelverhalten steuern möchten, sind die folgenden Parameter verfügbar: `tile_grid_size`, `tile_overlap_ratio` und `tile_predictions_nms_thresh`. Weitere Informationen zu diesen Parametern finden Sie unter [Trainieren eines Erkennungsmodells für kleine Objekte mit AutoML](how-to-use-automl-small-object-detect.md).

## <a name="example-notebooks"></a>Beispielnotebooks
Überprüfen Sie detaillierte Codebeispiele und Anwendungsfälle im [GitHub-Notebook-Repository für Beispiele zum automatisierten maschinellen Lernen](https://github.com/Azure/azureml-examples/tree/main/python-sdk/tutorials/automl-with-azureml). Überprüfen Sie die Ordner mit dem Präfix „image-“ auf spezifische Beispiele für die Erstellung von Modellen für maschinelles Sehen.


## <a name="next-steps"></a>Nächste Schritte

* [Tutorial: Trainieren eines Objekterkennungsmodells (Vorschauversion) mit AutoML und Python](tutorial-auto-train-image-models.md).
* [Behandeln Sie Probleme bei automatisierten ML-Experimenten.](how-to-troubleshoot-auto-ml.md)