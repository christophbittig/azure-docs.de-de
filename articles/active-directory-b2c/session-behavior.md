---
title: Konfigurieren des Sitzungsverhaltens – Azure Active Directory B2C
description: Hier erfahren Sie, wie Sie das Sitzungsverhalten in Azure Active Directory B2C konfigurieren.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/05/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 59d14afdbe6f4949f2761ddccb3a48a4770c3bd1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131028175"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Konfigurieren des Sitzungsverhaltens in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Mit dem einmaligen Anmelden (Single Sign-On, SSO) ist für Sicherheit und Komfort gesorgt, wenn sich Benutzer anwendungsübergreifend in Azure Active Directory B2C (Azure AD B2C) anmelden. In diesem Artikel werden die Methoden für das einmalige Anmelden beschrieben, die in Azure AD B2C verwendet werden. Sie erhalten zudem Hilfestellung beim Auswählen der am besten geeigneten SSO-Methode zum Konfigurieren Ihrer Anwendungen.

Mit der einmaligen Anmeldung melden sich Benutzer einmal mit einem einzigen Konto an und erhalten Zugriff auf mehrere Anwendungen. Dabei kann es sich unabhängig von Plattform oder Domänennamen um eine mobile Anwendung bzw. eine Web- oder Single-Page-Anwendung handeln.

Wenn sich der Benutzer anfänglich bei einer Anwendung anmeldet, speichert Azure AD B2C eine cookiebasierte Sitzung. Bei nachfolgenden Authentifizierungsanforderungen liest und überprüft Azure AD B2C die cookiebasierte Sitzung und gibt ein Zugriffstoken aus, ohne den Benutzer zur erneuten Anmeldung aufzufordern. Wenn die cookiebasierte Sitzung abläuft oder ungültig wird, wird der Benutzer zur erneuten Anmeldung aufgefordert.  

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="azure-ad-b2c-session-overview"></a>Übersicht über Azure AD B2C-Sitzungen

Die Integration mit Azure AD B2C beinhaltet drei SSO-Sitzungstypen:

- **Azure AD B2C**: Sitzung wird von Azure AD B2C verwaltet
- **Verbundidentitätsanbieter**: Sitzung wird vom Identitätsanbieter (z. B. Facebook, Salesforce oder Microsoft-Konto) verwaltet
- **Anwendung**: Sitzung wird von der mobilen Anwendung bzw. der Web- oder Single-Page-Anwendung verwaltet

![SSO-Sitzung](media/session-behavior/sso-session-types.png)

### <a name="azure-ad-b2c-session"></a>Azure AD B2C-Sitzung 

Wenn sich ein Benutzer erfolgreich mit einem lokalen oder Social Media-Konto authentifiziert, speichert Azure AD B2C eine cookiebasierte Sitzung im Browser des Benutzers. Das Cookie wird unter dem Domänennamen des Azure AD B2C-Mandanten gespeichert, z. B. `https://contoso.b2clogin.com`.

Wenn ein Benutzer sich anfänglich mit einem Verbundkonto anmeldet und sich während des Sitzungszeitfensters (Gültigkeitsdauer) bei derselben oder einer anderen App anmeldet, versucht Azure AD B2C, ein neues Zugriffstoken vom Verbundidentitätsanbieter abzurufen. Wenn die Verbundidentitätsanbieter-Sitzung abgelaufen ist oder ungültig wird, fordert der Verbundidentitätsanbieter den Benutzer zur Eingabe seiner Anmeldeinformationen auf. Wenn die Sitzung noch aktiv ist (oder wenn sich der Benutzer mit einem lokalen statt einem Verbundkonto angemeldet hat), autorisiert Azure AD B2C den Benutzer und eliminiert weitere Eingabeaufforderungen.

Sie können das Sitzungsverhalten konfigurieren. Dazu zählen auch die Gültigkeitsdauer der Sitzung und die Art, wie Azure AD B2C die Sitzung über Richtlinien und Anwendungen freigibt.

### <a name="federated-identity-provider-session"></a>Sitzung eines Verbundidentitätsanbieters

