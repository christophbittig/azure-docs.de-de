---
title: Konfiguration der Azure Active Directory-Identität für Azure API for FHIR
description: Lernen Sie die Grundlagen von Identität, Authentifizierung und Autorisierung für Azure FHIR-Server kennen.
services: healthcare-apis
author: caitlinv39
ms.reviewer: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 08/05/2021
ms.author: cavoeg
ms.openlocfilehash: bd5bc1c447a019a5d76995f386407136d1d50332
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355156"
---
# <a name="azure-active-directory-identity-configuration-for-azure-api-for-fhir"></a>Konfiguration der Azure Active Directory-Identität für Azure API for FHIR

Wenn Sie mit Gesundheitsdaten arbeiten, ist es wichtig sicherzustellen, dass die Daten sicher sind und nicht von nicht autorisierten Benutzern oder Anwendungen darauf zugegriffen werden kann. FHIR-Server verwenden [OAuth 2.0](https://oauth.net/2/), um diese Datensicherheit zu gewährleisten. Die [Azure API for FHIR](https://azure.microsoft.com/services/azure-api-for-fhir/) wird mit [Azure Active Directory](../../active-directory/index.yml) abgesichert, das ein Beispiel eines OAuth 2.0-Identitätsanbieters ist. Dieser Artikel bietet einen Überblick über die Autorisierung von FHIR-Servern und die notwendigen Schritte, um ein Token für den Zugriff auf einen FHIR-Server zu erhalten. Diese Schritte gelten zwar für jeden FHIR-Server und identitätsanbieter, aber wir werden Azure API for FHIR als FHIR-Server und Azure Active Directory (Azure AD) als Identitätsanbieter in diesem Artikel durchlaufen.

## <a name="access-control-overview"></a>Übersicht über die Zugriffssteuerung

Damit eine Clientanwendung auf Azure API for FHIR zugreifen kann, muss sie ein Zugriffstoken vorlegen. Beim Zugriffstoken handelt es sich um eine signierte mit [Base64](https://en.wikipedia.org/wiki/Base64) codierte Sammlung von Eigenschaften (Ansprüchen), die Informationen zur Identität des Clients und zu den ihm gewährten Rollen und Berechtigungen enthalten.

Es gibt viele Möglichkeiten, ein Token abzurufen, aber für die Azure API for FHIR ist es nicht wichtig, wie das Token abgerufen wird, solange es sich um ein entsprechend signiertes Token mit den richtigen Ansprüchen handelt. 

Am Beispiel des [Autorisierungscodeflows](../../active-directory/azuread-dev/v1-protocols-oauth-code.md) werden beim Zugriff auf einen FHIR-Server die folgenden vier Schritte durchlaufen:

![FHIR-Autorisierung](media/azure-ad-hcapi/fhir-authorization.png)

1. Der Kunde sendet eine Anforderung an den `/authorize`-Endpunkt von Azure AD. Azure AD leitet den Client zu einer Anmeldeseite um, auf der sich der Benutzer mit den entsprechenden Anmeldeinformationen (z. B. Benutzername und Kennwort oder Zwei-Faktor-Authentifizierung) authentifiziert. Weitere Informationen finden Sie unter [Abrufen eines Autorisierungscodes](../../active-directory/azuread-dev/v1-protocols-oauth-code.md#request-an-authorization-code). Nach erfolgreicher Authentifizierung wird ein *Autorisierungscode* an den Client zurückgegeben. Azure AD erlaubt die Rückgabe dieses Autorisierungscodes nur an eine registrierte Antwort-URL, die bei der Registrierung der Clientanwendung konfiguriert wurde (siehe unten).
1. Die Clientanwendung tauscht den Autorisierungscode gegen ein *Zugriffstoken* am `/token`-Endpunkt von Azure AD aus. Bei Anforderung eines Tokens muss die Clientanwendung ggf. ein Clientgeheimnis (das Anwendungskennwort) angeben. Weitere Informationen finden Sie unter [Abrufen eines Zugriffstokens](../../active-directory/azuread-dev/v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).
1. Der Client stellt eine Anforderung an die Azure API for FHIR, z. B. `GET /Patient`, um alle Patienten zu durchsuchen. Beim Stellen der Anforderung schließt er das Zugriffstoken in einen HTTP-Anforderungsheader ein, z. B. `Authorization: Bearer eyJ0e...`, wobei `eyJ0e...` das mit Base64 codierte Zugriffstoken darstellt.
1. Die Azure API for FHIR prüft, ob das Token entsprechende Ansprüche (Eigenschaften im Token) enthält. Wenn alles in Ordnung ist, wird die Anforderung erfüllt und ein FHIR-Paket mit Ergebnissen an den Client zurückgegeben.

Es ist wichtig zu beachten, dass die Azure API for FHIR nicht an der Überprüfung von Benutzeranmeldeinformationen beteiligt ist und das Token nicht ausstellt. Authentifizierung und Tokenerstellung übernimmt Azure AD. Die Azure API for FHIR prüft lediglich, ob das Token einwandfrei signiert (authentisch) ist und ob es entsprechende Ansprüche aufweist.

## <a name="structure-of-an-access-token"></a>Struktur eines Zugriffstokens

Bei der Entwicklung von FHIR-Anwendungen geht es häufig um das Beheben von Zugriffsproblemen. Wenn einem Client der Zugriff auf die Azure API for FHIR verweigert wird, ist es nützlich, den Aufbau des Zugriffstokens zu kennen und zu verstehen, wie es entschlüsselt werden kann, um den Inhalt (die Ansprüche) des Tokens zu überprüfen. 

FHIR-Server erwarten üblicherweise ein [JSON-Webtoken](https://en.wikipedia.org/wiki/JSON_Web_Token) (JWT). Es besteht aus drei Teilen:

**Teil 1:** Ein Header, der wie folgt aussehen könnte:
    ```json
    {
      "alg": "HS256",
      "typ": "JWT"
    }
    ```

**Teil 2:** Die Nutzlast (die Ansprüche), z. B.:
    ```json
    {
     "oid": "123",
     "iss": "https://issuerurl",
     "iat": 1422779638,
     "roles": [
        "admin"
      ]
    }
    ```

**Teil 3:** Eine Signatur, die berechnet wird, indem der Base64-codierte Inhalt des Headers und der Nutzlast verkettet wird und ein kryptografischer Hash auf der Grundlage des im Header angegebenen Algorithmus ( ) berechnet `alg` wird. Ein Server kann öffentliche Schlüssel vom Identitätsanbieter abrufen und prüfen, ob dieses Token von einem bestimmten Identitätsanbieter ausgestellt und nicht manipuliert wurde.

Das vollständige Token besteht aus den mit Base64 codierten (eigentlich mit Base64-URL codierten) Versionen dieser drei Segmente. Die drei Segmente werden verkettet und mit einem `.` (Punkt) getrennt.

Ein Beispiel für ein Token wird wie folgt gezeigt:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOiIxMjMiLCAiaXNzIjoiaHR0cHM6Ly9pc3N1ZXJ1cmwiLCJpYXQiOjE0MjI3Nzk2MzgsInJvbGVzIjpbImFkbWluIl19.gzSraSYS8EXBxLN_oWnFSRgCzcmJmMjLiuyu5CSpyHI
```

Das Token kann mit Tools wie [https://jwt.ms](https://jwt.ms) decodiert und untersucht werden. Das Ergebnis der Decodierung des Tokens lautet wie folgt:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}.{
  "oid": "123",
  "iss": "https://issuerurl",
  "iat": 1422779638,
  "roles": [
    "admin"
  ]
}.[Signature]
```

## <a name="obtaining-an-access-token"></a>Abrufen eines Zugriffstokens

Wie bereits erwähnt, gibt es mehrere Möglichkeiten, ein Token von Azure AD abzurufen. Diese werden in der [Azure AD-Dokumentation für Entwickler](../../active-directory/develop/index.yml) ausführlich beschrieben.

Azure AD unterstützt zwei Versionen der OAuth 2.0-Endpunkte, die als `v1.0` und `v2.0` bezeichnet werden. Bei beiden Versionen handelt es sich um OAuth 2.0-Endpunkte. Die Bezeichnungen `v1.0` und `v2.0` deuten auf Unterschiede in der Umsetzung dieses Standards durch Azure AD hin. 

Wenn Sie einen FHIR-Server nutzen, können Sie entweder den Endpunkt `v1.0` oder `v2.0` verwenden. Die Wahl kann von den Authentifizierungsbibliotheken abhängen, die Sie in Ihrer Clientanwendung verwenden.

Die zugehörigen Abschnitte in der Azure AD-Dokumentation sind wie folgt:

* `v1.0`-Endpunkt:
    * [Autorisierungscodeflow](../../active-directory/azuread-dev/v1-protocols-oauth-code.md)
    * [Flow der Clientanmeldeinformationen](../../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)
* `v2.0`-Endpunkt:
    * [Autorisierungscodeflow](../../active-directory/develop/v2-oauth2-auth-code-flow.md)
    * [Flow der Clientanmeldeinformationen](../../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)

Es gibt weitere Varianten (z. B. im Namen des Flows), um ein Token zu beziehen. Weitere Informationen finden Sie in der Azure AD-Dokumentation. Bei Verwendung der Azure API for FHIR gibt es [unter Verwendung der Azure CLI](get-healthcare-apis-access-token-cli.md) auch einige Abkürzungen für den Zugriff auf ein Zugriffstoken (für Debugzwecke).

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie einige der grundlegenden Konzepte zur Absicherung des Zugriffs auf die Azure API for FHIR mit Azure AD kennengelernt. Informationen zum Bereitstellen des Azure API for FHIR-Diensts finden Sie unter .

>[!div class="nextstepaction"]
>[Bereitstellen von Azure API for FHIR](fhir-paas-portal-quickstart.md)