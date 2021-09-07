---
title: Arbeiten mit Bedrohungsindikatoren in Azure Sentinel | Microsoft-Dokumentation
description: In diesem Artikel wird erläutert, wie Sie in Azure Sentinel Bedrohungen mit Threat Intelligence-Indikatoren anzeigen, erstellen, verwalten, visualisieren und erkennen können.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/27/2021
ms.author: yelevin
ms.openlocfilehash: 2ecc10c43600a20ceb7209f651f27e78e7b286be
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2021
ms.locfileid: "122350532"
---
# <a name="work-with-threat-indicators-in-azure-sentinel"></a>Arbeiten mit Bedrohungsindikatoren in Azure Sentinel

Sie können Threat Intelligence (TI) mithilfe der folgenden Aktivitäten in Azure Sentinel integrieren:

- **Importieren Sie Threat Intelligence-Daten** in Azure Sentinel, indem Sie **Datenconnectors** für verschiedene TI-[Plattformen](connect-threat-intelligence-tip.md) und -[Feeds](connect-threat-intelligence-taxii.md) aktivieren.

- Sie können die importierten Threat Intelligence-Daten in **Protokollen** und auf dem Blatt **Threat Intelligence** von Azure Sentinel **anzeigen und verwalten**.

- **Erkennen Sie Bedrohungen**, und generieren Sie Sicherheitswarnungen und Incidents mit den integrierten **Analytics**-Regelvorlagen, die auf Ihren importierten Threat Intelligence-Daten basieren.

- **Visualisieren Sie wichtige Informationen** zu Ihren Threat Intelligence-Daten in Azure Sentinel mithilfe der **Threat Intelligence-Arbeitsmappe**.

> [!IMPORTANT]
> Entsprechend gekennzeichnete Features sind derzeit als VORSCHAUVERSION verfügbar. In den [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) finden Sie weitere rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden oder anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.
>

## <a name="view-your-threat-indicators-in-azure-sentinel"></a>Anzeigen von Bedrohungsindikatoren in Azure Sentinel

### <a name="find-and-view-your-indicators-in-logs"></a>Suchen und Anzeigen Ihrer Indikatoren in Protokollen

Sie können Ihre erfolgreich importierten Bedrohungsindikatoren unabhängig von der Feedquelle oder dem verwendeten Connector in der Tabelle **ThreatIntelligenceIndicator** (unter der Gruppe **Azure Sentinel**) in **Protokolle** einsehen, in der alle Ihre Azure Sentinel-Ereignisdaten gespeichert sind. Diese Tabelle bildet die Grundlage für Threat Intelligence-Abfragen, die von anderen Azure Sentinel-Features wie Analytics und Arbeitsmappen ausgeführt werden.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/), und navigieren Sie zum Dienst **Azure Sentinel**.

1. Wählen Sie den **Arbeitsbereich** aus, in den Sie Bedrohungsindikatoren mit einem der Threat Intelligence-Datenconnectors importiert haben.

1. Wählen Sie im Abschnitt **Allgemein** des Azure-Sentinel-Menüs **Protokolle** aus.

1. Sie finden die Tabelle **ThreatIntelligenceIndicator** unter der Gruppe **Azure Sentinel**.

1. Wählen Sie neben dem Tabellennamen das Symbol **Datenvorschau** (das Auge) und dann die Schaltfläche **Im Abfrage-Editor anzeigen** aus, um eine Abfrage auszuführen, mit der die Datensätze aus dieser Tabelle angezeigt werden.

Die Ergebnisse sollten den Beispielbedrohungsindikatoren unten ähneln:

:::image type="content" source="media/work-with-threat-indicators/threat-intel-sample-query.png" alt-text="Beispielabfragedaten":::

### <a name="find-and-view-your-indicators-in-the-threat-intelligence-blade"></a>Suchen und Anzeigen Ihrer Indikatoren auf dem Blatt „Threat Intelligence“

Sie können Ihre Indikatoren auch auf dem neuen Blatt **Threat Intelligence** einsehen und verwalten, auf das Sie über das Hauptmenü von Azure Sentinel Zugriff haben. Sie können Ihre importierten Bedrohungsindikatoren sortieren, filtern und durchsuchen, ohne eine Log Analytics-Abfrage schreiben zu müssen. Mit diesem Feature können Sie auch direkt auf der Azure Sentinel-Benutzeroberfläche Bedrohungsindikatoren erstellen und zwei der gängigsten Verwaltungsaufgaben für Threat Intelligence ausführen: Markieren von Indikatoren mit Tags und Erstellen neuer Indikatoren im Zusammenhang mit Sicherheitsuntersuchungen.

