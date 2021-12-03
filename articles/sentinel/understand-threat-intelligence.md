---
title: Grundlegendes zu Threat Intelligence in Microsoft Sentinel | Microsoft-Dokumentation
description: Erfahren Sie, wie Threat Intelligence-Feeds mit Microsoft Sentinel verbunden, verwaltet und verwendet werden, um Daten zu analysieren, Bedrohungen zu erkennen und Warnungen anzureichern.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 09835e201417040d3f8db6fd2387f325b2728e9d
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132516948"
---
# <a name="understand-threat-intelligence-in-microsoft-sentinel"></a>Grundlegendes zu Threat Intelligence in Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

## <a name="introduction-to-threat-intelligence"></a>Einführung in Threat Intelligence

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Cyber Threat Intelligence (CTI) umfasst Informationen, die bekannte vorhandene oder potenzielle Bedrohungen für Systeme und Benutzer beschreiben. Diese Art von Informationen kann viele Formen annehmen – von schriftlichen Berichten, in denen die Motivationen, die Infrastruktur und die Techniken eines bestimmten mit einer Bedrohung verbundenen Akteurs beschrieben werden, bis zu spezifischen Beobachtungen von IP-Adressen, Domänen, Dateihashes und anderen Artefakten, die mit bekannten Cyberbedrohungen in Verbindung stehen. CTI wird von Organisationen verwendet, um wichtige Kontextinformationen für ungewöhnliche Aktivitäten bereitzustellen, damit Sicherheitsverantwortliche schnell Maßnahmen zum Schutz ihrer Kollegen, Informationen und anderer Ressourcen ergreifen können. CTI kann aus vielen Quellen stammen, z. B. aus Open-Source-Datenfeeds, Communitys zum Teilen von Threat Intelligence, kostenpflichtigen Intelligence-Feeds und Sicherheitsuntersuchungen mit lokalen Informationen in Unternehmen.

Innerhalb einer SIEM-Lösung (Security Information & Event Management) wie Microsoft Sentinel kommt CTI am häufigsten in Form von Bedrohungsindikatoren zum Einsatz, die auch als Anzeichen einer Kompromittierung (Indicator of Compromise, IoC) bezeichnet werden. Bedrohungsindikatoren sind Daten, die beobachtete Artefakte wie URLs, Dateihashes oder IP-Adressen bekannten Bedrohungsaktivitäten wie Phishing, Botnets oder Schadsoftware zuordnen. Diese Form von Threat Intelligence wird oft auch als ta *ktische Threat Intelligence* bezeichnet, da sie in großem Umfang auf Sicherheitsprodukte und Automatisierung angewendet werden kann, um potenzielle Bedrohungen für eine Organisation zu erkennen und sich vor ihnen zu schützen. In Microsoft Sentinel können Sie Bedrohungsindikatoren verwenden, um bösartige Aktivitäten zu erkennen, die in Ihrer Umgebung beobachtet wurden, und um Sicherheitsexperten Kontext bereitzustellen, um bei Entscheidungen über die jeweilige Reaktion zu helfen.

Integrieren Sie Threat Intelligence (TI) mithilfe der folgenden Aktivitäten in Microsoft Sentinel:

- **Importieren Sie Threat Intelligence-Daten** in Microsoft Sentinel, indem Sie **Datenconnectors** für verschiedene TI-[Plattformen](connect-threat-intelligence-tip.md) und -[Feeds](connect-threat-intelligence-taxii.md) aktivieren.

- Sie können die importierten Threat Intelligence-Daten in **Protokollen** und auf dem Blatt **Threat Intelligence** von Microsoft Sentinel **anzeigen und verwalten**.

- **Erkennen Sie Bedrohungen**, und generieren Sie Sicherheitswarnungen und Incidents mit den integrierten **Analytics**-Regelvorlagen, die auf Ihren importierten Threat Intelligence-Daten basieren.

- **Visualisieren Sie wichtige Informationen** zu Ihren Threat Intelligence-Daten in Microsoft Sentinel mithilfe der **Threat Intelligence-Arbeitsmappe**.

