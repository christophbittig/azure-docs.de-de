---
title: Konfigurieren der Anforderungen an die Komplexität des Kennworts
titleSuffix: Azure AD B2C
description: Konfigurieren der Komplexitätsanforderungen für Kennwörter, die von Kunden in Azure Active Directory B2C bereitgestellt werden
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/20/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 14c269ae8cb29d32659f500357bbc7bcd81e6d8c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128578924"
---
# <a name="configure-complexity-requirements-for-passwords-in-azure-active-directory-b2c"></a>Konfigurieren der Komplexitätsanforderungen für Kennwörter in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) unterstützt das Ändern der Komplexitätsanforderungen für Kennwörter, die von einem Endbenutzer beim Erstellen eines Kontos bereitgestellt werden. Standardmäßig verwendet Azure AD B2C **starke** Kennwörter. Azure AD B2C unterstützt außerdem Konfigurationsoptionen zur Steuerung der Komplexität von Kennwörtern, die Kunden verwenden können.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="password-rule-enforcement"></a>Kennwortregelerzwingung

Bei der Registrierung oder dem Zurücksetzen des Kennworts muss ein Endbenutzer ein Kennwort angeben, das die Regeln für die Komplexität erfüllt. Kennwortkomplexitätsregeln werden pro Benutzerflow erzwungen. Es ist möglich, dass ein Benutzerflow eine vierstellige PIN zur Registrierung fordert, ein anderer Benutzerflow hingegen eine Zeichenfolge von acht Zeichen. Sie können z.B. einen Benutzerflow mit unterschiedlicher Kennwortkomplexität für Erwachsene und Kinder verwenden.

Kennwortkomplexität wird nie während der Anmeldung erzwungen. Benutzer werden nie während der Anmeldung aufgefordert, ihr Kennwort zu ändern, da es die aktuellen Komplexitätsanforderungen nicht erfüllt.

In den folgenden Typen von Benutzerflows kann die Kennwortkomplexität konfiguriert werden:

- Benutzerflow für die Registrierung oder Anmeldung
- Benutzerflow für die Kennwortzurücksetzung

Wenn Sie benutzerdefinierte Richtlinien verwenden, können Sie [die Kennwortkomplexität in einer benutzerdefinierten Richtlinie konfigurieren](password-complexity.md).

## <a name="configure-password-complexity"></a>Konfigurieren der Kennwortkomplexität

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** Ihr Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Suchen Sie im Azure-Portal nach **Azure AD B2C**, und wählen Sie diese Option dann aus.
1. Wählen Sie **Benutzerflows** aus.
1. Wählen Sie einen Benutzerflow aus, und klicken Sie auf **Eigenschaften**.
1. Ändern Sie unter **Kennwortkomplexität** die Kennwortkomplexität für diesen Benutzerflow in **Einfach**, **Stark** oder **Benutzerdefiniert**.

### <a name="comparison-chart"></a>Vergleichsdiagramm

| Komplexität | BESCHREIBUNG |
| --- | --- |
| Einfach | Ein Kennwort mit mindestens 8 bis 64 Zeichen. |
| STARK (Strong) | Ein Kennwort mit mindestens 8 bis 64 Zeichen. Es sind 3 der 4 Zeichentypen Ziffern, Groß- und Kleinbuchstaben oder Symbole erforderlich. |
| Benutzerdefiniert | Diese Option bietet die bestmögliche Steuerung der Kennwortkomplexitätsregeln.  Sie ermöglicht das Konfigurieren einer benutzerdefinierten Länge.  Darüber hinaus ermöglicht sie das ausschließliche Akzeptieren von Kennwörtern, die nur aus Ziffern (PINs) bestehen. |

## <a name="custom-options"></a>Benutzerdefinierte Optionen

### <a name="character-set"></a>Zeichensatz

Ermöglicht Ihnen, wahlweise nur Ziffern (PIN) oder den vollständigen Zeichensatz zu akzeptieren.

- **Nur Ziffern** lässt nur Ziffern (0-9) bei der Kennworteingabe zu.
- **Alle** lässt beliebige Buchstaben, Ziffern oder Symbole zu.

### <a name="length"></a>Länge

Ermöglicht Ihnen, die Längenanforderungen des Kennworts zu steuern.

- **Mindestlänge** muss mindestens 4 sein.
- **Maximale Länge** muss größer oder gleich der Mindestlänge und höchstens 256 Zeichen lang sein.

### <a name="character-classes"></a>Zeichenklassen

