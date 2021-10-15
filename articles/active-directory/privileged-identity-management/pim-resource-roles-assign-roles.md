---
title: Zuweisen von Azure-Ressourcenrollen in Privileged Identity Management – Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure-Ressourcenrollen in Azure AD Privileged Identity Management (PIM) zuweisen.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
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
ms.openlocfilehash: 0837fc83bec560f12c31da1d9f0001b5bdd7768f
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669927"
---
# <a name="assign-azure-resource-roles-in-privileged-identity-management"></a>Zuweisen von Azure-Ressourcenrollen in Privileged Identity Management

Azure Active Directory Privileged Identity Management (Azure AD PIM) kann sowohl die integrierten Azure-Ressourcenrollen als auch benutzerdefinierte Rollen verwalten. Beispiele:

- Besitzer
- Benutzerzugriffsadministrator
- Mitwirkender
- Sicherheitsadministrator
- Sicherheits-Manager

> [!NOTE]
> Benutzer oder Mitglieder von Gruppen, die der Rolle „Besitzer“ oder „Benutzerzugriffsadministrator“ für das Abonnement zugewiesen sind, sowie globale Azure AD-Administratoren, die die Abonnementverwaltung in Azure AD ermöglichen, haben standardmäßig Ressourcenadministratorberechtigungen. Diese Administratoren können Rollen zuweisen, Rolleneinstellungen konfigurieren und den Zugriff mithilfe von Privileged Identity Management für Azure-Ressourcen überprüfen. Ohne Ressourcenadministratorberechtigung kann ein Benutzer in Privileged Identity Management keine Ressourcen verwalten. Zeigen Sie die Liste der [integrierten Azure-Rollen](../../role-based-access-control/built-in-roles.md) an.

Für Privileged Identity Management werden sowohl integrierte als auch benutzerdefinierte Azure-Rollen unterstützt. Weitere Informationen zu benutzerdefinierten Azure-Rollen finden Sie in [diesem Artikel](../../role-based-access-control/custom-roles.md).

## <a name="role-assignment-conditions"></a>Bedingungen für Rollenzuweisungen

Sie können die Vorschauversion der attributbasierten Zugriffssteuerung von Azure (Azure ABAC) verwenden, um mithilfe von Privileged Identity Management (PIM) Ressourcenbedingungen für berechtigte Rollenzuweisungen zu platzieren. Mit PIM müssen Ihre Endbenutzer eine Rollenzuweisung vom Typ „berechtigt“ aktivieren, um die Berechtigung zum Ausführen bestimmter Aktionen zu erhalten. Mithilfe der Bedingungen für die attributbasierte Zugriffssteuerung von Azure in PIM können Sie nicht nur die Rollenberechtigungen von Benutzer*innen für eine Ressource mit differenzierten Bedingungen einschränken, sondern mithilfe von PIM auch die Rollenzuweisung mit einer zeitgebundenen Einstellung, einem Genehmigungsworkflow, einem Überwachungspfad usw. schützen. Weitere Informationen finden Sie unter [Attributbasierte Zugriffssteuerung von Azure (öffentliche Vorschau)](../../role-based-access-control/conditions-overview.md).

## <a name="assign-a-role"></a>Zuweisen einer Rolle

Führen Sie folgende Schritte aus, um einen Benutzer für eine Azure AD-Ressourcenrolle als „berechtigt“ festzulegen.

