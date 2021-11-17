---
title: 'CLI-Skript: Konfigurieren der zonenredundanten Hochverfügbarkeit in einer Instanz von Azure Database for MySQL – Flexible Server'
description: In diesem Azure CLI-Beispielskript wird veranschaulicht, wie Sie die zonenredundante Hochverfügbarkeit in einer Instanz von Azure Database for MySQL – Flexible Server konfigurieren.
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: c24871aff30c34398a07604749551bf53b428d36
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131844083"
---
# <a name="configure-zone-redundant-high-availability-in-an-azure-database-for-mysql---flexible-server-using-azure-cli"></a>Konfigurieren der zonenredundanten Hochverfügbarkeit in einer Instanz von Azure Database for MySQL – Flexible Server mithilfe der Azure CLI

Dieses CLI-Beispielskript konfiguriert und verwaltet die [zonenredundante Hochverfügbarkeit](../concepts-high-availability.md) in einer Instanz von Azure Database for MySQL – Flexible Server. Sie können die zonenredundante Hochverfügbarkeit nur bei der Erstellung einer Flexible Server-Instanz aktivieren und sie jederzeit wieder deaktivieren. Sie können die Verfügbarkeitszone auch für das primäre Replikat und das Standbyreplikat auswählen. 

Gegenwärtig wird die zonenredundante Hochverfügbarkeit nur für die Tarife „Universell“ und „Arbeitsspeicheroptimiert“ unterstützt.


[!INCLUDE [flexible-server-free-trial-note](../../includes/flexible-server-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment](../../../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert. 

## <a name="sample-script"></a>Beispielskript

Bearbeiten Sie die hervorgehobenen Zeilen im Skript mit Ihren Werten für Variablen.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/high-availability/zone-redundant-ha.sh?highlight=7,10-11,13-14 "Configure Zone-Redundant High Availability.")]


## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Nach der Ausführung des Beispielskripts können die Ressourcen mit dem folgenden Codeausschnitt bereinigt werden:

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/high-availability/clean-up-resources.sh?highlight=4 "Clean up resources.")]


## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| **Befehl** | **Hinweise** |
|---|---|
|[az group create](/cli/azure/group#az_group_create)|Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind.|
|[az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)|Erstellt eine Flexible Server-Instanz, die die Datenbanken hostet.|
|[az mysql flexible-server update](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_update)|Aktualisiert eine Flexible Server-Instanz.|
|[az mysql flexible-server delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete)|Löscht eine Flexible Server-Instanz.|
|[az group delete](/cli/azure/group#az_group_delete) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen.|

## <a name="next-steps"></a>Nächste Schritte

- Probieren Sie weitere Skripts aus: [Azure CLI-Beispiele für Azure Database for MySQL – Flexible Server](../sample-scripts-azure-cli.md).
- Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).