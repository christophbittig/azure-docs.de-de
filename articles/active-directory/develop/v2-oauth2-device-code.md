---
title: OAuth 2.0-Gerätecodeflow | Azure
titleSuffix: Microsoft identity platform
description: Anmelden von Benutzern ohne Browser. Erstellen von eingebetteten und browserlosen Authentifizierungsflows unter Verwendung der Geräteautorisierungsgenehmigung.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 06/25/2021
ms.author: hirsin
ms.reviewer: marsma
ms.custom: aaddev
ms.openlocfilehash: 053f56f56c28560b700122a99fc2d97f4c8e0279
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/07/2021
ms.locfileid: "122639781"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-authorization-grant-flow"></a>Microsoft Identity Platform und der OAuth 2.0-Flow für die Geräteautorisierungsgenehmigung

Microsoft Identity Platform unterstützt die [Geräteautorisierungsgenehmigung](https://tools.ietf.org/html/rfc8628), die es Benutzern ermöglicht, sich bei eingabeeingeschränkten Geräten wie einem Smart-TV, IoT-Geräten oder einem Drucker anzumelden.  Um diesen Flow zu ermöglichen, veranlasst das Gerät den Benutzer zum Besuch einer Webseite im Browser auf einem anderen Gerät, um sich anzumelden.  Sobald sich der Benutzer anmeldet, kann das Gerät nach Bedarf Zugriffstoken und Aktualisierungstoken abrufen.

In diesem Artikel wird beschrieben, wie Sie direkt mit dem Protokoll in Ihrer Anwendung programmieren.  Es wird stattdessen empfohlen, ggf. die unterstützten Microsoft Authentication Libraries (MSAL) zu verwenden, um [Token zu erhalten und gesicherte Web-APIs aufzurufen](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Sehen Sie sich auch die [Beispiel-Apps an, die MSAL verwenden](sample-v2-code.md).

[!INCLUDE [try-in-postman-link](includes/try-in-postman-link.md)]

## <a name="protocol-diagram"></a>Protokolldiagramm

Der gesamte Gerätecodeflow sieht ähnlich wie im nächsten Diagramm aus. Wir beschreiben die einzelnen Schritte weiter unten in diesem Artikel.

![Gerätecodefluss](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>Geräteautorisierungsanforderung

Der Client muss zuerst den Authentifizierungsserver auf einen Geräte- und Benutzercode überprüfen, die zum Initiieren der Authentifizierung verwendet werden. Der Client sammelt diese Anforderung vom `/devicecode`-Endpunkt. In diese Anforderung sollte der Client außerdem die Berechtigungen aufnehmen, die er vom Benutzer erhalten muss. Ab dem Zeitpunkt des Absendens dieser Anforderung hat der Benutzer nur 15 Minuten, um sich anzumelden (der übliche Wert für `expires_in`), weshalb Sie diese Anforderung nur stellen sollten, wenn der Benutzer angezeigt hat, dass er zur Anmeldung bereit ist.

```HTTP
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=user.read%20openid%20profile

```

| Parameter | Bedingung | BESCHREIBUNG |
| --- | --- | --- |
| `tenant` | Erforderlich | Kann "/common", „/consumers“ oder „/organizations“ sein.  Dies kann auch der Verzeichnismandant sein, von dem Sie die Berechtigung im GUID- oder Anzeigenamensformat anfordern möchten.  |
| `client_id` | Erforderlich | Die **Anwendungs-ID (Client-ID)** , die Ihrer App im [Azure-Portal auf der Seite „App-Registrierungen“](https://go.microsoft.com/fwlink/?linkid=2083908) zugewiesen wurde. |
| `scope` | Erforderlich | Eine durch Leerzeichen getrennte Liste mit [Bereichen](v2-permissions-and-consent.md) , denen der Benutzer zustimmen soll.  |

### <a name="device-authorization-response"></a>Geräteautorisierungsantwort

Eine erfolgreiche Antwort wird ein JSON-Objekt sein, das die erforderlichen Informationen enthält, um dem Benutzer die Anmeldung zu ermöglichen.

| Parameter | Format | BESCHREIBUNG |
| ---              | --- | --- |
|`device_code`     | String | Eine lange Zeichenfolge, die zum Verifizieren der Sitzung zwischen dem Client und dem Autorisierungsserver verwendet wird. Der Client fordert über diesen Parameter das Zugriffstoken vom Autorisierungsserver an. |
|`user_code`       | String | Eine kurze, für den Benutzer angezeigte Zeichenfolge, mit der die Sitzung auf einem sekundären Gerät identifiziert wird.|
|`verification_uri`| URI | Der URI, den der Benutzer mit dem `user_code` besuchen sollte, um sich anzumelden. |
|`expires_in`      | INT | Die Anzahl der Sekunden, bevor `device_code` und `user_code` ablaufen. |
|`interval`        | INT | Die Anzahl der Sekunden, die der Client zwischen Abrufanforderungen warten soll. |
| `message`        | String | Eine lesbare Zeichenfolge mit Anweisungen für den Benutzer. Diese kann lokalisiert werden, indem ein **Abfrageparameter** in die Anforderung des Formulars `?mkt=xx-XX` aufgenommen und der entsprechende Sprachkulturcode eingetragen wird. |

> [!NOTE]
> Das Antwortfeld `verification_uri_complete` ist derzeit nicht vorhanden bzw. wird nicht unterstützt.  Dies ist wichtig zu wissen, da im [Standard](https://tools.ietf.org/html/rfc8628)`verification_uri_complete` als optionaler Teil des Gerätecodeflow-Standards aufgeführt wird.

## <a name="authenticating-the-user"></a>Authentifizieren des Benutzers

Nach dem Empfang von `user_code` und `verification_uri` zeigt der Client diese für den Benutzer an und weist ihn an, sich unter Verwendung seines Mobiltelefons oder PC-Browsers anzumelden.

Wenn sich der Benutzer mit einem persönlichen Konto (mit „/common“ oder „/consumers“) authentifiziert, wird er aufgefordert, sich erneut anzumelden, um den Authentifizierungsstatus ans Gerät zu übertragen.  Außerdem wird er aufgefordert, seine Zustimmung zu erteilen, um sicherzustellen, dass er die gewährten Berechtigungen kennt.  Dies gilt nicht für Geschäfts-, Schul- oder Unikonten, die für die Authentifizierung verwendet werden.

Während sich der Benutzer bei dem `verification_uri` authentifiziert, sollte der Client beim `/token`-Endpunkt das angeforderte Token mithilfe des `device_code` abrufen.

```HTTP
POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type=urn:ietf:params:oauth:grant-type:device_code
&client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&device_code=GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8...
```

| Parameter | Erforderlich | BESCHREIBUNG|
| -------- | -------- | ---------- |
| `tenant`  | Erforderlich | Derselbe Mandant oder Mandantenalias, der in der ursprünglichen Anforderung verwendet wurde. |
| `grant_type` | Erforderlich | Muss gleich `urn:ietf:params:oauth:grant-type:device_code` sein.|
| `client_id`  | Erforderlich | Muss mit der in der anfänglichen Anforderung verwendeten `client_id` übereinstimmen. |
| `device_code`| Erforderlich | Der in der Geräteautorisierungsanforderung zurückgegebene `device_code`.  |

### <a name="expected-errors"></a>Erwartete Fehler

Da der Gerätecodeflow ein Abrufprotokoll ist, muss der Client davon ausgehen, Fehler zu erhalten, bevor der Benutzer die Authentifizierung abgeschlossen hat.

| Fehler | BESCHREIBUNG | Clientaktion |
| ------ | ----------- | -------------|
| `authorization_pending` | Der Benutzer hat die Authentifizierung nicht abgeschlossen, den Flow aber nicht abgebrochen. | Wiederholen Sie die Anforderung nach mindestens `interval` Sekunden. |
| `authorization_declined` | Der Endbenutzer hat die Autorisierungsanforderung verweigert.| Beenden Sie das Abrufen, und kehren Sie in einen nicht authentifizierten Zustand zurück.  |
| `bad_verification_code`| Der an den `/token`-Endpunkt gesendete `device_code` wurde nicht erkannt. | Stellen Sie sicher, dass der Client den richtigen `device_code` in der Anforderung sendet. |
| `expired_token` | Mindestens `expires_in` Sekunden sind verstrichen, und die Authentifizierung ist mit diesem `device_code` nicht mehr möglich. | Beenden des Abrufens und Wiederherstellen eines nicht authentifizierten Zustands. |

### <a name="successful-authentication-response"></a>Erfolgreiche Authentifizierungsantwort

Eine erfolgreiche Tokenantwort sieht wie folgt aus:

```json
{
    "token_type": "Bearer",
    "scope": "User.Read profile openid email",
    "expires_in": 3599,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```

| Parameter | Format | BESCHREIBUNG |
| --------- | ------ | ----------- |
| `token_type` | String| Immer „Bearer. |
| `scope` | Durch Leerzeichen getrennte Zeichenfolgen | Wenn ein Zugriffstoken zurückgegeben wurde, werden hierdurch die Bereiche aufgeführt, für die das Zugriffstoken gültig ist. |
| `expires_in`| INT | Anzahl der Sekunden, bevor das enthaltene Zugriffstoken gültig ist. |
| `access_token`| Nicht transparente Zeichenfolge | Ausgestellt für die [Bereiche](v2-permissions-and-consent.md), die angefordert wurden.  |
| `id_token`   | JWT | Ausgestellt, wenn der ursprüngliche `scope`-Parameter den `openid`-Bereich enthalten hat.  |
| `refresh_token` | Nicht transparente Zeichenfolge | Ausgestellt, wenn der ursprüngliche `scope`-Parameter `offline_access` enthalten hat.  |

Sie können mit dem Aktualisierungstoken neue Zugriffstoken und Aktualisierungstoken abrufen. Verwenden Sie dazu den in der [Dokumentation für den OAuth-Codeflow](v2-oauth2-auth-code-flow.md#refresh-the-access-token) beschriebenen Flow.

[!INCLUDE [remind-not-to-validate-access-tokens](includes/remind-not-to-validate-access-tokens.md)]
