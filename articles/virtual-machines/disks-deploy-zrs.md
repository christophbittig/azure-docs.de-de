---
title: Bereitstellen eines verwalteten ZRS-Datenträgers (Vorschauversion)
description: Erfahren Sie, wie Sie einen verwalteten Datenträger bereitstellen, der zonenredundanten Speicher (ZRS) verwendet.
author: roygara
ms.author: rogarana
ms.date: 07/12/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: 7afa1a5416651dc7e93ff354e63fefb259ea81b4
ms.sourcegitcommit: aaaa6ee55f5843ed69944f5c3869368e54793b48
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/13/2021
ms.locfileid: "113667259"
---
# <a name="deploy-a-managed-disk-that-uses-zone-redundant-storage-preview"></a>Bereitstellen eines verwalteten Datenträgers, der zonenredundanten Speicher (Vorschau) verwendet

In diesem Artikel wird beschrieben, wie Sie einen Datenträger bereitstellen, der zonenredundanten Speicher (ZRS) als Redundanzoption (Vorschauversion) verwendet. Bei ZRS wird Ihr verwalteter Azure-Datenträger synchron über drei Azure-Verfügbarkeitszonen hinweg in der ausgewählten Region repliziert. Jede Verfügbarkeitszone ist ein getrennter physischer Standort mit unabhängigen Stromversorgungs-, Kühlungs- und Netzwerkgeräten.

