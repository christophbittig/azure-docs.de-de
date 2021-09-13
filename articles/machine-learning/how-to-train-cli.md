---
title: Trainieren von Modellen (Erstellen von Aufträgen) mit der CLI (v2)
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie mithilfe der Azure CLI-Erweiterung für Machine Learning Modelle trainieren (Aufträge erstellen).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: lostmygithubaccount
ms.author: copeters
ms.date: 06/18/2021
ms.reviewer: laobri
ms.custom: devx-track-azurecli, devplatv2
ms.openlocfilehash: 9f3a91f9abc472f285139bfac04af7dff5c63e9f
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444556"
---
# <a name="train-models-create-jobs-with-the-cli-v2"></a>Trainieren von Modellen (Erstellen von Aufträgen) mit der CLI (v2)

Die Azure Machine Learning CLI (v2) ist eine Azure CLI-Erweiterung, mit der Sie das Trainieren von Modellen beschleunigen und gleichzeitig Azure Compute-Ressourcen hoch- und aufskalieren können – mit nachverfolgtem und überwachbarem Modelllebenszyklus.

Das Trainieren eines Machine Learning-Modells ist üblicherweise ein iterativer Prozess. Dank moderner Tools ist es einfacher denn je, größere Modelle schneller und mit mehr Daten zu trainieren. Prozesse, die in der Vergangenheit mühsam manuell durchgeführt werden mussten, sind inzwischen meist automatisiert. Beispiele wären etwa die Hyperparameteroptimierung oder die Wahl des Algorithmus. Mit der Azure Machine Learning-CLI können Sie Ihre Aufträge (und Modelle) in einem [Arbeitsbereich](concept-workspace.md) mit Hyperparameteroptimierungen (Sweeps) nachverfolgen, Azure Compute-Hochleistungsressourcen hochskalieren und mithilfe von verteiltem Training eine horizontale Skalierung durchführen.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Voraussetzungen

- Für die Verwendung der CLI benötigen Sie ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://azure.microsoft.com/free/) noch heute aus.
- [Installieren und Einrichten der Azure CLI-Erweiterung für Machine Learning](how-to-configure-cli.md)

> [!TIP]
> Verwenden Sie für eine umfassende Entwicklungsumgebung Visual Studio Code und die [Azure Machine Learning-Erweiterung](how-to-setup-vs-code.md), um [Azure Machine Learning-Ressourcen zu verwalten](how-to-manage-resources-vscode.md) und [Machine Learning-Modelle zu trainieren](tutorial-train-deploy-image-classification-model-vscode.md).

### <a name="clone-examples-repository"></a>Repository für Klonbeispiele

Klonen Sie zum Ausführen der Trainingsbeispiele zunächst das Beispielerepository, und wechseln Sie in das `cli`-Verzeichnis:

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="git_clone":::

Beachten Sie, dass `--depth 1` nur den letzten Commit in das Repository klont, wodurch die Zeit zum Abschließen des Vorgangs reduziert wird.

### <a name="create-compute"></a>Erstellen von Computeressourcen

Sie können einen Azure Machine Learning-Computecluster über die Befehlszeile erstellen. Mit den folgenden Befehlen werden beispielsweise ein Cluster namens `cpu-cluster` und ein Cluster namens `gpu-cluster` erstellt.

:::code language="azurecli" source="~/azureml-examples-main/cli/create-compute.sh" id="create_computes":::

