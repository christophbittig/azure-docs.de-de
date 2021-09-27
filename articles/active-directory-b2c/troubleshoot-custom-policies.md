---
title: Beheben von Problemen mit benutzerdefinierten Richtlinien in Azure Active Directory B2C
description: Hier finden Sie Informationen zu den Ansätzen der Fehlerbehebung bei Verwendung von benutzerdefinierten Richtlinien in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 05/25/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0a340096875669acc63cef5f797fff8b5919b8bd
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/04/2021
ms.locfileid: "123478858"
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies"></a>Beheben von Problemen mit benutzerdefinierten Richtlinien in Azure AD B2C

Wenn Sie in Azure Active Directory B2C (Azure AD B2C) [benutzerdefinierte Richtlinien](custom-policy-overview.md) verwenden, können Probleme mit der XML-Schemasprache für die Richtlinien oder Laufzeitprobleme auftreten. In diesem Artikel werden einige Tools und Tipps beschrieben, die Ihnen beim Ermitteln und Lösen von Problemen behilflich sein können. 

Der Schwerpunkt des Artikels liegt auf der Problembehandlung für die Konfiguration von benutzerdefinierten Azure AD B2C-Richtlinien. Die Anwendung der vertrauenden Seite und die dazugehörige Identitätsbibliothek werden nicht behandelt.

## <a name="azure-ad-b2c-correlation-id-overview"></a>Übersicht über die Korrelations-ID von Azure AD B2C

Die Korrelations-ID von Azure AD B2C ist ein eindeutiger Bezeichnerwert, der Autorisierungsanforderungen angefügt wird. Dieser Wert wird an alle Orchestrierungsschritte übergeben, die ein Benutzer durchlaufen muss. Mit der Korrelations-ID haben Sie folgende Möglichkeiten:

- Identifizieren Sie die Anmeldeaktivität in Ihrer Anwendung, und verfolgen Sie die Leistungsfähigkeit Ihrer Richtlinie.
- Suchen Sie nach den Azure Application Insights-Protokollen für die Anmeldeanforderungen.
- Übergeben Sie die Korrelations-ID an Ihre REST-API, und identifizieren Sie mit ihrer Hilfe den Anmeldeflow. 

Die Korrelations-ID ändert sich jedes Mal, wenn eine neue Sitzung eingerichtet wird. Achten Sie beim Debuggen Ihrer Richtlinien darauf, dass Sie vorhandene Browserregisterkarten schließen oder einen neuen Browser im privaten Modus öffnen.

### <a name="get-the-azure-ad-b2c-correlation-id"></a>Abrufen der Korrelations-ID von Azure AD B2C

Sie finden die Korrelations-ID auf der Azure AD B2C-Registrierungs- oder Anmeldeseite. Wählen Sie in Ihrem Browser **Quelle anzeigen** aus. Die Korrelations-ID wird oben auf der Seite als Kommentar angezeigt.

![Screenshot: Azure AD B2C-Anmeldeseite mit der Option „Quelle anzeigen“.](./media/troubleshoot-custom-policies/find-azure-ad-b2c-correlation-id.png)

