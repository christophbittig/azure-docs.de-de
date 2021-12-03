---
title: Berechtigungen zum Anzeigen und Verwalten von Azure-Reservierungen
description: Hier erfahren Sie, wie Sie Azure-Reservierungen im Azure-Portal anzeigen und verwalten.
author: bandersmsft
ms.reviewer: primittal
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 10/28/2021
ms.author: banders
ms.openlocfilehash: 5ee6972ae409a2912f677c0411daf01aaa96f6f7
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131464697"
---
# <a name="permissions-to-view-and-manage-azure-reservations"></a>Berechtigungen zum Anzeigen und Verwalten von Azure-Reservierungen

In diesem Artikel wird erläutert, wie Reservierungsberechtigungen funktionieren und wie Benutzer*innen Azure-Reservierungen im Azure-Portal und mit Azure PowerShell anzeigen und verwalten können.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="who-can-manage-a-reservation-by-default"></a>Wer kann eine Reservierung standardmäßig verwalten?

Die folgenden Benutzer können Reservierungen standardmäßig anzeigen und verwalten:

- Die Person, die eine Reservierung erwirbt, und der Kontoadministrator des Abrechnungsabonnements, mit dem die Reservierung erworben wird, werden der Reservierungsreihenfolge hinzugefügt.
- Abrechnungsadministratoren für Konzernvertrag (Enterprise Agreement, EA) und Microsoft-Kundenvereinbarung.
- Benutzer mit erhöhten Zugriffsrechten zum Verwalten aller Azure-Abonnements und Verwaltungsgruppen
- Reservierungsadministrator*innen für Reservierungen in Azure Active Directory-Mandanten (Azure AD) (Verzeichnis)
- Reservierungsleser*innen mit schreibgeschütztem Zugriff auf Reservierungen in Azure Active Directory-Mandanten (Verzeichnis)

