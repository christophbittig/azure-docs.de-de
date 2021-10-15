---
title: Erstellen eines virtuellen Computers mit einer statischen öffentlichen IP-Adresse – Azure PowerShell
titlesuffix: Azure Virtual Network
description: Erstellen Sie einen virtuellen Computer mit einer statischen öffentlichen IP-Adresse mithilfe von Azure PowerShell. Statische öffentliche IP-Adressen sind Adressen, die sich nie ändern.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 10/01/2021
ms.custom: template-how-to
ms.openlocfilehash: 60c9d4827a83475b56abcc769bb1fa7f68467a24
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368233"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-azure-powershell"></a>Erstellen eines virtuellen Computers mit einer statischen öffentlichen IP-Adresse mithilfe von Azure PowerShell

In diesem Artikel erstellen Sie einen virtuellen Computer mit einer statischen öffentlichen IP-Adresse. Eine öffentliche IP-Adresse ermöglicht die Kommunikation mit einem virtuellen Computer über das Internet. Damit sichergestellt ist, dass sich die Adresse niemals ändert, weisen Sie anstelle einer dynamischen Adresse eine statische öffentliche IP-Adresse zu. 

Für öffentliche IP-Adressen fällt eine [geringe Gebühr](https://azure.microsoft.com/pricing/details/ip-addresses) an. Die Anzahl der öffentlichen IP-Adressen, die pro Abonnement verwendet werden können, ist [begrenzt](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell (lokal installiert) oder Azure Cloud Shell

Wenn Sie PowerShell lokal installieren und verwenden möchten, müssen Sie für diesen Artikel mindestens Version 5.4.1 des Azure PowerShell-Moduls verwenden. Führen Sie `Get-Module -ListAvailable Az` aus, um die installierte Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-Az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Erstellen Sie eine Ressourcengruppe mit [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) namens **myResourceGroup** am Speicherort **eastus2**.

```azurepowershell-interactive
$rg =@{
    Name = 'myResourceGroup'
    Location = 'eastus2'
}
New-AzResourceGroup @rg

```

## <a name="create-a-public-ip-address"></a>Erstellen einer öffentlichen IP-Adresse

Verwenden Sie [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress), um eine öffentliche Standard-IPv4-Adresse zu erstellen.

Mit dem folgenden Befehl wird eine zonenredundante öffentliche IP-Adresse mit dem Namen **myPublicIP** in **myResourceGroup** erstellt.

```azurepowershell-interactive
## Create IP. ##
$ip = @{
    Name = 'myPublicIP'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    Zone = 1,2,3   
}
New-AzPublicIpAddress @ip
```
## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

Erstellen Sie mit [New-AzVM](/powershell/module/az.Compute/new-azvm) einen virtuellen Computer. 

Mit dem folgenden Befehl wird ein virtueller Windows Server-Computer erstellt. Sie geben den Namen der zuvor erstellten öffentlichen IP-Adresse im Parameter **`-PublicIPAddressName`** ein. Geben Sie bei entsprechender Aufforderung einen Benutzernamen und ein Kennwort als Anmeldeinformationen für den virtuellen Computer ein:

```azurepowershell-interactive
## Create virtual machine. ##
$vm = @{
    ResourceGroupName = 'myResourceGroup'
    Location = 'East US 2'
    Name = 'myVM'
    PublicIpAddressName = 'myPublicIP'
}
New-AzVM @vm
```

Weitere Informationen zu SKUs für öffentliche IP-Adressen finden Sie unter [SKUs für öffentliche IP-Adressen](public-ip-addresses.md#sku). Ein virtueller Computer kann dem Back-End-Pool einer Azure Load Balancer-Instanz hinzugefügt werden. Die SKU der öffentlichen IP-Adresse muss mit der SKU der öffentlichen IP-Adresse eines Lastenausgleichs übereinstimmen. Weitere Informationen finden Sie unter [Azure-Lastenausgleichsmodul](../../load-balancer/skus.md).

Zeigen Sie die zugewiesene öffentliche IP-Adresse an, und bestätigen Sie mit [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress), dass sie als statische Adresse erstellt wurde:

```azurepowershell-interactive
## Retrieve public IP address settings. ##
$ip = @{
    Name = 'myPublicIP'
    ResourceGroupName = 'myResourceGroup'
}
Get-AzPublicIpAddress @ip | Select "IpAddress","PublicIpAllocationMethod" | Format-Table

```

> [!WARNING]
> Ändern Sie die IP-Adresseinstellungen nicht innerhalb des Betriebssystems des virtuellen Computers. Öffentliche Azure-IP-Adressen sind dem Betriebssystem nicht bekannt. Auch wenn Sie dem Betriebssystem Einstellungen für private IP-Adressen hinzufügen können, wird empfohlen, dies erst nach Lesen des Artikels [Hinzufügen einer privaten IP-Adresse zu einem Betriebssystem](virtual-network-network-interface-addresses.md#private) und bei absoluter Notwendigkeit zu tun.

[!INCLUDE [ephemeral-ip-note.md](../../../includes/ephemeral-ip-note.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcengruppe und alle darin enthaltenen Ressourcen nicht mehr benötigen, können Sie sie mit dem Befehl [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) entfernen:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie mehr über [öffentliche IP-Adressen](public-ip-addresses.md#public-ip-addresses) in Azure.
- Erfahren Sie mehr über alle [Einstellungen für öffentliche IP-Adressen](virtual-network-public-ip-address.md#create-a-public-ip-address).
- Lesen Sie mehr über [private IP-Adressen](private-ip-addresses.md) und das Zuweisen einer [statischen privaten IP-Adresse](virtual-network-network-interface-addresses.md#add-ip-addresses) zu einer Azure-VM.
- Erfahren Sie mehr über das Erstellen von VMs unter [Linux](../../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) und [Windows](../../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).