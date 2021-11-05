---
title: Autorisieren von Entwicklerkonten mithilfe von Azure Active Directory B2C
titleSuffix: Azure API Management
description: Es wird beschrieben, wie Sie für Benutzer des Entwicklerportals in Azure API Management die Autorisierung per Azure Active Directory B2C durchführen.
services: api-management
author: dlepow
ms.service: api-management
ms.topic: how-to
ms.date: 09/28/2021
ms.author: danlep
ms.openlocfilehash: e286cbb64078a2568d2aa3d0c523d782445470d4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131049138"
---
# <a name="how-to-authorize-developer-accounts-by-using-azure-active-directory-b2c-in-azure-api-management"></a>Autorisieren von Entwicklerkonten mithilfe von Azure Active Directory B2C in Azure API Management


Bei Azure Active Directory B2C handelt es sich um eine Lösung zur Cloudidentitätsverwaltung für kundenorientierte Web- und Mobilanwendungen. Sie können damit den Zugriff auf Ihr API Management-Entwicklerportal verwalten. 

In diesem Tutorial lernen Sie die in Ihrem API Management-Dienst für die Integration von Azure Active Directory B2C erforderliche Konfiguration kennen. Wie später in diesem Artikel angemerkt, unterscheiden sich bei Verwendung des veralteten Legacy-Entwicklerportals einige Schritte.

Informationen zum Aktivieren des Zugriffs auf das Entwicklerportal mithilfe einer klassischen Azure Active Directory-Instanz finden Sie unter [Autorisieren von Entwicklerkonten mithilfe von Active Directory](api-management-howto-aad.md).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure Active Directory B2C-Mandant, auf dem eine Anwendung erstellt werden kann. Weitere Informationen finden Sie unter [Azure Active Directory B2C – Übersicht](../active-directory-b2c/overview.md).
* Eine API Management-Instanz. Sollten Sie noch über keine verfügen, [erstellen Sie eine neue Azure API Management-Instanz](get-started-create-service-instance.md).

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="configure-sign-up-and-sign-in-user-flow"></a>Konfigurieren des Benutzerflows für die Registrierung und Anmeldung

In diesem Abschnitt erstellen Sie einen Benutzerflow auf Ihrem Azure Active Directory B2C-Mandanten, der sowohl Richtlinien für die Registrierung als auch für die Anmeldung enthält. Eine ausführliche Beschreibung der Schritte finden Sie unter [Erstellen von Benutzerflows und benutzerdefinierten Richtlinien in Azure Active Directory B2C](../active-directory-b2c/tutorial-create-user-flows.md?pivots=b2c-us).

