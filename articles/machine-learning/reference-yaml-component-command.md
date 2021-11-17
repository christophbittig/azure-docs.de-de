---
title: YAML-Schema der CLI-Befehlskomponente (v2)
titleSuffix: Azure Machine Learning
description: Referenzdokumentation für das YAML-Schema für Befehlskomponenten der CLI (v2)
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 2a512226a507d890963ee9e407c7eb8330e33fc5
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132552316"
---
# <a name="cli-v2-command-component-yaml-schema"></a>YAML-Schema der CLI-Befehlskomponente (v2)

Das JSON-Quellschema finden Sie unter https://azuremlschemas.azureedge.net/latest/commandComponent.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML-Syntax

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte | Standardwert |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | Zeichenfolge | Das YAML-Schema Wenn Sie die VS Code-Erweiterung für Azure Machine Learning zum Erstellen einer YAML-Datei verwenden, können Sie durch das Einfügen von `$schema` am Anfang der Datei Schema- und Ressourcenvervollständigungen aufrufen. | | |
| `type` | const | Der Typ der Komponente. | `command` | `command` |
| `name` | Zeichenfolge | **Erforderlich.** Der Name der Komponente. | | |
| `version` | Zeichenfolge | Version der Komponente. Wenn dies nicht angegeben wird, generiert Azure ML automatisch eine Version. | | |
| `display_name` | Zeichenfolge | Anzeigename der Komponente auf der Studio-Benutzeroberfläche Kann innerhalb des Arbeitsbereichs auch nicht eindeutig sein | | |
| `description` | Zeichenfolge | Die Beschreibung der Komponente | | |
| `tags` | Objekt (object) | Wörterbuch der Tags für die Komponente | | |
| `command` | Zeichenfolge | **Erforderlich.** Den auszuführenden Befehl. | | |
| `code.local_path` | Zeichenfolge | Lokaler Pfad zum Quellcodeverzeichnis, das hochgeladen und für die Komponente verwendet werden soll | | |
| `environment` | Zeichenfolge oder Objekt | **Erforderlich.** Die Umgebung, die für die Komponente verwendet werden soll. Dieser Wert kann entweder ein Verweis auf eine vorhandene Umgebung mit Versionsangabe im Arbeitsbereich oder eine Inlineumgebungsspezifikation sein. <br><br> Verwenden Sie die Syntax `azureml:<environment-name>:<environment-version>`, um auf eine vorhandene Umgebung zu verweisen. <br><br> Um eine Umgebung inline zu definieren, befolgen Sie das [Umgebungsschema](reference-yaml-environment.md#yaml-syntax). Schließen Sie die Eigenschaften `name` und `version` aus, da sie für Inlineumgebungen nicht unterstützt werden. | | |
| `distribution` | Objekt (object) | Die Verteilungskonfiguration für verteilte Trainingsszenarien Eine von [MpiConfiguration](#mpiconfiguration), [PyTorchConfiguration](#pytorchconfiguration)oder [TensorFlowConfiguration](#tensorflowconfiguration). | | |
| `resources.instance_count` | integer | Die Anzahl von Knoten, die für den Auftrag verwendet werden sollen | | `1` |
| `inputs` | Objekt (object) | Wörterbuch der Komponenteneingaben Der Schlüssel ist ein Name für die Eingabe im Kontext der Komponente, und der Wert ist die Komponenteneingabedefinition. <br><br> Eingaben können im `command` mit dem Ausdruck `${{ inputs.<input_name> }}` verwiesen werden. | | |
| `inputs.<input_name>` | Objekt (object) | Die Komponenteneingabedefinition Informationen zu den konfigurierbaren Eigenschaften finden Sie unter [Komponenteneingabe](#component-input). | | |
| `outputs` | Objekt (object) | Wörterbuch der Komponentenausgaben Der Schlüssel ist ein Name für die Ausgabe im Kontext der Komponente, und der Wert ist die Komponentenausgabedefinition. <br><br> Ausgaben können im `command` mit dem Ausdruck `${{ outputs.<output_name> }}` verwiesen werden. | |
| `outputs.<output_name>` | Objekt (object) | Die Komponentenausgabedefinition Informationen zu den konfigurierbaren Eigenschaften finden Sie unter [Komponentenausgabe](#component-output). | |

### <a name="distribution-configurations"></a>Verteilungskonfigurationen

#### <a name="mpiconfiguration"></a>MpiConfiguration

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte |
| --- | ---- | ----------- | -------------- |
| `type` | const | **Erforderlich.** Verteilungstyp.  | `mpi` |
| `process_count_per_instance` | integer | **Erforderlich.** Die Anzahl der Prozesse pro Knoten, die für den Auftrag gestartet werden sollen  | |

#### <a name="pytorchconfiguration"></a>PyTorchConfiguration

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte | Standardwert |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | const | **Erforderlich.** Verteilungstyp.  | `pytorch` | |
| `process_count_per_instance` | integer | Die Anzahl der Prozesse pro Knoten, die für den Auftrag gestartet werden sollen | |  `1` |

#### <a name="tensorflowconfiguration"></a>TensorFlowConfiguration

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte | Standardwert |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | const | **Erforderlich.** Verteilungstyp.  | `tensorflow` |
| `worker_count` | integer | Die Anzahl von Workerrollen, die für den Auftrag gestartet werden. | | Wird standardmäßig auf `resources.instance_count` festgelegt. |
| `parameter_server_count` | integer | Die Anzahl von Parameterservern, die für den Auftrag gestartet werden | | `0` |

### <a name="component-input"></a>Komponenteneingabe

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte | Standardwert |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | Zeichenfolge | **Erforderlich.** Der Typ der Komponenteneingabe <br><br> Verwenden Sie `type: path`, wenn der Eingabewert des Laufzeitauftrags ein Daten-URI oder ein Azure ML sein soll, wenn die Komponente ausgeführt wird. | `number`, `integer`, `boolean`, `string`, `path` | |
| `description` | Zeichenfolge | Die Beschreibung der Eingabe | | |
| `default` | number, integer, boolean oder string | Der Standardwert für die Eingabe | | |
| `optional` | boolean | Gibt an, ob die Eingabe erforderlich ist | | `false` |
| `min` | ganze Zahl oder Zahl | Der minimale akzeptierte Wert für die Eingabe Dieses Feld kann nur angegeben werden, wenn das Feld `type` `number` oder `integer` ist. | |
| `max` | ganze Zahl oder Zahl | Der maximale akzeptierte Wert für die Eingabe Dieses Feld kann nur angegeben werden, wenn das Feld `type` `number` oder `integer` ist. | |
| `enum` | array | Die Liste der zulässigen Werte für die Eingabe Trifft nicht zu, wenn das Feld `type` `boolean` ist.  | |

### <a name="component-output"></a>Komponentenausgabe

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte | Standardwert |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | Zeichenfolge | **Erforderlich.** Der Typ der Komponentenausgabe | `path` | |
| `description` | Zeichenfolge | Beschreibung der Ausgabe | | |

## <a name="remarks"></a>Hinweise

Mit den Befehlen `az ml component` können Sie Azure Machine Learning-Komponenten verwalten.

## <a name="examples"></a>Beispiele

Beispiele für Befehlskomponenten finden Sie im GitHub-Beispielrepository. Ausgewählte Beispiele sind unten dargestellt.

Beispiele finden Sie im [GitHub-Beispielrepository](https://github.com/Azure/azureml-examples/tree/main/cli/jobs/pipelines-with-components). Im Folgenden sind mehrere aufgeführt.

## <a name="hello-world-command-component"></a>Hello World-Befehlskomponente

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/pipelines-with-components/basics/2a_basic_component/component.yml":::

## <a name="next-steps"></a>Nächste Schritte

- [Installieren und Verwenden der CLI (V2)](how-to-configure-cli.md)
