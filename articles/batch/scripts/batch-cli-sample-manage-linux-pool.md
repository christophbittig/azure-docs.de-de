---
title: 'Azure CLI-Skriptbeispiel: Linux-Pool in Batch | Microsoft-Dokumentation'
description: Hier lernen Sie die verfügbaren Azure CLI-Befehle zum Erstellen und Verwalten eines Pools mit Linux-Computeknoten in Azure Batch kennen.
ms.topic: sample
ms.date: 09/17/2021
ms.custom: devx-track-azurecli, seo-azure-cli
keywords: Linux, Azure CLI-Beispiele, Azure CLI-Codebeispiele, Azure CLI-Skriptbeispiele
ms.openlocfilehash: 0c56a5baaaf107b82172b741a5407437cab777a6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128677775"
---
# <a name="cli-example-create-and-manage-a-linux-pool-in-azure-batch"></a>CLI-Beispiel: Erstellen und Verwalten eines Linux-Pools in Azure Batch

In diesem Skript werden einige der verfügbaren Azure CLI-Befehle zum Erstellen und Verwalten eines Pools mit Linux-Computeknoten in Azure Batch vorgestellt.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Für dieses Tutorial ist mindestens Version 2.0.20 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert. 

## <a name="example-script"></a>Beispielskript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/manage-pool/manage-pool-linux.sh "Manage Linux Virtual Machine Pool")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Führen Sie den folgenden Befehl aus, um die Ressourcengruppe und alle dazugehörigen Ressourcen zu entfernen:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Erstellt das Batch-Konto. |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Authentifiziert das angegebene Batch-Konto zur weiteren CLI-Interaktion.  |
| [az batch pool node-agent-skus list](../batch-linux-nodes.md#list-of-virtual-machine-images) | Listet verfügbare Knoten-Agent-SKUs und Imageinformationen auf.  |
| [az batch pool create](/cli/azure/batch/pool#az_batch_pool_create) | Erstellt einen Pool mit Computeknoten.  |
| [az batch pool resize](/cli/azure/batch/pool#az_batch_pool_resize) | Ändert die Größe der ausgeführten virtuellen Computer im angegebenen Pool.  |
| [az batch pool show](/cli/azure/batch/pool#az_batch_pool_show) | Zeigt die Eigenschaften eines Pools an.  |
| [az batch node list](/cli/azure/batch/node#az_batch_node_list) | Listet alle Computeknoten im angegebenen Pool auf.  |
| [az batch node reboot](/cli/azure/batch/node#az_batch_node_reboot) | Startet den angegebenen Computeknoten neu.  |
| [az batch node delete](/cli/azure/batch/node#az_batch_node_delete) | Löscht die aufgelisteten Knoten aus dem angegebenen Pool.  |
| [az group delete](/cli/azure/group#az_group_delete) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).
