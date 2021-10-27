---
title: Auftragsautomatisierung mit SQL-Agent-Aufträgen
titleSuffix: Azure SQL Managed Instance
description: Automatisierungsoptionen zum Ausführen von T-SQL-Skripts (Transact-SQL) in Azure SQL Managed Instance
services: sql-database
ms.service: sql-db-mi
ms.subservice: deployment-configuration
ms.custom: sqldbrb=1
dev_langs:
- TSQL
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 06/03/2021
ms.openlocfilehash: 8d23e2d2c1c6123a986e47a7f2d8e384c99eb5af
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130046808"
---
# <a name="automate-management-tasks-using-sql-agent-jobs-in-azure-sql-managed-instance"></a>Automatisieren von Verwaltungsaufgaben mithilfe von SQL-Agent-Aufträgen in Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Sie können mithilfe von [SQL Server-Agent](/sql/ssms/agent/sql-server-agent) und [SQL Managed Instance](sql-managed-instance-paas-overview.md) Aufträge erstellen und planen, die in regelmäßigen Abständen für einzelne oder mehrere Datenbanken ausgeführt werden können, um T-SQL-Abfragen (Transact-SQL) und Wartungsaufgaben auszuführen. Dieser Artikel behandelt die Verwendung von SQL-Agent für SQL Managed Instance.

> [!Note]
> SQL-Agent ist in SQL-Datenbank oder Azure Synapse Analytics nicht verfügbar. Stattdessen empfehlen wir [Auftragsautomatisierung mit elastischen Aufträgen](../database/job-automation-overview.md).

### <a name="sql-agent-job-limitations-in-sql-managed-instance"></a>SQL-Agent-Auftragseinschränkungen in SQL Managed Instance

Es ist erwähnenswert, dass es Unterschiede zwischen dem in SQL Server verfügbaren SQL-Agent und dem Agent gibt, der als Teil von SQL Managed Instance bereitgestellt wird. Weitere Informationen zu den unterstützten Funktionsunterschieden zwischen SQL Server und SQL Managed Instance finden Sie unter [Azure SQL Managed Instance: T-SQL-Unterschiede von SQL Server](../../azure-sql/managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent). 

Einige der in SQL Server verfügbaren SQL-Agent-Features werden in SQL Managed Instance nicht unterstützt:

- SQL-Agent-Einstellungen sind schreibgeschützt. 
    - Die gespeicherte Systemprozedur `sp_set_agent_properties` wird nicht unterstützt.
- Das Aktivieren/Deaktivieren des SQL-Agents wird derzeit nicht unterstützt. Der SQL-Agent wird immer ausgeführt.
- Benachrichtigungen werden teilweise unterstützt:
  - Der Pager wird nicht unterstützt.
  - NetSend wird nicht unterstützt.
  - Warnungen werden nicht unterstützt.
- Proxys werden nicht unterstützt.
- EventLog wird nicht unterstützt.
- Der auf einer CPU im Leerlauf basierende Auftragszeitplan-Trigger wird nicht unterstützt.

## <a name="when-to-use-sql-agent-jobs"></a>Verwenden von SQL-Agent-Aufträgen 

Es gibt mehrere Szenarien, in denen Sie SQL-Agent-Aufträge verwenden können:

