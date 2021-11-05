---
title: Hinzufügen von Benutzerattributen und Anpassen der Benutzereingabe
titleSuffix: Azure AD B2C
description: Erfahren Sie, wie Sie Benutzereingaben anpassen und der User Journey für die Registrierung bzw. Anmeldung in Azure Active Directory B2C Benutzerattribute hinzufügen.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/22/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 042829ac525ed911f1a9ec984843931e8029b0a6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131007465"
---
#  <a name="add-user-attributes-and-customize-user-input-in-azure-active-directory-b2c"></a>Hinzufügen von Benutzerattributen und Anpassen der Benutzereingabe in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

In diesem Artikel erfahren Sie, wie Sie während der User Journey für die Registrierung in Azure Active Directory B2C (Azure AD B2C) ein neues Attribut sammeln. Sie erhalten den Ort des Benutzers, konfigurieren diesen als Dropdownoption und legen fest, ob er bereitgestellt werden muss.

> [!IMPORTANT]
> In diesem Beispiel wird der integrierte Anspruch „City“ verwendet. Stattdessen können Sie eines der unterstützten [integrierten Azure AD B2C-Attribute](user-profile-attributes.md) oder ein benutzerdefiniertes Attribut wählen. Um ein benutzerdefiniertes Attribut zu verwenden, [aktivieren Sie benutzerdefinierte Attribute](user-flow-custom-attributes.md). Um ein anderes integriertes oder benutzerdefiniertes Attribut zu verwenden, ersetzen Sie „city“ durch das Attribut Ihrer Wahl, z. B. das integrierte Attribut *jobTitle* oder ein benutzerdefiniertes Attribut wie *extension_loyaltyId*.  

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="add-user-attributes-your-user-flow"></a>Hinzufügen von Benutzerattributen zum Benutzerflow

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie unter **Azure-Dienste** die Option **Azure AD B2C** aus. Verwenden Sie alternativ das Suchfeld, um nach **Azure AD B2C** zu suchen und diese Option auszuwählen.
1. Wählen Sie in Ihrem Azure AD B2C-Mandanten die Option **Benutzerflows** aus.
1. Wählen Sie eine Richtlinie aus (z.B. „B2C_1_SignupSignin“), um sie zu öffnen.
1. Wählen Sie **Benutzerattribute** und dann das Benutzerattribut aus (z. B. „City“). 
1. Wählen Sie **Speichern** aus.

## <a name="provide-optional-claims-to-your-app"></a>Bereitstellen optionaler Ansprüche für Ihre App

Die Anwendungsansprüche sind Werte, die an die Anwendung zurückgegeben werden. Aktualisieren Sie den Benutzerflow, sodass dieser die gewünschten Ansprüche enthält.

1. Wählen Sie eine Richtlinie aus (z.B. „B2C_1_SignupSignin“), um sie zu öffnen.
1. Wählen Sie **Anwendungsansprüche** aus.
1. Wählen Sie die Attribute aus, die an die Anwendung zurückgesendet werden sollen (z. B. „City“).
1. Wählen Sie **Speichern** aus.
 
## <a name="configure-user-attributes-input-type"></a>Konfigurieren des Eingabetyps von Benutzerattributen

1. Wählen Sie eine Richtlinie aus (z.B. „B2C_1_SignupSignin“), um sie zu öffnen.
1. Wählen Sie **Seitenlayouts** aus.
1. Wählen Sie **Registrierungsseite für lokales Konto** aus.
1. Wählen Sie unter **Benutzerattribute** die Option **City** aus.
    1. Wählen Sie in der Dropdownliste **Benutzereingabetyp** die Option **DropdownSingleSelect** aus. Optional: Verwenden Sie die Schaltflächen "nach oben/nach unten", um den Text auf der Anmeldeseite nach einer bestimmten Reihenfolge anzuordnen.
    1. Wählen Sie in der Dropdownliste **Optional** die Option **Nein** aus.
1. Wählen Sie **Speichern** aus. 

### <a name="provide-a-list-of-values-by-using-localized-collections"></a>Angeben einer Werteliste mithilfe lokalisierter Sammlungen