#### <a name="create-a-new-indicator"></a>Erstellen eines neuen Indikators

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zum Dienst **Azure Sentinel**.

1. Wählen Sie den **Arbeitsbereich** aus, in den Sie Bedrohungsindikatoren mit einem der Threat Intelligence-Datenconnectors importiert haben.

1. Wählen Sie im Azure Sentinel-Menü im Abschnitt „Bedrohungsmanagement“ die Option **Threat Intelligence** aus.

1. Wählen Sie im Menü oben auf der Seite die Schaltfläche **Neu hinzufügen** aus.

    :::image type="content" source="media/work-with-threat-indicators/threat-intel-add-new-indicator.png" alt-text="Hinzufügen eines neuen Bedrohungsindikators" lightbox="media/work-with-threat-indicators/threat-intel-add-new-indicator.png":::

1. Wählen Sie den Indikatortyp aus, und füllen Sie dann im Panel **Neuer Indikator** das Formular aus. Die Pflichtfelder sind mit einem roten Sternchen gekennzeichnet (*).

1. Wählen Sie **Übernehmen**. Der Indikator wird der Indikatorliste hinzugefügt und auch an die Tabelle *ThreatIntelligenceIndicator* in **Protokolle** übertragen.

#### <a name="tag-threat-indicators"></a>Markieren von Bedrohungsindikatoren

Das Markieren von Bedrohungsindikatoren mit Tags stellt eine einfache Möglichkeit dar, sie zu gruppieren und einfacher auffindbar zu machen. In der Regel können Sie ein Tag auf Indikatoren im Zusammenhang mit einem bestimmten Incident anwenden oder auf Indikatoren, die Bedrohungen von einem bestimmten bekannten Akteur oder einer bekannten Angriffskampagne darstellen. Sie können die Bedrohungsindikatoren einzeln markieren, oder Sie können mehrere Indikatoren auswählen und diese gleichzeitig markieren. Unten finden Sie ein Beispiel für das Markieren mehrerer Indikatoren mit einer Incident-ID. Da das Tagging eine Freiformangabe ist, empfiehlt es sich, Standardnamenskonventionen für Tags für Bedrohungsindikatoren zu erstellen. Sie können mehrere Tags auf jeden Indikator anwenden.

:::image type="content" source="media/work-with-threat-indicators/threat-intel-tagging-indicators.png" alt-text="Anwenden von Tags auf Bedrohungsindikatoren" lightbox="media/work-with-threat-indicators/threat-intel-tagging-indicators.png":::

## <a name="detect-threats-with-threat-indicator-based-analytics"></a>Erkennen von Bedrohungen mit auf Bedrohungsindikatoren basierenden Analysen

Der wichtigste Anwendungsfall für Bedrohungsindikatoren in SIEM-Lösungen wie Azure Sentinel ist die Unterstützung von Analyseregeln zur Bedrohungserkennung. Diese auf Indikatoren basierenden Regeln vergleichen Rohereignisse in Ihren Datenquellen mit Ihren Bedrohungsindikatoren, um zu ermitteln, ob es in Ihrer Organisation Sicherheitsbedrohungen gibt. Sie erstellen in Azure Sentinel **Analytics** Analyseregeln, die nach einem Zeitplan ausgeführt werden und Sicherheitswarnungen generieren. Die Regeln werden durch Abfragen gesteuert, zusammen mit Konfigurationen, die festlegen, wie oft die Regel ausgeführt werden soll, welche Art von Abfrageergebnissen Sicherheitswarnungen und Incidents generieren soll und welche Automatisierungen als Reaktion ausgelöst werden sollen.

