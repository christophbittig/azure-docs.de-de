---
title: Aufrufen der Anforderungsdienst-REST-API (Vorschau)
titleSuffix: Azure Active Directory Verifiable Credentials
description: Informationen zu Ausstellung und Überprüfung mithilfe der Anforderungsdienst-REST-API
documentationCenter: ''
author: barclayn
manager: karenh444
ms.service: active-directory
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 10/08/2021
ms.author: barclayn
ms.openlocfilehash: 805ba2fc9a24536a940e4baf02e2934d26c10d75
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131474020"
---
# <a name="request-service-rest-api-preview"></a>Anforderungsdienst-REST-API (Vorschau)

Azure Active Directory-Nachweise (Azure AD) enthalten die Anforderungsdienst-REST-API. Mit dieser API können Sie Anmeldeinformationen ausstellen und überprüfen. In diesem Artikel wird gezeigt, wie Sie mit der Arbeit mit der Anforderungsdienst-REST-API beginnen.

> [!IMPORTANT]
> Die Anforderungsdienst-REST-API ist derzeit als Vorschauversion verfügbar. Diese Vorschauversion wird ohne eine Vereinbarung zum Servicelevel bereitgestellt. Sie können gelegentliche Breaking Changes und die Einstellung der Unterstützung für die API erwarten, solange Sie sich noch in der Vorschauversion befindet. Die Vorschauversion der API wird nicht für Produktionsworkloads empfohlen. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="api-access-token"></a>API-Zugriffstoken

Damit Ihre Anwendung auf die Anforderungsdienst-REST-API zugreifen kann, müssen Sie neben den erforderlichen Berechtigungen ein gültiges Zugriffstoken hinzufügen. Die von Microsoft Identity Platform ausgestellten Zugriffstoken enthalten Informationen (Bereiche), welche die Anforderungsdienst-REST-API verwendet, um den Aufrufer zu überprüfen. Durch ein Zugriffstoken wird sichergestellt, dass der Anrufer die ordnungsgemäßen Berechtigungen hat, um den Vorgang auszuführen, der angefordert wird.

