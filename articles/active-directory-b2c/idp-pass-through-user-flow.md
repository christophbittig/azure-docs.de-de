---
title: Übergeben des Zugriffstokens eines Identitätsanbieters an Ihre App
titleSuffix: Azure AD B2C
description: Erfahren Sie, wie Sie ein Zugriffstoken für OAuth 2.0-Identitätsanbieter als Anspruch in einem Benutzerflow in Azure Active Directory B2C übergeben können.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/16/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: ae5f9409811e182bcc64dd9a6ea3ec12d46cfefd
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128574009"
---
# <a name="pass-an-identity-provider-access-token-to-your-application-in-azure-active-directory-b2c"></a>Übergeben des Zugriffstokens eines Identitätsanbieters an Ihre Anwendung in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Ein [Benutzerflow](user-flow-overview.md) in Azure Active Directory B2C (Azure AD B2C) ermöglicht es Benutzern Ihrer Anwendung, sich mit einem Identitätsanbieter zu registrieren oder anzumelden. Bei diesem Vorgang empfängt Azure AD B2C zunächst ein [Zugriffstoken](tokens-overview.md) vom Identitätsanbieter. Azure AD B2C verwendet dieses Token, um Informationen zum Benutzer abzurufen. Sie aktivieren einen Anspruch in Ihrem Benutzerflow, um das Token an die Anwendungen zu übergeben, die Sie in Azure AD B2C registrieren.

::: zone pivot="b2c-user-flow"

Azure AD B2C unterstützt die Übergabe des Zugriffstokens für [OAuth 2.0](add-identity-provider.md)-Identitätsanbieter. Hierzu zählen unter anderem [Facebook](identity-provider-facebook.md) und [Google](identity-provider-google.md). Für alle weiteren Identitätsanbieter wird ein leerer Anspruch zurückgegeben.

::: zone-end

::: zone pivot="b2c-custom-policy"

Azure AD B2C unterstützt die Übergabe des Zugriffstokens von [OAuth 2.0](authorization-code-flow.md)- und [OpenID Connect](openid-connect.md)-Identitätsanbietern. Für alle weiteren Identitätsanbieter wird ein leerer Anspruch zurückgegeben.

::: zone-end

Das folgende Diagramm zeigt, wie ein Identitätsanbieter Token an Ihre App zurückgibt: 

![Weiterleitungsflow bei einem Identitätsanbieter](./media/idp-pass-through-user-flow/identity-provider-pass-through-flow.png)

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="enable-the-claim"></a>Aktivieren des Anspruchs

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) als globaler Administrator Ihres Azure AD B2C-Mandanten an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Klicken Sie links oben im Azure-Portal auf **Alle Dienste**, suchen Sie nach **Azure AD B2C**, und klicken Sie darauf.
1. Wählen Sie **Benutzerflows (Richtlinien)** und dann Ihren Benutzerflow aus. Beispiel: **B2C_1_signupsignin1**.
1. Wählen Sie **Anwendungsansprüche** aus.
1. Aktivieren Sie den Anspruch **Zugriffstoken des Identitätsanbieters**.

    ![Aktivieren des Anspruchs „Zugriffstoken des Identitätsanbieters“](./media/idp-pass-through-user-flow/identity-provider-pass-through-app-claim.png)

1. Klicken Sie auf **Speichern**, um den Benutzerflow zu speichern.

## <a name="test-the-user-flow"></a>Testen des Benutzerflows

Wenn Sie Ihre Anwendungen in Azure AD B2C testen, kann es nützlich sein, das Azure AD B2C-Token an `https://jwt.ms` zurückzugeben, um die darin enthaltenen Ansprüche zu überprüfen.

