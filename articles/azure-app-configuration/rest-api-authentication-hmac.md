---
title: Azure App Configuration-REST-API – HMAC-Authentifizierung
description: Verwenden von HMAC zum Authentifizieren bei Azure App Configuration über die REST-API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: a301e57563d7a496ea4a0c0a6fbf17da165b9357
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132370553"
---
# <a name="hmac-authentication---rest-api-reference"></a>HMAC-Authentifizierung – REST-API-Referenz

Sie können HTTP-Anforderungen mithilfe des HMAC-SHA256-Authentifizierungsschemas authentifizieren. (HMAC bezieht sich auf Hash-based Message Authentication Code.) Diese Anforderungen müssen über TLS übertragen werden.

## <a name="prerequisites"></a>Voraussetzungen

- **Credential:** \<Access Key ID\>
- **Secret:** Base64-decodierter Zugriffsschlüsselwert. ``base64_decode(<Access Key Value>)``

Die Werte für „credential“ (auch als `id` bezeichnet) und „secret“ (auch `value` genannt) müssen von der Azure App Configuration-Instanz abgerufen werden. Dies kann mithilfe des [Azure-Portals](https://portal.azure.com) oder der [Azure CLI](/cli/azure/) erfolgen.

Geben Sie jede Anforderung mit allen für die Authentifizierung erforderlichen HTTP-Headern an. Folgende Header sind mindestens erforderlich:

|  Anforderungsheader | BESCHREIBUNG  |
| --------------- | ------------ |
| **Host** | Internethost und Portnummer. Weitere Informationen finden Sie im Abschnitt [3.2.2](https://www.w3.org/Protocols/rfc2616/rfc2616-sec3.html#sec3.2.2). |
| **Datum** | Datum und Uhrzeit der Anforderung. Kann nicht mehr als 15 Minuten von der aktuellen koordinierten Weltzeit (UTC) abweichen. Der Wert wird als „HTTP-date“ angegeben, wie im Abschnitt [3.3.1](https://www.w3.org/Protocols/rfc2616/rfc2616-sec3.html#sec3.3.1) beschrieben.
| **x-ms-date** | Gleich wie ```Date``` oben. Kann stattdessen verwendet werden, wenn der Agent nicht direkt auf den Anforderungsheader ```Date``` zugreifen kann oder dieser durch einen Proxy geändert wird. Wenn ```x-ms-date``` sowie ```Date``` angegeben ist, hat ```x-ms-date``` Vorrang. |
| **x-ms-content-sha256** | Base64-codierter SHA256-Hash des Anforderungstexts. Muss angegeben werden, auch wenn kein Text vorhanden ist. ```base64_encode(SHA256(body))```|
| **Autorisierung** | Authentifizierungsinformationen, die für das HMAC-SHA256-Schema erforderlich sind. Informationen zum Format sowie zusätzliche Informationen finden Sie weiter unten in diesem Artikel. |

**Beispiel:**

```http
Host: {myconfig}.azconfig.io
Date: Fri, 11 May 2018 18:48:36 GMT
x-ms-content-sha256: {SHA256 hash of the request body}
Authorization: HMAC-SHA256 Credential={Access Key ID}&SignedHeaders=x-ms-date;host;x-ms-content-sha256&Signature={Signature}
```

## <a name="authorization-header"></a>Authorization header (Autorisierungsheader)

### <a name="syntax"></a>Syntax

``Authorization``: **HMAC-SHA256** ```Credential```=\<value\>&```SignedHeaders```=\<value\>&```Signature```=\<value\>

|  Argument | BESCHREIBUNG  |
| ------ | ------ |
| **HMAC-SHA256** | Autorisierungsschema _(erforderlich)_ |
| **Credential** | Die ID des Zugriffsschlüssels, der zur Berechnung der Signatur verwendet wird. _(erforderlich)_ |
| **SignedHeaders** | HTTP-Anforderungsheader, die der Signatur hinzugefügt werden. _(erforderlich)_ |
| **Signature** | Base64-codierter HMACSHA256-Hash von String-To-Sign. _(erforderlich)_|

### <a name="credential"></a>Anmeldeinformationen

ID des Zugriffsschlüssels, der zur Berechnung von „Signature“ verwendet wird.

### <a name="signed-headers"></a>Signierte Header

Durch Semikolons getrennte Namen von HTTP-Anforderungsheadern, die zum Signieren der Anforderung erforderlich sind. Diese HTTP-Header müssen ebenfalls ordnungsgemäß mit der Anforderung angegeben werden. Verwenden Sie keine Leerzeichen.

### <a name="required-http-request-headers"></a>Erforderliche HTTP-Anforderungsheader

`x-ms-date`[oder `Date`];`host`;`x-ms-content-sha256`

Auch alle anderen HTTP-Anforderungsheader können der Signatur hinzugefügt werden. Fügen Sie sie einfach an das Argument ```SignedHeaders``` an.

**Beispiel:**

x-ms-date;host;x-ms-content-sha256;```Content-Type```;```Accept```

### <a name="signature"></a>Signatur

Base64-codierter HMACSHA256-Hash von „String-To-Sign“. Er verwendet den mit `Credential` angegebenen Zugriffsschlüssel. `base64_encode(HMACSHA256(String-To-Sign, Secret))`

### <a name="string-to-sign"></a>String-To-Sign

Kanonische Darstellung der Anforderung:

_String-To-Sign=_

**HTTP_METHOD** + '\n' + **path_and_query** + '\n' + **signed_headers_values**

|  Argument | BESCHREIBUNG  |
| ------ | ------ |
| **HTTP_METHOD** | Der mit der Anforderung verwendete HTTP-Methodenname in Großbuchstaben. Weitere Informationen finden Sie im Abschnitt [9](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). |
|**path_and_query** | Verkettung des absoluten URI-Pfads und der Abfragezeichenfolge der Anforderung. Weitere Informationen finden Sie im Abschnitt [3.3](https://tools.ietf.org/html/rfc3986#section-3.3).
| **signed_headers_values** | Durch Semikolons getrennte Werte aller in `SignedHeaders` aufgelisteten HTTP-Anforderungsheader. Das Format entspricht der Semantik von `SignedHeaders`. |

**Beispiel:**

```js
string-To-Sign=
            "GET" + '\n' +                                                                                  // VERB
            "/kv?fields=*&api-version=1.0" + '\n' +                                                         // path_and_query
            "Fri, 11 May 2018 18:48:36 GMT;{myconfig}.azconfig.io;{value of ms-content-sha256 header}"      // signed_headers_values
```


### <a name="errors"></a>Errors

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer
```

**Grund:** Der Anforderungsheader „Authorization“ wird beim HMAC-SHA256-Schema nicht angegeben.

**Lösung:** Geben Sie einen gültigen ```Authorization```-HTTP-Anforderungsheader an.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="The access token has expired", Bearer
```

**Grund:** Der ```Date```- oder ```x-ms-date```-Anforderungsheader weicht mehr als 15 Minuten von der aktuellen koordinierten Weltzeit (UTC) ab.

**Lösung:** Geben Sie das korrekte Datum und die richtige Uhrzeit an.


```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="Invalid access token date", Bearer
```

**Grund:** Fehlender oder ungültiger Anforderungsheader ```Date``` oder ```x-ms-date```.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="[Credential][SignedHeaders][Signature] is required", Bearer
```

**Grund:** Fehlender erforderlicher Parameter des Anforderungsheaders ```Authorization```.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="Invalid Credential", Bearer
```

**Grund:** Der angegebene Wert für [```Host```]/[Zugriffsschlüssel-ID] wurde nicht gefunden.

**Lösung:** Überprüfen Sie den ```Credential```-Parameter des ```Authorization```-Anforderungsheaders. Stellen Sie sicher, dass es sich um eine gültige Zugriffsschlüssel-ID handelt, und dass der ```Host```-Header auf das registrierte Konto verweist.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="Invalid Signature", Bearer
```

**Grund:** Der angegebene Wert für ```Signature``` stimmt nicht mit dem auf dem Server erwarteten Wert überein.

**Lösung:** Stellen Sie sicher, dass ```String-To-Sign``` korrekt ist. Vergewissern Sie sich, dass ```Secret``` korrekt ist und ordnungsgemäß verwendet wird (vor der Verwendung Base64-decodiert).

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="Signed request header 'xxx' is not provided", Bearer
```

**Grund:** Fehlender Anforderungsheader, der für den Parameter ```SignedHeaders``` im Header ```Authorization``` erforderlich ist.

**Lösung:** Geben Sie den erforderlichen Header mit dem richtigen Wert an.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="XXX is required as a signed header", Bearer
```

**Grund:** Fehlender Parameter in ```SignedHeaders```.

**Lösung:** Siehe Mindestanforderungen unter Signierte Header.

## <a name="code-snippets"></a>Codeausschnitte

### <a name="javascript"></a>JavaScript

*Voraussetzungen:* [Crypto-JS](https://code.google.com/archive/p/crypto-js/)

```javascript
function signRequest(host,
                     method,      // GET, PUT, POST, DELETE
                     url,         // path+query
                     body,        // request body (undefined of none)
                     credential,  // access key id
                     secret)      // access key value (base64 encoded)
{
        var verb = method.toUpperCase();
        var utcNow = new Date().toUTCString();
        var contentHash = CryptoJS.SHA256(body).toString(CryptoJS.enc.Base64);

        //
        // SignedHeaders
        var signedHeaders = "x-ms-date;host;x-ms-content-sha256"; // Semicolon separated header names

        //
        // String-To-Sign
        var stringToSign =
            verb + '\n' +                              // VERB
            url + '\n' +                               // path_and_query
            utcNow + ';' + host + ';' + contentHash;   // Semicolon separated SignedHeaders values

        //
        // Signature
        var signature = CryptoJS.HmacSHA256(stringToSign, CryptoJS.enc.Base64.parse(secret)).toString(CryptoJS.enc.Base64);

        //
        // Result request headers
        return [
            { name: "x-ms-date", value: utcNow },
            { name: "x-ms-content-sha256", value: contentHash },
            { name: "Authorization", value: "HMAC-SHA256 Credential=" + credential + "&SignedHeaders=" + signedHeaders + "&Signature=" + signature }
        ];
}
```

### <a name="c"></a>C#

```csharp
using (var client = new HttpClient())
{
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://{config store name}.azconfig.io/kv?api-version=1.0"),
        Method = HttpMethod.Get
    };

    //
    // Sign the request
    request.Sign(<Credential>, <Secret>);

    await client.SendAsync(request);
}

static class HttpRequestMessageExtensions
{
    public static HttpRequestMessage Sign(this HttpRequestMessage request, string credential, string secret)
    {
        string host = request.RequestUri.Authority;
        string verb = request.Method.ToString().ToUpper();
        DateTimeOffset utcNow = DateTimeOffset.UtcNow;
        string contentHash = Convert.ToBase64String(request.Content.ComputeSha256Hash());

        //
        // SignedHeaders
        string signedHeaders = "date;host;x-ms-content-sha256"; // Semicolon separated header names

        //
        // String-To-Sign
        var stringToSign = $"{verb}\n{request.RequestUri.PathAndQuery}\n{utcNow.ToString("r")};{host};{contentHash}";

        //
        // Signature
        string signature;

        using (var hmac = new HMACSHA256(Convert.FromBase64String(secret)))
        {
            signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.ASCII.GetBytes(stringToSign)));
        }

        //
        // Add headers
        request.Headers.Date = utcNow;
        request.Headers.Add("x-ms-content-sha256", contentHash);
        request.Headers.Authorization = new AuthenticationHeaderValue("HMAC-SHA256", $"Credential={credential}&SignedHeaders={signedHeaders}&Signature={signature}");

        return request;
    }
}

static class HttpContentExtensions
{
    public static byte[] ComputeSha256Hash(this HttpContent content)
    {
        using (var stream = new MemoryStream())
        {
            if (content != null)
            {
                content.CopyToAsync(stream).Wait();
                stream.Seek(0, SeekOrigin.Begin);
            }

            using (var alg = SHA256.Create())
            {
                return alg.ComputeHash(stream.ToArray());
            }
        }
    }
}
```

### <a name="java"></a>Java

```java
public CloseableHttpResponse signRequest(HttpUriRequest request, String credential, String secret)
        throws IOException, URISyntaxException {
    Map<String, String> authHeaders = generateHeader(request, credential, secret);
    authHeaders.forEach(request::setHeader);

    return httpClient.execute(request);
}

private static Map<String, String> generateHeader(HttpUriRequest request, String credential, String secret)
        throws URISyntaxException, IOException {
    String requestTime = GMT_DATE_FORMAT.format(new Date());

    String contentHash = buildContentHash(request);
    // SignedHeaders
    String signedHeaders = "x-ms-date;host;x-ms-content-sha256";

    // Signature
    String methodName = request.getRequestLine().getMethod().toUpperCase();
    URIBuilder uri = new URIBuilder(request.getRequestLine().getUri());
    String scheme = uri.getScheme() + "://";
    String requestPath = uri.toString().substring(scheme.length()).substring(uri.getHost().length());
    String host = new URIBuilder(request.getRequestLine().getUri()).getHost();
    String toSign = String.format("%s\n%s\n%s;%s;%s", methodName, requestPath, requestTime, host, contentHash);

    byte[] decodedKey = Base64.getDecoder().decode(secret);
    String signature = Base64.getEncoder().encodeToString(new HmacUtils(HMAC_SHA_256, decodedKey).hmac(toSign));

    // Compose headers
    Map<String, String> headers = new HashMap<>();
    headers.put("x-ms-date", requestTime);
    headers.put("x-ms-content-sha256", contentHash);

    String authorization = String.format("HMAC-SHA256 Credential=%s, SignedHeaders=%s, Signature=%s",
            credential, signedHeaders, signature);
    headers.put("Authorization", authorization);

    return headers;
}

private static String buildContentHash(HttpUriRequest request) throws IOException {
    String content = "";
    if (request instanceof HttpEntityEnclosingRequest) {
        try {
            StringWriter writer = new StringWriter();
            IOUtils.copy(((HttpEntityEnclosingRequest) request).getEntity().getContent(), writer,
                    StandardCharsets.UTF_8);

            content = writer.toString();
        }
        finally {
            ((HttpEntityEnclosingRequest) request).getEntity().getContent().close();
        }
    }

    byte[] digest = new DigestUtils(SHA_256).digest(content);
    return Base64.getEncoder().encodeToString(digest);
}
```

### <a name="golang"></a>Golang

```golang
import (
    "bytes"
    "crypto/hmac"
    "crypto/sha256"
    "encoding/base64"
    "io/ioutil"
    "net/http"
    "strings"
    "time"
)

//SignRequest Setup the auth header for accessing Azure App Configuration service
func SignRequest(id string, secret string, req *http.Request) error {
    method := req.Method
    host := req.URL.Host
    pathAndQuery := req.URL.Path
    if req.URL.RawQuery != "" {
        pathAndQuery = pathAndQuery + "?" + req.URL.RawQuery
    }

    content, err := ioutil.ReadAll(req.Body)
    if err != nil {
        return err
    }
    req.Body = ioutil.NopCloser(bytes.NewBuffer(content))

    key, err := base64.StdEncoding.DecodeString(secret)
    if err != nil {
        return err
    }

    timestamp := time.Now().UTC().Format(http.TimeFormat)
    contentHash := getContentHashBase64(content)
    stringToSign := fmt.Sprintf("%s\n%s\n%s;%s;%s", strings.ToUpper(method), pathAndQuery, timestamp, host, contentHash)
    signature := getHmac(stringToSign, key)

    req.Header.Set("x-ms-content-sha256", contentHash)
    req.Header.Set("x-ms-date", timestamp)
    req.Header.Set("Authorization", "HMAC-SHA256 Credential="+id+", SignedHeaders=x-ms-date;host;x-ms-content-sha256, Signature="+signature)

    return nil
}

func getContentHashBase64(content []byte) string {
    hasher := sha256.New()
    hasher.Write(content)
    return base64.StdEncoding.EncodeToString(hasher.Sum(nil))
}

func getHmac(content string, key []byte) string {
    hmac := hmac.New(sha256.New, key)
    hmac.Write([]byte(content))
    return base64.StdEncoding.EncodeToString(hmac.Sum(nil))
}
```

### <a name="python"></a>Python

```python

import base64
import hashlib
import hmac
from datetime import datetime
import six

def sign_request(host,
                method,     # GET, PUT, POST, DELETE
                url,        # Path + Query
                body,       # Request body
                credential, # Access Key ID
                secret):    # Access Key Value
    verb = method.upper()

    utc_now = str(datetime.utcnow().strftime("%b, %d %Y %H:%M:%S ")) + "GMT"

    if six.PY2:
        content_digest = hashlib.sha256(bytes(body)).digest()
    else:
        content_digest = hashlib.sha256(bytes(body, 'utf-8')).digest()

    content_hash = base64.b64encode(content_digest).decode('utf-8')

    # Signed Headers
    signed_headers = "x-ms-date;host;x-ms-content-sha256"  # Semicolon separated header names

    # String-To-Sign
    string_to_sign = verb + '\n' + \
                    url + '\n' + \
                    utc_now + ';' + host + ';' + content_hash  # Semicolon separated SignedHeaders values

    # Decode secret
    if six.PY2:
        decoded_secret = base64.b64decode(secret)
        digest = hmac.new(decoded_secret, bytes(
            string_to_sign), hashlib.sha256).digest()
    else:
        decoded_secret = base64.b64decode(secret, validate=True)
        digest = hmac.new(decoded_secret, bytes(
            string_to_sign, 'utf-8'), hashlib.sha256).digest()

    # Signature
    signature = base64.b64encode(digest).decode('utf-8')

    # Result request headers
    return {
        "x-ms-date": utc_now,
        "x-ms-content-sha256": content_hash,
        "Authorization": "HMAC-SHA256 Credential=" + credential + "&SignedHeaders=" + signed_headers + "&Signature=" + signature
    }
```

### <a name="powershell"></a>PowerShell

```PowerShell
function Sign-Request(
    [string] $hostname,
    [string] $method,      # GET, PUT, POST, DELETE
    [string] $url,         # path+query
    [string] $body,        # request body
    [string] $credential,  # access key id
    [string] $secret       # access key value (base64 encoded)
)
{  
    $verb = $method.ToUpperInvariant()
    $utcNow = (Get-Date).ToUniversalTime().ToString("R", [Globalization.DateTimeFormatInfo]::InvariantInfo)
    $contentHash = Compute-SHA256Hash $body

    $signedHeaders = "x-ms-date;host;x-ms-content-sha256";  # Semicolon separated header names

    $stringToSign = $verb + "`n" +
                    $url + "`n" +
                    $utcNow + ";" + $hostname + ";" + $contentHash  # Semicolon separated signedHeaders values

    $signature = Compute-HMACSHA256Hash $secret $stringToSign

    # Return request headers
    return @{
        "x-ms-date" = $utcNow;
        "x-ms-content-sha256" = $contentHash;
        "Authorization" = "HMAC-SHA256 Credential=" + $credential + "&SignedHeaders=" + $signedHeaders + "&Signature=" + $signature
    }
}

function Compute-SHA256Hash(
    [string] $content
)
{
    $sha256 = [System.Security.Cryptography.SHA256]::Create()
    try {
        return [Convert]::ToBase64String($sha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($content)))
    }
    finally {
        $sha256.Dispose()
    }
}

