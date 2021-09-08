---
title: 'Migrieren einer lokalen MySQL-Instanz zu Azure Database for MySQL: Optimierung'
description: Die Serverleistung kann nicht nur mit den Überwachungs- und Aktivitätsprotokollen überwacht werden, sondern auch mit Azure-Metriken.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: 4a1f279ad8eb81891a184700b1076bcec89b92e8
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114296200"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-optimization"></a>Migrieren einer lokalen MySQL-Instanz zu Azure Database for MySQL: Optimierung

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="prerequisites"></a>Voraussetzungen

[Verwaltung nach der Migration](10-post-migration-management.md)

## <a name="monitoring-hardware-and-query-performance"></a>Überwachen der Hardware- und Abfrageleistung

Die Serverleistung kann nicht nur mit den Überwachungs- und Aktivitätsprotokollen überwacht werden, sondern auch mit [Azure-Metriken](../../../azure-monitor/essentials/data-platform-metrics.md). Azure-Metriken werden im 1-Minuten-Takt bereitgestellt, und auf der Grundlage dieser Metriken können Warnungen konfiguriert werden. Ausführliche Informationen dazu, welche Art von Metriken überwacht werden können, finden Sie unter [Überwachen in Azure Database for MySQL](../../concepts-monitoring.md).

Wie bereits erwähnt können Überwachungsmetriken wie „cpu\_percent“ oder „memory\_percent“ wichtig sein, wenn Sie den Tarif für die Datenbank upgraden möchten. Dauerhaft hohe Werte können darauf hindeuten, dass ein Ebenenupgrade erforderlich ist.

Falls die CPU und der Arbeitsspeicher scheinbar nicht die Ursache des Problems sind, können Administratoren auch die Verwendung von datenbankbasierten Optionen erwägen, z. B. Indizierung und Änderungen von Abfragen mit schlechter Leistung.

Führen Sie Folgendes aus, um Abfragen mit schlechter Leistung zu ermitteln:

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.DBFORMYSQL"
| where Category == 'MySqlSlowLogs'
| project TimeGenerated, LogicalServerName\_s, 
event\_class\_s, start\_time\_t , q uery\_time\_d, 
sql\_text\_s| top 5 by query\_time\_d desc
```

## <a name="query-performance-insight"></a>Query Performance Insight

Zusätzlich zu den Funktionen für die grundlegenden Aspekte der Serverüberwachung verfügt Azure auch über Tools zum Überwachen der Leistung von Anwendungsabfragen. Die Korrektur bzw. Verbesserung von Abfragen kann zu einem erheblichen Anstieg des Abfragedurchsatzes führen. Verwenden Sie das [Tool „Query Performance Insight“](../../concepts-query-performance-insight.md), um die Abfragen mit der längsten Ausführungsdauer zu analysieren und zu ermitteln, ob diese Elemente zwischengespeichert werden können, falls sie innerhalb eines festgelegten Zeitraums deterministisch sind. Sie können die Abfragen auch ändern, um die Leistung zu steigern.

`slow\_query\_log` kann so festgelegt werden, dass langsame Abfragen in den MySQL-Protokolldateien angezeigt werden (Standardeinstellung: AUS). Mit dem Serverparameter `long\_query\_time` können Benutzer vor langen Abfragedauern gewarnt werden (Standardeinstellung: 10 Sekunden).

## <a name="upgrading-the-tier"></a>Durchführen eines Upgrades für den Tarif

Das Azure-Portal kann verwendet werden, um zwischen „Universell“ (`General Purpose`) und „Arbeitsspeicheroptimiert“ (`Memory Optimized`) zu skalieren. Wenn der Tarif `Basic` ausgewählt wird, ist keine Option vorhanden, mit der später für den Tarif das Upgrade auf `General Purpose` oder `Memory Optimized` durchgeführt werden kann. Sie können aber andere Verfahren nutzen, um eine Migration zu einer neuen bzw. ein Upgrade auf eine neue Azure Database for MySQL-Instanz durchzuführen.

Ein Beispiel für ein Skript, das die Migration von „Basic“ zu einem anderen Servertarif ermöglicht, finden Sie unter [Upgrade vom Tarif „Basic“ auf „Universell“ oder „Arbeitsspeicheroptimiert“ in Azure Database for MySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/upgrade-from-basic-to-general-purpose-or-memory-optimized-tiers/ba-p/830404).

## <a name="scale-the-server"></a>Skalieren des Servers

Innerhalb des Tarif ist es möglich, die Kerne und den Arbeitsspeicher auf die minimalen und maximalen Grenzwerte zu skalieren, die für den Tarif zulässig sind. Befolgen Sie die Schritte zum [Hochskalieren zur Erfüllung Ihres Bedarfs](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/upgrade-from-basic-to-general-purpose-or-memory-optimized-tiers/ba-p/830404), falls im Rahmen der Überwachung angezeigt wird, dass für die CPU oder den Arbeitsspeicher ständig der Höchstwert erreicht wird.

## <a name="moving-regions"></a>Verschieben in andere Regionen

Das Verschieben einer Datenbank in eine andere Azure-Region ist vom jeweiligen Ansatz und von der Architektur abhängig. Je nach Ansatz kann es für das System hierbei auch zu Downtime kommen.

Der empfohlene Prozess entspricht der Verwendung von Lesereplikaten für Wartungsfailover. Im Vergleich zum oben erwähnten Verfahren für die geplante Wartung ist die Geschwindigkeit des Failovers aber deutlich höher, wenn in der Anwendung eine Failoverebene implementiert wurde. Während des Failoverprozesses mit Lesereplikaten sollte es für die Anwendung nur zu einem sehr kurzen Ausfall kommen. Weitere Informationen finden Sie im Abschnitt „Business Continuity & Disaster Recovery (BCDR)“.

## <a name="wwi-scenario"></a>WWI-Szenario

WWI-Geschäfts- und -Anwendungsbenutzer haben die Nachricht, dass die Datenbank bedarfsabhängig skaliert werden kann, sehr erfreut aufgenommen. Sie waren auch daran interessiert, über Query Performance Insight ermitteln zu können, ob in Bezug auf die Leistung von zeitintensiven Abfragen etwas unternommen werden muss.

Die Benutzer haben sich für die Verwendung eines Lesereplikatservers für alle potenziellen Failover- oder Schreibschutzszenarien entschieden.

Das Migrationsteam hat in Zusammenarbeit mit den Azure-Technikern KQL-Abfragen eingerichtet, um potenzielle Probleme überwachen zu können, die im Hinblick auf die MySQL-Serverleistung ggf. auftreten. Für die KQL-Abfragen wurden Warnungen eingerichtet, bei denen Ereignisprobleme per E-Mail an das Datenbank- und das Konferenzteam gesendet werden.

Es wurde die Entscheidung getroffen, zunächst alle potenziellen Probleme zu überwachen und Azure Automation-Runbooks bei Bedarf später zu implementieren, um die betriebliche Effizienz zu verbessern.

## <a name="optimization-checklist"></a>Prüfliste für die Optimierung

  - Führen Sie eine Überwachung auf langsame Abfragen durch.

  - Überprüfen Sie in regelmäßigen Abständen das Performance Insight-Dashboard.

  - Nutzen Sie die Erkenntnisse aus der Überwachung, um Entscheidungen in Bezug auf Tarifupgrades und Skalierungen zu treffen.

  - Erwägen Sie eine Verschiebung in andere Regionen, falls für die Benutzer oder die Anwendung eine Änderung erforderlich ist.  


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Business Continuity & Disaster Recovery (BCDR)](./12-business-continuity-and-disaster-recovery.md)