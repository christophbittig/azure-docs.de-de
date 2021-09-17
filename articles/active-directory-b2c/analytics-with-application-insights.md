---
title: Nachverfolgen des Benutzerverhaltens mithilfe von Application Insights
titleSuffix: Azure AD B2C
description: Erfahren Sie, wie Sie Ereignisprotokolle in Application Insights von Azure AD B2C-User Journeys aus aktivieren.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 01/29/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a296babaafdee7939d462a356b9a3087e5e8990f
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122777774"
---
# <a name="track-user-behavior-in-azure-ad-b2c-by-using-application-insights"></a>Verfolgen des Benutzerverhaltens in Azure AD B2C mithilfe von Application Insights

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

In Azure Active Directory B2C (Azure AD B2C) können Sie Ereignisdaten direkt an [Application Insights](../azure-monitor/app/app-insights-overview.md) senden, indem Sie den für Azure AD B2C bereitgestellten Instrumentierungsschlüssel verwenden. Mit einem technischen Application Insights-Profil können Sie ausführliche und angepasste Ereignisprotokolle für Ihre User Journeys erhalten. Diese ermöglichen Folgendes:

- Gewinnen von Einblicken in Benutzerverhalten.
- Beheben von Problemen mit eigenen Richtlinien in der Entwicklung oder in der Produktion.
- Messen der Leistung.
- Erstellen von Benachrichtigungen von Application Insights.

## <a name="overview"></a>Übersicht

Wenn Sie benutzerdefinierte Ereignisprotokolle aktivieren möchten, fügen Sie ein technisches Application Insights-Profil hinzu. Im technischen Profil definieren Sie den Application Insights-Instrumentierungsschlüssel, den Ereignisnamen und die Ansprüche, die aufgezeichnet werden sollen. Um ein Ereignis zu senden, fügen Sie das technische Profil als Orchestrierungsschritt in einer [User Journey](userjourneys.md) hinzu.

Wenn Sie Application Insights verwenden, beachten Sie Folgendes:

- Es tritt eine kurze Verzögerung auf (in der Regel weniger als fünf Minuten), bevor neue Protokolle in Application Insights verfügbar sind.
- Sie können in Azure AD B2C auswählen, welche Ansprüche aufgezeichnet werden sollen. Beziehen Sie keine Ansprüche mit personenbezogenen Daten ein.
- Zum Aufzeichnen einer Benutzersitzung können Sie Ereignisse mithilfe einer Korrelations-ID vereinheitlichen.
- Rufen Sie das technische Application Insights-Profil direkt von einer [User Journey](userjourneys.md) oder [Sub Journey](subjourneys.md) aus auf. Verwenden Sie technische Application Insights-Profile nicht als [technisches Validierungsprofil](validation-technical-profile.md).

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="create-an-application-insights-resource"></a>Erstellen einer Application Insights-Ressource

Wenn Sie Application Insights mit Azure AD B2C verwenden, müssen Sie lediglich eine Ressource erstellen und den Instrumentierungsschlüssel abrufen. Informationen finden Sie unter [Erstellen einer Application Insights-Ressource](../azure-monitor/app/create-new-resource.md).

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das über Ihr Azure AD-Abonnement verfügt, und nicht Ihr Azure AD B2C-Verzeichnis. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse + Abonnements** das Azure AD-Verzeichnis, das über Ihr Abonnement verfügt, in der Liste **Verzeichnisname**, und wählen Sie dann **Wechseln** aus.
1. Wählen Sie links oben im Azure-Portal **Ressource erstellen** aus, suchen Sie **Application Insights**, und wählen Sie dann diese Option aus.
1. Wählen Sie **Erstellen** aus.
1. Geben Sie unter **Name** einen Namen für die Ressourcengruppe ein.
1. Wählen Sie als **Anwendungstyp** die Option **ASP.NET-Webanwendung** aus.
1. Wählen Sie als **Ressourcengruppe** eine vorhandene Gruppe aus, oder geben Sie den Namen für eine neue Gruppe ein.
1. Wählen Sie **Erstellen** aus.
1. Öffnen Sie die neue Application Insights-Ressource, erweitern Sie **Zusammenfassung**, und kopieren Sie den Instrumentierungsschlüssel.

![Screenshot des Instrumentierungsschlüssels auf der Registerkarte „Übersicht“ von Application Insights](./media/analytics-with-application-insights/app-insights.png)

## <a name="define-claims"></a>Definieren von Ansprüchen

