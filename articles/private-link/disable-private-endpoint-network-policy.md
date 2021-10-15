---
title: Verwalten von Netzwerkrichtlinien für private Endpunkte
titleSuffix: Azure Private Link
description: Hier erfahren Sie, wie Sie Netzwerkrichtlinien für private Endpunkte verwalten.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: how-to
ms.date: 08/26/2021
ms.author: allensu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6d4671823f5eb3f186007aea46983860269d916b
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129361761"
---
# <a name="manage-network-policies-for-private-endpoints"></a>Verwalten von Netzwerkrichtlinien für private Endpunkte

Netzwerkrichtlinien wie Netzwerksicherheitsgruppen (NSG) wurden für private Endpunkte bisher nicht unterstützt. Zum Bereitstellen eines privaten Endpunkts in einem bestimmten Subnetz war eine explizite Einstellung zum Deaktivieren in diesem Subnetz erforderlich. Diese Einstellung gilt nur für den privaten Endpunkt. Für andere Ressourcen im Subnetz wird der Zugriff basierend auf Sicherheitsregeln in der Netzwerksicherheitsgruppe gesteuert.

> [!IMPORTANT]
> Die NSG- und UDR-Unterstützung für private Endpunkte befindet sich in ausgewählten Regionen in der öffentlichen Vorschau. Weitere Informationen finden Sie unter [Public preview of Private Link UDR Support](https://azure.microsoft.com/updates/public-preview-of-private-link-udr-support/) (Öffentliche Vorschauversion von Private Link UDR-Unterstützung) und [Public preview of Private Link Network Security Group Support](https://azure.microsoft.com/updates/public-preview-of-private-link-network-security-group-support/) (Öffentliche Vorschauversion der Unterstützung von Netzwerksicherheitsgruppen).
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Wenn Sie das Portal zum Erstellen eines privaten Endpunkts verwenden, wird die `PrivateEndpointNetworkPolicies`-Einstellung im Rahmen des Erstellungsprozesses automatisch deaktiviert. Für die Bereitstellung mit anderen Clients ist ein zusätzlicher Schritt erforderlich, um diese Einstellung zu ändern. 

Sie können die Einstellung mit folgenden Funktionen deaktivieren und aktivieren:

* Cloud Shell im Azure-Portal.
* Azure PowerShell
* Azure CLI
* Azure-Ressourcen-Manager-Vorlagen
 
In den folgenden Beispielen wird beschrieben, wie `PrivateEndpointNetworkPolicies` für ein virtuelles Netzwerk namens **myVNet** mit einem **standardmäßigen** Subnetz in einer Ressourcengruppe namens **myResourceGroup** deaktiviert und aktiviert wird.

## <a name="azure-powershell"></a>Azure PowerShell

### <a name="disable-network-policy"></a>Deaktivieren von Netzwerkrichtlinien

In diesem Abschnitt wird beschrieben, wie Sie Subnetzrichtlinien für private Endpunkte mit Azure PowerShell deaktivieren können. Verwenden Sie [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) und [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork), um die Richtlinie zu deaktivieren.

```azurepowershell
$net =@{
    Name = 'myVNet'
    ResourceGroupName = 'myResourceGroup'
}
$vnet = Get-AzVirtualNetwork @net

($vnet | Select -ExpandProperty subnets | Where-Object {$_.Name -eq 'default'}).PrivateEndpointNetworkPolicies = "Disabled"

$vnet | Set-AzVirtualNetwork
```

### <a name="enable-network-policy"></a>Aktivieren von Netzwerkrichtlinien

In diesem Abschnitt wird beschrieben, wie Sie Subnetzrichtlinien für private Endpunkte mit Azure PowerShell aktivieren können. Verwenden Sie [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) und [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork), um die Richtlinie zu aktivieren.

```azurepowershell
$net =@{
    Name = 'myVNet'
    ResourceGroupName = 'myResourceGroup'
}
$vnet = Get-AzVirtualNetwork @net

($vnet | Select -ExpandProperty subnets | Where-Object {$_.Name -eq 'default'}).PrivateEndpointNetworkPolicies = "Enabled"

$vnet | Set-AzVirtualNetwork
```
## <a name="azure-cli"></a>Azure CLI

### <a name="disable-network-policy"></a>Deaktivieren von Netzwerkrichtlinien

In diesem Abschnitt wird beschrieben, wie Sie Subnetzrichtlinien für private Endpunkte mit der Azure CLI deaktivieren können. Verwenden Sie [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update), um die Richtlinie zu deaktivieren.

```azurecli
az network vnet subnet update \ 
  --disable-private-endpoint-network-policies true \
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  
```

### <a name="enable-network-policy"></a>Aktivieren von Netzwerkrichtlinien

In diesem Abschnitt wird beschrieben, wie Sie Subnetzrichtlinien für private Endpunkte mit der Azure CLI aktivieren können. Verwenden Sie [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update), um die Richtlinie zu aktivieren.

```azurecli
az network vnet subnet update \ 
  --disable-private-endpoint-network-policies false \
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  
```
## <a name="resource-manager-template"></a>Resource Manager-Vorlage

### <a name="disable-network-policy"></a>Deaktivieren von Netzwerkrichtlinien

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

### <a name="enable-network-policy"></a>Aktivieren von Netzwerkrichtlinien

In diesem Abschnitt wird beschrieben, wie Sie Subnetzrichtlinien für private Endpunkte mit einer Azure Resource Manager-Vorlage aktivieren können.

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
                                    "privateEndpointNetworkPolicies": "Enabled" 
                                 } 
                         } 
                  ] 
          } 
} 
```
## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zu [privaten Azure-Endpunkten](private-endpoint-overview.md)
 
