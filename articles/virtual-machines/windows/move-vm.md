---
title: Verschieben einer Windows-VM-Ressource in Azure
description: Verschieben Sie einen virtuellen Windows-Computer im Resource Manager-Bereitstellungsmodell in ein anderes Azure-Abonnement oder in eine andere Ressourcengruppe.
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/03/2019
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 855045625467963d03e2aca57317b51ff20cc1aa
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697912"
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Verschieben eines virtuellen Windows-Computers in ein anderes Azure-Abonnement oder in eine andere Ressourcengruppe

**Gilt für**: :heavy_check_mark: Windows VMs

In diesem Artikel wird beschrieben, wie Sie einen virtuellen Windows-Computer (VM) zwischen Ressourcengruppen oder Abonnements verschieben. Das Verschieben zwischen Abonnements kann hilfreich sein, wenn Sie einen virtuellen Computer ursprünglich in einem persönlichen Abonnement erstellt haben und ihn nun in das Abonnement Ihres Unternehmens verschieben möchten, um weiterarbeiten zu können. Sie müssen den virtuellen Computer nicht anhalten, um ihn zu verschieben, und er sollte auch während des Verschiebens weiter ausgeführt werden.

> [!IMPORTANT]
>Im Rahmen der Verschiebung werden neue Ressourcen-IDs erstellt. Nach dem Verschieben des virtuellen Computers müssen Sie Ihre Tools und Skripts aktualisieren, damit die neuen Ressourcen-IDs verwendet werden.
>
>

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>Verschieben eines virtuellen Computers mithilfe von PowerShell

Wenn Sie einen virtuellen Computer in eine andere Ressourcengruppe verschieben möchten, müssen Sie auch alle abhängigen Ressourcen verschieben. Um eine Liste mit der Ressourcen-ID jeder dieser Ressourcen zu erhalten, verwenden Sie das Cmdlet [Get-AzResource](/powershell/module/az.resources/get-azresource).

```azurepowershell-interactive
 Get-AzResource -ResourceGroupName myResourceGroup | Format-table -wrap -Property ResourceId
```

Sie können die Ausgabe des vorherigen Befehls verwenden, um eine durch Trennzeichen getrennte Liste von Ressourcen-IDs für [Move-AzResource](/powershell/module/az.resources/move-azresource) zu erstellen, um jede Ressource in das Ziel zu verschieben.

```azurepowershell-interactive
Move-AzResource -DestinationResourceGroupName "myDestinationResourceGroup" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```

Schließen Sie den Parameter **-DestinationSubscriptionId** ein, um Ressourcen in ein anderes Abonnement zu verschieben.

```azurepowershell-interactive
Move-AzResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


Wenn Sie aufgefordert werden, zu bestätigen, dass die angegebene Ressource verschoben werden soll, geben Sie **J** ein.

## <a name="next-steps"></a>Nächste Schritte
Sie können viele verschiedene Arten von Ressourcen zwischen Ressourcengruppen und Abonnements verschieben. Weitere Informationen finden Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md).    
