---
title: Erstellen und Bereitstellen von VM-Anwendungspaketen (Vorschau)
description: Erfahren Sie, wie Sie VM-Anwendungen mithilfe von Azure Compute Gallery erstellen und bereitstellen.
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 11/02/2021
ms.reviewer: amjads
ms.custom: ''
ms.openlocfilehash: f5978c40acdbcc842400224fb3cdaed7c49255da
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478879"
---
# <a name="create-and-deploy-vm-applications-preview"></a>Erstellen und Bereitstellen von VM-Anwendungen (Vorschau)

VM-Anwendungen sind ein Ressourcentyp in Azure Compute Gallery (ehemals Shared Image Gallery), der die Verwaltung, Freigabe und globale Verteilung von Anwendungen für Ihre VMs vereinfacht.


> [!IMPORTANT]
> **VM-Anwendungen in Azure Compute Gallery** befinden sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="prerequisites"></a>Voraussetzungen

Vergewissern Sie sich vorab, ob Folgendes vorhanden ist:


In diesem Artikel wird davon ausgegangen, dass Sie bereits über eine Azure Compute Gallery-Instanz verfügen. Wenn Sie noch nicht über einen Katalog verfügen, erstellen Sie zuerst einen. Weitere Informationen finden Sie unter [Erstellen eines Katalogs zum Speichern und Freigeben von Ressourcen](create-gallery.md).

Sie sollten Ihre Anwendung in einen Container in einem Azure Storage-Konto hochgeladen haben. Ihre Anwendung kann in einem Block- oder Seitenblob gespeichert werden. Wenn Sie sich für ein Seitenblob entscheiden, müssen Sie vor dem Hochladen für die Dateien eine Byteausrichtung durchführen. Im Folgenden finden Sie ein Beispiel für die Byteausrichtung Ihrer Datei:

```azurepowershell-interactive
$inputFile = <the file you want to pad>

$fileInfo = Get-Item -Path $inputFile

$remainder = $fileInfo.Length % 512

if ($remainder -ne 0){

    $difference = 512 - $remainder

    $bytesToPad = [System.Byte[]]::CreateInstance([System.Byte], $difference)

    Add-Content -Path $inputFile -Value $bytesToPad -Encoding Byte
    }
```

Sie müssen sicherstellen, dass die Dateien öffentlich verfügbar sind. Andernfalls benötigen Sie den SAS-URI für die Dateien in Ihrem Speicherkonto. Sie können [Storage-Explorer](/azure/vs-azure-tools-storage-explorer-blobs) verwenden, um schnell einen SAS-URI zu erstellen, falls Sie noch keinen besitzen.

## <a name="create-the-vm-application"></a>Erstellen der VM-Anwendung

Wählen Sie unten eine Option für das Erstellen Ihrer VM-Anwendungsdefinition und -version aus:

### <a name="portal"></a>[Portal](#tab/portal)


1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com), suchen Sie nach **Azure Compute Gallery**, und wählen Sie diese Option aus.
1. Wählen Sie den gewünschten Katalog aus der Liste aus.
1. Wählen Sie oben auf der Seite Ihres Katalogs **Hinzufügen** und dann im Dropdownmenü **VM-Imagedefinition** aus. Die Seite **Erstellen einer VM-Anwendungsdefinition** wird geöffnet.
1. Geben Sie auf der Registerkarte **Grundlagen** einen Namen für Ihre Anwendung ein, und wählen Sie aus, ob die Anwendung für VMs mit Linux oder Windows vorgesehen ist.
1. Wählen Sie die Registerkarte **Veröffentlichungsoptionen** aus, wenn Sie eine der folgenden optionalen Einstellungen für die VM-Anwendungsdefinition angeben möchten:
    - Eine Beschreibung der VM-Anwendungsdefinition
    - Datum für Ende des Lebenszyklus
    - Link zu einer Lizenzvereinbarung
    - URI zu Datenschutzbestimmungen
    - URI zu Versionshinweisen
1. Wählen Sie abschließend **Überprüfen + Erstellen** aus.
1. Wählen Sie nach Abschluss der Validierung **Erstellen** aus, um die Definition bereitzustellen.
1. Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.


Nun können Sie eine VM erstellen und die VM-Anwendung über das Portal darauf bereitstellen. Erstellen Sie die VM einfach wie gewohnt, und wählen Sie auf der Registerkarte **Erweitert** die Option **Select a VM application to install** (VM-Anwendung für Installation auswählen).

:::image type="content" source="media/vmapps/advanced-tab.png" alt-text="Screenshot der Registerkarte „Erweitert“ für die Installation der VM-Anwendung":::

