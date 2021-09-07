---
title: 'Zuweisen von Azure-Rollen zu externen Gastbenutzern über das Azure-Portal: Azure RBAC'
description: Hier erfahren Sie, wie Sie organisationsexternen Benutzern mithilfe des Azure-Portals und der rollenbasierten Zugriffssteuerung von Azure (Azure RBAC) Zugriff auf Azure-Ressourcen erteilen.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 06/28/2021
ms.author: rolyon
ms.custom: it-pro,subject-rbac-steps
ms.openlocfilehash: 175beacc486c4b59919bf20300bbd06f7b9aa1a7
ms.sourcegitcommit: 1c12bbaba1842214c6578d914fa758f521d7d485
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/28/2021
ms.locfileid: "112988504"
---
# <a name="assign-azure-roles-to-external-guest-users-using-the-azure-portal"></a>Zuweisen von Azure-Rollen zu externen Gastbenutzern über das Azure-Portal

Die [rollenbasierte Zugriffssteuerung von Azure (Azure RBAC)](overview.md) ermöglicht eine bessere Sicherheitsverwaltung für große Organisationen sowie für kleine und mittelständische Unternehmen, die mit externen Projektmitarbeitern, Lieferanten oder Freiberuflern zusammenarbeiten und für diese Zugriff auf bestimmte Ressourcen in der Umgebung benötigen, nicht aber auf die gesamte Infrastruktur oder abrechnungsrelevante Bereiche. Mithilfe der Funktionen in [Azure Active Directory B2B](../active-directory/external-identities/what-is-b2b.md) können Sie mit externen Gastbenutzern zusammenarbeiten, und Sie können mithilfe von Azure RBAC nur die Berechtigungen erteilen, die Gastbenutzer in Ihrer Umgebung benötigen.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes, um Azure-Rollen zuzuweisen oder zu entfernen:

