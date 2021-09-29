---
title: Clientassertionen (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Hier erfahren Sie mehr über signierte Client-Assertionen für vertrauliche Clientanwendungen in der Microsoft Authentication Library für .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/18/2021
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 92c1dbe34078442fa5ba84c00880e15502602bc6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124787147"
---
# <a name="confidential-client-assertions"></a>Assertionen für vertrauliche Clients

Um ihre Identität nachzuweisen, tauschen vertrauliche Clientanwendungen einen geheimen Schlüssel mit Azure AD aus. Folgende geheime Schlüssel sind möglich:
- Ein geheimer Clientschlüssel (Anwendungskennwort)
- Ein Zertifikat, mit dem eine signierte Assertion erstellt wird, die Standardansprüche enthält

Der geheime Schlüssel kann auch einfach eine signierte Assertion sein.

MSAL.NET verfügt über vier Methoden, um für die vertrauliche Client-App Anmeldeinformationen oder Assertionen bereitzustellen:
- `.WithClientSecret()`
- `.WithCertificate()`
- `.WithClientAssertion()`
- `.WithClientClaims()`

> [!NOTE]
> Obwohl es möglich ist, die `WithClientAssertion()`-API zum Abrufen von Token für den vertraulichen Client zu verwenden, empfiehlt es sich nicht, diese Methode standardmäßig zu verwenden, da sie komplexer ist und für sehr spezifische Szenarios konzipiert ist, die nicht häufig vorkommen. Die Verwendung der `.WithCertificate()`-API ermöglicht es MSAL.NET, dies für Sie zu verarbeiten. Diese API bietet Ihnen die Möglichkeit, Ihre Authentifizierungsanforderung bei Bedarf anzupassen, doch die von `.WithCertificate()` erstellte Standardassertion reicht für die meisten Authentifizierungsszenarien aus. Diese API kann auch als Problemumgehung in einigen Szenarien verwendet werden, in denen MSAL.NET den Signaturvorgang nicht intern ausführen kann.

### <a name="signed-assertions"></a>Signierte Assertionen

Eine signierte Client Assertion hat die Form eines signierten, Base64-codierten JWT mit der Nutzlast, die die erforderlichen Authentifizierungsansprüche enthält, die von Azure AD vorgeschrieben sind. Gehen Sie zur Verwendung wie folgt vor:

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

Sie können auch das Delegatformular verwenden, das Ihnen die Just-in-Time-Berechnung der Assertionen ermöglicht:

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(() => { return GetSignedClientAssertion(); } )
                                          .Build();
                                          
// or in async manner

app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(async cancellationToken => { return await GetClientAssertionAsync(cancellationToken); })
                                          .Build();
```

Die [von Azure AD erwarteten Ansprüche](active-directory-certificate-credentials.md) in der signierten Assertion lauten:

Anspruchstyp | Wert | BESCHREIBUNG
---------- | ---------- | ----------
aud | `https://login.microsoftonline.com/{tenantId}/v2.0` | Der Anspruch „aud“ (audience, Zielgruppe) identifiziert die Empfänger, für die das JWT vorgesehen ist (hier Azure AD). Weitere Informationen finden Sie unter [RFC 7519, Abschnitt 4.1.3](https://tools.ietf.org/html/rfc7519#section-4.1.3).  In diesem Fall ist dieser Empfänger der Anmeldeserver (login.microsoftonline.com).
exp | 1601519414 | Der Anspruch „exp“ (Ablaufzeit) gibt die Ablaufzeit an, ab oder nach der das JWT NICHT für die Bearbeitung akzeptiert werden darf. Weitere Informationen finden Sie unter [RFC 7519, Abschnitt 4.1.4](https://tools.ietf.org/html/rfc7519#section-4.1.4).  So kann die Assertion bis zu diesem Zeitpunkt verwendet werden. Daher sollte der Zeitraum kurz ausfallen, d. h. höchstens 5–10 Minuten nach `nbf` liegen.  In Azure AD sind derzeit keine Einschränkungen für den `exp`-Zeitpunkt vorgesehen. 
iss | {ClientID} | Der Anspruch „iss“ (issuer, Aussteller) identifiziert den Prinzipal, der das JWT ausgestellt hat, in diesem Fall Ihre Clientanwendung.  Verwenden Sie die GUID der Anwendungs-ID.
jti | (eine GUID) | Der Anspruch "jti" (JWT-ID) stellt einen eindeutigen Bezeichner für das JWT bereit. Es MUSS ein Bezeichnerwert zugewiesen werden, bei dem die Wahrscheinlichkeit vernachlässig ist, dass derselbe Wert versehentlich einem anderen Datenobjekt zugewiesen wird. Wenn die Anwendung mehrere Aussteller verwendet, MÜSSEN auch Konflikte zwischen von mehreren Ausstellern erstellten Werten verhindert werden. Der Wert von „JTI“ ist eine Zeichenfolge mit Beachtung der Groß-/Kleinschreibung. [RFC 7519, Abschnitt 4.1.7](https://tools.ietf.org/html/rfc7519#section-4.1.7)
nbf | 1601519114 | Der Anspruch „nbf“ (nicht vor) gibt die Zeit an, vor der das JWT NICHT für die Bearbeitung akzeptiert werden darf. Weitere Informationen finden Sie unter [RFC 7519, Abschnitt 4.1.5](https://tools.ietf.org/html/rfc7519#section-4.1.5).  Die Verwendung der aktuellen Uhrzeit ist angemessen. 
sub | {ClientID} | Der Anspruch „sub“ (subject, Antragsteller) identifiziert den Antragsteller des JWT, in diesem Fall ist das ebenfalls Ihre Anwendung. Verwenden Sie denselben Wert wie für `iss`. 

Im folgenden Beispiel wird das Erstellen dieser Ansprüche veranschaulicht:

```csharp
private static IDictionary<string, string> GetClaims()
{
      //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
      string aud = $"https://login.microsoftonline.com/{tenantId}/v2.0";

      string ConfidentialClientID = "00000000-0000-0000-0000-000000000000" //client id
      const uint JwtToAadLifetimeInSeconds = 60 * 10; // Ten minutes
      DateTime validFrom = DateTime.UtcNow;
      var nbf = ConvertToTimeT(validFrom);
      var exp = ConvertToTimeT(validFrom + TimeSpan.FromSeconds(JwtToAadLifetimeInSeconds));

      return new Dictionary<string, string>()
           {
                { "aud", aud },
                { "exp", exp.ToString() },
                { "iss", ConfidentialClientID },
                { "jti", Guid.NewGuid().ToString() },
                { "nbf", nbf.ToString() },
                { "sub", ConfidentialClientID }
            };
}
```

So erstellen Sie eine signierte Client-Assertion:

```csharp
string Encode(byte[] arg)
{
    char Base64PadCharacter = '=';
    char Base64Character62 = '+';
    char Base64Character63 = '/';
    char Base64UrlCharacter62 = '-';
    char Base64UrlCharacter63 = '_';

    string s = Convert.ToBase64String(arg);
    s = s.Split(Base64PadCharacter)[0]; // RemoveAccount any trailing padding
    s = s.Replace(Base64Character62, Base64UrlCharacter62); // 62nd char of encoding
    s = s.Replace(Base64Character63, Base64UrlCharacter63); // 63rd char of encoding

    return s;
}

string GetSignedClientAssertion()
{
    //Signing with SHA-256
    string rsaSha256Signature = "http://www.w3.org/2001/04/xmldsig-more#rsa-sha256";
     X509Certificate2 certificate = new X509Certificate2("Certificate.pfx", "Password", X509KeyStorageFlags.EphemeralKeySet);

    //Create RSACryptoServiceProvider
    var x509Key = new X509AsymmetricSecurityKey(certificate);
    var privateKeyXmlParams = certificate.PrivateKey.ToXmlString(true);
    var rsa = new RSACryptoServiceProvider();
    rsa.FromXmlString(privateKeyXmlParams);

    //alg represents the desired signing algorithm, which is SHA-256 in this case
    //kid represents the certificate thumbprint
    var header = new Dictionary<string, string>()
         {
              { "alg", "RS256"},
              { "kid", Encode(certificate.GetCertHash()) }
         };

    //Please see the previous code snippet on how to craft claims for the GetClaims() method
    string token = Encode(Encoding.UTF8.GetBytes(JObject.FromObject(header).ToString())) + "." + Encode(Encoding.UTF8.GetBytes(JObject.FromObject(GetClaims()).ToString()));

    string signature = Encode(rsa.SignData(Encoding.UTF8.GetBytes(token), new SHA256Cng()));
    string signedClientAssertion = string.Concat(token, ".", signature);
    return signedClientAssertion;
}
```

### <a name="alternative-method"></a>Alternative Methode

Sie haben auch die Möglichkeit, [Microsoft.IdentityModel.JsonWebTokens](https://www.nuget.org/packages/Microsoft.IdentityModel.JsonWebTokens/) zu verwenden, um die Assertion für Sie erstellen zu lassen. Der Code wird somit etwas eleganter, wie im folgenden Beispiel zu sehen ist:

```csharp
        string GetSignedClientAssertion()
        {
            var cert = new X509Certificate2("Certificate.pfx", "Password", X509KeyStorageFlags.EphemeralKeySet);

            //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
            string aud = $"https://login.microsoftonline.com/{tenantID}/v2.0";

            // client_id
            string confidentialClientID = "00000000-0000-0000-0000-000000000000";

            // no need to add exp, nbf as JsonWebTokenHandler will add them by default.
            var claims = new Dictionary<string, object>()
            {
                { "aud", aud },
                { "iss", confidentialClientID },
                { "jti", Guid.NewGuid().ToString() },
                { "sub", confidentialClientID }
            };

            var securityTokenDescriptor = new SecurityTokenDescriptor
            {
                Claims = claims,
                SigningCredentials = new X509SigningCredentials(cert)
            };

            var handler = new JsonWebTokenHandler();
            var signedClientAssertion = handler.CreateToken(securityTokenDescriptor);
        }
```

Sobald Sie Ihre signierte Clientassertion besitzen, können Sie sie wie unten gezeigt mit den MSAL-APIs verwenden.

```csharp
            string signedClientAssertion = GetSignedClientAssertion();

            var confidentialApp = ConfidentialClientApplicationBuilder
                .Create(ConfidentialClientID)
                .WithClientAssertion(signedClientAssertion)
                .Build();
```

### <a name="withclientclaims"></a>WithClientClaims

`WithClientClaims(X509Certificate2 certificate, IDictionary<string, string> claimsToSign, bool mergeWithDefaultClaims = true)` erzeugt standardmäßig eine signierte Assertion, die die von Azure AD erwarteten Ansprüche sowie zusätzliche Clientansprüche enthält, die Sie senden möchten. Im folgenden Codeausschnitt wird die Vorgehensweise gezeigt.

```csharp
string ipAddress = "192.168.1.2";
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, 
                                                                      new Dictionary<string, string> { { "client_ip", ipAddress } })
                                          .Build();

```

Wenn einer der Ansprüche im Wörterbuch, das Sie übergeben, mit einem der obligatorischen Ansprüche identisch ist, wird der Wert des zusätzlichen Anspruchs berücksichtigt. Er überschreibt die von MSAL.NET berechneten Ansprüche.

Wenn Sie eigene Ansprüche, einschließlich der obligatorischen Ansprüche, die von Azure AD erwartet werden, angeben möchten, übergeben Sie als `mergeWithDefaultClaims`-Parameter `false`.
