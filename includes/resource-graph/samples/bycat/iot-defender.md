---
author: georgewallace
ms.service: resource-graph
ms.topic: include
ms.date: 10/12/2021
ms.author: gwallace
ms.custom: generated
ms.openlocfilehash: 00d6f16fd755b7e6d87fa4201c804fba42f0e333
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132060168"
---
### <a name="count-all-sensors-by-type"></a>Zählen aller Sensoren nach Typ

Diese Abfrage fasst alle Sensoren nach Typ (OT, EIoT) zusammen.

```kusto
iotsecurityresources
| where type == 'microsoft.iotsecurity/sensors'
| summarize count() by tostring(properties.sensorType)
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
az graph query -q "iotsecurityresources | where type == 'microsoft.iotsecurity/sensors' | summarize count() by tostring(properties.sensorType)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "iotsecurityresources | where type == 'microsoft.iotsecurity/sensors' | summarize count() by tostring(properties.sensorType)"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Probieren Sie im Azure Resource Graph-Explorer die folgende Abfrage aus:

- Azure-Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2fsensors%27%0a%7c%20summarize%20count()%20by%20tostring(properties.sensorType)" target="_blank">portal.azure.com</a>
- Azure Government-Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2fsensors%27%0a%7c%20summarize%20count()%20by%20tostring(properties.sensorType)" target="_blank">portal.azure.us</a>
- Azure China 21Vianet-Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2fsensors%27%0a%7c%20summarize%20count()%20by%20tostring(properties.sensorType)" target="_blank">portal.azure.cn</a>

---

### <a name="count-how-many-iot-devices-there-are-in-your-network-by-operation-system"></a>IoT-Geräte, die sich in Ihrem Netzwerk befinden, nach Betriebssystem zählen

Diese Abfrage fast alle IoT-Geräte nach der Plattform ihres Betriebssystems zusammen.

```kusto
iotsecurityresources
| where type == 'microsoft.iotsecurity/locations/devicegroups/devices'
| summarize count() by tostring(properties.operatingSystem.platform)
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
az graph query -q "iotsecurityresources | where type == 'microsoft.iotsecurity/locations/devicegroups/devices' | summarize count() by tostring(properties.operatingSystem.platform)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "iotsecurityresources | where type == 'microsoft.iotsecurity/locations/devicegroups/devices' | summarize count() by tostring(properties.operatingSystem.platform)"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Probieren Sie im Azure Resource Graph-Explorer die folgende Abfrage aus:

- Azure-Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2flocations%2fdevicegroups%2fdevices%27%0a%7c%20summarize%20count()%20by%20tostring(properties.operatingSystem.platform)" target="_blank">portal.azure.com</a>
- Azure Government-Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2flocations%2fdevicegroups%2fdevices%27%0a%7c%20summarize%20count()%20by%20tostring(properties.operatingSystem.platform)" target="_blank">portal.azure.us</a>
- Azure China 21Vianet-Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2flocations%2fdevicegroups%2fdevices%27%0a%7c%20summarize%20count()%20by%20tostring(properties.operatingSystem.platform)" target="_blank">portal.azure.cn</a>

---

### <a name="get-all-high-severity-recommendations"></a>Alle Empfehlungen mit hohem Schweregrad abrufen

Diese Abfrage enthält eine Liste aller Empfehlungen des Benutzers mit hohem Schweregrad.

```kusto
iotsecurityresources
| where type == 'microsoft.iotsecurity/locations/devicegroups/recommendations'
| where properties.severity == 'High'
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
az graph query -q "iotsecurityresources | where type == 'microsoft.iotsecurity/locations/devicegroups/recommendations' | where properties.severity == 'High'"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "iotsecurityresources | where type == 'microsoft.iotsecurity/locations/devicegroups/recommendations' | where properties.severity == 'High'"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Probieren Sie im Azure Resource Graph-Explorer die folgende Abfrage aus:

- Azure-Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2flocations%2fdevicegroups%2frecommendations%27%0a%7c%20where%20properties.severity%20%3d%3d%20%27High%27" target="_blank">portal.azure.com</a>
- Azure Government-Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2flocations%2fdevicegroups%2frecommendations%27%0a%7c%20where%20properties.severity%20%3d%3d%20%27High%27" target="_blank">portal.azure.us</a>
- Azure China 21Vianet-Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2flocations%2fdevicegroups%2frecommendations%27%0a%7c%20where%20properties.severity%20%3d%3d%20%27High%27" target="_blank">portal.azure.cn</a>

---

### <a name="list-sites-with-a-specific-tag-value"></a>Websites mit einem bestimmten Tagwert auflisten

Diese Abfrage stellt eine Liste aller Websites mit einem bestimmten Tagwert bereit.

```kusto
iotsecurityresources
| where type == 'microsoft.iotsecurity/sites'
| where properties.tags['key'] =~ 'value1'
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
az graph query -q "iotsecurityresources | where type == 'microsoft.iotsecurity/sites' | where properties.tags['key'] =~ 'value1'"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "iotsecurityresources | where type == 'microsoft.iotsecurity/sites' | where properties.tags['key'] =~ 'value1'"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Probieren Sie im Azure Resource Graph-Explorer die folgende Abfrage aus:

- Azure-Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2fsites%27%0a%7c%20where%20properties.tags%5b%27key%27%5d%20%3d%7e%20%27value1%27" target="_blank">portal.azure.com</a>
- Azure Government-Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2fsites%27%0a%7c%20where%20properties.tags%5b%27key%27%5d%20%3d%7e%20%27value1%27" target="_blank">portal.azure.us</a>
- Azure China 21Vianet-Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2fsites%27%0a%7c%20where%20properties.tags%5b%27key%27%5d%20%3d%7e%20%27value1%27" target="_blank">portal.azure.cn</a>

---

