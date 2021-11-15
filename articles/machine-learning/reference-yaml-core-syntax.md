---
title: CLI (v2) YAML-Kernsyntax
titleSuffix: Azure Machine Learning
description: Übersicht CLI (v2) YAML-Kernsyntax.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: mx-iao
ms.author: minxia
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 943de3f7327c02e4bdb20bb364f530685bce2965
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566497"
---
# <a name="cli-v2-core-yaml-syntax"></a>CLI (v2) YAML-Kernsyntax

Jede Azure Machine Learning-Entität verfügt über eine schematisierte YAML-Darstellung. Sie können eine neue Entität aus einer YAML-Konfigurationsdatei mit der Erweiterung `.yml` oder `.yaml` erstellen.

Dieser Artikel bietet eine Übersicht über die grundlegenden Syntaxkonzepte, die beim Konfigurieren dieser YAML-Dateien auftreten werden.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="referencing-an-azure-ml-entity"></a>Verweisen auf eine Azure ML-Entität

Azure ML stellt eine Referenzsyntax (bestehend aus einem kompakten und einem ausgeschriebenen Format) zum Verweisen auf eine vorhandene Azure ML-Entität beim Konfigurieren einer YAML-Datei zur Verfügung. Sie können beispielsweise auf eine vorhandene registrierte Umgebung in Ihrem Arbeitsbereich verweisen, um sie in der Umgebung für einen Auftrag zu verwenden.

### <a name="shorthand"></a>Kompakt

Die kompakte Syntax besteht aus folgendem:

* Für Objekte: `azureml:<asset-name>:<asset-version>`
* Für Ressourcen: `azureml:<resource-name>`

Azure ML löst diesen Verweis auf das angegebene Objekt oder die angegebene Ressource im Arbeitsbereich auf.

### <a name="longhand"></a>Ausgeschrieben

Die ausgeschriebene Syntax besteht aus dem Präfix `azureml:` und der ARM-Ressourcen-ID der Entität:

```
azureml:/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>/environments/<environment-name>/versions/<environment-version>
```

## <a name="azure-ml-data-reference-uri"></a>Azure ML-Datenverweis-URI

Azure ML bietet ein komfortables URI-Format für Datenverweise, um auf Daten in einem Azure-Speicherdienst zu verweisen. Dies kann für Szenarien verwendet werden, in denen Sie einen Cloudspeicherort in Ihrer YAML-Datei angeben müssen, z. B. beim Erstellen eines Azure ML-Modells aus Dateien im Speicher oder beim Verweisen auf Daten, die als Eingabe an einen Auftrag übergeben werden sollen.

Um dieses Daten-URI-Format zu verwenden, muss der Speicherdienst, auf den Sie verweisen möchten, zunächst als Datenspeicher in Ihrem Arbeitsbereich registriert werden. Azure ML verarbeitet den Datenzugriff mit den Anmeldeinformationen, die Sie bei der Erstellung des Datenspeichers angegeben haben.

Das Format besteht aus einem Datenspeicher im aktuellen Arbeitsbereich und dem Pfad des Datenspeichers zu der Datei oder dem Ordner, auf die Sie verweisen möchten:

```
azureml://datastores/<datastore-name>/paths/<path-on-datastore>/
```

Zum Beispiel:

* `azureml://datastores/workspaceblobstore/paths/example-data/`
* `azureml://datastores/workspaceblobstore/paths/example-data/iris.csv`

Zusätzlich zum Azure ML-Datenverweis-URI unterstützt Azure ML auch die folgenden direkten Speicher-URI-Protokolle: `https`, `wasbs`, `abfss` und `adl` sowie öffentliche `http` und `https` URIs.

## <a name="expression-syntax-for-configuring-azure-ml-jobs-and-components"></a>Ausdruckssyntax zum Konfigurieren von Azure ML-Aufträgen und Komponenten

YAML-Dateien für einen v2-Auftrag und für v2-Komponenten ermöglichen die Verwendung von Ausdrücken zum Binden an Kontexte für verschiedene Szenarien. Der grundlegende Anwendungsfall verwendet einen Ausdruck für einen Wert, der zum Zeitpunkt der Dokumenterstellung der Konfiguration möglicherweise noch nicht bekannt ist, aber zur Runtime aufgelöst werden muss.

Verwenden Sie die folgende Syntax, um Azure ML anzuweisen, einen Ausdruck zu bewerten, anstatt ihn als Zeichenfolge zu behandeln:

`${{ <expression> }}`

Die unterstützten Szenarien werden unten behandelt.

### <a name="parameterizing-the-command-with-the-inputs-and-outputs-contexts-of-a-job"></a>Parametrisiert `command` mit den Kontexten `inputs` und `outputs` eines Auftrags

Sie können Literalwerte, URI-Pfade und Azure ML Datasets als Eingaben für einen Auftrag angeben. `command` kann dann mit Verweisen auf diese Eingabe(n) parametrisiert werden, indem die Syntax `${{inputs.<input-name>}}` verwendet wird. Verweise auf Literaleingaben werden zur Runtime in den Literalwert aufgelöst, während Verweise auf Daten-URI oder auf Azure ML-Dataseteingaben in den Downloadpfad oder den Einbindungspfad aufgelöst werden (je nach festgelegtem `mode`).

