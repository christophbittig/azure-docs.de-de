---
title: Microsoft Identity Platform – ID-Token | Azure
titleSuffix: Microsoft identity platform
description: Hier erfahren Sie mehr über die Verwendung von ID-Token, die von den Azure AD v1.0- und Microsoft Identity Platform (v2.0)-Endpunkten ausgegeben werden.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 06/25/2021
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: fb20ed3a3a09c7bef7495d439ad2856f646e8a51
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131067190"
---
# <a name="microsoft-identity-platform-id-tokens"></a>Microsoft Identity Platform – ID-Token

Das ID-Token ist die Kernerweiterung, die [OpenID Connect](v2-protocols-oidc.md) für OAuth 2.0 vornimmt. Die ID-Token werden vom Autorisierungsserver ausgestellt und enthalten die Ansprüche mit den Informationen über den Benutzer. Sie können zusammen mit einem Zugriffstoken oder anstelle dessen gesendet werden. Die Informationen in den ID-Tokens ermöglichen es dem Client, zu überprüfen, ob ein Benutzer derjenige ist, der er vorgibt zu sein. Die ID-Token sollen von Drittanbieteranwendungen verstanden werden. Die ID-Token sollten nicht zu Autorisierungszwecken verwendet werden. Die [Zugriffstoken](access-tokens.md) werden für die Autorisierung verwendet. Die von den ID-Token bereitgestellten Ansprüche können für die Benutzeroberfläche in Ihrer Anwendung, als [Schlüssel in einer Datenbank](#using-claims-to-reliably-identify-a-user-subject-and-object-id), verwendet werden. Sie können außerdem den Zugriff auf die Clientanwendung bereitstellen.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie diesen Artikel durchgehen, wird Ihnen der folgende Artikel von Nutzen sein:

* Die [OAuth 2.0 und OpenID-Verbindungsprotokolle](active-directory-v2-protocols.md) auf der Microsoft Identity Platform


## <a name="claims-in-an-id-token"></a>Die Ansprüche in einem ID-Token

Die ID-Token sind [JSON-Webtoken (JWT)](https://wikipedia.org/wiki/JSON_Web_Token). Diese ID-Token bestehen aus einem Header, einer Nutzlast und einer Signatur. Der Header und die Signatur werden verwendet, um die Authentizität des Tokens zu überprüfen, während die Nutzlast die Informationen über den von Ihrem Client angeforderten Benutzer enthält. Die ID-Token v1.0 und v2.0 tragen unterschiedliche Informationen. Die Version basiert auf dem Endpunkt, von dem aus die Anforderung erfolgt ist. Obwohl die vorhandenen Anwendungen wahrscheinlich den Azure AD-Endpunkt (v1.0) verwenden, sollten die neuen Anwendungen den „Microsoft Identity Platform“ Endpunkt (v2.0) verwenden.

* v1.0: Der Azure AD-Endpunkt: `https://login.microsoftonline.com/common/oauth2/authorize`
* v2.0: Der Microsoft Identity Platform-Endpunkt: `https://login.microsoftonline.com/common/oauth2/v2.0/authorize`

### <a name="sample-v10-id-token"></a>Beispiel: Ein v1.0 ID-Token

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q
```

Zeigen Sie dieses v1. 0-Beispieltoken in [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q) an.

### <a name="sample-v20-id-token"></a>Beispiel: Ein v2.0 ID-Token

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw
```

Zeigen Sie dieses v2. 0-Beispieltoken in [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw) an.

Alle unten aufgeführten JWT-Ansprüche werden in v1.0- und v2.0-Token angezeigt, sofern nicht anders angegeben.

### <a name="header-claims"></a>Headeransprüche

Die folgende Tabelle zeigt die Header-Ansprüche, die in den ID-Token vorhanden sind.

|Anspruch | Format | BESCHREIBUNG |
|-----|--------|-------------|
|`typ` | Zeichenfolge – immer „JWT“ | Gibt an, dass das Token ein JWT-Token ist.|
|`alg` | String | Gibt den Algorithmus an, mit dem das Token signiert wurde. Beispiel: "RS256" |
| `kid` | String | Gibt den Fingerabdruck für den öffentlichen Schlüssel an, mit dem die Signatur dieses Tokens überprüft werden kann. Wird in v1.0- und v2.0-ID-Token ausgegeben. |
| `x5t` | String | Hat dieselbe Funktion (hinsichtlich Verwendung und Wert) wie `kid`. `x5t` ist ein Legacyanspruch, der aus Kompatibilitätsgründen nur in v1.0-ID-Token ausgegeben wird. |

### <a name="payload-claims"></a>Nutzlastansprüche

Die nachfolgende Liste zeigt die Ansprüche, die (sofern nichts anderes angegeben ist) in den meisten ID-Token standardmäßig enthalten sind.  Allerdings kann Ihre App die[optionalen Ansprüche](active-directory-optional-claims.md) verwenden, um zusätzliche Ansprüche in dem ID-Token anzufordern. Optionale Ansprüche können von dem `groups` Anspruch bis zu den Informationen des Benutzernamens reichen.

|Anspruch | Format | Beschreibung |
|-----|--------|-------------|
|`aud` |  Die Zeichenfolge einer App-ID-GUID | Identifiziert den vorgesehenen Empfänger des Tokens. In `id_tokens` ist die Zielgruppe die Anwendungs-ID Ihrer App, die Ihrer App im Azure-Portal zugewiesen ist. Dieser Wert sollte überprüft werden. Das Token sollte abgelehnt werden, wenn es nicht mit der Anwendungs-ID Ihrer App übereinstimmt. |
|`iss` |  Die Zeichenfolge einer Aussteller-URI | Sie identifiziert den Aussteller oder den „Autorisierungsserver“, der das Token erstellt und zurückgibt. Sie identifiziert auch den Azure AD-Mandanten, für den der Benutzer authentifiziert wurde. Wenn das Token vom v2.0-Endpunkt ausgegeben wurde, endet der URI mit `/v2.0`.  Die GUID, die angibt, dass der Benutzer ein Consumer-Benutzer eines Microsoft-Kontos ist, lautet `9188040d-6c67-4c5b-b112-36a304b66dad`. Ihre App sollte ggf. den GUID-Teil des Anspruchs verwenden, um die Mandanten einzuschränken, die sich bei der App anmelden können. |
|`iat` |  int, ein UNIX-Zeitstempel | „Issued At“ gibt an, wann die Authentifizierung für dieses Token erfolgt ist.  |
|`idp`|Zeichenfolge, in der Regel ein STS-URI | Der Identitätsanbieter, der den Antragsteller des Tokens authentifiziert hat. Dieser Wert ist identisch mit dem Wert des Ausstelleranspruchs, es sei denn, das Benutzerkonto ist nicht im gleichen Mandanten wie der Aussteller vorhanden (etwa Gäste). Wenn der Anspruch nicht vorhanden ist, bedeutet dies, dass stattdessen der Wert `iss` verwendet werden kann.  Für in einem Organisationskontext verwendete persönliche Konten (etwa ein zu einem Azure AD-Mandanten eingeladenes persönliches Konto) kann der `idp`-Anspruch „live.com“ oder ein STS-URI sein, der den Microsoft-Kontomandanten `9188040d-6c67-4c5b-b112-36a304b66dad` enthält. |
|`nbf` |  int, ein UNIX-Zeitstempel | Der Anspruch „nbf“ (nicht vor) gibt die Zeit an, vor der das JWT NICHT für die Bearbeitung akzeptiert werden darf.|
|`exp` |  int, ein UNIX-Zeitstempel | Der Anspruch „exp“ (Ablaufzeit) gibt die Ablaufzeit an, ab oder nach der das JWT zur Bearbeitung **nicht akzeptiert werden darf**.  Es ist wichtig zu beachten, dass eine Ressource unter bestimmten Umständen das Token vor diesem Zeitpunkt ablehnen kann. Das kann zum Beispiel geschehen, wenn eine Änderung der Authentifizierung erforderlich ist oder ein Tokenwiderruf erkannt wurde. |
| `c_hash`| String |Der Codehash ist nur dann in ID-Token enthalten, wenn das ID-Token zusammen mit einem OAuth 2.0-Autorisierungscode ausgestellt wird. Mit seiner Hilfe kann die Authentizität eines Autorisierungscodes überprüft werden. Um zu verstehen, wie diese Überprüfung durchgeführt wird, finden Sie Informationen in der [OpenID Connect-Spezifikation](https://openid.net/specs/openid-connect-core-1_0.html#HybridIDToken). |
|`at_hash`| String |Der Zugriffstokenhash ist nur in ID-Token enthalten, wenn das ID-Token vom `/authorize`-Endpunkt zusammen mit einem OAuth 2.0-Zugriffstoken ausgestellt wird. Mit seiner Hilfe kann die Authentizität eines Zugriffstokens überprüft werden. Um zu verstehen, wie diese Überprüfung durchgeführt wird, finden Sie Informationen in der [OpenID Connect-Spezifikation](https://openid.net/specs/openid-connect-core-1_0.html#HybridIDToken). Dies wird für ID-Token vom `/token`-Endpunkt nicht zurückgegeben. |
|`aio` | Nicht transparente Zeichenfolge | Ein interner Anspruch, der von Azure AD verwendet wird, um Daten für die Wiederverwendung von Token aufzuzeichnen. Sollte ignoriert werden.|
|`preferred_username` | String | Der primäre Benutzername, der den Benutzer darstellt. Dabei kann es sich um eine E-Mail-Adresse, eine Telefonnummer oder einen generischen Benutzernamen ohne bestimmtes Format handeln. Der Wert kann geändert werden und sich im Laufe der Zeit ändern. Da dieser Wert geändert werden kann, darf er nicht verwendet werden, um Autorisierungsentscheidungen zu treffen. Der Bereich `profile` ist erforderlich, um diesen Anspruch zu empfangen.|
|`email` | String | Der Anspruch `email` ist standardmäßig für Gastkonten vorhanden, die über eine E-Mail-Adresse verfügen.  Ihre App kann den E-Mail-Anspruch für verwaltete Benutzer (unter demselben Mandanten wie die Ressource) über den `email` [optionalen Anspruch](active-directory-optional-claims.md) anfordern.  Auf dem v2.0-Endpunkt kann Ihre App auch den OpenID Connect-Bereich `email` anfordern. Sie müssen nicht sowohl den optionalen Anspruch als auch den Bereich abrufen, um den Anspruch zu erhalten.  Für den Anspruch „email“ werden nur adressierbare E-Mails aus den Profilinformationen des Benutzers unterstützt. |
|`name` | String | Der `name`-Anspruch gibt einen visuell lesbaren Wert an, der den Antragsteller des Tokens identifiziert. Der Wert ist nicht zwingend eindeutig, er kann aber geändert werden und dient nur zu Anzeigezwecken. Der Bereich `profile` ist erforderlich, um diesen Anspruch zu empfangen. |
|`nonce`| String | Die Nonce stimmt mit dem Parameter überein, der in der ursprünglichen /authorize-Anforderung an den IDP enthalten ist. Wenn diese Angaben nicht übereinstimmen, sollte Ihre Anwendung das Token ablehnen. |
|`oid` | Zeichenfolge, eine GUID | Der unveränderliche Bezeichner für ein Objekt im Microsoft-Identitätssystem, in diesem Fall ein Benutzerkonto. Diese ID identifiziert den Benutzer anwendungsübergreifend eindeutig: Zwei verschiedene Anwendungen, die den gleichen Benutzer anmelden, erhalten den gleichen Wert im `oid`-Anspruch. Microsoft Graph gibt diese ID als `id`-Eigenschaft für ein bestimmtes Benutzerkonto zurück. Da mit `oid` mehrere Apps Benutzer korrelieren können, ist der `profile`-Bereich erforderlich, um diesen Anspruch zu erhalten. Beachten Sie Folgendes: Wenn ein einzelner Benutzer in mehreren Mandanten vorhanden ist, enthält der Benutzer in jedem Mandanten eine andere Objekt-ID. Sie werden als unterschiedliche Konten betrachtet, obwohl sich der Benutzer bei jedem Konto mit den gleichen Anmeldeinformationen anmeldet. Der `oid`-Anspruch ist eine GUID und kann nicht wiederverwendet werden. |
|`roles`| Array von Zeichenfolgen | Die Rollen, die dem sich anmeldenden Benutzer zugewiesen wurden. |
|`rh` | Nicht transparente Zeichenfolge |Ein interner Anspruch, der von Azure zum erneuten Überprüfen von Token verwendet wird. Sollte ignoriert werden. |
|`sub` | String | Der Prinzipal, für den das Token Informationen zusichert, z. B. der Benutzer einer App. Dieser Wert ist unveränderlich und kann nicht erneut zugewiesen oder wiederverwendet werden. Der Antragsteller ist ein paarweiser Bezeichner: Er gilt nur für eine bestimmte Anwendungs-ID. Wenn sich ein Benutzer bei zwei verschiedenen Apps mit zwei verschiedenen Client-IDs anmeldet, erhalten diese Apps zwei unterschiedliche Werte für den Antragstelleranspruch. Dies kann – abhängig von den Architektur- und Datenschutzanforderungen – wünschenswert sein oder nicht. |
|`tid` | Zeichenfolge, eine GUID | Stellt den Mandanten dar, bei dem sich der Benutzer anmeldet. Bei Geschäfts-, Schul- oder Unikonten ist die GUID die unveränderliche Mandanten-ID der Organisation, bei der sich der Benutzer anmeldet. Für Anmeldungen beim persönlichen Microsoft-Kontomandanten (Dienste wie Xbox, Teams for Life oder Outlook) ist der Wert `9188040d-6c67-4c5b-b112-36a304b66dad`. Um diesen Anspruch zu erhalten, muss Ihre App den Bereich `profile` anfordern. |
|`unique_name` | String | Ein lesbarer Wert, der Aufschluss über den Antragsteller des Tokens gibt. Dieser Wert ist immer eindeutig. Da aber E-Mails und andere Bezeichner wiederverwendet werden können, kann dieser Wert in anderen Konten erneut auftreten. Daher sollte der Wert nur zu Anzeigezwecken verwendet werden. Wird nur in v1.0-`id_tokens` ausgegeben. |
|`uti` | Nicht transparente Zeichenfolge | Ein interner Anspruch, der von Azure zum erneuten Überprüfen von Token verwendet wird. Sollte ignoriert werden. |
|`ver` | Zeichenfolge, 1.0 oder 2.0 | Gibt die Version des ID-Tokens an. |
|`hasgroups`|Boolean|Ist immer auf TRUE festgelegt (sofern vorhanden) und gibt an, dass der Benutzer mindestens einer Gruppe angehört. Wird anstelle des Gruppenanspruchs für JWTs in Flows mit impliziter Gewährung verwendet, wenn der Anspruch für vollständige Gruppen das URI-Fragment über die URL-Längenbeschränkung (derzeit 6 oder mehr Gruppen) erweitert. Gibt an, dass der Client die Gruppen des Benutzers über die Microsoft Graph-API bestimmen soll (`https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects`).|
|`groups:src1`|JSON-Objekt | Für die Token-Anforderungen ohne eine Längenbeschränkung (siehe `hasgroups` oben), die aber dennoch zu groß für das Token sind, ist ein Link zur Liste der vollständigen Gruppen für den Benutzer enthalten. Für JWTs als verteilter Anspruch, für SAML als neuer Anspruch anstelle des Anspruchs `groups`. <br><br>**JWT-Beispielwert**: <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects" }`<br><br> Weitere Informationen finden Sie unter [Gruppenüberschreitungsanspruch](#groups-overage-claim).|

### <a name="using-claims-to-reliably-identify-a-user-subject-and-object-id"></a>Verwenden von Ansprüchen zur zuverlässigen Identifizierung von Benutzern (Betreff und Objekt-ID)

Wenn Sie einen Benutzer identifizieren (wenn Sie ihn z. B. in einer Datenbank suchen oder entscheiden, welche Berechtigungen er erhält), ist es wichtig, Informationen zu verwenden, die im Lauf der Zeit unverändert und eindeutig bleiben. Ältere Anwendungen verwenden manchmal Felder wie die E-Mail-Adresse, eine Telefonnummer oder den UPN.  All diese Änderungen können sich im Laufe der Zeit ändern und sie können auch im Laufe der Zeit wiederverwendet werden. Das kann z. B. vorkommen, wenn ein Mitarbeiter seinen Namen ändert oder wenn ein Mitarbeiter die E-Mail-Adresse eines früheren Mitarbeiters erhält, der nicht mehr im Unternehmen tätig ist. Daher ist es **sehr wichtig**, dass Ihre Anwendung keine für Menschen lesbaren Daten verwendet, um einen Benutzer zu identifizieren. Für Menschen lesbar bedeutet ganz allgemein, dass jemand diese Informationen lesen und ändern wollen wird. Verwenden Sie stattdessen die Ansprüche, die vom OIDC-Standard bereitgestellt werden, oder die von Microsoft bereitgestellten Erweiterungsansprüche `sub` und `oid`.

Verwenden Sie zum ordnungsgemäßen Speichern von Informationen pro Benutzer nur `sub` oder `oid` (die als GUIDs eindeutig sind) und bei Bedarf `tid` für das Routing oder Sharding.  Wenn Sie Daten dienstübergreifend freigeben müssen, eignet sich `oid`+`tid` am besten, da alle Apps dieselben Ansprüche `oid` und `tid` für einen bestimmten Benutzer erhalten, der in einem bestimmten Mandanten agiert.  Der `sub`-Anspruch in der Microsoft Identity-Plattform wird „paarweise“ festgelegt, d. h., er basiert auf einer Kombination aus dem Tokenempfänger, dem Mandanten und dem Benutzer und ist damit eindeutig.  Daher erhalten zwei Apps, die ID-Token für einen bestimmten Benutzer anfordern, unterschiedliche `sub`-Ansprüche, aber dieselben `oid`-Ansprüche für diesen Benutzer.

>[!NOTE]
> Verwenden Sie den `idp`-Anspruch nicht zum Speichern von Informationen zu einem Benutzer, um Benutzer über Mandanten hinweg zu korrelieren.  Dies funktioniert nicht, da die Ansprüche `oid` und `sub` für einen Benutzer in verschiedenen Mandanten unterschiedlich sind. Dies ist so konzipiert, um sicherzustellen, dass Anwendungen Benutzer nicht mandantenübergreifend nachverfolgen können.  
>
> Gastszenarien, in denen ein Benutzer einem Mandanten angehört und sich in einem anderen authentifiziert, sollten den Benutzer wie einen neuen Benutzer des Diensts behandeln.  Ihre Dokumente und Berechtigungen im Mandanten Contoso dürfen nicht im Mandanten Fabrikam angewandt werden. Dies ist wichtig, um versehentliche Datenlecks zwischen Mandanten und die Erzwingung von Datenlebenszyklen zu verhindern.  Beim Entfernen eines Gasts aus einem Mandanten sollte auch dessen Zugriff auf die Daten entfernt werden, die er in diesem Mandanten erstellt hat. 

### <a name="groups-overage-claim"></a>Gruppenüberschreitungsanspruch
Um sicherzustellen, dass die Tokengröße die Grenzwerte für HTTP-Header nicht überschreitet, schränkt Azure AD die Anzahl der im `groups`-Anspruch enthaltenen Objekt-IDs ein. Wenn ein Benutzer Mitglied mehrerer Gruppen als die zulässige Überschreitungsgrenze (150 für SAML-Token, 200 für JWT-Token) ist, gibt Azure AD den Gruppenanspruch nicht im Token aus. Stattdessen ist ein Überschreitungsanspruch im Token enthalten, der der Anwendung anzeigt, dass die Microsoft Graph-API abgefragt werden soll, um die Gruppenmitgliedschaft des Benutzers abzurufen.

```json
{
  ...
  "_claim_names": {
   "groups": "src1"
    },
    {
  "_claim_sources": {
    "src1": {
        "endpoint":"[Url to get this user's group membership from]"
        }
       }
     }
  ...
}
```

## <a name="id-token-lifetime"></a>ID-Tokengültigkeitsdauer

Standardmäßig ist ein ID-Token eine Stunde lang gültig. Nach einer Stunde muss der Client ein neues ID-Token beziehen.

Sie können die Gültigkeitsdauer eines ID-Tokens anpassen, um zu steuern, wie oft die Clientanwendung den Ablauf der Anwendungssitzung veranlasst und wie oft eine erneute Authentifizierung des Benutzers erforderlich ist (entweder im Hintergrund oder interaktiv). Weitere Informationen hierzu finden Sie unter [Konfigurierbare Tokengültigkeitsdauer](active-directory-configurable-token-lifetimes.md).

## <a name="validating-an-id-token"></a>Das Überprüfen eines ID-Tokens

Die Überprüfung eines ID-Tokens ähnelt dem ersten Schritt der [Überprüfung eines Zugriffstokens](access-tokens.md#validating-tokens). Ihr Client kann überprüfen, ob das Token manipuliert wurde. Außerdem kann der Aussteller überprüft werden, um sicherzustellen, dass der richtige Aussteller das Token zurückgesendet hat. Da die ID-Token immer JWT-Token sind, sind zahlreiche Bibliotheken zum Überprüfen dieser Token verfügbar. Es wird empfohlen, eine dieser Bibliotheken zu verwenden, anstatt den Vorgang selbst auszuführen.  Beachten Sie, dass nur vertrauliche Clients (mit einem Geheimnis) ID-Token überprüfen sollten.  Öffentliche Anwendungen (ein Code, der vollständig auf einem Gerät oder in einem Netzwerk ausgeführt wird, das Sie nicht steuern können, z. B. den Browser eines Benutzers oder dessen Heimnetzwerk) profitieren nicht von der Überprüfung des ID-Tokens. Das liegt daran, dass ein böswilliger Benutzer die Schlüssel abfangen und bearbeiten kann, die für die Überprüfung des Tokens verwendet werden.

Um das Token manuell zu überprüfen, beachten Sie die Details der Schritte unter [Überprüfen eines Zugriffstokens](access-tokens.md#validating-tokens). Die folgenden JWT-Ansprüche sollten im ID-Token überprüft werden, nachdem die Signatur für das Token überprüft wurde. Diese Ansprüche können auch von Ihrer Token-Überprüfungsbibliothek validiert werden:

* Zeitstempel: Die Zeitstempel `iat`, `nbf` und `exp` sollten alle vor oder nach der aktuellen Zeit liegen (je nach Bedarf).
* Zielgruppe: Der `aud`-Anspruch sollte mit der App-ID Ihrer Anwendung übereinstimmen.
* Nonce: Der `nonce`-Anspruch in der Nutzlast muss mit dem nonce-Parameter übereinstimmen, der während der ersten Anforderung an den /authorize-Endpunkt übergeben wurde.

## <a name="next-steps"></a>Nächste Schritte

* Schauen Sie sich den [OpenID Connect](v2-protocols-oidc.md)-Flow an, der die Protokolle definiert, die ein ID-Token ausgeben. 
* Informationen zu [Zugriffstoken](access-tokens.md)
* Passen Sie die JWT-Ansprüche in Ihrem ID-Token mithilfe von [optionalen Ansprüchen](active-directory-optional-claims.md) an.
