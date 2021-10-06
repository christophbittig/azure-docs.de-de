---
title: Bereitstellen von VM-Skalierungsgruppen mit IPv6 in Azure
titlesuffix: Azure Virtual Network
description: In diesem Artikel wird gezeigt, wie Sie VM-Skalierungsgruppen mit IPv6 in einem virtuellen Azure-Netzwerk bereitstellen können.
services: virtual-network
documentationcenter: na
author: asudbring
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: allensu
ms.openlocfilehash: 0f8bce99f26f175f3103ccc4824c094b2e1c4654
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368214"
---
# <a name="deploy-virtual-machine-scale-sets-with-ipv6-in-azure"></a>Bereitstellen von VM-Skalierungsgruppen mit IPv6 in Azure

In diesem Artikel erfahren Sie, wie Sie eine Dual-Stack-VM-Skalierungsgruppe (IPv4 + IPv6) mit einem externen Dual-Stack-Lastenausgleich in einem virtuellen Azure-Netzwerk bereitstellen. Der Prozess zur Erstellung einer IPv6-fähigen VM-Skalierungsgruppe ist nahezu identisch mit dem Prozess zur Erstellung einzelner virtueller Computer, der [hier](../../load-balancer/ipv6-configure-standard-load-balancer-template-json.md) beschrieben wird. Sie beginnen mit den Schritten, die denen ähneln, die für einzelne VMs beschrieben sind:
1.    Erstellen Sie öffentliche IPv4- und IPv6-Adressen.
2.    Erstellen Sie einen Dual-Stack-Lastenausgleich.  
3.    Erstellen Sie NSG-Regeln (Netzwerksicherheitsgruppe).  

Der einzige Schritt, der sich von einzelnen VMs unterscheidet, ist die Erstellung der NIC--Konfiguration (Netzwerkschnittstellenkarte), die die Ressourcen der VM-Skalierungsgruppe verwendet: „networkProfile/networkInterfaceConfigurations“. Die JSON-Struktur ähnelt der des „Microsoft.Network/networkInterfaces“-Objekts, das für einzelne VMs verwendet wird, mit der Ergänzung, dass die NIC und die „IPv4 IpConfiguration“ als primäre Schnittstelle mit dem Attribut **„primary“: true** eingestellt werden, wie im folgenden Beispiel gezeigt:

```json
          "networkProfile": {
            "networkInterfaceConfigurations": [
              {
                "name": "[variables('nicName')]",
                "properties": {
                  "primary": true,
          "networkSecurityGroup": {
            "id": "[resourceId('Microsoft.Network/networkSecurityGroups','VmssNsg')]"
          },                  
                  "ipConfigurations": [
                    {
                      "name": "[variables('ipConfigName')]",
                      "properties": {
                        "primary": true,
                        "subnet": {
                          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', 'MyvirtualNetwork','Mysubnet')]"
                        },
                        "privateIPAddressVersion":"IPv4",                       
                        "publicipaddressconfiguration": {
                          "name": "pub1",
                          "properties": {
                            "idleTimeoutInMinutes": 15
                          }
                        },
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', 'loadBalancer', 'bePool'))]"
                          }
                        ],
                        "loadBalancerInboundNatPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/inboundNatPools', 'loadBalancer', 'natPool')]"
                          }
                        ]
                      }
                    },
                    {
                      "name": "[variables('ipConfigNameV6')]",
                      "properties": {
                        "subnet": {
                          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets','MyvirtualNetwork','Mysubnet')]"
                        },
                        "privateIPAddressVersion":"IPv6",
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', 'loadBalancer','bePoolv6')]"
                          }
                        ],                        
                      }
                    }
                  ]
                }
              }
            ]
          }

```


## <a name="sample-virtual-machine-scale-set-template-json"></a>JSON-Beispielvorlage für VM-Skalierungsgruppen

Informationen zum Bereitstellen einer Dual-Stack-VM-Skalierungsgruppe (IPv4 + IPv6) mit einem externen Dual-Stack-Lastenausgleich und einem virtuellen Netzwerk finden Sie [hier](https://azure.microsoft.com/resources/templates/ipv6-in-vnet-vmss/) in der Beispielvorlage.
## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur IPv6-Unterstützung in virtuellen Azure-Netzwerken finden Sie unter [Was ist IPv6 für Azure Virtual Network?](ipv6-overview.md).
