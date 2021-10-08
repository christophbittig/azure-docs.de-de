---
title: 'REST-API-Fehlercodes: Azure Key Vault'
description: Diese Fehlercodes können von einem Vorgang eines Azure Key Vault-Webdiensts zurückgegeben werden.
keywords: ''
services: machine-learning
author: msmbaldwin
ms.custom: seodec18
ms.author: mbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 12/16/2019
ms.openlocfilehash: de0aa3f3149141ddacd7906297656d58b8fad24c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128633999"
---
# <a name="azure-key-vault-rest-api-error-codes"></a>Azure Key Vault: REST-API-Fehlercodes
 
Die folgenden Fehlercodes können von einem Vorgang eines Azure Key Vault-Webdiensts zurückgegeben werden.
 
## <a name="http-401-unauthenticated-request"></a>HTTP 401: Nicht authentifizierte Anforderung

401 bedeutet, dass die Anforderung für Key Vault nicht authentifiziert ist. 

Eine Anforderung ist authentifiziert, wenn Folgendes gilt:

- Der Schlüsseltresor kennt die Identität des Aufrufers.
- Der Aufrufer darf versuchen, auf Key Vault-Ressourcen zuzugreifen. 

Es gibt verschiedene Gründe, warum eine Anforderung 401 zurückgeben kann.

### <a name="no-authentication-token-attached-to-the-request"></a>An die Anforderung ist kein Authentifizierungstoken angefügt. 

Hier ist ein Beispiel für eine PUT-Anforderung, bei der der Wert eines Geheimnisses festgelegt wird:

``` 
PUT https://putreqexample.vault.azure.net//secrets/DatabaseRotatingPassword?api-version=7.0 HTTP/1.1
x-ms-client-request-id: 03d275a2-52a4-4bed-82c8-6fe15165affb
accept-language: en-US
Authorization: Bearer     eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9.eyJhdWQiOiJodHRwczovL3ZhdWx0LmF6dXJlLm5ldCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpYXQiOjE1NDg2OTc1MTMsIm5iZiI6MTU0ODY5NzUxMywiZXhwIjoxNTQ4NzAxNDEzLCJhaW8iOiI0MkpnWUhoODVqaVBnZHF5ZlRGZE5TdHY3bGUvQkFBPSIsImFwcGlkIjoiZmFkN2Q1YjMtNjlkNi00YjQ4LTkyNTktOGQxMjEyNGUxY2YxIiwiYXBwaWRhY3IiOiIxIiwiaWRwIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3LyIsIm9pZCI6IjM5NzVhZWVkLTdkMDgtNDUzYi1iNmY0LTQ0NWYzMjY5ODA5MSIsInN1YiI6IjM5NzVhZWVkLTdkMDgtNDUzYi1iNmY0LTQ0NWYzMjY5ODA5MSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInV0aSI6IjItZ3JoUmtlSWs2QmVZLUxuNDJtQUEiLCJ2ZXIiOiIxLjAifQ.fgubiz1MKqTJTXI8dHIV7t9Fle6FdHrkaGYKcBeVRX1WtLVuk1QVxzIFDlZKLXJ7QPNs0KWpeiWQI9IWIRK-8wO38yCqKTfDlfHOiNWGOpkKddlG729KFqakVf2w0GPyGPFCONRDAR5wjQarN9Bt8I8YbHwZQz_M1hztlnv-Lmsk1jBmech9ujD9-lTMBmSfFFbHcqquev119V7sneI-zxBZLf8C0pIDkaXf1t8y6Xr8CUJDMdlWLslCf3pBCNIOy65_TyGvy4Z4AJryTPBarNBPwOkNAtjCfZ4BDc2KqUZM5QN_VK4foP64sVzUL6mSr0Gh7lQJIL5b1qIpJxjxyQ
User-Agent: FxVersion/4.7.3324.0 OSName/Windows OSVersion/6.2.9200.0 Microsoft.Azure.KeyVault.KeyVaultClient/3.0.3.0
Content-Type: application/json; charset=utf-8
Host: putreqexample.vault.azure.net
Content-Length: 31

{
   "value": "m*gBJ7$Zuoz)"
}
```

Der Authorization-Header ist das Zugriffstoken, das bei jedem Aufruf von Key Vault für Vorgänge der Datenebene erforderlich ist. Wenn der Header nicht vorhanden ist, muss die Antwort 401 lauten.

### <a name="the-token-lacks-the-correct-resource-associated-with-it"></a>Dem Token fehlt die richtige zugeordnete Ressource. 

Wenn ein Zugriffstoken vom Azure OAuth-Endpunkt angefordert wird, ist ein Parameter mit dem Namen „resource“ obligatorisch. Der Wert ist für den Tokenanbieter wichtig, da er den Gültigkeitsbereich des Tokens für die beabsichtigte Verwendung festlegt. Die Ressource für **alle** Token für den Zugriff auf eine Key Vault-Instanz ist *https:\//vault.keyvault.net* (ohne nachgestellten Schrägstrich).

