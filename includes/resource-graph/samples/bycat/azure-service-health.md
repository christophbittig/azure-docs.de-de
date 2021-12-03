---
author: georgewallace
ms.service: resource-graph
ms.topic: include
ms.date: 10/12/2021
ms.author: gwallace
ms.custom: generated
ms.openlocfilehash: addfc32c7a785936006294b071db9c0b4cc32812
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132053021"
---
### <a name="active-service-health-event-subscription-impact"></a>Die Auswirkungen von Active Service Health-Ereignisabonnements

Hier werden alle aktiven Service Health Ereignisse zurückgegeben, einschließlich Dienstproblemen, geplanter Wartung, Integritätsratgebern und Sicherheitsratgebern – gruppiert nach Ereignistyp und der Anzahl der betroffenen Abonnements.

```kusto
ServiceHealthResources
| where type =~ 'Microsoft.ResourceHealth/events'
| extend eventType = tostring(properties.EventType), status = properties.Status, description = properties.Title, trackingId = properties.TrackingId, summary = properties.Summary, priority = properties.Priority, impactStartTime = properties.ImpactStartTime, impactMitigationTime = properties.ImpactMitigationTime
| where properties.Status == 'Active' and tolong(impactStartTime) > 1
| summarize count(subscriptionId) by name, eventType
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
az graph query -q "ServiceHealthResources | where type =~ 'Microsoft.ResourceHealth/events' | extend eventType = tostring(properties.EventType), status = properties.Status, description = properties.Title, trackingId = properties.TrackingId, summary = properties.Summary, priority = properties.Priority, impactStartTime = properties.ImpactStartTime, impactMitigationTime = properties.ImpactMitigationTime | where properties.Status == 'Active' and tolong(impactStartTime) > 1 | summarize count(subscriptionId) by name, eventType"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "ServiceHealthResources | where type =~ 'Microsoft.ResourceHealth/events' | extend eventType = tostring(properties.EventType), status = properties.Status, description = properties.Title, trackingId = properties.TrackingId, summary = properties.Summary, priority = properties.Priority, impactStartTime = properties.ImpactStartTime, impactMitigationTime = properties.ImpactMitigationTime | where properties.Status == 'Active' and tolong(impactStartTime) > 1 | summarize count(subscriptionId) by name, eventType"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Probieren Sie im Azure Resource Graph-Explorer die folgende Abfrage aus:

- Azure-Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ServiceHealthResources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.ResourceHealth%2fevents%27%0a%7c%20extend%20eventType%20%3d%20tostring(properties.EventType)%2c%20status%20%3d%20properties.Status%2c%20description%20%3d%20properties.Title%2c%20trackingId%20%3d%20properties.TrackingId%2c%20summary%20%3d%20properties.Summary%2c%20priority%20%3d%20properties.Priority%2c%20impactStartTime%20%3d%20properties.ImpactStartTime%2c%20impactMitigationTime%20%3d%20properties.ImpactMitigationTime%0a%7c%20where%20properties.Status%20%3d%3d%20%27Active%27%20and%20tolong(impactStartTime)%20%3e%201%0a%7c%20summarize%20count(subscriptionId)%20by%20name%2c%20eventType" target="_blank">portal.azure.com</a>
- Azure Government-Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ServiceHealthResources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.ResourceHealth%2fevents%27%0a%7c%20extend%20eventType%20%3d%20tostring(properties.EventType)%2c%20status%20%3d%20properties.Status%2c%20description%20%3d%20properties.Title%2c%20trackingId%20%3d%20properties.TrackingId%2c%20summary%20%3d%20properties.Summary%2c%20priority%20%3d%20properties.Priority%2c%20impactStartTime%20%3d%20properties.ImpactStartTime%2c%20impactMitigationTime%20%3d%20properties.ImpactMitigationTime%0a%7c%20where%20properties.Status%20%3d%3d%20%27Active%27%20and%20tolong(impactStartTime)%20%3e%201%0a%7c%20summarize%20count(subscriptionId)%20by%20name%2c%20eventType" target="_blank">portal.azure.us</a>
- Azure China 21Vianet-Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ServiceHealthResources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.ResourceHealth%2fevents%27%0a%7c%20extend%20eventType%20%3d%20tostring(properties.EventType)%2c%20status%20%3d%20properties.Status%2c%20description%20%3d%20properties.Title%2c%20trackingId%20%3d%20properties.TrackingId%2c%20summary%20%3d%20properties.Summary%2c%20priority%20%3d%20properties.Priority%2c%20impactStartTime%20%3d%20properties.ImpactStartTime%2c%20impactMitigationTime%20%3d%20properties.ImpactMitigationTime%0a%7c%20where%20properties.Status%20%3d%3d%20%27Active%27%20and%20tolong(impactStartTime)%20%3e%201%0a%7c%20summarize%20count(subscriptionId)%20by%20name%2c%20eventType" target="_blank">portal.azure.cn</a>

---

### <a name="all-active-health-advisory-events"></a>Alle aktiven Ereignisse zur Integritätsempfehlung

Die aktiven Service Health Ereignisse zur Integritätsempfehlung werden an alle Abonnements zurückgegeben, auf die der Benutzer Zugriff hat.

```kusto
ServiceHealthResources
| where type =~ 'Microsoft.ResourceHealth/events'
| extend eventType = properties.EventType, status = properties.Status, description = properties.Title, trackingId = properties.TrackingId, summary = properties.Summary, priority = properties.Priority, impactStartTime = properties.ImpactStartTime, impactMitigationTime = properties.ImpactMitigationTime
| where properties.Status == 'Active' and tolong(impactStartTime) > 1 and eventType == 'HealthAdvisory'
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
az graph query -q "ServiceHealthResources | where type =~ 'Microsoft.ResourceHealth/events' | extend eventType = properties.EventType, status = properties.Status, description = properties.Title, trackingId = properties.TrackingId, summary = properties.Summary, priority = properties.Priority, impactStartTime = properties.ImpactStartTime, impactMitigationTime = properties.ImpactMitigationTime | where properties.Status == 'Active' and tolong(impactStartTime) > 1 and eventType == 'HealthAdvisory'"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "ServiceHealthResources | where type =~ 'Microsoft.ResourceHealth/events' | extend eventType = properties.EventType, status = properties.Status, description = properties.Title, trackingId = properties.TrackingId, summary = properties.Summary, priority = properties.Priority, impactStartTime = properties.ImpactStartTime, impactMitigationTime = properties.ImpactMitigationTime | where properties.Status == 'Active' and tolong(impactStartTime) > 1 and eventType == 'HealthAdvisory'"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Probieren Sie im Azure Resource Graph-Explorer die folgende Abfrage aus:

