---
title: Leitfaden zum verteilten GPU-Training
titleSuffix: Azure Machine Learning
description: Erfahren Sie mehr über die bewährten Methoden für verteiltes Training mit durch Azure Machine Learning unterstützten Frameworks wie MPI, Horovod, DeepSpeed, PyTorch, PyTorch Lightning, Hugging Face Transformers, TensorFlow und InfiniBand.
author: fuhuifang
ms.author: fufang
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 10/21/2021
ms.openlocfilehash: 7c4d85f1f1c2d0c628b3e724f1af0297888504d5
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131557279"
---
# <a name="distributed-gpu-training-guide"></a>Leitfaden zum verteilten GPU-Training

Erfahren Sie mehr über die Verwendung von verteiltem GPU-Trainingscode in Azure Machine Learning (ML). Dieser Artikel vermittelt kein Basiswissen über verteiltes Training.  Er soll Sie vielmehr beim Ausführen Ihres vorhandenen verteilten Trainingscodes auf Azure Machine Learning unterstützen. Dazu dienen Tipps und Beispiele, die Sie für jedes Framework befolgen können:

* Message Passing Interface (MPI)
    * Horovod
    * DeepSpeed
    * Umgebungsvariablen aus Open MPI
* PyTorch
    * Initialisierung von Prozessgruppen
    * Startoptionen
    * DistributedDataParallel (Start pro Prozess)
    * Verwendung von `torch.distributed.launch` (Start pro Knoten)
    * PyTorch Lightning
    * Hugging Face Transformers
* TensorFlow
    * Umgebungsvariablen für TensorFlow (TF_CONFIG)
* Beschleunigen des GPU-Trainings mit InfiniBand

## <a name="prerequisites"></a>Voraussetzungen

Lernen Sie diese [grundlegenden Konzepte des verteilten GPU-Trainings](concept-distributed-training.md) wie _Datenparallelität_, _verteilte Datenparallelität_ und _Modellparallelität_ kennen.

> [!TIP]
> Wenn Sie nicht wissen, welche Art von Parallelität verwendet werden soll, sollten Sie in mehr als 90 % der Fälle __Verteilte Datenparallelität verwenden__.

## <a name="mpi"></a>MPI

