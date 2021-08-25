---
title: API-Authentifizierung und -Autorisierung – Azure Time Series Insights | Microsoft-Dokumentation
description: In diesem Artikel wird die Konfiguration der Authentifizierung und Autorisierung für eine benutzerdefinierte Anwendung erläutert, die die Azure Time Series Insights-API aufruft.
ms.service: time-series-insights
author: shreyasharmamsft
ms.author: shresha
manager: cnovak
ms.reviewer: orspodek
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/23/2021
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 86b663bb245b31ea89c9d523ea4df6e27bbc32b7
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772042"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Authentifizierung und Autorisierung für die Azure Time Series Insights-API

Je nach Ihren geschäftlichen Anforderungen kann Ihre Lösung eine oder mehrere Clientanwendungen enthalten, über die Sie mit den [APIs](/rest/api/time-series-insights/reference-data-access-overview) Ihrer Azure Time Series Insights-Umgebung interagieren. Die Authentifizierung erfolgt in Azure Time Series Insights über [OAuth 2.0-basierte Azure AD-Sicherheitstoken](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims). Zum Authentifizieren Ihrer Clients müssen Sie ein Bearertoken mit den richtigen Berechtigungen abrufen und es bei Ihren API-Aufrufen übergeben. In diesem Dokument werden verschiedene Methoden beschrieben, mit denen Sie Anmeldeinformationen zum Abrufen von Bearertoken und zum Authentifizieren erhalten können, einschließlich der Verwendung einer verwalteten Identität und der Azure Active Directory-App-Registrierung.

## <a name="managed-identities"></a>Verwaltete Identitäten

In den folgenden Abschnitten wird beschrieben, wie Sie mithilfe einer verwalteten Identität in Azure Active Directory (Azure AD) auf die Azure Time Series Insights-API zugreifen. In Azure brauchen Entwickler dank verwalteter Identitäten keine Anmeldeinformationen mehr zu verwalten. Für die Azure-Ressource in Azure AD wird eine Identität bereitgestellt, mit der Azure Active Directory (Azure AD)-Token abgerufen werden. Nachstehend sind einige Vorteile der Verwendung von verwalteten Identitäten beschrieben:

- Sie brauchen keine Anmeldeinformationen zu verwalten. Sie haben nicht einmal Zugriff auf die Anmeldeinformationen.
- Mit verwalteten Identitäten kann die Authentifizierung bei jedem Azure-Dienst erfolgen, der die Azure AD-Authentifizierung (einschließlich Azure Key Vault) unterstützt.
- Die Nutzung von verwalteten Identitäten verursacht keine zusätzlichen Kosten.

Weitere Informationen zu den beiden Typen von verwalteten Identitäten finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/overview.md).

Sie können verwaltete Identitäten über folgende Instanzen verwenden:

- Virtuelle Azure-Computer
- Azure App Services
- Azure-Funktionen
- Azure Container Instances
- und viele weitere

Eine vollständige Liste finden Sie unter [Azure-Dienste mit Unterstützung für verwaltete Identitäten für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources).

## <a name="azure-active-directory-app-registration"></a>Azure Active Directory-App-Registrierung

