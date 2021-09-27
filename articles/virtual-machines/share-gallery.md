---
title: Freigabe einer Galerie mit RBAC
description: Erfahren Sie, wie Sie eine Galerie mithilfe der rollenbasierten Zugriffskontrolle (RBAC) freigeben können.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 08/31/2021
ms.author: cynthn
ms.custom: template-how-to
ms.openlocfilehash: 01c98ba8d619573c241e5a8fdfa2f774b4aed22d
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123452517"
---
# <a name="use-rbac-to-share-image-gallery-resources"></a>RBAC zur Freigabe von Bildgalerieressourcen verwenden

Da es sich bei der Shared Image Gallery, der Bilddefinition und der Bildversion um Ressourcen handelt, können sie mit den integrierten Azure RBAC-Steuerelementen freigegeben werden. Mithilfe von Azure RBAC können Sie diese Ressourcen für andere Benutzer, Dienstprinzipale und Gruppen freigeben. Sie können sogar Zugriff für Personen freigeben, die sich außerhalb des Mandanten befinden, in dem sie erstellt wurden. Sobald ein Benutzer Zugriff auf die Version eines freigegebenen Images hat, kann er einen virtuellen Computer oder eine VM-Skalierungsgruppe bereitstellen.  

Zur Erzielung der besten Leistung empfiehlt sich ein Freigeben auf Katalogebene. Wir empfehlen nicht, einzelnen Imageversionen freizugeben. Weitere Informationen zu Azure RBAC finden Sie unter [Zuweisen von Azure-Rollen](../role-based-access-control/role-assignments-portal.md).

Wenn sich der Benutzer außerhalb Ihrer Organisation befindet, erhält er eine E-Mail-Einladung, um der Organisation beizutreten. Der Benutzer muss die Einladung annehmen, woraufhin er in der Lage ist, den Katalog und alle Imagedefinitionen und -versionen in seiner Liste der Ressourcen anzuzeigen.

## <a name="share-a-gallery"></a>Freigeben eines Katalogs

### <a name="portal"></a>[Portal](#tab/portal)

Wenn sich der Benutzer außerhalb Ihrer Organisation befindet, erhält er eine E-Mail-Einladung, um der Organisation beizutreten. Der Benutzer muss die Einladung annehmen, woraufhin er in der Lage ist, den Katalog und alle Imagedefinitionen und -versionen in seiner Liste der Ressourcen anzuzeigen.

1. Wählen Sie auf der Seite für Ihre Bildergalerie im Menü auf der linken Seite die Option **Zugangskontrolle (IAM)** . 
1. Wählen Sie unter **Rollenzuweisung hinzufügen** die Option **Hinzufügen** aus. Der Bereich **Rollenzuweisung hinzufügen** wird geöffnet. 
1. Wählen Sie unter **Rolle** die Option **Leser** aus.
1. Behalten Sie unter **Zugriff zuweisen zu** den Standardwert von **Azure AD-Benutzer, -Gruppe oder -Dienstprinzipal** bei.
1. Geben Sie unter **Auswählen** die E-Mail-Adresse der Person ein, die Sie einladen möchten.
1. Wenn der Benutzer außerhalb Ihrer Organisation ist, wird die Meldung **Dieser Benutzer erhält eine E-Mail, die ihm die Zusammenarbeit mit Microsoft ermöglicht.** angezeigt. Wählen Sie den Benutzer mit der E-Mail-Adresse aus, und klicken Sie dann auf **Speichern**.


### <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli)

Um die Objekt-ID Ihres Katalogs abzurufen, verwenden Sie [az sig show](/cli/azure/sig#az_sig_show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Verwenden Sie die Objekt-ID als Bereich, zusammen mit einer E-Mail-Adresse und [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create), um einem Benutzer Zugriff auf den geteilten Imagekatalog zu gewähren. Ersetzen Sie `<email-address>` und `<gallery iD>` durch Ihre eigenen Angaben.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

Weitere Informationen zum Teilen von Ressourcen mittels RBAC finden Sie unter [Verwalten des Zugriffs mithilfe von RBAC und der Azure CLI](../role-based-access-control/role-assignments-cli.md).

### <a name="powershell"></a>[PowerShell](#tab/powershell)

Verwenden Sie eine E-Mail-Adresse und das Cmdlet [Get-AzADUser](/powershell/module/az.resources/get-azaduser), um die Objekt-ID für den Benutzer abzurufen, und verwenden Sie dann [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment), um ihnen Zugriff auf den Katalog zu gewähren. Ersetzen Sie die Beispiel-E-Mail-Adresse alinne_montes@contoso.com in diesem Beispiel durch Ihre eigenen Informationen.

```azurepowershell-interactive
# Get the object ID for the user
$user = Get-AzADUser -StartsWith alinne_montes@contoso.com
# Grant access to the user for our gallery
New-AzRoleAssignment `
   -ObjectId $user.Id `
   -RoleDefinitionName Reader `
   -ResourceName $gallery.Name `
   -ResourceType Microsoft.Compute/galleries `
   -ResourceGroupName $resourceGroup.ResourceGroupName

```

---


## <a name="next-steps"></a>Nächste Schritte

Erstellen Sie eine Bilddefinition [ und eine Bildversion](image-version.md).

[Azure Image Builder (Vorschauversion)](./image-builder-overview.md) hilft beim Automatisieren der Erstellung von Imageversionen. Sie können den Dienst sogar zum Aktualisieren und [Erstellen einer neuen Imageversion aus einer vorhandenen](./windows/image-builder-gallery-update-image-version.md) verwenden. 

Sie können auch mithilfe von Vorlagen eine Ressource im Katalog für freigegebene Images erstellen. Es stehen mehrere Azure-Schnellstartvorlagen zur Verfügung: 

- [Erstellen eines Katalogs mit freigegebenen Images](https://azure.microsoft.com/resources/templates/sig-create/)
- [Erstellen einer Imagedefinition in einem Katalog mit freigegebenen Images](https://azure.microsoft.com/resources/templates/sig-image-definition-create/)
- [Erstellen einer Imageversion in einem Katalog mit freigegebenen Images](https://azure.microsoft.com/resources/templates/sig-image-version-create/)