Azure ML bietet einen [MPI-Auftrag](https://www.mcs.anl.gov/research/projects/mpi/) zum Starten einer bestimmten Anzahl von Prozessen in jedem Knoten. Sie können diesen Ansatz verwenden, um verteiltes Training entweder mit dem Pro-Prozess- oder Pro-Knoten-Startprogramm auszuführen. Dies hängt davon ab, ob `process_count_per_node` für das Pro-Knoten-Startprogramm auf 1 (Standard) oder für das Pro-Prozess-Startprogramm auf die Anzahl der Geräte/GPUs für das Pro-Prozess-Startprogramm festgelegt ist. Azure ML erstellt den vollständigen MPI-Startbefehl (`mpirun`) im Hintergrund.  Sie können keine eigenen vollständigen Hauptknoten-Startprogramm-Befehle wie `mpirun` oder `DeepSpeed launcher` bereitstellen.

> [!TIP]
> Für das Docker-Basisimage, das von einem Azure Machine Learning-MPI-Auftrag verwendet wird, muss eine MPI-Bibliothek installiert sein. [Open MPI](https://www.open-mpi.org/) ist in allen [AzureML-GPU-Basisimages](https://github.com/Azure/AzureML-Containers) enthalten. Wenn Sie ein benutzerdefiniertes Docker-Image verwenden, müssen Sie sicherstellen, dass das Image eine MPI-Bibliothek enthält. Open MPI wird empfohlen, aber Sie können auch eine andere MPI-Implementierung wie Intel MPI verwenden. Azure ML stellt auch [kuratierte Umgebungen](resource-curated-environments.md) für beliebte Frameworks bereit. 

Führen Sie die folgenden Schritte aus, um verteiltes Training mit MPI ausführen zu können:

1. Verwenden Sie eine Azure ML-Umgebung mit dem bevorzugten Deep Learning-Framework und MPI. Azure ML stellt [kuratierte Umgebungen](resource-curated-environments.md) für beliebte Frameworks bereit.
1. Definieren Sie `MpiConfiguration` mit `process_count_per_node` und `node_count`. `process_count_per_node` sollte gleich der Anzahl von GPUs pro Knoten für den Start pro Prozess sein, oder auf 1 (Standard) für den Start pro Knoten festgelegt sein, wenn das Benutzerskript für das Starten der Prozesse pro Knoten verantwortlich ist.
1. Übergeben Sie das `MpiConfiguration`-Objekt an den `distributed_job_config`-Parameter von `ScriptRunConfig`.

```python
from azureml.core import Workspace, ScriptRunConfig, Environment, Experiment
from azureml.core.runconfig import MpiConfiguration

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
distr_config = MpiConfiguration(process_count_per_node=4, node_count=2)

run_config = ScriptRunConfig(
  source_directory= './src',
  script='train.py',
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)

# submit the run configuration to start the job
run = Experiment(ws, "experiment_name").submit(run_config)
```

### <a name="horovod"></a>Horovod

Verwenden Sie die MPI-Auftragskonfiguration, wenn Sie [Horovod](https://horovod.readthedocs.io/en/stable/index.html) für verteiltes Training mit dem Deep Learning-Framework verwenden.

Stellen Sie sicher, dass Ihr Code den folgenden Tipps entspricht:

* Der Trainingscode wird ordnungsgemäß mit Horovod instrumentiert, bevor die Azure-ML-Teile hinzugefügt werden.
* Ihre Azure ML-Umgebung enthält Horovod und MPI. Die kuratierten GPU-Umgebungen PyTorch und TensorFlow sind mit Horovod und seinen Abhängigkeiten vorkonfiguriert.
* Erstellen Sie eine `MpiConfiguration` mit der gewünschten Verteilung.

### <a name="horovod-example"></a>Horovod-Beispiel

* [azureml-examples: Verteiltes TensorFlow-Training mit Horovod](https://github.com/Azure/azureml-examples/tree/main/python-sdk/workflows/train/tensorflow/mnist-distributed-horovod)

### <a name="deepspeed"></a>DeepSpeed

Verwenden Sie nicht das benutzerdefinierte Startprogramm von DeepSpeed, um verteiltes Training mit der [DeepSpeed](https://www.deepspeed.ai/)-Bibliothek in Azure ML auszuführen. Konfigurieren Sie stattdessen einen MPI-Auftrag, um den Trainingsauftrag [mit MPI](https://www.deepspeed.ai/getting-started/#mpi-and-azureml-compatibility) zu starten.

Stellen Sie sicher, dass Ihr Code den folgenden Tipps entspricht:

* Ihre Azure ML-Umgebung enthält DeepSpeed und seine Abhängigkeiten, Open MPI und mpi4py.
* Erstellen Sie eine `MpiConfiguration` mit der gewünschten Verteilung.

### <a name="deepseed-example"></a>DeepSeed-Beispiel

* [azureml-examples: Verteiltes Training mit DeepSpeed auf CIFAR-10](https://github.com/Azure/azureml-examples/tree/main/python-sdk/workflows/train/deepspeed/cifar)

### <a name="environment-variables-from-open-mpi"></a>Umgebungsvariablen aus Open MPI

Beim Ausführen von MPI-Aufträgen mit Open MPI-Images werden die folgenden Umgebungsvariablen für jeden Prozess gestartet:

1. `OMPI_COMM_WORLD_RANK` – der Rang des Prozesses
2. `OMPI_COMM_WORLD_SIZE` – die Weltgröße
3. `AZ_BATCH_MASTER_NODE` – primäre Adresse mit Port, `MASTER_ADDR:MASTER_PORT`
4. `OMPI_COMM_WORLD_LOCAL_RANK` – der lokale Rang des Prozesses auf dem Knoten
5. `OMPI_COMM_WORLD_LOCAL_SIZE` – Anzahl der Prozesse auf dem Knoten

> [!TIP]
> Trotz des Namens entspricht die Umgebungsvariable `OMPI_COMM_WORLD_NODE_RANK` nicht dem `NODE_RANK`. Um das Pro-Knoten-Startprogramm zu verwenden, legen Sie `process_count_per_node=1` fest, und verwenden Sie `OMPI_COMM_WORLD_RANK` als `NODE_RANK`.

## <a name="pytorch"></a>PyTorch

Azure ML unterstützt die Ausführung verteilter Aufträge mithilfe der nativen verteilten Trainingsfunktionen von PyTorch (`torch.distributed`).

> [!TIP]
> Für Datenparallelität ist die [offizielle PyTorch-Anleitung](https://pytorch.org/tutorials/intermediate/ddp_tutorial.html#comparison-between-dataparallel-and-distributeddataparallel) die Verwendung von DistributedDataParallel (DDP) über DataParallel für verteiltes Training sowohl mit einem einzelnen als auch mehreren Knoten. PyTorch [empfiehlt auch die Verwendung von DistributedDataParallel über das Multiprozessorpaket](https://pytorch.org/docs/stable/notes/cuda.html#use-nn-parallel-distributeddataparallel-instead-of-multiprocessing-or-nn-dataparallel). Azure Machine Learning-Dokumentation und -Beispiele konzentrieren sich daher auf das DistributedDataParallel-Training.

### <a name="process-group-initialization"></a>Initialisierung von Prozessgruppen

Eine Gruppe von Prozessen, die sich gegenseitig kennen und über ein Back-End miteinander kommunizieren können, bilden das Rückgrat eines verteilten Trainings. Für PyTorch wird die Prozessgruppe erstellt, indem [torch.distributed.init_process_group](https://pytorch.org/docs/stable/distributed.html#torch.distributed.init_process_group) in __allen verteilten Prozessen__ aufgerufen wird, um zusammen eine Prozessgruppe zu bilden.

```
torch.distributed.init_process_group(backend='nccl', init_method='env://', ...)
```

Die am häufigsten verwendeten Kommunikations-Back-Ends sind `mpi`, `nccl` und `gloo`. Für GPU-basiertes Training wird `nccl` empfohlen, um die beste Leistung zu erzielen, und sollte nach Möglichkeit verwendet werden. 

`init_method` gibt an, wie die einzelnen Prozesse sich gegenseitig entdecken und wie sie die Prozessgruppe mithilfe des Kommunikations-Back-Ends initialisieren und überprüfen. Wenn `init_method` nicht angegeben ist, verwendet PyTorch standardmäßig die Methode zum Initialisieren der Umgebungsvariablen (`env://`). Sie sollten die Initialisierungsmethode `init_method` in Ihrem Trainingscode zum Ausführen verteilter PyTorch-Aufträge in Azure ML verwenden.  PyTorch sucht für die Initialisierung nach den folgenden Umgebungsvariablen:

- **`MASTER_ADDR`** : IP-Adresse des Computers, auf dem der Prozess mit dem Rang 0 gehostet wird.
- **`MASTER_PORT`** : Ein freier Port auf dem Computer, auf dem der Prozess mit dem Rang 0 gehostet wird.
- **`WORLD_SIZE`** : Gesamtzahl von Prozessen. Dieser Wert sollte der Gesamtzahl von Geräten (GPU) entsprechen, die für das verteilte Training verwendet werden.
- **`RANK`** : Der (globale) Rang des aktuellen Prozesses. Die möglichen Werte sind 0 bis „WORLD_SIZE - 1“.

Weitere Informationen zur Initialisierung von Prozessgruppen finden Sie in der [PyTorch-Dokumentation](https://pytorch.org/docs/stable/distributed.html#torch.distributed.init_process_group).

Darüber hinaus benötigen viele Anwendungen auch die folgenden Umgebungsvariablen:
- **`LOCAL_RANK`** : Der lokale (relative) Rang des Prozesses auf dem Knoten. Die möglichen Werte sind 0 bis „Anzahl von Prozessen auf dem Knoten - 1“. Diese Informationen sind nützlich, da viele Vorgänge wie die Datenvorbereitung nur einmal pro Knoten ausgeführt werden sollten – und in der Regel auf local_rank = 0.
- **`NODE_RANK`** : Der Rang des Knotens in Bezug auf das Training mit mehreren Knoten. Die möglichen Werte sind 0 bis „Gesamtknotenzahl - 1“.

### <a name="pytorch-launch-options"></a>PyTorch-Startoptionen

Der PyTorch-Auftrag in Azure ML unterstützt zwei Arten von Optionen zum Starten verteilter Trainings:

- __Pro-Prozess-Startprogramm__: Das System startet alle verteilten Prozesse für Sie mit allen relevanten Informationen (z. B. Umgebungsvariablen), um die Prozessgruppe einzurichten.
- __Pro-Knoten-Startprogramm__: Sie stellen Azure ML mit dem Starthilfsprogramm zur Verfügung, das auf jedem Knoten ausgeführt wird. Das Starthilfsprogramm startet die jeweiligen Prozesse auf einem bestimmten Knoten. Innerhalb jedes Knotens werden `RANK` und `LOCAL_RANK` lokal vom Startprogramm eingerichtet. Sowohl das Hilfsprogramm **torch.distributed.launch** als auch PyTorch Lightning gehört zu dieser Kategorie.

Es gibt keine grundlegenden Unterschiede zwischen diesen Startoptionen. Die Wahl hängt größtenteils von Ihren Vorlieben oder den Konventionen der Frameworks/Bibliotheken ab, die basierend auf Vanilla PyTorch erstellt wurden (z. B. Lightning oder Hugging Face).

In den folgenden Abschnitten wird ausführlicher erläutert, wie Sie PyTorch-Aufträge in Azure ML für die einzelnen Startoptionen konfigurieren.

### <a name="distributeddataparallel-per-process-launch"></a><a name="per-process-launch"></a> DistributedDataParallel (Start pro Prozess)

Sie müssen kein Starthilfsprogramm wie `torch.distributed.launch` verwenden. So führen Sie einen verteilten PyTorch-Auftrag aus:

1. Geben Sie das Trainingsskript und die Argumente an.
1. Erstellen Sie eine `PyTorchConfiguration`, und geben Sie den Wert für `process_count` und `node_count` an. `process_count` steht für die Gesamtzahl von Prozessen, die Sie für Ihren Auftrag ausführen möchten. `process_count` sollte in der Regel gleich `# GPUs per node x # nodes` sein. Wenn `process_count` nicht angegeben ist, wird von Azure ML standardmäßig ein Prozess pro Knoten gestartet.

Azure ML legt die Umgebungsvariablen `MASTER_ADDR`, `MASTER_PORT`, `WORLD_SIZE` und `NODE_RANK` auf den einzelnen Knoten sowie die Umgebungsvariablen `RANK` und `LOCAL_RANK` auf Prozessebene fest.

Verwenden Sie das Azure ML Python SDK `>= 1.22.0`, um diese Option für das Training mit mehreren Prozessen pro Knoten zu verwenden. Process_count wurde in 1.22.0 eingeführt.

```python
from azureml.core import ScriptRunConfig, Environment, Experiment
from azureml.core.runconfig import PyTorchConfiguration

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
distr_config = PyTorchConfiguration(process_count=8, node_count=2)

run_config = ScriptRunConfig(
  source_directory='./src',
  script='train.py',
  arguments=['--epochs', 50],
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)

run = Experiment(ws, 'experiment_name').submit(run_config)
```

> [!TIP]
> Wenn mit Ihrem Trainingsskript Informationen wie lokaler Rang oder Rang als Skriptargumente übergeben werden, können Sie in den Argumenten auf die Umgebungsvariable(n) verweisen:
>
> ```python
> arguments=['--epochs', 50, '--local_rank', $LOCAL_RANK]
> ```

### <a name="pytorch-per-process-launch-example"></a>Pytorch-Beispiel für Start pro Prozess

- [azureml-examples: Verteiltes Training mit PyTorch auf CIFAR-10](https://github.com/Azure/azureml-examples/tree/main/python-sdk/workflows/train/pytorch/cifar-distributed)

### <a name="using-torchdistributedlaunch-per-node-launch"></a><a name="per-node-launch"></a> Verwendung von `torch.distributed.launch` (Start pro Knoten)

Für PyTorch wird unter [torch.distributed.launch](https://pytorch.org/docs/stable/distributed.html#launch-utility) ein Hilfsprogramm für das Starten bereitgestellt, mit dem Sie mehrere Prozesse pro Knoten starten können. Mit dem Modul `torch.distributed.launch` werden auf den einzelnen Knoten jeweils mehrere Trainingsprozesse erzeugt.

Die folgenden Schritte zeigen, wie ein PyTorch-Auftrag mit einem Pro-Knoten-Startprogramm auf Azure ML konfiguriert wird.  Der Auftrag entspricht dem Ausführen des folgenden Befehls:

```shell
python -m torch.distributed.launch --nproc_per_node <num processes per node> \
  --nnodes <num nodes> --node_rank $NODE_RANK --master_addr $MASTER_ADDR \
  --master_port $MASTER_PORT --use_env \
  <your training script> <your script arguments>
```

1. Geben Sie den Befehl `torch.distributed.launch` für den Parameter `command` des Konstruktors `ScriptRunConfig` an. Azure ML führt diesen Befehl auf allen Knoten Ihres Trainingsclusters aus. Der Wert `--nproc_per_node` sollte kleiner oder gleich der Anzahl von GPUs sein, die auf einem Knoten verfügbar sind. Da MASTER_ADDR, MASTER_PORT und NODE_RANK von Azure ML festgelegt werden, können Sie im Befehl einfach auf die Umgebungsvariablen verweisen. Von Azure ML wird MASTER_PORT auf `6105` festgelegt, aber wenn Sie möchten, können Sie auch einen anderen Wert an das `--master_port`-Argument des torch.distributed.launch-Befehls übergeben. (Mit dem Hilfsprogramm für das Starten werden die Umgebungsvariablen zurückgesetzt.)
2. Erstellen Sie ein `PyTorchConfiguration`-Element, und geben Sie den Wert für `node_count` an.

```python
from azureml.core import ScriptRunConfig, Environment, Experiment
from azureml.core.runconfig import PyTorchConfiguration

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
distr_config = PyTorchConfiguration(node_count=2)
launch_cmd = "python -m torch.distributed.launch --nproc_per_node 4 --nnodes 2 --node_rank $NODE_RANK --master_addr $MASTER_ADDR --master_port $MASTER_PORT --use_env train.py --epochs 50".split()

run_config = ScriptRunConfig(
  source_directory='./src',
  command=launch_cmd,
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)

run = Experiment(ws, 'experiment_name').submit(run_config)
```

> [!TIP]
> **Training mit mehreren GPUs mit einem Knoten:** Wenn Sie das Starthilfsprogramm zum Ausführen des PyTorch-Trainings mit mehreren GPUs mit einem Knoten verwenden, müssen Sie den `distributed_job_config`-Parameter von ScriptRunConfig nicht angeben.
>
>```python
> launch_cmd = "python -m torch.distributed.launch --nproc_per_node 4 --use_env train.py --epochs 50".split()
>
> run_config = ScriptRunConfig(
>  source_directory='./src',
>  command=launch_cmd,
>  compute_target=compute_target,
>  environment=pytorch_env,
> )
> ```

### <a name="pytorch-per-node-launch-example"></a>PyTorch-Beispiel für Start pro Knoten

- [azureml-examples: Verteiltes Training mit PyTorch auf CIFAR-10](https://github.com/Azure/azureml-examples/tree/main/python-sdk/workflows/train/pytorch/cifar-distributed)

### <a name="pytorch-lightning"></a>PyTorch Lightning

[PyTorch Lightning](https://pytorch-lightning.readthedocs.io/en/stable/) ist eine einfache Open-Source-Bibliothek, die eine allgemeine Schnittstelle für PyTorch bereitstellt. Lightning abstrahiert viele der detaillierteren Konfigurationen für verteiltes Training, die für Vanilla PyTorch erforderlich sind. Mit Lightning können Sie Ihre Trainingsskripts in Einstellungen mit einer einzelnen GPU, mehreren GPUs mit einem einzelnen Knoten und mehreren GPUs mit mehreren Knoten ausführen. Im Hintergrund werden mehrere Prozesse ähnlich wie `torch.distributed.launch` für Sie gestartet.

Für das Training mit einem einzelnen Knoten (einschließlich mehrerer GPUs mit einem einzelnen Knoten) können Sie Ihren Code in Azure ML ausführen, ohne eine `distributed_job_config` angeben zu müssen. Für das Training mit mehreren Knoten erfordert Lightning, dass die folgenden Umgebungsvariablen auf jedem Knoten Ihres Trainingsclusters festgelegt werden:

- MASTER_ADDR
- MASTER_PORT
- NODE_RANK

Zum Ausführen des Lightning-Trainings mit mehreren Knoten in Azure ML können Sie im Wesentlichen den [Anleitungen für den Start pro Knoten](#per-node-launch) folgen:

- Definieren Sie die `PyTorchConfiguration`, und geben Sie den `node_count` an. Geben Sie nicht `process_count` an, da Lightning das Starten der Workerprozesse für jeden Knoten intern verarbeitet.
- Für PyTorch-Aufträge übernimmt Azure ML das Festlegen der für Lightning erforderlichen Umgebungsvariablen MASTER_ADDR, MASTER_PORT und NODE_RANK.
- Lightning übernimmt die Berechnung der Weltgröße aus den Trainer-Flags `--gpus` und `--num_nodes` und verwaltet den Rang und den lokalen Rang intern.

```python
from azureml.core import ScriptRunConfig, Experiment
from azureml.core.runconfig import PyTorchConfiguration

nnodes = 2
args = ['--max_epochs', 50, '--gpus', 2, '--accelerator', 'ddp', '--num_nodes', nnodes]
distr_config = PyTorchConfiguration(node_count=nnodes)

run_config = ScriptRunConfig(
  source_directory='./src',
  script='train.py',
  arguments=args,
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)

run = Experiment(ws, 'experiment_name').submit(run_config)
```

### <a name="hugging-face-transformers"></a>Hugging Face Transformers

Hugging Face enthält viele [Beispiele](https://github.com/huggingface/transformers/tree/master/examples) für die Verwendung der Transformers-Bibliothek mit `torch.distributed.launch`, um verteiltes Training auszuführen. Um diese Beispiele und Ihre eigenen benutzerdefinierten Trainingsskripts mithilfe der Transformers-Trainer-API auszuführen, folgen Sie dem Abschnitt [Verwenden von `torch.distributed.launch` (Start pro Knoten)](#per-node-launch).

Beispielcode für die Auftragskonfiguration zum Optimieren des umfangreichen BERT-Modells für die MNLI-Aufgabe zur Textklassifizierung mithilfe des `run_glue.py`-Skripts auf einem Knoten mit 8 GPUs:

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import PyTorchConfiguration

distr_config = PyTorchConfiguration() # node_count defaults to 1
launch_cmd = "python -m torch.distributed.launch --nproc_per_node 8 text-classification/run_glue.py --model_name_or_path bert-large-uncased-whole-word-masking --task_name mnli --do_train --do_eval --max_seq_length 128 --per_device_train_batch_size 8 --learning_rate 2e-5 --num_train_epochs 3.0 --output_dir /tmp/mnli_output".split()

run_config = ScriptRunConfig(
  source_directory='./src',
  command=launch_cmd,
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)
```

Sie können auch die Option [Start pro Prozess](#per-process-launch) verwenden, um verteiltes Training ohne Verwendung von `torch.distributed.launch` auszuführen. Beachten Sie bei Verwendung dieser Methode, dass die Transformers-[TrainingArguments](https://huggingface.co/transformers/main_classes/trainer.html?highlight=launch#trainingarguments) erwarten, dass der lokale Rang als Argument (`--local_rank`) übergeben wird. `torch.distributed.launch` übernimmt dies bei `--use_env=False`, aber wenn Sie Start pro Prozess verwenden, müssen Sie den lokalen Rang explizit als Argument an das Trainingsskript `--local_rank=$LOCAL_RANK` übergeben, da Azure ML nur die `LOCAL_RANK`-Umgebungsvariable festlegt.

## <a name="tensorflow"></a>TensorFlow

Wenn Sie [natives verteiltes TensorFlow](https://www.tensorflow.org/guide/distributed_training) in Ihrem Trainingscode verwenden, z. B. die `tf.distribute.Strategy`-API von TensorFlow 2.x, können Sie den verteilten Auftrag über Azure ML mit `TensorflowConfiguration` starten.

Geben Sie dazu ein `TensorflowConfiguration`-Objekt für den `distributed_job_config`-Parameter des `ScriptRunConfig`-Konstruktors an. Wenn Sie `tf.distribute.experimental.MultiWorkerMirroredStrategy` verwenden, geben Sie den `worker_count`-Wert in der `TensorflowConfiguration` an, der der Anzahl der Knoten für den Trainingsauftrag entspricht.

```python
from azureml.core import ScriptRunConfig, Environment, Experiment
from azureml.core.runconfig import TensorflowConfiguration

curated_env_name = 'AzureML-TensorFlow-2.3-GPU'
tf_env = Environment.get(workspace=ws, name=curated_env_name)
distr_config = TensorflowConfiguration(worker_count=2, parameter_server_count=0)

run_config = ScriptRunConfig(
  source_directory='./src',
  script='train.py',
  compute_target=compute_target,
  environment=tf_env,
  distributed_job_config=distr_config,
)

# submit the run configuration to start the job
run = Experiment(ws, "experiment_name").submit(run_config)
```

Wenn Ihr Trainingsskript die Parameterserverstrategie für verteiltes Training wie für Legacy-TensorFlow 1.x verwendet, müssen Sie auch die Anzahl der Parameterserver angeben, die im Auftrag verwendet werden sollen, z. B. `tf_config = TensorflowConfiguration(worker_count=2, parameter_server_count=1)`.

### <a name="tf_config"></a>TF_CONFIG

In TensorFlow ist die **TF_CONFIG**-Umgebungsvariable für das Training auf mehreren Computern erforderlich. Für TensorFlow-Aufträge konfiguriert und bestimmt Azure ML die TF_CONFIG-Variable individuell für jeden Worker, bevor das Trainingsskript ausgeführt wird.

Sie können aus Ihrem Trainingsskript auf TF_CONFIG zugreifen, falls dies erforderlich ist: `os.environ['TF_CONFIG']`.

Beispiel für das Festlegen von TF_CONFIG auf einem Chief-Workerknoten:
```json
TF_CONFIG='{
    "cluster": {
        "worker": ["host0:2222", "host1:2222"]
    },
    "task": {"type": "worker", "index": 0},
    "environment": "cloud"
}'
```

### <a name="tensorflow-example"></a>TensorFlow-Beispiel

- [azureml-examples: Verteiltes TensorFlow-Training mit MultiWorkerMirroredStrategy](https://github.com/Azure/azureml-examples/tree/main/python-sdk/workflows/train/tensorflow/mnist-distributed)

## <a name="accelerating-gpu-training-with-infiniband"></a><a name="infiniband"></a> Beschleunigen des GPU-Trainings mit InfiniBand

Bestimmte Azure-VM-Serien, insbesondere die NC-, ND- und H-Serie, verfügen jetzt über VMs mit RDMA-Unterstützung mit SR-IOV- und Infiniband-Unterstützung. Diese VMs kommunizieren über das InfiniBand-Netzwerk mit geringer Latenz und hoher Bandbreite, was deutlich leistungsfähiger ist als ethernetbasierte Konnektivität. SR-IOV für InfiniBand ermöglicht für jede MPI-Bibliothek nahezu Bare-Metal-Leistung (MPI wird von vielen Frameworks und Tools einschließlich NCCL-Software von NVIDIA für verteiltes Training verwendet). Diese SKUs sollen die Anforderungen rechenintensiver, GPU-gestützter Machine Learning-Workloads erfüllen. Weitere Informationen finden Sie unter [Beschleunigen des verteilten Trainings in Azure Machine Learning mit SR-IOV](https://techcommunity.microsoft.com/t5/azure-ai/accelerating-distributed-training-in-azure-machine-learning/ba-p/1059050).

Wenn Sie einen `AmlCompute`-Cluster mit einer dieser Größen mit RDMA- und InfiniBand-Unterstützung erstellen, z. B. `Standard_ND40rs_v2`, wird das Betriebssystemimage mit dem Mellanox OFED-Treiber bereitgestellt, der erforderlich ist, um InfiniBand vorinstalliert und vorkonfiguriert zu aktivieren.

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen von Machine Learning-Modellen für Azure](how-to-deploy-and-where.md)
* [Bereitstellen und Bewerten eines Machine Learning-Modells mit einem verwalteten Onlineendpunkt (Vorschau)](how-to-deploy-managed-online-endpoints.md)
* [Verteiltes Trainieren von Deep Learning-Modellen in Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