- Azure-Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ServiceHealthResources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.ResourceHealth%2fevents%27%0a%7c%20extend%20eventType%20%3d%20properties.EventType%2c%20status%20%3d%20properties.Status%2c%20description%20%3d%20properties.Title%2c%20trackingId%20%3d%20properties.TrackingId%2c%20summary%20%3d%20properties.Summary%2c%20priority%20%3d%20properties.Priority%2c%20impactStartTime%20%3d%20properties.ImpactStartTime%2c%20impactMitigationTime%20%3d%20properties.ImpactMitigationTime%0a%7c%20where%20properties.Status%20%3d%3d%20%27Active%27%20and%20tolong(impactStartTime)%20%3e%201%20and%20eventType%20%3d%3d%20%27HealthAdvisory%27" target="_blank">portal.azure.com</a>
- Azure Government-Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ServiceHealthResources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.ResourceHealth%2fevents%27%0a%7c%20extend%20eventType%20%3d%20properties.EventType%2c%20status%20%3d%20properties.Status%2c%20description%20%3d%20properties.Title%2c%20trackingId%20%3d%20properties.TrackingId%2c%20summary%20%3d%20properties.Summary%2c%20priority%20%3d%20properties.Priority%2c%20impactStartTime%20%3d%20properties.ImpactStartTime%2c%20impactMitigationTime%20%3d%20properties.ImpactMitigationTime%0a%7c%20where%20properties.Status%20%3d%3d%20%27Active%27%20and%20tolong(impactStartTime)%20%3e%201%20and%20eventType%20%3d%3d%20%27HealthAdvisory%27" target="_blank">portal.azure.us</a>
- Azure China 21Vianet-Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ServiceHealthResources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.ResourceHealth%2fevents%27%0a%7c%20extend%20eventType%20%3d%20properties.EventType%2c%20status%20%3d%20properties.Status%2c%20description%20%3d%20properties.Title%2c%20trackingId%20%3d%20properties.TrackingId%2c%20summary%20%3d%20properties.Summary%2c%20priority%20%3d%20properties.Priority%2c%20impactStartTime%20%3d%20properties.ImpactStartTime%2c%20impactMitigationTime%20%3d%20properties.ImpactMitigationTime%0a%7c%20where%20properties.Status%20%3d%3d%20%27Active%27%20and%20tolong(impactStartTime)%20%3e%201%20and%20eventType%20%3d%3d%20%27HealthAdvisory%27" target="_blank">portal.azure.cn</a>

