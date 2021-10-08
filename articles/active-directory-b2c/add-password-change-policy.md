---
title: Einrichten der Kennwortänderung mithilfe von benutzerdefinierten Richtlinien
titleSuffix: Azure AD B2C
description: Erfahren Sie, wie Sie eine benutzerdefinierte Richtlinie einrichten, damit Benutzer ihr Kennwort in Azure Active Directory B2C ändern können.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/24/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 16295eb707968a606c74813f9f6b7585aaf59546
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128570619"
---
# <a name="set-up-password-change-by-using-custom-policies-in-azure-active-directory-b2c"></a>Einrichten der Kennwortänderung mithilfe benutzerdefinierter Richtlinien in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Sie können Azure Active Directory B2C (Azure AD B2C) so konfigurieren, dass ein Benutzer, der mit einem lokalen Konto angemeldet ist, sein Kennwort ändern kann, ohne die E-Mail-Überprüfung zu verwenden, um seine Identität nachzuweisen. 

Der Ablauf zur Kennwortänderung umfasst die folgenden Schritte:

1. Der Benutzer meldet sich bei seinem lokalen Konto an. Wenn die Sitzung noch aktiv ist, autorisiert Azure AD B2C den Benutzer und fährt mit dem nächsten Schritt fort.
1. Unter **Altes Kennwort** überprüft der Benutzer sein altes Kennwort. Unter **Neues Kennwort** wird das neue Kennwort erstellt und bestätigt.

   ![Screenshot, der zwei nummerierte Dialoge für ein Ändern des Kennworts zeigt.](./media/add-password-change-policy/password-change-flow.png)  

> [!TIP]
> Ein Benutzer kann den in diesem Artikel beschriebenen Kennwortänderungsablauf nur verwenden, wenn er sein Kennwort kennt und dieses ändern möchte. Sie sollten auch das [Rücksetzen des Kennworts in Selbstbedienung](add-password-reset-policy.md) aktivieren für Fälle, in denen der Benutzer sein Kennwort vergessen hat.

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="prerequisites"></a>Voraussetzungen

* Führen Sie die unter [Erste Schritte für benutzerdefinierte Richtlinien in Azure Active Directory B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy) beschriebenen Schritte aus.
* [Registrieren einer Webanwendung in Azure Active Directory B2C](tutorial-register-applications.md)

## <a name="add-the-elements"></a>Hinzufügen der Elemente

1. Öffnen Sie Ihre Datei *TrustFrameworkExtensions.xml*. Fügen Sie das folgende **ClaimType** Element zum [ClaimsSchema](claimsschema.md) hinzu, mit dem Bezeichner von `oldPassword`:

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="oldPassword">
          <DisplayName>Old Password</DisplayName>
          <DataType>string</DataType>
          <UserHelpText>Enter your old password</UserHelpText>
          <UserInputType>Password</UserInputType>
        </ClaimType>
      </ClaimsSchema>
    </BuildingBlocks>
    ```

1. Ein [ClaimsProvider](claimsproviders.md)-Element enthält das technische Profil, das den Benutzer authentifiziert. Fügen Sie dem **ClaimsProviders**-Element die folgenden Anspruchsanbieter hinzu:

    ```xml
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Local Account SignIn</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="login-NonInteractive-PasswordChange">
            <DisplayName>Local Account SignIn</DisplayName>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="oldPassword" PartnerClaimType="password" Required="true" />
              </InputClaims>
            <IncludeTechnicalProfile ReferenceId="login-NonInteractive" />
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
      <ClaimsProvider>
        <DisplayName>Local Account Password Change</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="LocalAccountWritePasswordChangeUsingObjectId">
            <DisplayName>Change password (username)</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
              <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item>
            </Metadata>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="objectId" />
            </InputClaims>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="oldPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
            </OutputClaims>
            <ValidationTechnicalProfiles>
              <ValidationTechnicalProfile ReferenceId="login-NonInteractive-PasswordChange" />
              <ValidationTechnicalProfile ReferenceId="AAD-UserWritePasswordUsingObjectId" />
            </ValidationTechnicalProfiles>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

