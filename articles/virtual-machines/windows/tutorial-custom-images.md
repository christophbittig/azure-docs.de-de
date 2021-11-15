---
title: 'Tutorial: Erstellen benutzerdefinierter VM-Images mit Azure PowerShell'
description: In diesem Tutorial erfahren Sie, wie Sie mithilfe von Azure PowerShell ein benutzerdefiniertes Image für virtuelle Windows-Computer in einer Azure Compute Gallery-Instanz erstellen.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 05/01/2020
ms.author: cynthn
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 5c7ce289d515892db72b681e1e7ef5337eb7cb9b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131421633"
---
# <a name="tutorial-create-windows-vm-images-with-azure-powershell"></a>Tutorial: Erstellen von Windows-VM-Images mit Azure PowerShell
**Gilt für:** :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

Images können für das Bootstrapping von Bereitstellungen verwendet werden und sorgen für VM-übergreifende Konsistenz. In diesem Tutorial erstellen Sie mithilfe von PowerShell ein eigenes spezialisiertes Image eines virtuellen Azure-Computers und speichern es in einer Azure Compute Gallery-Instanz (früher als „Shared Image Gallery“ bezeichnet). Folgendes wird vermittelt:

> [!div class="checklist"]
> * Erstellen einer Azure Compute Gallery-Instanz
> * Erstellen einer Imagedefinition
> * Erstellen einer Imageversion
> * Erstellen eines virtuellen Computers aus einem Image 
> * Freigeben eines Katalogs



## <a name="before-you-begin"></a>Voraussetzungen

In den folgenden Schritten wird erläutert, wie Sie eine vorhandene VM in ein wiederverwendbares benutzerdefiniertes Image umwandeln, mit dem Sie neue VMs erstellen können.

Für das Beispiel in diesem Tutorial muss ein virtueller Computer vorhanden sein. Sehen Sie sich ggf. die [Schnellstartanleitung zu PowerShell](quick-create-powershell.md) an, um einen virtuellen Computer für dieses Tutorial zu erstellen. Ersetzen Sie beim Durcharbeiten des Tutorials bei Bedarf die Ressourcennamen.

## <a name="overview"></a>Übersicht

Eine [Azure Compute Gallery](../shared-image-galleries.md)-Instanz vereinfacht das Freigeben benutzerdefinierter Images in Ihrer Organisation. Benutzerdefinierte Images sind wie Marketplace-Images, Sie erstellen sie jedoch selbst. Benutzerdefinierte Images können zum Starten von Konfigurationen verwendet werden, z.B. zum Vorabladen von Anwendungen, Anwendungskonfigurationen und anderen Betriebssystemkonfigurationen. 

Mithilfe von Azure Compute Gallery können Sie Ihre benutzerdefinierten VM-Images für andere Benutzer freigeben. Wählen Sie aus, welche Images Sie teilen möchten, in welchen Regionen Sie sie verfügbar machen möchten, und mit wem Sie sie teilen möchten. 

Die Funktion „Azure Compute Gallery“ verfügt über mehrere Ressourcentypen:

[!INCLUDE [virtual-machines-shared-image-gallery-resources](../includes/virtual-machines-shared-image-gallery-resources.md)]


## <a name="launch-azure-cloud-shell"></a>Starten von Azure Cloud Shell

Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel ausführen können. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert. 

