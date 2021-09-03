---
title: Konfigurieren von Einstellungen für Azure AD-Rollen in PIM – Azure AD | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Einstellungen für Azure AD-Rollen in Azure AD Privileged Identity Management (PIM) konfigurieren.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 07/27/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f843aebfea4e7c5b956d5e150c4e54dffa764a5c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122349404"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>Konfigurieren von Einstellungen für Azure AD-Rollen in PIM

Ein Administrator für privilegierte Rollen kann Privileged Identity Management (PIM) in seiner Azure AD-Organisation (Azure Active Directory) anpassen und dabei auch die Art und Weise ändern, in der ein Benutzer eine berechtigte Rollenzuweisung aktiviert.

## <a name="open-role-settings"></a>Öffnen von Rolleneinstellungen

Führen Sie die folgenden Schritte aus, um die Einstellungen für eine Azure AD-Rolle zu öffnen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) als Benutzer mit der Rolle [Administrator für privilegierte Rollen](../roles/permissions-reference.md#privileged-role-administrator) an.

1. Öffnen Sie **Azure AD Privileged Identity Management** &gt; **Azure AD-Rollen** &gt; **Rolleneinstellungen**.

    ![Seite „Rolleneinstellungen“ mit einer Auflistung der Azure AD-Rollen](./media/pim-how-to-change-default-settings/role-settings.png)

1. Wählen Sie die Rolle aus, deren Einstellungen Sie konfigurieren möchten.

    ![Seite „Details zur Rolleneinstellung“ mit einer Auflistung mehrerer Zuweisungs- und Aktivierungseinstellungen](./media/pim-how-to-change-default-settings/role-settings-page.png)

1. Klicken Sie auf **Bearbeiten**, um die Seite „Rolleneinstellungen“ zu öffnen.

    ![Seite „Rolleneinstellungen“ mit Optionen zum Aktualisieren von Zuweisungs- und Aktivierungseinstellungen](./media/pim-how-to-change-default-settings/role-settings-edit.png)

    Im Bereich „Rolleneinstellungen“ für jede Rolle können Sie verschiedene Einstellungen konfigurieren.

## <a name="assignment-duration"></a>Zuweisungsdauer

Bei der Konfiguration von Einstellungen für eine Rolle können Sie für jeden Zuweisungstyp („Berechtigt“ und „Aktiv“) zwischen zwei Optionen für die Zuweisungsdauer wählen. Diese Optionen werden zur maximalen Standarddauer, wenn ein Benutzer der Rolle in Privileged Identity Management zugewiesen wird.

Sie können beim Typ **Berechtigt** eine dieser Optionen für die Zuweisungsdauer wählen:

| Einstellung | BESCHREIBUNG |
| --- | --- |
| Allow permanent eligible assignment (Dauerhafte berechtigte Zuweisung zulassen) | Globale Administratoren und Administratoren mit einer privilegierten Rolle können dauerhafte berechtigte Zuweisungen zuweisen. |
| Berechtigte Zuweisungen laufen ab nach | Globale Administratoren und Administratoren mit einer privilegierten Rolle können anfordern, dass alle berechtigten Zuweisungen ein bestimmtes Start- und Enddatum haben. |

Beim Typ **Aktiv** können Sie eine dieser Optionen für die Zuweisungsdauer wählen:

| Einstellung | BESCHREIBUNG |
| --- | --- |
| Allow permanent active assignment (Dauerhafte aktive Zuweisung zulassen) | Globale Administratoren und Administratoren mit einer privilegierten Rolle können dauerhafte aktive Zuweisungen zuweisen. |
| Aktive Zuweisungen laufen ab nach | Globale Administratoren und Administratoren mit einer privilegierten Rolle können anfordern, dass alle aktiven Zuweisungen ein bestimmtes Start- und Enddatum haben. |

> [!NOTE]
> Alle Zuweisungen mit einem angegebenen Enddatum können von globalen Administratoren und Administratoren mit einer privilegierten Rolle erneuert werden. Zudem können Benutzer Self-Service-Anforderungen auslösen, um [Rollenzuweisungen zu verlängern oder zu erneuern](pim-resource-roles-renew-extend.md).

## <a name="require-multifactor-authentication"></a>Erzwingen der mehrstufigen Authentifizierung

Privileged Identity Management ermöglicht die Erzwingung von mehrstufiger Azure AD-Authentifizierung (Azure AD Multi-Factor Authentication, MFA) bei Aktivierung und bei aktiver Zuweisung.

### <a name="on-activation"></a>Bei Aktivierung

Sie können erzwingen, dass Benutzer, die für eine Rolle berechtigt sind, vor der Aktivierung ihre Identität über Azure AD MFA bestätigen müssen. Mithilfe von mehrstufiger Authentifizierung kann mit hoher Wahrscheinlichkeit sichergestellt werden, dass es sich auch wirklich um den jeweiligen Benutzer handelt. Durch die Erzwingung dieser Option werden wichtige Ressourcen in Situationen geschützt, in denen das Benutzerkonto unter Umständen kompromittiert wurde.

Wenn Sie mehrstufige Authentifizierung zum Aktivieren der Rollenzuweisung anfordern möchten, wählen Sie auf der Registerkarte „Aktivierung“ von **Rolleneinstellung bearbeiten** die Option **On activation, require Azure MFA** (Bei Aktivierung Azure MFA anfordern) aus.

### <a name="on-active-assignment"></a>Bei aktiver Zuweisung

Es kann vorkommen, dass Sie einen Benutzer einer Rolle für einen kurzen Zeitraum (z. B. einen Tag) zuweisen möchten. In diesem Fall müssen die zugewiesenen Benutzer die Aktivierung nicht anfordern. In diesem Szenario kann Privileged Identity Management die mehrstufige Authentifizierung nicht erzwingen, wenn der Benutzer seine Rollenzuweisung verwendet, weil die Rolle ab dem Zeitpunkt der Zuweisung bereits aktiv ist.

Wenn Sie mehrstufige Authentifizierung bei aktiver Zuweisung anfordern möchten, wählen Sie auf der Registerkarte „Zuweisung“ von **Rolleneinstellung bearbeiten** die Option **Require Azure Multi-Factor Authentication on active assignment** (Multi-Factor Authentication bei aktiver Zuweisung anfordern) aus.

Weitere Informationen finden Sie unter [Mehrstufige Authentifizierung und Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Maximale Aktivierungsdauer

Mit dem Schieberegler **Maximale Aktivierungsdauer** geben Sie die maximale Zeit in Stunden an, die eine Rolle aktiv bleibt, bevor sie abläuft. Dieser Wert kann zwischen einer und 24 Stunden betragen.

## <a name="require-justification"></a>Verlangen einer Begründung

Sie können verlangen, dass Benutzer bei der Aktivierung eine geschäftliche Begründung angeben müssen. Um eine Begründung zu verlangen, aktivieren Sie das Kontrollkästchen **Begründung für aktive Zuweisung erforderlich** oder **Begründung für Aktivierung erforderlich**.

## <a name="require-approval-to-activate"></a>Erzwingen der Genehmigung für die Aktivierung

Wenn Sie mehrere genehmigende Personen festlegen, ist der Genehmigungsvorgang abgeschlossen, sobald eine dieser Personen die Genehmigung gewährt oder verweigert hat. Sie können keine Genehmigung von mindestens zwei Benutzern anfordern. Wenn Sie für die Aktivierung einer Rolle eine Genehmigung anfordern möchten, gehen Sie wie folgt vor.

1. Aktivieren Sie das Kontrollkästchen **Genehmigung zum Aktivieren anfordern**.

1. Wählen Sie **Genehmigende Personen auswählen** aus.

    ![Bereich „Mitglied oder Gruppe auswählen“ zum Auswählen von genehmigenden Personen](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Wählen Sie mindestens einen Benutzer aus, und klicken Sie dann auf **Auswählen**. Wählen Sie mindestens eine genehmigende Person aus. Wenn keine bestimmten genehmigenden Personen ausgewählt sind, werden Administratoren/globale Administratoren mit privilegierten Rollen standardmäßig als genehmigende Personen festgelegt.

    Ihre Auswahl wird in der Liste der ausgewählten genehmigenden Personen angezeigt.

1. Wenn Sie alle gewünschten Rolleneinstellungen angegeben haben, klicken Sie auf **Aktualisieren**, um Ihre Änderungen zu speichern.

## <a name="next-steps"></a>Nächste Schritte

- [Zuweisen von Azure AD-Rollen in PIM](pim-how-to-add-role-to-user.md)
- [Konfigurieren von Sicherheitswarnungen für Azure-Ressourcenrollen in Privileged Identity Management (PIM)](pim-how-to-configure-security-alerts.md)
