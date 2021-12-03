---
title: Erstellen einer Skalierungsgruppe aus einem spezialisierten Image
description: Erstellen Sie eine Skalierungsgruppe mit einem spezialisierten Image in einer Azure Compute Gallery.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: mimckitt
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e881c400eeaea9202e5ef32cee7900d8065f8051
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131459955"
---
# <a name="create-a-scale-set-from-a-specialized-image-using-powershell"></a>Erstellen einer Skalierungsgruppe aus einem spezialisierten Image mit PowerShell 

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows VMs :heavy_check_mark: Einheitliche Skalierungsgruppen

Erstellen Sie mit Azure PowerShell eine VM aus einer spezialisierten Imageversion, die in einer [Azure Compute Gallery](../virtual-machines/shared-image-galleries.md) gespeichert ist. Wenn Sie eine Skalierungsgruppe mit einer generalisierten Imageversion erstellen möchten, finden Sie weitere Informationen unter [Erstellen von Skalierungsgruppeninstanzen aus einer generalisierten Imageversion](instance-generalized-image-version-powershell.md).

Nachdem Sie ein spezialisiertes Image in Ihrem Katalog erstellt haben, können Sie mithilfe des Cmdlets [New-AzVmss](/powershell/module/az.compute/new-azvmss) eine VM-Skalierungsgruppe erstellen. 

In diesem Beispiel verwenden wir die Imagedefinitions-ID, um sicherzustellen, dass Ihre neue VM die neueste Version eines Images verwendet. Sie können auch eine bestimmte Version verwenden, indem Sie die Imageversions-ID für `-ImageReferenceId` verwenden. Um beispielsweise die Imageversion *1.0.0* zu verwenden, geben Sie ein: `-ImageReferenceId "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"`. 

Beachten Sie, dass die Verwendung einer bestimmten Imageversion bedeutet, dass die Automatisierung fehlschlagen kann, wenn diese bestimmte Imageversion nicht verfügbar ist, weil sie gelöscht oder aus der Region entfernt wurde. Es wird empfohlen, die Imagedefinitions-ID zum Erstellen der neuen VM zu verwenden, es sei denn, eine bestimmte Imageversion ist erforderlich.

In den folgenden Beispielen wird eine Skalierungsgruppe mit dem Namen *myScaleSet* in der Ressourcengruppe *myVMSSRG* am Speicherort *SouthCentralUS* erstellt. Die Skalierungsgruppe wird aus dem Image *myImageDefinition* im Imagekatalog *myGallery* in der Ressourcengruppe *myGalleryRG* erstellt. Wenn Sie dazu aufgefordert werden, legen Sie eigene Administratoranmeldeinformationen für die VM-Instanzen in der Skalierungsgruppe fest.



```azurepowershell-interactive
# Get the image definition

$imageDefinition = Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myGalleryRG `
   -Name myImageDefinition
   
# Define variables for the scale set
$resourceGroupName = "myVMSSRG"
$scaleSetName = "myScaleSet"
$location = "South Central US"

# Create a resource group
New-AzResourceGroup -ResourceGroupName $resourceGroupName -Location $location

# Create a networking pieces
$subnet = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.0.0/24
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $resourceGroupName `
  -Name "myVnet" `
  -Location $location `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnet
$publicIP = New-AzPublicIpAddress `
  -ResourceGroupName $resourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -Name "myPublicIP"
$frontendIP = New-AzLoadBalancerFrontendIpConfig `
  -Name "myFrontEndPool" `
  -PublicIpAddress $publicIP
$backendPool = New-AzLoadBalancerBackendAddressPoolConfig -Name "myBackEndPool"
$inboundNATPool = New-AzLoadBalancerInboundNatPoolConfig `
  -Name "myRDPRule" `
  -FrontendIpConfigurationId $frontendIP.Id `
  -Protocol TCP `
  -FrontendPortRangeStart 50001 `
  -FrontendPortRangeEnd 50010 `
  -BackendPort 3389
# Create the load balancer and health probe
$lb = New-AzLoadBalancer `
  -ResourceGroupName $resourceGroupName `
  -Name "myLoadBalancer" `
  -Location $location `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool `
  -InboundNatPool $inboundNATPool
Add-AzLoadBalancerProbeConfig -Name "myHealthProbe" `
  -LoadBalancer $lb `
  -Protocol TCP `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
Add-AzLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol TCP `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe (Get-AzLoadBalancerProbeConfig -Name "myHealthProbe" -LoadBalancer $lb)
Set-AzLoadBalancer -LoadBalancer $lb

# Create IP address configurations
$ipConfig = New-AzVmssIpConfig `
  -Name "myIPConfig" `
  -LoadBalancerBackendAddressPoolsId $lb.BackendAddressPools[0].Id `
  -LoadBalancerInboundNatPoolsId $inboundNATPool.Id `
  -SubnetId $vnet.Subnets[0].Id

# Create a configuration 
$vmssConfig = New-AzVmssConfig `
    -Location $location `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode "Automatic"

# Reference the image version
Set-AzVmssStorageProfile $vmssConfig `
  -OsDiskCreateOption "FromImage" `
  -ImageReferenceId $imageDefinition.Id

# Complete the configuration
 
Add-AzVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name "network-config" `
  -Primary $true `
  -IPConfiguration $ipConfig

# Create the scale set 
New-AzVmss `
  -ResourceGroupName $resourceGroupName `
  -Name $scaleSetName `
  -VirtualMachineScaleSet $vmssConfig
```

Die Erstellung und Konfiguration aller Ressourcen und virtuellen Computer der Skalierungsgruppe dauert einige Minuten.

## <a name="next-steps"></a>Nächste Schritte
[Azure Image Builder (Vorschauversion)](../virtual-machines/image-builder-overview.md) hilft beim Automatisieren der Erstellung von Imageversionen. Sie können den Dienst sogar zum Aktualisieren und [Erstellen einer neuen Imageversion aus einer vorhandenen](../virtual-machines/linux/image-builder-gallery-update-image-version.md) verwenden.  

Sie können auch mithilfe von Vorlagen eine Ressource in der Azure Compute Gallery erstellen. Es stehen mehrere Azure-Schnellstartvorlagen zur Verfügung: 

- [Erstellen einer Azure Compute Gallery](https://azure.microsoft.com/resources/templates/sig-create/)
- [Erstellen einer Imagedefinition in einer Azure Compute Gallery](https://azure.microsoft.com/resources/templates/sig-image-definition-create/)
- [Erstellen einer Imageversion in einer Azure Compute Gallery](https://azure.microsoft.com/resources/templates/sig-image-version-create/)

Weitere Informationen zu Katalogen mit geteilten Images finden Sie in der [Übersicht](../virtual-machines/shared-image-galleries.md). Sollten Probleme auftreten, sehen Sie unter [Problembehandlung für Kataloge mit freigegebenen Images](../virtual-machines/troubleshooting-shared-images.md) nach.
