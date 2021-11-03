---
title: Tutorial zum Konfigurieren von Azure Active Directory B2C mit Microsoft Dynamics 365 Fraud Protection
titleSuffix: Azure AD B2C
description: Tutorial zum Konfigurieren von Azure Active Directory B2C mit Microsoft Dynamics 365 Fraud Protection zum Identifizieren von riskanten und betrügerischen Konten
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 5/12/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 83715b34578176ba08fb364cfae11df8b1cf1209
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131044720"
---
# <a name="tutorial-configure-microsoft-dynamics-365-fraud-protection-with-azure-active-directory-b2c"></a>Tutorial: Konfigurieren von Microsoft Dynamics 365 Fraud Protection mit Azure Active Directory B2C

In diesem Beispieltutorial lernen Sie, [Microsoft Dynamics 365 Fraud Protection](/dynamics365/fraud-protection/overview) (DFP) in Azure Active Directory (AD) B2C zu integrieren.

Microsoft DFP ermöglicht Organisationen, das Risiko von Versuchen, betrügerische Konten und Anmeldungen zu erstellen, zu bewerten. Mit der DFP-Bewertung von Microsoft können Kunden verdächtige Versuche, neue gefälschte Konten zu erstellen oder vorhandene Konten zu kompromittieren, blockieren oder abwehren.

Dieses Beispiel veranschaulicht, wie Sie die API-Endpunkte für Microsoft DFP-Gerätefingerabdruck, Kontoerstellung und Anmeldungsbewertung in eine benutzerdefinierte Azure AD B2C-Richtlinie integrieren.

## <a name="prerequisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

- Ein Azure-Abonnement. Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.

- Einen [Azure AD B2C-Mandanten](./tutorial-create-tenant.md). Der Mandant ist mit Ihrem Azure-Abonnement verknüpft.

