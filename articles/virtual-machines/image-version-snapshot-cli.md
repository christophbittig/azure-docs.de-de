---
title: 'CLI: Erstellen eines Images aus einer Momentaufnahme oder einem verwalteten Datenträger in einer Shared Image Gallery-Instanz'
description: Erfahren Sie, wie Sie aus einer Momentaufnahme oder einem verwalteten Datenträger in einer Shared Image Gallery-Instanz mithilfe der Azure CLI ein Image erstellen.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 06/30/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: b2c4a56be15aa8a331224958329ba263bc9a8426
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/15/2021
ms.locfileid: "114205079"
---
# <a name="create-an-image-from-a-managed-disk-or-snapshot-in-a-shared-image-gallery-using-the-azure-cli"></a>Erstellen eines Images aus einem verwalteten Datenträger oder einer Momentaufnahme in einer Shared Image Gallery-Instanz mithilfe der Azure CLI

Wenn Sie über eine vorhandene Momentaufnahme oder einen verwalteten Datenträger verfügen, die Sie zu einer Shared Image Gallery-Instanz migrieren möchten, können Sie direkt aus dem verwalteten Datenträger oder der Momentaufnahme ein Shared Image Gallery-Image erstellen. Nachdem Sie das neue Image getestet haben, können Sie den verwalteten Quelldatenträger oder die Quellmomentaufnahme löschen. Sie können auch [Azure PowerShell](image-version-snapshot-powershell.md) verwenden, um ein Image aus einen verwalteten Datenträger oder einer Momentaufnahme in einer Shared Image Gallery-Instanz zu erstellen.

Images in Imagekatalogen weisen zwei Komponenten auf, die in diesem Beispiel erstellt werden:
- Eine **Imagedefinition** enthält Informationen zum Image und zu den Anforderungen für dessen Verwendung. Dies umfasst Angaben dazu, ob es sich um ein Windows- oder Linux-Image handelt, ob es spezialisiert oder generalisiert ist sowie welche Mindest- und Höchstanforderungen für den Arbeitsspeicher gelten. Außerdem sind Versionshinweise enthalten. Es ist eine Definition eines Imagetyps. 
- Bei der Verwendung eines Katalogs mit freigegebenen Images werden **Imageversionen** verwendet, um VMs zu erstellen. Sie können nach Bedarf mehrere Versionen eines Images für Ihre Umgebung haben. Bei der Erstellung einer VM werden anhand der Imageversionen neue Datenträger für die VM erstellt. Imageversionen können mehrmals verwendet werden.


## <a name="before-you-begin"></a>Voraussetzungen

Sie benötigen eine Momentaufnahme oder einen verwalteten Datenträger, um die Schritte in diesem Artikel ausführen zu können. 

Wenn Sie einen Datenträger hinzufügen möchten, darf dieser nicht größer als 1 TB sein.

Ersetzen Sie beim Durcharbeiten dieses Artikels die Ressourcennamen, wo dies erforderlich ist.

## <a name="find-the-snapshot-or-managed-disk"></a>Suchen nach der Momentaufnahme oder dem verwalteten Datenträger 

