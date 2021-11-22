---
title: Konfigurieren von Einstellungen für Gruppen mit privilegiertem Zugriff in PIM – Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Einstellungen für Gruppen, denen Rollen zugewiesen werden können, in Azure AD Privileged Identity Management (PIM) konfigurieren.
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
ms.date: 11/12/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97798fdfc680d2cc644a47acc814a8fbe7e44654
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490040"
---
# <a name="configure-privileged-access-group-settings-preview-in-privileged-identity-management"></a>Konfigurieren von Einstellungen für Gruppen mit privilegiertem Zugriff (Vorschau) in Privileged Identity Management

Rolleneinstellungen sind die Standardeinstellungen, die auf Zuweisungen von privilegiertem Zugriff für Gruppenbesitzer und Gruppenmitglieder in Privileged Identity Management (PIM) angewendet werden. Führen Sie die folgenden Schritte aus, um den Genehmigungsworkflow einzurichten und anzugeben, wer Anforderungen zum Erweitern von Berechtigungen genehmigen oder ablehnen kann.

## <a name="open-role-settings"></a>Öffnen von Rolleneinstellungen

Führen Sie die folgenden Schritte aus, um die Einstellungen für die Azure-Rolle einer Gruppe mit privilegiertem Zugriff zu öffnen.

1. Melden Sie sich im [Azure-Portal](https://portal.azure.com/) mit einem Benutzer in der Rolle [Global Administrator](../roles/permissions-reference.md#global-administrator), der Rolle Privileged Role Administrator oder der Rolle Gruppenbesitzer an.

1. Öffnen Sie **Azure AD Privileged Identity Management**.

1. Wählen Sie **Privilegierter Zugriff (Vorschau)** aus.

1. Wählen Sie die Gruppe aus, die Sie verwalten möchten.

    ![Gruppen mit privilegiertem Zugriff nach Gruppenname gefiltert](./media/groups-role-settings/group-select.png)

1. Wählen Sie **Einstellungen** aus.

    ![Seite „Einstellungen“ mit einer Auflistung der Gruppeneinstellungen für die ausgewählte Gruppe](./media/groups-role-settings/group-settings-select-role.png)

1. Wählen Sie die Rolle „Besitzer“ oder „Mitglied“ aus, deren Einstellungen Sie anzeigen oder ändern möchten. Sie können die aktuellen Einstellungen für die Rolle auf der Seite **Details zur Rolleneinstellung** anzeigen.

    ![Seite „Details zur Rolleneinstellung“ mit einer Auflistung mehrerer Zuweisungs- und Aktivierungseinstellungen](./media/groups-role-settings/group-role-setting-details.png)

1. Wählen Sie **Bearbeiten** aus, um die Seite **Rolleneinstellung bearbeiten** zu öffnen. Auf der Registerkarte **Aktivierung** können Sie die Einstellungen für die Rollenaktivierung ändern und festlegen, ob dauerhaft berechtigte und aktive Zuweisungen zulässig sind.

    ![Seite „Rolleneinstellungen bearbeiten“ mit geöffneter Registerkarte „Aktivierung“](./media/groups-role-settings/role-settings-activation-tab.png)

1. Wählen Sie die Registerkarte **Zuweisung** aus, um die Registerkarte mit den Zuweisungseinstellungen zu öffnen. Diese Einstellungen steuern die Zuweisungseinstellungen für diese Rolle in Privileged Identity Management.

    ![Registerkarte „Rollenzuweisung“ auf der Seite mit den Rolleneinstellungen](./media/groups-role-settings/role-settings-assignment-tab.png)

1. Über die Registerkarte **Benachrichtigung** oder unten auf der Seite über die Schaltfläche **Weiter: Aktivierung** gelangen Sie zur Registerkarte für die Benachrichtigungseinstellungen für diese Rolle. Diese Einstellungen steuern alle E-Mail-Benachrichtigungen, die mit dieser Rolle verknüpft sind.

    ![Registerkarte für Rollenbenachrichtigungen auf der Seite „Rolleneinstellungen“](./media/groups-role-settings/role-settings-notification-tab.png)

1. Sie können jederzeit auf die Schaltfläche **Aktualisieren** klicken, um die Rolleneinstellungen zu aktualisieren.

Auf der Registerkarte **Benachrichtigungen** der Rolleneinstellungenseite kann mit Privileged Identity Management differenziert gesteuert werden, wer welche Benachrichtigungen empfängt.

- **Deaktivieren einer E-Mail**<br>Sie können bestimmte E-Mails deaktivieren, indem Sie das Standardempfänger-Kontrollkästchen deaktivieren und alle weiteren Empfänger löschen.  
- **E-Mails auf angegebene E-Mail-Adressen beschränken**<br>Sie können an Standardempfänger gesendete E-Mails deaktivieren, indem Sie das Standardempfänger-Kontrollkästchen deaktivieren. Sie können dann andere E-Mail-Adressen als Empfänger hinzufügen. Wenn Sie mehrere E-Mail-Adressen hinzufügen möchten, trennen Sie diese durch ein Semikolon (;).
- **E-Mails sowohl an Standardempfänger als auch weitere Empfänger senden**<br>Sie können E-Mails sowohl an Standardempfänger als auch an weitere Empfänger senden, indem Sie das Standardempfänger-Kontrollkästchen aktivieren und E-Mail-Adressen für weitere Empfänger hinzufügen.
- **Nur kritische E-Mails**<br>Um nur kritische E-Mails zu erhalten, können Sie für jeden E-Mail-Typ das Kontrollkästchen aktivieren. Dies bedeutet, dass Privileged Identity Management nur dann weiterhin E-Mails an die angegebenen Empfänger sendet, wenn die E-Mail eine sofortige Aktion erfordert. Beispielsweise werden E-Mails, die Benutzer zur Erweiterung ihrer Rollenzuweisung auffordern, nicht ausgelöst, während E-Mails, die Administratoren zum Genehmigen einer Erweiterungsanforderung auffordern, ausgelöst werden.

## <a name="assignment-duration"></a>Zuweisungsdauer

Bei der Konfiguration von Einstellungen für eine Rolle können Sie für jeden Zuweisungstyp („Berechtigt“ und „Aktiv“) zwischen zwei Optionen für die Zuweisungsdauer wählen. Diese Optionen werden zur maximalen Standarddauer, wenn ein Benutzer der Rolle in Privileged Identity Management zugewiesen wird.

Sie können beim Typ **Berechtigt** eine dieser Optionen für die Zuweisungsdauer wählen:

| | BESCHREIBUNG |
| --- | --- |
| **Dauerhafte berechtigte Zuweisung zulassen** | Ressourcenadministratoren können dauerhaft berechtigte Zuweisungen veranlassen. |
| **Berechtigte Zuweisungen laufen ab nach** | Ressourcenadministratoren können verlangen, dass alle berechtigten Zuweisungen ein bestimmtes Start- und Enddatum haben. |

Beim Typ **Aktiv** können Sie eine dieser Optionen für die Zuweisungsdauer wählen:

| | BESCHREIBUNG |
| --- | --- |
| **Permanente aktive Zuweisung zulassen** | Ressourcenadministratoren können dauerhaft aktive Zuweisungen veranlassen. |
| **Aktive Zuweisungen laufen ab nach** | Ressourcenadministratoren können verlangen, dass alle aktiven Zuweisungen ein bestimmtes Start- und Enddatum haben. |

> [!NOTE]
> Alle Zuweisungen mit einem angegebenen Enddatum können von Ressourcenadministratoren erneuert werden. Zudem können Benutzer Self-Service-Anforderungen auslösen, um [Rollenzuweisungen zu verlängern oder zu erneuern](pim-resource-roles-renew-extend.md).

## <a name="require-multifactor-authentication"></a>Erzwingen der mehrstufigen Authentifizierung

Privileged Identity Management ermöglicht die optionale Erzwingung der Azure AD Multi-Factor Authentication (MFA) für zwei bestimmte Szenarien.

### <a name="require-multifactor-authentication-on-active-assignment"></a>Multi-Factor Authentication bei aktiver Zuweisung erforderlich

Diese Option erfordert, dass Administratoren die mehrstufige Authentifizierung abschließen müssen, bevor sie eine aktive (im Gegensatz zu einer berechtigten) Rollenzuweisung erstellen. Privileged Identity Management kann die mehrstufige Authentifizierung nicht erzwingen, wenn Benutzer ihre Rollenzuweisung verwenden, weil die Rolle ab dem Zeitpunkt der Zuweisung bereits aktiv ist.

Aktivieren Sie das Kontrollkästchen **Multi-Factor Authentication bei aktiver Zuweisung erforderlich**, um die mehrstufige Authentifizierung beim Erstellen einer aktiven Rollenzuweisung als erforderlich festzulegen.

### <a name="require-multifactor-authentication-on-activation"></a>Bei Aktivierung Multi-Factor Authentication anfordern

Sie können erzwingen, dass Benutzer, die für eine Rolle berechtigt sind, vor der Aktivierung ihre Identität über Azure AD MFA bestätigen müssen. Mithilfe von mehrstufiger Authentifizierung kann mit hoher Wahrscheinlichkeit sichergestellt werden, dass es sich auch wirklich um den jeweiligen Benutzer handelt. Durch die Erzwingung dieser Option werden wichtige Ressourcen in Situationen geschützt, in denen das Benutzerkonto unter Umständen kompromittiert wurde.

Damit vor der Aktivierung die mehrstufige Authentifizierung erzwungen wird, müssen Sie das Kontrollkästchen **Bei Aktivierung Multi-Factor Authentication anfordern** aktivieren.

Weitere Informationen finden Sie unter [Mehrstufige Authentifizierung und Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Maximale Aktivierungsdauer

Mit dem Schieberegler **Maximale Aktivierungsdauer** geben Sie die maximale Zeit in Stunden an, die eine Aktivierungsanforderung für eine Rolle aktiv bleibt, bevor sie abläuft. Dieser Wert kann zwischen einer und 24 Stunden betragen.

## <a name="require-justification"></a>Verlangen einer Begründung

Sie können verlangen, dass Benutzer bei der Aktivierung eine geschäftliche Begründung angeben müssen. Um eine Begründung zu verlangen, aktivieren Sie das Kontrollkästchen **Begründung für aktive Zuweisung erforderlich** oder **Begründung für Aktivierung erforderlich**.

## <a name="require-approval-to-activate"></a>Erzwingen der Genehmigung für die Aktivierung

Wenn Sie für die Aktivierung einer Rolle eine Genehmigung anfordern möchten, gehen Sie wie folgt vor.

1. Aktivieren Sie das Kontrollkästchen **Genehmigung zum Aktivieren anfordern**.

1. Klicken Sie auf **Genehmigende Personen auswählen**, um die Seite **Mitglied oder Gruppe auswählen** zu öffnen.

    ![Bereich „Mitglied oder Gruppe auswählen“ zum Auswählen von genehmigenden Personen](./media/groups-role-settings/group-settings-select-approvers.png)

1. Wählen Sie mindestens einen Benutzer oder eine Gruppe aus, und klicken Sie dann auf **Auswählen**. Sie können eine beliebige Kombination von Benutzern und Gruppen hinzufügen. Sie müssen mindestens eine genehmigende Person auswählen. Für genehmigende Personen gibt es keine Standardeinstellung.

    Ihre Auswahl wird in der Liste der ausgewählten genehmigenden Personen angezeigt.

1. Wenn Sie alle gewünschten Rolleneinstellungen angegeben haben, klicken Sie auf **Aktualisieren**, um Ihre Änderungen zu speichern.

## <a name="next-steps"></a>Nächste Schritte

- [Zuweisen von Mitgliedschaft oder Besitz einer Gruppe mit privilegiertem Zugriff in PIM](groups-assign-member-owner.md)