Beachten Sie, dass Ihnen zu diesem Zeitpunkt keine Computegebühren berechnet werden, da `cpu-cluster` und `gpu-cluster` erst über Knoten verfügen, wenn ein Auftrag übermittelt wird. Erfahren Sie mehr darüber, wie Sie die [Kosten für AmlCompute verwalten und optimieren](how-to-manage-optimize-cost.md#use-azure-machine-learning-compute-cluster-amlcompute) können.

In den folgenden Beispielaufträgen in diesem Artikel wird entweder `cpu-cluster` oder `gpu-cluster` verwendet. Passen Sie diese nach Bedarf den Namen Ihrer Cluster an.

Verwenden Sie `az ml compute create -h`, um weitere Informationen zu Computeerstellungsoptionen zu erhalten.

[!INCLUDE [arc-enabled-kubernetes](../../includes/machine-learning-create-arc-enabled-training-computer-target.md)]

## <a name="introducing-jobs"></a>Einführung in Aufträge

Für die Azure Machine Learning CLI (v2) werden Aufträge im YAML-Format erstellt. Durch einen Auftrag wird Folgendes aggregiert:

- Auszuführendes Programm
- Art der Ausführung
- Ausführungsort

Der Auftrag „hello world“ umfasst alle drei Informationen:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/misc/hello-world.yml":::

Dies können Sie ausführen:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world":::

Dies ist jedoch lediglich ein Beispielauftrag, durch den nur eine Zeile in der Protokolldatei ausgegeben wird. Neben den vom System generierten Protokollen werden üblicherweise zusätzliche Artefakte wie Modellbinärdateien und zugehörige Metadaten generiert.

Folgende Artefakte werden von Azure Machine Learning automatisch erfasst:

- Die Verzeichnisse `./outputs` und `./logs` erhalten von Azure Machine Learning eine Sonderbehandlung. Dateien, die im Rahmen Ihres Auftrags in diese Verzeichnisse geschrieben werden, werden in den Ausführungsverlauf des Auftrags hochgeladen, sodass Sie nach Abschluss des Auftrags weiterhin auf sie zugreifen können. Der Ordner `./outputs` wird am Ende des Auftrags hochgeladen. Die in `./logs` geschriebenen Dateien werden dagegen in Echtzeit hochgeladen. Verwenden Sie die zweite Option, wenn Sie Protokolle während des Auftrags streamen möchten (beispielsweise TensorBoard-Protokolle).
- Azure Machine Learning arbeitet mit der Nachverfolgungsfunktion von MLflow zusammen. Sie können `mlflow.autolog()` für mehrere gängige ML-Frameworks verwenden, um Modellparameter, Leistungsmetriken, Modellartefakte und sogar Featurerelevanzgraphen zu protokollieren. Die Methoden vom Typ `mlflow.log_*()` können auch zur expliziten Protokollierung von Parametern, Metriken und Artefakten verwendet werden. Alle von MLflow protokollierten Metriken und Artefakte werden im Ausführungsverlauf des Auftrags gespeichert.

Im Rahmen eines Auftrags wird häufig Quellcode ausgeführt, der lokal bearbeitet und gesteuert wird. Sie können ein Quellcodeverzeichnis angeben, das in den Auftrag eingeschlossen werden soll und aus dem der Befehl ausgeführt wird.

Sehen Sie sich beispielsweise das Projektverzeichnis `jobs/train/lightgbm/iris` im Beispielrepository an:

```tree
.
├── job-sweep.yml
├── job.yml
└── src
    └── main.py
```

Dieses Verzeichnis enthält zwei Auftragsdateien und ein Quellcodeunterverzeichnis namens `src`. Dieses Beispiel enthält unter `src` zwar nur eine einzelne Datei, das gesamte Unterverzeichnis wird jedoch rekursiv hochgeladen und steht zur Verwendung im Auftrag zur Verfügung.

Der Befehlsauftrag wird über `job.yml` konfiguriert:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/train/lightgbm/iris/job.yml":::

Dies können Sie ausführen:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="lightgbm_iris":::

## <a name="basic-python-training-job"></a>Grundlegender Python-Trainingsauftrag

Im Anschluss finden Sie ausführliche Informationen zur YAML-Auftragsdatei:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/train/lightgbm/iris/job.yml":::

| Schlüssel | BESCHREIBUNG |
| --- | ----------- |
| `$schema` | (Optional) Das YAML-Schema. Sie können das [Schema](https://azuremlschemas.azureedge.net/latest/commandJob.schema.json) aus dem obigen Beispiel in einem Browser betrachten, um alle verfügbaren Optionen für eine YAML-Befehlsauftragsdatei anzuzeigen. Wenn Sie die YAML-Datei mithilfe der VS Code-Erweiterung für Azure Machine Learning erstellen, können Sie durch Einfügen der Eigenschaft `$schema` am Anfang der Datei Schema- und Ressourcenvervollständigungen aufrufen. |
| `code.local_path` | (Optional) Der lokale Pfad zum Quellverzeichnis relativ zur YAML-Datei, die hochgeladen und mit dem Auftrag verwendet werden soll. Aus Konsistenzgründen empfiehlt es sich, `src` im gleichen Verzeichnis zu verwenden, in dem sich auch die Auftragsdateien befinden. |
| `command` | Der auszuführende Befehl. Die Konvention `>` ermöglicht die Erstellung lesbarer mehrzeiliger Befehle, indem Neue-Zeile-Zeichen zu Leerzeichen gefaltet werden. Befehlszeilenargumente können explizit in den Befehl geschrieben oder mithilfe von geschweiften Klammern aus anderen Abschnitten (genauer gesagt `inputs` oder `search_space`) abgeleitet werden. |
| `inputs` | (Optional) Ein Wörterbuch der Eingabedatenbindungen. Der Schlüssel ist hierbei ein für die Eingabebindung angegebener Name. Der Wert für das jeweilige Element ist die Eingabebindung, die sich aus Feldern vom Typ `data` und `mode` zusammensetzt. `data` kann entweder 1) ein Verweis auf eine vorhandene Azure Machine Learning-Datenressource mit Versionsangabe unter Verwendung des Präfix `azureml:` sein (beispielsweise `azureml:iris-url:1`, um auf die Version 1 einer Datenressource mit dem Namen „iris-url“ zu verweisen) oder 2) eine Inlinedefinition der Daten. Verwenden Sie `data.path`, um einen Cloudstandort anzugeben. Verwenden Sie `data.local_path`, um Daten aus dem lokalen Dateisystem anzugeben, die in den Standarddatenspeicher hochgeladen werden. `mode` gibt an, wie die Daten in der Computeressource für den Auftrag verfügbar gemacht werden. Hierfür werden die beiden Optionen „mount“ (Einbinden) und „download“ (Herunterladen) unterstützt. <br><br> Auf eine Eingabe kann im Befehl anhand ihres Namens verwiesen werden. Beispiel: `{inputs.my_input_name}`. Die parametrisierte Notation im Befehl wird dann zur Laufzeit von Azure Machine Learning in den Speicherort der Daten am Computeziel aufgelöst. Wenn die Daten also beispielsweise für die Einbindung konfiguriert sind, wird `{inputs.my_input_name}` in den Bereitstellungspunkt aufgelöst. |
| `environment` | Die Umgebung, mit der der Befehl am Computeziel ausgeführt werden soll. Sie können die Umgebung inline definieren, indem Sie das zu verwendende Docker-Image oder Dockerfile für die Imageerstellung angeben. Mit dem Präfix `azureml:` können Sie auch auf eine vorhandene Umgebungsversion im Arbeitsbereich oder auf eine der kuratierten Umgebungen von Azure ML verweisen. `azureml:AzureML-TensorFlow2.4-Cuda11-OpenMpi4.1.0-py36:1` verweist beispielsweise auf Version 1 einer kuratierten Umgebung für TensorFlow mit GPU-Unterstützung. <br><br> In der für das Training verwendeten Umgebung muss Python installiert sein. Führen Sie bei Bedarf `apt-get update -y && apt-get install python3 -y` in Ihrem Dockerfile aus, um die Installation durchzuführen. |
| `compute.target` | Das Computeziel. Geben Sie für die lokale Ausführung `local` an, oder verwenden Sie das Präfix `azureml:`, um auf eine vorhandene Computeressource in Ihrem Arbeitsbereich zu verweisen. `azureml:cpu-cluster` verweist beispielsweise auf ein Computeziel namens „cpu-cluster“. |
| `experiment_name` | (Optional) Versieht den Auftrag mit einem Tag, um eine bessere Organisation in Azure Machine Learning Studio zu ermöglichen. Die Ausführungsaufzeichnung jedes Auftrags wird in Studio unter dem entsprechenden Experiment auf der Registerkarte „Experiment“ organisiert. Ohne Angabe wird standardmäßig der Name des Arbeitsverzeichnisses verwendet, in dem der Auftrag erstellt wurde. |
| `name` | (Optional) Der Name des Auftrags. Muss für alle Aufträge in einem Arbeitsbereich eindeutig sein. Wird kein Name angegeben (entweder in der YAML-Datei über das Feld `name` oder mithilfe der Befehlszeile über `--name/-n`), wird automatisch eine GUID/UUID generiert und für den Namen verwendet. Der Auftragsname entspricht der Ausführungs-ID auf der Studio-Benutzeroberfläche der Ausführungsaufzeichnung für den Auftrag. |

