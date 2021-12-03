---
title: Tutorial zum Konfigurieren von BioCatch mit Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Tutorial zum Konfigurieren von Azure Active Directory B2C mit BioCatch zum Identifizieren von riskanten und betrügerischen Benutzern
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/20/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 173ef82e77e44f3b7e2a664d613b1810995fee21
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131044796"
---
# <a name="tutorial-configure-biocatch-with-azure-active-directory-b2c"></a>Tutorial: Konfigurieren von BioCatch mit Azure Active Directory B2C

In diesem Beispieltutorial erfahren Sie, wie Sie die Azure Active Directory (AD) B2C-Authentifizierung in [BioCatch](https://www.biocatch.com/) integrieren, um den Sicherheitsstatus Ihrer Identitäts- und Zugriffsverwaltung für Kunden (Customer Identity and Access Management, CIAM) weiter zu verbessern. BioCatch analysiert das physische und kognitive digitale Verhalten eines Benutzers, um Erkenntnisse zu gewinnen, die zwischen legitimen Kunden und Cyberkriminellen unterscheiden.

## <a name="prerequisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

- Ein Azure-Abonnement. Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.

- [Einen Azure AD B2C-Mandanten](tutorial-create-tenant.md), der mit Ihrem Azure-Abonnement verknüpft ist.

- Ein [BioCatch-Konto](https://www.biocatch.com/contact-us).

## <a name="scenario-description"></a>Beschreibung des Szenarios

Die BioCatch-Integration umfasst die folgenden Komponenten:

- **Eine Web-App oder einen Webdienst**: Der Benutzer navigiert zuerst zu diesem Webdienst. Dieser Webdienst instanziiert eine eindeutige Clientsitzungs-ID, die an BioCatch gesendet wird. Die Clientsitzungs-ID beginnt dann sofort mit der Übertragung von Benutzerverhaltensmerkmalen an BioCatch.

- **Eine Methode**: Sendet die eindeutige Clientsitzungs-ID an Azure AD B2C. Im angegebenen Beispiel wird JavaScript verwendet, um den Wert in ein ausgeblendetes HTML-Feld einzugeben.

- **Eine benutzerdefinierte Azure AD B2C-Benutzeroberfläche**: Blendet ein HTML-Feld für die Eingabe der Clientsitzungs-ID aus JavaScript aus, wenn die oben genannte Methode verwendet wird

- **Benutzerdefinierte Azure AD B2C-Richtlinie**

  - Übernimmt die benutzerdefinierte Clientsitzungs-ID aus der Benutzeroberfläche in Form eines Anspruchs. Dies wird über ein selbstbestätigtes technisches Profil erreicht.

  - Die Integration in BioCatch erfolgt über einen REST-API-Anspruchsanbieter, und die Clientsitzungs-ID wird an die BioCatch-Plattform übergeben.

  - Von BioCatch werden mehrere benutzerdefinierte Ansprüche zurückgegeben, worauf die benutzerdefinierte Richtlinienlogik dann reagieren kann.

  - Eine User Journey, die einen zurückgegebenen Anspruch (z. B. das Sitzungsrisiko) auswertet und bedingt eine Aktion wie das Aufrufen der mehrstufigen Authentifizierung (Multi-Factor Authentication, MFA) ausführt.

![Diagramm der BioCatch-Architektur.](media/partner-biocatch/biocatch-architecture-diagram.png)

| Schritt  | Beschreibung |
|:---|:-----------------------|
|1a     | Der Benutzer navigiert durch den Webdienst. Der Webdienst gibt dann HTML-, CSS- oder JavaScript-Werte zurück und nimmt die Konfiguration vor, um das BioCatch JavaScript SDK zu laden. Über clientseitigen JavaScript-Code wird die Clientsitzungs-ID für das BioCatch SDK konfiguriert/festgelegt. Alternativ kann der Webdienst die Clientsitzungs-ID vorkonfigurieren und an den Client senden.        |
|1b    |  Konfigurieren Sie das instanziierte BioCatch JavaScript SDK für die BioCatch-Plattform. Beginnen Sie sofort mithilfe der Clientsitzungs-ID aus Schritt 1a damit, Benutzerverhaltensmerkmale vom Clientgerät an BioCatch zu senden.    |
|2     |  Der Benutzer versucht, sich zu registrieren/anzumelden, und wird zu Azure AD B2C umgeleitet.      |
|3a    | Ein Teil der User Journey ist ein selbstbestätigter Anspruchsanbieter, der die Clientsitzungs-ID als Eingabe verwendet. Dieses Feld wird auf dem Bildschirm ausgeblendet. Sie können JavaScript verwenden, um die Sitzungs-ID in das Feld einzugeben. Wählen Sie die Schaltfläche *Weiter* aus, um den Registrierungs-/Anmeldevorgang fortzusetzen.|
|3b     | Die Clientsitzungs-ID wird an die BioCatch-Plattform übermittelt, um eine Risikobewertung zu ermitteln. |
|3c     | BioCatch gibt Informationen über die Sitzung (z. B. eine Risikobewertung) und eine Empfehlung zur Vorgehensweise (Zulassen oder Blockieren) zurück. |
|3d    |Die User Journey umfasst einen bedingten Überprüfungsschritt, mit dem auf die zurückgegebenen Ansprüche reagiert wird.|
| 4   | Basierend auf dem Ergebnis der bedingten Überprüfung wird eine Aktion wie z. B. *Step-Up MFA* aufgerufen.|
|5    | Ab dem ersten Zugriff des Benutzers auf die Webdienstseite kann der Webdienst die Clientsitzungs-ID zum Abfragen der BioCatch-API verwenden, um die Risikobewertung und Sitzungsinformationen in Echtzeit zu ermitteln. |

## <a name="onboard-with-biocatch"></a>Durchführen des Onboardings mit BioCatch

Wenden Sie sich an [BioCatch](https://www.biocatch.com/contact-us), und erstellen Sie ein Konto.

## <a name="configure-the-custom-ui"></a>Konfigurieren der benutzerdefinierten Benutzeroberfläche

Es wird empfohlen, das Feld für die Clientsitzungs-ID auszublenden. Verwenden Sie zum Ausblenden des Felds CSS, JavaScript oder eine andere Methode. Zu Testzwecken können Sie das Feld (wieder) einblenden. Mit JavaScript wird das Eingabefeld beispielsweise wie folgt ausgeblendet:

```JavaScript
document.getElementById("clientSessionId").style.display = 'none';
```

## <a name="configure--azure-ad-b2c-identity-experience-framework-policies"></a>Konfigurieren von Azure AD B2C Identity Experience Framework-Richtlinien

1. Legen Sie die anfängliche [benutzerdefinierte Richtlinienkonfiguration](./tutorial-create-user-flows.md?pivots=b2c-custom-policy) fest.

2. Erstellen Sie eine neue Datei, die von der Erweiterungsdatei erbt.

    ```xml
    <BasePolicy> 

        <TenantId>tenant.onmicrosoft.com</TenantId> 

        <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId> 

      </BasePolicy> 
    ```

3. Erstellen Sie unter der BuildingBlocks-Ressource einen Verweis auf die benutzerdefinierte Benutzeroberfläche, um das Eingabefeld auszublenden.

    ```xml
    <ContentDefinitions> 

        <ContentDefinition Id="api.selfasserted"> 

            <LoadUri>https://domain.com/path/to/selfAsserted.cshtml</LoadUri> 

            <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.0</DataUri> 

          </ContentDefinition> 

        </ContentDefinitions>
    ```

4. Fügen Sie unter der BuildingBlocks-Ressource die folgenden Ansprüche hinzu.

    ```xml
    <ClaimsSchema> 

          <ClaimType Id="riskLevel"> 

            <DisplayName>Session risk level</DisplayName> 

            <DataType>string</DataType>       

          </ClaimType> 

          <ClaimType Id="score"> 

            <DisplayName>Session risk score</DisplayName> 

            <DataType>int</DataType>       

          </ClaimType> 

          <ClaimType Id="clientSessionId"> 

            <DisplayName>The ID of the client session</DisplayName> 

            <DataType>string</DataType> 

            <UserInputType>TextBox</UserInputType> 

          </ClaimType> 

    </ClaimsSchema> 
    ```

5. Konfigurieren Sie den selbstbestätigten Anspruchsanbieter für das Feld für die Clientsitzungs-ID.

    ```xml
    <ClaimsProvider> 

          <DisplayName>Client Session ID Claims Provider</DisplayName> 

          <TechnicalProfiles> 

            <TechnicalProfile Id="login-NonInteractive-clientSessionId"> 

              <DisplayName>Client Session ID TP</DisplayName> 

              <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" /> 

              <Metadata> 

                <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item> 

              </Metadata> 

              <CryptographicKeys> 

                <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" /> 

              </CryptographicKeys> 

            <!—Claim we created earlier --> 

              <OutputClaims> 

                <OutputClaim ClaimTypeReferenceId="clientSessionId" Required="false" DefaultValue="100"/> 

              </OutputClaims> 

            <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" /> 

            </TechnicalProfile> 

          </TechnicalProfiles> 

        </ClaimsProvider> 
    ```

6. Konfigurieren Sie den REST-API-Anspruchsanbieter für BioCatch. 

    ```xml
    <TechnicalProfile Id="BioCatch-API-GETSCORE"> 

          <DisplayName>Technical profile for BioCatch API to return session information</DisplayName> 

          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />

          <Metadata> 

            <Item Key="ServiceUrl">https://biocatch-url.com/api/v6/score?customerID=<customerid>&amp;action=getScore&amp;uuid=<uuid>&amp;customerSessionID={clientSessionId}&amp;solution=ATO&amp;activtyType=<activity_type>&amp;brand=<brand></Item>

            <Item Key="SendClaimsIn">Url</Item> 

            <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item> 

            <!-- Set AuthenticationType to Basic or ClientCertificate in production environments --> 

            <Item Key="AuthenticationType">None</Item> 

            <!-- REMOVE the following line in production environments --> 

            <Item Key="AllowInsecureAuthInProduction">true</Item> 

          </Metadata> 

          <InputClaims> 

            <InputClaim ClaimTypeReferenceId="clientsessionId" /> 

          </InputClaims> 

          <OutputClaims> 

            <OutputClaim ClaimTypeReferenceId="riskLevel" /> 

            <OutputClaim ClaimTypeReferenceId="score" /> 

          </OutputClaims> 

          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" /> 

        </TechnicalProfile> 

      </TechnicalProfiles>
    ```

    > [!NOTE]
    > BioCatch stellt die zu konfigurierende URL, Kunden-ID und eindeutige Benutzer-ID (Unique User ID, UUID) für Sie bereit. Der SessionID-Anspruch des Kunden wird als Abfragezeichenfolge-Parameter an BioCatch übergeben. Sie können den Aktivitätstyp (z. B. *MAKE_PAYMENT*) auswählen.

7. Konfigurieren Sie die User Journey. Folgen Sie dem Beispiel.

   1. Abrufen der clientSessionID als Anspruch

   1. Aufrufen der BioCatch-API zum Abrufen der Sitzungsinformationen

   1. Wenn der zurückgegebene Anspruch für die *Risikostufe* den Wert *LOW* aufweist, überspringen Sie den Schritt für die mehrstufige Authentifizierung. Andernfalls erzwingen Sie die mehrstufige Authentifizierung für den Benutzer.

    ```xml
    <OrchestrationStep Order="8" Type="ClaimsExchange"> 

          <ClaimsExchanges> 

            <ClaimsExchange Id="clientSessionIdInput" TechnicalProfileReferenceId="login-NonInteractive-clientSessionId" /> 

          </ClaimsExchanges> 

        </OrchestrationStep> 

        <OrchestrationStep Order="9" Type="ClaimsExchange"> 

          <ClaimsExchanges> 

            <ClaimsExchange Id="BcGetScore" TechnicalProfileReferenceId=" BioCatch-API-GETSCORE" /> 

          </ClaimsExchanges> 

        </OrchestrationStep> 

        <OrchestrationStep Order="10" Type="ClaimsExchange"> 

          <Preconditions> 

            <Precondition Type="ClaimEquals" ExecuteActionsIf="true"> 

              <Value>riskLevel</Value> 

              <Value>LOW</Value> 

              <Action>SkipThisOrchestrationStep</Action> 

            </Precondition> 

          </Preconditions> 

          <ClaimsExchanges> 

            <ClaimsExchange Id="PhoneFactor-Verify" TechnicalProfileReferenceId="PhoneFactor-InputOrVerify" /> 

          </ClaimsExchanges>
    ```

8. Festlegen der Konfiguration der vertrauenden Seite (optional)

    Es ist sinnvoll, die von BioCatch zurückgegebenen Informationen als Ansprüche im Token an Ihre Anwendung zu übergeben, insbesondere die Risikostufe (*riskLevel*) und die Bewertung (*score*).

    ```xml
    <RelyingParty> 

    <DefaultUserJourney ReferenceId="SignUpOrSignInMfa" /> 

    <UserJourneyBehaviors> 

      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" /> 

      <SessionExpiryType>Absolute</SessionExpiryType> 

      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds> 

      <ScriptExecution>Allow</ScriptExecution> 

    </UserJourneyBehaviors> 

    <TechnicalProfile Id="PolicyProfile"> 

      <DisplayName>PolicyProfile</DisplayName> 

      <Protocol Name="OpenIdConnect" /> 

      <OutputClaims> 

        <OutputClaim ClaimTypeReferenceId="displayName" /> 

        <OutputClaim ClaimTypeReferenceId="givenName" /> 

        <OutputClaim ClaimTypeReferenceId="surname" /> 

        <OutputClaim ClaimTypeReferenceId="email" /> 

        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" /> 

        <OutputClaim ClaimTypeReferenceId="identityProvider" />                 

        <OutputClaim ClaimTypeReferenceId="riskLevel" /> 

        <OutputClaim ClaimTypeReferenceId="score" /> 

        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" /> 

      </OutputClaims> 

      <SubjectNamingInfo ClaimType="sub" /> 

    </TechnicalProfile> 

  </RelyingParty>
    ```

## <a name="integrate-with-azure-ad-b2c"></a>Integration in Azure AD B2C

Führen Sie die folgenden Schritte aus, um Azure AD B2C die Richtliniendateien hinzuzufügen:

1. Melden Sie sich als globaler Administrator Ihres Azure AD B2C-Mandanten beim  [**Azure-Portal**](https://portal.azure.com/) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach Azure AD B2C, und wählen Sie dann diese Option aus.
1. Navigieren Sie zu  **Azure AD B2C** > **Identity Experience Framework**.
1. Laden Sie alle Richtliniendateien in Ihren Mandanten hoch.

## <a name="test-the-solution"></a>Testen der Lösung

1. [Registrieren Sie eine Dummyanwendung, die zu JWT.MS umleitet](./tutorial-register-applications.md?tabs=app-reg-ga).  
1. Wählen Sie unter **Identity Experience Framework** die von Ihnen erstellte Richtlinie aus.
1. Wählen Sie im Richtlinienfenster die JWT.MS-Dummyanwendung aus, und wählen Sie dann **Jetzt ausführen** aus.
1. Führen Sie den Registrierungsflow aus, und erstellen Sie ein Konto. Das an JWT.MS zurückgegebene Token sollte 2 Ansprüche für „riskLevel“ und „score“ enthalten. Folgen Sie dem Beispiel.  

    ```JavaScript
    { 

      "typ": "JWT", 

      "alg": "RS256", 

      "kid": "_keyid" 

    }.{ 

      "exp": 1615872580, 

      "nbf": 1615868980, 

      "ver": "1.0", 

      "iss": "https://tenant.b2clogin.com/12345678-1234-1234-1234-123456789012/v2.0/", 

      "sub": "12345678-1234-1234-1234-123456789012", 

      "aud": "12345678-1234-1234-1234-123456789012", 

      "acr": "b2c_1a_signup_signin_biocatch_policy", 

      "nonce": "defaultNonce", 

      "iat": 1615868980, 

      "auth_time": 1615868980, 

      "name": "John Smith", 

      "email": "john.smith@contoso.com", 

      "given_name": "John", 

      "family_name": "Smith", 

      "riskLevel": "LOW", 

      "score": 275, 

      "tid": "12345678-1234-1234-1234-123456789012" 

    }.[Signature]  

    ```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Benutzerdefinierte Richtlinien in Azure AD B2C](./custom-policy-overview.md)

- [Erste Schritte mit benutzerdefinierten Richtlinien in Azure AD B2C](./tutorial-create-user-flows.md?pivots=b2c-custom-policy&tabs=applications)
