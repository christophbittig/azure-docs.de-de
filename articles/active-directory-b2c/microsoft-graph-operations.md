---
title: Verwalten von Ressourcen mit Microsoft Graph
titleSuffix: Azure AD B2C
description: Erfahren Sie mehr zum Verwalten von Ressourcen in einem Azure AD B2C-Mandanten durch das Aufrufen der Microsoft Graph-API und Automatisieren des Vorgangs mithilfe einer Anwendungsidentität.
services: B2C
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/08/2021
ms.custom: project-no-code, ignite-fall-2021, b2c-support
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: c7d64e4991a71849f8a0269dcfa8eff32164ef75
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132133712"
---
# <a name="manage-azure-ad-b2c-with-microsoft-graph"></a>Verwalten von Azure AD B2C mit Microsoft Graph

Mit Microsoft Graph können Sie Ressourcen in Ihrem Azure AD B2C-Verzeichnis verwalten. In der Microsoft Graph-API werden die folgenden Vorgänge für die Verwaltung von Azure AD B2C-Ressourcen unterstützt (einschließlich Benutzern, Identitätsanbietern, Benutzerflows, benutzerdefinierten Richtlinien und Richtlinienschlüsseln). Jeder Link in den folgenden Abschnitten bezieht sich auf die entsprechende Seite in der Referenz zur Microsoft Graph-API für diesen Vorgang. 

> [!NOTE]
> Sie können auch programmgesteuert ein Azure AD B2C-Verzeichnis selbst erstellen, zusammen mit der entsprechenden Azure-Ressource, die mit einem Azure-Abonnement verknüpft ist. Diese Funktionalität wird nicht über die Microsoft Graph-API bereitgestellt, sondern über die Azure-REST-API. Weitere Informationen finden Sie unter [B2C-Mandanten: Erstellen](/rest/api/activedirectory/b2c-tenants/create).

Sehen Sie sich dieses Video an, um mehr über die Migration von Azure AD B2C-Benutzern mithilfe der Microsoft Graph API zu erfahren.