So geben Sie eine Liste von Werten für das City-Attribut an 

1. [Aktivieren der Sprachanpassung im Benutzerflow](language-customization.md#support-requested-languages-for-ui_locales)
1. Wählen Sie eine Richtlinie aus (z.B. „B2C_1_SignupSignin“), um sie zu öffnen.
1. Wählen Sie auf der Seite **Sprachen** für den Benutzerflow die Sprache aus, die Sie anpassen möchten.
1. Wählen Sie unter **Ressourcendateien auf Seitenebene** die Option **Registrierungsseite für lokales Konto** aus.
1. Klicken Sie auf **Standardeinstellungen herunterladen** (oder **Außerkraftsetzungen herunterladen**, wenn Sie diese Sprache zuvor bearbeitet haben).
1. Erstellen Sie ein `LocalizedCollections`-Attribut.

`LocalizedCollections` ist ein Array mit Paaren aus `Name` und `Value`. Die Reihenfolge der Elemente ist dieselbe, in der sie angezeigt werden. 

* `ElementId` ist das Benutzerattribut, für das dieses Attribut vom Typ `LocalizedCollections` eine Antwort darstellt.
* `Name` ist der Wert, der dem Benutzer angezeigt wird.
* `Value` wird im Anspruch zurückgegeben, wenn diese Option ausgewählt wird.

```json
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [
    {
      "ElementType": "ClaimType",
      "ElementId": "city",
      "TargetCollection": "Restriction",
      "Override": true,
      "Items": [
        {
          "Name": "Berlin",
          "Value": "Berlin"
        },
        {
          "Name": "London",
          "Value": "London"
        },
        {
          "Name": "Seattle",
          "Value": "Seattle"
        }
      ]
    }
  ]
}
```

#### <a name="upload-your-changes"></a>Hochladen von Änderungen

1. Kehren Sie nach dem Ändern der JSON-Datei wieder zu Ihrem B2C-Mandanten zurück.
1. Wählen Sie **Benutzerflows** und dann die Richtlinie aus (z. B. „B2C_1_SignupSignin“), um sie zu öffnen.
1. Wählen Sie **Sprachen** aus.
1. Wählen Sie die gewünschte Zielsprache für die Übersetzung aus.
1. Wählen Sie unter **Ressourcendateien auf Seitenebene** die Option **Registrierungsseite für lokales Konto** aus.
1. Klicken Sie auf das Ordnersymbol, und wählen Sie die hochzuladende JSON-Datei aus. Die Änderungen werden automatisch in Ihrem Benutzerflow gespeichert.

## <a name="test-your-user-flow"></a>Testen des Benutzerflows

1. Wählen Sie eine Richtlinie aus (z.B. „B2C_1_SignupSignin“), um sie zu öffnen.
1. Um die Richtlinie zu testen, wählen Sie **Benutzerflow ausführen** aus.
1. Wählen Sie für **Anwendung** die Webanwendung *testapp1* aus, die Sie zuvor registriert haben. Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Klicken Sie auf **Benutzerflow ausführen**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="overview"></a>Übersicht

Sie können die anfänglichen Daten von Ihren Benutzern mithilfe der User Journey für die Registrierung bzw. Anmeldung sammeln. Weitere Ansprüche können später mit einer User Journey für die Profilbearbeitung gesammelt werden. Jedes Mal, wenn Azure AD B2C auf interaktive Weise Informationen direkt vom Benutzer sammelt, wird das [selbstbestätigte technische Profil](self-asserted-technical-profile.md) verwendet. In diesem Beispiel führen Sie folgende Schritte aus:

1. Sie definieren den Anspruch „Ort“. 
1. Sie fragen den Benutzer nach seinem Ort.
1. Sie speichern den Ort dauerhaft im Benutzerprofil im Azure AD B2C-Verzeichnis.
1. Sie lesen bei jeder Anmeldung den Anspruch „Ort“ aus dem Azure AD B2C-Verzeichnis.
1. Sie geben nach der Anmeldung oder Registrierung den Ort an die Anwendung der vertrauenden Seite zurück.  

## <a name="define-a-claim"></a>Definieren eines Anspruchs

Ein Anspruch stellt eine temporäre Speicherung von Daten während der Ausführung einer Azure AD B2C-Richtlinie bereit. Im [Anspruchsschema](claimsschema.md) deklarieren Sie Ihre Ansprüche. Die folgenden Elemente werden zum Definieren des Anspruchs verwendet:

- **DisplayName**: Zeichenfolge, mit der die Bezeichnung für den Benutzer definiert wird.
- [DataType](claimsschema.md#datatype): Typ des Anspruchs.
- **UserHelpText**: Verdeutlicht dem Benutzer, was erforderlich ist.
- [UserInputType](claimsschema.md#userinputtype): Typ der Eingabesteuerung (z. B. Textfeld, Optionsfeld, Dropdownliste oder Mehrfachauswahl).

Öffnen Sie die Erweiterungsdatei Ihrer Richtlinie. Beispiel: <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.

1. Suchen Sie nach dem Element [BuildingBlocks](buildingblocks.md). Wenn das Element nicht vorhanden ist, fügen Sie es hinzu.
1. Suchen Sie nach dem Element [ClaimsSchema](claimsschema.md). Wenn das Element nicht vorhanden ist, fügen Sie es hinzu.
1. Fügen Sie dem Element **ClaimsSchema-** Element den Anspruch „Ort“ hinzu.  

```xml
<!-- 
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="city">
      <DisplayName>City where you work</DisplayName>
      <DataType>string</DataType>
      <UserInputType>DropdownSingleSelect</UserInputType>
      <Restriction>
        <Enumeration Text="Berlin" Value="berlin" />
        <Enumeration Text="London" Value="london" />
        <Enumeration Text="Seattle" Value="seattle" />
      </Restriction>
    </ClaimType>
  <!-- 
  </ClaimsSchema>
</BuildingBlocks>-->
```

Verwenden Sie das [SelectByDefault](claimsschema.md#enumeration)-Attribut für ein `Enumeration`-Element, damit es beim ersten Laden der Seite standardmäßig ausgewählt wird. Um beispielsweise das Element *London* vorab auszuwählen, ändern Sie das `Enumeration`-Element wie im folgenden Beispiel:

```xml
<Restriction>
  <Enumeration Text="Berlin" Value="berlin" />
  <Enumeration Text="London" Value="london" SelectByDefault="true" />
  <Enumeration Text="Seattle" Value="seattle" />
</Restriction>
```

## <a name="add-a-claim-to-the-user-interface"></a>Hinzufügen eines Anspruchs zur Benutzeroberfläche

Die folgenden technischen Profile sind [selbstbestätigt](self-asserted-technical-profile.md) und werden aufgerufen, wenn von einem Benutzer eine Eingabe erwartet wird:

- **LocalAccountSignUpWithLogonEmail**: Registrierungsflow mit lokalem Konto.
- **SelfAsserted-Social**: Erstmalige Benutzeranmeldung mit Verbundkonto.
- **SelfAsserted-ProfileUpdate**: Profilbearbeitungsflow.

Um den Anspruch „Ort“ bei der Registrierung zu sammeln, muss er dem technischen Profil `LocalAccountSignUpWithLogonEmail` als Ausgabeanspruch hinzugefügt werden. Überschreiben Sie dieses technische Profil in der Erweiterungsdatei. Geben Sie die gesamte Liste von Ausgabeansprüchen an, um die Reihenfolge zu steuern, in der die Ansprüche auf dem Bildschirm angezeigt werden. Suchen Sie nach dem Element **ClaimsProviders**. Fügen Sie ein neues Element „ClaimsProvider“ wie folgt hinzu:

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <!--Local account sign-up page-->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <OutputClaims>
       <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
       <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="displayName" />
       <OutputClaim ClaimTypeReferenceId="givenName" />
       <OutputClaim ClaimTypeReferenceId="surName" />
       <OutputClaim ClaimTypeReferenceId="city"/>
     </OutputClaims>
   </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

Zum Sammeln des Anspruchs „Ort“ nach der erstmaligen Anmeldung mit einem Verbundkonto muss er dem technischen Profil `SelfAsserted-Social` als Ausgabeanspruch hinzugefügt werden. Damit Benutzer von lokalen Konten und Verbundkonten ihre Profildaten später bearbeiten können, fügen Sie den Eingabe- und Ausgabeanspruch dem technischen Profil `SelfAsserted-ProfileUpdate` hinzu. Überschreiben Sie diese technischen Profile in der Erweiterungsdatei. Geben Sie die gesamte Liste von Ausgabeansprüchen an, um die Reihenfolge zu steuern, in der die Ansprüche auf dem Bildschirm angezeigt werden. Suchen Sie nach dem Element **ClaimsProviders**. Fügen Sie ein neues Element „ClaimsProvider“ wie folgt hinzu:

```xml
<ClaimsProvider>
  <DisplayName>Self Asserted</DisplayName>
  <TechnicalProfiles>
    <!--Federated account first-time sign-in page-->
    <TechnicalProfile Id="SelfAsserted-Social">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="city" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName"/>
        <OutputClaim ClaimTypeReferenceId="surname"/>
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
    <!--Edit profile page-->
    <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="city" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="read-and-write-a-claim"></a>Lesen und Schreiben eines Anspruchs

Die folgenden technischen Profile sind [technische Active Directory-Profile](active-directory-technical-profile.md), die Daten in Azure Active Directory schreiben und daraus lesen.  
Verwenden Sie `PersistedClaims`, um Daten in das Benutzerprofil zu schreiben und `OutputClaims`, um innerhalb der entsprechenden technischen Active Directory-Profile Daten aus dem Benutzerprofil zu lesen.

Überschreiben Sie diese technischen Profile in der Erweiterungsdatei. Suchen Sie nach dem Element **ClaimsProviders**.  Fügen Sie ein neues Element „ClaimsProvider“ wie folgt hinzu:

```xml
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <!-- Write data during a local account sign-up flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during a federated account first-time sign-in flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during edit profile flow. -->
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Read data after user resets the password. -->
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a local account. -->
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a federated account. -->
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="include-a-claim-in-the-token"></a>Einschließen eines Anspruchs in das Token 

Um den Anspruch „Ort“ an die Anwendung der vertrauenden Seite zurückzugeben, fügen Sie der Datei <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> einen Ausgabeanspruch hinzu. Der Ausgabeanspruch wird nach einer erfolgreichen User Journey in das Token eingefügt und an die Anwendung gesendet. Ändern Sie im Abschnitt für die vertrauende Seite das Element des technischen Profils, um den Ort als Ausgabeanspruch hinzuzufügen.
 
```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
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
      <OutputClaim ClaimTypeReferenceId="city" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="upload-and-test-your-updated-custom-policy"></a>Hochladen und Testen Ihrer aktualisierten benutzerdefinierten Richtlinie

1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Suchen Sie nach **Azure AD B2C**, und wählen Sie diese Option aus.
1. Wählen Sie unter **Richtlinien** die Option **Identity Experience Framework** aus.
1. Wählen Sie **Benutzerdefinierte Richtlinie hochladen** aus.
1. Laden Sie die Richtliniendateien hoch, die Sie zuvor geändert haben.

### <a name="test-the-custom-policy"></a>Testen der benutzerdefinierten Richtlinie

1. Wählen Sie die Richtliniendatei für die vertrauende Seite aus, z. B. `B2C_1A_signup_signin`.
1. Wählen Sie für **Anwendung** eine Webanwendung aus, die Sie [zuvor registriert haben](tutorial-register-applications.md). Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Wählen Sie die Schaltfläche **Jetzt ausführen** aus.
1. Wählen Sie auf der Registrierungs- oder Anmeldeseite die Option **Jetzt registrieren** aus, um sich zu registrieren. Schließen Sie die Eingabe der Benutzerinformationen einschließlich des Ortsnamens ab, und klicken Sie dann auf **Erstellen**. Es sollten Ihnen jetzt die Inhalte des zurückgegebenen Tokens angezeigt werden.

::: zone-end

Der Registrierungsbildschirm sollte ähnlich wie im folgenden Screenshot aussehen:

![Screenshot der geänderten Registrierungsoption](./media/configure-user-input/signup-with-city-claim-drop-down-example.png)

Das Token, das an die Anwendung zurückgesendet wird, enthält den Anspruch `city`.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1583500140,
  "nbf": 1583496540,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1583496540,
  "auth_time": 1583496540,
  "name": "Emily Smith",
  "email": "joe@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "city": "Berlin"
  ...
}
```

::: zone pivot="b2c-custom-policy"

## <a name="optional-localize-the-ui"></a>[Optional] Lokalisieren der Benutzeroberfläche

Azure AD B2C ermöglicht es Ihnen, Ihre Richtlinie in verschiedenen Sprachen bereitzustellen. Weitere Informationen finden Sie unter [Sprachanpassung in Azure Active Directory B2C](language-customization.md). Um die Registrierungsseite zu lokalisieren, [richten Sie die Liste der unterstützten Sprachen ein](language-customization.md#set-up-the-list-of-supported-languages), und [geben Sie sprachspezifische Bezeichnungen an](language-customization.md#provide-language-specific-labels).

> [!NOTE]
> Wenn Sie `LocalizedCollection` mit den sprachspezifischen Bezeichnungen verwenden, können Sie die `Restriction`-Sammlung aus der [Anspruchsdefinition](#define-a-claim) entfernen.

Im folgenden Beispiel wird veranschaulicht, wie Sie die Liste der Städte für Englisch und Spanisch bereitstellen. Beide legen die `Restriction`-Sammlung des Anspruchs *city* auf eine Liste von Elementen für Englisch und Spanisch fest. Das [SelectByDefault](claimsschema.md#enumeration)-Attribut sorgt dafür, dass ein Element beim ersten Laden der Seite standardmäßig ausgewählt wird.
   
```xml
<!-- 
<BuildingBlocks>-->
  <Localization Enabled="true">
    <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
      <SupportedLanguage>en</SupportedLanguage>
      <SupportedLanguage>es</SupportedLanguage>
    </SupportedLanguages>
    <LocalizedResources Id="api.localaccountsignup.en">
      <LocalizedCollections>
        <LocalizedCollection ElementType="ClaimType" ElementId="city" TargetCollection="Restriction">
          <Item Text="Berlin" Value="Berlin"></Item>
          <Item Text="London" Value="London" SelectByDefault="true"></Item>
          <Item Text="Seattle" Value="Seattle"></Item>
        </LocalizedCollection>
      </LocalizedCollections>
    </LocalizedResources>
    <LocalizedResources Id="api.localaccountsignup.es">
      <LocalizedCollections>
        <LocalizedCollection ElementType="ClaimType" ElementId="city" TargetCollection="Restriction">
          <Item Text="Berlina" Value="Berlin"></Item>
          <Item Text="Londres" Value="London" SelectByDefault="true"></Item>
          <Item Text="Seattle" Value="Seattle"></Item>
        </LocalizedCollection>
      </LocalizedCollections>
    </LocalizedResources>
  </Localization>
<!-- 
</BuildingBlocks>-->
```

Nachdem Sie das Lokalisierungselement hinzugefügt haben, können Sie [die Inhaltsdefinition mit der Lokalisierung bearbeiten](language-customization.md#edit-the-content-definition-with-the-localization). Im folgenden Beispiel werden der Registrierungsseite benutzerdefinierte lokalisierte Ressourcen für Englisch („en“) und Spanisch („es“) hinzugefügt:
   
```xml
<!-- 
<BuildingBlocks>
  <ContentDefinitions> -->
   <ContentDefinition Id="api.localaccountsignup">
    <LocalizedResourcesReferences MergeBehavior="Prepend">
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
        <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    </LocalizedResourcesReferences>
   </ContentDefinition>
  <!-- 
  </ContentDefinitions>
</BuildingBlocks>-->
```

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das Element [ClaimsSchema](claimsschema.md) in der IEF-Referenz.
- Erfahren Sie, wie Sie [benutzerdefinierte Attribute in Azure AD B2C verwenden](user-flow-custom-attributes.md).

::: zone-end
