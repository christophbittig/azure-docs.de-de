---
title: Erstellen einer VM aus einem generalisierten Image in einem Katalog
description: Erstellen Sie eine VM aus einem generalisierten Image in einem Katalog.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 08/31/2021
ms.author: cynthn
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: a9b1dc77eb8eaae9e634cb362beed527a74a84be
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131474513"
---
# <a name="create-a-vm-from-a-generalized-image-version"></a>Erstellen einer VM aus einer generalisierten Imageversion

Erstellen Sie eine VM aus einer [generalisierten Imageversion](./shared-image-galleries.md#generalized-and-specialized-images), die in einer Azure Compute Gallery (früher als Shared Image Gallery bezeichnet) gespeichert ist. Informationen zum Erstellen einer VM aus einem spezialisierten Image finden Sie unter [Erstellen einer VM aus einem spezialisierten Image](vm-specialized-image-version.md). 


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
1. Unter **Administratorkonto** müssen Sie einen Benutzernamen wie *azureuser* sowie ein Kennwort oder einen SSH-Schlüssel angeben. Das Kennwort muss mindestens zwölf Zeichen lang sein und die [definierten Anforderungen an die Komplexität](./windows/faq.yml#what-are-the-password-requirements-when-creating-a-vm-) erfüllen.
1. Wenn Sie Remotezugriff auf den virtuellen Computer zulassen möchten, wählen Sie unter **Öffentliche Eingangsports** die Option **Ausgewählte Ports zulassen** aus, und wählen Sie dann in der Dropdownliste **SSH (22)** oder **RDP (3389)** aus. Wenn Sie keinen Remotezugriff auf den virtuellen Computer ermöglichen möchten, lassen Sie für ausgewählte **Öffentliche Eingangsports** die Option **Keine** ausgewählt.
1. Wählen Sie abschließend die Schaltfläche **Überprüfen + erstellen** im unteren Seitenbereich aus.
1. Nachdem die VM-Konfiguration erfolgreich überprüft wurde, wählen Sie im unteren Seitenbereich **Erstellen** aus, um die Bereitstellung zu starten.

### <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli)


Listen Sie die Imagedefinitionen in einem Katalog mithilfe von [az sig image-definition list](/cli/azure/sig/image-definition#az_sig_image_definition_list) auf, um den Namen und die ID der Definitionen anzuzeigen.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list --resource-group $resourceGroup --gallery-name $gallery --query "[].[name, id]" --output tsv
```

Erstellen Sie einen virtuellen Computer mit [az vm create](/cli/azure/vm#az_vm_create). Wenn Sie die neueste Version des Images verwenden möchten, legen Sie `--image` auf die ID der Imagedefinition fest. 

Das folgende Beispiel dient zum Erstellen einer mit SSH geschützten Linux-VM. Für Windows oder zum Schutz einer Linux-VM mit einem Kennwort entfernen Sie `--generate-ssh-keys`, um zur Eingabe eines Kennworts aufgefordert zu werden. Wenn Sie ein Kennwort direkt angeben möchten, ersetzen Sie `--generate-ssh-keys` durch `--admin-password`. Ersetzen Sie bei Bedarf die Ressourcennamen in diesem Beispiel. 

```azurecli-interactive 
imgDef="/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition"
vmResourceGroup=myResourceGroup
location=eastus
vmName=myVM
adminUsername=azureuser


az group create --name $vmResourceGroup --location $location

az vm create\
   --resource-group $vmResourceGroup \
   --name $vmName \
   --image $imgDef \
   --admin-username $adminUsername \
   --generate-ssh-keys
```

Sie können auch eine bestimmte Version verwenden, indem Sie die Imageversion mit der ID für den `--image`-Parameter verwenden. Um beispielsweise die Imageversion *1.0.0* zu verwenden, geben Sie ein: `--image "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"`.

### <a name="powershell"></a>[PowerShell](#tab/powershell)

Nachdem Sie über eine generalisierte Imageversion verfügen, können Sie neue VMs erstellen. Mithilfe des Cmdlets [New-AzVM](/powershell/module/az.compute/new-azvm). 

In diesem Beispiel verwenden wir die Imagedefinitions-ID, um sicherzustellen, dass Ihre neue VM die neueste Version eines Images verwendet. Sie können auch eine bestimmte Version verwenden, indem Sie die Imageversions-ID für `Set-AzVMSourceImage -Id` verwenden. Um beispielsweise die Imageversion *1.0.0* zu verwenden, geben Sie ein: `Set-AzVMSourceImage -Id "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"`. 

Beachten Sie, dass die Verwendung einer bestimmten Imageversion bedeutet, dass die Automatisierung fehlschlagen kann, wenn diese bestimmte Imageversion nicht verfügbar ist, weil sie gelöscht oder aus der Region entfernt wurde. Es wird empfohlen, die Imagedefinitions-ID zum Erstellen der neuen VM zu verwenden, es sei denn, eine bestimmte Imageversion ist erforderlich.

Ersetzen Sie nach Bedarf die Ressourcennamen in diesen Beispielen. 

**Vereinfachter Parametersatz**

Sie können den vereinfachten Parametersatz verwenden, um schnell eine VM aus einem Image zu erstellen. Der vereinfachte Parametersatz erstellt mithilfe des VM-Namens automatisch einige der erforderlichen Ressourcen wie das VNET und die öffentliche IP-Adresse für Sie. 

```azurepowershell-interactive
# Create some variables for the new VM 
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "myVMfromImage"

# Get the image. Replace the name of your resource group, gallery, and image definition. This will create the VM from the latest image version available.

$imageDefinition = Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name myImageDefinition

# Create user object
$cred = Get-Credential `
   -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup `
   -Name $resourceGroup `
   -Location $location

New-AzVM `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name $vmName `
   -Image $imageDefinition.Id
   -Credential $cred
```



**Vollständiger Parametersatz**

Sie können eine VM mit bestimmten Ressourcen mithilfe des vollständigen Parametersatzes erstellen.

```azurepowershell-interactive
# Create some variables for the new VM 
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "myVMfromImage"

# Get the image. Replace the name of your resource group, gallery, and image definition. This will create the VM from the latest image version available.

$imageDefinition = Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name myImageDefinition

# Create user object
$cred = Get-Credential `
   -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup `
   -Name $resourceGroup `
   -Location $location

# Network pieces
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
   -DestinationPortRange 3389 `
   -Access Allow
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface `
   -Name myNic `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageDefinition.Id to use the latest image version.
$vmConfig = New-AzVMConfig `
   -VMName $vmName `
   -VMSize Standard_D1_v2 | `
   Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
   Set-AzVMSourceImage -Id $imageDefinition.Id | `
   Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -VM $vmConfig
```

### <a name="rest"></a>[REST](#tab/rest)

Erstellt ein virtuelles Netzwerk.

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/virtualNetworks/{vNetName}?api-version=2020-05-01

{
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "10.0.0.0/16"
            ]
        }
    },
    "location": "eastus"
}
```

Erstellen Sie ein Subnetz.

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/virtualNetworks/{vNetName}/subnets/{subnetName}?api-version=2020-05-01

{
    "properties": {
        "addressPrefix": "10.0.0.0/16"
    },
}
```