1. Greifen Sie im [Azure-Portal](https://portal.azure.com) auf Ihren Azure Active Directory B2C-Mandanten zu.
1. Wählen Sie unter **Richtlinien** die Option **Benutzerflows** >  **+ Neuer Benutzerflow** aus.
1. Wählen Sie auf der Seite **Benutzerflow erstellen** den Benutzerflow **Registrierung und Anmeldung** aus.
1. Geben Sie die folgenden Informationen ein:
    1. Geben Sie einen eindeutigen Namen für den Benutzerflow ein.
    1. Wählen Sie unter **Identitätsanbieter** die Option **E-Mail-Registrierung** aus.
    1. Wählen Sie unter **Benutzerattribute und Tokenansprüche** die Attribute und Ansprüche aus, die für das API Management-Entwicklerportal benötigt werden (für das Legacy-Entwicklerportal nicht erforderlich).
         ![Anwendungsansprüche](./media/api-management-howto-aad-b2c/api-management-application-claims.png)
        * **Attribute**: Vorname, Nachname
        * **Ansprüche**: E-Mail-Adressen, Vorname, Nachname, Objekt-ID des Benutzers
1. Klicken Sie auf **Erstellen**.

## <a name="configure-identity-provider-for-developer-portal"></a>Konfigurieren des Identitätsanbieters für das Entwicklerportal

1. Öffnen Sie einen separaten Tab mit dem [Azure-Portal](https://portal.azure.com), und navigieren Sie zu Ihrer API Management-Instanz.
1. Wählen Sie unter **Entwicklerportal** die Option **Identitäten** >  **+ Hinzufügen** aus.
1. Wählen Sie auf der Seite **Identitätsanbieter hinzufügen** die Option **Azure Active Directory B2C** aus.
1. Kopieren Sie im Fenster **Identitätsanbieter hinzufügen** die **Umleitungs-URL**.

    :::image type="content" source="media/api-management-howto-aad-b2c/b2c-identity-provider-redirect-url.png" alt-text="Kopieren der Umleitungs-URL":::    

1. Wechseln Sie im Azure-Portal zurück zum Browsertab für Ihren Azure Active Directory B2C-Mandanten. Wählen Sie **App-Registrierungen** >   **+ Neue Registrierung** aus.
1. Geben Sie auf der Seite **Anwendung registrieren** die Registrierungsinformationen Ihrer Anwendung ein.
    * Geben Sie im Abschnitt **Name** einen Anwendungsnamen Ihrer Wahl ein.
    * Wählen Sie im Abschnitt **Unterstützte Kontotypen** den Kontotyp aus, der für Ihr Szenario geeignet ist. Wählen Sie zur Abdeckung einer größeren Kundengruppe die Option **Konten in einem beliebigen Identitätsanbieter oder Organisationsverzeichnis (zum Authentifizieren von Benutzern mit Benutzerflows)** aus. Weitere Informationen finden Sie unter [Registrieren einer Client-App](../active-directory/develop/quickstart-register-app.md#register-an-application).
    * Geben Sie unter **Umleitungs-URI** die Umleitungs-URL ein, die Sie aus Ihrer API Management-Instanz kopiert haben.
    * Wählen Sie unter **Berechtigungen** die Option **Administratoreinwilligung für openid- und offline_access-Berechtigungen erteilen** aus.
    * Wählen Sie **Registrieren** aus, um die Anwendung zu erstellen.

    :::image type="content" source="media/api-management-howto-aad-b2c/b2c-app-registration.png" alt-text="Registrieren einer neuen Anwendung":::

1. Suchen Sie auf der App-Seite **Übersicht** nach der Option **Anwendungs-ID (Client)** , und kopieren Sie den darin enthaltenen Wert in die Zwischenablage.

    :::image type="content" source="media/api-management-howto-aad-b2c/b2c-app-id.png" alt-text="Anwendungs-ID":::
1. Wechseln Sie zurück zum API Management-Bereich **Identitätsanbieter hinzufügen**, und fügen Sie die ID in das Textfeld **Client-ID** ein.
1. Wechseln Sie zurück zur B2C-App-Registrierung. Wählen Sie **Zertifikate und Geheimnisse** >  **+ Neuer geheimer Clientschlüssel** aus. 
    :::image type="content" source="media/api-management-howto-aad-b2c/generate-app-key.png" alt-text="Erstellen eines geheimen Clientschlüssels"::: 
   * Geben Sie auf der Seite **Geheimen Clientschlüssel hinzufügen** eine **Beschreibung** ein, und wählen Sie **Hinzufügen** aus.
   * Notieren Sie sich den Schlüssel, und bewahren Sie ihn an einem sicheren Ort auf. Dieser Geheimniswert kann nach Verlassen dieser Seite nicht erneut angezeigt werden.
1. Wechseln Sie zurück zum API Management-Bereich **Identitätsanbieter hinzufügen**, und fügen Sie den Schlüssel in das Textfeld **Geheimer Clientschlüssel** ein.
1. Wechseln Sie zurück zur B2C-App-Registrierung. Wählen Sie im linken Menü unter **Verwalten** die Option **Authentifizierung** aus.
    * Aktivieren Sie unter **Implizite Genehmigung** und Hybridflows die beiden Kontrollkästchen **Zugriffstoken** und **ID-Token**.
    * Wählen Sie **Speichern** aus.
1. Wechseln Sie zurück zur API Management-Seite **Identitätsanbieter hinzufügen**.
    * Geben Sie unter **Anmeldemandant** den Domänennamen des Azure Active Directory B2C-Mandanten an.
    * Über das Feld **Autorität** können Sie steuern, welche Azure Active Directory B2C-Anmelde-URL verwendet wird. Legen Sie den Wert auf **<Name_Ihres_b2c_Mandanten>.b2clogin.com** fest.
    * Geben Sie die **Registrierungsrichtlinie** und die **Anmelderichtlinie** aus den Richtlinien des B2C-Mandanten an.
    * Optional können Sie die Richtlinien für die **Profilbearbeitung** und die **Kennwortzurücksetzung** angeben.

         :::image type="content" source="media/api-management-howto-aad-b2c/add-identity-provider.png" alt-text="Konfiguration des Active Directory B2C-Identitätsanbieters":::
1. Wählen Sie die Option **Speichern** aus, nachdem Sie die gewünschte Konfiguration angegeben haben.

Nach dem Speichern der Änderungen können Entwickler neue Konten erstellen und sich am Entwicklerportal anmelden, indem sie Azure Active Directory B2C verwenden.

## <a name="developer-portal---add-azure-active-directory-b2c-account-authentication"></a>Entwicklerportal – Hinzufügen der Azure Active Directory B2C-Kontoauthentifizierung

> [!IMPORTANT]
> Beim Erstellen oder Aktualisieren der Einstellungen für die Azure Active Directory B2C-Konfiguration müssen Sie das [Entwicklerportal erneut veröffentlichen](api-management-howto-developer-portal-customize.md#publish), damit die Änderungen wirksam werden.

Im Entwicklerportal können Sie sich über das Widget **Schaltfläche „Anmelden“: OAuth** bei Azure Active Directory B2C anmelden. Das Widget ist auf der Anmeldeseite des standardmäßigen Entwicklerportals bereits integriert.

1. Für die Registrierung über Azure Active Directory B2C öffnen Sie ein neues Browserfenster und navigieren zum Entwicklerportal. Wählen Sie **Anmelden**.

1. Wählen Sie auf der Seite **Anmelden** die Option **Azure Active Directory B2C** aus.

    :::image type="content" source="media/api-management-howto-aad-b2c/developer-portal-sign-in.png" alt-text="Anmelden beim Entwicklerportal":::
1. Sie werden zu der Registrierungsrichtlinie weitergeleitet, die Sie im vorherigen Abschnitt konfiguriert haben. Auswählen der Registrierung mit Ihrer E-Mail-Adresse auf dem Active Directory B2C-Mandanten

Wenn die Registrierung abgeschlossen ist, werden Sie zurück zum Entwicklerportal geleitet. Sie sind jetzt am Entwicklerportal für Ihre API Management-Dienstinstanz angemeldet.

:::image type="content" source="media/api-management-howto-aad-b2c/developer-portal-home.png" alt-text="Anmeldung beim Entwicklerportal abgeschlossen":::

Es wird zwar automatisch ein neues Konto erstellt, wenn sich ein neuer Benutzer bei Azure Active Directory B2C anmeldet, aber Sie können trotzdem erwägen, das Widget auch auf der Registrierungsseite hinzuzufügen.

Das Widget **Registrierungsformular: OAuth** stellt ein Formular dar, das zum Registrieren bei OAuth verwendet wird.

## <a name="legacy-developer-portal---how-to-sign-up-with-azure-active-directory-b2c"></a>Legacy-Entwicklerportal: Registrieren mit Azure Active Directory B2C

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

> [!NOTE]
> Um B2C ordnungsgemäß in das Legacy-Entwicklerportal zu integrieren, verwenden Sie **Standard v1**-Benutzerflows in Kombination mit der Aktivierung der [Kennwortzurücksetzung](../active-directory-b2c/add-password-reset-policy.md), bevor Sie sich mithilfe von Azure Active Directory B2C bei einem Entwicklerkonto registrieren bzw. anmelden. 

1. Öffnen Sie ein neues Browserfenster, und wechseln Sie zum Legacy-Entwicklerportal. Klicken Sie auf die Schaltfläche **Registrieren**.

    :::image type="content" source="media/api-management-howto-aad-b2c/b2c-dev-portal.png" alt-text="Registrieren über das Legacy-Entwicklerportal":::

1. Wählen Sie die Option zum Registrieren bei **Azure Active Directory B2C**.

    :::image type="content" source="media/api-management-howto-aad-b2c/b2c-dev-portal-b2c-button.png" alt-text="Registrieren über Azure Active Directory B2C":::

1. Sie werden zu der Registrierungsrichtlinie weitergeleitet, die Sie im vorherigen Abschnitt konfiguriert haben. Wählen Sie die Registrierung über Ihre E-Mail-Adresse oder eines Ihrer vorhandenen Social Media-Konten.

   > [!NOTE]
   > Wenn Azure Active Directory B2C als einzige Option im Azure-Portal auf der Registerkarte **Identitäten** aktiviert ist, werden Sie direkt zur Registrierungsrichtlinie umgeleitet.

   :::image type="content" source="media/api-management-howto-aad-b2c/b2c-dev-portal-b2c-options.png" alt-text="Optionen für die Registrierung im Legacy-Entwicklerportal":::

   Wenn die Registrierung abgeschlossen ist, werden Sie zurück zum Entwicklerportal geleitet. Sie sind jetzt am Entwicklerportal für Ihre API Management-Dienstinstanz angemeldet.



## <a name="next-steps"></a>Nächste Schritte

*  [Azure Active Directory B2C – Übersicht]
*  [Azure Active Directory B2C: Erweiterbares Richtlinienframework]
*  [Verwenden eines Microsoft-Kontos als Identitätsanbieter in Azure Active Directory B2C]
*  [Verwenden eines Google-Kontos als Identitätsanbieter in Azure Active Directory B2C]
*  [Verwenden eines LinkedIn-Kontos als Identitätsanbieter in Azure Active Directory B2C]
*  [Verwenden eines Facebook-Kontos als Identitätsanbieter in Azure Active Directory B2C]


[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Azure Active Directory B2C – Übersicht]: ../active-directory-b2c/overview.md
[How to authorize developer accounts using Azure Active Directory]: ./api-management-howto-aad.md
[Azure Active Directory B2C: Erweiterbares Richtlinienframework]: ../active-directory-b2c/user-flow-overview.md
[Verwenden eines Microsoft-Kontos als Identitätsanbieter in Azure Active Directory B2C]: ../active-directory-b2c/identity-provider-microsoft-account.md
[Verwenden eines Google-Kontos als Identitätsanbieter in Azure Active Directory B2C]: ../active-directory-b2c/identity-provider-google.md
[Verwenden eines Facebook-Kontos als Identitätsanbieter in Azure Active Directory B2C]: ../active-directory-b2c/identity-provider-facebook.md
[Verwenden eines LinkedIn-Kontos als Identitätsanbieter in Azure Active Directory B2C]: ../active-directory-b2c/identity-provider-linkedin.md

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
