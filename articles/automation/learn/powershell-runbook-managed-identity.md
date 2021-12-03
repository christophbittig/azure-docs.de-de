---
title: Erstellen eines PowerShell-Runbooks mithilfe einer verwalteten Identität in Azure Automation
description: In diesem Tutorial erfahren Sie, wie Sie verwaltete Identitäten mit einem PowerShell-Runbook in Azure Automation verwenden.
services: automation
ms.subservice: process-automation
ms.date: 09/28/2021
ms.topic: tutorial
ms.openlocfilehash: 31f9e30d938f70852eac5c5185626828e5305897
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131427169"
---
# <a name="tutorial-create-automation-powershell-runbook-using-managed-identity"></a>Tutorial: Erstellen eines Automation PowerShell-Runbooks mithilfe einer verwalteten Identität

Dieses Tutorial führt Sie schrittweise durch das Erstellen eines [PowerShell-Runbooks](../automation-runbook-types.md#powershell-runbooks) in Azure Automation, das eine [verwaltete Identität](../automation-security-overview.md#managed-identities) anstelle des ausführenden Kontos verwendet, um mit Ressourcen zu interagieren. PowerShell-Runbooks basieren auf Windows PowerShell. Durch eine verwaltete Entität aus Azure Active Directory (Azure AD) kann Ihr Runbook mühelos auf andere durch Azure AD geschützte Ressourcen zugreifen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Zuweisen von Berechtigungen zu verwalteten Identitäten
> * Erstellen eines PowerShell-Runbooks

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure Automation-Konto mit mindestens einer benutzerseitig zugewiesenen verwalteten Identität. Weitere Informationen finden Sie unter [Verwenden einer benutzerseitig zugewiesenen verwalteten Identität für ein Azure Automation-Konto](../add-user-assigned-identity.md).
* Az-Module: `Az.Accounts`, `Az.Automation`, `Az.ManagedServiceIdentity` und `Az.Compute`, importiert in das Automation-Konto. Weitere Informationen finden Sie unter [Importieren von Az-Modulen](../shared-resources/modules.md#import-az-modules).
* Das auf Ihrem Computer installierte [PowerShell-Modul „Azure Az“](/powershell/azure/new-azureps-module-az). Informationen zur einer Installation oder einem Upgrade finden Sie unter [Installieren des PowerShell-Moduls „Azure Az“](/powershell/azure/install-az-ps). `Az.ManagedServiceIdentity` ist ein Vorschaumodul und wird nicht als Teil des Az-Moduls installiert. Führen Sie zur Installation `Install-Module -Name Az.ManagedServiceIdentity` aus.
* Von eine [virtuellen Azure-Computer](../../virtual-machines/windows/quick-create-powershell.md). Da Sie diesen Computer starten und beenden, sollte es sich nicht um eine Produktions-VM handeln.
* Allgemeine Vertrautheit mit [Automation-Runbooks](../manage-runbooks.md).

## <a name="assign-permissions-to-managed-identities"></a>Zuweisen von Berechtigungen zu verwalteten Identitäten

Weisen Sie den verwalteten Identitäten Berechtigungen zu, damit sie einen virtuellen Computer beenden und starten können.

1. Melden Sie sich interaktiv mithilfe des Cmdlets [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) bei Azure an, und befolgen Sie die Anweisungen.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    
    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId <SUBSCRIPTIONID>
    ```

1. Geben Sie einen geeigneten Wert für die unten stehenden Variablen an, und führen Sie dann das Skript aus.

    ```powershell
    $resourceGroup = "resourceGroupName"
    
    # These values are used in this tutorial
    $automationAccount = "xAutomationAccount"
    $userAssignedManagedIdentity = "xUAMI"
    ```

1. Verwenden Sie das PowerShell-Cmdlet [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment), um der systemseitig zugewiesenen verwalteten Identität eine Rolle zu zuweisen.

    ```powershell
    $role1 = "DevTest Labs User"
    
    $SAMI = (Get-AzAutomationAccount -ResourceGroupName $resourceGroup -Name $automationAccount).Identity.PrincipalId
    New-AzRoleAssignment `
        -ObjectId $SAMI `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName $role1
    ```

1. Dieselbe Rollenzuweisung ist für die benutzerseitig zugewiesene verwaltete Identität erforderlich.

    ```powershell
    $UAMI = (Get-AzUserAssignedIdentity -ResourceGroupName $resourceGroup -Name $userAssignedManagedIdentity).PrincipalId
    New-AzRoleAssignment `
        -ObjectId $UAMI `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName $role1
    ```

1. Zusätzliche Berechtigungen sind für die systemseitig zugewiesene verwaltete Identität erforderlich, um die Cmdlets `Get-AzUserAssignedIdentity` und `Get-AzAutomationAccount` wie in diesem Tutorial verwendet auszuführen.

    ```powershell
    $role2 = "Reader"
    New-AzRoleAssignment `
        -ObjectId $SAMI `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName $role2
    ```

## <a name="create-powershell-runbook"></a>Erstellen eines PowerShell-Runbooks

Erstellen Sie ein Runbook, das die Ausführung durch eine der beiden verwalteten Identitäten zulässt. Das Runbook startet einen beendeten virtuellen Computer oder beendet einen ausgeführten virtuellen Computer.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, und navigieren Sie zu Ihrem Automation-Konto.

1. Wählen Sie unter **Prozessautomatisierung** die Option **Runbooks** aus.

1. Wählen Sie **Runbook erstellen** aus.
    1. Nennen Sie das Runbook `miTesting`.
    1. Wählen Sie in der Dropdownliste **Runbook-Typ** den Wert **PowerShell** aus.
    1. Klicken Sie auf **Erstellen**.

1. Fügen Sie im Runbook-Editor den folgenden Code ein:

    ```powershell
    Param(
     [string]$resourceGroup,
     [string]$VMName,
     [string]$method,
     [string]$UAMI 
    )
    
    $automationAccount = "xAutomationAccount"
    
    # Ensures you do not inherit an AzContext in your runbook
    Disable-AzContextAutosave -Scope Process | Out-Null
    
    # Connect using a Managed Service Identity
    try {
            $AzureContext = (Connect-AzAccount -Identity).context
        }
    catch{
            Write-Output "There is no system-assigned user identity. Aborting."; 
            exit
        }
    
    # set and store context
    $AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription `
        -DefaultProfile $AzureContext
    
    if ($method -eq "SA")
        {
            Write-Output "Using system-assigned managed identity"
        }
    elseif ($method -eq "UA")
        {
            Write-Output "Using user-assigned managed identity"
    
            # Connects using the Managed Service Identity of the named user-assigned managed identity
            $identity = Get-AzUserAssignedIdentity -ResourceGroupName $resourceGroup `
                -Name $UAMI -DefaultProfile $AzureContext
    
            # validates assignment only, not perms
            if ((Get-AzAutomationAccount -ResourceGroupName $resourceGroup `
                    -Name $automationAccount `
                    -DefaultProfile $AzureContext).Identity.UserAssignedIdentities.Values.PrincipalId.Contains($identity.PrincipalId))
                {
                    $AzureContext = (Connect-AzAccount -Identity -AccountId $identity.ClientId).context
    
                    # set and store context
                    $AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext
                }
            else {
                    Write-Output "Invalid or unassigned user-assigned managed identity"
                    exit
                }
        }
    else {
            Write-Output "Invalid method. Choose UA or SA."
            exit
         }
    
    # Get current state of VM
    $status = (Get-AzVM -ResourceGroupName $resourceGroup -Name $VMName `
        -Status -DefaultProfile $AzureContext).Statuses[1].Code
    
    Write-Output "`r`n Beginning VM status: $status `r`n"
    
    # Start or stop VM based on current state
    if($status -eq "Powerstate/deallocated")
        {
            Start-AzVM -Name $VMName -ResourceGroupName $resourceGroup -DefaultProfile $AzureContext
        }
    elseif ($status -eq "Powerstate/running")
        {
            Stop-AzVM -Name $VMName -ResourceGroupName $resourceGroup -DefaultProfile $AzureContext -Force
        }
    
    # Get new state of VM
    $status = (Get-AzVM -ResourceGroupName $resourceGroup -Name $VMName -Status `
        -DefaultProfile $AzureContext).Statuses[1].Code  
    
    Write-Output "`r`n Ending VM status: $status `r`n `r`n"
    
    Write-Output "Account ID of current context: " $AzureContext.Account.Id
    ```

1. Überarbeiten Sie nach Bedarf im Editor in Zeile 8 den Wert für die Variable `$automationAccount`.

1. Wählen Sie **Speichern** und anschließend **Testbereich** aus.

1. Geben Sie für die Parameter `RESOURCEGROUP` und `VMNAME` die geeigneten Werte ein. Geben Sie `SA` für den Parameter `METHOD` ein und `xUAMI` für den Parameter `UAMI`. Das Runbook versucht, den Betriebszustand Ihres virtuellen Computers mithilfe der systemseitig zugewiesenen verwalteten Identität zu ändern.

1. Wählen Sie **Starten** aus. Nach Abschluss des Runbooks sollte die Ausgabe in etwa wie folgt aussehen:

    ```output
     Beginning VM status: PowerState/deallocated
    
    OperationId : 5b707401-f415-4268-9b43-be1f73ddc54b
    Status      : Succeeded
    StartTime   : 8/3/2021 10:52:09 PM
    EndTime     : 8/3/2021 10:52:50 PM
    Error       : 
    Name        : 
    
     Ending VM status: PowerState/running 
     
    Account ID of current context: 
    MSI@50342
    ```

1. Ändern Sie den Wert des Parameters `METHOD` in `UA`.

1. Wählen Sie **Starten** aus. Das Runbook versucht, den Betriebszustand Ihres virtuellen Computers mithilfe der benannten benutzerseitig zugewiesenen verwalteten Identität zu ändern. Nach Abschluss des Runbooks sollte die Ausgabe in etwa wie folgt aussehen:

    ```output
    Using user-assigned managed identity
    
     Beginning VM status: PowerState/running 
    
    OperationId : 679fcadf-d0b9-406a-9282-66bc211a9fbf
    Status      : Succeeded
    StartTime   : 8/3/2021 11:06:03 PM
    EndTime     : 8/3/2021 11:06:49 PM
    Error       : 
    Name        : 
    
     Ending VM status: PowerState/deallocated 
     
    Account ID of current context: 
    9034f5d3-c46d-44d4-afd6-c78aeab837ea
    ```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Um alle nicht mehr benötigten Ressourcen zu entfernen, führen Sie das folgende Runbook aus.

```powershell
#Remove runbook
Remove-AzAutomationRunbook `
    -ResourceGroupName $resourceGroup `
    -AutomationAccountName $automationAccount `
    -Name "miTesting" `
    -Force

# Remove role assignments
Remove-AzRoleAssignment `
    -ObjectId $UAMI `
    -ResourceGroupName $resourceGroup `
    -RoleDefinitionName $role1

Remove-AzRoleAssignment `
    -ObjectId $SAMI `
    -ResourceGroupName $resourceGroup `
    -RoleDefinitionName $role2

Remove-AzRoleAssignment `
    -ObjectId $SAMI `
    -ResourceGroupName $resourceGroup `
    -RoleDefinitionName $role1
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie ein [PowerShell-Runbook](../automation-runbook-types.md#powershell-runbooks) in Azure Automation erstellt, das eine [verwaltete Identität](../automation-security-overview.md#managed-identities) anstelle des ausführenden Kontos verwendet hat, um mit Ressourcen zu interagieren. Informationen zu PowerShell-Workflow-Runbooks finden Sie unter:

> [!div class="nextstepaction"]
> [Tutorial: Erstellen eines PowerShell-Workflow-Runbooks](automation-tutorial-runbook-textual.md)