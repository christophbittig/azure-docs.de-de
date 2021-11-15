---
title: Zuweisen einer Azure-Rolle für den Zugriff auf Blobdaten
titleSuffix: Azure Storage
description: Hier erfahren Sie, wie Sie Berechtigungen für Blobdaten für einen Azure Active Directory-Sicherheitsprinzipal mit rollenbasierter Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC) zuweisen können. Azure Storage unterstützt integrierte und benutzerdefinierte Azure-Rollen für die Authentifizierung und Autorisierung über Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/03/2021
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6d2d2f530d64e3c459e0363079bebc9f99d44cf3
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131578817"
---
# <a name="assign-an-azure-role-for-access-to-blob-data"></a>Zuweisen einer Azure-Rolle für den Zugriff auf Blobdaten

Azure Active Directory (AAD) autorisiert Rechte für den Zugriff auf geschützte Ressourcen über die [rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)](../../role-based-access-control/overview.md). Azure Storage definiert eine Reihe von in Azure integrierten Rollen mit allgemeinen Berechtigungssätzen für den Zugriff auf Blob-Daten.

Wenn einem Azure AD-Sicherheitsprinzipal eine Azure-Rolle zugewiesen wird, gewährt Azure diesem Sicherheitsprinzipal Zugriff auf diese Ressourcen. Eine Azure AD-Sicherheitsprinzipal kann ein Benutzer, eine Gruppe, ein Anwendungsdienstprinzipal oder eine [verwaltete Identität für Azure-Ressourcen](../../active-directory/managed-identities-azure-resources/overview.md) sein.

Weitere Informationen zur Verwendung von Azure AD zum Autorisieren des Zugriffs auf Blob-Daten finden Sie unter [Autorisieren des Zugriffs auf Blobs mithilfe von Azure Active Directory](authorize-access-azure-active-directory.md).

> [!NOTE]
> In diesem Artikel wird gezeigt, wie Sie eine Azure-Rolle für den Zugriff auf Blob-Daten in einem Speicherkonto zuweisen. Informationen zum Zuweisen von Rollen für Verwaltungsvorgänge in Azure Storage finden Sie unter [Verwenden des Azure Storage-Ressourcenanbieters für den Zugriff auf Verwaltungsressourcen](../common/authorization-resource-provider.md).

## <a name="assign-an-azure-role"></a>Zuweisen einer Azure-Rolle

Sie können das Azure-Portal, PowerShell, die Azure CLI oder eine Azure Resource Manager-Vorlage verwenden, um eine Rolle für den Datenzugriff zuzuweisen.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Für den Zugriff auf Blobdaten im Azure-Portal mit Azure AD-Anmeldeinformationen muss ein Benutzer über die folgenden Rollenzuweisungen verfügen:

- Eine Datenzugriffsrolle, z. B. **Mitwirkender an Storage-Blobdaten**
- Die Azure Resource Manager-Rolle **Leser**

Wenn Sie erfahren möchten, wie Sie diese Rollen einem Benutzer zuweisen, führen Sie die Anleitungen unter [Zuweisen von Azure-Rollen über das Azure-Portal](../../role-based-access-control/role-assignments-portal.md) aus.