Microsoft erweitert alle importierten Threat Intelligence-Indikatoren mit [GeoLocation- und WhoIs-Daten](#view-your-geolocation-and-whois-data-enrichments-public-preview), die zusammen mit anderen Indikatordetails angezeigt werden.

> [!TIP]
> Threat Intelligence bietet zudem nützlichen Kontext in anderen Microsoft Sentinel Features wie etwa **Hunting** und **Notebooks**. Weitere Informationen finden Sie unter [Jupyter Notebooks in Microsoft Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/using-threat-intelligence-in-your-jupyter-notebooks/ba-p/860239) und [Tutorial: Erste Schritte mit Jupyter Notebooks und MSTICPy in Microsoft Sentinel](notebook-get-started.md).
>

## <a name="import-threat-intelligence-with-data-connectors"></a>Importieren von Threat Intelligence-Daten mit Datenconnectors

Microsoft Sentinel importiert Bedrohungsindikatoren wie auch alle anderen Ereignisdaten mithilfe von Datenconnectors. Es stehen zwei Microsoft Sentinel-Datenconnectors speziell für Bedrohungsindikatoren zur Verfügung: **Threat Intelligence – TAXII** für STIX/TAXII-Feeds gemäß branchenüblicher Standards und **Threat Intelligence-Plattformen** für integrierte und zusammengestellte TI-Feeds. Je nachdem, woher Ihre Organisation Daten zu Bedrohungsindikatoren bezieht, können Sie einen oder beide Datenconnectors verwenden.

Weitere Informationen finden Sie in diesem Katalog der [Threat Intelligence-Integrationen](threat-intelligence-integration.md), die mit Microsoft Sentinel verfügbar sind.

### <a name="add-threat-indicators-to-microsoft-sentinel-with-the-threat-intelligence-platforms-data-connector"></a>Hinzufügen von Bedrohungsindikatoren in Microsoft Sentinel mit dem Datenconnector „Threat Intelligence-Plattformen“

Viele Organisationen nutzen TIP-Lösungen (Threat Intelligence Platform), um Feeds mit Bedrohungsindikatoren aus mehreren Quellen zu aggregieren, die Daten innerhalb der Plattform zusammenzustellen und dann auszuwählen, welche Bedrohungsindikatoren auf die verschiedene Sicherheitslösungen wie Netzwerkgeräte, EDR/XDR-Lösungen oder SIEM-Lösungen wie Microsoft Sentinel angewendet werden sollen. Wenn Ihre Organisation eine [integrierte TIP-Lösung](connect-threat-intelligence-tip.md) verwendet, können Sie mit dem **Datenconnector für Threat Intelligence-Plattformen** Ihre TIP-Lösung zum Importieren von Bedrohungsindikatoren in Microsoft Sentinel verwenden.

Da der TIP-Datenconnector zu diesem Zweck mit der [tiIndicators-API von Microsoft Intelligent Security Graph](/graph/api/resources/tiindicator) arbeitet, kann er auch von jeder benutzerdefinierten Threat Intelligence Plattform verwendet werden, die mit der tiIndicators-API kommuniziert, um Indikatoren an Microsoft Sentinel (und an andere Microsoft-Sicherheitslösungen wie Microsoft 365 Defender) zu übermitteln.

:::image type="content" source="media/understand-threat-intelligence/threat-intel-import-path.png" alt-text="Importpfad für Threat Intelligence":::

Weitere Informationen zu den TIP-Lösungen, die in Microsoft Sentinel integriert sind, finden Sie unter [Produkte der integrierten Threat Intelligence-Plattform](threat-intelligence-integration.md#integrated-threat-intelligence-platform-products).

**So importieren Sie Bedrohungsindikatoren in Microsoft Sentinel aus Ihrer integrierten TIP- oder benutzerdefinierten Threat Intelligence-Plattform**:

1. Abrufen einer **Anwendungs-ID** und eines **geheimen Clientschlüssels** aus Azure Active Directory

1. Einfügen dieser Informationen in Ihre TIP-Lösung oder benutzerdefinierte Anwendung

1. Aktivieren des Datenconnectors „Threat Intelligence-Plattformen“ in Microsoft Sentinel

Siehe auch: [Verbinden Ihrer Threat Intelligence-Plattform (TP) mit Microsoft Sentinel](connect-threat-intelligence-tip.md).

### <a name="add-threat-indicators-to-microsoft-sentinel-with-the-threat-intelligence---taxii-data-connector"></a>Fügen Sie Bedrohungsindikatoren in Microsoft Sentinel mit dem Datenconnector Threat Intelligence – TAXII hinzu

Der am weitesten verbreitete Industriestandard für die Übertragung von Threat Intelligence ist eine [Kombination aus dem STIX-Datenformat und dem TAXII-Protokoll](https://oasis-open.github.io/cti-documentation/). Wenn Ihre Organisation die Bedrohungsindikatoren aus Lösungen erhält, die die aktuelle STIX/TAXII-Version (2.0 oder 2.1) unterstützen, können Sie Ihre Bedrohungsindikatoren mit dem Datenconnector **Threat Intelligence – TAXII** in Microsoft Sentinel importieren. Der Datenconnector „Threat Intelligence – TAXII“ ermöglicht einem integrierten TAXII-Client in Microsoft Sentinel das Importieren von Threat Intelligence von TAXII 2.x-Servern.

:::image type="content" source="media/understand-threat-intelligence/threat-intel-taxii-import-path.png" alt-text="TAXII-Importpfad":::

**So importieren Sie Bedrohungsindikatoren im STIX-Format in Microsoft Sentinel von einem TAXII-Server**:

1. Abrufen der Stamm- und Sammlungs-ID der TAXII-Server-API

1. Aktivieren des TAXII-Datenconnectors für Threat Intelligence in Microsoft Sentinel

Weitere Informationen finden Sie unter [Microsoft Sentinel mit STIX/TAXII Threat Intelligence-Feeds Verknüpfen](connect-threat-intelligence-taxii.md).

## <a name="view-and-manage-your-threat-indicators"></a>Anzeigen und Verwalten von Bedrohungsindikatoren

Sie können Ihre erfolgreich importierten Bedrohungsindikatoren unabhängig von der Feedquelle oder dem verwendeten Connector in der Tabelle **ThreatIntelligenceIndicator** (unter der Gruppe **Microsoft Sentinel**) in **Protokolle** einsehen, in der alle Ihre Microsoft Sentinel-Ereignisdaten gespeichert sind. Diese Tabelle bildet die Grundlage für Threat Intelligence-Abfragen, die von anderen Microsoft Sentinel-Features wie Analytics und Arbeitsmappen ausgeführt werden.

Die Ergebnisse sollten den Beispielbedrohungsindikatoren unten ähneln:

:::image type="content" source="media/understand-threat-intelligence/threat-intel-sample-query.png" alt-text="Beispielabfragedaten":::

Sie können Ihre Indikatoren auch auf dem neuen Blatt **Threat Intelligence** einsehen und verwalten, auf das Sie über das Hauptmenü von Microsoft Sentinel Zugriff haben. Sie können Ihre importierten Bedrohungsindikatoren sortieren, filtern und durchsuchen, ohne eine Log Analytics-Abfrage schreiben zu müssen. Mit diesem Feature können Sie auch direkt auf der Microsoft Sentinel-Benutzeroberfläche Bedrohungsindikatoren erstellen und zwei der gängigsten Verwaltungsaufgaben für Threat Intelligence ausführen: Markieren von Indikatoren mit Tags und Erstellen neuer Indikatoren im Zusammenhang mit Sicherheitsuntersuchungen.

Das Markieren von Bedrohungsindikatoren mit Tags stellt eine einfache Möglichkeit dar, sie zu gruppieren und einfacher auffindbar zu machen. In der Regel können Sie ein Tag auf Indikatoren im Zusammenhang mit einem bestimmten Incident anwenden oder auf Indikatoren, die Bedrohungen von einem bestimmten bekannten Akteur oder einer bekannten Angriffskampagne darstellen. Sie können die Bedrohungsindikatoren einzeln markieren, oder Sie können mehrere Indikatoren auswählen und diese gleichzeitig markieren. Unten finden Sie ein Beispiel für das Markieren mehrerer Indikatoren mit einer Incident-ID. Da das Tagging eine Freiformangabe ist, empfiehlt es sich, Standardnamenskonventionen für Tags für Bedrohungsindikatoren zu erstellen. Sie können mehrere Tags auf jeden Indikator anwenden.

:::image type="content" source="media/understand-threat-intelligence/threat-intel-tagging-indicators.png" alt-text="Anwenden von Tags auf Bedrohungsindikatoren" lightbox="media/understand-threat-intelligence/threat-intel-tagging-indicators.png":::

Weitere Informationen zum Anzeigen und Verwalten von Bedrohungsindikatoren finden Sie unter [Arbeiten mit Bedrohungsindikatoren in Microsoft Sentinel](work-with-threat-indicators.md#view-your-threat-indicators-in-microsoft-sentinel).

### <a name="view-your-geolocation-and-whois-data-enrichments-public-preview"></a>Anzeigen von GeoLocation- und WhoIs-Datenanreicherungen (öffentliche Vorschau)

Microsoft erweitert jeden Indikator mit zusätzlichen GeoLocation- und WhoIs-Daten und bietet mehr Kontext für Untersuchungen, bei denen der ausgewählte Indikator für die Kompromittierung (IOC) gefunden wird.

Sie können geoLocation- und WhoIs-Daten im **Threat Intelligence-Bereich** für jeden Indikator der Kompromittierung anzeigen, den Sie in Microsoft Sentinel importiert haben.

Verwenden Sie beispielsweise GeoLocation-Daten, um Details wie *Organisation* oder *Land* für den Indikator zu finden, und WhoIs-Daten, um Daten wie *Registrierungs-* und *Datensatzerstellungsdaten* zu finden.

## <a name="detect-threats-with-threat-indicator-based-analytics"></a>Erkennen von Bedrohungen mit auf Bedrohungsindikatoren basierenden Analysen

Der wichtigste Anwendungsfall für Bedrohungsindikatoren in SIEM-Lösungen wie Microsoft Sentinel ist die Unterstützung von Analyseregeln zur Bedrohungserkennung. Diese indikatorbasierten Regeln vergleichen unformatierte Ereignisse aus Ihren Datenquellen mit Ihren Bedrohungsindikatoren, um Sicherheitsbedrohungen in Ihrer Organisation zu erkennen. Sie erstellen in Microsoft Sentinel **Analytics** Analyseregeln, die gemäß Zeitplan ausgeführt werden und Sicherheitswarnungen generieren. Die Regeln werden durch Abfragen gesteuert, zusammen mit Konfigurationen, die festlegen, wie oft die Regel ausgeführt werden soll, welche Art von Abfrageergebnissen Sicherheitswarnungen und Incidents generieren soll und welche Automatisierungen, sofern vorgesehen, als Reaktion ausgelöst werden sollen.

Sie können stets neue Analyseregeln erstellen. Microsoft Sentinel bietet aber auch eine Reihe von integrierten Regelvorlagen, die von Microsoft-Sicherheitstechnikern erstellt wurden und die Sie unverändert verwenden oder entsprechend den jeweiligen Anforderungen anpassen können. Sie können Regelvorlagen, die Bedrohungsindikatoren verwenden, ganz einfach identifizieren, da ihr Name immer mit „**TI map**…“ beginnt. Alle diese Regelvorlagen funktionieren ähnlich, wobei der einzige Unterschied darin besteht, welche Art von Bedrohungsindikatoren verwendet wird (Domäne, E-Mail, Dateihash, IP-Adresse oder URL) und mit welchem Ereignistyp ein Abgleich erfolgen soll. Jede Vorlage listet die Datenquellen auf, die für das Funktionieren der Regel erforderlich sind, sodass Sie auf einen Blick erkennen können, ob Sie die nötigen Ereignisse bereits in Microsoft Sentinel importiert haben. Wenn Sie eine vorhandene Regelvorlage bearbeiten und speichern oder eine neue Regel erstellen, ist sie standardmäßig aktiviert.

Sie finden die aktivierte Regel auf der Registerkarte **Aktive Regeln** im Abschnitt **Analytics** von Microsoft Sentinel. Sie können die aktive Regel an dieser Stelle bearbeiten, aktivieren, deaktivieren, duplizieren oder löschen. Die neue Regel wird sofort nach der Aktivierung ausgeführt, und ab diesem Zeitpunkt nach dem festgelegten Zeitplan ausgeführt.

Gemäß den Standardeinstellungen wird jedes Mal, wenn die Regel nach dem Zeitplan ausgeführt wird und Ergebnisse gefunden werden, eine Sicherheitswarnung generiert. Sicherheitswarnungen in Microsoft Sentinel können im Abschnitt **Protokolle** von Microsoft Sentinel in der Tabelle **SecurityAlert** unter der Gruppe **Microsoft Sentinel** angezeigt werden.

In Microsoft Sentinel generieren Warnungen, die nach Analyseregeln generiert wurden, auch Sicherheitsincidents, die Sie in **Incidents** unter **Bedrohungsmanagement** im Microsoft Sentinel-Menü finden. Teams für Sicherheitsvorgänge können die Incidents selektieren und untersuchen, um angemessene Reaktionen zu ermitteln. Ausführliche Informationen finden Sie in diesem [Tutorial: Untersuchen von Vorfällen mit Microsoft Sentinel](./investigate-cases.md).

Weitere Informationen zum Verwenden von Bedrohungsindikatoren in Analyseregeln finden Sie unter [Arbeiten mit Bedrohungsindikatoren in Microsoft Sentinel](work-with-threat-indicators.md#detect-threats-with-threat-indicator-based-analytics).

## <a name="workbooks-provide-insights-about-your-threat-intelligence"></a>Arbeitsmappen für Erkenntnisse aus Threat Intelligence

Workbooks bieten leistungsstarke interaktive Dashboards, die Ihnen Erkenntnisse zu allen Aspekten von Microsoft Sentinel liefern, so auch zu Threat Intelligence. Sie können die integrierte **Azure Sentinel-Arbeitsmappe** verwenden, um wichtige Informationen über Ihre Threat Intelligence-Daten zu visualisieren. Die Arbeitsmappe lässt sich problemlos an Ihre geschäftlichen Anforderungen anpassen. Sie können sogar neue Dashboards erstellen, die viele verschiedene Datenquellen kombinieren, damit Sie Ihre Daten auf besondere Weisen visualisieren können. Da Microsoft Sentinel-Arbeitsmappen auf Azure Monitor-Arbeitsmappen basieren, sind eine umfassende Dokumentation und viele weitere Vorlagen verfügbar. Einen hervorragenden Einstieg bietet der Artikel zum [Erstellen interaktiver Berichte mit Azure Monitor-Arbeitsmappen](../azure-monitor/visualize/workbooks-overview.md).

Es gibt auch eine riesige Community für [Azure Monitor-Arbeitsmappen auf GitHub](https://github.com/microsoft/Application-Insights-Workbooks), in der Sie weitere Vorlagen herunterladen und eigene Vorlagen veröffentlichen können.

Weitere Informationen zur Verwendung und Anpassung der Threat Intelligence-Arbeitsmappe finden Sie unter [Arbeiten mit Bedrohungsindikatoren in Microsoft Sentinel](work-with-threat-indicators.md#workbooks-provide-insights-about-your-threat-intelligence).

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie etwas über die Threat Intelligence-Funktionen von Microsoft Sentinel sowie das Blatt „Threat Intelligence“ erfahren. Eine praktische Anleitung zur Verwendung der Threat Intelligence-Funktionen von Microsoft Sentinel finden Sie in den folgenden Artikeln:

- Herstellen einer Verbindung zwischen Microsoft Sentinel und [STIX-/TAXII-Threat-Intelligence-Feeds](./connect-threat-intelligence-taxii.md)
- [Verbinden von Threat Intelligence-Plattformen](./connect-threat-intelligence-tip.md) mit Microsoft Sentinel
- [Integrieren von TIP-Plattformen, TAXII-Feeds und Anreicherungen](threat-intelligence-integration.md) in Microsoft Sentinel
- [Arbeiten mit Bedrohungsindikatoren](work-with-threat-indicators.md) in der Microsoft Sentinel-Umgebung
- Erkennen von Bedrohungen mit [integrierten](./detect-threats-built-in.md) oder [benutzerdefinierten](./detect-threats-custom.md) Analyseregeln in Microsoft Sentinel
- [Untersuchen von Incidents](./investigate-cases.md) mit Microsoft Sentinel
