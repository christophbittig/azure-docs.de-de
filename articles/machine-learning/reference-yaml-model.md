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
ms.openlocfilehash: a9f3ac8ce87c3cec11c7bb5cb17e1004bce9f953
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131556881"
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
| `tags` | Objekt | Wörterbuch der Tags für das Modell. | |
| `local_path` | Zeichenfolge | Lokaler Pfad zu der/den Modelldatei(en). Dieser kann entweder auf eine Datei oder ein Verzeichnis verweisen. **Eines von `local_path` oder `model_uri` ist erforderlich.** | |
| `model_uri` | Zeichenfolge | URI der Modelldatei(en). Dieser kann entweder auf eine Datei oder ein Verzeichnis verweisen. **Eines von `local_path` oder `model_uri` ist erforderlich.** | |
| `model_format` | Zeichenfolge | Speicherformat des Modells. Anwendbar für no-code Bereitstellung szenarien. | `custom`, `mlflow`, `triton`, `openai` |
| `flavors` | Objekt | Geschmacksrichtungen des Modells. Jeder Modellspeicherformattyp kann einen oder mehrere unterstützte Flavors haben. Anwendbar für no-code Bereitstellung szenarien. | |

## <a name="remarks"></a>Hinweise

Mit dem Befehl `az ml model` können Sie Azure Machine Learning-Modelle verwalten.

## <a name="examples"></a>Beispiele

Beispiele sind im [examples GitHub Repository](https://github.com/Azure/azureml-examples/tree/main/cli/assets/model) verfügbar. Einige davon sind unten aufgeführt.

## <a name="yaml-local-file"></a>YAML: lokale Datei

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/assets/model/local-file.yml":::

## <a name="yaml-local-folder-in-mlflow-format"></a>YAML: lokaler Ordner im MLflow-Format

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/assets/model/local-mlflow.yml":::

- [Installieren und Verwenden der CLI (V2)](how-to-configure-cli.md)