Mit [az snapshot list](/cli/azure/snapshot#az_snapshot_list) können Sie eine Liste der Momentaufnahmen anzeigen, die in einer Ressourcengruppe verfügbar sind. 

```azurecli-interactive
az snapshot list --query "[].[name, id]" -o tsv
```

Sie können auch einen verwalteten Datenträger anstelle einer Momentaufnahme verwenden. Um einen verwalteten Datenträger abzurufen, verwenden Sie [az disk list](/cli/azure/disk#az_disk_list). 

```azurecli-interactive
az disk list --query "[].[name, id]" -o tsv
```

Nachdem Sie über die ID der Momentaufnahme oder des verwalteten Datenträgers verfügen, weisen Sie sie zur späteren Verwendung einer Variablen namens `$source` zu.

Sie können dasselbe Verfahren verwenden, um alle Datenträger abzurufen, die Sie in das Image aufnehmen möchten. Weisen Sie sie Variablen zu, und verwenden Sie diese Variablen später beim Erstellen der Imageversion.


## <a name="find-the-gallery"></a>Suchen des Katalogs

Sie benötigen Informationen zum Imagekatalog, um die Imagedefinition erstellen zu können.

Listen Sie Informationen zu den verfügbaren Imagekatalogen mit [az sig list](/cli/azure/sig#az_sig_list) auf. Notieren Sie sich den Namen des Katalogs, in dem sich die Ressourcengruppe befindet, zur späteren Verwendung.

```azurecli-interactive 
az sig list -o table
```


## <a name="create-an-image-definition"></a>Erstellen einer Imagedefinition

Imagedefinitionen erstellen eine logische Gruppierung von Images. Sie werden zum Verwalten von Informationen zum Image verwendet. Namen für Imagedefinition können aus Groß- und Kleinbuchstaben, Zahlen, Punkten und (Binde)Strichen bestehen. 

Stellen Sie beim Erstellen der Imagedefinition sicher, dass diese alle richtigen Informationen beinhaltet. In diesem Beispiel wird davon ausgegangen, dass es sich um die Momentaufnahme oder den verwalteten Datenträger einer VM handelt, die verwendet wird und nicht generalisiert wurde. Wenn der verwaltete Datenträger oder die Momentaufnahme von einem generalisierten Betriebssystem (nach dem Ausführen von Sysprep unter Windows bzw. [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` oder `-deprovision+user` unter Linux) erstellt wurde, ändern Sie den Wert für `-OsState` in `generalized`. 

Weitere Informationen zu den Werten, die Sie für eine Imagedefinition angeben können, finden Sie unter [Imagedefinitionen](./shared-image-galleries.md#image-definitions).

Erstellen Sie mithilfe von [az sig image-definition create](/cli/azure/sig/image-definition#az_sig_image_definition_create) eine Imagedefinition im Katalog.

In diesem Beispiel heißt die Imagedefinition *myImageDefinition* und ist für ein [spezialisiertes](./shared-image-galleries.md#generalized-and-specialized-images) Linux-Betriebssystemimage vorgesehen. Verwenden Sie `--os-type Windows`, um eine Definition für Images zu erstellen, die ein Windows-Betriebssystem verwenden. 

In diesem Beispiel heißt der Katalog *myGallery* und befindet sich in der Ressourcengruppe *myGalleryRG*. Der Name der Imagedefinition lautet *mImageDefinition*.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
imageDef=myImageDefinition
az sig image-definition create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```

> [!NOTE]
> Bei Imagedefinitionen, die Images enthalten, die von Images von Drittanbietern stammen, müssen die Planinformationen genau mit den Planinformationen des Drittanbieterimages übereinstimmen. Schließen Sie die Planinformationen in die Imagedefinition ein, indem Sie `--plan-name`, `--plan-product` und `--plan-publisher` hinzufügen, wenn Sie die Imagedefinition erstellen.
>

## <a name="create-the-image-version"></a>Erstellen der Imageversion

Erstellen Sie mithilfe von [az image gallery create-image-version](/cli/azure/sig/image-version#az_sig_image_version_create) eine Imageversion. 

Zulässige Zeichen für die Imageversion sind Zahlen und Punkte. Zahlen müssen im Bereich einer ganzen 32-Bit-Zahl liegen. Format: *Hauptversion*.*Nebenversion*.*Patch*.

In diesem Beispiel ist die Version des Images *1.0.0*, und es wird ein Replikat in der Region *USA, Süden-Mitte* und ein Replikat in der Region *USA, Osten 2* mithilfe von zonenredundantem Speicher erstellt. Bei der Auswahl der Zielregionen für die Replikation ist zu beachten, dass Sie auch die *Quellregion* des verwalteten Datenträgers oder der Momentaufnahme als Ziel für die Replikation angeben müssen.

Übergeben Sie die ID der Momentaufnahme oder des verwalteten Datenträgers im Parameter `--os-snapshot`.


```azurecli-interactive 
az sig image-version create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus2=1=standard_zrs" \
   --replica-count 2 \
   --os-snapshot $source
```

Wenn Sie Datenträger in das Image aufnehmen möchten, müssen Sie sowohl den auf die LUN festgelegten Parameter `--data-snapshot-luns` als auch den auf die ID des Datenträgers oder der Momentaufnahme festgelegten Parameter `--data-snapshots` einschließen.

> [!NOTE]
> Sie müssen warten, bis die Imageversion vollständig erstellt und repliziert wurde, bevor Sie dieses verwaltete Image verwenden können, um eine weitere Imageversion zu erstellen.
>
> Sie können alle Ihre Imageversionsreplikate auch in [zonenredundantem Speicher](../storage/common/storage-redundancy.md) speichern, indem Sie `--storage-account-type standard_zrs` hinzufügen, wenn Sie die Imageversion erstellen.
>

## <a name="next-steps"></a>Nächste Schritte

Erstellen Sie eine VM aus einer [spezialisierten Imageversion](vm-specialized-image-version-cli.md).

Weitere Informationen zur Bereitstellung von Erwerbsplaninformationen finden Sie unter [Bereitstellen von Azure Marketplace-Erwerbsplaninformationen beim Erstellen von Images](marketplace-images.md).
