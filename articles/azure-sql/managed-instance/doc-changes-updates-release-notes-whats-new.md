---
title: Neuigkeiten
titleSuffix: Azure SQL Managed Instance
description: Hier erfahren Sie mehr über die neuen Features und Dokumentationsverbesserungen für Azure SQL Managed Instance.
services: sql-database
author: MashaMSFT
ms.author: mathoma
ms.service: sql-managed-instance
ms.subservice: service-overview
ms.custom: references_regions, ignite-fall-2021
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/02/2021
ms.openlocfilehash: 5b7d1adfca344444dd1db7040a487e1fec27601f
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551955"
---
# <a name="whats-new-in-azure-sql-managed-instance"></a>Neuerungen in Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqlmi.md)]

In diesem Artikel werden die Dokumentationsänderungen zusammengefasst, die mit neuen Funktionen und Verbesserungen in den neuesten Releases von [Azure SQL Managed Instance](https://azure.microsoft.com/updates/?product=sql-database&query=sql%20managed%20instance) zusammenhängen. Weitere Informationen zu Azure SQL Managed Instance finden Sie unter [Übersicht](sql-managed-instance-paas-overview.md).


Informationen zur Azure SQL-Datenbank finden Sie unter [Neuerungen](../database/doc-changes-updates-release-notes-whats-new.md).

> [!div class="nextstepaction"]
> [Umfrage zur Verbesserung von Azure SQL](https://aka.ms/AzureSQLSurveyNov2021)


## <a name="preview"></a>Vorschau

In der folgenden Tabelle sind die Features von Azure SQL Managed Instance aufgeführt, die sich derzeit in der Vorschau befinden:

| Funktion | Details |
| ---| --- |
| [Unterstützung von 16 TB in Unternehmenskritisch](resource-limits.md#service-tier-characteristics) | Unterstützung für die Belegung von bis zu 16 TB Speicherplatz auf SQL Managed Instance in der Dienstebene „Unternehmenskritisch“ mithilfe der neuen arbeitsspeicheroptimierten Hardwaregeneration der Premium-Serie. | 
|[Endpunktrichtlinien](../../azure-sql/managed-instance/service-endpoint-policies-configure.md) | Konfigurieren Sie, auf welche Azure Storage-Konten über ein SQL Managed Instance-Subnetz zugegriffen werden kann. Bietet zusätzlichen Schutz vor unbeabsichtigter oder böswilliger Datenexfiltration.
| [Instanzenpools](instance-pools-overview.md) | Eine praktische und kostengünstige Möglichkeit, um kleinere SQL Server-Instanzen zur Cloud zu migrieren |
| [Linkfunktion](link-feature.md)| Onlinereplikat von SQL Server Datenbanken, die an einem beliebigen Ort in Azure SQL Managed Instance gehostet sind. |
| [Langfristiges Aufbewahren von Sicherungen](long-term-backup-retention-configure.md) | Unterstützung der langfristigen Sicherungsaufbewahrung von bis zu 10 Jahren für Azure SQL Managed Instance. |
| [Wartungsfenster](../database/maintenance-window.md)| Mithilfe des Features „Wartungsfenster“ können Sie einen Wartungszeitplan für Azure SQL Managed Instance konfigurieren. |
| [Arbeitsspeicheroptimierte Hardwaregeneration der Premium-Serie](resource-limits.md#service-tier-characteristics) | Stellen Sie Ihrer SQL Managed Instance die neue arbeitsspeicheroptimierte Hardwaregeneration der Premium-Serie zur Nutzung der neuesten Intel Ice Lake-CPUs zur Verfügung. Die arbeitsspeicheroptimierte Hardwaregeneration bietet ein höheres Verhältnis von Arbeitsspeicher zu virtuellem Kern. | 
| [Migration mit dem Protokollwiedergabedienst](log-replay-service-migrate.md) | Migrieren von Datenbanken aus SQL Server zu SQL Managed Instance mit dem Protokollwiedergabedienst. |
| [Hardwaregeneration der Premium-Serie](resource-limits.md#service-tier-characteristics) | Stellen Sie Ihrer SQL Managed Instance die neue Hardwaregeneration der Premium-Serie zur Nutzung der neuesten Intel Ice Lake-CPUs zur Verfügung.  | 
| [Instanzübergreifender Nachrichtenaustausch mit dem Service Broker](/sql/database-engine/configure-windows/sql-server-service-broker) | Unterstützung für instanzübergreifenden Nachrichtenaustausch mit dem Service Broker für Azure SQL Managed Instance. |
| [Erkenntnisse zu SQL](../../azure-monitor/insights/sql-insights-overview.md) | Bei SQL Insights handelt es sich um eine umfassende Lösung zur Überwachung jeglichem Produkt der Azure SQ- Familie. SQL Insights verwendet dynamische Managementansichten, um die Daten offenzulegen, die Sie zur Überwachung des Zustands, zur Diagnose von Problemen und zur Leistungsoptimierung benötigen. |
| [Transaktionsreplikation](replication-transactional-overview.md) | Replizieren Sie die Änderungen aus Ihren Tabellen in andere Datenbanken in SQL Managed Instance, SQL-Datenbank oder SQL Server. Sie können auch Ihre Tabellen aktualisieren, wenn einige Zeilen in anderen Instanzen von SQL Managed Instance oder SQL Server geändert werden. Weitere Informationen finden Sie unter [Konfigurieren der Replikation in Azure SQL Managed Instance](replication-between-two-instances-configure-tutorial.md). |
| [Bedrohungserkennung](threat-detection-configure.md) | Die Bedrohungserkennung benachrichtigt Sie über Sicherheitsbedrohungen, die für Ihre Datenbank erkannt wurden. |
| [Abfragespeicherhinweise](/sql/relational-databases/performance/query-store-hints?view=azuresqldb-mi-current&preserve-view=true) | Verwenden Sie Abfragehinweise, um die Abfrageausführung über die OPTION-Klausel zu optimieren. |
|||

## <a name="general-availability-ga"></a>Allgemeine Verfügbarkeit (General Availability, GA)

In der folgenden Tabelle sind die Features von Azure SQL Managed Instance aufgeführt, die innerhalb der letzten 12 Monate von der Vorschauversion auf die allgemeine Verfügbarkeit (General Availability, GA) umgestellt wurden: 

| Feature | GA-Monat | Details |
| ---| --- |--- |
|[16 TB Unterstützung in Universell](resource-limits.md)| November 2021 |  Unterstützung der Zuteilung von bis zu 16 TB Speicherplatz für SQL Managed Instance in der Dienstebene „Universell“. |
[Azure Active Directory – nur Authentifizierung](../database/authentication-azure-ad-only-authentication.md) | November 2021 |  Es ist jetzt möglich, die Authentifizierung für Azure SQL Managed Instance nur auf Azure Active Directory-Benutzer*innen zu beschränken. |
| [Verteilte Transaktionen](../database/elastic-transactions-overview.md) | November 2021 | Mithilfe verteilter Datenbanktransaktionen für Azure SQL Managed Instance können Sie verteilte Transaktionen ausführen, die sich über mehrere Datenbanken und Instanzen erstrecken. | 
|[Verbindungsserver – verwaltete Identität Azure AD-Authentifizierung](/sql/relational-databases/system-stored-procedures/sp-addlinkedserver-transact-sql#h-create-sql-managed-instance-linked-server-with-managed-identity-azure-ad-authentication) |November 2021 | Erstellen Sie einen Verbindungsserver mit Authentifizierung der verwalteten Identität für Ihre Azure SQL Managed Instance.|
|[Verbindungsserver – Passthrough-Azure AD-Authentifizierung](/sql/relational-databases/system-stored-procedures/sp-addlinkedserver-transact-sql#i-create-sql-managed-instance-linked-server-with-pass-through-azure-ad-authentication) |November 2021 | Erstellen Sie einen Verbindungsserver mit Passthrough-Azure AD-Authentifizierung für Ihre Azure SQL Managed Instance. |
| [Verschieben einer Instanz in ein anderes Subnetz](vnet-subnet-move-instance.md)| November 2021 | Verschieben Sie SQL Managed Instance über das Azure-Portal, per Azure PowerShell oder mithilfe der Azure CLI in ein anderes Subnetz.  |
|[Überwachen von Verwaltungsvorgängen](../database/auditing-overview.md#auditing-of-microsoft-support-operations) |  März 2021 | Mit den Azure SQL-Überwachungsfunktionen können Sie Vorgänge überwachen, die von Microsoft-Supporttechnikern durchgeführt werden, wenn diese während einer Supportanfrage auf Ihre SQL-Ressourcen zugreifen müssen. Dies sorgt für mehr Transparenz unter Ihren Mitarbeitern. |
|[Präzise Berechtigungen für die dynamische Datenmaskierung](../database/dynamic-data-masking-overview.md)| März 2021 | Die dynamische Datenmaskierung hilft beim Verhindern des unbefugten Zugriffs auf sensible Daten, indem Kunden festlegen dürfen, welcher Anteil der sensiblen Daten mit minimalen Auswirkungen auf die Anwendungsschicht offengelegt wird. Es handelt sich um eine richtlinienbasierte Sicherheitsfunktion, die die sensiblen Daten im Resultset einer Abfrage in festgelegten Datenbankfeldern ausblendet, während die Daten in der Datenbank nicht geändert werden. Es ist jetzt möglich, präzise Berechtigungen für Daten zuzuweisen, die dynamisch maskiert wurden. Weitere Informationen finden Sie unter [Dynamische Datenmaskierung](../database/dynamic-data-masking-overview.md#permissions). |
|[Machine Learning Service](machine-learning-services-overview.md) | März 2021 | Machine Learning Services ist ein Feature von Azure SQL Managed Instance, das in die Datenbank integriertes maschinelles Lernen bereitstellt und sowohl Python- als auch R-Skripts unterstützt. Das Feature umfasst Python- und R-Pakete von Microsoft für leistungsstarke Predictive Analytics und maschinelles Lernen. |
||| 

## <a name="documentation-changes"></a>Änderungen an der Dokumentation

Hier erfahren Sie mehr über wichtige Änderungen an der Dokumentation zu Azure SQL Managed Instance.

### <a name="november-2021"></a>November 2021

| Änderungen | Details |
| --- | --- |
| **16 TB-Unterstützung für die Vorschauversion von „Unternehmenskritisch“** | Die Dienstebene „Unternehmenskritisch“ von SQL Managed Instance bietet jetzt eine erhöhte maximale Instanzspeicherkapazität von bis zu 16 TB mit den neuen Hardwaregenerationen der Premium- und arbeitsspeicheroptimierten Premium-Serie, die sich derzeit in der Vorschau befinden. Weitere Informationen finden Sie unter [Ressourcenlimits](resource-limits.md#service-tier-characteristics). |
|**16 TB Unterstützung für Universell – allgemeine Verfügbarkeit** | Die Bereitstellung einer 16-TB-Instanz auf Dienstebene „Universell“ ist jetzt allgemein verfügbar.  Weitere Informationen finden Sie unter [Ressourcenlimits](resource-limits.md). |
| **Reine Azure AD-Authentifizierung – allgemeine Verfügbarkeit** |  Das Beschränken der Authentifizierung für Ihre Azure SQL Managed Instance auf Azure Active Directory-Benutzer*innen ist jetzt allgemein verfügbar. Weitere Informationen finden Sie unter [Reine Azure AD-Authentifizierung](../database/authentication-azure-ad-only-authentication.md). |
| **Verteilte Transaktionen – allgemeine Verfügbarkeit** | Die Möglichkeit, verteilte Transaktionen über verwaltete Instanzen hinweg auszuführen, ist jetzt allgemein verfügbar. Weitere Informationen finden Sie unter [Verteilte Transaktionen](../database/elastic-transactions-overview.md). |
|**Endpunktrichtlinien (Vorschauversion)** | Es ist jetzt möglich, eine Endpunktrichtlinie zu konfigurieren, um den Zugriff von einem SQL Managed Instance-Subnetz auf ein Azure Storage Konto einzuschränken. Dies bietet zusätzlichen Schutz vor unbeabsichtigter oder böswilliger Datenexfiltration. Weitere Informationen finden Sie unter [Endpunktrichtlinien](../../azure-sql/managed-instance/service-endpoint-policies-configure.md). |
|**Linkfunktionsvorschau** | Verwenden Sie die Linkfunktion für SQL Managed Instance, um Daten aus Ihrem SQL Server zu replizieren, die an einem beliebigen Ort in Azure SQL Managed Instance gehostet werden. Nutzen Sie dabei die Vorteile von Azure, ohne Ihre Daten in Azure zu verschieben, um Ihre Workloads abzuladen, für die Notfallwiederherstellung zu nutzen oder zur Cloud zu migrieren. Weitere Informationen finden Sie unter [Linkfunktion für SQL Managed Instance](link-feature.md). Linkfunktion befindet sich derzeit in der eingeschränkten öffentlichen Vorschau. |
| **Verschieben einer Instanz in ein anderes Subnetz (allgemeine Verfügbarkeit)** | Es ist jetzt möglich, Ihre Instanz von SQL Managed Instance in ein anderes Subnetz zu verschieben. Weitere Informationen finden Sie unter [Verschieben einer Instanz in ein anderes Subnetz](vnet-subnet-move-instance.md). |
|**Vorschau der neuen Hardwaregeneration** | Es gibt jetzt zwei neue Hardwaregenerationen für SQL Managed Instance: eine Premium-Serie und eine arbeitsspeicheroptimierte Premium-Serie. Beide Angebote nutzen eine neue Generation von Hardware, die von den neuesten Intel Ice Lake-CPUs unterstützt wird, und bieten ein besseres Verhältnis von Arbeitsspeicher virtuellem Kern, um Ihre ressourcenintensiven Datenbankanwendungen zu unterstützen. Im Rahmen dieser Ankündigung wurde die Gen5-Hardwaregeneration in Standard-Serie umbenannt. Die beiden neuen Premium-Hardwaregenerationen befinden sich derzeit in der Vorschau. Weitere Informationen finden Sie unter [Ressourcenlimits](resource-limits.md#service-tier-characteristics). |
| | | 


### <a name="october-2021"></a>Oktober 2021

| Änderungen | Details |
| --- | --- |
|**Unterteilung der Informationen zu neuen Funktionen** | Der zuvor kombinierte Artikel zu den **Neuigkeiten** wurde nach Produkt unterteilt: [Neuigkeiten in Azure SQL-Datenbank](../database/doc-changes-updates-release-notes-whats-new.md) und [Neuigkeiten in SQL Managed Instance](doc-changes-updates-release-notes-whats-new.md). Dadurch können Sie leichter erkennen, welche Features sich derzeit in der Vorschau befinden bzw. allgemein verfügbar sind und welche wichtigen Änderungen an der Dokumentation vorgenommen wurden. Darüber hinaus wurden der Inhalt über [bekannte Probleme in SQL Managed Instance](doc-changes-updates-known-issues.md) auf eine eigene Seite verschoben. | 
|  | | 


### <a name="june-2021"></a>Juni 2021

| Änderungen | Details |
| --- | --- |
|**16 TB Unterstützung für Universell – Vorschau** | Unterstützung für die Zuteilung von bis zu 16 TB Speicherplatz für SQL Managed Instance in der Dienstebene „Universell“ wurde hinzugefügt. Weitere Informationen finden Sie unter [Ressourcenlimits](resource-limits.md). Dieses Instanzangebot befindet sich zurzeit in der Vorschau. | 
| **Parallele Sicherung** | Es ist jetzt möglich, Sicherungen parallel für SQL Managed Instance auf der Ebene „universell“ zu erstellen, um schnellere Sicherungen zu ermöglichen. Weitere Informationen finden Sie im Blogeintrag [Parallele Sicherung für eine bessere Leistung](https://techcommunity.microsoft.com/t5/azure-sql/parallel-backup-for-better-performance-in-sql-managed-instance/ba-p/2421762). |
| **Reine Azure AD-Authentifizierung – Vorschau** | Es ist jetzt möglich, die Authentifizierung für Azure SQL Managed Instance nur auf Azure Active Directory-Benutzer*innen zu beschränken. Diese Funktion steht derzeit als Vorschau zur Verfügung. Weitere Informationen finden Sie unter [Authentifizierung nur über Azure AD](../database/authentication-azure-ad-only-authentication.md). | 
| **Resource Health-Überwachung** | Verwenden Sie Resource Health, um den Integritätsstatus von Azure SQL Managed Instance zu überwachen. Weitere Informationen finden Sie unter [Resource Health](../database/resource-health-to-troubleshoot-connectivity.md). |
| **Präzise Berechtigungen für die dynamische Datenmaskierung (GA)** | Differenzierte Berechtigungen für die dynamische Datenmaskierung für Azure SQL Managed Instance sind jetzt allgemein verfügbar (GA). Weitere Informationen finden Sie unter [Dynamische Datenmaskierung](../database/dynamic-data-masking-overview.md#permissions). | 
|  | |


### <a name="april-2021"></a>April 2021

| Änderungen | Details |
| --- | --- |
| **Benutzerdefinierte Routen (User-Defined Routes, UDR)** | Dienstgestützte Subnetzkonfiguration für Azure SQL Managed Instance nutzt jetzt Diensttags für benutzerdefinierte Routen-Tabellen (user-defined routes, UDR). Weitere Informationen finden Sie unter [Konnektivitätsarchitektur](connectivity-architecture-overview.md). |
|  |  |


### <a name="march-2021"></a>März 2021

| Änderungen | Details |
| --- | --- |
| **Überwachen von Verwaltungsvorgängen** | Die Möglichkeit, SQL Managed Instance-Vorgänge zu überwachen, ist jetzt allgemein verfügbar (GA). | 
| **Protokollwiedergabedienst** | Es ist jetzt möglich, Datenbanken mithilfe des Log Replay-Diensts von SQL Server zu Azure SQL Managed Instance zu migrieren. Weitere Informationen finden Sie unter [Migrieren mit dem Log Replay-Dienst](log-replay-service-migrate.md). Diese Funktion steht derzeit als Vorschau zur Verfügung. | 
| **Langfristiges Aufbewahren von Sicherungen** | Unterstützung der langfristigen Sicherungsaufbewahrung von bis zu 10 Jahren für Azure SQL Managed Instance. Weitere Informationen finden Sie unter [Langfristiges Aufbewahren von Sicherungen](long-term-backup-retention-configure.md).|
| **Machine Learning Services (GA)** | Die Machine Learning Services für Azure SQL Managed Instance sind jetzt allgemein verfügbar (GA). Weitere Informationen finden Sie unter [Machine Learning Services für SQL Managed Instance](machine-learning-services-overview.md).| 
| **Wartungsfenster** | Mithilfe des Features „Wartungsfenster“ können Sie einen Wartungszeitplan für Azure SQL Managed Instance konfigurieren. Dieses Feature befindet sich derzeit in der Vorschau. Weitere Informationen finden Sie unter [Wartungsfenster](../database/maintenance-window.md).|
| **Service Broker-Nachrichtenaustausch** | Mit der Service Broker-Komponente von Azure SQL Managed Instance können Sie Ihre Apps aus unabhängigen, eigenständigen Diensten zusammenstellen, indem Sie native Unterstützung für einen zuverlässigen und sicheren Nachrichtenaustausch zwischen den Datenbanken bereitstellen, die an den Dienst angefügt sind. Derzeit in der Vorschauphase. Weitere Informationen finden Sie unter [Service Broker](/sql/database-engine/configure-windows/sql-server-service-broker).
| **Erkenntnisse zu SQL** | Bei SQL Insights handelt es sich um eine umfassende Lösung zur Überwachung jeglichem Produkt der Azure SQ- Familie. SQL Insights verwendet dynamische Managementansichten, um die Daten offenzulegen, die Sie zur Überwachung des Zustands, zur Diagnose von Problemen und zur Leistungsoptimierung benötigen. Weitere Informationen finden Sie unter [SQL Insights](../../azure-monitor/insights/sql-insights-overview.md). | 
||| 

### <a name="2020"></a>2020

Die folgenden Änderungen wurden SQL Managed Instance und der Dokumentation 2020 hinzugefügt: 

| Änderungen | Details |
| --- | --- |
| **Überwachung von Supportvorgängen** | Die Überwachungsfunktion für Microsoft-Supportvorgänge ermöglicht Ihnen, diese Supportvorgänge zu überwachen, wenn Sie während einer Supportanforderung auf Ihre Server und/oder Datenbanken zugreifen müssen, um Ihr Überwachungsprotokoll abzurufen (Vorschau). Weitere Informationen finden Sie unter[Überwachung von Supportvorgängen](../database/auditing-overview.md#auditing-of-microsoft-support-operations).|
| **Elastische Transaktionen** | Elastische Transaktionen ermöglichen verteilte Datenbanktransaktionen, die sich über mehrere Datenbanken in der Azure SQL-Datenbank und Azure SQL Managed Instance erstrecken. Elastische Transaktionen wurden hinzugefügt, um die reibungslose Migration vorhandener Apps sowie die Entwicklung moderner Apps mit mehreren Mandanten zu ermöglichen, die auf einer vertikal oder horizontal partitionierten Datenbankarchitektur beruhen (Vorschau). Weitere Informationen finden Sie unter [Verteilte Transaktionen](../database/elastic-transactions-overview.md#transactions-for-sql-managed-instance). | 
| **Konfigurierbare Redundanz für Sicherungsspeicher** | Es ist jetzt möglich, Optionen für lokal redundanten Speicher (LRS) und zonenredundanten Speicher (ZRS) für Sicherungsspeicherredundanz zu konfigurieren, um mehr Flexibilität und Auswahl zu erhalten. Weitere Informationen finden Sie unter [Konfigurieren der Sicherungsspeicherredundanz](../database/automated-backups-overview.md?tabs=managed-instance#configure-backup-storage-redundancy).|
| **Leistungsverbesserungen bei TDE-verschlüsselten Sicherungen** | Es ist jetzt möglich, den Aufbewahrungszeitraum von Sicherungen der Zeitpunktwiederherstellung (Point-in-Time Restore, PITR) festzulegen. Außerdem ist die automatisierte Komprimierung von Sicherungen, die mit transparenter Datenverschlüsselung (Transparent Data Encryption, TDE) verschlüsselt sind, jetzt um 30 Prozent effizienter bei der Nutzung von Sicherungsspeicher, was kostensparend für Endbenutzer*innen ist. Weitere Informationen finden Sie unter [Ändern von PITR](../database/automated-backups-overview.md?tabs=managed-instance#change-the-short-term-retention-policy). |
| **Verbesserungen bei der Azure AD-Authentifizierung** | Automatisieren Sie die Benutzererstellung mit Azure AD-Apps, und erstellen Sie einzelne Azure AD-Gastbenutzer*innen (Vorschau). Weitere Informationen finden Sie unter [Verzeichnisleseberechtigte in Azure AD](../database/authentication-aad-directory-readers-role.md).|
| **Unterstützung des globalen VNet-Peerings** | Die Unterstützung für globales Peering virtueller Netzwerke wurde SQL Managed Instance hinzugefügt, um die Georeplikation zu verbessern. Weitere Informationen finden Sie unter [Georeplikation zwischen verwalteten Instanzen](../database/auto-failover-group-overview.md?tabs=azure-powershell#enabling-geo-replication-between-managed-instances-and-their-vnets). |
| **Hosten von SSRS-Katalogdatenbanken** | SQL Managed Instance kann jetzt Katalogdatenbanken für alle unterstützten Versionen von SQL Server Reporting Services (SSRS) hosten. | 
| **Wichtige Leistungsverbesserungen** | Einführung von Verbesserungen der Leistung von SQL Managed Instance, einschließlich verbessertem Transaktionsprotokoll-Schreibdurchsatz, verbessertem Daten- und Protokoll-IOPS für unternehmenskritische Instanzen und verbesserter TempDB-Leistung. Weitere Informationen finden Sie im Tech Community-Blog zur [verbesserten Leistung](https://techcommunity.microsoft.com/t5/azure-sql/announcing-major-performance-improvements-for-azure-sql-database/ba-p/1701256). 
| **Verbesserte Benutzeroberfläche für die Verwaltung** | Mithilfe der neuen [Vorgangs-API](/rest/api/sql/2021-02-01-preview/managed-instance-operations) ist es jetzt möglich, den Fortschritt von Instanzvorgängen mit langer Laufzeit zu überprüfen. Weitere Informationen finden Sie unter [Verwaltungsvorgänge](management-operations-overview.md?tabs=azure-portal).
| **Machine Learning-Support** | Machine Learning Services mit Support für die Sprachen R und Python bieten jetzt einen Vorschausupport in Azure SQL Managed Instance (Vorschau). Weitere Informationen finden Sie unter [Machine Learning mit SQL Managed Instance](machine-learning-services-overview.md). | 
| **Vom Benutzer initiiertes Failover** | Vom Benutzer initiiertes Failover ist jetzt allgemein verfügbar und bietet Ihnen die Möglichkeit, ein automatisches Failover mithilfe von PowerShell, CLI-Befehlen und API-Aufrufen manuell einzuleiten, wobei die Resilienz der App verbessert wird. Weitere Informationen finden Sie unter [Testen der Resilienz](../database/high-availability-sla.md#testing-application-fault-resiliency). 
|  |  |



## <a name="known-issues"></a>Bekannte Probleme

Die Informationen zu [bekannten Problemen in SQL Managed Instance](doc-changes-updates-known-issues.md) wurden in einen eigenen Artikel verschoben. 


## <a name="contribute-to-content"></a>Mitwirkung am Inhalt

Informationen zur Mitwirkung an der Azure SQL-Dokumentation finden Sie im [Leitfaden für Dokumentationsmitwirkende](/contribute/).
