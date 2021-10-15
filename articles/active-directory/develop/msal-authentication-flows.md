---
title: MSAL-Authentifizierungsflows | Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie mehr über die von der Microsoft-Authentifizierungsbibliothek (MSAL) verwendeten Authentifizierungsflüsse/-zuweisungen.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda
ms.openlocfilehash: 67ac453bb70c6889e1a69e7c0c40a88f623813ff
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129235115"
---
# <a name="authentication-flows"></a>Authentifizierungsflows

Die Microsoft-Authentifizierungsbibliothek (Microsoft Authentication Library, MSAL) unterstützt verschiedene Authentifizierungsflows, die in unterschiedlichen Anwendungsszenarien genutzt werden können.

| Flow | BESCHREIBUNG | Verwendung in |
|--|--|--|
| [Autorisierungscode](#authorization-code) | Wird in Apps verwendet, die auf einem Gerät installiert sind, um auf geschützte Ressourcen wie Web-APIs zuzugreifen. Dadurch können Sie mobile und Desktop-Apps mit Anmeldefunktionen und API-Zugriff ausstatten. | [Desktop-Apps](scenario-desktop-overview.md), [mobile Apps](scenario-mobile-overview.md), [Web-Apps](scenario-web-app-call-api-overview.md) |
| [Clientanmeldeinformationen](#client-credentials) | Ermöglicht es, über die Identität einer Anwendung auf Ressourcen zugreifen, die im Web gehostet werden. Wird häufig für Interaktionen zwischen Servern verwendet, die ohne direkten Benutzereingriff im Hintergrund ausgeführt werden müssen. | [Daemon-Apps](scenario-daemon-overview.md) |
| [Gerätecode](#device-code) | Ermöglicht es Benutzern, sich bei Geräten mit Eingabeeinschränkung wie einem Smart-TV, IoT-Gerät oder Drucker anzumelden. | [Desktop-/mobile Apps](scenario-desktop-acquire-token-device-code-flow.md) |
| [Implizite Gewährung](#implicit-grant) | Ermöglicht es der App, Token abzurufen, ohne dass die Anmeldeinformationen für den Back-End-Server ausgetauscht werden müssen. Dadurch kann die App den Benutzer anmelden, die Sitzung aufrechterhalten und Token für andere Web-APIs abrufen. All dies geschieht innerhalb des Client-JavaScript-Codes. | [Single-Page-Webanwendungen (SPA)](scenario-spa-overview.md) |
| [OBO (On-Behalf-Of)](#on-behalf-of) | Eine Anwendung ruft eine Dienst- oder Web-API auf, die wiederum eine andere Dienst- oder Web-API aufrufen muss. Die Idee dabei ist, die delegierte Benutzeridentität und Berechtigungen über die Anforderungskette weiterzugeben. | [Web-APIs](scenario-web-api-call-api-overview.md) |
| [Benutzername/Kennwort](#usernamepassword) | Ermöglicht es einer Anwendung, den Benutzer durch die direkte Verarbeitung seines Kennworts anzumelden. Von diesem Fluss wird abgeraten. | [Desktop-/mobile Apps](scenario-desktop-acquire-token-username-password.md) |
| [Integrierte Windows-Authentifizierung](#integrated-windows-authentication) | Ermöglicht es Anwendungen auf Computern, die in eine Domäne oder in Azure Active Directory (Azure AD) eingebunden sind, ohne Eingriff des Benutzers über die Benutzeroberfläche automatisch ein Token abzurufen. | [Desktop-/mobile Apps](scenario-desktop-acquire-token-integrated-windows-authentication.md) |

## <a name="how-each-flow-emits-tokens-and-codes"></a>Ausgabe von Token und Codes für die einzelnen Abläufe

Je nach Art Ihrer Clientanwendung können einer oder auch mehrere Authentifizierungsflows verwendet werden, die von der Microsoft Identity Platform unterstützt werden. Mit diesen Flows können verschiedene Tokentypen und Autorisierungscodes erstellt werden, und es sind unterschiedliche Token erforderlich.

| Flow                                                                               | Erforderlich            | id_token | Zugriffstoken | Aktualisierungstoken | Autorisierungscode |
|------------------------------------------------------------------------------------|:-------------------:|:--------:|:------------:|:-------------:|:------------------:|
| [Autorisierungscodeflow](v2-oauth2-auth-code-flow.md)                             |                     | x        | x            | x             | x                  |
| [Clientanmeldeinformationen](v2-oauth2-client-creds-grant-flow.md)                         |                     |          | x (nur App) |               |                    |
| [Gerätecodeflow](v2-oauth2-device-code.md)                                       |                     | x        | x            | x             |                    |
| [Impliziter Flow](v2-oauth2-implicit-grant-flow.md)                                  |                     | x        | x            |               |                    |
| [„Im Auftrag von“-Ablauf](v2-oauth2-on-behalf-of-flow.md)                                | Zugriffstoken        | x        | x            | x             |                    |
| [Benutzername/Kennwort](v2-oauth-ropc.md) (ROPC)                                       | Benutzername und Kennwort | x        | x            | x             |                    |
| [Hybrid-OIDC-Ablauf](v2-protocols-oidc.md#protocol-diagram-access-token-acquisition) |                     | x        |              |               | x                  |
| [Einlösung des Aktualisierungstokens](v2-oauth2-auth-code-flow.md#refresh-the-access-token)   | Aktualisierungstoken       | x        | x            | x             |                    |

### <a name="interactive-and-non-interactive-authentication"></a>Interaktive und nicht interaktive Authentifizierung

Verschiedene dieser Flows unterstützen sowohl interaktive als auch nicht interaktive Tokenkäufe.

  - **Interaktiv** bedeutet, dass der Benutzer zur Eingabe aufgefordert werden kann. Der Benutzer kann beispielsweise aufgefordert werden, sich anzumelden, die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) auszuführen oder Ressourcen eine zusätzliche Einwilligung zu erteilen.
  - Bei der **nicht interaktiven** oder *automatischen* Authentifizierung wird versucht, ein Token auf eine Weise abzurufen, bei der der Anmeldeserver den Benutzer *nicht* auffordern kann, zusätzliche Informationen einzugeben.

Ihre MSAL-basierte Anwendung sollte zunächst versuchen, ein Token *automatisch* abzurufen. Der interaktive Abruf ist anschließend nur möglich, wenn die nicht interaktive Methode fehlschlägt. Weitere Informationen finden Sie unter [Abrufen und Zwischenspeichern von Token mithilfe der Microsoft-Authentifizierungsbibliothek (Microsoft Authentication Library, MSAL)](msal-acquire-cache-tokens.md).

## <a name="authorization-code"></a>Authorization code (Autorisierungscode)

Das [Gewähren eines OAuth 2-Autorisierungcodes](v2-oauth2-auth-code-flow.md) ist für Apps möglich, die auf einem Gerät installiert sind, um Zugriff auf geschützte Ressourcen wie Web-APIs zu erhalten. Dadurch können Sie mobile und Desktop-Apps mit Anmeldefunktionen und API-Zugriff ausstatten.

Wenn sich Benutzer bei Webanwendungen (Websites) anmelden, erhält die Webanwendung einen Autorisierungscode. Der Autorisierungscode wird eingelöst, um ein Token zum Aufrufen von Web-APIs abzurufen.

![Diagramm: Autorisierungscodefluss](media/msal-authentication-flows/authorization-code.png)

Im obigen Diagramm führt die Anwendung folgende Vorgänge aus:

1. Fordert einen Autorisierungscode an, der gegen ein Zugriffstoken eingelöst wird.
2. Verwendet das Zugriffstoken zum Aufrufen einer Web-API.

### <a name="considerations"></a>Überlegungen

- Sie können den Autorisierungscode nur einmal zum Einlösen eines Tokens verwenden. Versuchen Sie nicht, ein Token mehrmals mit demselben Autorisierungscode abzurufen, da dies laut Protokollstandardspezifikation explizit untersagt ist. Wenn Sie den Code absichtlich mehrmals einlösen oder sich nicht bewusst sind, dass der Code von einem Framework ebenfalls für Sie eingelöst wird, erhalten Sie die folgende Fehlermeldung:

    `AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

## <a name="client-credentials"></a>Client credentials (Clientanmeldeinformationen)

Mit dem [OAuth 2-Clientanmeldeinformations-Flow](v2-oauth2-client-creds-grant-flow.md) können Sie über die Identität einer Anwendung auf Ressourcen zugreifen, die im Web gehostet werden. Diese Art der Gewährung wird häufig für Interaktionen zwischen Servern verwendet, die ohne Benutzereingriff im Hintergrund ausgeführt werden müssen. Diese Anwendungstypen werden oft als Daemons oder Dienstkonten bezeichnet.

Beim Fluss zur Zuweisung von Clientanmeldeinformationen kann ein Webdienst (ein vertraulicher Client) seine eigenen Anmeldeinformationen zum Authentifizieren verwenden, wenn ein anderer Webdienst aufgerufen wird, anstatt die Identität eines Benutzers anzunehmen. In diesem Szenario ist der Client normalerweise ein Webdienst der mittleren Ebene, ein Daemondienst oder eine Website. Für ein höheres Maß an Sicherheit bietet die Microsoft Identity Platform auch die Möglichkeit, dass der aufrufende Dienst ein Zertifikat (statt eines gemeinsamen Geheimnisses) als Anmeldeinformationen verwendet.

> [!NOTE]
> Der Flow für vertrauliche Clients ist auf mobilen Plattformen wie UWP, Xamarin.iOS und Xamarin.Android nicht verfügbar, da diese nur öffentliche Clientanwendungen unterstützen. Öffentliche Clientanwendungen sind nicht in der Lage, die Identität der Anwendung gegenüber dem Identitätsanbieter nachzuweisen. Um eine sichere Verbindung für Web-App- oder Web-API-Back-Ends zu erzielen, können Sie ein Zertifikat bereitstellen.

### <a name="application-secrets"></a>Anwendungsgeheimnisse

![Diagramm: vertraulicher Client mit Kennwort](media/msal-authentication-flows/confidential-client-password.png)

Im obigen Diagramm führt die Anwendung folgende Vorgänge aus:

1. Sie ruft ein Token mithilfe der Anmeldeinformationen ab, die aus einem Anwendungsgeheimnis oder Kennwort bestehen.
2. Verwendet das Token für Anforderungen an die Ressource.

### <a name="certificates"></a>Zertifikate

![Diagramm: vertraulicher Client mit Zertifikat](media/msal-authentication-flows/confidential-client-certificate.png)

Im obigen Diagramm führt die Anwendung folgende Vorgänge aus:

1. Sie ruft ein Token mithilfe der Zertifikatanmeldeinformationen ab.
2. Verwendet das Token für Anforderungen an die Ressource.

Die Clientanmeldeinformationen müssen folgende Voraussetzungen erfüllen:

- Sie wurden in Azure AD registriert.
- Sie wurden bei der Erstellung des vertraulichen Clientanwendungobjekts im Code übergeben.

## <a name="device-code"></a>Gerätecode

Der [OAuth 2-Gerätecodeflow](v2-oauth2-device-code.md) ermöglicht es Benutzern, sich bei Geräten mit Eingabeeinschränkung wie einem Smart-TV, einem IoT-Gerät oder einem Drucker anzumelden. Für die interaktive Authentifizierung mit Azure AD wird ein Webbrowser benötigt. Wenn das Gerät oder Betriebssystem keinen Webbrowser bereitstellt, ermöglicht es der Gerätecodeflow dem Benutzer, ein anderes Gerät (z. B. einen anderen Computer oder ein anderes Mobiltelefon) zu verwenden, um sich interaktiv anzumelden.

Mithilfe des Gerätecodeflows ruft die Anwendung Token in einem zweistufigen Prozess ab, der für diese Geräte und Betriebssysteme entwickelt wurde. Beispiele sind Anwendungen, die auf IoT-Geräten oder Befehlszeilentools (CLI-Tools) ausgeführt werden.

![Diagramm: Gerätecodefluss](media/msal-authentication-flows/device-code.png)

Im obigen Diagramm ist Folgendes zu sehen:

1. Sobald eine Benutzerauthentifizierung erforderlich ist, stellt die App einen Code bereit und fordert den Benutzer auf, mit einem anderen Gerät wie z. B. einem Smartphone mit Internetverbindung eine URL (z. B. `https://microsoft.com/devicelogin`) aufzurufen. Der Benutzer wird dann zur Eingabe des Codes aufgefordert und durch einen normalen Authentifizierungsprozess geführt, der ggf. auch Zustimmungsaufforderungen und die [mehrstufige Authentifizierung](../authentication/concept-mfa-howitworks.md) umfasst.
1. Nach erfolgreicher Authentifizierung empfängt die Befehlszeilen-App die erforderlichen Token über einen Backchannel und führt damit die benötigten Web-API-Aufrufe aus.

### <a name="constraints"></a>Einschränkungen

- Der Gerätecodeflow ist nur in öffentlichen Clientanwendungen verfügbar.
- Die beim Erstellen der öffentlichen Clientanwendung übergebene Autorität muss eine der folgenden Voraussetzungen erfüllen:
  - Sie muss auf Mandanten im Format `https://login.microsoftonline.com/{tenant}/,` beruhen, wobei `{tenant}` entweder die GUID ist, die die Mandanten-ID darstellt, oder ein Domänenname, der dem Mandanten zugeordnet ist.
  - Für Geschäfts- und Schulkonten muss sie im Format `https://login.microsoftonline.com/organizations/` vorliegen.

## <a name="implicit-grant"></a>Implicit grant (Implizite Gewährung)

Die [implizite OAuth 2-Genehmigung](v2-oauth2-implicit-grant-flow.md) ermöglicht es der App, Token von der Microsoft Identity Platform abzurufen, ohne dass die Anmeldeinformationen für den Back-End-Server ausgetauscht werden müssen. Mit diesem Flow kann die App den Benutzer anmelden, die Sitzung aufrechterhalten und Token für andere Web-APIs abrufen. All dies geschieht innerhalb des Client-JavaScript-Codes.

![Diagramm: Fluss für implizite Genehmigung](media/msal-authentication-flows/implicit-grant.svg)

Viele moderne Webanwendungen werden als clientseitige Single-Page-Anwendungen (SPA) erstellt, die in JavaScript- oder SPA-Frameworks wie Angular, Vue.js und React.js geschrieben werden. Diese Anwendungen werden in einem Webbrowser ausgeführt und haben andere Authentifizierungsmerkmale als herkömmliche serverseitige Webanwendungen. Microsoft Identity Platform ermöglicht Single-Page-Webanwendungen mithilfe des Flusses für implizite Genehmigungen das Anmelden von Benutzern und Abrufen von Token für den Zugriff auf Back-End-Dienste oder -Web-APIs. Durch den impliziten Fluss kann die Anwendung ID-Token abrufen, um den authentifizierten Benutzer darzustellen, sowie Zugriffstoken, die zum Aufrufen geschützter APIs erforderlich sind.

Dieser Authentifizierungsflow umfasst keine Anwendungsszenarien, in denen plattformübergreifende JavaScript-Frameworks wie Electron oder React-Native verwendet werden, da sie weitere Funktionen für die Interaktion mit den nativen Plattformen erfordern.

Token, die über den impliziten Flowmodus ausgegeben werden, haben eine **Längenbeschränkung**, da sie über die URL an den Browser zurückgegeben werden (wobei `response_mode` entweder `query` oder `fragment`ist). Einige Browser beschränken die Länge der URL in der Browserleiste und geben einen Fehler aus, wenn sie zu lang ist. Daher enthalten diese impliziten Flowtoken keine `groups`- oder `wids`-Ansprüche.

## <a name="on-behalf-of"></a>OBO (On-Behalf-Of)

Der [OAuth 2-On-Behalf-Of-Authentifizierungsflow](v2-oauth2-on-behalf-of-flow.md) wird verwendet, wenn eine Anwendung eine Dienst- oder Web-API aufruft, die wiederum eine andere Dienst- oder Web-API aufrufen muss. Die Idee dabei ist, die delegierte Benutzeridentität und Berechtigungen über die Anforderungskette weiterzugeben. Damit der Dienst auf der mittleren Ebene Authentifizierungsanforderungen an den Downstreamdienst stellen kann, muss *im Namen des Benutzers* ein Zugriffstoken der Microsoft Identity Platform gesichert werden.

![Diagramm: OBO-Fluss](media/msal-authentication-flows/on-behalf-of.png)

Im obigen Diagramm ist Folgendes zu sehen:

1. Die Anwendung ruft ein Zugriffstoken für die Web-API ab.
2. Ein Client (Webanwendung, Desktopanwendung, mobile Anwendung oder Single-Page-Webanwendung) ruft eine geschützte Web-API auf und fügt dabei das Zugriffstoken als Bearertoken in den Authentifizierungsheader der HTTP-Anforderung ein. Die Web-API authentifiziert den Benutzer.
3. Wenn der Client die Web-API aufruft, fordert diese ein weiteres Token im Namen des (On-Behalf-Of) Benutzers an.
4. Die geschützte Web-API verwendet dieses Token, um eine Downstream-Web-API im Namen des Benutzers aufzurufen. Die Web-API kann später auch Token für andere Downstream-APIs anfordern (allerdings immer noch im Namen desselben Benutzers).

## <a name="usernamepassword"></a>Benutzername/Kennwort

Die [OAuth 2-Gewährung für Kennwortanmeldeinformationen des Ressourcenbesitzers](v2-oauth-ropc.md) (Resource Owner Password Credentials, ROPC) ermöglicht es einer Anwendung, Benutzer anzumelden, indem sie ihr Kennwort direkt verarbeitet. Sie können in Ihrer Desktopanwendung den Benutzername/Kennwort-Flow verwenden, um ein Token automatisch abzurufen. Bei Verwendung der Anwendung ist keine Benutzeroberfläche erforderlich.

![Diagramm: Benutzername/Kennwort-Fluss](media/msal-authentication-flows/username-password.png)

Im obigen Diagramm führt die Anwendung folgende Vorgänge aus:

1. Ruft ein Token ab, indem der Benutzername und das Kennwort an den Identitätsanbieter gesendet werden.
2. Sie ruft eine Web-API mithilfe des Tokens auf.

> [!WARNING]
> Von diesem Fluss wird abgeraten. Er erfordert ein hohes Maß an Vertrauenswürdigkeit und die Offenlegung vieler Benutzerinformationen. Verwenden Sie diesen Flow *nur*, wenn kein anderer Flow verfügbar ist, der mehr Sicherheit bietet. Weitere Informationen finden Sie unter [What's the solution to the growing problem of passwords? (Wie sich das zunehmende Problem der Passwörter lösen lässt.)](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/).

Der bevorzugte Ablauf für die Beschaffung eines Tokens im Hintergrund auf Computern, die in eine Windows-Domäne eingebunden sind, ist die [integrierte Windows-Authentifizierung](#integrated-windows-authentication). Verwenden Sie in anderen Fällen den [Gerätecodeflow](#device-code).

Der Benutzername/Kennwort-Flow ist zwar in einigen Szenarien wie DevOps hilfreich, vermeiden Sie ihn jedoch, wenn Sie Benutzernamen und Kennwörter in interaktiven Szenarien verwenden möchten, in denen Sie eine eigene Benutzeroberfläche bereitstellen.

Die Verwendung von Benutzernamen und Kennwörtern hat folgende Auswirkungen:

- Benutzer, die auf die mehrstufige Authentifizierung angewiesen sind, können sich nicht anmelden, da keine Interaktion stattfindet.
- Die Benutzer können einmaliges Anmelden nicht verwenden.

### <a name="constraints"></a>Einschränkungen

Neben den [Einschränkungen der integrierten Windows-Authentifizierung](#integrated-windows-authentication) gelten zusätzlich folgende Einschränkungen:

- Der Benutzername/Kennwort-Fluss ist nicht kompatibel mit dem bedingten Zugriff und der mehrstufigen Authentifizierung. Wenn also Ihre App in einem Azure AD-Mandanten ausgeführt wird, für den der Mandantenadministrator die mehrstufige Authentifizierung fordert, können Sie diesen Flow nicht nutzen. Dies ist aber in vielen Organisationen der Fall.
- ROPC funktioniert nur für Geschäfts-, Schul- oder Unikonten. Sie können ROPC nicht für Microsoft-Konten (MSA) verwenden.
- Der Fluss ist für .NET-Desktop- und .NET Core-Plattformen, aber nicht für die Universelle Windows-Plattform verfügbar.
- In Azure AD B2C funktioniert der ROPC-Flow nur für lokale Konten. Informationen zu ROPC in MSAL.NET und Azure AD B2C finden Sie unter [Verwenden von ROPC mit Azure AD B2C](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc).

## <a name="integrated-windows-authentication"></a>Integrierte Windows-Authentifizierung

MSAL unterstützt die integrierte Windows-Authentifizierung (IWA) für Desktop- und mobile Anwendungen auf Windows-Computern, die in eine Domäne oder in Azure AD eingebunden sind. Mit IWA können diese Anwendungen automatisch ein Token abrufen, ohne dass der Benutzer mit der Benutzeroberfläche interagieren muss.

![Diagramm: integrierte Windows-Authentifizierung](media/msal-authentication-flows/integrated-windows-authentication.png)

Im obigen Diagramm führt die Anwendung folgende Vorgänge aus:

1. Sie ruft ein Token mithilfe der integrierten Windows-Authentifizierung ab.
2. Verwendet das Token für Anforderungen an die Ressource.

### <a name="constraints"></a>Einschränkungen

Die integrierte Windows-Authentifizierung (IWA) unterstützt *nur* Verbundbenutzer, d. h. Benutzer, die in Azure Active Directory erstellt und von Azure AD unterstützt werden. Direkt in Azure AD erstellte Benutzer ohne Active Directory-Unterstützung (verwaltete Benutzer) können diesen Authentifizierungsflow nicht verwenden. Diese Einschränkung wirkt sich nicht auf den [Benutzername/Kennwort-Fluss](#usernamepassword) aus.

IWA ist für .NET Framework-, .NET Core- und Universelle Windows-Plattform-Anwendungen bestimmt.

Die mehrstufige Authentifizierung (MFA) wird von der IWA nicht umgangen. Wenn eine solche Authentifizierung konfiguriert ist, kann die IWA im Fall einer MFA-Aufforderung fehlschlagen. Die MFA erfordert eine Interaktion mit dem Benutzer.

Sie haben keinen Einfluss darauf, wann der Identitätsanbieter eine MFA anfordert. Diese Einschränkung gilt allerdings nicht für den Mandantenadministrator. In der Regel ist die zweistufige Authentifizierung erforderlich, wenn Sie sich aus einem anderen Land/einer anderen Region anmelden, nicht über ein VPN mit einem Unternehmensnetzwerk verbunden sind und gelegentlich sogar dann, wenn eine VPN-Verbindung besteht. Azure AD greift auf KI zurück, um zu ermitteln, ob eine MFA erforderlich ist. Wenn IWA zu einem Fehler führt, greifen Sie auf eine [interaktive Benutzeraufforderung](#interactive-and-non-interactive-authentication) zurück.

Die beim Erstellen der öffentlichen Clientanwendung übergebene Autorität muss eine der folgenden Voraussetzungen erfüllen:

- Sie muss auf Mandanten im Format `https://login.microsoftonline.com/{tenant}/,` beruhen, wobei `{tenant}` entweder die GUID ist, die die Mandanten-ID darstellt, oder ein Domänenname, der dem Mandanten zugeordnet ist.
- Sie muss einem Geschäfts-, Schul- oder Unikonto entsprechen (`https://login.microsoftonline.com/organizations/`). Persönliche Microsoft-Konten (MSA) werden nicht unterstützt; Mandanten vom Typ `/common` oder `/consumers` können nicht verwendet werden.

Da die IWA ein automatischer Fluss ist, muss eine der folgenden Bedingungen zutreffen:

- Der Benutzer muss vorher in die Nutzung Ihrer Anwendung eingewilligt haben.
- Der Mandantenadministrator muss vorher im Namen aller Benutzer in die Nutzung der Anwendung eingewilligt haben.

Dies bedeutet, dass eine der folgenden Aussagen zutrifft:

- Sie als Entwickler haben im Azure-Portal **Gewähren** für sich selbst ausgewählt.
- Ein Mandantenadministrator hat bei der App-Registrierung im Azure-Portal auf der Registerkarte **API-Berechtigungen** die Option **Administratorzustimmung für {Mandantendomäne} erteilen/widerrufen** ausgewählt (siehe [Hinzufügen von Zugriffsberechtigungen für Ihre Web-API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)).
- Sie haben eine Möglichkeit für Benutzer eingeräumt, der Anwendung zuzustimmen (siehe [Anfordern der Zustimmung einzelner Benutzer](v2-permissions-and-consent.md#requesting-individual-user-consent)).
- Sie haben eine Möglichkeit für den Mandantenadministrator eingeräumt, der Anwendung zuzustimmen (siehe [Zustimmung des Administrators](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)).

Der IWA-Flow ist für .NET-Desktop-, .NET Core- und UWP-Apps (universelle Windows-Plattform) aktiviert.

Weitere Informationen zur Zustimmung finden Sie unter [Berechtigungen und Zustimmung für v2.0](v2-permissions-and-consent.md).

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie die von der Microsoft-Authentifizierungsbibliothek unterstützten Authentifizierungsflows überprüft haben, können Sie sich über das Abrufen und Zwischenspeichern der in diesen Flows verwendeten Token informieren:

[Abrufen und Zwischenspeichern von Token mithilfe der Microsoft-Authentifizierungsbibliothek (Microsoft Authentication Library, MSAL)](msal-acquire-cache-tokens.md)
