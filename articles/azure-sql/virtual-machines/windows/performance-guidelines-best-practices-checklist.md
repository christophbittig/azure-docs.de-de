---
title: 'Checkliste: Bewährte Methoden und Richtlinien'
description: Bietet bewährte Methoden und Richtlinien für den Speicher, um die Leistung Ihrer SQL Server-Instanz auf Azure Virtual Machines (VM) zu optimieren.
services: virtual-machines-windows
documentationcenter: na
author: bluefooted
editor: ''
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: performance
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/01/2021
ms.author: pamela
ms.custom: contperf-fy21q3
ms.reviewer: mathoma
ms.openlocfilehash: 54830fd4052e6121551d6246d6b325e6036b4800
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551385"
---
# <a name="checklist-best-practices-for-sql-server-on-azure-vms"></a>Checkliste: Bewährte Methoden für SQL Server auf Azure-VMs
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Bietet bewährte Methoden und Richtlinien für den Speicher, um die Leistung Ihrer SQL Server-Instanz auf Azure Virtual Machines (VM) zu optimieren. 

Ausführliche Informationen finden Sie in den anderen Artikeln dieser Reihe: [Checkliste](performance-guidelines-best-practices-checklist.md), [VM-Größe](performance-guidelines-best-practices-vm-size.md), [Speicher](performance-guidelines-best-practices-storage.md), [Sicherheit](security-considerations-best-practices.md), [HADR-Konfiguration](hadr-cluster-best-practices.md), [Baseline erfassen](performance-guidelines-best-practices-collect-baseline.md). 

Aktivieren Sie [SQL-Bewertung für SQL Server auf Azure-VMs,](sql-assessment-for-sql-vm.md) und Ihr SQL Server wird anhand bekannter bewährter Methoden und Ergebnisse ausgewertet, die auf der [Seite SQL VM-Verwaltung](manage-sql-vm-portal.md) des Azure-Portals angezeigt werden.


## <a name="overview"></a>Übersicht

Es empfiehlt sich, beim Ausführen von SQL Server auf Azure Virtual Machines weiterhin die gleichen Optionen zur Optimierung der Datenbankleistung zu verwenden, die für SQL Server in lokalen Serverumgebungen gelten. Die Leistung einer relationalen Datenbank in einer öffentlichen Cloud hängt jedoch von vielen Faktoren ab, z. B. der Größe eines virtuellen Computers und der Konfiguration der Datenträger für Daten.

In der Regel kommt es zu einem Kompromiss zwischen einer Kostenoptimierung und einer Leistungsoptimierung. Diese Folge von bewährten Methoden zur Leistung konzentriert sich darauf, die *beste* Leistung für SQL Server auf Azure Virtual Machines zu erzielen. Wenn Ihre Workload weniger anspruchsvoll ist, sind möglicherweise nicht alle empfohlenen Optimierungen erforderlich. Berücksichtigen Sie bei der Evaluierung dieser Empfehlungen Ihre Leistungsanforderungen und -kosten sowie Ihre Workloadmuster.

## <a name="vm-size"></a>Größe des virtuellen Computers

Im folgenden finden Sie eine kurze Checkliste mit bewährten Methoden für die VM-Größe für die Ausführung Ihrer SQL Server auf Azure-VM: 

