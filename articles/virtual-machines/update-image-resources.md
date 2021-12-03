---
title: Auflisten, Aktualisieren und Löschen von Imageressourcen
description: Auflisten, Aktualisieren und Löschen von Bildressourcen in Ihrer Azure Compute Gallery.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 08/05/2021
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2c01020c266a357329c38023c9e9c34c59424088
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131448765"
---
# <a name="list-update-and-delete-image-resources"></a>Auflisten, Aktualisieren und Löschen von Imageressourcen 

Sie können Ihre Azure Compute Gallery-Ressourcen (früher als Shared Image Gallery bezeichnet) mithilfe der Azure CLI oder Azure PowerShell verwalten.

## <a name="list-information"></a>Auflisten von Informationen

### <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli)

Den Speicherort, Status und weitere Informationen zu den verfügbaren Abbildkatalogen können Sit mit [az sig list](/cli/azure/sig#az_sig_list) abrufen.

```azurecli-interactive 
az sig list -o table
```

Die Abbilddefinitionen in einem Katalog, einschließlich der Informationen zum Betriebssystemtyp und -status können Sie mit [az sig image-definition list](/cli/azure/sig/image-definition#az_sig_image_definition_list) auflisten.

```azurecli-interactive 
az sig image-definition list --resource-group myGalleryRG --gallery-name myGallery -o table
```

Listen Sie die Imageversionen in einem Katalog mithilfe von [az sig image-version list](/cli/azure/sig/image-version#az_sig_image_version_list) auf.

```azurecli-interactive
az sig image-version list --resource-group myGalleryRG --gallery-name myGallery --gallery-image-definition myImageDefinition -o table
```

Die ID einer Abbildversion können Sie mit [az sig image-version show](/cli/azure/sig/image-version#az_sig_image_version_show) abrufen.

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id"
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)

Listet alle Kataloge nach Namen auf

```azurepowershell-interactive
$galleries = Get-AzResource -ResourceType Microsoft.Compute/galleries
$galleries.Name
```

Listet alle Imagedefinitionen nach Namen auf

```azurepowershell-interactive
$imageDefinitions = Get-AzResource -ResourceType Microsoft.Compute/galleries/images
$imageDefinitions.Name
```

Listet alle Imageversionen nach Namen auf

```azurepowershell-interactive
$imageVersions = Get-AzResource -ResourceType Microsoft.Compute/galleries/images/versions
$imageVersions.Name
```

Löscht eine Imageversion Dieses Beispiel löscht die Imageversion *1.0.0*.

```azurepowershell-interactive
Remove-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -Name 1.0.0 `
   -ResourceGroupName myGalleryRG
```


---


## <a name="update-resources"></a>Aktualisieren von Ressourcen

### <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli)
Es gibt Einschränkungen, welche Elemente aktualisiert werden können. Die folgenden Elemente können aktualisiert werden: 

Azure Compute Gallery:
- BESCHREIBUNG

Imagedefinition:
- Empfohlene vCPUs
- Empfohlener Arbeitsspeicher
- BESCHREIBUNG
- Datum für Ende des Lebenszyklus

Imageversion:
- Anzahl regionaler Replikate
- Zielregionen
- Ausschluss aus neuester Version
- Datum für Ende des Lebenszyklus

Wenn Sie Replikatregionen hinzufügen möchten, dürfen Sie das verwaltete Quellimage nicht löschen. Das verwaltete Quellimage ist erforderlich, um die Imageversion in weiteren Regionen zu replizieren. 

Aktualisieren Sie die Beschreibung eines Katalogs mit ([az sig update](/cli/azure/sig#az_sig_update). 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


Aktualisieren Sie die Beschreibung einer Imagedefinition mit [az sig image-definition update](/cli/azure/sig/image-definition#az_sig_image_definition_update).

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

Aktualisieren Sie eine Imageversion zum Hinzufügen einer Region als Replikationsziel mit [az sig image-version update](/cli/azure/sig/image-definition#az_sig_image_definition_update). Diese Änderung wird einige Zeit in Anspruch nehmen, da das Image in eine neue Region repliziert wird.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

In diesem Beispiel wird gezeigt, wie Sie mit [az sig image-version update](/cli/azure/sig/image-definition#az_sig_image_definition_update) dafür sorgen können, dass diese Imageversion nicht als das *neueste* Image verwendet wird.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=true
```

Dieses Beispiel zeigt, wie [az sig image-version update](/cli/azure/sig/image-definition#az_sig_image_definition_update) verwendet wird, um diese Imageversion in die Berücksichtigung als *neuestes* Image einzubeziehen.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=false
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)


Es gibt Einschränkungen, welche Elemente aktualisiert werden können. Die folgenden Elemente können aktualisiert werden: 

Azure Compute Gallery:
- BESCHREIBUNG

Imagedefinition:
- Empfohlene vCPUs
- Empfohlener Arbeitsspeicher
- BESCHREIBUNG
- Datum für Ende des Lebenszyklus

Imageversion:
- Anzahl regionaler Replikate
- Zielregionen
- Ausschluss aus neuester Version
- Datum für Ende des Lebenszyklus

Wenn Sie Replikatregionen hinzufügen möchten, dürfen Sie das verwaltete Quellimage nicht löschen. Das verwaltete Quellimage ist erforderlich, um die Imageversion in weiteren Regionen zu replizieren. 

Um die Beschreibung eines Katalogs zu aktualisieren, verwenden Sie [Update-AzGallery](/powershell/module/az.compute/update-azgallery).

```azurepowershell-interactive
Update-AzGallery `
   -Name $gallery.Name ` 
   -ResourceGroupName $resourceGroup.Name
```

In diesem Beispiel wird gezeigt, wie Sie mit [Update-AzGalleryImageDefinition](/powershell/module/az.compute/update-azgalleryimagedefinition) das Ablaufdatum für Ihre Imagedefinition aktualisieren können.

```azurepowershell-interactive
Update-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -Name $galleryImage.Name `
   -ResourceGroupName $resourceGroup.Name `
   -EndOfLifeDate 01/01/2030
```

In diesem Beispiel wird gezeigt, wie Sie mit [Update-AzGalleryImageVersion](/powershell/module/az.compute/update-azgalleryimageversion) dafür sorgen können, dass diese Image-Version nicht als das *neueste* Image verwendet wird.

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest
```

Dieses Beispiel zeigt, wie [Update-AzGalleryImageVersion](/powershell/module/az.compute/update-azgalleryimageversion) verwendet wird, um diese Imageversion in die Berücksichtigung als *neuestes* Image einzubeziehen.

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest:$false
```

---




## <a name="delete-resources"></a>Löschen von Ressourcen

Ressourcen müssen in umgekehrter Reihenfolge gelöscht werden, beginnend mit der Imageversion. Nachdem alle Imageversionen gelöscht sind, können Sie die Imagedefinition löschen. Nachdem alle Imagedefinitionen gelöscht sind, können Sie den Katalog löschen. 

### <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli)

Löschen Sie eine Imageversion mit [az sig image-version delete](/cli/azure/sig/image-version#az_sig_image_version_delete).

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

Löschen Sie eine Imagedefinition mit [az sig image-definition delete](/cli/azure/sig/image-definition#az_sig_image_definition_delete).

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


Löschen Sie einen Katalog mit [az sig delete](/cli/azure/sig#az_sig_delete).

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)



```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$gallery = "myGallery"
$imageDefinition = "myImageDefinition"
$imageVersion = "myImageVersion"

Remove-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition `
   -GalleryName $gallery `
   -Name $imageVersion `
   -ResourceGroupName $resourceGroup

Remove-AzGalleryImageDefinition `
   -ResourceGroupName $resourceGroup `
   -GalleryName $gallery `
   -GalleryImageDefinitionName $imageDefinition

Remove-AzGallery `
   -Name $gallery `
   -ResourceGroupName $resourceGroup

Remove-AzResourceGroup -Name $resourceGroup
```

---

## <a name="next-steps"></a>Nächste Schritte

[Azure Image Builder (Vorschauversion)](./image-builder-overview.md) hilft beim Automatisieren der Erstellung von Imageversionen. Sie können den Dienst sogar zum Aktualisieren und [Erstellen einer neuen Imageversion aus einer vorhandenen](./linux/image-builder-gallery-update-image-version.md) verwenden.