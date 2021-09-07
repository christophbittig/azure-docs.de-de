---
title: Bereitstellen von VMs auf Ihrem Azure Stack Edge-Gerät über Azure PowerShell
description: Hier erfahren Sie, wie virtuelle Computer (Virtual Machines, VMs) auf einem Azure Stack Edge-Gerät mithilfe von Azure PowerShell erstellt und verwaltet werden.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/26/2021
ms.author: alkohli
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 32d2102bad49a3023df9b1d25f5b5378a3b30ad0
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/27/2021
ms.locfileid: "114721503"
---
# <a name="deploy-vms-on-your-azure-stack-edge-device-via-azure-powershell"></a>Bereitstellen von VMs auf Ihrem Azure Stack Edge-Gerät über Azure PowerShell

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In diesem Artikel wird beschrieben, wie Sie eine einen virtuellen Computer (Virtual Machine, VM) mithilfe von Azure PowerShell auf Ihrem Azure Stack Edge-Gerät erstellen und verwalten können.

## <a name="vm-deployment-workflow"></a>VM-Bereitstellungsworkflow

Nachfolgend sind die wichtigsten Schritte des VM-Bereitstellungsworkflows aufgeführt:

1. Stellen Sie eine Verbindung mit der lokalen Azure Resource Manager-Instanz auf Ihrem Gerät her.
1. Ermitteln Sie das integrierte Abonnement auf dem Gerät.
1. Stellen Sie Ihr VM-Image bereit.
1. Erstellen Sie eine Ressourcengruppe im integrierten Abonnement. Die Ressourcengruppe enthält die VM und alle zugehörigen Ressourcen.  
1. Erstellen Sie ein lokales Speicherkonto auf dem Gerät, um die VHD zu speichern, die zum Erstellen eines VM-Images verwendet wird.
1. Laden Sie ein Windows/Linux-Quellimage in das Speicherkonto hoch, um einen verwalteten Datenträger zu erstellen.
1. Verwenden Sie den verwalteten Datenträger, um ein VM-Image zu erstellen.
1. Aktivieren Sie Compute an einem Geräteport, um einen virtuellen Switch zu erstellen.
1. Dadurch wird ein virtuelles Netzwerk mit dem virtuellen Switch erstellt, der an den Port angefügt ist, an dem Sie Compute aktiviert haben.
1. Erstellen Sie eine VM mithilfe des zuvor erstellten VM-Images, des virtuellen Netzwerks und der virtuellen Netzwerkschnittstellen, um innerhalb des virtuellen Netzwerks zu kommunizieren, und weisen Sie eine öffentliche IP-Adresse für den Remotezugriff auf die VM zu. Schließen Sie optional Datenträger ein, um mehr Speicher für Ihre VM bereitzustellen.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [azure-stack-edge-gateway-deploy-vm-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]


## <a name="query-for-a-built-in-subscription-on-the-device"></a>Abfrage für ein integriertes Abonnement auf dem Gerät

Bei Azure Resource Manager wird nur ein einziges für Benutzer sichtbares festes Abonnement unterstützt. Dieses Abonnement ist pro Gerät eindeutig. Der Abonnementname und die Abonnement-ID können nicht geändert werden.

Das Abonnement enthält alle Ressourcen, die für eine VM-Erstellung erforderlich sind. 

> [!IMPORTANT]
> Das Abonnement wird erstellt, wenn Sie VMs im Azure-Portal aktivieren, und es befindet sich lokal auf Ihrem Gerät.

Das Abonnement wird zur Bereitstellung der VMs verwendet.

### <a name="az"></a>[Az](#tab/az)

1.  Führen Sie den folgenden Befehl zum Auflisten des Abonnements aus:

    ```powershell
    Get-AzSubscription
    ```
    
    Hier ist eine Beispielausgabe angegeben:

    ```output
    PS C:\WINDOWS\system32> Get-AzSubscription
    
    Name                          Id                                   TenantId
    ----                          --                                   --------
    Default Provider Subscription ...                                  ...
    
    
    PS C:\WINDOWS\system32>
    ```
        
1. Rufen Sie eine Liste der registrierten Ressourcenanbieter ab, die auf dem Gerät ausgeführt werden. Die Liste enthält normalerweise Compute, Netzwerk und Speicher.

    ```powershell
    Get-AzResourceProvider
    ```

    > [!NOTE]
    > Die Ressourcenanbieter wurden vorab registriert, und sie können nicht bearbeitet oder geändert werden.
    
    Hier ist eine Beispielausgabe angegeben:

    ```output
    PS C:\WINDOWS\system32>  Get-AzResourceProvider
        
    ProviderNamespace : Microsoft.AzureBridge
    RegistrationState : Registered
    ResourceTypes     : {locations, operations, locations/ingestionJobs}
    Locations         : {DBELocal}
    
    ProviderNamespace : Microsoft.Compute
    RegistrationState : Registered
    ResourceTypes     : {virtualMachines, virtualMachines/extensions, locations, operations...}
    Locations         : {DBELocal}
    
    ProviderNamespace : Microsoft.Network
    RegistrationState : Registered
    ResourceTypes     : {operations, locations, locations/operations, locations/usages...}
    Locations         : {DBELocal}
    
    ProviderNamespace : Microsoft.Resources
    RegistrationState : Registered
    ResourceTypes     : {tenants, locations, providers, checkresourcename...}
    Locations         : {DBELocal}
    
    ProviderNamespace : Microsoft.Storage
    RegistrationState : Registered
    ResourceTypes     : {storageaccounts, storageAccounts/blobServices, storageAccounts/tableServices,
                        storageAccounts/queueServices...}
    Locations         : {DBELocal}
    
    PS C:\WINDOWS\system32>
    ```

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

1.  Führen Sie den folgenden Befehl zum Auflisten des Abonnements aus:

    ```powershell
    Get-AzureRmSubscription
    ```
    
    Hier ist eine Beispielausgabe angegeben:

    ```output
    PS C:\windows\system32> Get-AzureRmSubscription
    
    Name                 Id                 TenantId          State
    ----                 --                --------           -----
    Default Provider Subscription         ...                 c0257de7-538f-415c-993a-1b87a031879d Enabled
    
    PS C:\windows\system32>
    ```
        
1. Rufen Sie eine Liste der registrierten Ressourcenanbieter ab, die auf dem Gerät ausgeführt werden. Die Liste enthält normalerweise Compute, Netzwerk und Speicher.

    ```powershell
    Get-AzureRMResourceProvider
    ```

    > [!NOTE]
    > Die Ressourcenanbieter wurden vorab registriert, und sie können nicht bearbeitet oder geändert werden.
    
    Hier ist eine Beispielausgabe angegeben:

    ```output
    PS C:\Windows\system32> Get-AzureRmResourceProvider
    ProviderNamespace : Microsoft.Compute
    RegistrationState : Registered
    ResourceTypes     : {virtualMachines, virtualMachines/extensions, locations, operations...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Network
    RegistrationState : Registered
    ResourceTypes     : {operations, locations, locations/operations, locations/usages...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Resources
    RegistrationState : Registered
    ResourceTypes     : {tenants, locations, providers, checkresourcename...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Storage
    RegistrationState : Registered
    ResourceTypes     : {storageaccounts, storageAccounts/blobServices, storageAccounts/tableServices,
                        storageAccounts/queueServices...}
    Locations         : {DBELocal}
    ZoneMappings      :
    ```
