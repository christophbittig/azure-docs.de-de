---
title: Dienstebenen „Universell“ und „Unternehmenskritisch“
titleSuffix: Azure SQL Database & SQL Managed Instance
description: In diesem Artikel werden die Dienstebenen „Universell“ und „Unternehmenskritisch“ im vCore-basierten Kaufmodell erläutert, das von Azure SQL-Datenbank und Azure SQL Managed Instance verwendet wird.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service-overview
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: mathoma
ms.date: 11/02/2021
ms.openlocfilehash: 716c425958a457b45736835029b90567c090d4a9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131446978"
---
# <a name="azure-sql-database-and-azure-sql-managed-instance-service-tiers"></a>Dienstebenen für Azure SQL-Datenbank und Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

 Sowohl in Azure SQL-Datenbank als auch in Azure SQL Managed Instance sind zwei [V-Kern](service-tiers-vcore.md)-Dienstebenen verfügbar:

- [Universell](service-tier-general-purpose.md) ist eine budgetfreundliche Ebene, die für die meisten Workloads mit üblichen Leistungs- und Verfügbarkeitsanforderungen konzipiert ist.
- [Unternehmenskritisch](service-tier-business-critical.md) ist für leistungsabhängige Workloads mit strengen Verfügbarkeitsanforderungen konzipiert.

Azure SQL-Datenbank bietet auch die Dienstebene „Hyperscale“: 

- [Hyperscale](service-tier-hyperscale.md) ist für die meisten Geschäftsworkloads konzipiert und bietet hochgradig skalierbaren Speicher, horizontale Leseskalierung, Schnellskalierung und schnelle Datenbankwiederherstellungsfunktionen.

Einen Vergleich zwischen vCore-basiertem Kaufmodell und DTU-basiertem Kaufmodell finden Sie unter [Entscheiden zwischen dem vCore-basierten und dem DTU-basierten Kaufmodell – Azure SQL-Datenbank und SQL Managed Instance](purchasing-models.md).

## <a name="service-tier-comparison"></a>Vergleich der Dienstebenen

In der folgenden Tabelle sind die wichtigsten Unterschiede zwischen den Dienstebenen beschrieben.

