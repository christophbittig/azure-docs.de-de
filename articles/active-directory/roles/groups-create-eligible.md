---
title: Erstellen einer Gruppe für das Zuweisen von Rollen in Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie in Azure AD eine Gruppe erstellen, der Rollen zugewiesen werden können. Verwalten von Azure-Rollen im Azure-Portal, mit PowerShell oder über die Graph-API.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 07/30/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c14580790891190f40dd2866aaac25ad2c286137
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346849"
---
# <a name="create-a-role-assignable-group-in-azure-active-directory"></a>Erstellen einer Gruppe in Azure Active Directory für das Zuweisen von Rollen

Eine Rolle kann nur einer Gruppe zugewiesen werden, die mit der isAssignableToRole-Eigenschaft mit dem Wert TRUE oder die im Azure-Portal mit aktivierter Option **Azure AD-Rollen können der Gruppe zugewiesen werden** erstellt wurde. Mit diesem Gruppenattribut ist es möglich, der Gruppe eine Rolle in Azure Active Directory (Azure AD) zuzuweisen. In diesem Artikel wird beschrieben, wie Sie diesen besonderen Gruppentyp erstellen. **Hinweis:** Eine Gruppe, deren isAssignableToRole-Eigenschaft auf „true“ festgelegt ist, kann nicht den dynamischen Mitgliedschaftstyp aufweisen. Weitere Informationen finden Sie unter [Verwenden von Azure AD-Gruppen zum Verwalten von Rollenzuweisungen](groups-concept.md).

## <a name="prerequisites"></a>Voraussetzungen

- Eine Lizenz vom Typ Azure AD Premium P1 oder P2
- „Administrator für privilegierte Rollen“ oder „Globaler Administrator“
- AzureAD-Modul bei Verwendung von PowerShell
- Administratorzustimmung bei Verwendung von Graph-Tester für die Microsoft Graph-API

Weitere Informationen finden Sie unter [Voraussetzungen für die Verwendung von PowerShell oder Graph-Tester](prerequisites.md).

## <a name="azure-portal"></a>Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) oder [Azure AD Admin Center](https://aad.portal.azure.com) an.

1. Wählen Sie **Azure Active Directory** > **Gruppen** > **Alle Gruppen** > **Neue Gruppe** aus.

    [![Öffnen von Azure Active Directory und Erstellen einer neuen Gruppe](./media/groups-create-eligible/new-group.png "Öffnen von Azure Active Directory und Erstellen einer neuen Gruppe")](./media/groups-create-eligible/new-group.png#<lightbox>)

1. Geben Sie auf der Registerkarte **Neue Gruppe** den Gruppentyp, den Namen und eine Beschreibung an.

1. Aktivieren Sie **Azure AD-Rollen können der Gruppe zugewiesen werden**. Dieser Schalter wird nur den Rollen „Administratoren für privilegierte Rollen“ und „Globaler Administrator“ angezeigt, da nur diese beiden Rollen den Schalter festlegen können.

    [![Aktivieren der Rollenzuweisung für die neue Gruppe](./media/groups-create-eligible/eligible-switch.png "Aktivieren der Rollenzuweisung für die neue Gruppe")](./media/groups-create-eligible/eligible-switch.png#<lightbox>)

1. Wählen Sie die Mitglieder und Besitzer der Gruppe aus. Sie haben auch die Möglichkeit, der Gruppe Rollen zuzuweisen, dies ist hier allerdings nicht erforderlich.

    [![Hinzufügen von Mitgliedern zu der Gruppe mit Rollenzuweisung und Zuweisen von Rollen](./media/groups-create-eligible/specify-members.png "Hinzufügen von Mitgliedern zu der Gruppe mit Rollenzuweisung und Zuweisen von Rollen")](./media/groups-create-eligible/specify-members.png#<lightbox>)

1. Nachdem Sie die Mitglieder und Besitzer angegeben haben, wählen Sie **Erstellen** aus.

    [![Schaltfläche „Erstellen“ am unteren Rand der Seite](./media/groups-create-eligible/create-button.png "Schaltfläche „Erstellen“ am unteren Rand der Seite")](./media/groups-create-eligible/create-button.png#<lightbox>)

Die Gruppe wird mit allen Rollen erstellt, die Sie ihr zugewiesen haben.

## <a name="powershell"></a>PowerShell

### <a name="create-a-group-that-can-be-assigned-to-role"></a>Erstellen einer Gruppe, der die Rolle zugewiesen werden kann

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true
```

Bei diesem Gruppentyp ist `isPublic` immer FALSE und `isSecurityEnabled` immer TRUE.

### <a name="copy-one-groups-users-and-service-principals-into-a-role-assignable-group"></a>Kopieren der Benutzer und Dienstprinzipale einer Gruppe in eine Gruppe mit Rollenzuweisungen

```powershell
#Basic set up
Install-Module -Name AzureAD
Import-Module -Name AzureAD
Get-Module -Name AzureAD

#Connect to Azure AD. Sign in as Privileged Role Administrator or Global Administrator. Only these two roles can create a role-assignable group.
Connect-AzureAD

#Input variabled: Existing group
$idOfExistingGroup = "14044411-d170-4cb0-99db-263ca3740a0c"

#Input variables: New role-assignable group
$groupName = "Contoso_Bellevue_Admins"
$groupDescription = "This group is assigned to Helpdesk Administrator built-in role in Azure AD."
$mailNickname = "contosobellevueadmins"

#Create new security group which is a role assignable group. For creating a Microsoft 365 group, set GroupTypes="Unified" and MailEnabled=$true
$roleAssignablegroup = New-AzureADMSGroup -DisplayName $groupName -Description $groupDescription -MailEnabled $false -MailNickname $mailNickname -SecurityEnabled $true -IsAssignableToRole $true

#Get details of existing group
$existingGroup = Get-AzureADMSGroup -Id $idOfExistingGroup
$membersOfExistingGroup = Get-AzureADGroupMember -ObjectId $existingGroup.Id

#Copy users and service principals from existing group to new group
foreach($member in $membersOfExistingGroup){
if($member.ObjectType -eq 'User' -or $member.ObjectType -eq 'ServicePrincipal'){
Add-AzureADGroupMember -ObjectId $roleAssignablegroup.Id -RefObjectId $member.ObjectId
}
}
```

## <a name="microsoft-graph-api"></a>Microsoft Graph-API

### <a name="create-a-role-assignable-group-in-azure-ad"></a>Erstellen einer Gruppe, der Rollen zugewiesen werden können, in Azure AD

```http
POST https://graph.microsoft.com/beta/groups
{
  "description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD.",
  "displayName": "Contoso_Helpdesk_Administrators",
  "groupTypes": [
    "Unified"
  ],
  "isAssignableToRole": true,
  "mailEnabled": true,
  "securityEnabled": true,
  "mailNickname": "contosohelpdeskadministrators",
  "visibility" : "Private"
}
```

Bei diesem Gruppentyp ist `isPublic` immer FALSE und `isSecurityEnabled` immer TRUE.

## <a name="next-steps"></a>Nächste Schritte

- [Zuweisen von Azure AD-Rollen zu Gruppen](groups-assign-role.md)
- [Verwenden von Azure AD-Gruppen zum Verwalten von Rollenzuweisungen](groups-concept.md)
- [Behandeln von Problemen bei Azure AD-Rollen mit Gruppenzuweisung](groups-faq-troubleshooting.yml)
