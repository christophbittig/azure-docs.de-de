---
title: Einrichten der Registrierung und Anmeldung mit OpenID Connect
titleSuffix: Azure AD B2C
description: Hier erfahren Sie, wie Sie die Registrierung und Anmeldung mit einem beliebigen OpenID Connect-Identitätsanbieter (Identity Provider, IdP) in Azure Active Directory B2C einrichten.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2021
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 688210352385e95c7253ac82d95154eaf707d216
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130066314"
---
# <a name="set-up-sign-up-and-sign-in-with-generic-openid-connect-using-azure-active-directory-b2c"></a>Einrichten der Anmeldung und des Logins mit generischem OpenID Connect unter Verwendung von Azure Active Directory B2C

[OpenID Connect](openid-connect.md) ist ein Authentifizierungsprotokoll auf Grundlage von OAuth 2.0, das für die sichere Anmeldung von Benutzern verwendet werden kann. Die meisten Identitätsanbieter, die dieses Protokoll verwenden, werden in Azure AD B2C unterstützt. 

In diesem Artikel wird erläutert, wie Sie benutzerdefinierte OpenID Connect-Identitätsanbieter zu Ihren Benutzerflows hinzufügen können.

[!INCLUDE [active-directory-b2c-https-cipher-tls-requirements](../../includes/active-directory-b2c-https-cipher-tls-requirements.md)]

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="add-the-identity-provider"></a>Hinzufügen des Identitätsanbieters

::: zone pivot="b2c-user-flow"

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) als globaler Administrator Ihres Azure AD B2C-Mandanten an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Klicken Sie links oben im Azure-Portal auf **Alle Dienste**, suchen Sie nach **Azure AD B2C**, und klicken Sie darauf.
1. Wählen Sie **Identitätsanbieter** und dann **Neuer OpenID Connect-Anbieter** aus.
1. Geben Sie einen **Namen** ein. Geben Sie beispielsweise *Contoso* ein.

::: zone-end

::: zone pivot="b2c-custom-policy"

Definieren Sie den OpenId Connect Identitätsanbieter, indem Sie ihn zum **ClaimsProviders** Element in der Erweiterungsdatei Ihrer Richtlinie hinzufügen.

1. Öffnen Sie die Datei *TrustFrameworkExtensions.xml*.
2. Suchen Sie nach dem Element **ClaimsProviders**. Falls das Element nicht vorhanden sein sollte, fügen Sie es unter dem Stammelement hinzu.
3. Fügen Sie ein neues **ClaimsProvider**-Element wie folgt hinzu:

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Login with Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-OpenIdConnect">
          <DisplayName>Contoso</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://your-identity-provider.com/.well-known/openid-configuration</Item>
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <!-- <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoSecret"/>
          </CryptographicKeys> -->
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

::: zone-end

## <a name="configure-the-identity-provider"></a>Konfigurieren des Identitätsanbieters

Jeder OpenID Connect-Identitätsanbieter beschreibt ein Metadatendokument, das den Großteil der erforderlichen Informationen für die Anmeldung enthält. Das Metadaten-Dokument enthält Informationen wie die zu verwendenden URLs und den Speicherort der öffentlichen Signierschlüssel des Dienstes. Das OpenID Connect-Metadatendokument befindet sich immer an einem Endpunkt, der in `.well-known/openid-configuration` endet. Geben Sie für den OpenID Connect-Identitätsanbieter, den Sie hinzufügen möchten, die Metadaten-URL ein.

::: zone pivot="b2c-user-flow"

Geben Sie im Feld **Metadaten url** die URL des OpenID Connect-Metadaten-Dokuments ein.

::: zone-end

::: zone pivot="b2c-custom-policy"

Geben Sie in den `<Item Key="METADATA">`Metadaten des technischen Profils die URL des OpenID Connect-Metadatendokuments ein.

::: zone-end

## <a name="client-id-and-secret"></a>Client-ID und Geheimnis

