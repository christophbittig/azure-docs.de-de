---
title: 'Azure CLI-Skriptbeispiel: Starten eines virtuellen Computers in einem Lab'
description: Dieses Azure CLI-Skript startet einen virtuellen Computer in einem Lab in Azure DevTest Labs.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: f28427c13587af7654f56fb728a44ea42cce5f5a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128557560"
---
# <a name="use-azure-cli-to-start-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>Starten eines virtuellen Computers in einem Lab in Azure DevTest Labs mithilfe der Azure CLI

Dieses Azure CLI-Skript startet einen virtuellen Computer in einem Lab. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Beispielskript

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/start-connect-virtual-machine-in-lab/start-connect-virtual-machine-in-lab.sh "Start a VM")]


## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle:

| Get-Help | Notizen |
|---|---|
| [az lab vm start](/cli/azure/lab/vm#az_lab_vm_start) | Startet einen virtuellen Computer (virtual machine, VM) in einem Lab. Dieser Vorgang kann eine Weile dauern. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).

Weitere CLI-Skriptbeispiele für Azure Lab Services finden Sie unter [Azure Lab Services – Beispiele für die Befehlszeilenschnittstelle](../samples-cli.md).
