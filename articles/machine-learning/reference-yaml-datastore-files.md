---
title: YAML-Schema des Azure Files-Datenspeichers der CLI (v2)
titleSuffix: Azure Machine Learning
description: Referenzdokumentation zum YAML-Schema des Azure Files-Datenspeichers der CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: ynpandey
ms.author: yogipandey
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 1f06483d917631be8e1e4d97d8a37f922a6d11dc
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131555876"
---
# <a name="cli-v2-azure-files-datastore-yaml-schema"></a>YAML-Schema des Azure Files-Datenspeichers der CLI (v2)

Das JSON-Quellschema finden Sie unter https://azuremlschemas.azureedge.net/latest/azureFile.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML-Syntax

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte | Standardwert |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | Zeichenfolge | Das YAML-Schema. Wenn Sie die VS Code-Erweiterung für Azure Machine Learning zum Erstellen einer YAML-Datei verwenden, können Sie durch das Einfügen von `$schema` am Anfang der Datei Schema- und Ressourcenvervollständigungen aufrufen. | | |
| `type` | Zeichenfolge | **Erforderlich.** Der Typ des Datenspeichers | `azure_file` | |
| `name` | Zeichenfolge | **Erforderlich.** Der Name des Datenspeichers | | |
| `description` | Zeichenfolge | Die Beschreibung des Datenspeichers | | |
| `tags` | Objekt | Wörterbuch der Tags für den Datenspeicher | | |
| `account_name` | Zeichenfolge | **Erforderlich.** Der Name des Azure-Speicherkontos | | |
| `file_share_name` | Zeichenfolge | **Erforderlich.** Der Name der Dateifreigabe | | |
| `endpoint` | Zeichenfolge | Endpunktsuffix des Speicherdiensts, der zum Erstellen der Endpunkt-URL des Speicherkontos verwendet wird, indem der Speicherkontoname und `endpoint` kombiniert werden. Beispiel für die URL eines Speicherkontos: `https://<storage-account-name>.file.core.windows.net`. | | `core.windows.net` |
| `protocol` | Zeichenfolge | Protokoll, das zum Herstellen einer Verbindung mit der Dateifreigabe verwendet werden soll | `https` | `https` |
| `credentials` | Objekt | Anmeldeinformationsbasierte Authentifizierung zum Herstellen einer Verbindung mit dem Azure-Speicherkonto. Sie können entweder einen Kontoschlüssel oder ein Shared Access Signature-Token (SAS) bereitstellen. Anmeldeinformationsgeheimnisse werden im Schlüsseltresor des Arbeitsbereichs gespeichert. | | |
| `credentials.account_key` | Zeichenfolge | Der Kontoschlüssel für den Zugriff auf das Speicherkonto. **Entweder ist `credentials.account_key` oder `credentials.sas_token` erforderlich, wenn `credentials` angegeben wird.** | | |
| `credentials.sas_token` | Zeichenfolge | Das SAS-Token für den Zugriff auf das Speicherkonto. **Entweder ist `credentials.account_key` oder `credentials.sas_token` erforderlich, wenn `credentials` angegeben wird.** | | |

## <a name="remarks"></a>Hinweise

Mit dem Befehl `az ml datastore` können Sie Azure Machine Learning-Datenspeicher verwalten.

## <a name="examples"></a>Beispiele

Beispiele finden Sie im [GitHub-Repository für Beispiele](https://github.com/Azure/azureml-examples/tree/main/cli/resources/datastore). Im Folgenden sind mehrere aufgeführt.

## <a name="yaml-account-key"></a>YAML: Kontoschlüssel

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/datastore/file.yml":::

## <a name="yaml-sas-token"></a>YAML: SAS-Token

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/datastore/file-sas.yml":::

## <a name="next-steps"></a>Nächste Schritte

- [Installieren und Verwenden der CLI (V2)](how-to-configure-cli.md)