Derzeit können die Rollen „Reservierungsadministrator“ und „Reservierungsleser“ nur mithilfe von PowerShell zugewiesen werden. Sie können im Azure-Portal nicht angezeigt oder zugewiesen werden. Weitere Informationen finden Sie unter [Gewähren des Zugriffs mit PowerShell](#grant-access-with-powershell).

Der Reservierungslebenszyklus ist unabhängig von einem Azure-Abonnement. Daher handelt es sich bei der Reservierung nicht um eine Ressource unter dem Azure-Abonnement. Stattdessen ist es eine Ressource auf Mandantenebene mit eigener Azure RBAC-Berechtigung, die von Abonnements getrennt ist. Reservierungen erben nach dem Kauf keine Berechtigungen von Abonnements.

## <a name="view-and-manage-reservations"></a>Anzeigen und Verwalten von Reservierungen

Führen Sie als Abrechnungsadministrator*in die folgenden Schritte aus, um alle Reservierungen und Reservierungstransaktionen im Azure-Portal anzuzeigen und zu verwalten.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu **Kostenverwaltung + Abrechnung**.
    - Wenn Sie EA-Administrator sind, wählen Sie im Menü auf der linken Seite die Option **Abrechnungsbereiche** und dann in der Liste der Abrechnungsbereiche einen Bereich aus.
    - Wenn Sie Besitzer eines Abrechnungsprofils für eine Microsoft-Kundenvereinbarung sind, wählen Sie im linken Menü **Abrechnungsprofile** aus. Wählen Sie in der Liste der Abrechnungsprofile ein Profil aus.
1. Wählen Sie im Menü auf der linken Seite **Produkte und Dienste** > **Reservierungen** aus.
1. Die vollständige Liste der Reservierungen für Ihr EA-Registrierungsprofil oder -Abrechnungsprofil wird angezeigt.
1. Abrechnungsadministratoren können den Besitz einer Reservierung übernehmen, indem sie mindestens eine Reservierung auswählen, auf **Zugriff gewähren** klicken und dann im angezeigten Fenster die Option **Zugriff gewähren** auswählen.

### <a name="add-billing-administrators"></a>Hinzufügen von Abrechnungsadministrator*innen

Fügen Sie als Abrechnungsadministrator*in im Azure-Portal Benutzer*innen zu einem Enterprise Agreement oder einer Microsoft-Kundenvereinbarung hinzu.

- Fügen Sie bei einem Konzernvertrag Benutzer mit der Rolle _Unternehmensadministrator_ hinzu, um alle für diesen Vertrag geltenden Reservierungsaufträge anzuzeigen und zu verwalten. Unternehmensadministratoren können Reservierungen in **Cost Management + Billing** anzeigen und verwalten.
    - Benutzer mit der Rolle _Unternehmensadministrator (schreibgeschützt)_ können die Reservierung nur in **Cost Management + Billing** anzeigen. 
    - Abteilungsadministratoren und Kontobesitzer können Reservierungen nicht anzeigen, _außer_ wenn sie ihnen mithilfe der Zugriffssteuerung (Access Control, IAM) explizit hinzugefügt werden. Weitere Informationen finden Sie unter [Verwalten von Azure Enterprise-Rollen](../manage/understand-ea-roles.md).
- Bei einer Microsoft-Kundenvereinbarung können Benutzer mit der Rolle „Besitzer des Abrechnungsprofils“ oder „Mitwirkender am Abrechnungsprofil“ alle mit dem Abrechnungsprofil getätigten Reservierungseinkäufe verwalten. Benutzer mit Leseberechtigung für das Abrechnungsprofil und Rechnungs-Manager können alle Reservierungen anzeigen, die für das Abrechnungsprofil bezahlt werden. Sie können allerdings keine Änderungen an den Reservierungen vornehmen.
    Weitere Informationen finden Sie unter [Rollen und Aufgaben für ein Abrechnungsprofil](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="view-reservations-with-azure-rbac-access"></a>Anzeigen von Reservierungen mit Azure RBAC-Zugriff

Wenn Sie die Reservierung erworben haben oder einer Reservierung hinzugefügt werden, führen Sie die folgenden Schritte aus, um Reservierungen im Azure-Portal anzuzeigen und zu verwalten.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Klicken Sie auf **Alle Dienste** > **Reservierungen**, um Reservierungen aufzulisten, auf die Sie Zugriff haben.

## <a name="manage-subscriptions-and-management-groups-with-elevated-access"></a>Verwalten von Abonnements und Verwaltungsgruppen mit erhöhten Zugriffsberechtigungen

Sie können die [Zugriffsrechte eines Benutzers zum Verwalten aller Azure-Abonnements und Verwaltungsgruppen erhöhen](../../role-based-access-control/elevate-access-global-admin.md?toc=/azure/cost-management-billing/reservations/toc.json).

Nach dem Erhöhen der Zugriffsberechtigungen:

1. Navigieren Sie zu **Alle Dienste** > **Reservierung**, um alle Reservierungen im Mandanten anzuzeigen.
1. Um Änderungen an der Reservierung vorzunehmen, fügen Sie sich selbst mithilfe der Zugriffssteuerung (IAM) als Besitzer des Reservierungsauftrags hinzu.

## <a name="grant-access-to-individual-reservations"></a>Gewähren des Zugriffs auf einzelne Reservierungen

Benutzer*innen mit Besitzerzugriff auf die Reservierungen und Abrechnungsadministrator*innen können die Zugriffsverwaltung für einen einzelnen Reservierungsauftrag im Azure-Portal delegieren.

Damit andere Personen Reservierungen verwalten können, haben Sie zwei Optionen:

- Delegieren Sie die Zugriffsverwaltung für einen einzelnen Reservierungsauftrag, indem Sie einem Benutzer im Ressourcenbereich des Reservierungsauftrags die Rolle „Besitzer“ zuweisen. Wenn Sie eingeschränkten Zugriff gewähren möchten, wählen Sie eine andere Rolle aus.  
     Ausführliche Informationen finden Sie unter [Zuweisen von Azure-Rollen über das Azure-Portal](../../role-based-access-control/role-assignments-portal.md).

- Hinzufügen eines Benutzers als Abrechnungsadministrator zu einem Konzernvertrag oder einer Microsoft-Kundenvereinbarung:
    - Fügen Sie bei einem Konzernvertrag Benutzer mit der Rolle _Unternehmensadministrator_ hinzu, um alle für diesen Vertrag geltenden Reservierungsaufträge anzuzeigen und zu verwalten. Benutzer mit der Rolle _Unternehmensadministrator (schreibgeschützt)_ können die Reservierung nur anzeigen. Abteilungsadministratoren und Kontobesitzer können Reservierungen nicht anzeigen, _außer_ wenn sie ihnen mithilfe der Zugriffssteuerung (Access Control, IAM) explizit hinzugefügt werden. Weitere Informationen finden Sie unter [Verwalten von Azure Enterprise-Rollen](../manage/understand-ea-roles.md).

        _Unternehmensadministratoren können den Besitz einer Reservierungsreihenfolge übernehmen und andere Benutzer mithilfe der Zugriffssteuerung (Identity & Access Management, IAM) zu einer Reservierung hinzufügen._
    - Bei einer Microsoft-Kundenvereinbarung können Benutzer mit der Rolle „Besitzer des Abrechnungsprofils“ oder „Mitwirkender am Abrechnungsprofil“ alle mit dem Abrechnungsprofil getätigten Reservierungseinkäufe verwalten. Benutzer mit Leseberechtigung für das Abrechnungsprofil und Rechnungs-Manager können alle Reservierungen anzeigen, die für das Abrechnungsprofil bezahlt werden. Sie können allerdings keine Änderungen an den Reservierungen vornehmen.
    Weitere Informationen finden Sie unter [Rollen und Aufgaben für ein Abrechnungsprofil](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="grant-access-with-powershell"></a>Gewähren des Zugriffs mit PowerShell

Benutzer*innen mit Besitzerzugriff für Reservierungsaufträge, Benutzer*innen mit erhöhtem Zugriff und [Benutzerzugriffsadministrator*innen](../../role-based-access-control/built-in-roles.md#user-access-administrator) können die Zugriffsverwaltung für alle Reservierungsaufträge delegieren, auf die sie Zugriff haben.

Zugriff, der mithilfe von PowerShell gewährt wurde, wird im Azure-Portal nicht angezeigt. Verwenden Sie stattdessen den Befehl `get-AzRoleAssignment` im folgenden Abschnitt, um zugewiesene Rollen anzuzeigen.

## <a name="assign-the-owner-role-for-all-reservations"></a>Zuweisen der Rolle „Besitzer“ für alle Reservierungen

Verwenden Sie das folgende Azure PowerShell-Skript, um Benutzer*innen RBAC-Zugriff in Azure auf alle Reservierungsaufträge in ihren Azure AD-Mandanten (Verzeichnis) zu gewähren.

```azurepowershell

Import-Module Az.Accounts
Import-Module Az.Resources
 
Connect-AzAccount -Tenant <TenantId>
 
$response = Invoke-AzRestMethod -Path /providers/Microsoft.Capacity/reservations?api-version=2020-06-01 -Method GET
 
$responseJSON = $response.Content | ConvertFrom-JSON
 
$reservationObjects = $responseJSON.value
 
foreach ($reservation in $reservationObjects)
{
  $reservationOrderId = $reservation.id.substring(0, 84)
  Write-Host "Assigning Owner role assignment to "$reservationOrderId
  New-AzRoleAssignment -Scope $reservationOrderId -ObjectId <ObjectId> -RoleDefinitionName Owner
}
```

Wenn Sie das PowerShell-Skript zum Zuweisen der Besitzrolle verwenden, und dies erfolgreich ausgeführt wird, wird keine Erfolgsmeldung zurückgegeben. Sie können jedoch überprüfen, ob die Rolle zugewiesen wurde:

```azurepowershell
get-AzRoleAssignment -Scope "/providers/Microsoft.Capacity" |?{$_.RoleDefinitionName -Like "Reservations*"}
```

### <a name="parameters"></a>Parameter

**-ObjectId**: Dies ist die Azure AD-Objekt-ID von Benutzer*innen, der Gruppe oder des Dienstprinzipals.
- Typ: String
- Aliase: Id, PrincipalId
- Position: Benannt
- Standardwert: Keiner
- Pipelineeingabe akzeptieren: TRUE
- Platzhalterzeichen akzeptieren: FALSE

**-TenantId** Eindeutiger Bezeichner von Mandant*innen.
- Typ: String
- Position: 5
- Standardwert: Keiner
- Pipelineeingabe akzeptieren: FALSE
- Platzhalterzeichen akzeptieren: FALSE

[Benutzerzugriffsadministrator*innen](../../role-based-access-control/built-in-roles.md#user-access-administrator) können Benutzer*innen zu den Rollen „Reservierungsadministrator*in“ und „Reservierungsleser*in“ hinzufügen.

## <a name="add-a-reservation-administrator-role-at-the-tenant-level"></a>Hinzufügen der Rolle „Reservierungsadministrator*in“ auf Mandantenebene

Verwenden Sie das folgende Azure PowerShell-Skript, um mit PowerShell die Rolle „Reservierungsadministrator*in“ auf Mandantenebene hinzuzufügen.

```azurepowershell
Import-Module Az.Accounts
Import-Module Az.Resources
Connect-AzAccount -Tenant <TenantId>
New-AzRoleAssignment -Scope "/providers/Microsoft.Capacity" -PrincipalId <ObjectId> -RoleDefinitionName "Reservations Administrator"
```

### <a name="parameters"></a>Parameter

**-ObjectId**: Dies ist die Azure AD-Objekt-ID von Benutzer*innen, der Gruppe oder des Dienstprinzipals.
- Typ: String
- Aliase: Id, PrincipalId
- Position: Benannt
- Standardwert: Keiner
- Pipelineeingabe akzeptieren: TRUE
- Platzhalterzeichen akzeptieren: FALSE

**-TenantId** Eindeutiger Bezeichner von Mandant*innen.
- Typ: String
- Position: 5
- Standardwert: Keiner
- Pipelineeingabe akzeptieren: FALSE
- Platzhalterzeichen akzeptieren: FALSE

## <a name="assign-a-reservation-reader-role-at-the-tenant-level"></a>Zuweisen der Rolle „Reservierungsleser*in“ auf Mandantenebene

Verwenden Sie das folgende Azure PowerShell-Skript, um mit PowerShell die Rolle „Reservierungsleser*in“ auf Mandantenebene zuzuweisen.

```azurepowershell

Import-Module Az.Accounts
Import-Module Az.Resources

Connect-AzAccount -Tenant <TenantId>

New-AzRoleAssignment -Scope "/providers/Microsoft.Capacity" -PrincipalId <ObjectId> -RoleDefinitionName "Reservations Reader"
```

### <a name="parameters"></a>Parameter

**-ObjectId**: Dies ist die Azure AD-Objekt-ID von Benutzer*innen, der Gruppe oder des Dienstprinzipals.
- Typ: String
- Aliase: Id, PrincipalId
- Position: Benannt
- Standardwert: Keiner
- Pipelineeingabe akzeptieren: TRUE
- Platzhalterzeichen akzeptieren: FALSE

**-TenantId** Eindeutiger Bezeichner von Mandant*innen.
- Typ: String
- Position: 5
- Standardwert: Keiner
- Pipelineeingabe akzeptieren: FALSE
- Platzhalterzeichen akzeptieren: FALSE


## <a name="next-steps"></a>Nächste Schritte

- [Verwalten von Azure-Reservierungen](manage-reserved-vm-instance.md)