1. Das [UserJourney](userjourneys.md)-Element definiert den Pfad, den der Benutzer beim Interagieren mit Ihrer Anwendung durchläuft. Fügen Sie das **UserJourneys**-Element hinzu (sofern es noch nicht vorhanden ist), mit der **UserJourney**-Identifizierung von`PasswordChange`:

    ```xml
    <UserJourneys>
      <UserJourney Id="PasswordChange">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.signuporsignin">
            <ClaimsProviderSelections>
              <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
          </OrchestrationStep>
          <OrchestrationStep Order="2" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="3" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordChangeUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="4" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="5" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        </OrchestrationSteps>
        <ClientDefinition ReferenceId="DefaultWeb" />
      </UserJourney>
    </UserJourneys>
    ```

1. Speichern Sie die Richtliniendatei *TrustFrameworkExtensions.xml*.
1. Kopieren Sie die Datei *ProfileEdit.xml*, die Sie mit dem Starter Pack heruntergeladen haben, und weisen Sie ihr den Namen *ProfileEditPasswordChange.xml* zu.
1. Öffnen Sie die neue Datei, und aktualisieren Sie das Attribut **PolicyId** mit einem eindeutigen Wert. Dieser Wert ist der Name Ihrer Richtlinie. Beispiel: *B2C_1A_profile_edit_password_change*.
1. Ändern Sie das Attribut **ReferenceId** in der **DefaultUserJourney**, um die ID der neuen Nutzer-Journey, die Sie geschaffen haben, anzugleichen. Beispiel: *PasswordChange*.
1. Speichern Sie die Änderungen.

## <a name="upload-and-test-the-policy"></a>Hochladen und Testen der Richtlinie

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis mit Ihrem Azure AD B2C-Mandanten verwenden, indem Sie in der Portalsymbolleiste das Symbol **Verzeichnisse + Abonnements** auswählen.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse + Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie **Framework für die Identitätsfunktion** aus.
1. Wählen Sie unter **Benutzerdefinierte Richtlinien** die **Richtlinie zum Hochladen** aus.
1. Aktivieren Sie **Richtlinie überschreiben, sofern vorhanden**, suchen Sie nach der Datei *TrustFrameworkExtensions.xml*, und wählen Sie die Datei aus.
1. Wählen Sie **Hochladen** aus.
1. Wiederholen Sie die Schritte 5 bis 7 für die Datei der vertrauenden Seite, z. B. *ProfileEditPasswordChange.xml*.

## <a name="run-the-policy"></a>Ausführen der Richtlinie

1. Öffnen Sie die Richtlinie, die Sie geändert haben. Beispiel: *B2C_1A_profile_edit_password_change*.
1. Wählen Sie als **Anwendung** die Anwendung aus, die Sie vorher registriert haben. Um das Token anzuzeigen, muss als **Antwort-URL** der Wert `https://jwt.ms` angegeben werden.
1. Wählen Sie **Jetzt ausführen** aus. Auf der neuen Registerkarte, die sich öffnet, entfernen Sie „&prompt=login“ aus der URL und aktualisieren Sie die Registerkarte. Melden Sie sich dann mit dem Konto an, das Sie zuvor erstellt haben. In einem Dialogfeld zur Kennwortänderung haben Sie die Möglichkeit, das Kennwort zu ändern.

## <a name="next-steps"></a>Nächste Schritte

* Die [Beispielrichtlinie finden Sie auf GitHub](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change).
* Erfahren Sie, wie Sie die [Kennwortkomplexität in Azure AD B2C konfigurieren](password-complexity.md).
* Richten Sie einen [Kennwortzurücksetzungsflow](add-password-reset-policy.md) ein.

::: zone-end
