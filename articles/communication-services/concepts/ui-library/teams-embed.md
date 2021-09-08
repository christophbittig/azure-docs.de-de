---
title: Teams Embed-SDK
titleSuffix: An Azure Communication Services - Teams Embed SDK description
description: In diesem Dokument erfahren Sie mehr über die Teams Embed-Funktionen und die Funktionsweise in Ihren Anwendungen.
author: tophpalmer
ms.author: chpalm
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 14a063bc6b274c0c93ff700648e670f1c740730d
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2021
ms.locfileid: "114401025"
---
# <a name="teams-embed"></a>Teams-Einbettung

[!INCLUDE [Public Preview Notice](../../includes/private-preview-include.md)]

Bei der Teams-Einbettung handelt es sich um eine Azure Communication Services-Funktion für allgemeine Telefonieinteraktionen zwischen Unternehmen und Kunden sowie zwischen Unternehmen. Den Kern des Teams Embed-Systems bilden [Video- und Sprachanrufe](../voice-video-calling/calling-sdk-features.md), und auf Basis der Anrufprimitiven von Azure wird eine umfassende Benutzerumgebung auf der Grundlage von Microsoft Teams-Besprechungen bereitgestellt.

Beim Teams Embed-SDK ist eine Closed-Source-Komponente, die diese Funktionen in einem sofort einsatzbereiten, kombinierten Format verfügbar macht. Wenn Sie Teams Embed der Canvas Ihrer App hinzufügen, wird durch das SDK eine vollständige Benutzerumgebung generiert. Da sich diese Benutzerumgebung stark an Microsoft Teams-Besprechungen orientiert, profitieren Sie von Folgendem:

- Geringere Entwicklungsdauer und technische Komplexität.
- Vertrautheit von Endbenutzern mit Teams-Benutzeroberfläche.
- Möglichkeit zur Wiederverwendung von [Endbenutzer-Schulungsinhalten für Teams](https://support.microsoft.com/office/meetings-in-teams-e0b0ae21-53ee-4462-a50d-ca9b9e217b67).

Das Teams Embed-SDK bietet einen Großteil der Features, die auch in Teams-Besprechungen zur Verfügung stehen:

## <a name="joining-a-meeting"></a>Beitreten zu einer Besprechung

Die Benutzer haben mühelos mithilfe der Teams-Besprechungs-URL über die Besprechung Zugang zu einer einfacheren und großartigen Benutzererfahrung, genau wie die Teams-Anwendung. Der Benutzer kann an umfangreichen Live Meetings teilnehmen, ohne auf die Benutzerfreundlichkeit der Teams-Anwendung verzichten zu müssen.

## <a name="pre-meeting-experience"></a>Benutzererfahrung vor der Besprechung

Als Teilnehmer einer beliebigen Besprechung können Sie eine Standardkonfiguration für Audio- und Videogeräte einrichten. Fügen Sie Ihren Namen hinzu, und verwenden Sie Ihren eigenen Bildavatar.

## <a name="meeting-experience"></a>Benutzererfahrung während der Besprechung

Passen Sie die Benutzeroberfläche an, indem Sie die Funktionen Ihren Anforderungen anpassen. Sie steuern die gesamte Benutzererfahrung während der Besprechungen.

- [**Inhaltsfreigabe**](https://support.microsoft.com/office/share-content-in-a-meeting-in-teams-fcc2bf59-aecd-4481-8f99-ce55dd836ce8): Der Benutzer kann Videos, Fotos oder den gesamten Bildschirm freigeben, und den Benutzern wird der freigegebene Inhalt angezeigt.

- [**Mehrere Layoutoptionen für den Videokatalog**](https://support.microsoft.com/office/using-video-in-microsoft-teams-3647fc29-7b92-4c26-8c2d-8a596904cdae): Verwenden Sie die Funktionen, um die Standardoptionen für das Layout während der Besprechung auszuwählen: großer Katalog, Zusammenführungsmodus, Fokus, Anheften und Blickpunkt. Passen Sie auch das Layout der Geräteauflösung an.

- [**Video aktivieren/deaktivieren**](https://support.microsoft.com/office/using-video-in-microsoft-teams-3647fc29-7b92-4c26-8c2d-8a596904cdae#bkmk_turnvideoonoff): Ermöglichen Sie Benutzern, ihr Video während der Besprechung zu verwalten.

- **Teilnehmeraktionen**: Der Benutzer kann [„die Hand heben“](https://support.microsoft.com/en-us/office/raise-your-hand-in-a-teams-meeting-bb2dd8e1-e6bd-43a6-85cf-30822667b372), das Mikrofon stummschalten bzw. die Stummschaltung aufheben, die Kamera- oder Audiokonfiguration ändern, auflegen und viele weitere Aktionen ausführen.

- [**Unterstützung für mehrere Sprachen**](https://support.microsoft.com/topic/languages-supported-in-microsoft-teams-for-education-293792c3-352e-4b24-9fc2-4c28b5de2db8): Unterstützung von 56 Sprachen während der gesamten Teams-Erfahrung.

## <a name="quality-and-security"></a>Qualität und Sicherheit

Das Teams Embed-SDK basiert auf Teams Quality-Standards, sodass Sie [die Bandbreitenanforderungen](/microsoftteams/prepare-network#bandwidth-requirements) für die Videoqualität sehen können.

Sie können ein Azure Communication Service-Zugriffstoken verwenden. [Hier](../../quickstarts/access-tokens.md) finden Sie weitere Informationen zum Generieren und Verwalten von Zugriffstoken.

## <a name="capabilities"></a>Funktionen

| SDK-Funktionen                                                        | Verfügbarkeit |
|---------------------------------------------------------------------|--------------|
| *Besprechungsaktionen*                                                   |              |
| Annehmen eines Anrufs                                                         | Ja          |
| Behandeln des *Anrufstatus* (inklusive Verbindungsstatus, Teilnehmeranzahl sowie Modalitäten wie Mikrofon- oder Kamerastatus)                                           | Ja          |
| Auslösen von Benutzerereignissen wie Handzeichen, Stummschalten und Senden eines Videoereignisses                                                                 | Ja          |
| Unterstützung bei unzuverlässiger Netzwerkbehandlung                                      | Ja          |
| Entfernen von Teilnehmern aus der Besprechung                                    | Ja          |
| Unterstützung von 56 Sprachen                                               | Ja          |
| Chat während der Besprechung und 1n1-Chat                                    | Nein           |
| PSTN-Anrufe                                                        | Nein           |
| Aufzeichnung und Transkript                                            | Nein           |
| Whiteboardfreigabe                                                  | Nein           |
| Aufteilung in Räume                                                 | Nein           |
| *Benutzererfahrung beim Beitritt zu einer Besprechung*                                        |              |
| Beitritt bei Gruppenaufruf mit GUID und ACS-Token                             | Ja          |
| Beitritt zu Besprechungen mit Live Meeting-URL und ACS-Token                    | Ja          |
| Beitritt zu Besprechungen mit Meeting-URL und ACS-Token                         | Ja          |
| Beitreten über Warten im Wartebereich                                           | Ja          |

| Anpassung der Benutzererfahrung                                       | Verfügbarkeit |
|---------------------------------------------------------------------|--------------|
| *Besprechungsaktionen*                                                   |              |
| Anzeigen der Anrufliste                                             | Ja          |
| Anpassen des Layouts: Farben, Symbole, Schaltflächen                        | Teilweise    |
| Anpassen der Anrufbildschirm-Symbole                                     | Ja          |
| Ändern des Layouts von Besprechungsansichten                                         | Ja          |
| Ändern des Layouts dynamischer NxN-Anrufe                                    | Ja          |
| Heben/Senken der Hand                                                     | Ja          |
| Stummschalten/Stummschaltung aufheben                                                        | Ja          |
| In Wartestellung setzen                                                         | Ja          |
| Auswählen des Audioroutings                                                | Ja          |
| Auswählen der Kamera                                                       | Ja          |
| Freigeben von Foto, Bildschirm und Video                                       | Ja          |
| Video starten/beenden                                                    | Ja          |
| Benutzerkachel Pressetermin                                               | Ja          |
| Namensschild Pressetermin                                              | Ja          |
| Verschwommener Hintergrund                                                     | Ja          |
| Anpassen der Bildschirmhintergrund-Farbe                               | Nein           |
| Anpassen der Farbe des oberen/unteren Balkens                                  | Nein           |
| Whiteboardfreigabe                                                  | Nein           |
| *Benutzererfahrung vor der Besprechung*                                            |              |
| Beim Beitreten kann der Anzeigename konfiguriert und die Fotofreigabe aktiviert werden.            | Ja          |
| Beim Beitreten kann konfiguriert werden, dass die Anfrufstagingansicht (Bildschirm vor dem Anruf) angezeigt wird.   | Ja          |
| Beim Beitreten kann konfiguriert werden, dass das Namensschild auf dem Anrufbildschirm angezeigt wird.             | Ja          |
| Anpassen des Wartebereichbildschirms                                          | Teilweise    |

Weitere Informationen zum Einstieg in das Teams Embed-SDK finden Sie im [Schnellstart: Hinzufügen von Funktionen zum Beitreten zu Teams-Besprechungen zu Ihrer App](../../quickstarts/meeting/getting-started-with-teams-embed.md). Weitere Informationen zu den SDK-Funktionen finden Sie im [Beispielleitfaden](../../quickstarts/meeting/samples-for-teams-embed.md).