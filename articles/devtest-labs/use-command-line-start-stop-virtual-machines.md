---
title: Starten und Beenden von VMs mit Befehlszeilentools
description: Erfahren Sie, wie Sie Befehlszeilentools verwenden, um virtuelle Computer in Azure DevTest Labs zu starten und zu beenden.
ms.topic: how-to
ms.date: 10/22/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 58440d00f888816f95aac0159063a7b6d6fc5289
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131430189"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>Starten und Beenden von virtuellen Computern in Azure DevTest Labs mit Befehlszeilentools

In diesem Artikel erfahren Sie, wie Sie eine Lab-VM in Azure DevTest Labs starten oder beenden. Zur Automatisierung dieser Vorgänge können Sie Azure PowerShell- oder Azure CLI-Skripte erstellen. 

## <a name="prerequisites"></a>Voraussetzungen
- Wenn Sie PowerShell verwenden, müssen Sie das [Az-Modul](/powershell/azure/new-azureps-module-az) auf Ihrer Arbeitsstation installieren. Stellen Sie sicher, dass Sie über die aktuelle Version verfügen. Führen Sie gegebenenfalls `Update-Module -Name Az` aus.

- Wenn Sie die Azure-Befehlszeilenschnittstelle verwenden möchten, diese aber noch nicht installiert haben, lesen Sie [Installieren der Azure CLI](/cli/azure/install-azure-cli).

- Ein virtueller Computer in einem Lab von DevTest Labs

## <a name="overview"></a>Übersicht

Mit Azure DevTest Labs können Sie schnelle, einfache und schlanke Dev/Test-Umgebungen erstellen. Labs macht es Ihnen möglich, Kosten zu verwalten, schnell VMs zu erstellen und Verschwendung zu reduzieren. Sie können die Features im Azure-Portal nutzen, um VMs zu bestimmten Zeiten automatisch zu starten und zu beenden. Sie sollten eventuell jedoch das Starten und Beenden von VMs über Skripts automatisieren. Folgende Beispiele beschreiben Situationen, in denen das Ausführen dieser Tasks mithilfe von Skripts nützlich ist.

- Bei der Verwendung dreistufiger Anwendungen im Rahmen einer Testumgebung, und wenn die Stufen in einer bestimmten Reihenfolge gestartet werden müssen. 
- Zum Beenden einer VM, sobald ein benutzerdefiniertes Kriterium erfüllt ist, um Geld zu sparen. 
- Als Aufgabe innerhalb eines Continuous Integration und Continuous Delivery-Workflows, die am Anfang gestartet werden soll, und dann die VMs beenden soll, wenn der Prozess abgeschlossen ist. Ein Beispiel für diesen Workflow ist die benutzerdefinierte Image Factory in Azure DevTest Labs.  

## <a name="azure-powershell"></a>Azure PowerShell

Das folgende PowerShell-Skript kann eine VM in einem Lab starten oder beenden. [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) ist der Schwerpunkt dieses Skripts. Der **ResourceId**-Parameter ist die vollqualifizierte Ressourcen-ID für die VM in dem Lab. Im **Action**-Parameter werden je nach Bedarf die Optionen **Start** bzw. **Stop** festgelegt.

1. Melden Sie sich auf Ihrer Arbeitsstation mit dem PowerShell-Cmdlet [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) bei Ihrem Azure-Abonnement an, und befolgen Sie die Anweisungen auf dem Bildschirm.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    
    # If you have multiple subscriptions, set the one to use
    # Set-AzContext -SubscriptionId "<SUBSCRIPTIONID>"
    ```

1. Geben Sie einen geeigneten Wert für die Variablen an, und führen Sie dann das Skript aus.

    ```powershell
    $devTestLabName = "yourlabname"
    $vMToStart = "vmname"
    
    # The action on the virtual machine (Start or Stop)
    $vmAction = "Start"
    ```

1. Starten oder beenden Sie die VM basierend auf dem Wert, den Sie an `$vmAction` übergeben haben.

    ```powershell
    # Get the lab information
    $devTestLab = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $devTestLabName
    
    # Start or stop the VM and return a succeeded or failed status
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

Die [Azure CLI](/cli/azure/get-started-with-azure-cli) ist eine weitere Möglichkeit, das Starten und Beenden von VMs in DevTest Labs zu automatisieren. Im folgenden Skript finden Sie Befehle zum Starten und Beenden einer VM in einem Lab. Die Verwendung von Variablen in diesem Abschnitt basiert auf einer Windows-Umgebung. Leichte Variationen werden für bash- oder andere Umgebungen erforderlich.

1. Ersetzen Sie `SubscriptionID`, `yourlabname`, `yourVM` und `action` durch die entsprechenden Werte. Führen Sie das Skript dann aus.

    ```azurecli
    set SUBSCIPTIONID=SubscriptionID
    set DEVTESTLABNAME=yourlabname
    set VMNAME=yourVM
    
    REM The action on the virtual machine (Start or Stop)
    set ACTION=action
    ```

1. Melden Sie sich bei Ihrem Azure-Abonnement an, und rufen Sie den Namen der Ressourcengruppe ab, die das Lab enthält.

    ```azurecli
    az login
    
    REM If you have multiple subscriptions, set the one to use
    REM az account set --subscription %SUBSCIPTIONID%

    az resource list --resource-type "Microsoft.DevTestLab/labs" --name %DEVTESTLABNAME% --query "[0].resourceGroup"
    ```

1. Ersetzen Sie `resourceGroup` durch den Wert, den Sie im vorherigen Schritt erhalten haben. Führen Sie das Skript dann aus.

    ```azurecli
    set RESOURCEGROUP=resourceGroup
    ```

1. Starten oder beenden Sie die VM basierend auf dem Wert, den Sie an `ACTION` übergeben haben.

    ```azurecli
    az lab vm %ACTION% --lab-name %DEVTESTLABNAME% --name %VMNAME% --resource-group %RESOURCEGROUP%
    ```

## <a name="next-steps"></a>Nächste Schritte

Im folgenden Artikel erfahren Sie, wie Sie diese Vorgänge über das Azure-Portal ausführen: [Neustart einer VM](devtest-lab-restart-vm.md).
