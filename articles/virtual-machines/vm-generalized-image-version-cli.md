---
title: Erstellen einer VM aus einem generalisierten Image mithilfe der Azure-Befehlszeilenschnittstelle
description: Erstellen Sie eine VM aus einem generalisierten Image mithilfe der Azure-Befehlszeilenschnittstelle.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9304f75c1de6a46da960187ef82951bc77df4d96
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688160"
---
# <a name="create-a-vm-from-a-generalized-image-version-using-the-azure-cli"></a>Erstellen einer VM aus einer generalisierten Imageversion mithilfe der Azure-Befehlszeilenschnittstelle

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

Erstellen Sie eine VM aus einer [generalisierten Imageversion](./shared-image-galleries.md#generalized-and-specialized-images), die in einem Katalog mit freigegebenen Images gespeichert ist. Informationen zum Erstellen einer VM aus einem spezialisierten Image finden Sie unter [Erstellen einer VM aus einem spezialisierten Image](vm-specialized-image-version-powershell.md). 


## <a name="get-the-image-id"></a>Abrufen der Image-ID

Listen Sie die Imagedefinitionen in einem Katalog mithilfe von [az sig image-definition list](/cli/azure/sig/image-definition#az_sig_image_definition_list) auf, um den Namen und die ID der Definitionen anzuzeigen.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list --resource-group $resourceGroup --gallery-name $gallery --query "[].[name, id]" --output tsv
```

## <a name="create-the-vm"></a>Erstellen des virtuellen Computers

Erstellen Sie einen virtuellen Computer mit [az vm create](/cli/azure/vm#az_vm_create). Wenn Sie die neueste Version des Images verwenden möchten, legen Sie `--image` auf die ID der Imagedefinition fest. 

Ersetzen Sie bei Bedarf die Ressourcennamen in diesem Beispiel. 

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

## <a name="next-steps"></a>Nächste Schritte

[Azure Image Builder (Vorschauversion)](./image-builder-overview.md) hilft beim Automatisieren der Erstellung von Imageversionen. Sie können den Dienst sogar zum Aktualisieren und [Erstellen einer neuen Imageversion aus einer vorhandenen](./linux/image-builder-gallery-update-image-version.md) verwenden.