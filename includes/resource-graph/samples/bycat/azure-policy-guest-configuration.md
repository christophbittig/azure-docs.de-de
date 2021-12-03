---
author: georgewallace
ms.service: resource-graph
ms.topic: include
ms.date: 10/12/2021
ms.author: gwallace
ms.custom: generated
ms.openlocfilehash: 5a9a8a7d007352dbf14026a29f101cc5b8d357c1
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132060158"
---
### <a name="count-machines-in-scope-of-guest-configuration-policies"></a>Anzahl von Computern im Bereich der Gastkonfigurationsrichtlinien

Zeigt die Anzahl der virtuellen Azure-Computer und der mit Arc verbundenen Server im Bereich der Zuweisungen für [Azure Policy-Gastkonfiguration](../../../../articles/governance/policy/concepts/guest-configuration.md) an.

```kusto
GuestConfigurationResources
| where type =~ 'microsoft.guestconfiguration/guestconfigurationassignments'
| extend vmid = split(properties.targetResourceId,'/')
| mvexpand properties.latestAssignmentReport.resources
| where properties_latestAssignmentReport_resources.resourceId != 'Invalid assignment package.'
| project machine = tostring(vmid[(-1)]),type = tostring(vmid[(-3)])
| distinct machine, type
| summarize count() by type
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
az graph query -q "GuestConfigurationResources | where type =~ 'microsoft.guestconfiguration/guestconfigurationassignments' | extend vmid = split(properties.targetResourceId,'/') | mvexpand properties.latestAssignmentReport.resources | where properties_latestAssignmentReport_resources.resourceId != 'Invalid assignment package.' | project machine = tostring(vmid[(-1)]),type = tostring(vmid[(-3)]) | distinct machine, type | summarize count() by type"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "GuestConfigurationResources | where type =~ 'microsoft.guestconfiguration/guestconfigurationassignments' | extend vmid = split(properties.targetResourceId,'/') | mvexpand properties.latestAssignmentReport.resources | where properties_latestAssignmentReport_resources.resourceId != 'Invalid assignment package.' | project machine = tostring(vmid[(-1)]),type = tostring(vmid[(-3)]) | distinct machine, type | summarize count() by type"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Probieren Sie im Azure Resource Graph-Explorer die folgende Abfrage aus:

- Azure-Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/GuestConfigurationResources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.guestconfiguration%2fguestconfigurationassignments%27%0a%7c%20extend%20vmid%20%3d%20split(properties.targetResourceId%2c%27%2f%27)%0a%7c%20mvexpand%20properties.latestAssignmentReport.resources%0a%7c%20where%20properties_latestAssignmentReport_resources.resourceId%20!%3d%20%27Invalid%20assignment%20package.%27%0a%7c%20project%20machine%20%3d%20tostring(vmid%5b(-1)%5d)%2ctype%20%3d%20tostring(vmid%5b(-3)%5d)%0a%7c%20distinct%20machine%2c%20type%0a%7c%20summarize%20count()%20by%20type" target="_blank">portal.azure.com</a>
- Azure Government-Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/GuestConfigurationResources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.guestconfiguration%2fguestconfigurationassignments%27%0a%7c%20extend%20vmid%20%3d%20split(properties.targetResourceId%2c%27%2f%27)%0a%7c%20mvexpand%20properties.latestAssignmentReport.resources%0a%7c%20where%20properties_latestAssignmentReport_resources.resourceId%20!%3d%20%27Invalid%20assignment%20package.%27%0a%7c%20project%20machine%20%3d%20tostring(vmid%5b(-1)%5d)%2ctype%20%3d%20tostring(vmid%5b(-3)%5d)%0a%7c%20distinct%20machine%2c%20type%0a%7c%20summarize%20count()%20by%20type" target="_blank">portal.azure.us</a>
- Azure China 21Vianet-Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/GuestConfigurationResources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.guestconfiguration%2fguestconfigurationassignments%27%0a%7c%20extend%20vmid%20%3d%20split(properties.targetResourceId%2c%27%2f%27)%0a%7c%20mvexpand%20properties.latestAssignmentReport.resources%0a%7c%20where%20properties_latestAssignmentReport_resources.resourceId%20!%3d%20%27Invalid%20assignment%20package.%27%0a%7c%20project%20machine%20%3d%20tostring(vmid%5b(-1)%5d)%2ctype%20%3d%20tostring(vmid%5b(-3)%5d)%0a%7c%20distinct%20machine%2c%20type%0a%7c%20summarize%20count()%20by%20type" target="_blank">portal.azure.cn</a>

---

### <a name="count-of-non-compliant-guest-configuration-assignments"></a>Anzahl der nicht konformen Gastkonfigurationszuweisungen

Zeigt die Anzahl der nicht kompatiblen Computer pro [Gastkonfigurationszuweisungs-Grund](../../../../articles/governance/policy/how-to/determine-non-compliance.md#compliance-details-for-guest-configuration). Die Ergebnisse werden aus Leistungsgründen auf die ersten 100 beschränkt.

```kusto
GuestConfigurationResources
| where type =~ 'microsoft.guestconfiguration/guestconfigurationassignments'
| project id, name, resources = properties.latestAssignmentReport.resources, vmid = split(properties.targetResourceId,'/')[(-1)], status = tostring(properties.complianceStatus)
| extend resources = iff(isnull(resources[0]), dynamic([{}]), resources)
| mvexpand resources
| extend reasons = resources.reasons
| extend reasons = iff(isnull(reasons[0]), dynamic([{}]), reasons)
| mvexpand reasons
| project id, vmid, name, status, resource = tostring(resources.resourceId), reason = reasons.phrase
| summarize count() by resource, name
| order by count_
| limit 100
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
az graph query -q "GuestConfigurationResources | where type =~ 'microsoft.guestconfiguration/guestconfigurationassignments' | project id, name, resources = properties.latestAssignmentReport.resources, vmid = split(properties.targetResourceId,'/')[(-1)], status = tostring(properties.complianceStatus) | extend resources = iff(isnull(resources[0]), dynamic([{}]), resources) | mvexpand resources | extend reasons = resources.reasons | extend reasons = iff(isnull(reasons[0]), dynamic([{}]), reasons) | mvexpand reasons | project id, vmid, name, status, resource = tostring(resources.resourceId), reason = reasons.phrase | summarize count() by resource, name | order by count_ | limit 100"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "GuestConfigurationResources | where type =~ 'microsoft.guestconfiguration/guestconfigurationassignments' | project id, name, resources = properties.latestAssignmentReport.resources, vmid = split(properties.targetResourceId,'/')[(-1)], status = tostring(properties.complianceStatus) | extend resources = iff(isnull(resources[0]), dynamic([{}]), resources) | mvexpand resources | extend reasons = resources.reasons | extend reasons = iff(isnull(reasons[0]), dynamic([{}]), reasons) | mvexpand reasons | project id, vmid, name, status, resource = tostring(resources.resourceId), reason = reasons.phrase | summarize count() by resource, name | order by count_ | limit 100"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Probieren Sie im Azure Resource Graph-Explorer die folgende Abfrage aus:

- Azure-Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/GuestConfigurationResources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.guestconfiguration%2fguestconfigurationassignments%27%0a%7c%20project%20id%2c%20name%2c%20resources%20%3d%20properties.latestAssignmentReport.resources%2c%20vmid%20%3d%20split(properties.targetResourceId%2c%27%2f%27)%5b(-1)%5d%2c%20status%20%3d%20tostring(properties.complianceStatus)%0a%7c%20extend%20resources%20%3d%20iff(isnull(resources%5b0%5d)%2c%20dynamic(%5b%7b%7d%5d)%2c%20resources)%0a%7c%20mvexpand%20resources%0a%7c%20extend%20reasons%20%3d%20resources.reasons%0a%7c%20extend%20reasons%20%3d%20iff(isnull(reasons%5b0%5d)%2c%20dynamic(%5b%7b%7d%5d)%2c%20reasons)%0a%7c%20mvexpand%20reasons%0a%7c%20project%20id%2c%20vmid%2c%20name%2c%20status%2c%20resource%20%3d%20tostring(resources.resourceId)%2c%20reason%20%3d%20reasons.phrase%0a%7c%20summarize%20count()%20by%20resource%2c%20name%0a%7c%20order%20by%20count_%0a%7c%20limit%20100" target="_blank">portal.azure.com</a>
- Azure Government-Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/GuestConfigurationResources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.guestconfiguration%2fguestconfigurationassignments%27%0a%7c%20project%20id%2c%20name%2c%20resources%20%3d%20properties.latestAssignmentReport.resources%2c%20vmid%20%3d%20split(properties.targetResourceId%2c%27%2f%27)%5b(-1)%5d%2c%20status%20%3d%20tostring(properties.complianceStatus)%0a%7c%20extend%20resources%20%3d%20iff(isnull(resources%5b0%5d)%2c%20dynamic(%5b%7b%7d%5d)%2c%20resources)%0a%7c%20mvexpand%20resources%0a%7c%20extend%20reasons%20%3d%20resources.reasons%0a%7c%20extend%20reasons%20%3d%20iff(isnull(reasons%5b0%5d)%2c%20dynamic(%5b%7b%7d%5d)%2c%20reasons)%0a%7c%20mvexpand%20reasons%0a%7c%20project%20id%2c%20vmid%2c%20name%2c%20status%2c%20resource%20%3d%20tostring(resources.resourceId)%2c%20reason%20%3d%20reasons.phrase%0a%7c%20summarize%20count()%20by%20resource%2c%20name%0a%7c%20order%20by%20count_%0a%7c%20limit%20100" target="_blank">portal.azure.us</a>
- Azure China 21Vianet-Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/GuestConfigurationResources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.guestconfiguration%2fguestconfigurationassignments%27%0a%7c%20project%20id%2c%20name%2c%20resources%20%3d%20properties.latestAssignmentReport.resources%2c%20vmid%20%3d%20split(properties.targetResourceId%2c%27%2f%27)%5b(-1)%5d%2c%20status%20%3d%20tostring(properties.complianceStatus)%0a%7c%20extend%20resources%20%3d%20iff(isnull(resources%5b0%5d)%2c%20dynamic(%5b%7b%7d%5d)%2c%20resources)%0a%7c%20mvexpand%20resources%0a%7c%20extend%20reasons%20%3d%20resources.reasons%0a%7c%20extend%20reasons%20%3d%20iff(isnull(reasons%5b0%5d)%2c%20dynamic(%5b%7b%7d%5d)%2c%20reasons)%0a%7c%20mvexpand%20reasons%0a%7c%20project%20id%2c%20vmid%2c%20name%2c%20status%2c%20resource%20%3d%20tostring(resources.resourceId)%2c%20reason%20%3d%20reasons.phrase%0a%7c%20summarize%20count()%20by%20resource%2c%20name%0a%7c%20order%20by%20count_%0a%7c%20limit%20100" target="_blank">portal.azure.cn</a>

