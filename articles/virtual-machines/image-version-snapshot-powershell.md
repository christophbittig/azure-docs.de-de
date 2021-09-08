---
title: 'PowerShell: Erstellen eines Images aus einer Momentaufnahme oder einem verwalteten Datenträger in einer Shared Image Gallery-Instanz'
description: Erfahren Sie, wie Sie aus einer Momentaufnahme oder einem verwalteten Datenträger in einer Shared Image Gallery-Instanz mithilfe von PowerShell ein Image erstellen.
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure
ms.date: 06/30/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e437a313b982849e8d22def87774548eaa8ddb8a
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688981"
---
# <a name="create-an-image-from-a-managed-disk-or-snapshot-in-a-shared-image-gallery-using-powershell"></a>Erstellen eines Images aus einem verwalteten Datenträger oder einer Momentaufnahme in einer Shared Image Gallery-Instanz mithilfe von PowerShell

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Wenn Sie über eine vorhandene Momentaufnahme oder einen verwalteten Datenträger verfügen, die Sie zu einer Shared Image Gallery-Instanz migrieren möchten, können Sie direkt aus dem verwalteten Datenträger oder der Momentaufnahme ein Shared Image Gallery-Image erstellen. Nachdem Sie das neue Image getestet haben, können Sie den verwalteten Quelldatenträger oder die Quellmomentaufnahme löschen. Sie können auch die [Azure CLI](image-version-snapshot-cli.md) verwenden, um ein Image aus einen verwalteten Datenträger oder einer Momentaufnahme in einer Shared Image Gallery-Instanz zu erstellen.

Images in Imagekatalogen weisen zwei Komponenten auf, die in diesem Beispiel erstellt werden:
- Eine **Imagedefinition** enthält Informationen zum Image und zu den Anforderungen für dessen Verwendung. Dies umfasst Angaben dazu, ob es sich um ein Windows- oder Linux-Image handelt, ob es spezialisiert oder generalisiert ist sowie welche Mindest- und Höchstanforderungen für den Arbeitsspeicher gelten. Außerdem sind Versionshinweise enthalten. Es ist eine Definition eines Imagetyps. 
- Bei der Verwendung eines Katalogs mit freigegebenen Images werden **Imageversionen** verwendet, um VMs zu erstellen. Sie können nach Bedarf mehrere Versionen eines Images für Ihre Umgebung haben. Bei der Erstellung einer VM werden anhand der Imageversionen neue Datenträger für die VM erstellt. Imageversionen können mehrmals verwendet werden.


## <a name="before-you-begin"></a>Voraussetzungen

Sie benötigen eine Momentaufnahme oder einen verwalteten Datenträger, um die Schritte in diesem Artikel ausführen zu können. 

Wenn Sie einen Datenträger hinzufügen möchten, darf dieser nicht größer als 1 TB sein.

Ersetzen Sie beim Durcharbeiten dieses Artikels die Ressourcennamen, wo dies erforderlich ist.


## <a name="get-the-snapshot-or-managed-disk"></a>Abrufen der Momentaufnahme oder des verwalteten Datenträgers

Mit [Get-AzSnapshot](/powershell/module/az.compute/get-azsnapshot) können Sie eine Liste der Momentaufnahmen anzeigen, die in einer Ressourcengruppe verfügbar sind. 

```azurepowershell-interactive
get-azsnapshot | Format-Table -Property Name,ResourceGroupName
```

Wenn Sie den Namen und die Ressourcengruppe der Momentaufnahme kennen, können Sie `Get-AzSnapshot` noch mal verwenden, um das Momentaufnahmeobjekt abzurufen und zur späteren Verwendung in einer Variable zu speichern. Im folgenden Beispiel wird die Momentaufnahme *mySnapshot* aus der Ressourcengruppe „myResourceGroup“ abgerufen und der Variable *$source* zugewiesen. 

```azurepowershell-interactive
$source = Get-AzSnapshot `
   -SnapshotName mySnapshot `
   -ResourceGroupName myResourceGroup
```

Sie können auch einen verwalteten Datenträger anstelle einer Momentaufnahme verwenden. Um einen verwalteten Datenträger abzurufen, verwenden Sie [Get-AzDisk](/powershell/module/az.compute/get-azdisk). 

```azurepowershell-interactive
Get-AzDisk | Format-Table -Property Name,ResourceGroupName
```

Rufen Sie anschließend den verwalteten Datenträger ab, und weisen Sie ihn der Variablen `$source` zu.

```azurepowershell-interactive
$source = Get-AzDisk `
   -Name myDisk
   -ResourceGroupName myResourceGroup
```

Sie können dieselben Cmdlets verwenden, um alle Datenträger abzurufen, die Sie in das Image aufnehmen möchten. Weisen Sie sie Variablen zu, und verwenden Sie diese Variablen später beim Erstellen der Imageversion.


## <a name="get-the-gallery"></a>Abrufen des Katalogs

Sie können alle Kataloge und Imagedefinitionen nach Namen auflisten. Die Ergebnisse liegen im Format `gallery\image definition\image version` vor.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

Nachdem Sie den richtigen Katalog gefunden haben, erstellen Sie eine Variable zur späteren Verwendung. In diesem Beispiel heißt der Katalog *myGallery* in der Ressourcengruppe *myResourceGroup*.

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myResourceGroup
```


