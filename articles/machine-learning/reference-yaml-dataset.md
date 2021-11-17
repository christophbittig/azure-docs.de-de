---
title: YAML-Schema des Datasets der CLI (v2)
titleSuffix: Azure Machine Learning
description: Referenzdokumentation zum YAML-Schema für das Dataset der CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.topic: reference
author: ynpandey
ms.author: yogipandey
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 4a22fee30a28008fa440d606360623e156c332a0
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132133674"
---
# <a name="cli-v2-dataset-yaml-schema"></a>YAML-Schema des Datasets der CLI (v2)

Das JSON-Quellschema finden Sie unter https://azuremlschemas.azureedge.net/latest/dataset.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML-Syntax

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte |
| --- | ---- | ----------- | -------------- |
| `$schema` | Zeichenfolge | Das YAML-Schema Wenn Sie die VS Code-Erweiterung für Azure Machine Learning zum Erstellen einer YAML-Datei verwenden, können Sie durch das Einfügen von `$schema` am Anfang der Datei Schema- und Ressourcenvervollständigungen aufrufen. | |
| `name` | Zeichenfolge | **Erforderlich.** Name des Datasets. | |
| `version` | Zeichenfolge | Version des Datasets. Wenn sie nicht angegeben wird, generiert Azure ML automatisch eine Version. | |
| `description` | Zeichenfolge | Beschreibung des Datasets | |
| `tags` | Objekt (object) | Wörterbuch der Tags für das Dataset | |
| `local_path` | Zeichenfolge | Absoluter oder relativer Pfad einer einzelnen lokalen Datei oder eines Ordners, aus der bzw. dem das Dataset erstellt wird. **Entweder `local_path` oder `paths` ist erforderlich.** | |
| `paths` | array | Eine Liste der URI-Quellen, aus denen das Dataset erstellt wird. Jeder Eintrag in der Liste sollte dem Schema entsprechen, das im [Quellpfad des Datasets](#dataset-source-path) definiert ist. Derzeit wird nur eine einzelne Quelle unterstützt.  **Entweder `local_path` oder `paths` ist erforderlich.** | |

### <a name="dataset-source-path"></a>Quellpfad des Datasets

| Schlüssel | type | Beschreibung |
| --- | ---- | ----------- |
| `file` | Zeichenfolge | URI zu einer einzelnen Datei, die als Quelle für das Dataset verwendet wird. Unterstützte URI-Typen sind `azureml`, `https`, `wasbs`, `abfss` und `adl`. Weitere Informationen zur Verwendung des URI-Formats `azureml://` finden Sie unter [YAML-Kernsyntax](reference-yaml-core-syntax.md). **Entweder `file` oder `folder` ist erforderlich.** |
| `folder` | Zeichenfolge | URI zu einem Ordner, der als Quelle für das Dataset verwendet wird. Unterstützte URI-Typen sind `azureml`, `https`, `wasbs`, `abfss` und `adl`. Weitere Informationen zur Verwendung des URI-Formats `azureml://` finden Sie unter [YAML-Kernsyntax](reference-yaml-core-syntax.md). **Entweder `file` oder `folder` ist erforderlich.** |

## <a name="remarks"></a>Hinweise

Mit den Befehlen `az ml dataset` können Sie Azure Machine Learning-Datasets verwalten.

## <a name="examples"></a>Beispiele

Beispiele finden Sie im [GitHub-Beispielrepository](https://github.com/Azure/azureml-examples/tree/main/cli/assets/dataset). Im Folgenden sind mehrere aufgeführt.

## <a name="yaml-datastore-file"></a>YAML: Datenspeicherdatei

:::code language="yaml" source="~/azureml-examples-main/cli/assets/dataset/cloud-file.yml":::

## <a name="yaml-datastore-folder"></a>YAML: Datenspeicherordner

:::code language="yaml" source="~/azureml-examples-main/cli/assets/dataset/cloud-folder.yml":::

## <a name="yaml-https-file"></a>YAML: HTTPS-Datei

:::code language="yaml" source="~/azureml-examples-main/cli/assets/dataset/cloud-file-https.yml":::

## <a name="yaml-https-folder"></a>YAML: HTTPS-Ordner

:::code language="yaml" source="~/azureml-examples-main/cli/assets/dataset/cloud-folder-https.yml":::

## <a name="yaml-wasbs-file"></a>YAML: WASBS-Datei

:::code language="yaml" source="~/azureml-examples-main/cli/assets/dataset/cloud-file-wasbs.yml":::

## <a name="yaml-wasbs-folder"></a>YAML: WASBS-Ordner

:::code language="yaml" source="~/azureml-examples-main/cli/assets/dataset/cloud-folder-wasbs.yml":::

## <a name="yaml-local-file"></a>YAML: lokale Datei

:::code language="yaml" source="~/azureml-examples-main/cli/assets/dataset/local-file.yml":::

## <a name="yaml-local-folder"></a>YAML: lokaler Ordner

:::code language="yaml" source="~/azureml-examples-main/cli/assets/dataset/local-folder.yml":::

## <a name="next-steps"></a>Nächste Schritte

- [Installieren und Verwenden der CLI (V2)](how-to-configure-cli.md)