Beim Erstellen dieses Auftrags werden alle angegebenen lokalen Ressourcen (beispielsweise das Quellcodeverzeichnis) hochgeladen. Außerdem wird die YAML-Datei überprüft und die Ausführung übermittelt. Bei Bedarf wird die Umgebung erstellt, und die Computeressource wird hochskaliert und für die Ausführung des Auftrags konfiguriert.

So führen Sie den Trainingsauftrag „lightgbm/iris“ aus:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="lightgbm_iris":::

Nach Abschluss des Auftrags können Sie die Ausgaben herunterladen:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="download_outputs":::

> [!IMPORTANT]
> Ersetzen Sie `$run_id` durch Ihre Ausführungs-ID. Diese finden Sie in der Konsolenausgabe oder auf der Studio-Seite mit den Ausführungsdetails.

Dadurch werden die Protokolle und alle erfassten Artefakte lokal in ein Verzeichnis namens `$run_id` heruntergeladen. In diesem Beispiel wird das von MLflow protokollierte Modellunterverzeichnis heruntergeladen.

## <a name="sweep-hyperparameters"></a>Optimieren von Hyperparametern (Sweeping)

Azure Machine Learning ermöglicht auch eine effizientere Optimierung der Hyperparameter für Ihre Machine Learning-Modelle. Sie können einen Optimierungsauftrag für Hyperparameter (Sweep-Auftrag) konfigurieren und ihn über die CLI übermitteln.

