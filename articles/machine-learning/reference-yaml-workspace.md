---
title: 'CLI-Arbeitsbereich (v2): YAML-Schema'
titleSuffix: Azure Machine Learning
description: Referenzdokumentation für das YAML-Schema für CLI-Arbeitsbereiche (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: ea641b24b43c7ad1d22e497f9d2558ccb163e81b
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132060016"
---
# <a name="cli-v2-workspace-yaml-schema"></a>CLI-Arbeitsbereich (v2): YAML-Schema

Das JSON-Quellschema finden Sie unter https://azuremlschemas.azureedge.net/latest/workspace.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML-Syntax

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte | Standardwert |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | Zeichenfolge | Das YAML-Schema. Wenn Sie die Azure Machine Learning VS Code-Erweiterung verwenden, um die YAML-Datei zu erstellen, können Sie durch Einfügen von `$schema` am Anfang der Datei Schema- und Ressourcenvervollständigungen aufrufen. | | |
| `name` | Zeichenfolge | **Erforderlich.** Name des Arbeitsbereichs | | |
| `display_name` | Zeichenfolge | Anzeigename des Arbeitsbereichs auf der Studio-Benutzeroberfläche. Kann innerhalb der Ressourcengruppe uneindeutig sein. | | |
| `description` | Zeichenfolge | Beschreibung des Arbeitsbereichs | | |
| `tags` | Objekt (object) | Wörterbuch der Tags für den Arbeitsbereich | | |
| `location` | Zeichenfolge | Standort des Arbeitsbereichs. Wenn keine Angabe erfolgt, wird standardmäßig der Standort der Ressourcengruppe verwendet. | | |
| `resource_group` | Zeichenfolge | **Erforderlich.** Die Ressourcengruppe mit dem Arbeitsbereich. Wenn die Ressourcengruppe nicht vorhanden ist, wird eine neue erstellt. | | |
| `hbi_workspace` | boolean | Gibt an, ob die Kundendaten erhebliche Geschäftsauswirkungen (High Business Impact, HBI) haben und vertrauliche Geschäftsinformationen enthalten. Weitere Informationen finden Sie unter [Datenverschlüsselung ruhender Daten](concept-data-encryption.md#encryption-at-rest). | | `false` |
| `storage_account` | Zeichenfolge | Die vollqualifizierte Ressourcen-ID eines vorhandenen Azure Storage-Kontos, das als Standardspeicherkonto für den Arbeitsbereich verwendet werden soll. Ein Speicherkonto mit Storage Premium oder hierarchischem Namespace kann nicht als Standardspeicherkonto verwendet werden. Wenn keine Angabe erfolgt, wird ein neues Speicherkonto erstellt. | | |
| `container_registry` | Zeichenfolge | Die vollqualifizierte Ressourcen-ID einer vorhandenen Azure-Containerregistrierung, die als Standardcontainerregistrierung für den Arbeitsbereich verwendet werden soll. Azure ML verwendet Azure Container Registry (ACR) zum Verwalten von Containerimages, die für Training und Bereitstellung verwendet werden. Wenn keine Angabe erfolgt, wird eine neue Containerregistrierung erstellt. Da die Erstellung verzögert geladen wird, wird die Containerregistrierung erst erstellt, wenn sie zum ersten Mal für einen Trainings- oder Bereitstellungsvorgang benötigt wird. | | |
| `key_vault` | Zeichenfolge | Die vollqualifizierte Ressourcen-ID eines vorhandenen Azure-Schlüsseltresors, der als Standardschlüsseltresor für den Arbeitsbereich verwendet werden soll. Wenn keine Angabe erfolgt, wird ein neuer Schlüsseltresor erstellt. | | |
| `application_insights` | Zeichenfolge | Die vollqualifizierte Ressourcen-ID einer vorhandenen Azure Application Insights-Instanz, die als Standardinstanz für den Arbeitsbereich verwendet werden soll. Wenn keine Angabe erfolgt, wird eine neue Application Insights-Instanz erstellt. | | |
| `customer_managed_key` | Objekt (object) | Azure Machine Learning speichert Metadaten in einer Azure Cosmos DB-Instanz. Standardmäßig werden ruhende Daten mit von Microsoft verwalteten Schlüsseln verschlüsselt. Wenn Sie Ihren eigenen kundenseitig verwalteten Schlüssel für die Verschlüsselung verwenden möchten, geben Sie in diesem Abschnitt die Informationen zum kundenseitig verwalteten Schlüssel an. Weitere Informationen finden Sie unter [Datenverschlüsselung für Azure Cosmos DB](concept-data-encryption.md#azure-cosmos-db). | | |
| `customer_managed_key.key_vault` | Zeichenfolge | Die vollqualifizierte Ressourcen-ID des Schlüsseltresors mit dem kundenseitig verwalteten Schlüssel. Dieser Schlüsseltresor kann sich von dem in `key_vault` angegebenen Standardschlüsseltresor für den Arbeitsbereich unterscheiden.| | |
| `customer_managed_key.key_uri` | Zeichenfolge | Der Schlüssel-URI des kundenseitig verwalteten Schlüssels zum Verschlüsseln ruhender Daten. Das URI-Format lautet `https://<keyvault-dns-name>/keys/<key-name>/<key-version>`. | | |
| `image_build_compute` | Zeichenfolge | Der Name des Computeziels, das zum Erstellen von Docker-Umgebungsimages verwendet werden soll, wenn sich die Containerregistrierung hinter einem VNet befindet. Weitere Informationen finden Sie unter [Schützen von Arbeitsbereichsressourcen hinter VNets](how-to-secure-workspace-vnet.md#enable-azure-container-registry-acr). | | |
| `public_network_access` | Zeichenfolge | Gibt an, ob der Zugriff auf öffentliche Endpunkte zulässig ist, wenn der Arbeitsbereich Private Link verwendet. Weitere Informationen finden Sie unter [Aktivieren des öffentlichen Zugriffs in VNets](how-to-configure-private-link.md#enable-public-access). | `enabled`, `disabled` | `disabled` |

## <a name="remarks"></a>Hinweise

Mit dem Befehl `az ml workspace` können Sie Azure Machine Learning-Arbeitsbereiche verwalten.

## <a name="examples"></a>Beispiele

Beispiele sind im [GitHub-Repository Beispiele](https://github.com/Azure/azureml-examples/tree/main/cli/resources/workspace) verfügbar. Im Folgenden sind mehrere aufgeführt.

## <a name="yaml-basic"></a>YAML: Basic

:::code language="yaml" source="~/azureml-examples-main/cli/resources/workspace/basic.yml":::

## <a name="yaml-with-existing-resources"></a>YAML: mit vorhandenen Ressourcen

:::code language="yaml" source="~/azureml-examples-main/cli/resources/workspace/with-existing-resources.yml":::

## <a name="yaml-customer-managed-key"></a>YAML: kundenseitig verwalteter Schlüssel

:::code language="yaml" source="~/azureml-examples-main/cli/resources/workspace/cmk.yml":::

## <a name="yaml-private-link"></a>YAML: Private Link

:::code language="yaml" source="~/azureml-examples-main/cli/resources/workspace/privatelink.yml":::

## <a name="yaml-high-business-impact"></a>YAML: erhebliche Geschäftsauswirkungen

:::code language="yaml" source="~/azureml-examples-main/cli/resources/workspace/hbi.yml":::

## <a name="next-steps"></a>Nächste Schritte

- [Installieren und Verwenden der CLI (V2)](how-to-configure-cli.md)