1. Wählen Sie auf der Übersichtsseite des Benutzerflows die Option **Benutzerflow ausführen** aus.
1. Wählen Sie für **Anwendung** Ihre Anwendung aus, die Sie zuvor registriert haben. Um das Token im nachstehenden Beispiel anzuzeigen, muss als **Antwort-URL** der Wert `https://jwt.ms` angegeben werden.
1. Klicken Sie auf **Benutzerflow ausführen**, und melden Sie sich dann mit den Anmeldeinformationen für Ihr Konto an. Es sollte das Zugriffstoken des Identitätsanbieters im Anspruch **idp_access_token** angezeigt werden.

    Sie sollten eine Ausgabe ähnlich wie im folgenden Beispiel sehen:

    ![Decodiertes Token in „jwt.ms“ mit hervorgehobenem Block „idp_access_token“](./media/idp-pass-through-user-flow/identity-provider-pass-through-token.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="add-the-claim-elements"></a>Hinzufügen der Anspruchselemente

1. Öffnen Sie Ihre Datei *TrustframeworkExtensions.xml*, und fügen Sie das folgende **ClaimType**-Element mit dem Bezeichner `identityProviderAccessToken` zum **ClaimsSchema**-Element hinzu:

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="identityProviderAccessToken">
          <DisplayName>Identity Provider Access Token</DisplayName>
          <DataType>string</DataType>
          <AdminHelpText>Stores the access token of the identity provider.</AdminHelpText>
        </ClaimType>
        ...
      </ClaimsSchema>
    </BuildingBlocks>
    ```

1. Fügen Sie für jeden OAuth 2.0-Anbieter, für den Sie ein Zugriffstoken benötigen, das **OutputClaim**-Element zum **TechnicalProfile**-Element hinzu. Das folgende Beispiel zeigt, wie das Element zum technischen Profil für Facebook hinzugefügt wurde:

    ```xml
    <ClaimsProvider>
      <DisplayName>Facebook</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Facebook-OAUTH">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="{oauth2:access_token}" />
          </OutputClaims>
          ...
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Speichern Sie die Datei *TrustframeworkExtensions.xml*.
1. Öffnen Sie Ihre Richtliniendatei der vertrauenden Seite, z.B. *SignUpOrSignIn.xml*, und fügen Sie das **OutputClaim**-Element zum **TechnicalProfile**-Element hinzu:

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="idp_access_token"/>
        </OutputClaims>
        ...
      </TechnicalProfile>
    </RelyingParty>
    ```

1. Speichern Sie die Richtliniendatei.

## <a name="test-your-policy"></a>Testen Ihrer Richtlinie

Wenn Sie Ihre Anwendungen in Azure AD B2C testen, kann es nützlich sein, das Azure AD B2C-Token an `https://jwt.ms` zurückzugeben, um die darin enthaltenen Ansprüche zu überprüfen.

### <a name="upload-the-files"></a>Hochladen der Dateien

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie **Framework für die Identitätsfunktion** aus.
1. Klicken Sie auf der Seite „Benutzerdefinierte Richtlinien“ auf **Richtlinie hochladen**.
1. Aktivieren Sie **Richtlinie überschreiben, sofern vorhanden**, suchen Sie nach der Datei *TrustframeworkExtensions.xml*, und wählen Sie die Datei aus.
1. Klicken Sie auf **Hochladen**.
1. Wiederholen Sie die Schritte 5 bis 7 für die Datei der vertrauenden Seite, z.B. *SignUpOrSignIn.xml*.

### <a name="run-the-policy"></a>Ausführen der Richtlinie

1. Öffnen Sie die Richtlinie, die Sie geändert haben. Beispiel: *B2C_1A_signup_signin*.
1. Wählen Sie für **Anwendung** Ihre Anwendung aus, die Sie zuvor registriert haben. Um das Token im nachstehenden Beispiel anzuzeigen, muss als **Antwort-URL** der Wert `https://jwt.ms` angegeben werden.
1. Wählen Sie **Jetzt ausführen** aus.

    Sie sollten eine Ausgabe ähnlich wie im folgenden Beispiel sehen:

    ![Decodiertes Token in „jwt.ms“ mit hervorgehobenem Block „idp_access_token“](./media/idp-pass-through-user-flow/identity-provider-pass-through-token-custom.png)

::: zone-end

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Azure AD B2C: Tokenreferenz](tokens-overview.md).
