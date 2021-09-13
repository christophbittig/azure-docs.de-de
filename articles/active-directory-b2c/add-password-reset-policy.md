---
title: Einrichten eines Kennwortzurücksetzungsflows in Azure AD B2C
titleSuffix: Azure AD B2C
description: Hier erfahren Sie, wie Sie einen Kennwortzurücksetzungsflow in Azure Active Directory B2C einrichten.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/01/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 1c7d4eeaf7df1764b021cd5914d6f4f4a88a9a1c
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/02/2021
ms.locfileid: "113213469"
---
# <a name="set-up-a-password-reset-flow-in-azure-active-directory-b2c"></a>Einrichten eines Kennwortzurücksetzungsflows in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="overview"></a>Übersicht

Im Rahmen des [Registrierungs- und Anmeldevorgangs](add-sign-up-and-sign-in-policy.md) können Benutzer ihr eigenes Kennwort über den Link **Kennwort vergessen?** zurücksetzen. Dieser Flow für die Self-Service-Kennwortzurücksetzung wird für lokale Konten in Azure AD B2C verwendet, bei denen für die Anmeldung eine [E-Mail-Adresse](sign-in-options.md#email-sign-in) oder ein [Benutzername](sign-in-options.md#username-sign-in) und ein Kennwort verwendet werden.

Der Kennwortzurücksetzungsflow umfasst folgende Schritte:

![Kennwortzurücksetzungsflow](./media/add-password-reset-policy/password-reset-flow.png)

**1.** Der Benutzer klickt auf der Registrierungs- und Anmeldeseite auf den Link **Kennwort vergessen?** . Daraufhin wird von Azure AD B2C der Kennwortzurücksetzungsflow initiiert.

**2.** Der Benutzer gibt seine E-Mail-Adresse ein und klickt dann auf **Prüfcode senden**. Azure AD B2C sendet den Prüfcode an das Postfach des Benutzers. Der Benutzer kopiert den Prüfcode aus der E-Mail, gibt den Code auf der Seite für die Azure AD B2C-Kennwortzurücksetzung ein und klickt dann auf **Code überprüfen**.

**3.** Anschließend kann der Benutzer ein neues Kennwort eingeben. (Nach der Überprüfung der E-Mail-Adresse kann der Benutzer weiterhin auf die Schaltfläche **E-Mail-Adresse ändern** klicken (siehe folgender Abschnitt [Ausblenden der Schaltfläche zum Ändern der E-Mail-Adresse](#hiding-the-change-email-button)).)

> [!TIP]
> Mithilfe der Schritte zur Self-Service-Kennwortzurücksetzung können Benutzer, die ihr Kennwort vergessen haben und es zurücksetzen möchten, ihr Kennwort ändern. 
> - Wenn ein Benutzer sein Kennwort kennt und dieses ändern möchte, sollte ein [Kennwortänderungsflow](add-password-change-policy.md) verwendet werden. 
> - In Fällen, in denen Sie erzwingen möchten, dass Benutzer ihr Kennwort zurücksetzen (z. B. bei der erstmaligen Anmeldung, nach der Zurücksetzung des Kennworts durch einen Administrator oder nach der Migration zu Azure AD B2C mit einem zufälligen Kennwort), verwenden Sie einen [Flow zum Erzwingen der Kennwortzurücksetzung](force-password-reset.md).

### <a name="hiding-the-change-email-button"></a>Ausblenden der Schaltfläche zum Ändern der E-Mail-Adresse

Nachdem die E-Mail-Adresse überprüft wurde, kann der Benutzer weiterhin die Option **E-Mail-Adresse ändern** auswählen, die andere E-Mail-Adresse eingeben und die Überprüfung der E-Mail-Adresse von Anfang an wiederholen. Wenn Sie die Schaltfläche **E-Mail-Adresse ändern** ausblenden möchten, können Sie die CSS ändern, um die zugehörigen HTML-Elemente auf der Seite auszublenden. Sie können beispielsweise den folgenden CSS-Eintrag zu „selfAsserted.HTML“ hinzufügen und [die Benutzeroberfläche mit HTML-Vorlagen anpassen](customize-ui-with-html.md).

```html
<style type="text/css">
   .changeClaims
   {
     visibility: hidden;
   }
</style>
```

Beachten Sie, dass der Standardname der Schaltfläche **E-Mail-Adresse ändern** auf der Seite „selfasserted.html“ `changeclaims` lautet. Sie können den Namen der Schaltfläche ermitteln, indem Sie mithilfe eines Browsertools (z. B. Inspect) die Seitenquelle der Anmeldeseite überprüfen.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="self-service-password-reset-recommended"></a>Self-Service-Kennwortzurücksetzung (Self-Service Password Reset, SSPR) (empfohlen)

Die neue Kennwortzurücksetzung ist nun Teil der Registrierungs- oder Anmelderichtlinie. Wenn der Benutzer den Link **Kennwort vergessen?** auswählt, gelangt er sofort zur Kennwortzurücksetzung. Der [Fehlercode AADB2C90118](#password-reset-policy-legacy) muss von Ihrer Anwendung nicht mehr behandelt werden, und Sie benötigen keine separate Richtlinie für die Kennwortzurücksetzung.

::: zone pivot="b2c-user-flow"

Die Self-Service-Kennwortzurücksetzung kann für den Benutzerflow **Anmeldung (empfohlen)** oder **Registrierung und Anmeldung (empfohlen)** konfiguriert werden. Wenn Sie über keinen Benutzerflow dieser Art verfügen, erstellen Sie einen Benutzerflow vom Typ [Registrierung und Anmeldung](add-sign-up-and-sign-in-policy.md). 

So aktivieren Sie die Self-Service-Kennwortzurücksetzung für den Benutzerflow für die Registrierung oder Anmeldung:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.
1. Suchen Sie im Azure-Portal nach **Azure AD B2C**, und wählen Sie diese Option dann aus.
1. Wählen Sie **Benutzerflows** aus.
1. Wählen Sie einen Benutzerflow für die Registrierung oder Anmeldung (vom Typ **empfohlen**) aus, den Sie anpassen möchten.
1. Wählen Sie im Menü auf der linken Seite unter **Einstellungen** die Option **Eigenschaften** aus.
1. Wählen Sie unter der Registerkarte **Kennwortkonfiguration** die Option **Self-Service-Kennwortzurücksetzung** aus.
1. Wählen Sie **Speichern** aus.
1. Wählen Sie im linken Menü unter **Anpassen** die Option **Seitenlayouts** aus.
1. Wählen Sie unter der Registerkarte **Seitenlayout-Version** die Version **2.1.3** oder höher aus.
1. Wählen Sie **Speichern** aus.

::: zone-end

::: zone pivot="b2c-custom-policy"

In den folgenden Abschnitten erfahren Sie, wie Sie einer benutzerdefinierten Richtlinie eine Self-Service-Kennwortfunktion hinzufügen. Das Beispiel basiert auf den Richtliniendateien aus dem [Starter Pack für benutzerdefinierte Richtlinien](./tutorial-create-user-flows.md?pivots=b2c-custom-policy#custom-policy-starter-pack). 

> [!TIP]
> Ein umfassendes Beispiel für die Richtlinie zur Registrierung oder Anmeldung mit Kennwortzurücksetzung finden Sie auf [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/embedded-password-reset).

### <a name="indicate-a-user-selected-the-forgot-your-password-link"></a>Angeben, dass ein Benutzer den Link „Kennwort vergessen?“ ausgewählt hat

Definieren Sie einen booleschen Anspruch, um der Richtlinie gegenüber anzugeben, dass der Benutzer den Link **Kennwort vergessen?** ausgewählt hat. Dieser Anspruch dient dazu, die User Journey zum technischen Profil vom Typ „Kennwort vergessen“ weiterzuleiten. Der Anspruch kann auch an das Token ausgegeben werden, um der Anwendung zu vermitteln, dass sich der Benutzer über den Flow „Kennwort vergessen“ angemeldet hat.

Ansprüche werden im [Anspruchsschema](claimsschema.md) deklariert. Öffnen Sie die Erweiterungsdatei Ihrer Richtlinie. Beispiel: <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.

1. Suchen Sie nach dem Element [BuildingBlocks](buildingblocks.md). Wenn das Element nicht vorhanden ist, fügen Sie es hinzu.
1. Suchen Sie nach dem Element [ClaimsSchema](claimsschema.md). Wenn das Element nicht vorhanden ist, fügen Sie es hinzu.
1. Fügen Sie dem Element **ClaimsSchema** den folgenden Anspruch hinzu: 

```XML
<!-- 
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="isForgotPassword">
      <DisplayName>isForgotPassword</DisplayName>
      <DataType>boolean</DataType>
      <AdminHelpText>Whether the user has selected Forgot your Password</AdminHelpText>
    </ClaimType>
  <!--
  </ClaimsSchema>
</BuildingBlocks> -->
```

### <a name="upgrade-the-page-layout-version"></a>Upgraden der Seitenlayoutversion

Um den Self-Service-Kennwortzurücksetzungsflow für die Registrierung oder Anmeldung aktivieren zu können, benötigen Sie die [Seitenlayoutversion](contentdefinitions.md#migrating-to-page-layout) `2.1.2`.

1. Suchen Sie nach dem Element [BuildingBlocks](buildingblocks.md). Wenn das Element nicht vorhanden ist, fügen Sie es hinzu.
1. Suchen Sie nach dem Element [ContentDefinitions](contentdefinitions.md). Wenn das Element nicht vorhanden ist, fügen Sie es hinzu.
1. Ändern Sie innerhalb des Elements **ContentDefinition** mit der ID **api.signuporsignin** das Element **DataURI**:

```xml
<!-- 
<BuildingBlocks>
  <ContentDefinitions> -->
    <ContentDefinition Id="api.signuporsignin">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:2.1.2</DataUri>
    </ContentDefinition>
  <!-- 
  </ContentDefinitions>
</BuildingBlocks> -->
```

Zum Initiieren des Anspruchs `isForgotPassword` wird ein technisches Profil für die Anspruchstransformation verwendet. Auf dieses technische Profil wird später verwiesen. Wird es aufgerufen, wird der Wert des Anspruchs `isForgotPassword` auf `true` festgelegt. Suchen Sie das Element `ClaimsProviders`. Wenn das Element nicht vorhanden ist, fügen Sie es hinzu. Fügen Sie anschließend den folgenden Anspruchsanbieter hinzu:  

```xml
<!-- 
<ClaimsProviders> -->
  <ClaimsProvider>
    <DisplayName>Local Account</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="ForgotPassword">
        <DisplayName>Forgot your password?</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="isForgotPassword" DefaultValue="true" AlwaysUseDefaultValue="true"/>
        </OutputClaims>
      </TechnicalProfile>
      <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
        <Metadata>
          <Item Key="setting.forgotPasswordLinkOverride">ForgotPasswordExchange</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
<!-- 
</ClaimsProviders> -->
```

Durch das technische Profil `setting.forgotPasswordLinkOverride` vom Typ `SelfAsserted-LocalAccountSignin-Email` wird der Austausch von Kennwortzurücksetzungsansprüchen für Ihre User Journey definiert. 

### <a name="add-the-password-reset-sub-journey"></a>Hinzufügen der Sub Journey für die Kennwortzurücksetzung

Ihre Journey ermöglicht es dem Benutzer nun, sich anzumelden, sich zu registrieren und eine Kennwortzurücksetzung durchzuführen. Zur besseren Strukturierung der User Journey kann für die Behandlung des Kennwortzurücksetzungsflows eine [Sub Journey](subjourneys.md) verwendet werden.

Die Sub Journey wird von der User Journey aus aufgerufen und führt die spezifischen Schritte aus, um die Kennwortzurücksetzung für den Benutzer bereitzustellen. Verwenden Sie die Sub Journey vom Typ `Call`, damit nach Abschluss der Sub Journey die Steuerung an den Orchestrierungsschritt zurückgegeben wird, durch den die Sub Journey initiiert wurde.

Suchen Sie das Element `SubJourneys`. Ist das Element nicht vorhanden, fügen Sie es nach dem Element `User Journeys` hinzu. Fügen Sie anschließend die folgende Sub Journey hinzu:

```xml
<!--
<SubJourneys>-->
  <SubJourney Id="PasswordReset" Type="Call">
    <OrchestrationSteps>
      <!-- Validate user's email address. -->
      <OrchestrationStep Order="1" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="PasswordResetUsingEmailAddressExchange" TechnicalProfileReferenceId="LocalAccountDiscoveryUsingEmailAddress" />
        </ClaimsExchanges>
      </OrchestrationStep>

      <!-- Collect and persist a new password. -->
      <OrchestrationStep Order="2" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordUsingObjectId" />
        </ClaimsExchanges>
      </OrchestrationStep>
    </OrchestrationSteps>
  </SubJourney>
<!--
</SubJourneys>-->
```

### <a name="prepare-your-user-journey"></a>Vorbereiten Ihrer User Journey

Der Link **Kennwort vergessen?** muss mit der Sub Journey vom Typ „Kennwort vergessen“ verbunden werden. Verweisen Sie hierzu innerhalb des Elements **ClaimsProviderSelection** des Schritts **CombinedSignInAndSignUp** auf die ID der Sub Journey vom Typ „Kennwort vergessen“.

Sollten Sie über keine eigene benutzerdefinierte User Journey mit dem Schritt **CombinedSignInAndSignUp** verfügen, verwenden Sie das folgende Verfahren, um eine vorhandene User Journey für die Registrierung oder Anmeldung zu duplizieren. Fahren Sie andernfalls mit dem nächsten Abschnitt fort.

1. Öffnen Sie die Datei *TrustFrameworkBase.xml* aus dem Starter Pack.
2. Suchen und kopieren Sie den gesamten Inhalt des **UserJourney**-Elements, das `Id="SignUpOrSignIn"` enthält.
3. Öffnen Sie die Datei *TrustFrameworkExtensions.xml*, und suchen Sie nach dem **UserJourneys**-Element. Wenn das Element nicht vorhanden ist, fügen Sie ein solches hinzu.
4. Erstellen Sie ein untergeordnetes Element des Elements **UserJourneys**. Fügen Sie hierzu den gesamten Inhalt des Elements **UserJourney** ein, den Sie in Schritt 2 kopiert haben.
5. Benennen Sie die ID der User Journey um. Beispiel: `Id="CustomSignUpSignIn"`.

### <a name="connect-the-forgot-password-link-to-the-forgot-password-sub-journey"></a>Verbinden des Links „Kennwort vergessen?“ mit der Sub Journey vom Typ „Kennwort vergessen“ 

In Ihrer User Journey können Sie die Sub Journey vom Typ „Kennwort vergessen“ als **ClaimsProviderSelection** darstellen. Durch Hinzufügen dieses Elements wird der Link **Kennwort vergessen?** mit der Sub Journey vom Typ „Kennwort vergessen“ verbunden.

1. Suchen Sie in der User Journey nach dem Orchestrierungsschrittelement, das `Type="CombinedSignInAndSignUp"` oder `Type="ClaimsProviderSelection"` enthält. Dies ist in der Regel der erste Orchestrierungsschritt. Das Element **ClaimsProviderSelections** enthält eine Liste mit Identitätsanbietern, die ein Benutzer für die Anmeldung verwenden kann. Fügen Sie die folgende Zeile hinzu:
    
    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ForgotPasswordExchange" />
    ```

1. Fügen Sie im nächsten Orchestrierungsschritt ein **ClaimsExchange**-Element hinzu. Fügen Sie die folgende Zeile hinzu:

    ```xml
    <ClaimsExchange Id="ForgotPasswordExchange" TechnicalProfileReferenceId="ForgotPassword" />
    ```
    
1. Fügen Sie den folgenden Orchestrierungsschritt zwischen dem aktuellen Schritt und dem nächsten Schritt hinzu. In dem neu hinzugefügten Orchestrierungsschritt wird überprüft, ob der Anspruch `isForgotPassword` vorhanden ist. Ist der Anspruch vorhanden, wird die [Sub Journey für die Kennwortzurücksetzung](#add-the-password-reset-sub-journey) aufgerufen. 

    ```xml
    <OrchestrationStep Order="3" Type="InvokeSubJourney">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>isForgotPassword</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <JourneyList>
        <Candidate SubJourneyReferenceId="PasswordReset" />
      </JourneyList>
    </OrchestrationStep>
    ```
    
1. Nummerieren Sie nach dem Hinzufügen des neuen Orchestrierungsschritts die Schritte nacheinander von 1 bis N neu, ohne eine Ganzzahl zu überspringen.

### <a name="set-the-user-journey-to-be-executed"></a>Festlegen der auszuführenden User Journey

Nachdem Sie nun eine User Journey geändert oder erstellt haben, geben Sie als Nächstes im Abschnitt **Vertrauende Seite** die Journey an, die von Azure AD B2C für diese benutzerdefinierte Richtlinie ausgeführt werden soll. Suchen Sie im Element [RelyingParty](relyingparty.md) nach dem Element **DefaultUserJourney**. Aktualisieren Sie **DefaultUserJourney ReferenceId** mit der ID der User Journey, in der Sie **ClaimsProviderSelections** hinzugefügt haben.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="indicate-the-forgot-password-flow-to-your-app"></a>Angeben des Flows vom Typ „Kennwort vergessen“ für Ihre App

Ihre Anwendung muss möglicherweise erkennen können, ob sich der Benutzer über den Benutzerflow vom Typ „Kennwort vergessen“ angemeldet hat. Der Anspruch **isForgotPassword** enthält einen booleschen Wert, der dies angibt. Dieser Wert kann in dem an Ihre Anwendung gesendeten Token ausgegeben werden. Fügen Sie `isForgotPassword` bei Bedarf den Ausgabeansprüchen im Abschnitt **Vertrauende Seite** hinzu. Ihre Anwendung kann den Anspruch `isForgotPassword` überprüfen, um zu ermitteln, ob der Benutzer sein Kennwort zurücksetzt.

```xml
<RelyingParty>
  <OutputClaims>
    ...
    <OutputClaim ClaimTypeReferenceId="isForgotPassword" DefaultValue="false" />
  </OutputClaims>
</RelyingParty>
```


### <a name="upload-the-custom-policy"></a>Hochladen der benutzerdefinierten Richtlinie

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.
1. Suchen Sie im Azure-Portal nach **Azure AD B2C**, und wählen Sie diese Option dann aus.
1. Wählen Sie unter **Richtlinien** die Option **Identity Experience Framework** aus.
1. Wählen Sie **Benutzerdefinierte Richtlinie hochladen** aus, und laden Sie dann die beiden geänderten Richtliniendateien in der folgenden Reihenfolge hoch:
   1. Die Erweiterungsrichtlinie (beispielsweise `TrustFrameworkExtensions.xml`)
   2. Die Richtlinie für die vertrauende Seite (beispielsweise `SignUpSignIn.xml`)

::: zone-end

### <a name="test-the-password-reset-flow"></a>Testen des Kennwortzurücksetzungsflows

1. Wählen Sie einen (empfohlenen) Benutzerflow für die Registrierung oder Anmeldung aus, den Sie testen möchten.
1. Wählen Sie **Benutzerflow ausführen** aus.
1. Wählen Sie für **Anwendung** die Webanwendung *webapp1* aus, die Sie zuvor registriert haben. Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Wählen Sie **Benutzerflow ausführen** aus.
1. Wählen Sie auf der Seite für die Registrierung oder Anmeldung den Link **Kennwort vergessen?** aus.
1. Überprüfen Sie die E-Mail-Adresse des Kontos, das Sie zuvor erstellt haben, und wählen Sie **Weiter** aus.
1. Sie haben nun die Möglichkeit, das Kennwort des Benutzers zu ändern. Ändern Sie das Kennwort, und klicken Sie auf **Weiter**. Das Token wird an `https://jwt.ms` zurückgegeben und sollte Ihnen angezeigt werden.
1. Überprüfen Sie den Wert des Anspruchs `isForgotPassword` des Rückgabetokens. Wen er vorhanden ist und „true“ lautet, bedeutet das, dass der Benutzer das Kennwort zurückgesetzt hat.

## <a name="password-reset-policy-legacy"></a>Kennwortzurücksetzungsrichtlinie (Legacy)

Wenn die [Self-Service-Kennwortzurücksetzung](#self-service-password-reset-recommended) nicht aktiviert ist, wird durch Klicken auf diesen Link nicht automatisch ein Benutzerflow für die Kennwortzurücksetzung ausgelöst. Stattdessen wird der Fehlercode `AADB2C90118` an Ihre Anwendung zurückgegeben. Dieser Fehlercode muss von Ihrer Anwendung behandelt werden, indem die Authentifizierungsbibliothek neu initialisiert wird, um einen Azure AD B2C-Benutzerflow für die Kennwortzurücksetzung zu authentifizieren.

Das Diagramm weiter unten zeigt Folgendes:

1. Der Benutzer klickt in der Anwendung auf „Anmelden“. Von der App wird eine Autorisierungsanforderung initiiert, und der Benutzer wird zu Azure AD B2C weitergeleitet, um die Anmeldung abzuschließen. In der Autorisierungsanforderung wird der Name der Registrierungs- oder Anmelderichtlinie angegeben (beispielsweise **B2C_1_signup_signin**).
1. Der Benutzer wählt den Link **Kennwort vergessen?** aus. Von Azure AD B2C wird der Fehlercode AADB2C90118 an Ihre Anwendung zurückgegeben.
1. Der Fehlercode wird von der Anwendung behandelt, und eine neue Autorisierungsanforderung wird initiiert. In der Autorisierungsanforderung wird der Name der Kennwortzurücksetzungsrichtlinie angegeben (beispielsweise **B2C_1_pwd_reset**).

![Legacy-Benutzerflow für die Kennwortzurücksetzung](./media/add-password-reset-policy/password-reset-flow-legacy.png)

Die Verknüpfung von Benutzerflows wird in [diesem einfachen ASP.NET-Beispiel](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI) veranschaulicht.

::: zone pivot="b2c-user-flow"

### <a name="create-a-password-reset-user-flow"></a>Erstellen eines Benutzerflows zur Kennwortrücksetzung

Damit Benutzer Ihrer Anwendung ihr eigenes Kennwort zurücksetzten können, erstellen Sie einen Benutzerflow für die Kennwortzurücksetzung.

1. Wählen Sie im Übersichtsmenü des Azure AD B2C-Mandanten die Option **Benutzerflows** und dann **Neuer Benutzerflow** aus.
1. Wählen Sie auf der Seite **Benutzerflow erstellen** den Benutzerflow **Kennwortzurücksetzung** aus. 
1. Wählen Sie unter **Version auswählen** die Option **Empfohlen** und dann **Erstellen** aus.
1. Geben Sie unter **Name** einen Namen für den Benutzerflow ein. Beispiel *passwordreset1*.
1. Aktivieren Sie für **Identitätsanbieter** die Option **Reset password using username** (Kennwort mithilfe des Benutzernamens zurücksetzen) oder **Reset password using email address** (Kennwort mithilfe der E-Mail-Adresse zurücksetzen).
1. Wenn Sie unter **Mehrstufige Authentifizierung** festlegen möchten, dass Benutzer ihre Identität mit einer zweiten Authentifizierungsmethode überprüfen müssen, wählen Sie den Methodentyp und den Zeitpunkt aus, an dem die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) erzwungen werden soll. [Weitere Informationen](multi-factor-authentication.md)
1. Wenn Sie unter **Bedingter Zugriff** Richtlinien für den bedingten Zugriff für Ihren Azure AD B2C-Mandanten konfiguriert haben und diese für diesen Benutzerflow aktivieren möchten, aktivieren Sie das Kontrollkästchen **Enforce conditional access policies** (Richtlinien für bedingten Zugriff erzwingen). Sie müssen keinen Richtliniennamen angeben. [Weitere Informationen](conditional-access-user-flow.md?pivots=b2c-user-flow)
1. 1. Wählen Sie unter **Anwendungsansprüche** die Option **Mehr anzeigen**, und wählen Sie die Ansprüche aus, die in den an Ihre Anwendung gesendeten Autorisierungstoken zurückgegeben werden sollen. Wählen Sie beispielsweise **Objekt-ID des Benutzers**.
1. Klicken Sie auf **OK**.
1. Wählen Sie **Erstellen** aus, um den Benutzerflow hinzuzufügen. Dem Namen wird automatisch das Präfix *B2C_1* vorangestellt.

### <a name="test-the-user-flow"></a>Testen des Benutzerflows

1. Wählen Sie den von Ihnen erstellten Benutzerflow aus, um die entsprechende Übersichtsseite zu öffnen, und wählen Sie anschließend **Benutzerflow ausführen** aus.
1. Wählen Sie für **Anwendung** die Webanwendung *webapp1* aus, die Sie zuvor registriert haben. Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Klicken Sie auf **Benutzerflow ausführen**, überprüfen Sie die E-Mail-Adresse des Kontos, das Sie zuvor erstellt haben, und klicken Sie anschließend auf **Weiter**.
1. Nun können Sie das Kennwort für den Benutzer ändern. Ändern Sie das Kennwort, und klicken Sie auf **Weiter**. Das Token wird an `https://jwt.ms` zurückgegeben und sollte Ihnen angezeigt werden.

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="create-a-password-reset-policy"></a>Erstellen einer Richtlinie zur Kennwortrücksetzung

Benutzerdefinierte Richtlinien bestehen aus mehreren XML-Dateien, die Sie in den Azure AD B2C-Mandanten hochladen, um User Journeys zu definieren. Es sind Starter Packs mit mehreren integrierten Richtlinien verfügbar, z. B. für die Registrierung und Anmeldung, für die Kennwortzurücksetzung und für die Profilbearbeitung. Weitere Informationen finden Sie unter [Erste Schritte für benutzerdefinierte Richtlinien in Azure Active Directory B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy).

::: zone-end

## <a name="next-steps"></a>Nächste Schritte

Richten Sie eine [erzwungene Kennwortzurücksetzung](force-password-reset.md) ein.
