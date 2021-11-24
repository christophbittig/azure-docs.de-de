---
title: Azure Communication Services Einblicke Vorschau
titleSuffix: An Azure Communication Services concept document
description: Beschreibungen der Datenvisualisierungen, die für Kommunikationsdienste über Workbooks verfügbar sind
author: timmitchell
services: azure-communication-services
ms.author: timmitchell
ms.date: 10/25/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: data
ms.openlocfilehash: 1a11cc68d197d53ab2348c7b6ae625c6659a6884
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132138784"
---
# <a name="communications-services-insights-preview"></a>Kommunikationsdienste Einblicke Vorschau

## <a name="overview"></a>Übersicht
Innerhalb Ihrer Kommunikationsressource haben wir eine **Insights-Vorschau**-Funktion bereitgestellt, die eine Reihe von Datenvisualisierungen anzeigt, die Einblicke aus den Azure Monitor-Protokollen und Metriken vermitteln, die für Ihre Kommunikationsdienste überwacht werden. Die Visualisierungen innerhalb von Insights werden durch [Azure Monitor Workbooks](/azure/azure-monitor/visualize/workbooks-overview) ermöglicht. Um die Vorteile von Workbooks zu nutzen, befolgen Sie die Anweisungen unter [Azure Monitor in Diagnoseeinstellungen aktivieren](enable-logging.md), und um Workbooks zu aktivieren, müssen Sie Ihre Protokolle an einen [Log Analytics-Arbeitsbereich](/azure/azure-monitor/logs/log-analytics-overview) senden. 

:::image type="content" source="media\workbooks\insights-overview-2.png" alt-text="Kommunikationsdienste Einblicke":::

## <a name="accessing-azure-insights-for-communication-services"></a>Zugriff auf Azure Insights für Kommunikationsdienste

1. Wählen Sie auf der Startseite des **Azure-Portals** Ihre **Kommunikationsdienst**-Ressource:

    :::image type="content" source="media\workbooks\azure-portal-home-browser.png" alt-text="Azure Portal Startseite":::

2. Sobald Sie sich in Ihrer Ressource befinden, scrollen Sie in der linken Navigationsleiste nach unten zur Kategorie **Überwachung** und klicken Sie auf die Registerkarte **Einblicke**:

    :::image type="content" source="media\workbooks\acs-insights-nav.png" alt-text="Einblicke Navigation":::

3. Dies sollte das **Insights** Dashboard für Ihre Kommunikationsdienstressource anzeigen:

    :::image type="content" source="media\workbooks\acs-insights-tab.png" alt-text="Registerkarte Einblicke in die Kommunikationsdienste":::

## <a name="insights-dashboard-navigation"></a>Einblicke Dashboard-Navigation

Die Dashboards von Communication Service Insights bieten den Nutzern eine intuitive und übersichtliche Möglichkeit zur Navigation in den Protokolldaten ihrer Ressourcen. Der Abschnitt **Übersicht** bietet einen Überblick über alle Modalitäten, so dass der Benutzer die verschiedenen Arten der Nutzung seiner Ressource in einem bestimmten Zeitraum sehen kann:

:::image type="content" source="media\workbooks\overview.png" alt-text="Übersicht zu Erkenntnissen":::

Mit den oben angezeigten Parametern können die Benutzer den anzuzeigenden Zeitbereich und die Zeitgranularität steuern:

:::image type="content" source="media\workbooks\time-range-param.png" alt-text="Zeitbereichsparameter":::

Diese Parameter sind global, d. h. sie aktualisieren die im gesamten Dashboard angezeigten Daten.

Der Abschnitt **Übersicht** enthält einen zusätzlichen Parameter zur Steuerung der Art der angezeigten Visualisierung:

:::image type="content" source="media\workbooks\plot-type-param.png" alt-text="Parameter der Darstellungsart":::

Dieser Parameter ist lokal, d. h. er wirkt sich nur auf die Diagramme in diesem Abschnitt aus.

