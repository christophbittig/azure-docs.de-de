---
title: 'Tutorial: Erstellen einer Gateway Load Balancer-Instanz – Azure PowerShell'
titleSuffix: Azure Load Balancer
description: In diesem Tutorial erfahren Sie, wie Sie eine Gateway Load Balancer-Instanz mit Azure PowerShell erstellen.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 11/02/2021
ms.custom: template-tutorial, ignite-fall-2021
ms.openlocfilehash: 97b8b428d71da312c7cfc0ed9da057003ac68ee7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131101349"
---
# <a name="tutorial-create-a-gateway-load-balancer-using-azure-powershell"></a>Tutorial: Erstellen einer Gateway Load Balancer-Instanz mit Azure PowerShell

Azure Load Balancer umfasst Standard-, Basic- und Gateway-SKUs. Gateway Load Balancer wird zum transparenten Einfügen von virtuellen Netzwerkgeräten (Network Virtual Appliances, NVA) verwendet. Verwenden Sie Gateway Load Balancer für Szenarien, die hohe Leistung und hohe Skalierbarkeit von NVAs benötigen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Registrieren einer Previewfunktion.
> * Erstellen Sie ein virtuelles Netzwerk.
> * Erstellen einer Netzwerksicherheitsgruppe.
> * Erstellen einer Gateway Load Balancer-Instanz.
> * Verketten eines Load Balancer-Front-Ends mit der Gateway Load Balancer-Instanz.

> [!IMPORTANT]
> Die Azure Gateway Load Balancer befindet sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement ([kostenloses Azure-Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))
- Eine vorhandene öffentliche Azure Load Balancer-Instanz mit Standard-SKU. Weitere Informationen zum Erstellen eines Lastenausgleichs finden Sie im Artikel zum **[Erstellen eines öffentlichen Lastenausgleichs mit Azure PowerShell](quickstart-load-balancer-standard-public-powershell.md)** .
    - Für dieses Tutorial wird in den Beispielen **myLoadBalancer** als Name für den vorhandenen Lastenausgleich verwendet.
- Azure PowerShell (lokal installiert) oder Azure Cloud Shell

Wenn Sie PowerShell lokal installieren und verwenden möchten, müssen Sie für diesen Artikel mindestens Version 5.4.1 des Azure PowerShell-Moduls verwenden. Führen Sie `Get-Module -ListAvailable Az` aus, um die installierte Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-Az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

## <a name="register-preview-feature"></a>Registrieren einer Previewfunktion

Im Rahmen der öffentlichen Vorschau von Gateway Load Balancer muss der Anbieter in Ihrem Azure-Abonnement registriert sein.

Verwenden Sie [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature), um das **AllowGatewayLoadBalancer**-Anbieterfeature zu registrieren:

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowGatewayLoadBalancer

```

Verwenden Sie [Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider), um den **Microsoft.Network**-Ressourcenanbieter zu registrieren:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Network

```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Erstellen Sie mit [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Ressourcengruppe:

```azurepowershell-interactive
New-AzResourceGroup -Name 'TutorGwLB-rg' -Location 'eastus'

```

## <a name="create-virtual-network"></a>Virtuelles Netzwerk erstellen

Für die Ressourcen, die sich im Back-End-Pool der Gateway Load Balancer-Instanz befinden, ist ein virtuelles Netzwerk erforderlich. Verwenden Sie [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork), um das virtuelle Netzwerk zu erstellen. Verwenden Sie [New-AzBastion](/powershell/module/az.network/new-azbastion), um einen Bastionhost für die sichere Verwaltung von Ressourcen im virtuellen Netzwerk bereitzustellen.

```azurepowershell-interactive
## Create backend subnet config ##
$subnet = @{
    Name = 'myBackendSubnet'
    AddressPrefix = '10.1.0.0/24'
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create Azure Bastion subnet. ##
$bastsubnet = @{
    Name = 'AzureBastionSubnet' 
    AddressPrefix = '10.1.1.0/24'
}
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig @bastsubnet

## Create the virtual network ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'TutorGwLB-rg'
    Location = 'eastus'
    AddressPrefix = '10.1.0.0/16'
    Subnet = $subnetConfig,$bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @net

## Create public IP address for bastion host. ##
$ip = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'TutorGwLB-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @ip

## Create bastion host ##
$bastion = @{
    ResourceGroupName = 'TutorGwLB-rg'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @bastion -AsJob

```

## <a name="create-nsg"></a>NSG erstellen

Verwenden Sie das folgende Beispiel, um eine Netzwerksicherheitsgruppe zu erstellen. Sie konfigurieren die NSG-Regeln, die für den Netzwerkdatenverkehr in dem zuvor erstellten virtuellen Netzwerk erforderlich sind.

Verwenden Sie [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig), um Regeln für die NSG zu erstellen. Verwenden Sie [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup), um die NSG zu erstellen.

```azurepowershell-interactive
## Create rule for network security group and place in variable. ##
$nsgrule1 = @{
    Name = 'myNSGRule-AllowAll'
    Description = 'Allow all'
    Protocol = '*'
    SourcePortRange = '*'
    DestinationPortRange = '*'
    SourceAddressPrefix = '0.0.0.0/0'
    DestinationAddressPrefix = '0.0.0.0/0'
    Access = 'Allow'
    Priority = '100'
    Direction = 'Inbound'
}
$rule1 = New-AzNetworkSecurityRuleConfig @nsgrule1

$nsgrule2 = @{
    Name = 'myNSGRule-AllowAll-TCP-Out'
    Description = 'Allow all TCP Out'
    Protocol = 'TCP'
    SourcePortRange = '*'
    DestinationPortRange = '*'
    SourceAddressPrefix = '0.0.0.0/0'
    DestinationAddressPrefix = '0.0.0.0/0'
    Access = 'Allow'
    Priority = '100'
    Direction = 'Outbound'
}
$rule2 = New-AzNetworkSecurityRuleConfig @nsgrule2

## Create network security group ##
$nsg = @{
    Name = 'myNSG'
    ResourceGroupName = 'TutorGwLB-rg'
    Location = 'eastus'
    SecurityRules = $rule1,$rule2
}
New-AzNetworkSecurityGroup @nsg
```

## <a name="create-gateway-load-balancer"></a>Erstellen einer Gateway Load Balancer-Instanz

In diesem Abschnitt erstellen Sie die Konfiguration und stellen Sie die Gateway Load Balancer-Instanz bereit. Verwenden Sie [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig), um die Front-End-IP-Konfiguration für den Lastenausgleich zu erstellen. 

Sie verwenden [New-AzLoadBalancerTunnelInterface](/powershell/module/az.network/new-azloadbalancerfrontendipconfig), um zwei Tunnelschnittstellen für den Lastenausgleich zu erstellen. 

Erstellen Sie mit [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) einen Back-End-Pool für die NVAs. 

Um die Integrität der Back-End-Instanzen im Lastenausgleich zu überwachen, ist ein Integritätstest erforderlich. Verwenden Sie [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig), um den Integritätstest zu erstellen.

Datenverkehr, der an die Back-End-Instanzen gerichtet ist, wird mit einer Lastenausgleichsregel weitergeleitet. Verwenden Sie [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig), um die Lastenausgleichskonfiguration zu erstellen.

Verwenden Sie [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer), um den Lastenausgleich zu erstellen.

```azurepowershell-interactive
## Place virtual network configuration in a variable for later use. ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'TutorGwLB-rg'
}
$vnet = Get-AzVirtualNetwork @net

## Create load balancer frontend configuration and place in variable. ## 
$fe = @{
    Name = 'myFrontend'
    SubnetId = $vnet.subnets[0].id
}
$feip = New-AzLoadBalancerFrontendIpConfig @fe

## Create backend address pool configuration and place in variable. ## 
$int1 = @{
    Type = 'Internal'
    Protocol = 'Vxlan'
    Identifier = '800'
    Port = '10800'
}
$tunnelInterface1 = New-AzLoadBalancerBackendAddressPoolTunnelInterfaceConfig @int1

$int2 = @{
    Type = 'External'
    Protocol = 'Vxlan'
    Identifier = '801'
    Port = '10801'
}
$tunnelInterface2 = New-AzLoadBalancerBackendAddressPoolTunnelInterfaceConfig @int2

$pool = @{
    Name = 'myBackendPool'
    TunnelInterface = $tunnelInterface1,$tunnelInterface2
}
$bepool = New-AzLoadBalancerBackendAddressPoolConfig @pool

## Create the health probe and place in variable. ## 
$probe = @{
    Name = 'myHealthProbe'
    Protocol = 'http'
    Port = '80'
    IntervalInSeconds = '360'
    ProbeCount = '5'
    RequestPath = '/'
}
$healthprobe = New-AzLoadBalancerProbeConfig @probe

## Create the load balancer rule and place in variable. ## 
$para = @{
    Name = 'myLBRule'
    Protocol = 'All'
    FrontendPort = '0'
    BackendPort = '0'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bepool
    Probe = $healthprobe
}
$rule = New-AzLoadBalancerRuleConfig @para

## Create the load balancer resource. ## 
$lb = @{
    ResourceGroupName = 'TutorGwLB-rg'
    Name = 'myLoadBalancer-gw'
    Location = 'eastus'
    Sku = 'Gateway'
    LoadBalancingRule = $rule
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bepool
    Probe = $healthprobe
}
New-AzLoadBalancer @lb

```

## <a name="add-network-virtual-appliances-to-the-gateway-load-balancer-backend-pool"></a>Hinzufügen virtueller Netzwerkgeräte zum Back-End-Pool der Gateway Load Balancer-Instanz
Stellen Sie NVAs über den Azure Marketplace bereit. Fügen Sie die virtuellen Computer nach der Bereitstellung mit [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface) dem Back-End-Pool hinzu.

## <a name="chain-load-balancer-frontend-to-gateway-load-balancer"></a>Verketten eines Load Balancer-Front-Ends mit der Gateway Load Balancer-Instanz

In diesem Beispiel verketten Sie das Front-End eines Standardlastenausgleichs mit der Gateway Load Balancer-Instanz. 

Sie fügen das Front-End zur Front-End-IP eines vorhandenen Lastenausgleichs in Ihrem Abonnement hinzu.

Verwenden Sie [Set-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/set-azloadbalancerfrontendipconfig), um das Front-End der Gateway Load Balancer-Instanz mit Ihrem vorhandenen Lastenausgleich zu verketten.

```azurepowershell-interactive
## Place the gateway load balancer configuration into a variable. ##
$par1 = @{
    ResourceGroupName = 'TutorGwLB-rg'
    Name = 'myLoadBalancer-gw'
}
$gwlb = Get-AzLoadBalancer @par1

## Place the existing load balancer into a variable. ##
$par2 = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myLoadBalancer'
}
$lb = Get-AzLoadBalancer @par2

## Place the existing public IP for the existing load balancer into a variable.
$par3 = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myPublicIP'
}
$publicIP = Get-AzPublicIPAddress @par3

## Chain the gateway load balancer to your existing load balancer frontend. ##
$par4 = @{
    Name = 'myFrontEndIP'
    PublicIPAddress = $publicIP
    LoadBalancer = $lb
    GatewayLoadBalancerId = $gwlb.FrontendIpConfigurations.Id
}
$config = Set-AzLoadBalancerFrontendIpConfig @par4

$config | Set-AzLoadBalancer

```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcen nicht mehr benötigen, führen Sie den Befehl [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) aus, um die Ressourcengruppe, den Lastenausgleich und alle übrigen Ressourcen zu löschen.

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'TutorGwLB-rg'
```

## <a name="next-steps"></a>Nächste Schritte

Erstellen von virtuellen Netzwerkgeräten in Azure. 

Wählen Sie beim Erstellen der NVAs die in diesem Tutorial erstellten Ressourcen aus:

* Virtuelles Netzwerk

* Subnet

* Netzwerksicherheitsgruppe

* Gateway Load Balancer

Fahren Sie mit dem nächsten Artikel fort, um sich über das Erstellen einer regionsübergreifenden Azure Load Balancer-Instanz zu informieren:
> [!div class="nextstepaction"]
> [Regionsübergreifender Lastenausgleich](tutorial-cross-region-powershell.md)
