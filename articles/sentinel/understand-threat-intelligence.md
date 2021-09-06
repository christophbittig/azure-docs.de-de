---
title: Grundlegendes zu Threat Intelligence in Azure Sentinel | Microsoft-Dokumentation
description: Erfahren Sie, wie Threat Intelligence-Feeds mit Azure Sentinel verbunden, verwaltet und verwendet werden, um Daten zu analysieren, Bedrohungen zu erkennen und Warnungen anzureichern.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2021
ms.author: yelevin
ms.openlocfilehash: 6ab9ecbe3b67ec933604ab1d8f6efdc7dbd8a5af
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339579"
---
# <a name="understand-threat-intelligence-in-azure-sentinel"></a>Grundlegendes zu Threat Intelligence in Azure Sentinel

## <a name="introduction-to-threat-intelligence"></a>Einführung in Threat Intelligence

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Cyber Threat Intelligence (CTI) umfasst Informationen, die bekannte vorhandene oder potenzielle Bedrohungen für Systeme und Benutzer beschreiben. Diese Art von Informationen kann viele Formen annehmen – von schriftlichen Berichten, in denen die Motivationen, die Infrastruktur und die Techniken eines bestimmten mit einer Bedrohung verbundenen Akteurs beschrieben werden, bis zu spezifischen Beobachtungen von IP-Adressen, Domänen, Dateihashes und anderen Artefakten, die mit bekannten Cyberbedrohungen in Verbindung stehen. CTI wird von Organisationen verwendet, um wichtige Kontextinformationen für ungewöhnliche Aktivitäten bereitzustellen, damit Sicherheitsverantwortliche schnell Maßnahmen zum Schutz ihrer Kollegen, Informationen und anderer Ressourcen ergreifen können. CTI kann aus vielen Quellen stammen, z. B. aus Open-Source-Datenfeeds, Communitys zum Teilen von Threat Intelligence, kostenpflichtigen Intelligence-Feeds und Sicherheitsuntersuchungen mit lokalen Informationen in Unternehmen.

Innerhalb einer SIEM-Lösung (Security Information & Event Management) wie Azure Sentinel kommt CTI am häufigsten in Form von Bedrohungsindikatoren zum Einsatz, die auch als Anzeichen einer Kompromittierung (Indicator of Compromise, IoC) bezeichnet werden. Bedrohungsindikatoren sind Daten, die beobachtete Artefakte wie URLs, Dateihashes oder IP-Adressen bekannten Bedrohungsaktivitäten wie Phishing, Botnets oder Schadsoftware zuordnen. Diese Form von Threat Intelligence wird oft auch als taktische Threat Intelligence bezeichnet, da sie in großem Umfang auf Sicherheitsprodukte und Automatisierung angewendet werden kann, um potenzielle Bedrohungen für eine Organisation zu erkennen und sich vor ihnen zu schützen. In Azure Sentinel können Sie Bedrohungsindikatoren verwenden, um schädliche Aktivitäten in Ihrer Umgebung zu erkennen und den Sicherheitsanalysten Kontextinformationen bereitzustellen, damit sie fundierte Entscheidungen für eine Reaktion treffen können.

Sie können Threat Intelligence (TI) mithilfe der folgenden Aktivitäten in Azure Sentinel integrieren:

- **Importieren Sie Threat Intelligence-Daten** in Azure Sentinel, indem Sie **Datenconnectors** für verschiedene TI-[Plattformen](connect-threat-intelligence-tip.md) und -[Feeds](connect-threat-intelligence-taxii.md) aktivieren.
- Sie können die importierten Threat Intelligence-Daten in **Protokollen** und auf dem Blatt **Threat Intelligence** von Azure Sentinel **anzeigen und verwalten**.
- **Erkennen Sie Bedrohungen**, und generieren Sie Sicherheitswarnungen und Incidents mit den integrierten **Analytics**-Regelvorlagen, die auf Ihren importierten Threat Intelligence-Daten basieren.
- **Visualisieren Sie wichtige Informationen** zu Ihren Threat Intelligence-Daten in Azure Sentinel mithilfe der **Threat Intelligence-Arbeitsmappe**.

