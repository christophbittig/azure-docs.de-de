---
title: Erstellen einer Skalierungsgruppe aus einem generalisierten Image mit Azure CLI
description: Erstellen Sie eine Skalierungsgruppe mithilfe eines generalisierten Images in einer Shared Image Gallery. mit der Azure CLI.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: mimckitt
ms.openlocfilehash: a9ac2bd794ee4aa9f531ad9fcd2f07bb744d02c8
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122691249"
---
# <a name="create-a-scale-set-from-a-generalized-image-with-azure-cli"></a>Erstellen einer Skalierungsgruppe aus einem generalisierten Image mit Azure CLI

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows VMs :heavy_check_mark: Einheitliche Skalierungsgruppen

Erstellen Sie eine Skalierungsgruppe aus einer generalisierten Imageversion in einem [Katalog mit freigegebenen Images](../virtual-machines/shared-image-galleries.md) mithilfe der Azure-Befehlszeilenschnittstelle. Wenn Sie eine Skalierungsgruppe mit einer spezialisierten Imageversion erstellen möchten, finden Sie weitere Informationen unter [Erstellen von Skalierungsgruppeninstanzen aus einem spezialisierten Image](instance-specialized-image-version-cli.md).

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial Version 2.4.0 oder höher der Azure CLI ausführen. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI]( /cli/azure/install-azure-cli).

Ersetzen Sie bei Bedarf die Ressourcennamen in diesem Beispiel. 

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

Erstellen Sie die Skalierungsgruppe mithilfe von [`az vmss create`](/cli/azure/vmss#az_vmss_create). 

Verwenden Sie die Imagedefinitions-ID für `--image`, um die Skalierungsgruppeninstanzen auf Grundlage der neuesten verfügbaren Imageversion zu erstellen. Sie können die Skalierungsgruppeninstanzen auch auf Grundlage einer bestimmten Version erstellen, indem Sie die Imageversions-ID für `--image` verwenden. Beachten Sie, dass die Verwendung einer bestimmten Imageversion bedeutet, dass die Automatisierung fehlschlagen kann, wenn diese bestimmte Imageversion nicht verfügbar ist, weil sie gelöscht oder aus der Region entfernt wurde. Es wird empfohlen, die Imagedefinitions-ID zum Erstellen der neuen VM zu verwenden, es sei denn, eine bestimmte Imageversion ist erforderlich.

In diesem Beispiel erstellen Sie Instanzen auf Grundlage der neuesten Version des *myImageDefinition*-Images.

```azurecli
az group create --name myResourceGroup --location eastus
az vmss create \
   --resource-group myResourceGroup \
   --name myScaleSet \
   --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" 
   --admin-username azureuser \
   --generate-ssh-keys
```

Die Erstellung und Konfiguration aller Ressourcen und virtuellen Computer der Skalierungsgruppe dauert einige Minuten.

## <a name="next-steps"></a>Nächste Schritte
[Azure Image Builder (Vorschauversion)](../virtual-machines/image-builder-overview.md) hilft beim Automatisieren der Erstellung von Imageversionen. Sie können den Dienst sogar zum Aktualisieren und [Erstellen einer neuen Imageversion aus einer vorhandenen](../virtual-machines/linux/image-builder-gallery-update-image-version.md) verwenden. 

Sie können auch mithilfe von Vorlagen eine Ressource im Katalog für freigegebene Images erstellen. Es stehen mehrere Azure-Schnellstartvorlagen zur Verfügung: 

- [Erstellen eines Katalogs mit freigegebenen Images](https://azure.microsoft.com/resources/templates/sig-create/)
- [Erstellen einer Imagedefinition in einem Katalog mit freigegebenen Images](https://azure.microsoft.com/resources/templates/sig-image-definition-create/)
- [Erstellen einer Imageversion in einem Katalog mit freigegebenen Images](https://azure.microsoft.com/resources/templates/sig-image-version-create/)

Weitere Informationen zu Katalogen mit geteilten Images finden Sie in der [Übersicht](../virtual-machines/shared-image-galleries.md). Sollten Probleme auftreten, sehen Sie unter [Problembehandlung für Kataloge mit freigegebenen Images](../virtual-machines/troubleshooting-shared-images.md) nach.