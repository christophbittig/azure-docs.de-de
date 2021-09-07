---
title: Verwenden eines Azure Marketplace-Images zum Erstellen von VM-Images für Azure Stack Edge Pro GPU-Geräte
description: Beschrieben wird, wie Sie mit einem Azure Marketplace-Image ein VM-Image erstellen, das auf Ihrem Azure Stack Edge Pro GPU-Gerät verwendet werden soll.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/14/2021
ms.author: alkohli
ms.openlocfilehash: 13d020ed0ea6310f49cbe37555fc2a9048b9f042
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/22/2021
ms.locfileid: "112465383"
---
# <a name="use-azure-marketplace-image-to-create-vm-image-for-your-azure-stack-edge-pro-gpu"></a>Verwenden eines Azure Marketplace-Images zum Erstellen von VM-Images für Azure Stack Edge Pro GPU-Geräte

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Wenn Sie virtuelle Computer auf Ihrem Azure Stack Edge Pro GPU-Gerät bereitstellen möchten, müssen Sie ein VM-Image erstellen, das Sie zum Erstellen virtueller Computer verwenden können. In diesem Artikel werden die Schritte beschrieben, die erforderlich sind, um ausgehend von einem Azure Marketplace-Image ein VM-Image zu erstellen. Anschließend können Sie mit diesem VM-Image virtuelle Computer auf Ihrem Azure Stack Edge Pro GPU-Gerät bereitstellen.

## <a name="vm-image-workflow"></a>Workflow für VM-Images

In den folgenden Schritten wird der Workflow zum Erstellen von VM-Images mithilfe eines Azure Marketplace-Workflows beschrieben:

1.  Stellen Sie eine Verbindung zur Azure Cloud Shell oder einem Client her, auf dem die Azure-Befehlszeilenschnittstelle (CLI) installiert ist.
2.  Suchen Sie im Azure Marketplace nach dem von Ihnen bevorzugten Image.
3.  Erstellen Sie mit dem Marketplace-Image einen neuen verwalteten Datenträger.
4.  Exportieren Sie eine VHD vom verwalteten Datenträger in das Azure Storage-Konto.
5.  Bereinigen Sie den verwalteten Datenträger.


Weitere Informationen finden Sie unter [Bereitstellen von VMs auf Ihrem Azure Stack Edge Pro-Gerät mithilfe von Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md).

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie Azure Marketplace-Images für Azure Stack Edge verwenden können, müssen Sie sicherstellen, dass Sie auf eine der unten angegebenen Arten mit Azure verbunden sind.

[!INCLUDE [azure-cli-prepare-your-environment](../../includes/azure-cli-prepare-your-environment-no-header.md)]


## <a name="search-for-azure-marketplace-images"></a>Suchen nach Azure Marketplace-Images

Sie identifizieren nun ein bestimmtes Azure Marketplace-Image, das Sie verwenden möchten. Azure Marketplace hostet tausende VM-Images. 

Führen Sie den folgenden Befehl aus, um nach einigen der am häufigsten verwendeten Marketplace-Images zu suchen, die Ihren Suchkriterien entsprechen.  

```azurecli
az vm image list --all [--publisher <Publisher>] [--offer <Offer>] [--sku <SKU>]
```
Die letzten drei Parameter sind optional. Doch wenn Sie sie nicht angeben, wird eine lange Liste zurückgegeben.

Nachfolgend finden Sie einige Beispiele für Abfragen:

```azurecli
#Returns all images of type “Windows Server”
az vm image list --all --publisher "MicrosoftWindowsserver" --offer "WindowsServer" 

#Returns all Windows Server 2019 Datacenter images from West US published by Microsoft
az vm image list --all --location "westus" --publisher "MicrosoftWindowsserver" --offer "WindowsServer" --sku "2019-Datacenter"

#Returns all VM images from a publisher  
az vm image list --all --publisher "Canonical" 
```

Dies ist eine Beispielausgabe, wenn VM-Images eines bestimmten Herausgebers, Angebots und einer bestimmten SKU abgefragt wurden.

