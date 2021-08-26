---
title: 'Azure PowerShell-Beispiele für Azure Cosmos DB: Core-API (SQL)'
description: Hier erfahren Sie, wie Sie Azure PowerShell-Beispiele zum Durchführen gängiger Aufgaben in Azure Cosmos DB für die Core-API (SQL) abrufen.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 01/20/2021
ms.author: mjbrown
ms.openlocfilehash: 89f1b8bb3b50dad8322b5a30c37de5b2ed6cc507
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121725427"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-core-sql-api"></a>Azure PowerShell-Beispiele für Azure Cosmos DB: Core-API (SQL)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Die unten stehende Tabelle enthält Links zu häufig verwendeten Azure PowerShell-Skripts für Azure Cosmos DB. Navigieren Sie über die Links auf der rechten Seite zu API-spezifischen Beispielen. Allgemeine Beispiele sind in allen APIs identisch. Referenzseiten für alle Azure Cosmos DB-PowerShell-Cmdlets finden Sie in der [Referenz zu Azure PowerShell](/powershell/module/az.cosmosdb). Das Modul `Az.CosmosDB` ist nun Teil des Moduls `Az`. Sie können die aktuelle Version des Az-Moduls [herunterladen und installieren](/powershell/azure/install-az-ps), um die Azure Cosmos DB-Cmdlets zu erhalten. Die neueste Version können Sie außerdem aus dem [PowerShell-Katalog](https://www.powershellgallery.com/packages/Az/5.4.0) herunterladen. Sie können diese PowerShell-Beispiele für Cosmos DB auch aus unserem GitHub-Repository forken. Informationen dazu finden Sie in den [Cosmos DB-PowerShell-Beispielen auf GitHub](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

PowerShell-Cmdlets für andere APIs finden Sie unter [Azure PowerShell-Beispiele für Azure Cosmos DB: Cassandra-API](cassandra/powershell-samples.md), [Azure PowerShell-Beispiele für Azure Cosmos DB: MongoDB-API](powershell-samples-mongodb.md), [Azure PowerShell-Beispiele für Azure Cosmos DB: Gremlin-API](powershell-samples-gremlin.md) und [Azure PowerShell-Beispiele für Azure Cosmos DB: Tabellen-API](powershell-samples-table.md).

## <a name="common-samples"></a>Allgemeine Beispiele

|Aufgabe | BESCHREIBUNG |
|---|---|
|[Konto aktualisieren](scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualisieren der Standardkonsistenzebene eines Cosmos DB-Kontos |
|[Regionen eines Kontos aktualisieren](scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualisieren der Regionen eines Cosmos DB-Kontos |
|[Failoverpriorität ändern oder Failover auslösen](scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ändern der Priorität eines regionalen Failovers eines Azure Cosmos-Kontos oder Auslösen eines manuellen Failovers |
|[Kontoschlüssel oder Verbindungszeichenfolgen](scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Abrufen von Primär- und Sekundärschlüsseln und Verbindungszeichenfolgen oder erneutes Generieren eines Kontoschlüssels eines Azure Cosmos DB-Kontos |
|[Cosmos-Konto mit IP-Firewall erstellen](scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Erstellen eines Azure Cosmos DB-Kontos mit aktivierter IP-Firewall |
|||

## <a name="core-sql-api-samples"></a>Core-API-Beispiele (SQL)

|Aufgabe | BESCHREIBUNG |
|---|---|
|[Konto, Datenbank und Container erstellen](scripts/powershell/sql/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Erstellen eines Kontos, einer Datenbank und eines Containers in Azure Cosmos DB |
|[Konto, Datenbank und Container mit Autoskalierung erstellen](scripts/powershell/sql/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Erstellen eines Kontos, einer Datenbank und eines Containers mit Autoskalierung in Azure Cosmos DB |
|[Container mit einem großen Partitionsschlüssel erstellen](scripts/powershell/sql/create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Erstellen eines Containers mit einem großen Partitionsschlüssel |
|[Container ohne Indexrichtlinie erstellen](scripts/powershell/sql/create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Erstellen eines Azure Cosmos DB-Containers mit deaktivierter Indexrichtlinie.|
|[Datenbanken oder Container auflisten oder abrufen](scripts/powershell/sql/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Auflisten oder Abrufen von Datenbanken oder Containern |
|[Durchsatzvorgänge](scripts/powershell/sql/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Durchsatzvorgänge für eine Datenbank oder einen Container, z. B. Abrufen, Aktualisieren und Migrieren zwischen automatisch skaliertem und standardmäßigem Durchsatz. |
|[Sperren von Ressourcen für die Löschung](scripts/powershell/sql/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Verhindern des Löschens von Ressourcen mit Ressourcensperren. |
|||
