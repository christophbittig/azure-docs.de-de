---
title: Neuigkeiten
titleSuffix: Azure SQL Managed Instance
description: Hier erfahren Sie mehr über die neuen Features und Dokumentationsverbesserungen für Azure SQL Managed Instance.
services: sql-database
author: MashaMSFT
ms.author: mathoma
ms.service: sql-managed-instance
ms.subservice: service-overview
ms.custom: references_regions
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/21/2021
ms.openlocfilehash: 59147dd6037e5eb2f4565ec135d04f3a1c242cc3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130247927"
---
# <a name="whats-new-in-azure-sql-managed-instance"></a>Neuerungen in Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqlmi.md)]

In diesem Artikel werden die Dokumentationsänderungen zusammengefasst, die mit neuen Funktionen und Verbesserungen in den neuesten Releases von [Azure SQL Managed Instance](https://azure.microsoft.com/updates/?product=sql-database&query=sql%20managed%20instance) zusammenhängen. Weitere Informationen zu Azure SQL Managed Instance finden Sie unter [Übersicht](sql-managed-instance-paas-overview.md).


Informationen zur Azure SQL-Datenbank finden Sie unter [Neuerungen](../database/doc-changes-updates-release-notes-whats-new.md).


## <a name="preview"></a>Vorschau

In der folgenden Tabelle sind die Features von Azure SQL Managed Instance aufgeführt, die sich derzeit in der Vorschau befinden:


| Funktion | Details |
| ---| --- |
| [Unterstützung von 16 TB für SQL Managed Instance (universell)](resource-limits.md)| Unterstützung der Zuteilung von bis zu 16 TB Speicherplatz für SQL Managed Instance (universell) |
| [Authentifizierung für Azure SQL ausschließlich über Azure Active Directory](../database/authentication-azure-ad-only-authentication.md) |  Es ist jetzt möglich, die Authentifizierung für Azure SQL Managed Instance auf Azure Active Directory-Benutzer*innen zu beschränken. |
| [Elastische Transaktionen](../database/elastic-transactions-overview.md) | Mit elastischen Transaktionen können Sie verteilte Transaktionen über Clouddatenbanken in Azure SQL-Datenbank und Azure SQL Managed Instance ausführen. |
| [Instanzenpools](instance-pools-overview.md) | Eine praktische und kostengünstige Möglichkeit, um kleinere SQL Server-Instanzen zur Cloud zu migrieren |
| [Migration mit dem Protokollwiedergabedienst](log-replay-service-migrate.md) | Migrieren von Datenbanken aus SQL Server zu SQL Managed Instance mit dem Protokollwiedergabedienst. |
| [Wartungsfenster](../database/maintenance-window.md)| Mithilfe des Features „Wartungsfenster“ können Sie einen Wartungszeitplan für Azure SQL Managed Instance konfigurieren. |
| [Langfristiges Aufbewahren von Sicherungen](long-term-backup-retention-configure.md) | Unterstützung der langfristigen Sicherungsaufbewahrung von bis zu 10 Jahren für Azure SQL Managed Instance. |
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
| [Machine Learning Service](machine-learning-services-overview.md) | März 2021 | Machine Learning Services ist ein Feature von Azure SQL Managed Instance, das in die Datenbank integriertes maschinelles Lernen bereitstellt und sowohl Python- als auch R-Skripts unterstützt. Das Feature umfasst Python- und R-Pakete von Microsoft für leistungsstarke Predictive Analytics und maschinelles Lernen. |
| [Präzise Berechtigungen für die dynamische Datenmaskierung](../database/dynamic-data-masking-overview.md)| März 2021 | Die dynamische Datenmaskierung hilft beim Verhindern des unbefugten Zugriffs auf sensible Daten, indem Kunden festlegen dürfen, welcher Anteil der sensiblen Daten mit minimalen Auswirkungen auf die Anwendungsschicht offengelegt wird. Es handelt sich um eine richtlinienbasierte Sicherheitsfunktion, die die sensiblen Daten im Resultset einer Abfrage in festgelegten Datenbankfeldern ausblendet, während die Daten in der Datenbank nicht geändert werden. Es ist jetzt möglich, präzise Berechtigungen für Daten zuzuweisen, die dynamisch maskiert wurden. Weitere Informationen finden Sie unter [Dynamische Datenmaskierung](../database/dynamic-data-masking-overview.md#permissions). |
| [Überwachen von Verwaltungsvorgängen](../database/auditing-overview.md#auditing-of-microsoft-support-operations) |  März 2021 | Mit den Azure SQL-Überwachungsfunktionen können Sie Vorgänge überwachen, die von Microsoft-Supporttechnikern durchgeführt werden, wenn diese während einer Supportanfrage auf Ihre SQL-Ressourcen zugreifen müssen. Dies sorgt für mehr Transparenz unter Ihren Mitarbeitern. | 
||| 

## <a name="documentation-changes"></a>Änderungen an der Dokumentation

Hier erfahren Sie mehr über wichtige Änderungen an der Dokumentation zu Azure SQL Managed Instance.

### <a name="october-2021"></a>Oktober 2021

| Änderungen | Details |
| --- | --- |
|**Unterteilung der Informationen zu neuen Funktionen** | Der zuvor kombinierte Artikel zu den **Neuigkeiten** wurde nach Produkt unterteilt: [Neuigkeiten in Azure SQL-Datenbank](../database/doc-changes-updates-release-notes-whats-new.md) und [Neuigkeiten in SQL Managed Instance](doc-changes-updates-release-notes-whats-new.md). Dadurch können Sie leichter erkennen, welche Features sich derzeit in der Vorschau befinden bzw. allgemein verfügbar sind und welche wichtigen Änderungen an der Dokumentation vorgenommen wurden. Darüber hinaus wurden der Inhalt über [bekannte Probleme in SQL Managed Instance](doc-changes-updates-known-issues.md) auf eine eigene Seite verschoben.  | 


### <a name="june-2021"></a>Juni 2021

| Änderungen | Details |
| --- | --- |
|**16 TB Unterstützung für Universell** | Unterstützung für die Zuteilung von bis zu 16 TB Speicherplatz für SQL Managed Instance Universell wurde hinzugefügt. Weitere Informationen finden Sie unter [Ressourcenlimits](resource-limits.md). Dieses Instanzangebot befindet sich zurzeit in der Vorschau. | 
| **Parallele Sicherung** | Es ist jetzt möglich, Sicherungen parallel für SQL Managed Instance auf der Ebene „universell“ zu erstellen, um schnellere Sicherungen zu ermöglichen. Weitere Informationen finden Sie im Blogeintrag [Parallele Sicherung für eine bessere Leistung](https://techcommunity.microsoft.com/t5/azure-sql/parallel-backup-for-better-performance-in-sql-managed-instance/ba-p/2421762). |
| **Reine Azure AD-Authentifizierung** | Es ist jetzt möglich, die Authentifizierung für Azure SQL Managed Instance auf Azure Active Directory-Benutzer*innen zu beschränken. Diese Funktion steht derzeit als Vorschau zur Verfügung. Weitere Informationen finden Sie unter [Authentifizierung nur über Azure AD](../database/authentication-azure-ad-only-authentication.md). | 
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
| **Elastische Transaktionen** | Elastische Transaktionen ermöglichen verteilte Datenbanktransaktionen, die sich über mehrere Datenbanken in der Azure SQL-Datenbank und Azure SQL Managed Instance erstrecken. Elastische Transaktionen wurden hinzugefügt, um die reibungslose Migration vorhandener Apps sowie die Entwicklung moderner Apps mit mehreren Mandanten zu ermöglichen, die auf einer vertikal oder horizontal partitionierten Datenbankarchitektur beruhen (Vorschau). Weitere Informationen finden Sie unter [Verteilte Transaktionen](../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance). | 
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