Auf den übrigen Registerkarten werden Protokolldaten angezeigt, die sich auf eine bestimmte Modalität beziehen.

:::image type="content" source="media\workbooks\main-nav.png" alt-text="Hauptnavigation":::

Da **Sprach- und Videoprotokolle**  von Natur aus am komplexesten sind, wird diese Modalität in vier Unterabschnitte unterteilt:

:::image type="content" source="media\workbooks\voice-and-video-nav.png" alt-text="Sprach- und Videonavigation":::

Die Registerkarte **Zusammenfassung** enthält allgemeine Informationen über die Voice und Videonutzung, einschließlich der Arten der gemeinsam genutzten Medienströme, der an einem Anruf teilnehmenden Endpunkttypen (z. B. VoIP, Bot, Anwendung, PSTN oder Server), der OS-Nutzung und der Gründe für die Beendigung des Anrufs:

:::image type="content" source="media\workbooks\voice-and-video-summary.png" alt-text="Zusammenfassung in Wort und Video":::

Die Registerkarte **Volumen** unter der Modalität **Sprache und Video** zeigt die Anzahl der Anrufe und die Anzahl der Teilnehmer in einem bestimmten Zeitraum (Parameter **Zeitspanne**), unterteilt in Zeitabschnitte (Parameter **Zeitgranularität**):

:::image type="content" source="media\workbooks\voice-and-video-volume.png" alt-text="Sprach- und Videolautstärke":::

Die Registerkarte **Volumen** enthält einen Parameter **Gruppierung**, mit dessen Hilfe die Anzahl der Anrufe und Teilnehmer nach Anruftyp (P2P vs. Gruppenanrufe) und Interop-Anrufen (reine Azure Communication Services (ACS) vs. Teams Interop) segmentiert dargestellt werden kann:

:::image type="content" source="media\workbooks\voice-and-video-volume-grouping.png" alt-text="Sprach- und Videolautstärkegruppierung":::

Die Registerkarte **Qualität** unter **Sprach- und Videoanrufe** ermöglicht es dem Benutzer, die Qualitätsverteilung der Anrufe zu überprüfen, wobei die Qualität in diesem Dashboard in drei Stufen definiert ist:

- Der Anteil der Medienströme mit schlechter Qualität (**Stream quality** plot), wobei die Qualität eines Stroms als schlecht eingestuft wird, wenn er mindestens einen ungesunden Telemetriewert aufweist, wobei ungesunde Bereiche wie folgt definiert sind:
  - Jitter > 30 Millisekunden
  - Paketverlustrate > 10%
  - Rundlaufzeit > 500 Millisekunden

- Der Anteil der **beeinträchtigten Anrufe**, wobei ein beeinträchtigter Anruf definiert ist als ein Anruf mit mindestens einem Stream schlechter Qualität

