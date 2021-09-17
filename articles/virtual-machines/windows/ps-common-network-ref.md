---
title: Häufige PowerShell-Befehle für virtuelle Azure-Netzwerke
description: Enthält häufig verwendete PowerShell-Befehle als Einstiegshilfe für die Erstellung eines virtuellen Netzwerks und der zugeordneten Ressourcen für VMs.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: a99450a5762e619c167913ce22b126a2be13f8c4
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122694839"
---
# <a name="common-powershell-commands-for-azure-virtual-networks"></a>Häufige PowerShell-Befehle für virtuelle Azure-Netzwerke

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs 

Zur Erstellung eines virtuellen Computers müssen Sie ein [virtuelles Netzwerk](../../virtual-network/virtual-networks-overview.md) erstellen oder über ein vorhandenes virtuelles Netzwerk verfügen, dem die VM hinzugefügt werden kann. Normalerweise müssen Sie beim Erstellen einer VM auch die in diesem Artikel beschriebenen Ressourcen erstellen.

Unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/) erfahren Sie, wie Sie die neueste Version von Azure PowerShell installieren, Ihr Abonnement auswählen und sich bei Ihrem Konto anmelden.

Einige Variablen könnten hilfreich sein, wenn Sie mehr als einen der Befehle aus diesem Artikel ausführen:

