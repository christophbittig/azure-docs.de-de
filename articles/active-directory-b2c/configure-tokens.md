---
title: Konfigurieren von Token – Azure Active Directory B2C
description: Erfahren Sie, wie Sie die Lebensdauer der Token und die Kompatibilitätseinstellungen in Azure Active Directory B2C konfigurieren.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 70be31920e96c651285c4e77ace161b65ccd3118
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130066032"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Konfigurieren von Token in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

In diesem Artikel erfahren Sie, wie Sie die [Lebensdauer und Kompatibilität eines Tokens](tokens-overview.md) in Azure Active Directory B2C (Azure AD B2C) konfigurieren.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="token-lifetime-behavior"></a>Tokenlebensdauer und Verhalten

Sie können die Lebens- bzw. Gültigkeitsdauer von Token wie folgt konfigurieren:

- **Lebensdauer von Zugriffs- und ID-Token (Minuten)** : Gültigkeitsdauer des OAuth 2.0-Bearertokens und der ID-Token. Der Standardwert beträgt 60 Minuten (1 Stunde). Der Mindestwert ist fünf Minuten (einschließlich). Der Höchstwert beträgt 1.440 Minuten (24 Stunden).
- **Lebensdauer des Aktualisierungstokens (Tage)** : Der maximale Zeitraum, in dem ein Aktualisierungstoken zum Anfordern eines neuen Zugriffstokens verwendet werden kann, wenn Ihrer Anwendung der Bereich `offline_access` erteilt wurde. Der Standardwert ist 14 Tage. Der Mindestwert ist ein Tag (einschließlich). Der Höchstwert beträgt 90 Tage (einschließlich).
- **Lebensdauer für gleitendes Fenster des Aktualisierungstokens**: Typ des gleitenden Fensters für das Aktualisierungstoken. `Bounded` gibt an, dass das Aktualisierungstoken wie unter **Lebensdauer (Tage)** angegeben verlängert werden kann. `No expiry` gibt an, dass die Lebensdauer für das gleitende Fenster des Aktualisierungstokens nie abläuft.
- **Lebensdauer (Tage)** : Nach Ablauf dieses Zeitraums muss der Benutzer sich erneut authentifizieren, und zwar unabhängig von der Gültigkeitsdauer des zuletzt von der Anwendung erworbenen Aktualisierungstokens. Der Wert muss mindestens dem Wert **Lebensdauer des Aktualisierungstokens (Tage)** entsprechen oder darüber liegen.

Das folgende Diagramm zeigt das Verhalten der Lebensdauer für das gleitende Fenster des Aktualisierungstokens.

![Lebensdauer von Aktualisierungstoken](./media/configure-tokens/refresh-token-lifetime.png)