Konzeptionelle Informationen zu ZRS finden Sie unter [Zonenredundanter Speicher für verwaltete Datenträger (Vorschau)](disks-redundancy.md#zone-redundant-storage-for-managed-disks-preview).

## <a name="limitations"></a>Einschränkungen

[!INCLUDE [disk-storage-zrs-limitations](../../includes/disk-storage-zrs-limitations.md)]

# <a name="azure-portal"></a>[Azure-Portal](#tab/portal)

### <a name="prerequisites"></a>Voraussetzungen

Sie müssen das Features für Ihr Abonnement aktivieren. Führen Sie die folgenden Schritte aus, um das Feature für Ihr Abonnement zu aktivieren:

1.  Führen Sie den folgenden Befehl aus, um das Feature für Ihr Abonnement zu registrieren.

    ```powershell
     Register-AzProviderFeature -FeatureName "SsdZrsManagedDisks" -ProviderNamespace "Microsoft.Compute" 
    ```

1.  Überprüfen Sie mithilfe des folgenden Befehls, ob der Registrierungsstatus **Registriert** lautet (dies kann einige Minuten dauern), bevor Sie das Feature ausprobieren.

    ```powershell
     Get-AzProviderFeature -FeatureName "SsdZrsManagedDisks" -ProviderNamespace "Microsoft.Compute"  
    ```

### <a name="create-a-vm-with-a-zrs-os-disk"></a>Erstellen eines virtuellen Computers mit einem ZRS-Betriebssystemdatenträger

1. Melden Sie sich beim Azure-Portal an.
1. Navigieren Sie zu **Virtuelle Computer** und folgen Sie dem normalen VM-Erstellungsprozess.
1. Wählen Sie eine unterstützte Region aus, und legen Sie für **Verfügbarkeitsoptionen** die Einstellung **Keine Infrastrukturredundanz erforderlich** fest.

    :::image type="content" source="media/disks-deploy-zrs/disks-zrs-portal-basic.png" alt-text="Screenshot: Workflow für die VM-Erstellung, grundlegender Bereich, Redundanz und Regionen sind hervorgehoben." lightbox="media/disks-deploy-zrs/disks-zrs-portal-basic.png":::

1. Fahren Sie im Bereich **Datenträger** fort.
1. Wählen Sie Ihren Datenträger aus, indem Sie einen der ZRS-Datenträger in der Dropdownliste auswählen.

    :::image type="content" source="media/disks-deploy-zrs/disks-zrs-portal-select-blade.png" alt-text="Screenshot: Workflow für die VM-Erstellung, Bereich „Datenträger“, Dropdownliste der Betriebssystemdatenträger ist mit hervorgehobenen ZRS-Optionen „SSD Premium“ und „SSD Standard“ erweitert." lightbox="media/disks-deploy-zrs/disks-zrs-portal-select-blade.png":::

1. Setzen Sie die VM-Bereitstellung fort, und treffen Sie die gewünschte Auswahl.

Sie haben nun einen virtuellen Computer mit einem ZRS-Betriebssystemdatenträger bereitgestellt.

### <a name="create-a-zrs-disk"></a>Erstellen eines ZRS-Datenträgers

1. Suchen Sie im Azure-Portal nach **Datenträgern**, und wählen Sie sie aus.
1. Wählen Sie **+ Hinzufügen** aus, um einen neuen Datenträger zu erstellen.
1. Wählen Sie eine unterstützte Region aus, und legen Sie für **Verfügbarkeitszone** die Option **Keine** fest.
1. Wählen Sie **Größe ändern** aus.

    :::image type="content" source="media/disks-deploy-zrs/create-zrs-disk-pane.png" alt-text="Screenshot des Workflows für die Datenträgererstellung, Bereich „Grundlagen“." lightbox="media/disks-deploy-zrs/create-zrs-disk-pane.png":::

1. Wählen Sie einen der verfügbaren ZRS-Datenträger und dann **OK** aus.

    :::image type="content" source="media/disks-deploy-zrs/select-zrs-disk-sku.png" alt-text="Screenshot des Workflows für die Datenträgererstellung, Bereich zur Auswahl einer Datenträgergröße, hervorgehobene ZRS-Datenträger." lightbox="media/disks-deploy-zrs/select-zrs-disk-sku.png":::

1. Fahren Sie mit dem Bereitstellungsprozess fort.

Sie haben nun einen verwalteten Datenträger erstellt, der ZRS verwendet.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

#### <a name="prerequisites"></a>Voraussetzungen

Sie müssen das Features für Ihr Abonnement aktivieren. Führen Sie die folgenden Schritte aus, um das Feature für Ihr Abonnement zu aktivieren:

1.  Führen Sie den folgenden Befehl aus, um das Feature für Ihr Abonnement zu registrieren.

    ```azurecli
    az feature register --namespace Microsoft.Compute --name SsdZrsManagedDisks
    ```
 
2.  Überprüfen Sie mithilfe des folgenden Befehls, ob der Registrierungsstatus **Registriert** lautet (dies kann einige Minuten dauern), bevor Sie das Feature ausprobieren.

    ```azurecli
    az feature show --namespace Microsoft.Compute --name SsdZrsManagedDisks
    ```

#### <a name="create-a-vm-with-zrs-disks"></a>Erstellen einer VM mit ZRS-Datenträgern

```azurecli
rgName=yourRGName
vmName=yourVMName
location=westus2
vmSize=Standard_DS2_v2
image=UbuntuLTS 
osDiskSku=StandardSSD_ZRS
dataDiskSku=Premium_ZRS


az vm create -g $rgName \
-n $vmName \
-l $location \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--data-disk-sizes-gb 128 \
--storage-sku os=$osDiskSku 0=$dataDiskSku
```
#### <a name="create-vms-with-a-shared-zrs-disk-attached-to-the-vms-in-different-zones"></a>Erstellen von VMs mit einem freigegebenen ZRS-Datenträger, der an die VMs in verschiedenen Zonen angefügt ist
```azurecli

location=westus2
rgName=yourRGName
vmNamePrefix=yourVMNamePrefix
vmSize=Standard_DS2_v2
image=UbuntuLTS
osDiskSku=StandardSSD_LRS
sharedDiskName=yourSharedDiskName
sharedDataDiskSku=Premium_ZRS


az disk create -g $rgName \
-n $sharedDiskName \
-l $location \
--size-gb 1024 \
--sku $sharedDataDiskSku \
--max-shares 2


sharedDiskId=$(az disk show -g $rgName -n $sharedDiskName --query 'id' -o tsv)

az vm create -g $rgName \
-n $vmNamePrefix"01" \
-l $location \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--zone 1 \
--attach-data-disks $sharedDiskId \
--storage-sku os=$osDiskSku \
--vnet-name $vmNamePrefix"_vnet" \
--subnet $vmNamePrefix"_subnet"

az vm create -g $rgName \
-n $vmNamePrefix"02" \
-l $location \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--zone 2 \
--attach-data-disks $sharedDiskId \
--storage-sku os=$osDiskSku \
--vnet-name $vmNamePrefix"_vnet" \
--subnet $vmNamePrefix"_subnet"

```
#### <a name="create-a-virtual-machine-scale-set-with-zrs-disks"></a>Erstellen einer VM-Skalierungsgruppe mit ZRS-Datenträgern
```azurecli
location=westus2
rgName=yourRGName
vmssName=yourVMSSName
vmSize=Standard_DS3_V2
image=UbuntuLTS 
osDiskSku=StandardSSD_ZRS
dataDiskSku=Premium_ZRS

az vmss create -g $rgName \
-n $vmssName \
--encryption-at-host \
--image UbuntuLTS \
--upgrade-policy automatic \
--generate-ssh-keys \
--data-disk-sizes-gb 128 \
--storage-sku os=$osDiskSku 0=$dataDiskSku
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)


#### <a name="prerequisites"></a>Voraussetzungen

Sie müssen das Features für Ihr Abonnement aktivieren. Führen Sie die folgenden Schritte aus, um das Feature für Ihr Abonnement zu aktivieren:

1.  Führen Sie den folgenden Befehl aus, um das Feature für Ihr Abonnement zu registrieren.

    ```powershell
     Register-AzProviderFeature -FeatureName "SsdZrsManagedDisks" -ProviderNamespace "Microsoft.Compute" 
    ```

1.  Überprüfen Sie mithilfe des folgenden Befehls, ob der Registrierungsstatus **Registriert** lautet (dies kann einige Minuten dauern), bevor Sie das Feature ausprobieren.

    ```powershell
     Get-AzProviderFeature -FeatureName "SsdZrsManagedDisks" -ProviderNamespace "Microsoft.Compute"  
    ```
    
#### <a name="create-a-vm-with-zrs-disks"></a>Erstellen einer VM mit ZRS-Datenträgern

```powershell
$subscriptionId="yourSubscriptionId"
$vmLocalAdminUser = "yourAdminUserName"
$vmLocalAdminSecurePassword = ConvertTo-SecureString "yourVMPassword" -AsPlainText -Force
$location = "westus2"
$rgName = "yourResourceGroupName"
$vmName = "yourVMName"
$vmSize = "Standard_DS2_v2"
$osDiskSku = "StandardSSD_ZRS"
$dataDiskSku = "Premium_ZRS"


Connect-AzAccount

Set-AzContext -Subscription $subscriptionId
    
$subnet = New-AzVirtualNetworkSubnetConfig -Name $($vmName+"_subnet") `
                                           -AddressPrefix "10.0.0.0/24"

$vnet = New-AzVirtualNetwork -Name $($vmName+"_vnet") `
                             -ResourceGroupName $rgName `
                             -Location $location `
                             -AddressPrefix "10.0.0.0/16" `
                             -Subnet $subnet

$nic = New-AzNetworkInterface -Name $($vmName+"_nic") `
                              -ResourceGroupName $rgName `
                              -Location $location `
                              -SubnetId $vnet.Subnets[0].Id
    

$vm = New-AzVMConfig -VMName $vmName `
                     -VMSize $vmSize
                     

$credential = New-Object System.Management.Automation.PSCredential ($vmLocalAdminUser, $vmLocalAdminSecurePassword);

$vm = Set-AzVMOperatingSystem -VM $vm `
                              -ComputerName $vmName `
                              -Windows `
                              -Credential $credential

$vm = Add-AzVMNetworkInterface -VM $vm -Id $NIC.Id

$vm = Set-AzVMSourceImage -VM $vm `
                          -PublisherName 'MicrosoftWindowsServer' `
                          -Offer 'WindowsServer' `
                          -Skus '2012-R2-Datacenter' `
                          -Version latest


$vm = Set-AzVMOSDisk -VM $vm `
                     -Name $($vmName +"_OSDisk") `
                     -CreateOption FromImage `
                     -StorageAccountType $osDiskSku

$vm = Add-AzVMDataDisk -VM $vm `
                       -Name $($vmName +"_DataDisk1") `
                       -DiskSizeInGB 128 `
                       -StorageAccountType $dataDiskSku `
                       -CreateOption Empty -Lun 0
    
New-AzVM -ResourceGroupName $rgName `
         -Location $location `
         -VM $vm -Verbose
```

#### <a name="create-vms-with-a-shared-zrs-disk-attached-to-the-vms-in-different-zones"></a>Erstellen von VMs mit einem freigegebenen ZRS-Datenträger, der an die VMs in verschiedenen Zonen angefügt ist

```powershell
$location = "westus2"
$rgName = "yourResourceGroupName"
$vmNamePrefix = "yourVMPrefix"
$vmSize = "Standard_DS2_v2"
$sharedDiskName = "yourSharedDiskName"
$sharedDataDiskSku = "Premium_ZRS"
$vmLocalAdminUser = "yourVMAdminUserName"
$vmLocalAdminSecurePassword = ConvertTo-SecureString "yourPassword" -AsPlainText -Force  


$datadiskconfig = New-AzDiskConfig -Location $location `
                                   -DiskSizeGB 1024 `
                                   -AccountType $sharedDataDiskSku `
                                   -CreateOption Empty `
                                   -MaxSharesCount 2 `

$sharedDisk=New-AzDisk -ResourceGroupName $rgName `
            -DiskName $sharedDiskName `
            -Disk $datadiskconfig

$credential = New-Object System.Management.Automation.PSCredential ($vmLocalAdminUser, $vmLocalAdminSecurePassword);
    
$vm1 = New-AzVm `
        -ResourceGroupName $rgName `
        -Name $($vmNamePrefix+"01") `
        -Zone 1 `
        -Location $location `
        -Size $vmSize `
        -VirtualNetworkName $($vmNamePrefix+"_vnet") `
        -SubnetName $($vmNamePrefix+"_subnet") `
        -SecurityGroupName $($vmNamePrefix+"01_sg") `
        -PublicIpAddressName $($vmNamePrefix+"01_ip") `
        -Credential $credential `
        -OpenPorts 80,3389


$vm1 = Add-AzVMDataDisk -VM $vm1 -Name $sharedDiskName -CreateOption Attach -ManagedDiskId $sharedDisk.Id -Lun 0

update-AzVm -VM $vm1 -ResourceGroupName $rgName
  
$vm2 =  New-AzVm `
        -ResourceGroupName $rgName `
        -Name $($vmNamePrefix+"02") `
        -Zone 2 `
        -Location $location `
        -Size $vmSize `
        -VirtualNetworkName $($vmNamePrefix+"_vnet") `
        -SubnetName ($vmNamePrefix+"_subnet") `
        -SecurityGroupName $($vmNamePrefix+"02_sg") `
        -PublicIpAddressName $($vmNamePrefix+"02_ip") `
        -Credential $credential `
        -OpenPorts 80,3389


$vm2 = Add-AzVMDataDisk -VM $vm1 -Name $sharedDiskName -CreateOption Attach -ManagedDiskId $sharedDisk.Id -Lun 0

update-AzVm -VM $vm1 -ResourceGroupName $rgName
```

#### <a name="create-a-virtual-machine-scale-set-with-zrs-disks"></a>Erstellen einer VM-Skalierungsgruppe mit ZRS-Datenträgern
```powershell
$vmLocalAdminUser = "yourLocalAdminUser"
$vmLocalAdminSecurePassword = ConvertTo-SecureString "yourVMPassword" -AsPlainText -Force
$location = "westus2"
$rgName = "yourResourceGroupName"
$vmScaleSetName = "yourScaleSetName"
$vmSize = "Standard_DS3_v2"
$osDiskSku = "StandardSSD_ZRS"
$dataDiskSku = "Premium_ZRS"
   
    
$subnet = New-AzVirtualNetworkSubnetConfig -Name $($vmScaleSetName+"_subnet") `
                                                 -AddressPrefix "10.0.0.0/24"

$vnet = New-AzVirtualNetwork -Name $($vmScaleSetName+"_vnet") `
                             -ResourceGroupName $rgName `
                             -Location $location `
                             -AddressPrefix "10.0.0.0/16" `
                             -Subnet $subnet

$ipConfig = New-AzVmssIpConfig -Name "myIPConfig" `
                               -SubnetId $vnet.Subnets[0].Id 


$vmss = New-AzVmssConfig -Location $location `
                         -SkuCapacity 2 `
                         -SkuName $vmSize `
                         -UpgradePolicyMode 'Automatic'

$vmss = Add-AzVmssNetworkInterfaceConfiguration -Name "myVMSSNetworkConfig" `
                                                -VirtualMachineScaleSet $vmss `
                                                -Primary $true `
                                                -IpConfiguration $ipConfig

$vmss = Set-AzVmssStorageProfile $vmss -OsDiskCreateOption "FromImage" `
                                       -ImageReferenceOffer 'WindowsServer' `
                                       -ImageReferenceSku '2012-R2-Datacenter' `
                                       -ImageReferenceVersion latest `
                                       -ImageReferencePublisher 'MicrosoftWindowsServer' `
                                       -ManagedDisk $osDiskSku

$vmss = Set-AzVmssOsProfile $vmss -ComputerNamePrefix $vmScaleSetName `
                                  -AdminUsername $vmLocalAdminUser `
                                  -AdminPassword $vmLocalAdminSecurePassword 

$vmss = Add-AzVmssDataDisk -VirtualMachineScaleSet $vmss `
                           -CreateOption Empty `
                           -Lun 1 `
                           -DiskSizeGB 128 `
                           -StorageAccountType $dataDiskSku

New-AzVmss -VirtualMachineScaleSet $vmss `
           -ResourceGroupName $rgName `
           -VMScaleSetName $vmScaleSetName
```

# <a name="resource-manager-template"></a>[Resource Manager-Vorlage](#tab/azure-resource-manager)

Verwenden Sie die `2020-12-01`-API mit Ihrer Azure Resource Manager-Vorlage, um einen ZRS-Datenträger zu erstellen.

#### <a name="prerequisites"></a>Voraussetzungen

Sie müssen das Features für Ihr Abonnement aktivieren. Führen Sie die folgenden Schritte aus, um das Feature für Ihr Abonnement zu aktivieren:

1.  Führen Sie den folgenden Befehl aus, um das Feature für Ihr Abonnement zu registrieren.

    ```powershell
     Register-AzProviderFeature -FeatureName "SsdZrsManagedDisks" -ProviderNamespace "Microsoft.Compute" 
    ```

1.  Überprüfen Sie mithilfe des folgenden Befehls, ob der Registrierungsstatus **Registriert** lautet (dies kann einige Minuten dauern), bevor Sie das Feature ausprobieren.

    ```powershell
     Get-AzProviderFeature -FeatureName "SsdZrsManagedDisks" -ProviderNamespace "Microsoft.Compute"  
    ```
    
#### <a name="create-a-vm-with-zrs-disks"></a>Erstellen einer VM mit ZRS-Datenträgern

```
$vmName = "yourVMName" 
$adminUsername = "yourAdminUsername"
$adminPassword = ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$osDiskType = "StandardSSD_ZRS"
$dataDiskType = "Premium_ZRS"
$region = "eastus2euap"
$resourceGroupName = "yourResourceGroupName"

New-AzResourceGroup -Name $resourceGroupName -Location $region
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMWithZRSDataDisks.json" `
-resourceName $vmName `
-adminUsername $adminUsername `
-adminPassword $adminPassword `
-region $region `
-osDiskType $osDiskType `
-dataDiskType $dataDiskType
```

#### <a name="create-vms-with-a-shared-zrs-disk-attached-to-the-vms-in-different-zones"></a>Erstellen von VMs mit einem freigegebenen ZRS-Datenträger, der an die VMs in verschiedenen Zonen angefügt ist

```
$vmNamePrefix = "yourVMNamePrefix"
$adminUsername = "yourAdminUserName"
$adminPassword = ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$osDiskType = "StandardSSD_LRS"
$sharedDataDiskType = "Premium_ZRS"
$region = "eastus2euap"
$resourceGroupName = "zrstesting1"

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMsWithASharedDisk.json" `
-vmNamePrefix $vmNamePrefix `
-adminUsername $adminUsername `
-adminPassword $adminPassword `
-region $region `
-osDiskType $osDiskType `
-dataDiskType $sharedDataDiskType
```

#### <a name="create-a-virtual-machine-scale-set-with-zrs-disks"></a>Erstellen einer VM-Skalierungsgruppe mit ZRS-Datenträgern

```
$vmssName="yourVMSSName"
$adminUsername="yourAdminName"
$adminPassword=ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$region="eastus2euap"
$osDiskType="StandardSSD_LRS"
$dataDiskType="Premium_ZRS"

New-AzResourceGroupDeployment -ResourceGroupName zrstesting `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMSSWithZRSDisks.json" `
-vmssName "yourVMSSName" `
-adminUsername "yourAdminName" `
-adminPassword $password `
-region "eastus2euap" `
-osDiskType "StandardSSD_LRS" `
-dataDiskType "Premium_ZRS" `
```
---

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich weitere [Azure Resource Manager-Beispielvorlagen zum Erstellen einer VM mit ZRS-Datenträgern](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/ZRSDisks) an.