Sie können stets neue Analyseregeln erstellen. Azure Sentinel bietet aber auch eine Reihe von integrierten Regelvorlagen, die von Microsoft-Sicherheitstechnikern erstellt wurden und die Sie unverändert verwenden oder entsprechend den jeweiligen Anforderungen anpassen können. Sie können Regelvorlagen, die Bedrohungsindikatoren verwenden, ganz einfach identifizieren, da ihr Name immer mit „*TI map*…“ beginnt. Alle diese Regelvorlagen funktionieren ähnlich, wobei der einzige Unterschied darin besteht, welche Art von Bedrohungsindikatoren verwendet wird (Domäne, E-Mail, Dateihash, IP-Adresse oder URL) und mit welchem Ereignistyp ein Abgleich erfolgen soll. Jede Vorlage listet die Datenquellen auf, die für das Funktionieren der Regel erforderlich sind, sodass Sie auf einen Blick erkennen können, ob Sie die nötigen Ereignisse bereits in Azure Sentinel importiert haben. Wenn Sie eine vorhandene Regelvorlage bearbeiten und speichern oder eine neue Regel erstellen, ist sie standardmäßig aktiviert.

### <a name="configure-a-rule-to-generate-security-alerts"></a>Konfigurieren einer Regel zum Generieren von Sicherheitswarnungen

Es folgt ein Beispiel der Aktivierung und Konfiguration einer Regel zum Generieren von Sicherheitswarnungen anhand der in Azure Sentinel importierten Bedrohungsindikatoren. In diesem Beispiel verwenden Sie die Regelvorlage **TI map IP entity to AzureActivity** (TI: IP-Entität zu Azure-Aktivität zuordnen). Mit dieser Regel werden alle Bedrohungsindikatoren vom Typ „IP-Adresse“ mit allen Ihren Azure-Aktivitätsereignissen abgeglichen. Bei einer Übereinstimmung werden eine **Warnung** und ein entsprechender **Incident** generiert, der dann von Ihrem Team für Sicherheitsvorgänge untersucht werden kann. Diese Analyseregel funktioniert nur dann ordnungsgemäß, wenn Sie einen der Datenconnectors **Threat Intelligence** (zum Importieren von Bedrohungsindikatoren) und den Datenconnector **Azure-Aktivität** (zum Importieren von Ereignissen auf Azure-Abonnementebene) aktiviert haben.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zum Dienst **Azure Sentinel**.

1. Wählen Sie den **Arbeitsbereich** aus, in den Sie Bedrohungsindikatoren mithilfe der Datenconnectors **Threat Intelligence** und Azure-Aktivitätsdaten mithilfe des Datenconnectors **Azure-Aktivität** importiert haben.

1. Wählen Sie im Azure Sentinel-Menü im Abschnitt **Konfiguration** die Option **Analytics** aus.

1. Wählen Sie die Registerkarte **Regelvorlagen** aus, um die Liste der verfügbaren Analyseregelvorlagen anzuzeigen.

1. Navigieren Sie zur Regel **TI map IP entity to AzureActivity**, und vergewissern Sie sich, dass Sie alle erforderlichen Datenquellen wie unten gezeigt verbunden haben.

    :::image type="content" source="media/work-with-threat-indicators/threat-intel-required-data-sources.png" alt-text="Erforderliche Datenquellen":::

1. Wählen Sie diese Regel aus, und wählen Sie dann die Schaltfläche **Regel erstellen** aus. Dadurch wird ein Assistent zum Konfigurieren der Regel geöffnet. Füllen Sie die Einstellungen hier aus, und wählen Sie die Schaltfläche **Weiter: Regellogik festlegen** aus.

    :::image type="content" source="media/work-with-threat-indicators/threat-intel-create-analytics-rule.png" alt-text="Erstellen der Analyseregel":::

1. Der Regellogikteil des Assistenten wurde mit den folgenden Elementen vorab aufgefüllt:
    - Die Abfrage, die in der Regel verwendet wird

    - Entitätszuordnungen, die Azure Sentinel mitteilen, wie Entitäten wie Konten, IP-Adressen und URLs identifiziert werden, damit **Incidents** und **Untersuchungen** verstehen, wie mit den Daten in den von dieser Regel generierten Sicherheitswarnungen verfahren werden soll

    - Der Zeitplan für die Ausführung dieser Regel

    - Die Anzahl der erforderlichen Abfrageergebnisse, bevor eine Sicherheitswarnung generiert wird

    Die Standardeinstellungen in der Vorlage lauten:
    - Ausführen einmal pro Stunde

    - Vergleichen aller Bedrohungsindikatoren vom Typ „IP-Adresse“ aus der Tabelle **ThreatIntelligenceIndicator** mit sämtlichen IP-Adressen, die in der letzten Stunde mit Ereignissen in der Tabelle **AzureActivity** gefunden wurden

    - Generieren einer Sicherheitswarnung, wenn die Abfrageergebnisse größer als 0 (null) sind (also Übereinstimmungen gefunden wurden)

    Sie können die Standardeinstellungen belassen oder ändern, um Ihre Anforderungen zu erfüllen. Sie können Einstellungen für die Incidentgenerierung auf der Registerkarte **Incidenteinstellungen** festlegen. Weitere Informationen finden Sie unter Erstellen [benutzerdefinierter Analyseregeln zum Erkennen von Bedrohungen](detect-threats-custom.md). Wenn Sie fertig sind, wählen Sie die Registerkarte **Automatisierte Antwort** aus.

