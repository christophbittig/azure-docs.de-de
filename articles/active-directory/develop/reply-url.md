---
title: Einschränkungen für Umleitungs-URI/Antwort-URL | Azure AD
titleSuffix: Microsoft identity platform
description: Beschrieben werden die Einschränkungen, die für das Format des Umleitungs-URIs (Antwort-URL) gelten, das von der Microsoft Identity-Plattform erzwungen wird.
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 08/06/2021
ms.topic: conceptual
ms.subservice: develop
ms.custom: contperf-fy21q4-portal, aaddev
ms.service: active-directory
ms.reviewer: marsma, lenalepa, manrath
ms.openlocfilehash: 96fe21b4f1df662e72ec88abc68d74db25257de1
ms.sourcegitcommit: c2f0d789f971e11205df9b4b4647816da6856f5b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122662037"
---
# <a name="redirect-uri-reply-url-restrictions-and-limitations"></a>Einschränkungen für Umleitungs-URI/Antwort-URL

Ein Umleitungs-URI oder eine Antwort-URL definiert den Ort, an den der Autorisierungsserver den Benutzer leitet, sobald die App erfolgreich autorisiert und ein Autorisierungscode oder Zugriffstoken erteilt wurde. Der Autorisierungsserver sendet den Code oder das Token an den Umleitungs-URI. Daher ist es wichtig, dass Sie beim App-Registrierungsvorgang den richtigen Ort registrieren.

Das Azure Active Directory (Azure AD) Anwendungsmodul gibt die folgenden Einschränkungen zum Umleiten von URIs an:

* Der Umleitungs-URI muss mit dem Schema `https` beginnen. Bei Umleitungs-URIs gibt es einige [Ausnahmen für „localhost“](#localhost-exceptions).

* Bei Umleitungs-URIs wird die Kleinschreibung beachtet, und sie müssen mit der Schreibweise des URL-Pfads Ihrer ausgeführten Anwendung übereinstimmen. Wenn der Pfad für Ihre Anwendung z. B. `.../abc/response-oidc` als Bestandteil enthält, dürfen Sie im Umleitungs-URI nicht `.../ABC/response-oidc` angeben. Weil der Webbrowser bei Pfaden die Groß-/Kleinschreibung beachtet, werden Cookies, die `.../abc/response-oidc` zugeordnet sind, möglicherweise ausgeschlossen, wenn eine Umleitung an die anders geschriebene (nicht übereinstimmende) URL `.../ABC/response-oidc` erfolgt.

* Umleitungs-URIs, die *nicht* mit einem Pfadsegment konfiguriert sind, werden in der Antwort mit einem nachgestellten Schrägstrich ('`/`') zurückgegeben. Dies gilt nur, wenn der Antwortmodus `query` oder `fragment` ist.

    Beispiele:

    * `https://contoso.com` wird als `https://contoso.com/` zurückgegeben.
    * `http://localhost:7071` wird als `http://localhost:7071/` zurückgegeben.

* Umleitungs-URIs, die ein Pfadsegment enthalten, werden in der Antwort *nicht* mit einem nachgestellten Schrägstrich zurückgegeben.

    Beispiele:

    * `https://contoso.com/abc` wird als `https://contoso.com/abc` zurückgegeben.
    * `https://contoso.com/abc/response-oidc` wird als `https://contoso.com/abc/response-oidc` zurückgegeben.

## <a name="maximum-number-of-redirect-uris"></a>Maximale Anzahl von Umleitungs-URIs

Die folgende Tabelle zeigt die maximale Anzahl von Umleitungs-URIs, die Sie einer App-Registrierung in Microsoft Identity Plattform hinzufügen können.

| Angemeldete Konten | Maximale Anzahl von Umleitungs-URIs | BESCHREIBUNG |
|--------------------------|---------------------------------|-------------|
| Geschäfts-, Schul- oder Unikonten von Microsoft in einem beliebigen Azure Active Directory (Azure AD)-Mandanten eines Unternehmens | 256 | Das Feld `signInAudience` im Anwendungsmanifest ist entweder auf *AzureADMyOrg* oder *AzureADMultipleOrgs* eingestellt |
| Persönliche Konten sowie Geschäfts-, Schul- und Unikonten von Microsoft | 100 | Das Feld `signInAudience` im Anwendungsmanifest ist auf *AzureADandPersonalMicrosoftAccount* eingestellt |

## <a name="maximum-uri-length"></a>Maximale URI-Länge

Für jeden Umleitungs-URI, den Sie einer App-Registrierung hinzufügen, können Sie maximal 256 Zeichen verwenden.

## <a name="supported-schemes"></a>Unterstützte Schemas

**HTTPS**: Das HTTPS-Schema (`https://`) wird für alle HTTP-basierten Umleitungs-URIs unterstützt.

**HTTP**: Das HTTP-Schema (`http://`) wird *nur* für *localhost*-URIs unterstützt und sollte nur während der Entwicklung und Tests der aktiven lokalen Anwendung verwendet werden.

| Beispiel für Umleitungs-URI                    | Gültigkeitsdauer |
|-----------------------------------------|----------|
| `https://contoso.com`                   | Gültig    |
| `https://contoso.com/abc/response-oidc` | Gültig    |
| `https://localhost`                     | Gültig    |
| `http://contoso.com/abc/response-oidc`  | Ungültig  |
| `http://localhost`                      | Gültig    |
| `http://localhost/abc`                  | Gültig    |

### <a name="localhost-exceptions"></a>Ausnahmen für Localhost

Gemäß [RFC 8252, Abschnitte 8.3](https://tools.ietf.org/html/rfc8252#section-8.3) und [7.3](https://tools.ietf.org/html/rfc8252#section-7.3), gelten für die Umleitungs-URIs „loopback“ und „localhost“ zwei Besonderheiten:

1. `http`-URI-Schemas sind akzeptabel, da die Umleitung das Gerät niemals verlässt. Daher sind die beiden folgenden URIs akzeptabel:
    - `http://localhost/myApp`
    - `https://localhost/myApp`
1. Aufgrund kurzlebiger Portbereiche, die häufig von nativen Anwendungen benötigt werden, wird die Portkomponente (z. B. `:5001` oder `:443`) beim Abgleich eines Umleitungs-URI ignoriert. Folglich werden alle diese URIs als gleichwertig betrachtet:
    - `http://localhost/MyApp`
    - `http://localhost:1234/MyApp`
    - `http://localhost:5000/MyApp`
    - `http://localhost:8080/MyApp`

Aus Entwicklersicht bedeutet dies Folgendes:

* Registrieren Sie nicht mehrere Umleitungs-URIs, wenn sich nur der Port unterscheidet. Der Anmeldeserver wählt willkürlich einen Umleitungs-URI aus und verwendet das diesem zugeordnete Verhalten (z. B. entsprechend dem Umleitungstyp `web`, `native` oder `spa`).

    Dies ist besonders dann wichtig, wenn Sie in ein und derselben Anwendungsregistrierung verschiedene Authentifizierungsflows verwenden möchten, beispielsweise die Autorisierungscodegenehmigung und den impliziten Flow. Um jedem Umleitungs-URI das richtige Antwortverhalten zuzuordnen, muss der Anmeldeserver zwischen den verschiedenen URIs unterscheiden können. Dies ist nur mit unterschiedlichen Ports möglich.
* Wenn Sie mehrere Umleitungs-URIs für den Localhost registrieren, um während der Entwicklung verschiedene Flows zu testen, unterscheiden Sie diese mithilfe der *path*-Komponente des URI. Beispielsweise stimmt `http://localhost/MyWebApp` nicht mit `http://localhost/MyNativeApp` überein.
* Die IPv6-Loopback Adresse (`[::1]`) wird derzeit nicht unterstützt.

#### <a name="prefer-127001-over-localhost"></a>127.0.0.1 anstelle von localhost

Um Fehler in Ihrer App aufgrund falsch konfigurierter Firewalls oder umbenannter Netzwerkschnittstellen zu vermeiden, verwenden Sie anstelle von `localhost` die tatsächliche IP-Loopbackadresse `127.0.0.1` in Ihrem Umleitungs-URI. Beispiel: `https://127.0.0.1`.

Sie können jedoch nicht das Textfeld **Umleitungs-URIs** im Azure-Portal verwenden, um einen loopbackbasierten Umleitungs-URI mit dem `http`-Schema hinzuzufügen:

:::image type="content" source="media/reply-url/portal-01-no-http-loopback-redirect-uri.png" alt-text="Fehlerdialogfeld im Azure-Portal mit unzulässigem HTTP-basiertem Loopback-Umleitungs-URI":::

Zum Hinzufügen eines Umleitungs-URI, der das `http`-Schema verwendet, mit der tatsächlichen Loopbackadresse `127.0.0.1` müssen Sie derzeit das [replyUrlsWithType-Attribut im Anwendungsmanifest](reference-app-manifest.md#replyurlswithtype-attribute) ändern.

## <a name="restrictions-on-wildcards-in-redirect-uris"></a>Einschränkungen für Platzhalter in Umleitungs-URIs

Platzhalter in URIs wie `https://*.contoso.com` sind möglicherweise bequem, sind jedoch aufgrund von Sicherheitsrisiken zu vermeiden. Gemäß der OAuth 2.0-Spezifikation ([RFC 6749, Abschnitt 3.1.2](https://tools.ietf.org/html/rfc6749#section-3.1.2)) muss ein Umleitungsendpunkt-URI ein absoluter URI sein.

URIs mit Platzhalter werden derzeit in App-Registrierungen, die für die Anmeldung von persönlichen Microsoft-Konten und von Geschäfts-, Schul- oder Unikonten konfiguriert sind, nicht unterstützt. URIs mit Platzhalter sind jedoch zulässig bei Apps, die nur für die Anmeldung von Geschäfts-, Schul- oder Unikonten bei einem Azure AD-Mandanten in einer Organisation konfiguriert sind.

Verwenden Sie im Azure-Portal unter **App-Registrierungen** den Anwendungsmanifest-Editor, um Umleitungs-URIs mit Platzhaltern zu App-Registrierungen hinzuzufügen, die Geschäfts-, Schul- oder Unikonten anmelden. Auch wenn Sie mit dem Manifest-Editor einen Umleitungs-URI mit Platzhalter festlegen können, empfehlen wir *dringend*, den Abschnitt 3.1.2 von RFC 6749 zu beachten und ausschließlich absolute URIs zu verwenden.

Wenn die Anzahl der in Ihrem Szenario erforderlichen Umleitungs-URIs den zulässigen Höchstwert überschreitet, sollten Sie anstelle eines Umleitungs-URIs mit Platzhalter den folgenden Ansatz mit Statusparameter in Betracht ziehen.

#### <a name="use-a-state-parameter"></a>Verwenden eines Statusparameters

Wenn Sie mehrere Unterdomänen haben und in Ihrem Szenario Benutzer nach erfolgreicher Authentifizierung wieder auf die Ausgangsseite umgeleitet werden müssen, kann die Verwendung eines Statusparameters hilfreich sein.

Dieser Ansatz ermöglicht Folgendes:

1. Erstellen eines „gemeinsamen“ Umleitungs-URIs pro Anwendung, um die vom Autorisierungsendpunkt empfangenen Sicherheitstoken zu verarbeiten.
1. Ihre Anwendung kann anwendungsspezifische Parameter (z. B. URL der Unterdomäne, aus welcher der Benutzer stammt, oder andere Parameter wie Brandinginformationen) im Statusparameter senden. Die Verwendung eines Statusparameters bietet Schutz vor der websiteübergreifenden Anforderungsfälschung (CSRF) gemäß [RFC 6749, Abschnitt 10.12](https://tools.ietf.org/html/rfc6749#section-10.12).
1. Die anwendungsspezifischen Parameter enthalten alle Informationen, die die Anwendung benötigt, um die richtige Umgebung für den Benutzer zu rendern (d.h., den entsprechenden Anwendungsstatus zu erstellen). Der Azure AD-Autorisierungsendpunkt entfernt HTML aus dem Statusparameter. Stellen Sie also sicher, dass Sie in diesem Parameter keinen HTML-Inhalt übergeben.
1. Wenn Azure AD eine Antwort an den „gemeinsamen“ Umleitungs-URI sendet, wird gleichzeitig der Statusparameter an die Anwendung zurückgesendet.
1. Die Anwendung kann dann anhand des Werts im Statusparameter bestimmen, an welche URL der Benutzer weitergeleitet werden soll. Stellen Sie eine Überprüfung in Bezug auf CSRF-Schutz sicher.

> [!WARNING]
> Dieser Ansatz ermöglicht einem kompromittierten Client, die im Statusparameter gesendeten zusätzlichen Parameter zu ändern, wodurch der Benutzer zu einer anderen URL umgeleitet wird. Dieser [Open Redirect](https://tools.ietf.org/html/rfc6819#section-4.2.4)-Angriff (Sicherheitsrisiko durch offene Umleitung) ist in RFC 6819 beschrieben. Daher muss der Client diese Parameter schützen, indem er den Status verschlüsselt oder auf andere Weise verifiziert, z. B. durch Überprüfen des Domänennamens im Umleitungs-URI anhand des Tokens.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über das [Anwendungsmanifest](reference-app-manifest.md) der App-Registrierung.
