---
title: Trainieren von Modellen mit der CLI (v2)
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie mithilfe der Azure CLI-Erweiterung für Machine Learning Modelle trainieren (Aufträge erstellen).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: amibp
ms.author: amipatel
ms.date: 10/21/2021
ms.reviewer: laobri
ms.custom: devx-track-azurecli, devplatv2
ms.openlocfilehash: b849293d94b435fa74378d80d8989e6075f78d86
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493664"
---
# <a name="train-models-with-the-cli-v2-preview"></a>Trainieren von Modellen mit der CLI (v2) (Vorschau)

Die Azure Machine Learning CLI (v2) ist eine Azure CLI-Erweiterung, mit der Sie das Trainieren von Modellen beschleunigen und gleichzeitig Azure Compute-Ressourcen hoch- und aufskalieren können – mit nachverfolgtem und überwachbarem Modelllebenszyklus.

Das Trainieren eines Machine Learning-Modells ist üblicherweise ein iterativer Prozess. Dank moderner Tools ist es einfacher denn je, größere Modelle schneller und mit mehr Daten zu trainieren. Prozesse, die in der Vergangenheit mühsam manuell durchgeführt werden mussten, sind inzwischen meist automatisiert. Beispiele wären etwa die Hyperparameteroptimierung oder die Wahl des Algorithmus. Mit der Azure Machine Learning-CLI (v2) können Sie Ihre Aufträge (und Modelle) in einem [Arbeitsbereich](concept-workspace.md) mit Hyperparameteroptimierungen (Sweeps) nachverfolgen, Azure Compute-Hochleistungsressourcen hochskalieren und mithilfe von verteiltem Training eine horizontale Skalierung durchführen.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Voraussetzungen

- Für die Verwendung der CLI (v2) benötigen Sie ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://azure.microsoft.com/free/) noch heute aus.
- [Installieren und Einrichten der CLI (v2)](how-to-configure-cli.md)

> [!TIP]
> Verwenden Sie für eine Entwicklungsumgebung mit vollem Funktionsumfang mit Schemavalidierung und automatischer Vervollständigung für Auftrags-YAMLs Visual Studio Code und die [Erweiterung Azure Machine Learning](how-to-setup-vs-code.md).

### <a name="clone-examples-repository"></a>Repository für Klonbeispiele

Klonen Sie zum Ausführen der Trainingsbeispiele zunächst das Beispielerepository, und wechseln Sie in das `cli`-Verzeichnis:

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="git_clone":::

Wenn Sie `--depth 1` verwenden, wird nur der letzte Commit in das Repository geklont, wodurch die Zeit zum Abschließen des Vorgangs reduziert wird.

### <a name="create-compute"></a>Erstellen von Computeressourcen

Sie können einen Azure Machine Learning-Computecluster über die Befehlszeile erstellen. Mit den folgenden Befehlen werden beispielsweise ein Cluster namens `cpu-cluster` und ein Cluster namens `gpu-cluster` erstellt.

:::code language="azurecli" source="~/azureml-examples-main/setup-repo/create-compute.sh" id="create_computes":::