- **Teilnehmerbeendigungsgründe**, die den Grund für das Verlassen eines Anrufs durch einen Teilnehmer festhalten. Endgründe sind [SIP-Codes](https://en.wikipedia.org/wiki/List_of_SIP_response_codes), bei denen es sich um numerische Codes handelt, die den spezifischen Status einer Signalisierungs Anforderung beschreiben. SIP-Codes können in sechs Kategorien eingeteilt werden: *Erfolg*, *Client-Fehler*, *Server-Fehler*, *Globaler Fehler*, *Umleitung* und *Provisorisch*. Die Verteilung der SIP-Code-Kategorien wird in dem Tortendiagramm auf der linken Seite dargestellt, während auf der rechten Seite eine Liste der spezifischen SIP-Codes für die Gründe für die Beendigung der Teilnahme zu finden ist

:::image type="content" source="media\workbooks\voice-and-video-quality.png" alt-text="Sprach- und Videoqualität":::

Die Qualität kann auch nach den im Anruf verwendeten Medientypen (Parameter **Medientyp**) gefiltert werden, um z. B. nur die Anrufe zu erhalten, die hinsichtlich der Qualität des Videostroms beeinträchtigt sind:

:::image type="content" source="media\workbooks\voice-and-video-quality-params.png" alt-text="Parameter des Medientyps Sprach- und Videoqualität":::

Und kann auch nach Endpunkttypen gefiltert werden (Parameter **Endpunkttyp**), z. B. um die Teilnehmerendgründe für PSTN-Teilnehmer zu erhalten. Diese Filter ermöglichen eine Mehrfachauswahl:

:::image type="content" source="media\workbooks\voice-and-video-params-2.png" alt-text="Parameter für den Endpunkttyp Sprach- und Videoqualität":::

Die Registerkarte **Details** bietet eine schnelle Möglichkeit, durch die in einem bestimmten Zeitraum getätigten **Sprach- und Videoanrufe** zu navigieren, indem Anrufe nach Datum gruppiert werden und die Details jedes getätigten Anrufs in Bezug auf die Teilnehmer dieses Anrufs und die ausgehenden Streams pro Teilnehmer zusammen mit der Dauer und den Telemetriewerten für diese angezeigt werden:

:::image type="content" source="media\workbooks\voice-and-video-details.png" alt-text="Sprach- und Videodetails":::

Die Details eines Anrufs sind zunächst verborgen. Nach dem Anklicken eines Anrufs wird eine Liste der Teilnehmer angezeigt:

:::image type="content" source="media\workbooks\voice-and-video-details-participants.png" alt-text="Details der Sprach- und Videoteilnehmer":::

Durch Anklicken eines Teilnehmers wird eine Liste der ausgehenden Streams dieses Teilnehmers angezeigt, zusammen mit ihrer Dauer (proportional zur gesamten Gesprächsdauer) und den Telemetriewerten, wobei ungesunde Werte in Rot angezeigt werden:

:::image type="content" source="media\workbooks\voice-and-video-details-streams.png" alt-text="Details zum Sprach- und Videostrom":::

Die Registerkarte **Authentifizierung** zeigt Authentifizierungsprotokolle, die durch Vorgänge wie das Ausstellen eines Zugriffstokens oder das Erstellen einer Identität erstellt werden. Zu den angezeigten Daten gehören die Art der durchgeführten Vorgänge und die Ergebnisse dieser Vorgänge:

:::image type="content" source="media\workbooks\auth.png" alt-text="Registerkarte &quot;Authentifizierung&quot; ":::

Auf der Registerkarte **Chat** werden die Daten für alle chatbezogenen Vorgänge und ihre Ergebnistypen angezeigt:

:::image type="content" source="media\workbooks\chat.png" alt-text="Registerkarte Chat":::

Auf der Registerkarte **SMS** werden die Vorgänge und Ergebnisse für die SMS-Nutzung durch eine ACS-Ressource angezeigt (wir haben derzeit keine Daten für diese Modalität):

:::image type="content" source="media\workbooks\sms.png" alt-text="SMS-Registerkarte":::

## <a name="editing-dashboards"></a>Editieren von Dashboards

Die **Insights**-Dashboards, die mit Ihrer **Kommunikationsdienst**-Ressource geliefert werden, können durch Klicken auf die Schaltfläche **Bearbeiten** in der oberen Navigationsleiste angepasst werden:

:::image type="content" source="media\workbooks\dashboard-editing.png" alt-text="Bildschirmbearbeitung":::

Bei der Bearbeitung dieser Dashboards wird die Registerkarte **Einsichten** nicht geändert, sondern eine separate Arbeitsmappe erstellt, auf die Sie über die Registerkarte Arbeitsmappen Ihrer Ressource zugreifen können:

:::image type="content" source="media\workbooks\workbooks-tab.png" alt-text="Registerkarte Arbeitsbücher":::

Eine ausführliche Beschreibung der Arbeitsmappen finden Sie in der [Azure Monitor Arbeitsmappen](/azure/azure-monitor/visualize/workbooks-overview) Dokumentation.