---
title: Skalierung der SNAT-Ports mit Azure NAT Gateway
description: Sie können die Azure Firewall mit NAT Gateway integrieren, damit Sie die SNAT-Ports erhöhen können.
services: firewall
author: KumudD
ms.service: firewall
ms.topic: how-to
ms.date: 04/23/2021
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 30d1da573bec205212658fa6aff5ac3b71ad2e50
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399008"
---
# <a name="scale-snat-ports-with-azure-nat-gateway"></a>Skalierung der SNAT-Ports mit Azure NAT Gateway

Azure Firewall bietet 2048 SNAT-Ports pro konfigurierter öffentlicher IP-Adresse, so dass Sie bis zu [250 öffentliche IP-Adressen](./deploy-multi-public-ip-powershell.md) zuordnen können. Je nach Architektur und Datenverkehrsmustern benötigen Sie möglicherweise mehr als 512.000 verfügbare SNAT-Ports mit dieser Konfiguration. Wenn Sie z. B. damit große [Windows Virtual Desktop-Bereitstellungen](./protect-azure-virtual-desktop.md) schützen, die in Microsoft 365 Apps integriert sind.

Eine weitere Herausforderung bei der Nutzung einer großen Anzahl von öffentlichen IP-Adressen ist, wenn es nachgelagerte Anforderungen an die IP-Adressfilterung gibt. Azure Firewall wählt zufällig die öffentliche Quell-IP-Adresse aus, die Sie für eine Verbindung verwenden möchten, daher müssen Sie alle damit verbundenen öffentlichen IP-Adressen zulassen. Sogar wenn Sie [Präfixe für öffentliche IP-Adressen](../virtual-network/ip-services/public-ip-address-prefix.md) verwenden und Sie 250 öffentliche IP-Adressen zuordnen müssen, um die Anforderungen an den ausgehenden SNAT-Port zu erfüllen, müssen Sie dennoch 16 Präfixe für öffentliche IP-Adressen erstellen und zulassen.

Die bessere Option zur Skalierung ausgehender SNAT-Ports ist die Verwendung von [NAT-Gateway-Ressource](../virtual-network/nat-gateway/nat-overview.md). Es bietet 64.000 SNAT-Ports pro öffentlicher IP-Adresse und unterstützt bis zu 16 öffentliche IP-Adressen, sodass effektiv bis zu 1.024.000 ausgehende SNAT-Ports zur Verfügung stehen.

Sobald eine NAT-Gateway-Ressource mit einem Azure Firewall-Subnetz verbunden ist, wird für den gesamten ausgehenden Internetverkehr automatisch die öffentliche IP-Adresse des NAT-Gateways verwendet. Die Konfiguration von [Benutzerdefinierten Routen](../virtual-network/tutorial-create-route-table-portal.md) ist nicht erforderlich. Der Antwortdatenverkehr verwendet die öffentliche IP-Adresse der Azure Firewall, um die Flusssymmetrie zu wahren. Sind mehrere IP-Adressen mit dem NAT-Gateway verbunden, wird die IP-Adresse zufällig ausgewählt. Es ist nicht möglich, anzugeben, welche Adresse verwendet werden soll.

Es gibt keine doppelte NAT mit dieser Architektur. Azure Firewall Instanzen senden den Datenverkehr mithilfe ihrer privaten IP-Adresse an das NAT-Gateway, anstatt die öffentliche IP-Adresse Azure Firewall.

> [!NOTE]
> Die Verwendung von Azure NAT Gateway ist derzeit nicht kompatibel mit Azure Firewall, wenn Sie [Azure Firewall über mehrere Verfügbarkeitszonen hinweg](deploy-availability-zone-powershell.md) bereitgestellt haben.

## <a name="associate-nat-gateway-with-azure-firewall-subnet---azure-powershell"></a>NAT-Gateway mit Azure Firewall-Subnetz verknüpfen - Azure PowerShell

Das nachfolgende Beispiel erstellt und verknüpft ein NAT-Gateway mit einem Azure Firewall-Subnetz mithilfe von Azure PowerShell.

```azurepowershell-interactive
# Create public IP addresses
New-AzPublicIpAddress -Name public-ip-1 -ResourceGroupName nat-rg -Sku Standard -AllocationMethod Static -Location 'South Central US'
New-AzPublicIpAddress -Name public-ip-2 -ResourceGroupName nat-rg -Sku Standard -AllocationMethod Static -Location 'South Central US'

# Create NAT gateway
$PublicIPAddress1 = Get-AzPublicIpAddress -Name public-ip-1 -ResourceGroupName nat-rg
$PublicIPAddress2 = Get-AzPublicIpAddress -Name public-ip-2 -ResourceGroupName nat-rg
New-AzNatGateway -Name firewall-nat -ResourceGroupName nat-rg -PublicIpAddress $PublicIPAddress1,$PublicIPAddress2 -Location 'South Central US' -Sku Standard

# Associate NAT gateway to subnet
$virtualNetwork = Get-AzVirtualNetwork -Name nat-vnet -ResourceGroupName nat-rg
$natGateway = Get-AzNatGateway -Name firewall-nat -ResourceGroupName nat-rg
$firewallSubnet = $virtualNetwork.subnets | Where-Object -Property Name -eq AzureFirewallSubnet
$firewallSubnet.NatGateway = $natGateway
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="associate-nat-gateway-with-azure-firewall-subnet---azure-cli"></a>NAT-Gateway mit Azure Firewall-Subnetz verknüpfen - Azure CLI

Das nachfolgende Beispiel erstellt und verknüpft ein NAT-Gateway mit einem Azure Firewall-Subnetz mithilfe von Azure CLI.

```azurecli-interactive
# Create public IP addresses
az network public-ip create --name public-ip-1 --resource-group nat-rg --sku standard
az network public-ip create --name public-ip-2 --resource-group nat-rg --sku standard

# Create NAT gateway
az network nat gateway create --name firewall-nat --resource-group nat-rg --public-ip-addresses public-ip-1 public-ip-2

# Associate NAT gateway to subnet
az network vnet subnet update --name AzureFirewallSubnet --vnet-name nat-vnet --resource-group nat-rg --nat-gateway firewall-nat
```

## <a name="next-steps"></a>Nächste Schritte

- [Entwerfen von virtuellen Netzwerken mit NAT-Gatewayressourcen](../virtual-network/nat-gateway/nat-gateway-resource.md)