> [!NOTE]
> >Bei Single-Page-Webanwendungen, die den Autorisierungscodeflow mit PKCE verwenden, beträgt die Lebensdauer von Aktualisierungstoken immer 24 Stunden. Diese Einschränkung gilt nicht für mobile Apps, Desktop-Apps und Web-Apps. [Weitere Informationen zu Sicherheitsaspekten von Aktualisierungstoken im Browser](../active-directory/develop/reference-third-party-cookies-spas.md#security-implications-of-refresh-tokens-in-the-browser)


## <a name="configure-token-lifetime"></a>Konfigurieren der Tokenlebensdauer

::: zone pivot="b2c-user-flow"

So konfigurieren Sie die Tokenlebensdauer für den Benutzerflow

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie **Benutzerflows (Richtlinien)** aus.
1. Öffnen Sie den Benutzerflow, den Sie zuvor erstellt haben.
1. Wählen Sie **Eigenschaften** aus.
1. Passen Sie unter **Tokenlebensdauer** die Eigenschaften an die Anforderungen Ihrer Anwendung an.
1. Klicken Sie auf **Speichern**.



:::image type="content" source="./media/configure-tokens/configure-tokens.png" alt-text="Konfigurieren von Benutzerflowtoken im Azure-Portal":::


::: zone-end

::: zone pivot="b2c-custom-policy"

Um die Einstellungen für die Tokenkompatibilität zu ändern, legen Sie in der Erweiterung die Metadaten des technischen Profils für den [Tokenaussteller](jwt-issuer-technical-profile.md) oder die Datei der vertrauenden Seite der Richtlinie fest, die Sie beeinflussen möchten. Das technische Profil für den Tokenaussteller sieht wie im folgenden Beispiel aus:

```xml
<ClaimsProviders>
  <ClaimsProvider>
    <DisplayName>Token Issuer</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="JwtIssuer">
        <Metadata>
          <Item Key="token_lifetime_secs">3600</Item>
          <Item Key="id_token_lifetime_secs">3600</Item>
          <Item Key="refresh_token_lifetime_secs">1209600</Item>
          <Item Key="rolling_refresh_token_lifetime_secs">7776000</Item>
          <!--<Item Key="allow_infinite_rolling_refresh_token">true</Item>-->
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

Im vorstehenden Beispiel werden die folgenden Werte festgelegt:

- **token_lifetime_secs**: Lebensdauer von Zugriffstoken (Sekunden). Der Standardwert beträgt 3.600 Sekunden (1 Stunde). Der Mindestwert beträgt 300 Sekunden (5 Minuten). Der Höchstwert beträgt 86.400 Sekunden (24 Stunden). 
- **id_token_lifetime_secs**: Lebensdauer von ID-Token (Sekunden). Der Standardwert beträgt 3.600 Sekunden (1 Stunde). Der Mindestwert beträgt 300 Sekunden (5 Minuten). Der Höchstwert beträgt 86.400 Sekunden (24 Stunden). 
- **refresh_token_lifetime_secs**: Lebensdauer von Aktualisierungstoken (Sekunden). Der Standardwert beträgt 1.209.600 Sekunden (14 Tage). Der Mindestwert beträgt 86.400 Sekunden (24 Stunden). Der Höchstwert beträgt 7.776.000 Sekunden (90 Tage). 
- **rolling_refresh_token_lifetime_secs**: Lebensdauer für gleitendes Fenster des Aktualisierungstokens (Sekunden). Der Standardwert beträgt 7.776.000 Sekunden (90 Tage). Der Mindestwert beträgt 86.400 Sekunden (24 Stunden). Der Höchstwert beträgt 31.536.000 Sekunden (365 Tage). Wenn Sie keine Lebensdauer für das gleitende Fenster erzwingen möchten, legen Sie den Wert `allow_infinite_rolling_refresh_token` auf `true` fest. 
- **allow_infinite_rolling_refresh_token**: Das gleitende Fenster für Aktualisierungstoken läuft nie ab. 

::: zone-end


## <a name="token-compatibility-settings"></a>Tokenkompatibilitätseinstellungen

Sie können die Kompatibilität von Token konfigurieren. Dazu zählen auch:

- **Ausstelleranspruch (iss)** : Das Format des Ausstellers von Zugriffs- und ID-Token.
- **Antragstelleranspruch (sub)** : Der Prinzipal, für den das Token Informationen bestätigt (z. B. Benutzer einer Anwendung). Dieser Wert ist unveränderlich und kann nicht erneut zugewiesen oder wiederverwendet werden. Er kann für die sichere Durchführung von Autorisierungsüberprüfungen verwendet werden, z.B. wenn das Token verwendet wird, um auf eine Ressource zuzugreifen. Der Anspruch „Antragsteller“ wird standardmäßig mit der Objekt-ID des Benutzers im Verzeichnis aufgefüllt.
- **Anspruch für Benutzerflow**: Dieser Anspruch identifiziert den ausgeführten Benutzerflow. Mögliche Werte: `tfp` (Standard) oder `acr`.

::: zone pivot="b2c-user-flow"

So konfigurieren Sie die Kompatibilitätseinstellungen für Benutzerflows

1. Wählen Sie **Benutzerflows (Richtlinien)** aus.
1. Öffnen Sie den Benutzerflow, den Sie zuvor erstellt haben.
1. Wählen Sie **Eigenschaften** aus.
1. Passen Sie unter **Tokenkompatibilitätseinstellungen** die Eigenschaften an die Anforderungen Ihrer Anwendung an.
1. Klicken Sie auf **Speichern**.

::: zone-end

::: zone pivot="b2c-custom-policy"

Um die Einstellungen für die Tokenkompatibilität zu ändern, legen Sie in der Erweiterung die Metadaten des technischen Profils für den [Tokenaussteller](jwt-issuer-technical-profile.md) oder die Datei der vertrauenden Seite der Richtlinie fest, die Sie beeinflussen möchten. Das technische Profil für den Tokenaussteller sieht wie im folgenden Beispiel aus:

```xml
<ClaimsProviders>
  <ClaimsProvider>
    <DisplayName>Token Issuer</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="JwtIssuer">
        <Metadata>
          ...
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

- **Ausstelleranspruch (iss):** Der Ausstelleranspruch (iss) wird mit dem Metadatenelement **IssuanceClaimPattern** festgelegt. Die gültigen Werte sind `AuthorityAndTenantGuid` und `AuthorityWithTfp`.
- **Festlegen des Anspruchs zur Darstellung der Richtlinien-ID:** Die Optionen zum Festlegen dieses Werts sind `TFP` (Vertrauensframework-Richtlinie) und `ACR` (Authentifizierungskontext-Referenz). Der empfohlene Wert ist `TFP`. Legen Sie für **AuthenticationContextReferenceClaimPattern** den Wert `None` fest.

    Fügen Sie im **ClaimsSchema**-Element dieses Element hinzu:

    ```xml
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```

    Fügen Sie in der [Richtlinie für die vertrauende Seite](relyingparty.md)unter dem **OutputClaims**-Element den folgenden Ausgabeanspruch hinzu:

    ```xml
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" PartnerClaimType="tfp" />
    ```

    Entfernen Sie für ACR das Element **AuthenticationContextReferenceClaimPattern**.

- **Anspruch „Antragsteller“:** Diese Option hat standardmäßig den Wert „ObjectID“. Ersetzen Sie die folgende Zeile, um diese Einstellung in `Not Supported` zu ändern:

    ```xml
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```

    Durch diese Zeile:

    ```xml
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

::: zone-end

## <a name="provide-optional-claims-to-your-app"></a>Bereitstellen optionaler Ansprüche für Ihre App

Die Anwendungsansprüche sind Werte, die an die Anwendung zurückgegeben werden. Aktualisieren Sie den Benutzerflow, sodass dieser die gewünschten Ansprüche enthält.

::: zone pivot="b2c-user-flow"

1. Wählen Sie **Benutzerflows (Richtlinien)** aus.
1. Öffnen Sie den Benutzerflow, den Sie zuvor erstellt haben.
1. Wählen Sie **Anwendungsansprüche** aus.
1. Wählen Sie die Ansprüche und Attribute aus, die an die Anwendung zurückgesendet werden sollen.
1. Klicken Sie auf **Speichern**.

::: zone-end

::: zone pivot="b2c-custom-policy"

Ausgabeansprüche des [technischen Profils der Richtlinie für die vertrauende Seite](relyingparty.md#technicalprofile) sind Werte, die an eine Anwendung zurückgegeben werden. Durch Hinzufügen von Ausgabeansprüchen werden die Ansprüche nach einer erfolgreichen User Journey in das Token ausgegeben und an die Anwendung gesendet. Ändern Sie im Abschnitt für die vertrauende Seite das Element des technischen Profils, um die gewünschten Ansprüche als Ausgabeanspruch hinzuzufügen.

1. Öffnen Sie Ihre benutzerdefinierte Richtliniendatei. Beispiel: SignUpOrSignin.xml.
1. Suchen Sie nach dem OutputClaims-Element. Fügen Sie den OutputClaim hinzu, den Sie in das Token einschließen möchten. 
1. Legen Sie die Attribute des Ausgabeanspruchs fest. 

Im folgenden Beispiel wird der Anspruch `accountBalance` hinzugefügt. Der Anspruch „accountBalance“ wird als Kontostand an die Anwendung gesendet. 

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
      <!--Add the optional claims here-->
      <OutputClaim ClaimTypeReferenceId="accountBalance" DefaultValue="" PartnerClaimType="balance" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

Das OutputClaim-Element enthält die folgenden Attribute:

- **ClaimTypeReferenceId**: Der Bezeichner eines Anspruchstyps, der bereits im [ClaimsSchema](claimsschema.md)-Abschnitt der Richtliniendatei oder der übergeordneten Richtliniendatei definiert ist.
- **PartnerClaimType**: Ermöglicht Ihnen das Ändern des Namens des Anspruchs im Token. 
- **DefaultValue**: Ein Standardwert. Sie können den Standardwert auch auf einen [Anspruchskonfliktlöser](claim-resolver-overview.md) wie die Mandanten-ID festlegen.
- **AlwaysUseDefaultValue**: Erzwingt die Verwendung des Standardwerts.

::: zone-end

## <a name="authorization-code-lifetime"></a>Lebensdauer des Autorisierungscodes

Wenn Sie den [OAuth 2.0-Autorisierungscodefluss](authorization-code-flow.md) verwenden, kann die App den Autorisierungscode verwenden, um ein Zugriffstoken für eine Zielressource anzufordern. Autorisierungscodes sind kurzlebig und laufen nach etwa zehn Minuten ab. Die Lebensdauer des Autorisierungscodes kann nicht konfiguriert werden. Stellen Sie sicher, dass Ihre Anwendung die Autorisierungscodes innerhalb von 10 Minuten einlöst. 

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum [Anfordern von Zugriffstoken](access-tokens.md).