- Verwenden Sie VM-Größen mit vier oder mehr vCPUs, z. B. [Standard_M8-4ms](../../../virtual-machines/m-series.md), [E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series) und [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) oder höher. 
- Verwenden Sie VM-Größen mit [Arbeitsspeicheroptimierung](../../../virtual-machines/sizes-memory.md), um die bestmögliche Leistung für SQL Server-Workloads zu erzielen. 
- Die Serien [DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md), [Edsv4](../../../virtual-machines/edv4-edsv4-series.md), [M](../../../virtual-machines/m-series.md) und [Mv2](../../../virtual-machines/mv2-series.md) führen zu dem optimalen Verhältnis von Arbeitsspeicher zu virtuellen Kernen, das für OLTP-Workloads benötigt wird. Beide VMs der M-Serie bieten das beste Verhältnis von Arbeitsspeicher zu virtuellen Kernen für unternehmenskritische Workloads und sind zudem ideal für Data Warehouse-Workloads geeignet. 
- Für unternehmenskritische und Data Warehouse-Workloads ist unter Umständen ein höheres Verhältnis von Arbeitsspeicher zu virtuellen Kernen erforderlich. 
- Nutzen Sie die Azure Virtual Machines-Marketplace-Images, da die SQL Server-Einstellungen und Speicheroptionen für eine optimale SQL Server-Leistung konfiguriert sind. 
- Erfassen Sie die Leistungsmerkmale des Zielworkloads, und nutzen Sie sie zum Ermitteln der passenden VM-Größe für Ihr Unternehmen.
- Verwenden Sie das [SKU-Empfehlungstool](/sql/dma/dma-sku-recommend-sql-db) des [Datenmigrations-Assistenten](https://www.microsoft.com/download/details.aspx?id=53595), um die richtige VM-Größe für Ihre vorhandene SQL Server-Workload zu ermitteln.

Weitere Informationen finden Sie in den [bewährten Methoden für die VM-Größe](performance-guidelines-best-practices-vm-size.md). 

## <a name="storage"></a>Storage

Im folgenden finden Sie eine kurze Checkliste mit bewährten Methoden für die Speicherkonfiguration für die Ausführung Ihrer SQL Server auf Azure-VM: 

- Überwachen Sie die Anwendung, und [bestimmen Sie die Anforderungen an die Speicherbandbreite und Wartezeit](../../../virtual-machines/premium-storage-performance.md#counters-to-measure-application-performance-requirements) für SQL Server-Daten-, Protokoll- und tempdb-Dateien, bevor Sie den Datenträgertyp auswählen. 
- Um die Speicherleistung zu optimieren, planen Sie für die höchsten verfügbaren nicht zwischengespeicherten IOPS, und verwenden Sie Datenzwischenspeicherung als Leistungsmerkmal für Datenlesevorgänge, während Sie die [Begrenzung virtueller Computer und Datenträger](../../../virtual-machines/premium-storage-performance.md#throttling) vermeiden.
- Platzieren Sie Daten-, Protokoll- und tempdb-Dateien auf separaten Laufwerken.
    - Verwenden Sie für das Datenlaufwerk nur [Premium P30- und P40-Datenträger](../../../virtual-machines/disks-types.md#premium-ssds), um die Verfügbarkeit der Cacheunterstützung sicherzustellen.
    - Planen Sie für das Protokolllaufwerk die Kapazität und testen die Leistung im Verhältnis zu den Kosten, während Sie die [Premium-Datenträger P30-P80](../../../virtual-machines/disks-types.md#premium-ssds) bewerten.
      - Wenn eine Speicherwartezeit von weniger als einer Millisekunde erforderlich ist, verwenden Sie [Azure Ultra-Datenträger](../../../virtual-machines/disks-types.md#ultra-disks) für das Transaktionsprotokoll. 
      - Für Bereitstellungen von virtuellen Computern der M-Serie sollten Sie eine [Schreibbeschleunigung](../../../virtual-machines/how-to-enable-write-accelerator.md) der Verwendung von Azure-Ultra-Datenträgern vorziehen.
    - Platzieren Sie [tempdb](/sql/relational-databases/databases/tempdb-database) auf dem lokalen kurzlebigen SSD-Standardlaufwerk `D:\` für die meisten SQL Server-Workloads, nachdem Sie die optimale VM-Größe ausgewählt haben. 
      - Wenn die Kapazität des lokalen Laufwerks für „tempdb“ nicht ausreicht, können Sie die Vergrößerung der VM in Erwägung ziehen. Weitere Informationen finden Sie unter [Richtlinien für das Zwischenspeichern von Datendateien](performance-guidelines-best-practices-storage.md#data-file-caching-policies).
- Erstellen Sie ein Stripeset mehrerer Azure-Datenträger mithilfe von [Speicherplätzen](/windows-server/storage/storage-spaces/overview), um die E/A-Bandbreite bis zum IOPS- und Durchsatzlimit des virtuellen Zielcomputers zu erhöhen.
- Legen Sie [Hostzwischenspeicherung](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) für Datenträger mit Datendateien auf „Schreibgeschützt“ fest.
- Legen Sie [Hostzwischenspeicherung](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) für Protokolldateidatenträger auf „Keine“ fest.
    - Aktivieren Sie nicht das Zwischenspeichern von Lese-/Schreibzugriff auf Datenträgern mit SQL Server-Dateien. 
    - Vor dem Ändern der Cacheeinstellungen Ihres Datenträgers sollten Sie den SQL Server-Dienst immer anhalten.
- Für die Entwicklung und Test-Arbeitsauslastungen sollten Sie den Standardspeicher verwenden. Es wird nicht empfohlen, HDD Standard/SDD für Produktionsworkloads zu verwenden.
- [Auf Guthaben basierendes Datenträgerbursting](../../../virtual-machines/disk-bursting.md#credit-based-bursting) (P1-P20) sollte nur für kleinere Dev-/Test-Workloads und Abteilungssysteme in Betracht gezogen werden.
- Stellen Sie das Speicherkonto in derselben Region wie die SQL Server-VM bereit. 
- Deaktivieren Sie den georedundanten Azure-Speicher (Georeplikation), und verwenden Sie LRS (lokal redundanter Speicher) für das Speicherkonto.
- Formatieren Sie den Datenträger, um die Größe der Zuordnungseinheiten von 64 KB für alle Datendateien zu verwenden, die auf einem anderen Laufwerk als dem temporären Laufwerk `D:\` abgelegt werden (Standardwert: 4 KB). SQL Server über Azure Marketplace bereitgestellten virtuellen Computer verfügen über Datenträger, die mit der Größe der Zuordnungseinheit formatiert sind, und Interleave für den Speicherpool auf 64 KB. 


Weitere Informationen finden Sie in den [bewährten Methoden für den Speicher](performance-guidelines-best-practices-storage.md). 

## <a name="sql-server-features"></a>SQL Server-Funktionen

Im Folgenden finden Sie eine kurze Checkliste der Best Practices für SQL Server-Konfigurationseinstellungen, wenn Sie Ihre SQL Server-Instanzen auf einem virtuellen Azure-Computer in der Produktion ausführen: 

- Aktivieren Sie ggf. die [Datenbankseitenkomprimierung](/sql/relational-databases/data-compression/data-compression).
- Aktivieren Sie die [Sicherungskomprimierung](/sql/relational-databases/backup-restore/backup-compression-sql-server).
- Aktivieren Sie die [sofortige Dateiinitialisierung](/sql/relational-databases/databases/database-instant-file-initialization) für Datendateien.
- Begrenzen Sie die [automatische Vergrößerung](/troubleshoot/sql/admin/considerations-autogrow-autoshrink#considerations-for-autogrow) der Datenbank.
- Deaktivieren Sie die [automatische Verkleinerung](/troubleshoot/sql/admin/considerations-autogrow-autoshrink#considerations-for-auto_shrink) der Datenbank.
- Deaktivieren Sie das automatische Schließen der Datenbank.
- Verschieben Sie alle Datenbanken, einschließlich der [Systemdatenbanken](/sql/relational-databases/databases/move-system-databases) auf Datenträger für Daten.
- Verschieben Sie die Verzeichnisse für das SQL Server-Fehlerprotokoll und die Ablaufverfolgungsdateien auf die Datenträger für Daten.
- Konfigurieren Sie standardmäßige Dateispeicherorte für Sicherungen und Datenbanken.
- Legen Sie ein [Arbeitsspeicherlimit für SQL Server](/sql/database-engine/configure-windows/server-memory-server-configuration-options#use-) fest, damit dem Betriebssystem genügend Arbeitsspeicher zur Verfügung steht. ([Verwenden Sie „Arbeitsspeicher: Verfügbare Bytes“](/sql/relational-databases/performance-monitor/monitor-memory-usage), um den Arbeitsspeicherzustand des Betriebssystems zu überwachen.)
- Aktivieren Sie [Seiten im Arbeitsspeicher sperren](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows).
- Aktivieren Sie [Für Ad-hoc-Arbeitsauslastungen optimieren](/sql/database-engine/configure-windows/optimize-for-ad-hoc-workloads-server-configuration-option) für OLTP-Umgebungen mit hoher Auslastung.
- Evaluieren Sie die [aktuellen kumulativen Updates](/sql/database-engine/install-windows/latest-updates-for-microsoft-sql-server) für die installierte Version von SQL Server, und wenden Sie sie an.
- Aktivieren Sie [Abfragespeicher](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) für alle SQL Server-Produktionsdatenbanken [gemäß den Best Practices](/sql/relational-databases/performance/best-practice-with-the-query-store).
- Aktivieren Sie die [automatische Optimierung](/sql/relational-databases/automatic-tuning/automatic-tuning) für unternehmenskritische Anwendungsdatenbanken.
- Stellen Sie sicher, dass alle [Best Practices für tempdb](/sql/relational-databases/databases/tempdb-database#optimizing-tempdb-performance-in-sql-server) befolgt werden.
- Platzieren Sie tempdb auf dem kurzlebigen Laufwerk D:/.
- [Verwenden Sie die empfohlene Anzahl von Dateien](/troubleshoot/sql/performance/recommendations-reduce-allocation-contention#resolution). Beginnen Sie bei mehreren tempdb-Datendateien mit einer Datei pro Kern, und verwenden Sie nicht mehr als acht Dateien.
- Planen Sie SQL Server-Agent-Aufträge für die Ausführung von [DBCC CHECKDB](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql#a-checking-both-the-current-and-another-database)-, [index reorganize](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes#reorganize-an-index)-, [index rebuild](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes#rebuild-an-index)- und [update statistics](/sql/t-sql/statements/update-statistics-transact-sql#examples)-Vorgängen.
- Überwachen und verwalten Sie die Integrität und Größe der SQL Server-[Transaktionsprotokolldatei](/sql/relational-databases/logs/manage-the-size-of-the-transaction-log-file#Recommendations).
- Nutzen Sie die neuen [SQL Server-Features](/sql/sql-server/what-s-new-in-sql-server-ver15), die für die verwendete Version verfügbar sind.
- Beachten Sie die Unterschiede bei den [unterstützten Features](/sql/sql-server/editions-and-components-of-sql-server-version-15) der jeweiligen Editionen, die Sie bereitstellen möchten.

## <a name="azure-features"></a>Azure-Features

Im Folgenden finden Sie eine kurze Checkliste der Best Practices zur Azure-spezifischen Anleitung bei der Ausführung Ihrer SQL Server-Instanzen auf Azure-VM:

- Registrieren Sie sich für die [SQL Server-IaaS-Agent-Erweiterung](sql-agent-extension-manually-register-single-vm.md), um eine Reihe von [Featurevorteilen](sql-server-iaas-agent-extension-automate-management.md#feature-benefits) zu nutzen.
- Nutzen Sie die beste [Sicherungs- und Wiederherstellungsstrategie](backup-restore.md#decision-matrix) für Ihre SQL Server-Workload.
- Stellen Sie sicher, dass der [beschleunigte Netzwerkbetrieb](../../../virtual-network/create-vm-accelerated-networking-cli.md#portal-creation) auf dem virtuellen Computer aktiviert ist.
- Nutzen Sie [Microsoft Defender für Cloud](../../../security-center/index.yml), um den Sicherheitsstatus Ihres bereitgestellten virtuellen Computers insgesamt zu verbessern.
- Nutzen Sie [Microsoft Defender für Cloud](../../../security-center/azure-defender.md) in [Microsoft Defender für Cloud](https://azure.microsoft.com/services/security-center/) für bestimmte [SQL Server-VM-Abdeckungen](../../../security-center/defender-for-sql-introduction.md), einschließlich Sicherheitsrisikobewertungen und Just-in-Time-Zugriff, wodurch der Angriffsdienst reduziert wird, während berechtigte Benutzer bei Bedarf auf virtuelle Computer zugreifen können. Weitere Informationen finden Sie unter [Sicherheitsrisikobewertungen](../../../security-center/defender-for-sql-on-machines-vulnerability-assessment.md), [Aktivieren von Sicherheitsrisikobewertungen für SQL Server-VMs](../../../security-center/defender-for-sql-on-machines-vulnerability-assessment.md) und [Just-in-Time-Zugriff](../../../security-center/just-in-time-explained.md). 
- Verwenden Sie [Azure Advisor](../../../advisor/advisor-overview.md), um Empfehlungen für [Leistung](../../../advisor/advisor-performance-recommendations.md), [Kosten](../../../advisor/advisor-cost-recommendations.md), [Zuverlässigkeit](../../../advisor/advisor-high-availability-recommendations.md), [Operational Excellence](../../../advisor/advisor-operational-excellence-recommendations.md) und [Sicherheit](../../../advisor/advisor-security-recommendations.md) zu erfüllen.
- Verwenden Sie [Azure Monitor](../../../azure-monitor/vm/monitor-virtual-machine.md), um Telemetriedaten aus Ihrer SQL Server-Umgebung zu sammeln, zu analysieren und darauf zu reagieren. Dies umfasst das Identifizieren von Infrastrukturproblemen mit [VM-Erkenntnissen](../../../azure-monitor/vm/vminsights-overview.md) und das Überwachen von Daten mit [Log Analytics](../../../azure-monitor/logs/log-query-overview.md) für eine ausführlichere Diagnose.
- Aktivieren Sie die Option [Automatisches Herunterfahren](../../../automation/automation-solution-vm-management.md) für Entwicklungs- und Testumgebungen. 
- Implementieren Sie eine HADR-Lösung (High Availability and Disaster Recovery), die Ihren SLAs für Geschäftskontinuität entspricht. Weitere Informationen finden Sie unter [HADR-Optionen](business-continuity-high-availability-disaster-recovery-hadr-overview.md#deployment-architectures), die für SQL Server auf Azure-VMs verfügbar sind. 
- Verwenden Sie das Azure-Portal (Support und Problembehandlung), um die Integrität und den Verlauf von [Ressourcen](../../../service-health/resource-health-overview.md) zu bewerten. Übermitteln Sie bei Bedarf neue Supportanfragen.

## <a name="hadr-configuration"></a>HADR-Konfiguration

HADR-Features (High Availability and Disaster Recovery) wie die [AlwaysOn-Verfügbarkeitsgruppe](availability-group-overview.md) und die [Failoverclusterinstanz](failover-cluster-instance-overview.md) basieren auf der zugrunde liegenden [Windows Server-Failovercluster](hadr-windows-server-failover-cluster-overview.md)-Technologie. Sehen Sie sich die bewährten Methoden zum Ändern Ihrer HADR-Einstellungen an, um die Cloudumgebung besser zu unterstützen. 

Erwägen Sie für Ihren Windows-Cluster die folgenden bewährten Methoden: 

* Stellen Sie Ihre SQL Server-VMs nach Möglichkeit in mehreren Subnetzen bereit, um die Abhängigkeit von einem Azure Load Balancer oder einem verteilten Netzwerknamen (DNN) zur Weiterleitung des Datenverkehrs an Ihre HADR-Lösung zu vermeiden. 
* Ändern Sie den Cluster in weniger aggressive Parameter, um unerwartete Ausfälle durch vorübergehende Netzwerkfehler oder Wartung der Azure-Plattform zu vermeiden. Weitere Informationen finden Sie unter [Heartbeat- und Schwellenwerteinstellungen](hadr-cluster-best-practices.md#heartbeat-and-threshold). Verwenden Sie für Windows Server 2012 und höher die folgenden Werte: 
   - **SameSubnetDelay**: 1 Sekunde
   - **SameSubnetThreshold**: 40 Heartbeats
   - **CrossSubnetDelay**: 1 Sekunde
   - **CrossSubnetThreshold**: 40 Heartbeats
* Platzieren Sie Ihre VMs in einer Verfügbarkeitsgruppe oder verschiedenen Verfügbarkeitszonen.  Weitere Informationen finden Sie unter [VM-Verfügbarkeitseinstellungen](hadr-cluster-best-practices.md#vm-availability-settings). 
* Verwenden Sie eine einzelne NIC pro Clusterknoten und ein einzelnes Subnetz. 
* Konfigurieren Sie die [Clusterquorumabstimmung](hadr-cluster-best-practices.md#quorum-voting) so, dass eine ungerade Anzahl von mindestens drei Stimmen verwendet wird. Weisen Sie keine Stimmen zu DR-Regionen zu. 
* Überwachen Sie [Ressourcengrenzwerte](hadr-cluster-best-practices.md#resource-limits) sorgfältig, um unerwartete Neustarts oder Failover aufgrund von Ressourceneinschränkungen zu vermeiden.
   - Stellen Sie sicher, dass Betriebssystem, Treiber und SQL Server den neuesten Builds entsprechen. 
   - Optimieren Sie die Leistung für SQL Server auf Azure-VMs. Weitere Informationen finden Sie in den anderen Abschnitten dieses Artikels. 
   - Reduzieren oder verteilen Sie die Workload, um Ressourcengrenzwerte zu vermeiden. 
   - Wechseln Sie zu einer VM oder einem Datenträger mit höheren Grenzwerten, um Einschränkungen zu vermeiden. 

Erwägen Sie für die SQL Server Verfügbarkeitsgruppe oder Failoverclusterinstanz die folgenden bewährten Methoden: 

* Wenn häufig unerwartete Fehler auftreten, befolgen Sie die leistungsbezogenen bewährten Methoden, die im restlichen Teil dieses Artikels beschrieben werden. 
* Falls sich die unerwarteten Failover durch Optimierung der Leistung der SQL Server-VMs nicht beheben lassen, erwägen Sie eine [Lockerung der Überwachung](hadr-cluster-best-practices.md#relaxed-monitoring) für die Verfügbarkeitsgruppe oder Failoverclusterinstanz. Dadurch wird jedoch möglicherweise nicht die zugrunde liegende Ursache des Problems behoben, und durch Verringerung der Fehlerwahrscheinlichkeit können Symptome maskiert werden. Möglicherweise müssen Sie die zugrunde liegende Ursache dennoch untersuchen und beheben. Verwenden Sie für Windows Server 2012 oder höher die folgenden empfohlenen Werte: 
   - **Leasetimeout**:Verwenden Sie diese Gleichung, um den maximalen Leasetimeoutwert zu berechnen:   
    `Lease timeout < (2 * SameSubnetThreshold * SameSubnetDelay)`.    
    Beginnen Sie mit 40 Sekunden. Wenn Sie die zuvor empfohlenen gelockerten `SameSubnetThreshold`- und `SameSubnetDelay`-Werte verwenden, darf der Leasetimeoutwert 80 Sekunden nicht überschreiten. 
   - **Maximale Anzahl von Fehlern in einem angegebenen Zeitraum**: Legen Sie diesen Wert auf 6 fest.
   - **Timeout für die Integritätsprüfung**: Legen Sie diesen Wert zunächst auf 60.000 fest, und passen Sie ihn bei Bedarf an. 
* Wenn Sie den virtuellen Netzwerknamen (VNN) und Azure Load Balancer verwenden, um sich mit Ihrer HADR-Lösung zu verbinden, geben Sie `MultiSubnetFailover = true` in der Verbindungszeichenfolge an, auch wenn sich Ihr Cluster nur über ein Subnetz erstreckt. 
   - Wenn der Client `MultiSubnetFailover = True` nicht unterstützt, müssen Sie möglicherweise `RegisterAllProvidersIP = 0` und `HostRecordTTL = 300` festlegen, um Clientanmeldeinformationen für kürzere Zeiträume zwischenzuspeichern. Dies kann jedoch zu zusätzlichen Abfragen an den DNS-Server führen. 
- Beim Herstellen einer Verbindung mit der HADR-Lösung mithilfe des Namens des verteilten Netzwerks (Distributed Network Name, DNN) ist Folgendes zu beachten:
   - Sie müssen einen Clienttreiber verwenden, der `MultiSubnetFailover = True` unterstützt, und dieser Parameter muss in der Verbindungszeichenfolge enthalten sein. 
   - Verwenden Sie einen eindeutigen DNN-Port in der Verbindungszeichenfolge, wenn Sie eine Verbindung mit dem DNN-Listener für eine Verfügbarkeitsgruppe herstellen. 
- Verwenden Sie eine Verbindungszeichenfolge für Datenbankspiegelung für eine Basic-Verfügbarkeitsgruppe, um die Notwendigkeit eines Lastenausgleichs oder DNN zu umgehen. 
- Überprüfen Sie die Sektorgröße Ihrer VHDs, bevor Sie Ihre Hochverfügbarkeitslösung bereitstellen, um falsch ausgerichtete E/A zu vermeiden. Weitere Informationen finden Sie unter [KB3009974](https://support.microsoft.com/topic/kb3009974-fix-slow-synchronization-when-disks-have-different-sector-sizes-for-primary-and-secondary-replica-log-files-in-sql-server-ag-and-logshipping-environments-ed181bf3-ce80-b6d0-f268-34135711043c). 


Weitere Informationen finden Sie in den umfassenden [bewährten Methoden für HADR](hadr-cluster-best-practices.md). 


## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich auch die anderen Artikel in dieser Reihe an, um mehr zu erfahren:

- [Größe des virtuellen Computers](performance-guidelines-best-practices-vm-size.md)
- [Storage](performance-guidelines-best-practices-storage.md)
- [Security](security-considerations-best-practices.md)
- [HADR-Einstellungen](hadr-cluster-best-practices.md)
- [Baseline auswählen](performance-guidelines-best-practices-collect-baseline.md)

Bewährte Methoden für die Sicherheit finden Sie unter [Sicherheitsüberlegungen für SQL Server auf Azure Virtual Machines](security-considerations-best-practices.md).

Erwägen Sie die Aktivierung von [SQL-Bewertung für SQL Server auf Azure-VMs](sql-assessment-for-sql-vm.md).

Weitere Artikel zu virtuellen SQL Server-Computern finden Sie unter [Übersicht zu SQL Server auf virtuellen Azure-Computern](sql-server-on-azure-vm-iaas-what-is-overview.md). Falls Sie Fragen zu SQL Server-VMs haben, finden Sie in den [häufig gestellten Fragen](frequently-asked-questions-faq.yml) weitere Informationen.
