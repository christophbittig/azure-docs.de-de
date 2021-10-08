---
title: PowerShell für Azure AD-Rollen in PIM – Azure AD | Microsoft-Dokumentation
description: Verwalten Sie Azure AD-Rollen in Azure AD Privileged Identity Management (PIM) mit PowerShell-Cmdlets.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/30/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c53899c8a513d623fc11d7494c3473cf2878d71
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128609914"
---
# <a name="powershell-for-azure-ad-roles-in-privileged-identity-management"></a>PowerShell für Azure AD-Rollen in Privileged Identity Management (PIM)

Dieser Artikel enthält Anweisungen für die Verwendung von Azure Active Directory (Azure AD) PowerShell-Cmdlets, um Azure AD-Rollen in Privileged Identity Management (PIM) zu verwenden. Darüber hinaus erfahren Sie, wie das Azure AD PowerShell-Modul eingerichtet wird.

## <a name="installation-and-setup"></a>Installation und Einrichtung

1. Installieren des Azure AD Preview-Moduls

    ```powershell
    Install-module AzureADPreview
    ```

1. Vergewissern Sie sich, bevor Sie den Vorgang fortsetzen, ob Sie über die erforderlichen Rollenberechtigungen verfügen. Wenn Sie versuchen, Verwaltungsaufgaben wie Rollenzuweisungen oder Aktualisieren von Rolleneinstellungen auszuführen, müssen Sie entweder die Rolle „Globaler Administrator“ oder „Administrator für privilegierte Rollen“ haben. Wenn Sie lediglich versuchen, Ihre eigene Zuweisung zu aktivieren, sind neben den Standardbenutzerberechtigungen keine weiteren Berechtigungen erforderlich.

1. Stellen Sie eine Verbindung zu Azure AD her.

    ```powershell
    $AzureAdCred = Get-Credential  
    Connect-AzureAD -Credential $AzureAdCred
    ```

1. Suchen Sie die Mandanten-ID für Ihre Azure AD-Organisation, indem Sie zu **Azure Active Directory** > **Eigenschaften** > **Verzeichnis-ID** navigieren. Verwenden Sie diese ID im Cmdlet-Bereich, wenn Sie die „resourceId“ bereitstellen müssen.

    ![Suchen der Organisations-ID in den Eigenschaften für die Azure AD-Organisation](./media/powershell-for-azure-ad-roles/tenant-id-for-Azure-ad-org.png)