Damit Benutzer sich anmelden können, setzt der Identitätsanbieter voraus, dass Entwickler eine Anwendung in ihrem Dienst registrieren. Diese Anwendung verfügt über eine ID, die als die **Client-ID** und ein **Clientgeheimnis** bezeichnet wird. 

Das Clientgeheimnis ist optional. Sie müssen jedoch ein Client-Geheimnis angeben, wenn der [Antworttyp](#response-type) `code` ist, der das Geheimnis zum Austausch des Codes für das Token verwendet.

::: zone pivot="b2c-user-flow"

Um die Client-ID und das Client-Geheimnis hinzuzufügen, kopieren Sie diese Werte vom Identitätsanbieter und geben sie in die entsprechenden Felder ein.

::: zone-end

::: zone pivot="b2c-custom-policy"

Geben Sie in den Metadaten des technischen Profils `<Item Key="client_id">` die Client-ID ein.

### <a name="create-a-policy-key"></a>Erstellen eines Richtlinienschlüssels

Wenn das Kundengeheimnis erforderlich ist, speichern Sie das Kundengeheimnis, das Sie zuvor in Ihrem Azure AD B2C-Tenant gespeichert haben.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie den Filter **Verzeichnis + Abonnement** in der Symbolleiste des Portals.
3. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
4. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
5. Wählen Sie auf der Seite „Übersicht“ die Option **Framework für die Identitätsfunktion** aus.
6. Klicken Sie erst auf **Richtlinienschlüssel** und anschließend auf **Hinzufügen**.
7. Klicken Sie unter **Optionen** auf `Manual`.
8. Geben Sie einen **Namen** für den Richtlinienschlüssel ein. Beispiel: `ContosoSecret`. Dem Namen Ihres Schlüssels wird automatisch das Präfix `B2C_1A_` hinzugefügt.
9. Geben Sie im Feld **Geheimnis** den geheimen Clientschlüssel ein, den Sie zuvor notiert haben.
10. Wählen Sie für **Schlüsselverwendung** die Option `Signature` aus.
11. Klicken Sie auf **Erstellen**.
12. Im `CryptographicKeys` XML-Element fügen Sie das folgende Element hinzu:
    
    ```xml
    <CryptographicKeys>
      <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoSecret"/>
    </CryptographicKeys>
    ```

::: zone-end

## <a name="scope"></a>Bereich

Der Bereich definiert die Informationen und Berechtigungen, die Sie bei Ihrem Identitätsanbieter sammeln möchten, z. B. `openid profile`. Um das ID-Token vom Identitätsanbieter zu erhalten, muss der `openid`-Bereich angegeben werden. 

Ohne das ID-Token können Benutzer sich nicht mit dem benutzerdefinierten Identitätsanbieter bei Azure AD B2C anmelden. Andere Bereiche können (durch Leerzeichen getrennt) angefügt werden. Entnehmen Sie der Dokumentation des benutzerdefinierten Identitätsanbieters, welche anderen Bereiche möglicherweise zur Verfügung stehen.

::: zone pivot="b2c-user-flow"

Geben Sie in den Feldern **Scope** die Bereiche des Identitätsanbieters ein. Beispiel: `openid profile`.

::: zone-end

::: zone pivot="b2c-custom-policy"

Geben Sie in den `<Item Key="scope">` technischen Profil-Metadaten die Bereiche des Identitätsanbieters ein. Beispiel: `openid profile`.

::: zone-end

## <a name="response-type"></a>Antworttyp

Der Antworttyp beschreibt, welche Art von Informationen beim ersten Aufruf von `authorization_endpoint` des benutzerdefinierten Identitätsanbieters zurückgesendet werden. Die folgenden Antworttypen können verwendet werden:

* `code`: Gemäß dem [Autorisierungscodefluss](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth) wird ein Code an Azure AD B2C zurückgegeben. Azure AD B2C fährt damit fort, den `token_endpoint` für den Austausch des Codes für das Token aufzurufen.
* `id_token`: Ein ID-Token wird vom benutzerdefinierten Identitätsanbieter an Azure AD B2C zurückgegeben.

::: zone pivot="b2c-user-flow"

Wählen Sie unter **Antworttyp** je nach den Einstellungen Ihres Identitätsanbieters `code` oder `id_token`.

::: zone-end

::: zone pivot="b2c-custom-policy"

Wählen Sie in den `<Item Key="response_types">`Metadaten des technischen Profils je nach den Einstellungen Ihres Identitätsanbieters `code` oder `id_token`.

::: zone-end

## <a name="response-mode"></a>Antwortmodus

Der Antwortmodus definiert die Methode, die zum Senden der Daten vom benutzerdefinierten Identitätsanbieter zurück zu Azure AD B2C verwendet werden sollte. Die folgenden Modi können für die Antwort verwendet werden:

* `form_post`: Dieser Antwortmodus wird für die optimale Sicherheit empfohlen. Die Antwort wird mit der HTTP-`POST`-Methode übertragen, wobei Code oder Token im `application/x-www-form-urlencoded`-Format codiert wird.
* `query`: Der Code oder das Token wird als Abfrageparameter zurückgegeben.

::: zone pivot="b2c-user-flow"

Wählen Sie unter **Antwortmodus** je nach den Einstellungen Ihres Identitätsanbieters `form_post` oder `query`.

::: zone-end

::: zone pivot="b2c-custom-policy"

Wählen Sie in den `<Item Key="response_mode">`Metadaten des technischen Profils `form_post` oder `query`, je nach den Einstellungen Ihres Identitätsanbieters.

::: zone-end

## <a name="domain-hint"></a>Domänenhinweis

Der [Domänen-Hinweis](direct-signin.md) kann verwendet werden, um direkt zur Anmeldeseite des angegebenen Identitätsanbieters zu springen, anstatt den Benutzer eine Auswahl aus der Liste der verfügbaren Identitätsanbieter treffen zu lassen. 

Um diese Art von Verhalten zu ermöglichen, geben Sie einen Wert für den Domänenhinweis ein. Um direkt zum benutzerdefinierten Identitätsanbieter zu wechseln, fügen Sie den Parameter `domain_hint=<domain hint value>` beim Aufrufen von Azure AD B2C für die Anmeldung am Ende Ihrer Anforderung an.

::: zone pivot="b2c-user-flow"

Geben Sie im Element **Domain-Hinweis** einen im Domain-Hinweis verwendeten Domain-Namen ein.

::: zone-end

::: zone pivot="b2c-custom-policy"

Geben Sie im XML-Element `<Domain>contoso.com</Domain>`Technisches Profil einen Domänennamen ein, der im Domänenhinweis verwendet wird. Beispiel: `contoso.com`.

::: zone-end

## <a name="claims-mapping"></a>Anspruchszuordnung

Nachdem der benutzerdefinierte Identitätsanbieter ein ID-Token an Azure AD B2C zurückgesendet hat, muss Azure AD B2C in der Lage sein, die Ansprüche aus dem empfangenen Token den Ansprüchen zuzuordnen, die Azure AD B2C erkennt und verwendet. Ziehen Sie für jede der folgenden Zuordnungen die Dokumentation des benutzerdefinierten Identitätsanbieters zu Rate, um die Ansprüche zu verstehen, die in den Token des Identitätsanbieters zurückgegeben werden:

::: zone pivot="b2c-user-flow"

* **Benutzer-ID**: Geben Sie den Anspruch ein, der den *eindeutigen Bezeichner* für den angemeldeten Benutzer bereitstellt.
* **Anzeigename**: Geben Sie den Anspruch ein, der den *Anzeigenamen* oder den *vollständigen Namen* des Benutzers bereitstellt.
* **Vorname**: Geben Sie den Anspruch ein, der den *Vornamen* des Benutzers bereitstellt.
* **Nachname**: Geben Sie den Anspruch ein, der den *Nachnamen* des Benutzers bereitstellt.
* **E-Mail**: Geben Sie den Anspruch ein, der die *E-Mail-Adresse* des Benutzers bereitstellt.


::: zone-end

::: zone pivot="b2c-custom-policy"

Das Element `OutputClaims` enthält eine Liste der von Ihrem Identitätsanbieter zurückgegebenen Ansprüche. Ordnen Sie den Namen des Anspruchs, der in Ihrer Richtlinie definiert ist, dem im Identitätsanbieter definierten Namen zu. Konfigurieren Sie unter dem Element `<OutputClaims>` das Attribut `PartnerClaimType` mit dem entsprechenden Namen des Anspruchs, wie er von Ihrem Identitätsanbieter definiert wurde. 

|ClaimTypeReferenceId  |PartnerClaimType  |
|---------|---------|
| `issuerUserId`| Geben Sie den Anspruch ein, der den *eindeutigen Bezeichner* für den angemeldeten Benutzer bereitstellt.|
| `displayName`| Geben Sie den Anspruch ein, der den *Anzeigenamen* oder den *vollständigen Namen* des Benutzers bereitstellt. |
| `givenName`| Geben Sie den Anspruch ein, der den *Vornamen* des Benutzers bereitstellt.|
| `surName`| Geben Sie den Anspruch ein, der den *Nachnamen* des Benutzers bereitstellt.|
| `email`| Geben Sie den Anspruch ein, der die *E-Mail-Adresse* des Benutzers bereitstellt.|
| `identityProvider` | Geben Sie den Claim ein, der den Namen des Token-Ausstellers enthält. Beispiel: `iss`. Wenn der Identitätsanbieter den Ausstelleranspruch nicht in das Token aufnimmt, setzen Sie das `DefaultValue`-Attribut mit einem eindeutigen Bezeichner Ihres Identitätsanbieters. Beispiel: `DefaultValue="contoso.com"`.|


::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-the-identity-provider-to-a-user-flow"></a>Hinzufügen des Identitätsanbieters zu einem Benutzerflow 

1. Wählen Sie in Ihrem Azure AD B2C-Mandanten die Option **Benutzerflows** aus.
1. Klicken Sie auf den Benutzerflow, dem Sie den Identitätsanbieter hinzufügen möchten. 
1. Wählen Sie unter **Soziales Netzwerk als Identitätsanbieter** den Identitätsanbieter aus, den Sie hinzugefügt haben. Zum Beispiel *Contoso*.
1. Wählen Sie **Speichern** aus.

## <a name="test-your-user-flow"></a>Testen des Benutzerflows

1. Um die Richtlinie zu testen, wählen Sie **Benutzerflow ausführen** aus.
1. Wählen Sie für **Anwendung** die Webanwendung *testapp1* aus, die Sie zuvor registriert haben. Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Wählen Sie die Schaltfläche **Benutzerflow ausführen** aus.
1. Wählen Sie auf der Registrierungs- oder Anmeldeseite den Identitätsanbieter aus, den Sie anmelden möchten. Zum Beispiel *Contoso*.

Wenn der Anmeldevorgang erfolgreich verlaufen ist, wird der Browser an `https://jwt.ms` umgeleitet und dadurch der Inhalt des von Azure AD B2C zurückgegebenen Tokens angezeigt.

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

1. Wählen Sie die Richtliniendatei für die vertrauende Seite aus, z. B. `B2C_1A_signup_signin`.
1. Wählen Sie für **Anwendung** eine Webanwendung aus, die Sie [zuvor registriert haben](tutorial-register-applications.md). Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Wählen Sie die Schaltfläche **Jetzt ausführen** aus.
1. Wählen Sie auf der Registrierungs- oder Anmeldeseite **Contoso**, um sich mit einem Google-Konto anzumelden.

Wenn der Anmeldevorgang erfolgreich verlaufen ist, wird der Browser an `https://jwt.ms` umgeleitet und dadurch der Inhalt des von Azure AD B2C zurückgegebenen Tokens angezeigt.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie im [OpenId Connect technischen Profil](openid-connect-technical-profile.md) Referenzhandbuch.

::: zone-end
