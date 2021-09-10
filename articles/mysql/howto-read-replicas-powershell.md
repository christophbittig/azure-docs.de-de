---
title: Verwalten von Lesereplikaten – Azure PowerShell – Azure Database for MySQL
description: Erfahren Sie, wie Sie mit PowerShell Lesereplikate in Azure Database for MySQL einrichten und verwalten.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 06/17/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f4980692be64c2a8b3918d2dbaab5ef9c983f453
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122345768"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-powershell"></a>Informationen zum Erstellen und Verwalten von Lesereplikaten in Azure Database for MySQL mithilfe von PowerShell

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

In diesem Artikel erfahren Sie, wie Sie Lesereplikate im Azure Database for MySQL-Dienst über PowerShell erstellen und verwalten. Weitere Informationen zu Lesereplikaten finden Sie in der [Übersicht](concepts-read-replicas.md).

## <a name="azure-powershell"></a>Azure PowerShell

Sie können Lesereplikate mithilfe von PowerShell erstellen und verwalten.

## <a name="prerequisites"></a>Voraussetzungen

Zum Durcharbeiten dieses Leitfadens benötigen Sie Folgendes:

- Lokale Installation des [Moduls „Az PowerShell“](/powershell/azure/install-az-ps) oder von [Azure Cloud Shell](https://shell.azure.com/) im Browser
- Ein [Azure Database for MySQL-Server](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Solange nur eine Vorschauversion des PowerShell-Moduls „Az.MySql“ verfügbar ist, müssen Sie es separat über das AZ PowerShell-Modul installieren. Verwenden Sie dazu den folgenden Befehl: `Install-Module -Name Az.MySql -AllowPrerelease`.
> Sobald das PowerShell-Modul „Az.MySql“ allgemein verfügbar ist, wird es in die zukünftigen Releases des Az PowerShell-Moduls integriert und in Azure Cloud Shell nativ zur Verfügung gestellt.

Wenn Sie PowerShell lieber lokal verwenden möchten, stellen Sie mithilfe des Cmdlets [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) eine Verbindung mit Ihrem Azure-Konto her.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Das Feature für Lesereplikate ist nur für Azure Database for MySQL-Server in den Tarifen „Universell“ oder „Arbeitsspeicheroptimiert“ verfügbar. Stellen Sie sicher, dass für den Quellserver einer der folgenden Tarife festgelegt ist.
>
>Wenn GTID auf einem primären Server aktiviert ist (`gtid_mode` = ON), wird für neu erstellte Replikate GTID ebenfalls aktiviert, und es wird die GTID-Replikation verwendet. Weitere Informationen finden Sie unter [Globaler Transaktionsbezeichner (GTID)](concepts-read-replicas.md#global-transaction-identifier-gtid).

### <a name="create-a-read-replica"></a>Erstellen eines Lesereplikats

> [!IMPORTANT]
> Wenn Sie ein Replikat für eine Quelle erstellen, die keine vorhandenen Replikate hat, startet die Quelle zunächst neu, um sich auf die Replikation vorzubereiten. Beachten Sie dies, und führen Sie diese Vorgänge nicht zu Spitzenzeiten durch.

Ein Lesereplikatserver kann mit dem folgenden Befehl erstellt werden:

```azurepowershell-interactive
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzMySqlReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

Für den Befehl `New-AzMySqlReplica` sind folgende Parameter erforderlich:

| Einstellung | Beispielwert | BESCHREIBUNG  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Die Ressourcengruppe, in der der Replikatserver erstellt wird.  |
| Name | mydemoreplicaserver | Der Name des neuen Replikatservers, der erstellt wird. |

Verwenden Sie den **Location**-Parameter, um ein regionsübergreifendes Lesereplikat zu erstellen. Im folgenden Beispiel wird ein Replikat in der Region **USA, Westen** erstellt.

```azurepowershell-interactive
Get-AzMySqlServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzMySqlReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

> [!NOTE]
> Weitere Informationen zu den Regionen, in denen Sie ein Replikat erstellen können, finden Sie im [Konzeptartikel zu Lesereplikaten](concepts-read-replicas.md). 

Standardmäßig werden Lesereplikate mit derselben Serverkonfiguration wie der Quellserver erstellt, es sei denn, der Parameter **Sku** wird angegeben.

> [!NOTE]
> Für die Konfiguration des Replikatservers sollten mindestens die gleichen Werte verwendet werden wie für den Quellserver, damit das Replikat über genügend Kapazität verfügt.

### <a name="list-replicas-for-a-source-server"></a>Auflisten von Replikaten für einen Quellserver

Führen Sie den folgenden Befehl aus, um alle Replikate für einen bestimmten Quellserver anzuzeigen:

```azurepowershell-interactive
Get-AzMySqlReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

Für den Befehl `Get-AzMySqlReplica` sind folgende Parameter erforderlich:

| Einstellung | Beispielwert | BESCHREIBUNG  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Die Ressourcengruppe, in der der Replikatserver erstellt wird.  |
| ServerName | mydemoserver | Der Name oder die ID des Quellservers. |

### <a name="delete-a-replica-server"></a>Löschen eines Replikatservers

Zum Löschen eines Lesereplikatservers können Sie das Cmdlet `Remove-AzMySqlServer` ausführen.

```azurepowershell-interactive
Remove-AzMySqlServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-source-server"></a>Löschen eines Quellservers

> [!IMPORTANT]
> Wenn Sie einen Quellserver löschen, wird die Replikation auf allen Replikatservern beendet und der Quellserver selbst gelöscht. Replikatserver werden zu eigenständigen Servern, die nun Lese- und Schreibvorgänge unterstützen.

Zum Löschen eines Quellservers können Sie das Cmdlet `Remove-AzMySqlServer` ausführen.

```azurepowershell-interactive
Remove-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

### <a name="known-issue"></a>Bekanntes Problem

Es gibt zwei Generationen von Speicher, die von Servern im Tarif „Universell“ und „Arbeitsspeicheroptimiert“ verwendet werden: „Universell V1“ (Unterstützung von bis zu 4 TB) und „Universell V2“ (Unterstützung von bis zu 16 TB Speicher).
Quellserver und Replikatserver sollten denselben Speichertyp verwenden. Der Speichertyp [Universell V2](./concepts-pricing-tiers.md#general-purpose-storage-v2-supports-up-to-16-tb-storage) ist nicht in allen Regionen verfügbar. Deshalb müssen Sie sicherstellen, dass Sie die richtige Replikatregion auswählen, wenn Sie den Standort für die Erstellung von Lesereplikaten mit PowerShell festlegen. Informationen zur Ermittlung des Speichertyps Ihres Quellservers finden Sie unter [Wie kann ich bestimmen, mit welchem Speichertyp mein Server ausgeführt wird?](./concepts-pricing-tiers.md#how-can-i-determine-which-storage-type-my-server-is-running-on). 

Wenn Sie eine Region auswählen, in der Sie kein Lesereplikat für Ihren Quellserver erstellen können, tritt das in der folgenden Abbildung gezeigte Problem auf. Die Bereitstellung wird weiter ausgeführt wird, bis es zu einem Timeout mit dem Fehler *„Der Ressourcenbereitstellungsvorgang wurde nicht innerhalb des zulässigen Zeitlimits abgeschlossen“* kommt.

[ :::image type="content" source="media/howto-read-replicas-powershell/replcia-ps-known-issue.png" alt-text="CLI-Fehler zum Lesereplikat":::](media/howto-read-replicas-powershell/replcia-ps-known-issue.png#lightbox)


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Neustarten eines Azure Database for MySQL-Servers mithilfe von Azure PowerShell](howto-restart-server-powershell.md)
