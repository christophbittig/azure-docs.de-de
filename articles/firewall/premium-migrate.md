---
title: Migrieren zu Azure Firewall Premium
description: Erfahren Sie, wie Sie von Azure Firewall Standard zu Azure Firewall Premium migrieren.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 09/13/2021
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 580dcb11ae04aaae78d2c15f24c2c08d1df6158d
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/26/2021
ms.locfileid: "129061844"
---
# <a name="migrate-to-azure-firewall-premium"></a>Migrieren zu Azure Firewall Premium

Sie können Azure Firewall Standard zu Azure Firewall Premium migrieren, um von den neuen Premium-Funktionen zu profitieren. Weitere Informationen zu Azure Firewall Premium-Features finden Sie unter [Features von Azure Firewall Premium](premium-features.md).

Die folgenden zwei Beispiele zeigen die Vorgehensweise:
- Migrieren einer vorhandenen Standardrichtlinie mithilfe von Azure PowerShell
- Migrieren Sie eine vorhandene Standardfirewall (mit klassischen Regeln) zu Azure Firewall Premium mit einer Premiumrichtlinie.

Wenn Sie Terraform für die Bereitstellung der Azure Firewall verwenden, können Sie Terraform für die Migration zu Azure Firewall Premium nutzen. Weitere Informationen finden Sie unter [„Migrieren Sie Azure Firewall Standard zu Premium mit Terraform“](/azure/developer/terraform/firewall-upgrade-premium?toc=/azure/firewall/toc.json&bc=/azure/firewall/breadcrumb/toc.json).

## <a name="performance-considerations"></a>Überlegungen zur Leistung

Die Leistung ist ein Aspekt bei der Migration von der Standard-SKU. IDPS und TLS-Inspektion sind rechenintensive Vorgänge. Die Premium-SKU verwendet eine leistungsfähigere VM-SKU, die auf einen maximalen Durchsatz von 30 GBit/s skaliert wird, vergleichbar mit der Standard-SKU. Der Durchsatz von 30 GBit/s wird unterstützt, wenn er mit IDPS im Modus „Warnung“ konfiguriert ist. Werdenb IDPS im Modus „Ablehnen“ und die TLS-Inspektion verwendet, erhöht dies die CPU-Auslastung. Es kann zu einer Verschlechterung des maximalen Durchsatzes kommen. 

Der Firewalldurchsatz ist möglicherweise niedriger als 30 Gbit/s, wenn mindestens eine Signatur auf **Warnung und ablehnen** festgelegt ist oder Anwendungsregeln mit aktivierter **TLS-Inspektion** vorliegen. Microsoft empfiehlt Kunden, vollständige Skalierungstests in ihrer Azure-Bereitstellung durchzuführen, um sicherzustellen, dass die Leistung des Firewalldiensts den Erwartungen entspricht.

## <a name="downtime"></a>Ausfallzeit

Migrieren Sie Ihre Firewall während einer geplanten Wartung, da es während der Migration zu Ausfallzeiten kommt.

## <a name="migrate-an-existing-policy-using-azure-powershell"></a>Migrieren einer vorhandenen Richtlinie mithilfe von Azure PowerShell

`Transform-Policy.ps1` ist ein Azure PowerShell-Skript, das eine neue Premiumrichtlinie aus einer vorhandenen Standardrichtlinie erstellt.

Angesichts einer Standard-Firewallrichtlinien-ID wandelt das Skript diese in eine Premiumrichtlinie für Azure Firewall um. Das Skript stellt zunächst eine Verbindung mit Ihrem Azure-Konto her, pullt die Richtlinie, transformiert verschiedene Parameter oder fügt diese hinzu und lädt dann eine neue Premiumrichtlinie hoch. Die neue Premiumrichtlinie hat den Namen `<previous_policy_name>_premium`. Im Falle einer Umwandlung der untergeordneten Richtlinie bleibt die Verknüpfung zur übergeordneten Richtlinie bestehen.

Verwendungsbeispiel:

