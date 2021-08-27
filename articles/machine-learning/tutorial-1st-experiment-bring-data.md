---
title: 'Tutorial: Hochladen von Daten und Trainieren eines Modells'
titleSuffix: Azure Machine Learning
description: Hochladen und Verwenden eigener Daten bei der Ausführung eines Remotetrainings Dies ist Teil 3 einer dreiteiligen Reihe mit ersten Schritten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 04/29/2021
ms.custom: tracking-python, contperf-fy21q3, FY21Q4-aml-seo-hack, contperf-fy21q4
ms.openlocfilehash: 80841c800172c4f0f551b6437d18d437aed0536d
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634128"
---
# <a name="tutorial-upload-data-and-train-a-model-part-3-of-3"></a>Tutorial: Hochladen von Daten und Trainieren eines Modells (Teil 3 von 3)

In diesem Tutorial wird gezeigt, wie Sie Ihre eigenen Daten hochladen und in Azure Machine Learning zum Trainieren von Machine Learning-Modellen verwenden können. Dieses Tutorial ist der *dritte Teil einer dreiteiligen Reihe*.  

In [Teil 2: Trainieren eines Modells](tutorial-1st-experiment-sdk-train.md) haben Sie ein Modell in der Cloud mithilfe von Beispieldaten von `PyTorch` trainiert.  Sie haben diese Daten auch über die `torchvision.datasets.CIFAR10`-Methode in der PyTorch-API heruntergeladen. In diesem Tutorial verwenden Sie die heruntergeladenen Daten, um den Workflow für die Arbeit mit Ihren eigenen Daten in Azure Machine Learning zu erlernen.

In diesem Tutorial:

> [!div class="checklist"]
> * Hochladen von Daten in Azure.
> * Erstellen eines Steuerungsskripts.
> * Verstehen der neuen Azure Machine Learning-Konzepte (Übergeben von Parametern, Datasets, Datenspeichern).
> * Senden und Ausführen des Trainingsskripts
> * Anzeigen Ihrer Codeausgabe in der Cloud

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen die Daten, die im vorherigen Tutorial heruntergeladen wurden.  Stellen Sie sicher, dass Sie die folgenden Schritte ausgeführt haben:

1. [Erstellen des Trainingsskripts](tutorial-1st-experiment-sdk-train.md#create-training-scripts)  
1. [Lokales Testen](tutorial-1st-experiment-sdk-train.md#test-local)

## <a name="adjust-the-training-script"></a>Anpassen des Trainingsskripts

Mittlerweile können Sie Ihr Trainingsskript (get-started/src/train.py) in Azure Machine Learning ausführen und die Leistung des Modells überwachen. Wir können das Trainingsskript durch die Einführung von Argumenten parametrisieren. Mithilfe von Argumenten können Sie verschiedene Hyperparameter problemlos vergleichen.

Derzeit ist unser Trainingsskript so konfiguriert, dass bei jeder Ausführung das CIFAR10-Dataset heruntergeladen wird. Der folgende Python-Code wurde angepasst, um die Daten aus einem Verzeichnis zu lesen.

>[!NOTE] 
> Die Verwendung von `argparse` parametrisiert das Skript.

1. Öffnen Sie *train.py*, und ersetzen Sie es durch den folgenden Code:

    ```python
    import os
    import argparse
    import torch
    import torch.optim as optim
    import torchvision
    import torchvision.transforms as transforms
    from model import Net
    from azureml.core import Run
    run = Run.get_context()
    if __name__ == "__main__":
        parser = argparse.ArgumentParser()
        parser.add_argument(
            '--data_path',
            type=str,
            help='Path to the training data'
        )
        parser.add_argument(
            '--learning_rate',
            type=float,
            default=0.001,
            help='Learning rate for SGD'
        )
        parser.add_argument(
            '--momentum',
            type=float,
            default=0.9,
            help='Momentum for SGD'
        )
        args = parser.parse_args()
        print("===== DATA =====")
        print("DATA PATH: " + args.data_path)
        print("LIST FILES IN DATA PATH...")
        print(os.listdir(args.data_path))
        print("================")
        # prepare DataLoader for CIFAR10 data
        transform = transforms.Compose([
            transforms.ToTensor(),
            transforms.Normalize((0.5, 0.5, 0.5), (0.5, 0.5, 0.5))
        ])
        trainset = torchvision.datasets.CIFAR10(
            root=args.data_path,
            train=True,
            download=False,
            transform=transform,
        )
        trainloader = torch.utils.data.DataLoader(
            trainset,
            batch_size=4,
            shuffle=True,
            num_workers=2
        )
        # define convolutional network
        net = Net()
        # set up pytorch loss /  optimizer
        criterion = torch.nn.CrossEntropyLoss()
        optimizer = optim.SGD(
            net.parameters(),
            lr=args.learning_rate,
            momentum=args.momentum,
        )
        # train the network
        for epoch in range(2):
            running_loss = 0.0
            for i, data in enumerate(trainloader, 0):
                # unpack the data
                inputs, labels = data
                # zero the parameter gradients
                optimizer.zero_grad()
                # forward + backward + optimize
                outputs = net(inputs)
                loss = criterion(outputs, labels)
                loss.backward()
                optimizer.step()
                # print statistics
                running_loss += loss.item()
                if i % 2000 == 1999:
                    loss = running_loss / 2000
                    run.log('loss', loss)  # log loss metric to AML
                    print(f'epoch={epoch + 1}, batch={i + 1:5}: loss {loss:.2f}')
                    running_loss = 0.0
        print('Finished Training')
    ```

1. **Speichern** Sie die Datei.  Schließen Sie die Registerkarte, wenn Sie möchten.

### <a name="understanding-the-code-changes"></a>Grundlegendes zu den Codeänderungen

Der Code in `train.py` hat die `argparse`-Bibliothek zum Einrichten von `data_path`, `learning_rate` und `momentum` verwendet.

```python
# .... other code
parser = argparse.ArgumentParser()
parser.add_argument('--data_path', type=str, help='Path to the training data')
parser.add_argument('--learning_rate', type=float, default=0.001, help='Learning rate for SGD')
parser.add_argument('--momentum', type=float, default=0.9, help='Momentum for SGD')
args = parser.parse_args()
# ... other code
```

Außerdem wurde das `train.py`-Skript angepasst, um den Optimierer für die Verwendung der benutzerdefinierten Parameter zu aktualisieren:

```python
optimizer = optim.SGD(
    net.parameters(),
    lr=args.learning_rate,     # get learning rate from command-line argument
    momentum=args.momentum,    # get momentum from command-line argument
)
```


## <a name="upload-the-data-to-azure"></a><a name="upload"></a> Hochladen der Daten in Azure

Damit Sie dieses Skript in Azure Machine Learning ausführen können, müssen Sie Ihre Trainingsdaten in Azure verfügbar machen. Der Azure Machine Learning-Arbeitsbereich ist mit einem _Standarddatenspeicher_ ausgestattet. Dabei handelt es sich um ein Azure Blob Storage-Konto, in dem Sie Ihre Trainingsdaten speichern können.

>[!NOTE] 
> In Azure Machine Learning können Sie Verbindungen zu anderen cloudbasierten Datenspeichern herstellen, in denen Ihre Daten gespeichert sind. Weitere Details finden Sie in der [Dokumentation zu Datenspeichern](./concept-data.md).  

1. Erstellen Sie ein neues Python-Steuerungsskript im Ordner **get-started** (stellen Sie sicher, dass es sich im Ordner **get-started** und *nicht* im Ordner **/src** befindet).  Benennen Sie das Skript *upload-data.py*, und kopieren Sie den folgenden Code in die Datei:
    
    ```python
    # upload-data.py
    from azureml.core import Workspace
    ws = Workspace.from_config()
    datastore = ws.get_default_datastore()
    datastore.upload(src_dir='./data',
                     target_path='datasets/cifar10',
                     overwrite=True)
    
    ```

    Der `target_path`-Wert gibt den Pfad im Datenspeicher an, in den die CIFAR10-Daten hochgeladen werden sollen.

    >[!TIP] 
    > Während Sie Azure Machine Learning zum Hochladen der Daten verwenden, können Sie [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) zum Hochladen von Ad-hoc-Dateien verwenden. Wenn Sie ein ETL-Tool benötigen, können Sie [Azure Data Factory](../data-factory/introduction.md) für die Erfassung Ihrer Daten in Azure verwenden.

2. Wählen Sie **Save and run script in terminal** (Skript speichern und im Terminal ausführen) aus, um das Skript *upload-data.py* auszuführen.

    Es sollte die folgende Standardausgabe angezeigt werden:

    ```txt
    Uploading ./data\cifar-10-batches-py\data_batch_2
    Uploaded ./data\cifar-10-batches-py\data_batch_2, 4 files out of an estimated total of 9
    .
    .
    Uploading ./data\cifar-10-batches-py\data_batch_5
    Uploaded ./data\cifar-10-batches-py\data_batch_5, 9 files out of an estimated total of 9
    Uploaded 9 files
    ```

## <a name="create-a-control-script"></a><a name="control-script"></a> Erstellen eines Steuerungsskripts

Erstellen Sie wie zuvor im Ordner **get-started** ein neues Python-Steuerungsskript namens *run-pytorch-data.py*:

```python
# run-pytorch-data.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig
from azureml.core import Dataset

if __name__ == "__main__":
    ws = Workspace.from_config()
    datastore = ws.get_default_datastore()
    dataset = Dataset.File.from_files(path=(datastore, 'datasets/cifar10'))

    experiment = Experiment(workspace=ws, name='day1-experiment-data')

    config = ScriptRunConfig(
        source_directory='./src',
        script='train.py',
        compute_target='cpu-cluster',
        arguments=[
            '--data_path', dataset.as_named_input('input').as_mount(),
            '--learning_rate', 0.003,
            '--momentum', 0.92],
    )

    # use curated pytorch environment 
    env = ws.environments['AzureML-PyTorch-1.6-CPU']
    config.run_config.environment = env

    run = experiment.submit(config)
    aml_url = run.get_portal_url()
    print("Submitted to compute cluster. Click link below")
    print("")
    print(aml_url)
```

> [!TIP]
> Wenn Sie beim Erstellen ihres Computeclusters einen anderen Namen verwendet haben, stellen Sie sicher, dass Sie auch den Namen im Code `compute_target='cpu-cluster'` anpassen.

### <a name="understand-the-code-changes"></a>Erläuterung der Codeänderungen

Das Steuerungsskript ähnelt dem Skript aus [Teil 3 dieser Reihe](tutorial-1st-experiment-sdk-train.md), enthält aber die folgenden neuen Zeilen:

:::row:::
   :::column span="":::
      `dataset = Dataset.File.from_files( ... )`
   :::column-end:::
   :::column span="2":::
      Ein [Dataset](/python/api/azureml-core/azureml.core.dataset.dataset) wird verwendet, um auf die Daten zu verweisen, die Sie in Azure Blob Storage hochgeladen haben. Datasets stellen eine Abstraktionsschicht über Ihren Daten dar, die eine höhere Zuverlässigkeit und Vertrauenswürdigkeit sicherstellt.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig(...)`
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) wurde geändert und enthält jetzt eine Liste von Argumenten, die an `train.py` übergeben werden. Das `dataset.as_named_input('input').as_mount()`-Argument bedeutet, dass das angegebene Verzeichnis im Computeziel _eingebunden_ wird.
   :::column-end:::
:::row-end:::

## <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit-to-cloud"></a> Senden der Ausführung an Azure Machine Learning

Wählen Sie **Save and run script in terminal** (Skript speichern und im Terminal ausführen) aus, um das Skript *run-pytorch-data.py* auszuführen.  Bei dieser Ausführung wird das Modell im Computecluster mithilfe der von Ihnen hochgeladenen Daten trainiert.

Dieser Code gibt eine URL für das Experiment in Azure Machine Learning Studio aus. Wenn Sie zu diesem Link navigieren, können Sie Ihren Code bei der Ausführung sehen.

[!INCLUDE [amlinclude-info](../../includes/machine-learning-py38-ignore.md)]


### <a name="inspect-the-log-file"></a><a name="inspect-log"></a> Überprüfen der Protokolldatei

Wechseln Sie in Studio zum Experimentlauf (durch Auswählen der vorherigen URL-Ausgabe), gefolgt von **Ausgaben und Protokolle**. Wählen Sie die Datei `70_driver_log.txt` aus. Scrollen Sie in der Protokolldatei nach unten, bis Sie die folgende Ausgabe sehen:

```txt
Processing 'input'.
Processing dataset FileDataset
{
  "source": [
    "('workspaceblobstore', 'datasets/cifar10')"
  ],
  "definition": [
    "GetDatastoreFiles"
  ],
  "registration": {
    "id": "XXXXX",
    "name": null,
    "version": null,
    "workspace": "Workspace.create(name='XXXX', subscription_id='XXXX', resource_group='X')"
  }
}
Mounting input to /tmp/tmp9kituvp3.
Mounted input to /tmp/tmp9kituvp3 as folder.
Exit __enter__ of DatasetContextManager
Entering Run History Context Manager.
Current directory:  /mnt/batch/tasks/shared/LS_root/jobs/dsvm-aml/azureml/tutorial-session-3_1600171983_763c5381/mounts/workspaceblobstore/azureml/tutorial-session-3_1600171983_763c5381
Preparing to call script [ train.py ] with arguments: ['--data_path', '$input', '--learning_rate', '0.003', '--momentum', '0.92']
After variable expansion, calling script [ train.py ] with arguments: ['--data_path', '/tmp/tmp9kituvp3', '--learning_rate', '0.003', '--momentum', '0.92']

Script type = None
===== DATA =====
DATA PATH: /tmp/tmp9kituvp3
LIST FILES IN DATA PATH...
['cifar-10-batches-py', 'cifar-10-python.tar.gz']
```

Beachten Sie Folgendes:

- Azure Machine Learning hat Blob Storage in den Computecluster automatisch eingebunden.
- ``dataset.as_named_input('input').as_mount()``, das im Steuerungsskript verwendet wird, wird in den Bereitstellungspunkt aufgelöst.


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie jetzt mit einem anderen Tutorial fortfahren oder eigene Trainingsausführungen starten möchten, gehen Sie zu [Nächste Schritte](#next-steps).

### <a name="stop-compute-instance"></a>Beenden der Compute-Instanz

Wenn Sie die Compute-Instanz jetzt nicht verwenden möchten, beenden Sie sie:

1. Wählen Sie in Studio auf der linken Seite **Compute** aus.
1. Wählen Sie auf den oberen Registerkarten **Compute-Instanzen** aus.
1. Wählen Sie in der Liste die Compute-Instanz aus.
1. Wählen Sie auf der oberen Symbolleiste **Beenden** aus.


### <a name="delete-all-resources"></a>Löschen aller Ressourcen

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Sie können die Ressourcengruppe auch behalten und einen einzelnen Arbeitsbereich löschen. Zeigen Sie die Eigenschaften des Arbeitsbereichs an, und klicken Sie auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie erfahren, wie Daten mithilfe eines `Datastore` in Azure hochgeladen werden. Der Datenspeicher diente als Cloudspeicher für Ihren Arbeitsbereich und als permanenter und flexibler Ort für die Aufbewahrung Ihrer Daten.

Sie haben gelernt, wie Sie Ihr Trainingsskript an der Befehlszeile so verändern, dass es einen Datenpfad annimmt. Durch Verwendung eines `Dataset` konnten Sie ein Verzeichnis für die Remoteausführung bereitstellen.

Nachdem Sie nun über ein Modell verfügen, lernen Sie Folgendes:

> [!div class="nextstepaction"]
> [Bereitstellen von Modellen mit Azure Machine Learning](how-to-deploy-and-where.md).
