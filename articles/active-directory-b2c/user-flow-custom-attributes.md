---
title: Definieren benutzerdefinierter Attribute in Azure Active Directory B2C | Microsoft-Dokumentation
description: Definieren benutzerdefinierter Attribute für Ihre Anwendung in Azure Active Directory B2C zum Erfassen von Informationen zu Ihren Kunden.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 836802e65c8eb76f17bbf053e54ed9dbedf54049
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346748"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Definieren benutzerdefinierter Attribute in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Im Artikel [Hinzufügen von Ansprüchen und Anpassen von Benutzereingaben mithilfe benutzerdefinierter Richtlinien](configure-user-input.md) erfahren Sie, wie Sie integrierte [Benutzerprofilattribute](user-profile-attributes.md) verwenden können. In diesem Artikel aktivieren Sie in Ihrem Azure Active Directory B2C-Verzeichnis (Azure AD B2C) ein benutzerdefiniertes Attribut. Später können Sie das neue Attribut als benutzerdefinierten Anspruch gleichzeitig in [Benutzerflows](user-flow-overview.md) oder [benutzerdefinierten Richtlinien](user-flow-overview.md) verwenden.

Ihr Azure AD B2C-Verzeichnis verfügt über [mehrere integrierte Attribute](user-profile-attributes.md). Sie müssen jedoch oftmals für die Verwaltung Ihres spezifischen Szenarios eigene Attribute erstellen, z. B. in den folgenden Fällen:

* Für eine Kundenanwendung muss ein Attribut wie **loyaltyId** persistent gespeichert werden.
* Ein Identitätsanbieter hat eine eindeutige Benutzer-ID, die persistent gespeichert werden muss, z. B. **uniqueUserGUID**.
* Für eine benutzerdefinierte User Journey muss der Status eines Benutzers persistent gespeichert werden, z. B. **migrationStatus**.

Die Begriffe *Erweiterungseigenschaft*, *benutzerdefiniertes Attribut* und *benutzerdefinierter Anspruch* beziehen sich im Kontext dieses Artikels auf die gleiche Sache. Der Name variiert je nach Kontext (Anwendung, Objekt, Richtlinie).

Azure AD B2C ermöglicht Ihnen, die für die einzelnen Benutzerkonten gespeicherte Gruppe von Attributen zu erweitern. Außerdem können Sie diese Attribute mit der [Microsoft Graph-API](microsoft-graph-operations.md) lesen und schreiben.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-custom-attribute"></a>Erstellen eines benutzerdefinierten Attributs

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) als globaler Administrator Ihres Azure AD B2C-Mandanten an.
1. Vergewissern Sie sich, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält, indem Sie oben rechts im Azure-Portal zu diesem Verzeichnis wechseln. Wählen Sie die Abonnementinformationen aus, und klicken Sie dann auf **Verzeichnis wechseln**.
1. Klicken Sie links oben im Azure-Portal auf **Alle Dienste**, suchen Sie nach **Azure AD B2C**, und klicken Sie darauf.
1. Wählen Sie **Benutzerattribute** und dann **Hinzufügen** aus.
1. Geben Sie einen **Namen** für das benutzerdefinierte Attribut an (z.B. „ShoeSize“).
1. Wählen Sie einen **Datentyp** aus. Es stehen nur **Zeichenfolge**, **Boolescher Wert** und **Int** zur Verfügung.
1. Geben Sie optional eine **Beschreibung** zu Informationszwecken ein.
1. Klicken Sie auf **Erstellen**.

Das benutzerdefinierte Attribut steht jetzt in der Liste der **Benutzerattribute** zur Verfügung und kann in den Benutzerflows verwendet werden. Ein benutzerdefiniertes Attribut wird nur erstellt, wenn es zum ersten Mal in einem Benutzerflow verwendet wird, und nicht, wenn Sie es der Liste der **Benutzerattribute** hinzufügen.

::: zone pivot="b2c-user-flow"

## <a name="use-a-custom-attribute-in-your-user-flow"></a>Verwenden eines benutzerdefinierten Attributs in einem Benutzerflow

1. Wählen Sie in Ihrem Azure AD B2C-Mandanten die Option **Benutzerflows** aus.
1. Wählen Sie eine Richtlinie aus (z.B. „B2C_1_SignupSignin“), um sie zu öffnen.
1. Wählen Sie **Benutzerattribute** und dann das benutzerdefinierte Attribut aus (z.B. „ShoeSize“). Klicken Sie auf **Speichern**.
1. Wählen Sie **Anwendungsansprüche** und dann das benutzerdefinierte Attribut aus.
1. Klicken Sie auf **Speichern**.

