---
title: Hinzufügen und Entfernen von Verwaltungseinheiten – Azure Active Directory | Microsoft-Dokumentation
description: Verwenden Sie Verwaltungseinheiten, um den Geltungsbereich der Rollenberechtigungen in Azure Active Directory einzuschränken.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 05/14/2021
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34a6a353593d733cf50325049cd88034fb16c9ca
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122353684"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Verwalten von Verwaltungseinheiten in Azure Active Directory

Um eine präzisere administrative Steuerung in Azure Active Directory (Azure AD) zu ermöglichen, können Sie Benutzer einer Azure AD-Rolle mit einem auf eine oder mehrere Verwaltungseinheiten beschränkten Bereich zuweisen.


## <a name="prerequisites"></a>Voraussetzungen

- Azure AD Premium P1- oder P2-Lizenz für jeden Administrator der Verwaltungseinheit
- Azure AD Free-Lizenzen für Mitglieder von Verwaltungseinheiten
- „Administrator für privilegierte Rollen“ oder „Globaler Administrator“
- AzureAD-Modul bei Verwendung von PowerShell
- Administratorzustimmung bei Verwendung von Graph-Tester für die Microsoft Graph-API

Weitere Informationen finden Sie unter [Voraussetzungen für die Verwendung von PowerShell oder Graph-Tester](prerequisites.md).

## <a name="add-an-administrative-unit"></a>Hinzufügen einer Verwaltungseinheit

Sie können eine Verwaltungseinheit entweder über das Azure-Portal oder mithilfe von PowerShell hinzufügen.

### <a name="azure-portal"></a>Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) oder bei [Azure AD Admin Center](https://aad.portal.azure.com) an.

1. Wählen Sie **Azure Active Directory** > **Verwaltungseinheiten** aus.

    ![Screenshot: Link „Verwaltungseinheiten“ in Azure AD](./media/admin-units-manage/nav-to-admin-units.png)

1. Wählen Sie oben im Bereich die Schaltfläche **Hinzufügen** aus, und geben Sie dann im Feld **Name** den Namen der Verwaltungseinheit ein. Fügen Sie optional eine Beschreibung der Verwaltungseinheit hinzu.

    ![Screenshot: Schaltfläche „Hinzufügen“ und Feld „Name“ zum Eingeben des Namens der Verwaltungseinheit](./media/admin-units-manage/add-new-admin-unit.png)

1. Wählen Sie die blaue Schaltfläche **Hinzufügen** aus, um die Verwaltungseinheit abzuschließen.

### <a name="powershell"></a>PowerShell

```powershell
Connect-AzureAD
New-AzureADMSAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"
```

Sie können die Werte in den Anführungszeichen nach Bedarf ändern.

### <a name="microsoft-graph-api"></a>Microsoft Graph-API

Anforderung

```http
POST /administrativeUnits
```

Text

```http
{
  "displayName": "North America Operations",
  "description": "North America Operations administration"
}
```

## <a name="remove-an-administrative-unit"></a>Entfernen einer Verwaltungseinheit

In Azure AD können Sie eine Verwaltungseinheit entfernen, die nicht mehr als Bereichseinheit für Administratorrollen benötigt wird.

### <a name="azure-portal"></a>Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) oder bei [Azure AD Admin Center](https://aad.portal.azure.com) an.

1. Wählen Sie **Azure Active Directory** > **Verwaltungseinheiten** aus.
 
1. Wählen Sie die zu löschende Verwaltungseinheit und dann **Löschen** aus.

1. Klicken Sie auf **Ja**, um zu bestätigen, dass Sie die Verwaltungseinheit löschen möchten. Die Verwaltungseinheit wird gelöscht.

    ![Screenshot: Schaltfläche „Löschen“ und Bestätigungsfenster für eine Verwaltungseinheit](./media/admin-units-manage/select-admin-unit-to-delete.png)

### <a name="powershell"></a>PowerShell

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
Remove-AzureADMSAdministrativeUnit -Id $adminUnitObj.Id
```

Sie können die Werte in den Anführungszeichen so ändern, wie sie für die jeweilige Umgebung benötigt werden.

### <a name="microsoft-graph-api"></a>Microsoft Graph-API

Anforderung

```http
DELETE /administrativeUnits/{admin-unit-id}
```

Body

```http
{}
```

## <a name="next-steps"></a>Nächste Schritte

* [Verwalten von Benutzern in einer Verwaltungseinheit](admin-units-add-manage-users.md)
* [Verwalten von Gruppen in einer Verwaltungseinheit](admin-units-add-manage-groups.md)
