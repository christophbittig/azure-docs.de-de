---
title: Aktivieren von Azure-Ressourcenrollen in PIM – Azure AD | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ihre Azure-Ressourcenrollen in Azure AD Privileged Identity Management (PIM) aktivieren.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 09/28/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee4bb5c8be060f78f557391e406a8008294f34be
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129272217"
---
# <a name="activate-my-azure-resource-roles-in-privileged-identity-management"></a>Aktivieren meiner Azure-Ressourcenrollen in Privileged Identity Management

Verwenden Sie Privileged Identity Management (PIM), um berechtigten Rollenmitglieder die Planung der Aktivierung für ein künftiges Datum und eine künftige Uhrzeit zu gestatten. Zudem können sie eine bestimmte Aktivierungsdauer innerhalb des (von Administratoren konfigurierten) Bereichs auswählen.

Dieser Artikel richtet sich an Mitglieder, die ihre Azure-Ressourcenrolle in Privileged Identity Management aktivieren müssen.

## <a name="activate-a-role"></a>Aktivieren einer Rolle

Wenn Sie eine Azure-Ressourcenrolle übernehmen müssen, können Sie in Privileged Identity Management über die Navigationsoption **Meine Rollen** die Aktivierung anfordern.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Öffnen Sie **Azure AD Privileged Identity Management**. Informationen zum Hinzufügen der Privileged Identity Management-Kachel zu Ihrem Dashboard finden Sie unter [Einstieg in die Verwendung von PIM](pim-getting-started.md).

