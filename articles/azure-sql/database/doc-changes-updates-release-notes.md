---
title: Neuigkeiten
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Erfahren Sie mehr über die neuen Features und Dokumentationsverbesserungen für Azure SQL-Datenbank und SQL Managed Instance.
services: sql-database
author: MashaMSFT
ms.author: mathoma
ms.service: sql-db-mi
ms.subservice: service-overview
ms.custom: sqldbrb=2, references_regions
ms.devlang: ''
ms.topic: conceptual
ms.date: 06/22/2021
ms.openlocfilehash: 256f8f6f792f9bf373af4be9b429a9485b17b7a8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339247"
---
# <a name="whats-new-in-azure-sql-database--sql-managed-instance"></a>Welche Neuerungen gibt es in Azure SQL-Datenbank und SQL Managed Instance?
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

In diesem Artikel werden die Features von SQL-Datenbank und SQL Managed Instance aufgeführt, die sich derzeit in der öffentlichen Vorschau befinden. Informationen zu Updates und Verbesserungen für SQL-Datenbank und SQL Managed Instance finden Sie unter [Updates für SQL-Datenbank und den SQL Managed Instance-Dienst](https://azure.microsoft.com/updates/?product=sql-database). Informationen zu Updates und Verbesserungen für andere Azure-Dienste finden Sie unter [Dienstupdates](https://azure.microsoft.com/updates).

## <a name="whats-new"></a>Neuigkeiten

Die Dokumentation für Azure SQL-Datenbank und Azure SQL Managed Instance wurde in separate Abschnitte aufgeteilt. Wir haben auch aktualisiert, wie wir auf eine verwaltete Instanz der *verwalteten Azure SQL-Datenbank-Instanz* und auf *Azure SQL Managed Instance* verweisen.

Der Grund dafür ist, dass sich einige Features und Funktionen zwischen einem Singleton und einer verwalteten Instanz stark unterscheiden und es immer schwieriger wird, komplexe Nuancen zwischen Azure SQL-Datenbank und Azure SQL Managed Instance in einzelnen gemeinsamen Artikeln zu erläutern.

Diese Klarstellung zwischen den verschiedenen Azure SQL-Produkten sollte den Prozess der Arbeit mit der SQL Server-Datenbank-Engine in Azure vereinfachen und rationalisieren. Dabei ist es gleichgültig, ob es sich um einen verwalteten Singleton in Azure SQL-Datenbank, eine vollwertige verwaltete Instanz mit mehreren Datenbanken in Azure SQL Managed Instance oder das bekannte lokale SQL Server-Produkt auf einem virtuellen Computer in Azure handelt.

Bedenken Sie, dass es sich hierbei um ein laufendes Projekt handelt und noch nicht jeder Artikel aktualisiert wurde. Beispielsweise ist die Dokumentation für T-SQL-Anweisungen (Transact-SQL), gespeicherte Prozeduren und viele Features, die von Azure SQL-Datenbank und Azure SQL Managed Instance gemeinsam genutzt werden, noch nicht vollständig, weshalb wir Ihnen für Ihre Geduld bei der weiteren Klärung des Inhalts danken. 

Diese Tabelle bietet einen schnellen Vergleich der Änderungen in der Terminologie: 


|**Neuer Begriff**  | **Vorheriger Begriff**  |**Erklärung** |
|---------|---------|---------|
|**Verwaltete Azure SQL-Datenbank-Instanz** | *Verwaltete Azure SQL-Datenbank-Instanz*| Azure SQL Managed Instance ist ein eigenes Produkt innerhalb der Azure SQL-Produktfamilie, nicht nur eine Bereitstellungsoption in Azure SQL-Datenbank. | 
|**Azure SQL-Datenbank**|*Einzelne Datenbank* von Azure SQL-Datenbank| Sofern nicht ausdrücklich anders angegeben, umfasst der Produktname Azure SQL-Datenbank sowohl einzelne als auch in einem Pool für elastische Datenbanken bereitgestellte Datenbanken. |
|**Azure SQL-Datenbank**|*Pool für elastische* Azure SQL-Datenbank| Sofern nicht ausdrücklich anders angegeben, umfasst der Produktname Azure SQL-Datenbank sowohl einzelne als auch in einem Pool für elastische Datenbanken bereitgestellte Datenbanken.  |
|**Azure SQL-Datenbank** |Azure SQL-Datenbank | Obwohl der Begriff unverändert bleibt, gilt er jetzt nur noch für Bereitstellungen von einzelnen Datenbanken und Pools für elastische Datenbanken und umfasst keine verwaltete Instanz. |
| **Azure SQL**| – | Dies bezieht sich auf die Produktfamilie der SQL Server Datenbank-Engine-Produkte, die in Azure zur Verfügung stehen: Azure SQL-Datenbank, Azure SQL Managed Instance und SQL Server auf Azure-VMs. | 

## <a name="features-in-public-preview"></a>Features in der öffentlichen Vorschau

### <a name="azure-sql-database"></a>[Azure SQL-Datenbank](#tab/single-database)

| Funktion | Details |
| ---| --- |
| Aufträge für die elastische Datenbank | Weitere Informationen finden Sie unter [Erstellen, Konfigurieren und Verwalten von Aufträgen für die elastische Datenbank](elastic-jobs-overview.md). |
| Elastische Abfragen | Weitere Informationen finden Sie unter [Übersicht über elastische Abfragen](elastic-query-overview.md). |
| Elastische Transaktionen | Informationen finden Sie unter [Verteilte Transaktionen über Clouddatenbanken](elastic-transactions-overview.md). |
| Abfrage-Editor im Azure-Portal |Weitere Informationen finden Sie unter [Verwenden des SQL-Abfrage-Editors im Azure-Portal zum Verbinden und Abfragen von Daten](connect-query-portal.md).|
| Azure SQL-Analyse|Weitere Informationen finden Sie unter [Azure SQL-Analyse](../../azure-monitor/insights/azure-sql.md).|
| Hinweise zu Abfragespeicher | Informationen finden Sie unter [Abfragespeicherhinweise](/sql/relational-databases/performance/query-store-hints?view=azuresqldb-current&preserve-view=true).|
| ---| --- |

### <a name="azure-sql-managed-instance"></a>[Verwaltete Azure SQL-Datenbank-Instanz](#tab/managed-instance)

| Funktion | Details |
| ---| --- |
| [Unterstützung von 16 TB für SQL Managed Instance (universell)](https://techcommunity.microsoft.com/t5/azure-sql/increased-storage-limit-to-16-tb-for-sql-managed-instance/ba-p/2421443) | Unterstützung der Zuteilung von bis zu 16 TB Speicherplatz für SQL Managed Instance (universell) |
| [Ausschließliche Azure Active Directory-Authentifizierung für Azure SQL](https://techcommunity.microsoft.com/t5/azure-sql/azure-active-directory-only-authentication-for-azure-sql/ba-p/2417673) | Ausschließliche Azure Active Directory-Authentifizierung für Azure SQL Managed Instance in Public Preview. |
| [Migration mit dem Protokollwiedergabedienst](../managed-instance/log-replay-service-migrate.md) | Migrieren von Datenbanken aus SQL Server zu SQL Managed Instance mit dem Protokollwiedergabedienst. |
| [Wartungsfenster](./maintenance-window.md)| Mithilfe des Features „Wartungsfenster“ können Sie einen Wartungszeitplan konfigurieren. |
| [Service Broker: instanzübergreifender Nachrichtenaustausch für Azure SQL Managed Instance](https://azure.microsoft.com/updates/service-broker-message-exchange-for-azure-sql-managed-instance-in-public-preview/) | Unterstützung für instanzübergreifenden Nachrichtenaustausch für Azure SQL Managed Instance. |
| [Langfristige Sicherungsaufbewahrung für Azure SQL Managed Instance](https://azure.microsoft.com/updates/longterm-backup-retention-ltr-for-azure-sql-managed-instance-in-public-preview/) | Unterstützung der langfristigen Sicherungsaufbewahrung von bis zu 10 Jahren für Azure SQL Managed Instance. |
| [Azure Monitor SQL-Erkenntnisse für Azure SQL Managed Instance](https://azure.microsoft.com/updates/azure-monitor-sql-insights-for-azure-sql-in-public-preview/) | Azure Monitor SQL-Erkenntnisse für Azure SQL Managed Instance in Public Preview |
| [Verteilte Transaktionen](./elastic-transactions-overview.md) | Verteilte Transaktionen in verwalteten Instanzen |
| [Instanzenpools](../managed-instance/instance-pools-overview.md) | Eine praktische und kostengünstige Möglichkeit, um kleinere SQL-Instanzen zur Cloud zu migrieren. |
| [Transaktionsreplikation](../managed-instance/replication-transactional-overview.md) | Replizieren Sie die Änderungen aus Ihren Tabellen in andere Datenbanken in SQL Managed Instance, SQL-Datenbank oder SQL Server. Sie können auch Ihre Tabellen aktualisieren, wenn einige Zeilen in anderen Instanzen von SQL Managed Instance oder SQL Server geändert werden. Weitere Informationen finden Sie unter [Konfigurieren der Replikation in Azure SQL Managed Instance](../managed-instance/replication-between-two-instances-configure-tutorial.md). |
| Bedrohungserkennung |Weitere Informationen finden Sie unter [Konfigurieren der Bedrohungserkennung in Azure SQL Managed Instance](../managed-instance/threat-detection-configure.md).|
| Hinweise zu Abfragespeicher | Informationen finden Sie unter [Abfragespeicherhinweise](/sql/relational-databases/performance/query-store-hints?view=azuresqldb-mi-current&preserve-view=true).|
| ---| --- |

---

## <a name="new-features"></a>Neue Funktionen

### <a name="sql-managed-instance-h1-2021-updates"></a>Updates von SQL Managed Instance H1 2021

- [Unterstützung von 16 TB für SQL Managed Instance (universell) in Public Preview ](https://techcommunity.microsoft.com/t5/azure-sql/increased-storage-limit-to-16-tb-for-sql-managed-instance/ba-p/2421443): Unterstützung der Zuordnung von bis zu 16 TB Speicherplatz für SQL Managed Instance (universell) in Public Preview.
- [Parallele Sicherung für bessere Leistung in SQL Managed Instance (universell)](https://techcommunity.microsoft.com/t5/azure-sql/parallel-backup-for-better-performance-in-sql-managed-instance/ba-p/2421762): Unterstützung schnellerer Sicherungen in SQL Managed Instance (universell).
- [Ausschließliche Azure Active Directory-Authentifizierung für Azure SQL Managed Instance](https://techcommunity.microsoft.com/t5/azure-sql/azure-active-directory-only-authentication-for-azure-sql/ba-p/2417673): ausschließliche Azure Active Directory-Authentifizierung für Azure SQL Managed Instance in Public Preview.
- [Überwachen des Integritätsstatus Ihrer Azure SQL Managed Instance mit Resource Health](resource-health-to-troubleshoot-connectivity.md): Unterstützung der Resource Health-Überwachung für Azure SQL Managed Instance.
- [Dienstgestützte Subnetzkonfiguration für Azure SQL Managed Instance nutzt jetzt Diensttags für benutzerdefinierte Routen](../managed-instance/connectivity-architecture-overview.md): Unterstützung der Tabelle mit benutzerdefinierten Routen (User Defined Route, UDR).
- [Migrieren zu Managed Instance mit Protokollwiedergabedienst](../managed-instance/log-replay-service-migrate.md): ermöglicht die Migration von Datenbanken von SQL Server zu SQL Managed Instance unter Verwendung des Protokollwiedergabediensts (Public Preview).
- [Wartungsfenster](./maintenance-window.md):Mit dem Feature „Wartungsfenster“ können Sie einen Wartungszeitplan konfigurieren. Weitere Informationen finden Sie unter [Ankündigung des Wartungsfensters](https://techcommunity.microsoft.com/t5/azure-sql/maintenance-window-for-azure-sql-database-and-managed-instance/ba-p/2174835) (Public Preview).
- [Machine Learning Services für Azure SQL Managed Instance jetzt allgemein verfügbar](https://azure.microsoft.com/updates/machine-learning-services-on-azure-sql-managed-instance-now-generally-available/): allgemeine Verfügbarkeit von Machine Learning Services für Azure SQL Managed Instance.
- [Service Broker: instanzübergreifender Nachrichtenaustausch für Azure SQL Managed Instance](https://azure.microsoft.com/updates/service-broker-message-exchange-for-azure-sql-managed-instance-in-public-preview/): Unterstützung für instanzübergreifenden Nachrichtenaustausch.
- [Langfristige Sicherungsaufbewahrung für Azure SQL Managed Instance](https://azure.microsoft.com/updates/longterm-backup-retention-ltr-for-azure-sql-managed-instance-in-public-preview/): Unterstützung der langfristigen Sicherungsaufbewahrung von bis zu 10 Jahren für Azure SQL Managed Instance.
- [Detaillierte Berechtigungen für dynamische Datenmaskierung für Azure SQL Managed Instance](dynamic-data-masking-overview.md): allgemeine Verfügbarkeit detaillierter Berechtigungen für dynamische Datenmaskierung für Azure SQL Managed Instance. 
- [Azure SQL Managed Instance: Überwachung von Microsoft-Vorgängen](https://azure.microsoft.com/updates/azure-sql-auditing-of-microsoft-operations-is-now-generally-available/): allgemeine Verfügbarkeit der Überwachung von Microsoft-Vorgängen für Azure SQL Managed Instance.
- [Azure Monitor SQL-Erkenntnisse für Azure SQL Managed Instance](https://azure.microsoft.com/updates/azure-monitor-sql-insights-for-azure-sql-in-public-preview/): Azure Monitor SQL-Erkenntnisse für Azure SQL Managed Instance in Public Preview.

### <a name="sql-managed-instance-h2-2020-updates"></a>Updates von SQL Managed Instance H2 2020

- [Public Preview: Überwachung von Microsoft-Supportvorgängen in Azure SQL DB und Azure SQL MI](https://azure.microsoft.com/updates/auditing-of-microsoft-support-operations-in-azure-sql-db-and-azure-sql-mi/): Die Überwachungsfunktion für Microsoft-Supportvorgänge ermöglicht Ihnen, diese Supportvorgänge zu überwachen, wenn Sie während einer Supportanforderung auf Ihre Server und/oder Datenbanken zugreifen müssen, um Ihr Überwachungsprotokoll abzurufen (Public Preview).
- [Auf mehrere Azure SQL Managed Instances verteilte Datenbanktransaktionen](https://azure.microsoft.com/updates/distributed-database-transactions-spanning-multiple-azure-sql-managed-instances/): Auf mehrere Azure SQL Managed Instances verteilte Datenbanktransaktionen wurden hinzugefügt, um eine reibungslose Migration vorhandener Anwendungen sowie die Entwicklung moderner mehrinstanzenfähiger Anwendungen zu ermöglichen, die auf einer vertikal oder horizontal partitionierten Datenbankarchitektur basieren (Public Preview).
- [Konfigurierbare Redundanzoption für Sicherungsspeicher für Azure SQL Managed Instance](https://azure.microsoft.com/updates/configurable-backup-storage-redundancy-option-for-azure-sql-managed-instance-2/): Die Optionen für lokal redundanten Speicher (LRS) und zonenredundanten Speicher (ZRS) wurden zur Redundanz des Sicherungsspeichers hinzugefügt und bieten mehr Flexibilität und Auswahlmöglichkeiten. 
- [Kosteneinsparungen bei Sicherungsspeichern für Azure SQL-Datenbank und Managed Instance](https://azure.microsoft.com/updates/backup-storage-cost-savings-for-azure-sql-database-and-managed-instance/): Der Benutzer kann den Aufbewahrungszeitraum für PITR-Sicherungen festlegen. Die automatische Komprimierung von Sicherungen für Datenbanken mit transparenter Datenverschlüsselung (TDE) ist jetzt beim Belegen von Sicherungsspeichern bis zu 30 % effizienter.
- [Azure AD-Authentifizierungsfeatures für Azure SQL Managed Instance](https://azure.microsoft.com/updates/azure-ad-authentication-features-for-azure-sql-db-azure-synapse-analytics-and-azure-sql-managed-instance/): Diese Features helfen dabei, die Benutzererstellung mithilfe von Azure AD-Anwendungen zu automatisieren und ermöglichen die Erstellung einzelner Azure AD-Gastbenutzer in SQL Managed Instance (Public Preview).
- [Unterstützung des globalen Peerings virtueller Netzwerke für Azure SQL Managed Instance](https://azure.microsoft.com/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/)
- [Hosting von Katalogdatenbanken für alle unterstützten Versionen von SSRS in Azure SQL Managed Instance](https://azure.microsoft.com/updates/hosting-catalog-databases-for-all-supported-versions-of-ssrs-in-azure-sql-managed-instance/): Azure SQL Managed Instance kann Katalogdatenbanken für alle unterstützten Versionen von SQL Server Reporting Services (SSRS) hosten.
- [Wesentliche Leistungsverbesserungen für verwaltete Azure SQL Database Managed Instances](https://techcommunity.microsoft.com/t5/azure-sql/announcing-major-performance-improvements-for-azure-sql-database/ba-p/1701256) 
- [Verbesserte Verwaltung für Azure SQL Managed Instance](https://azure.microsoft.com/updates/enhanced-management-experience-for-azure-sql-managed-instance/)
- [Machine Learning in Azure SQL Managed Instance in der Vorschau](https://techcommunity.microsoft.com/t5/azure-sql/announcing-major-performance-improvements-for-azure-sql-database/ba-p/1701256): Machine Learning Services mit Unterstützung für die Sprachen R und Python bieten jetzt eine Vorschau auf die Unterstützung von Azure SQL Managed Instance (Public Preview).
- [Vom Benutzer eingeleitetes Failover für Resilienz bei Anwendungsfehlern in Azure SQL Managed Instance ist jetzt allgemein verfügbar](https://azure.microsoft.com/updates/userinitiated-failover-for-application-fault-resiliency-in-azure-sql-managed-instance-is-now-generally-available/): Vom Benutzer eingeleitetes Failover ist jetzt allgemein verfügbar und bietet Ihnen die Möglichkeit, ein automatisches Failover mithilfe von PowerShell, CLI-Befehlen und API-Aufrufen manuell einzuleiten.


### <a name="sql-managed-instance-h2-2019-updates"></a>Updates von SQL Managed Instance H2 2019

- [Dienstgestützte Subnetzkonfiguration](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/) stellt eine sichere und bequeme Möglichkeit zur Verwaltung der Subnetzkonfiguration dar, bei der Sie den Datenverkehr steuern, während SQL Managed Instance den ununterbrochenen Flow des Verwaltungsdatenverkehrs sicherstellt.
- [Transparent Data Encryption (TDE) mit Bring Your Own Key (BYOK)](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/) ermöglicht ein BYOK-Szenario (Bring Your Own Key) für den Schutz ruhender Daten, sodass Organisationen Verwaltungsaufgaben für Schlüssel und Daten trennen können.
- [Gruppen für automatisches Failover](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) ermöglichen es Ihnen, alle Datenbanken aus der primären Instanz in eine sekundäre Instanz in einer anderen Region zu replizieren.
- [Globale Ablaufverfolgungsflags](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/) ermöglichen Ihnen das Konfigurieren des Verhaltens von SQL Managed Instance.

### <a name="sql-managed-instance-h1-2019-updates"></a>Updates von SQL Managed Instance H1 2019

Die folgenden Funktionen sind im SQL Managed Instance-Bereitstellungsmodell in der ersten Jahreshälfte 2019 aktiviert:
  - Unterstützung für Abonnements mit <a href="/azure/azure-sql/managed-instance/resource-limits">monatlicher Azure-Gutschrift für Visual Studio-Abonnenten</a> und erhöhte [regionale Grenzwerte](../managed-instance/resource-limits.md#regional-resource-limitations).
  - Unterstützung für <a href="/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019">SharePoint 2016 und SharePoint 2019</a> und <a href="/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance">Dynamics 365 Business Central</a>.
  - Erstellen von verwalteten Instanzen mit <a href="/azure/azure-sql/managed-instance/scripts/create-powershell-azure-resource-manager-template">Sortierung auf Instanzebene</a> und einer <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">Zeitzone</a> Ihrer Wahl.
  - Verwaltete Instanzen sind jetzt durch eine [integrierte Firewall](../managed-instance/management-endpoint-verify-built-in-firewall.md) geschützt.
  - Konfigurieren von SQL Managed Instance für die Verwendung von [öffentlichen Endpunkten](../managed-instance/public-endpoint-configure.md), einer Verbindung zur [Proxy-Außerkraftsetzung](connectivity-architecture.md#connection-policy) für eine bessere Netzwerkleistung und <a href="https://aka.ms/four-cores-sql-mi-update">vier virtuellen Kernen bei der Gen5-Hardwaregenerierung</a> oder <a href="/azure/azure-sql/database/automated-backups-overview">Konfigurieren der Beibehaltung der Sicherung bis zu 35 Tage</a> für eine Point-in-Time-Wiederherstellung. [Die langfristige Sicherungsaufbewahrung](long-term-retention-overview.md) (bis zu 10 Jahre) ist zurzeit in der öffentlichen Vorschau verfügbar.  
  - Neue Funktionen ermöglichen Ihnen die <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">Geowiederherstellung Ihrer Datenbank in einem anderen Rechenzentrum mithilfe von PowerShell </a>, das [Umbenennen einer Datenbank](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/) und das [Löschen eines virtuellen Clusters](../managed-instance/virtual-cluster-delete.md).
  - Die neue integrierte [Rolle „Mitwirkender für Instanzen“](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) ermöglicht SoD-Einhaltung (Separation of Duty, Aufgabentrennung) von Sicherheitsprinzipien und Einhaltung von Unternehmensstandards.
  - SQL Managed Instance ist in den folgenden Azure Government-Regionen („US Gov Texas“, „US Gov Arizona“) sowie in „China, Norden 2“ und „China, Osten 2“ allgemein verfügbar. Außerdem ist sie in den folgenden öffentlichen Regionen verfügbar: Australien, Mitte; Australien, Mitte 2; Brasilien, Süden; Frankreich, Süden; VAE, Mitte; VAE, Norden; Südafrika, Norden; Südafrika, Westen.

## <a name="known-issues"></a>Bekannte Probleme

|Problem  |Entdeckt am  |Status  |Gelöst am  |
|---------|---------|---------|---------|
|[Das Ändern des Verbindungstyps wirkt sich nicht auf Verbindungen über den Endpunkt der Failovergruppe aus.](#changing-the-connection-type-does-not-affect-connections-through-the-failover-group-endpoint)|Jan 2021|Mit Problemumgehung||
|[Die Prozedur „sp_send_dbmail“ kann zu vorübergehenden Fehlern führen, wenn der Parameter @query verwendet wird.](#procedure-sp_send_dbmail-may-transiently-fail-when--parameter-is-used)|Jan 2021|Mit Problemumgehung||
|[Verteilte Transaktionen können auch nach dem Entfernen einer verwalteten Instanz aus der Serververtrauensstellungsgruppe ausgeführt werden.](#distributed-transactions-can-be-executed-after-removing-managed-instance-from-server-trust-group)|Oktober 2020|Mit Problemumgehung||
|[Verteilte Transaktionen können nach einem Managed Instance-Skalierungsvorgang nicht ausgeführt werden.](#distributed-transactions-cannot-be-executed-after-managed-instance-scaling-operation)|Oktober 2020|Mit Problemumgehung||
|[SQL Managed Instance mit demselben Namen wie der zuvor gelöschte logische Server kann nicht erstellt werden](#cannot-create-sql-managed-instance-with-the-same-name-as-logical-server-previously-deleted)|August 2020|Mit Problemumgehung||
|[BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql)/[OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql)-Anweisung in Azure SQL und die `BACKUP`/`RESTORE`-Anweisung in Managed Instance können keine verwalteten Azure AD-Identitäten verwenden, um sich für Azure Storage zu authentifizieren.|Sep 2020|Mit Problemumgehung||
|[Der Dienstprinzipal kann nicht auf Azure AD und Azure Key Vault zugreifen](#service-principal-cannot-access-azure-ad-and-akv)|August 2020|Mit Problemumgehung||
|[Wiederherstellen der manuellen Sicherung ohne CHECKSUM schlägt möglicherweise fehl](#restoring-manual-backup-without-checksum-might-fail)|Mai 2020|Gelöst|Juni 2020|
|[Der Agent reagiert beim Ändern, Deaktivieren oder Aktivieren vorhandener Aufträge nicht mehr](#agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs)|Mai 2020|Gelöst|Juni 2020|
|[Berechtigungen für Ressourcengruppe werden nicht auf SQL Managed Instance angewendet](#permissions-on-resource-group-not-applied-to-sql-managed-instance)|Februar 2020|Gelöst|November 2020|
|[Einschränkung beim manuellen Failover über das Portal für Failovergruppen](#limitation-of-manual-failover-via-portal-for-failover-groups)|Januar 2020|Mit Problemumgehung||
|[SQL-Agent-Rollen benötigen explizite EXECUTE-Berechtigungen für Anmeldungen, die keine Systemadministratoranmeldungen sind](#in-memory-oltp-memory-limits-are-not-applied)|Dezember 2019|Mit Problemumgehung||
|[SQL Agent-Aufträge können durch den Neustart des Agent-Prozesses unterbrochen werden](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|Dezember 2019|Gelöst|März 2020|
|[Azure AD-Anmeldungen und -Benutzer werden in SSDT nicht unterstützt.](#azure-ad-logins-and-users-are-not-supported-in-ssdt)|November 2019|Keine Problemumgehung||
|[In-Memory-OLTP-Arbeitsspeicherlimits werden nicht angewendet](#in-memory-oltp-memory-limits-are-not-applied)|Oktober 2019|Mit Problemumgehung||
|[Zurückgegebener Fehler beim Versuch, eine nicht leere Datei zu entfernen](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|Oktober 2019|Mit Problemumgehung||
|[Das Ändern der Dienstebene und Erstellen von Instanzvorgängen werden durch die laufende Datenbankwiederherstellung blockiert](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|September 2019|Mit Problemumgehung||
|[Resource Governor auf Dienstebene „Unternehmenskritisch“ muss nach einem Failover möglicherweise neu konfiguriert werden](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|September 2019|Mit Problemumgehung||
|[Datenbankübergreifende Service Broker-Dialoge müssen nach einem Upgrade der Dienstebene erneut initialisiert werden](#cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade)|August 2019|Mit Problemumgehung||
|[Identitätswechsel für Azure AD-Anmeldetypen wird nicht unterstützt.](#impersonation-of-azure-ad-login-types-is-not-supported)|Juli 2019|Keine Problemumgehung||
|[@queryParameter wird in „sp_send_db_mail“ nicht unterstützt](#-parameter-not-supported-in-sp_send_db_mail)|April 2019|Gelöst|Jan 2021|
|[Transaktionsreplikation muss nach einem geografischen Failover neu konfiguriert werden](#transactional-replication-must-be-reconfigured-after-geo-failover)|März 2019|Keine Problemumgehung||
|[Temporäre Datenbank wird während des RESTORE-Vorgangs verwendet](#temporary-database-is-used-during-restore-operation)||Mit Problemumgehung||
|[Struktur und Inhalt von TEMPDB werden neu erstellt](#tempdb-structure-and-content-is-re-created)||Keine Problemumgehung||
|[Überschreiten des Speicherplatzes mit kleinen Datenbankdateien](#exceeding-storage-space-with-small-database-files)||Mit Problemumgehung||
|[Statt Datenbanknamen werden GUID-Werte angezeigt](#guid-values-shown-instead-of-database-names)||Mit Problemumgehung||
|[Fehlerprotokolle werden nicht persistent gespeichert](#error-logs-arent-persisted)||Keine Problemumgehung||
|[Transaktionsbereich in zwei Datenbanken innerhalb derselben Instanz wird nicht unterstützt](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)||Mit Problemumgehung|März 2020|
|[CLR-Module und Verbindungsserver können manchmal nicht auf eine lokale IP-Adresse verweisen](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)||Mit Problemumgehung||
|Nach dem Wiederherstellen der Datenbank aus Azure Blob Storage wird die Datenbankkonsistenz nicht mithilfe von DBCC CHECKDB überprüft.||Gelöst|November 2019|
|Die Point-in-Time-Wiederherstellung einer Datenbank von der Dienstebene „Unternehmenskritisch“ in die Dienstebene „Universell“ wird nicht gelingen, wenn die Quelldatenbank In-Memory-OLTP-Objekte enthält.||Gelöst|Oktober 2019|
|Datenbank-E-Mail-Feature bei externen (Nicht-Azure-)E-Mail-Servern über sichere Verbindung||Gelöst|Oktober 2019|
|Eigenständige Datenbanken werden in SQL Managed Instance nicht unterstützt||Gelöst|August 2019|

### <a name="changing-the-connection-type-does-not-affect-connections-through-the-failover-group-endpoint"></a>Das Ändern des Verbindungstyps wirkt sich nicht auf Verbindungen über den Endpunkt der Failovergruppe aus.

Wenn eine Instanz Teil einer [Autofailover-Gruppe](./auto-failover-group-overview.md) ist, hat das Ändern des [Verbindungstyps](../managed-instance/connection-types-overview.md) der Instanz keine Auswirkungen auf Verbindungen, die über den Listenerendpunkt der Failovergruppe hergestellt werden.

**Problemumgehung:** Löschen Sie die Autofailover-Gruppe, und erstellen Sie sie nach dem Ändern des Verbindungstyps neu.

### <a name="procedure-sp_send_dbmail-may-transiently-fail-when-query-parameter-is-used"></a>Die Prozedur „sp_send_dbmail“ kann zu vorübergehenden Fehlern führen, wenn der Parameter @query verwendet wird.

Die Prozedur `sp_send_dbmail` kann zu vorübergehenden Fehlern führen, wenn der Parameter `@query` verwendet wird. Wenn dieses Problem besteht, tritt bei jeder zweiten Ausführung der Prozedur „sp_send_dbmail“ der Fehler `Msg 22050, Level 16, State 1` mit der Meldung `Failed to initialize sqlcmd library with error number -2147467259` auf. Damit dieser Fehler ordnungsgemäß angezeigt werden kann, sollte die Prozedur mit dem Standardwert 0 für den Parameter `@exclude_query_output` aufgerufen werden. Andernfalls wird der Fehler nicht weitergegeben.
Dieses Problem wird durch eine bekanntes Problem mit `sp_send_dbmail` bei der Verwendung von Identitätswechseln und Verbindungspooling verursacht.
Um dieses Problem zu umgehen, umschließen Sie den Code für das Senden von E-Mails in einer Wiederholungslogik, die auf dem Ausgabeparameter `@mailitem_id` basiert. Wenn die Ausführung zu einem Fehler führt, ist der Parameterwert NULL. Dies zeigt an, dass `sp_send_dbmail` noch einmal aufgerufen werden muss, um erfolgreich eine E-Mail zu senden. Im Folgenden finden Sie ein Beispiel für diese Wiederholungslogik.
```sql
CREATE PROCEDURE send_dbmail_with_retry AS
BEGIN
    DECLARE @miid INT
    EXEC msdb.dbo.sp_send_dbmail
        @recipients = 'name@mail.com', @subject = 'Subject', @query = 'select * from dbo.test_table',
        @profile_name ='AzureManagedInstance_dbmail_profile', @execute_query_database = 'testdb',
        @mailitem_id = @miid OUTPUT

    -- If sp_send_dbmail returned NULL @mailidem_id then retry sending email.
    --
    IF (@miid is NULL)
    EXEC msdb.dbo.sp_send_dbmail
        @recipients = 'name@mail.com', @subject = 'Subject', @query = 'select * from dbo.test_table',
        @profile_name ='AzureManagedInstance_dbmail_profile', @execute_query_database = 'testdb',
END
```

### <a name="distributed-transactions-can-be-executed-after-removing-managed-instance-from-server-trust-group"></a>Verteilte Transaktionen können auch nach dem Entfernen einer verwalteten Instanz aus der Vertrauensstellungsgruppe des Servers ausgeführt werden

[Serververtrauensstellungsgruppen](../managed-instance/server-trust-group-overview.md) werden verwendet, um eine Vertrauensstellung zwischen verwalteten Instanzen einzurichten. Die Vertrauensstellung ist eine Voraussetzung für [verteilte Transaktionen](./elastic-transactions-overview.md). Nachdem Sie Managed Instance aus der Vertrauensstellungsgruppe des Servers entfernt oder die Gruppe gelöscht haben, können Sie möglicherweise weiterhin verteilte Transaktionen ausführen. Es gibt eine Problemumgehung, mit der Sie sicherstellen können, dass verteilte Transaktionen deaktiviert sind, und zwar ein [vom Benutzer initiiertes manuelles Failover](../managed-instance/user-initiated-failover.md) auf Managed Instance.

### <a name="distributed-transactions-cannot-be-executed-after-managed-instance-scaling-operation"></a>Verteilte Transaktionen können nach einem Managed Instance-Skalierungsvorgang nicht ausgeführt werden

Bei Managed Instance-Skalierungsvorgängen, bei denen die Dienstebene oder die Anzahl von virtuellen Kernen geändert wird, werden Einstellungen für die Serververtrauensstellungsgruppe im Back-End zurückgesetzt und die Ausführung von [verteilten Transaktionen](./elastic-transactions-overview.md) deaktiviert. Dieses Problem lässt sich umgehen, indem Sie die [Serververtrauensstellungsgruppe](../managed-instance/server-trust-group-overview.md) im Azure-Portal löschen und eine neue erstellen.

### <a name="cannot-create-sql-managed-instance-with-the-same-name-as-logical-server-previously-deleted"></a>SQL Managed Instance mit demselben Namen wie der zuvor gelöschte logische Server kann nicht erstellt werden

Nachdem der [logische Server](./logical-servers.md) gelöscht wurde, gibt es eine Sperrfrist von 7 Tagen, bevor der Name in den Datensätzen freigegeben wird. In diesem Zeitraum kann keine SQL Managed Instance mit demselben Namen erstellt werden. Als Umgehungslösung müssen Sie einen anderen Namen für die SQL Managed Instance verwenden oder ein Supportticket für die Freigabe eines logischen Servernamens erstellen.

### <a name="bulk-insert-and-backuprestore-statements-should-use-sas-key-to-access-azure-storage"></a>BULK INSERT- und BACKUP/RESTORE-Anweisungen sollten einen SAS-Schlüssel verwenden, um auf Azure Storage zuzugreifen.

Derzeit wird die Verwendung der `DATABASE SCOPED CREDENTIAL`-Syntax mit verwalteter Identität für die Authentifizierung bei Azure Storage nicht unterstützt. Microsoft empfiehlt die Verwendung einer [Shared Access Signature](../../storage/common/storage-sas-overview.md) für die [Datenbank-Bereichsanmeldeinformationen](/sql/t-sql/statements/create-credential-transact-sql#d-creating-a-credential-using-a-sas-token), wenn auf Azure Storage zum Masseneinfügen, die Anweisungen `BACKUP` und `RESTORE` oder für die Funktion `OPENROWSET` zugegriffen wird. Beispiel:

```sql
CREATE DATABASE SCOPED CREDENTIAL sas_cred WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
 SECRET = '******srt=sco&sp=rwac&se=2017-02-01T00:55:34Z&st=2016-12-29T16:55:34Z***************';
GO
CREATE EXTERNAL DATA SOURCE MyAzureBlobStorage
  WITH ( TYPE = BLOB_STORAGE, LOCATION = 'https://****************.blob.core.windows.net/invoices', CREDENTIAL= sas_cred );
GO
BULK INSERT Sales.Invoices FROM 'inv-2017-12-08.csv' WITH (DATA_SOURCE = 'MyAzureBlobStorage');
```

Ein weiteres Beispiel für die Verwendung von `BULK INSERT` mit einem SAS-Schlüssel finden Sie unter [Shared Access Signature zur Authentifizierung beim Speicher](/sql/t-sql/statements/bulk-insert-transact-sql#f-importing-data-from-a-file-in-azure-blob-storage). 

### <a name="service-principal-cannot-access-azure-ad-and-akv"></a>Der Dienstprinzipal kann nicht auf Azure AD und Azure Key Vault zugreifen.

In einigen Fällen kann ein Problem bei dem Dienstprinzipal auftreten, der für den Zugriff auf Azure AD- und Azure Key Vault-Dienste (AKV) verwendet wird. Dieses Problem beeinträchtigt die Verwendung von Azure AD-Authentifizierung und Transparent Database Encryption (TDE) mit SQL Managed Instance. Dabei kann es sich um ein vorübergehendes Konnektivitätsproblem handeln, oder Anweisungen, z. B. `CREATE LOGIN/USER FROM EXTERNAL PROVIDER` oder `EXECUTE AS LOGIN/USER`, können nicht ausgeführt werden. In manchen Fällen ist es eventuell nicht möglich, TDE mit einem kundenseitig verwalteten Schlüssel in einer neuen Azure SQL Managed Instance einzurichten.

**Problemumgehung**: Wenn Sie verhindern möchten, dass dieses Problem in Ihrer Instanz von SQL Managed Instance auftritt, bevor Sie Updatebefehle ausführen, oder falls dieses Problem bereits nach Updatebefehlen aufgetreten ist, wechseln Sie zum Azure-Portal, und öffnen Sie in SQL Managed Instance die [Seite „Active Directory-Administrator“](./authentication-aad-configure.md?tabs=azure-powershell#azure-portal). Überprüfen Sie, ob die Fehlermeldung „Die verwaltete Instanz benötigt einen Dienstprinzipal für den Zugriff auf Azure Active Directory. Klicken Sie hier, um einen Dienstprinzipal zu erstellen.“ angezeigt wird. Falls diese Fehlermeldung auftritt, klicken Sie darauf, und befolgen Sie die Schrittanleitung, die so lange angezeigt wird, bis der Fehler behoben wurde.

### <a name="restoring-manual-backup-without-checksum-might-fail"></a>Wiederherstellen der manuellen Sicherung ohne CHECKSUM schlägt möglicherweise fehl

Unter bestimmten Umständen kann die manuelle Sicherung von Datenbanken, die für eine verwalteten Instanz ohne CHECKSUM erstellt wurde, nicht wiederhergestellt werden. Versuchen Sie in solchen Fällen erneut, die Sicherung wiederherzustellen, bis der Vorgang erfolgreich war.

**Problemumgehung**: Erstellen Sie manuelle Sicherungen von Datenbanken für verwaltete Instanzen mit aktivierter CHECKSUM.

### <a name="agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs"></a>Der Agent reagiert beim Ändern, Deaktivieren oder Aktivieren vorhandener Aufträge nicht mehr

Unter bestimmten Umständen kann es vorkommen, dass der Agent nicht mehr reagiert, wenn ein vorhandener Auftrag geändert, deaktiviert oder aktiviert wird. Das Problem wird bei Erkennung automatisch behoben, indem ein Neustart des Agent-Prozesses ausgeführt wird.

### <a name="permissions-on-resource-group-not-applied-to-sql-managed-instance"></a>Berechtigungen für Ressourcengruppe werden nicht auf SQL Managed Instance angewendet

Wenn die Azure-Rolle „Mitwirkender“ von SQL Managed Instance auf eine Ressourcengruppe (RG) angewandt wird, betrifft dies nicht SQL Managed Instance und hat keine Auswirkungen.

**Problemumgehung**: Richten Sie eine Rolle „Mitwirkender“ von SQL Managed Instance für Benutzer auf Abonnementebene ein.

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>Einschränkung beim manuellen Failover über das Portal für Failovergruppen

Wenn sich eine Failovergruppe über mehrere Instanzen in verschiedenen Azure-Abonnements oder Ressourcengruppen erstreckt, kann von der primären Instanz in der Failovergruppe kein manuelles Failover initiiert werden.

**Problemumgehung**: Initiieren Sie das Failover über das Portal von der geosekundären Instanz.

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>SQL-Agent-Rollen benötigen explizite EXECUTE-Berechtigungen für Anmeldungen, die keine Systemadministratoranmeldungen sind

Wenn Anmeldungen für Nicht-Systemadministratoren einer der [festen SQL-Agent-Datenbankrollen](/sql/ssms/agent/sql-server-agent-fixed-database-roles) hinzugefügt werden, besteht ein Problem, bei dem drei gespeicherten Prozeduren in der Masterdatenbank explizite EXECUTE-Berechtigungen gewährt werden müssen, damit diese Anmeldungen funktionieren. Wenn dieses Problem auftritt, wird die Fehlermeldung „The EXECUTE permission was denied on the object <object_name> (Microsoft SQL Server, Error: 229)“ (Die Berechtigung EXECUTE wurde für das Objekt <objekt_name> verweigert (Microsoft SQL Server, Fehler: 229) angezeigt.

**Problemumgehung**: Nachdem Sie einer festen SQL-Agent-Datenbankrolle (SQLAgentUserRole, SQLAgentReaderRole oder SQLAgentOperatorRole) Anmeldungen hinzugefügt haben, führen Sie für jede hinzugefügte Anmeldung das folgende T-SQL-Skript aus, um den aufgelisteten gespeicherten Prozeduren explizit EXECUTE-Berechtigungen zu erteilen.

```tsql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name];
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name];
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name];
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name];
```

### <a name="sql-agent-jobs-can-be-interrupted-by-agent-process-restart"></a>SQL Agent-Aufträge können durch den Neustart des Agent-Prozesses unterbrochen werden

**(Behoben im März 2020:)** Der SQL-Agent erstellt bei jedem Starten eines Auftrags eine neue Sitzung und erhöht den Speicherverbrauch allmählich. Um zu vermeiden, dass die interne Arbeitsspeichergrenze erreicht wird, wodurch die Ausführung geplanter Aufträge blockiert würde, wird der Agent-Prozess neu gestartet, sobald der Arbeitsspeicherverbrauch den Schwellenwert erreicht. Dies kann dazu führen, dass die Ausführung von Aufträgen unterbrochen wird, die zum Zeitpunkt des Neustarts ausgeführt werden.

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>In-Memory-OLTP-Arbeitsspeicherlimits werden nicht angewendet.

In einigen Fällen werden auf der Dienstebene „Unternehmenskritisch“ [maximale Arbeitsspeicherlimits für speicheroptimierte Objekte](../managed-instance/resource-limits.md#in-memory-oltp-available-space) nicht ordnungsgemäß angewandt. Mit SQL Managed Instance kann die Workload eventuell mehr Arbeitsspeicher für In-Memory-OLTP-Vorgänge belegen. Dies kann sich auf die Verfügbarkeit und Stabilität der Instanz auswirken. In-Memory-OLTP-Abfragen, die die Grenzwerte erreichen, führen möglicherweise nicht sofort zu Fehlern. Dieses Problem wird bald behoben. Bei Abfragen, die mehr In-Memory-OLTP-Arbeitsspeicher belegen, treten früher Fehler auf, wenn sie die [Grenzwerte](../managed-instance/resource-limits.md#in-memory-oltp-available-space) erreichen.

**Problemumgehung**: [Überwachen Sie die In-Memory-OLTP-Arbeitsspeichernutzung](../in-memory-oltp-monitor-space.md) mithilfe von [SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring), um sicherzustellen, dass die Workload nicht mehr als den verfügbaren Arbeitsspeicher belegt. Erhöhen Sie die Arbeitsspeicherlimits, die von der Anzahl von virtuellen Kernen abhängen, oder optimieren Sie Ihre Workload, damit sie weniger Arbeitsspeicher belegt.
 
### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>Zurückgegebener Fehler bei dem Versuch, eine nicht leere Datei zu entfernen

SQL Server und SQL Managed Instance [lassen nicht das Löschen von Dateien zu, die nicht leer sind](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites). Wenn Sie versuchen, eine nicht leere Datendatei mithilfe der Anweisung `ALTER DATABASE REMOVE FILE` zu entfernen, wird der Fehler `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` nicht sofort zurückgegeben. SQL Managed Instance versucht weiterhin, die Datei zu löschen, und für den Vorgang tritt nach 30 Minuten der Fehler `Internal server error` auf.

**Problemumgehung**: Entfernen Sie den Inhalt der Datei mit dem Befehl `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)`. Wenn es sich um die einzige Datei in der Dateigruppe handelt, müssen Sie Daten aus der Tabelle oder Partition löschen, die dieser Dateigruppe zugeordnet ist, bevor Sie die Datei verkleinern, und diese Daten optional in eine andere Tabelle/Partition laden.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>Das Ändern der Dienstebene und Erstellen von Instanzvorgängen wird durch die laufende Datenbankwiederherstellung blockiert

Durch die laufende `RESTORE`-Anweisung blockieren der Migrationsprozess des Datenmigrationsdiensts und die integrierte Point-in-Time-Wiederherstellung das Aktualisieren einer Dienstebene und eine Größenänderung der vorhandenen Instanz sowie das Erstellen neuer Instanzen so lange, bis der Wiederherstellungsvorgang abgeschlossen ist. 

Der Wiederherstellungsvorgang blockiert diese Vorgänge für die verwalteten Instanzen und Instanzenpools in dem Subnetz, in dem der Wiederherstellungsvorgang ausgeführt wird. Die Instanzen in Instanzenpools sind davon nicht betroffen. Beim Erstellen oder Ändern von Vorgängen auf Dienstebene tritt kein Fehler oder Timeout auf. Sie werden fortgesetzt, sobald der Wiederherstellungsvorgang abgeschlossen oder abgebrochen wurde.

**Problemumgehung**: Warten Sie, bis der Wiederherstellungsvorgang abgeschlossen ist, oder brechen Sie ihn ab, wenn die Erstellung oder Aktualisierung der Dienstebene eine höhere Priorität hat.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>Resource Governor auf Dienstebene „Unternehmenskritisch“ muss möglicherweise nach einem Failover neu konfiguriert werden

Das Feature [Resource Governor](/sql/relational-databases/resource-governor/resource-governor), mit dem Sie die der Benutzerworkload zugewiesenen Ressourcen einschränken können, klassifiziert möglicherweise eine Benutzerworkload nach einem Failover oder einer vom Benutzer initiierten Änderung der Dienstebene (z. B. Änderung der maximalen virtuellen Kerne oder der maximalen Instanzspeichergröße) falsch.

**Problemumgehung**: Führen Sie `ALTER RESOURCE GOVERNOR RECONFIGURE` regelmäßig oder als Teil des SQL-Agent-Auftrags aus, der den SQL-Task beim Starten der Instanz ausführt, wenn Sie [Resource Governor](/sql/relational-databases/resource-governor/resource-governor) verwenden.

### <a name="cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade"></a>Datenbankübergreifende Service Broker-Dialoge müssen nach einem Upgrade der Dienstebene erneut initialisiert werden

Datenbankübergreifenden Service Broker-Dialoge stellen die Zustellung der Nachrichten an die Dienste in anderen Datenbanken nach Änderung der Dienstebene ein. Die Nachrichten sind *nicht verloren*, sondern befinden sich in der Absenderwarteschlange. Jegliche Änderung virtueller Kerne oder der Instanzspeichergröße von SQL Managed Instance führt dazu, dass der Wert `service_broke_guid` in der Sicht [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) für alle Datenbanken geändert wird. Ein `DIALOG`, der mit der Anweisung [BEGIN DIALOG](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) erstellt wurde und auf Service Broker in einer anderen Datenbank verweist, stellt die Zustellung von Nachrichten an den Zieldienst ein.

**Problemumgehung**: Beenden Sie vor dem Aktualisieren der Dienstebene alle Aktivitäten, die datenbankübergreifende Dialogkonversationen von Service Broker verwenden, und initialisieren Sie sie danach neu. Wenn nach dem Ändern der Dienstebene noch nicht zugestellte Nachrichten vorhanden sind, lesen Sie sie aus der Quellwarteschlange, und senden Sie sie erneut an die Zielwarteschlange.

### <a name="impersonation-of-azure-ad-login-types-is-not-supported"></a>Identitätswechsel für Azure AD-Anmeldetypen wird nicht unterstützt.

Identitätswechsel mit `EXECUTE AS USER` oder `EXECUTE AS LOGIN` der folgenden Azure Active Directory-Prinzipale (Azure AD) werden nicht unterstützt:
-   Azure AD-Benutzer mit Alias. In diesem Fall wird der Fehler `15517` zurückgegeben.
- Azure AD-Anmeldungen und-Benutzer, die auf Azure AD-Anwendungen oder -Dienstprinzipalen basieren. In diesem Fall werden die Fehler `15517` und `15406` zurückgegeben.

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@queryDer Parameter wird in sp_send_db_mail nicht unterstützt.

Der `@query`-Parameter in der Prozedur [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) funktioniert nicht.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>Die Transaktionsreplikation muss nach einem geografischen Failover neu konfiguriert werden.

Wenn die Transaktionsreplikation für eine Datenbank in einer Autofailover-Gruppe aktiviert ist, muss der SQL Managed Instance-Administrator alle Veröffentlichungen für die alte primäre Instanz bereinigen und nach einem Failover in eine andere Region für die neue primäre Instanz erneut konfigurieren. Weitere Informationen finden Sie unter [Replikation](../managed-instance/transact-sql-tsql-differences-sql-server.md#replication).

### <a name="azure-ad-logins-and-users-are-not-supported-in-ssdt"></a>Azure AD-Anmeldungen und -Benutzer werden in SSDT nicht unterstützt.

SQL Server Data Tools unterstützen Azure AD-Anmeldungen und -Benutzer nicht vollständig.

### <a name="temporary-database-is-used-during-restore-operation"></a>Temporäre Datenbank wird während des RESTORE-Vorgangs verwendet

Wenn eine Datenbank in SQL Managed Instance wiederhergestellt wird, erstellt der Wiederherstellungsdienst zunächst eine leere Datenbank mit dem gewünschten Namen, um den Namen auf der Instanz zu belegen. Nach einiger Zeit wird diese Datenbank gelöscht, und die Wiederherstellung der eigentlichen Datenbank wird gestartet. 

Die Datenbank im Status *Wird wiederhergestellt* weist temporär einen zufälligen GUID-Wert anstelle des Namens auf. Nach Abschluss des Wiederherstellungsvorgangs wird der temporäre Name in den in der `RESTORE`-Anweisung angegebenen gewünschten Namen geändert. 

In der Anfangsphase kann der Benutzer auf die leere Datenbank zugreifen und sogar in dieser Datenbank Tabellen erstellen oder Daten laden. Diese temporäre Datenbank wird gelöscht, wenn der Wiederherstellungsdienst die zweite Phase startet.

**Problemumgehung**: Greifen Sie erst dann auf die Datenbank zu, die Sie wiederherstellen, wenn Sie feststellen, dass die Wiederherstellung abgeschlossen ist.

### <a name="tempdb-structure-and-content-is-re-created"></a>Struktur und Inhalt von TEMPDB werden neu erstellt.

Die Datenbank `tempdb` ist immer in 12 Datendateien aufgeteilt, und die Dateistruktur kann nicht geändert werden. Die maximale Größe pro Datei kann nicht geändert werden. Zudem können `tempdb` keine neuen Dateien hinzugefügt werden. `Tempdb` wird beim Start oder Failover einer Instanz immer als leere Datenbank neu erstellt. Änderungen an `tempdb` werden nicht beibehalten.

### <a name="exceeding-storage-space-with-small-database-files"></a>Überschreiten des Speicherplatzes mit kleinen Datenbankdateien

`CREATE DATABASE`-, `ALTER DATABASE ADD FILE`- und `RESTORE DATABASE`-Anweisungen schlagen möglicherweise fehl, weil die Instanz das Azure-Speicherlimit erreichen kann.

Mit jeder Instanz von SQL Managed Instance auf der Dienstebene „Universell“ sind bis zu 35 TB Speicherplatz für Azure Premium-Datenträger reserviert. Jede Datenbankdatei wird auf einem separaten physischen Datenträger platziert. Mögliche Datenträgergrößen sind 128 GB, 256 GB, 512 GB, 1 TB oder 4 TB. Nicht verwendeter Speicherplatz auf dem Datenträger wird nicht berechnet, aber die Gesamtgröße der Azure Premium-Datenträger darf 35 TB nicht überschreiten. In einigen Fällen kann eine verwaltete Instanz, die nicht insgesamt 8 TB benötigt, aufgrund interner Fragmentierung das Azure-Limit von 35 TB überschreiten.

Beispiel: In einer Instanz von SQL Managed Instance auf der Dienstebene „Universell“ gibt es eine große Datei (1,2 TB), die sich auf einem 4-TB-Datenträger befindet. Darüber hinaus befinden sich 248 Dateien mit jeweils 1 GB auf separaten 128-GB-Datenträgern. In diesem Beispiel:

- Die Gesamtgröße des zugewiesenen Datenträgerspeichers beträgt 1 x 4 TB + 248 x 128 GB = 35 TB.
- Der reservierte Gesamtspeicherplatz für Datenbanken in der Instanz beträgt 1 x 1,2 TB + 248 x 1 GB = 1,4 TB.

Dieses Beispiel veranschaulicht, dass eine Instanz von SQL Managed Instance unter bestimmten Umständen aufgrund einer spezifischen Verteilung von Dateien das für einen angefügten Azure Premium-Datenträger reservierte Limit von 35 TB erreichen kann, wenn Sie dies möglicherweise nicht erwarten.

In diesem Beispiel funktionieren vorhandene Datenbanken weiterhin und können ohne Probleme weiter wachsen, solange keine neuen Dateien hinzugefügt werden. Es könnten jedoch keine neuen Datenbanken erstellt oder wiederhergestellt werden, da für neue Datenträgerlaufwerke nicht genügend Speicherplatz vorhanden ist, selbst nicht dann, wenn die Gesamtgröße aller Datenbanken das Größenlimit der Instanz nicht überschreitet. Der Fehler, der in diesem Fall zurückgegeben wird, ist nicht klar.

Sie können mithilfe von Systemansichten [die Anzahl von verbleibenden Dateien identifizieren](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1). Wenn Sie dieses Limit erreichen, versuchen Sie, [einige der kleineren Dateien mithilfe der DBCC SHRINKFILE-Anweisung zu leeren und zu löschen](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file), oder wechseln Sie zur [Dienstebene „Unternehmenskritisch“, für die dieses Limit nicht gilt](../managed-instance/resource-limits.md#service-tier-characteristics).

### <a name="guid-values-shown-instead-of-database-names"></a>Anstelle von Datenbanknamen werden GUID-Werte gezeigt.

Mehrere Systemansichten, Leistungsindikatoren, Fehlermeldungen, XEvents und Fehlerprotokolleinträge zeigen GUID-Datenbankbezeichner anstelle der eigentlichen Datenbanknamen an. Verlassen Sie sich nicht auf diese GUIDs, da sie in Zukunft durch tatsächliche Datenbanknamen ersetzt werden.

**Problemumgehung**: Verwenden Sie die Sicht `sys.databases`, um den tatsächlichen Datenbanknamen aus dem physischen Datenbanknamen aufzulösen, der in Form von GUID-Datenbankbezeichnern angegeben wurde:

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4;
```

### <a name="error-logs-arent-persisted"></a>Fehlerprotokolle werden nicht persistent gespeichert

Die Fehlerprotokolle in einer SQL Managed Instance werden nicht persistent gespeichert, und ihre Größe wird im Speicherlimit nicht berücksichtigt. Fehlerprotokolle werden im Falle eines Failovers möglicherweise automatisch gelöscht. Möglicherweise gibt es Lücken im Fehlerprotokollverlauf, weil SQL Managed Instance auf mehreren virtuellen Computern mehrmals verschoben wurde.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>Ein Transaktionsbereich in zwei Datenbanken in derselben Instanz wird nicht unterstützt

**(Gelöst im März 2020)** Die `TransactionScope`-Klasse in .NET funktioniert nicht, wenn zwei Abfragen an zwei Datenbanken in derselben Instanz im gleichen Transaktionsbereich gesendet werden:

```csharp
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }

    using (var conn2 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into b.dbo.T2 values(2)");        cmd2.ExecuteNonQuery();
    }

    scope.Complete();
}

```

**Problemumgehung (seit März 2020 nicht mehr erforderlich):** Verwenden Sie [SqlConnection.ChangeDatabase(String)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase), um anstelle von zwei Verbindungen eine andere Datenbank im Verbindungskontext zu verwenden.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>CLR-Module und Verbindungsserver können manchmal nicht auf eine lokale IP-Adresse verweisen

CLR-Module in SQL Managed Instance und Verbindungsserver oder verteilte Abfragen, die auf eine aktuelle Instanz verweisen, können die IP-Adresse der lokalen Instanz mitunter nicht auflösen. Dieser Fehler ist ein vorübergehendes Problem.

**Problemumgehung**: Verwenden Sie nach Möglichkeit Kontextverbindungen im CLR-Modul.

## <a name="updates"></a>Aktualisierungen

Eine Liste mit Updates und Verbesserungen für SQL-Datenbank finden Sie unter [Azure-Updates](https://azure.microsoft.com/updates/?product=sql-database).

Informationen zu Updates und Verbesserungen für alle Azure-Dienste finden Sie unter [Dienstupdates](https://azure.microsoft.com/updates).

## <a name="contribute-to-content"></a>Mitwirkung am Inhalt

Informationen zur Mitwirkung an der Azure SQL-Dokumentation finden Sie im [Leitfaden für Dokumentationsmitwirkende](/contribute/).
