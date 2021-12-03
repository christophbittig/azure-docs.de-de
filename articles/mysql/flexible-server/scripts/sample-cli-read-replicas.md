---
title: 'CLI-Skript: Erstellen und Verwalten von Lesereplikaten in einer Instanz von Azure Database for MySQL – Flexible Server'
description: Dieses Azure CLI-Beispielskript zeigt, wie Sie Lesereplikate in einer Instanz von Azure Database for MySQL – Flexible Server erstellen und verwalten.
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: 1155231ca8909352aee2354023a19ea66c7ffc4d
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131844097"
---
# <a name="create-and-manage-read-replicas-in-an-azure-database-for-mysql---flexible-server-using-azure-cli"></a>Erstellen und Verwalten von Lesereplikaten in einer Instanz von Azure Database for MySQL – Flexible Server mithilfe der Azure CLI

Dieses CLI-Beispielskript erstellt und verwaltet [Lesereplikate](../concepts-read-replicas.md) in einer Instanz von Azure Database for MySQL – Flexible Server.

>[!IMPORTANT]
>Wenn Sie ein Replikat für eine Quelle erstellen, die keine vorhandenen Replikate hat, startet die Quelle zunächst neu, um sich auf die Replikation vorzubereiten. Beachten Sie dies, und führen Sie diese Vorgänge nicht zu Spitzenzeiten durch.

[!INCLUDE [flexible-server-free-trial-note](../../includes/flexible-server-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment](../../../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert. 

## <a name="sample-script"></a>Beispielskript

Bearbeiten Sie die hervorgehobenen Zeilen im Skript mit Ihren Werten für Variablen.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/read-replicas/create-manage-read-replicas.sh?highlight=7,10-12 "Create and manage Flexible Server Read Replicas.")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Nach der Ausführung des Beispielskripts können die Ressourcen mit dem folgenden Codeausschnitt bereinigt werden:

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/read-replicas/clean-up-resources.sh?highlight=4-5 "Clean up resources.")]

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| **Befehl** | **Hinweise** |
|---|---|
|[az group create](/cli/azure/group#az_group_create)|Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind.|
|[az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)|Erstellt eine Flexible Server-Instanz, die die Datenbanken hostet.|
|[az mysql flexible-server replica create](/cli/azure/mysql/flexible-server/replica#az_mysql_flexible_server_replica_create)|Erstellt ein Lesereplikat für einen Server.|
|[az mysql flexible-server replica list](/cli/azure/mysql/flexible-server/replica#az_mysql_flexible_server_replica_list)|Listet alle Lesereplikate für einen bestimmten Server auf.|
|[az mysql flexible-server replica stop-replication](/cli/azure/mysql/flexible-server/replica#az_mysql_flexible_server_replica_stop_replication)|Beendet die Replikation in ein Lesereplikat und macht es zu einem Lese-/Schreibserver.|
|[az mysql flexible-server delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete)|Löscht eine Flexible Server-Instanz.|
|[az group delete](/cli/azure/group#az_group_delete) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen.|

## <a name="next-steps"></a>Nächste Schritte

- Probieren Sie weitere Skripts aus: [Azure CLI-Beispiele für Azure Database for MySQL – Flexible Server](../sample-scripts-azure-cli.md).
- Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).