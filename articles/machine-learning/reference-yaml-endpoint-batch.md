---
title: 'CLI (v2) Batchendpunkt: YAML-Schema'
titleSuffix: Azure Machine Learning
description: Referenzdokumentation für das YAML-Schema des CLI (v2) Batchendpunkts
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: tracychms
ms.author: tracych
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: c91de82d4b0eec1256d34a28132ae3161c62690a
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132057763"
---
# <a name="cli-v2-batch-endpoint-yaml-schema"></a>CLI (v2) Batchendpunkt: YAML-Schema

Das JSON-Quellschema finden Sie unter https://azuremlschemas.azureedge.net/latest/batchEndpoint.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML-Syntax

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte | Standardwert |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | Zeichenfolge | Das YAML-Schema Wenn Sie die VS Code-Erweiterung für Azure Machine Learning zum Erstellen einer YAML-Datei verwenden, können Sie durch das Einfügen von `$schema` am Anfang der Datei Schema- und Ressourcenvervollständigungen aufrufen. | | |
| `name` | Zeichenfolge | **Erforderlich.** Name des Endpunkts. Muss auf Azure-Regionsebene eindeutig sein. | | |
| `description` | Zeichenfolge | Eine Beschreibung des Endpunkts | | |
| `tags` | Objekt (object) | Wörterbuch der Tags für den Endpunkt | | |
| `auth_mode` | Zeichenfolge | Die Authentifizierungsmethode für den Endpunkt Derzeit wird Azure Active Directory (Azure AD) tokenbasierte Authentifizierung unterstützt. | `aad_token` | `aad_token` |
| `defaults` | Objekt (object) | Standardeinstellungen für den Endpunkt | | |
| `defaults.deployment_name` | Zeichenfolge | Name der Bereitstellung, die als Standardbereitstellung für den Endpunkt dient | | |

## <a name="remarks"></a>Hinweise

Mit dem Befehl `az ml batch-endpoint` können Sie Azure Machine Learning-Endpunkte verwalten.

## <a name="examples"></a>Beispiele

Beispiele finden Sie im [GitHub-Repository für Beispiele](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/batch). Im Folgenden sind mehrere aufgeführt.

## <a name="yaml-basic"></a>YAML: Grundlagen

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/batch/batch-endpoint.yml":::

## <a name="next-steps"></a>Nächste Schritte

- [Installieren und Verwenden der CLI (V2)](how-to-configure-cli.md)
