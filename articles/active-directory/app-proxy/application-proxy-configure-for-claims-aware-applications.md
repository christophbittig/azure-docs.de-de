---
title: Ansprüche unterstützende Apps – Azure Active Directory-Anwendungsproxy
description: Erfahren Sie, wie Sie lokale ASP.NET-Anwendungen veröffentlichen, die AD FS-Ansprüche für sicheren Remotezugriff durch Ihre Benutzer akzeptieren.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: how-to
ms.date: 04/27/2021
ms.author: kenwith
ms.reviewer: ashishj
ms.openlocfilehash: 50f0f4ead9b44da13eb2a45a96db1caed81a5471
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129990005"
---
# <a name="working-with-claims-aware-apps-in-application-proxy"></a>Arbeiten mit Ansprüche unterstützenden Apps im Anwendungsproxy
[Ansprüche unterstützende Apps](/previous-versions/windows/desktop/legacy/bb736227(v=vs.85)) führen eine Umleitung an den Sicherheitstokendienst (Security Token Service, STS) aus. Der STS fordert vom Benutzer Anmeldeinformationen im Austausch gegen ein Token an und leitet ihn dann an die Anwendung um. Es gibt mehrere Möglichkeiten, den Anwendungsproxy zum Arbeiten mit diesen Umleitungen zu aktivieren. Verwenden Sie diesen Artikel zum Konfigurieren Ihrer Bereitstellung für Ansprüche unterstützende Anwendungen. 

## <a name="prerequisites"></a>Voraussetzungen
Stellen Sie sicher, dass der STS, zu dem die Ansprüche unterstützende App umgeleitet wird, außerhalb Ihres lokalen Netzwerks zur Verfügung steht. Sie können den STS über einen Proxy verfügbar machen, oder indem Sie externe Verbindungen zulassen. 

## <a name="publish-your-application"></a>Veröffentlichen der Anwendung

1. Veröffentlichen Sie Ihre Anwendung entsprechend den Anweisungen unter [Veröffentlichen von Anwendungen mit einem Anwendungsproxy](../app-proxy/application-proxy-add-on-premises-application.md).
2. Navigieren Sie zu der Anwendungenseite im Portal, und wählen Sie **Einmaliges Anmelden**.
3. Wenn Sie **Azure Active Directory** als **Präauthentifizierungsmethode** gewählt haben, wählen Sie **Azure AD-SSO deaktiviert** als **Interne Authentifizierungsmethode** aus. Wenn Sie **Passthrough** als **Methode für die Präauthentifizierung** wählen, müssen Sie nichts ändern.

## <a name="configure-adfs"></a>Konfigurieren von ADFS

Sie können AD FS auf zwei Arten für Ansprüche unterstützende Apps konfigurieren. Die erste ist die Verwendung von benutzerdefinierter Domänen. Die zweite erfolgt mit dem WS-Verbund. 

### <a name="option-1-custom-domains"></a>Option 1: Benutzerdefinierte Domänen

Wenn alle internen URLs für Ihre Anwendungen vollständig qualifizierte Domänennamen (Fully Qualified Domain Names, FQDNs) sind, können Sie [benutzerdefinierte Domänen](application-proxy-configure-custom-domain.md) für Ihre Anwendungen konfigurieren. Verwenden Sie die benutzerdefinierten Domänen, um externe URLs zu erstellen, die mit den internen URLs identisch sind. Wenn Ihre externen URLs mit Ihren internen URLs übereinstimmen, funktionieren die STS-Umleitungen unabhängig davon, ob Ihre Benutzer lokal oder remote darauf zugreifen. 

### <a name="option-2-ws-federation"></a>Option 2: WS-Verbund

1. Öffnen Sie die AD FS-Verwaltung.
2. Wechseln Sie zu **Vertrauensstellungen der vertrauenden Seite**, klicken Sie mit der rechten Maustaste auf die App, die Sie mit dem Anwendungsproxy veröffentlichen, und wählen Sie **Eigenschaften** aus.  

   ![Vertrauensstellungen der vertrauenden Seite: mit der rechten Maustaste auf den App-Namen klicken – Screenshot](./media/application-proxy-configure-for-claims-aware-applications/appproxyrelyingpartytrust.png)  

3. Wählen Sie auf der Registerkarte **Endpunkte** unter **Endpunkttyp** die Option **WS-Verbund** aus.
4. Geben Sie unter **Vertrauenswürdige URL** die URL ein, die Sie im Anwendungsproxy unter **Externe URL** eingegeben haben, und klicken Sie auf **OK**.  

   ![Hinzufügen eines Endpunkts – Wert "Vertrauenswürdige URL" festlegen – Screenshot](./media/application-proxy-configure-for-claims-aware-applications/appproxyendpointtrustedurl.png)  

## <a name="next-steps"></a>Nächste Schritte
* [Aktivieren von nativen Client-Apps für die Interaktion mit Proxyanwendungen](application-proxy-configure-native-client-application.md)