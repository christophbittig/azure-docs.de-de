---
title: Einrichten der Registrierung und Anmeldung mit einem Microsoft-Konto
titleSuffix: Azure AD B2C
description: Bereitstellen von Registrierung und Anmeldung für Kunden mit Microsoft-Konten in Ihren Anwendungen mithilfe von Azure Active Directory B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/16/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 5da695eeee6ef9ed26dcdbd29cf5b5684e277b12
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130227742"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Einrichten der Registrierung und Anmeldung mit einem Microsoft-Konto mithilfe von Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

### <a name="verify-the-applications-publisher-domain"></a>Überprüfen der Herausgeberdomäne einer Anwendung
Ab November 2020 werden neue Anwendungsregistrierungen in der Benutzereinwilligungsaufforderung als nicht überprüft angezeigt, es sei denn, [die Herausgeberdomäne der Anwendung wurde überprüft](../active-directory/develop/howto-configure-publisher-domain.md) ***und*** die Identität des Unternehmens wurde durch das Microsoft Partner Network überprüft und der Anwendung zugeordnet. ([Erfahren Sie mehr](../active-directory/develop/publisher-verification-overview.md) über diese Änderung.) Beachten Sie, dass für Azure AD B2C-Benutzerflows die Domäne des Herausgebers nur angezeigt wird, wenn ein Microsoft-Konto oder ein anderer [Azure AD](../active-directory-b2c/identity-provider-azure-ad-single-tenant.md)-Mandant als Identitätsanbieter verwendet wird. Gehen Sie wie folgt vor, um diese neuen Anforderungen zu erfüllen:

1. [Überprüfen Sie Ihre Unternehmensidentität mit Ihrem Microsoft Partner Network-Konto (MPN-Konto)](/partner-center/verification-responses). Bei diesem Prozess werden Informationen zu Ihrem Unternehmen und zum primären Kontakt Ihres Unternehmens überprüft.
1. Schließen Sie mithilfe einer der folgenden Optionen die Herausgeberüberprüfung ab, um Ihr MPN-Konto Ihrer App-Registrierung zuzuordnen:
   - Wenn sich die App-Registrierung für den Identitätsanbieter „Microsoft-Konto“ in einem Azure AD-Mandanten befindet, [überprüfen Sie Ihre App im App-Registrierungsportal](../active-directory/develop/mark-app-as-publisher-verified.md).
   - Wenn sich Ihre App-Registrierung für den Identitätsanbieter „Microsoft-Konto“ in einem Azure AD B2C-Mandanten befindet, [markieren Sie Ihre App mithilfe einer Microsoft Graph-API als vom Herausgeber verifiziert](../active-directory/develop/troubleshoot-publisher-verification.md#making-microsoft-graph-api-calls) (z. B. mithilfe von Graph Explorer). Die Benutzeroberfläche zum Festlegen des verifizierten Herausgebers einer App ist derzeit für Azure AD B2C-Mandanten deaktiviert.

## <a name="create-a-microsoft-account-application"></a>Erstellen einer Microsoft-Kontoanwendung

Wenn Sie die Anmeldung für Benutzer mit einem Microsoft-Konto in Azure Active Directory B2C (Azure AD B2C) aktivieren möchten, müssen Sie eine Anwendung im [Azure-Portal](https://portal.azure.com) erstellen. Weitere Informationen finden Sie unter [Registrieren einer Anwendung bei Microsoft Identity Platform](../active-directory/develop/quickstart-register-app.md). Wenn Sie noch kein Microsoft-Konto haben, können Sie unter [https://www.live.com/](https://www.live.com/) eins erstellen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse + Abonnements** das Azure AD-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Klicken Sie links oben im Azure-Portal auf **Alle Dienste**, suchen Sie nach **App-Registrierungen**, und wählen Sie dann diese Option aus.
1. Wählen Sie **Neue Registrierung** aus.
1. Geben Sie einen **Namen** für Ihre Anwendung ein. Zum Beispiel *MSAapp1*.
1. Wählen Sie unter **Unterstützte Kontotypen** die Option **Accounts in any organizational directory (Any Azure AD directory - Multitenant) and personal Microsoft accounts (e.g. Skype, Xbox)** (Konten in allen Organisationsverzeichnissen (beliebiges Azure AD-Verzeichnis: mehrere Mandanten) und persönliche Microsoft-Konten (z. B. Skype, Xbox)) aus.

   Weitere Informationen zur Auswahl anderer Kontotypen finden Sie unter [Schnellstart: Registrieren einer Anwendung bei der Microsoft Identity Platform](../active-directory/develop/quickstart-register-app.md).
1. Wählen Sie unter **Umleitungs-URI (optional)** die Option **Web** aus, und geben Sie `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` ein. Bei Verwendung einer [benutzerdefinierten Domäne](custom-domain.md) geben Sie `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` ein. Ersetzen Sie `your-tenant-name` durch den Namen Ihres Mandanten und `your-domain-name` durch Ihre benutzerdefinierte Domäne.
1. Wählen Sie **Registrieren** aus.
1. Notieren Sie die **Anwendungs-ID (Client)** , die auf der Seite „Übersicht“ der Anwendung angezeigt wird. Sie benötigen die Client-ID beim Konfigurieren des Identitätsanbieters im nächsten Abschnitt.
1. Wählen Sie **Zertifikate und Geheimnisse** aus.
1. Klicken Sie auf **Neuer geheimer Clientschlüssel**.
1. Geben Sie eine **Beschreibung** für das Geheimnis ein (z.B. *Anwendungskennwort 1*), und klicken Sie dann auf **Hinzufügen**.
1. Notieren Sie das in der Spalte **Wert** angezeigte Anwendungskennwort. Sie benötigen den geheimen Clientschlüssel beim Konfigurieren des Identitätsanbieters im nächsten Abschnitt.

::: zone pivot="b2c-user-flow"

## <a name="configure-microsoft-as-an-identity-provider"></a>Konfigurieren von Microsoft als Identitätsanbieter

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) als globaler Administrator Ihres Azure AD B2C-Mandanten an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Klicken Sie links oben im Azure-Portal auf **Alle Dienste**, suchen Sie nach **Azure AD B2C**, und klicken Sie darauf.
1. Wählen Sie **Identitätsanbieter** und dann **Microsoft-Konto** aus.
1. Geben Sie einen **Namen** ein. Beispiel: *MSA*.
1. Geben Sie für die **Client-ID** die Anwendungs-ID (Client) der Azure AD-Anwendung ein, die Sie zuvor erstellt haben.
1. Geben Sie für **Geheimer Clientschlüssel** den zuvor notierten geheimen Clientschlüssel ein.
1. Wählen Sie **Speichern** aus.

## <a name="add-microsoft-identity-provider-to-a-user-flow"></a>Hinzufügen von Microsoft als Identitätsanbieter zu einem Benutzerflow 

Der Microsoft-Identitätsanbieter ist jetzt eingerichtet, er ist jedoch noch auf keiner der Anmeldeseiten verfügbar. So fügen Sie den Microsoft-Identitätsanbieter einem Benutzerflow hinzu:

1. Wählen Sie in Ihrem Azure AD B2C-Mandanten die Option **Benutzerflows** aus.
1. Klicken Sie auf den Benutzerflow, dem Sie Microsoft als Identitätsanbieter hinzufügen möchten.
1. Wählen Sie unter **Soziales Netzwerk als Identitätsanbieter** die Option **Microsoft-Konto** aus.
1. Wählen Sie **Speichern** aus.
1. Um die Richtlinie zu testen, wählen Sie **Benutzerflow ausführen** aus.
1. Wählen Sie für **Anwendung** die Webanwendung *testapp1* aus, die Sie zuvor registriert haben. Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Wählen Sie die Schaltfläche **Benutzerflow ausführen** aus.
1. Wählen Sie auf der Registrierungs- oder Anmeldeseite die Option **Microsoft** aus, um sich mit dem Microsoft-Konto anzumelden.

Wenn der Anmeldevorgang erfolgreich verlaufen ist, wird der Browser an `https://jwt.ms` umgeleitet und dadurch der Inhalt des von Azure AD B2C zurückgegebenen Tokens angezeigt.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="configuring-optional-claims"></a>Konfigurieren optionaler Ansprüche

Wenn Sie die Ansprüche `family_name` und `given_name` von Azure AD erhalten möchten, können Sie optionale Ansprüche für Ihre Anwendung im Azure-Portal oder im Anwendungsmanifest konfigurieren. Weitere Informationen finden Sie unter [Bereitstellen optionaler Ansprüche für Ihre Azure AD-App](../active-directory/develop/active-directory-optional-claims.md).

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Suchen Sie nach **Azure Active Directory**, und wählen Sie diese Option aus.
1. Wählen Sie im Abschnitt **Verwalten** die Option **App-Registrierungen** aus.
1. Wählen Sie in der Liste die Anwendung aus, für die Sie optionale Ansprüche konfigurieren möchten.
1. Wählen Sie im Abschnitt **Verwalten** die Option **Tokenkonfiguration (Vorschau)** aus.
1. Wählen Sie **Optionalen Anspruch hinzufügen** aus.
1. Wählen Sie den Tokentyp aus, den Sie konfigurieren möchten.
1. Wählen Sie die hinzuzufügenden optionalen Ansprüche aus.
1. Klicken Sie auf **Hinzufügen**.

## <a name="create-a-policy-key"></a>Erstellen eines Richtlinienschlüssels

Da Sie die Anwendung nun in Ihrem Azure AD-Mandanten erstellt haben, müssen Sie den geheimen Clientschlüssel der Anwendung in Ihrem Azure AD B2C-Mandanten speichern.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie auf der Seite „Übersicht“ die Option **Framework für die Identitätsfunktion** aus.
1. Klicken Sie erst auf **Richtlinienschlüssel** und anschließend auf **Hinzufügen**.
1. Klicken Sie unter **Optionen** auf `Manual`.
1. Geben Sie einen **Namen** für den Richtlinienschlüssel ein. Beispiel: `MSASecret`. Dem Namen Ihres Schlüssels wird automatisch das Präfix `B2C_1A_` hinzugefügt.
1. Geben Sie unter **Geheimnis** den geheimen Clientschlüssel ein, den Sie im vorherigen Abschnitt notiert haben.
1. Wählen Sie für **Schlüsselverwendung** die Option `Signature` aus.
1. Klicken Sie auf **Erstellen**.

## <a name="configure-microsoft-as-an-identity-provider"></a>Konfigurieren von Microsoft als Identitätsanbieter

Damit sich Benutzer mit einem Microsoft-Konto anmelden können, müssen Sie das Konto als Anspruchsanbieter definieren, mit dem Azure AD B2C über einen Endpunkt kommunizieren kann. Der Endpunkt bietet eine Reihe von Ansprüchen, mit denen Azure AD B2C überprüft, ob ein bestimmter Benutzer authentifiziert wurde.

Sie können Azure AD als Anspruchsanbieter definieren, indem Sie in der Erweiterungsdatei Ihrer Richtlinie das Element **ClaimsProvider** hinzufügen.

1. Öffnen Sie die Richtliniendatei *TrustFrameworkExtensions.xml*.
1. Suchen Sie nach dem Element **ClaimsProviders**. Falls das Element nicht vorhanden sein sollte, fügen Sie es unter dem Stammelement hinzu.
1. Fügen Sie ein neues **ClaimsProvider**-Element wie folgt hinzu:

    ```xml
    <ClaimsProvider>
      <Domain>live.com</Domain>
      <DisplayName>Microsoft Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="MSA-MicrosoftAccount-OpenIdConnect">
          <DisplayName>Microsoft Account</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">https://login.live.com</Item>
            <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="client_id">Your Microsoft application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
            <OutputClaim ClaimTypeReferenceId="email" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Ersetzen Sie den Wert von **client_id** durch die zuvor notierte *Application (client) ID* (Anwendungs-ID (Client)) der Azure AD-Anwendung.
1. Speichern Sie die Datei .

Sie haben Ihre Richtlinie jetzt so konfiguriert, dass Azure AD B2C mit Ihrer Microsoft-Kontoanwendung in Azure AD kommunizieren kann.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-MicrosoftAccount-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Testen der benutzerdefinierten Richtlinie

1. Wählen Sie die Richtliniendatei für die vertrauende Seite aus, z. B. `B2C_1A_signup_signin`.
1. Wählen Sie für **Anwendung** eine Webanwendung aus, die Sie [zuvor registriert haben](tutorial-register-applications.md). Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Wählen Sie die Schaltfläche **Jetzt ausführen** aus.
1. Wählen Sie auf der Registrierungs- oder Anmeldeseite die Option **Microsoft** aus, um sich mit dem Microsoft-Konto anzumelden.

Wenn der Anmeldevorgang erfolgreich verlaufen ist, wird der Browser an `https://jwt.ms` umgeleitet und dadurch der Inhalt des von Azure AD B2C zurückgegebenen Tokens angezeigt.

::: zone-end