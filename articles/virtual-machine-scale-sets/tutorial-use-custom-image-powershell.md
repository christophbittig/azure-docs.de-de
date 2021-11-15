---
title: 'Tutorial: Verwenden eines benutzerdefinierten VM-Images in einer Skalierungsgruppe mit Azure PowerShell'
description: Es wird beschrieben, wie Sie Azure PowerShell zum Erstellen eines benutzerdefinierten VM-Images verwenden, das Sie zum Bereitstellen einer VM-Skalierungsgruppe verwenden können.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: shared-image-gallery
ms.topic: tutorial
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: mimckitt
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1c0ba3a58906275d136a437f3b18d32e3fe70a24
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131456782"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-azure-powershell"></a>Tutorial: Erstellen und Verwenden eines benutzerdefinierten Images für VM-Skalierungsgruppen mit Azure PowerShell

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Einheitliche Skalierungsgruppen

Wenn Sie eine Skalierungsgruppe erstellen, geben Sie ein Image an, das beim Bereitstellen der VM-Instanzen verwendet wird. Sie können ein benutzerdefiniertes VM-Image verwenden, um die Anzahl von Aufgaben zu reduzieren, nachdem VM-Instanzen bereitgestellt wurden. Dieses benutzerdefinierte VM-Image enthält alle erforderlichen Anwendungsinstallationen oder -konfigurationen. Für alle VM-Instanzen, die in der Skalierungsgruppe erstellt werden, wird das benutzerdefinierte VM-Image verwendet, und die VM-Instanzen sind für die Bereitstellung Ihres Anwendungsdatenverkehrs bereit. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer Azure Compute Gallery-Instanz
> * Erstellen einer Imagedefinition
> * Erstellen einer Imageversion
> * Erstellen einer Skalierungsgruppe aus einem Image 
> * Freigeben eines Imagekatalogs

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="before-you-begin"></a>Voraussetzungen

In den folgenden Schritten wird erläutert, wie Sie eine vorhandene VM in ein wiederverwendbares benutzerdefiniertes Image umwandeln, mit dem Sie eine Skalierungsgruppe erstellen können.

Für das Beispiel in diesem Tutorial muss ein virtueller Computer vorhanden sein. Sehen Sie sich ggf. die [Schnellstartanleitung zu PowerShell](quick-create-powershell.md) an, um einen virtuellen Computer für dieses Tutorial zu erstellen. Ersetzen Sie beim Durcharbeiten des Tutorials bei Bedarf die Ressourcennamen.

## <a name="launch-azure-cloud-shell"></a>Starten von Azure Cloud Shell

Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel ausführen können. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert. 

