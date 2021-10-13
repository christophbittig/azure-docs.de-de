---
title: Genehmigen oder Ablehnen von Anforderungen für Azure AD-Rollen in PIM – Azure AD | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Anforderungen für Azure AD-Rollen in Azure AD Privileged Identity Management (PIM) genehmigen oder ablehnen.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80965c0a1ff0ba14586ac99f08833f3a02a3a608
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129667229"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-privileged-identity-management"></a>Genehmigen oder Ablehnen von Anforderungen für Azure AD-Rollen in PIM

Mit Azure Active Directory (Azure AD) Privileged Identity Management (PIM) können Sie Rollen konfigurieren, um die Genehmigung für die Aktivierung anzufordern, und einen oder mehrere Benutzer oder Gruppen als delegierte genehmigende Personen auswählen. Delegierte genehmigende Personen haben zum Genehmigen von Anforderungen 24 Stunden Zeit. Wenn eine Anforderung nicht innerhalb von 24 Stunden genehmigt wird, muss der berechtigte Benutzer eine neue Anforderung senden. Das 24-Stunden-Zeitfenster für die Genehmigung ist nicht konfigurierbar.

## <a name="view-pending-requests"></a>Anzeigen ausstehender Anforderungen

Als delegierte genehmigende Person erhalten Sie eine E-Mail-Benachrichtigung, wenn Ihre Genehmigung einer Anforderung für eine Azure AD-Rolle aussteht. Sie können diese ausstehenden Anforderungen in Privileged Identity Management anzeigen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Öffnen Sie **Azure AD Privileged Identity Management**.

