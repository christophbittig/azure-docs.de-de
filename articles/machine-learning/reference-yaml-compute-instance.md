---
title: CLI (v2) Berechnungsinstanz YAML-Schema
titleSuffix: Azure Machine Learning
description: Referenzdokumentation für das CLI (v2) Kompute Instance YAML Schema.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: ff13936785dc48fc8fb0c4b26a55651f0e7f827c
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566496"
---
# <a name="cli-v2-compute-instance-yaml-schema"></a>CLI (v2) Berechnungsinstanz YAML-Schema

Das JSON-Quellschema finden Sie unter https://azuremlschemas.azureedge.net/latest/computeInstance.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML-Syntax

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte | Standardwert |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | Zeichenfolge | Das YAML-Schema. Wenn Sie die Azure Machine Learning VS Code-Erweiterung verwenden, um die YAML-Datei zu erstellen, können Sie durch Einfügen von `$schema` am Anfang der Datei Schema- und Ressourcenvervollständigungen aufrufen. | | |
| `type` | Zeichenfolge | **Erforderlich.** Die Art des Kompute. | `computeinstance` | |
| `name` | Zeichenfolge | **Erforderlich.** Name des Kompute. | | |
| `description` | Zeichenfolge | Beschreibung des Kompute. | | |
| `size` | Zeichenfolge | Die für die Kompute-instanz zu verwendende VM-Größe. Weitere Informationen finden Sie unter [Unterstützte VM-Serien und Größen](concept-compute-target.md#supported-vm-series-and-sizes). Beachten Sie, dass nicht alle Größen in allen Regionen verfügbar sind. | Für die Liste der unterstützten Größen in einer bestimmten Region verwenden Sie bitte den Befehl `az ml compute list-sizes`.  | `Standard_DS3_v2` |
| `create_on_behalf_of` | Objekt | Einstellungen für die Erstellung der Kompute-Instanz im Namen eines anderen Benutzers. Bitte stellen Sie sicher, dass der zugewiesene Benutzer über die richtigen RBAC-Berechtigungen verfügt. |  |  |
| `create_on_behalf_of.user_tenant_id` | Zeichenfolge | Die AAD-Mieter-ID des zugewiesenen Benutzers. |  |  |
| `create_on_behalf_of.user_object_id` | Zeichenfolge | Die AAD-Objekt-ID des zugewiesenen Benutzers. |  |  |
| `ssh_public_access_enabled` | boolean | Ob der öffentliche SSH-Zugang auf der Kompute-Instanz aktiviert werden soll. | | `false` |
| `ssh_settings` | Objekt | SSH-Einstellungen für die Verbindung mit der Kompute-Instanz. | | |
| `ssh_settings.ssh_key_value` | Zeichenfolge | Der öffentliche SSH-Schlüssel des Administrator-Benutzerkontos. | | |
| `network_settings` | Objekt | Einstellungen zur Netzwerksicherheit. | | |
| `network_settings.vnet_name` | Zeichenfolge | Name des virtuellen Netzes (VNet) bei der Erstellung eines neuen Netzes oder beim Verweis auf ein bestehendes Netz. | | |
| `network_settings.subnet` | Zeichenfolge | Entweder der Name des Subnetzes, wenn ein neues VNet erstellt oder auf ein bestehendes verwiesen wird, oder die vollständig qualifizierte Ressourcen-ID eines Subnetzes in einem bestehenden VNet. Geben Sie `network_settings.vnet_name` nicht an, wenn die Subnetz-ID angegeben ist. Die Subnetz-ID kann sich auf ein VNet/Subnetz in einer anderen Ressourcengruppe beziehen. | | |

## <a name="remarks"></a>Bemerkungen

Der Befehl `az ml compute` kann für die Verwaltung von Azure Machine Learning Compute-Instanzen verwendet werden.

## <a name="yaml-minimal"></a>YAML: minimal

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/compute/instance-minimal.yml":::

## <a name="yaml-basic"></a>YAML: grundlegend

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/compute/instance-basic.yml":::

## <a name="next-steps"></a>Nächste Schritte

- [Installieren und Verwenden der CLI (V2)](how-to-configure-cli.md)