Ein Social Media- oder Unternehmensidentitätsanbieter verwaltet seine eigene Sitzung. Das Cookie wird unter dem Domänennamen des Identitätsanbieters gespeichert, z. B. `https://login.salesforce.com`. Die Verbundidentitätsanbieter-Sitzung wird nicht von Azure AD B2C gesteuert. Das Sitzungsverhalten wird stattdessen vom Verbundidentitätsanbieter bestimmt. 

Nehmen Sie das folgende Szenario als Beispiel:

1. Ein Benutzer meldet sich bei Facebook an, um seinen Feed zu überprüfen.
2. Zu einem späteren Zeitpunkt öffnet der Benutzer Ihre Anwendung und startet den Anmeldevorgang. Die Anwendung leitet den Benutzer zu Azure AD B2C um, um den Anmeldevorgang abzuschließen.
3. Auf der Registrierungs- oder Anmeldeseite von Azure AD B2C wählt der Benutzer die Anmeldung mit seinem Facebook-Konto aus. Der Benutzer wird zu Facebook umgeleitet. Wenn auf Facebook eine aktive Sitzung vorhanden ist, wird der Benutzer nicht aufgefordert, seine Anmeldeinformationen einzugeben, sondern wird sofort mit einem Facebook-Token zu Azure AD B2C umgeleitet.

### <a name="application-session"></a>Anwendungssitzung

Eine mobile Anwendung bzw. eine Web- oder Single-Page-Anwendung kann durch einen OAuth2-Zugriffstoken, ID-Token oder SAML-Token geschützt werden. Wenn ein Benutzer versucht, auf eine geschützte Ressource in der App zuzugreifen, prüft die App, ob seitens der Anwendung eine aktive Sitzung vorhanden ist. Wenn keine App-Sitzung vorhanden oder die Sitzung abgelaufen ist, leitet die App den Benutzer zur Azure AD B2C-Anmeldeseite weiter.

Die Anwendungssitzung kann eine cookiebasierte Sitzung sein, die unter dem Domänennamen der Anwendung gespeichert wird, z. B. `https://contoso.com`. Mobile Anwendungen speichern die Sitzung möglicherweise anders, verwenden aber einen ähnlichen Ansatz.

## <a name="configure-azure-ad-b2c-session-behavior"></a>Konfigurieren des Verhaltens von Azure AD B2C-Sitzungen

Sie können das Verhalten der Azure AD B2C-Sitzung konfigurieren, einschließlich:

- **Lebensdauer der Web-App-Sitzung (Minuten):** die Zeitspanne, in der das Azure AD B2C-Sitzungscookie nach erfolgreicher Authentifizierung im Browser des Benutzers gespeichert wird. Die Sitzungslebensdauer kann auf bis zu 24 Stunden festgelegt werden.

- **Timeout für Web-App-Sitzung**: Gibt an, wie eine Sitzung durch die Einstellung der Sitzungslebensdauer oder durch die Einstellung „Angemeldet bleiben“ (Keep Me Signed In, KMSI) verlängert wird.
  - **Rollierend**: Gibt an, dass die Sitzung jedes Mal verlängert wird, wenn der Benutzer eine cookiebasierte Authentifizierung ausführt (Standardeinstellung).
  - **Absolut**: Gibt an, dass sich der Benutzer nach dem angegebenen Zeitraum erneut authentifizieren muss.

- **SSO-Konfiguration:** Die Azure AD B2C-Sitzung kann mit den folgenden Bereichen konfiguriert werden:
  - **Mandant**: Dies ist die Standardeinstellung. Mit dieser Einstellung können mehrere Anwendungen und Benutzerflows in Ihrem B2C-Mandanten die gleiche Benutzersitzung gemeinsam nutzen. Beispiel: Sobald sich ein Benutzer bei einer Anwendung angemeldet hat, kann er sich auch beim Zugriff auf eine andere Anwendung nahtlos anmelden.
  - **Anwendung**: Mit dieser Einstellung können Sie eine Benutzersitzung ausschließlich für eine Anwendung beibehalten, unabhängig von anderen Anwendungen. Beispiel: Diese Einstellung können Sie verwenden, wenn sich der Benutzer bei Contoso Pharmacy anmelden soll, auch wenn er sich bereits bei Contoso Groceries angemeldet hat.
  - **Richtlinie**: Mit dieser Einstellung können Sie eine Benutzersitzung ausschließlich für einen Benutzerflow beibehalten, unabhängig von den Anwendungen, die diesen verwenden. Beispiel: Wenn sich der Benutzer bereits angemeldet und einen Multi-Factor Authentication-Schritt (MFA) abgeschlossen hat, kann er Zugriff auf Bestandteile mit höherer Sicherheitsstufe von mehreren Anwendungen erhalten, solange die an den Benutzerflow gebundene Sitzung nicht abläuft.
  - **Unterdrückt**: Diese Einstellung zwingt den Benutzer, bei jeder Ausführung der Richtlinie den gesamten Benutzerflow zu durchlaufen.