function Compute-HMACSHA256Hash(
    [string] $secret,      # base64 encoded
    [string] $content
)
{
    $hmac = [System.Security.Cryptography.HMACSHA256]::new([Convert]::FromBase64String($secret))
    try {
        return [Convert]::ToBase64String($hmac.ComputeHash([Text.Encoding]::ASCII.GetBytes($content)))
    }
    finally {
        $hmac.Dispose()
    }
}

# Stop if any error occurs
$ErrorActionPreference = "Stop"

$uri = [System.Uri]::new("https://{myconfig}.azconfig.io/kv?api-version=1.0")
$method = "GET"
$body = $null
$credential = "<Credential>"
$secret = "<Secret>"

$headers = Sign-Request $uri.Authority $method $uri.PathAndQuery $body $credential $secret
Invoke-RestMethod -Uri $uri -Method $method -Headers $headers -Body $body
```

### <a name="bash"></a>Bash

*Voraussetzungen:*

| Voraussetzung | Befehl | Getestete Versionen |
| ------------ | ------- | --------------- |
| [Bash](https://www.gnu.org/software/bash/) | Bash | 3.5.27, 4.4.23 |
| [coreutils](https://www.gnu.org/software/coreutils/) | tr | 8.28 |
| [curl](https://curl.haxx.se/) | curl | 7.55.1, 7.58.0 |
| [OpenSSL](https://www.openssl.org/) | openssl | 1.1.0g, 1.1.1a |
| [util-linux](https://github.com/karelzak/util-linux/) | hexdump | 2.14.1, 2.31.1 |

```bash
#!/bin/bash

