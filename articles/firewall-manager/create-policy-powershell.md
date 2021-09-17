---
title: 'Schnellstart: Erstellen und Aktualisieren einer Azure Firewall-Richtlinie mit Azure PowerShell'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie eine Azure Firewall-Richtlinie mit Azure PowerShell erstellen und aktualisieren.
services: firewall-manager
author: vaboya
ms.author: victorh
ms.date: 08/16/2021
ms.topic: quickstart
ms.service: firewall-manager
ms.openlocfilehash: 3e7eef6eed76f2b6e3e122c1a373580e05556f03
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122254342"
---
# <a name="quickstart-create-and-update-an-azure-firewall-policy-using-azure-powershell"></a>Schnellstart: Erstellen und Aktualisieren einer Azure Firewall-Richtlinie mit Azure PowerShell

In dieser Schnellstartanleitung verwenden Sie Azure PowerShell, um eine Azure Firewall-Richtlinie mit Netzwerk- und Anwendungsregeln zu erstellen. Sie aktualisieren außerdem die vorhandene Richtlinie, indem Sie Netzwerk- und Anwendungsregeln hinzufügen.

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="set-up-the-network-and-policy"></a>Einrichten des Netzwerks und der Richtlinie

Zunächst erstellen Sie eine Ressourcengruppe und ein virtuelles Netzwerk. Dann erstellen Sie eine Azure Firewall-Richtlinie.

### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Die Ressourcengruppe enthält alle Ressourcen, die in diesem Verfahren verwendet werden.

```azurepowershell
New-AzResourceGroup -Name Test-FWpolicy-RG -Location "East US"
```

### <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks

```azurepowershell
$ServerSubnet = New-AzVirtualNetworkSubnetConfig -Name subnet-1 -AddressPrefix 10.0.0.0/24
$testVnet = New-AzVirtualNetwork -Name Test-FWPolicy-VNET -ResourceGroupName Test-FWPolicy-RG -Location "East US" -AddressPrefix 10.0.0.0/8 -Subnet $ServerSubnet
```
### <a name="create-a-firewall-policy"></a>Erstellen einer Firewallrichtlinie

```azurepowershell
New-AzFirewallPolicy -Name EUS-Policy -ResourceGroupName Test-FWPolicy-RG -Location "EAST US"
```

## <a name="create-a-network-rule-collection-group-and-add-new-rules"></a>Erstellen einer Netzwerk-Regelsammlungsgruppe und Hinzufügen neuer Regeln

Zunächst erstellen Sie die Regelsammlungsgruppe und fügen dann die Regelsammlung mit den Regeln hinzu.

### <a name="create-the-network-rule-collection-group"></a>Erstellen der Netzwerk-Regelsammlungsgruppe

```azurepowershell
$firewallpolicy = Get-AzFirewallPolicy -Name EUS-Policy -ResourceGroupName Test-FWPolicy-RG
$newnetworkrulecollectiongroup = New-AzFirewallPolicyRuleCollectionGroup  -Name "NetworkRuleCollectionGroup" -Priority 200 -ResourceGroupName Test-FWPolicy-RG -FirewallPolicyName EUS-Policy
$networkrulecollectiongroup = Get-AzFirewallPolicyRuleCollectionGroup -Name "NetworkRuleCollectionGroup" -ResourceGroupName Test-FWPolicy-RG -AzureFirewallPolicyName EUS-Policy
```
### <a name="create-network-rules"></a>Erstellen von Netzwerkregeln

```azurepowershell
$networkrule1= New-AzFirewallPolicyNetworkRule -Name NwRule1 -Description testRule1  -SourceAddress 10.0.0.0/24 -Protocol TCP -DestinationAddress 192.168.0.1/32 -DestinationPort 22 
$networkrule2= New-AzFirewallPolicyNetworkRule -Name NWRule2 -Description TestRule2  -SourceAddress 10.0.0.0/24 -Protocol UDP -DestinationAddress 192.168.0.10/32 -DestinationPort 1434
```
### <a name="create-a-network-rule-collection-and-add-new-rules"></a>Erstellen einer Netzwerkregelsammlung und Hinzufügen neuer Regeln

