---
title: 'Schnellstart: Anhalten und Fortsetzen von Computeressourcen im dedizierten SQL-Pool (vormals SQL DW) über Azure PowerShell'
description: Mit Azure PowerShell können Sie den dedizierten SQL-Pool (vormals SQL DW) anhalten und fortsetzen. .
services: synapse-analytics
author: julieMSFT
ms.author: jrasnick
manager: craigg
ms.reviewer: igorstan
ms.date: 03/20/2019
ms.topic: quickstart
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom: devx-track-azurepowershell, seo-lt-2019, azure-synapse, mode-api
ms.openlocfilehash: 5d0aab60ffc11fbc2ef5fe32055403b15f679b30
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131003502"
---
# <a name="quickstart-pause-and-resume-compute-in-dedicated-sql-pool-formerly-sql-dw-with-azure-powershell"></a>Schnellstart: Anhalten und Fortsetzen von Computeressourcen im dedizierten SQL-Pool (vormals SQL DW) über Azure PowerShell

Mit Azure PowerShell können Sie Computeressourcen des dedizierten SQL-Pools (vormals SQL DW) anhalten und fortsetzen.
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="before-you-begin"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

In dieser Schnellstartanleitung wird vorausgesetzt, dass Sie bereits über einen dedizierten SQL-Pool (vormals SQL DW) verfügen, den Sie anhalten und fortsetzen können. Verwenden Sie die Anleitung unter [Erstellen und Verbinden – Portal](create-data-warehouse-portal.md), um bei Bedarf einen dedizierten SQL-Pool (vormals SQL DW) namens **mySampleDataWarehouse** zu erstellen.

## <a name="log-in-to-azure"></a>Anmelden an Azure

Melden Sie sich mit dem Befehl [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) bei Ihrem Azure-Abonnement an, und befolgen Sie die Anweisungen auf dem Bildschirm.

```powershell
Connect-AzAccount
```

Verwenden Sie [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json), um zu ermitteln, welches Abonnement Sie verwenden.

```powershell
Get-AzSubscription
```

Falls Sie ein anderes Abonnement als das Standardabonnement verwenden müssen, führen Sie [Set-AzContext](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) aus.

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-dedicated-sql-pool-formerly-sql-dw-information"></a>Suchen der Informationen eines dedizierten SQL-Pools (vormals SQL DW)

Suchen Sie nach dem Datenbanknamen, dem Servernamen und der Ressourcengruppe für den dedizierten SQL-Pool (vormals SQL DW), den Sie anhalten und fortsetzen möchten.

