---
title: YAML-Schema für einen CLI-Komponentenauftrag (v2)
titleSuffix: Azure Machine Learning
description: Referenzdokumentation für das YAML-Schema des CLI-Komponentenauftrags (v2)
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: mx-iao
ms.author: minxia
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: fc1f9580b44132b172c7f58a93d75943dea63efd
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566503"
---
# <a name="cli-v2-component-job-yaml-schema"></a>YAML-Schema für einen CLI-Komponentenauftrag (v2)

Das JSON-Quellschema finden Sie unter https://azuremlschemas.azureedge.net/latest/commandJob.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML-Syntax

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte | Standardwert |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | Zeichenfolge | Das YAML-Schema. Wenn Sie die VS Code-Erweiterung für Azure Machine Learning zum Erstellen einer YAML-Datei verwenden, können Sie durch das Einfügen von `$schema` am Anfang der Datei Schema- und Ressourcenvervollständigungen aufrufen. | | |
| `type` | const | Typ des Auftrags. | `component` | |
| `component` | Objekt | **Erforderlich.** Die Komponente, die aufgerufen und in einem Auftrag ausgeführt werden soll. Dieser Wert kann entweder ein Verweis auf eine vorhandene Komponente mit Versionsangabe im Arbeitsbereich, eine Inlinekomponentenspezifikation oder der lokale Pfad zu einer separaten YAML-Komponentenspezifikationsdatei sein. <br><br> Verwenden Sie die Syntax `azureml:<component-name>:<component-version>`, um auf eine vorhandene Komponente zu verweisen. <br><br> Um eine Komponente inline oder in einer separaten YAML-Datei zu definieren, befolgen Sie das [Befehlskomponentenschema](reference-yaml-component-command.md#yaml-syntax). Schließen Sie die Eigenschaften `name` und `version` aus, da sie nicht für Inlinekomponentenspezifikationen gelten. | | |
| `compute` | Zeichenfolge | Der Name des Computeziels, auf dem der Auftrag ausgeführt werden soll. Dieser Wert sollte ein Verweis auf einen vorhandenes Computeziel im Arbeitsbereich sein, indem die Syntax `azureml:<compute-name>` verwendet wird. Wenn dies nicht angegeben wird, verwendet Azure ML das Computeziel, das in der `compute`-Eigenschaft des Pipelineauftrags definiert ist. | | |
| `inputs` | Objekt | Wörterbuch der Eingaben für den Auftrag. Der Schlüssel entspricht dem Namen einer der Komponenteneingaben, und der Wert ist der Laufzeiteingabewert. <br><br> Auf Eingaben kann im `command` mithilfe des `${{ inputs.<input_name> }}`-Ausdrucks verwiesen werden. | | |
| `inputs.<input_name>` | number, integer, boolean, string oder object | Eine Eingabe eines Literalwerts (vom Typ number, integer, boolean oder string), [JobInputUri](#jobinputuri) oder [JobInputDataset](#jobinputdataset). Sie können über `jobs.<COMPONENT_NAME>.outputs.<OUTPUT_NAME>` auch auf Ausgaben eines anderen Auftrags in derselben Pipeline verweisen. | | |
| `outputs` | Objekt | Wörterbuch der Ausgabekonfigurationen des Auftrags. Der Schlüssel entspricht dem Namen, der dem Namen einer der Komponentenausgaben entspricht, und der Wert ist die Laufzeitausgabekonfiguration. <br><br> Auf Ausgaben kann im `command` mit dem Ausdruck `${{ outputs.<output_name> }}` verwiesen werden. | |
| `outputs.<output_name>` | Objekt | Sie können entweder einen optionalen `mode` angeben oder das Objekt leer lassen. Für jede im `outputs`-Wörterbuch angegebene benannte Ausgabe generiert Azure ML automatisch einen Ausgabespeicherort basierend auf dem folgenden vorlagenbasierten Pfad: `{default-datastore}/azureml/{job-name}/{output-name}/`. Benutzer*innen können in einem späteren Release einen benutzerdefinierten Speicherort angeben. | |
| `outputs.<output_name>.mode` | Zeichenfolge | Der Modus, in dem Ausgabedateien an den Zielspeicher übermittelt werden. Für den Lese-/Schreib-Einbindungsmodus ist das Ausgabeverzeichnis ein eingebundenes Verzeichnis. Im Uploadmodus werden die in das Ausgabeverzeichnis geschriebenen Dateien am Ende des Auftrags hochgeladen. | `rw_mount`, `upload` | `rw_mount` |
| `overrides` | Objekt | Bestimmte Einstellungen einer Komponente können mit anderen Laufzeiteinstellungen überschrieben werden, wenn die Komponente in einem Auftrag ausgeführt wird. Für eine Befehlskomponente können die Eigenschaften `resources` und `distribution` über `overrides.resources` und `overrides.distribution` überschrieben werden. | | |

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

## <a name="remarks"></a>Bemerkungen

Komponentenaufträge können in Pipelineaufträgen ausgeführt werden. `az ml job`-Befehle können zum Verwalten von Azure Machine Learning-Pipelineaufträgen verwendet werden.

Komponentenaufträge können derzeit nicht als eigenständige Aufträge und nur in Pipelines ausgeführt werden.

## <a name="next-steps"></a>Nächste Schritte

- [Installieren und Verwenden der CLI (V2)](how-to-configure-cli.md)