1. Melden Sie sich mit den Rollenberechtigungen „Besitzer“ oder „Benutzerzugriffsadministrator“ beim [Azure-Portal](https://portal.azure.com/) an.

1. Öffnen Sie **Azure AD Privileged Identity Management**.

1. Wählen Sie **Azure-Ressourcen** aus.

1. Verwenden Sie den Ressourcenfilter, um nach den gewünschten verwalteten Ressourcen zu suchen.

    ![Liste der zu verwaltenden Azure-Ressourcen](./media/pim-resource-roles-assign-roles/resources-list.png)

1. Wählen Sie die zu verwaltende Ressource aus, um die Übersichtsseite der Ressource zu öffnen.

1. Wählen Sie unter **Verwalten** den Eintrag **Rollen** aus, um die Liste der Rollen für Azure-Ressourcen anzuzeigen.

    ![Azure-Ressourcenrollen](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. Wählen Sie **Zuweisungen hinzufügen** aus, um den Bereich **Zuweisungen hinzufügen** zu öffnen.

1. Wählen Sie **Rolle auswählen** aus, um die Seite **Rolle auswählen** zu öffnen.

    ![Bereich „Neue Zuweisung“](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. Wählen Sie die Rolle aus, die Sie zuweisen möchten, und klicken Sie dann auf **Auswählen**.

    Der Bereich **Mitglied oder Gruppe auswählen** wird geöffnet.

1. Wählen Sie zuerst ein Mitglied oder eine Gruppe, dem bzw. der die Rolle zugewiesen werden soll, und dann **Auswählen** aus.

    ![Bereich „Mitglied oder Gruppe auswählen“](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

1. Wählen Sie auf der Registerkarte **Einstellungen** in der Liste **Zuweisungstyp** entweder **Berechtigt** oder **Aktiv** aus.

    ![Bereich „Mitgliedschaftseinstellungen“](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    Privileged Identity Management für Azure-Ressourcen verfügt über zwei unterschiedliche Zuweisungstypen:

    - Für **berechtigte** Zuweisungen muss das Mitglied der Rolle eine Aktion durchführen, um die Rolle verwenden zu können. Beispiele für Aktionen sind eine erfolgreiche Multi-Factor Authentication-Überprüfung (MFA), die Angabe einer geschäftlichen Begründung oder das Anfordern einer Genehmigung von den angegebenen genehmigenden Personen.

    - Für **aktive** Zuweisungen ist es nicht erforderlich, dass das Mitglied eine Aktion durchführt, um die Rolle nutzen zu können. Für Mitglieder, die als „Aktiv“ zugewiesen sind, sind die Berechtigungen immer der Rolle zugewiesen.

1. Wenn Sie eine bestimmte Zuweisungsdauer angeben möchten, ändern Sie das Start- und Enddatum sowie die Zeitfelder.

1. Klicken Sie abschließend auf **Zuweisen**.

1. Nachdem die neue Rollenzuweisung erstellt wurde, wird eine Statusbenachrichtigung angezeigt.

    ![Neue Zuweisung: Benachrichtigung](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="assign-a-role-using-arm-api"></a>Zuweisen einer Rolle mit der ARM-API

Privileged Identity Management unterstützt ARM-API-Befehle (Azure Resource Manager) zum Verwalten von Azure-Ressourcenrollen, wie in der [ARM-API-Referenz von PIM](/rest/api/authorization/roleeligibilityschedulerequests) dokumentiert. Informationen zu den Berechtigungen, die für die Verwendung der PIM-API erforderlich sind, finden Sie unter [Verstehen der Privileged Identity Management-APIs](pim-apis.md).

Im Folgenden finden Sie eine Beispiel-HTTP-Anforderung zum Erstellen einer berechtigten Zuweisung für eine Azure-Rolle.

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
    "requestType": "AdminAssign",
    "scheduleInfo": {
      "startDateTime": "2020-09-09T21:31:27.91Z",
      "expiration": {
        "type": "AfterDuration",
        "endDateTime": null,
        "duration": "P365D"
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
    "targetRoleEligibilityScheduleId": "b1477448-2cc6-4ceb-93b4-54a202a89413",
    "targetRoleEligibilityScheduleInstanceId": null,
    "scope": "/providers/Microsoft.Subscription/subscriptions/dfa2a084-766f-4003-8ae1-c4aeb893a99f",
    "roleDefinitionId": "/subscriptions/dfa2a084-766f-4003-8ae1-c4aeb893a99f/providers/Microsoft.Authorization/roleDefinitions/c8d4ff99-41c3-41a8-9f60-21dfdad59608",
    "principalId": "a3bb8764-cb92-4276-9d2a-ca1e895e55ea",
    "principalType": "User",
    "requestType": "AdminAssign",
    "status": "Provisioned",
    "approvalId": null,
    "scheduleInfo": {
      "startDateTime": "2020-09-09T21:31:27.91Z",
      "expiration": {
        "type": "AfterDuration",
        "endDateTime": null,
        "duration": "P365D"
      }
    },
    "ticketInfo": {
      "ticketNumber": null,
      "ticketSystem": null
    },
    "justification": null,
    "requestorId": "a3bb8764-cb92-4276-9d2a-ca1e895e55ea",
    "createdOn": "2020-09-09T21:32:27.91Z",
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
  "name": "64caffb6-55c0-4deb-a585-68e948ea1ad6",
  "id": "/providers/Microsoft.Subscription/subscriptions/dfa2a084-766f-4003-8ae1-c4aeb893a99f/providers/Microsoft.Authorization/RoleEligibilityScheduleRequests/64caffb6-55c0-4deb-a585-68e948ea1ad6",
  "type": "Microsoft.Authorization/RoleEligibilityScheduleRequests"
}
````

## <a name="update-or-remove-an-existing-role-assignment"></a>Aktualisieren oder Entfernen einer vorhandenen Rollenzuweisung

Befolgen Sie diese Anweisungen zum Aktualisieren oder Entfernen einer vorhandenen Rollenzuweisung.

1. Öffnen Sie **Azure AD Privileged Identity Management**.

1. Wählen Sie **Azure-Ressourcen** aus.

1. Wählen Sie die zu verwaltende Ressource aus, um die Übersichtsseite zu öffnen.

1. Wählen Sie unter **Verwalten** den Eintrag **Rollen** aus, um die Liste der Rollen für Azure-Ressourcen anzuzeigen.

    ![Azure-Ressourcenrollen: Rolle auswählen](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. Wählen Sie die Rolle aus, die Sie aktualisieren oder entfernen möchten.

1. Suchen Sie die Rollenzuweisung auf den Registerkarten **Berechtigte Rollen** oder **Aktive Rollen**.

    ![Aktualisieren oder Entfernen der Rollenzuweisung](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. Wenn Sie eine Bedingung hinzufügen oder aktualisieren möchten, um den Zugriff auf Azure-Ressourcen zu verfeinern, wählen Sie in der Spalte **Bedingung** für die Rollenzuweisung **Hinzufügen** oder **Anzeigen/Bearbeiten** aus. Derzeit werden in Privileged Identity Management (PIM) im Rahmen der [Attributbasierten Zugriffssteuerung von Azure (öffentliche Vorschau)](../../role-based-access-control/conditions-overview.md) nur drei Rollen unterstützt: Besitzer von Speicherblobdaten, Leser von Speicherblobdaten und Mitwirkender an Speicherblobdaten.

    ![Aktualisieren oder Entfernen von Attributen für die Zugriffssteuerung](./media/pim-resource-roles-assign-roles/resources-abac-update-remove.png)

1. Wählen Sie **Aktualisieren** oder **Entfernen** aus, um die Rollenzuweisung zu aktualisieren oder zu entfernen.

    Informationen zum Erweitern einer Rollenzuweisung finden Sie unter [Verlängern oder Erneuern von Azure-Ressourcenrollen in Privileged Identity Management](pim-resource-roles-renew-extend.md).

## <a name="next-steps"></a>Nächste Schritte

- [Verlängern oder Erneuern von Azure-Ressourcenrollen in Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [Konfigurieren von Einstellungen für Azure-Ressourcenrollen in Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Zuweisen von Azure AD-Rollen in PIM](pim-how-to-add-role-to-user.md)
