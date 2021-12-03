---
title: Erstellen einer Azure Compute Gallery-Instanz zum Freigeben von Ressourcen
description: Erfahren Sie, wie Sie eine Azure Compute Gallery-Instanz erstellen
author: cynthn
ms.service: virtual-machines
ms.subservice: gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/05/2021
ms.author: cynthn
ms.custom: template-how-to
ms.openlocfilehash: 924cce1b23cf86a6e29ef4fd28f862bf2fa12363
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131459689"
---
# <a name="create-a-gallery-for-storing-and-sharing-resources"></a>Erstellen eines Katalogs zum Speichern und Freigeben von Ressourcen


Eine [Azure Compute Gallery](./shared-image-galleries.md)-Instanz (früher als Shared Image Gallery bezeichnet) vereinfacht die gemeinsame Nutzung von Ressourcen wie Images und Anwendungspaketen in Ihrer Organisation.  

Azure Compute Gallery gestattet es Ihnen, Ihre benutzerdefinierten VM-Images mit anderen Personen in Ihrer Organisation, innerhalb einer oder zwischen Regionen, innerhalb eines AAD-Mandanten zu teilen. Wählen Sie aus, was Sie teilen möchten, in welchen Regionen Sie es verfügbar machen möchten, und mit wem Sie es teilen möchten. Sie können mehrere Kataloge erstellen, damit Sie Ressourcen logisch gruppieren können. 

Der Katalog ist eine Ressource der obersten Ebene, die vollständige rollenbasierte Zugriffssteuerung in Azure (RBAC) bereitstellt. 

## <a name="create-a-gallery"></a>Erstellen eines Katalogs

Zulässige Zeichen für Katalognamen sind Groß- und Kleinbuchstaben, Zahlen und Punkte. Der Katalogname darf keine Bindestriche enthalten. Katalognamen müssen innerhalb Ihres Abonnements eindeutig sein. 

Wählen Sie unten eine Option zum Erstellen Ihres Katalogs aus:

### <a name="portal"></a>[Portal](#tab/portal)

Im folgenden Beispiel wird der Katalog *myGallery* in der Ressourcengruppe *myGalleryRG* erstellt.

1. Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.
1. Geben Sie im Suchfeld **Azure Compute Gallery** ein, und wählen Sie in den Ergebnissen **Azure Compute Gallery** aus.
1. Klicken Sie auf der Seite **Azure Compute Gallery** auf **Hinzufügen**.
1. Wählen Sie auf der Seite **Azure Compute Gallery-Instanz erstellen** das richtige Abonnement aus.
1. Wählen Sie in **Ressourcengruppe** **Neu erstellen** aus, und geben Sie *myGalleryRG* für den Namen ein.
1. Geben Sie unter **Name** *myGallery* als Namen des Katalogs ein.
1. Übernehmen Sie den Standardwert für **Region**.
1. Sie können eine kurze Beschreibung des Katalogs eingeben, wie *Mein Katalog zum Testen*. Klicken Sie dann auf **Überprüfen + Erstellen**.
1. Wenn die Überprüfung erfolgreich war, wählen Sie **Erstellen** aus.
1. Wählen Sie nach Abschluss der Bereitstellung die Option **Zu Ressourcengruppe wechseln**.

### <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli)

Erstellen Sie einen Katalog mit [az sig create](/cli/azure/sig#az_sig_create). Im folgenden Beispiel werden eine Ressourcengruppe namens *myGalleryRG* in der Region *USA, Osten* und ein Katalog namens *myGallery* erstellt.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)

Erstellen Sie mit [New-AzGallery](/powershell/module/az.compute/new-azgallery) einen Katalog. Im folgenden Beispiel wird der Katalog *myGallery* in der Ressourcengruppe *myGalleryRG* erstellt.

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'West Central US'  
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Azure Compute Gallery for my organization' 
```


### <a name="rest"></a>[REST](#tab/rest)

Verwenden Sie die [REST-API](/rest/api/resources/resource-groups/create-or-update), um eine Ressourcengruppe zu erstellen.

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}?api-version=2021-04-01

{
  "location": "eastus"
}
```

Verwenden Sie die [REST-API](/rest/api/compute/galleries/create-or-update), um einen Katalog zu erstellen.

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/galleries/{galleryName}?api-version=2019-12-01

{
  "properties": {
    "description": "Azure Compute Gallery for my organization"
  },
  "location": "eastus",
}
```
---

## <a name="next-steps"></a>Nächste Schritte

- Erstellen Sie eine [Imagedefinition und eine Imageversion](image-version.md).

- [Erstellen Sie eine VM-Anwendung](vm-applications-how-to.md) in Ihrem Katalog.

- Sie können auch einen [Azure Compute Gallery](https://azure.microsoft.com/resources/templates/sig-create/) mithilfe einer Vorlage erstellen.

- [Azure Image Builder](./image-builder-overview.md) hilft beim Automatisieren der Erstellung von Imageversionen. Sie können den Dienst sogar zum Aktualisieren und [Erstellen einer neuen Imageversion aus einer vorhandenen](./windows/image-builder-gallery-update-image-version.md) verwenden. 
