---
author: DCtheGeek
ms.service: resource-graph
ms.topic: include
ms.date: 09/03/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: bb5da3ac6ae5b0b1ccd6105049321df12c65ec3e
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/07/2021
ms.locfileid: "123536288"
---
### <a name="list-azure-cosmos-db-with-specific-write-locations"></a>Auflisten von Azure Cosmos DB mit bestimmten Schreibinstanzen

Die folgende Abfrage schränkt die Azure Cosmos DB-Ressourcen ein und verwendet `mv-expand`, um den Eigenschaftenbehälter für **properties.writeLocations** zu erweitern. Anschließend werden bestimmte Felder projiziert und die Ergebnisse weiter auf **properties.writeLocations.locationName**-Werte für „USA, Osten“ oder „USA, Westen“ eingeschränkt.

```kusto
Resources
| where type =~ 'microsoft.documentdb/databaseaccounts'
| project id, name, writeLocations = (properties.writeLocations)
| mv-expand writeLocations
| project id, name, writeLocation = tostring(writeLocations.locationName)
| where writeLocation in ('East US', 'West US')
| summarize by id, name
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.documentdb/databaseaccounts' | project id, name, writeLocations = (properties.writeLocations) | mv-expand writeLocations | project id, name, writeLocation = tostring(writeLocations.locationName) | where writeLocation in ('East US', 'West US') | summarize by id, name"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.documentdb/databaseaccounts' | project id, name, writeLocations = (properties.writeLocations) | mv-expand writeLocations | project id, name, writeLocation = tostring(writeLocations.locationName) | where writeLocation in ('East US', 'West US') | summarize by id, name"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Probieren Sie im Azure Resource Graph-Explorer die folgende Abfrage aus:

- Azure-Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.documentdb%2fdatabaseaccounts%27%0a%7c%20project%20id%2c%20name%2c%20writeLocations%20%3d%20(properties.writeLocations)%0a%7c%20mv-expand%20writeLocations%0a%7c%20project%20id%2c%20name%2c%20writeLocation%20%3d%20tostring(writeLocations.locationName)%0a%7c%20where%20writeLocation%20in%20(%27East%20US%27%2c%20%27West%20US%27)%0a%7c%20summarize%20by%20id%2c%20name" target="_blank">portal.azure.com</a>
- Azure Government-Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.documentdb%2fdatabaseaccounts%27%0a%7c%20project%20id%2c%20name%2c%20writeLocations%20%3d%20(properties.writeLocations)%0a%7c%20mv-expand%20writeLocations%0a%7c%20project%20id%2c%20name%2c%20writeLocation%20%3d%20tostring(writeLocations.locationName)%0a%7c%20where%20writeLocation%20in%20(%27East%20US%27%2c%20%27West%20US%27)%0a%7c%20summarize%20by%20id%2c%20name" target="_blank">portal.azure.us</a>
- Azure China 21Vianet-Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.documentdb%2fdatabaseaccounts%27%0a%7c%20project%20id%2c%20name%2c%20writeLocations%20%3d%20(properties.writeLocations)%0a%7c%20mv-expand%20writeLocations%0a%7c%20project%20id%2c%20name%2c%20writeLocation%20%3d%20tostring(writeLocations.locationName)%0a%7c%20where%20writeLocation%20in%20(%27East%20US%27%2c%20%27West%20US%27)%0a%7c%20summarize%20by%20id%2c%20name" target="_blank">portal.azure.cn</a>

---

