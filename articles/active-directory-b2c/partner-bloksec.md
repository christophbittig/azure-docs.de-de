---
title: Tutorial zum Konfigurieren von Azure Active Directory B2C mit BlokSec
titleSuffix: Azure AD B2C
description: Hier erfahren Sie, wie Sie die Azure AD B2C-Authentifizierung für die kennwortlose Authentifizierung mit BlokSec integrieren.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/20/2021
ms.author: gasinh
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 8e955a50ed85710c0a3a33680b1edc4ef5873da6
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132135511"
---
# <a name="tutorial-configure-azure-active-directory-b2c-with-bloksec-for-passwordless-authentication"></a>Tutorial: Konfigurieren von Azure Active Directory B2C mit BlokSec für die kennwortlose Authentifizierung

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

::: zone-end

In diesem Beispieltutorial wird beschrieben, wie Sie die Azure Active Directory B2C-Authentifizierung (Azure AD) mit [BlokSec](https://bloksec.com/) integrieren. BlokSec vereinfacht die Endbenutzeranmeldung durch eine kennwortlose Authentifizierung und tokenlose mehrstufige Authentifizierung. BlokSec schützt Kunden vor identitätsbezogenen Cyberangriffen wie Password Stuffing, Phishing und Man-in-the-Middle-Angriffen.

## <a name="scenario-description"></a>Beschreibung des Szenarios

Die BlokSec-Integration umfasst die folgenden Komponenten:

- **Azure AD B2C:** Konfiguriert als Autorisierungsserver/Identitätsanbieter für alle B2C-Anwendungen

- **Dezentralisierter BlokSec-Identitätsrouter:** Fungiert als Gateway für Dienste, die mithilfe der DIaaS™-Plattform von BlokSec Authentifizierungs- und Autorisierungsanforderungen an PIdP-Anwendungen (Personal Identity Provider) von Endbenutzern weiterleiten möchten; in Azure AD B2C als OpenID Connect-Identitätsanbieter (OIDC) konfiguriert

- **Mobile, auf dem BlokSec-SDK basierende App:** Fungiert im dezentralisierten Authentifizierungsszenario als PIdP der Benutzer. Die kostenlos herunterladbare BlokSec-Anwendung [yuID](https://play.google.com/store/apps/details?id=com.bloksec) kann eingesetzt werden, wenn Ihre Organisation es vorzieht, keine eigenen mobilen Anwendungen mithilfe der BlokSec-SDKs zu entwickeln.
Im folgenden Architekturdiagramm ist die Implementierung dargestellt.

![Abbildung zeigt das Architekturdiagramm](./media/partner-bloksec/partner-bloksec-architecture-diagram.png)

|Schritte| Beschreibung|
|:---------------|:----------------|
|1.| Ein Benutzer versucht, sich bei einer Azure AD B2C-Anwendung anzumelden und wird zur kombinierten Anmelde- und Registrierungsrichtlinie von Azure AD B2C weitergeleitet.|
|2.| Azure AD B2C leitet den Benutzer mithilfe des OIDC-Autorisierungscodeflows zum dezentralisierten BlokSec-Identitätsrouter weiter.|
|3.| Der dezentralisierte BlokSec-Router sendet eine Pushbenachrichtigung an die mobile App des Benutzers, einschließlich aller Kontextdetails der Authentifizierungs- und Autorisierungsanforderung.|
|4.| Der Benutzer prüft die Authentifizierungsaufforderung. Nimmt er sie an, wird er aufgefordert, seine Identität den Möglichkeiten des Geräts entsprechend durch Eingabe biometrischer Daten, z. B. durch einen Fingerabdruck oder die Gesichtserkennung, nachzuweisen.|
|5.| Die Antwort wird digital mit dem eindeutigen digitalen Schlüssel des Benutzers signiert. Die abschließende Authentifizierungsantwort liefert den Nachweis über den Besitz, die Urheberschaft und die Einwilligung. Die Antwort wird an den dezentralisierten BlokSec-Identitätsrouter zurückgegeben.|
|6.| Der dezentralisierte BlokSec-Identitätsrouter überprüft die digitale Signatur mit dem unveränderlichen eindeutigen öffentlichen Schlüssel des Benutzers, der in einem Distributed Ledger gespeichert ist. Anschließend antwortet Azure AD B2C mit dem Authentifizierungsergebnis.|
|7.| Auf Basis des Authentifizierungsergebnisses wird dem Benutzer der Zugriff gewährt oder verweigert.|

## <a name="onboard-to-bloksec"></a>Onboarding für BlokSec

Über [dieses Formular](https://bloksec.com/) können Sie einen Demo-Mandanten für BlokSec anfordern. Geben Sie im Nachrichtenfeld an, dass Sie das Onboarding für Azure AD B2C durchführen möchten. Laden Sie die kostenlose mobile BlokSec-App „yuID“ aus dem App Store herunter, und installieren Sie sie. Wenn Ihr Demo-Mandant vorbereitet ist, erhalten Sie eine E-Mail. Klicken Sie auf Ihrem mobilen Gerät, auf dem die BlokSec-Anwendung installiert ist, auf den Link, um Ihr Administratorkonto bei Ihrer yuID-Instanz zu registrieren.

::: zone pivot="b2c-user-flow"

## <a name="prerequisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

- Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.

- Einen [Azure AD B2C-Mandanten](./tutorial-create-tenant.md), der mit Ihrem Azure-Abonnement verknüpft ist.

- Ein BlokSec-[Testkonto](https://bloksec.com/)

- Falls noch nicht geschehen, führen Sie die Schritte zum [Registrieren](./tutorial-register-applications.md) einer Webanwendung und [Aktivieren der impliziten Genehmigung von ID-Token](./tutorial-register-applications.md#enable-id-token-implicit-grant) aus.
::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="prerequisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

- Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.

- Einen [Azure AD B2C-Mandanten](./tutorial-create-tenant.md), der mit Ihrem Azure-Abonnement verknüpft ist.

- Ein BlokSec-[Testkonto](https://bloksec.com/)

- Falls noch nicht geschehen, führen Sie die Schritte zum [Registrieren](./tutorial-register-applications.md) einer Webanwendung und [Aktivieren der impliziten Genehmigung von ID-Token](./tutorial-register-applications.md#enable-id-token-implicit-grant) aus.

- Führen Sie die unter [**Erste Schritte für benutzerdefinierte Richtlinien in Azure Active Directory B2C**](./tutorial-create-user-flows.md?pivots=b2c-custom-policy) beschriebenen Schritte aus.
::: zone-end

### <a name="part-1---create-an-application-registration-in-bloksec"></a>Teil 1: Erstellen einer Anwendungsregistrierung in BlokSec

1. Melden Sie sich beim BlokSec-Verwaltungsportal an. In der Kontoregistrierungs-E-Mail, die Sie nach dem Onboarding für BlokSec erhalten haben, ist der Link enthalten.

2. Klicken Sie im Hauptdashboard auf **Add Application > Create Custom** (Anwendung hinzufügen > Benutzerdefiniert erstellen).

3. Füllen Sie die Anwendungsdetails wie folgt aus, und übermitteln Sie sie:  

   |Eigenschaft  |Wert  |
   |---------|---------|
   |  Name         |Azure AD B2C oder den Namen der gewünschten Anwendung|
   |SSO type (SSO-Typ)         | OIDC|
   |Logo URI (Logo-URI)     |[https://bloksec.io/assets/AzureB2C.png](https://bloksec.io/assets/AzureB2C.png) – ein Link zu einem Bild Ihrer Wahl|
   |Umleitungs-URIs     | https://**Namen-Ihres-B2C-Mandaten**.b2clogin.com/**Namen-Ihres-B2C-Mandaten**.onmicrosoft.com/oauth2/authresp<BR>**Zum Beispiel**:      'https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp ' <BR><BR>Wenn Sie eine benutzerdefinierte Domäne verwenden, geben Sie https://**Name-Ihrer-Domäne**/**Name-Ihres-Mandanten**.onmicrosoft.com/oauth2/authresp ein. <BR> Ersetzen Sie „Name-Ihrer-Domäne“ durch Ihre benutzerdefinierte Domäne und „Name-Ihres-Mandanten“ durch den Namen Ihres Mandanten.         |
   |Post log out redirect URIs (Umleitungs-URIs nach dem Abmelden)  |https://**Name-Ihres-B2C-Mandanten**.b2clogin.com/**Name-Ihres-B2C-Mandanten**.onmicrosoft.com/ **{Richtlinie}** /oauth2/v2.0/logout <BR> [Senden einer Abmeldeanforderung](./openid-connect.md#send-a-sign-out-request) |

4. Klicken Sie nach dem Speichern auf die neu erstellte Azure AD B2C-Anwendung, um die Anwendungskonfiguration zu öffnen, und dann auf **Generate App Secret** (App-Geheimnis generieren).

>[!NOTE]
>Sie benötigen die Anwendungs-ID und das Anwendungsgeheimnis später, um den Identitätsanbieter in der Azure AD B2C zu konfigurieren.

::: zone pivot="b2c-user-flow"

### <a name="part-2---add-a-new-identity-provider-in-azure-ad-b2c"></a>Teil 2: Hinzufügen eines neuen Identitätsanbieters in Azure AD B2C

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/#home) als globaler Administrator Ihres Azure AD B2C-Mandanten an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Navigieren Sie zu **Dashboard** > **Azure Active Directory B2C** > **Identitätsanbieter**.
1. Wählen Sie **Neuer OpenID Connect-Anbieter** aus.
1. Wählen Sie **Hinzufügen**.

### <a name="part-3---configure-an-identity-provider"></a>Teil 3: Konfigurieren eines Identitätsanbieters

1. Klicken Sie auf **Identitätsanbietertyp > OpenID Connect**.

1. Füllen Sie das Formular aus, um den Identitätsanbieter einzurichten:

|Eigenschaft  |Wert  |
|:---------|:---------|
|Name     |Geben Sie „BlokSec yuID – Passwordless“ (BlokSec yuID – Kennwortlos) oder einen Namen Ihrer Wahl ein.|
|Metadaten-URL| `https://api.bloksec.io/oidc/.well-known/openid-configuration` |
|Client-ID|Die in **Teil 1** notierte Anwendungs-ID aus dem BlokSec-Verwaltungsportal.|
|Geheimer Clientschlüssel|Das in **Teil 1** notierte Anwendungsgeheimnis aus dem BlokSec-Verwaltungsportal.|
|`Scope`|OpenID-E-Mail-Profil|
|Antworttyp|Code|
|Domänenhinweis|yuID|

1. Klicken Sie auf **OK**.

1. Wählen Sie **Ansprüche dieses Identitätsanbieters zuordnen** aus.

1. Füllen Sie das Formular aus, um den Identitätsanbieter zuzuordnen:

|Eigenschaft  |Wert  |
|:---------|:---------|
|Benutzerkennung|sub|
|`Display name`|name|
|Vorname|given_name|
|Surname|family_name|
|Email|email|

1. Klicken Sie auf **Speichern**, um das Setup Ihres neuen OIDC-Identitätsanbieters abzuschließen.  

### <a name="part-4---user-registration"></a>Teil 4: Benutzerregistrierung

1. Melden Sie sich mit den zuvor bereitgestellten Anmeldeinformationen bei der BlokSec-Verwaltungskonsole an.

1. Navigieren Sie zur Azure AD B2C-Anwendung, die zuvor erstellt wurde. Klicken Sie oben rechts auf das Zahnradsymbol und dann auf **Create Account** (Konto erstellen).  

1. Geben Sie die Daten des Benutzers in das Kontoformular ein, notieren Sie sich den Kontonamen, und klicken Sie auf **Submit** (Übermitteln).  

Der Benutzer erhält unter der angegebenen E-Mail-Adresse eine **Kontoregistrierungs-E-Mail**. Fordern Sie den Benutzer auf, auf dem mobilen Gerät, auf dem die BlokSec-App „yuID“ installiert ist, den Registrierungslink zu öffnen.

### <a name="part-5---create-a-user-flow-policy"></a>Teil 5: Erstellen einer Richtlinie für Benutzerflows

BlokSec sollte unter Ihren B2C-Identitätsanbietern jetzt als neuer OIDC-Identitätsanbieter aufgeführt sein.  

1. Wählen Sie auf Ihrem Azure AD B2C-Mandanten unter **Richtlinien** die Option **Benutzerflows** aus.  

1. Klicken Sie auf **Neuer Benutzerflow**.

1. Klicken Sie auf **Registrierung und Anmeldung** > **Version** > **Erstellen**.

1. Geben Sie einen **Namen** für die Richtlinie ein.

1. Wählen Sie im Abschnitt „Identitätsanbieter“ den neu erstellten Identitätsanbieter „BlokSec“ aus.  

1. Wählen Sie bei „Lokale Konten“ **Keine** aus, um die E-Mail- und kennwortbasierte Authentifizierung zu deaktivieren.

1. Klicken Sie auf **Benutzerflow ausführen**.

1. Geben Sie im Formular die Antwort-URL ein, z. B. `https://jwt.ms`.

1. Im Browser erfolgt eine Weiterleitung zur Anmeldeseite von BlokSec. Geben Sie den Kontonamen ein, der während der Benutzerregistrierung registriert wurde. Der Benutzer erhält eine Pushbenachrichtigung an das mobile Gerät, auf dem die BlokSec-Anwendung „yuID“ installiert ist. Beim Öffnen der Benachrichtigung wird dem Benutzer eine Authentifizierungsaufforderung angezeigt.

1. Sobald die Authentifizierungsaufforderung akzeptiert wurde, erfolgt im Browser eine Weiterleitung zur Antwort-URL.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Benutzerdefinierte Richtlinien in Azure AD B2C](./custom-policy-overview.md)

- [Erste Schritte mit benutzerdefinierten Richtlinien in Azure AD B2C](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)

::: zone-end

::: zone pivot="b2c-custom-policy"

>[!NOTE]
>In Azure Active Directory B2C sind [**benutzerdefinierte Richtlinien**](./user-flow-overview.md) in erster Linie auf komplexe Szenarios ausgelegt. Für die meisten Szenarios werden integrierte [**Benutzerflows**](./user-flow-overview.md) empfohlen.

### <a name="part-2---create-a-policy-key"></a>Teil 2: Erstellen eines Richtlinienschlüssels

Speichern Sie den geheimen Clientschlüssel, den Sie zuvor aus Ihrem Azure AD B2C-Mandanten notiert haben.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie auf der Seite „Übersicht“ die Option **Framework für die Identitätsfunktion** aus.
1. Klicken Sie erst auf **Richtlinienschlüssel** und anschließend auf **Hinzufügen**.
1. Klicken Sie unter **Optionen** auf `Manual`.
1. Geben Sie einen **Namen** für den Richtlinienschlüssel ein. Beispiel: `BlokSecAppSecret`. Dem Namen Ihres Schlüssels wird automatisch das Präfix `B2C_1A_` hinzugefügt.
1. Geben Sie im Feld **Geheimnis** den geheimen Clientschlüssel ein, den Sie zuvor notiert haben.
1. Wählen Sie für **Schlüsselverwendung** die Option `Signature` aus.
1. Klicken Sie auf **Erstellen**.

### <a name="part-3---configure-bloksec-as-an-identity-provider"></a>Teil 3: Konfigurieren von BlokSec als Identitätsanbieter

Damit sich Benutzer mit einer dezentralen BlokSec-Identität anmelden können, müssen Sie BlokSec als Anspruchsanbieter definieren, mit dem Azure AD B2C über einen Endpunkt kommunizieren kann. Der Endpunkt stellt Ansprüche bereit, mit denen Azure AD B2C überprüft, ob sich ein bestimmter Benutzer den Möglichkeiten des Geräts entsprechend mithilfe eines biometrischen Nachweises wie einem Fingerabdruck oder der Gesichtserkennung authentifiziert hat.

Sie können BlokSec als Anspruchsanbieter definieren, indem Sie BlokSec in der Erweiterungsdatei Ihrer Richtlinie zum Element **ClaimsProvider** hinzufügen.

1. Öffnen Sie die Datei `TrustFrameworkExtensions.xml`.

2. Suchen Sie nach dem Element **ClaimsProviders**. Falls das Element nicht vorhanden ist, fügen Sie es unter dem Stammelement hinzu.

3. Fügen Sie ein neues **ClaimsProvider**-Element wie folgt hinzu:

    ```xml
    <ClaimsProvider>
      <Domain>bloksec</Domain>
      <DisplayName>BlokSec</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="BlokSec-OpenIdConnect">
          <DisplayName>BlokSec</DisplayName>
          <Description>Login with your BlokSec decentriled identity</Description>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="METADATA">https://api.bloksec.io/oidc/.well-known/openid-configuration</Item>
            <!-- Update the Client ID below to the BlokSec Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile email</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
            <Item Key="ValidTokenIssuerPrefixes">https://api.bloksec.io/oidc</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_BlokSecAppSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
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

### <a name="part-4---add-a-user-journey"></a>Teil 4: Hinzufügen einer User Journey

Der Identitätsanbieter wurde nun eingerichtet, aber er ist noch auf keiner der Anmeldeseiten verfügbar. Wenn Sie nicht über eine eigene benutzerdefinierte User Journey verfügen, erstellen Sie ein Duplikat einer vorhandenen User Journey-Vorlage, und fahren Sie andernfalls mit dem nächsten Schritt fort.  

1. Öffnen Sie die Datei `TrustFrameworkBase.xml` aus dem Starter Pack.

2. Suchen und kopieren Sie den gesamten Inhalt des Elements **UserJourneys**, das ID=`SignUpOrSignIn` enthält.

3. Öffnen Sie die Datei `TrustFrameworkExtensions.xml`, und suchen Sie nach dem Element **UserJourneys**. Wenn das Element nicht vorhanden ist, fügen Sie ein solches hinzu.

4. Fügen Sie den gesamten Inhalt des kopierten **UserJourney**-Element als untergeordnetes Element des **UserJourneys**-Elements ein.

5. Benennen Sie die ID der User Journey um. Beispiel: ID=`CustomSignUpSignIn`.  

### <a name="part-5---add-the-identity-provider-to-a-user-journey"></a>Teil 5: Hinzufügen des Identitätsanbieters zu einer User Journey

Nachdem Sie nun über eine User Journey verfügen, fügen Sie den neuen Identitätsanbieter der User Journey hinzu. Fügen Sie zunächst eine Anmeldeschaltfläche hinzu, und verknüpfen Sie die Schaltfläche dann mit einer Aktion. Die Aktion ist das technische Profil, das Sie zuvor erstellt haben.  

1. Suchen Sie nach dem Element für den Orchestrierungsschritt, das in der User Journey Type=`CombinedSignInAndSignUp` oder Type=`ClaimsProviderSelection` enthält. Dies ist in der Regel der erste Orchestrierungsschritt. Das **ClaimsProviderSelections**-Element enthält eine Liste mit Identitätsanbietern, mit denen sich ein Benutzer anmelden kann. Die Reihenfolge der Elemente gibt die Reihenfolge der Anmeldeschaltflächen vor, die dem Benutzer angezeigt werden. Fügen Sie ein **ClaimsProviderSelection**-XML-Element hinzu. Legen Sie für **TargetClaimsExchangeId** einen Anzeigenamen fest.

2. Fügen Sie im nächsten Orchestrierungsschritt ein **ClaimsExchange**-Element hinzu. Legen Sie die **Id** auf den Wert der Zielanspruchsaustausch-ID fest. Ändern Sie den Wert von **TechnicalProfileReferenceId** in die ID des technischen Profils, das Sie zuvor erstellt haben.

Der folgende XML-Code veranschaulicht die ersten beiden Orchestrierungsschritte einer User Journey mit dem Identitätsanbieter:

```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="BlokSecExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="BlokSecExchange" TechnicalProfileReferenceId="BlokSec-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

### <a name="part-6---configure-the-relying-party-policy"></a>Teil 6: Konfigurieren einer Richtlinie für die vertrauende Seite

Die Richtlinie für die vertrauende Seite (z. B. [SignUpSignIn.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/SocialAndLocalAccounts/SignUpOrSignin.xml)) gibt die User Journey an, die Azure AD B2C ausführt. Suchen Sie auf der vertrauenden Seite das Element **DefaultUserJourney**. Aktualisieren Sie **ReferenceId** auf die ID der User Journey, in der Sie den Identitätsanbieter hinzugefügt haben.

Im folgenden Beispiel ist ReferenceId für die User Journey `CustomSignUpOrSignIn` auf `CustomSignUpOrSignIn` festgelegt:

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="part-7---upload-the-custom-policy"></a>Teil 7: Hochladen der benutzerdefinierten Richtlinie

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/#home) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Suchen Sie im [Azure-Portal](https://portal.azure.com/#home) nach **Azure AD B2C**, und klicken Sie darauf.
1. Klicken Sie unter „Richtlinien“ auf **Identity Experience Framework**.
Wählen Sie **Benutzerdefinierte Richtlinie hochladen** aus, und laden Sie dann die beiden geänderten Richtliniendateien in der folgenden Reihenfolge hoch: zuerst die Erweiterungsrichtlinie (z. B. `TrustFrameworkExtensions.xml`) und dann die Richtlinie für die vertrauende Seite (z. B. `SignUpSignIn.xml`).

### <a name="part-8---test-your-custom-policy"></a>Teil 8: Testen Ihrer benutzerdefinierten Richtlinie

1. Wählen Sie die Richtliniendatei für die vertrauende Seite aus, z. B. `B2C_1A_signup_signin`.
1. Wählen Sie für **Anwendung** eine Webanwendung aus, die Sie [zuvor registriert haben](./tutorial-register-applications.md). Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Wählen Sie die Schaltfläche **Jetzt ausführen** aus.
1. Wählen Sie auf der Registrierungs- oder Anmeldeseite die Option **Google** aus, um sich mit einem Google-Konto anzumelden.

Wenn der Anmeldevorgang erfolgreich verlaufen ist, wird der Browser an `https://jwt.ms` umgeleitet und dadurch der Inhalt des von Azure AD B2C zurückgegebenen Tokens angezeigt.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Benutzerdefinierte Richtlinien in Azure AD B2C](./custom-policy-overview.md)

- [Erste Schritte mit benutzerdefinierten Richtlinien in Azure AD B2C](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)

::: zone-end