```output
PS /home/user> az vm image list --all --publisher "Canonical" --offer "UbuntuServer" --sku "12.04.4-LTS"
[
  {
    "offer": "UbuntuServer",
    "publisher": "Canonical",
    "sku": "12.04.4-LTS",
    "urn": "Canonical:UbuntuServer:12.04.4-LTS:12.04.201402270",
    "version": "12.04.201402270"
  },
  {
    "offer": "UbuntuServer",
    "publisher": "Canonical",
    "sku": "12.04.4-LTS",
    "urn": "Canonical:UbuntuServer:12.04.4-LTS:12.04.201404080",
    "version": "12.04.201404080"
  },
  {
    "offer": "UbuntuServer",
    "publisher": "Canonical",
    "sku": "12.04.4-LTS",
    "urn": "Canonical:UbuntuServer:12.04.4-LTS:12.04.201404280",
    "version": "12.04.201404280"
  },
  {
    "offer": "UbuntuServer",
    "publisher": "Canonical",
    "sku": "12.04.4-LTS",
    "urn": "Canonical:UbuntuServer:12.04.4-LTS:12.04.201405140",
    "version": "12.04.201405140"
  },
  {
    "offer": "UbuntuServer",
    "publisher": "Canonical",
    "sku": "12.04.4-LTS",
    "urn": "Canonical:UbuntuServer:12.04.4-LTS:12.04.201406060",
    "version": "12.04.201406060"
  },
  {
    "offer": "UbuntuServer",
    "publisher": "Canonical",
    "sku": "12.04.4-LTS",
    "urn": "Canonical:UbuntuServer:12.04.4-LTS:12.04.201406190",
    "version": "12.04.201406190"
  },
  {
    "offer": "UbuntuServer",
    "publisher": "Canonical",
    "sku": "12.04.4-LTS",
    "urn": "Canonical:UbuntuServer:12.04.4-LTS:12.04.201407020",
    "version": "12.04.201407020"
  },
  {
    "offer": "UbuntuServer",
    "publisher": "Canonical",
    "sku": "12.04.4-LTS",
    "urn": "Canonical:UbuntuServer:12.04.4-LTS:12.04.201407170",
    "version": "12.04.201407170"
  }
]
PS /home/user>
```

>[!IMPORTANT]
> Verwenden Sie nur die Images der 1. Generation (Gen 1). Alle Images, die als 2. Generation definiert sind (die SKU weist in der Regel das Suffix „-g2“ auf), funktionieren in Azure Stack Edge nicht.

In diesem Beispiel wählen Sie Windows Server 2019 Datacenter Core, Version 2019.0.20190410, aus. Sie identifizieren dieses Image anhand der universellen Ressourcennummer („URN“). 
 
:::image type="content" source="media/azure-stack-edge-create-virtual-machine-marketplace-image/marketplace-image-1.png" alt-text="Liste der Marketplace-Images":::

### <a name="commonly-used-marketplace-images"></a>Häufig verwendete Marketplace-Images

Im Folgenden finden Sie eine Liste mit den URNs für einige der am häufigsten verwendeten Images. Wenn nur die neueste Version eines bestimmten Betriebssystems verwendet werden soll, können Sie die Versionsnummer in der URN durch „latest“ ersetzen. Beispiel: „MicrosoftWindowsServer:WindowsServer:2019-Datacenter:Latest“. 


| OS              | SKU                                     | Version               | URN                                                                                       |
|-----------------|-----------------------------------------|-----------------------|-------------------------------------------------------------------------------------------|
| Windows Server  | 2019-Datacenter                         | 17763.1879.2104091832 | MicrosoftWindowsServer:WindowsServer:2019-Datacenter:17763.1879.2104091832                |
| Windows Server  | 2019-Datacenter (30 GB, smalldisk)      | 17763.1879.2104091832 | MicrosoftWindowsServer:WindowsServer:2019-Datacenter-smalldisk:17763.1879.2104091832      |
| Windows Server  | 2019-Datacenter-Core                    | 17763.1879.2104091832 | MicrosoftWindowsServer:WindowsServer:2019-Datacenter-Core:17763.1879.2104091832           |
| Windows Server  | 2019-Datacenter-Core (30 GB, smalldisk) | 17763.1879.2104091832 | MicrosoftWindowsServer:WindowsServer:2019-Datacenter-Core-smalldisk:17763.1879.2104091832 |
| Windows Desktop | Windows 10 20H2 Pro                     | 19042.928.2104091209  | MicrosoftWindowsDesktop:Windows-10:20h2-pro:19042.928.2104091209                          |
| Ubuntu Server   | Canonical Ubuntu Server 18.04 LTS       | 18.04.202002180       | Canonical:UbuntuServer:18.04-LTS:18.04.202002180                                          |
| Ubuntu Server   | Canonical Ubuntu Server 16.04 LTS       | 16.04.202104160       | Canonical:UbuntuServer:16.04-LTS:16.04.202104160                                          |
| CentOS          | CentOS 8.1                              | 8.1.2020062400        | OpenLogic:CentOS:8_1:8.1.2020062400                                                       |
| CentOS          | CentOS 7.7                              | 7.7.2020062400        | OpenLogic:CentOS:7.7:7.7.2020062400                                                       |



