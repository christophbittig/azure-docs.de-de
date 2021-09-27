---
title: Konfigurieren von benutzerdefinierten v2-Regeln mit PowerShell
titleSuffix: Azure Web Application Firewall
description: Erfahren Sie, wie Sie benutzerdefinierte Regeln für Web Application Firewall v2 (WAF) mit Azure PowerShell konfigurieren. Sie können eigene Regeln erstellen, die für jede Anforderung ausgewertet werden, die die Firewall durchläuft.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 05/21/2020
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7911903de844a06b59f95e52e2d6f3f05b43ccfb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128587364"
---
# <a name="configure-web-application-firewall-v2-on-application-gateway-with-a-custom-rule-using-azure-powershell"></a>Konfigurieren von Web Application Firewall v2 für Application Gateway mit einer benutzerdefinierten Regel mithilfe von Azure PowerShell

<!--- If you make any changes to the PowerShell in this article, also make the change in the corresponding Sample file: azure-docs-powershell-samples/application-gateway/waf-rules/waf-custom-rules.ps1 --->

Benutzerdefinierte Regeln ermöglichen die Erstellung eigener Regeln, die für jede Anforderung ausgewertet werden, für die Web Application Firewall v2 (WAF) durchlaufen wird. Diese Regeln haben eine höhere Priorität als die restlichen Regeln in den verwalteten Regelsätzen. Die benutzerdefinierten Regeln verfügen über eine Aktion (Zulassen oder Blockieren), eine Übereinstimmungsbedingung und einen Operator zum Zulassen der vollständigen Anpassung.

In diesem Artikel wird eine Application Gateway-WAF v2 erstellt, für die eine benutzerdefinierte Regel verwendet wird. Die benutzerdefinierte Regel blockiert Datenverkehr, wenn der Anforderungsheader den Benutzer-Agent *evilbot* enthält.

Weitere Beispiele für benutzerdefinierte Regeln finden Sie unter [Create and use Web Application Firewall custom rules](create-custom-waf-rules.md) (Erstellen und Verwenden benutzerdefinierter Web Application Firewall-Regeln).

Wenn Sie den Azure PowerShell-Code in diesem Artikel in einem fortlaufenden Skript benötigen, das Sie kopieren, einfügen und ausführen können, lesen Sie unter [PowerShell-Beispiele für Azure Application Gateway](powershell-samples.md) nach.

## <a name="prerequisites"></a>Voraussetzungen

### <a name="azure-powershell-module"></a>Azure PowerShell-Modul

Wenn Sie Azure PowerShell lokal installieren und verwenden möchten, müssen Sie für dieses Skript mindestens Version 2.1.0 des Azure PowerShell-Moduls verwenden.

1. Führen Sie `Get-Module -ListAvailable Az` aus, um die Version zu finden. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu.
2. Führen Sie `Connect-AzAccount` aus, um eine Verbindung mit Azure herzustellen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="example-script"></a>Beispielskript

### <a name="set-up-variables"></a>Einrichten von Variablen

```azurepowershell
$rgname = "CustomRulesTest"

$location = "East US"

$appgwName = "WAFCustomRules"
```

### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

```azurepowershell
$resourceGroup = New-AzResourceGroup -Name $rgname -Location $location
```

### <a name="create-a-vnet"></a>Erstellen eines VNET

```azurepowershell
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -AddressPrefix "10.0.0.0/24"

$sub2 = New-AzVirtualNetworkSubnetConfig -Name "backendSubnet" -AddressPrefix "10.0.1.0/24"

$vnet = New-AzvirtualNetwork -Name "Vnet1" -ResourceGroupName $rgname -Location $location `
  -AddressPrefix "10.0.0.0/16" -Subnet @($sub1, $sub2)
```

### <a name="create-a-static-public-vip"></a>Erstellen einer statischen öffentlichen VIP

```azurepowershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $rgname -name "AppGwIP" `
  -location $location -AllocationMethod Static -Sku Standard
```

