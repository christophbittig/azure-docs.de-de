---
title: Aktivieren der Authentifizierung in einer Web-API mit Azure Active Directory B2C
description: In diesem Artikel wird erläutert, wie Sie Azure Active Directory B2C verwenden, um eine Web-API zu schützen.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/26/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 38a60dee9d4a5b7c0516b8e3278e848e0be26c2e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131424437"
---
# <a name="enable-authentication-in-your-own-web-api-by-using-azure-ad-b2c"></a>Aktivieren der Authentifizierung in Ihrer eigenen Web-API mit Azure AD B2C

Um den Zugriff auf eine Web-API zu autorisieren, stellen Sie nur Anforderungen bereit, die ein gültiges von Azure AD B2C (Azure Active Directory B2C) ausgestelltes Zugriffstoken enthalten. In diesem Artikel erfahren Sie, wie Sie Azure AD B2C-Autorisierung für Ihre Web-API aktivieren. Nachdem Sie die Schritte in diesem Artikel ausgeführt haben, sind nur Benutzer, die ein gültiges Zugriffstoken erhalten, berechtigt, Ihre Web-API-Endpunkte aufzurufen.  

## <a name="prerequisites"></a>Voraussetzungen

Lesen Sie zunächst einen der folgenden Artikel, in denen erläutert wird, wie Sie die Authentifizierung für Apps konfigurieren, die Web-APIs aufrufen. Führen Sie dann die Schritte in diesem Artikel aus, um die Beispiel-Web-API durch Ihre eigene Web-API zu ersetzen.   

- [Konfigurieren der Authentifizierung in einer Beispielwebanwendung, die eine Web-API mithilfe von Azure Active Directory B2C aufruft](configure-authentication-sample-web-app-with-api.md)
- [Konfigurieren der Authentifizierung in einem Beispiel einer Single-Page-Webanwendung (SPA) mithilfe von Azure AD B2C](configure-authentication-sample-spa-app.md)

## <a name="overview"></a>Übersicht

Die tokenbasierte Authentifizierung stellt sicher, dass Anforderungen an eine Web-API von einem gültigen Zugriffstoken begleitet werden. 

Die App führt folgende Schritte aus:

1. Sie authentifiziert Benutzer mit Azure AD B2C.
1. Sie erwirbt ein Zugriffstoken mit den erforderlichen Berechtigungen (Bereichen) für den Web-API-Endpunkt.
1. Sie übergibt das Zugriffstoken als Bearertoken im Autorisierungsheader der HTTP-Anforderung in folgendem Format: 

    ```http
    Authorization: Bearer <token>
    ```    

Die Web-API führt folgende Schritte aus:

1. Sie liest das Bearertoken aus dem Autorisierungsheader in der HTTP-Anforderung.

1. Sie überprüft das Token. 
1. Sie überprüft die Berechtigungen (Bereiche) im Token.
1. Sie liest die Ansprüche, die im Token codiert sind (optional).
1. Sie antwortet auf die HTTP-Anforderung. 

### <a name="app-registration-overview"></a>Übersicht über die App-Registrierung

Damit sich Ihre App mit Azure AD B2C anmelden und eine Web-API aufrufen kann, müssen Sie im Azure AD B2C-Verzeichnis zwei Anwendungen registrieren.  

- Durch die *Web-, mobile oder SPA-Anwendungsregistrierung* kann sich Ihre App bei Azure AD B2C anmelden. Beim App-Registrierungsprozess wird eine *Anwendungs-ID* generiert, die auch als *Client-ID* bezeichnet wird und Ihre Anwendung eindeutig identifiziert (Beispiel: *App ID: 1*).

- Durch die *Web-API*-Registrierung kann Ihre App eine geschützte Web-API aufrufen. Die Registrierung macht die Web-API-Berechtigungen (Bereiche) verfügbar. Beim App-Registrierungsprozess wird eine *Anwendungs-ID* generiert, die Ihre Web-API eindeutig identifiziert (Beispiel: *App ID: 2*). Erteilen Sie Ihrer App (App-ID: 1) Berechtigungen für die Web-API-Bereiche (App-ID: 2). 

Die Anwendungsregistrierungen und die Anwendungsarchitektur werden im folgenden Diagramm beschrieben:

![Diagramm der Anwendungsregistrierungen und der Anwendungsarchitektur für eine App mit Web-API.](./media/enable-authentication-web-api/app-with-api-architecture.png) 

## <a name="prepare-your-development-environment"></a>Vorbereiten Ihrer Entwicklungsumgebung  

In den nächsten Abschnitten erstellen Sie ein neues Web-API-Projekt. Wählen Sie Ihre Programmiersprache aus, ASP.NET Core oder Node.js. Sie benötigen unbedingt einen Computer, auf dem eines der folgenden Betriebssysteme ausgeführt wird: 

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [C# für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) (neueste Version)
* [.NET 5.0 SDK](https://dotnet.microsoft.com/download/dotnet)

# <a name="nodejs"></a>[Node.js](#tab/nodejsgeneric)

* [Visual Studio Code](https://code.visualstudio.com/) oder ein anderer Code-Editor
* [Node.js-Runtime](https://nodejs.org/en/download/)

---

## <a name="step-1-create-a-protected-web-api"></a>Schritt 1: Erstellen einer geschützten Web-API

Erstellen Sie ein neues Web-API-Projekt. Wählen Sie zunächst die Programmiersprache aus, die Sie verwenden möchten, **ASP.NET Core** oder **Node.js**.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

Verwenden Sie den Befehl [`dotnet new`](/dotnet/core/tools/dotnet-new). Der `dotnet new`-Befehl erstellt einen neuen Ordner namens *TodoList* mit den Web-API-Projekt-Ressourcen. Öffnen Sie das Verzeichnis und dann [Visual Studio Code](https://code.visualstudio.com/). 

```dotnetcli
dotnet new webapi -o TodoList
cd TodoList
code . 
```

Wenn Sie zum „Hinzufügen der erforderlichen Ressourcen zum Projekt“ aufgefordert werden, wählen Sie **Ja** aus.

# <a name="nodejs"></a>[Node.js](#tab/nodejsgeneric)

Verwenden Sie [Express](https://expressjs.com/) für [Node.js](https://nodejs.org/), um eine Web-API zu erstellen. Gehen Sie folgendermaßen vor, um eine Web-API zu erstellen:

1. Erstellen Sie einen neuen Ordner mit dem Namen *TodoList*. 
1. Erstellen Sie im Ordner *TodoList* eine Datei namens *app.js*.
1. Führen Sie in einer Befehlsshell `npm init -y` aus. Dieser Befehl erstellt eine Standarddatei namens *package.json* für Ihr Node.js-Projekt.
1. Führen Sie `npm install express` in der Befehlsshell aus. Dieser Befehl installiert das Express-Framework.

--- 

## <a name="step-2-install-the-dependencies"></a>Schritt 2: Installieren der Abhängigkeiten

Fügen Sie ihrem Web-API-Projekt die Authentifizierungsbibliothek hinzu. Die Authentifizierungsbibliothek analysiert den HTTP-Authentifizierungsheader, überprüft das Token und extrahiert Ansprüche. Weitere Informationen finden Sie in der Dokumentation zur Bibliothek.


# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

Um die Authentifizierungsbibliothek hinzuzufügen, installieren Sie das Paket, indem Sie den folgenden Befehl ausführen:

```dotnetcli
dotnet add package Microsoft.Identity.Web
```

# <a name="nodejs"></a>[Node.js](#tab/nodejsgeneric)

Um die Authentifizierungsbibliothek hinzuzufügen, installieren Sie das Paket, indem Sie den folgenden Befehl ausführen:

```
npm install passport
npm install passport-azure-ad
npm install morgan
```
 
Das [morgen-Paket](https://www.npmjs.com/package/morgan) ist eine Middleware für die HTTP-Anforderungsprotokollierung für Node.js.

---

## <a name="step-3-initiate-the-authentication-library"></a>Schritt 3: Initiieren der Authentifizierungsbibliothek

Fügen Sie den zum Initiieren der Authentifizierungsbibliothek erforderlichen Code hinzu.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

Öffnen Sie *Startup.cs*, und fügen Sie am Anfang der Klasse die folgenden `using`-Deklarationen hinzu:

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.Identity.Web;
```

Suchen Sie die Funktion `ConfigureServices(IServiceCollection services)`. Fügen Sie dann vor der `services.AddControllers();`-Codezeile den folgenden Codeausschnitt hinzu:


```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Adds Microsoft Identity platform (Azure AD B2C) support to protect this Api
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddMicrosoftIdentityWebApi(options =>
    {
        Configuration.Bind("AzureAdB2C", options);

        options.TokenValidationParameters.NameClaimType = "name";
    },
    options => { Configuration.Bind("AzureAdB2C", options); });
    // End of the Microsoft Identity platform block    

    services.AddControllers();
}
```

Suchen Sie die Funktion `Configure`. Fügen Sie dann unmittelbar nach der `app.UseRouting();`-Codezeile den folgenden Codeausschnitt hinzu:


```csharp
app.UseAuthentication();
```

Nach den Änderungen sollte Ihr Code wie der folgende Codeausschnitt aussehen:

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }

    app.UseHttpsRedirection();

    app.UseRouting();
    
    // Add the following line 
    app.UseAuthentication();
    // End of the block you add
    
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

# <a name="nodejs"></a>[Node.js](#tab/nodejsgeneric)

Fügen Sie Ihrer *app.js*-Datei den folgenden JavaScript-Code hinzu.

```javascript
// Import the required libraries
const express = require('express');
const morgan = require('morgan');
const passport = require('passport');
const config = require('./config.json');

// Import the passport Azure AD library
const BearerStrategy = require('passport-azure-ad').BearerStrategy;

// Set the Azure AD B2C options
const options = {
    identityMetadata: `https://${config.credentials.tenantName}.b2clogin.com/${config.credentials.tenantName}.onmicrosoft.com/${config.policies.policyName}/${config.metadata.version}/${config.metadata.discovery}`,
    clientID: config.credentials.clientID,
    audience: config.credentials.clientID,
    issuer: config.credentials.issuer,
    policyName: config.policies.policyName,
    isB2C: config.settings.isB2C,
    scope: config.resource.scope,
    validateIssuer: config.settings.validateIssuer,
    loggingLevel: config.settings.loggingLevel,
    passReqToCallback: config.settings.passReqToCallback
}

// Instantiate the passport Azure AD library with the Azure AD B2C options
const bearerStrategy = new BearerStrategy(options, (token, done) => {
        // Send user info using the second argument
        done(null, { }, token);
    }
);

// Use the required libraries
const app = express();

app.use(morgan('dev'));

app.use(passport.initialize());

passport.use(bearerStrategy);

//enable CORS (for testing only -remove in production/deployment)
app.use((req, res, next) => {
    res.header('Access-Control-Allow-Origin', '*');
    res.header('Access-Control-Allow-Headers', 'Authorization, Origin, X-Requested-With, Content-Type, Accept');
    next();
});
```
--- 

## <a name="step-4-add-the-endpoints"></a>Schritt 4: Hinzufügen der Endpunkte

Fügen Sie Ihrer Web-API zwei Endpunkte hinzu:

- Anonymer `/public`-Endpunkt. Dieser Endpunkt gibt das aktuelle Datum und die aktuelle Uhrzeit zurück. Verwenden Sie ihn, um Ihre Web-API mit anonymen Aufrufen zu debuggen.
- Geschützter `/hello`-Endpunkt. Dieser Endpunkt gibt den Wert des `name`-Anspruchs innerhalb des Zugriffstokens zurück.

**So fügen Sie den anonymen Endpunkt hinzu:**

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

Fügen Sie im Ordner */Controllers* eine Datei *PublicController.cs* hinzu, und fügen Sie ihr dann folgenden Codeausschnitt hinzu:

```csharp
using System;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;

namespace TodoList.Controllers
{
    [ApiController]
    [Route("[controller]")]
    public class PublicController : ControllerBase
    {
        private readonly ILogger<PublicController> _logger;

        public PublicController(ILogger<PublicController> logger)
        {
            _logger = logger;
        }

        [HttpGet]
        public ActionResult Get()
        {
            return Ok( new {date = DateTime.UtcNow.ToString()});
        }
    }
}
```

# <a name="nodejs"></a>[Node.js](#tab/nodejsgeneric)

Fügen Sie in der *app.js*-Datei den folgenden JavaScript-Code hinzu:


```javascript
// API anonymous endpoint
app.get('/public', (req, res) => res.send( {'date': new Date() } ));
```

--- 

**So fügen Sie den geschützten Endpunkt hinzu:**

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

Fügen Sie im Ordner */Controllers* eine Datei *HelloController.cs* hinzu, und fügen Sie ihr dann folgenden Code hinzu:

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using Microsoft.Identity.Web.Resource;

namespace TodoList.Controllers
{
    [Authorize]
    [RequiredScope("tasks.read")]
    [ApiController]
    [Route("[controller]")]
    public class HelloController : ControllerBase
    {

        private readonly ILogger<HelloController> _logger;
        private readonly IHttpContextAccessor _contextAccessor;

        public HelloController(ILogger<HelloController> logger, IHttpContextAccessor contextAccessor)
        {
            _logger = logger;
            _contextAccessor = contextAccessor;
        }

        [HttpGet]
        public ActionResult Get()
        {
            return Ok( new { name = User.Identity.Name});
        }
    }
}
```

Der `HelloController`-Controller ist mit dem [AuthorizeAttribute](/aspnet/core/security/authorization/simple) versehen, das den Zugriff auf authentifizierte Benutzer beschränkt. 

Der Controller ist auch mit dem `[RequiredScope("tasks.read")]` versehen. Das [RequiredScopeAttribute](/dotnet/api/microsoft.identity.web.resource.requiredscopeattribute.-ctor) überprüft, ob die Web-API mit den richtigen Bereichen aufgerufen wird, `tasks.read`. 

# <a name="nodejs"></a>[Node.js](#tab/nodejsgeneric)

Fügen Sie in der *app.js*-Datei den folgenden JavaScript-Code hinzu: 

```javascript
// API protected endpoint
app.get('/hello',
    passport.authenticate('oauth-bearer', {session: false}),
    (req, res) => {
        console.log('Validated claims: ', req.authInfo);
        
        // Service relies on the name claim.  
        res.status(200).json({'name': req.authInfo['name']});
    }
);
```

Der `/hello`-Endpunkt ruft zunächst die `passport.authenticate()`-Funktion auf. Die Authentifizierungsfunktion schränkt den Zugriff auf authentifizierte Benutzer ein. 

Die Authentifizierungsfunktion überprüft auch, ob die Web-API mit den richtigen Bereichen aufgerufen wird. Die zulässigen Bereiche werden in der [Konfigurationsdatei](#step-6-configure-the-web-api) aufgeführt. 

--- 

## <a name="step-5-configure-the-web-server"></a>Schritt 5: Konfigurieren des Web-Servers

Legen Sie in einer Entwicklungsumgebung fest, dass die Web-API an der Portnummer für eingehende HTTP- oder HTTPS-Anforderungen lauscht. Verwenden Sie in diesem Beispiel HTTP-Port 6000 und HTTPS-Port 6001. Der Basis-URI der Web-API ist `http://localhost:6000` für HTTP und `https://localhost:6001` für HTTPS.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

Fügen Sie der Datei *appsettings.json* den folgenden JSON-Ausschnitt hinzu. 

```json
"Kestrel": {
    "EndPoints": {
      "Http": {
        "Url": "http://localhost:6000"
      },
      "Https": {
         "Url": "https://localhost:6001"   
        }
    }
  }
```

# <a name="nodejs"></a>[Node.js](#tab/nodejsgeneric)

Fügen Sie der *app.js*-Datei den folgenden JavaScript-Code hinzu. Es ist möglich, [HTTP- und HTTPS-Endpunkte für die Node-Anwendung einzurichten](https://github.com/expressjs/express/wiki/Migrating-from-2.x-to-3.x#application-function). 

```javascript
// Starts listening on port 6000
const port = process.env.PORT || 6000;

app.listen(port, () => {
    console.log('Listening on port ' + port);
});
```

---

## <a name="step-6-configure-the-web-api"></a>Schritt 6: Konfigurieren der Web-API

Fügen Sie einer Konfigurationsdatei Konfigurationen hinzu. Die Datei enthält Informationen zu Ihrem Azure AD B2C-Identitätsanbieter. Die Web-API-App überprüft anhand dieser Informationen das Zugriffstoken, das die Web-App als Bearertoken übergibt.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

Öffnen Sie im Projektstammordner die Datei *appsettings.json*, und fügen Sie folgende Einstellungen hinzu:

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com",
    "Domain": "contoso.onmicrosoft.com",
    "ClientId": "<web-api-app-application-id>",
    "SignedOutCallbackPath": "/signout/<your-sign-up-in-policy>",
    "SignUpSignInPolicyId": "<your-sign-up-in-policy>"
  },
  // More settings here
}
```

Aktualisieren Sie in der Datei *appsettings.json* die folgenden Eigenschaften: 

|`Section`  |Key  |Wert  |
|---------|---------|---------|
|AzureAdB2C|Instanz| Der erste Teil Ihres Azure AD B2C-[Mandantennamens](tenant-management.md#get-your-tenant-name) (z. B. `https://contoso.b2clogin.com`).|
|AzureAdB2C|Domain| Der vollständige Name Ihres Azure AD B2C-[Mandanten](tenant-management.md#get-your-tenant-name) (z. B. `contoso.onmicrosoft.com`).|
|AzureAdB2C|ClientId| Die ID der Web-API-Anwendung. Im [obigen Diagramm](#app-registration-overview) ist dies die Anwendung mit der *App-ID: 2*. Informationen zum Abrufen der Registrierungs-ID Ihrer Web-API-Anwendung finden Sie unter [Voraussetzungen](#prerequisites). |
|AzureAdB2C|SignUpSignInPolicyId|Die Benutzerflows oder die benutzerdefinierte Richtlinie. Informationen zum Abrufen Ihres Benutzerflows oder Ihrer Richtlinie finden Sie unter [Voraussetzungen](#prerequisites).  |

# <a name="nodejs"></a>[Node.js](#tab/nodejsgeneric)

Erstellen Sie im Stammordner des Projekts eine *config.json*-Datei, und fügen Sie ihr den folgenden JSON-Ausschnitt hinzu:  

```json
{
    "credentials": {
        "tenantName": "<your-tenant-name>",
        "clientID": "<your-webapi-application-ID>",
        "issuer": "https://<your-tenant-name>.b2clogin.com/<your-tenant-ID>/v2.0/"
    },
    "policies": {
        "policyName": "b2c_1_susi"
    },
    "resource": {
        "scope": ["tasks.read"]
    },
    "metadata": {
        "discovery": ".well-known/openid-configuration",
        "version": "v2.0"
    },
    "settings": {
        "isB2C": true,
        "validateIssuer": true,
        "passReqToCallback": false,
        "loggingLevel": "info"
    }
}
```

Aktualisieren Sie in der *config.json*-Datei folgende Eigenschaften:

|`Section`  |Key  |Wert  |
|---------|---------|---------|
| Anmeldeinformationen | tenantName | Der erste Teil Ihres Azure AD B2C-[Mandantennamens](tenant-management.md#get-your-tenant-name) (z. B. `contoso`).|
| Anmeldeinformationen |clientID | Die ID der Web-API-Anwendung. Im [obigen Diagramm](#app-registration-overview) ist dies die Anwendung mit der *App-ID: 2*. Informationen zum Abrufen der Registrierungs-ID Ihrer Web-API-Anwendung finden Sie unter [Voraussetzungen](#prerequisites). |
| Anmeldeinformationen | Issuer (Aussteller)| Der Anspruchswert des Tokenausstellers `iss`. Azure AD B2C gibt das Token standardmäßig im folgenden Format zurück: `https://<your-tenant-name>.b2clogin.com/<your-tenant-ID>/v2.0/`. Ersetzen Sie `<your-tenant-name>` durch den ersten Teil des [Namens Ihres Azure AD B2C-Mandanten](tenant-management.md#get-your-tenant-name). Ersetzen Sie `<your-tenant-ID>` durch die [ID Ihres Azure AD B2C-Mandanten](tenant-management.md#get-your-tenant-id). |
| Richtlinien | policyName | Die Benutzerflows oder die benutzerdefinierte Richtlinie. Informationen zum Abrufen Ihres Benutzerflows oder Ihrer Richtlinie finden Sie unter [Voraussetzungen](#prerequisites).|
| resource | scope | Die Bereiche ihrer Web-API-Anwendungsregistrierung. Wie Sie Ihren Web-API-Bereich abrufen, erfahren Sie unter [Voraussetzungen](#prerequisites).|

---

## <a name="step-7-run-and-test-the-web-api"></a>Schritt 7: Ausführen und Testen der Web-API

Führen Sie abschließend die Web-API mit Ihren Azure AD B2C-Umgebungseinstellungen aus. 

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

Starten Sie die Web-App in der Befehlsshell mit folgendem Befehl:

```bush
 dotnet run
```

Die folgende Ausgabe sollte angezeigt werden. Dies bedeutet, dass Ihre App ausgeführt wird und zum Empfang von Anforderungen bereit ist.

```
Now listening on: http://localhost:6000
```

Um das Programm zu beenden, wählen Sie in der Befehlsshell STRG+C aus. Sie können die App mit dem Befehl `node app.js` erneut ausführen.

> [!TIP]
> Alternativ können Sie den Befehl `dotnet run` auch mit dem [Visual Studio Code-Debugger](https://code.visualstudio.com/docs/editor/debugging) ausführen. Mit dem integrierten Debugger von Visual Studio Code können Sie Ihre Bearbeitungs-, Kompilierungs- und Debugschleife beschleunigen.

Öffnen Sie einen Browser, und wechseln Sie zu `http://localhost:6000/public`. Im Browserfenster sollte der folgende Text zusammen mit dem aktuellen Datum und der aktuellen Uhrzeit angezeigt werden.

# <a name="nodejs"></a>[Node.js](#tab/nodejsgeneric)

Starten Sie die Web-App in der Befehlsshell mit folgendem Befehl:

```bush
node app.js
```

Die folgende Ausgabe sollte angezeigt werden. Dies bedeutet, dass Ihre App ausgeführt wird und zum Empfang von Anforderungen bereit ist.

```
Example app listening on port 6000!
```

Um das Programm zu beenden, wählen Sie in der Befehlsshell STRG+C aus. Sie können die App mit dem Befehl `node app.js` erneut ausführen.

> [!TIP]
> Alternativ können Sie den Befehl `node app.js` auch mit dem [Visual Studio Code-Debugger](https://code.visualstudio.com/docs/editor/debugging) ausführen. Mit dem integrierten Debugger von Visual Studio Code können Sie Ihre Bearbeitungs-, Kompilierungs- und Debugschleife beschleunigen.

Öffnen Sie einen Browser, und wechseln Sie zu `http://localhost:6000/public`. Im Browserfenster sollte der folgende Text zusammen mit dem aktuellen Datum und der aktuellen Uhrzeit angezeigt werden.

---

## <a name="step-8-call-the-web-api-from-your-app"></a>Schritt 8: Aufrufen der Web-API aus Ihrer App

Versuchen Sie, den geschützten Web-API-Endpunkt ohne Zugriffstoken aufzurufen. Öffnen Sie einen Browser, und wechseln Sie zu `http://localhost:6000/hello`. Die API gibt eine nicht autorisierte HTTP-Fehlermeldung zurück, die bestätigt, dass die Web-API mit einem Bearertoken geschützt ist.

Fahren Sie mit dem Konfigurieren Ihrer App zum Aufrufen der Web-API fort. Weitere Informationen finden Sie im Abschnitt [Voraussetzungen](#prerequisites).

Sehen Sie sich dieses Video an, um einige bewährte Verfahren für die Integration von Azure AD B2C mit einer API kennenzulernen.

>[!Video https://www.youtube.com/embed/wuUu71RcsIo]

## <a name="next-steps"></a>Nächste Schritte

Rufen Sie das vollständige Beispiel auf GitHub ab:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)
* Rufen Sie die Web-API mit der [Microsoft-Identitätsbibliothek](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C/TodoListService) ab.

# <a name="nodejs"></a>[Node.js](#tab/nodejsgeneric)
* Rufen Sie die Web-API mit der [Passport.js-Bibliothek](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) ab.