## <a name="create-a-new-managed-disk-from-the-marketplace-image"></a>Erstellen eines neuen verwalteten Datenträger mit dem Marketplace-Image

Erstellen Sie mit dem ausgewählten Marketplace-Image einen verwalteten Azure-Datenträger. 

1. Legen Sie einige Parameter fest.

    ```azurecli
    $urn = <URN of the Marketplace image> #Example: “MicrosoftWindowsServer:WindowsServer:2019-Datacenter:Latest”
    $diskName = <disk name> #Name for new disk to be created
    $diskRG = <resource group> #Resource group that contains the new disk
    ```


1. Erstellen Sie den Datenträger, und generieren Sie eine URL mit SAS-Zugriff.

    ```azurecli
    az disk create -g $diskRG -n $diskName --image-reference $urn
    $sas = az disk grant-access --duration-in-seconds 36000 --access-level Read --name $diskName --resource-group $diskRG
    $diskAccessSAS = ($sas | ConvertFrom-Json)[0].accessSas 
    ```

Beispielausgabe:

```output
PS /home/user> $urn = “MicrosoftWindowsServer:WindowsServer:2019-Datacenter:Latest”
PS /home/user> $diskName = "newmanageddisk1"
PS /home/user> $diskRG = "newrgmd1"
PS /home/user> az disk create -g $diskRG -n $diskName --image-reference $urn
{
  "burstingEnabled": null,
  "creationData": {
    "createOption": "FromImage",
    "galleryImageReference": null,
    "imageReference": {
      "id": "/Subscriptions/db4e2fdb-6d80-4e6e-b7cd-736098270664/Providers/Microsoft.Compute/Locations/eastus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2019-Datacenter/Versions/17763.1935.2105080716",
      "lun": null
    },
    "logicalSectorSize": null,
    "sourceResourceId": null,
    "sourceUniqueId": null,
    "sourceUri": null,
    "storageAccountId": null,
    "uploadSizeBytes": null
  },
  "diskAccessId": null,
  "diskIopsReadOnly": null,
  "diskIopsReadWrite": 500,
  "diskMBpsReadOnly": null,
  "diskMBpsReadWrite": 100,
  "diskSizeBytes": 136367308800,
  "diskSizeGb": 127,
  "diskState": "Unattached",
  "encryption": {
    "diskEncryptionSetId": null,
    "type": "EncryptionAtRestWithPlatformKey"
  },
  "encryptionSettingsCollection": null,
  "extendedLocation": null,
  "hyperVGeneration": "V1",
  "id": "/subscriptions/db4e2fdb-6d80-4e6e-b7cd-736098270664/resourceGroups/newrgmd1/providers/Microsoft.Compute/disks/NewManagedDisk1",
  "location": "eastus",
  "managedBy": null,
  "managedByExtended": null,
  "maxShares": null,
  "name": "NewManagedDisk1",
  "networkAccessPolicy": "AllowAll",
  "osType": "Windows",
  "propertyUpdatesInProgress": null,
  "provisioningState": "Succeeded",
  "purchasePlan": null,
  "resourceGroup": "newrgmd1",
  "securityProfile": null,
  "shareInfo": null,
  "sku": {
    "name": "Premium_LRS",
    "tier": "Premium"
  },
  "supportsHibernation": null,
  "tags": {},
  "tier": "P10",
  "timeCreated": "2021-06-08T00:39:34.205982+00:00",
  "type": "Microsoft.Compute/disks",
  "uniqueId": "1a649ad4-3b95-471e-89ef-1d2ed1f51525",
  "zones": null
}

PS /home/user> $sas = az disk grant-access --duration-in-seconds 36000 --access-level Read --name $diskName --resource-group $diskRG
PS /home/user>  $diskAccessSAS = ($sas | ConvertFrom-Json)[0].accessSas
PS /home/user>
```

