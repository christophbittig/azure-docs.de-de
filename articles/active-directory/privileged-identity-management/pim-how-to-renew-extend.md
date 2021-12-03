---
title: Erneuern von Azure AD-Rollenzuweisungen in PIM – Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure Active Directory-Rollenzuweisungen in Azure AD Privileged Identity Management (PIM) verlängern oder erneuern.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 10/19/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51385ce98da9e163be0d70d0c0662f51094094a3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130222512"
---
# <a name="extend-or-renew-azure-ad-role-assignments-in-privileged-identity-management"></a>Verlängern oder Erneuern von Azure AD-Rollenzuweisungen in Privileged Identity Management

In Azure Active Directory (Azure AD) Privileged Identity Management (PIM) werden neue Steuerelemente zum Verwalten des Zugriffs und des Zuweisungslebenszyklus für Azure AD-Rollen bereitgestellt. Administratoren können Rollen zuweisen, indem sie Eigenschaften für die Start- und Endzeit verwenden. Wenn das Ende der Zuweisung fast erreicht ist, sendet Privileged Identity Management E-Mail-Benachrichtigungen an die betroffenen Benutzer oder Gruppen. Außerdem werden E-Mail-Benachrichtigungen an Azure AD-Administratoren gesendet, um einen entsprechenden Zugriff zu gewährleisten. Zuweisungen können auch verlängert werden. Sie bleiben im abgelaufenen Zustand noch 30 Tage lang sichtbar, auch wenn der Zugriff nicht verlängert wird.

## <a name="who-can-extend-and-renew"></a>Wer kann das Verlängern und Erneuern durchführen?

Nur globale Administratoren oder Administratoren für privilegierte Rollen können Azure AD-Rollenzuweisungen verlängern oder erneuern. Der betroffene Benutzer bzw. die betroffene Gruppe kann anfordern, dass in Kürze ablaufende Rollen verlängert und dass bereits abgelaufene Rollen erneuert werden.

## <a name="when-are-notifications-sent"></a>Wann werden die Benachrichtigungen gesendet?

Privileged Identity Management sendet E-Mail-Benachrichtigungen an Administratoren und betroffene Benutzer und Gruppen mit Rollen, die innerhalb der nächsten 14 Tage ablaufen. Einen Tag vor dem Ablauf wird eine weitere Benachrichtigung gesendet. Wenn eine Zuweisung offiziell abgelaufen ist, wird eine weitere E-Mail gesendet.

Administratoren erhalten Benachrichtigungen, wenn ein Benutzer oder eine Gruppe mit einer ablaufenden oder abgelaufenen Rolle die Verlängerung bzw. Erneuerung anfordert. Wenn ein Administrator eine Anforderung als „genehmigt“ oder „abgelehnt“ auflöst, werden alle anderen Administratoren über die Entscheidung benachrichtigt. Anschließend wird der anfordernde Benutzer bzw. die Gruppe ebenfalls über die Entscheidung informiert.

## <a name="extend-role-assignments"></a>Verlängern von Rollenzuweisungen

Die folgenden Schritte beschreiben den Prozess für das Anfordern, Bearbeiten bzw. Verwalten einer Verlängerung oder Erneuerung einer Rollenzuweisung.

### <a name="self-extend-expiring-assignments"></a>Selbständiges Verlängern von ablaufenden Zuweisungen

Benutzer*innen, die einer Rolle zugewiesen sind, können ablaufende Rollenzuweisungen direkt über die Registerkarten **Berechtigt** oder **Aktiv** auf der Seite **Meine Rollen** verlängern, entweder unter **Azure AD-Rollen** oder über die Seite **Meine Rollen** der obersten Ebene im Privileged Identity Management-Portal. Im Portal können Benutzer*innen die Verlängerung von berechtigten oder aktiven (zugewiesenen) Rollen anfordern, die in den nächsten 14 Tagen ablaufen.

![Azure AD-Rollen – Seite „Meine Rollen“, auf der die berechtigten Rollen mit der Spalte „Aktion“ aufgelistet werden](./media/pim-how-to-renew-extend/pim-extend-link-in-portal.png)

Wenn Enddatum und -uhrzeit der Zuweisung innerhalb von 14 Tagen liegen, wird die Schaltfläche **Verlängern** auf der Benutzeroberfläche zu einem aktiven Link. Im folgenden Beispiel wird angenommen, dass der 27. März das aktuelle Datum ist.

>[!Note]
>Für eine Gruppe, die einer Rolle zugewiesen ist, steht der Link **Verlängern** nie zur Verfügung, damit Benutzer*innen mit geerbten Zuweisungen nicht die Gruppenzuweisung verlängern können.

![Spalte „Aktion“ mit Links zum Aktivieren oder Verlängern](./media/pim-how-to-renew-extend/pim-extend-within-fourteen.png)

Wählen Sie **Verlängern**, um das Anforderungsformular zu öffnen und eine Verlängerung dieser Rollenzuweisung anzufordern.

![Bereich zum Verlängern einer Rollenzuweisung mit dem Feld „Grund“](./media/pim-how-to-renew-extend/extend-role-assignment-request.png)

