---
title: Registrieren für Microsoft 365 mit einem Azure-Konto
description: Hier erfahren Sie, wie Sie mit einem Azure-Konto ein Microsoft 365-Abonnement erstellen. Sie können auch vorhandene Azure- und Microsoft 365 Konten einander zuordnen.
author: bandersmsft
ms.reviewer: adwise
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 09/15/2021
ms.author: banders
ms.openlocfilehash: 4320e3c40cd7d7f9c9cbfa70959de775965e17a0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131473260"
---
# <a name="sign-up-for-a-microsoft-365-subscription-with-your-azure-account"></a>Registrieren für ein Microsoft 365-Abonnement mit Ihrem Azure-Konto

Wenn Sie Azure-Abonnent sind, können Sie zum Registrieren für ein Microsoft 365-Abonnement Ihr Azure-Konto verwenden. Wenn Sie Mitglied einer Organisation sind, die über ein Azure-Abonnement verfügt, können Sie Microsoft 365-Abonnements für Benutzer in der vorhandenen Azure Active Directory-Instanz (Azure AD) erstellen. Registrieren Sie sich für Microsoft 365 mit einem Konto mit der Berechtigung „Globaler Administrator“ oder „Abrechnungsadministrator“ in Ihrem Azure Active Directory-Mandanten. Weitere Informationen finden Sie unter [Überprüfen meiner Kontoberechtigungen in Azure AD](#RoleInAzureAD) und [Zuweisen von Administratorrollen in Azure Active Directory](../../active-directory/roles/permissions-reference.md).

Wenn Sie bereits über ein Microsoft 365-Konto und ein Azure-Abonnement verfügen, können Sie [einem Azure-Abonnement einen Microsoft 365-Mandanten zuordnen](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="get-a-microsoft-365-subscription-by-using-your-azure-account"></a>Erhalten eines Microsoft 365-Abonnements mithilfe Ihres Azure-Kontos

1. Wechseln Sie zur [Microsoft 365-Produktseite](https://www.microsoft.com/microsoft-365/business/all-business), und wählen Sie einen Plan aus.
2. Klicken Sie oben rechts auf der Seite auf **Anmelden** .

    ![Screenshot: Seite für die Microsoft 365-Testversion](./media/azure-account-for-microsoft-365-subscription/12-office-365-trial-page.png)
3. Melden Sie sich mit Ihren Azure-Kontoanmeldeinformationen an. Wenn Sie ein Abonnement für Ihre Organisation erstellen, verwenden Sie ein Azure-Konto, das Mitglied der Rolle „Globaler Administrator“ oder „Abrechnungsadministrator“ auf Ihrem Azure Active Directory-Mandanten ist.

    ![Screenshot: Microsoft-Anmeldung](./media/azure-account-for-microsoft-365-subscription/13-office-365-sign-in.png)
4. Klicken auf **Jetzt ausprobieren**.

    ![Screenshot: Bestätigung Ihrer Bestellung von Microsoft 365](./media/azure-account-for-microsoft-365-subscription/14-office-365-confirm-your-order.png)
5. Klicken Sie auf der Bestellbestätigungsseite auf **Weiter**.

    ![Screenshot: Microsoft 365-Bestellbestätigung](./media/azure-account-for-microsoft-365-subscription/15-office-365-order-receipt.png)

Der Vorgang ist nun abgeschlossen. Wenn Sie das Microsoft 365-Abonnement für Ihre Organisation erstellt haben, führen Sie die folgenden Schritte aus, um zu überprüfen, ob die Azure AD-Benutzer jetzt Microsoft 365-Benutzer sind.

1. Öffnen Sie das Microsoft 365 Admin Center.
2. Erweitern Sie **BENUTZER**, und klicken Sie dann auf **Aktive Benutzer**.

    ![Screenshot der Benutzer im Microsoft 365 Admin Center](./media/azure-account-for-microsoft-365-subscription/16-microsoft-365-admin-center-users.png)

Nach der Registrierung wird das Microsoft 365-Abonnement der gleichen Azure Active Directory-Instanz hinzugefügt, der Ihr Azure-Abonnement angehört. Weitere Informationen finden Sie unter [Hintergrundinformationen zu Azure- und Microsoft 365-Abonnements](microsoft-365-account-for-azure-subscription.md#more-about-subs) und [Zuordnen oder Hinzufügen eines Azure-Abonnements zu Ihrem Azure Active Directory-Mandanten](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="check-my-account-permissions-in-azure-ad"></a><a id="RoleInAzureAD"></a>Überprüfen meiner Kontoberechtigungen in Azure AD
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Klicken Sie auf **Alle Dienste**, und suchen Sie nach **Active Directory**.

    ![Screenshot von Active Directory im Azure-Portal](./media/azure-account-for-microsoft-365-subscription/billing-more-services-active-directory.png)
3. Klicken Sie auf **Benutzer und Gruppen** > **Alle Benutzer**.
4. Wählen Sie den Benutzernamen aus.

    ![Screenshot der Azure Active Directory-Benutzer](./media/azure-account-for-microsoft-365-subscription/billing-users-groups.png)

5. Klicken Sie auf **Verzeichnisrolle**.

    ![Screenshot der Verzeichnisrolle im Azure-Portal](./media/azure-account-for-microsoft-365-subscription/billing-user-directory-role.png)
6.  Um ein Microsoft 365-Abonnement für Benutzer in der vorhandenen Azure Active Directory-Instanz zu erstellen, ist die Rolle **Globaler Administrator** oder **Eingeschränkter Administrator** > **Rechnungsadministrator** erforderlich.

    ![Screenshot der Verzeichnisrolle „Rechnungsadministrator“ im Azure-Portal](./media/azure-account-for-microsoft-365-subscription/billing-directoryrole-limited.png)

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nächste Schritte

- [Zuordnen eines Microsoft 365-Mandanten zu einem Azure-Abonnement](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
