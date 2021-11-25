---
title: Erstellen und Ausführen von komponentenbasierten ML-Pipelines (CLI)
titleSuffix: Azure Machine Learning
description: Erstellen und Ausführen von Machine Learning-Pipelines mit der Azure Machine Learning-CLI
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: devplatv2
ms.openlocfilehash: 7dafb069419c1ff42d2ec5358fbd8eb37465c88f
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132552339"
---
# <a name="create-and-run-machine-learning-pipelines-using-components-with-the-azure-machine-learning-cli-preview"></a>Erstellen und Ausführen von Machine Learning-Pipelines mit Komponenten und der Azure Machine Learning-CLI (Vorschau)

In diesem Artikel erfahren Sie, wie Sie [Machine Learning-Pipelines](concept-ml-pipelines.md) mit der Azure-Befehlszeilenschnittstelle und Komponenten erstellen und ausführen. (Weitere Informationen finden Sie unter [Was ist eine Azure Machine Learning -Komponente?](concept-component.md).) Sie können [Pipelines ohne Komponenten erstellen](how-to-train-cli.md#build-a-training-pipeline), Komponenten bieten aber die größte Flexibilität und die meisten Möglichkeiten zur Wiederverwendung. Azure ML-Pipelines können in YAML definiert und über die Befehlszeilenschnittstelle (CLI) ausgeführt oder in Python bzw. im Azure ML Studio Designer auf einer Benutzeroberfläche per Drag & Drop erstellt werden. In diesem Dokument liegt der Schwerpunkt auf der Befehlszeilenschnittstelle.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://azure.microsoft.com/free/) aus.

* Sie benötigen einen [Azure Machine Learning-Arbeitsbereich](how-to-manage-workspace.md) für Ihre Pipelines und die zugehörigen Ressourcen.

* [Installieren und Einrichten der Azure CLI-Erweiterung für Machine Learning](how-to-configure-cli.md)

* Klonen Sie das Beispielrepository:

    ```azurecli-interactive
    git clone https://github.com/Azure/azureml-examples --depth 1
    cd azureml-examples/cli/jobs/pipelines-with-components/
    ```

## <a name="introducing-machine-learning-pipelines"></a>Einführung in Machine Learning-Pipelines

Mithilfe von Pipelines in Azure ML können Sie eine Sammlung von Tasks zum maschinellen Lernen in einem Workflow sequenzieren. Wissenschaftliche Fachkräfte für Daten verwenden für die Iteration in der Regel Skripts für spezielle Tasks wie Datenaufbereitung, Training, Bewertung usw. Wenn alle diese Skripts vorbereitet sind, helfen Pipelines dabei, eine Sammlung solcher Skripts mit Prozessen auf Produktionsniveau zu verbinden, für die Folgendes gilt:

| Vorteil | BESCHREIBUNG |
| --- | --- |
| Eigenständig | Pipelines können stunden- oder sogar tagelang vollständig eigenständig ausgeführt werden. Dabei werden Upstreamdaten ohne manuellen Eingriff übernommen, verarbeitet und an nachfolgende Skripts übergeben. |
| Leistungsstark | Pipelines können in großen Computeclustern ausgeführt werden, die in der Cloud gehostet werden und über die Verarbeitungsleistung für großer Datasets oder zum Ausführen von Tausenden von Sweeps verfügen, um die besten Modelle zu finden. | 
| Wiederholbar und automatisierbar | Pipelines können so geplant werden, dass sie neue Daten ausführen und verarbeiten und ML-Modelle aktualisieren, sodass Workflows zum maschinellen Lernen wiederholbar werden. | 
| Reproduzierbar | Pipelines können reproduzierbare Ergebnisse generieren, indem sämtliche Aktivitäten protokolliert und alle Ausgaben (einschließlich Zwischendaten) in der Cloud gespeichert werden, um Compliance- und Überwachungsanforderungen zu erfüllen. |

Azure bietet noch andere Pipelinetypen: Azure Data Factory-Pipelines unterstützen insbesondere Daten-zu-Daten-Pipelines, während Azure Pipelines die beste Wahl für die CI/CD-Automatisierung ist. [Vergleichen Sie Machine Learning-Pipelines mit verschiedenen anderen Pipelines.](concept-ml-pipelines.md#which-azure-pipeline-technology-should-i-use)

## <a name="create-your-first-pipeline"></a>Erstellen Ihrer ersten Pipeline

Navigieren Sie im [Repository `azureml-examples`](https://github.com/Azure/azureml-examples) im Verzeichnis `cli/jobs/pipelines-with-components/basics` zum Unterverzeichnis `3a_basic_pipeline` (die älteren Beispiele in diesem Verzeichnis veranschaulichen Pipelines ohne Komponenten). Listen Sie Ihre verfügbaren Computeressourcen mit dem folgenden Befehl auf: 

```azurecli
az ml compute list
```

Falls Sie über keinen Cluster verfügen, erstellen Sie einen mit dem Namen `cpu-cluster`, indem Sie folgenden Befehl ausführen:

```azurecli
az ml compute create -n cpu-cluster --type amlcompute --min-instances 0 --max-instances 10
```

Erstellen Sie nun mit dem folgenden Befehl einen Pipelineauftrag:

```azurecli
az ml job create --file pipeline.yml
```

Sie sollten ein JSON-Wörterbuch mit Informationen zum Pipelineauftrag erhalten, einschließlich:

| Schlüssel | BESCHREIBUNG | 
| --- | --- | 
| `name` | Der GUID-basierte Name des Auftrags | 
| `experiment_name` | Der Name, unter dem Aufträge in Studio organisiert werden | 
| `services.Studio.endpoint` | Eine URL zum Überwachen und Überprüfen des Pipelineauftrags | 
| `status` | Der Status des Auftrags. Dieser wird zu diesem Zeitpunkt wahrscheinlich `Preparing` lauten. |

### <a name="review-a-component"></a>Überprüfen einer Komponente 

 Sehen Sie sich den Python-Quellcode in `componentA_src`, `componentB_src` und `componentC_src` kurz an. Wie Sie erkennen können, enthält jedes dieser Verzeichnisse ein etwas anderes „Hallo Welt“-Programm in Python. 

Öffnen Sie `ComponentA.yaml`, um zu sehen, wie die erste Komponente definiert ist: 

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/pipelines-with-components/basics/3a_basic_pipeline/componentA.yml":::

In der aktuellen Vorschauversion werden nur Komponenten vom Typ `command` unterstützt. `name` ist der eindeutige Bezeichner, der in Studio verwendet wird, um die Komponente zu beschreiben, und `display_name` dient als Anzeigename. Mit dem Schlüssel-Wert-Paar `version` können Sie Ihre Pipelinekomponenten weiterentwickeln und gleichzeitig die Reproduzierbarkeit mit älteren Versionen aufrechterhalten. 

Alle Dateien im Wert `code.local_path` werden zur Verarbeitung in Azure hochgeladen. 

Im Abschnitt `environment` können Sie die Softwareumgebung angeben, in der die Komponente ausgeführt wird. In diesem Fall verwendet die Komponente ein Docker-Basisimage, wie in `environment.image` angegeben. Weitere Informationen finden Sie unter [Erstellen und Verwenden von Softwareumgebungen in Azure Machine Learning](how-to-use-environments.md). 

Schließlich wird der Schlüssel `command` verwendet, um den auszuführenden Befehl anzugeben.

> [!NOTE]
> Der Wert von `command` beginnt mit `>-`. Dies ist der YAML-Faltungsstil mit Block-Chomping. Damit können Sie Ihren Befehl zur besseren Übersichtlichkeit über mehrere Textzeilen schreiben.

Weitere Informationen zu Komponenten und ihrer Spezifikation finden Sie unter [Was ist eine Azure Machine Learning-Komponente?](concept-component.md).

### <a name="review-the-pipeline-specification"></a>Überprüfen der Pipelinespezifikation

Im Beispielverzeichnis sieht die Datei `pipeline.yaml` wie der folgende Code aus:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/pipelines-with-components/basics/3a_basic_pipeline/pipeline.yml":::

Wenn Sie die URL des Auftrags in Studio öffnen (der Wert von `services.Studio.endpoint` aus dem Befehl `job create` beim Erstellen eines Auftrags oder `job show`, nachdem der Auftrag erstellt wurde), wird eine Graphdarstellung Ihrer Pipeline angezeigt:

:::image type="content" source="media/how-to-create-component-pipelines-cli/pipeline-graph.png" lightbox="media/how-to-create-component-pipelines-cli/pipeline-graph.png" alt-text="Graphdarstellung der Pipeline in Studio":::

Es gibt keine Abhängigkeiten zwischen den Komponenten in dieser Pipeline. Im Allgemeinen weisen Pipelines Abhängigkeiten auf, die auf dieser Seite visuell dargestellt werden. Da diese Komponenten nicht voneinander abhängig sind und der `cpu-cluster` über genügend Knoten verfügt, wurden sie gleichzeitig ausgeführt. 

Wenn Sie im Pipelinegraphen auf eine Komponente doppelklicken, werden Details zur untergeordneten Ausführung der Komponente angezeigt. 

:::image type="content" source="media/how-to-create-component-pipelines-cli/component-details.png" alt-text="Screenshot mit Details und Ausgaben der untergeordneten Ausführung einer Komponente" lightbox="media/how-to-create-component-pipelines-cli/component-details.png"::: 

## <a name="upload-and-use-data"></a>Hochladen und Verwenden von Daten

Das Beispiel `3b_pipeline_with_data` veranschaulicht, wie Sie den Datenfluss und die Speicherung der Ein- und Ausgaben in Pipelines definieren. 

Sie definieren Eingabedatenverzeichnisse für Ihre Pipeline in der YAML-Pipelinedatei mithilfe des `inputs`-Pfads. Die Verzeichnisse für Ausgabe- und Zwischendaten definieren Sie mithilfe des `outputs`-Pfads. Sie verwenden diese Definitionen in den Pfaden `jobs.<JOB_NAME>.inputs` und `jobs.<JOB_NAME>.outputs`, wie in der folgenden Abbildung dargestellt:

:::image type="content" source="media/how-to-create-component-pipelines-cli/inputs-and-outputs.png" alt-text="Abbildung der Zuordnung von Ein- und Ausgabepfaden zu den Ein- und Ausgabepfaden von Aufträgen" lightbox="media/how-to-create-component-pipelines-cli/inputs-and-outputs.png":::

1. Der Pfad `inputs.pipeline_sample_input_data` erstellt einen Schlüsselbezeichner und lädt die Eingabedaten aus dem Verzeichnis `local_path` hoch. Dieser Bezeichner `${{inputs.pipeline_sample_input_data}}` wird dann als Wert des Schlüssels `jobs.componentA_job.inputs.componentA_input` verwendet. 
1. Der Pfad `jobs.componentA_job.outputs.componentA_output` dient als Bezeichner (`${{jobs.componentA_job.outputs.componentA_output`}}) als der Wert des Schlüssels `jobs.componentB_job.inputs.componentB_input` im nächsten Schritt. 
1. Wie bei Komponente A wird die Ausgabe von Komponente B als Eingabe für Komponente C verwendet.
1. Der Schlüssel `outputs.final_pipeline_output` der Pipeline ist die Quelle des Bezeichners, der als Wert für den Schlüssel `jobs.componentC_job.outputs.componentC_output` verwendet wird. Anders ausgedrückt: Die Ausgabe von Komponente C ist die endgültige Ausgabe der Pipeline.

Die Visualisierung dieser Pipeline sieht in Studio wie folgt aus: 

:::image type="content" source="media/how-to-create-component-pipelines-cli/pipeline-graph-dependencies.png" alt-text="Screenshot der Graphansicht einer Pipeline mit Datenabhängigkeiten in Studio" lightbox="media/how-to-create-component-pipelines-cli/pipeline-graph-dependencies.png":::

Sie können erkennen, dass `inputs.pipeline_sample_input_data` als `Dataset` dargestellt wird. Die Schlüssel der Pfade `jobs.<COMPONENT_NAME>.inputs` und `outputs` werden als Datenflüsse zwischen den Pipelinekomponenten angezeigt.

Sie können dieses Beispiel ausführen, indem Sie zum Unterverzeichnis `3b_pipeline_with_data` des Beispielrepositorys wechseln und Folgendes ausführen:

`az ml job create --file pipeline.yaml`

### <a name="access-data-in-your-script"></a>Zugreifen auf Daten im Skript

Die Pfade der Ein- und Ausgabeverzeichnisse einer Komponente werden als Argumente an Ihr Skript übergeben. Der Name des Arguments ist der Schlüssel, den Sie in der YAML-Datei im Pfad `inputs` bzw. `outputs` angegeben haben. Beispiel:

```python
import argparse

parser = argparse.ArgumentParser()
parser.add_argument("--componentA_input", type=str)
parser.add_argument("--componentA_output", type=str)

args = parser.parse_args()

print("componentA_input path: %s" % args.componentA_input)
print("componentA_output path: %s" % args.componentA_output)

```

Für Eingaben lädt der Pipelineorchestrator die Daten aus dem Cloudspeicher herunter (oder bindet sie ein) und stellt sie als lokalen Ordner zum Lesen für das Skript zur Verfügung, das in jedem Auftrag ausgeführt wird. Aufgrund dieses Verhaltens sind keine Änderungen m Skript zwischen einer lokalen Ausführung und der Ausführung in der Cloud notwendig. Ebenso schreibt das Skript Ausgaben in einen lokalen Ordner, der in den Cloudspeicher eingebunden und mit diesem synchronisiert oder nach Abschluss des Skripts hochgeladen wird. Sie können das Schlüsselwort `mode` verwenden, um für die Ein- und Ausgaben das Herunterladen oder Einbinden festzulegen. 

## <a name="create-a-preparation-train-evaluate-pipeline"></a>Erstellen einer Pipeline zum Vorbereiten, Trainieren und Auswerten

Eines der häufigsten Szenarien für Machine Learning-Pipelines umfasst drei Hauptphasen:

1. Datenaufbereitung
1. Training
1. Auswerten des Modells

Jede dieser Phasen kann mehrere Komponenten enthalten. Beispielsweise kann der Datenaufbereitungsschritt separate Schritte zum Laden und Transformieren der Trainingsdaten umfassen. Das Repository mit den Beispielen enthält im Verzeichnis `cli/jobs/pipelines-with-components/nyc_taxi_data_regression` eine End-to-End-Beispielpipeline. 

Die Datei `job.yml` beginnt mit dem obligatorischen Schlüssel-Wert-Paar `type: pipeline`. Anschließend werden die Ein- und Ausgaben wie folgt definiert:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/pipelines-with-components/nyc_taxi_data_regression/job.yml" range="5-22":::

Wie bereits beschrieben, geben diese Einträge die Eingabedaten für die Pipeline (in diesem Fall das Dataset in `./data`) und die Zwischen- und Endausgaben der Pipeline an, die in separaten Pfaden gespeichert werden. Die Namen in diesen Ein- und Ausgabeeinträgen werden zu Werten in den Einträgen `inputs` und `outputs` der einzelnen Aufträge: 

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/pipelines-with-components/nyc_taxi_data_regression/job.yml" range="26-72":::

Beachten Sie, wie `jobs.train_job.outputs.model_output` als Eingabe für den Vorhersageauftrag und den Bewertungsauftrag verwendet wird, wie im folgenden Diagramm dargestellt: 

:::image type="content" source="media/how-to-create-component-pipelines-cli/regression-graph.png" alt-text="Pipelinegraph des Vorhersagetasks für Taxigebühren in New York" lightbox="media/how-to-create-component-pipelines-cli/regression-graph.png":::

## <a name="caching--reuse"></a>Zwischenspeichern und Wiederverwenden  

Standardmäßig werden nur die Komponenten erneut ausgeführt, deren Eingaben geändert wurden. Sie können dieses Verhalten ändern, indem Sie den Schlüssel `is_deterministic` in der YAML-Komponentenspezifikation auf `False` festlegen. Hierfür ist häufig eine Komponente notwendig, die Daten lädt, die möglicherweise von einem festen Speicherort oder einer festen URL aktualisiert wurden. 

## <a name="faq"></a>Häufig gestellte Fragen

### <a name="how-do-i-change-the-location-of-the-outputs-generated-by-the-pipeline"></a>Wie ändere ich den Speicherort der von der Pipeline generierten Ausgaben?
Sie können den Abschnitt `settings` im Pipelineauftrag verwenden, um einen anderen Datenspeicher für alle Aufträge in der Pipeline anzugeben (siehe Zeile 25 bis 26 in [diesem Beispiel](https://github.com/Azure/azureml-examples/blob/main/cli/jobs/pipelines-with-components/basics/1a_e2e_local_components/pipeline.yml)). Das Angeben eines anderen Datenspeichers für einen bestimmten Auftrag oder eine bestimmte Ausgabe wird derzeit nicht unterstützt. Außerdem wird auch das Angeben von Pfaden, in denen Ausgaben im Datenspeicher gespeichert werden, derzeit nicht unterstützt.

### <a name="how-do-i-specify-a-compute-that-can-be-used-by-all-jobs"></a>Wie gebe ich ein Computeziel an, das von allen Aufträgen verwendet werden kann?
Sie können ein Computeziel auf der Ebene des Pipelineauftrags angeben, das dann von den Aufträgen verwendet wird, für die kein Computeziel explizit angegeben wurde. (Siehe Zeile 28 in [diesem Beispiel](https://github.com/Azure/azureml-examples/blob/main/cli/jobs/pipelines-with-components/basics/1a_e2e_local_components/pipeline.yml).)

### <a name="what-job-types-are-supported-in-the-pipeline-job"></a>Welche Auftragstypen werden im Pipelineauftrag unterstützt?
Das aktuelle Release unterstützt Befehls-, Komponenten- und Sweep-Auftragstypen.

### <a name="what-are-the-different-modes-that-i-use-with-inputs-or-outputs"></a>Welche verschiedenen Modi kann ich mit Ein- oder Ausgaben verwenden?
| Kategorie | Zulässige Modi | Standard |
| --- | --- | --- |
| Dataseteingaben | `ro_mount` und `download` | `ro_mount` |
| URI-Eingaben | `ro_mount`, `rw_mount` und `download` | `ro_mount` | 
| Ausgaben | `rw_mount`, `upload` | `rw_mount` | 

### <a name="when-do-i-use-command-jobs-vs-component-jobs"></a>Wann verwende ich Befehlsaufträge und wann Komponentenaufträge?
Sie können mit Befehlsaufträgen schnelle Iterationen durchführen und sie dann zu einer Pipeline verbinden. Dadurch wird die Pipeline jedoch monolithisch. Wenn eine andere Person einen der Schritte der Pipeline in einer anderen Pipeline verwenden möchte, muss sie die Auftragsdefinition, die Skripts, die Umgebung usw. kopieren. Wenn Sie die einzelnen Schritte pipelineübergreifend wiederverwendbar machen und dafür sorgen möchten, dass sie für andere Personen in Ihrem Team einfach zu verstehen und zu verwenden sind, sollten Sie zusätzliche Schritte zum Erstellen und Registrieren durchführen. Ein weiterer Grund, warum Sie die Verwendung von Komponenten in Betracht ziehen sollten, ist die Möglichkeit die Designer-Benutzeroberfläche mit Drag & Drop-Funktionen verwenden zu können, um Pipelines zu erstellen. Da Aufträge nicht im Arbeitsbereich registriert werden, können Sie sie nicht per Drag & Drop auf die Designer-Canvas ziehen.

### <a name="im-doing-distributed-training-in-my-component-the-component-which-is-registered-specifies-distributed-training-settings-including-node-count-how-can-i-change-the-number-of-nodes-used-during-runtime-the-optimal-number-of-nodes-is-best-determined-at-runtime-so-i-dont-want-to-update-the-component-and-register-a-new-version"></a>Ich führe in meiner Komponente verteiltes Training aus. Die (registrierte) Komponente gibt Einstellungen für das verteilte Training einschließlich der Knotenanzahl an. Wie kann ich während der Laufzeit die Anzahl der verwendeten Knoten ändern? Da die optimale Anzahl von Knoten am besten zur Laufzeit bestimmt wird, möchte ich die Komponente nicht aktualisieren und eine neue Version registrieren.

Sie können den Abschnitt „overrides“ im Komponentenauftrag verwenden, um die Ressourcen- und Verteilungseinstellungen zu ändern. Sehen Sie sich dazu [dieses Beispiel mit TensorFlow](https://github.com/Azure/azureml-examples/tree/main/cli/jobs/pipelines-with-components/basics/6a_tf_hello_world) oder [dieses Beispiel mit PyTorch](https://github.com/Azure/azureml-examples/tree/main/cli/jobs/pipelines-with-components/basics/6c_pytorch_hello_world) an.  

### <a name="how-can-i-define-an-environment-with-conda-dependencies-inside-a-component"></a>Wie kann ich eine Umgebung mit Conda-Abhängigkeiten innerhalb einer Komponente definieren?
Weitere Informationen finden Sie in [diesem Beispiel](https://github.com/Azure/azureml-examples/tree/main/cli/jobs/pipelines-with-components/basics/5c_env_conda_file).
 

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Freigeben Ihrer Pipeline für Kollegen oder Kunden finden Sie unter [Veröffentlichen von Pipelines des maschinellen Lernens](how-to-deploy-pipelines.md).
- Verwenden Sie [diese Jupyter Notebooks auf GitHub](https://aka.ms/aml-pipeline-readme), um Pipelines des maschinellen Lernens eingehender zu erkunden.
- Hinweise zu den Paketen [azureml-pipelines-core](/python/api/azureml-pipeline-core/) und [azureml-pipelines-steps](/python/api/azureml-pipeline-steps/) finden Sie in der SDK-Referenzhilfe.
- Tipps zum Debuggen und zur Problembehandlung bei Pipelines finden Sie unter [Problembehandlung bei Machine Learning-Pipelines](how-to-debug-pipelines.md).
- Informationen zum Ausführen von Notebooks finden Sie im Artikel [Verwenden von Jupyter-Notebooks zum Erkunden des Azure Machine Learning-Diensts](samples-notebooks.md).
