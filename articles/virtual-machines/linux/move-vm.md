---
title: Verschieben eines virtuellen Computers mit der Azure-Befehlszeilenschnittstelle
description: Verschieben Sie mit der Azure-Befehlszeilenschnittstelle einen virtuellen Computer in ein anderes Azure-Abonnement oder eine andere Ressourcengruppe.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/12/2018
ms.author: cynthn
ms.openlocfilehash: c2ceae3fbf1a59f5f148a488900e9bb88e6be4e4
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688279"
---
# <a name="move-a-vm-to-another-subscription-or-resource-group"></a>Verschieben eines virtuellen Computers in ein anderes Abonnement oder eine andere Ressourcengruppe

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

In diesem Artikel erfahren Sie, wie Sie einen virtuellen Computer (Virtual Machine, VM) zwischen Ressourcengruppen oder Abonnements verschieben. Das Verschieben einer VM zwischen Abonnements kann nützlich sein, wenn Sie eine VM in einem persönlichen Abonnement erstellt haben und sie nun in das Abonnement Ihres Unternehmens verschieben möchten.

> [!IMPORTANT]
>Im Rahmen der Verschiebung werden neue Ressourcen-IDs erstellt. Nach dem Verschieben des virtuellen Computers müssen Sie Ihre Tools und Skripts aktualisieren, damit die neuen Ressourcen-IDs verwendet werden.
>


## <a name="use-the-azure-cli-to-move-a-vm"></a>Verwenden der Azure-Befehlszeilenschnittstelle zum Verschieben einer VM


Bevor Sie Ihren virtuellen Computer mithilfe der Azure-Befehlszeilenschnittstelle verschieben, müssen Sie sich vergewissern, dass sich das Quellabonnement und das Zielabonnement im gleichen Mandanten befinden. Um zu überprüfen, ob beide Abonnements die gleiche Mandanten-ID aufweisen, verwenden Sie [az account show](/cli/azure/account).

```azurecli-interactive
az account show --subscription mySourceSubscription --query tenantId
az account show --subscription myDestinationSubscription --query tenantId
```
Wenn die Mandanten-IDs für das Quell- und das Zielabonnement nicht gleich sind, müssen Sie sich an den [Support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) wenden, um die Ressourcen in einen neuen Mandanten zu verschieben.

Um eine VM erfolgreich verschieben zu können, müssen Sie die VM und alle unterstützenden Ressourcen verschieben. Verwenden Sie den Befehl [azure resource list](/cli/azure/resource), um alle Ressourcen einer Ressourcengruppe und die dazugehörigen IDs aufzulisten. Es ist hilfreich, die Ausgabe dieses Befehls per Pipe-Vorgang an eine Datei zu übergeben, damit Sie die IDs später kopieren und in Befehle einfügen können.

```azurecli-interactive
az resource list --resource-group "mySourceResourceGroup" --query "[].{Id:id}" --output table
```
Die Ausgabe von `table` ist nicht verfügbar, wenn Sie `--interactive` verwenden. Ändern Sie die Ausgabe in eine andere Option wie `json`.

Verwenden Sie [az resource move](/cli/azure/resource), um einen virtuellen Computer und die dazugehörigen Ressourcen in eine andere Ressourcengruppe zu verschieben. Im folgenden Beispiel wird gezeigt, wie Sie eine VM und die wichtigsten Ressourcen dafür verschieben. Verwenden Sie den Parameter **-ids**, und übergeben Sie eine kommagetrennte Liste (ohne Leerstellen) mit IDs für die zu verschiebenden Ressourcen.

```azurecli-interactive
vm=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM
nic=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkInterfaces/myNIC
nsg=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG
pip=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIPAddress
vnet=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/virtualNetworks/myVNet
diag=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mydiagnosticstorageaccount
storage=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccountname    

az resource move \
    --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag \
    --destination-group "myDestinationResourceGroup"
```

Wenn Sie den virtuellen Computer und seine Ressourcen in ein anderes Abonnement verschieben möchten, fügen Sie den Parameter **--destination-subscriptionId** hinzu, um das Zielabonnement anzugeben.

Wenn Sie aufgefordert werden, zu bestätigen, dass die angegebene Ressource verschoben werden soll, geben Sie **J** ein.

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>Nächste Schritte
Sie können viele verschiedene Arten von Ressourcen zwischen Ressourcengruppen und Abonnements verschieben. Weitere Informationen finden Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md).    
