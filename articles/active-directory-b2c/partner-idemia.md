---
title: Konfigurieren von IDOXI mit Azure Active Directory B2C (Vorschau)
titleSuffix: Azure AD B2C
description: Hier erfahren Sie, wie Sie Azure AD B2C-Authentifizierung mit ID ENTF integrieren, damit die vertrauende Seite die idAUTH- oder vom US-Bundesstaat ausgestellten mobilen IDs nutzt.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/21/2021
ms.author: gasinh
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 27c83d2ade4552150793aa8fbe777e376a2ae762
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131012875"
---
# <a name="tutorial-configure-idemia-with-azure-active-directory-b2c-for-relying-party-to-consume-idemia-or-us-state-issued-mobile-identity-credentials-preview"></a>Tutorial: Konfigurieren von IDMOBILE mit Azure Active Directory B2C für die vertrauende Seite für die Nutzung von IDZEP-Anmeldeinformationen oder vom US-Bundesstaat ausgestellten Anmeldeinformationen für die mobile Identität (Vorschau)
[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

Dieses Feature ist nur für benutzerdefinierte Richtlinien verfügbar. Wählen Sie für die Einrichtungsschritte in der vorherigen Auswahl die Option **Benutzerdefinierte Richtlinie** aus.

::: zone-end

::: zone pivot="b2c-custom-policy"

In diesem Beispieltutorial wird beschrieben, wie Sie die Azure Active Directory (Azure AD) B2C-Authentifizierung in [IDEMIA](https://www.idemia.com/) integrieren. IDEMIA ist ein kennwortloser Authentifizierungsanbieter, der in Echtzeit einwilligungsbasierte Dienste mit biometrischer Authentifizierung wie Gesichts-ID und Fingerabdruck bereitstellt, um Betrug und Wiederverwendung von Anmeldeinformationen zu vermeiden. Die mobile ID von IDEMIA ermöglicht es Bürgern, von einer von der Regierung ausgestellten vertrauenswürdigen digitalen ID als Ergänzung ihrer physischen ID zu profitieren. Diese Anwendung wird verwendet, um die Identität mithilfe einer selbstgewählten PIN oder Touch-ID/Gesichts-ID zu überprüfen. Die mobile ID ermöglicht es Bürgern, ihre Identität zu schützen, indem sie ihnen erlaubt, nur die für eine Transaktion erforderlichen Informationen freizugeben, und ermöglicht Betrugsschutz.



## <a name="scenario-description"></a>Beschreibung des Szenarios

Die IDEMIA-Integration umfasst die folgenden Komponenten:

- **Azure AD B2C**: Der Autorisierungsserver ist für die Überprüfung der Anmeldeinformationen des Benutzers verantwortlich. Er wird auch als Identitätsanbieter (IdP) bezeichnet.

- **IDEMIA mID**: OpenID Connect-Anbieter (OIDC), der als [externer Azure AD B2C-Anbieter](add-identity-provider.md) konfiguriert ist

- **[IDEMIA mID-Anwendung](https://idemia-mobile-id.com/)** : Eine vertrauenswürdige, von Behörden ausgestellte digitale Identität. Mobile ID ist eine digitale Version Ihrer Fahrerlaubnis oder eines vom Bundesstaat ausgestellten Ausweises, der sich in einer App auf Ihrem Smartphone befindet. [IDEMIA](https://idemia-mobile-id.com/).

IDEMIA stellt mID für viele US-Bundesstaaten von Fahrzeugen (DMVs) bereit.

Die mID ist eine Digitalisierung eines Identifikationsdokuments in ein sicheres mobiles Identitätstoken, das für die Überprüfung sehr portabel ist und als Index für die Autorisierung von **mID Services** dient. Der mID-Dienst ermöglicht es den DMVs, die Identität von Personen zu überprüfen, indem sie die Authentifizierung von Ausweisdokumenten anhand der ausgestellten Führerscheine und biometrische **Selfie**-zu-Ausweis-Gesichtserkennungsdienste nutzen.  

Nach der Erstellung wird die mID auf dem Mobiltelefon des Endnutzers als digital signierte **Identität auf dem Edge** gespeichert. Die Endbenutzer können diese signierten Anmeldeinformationen jetzt für den Zugriff auf andere identitätskritische Dienste verwenden, z. B. Altersnachweis, Finanzinformationen Ihres Kunden und Kontozugriff, bei denen Sicherheit von entscheidender Bedeutung ist.

Das Angebot für Microsoft ist die Unterstützung dieser Dienste als vertrauende Seite (Relying Party, RP), die eine vom Staat ausgestellte mID verwendet, um Dienste mithilfe der vom Besitzer der mID gesendeten Attribute bereitzustellen.

Das folgende Diagramm zeigt die Implementierung für Web- oder lokale Szenarien:

![Screenshot der lokalen Überprüfung](./media/partner-idemia/idemia-architecture-diagram.png)

| Schritt | BESCHREIBUNG |
|:--------|:--------|
| 1. | Der Benutzer besucht die Azure AD B2C-Anmeldeseite, die in diesem Fall die antwortende Seite auf ihrem Gerät ist, um eine Transaktion durchzuführen und sich über ihre mID-App anzumelden. |
| 2. | Azure AD B2C erfordert eine ID-Überprüfung, und für diese wird der Benutzer mithilfe des OIDC-Autorisierungscodeflows an den IDEMIA-Router umgeleitet.|
| 3. | Der IDEMIA-Router sendet eine biometrische Abfrage an die mobile App des Benutzers, einschließlich aller Kontextdetails der Authentifizierungs- und Autorisierungsanforderung.|
| 4. | Abhängig von der erforderlichen Sicherheitsstufe muss der Benutzer möglicherweise zusätzliche Details angeben, seine PIN eingeben, ein aktuelles Selfie aufnehmen oder beides.|
| 5. | Die abschließende Authentifizierungsantwort liefert den Nachweis über den Besitz, die Urheberschaft und die Einwilligung. Die Antwort wird an den IDEMIA-Router zurückgegeben.|
| 6. | Der IDEMIA-Router überprüft die vom Benutzer bereitgestellten Informationen und antwortet mit dem Authentifizierungsergebnis auf Azure AD B2C.|
|7. | Auf Basis des Authentifizierungsergebnisses wird dem Benutzer der Zugriff gewährt oder verweigert. |

## <a name="onboard-with-idemia"></a>Durchführen des Onboardings mit IDEMIA

Nehmen Sie Kontakt mit [IDEAMIA](https://www.idemia.com/get-touch/) auf, um eine Demo anzufordern. Geben Sie beim Ausfüllen des Anforderungsformulars im Meldungsfeld an, dass Sie das Onboarding mit Azure AD B2C durchführen möchten.

## <a name="integrate-idemia-with-azure-ad-b2c"></a>Integrieren von IDEMIA in Azure AD B2C

## <a name="prerequisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

- Zugriff auf Endbenutzer, die über eine IDEMIA (Vom US-Bundesstaat ausgestellter Mobile ID-Ausweis, mID) oder während der Testphase die mID-Demoanwendung, die von [IDEMIA](https://www.idemia.com/) bereitgestellt wird, verfügen.

- Ein Azure AD-Abonnement Falls Sie noch kein Konto haben, können Sie ein  [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.

- Einen  [Azure AD B2C-Mandanten](tutorial-create-tenant.md), der mit Ihrem Azure-Abonnement verknüpft ist.

- Ihre Unternehmenswebanwendung, die in Azure AD B2C-Mandanten registriert ist. Zu Testzwecken können Sie https://jwt.ms konfigurieren, eine Webanwendung im Besitz von Microsoft, die den decodierten Inhalt eines Tokens anzeigt.

>[!NOTE]
>Der Inhalt des Tokens verlässt niemals Ihren Browser.


### <a name="part-1---submit-a-relying-party-application-on-boarding-for-mid"></a>Teil 1: Übermitteln einer Anwendung der vertrauenden Seite beim Onboarding für mID

Im Rahmen ihrer Integration in IDEMIA erhalten Sie die folgenden Informationen:

| Eigenschaft | BESCHREIBUNG |
|:---------|:----------|
| Anwendungsname | Azure AD B2C oder den Namen der gewünschten Anwendung |
| Client_ID | Dies ist der eindeutige Bezeichner, der vom IdP bereitgestellt wird. |
| Geheimer Clientschlüssel | Kennwort, das von der Anwendung der vertrauenden Seite für die Authentifizierung mit dem IDEMIA-IdP verwendet wird |
| Metadatenendpunkt | Dies ist eine URL, die auf ein Konfigurationsdokument eines Tokenausstellers verweist, das auch als bekannter OpenID-Konfigurationsendpunkt bekannt ist. |
|Umleitungs-URIs | `https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp`<br>Beispiel: `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`<br><br>Geben Sie `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` bei Verwendung einer benutzerdefinierten Domäne ein.<br>Ersetzen Sie „Name-Ihrer-Domäne“ durch Ihre benutzerdefinierte Domäne und „Name-Ihres-Mandanten“ durch den Namen Ihres Mandanten. |
|Post log out redirect URIs (Umleitungs-URIs nach dem Abmelden) | `https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/{policy}/oauth2/v2.0/logout`<br>Senden Sie eine Abmeldeanforderung. |

>[!NOTE]
>Sie benötigen die IDSEC-Client-ID und den geheimen Clientschlüssel später, um den IdP in Azure AD B2C zu konfigurieren.

### <a name="part-2---create-a-policy-key"></a>Teil 2: Erstellen eines Richtlinienschlüssels

Speichern Sie den geheimen IDEMIA-Clientschlüssel, den Sie zuvor aus Ihrem Azure AD B2C-Mandanten notiert haben.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.

3. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.

4. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.

5. Wählen Sie auf der Seite **Übersicht** die Option **Identity Experience Framework** aus.

6. Klicken Sie erst auf **Richtlinienschlüssel** und anschließend auf **Hinzufügen**.

7. Wählen Sie unter **Optionen** die Option **Manuell** aus.

8. Geben Sie einen **Namen** für den Richtlinienschlüssel ein. Beispiel: IdsecretAppSecret. Dem Namen Ihres Schlüssels wird automatisch das Präfix B2C_1A_ hinzugefügt.

9. Geben Sie im Feld **Geheimnis** den geheimen Clientschlüssel ein, den Sie zuvor notiert haben.

10. Wählen Sie unter **Schlüsselverwendung** **Signatur** aus.

11. Klicken Sie auf **Erstellen**.

### <a name="part-3---configure-idemia-as-an-external-idp"></a>Teil 3: Konfigurieren von IDEMIA als externer IdP

Damit sich Benutzer mit einer passwortlosen Identität der mobilen IDEMIA-ID anmelden können, müssen Sie IDEMIA als Anspruchsanbieter definieren, mit dem Azure AD B2C über einen Endpunkt kommunizieren kann. Der Endpunkt stellt Ansprüche bereit, mit denen Azure AD B2C überprüft, ob sich ein bestimmter Benutzer den Möglichkeiten des Geräts entsprechend mithilfe eines biometrischen Nachweises wie einem Fingerabdruck oder der Gesichtserkennung authentifiziert hat.
Sie können IDEMIA als Anspruchsanbieter definieren, indem Sie IDEMIA in der Erweiterungsdatei Ihrer Richtlinie zum Element **ClaimsProvider** hinzufügen.

```PowerShell
     <TechnicalProfile Id="Idemia-Oauth2">
          <DisplayName>IDEMIA</DisplayName>
          <Description>Login with your IDEMIA identity</Description>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="METADATA">https://idp.XXXX.net/oxauth/.well-known/openid-configuration</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid id_basic mt_scope</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="token_endpoint_auth_method">client_secret_basic</Item>
            <Item Key="ClaimsEndpoint">https://idp.XXXX.net/oxauth/restv1/userinfo</Item>
            <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_IdemiaAppSecret" />
</CryptographicKeys>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="acr" PartnerClaimType="acr_values" DefaultValue="loa-2" />
          </InputClaims>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName1" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="lastName1" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="idemia" />
            <OutputClaim ClaimTypeReferenceId="documentId" />
            <OutputClaim ClaimTypeReferenceId="address1" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
           
```

Legen Sie client_id auf die Anwendungs-ID aus der Anwendungsregistrierung fest.

|Eigenschaft | BESCHREIBUNG|
|:------|:-------|
|`Scope`| Für OpenID Verbinden (OIDC) ist die Mindestanforderung, dass der Bereichsparameter auf **openid** festgelegt wird. Zusätzliche Bereiche können als durch Leerzeichen getrennte Liste angefügt werden.|
|redirect_uri | Dadurch wird definiert, wo der Benutzer-Agent den Autorisierungscode zurück an Azure AD B2C sendet.|
|response_type| Beim Autorisierungscodefluss ist dies auf **Code** festgelegt.|
|acr_values| Dieser Parameter steuert die Authentifizierungsmethoden, die der Benutzer während des Authentifizierungsprozesses ausführen muss. |

Einer der folgenden Werte muss ausgewählt werden:

|Parameterwert| Auswirkungen auf den Benutzerauthentifizierungsprozess |
|:------|:-------|
|`loa-2`| Nur cryptobasierte Azure AD MFA|
|`loa-3`| Kryptobasierte Azure AD MFA plus ein zusätzlicher Faktor|
|`loa-4`| Kryptobasierte Azure AD MFA mit der Anforderung, dass der Benutzer auch eine pinbasierte und biometrische Authentifizierung durchführen muss |

Der Endpunkt **/userinfo** stellt die Ansprüche für die Bereiche zur Verfügung, die in der Autorisierungsanforderung angefordert werden. Für **<mt_scope>** umfasst dies unter anderem Ansprüche wie Vorname, Nachname und Fahrerlaubnisnummer.
Die für einen bestimmten Bereich festgelegten Ansprüche werden im Abschnitt **scope_to_claims_mapping** der Ermittlungs-API veröffentlicht.
Azure AD B2C fordert Ansprüche vom Anspruchsendpunkt an und gibt diese Ansprüche im OutputClaims-Element zurück. Möglicherweise müssen Sie den Namen des in Ihrer Richtlinie definierten Anspruchs dem im IdP definierten Namen zuordnen. Achten Sie dabei darauf, den Anspruchstyp im [ClaimSchema-Element](claimsschema.md) zu definieren:

```PowerShell
<ClaimType Id="documentId">
     <DisplayName>documentId</DisplayName>
     <DataType>string</DataType>
</ClaimType>
<ClaimType Id="address1">
     <DisplayName>address</DisplayName>
     <DataType>string</DataType>
</ClaimType>
```

### <a name="part-4---add-a-user-journey"></a>Teil 4: Hinzufügen einer User Journey

Der Identitätsanbieter wurde nun eingerichtet, aber er ist noch auf keiner der Anmeldeseiten verfügbar. Wenn Sie nicht über eine eigene benutzerdefinierte User Journey verfügen, erstellen Sie ein Duplikat einer vorhandenen User Journey-Vorlage, und fahren Sie andernfalls mit dem nächsten Schritt fort.

1. Öffnen Sie die Datei `TrustFrameworkBase.xml` aus dem Starter Pack.

2. Suchen und kopieren Sie den gesamten Inhalt des Elements **UserJourneys**, das `ID=SignUpOrSignIn` enthält.

3. Öffnen Sie die Datei `TrustFrameworkExtensions.xml`, und suchen Sie nach dem Element **UserJourneys**. Wenn das Element nicht vorhanden ist, fügen Sie ein solches hinzu.

4. Fügen Sie den gesamten Inhalt des kopierten **UserJourney**-Element als untergeordnetes Element des UserJourneys-Elements ein.

5. Benennen Sie die ID der User Journey um. Beispiel: `ID=CustomSignUpSignIn`.

### <a name="part-5---add-the-idp-to-a-user-journey"></a>Teil 5: Hinzufügen des Identitätsanbieters zu einer User Journey

Nachdem Sie nun über eine User Journey verfügen, fügen Sie den neuen Identitätsanbieter der User Journey hinzu. Fügen Sie zunächst eine Anmeldeschaltfläche hinzu, und verknüpfen Sie die Schaltfläche dann mit einer Aktion. Die Aktion ist das technische Profil, das Sie zuvor erstellt haben.

1. Suchen Sie nach dem Element für den Orchestrierungsschritt, das in der User Journey Type=`CombinedSignInAndSignUp` oder Type=`ClaimsProviderSelection` enthält. Dies ist in der Regel der erste Orchestrierungsschritt. Das **ClaimsProviderSelections**-Element enthält eine Liste mit Identitätsanbietern, mit denen sich ein Benutzer anmelden kann. Die Reihenfolge der Elemente gibt die Reihenfolge der Anmeldeschaltflächen vor, die dem Benutzer angezeigt werden. Fügen Sie ein **ClaimsProviderSelection**-XML-Element hinzu. Legen Sie für **TargetClaimsExchangeId** einen Anzeigenamen fest.

2. Fügen Sie im nächsten Orchestrierungsschritt ein **ClaimsExchange**-Element hinzu. Legen Sie die **Id** auf den Wert der Zielanspruchsaustausch-ID fest. Ändern Sie den Wert von **TechnicalProfileReferenceId** in die ID des technischen Profils, das Sie zuvor erstellt haben.

Der folgende XML-Code veranschaulicht die ersten beiden Orchestrierungsschritte einer User Journey mit dem Identitätsanbieter:

```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="IdemiaExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="IdemiaExchange" TechnicalProfileReferenceId="Idemia-Oauth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

### <a name="part-6---configure-the-relying-party-policy"></a>Teil 6: Konfigurieren einer Richtlinie für die vertrauende Seite

Die Richtlinie für die vertrauende Seite (z. B. [SignUpSignIn.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/SocialAndLocalAccounts/SignUpOrSignin.xml)) gibt die User Journey an, die Azure AD B2C ausführt. Suchen Sie auf der vertrauenden Seite das Element **DefaultUserJourney**. Aktualisieren Sie **ReferenceId** auf die ID der User Journey, in der Sie den Identitätsanbieter hinzugefügt haben.

Im folgenden Beispiel ist `CustomSignUpOrSignIn`ReferenceId **für die User Journey** auf `CustomSignUpOrSignIn` festgelegt:
 
```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="part-7---upload-the-custom-policy"></a>Teil 7: Hochladen der benutzerdefinierten Richtlinie

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/#home) an.

2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.

3. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.

4. Suchen Sie im [Azure-Portal](https://portal.azure.com/#home) nach **Azure AD B2C**, und klicken Sie darauf.

5. Klicken Sie unter „Richtlinien“ auf **Identity Experience Framework**.

Wählen Sie **Benutzerdefinierte Richtlinie hochladen** aus, und laden Sie dann die beiden geänderten Richtliniendateien in der folgenden Reihenfolge hoch: zuerst die Erweiterungsrichtlinie (z. B. `TrustFrameworkExtensions.xml`) und dann die Richtlinie für die vertrauende Seite (z. B. `SignUpSignIn.xml`).

### <a name="part-8---test-your-custom-policy"></a>Teil 8: Testen Ihrer benutzerdefinierten Richtlinie

1. Wählen Sie die Richtliniendatei für die vertrauende Seite aus, z. B. `B2C_1A_signup_signin`.

2. Wählen Sie für **Anwendung** eine Webanwendung aus, die Sie [zuvor registriert haben](./tutorial-register-applications.md). Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.

3. Wählen Sie die Schaltfläche **Jetzt ausführen** aus.

4. Wählen Sie auf der Anmeldeseite **IDEMIA** aus, um sich mit einer vom US-Bundesstaat ausgestellten mID (Mobile ID-Anmeldeinfos) anzumelden.

Wenn der Anmeldevorgang erfolgreich verlaufen ist, wird der Browser an `https://jwt.ms` umgeleitet und dadurch der Inhalt des von Azure AD B2C zurückgegebenen Tokens angezeigt.


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Benutzerdefinierte Richtlinien in Azure AD B2C](custom-policy-overview.md)

- [Erste Schritte mit benutzerdefinierten Richtlinien in Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy)

- [Weitere Informationen zu IDEMIA mID](https://www.idemia.com/mobile-id)

::: zone-end
