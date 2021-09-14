---
title: Veröffentlichen Ihrer Azure Active Directory B2C-App im Azure Active Directory-App-Katalog
description: Erfahren Sie, wie Sie eine Azure AD B2C-App, die das einmalige Anmelden unterstützt, im Azure Active Directory-App-Katalog listen.
titleSuffix: Azure AD B2C
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/15/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4413832661ffe2eea6fd48f8337618e628f2094b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122354732"
---
# <a name="publish-your-azure-ad-b2c-app-to-the-azure-ad-app-gallery"></a>Veröffentlichen Ihrer Azure AD B2C-App im Azure AD-App-Katalog

Der App-Katalog von Azure Active Directory (Azure AD) enthält Tausende von Apps. Dank dieses Katalogs können Sie das einmalige Anmelden (Single Sign-On, SSO) ganz einfach bereitstellen und konfigurieren und die Benutzereinrichtung automatisieren. In diesem Katalog finden Sie einige beliebte Cloud-Apps, wie z. B. Workday, ServiceNow und Zoom.

In diesem Artikel wird beschrieben, wie Sie Ihre Azure Active Directory B2C-App (Azure AD B2C) im Azure AD-App-Katalog veröffentlichen. Wenn Ihre App veröffentlicht wird, wird sie bei den Optionen aufgeführt, aus denen Kunden auswählen können, wenn sie ihrem Azure AD-Mandanten Apps hinzufügen.

Durch Hinzufügen Ihrer Azure AD B2C-App zum App-Katalog profitieren Sie u. a. von Folgendem:  

- Ihre App ist eine verifizierte Integration in Microsoft.
- Zwischen Ihrer App und Azure AD-Apps wird SSO-Zugriff ermöglicht.
- Kunden finden Ihre App durch eine schnelle Suche im Katalog.
- Die App-Konfiguration ist einfach und minimal.
- Kunden erhalten ein ausführliches Tutorial zur Konfiguration.
- Kunden können die App verschiedenen Benutzern und Gruppen in ihrer Organisation zuweisen.
- Der Mandantenadministrator kann Ihrer App eine mandantenweite Administratoreinwilligung erteilen.

## <a name="sign-in-flow-overview"></a>Übersicht über den Anmeldeflow

Der Anmeldeflow umfasst die folgenden Schritte:

