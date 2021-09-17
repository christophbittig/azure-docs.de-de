---
title: Konfigurieren der Kennwortänderung mithilfe von benutzerdefinierten Richtlinien
titleSuffix: Azure AD B2C
description: Erfahren Sie, wie Sie Benutzern das Ändern ihres Kennworts mithilfe benutzerdefinierter Richtlinien in Azure Active Directory B2C ermöglichen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/03/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a0f5376574dd0329641ebc68b906bc5342437be8
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122777702"
---
# <a name="configure-password-change-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurieren der Kennwortänderung mithilfe benutzerdefinierter Richtlinien in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

In Azure Active Directory B2C (Azure AD B2C) können Sie mit einem lokalen Konto angemeldeten Benutzern ermöglichen, ihr Kennwort zu ändern, ohne ihre Identität durch E-Mail-Überprüfung nachweisen zu müssen. Der Kennwortänderungsflow umfasst die folgenden Schritte:

1. Der Benutzer meldet sich bei seinem lokalen Konto an. Wenn die Sitzung noch aktiv ist, autorisiert Azure AD B2C den Benutzer und fährt mit dem nächsten Schritt fort.
1. Der Benutzer überprüft das **Alte Kennwort** und erstellt und bestätigt dann das **Neue Kennwort**.

![Kennwortänderungsflow](./media/add-password-change-policy/password-change-flow.png)  

> [!TIP]
> Der Ablauf der Kennwortänderung ermöglicht Benutzern nur dann, ihr Kennwort zu ändern, wenn sie ihr Kennwort kennen und es ändern möchten. Sie sollten auch die [Self-Service-Kennwortzurücksetzung](add-password-reset-policy.md) aktivieren für Fälle, in denen der Benutzer sein Kennwort vergessen hat.

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="prerequisites"></a>Voraussetzungen

* Führen Sie die unter [Erste Schritte für benutzerdefinierte Richtlinien in Azure Active Directory B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy) beschriebenen Schritte aus.
* Wenn dies noch nicht erfolgt ist, [registrieren Sie eine Webanwendung in Azure Active Directory B2C](tutorial-register-applications.md).

## <a name="add-the-elements"></a>Hinzufügen der Elemente

1. Öffnen Sie Ihre Datei *TrustframeworkExtensions.xml*, und fügen Sie das folgende **ClaimType**-Element mit dem Bezeichner `oldPassword` zum [ClaimsSchema](claimsschema.md)-Element hinzu:

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

2. Ein [ClaimsProvider](claimsproviders.md)-Element enthält das technische Profil, das den Benutzer authentifiziert. Fügen Sie dem **ClaimsProviders**-Element die folgenden Anspruchsanbieter hinzu:

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

3. Das [UserJourney](userjourneys.md)-Element definiert den Pfad, den der Benutzer beim Interagieren mit Ihrer Anwendung durchläuft. Fügen Sie das **UserJourneys**-Element hinzu (sofern es noch nicht vorhanden ist), und identifizieren Sie dabei die **UserJourney** als `PasswordChange`:

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

4. Speichern Sie die Richtliniendatei *TrustFrameworkExtensions.xml*.
5. Kopieren Sie die Datei *ProfileEdit.xml*, die Sie mit dem Starter Pack heruntergeladen haben, und weisen Sie ihr den Namen *ProfileEditPasswordChange.xml* zu.
6. Öffnen Sie die neue Datei, und aktualisieren Sie das Attribut **PolicyId** mit einem eindeutigen Wert. Dieser Wert ist der Name Ihrer Richtlinie. Beispiel: *B2C_1A_profile_edit_password_change*.
7. Ändern Sie das Attribut **ReferenceId** in `<DefaultUserJourney>`, sodass es der ID der neuen von Ihnen erstellten User Journey entspricht. Beispiel: *PasswordChange*.
8. Speichern Sie die Änderungen.

## <a name="upload-and-test-the-policy"></a>Hochladen und Testen der Richtlinie

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis mit Ihrem Azure AD B2C-Mandanten verwenden, indem Sie in der Portalsymbolleiste das Symbol **Verzeichnisse + Abonnements** auswählen.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse + Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie **Framework für die Identitätsfunktion** aus.
1. Klicken Sie auf der Seite „Benutzerdefinierte Richtlinien“ auf **Richtlinie hochladen**.
1. Aktivieren Sie **Richtlinie überschreiben, sofern vorhanden**, suchen Sie nach der Datei *TrustframeworkExtensions.xml*, und wählen Sie die Datei aus.
1. Klicken Sie auf **Hochladen**.
1. Wiederholen Sie die Schritte 5 bis 7 für die Datei der vertrauenden Seite, z. B. *ProfileEditPasswordChange.xml*.

### <a name="run-the-policy"></a>Ausführen der Richtlinie

1. Öffnen Sie die Richtlinie, die Sie geändert haben. Beispiel: *B2C_1A_profile_edit_password_change*.
2. Wählen Sie für **Anwendung** Ihre Anwendung aus, die Sie zuvor registriert haben. Um das Token anzuzeigen, muss als **Antwort-URL** der Wert `https://jwt.ms` angegeben werden.
3. Klicken Sie auf **Jetzt ausführen**. Entfernen Sie auf der neu geöffneten Registerkarte „&amp;prompt=login“ aus der URL, und aktualisieren Sie die Registerkarte. Melden Sie sich dann mit dem Konto an, das Sie zuvor erstellt haben. Jetzt haben Sie die Möglichkeit, das Kennwort zu ändern.

## <a name="next-steps"></a>Nächste Schritte

- Die Beispielrichtlinie finden Sie auf [GitHub](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change).
- Erfahren Sie, wie Sie die [Kennwortkomplexität in Azure AD B2C konfigurieren](password-complexity.md).
- Richten Sie einen [Kennwortzurücksetzungsflow](add-password-reset-policy.md) ein.

::: zone-end