Geben Sie einen Grund für die Verlängerungsanforderung an, und wählen Sie **Verlängern**.

>[!NOTE]
>Wir empfehlen Ihnen, die Details zum Grund der Verlängerung einzufügen und außerdem den Verlängerungszeitraum anzugeben (falls bekannt).

Administratoren erhalten eine E-Mail-Benachrichtigung, um die Verlängerungsanforderung zu überprüfen. Falls bereits eine Verlängerungsanforderung übermittelt wurde, wird im Portal eine Benachrichtigung angezeigt.

![Benachrichtigung, in der erklärt wird, dass bereits eine ausstehende Anforderung zur Verlängerung einer Rollenzuweisung vorhanden ist](./media/pim-how-to-renew-extend/extend-notification.png)

Navigieren Sie zur Seite **Ausstehende Anforderungen**, um den Status der Anforderung anzuzeigen oder sie zu stornieren.

![Azure AD-Rollen – Seite „Ausstehende Anforderungen“ mit einer Auflistung ausstehender Anforderungen und einem Link zum Stornieren](./media/pim-how-to-renew-extend/pending-requests.png)

### <a name="admin-approved-extension"></a>Vom Administrator genehmigte Verlängerung

Wenn ein Benutzer oder eine Gruppe eine Anforderung zur Verlängerung einer Rollenzuweisung übermittelt, erhalten die Administratoren eine E-Mail-Benachrichtigung mit den Details der ursprünglichen Zuweisung und dem Grund der Anforderung. Die Benachrichtigung enthält einen direkten Link zu der Anforderung, damit der Administrator diese genehmigen oder ablehnen kann.

Zusätzlich zur Verwendung des Links aus der E-Mail können Administratoren Anforderungen genehmigen oder ablehnen, indem sie zum Privileged Identity Management-Verwaltungsportal wechseln und im linken Bereich die Option **Anforderung genehmigen** auswählen.

![Azure AD-Rollen – Seite „Anforderungen genehmigen“ mit einer Auflistung von Anforderungen und Links zum Genehmigen oder Ablehnen](./media/pim-how-to-renew-extend/extend-admin-approve-list.png)

Wenn ein Administrator die Option **Genehmigen** oder **Ablehnen** wählt, werden die Details der Anforderung zusammen mit einem Feld zum Angeben der geschäftlichen Begründung für die Überwachungsprotokolle angezeigt.

![Bereich zum Genehmigen der Rollenzuweisungsanforderung mit der Begründung des Anforderers, dem Zuweisungstyp, der Startzeit, der Endzeit und dem Grund](./media/pim-how-to-renew-extend/extend-admin-approve-form.png)

Beim Genehmigen einer Anforderung zur Verlängerung der Rollenzuweisung können Administratoren einen neuen Start- und Endzeitpunkt und einen neuen Zuweisungstyp wählen. Das Ändern des Zuweisungstyps kann erforderlich sein, wenn der Administrator begrenzten Zugriff gewähren möchte (z.B. nur für einen Tag), damit eine bestimmte Aufgabe erfüllt werden kann. In diesem Beispiel kann der Administrator die Zuweisung von **Berechtigt** in **Aktiv** ändern. Das bedeutet, dass sie dem Anfordernden Zugriff gewähren können, ohne dass er aktiviert werden muss.

### <a name="admin-initiated-extension"></a>Vom Administrator initiierte Verlängerung

Wenn ein Benutzer, der einer Rolle zugewiesen ist, keine Verlängerung für die Rollenzuweisung anfordert, kann ein Administrator eine Zuweisung auch im Namen des Benutzers verlängern. Für Verlängerungen von Rollenzuweisungen durch Administratoren sind keine Genehmigungen erforderlich, nach Verlängerung der Rolle werden jedoch Benachrichtigungen an alle anderen Administratoren gesendet.

Um eine Rollenzuweisung zu verlängern, wechseln Sie zu der Rollen- oder Zuweisungsansicht in Privileged Identity Management. Suchen Sie die Zuweisung, für die eine Verlängerung erforderlich ist. Wählen Sie dann **Verlängern** in der Aktionsspalte.

![Azure AD-Rollen – Seite „Zuweisungen“, auf der die berechtigten Rollen mit Links zum Verlängern aufgelistet werden](./media/pim-how-to-renew-extend/extend-admin-extend.png)

## <a name="extend-role-assignments-using-graph-api"></a>Erweitern von Rollenzuweisungen mithilfe der Graph-API

Erweitern Sie eine aktive Zuweisung mithilfe der Graph-API.

#### <a name="http-request"></a>HTTP-Anforderung

````HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignmentScheduleRequests 
 
