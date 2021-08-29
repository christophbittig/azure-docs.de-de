---
title: 'Mit PowerShell: Wiederherstellen einer automatischen Sicherung einer Datenbank in Azure SQL-Datenbank'
description: Verwenden Sie ein Azure PowerShell-Beispielskript zum Wiederherstellen einer Datenbank in Azure SQL-Datenbank in einem früheren Zustand auf der Grundlage automatischer Sicherungen.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: devx-track-azurepowershell
ms.devlang: PowerShell
ms.topic: sample
author: SQLSourabh
ms.author: sourabha
ms.reviewer: mathoma
ms.date: 03/27/2019
ms.openlocfilehash: 0d6b8352aa0606101fc171f639c5887aa0879c6d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751288"
---
# <a name="use-powershell-to-restore-a-database-to-an-earlier-point-in-time"></a>Verwenden von PowerShell zum Wiederherstellen einer Datenbank in einem früheren Zustand

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Dieses PowerShell-Skriptbeispiel stellt eine bestimmten früheren Zustand einer Datenbank in Azure SQL-Datenbank wieder her.  

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Wenn Sie PowerShell lokal installieren und nutzen möchten, müssen Sie für dieses Tutorial mindestens die Version 1.4.0 von Azure PowerShell verwenden. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

## <a name="sample-script"></a>Beispielskript

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/restore-database/restore-database.ps1?highlight=17-18 "Create SQL Database")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Verwenden Sie den folgenden Befehl, um die Ressourcengruppe und alle dazugehörigen Ressourcen zu entfernen:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Erstellt einen Server, der Datenbanken und Pools für elastische Datenbanken hostet. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Erstellt eine Datenbank auf einem Server. |
| [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) | Ruft eine georedundante Sicherung einer eigenständigen Datenbank oder Pooldatenbank ab. |
| [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) | Stellt eine Datenbank wieder her. |
| [Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase) | Entfernt eine Datenbank. |
| [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Ruft eine gelöschte Datenbank ab, die Sie wiederherstellen können. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure PowerShell finden Sie in der [Azure PowerShell-Dokumentation](/powershell/azure/).

Zusätzliche PowerShell-Skriptbeispiele für SQL-Datenbank finden Sie in den [Azure PowerShell samples for Azure SQL Database](../powershell-script-content-guide.md) (Azure PowerShell-Beispiele für Azure SQL-Datenbank).
