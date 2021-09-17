---
title: 'Tutorial: Trainieren eines ersten Machine Learning-Modells in Python '
titleSuffix: Azure Machine Learning
description: Trainieren eines Machine Learning-Modells in Azure Machine Learning Dies ist Teil 2 einer dreiteiligen Reihe mit ersten Schritten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 08/18/2021
ms.custom: devx-track-python, contperf-fy21q3, FY21Q4-aml-seo-hack, contperf-fy21q
ms.openlocfilehash: f8ff5e9d5a7b35bcc4ada9fd600d28b54ead57a9
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634349"
---
# <a name="tutorial-train-your-first-machine-learning-model-part-2-of-3"></a>Tutorial: Trainieren Ihres ersten Machine Learning-Modells (Teil 2 von 3)

In diesem Tutorial wird gezeigt, wie Sie ein Machine Learning-Modell in Azure Machine Learning trainieren.  Dieses Tutorial ist der *zweite Teil einer dreiteiligen Reihe*.

 In [Teil 1: Ausführen von „Hello World!“](tutorial-1st-experiment-hello-world.md) der Reihe haben Sie erfahren, wie Sie ein Steuerungsskript verwenden, um einen Auftrag in der Cloud auszuführen.  

In diesem Tutorial führen Sie den nächsten Schritt aus, indem Sie ein Skript senden, das ein Machine Learning-Modell trainiert. Dieses Beispiel hilft Ihnen zu verstehen, wie Azure Machine Learning ein konsistentes Verhalten zwischen lokalem Debuggen und Remoteausführungen erleichtert.

In diesem Tutorial führen Sie Folgendes durch:

> [!div class="checklist"]
> * Erstellen Sie ein Trainingsskript.
> * Definieren einer Azure Machine Learning-Umgebung mit Conda
> * Erstellen eines Steuerungsskripts
> * Kennenlernen der Azure Machine Learning-Klassen (`Environment`, `Run`, `Metrics`)
> * Senden und Ausführen des Trainingsskripts
> * Anzeigen Ihrer Codeausgabe in der Cloud
> * Protokollieren von Metriken in Azure Machine Learning
> * Anzeigen der Metriken in der Cloud

## <a name="prerequisites"></a>Voraussetzungen

- Durcharbeitung von [Teil 1](tutorial-1st-experiment-hello-world.md) der Reihe.

## <a name="create-training-scripts"></a>Erstellen der Trainingsskripts

Zuerst definieren Sie die Architektur des neuronalen Netzes in der Datei *model.py*. Der gesamte Trainingscode (einschließlich *model.py*) wird im Unterverzeichnis `src` gespeichert.

Der Trainingscode stammt aus [diesem Einführungsbeispiel](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html) von PyTorch. Beachten Sie, dass die Azure Machine Learning-Konzepte für beliebigen Machine Learning-Code gelten und nicht nur für PyTorch.

1. Erstellen Sie die Datei *model.py* im Unterordner **src**. Kopieren Sie diesen Code in die Datei:

    ```python
    import torch.nn as nn
    import torch.nn.functional as F
    class Net(nn.Module):
        def __init__(self):
            super(Net, self).__init__()
            self.conv1 = nn.Conv2d(3, 6, 5)
            self.pool = nn.MaxPool2d(2, 2)
            self.conv2 = nn.Conv2d(6, 16, 5)
            self.fc1 = nn.Linear(16 * 5 * 5, 120)
            self.fc2 = nn.Linear(120, 84)
            self.fc3 = nn.Linear(84, 10)
        def forward(self, x):
            x = self.pool(F.relu(self.conv1(x)))
            x = self.pool(F.relu(self.conv2(x)))
            x = x.view(-1, 16 * 5 * 5)
            x = F.relu(self.fc1(x))
            x = F.relu(self.fc2(x))
            x = self.fc3(x)
            return x
    ```
1. Wählen Sie in der Symbolleiste die Option **Speichern** aus, um die Datei zu speichern.  Schließen Sie die Registerkarte, wenn Sie möchten.

