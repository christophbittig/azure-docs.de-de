---
title: Verwalten von Lesereplikaten – Azure PowerShell – Azure Database for PostgreSQL
description: Erfahren Sie, wie Sie mit PowerShell Lesereplikate in Azure Database for PostgreSQL einrichten und verwalten.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c260f4ba164bfce60bde939bd8dbde10a6659326
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132137861"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-postgresql-using-powershell"></a>Informationen zum Erstellen und Verwalten von Lesereplikaten in Azure Database for PostgreSQL mithilfe von PowerShell

In diesem Artikel erfahren Sie, wie Sie Lesereplikate im Azure Database for PostgreSQL-Dienst über PowerShell erstellen und verwalten. Weitere Informationen zu Lesereplikaten finden Sie in der [Übersicht](concepts-read-replicas.md).

## <a name="azure-powershell"></a>Azure PowerShell

Sie können Lesereplikate mithilfe von PowerShell erstellen und verwalten.

## <a name="prerequisites"></a>Voraussetzungen

Zum Durcharbeiten dieses Leitfadens benötigen Sie Folgendes:

- Lokale Installation des [Moduls „Az PowerShell“](/powershell/azure/install-az-ps) oder von [Azure Cloud Shell](https://shell.azure.com/) im Browser
- Einen [Azure-Datenbank für PostgreSQL-Server](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Solange nur eine Vorschauversion des PowerShell-Moduls „Az.PostgreSql“ verfügbar ist, müssen Sie es separat über das Az PowerShell-Modul installieren. Verwenden Sie dazu den folgenden Befehl: `Install-Module -Name Az.PostgreSql -AllowPrerelease`.
> Sobald das PowerShell-Modul „Az.PostgreSql“ allgemein verfügbar ist, wird es in die zukünftigen Releases des Az PowerShell-Moduls integriert und in Azure Cloud Shell nativ zur Verfügung gestellt.

Wenn Sie PowerShell lieber lokal verwenden möchten, stellen Sie mithilfe des Cmdlets [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) eine Verbindung mit Ihrem Azure-Konto her.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Das Feature für Lesereplikate ist nur für Azure Database for PostgreSQL-Server in den Tarifen „Universell“ oder „Arbeitsspeicheroptimiert“ verfügbar. Stellen Sie sicher, dass für den primären Server einer der folgenden Tarife festgelegt ist.

### <a name="create-a-read-replica"></a>Erstellen eines Lesereplikats

Ein Lesereplikatserver kann mit dem folgenden Befehl erstellt werden:

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzPostgreSqlReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

Für den Befehl `New-AzPostgreSqlReplica` sind folgende Parameter erforderlich:

| Einstellung | Beispielwert | BESCHREIBUNG  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Die Ressourcengruppe, in der der Replikatserver erstellt wird.  |
| Name | mydemoreplicaserver | Der Name des neuen Replikatservers, der erstellt wird. |

Verwenden Sie den **Location**-Parameter, um ein regionsübergreifendes Lesereplikat zu erstellen. Im folgenden Beispiel wird ein Replikat in der Region **USA, Westen** erstellt.

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzPostgreSqlReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

Weitere Informationen zu den Regionen, in denen Sie ein Replikat erstellen können, finden Sie im [Konzeptartikel zu Lesereplikaten](concepts-read-replicas.md).

Standardmäßig werden Lesereplikate mit derselben Serverkonfiguration wie der primäre Server erstellt, es sei denn, der Parameter **Sku** wird angegeben.

> [!NOTE]
> Für die Konfiguration des Replikatservers sollten mindestens die gleichen Werte verwendet werden wie für den primären Server, damit das Replikat über genügend Kapazität verfügt.

### <a name="list-replicas-for-a-primary-server"></a>Auflisten von Replikaten für einen primären Server

Führen Sie den folgenden Befehl aus, um alle Replikate für einen bestimmten primären Server anzuzeigen:

```azurepowershell-interactive
Get-AzPostgreSQLReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

Für den Befehl `Get-AzPostgreSQLReplica` sind folgende Parameter erforderlich:

| Einstellung | Beispielwert | BESCHREIBUNG  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Die Ressourcengruppe, in der der Replikatserver erstellt wird.  |
| ServerName | mydemoserver | Der Name oder die ID des primären Servers. |

### <a name="stop-a-replica-server"></a>Beenden eines Replikatservers

Durch das Beenden eines Lesereplikatservers wird das Lesereplikat auf einen unabhängigen Server heraufgestuft. Dazu können Sie das `Update-AzPostgreSqlServer`-Cmdlet ausführen und den ReplicationRole-Wert auf `None` festlegen.

```azurepowershell-interactive
Update-AzPostgreSqlServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -ReplicationRole None
```

### <a name="delete-a-replica-server"></a>Löschen eines Replikatservers

Zum Löschen eines Lesereplikatservers können Sie das Cmdlet `Remove-AzPostgreSqlServer` ausführen.

```azurepowershell-interactive
Remove-AzPostgreSqlServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-primary-server"></a>Löschen eines primären Servers

> [!IMPORTANT]
> Wenn Sie einen primären Server löschen, wird die Replikation auf allen Replikatservern beendet, und der primäre Server selbst wird gelöscht. Replikatserver werden zu eigenständigen Servern, die nun Lese- und Schreibvorgänge unterstützen.

Zum Löschen eines primären Servers können Sie das Cmdlet `Remove-AzPostgreSqlServer` ausführen.

```azurepowershell-interactive
Remove-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Neustarten eines Azure Database for PostgreSQL-Servers mit PowerShell](howto-restart-server-powershell.md)
