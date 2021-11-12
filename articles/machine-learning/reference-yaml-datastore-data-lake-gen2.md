---
title: CLI (V2) YAML-Schema für Azure Data Lake Gen2-Datenspeicher
titleSuffix: Azure Machine Learning
description: Referenzdokumentation für das YAML-Schema des Azure Data Lake Gen2-Datenspeichers der CLI (V2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: ynpandey
ms.author: yogipandey
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 56041a7fc16b09f1622d7090a3ef6e2d088742ee
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131556063"
---
# <a name="cli-v2-azure-data-lake-gen2-yaml-schema"></a>CLI (V2) YAML-Schema für Azure Data Lake Gen2

Das JSON-Quellschema finden Sie unter https://azuremlschemas.azureedge.net/latest/azureDataLakeGen2.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML-Syntax

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte | Standardwert |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | Zeichenfolge | Das YAML-Schema. Wenn Sie die Azure Machine Learning VS Code-Erweiterung verwenden, um die YAML-Datei zu erstellen, können Sie durch Einfügen von `$schema` am Anfang der Datei Schema- und Ressourcenvervollständigungen aufrufen. | | |
| `type` | Zeichenfolge | **Erforderlich.** Der Typ des Datenspeichers. | `azure_data_lake_gen2` | |
| `name` | Zeichenfolge | **Erforderlich.** Der Name des Datenspeichers | | |
| `description` | Zeichenfolge | Beschreibung des Datenspeichers | | |
| `tags` | Objekt | Wörterbuch der Tags für den Datenspeicher. | | |
| `account_name` | Zeichenfolge | **Erforderlich.** Name des Azure-Speicherkontos. | | |
| `filesystem` | Zeichenfolge | **Erforderlich.** Name des Dateisystems. Das übergeordnete Verzeichnis, das die Dateien und Ordner enthält. Dies entspricht einem Container in Azure Blob Storage. | | |
| `endpoint` | Zeichenfolge | Endpunktsuffix des Speicherdienstes, das zur Erstellung der Endpunkt-URL des Speicherkontos verwendet wird, indem der Name des Speicherkontos und `endpoint` kombiniert werden. Beispiel für die URL eines Speicherkontos: `https://<storage-account-name>.dfs.core.windows.net`. | | `core.windows.net` |
| `protocol` | Zeichenfolge | Protokoll, das für die Verbindung zum Dateisystem verwendet werden soll. | `https`, `abfss` | `https` |
| `credentials` | Objekt | Anmeldeinformationen des Dienstherrn für die Verbindung mit dem Azure-Speicherkonto. Die geheimen Zugangsdaten werden im Schlüsseltresor des Arbeitsbereichs gespeichert. | | |
| `credentials.tenant_id` | Zeichenfolge | Die Mieter-ID des Dienstherrn. **Erforderlich, wenn `credentials` angegeben ist.** | | |
| `credentials.client_id` | Zeichenfolge | Die Client-ID des Dienstherrn. **Erforderlich, wenn `credentials` angegeben ist.** | | |
| `credentials.client_secret` | Zeichenfolge | Das Kundengeheimnis des Dienstherrn. **Erforderlich, wenn `credentials` angegeben ist.** | | |
| `credentials.resource_url` | Zeichenfolge | Die Ressourcen-URL, die bestimmt, welche Vorgänge auf dem Azure Data Lake Storage Gen2-Konto ausgeführt werden. | | `https://storage.azure.com/` |
| `credentials.authority_url` | Zeichenfolge | Die URL der Behörde, die zur Authentifizierung des Benutzers verwendet wird. | | `https://login.microsoftonline.com` |

## <a name="remarks"></a>Hinweise

Mit dem Befehl `az ml datastore` können Sie Azure Machine Learning-Datenspeicher verwalten.

## <a name="examples"></a>Beispiele

Beispiele sind im [Beispiele GitHub-Repository](https://github.com/Azure/azureml-examples/tree/main/cli/resources/datastore) verfügbar. Einige davon sind unten aufgeführt.

## <a name="yaml-identity-based-access"></a>YAML: Identitätsbasierter Zugriff

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/datastore/adls-gen2-credless.yml":::

## <a name="yaml-tenant-id-client-id-client-secret"></a>YAML: Mieter-ID, Mandanten-ID, Mandantengeheimnis

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/datastore/adls-gen2.yml":::

## <a name="next-steps"></a>Nächste Schritte

- [Installieren und Verwenden der CLI (V2)](how-to-configure-cli.md)