- $location – Der Speicherort der Netzwerkressourcen. Sie können [Get-AzLocation](/powershell/module/az.resources/get-azlocation) verwenden, um eine [geografische Region](https://azure.microsoft.com/regions/) zu suchen, die sich für Ihre Zwecke eignet.
- $myResourceGroup – Der Name der Ressourcengruppe, in der die Netzwerkressourcen gespeichert sind.

## <a name="create-network-resources"></a>Erstellen von Netzwerkressourcen

| Aufgabe | Get-Help |
| ---- | ------- |
| Erstellen von Subnetzkonfigurationen |$subnet1 = [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) -Name "mySubnet1" -AddressPrefix XX.X.X.X/XX<BR>$subnet2 = New-AzVirtualNetworkSubnetConfig -Name "mySubnet2" -AddressPrefix XX.X.X.X/XX<BR><BR>Ein typisches Netzwerk kann über ein Subnetz für einen [Load Balancer mit Internetzugriff](../../load-balancer/load-balancer-overview.md) und ein separates Subnetz für einen [internen Load Balancer](../../load-balancer/load-balancer-overview.md) verfügen. |
| Erstellen eines virtuellen Netzwerks |$vnet = [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup -Location $location -AddressPrefix XX.X.X.X/XX -Subnet $subnet1, $subnet2 |
| Überprüfen auf einen eindeutigen Domänennamen |[Test-AzDnsAvailability](/powershell/module/az.network/test-azdnsavailability) -DomainNameLabel "myDNS" -Location $location<BR><BR>Sie können einen DNS-Domänennamen für eine [öffentliche IP-Ressource](../../virtual-network/public-ip-addresses.md) angeben. Dadurch erstellen Sie für domainname.location.cloudapp.azure.com eine Zuordnung zur öffentlichen IP-Adresse auf den von Azure verwalteten DNS-Servern. Der Name darf nur Buchstaben, Zahlen und Bindestriche enthalten. Das erste und letzte Zeichen muss jeweils ein Buchstabe oder eine Zahl sein, und der Domänenname muss für den Azure-Standort eindeutig sein. Wenn **TRUE** zurückgegeben wird, ist der vorgeschlagene Name global eindeutig. |
| Erstellen einer öffentlichen IP-Adresse |$pip = [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) -Name "myPublicIp" -ResourceGroupName $myResourceGroup -DomainNameLabel "myDNS" -Location $location -AllocationMethod Dynamic<BR><BR>Für die öffentliche IP-Adresse wird der zuvor getestete Domänenname verwendet. Sie wird bei der Front-End-Konfiguration des Load Balancers genutzt. |
| Erstellen einer Front-End-IP-Konfiguration |$frontendIP = [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) -Name "myFrontendIP" -PublicIpAddress $pip<BR><BR>Die Front-End-Konfiguration enthält die öffentliche IP-Adresse, die Sie zuvor für eingehenden Netzwerk-Datenverkehr erstellt haben. |
| Erstellen eines Back-End-Adresspools |$beAddressPool = [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) -Name "myBackendAddressPool"<BR><BR>Stellt interne Adressen für das Back-End des Load Balancers bereit, auf die über eine Netzwerkschnittstelle zugegriffen wird. |
| Erstellen eines Tests |$healthProbe = [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) -Name "myProbe" -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2<BR><BR>Enthält Integritätstests, die zum Überprüfen der Verfügbarkeit von Instanzen virtueller Computer im Back-End-Adresspool verwendet werden. |
| Erstellen einer Lastenausgleichsregel |$lbRule = [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>Enthält Regeln, mit denen ein öffentlicher Port auf dem Load Balancer einem Port im Back-End-Adresspool zugewiesen wird. |
| Erstellen einer eingehenden NAT-Regel |$inboundNATRule = [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) -Name "myInboundRule1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389<BR><BR>Enthält Regeln, mit denen ein öffentlicher Port auf dem Load Balancer einem Port für einen bestimmten virtuellen Computer im Back-End-Adresspool zugeordnet wird. |
| Erstellen eines Lastenausgleichs |$loadBalancer = [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) -ResourceGroupName $myResourceGroup -Name "myLoadBalancer" -Location $location -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule -LoadBalancingRule $lbRule -BackendAddressPool $beAddressPool -Probe $healthProbe |
| Erstellen einer Netzwerkschnittstelle |$nic1= [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) -ResourceGroupName $myResourceGroup -Name "myNIC" -Location $location -PrivateIpAddress XX.X.X.X -Subnet $subnet2 -LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] -LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>Erstellen Sie eine Netzwerkschnittstelle, indem Sie die öffentliche IP-Adresse und das zuvor erstellte virtuelle Netzwerksubnetz verwenden. |

## <a name="get-information-about-network-resources"></a>Abrufen von Informationen zu Netzwerkressourcen

| Aufgabe | Get-Help |
| ---- | ------- |
| Auflisten von virtuellen Netzwerken |[Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) -ResourceGroupName $myResourceGroup<BR><BR>Listet alle virtuellen Netzwerke in der Ressourcengruppe auf. |
| Abrufen von Informationen zu einem virtuellen Netzwerk |Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup |
| Auflisten von Subnetzen in einem virtuellen Netzwerk |Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup &#124; Select Subnets |
| Abrufen von Informationen zu einem Subnetz |[Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) -Name "mySubnet1" -VirtualNetwork $vnet<BR><BR>Ruft Informationen zu dem Subnetz im angegebenen virtuellen Netzwerk ab. Der $vnet-Wert gibt das von „Get-AzVirtualNetwork“ zurückgegebene Objekt an. |
| Auflisten von IP-Adressen |[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) -ResourceGroupName $myResourceGroup<BR><BR>Listet die öffentlichen IP-Adressen in der Ressourcengruppe auf. |
| Auflisten von Load Balancern |[Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer) -ResourceGroupName $myResourceGroup<BR><BR>Listet alle Load Balancer in der Ressourcengruppe auf. |
| Auflisten von Netzwerkschnittstellen |[Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) -ResourceGroupName $myResourceGroup<BR><BR>Listet alle Netzwerkschnittstellen in der Ressourcengruppe auf. |
| Abrufen von Informationen zu einer Netzwerkschnittstelle |Get-AzNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Ruft Informationen zu einer bestimmten Netzwerkschnittstelle ab. |
| Abrufen der IP-Konfiguration einer Netzwerkschnittstelle |[Get-AzNetworkInterfaceIPConfig](/powershell/module/az.network/get-aznetworkinterfaceipconfig) -Name "myNICIP" -NetworkInterface $nic<BR><BR>Ruft Informationen zur IP-Konfiguration der angegebenen Netzwerkschnittstelle ab. Der $nic-Wert gibt das von „Get-AzNetworkInterface“ zurückgegebene Objekt an. |

## <a name="manage-network-resources"></a>Verwalten von Netzwerkressourcen

| Aufgabe | Get-Help |
| ---- | ------- |
| Hinzufügen eines Subnetzes zu einem virtuellen Netzwerk |[Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) -AddressPrefix XX.X.X.X/XX -Name "mySubnet1" -VirtualNetwork $vnet<BR><BR>Fügt einem vorhandenen virtuellen Netzwerk ein Subnetz hinzu. Der $vnet-Wert gibt das von „Get-AzVirtualNetwork“ zurückgegebene Objekt an. |
| Löschen eines virtuellen Netzwerks |[Remove-AzVirtualNetwork](/powershell/module/az.network/remove-azvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup<BR><BR>Entfernt das angegebene virtuelle Netzwerk aus der Ressourcengruppe. |
| Löschen einer Netzwerkschnittstelle |[Remove-AzNetworkInterface](/powershell/module/az.network/remove-aznetworkinterface) -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Entfernt die angegebene Netzwerkschnittstelle aus der Ressourcengruppe. |
| Löschen eines Load Balancers |[Remove-AzLoadBalancer](/powershell/module/az.network/remove-azloadbalancer) -Name "myLoadBalancer" -ResourceGroupName $myResourceGroup<BR><BR>Entfernt den angegebenen Load Balancer aus der Ressourcengruppe. |
| Löschen einer öffentlichen IP-Adresse |[Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress)-Name "myIPAddress" -ResourceGroupName $myResourceGroup<BR><BR>Entfernt die angegebene öffentliche IP-Adresse aus der Ressourcengruppe. |

## <a name="next-steps"></a>Nächste Schritte
Verwenden Sie die soeben erstellte Netzwerkschnittstelle beim [Erstellen einer VM](./quick-create-powershell.md?toc=/azure/virtual-machines/windows/toc.json).
