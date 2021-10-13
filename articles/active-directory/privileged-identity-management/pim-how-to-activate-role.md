---
title: Aktivieren meiner Azure AD-Rollen in PIM – Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure AD-Rollen in Azure AD Privileged Identity Management (PIM) aktivieren.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae2bc007d6172e6f7ef2bfc7e22af89acf2c8afb
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669433"
---
# <a name="activate-my-azure-ad-roles-in-pim"></a>Aktivieren meiner Azure AD-Rollen in PIM

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) vereinfacht die Art und Weise, in der Unternehmen den privilegierten Zugriff auf Ressourcen in Azure AD und anderen Microsoft-Onlinediensten wie Microsoft 365 oder Microsoft Intune verwalten.  

Falls Sie zu einer Administratorrolle *berechtigt* sind, müssen Sie die Rollenzuweisung *aktivieren*, wenn Sie privilegierte Aufgaben ausführen möchten. Wenn Sie beispielsweise gelegentlich Microsoft 365-Funktionen verwalten, werden Sie von den Administratoren für privilegierte Rollen Ihrer Organisation möglicherweise nicht als permanenter globaler Administrator festgelegt, da sich diese Rolle auch auf andere Dienste auswirkt. Stattdessen erteilen sie Ihnen Berechtigungen für Azure AD-Rollen (beispielsweise Exchange Online-Administrator). Sie können eine Aktivierung dieser Rolle anfordern, wenn Sie diese Berechtigungen benötigen, und verfügen damit für einen bestimmten Zeitraum über Administratorkontrolle.

Dieser Artikel richtet sich an Administratoren, die ihre Azure AD-Rolle in Privileged Identity Management aktivieren müssen.

## <a name="activate-a-role"></a>Aktivieren einer Rolle

Wenn Sie eine Azure AD-Rolle annehmen müssen, können Sie in Privileged Identity Management die Aktivierung anfordern, indem Sie die Option **Meine Rollen** öffnen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Öffnen Sie **Azure AD Privileged Identity Management**. Informationen zum Hinzufügen der Privileged Identity Management-Kachel zu Ihrem Dashboard finden Sie unter [Einstieg in die Verwendung von PIM](pim-getting-started.md).

1. Wählen Sie **Meine Rollen** aus, und wählen Sie dann **Azure AD-Rollen** aus, um eine Liste der Azure AD-Rollen anzuzeigen, für die Sie berechtigt sind.

    ![Seite „Meine Rollen“ mit den Rollen, die aktiviert werden können](./media/pim-how-to-activate-role/my-roles.png)

1. Suchen Sie in der Liste **Azure AD-Rollen** die zu aktivierende Rolle.

    ![Azure AD-Rollen: Liste meiner berechtigten Rollen](./media/pim-how-to-activate-role/activate-link.png)

1. Wählen Sie **Aktivieren** aus, um den Bereich „Aktivieren“ zu öffnen.

    ![Azure AD Rollen: Aktivierungsseite mit Dauer und Bereich](./media/pim-how-to-activate-role/activate-page.png)

