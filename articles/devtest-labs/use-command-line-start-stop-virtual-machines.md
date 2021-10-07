---
title: Starten und Beenden von VMs mit Befehlszeilentools
description: Erfahren Sie, wie Sie Befehlszeilentools verwenden, um virtuelle Computer in Azure DevTest Labs zu starten und zu beenden.
ms.topic: how-to
ms.date: 06/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e0c75cda526f5a365badf3f97aeb92a94b93588f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128655560"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>Starten und Beenden von virtuellen Computern in Azure DevTest Labs mit Befehlszeilentools
In diesem Artikel erfahren Sie, wie Sie mithilfe von Azure PowerShell oder der Azure CLI virtuelle Computer in einem Lab in Azure DevTest Labs starten bzw. beenden. Zur Automatisierung dieser Vorgänge können Sie PowerShell- bzw. CLI-Skripts erstellen. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Übersicht
Mit Azure DevTest Labs erhalten Sie schnelle, einfache und schlanke Entwicklungs-/Testumgebungen. Sie können damit Kosten verwalten, schnell VMs bereitstellen und Ausschuss minimieren.  Im Azure-Portal gibt es integrierte Funktionen, mit denen Sie VMs in einem Lab so konfigurieren können, dass diese automatisch zu bestimmten Zeiten gestartet oder beendet werden. 

In einigen Szenarios sollten Sie das Starten und Beenden von VMs jedoch ggf. über PowerShell- bzw. CLI-Skripts automatisieren. Diese Methode bietet Ihnen mehr Flexibilität, wenn Sie einzelne virtuelle Computer jederzeit starten und beenden möchten und nicht nur zu bestimmten Zeitpunkten. Folgende Beispiele beschreiben Situationen, in denen das Ausführen dieser Tasks mithilfe von Skripts nützlich wäre.

- Bei der Verwendung dreistufiger Anwendungen im Rahmen einer Testumgebung müssen die Stufen in einer bestimmten Reihenfolge gestartet werden. 
- Eine VM soll beendet werden, sobald ein benutzerdefiniertes Kriterium erfüllt ist, um so Geld zu sparen. 
- Ein Task soll innerhalb eines CI/CD-Workflows ausgeführt werden, um die VMs am Anfang des Workflows zu starten, die VMs als Build-Computer, Testcomputer oder Infrastruktur zu verwenden und dann die VMs nach Abschluss des Prozesses zu beenden. Ein Beispiel hierfür wäre die benutzerdefinierte Image Factory mit Azure DevTest Labs.  

## <a name="azure-powershell"></a>Azure PowerShell

> [!NOTE]
> Das folgende Skript verwendet das Azure PowerShell-Az-Modul. 

Das folgende PowerShell-Skript startet eine VM in einem Lab. [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) ist der Schwerpunkt dieses Skripts. Der **ResourceId**-Parameter ist die vollqualifizierte Ressourcen-ID für die VM in dem Lab. Im **Action**-Parameter werden je nach Bedarf die Optionen **Start** bzw. **Stop** festgelegt.

```powershell
# The id of the subscription
$subscriptionId = "111111-11111-11111-1111111"

# The name of the lab
$devTestLabName = "yourlabname"

# The name of the virtual machine to be started
$vMToStart = "vmname"

# The action on the virtual machine (Start or Stop)
$vmAction = "Start"

# Select the Azure subscription
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab information
$devTestLab = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $devTestLabName

# Start the VM and return a succeeded or failed status
$returnStatus = Invoke-AzResourceAction `
                    -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vMToStart" `
                    -Action $vmAction `
                    -Force

if ($returnStatus.Status -eq 'Succeeded') {
    Write-Output "##[section] Successfully updated DTL machine: $vMToStart, Action: $vmAction"
}
else {
    Write-Error "##[error]Failed to update DTL machine: $vMToStart, Action: $vmAction"
}
```


## <a name="azure-cli"></a>Azure CLI
Die [Azure CLI](/cli/azure/get-started-with-azure-cli) ist eine weitere Möglichkeit, das Starten und Beenden von VMs in DevTest Labs zu automatisieren. Die Azure CLI kann unter verschiedenen Betriebssystemen [installiert](/cli/azure/install-azure-cli) werden. Im folgenden Skript finden Sie Befehle zum Starten und Beenden einer VM in einem Lab. 

```azurecli
# Sign in to Azure
az login 

## Get the name of the resource group that contains the lab
az resource list --resource-type "Microsoft.DevTestLab/labs" --name "yourlabname" --query "[0].resourceGroup" 

## Start the VM
az lab vm start --lab-name yourlabname --name vmname --resource-group labResourceGroupName

## Stop the VM
az lab vm stop --lab-name yourlabname --name vmname --resource-group labResourceGroupName
```


## <a name="next-steps"></a>Nächste Schritte
Im folgenden Artikel erfahren Sie, wie Sie diese Vorgänge über das Azure-Portal ausführen: [Neustart einer VM](devtest-lab-restart-vm.md).