---
   
## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie zunächst eine neue Azure-Ressourcengruppe, und verwenden Sie diese als logischen Container für alle VM-bezogenen Ressourcen wie Speicherkonto, Datenträger, Netzwerkschnittstelle und verwalteten Datenträger.

> [!IMPORTANT]
> Alle Ressourcen werden im Speicherort des Geräts erstellt, der auf **DBELocal** festgelegt wird.

### <a name="az"></a>[Az](#tab/az)

1. Legen Sie einige Parameter fest.

    ```powershell
    $ResourceGroupName = "<Resource group name>" 
    ```
1. Erstellen Sie eine Ressourcengruppe für die Ressourcen, die Sie für die VM erstellen.
   
    ```powershell
    New-AzResourceGroup -Name $ResourceGroupName -Location DBELocal
    ```

    Hier ist eine Beispielausgabe angegeben:
    
    ```output
    PS C:\WINDOWS\system32> New-AzResourceGroup -Name myaseazrg -Location DBELocal
    
    ResourceGroupName : myaseazrg
    Location          : dbelocal
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/.../resourceGroups/myaseazrg
    
    PS C:\WINDOWS\system32>
    ```

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
New-AzureRmResourceGroup -Name <Resource group name> -Location DBELocal
```

Hier ist eine Beispielausgabe angegeben:

```output
New-AzureRmResourceGroup -Name rg191113014333 -Location DBELocal 
Successfully created Resource Group:rg191113014333
```
---

## <a name="create-a-storage-account"></a>Speicherkonto erstellen

Erstellen Sie mithilfe der im vorhergehenden Schritt erstellten Ressourcengruppe ein neues Speicherkonto. Dabei handelt es sich um ein lokales Speicherkonto, das Sie zum Hochladen des virtuellen Datenträgerimages für die VM verwenden.

### <a name="az"></a>[Az](#tab/az)

1. Legen Sie einige Parameter fest.

    ```powershell
    $StorageAccountName = "<Storage account name>"    
    ```

1. Erstellen Sie ein neues lokales Speicherkonto auf Ihrem Gerät.

    ```powershell
    New-AzStorageAccount -Name $StorageAccountName -ResourceGroupName $ResourceGroupName -Location DBELocal -SkuName Standard_LRS
    ```
    
    > [!NOTE]
    > Mithilfe von Azure Resource Manager können Sie nur lokale Speicherkonten erstellen, beispielsweise lokal redundanten Speicher (Standard oder Premium). Informationen zum Erstellen von mehrstufigen Speicherkonten finden Sie im [Tutorial: Übertragen von Daten über Speicherkonten mit Azure Stack Edge Pro mit GPU](azure-stack-edge-gpu-deploy-add-storage-accounts.md).

    Hier ist eine Beispielausgabe:
    
    ```output
    PS C:\WINDOWS\system32> New-AzStorageAccount -Name myaseazsa -ResourceGroupName myaseazrg -Location DBELocal -SkuName Standard_LRS
    
    StorageAccountName ResourceGroupName PrimaryLocation SkuName      Kind    AccessTier CreationTime
    ------------------ ----------------- --------------- -------      ----    ---------- ------------
    myaseazsa          myaseazrg         DBELocal        Standard_LRS Storage            6/10/2021 11:45...
    
    PS C:\WINDOWS\system32>
    ```

1. Rufen Sie den Speicherkontoschlüssel für das Konto ab, das Sie im vorherigen Schritt erstellt haben. Geben Sie bei Aufforderung den Namen der Ressourcengruppe und des Speicherkontos an.

    ```powershell
    Get-AzStorageAccountKey
    ``` 

    Hier ist eine Beispielausgabe:

    ```output
    PS C:\WINDOWS\system32> Get-AzStorageAccountKey
    
    cmdlet Get-AzStorageAccountKey at command pipeline position 1
    Supply values for the following parameters:
    (Type !? for Help.)
    ResourceGroupName: myaseazrg
    Name: myaseazsa
    
    KeyName Value                                                                                    Permissions
    ------- -----                                         ------
    key1    gv3OF57tuPDyzBNc1M7fhil2UAiiwnhTT6zgiwE3TlF/CD217Cvw2YCPcrKF47joNKRvzp44leUe5HtVkGx8RQ==   Full
    key2    kmEynIs3xnpmSxWbU41h5a7DZD7v4gGV3yXa2NbPbmhrPt10+QmE5PkOxxypeSqbqzd9si+ArNvbsqIRuLH2Lw==   Full
    
    PS C:\WINDOWS\system32>
    ```

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> Mithilfe von Azure Resource Manager können Sie nur lokale Speicherkonten erstellen, beispielsweise lokal redundanten Speicher (Standard oder Premium). Informationen zum Erstellen von mehrstufigen Speicherkonten finden Sie im [Tutorial: Übertragen von Daten über Speicherkonten mit Azure Stack Edge Pro mit GPU](./azure-stack-edge-gpu-deploy-add-storage-accounts.md).

Hier ist eine Beispielausgabe angegeben:

```output
New-AzureRmStorageAccount -Name sa191113014333  -ResourceGroupName rg191113014333 -SkuName Standard_LRS -Location DBELocal

ResourceGroupName      : rg191113014333
StorageAccountName     : sa191113014333
Id                     : /subscriptions/.../resourceGroups/rg191113014333/providers/Microsoft.Storage/storageaccounts/sa191113014333
Location               : DBELocal
Sku                    : Microsoft.Azure.Management.Storage.Models.Sku
Kind                   : Storage
Encryption             : Microsoft.Azure.Management.Storage.Models.Encryption
AccessTier             :
CreationTime           : 11/13/2019 9:43:49 PM
CustomDomain           :
Identity               :
LastGeoFailoverTime    :
PrimaryEndpoints       : Microsoft.Azure.Management.Storage.Models.Endpoints
PrimaryLocation        : DBELocal
ProvisioningState      : Succeeded
SecondaryEndpoints     :
SecondaryLocation      :
StatusOfPrimary        : Available
StatusOfSecondary      :
Tags                   :
EnableHttpsTrafficOnly : False
NetworkRuleSet         :
Context                : Microsoft.WindowsAzure.Commands.Common.Storage.LazyAzureStorageContext
ExtendedProperties     : {}
```

Führen Sie den Befehl `Get-AzureRmStorageAccountKey` aus, um den Speicherkontoschlüssel abzurufen. Hier ist eine Beispielausgabe angegeben:

```output
PS C:\windows\system32> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: my-resource-ase
Name:myasestoracct

