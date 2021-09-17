---
title: Kopieren eines Images aus einer anderen Galerie mithilfe von PowerShell
description: Kopieren eines Images aus einer anderen Galerie mithilfe von Azure PowerShell.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1c5d06e510cfa78a0651f73c0f300b11c4d5b01e
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697051"
---
# <a name="copy-an-image-from-another-gallery-using-powershell"></a>Kopieren eines Images aus einer anderen Galerie mithilfe von PowerShell

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Wenn Sie in Ihrer Organisation über mehrere Kataloge verfügen, können Sie Images aus in anderen Katalogen gespeicherten Images erstellen. Beispielsweise können Sie über einen Entwicklungs- und Testkatalog verfügen, um neue Images zu erstellen und zu testen. Wenn diese für die Verwendung in der Produktion bereit sind, können Sie sie mithilfe dieses Beispiels in einen Produktionskatalog kopieren. Sie können auch ein Image aus einem Image in einem anderen Katalog erstellen, indem Sie die [Azure CLI](image-version-another-gallery-cli.md) verwenden.


## <a name="before-you-begin"></a>Voraussetzungen

Um diesen Artikel nachvollziehen zu können, benötigen Sie einen vorhandenen Quellkatalog, eine Imagedefinition und eine Imageversion. Sie sollten auch über einen Zielkatalog verfügen. 

Die Quellimageversion muss in die Region repliziert werden, in der sich der Zielkatalog befindet. 

Wir erstellen eine neue Imagedefinition und Imageversion in Ihrem Zielkatalog.


Ersetzen Sie beim Durcharbeiten dieses Artikels die Ressourcennamen, wo dies erforderlich ist.


## <a name="get-the-source-image"></a>Abrufen des Quellimages 

Sie benötigen Informationen aus der Quellimagedefinition, damit Sie eine Kopie davon in Ihrem Zielkatalog erstellen können.

Listen Sie mit dem Cmdlet [Get-AzResource](/powershell/module/az.resources/get-azresource) Informationen zu den vorhandenen Katalogen, Imagedefinitionen und Imageversionen auf.

Die Ergebnisse liegen im Format `gallery\image definition\image version` vor.

```azurepowershell-interactive
Get-AzResource `
   -ResourceType Microsoft.Compute/galleries/images/versions | `
   Format-Table -Property Name,ResourceGroupName
```

Sobald Sie über alle erforderlichen Informationen verfügen, können Sie mit [Get-AzGalleryImageVersion](/powershell/module/az.compute/get-azgalleryimageversion) die ID der Quellimageversion ermitteln. In diesem Beispiel rufen wir die `1.0.0`-Imageversion der `myImageDefinition`-Definition im `myGallery`-Quellkatalog in der `myResourceGroup`-Ressourcengruppe ab.

```azurepowershell-interactive
$sourceImgVer = Get-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name 1.0.0
```


## <a name="create-the-image-definition"></a>Erstellen der Imagedefinition 

Sie müssen eine neue Imagedefinition erstellen, die dem Betriebssystem, dem Betriebssystemstatus und der Hyper-V-Generation der Imagedefinition entspricht, die Ihre Quellimageversion enthält. Sie können alle Informationen anzeigen, die Sie benötigen, um die Imagedefinition mithilfe von [Get-AzGalleryImageDefinition](/powershell/module/az.compute/get-azgalleryimagedefinition) neu zu erstellen.

```azurepowershell-interactive
Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name myImageDefinition
```


Die Ausgabe sieht in etwa wie folgt aus:

```output
{
  "description": null,
  "disallowed": null,
  "endOfLifeDate": null,
  "eula": null,
  "hyperVgeneration": "V1",
  "id": "/subscriptions/1111abcd-1a23-4b45-67g7-1234567de123/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition",
  "identifier": {
    "offer": "myOffer",
    "publisher": "myPublisher",
    "sku": "mySKU"
  },
  "location": "eastus",
  "name": "myImageDefinition",
  "osState": "Specialized",
  "osType": "Windows",
  "privacyStatementUri": null,
  "provisioningState": "Succeeded",
  "purchasePlan": null,
  "recommended": null,
  "releaseNoteUri": null,
  "resourceGroup": "myGalleryRG",
  "tags": null,
  "type": "Microsoft.Compute/galleries/images"
}
```