Ebenso können Ausgaben an den Auftrag auch an `command` verwiesen werden. Für jede im Wörterbuch `outputs` angegebene benannte Ausgabe generiert Azure ML automatisch einen Ausgabespeicherort im Standarddatenspeicher, in den Sie Dateien schreiben können. Der Ausgabespeicherort für jede benannte Ausgabe basiert auf dem folgenden vorgegebenen Pfad: `<default-datastore>/azureml/<job-name>/<output-name>/`. Durch Parametrisieren von `command` mit der Syntax `${{outputs.<output-name>}}` wird dieser Verweis auf den automatisch generierten Pfad aufgelöst, sodass Ihr Skript Dateien an diesen Speicherort des Auftrags schreiben kann.

Im folgenden Beispiel für eine YAML-Datei eines Befehlsauftrags wird `command` parametrisiert mit zwei Eingaben, einer Literaleingabe sowie einer URI-Eingabe und einer Ausgabe. Zur Runtime wird der Ausdruck `${{inputs.learning_rate}}` in `0.01` aufgelöst und der Ausdruck `${{inputs.iris}}` in den Downloadpfad `iris.csv` aufgelöst. `${{outputs.model_dir}}` wird in den Einbindungspfad des automatisch generierten Ausgabespeicherorts aufgelöst.

```yaml
$schema: https://azuremlschemas.azureedge.net/latest/commandJob.schema.json
code:
  local_path: ./src
command: python train.py --lr ${{inputs.learning_rate}} --training-data ${{inputs.iris}} --model-dir ${{outputs.model_dir}}
environment: azureml:AzureML-Minimal:1
compute: azureml:cpu-cluster
inputs:
  learning_rate: 0.01
  iris:
    file: https://azuremlexamples.blob.core.windows.net/datasets/iris.csv
    mode: download
outputs:
  model_dir:
```

### <a name="parameterizing-the-command-with-the-search_space-context-of-a-sweep-job"></a>Parametrisiert `command` mit dem Kontext `search_space` eines Löschauftrags

Sie verwenden diese Ausdruckssyntax auch, wenn Sie die Hyperparameteroptimierung durch einen Löschauftrag ausführen, da die tatsächlichen Hyperparameterwerte zur Zeit der Dokumenterstellung des Auftrags nicht bekannt sind. Wenn Sie einen Löschauftrag ausführen, wählt Azure ML Hyperparameterwerte für jede Testversion basierend auf `search_space` aus. Um auf diese Werte in Ihrem Trainingsskript zugreifen zu können, müssen Sie diese über die Befehlszeilenargumente des Skripts übergeben. Verwenden Sie dazu die Syntax `${{search_space.<hyperparameter>}}` in `trial.command`.

Im folgenden Beispiel für eine YAML-Datei für einen Löschauftrag werden die Verweise `${{search_space.learning_rate}}` und `${{search_space.boosting}}` in `trial.command` in die tatsächlichen Hyperparameterwerte aufgelöst, die für jede Testversion beim Übermittelt des Testauftrags zur Ausführung ausgewählt wurden.

```yaml
$schema: https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json
type: sweep
sampling_algorithm: random
search_space:
  learning_rate:
    type: uniform
    min_value: 0.01
    max_value: 0.9
  boosting:
    type: choice
    values: ["gbdt", "dart"]
objective:
  goal: minimize
  primary_metric: test-multi_logloss
trial:
  code: 
    local_path: src 
  command: >-
    python train.py 
    --training-data ${{inputs.iris}}
    --lr ${{search_space.learning_rate}}
    --boosting ${{search_space.boosting}}
  environment: azureml:AzureML-Minimal:1
inputs:
  iris:
    file: https://azuremlexamples.blob.core.windows.net/datasets/iris.csv
    mode: download
compute: azureml:cpu-cluster
```

### <a name="binding-inputs-and-outputs-between-steps-in-a-pipeline-job"></a>Binden von Eingaben und Ausgaben zwischen den Schritten in einem Pipelineauftrag

Ausdrücke werden auch zum Binden von Eingaben und Ausgaben zwischen Schritten in einem Pipelineauftrag verwendet. Beispielsweise können Sie die Eingabe eines Auftrags (Auftrag #2) in einer Pipeline an die Ausgabe eines anderen Auftrags (Auftrag #1) binden. Diese Verwendung signalisiert Azure ML den Abhängigkeitsflow des Pipelinediagramms und Auftrag #2 wird nach Auftrag #1 ausgeführt, da die Ausgabe von Auftrag #1 als Eingabe für Auftrag #2 erforderlich ist.

Für eine YAML-Datei eines Pipelineauftrags werden die Abschnitte `inputs` und `outputs` jedes untergeordneten Auftrags im übergeordneten Kontext (dem Pipelineauftrag der obersten Ebene) ausgewertet. Andererseits wird `command` in den aktuellen Kontext (den untergeordneten Auftrag) aufgelöst.

Es gibt zwei Möglichkeiten, Eingaben und Ausgaben in einem Pipelineauftrag zu binden:

**1) Binden an die Eingaben und Ausgaben der obersten Ebene des Pipelineauftrags**

