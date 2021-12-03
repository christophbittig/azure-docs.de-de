---
title: YAML-Schema des CLI-Modells (v2)
titleSuffix: Azure Machine Learning
description: Referenzdokumentation zum YAML-Schema des CLI-Modells (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 51db45ecaac8be0679832dde29685a6ba2909f75
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132055755"
---
# <a name="cli-v2-model-yaml-schema"></a>YAML-Schema des CLI-Modells (v2)

Das JSON-Quellschema finden Sie unter https://azuremlschemas.azureedge.net/latest/model.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML-Syntax

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte |
| --- | ---- | ----------- | -------------- |
| `$schema` | Zeichenfolge | Das YAML-Schema. | |
| `name` | Zeichenfolge | **Erforderlich.** Name des Modells. | |
| `version` | Zeichenfolge | Version des Modells. Wird diese Angabe weggelassen, generiert Azure ML automatisch eine Version. | |
| `description` | Zeichenfolge | Beschreibung des Modells. | |
| `tags` | Objekt (object) | Wörterbuch der Tags für das Modell. | |
| `local_path` | Zeichenfolge | Lokaler Pfad zu der/den Modelldatei(en). Dieser kann entweder auf eine Datei oder ein Verzeichnis verweisen. **Eines von `local_path` oder `model_uri` ist erforderlich.** | |
| `model_uri` | Zeichenfolge | URI der Modelldatei(en). Dieser kann entweder auf eine Datei oder ein Verzeichnis verweisen. **Eines von `local_path` oder `model_uri` ist erforderlich.** | |
| `model_format` | Zeichenfolge | Speicherformat des Modells. Anwendbar für no-code Bereitstellung szenarien. | `custom`, `mlflow`, `triton`, `openai` |
| `flavors` | Objekt (object) | Geschmacksrichtungen des Modells. Jeder Modellspeicherformattyp kann einen oder mehrere unterstützte Flavors haben. Anwendbar für no-code Bereitstellung szenarien. | |

## <a name="remarks"></a>Hinweise

Mit dem Befehl `az ml model` können Sie Azure Machine Learning-Modelle verwalten.

## <a name="examples"></a>Beispiele

Beispiele sind im [Beispiele GitHub-Repository](https://github.com/Azure/azureml-examples/tree/main/cli/assets/model) verfügbar. Einige davon sind unten aufgeführt.

## <a name="yaml-local-file"></a>YAML: lokale Datei

:::code language="yaml" source="~/azureml-examples-main/cli/assets/model/local-file.yml":::

## <a name="yaml-local-folder-in-mlflow-format"></a>YAML: lokaler Ordner im MLflow-Format

:::code language="yaml" source="~/azureml-examples-main/cli/assets/model/local-mlflow.yml":::

- [Installieren und Verwenden der CLI (V2)](how-to-configure-cli.md)
