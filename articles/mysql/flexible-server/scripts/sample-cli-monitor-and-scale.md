---
title: 'CLI-Skript: Überwachen und Skalieren einer Instanz von Azure Database for MySQL – Flexible Server (Vorschau)'
description: Dieses Azure CLI-Beispielskript zeigt, wie Sie eine einzelne Instanz von Azure Database for MySQL – Flexible Server überwachen und hoch- und herunterskalieren, um wechselnde Leistungsanforderungen zu erfüllen.
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: 3ab338f1648753b89907180be78b3a687b370ded
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128604639"
---
# <a name="monitor-and-scale-an-azure-database-for-mysql---flexible-server-preview-using-azure-cli"></a>Überwachen und Skalieren einer Instanz von Azure Database for MySQL – Flexible Server (Vorschau) mithilfe der Azure CLI

Dieses CLI-Beispielskript skaliert Compute- und Speicherressourcen sowie IOPS für eine einzelne Instanz von Azure Database for MySQL – Flexible Server nach Abfragen der entsprechenden Metriken. Computeressourcen und IOPS können hoch- oder herunterskaliert werden, Speicher kann hingegen nur hochskaliert werden. 

[!INCLUDE [flexible-server-free-trial-note](../../includes/flexible-server-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment](../../../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert. 

## <a name="sample-script"></a>Beispielskript

Bearbeiten Sie die hervorgehobenen Zeilen im Skript mit Ihren Werten für Variablen.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/monitor-and-scale/monitor-and-scale.sh?highlight=8,11-12 "Monitor your Flexible Server and scale Compute, Storage and IOPS.")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Nach der Ausführung des Beispielskripts können die Ressourcen mit dem folgenden Codeausschnitt bereinigt werden:

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/monitor-and-scale/clean-up-resources.sh?highlight=4 "Clean up resources.")]


## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| **Befehl** | **Hinweise** |
|---|---|
|[az group create](/cli/azure/group#az_group_create)|Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind.|
|[az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)|Erstellt eine Flexible Server-Instanz, die die Datenbanken hostet.|
|[az monitor metrics list](/cli/azure/monitor/metrics#az_monitor_metrics_list)|Listet den Azure Monitor-Metrikwert für die Ressourcen auf.|
|[az mysql flexible-server update](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_update)|Aktualisiert die Eigenschaften von Flexible Server.|
|[az mysql flexible-server delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete)|Löscht eine Flexible Server-Instanz.|
|[az group delete](/cli/azure/group#az_group_delete) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen.|

## <a name="next-steps"></a>Nächste Schritte

- Probieren Sie weitere Skripts aus: [Azure CLI-Beispiele für Azure Database for MySQL – Flexible Server (Vorschau)](../sample-scripts-azure-cli.md).
- Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).