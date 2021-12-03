---
title: 'CLI-Skript: Auflisten und Ändern der Serverparameter einer Instanz von Azure Database for MySQL – Flexible Server'
description: Dieses Azure CLI-Beispielskript zeigt, wie Serverparameter einer Instanz von Azure Database for MySQL – Flexible Server aufgeführt und geändert werden.
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: 4316669346f8bedec7a674da3e7114b95dd8c4ca
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131844257"
---
# <a name="list-and-change-server-parameters-of-an-azure-database-for-mysql---flexible-server-using-azure-cli"></a>Auflisten und Ändern der Serverparameter einer Instanz von Azure Database for MySQL – Flexible Server mithilfe der Azure CLI

Mit diesem CLI-Beispielskript werden alle verfügbaren [Serverparameter](../concepts-server-parameters.md) sowie ihre zulässigen Werte für die Instanz von Azure Database for MySQL – Flexible Server aufgeführt. Zudem wird für die Parameter *max_connections* und *time_zone* (global) ein anderer Wert als der Standardwert festgelegt.

[!INCLUDE [flexible-server-free-trial-note](../../includes/flexible-server-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment](../../../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert. 

## <a name="sample-script"></a>Beispielskript

Bearbeiten Sie die hervorgehobenen Zeilen im Skript mit Ihren Werten für Variablen.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/manage-server/change-server-parameters.sh?highlight=7,10-11 "Change server parameters for Azure Database for MySQL - Flexible Server.")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Nach der Ausführung des Beispielskripts können die Ressourcen mit dem folgenden Codeausschnitt bereinigt werden:

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/manage-server/clean-up-resources.sh?highlight=4 "Clean up resources.")]


## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| **Befehl** | **Hinweise** |
|---|---|
|[az group create](/cli/azure/group#az_group_create)|Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind.|
|[az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)|Erstellt eine Flexible Server-Instanz, die die Datenbanken hostet.|
|[az mysql flexible-server parameter list](/cli/azure/mysql/flexible-server/parameter#az_mysql_flexible_server_parameter_list)|Listet die Parameterwerte für eine Flexible Server-Instanz auf.|
|[az mysql flexible-server parameter set](/cli/azure/mysql/flexible-server/parameter#az_mysql_flexible_server_parameter_set)|Aktualisiert den Parameter einer Flexible Server-Instanz.|
|[az mysql flexible-server parameter show](/cli/azure/mysql/flexible-server/parameter#az_mysql_flexible_server_parameter_show)|Ruft einen bestimmten Parameterwert für eine Flexible Server-Instanz ab.|
|[az mysql flexible-server delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete)|Löscht eine Flexible Server-Instanz.|
|[az group delete](/cli/azure/group#az_group_delete) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen.|

## <a name="next-steps"></a>Nächste Schritte

- Probieren Sie weitere Skripts aus: [Azure CLI-Beispiele für Azure Database for MySQL – Flexible Server](../sample-scripts-azure-cli.md).
- Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).