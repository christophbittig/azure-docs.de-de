---
title: 'Azure SQL-Datenbank: Verwalten der langfristigen Aufbewahrung von Sicherungen'
description: Erfahren Sie, wie Sie mit dem Azure-Portal, der Azure CLI und PowerShell automatisierte Sicherungen für Azure SQL-Datenbank bis zu 10 Jahre lang in Azure Storage speichern und wiederherstellen.
services: sql-database
ms.service: sql-db-mi
ms.subservice: backup-restore
ms.custom: devx-track-azurepowershell
ms.devlang: ''
ms.topic: how-to
author: SQLSourabh
ms.author: sourabha
ms.reviewer: mathoma
ms.date: 12/16/2020
ms.openlocfilehash: 72f92c1659497cda8e4f595df17899a4367d00b9
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130236242"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Verwalten der langfristigen Aufbewahrung von Sicherungen in Azure SQL-Datenbank
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

In Azure SQL-Datenbank können Sie eine Richtlinie für die [Langzeitaufbewahrung von Sicherungen](long-term-retention-overview.md) (Long-Term Retention, LTR) festlegen, um die Sicherungen automatisch in separaten Azure Blob Storage-Containern bis zu 10 Jahre lang aufzubewahren. Sie können dann mit dem Azure-Portal, der Azure CLI oder PowerShell eine Datenbank anhand dieser Sicherungen wiederherstellen. Richtlinien für die Langzeitaufbewahrung werden auch für [Azure SQL Managed Instance](../managed-instance/long-term-backup-retention-configure.md) unterstützt.

## <a name="prerequisites"></a>Voraussetzungen

# <a name="portal"></a>[Portal](#tab/portal)

Ein aktives Azure-Abonnement.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Bereiten Sie die Umgebung für die Azure CLI vor.