1. Definieren Sie als Nächstes ebenfalls im Unterordner **src** das Trainingsskript. Mit diesem Skript wird das CIFAR10-Dataset mithilfe der `torchvision.dataset`-APIs von PyTorch heruntergeladen, das in *model.py* definierte Netzwerk eingerichtet und zwei Epochen lang das Training mit Standard-SGD und Kreuzentropieverlust durchgeführt.

    Erstellen Sie im Unterordner **src** das Skript *train.py*:

    ```python
    import torch
    import torch.optim as optim
    import torchvision
    import torchvision.transforms as transforms
    
    from model import Net
    
    # download CIFAR 10 data
    trainset = torchvision.datasets.CIFAR10(
        root="../data",
        train=True,
        download=True,
        transform=torchvision.transforms.ToTensor(),
    )
    trainloader = torch.utils.data.DataLoader(
        trainset, batch_size=4, shuffle=True, num_workers=2
    )
    
    if __name__ == "__main__":
    
        # define convolutional network
        net = Net()
    
        # set up pytorch loss /  optimizer
        criterion = torch.nn.CrossEntropyLoss()
        optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)
    
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
                    print(f"epoch={epoch + 1}, batch={i + 1:5}: loss {loss:.2f}")
                    running_loss = 0.0
    
        print("Finished Training")
    ```

1. Sie verfügen jetzt über die folgende Ordnerstruktur:

    :::image type="content" source="media/tutorial-1st-experiment-sdk-train/directory-structure.png" alt-text="Verzeichnisstruktur mit „train.py“ im Unterverzeichnis „src“":::


## <a name="test-locally"></a><a name="test-local"></a> Lokales Testen

Wählen Sie die Option **Save and run script in terminal** (Skript speichern und im Terminal ausführen) aus, um das Skript *train.py* direkt auf der Compute-Instanz auszuführen.

Klicken Sie nach Abschluss des Skripts oberhalb der Dateiordner auf **Aktualisieren**. Der neue Datenordner mit dem Namen **get-started/data** wird angezeigt. Erweitern Sie diesen Ordner, um die heruntergeladenen Daten anzuzeigen.  

:::image type="content" source="media/tutorial-1st-experiment-hello-world/directory-with-data.png" alt-text="Screenshot: Neu erstellter Datenordner durch lokale Ausführung der Datei":::


## <a name="create-the-control-script"></a><a name="create-local"></a> Erstellen des Steuerungsskripts

Der Unterschied zwischen dem folgenden Steuerungsskript und dem Skript, das Sie zum Übermitteln von „Hello World!“ verwendet haben, besteht darin, dass Sie einige zusätzliche Zeilen zum Festlegen der Umgebung hinzufügen.

Erstellen Sie im Ordner **get-started** eine neue Python-Datei mit dem Namen `run-pytorch.py`:

```python
# run-pytorch.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig

if __name__ == "__main__":
    ws = Workspace.from_config()
    experiment = Experiment(workspace=ws, name='day1-experiment-train')
    config = ScriptRunConfig(source_directory='./src',
                             script='train.py',
                             compute_target='cpu-cluster')

    # use curated pytorch environment 
    env = ws.environments['AzureML-PyTorch-1.6-CPU']
    config.run_config.environment = env

    run = experiment.submit(config)

    aml_url = run.get_portal_url()
    print(aml_url)
```

> [!TIP]
> Wenn Sie beim Erstellen ihres Computeclusters einen anderen Namen verwendet haben, stellen Sie sicher, dass Sie auch den Namen im Code `compute_target='cpu-cluster'` anpassen.

### <a name="understand-the-code-changes"></a>Erläuterung der Codeänderungen