1. Benutzer besuchen das [Portal „Meine Apps“](https://myapps.microsoft.com/) und wählen Ihre App aus. Dadurch wird die URL zur Anmeldung bei der App geöffnet.
1. Die URL zur Anmeldung bei der App startet eine Autorisierungsanforderung und leitet Benutzer an den Azure AD B2C-Autorisierungsendpunkt weiter.
1. Benutzer melden sich mit ihrem Unternehmenskonto für Azure AD an. Azure AD B2C leitet die Benutzer an den Azure AD-Autorisierungsendpunkt weiter, wo sie sich mit ihrem Geschäftskonto anmelden.
1. Wenn die Azure AD-SSO-Sitzung aktiv ist, gibt Azure AD ein Zugriffstoken aus, ohne die Benutzer zur erneuten Anmeldung aufzufordern. Wenn die Azure AD-Sitzung abläuft oder ungültig wird, werden Benutzer aufgefordert, sich erneut anzumelden.

![Diagramm des Anmeldeflows von OpenID Connect.](./media/publish-app-to-azure-ad-app-gallery/app-gallery-sign-in-flow.png)

Abhängig von der SSO-Sitzung des Benutzers und den Azure AD-Identitätseinstellungen wird der Benutzer möglicherweise zu Folgendem aufgefordert:

- Angeben einer E-Mail-Adresse oder Telefonnummer.
- Eingeben des Kennworts oder Anmelden über die [Microsoft Authenticator-App](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6).
- Durchführen einer mehrstufigen Authentifizierung.
- Akzeptieren der Einwilligungsseite. Der Mandantenadministrator Ihres Kunden kann [einer App eine mandantenweite Administratoreinwilligung erteilen](../active-directory/manage-apps/grant-admin-consent.md). Wenn die Einwilligung erteilt ist, wird den Benutzern keine Einwilligungsseite angezeigt.

Nach erfolgreicher Anmeldung gibt Azure AD ein Token an Azure AD B2C zurück. Azure AD B2C überprüft und liest die Tokenansprüche und gibt dann ein Token an Ihre Anwendung zurück.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="step-1-register-your-application-in-azure-ad-b2c"></a>Schritt 1: Registrieren Ihrer Anwendung bei Azure AD B2C

Um die Anmeldung bei Ihrer App über Azure AD B2C zu ermöglichen, müssen Sie Ihre App im Azure AD B2C-Verzeichnis registrieren. Durch das Registrieren Ihrer App wird eine Vertrauensstellung zwischen der App und Azure AD B2C eingerichtet. 

Falls noch nicht geschehen, führen Sie die Schritte zum [Registrieren einer Webanwendung](tutorial-register-applications.md) und [Aktivieren der impliziten Genehmigung von ID-Token](tutorial-register-applications.md#enable-id-token-implicit-grant) aus. Später registrieren Sie diese App beim Azure-App-Katalog.

## <a name="step-2-set-up-sign-in-for-multitenant-azure-ad"></a>Schritt 2: Einrichten der Anmeldung für mehrinstanzenfähiges Azure AD

Um Mitarbeitern und Benutzern in einem beliebigen Azure AD-Mandanten die Anmeldung über Azure AD B2C zu ermöglichen, lesen Sie die Anleitungen zum [Einrichten der Anmeldung für mehrinstanzenfähiges Azure AD](identity-provider-azure-ad-multi-tenant.md?pivots=b2c-custom-policy).

## <a name="step-3-prepare-your-app"></a>Schritt 3: Vorbereiten Ihrer App

Kopieren Sie in Ihrer App die RUL des Anmeldeendpunkts. Wenn Sie das [Webanwendungsbeispiel](configure-authentication-sample-web-app.md) verwenden, lautet die URL `https://localhost:5001/MicrosoftIdentity/Account/SignIn?`. An diese URL leitet der Azure AD-App-Katalog Benutzer weiter, damit diese sich bei Ihrer App anmelden können.

In Produktionsumgebungen handelt es sich bei dem Umleitungs-URI der App-Registrierung in der Regel um einen öffentlich zugänglichen Endpunkt, an dem Ihre App ausgeführt wird, z. B. `https://woodgrovedemo.com/Account/SignIn`. Die Antwort-URL muss mit `https` beginnen.

## <a name="step-4-publish-your-azure-ad-b2c-app"></a>Schritt 4: Veröffentlichen Ihrer Azure AD B2C-App

Als Letztes fügen Sie die mehrinstanzenfähige App zum Azure AD-App-Katalog hinzu. Befolgen Sie die Anweisungen unter [Veröffentlichen Ihrer App im Azure AD-App-Katalog](../active-directory/develop/v2-howto-app-gallery-listing.md). Um die App zum App-Katalog hinzuzufügen, gehen Sie folgendermaßen vor:

1. [Erstellen und veröffentlichen Sie die Dokumentation.](../active-directory/develop/v2-howto-app-gallery-listing.md#step-5---create-and-publish-documentation)
1. [Übermitteln Sie Ihre App](../active-directory/develop/v2-howto-app-gallery-listing.md#step-6---submit-your-app) mit den folgenden Informationen:

    |Frage  |Von Ihnen bereitzustellende Antwort  |
    |---------|---------|
    |Welche Art von Anforderung möchten Sie übermitteln?| Wählen Sie **Meine Anwendung im Katalog listen** aus.|
    |Welches Feature möchten Sie aktivieren, wenn Ihre Anwendung im Katalog gelistet wird? | Wählen Sie **Verbund-SSO (SAML, WS-Fed und OpenID Connect)** aus. | 
    | Anwendungsverbundprotokoll auswählen| Wählen Sie **OpenID Connect und OAuth 2.0** aus. |
    | Anwendungs-ID (Client) | Geben Sie die ID [Ihrer Azure AD B2C-Anwendung](#step-1-register-your-application-in-azure-ad-b2c) an. |
    | Anmelde-URL der Anwendung|Geben Sie die Anmelde-URL der App ein, so wie sie in [Schritt 3: Vorbereiten Ihrer App](#step-3-prepare-your-app) konfiguriert wurde.|
    | Mehrere Mandanten| Wählen Sie **Ja** aus. |
    | | |

## <a name="next-steps"></a>Nächste Schritte

- Finden Sie heraus, wie Sie [Ihre App im Azure AD-App-Katalog veröffentlichen](../active-directory/develop/v2-howto-app-gallery-listing.md).