Wählen Sie zum Öffnen von Cloud Shell oben rechts in einem Codeblock einfach die Option **Ausprobieren**. Sie können Cloud Shell auch auf einer separaten Browserregisterkarte starten, indem Sie zu [https://shell.azure.com/powershell](https://shell.azure.com/powershell) navigieren. Wählen Sie **Kopieren**, um die Blöcke mit dem Code zu kopieren. Fügen Sie ihn anschließend in Cloud Shell ein, und drücken Sie die EINGABETASTE, um ihn auszuführen.


## <a name="get-the-vm"></a>Abrufen des virtuellen Computers

Mit [Get-AzVM](/powershell/module/az.compute/get-azvm) können Sie eine Liste der VMs abrufen, die in einer Ressourcengruppe verfügbar sind. Wenn Sie den Namen und die Ressourcengruppe des virtuellen Computers kennen, können Sie erneut `Get-AzVM` verwenden, um das VM-Objekt abzurufen und für die spätere Verwendung in einer Variablen zu speichern. In diesem Beispiel wird ein virtueller Computer namens *sourceVM* aus der Ressourcengruppe „myResourceGroup“ abgerufen und der Variablen *$vm* zugewiesen. 

```azurepowershell-interactive
$sourceVM = Get-AzVM `
   -Name sourceVM `
   -ResourceGroupName myResourceGroup
```
## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit dem Befehl [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Ressourcengruppe.

Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myGalleryRG* in der Region *EastUS* erstellt:

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'EastUS'
```

## <a name="create-an-image-gallery"></a>Erstellen eines Imagekatalogs 

Ein Imagekatalog ist die primäre Ressource, die zur Ermöglichung des Teilens von Images verwendet wird. Zulässige Zeichen für Katalognamen sind Groß- und Kleinbuchstaben, Zahlen und Punkte. Der Katalogname darf keine Bindestriche enthalten. Katalognamen müssen innerhalb Ihres Abonnements eindeutig sein. 

Erstellen Sie mit [New-AzGallery](/powershell/module/az.compute/new-azgallery) einen Imagekatalog. Im folgenden Beispiel wird der Katalog *myGallery* in der Ressourcengruppe *myGalleryRG* erstellt.

```azurepowershell-interactive
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Azure Compute Gallery for my organization' 
```


## <a name="create-an-image-definition"></a>Erstellen einer Imagedefinition 

Imagedefinitionen erstellen eine logische Gruppierung von Images. Sie werden verwendet, um Informationen über die Imageversionen zu verwalten, die in ihnen erstellt werden. Namen für Imagedefinition können aus Groß- und Kleinbuchstaben, Zahlen, Punkten und (Binde)Strichen bestehen. Weitere Informationen zu den Werten, die Sie für eine Imagedefinition angeben können, finden Sie unter [Imagedefinitionen](../virtual-machines/shared-image-galleries.md#image-definitions).

Erstellen Sie die Imagedefinition mit [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion). In diesem Beispiel hat das Katalogimage den Namen *myGalleryImage* und wird für ein spezialisiertes Image erstellt. 

```azurepowershell-interactive
$galleryImage = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-an-image-version"></a>Erstellen einer Imageversion

Erstellen Sie mit [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion) eine Imageversion aus einem virtuellen Computer. 

Zulässige Zeichen für die Imageversion sind Zahlen und Punkte. Zahlen müssen im Bereich einer ganzen 32-Bit-Zahl liegen. Format: *Hauptversion*.*Nebenversion*.*Patch*.

In diesem Beispiel lautet die Imageversion *1.0.0*. Sie wird in den Rechenzentren *USA, Osten* und *USA, Süden-Mitte* repliziert. Bei der Auswahl der Zielregionen für die Replikation müssen Sie die *Quellregion* als Ziel für die Replikation angeben.

Verwenden Sie `$vm.Id.ToString()` für `-Source`, um eine Imageversion des virtuellen Computers zu erstellen.

```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='East US';ReplicaCount=2}
$targetRegions = @($region1,$region2)

New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -TargetRegion $targetRegions  `
   -Source $sourceVM.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-01'
```

Es kann eine Weile dauern, bis das Image in alle Zielregionen repliziert ist.

## <a name="create-a-scale-set-from-the-image"></a>Erstellen einer Skalierungsgruppe auf der Grundlage des Images
Erstellen Sie jetzt mit [New-AzVmss](/powershell/module/az.compute/new-azvmss) eine Skalierungsgruppe, für die der Parameter `-ImageName` genutzt wird, um das im vorherigen Schritt erstellte VM-Image zu definieren. Um Datenverkehr an die einzelnen VM-Instanzen zu verteilen, wird auch ein Lastenausgleich erstellt. Der Lastenausgleich enthält Regeln zum Verteilen von Datenverkehr über TCP-Port 80 und zum Zulassen von Remotedesktop-Datenverkehr über TCP-Port 3389 und PowerShell-Remoting über TCP-Port 5985. Geben Sie Ihre gewünschten Administratoranmeldeinformationen für die VM-Instanzen in der Skalierungsgruppe an, wenn Sie dazu aufgefordert werden:

```azurepowershell-interactive
# Define variables for the scale set
$resourceGroupName = "myVMSSRG3"
$scaleSetName = "myScaleSet3"
$location = "East US"

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
  -ImageReferenceId $galleryImage.Id

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


## <a name="share-the-gallery"></a>Teilen des Katalogs

Wir empfehlen, dass Sie den Zugriff auf der Ebene des Imagekatalogs teilen. Verwenden Sie eine E-Mail-Adresse und das Cmdlet [Get-AzADUser](/powershell/module/az.resources/get-azaduser), um die Objekt-ID für den Benutzer abzurufen, und verwenden Sie dann [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment), um ihnen Zugriff auf den Katalog zu gewähren. Ersetzen Sie die Beispiel-E-Mail-Adresse alinne_montes@contoso.com in diesem Beispiel durch Ihre eigenen Informationen.

```azurepowershell-interactive
# Get the object ID for the user
$user = Get-AzADUser -StartsWith alinne_montes@contoso.com
# Grant access to the user for our gallery
New-AzRoleAssignment `
   -ObjectId $user.Id `
   -RoleDefinitionName Reader `
   -ResourceName $gallery.Name `
   -ResourceType Microsoft.Compute/galleries `
   -ResourceGroupName $resourceGroup.ResourceGroupName
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die Ressourcengruppe und alle dazugehörigen Ressourcen nicht mehr benötigt werden, können Sie sie mit dem Cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) entfernen:

```azurepowershell-interactive
# Delete the gallery 
Remove-AzResourceGroup -Name myGalleryRG

# Delete the scale set resource group
Remove-AzResourceGroup -Name myResoureceGroup
```

## <a name="azure-image-builder"></a>Azure Image Builder

Azure bietet darüber hinaus den auf Packer basierenden Dienst [Azure VM Image Builder](../virtual-machines/image-builder-overview.md). Beschreiben Sie einfach Ihre Anpassungen in einer Vorlage, und diese übernimmt die Imageerstellung. 

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial wurde beschrieben, wie Sie ein benutzerdefiniertes VM-Image für Ihre Skalierungsgruppen mit Azure PowerShell erstellen und verwenden:

> [!div class="checklist"]
> * Erstellen einer Azure Compute Gallery-Instanz
> * Erstellen einer Imagedefinition
> * Erstellen einer Imageversion
> * Erstellen einer Skalierungsgruppe aus einem Image 
> * Freigeben eines Imagekatalogs

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie Anwendungen in Ihrer Skalierungsgruppe bereitstellen.

> [!div class="nextstepaction"]
> [Bereitstellen von Anwendungen in Ihren Skalierungsgruppen](tutorial-install-apps-powershell.md)