---

### <a name="all-active-planned-maintenance-events"></a>Alle aktiven geplante Wartungsereignisse

Alle aktiven Service Health Ereignisse zur geplanten Wartung werden an alle Abonnements zurückgegeben, auf die der Benutzer Zugriff hat.

```kusto
ServiceHealthResources
| where type =~ 'Microsoft.ResourceHealth/events'
| extend eventType = properties.EventType, status = properties.Status, description = properties.Title, trackingId = properties.TrackingId, summary = properties.Summary, priority = properties.Priority, impactStartTime = properties.ImpactStartTime, impactMitigationTime = properties.ImpactMitigationTime
| where properties.Status == 'Active' and tolong(impactStartTime) > 1 and eventType == 'PlannedMaintenance'
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
az graph query -q "ServiceHealthResources | where type =~ 'Microsoft.ResourceHealth/events' | extend eventType = properties.EventType, status = properties.Status, description = properties.Title, trackingId = properties.TrackingId, summary = properties.Summary, priority = properties.Priority, impactStartTime = properties.ImpactStartTime, impactMitigationTime = properties.ImpactMitigationTime | where properties.Status == 'Active' and tolong(impactStartTime) > 1 and eventType == 'PlannedMaintenance'"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "ServiceHealthResources | where type =~ 'Microsoft.ResourceHealth/events' | extend eventType = properties.EventType, status = properties.Status, description = properties.Title, trackingId = properties.TrackingId, summary = properties.Summary, priority = properties.Priority, impactStartTime = properties.ImpactStartTime, impactMitigationTime = properties.ImpactMitigationTime | where properties.Status == 'Active' and tolong(impactStartTime) > 1 and eventType == 'PlannedMaintenance'"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Probieren Sie im Azure Resource Graph-Explorer die folgende Abfrage aus:

- Azure-Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ServiceHealthResources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.ResourceHealth%2fevents%27%0a%7c%20extend%20eventType%20%3d%20properties.EventType%2c%20status%20%3d%20properties.Status%2c%20description%20%3d%20properties.Title%2c%20trackingId%20%3d%20properties.TrackingId%2c%20summary%20%3d%20properties.Summary%2c%20priority%20%3d%20properties.Priority%2c%20impactStartTime%20%3d%20properties.ImpactStartTime%2c%20impactMitigationTime%20%3d%20properties.ImpactMitigationTime%0a%7c%20where%20properties.Status%20%3d%3d%20%27Active%27%20and%20tolong(impactStartTime)%20%3e%201%20and%20eventType%20%3d%3d%20%27PlannedMaintenance%27" target="_blank">portal.azure.com</a>
- Azure Government-Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ServiceHealthResources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.ResourceHealth%2fevents%27%0a%7c%20extend%20eventType%20%3d%20properties.EventType%2c%20status%20%3d%20properties.Status%2c%20description%20%3d%20properties.Title%2c%20trackingId%20%3d%20properties.TrackingId%2c%20summary%20%3d%20properties.Summary%2c%20priority%20%3d%20properties.Priority%2c%20impactStartTime%20%3d%20properties.ImpactStartTime%2c%20impactMitigationTime%20%3d%20properties.ImpactMitigationTime%0a%7c%20where%20properties.Status%20%3d%3d%20%27Active%27%20and%20tolong(impactStartTime)%20%3e%201%20and%20eventType%20%3d%3d%20%27PlannedMaintenance%27" target="_blank">portal.azure.us</a>
- Azure China 21Vianet-Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ServiceHealthResources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.ResourceHealth%2fevents%27%0a%7c%20extend%20eventType%20%3d%20properties.EventType%2c%20status%20%3d%20properties.Status%2c%20description%20%3d%20properties.Title%2c%20trackingId%20%3d%20properties.TrackingId%2c%20summary%20%3d%20properties.Summary%2c%20priority%20%3d%20properties.Priority%2c%20impactStartTime%20%3d%20properties.ImpactStartTime%2c%20impactMitigationTime%20%3d%20properties.ImpactMitigationTime%0a%7c%20where%20properties.Status%20%3d%3d%20%27Active%27%20and%20tolong(impactStartTime)%20%3e%201%20and%20eventType%20%3d%3d%20%27PlannedMaintenance%27" target="_blank">portal.azure.cn</a>

---

### <a name="all-active-service-health-events"></a>Alle aktiven Service Health Ereignisse

Die aktiven Service Health Ereignisse werden an alle Abonnements zurückgegeben, auf die der Benutzer Zugriff hat, einschließlich Dienstproblemen, geplanter Wartung, Integritätsempfehlungen und Sicherheitsempfehlungen.

```kusto
ServiceHealthResources
| where type =~ 'Microsoft.ResourceHealth/events'
| extend eventType = properties.EventType, status = properties.Status, description = properties.Title, trackingId = properties.TrackingId, summary = properties.Summary, priority = properties.Priority, impactStartTime = properties.ImpactStartTime, impactMitigationTime = properties.ImpactMitigationTime
| where properties.Status == 'Active' and tolong(impactStartTime) > 1
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
az graph query -q "ServiceHealthResources | where type =~ 'Microsoft.ResourceHealth/events' | extend eventType = properties.EventType, status = properties.Status, description = properties.Title, trackingId = properties.TrackingId, summary = properties.Summary, priority = properties.Priority, impactStartTime = properties.ImpactStartTime, impactMitigationTime = properties.ImpactMitigationTime | where properties.Status == 'Active' and tolong(impactStartTime) > 1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "ServiceHealthResources | where type =~ 'Microsoft.ResourceHealth/events' | extend eventType = properties.EventType, status = properties.Status, description = properties.Title, trackingId = properties.TrackingId, summary = properties.Summary, priority = properties.Priority, impactStartTime = properties.ImpactStartTime, impactMitigationTime = properties.ImpactMitigationTime | where properties.Status == 'Active' and tolong(impactStartTime) > 1"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Probieren Sie im Azure Resource Graph-Explorer die folgende Abfrage aus:

