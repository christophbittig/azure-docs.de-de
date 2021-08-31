---
title: Zuweisen einer Azure-Rolle für den Zugriff auf Tabellendaten (Vorschau)
titleSuffix: Azure Storage
description: Hier erfahren Sie, wie Sie Berechtigungen für Tabellendaten für einen Azure Active Directory-Sicherheitsprinzipal mit rollenbasierter Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC) zuweisen können (Vorschau). Azure Storage unterstützt integrierte und benutzerdefinierte Azure-Rollen für die Authentifizierung und Autorisierung über Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/13/2021
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 19c377c1b6d0b5cad7515ca199133f8d10c8742b
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/14/2021
ms.locfileid: "113760606"
---
# <a name="assign-an-azure-role-for-access-to-table-data-preview"></a>Zuweisen einer Azure-Rolle für den Zugriff auf Tabellendaten (Vorschau)

Azure Active Directory (Azure AD) autorisiert Rechte für den Zugriff auf geschützte Ressourcen über die [rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)](../../role-based-access-control/overview.md). Azure Storage definiert eine Reihe von in Azure integrierten Rollen mit allgemeinen Berechtigungssätzen für den Zugriff auf Tabellendaten in Azure Storage (Vorschau).

Wenn einem Azure AD-Sicherheitsprinzipal eine Azure-Rolle zugewiesen wird, gewährt Azure diesem Sicherheitsprinzipal Zugriff auf diese Ressourcen. Eine Azure AD-Sicherheitsprinzipal kann ein Benutzer, eine Gruppe, ein Anwendungsdienstprinzipal oder eine [verwaltete Identität für Azure-Ressourcen](../../active-directory/managed-identities-azure-resources/overview.md) sein.

Weitere Informationen zur Verwendung von Azure AD zum Autorisieren des Zugriffs auf Tabellendaten finden Sie unter [Autorisieren des Zugriffs auf Tabellen mithilfe von Azure Active Directory](authorize-access-azure-active-directory.md).

> [!IMPORTANT]
> Die Autorisierung mit Azure AD für Tabellen befindet sich derzeit in der **VORSCHAU**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten rechtliche Bedingungen. Sie gelten für diejenigen Azure-Features, die sich in der Beta- oder Vorschauversion befinden oder aber anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

## <a name="assign-an-azure-role"></a>Zuweisen einer Azure-Rolle

Sie können PowerShell, die Azure CLI oder eine Azure Resource Manager-Vorlage verwenden, um eine Rolle für den Datenzugriff zuzuweisen.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Wenn Sie eine Azure-Rolle einem Sicherheitsprinzipal zuweisen möchten, rufen Sie den Befehl [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) auf. Das Format des Befehls kann je nach Bereich der Zuweisung unterschiedlich sein. Zur Ausführung des Befehls müssen Sie über eine Rolle verfügen, die **Microsoft.Authorization/roleAssignments/write**-Berechtigungen enthält, die Ihnen im entsprechenden Bereich oder höher zugewiesen wurden.

Geben Sie zum Zuweisen einer auf eine Tabelle begrenzten Rolle für den Parameter `--scope` eine Zeichenfolge an, die den Tabellenbereich enthält. Der Bereich für eine Tabelle weist folgendes Format auf:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/tableServices/default/tables/<table-name>
```

Im folgenden Beispiel wird die Rolle **Mitwirkender an Storage-Tabellendaten** einem Benutzer zugewiesen und auf eine Tabelle begrenzt. Ersetzen Sie die Beispielwerte und die Platzhalterwerte in Klammern durch Ihre eigenen Werte:

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Table Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/tableServices/default/tables/<table>"
```

Informationen zum Zuweisen von Rollen mit PowerShell im Abonnement-, Ressourcengruppen- oder Speicherkontobereich finden Sie unter [Zuweisen von Azure-Rollen mithilfe von Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie den Befehl [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create), um einem Sicherheitsprinzipal eine Azure-Rolle zuzuweisen. Das Format des Befehls kann je nach Bereich der Zuweisung unterschiedlich sein. Das Format des Befehls kann je nach Bereich der Zuweisung unterschiedlich sein. Zur Ausführung des Befehls müssen Sie über eine Rolle verfügen, die **Microsoft.Authorization/roleAssignments/write**-Berechtigungen enthält, die Ihnen im entsprechenden Bereich oder höher zugewiesen wurden.

Geben Sie zum Zuweisen einer auf eine Tabelle begrenzten Rolle für den Parameter `--scope` eine Zeichenfolge an, die den Tabellenbereich enthält. Der Bereich für eine Tabelle weist folgendes Format auf:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/tableServices/default/tables/<table-name>
```

Im folgenden Beispiel wird die Rolle **Mitwirkender an Storage-Tabellendaten** einem Benutzer zugewiesen und auf die Ebene der Tabelle begrenzt. Ersetzen Sie die Beispielwerte und die Platzhalterwerte in Klammern durch Ihre eigenen Werte:

```azurecli-interactive
az role assignment create \
    --role "Storage Table Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/tableServices/default/tables/<table>"
```

Informationen zum Zuweisen von Rollen mit PowerShell im Abonnement-, Ressourcengruppen- oder Speicherkontobereich finden Sie unter [Zuweisen von Azure-Rollen mithilfe der Azure CLI](../../role-based-access-control/role-assignments-cli.md).

# <a name="template"></a>[Vorlage](#tab/template)

Informationen zur Verwendung einer Azure Resource Manager-Vorlage zum Zuweisen einer Azure-Rolle finden Sie unter [Zuweisen von Azure-Rollen mithilfe von Azure Resource Manager-Vorlagen](../../role-based-access-control/role-assignments-template.md).

---

Beachten Sie die folgenden Punkte zu Azure-Rollenzuweisungen in Azure Storage:

- Wenn Sie ein Azure Storage-Konto erstellen, erhalten Sie nicht automatisch Berechtigungen für den Zugriff auf Daten über Azure AD. Sie müssen sich selbst explizit eine Azure-Rolle für Azure Storage zuweisen. Sie können sie auf Ebene Ihres Abonnements, einer Ressourcengruppe, eines Speicherkontos oder einer Tabelle zuweisen.
- Wenn das Speicherkonto mit einem Schreibschutz von Azure Resource Manager gesperrt wurde, verhindert diese Sperre die Zuweisung von Azure-Rollen, die für das Speicherkonto oder eine Tabelle gelten.
- Die Vorschauversion des Storage-Explorers im Azure-Portal unterstützt nicht die Verwendung von Azure AD-Anmeldeinformationen zum Anzeigen und Ändern von Tabellendaten. Storage-Explorer im Azure-Portal verwendet immer die Kontoschlüssel für den Zugriff auf Daten. Um Storage-Explorer im Azure-Portal verwenden zu können, muss Ihnen eine Rolle zugewiesen werden, in der **Microsoft.Storage/storageAccounts/listkeys/action** enthalten ist.

## <a name="next-steps"></a>Nächste Schritte

- [Was ist die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)?](../../role-based-access-control/overview.md)
- [Bewährte Methoden für Azure RBAC](../../role-based-access-control/best-practices.md)
