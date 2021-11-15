---
title: CLI (v2) Computecluster (AmlCompute) YAML-Schema
titleSuffix: Azure Machine Learning
description: Referenzdokumentation zum CLI (v2) Computecluster (AmlCompute) YAML-Schema.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: eac97f87de7edafc45f0e434188579df19d99c36
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132057609"
---
# <a name="cli-v2-compute-cluster-amlcompute-yaml-schema"></a>CLI (v2) Computecluster (AmlCompute) YAML-Schema

Das JSON-Quellschema finden Sie unter https://azuremlschemas.azureedge.net/latest/amlCompute.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML-Syntax

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte | Standardwert |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | Zeichenfolge | Das YAML-Schema. Wenn Sie die Azure Machine Learning VS Code-Erweiterung verwenden, um die YAML-Datei zu erstellen, können Sie durch Einfügen von `$schema` am Anfang der Datei Schema- und Ressourcenvervollständigungen aufrufen. | | |
| `type` | Zeichenfolge | **Erforderlich.** Der Computetyp. | `amlcompute` | |
| `name` | Zeichenfolge | **Erforderlich.** Computename. | | |
| `description` | Zeichenfolge | Computebeschreibung. | | |
| `location` | Zeichenfolge | Der Computespeicherort. Wenn nicht angegeben, wird standardmäßig der Speicherort des Arbeitsbereichs verwendet. | | |
| `size` | Zeichenfolge | Die VM-Größe, die für den Cluster verwendet werden soll. Weitere Informationen finden Sie unter [Unterstützte VM-Serien und Größen](concept-compute-target.md#supported-vm-series-and-sizes). Beachten Sie, dass nicht alle Größen in allen Regionen verfügbar sind. | Für die Liste der unterstützten Größen in einer bestimmten Region verwenden Sie bitte `az ml compute list-sizes`.  | `Standard_DS3_v2` |
| `tier` | Zeichenfolge | Die VM-Prioritätsebene, die für den Cluster verwendet werden soll. VMs mit niedriger Priorität sind präemptiv, aber im Vergleich zu dedizierten VMs zu geringeren Kosten. | `dedicated`, `low_priority` | `dedicated` |
| `min_instances` | integer | Die Mindestanzahl von Knoten, die im Cluster verwendet werden. Wenn Sie die Mindestanzahl von Knoten auf `0` festlegen, kann Azure ML den Cluster automatisch auf null Knoten herunterskalieren, wenn er nicht verwendet wird. Bei jedem Wert größer als `0` wird die entsprechende Anzahl von Knoten weiter ausgeführt, auch wenn der Cluster nicht verwendet wird. | | `0` |
| `max_instances` | integer | Die maximale Anzahl von Knoten, die im Cluster verwendet werden. | | `1` |
| `idle_time_before_scale_down` | integer | Die Leerlaufzeit des Knotens in Sekunden vor dem herunterskalieren des Clusters. | | `120` |
| `ssh_public_access_enabled` | boolean | Gibt an, ob der öffentliche SSH-Zugriff auf die Knoten des Clusters aktiviert werden soll. | | `false` |
| `ssh_settings` | Objekt (object) | SSH-Einstellungen zum Herstellen einer Verbindung mit dem Cluster. | | |
| `ssh_settings.admin_username` | Zeichenfolge | Der Name des Administratorbenutzerkontos, das für die SSH-Verbindung mit Knoten verwendet werden kann. | | |
| `ssh_settings.admin_password` | Zeichenfolge | Das Kennwort des Administratorbenutzerkontos. **Entweder `admin_password` oder `ssh_key_value` ist erforderlich.** | | |
| `ssh_settings.ssh_key_value` | Zeichenfolge | Der öffentliche SSH-Schlüssel des Administratorbenutzerkontos. **Entweder `admin_password` oder `ssh_key_value` ist erforderlich.** | | |
| `network_settings` | Objekt (object) | Einstellungen zur Netzwerksicherheit. | | |
| `network_settings.vnet_name` | Zeichenfolge | Name des virtuellen Netzes (VNet) bei der Erstellung eines neuen Netzes oder beim Verweis auf ein bestehendes Netz. | | |
| `network_settings.subnet` | Zeichenfolge | Entweder der Name des Subnetzes beim Erstellen eines neuen VNet oder das Verweisen auf ein vorhandenes VNet oder die vollqualifizierte Ressourcen-ID eines Subnetzes in einem bestehenden VNet. Geben Sie `network_settings.vnet_name` nicht an, wenn die Subnetz-ID angegeben ist. Die Subnetz-ID kann sich auf ein VNet/Subnetz in einer anderen Ressourcengruppe beziehen. | | |
| `identity` | Objekt (object) | Die Konfiguration der verwalteten Identität, die dem Compute zugewiesen werden soll. AmlCompute-Cluster unterstützen nur eine systemseitig zugewiesene Identität oder mehrere benutzerseitig zugewiesene Identitäten, nicht beides gleichzeitig. | | |
| `identity.type` | Zeichenfolge | Der Typ der verwalteten Identität, die dem Compute zugewiesen werden soll. Wenn der Typ `user_assigned` ist, muss auch die Eigenschaft `identity.user_assigned_identities` angegeben werden. | `system_assigned`, `user_assigned` | |
| `identity.user_assigned_identities` | array | Liste der vollqualifizierten Ressourcen-IDs der vom Benutzer zugewiesenen Identitäten. | | |

## <a name="remarks"></a>Hinweise

Die Befehle `az ml compute` können zum Verwalten von Azure Machine Learning Computeclustern (AmlCompute) verwendet werden.

## <a name="examples"></a>Beispiele

Beispiele sind im [GitHub-Repository Beispiele](https://github.com/Azure/azureml-examples/tree/main/cli/resources/compute) verfügbar. Im Folgenden sind mehrere aufgeführt.

## <a name="yaml-minimal"></a>YAML: minimal

:::code language="yaml" source="~/azureml-examples-main/cli/resources/compute/cluster-minimal.yml":::

## <a name="yaml-basic"></a>YAML: grundlegend

:::code language="yaml" source="~/azureml-examples-main/cli/resources/compute/cluster-basic.yml":::

## <a name="yaml-custom-location"></a>YAML: benutzerdefinierter Standort

:::code language="yaml" source="~/azureml-examples-main/cli/resources/compute/cluster-location.yml":::

## <a name="yaml-low-priority"></a>YAML: niedrige Priorität

:::code language="yaml" source="~/azureml-examples-main/cli/resources/compute/cluster-low-priority.yml":::

## <a name="yaml-ssh-username-and-password"></a>YAML: SSH-Benutzername und Kennwort

:::code language="yaml" source="~/azureml-examples-main/cli/resources/compute/cluster-ssh-password.yml":::

## <a name="next-steps"></a>Nächste Schritte

- [Installieren und Verwenden der CLI (V2)](how-to-configure-cli.md)