KeyName Value
------- -----
key1 /IjVJN+sSf7FMKiiPLlDm8mc9P4wtcmhhbnCa7...
key2 gd34TcaDzDgsY9JtDNMUgLDOItUU0Qur3CBo6Q...
```
---

## <a name="add-the-blob-uri-to-the-host-file"></a>Hinzufügen des Blob-URI zur Hostdatei

Sie haben den Blob-URI bereits im Schritt **Ändern der Hostdatei für die Endpunktnamensauflösung** unter [Herstellen einer Verbindung mit Azure Resource Manager auf Ihrem Azure Stack Edge Pro-Gerät](./azure-stack-edge-gpu-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution) in der Hostdatei für den Client hinzugefügt, den Sie für die Verbindung mit Azure Blob Storage verwenden. Dieser Eintrag wurde verwendet, um den BLOB-URI hinzuzufügen:

`<Device IP address>` `<storage name>.blob.<appliance name>.<dnsdomain>`

## <a name="install-certificates"></a>Installieren von Zertifikaten

Wenn Sie HTTPS verwenden, müssen Sie die erforderlichen Zertifikate auf Ihrem Gerät installieren. Hier installieren Sie das Blobendpunktzertifikat. Weitere Informationen finden Sie unter [Verwenden von Zertifikaten bei Ihrem Azure Stack Edge Pro mit GPU-Gerät](azure-stack-edge-gpu-manage-certificates.md).

## <a name="upload-a-vhd"></a>Hochladen einer VHD-Datei

Kopieren Sie die zu verwendenden Datenträgerimages in Seitenblobs in dem lokalen Speicherkonto, das Sie vorher erstellt haben. Sie können ein Tool wie [AzCopy](../storage/common/storage-use-azcopy-v10.md) zum Hochladen der virtuellen Festplatte (Virtual Hard Disk, VHD) in das Speicherkonto verwenden. 


### <a name="az"></a>[Az](#tab/az)

Verwenden Sie mit AzCopy 10 die folgenden Befehle:  

1. Legen Sie einige Parameter fest (u. a. die geeignete Version der APIs für AzCopy). In diesem Beispiel wurde AzCopy 10 verwendet.

    ```powershell
    $Env:AZCOPY_DEFAULT_SERVICE_API_VERSION="2019-07-07"    
    $ContainerName = <Container name>
    $ResourceGroupName = <Resource group name>
    $StorageAccountName = <Storage account name>
    $VHDPath = "Full VHD Path"
    $VHDFile = <VHD file name>
    ```
1. Kopieren Sie die VHD aus der Quelle (in diesem Fall das lokale System) in das Speicherkonto, das Sie im vorherigen Schritt auf Ihrem Gerät erstellt haben.

    ```powershell
    $StorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName)[0].Value    
    $endPoint = (Get-AzStorageAccount -name $StorageAccountName -ResourceGroupName $ResourceGroupName).PrimaryEndpoints.Blob    
    $StorageAccountContext = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -Endpoint $endpoint    
    $StorageAccountSAS = New-AzStorageAccountSASToken -Service Blob -ResourceType Container,Service,Object -Permission "acdlrw" -Context $StorageAccountContext -Protocol HttpsOnly        
    <Path to azcopy.exe> cp "$VHDPath\$VHDFile" "$endPoint$ContainerName$StorageAccountSAS"    
    ```

    Hier ist eine Beispielausgabe: 
    
    ```output
    PS C:\windows\system32> $ContainerName = "testcontainer1"
    PS C:\windows\system32> $ResourceGroupName = "myaseazrg"
    PS C:\windows\system32> $StorageAccountName = "myaseazsa"
    PS C:\windows\system32> $VHDPath = "C:\Users\alkohli\Downloads\Ubuntu1604"           
    PS C:\windows\system32> $VHDFile = "ubuntu13.vhd"
    
    PS C:\windows\system32> $StorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName)[0].Value
    PS C:\windows\system32> $endPoint = (Get-AzStorageAccount -name $StorageAccountName -ResourceGroupName $ResourceGroupName).PrimaryEndpoints.Blob
    PS C:\windows\system32> $StorageAccountContext = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -Endpoint $endpoint
    PS C:\windows\system32> $StorageAccountSAS = New-AzStorageAccountSASToken -Service Blob -ResourceType Container,Service,Object -Permission "acdlrw" -Context $StorageAccountContext -Protocol HttpsOnly

    PS C:\windows\system32> C:\azcopy\azcopy_windows_amd64_10.10.0\azcopy.exe cp "$VHDPath\$VHDFile" "$endPoint$ContainerName$StorageAccountSAS"
    INFO: Scanning...
    INFO: Any empty folders will not be processed, because source and/or destination doesn't have full folder support
    
    Job 72a5e3dd-9210-3e43-6691-6bebd4875760 has started
    Log file is located at: C:\Users\alkohli\.azcopy\72a5e3dd-9210-3e43-6691-6bebd4875760.log
    
    INFO: azcopy.exe: A newer version 10.11.0 is available to download
    ```

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

Verwenden Sie mit AzCopy 10 die folgenden Befehle:  

```powershell
$StorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName <ResourceGroupName> -Name <StorageAccountName>)[0].Value

$endPoint = (Get-AzureRmStorageAccount -name <StorageAccountName> -ResourceGroupName <ResourceGroupName>).PrimaryEndpoints.Blob

$StorageAccountContext = New-AzureStorageContext -StorageAccountName <StorageAccountName> -StorageAccountKey <StorageAccountKey> -Endpoint <Endpoint>

$StorageAccountSAS = New-AzureStorageAccountSASToken -Service Blob,File,Queue,Table -ResourceType Container,Service,Object -Permission "acdlrw" -Context <StorageAccountContext> -Protocol HttpsOnly

<AzCopy exe path> cp "Full VHD path" "<BlobEndPoint>/<ContainerName><StorageAccountSAS>"
```

Hier ist eine Beispielausgabe angegeben: 

```output
$ContainerName = <Container name>
$ResourceGroupName = <Resource group name>
$StorageAccountName = <Storage account name>
$VHDPath = "Full VHD path"
$VHDFile = <VHD file name>

$StorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName)[0].Value

$endPoint = (Get-AzureRmStorageAccount -name $StorageAccountName -resourcegroupname $ResourceGroupName).PrimaryEndpoints.Blob

$StorageAccountContext = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -Endpoint $endPoint

$StorageAccountSAS = New-AzureStorageAccountSASToken -Service Blob,File,Queue,Table -ResourceType Container,Service,Object -Permission "acdlrw" -Context $StorageAccountContext -Protocol HttpsOnly

