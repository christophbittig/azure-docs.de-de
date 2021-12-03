---
title: Erstellen eines virtuellen Computers aus einer spezialisierten Imageversion
description: Erstellen Sie einen virtuellen Computer mithilfe einer spezialisierten Bildversion in einer Azure Compute Gallery.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 08/05/2021
ms.author: cynthn
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: b500a7e02167f5373eb2dbdeb4bdb708c5b69966
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131474494"
---
# <a name="create-a-vm-using-a-specialized-image-version"></a>Erstellen eines virtuellen Computers mithilfe einer spezialisierten Imageversion 

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs 

Erstellen Sie einen virtuellen Computer aus einer [spezialisierten Imageversion](./shared-image-galleries.md#generalized-and-specialized-images), die in einer Azure Compute Gallery (früher als Shared Image Gallery bezeichnet) gespeichert ist. Wenn Sie eine VM mit einer generalisierten Imageversion erstellen möchten, finden Sie weitere Informationen unter [Erstellen einer VM aus einer generalisierten Imageversion](vm-generalized-image-version.md).

> [!IMPORTANT]
> 
> Wenn Sie zur Erstellung eines neuen VM eine spezielle VHD verwenden, behält der neue VM den Computernamen des ursprünglichen VM bei. Andere computerspezifische Informationen (z. B. die CMID) werden ebenfalls beibehalten. In einigen Fällen können diese doppelten Informationen Probleme verursachen. Achten Sie beim Kopieren einer VM darauf, auf welche Arten von computerspezifischen Informationen Ihre Anwendungen angewiesen sind.  

Ersetzen Sie nach Bedarf die Ressourcennamen in diesen Beispielen. 

### <a name="portal"></a>[Portal](#tab/portal)

Jetzt können Sie einen oder mehrere neue virtuelle Computer erstellen. Dieses Beispiel erstellt den virtuellen Computer *myVM* in *myResourceGroup* im Rechenzentrum *USA, Osten*.

1. Wechseln Sie zur Imagedefinition. Sie können den Ressourcenfilter verwenden, um alle verfügbaren Imagedefinitionen anzuzeigen.
1. Wählen Sie im Menü oben auf der Seite für die Imagedefinition **VM erstellen** aus.
1. Wählen Sie unter **Ressourcengruppe** die Option **Neu erstellen** aus, und geben Sie *myResourceGroup* als Namen ein.
1. Geben Sie unter **Name des virtuellen Computers** die Zeichenfolge *myVM* ein.
1. Wählen Sie als **Region** die Option *USA, Osten* aus.
1. Übernehmen Sie für **Verfügbarkeitsoptionen** den Standardwert *Keine Infrastrukturredundanz erforderlich*.
1. Der Wert für **Image** wird automatisch mit der neuesten Imageversion (`latest`) ausgefüllt, wenn Sie auf der Seite für die Imagedefinition begonnen haben.
1. Wählen Sie für **Größe** in der Liste der verfügbaren Größen eine VM-Größe aus und klicken Sie dann auf **Auswählen**.
1. Der Benutzername unter **Administratorkonto** wird abgeblendet dargestellt, da der Benutzername und die Anmeldeinformationen des virtuellen Quellcomputers verwendet werden.
1. Wenn Sie Remotezugriff auf den virtuellen Computer zulassen möchten, wählen Sie unter **Öffentliche Eingangsports** die Option **Ausgewählte Ports zulassen** und dann in der Dropdownliste **SSH (22)** oder **RDP (3389)** aus. Wenn Sie keinen Remotezugriff auf den virtuellen Computer ermöglichen möchten, lassen Sie für ausgewählte **Öffentliche Eingangsports** die Option **Keine** ausgewählt.
1. Wählen Sie abschließend die Schaltfläche **Überprüfen + erstellen** im unteren Seitenbereich aus.
1. Nachdem die VM-Konfiguration erfolgreich überprüft wurde, wählen Sie im unteren Seitenbereich **Erstellen** aus, um die Bereitstellung zu starten.


### <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli)

Listen Sie die Imagedefinitionen in einem Katalog mithilfe von [az sig image-definition list](/cli/azure/sig/image-definition#az_sig_image_definition_list) auf, um den Namen und die ID der Definitionen anzuzeigen.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

Erstellen Sie den virtuellen Computer mithilfe von [az vm create](/cli/azure/vm#az_vm_create) und unter Verwendung des Parameters „--specialized“, um anzugeben, dass es sich um ein spezialisiertes Image handelt. 

Verwenden Sie die Imagedefinitions-ID für `--image`, um die VM auf der Grundlage der neuesten verfügbaren Imageversion zu erstellen. Sie können die VM auch auf der Grundlage einer bestimmten Version erstellen, indem Sie die Imageversions-ID für `--image` angeben. 

In diesem Beispiel erstellen Sie eine VM aus der aktuellen Version des Images *myImageDefinition*.

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)

Nachdem Sie über eine spezialisierte Imageversion verfügen, können Sie mithilfe des Cmdlets [New-AzVM](/powershell/module/az.compute/new-azvm) neue VMs erstellen. 

In diesem Beispiel verwenden wir die Imagedefinitions-ID, um sicherzustellen, dass Ihre neue VM die neueste Version eines Images verwendet. Sie können auch eine bestimmte Version verwenden, indem Sie die Imageversions-ID für `Set-AzVMSourceImage -Id` verwenden. Um beispielsweise die Imageversion *1.0.0* zu verwenden, geben Sie ein: `Set-AzVMSourceImage -Id "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"`. 

Beachten Sie, dass die Verwendung einer bestimmten Imageversion bedeutet, dass die Automatisierung fehlschlagen kann, wenn diese bestimmte Imageversion nicht verfügbar ist, weil sie gelöscht oder aus der Region entfernt wurde. Es wird empfohlen, die Imagedefinitions-ID zum Erstellen der neuen VM zu verwenden, es sei denn, eine bestimmte Imageversion ist erforderlich.

Ersetzen Sie bei Bedarf die Ressourcennamen in diesem Beispiel. 


```azurepowershell-interactive

# Create some variables for the new VM.

$resourceGroup = "mySIGSpecializedRG"
$location = "South Central US"
$vmName = "mySpecializedVM"

# Get the image. Replace the name of your resource group, gallery, and image definition. This will create the VM from the latest image version available.

$imageDefinition = Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name myImageDefinition


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

# Create a virtual machine configuration using Set-AzVMSourceImage -Id $imageDefinition.Id to use the latest available image version.

$vmConfig = New-AzVMConfig `
   -VMName $vmName `
   -VMSize Standard_D1_v2 | `
   Set-AzVMSourceImage -Id $imageDefinition.Id | `
   Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -VM $vmConfig

```

---

**Nächste Schritte**

Sie können auch mithilfe von Vorlagen eine Ressource in der Azure Compute Gallery erstellen. Es stehen mehrere Azure-Schnellstartvorlagen zur Verfügung: 

- [Erstellen einer Azure Compute Gallery-Instanz](https://azure.microsoft.com/resources/templates/sig-create/)
- [Erstellen einer Bilddefinition in einer Azure Compute Gallery-Instanz](https://azure.microsoft.com/resources/templates/sig-image-definition-create/)
- [Erstellen einer Bildversion in einer Azure Compute Gallery-Instanz](https://azure.microsoft.com/resources/templates/sig-image-version-create/)
