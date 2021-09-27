---
title: Erstellen einer Shared Image Gallery-Instanz
description: Erfahren Sie, wie Sie eine Shared Image Gallery-Instanz in Azure erstellen.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/15/2021
ms.author: cynthn
ms.custom: template-how-to
ms.openlocfilehash: c7462886c2f85e07f060caa7a11a39a782e38cea
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123452480"
---
# <a name="create-a-gallery-for-storing-and-sharing-images"></a>Erstellen eines Katalogs zum Speichern und Freigeben von Images


Der [Katalog mit freigegebenen Images](./shared-image-galleries.md) vereinfacht das Freigeben benutzerdefinierter Images in Ihrer Organisation. Benutzerdefinierte Images sind wie Marketplace-Images, Sie erstellen sie jedoch selbst. Benutzerdefinierte Images können zum Laden von Bereitstellungsaufgaben verwendet werden, z.B. zum Vorabladen von Anwendungen sowie für Anwendungskonfigurationen und andere Betriebssystemkonfigurationen. 

Der Katalog mit geteilten Images gestattet es Ihnen, Ihre benutzerdefinierten VM-Images mit anderen Personen in Ihrer Organisation, innerhalb einer oder zwischen Regionen, innerhalb eines AAD-Mandanten zu teilen. Wählen Sie aus, welche Images Sie teilen möchten, in welchen Regionen Sie sie verfügbar machen möchten, und mit wem Sie sie teilen möchten. Sie können mehrere Kataloge erstellen, damit Sie geteilte Images logisch gruppieren können. 

Der Katalog ist eine Ressource der obersten Ebene, die vollständige rollenbasierte Zugriffssteuerung in Azure (RBAC) bereitstellt. Images bieten Versionsverwaltung, und Sie können sich entschließen, jede Imageversion in eine andere Gruppe von Azure-Regionen zu replizieren. Der Katalog funktioniert nur mit verwalteten Images.

Die Funktion „Katalog mit freigegebenen Images“ verfügt über mehrere Ressourcentypen. 

[!INCLUDE [virtual-machines-shared-image-gallery-resources](./includes/virtual-machines-shared-image-gallery-resources.md)]

## <a name="create-a-gallery"></a>Erstellen eines Katalogs

Ein Imagekatalog ist die primäre Ressource, die zur Ermöglichung des Teilens von Images verwendet wird. Zulässige Zeichen für Katalognamen sind Groß- und Kleinbuchstaben, Zahlen und Punkte. Der Katalogname darf keine Bindestriche enthalten. Katalognamen müssen innerhalb Ihres Abonnements eindeutig sein. 

Wählen Sie unten eine Option zum Erstellen Ihres Katalogs aus:

### <a name="portal"></a>[Portal](#tab/portal)

Im folgenden Beispiel wird der Katalog *myGallery* in der Ressourcengruppe *myGalleryRG* erstellt.

1. Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.
1. Geben Sie in das Suchfeld **Katalog mit freigegebenen Images** ein und wählen Sie in den Ergebnissen **Katalog mit freigegebenen Images** aus.
1. Klicken Sie auf der Seite **Katalog der freigegebenen Images** auf **Hinzufügen**.
1. Wählen Sie auf der Seite **Katalog mit freigegebenen Images erstellen** das richtige Abonnement aus.
1. Wählen Sie in **Ressourcengruppe** **Neu erstellen** aus, und geben Sie *myGalleryRG* für den Namen ein.
1. Geben Sie unter **Name** *myGallery* als Namen des Katalogs ein.
1. Übernehmen Sie den Standardwert für **Region**.
1. Sie können eine kurze Beschreibung des Katalogs eingeben, wie *Mein Imagekatalog zum Testen*. Klicken Sie dann auf **Überprüfen + Erstellen**.
1. Wenn die Überprüfung erfolgreich war, wählen Sie **Erstellen** aus.
1. Wählen Sie nach Abschluss der Bereitstellung die Option **Zu Ressourcengruppe wechseln**.

### <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli)

Erstellen Sie einen Imagekatalog mit [az sig create](/cli/azure/sig#az_sig_create). Im folgenden Beispiel werden eine Ressourcengruppe namens *myGalleryRG* in der Region *USA, Osten* und ein Katalog namens *myGallery* erstellt.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)

Erstellen Sie mit [New-AzGallery](/powershell/module/az.compute/new-azgallery) einen Imagekatalog. Im folgenden Beispiel wird der Katalog *myGallery* in der Ressourcengruppe *myGalleryRG* erstellt.

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'West Central US'  
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
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
    "description": "Shared Image Gallery for my organization"
  },
  "location": "eastus",
}
```
---

## <a name="next-steps"></a>Nächste Schritte

Erstellen Sie eine [Imagedefinition und eine Imageversion](image-version.md).

[Azure Image Builder (Vorschauversion)](./image-builder-overview.md) hilft beim Automatisieren der Erstellung von Imageversionen. Sie können den Dienst sogar zum Aktualisieren und [Erstellen einer neuen Imageversion aus einer vorhandenen](./windows/image-builder-gallery-update-image-version.md) verwenden. 

Sie können auch mithilfe von Vorlagen eine Ressource im Katalog für freigegebene Images erstellen. Es stehen mehrere Azure-Schnellstartvorlagen zur Verfügung: 

- [Erstellen eines Katalogs mit freigegebenen Images](https://azure.microsoft.com/resources/templates/sig-create/)
- [Erstellen einer Imagedefinition in einem Katalog mit freigegebenen Images](https://azure.microsoft.com/resources/templates/sig-image-definition-create/)
- [Erstellen einer Imageversion in einem Katalog mit freigegebenen Images](https://azure.microsoft.com/resources/templates/sig-image-version-create/)