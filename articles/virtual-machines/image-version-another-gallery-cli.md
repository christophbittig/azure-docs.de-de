---
title: Kopieren einer Imageversion aus einem anderen Katalog mithilfe der CLI
description: Kopieren Sie mit der Azure-Befehlszeilenschnittstelle eine Imageversion aus einem anderen Katalog.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 904700344900d73b700bdfac01af2022425c7c69
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689019"
---
# <a name="copy-an-image-from-another-gallery-using-the-azure-cli"></a>Kopieren eines Images aus einem anderen Katalog mithilfe der Azure CLI

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Wenn Sie in Ihrer Organisation über mehrere Kataloge verfügen, können Sie Imageversionen auch aus vorhandenen Imageversionen in anderen Katalogen erstellen. Beispielsweise können Sie über einen Entwicklungs- und Testkatalog verfügen, um neue Images zu erstellen und zu testen. Wenn diese für die Verwendung in der Produktion bereit sind, können Sie sie mithilfe dieses Beispiels in einen Produktionskatalog kopieren. Sie können auch mit [Azure PowerShell](image-version-another-gallery-powershell.md) ein Image aus einem Image in einem anderen Katalog erstellen.



## <a name="before-you-begin"></a>Voraussetzungen

Um diesen Artikel nachvollziehen zu können, benötigen Sie einen vorhandenen Quellkatalog, eine Imagedefinition und eine Imageversion. Sie sollten auch über einen Zielkatalog verfügen. 

Die Quellimageversion muss in die Region repliziert werden, in der sich der Zielkatalog befindet. 

Ersetzen Sie beim Durcharbeiten dieses Artikels die Ressourcennamen, wo dies erforderlich ist.



## <a name="get-information-from-the-source-gallery"></a>Sammeln von Informationen aus dem Quellkatalog

Sie benötigen Informationen aus der Quellimagedefinition, damit Sie eine Kopie davon in Ihrem neuen Katalog erstellen können.

Rufen Sie mit [az sig list](/cli/azure/sig#az_sig_list) Informationen zu den verfügbaren Imagekatalogen ab, um Informationen zum Quellkatalog zu erhalten.

```azurecli-interactive 
az sig list -o table
```

Mit [az sig image-definition list](/cli/azure/sig/image-definition#az_sig_image_definition_list) listen Sie die Imagedefinitionen in einem Katalog auf. In diesem Beispiel suchen wir nach Imagedefinitionen im Katalog *myGallery* in der Ressourcengruppe *myGalleryRG*.

```azurecli-interactive 
az sig image-definition list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   -o table
```

Listen Sie die Versionen eines Images in einem Katalog mit [az sig image-version list](/cli/azure/sig/image-version#az_sig_image_version_list) auf, um die Imageversion zu finden, die Sie in Ihren neuen Katalog kopieren möchten. In diesem Beispiel suchen wir nach allen Imageversionen, die Teil der Imagedefinition *myImageDefinition* sind.

```azurecli-interactive
az sig image-version list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```

Sobald Sie über alle erforderlichen Informationen verfügen, können Sie mit [az sig image-version show](/cli/azure/sig/image-version#az_sig_image_version_show) die ID der Quellimageversion ermitteln.

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id" -o tsv
```


## <a name="create-the-image-definition"></a>Erstellen der Imagedefinition 

Sie müssen eine Imagedefinition erstellen, die dem Betriebssystem, dem Betriebssystemstatus und der Hyper-V-Generation der Imagedefinition entspricht, die Ihre Quellimageversion enthält. Sie können alle zum erneuten Erstellen der Imagedefinition in Ihrem neuen Katalog erforderlichen Informationen mit [az sig image-definition show](/cli/azure/sig/image-definition#az_sig_image_definition_show) anzeigen.

```azurecli-interactive
az sig image-definition show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
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
  "osType": "Linux",
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

Erstellen Sie eine neue Imagedefinition in Ihrem neuen Katalog mit den Informationen aus der obigen Ausgabe.


```azurecli-interactive 
az sig image-definition create \
   --resource-group myNewGalleryRG \
   --gallery-name myNewGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --hyper-v-generation V1 \
   --os-state specialized 
```

> [!NOTE]
> Bei Imagedefinitionen, die Images enthalten, die von Images von Drittanbietern stammen, müssen die Planinformationen genau mit den Planinformationen des Drittanbieterimages übereinstimmen. Schließen Sie die Planinformationen in die Imagedefinition ein, indem Sie `--plan-name`, `--plan-product` und `--plan-publisher` hinzufügen, wenn Sie die Imagedefinition erstellen.
>


## <a name="create-the-image-version"></a>Erstellen der Imageversion

Erstellen Sie Versionen mithilfe von [az image gallery create-image-version](/cli/azure/sig/image-version#az_sig_image_version_create). Sie müssen die ID des verwalteten Images, das als Grundlage zum Erstellen der Imageversion verwendet werden soll, als Eingabe übergeben. Sie können [az image list](/cli/azure/image?view#az_image_list) verwenden, um Informationen zu Images abzurufen, die sich in einer Ressourcengruppe befinden. 

Zulässige Zeichen für die Imageversion sind Zahlen und Punkte. Zahlen müssen im Bereich einer ganzen 32-Bit-Zahl liegen. Format: *Hauptversion*.*Nebenversion*.*Patch*.

In diesem Beispiel ist die Version des Images *1.0.0*, und es wird ein Replikat in der Region *USA, Süden-Mitte* und ein Replikat in der Region *USA, Osten* mithilfe von zonenredundantem Speicher erstellt.


```azurecli-interactive 
az sig image-version create \
   --resource-group myNewGalleryRG \
   --gallery-name myNewGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"
```

> [!NOTE]
> Sie müssen warten, bis die Imageversion vollständig erstellt und repliziert wurde, bevor Sie dieses verwaltete Image verwenden können, um eine weitere Imageversion zu erstellen.
>
> Fügen Sie beim Erstellen der Imageversion entweder `--storage-account-type  premium_lrs` hinzu, um Ihr Image in Premium-Speicher zu speichern, oder `--storage-account-type  standard_zrs`, um Ihr Image in [zonenredundantem Speicher](../storage/common/storage-redundancy.md) zu speichern.
>

## <a name="next-steps"></a>Nächste Schritte

Erstellen Sie eine VM aus einer [generalisierten](vm-generalized-image-version-cli.md) oder einer [spezialisierten](vm-specialized-image-version-cli.md) Imageversion.

Probieren Sie auch [Azure VM Image Builder](./image-builder-overview.md) aus, das Ihnen beim Automatisieren der Erstellung von Imageversionen helfen kann. Sie können es sogar zum Aktualisieren und [Erstellen einer neuen Imageversion aus einer vorhandenen](./linux/image-builder-gallery-update-image-version.md) verwenden. 

Weitere Informationen zur Bereitstellung von Erwerbsplaninformationen finden Sie unter [Bereitstellen von Azure Marketplace-Erwerbsplaninformationen beim Erstellen von Images](marketplace-images.md).