Threat Intelligence bietet auch einen nützlichen Kontext für andere Azure Sentinel-Umgebungen wie **Hunting** und **Notebooks**. Diese werden zwar in diesem Artikel nicht behandelt, aber [in diesem Blogbeitrag von Ian Hellen zu Jupyter Notebooks in Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/using-threat-intelligence-in-your-jupyter-notebooks/ba-p/860239) wird die Nutzung von CTI in Notebooks ausgezeichnet beschrieben.

## <a name="import-threat-intelligence-with-data-connectors"></a>Importieren von Threat Intelligence-Daten mit Datenconnectors

Azure Sentinel importiert Bedrohungsindikatoren wie auch alle anderen Ereignisdaten mithilfe von Datenconnectors. Es stehen zwei Azure Sentinel-Datenconnectors speziell für Bedrohungsindikatoren zur Verfügung: **Threat Intelligence – TAXII** für STIX/TAXII-Feeds gemäß branchenüblicher Standards und **Threat Intelligence-Plattformen** für integrierte und zusammengestellte TI-Feeds. Je nachdem, woher Ihre Organisation Daten zu Bedrohungsindikatoren bezieht, können Sie einen oder beide Datenconnectors verwenden. 

Weitere Informationen finden Sie in diesem Katalog der [Threat Intelligence-Integrationen](threat-intelligence-integration.md), die mit Azure Sentinel verfügbar sind.

### <a name="adding-threat-indicators-to-azure-sentinel-with-the-threat-intelligence-platforms-data-connector"></a>Hinzufügen von Bedrohungsindikatoren in Azure Sentinel mit dem Datenconnector „Threat Intelligence-Plattformen“

Viele Organisationen nutzen TIP-Lösungen (Threat Intelligence Platform), um Feeds mit Bedrohungsindikatoren aus mehreren Quellen zu aggregieren, die Daten innerhalb der Plattform zusammenzustellen und dann auszuwählen, welche Bedrohungsindikatoren auf die verschiedene Sicherheitslösungen wie Netzwerkgeräte, EDR/XDR-Lösungen oder SIEM-Lösungen wie Azure Sentinel angewendet werden sollen. Wenn Ihre Organisation eine [integrierte TIP-Lösung](connect-threat-intelligence-tip.md) verwendet, können Sie mit dem **Datenconnector für Threat Intelligence-Plattformen** Ihre TIP-Lösung zum Importieren von Bedrohungsindikatoren in Azure Sentinel verwenden. 

Da der TIP-Datenconnector zu diesem Zweck mit der [tiIndicators-API von Microsoft Intelligent Security Graph](/graph/api/resources/tiindicator) arbeitet, kann er auch von jeder benutzerdefinierten Threat Intelligence Plattform verwendet werden, die mit der tiIndicators-API kommuniziert, um Indikatoren an Azure Sentinel (und an andere Microsoft-Sicherheitslösungen wie Microsoft 365 Defender) zu übermitteln.

:::image type="content" source="media/understand-threat-intelligence/threat-intel-import-path.png" alt-text="Importpfad für Threat Intelligence":::