C:\AzCopy.exe  cp "$VHDPath\$VHDFile" "$endPoint$ContainerName$StorageAccountSAS"
```
---

## <a name="create-a-managed-disk-from-the-vhd"></a>Erstellen verwalteter Datenträger aus der VHD

Nun erstellen Sie einen verwalteten Datenträger aus der hochgeladenen VHD.

### <a name="az"></a>[Az](#tab/az)

1. Legen Sie einige Parameter fest.

    ```powershell
    $DiskName = "<Managed disk name>"
    ```

1. Erstellen Sie einen verwalteten Datenträger aus der hochgeladenen VHD. Um die Quell-URL für Ihre VHD abzurufen, wechseln Sie zu dem Container im Speicherkonto, der die VHD im Storage-Explorer enthält. Klicken Sie mit der rechten Maustaste auf die VHD, und wählen Sie **Eigenschaften** aus. Wählen Sie im Dialogfeld **Blobeigenschaften** den **URI** aus. 

    ```powershell
    $StorageAccountId = (Get-AzStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName).Id    
    $DiskConfig = New-AzDiskConfig -Location DBELocal -StorageAccountId $StorageAccountId -CreateOption Import -SourceUri "Source URL for your VHD"
    New-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName -Disk $DiskConfig
    ```
    Hier ist eine Beispielausgabe:
    
    ```output
    PS C:\WINDOWS\system32> $DiskName = "myazmd"
    PS C:\WINDOWS\system32> $StorageAccountId = (Get-AzStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName).Id
    PS C:\WINDOWS\system32> $DiskConfig = New-AzDiskConfig -Location DBELocal -StorageAccountId $StorageAccountId -CreateOption Import -SourceUri "https://myaseazsa.blob.myasegpu.wdshcsso.com/testcontainer1/ubuntu13.vhd"
    PS C:\WINDOWS\system32> New-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName -Disk $DiskConfig
    
    ResourceGroupName            : myaseazrg
    ManagedBy                    :
    Sku                          : Microsoft.Azure.Management.Compute.Models.DiskSku
    Zones                        :
    TimeCreated                  : 6/24/2021 12:19:56 PM
    OsType                       :
    HyperVGeneration             :
    CreationData                 : Microsoft.Azure.Management.Compute.Models.CreationDat
                                   a
    DiskSizeGB                   : 30
    DiskSizeBytes                : 32212254720
    UniqueId                     : 53743801-cbf2-4d2f-acb4-971d037a9395
    EncryptionSettingsCollection :
    ProvisioningState            : Succeeded
    DiskIOPSReadWrite            : 500
    DiskMBpsReadWrite            : 60
    DiskState                    : Unattached
    Encryption                   : Microsoft.Azure.Management.Compute.Models.Encryption
    Id                           : /subscriptions/.../r
                                   esourceGroups/myaseazrg/providers/Microsoft.Compute/d
                                   isks/myazmd
    Name                         : myazmd
    Type                         : Microsoft.Compute/disks
    Location                     : DBELocal
    Tags                         : {}
    
    PS C:\WINDOWS\system32>
    ```
 

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri "Source URL for your VHD"
```
Hier ist eine Beispielausgabe angegeben: 

```output
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import –SourceUri http://sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages/ubuntu13.vhd
```
 

```powershell
New-AzureRMDisk -ResourceGroupName <Resource group name> -DiskName <Disk name> -Disk $DiskConfig
```

Hier ist eine Beispielausgabe angegeben. Weitere Informationen zu diesem Cmdlet finden Sie unter [New-AzureRmDisk](/powershell/module/azurerm.compute/new-azurermdisk?view=azurermps-6.13.0&preserve-view=true).

```output
Tags               : New-AzureRmDisk -ResourceGroupName rg191113014333 -DiskName ld191113014333 -Disk $DiskConfig

ResourceGroupName  : rg191113014333
ManagedBy          :
Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
Zones              :
TimeCreated        : 11/13/2019 1:49:07 PM
OsType             :
CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
DiskSizeGB         : 30
EncryptionSettings :
ProvisioningState  : Succeeded
Id                 : /subscriptions/.../resourceGroups/rg191113014333/providers/Micros
                     oft.Compute/disks/ld191113014333
Name               : ld191113014333
Type               : Microsoft.Compute/disks
Location           : DBELocal
Tags               : {}
```
---

## <a name="create-a-vm-image-from-the-managed-disk"></a>Erstellen eines VM-Images aus dem verwalteten Datenträger

Sie erstellen nun ein VM-Image aus dem verwalteten Datenträger.

### <a name="az"></a>[Az](#tab/az)

1. Legen Sie einige Parameter fest.

    ```powershell
    $DiskSize = "<Size greater than or equal to size of source managed disk>"
    $OsType = "<linux or windows>" 
    $ImageName = "<Image name>"
    ```
1. Erstellen Sie ein VM-Image. Die unterstützten Betriebssystemtypen sind Linux und Windows.

    ```powershell
    $imageConfig = New-AzImageConfig -Location DBELocal
    $ManagedDiskId = (Get-AzDisk -Name $DiskName -ResourceGroupName $ResourceGroupName).Id
    Set-AzImageOsDisk -Image $imageConfig -OsType $OsType -OsState 'Generalized' -DiskSizeGB $DiskSize -ManagedDiskId $ManagedDiskId 
    New-AzImage -Image $imageConfig -ImageName $ImageName -ResourceGroupName $ResourceGroupName  
    ```  
    Hier ist eine Beispielausgabe: 

    ```output
    PS C:\WINDOWS\system32> $OsType = "linux"
    PS C:\WINDOWS\system32> $ImageName = "myaseazlinuxvmimage"
    PS C:\WINDOWS\system32> $DiskSize = 35
    PS C:\WINDOWS\system32> $imageConfig = New-AzImageConfig -Location DBELocal
    PS C:\WINDOWS\system32> $ManagedDiskId = (Get-AzDisk -Name $DiskName -ResourceGroupName $ResourceGroupName).Id
    PS C:\WINDOWS\system32> Set-AzImageOsDisk -Image $imageConfig -OsType $OsType -OsState 'Generalized' -DiskSizeGB $DiskSize -ManagedDiskId $ManagedDiskId
    
    ResourceGroupName    :
    SourceVirtualMachine :
    StorageProfile       : Microsoft.Azure.Management.Compute.Models.ImageStorageProfile
    ProvisioningState    :
    HyperVGeneration     : V1
    Id                   :
    Name                 :
    Type                 :
    Location             : DBELocal
    Tags                 :
    
    PS C:\WINDOWS\system32> New-AzImage -Image $imageConfig -ImageName $ImageName -ResourceGroupName $ResourceGroupName
    
    ResourceGroupName    : myaseazrg
    SourceVirtualMachine :
    StorageProfile       : Microsoft.Azure.Management.Compute.Models.ImageStorageProfile
    ProvisioningState    : Succeeded
    HyperVGeneration     : V1
    Id                   : /subscriptions/.../resourceG
                           roups/myaseazrg/providers/Microsoft.Compute/images/myaseazlin
                           uxvmimage
    Name                 : myaseazlinuxvmimage
    Type                 : Microsoft.Compute/images
    Location             : dbelocal
    Tags                 : {}
    
    PS C:\WINDOWS\system32> 
    ```

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

