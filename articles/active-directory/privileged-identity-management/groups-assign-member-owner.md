---
title: Zuweisen berechtigter Besitzer und Mitglieder für Gruppen mit privilegiertem Zugriff – Azure Active Directory
description: Erfahren Sie, wie Sie berechtigte Besitzer oder Mitglieder einer Gruppe, der Rollen zugeordnet werden können, in Azure AD Privileged Identity Management (PIM) zuweisen.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/09/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 62012b910ad1e08291760a2aea0747bf5d4c024d
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132157585"
---
# <a name="assign-eligibility-for-a-privileged-access-group-preview-in-privileged-identity-management"></a>Zuweisen der Berechtigung für eine Gruppe mit privilegiertem Zugriff (Vorschau) in Privileged Identity Management

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) kann Sie bei der Verwaltung der Berechtigung und der Aktivierung von Zuweisungen zu Gruppen mit privilegiertem Zugriff in Azure AD unterstützen. Sie können Mitgliedern oder Besitzern der Gruppe die Berechtigung zuweisen.

>[!NOTE]
>Jeder Benutzer, der berechtigt ist, Mitglied oder Besitzer einer Gruppe mit privilegiertem Zugriff zu sein, muss über eine Azure AD Premium P2-Lizenz verfügen. Weitere Informationen finden Sie unter [Lizenzanforderungen für die Verwendung von PIM](subscription-requirements.md).

## <a name="assign-an-owner-or-member-of-a-group"></a>Zuweisen eines Besitzers oder Mitglieds einer Gruppe

Führen Sie die folgenden Schritte aus, um einen Benutzer als berechtigt für die Mitgliedschaft oder den Besitz einer Gruppe mit privilegiertem Zugriff einzurichten.

1. Melden Sie sich im [Azure AD-Administrationszentrum](https://aad.portal.azure.com/) mit einem Benutzer in der Rolle [Global Administrator](../roles/permissions-reference.md#global-administrator), der Rolle Privileged Role Administrator oder der Rolle Gruppenbesitzer an.

1. Wählen Sie **Gruppen** und dann die Gruppe mit Rollenzuweisung aus, die Sie verwalten möchten. Sie können die Liste durchsuchen und filtern.

    ![Suchen einer Gruppe mit Rollenzuweisung für die Verwaltung in PIM](./media/groups-assign-member-owner/groups-list-in-azure-ad.png)

1. Öffnen Sie die Gruppe, und wählen Sie **Privilegierter Zugriff (Vorschau)** aus.

    ![Öffnen der Privileged Identity Management-Benutzeroberfläche](./media/groups-assign-member-owner/groups-discover-groups.png)

1. Wählen Sie **Zuweisungen hinzufügen** aus.

    ![Bereich „Neue Zuweisung“](./media/groups-assign-member-owner/groups-add-assignment.png)

1. Wählen Sie die Mitglieder oder Besitzer aus, die Sie als berechtigt für die Gruppe mit privilegiertem Zugriff einrichten möchten.

    ![Der Screenshot zeigt die Seite „Zuweisungen hinzufügen“ mit geöffnetem Bereich „Mitglied oder Gruppe auswählen“ und hervorgehobener Schaltfläche „Auswählen“.](./media/groups-assign-member-owner/add-assignments.png)

1. Wählen Sie **Weiter** aus, um die Dauer von Mitgliedschaft oder Besitz festzulegen.

    ![Bereich „Mitglied oder Gruppe auswählen“](./media/groups-assign-member-owner/assignment-duration.png)

1. Wählen Sie in der Liste **Zuweisungstyp** entweder **Berechtigt** oder **Aktiv** aus. Gruppen mit privilegiertem Zugriff bieten zwei verschiedene Zuweisungstypen:

    - Für **berechtigte** Zuweisungen muss das Mitglied der Rolle eine Aktion durchführen, um die Rolle verwenden zu können. Beispiele für Aktionen sind eine erfolgreiche Multi-Factor Authentication-Überprüfung (MFA), die Angabe einer geschäftlichen Begründung oder das Anfordern einer Genehmigung von den angegebenen genehmigenden Personen. 

      > [!Important]
      > Für Gruppen mit berechtigtem Zugriff, die für die Erhöhung Azure AD Rollen verwendet werden, empfiehlt Microsoft, dass Sie einen Genehmigungsprozess für berechtigte Mitgliedszuweisungen benötigen. Zuweisungen, die ohne Genehmigung aktiviert werden können, können Sie anfällig für ein Sicherheitsrisiko von einem anderen Administrator mit der Berechtigung zum Zurücksetzen der Kennwörter eines berechtigten Benutzers machen.

    - Für **aktive** Zuweisungen ist es nicht erforderlich, dass das Mitglied eine Aktion durchführt, um die Rolle nutzen zu können. Für Mitglieder, die als „Aktiv“ zugewiesen sind, sind die Berechtigungen immer der Rolle zugewiesen.

1. Wenn die Zuweisung dauerhaft sein soll (dauerhaft berechtigt oder dauerhaft zugewiesen), aktivieren Sie das Kontrollkästchen **Dauerhaft**. Je nach Einstellungen Ihrer Organisation wird das Kontrollkästchen möglicherweise nicht angezeigt oder kann nicht bearbeitet werden.

1. Klicken Sie abschließend auf **Zuweisen**.

1. Wählen Sie **Hinzufügen** aus, um die neue Rollenzuweisung zu erstellen. Eine Benachrichtigung zum Status wird angezeigt.

    ![Neue Zuweisung: Benachrichtigung](./media/groups-assign-member-owner/groups-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Aktualisieren oder Entfernen einer vorhandenen Rollenzuweisung

Befolgen Sie diese Anweisungen zum Aktualisieren oder Entfernen einer vorhandenen Rollenzuweisung.

1. [Melden Sie sich bei Azure AD](https://aad.portal.azure.com) mit den Berechtigungen eines globalen Administrators oder Gruppenbesitzers an.
1. Wählen Sie **Gruppen** und dann die Gruppe mit Rollenzuweisung aus, die Sie verwalten möchten. Sie können die Liste durchsuchen und filtern.

    ![Suchen einer Gruppe mit Rollenzuweisung für die Verwaltung in PIM](./media/groups-assign-member-owner/groups-list-in-azure-ad.png)

1. Öffnen Sie die Gruppe, und wählen Sie **Privilegierter Zugriff (Vorschau)** aus.

    ![Öffnen der Privileged Identity Management-Benutzeroberfläche](./media/groups-assign-member-owner/groups-discover-groups.png)

1. Wählen Sie die Rolle aus, die Sie aktualisieren oder entfernen möchten.

1. Suchen Sie die Rollenzuweisung auf den Registerkarten **Berechtigte Rollen** oder **Aktive Rollen**.

    ![Aktualisieren oder Entfernen der Rollenzuweisung](./media/groups-assign-member-owner/groups-bring-under-management.png)

1. Wählen Sie **Aktualisieren** oder **Entfernen** aus, um die Rollenzuweisung zu aktualisieren oder zu entfernen.

    Informationen zum Erweitern einer Rollenzuweisung finden Sie unter [Verlängern oder Erneuern von Azure-Ressourcenrollen in Privileged Identity Management](pim-resource-roles-renew-extend.md).

## <a name="next-steps"></a>Nächste Schritte

- [Verlängern oder Erneuern von Azure-Ressourcenrollen in Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [Konfigurieren von Einstellungen für Azure-Ressourcenrollen in Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Zuweisen von Azure AD-Rollen in PIM](pim-how-to-add-role-to-user.md)