:::row:::
   :::column span="":::
      `env = ...`
   :::column-end:::
   :::column span="2":::
      Bei Azure Machine Learning wird das Konzept einer [Umgebung](/python/api/azureml-core/azureml.core.environment.environment) verwendet, um eine reproduzierbare versionierte Python-Umgebung zum Ausführen von Experimenten darzustellen. Hier verwenden Sie eine der [zusammengestellten Umgebungen](how-to-use-environments.md#use-a-curated-environment).  Eine Umgebung kann auch ganz einfach aus einer lokalen Conda- oder PIP-Umgebung erstellt werden.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config.run_config.environment = env`
   :::column-end:::
   :::column span="2":::
      Fügt die Umgebung der [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig)-Klasse hinzu.
   :::column-end:::
:::row-end:::

## <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit"></a> Senden der Ausführung an Azure Machine Learning

1. Wählen Sie **Save and run script in terminal** (Skript speichern und im Terminal ausführen) aus, um das Skript *run-pytorch.py* auszuführen.

1. In dem sich öffnenden Terminalfenster wird ein Link angezeigt. Wählen Sie den Link aus, um die Ausführung anzuzeigen.

    [!INCLUDE [amlinclude-info](../../includes/machine-learning-py38-ignore.md)]

### <a name="view-the-output"></a>Anzeigen der Ausgabe

1. Auf der Seite, die sich öffnet, sehen Sie den Status der Ausführung. Wenn Sie dieses Skript zum ersten Mal ausführen, erstellt Azure Machine Learning ein neues Docker-Image aus Ihrer PyTorch-Umgebung. Die gesamte Ausführung kann drei bis vier Minuten dauern.  Dieses Image wird auch in zukünftigen Ausführungen verwendet, um die Dauer deutlich zu verkürzen.
1. Die Docker-Buildprotokolle werden in Azure Machine Learning Studio angezeigt. Wählen Sie die Registerkarte **Ausgaben und Protokolle** und dann **20_image_build_log.txt** aus.
1. Wenn der Ausführungsstatus **Abgeschlossen** lautet, wählen Sie **Ausgabe und Protokolle** aus.
1. Wählen Sie **70_driver_log.txt** aus, um die Ausgabe der Ausführung anzuzeigen.

```txt
Downloading https://www.cs.toronto.edu/~kriz/cifar-10-python.tar.gz to ./data/cifar-10-python.tar.gz
...
Files already downloaded and verified
epoch=1, batch= 2000: loss 2.19
epoch=1, batch= 4000: loss 1.82
epoch=1, batch= 6000: loss 1.66
epoch=1, batch= 8000: loss 1.58
epoch=1, batch=10000: loss 1.52
epoch=1, batch=12000: loss 1.47
epoch=2, batch= 2000: loss 1.39
epoch=2, batch= 4000: loss 1.38
epoch=2, batch= 6000: loss 1.37
epoch=2, batch= 8000: loss 1.33
epoch=2, batch=10000: loss 1.31
epoch=2, batch=12000: loss 1.27
Finished Training
```

Wenn ein Fehler vom Typ `Your total snapshot size exceeds the limit` (Gesamtgröße der Momentaufnahme überschreitet den Grenzwert) angezeigt wird, befindet sich der Ordner **data** unter dem Wert `source_directory`, der in `ScriptRunConfig` verwendet wird.

Wählen Sie am Ende des Ordners die Auslassungszeichen ( **...** ) und dann die Option **Verschieben** aus, um **data** in den Ordner **get-started** zu verschieben.  



## <a name="log-training-metrics"></a><a name="log"></a> Protokollieren von Trainingsmetriken

Nachdem Sie nun über ein Modelltraining in Azure Machine Learning verfügen, beginnen Sie mit dem Nachverfolgen einiger Leistungsmetriken.

Das aktuelle Trainingsskript gibt Metriken auf dem Terminal aus. Azure Machine Learning bietet einen Mechanismus zum Protokollieren von Metriken mit weiteren Funktionen. Durch Hinzufügen von wenigen Codezeilen können Sie Metriken in Studio visualisieren und Metriken aus mehreren Ausführungen vergleichen.

### <a name="modify-trainpy-to-include-logging"></a>Ändern von *train.py* zur Einbindung der Protokollierung

1. Erweitern Sie Ihr Skript *train.py* um zwei weitere Codezeilen:
    
    ```python
    import torch
    import torch.optim as optim
    import torchvision
    import torchvision.transforms as transforms
    from model import Net
    from azureml.core import Run
    # ADDITIONAL CODE: get run from the current context
    run = Run.get_context()
    # download CIFAR 10 data
    trainset = torchvision.datasets.CIFAR10(
        root='./data',
        train=True,
        download=True,
        transform=torchvision.transforms.ToTensor()
    )
    trainloader = torch.utils.data.DataLoader(
        trainset,
        batch_size=4,
        shuffle=True,
        num_workers=2
    )
    if __name__ == "__main__":
        # define convolutional network
        net = Net()
        # set up pytorch loss /  optimizer
        criterion = torch.nn.CrossEntropyLoss()
        optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)
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
                    # ADDITIONAL CODE: log loss metric to AML
                    run.log('loss', loss)
                    print(f'epoch={epoch + 1}, batch={i + 1:5}: loss {loss:.2f}')
                    running_loss = 0.0
        print('Finished Training')
    ```

2. **Speichern** Sie diese Datei, und schließen Sie die Registerkarte, wenn Sie möchten.

#### <a name="understand-the-additional-two-lines-of-code"></a>Erläuterungen zu den beiden zusätzlichen Codezeilen

In *train.py* greifen Sie _innerhalb_ des Trainingsskripts selbst mit der Methode `Run.get_context()` auf das Run-Objekt zu und verwenden es zum Protokollieren der Metriken:

```python
# ADDITIONAL CODE: get run from the current context
run = Run.get_context()

...
# ADDITIONAL CODE: log loss metric to AML
run.log('loss', loss)
```

Vorteile der Metriken in Azure Machine Learning:

- Metriken sind nach Experiment und Ausführung strukturiert und können daher leicht nachverfolgt und verglichen werden.
- Sie verfügen über eine Benutzeroberfläche zur Visualisierung der Trainingsleistung in Studio.
- Sie sind skalierbar konzipiert, sodass Sie auch bei Hunderten von Experimenten von diesen Vorteilen profitieren.

### <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit-again"></a> Senden der Ausführung an Azure Machine Learning

Wählen Sie die Registerkarte für das Skript *run-pytorch.py* und dann die Option **Save and run script in terminal** (Skript speichern und im Terminal ausführen) aus, um das Skript *run-pytorch.py* erneut auszuführen. 

Bei diesem Zugriff auf Studio öffnen Sie die Registerkarte **Metriken**, auf der nun Liveupdates zum Modelltrainingsverlust angezeigt werden. Es kann ein bis zwei Minuten dauern, bis das Training beginnt.  

:::image type="content" source="media/tutorial-1st-experiment-sdk-train/logging-metrics.png" alt-text="Diagramm: Trainingsverlust auf der Registerkarte „Metriken“":::

## <a name="next-steps"></a>Nächste Schritte

In dieser Sitzung haben Sie ein Upgrade von einem einfachen „Hello World!“-Skript zu einem realistischeren Trainingsskript durchgeführt, das eine bestimmte Python-Umgebung für die Ausführung benötigt. Sie haben erfahren, wie Sie zusammengestellte Azure Machine Learning-Umgebungen verwenden. Schließlich haben Sie erfahren, wie Sie mit einigen wenigen Codezeilen Metriken für Azure Machine Learning protokollieren können.

Es gibt weitere Möglichkeiten, Azure Machine Learning-Umgebungen zu erstellen, z. B. [mit der PIP-Datei „requirements.txt“](/python/api/azureml-core/azureml.core.environment.environment#from-pip-requirements-name--file-path-) oder [aus einer vorhandenen lokalen Conda-Umgebung](/python/api/azureml-core/azureml.core.environment.environment#from-existing-conda-environment-name--conda-environment-name-).

In der nächsten Sitzung sehen Sie, wie Sie mit Daten in Azure Machine Learning arbeiten, indem Sie das CIFAR10-Dataset in Azure hochladen.

> [!div class="nextstepaction"]
> [Tutorial: Verwenden eigener Daten](tutorial-1st-experiment-bring-data.md)

>[!NOTE] 
> Wenn Sie die Tutorialreihe hier beenden und nicht mit dem nächsten Schritt fortfahren möchten, sollten Sie nicht vergessen, [Ihre Ressourcen zu bereinigen](tutorial-1st-experiment-bring-data.md#clean-up-resources).
