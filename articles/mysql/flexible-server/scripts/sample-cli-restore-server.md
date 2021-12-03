---
title: 'CLI-Skript: Wiederherstellen einer Instanz von Azure Database for MySQL – Flexible Server'
description: Dieses Azure CLI-Beispielskript veranschaulicht das Wiederherstellen eines früheren Zustands einer einzelnen Instanz von Azure Database for MySQL – Flexible Server.
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: f109da4924f04cd97fbe9cd816c2da45ce5c0e2d
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131844149"
---
# <a name="restore-an-azure-database-for-mysql---flexible-server-using-azure-cli"></a>Wiederherstellen einer Instanz von Azure Database for MySQL – Flexible Server mit der Azure CLI

Azure Database for MySQL – Flexible Server erstellt automatisch Serversicherungen und speichert diese sicher in lokalen redundanten Speichern innerhalb der Region.

Dieses CLI-Beispielskript führt eine [Zeitpunktwiederherstellung](../concepts-backup-restore.md) durch und erstellt einen neuen Server aus den Sicherungen Ihrer Flexible Server-Instanz. 

Die neue Flexible Server-Instanz wird mit der Konfiguration des ursprünglichen Servers erstellt und erbt außerdem Tags und Einstellungen wie ein virtuelles Netzwerk und eine Firewall vom Quellserver. Die Compute- und Speicherebene, die Konfiguration und die Sicherheitseinstellungen des wiederhergestellten Servers können nach Abschluss der Wiederherstellung geändert werden.

[!INCLUDE [flexible-server-free-trial-note](../../includes/flexible-server-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment](../../../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert. 

## <a name="sample-script"></a>Beispielskript

Bearbeiten Sie die hervorgehobenen Zeilen im Skript mit Ihren Werten für Variablen.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/backup-restore/restore-server.sh?highlight=7,10-12 "Perform point-in-time-restore of a source server to a new server.")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Nach der Ausführung des Beispielskripts können die Ressourcen mit dem folgenden Codeausschnitt bereinigt werden:

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/backup-restore/clean-up-resources.sh?highlight=4-5 "Clean up resources.")]

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| **Befehl** | **Hinweise** |
|---|---|
|[az group create](/cli/azure/group#az_group_create)|Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind.|
|[az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)|Erstellt eine Flexible Server-Instanz, die die Datenbanken hostet.|
|[az mysql flexible-server restore](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_restore)|Stellt eine Flexible Server-Instanz aus einer Sicherung wieder her.|
|[az mysql flexible-server show](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_show)|Ruft Details einer Flexible Server-Instanz ab.|
|[az mysql flexible-server delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete)|Löscht eine Flexible Server-Instanz.|
|[az group delete](/cli/azure/group#az_group_delete) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen.|

## <a name="next-steps"></a>Nächste Schritte

- Probieren Sie weitere Skripts aus: [Azure CLI-Beispiele für Azure Database for MySQL – Flexible Server](../sample-scripts-azure-cli.md).
- Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).