- Erwerben Sie ein Microsoft DFP-[Abonnement](https://dynamics.microsoft.com/pricing/#Sales). Sie können auch eine [Clienttestversion](https://dynamics.microsoft.com/ai/fraud-protection/signin/?RU=https%3A%2F%2Fdfp.microsoft.com%2Fsignin) einrichten.

## <a name="scenario-description"></a>Beschreibung des Szenarios

Die Microsoft DFP-Integration umfasst die folgenden Komponenten:

- **Azure AD B2C-Mandant**: Authentifiziert den Benutzer und fungiert als Client von Microsoft DFP. Hostet ein Fingerabdruckskript, das Identifikations- und Diagnosedaten jedes Benutzers sammelt, der eine Zielrichtlinie ausführt. Später werden Anmelde- oder Registrierungsversuche auf dem von Microsoft DFP zurückgegebenen Regelauswertungsergebnis basierend blockiert oder abgewehrt.

- **Benutzerdefinierte Benutzeroberflächenvorlagen**: Werden verwendet, um den HTML-Inhalt der von Azure AD B2C gerenderten Seiten anzupassen. Diese Seiten enthalten den JavaScript-Codeausschnitt, der für den Microsoft DFP-Fingerabdruck erforderlich ist.

- **Microsoft DFP-Fingerabdruckdienst**: Dynamisch eingebettetes Skript, das Gerätetelemetriedaten und selbstbestätigte Benutzerdetails protokolliert, um einen eindeutig identifizierbaren Fingerabdruck des Benutzers zu erstellen, der später im Entscheidungsprozess verwendet werden kann.

- **Microsoft DFP-API-Endpunkte**: Stellt das Entscheidungsergebnis bereit und akzeptiert einen endgültigen Status, der den von der Clientanwendung ausgeführten Vorgang widerspiegelt. Azure AD B2C kommuniziert über REST-API-Connectors direkt mit den Microsoft DFP-Endpunkten. Die API-Authentifizierung erfolgt über eine client_credentials-Gewährung für den Azure AD-Mandanten, in dem Microsoft DFP lizenziert und installiert ist, um ein Bearertoken abzurufen.

Im folgenden Architekturdiagramm ist die Implementierung dargestellt.

![Abbildung des Microsoft Dynamics 365 Fraud Protection-Architekturdiagramms](./media/partner-dynamics365-fraud-protection/microsoft-dynamics-365-fraud-protection-diagram.png)

|Schritt | BESCHREIBUNG |
|:-----| :-----------|
| 1. | Der Benutzer gelangt auf eine Anmeldeseite. Der Benutzer wählt die Registrierung zum Erstellen eines neuen Kontos aus und gibt Informationen auf der Seite ein. Azure AD B2C erfasst die Benutzerattribute.
| 2. | Azure AD B2C ruft die Microsoft DFP-API auf und übergibt die Benutzerattribute.
| 3. | Nachdem die Microsoft DFP-API die Informationen verwendet und verarbeitet hat, wird das Ergebnis an Azure AD B2C zurückgegeben.
| 4. | Azure AD B2C empfängt die Informationen wieder von der Microsoft DFP-API. Bei einer Fehlerantwort wird dem Benutzer eine Fehlermeldung angezeigt. Bei einer Erfolgsantwort wird der Benutzer authentifiziert und in das Verzeichnis geschrieben.

## <a name="set-up-the-solution"></a>Einrichten der Lösung

1. [Erstellen Sie eine Facebook-Anwendung](./identity-provider-facebook.md#create-a-facebook-application), die so konfiguriert ist, dass sie den Verbund mit Azure AD B2C zulässt.
2. [Fügen Sie das von Ihnen erstellte Facebook-Geheimnis](./tutorial-create-user-flows.md?pivots=b2c-custom-policy#create-the-facebook-key) als Identity Experience Framework-Richtlinienschlüssel hinzu.

## <a name="configure-your-application-under-microsoft-dfp"></a>Konfigurieren Ihrer Anwendung unter Microsoft DFP

[Richten Sie Ihren Azure AD-Mandanten](/dynamics365/fraud-protection/integrate-real-time-api) für die Verwendung von Microsoft DFP ein.

## <a name="set-up-your-custom-domain"></a>Einrichten Ihrer benutzerdefinierten Domäne

In einer Produktionsumgebung müssen Sie eine [benutzerdefinierte Domäne für Azure AD B2C](./custom-domain.md?pivots=b2c-custom-policy) und für den [Microsoft DFP-Fingerabdruckdienst](/dynamics365/fraud-protection/device-fingerprinting#set-up-dns) verwenden. Die Domäne für beide Dienste sollte sich in derselben DNS-Stammzone befinden, um zu verhindern, dass Browserdatenschutz-Einstellungen domänenübergreifende Cookies blockieren. Dies ist in einer Nicht-Produktionsumgebung nicht erforderlich.

Dies ist ein Beispiel:

| Umgebung | Dienst | Domain |
|:------------|:---------------|:---------------|
| Entwicklung | Azure AD B2C | `contoso-dev.b2clogin.com` |
| Entwicklung | Microsoft DFP-Fingerabdruck | `fpt.dfp.microsoft-int.com` |
| UAT | Azure AD B2C | `contoso-uat.b2clogin.com` |
| UAT | Microsoft DFP-Fingerabdruck | `fpt.dfp.microsoft.com` |
| Bereitstellung | Azure AD B2C | `login.contoso.com` |
| Bereitstellung | Microsoft DFP-Fingerabdruck | `fpt.login.contoso.com` |

## <a name="deploy-the-ui-templates"></a>Bereitstellen der Benutzeroberflächenvorlagen

1. Stellen Sie die verfügbaren [Azure AD B2C-Benutzeroberflächenvorlagen](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/ui-templates) für einen öffentlichen Internethostingdienst wie Azure Blob Storage bereit.

2. Ersetzen Sie den Wert `https://<YOUR-UI-BASE-URL>/` durch die Stamm-URL für Ihren Bereitstellungsspeicherort.

  >[!NOTE]
  >Sie benötigen später die Basis-URL, um die Azure AD B2C-Richtlinien zu konfigurieren.

3. Ersetzen Sie in der `ui-templates/js/dfp.js`-Datei `<YOUR-DFP-INSTANCE-ID>` durch Ihre Microsoft DFP-Instanz-ID.

4. Stellen Sie sicher, dass CORS für Ihren Azure AD B2C-Domänennamen `https://{your_tenant_name}.b2clogin.com` oder `your custom domain` aktiviert ist.

Weitere Informationen finden Sie unter [Anpassen der Benutzeroberfläche mit HTML-Vorlagen in Azure Active Directory B2C](./customize-ui-with-html.md?pivots=b2c-custom-policy).

## <a name="azure-ad-b2c-configuration"></a>Azure AD B2C-Konfiguration

### <a name="add-policy-keys-for-your-microsoft-dfp-client-app-id-and-secret"></a>Hinzufügen von Richtlinienschlüsseln für Ihre Microsoft DFP-Client-App-ID und das Geheimnis

1. Erstellen Sie im Azure AD-Mandanten, in dem Microsoft DFP eingerichtet ist, eine [Azure AD-Anwendung, und erteilen Sie die Administratoreinwilligung](/dynamics365/fraud-protection/integrate-real-time-api#create-azure-active-directory-applications).
2. Erstellen Sie einen Geheimniswert für diese Anwendungsregistrierung, und notieren Sie sich die Client-ID und den geheimen Clientschlüsselwert der Anwendung.
3. Speichern Sie die Werte für Client-ID und geheimen Clientschlüssel als [Richtlinienschlüssel in Ihrem Azure AD B2C-Mandanten](./policy-keys-overview.md).

 >[!NOTE]
 >Sie benötigen später die Richtlinienschlüssel, um Ihre Azure AD B2C-Richtlinien zu konfigurieren.

### <a name="replace-the-configuration-values"></a>Ersetzen der Konfigurationswerte

Suchen Sie in den bereitgestellten [benutzerdefinierten Richtlinien](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/policies) die folgenden Platzhalter, und ersetzen Sie diese durch die entsprechenden Werte aus Ihrer Instanz.

| Platzhalter | Ersetzen durch | Notizen |
| :-------- | :------------| :-----------|
|{Settings:Production} | Gibt an, ob die Richtlinien im Produktionsmodus bereitgestellt werden sollen. | `true` oder `false`  |
|{Settings:Tenant} | Kurzname Ihres Mandanten |  `your-tenant`: aus Ihr-Mandant.onmicrosoft.com  |
| {Settings:DeploymentMode}    |  Zu verwendender Application Insights-Bereitstellungsmodus   |  `Production` oder `Development`  |
|  {Settings:DeveloperMode}    | Gibt an, ob die Richtlinien im Application Insights-Entwicklermodus bereitgestellt werden sollen.      |   `true` oder `false`    |
|  {Settings:AppInsightsInstrumentationKey}  |  Instrumentierungsschlüssel Ihrer App Insights-Instanz*   |  `01234567-89ab-cdef-0123-456789abcdef` |
|  {Settings:IdentityExperienceFrameworkAppId}  | App-ID der IdentityExperienceFramework-App, die in Ihrem Azure AD B2C-Mandanten konfiguriert ist  | `01234567-89ab-cdef-0123-456789abcdef`|
|  {Settings:ProxyIdentityExperienceFrameworkAppId}  |   App-ID der ProxyIdentityExperienceFramework-App, die in Ihrem Azure AD B2C-Mandanten konfiguriert ist |   `01234567-89ab-cdef-0123-456789abcdef`|
| {Settings:FacebookClientId}  | App-ID der Facebook-App, die Sie für den Verbund mit B2C konfiguriert haben    | `000000000000000`   |
|   {Settings:FacebookClientSecretKeyContainer}  | Name des Richtlinienschlüssels, unter dem Sie das Geheimnis der Facebook-App gespeichert haben    |  `B2C_1A_FacebookAppSecret` |
|   {Settings:ContentDefinitionBaseUri} |  Endpunkt, in dem Sie die UI-Dateien bereitgestellt haben   | `https://<my-storage-account>.blob.core.windows.net/<my-storage-container>`   |
|  {Settings:DfpApiBaseUrl}   |  Der Basispfad für Ihre DFP-API-Instanz im DFP-Portal   | `https://tenantname-01234567-89ab-cdef-0123-456789abcdef.api.dfp.dynamics.com/v1.0/`   |
| {Settings:DfpApiAuthScope} | Der client_credentials-Bereich für den DFP-API-Dienst | `https://api.dfp.dynamics-int.com/.default or https://api.dfp.dynamics.com/.default` |
| {Settings:DfpTenantId} | Die ID des Azure AD-Mandanten (nicht B2C), in dem DFP lizenziert und installiert ist | `01234567-89ab-cdef-0123-456789abcdef` oder `consoto.onmicrosoft.com` |
| {Settings:DfpAppClientIdKeyContainer} | Name des Richtlinienschlüssels, in dem Sie die DFP-Client-ID speichern | `B2C_1A_DFPClientId` |
| {Settings:DfpAppClientSecretKeyContainer} | Name des Richtlinienschlüssels, in dem Sie den geheimen DFP-Clientschlüssel speichern | `B2C_1A_DFPClientSecret` |

*Application Insights kann in jedem Azure AD-Mandanten/-Abonnement eingerichtet werden. Dieser Wert ist optional, wird jedoch [zur Unterstützung beim Debuggen empfohlen](./troubleshoot-with-application-insights.md).

>[!NOTE]
>Fügen Sie der Seite für die Attributsammlung eine Einwilligungsbenachrichtigung hinzu. Informieren Sie darüber, dass die Telemetrie- und Benutzeridentitätsinformationen der Benutzer zu Kontoschutzzwecken aufgezeichnet werden.

## <a name="configure-the-azure-ad-b2c-policy"></a>Konfigurieren der Azure AD B2C-Richtlinie

1. Wechseln Sie im Richtlinienordner zur [Azure AD B2C-Richtlinie](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/policies).

2. Folgen Sie den Anweisungen in diesem [Dokument](./tutorial-create-user-flows.md?pivots=b2c-custom-policy?tabs=applications#custom-policy-starter-pack), um das [LocalAccounts Starter Pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts) herunterzuladen.

3. Konfigurieren Sie die Richtlinie für den Azure AD B2C-Mandanten.

>[!NOTE]
>Aktualisieren Sie die bereitgestellten Richtlinien so, dass sie sich auf Ihren spezifischen Mandanten beziehen.

## <a name="test-the-user-flow"></a>Testen des Benutzerflows

1. Öffnen Sie den Azure AD B2C-Mandanten, und wählen Sie unter „Richtlinien“ die Option **Identity Experience Framework** aus.

2. Wählen Sie den zuvor erstellten Flow **SignUpSignIn** aus.

3. Wählen Sie **Benutzerflow ausführen** und dann die Einstellungen aus:

   a. **Anwendung:** Wählen Sie die registrierte App aus (Beispiel: JWT).

   b. **Antwort-URL:** Wählen Sie die **Umleitungs-URL** aus.

   c. Wählen Sie **Benutzerflow ausführen** aus.

4. Führen Sie den Registrierungsflow aus, und erstellen Sie ein Konto.

5. Der Microsoft DFP-Dienst wird während des Flows aufgerufen, nachdem das Benutzerattribut erstellt wurde. Wenn der Flow unvollständig ist, stellen Sie sicher, dass der Benutzer nicht im Verzeichnis gespeichert ist.

>[!NOTE]
>Aktualisieren Sie Regeln direkt im Microsoft DFP-Portal, wenn Sie das [Microsoft DFP-Regelmodul](/dynamics365/fraud-protection/rules) verwenden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Microsoft DFP-Beispiele](https://github.com/Microsoft/Dynamics-365-Fraud-Protection-Samples)

- [Benutzerdefinierte Richtlinien in Azure AD B2C](./custom-policy-overview.md)

- [Erste Schritte mit benutzerdefinierten Richtlinien in Azure AD B2C](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)
