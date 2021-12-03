---
title: YAML-Schema für Sweep-Aufträge in der Befehlszeilenschnittstelle (v2)
titleSuffix: Azure Machine Learning
description: Referenzdokumentation für das YAML-Schema von Sweep-Aufträgen an der Befehlszeilenschnittstelle (v2)
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: reference
author: mx-iao
ms.author: minxia
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 90a69fceb96067ad92cc4d68f8b3e0929d2622a6
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132135119"
---
# <a name="cli-v2-sweep-job-yaml-schema"></a>YAML-Schema für Sweep-Aufträge in der Befehlszeilenschnittstelle (v2)

Das JSON-Quellschema finden Sie unter https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML-Syntax

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte | Standardwert |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | Zeichenfolge | Das YAML-Schema Wenn Sie die VS Code-Erweiterung für Azure Machine Learning zum Erstellen einer YAML-Datei verwenden, können Sie durch das Einfügen von `$schema` am Anfang der Datei Schema- und Ressourcenvervollständigungen aufrufen. | | |
| `type` | const | **Erforderlich.** Typ des Auftrags. | `sweep` | `sweep` |
| `name` | Zeichenfolge | Der Name des Auftrags. Muss für alle Aufträge im Arbeitsbereich eindeutig sein. Wenn dieser nicht angegeben wird, generiert Azure ML automatisch eine GUID für den Namen. | | |
| `display_name` | Zeichenfolge | Der Anzeigename des Auftrags auf der Studio-Benutzeroberfläche. Kann innerhalb des Arbeitsbereichs nicht eindeutig sein. Wenn er nicht angegeben wird, generiert Azure ML automatisch einen für Menschen lesbaren Bezeichner für den Anzeigenamen. | | |
| `experiment_name` | Zeichenfolge | Der Experimentname zum Organisieren des Auftrags. Die Ausführungsaufzeichnung jedes Auftrags wird auf der Registerkarte „Experimente“ des Studios unter dem entsprechenden Experiment organisiert. Wenn dieser nicht angegeben wird, wird von Azure ML standardmäßig der Namen des Arbeitsverzeichnisses verwendet, in dem der Auftrag erstellt wurde. | | |
| `description` | Zeichenfolge | Die Beschreibung des Auftrags | | |
| `tags` | Objekt (object) | Wörterbuch der Tags für den Auftrag. | | |
| `sampling_algorithm` | Zeichenfolge | **Erforderlich.** Der Hyperparameter-Samplingalgorithmus, der über `search_space` verwendet werden soll. | `random`, `grid`, `bayesian` | |
| `search_space` | Objekt (object) | **Erforderlich.** Wörterbuch des Suchbereichs für Hyperparameter. Der Schlüssel ist der Name des Hyperparameters, und der Wert ist der Parameterausdruck. <br><br> Auf Hyperparameter kann im `trial.command` mit dem Ausdruck `${{ search_space.<hyperparameter> }}` verwiesen werden. | | |
| `search_space.<hyperparameter>` | Objekt (object) | Weitere Informationen zu den möglichen Ausdrücken finden Sie unter [Parameterausdrücke](#parameter-expressions). | | |
| `objective.primary_metric` | Zeichenfolge | **Erforderlich.** Der Name der primären Metrik, die von jedem Testauftrag gemeldet wird. Die Metrik muss im Trainingsskript des Benutzers mithilfe von `mlflow.log_metric()` mit dem gleichen entsprechenden Metriknamen protokolliert werden. | | |
| `objective.goal` | Zeichenfolge | **Erforderlich.** Das Optimierungsziel von `objective.primary_metric`. | `maximize`, `minimize` | |
| `early_termination` | Objekt (object) | Die zu verwendende Richtlinie für vorzeitige Beendigung. Ein Testauftrag wird abgebrochen, wenn die Kriterien der angegebenen Richtlinie erfüllt sind. Wenn diese Einstellung nicht angegeben wird, erfolgt keine Anwendung der Richtlinie für die vorzeitige Beendigung. Eine von [BanditPolicy](#banditpolicy), [MedianStoppingPolicy](#medianstoppingpolicy) oder [TruncationSelectionPolicy](#truncationselectionpolicy). | | |
| `limits` | Objekt (object) | Grenzwerte für den Sweep-Auftrag. Siehe [Attribute des `limits`-Schlüssels](#attributes-of-the-limits-key). | | |
| `compute` | Zeichenfolge | **Erforderlich.** Name des Computeziels, auf dem der Auftrag mithilfe der `azureml:<compute_name>`-Syntax ausgeführt werden soll. | | |
| `trial` | Objekt (object) | **Erforderlich.** Die Auftragsvorlage für die einzelnen Testversionen. Jeder Testauftrag wird mit einer anderen Kombination von Hyperparameterwerten bereitgestellt, die das System aus dem `search_space` als Stichproben entnimmt. Siehe [Attribute des `trial`-Schlüssels](#attributes-of-the-trial-key). | | |
| `inputs` | Objekt (object) | Wörterbuch der Eingaben für den Auftrag. Der Schlüssel ist ein Name für die Eingabe im Kontext des Auftrags, und der Wert ist der Eingabewert. <br><br> Eingaben können im `trial.command` mit dem Ausdruck `${{ inputs.<input_name> }}` verwiesen werden. | | |
| `inputs.<input_name>` | number, integer, boolean, string oder object | Einer eines Literalwerts (vom Typ number, integer, boolean oder string), [JobInputUri](#jobinputuri) oder [JobInputDataset](#jobinputdataset). | | |
| `outputs` | Objekt (object) | Wörterbuch der Ausgabekonfigurationen des Auftrags. Der Schlüssel ist ein Name für die Ausgabe im Kontext des Auftrags, und der Wert ist die Ausgabekonfiguration. <br><br> Ausgaben können im `trial.command` mit dem Ausdruck `${{ outputs.<output_name> }}` verwiesen werden. | |
| `outputs.<output_name>` | Objekt (object) | Sie können entweder einen optionalen `mode` angeben oder das Objekt leer lassen. Für jede im Wörterbuch (`outputs`) angegebene benannte Ausgabe generiert Azure ML automatisch eine standortbasierte Ausgabe. | |
| `outputs.<output_name>.mode` | Zeichenfolge | Modus, wie Ausgabedateien an den Zielspeicher übermittelt werden. Für den Lese-/Schreib-Bereitstellungsmodus ist das Ausgabeverzeichnis ein bereitgestelltes Verzeichnis. Im Upload-Modus werden Dateien, die in das Ausgabeverzeichnis geschrieben werden, zum Auftragsende hochgeladen. | `rw_mount`, `upload` | `rw_mount` |

### <a name="early-termination-policies"></a>Richtlinien zum vorzeitigen Beenden

#### <a name="banditpolicy"></a>BanditPolicy

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte | Standardwert |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | const | **Erforderlich.** Der Typ der Richtlinie. | `bandit` | |
| `slack_factor` | number | Das Verhältnis, das zur Berechnung des zulässigen Abstands zur besten Testversion verwendet wird. **Eines von `slack_factor` oder `slack_amount` ist erforderlich.** | | |
| `slack_amount` | number | Der absolute Abstand, der von der Testversion mit der besten Leistung zulässig ist. **Eines von `slack_factor` oder `slack_amount` ist erforderlich.** | | |
| `evaluation_interval` | integer | Die Anwendungshäufigkeit der Richtlinie. | | `1` |
| `delay_evaluation` | integer | Die Anzahl der Intervalle, für die die erste Richtlinienauswertung verzögert werden soll. Sofern angegeben, gilt die Richtlinie für jedes Vielfache von `evaluation_interval`, das größer oder gleich `delay_evaluation` ist. | | `0` |

#### <a name="medianstoppingpolicy"></a>MedianStoppingPolicy

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte | Standardwert |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | const | **Erforderlich.** Der Typ der Richtlinie. | `median_stopping` | |
| `evaluation_interval` | integer | Die Anwendungshäufigkeit der Richtlinie. | | `1` |
| `delay_evaluation` | integer | Die Anzahl der Intervalle, für die die erste Richtlinienauswertung verzögert werden soll. Sofern angegeben, gilt die Richtlinie für jedes Vielfache von `evaluation_interval`, das größer oder gleich `delay_evaluation` ist. | | `0` |

#### <a name="truncationselectionpolicy"></a>TruncationSelectionPolicy

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte | Standardwert |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | const | **Erforderlich.** Der Typ der Richtlinie. | `truncation_selection` | |
| `truncation_percentage` | integer | **Erforderlich.** Der Prozentsatz der Testaufträge, die bei jedem Auswertungsintervall abgebrochen werden sollen. | | |
| `evaluation_interval` | integer | Die Anwendungshäufigkeit der Richtlinie. | | `1` |
| `delay_evaluation` | integer | Die Anzahl der Intervalle, für die die erste Richtlinienauswertung verzögert werden soll. Sofern angegeben, gilt die Richtlinie für jedes Vielfache von `evaluation_interval`, das größer oder gleich `delay_evaluation` ist. | | `0` |

### <a name="parameter-expressions"></a>Parameterausdrücke

#### <a name="choice"></a>choice

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte |
| --- | ---- | ----------- | -------------- |
| `type` | const | **Erforderlich.** Der Typ des Ausdrucks. | `choice` |
| `values` | array | **Erforderlich.** Die Liste der zur Auswahl stehenden diskreten Werte. | |

#### <a name="randint"></a>randint

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte |
| --- | ---- | ----------- | -------------- |
| `type` | const | **Erforderlich.** Der Typ des Ausdrucks. | `randint` |
| `upper` | integer | **Erforderlich.** Die exklusive Obergrenze für den Bereich ganzer Zahlen. | |

#### <a name="qlognormal-qnormal"></a>qlognormal, qnormal

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte |
| --- | ---- | ----------- | -------------- |
| `type` | const | **Erforderlich.** Der Typ des Ausdrucks. | `qlognormal`, `qnormal` |
| `mu` | number | **Erforderlich.** Der Mittelwert der Normalverteilung. | |
| `sigma` | number | **Erforderlich.** Die Standardabweichung der Normalverteilung. | |
| `q` | integer | **Erforderlich.** Der Glättungsfaktor. | |

#### <a name="qloguniform-quniform"></a>qloguniform, quniform

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte |
| --- | ---- | ----------- | -------------- |
| `type` | const | **Erforderlich.** Der Typ des Ausdrucks. | `qloguniform`, `quniform` |
| `min_value` | number | **Erforderlich.** Der Mindestwert im Bereich (einschließlich). | |
| `max_value` | number | **Erforderlich.** Der Höchstwert im Bereich (einschließlich). | |
| `q` | integer | **Erforderlich.** Der Glättungsfaktor. | |

#### <a name="lognormal-normal"></a>lognormal, normal

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte |
| --- | ---- | ----------- | -------------- |
| `type` | const | **Erforderlich.** Der Typ des Ausdrucks. | `lognormal`, `normal` |
| `mu` | number | **Erforderlich.** Der Mittelwert der Normalverteilung. | |
| `sigma` | number | **Erforderlich.** Die Standardabweichung der Normalverteilung. | |

#### <a name="loguniform"></a>loguniform

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte |
| --- | ---- | ----------- | -------------- |
| `type` | const | **Erforderlich.** Der Typ des Ausdrucks. | `loguniform` |
| `min_value` | number | **Erforderlich.** Der Mindestwert im Bereich ist `exp(min_value)` (einschließlich). | |
| `max_value` | number | **Erforderlich.** Der Höchstwert im Bereich ist `exp(max_value)` (einschließlich). | |

#### <a name="uniform"></a>uniform-Variable

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte |
| --- | ---- | ----------- | -------------- |
| `type` | const | **Erforderlich.** Der Typ des Ausdrucks. | `uniform` |
| `min_value` | number | **Erforderlich.** Der Mindestwert im Bereich (einschließlich). | |
| `max_value` | number | **Erforderlich.** Der Höchstwert im Bereich (einschließlich). | |

### <a name="attributes-of-the-limits-key"></a>Attribute des `limits`-Schlüssels

| Schlüssel | type | Beschreibung | Standardwert |
| --- | ---- | ----------- | ------------- |
| `max_total_trials` | integer | Die maximale Zeit in Sekunden, die der Auftrag ausgeführt werden darf. Sobald dieser Grenzwert erreicht ist, bricht das System den Auftrag ab. | `1000` |
| `max_concurrent_trials` | integer | | Wird standardmäßig auf `max_total_trials` festgelegt. |
| `timeout` | integer | Die maximale Zeit in Minuten, die der gesamte Sweep-Auftrag ausgeführt werden darf. Sobald dieser Grenzwert erreicht ist, bricht das System den Sweep-Auftrag ab, einschließlich aller Testversionen. | `10080` |
| `trial_timeout` | integer | Die maximale Zeit in Sekunden, die jeder Testauftrag ausgeführt werden darf. Sobald dieser Grenzwert erreicht ist, bricht das System die Testversion ab. | |

### <a name="attributes-of-the-trial-key"></a>Attribute des `trial`-Schlüssels

| Schlüssel | type | Beschreibung | Standardwert |
| --- | ---- | ----------- | ------------- |
| `command` | Zeichenfolge | **Erforderlich.** Den auszuführenden Befehl. | |
| `code.local_path` | Zeichenfolge | Lokaler Pfad zum Quellcodeverzeichnis, das hochgeladen und für den Auftrag verwendet werden soll. | |
| `environment` | Zeichenfolge oder Objekt | **Erforderlich.** Die Umgebung, die für den Auftrag verwendet werden soll. Dies kann entweder ein Verweis auf eine vorhandene Umgebung mit Versionsangabe im Arbeitsbereich oder eine Inline-Umgebungsspezifikation sein. <br> <br> Verwenden Sie die Syntax `azureml:<environment-name>:<environment-version>`, um auf eine vorhandene Umgebung zu verweisen. <br><br> Um eine Umgebung inline zu definieren, befolgen Sie das [Umgebungsschema](reference-yaml-environment.md#yaml-syntax). Schließen Sie die Eigenschaften `name` und `version` aus, da sie für Inlineumgebungen nicht unterstützt werden. | |
| `environment_variables` | Objekt (object) | Wörterbuch der Name-Wert-Paare von Umgebungsvariablen, die für den Prozess festgelegt werden, in dem der Befehl ausgeführt wird. | |
| `distribution` | Objekt (object) | Die Verteilungskonfiguration für verteilte Trainingsszenarien Eine von [MpiConfiguration](#mpiconfiguration), [PyTorchConfiguration](#pytorchconfiguration)oder [TensorFlowConfiguration](#tensorflowconfiguration). | |
| `resources.instance_count` | integer | Die Anzahl von Knoten, die für den Auftrag verwendet werden sollen. | `1` |

#### <a name="distribution-configurations"></a>Verteilungskonfigurationen

##### <a name="mpiconfiguration"></a>MpiConfiguration

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte |
| --- | ---- | ----------- | -------------- |
| `type` | const | **Erforderlich.** Verteilungstyp.  | `mpi` |
| `process_count_per_instance` | integer | **Erforderlich.** Die Anzahl der Prozesse pro Knoten, die für den Auftrag gestartet werden sollen  | |

##### <a name="pytorchconfiguration"></a>PyTorchConfiguration

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte | Standardwert |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | const | **Erforderlich.** Verteilungstyp.  | `pytorch` | |
| `process_count_per_instance` | integer | Die Anzahl der Prozesse pro Knoten, die für den Auftrag gestartet werden sollen | |  `1` |

##### <a name="tensorflowconfiguration"></a>TensorFlowConfiguration

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte | Standardwert |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | const | **Erforderlich.** Verteilungstyp.  | `tensorflow` |
| `worker_count` | integer | Die Anzahl von Workerrollen, die für den Auftrag gestartet werden. | | Wird standardmäßig auf `resources.instance_count` festgelegt. |
| `parameter_server_count` | integer | Die Anzahl von Parameterservern, die für den Auftrag gestartet werden. | | `0` |

### <a name="job-inputs"></a>Auftragseingaben

#### <a name="jobinputuri"></a>JobInputUri

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte | Standardwert |
| --- | ---- | ----------- | -------------- | ------------- |
| `file` | Zeichenfolge | Der URI zu einer einzelnen Datei, die als Eingabe verwendet werden soll. Unterstützte URI-Typen sind `azureml`, `https`, `wasbs`, `abfss` und `adl`. Weitere Informationen zur Verwendung des `azureml://`-URI-Formats finden Sie unter [YAML-Kernsyntax](reference-yaml-core-syntax.md). **Entweder `file` oder `folder` ist erforderlich.**  | | |
| `folder` | Zeichenfolge | Der URI zu einem Ordner, der als Eingabe verwendet werden soll. Unterstützte URI-Typen sind `azureml`, `wasbs`, `abfss` und `adl`. Weitere Informationen zur Verwendung des `azureml://`-URI-Formats finden Sie unter [YAML-Kernsyntax](reference-yaml-core-syntax.md). **Entweder `file` oder `folder` ist erforderlich.**   | | |
| `mode` | Zeichenfolge | Modus, in dem die Daten an das Computeziel übermittelt werden sollen. Für das schreibgeschützte Einbinden und die Lese-/Schreib-Einbindung werden die Daten als Einbindungspfad verwendet. Ein Ordner wird als Ordner eingebunden, und eine Datei wird als Datei eingebunden. Im Download-Modus werden die Daten als heruntergeladener Pfad verwendet. | `ro_mount`, `rw_mount`, `download` | `ro_mount` |

#### <a name="jobinputdataset"></a>JobInputDataset

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte | Standardwert |
| --- | ---- | ----------- | -------------- | ------------- |
| `dataset` | Zeichenfolge oder Objekt | **Erforderlich.** Ein Dataset, das als Eingabe verwendet werden soll. Dies kann entweder ein Verweis auf ein vorhandenes Dataset mit Versionsangabe im Arbeitsbereich oder eine Inline-Datasetspezifikation sein. <br><br> Verwenden Sie die Syntax `azureml:<dataset_name>:<dataset_version>`, um auf ein vorhandenes Dataset zu verweisen. <br><br> Um ein Dataset inline zu definieren, befolgen Sie das [Datasetschema](reference-yaml-dataset.md#yaml-syntax). Schließen Sie die Eigenschaften `name` und `version` aus, da sie für Inline-Datasets nicht unterstützt werden. | | |
| `mode` | Zeichenfolge | Modus, in dem das Dataset an das Computeziel übermittelt werden sollen. Für das schreibgeschützte Einbinden wird das Dataset als Einbindungspfad verwendet. Ein Ordner wird als Ordner eingebunden, und eine Datei wird als übergeordnete Datei eingebunden. Im Download-Modus wird das Dataset als heruntergeladener Pfad verwendet. | `ro_mount`, `download` | `ro_mount` |

## <a name="remarks"></a>Hinweise

Mit dem Befehl `az ml job` können Sie Azure Machine Learning-Aufträge verwalten.

## <a name="examples"></a>Beispiele

Beispiele finden Sie im [GitHub-Beispielrepository](https://github.com/Azure/azureml-examples/tree/main/cli/jobs). Im Folgenden sind mehrere aufgeführt.

## <a name="yaml-hello-sweep"></a>YAML: Hello-Sweep

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-sweep.yml":::

## <a name="yaml-basic-python-model-hyperparameter-tuning"></a>YAML: Grundlegende Optimierung von Python-Modellen mit Hyperparametern

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/single-step/scikit-learn/iris/job-sweep.yml":::

## <a name="next-steps"></a>Nächste Schritte

- [Installieren und Verwenden der CLI (V2)](how-to-configure-cli.md)