Erstellen Sie mit dem Cmdlet [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) und den Informationen aus der Ausgabe oben eine neue Imagedefinition in Ihrem Zielkatalog.


In diesem Beispiel heißt die Imagedefinition *myDestinationImgDef* im Katalog mit dem Namen *myDestinationGallery*.


```azurepowershell-interactive
$destinationImgDef  = New-AzGalleryImageDefinition `
   -GalleryName myDestinationGallery `
   -ResourceGroupName myDestinationRG `
   -Location WestUS `
   -Name 'myDestinationImgDef' `
   -OsState specialized `
   -OsType Windows `
   -HyperVGeneration v1 `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```

> [!NOTE]
> Bei Imagedefinitionen mit Images, die von Drittanbieterimages stammen, müssen die Planinformationen exakt mit den Planinformationen des Drittanbieterimages übereinstimmen. Schließen Sie die Planinformationen in die Imagedefinition ein, indem Sie `-PurchasePlanName`, `-PurchasePlanProduct` und `-PurchasePlanPublisher` hinzufügen, wenn Sie die Imagedefinition erstellen.
>


## <a name="create-the-image-version"></a>Erstellen der Imageversion

Erstellen Sie mit [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion) eine Imageversion. Sie müssen die ID des Quellimages im `-SourceImageId`-Parameter übergeben, um die Imageversion in Ihrem Zielkatalog zu erstellen. 

Zulässige Zeichen für die Imageversion sind Zahlen und Punkte. Zahlen müssen im Bereich einer ganzen 32-Bit-Zahl liegen. Format: *Hauptversion*.*Nebenversion*.*Patch*.

In diesem Beispiel trägt der Zielkatalog den Namen *myDestinationGallery* in der *myDestinationRG*-Ressourcengruppe am Standort *USA, Westen*. Die Version unseres Images ist *1.0.0*, und wir werden ein Replikat in der Region *USA, Süden-Mitte* und zwei Replikate in der Region *USA, Westen* erstellen. 


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West US';ReplicaCount=2}
$targetRegions = @($region1,$region2)

$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $destinationImgDef.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName myDestinationGallery `
   -ResourceGroupName myDestinationRG `
   -Location WestUS `
   -TargetRegion $targetRegions  `
   -SourceImageId $sourceImgVer.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-01' `
   -asJob 
```

Es dauert eine Weile, das Image für alle Zielregionen zu replizieren. Daher haben wir zum Verfolgen des Fortschritts einen Auftrag erstellt. Um den Fortschritt des Auftrags anzuzeigen, geben Sie `$job.State` ein.

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> Sie müssen warten, bis die Imageversion vollständig erstellt und repliziert wurde, bevor Sie dieses verwaltete Image verwenden können, um eine weitere Imageversion zu erstellen.
>
> Sie können Ihr Image auch in einem Premium-Speicher durch Hinzufügen von `-StorageAccountType Premium_LRS` oder in einem [zonenredundanten Speicher](../storage/common/storage-redundancy.md) speichern, indem Sie `-StorageAccountType Standard_ZRS` hinzufügen, wenn Sie die Imageversion erstellen.
>


## <a name="next-steps"></a>Nächste Schritte

Erstellen Sie eine VM aus einer [generalisierten](vm-generalized-image-version-powershell.md) oder einer [spezialisierten](vm-specialized-image-version-powershell.md) Imageversion.

[Azure Image Builder (Vorschauversion)](./image-builder-overview.md) hilft beim Automatisieren der Erstellung von Imageversionen. Sie können den Dienst sogar zum Aktualisieren und [Erstellen einer neuen Imageversion aus einer vorhandenen](./linux/image-builder-gallery-update-image-version.md) verwenden. 

Weitere Informationen zur Bereitstellung von Erwerbsplaninformationen finden Sie unter [Bereitstellen von Azure Marketplace-Erwerbsplaninformationen beim Erstellen von Images](marketplace-images.md).