- `Microsoft.Authorization/roleAssignments/write`- und `Microsoft.Authorization/roleAssignments/delete`-Berechtigungen, wie z.B. [Benutzerzugriffsadministrator](built-in-roles.md#user-access-administrator) oder [Besitzer](built-in-roles.md#owner)

## <a name="when-would-you-invite-guest-users"></a>Wann würden Sie Gastbenutzer einladen?

Einige Beispielszenarios, in denen Sie möglicherweise Gastbenutzer in Ihre Organisation einladen und Berechtigungen erteilen:

- Zugriff für einen externen selbstständigen Anbieter, der nur über ein E-Mail-Konto verfügt, auf Ihre Azure-Ressourcen für ein Projekt
- Zugriff für einen externen Partner zum Verwalten bestimmter Ressourcen oder eines ganzen Abonnements
- Temporärer Zugriff für Supporttechniker außerhalb Ihrer Organisation (z.B. Microsoft-Support) auf Ihre Azure-Ressource zur Behebung von Problemen

## <a name="permission-differences-between-member-users-and-guest-users"></a>Berechtigungsunterschiede zwischen Mitgliedsbenutzern und Gastbenutzern

Native Mitglieder eines Verzeichnisses (Mitgliedsbenutzer) verfügen über andere Berechtigungen als Benutzer, die als B2B-Zusammenarbeitsgast (Gastbenutzer) aus einem anderen Verzeichnis eingeladen wurden. Ein Mitgliedsbenutzer kann z. B. fast alle Verzeichnisinformationen lesen, während Gastbenutzer über eingeschränkte Verzeichnisberechtigungen verfügen. Weitere Informationen zu Mitglieds- und Gastbenutzern finden Sie unter [Welche Standardbenutzerberechtigungen gibt es in Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md).

## <a name="add-a-guest-user-to-your-directory"></a>Hinzufügen eines Gastbenutzers zu Ihrem Verzeichnis

Führen Sie diese Schritte aus, um einen Gastbenutzer über die Azure Active Directory-Seite zu Ihrem Verzeichnis hinzuzufügen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Stellen Sie sicher, dass die Einstellungen für externe Zusammenarbeit Ihrer Organisation so konfiguriert sind, dass Sie Gäste einladen dürfen. Weitere Informationen finden Sie unter [Aktivieren der externen B2B-Zusammenarbeit und Steuern, wer Gäste einladen kann](../active-directory/external-identities/delegate-invitations.md).

1. Klicken Sie auf **Azure Active Directory** > **Benutzer** > **Neuer Gastbenutzer**.

    ![Screenshot: Feature „Neuer Gastbenutzer“ im Azure-Portal](./media/role-assignments-external-users/invite-guest-user.png)

1. Befolgen Sie die Schritte, um einen neuen Gastbenutzer hinzuzufügen. Weitere Informationen finden Sie unter [Hinzufügen von Azure Active Directory B2B-Zusammenarbeitsbenutzern über das Azure-Portal](../active-directory/external-identities/add-users-administrator.md#add-guest-users-to-the-directory).

Nachdem Sie dem Verzeichnis einen Gastbenutzer hinzugefügt haben, können Sie dem Gastbenutzer einen direkten Link zu einer freigegebenen App senden, oder der Gastbenutzer kann auf den Link „Einladung annehmen“ in der Einladungs-E-Mail klicken.

![Screenshot: Einladungs-E-Mail für Gastbenutzer](./media/role-assignments-external-users/invite-email.png)

Damit der Gastbenutzer auf Ihr Verzeichnis zugreifen kann, muss er den Einladungsprozess abschließen.

![Screenshot: Berechtigungen zum Überprüfen der Gastbenutzereinladung](./media/role-assignments-external-users/invite-review-permissions.png)

Weitere Informationen zum Einladungsprozess finden Sie unter [Azure Active Directory B2B-Zusammenarbeit: Einlösen von Einladungen](../active-directory/external-identities/redemption-experience.md).

## <a name="assign-a-role-to-a-guest-user"></a>Zuweisen einer Rolle zu einem Gastbenutzer

In Azure RBAC weisen Sie zum Gewähren des Zugriffs eine Rolle zu. Um einem Gastbenutzer eine Rolle zuzuweisen, befolgen Sie [dieselben Schritte](role-assignments-portal.md) wie für einen Mitgliedsbenutzer, eine Gruppe, einen Dienstprinzipal oder eine verwaltete Identität. Gehen Sie wie folgt vor, um einem Gastbenutzer eine Rolle mit verschiedenen Gültigkeitsbereichen zuzuweisen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Suchen Sie im oberen Suchfeld nach dem Bereich, für den Sie Zugriff gewähren möchten. Suchen Sie beispielsweise nach **Verwaltungsgruppen**, **Abonnements**, **Ressourcengruppen** oder eine bestimmten Ressource.

1. Klicken Sie auf die gewünschte Ressource für diesen Bereich.

1. Klicken Sie auf **Zugriffssteuerung (IAM)** .

    Die folgende Abbildung zeigt ein Beispiel der Seite „Zugriffssteuerung (IAM)“ für eine Ressourcengruppe.

    ![Screenshot: Seite „Zugriffssteuerung (IAM)“ für eine Ressourcengruppe in der Benutzeroberfläche der Vorschauversion.](./media/shared/rg-access-control.png)

1. Klicken Sie auf die Registerkarte **Rollenzuweisungen**, um die Rollenzuweisungen für diesen Bereich anzuzeigen.

1. Klicken Sie auf **Hinzufügen** > **Rollenzuweisung hinzufügen (Vorschau)** .

    Wenn Sie keine Berechtigungen zum Zuweisen von Rollen haben, ist die Option „Rollenzuweisung hinzufügen“ deaktiviert.

    ![Screenshot: Menü „Hinzufügen > Rollenzuweisung hinzufügen“ in der Benutzeroberfläche der Vorschauversion.](./media/shared/add-role-assignment-menu-preview.png)

    Die Seite „Rollenzuweisung hinzufügen“ wird geöffnet.

1. Wählen Sie auf der Registerkarte **Rolle** eine Rolle aus, etwa **Mitwirkender für virtuelle Computer**.

   ![Screenshot: Seite „Rollenzuweisung hinzufügen“ mit der Registerkarte „Rollen“ auf der Benutzeroberfläche der Vorschauversion](./media/shared/roles.png)

1. Wählen Sie auf der Registerkarte **Mitglieder** die Option **Benutzer, Gruppe oder Dienstprinzipal** aus.

   ![Screenshot: Seite „Rollenzuweisung hinzufügen“ mit der Registerkarte „Mitglieder“ auf der Benutzeroberfläche der Vorschauversion](./media/shared/members.png)

1. Klicken Sie auf **Mitglieder auswählen**.

1. Suchen Sie den Gastbenutzer, und wählen Sie ihn aus. Wird der Benutzer in der Liste nicht angezeigt, können Sie im Feld **Auswählen** einen Suchbegriff eingeben, um das Verzeichnis nach Anzeigename oder E-Mail-Adresse zu durchsuchen.

    Sie können im Feld **Auswählen** einen Begriff eingeben, um das Verzeichnis nach Anzeigename oder E-Mail-Adresse zu durchsuchen.

    ![Screenshot: Bereich „Mitglieder auswählen“ auf der Benutzeroberfläche der Vorschauversion](./media/role-assignments-external-users/select-members.png)

1. Klicken Sie auf **Auswählen**, um den Gastbenutzer der Mitgliederliste hinzuzufügen.

1. Klicken Sie auf der Registerkarte **Überprüfen und zuweisen** auf **Überprüfen und zuweisen**.

    Nach einigen Augenblicken wird dem Gastbenutzer die Rolle für den ausgewählten Bereich zugewiesen.

    ![Screenshot: Rollenzuweisung für „Mitwirkender für virtuelle Computer“](./media/role-assignments-external-users/access-control-role-assignments.png)

## <a name="assign-a-role-to-a-guest-user-not-yet-in-your-directory"></a>Zuweisen einer Rolle zu einem Gastbenutzer, der sich noch nicht in Ihrem Verzeichnis befindet

Um einem Gastbenutzer eine Rolle zuzuweisen, befolgen Sie [dieselben Schritte](role-assignments-portal.md) wie für einen Mitgliedsbenutzer, eine Gruppe, einen Dienstprinzipal oder eine verwaltete Identität.

Wenn sich der Gastbenutzer noch nicht in Ihrem Verzeichnis befindet, können Sie den Benutzer direkt über den Bereich „Mitglieder auswählen“ einladen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Suchen Sie im oberen Suchfeld nach dem Bereich, für den Sie Zugriff gewähren möchten. Suchen Sie beispielsweise nach **Verwaltungsgruppen**, **Abonnements**, **Ressourcengruppen** oder eine bestimmten Ressource.

1. Klicken Sie auf die gewünschte Ressource für diesen Bereich.

1. Klicken Sie auf **Zugriffssteuerung (IAM)** .

1. Klicken Sie auf **Hinzufügen** > **Rollenzuweisung hinzufügen (Vorschau)** .

    Wenn Sie keine Berechtigungen zum Zuweisen von Rollen haben, ist die Option „Rollenzuweisung hinzufügen“ deaktiviert.

    ![Screenshot: Menü „Hinzufügen > Rollenzuweisung hinzufügen“ in der Benutzeroberfläche der Vorschauversion.](./media/shared/add-role-assignment-menu-preview.png)

    Die Seite „Rollenzuweisung hinzufügen“ wird geöffnet.

1. Wählen Sie auf der Registerkarte **Rolle** eine Rolle aus, etwa **Mitwirkender für virtuelle Computer**.

1. Wählen Sie auf der Registerkarte **Mitglieder** die Option **Benutzer, Gruppe oder Dienstprinzipal** aus.

   ![Screenshot: Seite „Rollenzuweisung hinzufügen“ mit der Registerkarte „Mitglieder“ auf der Benutzeroberfläche der Vorschauversion](./media/shared/members.png)

1. Klicken Sie auf **Mitglieder auswählen**.

1. Geben Sie im Feld **Auswählen** die E-Mail-Adresse der einzuladenden Person ein, und wählen Sie diese Person dann aus.

    ![Screenshot: Einladen eines Gastbenutzers im Bereich „Mitglieder auswählen“](./media/role-assignments-external-users/select-members-new-guest.png)

1. Klicken Sie auf **Auswählen**, um den Gastbenutzer der Mitgliederliste hinzuzufügen.

1. Klicken Sie auf der Registerkarte **Überprüfen und zuweisen** auf **Überprüfen und zuweisen**, um den Gastbenutzer zu Ihrem Verzeichnis hinzuzufügen, die Rolle zuzuweisen und eine Einladung zu senden.

    Nach einigen Momenten werden eine Benachrichtigung über die Rollenzuweisung sowie Informationen zur Einladung angezeigt.

    ![Screenshot: Rollenzuweisung und Benachrichtigung des eingeladenen Benutzers](./media/role-assignments-external-users/invited-user-notification.png)

1. Um den Gastbenutzer manuell einzuladen, klicken Sie mit der rechten Maustaste und kopieren den Link der Einladung in der Benachrichtigung. Klicken Sie nicht auf den Link zur Einladung, da dadurch der Einladungsprozess gestartet wird.

    Der Link zur Einladung hat das folgende Format:

    `https://login.microsoftonline.com/redeem?rd=https%3a%2f%2finvitations.microsoft.com%2fredeem%2f%3ftenant%3d0000...`

1. Senden Sie den Einladungslink an den Gastbenutzer, um den Einladungsprozess abzuschließen.

    Weitere Informationen zum Einladungsprozess finden Sie unter [Azure Active Directory B2B-Zusammenarbeit: Einlösen von Einladungen](../active-directory/external-identities/redemption-experience.md).

## <a name="remove-a-guest-user-from-your-directory"></a>Entfernen eines Gastbenutzers aus Ihrem Verzeichnis

Bevor Sie einen Gastbenutzer aus einem Verzeichnis entfernen, sollten Sie zunächst alle Rollenzuweisungen für diesen Gastbenutzer entfernen. Führen Sie die folgenden Schritte aus, um einen Gastbenutzer aus einem Verzeichnis zu entfernen.

1. Öffnen Sie das Blatt **Zugriffssteuerung (IAM)** für einen Bereich, z. B. für eine Verwaltungsgruppe, ein Abonnement, eine Ressourcengruppe oder eine Ressource, für den der Gastbenutzer über eine Rollenzuweisung verfügt.

1. Klicken Sie auf die Registerkarte **Rollenzuweisungen**, um alle Rollenzuweisungen anzuzeigen.

1. Aktivieren Sie in der Liste der Rollenzuweisungen den Gastbenutzer mit der zu entfernenden Rollenzuweisung.

   ![Screenshot: Ausgewählte Rollenzuweisung, die entfernt werden soll](./media/role-assignments-external-users/remove-role-assignment-select.png)

1. Klicken Sie auf **Entfernen**.

   ![Screenshot: Meldung zum Entfernen der Rollenzuweisung](./media/shared/remove-role-assignment.png)

1. Klicken Sie in der angezeigten Meldung zum Entfernen der Rollenzuweisung auf **Ja**.

1. Klicken Sie auf der linken Navigationsleiste auf **Azure Active Directory** > **Benutzer**.

1. Klicken Sie auf den zu entfernenden Gastbenutzer.

1. Klicken Sie auf **Löschen**.

   ![Screenshot: Löschen eines Gastbenutzers](./media/role-assignments-external-users/delete-guest-user.png)

1. Klicken Sie im angezeigten Meldungsfeld auf **Ja**.

## <a name="troubleshoot"></a>Problembehandlung

### <a name="guest-user-cannot-browse-the-directory"></a>Gastbenutzer können das Verzeichnis nicht durchsuchen.

Gastbenutzer erhalten eingeschränkte Verzeichnisberechtigungen. Gastbenutzer können z. B. das Verzeichnis nicht durchsuchen und nicht nach Gruppen oder Anwendungen suchen. Weitere Informationen finden Sie unter [Welche Standardbenutzerberechtigungen gibt es in Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md).

![Screenshot: Gastbenutzer können Benutzer in einem Verzeichnis nicht durchsuchen.](./media/role-assignments-external-users/directory-no-users.png)

Wenn ein Gastbenutzer zusätzliche Berechtigungen im Verzeichnis benötigt, können Sie dem Gastbenutzer eine Azure AD-Rolle zuweisen. Wenn Sie einem Gastbenutzer tatsächlich uneingeschränkten Lesezugriff auf Ihr Verzeichnis gestatten möchten, können Sie den Gastbenutzer der Rolle [Verzeichnisleseberechtigte](../active-directory/roles/permissions-reference.md#directory-readers) in Azure AD hinzufügen. Weitere Informationen finden Sie unter [Hinzufügen von Azure Active Directory B2B-Zusammenarbeitsbenutzern über das Azure-Portal](../active-directory/external-identities/add-users-administrator.md).

![Screenshot: Zuweisen der Rolle „Verzeichnisleseberechtigte“](./media/role-assignments-external-users/directory-roles.png)

### <a name="guest-user-cannot-browse-users-groups-or-service-principals-to-assign-roles"></a>Gastbenutzer können keine Benutzer, Gruppen oder Dienstprinzipale durchsuchen, um Rollen zuzuweisen.

Gastbenutzer erhalten eingeschränkte Verzeichnisberechtigungen. Selbst wenn ein Gastbenutzer ein [Besitzer](built-in-roles.md#owner) in einem Bereich ist, kann er die Liste der Benutzer, Gruppen oder Dienstprinzipale nicht durchsuchen, wenn er versucht, einer anderen Person Zugriff zu erteilen.

![Screenshot: Gastbenutzer können keine Sicherheitsprinzipale durchsuchen, um Rollen zuzuweisen.](./media/role-assignments-external-users/directory-no-browse.png)

Wenn der Gastbenutzer den genauen Anmeldenamen einer Person im Verzeichnis kennt, kann er Zugriff gewähren. Wenn Sie einem Gastbenutzer tatsächlich uneingeschränkten Lesezugriff auf Ihr Verzeichnis gestatten möchten, können Sie den Gastbenutzer der Rolle [Verzeichnisleseberechtigte](../active-directory/roles/permissions-reference.md#directory-readers) in Azure AD hinzufügen. Weitere Informationen finden Sie unter [Hinzufügen von Azure Active Directory B2B-Zusammenarbeitsbenutzern über das Azure-Portal](../active-directory/external-identities/add-users-administrator.md).

### <a name="guest-user-cannot-register-applications-or-create-service-principals"></a>Gastbenutzer können keine Anwendungen registrieren oder Dienstprinzipale erstellen.

Gastbenutzer erhalten eingeschränkte Verzeichnisberechtigungen. Wenn ein Gastbenutzer Anwendungen registrieren oder Dienstprinzipale erstellen können muss, können Sie den Gastbenutzer der Rolle [Anwendungsentwickler](../active-directory/roles/permissions-reference.md#application-developer) in Azure AD hinzufügen. Weitere Informationen finden Sie unter [Hinzufügen von Azure Active Directory B2B-Zusammenarbeitsbenutzern über das Azure-Portal](../active-directory/external-identities/add-users-administrator.md).

![Screenshot: Gastbenutzer können keine Anwendungen registrieren.](./media/role-assignments-external-users/directory-access-denied.png)

### <a name="guest-user-does-not-see-the-new-directory"></a>Gastbenutzern wird das neue Verzeichnis nicht angezeigt.

Wenn ein Gastbenutzer Zugriff auf ein Verzeichnis hat, aber das neue Verzeichnis nicht im Azure-Portal angezeigt wird, wenn er versucht, auf der Seite **Verzeichnisse** dorthin zu wechseln, stellen Sie sicher, dass der Gastbenutzer den Einladungprozess abgeschlossen hat. Weitere Informationen zum Einladungsprozess finden Sie unter [Azure Active Directory B2B-Zusammenarbeit: Einlösen von Einladungen](../active-directory/external-identities/redemption-experience.md).

### <a name="guest-user-does-not-see-resources"></a>Gastbenutzern werden keine Ressourcen angezeigt.

Wenn einem Gastbenutzer der Zugriff auf ein Verzeichnis gewährt wurde, er aber die Ressourcen, auf die er im Azure-Portal Zugriff erhalten hat, nicht sieht, stellen Sie sicher, dass der Gastbenutzer das richtige Verzeichnis ausgewählt hat. Ein Gastbenutzer kann Zugriff auf mehrere Verzeichnisse erhalten. Klicken Sie zum Wechseln des Verzeichnisses oben links auf **Einstellungen** > **Verzeichnisse**, und klicken Sie dann auf das entsprechende Verzeichnis.

![Screenshot: Abschnitt „Verzeichnisse“ in den Portaleinstellungen im Azure-Portal](./media/role-assignments-external-users/directory-switch.png)

## <a name="next-steps"></a>Nächste Schritte

- [Add Azure Active Directory B2B collaboration users in the Azure portal](../active-directory/external-identities/add-users-administrator.md) (Hinzufügen von Azure Active Directory B2B-Zusammenarbeitsbenutzern über das Azure-Portal)
- [Eigenschaften eines Azure Active Directory B2B-Zusammenarbeitsbenutzers](../active-directory/external-identities/user-properties.md)
- [Die Elemente der Einladungs-E-Mail für die B2B-Zusammenarbeit – Azure Active Directory](../active-directory/external-identities/invitation-email-elements.md)
- [Hinzufügen eines Gastbenutzers als Co-Admin](classic-administrators.md#add-a-guest-user-as-a-co-administrator)