Es wird empfohlen, nach Möglichkeit verwaltete Identitäten zu verwenden, damit Sie keine Anmeldeinformationen verwalten müssen. Wenn Ihre Clientanwendung in einem Azure-Dienst gehostet wird, der keine verwalteten Identitäten unterstützt, können Sie Ihre Anwendung bei einem Azure AD-Mandanten registrieren. Wenn Sie Ihre Anwendung bei Azure AD registrieren, erstellen Sie eine Identitätskonfiguration für Ihre Anwendung, die die Integration in Azure AD ermöglicht. Wenn Sie eine App im [Azure-Portal](https://portal.azure.com/) registrieren, wählen Sie aus, ob es sich um einen Mandanten (Zugriff nur in Ihrem Mandanten möglich) oder um mehrere Mandanten (Zugriff in anderen Mandanten möglich) handelt. Optional können Sie auch einen Umleitungs-URI festlegen, an den das Zugriffstoken gesendet wird.

Wenn Sie die App-Registrierung abgeschlossen haben, verfügen Sie über eine global eindeutige Instanz der App (das Anwendungsobjekt), die sich in Ihrem Basismandanten oder -verzeichnis befindet. Außerdem verfügen Sie über eine global eindeutige ID für Ihre App (App- oder Client-ID). Im Portal können Sie dann Geheimnisse oder Zertifikate und Geltungsbereiche hinzufügen, damit Ihre App funktioniert. Sie können auch das Branding Ihrer App im Anmeldedialogfeld anpassen und vieles mehr.

Wenn Sie eine Anwendung im Portal registrieren, werden automatisch ein Anwendungsobjekt sowie ein Dienstprinzipalobjekt in Ihrem Basismandanten erstellt. Wenn Sie eine Anwendung mit den Microsoft Graph-APIs registrieren/erstellen, wird das Dienstprinzipalobjekt in einem separaten Schritt erstellt. Ein Dienstprinzipalobjekt ist erforderlich, um Token anzufordern.

Denken Sie daran, die [Sicherheitscheckliste](../active-directory/develop/identity-platform-integration-checklist.md#security) noch einmal für Ihre Anwendung durchzugehen. Es hat sich bewährt, die [Zertifikatanmeldeinformationen](../active-directory/develop/active-directory-certificate-credentials.md) zu verwenden, nicht die Kennwortanmeldeinformationen (Clientgeheimnisse).

Weitere Informationen finden Sie unter [Anwendungs- und Dienstprinzipalobjekte in Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

## <a name="step-1-create-your-managed-identity-or-app-registration"></a>Schritt 1: Erstellen der verwalteten Identität oder App-Registrierung

Nachdem Sie festgestellt haben, ob Sie eine verwaltete Identität oder eine App-Registrierung verwenden werden, besteht der nächste Schritt in der Bereitstellung einer verwalteten Identität oder App-Registrierung.

### <a name="managed-identity"></a>Verwaltete Identität

Die Schritte für die Erstellung einer verwalteten Identität hängen davon ab, wo Ihr Code gehostet wird und ob Sie eine system- oder benutzerseitig zugewiesene Identität erstellen. Ausführlichere Informationen zum Unterschied finden Sie unter [Typen verwalteter Identitäten](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types). Nachdem Sie einen Identitätstyp ausgewählt haben, suchen Sie in der [Dokumentation](../active-directory/managed-identities-azure-resources/index.yml) zu verwalteten Azure AD-Identitäten das richtige Tutorial, und befolgen Sie die darin beschriebenen Schritte. Sie enthalten Anweisungen zur Konfiguration von verwalteten Identitäten für:

- [Virtuelle Azure-Computer](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-during-creation-of-a-vm)
- [App Service und Azure Functions](../app-service/overview-managed-identity.md)
- [Azure Container Instances](../container-instances/container-instances-managed-identity.md)
- und viele weitere

### <a name="application-registration"></a>Anwendungsregistrierung

Befolgen Sie die Anleitung unter [Registrieren einer Anwendung](../active-directory/develop/quickstart-register-app.md#register-an-application).

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

## <a name="step-2-grant-access"></a>Schritt 2: Gewähren des Zugriffs

Wenn Ihre Azure Time Series Insights-Umgebung eine Anforderung empfängt, wird zuerst das Bearertoken des Aufrufers überprüft. Wenn die Überprüfung erfolgreich verläuft, wurde der Aufrufer authentifiziert, und es wird eine weitere Überprüfung durchgeführt, um sicherzustellen, dass der Aufrufer zur Durchführung der angeforderten Aktion auch autorisiert ist. Wenn Sie einen Benutzer oder Dienstprinzipal autorisieren möchten, müssen Sie ihm zuerst Zugriff auf die Umgebung erteilen, indem Sie ihm entweder die Rolle „Leser“ oder „Mitwirkender“ zuweisen.

- Zugriff über das [Azure-Portal](https://portal.azure.com/) erteilen: Befolgen Sie die Anleitung im Artikel [Gewähren von Datenzugriff auf eine Umgebung](concepts-access-policies.md). Wenn Sie den Benutzer auswählen, können Sie über den Namen oder die ID nach der verwalteten Identität bzw. App-Registrierung suchen.

- Zugriff über die Azure CLI erteilen: Führen Sie den folgenden Befehl aus. Eine vollständige Liste der für die Zugriffsverwaltung verfügbaren Befehle finden Sie [in der Dokumentation](/cli/azure/tsi/access-policy).

   ```azurecli-interactive
   az tsi access-policy create --name "ap1" --environment-name "env1" --description "some description" --principal-object-id "aGuid" --roles Reader Contributor --resource-group "rg1"
   ```

> [!Note]
> Die Erweiterung „timeseriesinsights“ für die Azure CLI erfordert Version 2.11.0 oder höher. Die Erweiterung wird automatisch installiert, wenn Sie einen „az tsi access-policy“-Befehl zum ersten Mal ausführen. [Weitere Informationen zu Erweiterungen](/cli/azure/azure-cli-extensions-overview)

## <a name="step-3-requesting-tokens"></a>Schritt 3: Anfordern von Token

Nachdem die verwaltete Identität oder App-Registrierung bereitgestellt und mit einer Rolle versehen wurde, können Sie damit OAuth 2.0-Bearertoken anfordern. Die Methode, mit der Sie ein Token abrufen, hängt davon ab, wo Ihr Code gehostet wird und welche Sprache Sie auswählen. Wenn Sie die Ressource angeben (auch die „Zielgruppe“ des Tokens genannt), können Sie Azure Time Series Insights anhand der URL oder der GUID identifizieren:

* `https://api.timeseries.azure.com/`
* `120d688d-1518-4cf7-bd38-182f158850b6`

> [!IMPORTANT]
> Wenn Sie die URL als Ressourcen-ID verwenden, muss das Token genau für `https://api.timeseries.azure.com/` ausgestellt werden. Der nachstehende Schrägstrich ist erforderlich.

> * Wenn Sie [Postman](https://www.getpostman.com/) verwenden, lautet **AuthURL** daher: `https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?scope=https://api.timeseries.azure.com//.default`
> * `https://api.timeseries.azure.com/` ist gültig, `https://api.timeseries.azure.com` jedoch nicht.

### <a name="managed-identities"></a>Verwaltete Identitäten

Wenn der Zugriff über Azure App Service oder Azure Functions erfolgt, befolgen Sie die Anweisungen unter [Abrufen von Token für Azure-Ressourcen](../app-service/overview-managed-identity.md).

In .NET-Anwendungen und -Funktionen ist es am einfachsten, über die [Azure Identity-Clientbibliothek](/dotnet/api/overview/azure/identity-readme) mit einer verwalteten Identität zu arbeiten. Die Clientbibliothek ist aufgrund ihrer Einfachheit und ihrer Sicherheitsvorteile beliebt. Entwickler können Code einmal schreiben und die Clientbibliothek auf Grundlage der Anwendungsumgebung über die Authentifizierung bestimmen lassen. Entscheidend ist, ob die Umgebung über ein Entwicklerkonto auf einer Entwicklungsarbeitsstation oder mithilfe einer verwalteten Dienstidentität in Azure bereitgestellt wurde. Eine Migrationsanleitung zur Vorgängerbibliothek „AppAuthentication“ finden Sie unter [Leitfaden für die Migration von AppAuthentication zu Azure.Identity](/dotnet/api/overview/azure/app-auth-migration).

Fordern Sie mit C# und der Azure Identity-Clientbibliothek für .NET ein Token für Azure Time Series Insights an:

   ```csharp
   using Azure.Identity;
   // ...
   var credential = new DefaultAzureCredential();
   var token = credential.GetToken(
   new Azure.Core.TokenRequestContext(
       new[] { "https://api.timeseries.azure.com/" }));
   var accessToken = token.Token;
   ```

### <a name="app-registration"></a>App-Registrierung

* Entwickler können Token für App-Registrierungen über die [Microsoft-Authentifizierungsbibliothek](../active-directory/develop/msal-overview.md) (Microsoft Authentication Library, MSAL) abrufen.

MSAL kann in vielen Anwendungsszenarios verwendet werden, unter anderem:

* [Single-Page-Anwendungen (JavaScript)](../active-directory/develop/scenario-spa-overview.md)
* [Webanwendungen, die einen Benutzer anmelden und eine Web-API im Namen des Benutzers aufrufen](../active-directory/develop/scenario-web-app-call-api-overview.md)
* [Eine Web-API, die im Namen des angemeldeten Benutzers eine andere Downstream-Web-API aufruft](../active-directory/develop/scenario-web-api-call-api-overview.md)
* [Eine Desktopanwendung, die im Namen des angemeldeten Benutzers eine Web-API aufruft](../active-directory/develop/scenario-desktop-overview.md)
* [Mobile Anwendung, die eine Web-API im Namen des Benutzers aufruft, der sich interaktiv angemeldet hat](../active-directory/develop/scenario-mobile-overview.md).
* [Eine Desktop-/Servicedaemonanwendung, die eine Web-API im eigenen Namen aufruft](../active-directory/develop/scenario-daemon-overview.md)

C#-Beispielcode zum Abrufen eines Tokens als App-Registrierung und zum Abfragen von Daten aus einer Gen2-Umgebung finden Sie in der Beispiel-App auf [GitHub](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClientSampleApp/Program.cs).

> [!IMPORTANT]
> Wenn Sie die [Active Directory-Authentifizierungsbibliothek (ADAL)](../active-directory/azuread-dev/active-directory-authentication-libraries.md) verwenden, informieren Sie sich über die [Migration zu MSAL](../active-directory/develop/msal-net-migration.md).

## <a name="common-headers-and-parameters"></a>Allgemeine Parameter und Header

In diesem Abschnitt werden allgemeine HTTP-Anforderungsheader und Parameter beschrieben, mit denen Abfragen der APIs für Azure Time Series Insights Gen1 und Gen2 durchgeführt werden. API-spezifische Anforderungen werden ausführlicher in der [Referenzdokumentation zur Azure Time Series Insights-REST-API](/rest/api/time-series-insights/) behandelt.

> [!TIP]
> Weitere Informationen zum Nutzen von REST-APIs, Ausführen von HTTP-Anforderungen und Verarbeiten von HTTP-Antworten finden Sie in der [Azure-Rest-API-Referenz](/rest/api/azure/).

### <a name="http-headers"></a>HTTP-Header

Erforderliche Anforderungsheader werden nachfolgend beschrieben.

| Erforderlicher Anforderungsheader | BESCHREIBUNG |
| --- | --- |
| Authorization | Für die Authentifizierung bei Azure Time Series Insights muss im [Autorisierungsheader](/rest/api/apimanagement/2020-12-01/authorization-server/create-or-update) ein gültiges OAuth 2.0-Bearertoken übergeben werden. |

> [!TIP]
> Weitere Informationen zur programmgesteuerten Authentifizierung mit den APIs von Azure Time Series Insights unter Verwendung des [JavaScript Client SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) zusammen mit Diagrammen und Grafiken finden Sie in der gehosteten [Beispielvisualisierung des Azure Time Series Insights-Client-SDK](https://tsiclientsample.azurewebsites.net/).

Optionale Anforderungsheader werden nachfolgend beschrieben.

| Optionaler Anforderungsheader. | BESCHREIBUNG |
| --- | --- |
| Inhaltstyp | Nur `application/json` wird unterstützt. |
| x-ms-client-request-id | Eine Clientanforderungs-ID. Der Dienst zeichnet diesen Wert auf. Ermöglicht es dem Dienst, den Vorgang dienstübergreifend nachzuverfolgen. |
| x-ms-client-session-id | Eine Clientsitzungs-ID. Der Dienst zeichnet diesen Wert auf. Ermöglicht es dem Dienst, eine Gruppe verwandter Vorgänge dienstübergreifend nachzuverfolgen. |
| x-ms-client-application-name | Der Name der Anwendung, die diese Anforderung generiert hat. Der Dienst zeichnet diesen Wert auf. |

Optionale, aber empfohlene Antwortheader werden unten beschrieben.

| Antwortheader | BESCHREIBUNG |
| --- | --- |
| Inhaltstyp | Nur `application/json` wird unterstützt. |
| x-ms-request-id | Vom Server generierte Anforderungs-ID. Kann zum Kontaktieren von Microsoft verwendet werden, um eine Anforderung zu untersuchen. |
| x-ms-property-not-found-behavior | Optionaler GA-API-Antwortheader. Mögliche Werte sind: `ThrowError` (Standard) oder `UseNull`. |

### <a name="http-parameters"></a>HTTP-Parameter

> [!TIP]
> Weitere Informationen zu erforderlichen und optionalen Abfrageinformationen finden Sie in der [Referenzdokumentation](/rest/api/time-series-insights/).

Die erforderlichen URL-Abfragezeichenfolgenparameter hängen von der API-Version ab.

| Release | API-Versionswerte |
| --- |  --- |
| Gen1 | `api-version=2016-12-12`|
| Gen2 | `api-version=2020-07-31`|

Optionale URL-Abfragezeichenfolgen-Parameter umfassen das Festlegen eines Timeouts für die HTTP-Anforderungsausführungszeiten.

| Optionaler Abfrageparameter | BESCHREIBUNG | Version |
| --- |  --- | --- |
| `timeout=<timeout>` | Das serverseitige Timeout für die Ausführung der HTTP-Anforderung. Gilt nur für die APIs zum [Abrufen von Umgebungsereignissen](/rest/api/time-series-insights/dataaccessgen2/query/get-availability) und [Abrufen von Umgebungsaggregaten](/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-api). Der Timeoutwert muss das ISO 8601-Format für die Dauer aufweisen (z.B. `"PT20S"`) und sollte im Bereich `1-30 s` liegen. Der Standardwert ist `30 s`. | Gen1 |
| `storeType=<storeType>` | Für Gen2-Umgebungen mit aktiviertem Warm Storage kann die Abfrage entweder für den `WarmStore` oder den `ColdStore` ausgeführt werden. Dieser Parameter in der Abfrage definiert, in welchem Speicher die Abfrage ausgeführt werden soll. Wenn nicht definiert, wird die Abfrage im kalten Speicher ausgeführt. Um den warmen Speicher abzufragen, muss **storeType** auf `WarmStore` festgelegt werden. Wenn nicht definiert, wird die Abfrage im kalten Speicher ausgeführt. | Gen2 |

## <a name="next-steps"></a>Nächste Schritte

* Beispielcode, in dem die Gen1 Azure Time Series Insights-API aufgerufen wird, finden Sie unter [Abfragen von Gen1-Daten mit C#](./time-series-insights-query-data-csharp.md).

* Beispielcode, in dem die Gen2 Azure Time Series Insights-API aufgerufen wird, finden Sie unter [Abfragen von Gen2-Daten mit C#](./time-series-insights-update-query-data-csharp.md).

* API-Referenzinformationen finden Sie in der [Abfrage-API-Referenzdokumentation](/rest/api/time-series-insights/reference-query-apis).