---

### <a name="find-all-reasons-a-machine-is-non-compliant-for-guest-configuration-assignments"></a>Ermitteln aller Gründe dafür, warum ein Computer nicht konform für Gastkonfigurationszuweisungen ist

Zeigt alle [Gastkonfigurationszuweisungs-Gründe](../../../../articles/governance/policy/how-to/determine-non-compliance.md#compliance-details-for-guest-configuration) für einen bestimmten Computer an. Entfernen Sie die erste `where`-Klausel, um auch Überwachungen mit konformen Computern einzubeziehen.

```kusto
GuestConfigurationResources
| where type =~ 'microsoft.guestconfiguration/guestconfigurationassignments'
| where properties.complianceStatus == 'NonCompliant'
| project id, name, resources = properties.latestAssignmentReport.resources, machine = split(properties.targetResourceId,'/')[(-1)], status = tostring(properties.complianceStatus)
| extend resources = iff(isnull(resources[0]), dynamic([{}]), resources)
| mvexpand resources
| extend reasons = resources.reasons
| extend reasons = iff(isnull(reasons[0]), dynamic([{}]), reasons)
| mvexpand reasons
| where machine == 'MACHINENAME'
| project id, machine, name, status, resource = resources.resourceId, reason = reasons.phrase
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
az graph query -q "GuestConfigurationResources | where type =~ 'microsoft.guestconfiguration/guestconfigurationassignments' | where properties.complianceStatus == 'NonCompliant' | project id, name, resources = properties.latestAssignmentReport.resources, machine = split(properties.targetResourceId,'/')[(-1)], status = tostring(properties.complianceStatus) | extend resources = iff(isnull(resources[0]), dynamic([{}]), resources) | mvexpand resources | extend reasons = resources.reasons | extend reasons = iff(isnull(reasons[0]), dynamic([{}]), reasons) | mvexpand reasons | where machine == 'MACHINENAME' | project id, machine, name, status, resource = resources.resourceId, reason = reasons.phrase"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "GuestConfigurationResources | where type =~ 'microsoft.guestconfiguration/guestconfigurationassignments' | where properties.complianceStatus == 'NonCompliant' | project id, name, resources = properties.latestAssignmentReport.resources, machine = split(properties.targetResourceId,'/')[(-1)], status = tostring(properties.complianceStatus) | extend resources = iff(isnull(resources[0]), dynamic([{}]), resources) | mvexpand resources | extend reasons = resources.reasons | extend reasons = iff(isnull(reasons[0]), dynamic([{}]), reasons) | mvexpand reasons | where machine == 'MACHINENAME' | project id, machine, name, status, resource = resources.resourceId, reason = reasons.phrase"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Probieren Sie im Azure Resource Graph-Explorer die folgende Abfrage aus:

- Azure-Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/GuestConfigurationResources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.guestconfiguration%2fguestconfigurationassignments%27%0a%7c%20where%20properties.complianceStatus%20%3d%3d%20%27NonCompliant%27%0a%7c%20project%20id%2c%20name%2c%20resources%20%3d%20properties.latestAssignmentReport.resources%2c%20machine%20%3d%20split(properties.targetResourceId%2c%27%2f%27)%5b(-1)%5d%2c%20status%20%3d%20tostring(properties.complianceStatus)%0a%7c%20extend%20resources%20%3d%20iff(isnull(resources%5b0%5d)%2c%20dynamic(%5b%7b%7d%5d)%2c%20resources)%0a%7c%20mvexpand%20resources%0a%7c%20extend%20reasons%20%3d%20resources.reasons%0a%7c%20extend%20reasons%20%3d%20iff(isnull(reasons%5b0%5d)%2c%20dynamic(%5b%7b%7d%5d)%2c%20reasons)%0a%7c%20mvexpand%20reasons%0a%7c%20where%20machine%20%3d%3d%20%27MACHINENAME%27%0a%7c%20project%20id%2c%20machine%2c%20name%2c%20status%2c%20resource%20%3d%20resources.resourceId%2c%20reason%20%3d%20reasons.phrase" target="_blank">portal.azure.com</a>
- Azure Government-Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/GuestConfigurationResources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.guestconfiguration%2fguestconfigurationassignments%27%0a%7c%20where%20properties.complianceStatus%20%3d%3d%20%27NonCompliant%27%0a%7c%20project%20id%2c%20name%2c%20resources%20%3d%20properties.latestAssignmentReport.resources%2c%20machine%20%3d%20split(properties.targetResourceId%2c%27%2f%27)%5b(-1)%5d%2c%20status%20%3d%20tostring(properties.complianceStatus)%0a%7c%20extend%20resources%20%3d%20iff(isnull(resources%5b0%5d)%2c%20dynamic(%5b%7b%7d%5d)%2c%20resources)%0a%7c%20mvexpand%20resources%0a%7c%20extend%20reasons%20%3d%20resources.reasons%0a%7c%20extend%20reasons%20%3d%20iff(isnull(reasons%5b0%5d)%2c%20dynamic(%5b%7b%7d%5d)%2c%20reasons)%0a%7c%20mvexpand%20reasons%0a%7c%20where%20machine%20%3d%3d%20%27MACHINENAME%27%0a%7c%20project%20id%2c%20machine%2c%20name%2c%20status%2c%20resource%20%3d%20resources.resourceId%2c%20reason%20%3d%20reasons.phrase" target="_blank">portal.azure.us</a>
- Azure China 21Vianet-Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/GuestConfigurationResources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.guestconfiguration%2fguestconfigurationassignments%27%0a%7c%20where%20properties.complianceStatus%20%3d%3d%20%27NonCompliant%27%0a%7c%20project%20id%2c%20name%2c%20resources%20%3d%20properties.latestAssignmentReport.resources%2c%20machine%20%3d%20split(properties.targetResourceId%2c%27%2f%27)%5b(-1)%5d%2c%20status%20%3d%20tostring(properties.complianceStatus)%0a%7c%20extend%20resources%20%3d%20iff(isnull(resources%5b0%5d)%2c%20dynamic(%5b%7b%7d%5d)%2c%20resources)%0a%7c%20mvexpand%20resources%0a%7c%20extend%20reasons%20%3d%20resources.reasons%0a%7c%20extend%20reasons%20%3d%20iff(isnull(reasons%5b0%5d)%2c%20dynamic(%5b%7b%7d%5d)%2c%20reasons)%0a%7c%20mvexpand%20reasons%0a%7c%20where%20machine%20%3d%3d%20%27MACHINENAME%27%0a%7c%20project%20id%2c%20machine%2c%20name%2c%20status%2c%20resource%20%3d%20resources.resourceId%2c%20reason%20%3d%20reasons.phrase" target="_blank">portal.azure.cn</a>

---

### <a name="list-machines-and-status-of-pending-reboot"></a>Auflisten von Computern und Status des ausstehenden Neustarts

Stellt eine Liste der Computer mit Konfigurationsdetails dazu bereit, ob ein Neustart für sie aussteht.

```kusto
GuestConfigurationResources
| where name in ('WindowsPendingReboot')
| project id, name, resources = properties.latestAssignmentReport.resources, vmid = split(properties.targetResourceId,'/'), status = tostring(properties.complianceStatus)
| extend resources = iff(isnull(resources[0]), dynamic([{}]), resources)
| mvexpand resources
| extend reasons = resources.reasons
| extend reasons = iff(isnull(reasons[0]), dynamic([{}]), reasons)
| mvexpand reasons
| project id, vmid, name, status, resource = resources.resourceId, reason = reasons.phrase
| summarize name = any(name), status = any(status), vmid = any(vmid), resources = make_list_if(resource, isnotnull(resource)), reasons = make_list_if(reason, isnotnull(reason)) by id = tolower(id)
| project id, machine = tostring(vmid[(-1)]), type = tostring(vmid[(-3)]), name, status, reasons
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
az graph query -q "GuestConfigurationResources | where name in ('WindowsPendingReboot') | project id, name, resources = properties.latestAssignmentReport.resources, vmid = split(properties.targetResourceId,'/'), status = tostring(properties.complianceStatus) | extend resources = iff(isnull(resources[0]), dynamic([{}]), resources) | mvexpand resources | extend reasons = resources.reasons | extend reasons = iff(isnull(reasons[0]), dynamic([{}]), reasons) | mvexpand reasons | project id, vmid, name, status, resource = resources.resourceId, reason = reasons.phrase | summarize name = any(name), status = any(status), vmid = any(vmid), resources = make_list_if(resource, isnotnull(resource)), reasons = make_list_if(reason, isnotnull(reason)) by id = tolower(id) | project id, machine = tostring(vmid[(-1)]), type = tostring(vmid[(-3)]), name, status, reasons"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "GuestConfigurationResources | where name in ('WindowsPendingReboot') | project id, name, resources = properties.latestAssignmentReport.resources, vmid = split(properties.targetResourceId,'/'), status = tostring(properties.complianceStatus) | extend resources = iff(isnull(resources[0]), dynamic([{}]), resources) | mvexpand resources | extend reasons = resources.reasons | extend reasons = iff(isnull(reasons[0]), dynamic([{}]), reasons) | mvexpand reasons | project id, vmid, name, status, resource = resources.resourceId, reason = reasons.phrase | summarize name = any(name), status = any(status), vmid = any(vmid), resources = make_list_if(resource, isnotnull(resource)), reasons = make_list_if(reason, isnotnull(reason)) by id = tolower(id) | project id, machine = tostring(vmid[(-1)]), type = tostring(vmid[(-3)]), name, status, reasons"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Probieren Sie im Azure Resource Graph-Explorer die folgende Abfrage aus:

- Azure-Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/GuestConfigurationResources%0a%7c%20where%20name%20in%20(%27WindowsPendingReboot%27)%0a%7c%20project%20id%2c%20name%2c%20resources%20%3d%20properties.latestAssignmentReport.resources%2c%20vmid%20%3d%20split(properties.targetResourceId%2c%27%2f%27)%2c%20status%20%3d%20tostring(properties.complianceStatus)%0a%7c%20extend%20resources%20%3d%20iff(isnull(resources%5b0%5d)%2c%20dynamic(%5b%7b%7d%5d)%2c%20resources)%0a%7c%20mvexpand%20resources%0a%7c%20extend%20reasons%20%3d%20resources.reasons%0a%7c%20extend%20reasons%20%3d%20iff(isnull(reasons%5b0%5d)%2c%20dynamic(%5b%7b%7d%5d)%2c%20reasons)%0a%7c%20mvexpand%20reasons%0a%7c%20project%20id%2c%20vmid%2c%20name%2c%20status%2c%20resource%20%3d%20resources.resourceId%2c%20reason%20%3d%20reasons.phrase%0a%7c%20summarize%20name%20%3d%20any(name)%2c%20status%20%3d%20any(status)%2c%20vmid%20%3d%20any(vmid)%2c%20resources%20%3d%20make_list_if(resource%2c%20isnotnull(resource))%2c%20reasons%20%3d%20make_list_if(reason%2c%20isnotnull(reason))%20by%20id%20%3d%20tolower(id)%0a%7c%20project%20id%2c%20machine%20%3d%20tostring(vmid%5b(-1)%5d)%2c%20type%20%3d%20tostring(vmid%5b(-3)%5d)%2c%20name%2c%20status%2c%20reasons" target="_blank">portal.azure.com</a>
- Azure Government-Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/GuestConfigurationResources%0a%7c%20where%20name%20in%20(%27WindowsPendingReboot%27)%0a%7c%20project%20id%2c%20name%2c%20resources%20%3d%20properties.latestAssignmentReport.resources%2c%20vmid%20%3d%20split(properties.targetResourceId%2c%27%2f%27)%2c%20status%20%3d%20tostring(properties.complianceStatus)%0a%7c%20extend%20resources%20%3d%20iff(isnull(resources%5b0%5d)%2c%20dynamic(%5b%7b%7d%5d)%2c%20resources)%0a%7c%20mvexpand%20resources%0a%7c%20extend%20reasons%20%3d%20resources.reasons%0a%7c%20extend%20reasons%20%3d%20iff(isnull(reasons%5b0%5d)%2c%20dynamic(%5b%7b%7d%5d)%2c%20reasons)%0a%7c%20mvexpand%20reasons%0a%7c%20project%20id%2c%20vmid%2c%20name%2c%20status%2c%20resource%20%3d%20resources.resourceId%2c%20reason%20%3d%20reasons.phrase%0a%7c%20summarize%20name%20%3d%20any(name)%2c%20status%20%3d%20any(status)%2c%20vmid%20%3d%20any(vmid)%2c%20resources%20%3d%20make_list_if(resource%2c%20isnotnull(resource))%2c%20reasons%20%3d%20make_list_if(reason%2c%20isnotnull(reason))%20by%20id%20%3d%20tolower(id)%0a%7c%20project%20id%2c%20machine%20%3d%20tostring(vmid%5b(-1)%5d)%2c%20type%20%3d%20tostring(vmid%5b(-3)%5d)%2c%20name%2c%20status%2c%20reasons" target="_blank">portal.azure.us</a>
- Azure China 21Vianet-Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/GuestConfigurationResources%0a%7c%20where%20name%20in%20(%27WindowsPendingReboot%27)%0a%7c%20project%20id%2c%20name%2c%20resources%20%3d%20properties.latestAssignmentReport.resources%2c%20vmid%20%3d%20split(properties.targetResourceId%2c%27%2f%27)%2c%20status%20%3d%20tostring(properties.complianceStatus)%0a%7c%20extend%20resources%20%3d%20iff(isnull(resources%5b0%5d)%2c%20dynamic(%5b%7b%7d%5d)%2c%20resources)%0a%7c%20mvexpand%20resources%0a%7c%20extend%20reasons%20%3d%20resources.reasons%0a%7c%20extend%20reasons%20%3d%20iff(isnull(reasons%5b0%5d)%2c%20dynamic(%5b%7b%7d%5d)%2c%20reasons)%0a%7c%20mvexpand%20reasons%0a%7c%20project%20id%2c%20vmid%2c%20name%2c%20status%2c%20resource%20%3d%20resources.resourceId%2c%20reason%20%3d%20reasons.phrase%0a%7c%20summarize%20name%20%3d%20any(name)%2c%20status%20%3d%20any(status)%2c%20vmid%20%3d%20any(vmid)%2c%20resources%20%3d%20make_list_if(resource%2c%20isnotnull(resource))%2c%20reasons%20%3d%20make_list_if(reason%2c%20isnotnull(reason))%20by%20id%20%3d%20tolower(id)%0a%7c%20project%20id%2c%20machine%20%3d%20tostring(vmid%5b(-1)%5d)%2c%20type%20%3d%20tostring(vmid%5b(-3)%5d)%2c%20name%2c%20status%2c%20reasons" target="_blank">portal.azure.cn</a>

---

### <a name="list-machines-that-are-not-running-and-the-last-compliance-status"></a>Auflisten von nicht ausgeführten Computern und des letzten Konformitätsstatus

Stellt eine Liste der nicht eingeschalteten Computer mit ihren Konfigurationszuweisungen und dem zuletzt gemeldeten Konformitätsstatus bereit.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| where properties.extended.instanceView.powerState.code != 'PowerState/running'
| project vmName = name, power = properties.extended.instanceView.powerState.code
| join kind = leftouter (GuestConfigurationResources
    | extend vmName = tostring(split(properties.targetResourceId,'/')[(-1)])
    | project vmName, name, compliance = properties.complianceStatus) on vmName | project-away vmName1
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | where properties.extended.instanceView.powerState.code != 'PowerState/running' | project vmName = name, power = properties.extended.instanceView.powerState.code | join kind = leftouter (GuestConfigurationResources | extend vmName = tostring(split(properties.targetResourceId,'/')[(-1)]) | project vmName, name, compliance = properties.complianceStatus) on vmName | project-away vmName1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | where properties.extended.instanceView.powerState.code != 'PowerState/running' | project vmName = name, power = properties.extended.instanceView.powerState.code | join kind = leftouter (GuestConfigurationResources | extend vmName = tostring(split(properties.targetResourceId,'/')[(-1)]) | project vmName, name, compliance = properties.complianceStatus) on vmName | project-away vmName1"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Probieren Sie im Azure Resource Graph-Explorer die folgende Abfrage aus:

- Azure-Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Compute%2fvirtualMachines%27%0a%7c%20where%20properties.extended.instanceView.powerState.code%20!%3d%20%27PowerState%2frunning%27%0a%7c%20project%20vmName%20%3d%20name%2c%20power%20%3d%20properties.extended.instanceView.powerState.code%0a%7c%20join%20kind%20%3d%20leftouter%20(GuestConfigurationResources%0a%09%7c%20extend%20vmName%20%3d%20tostring(split(properties.targetResourceId%2c%27%2f%27)%5b(-1)%5d)%0a%09%7c%20project%20vmName%2c%20name%2c%20compliance%20%3d%20properties.complianceStatus)%20on%20vmName%20%7c%20project-away%20vmName1" target="_blank">portal.azure.com</a>
- Azure Government-Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Compute%2fvirtualMachines%27%0a%7c%20where%20properties.extended.instanceView.powerState.code%20!%3d%20%27PowerState%2frunning%27%0a%7c%20project%20vmName%20%3d%20name%2c%20power%20%3d%20properties.extended.instanceView.powerState.code%0a%7c%20join%20kind%20%3d%20leftouter%20(GuestConfigurationResources%0a%09%7c%20extend%20vmName%20%3d%20tostring(split(properties.targetResourceId%2c%27%2f%27)%5b(-1)%5d)%0a%09%7c%20project%20vmName%2c%20name%2c%20compliance%20%3d%20properties.complianceStatus)%20on%20vmName%20%7c%20project-away%20vmName1" target="_blank">portal.azure.us</a>
- Azure China 21Vianet-Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Compute%2fvirtualMachines%27%0a%7c%20where%20properties.extended.instanceView.powerState.code%20!%3d%20%27PowerState%2frunning%27%0a%7c%20project%20vmName%20%3d%20name%2c%20power%20%3d%20properties.extended.instanceView.powerState.code%0a%7c%20join%20kind%20%3d%20leftouter%20(GuestConfigurationResources%0a%09%7c%20extend%20vmName%20%3d%20tostring(split(properties.targetResourceId%2c%27%2f%27)%5b(-1)%5d)%0a%09%7c%20project%20vmName%2c%20name%2c%20compliance%20%3d%20properties.complianceStatus)%20on%20vmName%20%7c%20project-away%20vmName1" target="_blank">portal.azure.cn</a>

---

### <a name="query-details-of-guest-configuration-assignment-reports"></a>Abfragen von Details aus den Berichten zur Gastkonfigurationszuweisung

Zeigt den Bericht aus den Details für den [Gastkonfigurationszuweisungs-Grund](../../../../articles/governance/policy/how-to/determine-non-compliance.md#compliance-details-for-guest-configuration) an. Im folgenden Beispiel gibt die Abfrage nur Ergebnisse zurück, bei denen der Name der Gastzuweisung `installed_application_linux` lautet und die Ausgabe die Zeichenfolge `Chrome` enthält, um alle Linux-Computer aufzulisten, auf denen ein Paket mit dem Namen **Chrome** installiert ist.

```kusto
GuestConfigurationResources
| where name in ('installed_application_linux')
| project id, name, resources = properties.latestAssignmentReport.resources, vmid = split(properties.targetResourceId,'/')[(-1)], status = tostring(properties.complianceStatus)
| extend resources = iff(isnull(resources[0]), dynamic([{}]), resources)
| mvexpand resources
| extend reasons = resources.reasons
| extend reasons = iff(isnull(reasons[0]), dynamic([{}]), reasons)
| mvexpand reasons
| where reasons.phrase contains 'chrome'
| project id, vmid, name, status, resource = resources.resourceId, reason = reasons.phrase
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
az graph query -q "GuestConfigurationResources | where name in ('installed_application_linux') | project id, name, resources = properties.latestAssignmentReport.resources, vmid = split(properties.targetResourceId,'/')[(-1)], status = tostring(properties.complianceStatus) | extend resources = iff(isnull(resources[0]), dynamic([{}]), resources) | mvexpand resources | extend reasons = resources.reasons | extend reasons = iff(isnull(reasons[0]), dynamic([{}]), reasons) | mvexpand reasons | where reasons.phrase contains 'chrome' | project id, vmid, name, status, resource = resources.resourceId, reason = reasons.phrase"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "GuestConfigurationResources | where name in ('installed_application_linux') | project id, name, resources = properties.latestAssignmentReport.resources, vmid = split(properties.targetResourceId,'/')[(-1)], status = tostring(properties.complianceStatus) | extend resources = iff(isnull(resources[0]), dynamic([{}]), resources) | mvexpand resources | extend reasons = resources.reasons | extend reasons = iff(isnull(reasons[0]), dynamic([{}]), reasons) | mvexpand reasons | where reasons.phrase contains 'chrome' | project id, vmid, name, status, resource = resources.resourceId, reason = reasons.phrase"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Probieren Sie im Azure Resource Graph-Explorer die folgende Abfrage aus:

- Azure-Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/GuestConfigurationResources%0a%7c%20where%20name%20in%20(%27installed_application_linux%27)%0a%7c%20project%20id%2c%20name%2c%20resources%20%3d%20properties.latestAssignmentReport.resources%2c%20vmid%20%3d%20split(properties.targetResourceId%2c%27%2f%27)%5b(-1)%5d%2c%20status%20%3d%20tostring(properties.complianceStatus)%0a%7c%20extend%20resources%20%3d%20iff(isnull(resources%5b0%5d)%2c%20dynamic(%5b%7b%7d%5d)%2c%20resources)%0a%7c%20mvexpand%20resources%0a%7c%20extend%20reasons%20%3d%20resources.reasons%0a%7c%20extend%20reasons%20%3d%20iff(isnull(reasons%5b0%5d)%2c%20dynamic(%5b%7b%7d%5d)%2c%20reasons)%0a%7c%20mvexpand%20reasons%0a%7c%20where%20reasons.phrase%20contains%20%27chrome%27%0a%7c%20project%20id%2c%20vmid%2c%20name%2c%20status%2c%20resource%20%3d%20resources.resourceId%2c%20reason%20%3d%20reasons.phrase" target="_blank">portal.azure.com</a>
- Azure Government-Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/GuestConfigurationResources%0a%7c%20where%20name%20in%20(%27installed_application_linux%27)%0a%7c%20project%20id%2c%20name%2c%20resources%20%3d%20properties.latestAssignmentReport.resources%2c%20vmid%20%3d%20split(properties.targetResourceId%2c%27%2f%27)%5b(-1)%5d%2c%20status%20%3d%20tostring(properties.complianceStatus)%0a%7c%20extend%20resources%20%3d%20iff(isnull(resources%5b0%5d)%2c%20dynamic(%5b%7b%7d%5d)%2c%20resources)%0a%7c%20mvexpand%20resources%0a%7c%20extend%20reasons%20%3d%20resources.reasons%0a%7c%20extend%20reasons%20%3d%20iff(isnull(reasons%5b0%5d)%2c%20dynamic(%5b%7b%7d%5d)%2c%20reasons)%0a%7c%20mvexpand%20reasons%0a%7c%20where%20reasons.phrase%20contains%20%27chrome%27%0a%7c%20project%20id%2c%20vmid%2c%20name%2c%20status%2c%20resource%20%3d%20resources.resourceId%2c%20reason%20%3d%20reasons.phrase" target="_blank">portal.azure.us</a>
- Azure China 21Vianet-Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/GuestConfigurationResources%0a%7c%20where%20name%20in%20(%27installed_application_linux%27)%0a%7c%20project%20id%2c%20name%2c%20resources%20%3d%20properties.latestAssignmentReport.resources%2c%20vmid%20%3d%20split(properties.targetResourceId%2c%27%2f%27)%5b(-1)%5d%2c%20status%20%3d%20tostring(properties.complianceStatus)%0a%7c%20extend%20resources%20%3d%20iff(isnull(resources%5b0%5d)%2c%20dynamic(%5b%7b%7d%5d)%2c%20resources)%0a%7c%20mvexpand%20resources%0a%7c%20extend%20reasons%20%3d%20resources.reasons%0a%7c%20extend%20reasons%20%3d%20iff(isnull(reasons%5b0%5d)%2c%20dynamic(%5b%7b%7d%5d)%2c%20reasons)%0a%7c%20mvexpand%20reasons%0a%7c%20where%20reasons.phrase%20contains%20%27chrome%27%0a%7c%20project%20id%2c%20vmid%2c%20name%2c%20status%2c%20resource%20%3d%20resources.resourceId%2c%20reason%20%3d%20reasons.phrase" target="_blank">portal.azure.cn</a>

---