- Azure-Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ServiceHealthResources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.ResourceHealth%2fevents%27%0a%7c%20extend%20eventType%20%3d%20properties.EventType%2c%20status%20%3d%20properties.Status%2c%20description%20%3d%20properties.Title%2c%20trackingId%20%3d%20properties.TrackingId%2c%20summary%20%3d%20properties.Summary%2c%20priority%20%3d%20properties.Priority%2c%20impactStartTime%20%3d%20properties.ImpactStartTime%2c%20impactMitigationTime%20%3d%20properties.ImpactMitigationTime%0a%7c%20where%20properties.Status%20%3d%3d%20%27Active%27%20and%20tolong(impactStartTime)%20%3e%201" target="_blank">portal.azure.com</a>
- Azure Government-Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ServiceHealthResources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.ResourceHealth%2fevents%27%0a%7c%20extend%20eventType%20%3d%20properties.EventType%2c%20status%20%3d%20properties.Status%2c%20description%20%3d%20properties.Title%2c%20trackingId%20%3d%20properties.TrackingId%2c%20summary%20%3d%20properties.Summary%2c%20priority%20%3d%20properties.Priority%2c%20impactStartTime%20%3d%20properties.ImpactStartTime%2c%20impactMitigationTime%20%3d%20properties.ImpactMitigationTime%0a%7c%20where%20properties.Status%20%3d%3d%20%27Active%27%20and%20tolong(impactStartTime)%20%3e%201" target="_blank">portal.azure.us</a>
- Azure China 21Vianet-Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ServiceHealthResources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.ResourceHealth%2fevents%27%0a%7c%20extend%20eventType%20%3d%20properties.EventType%2c%20status%20%3d%20properties.Status%2c%20description%20%3d%20properties.Title%2c%20trackingId%20%3d%20properties.TrackingId%2c%20summary%20%3d%20properties.Summary%2c%20priority%20%3d%20properties.Priority%2c%20impactStartTime%20%3d%20properties.ImpactStartTime%2c%20impactMitigationTime%20%3d%20properties.ImpactMitigationTime%0a%7c%20where%20properties.Status%20%3d%3d%20%27Active%27%20and%20tolong(impactStartTime)%20%3e%201" target="_blank">portal.azure.cn</a>

