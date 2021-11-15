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
ms.openlocfilehash: 3a0048e0bba1e4772345845dbe1505cc636aba5b
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131556120"
---
# <a name="cli-v2-azure-data-lake-gen1-yaml-schema"></a>CLI (V2) YAML-Schema für Azure Data Lake Gen1

Das JSON-Quellschema finden Sie unter https://azuremlschemas.azureedge.net/latest/azureDataLakeGen1.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML-Syntax

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte | Standardwert |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | Zeichenfolge | Das YAML-Schema. Wenn Sie die YAML-Datei mithilfe der VS Code-Erweiterung für Azure Machine Learning erstellen, können Sie durch Einfügen von `$schema` am Anfang der Datei Schema- und Ressourcenvervollständigungen aufrufen. | | |
| `type` | Zeichenfolge | **Erforderlich.** Der Typ des Datenspeichers | `azure_data_lake_gen1` | |
| `name` | Zeichenfolge | **Erforderlich.** Der Name des Datenspeichers | | |
| `description` | Zeichenfolge | Die Beschreibung des Datenspeichers | | |
| `tags` | Objekt | Wörterbuch der Tags für den Datenspeicher | | |
| `store_name` | Zeichenfolge | **Erforderlich.** Der Name des Azure Data Lake Storage Gen1-Kontos | | |
| `credentials` | Objekt | Anmeldeinformationen des Dienstprinzipals zum Herstellen einer Verbindung mit dem Azure Storage-Konto. Die Geheimnisse zu den Anmeldeinformationen werden im Schlüsseltresor des Arbeitsbereichs gespeichert. | | |
| `credentials.tenant_id` | Zeichenfolge | Die Mandanten-ID des Dienstprinzipals. **Erforderlich, wenn `credentials` angegeben wird.** | | |
| `credentials.client_id` | Zeichenfolge | Die Client-ID des Dienstprinzipals. **Erforderlich, wenn `credentials` angegeben wird.** | | |
| `credentials.client_secret` | Zeichenfolge | Der geheime Clientschlüssel des Dienstprinzipals. **Erforderlich, wenn `credentials` angegeben wird.** | | |
| `credentials.resource_url` | Zeichenfolge | Die Ressourcen-URL, die bestimmt, welche Vorgänge für das Azure Data Lake Storage Gen1-Konto ausgeführt werden. | | `https://datalake.azure.net/` |
| `credentials.authority_url` | Zeichenfolge | Die Autorisierungs-URL zum Authentifizieren der Benutzer*innen. | | `https://login.microsoftonline.com` |

## <a name="remarks"></a>Hinweise

Mit dem Befehl `az ml datastore` können Sie Azure Machine Learning-Datenspeicher verwalten.

## <a name="examples"></a>Beispiele

Beispiele finden Sie im [GitHub-Beispielrepository](https://github.com/Azure/azureml-examples/tree/main/cli/resources/datastore). Im Folgenden sind mehrere aufgeführt.

## <a name="yaml-identity-based-access"></a>YAML: identitätsbasierter Zugriff

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/datastore/adls-gen1-credless.yml":::

## <a name="yaml-tenant-id-client-id-client-secret"></a>YAML: Mandanten-ID, Client-ID, geheimer Clientschlüssel

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/datastore/adls-gen1.yml":::

## <a name="next-steps"></a>Nächste Schritte

- [Installieren und Verwenden der CLI (V2)](how-to-configure-cli.md)