Führen Sie den folgenden Befehl aus. Ersetzen Sie *\<Disk name>* , *\<OS type>* und *\<Disk size>* durch echte Werte.

```powershell
$imageConfig = New-AzureRmImageConfig -Location DBELocal
$ManagedDiskId = (Get-AzureRmDisk -Name <Disk name> -ResourceGroupName <Resource group name>).Id
Set-AzureRmImageOsDisk -Image $imageConfig -OsType '<OS type>' -OsState 'Generalized' -DiskSizeGB <Disk size> -ManagedDiskId $ManagedDiskId 
New-AzureRmImage -Image $imageConfig -ImageName <Image name>  -ResourceGroupName <Resource group name>
```

Die unterstützten Betriebssystemtypen sind Linux und Windows.

Hier ist eine Beispielausgabe. Weitere Informationen zu diesem Cmdlet finden Sie unter [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage?view=azurermps-6.13.0&preserve-view=true).

```output
PS C:\Windows\system32> New-AzImage -Image $imageConfig -ImageName ig191113014333    -ResourceGroupName RG191113014333
ResourceGroupName    : RG191113014333
SourceVirtualMachine :
StorageProfile       : Microsoft.Azure.Management.Compute.Models.ImageStorageProfile
ProvisioningState    : Succeeded
HyperVGeneration     : V1
Id                   : /subscriptions/.../resourceGroups/RG191113014333/providers/Microsoft.Compute/images/ig191113014333
Name                 : ig191113014333
Type                 : Microsoft.Compute/images
Location             : dbelocal
Tags                 : {}
```
---

## <a name="create-your-vm-with-previously-created-resources"></a>Erstellen Ihres virtuellen Computers mit zuvor erstellten Ressourcen

Bevor Sie den virtuellen Computer erstellen und bereitstellen, müssen Sie ein einziges virtuelles Netzwerk erstellen und ihm eine virtuelle Netzwerkschnittstelle zuordnen.

> [!IMPORTANT]
> Es gelten die folgenden Regeln:
> - Sie können nur ein einziges virtuelles Netzwerk erstellen, sogar über Ressourcengruppen hinweg. Das virtuelle Netzwerk muss genau denselben Adressraum wie das logische Netzwerk haben.
> - Das virtuelle Netzwerk kann nur ein einzelnes Subnetz enthalten. Das Subnetz muss genau denselben Adressraum wie das virtuelle Netzwerk haben.
> - Wenn Sie die virtuelle Netzwerkschnittstellenkarte erstellen, können Sie nur die statische Zuordnungsmethode verwenden. Der Benutzer muss eine private IP-Adresse angeben.<!--Confirm w/ Neeraj given we can have both static and DHCP-->

### <a name="query-the-automatically-created-virtual-network"></a>Abfragen des automatisch erstellten virtuellen Netzwerks

Wenn Sie Compute über die lokale Benutzeroberfläche Ihres Geräts aktivieren, wird in der Ressourcengruppe `ASERG` das virtuelle Netzwerk `ASEVNET` automatisch erstellt. 

### <a name="az"></a>[Az](#tab/az)

Fragen Sie mit dem folgenden Befehl das vorhandene virtuelle Netzwerk ab:

```powershell
$ArmVn = Get-AzVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG 
```

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

Fragen Sie mit dem folgenden Befehl das vorhandene virtuelle Netzwerk ab:

```powershell
$aRmVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG 
```
---

### <a name="create-a-virtual-network-interface-card"></a>Erstellen einer virtuellen Netzwerkadapterkarte

Sie erstellen eine virtuelle Netzwerkschnittstellenkarte mithilfe der Subnetz-ID des virtuellen Netzwerks.

### <a name="az"></a>[Az](#tab/az)

1. Legen Sie einige Parameter fest.

    ```powershell
    $IpConfigName = "<IP config name>"
    $NicName = "<Network interface name>"
    ```

1. Erstellen Sie eine virtuelle Netzwerkschnittstelle.

    ```powershell
    $ipConfig = New-AzNetworkInterfaceIpConfig -Name $IpConfigName -SubnetId $aRmVN.Subnets[0].Id 
    $Nic = New-AzNetworkInterface -Name $NicName -ResourceGroupName $ResourceGroupName -Location DBELocal -IpConfiguration $IpConfig    
    ```

    Standardmäßig wird Ihrer Netzwerkschnittstelle über das für Compute aktivierte Netzwerk dynamisch eine IP-Adresse zugewiesen. Verwenden Sie den `-PrivateIpAddress parameter`, wenn Sie Ihrer Netzwerkschnittstelle eine statische IP-Adresse zuordnen.         

    Hier ist eine Beispielausgabe:
    
    ```output
    PS C:\WINDOWS\system32> $IpConfigName = "myazipconfig1"
    PS C:\WINDOWS\system32> $NicName = "myaznic1"
    PS C:\WINDOWS\system32> $ipConfig = New-AzNetworkInterfaceIpConfig -Name $IpConfigName -SubnetId $aRmVN.Subnets[0].Id 
    PS C:\WINDOWS\system32> $ipConfig = New-AzNetworkInterfaceIpConfig -Name $IpConfigName -SubnetId $aRmVN.Subnets[0].Id
    PS C:\WINDOWS\system32> $Nic = New-AzNetworkInterface -Name $NicName -ResourceGroupName $ResourceGroupName -Location DBELocal -IpConfiguration $IpConfig
    PS C:\WINDOWS\system32> $Nic
        
    Name                        : myaznic1
    ResourceGroupName           : myaseazrg
    Location                    : dbelocal
    Id                          : /subscriptions/.../re
                                  sourceGroups/myaseazrg/providers/Microsoft.Network/net
                                  workInterfaces/myaznic1
    Etag                        : W/"0b20057b-2102-4f34-958b-656327c0fb1d"
    ResourceGuid                : e7d4131f-6f01-4492-9d4c-a8ff1af7244f
    ProvisioningState           : Succeeded
    Tags                        :
    VirtualMachine              : null
    IpConfigurations            : [
                                    {
                                      "Name": "myazipconfig1",
                                      "Etag":
                                  "W/\"0b20057b-2102-4f34-958b-656327c0fb1d\"",
                                      "Id": "/subscriptions/.../resourceGroups/myaseazrg/providers/Microsoft.
                                  Network/networkInterfaces/myaznic1/ipConfigurations/my
                                  azipconfig1",
                                      "PrivateIpAddress": "10.126.76.60",
                                      "PrivateIpAllocationMethod": "Dynamic",
                                      "Subnet": {
                                        "Delegations": [],
                                        "Id": "/subscriptions/.../resourceGroups/ASERG/providers/Microsoft.Ne
                                  twork/virtualNetworks/ASEVNET/subnets/ASEVNETsubNet",
                                        "ServiceAssociationLinks": []
                                      },
                                      "ProvisioningState": "Succeeded",
                                      "PrivateIpAddressVersion": "IPv4",
                                      "LoadBalancerBackendAddressPools": [],
                                      "LoadBalancerInboundNatRules": [],
                                      "Primary": true,
                                      "ApplicationGatewayBackendAddressPools": [],
                                      "ApplicationSecurityGroups": []
                                    }
                                  ]
    DnsSettings                 : {
                                    "DnsServers": [],
                                    "AppliedDnsServers": [],
                                    "InternalDomainNameSuffix": "auwlfcx0dhxurjgisct43fc
                                  ywb.a--x.internal.cloudapp.net"
                                  }
    EnableIPForwarding          : False
    EnableAcceleratedNetworking : False
    NetworkSecurityGroup        : null
    Primary                     :
    MacAddress                  : 001DD84A58D1
           
    PS C:\WINDOWS\system32>
    ```

