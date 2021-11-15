---
title: An die CLI (v2) angefügtes Virtual Machine YAML-Schema
titleSuffix: Azure Machine Learning
description: Referenzdokumentation für das an die CLI (v2) angefügte Virtual Machine-Schema.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: b639c4193a246ccf7f57520089132f0f49d16ebb
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132062432"
---
# <a name="cli-v2-attached-virtual-machine-yaml-schema"></a>An die CLI (v2) angefügtes Virtual Machine YAML-Schema

Das JSON-Quellschema finden Sie unter https://azuremlschemas.azureedge.net/latest/vmCompute.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML-Syntax

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte | Standardwert |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | Zeichenfolge | Das YAML-Schema. Wenn Sie die Azure Machine Learning VS Code-Erweiterung verwenden, um die YAML-Datei zu erstellen, können Sie durch Einfügen von `$schema` am Anfang der Datei Schema- und Ressourcenvervollständigungen aufrufen. | | |
| `type` | Zeichenfolge | **Erforderlich.** Der Computetyp. | `virtualmachine` | |
| `name` | Zeichenfolge | **Erforderlich.** Der Computename. | | |
| `description` | Zeichenfolge | Die Computebeschreibung. | | |
| `resource_id` | Zeichenfolge | **Erforderlich.** Vollqualifizierte Ressourcen-ID des virtuellen Azure-Computers, der als Computeziel an den Arbeitsbereich angefügt werden soll. | | |
| `ssh_settings` | Objekt (object) | Die SSH-Einstellungen zum Herstellen einer Verbindung mit dem virtuellen Computer. | | |
| `ssh_settings.admin_username` | Zeichenfolge | Der Name des Administratorbenutzerkontos, das für die SSH-Verbindung mit dem virtuellen Computer verwendet werden kann. | | |
| `ssh_settings.admin_password` | Zeichenfolge | Das Kennwort des Administratorbenutzerkontos. **Entweder `admin_password` oder `ssh_private_key_file` ist erforderlich.** | | |
| `ssh_settings.ssh_private_key_file` | Zeichenfolge | Der lokale Pfad zur Datei mit dem privaten SSH-Schlüssel des Administratorbenutzerkontos. **Entweder `admin_password` oder `ssh_private_key_file` ist erforderlich.** | | |
| `ssh_settings.ssh_port` | integer | Der SSH-Port auf dem virtuellen Computer. | | `22` |

## <a name="remarks"></a>Hinweise

Der Befehl `az ml compute` kann zur Verwaltung von virtuellen Computern (VM) verwendet werden, die an einen Azure Machine Learning-Arbeitsbereich angefügt sind.

## <a name="examples"></a>Beispiele

Beispiele finden Sie im [GitHub-Beispielrepository](https://github.com/Azure/azureml-examples/tree/main/cli/resources/compute). Im Folgenden sind mehrere aufgeführt.

## <a name="yaml-basic"></a>YAML: Grundlagen

:::code language="yaml" source="~/azureml-examples-main/cli/resources/compute/vm-attach.yml":::

## <a name="next-steps"></a>Nächste Schritte

- [Installieren und Verwenden der CLI (V2)](how-to-configure-cli.md)
