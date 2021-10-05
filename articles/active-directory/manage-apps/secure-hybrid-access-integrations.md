---
title: Sicherer Hybridzugriff über Azure AD-Partnerintegrationen
description: Kunden bei der Ermittlung und Migration von SaaS-Anwendungen in Azure AD unterstützen und Apps verbinden, die Legacyauthentifizierungsmethoden mit Azure AD verwenden.
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 04/20/2021
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d7463c6f21ce3973f92ea6261c026adaeaf003d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128630336"
---
# <a name="secure-hybrid-access-with-azure-active-directory-partner-integrations"></a>Sicherer Hybridzugriff über Azure Active Directory Partnerintegrationen

Azure Active Directory (Azure AD) unterstützt moderne Authentifizierungsprotokolle, die dazu beitragen, Anwendungen in einer stark vernetzten, cloudbasierten Welt abzusichern. Viele Geschäftsanwendungen wurden jedoch entwickelt, um innerhalb eines geschützten Unternehmensnetzwerks zu funktionieren, und einige dieser Anwendungen verwenden Legacy-Authentifizierungsmethoden. Wenn Unternehmen eine Zero Trust-Strategie entwickeln und Hybrid- und Cloud-First-Arbeitsumgebungen unterstützen, benötigen sie Lösungen, die Apps mit Azure Active Directory verbinden und moderne Authentifizierungslösungen für Legacy-Anwendungen bereitstellen.

Azure Active Directory (Azure AD) unterstützt moderne Protokolle wie SAML, WS-Fed und OIDC. Der App-Proxy von Azure AD unterstützt Kerberos- und headerbasierte Authentifizierung. Andere Protokolle wie SSH, NTLM, LDAP und Cookies werden noch nicht unterstützt. ISVs können jedoch Lösungen erstellen, um diese Anwendungen mit Azure AD zu verbinden und Kunden auf ihrem Weg zu Zero Trust zu unterstützen.

ISVs haben die Chance, Kunden bei der Erkennung und Migration von Software-as-a-Service-Anwendungen (SaaS) in Azure AD zu unterstützen. Sie können auch Apps verbinden, die unsere Legacyauthentifizierungsmethoden mit Azure AD einsetzen. Dies hilft Kunden, sich auf einer einzelnen Plattform (Azure AD) zu konsolidieren, um ihre App-Verwaltung zu vereinfachen, und ermöglicht ihnen die Implementierung von Zero Trust-Prinzipien. Das Unterstützen von Apps, die die Legacyauthentifizierung verwenden, macht Benutzer sicherer. Diese Lösung kann eine hervorragende Übergangslösung sein, bis die Kunden ihre Apps modernisieren, um moderne Authentifizierungsprotokolle zu unterstützen.

## <a name="solution-overview"></a>Lösungsübersicht

Die Lösung, die Sie erstellen, kann die folgenden Teile umfassen:

1. **App-Ermittlung**. Oft sind sich die Kunden nicht aller Anwendungen bewusst, die sie verwenden. Ein erster Schritt könnte also sein, dass Sie Fähigkeiten zur Anwendungsermittlung in Ihre Lösung integrieren und ermittelte Anwendungen in der Benutzeroberfläche anzeigen können. Auf diese Weise kann der Kunde priorisieren, wie er seine Anwendungen in Azure AD integrieren möchte.
2. **App-Migration**. Als Nächstes können Sie einen produktinternen Workflow erstellen, bei dem der Kunde Anwendungen direkt in Azure AD integrieren kann, ohne zum Azure AD-Portal wechseln zu müssen. Wenn Sie keine Ermittlungsfähigkeiten in seine Lösung implementieren, können Sie Ihre Lösung damit beginnen, dass Sie die dem Kunden bekannten Anwendungen in Azure AD integrieren.
3. **Unterstützung für Legacyauthentifizierung**. Sie können Apps mithilfe von Legacyauthentifizierungsmethoden mit Azure AD verbinden, damit sie die Vorteile von einmaligem Anmelden und anderer Funktionen nutzen können.
4. **Bedingter Zugriff**. Als eine zusätzliche Funktion können Sie es Kunden ermöglichen, sofort [Azure AD](/azure/active-directory/conditional-access/overview/)-Richtlinien für bedingten Zugriff auf die Anwendungen innerhalb Ihrer Lösung anzuwenden, ohne zum Azure AD-Portal wechseln zu müssen.

Im restlichen Teil dieses Leitfadens werden die technischen Aspekte und unsere Empfehlungen für die Implementierung einer Lösung erläutert.

## <a name="publishing-your-application-to-azure-marketplace"></a>Veröffentlichen Ihrer Anwendung in Azure Marketplace

Sie können Ihre Anwendung vorab in Azure AD integrieren, um SSO und automatische Bereitstellung zu unterstützen, indem Sie den Prozess zur [Veröffentlichung im Azure Marketplace](/azure/active-directory/develop/v2-howto-app-gallery-listing/) ausführen lassen. Azure Marketplace ist eine vertrauenswürdige Quelle für Anwendungen für IT-Administratoren. Die dort aufgeführten Anwendungen wurden auf ihre Kompatibilität mit Azure AD überprüft. Sie unterstützen SSO: Sie automatisieren die Benutzerbereitstellung und können problemlos in Kunden-Mandanten mit automatischer App-Registrierung integriert werden.