Sie können `job.yml` in `job-sweep.yml` ändern, um Hyperparameter zu optimieren:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/train/lightgbm/iris/job-sweep.yml":::

| Schlüssel | BESCHREIBUNG |
| --- | ----------- |
| `$schema` | (Optional) Das YAML-Schema, das geändert wurde und jetzt auf das [Schema](https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json) des Sweep-Auftrags verweist. |
| `type` | Der Auftragstyp. |
| `algorithm` | Der Samplingalgorithmus. „random“ (zufällig) ist meist eine gute Wahl. Eine Aufzählung der Optionen finden Sie im Schema. |
| `trial` | Die Befehlsauftragskonfiguration für jeden auszuführenden Test. Der Befehl `trial.command` aus dem vorherigen Beispiel wurde geändert, um die Notation `{search_space.<hyperparameter_name>}` zu verwenden und so auf die in `search_space` definierten Hyperparameter zu verweisen. Von Azure Machine Learning wird daraufhin jede parametrisierte Notation in den Wert für den entsprechenden Hyperparameter aufgelöst, der für den jeweiligen Test generiert wird. |
| `search_space` | Ein Wörterbuch der Hyperparameter für den Sweep-Vorgang. Der Schlüssel ist ein Name für den Hyperparameter (beispielsweise `search_space.learning_rate`). Beachten Sie, dass der Name nicht dem Argument des Trainingsskripts entsprechen muss, sondern nur dem Suchbereichsverweis in der Notation mit geschweiften Klammern im Befehl (beispielsweise `{search_space.learning_rate}`). Der Wert ist die Hyperparameterverteilung. Eine Aufzählung der Optionen finden Sie im Schema. |
| `objective.primary_metric` | Die Optimierungsmetrik. Muss dem Namen einer über den Trainingscode protokollierten Metrik entsprechen. `objective.goal` gibt die Richtung an. Mögliche Optionen sind „minimize“ (minimieren) und „maximize“ (maximieren). Eine vollständige Aufzählung der Optionen finden Sie im Schema. |
| `max_total_trials` | Die maximale Anzahl einzelner ausführbarer Tests. |
| `max_concurrent_trials` | (Optional) Die maximale Anzahl von Tests, die gleichzeitig in Ihrem Computecluster ausgeführt werden können. |
| `timeout_minutes` | (Optional) Die maximale Ausführungsdauer des Sweep-Auftrags (in Minuten). |
| `experiment_name` | (Optional) Das Experiment, unter dem der Sweep-Auftrag nachverfolgt werden soll. Ohne Angabe wird bei der Auftragserstellung standardmäßig der Name des Arbeitsverzeichnisses verwendet. |

