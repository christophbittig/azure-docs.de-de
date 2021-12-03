---
title: Lokales Rückschließen mithilfe von ONNX für AutoML-Images
titleSuffix: Azure Machine Learning
description: Verwenden Sie ONNX mit dem automatisierten maschinellen Lernen in Azure Machine Learning, um Vorhersagen zu Modellen für maschinelles Sehen zur Klassifizierung, Objekterkennung und Segmentierung zu treffen.
author: vadthyavath
ms.author: rvadthyavath
ms.service: machine-learning
ms.subservice: automl
ms.topic: how-to
ms.date: 10/18/2021
ms.openlocfilehash: a8eded57142bf4682a0b555136c30462e9cf08ad
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132060508"
---
# <a name="make-predictions-with-onnx-on-computer-vision-models-from-automl"></a>Treffen von Vorhersagen mit ONNX für Modelle für maschinelles Sehen über AutoML 

In diesem Artikel wird erläutert, wie Sie mithilfe von Open Neural Network Exchange (ONNX) Vorhersagen zu Modellen für maschinelles Sehen treffen, die über das automatisierte maschinelle Lernen (AutoML) in Azure Machine Learning generiert wurden. 

Um ONNX für Vorhersagen zu verwenden, ist Folgendes erforderlich: 
 
1. Laden Sie ONNX-Modelldateien aus einer AutoML-Trainingsausführung herunter.
1. Sie müssen die Eingaben und Ausgaben eines ONNX-Modells verstehen.
1. Sie müssen Ihre Daten vorverarbeiten, damit sie im erforderlichen Format für Eingabebilder vorliegen.
1. Führen Sie Rückschlüsse mit ONNX Runtime für Python aus.
1. Visualisieren Sie Vorhersagen für Objekterkennungs- und Segmentierungsaufgaben.