{ 
    "action": "AdminExtend", 
    "justification": "abcde", 
    "roleDefinitionId": "<definition-ID-GUID>", 
    "directoryScopeId": "/", 
    `"principalId": "<principal-ID-GUID>", 
    "scheduleInfo": { 
        "startDateTime": "2021-07-15T19:15:08.941Z", 
        "expiration": { 
            "type": "AfterDuration", 
            "duration": "PT3H" 
        } 
    } 
}
````

#### <a name="http-response"></a>HTTP-Antwort

````HTTP
{ 
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#roleManagement/directory/roleAssignmentScheduleRequests/$entity", 
    "id": "<assignment-ID-GUID>", 
    "status": "Provisioned", 
    "createdDateTime": "2021-07-15T20:26:44.865248Z", 
    "completedDateTime": "2021-07-15T20:26:47.9434068Z", 
    "approvalId": null, 
    "customData": null, 
    "action": "AdminExtend", 
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
        "startDateTime": "2021-07-15T20:26:47.9434068Z", 
        "recurrence": null, 
        "expiration": { 
            "type": "afterDuration", 
            "endDateTime": null, 
            "duration": "PT3H" 
        } 
    }, 
    "ticketInfo": { 
        "ticketNumber": null, 
        "ticketSystem": null 
    } 
} 
````

## <a name="renew-role-assignments"></a>Erneuern von Rollenzuweisungen

Der Prozess zum Erneuern einer abgelaufenen Rollenzuweisung ist vom Konzept her zwar ähnlich dem Prozess zum Anfordern einer Verlängerung, es bestehen jedoch auch Unterschiede. Mit den folgenden Schritten können Zuweisungen und Administratoren den Zugriff auf abgelaufene Rollen bei Bedarf erneuern.

### <a name="self-renew"></a>Selbstverlängerung

Benutzer, die nicht mehr auf Ressourcen zugreifen können, können bis zu 30 Tage auf den Verlauf der abgelaufenen Zuweisungen zugreifen. Navigieren Sie dazu im linken Bereich zu **Meine Rollen** und wählen Sie die Registerkarte **Abgelaufene Rollen** im Abschnitt der Azure AD-Rollen.

![Seite „Meine Rollen“ – Registerkarte „Abgelaufene Rollen“](./media/pim-how-to-renew-extend/renew-from-myroles.png)

In der Liste der Rollen werden die Standardeinstellungen für **Berechtigte Rollen** angezeigt. Wählen Sie zugewiesene Rollen mit dem Status **Berechtigt** oder **Aktiv** aus.

Wählen Sie die Aktion **Erneuern**, um für Rollenzuweisungen in der Liste eine Erneuerung anzufordern. Geben Sie dann einen Grund für Ihre Anforderung an. Es ist hilfreich, zusätzlich zum weiteren Kontext oder einer geschäftlichen Begründung einen Gültigkeitszeitraum anzugeben, um dem Administrator die Entscheidung über die Genehmigung oder Ablehnung zu erleichtern.

![Bereich zum Erneuern einer Rollenzuweisung mit dem Feld „Grund“](./media/pim-how-to-renew-extend/renew-request-form.png)

Nach dem Übermitteln der Anforderung werden die Administratoren über eine ausstehende Anforderung zur Erneuerung einer Rollenzuweisung informiert.

### <a name="admin-approves"></a>Genehmigung durch Administrator

Azure AD-Administratoren können über den Link in der E-Mail-Benachrichtigung auf die Erneuerungsanforderung zugreifen, oder sie können über das Azure-Portal auf Privileged Identity Management zugreifen und in PIM die Option **Anforderungen genehmigen** wählen.

![Azure AD-Rollen – Seite „Anforderungen genehmigen“ mit einer Auflistung von Anforderungen und Links zum Genehmigen oder Ablehnen](./media/pim-how-to-renew-extend/extend-admin-approve-list.png)

Wenn ein Administrator die Option **Genehmigen** oder **Ablehnen** wählt, werden die Details der Anforderung zusammen mit einem Feld zum Angeben der geschäftlichen Begründung für die Überwachungsprotokolle angezeigt.

![Bereich zum Genehmigen der Rollenzuweisungsanforderung mit der Begründung des Anforderers, dem Zuweisungstyp, der Startzeit, der Endzeit und dem Grund](./media/pim-how-to-renew-extend/extend-admin-approve-form.png)

Beim Genehmigen einer Anforderung zur Erneuerung der Rollenzuweisung müssen Administratoren einen neuen Start- und Endzeitpunkt und einen neuen Zuweisungstyp eingeben.

### <a name="admin-renew"></a>Erneuerung durch Administrator

Sie können darüber hinaus abgelaufene Rollenzuweisungen über die Registerkarte **Abgelaufene Rollen** einer Azure AD-Rolle erneuern. Um eine Liste mit allen abgelaufenen Rollenzuweisungen anzuzeigen, wählen Sie auf dem Bildschirm **Zuweisungen** die Option **Abgelaufenen Rollen**.

![Azure AD-Rollen – Seite „Zuweisungen“, auf der die abgelaufenen Rollen mit Links zum Erneuern aufgelistet werden](./media/pim-how-to-renew-extend/renew-from-assignments-pane.png)

## <a name="next-steps"></a>Nächste Schritte

- [Genehmigen oder Ablehnen von Anforderungen für Azure AD-Rollen in PIM](azure-ad-pim-approval-workflow.md)
- [Konfigurieren von Einstellungen für Azure AD-Rollen in PIM](pim-how-to-change-default-settings.md)