Befolgen Sie diese Schritte, um Standortinformationen für Ihren dedizierten SQL-Pool (vormals SQL DW) zu ermitteln:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Klicken Sie auf der linken Seite des Azure-Portals auf **Azure Synapse Analytics (vormals SQL DW)** .
1. Wählen Sie auf der Seite **Azure Synapse Analytics (vormals SQL DW)** die Option **mySampleDataWarehouse** aus. Der SQL-Pool wird geöffnet.

    ![Servername und Ressourcengruppe](./media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

1. Notieren Sie sich den Namen des dedizierten SQL-Pools (vormals SQL DW). Dabei handelt es sich um den Datenbanknamen. Notieren Sie außerdem den Servernamen und die Ressourcengruppe.
1. Verwenden Sie in den PowerShell-Cmdlets nur den ersten Teil des Servernamens. In der obigen Abbildung lautet der vollständige Servername „sqlpoolservername.database.windows.net“. Im PowerShell-Cmdlet wird **sqlpoolservername** als Servername verwendet.

## <a name="pause-compute"></a>Anhalten von Computeressourcen

Um Kosten zu sparen, können Sie Computeressourcen bei Bedarf anhalten und fortsetzen. Wenn Sie die Datenbank z.B. nachts oder am Wochenende nicht verwenden, können Sie sie während dieser Zeiträume anhalten und während des Tages wieder fortsetzen.

> [!NOTE]
> Für Computeressourcen fallen keine Gebühren an, während die Datenbank angehalten ist. Allerdings wird Ihnen der Speicher weiterhin in Rechnung gestellt.

Wenn Sie eine Datenbank anhalten möchten, verwenden Sie das Cmdlet [Suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Im folgenden Beispiel wird ein SQL-Pool namens **mySampleDataWarehouse** angehalten, der auf einem Server mit dem Namen **sqlpoolservername** gehostet wird. Der Server befindet sich in einer Azure-Ressourcengruppe namens **myResourceGroup**.

```powershell
Suspend-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
```

Das folgende Beispiel ruft die Datenbank in das $database-Objekt ab. Das Objekt wird dann an [Suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) weitergereicht. Die Ergebnisse werden in dem Objekt „resultDatabase“ gespeichert. Der letzte Befehl zeigt die Ergebnisse an.

```powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="resume-compute"></a>Fortsetzen von Computeressourcen

Verwenden Sie das Cmdlet [Resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json), um eine Datenbank zu starten. Im folgenden Beispiel wird eine Datenbank namens **mySampleDataWarehouse** gestartet, das auf einem Server mit dem Namen **sqlpoolservername** gehostet wird. Der Server befindet sich in einer Azure-Ressourcengruppe namens **myResourceGroup**.

```powershell
Resume-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

Das nächste Beispiel ruft die Datenbank in das $database-Objekt ab. Anschließend wird das Objekt an [Resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) weitergereicht, und die Ergebnisse werden in „$resultDatabase“ gespeichert. Der letzte Befehl zeigt die Ergebnisse an.

```powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Resume-AzSqlDatabase
$resultDatabase
```

## <a name="check-status-of-your-sql-pool-operation"></a>Überprüfen des Status eines Vorgangs für Ihren SQL-Pool

Verwenden Sie zum Überprüfen des Status Ihres dedizierten SQL-Pools (vormals SQL DW) das Cmdlet [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/Get-AzSqlDatabaseActivity?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```powershell
Get-AzSqlDatabaseActivity -ResourceGroupName "myResourceGroup" -ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Ihnen werden Gebühren für Data Warehouse-Einheiten und die in Ihrem dedizierten SQL-Pool (vormals SQL DW) gespeicherten Daten in Rechnung gestellt. Diese Compute- und Speicherressourcen werden separat in Rechnung gestellt.

- Wenn Sie die Daten im Speicher beibehalten möchten, halten Sie die Computeressourcen an.
- Wenn künftig keine Gebühren mehr anfallen sollen, können Sie den SQL-Pool löschen.

Führen Sie die folgenden Schritte aus, um Ressourcen nach Wunsch zu bereinigen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und klicken Sie auf Ihren SQL-Pool.

    ![Bereinigen von Ressourcen](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Zum Anhalten von Computeressourcen klicken Sie auf die Schaltfläche **Anhalten**. Wenn der SQL-Pool angehalten ist, wird die Schaltfläche **Starten** angezeigt.  Klicken Sie zum Fortsetzen der Computeressourcen auf **Starten**.

3. Wenn Sie den SQL-Pool entfernen möchten, damit keine Gebühren für Compute- oder Speicherressourcen anfallen, klicken Sie auf **Löschen**.

4. Wenn Sie die von Ihnen erstellte SQL Server-Instanz entfernen möchten, klicken Sie auf **sqlpoolservername.database.windows.net** und anschließend auf **Löschen**.  Seien Sie bei diesem Löschvorgang vorsichtig, da beim Löschen des Servers auch alle Datenbanken gelöscht werden, die dem Server zugewiesen sind.

5. Zum Entfernen der Ressourcengruppe klicken Sie auf **myResourceGroup**, und klicken Sie dann auf **Ressourcengruppe löschen**.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum SQL-Pool finden Sie im Artikel [Tutorial: Laden des Datasets „New York Taxis“](./load-data-from-azure-blob-storage-using-copy.md). Weitere Informationen zum Verwalten von Computefunktionen finden Sie im Artikel [Verwalten von Computeressourcen im Azure Synapse Analytics-Data Warehouse](sql-data-warehouse-manage-compute-overview.md).