Ein Anspruch bietet eine temporäre Speicherung von Daten während der Ausführung einer Azure AD B2C-Richtlinie. Ansprüche werden im [Element „ClaimsSchema“](claimsschema.md) deklariert.

1. Öffnen Sie die Erweiterungsdatei Ihrer Richtlinie. Der Dateiname lautet ähnlich wie `SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`** .
1. Suchen Sie nach dem Element [BuildingBlocks](buildingblocks.md). Wenn das Element nicht angezeigt wird, fügen Sie es hinzu.
1. Suchen Sie nach dem Element **ClaimsSchema**. Wenn das Element nicht angezeigt wird, fügen Sie es hinzu.
1. Fügen Sie dem Element **ClaimsSchema** die folgenden Ansprüche hinzu:

   ```xml
   <ClaimType Id="EventType">
     <DisplayName>Event type</DisplayName>
     <DataType>string</DataType>
   </ClaimType>
   <ClaimType Id="EventTimestamp">
     <DisplayName>Event timestamp</DisplayName>
     <DataType>string</DataType>
   </ClaimType>
   <ClaimType Id="PolicyId">
     <DisplayName>Policy Id</DisplayName>
     <DataType>string</DataType>
   </ClaimType>
   <ClaimType Id="Culture">
     <DisplayName>Culture ID</DisplayName>
     <DataType>string</DataType>
   </ClaimType>
   <ClaimType Id="CorrelationId">
     <DisplayName>Correlation Id</DisplayName>
     <DataType>string</DataType>
   </ClaimType>
   <ClaimType Id="federatedUser">
     <DisplayName>Federated user</DisplayName>
     <DataType>boolean</DataType>
   </ClaimType>
   <ClaimType Id="parsedDomain">
     <DisplayName>Domain name</DisplayName>
     <DataType>string</DataType>
     <UserHelpText>The domain portion of the email address.</UserHelpText>
   </ClaimType>
   <ClaimType Id="userInLocalDirectory">
     <DisplayName>userInLocalDirectory</DisplayName>
     <DataType>boolean</DataType>
   </ClaimType>
   ```

## <a name="add-new-technical-profiles"></a>Hinzufügen neuer technischer Profile