Optional können Sie beim Erstellen einer virtuellen Netzwerkschnittstellenkarte für eine VM die öffentliche IP-Adresse übergeben. In dieser Instanz gibt die öffentliche IP-Adresse die private IP-Adresse zurück. 

```powershell
New-AzPublicIPAddress -Name <Public IP> -ResourceGroupName <ResourceGroupName> -AllocationMethod Static -Location DBELocal
$publicIP = (Get-AzPublicIPAddress -Name <Public IP> -ResourceGroupName <Resource group name>).Id
$ipConfig = New-AzNetworkInterfaceIpConfig -Name <ConfigName> -PublicIpAddressId $publicIP -SubnetId $subNetId
```

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <IP config Name> -SubnetId $aRmVN.Subnets[0].Id -PrivateIpAddress <Private IP>
$Nic = New-AzureRmNetworkInterface -Name <Nic name> -ResourceGroupName <Resource group name> -Location DBELocal -IpConfiguration $ipConfig
```

Standardmäßig wird Ihrer Netzwerkschnittstelle über das für Compute aktivierte Netzwerk dynamisch eine IP-Adresse zugewiesen. Verwenden Sie den `-PrivateIpAddress parameter`, wenn Sie Ihrer Netzwerkschnittstelle eine statische IP-Adresse zuordnen. 

Hier ist eine Beispielausgabe angegeben:

```output
PS C:\windows\system32> $subNetId=New-AzureRmVirtualNetworkSubnetConfig -Name my-ase-subnet -AddressPrefix "5.5.0.0/16"

PS C:\windows\system32> $aRmVN = New-AzureRmVirtualNetwork -ResourceGroupName Resource-my-ase -Name my-ase-virtualnetwork -Location DBELocal -AddressPrefix "5.5.0.0/16" -Subnet $subNetId
WARNING: The output object type of this cmdlet will be modified in a future release.
PS C:\windows\system32> $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name my-ase-ip -SubnetId $aRmVN.Subnets[0].Id
PS C:\windows\system32> $Nic = New-AzureRmNetworkInterface -Name my-ase-nic -ResourceGroupName Resource-my-ase -Location DBELocal -IpConfiguration $ipConfig
WARNING: The output object type of this cmdlet will be modified in a future release.

PS C:\windows\system32> $Nic

PS C:\windows\system32> (Get-AzureRmNetworkInterface)[0]

Name                        : nic200108020444
ResourceGroupName           : rg200108020444
Location                    : dbelocal
Id                          : /subscriptions/.../resourceGroups/rg200108020444/providers/Microsoft.Network/networ
                              kInterfaces/nic200108020444
Etag                        : W/"f9d1759d-4d49-42fa-8826-e218e0b1d355"
ResourceGuid                : 3851ae62-c13e-4416-9386-e21d9a2fef0f
ProvisioningState           : Succeeded
Tags                        :
VirtualMachine              : {
                                "Id": "/subscriptions/.../resourceGroups/rg200108020444/providers/Microsoft.Compu
                              te/virtualMachines/VM200108020444"
                              }
IpConfigurations            : [
                                {
                                  "Name": "ip200108020444",
                                  "Etag": "W/\"f9d1759d-4d49-42fa-8826-e218e0b1d355\"",
                                  "Id": "/subscriptions/.../resourceGroups/rg200108020444/providers/Microsoft.Net
                              work/networkInterfaces/nic200108020444/ipConfigurations/ip200108020444",
                                  "PrivateIpAddress": "5.5.166.65",
                                  "PrivateIpAllocationMethod": "Static",
                                  "Subnet": {
                                    "Id": "/subscriptions/.../resourceGroups/DbeSystemRG/providers/Microsoft.Netw
                              ork/virtualNetworks/vSwitch1/subnets/subnet123",
                                    "ResourceNavigationLinks": [],
                                    "ServiceEndpoints": []
                                  },
                                  "ProvisioningState": "Succeeded",
                                  "PrivateIpAddressVersion": "IPv4",
                                  "LoadBalancerBackendAddressPools": [],
                                  "LoadBalancerInboundNatRules": [],
                                  "Primary": true,
                                  "ApplicationGatewayBackendAddressPools": [],
                                  "ApplicationSecurityGroups": []
                                }
                              ]
DnsSettings                 : {
                                "DnsServers": [],
                                "AppliedDnsServers": []
                              }
