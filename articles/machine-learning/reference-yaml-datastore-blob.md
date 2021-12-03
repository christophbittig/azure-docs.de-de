---
title: YAML-Schema des Azure-Blobdatenspeichers der CLI (v2)
titleSuffix: Azure Machine Learning
description: Referenzdokumentation zum YAML-Schema des Azure Blobdatenspeichers der CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.topic: reference
author: ynpandey
ms.author: yogipandey
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: a2eb42974b64cf3d70422b4fef8d4611639cf826
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132134551"
---
# <a name="cli-v2-azure-blob-datastore-yaml-schema"></a>YAML-Schema des Azure-Blobdatenspeichers der CLI (v2)

Das JSON-Quellschema finden Sie unter https://azuremlschemas.azureedge.net/latest/azureBlob.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML-Syntax

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte | Standardwert |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | Zeichenfolge | Das YAML-Schema Wenn Sie die VS Code-Erweiterung für Azure Machine Learning zum Erstellen einer YAML-Datei verwenden, können Sie durch das Einfügen von `$schema` am Anfang der Datei Schema- und Ressourcenvervollständigungen aufrufen. | | |
| `type` | Zeichenfolge | **Erforderlich.** Der Typ des Datenspeichers | `azure_blob` | |
| `name` | Zeichenfolge | **Erforderlich.** Der Name des Datenspeichers | | |
| `description` | Zeichenfolge | Die Beschreibung des Datenspeichers | | |
| `tags` | Objekt (object) | Wörterbuch der Tags für den Datenspeicher | | |
| `account_name` | Zeichenfolge | **Erforderlich.** Der Name des Azure-Speicherkontos | | |
| `container_name` | Zeichenfolge | **Erforderlich.** Der Name des Containers | | |
| `endpoint` | Zeichenfolge | Endpunktsuffix des Speicherdiensts, der zum Erstellen der Endpunkt-URL des Speicherkontos verwendet wird, indem der Speicherkontoname und `endpoint` kombiniert werden. Beispiel für die URL eines Speicherkontos: `https://<storage-account-name>.blob.core.windows.net`. | | `core.windows.net` |
| `protocol` | Zeichenfolge | Protokoll zum Herstellen einer Verbindung mit dem Container. | `https`, `wasbs` | `https` |
| `credentials` | Objekt (object) | Anmeldeinformationsbasierte Authentifizierung zum Herstellen einer Verbindung mit dem Azure-Speicherkonto. Sie können entweder einen Kontoschlüssel oder ein Shared Access Signature-Token (SAS) bereitstellen. Anmeldeinformationsgeheimnisse werden im Schlüsseltresor des Arbeitsbereichs gespeichert. | | |
| `credentials.account_key` | Zeichenfolge | Der Kontoschlüssel für den Zugriff auf das Speicherkonto. **Entweder ist `credentials.account_key` oder `credentials.sas_token` erforderlich, wenn `credentials` angegeben wird.** | | |
| `credentials.sas_token` | Zeichenfolge | Das SAS-Token für den Zugriff auf das Speicherkonto. **Entweder ist `credentials.account_key` oder `credentials.sas_token` erforderlich, wenn `credentials` angegeben wird.** | | |

## <a name="remarks"></a>Hinweise

Mit dem Befehl `az ml datastore` können Sie Azure Machine Learning-Datenspeicher verwalten.

## <a name="examples"></a>Beispiele

Beispiele finden Sie im [GitHub-Beispielrepository](https://github.com/Azure/azureml-examples/tree/main/cli/resources/datastore). Im Folgenden sind mehrere aufgeführt.

## <a name="yaml-identity-based-access"></a>YAML: identitätsbasierter Zugriff

:::code language="yaml" source="~/azureml-examples-main/cli/resources/datastore/blob-credless.yml":::

## <a name="yaml-account-key"></a>YAML: Kontoschlüssel

:::code language="yaml" source="~/azureml-examples-main/cli/resources/datastore/blob.yml":::

## <a name="yaml-wasbs-protocol"></a>YAML: WASBS-Protokoll

:::code language="yaml" source="~/azureml-examples-main/cli/resources/datastore/blob-protocol.yml":::

## <a name="yaml-sas-token"></a>YAML: SAS-Token

:::code language="yaml" source="~/azureml-examples-main/cli/resources/datastore/blob-sas.yml":::

## <a name="next-steps"></a>Nächste Schritte

- [Installieren und Verwenden der CLI (V2)](how-to-configure-cli.md)