Die Rolle [Leser](../../role-based-access-control/built-in-roles.md#reader) ist eine Azure Resource Manager-Rolle, die es Benutzern ermöglicht, Ressourcen im Speicherkonto anzuzeigen, ohne sie ändern zu können. Sie bietet keine Leseberechtigungen für Daten in Azure Storage, sondern nur für Ressourcen zur Kontoverwaltung. Die Rolle **Leser** ist erforderlich, damit Benutzer im Azure-Portal zu Blobcontainern navigieren können.

Wenn Sie beispielsweise der Benutzerin Mary die Rolle **Mitwirkender an Storage-Warteschlangendaten** auf der Ebene eines Containers namens **sample-container** zuweisen, erhält Mary Lese-, Schreib- und Löschzugriff auf alle Blobs in diesem Container. Wenn Mary jedoch ein Blob im Azure-Portal anzeigen möchte, bietet die Rolle **Mitwirkender an Storage-Warteschlangendaten** allein nicht genügend Berechtigungen, um im Portal zum Blob zu navigieren, um ihn anzuzeigen. Die zusätzlichen Berechtigungen sind erforderlich, um durch das Portal zu navigieren und die anderen dort sichtbaren Ressourcen anzuzeigen.

Einem Benutzer muss die Rolle **Leser** zugewiesen werden, damit er das Azure-Portal mit Azure AD-Anmeldeinformationen nutzen kann. Wenn ihm aber eine Rolle mit den Berechtigungen **Microsoft.Storage/storageAccounts/listKeys/action** zugewiesen wurde, kann er das Portal mit den Speicherkontoschlüsseln über die Autorisierung mit gemeinsam verwendeten Schlüsseln nutzen. Um die Speicherkontoschlüssel verwenden zu können, muss der Zugriff mit gemeinsam verwendeten Schlüsseln für das Speicherkonto zulässig sein. Weitere Informationen zum Erlauben oder Verweigern des Zugriffs auf gemeinsam verwendete Schlüssel finden Sie unter [Verhindern der Autorisierung mit gemeinsam verwendeten Schlüsseln für ein Azure Storage-Konto](../common/shared-key-authorization-prevent.md).

Sie können auch eine Azure Resource Manager-Rolle zuweisen, die zusätzliche Berechtigungen über die Rolle **Leser** hinaus bietet. Das Zuweisen der geringstmöglichen Berechtigungen wird als bewährte Sicherheitsmethode empfohlen. Weitere Informationen finden Sie unter [Bewährte Methoden für Azure RBAC](../../role-based-access-control/best-practices.md).

> [!NOTE]
> Bevor Sie sich eine Rolle für den Datenzugriff zuweisen, können Sie bereits über das Azure-Portal auf Daten in Ihrem Speicherkonto zugreifen, da das Azure-Portal auch den Kontoschlüssel für den Datenzugriff nutzen kann. Weitere Informationen finden Sie unter [Auswählen der Autorisierung des Zugriffs auf Blobdaten im Azure-Portal](../blobs/authorize-data-operations-portal.md).
>
> Die Vorschauversion von Azure Storage-Explorer im Azure-Portal unterstützt nicht die Verwendung von Azure AD-Anmeldeinformationen zum Anzeigen und Ändern von Blobdaten. Storage-Explorer im Azure-Portal verwendet immer die Kontoschlüssel für den Zugriff auf Daten. Um Storage-Explorer im Azure-Portal verwenden zu können, muss Ihnen eine Rolle zugewiesen werden, in der **Microsoft.Storage/storageAccounts/listkeys/action** enthalten ist.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Wenn Sie eine Azure-Rolle einem Sicherheitsprinzipal mit PowerShell zuweisen möchten, rufen Sie den Befehl [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) auf. Zur Ausführung des Befehls müssen Sie über eine Rolle verfügen, die **Microsoft.Authorization/roleAssignments/write**-Berechtigungen enthält, die Ihnen im entsprechenden Bereich oder höher zugewiesen wurden. 

Das Format des Befehls kann je nach Bereich der Zuweisung unterschiedlich sein, doch `-ObjectId` und `-RoleDefinitionName` sind erforderliche Parameter. Es muss zwar kein Wert für den Parameter `-Scope` übergeben werden, doch wird dies dringend empfohlen, um das Prinzip der geringsten Rechte zu wahren. Durch das Einschränken von Rollen und Bereichen begrenzen Sie die Ressourcen, die bei einer Kompromittierung des Sicherheitsprinzipals gefährdet sind.

Der Parameter `-ObjectId` ist die AAD-Objekt-ID (Azure Active Directory) des Benutzers, der Gruppe oder des Dienstprinzipals, dem bzw. der die Rolle zugewiesen wird. Zum Abrufen des Bezeichners können Sie [get-AzADUser](/powershell/module/az.resources/get-azaduser) verwenden, um Azure Active Directory Benutzer zu filtern, wie im folgenden Beispiel gezeigt.

```azurepowershell
Get-AzADUser -DisplayName '<Display Name>'
(Get-AzADUser -StartsWith '<substring>').Id
```

Die erste Antwort gibt den Sicherheitsprinzipal und die zweite die Objekt-ID des Sicherheitsprinzipals zurück.

```Response
UserPrincipalName : markpdaniels@contoso.com
ObjectType        : User
DisplayName       : Mark P. Daniels
Id                : ab12cd34-ef56-ab12-cd34-ef56ab12cd34
Type              : 

ab12cd34-ef56-ab12-cd34-ef56ab12cd34
```

Der Parameterwert `-RoleDefinitionName` ist der Name der RBAC-Rolle, die dem Prinzipal zugewiesen werden muss. Für den Zugriff auf Blobdaten im Azure-Portal mit Azure AD-Anmeldeinformationen muss ein Benutzer über die folgenden Rollenzuweisungen verfügen:

- Eine Datenzugriffsrolle, z. B. **Mitwirkender an Storage-Blobdaten** oder **Storage-Blobdatenleser**
- Die Azure Resource Manager-Rolle **Leser**

Zum Zuweisen einer auf einen Blobcontainer oder ein Speicherkonto begrenzten Rolle sollten Sie eine Zeichenfolge für den Parameter `-Scope` angeben, die den Ressourcenbereich enthält. Diese Aktion entspricht dem Prinzip der geringsten Rechte, einem Informationssicherheitskonzept, bei dem einem Benutzer die geringstmögliche Zugriffsberechtigung zum Ausführen seiner Aufgaben gewährt wird. Diese Vorgehensweise verringert das potenzielle Risiko von versehentlichen oder absichtlichen Schäden, die durch unnötige Berechtigungen verursacht werden können.

Der Bereich für einen Container weist folgendes Format auf:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

Der Bereich für ein Speicherkonto weist folgendes Format auf:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

Geben Sie zum Zuweisen einer auf ein Speicherkonto begrenzten Rolle für den Parameter `--scope` eine Zeichenfolge an, die den Containerbereich enthält.

Im folgenden Beispiel wird die Rolle **Mitwirkender an Storage-Blobdaten** einem Benutzer zugewiesen und auf einen Container mit dem Namen *sample-container* begrenzt. Ersetzen Sie die Beispielwerte und die Platzhalterwerte in Klammern durch Ihre eigenen Werte:

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/sample-container"
```

Im folgenden Beispiel wird einem Benutzer durch Angabe der Objekt-ID die Rolle **Storage-Blobdatenleser** zugewiesen. Die Rollenzuweisung ist auf ein Speicherkonto mit dem Namen **storage-account** begrenzt. Ersetzen Sie die Beispielwerte und die Platzhalterwerte in Klammern durch Ihre eigenen Werte: 

```powershell
New-AzRoleAssignment -ObjectID "ab12cd34-ef56-ab12-cd34-ef56ab12cd34" `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/storage-account"
```

Die Ausgabe sollte in etwa wie folgt aussehen:

```Response
RoleAssignmentId   : /subscriptions/<subscription ID>/resourceGroups/<Resource Group>/providers/Microsoft.Storage/storageAccounts/<Storage Account>/providers/Microsoft.Authorization/roleAssignments/<Role Assignment ID>
Scope              : /subscriptions/<subscription ID>/resourceGroups/<Resource Group>/providers/Microsoft.Storage/storageAccounts/<Storage Account>
DisplayName        : Mark Patrick
SignInName         : markpdaniels@contoso.com
RoleDefinitionName : Storage Blob Data Reader
RoleDefinitionId   : <Role Definition ID>
ObjectId           : <Object ID>
ObjectType         : User
CanDelegate        : False
```

Informationen zum Zuweisen von Rollen mit PowerShell im Abonnement- oder Ressourcengruppenbereich finden Sie unter [Zuweisen von Azure-Rollen mithilfe von Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie den Befehl [Erstellen einer Azure Rollenzuweisung](/cli/azure/role/assignment#az_role_assignment_create), um einem Sicherheitsprinzipal eine Azure-Rolle mit einer Azure CLI zuzuweisen. Das Format des Befehls kann je nach Bereich der Zuweisung unterschiedlich sein. Das Format des Befehls kann je nach Bereich der Zuweisung unterschiedlich sein. Zur Ausführung des Befehls müssen Sie über eine Rolle verfügen, die **Microsoft.Authorization/roleAssignments/write**-Berechtigungen enthält, die Ihnen im entsprechenden Bereich oder höher zugewiesen wurden.

Geben Sie zum Zuweisen einer auf einen Container begrenzten Rolle für den Parameter `--scope` eine Zeichenfolge an, die den Containerbereich enthält. Der Bereich für einen Container weist folgendes Format auf:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

Im folgenden Beispiel wird die Rolle **Mitwirkender an Storage-Blobdaten** einem Benutzer zugewiesen und auf die Ebene eines Containers begrenzt. Ersetzen Sie die Beispielwerte und die Platzhalterwerte in Klammern durch Ihre eigenen Werte:

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>"
```

Informationen zum Zuweisen von Rollen mit PowerShell im Abonnement-, Ressourcengruppen- oder Speicherkontobereich finden Sie unter [Zuweisen von Azure-Rollen mithilfe der Azure CLI](../../role-based-access-control/role-assignments-cli.md).

# <a name="template"></a>[Vorlage](#tab/template)

Informationen zur Verwendung einer Azure Resource Manager-Vorlage zum Zuweisen einer Azure-Rolle finden Sie unter [Zuweisen von Azure-Rollen mithilfe von Azure Resource Manager-Vorlagen](../../role-based-access-control/role-assignments-template.md).

---

Beachten Sie die folgenden Punkte zu Azure-Rollenzuweisungen in Azure Storage:

- Wenn Sie ein Azure Storage-Konto erstellen, erhalten Sie nicht automatisch Berechtigungen für den Zugriff auf Daten über Azure AD. Sie müssen sich selbst explizit eine Azure-Rolle für Azure Storage zuweisen. Sie können sie auf Ebene Ihres Abonnements, einer Ressourcengruppe, eines Speicherkontos oder eines Containers zuordnen.
- Wenn das Speicherkonto mit einem Schreibschutz von Azure Resource Manager gesperrt wurde, verhindert diese Sperre die Zuweisung von Azure-Rollen, die für das Speicherkonto oder einen Container gelten.
- Wenn Sie die entsprechenden Berechtigungen für den Zugriff auf Daten über Azure AD festgelegt haben und nicht auf die Daten zugreifen können, erhalten Sie z. B. den Fehler „AuthorizationPermissionMismatch“. Stellen Sie sicher, dass Sie genügend Zeit für die Änderungen an den Berechtigungen haben, die Sie in Azure AD vorgenommen haben, um zu replizieren. Stellen Sie auch sicher, dass Sie keine Verweigerungszuweisungen haben, die Ihren Zugriff blockieren. Weitere Informationen finden Sie unter [Verstehen von Azure-Verweigerungszuweisungen](../../role-based-access-control/deny-assignments.md).

## <a name="next-steps"></a>Nächste Schritte

- [Was ist die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)?](../../role-based-access-control/overview.md)
- [Bewährte Methoden für Azure RBAC](../../role-based-access-control/best-practices.md)
