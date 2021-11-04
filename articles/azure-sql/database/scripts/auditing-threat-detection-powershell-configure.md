---
title: 'PowerShell-Beispiel: Überwachung und Advanced Threat Protection – Azure SQL-Datenbank'
description: Azure PowerShell-Beispielskript zum Konfigurieren von Überwachung und Advanced Threat Protection in Azure SQL-Datenbank
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: security, sqldbrb=1, devx-track-azurepowershell
ms.devlang: PowerShell
ms.topic: sample
author: DavidTrigano
ms.author: datrigan
ms.reviewer: mathoma, vanto
ms.date: 04/28/2020
ms.openlocfilehash: cee0c010101c132cb8c4f105ccfd46a5cb8afcf6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "131465305"
---
# <a name="use-powershell-to-configure-sql-database-auditing-and-advanced-threat-protection"></a>Konfigurieren von SQL-Datenbank-Überwachung und Advanced Threat Protection mithilfe von PowerShell
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Mit diesem PowerShell-Skriptbeispiel konfigurieren Sie die Azure SQL-Datenbank-Überwachung und Advanced Threat Protection.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Wenn Sie PowerShell lokal installieren und nutzen möchten, müssen Sie für dieses Tutorial mindestens die Version 1.4.0 von Azure PowerShell verwenden. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

## <a name="sample-script"></a>Beispielskript

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/database-auditing-and-threat-detection/database-auditing-and-threat-detection.ps1?highlight=15-16 "Configure auditing and threat detection")]

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
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Erstellt einen Server. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Erstellt eine Datenbank oder einen Pool für elastische Datenbanken. |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | Erstellt ein Speicherkonto. |
| [Set-AzSqlDatabaseAuditing](/powershell/module/az.sql/set-azsqldatabaseaudit) | Legt die Überwachungsrichtlinie für eine Datenbank fest. |
| Set-AzSqlDatabaseThreatDetectionPolicy | Legt eine Advanced Threat Protection-Richtlinie für eine Datenbank fest. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |
|||

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure PowerShell finden Sie in der [Azure PowerShell-Dokumentation](/powershell/azure/).

Zusätzliche PowerShell-Skriptbeispiele für SQL-Datenbank finden Sie in den [Azure PowerShell samples for Azure SQL Database](../powershell-script-content-guide.md) (Azure PowerShell-Beispiele für Azure SQL-Datenbank).
