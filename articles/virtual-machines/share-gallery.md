---
title: Freigabe einer Galerie mit RBAC
description: Erfahren Sie, wie Sie eine Galerie mithilfe der rollenbasierten Zugriffskontrolle (RBAC) freigeben können.
author: cynthn
ms.service: virtual-machines
ms.subservice: gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 08/31/2021
ms.author: cynthn
ms.custom: template-how-to
ms.openlocfilehash: 373ea9917fb4386ee9b977f64b003eb24a2803fc
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131462991"
---
# <a name="use-rbac-to-share-gallery-resources"></a>RBAC zur Freigabe von Katalogressourcen verwenden

Azure Compute Gallery, Definitionen und Versionen sind allesamt Ressourcen. Sie können mithilfe der integrierten nativen Azure RBAC-Steuerelemente freigegeben werden. Mithilfe von Azure RBAC können Sie diese Ressourcen für andere Benutzer, Dienstprinzipale und Gruppen freigeben. Sie können sogar Zugriff für Personen freigeben, die sich außerhalb des Mandanten befinden, in dem sie erstellt wurden. Sobald ein Benutzer Zugriff auf das Image oder die Version einer Anwendung hat, kann er einen virtuellen Computer oder eine VM-Skalierungsgruppe bereitstellen.  

Zur Erzielung der besten Leistung empfiehlt sich ein Freigeben auf Katalogebene. Wir empfehlen nicht, einzelne Image- oder Anwendungsversionen freizugeben. Weitere Informationen zu Azure RBAC finden Sie unter [Zuweisen von Azure-Rollen](../role-based-access-control/role-assignments-portal.md).

Wenn sich der Benutzer außerhalb Ihrer Organisation befindet, erhält er eine E-Mail-Einladung, um der Organisation beizutreten. Der Benutzer muss die Einladung annehmen, woraufhin er in der Lage ist, den Katalog und alle Imagedefinitionen und -versionen in seiner Liste der Ressourcen anzuzeigen.

## <a name="share-a-gallery"></a>Freigeben eines Katalogs

### <a name="portal"></a>[Portal](#tab/portal)

Wenn sich der Benutzer außerhalb Ihrer Organisation befindet, erhält er eine E-Mail-Einladung, um der Organisation beizutreten. Der Benutzer muss die Einladung annehmen, woraufhin er in der Lage ist, den Katalog und alle Definitionen und Versionen in seiner Liste der Ressourcen anzuzeigen.

1. Wählen Sie auf der Seite für Ihren Katalog im Menü auf der linken Seite die Option **Zugangskontrolle (IAM)** . 
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

Verwenden Sie die Objekt-ID als Bereich, zusammen mit einer E-Mail-Adresse und [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create), um einem Benutzer Zugriff auf Azure Compute Gallery zu gewähren. Ersetzen Sie `<email-address>` und `<gallery iD>` durch Ihre eigenen Angaben.

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

Erstellen Sie eine [Imagedefinition und eine Imageversion](image-version.md).

Sie können auch mithilfe von Vorlagen eine Ressourcen in der Azure Compute Gallery erstellen. Es stehen mehrere Azure-Schnellstartvorlagen zur Verfügung: 

- [Erstellen einer Azure Compute Gallery-Instanz](https://azure.microsoft.com/resources/templates/sig-create/)
- [Erstellen einer Imagedefinition in einer Azure Compute Gallery-Instanz](https://azure.microsoft.com/resources/templates/sig-image-definition-create/)
- [Erstellen einer Imageversion in einer Azure Compute Gallery-Instanz](https://azure.microsoft.com/resources/templates/sig-image-version-create/)
