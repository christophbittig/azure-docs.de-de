---
title: Öffentliche und vertrauliche Client-Apps (MSAL) | Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie mehr über öffentliche und vertrauliche Clientanwendungen in der Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/26/2021
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, has-adal-ref
ms.openlocfilehash: 03c14170a1093913fe69af2407f587835a2bb76a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131440923"
---
# <a name="public-client-and-confidential-client-applications"></a>Öffentliche und vertrauliche Clientanwendungen

Die Microsoft Authentication Library (MSAL) definiert zwei Arten von Clients: öffentliche und vertrauliche Clients. Diese beiden Clienttypen unterscheiden sich in ihrer Fähigkeit, sich auf sichere Weise bei einem Autorisierungsserver zu authentifizieren und die Vertraulichkeit ihrer Clientanmeldeinformationen zu gewährleisten. Die Azure Active Directory-Authentifizierungsbibliothek (ADAL) dagegen funktioniert nach dem Konzept des _Authentifizierungskontexts_ (hierbei handelt es sich um eine Verbindung mit Azure Active Directory).

- **Vertrauliche Clientanwendungen** sind Apps, die auf Servern ausgeführt werden (Web-Apps, Web-API-Apps oder sogar Dienst-/Daemon-Apps). Sie gelten als schwer zugänglich und können daher ein Anwendungsgeheimnis speichern. Vertrauliche Clients können Geheimnisse speichern, die zur Konfigurationszeit eingerichtet wurden. Jede Instanz eines Clients verfügt über eine eigene Konfiguration (einschließlich Client-ID und geheimem Clientschlüssel). Endbenutzer können diese Werte nur schwer extrahieren. Eine Web-App ist die häufigste Form eines vertraulichen Clients. Die Client-ID wird über den Webbrowser verfügbar gemacht, aber der geheime Schlüssel wird nur im Rückkanal übergeben und niemals direkt verfügbar gemacht.

  Vertrauliche Client-Apps: 

  ![Web-App](media/msal-client-applications/web-app.png) ![Web-API](media/msal-client-applications/web-api.png) ![Daemon/Dienst](media/msal-client-applications/daemon-service.png)

- **Öffentliche Clientanwendungen** sind Apps, die auf Geräten, Desktopcomputern oder in einem Webbrowser ausgeführt werden. Sie sind in Bezug auf eine sichere Speicherung von Anwendungsgeheimnissen nicht vertrauenswürdig und können daher nur im Namen eines Benutzers auf Web-APIs zugreifen. (Sie unterstützen nur öffentliche Clientflows.) Öffentliche Clients können keine zur Konfigurationszeit festgelegten Geheimnisse speichern und verfügen daher nicht über geheime Clientschlüssel.

  Öffentliche Client-Apps: 

  ![Desktop-App](media/msal-client-applications/desktop-app.png) ![API ohne Browser](media/msal-client-applications/browserless-app.png) ![Mobile App](media/msal-client-applications/mobile-app.png)

In MSAL.js gibt es keine Trennung zwischen öffentlichen und vertraulichen Client-Apps. MSAL.js stellt Client-Apps als Benutzer-Agent-basierte Apps dar: öffentliche Clients, bei denen der Clientcode in einem Benutzer-Agent wie beispielsweise einem Webbrowser ausgeführt wird. Diese Clients speichern keine Geheimnisse, da der Browserkontext offen zugänglich ist.

## <a name="comparing-the-client-types"></a>Vergleich der Clienttypen

Im Folgenden finden Sie einige Gemeinsamkeiten und Unterschiede bei öffentlichen und vertraulichen Client-Apps:

- Beide Arten von Apps verwalten einen Benutzertokencache und können ein Token im Hintergrund abrufen (wenn sich das Token bereits im Tokencache befindet). Vertrauliche Client-Apps verfügen auch über einen App-Tokencache für Token, die für die App selbst gelten.
- Beide Arten von Apps verwalten Benutzerkonten und können ein Konto aus dem Benutzertokencache abrufen, ein Konto anhand seines Bezeichners abrufen oder ein Konto entfernen.
- Öffentliche Client-Apps verfügen über vier Möglichkeiten, ein Token abzurufen (vier Authentifizierungsflows). Vertrauliche Client-Apps verfügen über drei Möglichkeiten, ein Token abzurufen (sowie eine Möglichkeit, die URL des Autorisierungsendpunkts des Identitätsanbieters zu berechnen). Weitere Informationen finden Sie unter [Abrufen von Token](msal-acquire-cache-tokens.md).

In MSAL wird die Client-ID (auch als _Anwendungs-ID_ oder _App-ID_ bezeichnet) einmal bei der Konstruktion der Anwendung übergeben. Sie muss nicht erneut übergeben werden, wenn die App ein Token abruft. Dies gilt sowohl für öffentliche als auch für vertrauliche Client-Apps. Geheime Clientschlüssel werden auch an Konstruktoren von öffentlichen Client-Apps übergeben: Dies sind die Geheimnisse, die für den Identitätsanbieter freigegeben werden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Anwendungskonfiguration und Instanziierung finden Sie unter:

- [Optionen für die Clientanwendungskonfiguration](msal-client-application-configuration.md)
- [Initialisieren von Clientanwendungen mithilfe von MSAL.NET](msal-net-initializing-client-applications.md)
- [Initialisieren von Clientanwendungen mithilfe von MSAL.js](msal-js-initializing-client-applications.md)
