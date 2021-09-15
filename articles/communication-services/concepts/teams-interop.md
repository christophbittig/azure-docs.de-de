---
title: Interoperabilität von Teams-Besprechungen
titleSuffix: An Azure Communication Services concept document
description: Beitreten zu Teams-Besprechungen
author: chpalm
manager: chpalm
services: azure-communication-services
ms.author: chpalm
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 1bcb97892965cbe978899df4208888a4adb07253
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123251337"
---
# <a name="teams-interoperability"></a>Teams-Interoperabilität

> [!IMPORTANT]
> Die BYOI-Interoperabilität befindet sich in der Public Preview-Phase und ist für alle Communication Services-Anwendungen und Teams-Organisationen verfügbar.
>
> Die von Microsoft 365 authentifizierte Interoperabilität befindet sich in der privaten Vorschau, und die Verwendung von Dienststeuerungen ist auf Early Adopter von Azure Communication Services beschränkt. Wenn Sie am Early Access-Programm teilnehmen möchten, füllen Sie [dieses Formular](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8MfnD7fOYZEompFbYDoD4JUMkdYT0xKUUJLR001ODdQRk1ITTdOMlRZNSQlQCN0PWcu) aus.
>
> Preview-APIs und -SDKs werden ohne Vereinbarung zum Servicelevel bereitgestellt und sind nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Mit Azure Communication Services können benutzerdefinierte Anwendungen erstellt werden, die mit Microsoft Teams interagieren. Endbenutzer Ihrer Communication Services-Anwendung können mit den Teams-Teilnehmern über Sprache, Video, Chat und Bildschirmfreigabe interagieren. Dies wird im folgenden Video gezeigt:


<br>
<br>


> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWGTqQ]


Azure Communication Services unterstützt abhängig von der Identität des Endbenutzers zwei Arten von Teams-Interoperabilität:

- **Verwendung eigener Identitäten:** Sie steuern die Benutzerauthentifizierung, und Benutzer Ihrer benutzerdefinierten Anwendungen benötigen keine Azure Active Directory-Identitäten oder Teams-Lizenzen für die Teilnahme an Teams-Besprechungen. Ihre Anwendung wird von Teams als anonymer externer Benutzer behandelt.
- **Microsoft 365-Teams-Identität:** Ihre Anwendung agiert im Namen der Identität und der konfigurierten Teams-Ressourcen eines Microsoft 365-Endbenutzers. Diese authentifizierten Anwendungen können im Namen von Microsoft 365-Benutzern nahtlos Anrufe tätigen und Besprechungen beitreten.

Anwendungen können beide Authentifizierungsschemas implementieren und die Wahl der Authentifizierung dem Endbenutzer überlassen.

## <a name="overview"></a>Übersicht

Benutzer können auf zwei Arten auf die Teams-Anrufbenutzeroberfläche zugreifen:

- Über Teams-Clients als **Teams-Benutzer**. Dies schließt desktopbasierte, mobile und webbasierte Teams-Clients ein. 
- Über die Weboberfläche Ihrer Anwendung als **anonyme Teams-Benutzer**. 

Anonyme Teams-Benutzer müssen keine Teams-Benutzer sein. Azure Communication Services ermöglicht das Erstellen und Anpassen neuer Teams-Anrufendpunkte für Teams-Benutzer und anonyme Teams-Benutzer. Sie können das Calling SDK von Communication Services und die Benutzeroberflächenbibliothek für die Anpassung und Integration in bereits vorhandene Anwendungen oder Produkte verwenden. Das folgende Diagramm zeigt den Beitritt zu einer Teams-Besprechung von mehreren Endpunkten aus: ![Übersicht über mehrere Interoperabilitätsszenarien in Azure Communication Services](./media/teams-identities/teams_interop_overview.png)

Wenn ein Endpunkt unter Verwendung einer Teams-Identität über die Azure Communication Services-Clientbibliotheken eine Verbindung mit einer Teams-Besprechung herstellt, wird der Endpunkt wie ein Teams-Benutzer mit einem Teams-Client behandelt. Teams-Benutzer haben Zugriff auf mehr Funktionen als anonyme Teams-Benutzer. Teams-Benutzer können Teams-Besprechungen beitreten, andere Teams-Benutzer anrufen, Anrufe von Telefonnummern entgegennehmen und laufende Anrufe an die Teams-Anrufwarteschlange übergeben. Die Konnektivität des Communication Services-Endpunkts mit Teams-Identität ist im folgenden Diagramm dargestellt:

![Übersicht über Interoperabilitätsszenarien in Azure Communication Services](./media/teams-identities/teams_interop_m365_identity_interop_overview.png)

## <a name="bring-your-own-identity"></a>Verwendung eigener Identitäten