Nach dem Erstellen eines neuen Benutzers über einen Benutzerflow, der das neu erstellte benutzerdefinierte Attribut verwendet, kann das Objekt im [Microsoft Graph-Tester](https://developer.microsoft.com/graph/graph-explorer) abgefragt werden. Alternativ können Sie mit der Funktion [Benutzerflow ausführen](./tutorial-create-user-flows.md) für den Benutzerflow die Benutzerfreundlichkeit überprüfen. **ShoeSize** sollte jetzt in der Liste der Attribute, die während der Registrierung erfasst werden, sowie in dem Token angezeigt werden, das zurück an die Anwendung gesendet wird.

::: zone-end

## <a name="azure-ad-b2c-extensions-app"></a>Erweiterungs-App für Azure AD B2C

Erweiterungsattribute können nur für ein Anwendungsobjekt registriert werden, obwohl sie Daten für einen Benutzer enthalten können. Das Erweiterungsattribut wird an die Anwendung namens `b2c-extensions-app` angefügt. Ändern Sie diese Anwendung nicht, da sie von Azure AD B2C zum Speichern von Benutzerdaten genutzt wird. Sie finden diese Anwendung unter Azure AD B2C, App-Registrierungen. 

::: zone pivot="b2c-user-flow"

So rufen Sie die Anwendungs-ID ab:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im oberen Menü den Filter **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.
1. Wählen Sie im linken Menü die Option **Azure AD B2C** aus. Oder wählen Sie **Alle Dienste** aus, suchen Sie nach dem Eintrag **Azure AD B2C**, und wählen Sie ihn aus.
1. Wählen Sie **App-Registrierungen** und dann **Alle Anwendungen** aus.
1. Wählen Sie die Anwendung `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` aus.
1. Kopieren Sie die **Anwendungs-ID**. Beispiel: `11111111-1111-1111-1111-111111111111`.
 
::: zone-end

::: zone pivot="b2c-custom-policy"

Rufen Sie die Anwendungseigenschaften ab:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im oberen Menü den Filter **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.
1. Wählen Sie im linken Menü die Option **Azure AD B2C** aus. Oder wählen Sie **Alle Dienste** aus, suchen Sie nach dem Eintrag **Azure AD B2C**, und wählen Sie ihn aus.
1. Wählen Sie **App-Registrierungen** und dann **Alle Anwendungen** aus.
1. Wählen Sie die Anwendung `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` aus.
1. Kopieren Sie die folgenden Bezeichner in die Zwischenablage, und speichern Sie sie:
    * **Anwendungs-ID**. Beispiel: `11111111-1111-1111-1111-111111111111`.
    * **Objekt-ID** Beispiel: `22222222-2222-2222-2222-222222222222`.

## <a name="modify-your-custom-policy"></a>Ändern der benutzerdefinierten Richtlinie

Um benutzerdefinierte Attribute in Ihrer Richtlinie zu aktivieren, geben Sie die **Anwendungs-ID** und **Objekt-ID** der Anwendung in den Metadaten des technischen Profils AAD-Common an. Das technische Profil *AAD-Common* befindet sich im technischen Basisprofil [Azure Active Directory](active-directory-technical-profile.md) und bietet Unterstützung für die Benutzerverwaltung in Azure AD. Andere technische Profile von Azure AD enthalten AAD-Common, um dessen Konfiguration zu nutzen. Überschreiben Sie das technische Profil AAD-Common in der Erweiterungsdatei.

1. Öffnen Sie die Erweiterungsdatei Ihrer Richtlinie. Beispiel: <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.
1. Wechseln Sie zum ClaimsProviders-Element. Fügen Sie dem ClaimsProviders-Element ein neues ClaimsProvider-Element hinzu.
1. Fügen Sie die zuvor erfasste **Anwendungs-ID** zwischen dem öffnenden und dem schließenden Element (`<Item Key="ClientId">` und `</Item>`) ein.
1. Fügen Sie die zuvor erfasste **Anwendungsobjekt-ID** zwischen dem öffnenden und dem schließenden Element (`<Item Key="ApplicationObjectId">` und `</Item>`) ein.

    ```xml
    <!-- 
    <ClaimsProviders> -->
      <ClaimsProvider>
        <DisplayName>Azure Active Directory</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="AAD-Common">
            <Metadata>
              <!--Insert b2c-extensions-app application ID here, for example: 11111111-1111-1111-1111-111111111111-->  
              <Item Key="ClientId"></Item>
              <!--Insert b2c-extensions-app application ObjectId here, for example: 22222222-2222-2222-2222-222222222222-->
              <Item Key="ApplicationObjectId"></Item>
            </Metadata>
          </TechnicalProfile>
        </TechnicalProfiles> 
      </ClaimsProvider>
    <!-- 
    </ClaimsProviders> -->
    ```

## <a name="upload-your-custom-policy"></a>Hochladen der benutzerdefinierten Richtlinie

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD-Mandanten enthält, indem Sie im oberen Menü den Filter **Verzeichnis und Abonnement** und dann das Verzeichnis auswählen, das Ihren Azure AD B2C-Mandanten enthält.
3. Klicken Sie links oben im Azure-Portal auf **Alle Dienste**, suchen Sie nach **App-Registrierungen**, und wählen Sie dann diese Option aus.
4. Wählen Sie **Framework für die Identitätsfunktion** aus.
5. Wählen Sie **Benutzerdefinierte Richtlinie hochladen** aus, und laden Sie dann die geänderten Richtliniendateien des Typs „TrustFrameworkExtensions.xml“ hoch.

> [!NOTE]
> Wenn das technische Azure AD-Profil zum ersten Mal den Anspruch auf das Verzeichnis persistent speichert, prüft es, ob das benutzerdefinierte Attribut existiert. Falls nicht, wird das benutzerdefinierte Attribut erstellt.  

## <a name="create-a-custom-attribute-through-azure-portal"></a>Erstellen eines benutzerdefinierten Attributs im Azure-Portal

Von integrierten und benutzerdefinierten Profilen werden dieselben Erweiterungsattribute genutzt. Beim Hinzufügen benutzerdefinierter Attribute auf der Portaloberfläche werden diese Attribute mithilfe von der in jedem B2C-Mandanten vorhandenen **b2c-extensions-app** registriert.

Sie können diese Attribute auf der Portalbenutzeroberfläche erstellen, bevor Sie sie in Ihren benutzerdefinierten Richtlinien einsetzen. Wenn Sie im Portal das Attribut **loyaltyId** erstellen, müssen Sie wie folgt darauf verweisen:

|Name     |Verwendung in |
|---------|---------|
|`extension_loyaltyId`  | Benutzerdefinierte Richtlinie|
|`extension_<b2c-extensions-app-guid>_loyaltyId`  | [Microsoft Graph-API](microsoft-graph-operations.md)|

Das folgende Beispiel veranschaulicht die Verwendung benutzerdefinierter Attribute in einer benutzerdefinierten Azure AD B2C-Anspruchsdefinition für eine Richtlinie.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="extension_loyaltyId">
      <DisplayName>Loyalty Identification</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Your loyalty number from your membership card</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  </ClaimsSchema>
</BuildingBlocks>
```

Das folgende Beispiel veranschaulicht die Verwendung eines benutzerdefinierten Attributs in der benutzerdefinierten Azure AD B2C-Richtlinie in einem technischen Profil, einer Eingabe, einer Ausgabe und persistent gespeicherten Ansprüchen.

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="extension_loyaltyId"  />
</InputClaims>
<PersistedClaims>
  <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />
</PersistedClaims>
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
</OutputClaims>
```

::: zone-end

## <a name="using-custom-attribute-with-ms-graph-api"></a>Verwenden benutzerdefinierter Attribute mit der Microsoft Graph-API

Die Microsoft Graph-API unterstützt das Erstellen und Aktualisieren eines Benutzers mit Erweiterungsattributen. Erweiterungsattribute in der Microsoft Graph-API werden gemäß der Konvention `extension_ApplicationClientID_attributename` benannt, wobei `ApplicationClientID` die **Anwendungs-ID (Client)** der Anwendung `b2c-extensions-app` ist. Beachten Sie, dass die **Anwendungs-ID (Client)** in der Darstellung im Namen des Erweiterungsattributs keine Bindestriche enthält. Beispiel:

```json
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyId": "212342" 
``` 

## <a name="next-steps"></a>Nächste Schritte

Befolgen Sie die Anleitungen zum [Hinzufügen von Ansprüchen und Anpassen von Benutzereingaben mit benutzerdefinierten Richtlinien in Azure Active Directory B2C](configure-user-input.md). In diesem Beispiel wird der integrierte Anspruch „city“ verwendet. Um ein benutzerdefiniertes Attribut zu verwenden, ersetzen Sie „city“ durch Ihre eigenen benutzerdefinierten Attribute.
