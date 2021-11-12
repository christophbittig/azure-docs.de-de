---
title: Erstellen einer Imagedefinition und Imageversion
description: Erfahren Sie, wie Sie ein Image in der Azure Compute Gallery erstellen.
author: cynthn
ms.service: virtual-machines
ms.subservice: gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 08/31/2021
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: e52457a67e2c653d1fa4e191ee568b35cb7e365f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131471514"
---
# <a name="create-an-image-definition-and-an-image-version"></a>Erstellen einer Imagedefinition und einer Imageversion 

Eine [Azure Compute Gallery](shared-image-galleries.md) (früher Shared Image Gallery) vereinfacht das Freigeben benutzerdefinierter Images in Ihrer Organisation. Benutzerdefinierte Images sind wie Marketplace-Images, Sie erstellen sie jedoch selbst. Benutzerdefinierte Images können zum Laden von Bereitstellungsaufgaben verwendet werden, z.B. zum Vorabladen von Anwendungen sowie für Anwendungskonfigurationen und andere Betriebssystemkonfigurationen. 

Mit der Azure Compute Gallery können Sie ihre benutzerdefinierten Images der virtuellen Computer für andere Personen in Ihrer Organisation, innerhalb eines AAD-Mandanten, innerhalb einer Region oder zwischen den Regionen freigeben. Wählen Sie aus, welche Images Sie teilen möchten, in welchen Regionen Sie sie verfügbar machen möchten, und mit wem Sie sie teilen möchten. Sie können mehrere Galerien erstellen, damit Sie die Images sinnvoll gruppieren können. 

Die Funktion „Azure Compute Gallery“ verfügt über mehrere Ressourcentypen:

[!INCLUDE [virtual-machines-shared-image-gallery-resources](./includes/virtual-machines-shared-image-gallery-resources.md)]


## <a name="before-you-begin"></a>Vorbereitung