## <a name="create-an-image-definition"></a>Erstellen einer Imagedefinition 

Imagedefinitionen erstellen eine logische Gruppierung von Images. Sie werden zum Verwalten von Informationen zum Image verwendet. Namen für Imagedefinition können aus Groß- und Kleinbuchstaben, Zahlen, Punkten und (Binde)Strichen bestehen. 

Stellen Sie beim Erstellen der Imagedefinition sicher, dass diese alle richtigen Informationen beinhaltet. In diesem Beispiel wird davon ausgegangen, dass es sich um die Momentaufnahme oder den verwalteten Datenträger einer VM handelt, die verwendet wird und nicht generalisiert wurde. Wenn der verwaltete Datenträger oder die Momentaufnahme von einem generalisierten Betriebssystem (nach dem Ausführen von Sysprep unter Windows bzw. [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` oder `-deprovision+user` unter Linux) erstellt wurde, ändern Sie den Wert für `-OsState` in `generalized`. 

Weitere Informationen zu den Werten, die Sie für eine Imagedefinition angeben können, finden Sie unter [Imagedefinitionen](./shared-image-galleries.md#image-definitions).

Erstellen Sie die Imagedefinition mit [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion). In diesem Beispiel heißt die Imagedefinition *myImageDefinition* und bezieht sich auf ein spezialisiertes Windows-Betriebssystem. Um eine Definition für Images zu erstellen, die ein Linux-Betriebssystem verwenden, verwenden Sie `-OsType Linux`. 

```azurepowershell-interactive
$imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```

> [!NOTE]
> Bei Imagedefinitionen, die Images enthalten, die von Images von Drittanbietern stammen, müssen die Planinformationen genau mit den Planinformationen des Drittanbieterimages übereinstimmen. Schließen Sie die Planinformationen in die Imagedefinition ein, indem Sie `-PurchasePlanName`, `-PurchasePlanProduct` und `-PurchasePlanPublisher` hinzufügen, wenn Sie die Imagedefinition erstellen.
>

### <a name="purchase-plan-information"></a>Informationen zum Erwerbsplan

In einigen Fällen müssen Sie beim Erstellen einer VM aus einem Image die Erwerbsplaninformationen übergeben, wenn das Image auf einem Azure Marketplace-Image basiert. In diesen Fällen wird empfohlen, die Erwerbsplaninformationen in die Imagedefinition aufzunehmen. Weitere Informationen finden Sie in diesem Fall unter [Bereitstellen von Azure Marketplace-Erwerbsplaninformationen beim Erstellen von Images](marketplace-images.md).


## <a name="create-an-image-version"></a>Erstellen einer Imageversion

Erstellen Sie mit [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion) eine Imageversion aus der Momentaufnahme. 

Zulässige Zeichen für die Imageversion sind Zahlen und Punkte. Zahlen müssen im Bereich einer ganzen 32-Bit-Zahl liegen. Format: *Hauptversion*.*Nebenversion*.*Patch*.

Wenn Sie möchten, dass Ihr Image neben dem Betriebssystem-Datenträger einen weiteren Datenträger enthält, fügen Sie den Parameter `-DataDiskImage` hinzu, und legen Sie ihn auf die ID der Datenträgermomentaufnahme oder des verwalteten Datenträgers fest.

In diesem Beispiel lautet die Imageversion *1.0.0*. Sie wird in den Rechenzentren *USA, Westen-Mitte* und *USA, Süden-Mitte* repliziert. Bei der Auswahl der Zielregionen für die Replikation ist zu beachten, dass Sie auch die *Quell* region als Ziel für die Replikation angeben müssen.


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -TargetRegion $targetRegions  `
   -OSDiskImage @{Source = @{Id=$source.Id}; HostCaching = "ReadOnly" } `
   -PublishingProfileEndOfLifeDate '2025-01-01' `
   -asJob 
```

Es dauert eine Weile, das Image für alle Zielregionen zu replizieren. Daher haben wir zum Verfolgen des Fortschritts einen Auftrag erstellt. Um den Fortschritt des Auftrags anzuzeigen, geben Sie `$job.State` ein.

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> Sie müssen warten, bis die Imageversion vollständig erstellt und repliziert wurde, bevor Sie mit dieser Momentaufnahme eine weitere Imageversion erstellen können. 
>
> Sie können Ihre Imageversion auch in [zonenredundantem Speicher](../storage/common/storage-redundancy.md) speichern, indem Sie `-StorageAccountType Standard_ZRS` hinzufügen, wenn Sie die Imageversion erstellen.
>

## <a name="delete-the-source"></a>Löschen Sie die Quelle.

Wenn Sie sichergestellt haben, dass Ihre neue Imageversion ordnungsgemäß funktioniert, können Sie die Quelle für das Image mit [Remove-AzSnapshot](/powershell/module/Az.Compute/Remove-AzSnapshot) bzw. [Remove-AzDisk](/powershell/module/az.compute/remove-azdisk) löschen.


## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich vergewissert haben, dass die Replikation abgeschlossen ist, können Sie aus dem [spezialisierten Image](vm-specialized-image-version-powershell.md) eine VM erstellen.

Weitere Informationen zur Bereitstellung von Erwerbsplaninformationen finden Sie unter [Bereitstellen von Azure Marketplace-Erwerbsplaninformationen beim Erstellen von Images](marketplace-images.md).
