---
title: Beispiele für die Kommunikation mit Ausgabediensten (Vorschau) - Azure Active Directory überprüfbare Anmeldeinformationen
description: Details zur Kommunikation zwischen Identitätsanbieter und Ausgabedienst
author: barclayn
manager: karenh444
ms.service: active-directory
ms.subservice: verifiable-credentials
ms.workload: identity
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 6420825d8ed758658db0e652dc2b88fea91ce908
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129994782"
---
# <a name="issuer-service-communication-examples-preview"></a>Beispiele für die Kommunikation des Ausstellerdiensts (Vorschau)

Der Dienst zum Ausstellen von überprüfbaren Berechtigungsnachweisen von Azure AD kann überprüfbare Berechtigungsnachweise ausstellen, indem er Ansprüche aus einem ID-Token abruft, das vom OpenID-konformen Identitätsanbieter Ihrer Organisation generiert wurde. In diesem Artikel wird beschrieben, wie Sie Ihren Identitätsanbieter einrichten, damit der Authentifikator damit kommunizieren und das korrekte ID-Token abrufen kann, das an den ausstellenden Dienst übergeben werden soll. 

> [!IMPORTANT]
> Überprüfbare Anmeldeinformationen für Azure Active Directory-befinden sich derzeit in der öffentlichen Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Zum Ausstellen von überprüfbaren Anmelde Informationen wird der Authentifikator durch Herunterladen des Vertrags angewiesen, um Eingaben vom Benutzer zu erfassen und diese Informationen an den ausstellenden Dienst zu senden. Wenn Sie ein ID-Token verwenden müssen, müssen Sie Ihren Identitätsanbieter so einrichten, dass der Authenticator einen Benutzer über das OpenID Connect-Protokoll anmelden kann. Die Angaben im resultierenden ID-Token werden verwendet, um den Inhalt Ihres verifizierbaren Berechtigungsnachweises aufzufüllen. Der Authentifikator authentifiziert den Benutzer mithilfe des OpenID Connect-Autorisierungs Kode-Flusses. Ihr OpenID-Anbieter muss die folgenden OpenID Connect-Features unterstützen: 

| Funktion | BESCHREIBUNG |
| ------- | ----------- |
| Gewährungstyp | Muss den Grant-Typ-Berechtigungscode unterstützen. |
| Tokenformat | Es muss unverschlüsselte kompakte JWTs erzeugen. |
| Signaturalgorithmus | Muss JWTs erzeugen, die mit RS 256 signiert sind. |
| Konfigurations-Dokument | Muss das OpenID Connect-Konfigurations Dokument unterstützen `jwks_uri` . | 
| Clientregistrierung | Muss die öffentliche Client-Registrierung unterstützen, unter Verwendung eines `redirect_uri`Werts von `vcclient://openid/`. | 
| PKCE | Empfohlen aus Sicherheitsgründen, jedoch nicht erforderlich. |

Beispiele für die HTTP-Anfragen, die an Ihren Identitätsanbieter gesendet werden, sind unten aufgeführt. Ihr Identitätsanbieter muss diese Anfragen in Übereinstimmung mit dem OpenID Connect-Authentifizierungsstandard akzeptieren und beantworten.

## <a name="client-registration"></a>Clientregistrierung

Um verifizierbare Anmeldedaten zu erhalten, müssen sich Ihre Benutzer über die Microsoft Authenticator-App bei Ihrem IDP anmelden. 

Um diesen Austausch zu aktivieren, registrieren Sie eine Anwendung bei Ihrem Identitätsanbieter. Falls Sie Azure AD verwenden, finden Sie die Anleitung [hier](../develop/quickstart-register-app.md). Verwenden Sie bei der Registrierung die folgenden Werte.

| Einstellung | Wert |
| ------- | ----- |
| Anwendungsname | `<Issuer Name> Verifiable Credential Service` |
| Umleitungs-URI | `vcclient://openid/ ` |


Nachdem Sie eine Anwendung bei Ihrem Identitätsanbieter registriert haben, zeichnen Sie deren Client-ID auf. Sie werden es im folgenden Abschnitt verwenden. Sie müssen auch die URL zum bekannten Endpunkt für den OIDC-kompatiblen Identitätsanbieter notieren. Der ausstellende Dienst verwendet diesen Endpunkt, um die öffentlichen Schlüssel herunterzuladen, die zur Validierung des ID-Tokens benötigt werden, sobald dieses von Authenticator gesendet wurde.

Die konfigurierte Redirect-URI wird von Authenticator verwendet, damit er weiß, wann die Anmeldung abgeschlossen ist und er das ID-Token abrufen kann. 

## <a name="authorization-request"></a>Authorization request (Autorisierungsanforderung)

Die an Ihren Identitätsanbieter gesendete Autorisierungsanfrage verwendet das folgende Format.

```HTTP
GET /authorize?client_id=<client-id>&redirect_uri=portableidentity%3A%2F%2Fverify&response_mode=query&response_type=code&scope=openid&state=12345&nonce=12345 HTTP/1.1
Host: www.contoso.com
Connection: Keep-Alive
```

| Parameter | Wert |
| ------- | ----------- |
| `client_id` | Die Client-ID, die Sie bei der Registrierung der Anwendung erhalten haben. |
| `redirect_uri` | Muss `vcclient://openid/` verwenden. |
| `response_mode` | Muss `query` unterstützen. |
| `response_type` | Muss `code` unterstützen. |
| `scope` | Muss `openid` unterstützen. |
| `state` | Muss gemäß dem OpenID Connect-Standard an den Client zurückgegeben werden. |
| `nonce` | Muss nach dem OpenID-Connect-Standard als Claim im ID-Token zurückgegeben werden. |

