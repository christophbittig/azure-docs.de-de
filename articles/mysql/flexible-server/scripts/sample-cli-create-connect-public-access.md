---
title: 'CLI-Skript: Erstellen einer Instanz von Azure Database for MySQL – Flexible Server und Aktivieren der Konnektivität für den öffentlichen Zugriff'
description: Dieses Azure CLI-Beispielskript zeigt, wie Sie eine Instanz von Azure Database for MySQL – Flexible Server erstellen, eine Firewallregel auf Serverebene konfigurieren (Konnektivitätsmethode für den öffentlichen Zugriff) und eine Verbindung mit dem Server herstellen.
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: 59df8d613e8753895fba77d3bfabefe476f49cf1
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131844192"
---
# <a name="create-an-azure-database-for-mysql---flexible-server-and-enable-public-access-connectivity-using-azure-cli"></a>Erstellen einer Instanz von Azure Database for MySQL – Flexible Server und Aktivieren der Konnektivität für den öffentlichen Zugriff mithilfe der Azure CLI

Dieses CLI-Beispielskript erstellt eine Instanz von Azure Database for MySQL – Flexibler Server, konfiguriert eine Firewallregel auf Serverebene ([Konnektivitätsmethode für den öffentlichen Zugriff](../concepts-networking-public.md)) und stellt nach der Erstellung eine Verbindung mit dem Server her. 

Sobald das Skript erfolgreich ausgeführt wird, ist die MySQL Flexible Server-Instanz für alle Azure-Dienste und die konfigurierte IP-Adresse zugänglich, und Sie werden mit dem Server in einem interaktiven Modus verbunden.

> [!NOTE] 
> Die Konnektivitätsmethode kann nicht geändert werden, nachdem der Server erstellt wurde. Wenn Sie z. B. einen Server mithilfe des *öffentlichen Zugriffs (zulässige IP-Adressen)* erstellen, können Sie nach der Erstellung nicht zum *privaten Zugriff (VNet-Integration)* wechseln. Weitere Informationen zu Konnektivitätsmethoden finden Sie unter [Konnektivitäts- und Netzwerkkonzepte für Azure Database for MySQL – Flexible Server (Vorschau)](../concepts-networking.md).


[!INCLUDE [flexible-server-free-trial-note](../../includes/flexible-server-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment](../../../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert. 

## <a name="sample-script"></a>Beispielskript

Bearbeiten Sie die hervorgehobenen Zeilen im Skript mit Ihren Werten für Variablen.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/create-server-public-access/create-connect-burstable-server-public-access.sh?highlight=8,11-12 "Create Flexible Server and enable public access.")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Nach der Ausführung des Beispielskripts können die Ressourcen mit dem folgenden Codeausschnitt bereinigt werden:

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/create-server-public-access/clean-up-resources.sh?highlight=4 "Clean up resources.")]

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| **Befehl** | **Hinweise** |
|---|---|
|[az group create](/cli/azure/group#az_group_create)|Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind.|
|[az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)|Erstellt eine Flexible Server-Instanz, die die Datenbanken hostet.|
|[az mysql flexible-server firewall-rule create](/cli/azure/mysql/flexible-server/firewall-rule#az_mysql_flexible_server_firewall_rule_create)|Erstellt eine Firewallregel, die vom eingegebenen IP-Adressbereich aus den Zugriff auf die Flexible Server-Instanz und die darauf befindlichen Datenbanken ermöglicht.|
|[az mysql flexible-server connect](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_connect)|Stellt eine Verbindung mit einer Flexible Server-Instanz her, um Server- oder Datenbankvorgänge auszuführen.|
|[az mysql flexible-server delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete)|Löscht eine Flexible Server-Instanz.|
|[az group delete](/cli/azure/group#az_group_delete) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen.|

## <a name="next-steps"></a>Nächste Schritte

- Probieren Sie weitere Skripts aus: [Azure CLI-Beispiele für Azure Database for MySQL – Flexible Server](../sample-scripts-azure-cli.md).
- Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).