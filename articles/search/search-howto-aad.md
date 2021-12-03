---
title: Autorisieren von Suchanforderungen mit Azure AD
titleSuffix: Azure Cognitive Search
description: Abrufen eines Tokens von Azure AD zum Autorisieren von Suchanforderungen
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/04/2021
ms.openlocfilehash: d4053d64b8a35bf13b10a47a685b838d89a64dc3
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132518143"
---
# <a name="authorize-search-requests-using-azure-ad-preview"></a>Autorisieren von Suchanforderungen mit Azure AD (Vorschau)

> [!IMPORTANT]
> Die rollenbasierte Zugriffssteuerung für Datenebenenvorgänge wie das Erstellen oder Abfragen eines Indexes befindet sich derzeit in der öffentlichen Vorschau und wird gemäß den [zusätzlichen Nutzungsbestimmungen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) zur Verfügung gestellt. Diese Funktionalität ist nur in öffentlichen Cloudregionen verfügbar und kann sich auf die Latenz Ihrer Vorgänge auswirken, während sich die Funktionalität in der Vorschau befindet.

Mit Azure Active Directory (Azure AD) können Sie die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) verwenden, um Zugriff auf Ihre Azure Cognitive Search-Dienste zu gewähren. Ein wesentlicher Vorteil der Verwendung von Azure AD besteht darin, dass Ihre Anmeldeinformationen nicht mehr im Code gespeichert werden müssen. Azure AD übernimmt die Authentifizierung des Sicherheitsprinzipals (Benutzer, Gruppe oder Dienstprinzipal), der die Anwendung ausführt. Wenn die Authentifizierung erfolgreich ist, gibt Azure AD das Zugriffstoken an die Anwendung zurück, und die Anwendung kann dieses dann zum Autorisieren von Anforderungen an Azure Cognitive Search verwenden. Weitere Informationen zu den Vorteilen der Verwendung von Azure AD in Ihren Anwendungen finden Sie unter [Vorteile der Integration](../active-directory/develop/active-directory-how-to-integrate.md#benefits-of-integration).

In diesem Artikel erfahren Sie, wie Sie Ihre Anwendung für die Authentifizierung mit der Microsoft Identity Platform konfigurieren. Informationen zur Microsoft Identity Platform finden Sie in der [Übersicht über die Microsoft Identity Platform](../active-directory/develop/v2-overview.md). Informationen zum von Azure AD verwendeten Flow für OAuth 2.0-Codeberechtigungen finden Sie unter [Microsoft Identity Platform und der OAuth 2.0-Autorisierungscodeflow](../active-directory/develop/v2-oauth2-auth-code-flow.md).

## <a name="prepare-your-search-service"></a>Vorbereiten des Suchdiensts

Im ersten Schritt [erstellen Sie einen Suchdienst](search-create-service-portal.md) und konfigurieren ihn für die Verwendung der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC).

### <a name="sign-up-for-the-preview"></a>Anmelden für die Vorschau

Die Teile der RBAC-Funktionen von Azure Cognitive Search, die für die Verwendung von Azure AD zur Abfrage des Suchdienstes erforderlich sind, befinden sich noch in der Vorschau. Um diese Funktionen zu nutzen, müssen Sie die Vorschaufunktion zu Ihrem Azure-Abonnement hinzufügen.

Um Ihr Abonnement zur Vorschau hinzuzufügen:

1. Navigieren Sie zu Ihrem Suchdienst im [Azure-Portal](https://portal.azure.com/).
1. Wählen Sie auf der linken Seite die Option **Schlüssel** aus.
1. Wählen Sie im blauen Banner, in dem die Vorschau erwähnt wird, **Registrieren** aus, um das Feature Ihrem Abonnement hinzuzufügen.

![Screenshot: Registrieren für die RBAC-Vorschau im Portal](media/search-howto-aad/rbac-signup-portal.png)

Sie können sich auch für die Vorschau registrieren, indem Sie Azure Feature Exposure Control (AFEC) verwenden und nach *Rollenbasierte Zugriffssteuerung für Suchdienst (Vorschau)* suchen. Weitere Informationen zum Hinzufügen von Vorschaufeatures finden Sie unter [Einrichten von Vorschaufeatures im Azure-Abonnement](../azure-resource-manager/management/preview-features.md?tabs=azure-portal).

> [!NOTE]
> Nachdem Sie Die Vorschau zu Ihrem Abonnement hinzugefügt haben, werden alle Dienste im Abonnement dauerhaft in der Vorschau registriert. Wenn Sie die rollenbasierte Zugriffssteuerung (RBAC) für einen bestimmten Dienst nicht verwenden möchten, können Sie RBAC für Vorgänge auf Datenebene deaktivieren, wie im nächsten Schritt gezeigt.

### <a name="enable-rbac-for-data-plane-operations"></a>Aktivieren von RBAC für Datenebenenvorgänge

Nachdem Ihr Abonnement zur Vorschauversion hinzugefügt wurde, müssen Sie RBAC für Datenebenenvorgänge aktivieren, damit Sie die Azure AD-Authentifizierung verwenden können. Standardmäßig verwendet Azure Cognitive Search die schlüsselbasierte Authentifizierung für Datenebenenvorgänge, Sie können die Einstellung jedoch ändern, um die rollenbasierte Zugriffssteuerung zuzulassen. 

So aktivieren Sie die rollenbasierte Zugriffssteuerung:

1. Navigieren Sie zu Ihrem Suchdienst im [Azure-Portal](https://portal.azure.com/).
1. Wählen Sie im linken Navigationsbereich **Tasten**.
1. Legen Sie fest, ob Sie sowohl die schlüsselbasierte als auch die rollenbasierte Zugriffssteuerung oder nur die rollenbasierte Zugriffssteuerung zulassen möchten.

![Screenshot: Authentifizierungsoptionen für Azure Cognitive Search im Portal](media/search-howto-aad/portal-api-access-control.png)

Sie können diese Einstellungen auch programmgesteuert ändern, wie in der [Dokumentation zu RBAC in Azure Cognitive Search](./search-security-rbac.md?tabs=config-svc-rest%2croles-powershell%2ctest-rest#step-2-preview-configuration) beschrieben.

## <a name="register-an-application-with-azure-ad"></a>Registrieren einer Anwendung in Azure AD

Der nächste Schritt bei der Verwendung von Azure AD für die Authentifizierung besteht darin, eine Anwendung bei der [Microsoft Identity Platform](../active-directory/develop/quickstart-register-app.md) zu registrieren. Wenn beim Erstellen der Anwendung Probleme auftreten, überprüfen Sie, ob Sie über die [erforderlichen Berechtigungen zum Registrieren einer Anwendung](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app) verfügen.

So registrieren Sie eine Anwendung bei Azure AD:

1. Melden Sie sich im [Azure-Portal](https://portal.azure.com) bei Ihrem Azure-Konto an.
1. Wählen Sie **Azure Active Directory** aus.
1. Klicken Sie auf **App-Registrierungen**.
1. Wählen Sie **Neue Registrierung** aus.
1. Geben Sie Ihrer Anwendung einen Namen, und wählen Sie einen unterstützten Kontotyp aus, der bestimmt, wer die Anwendung verwenden kann. Wählen Sie dann **Registrieren**.

![Screenshot: Assistent zum Registrieren einer Anwendung](media/search-howto-aad/register-app.png)

Damit haben Sie Ihre Azure AD-Anwendung und den Dienstprinzipal erstellt. Notieren Sie sich die Mandanten-ID (Verzeichnis-ID) und die Client-ID (Anwendungs-ID) auf der Übersichtsseite Ihrer App-Registrierung. Sie benötigen diese Werte in einem späteren Schritt.

## <a name="create-a-client-secret"></a>Erstellen eines Clientgeheimnisses

Die Anwendung benötigt auch einen geheimen Clientschlüssel oder ein Zertifikat, um beim Anfordern eines Tokens ihre Identität nachzuweisen. In diesem Dokument erfahren Sie, wie Sie einen geheimen Clientschlüssel verwenden.

1. Navigieren Sie zu der erstellten App-Registrierung.
1. Wählen Sie **Zertifikate und Geheimnisse** aus.
1. Wählen Sie unter **Geheime Clientschlüssel** die Option **Neuer geheimer Clientschlüssel**.
1. Geben Sie eine Beschreibung des geheimen Schlüssels an, und wählen Sie das gewünschte Ablaufintervall aus.

![Screenshot: Assistent zum Erstellen eines geheimen Clientschlüssels](media/search-howto-aad/create-secret.png)

Stellen Sie sicher, dass Sie den Wert des geheimen Schlüssels an einem sicheren Ort speichern, da Sie nicht erneut auf den Wert zugreifen können. 

## <a name="grant-your-application-permissions-to-azure-cognitive-search"></a>Erteilen von Anwendungsberechtigungen für Azure Cognitive Search

Als Nächstes müssen Sie Ihrer Azure AD Anwendung Zugriff auf Ihren Suchdienst gewähren. Azure Cognitive Search verfügt über verschiedene [integrierte Rollen](./search-security-rbac.md?tabs=config-svc-portal%2croles-portal%2ctest-portal#built-in-roles-used-in-search), die abhängig von dem Zugriff verwendet werden können, den Ihre Anwendung erfordert.

Im Allgemeinen ist es am besten, Ihrer Anwendung nur den erforderlichen Zugriff zu gewähren. Wenn Ihre Anwendung beispielsweise nur den Suchindex abfragen muss, können Sie ihr die Rolle [Suchindexdatenleser (Vorschau)](../role-based-access-control/built-in-roles.md#search-index-data-reader) gewähren. Wenn sie in der Lage sein muss, einen Suchindex zu lesen und darin zu schreiben, können Sie alternativ die Rolle [Mitwirkender an Suchindexdaten (Vorschau)](../role-based-access-control/built-in-roles.md#search-index-data-contributor) verwenden.

So weisen Sie Ihrer App-Registrierung eine Rolle zu:

1. Öffnen Sie das Azure-Portal, und navigieren Sie zu Ihrem Suchdienst.
1. Wählen Sie im linken Navigationsbereich **Access Control (IAM)** aus.
1. Wählen Sie auf der rechten Seite unter **Zugriff auf diese Ressource gewähren** die Option **Rollenzuweisung hinzufügen** aus.
1. Wählen Sie die Rolle, die Sie verwenden möchten, und dann **Weiter** aus.
1. Wählen Sie auf der nächsten Seite **Mitglieder auswählen** aus, und suchen Sie die Anwendung, die Sie zuvor erstellt haben. 
1. Wählen Sie schließlich **Überprüfen und zuweisen** aus.

![Screenshot: Hinzufügen einer Rollenzuweisung im Azure-Portal](media/search-howto-aad/role-assignment.png)

Sie können [Rollen auch mithilfe von PowerShell zuweisen](./search-security-rbac.md?tabs=config-svc-rest%2croles-powershell%2ctest-rest#step-3-assign-roles).

### <a name="create-a-custom-role"></a>Erstellen einer benutzerdefinierten Rolle

Zusätzlich zur Verwendung [integrierter Rollen](./search-security-rbac.md?tabs=config-svc-portal%2croles-portal%2ctest-portal#built-in-roles-used-in-search) können Sie auch eine [benutzerdefinierte Rolle](../role-based-access-control/custom-roles.md) erstellen, um genau zu definieren, was Ihrer Anwendung erlaubt sein soll.

Wenn Sie beispielsweise eine Rolle wünschen, mit der sich Indizes vollständig verwalten lassen, einschließlich der Möglichkeit, Indizes zu erstellen und Daten daraus zu lesen, können Sie die folgende Rolle definieren:

```json
{
  "Name": "Search Index Manager",
  "Id": "88888888-8888-8888-8888-888888888888",
  "IsCustom": true,
  "Description": "Can manage search indexes and read or write to them",
  "Actions": [
    "Microsoft.Search/searchServices/indexes/*",
    
  ],
  "NotActions": [],
  "DataActions": [
      "Microsoft.Search/searchServices/indexes/documents/*"
  ],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}"
  ]
}
```

Benutzerdefinierte Rollen können im [Azure-Portal](../role-based-access-control/custom-roles-portal.md), mit [Azure PowerShell](../role-based-access-control/custom-roles-powershell.md), [Azure CLI](../role-based-access-control/custom-roles-cli.md) oder der [REST-API](../role-based-access-control/custom-roles-rest.md) erstellt werden. Der obige JSON-Code zeigt die Syntax zum Erstellen einer benutzerdefinierten Rolle mit PowerShell.

Eine vollständige Liste der verfügbaren Vorgänge finden Sie unter [Microsoft.Search-Ressourcenanbietervorgänge](../role-based-access-control/resource-provider-operations.md#microsoftsearch).


### <a name="grant-access-to-only-a-single-index"></a>Gewähren des Zugriffs nur auf einen einzelnen Index

In einigen Szenarien kann es sinnvoll sein, den Zugriff einer Anwendung auf eine einzelne Ressource zu beschränken, z. B. einen Index. 

Das Portal unterstützt das Gewähren des Zugriffs auf einen einzelnen Index derzeit nicht, Sie können dies aber über [PowerShell](../role-based-access-control/role-assignments-powershell.md) oder die [Azure CLI](../role-based-access-control/role-assignments-cli.md) einrichten.

In PowerShell verwenden Sie dafür den Befehl [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) und geben den Namen des Azure-Benutzers oder der Azure-Gruppe sowie den Umfang der Zuweisung an.

Stellen Sie vor Beginn sicher, dass Sie die Azure- und Azure AD-Module geladen und eine Verbindung mit Ihrem Azure-Konto hergestellt haben:

```powershell
Import-Module -Name Az
Import-Module -Name AzureAD
Connect-AzAccount
```

Um eine auf einen einzelnen Index beschränkte Rollenzuweisung hinzuzufügen, führen Sie dann den folgenden Befehl aus:

```powershell
New-AzRoleAssignment -ObjectId <objectId> `
    -RoleDefinitionName "Search Index Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Search/searchServices/<search-service>/indexes/<index-name>"
```

## <a name="set-up-azure-ad-authentication-in-your-client"></a>Einrichten der Azure AD-Authentifizierung in Ihrem Client

Nachdem Sie eine Azure AD Anwendung erstellt und ihr Berechtigungen für den Zugriff auf Ihren Suchdienst erteilt haben, können Sie der Anwendung Code hinzufügen, um einen Sicherheitsprinzipal zu authentifizieren und ein OAuth 2.0-Token abzurufen.

### <a name="azure-ad-authentication-with-the-net-sdk"></a>Azure AD-Authentifizierung mit dem .NET SDK

Die Azure SDKs erleichtern die Integration in Azure AD. Version [11.4.0-beta.2](https://www.nuget.org/packages/Azure.Search.Documents/11.4.0-beta.2) und höher des .NET SDK unterstützen die Azure AD-Authentifizierung. Die Azure AD-Authentifizierung wird auch in den Vorschau-SDKs für [Java](https://search.maven.org/artifact/com.azure/azure-search-documents/11.5.0-beta.3/jar), [Python](https://pypi.org/project/azure-search-documents/11.3.0b3/) und [JavaScript](https://www.npmjs.com/package/@azure/search-documents/v/11.3.0-beta.3) unterstützt.

Als Erstes klonen Sie den [Quellcode](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11) für den [C#-Schnellstart](search-get-started-dotnet.md).  Der Schnellstart verwendet derzeit die schlüsselbasierte Authentifizierung, um den `SearchClient` und den `SearchIndexClient` zu erstellen. Sie können jedoch eine kleine Änderung vornehmen, um zur rollenbasierten Authentifizierung zu wechseln. Anstatt in [Program.cs](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/quickstart/v11/AzureSearchQuickstart-v11/Program.cs) `AzureKeyCredential` am Anfang von `Main()` zu verwenden, verwenden Sie folgenden Code: 

```dotnet
AzureKeyCredential credential = new AzureKeyCredential(apiKey);

// Create a SearchIndexClient to send create/delete index commands
SearchIndexClient adminClient = new SearchIndexClient(serviceEndpoint, credential);
// Create a SearchClient to load and query documents
SearchClient srchclient = new SearchClient(serviceEndpoint, indexName, credential);
```

Sie können `ClientSecretCredential` verwenden, um sich beim Suchdienst zu authentifizieren.

Dafür benötigen Sie Folgendes:
+ Die ID des Mandanten (oder des Verzeichnisses). Diese kann auf der Übersichtsseite Ihrer App-Registrierung abgerufen werden.
+ Die Client-ID (oder Anwendungs-ID). Diese kann auf der Übersichtsseite Ihrer App-Registrierung abgerufen werden.
+ Der Wert des geheimen Clientschlüssels, den Sie in einem der vorherigen Schritte kopiert haben.

```dotnet
var tokenCredential =  new ClientSecretCredential(aadTenantId, aadClientId, aadSecret);
SearchIndexClient adminClient = new SearchIndexClient(serviceEndpoint, tokenCredential);
```

Sie müssen die [Azure.Identity](https://www.nuget.org/packages/Azure.Identity/)-Bibliothek importieren, um `ClientSecretCredential` zu verwenden.

Die Azure.Identity-Dokumentation enthält auch weitere Details zur Verwendung der [Azure AD-Authentifizierung mit dem Azure SDK für .NET](/dotnet/api/overview/azure/identity-readme).

### <a name="azure-ad-authentication-with-the-rest-api"></a>Azure AD-Authentifizierung mit der REST-API

Die Verwendung eines Azure SDK vereinfacht den OAuth 2.0-Flow, Sie können aber auch direkt für das Protokoll in Ihrer Anwendung programmieren. Vollständige Details finden Sie unter [Flow der OAuth 2.0-Clientanmeldeinformationen in Microsoft Identity Platform](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md).

#### <a name="get-a-token"></a>Abrufen von Token

Zunächst müssen Sie von der Microsoft Identity Platform [ein Token abrufen](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md#get-a-token):

```
POST /[tenant id]/oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=[client id]
&scope=https%3A%2F%2Fsearch.azure.com%2F.default
&client_secret=[client secret]
&grant_type=client_credentials
```

Der erforderliche Bereich lautet „https://search.azure.com/.default“. 

#### <a name="use-a-token"></a>Verwenden eines Tokens

Nachdem Sie nun über ein Token verfügen, können Sie eine Anforderung an den Suchdienst senden. 

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs?[query parameters]
Content-Type: application/json   
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="see-also"></a>Siehe auch

+ [Verwenden der rollenbasierten Autorisierung in Azure Cognitive Search](search-security-rbac.md)
+ [Autorisieren des Zugriffs auf Azure Active Directory-Webanwendungen mit dem Flow zum Erteilen des OAuth 2.0-Codes](../active-directory/develop/v2-oauth2-auth-code-flow.md)
+ [Integration in Azure Active Directory](../active-directory/develop/active-directory-how-to-integrate.md#benefits-of-integration)
+ [Benutzerdefinierte Azure-Rollen](../role-based-access-control/custom-roles.md)