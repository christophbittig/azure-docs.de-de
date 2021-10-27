---
title: Vorbereiten von Daten für Aufgaben zum maschinellen Sehen
titleSuffix: Azure Machine Learning
description: Vorbereitung von Bilddaten für Azure Machine Learning AutoML (automatisiertes maschinelles Lernen) zum Trainieren von Modellen für maschinelles Sehen zur Klassifizierung, Objekterkennung und Segmentierung
author: vadthyavath
ms.author: rvadthyavath
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: template-how-to
ms.date: 10/13/2021
ms.openlocfilehash: 7f2f3cf3c0af623c6c33e46fe9735a2af03c14c6
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007265"
---
# <a name="prepare-data-for-computer-vision-tasks-with-automated-machine-learning-preview"></a>Vorbereiten von Daten für Aufgaben zum maschinellen Sehen mit automatisiertem ML (Vorschauversion)

> [!IMPORTANT]
> Die Unterstützung für das Trainieren von Modellen für maschinelles Sehen mit automatisiertem ML in Azure Machine Learning ist ein experimentelles Feature, das sich in der öffentlichen Vorschau befindet. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

In diesem Artikel erfahren Sie, wie Sie Bilddaten für das Trainieren von Modellen für maschinelles Sehen mit [automatisiertem maschinellem Lernen in Azure Machine Learning](concept-automated-ml.md) vorbereiten. 

Um Modelle für Aufgaben zum maschinellen Sehen mit automatisiertem maschinellem Lernen zu generieren, müssen Sie beschriftete Bilddaten als Eingabe für das Modelltraining in Form eines [Azure Machine Learning-TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) verwenden. 

Um sicherzustellen, dass Ihr TabularDataset das akzeptierte Schema für die Nutzung in automatisiertem ML aufweist, können Sie das Datenbeschriftungstool von Azure Machine Learning oder ein Konvertierungsskript verwenden. 

## <a name="prerequisites"></a>Voraussetzungen

* Kenntnis der akzeptierten [Schemas für JSONL-Dateien für AutoML-Experimente für maschinelles Sehen](reference-automl-images-schema.md)

* Beschriftete Daten, die Sie zum Trainieren von Modellen für maschinelles Sehen mit automatisiertem ML verwenden möchten

## <a name="azure-machine-learning-data-labeling"></a>Azure Machine Learning-Datenbeschriftung

Wenn Sie über keine beschrifteten Daten verfügen, können Sie das [Datenbeschriftungstool](how-to-create-image-labeling-projects.md) von Azure Machine Learning verwenden, um Bilder manuell zu beschriften. Dieses Tool generiert automatisch die für das Training erforderlichen Daten im akzeptierten Format.

Es unterstützt Sie beim Erstellen, Verwalten und Überwachen von Datenbeschriftungsaufgaben für: 

+ Bildklassifizierung (mehrere Klassen und mehrere Beschriftungen)
+ Objekterkennung (Begrenzungsrahmen)
+ Instanzsegmentierung (Polygon)

Wenn Sie bereits über ein Datenbeschriftungsprojekt verfügen und diese Daten verwenden möchten, können Sie [Ihre beschrifteten Daten als Azure Machine Learning-TabularDataset exportieren](how-to-create-image-labeling-projects.md#export-the-labels), das dann direkt mit automatisiertem ML zum Trainieren von Modellen für maschinelles Sehen verwendet werden kann.

## <a name="use-conversion-scripts"></a>Verwenden von Konvertierungsskripts

Wenn Sie Daten in gängigen Datenformaten für das maschinelle Sehen beschriftet haben – beispielsweise VOC oder COCO –, stellen wir Skripts bereit, um JSONL-Dateien für Trainings- und Validierungsdaten zu generieren. Ausführliche Anweisungen und Skripts finden Sie in unseren [Notebooks](https://github.com/Azure/azureml-examples/tree/main/python-sdk/tutorials/automl-with-azureml).

Wenn Ihre Daten keines der zuvor erwähnten Formate aufweisen, können Sie ein eigenes Skript verwenden, um JSON Lines-Dateien basierend auf Schemas zu generieren, die unter [Schema für JSONL-Dateien für AutoML-Bildexperimente](reference-automl-images-schema.md) definiert sind.

Nachdem Ihre Datendateien in das akzeptierte JSONL-Format konvertiert wurden, können Sie sie in Ihr Speicherkonto in Azure hochladen. 

## <a name="upload-the-jsonl-file-and-images-to-storage"></a>Hochladen der JSONL-Datei und der Bilder in den Speicher

Um die Daten für ein automatisiertes ML-Training zu verwenden, laden Sie die Daten über einen [Datenspeicher](how-to-access-data.md) in Ihren [Azure Machine Learning-Arbeitsbereich](concept-workspace.md) hoch. Der Datenspeicher bietet Ihnen einen Mechanismus zum Hoch- und Herunterladen von Daten in den Azure-Speicher und zur Interaktion mit diesen Daten von Ihren Remotecomputezielen aus.

Laden Sie das gesamte übergeordnete Verzeichnis, das aus Bildern und JSONL-Dateien besteht, in den Standarddatenspeicher hoch, der bei der Erstellung des Arbeitsbereichs automatisch erstellt wird.  Dieser Datenspeicher stellt eine Verbindung mit dem standardmäßigen Azure Blob Storage-Container her, der im Rahmen der Arbeitsbereichserstellung erstellt wurde.

```python
# Retrieve default datastore that's automatically created when we setup a workspace
ds = ws.get_default_datastore()
ds.upload(src_dir='./fridgeObjects', target_path='fridgeObjects')
```
Sobald der Datenupload abgeschlossen ist, können Sie ein [Azure Machine Learning-TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) erstellen und für die zukünftige Verwendung als Eingabe für Ihre automatisierten ML-Experimente für Modelle für maschinelles Sehen in Ihrem Arbeitsbereich registrieren.

```python
from azureml.core import Dataset
from azureml.data import DataType

training_dataset_name = 'fridgeObjectsTrainingDataset'
# create training dataset
training_dataset = Dataset.Tabular.from_json_lines_files(path=ds.path("fridgeObjects/train_annotations.jsonl"),
                                                         set_column_types={"image_url": DataType.to_stream(ds.workspace)}
                                                        )
training_dataset = training_dataset.register( workspace=ws,name=training_dataset_name)

print("Training dataset name: " + training_dataset.name)
```

## <a name="next-steps"></a>Nächste Schritte

* [Trainieren von Modellen für maschinelles Sehen mit automatisiertem maschinellem Lernen](how-to-auto-train-image-models.md)
* [Trainieren eines kleinen Modells für die Objekterkennung mit automatisiertem maschinellem Lernen](how-to-use-automl-small-object-detect.md) 