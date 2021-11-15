---
title: CLI (V2) YAML-Schema für Azure Data Lake Gen1-Datenspeicher
titleSuffix: Azure Machine Learning
description: Referenzdokumentation für das YAML-Schema des Azure Data Lake Gen1-Datenspeichers der CLI (V2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: ynpandey
ms.author: yogipandey
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: c804d06f7a99590fcc7fd62e8b662e8eaadac2a5
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132062016"
---
# <a name="cli-v2-azure-data-lake-gen1-yaml-schema"></a>CLI (V2) YAML-Schema für Azure Data Lake Gen1

Das JSON-Quellschema finden Sie unter https://azuremlschemas.azureedge.net/latest/azureDataLakeGen1.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML-Syntax

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte | Standardwert |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | Zeichenfolge | Das YAML-Schema. Wenn Sie die Azure Machine Learning VS Code-Erweiterung verwenden, um die YAML-Datei zu erstellen, können Sie durch Einfügen von `$schema` am Anfang der Datei Schema- und Ressourcenvervollständigungen aufrufen. | | |
| `type` | Zeichenfolge | **Erforderlich.** Der Typ des Datenspeichers. | `azure_data_lake_gen1` | |
| `name` | Zeichenfolge | **Erforderlich.** Der Name des Datenspeichers | | |
| `description` | Zeichenfolge | Beschreibung des Datenspeichers | | |
| `tags` | Objekt (object) | Wörterbuch der Tags für den Datenspeicher. | | |
| `store_name` | Zeichenfolge | **Erforderlich.** Der Name des Azure Data Lake Storage Gen1-Kontos | | |
| `credentials` | Objekt (object) | Anmeldeinformationen des Dienstherrn für die Verbindung mit dem Azure-Speicherkonto. Die geheimen Zugangsdaten werden im Schlüsseltresor des Arbeitsbereichs gespeichert. | | |
| `credentials.tenant_id` | Zeichenfolge | Die Mieter-ID des Dienstherrn. **Erforderlich, wenn `credentials` angegeben ist.** | | |
| `credentials.client_id` | Zeichenfolge | Die Client-ID des Dienstherrn. **Erforderlich, wenn `credentials` angegeben ist.** | | |
| `credentials.client_secret` | Zeichenfolge | Das Kundengeheimnis des Dienstherrn. **Erforderlich, wenn `credentials` angegeben ist.** | | |
| `credentials.resource_url` | Zeichenfolge | Die Ressourcen-URL, die bestimmt, welche Vorgänge für das Azure Data Lake Storage Gen1-Konto ausgeführt werden. | | `https://datalake.azure.net/` |
| `credentials.authority_url` | Zeichenfolge | Die URL der Behörde, die zur Authentifizierung des Benutzers verwendet wird. | | `https://login.microsoftonline.com` |

## <a name="remarks"></a>Hinweise

Mit dem Befehl `az ml datastore` können Sie Azure Machine Learning-Datenspeicher verwalten.

## <a name="examples"></a>Beispiele

Beispiele sind im [GitHub-Repository Beispiele](https://github.com/Azure/azureml-examples/tree/main/cli/resources/datastore) verfügbar. Einige davon sind unten aufgeführt.

## <a name="yaml-identity-based-access"></a>YAML: Identitätsbasierter Zugriff

:::code language="yaml" source="~/azureml-examples-main/cli/resources/datastore/adls-gen1-credless.yml":::

## <a name="yaml-tenant-id-client-id-client-secret"></a>YAML: Mieter-ID, Mandanten-ID, Mandantengeheimnis

:::code language="yaml" source="~/azureml-examples-main/cli/resources/datastore/adls-gen1.yml":::

## <a name="next-steps"></a>Nächste Schritte

- [Installieren und Verwenden der CLI (V2)](how-to-configure-cli.md)
