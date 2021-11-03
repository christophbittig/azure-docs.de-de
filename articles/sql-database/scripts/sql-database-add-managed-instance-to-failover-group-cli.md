---
title: 'CLI-Beispiel: Failovergruppe – Azure SQL Managed Instance'
description: Enthält ein Azure CLI-Beispielskript, mit dem Sie eine Azure SQL Managed Instance erstellen, einer Failovergruppe hinzufügen und das Failover testen können.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: rothja
ms.author: jroth
ms.reviewer: mathoma
ms.date: 07/16/2019
ms.openlocfilehash: 5ffac59d08c9646273fd0056a05b6474f3339abe
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131074990"
---
# <a name="use-cli-to-add-an-azure-sql-managed-instance-to-a-failover-group"></a>Hinzufügen einer Azure SQL Managed Instance zu einer Failovergruppe mit der CLI

Mit diesem Azure CLI-Beispielskript werden zwei verwaltete Instanzen erstellt und einer Failovergruppe hinzugefügt. Anschließend wird das Failover von der primären verwalteten Instanz auf die sekundäre verwaltete Instanz getestet.

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für diesen Artikel die Azure CLI-Version 2.0 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu ermitteln. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Beispielskript

### <a name="sign-in-to-azure"></a>Anmelden bei Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Führen Sie das Skript aus.

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-cli.sh "Add managed instance to a failover group")]

### <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Verwenden Sie den folgenden Befehl, um die Ressourcengruppe und alle zugehörigen Ressourcen zu entfernen: Die Ressourcengruppe muss zweimal entfernt werden. Wenn Sie die Ressourcengruppe zum ersten Mal entfernen, werden die verwaltete Instanz und die virtuellen Cluster entfernt, es wird jedoch die Fehlermeldung `az group delete : Long running operation failed with status 'Conflict'.` ausgegeben. Führen Sie den Befehl „az group delete“ ein zweites Mal aus, um alle übrigen Ressourcen sowie die Ressourcengruppe zu entfernen.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Beispielreferenz

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Beschreibung |
|---|---|
| [az network vnet](/cli/azure/network/vnet) | Befehle für virtuelle Netzwerke  |
| [az network vnet subnet](/cli/azure/network/vnet/subnet) | Befehle für Subnetze des virtuellen Netzwerks |
| [az network nsg](/cli/azure/network/nsg) | Befehle für Netzwerksicherheitsgruppen |
| [az network route-table](/cli/azure/network/route-table) | Befehle für Routingtabellen |
| [az sql mi](/cli/azure/sql/mi) | Befehle für SQL Managed Instance |
| [az network public-ip](/cli/azure/network/public-ip) | Befehle für öffentliche IP-Adressen von Netzwerken |
| [az network vnet-gateway](/cli/azure/network/vnet-gateway) | Befehle für Gateways für virtuelle Netzwerke |
| [az sql instance-failover-group](/cli/azure/sql/instance-failover-group) | Befehle für Failovergruppen von SQL Managed Instance |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).

Zusätzliche SQL-Datenbank-CLI-Skriptbeispiele finden Sie in der [Azure SQL-Datenbank-Dokumentation](../../azure-sql/database/az-cli-script-samples-content-guide.md).
