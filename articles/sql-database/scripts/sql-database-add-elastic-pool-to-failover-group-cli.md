---
title: 'CLI-Beispiel: Failovergruppe – Pool für elastische Azure SQL-Datenbank-Instanzen'
description: Hier finden Sie ein Azure CLI-Beispielskript, mit dem Sie einen Pool für elastische Azure SQL-Datenbank-Instanzen erstellen, ihn einer Failovergruppe hinzufügen und das Failover testen können.
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
ms.openlocfilehash: 04389f3cc5604e0412776e2c02b4e3e4fcc8738b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131013724"
---
# <a name="use-cli-to-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>Verwenden der CLI zum Hinzufügen eines Pools für elastische Azure SQL-Datenbank-Instanzen zu einer Failovergruppe

Mit diesem Azure CLI-Skriptbeispiel wird eine Einzeldatenbank erstellt und einem Pool für elastische Azure SQL-Datenbank-Instanzen hinzugefügt, eine Failovergruppe erstellt und das Failover getestet.

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für diesen Artikel die Azure CLI-Version 2.0 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu ermitteln. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Beispielskript

### <a name="sign-in-to-azure"></a>Anmelden bei Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Führen Sie das Skript aus.

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-cli.sh "Add elastic pool to a failover group")]

### <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Verwenden Sie den folgenden Befehl, um die Ressourcengruppe und alle zugehörigen Ressourcen zu entfernen:

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Beispielreferenz

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | BESCHREIBUNG |
|---|---|
| [az sql elastic-pool](/cli/azure/sql/elastic-pool) | Befehle für Pools für elastische Datenbanken |
| [az sql failover-group ](/cli/azure/sql/failover-group) | Befehle für Failovergruppen |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure/overview).

Weitere Azure CLI-Skriptbeispiele für SQL-Datenbank finden Sie [hier](../../azure-sql/database/az-cli-script-samples-content-guide.md).
