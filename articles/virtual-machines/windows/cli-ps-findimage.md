---
title: Suchen und Verwenden von Marketplace-Erwerbsplaninformationen mithilfe von PowerShell
description: Verwenden Sie die Azure PowerShell, um Image-URNs und Erwerbsplanparameter, wie Herausgeber, Angebot, SKU und Version, für Marketplace-VM-Images zu finden.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 03/17/2021
ms.author: cynthn
ms.custom: contperf-fy21q3, devx-track-azurepowershell
ms.openlocfilehash: b445b52e1e4a3131f7830d6ea167046109d0e2af
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131466749"
---
# <a name="find-and-use-azure-marketplace-vm-images-with-azure-powershell"></a>Suchen und Verwenden von VM-Images im Azure Marketplace mit Azure PowerShell

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen


Dieser Artikel beschreibt, wie Sie mit Azure PowerShell nach VM-Images im Azure Marketplace suchen. Sie können dann ein Marketplace-Image und Informationen zum Plan angeben, wenn Sie eine VM erstellen.

Sie können verfügbare Images und Angebote auch über den [Azure Marketplace](https://azuremarketplace.microsoft.com/) oder [Azure CLI](../linux/cli-ps-findimage.md) durchsuchen. 

## <a name="terminology"></a>Begriff

Ein Marketplace-Image in Azure hat die folgenden Attribute:

* **Publisher** (Herausgeber): Die Organisation, die das Image erstellt hat. Beispiele: Canonical, MicrosoftWindowsServer
* **Offer** (Angebot): Der Name einer Gruppe verwandter Images, die von einem Herausgeber erstellt wurden. Beispiele: UbuntuServer, WindowsServer
* **SKU**: Eine Instanz eines Angebots, etwa eine Hauptversion einer Distribution. Beispiele: 18.04-LTS, 2019-Datacenter
* **Version**: Die Versionsnummer einer Image-SKU. 

Diese Werte können einzeln oder als Image-*URN* übergeben werden, wobei die Werte durch den Doppelpunkt (:) getrennt kombiniert werden. Beispiel: *Herausgeber*:*Angebot*:*SKU*:*Version*. Sie können die Versionsnummer im URN durch `latest` ersetzen, um die neueste Version des Images zu verwenden. 

Wenn der Imageherausgeber zusätzliche Lizenz- und Kaufbedingungen angibt, müssen Sie diese akzeptieren, bevor Sie das Image verwenden können.  Weitere Informationen finden Sie unter [Annehmen der Geschäftsbedingungen des Erwerbsplans](#accept-purchase-plan-terms).

## <a name="list-images"></a>Auflisten von Images

Sie können PowerShell verwenden, um eine Liste von Images einzugrenzen. Ersetzen Sie die Werte der Variablen entsprechend Ihren Anforderungen.

1. Auflisten der Image-Herausgeber mithilfe von [Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher).
    
    ```powershell
    $locName="<location>"
    Get-AzVMImagePublisher -Location $locName | Select PublisherName
    ```
1. Auflistung der Angebote eines bestimmten Anbieters mithilfe von [Get-AzVMImageOffer](/powershell/module/az.compute/get-azvmimageoffer).
    
    ```powershell
    $pubName="<publisher>"
    Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
    ```
1. Auflisten der verfügbaren SKUs für einen bestimmten Herausgeber und ein Angebot mithilfe von [Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku).
    
    ```powershell
    $offerName="<offer>"
    Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
    ```
1. Auflistung für eine SKU der Versionen des Images mithilfe von [Get-AzVMImage](/powershell/module/az.compute/get-azvmimage).

    ```powershell
    $skuName="<SKU>"
    Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
    ```
    Sie können auch `latest` verwenden, wenn Sie das neueste Image und nicht eine bestimmte ältere Version verwenden möchten.


Nun können Sie Herausgeber, Angebot, SKU und Version Ihrer Wahl in einem URN kombinieren (Trennung der Werte durch „:“). Übergeben Sie diesen URN mit dem Parameter `-Image`, wenn Sie einen virtuellen Computer mit dem Cmdlet [New-AzVM](/powershell/module/az.compute/new-azvm) erstellen. Sie können auch die Versionsnummer im URN durch `latest` ersetzen, um die neueste Version des Images zu erhalten.

Wenn Sie einen virtuellen Computer mithilfe einer Resource Manager-Vorlage bereitstellen, legen Sie die Imageparameter einzeln in den `imageReference`-Eigenschaften fest. Informationen finden Sie in der [Vorlagenreferenz](/azure/templates/microsoft.compute/virtualmachines).


## <a name="view-purchase-plan-properties"></a>Eigenschaften des Erwerbsplans anzeigen

Für einige VM-Images im Azure Marketplace gelten zusätzliche Lizenz- und Kaufbedingungen, die Sie akzeptieren müssen, bevor Sie sie programmgesteuert bereitstellen können. Sie müssen die Imagebedingungen einmal pro Abonnement akzeptieren.

Um die Kaufplaninformationen eines Images anzuzeigen, führen Sie das `Get-AzVMImage`-Cmdlet aus. Wenn die `PurchasePlan`-Eigenschaft in der Ausgabe nicht `null` ist, müssen Sie vor der programmgesteuerten Bereitstellung Bedingungen des Images akzeptieren.  

Für das Image *Windows Server 2016 Datacenter* gelten z.B. keine weiteren Bedingungen, daher lautet die `PurchasePlan`-Angabe `null`:

```powershell
$version = "2016.127.20170406"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName -Version $version
```

Die Ausgabe ist mit folgender Zeichenfolge vergleichbar:

```output
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2019.0.20190115
Location         : westus
PublisherName    : MicrosoftWindowsServer
Offer            : WindowsServer
Skus             : 2019-Datacenter
Version          : 2019.0.20190115
FilterExpression :
Name             : 2019.0.20190115
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : null
DataDiskImages   : []

```

Im Beispiel unten wird ein ähnlicher Befehl für das Image *Data Science Virtual Machine – Windows 2016* angezeigt. Dieses weist folgenden `PurchasePlan`-Eigenschaften auf: `name`, `product` und `publisher`. Einige Images weisen auch eine `promotion code`-Eigenschaft auf. Sehen Sie sich zum Bereitstellen dieses Images die folgenden Abschnitte an, um die Bedingungen zu akzeptieren und die programmgesteuerte Bereitstellung zu ermöglichen.

```powershell
Get-AzVMImage -Location "westus" -PublisherName "microsoft-ads" -Offer "windows-data-science-vm" -Skus "windows2016" -Version "0.2.02"
```

Die Ausgabe ist mit folgender Zeichenfolge vergleichbar:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/microsoft-ads/ArtifactTypes/VMImage/Offers/windows-data-science-vm/Skus/windows2016/Versions/19.01.14
Location         : westus
PublisherName    : microsoft-ads
Offer            : windows-data-science-vm
Skus             : windows2016
Version          : 19.01.14
FilterExpression :
Name             : 19.01.14
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : {
                     "publisher": "microsoft-ads",
                     "name": "windows2016",
                     "product": "windows-data-science-vm"
                   }
DataDiskImages   : []

```

Verwenden Sie zum Anzeigen der Lizenzbedingungen das Cmdlet [Get-AzMarketplaceterms](/powershell/module/az.marketplaceordering/get-azmarketplaceterms), und übergeben Sie die Kaufplanparameter. Die Ausgabe enthält einen Link zu den Bedingungen für das Marketplace-Image und zeigt an, ob Sie zuvor die Geschäftsbedingungen akzeptiert haben. Verwenden Sie in den Parameterwerten ausschließlich Kleinbuchstaben.

```powershell
Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"
```

Die Ausgabe ist mit folgender Zeichenfolge vergleichbar:

```output
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DVM%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : 2UMWH6PHSAIM4U22HXPXW25AL2NHUJ7Y7GRV27EBL6SUIDURGMYG6IIDO3P47FFIBBDFHZHSQTR7PNK6VIIRYJRQ3WXSE6BTNUNENXA
Accepted          : False
Signdate          : 1/25/2019 7:43:00 PM
```

## <a name="accept-purchase-plan-terms"></a>Geschäftsbedingungen des Erwerbsplans akzeptieren

Verwenden Sie das Cmdlet [Set-AzMarketplaceterms](/powershell/module/az.marketplaceordering/set-azmarketplaceterms) zum Annehmen oder Ablehnen der Bedingungen. Sie müssen für das Image die Bedingungen nur einmal pro Abonnement akzeptieren. Verwenden Sie in den Parameterwerten ausschließlich Kleinbuchstaben. 

```powershell
$agreementTerms=Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"

Set-AzMarketplaceTerms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016" -Terms $agreementTerms -Accept
```



```output
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DV
                    M%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : XXXXXXK3MNJ5SROEG2BYDA2YGECU33GXTD3UFPLPC4BAVKAUL3PDYL3KBKBLG4ZCDJZVNSA7KJWTGMDSYDD6KRLV3LV274DLBXXXXXX
Accepted          : True
Signdate          : 2/23/2018 7:49:31 PM
```


## <a name="create-a-new-vm-from-a-marketplace-image"></a>Erstellen einer neuen VM aus einem Marketplace-Image

Wenn Sie die Informationen zu dem Image, das Sie verwenden möchten, bereits haben, können Sie diese an das Cmdlet [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage) übergeben, um der VM-Konfiguration die Imageinformationen hinzuzufügen. In den nächsten Abschnitten finden Sie Informationen zum Suchen und Auflisten der im Marketplace verfügbaren Images.

Für einige kostenpflichtige Images ist es außerdem erforderlich, mithilfe von [Set-AzVMPlan](/powershell/module/az.compute/set-azvmplan) Informationen zum Erwerbsplan anzugeben. 

```powershell
...

$vmConfig = New-AzVMConfig -VMName "myVM" -VMSize Standard_D1

# Set the Marketplace image
$offerName = "windows-data-science-vm"
$skuName = "windows2016"
$version = "19.01.14"
$vmConfig = Set-AzVMSourceImage -VM $vmConfig -PublisherName $publisherName -Offer $offerName -Skus $skuName -Version $version

# Set the Marketplace plan information, if needed
$publisherName = "microsoft-ads"
$productName = "windows-data-science-vm"
$planName = "windows2016"
$vmConfig = Set-AzVMPlan -VM $vmConfig -Publisher $publisherName -Product $productName -Name $planName

...
```

Anschließend übergeben Sie die VM-Konfiguration zusammen mit anderen Konfigurationsobjekten an das Cmdlet `New-AzVM`. Ein ausführliches Beispiel für die Verwendung einer VM-Konfiguration mit PowerShell finden Sie in diesem [Skript](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine/create-vm-detailed/create-windows-vm-detailed.ps1).

Wenn Sie in einer Meldung aufgefordert werden, die Nutzungsbedingungen für das Image zu akzeptieren, finden Sie im vorherigen Abschnitt zum Thema [Geschäftsbedingungen des Erwerbsplans](#accept-purchase-plan-terms) weitere Informationen.

## <a name="create-a-new-vm-from-a-vhd-with-purchase-plan-information"></a>Erstellen einer neuen VM von einer vorhandenen VHD mit Erwerbsplaninformationen

Wenn eine Ihrer vorhandenen virtuellen Festplatten mit einem Azure Marketplace-Image erstellt wurde, müssen Sie beim Erstellen einer neuen VM von dieser VHD möglicherweise die Informationen zu Ihrem Erwerbsplan angeben.

Wenn Sie noch über die ursprüngliche VM oder eine andere VM verfügen, die mit demselben Image erstellt wurde, können Sie den Plannamen, den Herausgeber und die Produktinformationen mithilfe von Get-AzVM abrufen. Dieses Beispiel ruft eine VM namens *myVM* in der Ressourcengruppe *myResourceGroup* ab und zeigt dann die Erwerbsplaninformationen an.

```azurepowershell-interactive
$vm = Get-azvm `
   -ResourceGroupName myResourceGroup `
   -Name myVM
$vm.Plan
```

Wenn Sie die Planinformationen nicht vor dem Löschen der ursprünglichen VM abgerufen haben, können Sie eine [Supportanfrage](https://ms.portal.azure.com/#create/Microsoft.Support) erstellen. Sie benötigen dazu den VM-Namen, die Abonnement-ID und den Zeitstempel des Löschvorgangs.

Informationen zum Erstellen einer VM mit einer VHD finden Sie im Artikel [Erstellen eines virtuellen Computers aus einer speziellen VHD](create-vm-specialized.md). Fügen Sie beim Erstellen der VM-Konfiguration eine Zeile mit den Planinformationen mithilfe von [Set-AzVMPlan](/powershell/module/az.compute/set-azvmplan) hinzu, wie im Folgenden gezeigt:

```azurepowershell-interactive
$vmConfig = Set-AzVMPlan `
   -VM $vmConfig `
   -Publisher "publisherName" `
   -Product "productName" `
   -Name "planName"
```


## <a name="next-steps"></a>Nächste Schritte

Um mit den grundlegenden Imageinformationen schnell mit dem Cmdlet `New-AzVM` einen virtuellen Computer zu erstellen, lesen Sie [Erstellen eines virtuellen Windows-Computers mit PowerShell](quick-create-powershell.md).

Weitere Informationen zur Verwendung von Azure Marketplace-Images zum Erstellen benutzerdefinierter Images in einer Azure Compute Gallery-Instanz (ehemals Shared Image Gallery) finden Sie unter [Bereitstellen von Azure Marketplace-Erwerbsplaninformationen beim Erstellen von Images](../marketplace-images.md).
