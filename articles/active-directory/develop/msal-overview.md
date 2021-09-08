---
title: Informationen zu MSAL | Azure
titleSuffix: Microsoft identity platform
description: Die Microsoft-Authentifizierungsbibliothek (Microsoft Authentication Library, MSAL) ermöglicht Anwendungsentwicklern das Abrufen von Token, um geschützte Web-APIs aufzurufen. Bei diesen Web-APIs kann es sich um Microsoft Graph, andere Microsoft APIs, Web-APIs von Drittanbietern oder Ihre eigene Web-API handeln. Die MSAL unterstützt mehrere Anwendungsarchitekturen und -plattformen.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/22/2021
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40, has-adal-ref
ms.openlocfilehash: 405843ad0d56a75dac20dc2a6c85d8bc15600312
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122429102"
---
# <a name="overview-of-the-microsoft-authentication-library-msal"></a>Übersicht über die Microsoft-Authentifizierungsbibliothek (MSAL)
Mit der Microsoft-Authentifizierungsbibliothek (MSAL) können Entwickler [Token](developer-glossary.md#security-token) von Microsoft Identity Platform abrufen, um Benutzer zu authentifizieren und auf geschützte Web-APIs zuzugreifen. Sie kann verwendet werden, um sicheren Zugriff auf Microsoft Graph, andere Microsoft-APIs, Web-APIs von Drittanbietern oder Ihre eigene Web-API zu gewährleisten. MSAL unterstützt verschiedene Anwendungsarchitekturen und -plattformen einschließlich .NET, JavaScript, Java, Python, Android und iOS.

MSAL bietet zahlreiche Möglichkeiten für den Tokenabruf und stellt eine konsistente API für unterschiedliche Plattformen bereit. MSAL bietet folgende Vorteile:

* Die OAuth-Bibliotheken oder Code müssen nicht direkt in Übereinstimmung mit dem Protokoll in Ihrer Anwendung verwendet werden.
* Token werden im Namen eines Benutzers oder einer Anwendung abgerufen (sofern auf die Plattform zutreffend).
* MSAL stellt einen Tokencache bereit und aktualisiert Token, bevor sie ablaufen. Sie müssen sich nicht um den Ablauf von Token kümmern.
* Sie können angeben, welche Benutzer sich bei Ihrer Anwendung anmelden sollen (Ihre Organisation, mehrere Organisationen, Geschäfts-, Schul- und Unikonten sowie persönliche Microsoft-Konten, Identitäten in sozialen Netzwerken mit Azure AD B2C, Benutzer in Sovereign Clouds und nationalen Clouds).
* Sie können Ihre Anwendung mithilfe von Konfigurationsdateien einrichten.
* Sie können Fehler in Ihrer App mithilfe von handlungsrelevanten Ausnahmen sowie Protokoll- und Telemetriedaten beheben.

> [!VIDEO https://www.youtube.com/embed/zufQ0QRUHUk]

## <a name="application-types-and-scenarios"></a>Anwendungstypen und -szenarien
Mit MSAL kann ein Token von verschiedenen Anwendungstypen abgerufen werden: Webanwendungen, Web-APIs, Single-Page-Apps (JavaScript), mobilen und nativen Anwendungen sowie Daemonanwendungen und serverseitigen Anwendungen.

MSAL kann in vielen Anwendungsszenarien verwendet werden, darunter:

* [Single-Page-Anwendungen (JavaScript)](scenario-spa-overview.md)
* [Web-Apps für Benutzeranmeldungen](scenario-web-app-sign-user-overview.md)
* [Webanwendungen, die einen Benutzer anmelden und eine Web-API im Namen des Benutzers aufrufen](scenario-web-app-call-api-overview.md)
* [Zum Schutz einer Web-API, sodass nur authentifizierte Benutzer darauf zugreifen können](scenario-protected-web-api-overview.md)
* [Eine Web-API, die im Namen des angemeldeten Benutzers eine andere Downstream-Web-API aufruft](scenario-web-api-call-api-overview.md)
* [Eine Desktopanwendung, die im Namen des angemeldeten Benutzers eine Web-API aufruft](scenario-desktop-overview.md)
* [Mobile Anwendung, die eine Web-API im Namen des Benutzers aufruft, der sich interaktiv angemeldet hat](scenario-mobile-overview.md).
* [Eine Desktop-/Servicedaemonanwendung, die eine Web-API im eigenen Namen aufruft](scenario-daemon-overview.md)

## <a name="languages-and-frameworks"></a>Sprachen und Frameworks

| Bibliothek | Unterstützte Plattformen und Frameworks|
| --- | --- |
| [MSAL für Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)|Android|
| [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular)| Single-Page-Apps mit den Frameworks Angular und Angular.js|
| [MSAL für iOS und macOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|iOS und macOS|
| [MSAL Go (Vorschau)](https://github.com/AzureAD/microsoft-authentication-library-for-go)|Windows, macOS, Linux|
| [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java)|Windows, macOS, Linux|
| [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)| JavaScript-/TypeScript-Frameworks wie Vue.js, Ember.js oder Durandal.js|
| [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)| .NET Framework, .NET Core, Xamarin Android, Xamarin iOS, Universelle Windows-Plattform|
| [MSAL Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node)|Web-Apps mit Express, Desktop-Apps mit Electron, plattformübergreifende Konsolen-Apps|
| [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)|Windows, macOS, Linux|
| [MSAL React](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)| Single-Page-Apps mit React-Bibliotheken und React-basierten Bibliotheken (Next.js, Gatsby.js)|

## <a name="migrate-apps-that-use-adal-to-msal"></a>Migrieren von Apps von der ADAL zu der MSAL

Die Active Directory-Authentifizierungsbibliothek (ADAL) ist in den Endpunkt von Azure AD für Entwickler (v1.0) integriert, während MSAL in Microsoft Identity Platform integriert ist. Der v1.0-Endpunkt unterstützt Geschäftskonten, aber keine persönlichen Konten. Der v2.0-Endpunkt wird für die Zusammenführung von persönlichen Konten und Geschäftskonten von Microsoft in einem gemeinsamen Authentifizierungssystem genutzt. Darüber hinaus unterstützt MSAL auch Authentifizierungen für Azure AD B2C.

Weitere Informationen zur Migration zu der MSAL finden Sie unter [Migrieren von Anwendungen zu der Microsoft Authentication Library (MSAL)](msal-migration.md).