Technische Profile können in der benutzerdefinierten Richtlinie als Funktionen angesehen werden. Bei diesen Funktionen erfolgt die [Einbindung eines technischen Profils](technicalprofiles.md#include-technical-profile). Das bedeutet, dass ein technisches Profil ein weiteres technisches Profile enthält, wobei Einstellungen geändert werden oder neue Funktionalität hinzugefügt wird. In der folgenden Tabelle werden die technischen Profile definiert, die zum Öffnen einer Sitzung und Senden von Ereignissen verwendet werden.

| Technisches Profil | Aufgabe |
| ----------------- | -----|
| AppInsights-Common | Dies ist ein allgemeines technisches Profil mit typischer Konfiguration. Es enthält den Application Insights-Instrumentierungsschlüssel, eine Sammlung der aufzuzeichnenden Ansprüche und den Entwicklermodus. Die anderen technischen Profile enthalten das allgemeine technische Profil und fügen weitere Ansprüche hinzu, z. B. den Ereignisnamen. |
| AppInsights-SignInRequest | Zeichnet ein **SignInRequest**-Ereignis mit einem Satz von Ansprüchen auf, wenn eine Anmeldeanforderung empfangen wurde. |
| AppInsights-UserSignUp | Zeichnet ein **UserSignUp**-Ereignis auf, wenn der Benutzer die Registrierungsoption in einer Registrierungs- oder Anmeldungsjourney auslöst. |
| AppInsights-SignInComplete | Zeichnet ein **SignInComplete**-Ereignis bei erfolgreicher Authentifizierung auf, wenn ein Token an die Anwendung der vertrauenden Seite gesendet wurde. |

Öffnen Sie die Datei *TrustFrameworkExtensions.xml* aus dem Starter Pack. Fügen Sie dem Element **ClaimsProvider** die technischen Profile hinzu:

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AppInsights-Common">
      <DisplayName>Application Insights</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key, which you use for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <Item Key="DeveloperMode">false</Item>
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is the property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
        <InputClaim ClaimTypeReferenceId="EventTimestamp" PartnerClaimType="{property:EventTimestamp}" DefaultValue="{Context:DateTimeInUtc}" />
        <InputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="{property:TenantId}" DefaultValue="{Policy:TrustFrameworkTenantId}" />
        <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-SignInRequest">
      <InputClaims>
        <!-- An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider to create an event with the specified value. -->
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-UserSignUp">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignUp" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
    
    <TechnicalProfile Id="AppInsights-SignInComplete">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
        <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
        <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
        <InputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="{property:IDP}" DefaultValue="Local" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

> [!IMPORTANT]
> Ändern Sie den Instrumentierungsschlüssel im technischen Profil `AppInsights-Common` in die GUID, die von Ihrer Application Insights-Ressource bereitgestellt wurde.

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>Hinzufügen der technischen Profil als Orchestrierungsschritte

Fügen Sie neue Orchestrierungsschritte hinzu, die auf die technischen Profile verweisen.

> [!IMPORTANT]
> Nummerieren Sie nach dem Hinzufügen der neuen Orchestrierungsschritte die Schritte nacheinander von 1 bis N neu, ohne einen Integer zu überspringen.

1. Rufen Sie `AppInsights-SignInRequest` als zweiten Orchestrierungsschritt auf. In diesem Schritt wird nachverfolgt, dass eine Registrierungs- oder Anmeldeanforderung empfangen wurde.

   ```xml
   <!-- Track that we have received a sign in request -->
   <OrchestrationStep Order="2" Type="ClaimsExchange">
     <ClaimsExchanges>
       <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AppInsights-SignInRequest" />
     </ClaimsExchanges>
   </OrchestrationStep>
   ```

1. Fügen Sie vor dem Orchestrierungsschritt `SendClaims` einen neuen Schritt hinzu, der `AppInsights-UserSignup` aufruft. Der Schritt wird ausgelöst, wenn der Benutzer die Registrierungsschaltfläche in einer Registrierungs- oder Anmeldungsjourney auswählt.

   ```xml
   <!-- Handles the user selecting the sign-up link in the local account sign-in page -->
   <OrchestrationStep Order="8" Type="ClaimsExchange">
     <Preconditions>
       <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
         <Value>newUser</Value>
         <Action>SkipThisOrchestrationStep</Action>
       </Precondition>
       <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
         <Value>newUser</Value>
         <Value>false</Value>
         <Action>SkipThisOrchestrationStep</Action>
       </Precondition>
     </Preconditions>
     <ClaimsExchanges>
       <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AppInsights-UserSignup" />
     </ClaimsExchanges>
   </OrchestrationStep>
   ```

1. Rufen Sie nach dem Orchestrierungsschritt `SendClaims` den Schritt `AppInsights-SignInComplete` auf. Dieser Schritt zeigt eine erfolgreich abgeschlossene Journey an.

   ```xml
   <!-- Track that we have successfully sent a token -->
   <OrchestrationStep Order="10" Type="ClaimsExchange">
     <ClaimsExchanges>
       <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AppInsights-SignInComplete" />
     </ClaimsExchanges>
   </OrchestrationStep>
   ```

## <a name="upload-your-file-run-the-policy-and-view-events"></a>Hochladen Ihrer Datei, Ausführen der Richtlinie und Anzeigen von Ereignissen

Speichern Sie die Datei *TrustFrameworkExtensions.xml*, und laden Sie sie hoch. Rufen Sie dann die Richtlinie für die vertrauende Seite Ihrer Anwendung ab, oder verwenden Sie **Jetzt ausführen** im Azure-Portal. Warten Sie, bis die Ereignisse in Application Insights verfügbar sind.

1. Öffnen Sie die **Application Insights**-Ressource in Ihrem Azure Active Directory-Mandanten.
1. Wählen Sie **Verwendung** und dann **Ereignisse** aus.
1. Legen Sie **Während** auf **letzte Stunde** und **Für** auf **3 Minuten** fest. Möglicherweise müssen Sie das Fenster aktualisieren, damit die Ergebnisse angezeigt werden.

![Screenshot der Ereignisstatistiken in Application Insights](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="collect-more-data"></a>Erfassen weiterer Daten

Um Ihre geschäftlichen Anforderungen zu erfüllen, können Sie weitere Ansprüche aufzeichnen. Um einen Anspruch hinzuzufügen, müssen Sie zuerst [einen Anspruch definieren](#define-claims) und dann der Eingabeansprüchesammlung hinzufügen. Ansprüche, die Sie dem technischen **AppInsights-Common**-Profil hinzufügen, werden in allen Ereignissen angezeigt. Ansprüche, die Sie einem bestimmten technischen Profil hinzufügen, werden nur in diesem Ereignis angezeigt. Das Eingabeanspruchselement enthält die folgenden Attribute:

- **ClaimTypeReferenceId** ist der Verweis auf einen Anspruchstyp.
- **PartnerClaimType** ist der Name der Eigenschaft, die in Azure Insights angezeigt wird. Verwenden Sie die Syntax `{property:NAME}`, wobei `NAME` eine Eigenschaft ist, die dem Ereignis hinzugefügt wird.
- **DefaultValue** ist ein vordefinierter Wert, der aufgezeichnet werden soll, z. B. ein Ereignisname. Wenn ein in der User Journey verwendeter Anspruch nicht aufgeführt wird, wird der Standardwert verwendet. Beispielsweise wird der Anspruch `identityProvider` durch die technischen Profile des Verbunds festgelegt, z. B. Facebook. Wenn der Anspruch nicht aufgeführt wird, bedeutet dies, dass sich der Benutzer mit einem lokalen Konto angemeldet hat. Folglich ist dieser Wert standardmäßig auf **Lokal** festgelegt. Sie können auch einen [Anspruchskonfliktlöser](claim-resolver-overview.md) mit einem Kontextwert aufzeichnen, z. B. mit der Anwendungs-ID oder Benutzer-IP-Adresse.

### <a name="manipulate-claims"></a>Ändern von Ansprüchen

Sie können mit [Eingabeanspruchstransformationen](custom-policy-overview.md#manipulating-your-claims) die Eingabeansprüche ändern oder neue generieren, bevor Sie sie an Application Insights senden. Im folgenden Beispiel enthält das technische Profil die Eingabeanspruchstransformation `CheckIsAdmin`.

```xml
<TechnicalProfile Id="AppInsights-SignInComplete">
  <InputClaimsTransformations>  
    <InputClaimsTransformation ReferenceId="CheckIsAdmin" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isAdmin" PartnerClaimType="{property:IsAdmin}"  />
    ...
  </InputClaims>
  <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
</TechnicalProfile>
```

### <a name="add-events"></a>Ereignisse hinzufügen

Um ein Ereignis hinzuzufügen, erstellen Sie ein neues technisches Profil, das das technische `AppInsights-Common`-Profil enthält. Fügen Sie dann der [User Journey](custom-policy-overview.md#orchestration-steps) das technische Profil als Orchestrierungsschritt hinzu. Verwenden Sie das Element [Precondition](userjourneys.md#preconditions), um das Ereignis zu initiieren, wenn Sie bereit sind. Lassen Sie das Ereignis beispielsweise nur melden, wenn Benutzer die mehrstufige Authentifizierung durchlaufen.

```xml
<TechnicalProfile Id="AppInsights-MFA-Completed">
  <InputClaims>
     <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="MFA-Completed" />
  </InputClaims>
  <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
</TechnicalProfile>
```

>[!Important]
>Wenn Sie der User Journey ein Ereignis hinzufügen, müssen Sie die fortlaufende Nummerierung der Orchestrierungsschritte ändern.

```xml
<OrchestrationStep Order="8" Type="ClaimsExchange">
  <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
    <Value>isActiveMFASession</Value>
    <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackUserMfaCompleted" TechnicalProfileReferenceId="AppInsights-MFA-Completed" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="enable-developer-mode"></a>Aktivieren des Entwicklermodus

Wenn Sie Application Insights zum Definieren von Ereignissen verwenden, können Sie angeben, ob der Entwicklermodus aktiviert ist. Der Entwicklermodus steuert die Pufferung von Ereignissen. In einer Entwicklungsumgebung mit minimalem Ereignisaufkommen führt die Aktivierung des Entwicklermodus dazu, dass Ereignisse sofort an Application Insights gesendet werden. Standardwert: `false`. Aktivieren Sie den Entwicklermodus nicht in Produktionsumgebungen.

Um den Entwicklermodus zu aktivieren, ändern Sie im technischen `AppInsights-Common`-Profil die `DeveloperMode`-Metadaten in `true`:

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DeveloperMode">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="disable-telemetry"></a>Telemetrie deaktivieren

Um die Application Insights-Protokolle zu deaktivieren, ändern Sie im technischen `AppInsights-Common`-Profil die `DisableTelemetry`-Metadaten in `true`:

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DisableTelemetry">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [benutzerdefinierte KPI-Dashboards mithilfe von Azure Application Insights](../azure-monitor/app/tutorial-app-dashboards.md) erstellen.

::: zone-end