```azurepowershell
$newrulecollectionconfig=New-AzFirewallPolicyFilterRuleCollection -Name myfirstrulecollection -Priority 1000 -Rule $networkrule1,$networkrule2 -ActionType Allow
$newrulecollection = $networkrulecollectiongroup.Properties.RuleCollection.Add($newrulecollectionconfig)
```

### <a name="update-the-network-rule-collection-group"></a>Aktualisieren der Netzwerk-Regelsammlungsgruppe

```azurepowershell
Set-AzFirewallPolicyRuleCollectionGroup -Name "NetworkRuleCollectionGroup" -Priority "200" -FirewallPolicyObject $firewallpolicy -RuleCollection $networkrulecollectiongroup.Properties.RuleCollection
```

### <a name="output"></a>Ausgabe

Zeigen Sie die neue Regelsammlung und ihre Regeln an:

```azurepowershell
$output = $networkrulecollectiongroup.Properties.GetRuleCollectionByName("myfirstrulecollection")
Write-Output  $output
```

## <a name="add-network-rules-to-an-existing-rule-collection"></a>Hinzufügen von Netzwerkregeln zu einer vorhandenen Regelsammlung

Nachdem Sie nun über eine vorhandene Regelsammlung verfügen, können Sie ihr weitere Regeln hinzufügen.

### <a name="get-existing-network-rule-group-collection"></a>Abrufen einer vorhandenen Netzwerk-Regelgruppensammlung

```azurepowershell
$firewallpolicy = Get-AzFirewallPolicy -Name EUS-Policy -ResourceGroupName Test-FWPolicy-RG
$networkrulecollectiongroup = Get-AzFirewallPolicyRuleCollectionGroup -Name "NetworkRuleCollectionGroup" -ResourceGroupName Test-FWPolicy-RG -AzureFirewallPolicyName EUS-Policy 
```

### <a name="create-new-network-rules"></a>Erstellen von Netzwerkregeln

```azurepowershell
$newnetworkrule1 = New-AzFirewallPolicyNetworkRule -Name newNwRule01 -Description testRule01  -SourceAddress 10.0.0.0/24 -Protocol TCP -DestinationAddress 192.168.0.5/32 -DestinationPort 3389
$newnetworkrule2 = New-AzFirewallPolicyNetworkRule -Name newNWRule02 -Description TestRule02  -SourceAddress 10.0.0.0/24 -Protocol UDP -DestinationAddress 192.168.0.15/32 -DestinationPort 1434
```
### <a name="update-the-network-rule-collection-and-add-new-rules"></a>Aktualisieren der Netzwerkregelsammlung und Hinzufügen neuer Regeln

```azurepowershell
$getexistingrullecollection = $networkrulecollectiongroup.Properties.RuleCollection | where {$_.Name -match "myfirstrulecollection"}
$getexistingrullecollection.RuleS.Add($newnetworkrule1)
$getexistingrullecollection.RuleS.Add($newnetworkrule2)
```
### <a name="update-network-rule-collection-group"></a>Aktualisieren der Netzwerk-Regelsammlungsgruppe

```azurepowershell
Set-AzFirewallPolicyRuleCollectionGroup -Name "NetworkRuleCollectionGroup" -FirewallPolicyObject $firewallpolicy -Priority 200 -RuleCollection $networkrulecollectiongroup.Properties.RuleCollection
```
### <a name="output"></a>Ausgabe

Zeigen Sie die Regeln an, die Sie gerade hinzugefügt haben:

```azurepowershell
$output = $networkrulecollectiongroup.Properties.GetRuleCollectionByName("myfirstrulecollection")
Write-output $output
```
## <a name="create-an-application-rule-collection-and-add-new-rules"></a>Erstellen einer Anwendungsregelsammlung und Hinzufügen neuer Regeln

Zunächst erstellen Sie die Regelsammlungsgruppe, und fügen Sie dann die Regelsammlung mit Regeln hinzu.

### <a name="create-the-application-rule-collection-group"></a>Erstellen der Anwendungs-Regelsammlungsgruppe

```azurepowershell
$firewallpolicy = Get-AzFirewallPolicy -Name EUS-Policy -ResourceGroupName Test-FWPolicy-RG
$newapprulecollectiongroup = New-AzFirewallPolicyRuleCollectionGroup  -Name "ApplicationRuleCollectionGroup" -Priority 300 -ResourceGroupName Test-FWPolicy-RG -FirewallPolicyName EUS-Policy
```
### <a name="create-new-application-rules"></a>Erstellen neuer Anwendungsregeln