Weitere Informationen zu den TIP-Lösungen, die in Azure Sentinel integriert sind, finden Sie unter [Produkte der integrierten Threat Intelligence-Plattform](threat-intelligence-integration.md#integrated-threat-intelligence-platform-products).

Dies sind die wichtigsten Schritte, die Sie ausführen müssen, um Bedrohungsindikatoren aus Ihrer integrierten TIP- oder benutzerdefinierten Threat Intelligence-Lösung in Azure Sentinel zu importieren:

1. Abrufen einer **Anwendungs-ID** und eines **geheimen Clientschlüssels** aus Azure Active Directory

1. Einfügen dieser Informationen in Ihre TIP-Lösung oder benutzerdefinierte Anwendung

1. Aktivieren des Datenconnectors „Threat Intelligence-Plattformen“ in Azure Sentinel

Einen detaillierten Überblick über diese Schritte finden Sie unter [Verbinden Ihrer Threat Intelligence-Plattform mit Azure Sentinel](connect-threat-intelligence-tip.md).


### <a name="adding-threat-indicators-to-azure-sentinel-with-the-threat-intelligence---taxii-data-connector"></a>Hinzufügen von Bedrohungsindikatoren in Azure Sentinel mit dem Datenconnector „Threat Intelligence – TAXII“

Der am weitesten verbreitete Industriestandard für die Übertragung von Threat Intelligence ist eine [Kombination aus dem STIX-Datenformat und dem TAXII-Protokoll](https://oasis-open.github.io/cti-documentation/). Wenn Ihre Organisation die Bedrohungsindikatoren aus Lösungen erhält, die die aktuelle STIX/TAXII-Version (2.0 oder 2.1) unterstützen, können Sie Ihre Bedrohungsindikatoren mit dem Datenconnector **Threat Intelligence – TAXII** in Azure Sentinel importieren. Der Datenconnector „Threat Intelligence – TAXII“ ermöglicht einem integrierten TAXII-Client in Azure Sentinel das Importieren von Threat Intelligence von TAXII 2.x-Servern.

:::image type="content" source="media/understand-threat-intelligence/threat-intel-taxii-import-path.png" alt-text="TAXII-Importpfad":::
 
Gehen Sie folgendermaßen vor, um von einem TAXII-Server Bedrohungsindikatoren im STIX-Format in Azure Sentinel zu importieren:

1. Abrufen der Stamm- und Sammlungs-ID der TAXII-Server-API

1. Aktivieren des Datenconnectors „Threat Intelligence – TAXII“ in Azure Sentinel

Einen detaillierten Überblick über diese Schritte finden Sie unter [Verbinden von Azure Sentinel mit STIX/TAXII-Threat Intelligence-Feeds](connect-threat-intelligence-taxii.md).

## <a name="view-and-manage-your-threat-indicators"></a>Anzeigen und Verwalten von Bedrohungsindikatoren

Sie können Ihre erfolgreich importierten Bedrohungsindikatoren unabhängig von der Feedquelle oder dem verwendeten Connector in der Tabelle **ThreatIntelligenceIndicator** (unter der Gruppe **Azure Sentinel**) in **Protokollen** einsehen, in der alle Ihre Azure Sentinel-Ereignisdaten gespeichert sind. Diese Tabelle bildet die Grundlage für Threat Intelligence-Abfragen, die von anderen Azure Sentinel-Features wie Analytics und Arbeitsmappen ausgeführt werden.

Die Ergebnisse sollten den Beispielbedrohungsindikatoren unten ähneln:

:::image type="content" source="media/understand-threat-intelligence/threat-intel-sample-query.png" alt-text="Beispielabfragedaten":::

Sie können Ihre Indikatoren auch auf dem neuen Blatt **Threat Intelligence** einsehen und verwalten, auf das Sie über das Hauptmenü von Azure Sentinel Zugriff haben. Sie können Ihre importierten Bedrohungsindikatoren sortieren, filtern und durchsuchen, ohne eine Log Analytics-Abfrage schreiben zu müssen. Mit diesem Feature können Sie auch direkt auf der Azure Sentinel-Benutzeroberfläche Bedrohungsindikatoren erstellen und zwei der gängigsten Verwaltungsaufgaben für Threat Intelligence ausführen: Markieren von Indikatoren mit Tags und Erstellen neuer Indikatoren im Zusammenhang mit Sicherheitsuntersuchungen.

Das Markieren von Bedrohungsindikatoren mit Tags stellt eine einfache Möglichkeit dar, sie zu gruppieren und einfacher auffindbar zu machen. In der Regel können Sie ein Tag auf Indikatoren im Zusammenhang mit einem bestimmten Incident anwenden oder auf Indikatoren, die Bedrohungen von einem bestimmten bekannten Akteur oder einer bekannten Angriffskampagne darstellen. Sie können die Bedrohungsindikatoren einzeln markieren, oder Sie können mehrere Indikatoren auswählen und diese gleichzeitig markieren. Unten finden Sie ein Beispiel für das Markieren mehrerer Indikatoren mit einer Incident-ID. Da das Tagging eine Freiformangabe ist, empfiehlt es sich, Standardnamenskonventionen für Tags für Bedrohungsindikatoren zu erstellen. Sie können mehrere Tags auf jeden Indikator anwenden.

:::image type="content" source="media/understand-threat-intelligence/threat-intel-tagging-indicators.png" alt-text="Anwenden von Tags auf Bedrohungsindikatoren" lightbox="media/understand-threat-intelligence/threat-intel-tagging-indicators.png":::

Weitere Informationen zum Anzeigen und Verwalten von Bedrohungsindikatoren finden Sie unter [Arbeiten mit Bedrohungsindikatoren in Azure Sentinel](work-with-threat-indicators.md#view-your-threat-indicators-in-azure-sentinel).

## <a name="detect-threats-with-threat-indicator-based-analytics"></a>Erkennen von Bedrohungen mit auf Bedrohungsindikatoren basierenden Analysen

Der wichtigste Anwendungsfall für Bedrohungsindikatoren in SIEM-Lösungen wie Azure Sentinel ist die Unterstützung von Analyseregeln zur Bedrohungserkennung. Diese indikatorbasierten Regeln vergleichen unformatierte Ereignisse aus Ihren Datenquellen mit Ihren Bedrohungsindikatoren, um Sicherheitsbedrohungen in Ihrer Organisation zu erkennen. Sie erstellen in Azure Sentinel **Analytics** Analyseregeln, die gemäß Zeitplan ausgeführt werden und Sicherheitswarnungen generieren. Die Regeln werden durch Abfragen gesteuert, zusammen mit Konfigurationen, die festlegen, wie oft die Regel ausgeführt werden soll, welche Art von Abfrageergebnissen Sicherheitswarnungen und Incidents generieren soll und welche Automatisierungen, sofern vorgesehen, als Reaktion ausgelöst werden sollen.

Sie können stets neue Analyseregeln erstellen. Azure Sentinel bietet aber auch eine Reihe von integrierten Regelvorlagen, die von Microsoft-Sicherheitstechnikern erstellt wurden und die Sie unverändert verwenden oder entsprechend den jeweiligen Anforderungen anpassen können. Sie können Regelvorlagen, die Bedrohungsindikatoren verwenden, ganz einfach identifizieren, da ihr Name immer mit „**TI map**…“ beginnt. Alle diese Regelvorlagen funktionieren ähnlich, wobei der einzige Unterschied darin besteht, welche Art von Bedrohungsindikatoren verwendet wird (Domäne, E-Mail, Dateihash, IP-Adresse oder URL) und mit welchem Ereignistyp ein Abgleich erfolgen soll. Jede Vorlage listet die Datenquellen auf, die für das Funktionieren der Regel erforderlich sind, sodass Sie auf einen Blick erkennen können, ob Sie die nötigen Ereignisse bereits in Azure Sentinel importiert haben. Wenn Sie eine vorhandene Regelvorlage bearbeiten und speichern oder eine neue Regel erstellen, ist sie standardmäßig aktiviert.

Sie finden die aktivierte Regel auf der Registerkarte **Aktive Regeln** im Abschnitt **Analytics** von Azure Sentinel. Sie können die aktive Regel an dieser Stelle bearbeiten, aktivieren, deaktivieren, duplizieren oder löschen. Die neue Regel wird sofort nach der Aktivierung ausgeführt, und ab diesem Zeitpunkt nach dem festgelegten Zeitplan ausgeführt.

Gemäß den Standardeinstellungen wird jedes Mal, wenn die Regel nach dem Zeitplan ausgeführt wird und Ergebnisse gefunden werden, eine Sicherheitswarnung generiert. Sicherheitswarnungen in Azure Sentinel können im Abschnitt **Protokolle** von Azure Sentinel in der Tabelle **SecurityAlert** unter der Gruppe **Azure Sentinel** angezeigt werden.

In Azure Sentinel generieren Warnungen, die nach Analyseregeln generiert wurden, auch Sicherheitsincidents, die Sie in **Incidents** unter **Bedrohungsmanagement** im Azure Sentinel-Menü finden. Teams für Sicherheitsvorgänge können die Incidents selektieren und untersuchen, um angemessene Reaktionen zu ermitteln. Ausführliche Informationen hierzu finden Sie in diesem [Tutorial: Untersuchen von Incidents mit Azure Sentinel](./investigate-cases.md).

Weitere Informationen zum Verwenden von Bedrohungsindikatoren in Analyseregeln finden Sie unter [Arbeiten mit Bedrohungsindikatoren in Azure Sentinel](work-with-threat-indicators.md#detect-threats-with-threat-indicator-based-analytics).

## <a name="workbooks-provide-insights-about-your-threat-intelligence"></a>Arbeitsmappen für Erkenntnisse aus Threat Intelligence

Workbooks bieten leistungsstarke interaktive Dashboards, die Ihnen Erkenntnisse zu allen Aspekten von Azure Sentinel liefern, so auch zu Threat Intelligence. Sie können die integrierte **Azure Sentinel-Arbeitsmappe** verwenden, um wichtige Informationen über Ihre Threat Intelligence-Daten zu visualisieren. Die Arbeitsmappe lässt sich problemlos an Ihre geschäftlichen Anforderungen anpassen. Sie können sogar neue Dashboards erstellen, die viele verschiedene Datenquellen kombinieren, damit Sie Ihre Daten auf besondere Weisen visualisieren können. Da Azure Sentinel-Arbeitsmappen auf Azure Monitor-Arbeitsmappen basieren, sind eine umfassende Dokumentation und viele weitere Vorlagen verfügbar. Einen hervorragenden Einstieg bietet der Artikel zum [Erstellen interaktiver Berichte mit Azure Monitor-Arbeitsmappen](../azure-monitor/visualize/workbooks-overview.md). 

Es gibt auch eine riesige Community für [Azure Monitor-Arbeitsmappen auf GitHub](https://github.com/microsoft/Application-Insights-Workbooks), in der Sie weitere Vorlagen herunterladen und eigene Vorlagen veröffentlichen können.

Weitere Informationen zur Verwendung und Anpassung der Threat Intelligence-Arbeitsmappe finden Sie unter [Arbeiten mit Bedrohungsindikatoren in Azure Sentinel](work-with-threat-indicators.md#workbooks-provide-insights-about-your-threat-intelligence).

## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument haben Sie etwas über die Threat Intelligence-Funktionen von Azure Sentinel sowie das Blatt „Threat Intelligence“ erfahren. Eine praktische Anleitung zur Verwendung der Threat Intelligence-Funktionen von Azure Sentinel finden Sie in den folgenden Artikeln:

- Verbinden von Azure Sentinel mit [STIX/TAXII-Threat Intelligence-Feeds](./connect-threat-intelligence-taxii.md)
- [Verbinden von Threat Intelligence-Plattformen](./connect-threat-intelligence-tip.md) mit Azure Sentinel
- [Integrieren von TIP-Plattformen, TAXII-Feeds und Anreicherungen](threat-intelligence-integration.md) in Azure Sentinel
- [Arbeiten mit Bedrohungsindikatoren](work-with-threat-indicators.md) in der Azure Sentinel-Umgebung
- Erkennen von Bedrohungen mit [integrierten](./detect-threats-built-in.md) oder [benutzerdefinierten](./detect-threats-custom.md) Analyseregeln in Azure Sentinel
- [Untersuchen von Incidents](./investigate-cases.md) in Azure Sentinel