EnableIPForwarding          : False
EnableAcceleratedNetworking : False
NetworkSecurityGroup        : null
Primary                     : True
MacAddress                  : 00155D18E432                :
```

Optional können Sie beim Erstellen einer virtuellen Netzwerkschnittstellenkarte für eine VM die öffentliche IP-Adresse übergeben. In dieser Instanz gibt die öffentliche IP-Adresse die private IP-Adresse zurück. 

```powershell
New-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <ResourceGroupName> -AllocationMethod Static -Location DBELocal
$publicIP = (Get-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <Resource group name>).Id
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <ConfigName> -PublicIpAddressId $publicIP -SubnetId $subNetId
```
---

### <a name="create-a-vm"></a>Erstellen einer VM

Sie können jetzt das VM-Image verwenden, um einen virtuellen Computer zu erstellen und dem zuvor erstellten virtuellen Netzwerk anzufügen.

### <a name="az"></a>[Az](#tab/az)

1. Legen Sie den Benutzernamen und das Kennwort für die Anmeldung bei dem virtuellen Computer fest, den Sie erstellen möchten.

    ```powershell
    $pass = ConvertTo-SecureString "<Password>" -AsPlainText -Force;
    $cred = New-Object System.Management.Automation.PSCredential("<Enter username>", $pass)
    ```
    Nachdem Sie die VM erstellt und eingeschaltet haben, verwenden Sie den vorstehenden Benutzernamen und das zugehörige Kennwort, um sich bei der VM anzumelden.

1. Legen Sie die Parameter fest.

    ```powershell
    $VmName = "<VM name>"
    $ComputerName = "<VM display name>"
    $OsDiskName = "<OS disk name>"
    ```
1. Erstellen Sie den virtuellen Computer.

    ```powershell
    $VirtualMachine =  New-AzVMConfig -VmName $VmName -VMSize "Standard_D1_v2"
 
    $VirtualMachine =  Set-AzVMOperatingSystem -VM $VirtualMachine -Linux -ComputerName $ComputerName -Credential $cred
     
    $VirtualMachine =  Set-AzVmOsDisk -VM $VirtualMachine -Name $OsDiskName -Caching "ReadWrite" -CreateOption "FromImage" -Linux -StorageAccountType Standard_LRS
     
    $nicID = (Get-AzNetworkInterface -Name $NicName -ResourceGroupName $ResourceGroupName).Id
     
    $VirtualMachine =  Add-AzVMNetworkInterface -Vm $VirtualMachine -Id $nicID
     
    $image = ( Get-AzImage -ResourceGroupName $ResourceGroupName -ImageName $ImageName).Id
     
    $VirtualMachine =  Set-AzVMSourceImage -VM $VirtualMachine -Id $image
     
    New-AzVM -ResourceGroupName $ResourceGroupName -Location DBELocal -VM $VirtualMachine -Verbose
    ```

    Hier ist eine Beispielausgabe:
    
    ```powershell
    PS C:\WINDOWS\system32> $pass = ConvertTo-SecureString "Password1" -AsPlainText -Force;
    PS C:\WINDOWS\system32> $cred = New-Object System.Management.Automation.PSCredential("myazuser", $pass)
    PS C:\WINDOWS\system32> $VmName = "myazvm"
    >> $ComputerName = "myazvmfriendlyname"
    >> $OsDiskName = "myazosdisk1"
    PS C:\WINDOWS\system32> $VirtualMachine =  New-AzVMConfig -VmName $VmName -VMSize "Standard_D1_v2"
    PS C:\WINDOWS\system32> $VirtualMachine =  Set-AzVMOperatingSystem -VM $VirtualMachine -Linux -ComputerName $ComputerName -Credential $cred
    PS C:\WINDOWS\system32> $VirtualMachine =  Set-AzVmOsDisk -VM $VirtualMachine -Name $OsDiskName -Caching "ReadWrite" -CreateOption "FromImage" -Linux -StorageAccountType Standard_LRS
    PS C:\WINDOWS\system32> $nicID = (Get-AzNetworkInterface -Name $NicName -ResourceGroupName $ResourceGroupName).Id
    PS C:\WINDOWS\system32> $nicID/subscriptions/.../resourceGroups/myaseazrg/providers/Microsoft.Network/networkInterfaces/myaznic1
    PS C:\WINDOWS\system32> $VirtualMachine =  Add-AzVMNetworkInterface -VM $VirtualMachine -Id $nicID
    PS C:\WINDOWS\system32> $image = ( Get-AzImage -ResourceGroupName $ResourceGroupName -ImageName $ImageName).Id
    PS C:\WINDOWS\system32> $VirtualMachine =  Set-AzVMSourceImage -VM $VirtualMachine -Id $image
    PS C:\WINDOWS\system32> New-AzVM -ResourceGroupName $ResourceGroupName -Location DBELocal -VM $VirtualMachine -Verbose
    WARNING: Since the VM is created using premium storage or managed disk, existing
    standard storage account, myaseazsa, is used for boot diagnostics.
    VERBOSE: Performing the operation "New" on target "myazvm".
    
    RequestId IsSuccessStatusCode StatusCode ReasonPhrase
    --------- ------------------- ---------- ------------
                             True         OK OK
    ```
1. Fragen Sie die erstellte virtuelle Netzwerkschnittstelle ab, um die IP-Adresse zu ermitteln, die dem erstellten virtuellen Computer zugewiesen ist. Suchen Sie nach `PrivateIPAddress`, und kopieren Sie die IP für Ihre VM. Hier ist eine Beispielausgabe:

    ```powershell
    PS C:\WINDOWS\system32> $Nic

    Name                        : myaznic1
    ResourceGroupName           : myaseazrg
    Location                    : dbelocal
    Id                          : /subscriptions/.../re
                                  sourceGroups/myaseazrg/providers/Microsoft.Network/net
                                  workInterfaces/myaznic1
    Etag                        : W/"0b20057b-2102-4f34-958b-656327c0fb1d"
    ResourceGuid                : e7d4131f-6f01-4492-9d4c-a8ff1af7244f
    ProvisioningState           : Succeeded
    Tags                        :
    VirtualMachine              : null
    IpConfigurations            : [
                                    {
                                      "Name": "myazipconfig1",
                                      "Etag":
                                  "W/\"0b20057b-2102-4f34-958b-656327c0fb1d\"",
                                      "Id": "/subscriptions/.../resourceGroups/myaseazrg/providers/Microsoft.
                                  Network/networkInterfaces/myaznic1/ipConfigurations/my
                                  azipconfig1",
                                      "PrivateIpAddress": "10.126.76.60",
                                      "PrivateIpAllocationMethod": "Dynamic",
                                      "Subnet": {
                                        "Delegations": [],
                                        "Id": "/subscriptions/.../resourceGroups/ASERG/providers/Microsoft.Ne
                                  twork/virtualNetworks/ASEVNET/subnets/ASEVNETsubNet",
                                        "ServiceAssociationLinks": []
                                      },
                                      "ProvisioningState": "Succeeded",
                                      "PrivateIpAddressVersion": "IPv4",
                                      "LoadBalancerBackendAddressPools": [],
                                      "LoadBalancerInboundNatRules": [],
                                      "Primary": true,
                                      "ApplicationGatewayBackendAddressPools": [],
                                      "ApplicationSecurityGroups": []
                                    }
                                  ]
    DnsSettings                 : {
                                    "DnsServers": [],
                                    "AppliedDnsServers": [],
                                    "InternalDomainNameSuffix": "auwlfcx0dhxurjgisct43fc
                                  ywb.a--x.internal.cloudapp.net"
                                  }
    EnableIPForwarding          : False
    EnableAcceleratedNetworking : False
    NetworkSecurityGroup        : null
    Primary                     :
    MacAddress                  : 001DD84A58D1
      
    PS C:\WINDOWS\system32>
    ```


### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
$pass = ConvertTo-SecureString "<Password>" -AsPlainText -Force;
$cred = New-Object System.Management.Automation.PSCredential("<Enter username>", $pass)
```

Nachdem Sie die VM erstellt und eingeschaltet haben, verwenden Sie den folgenden Benutzernamen und das zugehörige Kennwort, um sich bei ihr anzumelden.