|-| Ressourcentyp | Universell | Hyperscale | Unternehmenskritisch |
|:---:|:---:|:---:|:---:|:---:|
| **Am besten geeignet für** | |  Bietet budgetorientierte ausgewogene Compute- und Speicheroptionen. | Die meisten geschäftlichen Workloads. Automatische Skalierung der Speichergröße bis zu 100 TB, fluide vertikale und horizontale Computeskalierung, schnelle Datenbankwiederherstellung. | OLTP-Anwendungen mit hoher Transaktionsrate und geringen Latenzen bei E/A-Vorgängen. Bietet mit mehreren synchron aktualisierten Replikaten höchste Resilienz gegenüber Fehlern und schnelle Failover.|
| **Verfügbar in Ressourcentyp:** ||SQL-Datenbank/SQL Managed Instance | Azure SQL-Einzeldatenbank | SQL-Datenbank/SQL Managed Instance |
| **Computegröße**| SQL-Datenbank | 1 bis 80 virtuelle Kerne | 1 bis 80 virtuelle Kerne | 1 bis 128 virtuelle Kerne |
| | Verwaltete SQL-Instanz | 4, 8, 16, 24, 32, 40, 64, 80 virtuelle Kerne | – | 4, 8, 16, 24, 32, 40, 64, 80 virtuelle Kerne |
| | SQL Managed Instance-Pools | 2, 4, 8, 16, 24, 32, 40, 64, 80 virtuelle Kerne | – | – |
| **Speichertyp** | All | Remotespeicher | Mehrstufiger Remotespeicher und lokaler SSD-Speicher | Lokaler SSD-Speicher |
| **Datenbankgröße** | SQL-Datenbank | 1 GB – 4 TB | 40 GB – 100 TB | 1 GB – 4 TB |
| | Verwaltete SQL-Instanz  | 32 GB – 8 TB </br> 16 TB (Vorschau) je nach Anzahl der Kerne, nur Gen5| – | 32 GB – 4 TB |
| **Speichergröße** | SQL-Datenbank | 1 GB – 4 TB | 40 GB – 100 TB | 1 GB – 4 TB |
| | Verwaltete SQL-Instanz  | 32 GB – 8 TB | – | 32 GB – 4 TB |
| **TempDB-Größe** | SQL-Datenbank | [32 GB pro virtuellem Kern](resource-limits-vcore-single-databases.md) | [32 GB pro virtuellem Kern](resource-limits-vcore-single-databases.md) | [32 GB pro virtuellem Kern](resource-limits-vcore-single-databases.md) |
| | Verwaltete SQL-Instanz  | [24 GB pro virtuellem Kern](../managed-instance/resource-limits.md#service-tier-characteristics) | – | Bis zu 4 TB – [begrenzt durch Speichergröße](../managed-instance/resource-limits.md#service-tier-characteristics) |
| **Protokollschreibdurchsatz** | SQL-Datenbank | Einzeldatenbanken: [4,5 MB/s pro virtuellem Kern (max. 50 MB/s)](resource-limits-vcore-single-databases.md) <br> Pools für elastische Datenbanken: [6 MB/s pro virtuellem Kern (max. 62,5 MB/s)](resource-limits-vcore-elastic-pools.md)| 100 MB/s | Einzeldatenbanken: [12 MB/s pro virtuellem Kern (max. 96 MB/s)](resource-limits-vcore-single-databases.md) <br> Pools für elastische Datenbanken: [15 MB/s pro virtuellem Kern (max. 120 MB/s)](resource-limits-vcore-elastic-pools.md)|
| | Verwaltete SQL-Instanz | [3 MB/Sek. pro virtuellem Kern (max. 22 MB/s)](../managed-instance/resource-limits.md#service-tier-characteristics) | – | [4 MB/s pro virtuellem Kern (max. 48 MB/s)](../managed-instance/resource-limits.md#service-tier-characteristics) |
|**Verfügbarkeit**|SQL-Datenbank ([SLA](https://azure.microsoft.com/support/legal/sla/azure-sql-database/))| 99,99 % | [99,95 % mit einem sekundären Replikat, 99,99 % mit weiteren Replikaten](service-tier-hyperscale-frequently-asked-questions-faq.yml#what-slas-are-provided-for-a-hyperscale-database-) | 99,99 % <br/> [99,995 % mit zonenredundantem Singleton](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
| |SQL Managed Instance ([SLA](https://azure.microsoft.com/support/legal/sla/azure-sql-sql-managed-instance/))| 99,99 % |  [99,95 % mit einem sekundären Replikat, 99,99 % mit weiteren Replikaten](service-tier-hyperscale-frequently-asked-questions-faq.yml#what-slas-are-provided-for-a-hyperscale-database-) | 99,99 % <br/> [99,995 % mit zonenredundantem Singleton](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
|**Sicherungen**|All|RA-GRS, 1–35 Tage (standardmäßig 7 Tage) | RA-GRS, 7 Tage, schnelle Zeitpunktwiederherstellung (Point-in-Time Recovery, PITR) | RA-GRS, 1–35 Tage (standardmäßig 7 Tage) |
|**In-Memory-OLTP** | | – | Teilweise unterstützt. Speicheroptimierte Tabellentypen, Tabellenvariablen und nativ kompilierte Module werden unterstützt. | Verfügbar |
|**Schreibgeschützte Replikate**| | 0: Integriert <br> 0–4: Verwendung von [Georeplikation](active-geo-replication-overview.md) | 0–4: Integriert | 1: Integriert, im Preis inbegriffen <br> 0–4: Verwendung von [Georeplikation](active-geo-replication-overview.md) |
|**Preise/Abrechnung** | SQL-Datenbank | [Virtueller Kern, reservierter Speicher und Sicherungsspeicher](https://azure.microsoft.com/pricing/details/sql-database/single/) werden in Rechnung gestellt. <br/>IOPS werden nicht in Rechnung gestellt. | [Virtueller Kern für jedes Replikat und den verwendeten Speicher](https://azure.microsoft.com/pricing/details/sql-database/single/) wird in Rechnung gestellt. <br/>IOPS noch nicht in Rechnung gestellt. | [Virtueller Kern, reservierter Speicher und Sicherungsspeicher](https://azure.microsoft.com/pricing/details/sql-database/single/) werden in Rechnung gestellt. <br/>IOPS werden nicht in Rechnung gestellt. |
|| Verwaltete SQL-Instanz | [Virtueller Kern, reservierter Speicher und Sicherungsspeicher](https://azure.microsoft.com/pricing/details/sql-database/managed/) werden in Rechnung gestellt. <br/>IOPS werden nicht in Rechnung gestellt.| – | [Virtueller Kern, reservierter Speicher und Sicherungsspeicher](https://azure.microsoft.com/pricing/details/sql-database/managed/) werden in Rechnung gestellt. <br/>IOPS werden nicht in Rechnung gestellt.| 
|**Rabattmodelle**| | [Reservierte Instanzen](reserved-capacity-overview.md)<br/>[Azure-Hybridvorteil](../azure-hybrid-benefit.md) (nicht verfügbar für Dev/Test-Abonnements)<br/>[Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/)- und [Pay-as-you-Go](https://azure.microsoft.com/offers/ms-azr-0023p/)-Dev/Test-Abonnements| [Azure-Hybridvorteil](../azure-hybrid-benefit.md) (nicht verfügbar für Dev/Test-Abonnements)<br/>[Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/)- und [Pay-as-you-Go](https://azure.microsoft.com/offers/ms-azr-0023p/)-Dev/Test-Abonnements| [Reservierte Instanzen](reserved-capacity-overview.md)<br/>[Azure-Hybridvorteil](../azure-hybrid-benefit.md) (nicht verfügbar für Dev/Test-Abonnements)<br/>[Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/)- und [Pay-as-you-Go](https://azure.microsoft.com/offers/ms-azr-0023p/)-Dev/Test-Abonnements|

> [!NOTE]
> Weitere Informationen zur Vereinbarung zum Servicelevel (Service Level Agreement, SLA) finden Sie unter [SLA für Azure SQL-Datenbank](https://azure.microsoft.com/support/legal/sla/azure-sql-database/) oder [SLA für Azure SQL Managed Instance](https://azure.microsoft.com/support/legal/sla/azure-sql-sql-managed-instance/).

### <a name="resource-limits"></a>Ressourceneinschränkungen

Weitere Informationen zu Ressourcenbeschränkungen finden Sie unter:

 - [Azure SQL-Datenbank (virtueller Kern)](resource-limits-vcore-single-databases.md)
 - [Azure SQL-Einzeldatenbank (DTU)](resource-limits-dtu-single-databases.md)
 - [Azure SQL-Pooldatenbank (DTU)](resource-limits-dtu-single-databases.md)
 - [Verwaltete Azure SQL-Datenbank-Instanz](../managed-instance/resource-limits.md)

## <a name="data-and-log-storage"></a>Daten- und Protokollspeicher

Die folgenden Faktoren wirken sich darauf aus, wie viel Speicherplatz für Daten- und Protokolldateien verwendet wird. Dies gilt für die Dienstebenen „Universell“ und „Unternehmenskritisch“. Ausführliche Informationen zu Daten und zum Protokollspeicher in Hyperscale finden Sie unter [Hyperscale-Dienstebene](service-tier-hyperscale.md).

- Jede Computegröße unterstützt eine maximale Datengröße, die standardmäßig bei 32 GB liegt.
- Wenn Sie die maximale Datengröße konfigurieren, werden automatisch 30 Prozent zusätzlicher Speicher für Protokolldateien hinzugefügt.
- Sie können eine beliebige maximale Datengröße zwischen 1 GB und der unterstützten maximalen Speichergröße in Schritten von 1 GB auswählen.
- In der Dienstebene „Universell“ wird für `tempdb` lokaler SSD-Speicher verwendet, und diese Speicherkosten sind im V-Kern-Preis enthalten.
- In der Dienstebene „Unternehmenskritisch“ wird für `tempdb` lokaler SSD-Speicher für Daten- und Protokolldateien gemeinsam genutzt, und `tempdb`-Speicherkosten sind im V-Kern-Preis enthalten.
- Die maximale Speichergröße für eine Instanz von SQL Managed Instance muss als Vielfaches von 32 GB angegeben werden.

> [!IMPORTANT]
> In den Dienstebenen „Universell“ und „Unternehmenskritisch“ wird Ihnen die maximale Speichergröße in Rechnung gestellt, die für eine Datenbank, einen Pool für elastische Datenbanken oder eine verwaltete Instanz konfiguriert ist. In der Dienstebene „Hyperscale“ wird Ihnen der zugeordnete Datenspeicher in Rechnung gestellt.

Wenn Sie die aktuell zugeordnete und verwendete Datenspeichergröße in SQL-Datenbank überwachen möchten, verwenden Sie die Azure Monitor-[Metriken](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserversdatabases) *allocated_data_storage* bzw. *storage*. Zum Überwachen der insgesamt verbrauchten Instanzspeichergröße für SQL Managed Instance verwenden Sie die [Metrik](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlmanagedinstances) *storage_space_used_mb*. Wenn Sie die aktuell zugeordnete und verwendete Speichergröße einzelner Daten- und Protokolldateien in einer Datenbank mit T-SQL überwachen möchten, verwenden Sie die Ansicht [sys.database_files](/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql) und die Funktion [FILEPROPERTY(... , 'SpaceUsed')](/sql/t-sql/functions/fileproperty-transact-sql).

> [!TIP]
> Unter bestimmten Umständen müssen Sie ggf. eine Datenbank verkleinern, um ungenutzten Speicherplatz freizugeben. Weitere Informationen finden Sie unter [Verwalten von Dateispeicherplatz in Azure SQL-Datenbank](file-space-manage.md).

## <a name="backups-and-storage"></a>Sicherungen und Speicher

Der Speicher für Datenbanksicherungen wird zugeordnet, um die Funktionen [Zeitpunktwiederherstellung](recovery-using-backups.md) (Point in Time Restore, PITR) und [Langzeitaufbewahrung](long-term-retention-overview.md) (Long Term Retention, LTR) von SQL-Datenbank und SQL Managed Instance zu unterstützen. Dieser Speicher ist vom Daten- und Protokolldateispeicher getrennt und wird separat abgerechnet.

- **PITR**: In den Dienstebenen „Universell“ und „Unternehmenskritisch“ werden einzelne Datenbanksicherungen automatisch in den [georedundanten Speicher mit Lesezugriff](../../storage/common/geo-redundant-design.md) (Read-Access Geo-Redundant Storage, RA-GRS) kopiert. Die Speichergröße wird dynamisch erhöht, wenn neue Sicherungen erstellt werden. Der Speicher wird für vollständige, differenzielle und Transaktionsprotokollsicherungen verwendet. Der Speicherverbrauch richtet sich nach der Änderungsrate der Datenbank und nach dem für Sicherungen konfigurierten Aufbewahrungszeitraum. Sie können für jede Datenbank einen separaten Aufbewahrungszeitraum zwischen 1 und 35 Tagen für SQL-Datenbank und zwischen 0 und 35 Tagen für SQL Managed Instance konfigurieren. Eine Sicherungsspeichermenge, die der konfigurierten maximalen Datengröße entspricht, wird ohne Zusatzkosten bereitgestellt.
- **LTR**: Sie haben auch die Möglichkeit, die Langzeitaufbewahrung von vollständigen Sicherungen für bis zu 10 Jahre zu konfigurieren. Wenn Sie eine LTR-Richtlinie einrichten, werden diese Sicherungen automatisch im RA-GRS-Speicher gespeichert. Sie können jedoch steuern, wie häufig die Sicherungen kopiert werden. Zur Einhaltung unterschiedlicher Konformitätsanforderungen können Sie verschiedene Aufbewahrungszeiträume für wöchentliche, monatliche und/oder jährliche Sicherungen auswählen. Wie viel Speicher für LTR-Sicherungen verwendet wird, richtet sich nach der ausgewählten Konfiguration. Weitere Informationen finden Sie unter [Langfristiges Aufbewahren von Sicherungen](long-term-retention-overview.md).

## <a name="next-steps"></a>Nächste Schritte

Ausführliche Informationen zu den spezifischen Compute- und Speichergrößen, die in V-Kern-Dienstebenen verfügbar sind, finden Sie unter: 

- [Ressourcenlimits des auf virtuellen Kernen basierenden Kaufmodells für Azure SQL-Datenbank](resource-limits-vcore-single-databases.md)
- [Limits des auf virtuellen Kernen basierenden Kaufmodells für Pooldatenbanken in Azure SQL-Datenbank](resource-limits-vcore-elastic-pools.md)
- [Ressourcenlimits des auf virtuellen Kernen basierenden Kaufmodells für Azure SQL Managed Instance](../managed-instance/resource-limits.md)