1. Wählen Sie **Zusätzliche Überprüfung erforderlich** aus, und gehen Sie gemäß den Anweisungen vor, um die Sicherheitsüberprüfung zu durchlaufen. Sie müssen sich nur einmal pro Sitzung authentifizieren.

    ![Anzeige, die eine Sicherheitsüberprüfung wie z. B. einen PIN-Code verlangt](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Wählen Sie nach der mehrstufigen Authentifizierung die Option **Aktivieren** aus, bevor Sie den Vorgang fortsetzen.

    ![Bestätigen meiner Identität mit MFA vor der Rollenaktivierung](./media/pim-how-to-activate-role/activate-role-mfa-banner.png)

1. Wenn Sie einen reduzierten Bereich angeben möchten, wählen Sie **Bereich** aus, um den Filterbereich zu öffnen. Im Filterbereich können Sie die Azure AD-Ressourcen angeben, auf die Sie Zugriff benötigen. Es empfiehlt sich, Zugriff auf möglichst wenige benötigte Ressourcen anzufordern.

1. Falls erforderlich, geben Sie einen Startzeitpunkt für die Aktivierung an. Die Azure AD-Rolle wird dann nach dem ausgewählten Zeitpunkt aktiviert.

1. Geben Sie im Feld **Grund** den Grund für die Aktivierungsanforderung ein.

1. Wählen Sie **Aktivieren** aus.

    Wenn für die Aktivierung der [Rolle eine Genehmigung erforderlich ist](pim-resource-roles-approval-workflow.md), werden Sie über eine Benachrichtigung in der oberen rechten Ecke des Browsers darüber informiert, dass die Genehmigung der Anforderung aussteht.

    ![Ausstehende Genehmigung für die Aktivierungsanforderung](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="activate-a-role-using-graph-api"></a>Aktivieren einer Rolle mithilfe der Graph-API

### <a name="get-all-eligible-roles-that-you-can-activate"></a>Abrufen aller berechtigten Rollen, die Sie aktivieren können

Wenn ein Benutzer seine Rollenberechtigung über die Gruppenmitgliedschaft erhält, gibt diese Graphanforderung seine Berechtigung nicht zurück.

#### <a name="http-request"></a>HTTP-Anforderung

````HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleEligibilityScheduleRequests/filterByCurrentUser(on='principal')  
````

#### <a name="http-response"></a>HTTP-Antwort

Um Speicherplatz zu sparen, zeigen wir nur die Antwort für eine Rolle an, aber alle berechtigten Rollenzuweisungen, die Sie aktivieren können, werden aufgelistet.

````HTTP
{ 
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#Collection(unifiedRoleEligibilityScheduleRequest)", 
    "value": [ 
        { 
            "@odata.type": "#microsoft.graph.unifiedRoleEligibilityScheduleRequest", 
            "id": "<request-ID-GUID>", 
            "status": "Provisioned", 
            "createdDateTime": "2021-07-15T19:39:53.33Z", 
            "completedDateTime": "2021-07-15T19:39:53.383Z", 
            "approvalId": null, 
            "customData": null, 
            "action": "AdminAssign", 
            "principalId": "<principal-ID-GUID>", 
            "roleDefinitionId": "<definition-ID-GUID>", 
            "directoryScopeId": "/", 
            "appScopeId": null, 
            "isValidationOnly": false, 
            "targetScheduleId": "<schedule-ID-GUID>", 
            "justification": "test", 
            "createdBy": { 
                "application": null, 
                "device": null, 
                "user": { 
                    "displayName": null, 
                    "id": "<user-ID-GUID>" 
                } 
            }, 
            "scheduleInfo": { 
                "startDateTime": "2021-07-15T19:39:53.3846704Z", 
                "recurrence": null, 
                "expiration": { 
                    "type": "noExpiration", 
                    "endDateTime": null, 
                    "duration": null 
                } 
            }, 
            "ticketInfo": { 
                "ticketNumber": null, 
                "ticketSystem": null 
            } 
        },
} 
````

### <a name="activate-a-role-assignment-with-justification"></a>Aktivieren einer Rollenzuweisung mit Begründung

#### <a name="http-request"></a>HTTP-Anforderung

````HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignmentScheduleRequests 

{ 
    "action": "SelfActivate", 
    "justification": "adssadasasd", 
    "roleDefinitionId": "<definition-ID-GUID>", 
    "directoryScopeId": "/", 
    "principalId": "<principal-ID-GUID>" 
} 
````

#### <a name="http-response"></a>HTTP-Antwort

````HTTP
{ 
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#roleManagement/directory/roleAssignmentScheduleRequests/$entity", 
    "id": "f1ccef03-8750-40e0-b488-5aa2f02e2e55", 
    "status": "PendingApprovalProvisioning", 
    "createdDateTime": "2021-07-15T19:51:07.1870599Z", 
    "completedDateTime": "2021-07-15T19:51:17.3903028Z", 
    "approvalId": "<approval-ID-GUID>", 
    "customData": null, 
    "action": "SelfActivate", 
    "principalId": "<principal-ID-GUID>", 
    "roleDefinitionId": "<definition-ID-GUID>", 
    "directoryScopeId": "/", 
    "appScopeId": null, 
    "isValidationOnly": false, 
    "targetScheduleId": "<schedule-ID-GUID>", 
    "justification": "test", 
    "createdBy": { 
        "application": null, 
        "device": null, 
        "user": { 
            "displayName": null, 
            "id": "<user-ID-GUID>" 
        } 
    }, 
    "scheduleInfo": { 
        "startDateTime": null, 
        "recurrence": null, 
        "expiration": { 
            "type": "afterDuration", 
            "endDateTime": null, 
            "duration": "PT5H30M" 
        } 
    }, 
    "ticketInfo": { 
        "ticketNumber": null, 
        "ticketSystem": null 
    } 
} 
````

## <a name="view-the-status-of-activation-requests"></a>Anzeigen des Status von Aktivierungsanforderungen

Sie können den Status Ihrer ausstehenden Aktivierungsanforderungen anzeigen.

1. Öffnen Sie Azure AD Privileged Identity Management.

1. Wählen Sie **Meine Anforderungen** aus, um eine Liste mit Ihren Anforderungen von Azure AD-Rollen und Azure-Ressourcenrollen anzuzeigen.

    ![Meine Anforderungen: Azure AD-Seite mit Ihren ausstehenden Anforderungen](./media/pim-how-to-activate-role/my-requests-page.png)

1. Scrollen Sie nach rechts, um die Spalte **Anforderungsstatus** anzuzeigen.

## <a name="cancel-a-pending-request-for-new-version"></a>Abbrechen einer ausstehenden Anforderung für die neue Version

Sollten Sie die Aktivierung einer Rolle, für die eine Genehmigung erforderlich ist, nicht mehr benötigen, können Sie eine ausstehende Anforderung jederzeit abbrechen.

1. Öffnen Sie Azure AD Privileged Identity Management.

1. Wählen Sie **Meine Anforderungen** aus.

1. Wählen Sie für die Rolle, für die Sie eine ausstehende Anforderung abbrechen möchten, den Link **Abbrechen** aus.

    Durch das Auswählen von „Abbrechen“ wird die Anforderung abgebrochen. Um die Rolle erneut zu aktivieren, müssen Sie eine neue Anforderung zur Aktivierung übermitteln.

   ![Liste „Meine Anforderungen“ mit hervorgehobener Aktion „Abbrechen“](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot-portal-delay"></a>Problembehandlung bei der Portalverzögerung

### <a name="permissions-arent-granted-after-activating-a-role"></a>Keine Gewährung von Berechtigungen nach der Aktivierung einer Rolle

Wenn Sie in Privileged Identity Management eine Rolle aktivieren, wird die Aktivierung möglicherweise nicht sofort an alle Portale weitergegeben, für die diese privilegierte Rolle benötigt wird. In einigen Fällen kann die Web-Zwischenspeicherung im Portal auch dann dazu führen, dass die Änderung nicht sofort wirksam ist, wenn diese weitergegeben wurde. Wenn Ihre Aktivierung verzögert ist, melden Sie sich vom Portal ab, in dem Sie die Aktion durchführen möchten, und melden Sie sich neu an. Im Azure-Portal werden Sie automatisch von PIM (Privileged Identity Management) abgemeldet und wieder angemeldet.

## <a name="next-steps"></a>Nächste Schritte

- [Anzeigen des Überwachungsverlaufs für Azure AD-Rollen](pim-how-to-use-audit-log.md)
