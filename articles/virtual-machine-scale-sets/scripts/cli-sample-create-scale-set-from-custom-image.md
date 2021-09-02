---
title: 'Azure CLI-Beispiele: Verwenden eines benutzerdefinierten VM-Images'
description: Dieses Azure CLI-Skript erstellt eine VM-Skalierungsgruppe mit einem benutzerdefinierten VM-Image als Quelle für die VM-Instanzen.
author: mamccrea
ms.author: mamccrea
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.date: 03/27/2018
ms.reviewer: cynthn
ms.custom: akjosh, devx-track-azurecli
ms.openlocfilehash: aaf58fafa757f0d601609682b70a5aa9946d6362
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123223551"
---
# <a name="create-a-virtual-machine-scale-set-from-a-custom-vm-image-with-the-azure-cli"></a>Erstellen einer VM-Skalierungsgruppe auf der Grundlage eines benutzerdefinierten VM-Images mithilfe der Azure CLI
Dieses Skript erstellt eine VM-Skalierungsgruppe mit einem benutzerdefinierten VM-Image als Quelle für die VM-Instanzen.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Beispielskript
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/use-custom-vm-image/use-custom-vm-image.sh "Create a virtual machine scale set with a custom VM image")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung
Führen Sie den folgenden Befehl aus, um die Ressourcengruppe, die Skalierungsgruppe und alle dazugehörigen Ressourcen zu entfernen.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Erläuterung des Skripts
In diesem Skript werden die folgenden Befehle verwendet, um eine Ressourcengruppe, eine VM-Skalierungsgruppe und alle dazugehörigen Ressourcen zu erstellen. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [az group create](/cli/azure/ad/group) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [az vmss create](/cli/azure/vmss) | Erstellt die VM-Skalierungsgruppe und verbindet sie mit dem virtuellen Netzwerk, dem Subnetz und der Netzwerksicherheitsgruppe. Des Weiteren wird ein Lastenausgleich erstellt, um Datenverkehr auf mehrere VM-Instanzen zu verteilen. Dieser Befehl gibt auch das zu verwendende VM-Image und die Administratoranmeldeinformationen an.  |
| [az group delete](/cli/azure/ad/group) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure/overview).
