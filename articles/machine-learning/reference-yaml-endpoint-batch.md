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
ms.openlocfilehash: cfb475bef0cb308a8e7570a4cbad71e4e9bebb54
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566489"
---
# <a name="cli-v2-batch-endpoint-yaml-schema"></a>CLI (v2) Batchendpunkt: YAML-Schema

Das JSON-Quellschema finden Sie unter https://azuremlschemas.azureedge.net/latest/batchEndpoint.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML-Syntax

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte | Standardwert |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | Zeichenfolge | Das YAML-Schema Wenn Sie die YAML-Datei mithilfe der VS Code-Erweiterung für Azure Machine Learning erstellen, können Sie `$schema` am Anfang der Datei Schema- und Ressourcenvervollständigungen aufrufen. | | |
| `name` | Zeichenfolge | **Erforderlich.** Name des Endpunkts. Muss auf Azure-Regionsebene eindeutig sein. | | |
| `description` | Zeichenfolge | Eine Beschreibung des Endpunkts | | |
| `tags` | Objekt | Wörterbuch der Tags für den Endpunkt | | |
| `auth_mode` | Zeichenfolge | Die Authentifizierungsmethode für den Endpunkt Derzeit wird Azure Active Directory (Azure AD) tokenbasierte Authentifizierung unterstützt. | `aad_token` | `aad_token` |
| `defaults` | Objekt | Standardeinstellungen für den Endpunkt | | |
| `defaults.deployment_name` | Zeichenfolge | Name der Bereitstellung, die als Standardbereitstellung für den Endpunkt dient | | |

## <a name="remarks"></a>Bemerkungen

Mit dem Befehl `az ml batch-endpoint` können Sie Azure Machine Learning-Endpunkte verwalten.

## <a name="examples"></a>Beispiele

Beispiele finden Sie im [GitHub-Beispielrepository](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/batch). Im Folgenden sind mehrere aufgeführt.

## <a name="yaml-basic"></a>YAML: basic

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/batch/batch-endpoint.yml":::

## <a name="next-steps"></a>Nächste Schritte

- [Installieren und Verwenden der CLI (V2)](how-to-configure-cli.md)