Außerdem empfehlen wir, dass Sie ein [verifizierter Herausgeber](/azure/active-directory/develop/publisher-verification-overview/) werden, damit die Kunden wissen, dass Sie der vertrauenswürdige Herausgeber der App sind.

## <a name="enabling-single-sign-on-for-it-admins"></a>Aktivieren von Einmaliges Anmelden für IT-Administratoren

[Wählen Sie entweder OIDC oder SAML](/azure/active-directory/manage-apps/sso-options#choosing-a-single-sign-on-method/), um SSO für IT-Administratoren für Ihre Lösung zu aktivieren. Die beste Option ist die Verwendung von OIDC. 

Microsoft Graph verwendet [OIDC/OAuth](/azure/active-directory/develop/v2-protocols-oidc/). Wenn Ihre Lösung OIDC mit Azure AD für die SSO von IT-Administratoren verwendet, haben Ihre Kunden eine nahtlose End-to-End-Erfahrung. Sie verwenden OIDC, um sich bei Ihrer Lösung anzumelden, und dasselbe JSON Web Token (JWT), das Azure AD ausgestellt hat, kann dann zur Interaktion mit Microsoft Graph verwendet werden.

Wenn Ihre Lösung stattdessen [SAML](/azure/active-directory/manage-apps/configure-saml-single-sign-on/) für IT-Administrator-SSO verwendet: Das SAML-Token ermöglicht Ihrer Lösung nicht die Interaktion mit Microsoft Graph. Sie können immer noch SAML für IT-Administrator-SSO verwenden, aber Ihre Lösung muss die OIDC-Integration mit Azure AD unterstützen, damit sie ein JWT von Azure AD erhalten kann, um ordnungsgemäß mit Microsoft Graph zu interagieren. Sie können einen der folgenden Ansätze verwenden:

- **Empfohlener SAML-Ansatz**: Erstellen Sie eine neue Registrierung im Azure Marketplace, der [eine OIDC Anwendung](/azure/active-directory/saas-apps/openidoauth-tutorial/) ist. Dies bietet die beste nahtlose Erfahrung für Ihre Kunden. Sie werden sowohl die SAML als auch die OIDC-Anwendungen zu ihrem Mandanten hinzufügen. Wenn sich Ihre Anwendung derzeit nicht im Azure AD-Katalog befindet, können Sie mit einer [mehrinstanzenfähigen Anwendung](/azure/active-directory/develop/howto-convert-app-to-be-multi-tenant/) beginnen, die nicht aus dem Katalog stammt.

- **Alternativer SAML-Ansatz**: Ihr Kunde kann manuell [eine OIDC-Anwendungsregistrierung erstellen](/azure/active-directory/saas-apps/openidoauth-tutorial/) in ihrem Azure AD Mandanten und sicherstellen, dass die richtigen URIs, Endpunkte und Berechtigungen festgelegt werden, die weiter unten in diesem Dokument angegeben werden.

Sie werden den [Gewährungstyp client_credentials](/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow#get-a-token/) verwenden wollen. Dies setzt voraus, dass Ihre Lösung jedem Kunden erlaubt, eine Client-ID und ein Geheimnis in Ihre Benutzeroberfläche einzugeben, und dass Sie diese Informationen speichern. Rufen Sie ein JWT von Azure AD ab, und verwenden Sie es dann für die Interaktion mit Microsoft Graph.

Wenn Sie sich für diesen Weg entscheiden, sollten Sie Ihren Kunden eine fertige Dokumentation zur Verfügung stellen, wie sie diese Anwendungsregistrierung in ihrem Azure AD-Mandanten erstellen können. Diese Informationen umfassen die Endpunkte, URIs und erforderlichen Berechtigungen.

> [!NOTE]
> Bevor eine Anwendung für IT-Administrator oder Benutzer-SSO verwendet werden kann, muss der IT-Administrator des Kunden [der Anwendung in seinem Mandanten zustimmen](/azure/active-directory/manage-apps/grant-admin-consent/).

## <a name="authentication-flows"></a>Authentifizierungsflows

Die Lösung umfasst drei wichtige Authentifizierungsflows, die die folgenden Szenarien unterstützen:

- Der IT-Administrator des Kunden meldet sich mit SSO an, um Ihre Lösung zu verwalten.

- Der IT-Administrator des Kunden verwendet Ihre Lösung, um Anwendungen über Microsoft Graph in Azure AD zu integrieren.

- Benutzer melden sich bei Legacy-Anwendungen an, die durch Ihre Lösung und Azure AD gesichert sind.

### <a name="your-customers-it-administrator-does-single-sign-on-to-your-solution"></a>Der IT-Administrator Ihres Kunden führt eine Einzelanmeldung zu Ihrer Lösung durch.

Ihre Lösung kann entweder SAML oder OIDC für SSO verwenden, wenn sich der IT-Administrator des Kunden anmeldet. In jedem Fall empfehlen wir, dass sich der IT-Administrator mit seinen Azure AD-Anmeldedaten bei Ihrer Lösung anmelden kann. Dies ermöglicht eine nahtlose Erfahrung und erlaubt es ihnen, die bestehenden Sicherheitskontrollen zu nutzen, die sie bereits eingerichtet haben. Ihre Lösung sollte mit Azure AD für SSO entweder über SAML oder OIDC integriert werden.

Im Folgenden finden Sie ein Diagramm und eine Zusammenfassung dieses Benutzerauthentifizierungsflows:

![Das Diagramm zeigt, wie ein IT-Administrator von der Lösung zu Azure AD umgeleitet wird, um sich anzumelden, und dann in einem Benutzerauthentifizierungsflow von Azure AD zurück zur Lösung umgeleitet wird.](./media/secure-hybrid-access-integrations/admin-flow.png)

1. Der IT-Administrator möchte sich mit seinen Azure AD-Anmeldedaten bei Ihrer Lösung anmelden.

2. Ihre Lösung leitet den IT-Administrator entweder mit einer SAML- oder einer OIDC-Anforderung an Azure AD weiter.

3. Azure AD authentifiziert den IT-Administrator und sendet ihn dann mit einem angehängten SAML-Token oder JSON Web Token zurück an Ihre Lösung, um innerhalb Ihrer Lösung autorisiert zu werden.

### <a name="the-it-administrator-integrates-applications-with-azure-ad-by-using-your-solution"></a>Der IT-Administrator integriert Anwendungen mit Azure AD unter Verwendung Ihrer Lösung

Die zweite Etappe der Journey des IT-Administrators besteht in der Integration von Anwendungen in Azure AD mit Hilfe Ihrer Lösung. Hierzu verwendet Ihre Lösung Microsoft Graph, um Anwendungsregistrierungen und Azure AD-Richtlinien für bedingten Zugriff zu erstellen.

Im Folgenden finden Sie ein Diagramm und eine Zusammenfassung dieses Benutzerauthentifizierungsflows:

![Diagramm der Weiterleitungen und anderer Interaktionen zwischen dem IT-Administrator, Azure Active Directory, Ihrer Lösung und Microsoft Graph in einem Benutzerauthentifizierungsflows.](./media/secure-hybrid-access-integrations/registration-flow.png)


1. Der IT-Administrator möchte sich mit seinen Azure AD-Anmeldedaten bei Ihrer Lösung anmelden.

2. Ihre Lösung leitet den IT-Administrator entweder mit einer SAML- oder einer OIDC-Anforderung an Azure AD weiter.

3. Azure AD authentifiziert den IT-Administrator und sendet ihn dann entweder mit einem SAML-Token oder einem JSON Web Token zur Autorisierung innerhalb Ihrer Lösung an Ihre Lösung zurück.

4. Wenn der IT-Administrator eine seiner Anwendungen in Azure AD integrieren möchte, muss er nicht zum Azure AD-Portal gehen, sondern Ihre Lösung ruft Microsoft Graph mit dem vorhandenen JWT auf, um diese Anwendungen zu registrieren oder Azure AD-Richtlinien für den bedingten Zugang auf sie anzuwenden.

### <a name="users-sign-in-to-the-applications-secured-by-your-solution-and-azure-ad"></a>Benutzer melden sich bei den durch Ihre Lösung und Azure AD gesicherten Anwendungen an

Wenn sich Benutzer bei einzelnen Anwendungen anmelden müssen, die mit Ihrer Lösung und Azure AD gesichert sind, verwenden sie entweder OIDC oder SAML. Wenn die Anwendungen mit Microsoft Graph oder einer von Azure AD geschützten API interagieren müssen, empfehlen wir Ihnen, sie für die Verwendung von OICD zu konfigurieren. Diese Konfiguration stellt sicher, dass das JWT, das die Anwendungen von Azure AD erhalten, um sie bei den Anwendungen zu authentifizieren, auch für die Interaktion mit Microsoft Graph verwendet werden kann. Wenn keine Notwendigkeit besteht, dass die einzelnen Anwendungen mit Microsoft Graph oder einer von Azure AD geschützten API interagieren, reicht SAML aus.

Hier finden Sie ein Diagramm und eine Zusammenfassung dieses Benutzerauthentifizierungsflows:

![Diagramm mit Umleitungen und anderen Interaktionen zwischen dem Benutzer, Azure Active Directory, Ihrer Lösung und der Anwendung in einem Benutzerauthentifizierungsflow.](./media/secure-hybrid-access-integrations/end-user-flow.png)

1. Der Benutzer möchte sich bei einer Anwendung anmelden, die durch Ihre Lösung und Azure AD gesichert ist.
2. Ihre Lösung leitet den Benutzer entweder mit einer SAML- oder einer OIDC-Anforderung an Azure AD weiter.
3. Azure AD authentifiziert den Benutzer und sendet ihn dann entweder mit einem SAML-Token oder einem JSON Web Token zur Autorisierung innerhalb Ihrer Lösung an Ihre Lösung zurück.
4. Nach der Autorisierung lässt Ihre Lösung die ursprüngliche Anforderung an die Anwendung zu, indem sie das bevorzugte Protokoll der Anwendung verwendet.

## <a name="summary-of-microsoft-graph-apis"></a>Zusammenfassung der Microsoft Graph-APIs

Ihre Lösung muss die folgenden APIs verwenden. Mit Azure AD können Sie entweder delegierte Berechtigungen oder Anwendungsberechtigungen konfigurieren. Für diese Lösung benötigen Sie nur delegierte Berechtigungen.

- [Anwendungsvorlagen-API](/graph/application-saml-sso-configure-api#retrieve-the-gallery-application-template-identifier/): Wenn Sie den Azure Marketplace durchsuchen möchten, können Sie diese API verwenden, um eine passende Anwendungsvorlage zu finden. **Erforderliche Berechtigungen**: Application.Read.All.

- [Anwendungsregistrierungs-API](/graph/api/application-post-applications): Sie verwenden diese API, um entweder OIDC- oder SAML-Anwendungsregistrierungen zu erstellen, damit sich Benutzer bei den Anwendungen anmelden können, die die Kunden mit Ihrer Lösung gesichert haben. Dadurch können diese Anwendungen auch mit Azure AD geschützt werden. **Erforderliche Berechtigungen**: Application.Read.All, Application.ReadWrite.All.

- [Service Principal API](/graph/api/serviceprincipal-update): Nachdem Sie die Anwendung registriert haben, müssen Sie das Service Principal Objekt aktualisieren, um einige SSO-Eigenschaften festzulegen. **Erforderliche Berechtigungen**: Application.ReadWrite.All, Directory.AccessAsUser.All, AppRoleAssignment.ReadWrite.All (zur Zuweisung).

- [Bedingter Zugriff API](/graph/api/resources/conditionalaccesspolicy): Wenn Sie Azure AD Richtlinien zum bedingten Zugriff auch auf diese Benutzeranwendungen anwenden möchten, können Sie diese API verwenden. **Erforderliche Berechtigungen**: Policy.Read.All, Policy.ReadWrite.ConditionalAccess, und Application.Read.All.

## <a name="example-graph-api-scenarios"></a>Graph-API-Beispielszenarien

Dieser Abschnitt enthält ein Beispiel für einen Verweis zur Verwendung von Microsoft Graph APIs zur Implementierung von Anwendungsregistrierungen, zur Verbindung von Legacy-Anwendungen und zur Aktivierung von Richtlinien für bedingten Zugriff über Ihre Lösung. Dieser Abschnitt enthält auch Anleitungen zum Automatisieren der Administratoreinwilligung zum Abrufen des Tokensignaturzertifikats und zum Zuweisen von Benutzern und Gruppen. Diese Funktionen könnten für Ihre Lösung nützlich sein.

### <a name="use-the-graph-api-to-register-apps-with-azure-ad"></a>Verwenden der Graph-API zum Registrieren von Apps bei Azure AD

#### <a name="add-apps-that-are-in-azure-marketplace"></a>Fügen Sie Anwendungen hinzu, die sich im Azure Marketplace befinden

Einige der Anwendungen, die Ihr Kunde verwendet, werden bereits im [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps) verfügbar sein. Sie können eine Lösung erstellen, die diese Anwendungen programmgesteuert dem Mandanten des Kunden hinzufügt. Der folgende Code ist ein Beispiel für die Verwendung der Microsoft Graph-API, um den Azure Marketplace nach einer passenden Vorlage zu durchsuchen und dann die Anwendung im Azure AD-Mandanten des Kunden zu registrieren.

Suchen Sie im Azure Marketplace nach einer passenden Anwendung. Wenn Sie die Anwendungsvorlagen API verwenden, wird beim Anzeigenamen Groß-/Kleinschreibung beachtet.

```http
Authorization: Required with a valid Bearer token
Method: Get

https://graph.microsoft.com/v1.0/applicationTemplates?$filter=displayname eq "Salesforce.com"
```

Wenn der vorangegangene API-Aufruf eine Übereinstimmung ergibt, zeichnen Sie die ID auf und führen Sie dann den folgenden API-Aufruf durch, wobei Sie einen benutzerfreundlichen Anzeigenamen für die Anwendung im JSON-Körper bereitstellen:

```https
Authorization: Required with a valid Bearer token
Method: POST
Content-type: application/json

https://graph.microsoft.com/v1.0/applicationTemplates/cd3ed3de-93ee-400b-8b19-b61ef44a0f29/instantiate
{
    "displayname": "Salesforce.com"
}
```

Wenn Sie den vorangehenden API-Aufruf tätigen, generieren Sie auch ein Dienstprinzipalobjekt, was einige Sekunden in Anspruch nehmen kann. Achten Sie darauf, die Anwendungs-ID und die Service Principal-ID aufzuzeichnen. Sie werden sie in den nächsten API-Aufrufen verwenden.

Als Nächstes ergänzen Sie das Dienstprinzipalobjekt mit dem SAML-Protokoll und der entsprechenden Anmelde-URL:

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: servicePrincipal/json

https://graph.microsoft.com/v1.0/servicePrincipals/3161ab85-8f57-4ae0-82d3-7a1f71680b27
{
    "preferredSingleSignOnMode":"saml",
    "loginURL": "https://www.salesforce.com"
}
```

Schließlich fügen Sie dem Anwendungsobjekt die entsprechenden Umleitungs-URIs und die Bezeichner-URIs hinzu:

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: application/json

https://graph.microsoft.com/v1.0/applications/54c4806b-b260-4a12-873c-967116983792
{
    "web": {
    "redirectUris":["https://www.salesforce.com"]},
    "identifierUris":["https://www.salesforce.com"]
}
```

#### <a name="add-apps-that-are-not-in-azure-marketplace"></a>Hinzufügen von Anwendungen, die sich nicht im Azure Marketplace

Wenn Sie im Azure Marketplace keine passende Anwendung finden oder einfach nur eine benutzerdefinierte Anwendung integrieren möchten, können Sie eine benutzerdefinierte Anwendung in Azure AD registrieren, indem Sie diese Vorlagen-ID verwenden: **H8adf8e6e-67b2-4cf2-a259-e3dc5476c621**. Führen Sie dann diesen API-Aufruf aus, wobei Sie einen benutzerfreundlichen Anzeigenamen der Anwendung im JSON-Text bereitstellen:

```https
Authorization: Required with a valid Bearer token
Method: POST
Content-type: application/json

https://graph.microsoft.com/v1.0/applicationTemplates/8adf8e6e-67b2-4cf2-a259-e3dc5476c621/instantiate
{
    "displayname": "Custom SAML App"
}
```

Wenn Sie den vorangehenden API-Aufruf tätigen, generieren Sie auch ein Dienstprinzipalobjekt, was einige Sekunden in Anspruch nehmen kann. Achten Sie darauf, die Anwendungs-ID und die Service Principal-ID aufzuzeichnen. Sie werden sie in den nächsten API-Aufrufen verwenden.

Als Nächstes ergänzen Sie das Dienstprinzipalobjekt mit dem SAML-Protokoll und der entsprechenden Anmelde-URL:

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: servicePrincipal/json

https://graph.microsoft.com/v1.0/servicePrincipals/3161ab85-8f57-4ae0-82d3-7a1f71680b27
{
    "preferredSingleSignOnMode":"saml",
    "loginURL": "https://www.samlapp.com"
}
```

Schließlich fügen Sie dem Anwendungsobjekt die entsprechenden Umleitungs-URIs und die Bezeichner-URIs hinzu:

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: application/json

https://graph.microsoft.com/v1.0/applications/54c4806b-b260-4a12-873c-967116983792
{
    "web": {
    "redirectUris":["https://www.samlapp.com"]},
    "identifierUris":["https://www.samlapp.com"]
}
```

#### <a name="cut-over-to-azure-ad-single-sign-on"></a>Umstellung auf einmaliges Anmelden in Azure AD

Nachdem Sie die SaaS-Anwendungen in Azure AD registriert haben, müssen die Anwendungen noch umgestellt werden, damit sie Azure AD als Identitätsanbieter nutzen können. Hierfür gibt es zwei Möglichkeiten:

- Wenn die Anwendungen 1-Klick-SSO unterstützen, kann Azure AD die Anwendungen für den Kunden freischalten. Der Kunde muss lediglich zum Azure AD-Portal gehen und die 1-Klick-SSO mit den administrativen Anmeldeinformationen für die unterstützten SaaS-Anwendungen durchführen. Weitere Informationen finden Sie unter [1-Klick-App-Konfiguration für Einmaliges Anmelden](/azure/active-directory/manage-apps/one-click-sso-tutorial/).
- Wenn die Anwendungen kein 1-Klick-SSO unterstützen, muss der Kunde die Anwendungen manuell umstellen, um Azure AD zu nutzen. Weitere Informationen finden Sie unter [Tutorials zur Integration von SaaS-Anwendungen mit Azure Active Directory](/azure/active-directory/saas-apps/tutorial-list/).

### <a name="connect-apps-by-using-legacy-authentication-methods-to-azure-ad"></a>Verbinden Sie Anwendungen mithilfe von Legacy-Authentifizierungsmethoden mit Azure AD

Hier kann sich Ihre Lösung zwischen Azure AD und die Anwendung befinden und dem Kunden die Vorteile von SSO und anderen Azure Active Directory-Funktionen ermöglichen, selbst für Anwendungen, die nicht unterstützt werden. Hierzu ruft Ihre Anwendung Azure AD auf, um den Benutzer zu authentifizieren und Azure AD-Richtlinien für bedingten Zugriff anzuwenden, bevor er mit Legacy-Protokollen auf diese Anwendungen zugreifen kann.

Sie können es Kunden ermöglichen, diese Integration direkt über Ihre Konsole durchzuführen, damit die Ermittlung und Integration eine nahtlose End-to-End-Erfahrung ist. Dazu muss Ihre Plattform entweder eine SAML- oder eine OIDC-Anwendungsregistrierung zwischen Ihrer Plattform und Azure AD einrichten.

#### <a name="create-a-saml-application-registration"></a>Erstellen einer SAML-Anwendungsregistrierung

Verwenden Sie zum Erstellen einer SAML-Anwendungsregistrierung diese benutzerdefinierte Anwendungsvorlagen-ID für eine benutzerdefinierte Anwendung: **8adf8e6e-67b2-4cf2-a259-e3dc5476c621**. Führen Sie dann diesen API-Aufruf aus, wobei Sie einen benutzerfreundlichen Anzeigenamen im JSON-Text bereitstellen:

```https
Authorization: Required with a valid Bearer token
Method: POST
Content-type: application/json

https://graph.microsoft.com/v1.0/applicationTemplates/8adf8e6e-67b2-4cf2-a259-e3dc5476c621/instantiate
{
    "displayname": "Custom SAML App"
}
```

Wenn Sie den vorangehenden API-Aufruf tätigen, generieren Sie auch ein Dienstprinzipalobjekt, was einige Sekunden in Anspruch nehmen kann. Achten Sie darauf, die Anwendungs-ID und die Service Principal-ID aufzuzeichnen. Sie werden sie in den nächsten API-Aufrufen verwenden.

Als Nächstes ergänzen Sie das Dienstprinzipalobjekt mit dem SAML-Protokoll und der entsprechenden Anmelde-URL:

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: servicePrincipal/json

https://graph.microsoft.com/v1.0/servicePrincipals/3161ab85-8f57-4ae0-82d3-7a1f71680b27
{
    "preferredSingleSignOnMode":"saml",
    "loginURL": "https://www.samlapp.com"
}
```

Schließlich fügen Sie dem Anwendungsobjekt die entsprechenden Umleitungs-URIs und die Bezeichner-URIs hinzu:

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: application/json

https://graph.microsoft.com/v1.0/applications/54c4806b-b260-4a12-873c-967116983792
{
    "web": {
    "redirectUris":["https://www.samlapp.com"]},
    "identifierUris":["https://www.samlapp.com"]
}
```

#### <a name="create-an-oidc-application-registration"></a>Erstellen einer OIDC-Anwendungsregistrierung

Verwenden Sie zum Erstellen einer OIDC-Anwendungsregistrierung diese Vorlagen-ID für eine benutzerdefinierte Anwendung: **8adf8e6e-67b2-4cf2-a259-e3dc5476c621**. Führen Sie dann diesen API-Aufruf aus, wobei Sie einen benutzerfreundlichen Anzeigenamen im JSON-Text bereitstellen:

```https
Authorization: Required with a valid Bearer token
Method: POST
Content-type: application/json

https://graph.microsoft.com/v1.0/applicationTemplates/8adf8e6e-67b2-4cf2-a259-e3dc5476c621/instantiate
{
    "displayname": "Custom OIDC App"
}
```

Erfassen Sie über den API-Aufruf die Anwendungs-ID und die Dienstprinzipal-ID. Sie werden sie in den nächsten API-Aufrufen verwenden.

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: application/json

https://graph.microsoft.com/v1.0/applications/{Application Object ID}
{
    "web": {
    "redirectUris":["https://www.samlapp.com"]},
    "identifierUris":["[https://www.samlapp.com"],
    "requiredResourceAccess": [
    {
        "resourceAppId": "00000003-0000-0000-c000-000000000000",
        "resourceAccess": [
        {
            "id": "7427e0e9-2fba-42fe-b0c0-848c9e6a8182",
            "type": "Scope"
        },
        {
            "id": "e1fe6dd8-ba31-4d61-89e7-88639da4683d",
            "type": "Scope"
        },
        {
            "id": "37f7f235-527c-4136-accd-4a02d197296e",
            "type": "Scope"
        }]
    }]
}
```

> [!NOTE]
> Die innerhalb des `resourceAccess`Knotens aufgeführten API-Berechtigungen weisen der Anwendung die Berechtigungen *openid*, *User.Read* und *offline_access* zu, was ausreichen sollte, um den Benutzer bei Ihrer Lösung anzumelden. Weitere Informationen zu Berechtigungen finden Sie in der [Microsoft Graph-Berechtigungsreferenz](/graph/permissions-reference/).

### <a name="apply-conditional-access-policies"></a>Anwenden von Richtlinien für bedingten Zugriff

Kunden und Partner können die Microsoft Graph-API auch verwenden, um Richtlinien für den bedingten Zugriff auf Kundenanwendungen zu erstellen oder anzuwenden. Für Partner kann dies einen zusätzlichen Wert darstellen, da Kunden diese Richtlinien direkt von Ihrer Lösung aus anwenden können, ohne zum Azure AD-Portal gehen zu müssen. 

Sie haben zwei Möglichkeiten, Azure AD-Richtlinien für bedingten Zugriff anzuwenden:

- Weisen Sie die Anwendung einer vorhandenen Richtlinie für bedingten Zugriff zu.
- Erstellen Sie eine neue Richtlinie für bedingten Zugriff und weisen Sie die Anwendung dieser neuen Richtlinie zu.

#### <a name="use-an-existing-conditional-access-policy"></a>Verwenden einer vorhandene Richtlinie für bedingten Zugriff

Führen Sie zunächst die folgende Abfrage aus, um eine Liste aller Richtlinien für bedingten Zugriff zu erhalten. Abrufen der Objekt-ID der Richtlinie, die Sie ändern möchten.

```https
Authorization: Required with a valid Bearer token
Method:GET

https://graph.microsoft.com/v1.0/identity/conditionalAccess/policies
```

Als Nächstes ergänzen Sie die Richtlinie, indem Sie die ID des Anwendungsobjekts so einfügen, dass sie `includeApplications`in den JSON-Körper fällt:

```https
Authorization: Required with a valid Bearer token
Method: PATCH

https://graph.microsoft.com/v1.0/identity/conditionalAccess/policies/{policyid}
{
    "displayName":"Existing CA Policy",
    "state":"enabled",
    "conditions": 
    {
        "applications": 
        {
            "includeApplications":[
                "00000003-0000-0ff1-ce00-000000000000", 
                "{Application Object ID}"
            ]
        },
        "users": {
            "includeUsers":[
                "All"
            ] 
        }
    },
    "grantControls": 
    {
        "operator":"OR",
        "builtInControls":[
            "mfa"
        ]
    }
}
```

#### <a name="create-a-new-conditional-access-policy"></a>Erstellen einer neuen Richtlinie für bedingten Zugriff

Fügen Sie die Anwendungsobjekt-ID so ein, dass sie in den Bereich `includeApplications` des JSON-Körpers fällt:

```https
Authorization: Required with a valid Bearer token
Method: POST

https://graph.microsoft.com/v1.0/identity/conditionalAccess/policies/
{
    "displayName":"New CA Policy",
    "state":"enabled",
    "conditions": 
    {
        "applications": {
            "includeApplications":[
                "{Application Object ID}"
            ]
        },
        "users": {
            "includeUsers":[
                "All"
            ]
        }
    },
    "grantControls": {
        "operator":"OR",
        "builtInControls":[
            "mfa"
        ]
    }
}
```

Falls Sie eine neue Azure AD-Richtlinie für bedingten Zugriff erstellen möchten, finden Sie hier einige zusätzliche Vorlagen, die Ihnen den Einstieg in die Verwendung der [API für bedingten Zugriff](/azure/active-directory/conditional-access/howto-conditional-access-apis/) erleichtern können:

```https
#Policy Template for Requiring Compliant Device

{
    "displayName":"Enforce Compliant Device",
    "state":"enabled",
    "conditions": {
        "applications": {
            "includeApplications":[
                "{Application Object ID}"
            ]
        },
        "users": {
            "includeUsers":[
                "All"
            ]
        }
    },
    "grantControls": {
        "operator":"OR",
        "builtInControls":[
            "compliantDevice",
            "domainJoinedDevice"
        ]
    }
}

#Policy Template for Block

{
    "displayName":"Block",
    "state":"enabled",
    "conditions": {
        "applications": {
            "includeApplications":[
                "{Application Object ID}"
            ]
        },
        "users": {
            "includeUsers":[
                "All"
            ] 
        }
    },
    "grantControls": {
        "operator":"OR",
        "builtInControls":[
            "block"
        ]
    }
}
```

### <a name="automate-admin-consent"></a>Automatisieren der Administratoreinwilligung

Wenn der Kunde ein Onboarding von zahlreichen Anwendungen von Ihrer Plattform in Azure AD durchführt, können Sie die Administratoreinwilligung für ihn automatisieren, damit er nicht manuell die Einwilligung zu einer Vielfalt von Anwendungen erteilen muss. Sie können diese Automatisierung auch über Microsoft Graph vornehmen. Sie benötigen sowohl die Dienstprinzipalobjekt-ID der Anwendung, die Sie in früheren API-Aufrufen erstellt haben, als auch die Dienstprinzipalobjekt-ID von Microsoft Graph aus dem Mandanten des Kunden.

Rufen Sie die Dienstprinzipalobjekt-ID von Microsoft Graph mit diesem API-Aufruf ab:

```https
Authorization: Required with a valid Bearer token
Method:GET

https://graph.microsoft.com/v1.0/serviceprincipals/?$filter=appid eq '00000003-0000-0000-c000-000000000000'&$select=id,appDisplayName
```

Wenn Sie bereit sind, die Administratoreinwilligung zu automatisieren, führen Sie diesen API-Aufruf durch:

```https
Authorization: Required with a valid Bearer token
Method: POST
Content-type: application/json

https://graph.microsoft.com/v1.0/oauth2PermissionGrants
{
    "clientId":"{Service Principal Object ID of Application}",
    "consentType":"AllPrincipals",
    "principalId":null,
    "resourceId":"{Service Principal Object ID Of Microsoft Graph}",
    "scope":"openid user.read offline_access}"
}
```

### <a name="get-the-token-signing-certificate"></a>Abrufen des Tokensignaturzertifikats

Um den öffentlichen Teil des Tokensignaturzertifikats für alle diese Anwendungen abzurufen, verwenden Sie `GET` vom Azure AD-Metadaten-Endpunkt für die Anwendung:

```https
Method:GET

https://login.microsoftonline.com/{Tenant_ID}/federationmetadata/2007-06/federationmetadata.xml?appid={Application_ID}
```

### <a name="assign-users-and-groups"></a>Zuweisen von Benutzern und Gruppen

Nachdem Sie die Anwendung in Azure AD veröffentlicht haben, können Sie sie optional Benutzern und Gruppen zuweisen, um sicherzustellen, dass sie im Portal [MyApplications](/azure/active-directory/user-help/my-applications-portal-workspaces/) angezeigt wird. Diese Zuweisung wird im Dienstprinzipalobjekt gespeichert, das beim Erstellen der Anwendung generiert wurde.

Ermitteln Sie zunächst alle `AppRole`-Instanzen, mit der die Anwendung verknüpft sein kann. Bei SaaS-Anwendungen ist es üblich, dass verschiedene `AppRole`-Instanzen mit ihnen verbunden sind. Für benutzerdefinierte Anwendungen gibt es normalerweise nur eine standardmäßige `AppRole`-Instanz. Ermitteln Sie die ID der `AppRole`-Instanz, die Sie zuweisen möchten:

```https
Authorization: Required with a valid Bearer token
Method:GET

https://graph.microsoft.com/v1.0/servicePrincipals/3161ab85-8f57-4ae0-82d3-7a1f71680b27
```

Als Nächstes rufen Sie die Objekt-ID des Benutzers oder der Gruppe aus Azure AD ab, die Sie der Anwendung zuweisen möchten. Nehmen Sie auch die Anwendungsrollen-ID aus dem vorherigen API-Aufruf auf und übermitteln Sie sie als Teil des Patch-Körpers auf dem Dienstprinzipal:

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: servicePrincipal/json

https://graph.microsoft.com/v1.0/servicePrincipals/3161ab85-8f57-4ae0-82d3-7a1f71680b27
{
    "principalId":"{Principal Object ID of User -or- Group}",
    "resourceId":"{Service Principal Object ID}",
    "appRoleId":"{App Role ID}"
}
```

## <a name="partnerships"></a>Partnerschaften

Microsoft unterhält Partnerschaften mit diesen Anwendungslieferungs-Controller (ADC)-Anbietern, um den Schutz von Legacy-Anwendungen unter Verwendung vorhandener Netz- und Lieferungs-Controllers zu unterstützen.

| **ADC-Anbieter** | **Link** |
| --- | --- |
| Akamai Enterprise Application Access | [https://docs.microsoft.com/azure/active-directory/saas-apps/akamai-tutorial](/azure/active-directory/saas-apps/akamai-tutorial) |
| Citrix ADC | [https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial](/azure/active-directory/saas-apps/citrix-netscaler-tutorial) |
| F5 Big-IP Zugriffsrichtlinienverwalter | [https://docs.microsoft.com/azure/active-directory/manage-apps/f5-aad-integration](/azure/active-directory/manage-apps/f5-aad-integration) |
| Kemp LoadMaster | [https://docs.microsoft.com/azure/active-directory/saas-apps/kemp-tutorial](/azure/active-directory/saas-apps/kemp-tutorial) |
| Pulse Secure Virtual Traffic Manager | [https://docs.microsoft.com/azure/active-directory/saas-apps/pulse-secure-virtual-traffic-manager-tutorial](/azure/active-directory/saas-apps/pulse-secure-virtual-traffic-manager-tutorial) |

Die folgenden VPN-Lösungsanbieter stellen eine Verbindung mit Azure AD her, um moderne Authentifizierungs- und Autorisierungsmethoden wie einmaliges Anmelden und mehrstufige Authentifizierung zu ermöglichen.

| **VPN-Anbieter** | **Link** |
| --- | --- |
| Cisco AnyConnect | [https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect](/azure/active-directory/saas-apps/cisco-anyconnect) |
| Fortinet Fortigate | [https://docs.microsoft.com/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial](/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial) |
| F5 Big-IP Zugriffsrichtlinienverwalter | [https://docs.microsoft.com/azure/active-directory/manage-apps/f5-aad-password-less-vpn](/azure/active-directory/manage-apps/f5-aad-password-less-vpn) |
| Palo Alto Networks GlobalProtect | [https://docs.microsoft.com/azure/active-directory/saas-apps/paloaltoadmin-tutorial](/azure/active-directory/saas-apps/paloaltoadmin-tutorial) |
| Pulse Connect Secure | [https://docs.microsoft.com/azure/active-directory/saas-apps/pulse-secure-pcs-tutorial](/azure/active-directory/saas-apps/pulse-secure-pcs-tutorial) |

Die folgenden Anbieter von Software-Defined Perimeter-Lösungen (SDP) stellen eine Verbindung mit Azure AD her, um moderne Authentifizierungs- und Autorisierungsmethoden wie SSO und mehrstufige Authentifizierung zu ermöglichen.

| **SDP-Anbieter** | **Link** |
| --- | --- |
| Datawiza Access Broker | [https://docs.microsoft.com/en-us/azure/active-directory/manage-apps/datawiza-with-azure-ad](/azure/active-directory/manage-apps/datawiza-with-azure-ad) |
| Perimeter 81 | [https://docs.microsoft.com/azure/active-directory/saas-apps/perimeter-81-tutorial](/azure/active-directory/saas-apps/perimeter-81-tutorial) |
| Silverfort Authentication Platform | [https://docs.microsoft.com/en-us/azure/active-directory/manage-apps/silverfort-azure-ad-integration](/azure/active-directory/manage-apps/silverfort-azure-ad-integration) |
| Strata Maverics Identitätsorchestrator | [https://docs.microsoft.com/azure/active-directory/saas-apps/maverics-identity-orchestrator-saml-connector-tutorial](/azure/active-directory/saas-apps/maverics-identity-orchestrator-saml-connector-tutorial) |
| Zscaler Private Access | [https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial](/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial) |
