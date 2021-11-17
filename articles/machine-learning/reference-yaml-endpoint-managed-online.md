---
title: YAML-Referenz zu Onlineendpunkten (Vorschau)
titleSuffix: Azure Machine Learning
description: Erfahren Sie mehr über die YAML-Dateien, die zum Bereitstellen von Modellen als Onlineendpunkte verwendet werden.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: how-to
author: rsethur
ms.author: seramasu
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 462c781ae4f304d2b9cb46b9b89d2ff7fbf7eb22
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132137820"
---
# <a name="cli-v2-online-endpoint-yaml-schema"></a>CLI (v2): YAML-Schema für Onlineendpunkt

Das JSON-Quellschema finden Sie unter https://azuremlschemas.azureedge.net/latest/managedOnlineEndpoint.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

> [!NOTE]
> Ein vollständig angegebener YAML-Beispielcode für verwaltete Onlineendpunkte steht als [Referenz](https://azuremlschemas.azureedge.net/latest/managedOnlineEndpoint.template.yaml) zur Verfügung.

## <a name="yaml-syntax"></a>YAML-Syntax

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte | Standardwert |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | Zeichenfolge | Das YAML-Schema Wenn Sie die VS Code-Erweiterung für Azure Machine Learning zum Erstellen einer YAML-Datei verwenden, können Sie durch das Einfügen von `$schema` am Anfang der Datei Schema- und Ressourcenvervollständigungen aufrufen. | | |
| `name` | Zeichenfolge | **Erforderlich.** Name des Endpunkts. Muss auf Azure-Regionsebene eindeutig sein. <br><br> Benennungsregeln sind [hier](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview) definiert.| | |
| `description` | Zeichenfolge | Eine Beschreibung des Endpunkts | | |
| `tags` | Objekt (object) | Wörterbuch der Tags für den Endpunkt | | |
| `auth_mode` | Zeichenfolge | Die Authentifizierungsmethode für den Endpunkt Die schlüsselbasierte Authentifizierung und die tokenbasierte Azure ML-Authentifizierung werden unterstützt. Die schlüsselbasierte Authentifizierung läuft nicht ab, jedoch die tokenbasierte Azure ML-Authentifizierung. | `key`, `aml_token` | `key` |
| `allow_public_access` | boolean | Gibt an, ob der öffentliche Zugriff zugelassen werden soll, wenn Private Link aktiviert ist. | | `true` |
| `identity` | Objekt (object) | Die Konfiguration der verwalteten Identität für den Zugriff auf Azure-Ressourcen für die Endpunktbereitstellung und den Rückschluss. | | |
| `identity.type` | Zeichenfolge | Der Typ der verwalteten Identität. Wenn der Typ `user_assigned` ist, muss auch die Eigenschaft `identity.user_assigned_identities` angegeben werden. | `system_assigned`, `user_assigned` | |
| `identity.user_assigned_identities` | array | Liste der vollqualifizierten Ressourcen-IDs der vom Benutzer zugewiesenen Identitäten. | | |

## <a name="remarks"></a>Hinweise

Mit dem Befehl `az ml online-endpoint` können Sie Azure Machine Learning-Onlineendpunkte verwalten.

## <a name="examples"></a>Beispiele

Beispiele finden Sie im [GitHub-Beispielrepository](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/batch). Im Folgenden sind mehrere aufgeführt.

## <a name="yaml-basic"></a>YAML: Grundlagen

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/sample/endpoint.yml":::

## <a name="yaml-system-assigned-identity"></a>YAML: Systemseitig zugewiesene Identität

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/managed-identities/1-sai-create-endpoint.yml":::

## <a name="yaml-user-assigned-identity"></a>YAML: Benutzerseitig zugewiesene Identität

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/managed-identities/1-uai-create-endpoint.yml":::

## <a name="next-steps"></a>Nächste Schritte

- [Installieren und Verwenden der CLI (v2)](how-to-configure-cli.md)
- Erfahren Sie, wie Sie ein [Modell mit einem verwalteten Onlineendpunkt bereitstellen](how-to-deploy-managed-online-endpoints.md).
- [Problembehandlung für die Bereitstellung und Bewertung verwalteter Onlineendpunkte (Vorschau)](./how-to-troubleshoot-online-endpoints.md)