```azurepowershell
$apprule1 = New-AzFirewallPolicyApplicationRule -Name apprule1 -Description testapprule1 -SourceAddress 192.168.0.1/32 -TargetFqdn "*.contoso.com" -Protocol HTTPS
$apprule2 = New-AzFirewallPolicyApplicationRule -Name apprule2 -Description testapprule2  -SourceAddress 192.168.0.10/32 -TargetFqdn "www.contosoweb.com" -Protocol HTTPS
```
### <a name="create-a-new-application-rule-collection-with-rules"></a>Erstellen einer neuen Anwendungsregelsammlung mit Regeln

```azurepowershell
$apprulecollectiongroup = Get-AzFirewallPolicyRuleCollectionGroup -Name "ApplicationRuleCollectionGroup" -ResourceGroupName Test-FWPolicy-RG -AzureFirewallPolicyName EUS-Policy
$apprulecollection = New-AzFirewallPolicyFilterRuleCollection -Name myapprulecollection -Priority 1000 -Rule $apprule1,$apprule2 -ActionType Allow 
$newapprulecollection = $apprulecollectiongroup.Properties.RuleCollection.Add($apprulecollection) 
```

### <a name="update-the-application-rule-collection-group"></a>Aktualisieren der Anwendungs-Regelsammlungsgruppe

```azurepowershell
Set-AzFirewallPolicyRuleCollectionGroup -Name "ApplicationRuleCollectionGroup" -FirewallPolicyObject $firewallpolicy -Priority 300 -RuleCollection $apprulecollectiongroup.Properties.RuleCollection 
```

### <a name="output"></a>Ausgabe

Untersuchen Sie die neue Regelsammlungsgruppe und ihre neuen Regeln:

```azurepowershell
$output = $apprulecollectiongroup.Properties.GetRuleCollectionByName("myapprulecollection")
Write-Output $output
```

## <a name="add-application-rules-to-an-existing-rule-collection"></a>Hinzufügen von Anwendungsregeln zu einer vorhandenen Regelsammlung

Nachdem Sie nun über eine vorhandene Regelsammlung verfügen, können Sie ihr weitere Regeln hinzufügen.

```azurepowershell 
#Create new Application Rules for exist Rule collection
$newapprule1 = New-AzFirewallPolicyApplicationRule -Name newapprule01 -Description testapprule01 -SourceAddress 192.168.0.5/32 -TargetFqdn "*.contosoabc.com" -Protocol HTTPS
$newapprule2 = New-AzFirewallPolicyApplicationRule -Name newapprule02 -Description testapprule02  -SourceAddress 192.168.0.15/32 -TargetFqdn "www.contosowebabcd.com" -Protocol HTTPS
```

### <a name="update-the-application-rule-collection"></a>Aktualisieren der Anwendungsregelsammlung

```azurepowershell
$apprulecollection = $apprulecollectiongroup.Properties.RuleCollection | where {$_.Name -match "myapprulecollection"}
$apprulecollection.Rules.Add($newapprule1)
$apprulecollection.Rules.Add($newapprule2)

# Update Application Rule collection Group  
Set-AzFirewallPolicyRuleCollectionGroup -Name "ApplicationRuleCollectionGroup" -FirewallPolicyObject $firewallpolicy -Priority 300 -RuleCollection $apprulecollectiongroup.Properties.RuleCollection
```
### <a name="output"></a>Ausgabe

Zeigen Sie die neuen Regeln an:

```azurepowershell
$Output = $apprulecollectiongroup.Properties.GetRuleCollectionByName("myapprulecollection")
Write-Output $output
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die von Ihnen erstellten Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe. Dadurch werden alle erstellten Ressourcen entfernt.

Um die Ressourcengruppe zu löschen, verwenden Sie das Cmdlet `Remove-AzResourceGroup`:

```azurepowershell
Remove-AzResourceGroup -Name Test-FWpolicy-RG
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Weitere Informationen zur Bereitstellung mit Azure Firewall Manager](deployment-overview.md)