`Transform-Policy -PolicyId /subscriptions/XXXXX-XXXXXX-XXXXX/resourceGroups/some-resource-group/providers/Microsoft.Network/firewallPolicies/policy-name`

> [!IMPORTANT]
> Das Skript migriert die Threat Intelligence-Einstellungen nicht. Sie müssen sich diese Einstellungen notieren, bevor Sie fortfahren und sie manuell migrieren.

```azurepowershell
<#
    .SYNOPSIS
        Given an Azure firewall policy id the script will transform it to a Premium Azure firewall policy. 
        The script will first pull the policy, transform/add various parameters and then upload a new premium policy. 
        The created policy will be named <previous_policy_name>_premium if no new name provided else new policy will be named as the parameter passed.  
    .Example
        Transform-Policy -PolicyId /subscriptions/XXXXX-XXXXXX-XXXXX/resourceGroups/some-resource-group/providers/Microsoft.Network/firewallPolicies/policy-name -NewPolicyName <optional param for the new policy name>
#>

param (
    #Resource id of the azure firewall policy. 
    [Parameter(Mandatory=$true)]
    [string]
    $PolicyId,

    #new filewallpolicy name, if not specified will be the previous name with the '_premium' suffix
    [Parameter(Mandatory=$false)]
    [string]
    $NewPolicyName = ""
)
$ErrorActionPreference = "Stop"
$script:PolicyId = $PolicyId
$script:PolicyName = $NewPolicyName

function ValidatePolicy {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Object]
        $Policy
    )

    Write-Host "Validating resource is as expected"

    if ($null -eq $Policy) {
        Write-Error "Recived null policy"
        exit(1)
    }
    if ($Policy.GetType().Name -ne "PSAzureFirewallPolicy") {
        Write-Host "Resource must be of type Microsoft.Network/firewallPolicies" -ForegroundColor Red
        exit(1)
    }

    if ($Policy.Sku.Tier -eq "Premium") {
        Write-Host "Policy is already premium"
        exit(1)
    }
}

function GetPolicyNewName {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Microsoft.Azure.Commands.Network.Models.PSAzureFirewallPolicy]
        $Policy
    )

    if (-not [string]::IsNullOrEmpty($script:PolicyName)) {
        return $script:PolicyName
    }

    return $Policy.Name + "_premium"
}

function TransformPolicyToPremium {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Microsoft.Azure.Commands.Network.Models.PSAzureFirewallPolicy]
        $Policy
    )    
    $NewPolicyParameters = @{
                        Name = (GetPolicyNewName -Policy $Policy) 
                        ResourceGroupName = $Policy.ResourceGroupName 
                        Location = $Policy.Location 
                        ThreatIntelMode = $Policy.ThreatIntelMode 
                        BasePolicy = $Policy.BasePolicy.Id
                        DnsSetting = $Policy.DnsSettings 
                        Tag = $Policy.Tag 
                        SkuTier = "Premium" 
    }

    Write-Host "Creating new policy"
    $premiumPolicy = New-AzFirewallPolicy @NewPolicyParameters

    Write-Host "Populating rules in new policy"
    foreach ($ruleCollectionGroup in $Policy.RuleCollectionGroups) {
        $ruleResource = Get-AzResource -ResourceId $ruleCollectionGroup.Id
        $ruleToTransfom = Get-AzFirewallPolicyRuleCollectionGroup -AzureFirewallPolicy $Policy -Name $ruleResource.Name
        $ruleCollectionGroup = @{
            FirewallPolicyObject = $premiumPolicy
            Priority = $ruleToTransfom.Properties.Priority
            Name = $ruleToTransfom.Name
        }

        if ($ruleToTransfom.Properties.RuleCollection.Count) {
            $ruleCollectionGroup["RuleCollection"] = $ruleToTransfom.Properties.RuleCollection
        }

        Set-AzFirewallPolicyRuleCollectionGroup @ruleCollectionGroup
    }
}

function ValidateAzNetworkModuleExists {
    Write-Host "Validating needed module exists"
    $networkModule = Get-InstalledModule -Name "Az.Network" -ErrorAction SilentlyContinue
    if (($null -eq $networkModule) -or ($networkModule.Version -lt 4.5.0)){
        Write-Host "Please install Az.Network module version 4.5.0 or higher, see instructions: https://github.com/Azure/azure-powershell#installation"
        exit(1)
    }
    Import-Module Az.Network -MinimumVersion 4.5.0
}

ValidateAzNetworkModuleExists
$policy = Get-AzFirewallPolicy -ResourceId $script:PolicyId
ValidatePolicy -Policy $policy
TransformPolicyToPremium -Policy $policy

```

