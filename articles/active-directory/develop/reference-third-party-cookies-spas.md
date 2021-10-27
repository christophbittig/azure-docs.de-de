---
title: Umgang mit der Schutzfunktion „Intelligent Tracking Prevention“ (ITP) in Safari | Azure
titleSuffix: Microsoft identity platform
description: Bei der Authentifizierung in einer Single-Page-Webanwendung (Single-Page App, SPA) sind keine Cookies von Drittanbietern mehr zulässig.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/06/2021
ms.author: hirsin
ms.reviewer: kkrishna
ms.custom: aaddev
ms.openlocfilehash: 13df7323c1b0e0c7a18ffa722454bf1b9ce147d8
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129993680"
---
# <a name="handle-itp-in-safari-and-other-browsers-where-third-party-cookies-are-blocked"></a>Umgang mit ITP in Safari und anderen Browsern, in denen Cookies von Drittanbietern blockiert werden

Viele Browser blockieren heutzutage Cookies von Drittanbietern, d. h. Cookies für Anfragen an Domains, die nicht mit der in der Browserleiste angezeigten Domain übereinstimmen. Dadurch wird der implizite Fluss unterbrochen, und es sind neue Authentifizierungsmuster erforderlich, um Benutzer erfolgreich anzumelden. In der Microsoft-Identitätsplattform verwenden wir den Autorisierungsfluss mit Proof Key for Code Exchange (PKCE) und Refresh-Tokens, um die Anmeldung von Benutzern aufrechtzuerhalten, wenn Cookies von Drittanbietern blockiert werden.

## <a name="what-is-intelligent-tracking-protection-itp"></a>Was ist Intelligent Tracking Prevention (ITP)?

