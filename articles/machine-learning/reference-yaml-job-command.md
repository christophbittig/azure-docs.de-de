---
title: YAML-Schema für Befehlsaufträge der CLI (v2)
titleSuffix: Azure Machine Learning
description: Referenzdokumentation für das YAML-Schema für Befehlsaufträge der CLI (v2)
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: mx-iao
ms.author: minxia
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: ecfd6d57d7f978e64bed0003253a05941effa910
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132060962"
---
# <a name="cli-v2-command-job-yaml-schema"></a>YAML-Schema für Befehlsaufträge der CLI (v2)

Das JSON-Quellschema finden Sie unter https://azuremlschemas.azureedge.net/latest/commandJob.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML-Syntax

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte | Standardwert |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | Zeichenfolge | Das YAML-Schema. Wenn Sie die YAML-Datei mithilfe der VS Code-Erweiterung für Azure Machine Learning erstellen, können Sie `$schema` am Anfang der Datei Schema- und Ressourcenvervollständigungen aufrufen. | | |
| `type` | const | Typ des Auftrags. | `command` | `command` |
| `name` | Zeichenfolge | Der Name des Auftrags. Muss für alle Aufträge im Arbeitsbereich eindeutig sein. Wenn diese Angabe nicht erfolgt, generiert Azure ML automatisch eine GUID für den Namen. | | |
| `display_name` | Zeichenfolge | Anzeigename des Auftrags auf der Studio-Benutzeroberfläche. Kann innerhalb des Arbeitsbereichs nicht eindeutig sein. Wenn kein Wert angegeben wird, generiert Azure ML automatisch einen für Menschen lesbaren Adjektiv-Nomen-Bezeichner für den Anzeigenamen. | | |
| `experiment_name` | Zeichenfolge | Experimentname, unter dem der Auftrag organisiert werden soll. Die Ausführungsaufzeichnung jedes Auftrags wird in Studio unter dem entsprechenden Experiment auf der Registerkarte „Experimente“ organisiert. Ohne Angabe verwendet Azure ML standardmäßig den Namen des Arbeitsverzeichnisses verwendet, in dem der Auftrag erstellt wurde. | | |
| `description` | Zeichenfolge | Beschreibung des Auftrags. | | |
| `tags` | Objekt (object) | Wörterbuch der Tags für den Auftrag. | | |
| `command` | Zeichenfolge | **Erforderlich.** Den auszuführenden Befehl. | | |
| `code.local_path` | Zeichenfolge | Lokaler Pfad zum Quellcodeverzeichnis, das hochgeladen und für den Auftrag verwendet werden soll. | | |
| `environment` | Zeichenfolge oder Objekt | **Erforderlich.** Die Umgebung, die für den Auftrag verwendet werden soll. Dies kann entweder ein Verweis auf eine vorhandene Umgebung mit Versionsangabe im Arbeitsbereich oder eine Inlineumgebungsspezifikation sein. <br><br> Verwenden Sie die Syntax `azureml:<environment_name>:<environment_version>`, um auf eine vorhandene Umgebung zu verweisen. <br><br> Um eine Umgebung inline zu definieren, befolgen Sie das [Umgebungsschema](reference-yaml-environment.md#yaml-syntax). Schließen Sie die Eigenschaften `name` und `version` aus, da sie für Inlineumgebungen nicht unterstützt werden. | | |
| `environment_variables` | Objekt (object) | Wörterbuch der Name-Wert-Paare von Umgebungsvariablen, die für den Prozess festgelegt werden, in dem der Befehl ausgeführt wird. | | |
| `distribution` | Objekt (object) | Die Verteilungskonfiguration für verteilte Trainingsszenarien. Eine von [MpiConfiguration,](#mpiconfiguration)[PyTorchConfiguration](#pytorchconfiguration)oder [TensorFlowConfiguration](#tensorflowconfiguration). | | |
| `compute` | Zeichenfolge | Name des Computeziels, auf dem der Auftrag ausgeführt werden soll. Dies kann entweder ein Verweis auf ein vorhandenes Compute im Arbeitsbereich (mithilfe der Syntax `azureml:<compute_name>`) oder `local` zum Festlegen der lokalen Ausführung sein. | | `local` |
| `resources.instance_count` | integer | Die Anzahl von Knoten, die für den Auftrag verwendet werden sollen. | | `1` |
| `limits.timeout` | integer | Die maximale Zeit in Sekunden, die der Auftrag ausgeführt werden darf. Sobald dieser Grenzwert erreicht ist, bricht das System den Auftrag ab. | | |
| `inputs` | Objekt (object) | Wörterbuch der Eingaben für den Auftrag. Der Schlüssel ist ein Name für die Eingabe im Kontext des Auftrags, und der Wert ist der Eingabewert. <br><br> Eingaben können im `command` mit dem Ausdruck `${{ inputs.<input_name> }}` verwiesen werden. | | |
| `inputs.<input_name>` | number, integer, boolean, string oder object | Einer eines Literalwerts (vom Typ number, integer, boolean oder string), [JobInputUri](#jobinputuri)oder [JobInputDataset.](#jobinputdataset) | | |
| `outputs` | Objekt (object) | Wörterbuch der Ausgabekonfigurationen des Auftrags. Der Schlüssel ist ein Name für die Ausgabe im Kontext des Auftrags, und der Wert ist die Ausgabekonfiguration. <br><br> Ausgaben können im `command` mit dem Ausdruck `${{ outputs.<output_name> }}` verwiesen werden. | |
| `outputs.<output_name>` | Objekt (object) | Sie können entweder einen optionalen `mode` angeben oder das Objekt leer lassen. Für jede im Wörterbuch (`outputs`) angegebene benannte Ausgabe generiert Azure ML automatisch einen Ausgabespeicherort. | |
| `outputs.<output_name>.mode` | Zeichenfolge | Modus, wie Ausgabedateien an den Zielspeicher übermittelt werden. Für den Lese-/Schreib-Bereitstellungsmodus ist das Ausgabeverzeichnis ein bereitgestelltes Verzeichnis. Im Upload-Modus werden Dateien, die in das Ausgabeverzeichnis geschrieben werden, zum Auftragsende hochgeladen. | `rw_mount`, `upload` | `rw_mount` |

### <a name="distribution-configurations"></a>Verteilungskonfigurationen

#### <a name="mpiconfiguration"></a>MpiConfiguration

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte |
| --- | ---- | ----------- | -------------- |
| `type` | const | **Erforderlich.** Verteilungstyp.  | `mpi` |
| `process_count_per_instance` | integer | **Erforderlich.** Die Anzahl der Prozesse pro Knoten, die für den Auftrag gestartet werden sollen.  | |

#### <a name="pytorchconfiguration"></a>PyTorchConfiguration

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte | Standardwert |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | const | **Erforderlich.** Verteilungstyp.  | `pytorch` | |
| `process_count_per_instance` | integer | Die Anzahl der Prozesse pro Knoten, die für den Auftrag gestartet werden sollen. | |  `1` |

#### <a name="tensorflowconfiguration"></a>TensorFlowConfiguration

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte | Standardwert |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | const | **Erforderlich.** Verteilungstyp.  | `tensorflow` |
| `worker_count` | integer | Die Anzahl von Arbeitsmitarbeitern, die für den Auftrag gestartet werden. | | Wird standardmäßig auf `resources.instance_count` festgelegt. |
| `parameter_server_count` | integer | Die Anzahl von Parameterservern, die für den Auftrag gestartet werden. | | `0` |

### <a name="job-inputs"></a>Auftragseingaben

#### <a name="jobinputuri"></a>JobInputUri

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte | Standardwert |
| --- | ---- | ----------- | -------------- | ------------- |
| `file` | Zeichenfolge | URI zu einer einzelnen Datei, die als Eingabe verwendet werden soll. Unterstützte URI-Typen sind `azureml`, `https`, `wasbs`, `abfss` und `adl`. Weitere Informationen zur Verwendung des`azureml://` URI-Formats finden Sie unter [Yaml-Kernsyntax](reference-yaml-core-syntax.md). **Eines von `file` oder `folder` ist erforderlich.**  | | |
| `folder` | Zeichenfolge | URI zu einem Ordner, der als Eingabe verwendet werden soll. Unterstützte URI-Typen sind `azureml`, `wasbs`, `abfss` und `adl`. Weitere Informationen zur Verwendung des`azureml://` URI-Formats finden Sie unter [Yaml-Kernsyntax](reference-yaml-core-syntax.md). **Eines von `file` oder `folder` ist erforderlich.**   | | |
| `mode` | Zeichenfolge | Modus, in dem die Daten an das Computeziel übermittelt werden sollen. Für die schreibgeschützte Bereitstellung und die Lese-/Schreib-Bereitstellung werden die Daten als Bereitstellungspfad verwendet. Ein Ordner wird als Ordner eingebunden, und eine Datei wird als Datei eingebunden. Im Download-Modus werden die Daten als heruntergeladener Pfad verwendet. | `ro_mount`, `rw_mount`, `download` | `ro_mount` |

#### <a name="jobinputdataset"></a>JobInputDataset

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte | Standardwert |
| --- | ---- | ----------- | -------------- | ------------- |
| `dataset` | Zeichenfolge oder Objekt | **Erforderlich.** Ein Dataset, das als Eingabe verwendet werden soll. Dies kann entweder ein Verweis auf ein vorhandenes Dataset mit Versionsangabe im Arbeitsbereich oder eine Inline-Datasetspezifikation sein. <br><br> Verwenden Sie die Syntax `azureml:<dataset_name>:<dataset_version>`, um auf ein vorhandenes Dataset zu verweisen. <br><br> Um ein Dataset inline zu definieren, befolgen Sie das [Datasetschema](reference-yaml-dataset.md#yaml-syntax). Schließen Sie die Eigenschaften `name` und `version` aus, da sie für Inline-Datasets nicht unterstützt werden. | | |
| `mode` | Zeichenfolge | Modus, in dem das Dataset an das Computeziel übermittelt werden sollen. Für die schreibgeschützte Bereitstellung wird das Dataset als Bereitstellungspfad verwendet. Ein Ordner wird als Ordner eingebunden, und eine Datei wird als übergeordnete Datei eingebunden. Im Download-Modus wird das Dataset als heruntergeladener Pfad verwendet. | `ro_mount`, `download` | `ro_mount` |

## <a name="remarks"></a>Hinweise

Mit dem Befehl `az ml job` können Sie Azure Machine Learning-Aufträge verwalten.

## <a name="examples"></a>Beispiele

Beispiele finden Sie im [GitHub-Repository für Beispiele](https://github.com/Azure/azureml-examples/tree/main/cli/jobs). Im Folgenden sind mehrere aufgeführt.

## <a name="yaml-hello-world"></a>YAML: Hallo Welt

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world.yml":::

## <a name="yaml-display-name-experiment-name-description-and-tags"></a>YAML: Anzeigename, Experimentname, Beschreibung und Tags

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-org.yml":::

## <a name="yaml-environment-variables"></a>YAML: Umgebungsvariablen

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-env-var.yml":::

## <a name="yaml-source-code"></a>YAML: Quellcode

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-code.yml":::

## <a name="yaml-literal-inputs"></a>YAML: Literaleingaben

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-input.yml":::

## <a name="yaml-write-to-default-outputs"></a>YAML: Schreiben in Standardausgabe

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-output.yml":::

## <a name="yaml-write-to-named-data-output"></a>YAML: Schreiben in benannte Datenausgabe

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-output-data.yml":::

## <a name="yaml-datastore-uri-file-input"></a>YAML: Eingabe der Datenspeicher-URI-Datei

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-iris-datastore-file.yml":::

## <a name="yaml-datastore-uri-folder-input"></a>YAML: Eingabe des Datenspeicher-URI-Ordners

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-iris-datastore-folder.yml":::

## <a name="yaml-uri-file-input"></a>YAML: URI-Dateieingabe

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-iris-file.yml":::

## <a name="yaml-uri-folder-input"></a>YAML: URI-Ordnereingabe

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-iris-folder.yml":::

## <a name="yaml-notebook-via-papermill"></a>YAML: Notebook über Papermill

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-notebook.yml":::

## <a name="yaml-basic-python-model-training"></a>YAML: Grundlegendes Python-Modelltraining

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/single-step/scikit-learn/iris/job.yml":::

## <a name="yaml-basic-r-model-training-with-local-docker-build-context"></a>YAML: Grundlegendes R-Modelltraining mit lokalem Docker-Buildkontext

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/single-step/r/iris/job.yml":::

## <a name="yaml-distributed-pytorch"></a>YAML: verteiltes PyTorch

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/single-step/pytorch/cifar-distributed/job.yml":::

## <a name="yaml-distributed-tensorflow"></a>YAML: verteilter TensorFlow

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/single-step/tensorflow/mnist-distributed/job.yml":::

## <a name="yaml-distributed-mpi"></a>YAML: verteiltes MPI

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/single-step/tensorflow/mnist-distributed-horovod/job.yml":::

## <a name="next-steps"></a>Nächste Schritte

- [Installieren und Verwenden der CLI (V2)](how-to-configure-cli.md)
