---
title: CLI (v2) Batch-Bereitstellung YAML-Schema
titleSuffix: Azure Machine Learning
description: Referenzdokumentation für das CLI (v2) Batch Bereitstellung YAML-Schema.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: tracychms
ms.author: tracych
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 53e83f76e61038bc1fb95bfba653b18f63febede
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132054503"
---
# <a name="cli-v2-batch-deployment-yaml-schema"></a>CLI (v2) Batch-Bereitstellung YAML-Schema

Das JSON-Quellschema finden Sie unter https://azuremlschemas.azureedge.net/latest/batchDeployment.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML-Syntax

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte | Standardwert |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | Zeichenfolge | Das YAML-Schema. Wenn Sie die Azure Machine Learning VS Code-Erweiterung verwenden, um die YAML-Datei zu erstellen, können Sie durch Einfügen von `$schema` am Anfang der Datei Schema- und Ressourcenvervollständigungen aufrufen. | | |
| `name` | Zeichenfolge | **Erforderlich.** Name der Bereitstellung | | |
| `description` | Zeichenfolge | Beschreibung des Bereitstellung. | | |
| `tags` | Objekt (object) | Wörterbuch der Tags für den Bereitstellung. | | |
| `endpoint_name` | Zeichenfolge | **Erforderlich.** Name des Endpunkts, unter dem die Bereitstellung erstellt werden soll. | | |
| `model` | Zeichenfolge oder Objekt | **Erforderlich.** Das für die Bereitstellung zu verwendende Modell. Dieser Wert kann entweder ein Verweis auf ein vorhandenes versioniertes Modell im Arbeitsbereich oder eine Inline-Modellspezifikation sein. <br><br> Um auf ein bestehendes Modell zu verweisen, verwenden Sie die Syntax `azureml:<model-name>:<model-version>`. <br><br> Um ein Modell inline zu definieren, folgen Sie dem [Modellschema](reference-yaml-model.md#yaml-syntax). <br><br> Als bewährtes Verfahren für Produktionsszenarien sollten Sie das Modell separat erstellen und hier referenzieren. | | |
| `code_configuration` | Objekt (object) | Konfiguration für die Logik des Scoring-Codes. <br><br> Diese Eigenschaft ist nicht erforderlich, wenn Ihr Modell im MLflow-Format vorliegt. | | |
| `code_configuration.code.local_path` | Zeichenfolge | Lokaler Pfad zum Quellcodeverzeichnis für das Scoring des Modells. | | |
| `code_configuration.scoring_script` | Zeichenfolge | Relativer Pfad zur Scoring-Datei im Quellcode-Verzeichnis. | | |
| `environment` | Zeichenfolge oder Objekt | Die für die Bereitstellung zu verwendende Umgebung. Dieser Wert kann entweder ein Verweis auf eine vorhandene versionierte Umgebung im Arbeitsbereich oder eine Inline-Umgebungsspezifikation sein. <br><br> Diese Eigenschaft ist nicht erforderlich, wenn Ihr Modell im MLflow-Format vorliegt. <br><br> Um auf eine bestehende Umgebung zu verweisen, verwenden Sie die Syntax `azureml:<environment-name>:<environment-version>`. <br><br> Um eine Umgebung inline zu definieren, folgen Sie dem [Umgebungsschema](reference-yaml-environment.md#yaml-syntax). <br><br> Als bewährtes Verfahren für Produktionsszenarien sollten Sie die Umgebung separat erstellen und hier referenzieren. | | |
| `compute` | Zeichenfolge | **Erforderlich.** Name des Rechenziels, auf dem die Batch-Scoring-Aufträge ausgeführt werden sollen. Dieser Wert sollte ein Verweis auf eine bestehende Berechnung im Arbeitsbereich unter Verwendung der `azureml:<compute-name>`-Syntax sein. | | |
| `resources.instance_count` | integer | Die Anzahl der Knoten, die für jeden Batch-Scoring-Auftrag verwendet werden sollen. | | `1` |
| `max_concurrency_per_instance` | integer | Die maximale Anzahl von parallelen `scoring_script` Läufen pro Instanz. | | `1` |
| `error_threshold` | integer | Die Anzahl der Dateifehler, die ignoriert werden sollen. Wenn die Fehlerzahl für die gesamte Eingabe diesen Wert übersteigt, wird der Batch-Scoring-Auftrag abgebrochen. `error_threshold` bezieht sich auf den gesamten Input und nicht auf einzelne Mini-Batches. Wird dieser Parameter nicht angegeben, können beliebig viele Dateifehler auftreten, ohne dass der Auftrag abgebrochen wird.  | | `-1` |
| `logging_level` | Zeichenfolge | Die Ausführlichkeitsstufe des Protokolls. | `warning`, `info`, `debug` | `info` |
| `mini_batch_size` | integer | Die Anzahl der Dateien, die `code_configuration.scoring_script` in einem `run()`-Aufruf verarbeiten kann. | | `10` |
| `retry_settings` | Objekt (object) | Wiederholungseinstellungen für die Bewertung jeder Mini-Charge. | | |
| `retry_settings.max_retries` | integer | Die maximale Anzahl der Wiederholungsversuche für einen fehlgeschlagenen oder zeitlich begrenzten Mini-Batch. | | `3` |
| `retry_settings.timeout` | integer | Die Zeitüberschreitung in Sekunden für das Erfassen eines Mini-Batches. | | `30` |
| `output_action` | Zeichenfolge | Gibt an, wie die Ausgabe in der Ausgabedatei organisiert werden soll. | `append_row`, `summary_only` | `append_row` |
| `output_file_name` | Zeichenfolge | Name der Ausgabedatei für die Stapelauswertung. | | `predictions.csv` |
| `environment_variables` | Objekt (object) | Wörterbuch mit Namen-Wert-Paaren von Umgebungsvariablen, die für jeden Batch-Scoring-Auftrag festzulegen sind. | | |

## <a name="remarks"></a>Hinweise

Die `az ml batch-deployment`-Befehle können für die Verwaltung von Azure Machine Learning-Batch-Bereitstellungen verwendet werden.

## <a name="examples"></a>Beispiele

Beispiele sind im [Beispiele GitHub-Repository](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/batch) verfügbar. Einige davon sind unten aufgeführt.

## <a name="yaml-basic-mlflow"></a>YAML: grundlegend (MLflow)

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/batch/mlflow-deployment.yml":::

## <a name="yaml-custom-model-and-scoring-code"></a>YAML: Benutzerdefiniertes Modell und Scoring-Code

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/batch/nonmlflow-deployment.yml":::

## <a name="next-steps"></a>Nächste Schritte

- [Installieren und Verwenden der CLI (V2)](how-to-configure-cli.md)