- Automatisierung von Verwaltungsaufgaben und Planung der Ausführung beispielsweise an jedem Werktag oder nach Geschäftsschluss usw.
  - Bereitstellung von Schemaänderungen, Anmeldeinformationsverwaltung, Leistungsdatensammlung oder Telemetrieerfassung für Mandanten (Kunden).
  - Aktualisierung von Referenzdaten (datenbankübergreifende Informationen), Laden von Daten aus Azure Blob Storage. Microsoft empfiehlt die Verwendung der [SHARED ACCESS SIGNATURE-Authentifizierung für die Authentifizierung bei Azure Blob Storage](/sql/t-sql/statements/bulk-insert-transact-sql#f-importing-data-from-a-file-in-azure-blob-storage).
  - Allgemeine Wartungsaufgaben, einschließlich `DBCC CHECKDB` für die Sicherstellung der Datenintegrität oder Indexwartung zur Verbesserung der Abfrageleistung. Konfiguration von Aufträgen, sodass diese für eine gesamte Sammlung von Datenbanken auf wiederkehrender Basis ausgeführt werden (etwa in Zeiten mit geringer Auslastung).
  - Sammlung von Abfrageergebnissen aus einer Menge von Datenbanken in einer zentralen Tabelle auf fortlaufender Grundlage. Leistungsabfragen können fortlaufend ausgeführt werden und für die Auslösung der Ausführung weiterer Aufgaben konfiguriert werden.
- Sammeln von Berichtsdaten
  - Aggregation von Daten aus einer Sammlung von Datenbanken in einer einzelnen Zieltabelle.
  - Ausführung von Abfragen zur Datenverarbeitung mit längerer Laufzeit für eine große Anzahl von Datenbanken, z.B. bei der Sammlung von Kundentelemetrie. Die Ergebnisse werden zur weiteren Analyse in einer einzelnen Zieltabelle gesammelt.
- Datenverschiebungen
  - Erstellung von Aufträgen, die Änderungen an Ihren Datenbanken in anderen Datenbanken replizieren oder Aktualisierungen aus Remotedatenbanken erfassen und die Änderungen in der Datenbank anwenden.
  - Erstellung von Aufträgen, die Daten mithilfe von SQL Server Integration Services (SSIS) aus Ihren oder in Ihre Datenbanken laden.

## <a name="sql-agent-jobs-in-sql-managed-instance"></a>SQL-Agent-Aufträge in SQL Managed Instance

SQL-Agent-Aufträge werden vom SQL-Agent-Dienst ausgeführt, der weiterhin für Aufgabenautomatisierung in SQL Server und SQL Managed Instance verwendet wird. 

Bei SQL-Agent-Aufträgen handelt es sich um eine angegebene Reihe von T-SQL-Skripts, die für Ihre Datenbank ausgeführt werden. Verwenden Sie Aufträge, um eine Verwaltungsaufgabe zu definieren, die einmalig oder mehrmals ausgeführt und deren Erfolgsstatus überwacht werden kann. 

Ein Auftrag kann auf einem einzelnen lokalen Server oder auf mehreren Remoteservern ausgeführt werden. Ein SQL-Agent-Auftrag ist eine interne Komponente der Datenbank-Engine, die innerhalb des SQL Managed Instance-Diensts ausgeführt wird.

Im Zusammenhang mit SQL-Agent-Aufträgen gibt es mehrere wichtige Konzepte:

- **Auftragsschritte:** Eine Gruppe von einzelnen oder mehreren Schritten, die im Rahmen des Auftrags ausgeführt werden sollen. Für jeden Auftragsschritt können Sie eine Wiederholungsstrategie und die Aktion definieren, die ausgeführt werden soll, wenn der Auftragsschritt erfolgreich oder nicht erfolgreich war.
- **Zeitpläne:** Definieren, wann der Auftrag ausgeführt werden soll.
- **Benachrichtigungen:** Ermöglichen es, Regeln zu definieren, auf deren Grundlage Bediener nach Abschluss des Auftrags per E-Mail benachrichtigt werden.

### <a name="sql-agent-job-steps"></a>SQL-Agent-Auftragsschritte

SQL-Agent-Auftragsschritte sind Aktionssequenzen, die der SQL-Agent ausführen soll. Jeder Schritt verfügt über den nächsten Schritt, der ausgeführt werden soll, wenn der Schritt erfolgreich oder nicht erfolgreich war, sowie die Anzahl von Wiederholungsversuchen, falls der Schritt nicht erfolgreich war.

Der SQL-Agent ermöglicht die Erstellung verschiedener Arten von Auftragsschritten. Hierzu zählen etwa Transact-SQL-Auftragsschritte zum Ausführen eines einzelnen Transact-SQL-Batchs in der Datenbank, Betriebssystembefehls-/PowerShell-Schritte zum Ausführen eines benutzerdefinierten Betriebssystemskripts, [SSIS-Auftragsschritte](../../data-factory/how-to-invoke-ssis-package-managed-instance-agent.md) zum Laden von Daten unter Verwendung der SSIS-Runtime sowie [Replikationsschritte](../managed-instance/replication-transactional-overview.md) zum Veröffentlichen von Änderungen aus Ihrer Datenbank in anderen Datenbanken.

> [!Note]
> Weitere Informationen zum Nutzen der Azure SSIS Integration Runtime mit SSISDB gehostet von SQL Managed Instance finden Sie unter [Verwenden von Azure SQL Managed Instance mit SQL Server Integration Services (SSIS) in Azure Data Factory](../../data-factory/how-to-use-sql-managed-instance-with-ir.md).

[Transaktionsreplikation](../managed-instance/replication-transactional-overview.md) kann die Änderungen aus Ihren Tabellen in andere Datenbanken in SQL Managed Instance, SQL-Datenbank oder SQL Server replizieren. Weitere Informationen finden Sie unter [Konfigurieren der Replikation in Azure SQL Managed Instance](../../azure-sql/managed-instance/replication-between-two-instances-configure-tutorial.md). 

Andere Arten von Auftragsschritten werden derzeit in SQL Managed Instance nicht unterstützt, einschließlich:

- Der Auftragsschritt Mergereplikation wird nicht unterstützt.
- Der Warteschlangenleser wird nicht unterstützt.
- Analysis Services wird nicht unterstützt.
 
### <a name="sql-agent-job-schedules"></a>SQL-Agent-Auftragszeitpläne

Ein Zeitplan gibt an, wann ein Auftrag ausgeführt wird. Mehrere Aufträge können auf dem gleichen Zeitplan basieren, und für einen Auftrag können mehrere Zeitpläne gelten.

Ein Zeitplan kann folgende Bedingungen für die Ausführungszeit eines Auftrags definieren:

- Bei jedem Start des SQL Server-Agents. Der Auftrag wird nach jedem Failover aktiviert.
- Einmalig zum angegebenen Zeitpunkt (Datum und Uhrzeit). Hilfreich für die verzögerte Ausführung eines Auftrags.
- Auf der Grundlage einer Zeitplanserie.

> [!Note]
> SQL Managed Instance bietet derzeit keine Möglichkeit, Aufträge zu starten, wenn sich die CPU im Leerlauf befindet.

### <a name="sql-agent-job-notifications"></a>SQL-Agent-Auftragsbenachrichtigungen

SQL-Agent-Aufträge ermöglichen es, Benachrichtigungen zu erhalten, wenn der Auftrag erfolgreich abgeschlossen wurde oder ein Fehler aufgetreten ist. Sie können Benachrichtigungen per E-Mail erhalten.

Wenn nicht bereits geschehen, müssen Sie zunächst [das Datenbank-E-Mail-Feature](/sql/relational-databases/database-mail/database-mail) für SQL Managed Instance konfigurieren:

```sql
GO
EXEC sp_configure 'show advanced options', 1;
GO
RECONFIGURE;
GO
EXEC sp_configure 'Database Mail XPs', 1;
GO
RECONFIGURE
```

Richten Sie als Beispielübung das E-Mail-Konto ein, das zum Senden der E-Mail-Benachrichtigungen verwendet wird. Weisen Sie das Konto dem E-Mail-Profil namens `AzureManagedInstance_dbmail_profile` zu. Um E-Mail-Nachrichten mithilfe von SQL-Agent-Aufträgen in SQL Managed Instance zu senden, muss ein Profil mit dem Namen `AzureManagedInstance_dbmail_profile` vorhanden sein. Andernfalls kann SQL Managed Instance keine E-Mails über den SQL-Agent senden. Sehen Sie sich das folgende Beispiel an:

```sql
-- Create a Database Mail account
EXECUTE msdb.dbo.sysmail_add_account_sp
    @account_name = 'SQL Agent Account',
    @description = 'Mail account for Azure SQL Managed Instance SQL Agent system.',
    @email_address = '$(loginEmail)',
    @display_name = 'SQL Agent Account',
    @mailserver_name = '$(mailserver)' ,
    @username = '$(loginEmail)' ,
    @password = '$(password)';

-- Create a Database Mail profile
EXECUTE msdb.dbo.sysmail_add_profile_sp
    @profile_name = 'AzureManagedInstance_dbmail_profile',
    @description = 'E-mail profile used for messages sent by Managed Instance SQL Agent.';

-- Add the account to the profile
EXECUTE msdb.dbo.sysmail_add_profileaccount_sp
    @profile_name = 'AzureManagedInstance_dbmail_profile',
    @account_name = 'SQL Agent Account',
    @sequence_number = 1;
```

Testen Sie die E-Mail-Konfiguration der Datenbank über T-SQL mithilfe der gespeicherten Systemprozedur [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql):

```sql
DECLARE @body VARCHAR(4000) = 'The email is sent from ' + @@SERVERNAME;
EXEC msdb.dbo.sp_send_dbmail  
    @profile_name = 'AzureManagedInstance_dbmail_profile',  
    @recipients = 'ADD YOUR EMAIL HERE',  
    @body = 'Add some text',  
    @subject = 'Azure SQL Instance - test email';  
```

Sie können Bediener über Vorkommnisse im Zusammenhang mit Ihren SQL-Agent-Aufträgen informieren. Ein Bediener definiert Kontaktinformationen für eine Person, die für die Wartung einzelner oder mehrerer Instanzen in SQL Managed Instance zuständig ist. Manchmal sind die Aufgaben eines Bedieners einer einzelnen Person zugewiesen.

In Systemen mit mehreren Instanzen in SQL Managed Instance oder in SQL Server können die Bedieneraufgaben auf zahlreiche Personen verteilt sein. Ein Bediener verfügt über keine Sicherheitsinformationen und definiert keinen Sicherheitsprinzipal. Im Idealfall ist ein Bediener keine Einzelperson, deren Zuständigkeiten sich ändern können, sondern eine E-Mail-Verteilergruppe.   

Bediener können mithilfe von SQL Server Management Studio (SSMS) oder mit einem Transact-SQL-Skript [erstellt](/sql/relational-databases/system-stored-procedures/sp-add-operator-transact-sql) werden, wie im folgenden Beispiel gezeigt:

```sql
EXEC msdb.dbo.sp_add_operator
    @name=N'AzureSQLTeam',
    @enabled=1,
    @email_address=N'AzureSQLTeamn@contoso.com';
```

Bestätigen Sie den Erfolg oder Misserfolg der E-Mail über das [Datenbank-E-Mail Protokoll](/sql/relational-databases/database-mail/database-mail-log-and-audits) in SSMS.

Sie können dann [jeden SQL-Agent-Auftrag ändern](/sql/relational-databases/system-stored-procedures/sp-update-job-transact-sql) und Bediener zuweisen, die über E-Mail benachrichtigt werden, wenn ein Auftrag abgeschlossen wird, nicht erfolgreich war oder erfolgreich war. Hierzu können Sie entweder SSMS oder das folgende Transact-SQL-Skript verwenden:

```sql
EXEC msdb.dbo.sp_update_job @job_name=N'Load data using SSIS',
    @notify_level_email=3, -- Options are: 1 on succeed, 2 on failure, 3 on complete
    @notify_email_operator_name=N'AzureSQLTeam';
```

### <a name="sql-agent-job-history"></a>SQL-Agent-Auftragsverlauf

Mit SQL Managed Instance können Sie derzeit keine SQL-Agent-Eigenschaften ändern, da diese in den zugrunde liegenden Registrierungswerten gespeichert sind. Das bedeutet, dass die Optionen zum Anpassen der Agent-Aufbewahrungsrichtlinie für Auftragsverlaufsdatensätze auf den Standardwert von 1.000 Datensätzen insgesamt und maximal 100 Verlaufsdatensätze pro Auftrag festgelegt sind.

### <a name="sql-agent-fixed-database-role-membership"></a>Mitgliedschaft in fester SQL-Agent-Datenbankrolle

Wenn mit Nicht-Systemadministratoranmeldungen verknüpfte Benutzer einer der drei festen SQL-Agent-Datenbankrollen in der msdb-Datenbank hinzugefügt werden, gibt es ein Problem, bei dem drei gespeicherten Masterprozeduren in der Masterdatenbank explizite EXECUTE-Berechtigungen gewährt werden müssen. Wenn dieses Problem auftritt, wird die Fehlermeldung „The EXECUTE permission was denied on the object <object_name> (Microsoft SQL Server, Error: 229)“ (Die Berechtigung EXECUTE wurde für das Objekt <objekt_name> verweigert (Microsoft SQL Server, Fehler: 229) angezeigt. 

Sobald Sie Benutzer einer festen SQL-Agent-Datenbankrolle (SQLAgentUserRole, SQLAgentReaderRole oder SQLAgentOperatorRole) in msdb hinzugefügt haben, führen Sie für jede der zu diesen Rollen hinzugefügten Benutzeranmeldungen das folgende T-SQL-Skript aus, um explizit EXECUTE-Berechtigungen für die aufgelisteten gespeicherten Systemprozeduren zu erteilen. In diesem Beispiel wird davon ausgegangen, dass der Benutzername und der Anmeldename identisch sind:

```sql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name];
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name];
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name];
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name];
```

## <a name="learn-more"></a>Erfahren Sie mehr

- [Was ist Azure SQL Managed Instance?](../managed-instance/sql-managed-instance-paas-overview.md)
- [Neuerungen in Azure SQL Managed Instance](doc-changes-updates-release-notes-whats-new.md)
- [T-SQL-Unterschiede zwischen Azure SQL Managed Instance und SQL Server](../../azure-sql/managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)
- [Featurevergleich: Azure SQL-Datenbank und Azure SQL Managed Instance](../../azure-sql/database/features-comparison.md)
