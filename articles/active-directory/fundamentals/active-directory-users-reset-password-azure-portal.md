---
title: Zurücksetzen eines Benutzerkennworts – Azure Active Directory | Microsoft-Dokumentation
description: Anweisungen zum Zurücksetzen eines Benutzerkennworts mit Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
ms.date: 09/05/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7efa458ebcae7e837be4bb574f4a2707e9cb9e30
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124732601"
---
# <a name="reset-a-users-password-using-azure-active-directory"></a>Zurücksetzen eines Benutzerkennworts mit Azure Active Directory

Als Administrator können Sie das Kennwort eines Benutzers zurücksetzen, wenn er das Kennwort vergessen hat, auf einem Gerät gesperrt wurde oder das Kennwort nie erhalten hat.

>[!Note]
>Wenn Ihr Azure AD-Mandant nicht das Basisverzeichnis für einen Benutzer ist, können Sie sein Kennwort nicht zurücksetzen. Das bedeutet, wenn sich Ihr Benutzer mit dem Konto einer anderen Organisation, einem Microsoft- oder einem Google-Konto bei Ihrer Organisation anmeldet, können Sie sein Kennwort nicht zurücksetzen.<br><br>Wenn Ihr Benutzer über eine Autoritätsquelle als Windows Server Active Directory verfügt, können Sie das Kennwort nur dann zurücksetzen, wenn das Kennwortrückschreiben aktiviert ist.<br><br>Wenn Ihr Benutzer eine Autoritätsquelle als externe Azure AD-Instanz hat, können Sie das Kennwort nicht zurücksetzen. Nur der Benutzer oder ein Administrator in einer externen Azure AD-Instanz kann das Kennwort zurücksetzen.

>[!Note]
>Wenn Sie kein Administrator sind und stattdessen nach Anweisungen zum Zurücksetzen Ihres eigenen Kennworts für ein Geschäfts-, Schul- oder Unikonto suchen, finden Sie die entsprechenden Informationen unter [Zurücksetzen des Kennworts eines Geschäfts-, Schul- oder Unikontos](https://support.microsoft.com/account-billing/reset-your-work-or-school-password-using-security-info-23dde81f-08bb-4776-ba72-e6b72b9dda9e).

## <a name="to-reset-a-password"></a>Zurücksetzen eines Kennworts

1. Melden Sie sich als Benutzeradministrator oder Kennwortadministrator beim [Azure-Portal](https://portal.azure.com/) an. Weitere Informationen zu den verfügbaren Rollen finden Sie unter [Integrierte Rollen in Azure AD](../roles/permissions-reference.md).

2. Wählen Sie **Azure Active Directory**, **Benutzer**, den Benutzer, für den Sie das Kennwort zurücksetzen möchten, und dann **Kennwort zurücksetzen** aus.

    Daraufhin wird die Seite **Alain Charon – Profil** mit der Option **Kennwort zurücksetzen** angezeigt.

    ![Benutzerprofilseite mit hervorgehobener Option „Kennwort zurücksetzen“](media/active-directory-users-reset-password-azure-portal/user-profile-reset-password-link.png)

3. Wählen Sie auf der Seite **Kennwort zurücksetzen** **Kennwort zurücksetzen** aus.

    > [!Note]
    > Bei Verwendung von Azure Active Directory wird für den Benutzer automatisch ein temporäres Kennwort generiert. Wenn Sie eine lokale Active Directory-Instanz verwenden, erstellen Sie das Kennwort für den Benutzer.

4. Kopieren Sie das Kennwort, und leiten Sie es an den Benutzer weiter. Der Benutzer muss das Kennwort bei seiner nächsten Anmeldung ändern.

    >[!Note]
    >Das temporäre Kennwort läuft nie ab. Wenn sich der Benutzer das nächste Mal anmeldet, ist das Kennwort immer noch gültig, unabhängig davon, wie lange das Generieren des temporären Kennworts zurückliegt.

> [!IMPORTANT]
> Wenn ein Administrator das Benutzerkennwort nicht zurücksetzen kann und in den Anwendungsereignisprotokollen auf dem Azure AD Connect-Server der Fehlercode „hr=80231367“ angezeigt wird, überprüfen Sie die Attribute des Benutzers in Active Directory.  Wenn das Attribut **AdminCount** auf „1“ festgelegt ist, wird verhindert, dass ein Administrator das Kennwort des Benutzers zurücksetzt.  Das Attribut **AdminCount** muss auf „0“ festgelegt werden, damit ein Administrator das Kennwort des Benutzers zurücksetzen kann.


## <a name="next-steps"></a>Nächste Schritte

Wenn Sie das Kennwort Ihres Benutzers zurückgesetzt haben, können Sie die folgenden grundlegenden Prozesse ausführen:

- [Hinzufügen oder Löschen von Benutzern](add-users-azure-active-directory.md)

- [Zuweisen von Rollen zu Benutzern](active-directory-users-assign-role-azure-portal.md)

- [Hinzufügen oder Ändern von Profilinformationen](active-directory-users-profile-azure-portal.md)

- [Erstellen einer einfachen Gruppe und Hinzufügen von Mitgliedern](active-directory-groups-create-azure-portal.md)

Sie können auch komplexere Benutzerszenarien ausführen, z.B. Delegate zuweisen, Richtlinien verwenden und Benutzerkonten freigeben. Weitere Informationen zu anderen verfügbaren Aktionen finden Sie unter [Dokumentation zur Azure Active Directory-Benutzerverwaltung](../enterprise-users/index.yml).