1. Wählen Sie **Anforderungen genehmigen** aus.

    ![Anforderungen genehmigen: Seite mit einer Anforderung zum Überprüfen von Azure AD-Rollen](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

    Im Abschnitt **Anforderungen für Rollenaktivierungen** wird eine Liste der Anforderungen angezeigt, die Sie noch genehmigen müssen.

## <a name="view-pending-requests-using-graph-api"></a>Anzeigen der ausstehenden Anforderungen mithilfe von Graph-API

### <a name="http-request"></a>HTTP-Anforderung

````HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignmentScheduleRequests/filterByCurrentUser(on='approver')?$filter=status eq 'PendingApproval' 
````

### <a name="http-response"></a>HTTP-Antwort

````HTTP
{ 
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#Collection(unifiedRoleAssignmentScheduleRequest)", 
    "value": [ 
        { 
            "@odata.type": "#microsoft.graph.unifiedRoleAssignmentScheduleRequest", 
            "id": "9f2b5ddb-a50e-44a1-a6f4-f616322262ea", 
            "status": "PendingApproval", 
            "createdDateTime": "2021-07-15T19:57:17.76Z", 
            "completedDateTime": "2021-07-15T19:57:17.537Z", 
            "approvalId": "9f2b5ddb-a50e-44a1-a6f4-f616322262ea", 
            "customData": null, 
            "action": "SelfActivate", 
            "principalId": "d96ea738-3b95-4ae7-9e19-78a083066d5b", 
            "roleDefinitionId": "88d8e3e3-8f55-4a1e-953a-9b9898b8876b", 
            "directoryScopeId": "/", 
            "appScopeId": null, 
            "isValidationOnly": false, 
            "targetScheduleId": "9f2b5ddb-a50e-44a1-a6f4-f616322262ea", 
            "justification": "test", 
            "createdBy": { 
                "application": null, 
                "device": null, 
                "user": { 
                    "displayName": null, 
                    "id": "d96ea738-3b95-4ae7-9e19-78a083066d5b" 
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
    ] 
} 
````

## <a name="approve-requests"></a>Genehmigen von Anforderungen

1. Suchen Sie nach der zu genehmigenden Anforderung, und wählen Sie diese aus. Daraufhin wird eine Seite zum Genehmigen oder Ablehnen von Anforderungen angezeigt.

    ![Screenshot: Seite „Anforderungen genehmigen – Azure AD-Rollen“](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

1. Geben Sie im Feld **Begründung** die geschäftliche Begründung ein.

1. Wählen Sie **Genehmigen** aus. Sie erhalten eine Azure-Benachrichtigung über Ihre Genehmigung.

    ![Genehmigungsbenachrichtigung, dass die Anforderung genehmigt wurde](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

## <a name="approve-pending-requests-using-graph-api"></a>Genehmigen der ausstehenden Anforderungen mithilfe von Graph-API

### <a name="get-ids-for-the-steps-that-require-approval"></a>Abrufen von IDs für die Schritte, die eine Genehmigung erfordern

Für eine bestimmte Aktivierungsanforderung ruft dieser Befehl alle Genehmigungsschritte ab, die eine Genehmigung erfordern. Mehrstufige Genehmigungen werden derzeit nicht unterstützt.

#### <a name="http-request"></a>HTTP-Anforderung

````HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignmentApprovals/<request-ID-GUID> 
````

#### <a name="http-response"></a>HTTP-Antwort

````HTTP
{ 
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#roleManagement/directory/roleAssignmentApprovals/$entity", 
    "id": "<request-ID-GUID>",
    "steps@odata.context": "https://graph.microsoft.com/beta/$metadata#roleManagement/directory/roleAssignmentApprovals('<request-ID-GUID>')/steps", 
    "steps": [ 
        { 
            "id": "<approval-step-ID-GUID>", 
            "displayName": null, 
            "reviewedDateTime": null, 
            "reviewResult": "NotReviewed", 
            "status": "InProgress", 
            "assignedToMe": true, 
            "justification": "", 
            "reviewedBy": null 
        } 
    ] 
} 
````

### <a name="approve-the-activation-request-step"></a>Genehmigen des Aktivierungsanforderungsschritts

#### <a name="http-request"></a>HTTP-Anforderung

````HTTP
PATCH 
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignmentApprovals/<request-ID-GUID>/steps/<approval-step-ID-GUID> 
{ 
    "reviewResult": "Approve", 
    "justification": "abcdefg" 
} 
 ````

#### <a name="http-response"></a>HTTP-Antwort

Erfolgreiche PATCH-Aufrufe generieren eine leere Antwort.

## <a name="deny-requests"></a>Ablehnen von Anforderungen

1. Suchen Sie nach der Anforderung, die Sie ablehnen möchten, und wählen Sie diese aus. Daraufhin wird eine Seite zum Genehmigen oder Ablehnen von Anforderungen angezeigt.

    ![Anforderungen genehmigen: Bereich zum Genehmigen oder Ablehnen von Anforderungen mit Details und dem Feld „Begründung“](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Geben Sie im Feld **Begründung** die geschäftliche Begründung ein.

1. Wählen Sie **Ablehnen** aus. Es wird eine Benachrichtigung mit Ihrer Ablehnung angezeigt.

## <a name="workflow-notifications"></a>Workflowbenachrichtigungen

Nachfolgend finden Sie einige Informationen zu Workflowbenachrichtigungen:

- Die genehmigenden Personen werden per E-Mail benachrichtigt, wenn für die Anforderung einer Rolle die Überprüfung aussteht. E-Mail-Benachrichtigungen enthalten einen direkten Link zur Anforderung, damit diese von der genehmigenden Person genehmigt oder abgelehnt werden kann.
- Anforderungen werden von der ersten genehmigenden Person bearbeitet, die sich mit der Genehmigung oder Ablehnung befasst.
- Wenn eine genehmigende Person auf die Anforderung reagiert, werden alle genehmigenden Personen über die Aktion informiert.
- Globale Administratoren und Administratoren für privilegierte Rollen werden benachrichtigt, wenn ein genehmigter Benutzer in seiner Rolle aktiv wird.

>[!NOTE]
>Wenn ein globaler Administrator oder ein Administrator für privilegierte Rollen der Meinung ist, dass ein genehmigter Benutzer nicht aktiv sein sollte, kann er die aktive Rollenzuweisung in Privileged Identity Management entfernen. Administratoren werden nur über ausstehende Anforderungen benachrichtigt, wenn sie genehmigende Personen sind. Sie können aber ausstehende Anforderungen aller Benutzer anzeigen und abbrechen, indem sie die ausstehenden Anforderungen in Privileged Identity Management anzeigen.

## <a name="next-steps"></a>Nächste Schritte

- [E-Mail-Benachrichtigungen in Privileged Identity Management](pim-email-notifications.md)
- [Genehmigen oder Ablehnen von Anforderungen für Azure-Ressourcenrollen in PIM](pim-resource-roles-approval-workflow.md)
