---
title: Automatisches Bereinigen von SSISDB-Protokollen
description: In diesem Artikel wird beschrieben, wie Sie die in SSISDB gespeicherten SSIS-Projektbereitstellungs- und -Paketausführungsprotokolle bereinigen können, indem Sie die entsprechende gespeicherte SSISDB-Prozedur über Azure Data Factory, Azure SQL Managed Instance Agent oder „Aufträge für die elastische Datenbank“ automatisch aufrufen.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/22/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 78b6c754fe2b49ceabf940aa76b86ddd90dfc91e
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131843653"
---
# <a name="how-to-clean-up-ssisdb-logs-automatically"></a>Automatisches Bereinigen von SSISDB-Protokollen

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Nachdem Sie in Azure Data Factory (ADF) eine Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) bereitgestellt haben, können Sie damit SSIS-Pakete ausführen, die bereitgestellt werden in:

- SSIS-Katalog (SSISDB), gehostet von Azure SQL-Datenbank-Server/Managed Instance (Projektbereitstellungsmodell)
- Dateisystem, Azure Files oder SQL Server-Datenbank (MSDB), gehostet von Azure SQL Managed Instance (Paketbereitstellungsmodell)

Im Projektbereitstellungsmodell stellt Ihre Azure-SSIS Integration Runtime SSIS-Projekte in SSISDB bereit, ruft SSIS-Pakete für die Ausführung aus SSISDB ab und schreibt Protokolle zur Paketausführung zurück in SSISDB. SSISDB wird auch zum Speichern von SSIS-Auftrags- und Integration Runtime-Vorgangsprotokollen verwendet. Zum Verwalten der gesammelten Protokolle haben wir entsprechende SSISDB-Eigenschaften und gespeicherte Prozeduren bereitgestellt, die über ADF, Azure SQL Managed Instance Agent oder „Aufträge für die elastische Datenbank“ nach Zeitplan automatisch aufgerufen werden können.