>[!Video https://www.youtube.com/embed/9BRXBtkBzL4]

## <a name="prerequisites"></a>Voraussetzungen

Zum Verwenden der MS Graph-API und Interagieren mit Ressourcen auf Ihrem Azure AD B2C-Mandanten benötigen Sie eine Anwendungsregistrierung, bei der die erforderlichen Berechtigungen gewährt werden. Führen Sie die Schritte im Artikel [Verwalten von Azure AD B2C mit Microsoft Graph](microsoft-graph-get-started.md) aus, um eine Anwendungsregistrierung zu erstellen, die von Ihrer Verwaltungsanwendung genutzt werden kann. 

## <a name="user-management"></a>Benutzerverwaltung
> [!NOTE]
> Azure AD B2C unterstützt derzeit keine erweiterten Abfragefunktionen für Verzeichnisobjekte. Dies bedeutet, dass keine Unterstützung für `$count`, `$search`Abfrageparameter und Not (`not`), Not equals (`ne`) und Ends with (`endsWith`) im Abfrageparameter `$filter` gibt. Weitere Informationen finden Sie unter [Abfrageparameter in Microsoft Graph](/graph/query-parameters) und [ erweiterte Abfragefunktionen in Microsoft Graph](/graph/aad-advanced-queries).


- [Auflisten von Benutzern](/graph/api/user-list)
- [Erstellen eines Consumerbenutzers](/graph/api/user-post-users)
- [Abrufen eines Benutzers](/graph/api/user-get)
- [Aktualisieren eines Benutzers](/graph/api/user-update)
- [Löschen eines Benutzers](/graph/api/user-delete)

## <a name="user-phone-number-management-beta"></a>Verwaltung der Telefonnummer des Benutzers (Beta)

Eine Telefonnummer, mit der sich ein Benutzer per [SMS oder Sprachanruf](sign-in-options.md#phone-sign-in) oder per [mehrstufiger Authentifizierung](multi-factor-authentication.md) anmelden kann. Weitere Informationen finden Sie unter [Ressourcentyp „phoneAuthenticationMethod“](/graph/api/resources/phoneauthenticationmethod).

- [Add (Hinzufügen)](/graph/api/authentication-post-phonemethods)
- [Liste](/graph/api/authentication-list-phonemethods)
- [Get](/graph/api/phoneauthenticationmethod-get)
- [Aktualisieren](/graph/api/phoneauthenticationmethod-update)
- [Löschen](/graph/api/phoneauthenticationmethod-delete)

Hinweis: Durch den [Auflistungsvorgang](/graph/api/authentication-list-phonemethods) werden nur aktivierte Telefonnummern zurückgegeben. Die folgende Telefonnummer muss aktiviert werden, um mit den Auflistungsvorgängen verwendet werden zu können. 

![Aktivieren der Anmeldung per Telefon](./media/microsoft-graph-operations/enable-phone-sign-in.png)

> [!NOTE]
> In der aktuellen Betaversion funktioniert diese API nur, wenn die Telefonnummer mit einem Leerzeichen zwischen der Länderkennzahl und der Telefonnummer gespeichert wird. Der Azure AD B2C-Dienst fügt dieses Leerzeichen derzeit nicht standardmäßig hinzu.

## <a name="self-service-password-reset-email-address-beta"></a>E-Mail-Adresse für Self-Service-Kennwortzurücksetzung (Beta)

Eine E-Mail-Adresse, die von einem [Konto mit benutzernamenbasierter Anmeldung](sign-in-options.md#username-sign-in) verwendet werden kann, um das Kennwort zurückzusetzen. Weitere Informationen finden Sie unter [Ressourcentyp „emailAuthenticationMethod“](/graph/api/resources/emailauthenticationmethod).

- [Add (Hinzufügen)](/graph/api/emailauthenticationmethod-post)
- [Liste](/graph/api/emailauthenticationmethod-list)
- [Get](/graph/api/emailauthenticationmethod-get)
- [Aktualisieren](/graph/api/emailauthenticationmethod-update)
- [Löschen](/graph/api/emailauthenticationmethod-delete)

## <a name="identity-providers"></a>Identitätsanbieter

Verwalten Sie die [Identitätsanbieter](add-identity-provider.md), die für die Benutzerflows in Ihrem Azure AD B2C-Mandanten verfügbar sind.

- [Auflisten von Identitätsanbietern, die im Azure AD B2C-Mandanten registriert sind](/graph/api/identityprovider-list)
- [Erstellen eines Identitätsanbieters](/graph/api/identityprovider-post-identityproviders)
- [Abrufen eines Identitätsanbieters](/graph/api/identityprovider-get)
- [Aktualisieren eines Identitätsanbieters](/graph/api/identityprovider-update)
- [Löschen eines Identitätsanbieters](/graph/api/identityprovider-delete)

## <a name="user-flow"></a>Benutzerflow

Konfigurieren Sie vordefinierte Richtlinien für Registrierung, Anmeldung, kombinierte Registrierung und Anmeldung, Kennwortzurücksetzung und Profilaktualisierung.

- [Auflisten von Benutzerflows](/graph/api/identitycontainer-list-b2cuserflows)
- [Erstellen eines Benutzerflows](/graph/api/identitycontainer-post-b2cuserflows)
- [Abrufen eines Benutzerflows](/graph/api/b2cidentityuserflow-get)
- [Löschen eines Benutzerflows](/graph/api/b2cidentityuserflow-delete)

## <a name="user-flow-authentication-methods-beta"></a>Authentifizierungsmethoden mit Benutzerflow (Beta)

Wählen Sie einen Mechanismus, mit dem sich Benutzer über lokale Konten registrieren können. Lokale Konten sind die Konten, bei denen die Identitätsassertion von Azure AD durchgeführt wird. Weitere Informationen finden Sie unter [Ressourcentyp „b2cAuthenticationMethodsPolicy“](/graph/api/resources/b2cauthenticationmethodspolicy).

- [Get](/graph/api/b2cauthenticationmethodspolicy-get)
- [Aktualisieren](/graph/api/b2cauthenticationmethodspolicy-update)

## <a name="custom-policies"></a>Benutzerdefinierte Richtlinien

Mit den folgenden Vorgängen können Sie Ihre Azure AD B2C-Vertrauensframeworkrichtlinien verwalten, die als [benutzerdefinierte Richtlinien](custom-policy-overview.md) bezeichnet werden.

- [Auflisten aller Vertrauensframeworkrichtlinien, die in einem Mandanten konfiguriert sind](/graph/api/trustframework-list-trustframeworkpolicies)
- [Erstellen einer Vertrauensframeworkrichtlinie](/graph/api/trustframework-post-trustframeworkpolicy)
- [Lesen der Eigenschaften einer vorhandenen Vertrauensframeworkrichtlinie](/graph/api/trustframeworkpolicy-get)
- [Aktualisieren oder Erstellen einer Vertrauensframeworkrichtlinie](/graph/api/trustframework-put-trustframeworkpolicy)
- [Löschen einer vorhandenen Vertrauensframeworkrichtlinie](/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>Richtlinienschlüssel

Das Identity Experience Framework speichert die Geheimnisse, auf die in einer benutzerdefinierten Richtlinie verwiesen wird, um Vertrauen zwischen den Komponenten herzustellen. Diese Geheimnisse können symmetrische oder asymmetrische Schlüssel/Werte sein. Im Azure-Portal werden diese Entitäten als **Richtlinienschlüssel** angezeigt.

Die vorrangige Ressource für Richtlinienschlüssel in der Microsoft Graph-API ist das [Vertrauensframework-Keyset](/graph/api/resources/trustframeworkkeyset). Jedes **Keyset** enthält mindestens einen **Schlüssel**. Zum Erstellen eines Schlüssels müssen Sie zunächst ein leeres Keyset erstellen und anschließend darin einen Schlüssel generieren. Sie können ein manuelles Geheimnis erstellen, ein Zertifikat hochladen oder einen PKCS12-Schlüssel erstellen. Der Schlüssel kann ein generiertes Geheimnis, eine Zeichenfolge (z. B. der geheime Facebook-Anwendungsschlüssel) oder ein von Ihnen hochgeladenes Zertifikat sein. Wenn ein Keyset mehrere Schlüssel enthält, ist immer nur einer der Schlüssel aktiv.

### <a name="trust-framework-policy-keyset"></a>Vertrauensframeworkrichtlinie – Keyset

- [Auflisten der Vertrauensframework-Keysets](/graph/api/trustframework-list-keysets)
- [Erstellen eines Vertrauensframework-Keysets](/graph/api/trustframework-post-keysets)
- [Abrufen eines Keysets](/graph/api/trustframeworkkeyset-get)
- [Aktualisieren eines Vertrauensframework-Keysets](/graph/api/trustframeworkkeyset-update)
- [Löschen eines Vertrauensframework-Keysets](/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>Vertrauensframeworkrichtlinie – Schlüssel

- [Abrufen des derzeit aktiven Schlüssels im Keyset](/graph/api/trustframeworkkeyset-getactivekey)
- [Erstellen eines Schlüssels im Keyset](/graph/api/trustframeworkkeyset-generatekey)
- [Hochladen eines zeichenfolgebasierten Geheimnisses](/graph/api/trustframeworkkeyset-uploadsecret)
- [Hochladen eines X.509-Zertifikats](/graph/api/trustframeworkkeyset-uploadcertificate)
- [Hochladen eines Zertifikats im PKCS12-Format](/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>Anwendungen

- [Auflisten von Anwendungen](/graph/api/application-list)
- [Erstellen einer Anwendung](/graph/api/resources/application)
- [Aktualisieren einer Anwendung](/graph/api/application-update)
- [Erstellen eines Dienstprinzipals](/graph/api/resources/serviceprincipal)
- [Erstellen einer oauth2Permission-Genehmigung](/graph/api/resources/oauth2permissiongrant)
- [Löschen der Anwendung](/graph/api/application-delete)

## <a name="application-extension-properties"></a>Anwendungserweiterungseigenschaften

- [Auflisten von Erweiterungseigenschaften](/graph/api/application-list-extensionproperty)
- [Erweiterungseigenschaft löschen](/graph/api/application-delete-extensionproperty)

Azure AD B2C stellt ein Verzeichnis bereit, das 100 benutzerdefinierte Attribute pro Benutzer enthalten kann. Bei Benutzerflows werden diese Erweiterungseigenschaften [mit dem Azure-Portal verwaltet](user-flow-custom-attributes.md). Bei benutzerdefinierten Richtlinien erstellt Azure AD B2C die Eigenschaft für Sie, wenn die Richtlinie zum ersten Mal einen Wert in die Erweiterungseigenschaft schreibt.

## <a name="audit-logs"></a>Überwachungsprotokolle

- [Auflisten von Überwachungsprotokollen](/graph/api/directoryaudit-list)

Weitere Informationen zum Zugriff auf Azure AD B2C-Überwachungsprotokolle finden Sie unter [Zugriff auf Azure AD B2C-Überwachungsprotokolle](view-audit-logs.md).

## <a name="conditional-access"></a>Bedingter Zugriff

- [Auflisten aller Richtlinien für bedingten Zugriff](/graph/api/conditionalaccessroot-list-policies?tabs=http)
- [Lesezugriffeigenschaften und Beziehungen einer Richtlinie für bedingten Zugriff](/graph/api/conditionalaccesspolicy-get)
- [Erstellen einer neuen Richtlinie für bedingten Zugriff](/graph/api/resources/application)
- [Aktualisieren einer Richtlinie für bedingten Zugriff](/graph/api/conditionalaccesspolicy-update)
- [Löschen einer Richtlinie für bedingten Zugriff](/graph/api/conditionalaccesspolicy-delete)

## <a name="how-to-programmatically-manage-microsoft-graph"></a>Gewusst wie: Programmgesteuertes Verwalten von Microsoft Graph

Wenn Sie Microsoft Graph verwalten möchten, können Sie dies entweder als Anwendung mit den Anwendungsberechtigungen oder mit den delegierten Berechtigungen ausführen. Bei den delegierten Berechtigungen stimmt entweder der Benutzer oder ein Administrator den von der App angeforderten Berechtigungen zu. An die App wird mit der Berechtigung delegiert, bei an die Zielressource gerichteten Aufrufen als angemeldeter Benutzer zu fungieren. Anwendungsberechtigungen werden von den Apps verwendet, für die kein angemeldeter Benutzer erforderlich ist und die daher Anwendungsberechtigungen erfordern. Aus diesem Grund können nur Administratoren den Anwendungsberechtigungen zustimmen.

> [!NOTE]
> Delegierte Berechtigungen für Benutzer, die sich über Benutzerflows oder benutzerdefinierte Richtlinien anmelden, können nicht für delegierte Berechtigungen für Microsoft Graph verwendet werden.
## <a name="code-sample-how-to-programmatically-manage-user-accounts"></a>Codebeispiel: Programmgesteuertes Verwalten von Benutzerkonten

Dieses Codebeispiel ist eine .NET Core-Konsolenanwendung, die das [Microsoft Graph SDK](/graph/sdks/sdks-overview) für die Interaktion mit der Microsoft Graph-API verwendet. Der Code veranschaulicht, wie die API aufgerufen wird, um Benutzer in einem Azure AD B2C-Mandanten programmgesteuert zu verwalten.
Sie können das [Beispielarchiv herunterladen](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (*.zip), das Repository auf GitHub [durchsuchen](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) oder das Repository klonen:

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

Nachdem Sie das Codebeispiel abgerufen haben, konfigurieren Sie es für Ihre Umgebung, und erstellen Sie dann das Projekt:

1. Öffnen Sie das Projekt in [Visual Studio](https://visualstudio.microsoft.com) oder [Visual Studio Code](https://code.visualstudio.com).
1. Öffnen Sie `src/appsettings.json`.
1. Ersetzen Sie im Abschnitt `appSettings` die Zeichenfolge `your-b2c-tenant` durch den Namen Ihres Mandanten und `Application (client) ID` und `Client secret` durch die Werte für Ihre Registrierung der Verwaltungsanwendung. Weitere Informationen finden Sie unter [Registrieren einer Microsoft Graph-Anwendung](microsoft-graph-get-started.md).
1. Öffnen Sie in Ihrem lokalen Klon des Repositorys ein Konsolenfenster, wechseln Sie in das Verzeichnis `src`, und erstellen Sie dann das Projekt:

    ```console
    cd src
    dotnet build
    ```
    
1. Führen Sie die Anwendung mit Befehl `dotnet` aus:

    ```console
    dotnet bin/Debug/netcoreapp3.1/b2c-ms-graph.dll
    ```

Die Anwendung zeigt eine Liste der Befehle an, die Sie ausführen können. Beispielsweise können Sie alle Benutzer oder einen einzelnen Benutzer abrufen, einen Benutzer löschen, das Kennwort eines Benutzers aktualisieren und einen Massenimport ausführen.

> [!NOTE]
> Damit die Anwendung Benutzerkonten-Kennwörter aktualisiert, müssen Sie der Anwendung [die Rolle des Benutzeradministrators erteilen](microsoft-graph-get-started.md#optional-grant-user-administrator-role).
 
### <a name="code-discussion"></a>Überlegungen zum Code

Der Beispielcode verwendet das [Microsoft Graph SDK](/graph/sdks/sdks-overview), das entwickelt wurde, um das Entwickeln hochwertiger, effizienter und robuster Anwendungen mit Zugriff auf Microsoft Graph zu vereinfachen.

Alle Anforderungen an die Microsoft Graph-API erfordern ein Zugriffstoken für die Authentifizierung. Die Lösung nutzt das NuGet-Paket [Microsoft.Graph.Auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/), das einen auf einem Authentifizierungsszenario basierenden Wrapper der Microsoft Authentication Library (MSAL) für die Verwendung mit dem Microsoft Graph SDK bereitstellt.

Die `RunAsync`-Methode in der Datei _Program.cs_ führt Folgendes aus:

1. Sie liest Anwendungseinstellungen aus der Datei _appsettings.json_
1. Sie initialisiert den Authentifizierungsanbieter unter Verwendung des [Gewährungsflows für OAuth 2.0-Clientanmeldeinformationen](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). Mit dem Gewährungsflow für Clientanmeldeinformationen kann die App ein Zugriffstoken abrufen, um die Microsoft Graph-API aufzurufen.
1. Sie richtet den Microsoft Graph-Dienstclient mit dem Authentifizierungsanbieter ein:

    ```csharp
    // Read application settings from appsettings.json (tenant ID, app ID, client secret, etc.)
    AppSettings config = AppSettingsFile.ReadFromJsonFile();

    // Initialize the client credential auth provider
    IConfidentialClientApplication confidentialClientApplication = ConfidentialClientApplicationBuilder
        .Create(config.AppId)
        .WithTenantId(config.TenantId)
        .WithClientSecret(config.ClientSecret)
        .Build();
    ClientCredentialProvider authProvider = new ClientCredentialProvider(confidentialClientApplication);

    // Set up the Microsoft Graph service client with client credentials
    GraphServiceClient graphClient = new GraphServiceClient(authProvider);
    ```

Der initialisierte *GraphServiceClient* wird dann in _UserService.cs_ verwendet, um die Vorgänge zur Benutzerverwaltung auszuführen. Beispielsweise können Sie eine Liste der Benutzerkonten im Mandanten abrufen:

```csharp
public static async Task ListUsers(GraphServiceClient graphClient)
{
    Console.WriteLine("Getting list of users...");

    // Get all users (one page)
    var result = await graphClient.Users
        .Request()
        .Select(e => new
        {
            e.DisplayName,
            e.Id,
            e.Identities
        })
        .GetAsync();

    foreach (var user in result.CurrentPage)
    {
        Console.WriteLine(JsonConvert.SerializeObject(user));
    }
}
```

Das [Ausführen von API-Aufrufen mithilfe der Microsoft Graph-SDKs](/graph/sdks/create-requests) umfasst Informationen zum Lesen und Schreiben von Informationen aus Microsoft Graph. Verwenden Sie `$select`, um die zurückgegebenen Eigenschaften zu steuern, benutzerdefinierte Abfrageparameter bereitzustellen und die Abfrageparameter `$filter` und `$orderBy` zu verwenden.

<!-- LINK -->

[graph-objectIdentity]: /graph/api/resources/objectidentity
[graph-user]: /graph/api/resources/user