1. Wählen Sie **Meine Rollen** aus.

    ![Seite „Meine Rollen“ mit den Rollen, die aktiviert werden können](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. Wählen Sie **Azure-Ressourcenrollen** aus, um eine Liste der Azure-Ressourcenrollen anzuzeigen, für die Sie berechtigt sind.

    ![Meine Rollen – Azure-Ressourcenrollen-Seite](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png)

1. Wechseln Sie in der Liste **Azure-Ressourcenrollen** zur Rolle, die Sie aktivieren möchten.

    ![Azure-Ressourcenrollen: Liste meiner berechtigten Rollen](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. Wählen Sie **Aktivieren** aus, um die Seite „Aktivieren“ zu öffnen.

    ![Geöffneter Aktivierungsbereich mit Umfang, Startzeit, Dauer und Grund](./media/pim-resource-roles-activate-your-roles/azure-role-eligible-activate.png)

1. Wenn Ihre Rolle eine mehrstufige Authentifizierung erfordert, klicken Sie auf **Überprüfen Sie Ihre Identität, bevor Sie den Vorgang fortsetzen**. Sie müssen sich nur einmal pro Sitzung authentifizieren.

    ![Überprüfung meiner Identität mit MFA vor der Rollenaktivierung](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Wählen Sie **Meine Identität überprüfen** aus, und folgen Sie den Anweisungen zur Bereitstellung einer zusätzlichen Sicherheitsüberprüfung.

    ![Anzeige, die eine Sicherheitsüberprüfung wie z. B. einen PIN-Code verlangt](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Wenn Sie einen reduzierten Bereich angeben möchten, wählen Sie **Bereich** aus, um „Ressourcenfilter“ zu öffnen.

    Es wird empfohlen, nur Zugriff auf die Ressourcen anzufordern, die Sie benötigen. Im Bereich „Ressourcenfilter“ können Sie die Ressourcengruppen bzw. angeben, auf die Sie Zugriff haben möchten.

    ![Aktivieren – Ressourcenfilterbereich, der den Umfang spezifiziert](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. Falls erforderlich, geben Sie einen Startzeitpunkt für die Aktivierung an. Das Mitglied wird nach dem ausgewählten Zeitpunkt aktiviert.

1. Geben Sie im Feld **Grund** den Grund für die Aktivierungsanforderung ein.

    ![Vervollständigter Aktivierungsbereich mit Umfang, Startzeit, Dauer und Grund](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. Wählen Sie **Aktivieren** aus.

    Wenn für die Aktivierung der [Rolle eine Genehmigung erforderlich ist](pim-resource-roles-approval-workflow.md), werden Sie über eine Benachrichtigung in der oberen rechten Ecke des Browsers darüber informiert, dass die Genehmigung der Anforderung aussteht.

    ![Ausstehende Genehmigung für die Aktivierungsanforderung](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="activate-a-role-with-arm-api"></a>Aktivieren einer Rolle mit der ARM-API

Privileged Identity Management unterstützt ARM-API-Befehle (Azure Resource Manager) zum Verwalten von Azure-Ressourcenrollen, wie in der [ARM-API-Referenz von PIM](/rest/api/authorization/roleeligibilityschedulerequests) dokumentiert. Informationen zu den Berechtigungen, die für die Verwendung der PIM-API erforderlich sind, finden Sie unter [Verstehen der Privileged Identity Management-APIs](pim-apis.md).

Im Folgenden finden Sie eine Beispiel-HTTP-Anforderung zum Aktivieren einer berechtigten Zuweisung für eine Azure-Rolle.

### <a name="request"></a>Anforderung

````HTTP
PUT https://management.azure.com/providers/Microsoft.Subscription/subscriptions/dfa2a084-766f-4003-8ae1-c4aeb893a99f/providers/Microsoft.Authorization/roleEligibilityScheduleRequests/64caffb6-55c0-4deb-a585-68e948ea1ad6?api-version=2020-10-01-preview
````

### <a name="request-body"></a>Anforderungstext

````JSON
{
  "properties": {
    "principalId": "a3bb8764-cb92-4276-9d2a-ca1e895e55ea",
    "roleDefinitionId": "/subscriptions/dfa2a084-766f-4003-8ae1-c4aeb893a99f/providers/Microsoft.Authorization/roleDefinitions/c8d4ff99-41c3-41a8-9f60-21dfdad59608",
    "requestType": "SelfActivate",
    "linkedRoleEligibilityScheduleId": "b1477448-2cc6-4ceb-93b4-54a202a89413",
    "scheduleInfo": {
      "startDateTime": "2020-09-09T21:35:27.91Z",
      "expiration": {
        "type": "AfterDuration",
        "endDateTime": null,
        "duration": "PT8H"
      }
    },
    "condition": "@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:ContainerName] StringEqualsIgnoreCase 'foo_storage_container'",
    "conditionVersion": "1.0"
  }
}
````

### <a name="response"></a>Antwort

Statuscode: 201

````HTTP
{
  "properties": {
    "targetRoleAssignmentScheduleId": "c9e264ff-3133-4776-a81a-ebc7c33c8ec6",
    "targetRoleAssignmentScheduleInstanceId": null,
    "scope": "/providers/Microsoft.Subscription/subscriptions/dfa2a084-766f-4003-8ae1-c4aeb893a99f",
    "roleDefinitionId": "/subscriptions/dfa2a084-766f-4003-8ae1-c4aeb893a99f/providers/Microsoft.Authorization/roleDefinitions/c8d4ff99-41c3-41a8-9f60-21dfdad59608",
    "principalId": "a3bb8764-cb92-4276-9d2a-ca1e895e55ea",
    "principalType": "User",
    "requestType": "SelfActivate",
    "status": "Provisioned",
    "approvalId": null,
    "scheduleInfo": {
      "startDateTime": "2020-09-09T21:35:27.91Z",
      "expiration": {
        "type": "AfterDuration",
        "endDateTime": null,
        "duration": "PT8H"
      }
    },
    "ticketInfo": {
      "ticketNumber": null,
      "ticketSystem": null
    },
    "justification": null,
    "requestorId": "a3bb8764-cb92-4276-9d2a-ca1e895e55ea",
    "createdOn": "2020-09-09T21:35:27.91Z",
    "condition": "@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:ContainerName] StringEqualsIgnoreCase 'foo_storage_container'",
    "conditionVersion": "1.0",
    "expandedProperties": {
      "scope": {
        "id": "/subscriptions/dfa2a084-766f-4003-8ae1-c4aeb893a99f",
        "displayName": "Pay-As-You-Go",
        "type": "subscription"
      },
      "roleDefinition": {
        "id": "/subscriptions/dfa2a084-766f-4003-8ae1-c4aeb893a99f/providers/Microsoft.Authorization/roleDefinitions/c8d4ff99-41c3-41a8-9f60-21dfdad59608",
        "displayName": "Contributor",
        "type": "BuiltInRole"
      },
      "principal": {
        "id": "a3bb8764-cb92-4276-9d2a-ca1e895e55ea",
        "displayName": "User Account",
        "email": "user@my-tenant.com",
        "type": "User"
      }
    }
  },
  "name": "fea7a502-9a96-4806-a26f-eee560e52045",
  "id": "/providers/Microsoft.Subscription/subscriptions/dfa2a084-766f-4003-8ae1-c4aeb893a99f/providers/Microsoft.Authorization/RoleAssignmentScheduleRequests/fea7a502-9a96-4806-a26f-eee560e52045",
  "type": "Microsoft.Authorization/RoleAssignmentScheduleRequests"
}
````

## <a name="view-the-status-of-your-requests"></a>Anzeigen des Status Ihrer Anforderungen

Sie können den Status Ihrer ausstehenden Aktivierungsanforderungen anzeigen.

1. Öffnen Sie Azure AD Privileged Identity Management.

1. Wählen Sie **Meine Anforderungen** aus, um eine Liste mit Ihren Anforderungen von Azure AD-Rollen und Azure-Ressourcenrollen anzuzeigen.

    ![Meine Anforderungen – Azure-Ressourcenseite, die Ihre ausstehenden Anforderungen zeigt](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. Scrollen Sie nach rechts, um die Spalte **Anforderungsstatus** anzuzeigen.

## <a name="cancel-a-pending-request"></a>Abbrechen einer ausstehenden Anforderung

Sollten Sie die Aktivierung einer Rolle, für die eine Genehmigung erforderlich ist, nicht mehr benötigen, können Sie eine ausstehende Anforderung jederzeit abbrechen.

1. Öffnen Sie Azure AD Privileged Identity Management.

1. Wählen Sie **Meine Anforderungen** aus.

1. Wählen Sie für die Rolle, für die Sie eine ausstehende Anforderung abbrechen möchten, den Link **Abbrechen** aus.

    Durch das Auswählen von „Abbrechen“ wird die Anforderung abgebrochen. Um die Rolle erneut zu aktivieren, müssen Sie eine neue Anforderung zur Aktivierung übermitteln.

   ![Liste „Meine Anforderungen“ mit hervorgehobener Aktion „Abbrechen“](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>Problembehandlung

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Keine Gewährung von Berechtigungen nach der Aktivierung einer Rolle

Wenn Sie in Privileged Identity Management eine Rolle aktivieren, wird die Aktivierung möglicherweise nicht sofort an alle Portale weitergegeben, für die diese privilegierte Rolle benötigt wird. In einigen Fällen kann die Web-Zwischenspeicherung im Portal auch dann dazu führen, dass die Änderung nicht sofort wirksam ist, wenn diese weitergegeben wurde. Falls die Aktivierung verzögert ist, sollten Sie Folgendes tun.

1. Melden Sie sich vom Azure-Portal ab und dann wieder an.
1. Überprüfen Sie in Privileged Identity Management, ob Sie als Mitglied der Rolle aufgeführt sind.

## <a name="next-steps"></a>Nächste Schritte

- [Verlängern oder Erneuern von Azure-Ressourcenrollen in Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [Aktivieren meiner Azure-Ressourcenrollen in Privileged Identity Management](pim-how-to-activate-role.md)