Ermöglicht Ihnen, die verschiedenen im Kennwort verwendeten Zeichentypen zu steuern.

- **2 von 4: Kleinbuchstaben, Großbuchstaben, Ziffern (0–9), Symbole** stellt sicher, dass das Kennwort mindestens drei Zeichentypen enthält. Beispielsweise eine Ziffer und einen Kleinbuchstaben.
- **3 von 4: Kleinbuchstaben, Großbuchstaben, Ziffern (0–9), Symbole** stellt sicher, dass das Kennwort mindestens drei Zeichentypen enthält. Beispielsweise eine Ziffer, einen Kleinbuchstaben und einen Großbuchstaben.
- **4 von 4: Kleinbuchstaben, Großbuchstaben, Ziffern (0–9), Symbole** stellt sicher, dass das Kennwort alle vier Zeichentypen enthält.

    > [!NOTE]
    > **4 von 4** zu fordern, kann beim Endbenutzer für Frustration sorgen. Studien haben gezeigt, dass diese Anforderung die Kennwortentropie nicht verbessert. Siehe [NIST Password Guidelines](https://pages.nist.gov/800-63-3/sp800-63b.html#appA) (NIST-Kennwortrichtlinien).

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="password-predicate-validation"></a>Prädikatüberprüfungen für Kennwörter

Um die Kennwortkomplexität zu konfigurieren, überschreiben Sie die [Anspruchstypen](claimsschema.md) `newPassword` und `reenterPassword` mit einem Verweis auf [Prädikatüberprüfungen](predicates.md#predicatevalidations). Das Element „PredicateValidations“ gruppiert eine Reihe von Prädikaten mit dem Ziel, eine Überprüfung der Benutzereingabe zu erstellen, die auf einen Anspruchstyp angewendet werden kann. Öffnen Sie die Erweiterungsdatei Ihrer Richtlinie. Beispiel: <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.

1. Suchen Sie nach dem Element [BuildingBlocks](buildingblocks.md). Wenn das Element nicht vorhanden ist, fügen Sie es hinzu.
1. Suchen Sie nach dem Element [ClaimsSchema](claimsschema.md). Wenn das Element nicht vorhanden ist, fügen Sie es hinzu.
1. Fügen Sie dem Element **ClaimsSchema** die Ansprüche `newPassword` und `reenterPassword` hinzu.

    ```xml
    <!-- 
    <BuildingBlocks>
      <ClaimsSchema> -->
        <ClaimType Id="newPassword">
          <PredicateValidationReference Id="CustomPassword" />
        </ClaimType>
        <ClaimType Id="reenterPassword">
          <PredicateValidationReference Id="CustomPassword" />
        </ClaimType>
      <!-- 
      </ClaimsSchema>
    </BuildingBlocks>-->
    ```

1. Das Element [Predicates](predicates.md) definiert eine grundlegende Überprüfung zum Prüfen des Werts eines Anspruchstyps und gibt „true“ oder „false“ zurück. Die Überprüfung erfolgt mit einem bestimmten Methodenelement und einer Reihe von Parametern, die für die Methode relevant sind. Fügen Sie dem Element **BuildingBlocks** unmittelbar nach dem Schließen des Elements `</ClaimsSchema>` die folgenden Prädikate hinzu:

    ```xml
    <!-- 
    <BuildingBlocks>-->
      <Predicates>
        <Predicate Id="LengthRange" Method="IsLengthRange">
          <UserHelpText>The password must be between 6 and 64 characters.</UserHelpText>
          <Parameters>
            <Parameter Id="Minimum">6</Parameter>
            <Parameter Id="Maximum">64</Parameter>
          </Parameters>
        </Predicate>
        <Predicate Id="Lowercase" Method="IncludesCharacters">
          <UserHelpText>a lowercase letter</UserHelpText>
          <Parameters>
            <Parameter Id="CharacterSet">a-z</Parameter>
          </Parameters>
        </Predicate>
        <Predicate Id="Uppercase" Method="IncludesCharacters">
          <UserHelpText>an uppercase letter</UserHelpText>
          <Parameters>
            <Parameter Id="CharacterSet">A-Z</Parameter>
          </Parameters>
        </Predicate>
        <Predicate Id="Number" Method="IncludesCharacters">
          <UserHelpText>a digit</UserHelpText>
          <Parameters>
            <Parameter Id="CharacterSet">0-9</Parameter>
          </Parameters>
        </Predicate>
        <Predicate Id="Symbol" Method="IncludesCharacters">
          <UserHelpText>a symbol</UserHelpText>
          <Parameters>
            <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\\:',.?/`~"();!</Parameter>
          </Parameters>
        </Predicate>
      </Predicates>
    <!-- 
    </BuildingBlocks>-->
    ```

1. Fügen Sie dem Element **BuildingBlocks** unmittelbar nach dem Schließen des Elements `</Predicates>` die folgenden Prädikatüberprüfungen hinzu:

    ```xml
    <!-- 
    <BuildingBlocks>-->
      <PredicateValidations>
        <PredicateValidation Id="CustomPassword">
          <PredicateGroups>
            <PredicateGroup Id="LengthGroup">
              <PredicateReferences MatchAtLeast="1">
                <PredicateReference Id="LengthRange" />
              </PredicateReferences>
            </PredicateGroup>
            <PredicateGroup Id="CharacterClasses">
              <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
              <PredicateReferences MatchAtLeast="3">
                <PredicateReference Id="Lowercase" />
                <PredicateReference Id="Uppercase" />
                <PredicateReference Id="Number" />
                <PredicateReference Id="Symbol" />
              </PredicateReferences>
            </PredicateGroup>
          </PredicateGroups>
        </PredicateValidation>
      </PredicateValidations>
    <!-- 
    </BuildingBlocks>-->
    ```

## <a name="disable-strong-password"></a>Deaktivieren sicherer Kennwörter 

Die folgenden technischen Profile sind [technische Active Directory-Profile](active-directory-technical-profile.md), die Daten in Azure Active Directory schreiben und daraus lesen. Überschreiben Sie diese technischen Profile in der Erweiterungsdatei. Verwenden Sie `PersistedClaims`, um die sichere Kennwortrichtlinie zu deaktivieren. Suchen Sie nach dem Element **ClaimsProviders**.  Fügen Sie wie folgt die folgenden Anspruchsanbieter hinzu:

```xml
<!-- 
<ClaimsProviders>-->
  <ClaimsProvider>
    <DisplayName>Azure Active Directory</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
        <PersistedClaims>
          <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
        </PersistedClaims>
      </TechnicalProfile>
      <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
        <PersistedClaims>
          <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
        </PersistedClaims>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
<!-- 
</ClaimsProviders>-->
```

Wenn Sie die Richtlinie [Auf Benutzername basierende Anmeldung](https://github.com/azure-ad-b2c/samples/tree/master/policies/username-signup-or-signin) verwenden, aktualisieren Sie die technischen Profile `AAD-UserWriteUsingLogonEmail`, `AAD-UserWritePasswordUsingObjectId` und `LocalAccountWritePasswordUsingObjectId` mit der Richtlinie *DisableStrongPassword*.

Speichern Sie die Richtliniendatei.

## <a name="test-your-policy"></a>Testen Ihrer Richtlinie

### <a name="upload-the-files"></a>Hochladen der Dateien

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie **Framework für die Identitätsfunktion** aus.
1. Klicken Sie auf der Seite „Benutzerdefinierte Richtlinien“ auf **Richtlinie hochladen**.
1. Aktivieren Sie **Richtlinie überschreiben, sofern vorhanden**, suchen Sie nach der Datei *TrustFrameworkExtensions.xml*, und wählen Sie die Datei aus.
1. Klicken Sie auf **Hochladen**.

### <a name="run-the-policy"></a>Ausführen der Richtlinie

1. Öffnen Sie die Registrierungs- oder Anmelderichtlinie. Beispiel: *B2C_1A_signup_signin*.
2. Wählen Sie für **Anwendung** Ihre Anwendung aus, die Sie zuvor registriert haben. Um das Token anzuzeigen, muss als **Antwort-URL** der Wert `https://jwt.ms` angegeben werden.
3. Klicken Sie auf **Jetzt ausführen**.
4. Wählen Sie **Jetzt registrieren** aus, geben Sie eine E-Mail-Adresse ein, und geben Sie ein neues Kennwort ein. Es werden Anweisungen zu Kennworteinschränkungen angezeigt. Schließen Sie die Eingabe der Benutzerinformationen ab, und klicken Sie dann auf **Erstellen**. Es sollten Ihnen jetzt die Inhalte des zurückgegebenen Tokens angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das [Konfigurieren der Kennwortänderung in Azure Active Directory B2C](add-password-change-policy.md).
- Weitere Informationen finden Sie unter den Elementen [Predicates](predicates.md) und [PredicateValidations](predicates.md#predicatevalidations) in der IEF-Referenz.


::: zone-end