[ONNX](https://onnx.ai/about.html) ist ein offener Standard für Machine Learning- und Deep-Learning-Modelle. Das Format ermöglicht den Modellimport und -export (Interoperabilität) in den gängigen KI-Frameworks. Weitere Informationen finden Sie im [ONNX-GitHub-Projekt](https://github.com/onnx/onnx).

[ONNX Runtime](https://onnxruntime.ai/index.html) ist ein Open-Source-Projekt, das plattformübergreifende Rückschlüsse unterstützt. ONNX Runtime stellt APIs für verschiedene Programmiersprachen (einschließlich Python, C++, C#, C, Java und JavaScript) bereit. Sie können diese APIs verwenden, um Rückschlüsse auf Eingabebilder auszuführen. Nachdem Sie über das Modell verfügen, das in das ONNX-Format exportiert wurde, können Sie diese APIs in jeder Programmiersprache verwenden, die Ihr Projekt erfordert. 

In diesem Leitfaden wird erläutert, wie Sie [Python-APIs für ONNX Runtime](https://onnxruntime.ai/docs/get-started/with-python.html) verwenden, um Vorhersagen für Bilder für beliebte Aufgaben im Bereich des maschinellen Sehens zu treffen. Sie können diese exportierten ONNX-Modelle sprachübergreifend verwenden.

## <a name="prerequisites"></a>Voraussetzungen

* Laden Sie ein AutoML-trainiertes Modell für maschinelles Sehen für eine beliebige der unterstützten Bildaufgaben herunter: Klassifizierung, Objekterkennung oder Segmentierung. Informieren Sie sich weiter über das [Einrichten von AutoML für das Trainieren von Modellen für maschinelles Sehen mit Python (Vorschauversion)](how-to-auto-train-image-models.md).

* Installieren Sie das Paket [onnxruntime](https://onnxruntime.ai/docs/get-started/with-python.html). Die Methoden in diesem Artikel wurden mit den Versionen 1.3.0 bis 1.8.0 getestet.

## <a name="download-onnx-model-files"></a>Herunterladen von ONNX-Modelldateien

Sie können ONNX-Modelldateien aus AutoML-Ausführungen herunterladen, indem Sie die Azure Machine Learning Studio-Benutzeroberfläche oder das Python-SDK für Azure Machine Learning verwenden. Es wird empfohlen, den Download über das SDK mit dem Experimentnamen und der ID der übergeordneten Ausführung durchzuführen. 

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio

Wechseln Sie im Azure Machine Learning Studio zu Ihrem Experiment, indem Sie den Hyperlink zum Experiment verwenden, das im Trainingsnotebook generiert wurde. Alternativ können Sie auf der Registerkarte **Experimente** unter **Ressourcen** auf den Experimentnamen klicken. Wählen Sie dann die am besten geeignete untergeordnete Ausführung aus. 

Wechseln Sie innerhalb dieser untergeordnet Ausführung zu **Ausgaben + Protokolle** > **train_artifacts**. Klicken Sie auf die Schaltfläche **Download**, um die folgenden Dateien manuell herunterzuladen:

- *labels.json*: Diese Datei enthält alle Klassen oder Bezeichnungen im Trainingsdataset.
- *model.onnx*: Dies ist ein Modell im ONNX-Format. 

![Screenshot: Auswahl zum Herunterladen von ONNX-Modelldateien](./media/how-to-inference-onnx-automl-image-models/onnx-files-manual-download.png)

Speichern Sie die heruntergeladenen Modelldateien in einem Verzeichnis. Im Beispiel in diesem Artikel wird das Verzeichnis *./automl_models* verwendet. 

### <a name="azure-machine-learning-python-sdk"></a>Python-SDK für Azure Machine Learning

Mit dem SDK können Sie die am besten geeignete untergeordnete Ausführung (nach primärer Metrik) mit dem Experimentnamen und der ID der übergeordneten Ausführung auswählen. Anschließend können Sie die Dateien *labels.json* und *model.onnx* herunterladen.

Der folgende Code gibt die am besten geeignete untergeordnete Ausführung basierend auf der relevanten primären Metrik zurück.

```python
# Select the best child run
from azureml.train.automl.run import AutoMLRun

run_id = "" # Specify the run ID
automl_image_run = AutoMLRun(experiment=experiment, run_id=run_id)
best_child_run = automl_image_run.get_best_child()
```

Laden Sie die Datei *labels.json* herunter, die alle Klassen und Bezeichnungen im Trainingsdataset enthält.

```python
import json

labels_file = "automl_models/labels.json"
best_child_run.download_file(name="train_artifacts/labels.json", output_file_path=labels_file)

```

Laden Sie die Datei *model.onnx* herunter.

```python
onnx_model_path = "automl_models/model.onnx"
best_child_run.download_file(name="train_artifacts/model.onnx", output_file_path=onnx_model_path)
```

Nach dem Herunterladen des Modells verwenden Sie das ONNX Runtime-Python-Paket, um mithilfe der Datei *model.onnx* Rückschlüsse auszuführen. Zu Demonstrationszwecken werden in diesem Artikel die Datasets aus dem Artikel [Vorbereiten von Daten für Aufgaben zum maschinellen Sehen mit automatisiertem ML (Vorschauversion)](how-to-prepare-datasets-for-automl-images.md) für jede Aufgabe für maschinelles Sehen verwendet. 

Die Modelle wurden für alle Aufgaben für maschinelles Sehen mit ihren jeweiligen Datasets trainiert, um den ONNX-Modellrückschluss zu veranschaulichen.
 
## <a name="load-the-labels-and-onnx-model-files"></a>Laden der Bezeichnungen und ONNX-Modelldateien

Der folgende Codeausschnitt lädt *labels.json*, wobei Klassennamen geordnet sind. Das heißt, wenn das ONNX-Modell eine Bezeichnungs-ID als 2 vorhersagt, entspricht dies dem Bezeichnungsnamen, der am dritten Index in der Datei *labels.json* angegeben ist.

```python
import onnxruntime

labels_file = "automl_models/labels.json"
with open(labels_file) as f:
    classes = json.load(f)
print(classes)
try:
    session = onnxruntime.InferenceSession(onnx_model_path)
    print("ONNX model loaded...")
except Exception as e: 
    print("Error loading ONNX file: ",str(e))
```

## <a name="get-expected-input-and-output-details-for-an-onnx-model"></a>Abrufen der erwarteten Eingabe- und Ausgabedetails für ein ONNX-Modell

Wenn Sie über das Modell verfügen, ist es wichtig, einige modellspezifische und aufgabenspezifische Details zu kennen. Zu diesen Details gehören die Anzahl der Eingaben und Ausgaben, die erwartete Eingabeform oder das erwartete Format für die Vorverarbeitung des Bilds und die Ausgabeform, damit Sie die modellspezifischen oder aufgabenspezifischen Ausgaben kennen.

```python
sess_input = session.get_inputs()
sess_output = session.get_outputs()
print(f"No. of inputs : {len(sess_input)}, No. of outputs : {len(sess_output)}")

for idx, input_ in enumerate(range(len(sess_input))):
    input_name = sess_input[input_].name
    input_shape = sess_input[input_].shape
    input_type = sess_input[input_].type
    print(f"{idx} Input name : { input_name }, Input shape : {input_shape}, \
    Input type  : {input_type}")  

for idx, output in enumerate(range(len(sess_output))):
    output_name = sess_output[output].name
    output_shape = sess_output[output].shape
    output_type = sess_output[output].type
    print(f" {idx} Output name : {output_name}, Output shape : {output_shape}, \
    Output type  : {output_type}") 
``` 

### <a name="expected-input-and-output-formats-for-the-onnx-model"></a>Erwartete Eingabe- und Ausgabeformate für das ONNX-Modell

Jedes ONNX-Modell verfügt über vordefinierte Eingabe- und Ausgabeformate.

# <a name="multi-class-image-classification"></a>[Bildklassifizierung mit mehreren Klassen](#tab/multi-class)

In diesem Beispiel wird das Modell angewendet, das mit dem Dataset [fridgeObjects](https://cvbp-secondary.z19.web.core.windows.net/datasets/image_classification/fridgeObjects.zip) mit 134 Bildern und 4 Klassen bzw. Bezeichnungen trainiert wurde, um den ONNX-Modellrückschluss zu erklären. Weitere Informationen zum Trainieren einer Bildklassifizierungsaufgabe finden Sie im [Notebook für die Bildklassifizierung mit mehreren Klassen](https://github.com/Azure/azureml-examples/tree/81c7d33ed82f62f419472bc11f7e1bad448ff15b/python-sdk/tutorials/automl-with-azureml/image-classification-multiclass).

### <a name="input-format"></a>Eingabeformat
    
Bei der Eingabe handelt es sich um ein vorverarbeitetes Bild.

| Eingabename  | Eingabeform  | Eingabetyp | BESCHREIBUNG |
| -------- |----------:|-----|--------|
| input1 | `(batch_size, num_channels, height, width)` | ndarray(float) | Die Eingabe ist ein vorverarbeitetes Bild mit der Form `(1, 3, 224, 224)` für die Batchgröße 1 und einer Höhe und Breite von 224. Diese Zahlen entsprechen den Werten, die für `crop_size` im Trainingsbeispiel verwendet wurden. |
    

### <a name="output-format"></a>Ausgabeformat

Die Ausgabe ist ein Array von Logits für alle Klassen bzw. Bezeichnungen.
         
| Ausgabename   | Ausgabeform  | Ausgabetyp | BESCHREIBUNG |
| -------- |----------|-----|------|
| output1 | `(batch_size, num_classes)` | ndarray(float) | Das Modell gibt Logits (ohne `softmax`) zurück. Für die Klassen der Batchgrößen 1 und 4 wird beispielsweise `(1, 4)` zurückgegeben. |

# <a name="multi-label-image-classification"></a>[Bildklassifizierung mit mehreren Beschriftungen](#tab/multi-label)

In diesem Beispiel wird das Modell verwendet, das mit dem [fridgeObjects-Dataset mit mehreren Bezeichnungen](https://cvbp-secondary.z19.web.core.windows.net/datasets/image_classification/multilabelFridgeObjects.zip) mit 128 Bildern und 4 Klassen bzw. Bezeichnungen trainiert wurde, um den ONNX-Modellrückschluss zu erklären. Weitere Informationen zum Modelltraining für die Bildklassifizierung mit mehreren Bezeichnungen finden Sie im [Notebook für die Bildklassifizierung mit mehreren Bezeichnungen](https://github.com/Azure/azureml-examples/tree/81c7d33ed82f62f419472bc11f7e1bad448ff15b/python-sdk/tutorials/automl-with-azureml/image-classification-multilabel).

### <a name="input-format"></a>Eingabeformat

Die Eingabe ist ein vorverarbeitetes Bild.

| Eingabename       | Eingabeform  | Eingabetyp | BESCHREIBUNG |
| -------- |----------|-----|--------|
| input1 | `(batch_size, num_channels, height, width)` | ndarray(float) | Die Eingabe ist ein vorverarbeitetes Bild mit der Form `(1, 3, 224, 224)` für die Batchgröße 1 und einer Höhe und Breite von 224. Diese Zahlen entsprechen den Werten, die für `crop_size` im Trainingsbeispiel verwendet wurden. |
        
### <a name="output-format"></a>Ausgabeformat

Die Ausgabe ist ein Array von Logits für alle Klassen bzw. Bezeichnungen.
    
      
| Ausgabename       | Ausgabeform  | Ausgabetyp | BESCHREIBUNG |
| -------- |----------|-----|------
| output1 | `(batch_size, num_classes)` | ndarray(float) | Das Modell gibt Logits (ohne `sigmoid`) zurück. Für die Klassen der Batchgrößen 1 und 4 wird beispielsweise `(1, 4)` zurückgegeben. |


# <a name="object-detection-with-faster-r-cnn"></a>[Objekterkennung mit Faster R-CNN-Modellen](#tab/object-detect-cnn)

In diesem Objekterkennungsbeispiel wird das Modell verwendet, das mit dem [fridgeObjects-Erkennungsdataset](https://cvbp-secondary.z19.web.core.windows.net/datasets/object_detection/odFridgeObjects.zip) mit 128 Bildern und 4 Klassen bzw. Bezeichnungen trainiert wurde, um den ONNX-Modellrückschluss zu erklären. In diesem Beispiel werden Faster R-CNN-Modelle trainiert, um Rückschlussschritte zu veranschaulichen. Weitere Informationen zum Trainieren von Objekterkennungsmodellen finden Sie im [Objekterkennungsnotebook](https://github.com/Azure/azureml-examples/tree/81c7d33ed82f62f419472bc11f7e1bad448ff15b/python-sdk/tutorials/automl-with-azureml/image-object-detection).

### <a name="input-format"></a>Eingabeformat

Die Eingabe ist ein vorverarbeitetes Bild.

| Eingabename       | Eingabeform  | Eingabetyp | BESCHREIBUNG |
| -------- |----------|-----|--------|
| Eingabe | `(batch_size, num_channels, height, width)` | ndarray(float) | Die Eingabe ist ein vorverarbeitetes Bild mit der Form `(1, 3, 600, 800)` für die Batchgröße 1 und einer Höhe von 600 und einer Breite von 800.|
        
    
### <a name="output-format"></a>Ausgabeformat

Die Ausgabe ist ein Tupel aus Rahmen, Bezeichnungen und Scores.
  
| Ausgabename       | Ausgabeform  | Ausgabetyp | BESCHREIBUNG |
| -------- |----------|-----|------|
| Rahmen | `(n_boxes, 4)`, wobei für jeden Rahmen `x_min, y_min, x_max, y_max` angegeben ist | ndarray(float) | Das Modell gibt *n* Rahmen mit den zugehörigen Koordinaten oben links und unten rechts zurück. |
| Bezeichnungen | `(n_boxes)`| ndarray(float) | Bezeichnungs- oder Klassen-ID eines Objekts in jedem Rahmen |  
| Scores | `(n_boxes)` | ndarray(float) | Konfidenzscore eines Objekts in jedem Rahmen |    


# <a name="object-detection-with-yolo"></a>[Objekterkennung mit YOLO](#tab/object-detect-yolo)

In diesem Objekterkennungsbeispiel wird das Modell verwendet, das mit dem [fridgeObjects-Erkennungsdataset](https://cvbp-secondary.z19.web.core.windows.net/datasets/object_detection/odFridgeObjects.zip) mit 128 Bildern und 4 Klassen bzw. Bezeichnungen trainiert wurde, um den ONNX-Modellrückschluss zu erklären. In diesem Beispiel werden YOLO-Modelle trainiert, um Rückschlussschritte zu veranschaulichen. Weitere Informationen zum Trainieren von Objekterkennungsmodellen finden Sie im [Objekterkennungsnotebook](https://github.com/Azure/azureml-examples/tree/81c7d33ed82f62f419472bc11f7e1bad448ff15b/python-sdk/tutorials/automl-with-azureml/image-object-detection). 

### <a name="input-format"></a>Eingabeformat

Die Eingabe ist ein vorverarbeitetes Bild mit der Form `(1, 3, 640, 640)` für die Batchgröße 1 und einer Höhe und Breite von 640. Diese Zahlen entsprechen den Werten, die im Trainingsbeispiel verwendet wurden.        

| Eingabename       | Eingabeform  | Eingabetyp | BESCHREIBUNG |
| -------- |----------|-----|--------|
| Eingabe | `(batch_size, num_channels, height, width)` | ndarray(float) | Die Eingabe ist ein vorverarbeitetes Bild mit der Form `(1, 3, 600, 800)` für die Batchgröße 1 und einer Höhe von 600 und einer Breite von 800.|
        
### <a name="output-format"></a>Ausgabeformat
Die Ausgabe ist eine Liste von Rahmen, Bezeichnungen und Scores. Für YOLO benötigen Sie die erste Ausgabe, um Rahmen, Bezeichnungen und Scores zu extrahieren.
    
| Ausgabename       | Ausgabeform  | Ausgabetyp | BESCHREIBUNG |
| -------- |----------|-----|------|
| Output | `(n_boxes, 6)`, wobei für jeden Rahmen `x_min, y_min, x_max, y_max, confidence_score, class_id` angegeben ist | ndarray(float) | Das Modell gibt *n* Rahmen mit den zugehörigen Koordinaten oben links und unten rechts sowie Objektkonfidenzscores, Klassen-IDs oder Bezeichnungs-IDs zurück. |

# <a name="instance-segmentation"></a>[Instanzsegmentierung](#tab/instance-segmentation)

In diesem Beispiel für die Instanzsegmentierung verwenden Sie das Mask R-CNN-Modell, das mit dem [fridgeObjects-Dataset](https://cvbp-secondary.z19.web.core.windows.net/datasets/object_detection/odFridgeObjectsMask.zip) mit 128 Bilder und 4 Klassen bzw. Bezeichnungen trainiert wurde, um den ONNX-Modellrückschluss zu erklären. Weitere Informationen zum Training des Instanzsegmentierungsmodells finden Sie im [Notebook zur Instanzsegmentierung](https://github.com/Azure/azureml-examples/tree/81c7d33ed82f62f419472bc11f7e1bad448ff15b/python-sdk/tutorials/automl-with-azureml/image-instance-segmentation).

>[!IMPORTANT]
> Nur Mask R-CNN wird für Instanzsegmentierungsaufgaben unterstützt. Die Ein- und Ausgabeformate basieren nur auf Mask R-CNN.

### <a name="input-format"></a>Eingabeformat

Bei der Eingabe handelt es sich um ein vorverarbeitetes Bild. Das ONNX-Modell für Mask R-CNN wurde exportiert, um für Bilder mit verschiedenen Formen verwendet zu werden. Es wird empfohlen, für eine bessere Leistung die Größe in eine feste Größe zu ändern, die mit den Trainingsbildgrößen konsistent ist.
    
| Eingabename       | Eingabeform  | Eingabetyp | BESCHREIBUNG |
| -------- |----------|-----|--------|
| Eingabe | `(batch_size, num_channels, height, width)` | ndarray(float) | Die Eingabe ist ein vorverarbeitetes Bild mit der Form `(1, 3, input_image_height, input_image_width)` für die Batchgröße 1 und der Höhe und Breite eines Eingabebilds. |
        
    
### <a name="output-format"></a>Ausgabeformat

Die Ausgabe ist ein Tupel aus Rahmen (Instanzen), Bezeichnungen und Scores.
    
| Ausgabename       | Ausgabeform  | Ausgabetyp | BESCHREIBUNG |
| -------- |----------|-----|------|
| Rahmen | `(n_boxes, 4)`, wobei für jeden Rahmen `x_min, y_min, x_max, y_max` angegeben ist | ndarray(float) | Das Modell gibt *n* Rahmen mit den zugehörigen Koordinaten oben links und unten rechts zurück. |
| Bezeichnungen | `(n_boxes)`| ndarray(float) | Bezeichnungs- oder Klassen-ID eines Objekts in jedem Rahmen |  
| Scores | `(n_boxes)` | ndarray(float) | Konfidenzscore eines Objekts in jedem Rahmen |    
| Masken | `(n_boxes, 1, height, width)` | ndarray(float) | Masken (Polygone) erkannter Objekte mit der Formhöhe und -breite eines Eingabebilds |    

---

## <a name="preprocessing"></a>Preprocessing (Vorverarbeitung)

# <a name="multi-class-image-classification"></a>[Bildklassifizierung mit mehreren Klassen](#tab/multi-class)

Führen Sie die folgenden Vorverarbeitungsschritte für den ONNX-Modellrückschluss aus:

1. Konvertieren Sie das Bild in RGB.
2. Ändern Sie die Größe des Bilds in die Werte `valid_resize_size` und `valid_resize_size`, die den Werten entsprechen, die bei der Transformation des Validierungsdatasets während des Trainings verwendet wurden. Der Standardwert für `valid_resize_size` lautet 256.
3. Schneiden Sie das Bild zentriert auf `height_onnx_crop_size` und `width_onnx_crop_size` zu. Dies entspricht `valid_crop_size` mit dem Standardwert 224.
4. Ändern Sie `HxWxC` in `CxHxW`.
5. Konvertieren Sie das Element in einen Gleitkommatyp.
6. Dann ist eine Normalisierung mit `mean` = `[0.485, 0.456, 0.406]` und `std` = `[0.229, 0.224, 0.225]` von ImageNet erforderlich.

Wenn Sie während des Trainings unterschiedliche Werte für die [Hyperparamter](how-to-auto-train-image-models.md#configure-model-algorithms-and-hyperparameters) `valid_resize_size` und `valid_crop_size` verwenden, sollten diese Werte genutzt werden.

Rufen Sie die Eingabeform ab, die für das ONNX-Modell erforderlich ist.

```python
batch, channel, height_onnx_crop_size, width_onnx_crop_size = session.get_inputs()[0].shape
batch, channel, height_onnx_crop_size, width_onnx_crop_size
```

### <a name="without-pytorch"></a>Ohne PyTorch

```python
def preprocess(image, resize_size, crop_size_onnx):
    """perform pre-processing on raw input image
  
    :param image: raw input image
    :type image: PIL image
    :param resize_size: value to resize the image
    :type image: Int
    :param crop_size_onnx: expected crop size of an input image in onnx model
    :type crop_size_onnx: Int
    :return: pre-processed image in numpy format
    :rtype: ndarray of shape 1xCxHxW
    """

    image = image.convert('RGB')
    # resize
    image = image.resize((resize_size, resize_size))

    # center crop
    left = (resize_size - crop_size_onnx)/2
    top = (resize_size - crop_size_onnx)/2
    right = (resize_size + crop_size_onnx)/2
    bottom = (resize_size + crop_size_onnx)/2
    image = image.crop((left, top, right, bottom))
    np_image = np.array(image)

    # HWC -> CHW
    np_image = np_image.transpose(2, 0, 1)# CxHxW

    # normalize the image
    mean_vec = np.array([0.485, 0.456, 0.406])
    std_vec = np.array([0.229, 0.224, 0.225])
    norm_img_data = np.zeros(np_image.shape).astype('float32')
    for i in range(np_image.shape[0]):
        norm_img_data[i,:,:] = (np_image[i,:,:]/255 - mean_vec[i]) / std_vec[i]
    np_image = np.expand_dims(norm_img_data, axis=0)# 1xCxHxW
    return np_image

from PIL import Image

# you can modify resize_size based on your trained model
resize_size = 256 

# height and width will be the same for classification
crop_size_onnx = height_onnx_crop_size 
test_image_path = "automl_models/test_image.jpg"
img = Image.open(test_image_path)
print("Input image dimensions: ", img.size)
display(img)
img_data = preprocess(img, resize_size, crop_size_onnx)

```

### <a name="with-pytorch"></a>Mit PyTorch

```python
import torch
from torchvision import transforms
# tested with torch version: '1.7.1', torchvision version: '0.8.2')

def _make_3d_tensor(x) -> torch.Tensor:
    """This function is for images that have less channels.

    :param x: input tensor
    :type x: torch.Tensor
    :return: return a tensor with the correct number of channels
    :rtype: torch.Tensor
    """
    return x if x.shape[0] == 3 else x.expand((3, x.shape[1], x.shape[2]))

def preprocess(image, resize_size, crop_size_onnx):
    transform = transforms.Compose([
        transforms.Resize(resize_size),
        transforms.CenterCrop(crop_size_onnx),
        transforms.ToTensor(),
        transforms.Lambda(_make_3d_tensor),
        transforms.Normalize([0.485, 0.456, 0.406], [0.229, 0.224, 0.225])])
    
    img_data = transform(image)
    img_data = img_data.numpy()
    img_data = np.expand_dims(img_data, axis=0)
    return img_data

# you can modify resize_size based on your trained model
resize_size = 256

#height and width will be the same for classification
test_image_path = "automl_models/test_image.jpg"
img = Image.open(test_image_path)
print("Input image dimensions: ", img.size)
display(img)
img_data = preprocess(img, resize_size, crop_size_onnx)
```

# <a name="multi-label-image-classification"></a>[Bildklassifizierung mit mehreren Beschriftungen](#tab/multi-label)

Führen Sie die folgenden Vorverarbeitungsschritte für den ONNX-Modellrückschluss aus. Diese Schritte sind für die Bildklassifizierung mit mehreren Klassen identisch.

1. Konvertieren Sie das Bild in RGB.
2. Ändern Sie die Größe des Bilds in die Werte `valid_resize_size` und `valid_resize_size`, die den Werten entsprechen, die bei der Transformation des Validierungsdatasets während des Trainings verwendet wurden. Der Standardwert für `valid_resize_size` lautet 256.
3. Schneiden Sie das Bild zentriert auf `height_onnx_crop_size` und `width_onnx_crop_size` zu. Dies entspricht `valid_crop_size` mit dem Standardwert 224.
4. Ändern Sie `HxWxC` in `CxHxW`.
5. Konvertieren Sie das Element in einen Gleitkommatyp.
6. Dann ist eine Normalisierung mit `mean` = `[0.485, 0.456, 0.406]` und `std` = `[0.229, 0.224, 0.225]` von ImageNet erforderlich.

Wenn Sie während des Trainings unterschiedliche Werte für die [Hyperparamter](how-to-auto-train-image-models.md#configure-model-algorithms-and-hyperparameters) `valid_resize_size` und `valid_crop_size` verwenden, sollten diese Werte genutzt werden.

Rufen Sie die Eingabeform ab, die für das ONNX-Modell erforderlich ist.

```python
batch, channel, height_onnx_crop_size, width_onnx_crop_size = session.get_inputs()[0].shape
batch, channel, height_onnx_crop_size, width_onnx_crop_size
```

### <a name="without-pytorch"></a>Ohne PyTorch

```python
def preprocess(image, resize_size, crop_size_onnx):
    """perform pre-processing on raw input image
  
    :param image: raw input image
    :type image: PIL image
    :param resize_size: value to resize the image
    :type image: Int
    :param crop_size_onnx: expected crop size of an input image in onnx model
    :type crop_size_onnx: Int
    :return: pre-processed image in numpy format
    :rtype: ndarray of shape 1xCxHxW
    """

    image = image.convert('RGB')
    # resize
    image = image.resize((resize_size, resize_size))

    # center crop
    left = (resize_size - crop_size_onnx)/2
    top = (resize_size - crop_size_onnx)/2
    right = (resize_size + crop_size_onnx)/2
    bottom = (resize_size + crop_size_onnx)/2
    image = image.crop((left, top, right, bottom))
    np_image = np.array(image)

    # HWC -> CHW
    np_image = np_image.transpose(2, 0, 1)# CxHxW

    # normalize the image
    mean_vec = np.array([0.485, 0.456, 0.406])
    std_vec = np.array([0.229, 0.224, 0.225])
    norm_img_data = np.zeros(np_image.shape).astype('float32')
    for i in range(np_image.shape[0]):
        norm_img_data[i,:,:] = (np_image[i,:,:]/255 - mean_vec[i]) / std_vec[i]
    np_image = np.expand_dims(norm_img_data, axis=0)# 1xCxHxW
    return np_image

from PIL import Image

# you can modify resize_size based on your trained model
resize_size = 256 

# height and width will be the same for classification
crop_size_onnx = height_onnx_crop_size 
test_image_path = "automl_models/test_image.jpg"
img = Image.open(test_image_path)
print("Input image dimensions: ", img.size)
display(img)
img_data = preprocess(img, resize_size, crop_size_onnx)

```

### <a name="with-pytorch"></a>Mit PyTorch

```python
import torch
from torchvision import transforms
# tested with torch version: '1.7.1', torchvision version: '0.8.2')

def _make_3d_tensor(x) -> torch.Tensor:
    """This function is for images that have less channels.

    :param x: input tensor
    :type x: torch.Tensor
    :return: return a tensor with the correct number of channels
    :rtype: torch.Tensor
    """
    return x if x.shape[0] == 3 else x.expand((3, x.shape[1], x.shape[2]))

def preprocess(image, resize_size, crop_size_onnx):
    transform = transforms.Compose([
        transforms.Resize(resize_size),
        transforms.CenterCrop(crop_size_onnx),
        transforms.ToTensor(),
        transforms.Lambda(_make_3d_tensor),
        transforms.Normalize([0.485, 0.456, 0.406], [0.229, 0.224, 0.225])])
    
    img_data = transform(image)
    img_data = img_data.numpy()
    img_data = np.expand_dims(img_data, axis=0)
    return img_data

# you can modify resize_size based on your trained model
resize_size = 256

# height and width will be the same for classification
crop_size_onnx = height_onnx_crop_size 
test_image_path = "automl_models/test_image.jpg"
img = Image.open(test_image_path)
print("Input image dimensions: ", img.size)
display(img)
img_data = preprocess(img, resize_size, crop_size_onnx)
```


# <a name="object-detection-with-faster-r-cnn"></a>[Objekterkennung mit Faster R-CNN-Modellen](#tab/object-detect-cnn)

Führen Sie für die Objekterkennung mit dem Faster R-CNN-Algorithmus die gleichen Vorverarbeitungsschritte wie bei der Bildklassifizierung aus, mit Ausnahme des Zuschneidens von Bildern. Sie können die Größe des Bilds mit der Höhe `600` und Breite `800` ändern. Sie können die erwartete Eingabehöhe und -breite mit dem folgenden Code erhalten.

```python
batch, channel, height_onnx, width_onnx = session.get_inputs()[0].shape
batch, channel, height_onnx, width_onnx
```

Führen Sie dann die Vorverarbeitungsschritte aus.

```python
def preprocess(image, height_onnx, width_onnx):
    """perform pre-processing on raw input image
    
    :param image: raw input image
    :type image: PIL image
    :param height_onnx: expected height of an input image in onnx model
    :type height_onnx: Int
    :param width_onnx: expected width of an input image in onnx model
    :type width_onnx: Int
    :return: pre-processed image in numpy format
    :rtype: ndarray of shape 1xCxHxW
    """

    image = image.convert('RGB')
    image = image.resize((width_onnx, height_onnx))
    np_image = np.array(image)
    # HWC -> CHW
    np_image = np_image.transpose(2, 0, 1)# CxHxW

    # Normalize the image
    mean_vec = np.array([0.485, 0.456, 0.406])
    std_vec = np.array([0.229, 0.224, 0.225])
    norm_img_data = np.zeros(np_image.shape).astype('float32')
    for i in range(np_image.shape[0]):
        norm_img_data[i,:,:] = (np_image[i,:,:]/255 - mean_vec[i]) / std_vec[i]
        
        
    np_image = np.expand_dims(norm_img_data, axis=0)# 1xCxHxW
    return np_image

from PIL import Image

test_image_path = "automl_models/test_image.jpg"
img = Image.open(test_image_path)
print("Input image dimensions: ", img.size)
display(img)
img_data = preprocess(img, height_onnx, width_onnx)
```

# <a name="object-detection-with-yolo"></a>[Objekterkennung mit YOLO](#tab/object-detect-yolo)

Führen Sie für die Objekterkennung mit dem YOLO-Algorithmus die gleichen Vorverarbeitungsschritte wie bei der Bildklassifizierung aus, mit Ausnahme des Zuschneidens von Bildern. Sie können die Größe des Bilds mit der Höhe `600` und der Breite `800` ändern und die erwartete Eingabehöhe und -breite mit dem folgenden Code abrufen.

```python
batch, channel, height_onnx, width_onnx = session.get_inputs()[0].shape
batch, channel, height_onnx, width_onnx
```

Informationen zu Vorverarbeitungsschritten bei der YOLO-Methode finden Sie unter [yolo_onnx_preprocessing_utils.py](https://github.com/Azure/azureml-examples/tree/main/python-sdk/tutorials/automl-with-azureml/image-object-detection).

```python
from yolo_onnx_preprocessing_utils import preprocess

test_image_path = "automl_models_od_yolo/test_image.jpg"
img_data, pad = preprocess(test_image_path)
```

# <a name="instance-segmentation"></a>[Instanzsegmentierung](#tab/instance-segmentation)
>[!IMPORTANT]
> Nur Mask R-CNN wird für Instanzsegmentierungsaufgaben unterstützt. Die Vorverarbeitungsschritte basieren nur auf Mask R-CNN.

Führen Sie die folgenden Vorverarbeitungsschritte für den ONNX-Modellrückschluss aus:

1. Konvertieren Sie das Bild in RGB.
2. Ändern Sie die Größe des Bilds. 
3. Ändern Sie `HxWxC` in `CxHxW`.
4. Konvertieren Sie das Element in einen Gleitkommatyp.
5. Dann ist eine Normalisierung mit `mean` = `[0.485, 0.456, 0.406]` und `std` = `[0.229, 0.224, 0.225]` von ImageNet erforderlich.

Für `resize_height` und `resize_width` können Sie auch die Werte verwenden, die Sie während des Trainings genutzt haben, die durch die [Hyperparameter](how-to-auto-train-image-models.md#configure-model-algorithms-and-hyperparameters) `min_size` und `max_size` für Mask R-CNN begrenzt sind.

```python
def preprocess(image, resize_height, resize_width):
    """perform pre-processing on raw input image
        
    :param image: raw input image
    :type image: PIL image
    :param resize_height: resize height of an input image
    :type resize_height: Int
    :param resize_width: resize width of an input image
    :type resize_width: Int
    :return: pre-processed image in numpy format
    :rtype: ndarray of shape 1xCxHxW
    """

    image = image.convert('RGB')
    image = image.resize((resize_width,resize_height))
    np_image = np.array(image)

    # HWC -> CHW
    np_image = np_image.transpose(2, 0, 1)# CxHxW

    # normalize the image
    mean_vec = np.array([0.485, 0.456, 0.406])
    std_vec = np.array([0.229, 0.224, 0.225])
    norm_img_data = np.zeros(np_image.shape).astype('float32')
    for i in range(np_image.shape[0]):
        norm_img_data[i,:,:] = (np_image[i,:,:]/255 - mean_vec[i]) / std_vec[i]
    np_image = np.expand_dims(norm_img_data, axis=0)# 1xCxHxW
    return np_image

from PIL import Image
# use height and width based on the trained model
resize_height, resize_width = 600, 800 
test_image_path = 'automl_models/test_image.jpg'
img = Image.open(test_image_path)
print("Input image dimensions: ", img.size)
display(img)
img_data = preprocess(img, resize_height, resize_width)

```

---

## <a name="inference-with-onnx-runtime"></a>Rückschluss mit ONNX Runtime

Die Rückschließen mit ONNX Runtime unterscheidet sich bei jeder Aufgabe für maschinelles Sehen.

>[!WARNING]
> Die Batchbewertung wird derzeit nicht für alle Aufgaben für maschinelles Sehen unterstützt. 

# <a name="multi-class-image-classification"></a>[Bildklassifizierung mit mehreren Klassen](#tab/multi-class)

```python
def get_predictions_from_ONNX(onnx_session,img_data):
    """perform predictions with ONNX Runtime
    
    :param onnx_session: onnx inference session
    :type onnx_session: class InferenceSession
    :param img_data: pre-processed numpy image
    :type img_data: ndarray with shape 1xCxHxW
    :return: scores with shapes
            (1, No. of classes in training dataset) 
    :rtype: ndarray
    """
    sess_input = onnx_session.get_inputs()
    sess_output = onnx_session.get_outputs()
    print(f"No. of inputs : {len(sess_input)}, No. of outputs : {len(sess_output)}")    
    # predict with ONNX Runtime
    output_names = [ output.name for output in sess_output]
    scores = onnx_session.run(output_names=output_names,\
                                               input_feed={sess_input[0].name: img_data})
    return scores[0]

scores = get_predictions_from_ONNX(session, img_data)
```

# <a name="multi-label-image-classification"></a>[Bildklassifizierung mit mehreren Beschriftungen](#tab/multi-label)

```python
def get_predictions_from_ONNX(onnx_session,img_data):
    """perform predictions with ONNX Runtime
    
    :param onnx_session: onnx inference session
    :type onnx_session: class InferenceSession
    :param img_data: pre-processed numpy image
    :type img_data: ndarray with shape 1xCxHxW
    :return: scores with shapes
            (1, No. of classes in training dataset) 
    :rtype: ndarray
    """
    sess_input = onnx_session.get_inputs()
    sess_output = onnx_session.get_outputs()
    print(f"No. of inputs : {len(sess_input)}, No. of outputs : {len(sess_output)}")    
    # predict with ONNX Runtime
    output_names = [ output.name for output in sess_output]
    scores = onnx_session.run(output_names=output_names,\
                                               input_feed={sess_input[0].name: img_data})
    return scores[0]

scores = get_predictions_from_ONNX(session, img_data)
```

# <a name="object-detection-with-faster-r-cnn"></a>[Objekterkennung mit Faster R-CNN-Modellen](#tab/object-detect-cnn)

```python
def get_predictions_from_ONNX(onnx_session,img_data):
    """perform predictions with ONNX Runtime
    
    :param onnx_session: onnx model session
    :type onnx_session: class InferenceSession
    :param img_data: pre-processed numpy image
    :type img_data: ndarray with shape 1xCxHxW
    :return: boxes, labels , scores 
            (No. of boxes, 4) (No. of boxes,) (No. of boxes,)
    :rtype: tuple
    """
    sess_input = onnx_session.get_inputs()
    sess_output = onnx_session.get_outputs()
    
    # predict with ONNX Runtime
    output_names = [ output.name for output in sess_output]
    boxes, labels, scores = onnx_session.run(output_names=output_names,\
                                               input_feed={sess_input[0].name: img_data})
    return boxes, labels, scores

boxes, labels, scores = get_predictions_from_ONNX(session, img_data)
```

# <a name="object-detection-with-yolo"></a>[Objekterkennung mit YOLO](#tab/object-detect-yolo)

```python
def get_predictions_from_ONNX(onnx_session,img_data):
    """perform predictions with ONNX Runtime
    
    :param onnx_session: onnx model session
    :type onnx_session: class InferenceSession
    :param img_data: pre-processed numpy image
    :type img_data: ndarray with shape 1xCxHxW
    :return: boxes, labels , scores 
    :rtype: list
    """
    sess_input = onnx_session.get_inputs()
    sess_output = onnx_session.get_outputs()
    # predict with ONNX Runtime
    output_names = [ output.name for output in sess_output]
    pred = onnx_session.run(output_names=output_names,\
                                               input_feed={sess_input[0].name: img_data})
    return pred[0]

result = get_predictions_from_ONNX(session, img_data)

```

# <a name="instance-segmentation"></a>[Instanzsegmentierung](#tab/instance-segmentation)

Das Instanzsegmentierungsmodell sagt Rahmen, Bezeichnungen, Scores und Masken vorher. ONNX gibt eine vorhergesagte Maske pro Instanz zusammen mit den entsprechenden Begrenzungsrahmen und dem Klassenkonfidenzscore aus. Möglicherweise müssen Sie eine Konvertierung von einer binären Maske in ein Polygon durchführen.

```python
def get_predictions_from_ONNX(onnx_session,img_data):
    """perform predictions with ONNX Runtime
    
    :param onnx_session: onnx model session
    :type onnx_session: class InferenceSession
    :param img_data: pre-processed numpy image
    :type img_data: ndarray with shape 1xCxHxW
    :return: boxes, labels , scores , masks with shapes
            (No. of instances, 4) (No. of instances,) (No. of instances,)
            (No. of instances, 1, HEIGHT, WIDTH))  
    :rtype: tuple
    """
    
    sess_input = onnx_session.get_inputs()
    sess_output = onnx_session.get_outputs()
    # predict with ONNX Runtime
    output_names = [ output.name for output in sess_output]
    boxes, labels, scores, masks = onnx_session.run(output_names=output_names,\
                                               input_feed={sess_input[0].name: img_data})
    return boxes, labels, scores, masks


boxes, labels, scores, masks = get_predictions_from_ONNX(session, img_data)
```

---

## <a name="postprocessing"></a>Nachbearbeitung

# <a name="multi-class-image-classification"></a>[Bildklassifizierung mit mehreren Klassen](#tab/multi-class)

Wenden Sie `softmax()` auf vorhergesagte Werte an, um Klassifizierungskonfidenzscores (Wahrscheinlichkeiten) für jede Klasse zu erhalten. Dann entspricht die Vorhersage der Klasse mit der höchsten Wahrscheinlichkeit. 

### <a name="without-pytorch"></a>Ohne PyTorch

```python

def softmax(x):
    x = x.reshape(-1)
    e_x = np.exp(x - np.max(x))
    return e_x / e_x.sum(axis=0)
conf_scores = softmax(scores).tolist()
class_idx = np.argmax(conf_scores)
print("predicted class:",(class_idx, classes[class_idx]))

```

### <a name="with-pytorch"></a>Mit PyTorch

```python

conf_scores = torch.nn.functional.softmax(torch.from_numpy(scores), dim=1).tolist()[0]
class_idx = np.argmax(conf_scores)
print("predicted class:", (class_idx, classes[class_idx]))
```

# <a name="multi-label-image-classification"></a>[Bildklassifizierung mit mehreren Beschriftungen](#tab/multi-label)

Dieser Schritt unterscheidet sich von der Klassifizierung mit mehreren Klassen. Sie müssen `sigmoid` auf die Logits (ONNX-Ausgabe) anwenden, um Konfidenzscores für die Bildklassifizierung mit mehreren Bezeichnungen zu erhalten.

### <a name="without-pytorch"></a>Ohne PyTorch

```python

import math

def sigmoid(x):
    return 1 / (1 + math.exp(-x))

# we apply a threshold of 0.5 on confidence scores
cutoff_threshold = .5
for class_idx, prob in enumerate([sigmoid(logit) for logit in scores[0]]):
    if prob > cutoff_threshold:
        print("predicted class:", (class_idx, classes[class_idx]))
```

### <a name="with-pytorch"></a>Mit PyTorch

```python

# we apply a threshold of 0.5 on confidence scores
cutoff_threshold = .5
conf_scores = torch.sigmoid(torch.from_numpy(scores[0])).tolist()
for class_idx, prob in enumerate(conf_scores):
    if prob > cutoff_threshold:
        print("predicted class:", (class_idx, classes[class_idx]))
```

Bei der Klassifizierung mit mehreren Klassen und mehreren Bezeichnungen können Sie die oben genannten Schritte für alle unterstützten Algorithmen in AutoML ausführen.


# <a name="object-detection-with-faster-r-cnn"></a>[Objekterkennung mit Faster R-CNN-Modellen](#tab/object-detect-cnn)

```python
def _get_box_dims(image_shape, box):
    box_keys = ['topX', 'topY', 'bottomX', 'bottomY']
    height, width = image_shape[0], image_shape[1]

    box_dims = dict(zip(box_keys, [coordinate.item() for coordinate in box]))

    box_dims['topX'] = box_dims['topX'] * 1.0 / width
    box_dims['bottomX'] = box_dims['bottomX'] * 1.0 / width
    box_dims['topY'] = box_dims['topY'] * 1.0 / height
    box_dims['bottomY'] = box_dims['bottomY'] * 1.0 / height

    return box_dims

def _get_prediction(boxes, labels, scores, image_shape, classes):
    bounding_boxes = []
    for box, label_index, score in zip(boxes, labels, scores):
        box_dims = _get_box_dims(image_shape, box)

        box_record = {'box': box_dims,
                      'label': classes[label_index],
                      'score': score.item()}

        bounding_boxes.append(box_record)

    return bounding_boxes

bounding_boxes = _get_prediction(boxes, labels, scores, (height_onnx,width_onnx), classes)
print(json.dumps(bounding_boxes, indent=1))

# Filter the results with a threshold.
# Replace the threshold for your test scenario.
score_threshold = 0.8
filtered_bounding_boxes = []
for box in bounding_boxes:
    if box['score'] >= score_threshold:
        filtered_bounding_boxes.append(box)
```

### <a name="visualize-boxes"></a>Visualisieren von Rahmen

```python

import matplotlib.pyplot as plt
import matplotlib.image as mpimg
import matplotlib.patches as patches
from PIL import Image
%matplotlib inline

IMAGE_SIZE = (18,12)
plt.figure(figsize=IMAGE_SIZE)
img_np = mpimg.imread(test_image_path)
img = Image.fromarray(img_np.astype('uint8'),'RGB')
x, y = img.size
print(img.size)

fig,ax = plt.subplots(1)
# Display the image
ax.imshow(img_np)

# Draw a box and label for each detection 
for detect in filtered_bounding_boxes:
    label = detect['label']
    box = detect['box']
    ymin, xmin, ymax, xmax =  box['topY'],box['topX'], box['bottomY'],box['bottomX']
    topleft_x, topleft_y = x * xmin, y * ymin
    width, height = x * (xmax - xmin), y * (ymax - ymin)
    print('{}: {}, {}, {}, {}'.format(detect['label'], topleft_x, topleft_y, width, height))
    rect = patches.Rectangle((topleft_x, topleft_y), width, height, 
                             linewidth=1, edgecolor='green',facecolor='none')

    ax.add_patch(rect)
    color = 'green'
    plt.text(topleft_x, topleft_y, label, color=color)

plt.show()
```

# <a name="object-detection-with-yolo"></a>[Objekterkennung mit YOLO](#tab/object-detect-yolo)

Mit dem folgenden Code werden Rahmen, Bezeichnungen und Scores erstellt. Verwenden Sie diese Begrenzungsrahmendetails, um die gleichen Nachbearbeitungsschritte wie für das Faster R-CNN-Modell durchzuführen. 

```python
from yolo_onnx_preprocessing_utils import non_max_suppression, _convert_to_rcnn_output
import torch

result = non_max_suppression(
    torch.from_numpy(result),
    conf_thres=.1,
    iou_thres=.5)
label, image_shape = _convert_to_rcnn_output(result[0], height_onnx, width_onnx, pad)
boxes = np.array(label["boxes"])
labels = np.array(label["labels"])
labels = [label[0] for label in labels]
scores = np.array(label["scores"])
scores = [score[0] for score in scores]
boxes, labels, scores
```

### <a name="visualize-boxes"></a>Visualisieren von Rahmen

```python

import matplotlib.pyplot as plt
import matplotlib.image as mpimg
import matplotlib.patches as patches
from PIL import Image
%matplotlib inline

IMAGE_SIZE = (18,12)
plt.figure(figsize=IMAGE_SIZE)
img_np = mpimg.imread(test_image_path)
img = Image.fromarray(img_np.astype('uint8'),'RGB')
x, y = img.size
print(img.size)

fig,ax = plt.subplots(1)
# Display the image
ax.imshow(img_np)

# Draw a box and label for each detection 
for detect in filtered_bounding_boxes:
    label = detect['label']
    box = detect['box']
    ymin, xmin, ymax, xmax =  box['topY'],box['topX'], box['bottomY'],box['bottomX']
    topleft_x, topleft_y = x * xmin, y * ymin
    width, height = x * (xmax - xmin), y * (ymax - ymin)
    print('{}: {}, {}, {}, {}'.format(detect['label'], topleft_x, topleft_y, width, height))
    rect = patches.Rectangle((topleft_x, topleft_y), width, height, 
                             linewidth=1, edgecolor='green',facecolor='none')

    ax.add_patch(rect)
    color = 'green'
    plt.text(topleft_x, topleft_y, label, color=color)

plt.show()
```


# <a name="instance-segmentation"></a>[Instanzsegmentierung](#tab/instance-segmentation)

#### <a name="visualize-the-masks-and-bounding-boxes"></a>Visualisieren der Masken und Begrenzungsrahmen

```python
import numpy as np
import matplotlib.pyplot as plt
import matplotlib.patches as patches
import cv2
%matplotlib inline

def display_detections(image, boxes, labels, scores, masks, resize_height, 
                       resize_width, classes, score_threshold=0.3):
    """visualize boxes and masks
    
    :param image: raw image
    :type image: PIL image
    :param boxes: box with shape (No. of instances, 4) 
    :type boxes: ndarray 
    :param labels: classes with shape (No. of instances,) 
    :type labels: ndarray
    :param scores: scores with shape (No. of instances,)
    :type scores: ndarray
    :param masks: masks with shape (No. of instances, 1, HEIGHT, WIDTH) 
    :type masks:  ndarray
    :param resize_height: resize height of an input image
    :type resize_height: Int
    :param resize_width: resize width of an input image
    :type resize_width: Int
    :param classes: classes with shape (No. of classes) 
    :type classes:  list
    :param score_threshold: threshold on scores in the range of 0-1
    :type score_threshold: float
    :return: None     
   
    """

    _, ax = plt.subplots(1, figsize=(12,9))

    image = np.array(image)
    original_height = image.shape[0]
    original_width = image.shape[1]

    for mask, box, label, score in zip(masks, boxes, labels, scores):        
        if score <= score_threshold:
            continue
        mask = mask[0, :, :, None]        
        # resize boxes to original raw input size
        box = [box[0]*original_width/resize_width, 
               box[1]*original_height/resize_height, 
               box[2]*original_width/resize_width, 
               box[3]*original_height/resize_height]
        
        mask = cv2.resize(mask, (image.shape[1],image.shape[0]), 0, 0, interpolation = cv2.INTER_NEAREST)
        # mask is a matrix with values in the range of [0,1]
        # higher values indicate the presence of an object and vice versa
        # select the threshold or cutoff value to get objects present               
        mask = mask > 0.5 
        image_masked = image.copy()
        image_masked[mask] = (0, 255, 255)
        alpha = .5 # alpha blending with range 0 to 1
        cv2.addWeighted(image_masked, alpha, image, 1 - alpha,0, image)        
        rect = patches.Rectangle((box[0], box[1]), box[2] - box[0], box[3] - box[1],\
                                 linewidth=1, edgecolor='b', facecolor='none')
        ax.annotate(classes[label] + ':' + str(np.round(score, 2)), (box[0], box[1]),\
                    color='w', fontsize=12)
        ax.add_patch(rect)
        

    ax.imshow(image)
    plt.show()

display_detections(img, boxes.copy(), labels, scores, masks.copy(), 
                   resize_height, resize_width, classes, score_threshold=.5)
```

---


## <a name="next-steps"></a>Nächste Schritte
* [Einrichten von AutoML für das Trainieren von Modellen für maschinelles Sehen mit Python (Vorschauversion)](how-to-auto-train-image-models.md)
* [Behandeln von Problemen bei Experimenten mit automatisiertem ML in Python](how-to-troubleshoot-auto-ml.md)