Wählen Sie die VM-Anwendung in der Liste aus, und wählen Sie dann unten auf der Seite **Speichern** aus.

:::image type="content" source="media/vmapps/select-app.png" alt-text="Screenshot der Auswahl einer VM-Anwendung für die Installation auf der VM":::

Wenn Sie mehrere VM-Anwendungen installieren möchten, können Sie auf der Registerkarte **Erweitert** die Installationsreihenfolge für jede VM-Anwendung festlegen.

### <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli)

Für VM-Anwendungen ist mindestens Version 2.30.0 der [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli) erforderlich.

Erstellen Sie die VM-Anwendungsdefinition mit [az sig gallery-application create](/cli/azure/sig/gallery-application#az_sig_gallery_application_create). In diesem Beispiel erstellen Sie eine VM-Anwendungsdefinition namens *myApp* für Linux-VMs.

```azurecli-interactive
az sig gallery-application create \
    --application-name myApp \
    --gallery-name myGallery \
    --resource-group myResourceGroup \
    --os-type Linux \
    --location "East US"
```

Erstellen Sie die VM-Anwendungsversion mit [az sig gallery-application version create](/cli/azure/sig/gallery-application/version#az_sig_gallery_application_version_create). Zulässige Zeichen für die Version sind Zahlen und Punkte. Zahlen müssen im Bereich einer ganzen 32-Bit-Zahl liegen. Format: *Hauptversion*.*Nebenversion*.*Patch*.

Ersetzen Sie die Werte der Parameter durch Ihre eigenen.

```azurecli-interactive
az sig gallery-application version create \
   --version-name 1.0.0 \
   --application-name myApp \
   --gallery-name myGallery \
   --location "East US" \
   --resource-group myResourceGroup \
   --package-file-link "https://<storage account name>.blob.core.windows.net/<container name>/<filename>" \
   --install-command "mv myApp .\myApp\myApp" \
   --remove-command "rm .\myApp\myApp" \
   --update-command  "mv myApp .\myApp\myApp \
   --default-configuration-file-link "https://<storage account name>.blob.core.windows.net/<container name>/<filename>"\
```


### <a name="powershell"></a>[PowerShell](#tab/powershell)

Erstellen Sie die VM-Anwendungsdefinition mit `New-AzGalleryApplication`. In diesem Beispiel erstellen Sie eine Linux-App namens *myApp* in der Azure Compute Gallery-Instanz *myGallery* in der Ressourcengruppe *myGallery*. Die Kurzbeschreibung dient lediglich der eigenen Verwendung. Ersetzen Sie die Werte nach Bedarf.

```azurepowershell-interactive
$galleryName = myGallery
$rgName = myResourceGroup
$applicationName = myApp
New-AzGalleryApplication `
  -ResourceGroupName $rgName `
  -GalleryName $galleryName `
  -Name $applicationName `
  -SupportedOSType Linux `
  -Description "Backend Linux application for finance."
```

Erstellen Sie mithilfe von `New-AzGalleryApplicationVersion` eine Version Ihrer Anwendung. Zulässige Zeichen für die Version sind Zahlen und Punkte. Zahlen müssen im Bereich einer ganzen 32-Bit-Zahl liegen. Format: *Hauptversion*.*Nebenversion*.*Patch*.

In diesem Beispiel erstellen Sie die Versionsnummer *1.0.0*. Ersetzen Sie die Werte der Variablen nach Bedarf.

```azurepowershell-interactive
$version = 1.0.0
New-AzGalleryApplicationVersion `
   -ResourceGroupName $rgName `
   -GalleryName $galleryName `
   -GalleryApplicationName $applicationName `
   -Name $version `
   -PackageFileLink "https://<storage account name>.blob.core.windows.net/<containder name>/<filename>" `
   -Location "East US" `
   -Install myApp.exe /silent `
   -Remove myApp.exe /uninstall `
```


Um die Anwendung auf einer vorhandenen VM hinzuzufügen, rufen Sie die Anwendungsversion ab, und verwenden diese, um die ID der VM-Anwendungsversion abzurufen. Mithilfe diese ID fügen Sie die Anwendung der VM-Konfiguration hinzu.

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName $rgname -Name myVM
$vmapp = Get-AzGalleryApplicationVersion `
   -ResourceGroupName $rgname `
   -GalleryName $galleryname `
   -ApplicationName $applicationname `
   -Version $version

$vm = Add-AzVmGalleryApplication `
   -VM $vm `
   -Id $vmapp.Id

Update-AzVm -ResourceGroupName $rgname -VM $vm
```


### <a name="rest"></a>[REST](#tab/rest2)

Erstellen Sie die Anwendungsdefinition.


```rest
PUT
/subscriptions/\<**subscriptionId**\>/resourceGroups/\<**resourceGroupName**\>/providers/Microsoft.Compute/galleries/\<**galleryName**\>/applications/\<**applicationName**\>?api-version=2019-03-01

{
    "location": "West US",
    "name": "myApp",
    "properties": {
        "supportedOSType": "Windows | Linux",
        "endOfLifeDate": "2020-01-01"
    }
}

```

| Feldname | BESCHREIBUNG | Einschränkungen |
|--|--|--|
| name | Ein eindeutiger Name für die VM-Anwendung im Katalog | Die maximale Länge beträgt 117 Zeichen. Zulässige Zeichen sind Groß- und Kleinbuchstaben, Ziffern, Bindestrich (-), Punkt (.) und Unterstrich (_). Namen dürfen nicht mit einem Punkt (.) enden. |
| supportedOSType | Gibt an, ob es sich um eine Windows- oder Linux-Anwendung handelt | „Windows“ oder „Linux“ |
| endOfLifeDate | Ein in der Zukunft liegendes Datum für das Ende der Lebensdauer der Anwendung. Beachten Sie, dass dies nur als Referenz dient und nicht erzwungen wird. | Gültiges Datum in der Zukunft |

Erstellen Sie eine VM-Anwendungsversion.

```rest
PUT
/subscriptions/\<**subscriptionId**\>/resourceGroups/\<**resourceGroupName**\>/providers/Microsoft.Compute/galleries/\<**galleryName**\>/applications/\<**applicationName**\>/versions/\<**versionName**\>?api-version=2019-03-01

{
  "location": "$location",
  "properties": {
    "publishingProfile": {
      "source": {
        "mediaLink": "$mediaLink",
        "defaultConfigurationLink": "$configLink"
      },
      "manageActions": {
        "install": "echo installed",
        "remove": "echo removed",
        "update": "echo update"
      },
      "targetRegions": [
        {
          "name": "$location1",
          "regionalReplicaCount": 1 
        },
        { "name": "$location1" }
      ]
    },
    "endofLifeDate": "datetime",
    "excludeFromLatest": "true | false"
  }
}

```

| Feldname | BESCHREIBUNG | Einschränkungen |
|--|--|--|
| location | Quellspeicherort für die VM-Anwendungsversion | Gültige Azure-Region |
| mediaLink | Die URL mit dem Anwendungsversionspaket | Gültige und vorhandene Speicher-URL |
| defaultConfigurationLink | Optional. URL mit der Standardkonfiguration, die zum Zeitpunkt der Bereitstellung überschrieben werden kann | Gültige und vorhandene Speicher-URL |
| Installieren | Befehl zum Installieren der Anwendung | Gültiger Befehl für das angegebene Betriebssystem |
| Entfernen | Befehl zum Entfernen der Anwendung | Gültiger Befehl für das angegebene Betriebssystem |
| Aktualisieren | Optional. Befehl zum Aktualisieren der Anwendung. Wenn keine Angabe erfolgt und Update erforderlich ist, wird die alte Version entfernt und die neue Version installiert. | Gültiger Befehl für das angegebene Betriebssystem |
| targetRegions/name | Name einer Region, in die repliziert werden soll | Gültige Azure-Region |
| targetRegions/regionalReplicaCount | Optional. Anzahl der Replikate in der zu erstellenden Region. Der Standardwert lautet 1. | Ganze Zahl von 1 bis 3 |
| endOfLifeDate | Ein in der Zukunft liegendes Datum für die Anwendungsversion. Beachten Sie, dass dies nur als Referenz für die Kundschaft dient und nicht erzwungen wird. | Gültiges Datum in der Zukunft |
| excludeFromLatest | Bei einer Angabe wird diese Version nicht als neueste Version berücksichtigt. | „true“ oder „false“ |




Um einer VM eine VM-Anwendungsversion hinzuzufügen, führen Sie einen PUT-Befehl auf der VM aus.

```rest
PUT
/subscriptions/\<**subscriptionId**\>/resourceGroups/\<**resourceGroupName**\>/providers/Microsoft.Compute/virtualMachines/\<**VMName**\>?api-version=2019-03-01

{
  "properties": {
    "applicationProfile": {
      "galleryApplications": [
        {
          "order": 1,
          "packageReferenceId": "/subscriptions/{subscriptionId}/resourceGroups/<resource group>/providers/Microsoft.Compute/galleries/{gallery name}/applications/{application name}/versions/{version}",
          "configurationReference": "{path to configuration storage blob}"
        }
      ]
    }
  },
  "name": "{vm name}",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resource group}/providers/Microsoft.Compute/virtualMachines/{vm name}",
  "location": "{vm location}"
}
```


So wenden Sie die VM-Anwendung auf eine einheitliche Skalierungsgruppe an

```rest
PUT
/subscriptions/\<**subscriptionId**\>/resourceGroups/\<**resourceGroupName**\>/providers/Microsoft.Compute/
virtualMachineScaleSets/\<**VMSSName**\>?api-version=2019-03-01

{
  "properties": {
    "virtualMachineProfile": {
      "applicationProfile": {
        "galleryApplications": [
          {
            "order": 1,
            "packageReferenceId": "/subscriptions/{subscriptionId}/resourceGroups/<resource group>/providers/Microsoft.Compute/galleries/{gallery name}/applications/{application name}/versions/{version}",
            "configurationReference": "{path to configuration storage blob}"
          }
        ]
      }
    }
  },
  "name": "{vm name}",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resource group}/providers/Microsoft.Compute/virtualMachines/{vm name}",
  "location": "{vm location}"
}
```


| Feldname | BESCHREIBUNG | Einschränkungen |
|--|--|--|
| order | Optional. Reihenfolge, in der die Anwendungen bereitgestellt werden sollen. Siehe unten. | Gültige ganze Zahl |
| packageReferenceId | Verweis auf die Kataloganwendungsversion | Gültiger Verweis auf die Anwendungsversion |
| configurationReference | Optional. Vollständige URL eines Speicherblobs, das die Konfiguration für diese Bereitstellung enthält. Dadurch werden alle zuvor für defaultConfiguration angegebenen Werte überschrieben. | Gültiger Verweis auf ein Speicherblob |

Mithilfe des Felds „order“ können Sie Abhängigkeiten zwischen Anwendungen angeben. Folgende Regeln gelten für die Reihenfolge:

| Fall | Bedeutung für die Installation | Auswirkungen bei Fehlern |
|--|--|--|
| Keine Reihenfolge angegeben | Anwendungen ohne festgelegte Reihenfolge werden nach sortierten Anwendungen installiert. Es gibt keine Garantie für die Installationsreihenfolge der unsortierten Anwendungen. | Installationsfehler anderer Anwendungen (sortiert oder unsortiert) wirken sich nicht auf die Installation unsortierter Anwendungen aus. |
| Doppelte Reihenfolgewerte | Die Anwendung wird im Vergleich zu anderen Anwendungen derselben Sortierung in beliebiger Reihenfolge installiert. Alle Anwendungen mit derselben Reihenfolge werden nach Anwendungen mit niedrigeren Reihenfolgewerten und vor denen mit höheren Reihenfolgewerten installiert. | Wenn eine vorherige Anwendung mit einem niedrigeren Reihenfolgewert nicht installiert werden konnte, werden keine Anwendungen mit diesem Reihenfolgewert installiert. Wenn Anwendungen mit demselben Reihenfolgewert nicht installiert werden können, werden keine Anwendungen mit einem höheren Reihenfolgewert installiert. |
| Aufsteigende Reihenfolge | Anwendungen werden nach Anwendungen mit niedrigeren Reihenfolgewerten und vor denen mit höheren Reihenfolgewerten installiert. | Wenn eine vorherige Anwendung mit einem niedrigeren Reihenfolgewert nicht installiert werden konnte, wird diese Anwendung nicht installiert. Wenn diese Anwendung nicht installiert werden kann, wird keine Anwendung mit einem höheren Reihenfolgewert installiert. |

Die Antwort enthält das vollständige VM-Modell. Im Folgenden finden Sie die relevanten Teile.

```rest
{
  "name": "{vm name}",
  "id": "{vm id}",
  "type": "Microsoft.Compute/virtualMachines",
  "location": "{vm location}",
  "properties": {
    "applicationProfile": {
      "galleryApplications": ""
    },
    "provisioningState": "Updating"
  },
  "resources": [
    {
      "name": "VMAppExtension",
      "id": "{extension id}",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "centraluseuap",
      "properties": "@{autoUpgradeMinorVersion=True; forceUpdateTag=7c4223fc-f4ea-4179-ada8-c8a85a1399f5; provisioningState=Creating; publisher=Microsoft.CPlat.Core; type=VMApplicationManagerLinux; typeHandlerVersion=1.0; settings=}"
    }
  ]
}

```

Wenn die VM-Anwendungen noch nicht auf der VM installiert wurden, ist der Wert leer. 

---



## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu [VM-Anwendungen](vm-applications.md).
