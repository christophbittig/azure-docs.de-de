---
title: Erstellen und Löschen von Azure AD B2C-Benutzerkonten für Consumer im Azure-Portal
description: Erfahren Sie, wie Sie mithilfe des Azure-Portals Consumerbenutzer im Azure AD B2C-Verzeichnis erstellen und löschen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/20/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 02c0197af40854bfbcff04401dbc4e9ca0d990dc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128573895"
---
# <a name="use-the-azure-portal-to-create-and-delete-consumer-users-in-azure-ad-b2c"></a>Verwenden des Azure-Portals zum Erstellen und Löschen von Consumerbenutzern in Azure AD B2C

Möglicherweise gibt es Szenarien, in denen Sie manuell Consumerkonten im Azure Active Directory B2C-Verzeichnis (Azure AD B2C) erstellen möchten. Obwohl Consumerkonten in einem Azure AD B2C-Verzeichnis in der Regel erstellt werden, wenn sich Benutzer für die Verwendung einer Ihrer Anwendungen registrieren, können Sie sie programmgesteuert und mithilfe des Azure-Portals erstellen. In diesem Artikel wird das Erstellen und Löschen von Benutzern mithilfe des Azure-Portals beschrieben.

Ihrem Konto muss die Rolle *Benutzeradministrator* oder *globaler Administrator* zugewiesen sein, damit Sie Benutzer hinzuzufügen oder löschen können.

## <a name="types-of-user-accounts"></a>Typen von Benutzerkonten

Wie in [Übersicht über Benutzerkonten in Azure AD B2C](user-overview.md) beschrieben, gibt es drei Typen von Benutzerkonten, die in einem Azure AD B2C-Verzeichnis erstellt werden können:

* Work
* Gast
* Consumer

In diesem Artikel wird das Arbeiten mit **Consumerkonten** im Azure-Portal behandelt. Informationen zum Erstellen und Löschen von Geschäfts- und Gastkonten finden Sie unter [Hinzufügen oder Löschen von Benutzern in Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md).

## <a name="create-a-consumer-user"></a>Erstellen eines Consumerbenutzers

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie im linken Menü die Option **Azure AD B2C** aus. Oder wählen Sie **Alle Dienste** aus, suchen Sie nach dem Eintrag **Azure AD B2C**, und wählen Sie ihn aus.
1. Wählen Sie unter **Verwalten** die Option **Benutzer** aus.
1. Wählen Sie **Neuer Benutzer** aus.
1. Wählen Sie **Azure AD B2C-Benutzer erstellen** aus.
1. Wählen Sie eine **Anmeldemethode** aus, und geben Sie entweder eine **E-Mail**-Adresse oder einen **Benutzernamen** für den neuen Benutzer ein. Die hier ausgewählte Anmeldemethode muss mit der Einstellung identisch sein, die Sie für den Identitätsanbieter für *Lokales Konto* des Azure AD B2C-Mandanten festgelegt haben (siehe **Verwalten** > **Identitätsanbieter** im Azure AD B2C-Mandanten).
1. Geben Sie einen **Namen** für den Benutzer ein. Dabei handelt es sich in der Regel um den vollständigen Namen (Vor- und Nachname) des Benutzers.
1. (Optional) Sie können **Anmeldung blockieren** auswählen, wenn sich der Benutzer nicht sofort anmelden können soll. Sie können die Anmeldung später ermöglichen, indem Sie das **Profil** des Benutzers im Azure-Portal bearbeiten.
1. Wählen Sie **Kennwort automatisch generieren** oder **Kennwort selbst erstellen** aus.
1. Geben Sie den **Vornamen** und den **Nachnamen** des Benutzers ein.
1. Klicken Sie auf **Erstellen**.

Der Benutzer kann sich nun mit der von Ihnen angegebenen Anmeldemethode (E-Mail-Adresse oder Benutzername) anmelden, es sei denn, Sie haben **Anmeldung blockieren** ausgewählt.

## <a name="reset-a-users-password"></a>Zurücksetzen des Kennworts für einen Benutzer

Als Administrator können Sie das Kennwort für einen Benutzer zurücksetzen, wenn der Benutzer sein Kennwort vergessen hat. Wenn Sie das Kennwort des Benutzers zurücksetzen, wird automatisch ein temporäres Kennwort für den Benutzer generiert. Das temporäre Kennwort läuft nie ab. Wenn sich der Benutzer das nächste Mal anmeldet, ist das Kennwort immer noch gültig, unabhängig davon, wie lange das Generieren des temporären Kennworts zurückliegt. Der Benutzer muss dann das Kennwort auf ein permanentes Kennwort festlegen. 

> [!IMPORTANT]
> Bevor Sie das Kennwort eines Benutzers zurücksetzen, müssen Sie [einen Flow zum Erzwingen der Kennwortzurücksetzung in Azure Active Directory B2C einrichten](force-password-reset.md). Andernfalls kann sich der Benutzer nicht anmelden.

Gehen Sie wie folgt vor, um das Kennwort für einen Benutzer zurückzusetzen:

1. Wählen Sie in Ihrem Azure AD B2C-Verzeichnis die Option **Benutzer** aus, und wählen Sie dann den Benutzer aus, für den Sie das Kennwort zurücksetzen möchten.
1. Suchen Sie nach dem Benutzer, dessen Kennwort zurückgesetzt werden muss, wählen Sie den Benutzer aus, und wählen Sie dann **Kennwort zurücksetzen** aus.

    Daraufhin wird die Seite **Alain Charon – Profil** mit der Option **Kennwort zurücksetzen** angezeigt.

    ![Benutzerprofilseite mit hervorgehobener Option „Kennwort zurücksetzen“](media/manage-users-portal/user-profile-reset-password-link.png)

1. Wählen Sie auf der Seite **Kennwort zurücksetzen** **Kennwort zurücksetzen** aus.
1. Kopieren Sie das Kennwort, und leiten Sie es an den Benutzer weiter. Der Benutzer muss das Kennwort bei seiner nächsten Anmeldung ändern.


## <a name="delete-a-consumer-user"></a>Löschen eines Consumerbenutzers

1. Wählen Sie im Azure AD B2C Verzeichnis **Benutzer**, und wählen Sie dann den Benutzer aus, den Sie löschen möchten.
1. Wählen Sie **Löschen** und dann **Ja**, um den Löschvorgang zu bestätigen.

Ausführliche Informationen zum Wiederherstellen eines Benutzers innerhalb der ersten 30 Tage nach dem Löschen oder zum endgültigen Löschen eines Benutzers finden Sie unter [Wiederherstellen oder Entfernen eines kürzlich gelöschten Benutzers mit Azure Active Directory](../active-directory/fundamentals/active-directory-users-restore.md).

## <a name="next-steps"></a>Nächste Schritte

Informationen zu Szenarien der automatisierten Benutzerverwaltung (z. B. zur Migration von Benutzern von einem anderen Identitätsanbieter zu Ihrem Azure AD B2C-Verzeichnis) finden Sie unter [Migrieren von Benutzern zu Azure AD B2C](user-migration.md).