Um ein Zugriffstoken zu erhalten, muss Ihre App bei der Plattform Microsoft Identity Platform registriert sein und von einem*einer Administrator*in für den Zugriff auf die Anforderungsdienst-API autorisiert werden. Wenn Sie die *verifiable-credentials-app* (Nachweise-App) noch nicht registriert haben, führen Sie die unter [Registrieren der App](verifiable-credentials-configure-tenant.md#register-an-application-in-azure-ad) beschriebenen Schritte aus und [generieren Sie dann ein Anwendungsgeheimnis](verifiable-credentials-configure-issuer.md#configure-the-verifiable-credentials-app).

### <a name="get-an-access-token"></a>Abrufen eines Zugriffstokens

Verwenden Sie den [Flow zur Gewährung von OAuth 2.0-Clientanmeldeinformationen](../../active-directory/develop/v2-oauth2-client-creds-grant-flow.md), um mithilfe von Microsoft Identity Platform das Zugriffstoken anzufordern. Verwenden Sie dazu eine vertrauenswürdige Bibliothek. In diesem Tutorial wird die [Microsoft-Authentifizierungsbibliothek (Microsoft Authentication Library, MSAL)](../../active-directory/develop/msal-overview.md) verwendet. MSAL vereinfacht das Hinzufügen von Authentifizierung und Autorisierung zu einer Anwendung, die eine sichere Web-API aufrufen kann.

# <a name="http"></a>[HTTP](#tab/http)

```http
Refer to to the Microsoft Authentication Library (MSAL) documentation for more information on how to acquire tokens via HTTP.
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
// Initialize MSAL library by using the following code
ConfidentialClientApplicationBuilder.Create(AppSettings.ClientId)
    .WithClientSecret(AppSettings.ClientSecret)
    .WithAuthority(new Uri(AppSettings.Authority))
    .Build();

// Acquire an access token
result = await app.AcquireTokenForClient(AppSettings.Scopes)
                .ExecuteAsync();
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```nodejs
// Initialize MSAL library by using the following code
const msalConfig = {
  auth: {
      clientId: config.azClientId,
      authority: `https://login.microsoftonline.com/${config.azTenantId}`,
      clientSecret: config.azClientSecret,
  },
  system: {
      loggerOptions: {
          loggerCallback(loglevel, message, containsPii) {
              console.log(message);
          },
          piiLoggingEnabled: false,
          logLevel: msal.LogLevel.Verbose,
      }
  }
};
const cca = new msal.ConfidentialClientApplication(msalConfig);
const msalClientCredentialRequest = {
  scopes: ["bbb94529-53a3-4be5-a069-7eaf2712b826/.default"],
  skipCache: false, 
};
module.exports.msalCca = cca;
module.exports.msalClientCredentialRequest = msalClientCredentialRequest;

// Acquire an access token
const result = await mainApp.msalCca.acquireTokenByClientCredential(mainApp.msalClientCredentialRequest);
    if ( result ) {
      accessToken = result.accessToken;
    }
```

---

Geben Sie im vorangehenden Code die folgenden Parameter an:

| Parameter | Bedingung | BESCHREIBUNG |
| --- | --- | --- |
| Authority | Erforderlich | Der Verzeichnismandant, der von der Anwendung für den Betrieb verwendet werden soll. Beispiel: `https://login.microsoftonline.com/{your-tenant}`. (Ersetzen Sie `your-tenant` mit Ihrer [Mandanten-ID oder Ihrem Namen](../fundamentals/active-directory-how-to-find-tenant.md).) |
| Client-ID | Erforderlich | Die Anwendungs-ID, die Ihrer App zugewiesen ist. Diese Informationen finden Sie im Azure-Portal, in dem Sie Ihre App registriert haben. |
| Geheimer Clientschlüssel | Erforderlich | Der geheime Clientschlüssel, den Sie für Ihre App generiert haben.|
| Bereiche | Erforderlich | Muss auf `bbb94529-53a3-4be5-a069-7eaf2712b826/.default` festgelegt sein. |

Weitere Informationen zum Abrufen eines Zugriffstokens mithilfe der Identität einer Konsolen-App finden Sie in einem der folgenden Artikel: [C#](../develop/quickstart-v2-netcore-daemon.md), [Python](../develop/quickstart-v2-python-daemon.md), [Node.js](../develop/quickstart-v2-nodejs-console.md), oder [Java](../develop/quickstart-v2-java-daemon.md).

Sie können auch [auf eine Tokenanforderung mit einem Zertifikat](../develop/v2-oauth2-client-creds-grant-flow.md) anstelle eines geheimen Clientschlüssels zugreifen.

# <a name="http"></a>[HTTP](#tab/http)

```http
POST /{tenant}/oauth2/v2.0/token HTTP/1.1   //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=12345678-0000-0000-00000000000000000
&scope=bbb94529-53a3-4be5-a069-7eaf2712b826/.default
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
// Initialize MSAL library by using the following code
X509Certificate2 certificate = AppSettings.ReadCertificate(AppSettings.CertificateName);
    app = ConfidentialClientApplicationBuilder.Create(AppSettings.ClientId)
        .WithCertificate(certificate)
        .WithAuthority(new Uri(AppSettings.Authority))
        .Build();

// Acquire an access token
result = await app.AcquireTokenForClient(AppSettings.Scopes)
                .ExecuteAsync();
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```nodejs
// Initialize MSAL library by using the following code
const msalConfig = {
  auth: {
      clientId: config.azClientId,
      authority: `https://login.microsoftonline.com/${config.azTenantId}`,
      clientCertificate: {
            thumbprint: "CERT_THUMBPRINT", // a 40-digit hexadecimal string
            privateKey: "CERT_PRIVATE_KEY"
        }
  },
  system: {
      loggerOptions: {
          loggerCallback(loglevel, message, containsPii) {
              console.log(message);
          },
          piiLoggingEnabled: false,
          logLevel: msal.LogLevel.Verbose,
      }
  }
};
const cca = new msal.ConfidentialClientApplication(msalConfig);
const msalClientCredentialRequest = {
  scopes: ["bbb94529-53a3-4be5-a069-7eaf2712b826/.default"],
  skipCache: false, 
};
module.exports.msalCca = cca;
module.exports.msalClientCredentialRequest = msalClientCredentialRequest;

// Acquire an access token
const result = await mainApp.msalCca.acquireTokenByClientCredential(mainApp.msalClientCredentialRequest);
    if ( result ) {
      accessToken = result.accessToken;
    }
```

---

## <a name="call-the-api"></a>Aufrufen der API

Führen Sie die folgenden Schritte aus, um einen Nachweis auszustellen oder zu überprüfen:

1. Erstellen Sie eine HTTP POST-Anforderung für die Anforderungsdienst-REST-API. Ersetzen Sie `{tenantID}` durch Ihre Mandanten-ID oder durch den Namen Ihres Mandanten.

    ```http
    POST https://beta.did.msidentity.com/v1.0/{tenantID}/verifiablecredentials/request
    ```

1. Fügen Sie das Zugriffstoken als Bearertoken an den Autorisierungsheader einer HTTP-Anforderung an.

    ```http
    Authorization: Bearer <token>
    ```

1. Legen Sie den Header `Content-Type` auf `Application/json` fest.

1. Bereiten Sie die [Ausstellungs-](issuance-request-api.md#issuance-request-payload) oder [Präsentations](presentation-request-api.md#presentation-request-payload)-Anforderungsnutzdaten vor, und fügen Sie sie an den Anforderungstext an.

1. Übermitteln Sie die Anforderung an die Anforderungsdienst-REST-API.

## <a name="issuance-request-example"></a>Beispiel für eine Ausstellungsanforderung

Im folgenden Beispiel wird eine Anforderung zur Nachweisausstellung veranschaulicht. Informationen zu den Nutzdaten finden Sie unter [Anforderungsdienst-REST-API – Spezifikation für die Ausstellung](issuance-request-api.md).

```http
POST https://beta.did.msidentity.com/v1.0/contoso.onmicrosoft.com/verifiablecredentials/request
Content-Type: application/json
Authorization: Bearer  <token>

{
    "includeQRCode": true,
    "callback": {
        "url": "https://www.contoso.com/api/issuer/issuanceCallback",
        "state": "de19cb6b-36c1-45fe-9409-909a51292a9c",
        "headers": {
            "api-key": "OPTIONAL API-KEY for VERIFIER CALLBACK API"
        }
    },
    "authority": "did:ion:EiCLL8lzCqlGLYTGbjwgR6SN6OkIjO6uUKyF5zM7fQZ8Jg:eyJkZWx0YSI6eyJwYXRjaGVzIjpbeyJhY3Rpb24iOiJyZXBsYWNlIiwiZG9jdW1lbnQiOnsicHVibGljS2V5cyI6W3siaWQiOiJzaWdfOTAyZmM2NmUiLCJwdWJsaWNLZXlKd2siOnsiY3J2Ijoic2VjcDI1NmsxIiwia3R5IjoiRUMiLCJ4IjoiTEdUOWk3aFYzN1dUcFhHcUg5c1VDek...",
    "registration": {
        "clientName": "Verifiable Credential Expert Sample"
    },
    "issuance": {
        "type": "VerifiedCredentialExpert",
        "manifest": "https://beta.did.msidentity.com/v1.0/12345678-0000-0000-0000-000000000000/verifiableCredential/contracts/VerifiedCredentialExpert1",
        "pin": {
            "value": "3539",
            "length": 4
        },
        "claims": {
            "given_name": "Megan",
            "family_name": "Bowen"
        }
    }
}
```  

Den vollständigen Code finden Sie in einem der folgenden Beispiele:

- [C#](https://github.com/Azure-Samples/active-directory-verifiable-credentials-dotnet/blob/main/AspNetCoreVerifiableCredentials/IssuerController.cs)
- [Node.js](https://github.com/Azure-Samples/active-directory-verifiable-credentials-node/blob/main/1-node-api-idtokenhint/issuer.js)

## <a name="presentation-request-example"></a>Beispiel für Präsentationsanforderung

Im folgenden Beispiel wird eine Anforderung zur Nachweispräsentation veranschaulicht. Informationen zu den Nutzdaten finden Sie unter [Anforderungsdienst-REST-API – Spezifikation für die Präsentation](presentation-request-api.md).

```http
POST https://beta.did.msidentity.com/v1.0/contoso.onmicrosoft.com/verifiablecredentials/request
Content-Type: application/json
Authorization: Bearer  <token>

{
  "includeQRCode": true,
  "callback": {
    "url": "https://www.contoso.com/api/verifier/presentationCallback",
    "state": "92d076dd-450a-4247-aa5b-d2e75a1a5d58",
    "headers": {
      "api-key": "OPTIONAL API-KEY for VERIFIER CALLBACK API"
    }
  },
  "authority": "did:ion:EiCLL8lzCqlGLYTGbjwgR6SN6OkIjO6uUKyF5zM7fQZ8Jg:eyJkZWx0YSI6eyJwYXRjaGVzIjpbeyJhY3Rpb24iOiJyZXBsYWNlIiwiZG9jdW1lbnQiOnsicHVibGljS2V5cyI6W3siaWQiOiJzaWdfOTAyZmM2NmUiLCJwdWJsaWNLZXlKd2siOnsiY3J2Ijoic2VjcDI1NmsxIiwia3R5IjoiRUMiLCJ4IjoiTEdUOWk3aFYzN1dUcFhHcUg5c1VDekxTVlFWcVl3S2JNY1Fsc0RILUZJUSIsInkiOiJiRWo5MDY...",
  "registration": {
    "clientName": "Veritable Credential Expert Verifier"
  },
  "presentation": {
    "includeReceipt": true,
    "requestedCredentials": [
      {
        "type": "VerifiedCredentialExpert",
        "purpose": "So we can see that you a veritable credentials expert",
        "acceptedIssuers": [
          "did:ion:EiCLL8lzCqlGLYTGbjwgR6SN6OkIjO6uUKyF5zM7fQZ8Jg:eyJkZWx0YSI6eyJwYXRjaGVzIjpbeyJhY3Rpb24iOiJyZXBsYWNlIiwiZG9jdW1lbnQiOnsicHVibGljS2V5cyI6W3siaWQiOiJzaWdfOTAyZmM2NmUiLCJwdWJsaWNLZXlKd2siOnsiY3J2Ijoic2VjcDI1NmsxIiwia3R5IjoiRUMiLCJ4IjoiTEdUOWk3aFYzN1dUcFhHcUg5c1VDekxTVlFWcVl3S2JNY1Fsc0RILUZJUSIsInkiO..."
        ]
      }
    ]
  }
}
```

Den vollständigen Code finden Sie in einem der folgenden Beispiele:

- [C#](https://github.com/Azure-Samples/active-directory-verifiable-credentials-dotnet/blob/main/1-asp-net-core-api-idtokenhint/VerifierController.cs) 
- [Node.js](https://github.com/Azure-Samples/active-directory-verifiable-credentials-node/blob/main/1-node-api-idtokenhint/verifier.js)

## <a name="callback-events"></a>Rückrufereignisse

Die Anforderungsnutzdaten enthalten den [Ausstellungs-](issuance-request-api.md#callback-events) und [Präsentations](presentation-request-api.md#callback-events)-Rückrufendpunkt. Der Endpunkt ist Bestandteil Ihrer Webanwendung und sollte öffentlich verfügbar sein. Azure AD-Nachweise rufen Ihren Endpunkt auf, um Ihre App über bestimmte Ereignisse zu informieren. Solche Ereignisse können beispielsweise das Scannen des QR-Codes durch eine*n Benutzer*in, das Verwenden des Deep-Links zur Authentifikator-App oder das Beenden des Präsentationsprozesses sein.

Im folgenden Diagramm werden der Aufruf ihrer App an die Anforderungsdienst-REST-API und die Rückrufe an Ihre Anwendung beschrieben.

![Diagramm, das den Aufruf der API und die Rückrufereignisse beschreibt](media/get-started-request-api/callback-events.png)

Konfigurieren Sie Ihren Endpunkt für das Lauschen auf eingehende HTTP POST-Anforderungen. Im folgenden Codeausschnitt wird veranschaulicht, wie die HTTP-Anforderung für den Ausstellungsrückruf behandelt und die Benutzeroberfläche entsprechend aktualisiert werden kann:

# <a name="http"></a>[HTTP](#tab/http)

Nicht zutreffend. Wählen Sie eine der anderen Programmiersprachen aus.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[HttpPost]
public async Task<ActionResult> IssuanceCallback()
{
try
{
    string content = new System.IO.StreamReader(this.Request.Body).ReadToEndAsync().Result;
    _log.LogTrace("callback!: " + content);
    JObject issuanceResponse = JObject.Parse(content);
    
    // More code here
    if (issuanceResponse["code"].ToString() == "request_retrieved")
    {
        var cacheData = new
        {
            status = "request_retrieved",
            message = "QR Code is scanned. Waiting for issuance...",
        };
        _cache.Set(state, JsonConvert.SerializeObject(cacheData));

        // More code here
    }
}
```

Den vollständigen Code finden Sie im [Ausstellungs- ](https://github.com/Azure-Samples/active-directory-verifiable-credentials-dotnet/blob/main/AspNetCoreVerifiableCredentials/IssuerController.cs) und [Präsentations](https://github.com/Azure-Samples/active-directory-verifiable-credentials-dotnet/blob/main/AspNetCoreVerifiableCredentials/VerifierController.cs)-Code im GitHub-Repository.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```nodejs
mainApp.app.post('/api/issuer/issuance-request-callback', parser, async (req, res) => {
  var body = '';
  req.on('data', function (data) {
    body += data;
  });
  req.on('end', function () {
    requestTrace( req );
    console.log( body );
    var issuanceResponse = JSON.parse(body.toString());
    var message = null;
    
    if ( issuanceResponse.code == "request_retrieved" ) {
      message = "QR Code is scanned. Waiting for issuance to complete...";
    }
    if ( issuanceResponse.code == "issuance_successful" ) {
      message = "Credential successfully issued";
    }
    if ( issuanceResponse.code == "issuance_error" ) {
      message = issuanceResponse.error.message;
    }
    
    // More code here
    res.send()
  });  
  res.send()
})
```

Den vollständigen Code finden Sie im [Ausstellungs-](https://github.com/Azure-Samples/active-directory-verifiable-credentials-node/blob/main/1-node-api-idtokenhint/issuer.js) und [Präsentations](https://github.com/Azure-Samples/active-directory-verifiable-credentials-node/blob/main/1-node-api-idtokenhint/verifier.js)-Code im GitHub-Repository.

---

## <a name="next-steps"></a>Nächste Schritte

Hier erfahren Sie mehr über diese Spezifikationen:

- [Spezifikation der Ausstellungs-API](issuance-request-api.md)
- [Spezifikation der Darstellungs-API](presentation-request-api.md)
