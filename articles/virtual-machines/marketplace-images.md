---
title: Angeben von Marketplace-Erwerbsplaninformationen mithilfe von Azure PowerShell
description: Erfahren Sie, wie Sie Azure Marketplace-Kaufplandetails angeben, wenn Sie Images in einer Azure Compute Gallery (früher als Shared Image Gallery bezeichnet) erstellen.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/07/2020
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d50aa76f80205df1d34215a020691c51ca58d4b0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131474608"
---
# <a name="supply-azure-marketplace-purchase-plan-information-when-creating-images"></a>Bereitstellen von Azure Marketplace-Erwerbsplaninformationen beim Erstellen von Images

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Wenn Sie ein Image in einem Katalog für freigegebenen Images erstellen und dabei eine Quelle verwenden, die ursprünglich aus einem Azure Marketplace-Image erstellt wurde, müssen Sie möglicherweise die Erwerbsplaninformationen nachverfolgen. In diesem Artikel wird erläutert, wie Sie Erwerbsplaninformationen für eine VM suchen und diese dann beim Erstellen einer Imagedefinition verwenden. Es wird ebenfalls beschrieben, wie Sie die Informationen aus der Imagedefinition verwenden, um die Bereitstellung der Erwerbsplaninformationen beim Erstellen einer VM für ein Image zu vereinfachen.

Weitere Informationen zum Suchen und Verwenden von Marketplace-Images finden Sie unter [Suchen und Verwenden von Azure Marketplace-Images](./windows/cli-ps-findimage.md).


## <a name="get-the-source-vm-information"></a>Abrufen der Informationen zur Quell-VM
Wenn Sie noch über die ursprüngliche VM verfügen, können Sie Informationen zum Plannamen, Herausgeber und dem Produkt mithilfe von „Get-AzVM“ von dieser VM abrufen. Dieses Beispiel ruft eine VM namens *myVM* in der Ressourcengruppe *myResourceGroup* ab und zeigt dann die Erwerbsplaninformationen für die VM an.

```azurepowershell-interactive
$vm = Get-azvm `
   -ResourceGroupName myResourceGroup `
   -Name myVM
$vm.Plan
```

## <a name="create-the-image-definition"></a>Erstellen der Imagedefinition

Rufen Sie den Katalog ab, den Sie zum Speichern des Images verwenden möchten. Sie können zuerst aller Kataloge auflisten.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

Erstellen Sie dann Variablen für den Katalog, den Sie verwenden möchten. In diesem Beispiel erstellen wir die Variable `$gallery` für *myGallery* in der Ressourcengruppe *myGalleryRG*.

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myGalleryRG
```

Erstellen Sie mithilfe der Parameter `-PurchasePlanPublisher`, `-PurchasePlanProduct` und `-PurchasePlanName` die Imagedefinition.

```azurepowershell-interactive
 $imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Linux `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU' `
   -PurchasePlanPublisher $vm.Plan.Publisher `
   -PurchasePlanProduct $vm.Plan.Product `
   -PurchasePlanName  $vm.Plan.Name
```

Erstellen Sie dann Ihre [Imageversion](image-version.md), indem Sie [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion) verwenden.  


## <a name="create-the-vm"></a>Erstellen des virtuellen Computers

Wenn Sie die VM aus dem Image erstellen, können Sie die Informationen aus der Imagedefinition verwenden, um mithilfe von [Set-AzVMPlan](/powershell/module/az.compute/set-azvmplan) die Herausgeberinformationen zu übergeben.


```azurepowershell-interactive
# Create some variables for the new VM.
$resourceGroup = "mySIGPubVM"
$location = "West Central US"
$vmName = "mySIGPubVM"

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create the network resources.
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet `
   -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name MYvNET `
   -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -Name "mypublicdns$(Get-Random)" `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig `
   -Name myNetworkSecurityGroupRuleRDP  `
   -Protocol Tcp `
   -Direction Inbound `
   -Priority 1000 `
   -SourceAddressPrefix * `
   -SourcePortRange * `
   -DestinationAddressPrefix * `
   -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name myNetworkSecurityGroup `
   -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface `
   -Name $vmName `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using Set-AzVMSourceImage -Id $imageDefinition.Id to use the latest available image version. Set-AZVMPlan is used to pass the plan information in for the VM.

$vmConfig = New-AzVMConfig `
   -VMName $vmName `
   -VMSize Standard_D1_v2   | `
   Set-AzVMSourceImage -Id $imageDefinition.Id | `
   Set-AzVMPlan `
     -Publisher $imageDefinition.PurchasePlan.Publisher `
     -Product $imageDefinition.PurchasePlan.Product `
     -Name $imageDefinition.PurchasePlan.Name | `
   Add-AzVMNetworkInterface -Id $nic.Id

# Create the virtual machine
New-AzVM `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -VM $vmConfig
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Suchen und Verwenden von Marketplace-Images finden Sie unter [Suchen und Verwenden von Azure Marketplace-Images](./windows/cli-ps-findimage.md).