[!INCLUDE[azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Bereiten Sie die Umgebung für PowerShell vor.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Das PowerShell Azure Resource Manager-Modul wird von Azure SQL-Datenbank weiterhin unterstützt, aber alle zukünftigen Entwicklungen erfolgen für das Az.Sql-Modul. Informationen zu diesen Cmdlets finden Sie unter [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Die Argumente für die Befehle im Az-Modul und den AzureRm-Modulen sind im Wesentlichen identisch.

Für **Get-AzSqlDatabaseLongTermRetentionBackup** und **Restore-AzSqlDatabase** müssen Sie über eine der folgenden Rollen verfügen:

- Rolle „Besitzer des Abonnements“ oder
- Rolle „Mitwirkender von SQL Server“ oder
- Benutzerdefinierte Rolle mit den folgenden Berechtigungen:

   Microsoft.Sql/locations/longTermRetentionBackups/read Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read

Für **Remove-AzSqlDatabaseLongTermRetentionBackup** müssen Sie über eine der folgenden Rollen verfügen:

- Rolle „Besitzer des Abonnements“ oder
- Benutzerdefinierte Rolle mit der folgenden Berechtigung:

   Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete

> [!NOTE]
> Die Rolle „Mitwirkender von SQL Server“ besitzt keine Berechtigung zum Löschen von LTR-Sicherungen.

Azure RBAC-Berechtigungen können im Bereich *Abonnement* oder *Ressourcengruppe* erteilt werden. Für den Zugriff auf LTR-Sicherungen, die zu einem gelöschten Server gehören, muss die Berechtigung jedoch im Bereich *Abonnement* dieses Servers erteilt werden.

- Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete

---

## <a name="create-long-term-retention-policies"></a>Erstellen von Richtlinien für die Langzeitaufbewahrung

# <a name="portal"></a>[Portal](#tab/portal)

Sie können SQL-Datenbank [zur Aufbewahrung von automatisierten Sicherungen](long-term-retention-overview.md) für einen längeren Zeitraum konfigurieren, als gemäß der Aufbewahrungsdauer für Ihre Dienstebene vorgesehen ist.

1. Navigieren Sie im Azure-Portal zu Ihrem Server, und wählen Sie **Sicherungen** aus. Wählen Sie die Registerkarte **Aufbewahrungsrichtlinien** aus, um Ihre Einstellungen für die Sicherungsaufbewahrung zu ändern.

   ![Registerkarte „Aufbewahrungsrichtlinien“](./media/long-term-backup-retention-configure/ltr-policies-tab.png)

2. Wählen Sie auf der Registerkarte „Aufbewahrungsrichtlinien“ die Datenbank(en) aus, für die Sie Richtlinien für die Langzeitaufbewahrung von Sicherungen festlegen oder ändern möchten. Nicht ausgewählte Datenbanken sind nicht betroffen.

   ![Auswählen der Datenbank zum Konfigurieren von Sicherungsaufbewahrungsrichtlinien](./media/long-term-backup-retention-configure/ltr-policies-tab-configure.png)

3. Geben Sie im Bereich **Richtlinien konfigurieren** den gewünschten Aufbewahrungszeitraum für wöchentliche, monatliche oder jährliche Sicherungen an. Wählen Sie den Aufbewahrungszeitraum „0“ aus, um anzugeben, dass keine Langzeitaufbewahrung von Sicherungen festgelegt werden soll.

   ![Bereich „Richtlinien konfigurieren“](./media/long-term-backup-retention-configure/ltr-configure-policies.png)

4. Wählen Sie **Anwenden** aus, um die ausgewählten Datenaufbewahrungseinstellungen auf alle ausgewählten Datenbanken anzuwenden.

> [!IMPORTANT]
> Wenn Sie eine Richtlinie für die Langzeitaufbewahrung von Sicherungen aktivieren, kann es bis zu 7 Tage dauern, bevor die erste Sicherung angezeigt wird und wiederhergestellt werden kann. Weitere Informationen dazu, in welchen Intervallen Sicherungskopien für die Langzeitaufbewahrung erstellt werden, finden Sie unter [Langzeitaufbewahrung von Sicherungen](long-term-retention-overview.md).

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Führen Sie den Befehl [az sql db ltr-policy set](/cli/azure/sql/db/ltr-policy#az_sql_db_ltr_policy_set) aus, um eine LTR-Richtlinie zu erstellen. Im folgenden Beispiel wird eine Richtlinie für die Langzeitaufbewahrung für die wöchentliche Sicherung über 12 Wochen definiert.

```azurecli
az sql db ltr-policy set \
   --resource-group mygroup \
   --server myserver \
   --name mydb \
   --weekly-retention "P12W"
```

In diesem Beispiel wird eine Aufbewahrungsrichtlinie für 12 Wochen für die wöchentliche Sicherung, für 5 Jahre für die jährliche Sicherung und für die Woche ab dem 15. April erstellt, in der die jährliche LTR-Sicherung erstellt werden soll.

```azurecli
az sql db ltr-policy set \
   --resource-group mygroup \
   --server myserver \
   --name mydb \
   --weekly-retention "P12W" \
   --yearly-retention "P5Y" \
   --week-of-year 16
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
# get the SQL server
$subId = "<subscriptionId>"
$serverName = "<serverName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subId

$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

---

## <a name="view-backups-and-restore-from-a-backup"></a>Anzeigen von Sicherungen und Wiederherstellen einer Sicherung

Zeigen Sie die Sicherungen an, die für eine bestimmte Datenbank mit einer LTR-Richtlinie aufbewahrt werden, und führen Sie die Wiederherstellung aus diesen Sicherungen aus.

# <a name="portal"></a>[Portal](#tab/portal)

1. Navigieren Sie im Azure-Portal zu Ihrem Server, und wählen Sie **Sicherungen** aus. Um die verfügbaren LTR-Sicherungen für eine bestimmte Datenbank anzuzeigen, wählen Sie in der Spalte „Verfügbare LTR-Sicherungen“ die Option **Verwalten** aus. Ein Bereich mit einer Liste der verfügbaren LTR-Sicherungen für die ausgewählte Datenbank wird angezeigt.

   ![Registerkarte „Verfügbare Sicherungen“](./media/long-term-backup-retention-configure/ltr-available-backups-tab.png)

1. Überprüfen Sie im Bereich **Verfügbare LTR-Sicherungen** die verfügbaren Sicherungen. Sie können eine Sicherung zum Wiederherstellen oder Löschen auswählen.

   ![Anzeige verfügbarer LTR-Sicherungen](./media/long-term-backup-retention-configure/ltr-available-backups-manage.png)

1. Wählen Sie zum Wiederherstellen aus einer verfügbaren LTR-Sicherung die Sicherung, die Sie zum Wiederherstellen nutzen möchten, und dann **Wiederherstellen** aus.

   ![Wiederherstellen aus einer verfügbaren LTR-Sicherung](./media/long-term-backup-retention-configure/ltr-available-backups-restore.png)

1. Wählen Sie einen Namen für die neue Datenbank und dann **Überprüfen + erstellen** aus, um die Details Ihrer Wiederherstellung anzuzeigen. Wählen Sie **Erstellen** aus, um die Datenbank aus der ausgewählten Sicherung wiederherzustellen.

   ![Konfigurieren von Wiederherstellungsdetails](./media/long-term-backup-retention-configure/restore-ltr.png)

1. Wählen Sie auf der Symbolleiste das Benachrichtigungssymbol aus, um den Status des Wiederherstellungsauftrags anzuzeigen.

   ![Status des Wiederherstellungsauftrags](./media/long-term-backup-retention-configure/restore-job-progress-long-term.png)

1. Öffnen Sie nach Abschluss des Wiederherstellungsauftrags die Seite **SQL-Datenbanken**, um die neu wiederhergestellte Datenbank anzuzeigen.

> [!NOTE]
> Auf diesem Blatt können Sie mithilfe von SQL Server Management Studio eine Verbindung mit der wiederhergestellten Datenbank herstellen, um erforderliche Aufgaben durchzuführen. Sie können beispielsweise [einen Teil der Daten aus der wiederhergestellten Datenbank extrahieren und in die vorhandene Datenbank kopieren oder die vorhandene Datenbank löschen und die wiederhergestellte Datenbank in den vorhandenen Datenbanknamen umbenennen](recovery-using-backups.md#point-in-time-restore).

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

### <a name="view-ltr-policies"></a>Anzeigen von LTR-Richtlinien

Führen Sie den Befehl [az sql db ltr-policy show](/cli/azure/sql/db/ltr-policy#az_sql_db_ltr_policy_show) aus, um die LTR-Richtlinie für eine einzelne Datenbank auf Ihrem Server anzuzeigen.

```azurecli
az sql db ltr-policy show \
    --resource-group mygroup \
    --server myserver \
    --name mydb
```

### <a name="view-ltr-backups"></a>Anzeigen von LTR-Sicherungen

Verwenden Sie den Befehl [az sql db ltr-backup list](/cli/azure/sql/db/ltr-backup#az_sql_db_ltr_backup_list), um die LTR-Sicherungen für eine Datenbank aufzulisten. Mit diesem Befehl können Sie den Parameter `name` zur Verwendung in anderen Befehlen suchen.

```azurecli
az sql db ltr-backup list \
   --location eastus2 \
   --server myserver \
   --database mydb
```

### <a name="delete-ltr-backups"></a>Löschen von LTR-Sicherungen

Führen Sie den Befehl [az sql db ltr-backup delete](/cli/azure/sql/db/ltr-backup#az_sql_db_ltr_backup_delete) aus, um eine LTR-Sicherung zu entfernen. Mit[az sql db ltr-backup list](/cli/azure/sql/db/ltr-backup#az_sql_db_ltr_backup_list) können Sie nach der Sicherung `name` suchen.

```azurecli
az sql db ltr-backup delete \
   --location eastus2 \
   --server myserver \
   --database mydb \
   --name "3214b3fb-fba9-43e7-96a3-09e35ffcb336;132292152080000000"
```

> [!IMPORTANT]
> Das Löschen der LTR-Sicherung kann nicht rückgängig gemacht werden. Wenn Sie eine LTR-Sicherung löschen möchten, nachdem der Server gelöscht wurde, müssen Sie über die Berechtigung im Bereich „Abonnement“ verfügen. Sie können Benachrichtigungen zu jedem Löschvorgang in Azure Monitor einrichten, indem Sie nach dem Vorgang „Löscht eine Sicherung zur langfristigen Aufbewahrung“ filtern. Das Aktivitätsprotokoll enthält Informationen dazu, wer die Anforderung vorgenommen hat und wann sie erfolgt ist. Ausführliche Anweisungen finden Sie unter [Erstellen von Aktivitätsprotokollwarnungen](../../azure-monitor/alerts/alerts-activity-log.md).

### <a name="restore-from-ltr-backups"></a>Wiederherstellen von LTR-Sicherungen

Führen Sie den Befehl [az sql db ltr-backup restore](/cli/azure/sql/db/ltr-backup#az_sql_db_ltr_backup_restore) aus, um Ihre Datenbank aus einer LTR-Sicherung wiederherzustellen. Sie können den Befehl [az sql db ltr-backup show](/cli/azure/sql/db/ltr-backup#az_sql_db_ltr_backup_show) ausführen, um die `backup-id` abzurufen.

1. Erstellen Sie mit dem Befehl „az sql db ltr-backup show“ eine Variable für die `backup-id` zur zukünftigen Verwendung.

   ```azurecli
   get_backup_id=$(az sql db ltr-backup show 
       --location eastus2 \
       --server myserver \
       --database mydb \
       --name "3214b3fb-fba9-43e7-96a3-09e35ffcb336;132292152080000000" \
       --query 'id' \
       --output tsv)
   ```

2. Stellen Sie Ihre Datenbank aus der LTR-Sicherung wieder her.

    ```azurecli
    az sql db ltr-backup restore \
       --dest-database targetdb \
       --dest-server myserver \
       --dest-resource-group mygroup \
       --backup-id $get_backup_id
    ```

> [!IMPORTANT]
> Sie müssen über Berechtigungen im Bereich des Abonnements des Servers verfügen, und dieses Abonnement muss aktiv sein, um die Wiederherstellung aus einer LTR-Sicherung nach dem Löschen des Servers oder der Ressourcengruppe durchführen zu können. Sie müssen außerdem den optionalen Parameter -ResourceGroupName auslassen.

> [!NOTE]
> Von hier aus können Sie mit SQL Server Management Studio eine Verbindung mit der wiederhergestellten Datenbank herstellen, um erforderliche Aufgaben durchzuführen, z. B. den Datenbankaustausch. Siehe [Point-in-Time-Wiederherstellung](recovery-using-backups.md#point-in-time-restore).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

### <a name="view-ltr-policies"></a>Anzeigen von LTR-Richtlinien

In diesem Beispiel wird gezeigt, wie Sie die LTR-Richtlinien auf einem Server auflisten.

```powershell
# get all LTR policies within a server
$ltrPolicies = Get-AzSqlDatabase -ResourceGroupName $resourceGroup -ServerName $serverName | `
    Get-AzSqlDatabaseLongTermRetentionPolicy

# get the LTR policy of a specific database
$ltrPolicies = Get-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup
```

### <a name="clear-an-ltr-policy"></a>Löschen einer LTR-Richtlinie

In diesem Beispiel wird gezeigt, wie Sie eine LTR-Richtlinie aus einer Datenbank löschen.

```powershell
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>Anzeigen von LTR-Sicherungen

Dieses Beispiel zeigt, wie Sie die LTR-Sicherungen auf einem Server auflisten.

```powershell
# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location

# get the list of LTR backups from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName

# get the LTR backups for a specific database from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -DatabaseName $dbName

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -DatabaseState Live

# only list the latest LTR backup for each database
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>Löschen von LTR-Sicherungen

Dieses Beispiel zeigt, wie Sie eine LTR-Sicherung aus der Liste der Sicherungen löschen.

```powershell
# remove the earliest backup
$ltrBackup = $ltrBackups[0]
Remove-AzSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> Das Löschen der LTR-Sicherung kann nicht rückgängig gemacht werden. Wenn Sie eine LTR-Sicherung löschen möchten, nachdem der Server gelöscht wurde, müssen Sie über die Berechtigung im Bereich „Abonnement“ verfügen. Sie können Benachrichtigungen zu jedem Löschvorgang in Azure Monitor einrichten, indem Sie nach dem Vorgang „Löscht eine Sicherung zur langfristigen Aufbewahrung“ filtern. Das Aktivitätsprotokoll enthält Informationen dazu, wer die Anforderung vorgenommen hat und wann sie erfolgt ist. Ausführliche Anweisungen finden Sie unter [Erstellen von Aktivitätsprotokollwarnungen](../../azure-monitor/alerts/alerts-activity-log.md).

### <a name="restore-from-ltr-backups"></a>Wiederherstellen von LTR-Sicherungen

Dieses Beispiel zeigt, wie Sie eine LTR-Sicherung wiederherstellen. Beachten Sie, dass sich diese Schnittstelle nicht geändert hat, der Parameter für die Ressourcen-ID jetzt jedoch die Ressourcen-ID der LTR-Sicherung erfordert.

```powershell
# restore a specific LTR backup as an P1 database on the server $serverName of the resource group $resourceGroup
Restore-AzSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup `
    -TargetDatabaseName $dbName -ServiceObjectiveName P1
```

> [!IMPORTANT]
> Sie müssen über Berechtigungen im Bereich des Abonnements des Servers verfügen, und dieses Abonnement muss aktiv sein, um die Wiederherstellung aus einer LTR-Sicherung nach dem Löschen des Servers oder der Ressourcengruppe durchführen zu können. Sie müssen außerdem den optionalen Parameter -ResourceGroupName auslassen.

> [!NOTE]
> Auf diesem Blatt können Sie mithilfe von SQL Server Management Studio eine Verbindung mit der wiederhergestellten Datenbank herstellen, um erforderliche Aufgaben durchzuführen. Sie können beispielsweise einen Teil der Daten aus der wiederhergestellten Datenbank extrahieren und in die vorhandene Datenbank kopieren oder die vorhandene Datenbank löschen und die wiederhergestellte Datenbank in den vorhandenen Datenbanknamen umbenennen. Siehe [Point-in-Time-Wiederherstellung](recovery-using-backups.md#point-in-time-restore).

---

## <a name="limitations"></a>Einschränkungen
- Bei der Wiederherstellung mithilfe einer LTR-Sicherung wird die Eigenschaft für die Leseskalierung deaktiviert. Aktualisieren Sie die Datenbank nach ihrer Erstellung, um die Leseskalierung für die wiederhergestellte Datenbank zu aktivieren.
- Beim Wiederherstellen aus einer LTR-Sicherung, die erstellt wurde, als die Datenbank zu einem Pool für elastische Datenbanken gehörte, müssen Sie das Servicelevelziel angeben. 

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu vom Dienst generierten automatischen Sicherungen finden Sie im Artikel zu [automatischen Sicherungen](automated-backups-overview.md).
- Weitere Informationen zur langfristigen Beibehaltung von Sicherungen finden Sie im Artikel zur [langfristigen Beibehaltung von Sicherungen](long-term-retention-overview.md).