## <a name="export-a-vhd-from-the-managed-disk-to-azure-storage"></a>Exportieren einer VHD vom verwalteten Datenträger in Azure Storage 

In diesem Schritt exportieren Sie eine VHD vom verwalteten Datenträger in Ihr bevorzugtes Azure Blob Storage-Konto. Diese VHD kann dann zum Erstellen von VM-Images auf Azure Stack Edge-Geräten verwendet werden.

1. Legen Sie das Zielspeicherkonto fest, in das die VHD kopiert wird.
    
    ```azurecli
    $storageAccountName = <destination storage account name>
    $containerName = <destination container name>
    $destBlobName = <blobname.vhd> #Blob that will be created, including .vhd extension
    $storageAccountKey = <storage account key>
    ```

1. Kopieren Sie die VHD in das Zielspeicherkonto.

    ```azurecli
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
    Start-AzureStorageBlobCopy -AbsoluteUri $diskAccessSAS -DestContainer $containerName -DestContext $destContext -DestBlob $destBlobName
    ```

    Dieser VHD-Kopiervorgang dauert einige Minuten. Stellen Sie sicher, dass der Kopiervorgang abgeschlossen ist, bevor Sie den folgenden Befehl ausführen. Im Statusfeld wird nach Abschluss des Vorgangs „Erfolgreich“ angezeigt.
    
    ```azurecli
    Get-AzureStorageBlobCopyState –Container $containerName –Context $destContext -Blob $destBlobName 
    ```

Beispielausgabe:

```output
PS /home/user> $storageAccountName = "edgeazurevmeus"
PS /home/user> $containerName = "azurevmmp"
PS /home/user> $destBlobName = "newblobmp.vhd"
PS /home/user> $storageAccountKey = "n9sCytWLdTBz0F4Sco9SkPGWp6BJBtf7BJBk79msf1PfxJGQdqSfu6TboZWZ10xyZdc4y+Att08cC9B79jB0YA=="

PS /home/user> $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
PS /home/user> Start-AzureStorageBlobCopy -AbsoluteUri $diskAccessSAS -DestContainer $containerName -DestContext $destContext -DestBlob $destBlobName

   AccountName: edgeazurevmeus, ContainerName: azurevmmp

Name                 BlobType  Length          ContentType                    LastModified         AccessTier SnapshotTime                 IsDeleted  VersionId
----                 --------  ------          -----------                    ------------         ---------- ------------                 ---------  ---------
newblobmp.vhd        PageBlob  -1                                             2021-06-08 00:50:10Z                                         False

PS /home/user> Get-AzureStorageBlobCopyState –Container $containerName –Context $destContext -Blob $destBlobName

CopyId                  : 24a1e3f5-886a-490d-9dd7-562bb4acff58
CompletionTime          :
Status                  : Pending
Source                  : https://md-lfn221fppr2c.blob.core.windows.net/d4tb2hp5ff2q/abcd?sv=2018-03-28&sr=b&si=4f588db1-9aac-44d9-9607-35497cc08a7f
BytesCopied             : 696254464
TotalBytes              : 136367309312
StatusDescription       :
DestinationSnapshotTime :

PS /home/user> Get-AzureStorageBlobCopyState –Container $containerName –Context $destContext -Blob $destBlobName

CopyId                  : 24a1e3f5-886a-490d-9dd7-562bb4acff58
CompletionTime          : 6/8/2021 12:57:26 AM +00:00
Status                  : Success
Source                  : https://md-lfn221fppr2c.blob.core.windows.net/d4tb2hp5ff2q/abcd?sv=2018-03-28&sr=b&si=4f588db1-9aac-44d9-9607-35497cc08a7f
BytesCopied             : 136367309312
TotalBytes              : 136367309312
StatusDescription       :
DestinationSnapshotTime :
```

## <a name="clean-up-the-managed-disk"></a>Bereinigen des verwalteten Datenträgers

Führen Sie die folgenden Schritte aus, um den von Ihnen erstellten verwalteten Datenträger zu löschen:

```azurecli
az disk revoke-access --name $diskName --resource-group $diskRG 
az disk delete --name $diskName --resource-group $diskRG --yes 
```
Der Löschvorgang kann einige Minuten dauern.

## <a name="next-steps"></a>Nächste Schritte

[Bereitstellen von virtuellen Computern auf Ihrem Azure Stack Edge Pro GPU-Gerät](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).