---
title: Migrieren von einer vorhandenen SIEM-Lösung zu Microsoft Sentinel
description: Hier erfahren Sie, wie Sie eine vorhandene SIEM-Lösung am besten zu Microsoft Sentinel migrieren und so organisationsweit von skalierbaren, intelligenten Sicherheitsanalysen profitieren.
services: sentinel
documentationcenter: na
author: batamig
ms.service: microsoft-sentinel
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5df30dd53422da751271ac644adf7072efc6dc46
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520786"
---
# <a name="migrate-to-microsoft-sentinel-from-an-existing-siem"></a>Migrieren von einer vorhandenen SIEM-Lösung zu Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Ihr SOC-Team (Security Operations Center) verwendet zentrale SIEM- (Security Information and Event Management) und SOAR-Lösungen (Security Orchestration, Automation and Response), um Ihre zunehmend dezentralisierte digitale Infrastruktur zu schützen.

Legacy-SIEM-Lösungen sind häufig lokal und können eine gute Abdeckung von lokalen Ressourcen gewährleisten. Lokale Architekturen verfügen jedoch möglicherweise über unzureichende Abdeckung für Ihre Cloudressourcen, z. B. in Bezug auf Azure, Microsoft 365, AWS oder Google Cloud Platform (GCP). Im Gegensatz dazu können Sie mit Microsoft Sentinel Daten aus lokalen Systemen sowie aus Cloudressourcen erfassen und so die Abdeckung der gesamten Ressourcen sicherstellen.

In diesem Artikel wird beschrieben, wie Sie von einer vorhandenen Legacy-SIEM-Lösung zu Microsoft Sentinel migrieren, entweder im Rahmen einer parallelen Konfiguration oder durch den Übergang zu einer vollständigen Bereitstellung von Microsoft Sentinel.

## <a name="plan-your-migration"></a>Planen Ihrer Migration

Je nach den bestehenden Geschäftsanforderungen und verfügbaren Ressourcen haben Sie sich möglicherweise dazu entschieden, einen direkten oder schrittweise erfolgenden Übergang zu Microsoft Sentinel zu starten.

Sie sollten die Migration genau planen, um zu gewährleisten, dass beim Übergang keine Abdeckungslücken entstehen, die die Sicherheit Ihrer Organisation gefährden könnten.

Identifizieren Sie zunächst die wichtigsten Kernfunktionen und Anforderungen mit oberster Priorität. Bewerten Sie die wichtigsten Anwendungsfälle, die Ihre aktuelle SIEM-Lösung abdeckt, und entscheiden Sie, welche Erkennungen und Funktionen Microsoft Sentinel weiterhin abdecken muss.

