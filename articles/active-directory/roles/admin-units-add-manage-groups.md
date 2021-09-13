---
title: Hinzufügen, Entfernen und Auflisten von Gruppen in einer Verwaltungseinheit – Azure Active Directory | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie Gruppen und deren Rollenberechtigungen in einer Verwaltungseinheit in Azure Active Directory verwalten.
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
ms.openlocfilehash: c824ee3a8365261fa6409bf79904cde1b409fc26
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346427"
---
# <a name="add-and-manage-groups-in-an-administrative-unit-in-azure-active-directory"></a>Hinzufügen und Verwalten von Gruppen in einer Verwaltungseinheit in Azure Active Directory

In Azure Active Directory (Azure AD) können Sie einer Verwaltungseinheit Gruppen hinzufügen, um eine präzisere Steuerung der Verwaltungsbereiche zu erzielen.

## <a name="prerequisites"></a>Voraussetzungen

- Azure AD Premium P1- oder P2-Lizenz für jeden Administrator der Verwaltungseinheit
- Azure AD Free-Lizenzen für Mitglieder von Verwaltungseinheiten
- „Administrator für privilegierte Rollen“ oder „Globaler Administrator“
- AzureAD-Modul bei Verwendung von PowerShell
- Administratorzustimmung bei Verwendung von Graph-Tester für die Microsoft Graph-API

Weitere Informationen finden Sie unter [Voraussetzungen für die Verwendung von PowerShell oder Graph-Tester](prerequisites.md).

## <a name="add-groups-to-an-administrative-unit"></a>Zuweisen von Gruppen zu einer Verwaltungseinheit

Sie können Gruppen einer Verwaltungseinheit hinzufügen, indem Sie das Azure-Portal, PowerShell oder Microsoft Graph verwenden.

### <a name="azure-portal"></a>Azure-Portal

Sie können Gruppen einer Verwaltungseinheit nur einzeln zuweisen. Eine Option zum Zuweisen von Gruppen per Massenvorgang ist nicht vorhanden. Es gibt zwei Möglichkeiten, wie Sie im Azure-Portal eine Gruppe einer Verwaltungseinheit zuweisen können:

* Im Bereich **Gruppen**:

  1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) oder bei [Azure AD Admin Center](https://aad.portal.azure.com) an.

  1. Klicken Sie auf **Azure Active Directory** > **Gruppen**.

  1. Wählen Sie die Gruppe aus, die Sie einer Verwaltungseinheit zuweisen möchten.

  1. Wählen Sie im linken Bereich die Option **Verwaltungseinheiten** aus, um eine Liste mit den Verwaltungseinheiten anzuzeigen, denen die Gruppe zugewiesen ist. 

     ![Screenshot: Link „Der Verwaltungseinheit zuweisen“ im Bereich „Verwaltungseinheiten“](./media/admin-units-add-manage-groups/assign-to-group-1.png)

  1. Wählen Sie die Option **Der Verwaltungseinheit zuweisen** aus.

  1. Wählen Sie im rechten Bereich die Verwaltungseinheit aus.

* Im Bereich **Verwaltungseinheiten** > **Gruppen**:

  1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) oder bei [Azure AD Admin Center](https://aad.portal.azure.com) an.

  1. Klicken Sie auf **Azure Active Directory** > **Verwaltungseinheiten**.
  
  1. Wählen Sie eine Verwaltungseinheit aus, der Sie eine Gruppe hinzufügen möchten.
  
  1. Wählen Sie **Gruppen** aus. Alle Gruppen, die der Verwaltungseinheit bereits zugewiesen sind, werden im rechten Bereich angezeigt. 

  1. Wählen Sie im Bereich **Gruppen** die Option **Hinzufügen** aus.
    Im rechten Bereich sind alle verfügbaren Gruppen Ihrer Azure AD-Organisation aufgeführt. 

     ![Screenshot: Schaltfläche „Hinzufügen“ zum Hinzufügen einer Gruppe zu einer Verwaltungseinheit](./media/admin-units-add-manage-groups/assign-to-admin-unit.png)

  1. Wählen Sie mindestens eine Gruppe aus, die der Verwaltungseinheit zugewiesen werden soll, und wählen Sie dann die Schaltfläche **Auswählen** aus.

### <a name="powershell"></a>PowerShell

Verwenden Sie im folgenden Beispiel das Cmdlet `Add-AzureADMSAdministrativeUnitMember`, um die Gruppe der Verwaltungseinheit hinzuzufügen. Die Objekt-ID der Verwaltungseinheit und die Objekt-ID der hinzuzufügenden Gruppe werden als Argumente verwendet. Ändern Sie den hervorgehobenen Abschnitt so, wie dies für Ihre jeweilige Umgebung erforderlich ist.


```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
Add-AzureADMSAdministrativeUnitMember -Id $adminUnitObj.Id -RefObjectId $GroupObj.ObjectId
```

### <a name="microsoft-graph-api"></a>Microsoft Graph-API

Führen Sie die folgenden Befehle aus:

Anforderung

```http
POST /administrativeUnits/{admin-unit-id}/members/$ref
```

Text

```http
{
"@odata.id":"https://graph.microsoft.com/v1.0/groups/{group-id}"
}
```

Beispiel

```http
{
"@odata.id":"https://graph.microsoft.com/v1.0/groups/871d21ab-6b4e-4d56-b257-ba27827628f3"
}
```

## <a name="view-a-list-of-groups-in-an-administrative-unit"></a>Anzeigen einer Liste mit Gruppen in einer Verwaltungseinheit

### <a name="azure-portal"></a>Azure-Portal

1. Navigieren Sie im Azure-Portal zu **Azure AD**.

1. Wählen Sie im linken Bereich die Option **Verwaltungseinheiten** und dann die Verwaltungseinheit aus, deren Gruppen Sie anzeigen möchten. Standardmäßig ist im linken Bereich die Option **Alle Benutzer** ausgewählt. 

1. Wählen Sie im linken Bereich die Option **Gruppen** aus. Im rechten Bereich wird eine Liste mit den Gruppen angezeigt, die Mitglieder der ausgewählten Verwaltungseinheit sind.

   ![Screenshot: Bereich „Gruppen“ mit einer Liste der Gruppen in einer Verwaltungseinheit](./media/admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

Führen Sie den folgenden Befehl aus, um eine Liste mit allen Mitgliedern der Verwaltungseinheit anzuzeigen: 

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
Get-AzureADMSAdministrativeUnitMember -Id $adminUnitObj.Id
```

Verwenden Sie den folgenden Codeausschnitt, um alle Gruppen anzuzeigen, die Mitglieder der Verwaltungseinheit sind:

```powershell
foreach ($member in (Get-AzureADMSAdministrativeUnitMember -Id $adminUnitObj.Id)) 
{
if($member.ObjectType -eq "Group")
{
Get-AzureADGroup -ObjectId $member.ObjectId
}
}
```

### <a name="microsoft-graph-api"></a>Microsoft Graph-API

Führen Sie den folgenden Befehl aus:

Anforderung

```http
GET /directory/administrativeUnits/{admin-unit-id}/members/$/microsoft.graph.group
```

Text

```http
{}
```

## <a name="view-a-list-of-administrative-units-for-a-group"></a>Anzeigen einer Liste mit Verwaltungseinheiten für eine Gruppe

### <a name="azure-portal"></a>Azure-Portal

1. Navigieren Sie im Azure-Portal zu **Azure AD**.

1. Wählen Sie im linken Bereich die Option **Gruppen** aus, um eine Liste mit Gruppen anzuzeigen.

1. Wählen Sie eine Gruppe aus, um das Gruppenprofil zu öffnen. 

1. Wählen Sie im linken Bereich die Option **Verwaltungseinheiten** aus, um alle Verwaltungseinheiten aufzulisten, deren Mitglied die Gruppe ist.

   ![Screenshot: Bereich „Verwaltungseinheiten“ mit einer Liste mit Verwaltungseinheiten, denen eine Gruppe zugewiesen ist](./media/admin-units-add-manage-groups/list-group-au.png)

### <a name="powershell"></a>PowerShell

Führen Sie den folgenden Befehl aus:

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }
```

### <a name="microsoft-graph-api"></a>Microsoft Graph-API

Führen Sie den folgenden Befehl aus:

```http
https://graph.microsoft.com/v1.0/groups/{group-id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-group-from-an-administrative-unit"></a>Entfernen einer Gruppe aus einer Verwaltungseinheit

### <a name="azure-portal"></a>Azure-Portal

Im Azure-Portal gibt es zwei Möglichkeiten, wie Sie eine Gruppe aus einer Verwaltungseinheit entfernen können:

- Entfernen aus einer Gruppenübersicht:

  1. Navigieren Sie im Azure-Portal zu **Azure AD**.
  1. Wählen Sie im linken Bereich die Option **Gruppen** aus, und öffnen Sie dann das Profil für die Gruppe, die Sie aus einer Verwaltungseinheit entfernen möchten.
  1. Wählen Sie im linken Bereich die Option **Verwaltungseinheiten** aus, um alle Verwaltungseinheiten aufzulisten, denen die Gruppe zugewiesen ist. 
  1. Wählen Sie die Verwaltungseinheit aus, aus der Sie die Gruppe entfernen möchten, und wählen Sie dann **Aus Verwaltungseinheit entfernen** aus.

     ![Screenshot: Bereich „Verwaltungseinheiten“ mit einer Liste mit Gruppen, die der ausgewählten Verwaltungseinheit zugewiesen sind](./media/admin-units-add-manage-groups/group-au-remove.png)

- Entfernen aus einer Verwaltungseinheit:

  1. Navigieren Sie im Azure-Portal zu **Azure AD**.
  1. Wählen Sie im linken Bereich die Option **Verwaltungseinheiten** und dann die Verwaltungseinheit aus, der die Gruppe zugewiesen ist.
  1. Wählen Sie im linken Bereich die Option **Gruppen** aus, um alle Gruppen aufzulisten, die der Verwaltungseinheit zugewiesen sind.
  1. Wählen Sie die zu entfernende Gruppe und dann die Option **Gruppen entfernen** aus.

    ![Screenshot: Bereich „Gruppen“ mit einer Liste der Gruppen in einer Verwaltungseinheit](./media/admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

Führen Sie den folgenden Befehl aus:

```powershell
Remove-AzureADMSAdministrativeUnitMember -ObjectId $adminUnitId -MemberId $memberGroupObjId
```

### <a name="microsoft-graph-api"></a>Microsoft Graph-API

Führen Sie den folgenden Befehl aus:

```http
https://graph.microsoft.com/v1.0/directory/administrativeUnits/{admin-unit-id}/members/{group-id}/$ref
```

## <a name="next-steps"></a>Nächste Schritte

- [Zuweisen einer Rolle zu einer Verwaltungseinheit](admin-units-assign-roles.md)
- [Verwalten von Benutzern in einer Verwaltungseinheit](admin-units-add-manage-users.md)
