---
title: Fehler aufgrund nicht verfügbarer SKU
description: Hier wird beschrieben, wie Sie den Fehler aufgrund einer nicht verfügbaren SKU beim Bereitstellen von Ressourcen mit Azure Resource Manager beheben.
ms.topic: troubleshooting
ms.date: 04/14/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2ed04cd3839b8146d7e988b571113a737f38801f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095581"
---
# <a name="resolve-errors-for-sku-not-available"></a>Beheben des Fehlers vom Typ „SKU nicht verfügbar“

In diesem Artikel wird beschrieben, wie Sie den Fehler **SkuNotAvailable** beheben. Wenn Sie in dieser Region/Zone oder einer anderen Region/Zone keine geeignete SKU finden, die Ihre Geschäftsanforderungen erfüllt, übermitteln Sie eine [SKU-Anforderung](/troubleshoot/azure/general/region-access-request-process) an den Azure-Support.

## <a name="symptom"></a>Symptom

Beim Bereitstellen einer Ressource (in der Regel ein virtueller Computer) werden der folgende Fehlercode und die folgende Fehlermeldung angezeigt:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>'
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Ursache

Sie erhalten diesen Fehler, wenn die ausgewählte Ressourcen-SKU (z.B. die Größe des virtuellen Computers) für den ausgewählten Standort nicht verfügbar ist.

Wenn Sie eine Azure Spot-VM oder eine Instanz einer Spot-Skalierungsgruppe bereitstellen, ist an diesem Standort keine Kapazität für Azure Spot verfügbar. Weitere Informationen finden Sie unter [Spot-Fehlermeldungen](../../virtual-machines/error-codes-spot.md).

## <a name="solution-1---powershell"></a>Lösung 1: PowerShell

Verwenden Sie den Befehl [Get AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku), um zu ermitteln, welche SKUs in einer Region/Zone verfügbar sind. Filtern Sie die Ergebnisse nach Standort. Für diesen Befehl benötigen Sie die aktuelle Version von PowerShell.

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

Die Ergebnisse enthalten eine Liste von SKUs für den Standort und alle Einschränkungen für diese SKU. Beachten Sie, dass die SKU möglicherweise als `NotAvailableForSubscription` aufgeführt wird.

```output
ResourceType          Name           Locations   Zone      Restriction                      Capability           Value
------------          ----           ---------   ----      -----------                      ----------           -----
virtualMachines       Standard_A0    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2    centralus             NotAvailableForSubscription      MaxResourceVolumeMB  138240
virtualMachines       Standard_D1_v2 centralus   {2, 1, 3}                                  MaxResourceVolumeMB
```

Verwenden Sie zum Filtern nach Standort und SKU den folgenden Befehl:

```azurepowershell-interactive
$SubId = (Get-AzContext).Subscription.Id

$Region = "centralus" # change region here
$VMSku = "Standard_M" # change VM SKU here

$VMSKUs = Get-AzComputeResourceSku | where {$_.Locations.Contains($Region) -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains($VMSku)}

$OutTable = @()

foreach ($SkuName in $VMSKUs.Name)
        {
            $LocRestriction = if ((($VMSKUs | where Name -EQ $SkuName).Restrictions.Type | Out-String).Contains("Location")){"NotAvavalableInRegion"}else{"Available - No region restrictions applied" }
            $ZoneRestriction = if ((($VMSKUs | where Name -EQ $SkuName).Restrictions.Type | Out-String).Contains("Zone")){"NotAvavalableInZone: "+(((($VMSKUs | where Name -EQ $SkuName).Restrictions.RestrictionInfo.Zones)| Where-Object {$_}) -join ",")}else{"Available - No zone restrictions applied"}
            
            
            $OutTable += New-Object PSObject -Property @{
                                                         "Name" = $SkuName
                                                         "Location" = $Region
                                                         "Applies to SubscriptionID" = $SubId
                                                         "Subscription Restriction" = $LocRestriction
                                                         "Zone Restriction" = $ZoneRestriction
                                                         }
         }

$OutTable | select Name, Location, "Applies to SubscriptionID", "Region Restriction", "Zone Restriction" | Sort-Object -Property Name | FT
```

