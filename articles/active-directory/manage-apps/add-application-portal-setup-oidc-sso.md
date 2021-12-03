---
title: Hinzufügen einer auf OpenID Connect basierenden Anwendung für einmaliges Anmelden
description: Hier erfahren Sie, wie Sie eine auf OpenID Connect basierende Anwendung für einmaliges Anmelden in Azure Active Directory hinzufügen.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 09/22/2021
ms.author: davidmu
ms.reviewer: ergreenl
ms.openlocfilehash: 02bbc4d6b0cc6f346111a8cd0145ef099dd33feb
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132552012"
---
# <a name="add-an-openid-connect-based-single-sign-on-application"></a>Hinzufügen einer auf OpenID Connect basierenden Anwendung für einmaliges Anmelden

Fügen Sie Ihrem Azure AD-Mandanten (Azure Active Directory) eine Anwendung hinzu, die auf [OpenID Connect (OIDC)](../develop/active-directory-v2-protocols.md) basierendes einmaliges Anmelden (Single Sign-On, SSO) unterstützt.

Es wird empfohlen, zum Testen der Schritte auf dieser Seite keine Produktionsumgebung zu verwenden.

## <a name="prerequisites"></a>Voraussetzungen

Zum Konfigurieren des OIDC-basierten einmaligen Anmeldens benötigen Sie Folgendes:

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Eine der folgenden Rollen: Globaler Administrator, Cloudanwendungsadministrator, Anwendungsadministrator oder Besitzer des Dienstprinzipals.

## <a name="add-the-application"></a>Hinzufügen der Anwendung

Beim Hinzufügen einer Unternehmensanwendung, bei der der OIDC-Standard für einmaliges Anmelden genutzt wird, wählen Sie eine Setupschaltfläche aus. Wenn Sie die Schaltfläche auswählen, führen Sie den Registrierungsvorgang für die Anwendung durch.

So konfigurieren Sie OIDC-basiertes einmaliges Anmelden für eine Anwendung:

1. Navigieren Sie zu [Azure Active Directory Admin Center](https://aad.portal.azure.com), und melden Sie sich mit einer der in den Voraussetzungen aufgeführten Rollen an.
1. Wählen Sie im Menü auf der linken Seite die Option **Unternehmensanwendungen** aus. Der Bereich **Alle Anwendungen** wird geöffnet und zeigt eine Liste der Anwendungen in Ihrem Azure AD-Mandanten an. 
1. Wählen Sie im Bereich **Unternehmensanwendungen** die Option **Neue Anwendung** aus.
1. Der Bereich **Azure AD-Katalog durchsuchen** wird mit Kacheln für Cloudplattformen, lokale Anwendungen und ausgewählten Anwendungen geöffnet. Für die im Abschnitt **Ausgewählte Anwendungen** aufgeführten Anwendungen wird mithilfe von Symbolen angegeben, ob sie die einmalige Verbundanmeldung und -bereitstellung unterstützen. Suchen Sie nach der Anwendung, und wählen Sie diese aus. In diesem Beispiel wird **SmartSheet** verwendet.
1. Wählen Sie **Registrieren** aus. Melden Sie sich mit den Anmeldeinformationen des Benutzerkontos von Azure Active Directory an. Falls Sie bereits über ein Abonnement für die Anwendung verfügen, wird eine Überprüfung der Benutzerdetails und Mandanteninformationen durchgeführt. Falls der Benutzer von der Anwendung nicht verifiziert werden kann, werden Sie zur Registrierung für den Anwendungsdienst umgeleitet.

    :::image type="content" source="media/add-application-portal-setup-oidc-sso/oidc-sso-configuration.png" alt-text="Ausfüllen des Zustimmungsbildschirms für eine Anwendung":::

1. Wählen Sie die Option **Zustimmung im Namen Ihrer Organisation** und dann **Annehmen** aus. Die Anwendung wird Ihrem Mandanten hinzugefügt, und die Startseite der Anwendung wird angezeigt. Weitere Informationen zur Benutzer- und Administratoreinwilligung finden Sie unter [Grundlegendes zur Benutzer- und Administratoreinwilligung](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die Planung einer Bereitstellung für einmaliges Anmelden.
> [!div class="nextstepaction"]
> [Planen der Bereitstellung des einmaligen Anmeldens](plan-sso-deployment.md)