Kopieren Sie die Korrelations-ID, und setzen Sie dann den Anmeldeflow fort. Verwenden Sie die Korrelations-ID, um das Anmeldeverhalten zu beobachten. Weitere Informationen finden Sie unter [Problembehandlung mit Application Insights](#troubleshooting-with-application-insights).

### <a name="echo-the-azure-ad-b2c-correlation-id"></a>Echoanforderung der Korrelations-ID von Azure AD B2C

Sie können die Korrelations-ID in Ihre Azure AD B2C-Token einbeziehen. Gehen Sie zum Einbeziehen der Korrelations-ID wie folgt vor:

1. Öffnen Sie die Erweiterungsdatei Ihrer Richtlinie. Beispiel: <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.
1. Suchen Sie nach dem Element [BuildingBlocks](buildingblocks.md). Wenn das Element nicht vorhanden ist, fügen Sie es hinzu.
1. Suchen Sie nach dem Element [ClaimsSchema](claimsschema.md). Wenn das Element nicht vorhanden ist, fügen Sie es hinzu.
1. Fügen Sie den Korrelations-ID-Anspruch dem **ClaimsSchema**-Element hinzu.  

    ```xml
    <!-- 
    <BuildingBlocks>
      <ClaimsSchema> -->
        <ClaimType Id="correlationId">
          <DisplayName>correlation ID</DisplayName>
          <DataType>string</DataType>
        </ClaimType>
      <!-- 
      </ClaimsSchema>
    </BuildingBlocks>-->
    ```

1. Öffnen Sie die Datei der vertrauenden Seite Ihrer Richtlinie. Beispielsweise die Datei <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em>. Der Ausgabeanspruch wird nach einer erfolgreichen User Journey in das Token eingefügt und an die Anwendung gesendet. Ändern Sie das Element „Technisches Profil“ im Abschnitt „vertrauende Seite“, um `correlationId` als eine Ausgabeanspruch hinzuzufügen.
 
    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
          ...
          <OutputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```


## <a name="troubleshooting-with-application-insights"></a>Problembehandlung mit Application Insights

Diagnostizieren Sie mit [Application Insights](troubleshoot-with-application-insights.md) Probleme mit benutzerdefinierten Richtlinien. Application Insights verfolgt die Aktivitäten der User Journey für Ihre benutzerdefinierten Richtlinien. Das Tool bietet die Möglichkeit, Ausnahmen zu diagnostizieren und den Austausch von Ansprüchen zwischen Azure AD B2C und den verschiedenen, durch technische Profile definierten Anspruchsanbietern (z. B. Identitätsanbieter, API-basierte Dienste, Azure AD B2C-Benutzerverzeichnis und andere Dienste), zu beobachten.  

Es wird empfohlen, die [Azure AD B2C-Erweiterung](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) für [VS Code](https://code.visualstudio.com/) zu installieren. Mit der Azure AD B2C-Erweiterung werden die Protokolle nach Richtlinienname, Korrelations-ID (Application Insights zeigt die erste Ziffer der Korrelations-ID) und Protokollzeitstempel organisiert. Dieses Feature hilft Ihnen, anhand des lokalen Zeitstempels das entsprechende Protokoll zu finden und die von Azure AD B2C ausgeführte User Journey anzuzeigen. 

> [!NOTE]
> - Es gibt eine kurze Verzögerung (in der Regel weniger als fünf Minuten), bevor Sie neue Protokolle in Application Insights anzeigen können.
> - In der Community wurde für Azure AD B2C die Visual Studio Code-Erweiterung entwickelt, um Identitätsentwickler zu unterstützen. Die Erweiterung wird nicht von Microsoft unterstützt und wird grundsätzlich im Ist-Zustand zur Verfügung gestellt.

Ein einzelner Anmeldeflow kann mehr als eine Azure Application Insights-Ablaufverfolgung ausgeben. Im folgenden Screenshot enthält die Richtlinie *B2C_1A_signup_signin* drei Protokolle. Jedes Protokoll stellt einen Teil des Anmeldeflows dar.

Der folgende Screenshot enthält die Azure AD B2C-Erweiterung für VS Code mit dem Ablaufverfolgungs-Explorer von Azure Application Insights.

![Screenshot: Azure AD B2C-Erweiterung für VS Code mit der Azure Application Insights-Ablaufverfolgung.](./media/troubleshoot-custom-policies/vscode-extension-application-insights-trace.png)

### <a name="filter-the-trace-log"></a>Filtern des Ablaufverfolgungsprotokolls

Beginnen Sie im Ablaufverfolgungs-Explorer von Azure AD B2C mit der Eingabe der ersten Ziffer der Korrelations-ID, oder geben Sie eine Zeit ein, nach der Sie suchen möchten. Oben rechts im Azure AD B2C-Ablaufverfolgungs-Explorer wird ein Filterfeld mit der bisherigen Eingabe angezeigt. Übereinstimmende Ablaufverfolgungsprotokolle werden hervorgehoben.  

![Screenshot: Azure AD B2C-Erweiterung mit Filter für Azure AD B2C-Ablaufverfolgungs-Explorer und Hervorhebungen.](./media/troubleshoot-custom-policies/vscode-extension-application-insights-highlight.png)

Wenn Sie auf das Filterfeld zeigen und **Filter für Typ aktivieren** auswählen, werden nur übereinstimmende Ablaufverfolgungsprotokolle angezeigt. Mit der Schaltfläche **X (Löschen)** können Sie den Filter entfernen.

![Screenshot: Azure AD B2C-Erweiterung mit Filter für Azure AD B2C-Ablaufverfolgungs-Explorer.](./media/troubleshoot-custom-policies/vscode-extension-application-insights-filter.png)

### <a name="application-insights-trace-log-details"></a>Details des Application Insight-Ablaufverfolgungsprotokolls

Wenn Sie eine Azure Application Insights-Ablaufverfolgung auswählen, öffnet die Erweiterung das Fenster **Application Insights-Details** mit den folgenden Informationen:

- **Application Insights**: Allgemeine Informationen zum Ablaufverfolgungsprotokoll, einschließlich Richtlinienname, Korrelations-ID, Azure Application Insights-Ablaufverfolgungs-ID und Ablaufverfolgungszeitstempel.
- **Technische Profile**: Liste der technischen Profile, die im Ablaufverfolgungsprotokoll angezeigt werden.
- **Ansprüche**: Alphabetische Liste der Ansprüche mit den entsprechenden Werten, die im Ablaufverfolgungsprotokoll angezeigt werden. Wenn ein Anspruch mehrmals mit unterschiedlichen Werten im Ablaufverfolgungsprotokoll angezeigt wird, wird der neueste Wert durch das Vorzeichen `=>` kenntlich gemacht. Sie können diese Ansprüche überprüfen, um zu ermitteln, ob die erwarteten Anspruchswerte richtig festgelegt sind. Wenn Sie z. B. anhand einer Vorbedingung einen Anspruchswert überprüfen, können Sie im Abschnitt „Ansprüche“ ermitteln, warum sich ein Flow anders als erwartet verhält.
- **Anspruchstransformation**: Liste der Anspruchstransformationen, die im Ablaufverfolgungsprotokoll angezeigt werden. Jede Anspruchstransformation enthält die Eingabeansprüche, Eingabeparameter und Ausgabeansprüche. Der Abschnitt „Anspruchstransformation“ bietet eine Übersicht über die eingegangenen Daten und das Ergebnis der Anspruchstransformation.
- **Token**: Liste der Token, die im Ablaufverfolgungsprotokoll angezeigt werden. Zu den Token zählen das zugrunde liegende OAuth-Token des Verbundidentitätsanbieters und das Token des OpenId Connect-Identitätsanbieters. Das Token des Verbundidentitätsanbieters enthält Details zur Rückgabe der Ansprüche an Azure AD B2C durch den Identitätsanbieter, damit Sie die Ausgabeansprüche dem technischen Profil des Identitätsanbieters zuordnen können. 
- **Ausnahmen**: Liste der Ausnahmen oder schwerwiegenden Fehler, die im Ablaufverfolgungsprotokoll angezeigt werden.
- **JSON-Datei von Application Insights**: Die Rohdaten, die von Application Insights zurückgegeben werden.

Der folgende Screenshot enthält ein Beispielfenster mit Details des Application Insights-Ablaufverfolgungsprotokolls.  

![Screenshot: Azure AD B2C-Erweiterung mit Azure AD B2C-Ablaufverfolgungsbericht.](./media/troubleshoot-custom-policies/vscode-extension-application-insights-report.png)

## <a name="troubleshoot-jwt-tokens"></a>Behandeln von Problemen mit JWT-Token

Zu Prüfungs- und Debugzwecken können Entwickler JWT-Token mit einer Website wie [https://jwt.ms](https://jwt.ms) decodieren. Erstellen Sie eine Testanwendung, die Sie zur Tokenüberprüfung an `https://jwt.ms` weiterleiten kann. Falls noch nicht geschehen, führen Sie die Schritte zum [Registrieren einer Webanwendung](tutorial-register-applications.md) und [Aktivieren der impliziten Genehmigung von ID-Token](tutorial-register-applications.md#enable-id-token-implicit-grant) aus. 

![Screenshot: Vorschau des JWT-Tokens.](./media/troubleshoot-custom-policies/jwt-token-preview.png)

Verwenden Sie **Jetzt ausführen** und `https://jwt.ms`, um Ihre Richtlinien unabhängig von Ihrer Webanwendung oder mobilen Anwendung zu testen. Diese Website verhält sich wie eine Anwendung der vertrauenden Seite. Es werden die Inhalte des von Ihrer Azure AD B2C-Richtlinie generierten JSON-Webtokens (JWT) angezeigt.

## <a name="troubleshoot-saml-protocol"></a>Behandeln von Problemen mit dem SAML-Protokoll

Zum Konfigurieren und Debuggen der Integration mit Ihrem Dienstanbieter können Sie eine Browsererweiterung für das SAML-Protokoll verwenden, z. B. die [SAML-DevTools-Erweiterung](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio) für Chrome, [SAML-tracer](https://addons.mozilla.org/es/firefox/addon/saml-tracer/) für Firefox oder [Entwicklertools für Edge oder IE](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957).

Der folgende Screenshot enthält die SAML DevTools-Erweiterung mit der Darstellung der von Azure AD B2C an den Identitätsanbieter gesendeten SAML-Anforderung sowie die SAML-Antwort.

![Screenshot: Ablaufverfolgungsprotokoll für das SAML-Protokoll.](./media/troubleshoot-custom-policies/saml-protocol-trace.png)

Mit diesen Tools können Sie die Integration zwischen Ihrer Anwendung und Azure AD B2C überprüfen. Beispiel:

- Überprüfen Sie, ob die SAML-Anforderung eine Signatur enthält, und bestimmen Sie, welcher Algorithmus zum Anmelden für die Autorisierungsanforderung verwendet wird.
- Überprüfen Sie, ob Azure AD B2C eine Fehlermeldung zurückgibt.
- Überprüfen Sie, ob der Assertionsabschnitt verschlüsselt ist.
- Rufen Sie den Namen der Ansprüche ab, die vom Identitätsanbieter zurückgegeben werden.

Sie können auch mit [Fiddler](https://www.telerik.com/fiddler) den Austausch von Nachrichten zwischen Ihrem Clientbrowser und Azure AD B2C nachverfolgen. So erhalten Sie Hinweise dazu, an welcher Stelle für Ihre User Journey in den Orchestrierungsschritten ein Fehler auftritt.

## <a name="troubleshoot-policy-validity"></a>Behandeln von Problemen mit der Gültigkeit von Richtlinien

Nachdem Sie die Entwicklung Ihrer Richtlinie abgeschlossen haben, müssen Sie die Richtlinie in Azure AD B2C hochladen. Möglicherweise treten Probleme mit Ihrer Richtlinie auf. Stellen Sie mithilfe folgender Methoden die Integrität/Gültigkeit Ihrer Richtlinie sicher.

Der häufigste Fehler beim Einrichten von benutzerdefinierten Richtlinien ist falsch formatierter XML-Code. Es ist praktisch unerlässlich, einen guten XML-Editor zu verwenden. Dieser verfügt über folgende Funktionen: natives Anzeigen von XML-Code, farbiges Codieren von Inhalt, Vorabeinfügen von häufig verwendeten Ausdrücken, Beibehalten der Indizierung von XML-Elementen und Überprüfen per XML-Schema.

Wir empfehlen die Verwendung von [Visual Studio Code](https://code.visualstudio.com/). Installieren Sie dann eine XML-Erweiterung, z. B. [Red Hat XML Language Support](https://marketplace.visualstudio.com/items?itemName=redhat.vscode-xml). Mit der XML-Erweiterung können Sie das XML-Schema überprüfen, bevor Sie Ihre XML-Datei hochladen, indem Sie die [XSD](https://raw.githubusercontent.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/master/TrustFrameworkPolicy_0.3.0.0.xsd)-Schemadefinition der benutzerdefinierten Richtlinie verwenden.

Mit der XML-Dateizuordnungsstrategie können Sie die XML-Datei an die XSD-Definition binden, indem Sie Ihrer VS Code-Datei `settings.json` die folgenden Einstellungen hinzufügen. Dazu gehen Sie wie folgt vor:

1. Klicken Sie in VS Code auf **Einstellungen**. Weitere Informationen finden Sie unter [Benutzer- und Arbeitsbereichseinstellungen](https://code.visualstudio.com/docs/getstarted/settings).
1. Suchen Sie nach **fileAssociations**, und wählen Sie dann unter **Erweiterung** die Option **XML** aus.
1. Wählen Sie **In settings.json bearbeiten** aus.

    ![Screenshot: XML-Schemaüberprüfung in VS Code.](./media/troubleshoot-custom-policies/xml-validation.png)
1. Fügen Sie in der Datei „settings.json“ den folgenden JSON-Code hinzu:

    ```json
    "xml.fileAssociations": [
      {
        "pattern": "**.xml",
        "systemId": "https://raw.githubusercontent.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/master/TrustFrameworkPolicy_0.3.0.0.xsd"
      }
    ]
    ```

Das folgende Beispiel enthält einen XML-Validierungsfehler. Wenn Sie mit dem Mauszeiger auf den Elementnamen zeigen, werden die erwarteten Elemente in der Erweiterung aufgelistet.

![Screenshot: Fehlerindikator bei der XML-Schemavalidierung in VS Code.](./media/troubleshoot-custom-policies/xml-validation-error.png)

Im folgenden Fall ist das Element `DisplayName` richtig, aber die Reihenfolge ist falsch. `DisplayName` muss sich vor dem Element `Protocol` befinden. Um das Problem zu beheben, zeigen Sie mit dem Mauszeiger auf das Element `DisplayName`, um die Reihenfolge der Elemente zu korrigieren.

![Screenshot: Reihenfolgefehler bei der XML-Schemavalidierung in VS Code.](./media/troubleshoot-custom-policies/xml-validation-order-error.png)

## <a name="upload-policies-and-policy-validation"></a>Hochladen von Richtlinien und Richtlinienvalidierung

Die Überprüfung der XML-Richtliniendatei erfolgt automatisch beim Hochladen. Die meisten Fehler haben zur Folge, dass der Upload fehlschlägt. Die Validierung enthält die Richtliniendatei, die Sie hochladen. Außerdem enthält sie die Kette der Dateien, auf die sich die Uploaddatei bezieht (Datei mit den Richtlinien der vertrauenden Seite, Datei mit Erweiterungen und Basisdatei).

> [!TIP]
> Azure AD B2C führt eine zusätzliche Validierung für die Richtlinie für die vertrauende Seite aus. Wenn ein Problem mit Ihrer Richtlinie auftritt, hat es sich bewährt, auch die Richtlinie für die vertrauende Seite hochzuladen, selbst wenn Sie nur die Erweiterungsrichtlinie bearbeiten. 

Dieser Abschnitt enthält die allgemeinen Validierungsfehler und mögliche Lösungen.

### <a name="schema-validation-error-found-has-invalid-child-element-name"></a>Schemavalidierungsfehler gefunden ... weist ein ungültiges untergeordnetes Element '{Name}' auf

Ihre Richtlinie enthält ein ungültiges XML-Element, oder das XML-Element ist zwar gültig, aber anscheinend in der falschen Reihenfolge angegeben. Informationen zum Beheben dieser Art von Fehler finden Sie im Abschnitt [Behandeln von Problemen mit der Gültigkeit von Richtlinien](#troubleshoot-policy-validity).

### <a name="there-is-a-duplicate-key-sequence-number"></a>Es gibt eine doppelte Schlüsselsequenz '{Zahl}' 

Eine [User Journey](userjourneys.md) oder [Sub Journey](subjourneys.md) besteht aus einer sortierten Liste von Orchestrierungsschritten, die nacheinander ausgeführt werden. Wenn Sie Ihre Journey geändert haben, müssen Sie die Schritte nacheinander von 1 bis n neu nummerieren, ohne eine Ganzzahl zu überspringen.

> [!TIP]
> Mit dem Befehl `(Shift+Ctrl+r)` der [Azure AD B2C-Erweiterung](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) für [VS Code](https://code.visualstudio.com/) können Sie alle User Journey- und Sub Journey-Orchestrierungsschritte in Ihrer Richtlinie neu nummerieren.

### <a name="was-expected-to-have-step-with-order-number-but-it-was-not-found"></a>... in der Reihenfolge wurde der Schritt '{Zahl}' erwartet, aber nicht gefunden...

Lesen Sie hierzu die Beschreibung zum vorherigen Fehler.

### <a name="orchestration-step-order-number-in-user-journey-name-is-followed-by-a-claims-provider-selection-step-and-must-be-a-claims-exchange-but-it-is-of-type"></a>Auf den Orchestrierungsschritt '{Zahl}' in User Journey '{Name}' ... folgt ein Schritt zur Auswahl des Anspruchsanbieters, der den Typ „ClaimsExchange“ aufweisen muss. Der Typ ist aber ...

Die Orchestrierungsschritte vom Typ `ClaimsProviderSelection` und `CombinedSignInAndSignUp` enthalten eine Liste der Optionen, die für einen Benutzer zur Auswahl stehen. Darauf muss der Typ `ClaimsExchange` mit mindestens einem Anspruchsaustausch folgen.

Die folgenden Orchestrierungsschritte verursachen diese Art von Fehler. Der zweite Orchestrierungsschritt muss den Typ `ClaimsExchange` und nicht den Typ `ClaimsProviderSelection` aufweisen.

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      <OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
        <ClaimsProviderSelections>
          <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange"/>
          <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange"/>
        </ClaimsProviderSelections>
        <ClaimsExchanges>
          <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email"/>
        </ClaimsExchanges>
      </OrchestrationStep> 
      
      <OrchestrationStep Order="2" Type="ClaimsProviderSelection">
        ...
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

### <a name="step-number-with-2-claims-exchanges-it-must-be-preceded-by-a-claims-provider-selection-in-order-to-determine-which-claims-exchange-can-be-used"></a>... Schritt {Zahl} mit 2 Anspruchsaustauschvorgängen. Diesem Schritt muss ein Schritt für die Auswahl des Anspruchsanbieters vorausgehen, um festzulegen, welcher Anspruchsaustausch verwendet werden kann

Ein Orchestrierungsschritt vom Typ `ClaimsExchange` darf nur einen `ClaimsExchange` enthalten, es sei denn, der vorherige Schritt weist den Typ `ClaimsProviderSelection` oder `CombinedSignInAndSignUp` auf. Die folgenden Orchestrierungsschritte verursachen diese Art von Fehler. Der sechste Schritt enthält zwei Anspruchsaustauschvorgänge. 

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="5" Type="ClaimsExchange">
        ...
        <ClaimsExchanges>
          <ClaimsExchange Id="SelfAsserted-Social" TechnicalProfileReferenceId="SelfAsserted-Social"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="6" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-First-API" TechnicalProfileReferenceId="Call-REST-First-API"/>
          <ClaimsExchange Id="Call-REST-Second-API" TechnicalProfileReferenceId="Call-REST-Second-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

Verwenden Sie zwei Orchestrierungsschritte, um diese Art von Fehler zu beheben. Jeder Orchestrierungsschritt enthält einen Anspruchsaustausch.

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="5" Type="ClaimsExchange">
        ...
        <ClaimsExchanges>
          <ClaimsExchange Id="SelfAsserted-Social" TechnicalProfileReferenceId="SelfAsserted-Social"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="6" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-First-API" TechnicalProfileReferenceId="Call-REST-First-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="7" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-Second-API" TechnicalProfileReferenceId="Call-REST-Second-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

### <a name="there-is-a-duplicate-key-sequence-name"></a>Es gibt eine doppelte Schlüsselsequenz '{Name}'

Eine Journey weist mehr als einen `ClaimsExchange` mit derselben `Id` auf. Die folgenden Schritte verursachen diese Art von Fehler. Die ID *AADUserWrite* wird in der User Journey zweimal angezeigt.

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="7" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="AADUserWrite" TechnicalProfileReferenceId="AAD-UserWriteUsingAlternativeSecurityId"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="8" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="AADUserWrite" TechnicalProfileReferenceId="Call-REST-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

Um diese Art von Fehler zu beheben, ändern Sie den Anspruchsaustausch im achten Orchestrierungsschritt in einen eindeutigen Namen, z. B. *Call-REST-API*.

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="7" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="AADUserWrite" TechnicalProfileReferenceId="AAD-UserWriteUsingAlternativeSecurityId"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="8" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-API" TechnicalProfileReferenceId="Call-REST-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

### <a name="makes-a-reference-to-claimtype-with-id-claim-name-but-neither-the-policy-nor-any-of-its-base-policies-contain-such-an-element"></a>... verweist auf ClaimType mit ID '{Anspruchsname}', aber weder die Richtlinie noch eine der Basisrichtlinien enthalten ein solches Element

Diese Art von Fehler tritt auf, wenn Ihre Richtlinie auf einen Anspruch verweist, der nicht im [Anspruchsschema](claimsschema.md) deklariert ist. Ansprüche müssen in mindestens einer Datei der Richtlinie definiert sein. 

Beispiel: technisches Profil mit dem Ausgabeanspruch *schoolId*. Der Ausgabeanspruch *schoolId* wurde jedoch nie in der Richtlinie oder in einer Vorgängerrichtlinie deklariert.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="schoolId" />
  ...
</OutputClaims>
```

Um diese Art von Fehler zu beheben, überprüfen Sie, ob der Wert `ClaimTypeReferenceId` möglicherweise falsch geschrieben wurde oder nicht im Schema vorhanden ist. Wenn der Anspruch in der Erweiterungsrichtlinie definiert ist, aber auch in der Basisrichtlinie verwendet wird, Stellen Sie sicher, dass der Anspruch in der Richtlinie, in der er verwendet wird, oder in einer Richtlinie der oberen Ebene definiert ist.

Durch das Hinzufügen des Anspruchs zum Anspruchsschema wird diese Art von Fehler behoben.

```xml
<!--
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="schoolId">
      <DisplayName>School name</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Enter your school name</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  <!-- 
  </ClaimsSchema>
</BuildingBlocks> -->
```

### <a name="makes-a-reference-to-a-claimstransformation-with-id"></a>... verweist auf eine Anspruchstransformation mit ID...

Dieser Fehler hat eine ähnliche Ursache wie der Anspruchsfehler. Lesen Sie hierzu die Beschreibung zum vorherigen Fehler.

### <a name="user-is-currently-logged-as-a-user-of-yourtenantonmicrosoftcom-tenant"></a>Der Benutzer ist derzeit als Benutzer des Mandanten 'yourtenant.onmicrosoft.com' angemeldet...

Sie haben sich mit einem Konto von einem Mandanten angemeldet, der nicht dem Mandanten in der Richtlinie entspricht, die Sie hochladen möchten. Beispiel: Sie haben sich mit admin@contoso.onmicrosoft.com angemeldet, während die `TenantId` in Ihrer Richtlinie auf `fabrikam.onmicrosoft.com` festgelegt ist.

```xml
<TrustFrameworkPolicy ...
  TenantId="fabrikam.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://fabrikam.onmicrosoft.com/B2C_1A_signup_signin">

  <BasePolicy>
    <TenantId>fabrikam.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  ...
</TrustFrameworkPolicy>
```

- Überprüfen Sie, ob der Wert für `TenantId` in den Elementen `<TrustFrameworkPolicy\>` und `<BasePolicy\>` mit Ihrem Azure AD B2C-Zielmandanten übereinstimmt.

### <a name="claim-type-name-is-the-output-claim-of-the-relying-partys-technical-profile-but-it-is-not-an-output-claim-in-any-of-the-steps-of-user-journey"></a>Der Anspruchstyp '{Name}' ist der Ausgabeanspruch des technischen Profils der vertrauenden Seite, aber kein Ausgabeanspruch in einem der Schritte der User Journey...

In einer Richtlinie für die vertrauende Seite haben Sie einen Ausgabeanspruch hinzugefügt, aber dieser Ausgabeanspruch ist in keinem der Schritte der User Journey vorhanden. Azure AD B2C kann den Anspruchswert aus dem Anspruchsbehälter nicht lesen.

Im folgenden Beispiel ist der Anspruch *schoolId* ein Ausgabeanspruch des technischen Profils der vertrauenden Seite, aber kein Ausgabeanspruch in einem der Schritte der User Journey *SignUpOrSignIn*.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="schoolId" />
      ...
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
``` 

Um diese Art von Fehler zu beheben, stellen Sie sicher, dass die Ausgabeansprüche in mindestens einer Sammlung der Ausgabeansprüche des technischen Profils für die Orchestrierungsschritte angezeigt werden. Wenn Ihre User Journey den Anspruch nicht ausgeben kann, legen Sie im technischen Profil der vertrauenden Seite einen Standardwert fest, z. B. eine leere Zeichenfolge.  

```xml
<OutputClaim ClaimTypeReferenceId="schoolId" DefaultValue="" />
```

### <a name="input-string-was-not-in-a-correct-format"></a>Die Eingabezeichenfolge hat das falsche Format

Sie haben einen falschen Werttyp für einen Anspruch eines anderen Typs festgelegt. Beispiel: Sie haben einen ganzzahligen Anspruch definiert.

```xml
<!--
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="age">
      <DisplayName>Age</DisplayName>
      <DataType>int</DataType>
    </ClaimType>
  <!--
  </ClaimsSchema>
</BuildingBlocks> -->
```

Anschließend versuchen Sie, einen Zeichenfolgenwert zu festzulegen:

```xml
<OutputClaim ClaimTypeReferenceId="age" DefaultValue="ABCD" />
```

Um diese Art von Fehler zu beheben, stellen Sie sicher, dass Sie den richtigen Wert festlegen, z. B. `DefaultValue="0"`.


### <a name="tenant-name-already-has-a-policy-with-id-name-another-policy-with-the-same-id-cannot-be-stored"></a>Für Mandant '{Name}' gibt es bereits eine Richtlinie mit ID '{Name}'. Eine andere Richtlinie mit der gleichen ID kann nicht gespeichert werden

Sie versuchen, eine Richtlinie in Ihren Mandanten hochzuladen, aber eine Richtlinie mit demselben Namen wurde bereits in Ihren Mandanten hochgeladen. 

Um diese Art von Fehler zu beheben, aktivieren Sie beim Hochladen der Richtlinie das Kontrollkästchen **Benutzerdefinierte Richtlinie überschreiben, sofern bereits vorhanden**.

![Im Screenshot wird gezeigt, wie die benutzerdefinierte Richtlinie überschrieben wird, wenn sie bereits vorhanden ist.](./media/troubleshoot-custom-policies/overwrite-custom-policy-if-exists.png)


## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das [Sammeln von Azure Active Directory B2C-Protokollen mit Application Insights](troubleshoot-with-application-insights.md).