---

### <a name="all-active-service-issue-events"></a>Alle aktiven Dienstproblemereignisse

Die aktiven Service Health Ereignisse zu Dienstproblemen (Ausfälle) werden an alle Abonnements zurückgegeben, auf die der Benutzer Zugriff hat.

```kusto
ServiceHealthResources
| where type =~ 'Microsoft.ResourceHealth/events'
| extend eventType = properties.EventType, status = properties.Status, description = properties.Title, trackingId = properties.TrackingId, summary = properties.Summary, priority = properties.Priority, impactStartTime = properties.ImpactStartTime, impactMitigationTime = properties.ImpactMitigationTime
| where properties.Status == 'Active' and tolong(impactStartTime) > 1 and eventType == 'ServiceIssue'
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
az graph query -q "ServiceHealthResources | where type =~ 'Microsoft.ResourceHealth/events' | extend eventType = properties.EventType, status = properties.Status, description = properties.Title, trackingId = properties.TrackingId, summary = properties.Summary, priority = properties.Priority, impactStartTime = properties.ImpactStartTime, impactMitigationTime = properties.ImpactMitigationTime | where properties.Status == 'Active' and tolong(impactStartTime) > 1 and eventType == 'ServiceIssue'"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "ServiceHealthResources | where type =~ 'Microsoft.ResourceHealth/events' | extend eventType = properties.EventType, status = properties.Status, description = properties.Title, trackingId = properties.TrackingId, summary = properties.Summary, priority = properties.Priority, impactStartTime = properties.ImpactStartTime, impactMitigationTime = properties.ImpactMitigationTime | where properties.Status == 'Active' and tolong(impactStartTime) > 1 and eventType == 'ServiceIssue'"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Probieren Sie im Azure Resource Graph-Explorer die folgende Abfrage aus:

- Azure-Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ServiceHealthResources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.ResourceHealth%2fevents%27%0a%7c%20extend%20eventType%20%3d%20properties.EventType%2c%20status%20%3d%20properties.Status%2c%20description%20%3d%20properties.Title%2c%20trackingId%20%3d%20properties.TrackingId%2c%20summary%20%3d%20properties.Summary%2c%20priority%20%3d%20properties.Priority%2c%20impactStartTime%20%3d%20properties.ImpactStartTime%2c%20impactMitigationTime%20%3d%20properties.ImpactMitigationTime%0a%7c%20where%20properties.Status%20%3d%3d%20%27Active%27%20and%20tolong(impactStartTime)%20%3e%201%20and%20eventType%20%3d%3d%20%27ServiceIssue%27" target="_blank">portal.azure.com</a>
- Azure Government-Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ServiceHealthResources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.ResourceHealth%2fevents%27%0a%7c%20extend%20eventType%20%3d%20properties.EventType%2c%20status%20%3d%20properties.Status%2c%20description%20%3d%20properties.Title%2c%20trackingId%20%3d%20properties.TrackingId%2c%20summary%20%3d%20properties.Summary%2c%20priority%20%3d%20properties.Priority%2c%20impactStartTime%20%3d%20properties.ImpactStartTime%2c%20impactMitigationTime%20%3d%20properties.ImpactMitigationTime%0a%7c%20where%20properties.Status%20%3d%3d%20%27Active%27%20and%20tolong(impactStartTime)%20%3e%201%20and%20eventType%20%3d%3d%20%27ServiceIssue%27" target="_blank">portal.azure.us</a>
- Azure China 21Vianet-Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ServiceHealthResources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.ResourceHealth%2fevents%27%0a%7c%20extend%20eventType%20%3d%20properties.EventType%2c%20status%20%3d%20properties.Status%2c%20description%20%3d%20properties.Title%2c%20trackingId%20%3d%20properties.TrackingId%2c%20summary%20%3d%20properties.Summary%2c%20priority%20%3d%20properties.Priority%2c%20impactStartTime%20%3d%20properties.ImpactStartTime%2c%20impactMitigationTime%20%3d%20properties.ImpactMitigationTime%0a%7c%20where%20properties.Status%20%3d%3d%20%27Active%27%20and%20tolong(impactStartTime)%20%3e%201%20and%20eventType%20%3d%3d%20%27ServiceIssue%27" target="_blank">portal.azure.cn</a>

---