sign_request () {
    local host="$1"
    local method="$2"      # GET, PUT, POST, DELETE
    local url="$3"         # path+query
    local body="$4"        # request body
    local credential="$5"  # access key id
    local secret="$6"      # access key value (base64 encoded)

    local verb=$(printf "$method" | tr '[:lower:]' '[:upper:]')
    local utc_now="$(date -u '+%a, %d %b %Y %H:%M:%S GMT')"
    local content_hash="$(printf "$body" | openssl sha256 -binary | base64)"

    local signed_headers="x-ms-date;host;x-ms-content-sha256"  # Semicolon separated header names
    local string_to_sign="$verb\n$url\n$utc_now;$host;$content_hash"  # Semicolon separated signed_headers values

    local decoded_secret="$(printf "$secret" | base64 -d | hexdump -v -e '/1 "%02x"')"
    local signature="$(printf "$string_to_sign" | openssl sha256 -mac HMAC -macopt hexkey:"$decoded_secret" -binary | base64)"

    # Output request headers
    printf '%s\n' \
           "x-ms-date: $utc_now" \
           "x-ms-content-sha256: $content_hash" \
           "Authorization: HMAC-SHA256 Credential=$credential&SignedHeaders=$signed_headers&Signature=$signature"
}

host="{config store name}.azconfig.io"
method="GET"
url="/kv?api-version=1.0"
body=""
credential="<Credential>"
secret="<Secret>"

headers=$(sign_request "$host" "$method" "$url" "$body" "$credential" "$secret")

while IFS= read -r line; do
    header_args+=("-H$line")
done <<< "$headers"
curl -X "$method" -d "$body" "${header_args[@]}" "https://$host$url"
```