1. Sie können alle Automatisierungen konfigurieren, die Sie auslösen möchten, wenn eine Sicherheitswarnung durch diese Analyseregel generiert wird. Die Automatisierung in Azure Sentinel erfolgt mittels Kombinationen von **Automatisierungsregeln** und **Playbooks**, die von Azure Logic Apps bereitgestellt werden. Weitere Informationen finden Sie unter [Tutorial: Verwenden von Playbooks mit Automatisierungsregeln in Azure Sentinel](./tutorial-respond-threats-playbook.md). Wählen Sie, sobald Sie fertig sind, die Schaltfläche **Weiter: Überprüfen >** aus, um fortzufahren.

1. Wenn Sie die Meldung sehen, dass die Regelüberprüfung bestanden wurde, wählen Sie die Schaltfläche **Erstellen** aus. Sie sind dann fertig.

Sie finden die aktivierten Regeln auf der Registerkarte **Aktive Regeln** im Abschnitt **Analytics** von Azure Sentinel. Sie können die aktive Regel an dieser Stelle bearbeiten, aktivieren, deaktivieren, duplizieren oder löschen. Die neue Regel wird sofort nach der Aktivierung ausgeführt, und ab diesem Zeitpunkt nach dem festgelegten Zeitplan ausgeführt.

Gemäß den Standardeinstellungen wird jedes Mal, wenn die Regel nach dem Zeitplan ausgeführt wird und Ergebnisse gefunden werden, eine Sicherheitswarnung generiert. Sicherheitswarnungen in Azure Sentinel können im Abschnitt **Protokolle** von Azure Sentinel in der Tabelle **SecurityAlert** unter der Gruppe **Azure Sentinel** angezeigt werden.

In Azure Sentinel generieren Warnungen, die nach Analyseregeln generiert wurden, auch Sicherheitsincidents, die Sie in **Incidents** unter **Bedrohungsmanagement** im Azure Sentinel-Menü finden. Teams für Sicherheitsvorgänge können die Incidents selektieren und untersuchen, um angemessene Reaktionen zu ermitteln. Ausführliche Informationen hierzu finden Sie in diesem [Tutorial: Untersuchen von Incidents mit Azure Sentinel](./investigate-cases.md).

## <a name="detect-threats-using-matching-analytics-public-preview"></a>Erkennen von Bedrohungen mithilfe von Abgleichsanalysen (Public Preview)

