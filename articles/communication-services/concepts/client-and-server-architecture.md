---
title: Client- und Serverarchitektur
titleSuffix: An Azure Communication Services concept document
description: Erfahren Sie mehr zur Architektur von Communication Services.
author: probableprime
manager: mikben
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 1346c9f6505c03ccebb2d2e2dc33e899050bfe20
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128672018"
---
# <a name="client-and-server-architecture"></a>Client- und Serverarchitektur

Auf dieser Seite werden typische Architekturkomponenten und Dataflows in verschiedenen Azure Communication Service-Szenarien veranschaulicht. Zu den relevanten Komponenten gehören:

1. **Clientanwendung**. Diese Website oder native Anwendung wird von Endbenutzern für die Kommunikation genutzt. Azure Communication Services bietet [SDK-Clientbibliotheken](sdk-options.md) für mehrere Browser und Anwendungsplattformen. Zusätzlich zu unseren Kern-SDKs ist [eine UI-Bibliothek](https://aka.ms/acsstorybook) verfügbar, um die Entwicklung von Browseranwendungen zu beschleunigen.
1. **Identitätsverwaltungsdienst**.  Diese Dienstfunktion können Sie erstellen, um Benutzer und andere Konzepte in Ihrer Geschäftslogik zu Azure Communication Services zuzuordnen und bei Bedarf Token für diese Benutzer zu erstellen.
1. **Anrufverwaltungsdienst**.  Diese Dienstfunktion erstellen Sie zum Verwalten und Überwachen von Sprach- und Videoanrufen.  Dieser Dienst kann über das Calling Automation SDK und die REST-APIs Anrufe erstellen, Benutzer einladen, Telefonnummern anrufen, Audio wiedergeben, DMTF-Töne anhören und viele andere Anruffeatures nutzen.


## <a name="user-access-management"></a>Verwaltung des Benutzerzugriffs

Azure Communication Services-Clients müssen `user access tokens` bereitstellen, um sicher auf Communication Services-Ressourcen zuzugreifen. `User access tokens` sollten von einem vertrauenswürdigen Dienst generiert und verwaltet werden, und zwar aufgrund der vertraulichen Natur des Tokens und der für seine Generierung erforderlichen Verbindungszeichenfolge oder verwalteten Identität. Wenn Zugriffstoken nicht ordnungsgemäß verwaltet werden, kann dies zu zusätzlichen Kosten aufgrund des falschen Einsatzes von Ressourcen führen.

:::image type="content" source="../media/scenarios/architecture_v2_identity.svg" alt-text="Diagramm der Architektur von Benutzerzugriffstoken":::

### <a name="dataflows"></a>Dataflows
1. Der Benutzer startet die Clientanwendung. Der Entwurf dieser Anwendung und das Schema der Benutzerauthentifizierung liegen in Ihrer Hand.
2. Die Clientanwendung kontaktiert Ihren Identitätsverwaltungsdienst. Der Identitätsverwaltungsdienst verwaltet eine Zuordnung zwischen Ihren Benutzern und anderen adressierbaren Objekten (z. B. Diensten oder Bots) und den Azure Communication Service-Identitäten.
3. Der Identitätsverwaltungsdienst erstellt ein Benutzerzugriffstoken für die entsprechende Identität. Wenn in der Vergangenheit keine Azure Communication Services-Identität zugewiesen wurde, wird eine neue Identität erstellt.  

### <a name="resources"></a>Ressourcen
- **Konzept:** [Benutzeridentität](identity-model.md)
- **Schnellstart:** [Erstellen und Verwalten von Zugriffstoken](../quickstarts/access-tokens.md)
- **Tutorial:** [Erstellen eines Identitätsverwaltungsdiensts mithilfe von Azure Functions](../tutorials/trusted-service-tutorial.md)

> [!IMPORTANT]
> Der Einfachheit halber werden die Verwaltung des Benutzerzugriffs und die Verteilung der Token in den nachfolgenden Architekturabläufen nicht dargestellt.


## <a name="calling-a-user-without-push-notifications"></a>Anrufen eines Benutzers ohne Pushbenachrichtigung
Das einfachste Szenario für Sprach- und Videoanrufe besteht darin, dass ein Benutzer einen anderen Benutzer im Vordergrund und ohne Pushbenachrichtigung anruft.

:::image type="content" source="../media/scenarios/architecture_v2_calling_without_notifications.svg" alt-text="Diagramm: Communication Services-Architektur zum Anrufen ohne Pushbenachrichtigungen":::

### <a name="dataflows"></a>Dataflows

1. Der annehmende Benutzer initialisiert den Anrufclient, sodass er eingehende Anrufe empfangen kann.
2. Der Benutzer, der den Anruf initiiert, benötigt die Azure Communication Services-Identität der Person, die er anrufen möchte. Ein typisches Beispiel ist eine *Freundesliste*, die vom Identitätsverwaltungsdienst verwaltet wird, der die Freunde des Benutzers und die zugehörigen Azure Communication Service-Identitäten zusammenfasst.
3. Der initiierende Benutzer initialisiert seinen Anrufclient und ruft den Remotebenutzer an.
4. Der annehmende Benutzer wird durch das Calling SDK über den eingehenden Anruf benachrichtigt.
5. Die Benutzer kommunizieren mithilfe von Sprache und Video in einem Anruf miteinander.

### <a name="resources"></a>Ressourcen
- **Konzept:** [Anrufübersicht](voice-video-calling/calling-sdk-features.md)
- **Schnellstart:** [Hinzufügen von Sprachanrufen zu Ihrer App](../quickstarts/voice-video-calling/getting-started-with-calling.md)
- **Schnellstart:** [Hinzufügen von Videoanrufen zu Ihrer App](../quickstarts/voice-video-calling/get-started-with-video-calling.md)
- **Hero-Beispiel:** [Gruppenanruf für Web, iOS und Android](../samples/calling-hero-sample.md)


## <a name="joining-a-user-created-group-call"></a>Beitreten zu einem vom Benutzer erstellten Gruppenaufruf
Möglicherweise möchten Sie, dass Benutzer einem Aufruf ohne explizite Einladung beitreten. Sie können z. B. einen *gemeinschaftlichen Bereich* mit einem zugehörigen Anruf einrichten, dem die Benutzer nach Belieben beitreten können. In diesem ersten Dataflow wird ein Anruf angezeigt, der anfänglich von einem Client erstellt wird.

:::image type="content" source="../media/scenarios/architecture_v2_calling_join_client_driven.svg" alt-text="Diagramm: Communication Services-Architektur, die Out-of-Band-Signalisierung aufruft":::

### <a name="dataflows"></a>Dataflows
1. Der initiierende Benutzer initialisiert seinen Anrufclient und startet einen Gruppenanruf.
2. Der initiierende Benutzer teilt die Gruppenanruf-ID mit einem Anrufverwaltungsdienst.
3. Der Anrufverwaltungsdienst gibt die Anruf-ID für andere Benutzer frei. Wenn sich die Anwendung z. B. an geplanten Ereignissen orientiert, könnte die Gruppenanruf-ID ein Attribut des Datenmodells des geplanten Ereignisses sein.
4. Andere Benutzer treten dem Anruf mithilfe der Gruppenanruf-ID bei.
5. Die Benutzer kommunizieren mithilfe von Sprache und Video in einem Anruf miteinander.


## <a name="joining-a-scheduled-teams-call"></a>Beitreten zu einem geplanten Teams-Anruf
Azure Communication Service-Anwendungen können Teams-Anrufen beitreten. Dies ist ideal für viele Business-to-Consumer-Szenarien, bei denen der Kunde eine benutzerdefinierte Anwendung und eine benutzerdefinierte Identität verwendet, während die Unternehmensseite Teams verwendet.

:::image type="content" source="../media/scenarios/architecture_v2_calling_join_teams_driven.svg" alt-text="Diagramm: Communication Services-Architektur zum Beitreten einer Teams-Besprechung":::


### <a name="dataflows"></a>Dataflows
1. Der Anrufverwaltungsdienst erstellt einen Gruppenanruf mit [Graph-APIs](/graph/api/resources/onlinemeeting?view=graph-rest-1.0). Ein anderes Muster sieht vor, dass die Benutzer den Gruppenanruf mithilfe von [Bookings](https://www.microsoft.com/microsoft-365/business/scheduling-and-booking-app), Outlook, Teams oder einer anderen Benutzeroberfläche für die Planung im Microsoft 365-Ökosystem erstellen.
2. Der Anrufverwaltungsdienst gibt die Teams-Anrufdetails für Azure Communication Service-Clients frei.
3. In der Regel muss ein Teams-Benutzer dem Anruf beitreten und externen Benutzern die Teilnahme über die Lobby ermöglichen. Diese Erfahrung reagiert jedoch empfindlich auf die Konfiguration des Teams-Mandanten und die bestimmten Besprechungseinstellungen.
4. Azure Communication Service-Benutzer initialisieren ihren Anrufclient und nehmen mithilfe der in Schritt 2 erhaltenen Daten an der Teams-Besprechung teil.
5. Die Benutzer kommunizieren mithilfe von Sprache und Video in einem Anruf miteinander.

### <a name="resources"></a>Ressourcen
- **Konzept:** [Teams-Interoperabilität](teams-interop.md)
- **Schnellstart:** [Beitreten zu einer Teams-Besprechung](../quickstarts/voice-video-calling/get-started-teams-interop.md)
