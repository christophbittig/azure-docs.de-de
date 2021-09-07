---
title: Zuweisen von Azure AD-Rollen in PIM – Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure AD-Rollen in Azure AD Privileged Identity Management (PIM) zuweisen.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 06/03/2021
ms.author: curtand
ms.collection: M365-identity-device-management
ms.custom: subject-rbac-steps
ms.openlocfilehash: a741ce7fff528fbe1f4120f4138a88d7b6e2e915
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/16/2021
ms.locfileid: "112233005"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>Zuweisen von Azure AD-Rollen in Privileged Identity Management

Mit Azure Active Directory (Azure AD) kann ein globaler Administrator **dauerhafte** Rollenzuweisungen für Azure AD-Administratoren vornehmen. Diese Rollenzuweisungen können über das [Azure-Portal](../roles/permissions-reference.md) oder über [PowerShell-Befehle](/powershell/module/azuread#directory_roles) erstellt werden.

Der Dienst Azure AD Privileged Identity Management (PIM) ermöglicht es auch Administratoren für privilegierte Rollen, dauerhafte Administratorrollen zuzuweisen. Außerdem können Administratoren für privilegierte Rollen Benutzer als für Azure AD-Administratorrollen **berechtigt** festlegen. Ein berechtigter Administrator kann die Rolle bei Bedarf aktivieren, und die entsprechenden Berechtigungen laufen nach einem bestimmten Zeitraum ab.

Für Privileged Identity Management werden sowohl integrierte als auch benutzerdefinierte Azure AD-Rollen unterstützt. Weitere Informationen zu benutzerdefinierten Azure AD-Rollen finden Sie unter [Übersicht über die rollenbasierte Zugriffssteuerung in Azure Active Directory](../roles/custom-overview.md).

## <a name="assign-a-role"></a>Zuweisen einer Rolle

Führen Sie folgende Schritte aus, um einen Benutzer als für eine Azure AD-Administratorrolle „berechtigt“ festzulegen.

1. Melden Sie sich im [Azure-Portal](https://portal.azure.com/) als Benutzer an, der ein Mitglied der Rolle [Administrator für privilegierte Rollen](../roles/permissions-reference.md#privileged-role-administrator) ist.

1. Öffnen Sie **Azure AD Privileged Identity Management**.

1. Wählen Sie **Azure AD-Rollen** aus.

1. Wählen Sie **Rollen** aus, um die Liste der Rollen für Azure AD-Berechtigungen anzuzeigen.

    ![Screenshot: Seite „Rollen“ mit ausgewählter Aktion „Zuweisungen hinzufügen“](./media/pim-how-to-add-role-to-user/roles-list.png)

1. Wählen Sie **Zuweisungen hinzufügen** aus, um die Seite **Zuweisungen hinzufügen** zu öffnen.

1. Wählen Sie **Rolle auswählen** aus, um die Seite **Rolle auswählen** zu öffnen.

    ![Bereich „Neue Zuweisung“](./media/pim-how-to-add-role-to-user/select-role.png)

1. Wählen Sie eine Rolle aus, die Sie zuweisen möchten. Wählen Sie ein Mitglied aus, dem die Rolle zugewiesen werden soll. Wählen Sie dann **Weiter** aus.

1. Wählen Sie in der Liste **Zuweisungstyp** im Bereich **Mitgliedschaftseinstellungen** die Option **Berechtigt** oder **Aktiv** aus.

    - Für **berechtigte** Zuweisungen muss das Mitglied der Rolle eine Aktion durchführen, um die Rolle verwenden zu können. Beispiele für Aktionen sind eine erfolgreiche Multi-Factor Authentication-Überprüfung (MFA), die Angabe einer geschäftlichen Begründung oder das Anfordern einer Genehmigung von den angegebenen genehmigenden Personen.

    - Für **aktive** Zuweisungen ist es nicht erforderlich, dass das Mitglied eine Aktion durchführt, um die Rolle nutzen zu können. Für Mitglieder, die als „Aktiv“ zugewiesen sind, sind die Berechtigungen immer der Rolle zugewiesen.

1. Wenn Sie eine bestimmte Zuweisungsdauer angeben möchten, fügen Sie ein Start-und ein Enddatum und Zeitfelder hinzu. Wählen Sie abschließend **Zuweisen** aus, um die neue Rollenzuweisung zu erstellen.

    ![Mitgliedschaftseinstellungen: Datum und Uhrzeit](./media/pim-how-to-add-role-to-user/start-and-end-dates.png)

1. Nachdem die Rolle zugewiesen wurde, wird eine Benachrichtigung über den Zuweisungsstatus angezeigt.

    ![Neue Zuweisung: Benachrichtigung](./media/pim-how-to-add-role-to-user/assignment-notification.png)

## <a name="assign-a-role-with-restricted-scope"></a>Zuweisen einer Rolle mit eingeschränktem Bereich

Bei bestimmten Rollen kann der Bereich der erteilten Berechtigungen auf eine einzelne Verwaltungseinheit, einen Dienstprinzipal oder eine Anwendung beschränkt werden. Dieses Verfahren ist ein Beispiel für die Zuweisung einer Rolle, die den Bereich einer Verwaltungseinheit aufweist. Eine Liste der Rollen, die einen Bereich per Verwaltungseinheit unterstützen, finden Sie unter [Zuweisen von bereichsbezogenen Rollen zu einer Verwaltungseinheit](../roles/admin-units-assign-roles.md). Diese Funktion wird derzeit in Azure AD-Organisationen eingeführt.

1. Melden Sie sich mit Berechtigungen der Rolle „Administrator für privilegierte Rollen“ im [Azure Active Directory Admin Center](https://aad.portal.azure.com) an.

1. Wählen Sie **Azure Active Directory** > **Rollen und Administratoren** aus.

1. Wählen Sie **Benutzeradministrator** aus.

    ![Der Befehl „Zuweisung hinzufügen“ ist verfügbar, wenn Sie eine Rolle im Portal öffnen](./media/pim-how-to-add-role-to-user/add-assignment.png)

1. Wählen Sie **Zuweisungen hinzufügen** aus.

    ![Wenn eine Rolle Bereiche unterstützt, können Sie einen Bereich auswählen](./media/pim-how-to-add-role-to-user/add-scope.png)

1. Auf der Seite **Zuweisungen hinzufügen** haben Sie folgende Möglichkeiten:

   - Auswählen eines Benutzers oder einer Gruppe, der bzw. die der Rolle zugewiesen werden soll
   - Auswählen des Rollenbereichs (in diesem Fall „Verwaltungseinheiten“)
   - Auswählen einer Verwaltungseinheit für den Bereich

Weitere Informationen zum Erstellen von Verwaltungseinheiten finden Sie unter [Hinzufügen und Entfernen von Verwaltungseinheiten](../roles/admin-units-manage.md).

## <a name="update-or-remove-an-existing-role-assignment"></a>Aktualisieren oder Entfernen einer vorhandenen Rollenzuweisung

Befolgen Sie diese Anweisungen zum Aktualisieren oder Entfernen einer vorhandenen Rollenzuweisung. **Nur Kunden mit Azure AD P2-Lizenz**: Weisen Sie einer Rolle eine Gruppe nicht über Azure AD und Privileged Identity Management (PIM) als „Aktiv“ zu. Eine ausführliche Erläuterung finden Sie unter [Bekannte Probleme](../roles/groups-concept.md#known-issues).

1. Öffnen Sie **Azure AD Privileged Identity Management**.

1. Wählen Sie **Azure AD-Rollen** aus.

1. Wählen Sie **Rollen** aus, um die Liste der Rollen für Azure AD anzuzeigen.

1. Wählen Sie die Rolle aus, die Sie aktualisieren oder entfernen möchten.

1. Suchen Sie die Rollenzuweisung auf den Registerkarten **Berechtigte Rollen** oder **Aktive Rollen**.

    ![Aktualisieren oder Entfernen der Rollenzuweisung](./media/pim-how-to-add-role-to-user/remove-update-assignments.png)

1. Wählen Sie **Aktualisieren** oder **Entfernen** aus, um die Rollenzuweisung zu aktualisieren oder zu entfernen.

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren von Einstellungen für Azure AD-Rollen in PIM](pim-how-to-change-default-settings.md)
- [Zuweisen von Azure-Ressourcenrollen in PIM](pim-resource-roles-assign-roles.md)