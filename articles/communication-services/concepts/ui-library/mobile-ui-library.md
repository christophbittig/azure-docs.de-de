---
title: Mobile UI-Bibliothek
titleSuffix: An Azure Communication Services - UI Mobile Library
description: In diesem Dokument wird die mobile UI-Bibliothek eingeführt.
author: jorgegarc
ms.author: jorgegarc
ms.date: 09/14/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: fdf84a2eb2cc57081a315bbf9dc41c14ee96ae8e
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130181625"
---
# <a name="ui-mobile-library"></a>Mobile UI-Bibliothek

[!INCLUDE [Public Preview Notice](../../includes/private-preview-include.md)]

Bei unserer mobilen UI-Bibliothek handelt es sich um eine Azure Communication Services-Funktion, deren Kernaufgabe in der Bereitstellung von Benutzeroberflächenkomponenten für allgemeine Anrufinteraktionen zwischen Unternehmen und Kunden sowie zwischen Unternehmen liegt. Der Hauptfokus der mobilen UI-Bibliothek liegt auf Komponenten für [Video- und Sprachanrufe](../voice-video-calling/calling-sdk-features.md). Wir bauen auf den Anruf-Primitiven von Azure auf, um eine vollständige Benutzeroberfläche für Anruf- und Besprechungserfahrungen auf mobilen Geräten zu bieten.

Das Ziel der mobile UI-Bibliothek besteht darin, Ihnen diese Funktionen in einem schlüsselfertigen, zusammengesetzten Format zur Verfügung zu stellen. Wenn Sie das SDK im Canvas Ihrer bevorzugten mobilen Entwicklungs-App hinzufügen, wird durch das SDK eine vollständige Benutzererfahrung generiert. Da diese Benutzererfahrung schlank ist, können Sie die verringerte Entwicklungszeit und geringere technische Komplexität nutzen.

## <a name="composites"></a>Verbundwerkstoffe

Composites sind allgemeinere Komponenten, die aus kleineren Benutzeroberflächenkomponenten bestehen, die mithilfe von Azure Communication Services schlüsselfertige Lösungen für allgemeine Kommunikationsszenarien bereitstellen.

Entwickler können die Composites ganz einfach mithilfe eines Azure Communication Services-Zugriffstokens und der erforderlichen Konfigurationsattribute für Anrufe instanziieren.

| Composite                                                                   | Anwendungsfälle                                                                                                                                                                                                                                                                                                  |
| --------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [CallComposite](../../quickstarts/ui-library/get-started-call.md)  | Anrufbenutzeroberfläche, mit der Benutzer einen Anruf starten oder ihm beitreten können. Innerhalb der Erfahrung können Benutzer ihre Geräte konfigurieren, am Anruf mit Video teilnehmen und andere Teilnehmer sehen, einschließlich derjenigen mit aktivierten Videofunktionen. Um Teams-Interoperabilität zu verwenden, ist die Wartebereichsfunktionalität enthalten, damit Benutzer warten können, bis sie zugelassen werden. |

## <a name="scenarios"></a>Szenarien

### <a name="joining-a-videoaudio-call"></a>Beitritt zu einem Video-/Audioanruf

Benutzer können ganz einfach über den Anruf mithilfe der *Teams-Besprechungs-URL* beitreten oder einen Azure Communication Services-Anruf mit einer einfacheren und großartigen Erfahrung einrichten, genau wie die Teams-Anwendung. Hinzufügen der Funktion, mit der der Benutzer Teil eines umfangreichen Livevideo- oder -audioanrufs werden kann, ohne die Erfahrung der Einfachheit zu verlieren, und sich dabei auf das konzentrieren zu können, was wirklich wichtig ist.

### <a name="pre-call-experience"></a>Erfahrung vor dem Anruf

Als Teilnehmer der Anrufe können Sie einen Namen angeben sowie eine Standardkonfiguration für Audio- und Videogeräte einrichten – und schon können Sie an dem Anruf teilnehmen.

:::image type="content" source="../media/mobile-ui/teams-meet.png" alt-text="Erfahrung vor der Besprechung.":::

### <a name="call-experience"></a>Anruferfahrung

Das Anruf-Composite bietet eine End-to-End-Erfahrung, optimiert die Entwicklungszeit und konzentriert sich auf ein sauberes Layout.  

:::image type="content" source="../media/mobile-ui/calling-composite.png" alt-text="Benutzererfahrung.":::

**Die Anruferfahrung bietet all diese Funktionen in einer einzelnen zusammengesetzten Komponente und stellt so einen eindeutigen Pfad ohne komplexen Code zur Verfügung, was zu einer schnelleren Entwicklungszeit führt.**

### <a name="quality-and-security"></a>Qualität und Sicherheit

Mobile Composites werden mit [Azure Communication Services-Zugriffstoken](../../quickstarts/access-tokens.md) initialisiert.

### <a name="more-details"></a>Weitere Informationen

Weitere Informationen zu mobilen Composites finden Sie auf der [Website mit Anwendungsfällen](mobile-ui-use-cases.md).

## <a name="what-ui-artifact-is-best-for-my-project"></a>Welches Benutzeroberflächenartefakt ist für mein Projekt am besten geeignet?

Wenn Sie sich mit den folgenden Anforderungen vertraut machen, können Sie die richtige Clientbibliothek auswählen:

- **Welchen Anpassungsgrad streben Sie an?** Die zentralen Azure Communication Services-Clientbibliotheken weisen keine Benutzeroberfläche auf und sind so konzipiert, dass Sie die Benutzeroberfläche nach Ihren Vorstellungen erstellen können. Mit den Komponenten der UI-Bibliothek werden UI-Ressourcen auf Kosten verringerter Anpassungsmöglichkeiten bereitgestellt.

- **Welche Plattformen sollen verwendet werden?** Verschiedene Plattformen verfügen über unterschiedliche Funktionen.

Ausführliche Informationen zur Verfügbarkeit von Features in der UI-Bibliothek finden Sie [hier](mobile-ui-use-cases.md), doch eine Zusammenfassung der wichtigsten Nachteile finden Sie unten.

| Clientbibliothek/SDK  | Implementierungskomplexität | Möglichkeit zur Anpassung | Aufrufen |  [Teams-Interoperabilität](../../concepts/teams-interop.md) |
| --------------------- | ------------------------- | --------------------- |  ---- | ----------------------------------------------------------------------------------------------- |
| Zusammengesetzte Komponenten  | Niedrig                       | Niedrig                   |         ✔    | ✔                                                                                               |
| Zentrale Clientbibliotheken | High                      | High                  |         ✔    | ✔                                                                                               |

> [!div class="nextstepaction"]

Weitere Informationen zu den ersten Schritten mit den Composites der mobilen UI-Bibliothek finden Sie in [unserem Leitfaden für den Schnellstart](../../quickstarts/ui-library/get-started-call.md).