Erstellen Sie einen Auftrag, und öffnen Sie ihn in Studio:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="lightgbm_iris_sweep":::

> [!TIP]
> Hyperparameteroptimierungen können mit verteilten Befehlsaufträgen verwendet werden.

## <a name="distributed-training"></a>Verteiltes Training

Sie können in einem Befehlsauftrag den Abschnitt `distribution` angeben. Azure ML unterstützt verteiltes Training für PyTorch, TensorFlow und MPI-kompatible Frameworks. PyTorch und TensorFlow ermöglichen natives verteiltes Training für die jeweiligen Frameworks (beispielsweise APIs vom Typ `tf.distributed.Strategy` für TensorFlow).

Wichtig: Legen Sie `compute.instance_count` auf die gewünschte Anzahl von Knoten für den Auftrag fest (Standardwert: 1).

### <a name="pytorch"></a>PyTorch

Eine YAML-Beispieldatei für verteiltes PyTorch-Training für das CIFAR-10-Dataset:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/train/pytorch/cifar-distributed/job.yml":::

Wie Sie sehen, wird hier auf lokale Daten verwiesen, die im geklonten Beispielrepository nicht vorhanden sind. Daher müssen Sie zunächst das CIFAR-10-Dataset lokal herunterladen, extrahieren und am richtigen Ort im Projektverzeichnis platzieren:

:::code language="bash" source="~/azureml-examples-main/cli/train.sh" id="download_cifar":::

Erstellen Sie den Auftrag, und öffnen Sie ihn in Studio:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="pytorch_cifar":::

### <a name="tensorflow"></a>TensorFlow

Eine YAML-Beispieldatei für verteiltes TensorFlow-Training für die MNIST-Datenbank:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/train/tensorflow/mnist-distributed/job.yml":::

Erstellen Sie den Auftrag, und öffnen Sie ihn in Studio:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="tensorflow_mnist":::

### <a name="mpi"></a>MPI

Azure ML unterstützt das Starten eines MPI-Auftrags auf mehreren Knoten sowie mehrere Prozesse pro Knoten. Der Auftrag wird über `mpirun` gestartet. Falls von Ihrem Trainingscode beispielsweise das Horovod-Framework für verteiltes Training verwendet wird, können Sie diesen Auftragstyp für das Training in Azure ML nutzen.

Geben Sie zum Starten eines MPI-Auftrags `mpi` als Typ sowie die Anzahl zu startender Prozesse pro Knoten (`process_count_per_instance`) im Abschnitt `distribution` an. Ohne diese Angabe wird von Azure ML standardmäßig ein Prozess pro Knoten gestartet.

Eine YAML-Beispielspezifikation zum Ausführen eines TensorFlow-Auftrags für MNIST mit Horovod:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/train/tensorflow/mnist-distributed-horovod/job.yml":::

Erstellen Sie den Auftrag, und öffnen Sie ihn in Studio:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="tensorflow_mnist_horovod":::

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen und Bewerten eines Machine Learning-Modells mit einem verwalteten Onlineendpunkt (Vorschau)](how-to-deploy-managed-online-endpoints.md)
- [Trainieren von Modellen per REST (Vorschau)](how-to-train-with-rest.md)