Wenn er eine Autorisierungsanfrage erhält, sollte Ihr Identitätsanbieter den Benutzer authentifizieren und alle notwendigen Schritte unternehmen, um die Anmeldung abzuschließen, wie z. B. die Multi-Faktor-Authentifizierung.

Sie können den Anmeldevorgang an Ihre Bedürfnisse anpassen. Sie können Benutzer dazu auffordern, zusätzliche Informationen anzugeben, die Nutzungsbedingungen zu akzeptieren, Ihre Anmelde-Informationen zu bezahlen, usw. Nachdem alle Schritte ausgeführt wurden, reagieren Sie auf die Autorisierungs Anforderung, indem Sie wie unten dargestellt an den Umleitungs-URI umleiten. 

```HTTP
vcclient://openid/?code=nbafhjbh1ub1yhbj1h4jr1&state=12345
```

| Parameter | Wert |
| ------- | ----------- |
| `code` |  Der Autorisierungscode, der von Ihrem Identitätsanbieter zurückgegeben wird. |
| `state` | Muss gemäß dem OpenID Connect-Standard an den Client zurückgegeben werden. |

## <a name="token-request"></a>Tokenanforderung

Die an Ihren Identitätsanbieter gesendete Tokenanforderung weist das folgende Format auf.

```HTTP
POST /token HTTP/1.1
Host: www.contoso.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 291

client_id=<client-id>&redirect_uri=vcclient%3A%2F%2Fopenid%2F&grant_type=authorization_code&code=nbafhjbh1ub1yhbj1h4jr1&scope=openid
```

| Parameter | Wert |
| ------- | ----------- |
| `client_id` | Die Client-ID, die Sie bei der Registrierung der Anwendung erhalten haben. |
| `redirect_uri` | Muss `vcclient://openid/` verwenden. |
| `scope` | Muss `openid` unterstützen. |
| `grant_type` | Muss `authorization_code` unterstützen. |
| `code` | Der Autorisierungscode, der von Ihrem Identitätsanbieter zurückgegeben wird. |

Nach Erhalt der Token-Anforderung sollte Ihr Identitätsanbieter mit einem ID-Token antworten.

```HTTP
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-store
Pragma: no-cache

{
"id_token": "eyJhbGciOiJSUzI1NiIsImtpZCI6IjFlOWdkazcifQ.ewogImlzc
    yI6ICJodHRwOi8vc2VydmVyLmV4YW1wbGUuY29tIiwKICJzdWIiOiAiMjQ4Mjg5
    NzYxMDAxIiwKICJhdWQiOiAiczZCaGRSa3F0MyIsCiAibm9uY2UiOiAibi0wUzZ
    fV3pBMk1qIiwKICJleHAiOiAxMzExMjgxOTcwLAogImlhdCI6IDEzMTEyODA5Nz
    AKfQ.ggW8hZ1EuVLuxNuuIJKX_V8a_OMXzR0EHR9R6jgdqrOOF4daGU96Sr_P6q
    Jp6IcmD3HP99Obi1PRs-cwh3LO-p146waJ8IhehcwL7F09JdijmBqkvPeB2T9CJ
    NqeGpe-gccMg4vfKjkM8FcGvnzZUN4_KSP0aAp1tOJ1zZwgjxqGByKHiOtX7Tpd
    QyHE5lcMiKPXfEIQILVq0pc_E2DzL7emopWoaoZTF_m0_N0YzFC6g6EJbOEoRoS
    K5hoDalrcvRYLSrQAZZKflyuVCyixEoV9GfNQC3_osjzw2PAithfubEEBLuVVk4
    XUVrWOLrLl0nx7RkKU8NXNHq-rvKMzqg"
}
```

Das ID-Token muss das kompakte Serialisierungsformat JWT verwenden und darf nicht verschlüsselt sein. Das ID-Token sollte die folgenden Ansprüche enthalten.

| Anspruch | Wert |
| ------- | ----------- |
| `kid` | Der Schlüsselbezeichner des Schlüssels, der zum Signieren des ID-Tokens verwendet wird, entsprechend einem Eintrag in der OpenID-Anbieterliste`jwks_uri`. |
| `aud` | Die Client-ID, die Sie bei der Registrierung der Anwendung erhalten haben. |
| `iss` | Muss der `issuer` Wert im OpenID Connect-Konfigurations-Dokument sein. |
| `exp` | Muss die Ablaufzeit des ID-Tokens enthalten. |
| `iat` | Speichert die Uhrzeit, zu der das Token ausgestellt wurde. |
| `nonce` | Der Wert, der in der Autorisierungsanforderung enthalten ist. |
| Zusätzliche Ansprüche | Das ID-Token muss alle zusätzlichen Ansprüche enthalten, deren Werte in die überprüfbaren Anmeldeinformationen eingeschlossen werden, die ausgestellt werden. In diesem Abschnitt sollten Sie alle Attribute des Benutzers einschließen, z. b. den Namen. |

## <a name="next-steps"></a>Nächste Schritte

- [Vorgehensweise beim Anpassen Ihrer überprüfbaren Anmeldeinformationen für Azure Active Directory](credential-design.md)