Erstellen einer öffentlichen IP-Adresse

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/publicIPAddresses/{pIPName}?api-version=2020-11-01

{
  "location": "eastus"
}

```

Erstellen Sie eine Netzwerksicherheitsgruppe.

```rest
# @name vmNSG
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}?api-version=2020-11-01

{
  "properties": {
    "securityRules": [
      {
        "name": "AllowSSH",
        "properties": {
          "protocol": "Tcp",
          "sourceAddressPrefix": "*",
          "destinationAddressPrefix": "*",
          "access": "Allow",
          "destinationPortRange": "3389",
          "sourcePortRange": "*",
          "priority": 1000,
          "direction": "Inbound"
        }
      }
    ]
  },
  "location": "eastus"
}
```

Erstellen Sie eine NIC.

```rest
# @name vmNIC
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}?api-version=2020-05-01

{
    "properties": {
        "enableAcceleratedNetworking": true,
        "networkSecurityGroup": {
          "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
        },
        "ipConfigurations": [
            {
                "name": "ipconfig1",
                "properties": {
                    "subnet": {
                        "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/virtualNetworks/{vNetName}/subnets/{subNetName}",
                    },
                    "publicIPAddress": {
                        "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/publicIPAddresses/{pipName}"
          }
                }
            }
        ]
    },
    "location": "eastus",
}
```
Erstellen Sie einen virtuellen Linux-Computer. Der Abschnitt `oSProfile` enthält einige betriebssystemspezifische Details. Im nächsten Codebeispiel finden Sie die Windows-Syntax.

```rest
# @name vm
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2020-06-01

{
    "location": "eastus",
    "properties": {
        "hardwareProfile": {
            "vmSize": "Standard_DS3_v2"
        },
        "storageProfile": {
            "imageReference": {
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/galleries/{galleryName}/images/{galleryImageName}/versions/{versionNumber}"
            },
            "osDisk": {
                "caching": "ReadWrite",
                "managedDisk": {
                    "storageAccountType": "Standard_LRS"
                },
                "createOption": "FromImage"
            }
        },
        "osProfile": {
            "adminUsername": "{your-username}",
            "computerName": "myVM",
            "linuxConfiguration": {
                "ssh": {
                    "publicKeys": [
                        {
                            "path": "/home/{your-username}/.ssh/authorized_keys",
                            "keyData": "{sshKey}",
                        }
                    ]
                },
                "disablePasswordAuthentication": true
            }
        },
        "networkProfile": {
            "networkInterfaces": [
                {
                    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
                }
            ]
        }
    },
}

```

Erstellen Sie eine Windows-VM.

```rest
# @name vm
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2020-06-01

{
    "location": "eastus",
    "properties": {
        "hardwareProfile": {
            "vmSize": "Standard_DS3_v2"
        },
        "storageProfile": {
            "imageReference": {
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/galleries/{galleryName}/images/{galleryImageName}/versions/{versionNumber}"
            },
            "osDisk": {
                "caching": "ReadWrite",
                "managedDisk": {
                    "storageAccountType": "Standard_LRS"
                },
                "createOption": "FromImage"
            }
        },
        "osProfile": {
            "adminUsername": "{your-username}",
            "computerName": "myVM",
            "adminPassword": "{your-password}"
        },
        "networkProfile": {
            "networkInterfaces": [
                {
                    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
                }
            ]
        }
    },
```

---

**Nächste Schritte**

[Azure Image Builder (Vorschauversion)](./image-builder-overview.md) hilft beim Automatisieren der Erstellung von Imageversionen. Sie können den Dienst sogar zum Aktualisieren und [Erstellen einer neuen Imageversion aus einer vorhandenen](./linux/image-builder-gallery-update-image-version.md) verwenden.