Für diesen Abschnitt müssen Sie über eine vorhandene Azure Compute Gallery und einer Quelle für Ihr Image in Azure verfügen. Folgende Imagequellen sind möglich:
- Eine VM in Ihrem Abonnement. Sie können sowohl für [spezialisierte als auch für generalisierte](shared-image-galleries.md#generalized-and-specialized-images) VMs ein Image erstellen. 
- Ein verwaltetes Image,
- Verwaltete Datenträger für Betriebssystem und Daten.
- Datenträger für Betriebssystem und Daten als virtuelle Festplatten in einem Speicherkonto.
- Andere Imageversionen in demselben Katalog oder in einem anderen Katalog in demselben Abonnement.

Wenn das Image Datenträger für Daten enthalten wird, darf die Datenträgergröße nicht mehr als 1 TB betragen.

Namen für Imagedefinition können aus Groß- und Kleinbuchstaben, Zahlen, Punkten und (Binde)Strichen bestehen. Weitere Informationen zu den Werten, die Sie für eine Imagedefinition angeben können, finden Sie unter [Imagedefinitionen](shared-image-galleries.md#image-definitions).

Zulässige Zeichen für die Imageversion sind Zahlen und Punkte. Zahlen müssen im Bereich einer ganzen 32-Bit-Zahl liegen. Format: *Hauptversion*.*Nebenversion*.*Patch*.

Ersetzen Sie beim Durcharbeiten dieses Artikels die Ressourcennamen, wo dies erforderlich ist.

## <a name="create-an-image"></a>Erstellen eines Images 

Wählen Sie unten eine Option zum Erstellen Ihrer Imagedefinition und Imageversion aus:

### <a name="portal"></a>[Portal](#tab/portal)

Informationen zum Erstellen eines Images von einer VM im Portal finden Sie unter [Capture an image of a VM](capture-image-portal.md) (Erfassen eines Images eines virtuellen Computers). 

Führen Sie die folgenden Schritte aus, um ein Image mit einer anderen Quelle als einer VM zu erstellen.

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com) und suchen Sie dann nach **Azure Compute Gallery**, was Sie dann bitte auswählen.
1. Wählen Sie den gewünschten Katalog aus der Liste aus.
1. Wählen Sie oben auf der Seite Ihrer Galerie **Hinzufügen** und dann im Dropdownmenü **Imagedefinition für virtuelle Computer** aus. 
1. Wählen Sie auf der Seite **Neue Imagedefinition zu Azure Compute Gallery hinzufügen** auf der Registerkarte **Grundlagen** eine **Region** aus. 
1. Geben Sie für **Name der Imagedefinition** einen Namen wie *meineImageDefinition* ein.
1. Wählen Sie für **Betriebssystem** die richtige Option basierend auf Ihrer Quelle aus.  
1. Wählen Sie für **VM-Generation** die Option basierend auf Ihrer Quelle aus. In den meisten Fällen wird dies *1. Generation* sein. Weitere Informationen finden Sie unter [Unterstützung für VMs der Generation 2 in Azure](generation-2.md).
1. Wählen Sie für **Betriebssystemstatus** die richtige Option basierend auf Ihrer Quelle aus. Weitere Informationen finden Sie unter [Generalisierte und spezialisierte Images](shared-image-galleries.md#generalized-and-specialized-images).
1. Geben Sie für **Herausgeber** einen eindeutigen Namen wie *myPublisher* ein. 
1. Geben Sie für **Angebot** einen eindeutigen Namen wie *myOffer* ein.
1. Geben Sie für **SKU** einen eindeutigen Namen wie *mySKU* ein.
1. Klicken Sie im unteren Seitenbereich auf **Überprüfen und erstellen**.
1. Nachdem die Imagedefinition erfolgreich überprüft wurde, wählen Sie **Erstellen**.
1. Wählen Sie nach Abschluss der Bereitstellung die Option **Zu Ressourcengruppe wechseln**.
1. Wählen Sie auf der Seite für Ihre Imagedefinition auf der Registerkarte **Erste Schritte** die Option **Version erstellen** aus.
1. Wählen Sie unter **Region** die Region aus, in der das Image erstellt werden soll. In einigen Fällen muss sich die Quelle in derselben Region befinden, in der das Image erstellt wird. Wenn Ihre Quelle in späteren Dropdownlisten nicht aufgeführt wird, versuchen Sie, die Region für das Image zu ändern. Sie können das Image später jederzeit in andere Regionen replizieren.
1. Geben Sie für **Versionsnummer** eine Zahl wie *1.0.0* ein. Der Name der Imageversion sollte dem Format *major*.*minor*.*patch* mit ganzen Zahlen entsprechen. 
1. Wählen Sie unter **Quelle** aus der Dropdownliste den Dateityp aus, den Sie für Ihre Quelle verwenden. In der folgenden Tabelle finden Sie spezifische Angaben zu den einzelnen Quellentypen.

    | `Source` | Weitere Felder |
    |---|---|
    | Datenträger oder Momentaufnahmen | – Wählen Sie für **Betriebssystemdatenträger** den Datenträger oder die Momentaufnahme aus der Dropdownliste aus. <br> – Zum Hinzufügen eines Datenträgers geben Sie die LUN-Nummer ein, und wählen Sie dann in der Dropdownliste den Datenträger aus. |
    | Imageversion | – Wählen Sie in der Dropdownliste den **Quellkatalog** aus. <br> – Wählen Sie in der Dropdownliste die richtige Bilddefinition aus. <br>– Wählen Sie in der Dropdownliste die vorhandene Imageversion aus, die Sie verwenden möchten. |
    | Verwaltetes Image | – Wählen Sie in der Dropdownliste das **Quellimage** aus. <br>Das verwaltete Image muss sich in derselben Region befinden, die Sie unter **Instanzdetails** ausgewählt haben.
    | VHD in einem Speicherkonto | Wählen Sie **Durchsuchen** aus, um das Speicherkonto für die VHD auszuwählen. |

1. Behalten Sie in **Exclude from latest** den Standardwert *No* bei, es sei denn, diese Version soll nicht verwendet werden, wenn Sie eine VM mit `latest` anstelle einer Versionsnummer erstellen.
1. Wählen Sie für **End of life date** ein Datum aus dem Kalender aus, an dem diese Version Ihrer Meinung nach nicht mehr verwendet werden sollte.
1. Wählen Sie auf der Registerkarte **Replikation** in der Dropdownliste den Speichertyp aus.
1. Legen Sie die **Anzahl von Standardreplikaten** fest. Diese können Sie für jede hinzugefügte Region überschreiben. 
1. Sie müssen in die Quellregion replizieren, damit sich das erste Replikat in der Liste in der Region befindet, in der Sie das Image erstellt haben. Sie können weitere Replikate hinzufügen, indem Sie die Region in der Dropdownliste auswählen und die Anzahl der Replikate nach Bedarf anpassen.
1. Wählen Sie abschließend **Überprüfen + Erstellen** aus. Azure überprüft die Konfiguration.
1. Nachdem die Imageversion erfolgreich überprüft wurde, wählen Sie **Erstellen**.
1. Wählen Sie nach Abschluss der Bereitstellung die Option **Zu Ressourcengruppe wechseln**.

Es kann eine Weile dauern, bis das Image in alle Zielregionen repliziert ist.

Sie können eine vorhandene VM auch über das Portal als Image erfassen. Weitere Informationen finden Sie unter [Erstellen eines Images eines virtuellen Computers im Portal](capture-image-portal.md).

### <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli)

Imagedefinitionen erstellen eine logische Gruppierung von Images. Sie werden verwendet, um Informationen über die Imageversionen zu verwalten, die in ihnen erstellt werden.

Erstellen Sie mithilfe von [az sig image-definition create](/cli/azure/sig/image-definition#az_sig_image_definition_create) eine Imagedefinition in einem Katalog. Stellen Sie sicher, dass die Imagedefinition den richtigen Typ aufweist. Wenn Sie die VM generalisiert haben (über Sysprep für Windows oder waagent -deprovision für Linux) sollten Sie unter Verwendung von `--os-state generalized` eine generalisierte Imagedefinition erstellen. Wenn Sie die VM verwenden möchten, ohne vorhandene Benutzerkonten zu entfernen, erstellen Sie mithilfe von `--os-state specialized` eine spezialisierte Imagedefinition.

Weitere Informationen zu den Parametern, die Sie für eine Imagedefinition angeben können, finden Sie unter [Imagedefinitionen](shared-image-galleries.md#image-definitions).

In diesem Beispiel heißt die Imagedefinition *myImageDefinition* und ist für ein [spezialisiertes](shared-image-galleries.md#generalized-and-specialized-images) Linux-Betriebssystemimage vorgesehen. Verwenden Sie `--os-type Windows`, um eine Definition für Images zu erstellen, die ein Windows-Betriebssystem verwenden. 

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```

> [!NOTE]
> Bei Imagedefinitionen, die Images enthalten, die von Marketplace-Images von Drittanbietern stammen, müssen die Planinformationen genau mit den Planinformationen des Drittanbieterimages übereinstimmen. Schließen Sie die Planinformationen in die Imagedefinition ein, indem Sie `--plan-name`, `--plan-product` und `--plan-publisher` hinzufügen, wenn Sie die Imagedefinition erstellen.
>

**Erstellen der Imageversion**

Erstellen Sie eine Imageversion mit [az sig image version create](/cli/azure/sig/image-version#az_sig_image_version_create).  

Die Syntax zum Erstellen des Images ändert sich abhängig davon, was Sie als Quelle verwenden. Sie können die Quelltypen mischen, solange Sie nur über eine Betriebssystemquelle verfügen. Sie können auch unterschiedliche Quellen für die einzelnen Datenträger verwenden.

| `Source`  | Parametersatz |
|---|---|
| **Betriebssystemdatenträger**| |
| VM mit der VM-ID| `--managed-image <Resource ID of the VM>` |
| Verwaltetes Image oder eine andere Imageversion | `--managed-image <Resource ID of the managed image or image version` |
| Momentaufnahme oder verwalteter Datenträger | `--os-snapshot <Resource ID of the snapshot or managed disk>` |
| VHD in einem Speicherkonto | `--os-vhd-uri <URI> --os-vhd-storage-account <storage account name>`.  | 
| **Datenträger für Daten** |
| Momentaufnahme oder verwalteter Datenträger | `--data-snapshots <Resource ID of the snapshot or managed disk> --data-snapshot-luns <LUN number>` |
| VHD in einem Speicherkonto | `--data-vhds-sa <storageaccountname> --data-vhds-uris <URI> --data-vhds-luns <LUN number>` |

Ausführliche Beispiele zum Angeben verschiedener Quellen für Ihr Image finden Sie unter den [Beispielen für az sig image-version create](/cli/azure/sig/image-version#az_sig_image_version_create-examples).

Im folgenden Beispiel erstellen wir ein Image von einer **VM**. Die Version des Images lautet *1.0.0*, und es werden unter Verwendung von zonenredundantem Speicher zwei Replikate in der Region *USA, Westen-Mitte*, ein Replikat in der Region *USA, Süden-Mitte* und ein Replikat in der Region *USA, Osten 2* erstellt. Die Replikationsregionen müssen die Region beinhalten, in der sich der virtuelle Quellcomputer befindet.

Es empfiehlt sich, den virtuellen Computer vor dem Erstellen eines Images zu beenden oder die Zuordnung aufzuheben.

Ersetzen Sie den Wert für `--managed-image` in diesem Beispiel durch die ID Ihrer VM.

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "westcentralus" "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> Sie müssen warten, bis die Imageversion vollständig erstellt und repliziert wurde, bevor Sie dieses verwaltete Image verwenden können, um eine weitere Imageversion zu erstellen.
>
> Fügen Sie beim Erstellen der Imageversion entweder `--storage-account-type  premium_lrs` hinzu, um Ihr Image in Premium-Speicher zu speichern, oder `--storage-account-type  standard_zrs`, um Ihr Image in [zonenredundantem Speicher](../storage/common/storage-redundancy.md) zu speichern.

### <a name="powershell"></a>[PowerShell](#tab/powershell)

Imagedefinitionen erstellen eine logische Gruppierung von Images. Stellen Sie beim Erstellen der Imagedefinition sicher, dass diese alle zutreffenden Informationen beinhaltet. Wenn Sie die Quelle für das Image generalisiert haben (mit Sysprep für Windows oder waagent-deprovision für Linux), sollten Sie eine Imagedefinition unter Verwendung von `-OsState generalized` erstellen. Wenn Sie die Quelle nicht generalisiert haben, erstellen Sie eine Imagedefinition unter Verwendung von `-OsState specialized`.

Weitere Informationen zu den Werten, die Sie für eine Imagedefinition angeben können, finden Sie unter [Imagedefinitionen](./shared-image-galleries.md#image-definitions).

Erstellen Sie die Imagedefinition mit [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion). 

In diesem Beispiel heißt die Imagedefinition *myImageDefinition* und ist für eine spezialisierte VM gedacht, die unter Windows ausgeführt wird. Verwenden Sie `-OsType Linux`, um eine Definition für Images unter Linux zu erstellen. 

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

**Erstellen einer Imageversion**

Erstellen Sie mithilfe von [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion) eine Imageversion. 

Die Syntax zum Erstellen des Images ändert sich abhängig davon, was Sie als Quelle verwenden. 

| `Source`  | Parametersatz |
|---|---|
| **Betriebssystemdatenträger**| |
| VM mit der VM-ID| `-SourceImageId <Resource ID of the VM>` |
| Verwaltetes Image oder eine andere Imageversion | `-SourceImageId <Resource ID of the managed image or image version` |
| Momentaufnahme oder verwalteter Datenträger | `-OSDiskImage <Resource ID of the snapshot or managed disk>` |
| **Datenträger für Daten** |
| Momentaufnahme oder verwalteter Datenträger | `-DataDiskImage @{Source = @{Id=<source_id>}; Lun=<LUN>; SizeInGB = <Size in GB>; HostCaching = <Caching> }` |


Im folgenden Beispiel erstellen wir eine Imageversion von einer VM. Es empfiehlt sich, den virtuellen Computer vor dem Erstellen eines Images mit [Stop-AzVM](/powershell/module/az.compute/stop-azvm) zu beenden oder die Zuordnung aufzuheben.

In diesem Beispiel lautet die Imageversion *1.0.0*. Sie wird in den Rechenzentren *USA, Westen-Mitte* und *USA, Süden-Mitte* repliziert. Bei der Auswahl der Zielregionen für die Replikation ist zu beachten, dass Sie auch die *Quell* region als Ziel für die Replikation angeben müssen.


```azurepowershell-interactive
   $region1 = @{Name='South Central US';ReplicaCount=1}
   $region2 = @{Name='East US';ReplicaCount=2}
   $targetRegions = @($region1,$region2)

$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -TargetRegion $targetRegions  `
   -SourceImageId $sourceVm.Id.ToString() `
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
> Fügen Sie beim Erstellen der Imageversion entweder `-StorageAccountType Premium_LRS` hinzu, um Ihr Image in Premium-Speicher zu speichern, oder `-StorageAccountType Standard_ZRS`, um Ihr Image in [zonenredundantem Speicher](../storage/common/storage-redundancy.md) zu speichern.
>

### <a name="rest"></a>[REST](#tab/rest)

Erstellen einer Imagedefinition über die [REST-API](/rest/api/compute/gallery-images/create-or-update)

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/galleries/{galleryName}/images/{galleryDefinitionName}?api-version=2019-12-01

{
    "location": "eastus",
    "properties": {
        "hyperVGeneration": "V1",
        "identifier": {
            "offer": "myOffer",
            "publisher": "myPublisher",
            "sku": "mySKU"
        },
        "osState": "Specialized",
        "osType": "Linux",
    },
}
```

Erstellen Sie eine Imageversion über die [REST-API](/rest/api/compute/gallery-image-versions/create-or-update). In diesem Beispiel erstellen wir eine Imageversion von einer VM. Um eine andere Quelle zu verwenden, übergeben Sie die Ressourcen-ID für die Quelle (z. B. die ID der Momentaufnahme des Betriebssystemdatenträgers).

```rest
# @name imageVersion
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/galleries/{galleryName}/images/{galleryDefinitionName}/versions/{galleryImageVersionName}?api-version=2019-12-01

{
    "location": "{region}",
    "properties": {
        "publishingProfile": {
            "endOfLifeDate": "2024-12-02T00:00:00+00:00",
            "replicaCount": 1,
            "storageAccountType": "Standard_ZRS",
            "targetRegions": [
                {
                    "name": "eastus",
                    "regionalReplicaCount": 2,
                    "storageAccountType": "Standard_LRS",
                },
                {
                    "name": "westus2",
                }
            ]
        },
        "storageProfile": {
            "source": {
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}"
            }
        }
    }
}
```

---


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Bereitstellung von Erwerbsplaninformationen finden Sie unter [Bereitstellen von Azure Marketplace-Erwerbsplaninformationen beim Erstellen von Images](marketplace-images.md).
