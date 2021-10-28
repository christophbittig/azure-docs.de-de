---
title: Einrichten der Registrierung und Anmeldung mit einem QQ-Konto mithilfe von Azure Active Directory B2C
description: Bereitstellen von Registrierung und Anmeldung für Kunden mit QQ-Konten in Ihren Anwendungen mithilfe von Azure Active Directory B2C.
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
ms.openlocfilehash: ea4be3d86c45982b61c4e3df80e6fa4f5144fb92
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130227767"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Einrichten der Registrierung und Anmeldung mit einem QQ-Konto mithilfe von Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

::: zone-end

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-qq-application"></a>Erstellen einer QQ-Anwendung

Wenn Sie die Anmeldung für Benutzer mit einem QQ-Konto in Azure Active Directory B2C (Azure AD B2C) aktivieren möchten, müssen Sie eine Anwendung im [QQ-Entwicklerportal](http://open.qq.com) erstellen. Wenn Sie noch über kein QQ-Konto verfügen, können Sie sich unter [https://ssl.zc.qq.com](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033) registrieren.

### <a name="register-for-the-qq-developer-program"></a>Registrieren beim QQ-Entwicklerprogramm

1. Melden Sie sich beim [QQ-Entwicklerportal](http://open.qq.com) mit den Anmeldeinformationen für Ihr QQ-Konto an.
1. Rufen Sie nach der Anmeldung die Seite [https://open.qq.com/reg](https://open.qq.com/reg) auf, um sich als Entwickler zu registrieren.
1. Wählen Sie **个人** (Einzelner Entwickler) aus.
1. Geben Sie die erforderlichen Informationen ein, und wählen Sie **下一步** (Nächster Schritt) aus.
1. Schließen Sie den E-Mail-Überprüfungsprozess ab. Es dauert einige Tage, bis Ihre Registrierung als Entwickler genehmigt wird.

### <a name="register-a-qq-application"></a>Registrieren einer QQ-Anwendung

1. Wechseln Sie zu [https://connect.qq.com/index.html](https://connect.qq.com/index.html).
1. Wählen Sie **应用管理** (App-Verwaltung) aus.
1. Wählen Sie **创建应用** (App erstellen) aus, und geben Sie die erforderlichen Informationen ein.
1. Geben Sie unter **授权回调域** (Rückruf-URL) `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` ein. Bei Verwendung einer [benutzerdefinierten Domäne](custom-domain.md) geben Sie `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` ein. Ersetzen Sie `your-tenant-name` durch den Namen Ihres Mandanten und `your-domain-name` durch Ihre benutzerdefinierte Domäne.
1. Wählen Sie **创建应用** (App erstellen) aus.
1. Wählen Sie auf der Bestätigungsseite die Option **应用管理** (App-Verwaltung) aus, um zur Seite für die App-Verwaltung zurückzukehren.
1. Wählen Sie neben der erstellten App die Option **查看** (Anzeigen) aus.
1. Wählen Sie **修改** (Bearbeiten) aus.
1. Kopieren Sie die **APP-ID** und den **APP-SCHLÜSSEL**. Sie benötigen beide Werte, um den Identitätsanbieter Ihrem Mandanten hinzuzufügen.

::: zone pivot="b2c-user-flow"

## <a name="configure-qq-as-an-identity-provider"></a>Konfigurieren von QQ als Identitätsanbieter

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Suchen Sie im Azure-Portal nach **Azure AD B2C**, und wählen Sie diese Option dann aus.
1. Wählen Sie **Identitätsanbieter** und dann **QQ (Vorschau)** aus.
1. Geben Sie einen **Namen** ein. Beispiel: *QQ*.
1. Geben Sie für die **Client-ID** die App-ID der QQ-Anwendung ein, die Sie zuvor erstellt haben.
1. Geben Sie das zuvor notierte App-Geheimnis als **Geheimer Clientschlüssel** ein.
1. Wählen Sie **Speichern** aus.

## <a name="add-qq-identity-provider-to-a-user-flow"></a>Hinzufügen von QQ als Identitätsanbieter zu einem Benutzerflow 

1. Wählen Sie in Ihrem Azure AD B2C-Mandanten die Option **Benutzerflows** aus.
1. Klicken Sie auf den Benutzerflow, dem Sie QQ als Identitätsanbieter hinzufügen möchten.
1. Wählen Sie unter **Soziales Netzwerk als Identitätsanbieter** die Option **QQ** aus.
1. Wählen Sie **Speichern** aus.
1. Um die Richtlinie zu testen, wählen Sie **Benutzerflow ausführen** aus.
1. Wählen Sie für **Anwendung** die Webanwendung *testapp1* aus, die Sie zuvor registriert haben. Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Wählen Sie die Schaltfläche **Benutzerflow ausführen** aus.
1. Wählen Sie auf der Registrierungs- oder Anmeldeseite die Option **QQ** aus, um sich mit dem QQ-Konto anzumelden.

Wenn der Anmeldevorgang erfolgreich verlaufen ist, wird der Browser an `https://jwt.ms` umgeleitet und dadurch der Inhalt des von Azure AD B2C zurückgegebenen Tokens angezeigt.


::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Erstellen eines Richtlinienschlüssels

Sie müssen den geheimen Clientschlüssel speichern, den Sie zuvor in Ihrem Azure AD B2C-Mandanten notiert haben.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie auf der Seite „Übersicht“ die Option **Framework für die Identitätsfunktion** aus.
1. Klicken Sie erst auf **Richtlinienschlüssel** und anschließend auf **Hinzufügen**.
1. Klicken Sie unter **Optionen** auf `Manual`.
1. Geben Sie einen **Namen** für den Richtlinienschlüssel ein. Beispiel: `QQSecret`. Dem Namen Ihres Schlüssels wird automatisch das Präfix `B2C_1A_` hinzugefügt.
1. Geben Sie im Feld **Geheimnis** den geheimen Clientschlüssel ein, den Sie zuvor notiert haben.
1. Wählen Sie für **Schlüsselverwendung** die Option `Signature` aus.
1. Klicken Sie auf **Erstellen**.

## <a name="configure-qq-as-an-identity-provider"></a>Konfigurieren von QQ als Identitätsanbieter

Damit sich Benutzer mit einem QQ-Konto anmelden können, müssen Sie das Konto als Anspruchsanbieter definieren, mit dem Azure AD B2C über einen Endpunkt kommunizieren kann. Der Endpunkt bietet eine Reihe von Ansprüchen, mit denen Azure AD B2C überprüft, ob ein bestimmter Benutzer authentifiziert wurde.

Sie können ein QQ-Konto als Anspruchsanbieter definieren, indem Sie es in der Erweiterungsdatei Ihrer Richtlinie dem **ClaimsProviders**-Element hinzufügen.

1. Öffnen Sie die Datei *TrustFrameworkExtensions.xml*.
2. Suchen Sie nach dem Element **ClaimsProviders**. Falls das Element nicht vorhanden sein sollte, fügen Sie es unter dem Stammelement hinzu.
3. Fügen Sie ein neues **ClaimsProvider**-Element wie folgt hinzu:

    ```xml
    <ClaimsProvider>
      <Domain>qq.com</Domain>
      <DisplayName>QQ (Preview)</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="QQ-OAuth2">
          <DisplayName>QQ</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">qq</Item>
            <Item Key="authorization_endpoint">https://graph.qq.com/oauth2.0/authorize</Item>
            <Item Key="AccessTokenEndpoint">https://graph.qq.com/oauth2.0/token</Item>
            <Item Key="ClaimsEndpoint">https://graph.qq.com/oauth2.0/me</Item>
            <Item Key="scope">get_user_info</Item>
            <Item Key="HttpBinding">GET</Item>
            <Item Key="ClaimsResponseFormat">JsonP</Item>
            <Item Key="ResponseErrorCodeParamName">error</Item>
            <Item Key="external_user_identity_claim_id">openid</Item>
            <Item Key="client_id">Your QQ application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_QQSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="UserId" PartnerClaimType="openid" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="qq.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
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

4. Legen Sie **client_id** auf die Anwendungs-ID aus der Anwendungsregistrierung fest.
5. Speichern Sie die Datei .

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="QQExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="QQExchange" TechnicalProfileReferenceId="QQ-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Testen der benutzerdefinierten Richtlinie

1. Wählen Sie die Richtliniendatei für die vertrauende Seite aus, z. B. `B2C_1A_signup_signin`.
1. Wählen Sie für **Anwendung** eine Webanwendung aus, die Sie [zuvor registriert haben](tutorial-register-applications.md). Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Wählen Sie die Schaltfläche **Jetzt ausführen** aus.
1. Wählen Sie auf der Registrierungs- oder Anmeldeseite die Option **QQ** aus, um sich mit dem QQ-Konto anzumelden.

Wenn der Anmeldevorgang erfolgreich verlaufen ist, wird der Browser an `https://jwt.ms` umgeleitet und dadurch der Inhalt des von Azure AD B2C zurückgegebenen Tokens angezeigt.

::: zone-end
