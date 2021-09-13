---
title: Residenz in einer einzelnen Region
description: Schrittanleitung zum Konfigurieren der Residenz in einer einzelnen Region für einen dedizierten SQL-Pool (früher SQL Data Warehouse) in Azure Synapse Analytics
services: synapse-analytics
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/15/2021
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a9fd0860fba93f2aa45616707c791aef498fb06e
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2021
ms.locfileid: "110580699"
---
# <a name="configure-single-region-residency-for-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Konfigurieren der Residenz in einer einzelnen Region für einen dedizierten SQL-Pool (früher SQL Data Warehouse) in Azure Synapse Analytics

In diesem Artikel erfahren Sie, wie Sie Ihre Datenbank in lokal redundantem Speicher bereitstellen, um sicherzustellen, dass Ihre Daten innerhalb Ihrer geografischen Grenzen bleiben. Dieser Artikel gilt nur für dedizierte SQL-Pools, die sich in einer Region befinden, in der sich das Azure-Regionspaar außerhalb des Landes befindet. Wenn Sie die Schritte in diesem Artikel ausführen, werden die meisten Daten und alle Sicherungen nicht in ein [Regionspaar](../../best-practices-availability-paired-regions.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) repliziert. Beachten Sie, dass einige Metadaten wie der Name des logischen Servers, der Name der logischen Datenbank und der Name der Ressourcengruppe weiterhin standardmäßig in ein Regionspaar repliziert werden und dies nicht geändert werden kann. 

Um die Residenz in einer einzelnen Region zu erreichen, müssen Sie Ihren dedizierten SQL-Pool (vormals SQL DW) für lokal redundanten Speicher bereitstellen, indem Sie bei der Bereitstellung Ihres SQL-Pools die Option „Nein“ für Georedundanz auswählen. Wenn Sie Ihren SQL-Pool bereits bereitgestellt haben und er sich in georedundantem Speicher befindet, können Sie Ihren SQL-Pool wiederherstellen und während des Wiederherstellungsvorgangs die Option „Nein“ für Georedundanz auswählen. Anweisungen für beide Szenarien finden Sie weiter unten. 

## <a name="provision-a-dedicated-sql-pool-formerly-sql-dw-on-locally-redundant-storage-through-azure-portal"></a>Bereitstellen eines dedizierten SQL-Pools (früher SQL DW) in lokal redundantem Speicher über das Azure-Portal

Führen Sie die folgenden Schritte aus, um einen dedizierten SQL-Pool (vormals SQL DW) in lokal redundantem Speicher bereitstellen:

1. Melden Sie sich bei Ihrem [Azure-Portal](https://portal.azure.com/)-Konto an.
1. Suchen Sie nach **Dedizierte SQL-Pools (früher SQL Data Warehouse)** .

   ![Neues DW 2](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)

1. Klicken Sie auf „Hinzufügen“, und geben Sie auf der Registerkarte* **Grundlagen** die angeforderten Informationen ein.

1. Wenn sich Ihr Server in einer Region (z. B. Asien, Südosten) befindet, in der sich das Regionspaar in einer anderen Geografie befindet, haben Sie die Möglichkeit, die Georedundanz zu deaktivieren. 

   ![Datenresidenz](./media/sql-data-warehouse-single-region-residency/data-residency-1.png)

1. Wählen Sie **Weiter: Netzwerk** aus, um einen öffentlichen oder privaten Endpunkt zu konfigurieren. 

1. Wählen Sie **Weiter: Zusätzliche Einstellungen** aus, um eine Sicherung wiederherzustellen, ein Beispiel zu erstellen oder eine leere Datenbank zu erstellen.  

1. Wählen Sie **Weiter: Tags** aus, um Tags zum Kategorisieren Ihres SQL-Pools zu konfigurieren. 

1. Klicken Sie auf **Weiter: Überprüfen + erstellen**. Vergewissern Sie sich, dass die richtige Speicherredundanz gewählt wurde, um die Datenverfügbarkeit zu gewährleisten. 

    ![Erstellen eines Pools in einer einzelnen Region](./media/sql-data-warehouse-single-region-residency/data-residency-2.png)

1. Klicken Sie auf **Erstellen**.  

## <a name="provision-a-dedicated-sql-pool-formerly-sql-dw-on-locally-redundant-storage-through-powershell"></a>Bereitstellen eines dedizierten SQL-Pools (früher SQL DW) in lokal redundantem Speicher über PowerShell
Verwenden Sie das PowerShell-Cmdlet [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json), um einen neuen dedizierten SQL-Pool über PowerShell zu erstellen.

1. Bevor Sie beginnen, müssen Sie [Azure PowerShell installieren](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Öffnen Sie PowerShell.
3. Stellen Sie eine Verbindung mit Ihrem Azure-Konto her, und listen Sie alle Abonnements auf, die Ihrem Konto zugeordnet sind.
4. Wählen Sie das Abonnement aus, in dem die wiederherzustellende Datenbank enthalten ist.
5. Erstellen Sie eine neue Datenbank in lokal redundantem Speicher.
1. Stellen Sie sicher, dass die Datenbank erfolgreich in lokal redundantem Speicher erstellt wurde.  

```powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$Label = "<YourRestorePointLabel>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName
New-AzSqlDatabase -ResourceGroupName $ResourceGroupName -Edition 'DataWarehouse' -ServerName $ServerName -DatabaseName $DatabaseName -BackupStorageRedundancy 'Local'
Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -Edition 'DataWarehouse' -ServerName $ServerName -DatabaseName $DatabaseName 
```

## <a name="restore-a-dedicated-sql-pool-formerly-sql-dw-on-locally-redundant-storage-through-azure-portal"></a>Wiederherstellen eines dedizierten SQL-Pools (früher SQL DW) in lokal redundantem Speicher über das Azure-Portal
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Navigieren Sie zum dedizierten SQL-Pool, mit dem Sie die Wiederherstellung durchführen möchten.

1. Wählen Sie oben im Blatt „Übersicht“ die Option **Wiederherstellen** aus.

1. Wählen Sie den Wiederherstellungspunkt aus, von dem Sie wiederherstellen möchten. 

1. Wählen Sie für **Georedundanz** die Option „Nein“ aus. 

   ![Wiederherstellen über das Portal](./media/sql-data-warehouse-single-region-residency/data-residency-3.png)

## <a name="restore-a-dedicated-sql-pool-formerly-sql-dw-on-locally-redundant-storage-through-powershell"></a>Wiederherstellen eines dedizierten SQL-Pools (früher SQL DW) in lokal redundantem Speicher über PowerShell
Verwenden Sie das PowerShell-Cmdlet [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json), um einen neuen dedizierten SQL-Pool über PowerShell zu erstellen.

1. Bevor Sie beginnen, müssen Sie [Azure PowerShell installieren](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Öffnen Sie PowerShell.
3. Stellen Sie eine Verbindung mit Ihrem Azure-Konto her, und listen Sie alle Abonnements auf, die Ihrem Konto zugeordnet sind.
4. Wählen Sie das Abonnement aus, in dem die wiederherzustellende Datenbank enthalten ist.
5. Listen Sie die Wiederherstellungspunkte für den dedizierten SQL-Pool (früher SQL DW) auf.
1. Wählen Sie den gewünschten Wiederherstellungspunkt mit RestorePointCreationDate aus.
1. Stellen Sie den dedizierten SQL-Pool (früher SQL DW) anhand des gewünschten Wiederherstellungspunkts mit dem PowerShell-Cmdlet „Restore-AzSqlDatabase“ wieder her, und geben Sie dabei „Lokal“ für „BackupStorageRedundnacy“ an. 

```powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Or list all restore points
Get-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate "xx/xx/xxxx xx:xx:xx xx"
$PointInTime="<RestorePointCreationDate>"

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID -BackupStorageRedundancy 'Local'

# Verify the status of restored database
$RestoredDatabase.status
```


## <a name="next-steps"></a>Nächste Schritte

- [Wiederherstellen eines vorhandenen dedizierten SQL-Pools (früher SQL DW)](sql-data-warehouse-restore-active-paused-dw.md)
- [Wiederherstellen eines gelöschten dedizierten SQL-Pools (früher SQL Data Warehouse)](sql-data-warehouse-restore-deleted-dw.md)