### <a name="the-token-is-expired"></a>Das Token ist abgelaufen.

Token sind Base64-codiert, und die Werte können auf Websites wie [http://jwt.calebb.net](http://jwt.calebb.net) decodiert werden. Dies ist das Token oben, das decodiert wurde:

```
    {
 typ: "JWT",
 alg: "RS256",
 x5t: "nbCwW11w3XkB-xUaXwKRSLjMHGQ",
 kid: "nbCwW11w3XkB-xUaXwKRSLjMHGQ"
}.
{
 aud: "https://vault.azure.net",
 iss: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
 iat: 1548697513,
 nbf: 1548697513,
 exp: 1548701413,
 aio: "42JgYHh85jiPgdqyfTFdNStv7le/BAA=",
 appid: "fad7d5b3-69d6-4b48-9259-8d12124e1cf1",
 appidacr: "1",
 idp: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
 oid: "3975aeed-7d08-453b-b6f4-445f32698091",
 sub: "3975aeed-7d08-453b-b6f4-445f32698091",
 tid: "72f988bf-86f1-41af-91ab-2d7cd011db47",
 uti: "2-grhRkeIk6BeY-Ln42mAA",
 ver: "1.0"
}.
[signature]
```

In diesem Token finden sich viele wichtige Informationen:

- aud (audience, Zielgruppe) Die Ressource des Tokens. Beachten Sie, dass dies `https://vault.azure.net` ist. Dieses Token funktioniert NICHT für Ressourcen, die nicht explizit mit diesem Wert übereinstimmen, z.B. Graph.
- iat (issued at, ausgestellt um): Die Anzahl der Takte seit dem Beginn der Epoche, in der das Token ausgestellt wurde.
- nbf (not before, nicht bevor): Die Anzahl der Takte seit dem Beginn der Epoche, in der dieses Token gültig wird.
- exp (expiration, Ablauf): Die Anzahl der Takte seit dem Beginn der Epoche, in der dieses Token abläuft.
- appid (application ID, Anwendungs-ID): Die GUID für die Anwendungs-ID, die diese Anforderung vornimmt.
- tid (tenant ID, Mandanten-ID): Die GUID für die Mandanten-ID des Prinzipals, der diese Anforderung vornimmt.

Es ist wichtig, dass alle Werte im Token ordnungsgemäß identifiziert werden, damit die Anforderung funktioniert. Wenn alles richtig ist, führt die Anforderung nicht zu Fehlercode 401.

### <a name="troubleshooting-401"></a>Problembehandlung von Fehlercode 401

Fehlercodes des Typs 401 sollten ab dem Zeitpunkt der Tokengenerierung untersucht werden, bevor die Anforderung an den Schlüsseltresor vorgenommen wird. Im Allgemeinen wird Code verwendet, um das Token anzufordern. Nachdem das Token empfangen wurde, wird es an die Key-Vault Anforderung übergeben. Wenn der Code lokal ausgeführt wird, können Sie mit Fiddler die Anforderung/Antwort an `https://login.microsoftonline.com` erfassen. Eine Anforderung sieht wie folgt aus:

``` 
POST https://login.microsoftonline.com/<key vault tenant ID>/oauth2/token HTTP/1.1
Accept: application/json
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Host: login.microsoftonline.com
Content-Length: 192

resource=https%3A%2F%2Fvault.azure.net&client_id=<registered-app-ID>&client_secret=<registered-app-secret>&client_info=1&grant_type=client_credentials
```

Die folgenden vom Benutzer bereitgestellten Informationen müssen richtig sein:

- Die Mandanten-ID des Schlüsseltresors
- Ressourcenwert festgelegt auf https%3A%2F%2Fvault.azure.net (URL-codiert)
- Client-ID
- Geheimer Clientschlüssel

Stellen Sie sicher, dass die restliche Anforderung nahezu identisch ist.

Wenn Sie nur das Antwortzugriffstoken abrufen können, können Sie es (wie oben gezeigt) decodieren, um die Mandanten-ID, die Client-ID (App-ID) und die Ressource sicherzustellen.

## <a name="http-403-insufficient-permissions"></a>HTTP 403: Unzureichende Berechtigungen

HTTP 403 bedeutet, dass die Anforderung authentifiziert wurde (die anfordernde Identität ist bekannt), die Identität aber keine Berechtigung für den Zugriff auf die angeforderte Ressource besitzt. Da für gibt es zwei Gründe:

- Es ist keine Zugriffsrichtlinie für die Identität vorhanden.
- Die IP-Adresse der anfordernden Ressource ist in den Firewalleinstellungen des Schlüsseltresors nicht genehmigt.

HTTP 403 tritt häufig auf, wenn die Anwendung des Kunden nicht die Client-ID verwendet, die der Kunde meint. Dies bedeutet in der Regel, dass die Zugriffsrichtlinien für die tatsächliche aufrufende Identität nicht ordnungsgemäß eingerichtet sind.

Wenn Sie sofort nach dem Hinzufügen einer Identität zur Zugriffsrichtlinie einen 403-Fehler erhalten, können Sie diesen behandeln, indem Sie regelmäßige Wiederholungsversuche hinzufügen.

### <a name="troubleshooting-403"></a>Problembehandlung von Fehlercode 403

Aktivieren Sie zunächst die Protokollierung. Anleitungen hierzu finden Sie unter [Azure Key Vault-Protokollierung](logging.md).

Nachdem die Protokollierung aktiviert wurde, können Sie bestimmen, ob der Fehlercode 403 auf die Zugriffsrichtlinie oder die Firewallrichtlinie zurückzuführen ist.

#### <a name="error-due-to-firewall-policy"></a>Fehler aufgrund einer Firewallrichtlinie

„Client address (00.00.00.00) is not authorized and caller is not a trusted service“ (Die Client Adresse (00.00.00.00) ist nicht autorisiert, und der Aufrufer ist kein vertrauenswürdiger Dienst)

Es gibt eine begrenzte Liste der „Vertrauenswürdigen Azure-Dienste“. Azure-Websites sind **kein** vertrauenswürdiger Azure-Dienst. Weitere Informationen finden Sie im Blogbeitrag [Key Vault-Firewallzugriff durch Azure App Services](https://azidentity.azurewebsites.net/post/2019/01/03/key-vault-firewall-access-by-azure-app-services).

Sie müssen dem Key Vault die IP-Adresse der Azure-Website hinzufügen, damit sie funktioniert.

Wenn der Fehler aufgrund der Zugriffsrichtlinie auftritt: Suchen Sie die Objekt-ID für die Anforderung, und stellen Sie sicher, dass die Objekt-ID mit dem Objekt übereinstimmt, dem der Benutzer die Zugriffsrichtlinie zuweisen möchte. In der AAD sind häufig mehrere Objekte vorhanden, die denselben Namen aufweisen. Daher ist die Auswahl des richtigen Objekts sehr wichtig. Durch Löschen und erneutes Hinzufügen der Zugriffsrichtlinie ist es möglich, zu überprüfen, ob mehrere Objekte mit demselben Namen vorhanden sind.

Außerdem ist für die meisten Zugriffsrichtlinien die Verwendung der „autorisierten Anwendung“ nicht erforderlich, wie im Portal gezeigt. Autorisierte Anwendungen werden für Authentifizierungsszenarien des Typs „im Auftrag von“ verwendet, die selten vorkommen. 


## <a name="http-429-too-many-requests"></a>HTTP 429: Zu viele Anforderungen

Eine Drosselung tritt auf, wenn die Anzahl der Anforderungen den für den Zeitrahmen angegebenen maximalen Wert überschreitet. Wenn Drosselung auftritt, ist die Antwort des Key Vault HTTP 429. Es gibt für die Typen der vorgenommenen Anforderungen angegebene Höchstwerte. Beispiel: Die Erstellung eines HSM-2048-Bit-Schlüssels beträgt 5 Anforderungen pro 10 Sekunden, aber alle anderen HSM-Transaktionen weisen ein Limit von 1.000 Anforderungen/10 Sekunden auf. Daher ist es bei der Ermittlung der Ursache der Drosselung wichtig zu verstehen, welche Arten von Aufrufen durchgeführt werden.
Im Allgemeinen sind Anforderungen an den Key Vault auf 2.000 Anforderungen/10 Sekunden beschränkt. Ausnahmen sind wichtige Vorgänge, die unter [Limits des Key Vault-Diensts](service-limits.md) dokumentiert werden.

### <a name="troubleshooting-429"></a>Problembehandlung von Fehlercode 429
Drosselung wird durch die folgenden Techniken umgangen:

- Verringern Sie die Anzahl der Anforderungen an den Key Vault, indem Sie feststellen, ob es Muster zu einer angeforderten Ressource gibt, und versuchen Sie, diese in der aufrufenden Anwendung zwischenzuspeichern. 

- Wenn Key Vault-Drosselung auftritt, passen Sie den anfordernden so Code an, dass er ein exponentielles Backoff für Wiederholungsversuche verwendet. Der Algorithmus wird hier erläutert: [Drosselung Ihrer App](overview-throttling.md#how-to-throttle-your-app-in-response-to-service-limits)

- Wenn die Anzahl der Anforderungen durch Zwischenspeichern nicht reduziert werden kann und ein zeitgesteuertes Backoff nicht funktioniert, sollten Sie die Schlüssel auf mehrere Key Vaults aufteilen. Das Dienstlimit für ein einzelnes Abonnement beträgt das Fünffache des einzelnen Key Vault-Limits. Wenn Sie mehr als fünf Key Vaults verwenden, sollten Sie die Verwendung mehrerer Abonnements in Betracht ziehen. 

Ausführliche Anleitungen einschließlich der Anforderung zur Erhöhung der Limits finden Sie hier: [Anleitung zur Drosselung von Key Vault](overview-throttling.md)
