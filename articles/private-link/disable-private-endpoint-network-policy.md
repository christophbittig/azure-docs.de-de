---
title: Deaktivieren von Netzwerkrichtlinien für private Endpunkte
titleSuffix: Azure Private Link
description: Erfahren Sie, wie Sie Netzwerkrichtlinien für private Endpunkte deaktivieren.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: how-to
ms.date: 07/14/2021
ms.author: allensu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 41cdefd340ace93d5a068c9a74543965834d01ca
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/15/2021
ms.locfileid: "114221352"
---
# <a name="disable-network-policies-for-private-endpoints"></a>Deaktivieren von Netzwerkrichtlinien für private Endpunkte

Netzwerkrichtlinien wie Netzwerksicherheitsgruppen (NSG) werden für private Endpunkte nicht unterstützt. Zum Bereitstellen eines privaten Endpunkts in einem bestimmten Subnetz ist eine explizite Einstellung zum Deaktivieren in diesem Subnetz erforderlich. Diese Einstellung gilt nur für den privaten Endpunkt. Für andere Ressourcen im Subnetz wird der Zugriff basierend auf Sicherheitsregeln in der Netzwerksicherheitsgruppe gesteuert.
 
Wenn Sie das Portal zum Erstellen eines privaten Endpunkts verwenden, wird die `PrivateEndpointNetworkPolicies`-Einstellung im Rahmen des Erstellungsprozesses automatisch deaktiviert. Für die Bereitstellung mit anderen Clients ist ein zusätzlicher Schritt erforderlich, um diese Einstellung zu ändern. 

Sie können die Einstellung mit folgenden Funktionen deaktivieren:

* Cloud Shell im Azure-Portal.
* Azure PowerShell
* Azure CLI
* Azure Resource Manager-Vorlagen
 
In den folgenden Beispielen wird beschrieben, wie `PrivateEndpointNetworkPolicies` für ein virtuelles Netzwerk namens **myVNet** mit einem **standardmäßigen** Subnetz in einer Ressourcengruppe namens **myResourceGroup** deaktiviert wird.

## <a name="azure-powershell"></a>Azure PowerShell

In diesem Abschnitt wird beschrieben, wie Sie Subnetzrichtlinien für private Endpunkte mit Azure PowerShell deaktivieren können. Verwenden Sie [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) und [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork), um die Richtlinie zu deaktivieren.

```azurepowershell
$net =@{
    Name = 'myVNet'
    ResourceGroupName = 'myResourceGroup'
}
$vnet = Get-AzVirtualNetwork @net

($vnet | Select -ExpandProperty subnets).PrivateEndpointNetworkPolicies = "Disabled"

$vnet | Set-AzVirtualNetwork
```
## <a name="azure-cli"></a>Azure CLI
In diesem Abschnitt wird beschrieben, wie Sie Subnetzrichtlinien für private Endpunkte mit der Azure CLI deaktivieren können. Verwenden Sie [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update), um die Richtlinie zu deaktivieren.

```azurecli
az network vnet subnet update \ 
  --disable-private-endpoint-network-policies true \
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  
```
## <a name="resource-manager-template"></a>Resource Manager-Vorlage
In diesem Abschnitt wird beschrieben, wie Sie Subnetzrichtlinien für private Endpunkte mit einer Azure Resource Manager-Vorlage deaktivieren können.

```json
{ 
          "name": "myVirtualNetwork", 
          "type": "Microsoft.Network/virtualNetworks", 
          "apiVersion": "2019-04-01", 
          "location": "WestUS", 
          "properties": { 
                "addressSpace": { 
                     "addressPrefixes": [ 
                          "10.0.0.0/16" 
                        ] 
                  }, 
                  "subnets": [ 
                         { 
                                "name": "default", 
                                "properties": { 
                                    "addressPrefix": "10.0.0.0/24", 
                                    "privateEndpointNetworkPolicies": "Disabled" 
                                 } 
                         } 
                  ] 
          } 
} 
```
## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zu [privaten Azure-Endpunkten](private-endpoint-overview.md)
 
