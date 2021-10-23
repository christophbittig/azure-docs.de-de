---
title: Szenarien für Anwendungsfälle der mobilen UI-Bibliothek
titleSuffix: An Azure Communication Services - UI Mobile Library use cases scenarios
description: In diesem Dokument werden die Funktionen der mobilen UI-Bibliothek und deren Funktionsweise in Ihren Anwendungen eingeführt.
author: jorgegarc
ms.author: jorgegarc
ms.date: 09/14/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: cc849bb98faea5cbf7f3ec7828cc8318061dc5a5
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130181631"
---
# <a name="ui-library-ios-and-android-capabilities"></a>Funktionen der UI-Bibliothek (iOS und Android)

[!INCLUDE [Public Preview Notice](../../includes/private-preview-include.md)]

Die UI-Bibliothek für iOS und Android unterstützt das Aufrufen von Anwendungsfällen mithilfe des **Anruf-Composites**.
Mit Composites können Entwickler ganz einfach eine vollständige Anruferfahrung mit nur wenigen Codezeilen in ihre Anwendung integrieren. Diese Composites kümmern sich um den gesamten Lebenszyklus des Anrufs von der Einrichtung bis zur Beendigung des Anrufs.

## <a name="calling"></a>Aufrufen

| Bereich                                                                                            | Anwendungsfälle                                              |
| ----------------------------------------------------------------------------------------------- | ------------------------------------------------------ |
| Call Types                                                                                      | Teams-Besprechung beitreten                                     |
|                                                                                                 | Azure Communication Services-Anruf mit Gruppen-ID beitreten   |
| [Teams-Interoperabilität](../../concepts/teams-interop.md) | Wartebereich für Anrufe                                             |
|                                                                                                 | Banner für Transkription und Aufzeichnungswarnung               |
| Teilnehmerkatalog                                                                             | Remoteteilnehmer werden im Raster angezeigt              |
|                                                                                                 | Videovorschau im gesamten Anruf für lokalen Benutzer verfügbar |
|                                                                                                 | Standardavatare, die verfügbar sind, wenn das Video deaktiviert ist            |
|                                                                                                 | Inhalte auf freigegebenen Bildschirmen, die im Teilnehmerkatalog angezeigt werden |
|                                                                                     | Teilnehmerliste                                     |
| Anrufkonfiguration                                                                              | Mikrofongeräteverwaltung                           |
|                                                                                                 | Kamerageräteverwaltung                               |
|                                                                                                 | Lautsprechergeräteverwaltung                              |
|                                                                                                 | Lokale Vorschau für Benutzer zum Überprüfen des Videos verfügbar        |
| Anrufsteuerelemente                                                                                   | Stummschalten ein/aus für Anruf                                       |
|                                                                                                 | Video ein/aus für Anruf                                   |
|                                                                                                 | Anruf beenden                                               |

## <a name="supported-identities"></a>Unterstützte Identitäten

Es wird eine Azure Communication Services-Identität benötigt, um die Composites initialisieren und die Authentifizierung beim Dienst durchführen zu können.
Weitere Informationen zur Authentifizierung finden Sie unter [Authentifizierung](../authentication.md) und [Zugriffstoken](../../quickstarts/access-tokens.md).

## <a name="teams-interop"></a>Teams-Interoperabilität

![Teams Interop-Muster für Anrufe und Chats](../media/mobile-ui/teams-interop-diagram.png)

In [Teams-Interoperabilität](../teams-interop.md)-Szenarien können Entwickler die Komponenten der mobilen UI-Bibliothek verwenden, um Teams-Besprechungen über Azure Communication Services beizutreten.
Um Teams-Interoperabilität zu aktivieren, können Entwickler das Anruf-Composite verwenden, das sich um den Lebenszyklus des Beitritts zu einem Teams-Interoperabilitätsanruf kümmert.

:::image type="content" source="../media/mobile-ui/teams-meet.png" alt-text="Benutzererfahrung vor der Besprechung":::

## <a name="theming"></a>Design

Das Anruf-Composite für iOS und Android der UI-Bibliothek bietet Schnittstellen für Entwickler, um das Design der Erfahrung durch Übergabe einer Primärfarbe zu ändern. Das Composite verwendet diese Primärfarbe, um ein geeignetes Design in der gesamten Erfahrung zu bieten.