Sie können die Ein- oder Ausgaben eines untergeordneten Auftrags mithilfe der folgenden Syntax an die Ein-/Ausgaben des übergeordneten Pipelineauftrags der obersten Ebene binden: `${{inputs.<input-name>}}` oder `${{outputs.<output-name>}}`. Dieser Verweis löst in den übergeordneten Kontext auf; daher die Eingaben/Ausgaben der obersten Ebene. 

Im folgenden Beispiel wird die Ausgabe (`model_dir`) des letzten `train` Schritts über `${{outputs.trained_model}}` an die Ausgabe des Pipelineauftrags der obersten Ebene gebunden

**2) Binden an die Ein- und Ausgaben eines anderen untergeordneten Auftrags (Schritt)**

Verwenden Sie die folgende Syntax, um die Eingaben/Ausgaben eines Schritts an die Eingaben/Ausgaben eines anderen Schritts zu binden: `${{jobs.<step-name>.inputs.<input-name>}}` oder `${{jobs.<step-name>.outputs.<outputs-name>}}`. Auch hier löst dieser Verweis in den übergeordneten Kontext auf, sodass der Kontext mit `jobs.<step-name>` beginnt.

Im folgenden Beispiel wird die Eingabe (`clean_data`) des Schritts `train` über `${{jobs.prep.outputs.prep_data}}` an die Ausgabe (`prep_data`) des Schritts `prep` gebunden. Die vorbereiteten Daten aus dem Schritt `prep` werden als Trainingsdaten für den Schritt `train` verwendet.

Andererseits werden die Kontextverweise innerhalb der Eigenschaften `command` in den aktuellen Kontext aufgelöst. Beispielsweise wird der Verweis `${{inputs.raw_data}}` in `prep` des Schritts `command` in die Eingaben des aktuellen Kontexts aufgelöst, bei dem es sich um den untergeordneten Auftrag `prep` handelt. Der Lookup wird auf `prep.inputs` durchgeführt, sodass eine Eingabe mit dem Namen `raw_data` dort definiert werden muss.

```yaml
$schema: https://azuremlschemas.azureedge.net/latest/pipelineJob.schema.json
type: pipeline
inputs:
outputs:
  trained_model:
jobs:
  prep:
    type: command
    inputs:
      raw_data:
        folder:
        mode: rw_mount
    outputs:
      prep_data: 
        mode: upload
    code:
      local_path: src/prep
    environment: azureml:AzureML-Minimal:1
    command: >-
      python prep.py 
      --raw-data ${{inputs.raw_data}} 
      --prep-data ${{outputs.prep_data}}
    compute: azureml:cpu-cluster
  train:
    type: command
    inputs: 
      clean_data: ${{jobs.prep.outputs.prep_data}}
    outputs:
      model_dir: $${{outputs.trained_model}}
    code: 
      local_path: src/train
    environment: azureml:AzureML-Minimal:1
    compute: azureml:gpu-cluster
    command: >-
      python train.py 
      --training-data ${{inputs.clean_data}} 
      --model-output ${{outputs.model_dir}}
```

### <a name="parameterizing-the-command-with-the-inputs-and-outputs-contexts-of-a-component"></a>Parametrisiert `command` mit den Kontexten `inputs` und `outputs` einer Komponente

Ähnlich wie bei `command` eines Auftrags kann `command` für eine Komponente auch mit Verweisen auf die Kontexte `inputs` und `outputs` parametrisiert werden. In diesem Fall bezieht sich der Verweis auf die Ein- und Ausgaben dieser Komponente. Wenn die Komponente in einem Auftrag ausgeführt wird, löst Azure ML diese Verweise auf die Eingabe- und Ausgabewerte der Runtime des Auftrags auf, die für die jeweiligen Komponenteneingaben und -ausgaben angegeben sind. Im Folgenden finden Sie ein Beispiel für die Verwendung der Kontextsyntax für eine YAML-Spezifikation der Befehlskomponente.

```yaml
$schema: https://azuremlschemas.azureedge.net/latest/commandComponent.schema.json
type: command
code:
  local_path: ./src
command: python train.py --lr ${{inputs.learning_rate}} --training-data ${{inputs.iris}} --model-dir ${{outputs.model_dir}}
environment: azureml:AzureML-Minimal:1
inputs:
  learning_rate:
    type: number
    default: 0.01
    optional: true
  iris:
    type: path
outputs:
  model_dir:
    type: path
```

## <a name="next-steps"></a>Nächste Schritte

* [Installieren und Verwenden der CLI (V2)](how-to-configure-cli.md)
* [Trainieren von Modellen mit der CLI (v2)](how-to-train-cli.md)
* [YAML-Schemas für CLI (v2)](reference-yaml-overview.md)
