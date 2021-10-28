---
title: Einrichten eines Kennwortzurücksetzungsflows
titleSuffix: Azure AD B2C
description: Hier erfahren Sie, wie Sie einen Ablauf zum Zurücksetzen des Kennworts in Azure Active Directory B2C (Azure AD B2C) einrichten.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/24/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 2d1c0dfd5a5fdb9573859c61421513c4dc91eb8e
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130251203"
---
# <a name="set-up-a-password-reset-flow-in-azure-active-directory-b2c"></a>Einrichten eines Kennwortzurücksetzungsflows in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Beim [Registrierungs- und Anmeldevorgang](add-sign-up-and-sign-in-policy.md) können Benutzer ihr eigenes Kennwort über den Link **Kennwort vergessen?** zurücksetzen. Dieser Ablauf für Kennwortzurücksetzung in Selbstbedienung trifft zu für lokale Konten in Azure AD B2C (Azure AD B2C), bei denen für die Anmeldung eine [E-Mail-Adresse](sign-in-options.md#email-sign-in) oder ein [Benutzername](sign-in-options.md#username-sign-in) und ein Kennwort verwendet werden.

Der Kennwortzurücksetzungsflow umfasst folgende Schritte:

1. Auf der Registrierungs- und Anmeldeseite wählt der Nutzer den Link **Passwort vergessen?** . Daraufhin wird von Azure AD B2C der Kennwortzurücksetzungsflow initiiert.
1. Im nächsten Dialogfeld gibt der Benutzer seine E-Mail-Adresse ein und wählt dann **Prüfcode senden**. Azure AD B2C sendet den Prüfcode an das E-Mail-Konto des Benutzers. Der Benutzer kopiert den Prüfcode aus der E-Mail, gibt den Code auf der Seite für die Azure AD B2C-Kennwortzurücksetzung ein und wählt dann **Code überprüfen**.
1. Anschließend kann der Benutzer ein neues Kennwort eingeben. (Nach der Überprüfung der E-Mail-Adresse kann der Benutzer weiterhin auf die Schaltfläche **E-Mail-Adresse ändern** klicken, siehe [Ausblenden der Schaltfläche zum Ändern der E-Mail-Adresse](#hide-the-change-email-button).)

   ![Diagramm, das drei Dialogfelder im Ablauf der Kennwortzurücksetzung zeigt.](./media/add-password-reset-policy/password-reset-flow.png)

> [!TIP]
> Ein Benutzer kann sein Kennwort mithilfe des Ablaufs zur Kennwortzurücksetzung ändern, wenn er sein Kennwort vergessen hat und es zurücksetzen möchte. Dazu wählt man eine der folgenden Ablaufoptionen aus:
> - Wenn ein Benutzer sein Kennwort kennt und dieses ändern möchte, benutzen Sie einen [Kennwortänderungsflow](add-password-change-policy.md).
> - Wenn Sie erzwingen möchten, dass Benutzer sein Kennwort zurücksetzt (z. B. bei der erstmaligen Anmeldung, nach der Zurücksetzung des Kennworts durch einen Administrator oder nach der Migration zu Azure AD B2C mit einem zufälligen Kennwort), verwenden Sie einen Flow zum[Erzwingen der Kennwortzurücksetzung](force-password-reset.md).

### <a name="hide-the-change-email-button"></a>Ausblenden der Schaltfläche zum Ändern der E-Mail-Adresse

Nachdem die E-Mail-Adresse überprüft wurde, kann der Benutzer weiterhin die Option **E-Mail-Adresse ändern** auswählen, eine andere E-Mail-Adresse eingeben und die Überprüfung der E-Mail-Adresse von Anfang an wiederholen. Wenn Sie die Schaltfläche **E-Mail-Adresse ändern** ausblenden möchten, können Sie die CSS ändern, um die zugehörigen HTML-Elemente auf der Seite auszublenden. Sie können beispielsweise den folgenden CSS-Eintrag zu „selfAsserted.HTML“ hinzufügen und [die Benutzeroberfläche mit HTML-Vorlagen anpassen](customize-ui-with-html.md):

```html
<style type="text/css">
   .changeClaims
   {
     visibility: hidden;
   }
</style>
```

Der Standardname der Schaltfläche **E-Mail-Adresse ändern** in *selfAsserted.html* ist **changeclaims**. Um den Namen der Schaltfläche auf der Anmeldeseite zu finden, prüfen Sie die Seitenquelle mithilfe eines Browsertools wie Inspect.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="self-service-password-reset-recommended"></a>Self-Service-Kennwortzurücksetzung (Self-Service Password Reset, SSPR) (empfohlen)

Die neue Kennwortzurücksetzung ist nun Teil der Registrierungs- oder Anmelderichtlinie. Wenn der Benutzer den Link **Kennwort vergessen?** auswählt, gelangt er sofort zur Kennwortzurücksetzung. Der [Fehlercode AADB2C90118](#password-reset-policy-legacy) muss von Ihrer Anwendung nicht mehr behandelt werden, und Sie benötigen keine separate Richtlinie für die Kennwortzurücksetzung.

::: zone pivot="b2c-user-flow"

Die Kennwortzurücksetzung in Selbstbedienung kann für die Abläufe Anmeldung (empfohlen) oder Registrierung und Anmeldung (empfohlen) konfiguriert werden. Wenn Sie keinen dieser Benutzerabläufe eingerichtet haben, erstellen Sie einen Benutzerablauf für die [Registrierung oder die Anmeldung](add-sign-up-and-sign-in-policy.md).

So aktivieren Sie die Kennwortzurücksetzung in Selbstbedienung für den Ablauf Registrierung oder Anmeldung:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie auf der Toolbar des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen | Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Suchen Sie im Azure-Portal nach **Azure AD B2C**, und wählen Sie diese Option dann aus.
1. Wählen Sie **Benutzerflows** aus.
1. Wählen Sie einen Benutzerflow für die Registrierung oder Anmeldung (vom Typ **empfohlen**) aus, den Sie anpassen möchten.
1. Wählen Sie im linken Menü unter **Einstellungen** **Eigenschaften** aus.
1. Wählen Sie unter der Registerkarte **Kennwortkonfiguration** die Option **Self-Service-Kennwortzurücksetzung** aus.
1. Wählen Sie **Speichern** aus.
1. Wählen Sie im linken Menü unter **Anpassen** **Seitenlayouts** aus.
1. Unter **Seitenlayoutversion** wählen Sie **2.1.3** oder höher aus.
1. Wählen Sie **Speichern** aus.

::: zone-end

::: zone pivot="b2c-custom-policy"

In den folgenden Abschnitten erfahren Sie, wie Sie einer benutzerdefinierten Richtlinie eine Self-Service-Kennwortfunktion hinzufügen. Das Beispiel basiert auf den Richtliniendateien, die im [Starter-Paket für benutzerdefinierte Richtlinien](./tutorial-create-user-flows.md?pivots=b2c-custom-policy#custom-policy-starter-pack) enthalten sind.

> [!TIP]
> Ein vollständiges Beispiel für die Richtlinie zur Registrierung oder Anmeldung mit Kennwortzurücksetzung finden Sie auf [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/embedded-password-reset).

### <a name="indicate-that-a-user-selected-the-forgot-your-password-link"></a>Geben Sie an, dass ein Benutzer den Link „Kennwort vergessen?“ ausgewählt hat

Um der Richtlinie gegenüber anzugeben, dass ein Nutzer den Link **Passwort vergessen?** gewählt hat, definieren Sie einen Booleschen Anspruch. Verwenden Sie diesen Anspruch, um die User Journey zum technischen Profil **Passwort vergessen** weiterzuleiten. Der Anspruch kann auch an das Token ausgegeben werden. So erkennt die Anwendung, dass sich der Benutzer über den Flow „Kennwort vergessen“ angemeldet hat.

Deklarieren Sie Ihre Ansprüche im [Anspruchsschema](claimsschema.md). Öffnen Sie die Erweiterungsdatei Ihrer Richtlinie, z.B. in *SocialAndLocalAccounts/TrustFrameworkExtensions.xml*.

1. Suchen Sie nach dem Element [BuildingBlocks](buildingblocks.md). Wenn das Element nicht vorhanden ist, fügen Sie es hinzu.
1. Suchen Sie nach dem Element [ClaimsSchema](claimsschema.md). Wenn das Element nicht vorhanden ist, fügen Sie es hinzu.
1. Fügen Sie dem Element **ClaimsSchema** den folgenden Anspruch hinzu:

    ```xml
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

Sie benötigen[Seitenlayout Version](contentdefinitions.md#migrating-to-page-layout) 2.1.2, um den Ablauf zur Kennwortzurücksetzung für Registrierung oder Anmeldung zu aktivieren. Upgraden der Seitenlayoutversion:

1. Suchen Sie nach dem Element [BuildingBlocks](buildingblocks.md). Wenn das Element nicht vorhanden ist, fügen Sie es hinzu.
1. Suchen Sie nach dem Element [ContentDefinitions](contentdefinitions.md). Wenn das Element nicht vorhanden ist, fügen Sie es hinzu.
1. Ändern Sie das **DataURI** Element innerhalb des **ContentDefinition** Elements, um die ID zu erhalten`api.signuporsignin`:

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

Ein technisches Profil zur Anspruchstransformation startet den **isForgotPassword** Anspruch. Auf das technische Profil wird später verwiesen. Beim Aufruf wird der Wert des **IsForgotPassword** Anspruch auf`true` gelegt. Suchen Sie nach dem Element **ClaimsProviders**. Wenn das Element nicht vorhanden ist, fügen Sie es hinzu. Fügen Sie anschließend den folgenden Anspruchsanbieter hinzu:  

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
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
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

Das technische Profil in der **SelfAsserted-LocalAccountSignin-Email** **setting.forgotPasswordLinkOverride** definiert den Austausch von Ansprüchen innerhalb der Kennwortzurücksetzung, der in Ihrer User Journey stattfindet.

### <a name="add-the-password-reset-sub-journey"></a>Hinzufügen der Sub Journey für die Kennwortzurücksetzung

Der Nutzer kann sich jetzt anmelden, sich registrieren und die Kennwortzurücksetzung in Ihrer User Journey durchführen. Zur besseren Strukturierung der User Journey kann für die Behandlung des Ablaufs zur Kennwortzurücksetzung eine [Sub Journey](subjourneys.md) verwendet werden.

Die Sub Journey wird von der User Journey aus aufgerufen und führt die speziellen Schritte aus, damit der Benutzer sein Passwort zurücksetzen kann. Verwenden Sie die Sub Journey vom Typ `Call`, damit nach Abschluss der Sub Journey die Steuerung an den Orchestrierungsschritt zurückgegeben wird, durch den die Sub Journey begonnen wurde.

Suchen Sie das Element **SubJourneys**. Wenn es das Element nicht gibt, fügen Sie es nach dem Element **User Journeys** hinzu. Fügen Sie anschließend die folgende Sub Journey hinzu:

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

Als Nächstes verbinden Sie den Link **Kennwort vergessen?** muss mit der Sub Journey „Kennwort vergessen“. Verweisen Sie auf die Sub Journey ID „Passwort vergessen“ im **ClaimsProviderSelection** Element des Schritts **CombinedSignInAndSignUp**.

Sollten Sie über keine eigene benutzerdefinierte User Journey mit dem Schritt **CombinedSignInAndSignUp** verfügen,führen Sie die folgenden Schritte aus, um eine vorhandene User Journey für die Registrierung oder Anmeldung zu duplizieren. Fahren Sie andernfalls mit dem nächsten Abschnitt fort.

1. Öffnen Sie im Starter-Paket die Datei *TrustFrameworkBase.xml*.
1. Suchen und kopieren Sie den gesamten Inhalt des **UserJourney**-Elements, das `Id="SignUpOrSignIn"` enthält.
1. Öffnen Sie *TrustFrameworkExtensions.xml* und suchen Sie nach dem **UserJourneys**-Element. Wenn das Element nicht vorhanden ist, fügen Sie ein solches hinzu.
1. Erstellen Sie ein untergeordnetes Element des Elements **UserJourneys**. Fügen Sie hierzu den gesamten Inhalt des Elements **UserJourney** ein, den Sie in Schritt 2 kopiert haben.
1. Benennen Sie die ID der User Journey um. Beispiel: `Id="CustomSignUpSignIn"`.

### <a name="connect-the-forgot-password-link-to-the-forgot-password-sub-journey"></a>Verbinden Sie den Link „Kennwort vergessen?“ mit der Sub Journey „Kennwort vergessen“

In Ihrer User Journey können Sie die Sub Journey vom Typ „Kennwort vergessen“ als **ClaimsProviderSelection** darstellen. Durch Hinzufügen dieses Elements wird der Link **Kennwort vergessen?** mit der Sub Journey vom Typ „Kennwort vergessen“ verbunden.

1. Suchen Sie in der User Journey nach dem Orchestrierungsschrittelement, das `Type="CombinedSignInAndSignUp"` oder `Type="ClaimsProviderSelection"` enthält. Dies ist in der Regel der erste Orchestrierungsschritt. Das Element **ClaimsProviderSelections** enthält eine Liste mit Identitätsanbietern, die ein Benutzer für die Anmeldung verwenden kann. Fügen Sie die folgende Zeile hinzu:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ForgotPasswordExchange" />
    ```

1. Fügen Sie im nächsten Orchestrierungsschritt ein **ClaimsExchange**-Element hinzu. Fügen Sie die folgende Zeile hinzu:

    ```xml
    <ClaimsExchange Id="ForgotPasswordExchange" TechnicalProfileReferenceId="ForgotPassword" />
    ```

1. Fügen Sie den folgenden Orchestrierungsschritt zwischen dem aktuellen Schritt und dem nächsten Schritt hinzu. In dem neuen Orchestrierungsschritt fügen Sie eine Prüfung hinzu, ob der Anspruch **isForgotPassword** vorhanden ist. Ist der Anspruch vorhanden, wird die [Sub Journey für die Kennwortzurücksetzung](#add-the-password-reset-sub-journey) aufgerufen. 

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

Nachdem Sie nun eine User Journey geändert oder erstellt haben, geben Sie als Nächstes im Abschnitt **RelyingParty** die Journey an, die von Azure AD B2C für diese benutzerdefinierte Richtlinie ausgeführt werden soll. Suchen Sie im Element [RelyingParty](relyingparty.md) nach dem Element **DefaultUserJourney**. Aktualisieren Sie **DefaultUserJourney ReferenceId**, um sie an die ID der User Journey, in der Sie **ClaimsProviderSelections** hinzugefügt haben, anzugleichen.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="indicate-the-forgot-password-flow-to-your-app"></a>Angeben des Flows vom Typ „Kennwort vergessen“ für Ihre App

Ihre Anwendung muss möglicherweise erkennen können, ob sich der Benutzer über den Benutzerflow „Kennwort vergessen“ angemeldet hat. Der **IsForgotPassword-Anspruch** enthält einen booleschen Wert, der angibt, der Benutzer das getan hat. Der Anspruch kann in dem Token ausgegeben werden, der zu Ihrer Anwendung gesendet wird. Falls nötig, fügen Sie **isForgotPassword** zu den Ausgabeansprüchen im Abschnitt **Relying Party** hinzu. Ihre Anwendung kann den Anspruch **isForgotPassword** überprüfen, um zu ermitteln, ob der Benutzer sein Kennwort zurücksetzt.

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
1. Wählen Sie auf der Toolbar des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen | Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Suchen Sie im Azure-Portal nach **Azure AD B2C**, und wählen Sie diese Option dann aus.
1. Wählen Sie im Menü unter **Richtlinien** **Identity Experience Framework** aus.
1. Wählen Sie **Benutzerdefinierte Richtlinie hochladen** aus. Laden Sie in der folgenden Reihenfolge die beiden Richtliniendateien hoch, die Sie geändert haben:
   1. Die Erweiterungsrichtlinie, beispielsweise *TrustFrameworkExtensions.xml*.
   1. Die Richtlinie für die RelyingParty, beispielsweise,*SignUpSignIn.xml*.

::: zone-end

### <a name="test-the-password-reset-flow"></a>Testen des Kennwortzurücksetzungsflows

1. Wählen Sie einen Benutzerflow (vom empfohlenen Typ) für Registrierung und Anmeldung aus, den Sie testen möchten.
1. Wählen Sie **Benutzerflow ausführen** aus.
1. Als **Anwendung** wählen Sie die Webanwendung *webapp1* aus, die Sie zuvor registriert haben. Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Wählen Sie **Benutzerflow ausführen** aus.
1. Wählen Sie auf der Seite für die Registrierung oder Anmeldung **Kennwort vergessen?** aus.
1. Überprüfen Sie die E-Mail-Adresse des Kontos, das Sie zuvor erstellt haben, und wählen Sie **Weiter**.
1. Im Dialogfeld, das jetzt erscheint, ändern Sie das Passwort für den Benutzer und wählen dann **Weiter**. Das Token wird an `https://jwt.ms` zurückgegeben und der Browser zeigt es an.
1. Überprüfen Sie den Anspruchswert **isForgotPassword** des zurückgegebenen Tokens. Wen er vorhanden und auf `true` gesetzt ist, bedeutet das, dass der Benutzer das Kennwort zurückgesetzt hat.

## <a name="password-reset-policy-legacy"></a>Kennwortzurücksetzungsrichtlinie (Legacy)

Wenn die [Kennwortzurücksetzung in Selbstbedienung](#self-service-password-reset-recommended) nicht aktiviert ist, löst das Klicken auf diesen Link nicht automatisch einen Ablauf für die Kennwortzurücksetzung aus. Stattdessen wird der Fehlercode `AADB2C90118` an Ihre Anwendung zurückgegeben. Ihre Anwendung muss diesen Fehlercode behandeln, indem sie die Authentifizierungsbibliothek neu initialisiert, um einen Azure AD B2C-Benutzerflow für die Kennwortzurücksetzung zu bestätigen.

Im folgenden Diagramm ist dieser Ablauf dargestellt:

1. In der Anwendung wählt der Benutzer **Anmelden** aus. Von der App wird eine Autorisierungsanforderung gestartet, und der Benutzer wird zu Azure AD B2C weitergeleitet, um die Anmeldung abzuschließen. Die Autorisierungsanforderung gibt einen Namen einer Registrierungs- oder Anmelderichtlinie an, beispielsweise *B2C_1_signup_signin*.
1. Der Benutzer wählt den Link **Kennwort vergessen?** aus. Azure AD B2C gibt den Fehlercode `AADB2C90118` an die Anwendung zurück.
1. Der Fehlercode wird von der Anwendung behandelt, und eine neue Autorisierungsanforderung wird initiiert. In der Autorisierungsanforderung wird der Name der Kennwortzurücksetzungsrichtlinie angegeben (beispielsweise *B2C_1_pwd_reset*).

    ![Diagramm, das den Benutzerflow für die Legacy-Kennwortrücksetzung zeigt.](./media/add-password-reset-policy/password-reset-flow-legacy.png)

Sie sehen ein einfaches [ASP.NET Beispiel](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI), das veranschaulicht, wie Benutzerflows verknüpft werden.

::: zone pivot="b2c-user-flow"

### <a name="create-a-password-reset-user-flow"></a>Erstellen eines Benutzerflows zur Kennwortrücksetzung

Damit Ihre Benutzer der Anwendung ihr eigenes Kennwort zurücksetzen können, erstellen Sie einen Benutzerflow für die Kennwortzurücksetzung:

1. Im Azure-Portal gehen Sie zur Übersicht für Ihren Azure AD B2C-Mandanten.
1. Wählen Sie im Menü links unter **Richtlinien** die Option **Benutzerflows** aus und wählen Sie dann **Neuer Benutzerflow**.
1. Wählen Sie bei **Benutzerflow erstellen** den Benutzerflow **Kennwortzurücksetzung** aus.
1. Wählen Sie unter **Version auswählen** die Option **Empfohlen** und dann **Erstellen** aus.
1. Bei **Name** geben Sie einen Namen für den Benutzerflow ein. Beispiel *passwordreset1*.
1. Aktivieren Sie für **Identitätsanbieter** die Option **Reset password using username** (Kennwort mithilfe des Benutzernamens zurücksetzen) oder **Reset password using email address** (Kennwort mithilfe der E-Mail-Adresse zurücksetzen).
1. Wenn Sie unter **Mehrstufige Authentifizierung** festlegen möchten, dass Benutzer ihre Identität mit einer zweiten Authentifizierungsmethode bestätigen müssen, wählen Sie den Methodentyp und den Zeitpunkt aus, an dem die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) erzwungen werden soll. [Weitere Informationen](multi-factor-authentication.md)
1. Wenn Sie unter **Bedingter Zugriff** Richtlinien für bedingten Zugriff für Ihren Azure AD B2C-Mandanten konfiguriert haben und Sie diese für diesen Benutzerflow aktivieren möchten, aktivieren Sie das Kontrollkästchen **Richtlinien für bedingten Zugriff erzwingen**. Sie müssen keinen Richtliniennamen angeben. [Weitere Informationen](conditional-access-user-flow.md?pivots=b2c-user-flow)
1. Klicken Sie unter **Anwendungsansprüche** auf **Mehr anzeigen**. Wählen Sie die Ansprüche aus, die in den an Ihre Anwendung gesendeten Autorisierungstoken zurückgegeben werden sollen. Wählen Sie beispielsweise **Objekt-ID des Benutzers**.
1. Klicken Sie auf **OK**.
1. Wählen Sie **Erstellen** aus, um den Benutzerflow hinzuzufügen. Dem Namen wird automatisch das Präfix *B2C_1* vorangestellt.

### <a name="test-the-user-flow"></a>Testen des Benutzerflows

Testen des Benutzerflows:

1. Wählen Sie den Benutzerflow aus, den Sie erstellt haben. Wählen Sie auf der Übersichtsseite des Benutzerflows die Option **Benutzerflow ausführen** aus.
1. Als **Anwendung** wählen Sie die Webanwendung *webapp1* aus, die Sie zuvor registriert haben. Die **Antwort-URL** sollte `https://jwt.ms` sein.
1. Wählen Sie **Benutzerflow ausführen**, prüfen Sie die E-Mail-Adresse des Kontos, das Sie vorher erstellt hatten, und wählen Sie dann **Weiter**.
1. Ändern Sie das Kennwort und klicken Sie auf **Weiter**. Das Token wird an `https://jwt.ms` zurückgegeben und der Browser zeigt es an.

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="create-a-password-reset-policy"></a>Erstellen einer Richtlinie zur Kennwortrücksetzung

Benutzerdefinierte Richtlinien sind ein Satz von XML-Dateien, die Sie in den Azure AD B2C-Mandanten hochladen, um User Journeys zu definieren. Wir bieten Starter Packs mit mehreren integrierten Richtlinien, z. B. für die Registrierung und Anmeldung, für die Kennwortzurücksetzung und für die Profilbearbeitung. Weitere Informationen finden Sie unter [Erste Schritte für benutzerdefinierte Richtlinien in Azure Active Directory B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy).

::: zone-end

## <a name="troubleshoot-azure-ad-b2c-user-flows-and-custom-policies"></a>Beheben von Problemen mit Benutzerflows und benutzerdefinierten Richtlinien in Azure AD B2C
Ihre Anwendung muss bestimmte Fehler behandeln, die vom Azure B2C-Dienst verursacht werden. Weitere Informationen finden Sie unter [Beheben von Problemen mit Benutzerflows und benutzerdefinierten Richtlinien in Azure AD B2C](troubleshoot.md).

## <a name="next-steps"></a>Nächste Schritte

Richten Sie eine [erzwungene Kennwortzurücksetzung](force-password-reset.md) ein.
