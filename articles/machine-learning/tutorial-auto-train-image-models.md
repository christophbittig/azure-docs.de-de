---
title: 'Tutorial: AutoML – Trainieren eines Objekterkennungsmodells'
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie mithilfe des Python SDK für Azure Machine Learning ein Objekterkennungsmodell für die Vorhersage von Taxitarifen in New York City trainieren und dabei das automatisierte maschinelle Lernen von Azure Machine Learning verwenden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: swatig007
ms.author: swatig
ms.reviewer: nibaccam
ms.date: 10/06/2021
ms.custom: devx-track-python, automl
ms.openlocfilehash: e6906988b47ecdb1ba5e1ca7947666e6371ddd8e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131468573"
---
# <a name="tutorial-train-an-object-detection-model-preview-with-automl-and-python"></a>Tutorial: Trainieren eines Objekterkennungsmodells (Vorschauversion) mit AutoML und Python

>[!IMPORTANT]
> Die in diesem Artikel vorgestellten Features befinden sich in der Vorschauversion. Sie sollten als [experimentelle](/python/api/overview/azure/ml/#stable-vs-experimental) Previewfunktionen betrachtet werden, die sich jederzeit ändern können.

In diesem Tutorial erfahren Sie, wie Sie das automatisierte maschinelle Lernen von Azure Machine Learning mit dem Python SDK für Azure Machine Learning verwenden, um ein Objekterkennungsmodell zu trainieren. Dieses Objekterkennungsmodell ermittelt, ob das Bild Objekte wie etwa eine Dose, einen Karton, eine Milchflasche oder eine Wasserflasche enthält.

Automatisiertes maschinelles Lernen akzeptiert Trainingsdaten und Konfigurationseinstellungen und durchläuft automatisch Kombinationen von verschiedenen Methoden zur Featurenormalisierung/-standardisierung, Modellen und Hyperparametereinstellungen, um das bestmögliche Modell zu erhalten.


In diesem Tutorial wird Code mithilfe des Python SDK geschrieben, und es werden folgende Aufgaben vermittelt:

> [!div class="checklist"]
> * Herunterladen und Transformieren von Daten
> * Trainieren eines Objekterkennungsmodells mit automatisiertem maschinellem Lernen
> * Angeben von Hyperparameterwerten für Ihr Modell
> * Durchführen eines Hyperparameter-Sweepings
> * Bereitstellen Ihres Modells
> * Visualisieren von Erkennungen

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie noch heute die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://azure.microsoft.com/free/) aus.

* Für dieses Feature werden Python 3.6 und 3.7 unterstützt.

* Sollten Sie noch nicht über einen Azure Machine Learning-Arbeitsbereich verfügen, durchlaufen Sie [Schnellstart: Erste Schritte mit Azure Machine Learning](quickstart-create-resources.md#create-the-workspace).

* Laden Sie die Datendatei [**odFridgeObjects.csv**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) herunter.

Dieses Tutorial ist auch auf [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) verfügbar, falls Sie es in Ihrer eigenen [lokalen Umgebung](how-to-configure-environment.md#local) ausführen möchten. So erhalten Sie die erforderlichen Pakete:
* Ausführen von `pip install azureml`
* [Installieren Sie den vollständigen `automl`-Client.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md#setup-using-a-local-conda-environment)

## <a name="compute-target-setup"></a>Einrichten des Computeziels

Zuerst muss ein Computeziel zum Trainieren des automatisierten ML-Modells eingerichtet werden. Automatisierte ML-Modelle für Bildaufgaben erfordern GPU-SKUs.

In diesem Tutorial wird die NCsv3-Serie (mit V100-GPUs) verwendet, da diese Art von Computeziel mehrere GPUs nutzt, um das Trainieren zu beschleunigen. Darüber hinaus können Sie mehrere Knoten einrichten, um bei der Optimierung von Hyperparametern für Ihr Modell von Parallelität zu profitieren.

Mit dem folgenden Code wird eine GPU-Computeressource der Größe „Standard_NC24s_v3“ mit vier Knoten erstellt, die an den Arbeitsbereich `ws` angefügt sind.

> [!WARNING]
> Stellen Sie sicher, dass Ihr Abonnement über ein ausreichendes Kontingent für das gewünschte Computeziel verfügt.

```python
from azureml.core.compute import AmlCompute, ComputeTarget

cluster_name = "gpu-cluster-nc24s_v3"

try:
    compute_target = ws.compute_targets[cluster_name]
    print('Found existing compute target.')
except KeyError:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='Standard_NC24s_v3',
                                                           idle_seconds_before_scaledown=1800,
                                                           min_nodes=0,
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

#If no min_node_count is provided, the scale settings are used for the cluster.
compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

## <a name="experiment-setup"></a>Einrichten des Experiments
Erstellen Sie als Nächstes ein `Experiment` in Ihrem Arbeitsbereich, um Ihre Modelltrainingsausführungen nachzuverfolgen.

```python

from azureml.core import Experiment

experiment_name = 'automl-image-object-detection'
experiment = Experiment(ws, name=experiment_name)
```

## <a name="visualize-input-data"></a>Visualisieren von Eingabedaten

Nachdem Sie die Eingabebilddaten im Format [JSONL](https://jsonlines.org/) (JSON-Zeilen) vorbereitet haben, können Sie die Ground Truth-Begrenzungsrahmen für ein Bild visualisieren. Vergewissern Sie sich hierzu, dass `matplotlib` installiert ist.

```
%pip install --upgrade matplotlib
```
```python

%matplotlib inline
import matplotlib.pyplot as plt
import matplotlib.image as mpimg
import matplotlib.patches as patches
from PIL import Image as pil_image
import numpy as np
import json
import os

def plot_ground_truth_boxes(image_file, ground_truth_boxes):
    # Display the image
    plt.figure()
    img_np = mpimg.imread(image_file)
    img = pil_image.fromarray(img_np.astype("uint8"), "RGB")
    img_w, img_h = img.size

    fig,ax = plt.subplots(figsize=(12, 16))
    ax.imshow(img_np)
    ax.axis("off")

    label_to_color_mapping = {}

    for gt in ground_truth_boxes:
        label = gt["label"]

        xmin, ymin, xmax, ymax =  gt["topX"], gt["topY"], gt["bottomX"], gt["bottomY"]
        topleft_x, topleft_y = img_w * xmin, img_h * ymin
        width, height = img_w * (xmax - xmin), img_h * (ymax - ymin)

        if label in label_to_color_mapping:
            color = label_to_color_mapping[label]
        else:
            # Generate a random color. If you want to use a specific color, you can use something like "red".
            color = np.random.rand(3)
            label_to_color_mapping[label] = color

        # Display bounding box
        rect = patches.Rectangle((topleft_x, topleft_y), width, height,
                                 linewidth=2, edgecolor=color, facecolor="none")
        ax.add_patch(rect)

        # Display label
        ax.text(topleft_x, topleft_y - 10, label, color=color, fontsize=20)

    plt.show()

def plot_ground_truth_boxes_jsonl(image_file, jsonl_file):
    image_base_name = os.path.basename(image_file)
    ground_truth_data_found = False
    with open(jsonl_file) as fp:
        for line in fp.readlines():
            line_json = json.loads(line)
            filename = line_json["image_url"]
            if image_base_name in filename:
                ground_truth_data_found = True
                plot_ground_truth_boxes(image_file, line_json["label"])
                break
    if not ground_truth_data_found:
        print("Unable to find ground truth information for image: {}".format(image_file))

def plot_ground_truth_boxes_dataset(image_file, dataset_pd):
    image_base_name = os.path.basename(image_file)
    image_pd = dataset_pd[dataset_pd['portable_path'].str.contains(image_base_name)]
    if not image_pd.empty:
        ground_truth_boxes = image_pd.iloc[0]["label"]
        plot_ground_truth_boxes(image_file, ground_truth_boxes)
    else:
        print("Unable to find ground truth information for image: {}".format(image_file))
```

Mithilfe der oben angegebenen Hilfsfunktionen können Sie für jedes beliebige Bild den folgenden Code ausführen, um die Begrenzungsrahmen anzuzeigen.

```python
image_file = "./odFridgeObjects/images/31.jpg"
jsonl_file = "./odFridgeObjects/train_annotations.jsonl"

plot_ground_truth_boxes_jsonl(image_file, jsonl_file)
```

## <a name="upload-data-and-create-dataset"></a>Hochladen der Daten und Erstellen des Datasets

Laden Sie die Daten über einen Datenspeicher in Ihren Arbeitsbereich hoch, um sie für das Training zu verwenden. Der Datenspeicher bietet einen Mechanismus, mit dem Sie Daten hoch- oder herunterladen sowie von Ihren Remotecomputezielen aus mit ihnen interagieren können.

```python
ds = ws.get_default_datastore()
ds.upload(src_dir='./odFridgeObjects', target_path='odFridgeObjects')
```

Nach dem Hochladen in den Datenspeicher können Sie auf der Grundlage der Daten ein Azure Machine Learning-Dataset erstellen. Datasets verpacken Ihre Daten in ein Objekt, das zum Trainieren genutzt werden kann.

Mit dem folgenden Code wird ein Dataset für das Training erstellt. Da kein Validierungsdataset angegeben ist, werden standardmäßig 20 Prozent Ihrer Trainingsdaten für die Validierung verwendet.

``` python
from azureml.core import Dataset
from azureml.data import DataType

training_dataset_name = 'odFridgeObjectsTrainingDataset'
if training_dataset_name in ws.datasets:
    training_dataset = ws.datasets.get(training_dataset_name)
    print('Found the training dataset', training_dataset_name)
else:
    # create training dataset
        # create training dataset
    training_dataset = Dataset.Tabular.from_json_lines_files(
        path=ds.path('odFridgeObjects/train_annotations.jsonl'),
        set_column_types={"image_url": DataType.to_stream(ds.workspace)},
    )
    training_dataset = training_dataset.register(workspace=ws, name=training_dataset_name)

print("Training dataset name: " + training_dataset.name)
```

### <a name="visualize-dataset"></a>Visualisieren des Datasets

Sie können auch die Ground Truth-Begrenzungsrahmen für ein Bild aus diesem Dataset visualisieren.

Laden Sie das Dataset in einen Pandas-Datenrahmen.

```python
import azureml.dataprep as dprep

from azureml.dataprep.api.functions import get_portable_path

# Get pandas dataframe from the dataset
dflow = training_dataset._dataflow.add_column(get_portable_path(dprep.col("image_url")),
                                              "portable_path", "image_url")
dataset_pd = dflow.to_pandas_dataframe(extended_types=True)
```

Sie können für jedes beliebige Bild den folgenden Code ausführen, um die Begrenzungsrahmen anzuzeigen:

```python
image_file = "./odFridgeObjects/images/31.jpg"
plot_ground_truth_boxes_dataset(image_file, dataset_pd)
```

## <a name="configure-your-object-detection-experiment"></a>Konfigurieren Ihres Objekterkennungsexperiments

Verwenden Sie das Objekt `AutoMLImageConfig`, um automatisierte ML-Ausführungen für bildbezogene Aufgaben zu konfigurieren. In `AutoMLImageConfig` können Sie mit dem Parameter `model_name` die Modellalgorithmen angeben und die Einstellungen so konfigurieren, dass ein Hyperparameter-Sweeping innerhalb eines definierten Parameterraums durchgeführt wird, um das optimale Modell zu finden.

In diesem Beispiel wird `AutoMLImageConfig` verwendet, um ein Objekterkennungsmodell mit `yolov5` und `fasterrcnn_resnet50_fpn` zu trainieren. Beide wurden mit COCO vortrainiert – einem umfangreichen Dataset für Objekterkennung, Segmentierung und Beschriftung, das mehrere tausend beschriftete Bilder mit über 80 Beschriftungskategorien umfasst.

### <a name="hyperparameter-sweeping-for-image-tasks"></a>Hyperparameter-Sweeping für Bildaufgaben

Sie können ein Hyperparameter-Sweeping innerhalb eines definierten Parameterraums durchführen, um das optimale Modell zu finden.

Mit dem folgenden Code wird der Parameterraum für die definierten Algorithmen `yolov5` und `fasterrcnn_resnet50_fpn` definiert, um das Hyperparameter-Sweeping vorzubereiten.  Geben Sie in dem Parameterraum den Wertebereich für `learning_rate`, `optimizer`, `lr_scheduler` usw. an, der AutoML zur Verfügung stehen soll, um ein Modell mit der optimalen primären Metrik zu generieren. Ohne Angabe von Hyperparameterwerten werden für die Algorithmen jeweils Standardwerte verwendet.

Verwenden Sie für die Optimierungseinstellungen die zufällige Stichprobenentnahme, um Stichproben aus diesem Parameterraum zu wählen, indem Sie die Klassen `GridParameterSampling, RandomParameterSampling` und `BayesianParameterSampling` importieren.  Dadurch wird das automatisierte maschinelle Lernen angewiesen, insgesamt 20 Iterationen mit diesen verschiedenen Stichproben zu durchlaufen, wobei für unser mit vier Knoten eingerichtetes Computeziel jeweils vier Iterationen gleichzeitig ausgeführt werden. Je höher die Parameteranzahl des Raums, desto mehr Iterationen werden benötigt, um optimale Modelle zu finden.

Außerdem wird die Banditenrichtlinie für vorzeitige Beendigung verwendet. Durch diese Richtlinie werden Konfigurationen beendet, die mehr als 20 Prozent schlechter sind als die Konfiguration mit der besten Leistung, was zu einer erheblichen Einsparung von Computeressourcen führt.

```python
from azureml.train.hyperdrive import RandomParameterSampling
from azureml.train.hyperdrive import BanditPolicy, HyperDriveConfig
from azureml.train.hyperdrive import choice, uniform

parameter_space = {
    'model': choice(
        {
            'model_name': choice('yolov5'),
            'learning_rate': uniform(0.0001, 0.01),
            #'model_size': choice('small', 'medium'), # model-specific
            'img_size': choice(640, 704, 768), # model-specific
        },
        {
            'model_name': choice('fasterrcnn_resnet50_fpn'),
            'learning_rate': uniform(0.0001, 0.001),
            #'warmup_cosine_lr_warmup_epochs': choice(0, 3),
            'optimizer': choice('sgd', 'adam', 'adamw'),
            'min_size': choice(600, 800), # model-specific
        }
    )
}

tuning_settings = {
    'iterations': 20,
    'max_concurrent_iterations': 4,
    'hyperparameter_sampling': RandomParameterSampling(parameter_space),
    'policy': BanditPolicy(evaluation_interval=2, slack_factor=0.2, delay_evaluation=6)
}
```

Nach dem Definieren des Parameterraums und der Optimierungseinstellungen können Sie sie an Ihr Objekt vom Typ `AutoMLImageConfig` übergeben und anschließend das Experiment übermitteln, um ein Bildmodell mit Ihrem Trainingsdataset zu trainieren.

```python
from azureml.train.automl import AutoMLImageConfig
automl_image_config = AutoMLImageConfig(task='image-object-detection',
                                        compute_target=compute_target,
                                        training_data=training_dataset,
                                        validation_data=validation_dataset,
                                        primary_metric='mean_average_precision',
                                        **tuning_settings)

automl_image_run = experiment.submit(automl_image_config)
automl_image_run.wait_for_completion(wait_post_processing=True)
```

Im Zuge eines Hyperparameter-Sweepings kann es hilfreich sein, die verschiedenen getesteten Konfigurationen mithilfe der HyperDrive-Benutzeroberfläche zu visualisieren. Zu dieser Benutzeroberfläche gelangen Sie, indem Sie auf der Benutzeroberfläche der weiter oben angegebenen Hauptausführung „automl_image_run“ (also der übergeordneten HyperDrive-Ausführung) zur Registerkarte für untergeordnete Ausführungen navigieren. Dort können Sie dann zur entsprechenden Registerkarte für untergeordnete Ausführungen wechseln. Alternativ können Sie wie folgt direkt die übergeordnete HyperDrive-Ausführung anzeigen und zur zugehörigen Registerkarte für untergeordnete Ausführungen navigieren:

```python
from azureml.core import Run
hyperdrive_run = Run(experiment=experiment, run_id=automl_image_run.id + '_HD')
hyperdrive_run
```

## <a name="register-the-best-model"></a>Registrieren des besten Modells

Nach Abschluss der Ausführung kann das Modell registriert werden, das auf der Grundlage der besten Ausführung erstellt wurde.

```python
best_child_run = automl_image_run.get_best_child()
model_name = best_child_run.properties['model_name']
model = best_child_run.register_model(model_name = model_name, model_path='outputs/model.pt')
```

## <a name="deploy-model-as-a-web-service"></a>Bereitstellen des Modells als Webdienst

Das trainierte Modell kann in Azure bereitgestellt werden. Sie können Ihr trainiertes Modell als Webdienst in Azure Container Instances (ACI) oder in Azure Kubernetes Service (AKS) bereitstellen. ACI eignet sich perfekt zum Testen von Bereitstellungen, während AKS besser zur umfangreichen Nutzung in der Produktion geeignet ist.

In diesem Tutorial wird das Modell als Webdienst in AKS bereitgestellt.

1. Erstellen Sie einen AKS-Computecluster. In diesem Beispiel wird für den Bereitstellungscluster eine VM-SKU mit GPU verwendet.

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

1. Definieren Sie die Rückschlusskonfiguration, um die Einrichtung des Webdiensts zu beschreiben, der Ihr Modell enthält. In Ihrer Rückschlusskonfiguration können Sie das Bewertungsskript und die Umgebung aus der Trainingsausführung verwenden.

    > [!NOTE]
    > Wenn Sie die Einstellungen des Modells ändern möchten, öffnen Sie das heruntergeladene Bewertungsskript, und ändern Sie die Variable „model_settings“, bevor Sie das Modell bereitstellen.

    ```python
    from azureml.core.model import InferenceConfig

    best_child_run.download_file('outputs/scoring_file_v_1_0_0.py', output_file_path='score.py')
    environment = best_child_run.get_environment()
    inference_config = InferenceConfig(entry_script='score.py', environment=environment)
    ```

1. Anschließend können Sie das Modell als AKS-Webdienst bereitstellen.

    ```python

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

## <a name="test-the-web-service"></a>Testen des Webdiensts

Sie können den bereitgestellten Webdienst testen, um neue Bilder vorherzusagen. Übergeben Sie für dieses Tutorial ein zufälliges Bild aus dem Dataset an den Bewertungs-URI.

```python
import requests

# URL for the web service
scoring_uri = aks_service.scoring_uri

# If the service is authenticated, set the key or token
key, _ = aks_service.get_keys()

sample_image = './test_image.jpg'

# Load image data
data = open(sample_image, 'rb').read()

# Set the content type
headers = {'Content-Type': 'application/octet-stream'}

# If authentication is enabled, set the authorization header
headers['Authorization'] = f'Bearer {key}'

# Make the request and display the response
resp = requests.post(scoring_uri, data, headers=headers)
print(resp.text)
```
## <a name="visualize-detections"></a>Visualisieren von Erkennungen
Nachdem Sie nun ein Testbild bewertet haben, können Sie die Begrenzungsrahmen für dieses Bild visualisieren. Hierzu muss „matplotlib“ installiert sein.

```
%pip install --upgrade matplotlib
```

```python
%matplotlib inline
import matplotlib.pyplot as plt
import matplotlib.image as mpimg
import matplotlib.patches as patches
from PIL import Image
import numpy as np
import json

IMAGE_SIZE = (18,12)
plt.figure(figsize=IMAGE_SIZE)
img_np=mpimg.imread(sample_image)
img = Image.fromarray(img_np.astype('uint8'),'RGB')
x, y = img.size

fig,ax = plt.subplots(1, figsize=(15,15))
# Display the image
ax.imshow(img_np)

# draw box and label for each detection
detections = json.loads(resp.text)
for detect in detections['boxes']:
    label = detect['label']
    box = detect['box']
    conf_score = detect['score']
    if conf_score > 0.6:
        ymin, xmin, ymax, xmax =  box['topY'],box['topX'], box['bottomY'],box['bottomX']
        topleft_x, topleft_y = x * xmin, y * ymin
        width, height = x * (xmax - xmin), y * (ymax - ymin)
        print('{}: [{}, {}, {}, {}], {}'.format(detect['label'], round(topleft_x, 3),
                                                round(topleft_y, 3), round(width, 3),
                                                round(height, 3), round(conf_score, 3)))

        color = np.random.rand(3) #'red'
        rect = patches.Rectangle((topleft_x, topleft_y), width, height,
                                 linewidth=3, edgecolor=color,facecolor='none')

        ax.add_patch(rect)
        plt.text(topleft_x, topleft_y - 10, label, color=color, fontsize=20)

plt.show()
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Überspringen Sie diesen Abschnitt, wenn Sie weitere Azure Machine Learning-Tutorials absolvieren möchten.

Wenn Sie die erstellten Ressourcen nicht mehr benötigen, löschen Sie sie, damit Ihnen keine Kosten entstehen.

1. Wählen Sie ganz links im Azure-Portal **Ressourcengruppen** aus.
1. Wählen Sie in der Liste die Ressourcengruppe aus, die Sie erstellt haben.
1. Wählen Sie die Option **Ressourcengruppe löschen**.
1. Geben Sie den Ressourcengruppennamen ein. Wählen Sie anschließend die Option **Löschen**.

Sie können die Ressourcengruppe auch behalten und einen einzelnen Arbeitsbereich löschen. Zeigen Sie die Eigenschaften des Arbeitsbereichs an, und klicken Sie auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial zum automatisierten maschinellen Lernen haben Sie folgende Aufgaben ausgeführt:

> [!div class="checklist"]
> * Konfigurieren eines Arbeitsbereichs und Vorbereiten der Daten für ein Experiment
> * Trainieren eines automatisierten Objekterkennungsmodells
> * Angeben von Hyperparameterwerten für Ihr Modell
> * Durchführen eines Hyperparameter-Sweepings
> * Bereitstellen Ihres Modells
> * Visualisieren von Erkennungen

* Weitere Informationen zu maschinellem Sehen im Zusammenhang mit automatisiertem maschinellem Lernen (Vorschauversion) finden Sie [hier](concept-automated-ml.md#computer-vision-preview).
* Informationen zum Einrichten von automatisiertem maschinellem Lernen für das Trainieren von Modellen für maschinelles Sehen mit Python (Vorschauversion) finden Sie [hier](how-to-auto-train-image-models.md).
* Überprüfen Sie detaillierte Codebeispiele und Anwendungsfälle im [GitHub-Notebook-Repository für Beispiele zum automatisierten maschinellen Lernen](https://github.com/Azure/azureml-examples/tree/main/python-sdk/tutorials/automl-with-azureml). Überprüfen Sie die Ordner mit dem Präfix „image-“ auf spezifische Beispiele für die Erstellung von Modellen für maschinelles Sehen.

> [!NOTE]
> Das Dataset „Fridge Objects“ kann über die Lizenz unter [MIT-Lizenz](https://github.com/microsoft/computervision-recipes/blob/master/LICENSE) genutzt werden.