Wählen Sie zum Öffnen von Cloud Shell oben rechts in einem Codeblock einfach die Option **Ausprobieren**. Sie können Cloud Shell auch auf einer separaten Browserregisterkarte starten, indem Sie zu [https://shell.azure.com/powershell](https://shell.azure.com/powershell) navigieren. Wählen Sie **Kopieren**, um die Blöcke mit dem Code zu kopieren. Fügen Sie ihn anschließend in Cloud Shell ein, und drücken Sie die EINGABETASTE, um ihn auszuführen.

## <a name="get-the-vm"></a>Abrufen des virtuellen Computers

Mit [Get-AzVM](/powershell/module/az.compute/get-azvm) können Sie eine Liste der VMs abrufen, die in einer Ressourcengruppe verfügbar sind. Wenn Sie den Namen und die Ressourcengruppe des virtuellen Computers kennen, können Sie erneut `Get-AzVM` verwenden, um das VM-Objekt abzurufen und für die spätere Verwendung in einer Variablen zu speichern. Dieses Beispiel ruft die VM *sourceVM* aus der Ressourcengruppe „myResourceGroup“ ab und weist es der Variablen *$sourceVM* zu. 

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

## <a name="create-a-gallery"></a>Erstellen eines Katalogs 

Ein Katalog ist die primäre Ressource, die zur Ermöglichung der Imagefreigabe verwendet wird. Zulässige Zeichen für Katalognamen sind Groß- und Kleinbuchstaben, Zahlen und Punkte. Der Katalogname darf keine Bindestriche enthalten. Katalognamen müssen innerhalb Ihres Abonnements eindeutig sein. 

Erstellen Sie mithilfe von [New-AzGallery](/powershell/module/az.compute/new-azgallery) einen Katalog. Im folgenden Beispiel wird der Katalog *myGallery* in der Ressourcengruppe *myGalleryRG* erstellt.

```azurepowershell-interactive
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Azure Compute Gallery for my organization' 
```


## <a name="create-an-image-definition"></a>Erstellen einer Imagedefinition 

Imagedefinitionen erstellen eine logische Gruppierung von Images. Sie werden verwendet, um Informationen über die Imageversionen zu verwalten, die in ihnen erstellt werden. Namen für Imagedefinition können aus Groß- und Kleinbuchstaben, Zahlen, Punkten und (Binde)Strichen bestehen. Weitere Informationen zu den Werten, die Sie für eine Imagedefinition angeben können, finden Sie unter [Imagedefinitionen](../shared-image-galleries.md#image-definitions).

Erstellen Sie die Imagedefinition mit [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimagedefinition). In diesem Beispiel hat das Katalogimage den Namen *myGalleryImage* und wird für ein spezialisiertes Image erstellt. 

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


## <a name="create-a-vm"></a>Erstellen einer VM 

Wenn Sie ein spezialisiertes Image erstellt haben, können Sie eine neue VM oder mehrere neue VMs erstellen. Mithilfe des Cmdlets [New-AzVM](/powershell/module/az.compute/new-azvm). Verwenden Sie zur Nutzung des Images `Set-AzVMSourceImage`, und legen Sie `-Id` auf die Imagedefinitions-ID (in diesem Fall „$galleryImage.Id“) fest, damit stets die aktuelle Imageversion verwendet wird. 

Ersetzen Sie bei Bedarf die Ressourcennamen in diesem Beispiel. 

```azurepowershell-interactive
# Create some variables for the new VM.
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "mySpecializedVM"

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create the network resources.
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name $vmName -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the image version.
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMSourceImage -Id $galleryImage.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```


## <a name="share-the-gallery"></a>Teilen des Katalogs

Es wird empfohlen, den Zugriff auf der Katalogebene freizugeben. Verwenden Sie eine E-Mail-Adresse und das Cmdlet [Get-AzADUser](/powershell/module/az.resources/get-azaduser), um die Objekt-ID für den Benutzer abzurufen, und verwenden Sie dann [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment), um ihnen Zugriff auf den Katalog zu gewähren. Ersetzen Sie die Beispiel-E-Mail-Adresse alinne_montes@contoso.com in diesem Beispiel durch Ihre eigenen Informationen.

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

# Delete the VM
Remove-AzResourceGroup -Name myResoureceGroup
```

## <a name="azure-image-builder"></a>Azure Image Builder

Azure bietet darüber hinaus den auf Packer basierenden Dienst [Azure VM Image Builder](../image-builder-overview.md). Beschreiben Sie einfach Ihre Anpassungen in einer Vorlage, und diese übernimmt die Imageerstellung. 

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie ein spezialisiertes VM-Image erstellt. Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen einer Azure Compute Gallery-Instanz
> * Erstellen einer Imagedefinition
> * Erstellen einer Imageversion
> * Erstellen eines virtuellen Computers aus einem Image 
> * Freigeben eines Katalogs

Im nächsten Tutorial erhalten Sie Informationen zum Erstellen hoch verfügbarer virtueller Computer.

> [!div class="nextstepaction"]
> [Erstellen eines hoch verfügbaren virtuellen Computers](tutorial-availability-sets.md)