Der Befehl gibt Ergebnisse ähnlich den folgenden zurück:

```output
Name                   Location  Applies to SubscriptionID            Region Restriction                         Zone Restriction                        
----                   --------  -------------------------            ------------------------                   ----------------     
Standard_M128          centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Available - No region restrictions applied Available - No zone restrictions applied
Standard_M128-32ms     centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Available - No region restrictions applied Available - No zone restrictions applied
Standard_M128-64ms     centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Available - No region restrictions applied Available - No zone restrictions applied
Standard_M128dms_v2    centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx NotAvavalableInRegion                      NotAvavalableInZone: 1,2,3
```

## <a name="solution-2---azure-cli"></a>Lösung 2: Azure CLI

Verwenden Sie den Befehl [az vm list-skus](/cli/azure/vm#az_vm_list_skus), um zu ermitteln, welche SKUs in einer Region verfügbar sind. Verwenden Sie den Parameter `--location`, um die Ausgabe nach Standort zu filtern. Verwenden Sie den Parameter `--size`, um nach dem Teil eines Namens für die Größe zu suchen. Verwenden Sie den Parameter `--all`, um alle Informationen anzuzeigen, einschließlich der Größen, die für das aktuelle Abonnement nicht verfügbar sind.

Sie müssen die Azure CLI, Version 2.15.0 oder höher, verwenden. Führen Sie `az --version` aus, um Ihre Version zu überprüfen. [Aktualisieren Sie bei Bedarf Ihre Installation](/cli/azure/update-azure-cli).

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --all --output table
```

Der Befehl gibt Ergebnisse ähnlich den folgenden zurück:

```output
ResourceType     Locations       Name              Zones    Restrictions
---------------  --------------  ----------------  -------  --------------
virtualMachines  southcentralus  Standard_F1       1,2,3    None
virtualMachines  southcentralus  Standard_F2       1,2,3    None
virtualMachines  southcentralus  Standard_F4       1,2,3    None
...
virtualMachines  southcentralus  Standard_F72s_v2  1,2,3    NotAvailableForSubscription, type: Zone, locations: southcentralus, zones: 1,2,3
...
```

## <a name="solution-3---azure-portal"></a>Lösung 3: Azure-Portal

Verwenden Sie das [Portal](https://portal.azure.com), um zu ermitteln, welche SKUs in einer Region verfügbar sind. Melden Sie sich beim Portal an, und fügen Sie über die Oberfläche eine Ressource hinzu. Beim Festlegen der Werte werden die verfügbaren SKUs für die Ressource angezeigt. Sie müssen die Bereitstellung nicht abschließen.

Starten Sie beispielsweise den Prozess zum Erstellen eines virtuellen Computers. Um andere verfügbare Größen anzuzeigen, wählen Sie **Größe ändern** aus.

![Erstellen eines virtuellen Computers](./media/error-sku-not-available/create-vm.png)

Sie können die verfügbaren Größen filtern und durch sie scrollen.

![Verfügbare SKUs](./media/error-sku-not-available/available-sizes.png)

## <a name="solution-4---rest"></a>Lösung 4: REST

Verwenden Sie den Vorgang [Ressourcen-SKUs – Auflisten](/rest/api/compute/resourceskus/list), um zu ermitteln, welche SKUs in einer Region verfügbar sind.

Verfügbare SKUs und Regionen werden im folgenden Format zurückgegeben:

```json
{
  "value": [
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A0",
      "tier": "Standard",
      "size": "A0",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A1",
      "tier": "Standard",
      "size": "A1",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    ...
  ]
}
```
