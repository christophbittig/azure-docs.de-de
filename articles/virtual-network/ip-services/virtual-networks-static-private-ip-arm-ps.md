---
title: 'Erstellen eines virtuellen Computers mit einer statischen privaten IP-Adresse: Azure PowerShell'
description: Erfahren Sie, wie Sie eine VM mit einer statischen privaten IP-Adresse über Azure PowerShell erstellen.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 10/01/2021
ms.custom: template-how-to
ms.openlocfilehash: a98fab6f07e2616e8f3732f28446c24e9051bf09
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368167"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-azure-powershell"></a>Erstellen einer VM mit einer statischen privaten IP-Adresse über Azure PowerShell

Einem virtuellen Computer (VM) wird automatisch eine private IP-Adresse aus einem Bereich zugewiesen, den Sie angeben. Dieser Bereich basiert auf dem Subnetz, in dem der virtuelle Computer bereitgestellt wird. Der virtuelle Computer behält die Adresse bei, bis er gelöscht wird. Azure weist dynamisch die nächste verfügbare private IP-Adresse aus dem Subnetz zu, in dem Sie einen virtuellen Computer erstellen. Weisen Sie der VM eine statische IP-Adresse zu, wenn Sie eine bestimmte IP-Adresse im Subnetz wünschen.

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

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

Erstellen Sie eine VM mithilfe von [New-AzVM](/powershell/module/az.compute/new-azvm). 

Mit dem folgenden Befehl wird ein virtueller Windows Server-Computer erstellt. Geben Sie bei entsprechender Aufforderung einen Benutzernamen und ein Kennwort als Anmeldeinformationen für den virtuellen Computer ein:

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

## <a name="change-private-ip-address-to-static"></a>Ändern der privaten IP-Adresse in statisch

In diesem Abschnitt ändern Sie die private IP-Adresse für den zuvor erstellten virtuellen Computer von **dynamisch** in **statisch**. 

Verwenden Sie [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork), um die Konfiguration des virtuellen Netzwerks in eine Variable einzufügen. Verwenden Sie [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig), um die Subnetzkonfiguration in eine Variable einzufügen. Verwenden Sie [Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface), um die Netzwerkschnittstellenkonfiguration abzurufen und in eine Variable einzufügen. Verwenden Sie [Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig), um die Konfiguration der Netzwerkschnittstelle festzulegen. Verwenden Sie abschließend [Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface), um die Konfiguration für die VM festzulegen.

Mit dem folgenden Befehl wird die private IP-Adresse des virtuellen Computers in statisch geändert:

```azurepowershell-interactive
## Place virtual network configuration into a variable. ##
$net = @{
    Name = 'myVM'
    ResourceGroupName = 'myResourceGroup'
}
$vnet = Get-AzVirtualNetwork @net

## Place subnet configuration into a variable. ##
$sub = @{
    Name = 'myVM'
    VirtualNetwork = $vnet
}
$subnet = Get-AzVirtualNetworkSubnetConfig @sub

## Get name of network interface and place into a variable ##
$int1 = @{
    Name = 'myVM'
    ResourceGroupName = 'myResourceGroup'
}
$vm = Get-AzVM @int1

## Place network interface configuration into a variable. ##
$nic = Get-AzNetworkInterface -ResourceId $vm.NetworkProfile.NetworkInterfaces.Id

## Set interface configuration. ##
$config =@{
    Name = 'myVM'
    PrivateIpAddress = '192.168.1.4'
    Subnet = $subnet
}
$nic | Set-AzNetworkInterfaceIpConfig @config -Primary

## Save interface configuration. ##
$nic | Set-AzNetworkInterface
```

> [!WARNING]
> Sie können dem Betriebssystem zwar Einstellungen für private IP-Adressen hinzufügen, dies wird jedoch erst nach dem Lesen des Artikels [Hinzufügen einer privaten IP-Adresse zu einem Betriebssystem](virtual-network-network-interface-addresses.md#private) empfohlen.

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