| Android                            | iOS                                     |
| -------------------------------------------------------- | --------------------------------------------------------------- |
| :::image type="content" source="../media/mobile-ui/android-color.png" alt-text="Android-Designs"::: | :::image type="content" source="../media/mobile-ui/ios-dark.png" alt-text="iOS-Designs":::  |


## <a name="screen-size"></a>Bildschirmgröße

Das Anruf-Composite bietet Anpassung an jede Bildschirmgröße, was Unterstützung von 5-Zoll-Bildschirmen bis Tablets bieten würde, das Abrufen des dynamischen Layouts der Teilnehmerliste, Bereitstellung einer klaren Ansicht sowie die Konzentration auf die Unterhaltung.

|Split mode | Tablet-Modus|
|---------|---------|
| :::image type="content" source="../media/mobile-ui/meet-splitscreen.png" alt-text="Geteilter Bildschirm"::: |  :::image type="content" source="../media/mobile-ui/tablet-landscape.png" alt-text="Tablet-Modus"::: |

## <a name="recommended-architecture"></a>Empfohlene Architektur

Composites werden mit einem Azure Communication Services-Zugriffstoken initialisiert. Zugriffstoken sollten über einen vertrauenswürdigen Dienst, der von Ihnen verwaltet wird, aus Azure Communication Services beschafft werden. Weitere Informationen finden Sie unter [Schnellstart: Erstellen und Verwalten von Zugriffstoken](../../quickstarts/access-tokens.md) und [Erstellen eines vertrauenswürdigen Authentifizierungsdiensts mithilfe von Azure Functions](../../tutorials/trusted-service-tutorial.md).

:::image type="content" source="../media/mobile-ui/ui-library-architecture.png" alt-text="Diagramm der empfohlenen Architektur":::

Für diese Clientbibliotheken wird auch der Kontext für den Anruf benötigt, zu dem der Beitritt erfolgen soll. Ähnlich wie bei Zugriffstoken sollte dieser Kontext über Ihren eigenen vertrauenswürdigen Dienst an Clients übermittelt werden. In der Liste unten sind die Funktionen für die Initialisierung und Ressourcenverwaltung zusammengefasst, die Sie operationalisieren müssen.

| Contoso-Zuständigkeiten                                 | Zuständigkeiten der UI-Bibliothek                                     |
| -------------------------------------------------------- | --------------------------------------------------------------- |
| Bereitstellen des Zugriffstokens aus Azure                          | Übergeben des Zugriffstokens zum Initialisieren von Komponenten        |
| Bereitstellen der Aktualisierungsfunktion                                 | Aktualisieren des Zugriffstokens mit der vom Entwickler bereitgestellten Funktion          |
| Abrufen/Übergeben von Beitrittsinformationen für Anrufe oder Chats          | Übergeben von Anruf- und Chatinformationen zum Initialisieren von Komponenten |
| Abrufen/Übergeben von Benutzerinformationen für ein beliebiges benutzerdefiniertes Datenmodell | Übergeben des benutzerdefinierten Datenmodells an Komponenten für das Rendering          |

## <a name="platform-support"></a>Plattformunterstützung

|Plattform | Versionen|
|---------|---------|
| iOS     | iOS ab Version 13 |
| Android- | ab v23    |

## <a name="accessibility"></a>Zugriff

- Entwurfsbedingte Barrierefreiheit ist ein Prinzip für alle Microsoft-Produkte.
- Die UI-Bibliothek folgt diesem Prinzip, um sicherzustellen, dass alle UI-Komponenten vollständig zugänglich sind.
- Während der öffentlichen Vorschau wird die UI-Bibliothek weiterhin verbessert, und den UI-Komponenten werden Barrierefreiheitsfunktionen hinzugefügt.

## <a name="localization"></a>Lokalisierung

- Lokalisierung ist ein Schlüssel zum Entwickeln von Produkten, die auf der ganzen Welt und von Personen verwendet werden können, die verschiedene Sprachen sprechen.
- Die UI-Bibliothek bietet für einige Sprachen und Funktionen (z. B. Schreibrichtung von rechts nach links) integrierte Unterstützung.
- Entwickler können ihre eigenen Lokalisierungsdateien bereitstellen, die für die UI-Bibliothek verwendet werden sollen.

> [!div class="nextstepaction"]

Weitere Informationen zu den ersten Schritten mit den Composites der mobilen UI-Bibliothek finden Sie in [unserem Leitfaden für den Schnellstart](../../quickstarts/ui-library/get-started-call.md).