- **Angemeldet bleiben**: Verlängert die Sitzungslebensdauer durch die Verwendung eines beständigen Cookies. Wenn dieses Feature aktiviert ist und der Benutzer es auswählt, bleibt die Sitzung aktiv, auch wenn der Benutzer den Browser schließt und wieder öffnet. Die Sitzung wird nur widerrufen, wenn sich der Benutzer abmeldet. Die Funktion „Angemeldet bleiben“ gilt nur für die Anmeldung mit lokalen Konten. Das Feature „Angemeldet bleiben“ hat Vorrang vor der Sitzungslebensdauer.

::: zone pivot="b2c-user-flow"

So konfigurieren Sie das Sitzungsverhalten

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie **Benutzerflows** aus.
1. Öffnen Sie den Benutzerflow, den Sie zuvor erstellt haben.
1. Wählen Sie **Eigenschaften** aus.
1. Konfigurieren Sie **Lebensdauer der Web-App-Sitzung (Minuten)** , **Timeout für Web-App-Sitzung**, **Konfiguration des einmaligen Anmeldens** und **ID-Token in Abmeldeanforderungen erforderlich** nach Bedarf.
1. Klicken Sie auf **Speichern**.

::: zone-end

::: zone pivot="b2c-custom-policy"

Zum Ändern Ihres Sitzungsverhaltens und der SSO-Konfigurationen ist es erforderlich, dass Sie im [RelyingParty](relyingparty.md)-Element ein **UserJourneyBehaviors**-Element hinzufügen.  Das **UserJourneyBehaviors**-Element muss unmittelbar nach **DefaultUserJourney** eingefügt werden. Das **UserJourneyBehavors**-Element sollte wie in folgendem Beispiel aussehen:

```xml
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```
::: zone-end

## <a name="enable-keep-me-signed-in-kmsi"></a>Aktivieren von „Angemeldet bleiben“

Sie können das Feature „Angemeldet bleiben“ für Benutzer Ihrer webbasierten und nativen Anwendungen aktivieren, die über lokale Konten in Ihrem Azure AD B2C-Verzeichnis verfügen. Wenn Sie das Feature aktivieren, haben Benutzer die Möglichkeit, angemeldet zu bleiben, was dazu führt, dass die Sitzung auch nach dem Schließen des Browsers aktiv bleibt. Die Sitzung wird durch Festlegen eines [persistenten Cookies](cookie-definitions.md) verwaltet. Benutzer, die die Option „Angemeldet bleiben“ auswählen, können den Browser erneut öffnen, ohne zur Eingabe ihres Benutzernamens und Kennworts aufgefordert zu werden. Dieser Zugriff (das persistente Cookie) wird widerrufen, wenn sich der Benutzer abmeldet. 

![Beispiel für eine Seite zur Registrierung/Anmeldung mit dem Kontrollkästchen „Angemeldet bleiben“](./media/session-behavior/keep-me-signed-in.png)


::: zone pivot="b2c-user-flow"

„Angemeldet bleiben“ kann auf der Ebene individueller Benutzerflows konfiguriert werden. Beachten Sie Folgendes, bevor Sie „Angemeldet bleiben“ für Ihre Benutzerflows aktivieren:

- „Angemeldet bleiben“ wird nur für die **empfohlenen** Versionen der Benutzerflows für Registrierung und Anmeldung (Sign-Up/Sign-In, SUSI), Anmeldung und Profilbearbeitung unterstützt. Wenn Sie aktuell Versionen vom Typ **Standard (Legacy)** oder **Legacyvorschau v2** dieser Benutzerflows verwenden und „Angemeldet bleiben“ aktivieren möchten, müssen Sie neue, **empfohlene** Versionen dieser Benutzerflows erstellen.
- „Angemeldet bleiben“ wird bei Benutzerflows für Kennwortzurücksetzung oder Registrierung nicht unterstützt.
- Wenn Sie „Angemeldet bleiben“ für alle Anwendungen in Ihrem Mandanten aktivieren möchten, empfiehlt es sich, „Angemeldet bleiben“ für alle Benutzerflows in Ihrem Mandanten zu aktivieren. Da einem Benutzer im Zuge einer Sitzung mehrere Richtlinien begegnen können, kann es vorkommen, dass ein Benutzer auf eine Richtlinie trifft, für die „Angemeldet bleiben“ nicht aktiviert ist, was dazu führen würde, dass das Cookie für „Angemeldet bleiben“ aus der Sitzung entfernt wird.
- Auf öffentlichen Computern sollte „Angemeldet bleiben“ nicht aktiviert werden.

### <a name="configure-kmsi-for-a-user-flow"></a>Konfigurieren von „Angemeldet bleiben“ für einen Benutzerflow

So aktivieren Sie „Angemeldet bleiben“ für Ihren Benutzerflow:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie diese Option aus.
1. Wählen Sie **Benutzerflows (Richtlinien)** aus.
1. Öffnen Sie den Benutzerflow, den Sie zuvor erstellt haben.
1. Wählen Sie **Eigenschaften** aus.

