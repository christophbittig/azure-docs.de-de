---
title: YAML-Schema des Azure-Blobdatenspeichers der CLI (v2)
titleSuffix: Azure Machine Learning
description: Referenzdokumentation zum YAML-Schema des Azure Blobdatenspeichers der CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: ynpandey
ms.author: yogipandey
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: c7c2bc711299ad50dd1c606cb1c8fd41aa5afe7f
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132062831"
---
# <a name="cli-v2-azure-blob-datastore-yaml-schema"></a>YAML-Schema des Azure-Blobdatenspeichers der CLI (v2)

Das JSON-Quellschema finden Sie unter https://azuremlschemas.azureedge.net/latest/azureBlob.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML-Syntax

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte | Standardwert |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | Zeichenfolge | Das YAML-Schema. Wenn Sie die Azure Machine Learning VS Code-Erweiterung verwenden, um die YAML-Datei zu erstellen, können Sie durch Einfügen von `$schema` am Anfang der Datei Schema- und Ressourcenvervollständigungen aufrufen. | | |
| `type` | Zeichenfolge | **Erforderlich.** Der Typ des Datenspeichers. | `azure_blob` | |
| `name` | Zeichenfolge | **Erforderlich.** Der Name des Datenspeichers | | |
| `description` | Zeichenfolge | Beschreibung des Datenspeichers | | |
| `tags` | Objekt (object) | Wörterbuch der Tags für den Datenspeicher. | | |
| `account_name` | Zeichenfolge | **Erforderlich.** Name des Azure-Speicherkontos. | | |
| `container_name` | Zeichenfolge | **Erforderlich.** Der Name des Containers | | |
| `endpoint` | Zeichenfolge | Endpunktsuffix des Speicherdienstes, das zur Erstellung der Endpunkt-URL des Speicherkontos verwendet wird, indem der Name des Speicherkontos und `endpoint` kombiniert werden. Beispiel für die URL eines Speicherkontos: `https://<storage-account-name>.blob.core.windows.net`. | | `core.windows.net` |
| `protocol` | Zeichenfolge | Protokoll zum Herstellen einer Verbindung mit dem Container. | `https`, `wasbs` | `https` |
| `credentials` | Objekt (object) | Die Anmeldeinformationen für die anmeldeinformationsbasierte Authentifizierung zum Herstellen einer Verbindung mit dem Azure Storage-Konto. Sie können entweder einen Kontoschlüssel oder ein SAS-Token (Shared Access Signature) bereitstellen. Die Geheimnisse zu den Anmeldeinformationen werden im Schlüsseltresor des Arbeitsbereichs gespeichert. | | |
| `credentials.account_key` | Zeichenfolge | Der Kontoschlüssel für den Zugriff auf das Speicherkonto. **Entweder `credentials.account_key` oder `credentials.sas_token` ist erforderlich, wenn `credentials` angegeben wird.** | | |
| `credentials.sas_token` | Zeichenfolge | Das SAS-Token für den Zugriff auf das Speicherkonto. **Entweder `credentials.account_key` oder `credentials.sas_token` ist erforderlich, wenn `credentials` angegeben wird.** | | |

## <a name="remarks"></a>Hinweise

Mit dem Befehl `az ml datastore` können Sie Azure Machine Learning-Datenspeicher verwalten.

## <a name="examples"></a>Beispiele

Beispiele sind im [GitHub-Repository Beispiele](https://github.com/Azure/azureml-examples/tree/main/cli/resources/datastore) verfügbar. Einige davon sind unten aufgeführt.

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
