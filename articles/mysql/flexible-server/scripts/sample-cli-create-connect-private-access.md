---
title: 'CLI-Skript: Erstellen einer Instanz von Azure Database for MySQL – Flexible Server in einem VNet'
description: Dieses Azure CLI-Beispielskript zeigt, wie Sie eine Instanz von Azure Database for MySQL – Flexible Server in einem VNet (Verbindungsmethode für privaten Zugriff) erstellen und über einen virtuellen Computer innerhalb des VNet eine Verbindung mit dem Server herstellen.
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: 4d7972293652d765cdb1686ff7759a41104f8229
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131844238"
---
# <a name="create-an-azure-database-for-mysql---flexible-server-in-a-vnet-using-azure-cli"></a>Erstellen einer Instanz von Azure Database for MySQL – Flexible Server in einem VNet mithilfe der Azure CLI

Mit diesem Azure CLI-Beispielskript wird eine Instanz von Azure Database for MySQL – Flexible Server in einem VNet erstellt ([Verbindungsmethode für privaten Zugriff](../concepts-networking-vnet.md)) und über einen virtuellen Computer innerhalb des VNet eine Verbindung mit dem Server hergestellt.

> [!NOTE] 
> Die Konnektivitätsmethode kann nicht geändert werden, nachdem der Server erstellt wurde. Wenn Sie beispielsweise einen Server mithilfe von *Private access (VNet Integration)* (Privater Zugriff (VNet-Integration)) erstellen, können Sie nach der Erstellung nicht zu *Public access (allowed IP addresses)* (Öffentlicher Zugriff (zulässige IP-Adressen)) wechseln. Weitere Informationen zu Konnektivitätsmethoden finden Sie unter [Konnektivitäts- und Netzwerkkonzepte für Azure Database for MySQL – Flexible Server (Vorschau)](../concepts-networking.md).

[!INCLUDE [flexible-server-free-trial-note](../../includes/flexible-server-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment](../../../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert. 

## <a name="sample-script"></a>Beispielskript

Bearbeiten Sie die hervorgehobenen Zeilen im Skript mit Ihren Werten für Variablen.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/create-server-private-access/create-connect-server-in-vnet.sh?highlight=7,10 "Create and Connect to an Azure Database for MySQL - Flexible Server (General Purpose SKU) in VNet")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Nach der Ausführung des Beispielskripts können die Ressourcen mit dem folgenden Codeausschnitt bereinigt werden:

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/create-server-private-access/clean-up-resources.sh "Clean up resources.")]

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| **Befehl** | **Hinweise** |
|---|---|
|[az group create](/cli/azure/group#az_group_create)|Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind.|
|[az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)|Erstellt eine Flexible Server-Instanz, die die Datenbanken hostet.|
|[az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create)|Erstellt ein Subnetz innerhalb des VNet.|
|[az vm create](/cli/azure/vm#az_vm_create)|Erstellt einen virtuellen Azure-Computer.|
|[az vm open-port](/cli/azure/vm#az_vm_open_port)|Öffnet einen virtuellen Computer für eingehenden Datenverkehr an angegebenen Ports.|
|[az group delete](/cli/azure/group#az_group_delete) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen.|

## <a name="next-steps"></a>Nächste Schritte

- Probieren Sie weitere Skripts aus: [Azure CLI-Beispiele für Azure Database for MySQL – Flexible Server](../sample-scripts-azure-cli.md).
- Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).