### <a name="create-pool-and-frontend-port"></a>Erstellen eines Pools und eines Front-End-Ports

```azurepowershell
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -VirtualNetwork $vnet

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "AppGwIpConfig" -Subnet $gwSubnet

$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "fipconfig" -PublicIPAddress $publicip

$pool = New-AzApplicationGatewayBackendAddressPool -Name "pool1" `
  -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com

$fp01 = New-AzApplicationGatewayFrontendPort -Name "port1" -Port 80
```

### <a name="create-a-listener-http-setting-rule-and-autoscale"></a>Erstellen eines Listeners, einer HTTP-Einstellung, einer Regel und der Autoskalierung

```azurepowershell
$listener01 = New-AzApplicationGatewayHttpListener -Name "listener1" -Protocol Http `
  -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01

$poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name "setting1" -Port 80 `
  -Protocol Http -CookieBasedAffinity Disabled

$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType basic `
  -BackendHttpSettings $poolSetting01 -HttpListener $listener01 -BackendAddressPool $pool

$autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 3

$sku = New-AzApplicationGatewaySku -Name WAF_v2 -Tier WAF_v2
```

### <a name="create-two-custom-rules-and-apply-it-to-waf-policy"></a>Erstellen von zwei benutzerdefinierten Regeln und Anwenden dieser Regeln auf die WAF-Richtlinie

```azurepowershell
# Create WAF config
$wafConfig = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention" -RuleSetType "OWASP" -RuleSetVersion "3.0"
# Create a User-Agent header custom rule 
$variable = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestHeaders -Selector User-Agent
$condition = New-AzApplicationGatewayFirewallCondition -MatchVariable $variable -Operator Contains -MatchValue "evilbot" -Transform Lowercase -NegationCondition $False  
$rule = New-AzApplicationGatewayFirewallCustomRule -Name blockEvilBot -Priority 2 -RuleType MatchRule -MatchCondition $condition -Action Block
 
# Create a geo-match custom rule
$var2 = New-AzApplicationGatewayFirewallMatchVariable -VariableName RemoteAddr
$condition2 = New-AzApplicationGatewayFirewallCondition -MatchVariable $var2 -Operator GeoMatch -MatchValue "US"  -NegationCondition $False
$rule2 = New-AzApplicationGatewayFirewallCustomRule -Name allowUS -Priority 14 -RuleType MatchRule -MatchCondition $condition2 -Action Allow

# Create a firewall policy
$wafPolicy = New-AzApplicationGatewayFirewallPolicy -Name wafpolicyNew -ResourceGroup $rgname -Location $location -CustomRule $rule,$rule2
```

### <a name="create-the-application-gateway"></a>Erstellen des Anwendungsgateways

```azurepowershell
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $rgname `
  -Location $location -BackendAddressPools $pool `
  -BackendHttpSettingsCollection  $poolSetting01 `
  -GatewayIpConfigurations $gipconfig -FrontendIpConfigurations $fipconfig01 `
  -FrontendPorts $fp01 -HttpListeners $listener01 `
  -RequestRoutingRules $rule01 -Sku $sku -AutoscaleConfiguration $autoscaleConfig `
  -WebApplicationFirewallConfig $wafConfig `
  -FirewallPolicy $wafPolicy
```

## <a name="update-your-waf"></a>Aktualisieren der WAF

Nach dem Erstellen der WAF können Sie sie mit einer Prozedur aktualisieren, die dem folgenden Code ähnelt:

```azurepowershell
# Get the existing policy
$policy = Get-AzApplicationGatewayFirewallPolicy -Name $policyName -ResourceGroupName $RGname
# Add an existing rule named $rule
$policy.CustomRules.Add($rule)
# Update the policy
Set-AzApplicationGatewayFirewallPolicy -InputObject $policy
```

## <a name="next-steps"></a>Nächste Schritte

[Weitere Informationen zu Web Application Firewall für Application Gateway](ag-overview.md)