## <a name="migrate-an-existing-standard-firewall-using-the-azure-portal"></a>Migrieren einer vorhandenen Standardfirewall über das Azure-Portal

In diesem Beispiel wird gezeigt, wie Sie über das Azure-Portal eine Standardfirewall (klassische Regeln) zu Azure Firewall Premium mit einer Premiumrichtlinie migrieren.

1. Wählen Sie im Azure-Portal Ihre Standardfirewall aus. Wählen Sie auf der Seite **Übersicht** die Option zum **Migrieren zur Firewallrichtlinie** aus.

   :::image type="content" source="media/premium-migrate/firewall-overview-migrate.png" alt-text="Migrieren zur Firewallrichtlinie":::

1. Wählen Sie auf der Seite **Migrieren zur Firewallrichtlinie** die Option **Überprüfen und erstellen** aus.
1. Klicken Sie auf **Erstellen**.

   Die Bereitstellung dauert einige Minuten.
1. Verwenden Sie das [Azure PowerShell-Skript](#migrate-an-existing-policy-using-azure-powershell) `Transform-Policy.ps1`, um diese neue Standardrichtlinie in eine Premiumrichtlinie umzuwandeln.
1. Wählen Sie im Portal Ihre Standardfirewallressource aus. 
1. Wählen Sie unter **Automatisierung** die Option **Vorlage exportieren** aus. Lassen Sie diese Browserregisterkarte geöffnet. Sie werden später darauf zurückkommen.
   > [!TIP]
   > Um sicherzustellen, dass Sie die Vorlage nicht verlieren, laden Sie sie herunter und speichern Sie sie für den Fall, dass Ihre Browserregisterkarte geschlossen oder aktualisiert wird.
1. Öffnen Sie eine neue Browserregisterkarte, navigieren Sie zum Azure-Portal, und öffnen Sie die Ressourcengruppe, die Ihre Firewall enthält.
1. Löschen Sie die vorhandene Standardfirewallinstanz.

   Der Vorgang kann einige Minuten dauern.

1. Kehren Sie zur Browserregisterkarte mit der exportierten Vorlage zurück.
1. Wählen Sie **Bereitstellen** und dann auf der Seite **Benutzerdefinierte Bereitstellung** die Option **Vorlage bearbeiten** aus.
1. Bearbeiten Sie den Text der Vorlage:
   
   1. Ändern Sie unter der Ressource `Microsoft.Network/azureFirewalls` unter `Properties`, `sku`, die `tier` von „Standard“ in „Premium“.
   1. Ändern Sie unter der Vorlage `Parameters` den Wert `defaultValue` für die `firewallPolicies_FirewallPolicy_,<your policy name>_externalid` von:
      
       `"/subscriptions/<subscription id>/resourceGroups/<your resource group>/providers/Microsoft.Network/firewallPolicies/FirewallPolicy_<your policy name>"`

      in:

      `"/subscriptions/<subscription id>/resourceGroups/<your resource group>/providers/Microsoft.Network/firewallPolicies/FirewallPolicy_<your policy name>_premium"`
1. Wählen Sie **Speichern** aus.
1. Klicken Sie auf **Überprüfen + erstellen**.
1. Klicken Sie auf **Erstellen**.

Wenn die Bereitstellung abgeschlossen ist, können Sie nun alle neuen Features von Azure Firewall Premium konfigurieren.

## <a name="next-steps"></a>Nächste Schritte

- [Erfahren Sie mehr über die Features von Azure Firewall Premium](premium-features.md)