## <a name="ssisdb-log-clean-up-properties-and-stored-procedures"></a>SSISDB-Protokollbereinigungseigenschaften und gespeicherte Prozeduren
Zum Verwalten von SSIS-Paketausführungsprotokollen können Sie SSISDB-Protokollbereinigungseigenschaften konfigurieren, indem Sie eine Verbindung mit SSISDB herstellen, gehostet von Ihrem Azure SQL-Datenbank-Server/Managed Instance mithilfe von SQL Server Management Studio (SSMS). Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit SSISDB](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial?view=sql-server-ver15&preserve-view=true#connect-to-the-ssisdb-database). Nachdem die Verbindung hergestellt wurde, können Sie im Fenster **Objekt-Explorer** von SSMS den Knoten **Integration Services-Kataloge** erweitern, mit der rechten Maustaste auf den Unterknoten **SSISDB** klicken und das Menüelement **Eigenschaften** auswählen, um das Dialogfeld **Katalogeigenschaften** zu öffnen. Das Dialogfeld **Katalogeigenschaften** enthält die folgenden SSISDB-Protokollbereinigungseigenschaften:

- **Protokolle regelmäßig bereinigen**: Aktiviert die Bereinigung von Paketausführungsprotokollen; standardmäßig auf *True* festgelegt.
- **Aufbewahrungszeitraum (Tage)** : Gibt das maximale Alter der aufbewahrten Protokolle (in Tagen) an, das standardmäßig auf *365* festgelegt ist. Ältere Protokolle werden gelöscht, wenn die entsprechende gespeicherte SSISDB-Prozedur aufgerufen wird.
- **Alte Versionen regelmäßig entfernen**: Aktiviert die Bereinigung gespeicherter Projektversionen; standardmäßig auf *True* festgelegt.
- **Maximale Anzahl der Versionen pro Project**: Gibt die maximale Anzahl von gespeicherten Projektversionen an, die standardmäßig auf *10* festgelegt ist. Ältere Protokolle werden gelöscht, wenn die entsprechende gespeicherte SSISDB-Prozedur aufgerufen wird.

:::image type="content" source="media/how-to-clean-up-ssisdb-logs-with-elastic-jobs/clean-up-logs-ssms-ssisdb-properties.png" alt-text="SSISDB-Protokollbereinigungseigenschaften":::

Nachdem die SSISDB-Protokollbereinigungseigenschaften konfiguriert wurden, können Sie die entsprechende gespeicherte SSISDB-Prozedur, `[internal].[cleanup_server_retention_window_exclusive]`, aufrufen, um SSIS-Paketausführungsprotokolle zu bereinigen.

Zum Bereinigen von SSIS-Auftragsprotokollen können Sie die entsprechende gespeicherte SSISDB-Prozedur, `[internal].[cleanup_completed_jobs_exclusive]`, aufrufen. Der Aufbewahrungszeitraum ist standardmäßig auf 60 Minuten festgelegt, und ältere Protokolle werden beim Aufrufen der gespeicherten Prozedur gelöscht.

Zum Bereinigen von SSIS Integration Runtime-Auftragsprotokollen können Sie die entsprechende gespeicherte SSISDB-Prozedur, `[internal].[cleanup_expired_worker]`, aufrufen. Der Aufbewahrungszeitraum ist standardmäßig auf 168 Stunden festgelegt. Ältere Protokolle werden beim Aufrufen der gespeicherten Prozedur gelöscht.

Diese gespeicherten SSISDB-Prozeduren bereinigen verschiedene SSISDB-Tabellen:

| Gespeicherte SSISDB-Prozeduren | Zu bereinigende SSISDB-Tabellen |
|--------------------------|---------------------------|
| `[internal].[cleanup_server_retention_window_exclusive]` | `[internal].[event_message_context_scaleout]`<br/>`[internal].[event_messages_scaleout]`<br/>`[internal].[executable_statistics]`<br/>`[internal].[execution_component_phases]`<br/>`[internal].[execution_data_statistics]`<br/>`[internal].[execution_data_taps]`<br/>`[internal].[execution_parameter_values]`<br/>`[internal].[execution_parameter_values_noncatalog]`<br/>`[internal].[execution_property_override_values]`<br/>`[internal].[execution_property_override_values_noncatalog]`<br/>`[internal].[executions]`<br/>`[internal].[executions_noncatalog]`<br/>`[internal].[extended_operation_info]`<br/>`[internal].[operation_messages]`<br/>`[internal].[operation_messages_scaleout]`<br/>`[internal].[operation_permissions]`<br/>`[internal].[operations]`<br/>`[internal].[validations]` |
| `[internal].[cleanup_completed_jobs_exclusive]` | `[internal].[job_worker_agents]`<br/>`[internal].[jobs]`<br/>`[internal].[tasks]` |
| `[internal].[cleanup_expired_worker]` | `[internal].[worker_agents]` |

Diese gespeicherten SSISDB-Prozeduren können auch über ADF, Azure SQL Managed Instance Agent oder „Aufträge für die elastische Datenbank“ nach Zeitplan automatisch aufgerufen werden.

## <a name="clean-up-ssisdb-logs-automatically-via-adf"></a>Automatisches Bereinigen von SSISDB-Protokollen über ADF
Unabhängig davon, ob Sie Azure SQL-Datenbank-Server/Managed Instance zum Hosten von SSISDB verwenden, können Sie ADF immer verwenden, um SSISDB-Protokolle nach Zeitplan automatisch zu bereinigen. Hierzu können Sie eine Aktivität des Typs „SSIS-Paket ausführen“ in einer ADF-Pipeline mit einem eingebetteten Paket vorbereiten, das einen einzelnen Task „SQL ausführen“ zum Aufrufen der entsprechenden gespeicherten SSISDB-Prozeduren enthält. Sehen Sie sich dazu Beispiel 4) in unserem Blog [Run Any SQL Anywhere in 3 Easy Steps with SSIS in Azure Data Factory](https://techcommunity.microsoft.com/t5/sql-server-integration-services/run-any-sql-anywhere-in-3-easy-steps-with-ssis-in-azure-data/ba-p/2457244) (Ausführen von SQL an beliebigen Orten in drei einfachen Schritten mit SSIS in Azure Data Factory) an.

:::image type="content" source="media/how-to-clean-up-ssisdb-logs-with-elastic-jobs/run-sql-ssis-activity-ssis-parameters-ssisdb-clean-up.png" alt-text="SSISDB-Protokollbereinigung über ADF":::

Für den Parameter **SQLStatementSource** können Sie `EXEC internal.cleanup_server_retention_window_exclusive` eingeben, um SSIS-Paketausführungsprotokolle zu bereinigen. 

Zum Bereinigen von SSIS-Auftragsprotokollen können Sie `EXEC internal.cleanup_completed_jobs_exclusive [@minutesToKeep=’Number of minutes to set as retention period’]` hinzufügen. 

Zum Bereinigen von SSIS Integration Runtime-Vorgangsprotokollen können Sie `EXEC internal.cleanup_expired_worker [@hoursToKeep=’Number of hours to set as retention period’] ` hinzufügen.

Nachdem Ihre ADF-Pipeline vorbereitet wurde, können Sie einen Plantrigger anfügen, damit sie regelmäßig ausgeführt wird. Weitere Informationen finden Sie unter [How to trigger ADF pipeline on a schedule](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule) (Auslösen der ADF-Pipeline nach einem Zeitplan).

## <a name="clean-up-ssisdb-logs-automatically-via-azure-sql-managed-instance-agent"></a>Automatisches Bereinigen von SSISDB-Protokollen über Azure SQL Managed Instance Agent
Wenn Sie Azure SQL Managed Instance zum Hosten von SSISDB verwenden, können Sie auch den integrierten Auftragsorchestrator/Scheduler, Azure SQL Managed Instance Agent, verwenden, um SSISDB-Protokolle nach Zeitplan automatisch zu bereinigen. Wenn SSISDB kürzlich in Ihrer Azure SQL Managed Instance erstellt wurde, haben wir außerdem unter Azure SQL Managed Instance Agent einen Transact-SQL-Auftrag namens **SSIS-Serverwartungsauftrag** erstellt, um speziell SSIS-Paketausführungsprotokolle zu bereinigen. Er ist standardmäßig deaktiviert und mit einem Zeitplan für tägliche Ausführung konfiguriert.  Wenn Sie den Transact-SQL-Auftrag aktivieren und/oder seinen Zeitplan neu konfigurieren möchten, können Sie dazu eine Verbindung mit Ihrer Azure SQL Managed Instance mithilfe von SSMS herstellen. Nachdem die Verbindung hergestellt wurde, können Sie im Fenster **Objekt-Explorer** von SSMS den Knoten **SQL Server-Agent** erweitern, dann den Unterknoten **Aufträge** und auf den **Wartungsauftrag für SSIS-Server** doppelklicken, um ihn zu aktivieren/neu zu konfigurieren.

:::image type="content" source="media/how-to-clean-up-ssisdb-logs-with-elastic-jobs/clean-up-logs-ssms-maintenance-job.png" alt-text="SSISDB-Protokollbereinigung über Azure SQL Managed Instance Agent":::

Wenn für Ihren Azure SQL Managed Instance Agent noch kein **Wartungsauftrag für SSIS-Server** erstellt wurde, können Sie ihn manuell hinzufügen, indem Sie das folgende Transact-SQL-Skript auf Ihrer Azure SQL Managed Instance ausführen.

```sql
USE msdb
IF EXISTS(SELECT * FROM sys.server_principals where name = '##MS_SSISServerCleanupJobLogin##')
   DROP LOGIN ##MS_SSISServerCleanupJobLogin##

DECLARE @loginPassword nvarchar(256)
SELECT @loginPassword = REPLACE (CONVERT( nvarchar(256), CRYPT_GEN_RANDOM( 64 )), N'''', N'''''')
EXEC ('CREATE LOGIN ##MS_SSISServerCleanupJobLogin## WITH PASSWORD =''' +@loginPassword + ''', CHECK_POLICY = OFF')
ALTER LOGIN ##MS_SSISServerCleanupJobLogin## DISABLE

USE master
GRANT VIEW SERVER STATE TO ##MS_SSISServerCleanupJobLogin##

USE SSISDB
IF EXISTS (SELECT name FROM sys.database_principals WHERE name = '##MS_SSISServerCleanupJobUser##')
    DROP USER ##MS_SSISServerCleanupJobUser##
CREATE USER ##MS_SSISServerCleanupJobUser## FOR LOGIN ##MS_SSISServerCleanupJobLogin##
GRANT EXECUTE ON [internal].[cleanup_server_retention_window_exclusive] TO ##MS_SSISServerCleanupJobUser##
GRANT EXECUTE ON [internal].[cleanup_server_project_version] TO ##MS_SSISServerCleanupJobUser##

USE msdb
EXEC dbo.sp_add_job
    @job_name = N'SSIS Server Maintenance Job', 
    @enabled = 0,
    @owner_login_name = '##MS_SSISServerCleanupJobLogin##',
    @description = N'Runs every day. The job removes operation records from the database that are outside the retention period and maintains a maximum number of versions per project.'

DECLARE @IS_server_name NVARCHAR(30)
SELECT @IS_server_name = CONVERT(NVARCHAR, SERVERPROPERTY('ServerName'))
EXEC sp_add_jobserver  @job_name = N'SSIS Server Maintenance Job',
                       @server_name = @IS_server_name

EXEC sp_add_jobstep
    @job_name = N'SSIS Server Maintenance Job',
    @step_name = N'SSIS Server Operation Records Maintenance',
    @subsystem = N'TSQL',
    @command = N'
       DECLARE @role int
       SET @role = (SELECT [role] FROM [sys].[dm_hadr_availability_replica_states] hars INNER JOIN [sys].[availability_databases_cluster] adc ON hars.[group_id] = adc.[group_id] WHERE hars.[is_local] = 1 AND adc.[database_name] =''SSISDB'')
       IF DB_ID(''SSISDB'') IS NOT NULL AND (@role IS NULL OR @role = 1)
              EXEC [SSISDB].[internal].[cleanup_server_retention_window_exclusive]',
    @database_name = N'msdb',
    @on_success_action = 3,
    @retry_attempts = 3,
    @retry_interval = 3;

EXEC sp_add_jobstep
    @job_name = N'SSIS Server Maintenance Job',
    @step_name = N'SSIS Server Max Version Per Project Maintenance',
    @subsystem = N'TSQL',
    @command = N'
       DECLARE @role int
       SET @role = (SELECT [role] FROM [sys].[dm_hadr_availability_replica_states] hars INNER JOIN [sys].[availability_databases_cluster] adc ON hars.[group_id] = adc.[group_id] WHERE hars.[is_local] = 1 AND adc.[database_name] =''SSISDB'')
       IF DB_ID(''SSISDB'') IS NOT NULL AND (@role IS NULL OR @role = 1)
              EXEC [SSISDB].[internal].[cleanup_server_project_version]',
    @database_name = N'msdb',
    @retry_attempts = 3,
    @retry_interval = 3;

EXEC sp_add_jobschedule
    @job_name = N'SSIS Server Maintenance Job',
    @name = 'SSISDB Scheduler',
    @enabled = 1,
    @freq_type = 4, /*daily*/
    @freq_interval = 1,/*every day*/
    @freq_subday_type = 0x1,
    @active_start_date = 20001231,
    @active_end_date = 99991231,
    @active_start_time = 0,
    @active_end_time = 120000
```

Sie können auch den vorhandenen **SSIS-Serverwartungsauftrag** konfigurieren oder das vorstehende Transact-SQL-Skript zum zusätzlichen Bereinigen von SSIS-Auftrags-/Integration Runtime-Vorgangsprotokollen ändern, indem Sie die entsprechenden gespeicherten SSISDB-Prozeduren aufrufen.

## <a name="clean-up-ssisdb-logs-automatically-via-elastic-database-jobs"></a>Automatisches Bereinigen von SSISDB-Protokollen über „Aufträge für die elastische Datenbank“
Wenn Sie Azure SQL-Datenbank-Server zum Hosten von SSISDB verwenden, enthält er keinen integrierten Auftragsorchestrator/Scheduler. Deshalb müssen Sie eine externe Komponente verwenden, z. B. ADF (siehe oben) oder „Aufträge für die elastische Datenbank“ (siehe die restlichen Abschnitte), um SSISDB-Protokolle nach Zeitplan automatisch zu bereinigen.

„Aufträge für die elastische Datenbank“ ist ein Azure-Dienst, der Aufträge für eine Datenbank oder eine Gruppe von Datenbanken automatisieren und ausführen kann. Sie können diese Aufträge im Azure-Portal, mithilfe von Azure PowerShell, Transact-SQL oder REST-APIs planen, ausführen und überwachen. Mithilfe von „Aufträge für die elastische Datenbank“ können Sie die relevanten gespeicherten SSISDB-Prozeduren für die Protokollbereinigung einmal oder nach einem Zeitplan aufrufen. Sie können das Zeitplanintervall basierend auf der SSISDB-Ressourcenverwendung auswählen, um eine hohe Datenbanklast zu vermeiden.

Weitere Informationen finden Sie unter [Verwalten von Datenbankgruppen mithilfe von Aufträgen für die elastische Datenbank](../azure-sql/database/elastic-jobs-overview.md).

In den folgenden Abschnitten wird beschrieben, wie Sie die relevanten gespeicherten SSISDB-Prozeduren, `[internal].[cleanup_server_retention_window_exclusive]`/`[internal].[cleanup_completed_jobs_exclusive]`/`[internal].[cleanup_expired_worker]`, aufrufen, durch die SSISDB-Protokolle entfernt werden, die außerhalb ihrer jeweiligen Aufbewahrungsdauern liegen.

### <a name="configure-elastic-database-jobs-using-azure-powershell"></a>Konfigurieren von „Aufträge für die elastische Datenbank“ mithilfe von Azure PowerShell

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

Die folgenden Azure PowerShell-Skripts erstellen einen neuen elastischen Auftrag, der Ihre ausgewählte gespeicherte SSISDB-Protokollbereinigungsprozedur aufruft. Weitere Informationen finden Sie unter [Erstellen eines Agents für elastische Aufträge mithilfe von PowerShell](../azure-sql/database/elastic-jobs-powershell-create.md).

#### <a name="create-parameters"></a>Erstellen von Parametern

``` powershell
# Parameters needed to create your job database
param(
$ResourceGroupName = $(Read-Host "Please enter an existing resource group name"),
$AgentServerName = $(Read-Host "Please enter the name of an existing Azure SQL Database server, for example myjobserver, to hold your job database"),
$SSISDBLogCleanupJobDB = $(Read-Host "Please enter a name for your job database to be created in the given Azure SQL Database server"),
$StoredProcName = $(Read-Host "Please enter the name of SSISDB log clean-up stored procedure to be invoked by your job (internal.cleanup_server_retention_window_exclusive/internal.cleanup_completed_jobs_exclusive/internal.cleanup_expired_worker)"), 

# Your job database should be a clean, empty S0 or higher service tier. We set S0 as default.
$PricingTier = "S0",

# Parameters needed to create your Elastic Job agent
$SSISDBLogCleanupAgentName = $(Read-Host "Please enter a name for your Elastic Job agent"),

# Parameters needed to create credentials in your job database for connecting to SSISDB
$PasswordForSSISDBCleanupUser = $(Read-Host "Please provide a new password for the log clean-up job user to connect to SSISDB"),

# Parameters needed to create the login and user for SSISDB
$SSISDBServerEndpoint = $(Read-Host "Please enter the name of target Azure SQL Database server that contains SSISDB, for example myssisdbserver") + '.database.windows.net',
$SSISDBServerAdminUserName = $(Read-Host "Please enter the target server admin username for SQL authentication"),
$SSISDBServerAdminPassword = $(Read-Host "Please enter the target server admin password for SQL authentication"),
$SSISDBName = "SSISDB",

# Parameters needed to set the job schedule for invoking SSISDB log clean-up stored procedure
$RunJobOrNot = $(Read-Host "Please indicate whether you want to run your job that cleans up SSISDB logs outside their retention period immediately (Y/N). Make sure the specific retention period is set properly before running the following scripts as deleted logs cannot be recovered."),
$IntervalType = $(Read-Host "Please enter the interval type for SSISDB log clean-up schedule: Year, Month, Day, Hour, Minute, Second are supported."),
$IntervalCount = $(Read-Host "Please enter the count of interval type for SSISDB log clean-up schedule."),

# The start time for SSISDB log clean-up schedule is set to current time by default. 
$StartTime = (Get-Date)
```

#### <a name="invoke-ssisdb-log-clean-up-stored-procedure"></a>Aufrufen der gespeicherten SSISDB-Protokollbereinigungsprozedur

```powershell
# Install the latest PowerShell PackageManagement module that PowerShellGet v1.6.5 depends on
Find-Package PackageManagement -RequiredVersion 1.1.7.2 | Install-Package -Force

# You may need to restart your PowerShell session
# Install the latest PowerShellGet module that adds the -AllowPrerelease flag to Install-Module
Find-Package PowerShellGet -RequiredVersion 1.6.5 | Install-Package -Force

# Install AzureRM.Sql preview cmdlets side by side with the existing AzureRM.Sql version
Install-Module -Name AzureRM.Sql -AllowPrerelease -Force

# Sign in to your Azure account
Connect-AzureRmAccount

# Create your job database for defining SSISDB log clean-up job and tracking the job history
Write-Output "Creating a blank SQL database to be used as your job database ..."
$JobDatabase = New-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $AgentServerName -DatabaseName $SSISDBLogCleanupJobDB -RequestedServiceObjectiveName $PricingTier
$JobDatabase

# Enable Elastic Database Jobs preview in your Azure subscription
Register-AzureRmProviderFeature -FeatureName sqldb-JobAccounts -ProviderNamespace Microsoft.Sql

# Create your Elastic Job agent
Write-Output "Creating your Elastic Job agent..."
$JobAgent = $JobDatabase | New-AzureRmSqlElasticJobAgent -Name $SSISDBLogCleanupAgentName
$JobAgent

# Create job credentials in your job database for connecting to SSISDB in target server
Write-Output "Creating job credentials for connecting to SSISDB..."
$JobCredSecure = ConvertTo-SecureString -String $PasswordForSSISDBCleanupUser -AsPlainText -Force
$JobCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "SSISDBLogCleanupUser", $JobCredSecure
$JobCred = $JobAgent | New-AzureRmSqlElasticJobCredential -Name "SSISDBLogCleanupUser" -Credential $JobCred

# Create the job user login in master database of target server
Write-Output "Grant permissions on the master database of target server..."
$Params = @{
  'Database' = 'master'
  'ServerInstance' = $SSISDBServerEndpoint
  'Username' = $SSISDBServerAdminUserName
  'Password' = $SSISDBServerAdminPassword
  'OutputSqlErrors' = $true
  'Query' = "CREATE LOGIN SSISDBLogCleanupUser WITH PASSWORD = '" + $PasswordForSSISDBCleanupUser + "'"
}
Invoke-SqlCmd @Params

# Create SSISDB log clean-up user from login in SSISDB and grant it permissions to invoke SSISDB log clean-up stored procedure
Write-Output "Grant appropriate permissions on SSISDB..."
$TargetDatabase = $SSISDBName
$CreateJobUser = "CREATE USER SSISDBLogCleanupUser FROM LOGIN SSISDBLogCleanupUser"
$GrantStoredProcedureExecution = "GRANT EXECUTE ON " + $StoredProcName + " TO SSISDBLogCleanupUser" 

$TargetDatabase | ForEach-Object -Process {
  $Params.Database = $_
  $Params.Query = $CreateJobUser
  Invoke-SqlCmd @Params
  $Params.Query = $GrantStoredProcedureExecution
  Invoke-SqlCmd @Params
}

# Create your target group that includes only SSISDB to clean up
Write-Output "Creating your target group that includes only SSISDB to clean up..."
$SSISDBTargetGroup = $JobAgent | New-AzureRmSqlElasticJobTargetGroup -Name "SSISDBTargetGroup"
$SSISDBTargetGroup | Add-AzureRmSqlElasticJobTarget -ServerName $SSISDBServerEndpoint -Database $SSISDBName 

# Create your job to invoke SSISDB log clean-up stored procedure
Write-Output "Creating your job to invoke SSISDB log clean-up stored procedure..."
$JobName = "CleanupSSISDBLog"
$Job = $JobAgent | New-AzureRmSqlElasticJob -Name $JobName -RunOnce
$Job

# Add your job step to invoke SSISDB log clean-up stored procedure
Write-Output "Adding your job step to invoke SSISDB log clean-up stored procedure..."
$SqlText = "EXEC " + $StoredProcName 
$Job | Add-AzureRmSqlElasticJobStep -Name "Step to invoke SSISDB log clean-up stored procedure" -TargetGroupName $SSISDBTargetGroup.TargetGroupName -CredentialName $JobCred.CredentialName -CommandText $SqlText

# Run your job to immediately invoke SSISDB log clean-up stored procedure once
if ($RunJobOrNot -eq 'Y')
{
Write-Output "Invoking SSISDB log clean-up stored procedure immediately..."
$JobExecution = $Job | Start-AzureRmSqlElasticJob
$JobExecution
}

# Schedule your job to invoke SSISDB log clean-up stored procedure periodically, deleting SSISDB logs outside their retention period
Write-Output "Starting your schedule to invoke SSISDB log clean-up stored procedure periodically..."
$Job | Set-AzureRmSqlElasticJob -IntervalType $IntervalType -IntervalCount $IntervalCount -StartTime $StartTime -Enable
```

### <a name="configure-elastic-database-jobs-using-t-sql"></a>Konfigurieren von „Aufträge für die elastische Datenbank“ mithilfe von Transact-SQL

Die folgenden Transact-SQL-Skripts erstellen einen neuen elastischen Auftrag, der Ihre ausgewählte gespeicherte SSISDB-Protokollbereinigungsprozedur aufruft. Weitere Informationen finden Sie unter [Erstellen und Verwalten von Aufträgen für die elastische Datenbank mit Transact-SQL](../azure-sql/database/elastic-jobs-tsql-create-manage.md).

1. Identifizieren Sie eine leere S0/höhere Dienstebene von Azure SQL-Datenbank, oder erstellen Sie eine neue Dienstebene für Ihre Auftragsdatenbank. Erstellen Sie anschließend im [Azure-Portal](https://ms.portal.azure.com/#create/Microsoft.SQLElasticJobAgent) einen Agent für elastische Aufträge.

2. Erstellen Sie in Ihrer Auftragsdatenbank Anmeldeinformationen zum Herstellen einer Verbindung mit SSISDB auf Ihrem Zielserver.

   ```sql
   -- Connect to the job database specified when creating your job agent.
   -- Create a database master key if one doesn't already exist, using your own password.
   CREATE MASTER KEY ENCRYPTION BY PASSWORD= '<EnterStrongPasswordHere>';

   -- Create credentials for SSISDB log clean-up.
   CREATE DATABASE SCOPED CREDENTIAL SSISDBLogCleanupCred WITH IDENTITY = 'SSISDBLogCleanupUser', SECRET = '<EnterStrongPasswordHere>'; 
   ```

3. Definieren Sie Ihre Zielgruppe, die nur die zu bereinigende SSISDB enthält.

   ```sql
   -- Connect to your job database.
   -- Add your target group.
   EXEC jobs.sp_add_target_group 'SSISDBTargetGroup'

   -- Add SSISDB to your target group
   EXEC jobs.sp_add_target_group_member 'SSISDBTargetGroup',
   @target_type = 'SqlDatabase',
   @server_name = '<EnterSSISDBTargetServerName>',
   @database_name = 'SSISDB'

   -- View your recently created target group and its members.
   SELECT * FROM jobs.target_groups WHERE target_group_name = 'SSISDBTargetGroup';
   SELECT * FROM jobs.target_group_members WHERE target_group_name = 'SSISDBTargetGroup';
   ```

4. Erstellen Sie aus der Anmeldung in SSISDB einen SSISDB-Protokollbereinigungsbenutzer, und erteilen Sie ihm Berechtigungen zum Aufrufen der gespeicherten SSISDB-Protokollbereinigungsprozedur. Eine ausführliche Anleitung finden Sie unter [Verwalten von Anmeldungen](../azure-sql/database/logins-create-manage.md).

   ```sql
   -- Connect to the master database of target server that hosts SSISDB 
   CREATE LOGIN SSISDBLogCleanupUser WITH PASSWORD = '<strong_password>';

   -- Connect to SSISDB
   CREATE USER SSISDBLogCleanupUser FROM LOGIN SSISDBLogCleanupUser;
   GRANT EXECUTE ON '<internal.cleanup_server_retention_window_exclusive/internal.cleanup_completed_jobs_exclusive/internal.cleanup_expired_worker>' TO SSISDBLogCleanupUser 
   ```

5. Erstellen Sie Ihren Auftrag, und fügen Sie Ihren Auftragsschritt hinzu, um die gespeicherte SSISDB-Protokollbereinigungsprozedur aufzurufen.

   ```sql
   -- Connect to your job database.
   -- Add your job to invoke the relevant SSISDB log clean-up stored procedure.
   EXEC jobs.sp_add_job @job_name='CleanupSSISDBLog', @description='Remove SSISDB logs outside their specific retention period'

   -- Add your job step to invoke the relevant SSISDB log clean-up stored procedure
   EXEC jobs.sp_add_jobstep @job_name='CleanupSSISDBLog',
   @command=N'<EXEC internal.cleanup_server_retention_window_exclusive/EXEC internal.cleanup_completed_jobs_exclusive/EXEC internal.cleanup_expired_worker>',
   @credential_name='SSISDBLogCleanupCred',
   @target_group_name='SSISDBTargetGroup'
   ```

6. Bevor Sie den Vorgang fortsetzen, müssen Sie den spezifischen Aufbewahrungszeitraum ordnungsgemäß festlegen. SSISDB-Protokolle außerhalb dieses Zeitraums werden gelöscht und können nicht wiederhergestellt werden. Sie können ihren Auftrag dann sofort ausführen, um die SSISDB-Protokollbereinigung zu starten.

   ```sql
   -- Connect to your job database.
   -- Run your job immediately to invoke SSISDB log clean-up stored procedure.
   declare @je uniqueidentifier
   exec jobs.sp_start_job 'CleanupSSISDBLog', @job_execution_id = @je output

   -- Watch SSISDB log clean-up results 
   select @je
   select * from jobs.job_executions where job_execution_id = @je
   ```

7. Optional können Sie SSISDB-Protokolle außerhalb ihres Aufbewahrungszeitraums nach einem Zeitplan löschen. Konfigurieren Sie Ihre Auftragsparameter wie folgt.

   ```sql
   -- Connect to your job database.
   EXEC jobs.sp_update_job
   @job_name='CleanupSSISDBLog',
   @enabled=1,
   @schedule_interval_type='<EnterIntervalType(Month,Day,Hour,Minute,Second)>',
   @schedule_interval_count='<EnterDetailedIntervalValue>',
   @schedule_start_time='<EnterProperStartTimeForSchedule>',
   @schedule_end_time='<EnterProperEndTimeForSchedule>'
   ```

### <a name="monitor-ssisdb-log-clean-up-job-using-azure-portal"></a>Überwachen des SSISDB-Protokollbereinigungsauftrags im Azure-Portal

Sie können den SSISDB-Protokollbereinigungsauftrag im Azure-Portal überwachen. Bei jeder Ausführung können Sie deren Status, Start- und Endzeit anzeigen.

:::image type="content" source="media/how-to-clean-up-ssisdb-logs-with-elastic-jobs/monitor-cleanup-job-portal.png" alt-text="Überwachen des SSISDB-Protokollbereinigungsauftrags im Azure-Portal":::

### <a name="monitor-ssisdb-log-clean-up-job-using-t-sql"></a>Überwachen des SSISDB-Protokollbereinigungsauftrags mithilfe von Transact-SQL

Sie können den Ausführungsverlauf des SSISDB-Protokollbereinigungsauftrags auch mithilfe von Transact-SQL anzeigen.

```sql
-- Connect to your job database.
-- View all SSISDB log clean-up job executions.
SELECT * FROM jobs.job_executions WHERE job_name = 'CleanupSSISDBLog' 
ORDER BY start_time DESC

-- View all active executions.
SELECT * FROM jobs.job_executions WHERE is_active = 1
ORDER BY start_time DESC
```

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Verwalten und Überwachen Ihrer Azure-SSIS Integration Runtime finden Sie in den folgenden Artikeln.

- [Neukonfigurieren der Azure-SSIS-Integration Runtime](manage-azure-ssis-integration-runtime.md)

- [Überwachen einer Integrationslaufzeit in Azure Data Factory](monitor-integration-runtime.md#azure-ssis-integration-runtime).