> [!Note]
> In den folgenden Abschnitten finden Sie einfache Beispiele, die Ihnen beim Start helfen. Eine ausführliche Dokumentation über die folgenden Cmdlets finden Sie unter [/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#privileged_role_management](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#privileged_role_management). Sie müssen jedoch „azureResources“ im Parameter „providerID“ durch „aadRoles“ ersetzen. Sie müssen auch daran denken, die Mandanten-ID für Ihre Azure AD-Organisation als Parameter „ResourceId“ zu verwenden.

## <a name="retrieving-role-definitions"></a>Abrufen von Rollendefinitionen

Verwenden Sie das folgende Cmdlet, um alle integrierten und benutzerdefinierten Azure AD-Rollen in Ihrer Azure AD-Organisation abzurufen. Durch diesen wichtigen Schritt erhalten Sie die Zuordnung zwischen Rollenname und „roleDefinitionId“. „roleDefinitionId“ wird in all diesen Cmdlets zum Verweis auf eine bestimmte Rolle verwendet.

Der Wert von „roleDefinitionId“ ist spezifisch für Ihre Azure AD-Organisation und unterscheidet sich von dem Wert für „roleDefinitionId“, der von der Rollenverwaltungs-API zurückgegeben wird.

```powershell
Get-AzureADMSPrivilegedRoleDefinition -ProviderId aadRoles -ResourceId 926d99e7-117c-4a6a-8031-0cc481e9da26
```

Ergebnis:

![Abrufen aller Rollen für die Azure AD-Organisation](./media/powershell-for-azure-ad-roles/get-all-roles-result.png)

## <a name="retrieving-role-assignments"></a>Abrufen von Rollenzuweisungen

Verwenden Sie das folgende Cmdlet, um alle Rollenzuweisungen in Ihrer Azure AD-Organisation abzurufen.

```powershell
Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26"
```

Verwenden Sie das folgende Cmdlet, um alle Rollenzuweisungen für einen bestimmten Benutzer abzurufen. Diese Liste wird im Azure AD-Portal auch als „Meine Rollen“ bezeichnet. Der einzige Unterschied hier ist, dass Sie einen Filter für die Element-ID hinzugefügt haben. Die Element-ID ist in diesem Zusammenhang die Benutzer-ID oder Gruppen-ID.

```powershell
Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "subjectId eq 'f7d1887c-7777-4ba3-ba3d-974488524a9d'" 
```

Verwenden Sie das folgende Cmdlet, um alle Rollenzuweisungen für eine bestimmte Rolle abzurufen. „roleDefinitionId“ ist in diesem Fall die ID, die vom vorherigen Cmdlet zurückgegeben wird.

```powershell
Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "roleDefinitionId eq '0bb54a22-a3df-4592-9dc7-9e1418f0f61c'"
```

Mithilfe der Cmdlets erhalten Sie eine Liste von Rollenzuweisungsobjekten, wie nachstehend gezeigt. Die Element-ID ist die Benutzer-ID des Benutzers, dem die Rolle zugewiesen ist. Der Zuweisungsstatus kann entweder aktiv („Active“) oder berechtigt („Eligible“) sein. Wenn der Benutzer aktiv ist und sich im Feld „LinkedEligibleRoleAssignmentId“ eine ID befindet, bedeutet das, dass die Rolle derzeit aktiv ist.

Ergebnis:

![Abrufen aller Rollenzuweisungen für die Azure AD-Organisation](./media/powershell-for-azure-ad-roles/get-all-role-assignments-result.png)

## <a name="assign-a-role"></a>Zuweisen einer Rolle

Verwenden Sie das folgende Cmdlet, um eine berechtigte Zuweisung zu erstellen.

```powershell
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'ff690580-d1c6-42b1-8272-c029ded94dec' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'adminAdd' -AssignmentState 'Eligible' -schedule $schedule -reason "dsasdsas" 
```

Der Zeitplan, der Start- und Endzeitpunkt der Zuweisung definiert, ist ein Objekt, das erstellt werden kann, wie aus dem folgenden Beispiel hervorgeht:

```powershell
$schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
$schedule.Type = "Once"
$schedule.StartDateTime = (Get-Date).ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ss.fffZ")
$schedule.endDateTime = "2020-07-25T20:49:11.770Z"
```
> [!Note]
> Wenn der Wert von EndDateTime auf NULL festgelegt ist, weist dies auf eine permanente Zuweisung hin.

## <a name="activate-a-role-assignment"></a>Aktivieren einer Rollenzuweisung

Verwenden Sie das folgende Cmdlet, um eine berechtigte Zuweisung im Zusammenhang mit einem normalen Benutzer zu aktivieren:

```powershell
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'f55a9a68-f424-41b7-8bee-cee6a442d418' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'UserAdd' -AssignmentState 'Active' -Schedule $schedule -Reason "Business Justification for the role assignment"
``` 

Wenn Sie eine berechtigte Zuweisung als Administrator aktivieren müssen, geben Sie für den `Type`-Parameter `adminAdd` an:

```powershell
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'f55a9a68-f424-41b7-8bee-cee6a442d418' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'adminAdd' -AssignmentState 'Active' -Schedule $schedule -Reason "Business Justification for the role assignment"
``` 

Diese Cmdlet ist nahezu identisch mit dem Cmdlet zum Erstellen einer Rollenzuweisung. Der Hauptunterschied zwischen den Cmdlets besteht darin, dass für den Parameter „–Type“ die Aktivierung „userAdd“ und nicht „adminAdd“ lautet. Ein weiterer Unterschied besteht darin, das der Parameter „–AssignmentState“ den Wert „Active“ und nicht „Eligible“ aufweist.

> [!Note]
> Bei der Rollenaktivierung über PowerShell gibt es zwei Szenarien, die Einschränkungen unterliegen.
> 1. Wenn für Ihre Rolleneinstellung ein Ticketsystem bzw. eine Ticketnummer erforderlich ist, gibt es keine Möglichkeit, diese als Parameter anzugeben. Daher ist es nicht möglich, die Rolle außerhalb des Azure-Portals zu aktivieren. Diese Funktion wird in den nächsten Monaten auch für PowerShell eingeführt.
> 1. Wenn für die Rollenaktivierung eine mehrstufige Authentifizierung (MFA) erforderlich ist, gibt es derzeit keine Möglichkeit, in PowerShell Benutzer beim Aktivieren ihrer Rolle zur Eingabe aufzufordern. Stattdessen müssen die Benutzer die Eingabeaufforderung für MFA auslösen, wenn sie eine Verbindung zu Azure AD herstellen, indem sie die Schritte in [diesem Blogbeitrag](http://www.anujchaudhary.com/2020/02/connect-to-azure-ad-powershell-with-mfa.html) von einem unserer Techniker ausführen. Wenn Sie eine App für PIM entwickeln, können Sie als eine mögliche Implementierung Benutzer zur Eingabe auffordern und sie nach dem Erhalt des Fehlers „MfaRule“ wieder mit dem Modul verbinden.

## <a name="retrieving-and-updating-role-settings"></a>Abrufen und Aktualisieren von Rolleneinstellungen

Verwenden Sie das folgende Cmdlet, um alle Rolleneinstellungen in Ihrer Azure AD-Organisation abzurufen.

```powershell
Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "ResourceId eq '926d99e7-117c-4a6a-8031-0cc481e9da26'"
```

Die Einstellung enthält vier Hauptobjekte. Nur drei dieser Objekte werden derzeit von PIM verwendet. „UserMemberSettings“ sind Aktivierungseinstellungen, „AdminEligibleSettings“ sind Zuweisungseinstellungen für berechtigte Zuweisungen, und „AdminmemberSettings“ sind Zuweisungseinstellungen für aktive Zuweisungen.

[![Abrufen und Aktualisieren von Rolleneinstellungen](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png)](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png#lightbox)

Zum Aktualisieren der Rolleneinstellung müssen Sie das vorhandene Einstellungsobjekt für eine bestimmte Rolle abrufen und Änderungen daran vornehmen:

```powershell
Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "ResourceId eq 'tenant id' and RoleDefinitionId eq 'role id'"
$settinga = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedRuleSetting
$settinga.RuleIdentifier = "JustificationRule"
$settinga.Setting = '{"required":false}'
```

Sie können dann den Vorgang fortsetzen und die Einstellung auf eins der Objekt für eine bestimmte Rolle anwenden, wie nachstehend gezeigt. Die hier gezeigte ID ist die Rolleneinstellungs-ID, die aus der Ergebnisliste des Cmdlets zum Abrufen der Rolleneinstellungen abgerufen werden kann.

```powershell
Set-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Id 'ff518d09-47f5-45a9-bb32-71916d9aeadf' -ResourceId '3f5887ed-dd6e-4821-8bde-c813ec508cf9' -RoleDefinitionId '2387ced3-4e95-4c36-a915-73d803f93702' -UserMemberSettings $settinga 
```

## <a name="next-steps"></a>Nächste Schritte

- [Rollendefinitionen in Azure AD](../roles/permissions-reference.md)