Ihnen werden zu diesem Zeitpunkt keine Computegebühren berechnet, da `cpu-cluster` und `gpu-cluster` erst über Knoten verfügen, wenn ein Auftrag übermittelt wird. Erfahren Sie mehr darüber, wie Sie die [Kosten für AmlCompute verwalten und optimieren](how-to-manage-optimize-cost.md#use-azure-machine-learning-compute-cluster-amlcompute) können.

In den folgenden Beispielaufträgen in diesem Artikel wird entweder `cpu-cluster` oder `gpu-cluster` verwendet. Passen Sie diese Namen in den Beispielaufträgen in diesem Artikel nach Bedarf an den Namen Ihrer Cluster an. Verwenden Sie `az ml compute create -h`, um weitere Informationen zu Computeerstellungsoptionen zu erhalten.

## <a name="hello-world"></a>Hello World

Für die Azure Machine Learning CLI (v2) werden Aufträge im YAML-Format erstellt. Durch einen Auftrag wird Folgendes aggregiert:

- Auszuführendes Programm
- Art der Ausführung
- Ausführungsort

Der Auftrag „hello world“ umfasst alle drei Informationen:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world.yml":::

> [!WARNING]
> In der für die Aufträge verwendeten Umgebung muss Python installiert sein. Führen Sie `apt-get update -y && apt-get install python3 -y` in Ihrer Dockerfile-Datei aus, um bei Bedarf zu installieren, oder leiten Sie es von einem Basisimage ab, auf dem Python bereits installiert ist.

> [!TIP]
> Die `$schema:` gesamten Beispiele ermöglichen die Schemavalidierung und automatische Vervollständigung, wenn YAML-Dateien in [VSCode mit der Azure Machine Learning Erweiterung](how-to-setup-vs-code.md) erstellt werden.

Dies können Sie ausführen:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world":::

> [!TIP]
> Der `--web`-Parameter versucht, Ihren Auftrag im Azure Machine Learning Studio mit ihrem Standardwebbrowser zu öffnen. Der `--stream`-Parameter kann verwendet werden, um Protokolle an die Konsole zu streamen und weitere Befehle zu blockieren.

## <a name="overriding-values-on-create-or-update"></a>Überschreiben von Werten beim Erstellen oder Aktualisieren

YAML-Auftragsspezifikationswerte können beim Erstellen oder Aktualisieren eines Auftrags mit `--set` überschrieben werden. Beispiel:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_set":::

## <a name="job-names"></a>Auftragsnamen

Die meisten `az ml job` Befehle außer `create` und `list` erfordern `--name/-n`, bei denen es sich um den Namen eines Auftrags oder die „Ausführungs-ID“ im Studio handelt. Sie sollten die`name`-Eigenschaft eines Auftrags während der Erstellung nicht direkt festlegen, da sie pro Arbeitsbereich eindeutig sein muss. Azure Machine Learning generiert eine zufällige GUID für den Auftragsnamen, wenn er nicht festgelegt ist, die aus der Ausgabe der Auftragserstellung in der CLI oder durch Kopieren der Eigenschaft „Ausführungs-ID“ in den Studio- und MLflow-APIs abgerufen werden kann.

Sie können den Namen eines Auftrags erfassen, wenn er erstellt wird, um Aufträge in Skripts und CI/CD-Flows zu automatisieren. Fügen Sie dazu `--query name -o tsv` hinzu, indem Sie die Ausgabe abfragen und strippen. Die Besonderheiten variieren je nach Shell, aber für Bash:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_name":::

In nachfolgenden Befehlen (z. B. `update`, `show` oder `stream`) müssen Sie `$run_id` angeben.

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_show":::

## <a name="organize-jobs"></a>Organisieren von Aufträgen

Zum Organisieren von Aufträgen können Sie einen Anzeigenamen, einen Experimentnamen, eine Beschreibung und Tags festlegen. Beschreibungen unterstützen die Markdownsyntax in Studio. Diese Eigenschaften sind veränderlich, nachdem ein Auftrag erstellt wurde. Vollständiges Beispiel:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-org.yml":::

Sie können diesen Auftrag ausführen, bei dem diese Eigenschaften sofort in Studio sichtbar sind:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_org":::

Mit `--set` können Sie die veränderlichen Werte aktualisieren, nachdem der Auftrag erstellt wurde:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_org_set":::

## <a name="environment-variables"></a>Umgebungsvariablen

Sie können Umgebungsvariablen für die Verwendung in Ihrem Auftrag festlegen:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-env-var.yml":::

Sie können diesen Auftrag ausführen:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_env_var":::

> [!WARNING]
> Sie sollten `inputs` zum Parametrisieren von Argumenten in `command` verwenden. Siehe [Eingaben und Ausgaben](#inputs-and-outputs).

## <a name="track-models-and-source-code"></a>Nachverfolgen von Modellen und Quellcode

Machine Learning-Produktionsmodelle müssen überprüfbar sein (wenn sie nicht reproduzierbar sind). Es ist wichtig, den Quellcode für ein bestimmtes Modell nachzuverfolgen. Azure Machine Learning erstellt eine Momentaufnahme Ihres Quellcodes und speichert sie mit dem Auftrag. Darüber hinaus werden das Quellrepository und der Commit beibehalten, wenn Sie Aufträge aus einem Git-Repository ausführen.

> [!TIP]
> Wenn Sie das Beispielrepository nutzen und ausführen, können Sie das Quellrepository und den Commit in Studio für alle bisher ausgeführten Aufträge anzeigen.

Sie können den `code.local_path`-Schlüssel in einem Auftrag mit dem Wert als Pfad zu einem Quellcodeverzeichnis angeben. Eine Momentaufnahme des Verzeichnisses wird erstellt und mit dem Auftrag hochgeladen. Der Inhalt des Verzeichnisses ist direkt über das Arbeitsverzeichnis des Auftrags verfügbar.

> [!WARNING]
> Der Quellcode sollte keine großen Dateneingaben für das Modelltraining enthalten. Verwenden Sie stattdessen [Dateneingaben](#data-inputs). Sie können eine `.gitignore`-Datei im Quellcodeverzeichnis verwenden, um Dateien aus der Momentaufnahme auszuschließen. Die Grenzwerte für die Momentaufnahmegröße sind 300 MB oder 2.000 Dateien.

Sehen wir uns einen Auftrag an, der Code angibt:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-mlflow.yml":::

Das Python-Skript befindet sich im lokalen Quellcodeverzeichnis. Der Befehl ruft dann `python` auf, um das Skript auszuführen. Das gleiche Muster kann auch auf andere Programmiersprachen angewendet werden.

> [!WARNING]
> Die in diesem Artikel gezeigte „hello“-Familie von Aufträgen dient zu Demonstrationszwecken und folgt nicht unbedingt den empfohlenen bewährten Methoden. Die Verwendung von `&&` oder ähnlichen zur Ausführung vieler Befehle in einer Sequenz wird nicht empfohlen. Stattdessen sollten Sie die Befehle in eine Skriptdatei im Quellcodeverzeichnis schreiben und das Skript in Ihrer `command` aufrufen. Die Installation von Abhängigkeiten in `command`, wie oben gezeigt über `pip install`, wird nicht empfohlen. Stattdessen sollten alle Auftragsabhängigkeiten als Teil Ihrer Umgebung angegeben werden. Weitere Einzelheiten finden Sie unter [Verwalten von Umgebungen mit der Befehlszeilenschnittstelle (v2)](how-to-manage-environments-v2.md).

### <a name="model-tracking-with-mlflow"></a>Modellnachverfolgung mit MLflow

Beim Iterieren von Modellen müssen wissenschaftliche Fachkräfte für Daten in der Lage sein, Modellparameter und Trainingsmetriken nachzuverfolgen. Azure Machine Learning lässt sich in die MLflow-Nachverfolgung integrieren, um die Protokollierung von Modellen, Artefakten, Metriken und Parametern für einen Auftrag zu ermöglichen. Fügen Sie Ihrem Trainingscode `import mlflow` hinzu oder rufen Sie `mlflow.log_*`- oder `mlflow.autolog()`-APIs auf, um MLflow in Ihren Python-Skripts zu verwenden.

> [!WARNING]
> Die Pakete `mlflow` und `azureml-mlflow` müssen in Ihrer Python-Umgebung für MLflow-Nachverfolgungsfunktionen installiert werden.

> [!TIP]
> Der `mlflow.autolog()`-Aufruf wird für viele beliebte Frameworks unterstützt und übernimmt den Großteil der Protokollierung für Sie.

Sehen wir uns das Python-Skript an, das im obigen Auftrag aufgerufen wird und `mlflow` zum Protokollieren eines Parameters, einer Metrik und eines Artefakts verwendet:

:::code language="python" source="~/azureml-examples-main/cli/jobs/basics/src/hello-mlflow.py":::

Sie können diesen Auftrag in der Cloud über Azure Machine Learning ausführen, wo er nachverfolgt und überwacht werden kann:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_mlflow":::

### <a name="query-metrics-with-mlflow"></a>Abfragemetriken mit MLflow

Nach dem Ausführen von Aufträgen können Sie die Ausführungsergebnisse der Aufträge und deren protokollierte Metriken abfragen. Python eignet sich besser für diese Aufgabe als eine CLI. Sie können Ausführungen und deren Metriken über `mlflow` abfragen und zur Analyse in vertraute Objekte wie Pandas-Dataframes laden.

Rufen Sie zunächst den MLflow-Nachverfolgungs-URI für Ihren Azure Machine Learning Arbeitsbereich ab:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="mlflow_uri":::

Verwenden Sie die Ausgabe dieses Befehls in `mlflow.set_tracking_uri(<YOUR_TRACKING_URI>)` aus einer Python-Umgebung, in die MLflow importiert wurde. MLflow-Aufrufe entsprechen jetzt Aufträgen in Ihrem Azure Machine Learning Arbeitsbereich.

## <a name="inputs-and-outputs"></a>Eingaben und Ausgaben

Aufträge verfügen in der Regel über Eingaben und Ausgaben. Eingaben können Modellparameter sein, die für die Hyperparameteroptimierung bereinigt werden können, oder Clouddateneingaben, die in das Computeziel eingebunden oder heruntergeladen werden. Ausgaben (ohne Metriken) sind Artefakte, die in die Standardausgaben oder eine benannte Datenausgabe geschrieben oder kopiert werden können.

### <a name="literal-inputs"></a>Literaleingaben

Literaleingaben werden direkt im Befehl aufgelöst. Sie können unseren Auftrag „hello world“ ändern, um Literaleingaben zu verwenden:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-input.yml":::

Sie können diesen Auftrag ausführen:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_input":::

Sie können `--set` verwenden, um Eingaben zu überschreiben:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_input_set":::

Literale Eingaben für Aufträge können in [Suchbereichseingaben](#search-space-inputs) für Hyperparameter-Sweeps beim Modelltraining konvertiert werden.

### <a name="search-space-inputs"></a>Suchbereichseingaben

Für einen Sweepauftrag können Sie einen Suchbereich für Literaleingaben angeben, aus dem ausgewählt werden soll. Den vollständigen Bereich der Optionen für Suchraumeingaben finden Sie in der [YAML-Syntaxreferenz zum Sweepauftrag](reference-yaml-job-sweep.md).

> [!WARNING]
> Sweepaufträge werden derzeit in Pipelineaufträgen nicht unterstützt.

Wir veranschaulichen das Konzept mit einem einfachen Python-Skript, das Argumente aufnimmt und eine zufällige Metrik protokolliert:

:::code language="python" source="~/azureml-examples-main/cli/jobs/basics/src/hello-sweep.py":::

Erstellen Sie einen entsprechenden Sweepauftrag:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-sweep.yml":::

Führen Sie ihn aus:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_sweep":::

### <a name="data-inputs"></a>Dateneingaben

Dateneingaben werden in einen Pfad im lokalen Dateisystem des Auftragscomputers aufgelöst. Lassen Sie uns dies mit dem klassischen Iris-Dataset veranschaulichen, das öffentlich in einem Blobcontainer unter `https://azuremlexamples.blob.core.windows.net/datasets/iris.csv` gehostet wird.

Sie können ein Python-Skript erstellen, das den Pfad zur IRIS-CSV-Datei als Argument verwendet, in einen Datenrahmen einliest, die ersten fünf Zeilen druckt und im `outputs`-Verzeichnis speichert.

:::code language="python" source="~/azureml-examples-main/cli/jobs/basics/src/hello-iris.py":::

Azure Storage-URI-Eingaben können angegeben werden, wodurch Daten in das lokale Dateisystem bereitgestellt oder heruntergeladen werden. Sie können eine einzelne Datei angeben:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-iris-file.yml":::

Und führen Sie folgendes aus:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="iris_file":::

Oder geben Sie einen gesamten Ordner an:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-iris-folder.yml":::

Und führen Sie folgendes aus:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="iris_folder":::

#### <a name="private-data"></a>Private Daten

Für private Daten in Azure Blob Storage oder Azure Data Lake Storage, die über einen Datenspeicher mit Azure Machine Learning verbunden sind, können Sie Azure Machine Learning URIs im Format `azureml://datastores/<DATASTORE_NAME>/paths/<PATH_TO_DATA>` für Eingabedaten verwenden. Wenn Sie z. B. die Iris-CSV-Datei in ein Verzeichnis mit dem Namen `/example-data/` im Blobcontainer hochladen, der dem Datenspeicher mit dem Namen `workspaceblobstore` entspricht, können Sie einen vorherigen Auftrag ändern, um die Datei im Datenspeicher zu verwenden:

> [!WARNING]
> Die Ausführung dieser Aufträge schlägt für Sie fehl, wenn Sie die Iris-CSV-Datei nicht an den gleichen Speicherort in `workspaceblobstore` kopiert haben.

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-iris-datastore-file.yml":::

Oder das gesamte Verzeichnis:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-iris-datastore-folder.yml":::

### <a name="default-outputs"></a>Standardausgaben

Die Verzeichnisse `./outputs` und `./logs` erhalten von Azure Machine Learning eine Sonderbehandlung. Dateien, die im Rahmen Ihres Auftrags in diese Verzeichnisse geschrieben werden, werden in den Auftrag hochgeladen, sodass Sie nach dessen Abschluss weiterhin auf sie zugreifen können. Der Ordner `./outputs` wird am Ende des Auftrags hochgeladen. Die in `./logs` geschriebenen Dateien werden dagegen in Echtzeit hochgeladen. Verwenden Sie die zweite Option, wenn Sie Protokolle während des Auftrags streamen möchten (beispielsweise TensorBoard-Protokolle).

Sie können den Auftrag „hello world“ so ändern, dass er in eine Datei im Standardausgabeverzeichnis ausgegeben wird, anstatt in zu `stdout` auszugeben:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-output.yml":::

Sie können diesen Auftrag ausführen:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_output":::

Laden Sie die Protokolle herunter, wobei `helloworld.txt` im Verzeichnis `<RUN_ID>/outputs/` vorhanden ist:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_output_download":::

### <a name="data-outputs"></a>Datenausgaben

Sie können benannte Datenausgaben angeben. Dadurch wird ein Verzeichnis im Standarddatenspeicher erstellt, das standardmäßig mit Lese-/Schreibzugriff bereitgestellt wird.

Sie können den früheren Auftrag „hello world“ ändern, um in eine benannte Datenausgabe zu schreiben:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-output-data.yml":::

## <a name="hello-pipelines"></a>Hello-Pipelines

Pipelineaufträge können mehrere Aufträge parallel oder nacheinander ausführen. Wenn zwischen den Schritten in einer Pipeline Eingabe-/Ausgabeabhängigkeiten bestehen, wird der abhängige Schritt nach Abschluss des anderen Schritts ausgeführt.

Sie können einen „hello world“-Auftrag in zwei Aufträge aufteilen:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-pipeline.yml":::

Führen Sie ihn aus:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_pipeline":::

Die Aufträge „hello“ bzw. „world“ werden parallel ausgeführt, wenn das Computeziel über die dafür verfügbaren Ressourcen verfügt.

Definieren Sie eine Datenausgabe im Auftrag „hello“ und eine entsprechende Eingabe im Auftrag „world“, die sich auf die Ausgabe des vorherigen bezieht, um Daten zwischen den Schritten in einer Pipeline zu übergeben:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-pipeline-io.yml":::

Führen Sie ihn aus:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_pipeline_io":::

Dieses Mal wird der Auftrag „world“ ausgeführt, nachdem der Auftrag „hello“ abgeschlossen wurde.

Um zu vermeiden, dass allgemeine Einstellungen für Aufträge in einer Pipeline dupliziert werden, können Sie sie außerhalb der Aufträge festlegen:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-pipeline-settings.yml":::

Sie können diesen ausführen:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_pipeline_settings":::

Die entsprechende Einstellung für einen einzelnen Auftrag überschreibt die allgemeinen Einstellungen für einen Pipelineauftrag. Die bisherigen Konzepte können in einem dreistufigen Pipelineauftrag mit den Aufträgen „A“, „B“ und „C“ kombiniert werden. Der „C“-Auftrag weist eine Datenabhängigkeit vom Auftrag „B“ auf, während der Auftrag „A“ unabhängig ausgeführt werden kann. Der „A“-Auftrag verwendet auch eine individuell festgelegte Umgebung und bindet eine seiner Eingaben an eine Pipelineauftragseingabe der obersten Ebene:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-pipeline-abc.yml":::

Sie können diesen ausführen:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_pipeline_abc":::

## <a name="train-a-model"></a>Trainieren eines Modells

An diesem Punkt wurde noch kein Modell trainiert. Fügen wir `sklearn`-Code in ein Python-Skript mit MLflow-Nachverfolgung ein, um ein Modell auf der Iris-CSV-Datei zu trainieren:

:::code language="python" source="~/azureml-examples-main/cli/jobs/single-step/scikit-learn/iris/src/main.py":::

Das scikit-learn-Framework wird von MLflow für die automatische Protokollierung unterstützt, sodass ein einzelner `mlflow.autolog()`-Aufruf im Skript alle Modellparameter, Trainingsmetriken, Modellartefakte und einige zusätzliche Artefakte (in diesem Fall ein Konfusionsmatrixbild) protokolliert.

Um dies in der Cloud auszuführen, geben Sie als Auftrag an:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/single-step/scikit-learn/iris/job.yml":::

Führen Sie ihn aus:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="sklearn_iris":::

Um ein Modell zu registrieren, können Sie die Ausgaben herunterladen und ein Modell aus dem lokalen Verzeichnis erstellen:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="sklearn_download_register_model":::

## <a name="sweep-hyperparameters"></a>Optimieren von Hyperparametern (Sweeping)

Sie können den vorigen Auftrag ändern, um Hyperparameter zu optimieren:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/single-step/scikit-learn/iris/job-sweep.yml":::

Führen Sie ihn aus:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="sklearn_sweep":::

> [!TIP]
> Überprüfen Sie die Registerkarte „Untergeordnete Ausführungen“ in Studio, um den Fortschritt zu überwachen und Parameterdiagramme anzuzeigen.

Weitere Sweepoptionen finden Sie in der [YAML-Syntaxreferenz für den Sweepauftrag](reference-yaml-job-sweep.md).

## <a name="distributed-training"></a>Verteiltes Training

Azure Machine Learning unterstützt PyTorch, TensorFlow und MPI-basiertes verteiltes Training. Weitere Informationen finden Sie im Abschnitt [Verteilt der YAML-Syntaxreferenz für den Befehlsauftrag](reference-yaml-job-command.md#distribution-configurations).

Beispielsweise können Sie ein Convolutional Neural Network (CNN) für das CIFAR-10-Dataset mit verteiltem PyTorch trainieren. Das vollständige Skript ist [im Beispielrepository verfügbar](https://github.com/Azure/azureml-examples/tree/main/cli/jobs/single-step/pytorch/cifar-distributed).

Das CIFAR-10-Dataset in `torchvision` erwartet als Eingabe ein Verzeichnis, das das Verzeichnis `cifar-10-batches-py` enthält. Sie können die gezippte Quelle herunterladen und in ein lokales Verzeichnis extrahieren:

:::code language="azurecli" source="~/azureml-examples-main/setup-repo/create-datasets.sh" id="download_untar_cifar":::

Erstellen Sie dann ein Azure Machine Learning Dataset aus dem lokalen Verzeichnis, das in den Standarddatenspeicher hochgeladen wird:

:::code language="azurecli" source="~/azureml-examples-main/setup-repo/create-datasets.sh" id="create_cifar":::

Entfernen Sie optional die lokale Datei und das lokale Verzeichnis:

:::code language="azurecli" source="~/azureml-examples-main/setup-repo/create-datasets.sh" id="cleanup_cifar":::

Auf Datasets (nur Datei) kann in einem Auftrag mithilfe des Schlüssels `dataset` einer Dateneingabe verwiesen werden. Das Format ist `azureml:<DATASET_NAME>:<DATASET_VERSION>`. Für das soeben erstellte CIFAR-10-Dataset lautet es also `azureml:cifar-10-example:1`.

Wenn das Dataset vorhanden ist, können Sie einen verteilten PyTorch-Auftrag erstellen, um unser Modell zu trainieren:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/single-step/pytorch/cifar-distributed/job.yml":::

Führen Sie ihn aus:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="pytorch_cifar":::

## <a name="build-a-training-pipeline"></a>Erstellen einer Trainingspipeline

Das obige CIFAR-10-Beispiel lässt sich gut in einen Pipelineauftrag umsetzen. Der vorherige Auftrag kann für die Orchestrierung in einer Pipeline in drei Aufträge aufgeteilt werden:

- „get-data“ zum Ausführen eines Bash-Skripts zum Herunterladen und Extrahieren von `cifar-10-batches-py`
- „train-model“, um die Daten zu verwenden und ein Modell mit verteiltem PyTorch zu trainieren
- „eval-model“, um die Daten und das trainierte Modell zu verwenden und die Genauigkeit zu prüfen

Sowohl „train-model“ als auch „eval-model“ weisen eine Abhängigkeit von der Ausgabe des Auftrags „get-data“ auf. Darüber hinaus ist „eval-model“ von der Ausgabe des Auftrags „train-model“ abhängig. Daher werden die drei Aufträge sequenziell ausgeführt.

Sie können diese drei Aufträge innerhalb eines Pipelineauftrags orchestrieren:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/pipelines/cifar-10/job.yml":::

Und führen Sie folgendes aus:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="pipeline_cifar":::

Pipelines können auch mit wiederverwendbaren Komponenten geschrieben werden. Weitere Informationen finden Sie unter [Erstellen und Ausführen komponentenbasierter Machine Learning-Pipelines mit der Azure Machine Learning CLI (Vorschau)](how-to-create-component-pipelines-cli.md).

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen und Bewerten eines Machine Learning-Modells mit einem verwalteten Onlineendpunkt (Vorschau)](how-to-deploy-managed-online-endpoints.md)