Die Verwendung eigener Identitäten (Bring Your Own Identity, BYOI) ist das gängige Modell für die Verwendung von Azure Communication Services und Teams-Interoperabilität. Es unterstützt alle Identitätsanbieter und Authentifizierungsschemas. Im ersten ermöglichten Szenario kann Ihre Anwendung Microsoft Teams-Besprechungen beitreten, und Teams behandelt die entsprechenden Benutzer als anonyme externe Konten – genau wie Benutzer, die über die anonyme Teams-Webanwendung beitreten. Dies eignet sich perfekt für B2C-Anwendungen, die (mit Teams vertraute) Mitarbeiter und externe Benutzer (mit einer benutzerdefinierten Anwendungsumgebung) in einer Besprechungsumgebung zusammenbringen. Später kommen noch weitere Szenarien hinzu. Dazu zählen beispielsweise direkte Anrufe und Chats, die es Ihrer Anwendung ermöglichen, Anrufe und Chats mit Teams-Benutzern außerhalb des Kontexts einer Teams-Besprechung zu initiieren.

Die Möglichkeit für Communication Services-Benutzer, Teams-Besprechungen als anonyme Benutzer beizutreten, wird durch die bereits vorhandene Konfiguration zum Zulassen des anonymen Besprechungsbeitritts gesteuert. Diese steuert auch den vorhandenen anonymen Besprechungsbeitritt von Teams.  Diese Einstellung kann in Teams Admin Center (https://admin.teams.microsoft.com/meetings/settings) oder mithilfe des Teams-PowerShell-Cmdlets (https://docs.microsoft.com/powershell/module/skype/set-csteamsmeetingconfiguration) aktualisiert werden. Genau wie beim anonymen Besprechungsbeitritt von Teams muss Ihre Anwendung über den Besprechungslink für den Beitritt verfügen. Dieser kann über die Graph-API oder aus dem Kalender in Microsoft Teams abgerufen werden.  Der in Teams angezeigte Name von Communication Services-Benutzern kann über das Calling SDK von Communication Services konfiguriert werden.

Externe Benutzer können grundlegende Audio-, Video-, Bildschirmfreigabe- und Chatfunktionen über Azure Communication Services-SDKs verwenden. Features wie das Heben der Hand, der Zusammen-Modus und Gruppenräume stehen nur Teams-Benutzern zur Verfügung. Communication Services-Benutzer können Nachrichten nur senden und empfangen, solange sie sich in der Teams-Besprechung befinden und die Besprechung nicht für einen Kanal geplant ist.

Ihre benutzerdefinierte Anwendung sollte die Benutzerauthentifizierung und andere Sicherheitsmaßnahmen zum Schutz von Teams-Besprechungen berücksichtigen. Seien Sie sich der Auswirkungen auf die Sicherheit bewusst, wenn Sie anonymen Benutzern die Teilnahme an Besprechungen ermöglichen, und verwenden Sie den [Teams-Sicherheitsleitfaden](/microsoftteams/teams-security-guide#addressing-threats-to-teams-meetings), um die für anonyme Benutzer verfügbaren Funktionen zu konfigurieren.

Weitere Informationen zu erforderlichen Dataflows für den Beitritt zu Teams-Besprechungen finden Sie auf der Seite [Client- und Serverarchitektur](client-and-server-architecture.md). Das [Hero-Beispiel für Gruppenanrufe](../samples/calling-hero-sample.md) enthält Beispielcode für den Beitritt zu einer Teams-Besprechung über eine Webanwendung.

## <a name="microsoft-365-teams-identity"></a>Microsoft 365-Teams-Identität
Das Calling SDK von Azure Communication Services kann mit Microsoft 365-Teams-Identitäten verwendet werden, um Teams-ähnliche Umgebungen für die Teams-Interoperabilität zu unterstützen. Microsoft 365-Teams-Identitäten werden von Azure Active Directory bereitgestellt und authentifiziert. Ihre App kann Anrufe mit einer regulären Microsoft 365-Identität tätigen oder annehmen. Alle Attribute und Details im Zusammenhang mit dem Benutzer sind an den Azure Active Directory-Benutzer gebunden.

Dieses Identitätsmodell eignet sich perfekt für Anwendungsfälle, in denen eine benutzerdefinierte Benutzeroberfläche erforderlich ist, der Teams-Client nicht für Ihre Plattform verfügbar ist oder der Teams-Client keine ausreichenden Anpassungen unterstützt. So kann beispielsweise eine Anwendung mit einer für Empfangsmitarbeiter oder Callcenter optimierten Benutzeroberfläche verwendet werden, um Telefonanrufe mit der von Teams bereitgestellten Festnetznummer des Endbenutzers entgegenzunehmen.  

Telefonie- und Bildschirmfreigabefunktionen stehen über das Calling SDK von Communication Services zur Verfügung. Die Anrufverwaltung ist über die Graph-API oder per Konfiguration im Teams-Client oder im Teams-Verwaltungsportal verfügbar. Chatfunktionen stehen über die Graph-API zur Verfügung.

Teams-Benutzer werden per MSAL-Bibliothek über Azure Active Directory in der Clientanwendung authentifiziert. Authentifizierungstoken werden durch Microsoft 365-Teams-Token über das Identity SDK von Communication Services ausgetauscht. Es empfiehlt sich, einen Tokenaustausch in Ihrem Back-End-Diensten zu implementieren, da Austauschanforderungen durch Anmeldeinformationen für Azure Communication Services signiert werden. In Ihren Back-End-Diensten können Sie eine beliebige zusätzliche Authentifizierung erforderlich machen.

Wenn Sie mehr über die Funktion erfahren möchten, können Sie dem TAP-Early Access-Programm beitreten. Füllen Sie hierzu [dieses Formular](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8MfnD7fOYZEompFbYDoD4JUMkdYT0xKUUJLR001ODdQRk1ITTdOMlRZNSQlQCN0PWcu) aus.

## <a name="comparison"></a>Vergleich

|Kriterien|Verwendung eigener Identitäten| Microsoft 365-Teams-Identität|
|---|---|---|
|Zutreffend| In B2C-Szenarien für Consumeranwendungen | In B2B- oder B2C-Szenarien für Geschäftsanwendungen |
|Identitätsanbieter|Beliebig|Azure Active Directory|
|Authentifizierung und Autorisierung|Benutzerdefiniert*| Azure Active Directory und benutzerdefiniert*|
|Telefonie verfügbar über | Calling SDKs von Communication Services | Calling SDKs von Communication Services |
|Chat verfügbar über | Chat SDKs von Communication Services | Graph-API |
|Telefonfestnetzunterstützung| Ausgehende Sprachanrufe, ausgehendes Direct Routing, [Details](./telephony-sms/telephony-concept.md) | Eingehende Anrufe mit Teams-Identitätszuweisung, ausgehende Anrufe mit Telefontarif|

\* Durch die Serverlogik zum Ausstellen von Zugriffstoken kann eine beliebige benutzerdefinierte Authentifizierung und Autorisierung der Anforderung vorgenommen werden.

## <a name="privacy"></a>Datenschutz
Die Interoperabilität zwischen Azure Communication Services und Microsoft Teams ermöglicht Ihren Anwendungen und Benutzern, an Teams-Anrufen, -Besprechungen und -Chats teilzunehmen. Sie sind dafür verantwortlich, dass die Benutzer Ihrer Anwendung benachrichtigt werden, wenn die Aufzeichnung oder Transkription in einem Teams-Anruf oder einer Teams-Besprechung aktiviert ist.

Microsoft gibt Ihnen über die Azure Communication Services-API einen Hinweis, dass die Aufzeichnung oder Transkription begonnen hat. Sie müssen dies Ihren Benutzern auf der Benutzeroberfläche Ihrer Anwendung in Echtzeit mitteilen. Sie erklären sich damit einverstanden, Microsoft für alle Kosten und Schäden freizustellen, die aufgrund der Nichteinhaltung dieser Verpflichtung durch Sie entstehen.

## <a name="pricing"></a>Preise
Durch die Nutzung von Azure Communication Services-APIs und entsprechenden SDKs erhöhen sich die [Verbrauchseinheiten für die Abrechnung von Azure Communication Services](https://azure.microsoft.com/pricing/details/communication-services/). Diese Verbrauchseinheiten erhöhen sich auch durch Interaktionen mit Microsoft Teams (etwa durch den Beitritt zu einer Besprechung oder die Initiierung eines Telefonanrufs mit einer von Teams zugeordneten Nummer). Für die eigentliche Teams-Interoperabilitätsfunktion fällt dagegen keine zusätzliche Gebühr an, und es gibt keinen Preisunterschied zwischen den Authentifizierungsoptionen BYOI und Microsoft 365.

Wenn ein Endbenutzer über Ihre Azure-Anwendung zehn Minuten an einer Besprechung mit einem Benutzer von Microsoft Teams teilnimmt, haben diese beiden Benutzer zusammen 20 Anrufminuten verbraucht. Die zehn Minuten über die benutzerdefinierte Anwendung unter Verwendung von Azure-APIs und entsprechenden SDKs werden für Ihre Ressource in Rechnung stellen. Die zehn Minuten des Endbenutzers in der nativen Teams-Anwendung sind dagegen durch die entsprechende Teams-Lizenz abgedeckt und werden nicht von Azure erfasst.

## <a name="teams-in-government-clouds-gcc"></a>Teams in Government-Clouds (GCC)
Azure Communication Services-Interoperabilität ist derzeit nicht mit Teams-Bereitstellungen mit [Microsoft 365-Government-Clouds (GCC)](/MicrosoftTeams/plan-for-government-gcc) kompatibel.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Schnellstart: Beitreten mit einer Telefonie-App zu einer Teams-Besprechung](../quickstarts/voice-video-calling/get-started-teams-interop.md)
> [Schnellstart: Einrichten und Verwalten von Teams-Zugriffstoken](../quickstarts/manage-teams-identity.md)
