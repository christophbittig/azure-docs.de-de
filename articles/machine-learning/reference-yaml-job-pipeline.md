---
title: YAML-Schema für einen CLI-Pipelineauftrag (v2)
titleSuffix: Azure Machine Learning
description: Referenzdokumentation für das YAML-Schema für einen CLI-Pipelineauftrag (v2)
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 93c8f11fbfab8386e1083985399d8138e2b2b7d6
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132057079"
---
# <a name="cli-v2-pipeline-job-yaml-schema"></a>YAML-Schema für einen CLI-Pipelineauftrag (v2)

Das JSON-Quellschema finden Sie unter https://azuremlschemas.azureedge.net/latest/pipelineJob.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML-Syntax

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte | Standardwert |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | Zeichenfolge | Das YAML-Schema Wenn Sie die VS Code-Erweiterung für Azure Machine Learning zum Erstellen einer YAML-Datei verwenden, können Sie durch das Einfügen von `$schema` am Anfang der Datei Schema- und Ressourcenvervollständigungen aufrufen. | | |
| `type` | const | **Erforderlich.** Typ des Auftrags. | `pipeline` | |
| `name` | Zeichenfolge | Der Name des Auftrags. Muss für alle Aufträge im Arbeitsbereich eindeutig sein. Wenn dieser nicht angegeben wird, generiert Azure ML automatisch eine GUID für den Namen. | | |
| `display_name` | Zeichenfolge | Der Anzeigename des Auftrags auf der Studio-Benutzeroberfläche. Kann innerhalb des Arbeitsbereichs nicht eindeutig sein. Wenn er nicht angegeben wird, generiert Azure ML automatisch einen für Menschen lesbaren Bezeichner für den Anzeigenamen. | | |
| `experiment_name` | Zeichenfolge | Der Experimentname zum Organisieren des Auftrags. Die Ausführungsaufzeichnung jedes Auftrags wird auf der Registerkarte „Experimente“ des Studios unter dem entsprechenden Experiment organisiert. Wenn dieser nicht angegeben wird, wird von Azure ML standardmäßig der Namen des Arbeitsverzeichnisses verwendet, in dem der Auftrag erstellt wurde. | | |
| `description` | Zeichenfolge | Die Beschreibung des Auftrags | | |
| `tags` | Objekt (object) | Das Wörterbuch der Tags für den Auftrag | | |
| `settings` | Objekt (object) | Die Standardeinstellungen für den Pipelineauftrag. Informationen zu den konfigurierbaren Eigenschaften finden Sie unter [Attribute des `settings`-Schlüssels](#attributes-of-the-settings-key). | | |
| `jobs` | Objekt (object) | **Erforderlich.** Das Wörterbuch der einzelnen Aufträge, die als Schritte innerhalb der Pipeline ausgeführt werden sollen. Diese Aufträge werden als untergeordnete Aufträge des übergeordneten Pipelineauftrags betrachtet. <br><br> Der Schlüssel ist der Name des Schritts im Kontext des Pipelineauftrags. Dieser Name unterscheidet sich vom eindeutigen Auftragsnamen des untergeordneten Auftrags. Der Wert ist die Auftragsspezifikation, die dem [Befehlsauftragsschema](reference-yaml-job-command.md#yaml-syntax) oder dem [Komponentenauftragsschema](reference-yaml-job-component.md#yaml-syntax) folgen kann. Derzeit können nur Befehlsaufträge und Komponentenaufträge, die Befehlskomponenten ausführen, in einer Pipeline ausgeführt werden. In späteren Releases werden andere Auftragstypen unterstützt. | | |
| `inputs` | Objekt (object) | Das Wörterbuch der Eingaben für den Pipelineauftrag. Der Schlüssel ist ein Name für die Eingabe im Kontext des Auftrags, und der Wert ist der Eingabewert. <br><br> Auf diese Pipelineeingaben kann mithilfe des Ausdrucks `${{ inputs.<input_name> }}` durch die Eingaben eines einzelnen Schrittauftrags in der Pipeline verwiesen werden. | | |
| `inputs.<input_name>` | number, integer, boolean, string oder object | Eine Eingabe eines Literalwerts (vom Typ number, integer, boolean oder string), [JobInputUri](#jobinputuri) oder [JobInputDataset](#jobinputdataset). | | |
| `outputs` | Objekt (object) | Das Wörterbuch der Ausgabekonfigurationen des Pipelineauftrags. Der Schlüssel ist de Name für die Ausgabe im Kontext des Auftrags, und der Wert ist die Ausgabekonfiguration. <br><br> Auf diese Pipelineausgaben kann mithilfe des Ausdrucks `${{ outputs.<output_name> }}` von den Ausgaben eines einzelnen Schrittauftrags in der Pipeline verwiesen werden. | |
| `outputs.<output_name>` | Objekt (object) | Sie können entweder einen optionalen `mode` angeben oder das Objekt leer lassen. Für jede im `outputs`-Wörterbuch angegebene benannte Ausgabe generiert Azure ML automatisch einen Ausgabespeicherort basierend auf dem folgenden vorlagenbasierten Pfad: `{default-datastore}/azureml/{job-name}/{output-name}/`. Benutzer*innen können in einem späteren Release einen benutzerdefinierten Speicherort angeben. | |
| `outputs.<output_name>.mode` | Zeichenfolge | Der Modus, in dem Ausgabedateien an den Zielspeicher übermittelt werden. Für den Lese-/Schreib-Einbindungsmodus ist das Ausgabeverzeichnis ein eingebundenes Verzeichnis. Im Uploadmodus werden die in das Ausgabeverzeichnis geschriebenen Dateien am Ende des Auftrags hochgeladen. | `rw_mount`, `upload` | `rw_mount` |
| `compute` | Zeichenfolge | Der Name des Standardcomputeziels, für das ein Schrittauftrag ausgeführt werden soll, wenn für den einzelnen Schritt kein Computeziel angegeben ist. Dieser Wert muss ein Verweis auf ein vorhandenes Computeziel im Arbeitsbereich sein, indem die Syntax `azureml:<compute-name>` verwendet wird. |

### <a name="attributes-of-the-settings-key"></a>Attribute des `settings`-Schlüssels

| Schlüssel | type | BESCHREIBUNG |
| --- | ---- | ----------- |
| `datastore` | Zeichenfolge | Der Name des Datenspeichers, der als Standarddatenspeicher für den Pipelineauftrag verwendet werden soll. Dieser Wert muss ein Verweis auf einen vorhandenen Datenspeicher im Arbeitsbereich sein, der die Syntax `azureml:<datastore-name>` verwendet. Alle Ausgaben, die in der Eigenschaft `outputs` des übergeordneten Pipelineauftrags oder der untergeordneten Schrittaufträge definiert sind, werden in diesem Datenspeicher gespeichert. Wenn dieser nicht angegeben wird, werden Ausgaben im Blobdatenspeicher des Arbeitsbereichs gespeichert. |

### <a name="job-inputs"></a>Auftragseingaben

#### <a name="jobinputuri"></a>JobInputUri

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte | Standardwert |
| --- | ---- | ----------- | -------------- | ------------- |
| `file` | Zeichenfolge | Der URI zu einer einzelnen Datei, die als Eingabe verwendet werden soll. Unterstützte URI-Typen sind `azureml`, `https`, `wasbs`, `abfss`, `adl`. Weitere Informationen zum Verwenden des URI-Formats `azureml` finden Sie unter [YAML-Kernsyntax](reference-yaml-core-syntax.md). **Entweder ist `file` oder `folder` erforderlich.**  | | |
| `folder` | Zeichenfolge | Der URI zu einem Ordner, der als Eingabe verwendet werden soll. Unterstützte URI-Typen sind `azureml`, `wasbs`, `abfss`, `adl`. Weitere Informationen zum Verwenden des URI-Formats `azureml` finden Sie unter [YAML-Kernsyntax](reference-yaml-core-syntax.md). **Entweder ist `file` oder `folder` erforderlich.**   | | |
| `mode` | Zeichenfolge | Der Modus, in dem die Daten an das Computeziel übermittelt werden sollen. Für das schreibgeschützte Einbinden und die Lese-/Schreib-Einbindung werden die Daten als Einbindungspfad verwendet. Ein Ordner wird als Ordner eingebunden, und eine Datei wird als Datei eingebunden. Im Downloadmodus werden die Daten als heruntergeladener Pfad verwendet. | `ro_mount`, `rw_mount`, `download` | `ro_mount` |

#### <a name="jobinputdataset"></a>JobInputDataset

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte | Standardwert |
| --- | ---- | ----------- | -------------- | ------------- |
| `dataset` | Zeichenfolge oder Objekt | **Erforderlich.** Ein Dataset, das als Eingabe verwendet werden soll. Dieser Wert kann entweder ein Verweis auf ein vorhandenes Dataset mit Versionsangabe im Arbeitsbereich oder eine Inlinedatasetspezifikation sein. <br><br> Um auf ein vorhandenes Dataset zu verweisen, verwenden Sie die Syntax `azureml:<dataset-name>:<dataset-version>`. <br><br> Um ein Dataset inline zu definieren, befolgen Sie das [Datasetschema](reference-yaml-dataset.md#yaml-syntax). Schließen Sie die Eigenschaften `name` und `version` aus, da sie für Inlinedatasets nicht unterstützt werden. | | |
| `mode` | Zeichenfolge | Der Modus, in dem das Dataset an das Computeziel übermittelt werden soll. Bei einem schreibgeschützten Einbinden wird das Dataset als Einbindungspfad verwendet. Ein Ordner wird als Ordner eingebunden, und eine Datei wird als übergeordneter Ordner eingebunden. Im Downloadmodus wird das Dataset als heruntergeladener Pfad verwendet. | `ro_mount`, `download` | `ro_mount` |

## <a name="remarks"></a>Hinweise

Die `az ml job`-Befehle können zum Verwalten von Azure Machine Learning-Pipelineaufträgen verwendet werden.

## <a name="examples"></a>Beispiele

Beispiele finden Sie im [GitHub-Repository für Beispiele](https://github.com/Azure/azureml-examples/tree/main/cli/jobs). Im Folgenden sind mehrere aufgeführt.

## <a name="yaml-hello-pipeline"></a>YAML: Hallo Pipeline

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-pipeline.yml":::

## <a name="yaml-inputoutput-dependency"></a>YAML: Eingabe-/Ausgabeabhängigkeit

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-pipeline-io.yml":::

## <a name="yaml-common-pipeline-job-settings"></a>YAML: Allgemein Einstellungen für Pipelineaufträge

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-pipeline-settings.yml":::

## <a name="yaml-top-level-input-and-overriding-common-pipeline-job-settings"></a>YAML: Eingabe auf oberster Ebene und Überschreiben allgemeiner Einstellungen für Pipelineaufträge

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-pipeline-abc.yml":::

## <a name="yaml-model-training-pipeline"></a>YAML: Modelltrainingspipeline

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/pipelines/cifar-10/job.yml":::

## <a name="next-steps"></a>Nächste Schritte

- [Installieren und Verwenden der CLI (V2)](how-to-configure-cli.md)