Apple Safari verfügt über eine standardmäßig aktivierte Datenschutzfunktion namens [Intelligent Tracking Prevention](https://webkit.org/tracking-prevention-policy/) oder _ITP_. ITP blockiert Cookies von „Drittanbietern“. Das sind Cookies bei domänenübergreifenden Anforderungen.

Eine gängige Form der Benutzernachverfolgung erfolgt durch das Laden eines IFrames auf eine Drittanbieterwebsite im Hintergrund und das Verwenden von Cookies, um den Benutzer über das Internet zu korrelieren. Leider handelt es sich bei diesem Muster auch um die Standardmethode für das Implementieren des [impliziten Flusses](v2-oauth2-implicit-grant-flow.md) in Single-Page-Webanwendungen (Single-Page Apps, SPAs). Wenn ein Browser Cookies von Drittanbietern blockiert, um die Benutzernachverfolgung zu verhindern, werden auch SPAs unterbrochen.

Nicht nur Safari blockiert Cookies von Drittanbietern, um den Datenschutz zu verbessern. Brave blockiert standardmäßig Cookies von Drittanbietern, und Chromium (die Plattform hinter Google Chrome und Microsoft Edge) hat angekündigt, dass auch sie Cookies von Drittanbietern in Zukunft nicht mehr unterstützen werden.

Die in diesem Artikel beschriebene Lösung funktioniert in all diesen Browsern bzw. überall, wo Cookies von Drittanbietern blockiert werden.

## <a name="overview-of-the-solution"></a>Übersicht über die Lösung

Damit Benutzer in SPAs weiterhin authentifiziert werden können, müssen App-Entwickler den [Autorisierungscodeflow](v2-oauth2-auth-code-flow.md) verwenden. Im Autorisierungscodeflow gibt der Identitätsanbieter einen Code aus, und die SPA löst den Code für ein Zugriffstoken und ein Aktualisierungstoken ein. Wenn die App zusätzliche Token benötigt, kann sie den [Aktualisierungstokenflow](v2-oauth2-auth-code-flow.md#refresh-the-access-token) zum Abrufen neuer Token verwenden. Microsoft Authentication Library (MSAL) für JavaScript v2.0 implementiert den Autorisierungscodefluss für SPAs und ist, mit kleineren Updates, ein direkter Ersatz für MSAL.js 1.x.

In Bezug auf die Microsoft Identity Platform gelten für SPAs und native Clients ähnliche Protokollleitlinien:

- Verwendung einer [PKCE-Codeabfrage](https://tools.ietf.org/html/rfc7636)
  - PKCE ist für SPAs auf der Microsoft Identity Platform _erforderlich_. Für native und vertrauliche Clients wird PKCE _empfohlen_.
- Keine Verwendung eines geheimen Clientschlüssels

Für SPAs gelten zwei zusätzliche Einschränkungen:

- [Der Umleitungs-URI muss als Typ gekennzeichnet werden`spa`](v2-oauth2-auth-code-flow.md#redirect-uri-setup-required-for-single-page-apps), um CORS auf Anmeldeendpunkten zu aktivieren.
- Über den Autorisierungscodeflow an `spa`-Umleitungs-URIs ausgegebene Aktualisierungstoken haben statt einer Lebensdauer von 90 Tagen eine Lebensdauer von 24 Stunden.

:::image type="content" source="media/v2-oauth-auth-code-spa/active-directory-oauth-code-spa.svg" alt-text="Abbildung des OAuth 2.0-Autorisierungscodeflows zwischen einer Single-Page-Webanwendung und dem Endpunkt des Sicherheitstokendiensts" border="false":::

## <a name="performance-and-ux-implications"></a>Auswirkungen auf die Leistung und Benutzerfreundlichkeit

Einige Anwendungen, die den impliziten Fluss verwenden, versuchen, sich ohne Umleitung anzumelden, indem sie einen Anmelde-IFrame mit `prompt=none` öffnen. In den meisten Browsern reagiert diese Anforderung mit Token für den aktuell angemeldeten Benutzer (vorausgesetzt, die Zustimmung wurde bereits erteilt). Dieses Muster bedeutete, dass Anwendungen keine vollständige Seitenumleitung benötigten, um den Benutzer anzumelden, was die Leistung und die Benutzerfreundlichkeit verbesserte - der Benutzer besucht die Webseite und ist bereits angemeldet. Weil `prompt=none` in einem IFrame keine Option mehr ist, wenn Cookies von Drittanbietern blockiert werden, müssen Anwendungen die Anmeldeseite in einem Frame der obersten Ebene aufrufen, damit ein Autorisierungscode ausgegeben wird.

Es gibt zwei Möglichkeiten, die Anmeldung vorzunehmen:

- **Vollständige Seitenumleitungen**
  - Leiten Sie den Benutzer beim ersten Laden der SPA auf die Anmeldeseite um, wenn noch keine Sitzung vorhanden ist (oder wenn die Sitzung abgelaufen ist). Der Browser des Benutzers ruft die Anmeldeseite auf, zeigt die Cookies an, die die Benutzersitzung enthalten, und leitet den Benutzer dann mit dem Code und den Token in einem Fragment zurück zur Anwendung.
  - Die Umleitung führt dazu, dass die SPA zweimal geladen wird. Befolgen Sie die Best Practices für die Zwischenspeicherung von SPAs, damit die App nicht zweimal vollständig heruntergeladen wird.
  - Ziehen Sie in Erwägung, eine Vorladesequenz in der Anwendung zu verwenden, die auf das Vorhandensein einer Anmeldesitzung prüft und auf die Anmeldeseite umleitet, bevor die App vollständig entpackt wird und die JavaScript-Nutzlast ausführt.
- **Popups**
  - Wenn die Benutzerfreundlichkeit (User Experience, UX) einer vollständigen Seitenumleitung für die Anwendung nicht funktioniert, empfiehlt sich die Verwendung eines Popups für die Authentifizierung.
  - Wenn das Popup die Umleitung zur Anwendung nach der Authentifizierung beendet, speichert der Code im Umleitungshandler den Code und die Token im lokalen Speicher, damit die Anwendung sie verwenden kann. MSAL.js unterstützt Popups für die Authentifizierung, wie die meisten Bibliotheken.
  - Die Unterstützung von Popups in Browsern nimmt ab, daher stellen sie möglicherweise nicht die zuverlässigste Option dar. Möglicherweise ist eine Benutzerinteraktion mit der SPA vor dem Erstellen des Popups erforderlich, um die Browseranforderungen zu erfüllen.

> [!NOTE]
> Apple [beschreibt eine Popupmethode](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/) als temporäre Kompatibilitätskorrektur, um dem ursprünglichen Fenster Zugriff auf Cookies von Drittanbietern zu ermöglichen. Auch wenn Apple diese Übertragung von Berechtigungen in Zukunft möglicherweise abschaffen wird, wirkt sich dies nicht auf die vorliegende Anleitung aus. Hier wird das Popup als Erstnavigation zur Anmeldeseite verwendet, sodass eine Sitzung gefunden wird und ein Autorisierungscode bereitgestellt werden kann. Dies sollte auch in Zukunft weiterhin funktionieren.

### <a name="a-note-on-iframe-apps"></a>Hinweis zu IFrame-Apps

Ein gängiges Muster in Web-Apps ist die Verwendung eines IFrames zum Einbetten einer App in eine andere. Der Frame der obersten Ebene übernimmt die Authentifizierung des Benutzers, und die im IFrame gehostete Anwendung kann darauf vertrauen, dass der Benutzer angemeldet ist, wobei Token automatisch mithilfe des impliziten Flusses abgerufen werden. Das automatische Abrufen von Token funktioniert nicht mehr, wenn Cookies von Drittanbietern blockiert werden. Die in den IFrame eingebettete Anwendung muss auf die Verwendung von Popups umstellen, um auf die Sitzung des Benutzers zuzugreifen, weil sie nicht zur Anmeldeseite navigieren kann.

## <a name="security-implications-of-refresh-tokens-in-the-browser"></a>Sicherheitsaspekte von Aktualisierungstoken im Browser

Das Ausstellen von Aktualisierungstoken für den Browser wird als Sicherheitsproblem angesehen. Angriffe durch websiteübergreifendes Skripting (Cross-Site Scripting, XSS) oder kompromittierte JS-Pakete können das Aktualisierungstoken stehlen und es remote verwenden, bis es abläuft oder widerrufen wird. Um das Risiko durch gestohlene Aktualisierungstoken zu minimieren, werden für SPAs nur 24 Stunden gültige Token ausgestellt. Nach 24 Stunden muss die App durch Aufrufen der Anmeldeseite in einem Frame der obersten Ebene einen neuen Autorisierungscode abrufen.

Dieses Muster für Aktualisierungstoken mit begrenzter Lebensdauer wurde als Kompromiss zwischen Sicherheit und verminderter Benutzerfreundlichkeit ausgewählt. Ohne Aktualisierungstoken oder Cookies von Drittanbietern wird der Autorisierungscodeflow (wie im [Entwurf der aktuellen bewährten Sicherheitsverfahren für OAuth](https://tools.ietf.org/html/draft-ietf-oauth-security-topics-14) empfohlen) aufwändig, wenn neue oder zusätzliche Token erforderlich sind. Jedes Mal, wenn ein Token abläuft (bei Microsoft Identity Platform-Token normalerweise stündlich) ist eine vollständige Seitenumleitung oder ein Popup für jedes einzelne Token erforderlich.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Autorisierungscodefluss und zur Microsoft Authentication Library (MSAL) für JavaScript v2.0 finden Sie hier:

- [Autorisierungscodeflow](v2-oauth2-auth-code-flow.md)
- [ MSAL.js 2.0-Schnellstart](quickstart-v2-javascript-auth-code.md).