[Erstellen Sie eine Regel](detect-threats-built-in.md#use-built-in-analytics-rules) mithilfe der integrierten Analyseregelvorlage für die **Microsoft Threat Intelligence-Abgleichsanalyse**, damit Azure Sentinel von Microsoft generierte Threat Intelligence-Daten mit den Protokollen abgleicht, die Sie in Azure Sentinel erfasst haben.

Das Abgleichen von Threat Intelligence-Daten mit Ihren Protokollen hilft dabei, Warnungen und Incidents mit hoher Genauigkeit und entsprechenden Schweregraden zu generieren. Wenn eine Übereinstimmung gefunden wird, werden alle generierten Warnungen zu Incidents gruppiert.

Die Warnungen werden auf Basis der einzelnen Beobachtungen über einen Zeitraum von 24 Stunden gruppiert. So werden beispielsweise alle Warnungen, die in einem Zeitraum von 24 Stunden generiert werden und der Domäne `abc.com` entsprechen, zu einem einzigen Incident gruppiert.

### <a name="triage-through-an-incident-generated-by-matching-analytics"></a>Selektierung mittels eines Incidents, der per Abgleichsanalyse generiert wurde

Wenn Sie eine Übereinstimmung gefunden haben, werden alle generierten Warnungen zu Incidents gruppiert.

Führen Sie die folgenden Schritte aus, um die von der Regel **Microsoft Threat Intelligence-Abgleichsanalyse** generierten Incidents zu selektieren:

1. Wählen Sie im Azure Sentinel Arbeitsbereich, in dem Sie die Regel **Microsoft Threat Intelligence-Abgleichsanalyse** aktiviert haben, **Incidents** aus, und suchen Sie nach **Microsoft Threat Intelligence Analytics**.

    Alle gefundenen Incidents werden im Raster angezeigt.

1. Wählen Sie **Vollständige Details anzeigen aus**, um Entitäten und andere Details zum Incident anzuzeigen, z. B. bestimmte Warnungen.

    Zum Beispiel:

    :::image type="content" source="media/work-with-threat-indicators/matching-analytics.png" alt-text="Beispiel für Abgleichsanalysedetails.":::

Wenn eine Übereinstimmung gefunden wird, wird der Indikator auch im Log Analytics-Protokoll **ThreatIntelligenceIndicators** veröffentlicht und auf der Seite **Threat Intelligence** angezeigt. Für alle Indikatoren, die anhand dieser Regel veröffentlicht werden, wird die Quelle als **Microsoft Threat Intelligence Analytics** festgelegt.

Beispielsweise im Protokoll **ThreatIntelligenceIndicators**:

:::image type="content" source="media/work-with-threat-indicators/matching-analytics-logs.png" alt-text="Abgleichsanalysen, die im Protokoll ThreatIntelligenceIndicators angezeigt werden.":::

Auf der Seite **Threat Intelligence**:

:::image type="content" source="media/work-with-threat-indicators/matching-analytics-threat-intelligence.png" alt-text="Abgleichsanalysen, die auf der Seite „Threat Intelligence“ angezeigt werden.":::

### <a name="supported-log-sources-for-matching-analytics"></a>Unterstützte Protokollquellen für Abgleichsanalysen

Die Regel **Microsoft Threat Intelligence-Abgleichsanalyse** wird derzeit für die folgenden Protokollquellen unterstützt:

|Protokollquelle  |BESCHREIBUNG  |
|---------|---------|
|[CEF](connect-common-event-format.md)     |  Der Abgleich erfolgt für alle CEF-Protokolle, die in der Log Analytics-Tabelle **CommonSecurityLog** erfasst sind, mit Ausnahme derjenigen, bei denen `DeviceVendor` als `Cisco` aufgeführt ist. <br><br>Um von Microsoft generierte Threat Intelligence-Daten mit CEF-Protokollen abzugleichen, stellen Sie sicher, dass Sie die Domäne im Feld `RequestURL` des CEF-Protokolls zuordnen.      |
|[DNS](connect-dns.md)     | Der Abgleich erfolgt für alle DNS-Protokolle, bei denen es sich um DNS-Suchabfragen von Clients von DNS-Diensten (`SubType == "LookupQuery"`) handelt. DNS-Abfragen werden nur für IPv4- (`QueryType=”A”`) und IPv6-Abfragen (`QueryType=” AAAA”`) verarbeitet.<br><br>Um von Microsoft generierte Threat Intelligence-Daten mit DNS-Protokollen zu abgleichen, ist keine manuelle Zuordnung von Spalten erforderlich, da alle Spalten standardmäßig vom Windows-DNS-Server verwendet werden und die Domänen standardmäßig in der Spalte `Name` enthalten sind.   |
|[Syslog](connect-syslog.md)     |  Der Abgleich erfolgt derzeit nur für Syslog-Ereignisse, bei denen `Facility` gleich `cron` ist. <br><br>Um von Microsoft generierte Threat Intelligence-Daten mit Syslog zu abgleichen, ist keine manuelle Spaltenzuordnung erforderlich. Die Details werden von Syslog standardmäßig im Feld angezeigt, und die Regel analysiert die Domäne `SyslogMessage` direkt anhand von SyslogMessage.     |
|     |         |


## <a name="workbooks-provide-insights-about-your-threat-intelligence"></a>Arbeitsmappen für Erkenntnisse aus Threat Intelligence

Sie können eine eigens erstellte Azure Sentinel-Arbeitsmappe verwenden, um wichtige Informationen über Ihre Threat Intelligence-Daten in Azure Sentinel zu visualisieren. Die Arbeitsmappe lässt sich problemlos an Ihre geschäftlichen Anforderungen anpassen.
Hier erfahren Sie, wie Sie die in Azure Sentinel enthaltene Threat Intelligence-Arbeitsmappe finden und wie Sie die Arbeitsmappe bearbeiten können, um sie anzupassen.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zum Dienst **Azure Sentinel**.

1. Wählen Sie den **Arbeitsbereich** aus, in den Sie Bedrohungsindikatoren mit einem der Threat Intelligence-Datenconnectors importiert haben.

1. Wählen Sie im Azure Sentinel-Menü im Abschnitt **Bedrohungsmanagement** die Option **Arbeitsmappen** aus.

1. Navigieren Sie zur Arbeitsmappe **Threat Intelligence**, und vergewissern Sie sich, dass die Tabelle **ThreatIntelligenceIndicator** wie unten gezeigt Daten enthält.

    :::image type="content" source="media/work-with-threat-indicators/threat-intel-verify-data.png" alt-text="Überprüfen der Daten":::

1. Wählen Sie die Schaltfläche **Speichern** aus, und wählen Sie dann einen Azure-Speicherort für die Arbeitsmappe aus. Dieser Schritt ist erforderlich, wenn Sie die Arbeitsmappe anpassen und Ihre Änderungen speichern möchten.

1. Wählen Sie nun die Schaltfläche **Gespeicherte Arbeitsmappe anzeigen** aus, um die Arbeitsmappe für die Anzeige und Bearbeitung zu öffnen.

1. Es sollten die von der Vorlage bereitgestellten Standarddiagramme angezeigt werden. Wählen Sie zum Ändern eines Diagramms oben auf der Seite die Schaltfläche **Bearbeiten** aus, um den Bearbeitungsmodus für die Arbeitsmappe zu aktivieren.

1. Fügen Sie ein neues Diagramm mit Bedrohungsindikatoren nach Bedrohungstyp hinzu. Scrollen Sie auf der Seite nach unten, und wählen Sie **Abfrage hinzufügen** aus.

1. Fügen Sie den folgenden Text in das Textfeld **Protokollabfrage** des Log Analytics-Arbeitsbereichs ein:
    ```kusto
    ThreatIntelligenceIndicator
    | summarize count() by ThreatType
    ```

1. Wählen Sie in der Dropdownliste **Visualisierung** den Eintrag **Balkendiagramm** aus.

1. Wählen Sie die Schaltfläche **Bearbeitung abgeschlossen** aus. Sie haben ein neues Diagramm für Ihre Arbeitsmappe erstellt.

    :::image type="content" source="media/work-with-threat-indicators/threat-intel-bar-chart.png" alt-text="Balkendiagramm":::

Mit Arbeitsmappen verfügen Sie über leistungsstarke interaktive Dashboards, über die Sie Einblicke in alle Aspekte von Azure Sentinel erhalten. Es gibt zahlreiche Anwendungsmöglichkeiten für Arbeitsmappen, und die bereitgestellten Vorlagen sind ein guter Ausgangspunkt. Sie können diese Vorlagen aber natürlich genauer untersuchen und anpassen oder neue Dashboards erstellen, auf denen viele verschiedene Datenquellen kombiniert werden, um so Ihre Daten auf spezifische Weise zu visualisieren. Da Azure Sentinel-Arbeitsmappen auf Azure Monitor-Arbeitsmappen basieren, sind eine umfassende Dokumentation und viele weitere Vorlagen verfügbar. Einen hervorragenden Einstieg bietet der Artikel zum [Erstellen interaktiver Berichte mit Azure Monitor-Arbeitsmappen](../azure-monitor/visualize/workbooks-overview.md). 

Es gibt auch eine riesige Community für [Azure Monitor-Arbeitsmappen auf GitHub](https://github.com/microsoft/Application-Insights-Workbooks), in der Sie weitere Vorlagen herunterladen und eigene Vorlagen veröffentlichen können.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie alle Möglichkeiten zum Arbeiten mit Threat Intelligence-Indikatoren in Azure Sentinel kennengelernt. Weitere Informationen zu Threat Intelligence in Azure Sentinel finden Sie in den folgenden Artikeln:
- [Grundlegendes zu Threat Intelligence in Azure Sentinel](understand-threat-intelligence.md)
- Verbinden von Azure Sentinel mit [STIX/TAXII-Threat Intelligence-Feeds](./connect-threat-intelligence-taxii.md)
- [Verbinden von Threat Intelligence-Plattformen](./connect-threat-intelligence-tip.md) mit Azure Sentinel
- [Integrieren von TIP-Plattformen, TAXII-Feeds und Anreicherungen](threat-intelligence-integration.md) in Azure Sentinel