1. Wählen Sie unter  **Sitzungsverhalten** die Option **"Angemeldet bleiben" in Sitzung aktivieren** aus. Geben Sie als Nächstes neben **Bei Sitzung angemeldet bleiben (Tage)** einen Wert zwischen 1 und 90 ein, um die Anzahl von Tagen anzugeben, für die eine Sitzung geöffnet bleiben kann.

   ![Aktivieren von „Angemeldet bleiben“ für eine Sitzung](media/session-behavior/enable-keep-me-signed-in.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

Benutzer sollten diese Option nicht auf öffentlichen Computern aktivieren.

### <a name="configure-the-page-identifier"></a>Konfigurieren des Seitenbezeichners

Legen Sie zum Aktivieren von KMSI das ContentDefinitions-Element `DataUri` auf [Seitenbezeichner](contentdefinitions.md#datauri) `unifiedssp` und die [Seitenversion](page-layout.md) auf *1.1.0* oder höher fest.

1. Öffnen Sie die Erweiterungsdatei Ihrer Richtlinie. Beispiel: <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>. Die Erweiterungsdatei ist eine der Richtliniendateien im Starter Pack für benutzerdefinierte Richtlinien, das Sie im unter „Voraussetzungen“ genannten Artikel [Erste Schritte mit benutzerdefinierten Richtlinien](tutorial-create-user-flows.md?pivots=b2c-custom-policy) abgerufen haben sollten.
1. Suchen Sie nach dem Element **BuildingBlocks**. Wenn das Element nicht vorhanden ist, fügen Sie es hinzu.
1. Fügen Sie das **ContentDefinitions**-Element dem Element **BuildingBlocks** der Richtlinie hinzu.

    Ihre benutzerdefinierte Richtlinie sollte wie der folgende Codeausschnitt aussehen:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

### <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>Hinzufügen der Metadaten zum selbstbestätigten technischen Profil

Um das Kontrollkästchen KMSI zur Registrierungs-und Anmeldeseite hinzuzufügen, legen Sie die `setting.enableRememberMe`-Metadaten auf TRUE fest. Überschreiben Sie in der Erweiterungsdatei die technischen Profile in SelfAsserted-LocalAccountSignin-Email.

1. Suchen Sie das ClaimsProviders-Element. Wenn das Element nicht vorhanden ist, fügen Sie es hinzu.
1. Fügen Sie dem ClaimsProviders-Element die folgenden Anspruchsanbieter hinzu:

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <Metadata>
        <Item Key="setting.enableRememberMe">True</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

1. Speichern Sie die Erweiterungsdatei.

### <a name="configure-a-relying-party-file"></a>Konfigurieren einer Datei der vertrauenden Seite

Aktualisieren Sie als Nächstes die Datei der vertrauenden Seite, mit der die erstellte User Journey initiiert wird. Mithilfe des Parameters „keepAliveInDays“ können Sie konfigurieren, wie lange das Sitzungscookie für „Angemeldet bleiben“ erhalten bleiben soll. Wenn Sie den Wert beispielsweise auf 30 festlegen, bleibt das Sitzungscookie für „Angemeldet bleiben“ 30 Tage lang erhalten. Der zulässige Bereich für diesen Wert liegt zwischen 1 und 90 Tagen. Durch Festlegen des Werts auf 0 wird die Funktion „Angemeldet bleiben“ deaktiviert.

1. Öffnen Sie Ihre benutzerdefinierte Richtliniendatei. Beispiel: *SignUpOrSignin.xml*.
1. Fügen Sie dem Knoten `<RelyingParty>` einen untergeordneten Knoten `<UserJourneyBehaviors>` hinzu, falls dieser noch nicht vorhanden ist. Er muss direkt nach `<DefaultUserJourney ReferenceId="User journey Id" />` angeordnet werden. Beispiel: `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />`.
1. Fügen Sie den folgenden Knoten als untergeordnetes Element des `<UserJourneyBehaviors>`-Elements hinzu.

    ```xml
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

Es wird empfohlen, den Wert von „SessionExpiryInSeconds“ auf einen kurzen Zeitraum (1200 Sekunden) und den Wert von „KeepAliveInDays“ auf einen relativ langen Zeitraum (30 Tage) festzulegen. Dies ist im folgenden Beispiel dargestellt:

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <UserJourneyBehaviors>
    <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
    <SessionExpiryType>Absolute</SessionExpiryType>
    <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
  </UserJourneyBehaviors>
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

::: zone-end

## <a name="sign-out"></a>Abmeldung

Wenn Sie den Benutzer bei der Anwendung abmelden möchten, reicht es nicht aus, die Cookies der Anwendung zu löschen oder die Sitzung mit dem Benutzer auf andere Weise zu beenden. Sie müssen den Benutzer für die Abmeldung zu Azure AD B2C umleiten. Ansonsten kann sich der Benutzer möglicherweise erneut bei Ihrer Anwendung authentifizieren, ohne seine Anmeldeinformationen erneut einzugeben.

Auf die Abmeldeanforderung reagiert Azure AD B2C wie folgt:

::: zone pivot="b2c-user-flow"
1. Die cookiebasierte Azure AD B2C-Sitzung wird für ungültig erklärt.
1. Azure AD B2C versucht, den Benutzer vom Verbundidentitätsanbieter abzumelden.
::: zone-end

::: zone pivot="b2c-custom-policy"
1. Die cookiebasierte Azure AD B2C-Sitzung wird für ungültig erklärt.
1. Azure AD B2C versucht, den Benutzer vom Verbundidentitätsanbieter abzumelden:
   - OpenId Connect, wenn der bekannte Konfigurationsendpunkt des Identitätsanbieters eine `end_session_endpoint`-Adresse angibt. Die Abmeldeanforderung übergibt den `id_token_hint`-Parameter nicht. Wenn der Verbundidentitätsanbieter diesen Parameter erfordert, tritt bei der Abmeldeanforderung ein Fehler auf.
   - OAuth2, wenn die [Metadaten des Identitätsanbieters](oauth2-technical-profile.md#metadata) die `end_session_endpoint`-Adresse enthalten.
   - SAML, wenn die [Metadaten des Identitätsanbieters](identity-provider-generic-saml.md) die `SingleLogoutService`-Adresse enthalten.
1. Optional nimmt Azure AD B2C die Abmeldung bei anderen Anwendungen vor. Weitere Informationen finden Sie im Abschnitt [Einmaliges Abmelden](#single-sign-out).

> [!NOTE]
> Sie können das Abmelden vom Verbundidentitätsanbieter deaktivieren, indem Sie die Metadaten `SingleLogoutEnabled` im technischen Profil des Identitätsanbieters auf `false` festlegen.
::: zone-end

Bei der Abmeldung wird zwar der SSO-Status des Benutzers bei Azure AD B2C gelöscht, der Benutzer wird möglicherweise jedoch nicht von seiner Sitzung beim Social Media-Identitätsanbieter abgemeldet. Wenn der Benutzer bei einer nachfolgenden Anmeldung den gleichen Identitätsanbieter auswählt, kann er sich möglicherweise ohne Eingabe seiner Anmeldeinformationen erneut authentifizieren. Wenn sich ein Benutzer von der Anwendung abmelden möchte, bedeutet dies nicht unbedingt, dass er sich auch vollständig von seinem Facebook-Konto abmelden möchte. Wenn jedoch lokale Konten verwendet werden, wird die Sitzung des Benutzers ordnungsgemäß beendet.

::: zone pivot="b2c-custom-policy"

## <a name="single-sign-out"></a>Einmaliges Abmelden 

Wenn Sie den Benutzer zum [Azure AD B2C-Abmeldeendpunkt](openid-connect.md#send-a-sign-out-request) (für OAuth2 und OpenID Connect) umleiten oder eine `LogoutRequest` senden (für SAML), löscht Azure AD B2C die Sitzung des Benutzers im Browser. Allerdings kann der Benutzer weiterhin bei anderen Anwendungen angemeldet sein, die Azure AD B2C für die Authentifizierung verwenden. Um den Benutzer von allen Anwendungen abzumelden, für die eine Sitzung aktiv ist, unterstützt Azure AD B2C *einmaliges Abmelden*, auch als *Single Log-Out (SLO)* bezeichnet.

Bei der Abmeldung sendet Azure AD B2C gleichzeitig eine HTTP-Anforderung an die registrierte Abmelde-URL aller Anwendungen, bei denen der Benutzer aktuell angemeldet ist.

### <a name="configure-your-custom-policy"></a>Konfigurieren Ihrer benutzerdefinierten Richtlinie

Um das einmalige Abmelden zu unterstützen, müssen die technischen Profile des Tokenausstellers für JWT und SAML Folgendes angeben:

- Protokollname, z. B. `<Protocol Name="OpenIdConnect" />`
- Verweis auf das technische Profil für die Sitzung, z. B. `UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />`.

Das folgende Beispiel veranschaulicht die JWT- und SAML-Tokenaussteller mit einmaligem Abmelden:

```xml
<ClaimsProvider>
  <DisplayName>Local Account SignIn</DisplayName>
  <TechnicalProfiles>
    <!-- JWT Token Issuer -->
    <TechnicalProfile Id="JwtIssuer">
      <DisplayName>JWT token Issuer</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputTokenFormat>JWT</OutputTokenFormat>
      ...    
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for OIDC based tokens -->
    <TechnicalProfile Id="SM-OAuth-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.OAuthSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>

    <!--SAML token issuer-->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>SAML token issuer</DisplayName>
      <Protocol Name="SAML2" />
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      ...
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

### <a name="configure-your-application"></a>Konfigurieren der Anwendung

Konfigurieren einer Anwendung für einmaliges Abmelden:

- Konfigurieren Sie für [SAML-Dienstanbieter](saml-service-provider.md) die Anwendung mit dem [SingleLogoutService-Speicherort im SAML-Metadatendokument](saml-service-provider.md#override-or-set-the-logout-url-optional). Sie können auch die App-Registrierung `logoutUrl` konfigurieren. Weitere Informationen finden Sie unter [Festlegen der Abmelde-URL](saml-service-provider.md#override-or-set-the-logout-url-optional).
- Legen Sie für OpenID Connect- oder OAuth2-Anwendungen das `logoutUrl`-Attribut Ihres App-Registrierungsmanifests fest. Konfigurieren der Abmelde-URL:
    1. Wählen Sie im Azure AD B2C-Menü die Option **App-Registrierungen**.
    1. Wählen Sie Ihre Anwendungsregistrierung aus.
    1. Wählen Sie unter **Verwalten** die Option **Authentifizierung** aus.
    1. Konfigurieren Sie unter der **Front-Channel-Abmelde-URL** Ihre Abmelde-URL.

### <a name="handling-single-sign-out-requests"></a>Verarbeiten von Anforderungen für einmaliges Abmelden

Wenn Azure AD B2C die Abmeldeanforderung empfängt, wird ein Front-Channel-HTML-iFrame verwendet, um eine HTTP-Anforderung an die registrierte Abmelde-URL jeder konfigurierten Anwendung zu senden, bei der der Benutzer aktuell angemeldet ist. Beachten Sie, dass die Anwendung, welche die Abmeldeanforderung auslöst, diese Abmeldemeldung nicht erhält. Die Anwendungen müssen auf die Abmeldeanforderung mit dem Löschen der Anwendungssitzung reagieren, die den Benutzer identifiziert.

- Bei OpenID Connect- und OAuth2-Anwendungen sendet Azure AD B2C eine HTTP GET-Anforderung an die registrierte Abmelde-URL.
- Bei SAML-Anwendungen sendet Azure AD B2C eine SAML-Abmeldeanforderung an die registrierte Abmelde-URL.

Wenn alle Anwendungen über die Abmeldung benachrichtigt wurden, führt Azure AD B2C einen der folgenden Schritte aus:

- Bei OpenID Connect- oder OAuth2-Anwendungen wird der Benutzer an den angeforderten `post_logout_redirect_uri` umgeleitet, einschließlich des (optionalen) `state`-Parameters, der in der ersten Anforderung angegeben ist. Beispiel: `https://contoso.com/logout?state=foo`.
- Bei SAML-Anwendungen wird eine SAML-Abmeldeantwort über HTTP POST an die Anwendung gesendet, die die Abmeldeanforderung ursprünglich gesendet hat.

::: zone-end

### <a name="secure-your-logout-redirect"></a>Sichern der Umleitung beim Abmelden

Nach der Abmeldung wird der Benutzer an den im `post_logout_redirect_uri`-Parameter angegebenen URI umgeleitet, ungeachtet der Antwort-URLs, die für die Anwendung angegeben wurden. Wenn jedoch ein gültiger `id_token_hint`-Wert übergeben wird und die Option **ID-Token in Abmeldeanforderungen erforderlich** aktiviert ist, überprüft Azure AD B2C, ob der Wert von `post_logout_redirect_uri` einem der für die Anwendung konfigurierten Umleitungs-URIs entspricht, bevor die Umleitung ausgeführt wird. Wenn keine entsprechende Antwort-URL für die Anwendung konfiguriert ist, wird eine Fehlermeldung angezeigt, und der Benutzer wird nicht umgeleitet. 

::: zone pivot="b2c-user-flow"

So erzwingen Sie ein ID-Token in Abmeldeanforderungen

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie **Benutzerflows** aus.
1. Öffnen Sie den Benutzerflow, den Sie zuvor erstellt haben.
1. Wählen Sie **Eigenschaften** aus.
1. Aktivieren Sie die Option **ID-Token in Abmeldeanforderungen erforderlich**.
1. Wechseln Sie zurück zu **Azure AD B2C**.
1. Wählen Sie **App-Registrierungen** aus, und wählen Sie dann Ihre Anwendung aus.
1. Siehe **Authentifizierung**.
1. Geben Sie im Textfeld **Abmelde-URL** den Umleitungs-URI nach der Abmeldung ein, und wählen Sie dann **Speichern** aus.

::: zone-end

::: zone pivot="b2c-custom-policy"

Um festzulegen, dass ein ID-Token in Abmeldeanforderungen erforderlich ist, fügen Sie innerhalb des [RelyingParty](relyingparty.md)-Elements ein **UserJourneyBehaviors**-Element hinzu. Legen Sie dann den **EnforceIdTokenHintOnLogout**-Wert des **SingleSignOn**-Elements auf `true` fest. Das **UserJourneyBehaviors**-Element sollte wie im folgenden Beispiel aussehen:

```xml
<UserJourneyBehaviors>
  <SingleSignOn Scope="Tenant" EnforceIdTokenHintOnLogout="true"/>
</UserJourneyBehaviors>
```

::: zone-end

Gehen Sie wie folgt vor, um die Abmelde-URL Ihrer Anwendung zu konfigurieren:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie **App-Registrierungen** aus, und wählen Sie dann Ihre Anwendung aus.
1. Siehe **Authentifizierung**.
1. Geben Sie im Textfeld **Abmelde-URL** den Umleitungs-URI nach der Abmeldung ein, und wählen Sie dann **Speichern** aus.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr zum [Konfigurieren von Token in Azure AD B2C](configure-tokens.md).
