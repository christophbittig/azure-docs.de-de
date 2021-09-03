---
title: Anzeigen der zugewiesenen Rollen einer Gruppe in Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie, wie die einer Gruppe zugewiesenen Rollen im Azure-Portal angezeigt werden können. Das Anzeigen von Gruppen und zugewiesenen Rollen gehört zu den Standardbenutzerberechtigungen.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 05/14/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87e59c7ab9bfc5fa9211d84d2d9a855dea97ec1a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346260"
---
# <a name="view-roles-assigned-to-a-group-in-azure-active-directory"></a>Anzeigen der zugewiesenen Rollen einer Gruppe in Azure Active Directory

In diesem Abschnitt wird beschrieben, wie die einer Gruppe zugewiesenen Rollen im Azure-Portal angezeigt werden können. Das Anzeigen von Gruppen und zugewiesenen Rollen gehört zu den Standardbenutzerberechtigungen.

## <a name="prerequisites"></a>Voraussetzungen

- AzureAD-Modul bei Verwendung von PowerShell
- Administratorzustimmung bei Verwendung von Graph-Tester für die Microsoft Graph-API

Weitere Informationen finden Sie unter [Voraussetzungen für die Verwendung von PowerShell oder Graph-Tester](prerequisites.md).

## <a name="azure-portal"></a>Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) oder [Azure AD Admin Center](https://aad.portal.azure.com) an.

1. Klicken Sie auf **Azure Active Directory** > **Gruppen**.

1. Wählen Sie eine der Rolle zuweisbare Gruppe aus, an der Sie interessiert sind.

1. Wählen Sie **Zugewiesene Rollen** aus. Damit werden alle Azure AD-Rollen angezeigt, die dieser Gruppe zugewiesen sind.

   ![Anzeigen aller einer ausgewählten Gruppe zugewiesenen Rollen](./media/groups-view-assignments/view-assignments.png)

## <a name="powershell"></a>PowerShell

### <a name="get-object-id-of-the-group"></a>Abrufen der Objekt-ID der Gruppe

```powershell
Get-AzureADMSGroup -SearchString "Contoso_Helpdesk_Administrators"
```

### <a name="view-role-assignment-to-a-group"></a>Anzeigen der Rollenzuweisung einer Gruppe

```powershell
Get-AzureADMSRoleAssignment -Filter "principalId eq '<object id of group>" 
```

## <a name="microsoft-graph-api"></a>Microsoft Graph-API

### <a name="get-object-id-of-the-group"></a>Abrufen der Objekt-ID der Gruppe

```http
GET https://graph.microsoft.com/beta/groups?$filter=displayName+eq+'Contoso_Helpdesk_Administrator'
```

### <a name="get-role-assignments-to-a-group"></a>Abrufen der Rollenzuweisungen einer Gruppe

```http
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=principalId eq
```

## <a name="next-steps"></a>Nächste Schritte

- [Verwenden von Azure AD-Gruppen zum Verwalten von Rollenzuweisungen](groups-concept.md)
- [Behandeln von Problemen bei Azure AD-Rollen mit Gruppenzuweisung](groups-faq-troubleshooting.yml)
