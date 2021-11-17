---
title: 'Schnellstart: Erstellen und Zuweisen eines Benutzerkontos'
titleSuffix: Azure AD
description: Erstellen Sie ein Benutzerkonto in Ihrem Azure Active Directory-Mandanten, und weisen Sie es einer Anwendung zu.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 09/22/2021
ms.author: davidmu
ms.reviewer: alamaral
ms.openlocfilehash: 38be83db1c585a3660882ebdcca70a239a620f03
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132549145"
---
# <a name="quickstart-create-and-assign-a-user-account"></a>Schnellstart: Erstellen und Zuweisen eines Benutzerkontos

In dieser Schnellstartanleitung verwenden Sie Azure Active Directory Admin Center, um ein Benutzerkonto in Ihrem Azure AD-Mandanten (Azure Active Directory) zu erstellen. Nach dem Erstellen des Kontos können Sie es der Unternehmensanwendung zuweisen, die Sie Ihrem Mandanten hinzugefügt haben.

Es wird empfohlen, zum Testen der Schritte in dieser Schnellstartanleitung keine Produktionsumgebung zu verwenden.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes, um ein Benutzerkonto zu erstellen und es einer Unternehmensanwendung zuzuweisen:

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Eine der folgenden Rollen: Globaler Administrator, Cloudanwendungsadministrator, Anwendungsadministrator oder Besitzer des Dienstprinzipals.
- Ausführen der Schritte unter [Schnellstart: Hinzufügen einer Unternehmensanwendung](add-application-portal.md).

## <a name="create-a-user-account"></a>Erstellen eines Benutzerkontos

So erstellen Sie ein Benutzerkonto in Ihrem Azure AD-Mandanten:

1. Navigieren Sie zu [Azure Active Directory Admin Center](https://aad.portal.azure.com), und melden Sie sich mit einer der in den Voraussetzungen aufgeführten Rollen an.
1. Wählen Sie im Menü auf der linken Seite die Option **Benutzer** aus.
1. Wählen Sie im oberen Bereich die Option **Neuer Benutzer** aus.

    :::image type="content" source="media/add-application-portal-assign-users/new-user.png" alt-text="Hinzufügen eines neuen Benutzerkontos zu Ihrem Azure AD-Mandanten":::
    
1. Geben Sie in das Feld **Benutzername** den Benutzernamen des Benutzerkontos ein. Beispiel: `contosouser1@contoso.com`. Achten Sie darauf, `contoso.com` in den Namen Ihrer Mandantendomäne zu ändern.
1. Geben Sie in das Feld **Name** den Namen des Benutzers des Kontos ein. Beispiel: `contosouser1`.
1. Lassen Sie **Kennwort automatisch generieren** aktiviert, und wählen Sie dann **Kennwort anzeigen** aus. Notieren Sie sich den Wert, der im Feld Kennwort angezeigt wird.
1. Wählen Sie **Erstellen** aus.

## <a name="assign-a-user-account-to-an-enterprise-application"></a>Zuweisen eines Benutzerkontos zu einer Unternehmensanwendung

So weisen Sie einer Unternehmensanwendung ein Benutzerkonto zu:

1. Wählen Sie in [Azure Active Directory Admin Center](https://aad.portal.azure.com) die Option **Unternehmensanwendungen** aus, suchen Sie dann nach der Anwendung, der Sie das Benutzerkonto zuweisen möchten, und wählen Sie sie aus. Verwenden Sie beispielsweise die in der vorherigen Schnellstartanleitung erstellte Anwendung namens **Azure AD SAML Toolkit 1**.
1. Wählen Sie im Bereich auf der linken Seite **Benutzer und Gruppen** und dann **Benutzer/Gruppe hinzufügen** aus.

    :::image type="content" source="media/add-application-portal-assign-users/assign-user.png" alt-text="Zuweisen eines Benutzerkontos zu einer Anwendung in Ihrem Azure AD-Mandanten":::

1. Wählen Sie im Bereich **Zuweisung hinzufügen** unter **Benutzer und Gruppen** die Option **Keine ausgewählt** aus.
1. Suchen Sie nach dem Benutzer, den Sie der Anwendung zuweisen möchten, und wählen Sie ihn aus. Beispiel: `contosouser1@contoso.com`.
1. Wählen Sie **Auswählen**.
1. Wählen Sie im Bereich **Zuweisung hinzufügen** unten die Option **Zuweisen** aus.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie den nächsten Schnellstart durcharbeiten möchten, behalten Sie die von Ihnen erstellte Anwendung. Andernfalls können Sie sie löschen, um Ihren Mandanten zu bereinigen. Weitere Informationen finden Sie unter [Löschen einer Anwendung](delete-application-portal.md).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie einmaliges Anmelden für eine Unternehmensanwendung einrichten:
> [!div class="nextstepaction"]
> [Einmaliges Anmelden aktivieren](add-application-portal-setup-sso.md)
