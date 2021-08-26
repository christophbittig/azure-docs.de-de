---
title: Anwendungsfälle der UI-Bibliothek
titleSuffix: An Azure Communication Services concept document
description: Erfahren Sie mehr über die UI-Bibliothek und wie sie Ihnen beim Erstellen von Kommunikationsbenutzeroberflächen helfen kann.
author: ddematheu2
manager: chrispalm
services: azure-communication-services
ms.author: dademath
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: dd338cd0f7d8e39f7f2d72445477991219e8d91c
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/30/2021
ms.locfileid: "122639774"
---
# <a name="ui-library-use-cases"></a>Anwendungsfälle der UI-Bibliothek

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

> [!NOTE]
> Eine ausführliche Dokumentation zur UI-Bibliothek finden Sie im [Storybook der UI-Bibliothek](https://azure.github.io/communication-ui-library). Dort finden Sie zusätzliche konzeptionelle Dokumentation, Schnellstarts und Beispiele.


Die UI-Bibliothek unterstützt viele Fälle von Anruf- und Chatbenutzeroberflächen.
Diese Funktionen sind über Benutzeroberflächenkomponenten und Zusammensetzungen verfügbar.
Bei Zusammensetzungen werden diese Funktionen direkt integriert und zur Verfügung gestellt, wenn die Zusammensetzung in eine Anwendung integriert wird.
Für Benutzeroberflächenkomponenten werden diese Funktionen durch eine Kombination aus Benutzeroberflächenfunktionen und zugrunde liegenden zustandsbehafteten Bibliotheken zur Verfügung gestellt.
Um diese Funktionen vollständig zu nutzen, empfehlen wir die Verwendung der Benutzeroberflächenkomponenten mit den zustandsbehafteten Anruf- und Chatclientbibliotheken.

## <a name="calling-use-cases"></a>Anrufanwendungsfälle

| Bereich                | Anwendungsfälle                                              |
| ------------------- | ------------------------------------------------------ |
| Call Types          | Teams-Besprechung beitreten                                     |
|                     | Azure Communication Services-Anruf mit Gruppen-ID beitreten   |
| [Teams-Interoperabilität](../teams-interop.md)      | Wartebereich für Anrufe                                             |
|                     | Banner für Transkription und Aufzeichnungswarnung               |
| Anrufsteuerelemente       | Stummschalten ein/aus für Anruf                                       |
|                     | Video ein/aus für Anruf                                   |
|                     | Bildschirmfreigabe                                         |
|                     | Anruf beenden                                               |
| Teilnehmerkatalog | Remoteteilnehmer werden im Raster angezeigt              |
|                     | Videovorschau im gesamten Anruf für lokalen Benutzer verfügbar |
|                     | Standardavatare, die verfügbar sind, wenn das Video deaktiviert ist            |
|                     | Inhalte auf freigegebenen Bildschirmen, die im Teilnehmerkatalog angezeigt werden |
| Anrufkonfiguration  | Mikrofongeräteverwaltung                           |
|                     | Kamerageräteverwaltung                               |
|                     | Lautsprechergeräteverwaltung                              |
|                     | Lokale Vorschau für Benutzer zum Überprüfen des Videos verfügbar        |
| Teilnehmer        | Teilnehmerliste                                     |

## <a name="chat-use-cases"></a>Chatanwendungsfälle

| Bereich         | Anwendungsfälle                                        |
| ------------ | ------------------------------------------------ |
| Chattypen   | Teams-Besprechungschat beitreten                        |
|              | Azure Communication Services-Chatthread beitreten |
| Chataktionen | Chatnachricht senden                                |
|              | Chatnachricht empfangen                             |
| Chatereignisse  | Eingabeindikatoren                                |
|              | Lesebestätigung                                     |
|              | Teilnehmer hinzugefügt/entfernt                        |
|              | Chattitel geändert                               |
| Teilnehmer | Teilnehmerliste                               |

## <a name="supported-identities"></a>Unterstützte Identitäten

Es wird eine Azure Communication Services-Identität benötigt, um die zustandsbehafteten Clientbibliotheken zu initialisieren und die Authentifizierung beim Dienst durchführen zu können.
Weitere Informationen zur Authentifizierung finden Sie unter [Authentifizierung](../authentication.md) und [Zugriffstoken](../../quickstarts/access-tokens.md?pivots=programming-language-javascript).

## <a name="teams-interop-use-case"></a>Teams Interop-Anwendungsfall

In [Teams Interop](../teams-interop.md)-Szenarien können Entwickler Komponenten der Benutzeroberflächenbibliothek verwenden, um Teams-Besprechungen über Azure Communication Services beizutreten.
Um Teams Interop zu aktivieren, können Entwickler entweder Anruf- und Chatzusammensetzungen direkt verwenden oder Benutzeroberflächenkomponenten verwenden, um eine benutzerdefinierte Benutzeroberfläche zu erstellen.
Beim Aktivieren von Anwendungen mit Anruf- und Chatfunktionen ist es wichtig zu beachten, dass der Chatclient erst initialisiert werden kann, nachdem der Teilnehmer zum Anruf zugelassen wurde.
Nach der Zulassen des Benutzers kann der Chatclient initialisiert werden, um dem Besprechungschatthread beizutreten.
Eine Anleitung finden Sie in der folgenden Abbildung:

:::image type="content" source="../media/teams-interop-pattern.png" alt-text="Teams Interop-Muster für Anrufe und Chats":::

Wenn Benutzeroberflächenkomponenten zum Bereitstellen von Teams Interop-Benutzeroberflächen verwendet werden, enthält die UI-Bibliothek Beispiele für wichtige Teile der Benutzeroberfläche.
Beispiel:
- [Beispiel für Wartebereich](https://azure.github.io/communication-ui-library/?path=/story/examples-teams-interop--lobby): Beispiel für Wartebereich, in dem Teilnehmer warten, bis sie zum Anruf zugelassen werden.
- [Konformitätsbanner](https://azure.github.io/communication-ui-library/?path=/story/examples-teams-interop--compliance-banner): Beispielbanner, das dem Benutzer zeigt, ob der Anruf aufgezeichnet wird.
- [Teams-Design](https://azure.github.io/communication-ui-library/?path=/story/examples-themes--teams): Beispieldesign, mit dem die UI-Bibliothek wie Microsoft Teams aussieht.


## <a name="customization"></a>Anpassung

Die UI-Bibliothek stellt Muster für Entwickler zur Verfügung, um Komponenten so zu ändern, dass sie dem Aussehen und Verhalten ihrer Anwendung entsprechen.
Diese Funktionen sind ein wichtiger Bereich für die Unterscheidung zwischen Zusammensetzungen und Benutzeroberflächenkomponenten, wobei Zusammensetzungen weniger Anpassungsoptionen, dafür aber einfachere Integrationsmöglichkeiten bieten.

| Anwendungsfall                                            | Verbundwerkstoffe | UI-Komponenten |
| --------------------------------------------------- | ---------- | ------------- |
| Fluent-basiertes Design                                | X          | X             |
| Das Layout der Benutzeroberfläche ist zusammensetzbar                     |            | X             |
| CSS-Stil kann verwendet werden, um Stileigenschaften zu ändern  |            | X             |
| Symbole können ersetzt werden                               |            | X             |
| Das Layout des Teilnehmerkatalogs kann geändert werden          |            | X             |
| Das Layout der Anrufsteuerung kann geändert werden                 | X          | X             |
| Datenmodelle können eingefügt werden, um Benutzermetadaten zu ändern | X          | X             |

## <a name="observability"></a>Einblick

Im Rahmen der entkoppelten Zustandsverwaltungsarchitektur der UI-Bibliothek können Entwickler direkt auf die zustandsbehafteten Anruf- und Chatclients zugreifen.

Entwickler können sich in den zustandsbehafteten Client einklinken, um den Zustand zu lesen, Ereignisse zu verarbeiten und Verhalten zu überschreiben, um es an die UI-Komponenten zu übergeben.

| Anwendungsfall                                  | Verbundwerkstoffe | UI-Komponenten |
| ----------------------------------------- | ---------- | ------------- |
| Auf den Anruf-/Chatclientstatus kann zugegriffen werden    | X          | X             |
| Auf Clientereignisse kann zugegriffen werden, und sie können behandelt werden | X          | X             |
| Auf UI-Ereignisse kann zugegriffen werden, und sie können behandelt werden     | X          | X             |

## <a name="recommended-architecture"></a>Empfohlene Architektur

:::image type="content" source="../media/ui-library-architecture.png" alt-text="Empfohlene Client-Server-Architektur der UI-Bibliothek":::

Zusammengesetzte Komponenten und Basiskomponenten werden mit einem Azure Communication Services-Zugriffstoken initialisiert. Zugriffstoken sollten über einen vertrauenswürdigen Dienst, der von Ihnen verwaltet wird, aus Azure Communication Services beschafft werden. Weitere Informationen finden Sie unter [Schnellstart: Erstellen und Verwalten von Zugriffstoken](../../quickstarts/access-tokens.md?pivots=programming-language-javascript) und [Erstellen eines vertrauenswürdigen Authentifizierungsdiensts mithilfe von Azure Functions](../../tutorials/trusted-service-tutorial.md).

Für diese Clientbibliotheken wird auch der Kontext für den Anruf oder Chat benötigt, zu dem der Beitritt erfolgt. Ähnlich wie bei Zugriffstoken sollte dieser Kontext über Ihren eigenen vertrauenswürdigen Dienst an Clients übermittelt werden. In der Liste unten sind die Funktionen für die Initialisierung und Ressourcenverwaltung zusammengefasst, die Sie operationalisieren müssen.

| Contoso-Zuständigkeiten                                 | Zuständigkeiten der UI-Bibliothek                                     |
| -------------------------------------------------------- | --------------------------------------------------------------- |
| Bereitstellen des Zugriffstokens aus Azure                          | Übergeben des Zugriffstokens zum Initialisieren von Komponenten        |
| Bereitstellen der Aktualisierungsfunktion                                 | Aktualisieren des Zugriffstokens mit der vom Entwickler bereitgestellten Funktion          |
| Abrufen/Übergeben von Beitrittsinformationen für Anrufe oder Chats          | Übergeben von Anruf- und Chatinformationen zum Initialisieren von Komponenten |
| Abrufen/Übergeben von Benutzerinformationen für ein beliebiges benutzerdefiniertes Datenmodell | Übergeben des benutzerdefinierten Datenmodells an Komponenten für das Rendering          |

## <a name="platform-support"></a>Plattformunterstützung

| SDK    | Windows            | macOS                | Ubuntu   | Linux    | Android  | iOS        |
| ------ | ------------------ | -------------------- | -------- | -------- | -------- | ---------- |
| Benutzeroberflächen-SDK | Chrome\*, Microsoft Edge (neu) | Chrome\*, Safari\*\* | Chrome\* | Chrome\* | Chrome\* | Safari\*\* |

\*Hinweis: Neben den beiden vorherigen Releases wird auch die neueste Version von Chrome unterstützt.

\*\*Hinweis: Safari wird ab Version 13.1 unterstützt. Ausgehende Videodaten werden für Safari macOS noch nicht unterstützt, für iOS dagegen schon. Die ausgehende Bildschirmfreigabe wird in der Desktopversion von iOS unterstützt.

## <a name="accessibility"></a>Zugriff

Entwurfsbedingte Barrierefreiheit ist ein Prinzip für alle Microsoft-Produkte.
Die UI-Bibliothek folgt diesem Prinzip, um sicherzustellen, dass alle UI-Komponenten vollständig zugänglich sind.
Während der öffentlichen Vorschau wird die UI-Bibliothek weiterhin verbessert, und den UI-Komponenten werden Barrierefreiheitsfunktionen hinzugefügt.
Wir erwarten, weitere Details zur Barrierefreiheit hinzuzufügen, bevor die UI-Bibliothek allgemeine Verfügbarkeit erreicht.

## <a name="localization"></a>Lokalisierung

Lokalisierung ist ein Schlüssel zum Entwickeln von Produkten, die auf der ganzen Welt und von Personen verwendet werden können, die verschiedene Sprachen sprechen.
Die UI-Bibliothek bietet für einige Sprachen und Funktionen (z. B. Schreibrichtung von rechts nach links) integrierte Unterstützung.
Entwickler können ihre eigenen Lokalisierungsdateien bereitstellen, die für die UI-Bibliothek verwendet werden sollen.
Diese Lokalisierungsfunktionen werden vor der allgemeinen Verfügbarkeit hinzugefügt.

> [!div class="nextstepaction"]
> [Storybook der UI-Bibliothek besuchen](https://azure.github.io/communication-ui-library)