```powershell
$VirtualMachine = New-AzureRmVMConfig -VMName <VM name> -VMSize "Standard_D1_v2"

$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -<OS type> -ComputerName <Your computer Name> -Credential $cred

$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name <OS Disk Name> -Caching "ReadWrite" -CreateOption "FromImage" -Linux -StorageAccountType StandardLRS

$nicID = (Get-AzureRmNetworkInterface -Name <nic name> -ResourceGroupName <Resource Group Name>).Id

$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nicID

$image = (Get-AzureRmImage -ResourceGroupName <Resource Group Name> -ImageName $ImageName).Id

$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -Id $image

New-AzureRmVM -ResourceGroupName <Resource Group Name> -Location DBELocal -VM $VirtualMachine -Verbose
```
---

## <a name="connect-to-the-vm"></a>Herstellen der Verbindung zur VM

Je nachdem, ob Sie einen virtuellen Windows-Computer (Windows-VM) oder einen virtuellen Linux-Computer (Linux-VM) erstellt haben, können die Verbindungsanweisungen unterschiedlich sein.

## <a name="connect-to-a-linux-vm"></a>Herstellen einer Verbindung mit einem virtuellen Linux-Computer

Wenn Sie eine Verbindung mit einem virtuellen Linux-Computer herstellen möchten, führen Sie die folgenden Schritte aus:

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

Wenn Sie während der Erstellung der VM eine öffentliche IP-Adresse verwendet haben, können Sie über diese IP-Adresse eine Verbindung mit der VM herstellen. Um die öffentliche IP-Adresse abzurufen, führen Sie den folgenden Befehl aus: 

### <a name="az"></a>[Az](#tab/az)

```powershell
$publicIp = Get-AzPublicIpAddress -Name $PublicIp -ResourceGroupName $ResourceGroupName
```
Die öffentliche IP-Adresse ist in diesem Fall identisch mit der privaten IP-Adresse, die Sie beim Erstellen der virtuellen Netzwerkschnittstelle übermittelt haben.
  
### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
$publicIp = Get-AzureRmPublicIpAddress -Name <Public IP> -ResourceGroupName <Resource group name>
```
Die öffentliche IP-Adresse ist in diesem Fall identisch mit der privaten IP-Adresse, die Sie beim Erstellen der virtuellen Netzwerkschnittstelle übermittelt haben.

---

## <a name="connect-to-a-windows-vm"></a>Herstellen einer Verbindung mit einem virtuellen Windows-Computer

Wenn Sie eine Verbindung mit einem virtuellen Windows-Computer herstellen möchten, führen Sie die folgenden Schritte aus:

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]


## <a name="manage-the-vm"></a>Verwalten der VM

In den folgenden Abschnitten werden einige der häufigen Vorgänge beschrieben, die Sie auf Ihrem Azure Stack Edge Pro-Gerät erstellen können.

### <a name="list-vms-that-are-running-on-the-device"></a>Auflisten der auf dem Gerät ausgeführten VMs

Wenn Sie eine Liste aller auf Ihrem Azure Stack Edge-Gerät ausgeführten VMs zurückgeben möchten, führen Sie diesen Befehl aus:

### <a name="az"></a>[Az](#tab/az)

```powershell
Get-AzVM -ResourceGroupName <String> -Name <String>
```

Weitere Informationen zu diesem Cmdlet finden Sie unter [Get-AzVM](/powershell/module/az.compute/get-azvm?view=azps-6.1.0&preserve-view=true).

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
Get-AzureRmVM -ResourceGroupName <String> -Name <String>
```

---


### <a name="turn-on-the-vm"></a>Einschalten der VM

Wenn Sie einen auf Ihrem Gerät ausgeführten virtuellen Computer einschalten möchten, führen Sie das folgende Cmdlet aus:

### <a name="az"></a>[Az](#tab/az)

```powershell
Start-AzVM [-Name] <String> [-ResourceGroupName] <String>
```
Weitere Informationen zu diesem Cmdlet finden Sie unter [Start-AzVM](/powershell/module/az.compute/start-azvm?view=azps-5.9.0&preserve-view=true).

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
Start-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>
```

Weitere Informationen zu diesem Cmdlet finden Sie unter [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm?view=azurermps-6.13.0&preserve-view=true).

---

### <a name="suspend-or-shut-down-the-vm"></a>Anhalten oder Herunterfahren des virtuellen Computers

Wenn Sie einen auf Ihrem Gerät ausgeführten virtuellen Computer anhalten oder herunterfahren möchten, führen Sie das folgende Cmdlet aus:

### <a name="az"></a>[Az](#tab/az)

```powershell
Stop-AzVM [-Name] <String> [-StayProvisioned] [-ResourceGroupName] <String>
```

Weitere Informationen zu diesem Cmdlet finden Sie unter [Stop-AzVM](/powershell/module/az.compute/stop-azvm?view=azps-5.9.0&preserve-view=true).

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
Stop-AzureRmVM [-Name] <String> [-StayProvisioned] [-ResourceGroupName] <String>
```

Weitere Informationen zu diesem Cmdlet finden Sie unter [Stop-AzureRmVM cmdlet](/powershell/module/azurerm.compute/stop-azurermvm?view=azurermps-6.13.0&preserve-view=true).

---

### <a name="add-a-data-disk"></a>Hinzufügen eines Datenträgers

Wenn die Anforderungen an die Workload auf Ihrem virtuellen Computer größer werden, müssen Sie möglicherweise einen Datenträger hinzufügen. Führen Sie hierzu den folgenden Befehl aus:

### <a name="az"></a>[Az](#tab/az)

```powershell
Add-AzRmVMDataDisk -VM $VirtualMachine -Name "disk1" -VhdUri "https://contoso.blob.core.windows.net/vhds/diskstandard03.vhd" -LUN 0 -Caching ReadOnly -DiskSizeinGB 1 -CreateOption Empty 
 
Update-AzVM -ResourceGroupName "<Resource Group Name string>" -VM $VirtualMachine
```

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" -VhdUri "https://contoso.blob.core.windows.net/vhds/diskstandard03.vhd" -LUN 0 -Caching ReadOnly -DiskSizeinGB 1 -CreateOption Empty 
 
Update-AzureRmVM -ResourceGroupName "<Resource Group Name string>" -VM $VirtualMachine
```
---



### <a name="delete-the-vm"></a>Löschen der virtuellen Computer

Wenn Sie einen virtuellen Computer von Ihrem Gerät entfernen möchten, führen Sie das folgende Cmdlet aus:

### <a name="az"></a>[Az](#tab/az)

```powershell
Remove-AzVM [-Name] <String> [-ResourceGroupName] <String>
```
Weitere Informationen zu diesem Cmdlet finden Sie unter [Remove-AzVm](/powershell/module/az.compute/remove-azvm?view=azps-5.9.0&preserve-view=true).

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
Remove-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>
```
Weitere Informationen zu diesem Cmdlet finden Sie unter [Remove-AzureRmVm cmdlet](/powershell/module/azurerm.compute/remove-azurermvm?view=azurermps-6.13.0&preserve-view=true).

---

## <a name="next-steps"></a>Nächste Schritte

[Azure Resource Manager-Cmdlets](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)