Sie ergänzen jeden Schritt des Migrationsprozesses durch eine weitere Prozessplanung, während Sie die genauen Datenquellen und Erkennungsregeln berücksichtigen, die Sie migrieren möchten. Weitere Informationen finden Sie unter [Migrieren von Daten](#migrate-your-data) und [Migrieren von Analyseregeln](#migrate-analytics-rules).

> [!TIP]
> Ihre aktuelle SIEM-Lösung verfügt möglicherweise über eine überwältigende Anzahl von Erkennungen und Anwendungsfällen. Entscheiden Sie, welche für Ihr Unternehmen am nützlichsten sind, und bestimmen Sie, welche möglicherweise nicht migriert werden müssen. Überprüfen Sie beispielsweise, mit welchen Erkennungen im letzten Jahr Ergebnisse generiert wurden.
>

### <a name="compare-your-legacy-siem-to-microsoft-sentinel"></a>Vergleichen der Legacy-SIEM-Lösung mit Microsoft Sentinel

Vergleichen Sie Ihre Legacy-SIEM-Lösung mit Microsoft Sentinel, um Ihre Kriterien für den Abschluss der Migration zu verfeinern und zu verstehen, in welchen Bereichen Sie mit Microsoft Sentinel einen Mehrwert erzielen können.

Werten Sie beispielsweise die folgenden Schlüsselbereiche aus:

|Auswertungsbereich |BESCHREIBUNG  |
|---------|---------|
|**Abdeckung der Angriffserkennung**     | Vergleichen Sie mithilfe von [MITRE ATT&CK](https://attack.mitre.org/) oder einem ähnlichen Framework, wie gut mit der jeweiligen SIEM-Lösung die gesamte Bandbreite von Angriffen erkannt werden kann.        |
|**Reaktionsfähigkeit**     |   Messen Sie die durchschnittliche Bestätigungszeit (Mean Time To Acknowledge, MTTA), also die Zeit zwischen einer Warnung, die von der SIEM-Lösung angezeigt wird, und dem Zeitpunkt, an dem ein Analyst mit der Arbeit am Gegenstand der Warnung beginnt. Diese Zeit wird wahrscheinlich zwischen den jeweiligen SIEM-Lösungen ähnlich sein.      |
|**Durchschnittliche Korrekturzeit (Mean Time To Remediate, MTTR)**     |    Vergleichen Sie die MTTR für Incidents, die von der jeweiligen SIEM-Lösung untersucht werden, und gehen Sie dabei davon aus, dass Analysten über gleichwertige Qualifikationsniveaus verfügen.     |
|**Hunting-Geschwindigkeit und -Agilität**     | Messen Sie, wie schnell Teams ausgehend von einer vollständigen Hypothese über das Abfragen der Daten bis hin zum Abrufen der Ergebnisse auf der jeweiligen SIEM-Plattform eine Suche durchführen können.        |
|**Probleme beim Kapazitätswachstum**     |  Vergleichen Sie die Schwierigkeiten, die sich mit zunehmender Auslastung hinsichtlich der Kapazitätserweiterung ergeben. Beachten Sie, dass Clouddienste und -anwendungen tendenziell mehr Protokolldaten generieren als herkömmliche lokale Workloads.       |
|     |         |

Wenn Sie nur begrenzte oder gar keine Investitionen in eine bestehende lokale SIEM-Lösung getätigt haben, kann die Umstellung auf Microsoft Sentinel eine unkomplizierte, direkte Bereitstellung sein. Unternehmen, die umfangreiche Investitionen in eine Legacy-SIEM-Lösung getätigt haben, benötigen jedoch in der Regel einen mehrstufigen Prozess, um die Aufgaben im Zusammenhang mit dem Übergang zu bewältigen.

Obwohl Microsoft Sentinel erweiterte Daten und Antworten sowohl für lokale Ressourcen als auch für Cloudressourcen verfügbar macht, sollten Sie die Migration langsam starten, indem Sie Microsoft Sentinel und die Legacy-SIEM-Lösung [parallel](#select-a-side-by-side-approach-and-method) ausführen. In einer parallelen Architektur kann für lokale Ressourcen die lokale SIEM-Lösung und für Cloudressourcen und neue Workloads eine cloudbasierte Analyse verwendet werden.

Sofern Sie sich nicht dafür entscheiden, die Architektur langfristig im Parallelbetrieb zu konfigurieren, empfiehlt es sich, die Migration mit einer vollständigen Bereitstellung von Microsoft Sentinel abzuschließen, sodass Sie von niedrigeren Infrastrukturkosten, Echtzeit-Bedrohungsanalyse und Cloudskalierbarkeit profitieren.

## <a name="select-a-side-by-side-approach-and-method"></a>Auswählen eines Ansatzes und einer Methode für den Parallelbetrieb

Verwenden Sie eine parallele Architektur entweder für eine kurzfristige Übergangsphase, die eine vollständig in der Cloud gehostete SIEM-Lösung zum Ziel hat, oder als mittel- bis langfristiges Betriebsmodell, je nachdem, welche SIEM-Anforderungen in Ihrer Organisation bestehen.

Normalerweise empfiehlt es sich, eine parallele Struktur nur zu verwenden, bis die Migration abgeschlossen ist; allerdings kann es für Ihr Unternehmen nützlich sein, länger an der parallelen Konfiguration festzuhalten, beispielsweise für den Fall, dass Sie noch nicht dazu bereit sind, die Legacy-SIEM-Lösung aufzugeben. Organisationen, die langfristig auf eine parallele Konfiguration setzen, verwenden Microsoft Sentinel in der Regel ausschließlich dazu, die Clouddaten zu analysieren.

Berücksichtigen Sie die Vor- und Nachteile des jeweiligen Ansatzes, wenn Sie entscheiden, welchen Sie für Ihre Migration verwenden möchten.

> [!NOTE]
> Viele Organisationen vermeiden aufgrund von Kosten und Komplexität die Ausführung mehrerer lokaler Analyselösungen.
>
> Microsoft Sentinel bietet die Vorteile einer [nutzungsbasierten Bezahlung](azure-sentinel-billing.md) und einer flexiblen Infrastruktur, sodass SOC-Teams Zeit haben, sich an die Änderung anzupassen. Migrieren und testen Sie Ihre Inhalte in einem Tempo, das für Ihre Organisation am besten geeignet ist.
>
### <a name="short-term-approach"></a>Kurzfristiger Ansatz

:::row:::
   :::column span="":::
      **Vorteile**

        – Bietet SOC-Mitarbeitern Zeit für die Anpassung an neue Prozesse, wenn Workloads und Analysen migriert werden

        – Erzielt für Huntingszenarios datenquellenübergreifend eine umfassende Korrelation

        – Sie müssen keine Analysen zwischen SIEM-Lösungen durchführen, Weiterleitungsregeln erstellen und Untersuchungen an zwei Stellen abschließen.

        – Ermöglicht es Ihrem SOC-Team, Legacy-SIEM-Lösungen schnell herabzustufen. Damit entfallen Infrastruktur- und Lizenzkosten.
   :::column-end:::
   :::column span="":::
      **Nachteile**

        – Kann eine steile Lernkurve für SOC-Mitarbeiter erfordern
   :::column-end:::
:::row-end:::

### <a name="medium--to-long-term-approach"></a>Mittel- bis langfristiger Ansatz

:::row:::
   :::column span="":::
      **Vorteile**

        – Ermöglicht die Inanspruchnahme wichtiger Microsoft Sentinel-Vorteile, z. B. KI, ML und Untersuchungsfunktionen, ohne die Legacy-SIEM-Lösung vollständig aufgeben zu müssen

        – Spart im Vergleich zur Legacy-SIEM-Lösung Geld, indem Cloud- oder Microsoft-Daten in Microsoft Sentinel analysiert werden
   :::column-end:::
   :::column span="":::
      **Nachteile**

        – Erhöht die Komplexität, indem Analysen datenbankübergreifend getrennt werden

        – Teilt Fallverwaltung und Untersuchungen für in mehreren Umgebungen auftretende Incidents auf

        – Es fallen höhere Personal- und Infrastrukturkosten an.

        – Erfordert, dass sich SOC-Mitarbeiter mit zwei verschiedenen SIEM-Lösungen auskennen müssen
   :::column-end:::
:::row-end:::



### <a name="send-alerts-from-a-legacy-siem-to-microsoft-sentinel-recommended"></a>Senden von Warnungen von einer Legacy-SIEM-Lösung an Microsoft Sentinel (empfohlen)

Senden Sie Warnungen oder Indikatoren für anomale Aktivitäten von Ihrer Legacy-SIEM-Lösung an Microsoft Sentinel.

- Erfassen und analysieren Sie Clouddaten in Microsoft Sentinel.
- Verwenden Sie Ihre Legacy-SIEM-Lösung, um lokale Daten zu analysieren und Warnungen zu generieren.
- Leiten Sie die Warnungen von der lokalen SIEM-Lösung an Microsoft Sentinel weiter, um eine zentrale Schnittstelle einzurichten.

Leiten Sie beispielsweise Warnungen mithilfe von [Logstash](connect-logstash.md), [APIs](/rest/api/securityinsights/) oder [Syslog](connect-syslog.md) weiter, und speichern Sie sie im [JSON](https://techcommunity.microsoft.com/t5/azure-sentinel/tip-easily-use-json-fields-in-sentinel/ba-p/768747)-Format im [Log Analytics-Arbeitsbereich](../azure-monitor/logs/quick-create-workspace.md) von Microsoft Sentinel.

Durch das Senden von Warnungen von der Legacy-SIEM-Lösung an Microsoft Sentinel kann Ihr Team diese Warnungen in Microsoft Sentinel übergreifend korrelieren und untersuchen. Das Team kann bei Bedarf weiterhin auf die Legacy-SIEM-Lösung zugreifen, um eine ausführlichere Untersuchung durchzuführen. In der Zwischenzeit können Sie die Migration von Datenquellen über einen längeren Übergangszeitraum hinweg fortsetzen.

Diese empfohlene Methode für die Migration im Parallelbetrieb bietet Ihnen den vollen Nutzen von Microsoft Sentinel und die Möglichkeit, Datenquellen in dem für Ihre Organisation geeigneten Tempo zu migrieren. Mit diesem Ansatz wird die Verdopplung der Kosten für Datenspeicherung und -erfassung verhindert, während Sie die Datenquellen entsprechend überführen.

Weitere Informationen finden Sie unter:

- [Migrieren von QRadar-Verstößen zu Microsoft Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/migrating-qradar-offenses-to-azure-sentinel/ba-p/2102043)
- [Exportieren von Daten aus Splunk in Microsoft Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/how-to-export-data-from-splunk-to-azure-sentinel/ba-p/1891237)


### <a name="send-alerts-and-enriched-incidents-from-microsoft-sentinel-to-a-legacy-siem"></a>Senden von Warnungen und angereicherten Incidents von Microsoft Sentinel an eine Legacy-SIEM-Lösung

Analysieren Sie einige Daten in Microsoft Sentinel, z. B. Clouddaten, und senden Sie die generierten Warnungen dann an eine Legacy-SIEM-Lösung. Verwenden Sie die *Legacy*-SIEM-Lösung als einzige Schnittstelle, um eine Kreuzkorrelation mit den von Microsoft Sentinel generierten Warnungen vorzunehmen. Sie können Microsoft Sentinel weiterhin verwenden, um die von Microsoft Sentinel generierten Warnungen eingehender zu untersuchen.

Diese Konfiguration ist kostengünstig, da Sie die Clouddatenanalyse in Microsoft Sentinel auslagern können, ohne Kosten zu verdoppeln oder zweimal für Daten zu bezahlen. Sie haben weiterhin die Möglichkeit, die Migration in Ihrem eigenen Tempo durchzuführen. Wenn Sie damit fortfahren, Datenquellen und Erkennungen in Microsoft Sentinel auszulagern, wird es einfacher, die Migration zu Microsoft Sentinel durchzuführen und Microsoft Sentinel als primäre Schnittstelle zu verwenden. Durch die einfache Weiterleitung angereicherter Incidents an eine Legacy-SIEM-Lösung wird jedoch der Nutzen der Untersuchungs-, Hunting- und Automatisierungsfunktionen von Microsoft Sentinel eingeschränkt.

Weitere Informationen finden Sie unter

- [Senden von angereicherten Microsoft Sentinel-Warnungen an eine Legacy-SIEM-Lösung](https://techcommunity.microsoft.com/t5/azure-sentinel/sending-enriched-azure-sentinel-alerts-to-3rd-party-siem-and/ba-p/1456976)
- [Senden von angereicherten Microsoft Sentinel-Warnungen an IBM QRadar](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-side-by-side-with-qradar/ba-p/1488333)
- [Erfassen von Microsoft Sentinel-Warnungen in Splunk](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-side-by-side-with-splunk/ba-p/1211266)

### <a name="other-methods"></a>Andere Methoden

In der folgenden Tabelle werden die *nicht* empfohlenen Konfigurationen für den Parallelbetrieb zusammen mit den diesbezüglichen Gründen beschrieben:

|Methode  |BESCHREIBUNG  |
|---------|---------|
|**Senden von Microsoft Sentinel-Protokollen an eine Legacy-SIEM-Lösung**     |  Mit dieser Methode werden die Kosten und Skalierungsprobleme der lokalen SIEM-Lösung weiter bestehen bleiben. <br><br>Sie bezahlen für die Datenerfassung in Microsoft Sentinel, zusammen mit den Speicherkosten in Verbindung mit der Legacy-SIEM-Lösung, und Sie können die SIEM- und SOAR-Erkennungs-, Analyse-, User Entity Behavior Analytics (UEBA)-, KI- oder Untersuchungs- und Automatisierungstools von Microsoft Sentinel nicht nutzen.       |
|**Senden von Protokollen aus einer Legacy-SIEM-Lösung an Microsoft Sentinel**     |   Diese Methode bietet zwar die vollständige Funktionalität von Microsoft Sentinel, aber Ihre Organisation bezahlt trotzdem für zwei verschiedene Datenerfassungsquellen. Dieses Modell kann nicht nur dazu führen, dass die Komplexität der Architektur zunimmt, sondern auch Kostensteigerungen nach sich ziehen.     |
|**Verwenden von Microsoft Sentinel und der Legacy-SIEM-Lösung als zwei vollständig separate Lösungen**     |  Sie können Microsoft Sentinel verwenden, um einige Datenquellen zu analysieren, z. B. die Clouddaten, und die lokale SIEM-Lösung weiterhin für andere Quellen einsetzen. Dieses Setup ermöglicht klare Grenzen für die Verwendung der jeweiligen Lösung und verhindert die Verdoppelung von Kosten. <br><br>Die Kreuzkorrelation wird jedoch erschwert, und Sie können Angriffe, die beide Datenquellengruppen betreffen, nicht vollständig diagnostizieren. In der heutigen Bedrohungslandschaft, in der sich Bedrohungen häufig lateral durch eine Organisation ausbreiten, können solche Transparenzlücken erhebliche Sicherheitsrisiken darstellen.       |
|     |         |



## <a name="migrate-your-data"></a>Migrieren Ihrer Daten

Achten Sie darauf, dass Sie nur Daten migrieren, die Ihren aktuell wichtigen Anwendungsfällen entsprechen.

1. Bestimmen Sie die Daten, die zur Unterstützung der einzelnen Anwendungsfälle erforderlich sind.

1. Bestimmen Sie, ob von den aktuellen Datenquellen wertvolle Daten bereitgestellt werden.

1. Identifizieren Sie alle Sichtbarkeitslücken in der aktuellen SIEM-Lösung sowie Maßnahmen, mit denen Sie die Lücken schließen können.

1. Überlegen Sie sich für jede Datenquelle, ob Sie unformatierte Protokolle erfassen müssen (dies kann kostspielig sein) oder ob angereicherte Warnungen genügend Kontext für Ihre wichtigsten Anwendungsfälle verfügbar machen.

      Beispielsweise können Sie angereicherte Daten aus Sicherheitsprodukten in der gesamten Organisation erfassen und Microsoft Sentinel dazu verwenden, diese übergreifend zu korrelieren, ohne unformatierte Protokolle aus den eigentlichen Datenquellen erfassen zu müssen.

1. Verwenden Sie eine der folgenden Ressourcen, um Daten zu erfassen:

    - Verwenden Sie die in **Microsoft Sentinel [integrierten Datenconnectors](connect-data-sources.md)** , um mit der Datenerfassung zu beginnen. Beispielsweise empfiehlt es sich, eine kostenlose [Testversion](azure-sentinel-billing.md#free-trial) mit Ihren Clouddaten zu starten oder [kostenlose Datenconnectors](azure-sentinel-billing.md#free-data-sources) zu verwenden, damit Sie Daten aus anderen Produkten von Microsoft erfassen können.

    - Sie können auch **[Syslog](connect-data-sources.md#syslog), [Common Event Format (CEF)](connect-data-sources.md#common-event-format-cef) oder [REST-APIs](connect-data-sources.md#rest-api-integration)** verwenden, um andere Datenquellen mit Azure Sentinel zu verbinden.

        Weitere Informationen finden Sie in der [Referenz zu Microsoft Sentinel-Datenconnectors](data-connectors-reference.md) sowie im [Microsoft Sentinel-Lösungskatalog](sentinel-solutions-catalog.md).

> [!TIP]
> - Wenn Sie sich auf kostenlose Datenquellen beschränken, schränken Sie damit gegebenenfalls auch Ihre Möglichkeit ein, Tests mit Daten durchzuführen, die für Sie wichtig sind. Erwägen Sie beim Testen eine begrenzte Datenerfassung über kostenlose und kostenpflichtige Datenconnectors, damit Sie den größten Nutzen aus den Testergebnissen ziehen können.
>
> - Beachten Sie beim Migrieren von Erkennungen und Erstellen von Anwendungsfällen in Microsoft Sentinel die von Ihnen erfassten Daten, und überprüfen Sie ihren Wert für Ihre wichtigsten Prioritäten. Gehen Sie erneut Konversationen zur Datensammlung durch, um eine ausreichende Datentiefe und -breite in Ihren Anwendungsfällen zu gewährleisten.
>

## <a name="migrate-analytics-rules"></a>Migrieren von Analyseregeln

Von Microsoft Sentinel werden aus maschinellem Lernen gewonnene Analysen dazu verwendet, hochwertige und praktisch verwertbare Incidents zu erstellen; einige Ihrer vorhandenen Erkennungen sind in Microsoft Sentinel möglicherweise redundant. Migrieren Sie daher nicht einfach unhinterfragt alle Erkennungs- und Analyseregeln:

- Achten Sie darauf, dass Sie Anwendungsfälle auswählen, die die Regelmigration unter Berücksichtigung von Geschäftspriorität und Effizienz rechtfertigen.

- Bewerten Sie [integrierte Analyseregeln](detect-threats-built-in.md), die Ihren Anwendungsfällen möglicherweise bereits gerecht werden. Öffnen Sie in Microsoft Sentinel die Registerkarte **Konfiguration > Analytics > Regelvorlagen**, um Regeln basierend auf integrierten Vorlagen zu erstellen.

- Überprüfen Sie alle Regeln, durch die in den letzten 6 bis 12 Monaten keine Warnungen ausgelöst wurden, und bestimmen Sie, ob sie weiterhin relevant sind.

- Beseitigen Sie Bedrohungen oder Warnungen mit niedrigem Schweregrad, die Sie routinemäßig ignorieren.

**Migrieren von Analyseregeln zu Microsoft Sentinel**:

1. Stellen Sie sicher, dass für jede Regel, die Sie migrieren möchten, ein Testsystem vorhanden ist.

    1. **Konzipieren Sie einen Überprüfungsprozess** für die migrierten Regeln, einschließlich vollständiger Testszenarios und Skripts.

    1. **Stellen Sie sicher, dass Ihr Team über nützliche Ressourcen verfügt**, damit die migrierten Regeln getestet werden können.

    1. **Vergewissern Sie sich, dass alle erforderlichen Datenquellen verbunden sind**, und überprüfen Sie die Datenverbindungsmethoden.

1. Überprüfen Sie, ob die Erkennungen als integrierte Vorlagen in Microsoft Sentinel verfügbar sind:

    - **Wenn die integrierten Regeln ausreichend sind**, verwenden Sie integrierte Regelvorlagen, um Regeln für Ihren eigenen Arbeitsbereich zu erstellen.

        Öffnen Sie in Microsoft Sentinel die Registerkarte **Konfiguration > Analytics > Regelvorlagen**, um die jeweils relevante Analyseregel zu erstellen und zu aktualisieren.

        Weitere Informationen finden Sie unter [Standardmäßig verfügbare Erkennung von Bedrohungen](detect-threats-built-in.md).

    - **Wenn Sie über Erkennungen verfügen, die nicht durch die integrierten Regeln von Microsoft Sentinel abgedeckt sind**, versuchen Sie es mit einem Onlineabfragekonverter wie [Uncoder.io](https://uncoder.io/), um Ihre Abfragen in KQL zu konvertieren.

        Identifizieren Sie die Auslöserbedingung und die Regelaktion, und erstellen und überprüfen Sie dann die KQL-Abfrage.

    - **Wenn weder die integrierten Regeln noch ein Onlineregelkonverter ausreichend sind**, müssen Sie die Regel manuell erstellen. Führen Sie in solchen Fällen die folgenden Schritte aus, um mit der Erstellung der Regel zu beginnen:

        1. **Identifizieren Sie die Datenquellen, die Sie in der Regel verwenden möchten**. Sie sollten eine Zuordnungstabelle zwischen Datenquellen und Datentabellen in Microsoft Sentinel erstellen, um die Tabellen zu identifizieren, die Sie abfragen möchten.

        1. **Identifizieren Sie alle Attribute, Felder oder Entitäten** in den Daten, die Sie in den Regeln verwenden möchten.

        1. **Identifizieren Sie die Regelkriterien und die Logik**. In dieser Phase sollten Sie Regelvorlagen als Beispiele zum Erstellen der KQL-Abfragen verwenden.

            Berücksichtigen Sie Filter, Korrelationsregeln, aktive Listen, Verweissätze, Watchlists, Erkennungsanomalien, Aggregationen usw. Sie können die von der Legacy-SIEM-Lösung bereitgestellten Verweise verwenden, um nachzuvollziehen, wie Sie die Abfragesyntax am besten zuordnen können.

            Siehe hierzu z.B.:

            - [Beispielregelzuordnung zwischen ArcSight/QRadar und Microsoft Sentinel](https://github.com/Azure/Azure-Sentinel/blob/master/Tools/RuleMigration/Rule%20Logic%20Mappings.md)
            - [SPL-zu-KQL-Zuordnungsbeispiele](https://github.com/Azure/Azure-Sentinel/blob/master/Tools/RuleMigration/Rule%20Logic%20Mappings.md) 

        1. **Identifizieren Sie die Auslöserbedingung und die Regelaktion, und erstellen und überprüfen Sie dann die KQL-Abfrage**. Wenn Sie die Abfrage überprüfen, sollten Sie die Anleitungsressourcen für die KQL-Optimierung in Betracht ziehen.

1. Testen Sie die Regel mit jedem der relevanten Anwendungsfälle. Wenn keine erwarteten Ergebnisse bereitgestellt werden, sollten Sie die KQL-Abfrage überprüfen und erneut testen.

1. Wenn Sie mit dem Ergebnis zufrieden sind, können Sie die Regel als migriert betrachten. Erstellen Sie bei Bedarf ein Playbook für die Regelaktion. Weitere Informationen finden Sie unter [Automatisieren der Bedrohungsabwehr mit Playbooks in Microsoft Sentinel](automate-responses-with-playbooks.md).

**Weitere Informationen finden Sie unter**:

- [**Erstellen benutzerdefinierter Analyseregeln zum Erkennen von Bedrohungen**](detect-threats-custom.md). Verwenden Sie [Warnungsgruppen](detect-threats-custom.md#alert-grouping), um Warnungsmüdigkeit zu reduzieren, indem Sie Warnungen gruppieren, die innerhalb eines bestimmten Zeitraums auftreten.
- [**Ordnen Sie Datenfelder Entitäten in Microsoft Sentinel zu**](map-data-fields-to-entities.md), damit SOC-Techniker Entitäten als Teil des Beweismaterials definieren können, das während einer Untersuchung nachverfolgt werden soll. Die Entitätszuordnung ermöglicht es SOC-Analysten zudem, ein intuitives [Untersuchungsdiagramm](investigate-cases.md#use-the-investigation-graph-to-deep-dive) zu nutzen, mit dem sie Zeit und Aufwand reduzieren können.
- [**Untersuchen Sie Incidents mit UEBA-Daten**](investigate-with-ueba.md) als Beispiel für die Verwendung von Beweisen, um Ereignisse, Warnungen und alle Lesezeichen, die einem bestimmten Incident zugeordnet sind, im Bereich der Incidentvorschau anzuzeigen.
- [**Kusto Query Language (KQL)**](/azure/data-explorer/kusto/query/), mit der Sie schreibgeschützte Anforderungen an die [Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)-Datenbank senden können, um Daten zu verarbeiten und Ergebnisse zurückzugeben. KQL wird auch für andere Microsoft-Dienste verwendet, z. B. [Microsoft Defender für Endpunkt](https://www.microsoft.com/microsoft-365/security/endpoint-defender) und [Application Insights](../azure-monitor/app/app-insights-overview.md).

## <a name="use-automation-to-streamline-processes"></a>Verwenden von Automatisierung zum Optimieren von Prozessen

Verwenden Sie automatisierte Workflows, um Warnungen zu einem allgemeinen Incident zu gruppieren und zu priorisieren und die zugehörige Priorität zu ändern.

Weitere Informationen finden Sie unter:

- [Sicherheitsorchestrierung, Automatisierung und Reaktion (Security Orchestration, Automation and Response, SOAR) in Microsoft Sentinel](automation-in-azure-sentinel.md)
- [Automatisieren der Bedrohungsabwehr mit Playbooks in Microsoft Sentinel](automate-responses-with-playbooks.md)
- [Automatisierung der Vorfallbehandlung in Microsoft Sentinel mit Automatisierungsregeln](automate-incident-handling-with-automation-rules.md)

## <a name="retire-your-legacy-siem"></a>Ausmustern einer Legacy-SIEM-Lösung

Verwenden Sie die folgende Checkliste, um sicherzustellen, dass Sie eine vollständige Migration zu Microsoft Sentinel durchgeführt haben und bereit sind, Ihre Legacy-SIEM-Lösung auszumustern:


|Bereitschaftsbereich  |Details  |
|---------|---------|
|**Bereitschaft der Technologie**     | **Überprüfen kritischer Daten**: Vergewissern Sie sich, dass alle Quellen und Warnungen in Microsoft Sentinel verfügbar sind. <br><br>**Archivieren aller Datensätze**: Speichern Sie kritische Datensätze zu früheren Incidents und Fällen (optional auch Rohdaten), um Archivdaten für die Organisation vorzuhalten.   |
|**Bereitschaft von Prozessen**     |  **Playbooks**: Aktualisieren Sie [Untersuchungs- und Hunting-Prozesse](investigate-cases.md) zur Umstellung auf Microsoft Sentinel.<br><br>**Metriken**: Vergewissern Sie sich, dass Sie alle wichtigen Metriken von Microsoft Sentinel abrufen können.<br><br>**Arbeitsmappen**: Erstellen Sie [benutzerdefinierte Arbeitsmappen](monitor-your-data.md), oder verwenden Sie integrierte Arbeitsmappenvorlagen, um schnell Erkenntnisse zu gewinnen, sobald Sie eine [Verbindung mit Datenquellen](connect-data-sources.md) herstellen.<br><br>**Incidents**: Vergewissern Sie sich, dass alle aktuellen Incidents auf das neue System übertragen werden, einschließlich der erforderlichen Quelldaten.        |
|**Bereitschaft von Personen**     |  **SOC-Analysten**: Sorgen Sie dafür, dass alle Mitarbeiter in Ihrem Team für die Nutzung von Microsoft Sentinel geschult sind und die Ausmusterung der Legacy-SIEM-Lösung befürworten.   |
|     |         |
## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich nach der Migration die Microsoft Sentinel-Ressourcen von Microsoft an, um Ihre Qualifikationen zu erweitern und Microsoft Sentinel optimal zu nutzen.

Erwägen Sie auch, den Bedrohungsschutz zu erhöhen, indem Sie Microsoft Sentinel neben [Microsoft 365 Defender](./microsoft-365-defender-sentinel-integration.md) und [Microsoft Defender für Cloud](../security-center/azure-defender.md) verwenden und so von [integriertem Bedrohungsschutz](https://www.microsoft.com/security/business/threat-protection) profitieren. Profitieren Sie von der Breite der Sichtbarkeit, die Microsoft Sentinel bietet, während Sie sich eingehender mit der detaillierten Bedrohungsanalyse befassen.

Weitere Informationen finden Sie unter:

- [Bewährte Methoden für Regelmigration](https://techcommunity.microsoft.com/t5/azure-sentinel/best-practices-for-migrating-detection-rules-from-arcsight/ba-p/2216417)
- [Webinar: Bewährte Methoden zum Konvertieren von Erkennungsregeln](https://www.youtube.com/watch?v=njXK1h9lfR4)
- [Sicherheitsorchestrierung, Automatisierung und Reaktion (Security Orchestration, Automation and Response, SOAR) in Microsoft Sentinel](automation-in-azure-sentinel.md)
- [Bessere Verwaltung von SOC mit Incidentmetriken](manage-soc-with-incident-metrics.md)
- [Microsoft Sentinel-Lernpfad](/learn/paths/security-ops-sentinel/)
- [SC-200 Microsoft Security Operations Analyst-Zertifizierung](/learn/certifications/exams/sc-200)
- [Microsoft Sentinel-Ninja-Training](https://techcommunity.microsoft.com/t5/azure-sentinel/become-an-azure-sentinel-ninja-the-complete-level-400-training/ba-p/1246310)
- [Nutzen von Microsoft Sentinel zur Untersuchung eines Angriffs auf eine Hybridumgebung](https://mslearn.cloudguides.com/guides/Investigate%20an%20attack%20on%20a%20hybrid%20environment%20with%20Azure%20Sentinel)
