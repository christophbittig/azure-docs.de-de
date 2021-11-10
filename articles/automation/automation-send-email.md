---
title: Senden einer E-Mail aus einem Azure Automation-Runbook
description: Dieser Artikel beschreibt, wie Sie eine E-Mail aus einem Runbook heraus versenden können.
services: automation
ms.subservice: process-automation
ms.date: 09/21/2021
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c5f89a755cecd2dcf36d3a8a41711fe941108ffa
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131427264"
---
# <a name="send-an-email-from-am-automation-runbook"></a>Senden einer E-Mail aus einem Automation-Runbook

Sie können unter Verwendung von PowerShell und [SendGrid](https://sendgrid.com/solutions) eine E-Mail aus einem Runbook senden. 

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

* [Ein SendGrid-Konto](https://docs.sendgrid.com/for-developers/partners/microsoft-azure-2021#create-a-sendgrid-account).
* SendGrid-Absenderüberprüfung. Entweder [„Domäne“ oder „Einzelner Absender“](https://sendgrid.com/docs/for-developers/sending-email/sender-identity/) 
* Ihr [SendGrid-API-Schlüssel](https://docs.sendgrid.com/for-developers/partners/microsoft-azure-2021#to-find-your-sendgrid-api-key).

* Ein Azure Automation-Konto mit mindestens einer benutzerseitig zugewiesenen verwalteten Identität. Weitere Informationen finden Sie unter [Aktivieren von verwalteten Identitäten](./quickstarts/enable-managed-identity.md).
* Az-Module: `Az.Accounts` und `Az.KeyVault`, importiert in das Automation-Konto. Weitere Informationen finden Sie unter [Importieren von Az-Modulen](./shared-resources/modules.md#import-az-modules).
* Das auf Ihrem Computer installierte [PowerShell-Modul „Azure Az“](/powershell/azure/new-azureps-module-az). Informationen zur einer Installation oder einem Upgrade finden Sie unter [Installieren des PowerShell-Moduls „Azure Az“](/powershell/azure/install-az-ps).

## <a name="create-an-azure-key-vault"></a>Erstellen einer Azure Key Vault-Instanz

Erstellen Sie eine Azure Key Vault-Instanz und eine [Key Vault-Zugriffsrichtlinie](../key-vault/general/assign-access-policy-portal.md), die es der Anmeldeinformation gestattet, Schlüsseltresorgeheimnisse aus dem angegebenen Schlüsseltresor abzurufen bzw. dort festzulegen.

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
    $resourceGroup = "<Resource group>"
    $automationAccount = "<Automation account>"
    $region = "<Region>"
    $SendGridAPIKey = "<SendGrid API key>"
    $VaultName = "<A universally unique vault name>"

    $userAssignedManagedIdentity = "<User-assigned managed identity>"
    ```

1. Erstellen einer Key Vault-Instanz und Zuweisen von Berechtigungen

    ```powershell
    # Create the new key vault
    $newKeyVault = New-AzKeyVault `
        -VaultName $VaultName `
        -ResourceGroupName $resourceGroup `
        -Location $region

    $resourceId = $newKeyVault.ResourceId
    
    # Convert the SendGrid API key into a SecureString
    $Secret = ConvertTo-SecureString -String $SendGridAPIKey `
        -AsPlainText -Force

    Set-AzKeyVaultSecret -VaultName $VaultName `
        -Name 'SendGridAPIKey' `
        -SecretValue $Secret
    
    # Grant Key Vault access to the Automation account's system-assigned managed identity.
    $SA_PrincipalId = (Get-AzAutomationAccount `
        -ResourceGroupName $resourceGroup `
        -Name $automationAccount).Identity.PrincipalId

    Set-AzKeyVaultAccessPolicy `
        -VaultName $vaultName `
        -ObjectId $SA_PrincipalId `
        -PermissionsToSecrets Set, Get

    # Grant Key Vault access to the user-assigned managed identity.
    $UAMI = Get-AzUserAssignedIdentity `
        -ResourceGroupName $resourceGroup `
        -Name $userAssignedManagedIdentity

    Set-AzKeyVaultAccessPolicy `
        -VaultName $vaultName `
        -ObjectId $UAMI.PrincipalId `
        -PermissionsToSecrets Set, Get
    ```

   Weitere Möglichkeiten zum Erstellen einer Azure Key Vault-Instanz und zum Speichern eines Geheimnisses finden Sie unter [Schnellstarts zu Key Vault](../key-vault/index.yml).

## <a name="assign-permissions-to-managed-identities"></a>Zuweisen von Berechtigungen zu verwalteten Identitäten

Weisen Sie der entsprechenden [verwalteten Identität](./automation-security-overview.md#managed-identities) Berechtigungen zu. Das Runbook kann die systemseitig zugewiesene verwaltete Identität des Automation-Kontos oder eine benutzerseitig zugewiesene verwaltete Identität verwenden. Schritte zur Zuweisung von Berechtigungen für jede Identität sind angegeben. In den folgenden Schritten wird PowerShell verwendet. Informationen zur Verwendung des Azure-Portals finden Sie unter [Zuweisen von Azure-Rollen über das Azure-Portal](./../role-based-access-control/role-assignments-portal.md).

1. Verwenden Sie das PowerShell-Cmdlet [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment), um der systemseitig zugewiesenen verwalteten Identität eine Rolle zu zuweisen.

    ```powershell
    New-AzRoleAssignment `
        -ObjectId $SA_PrincipalId `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName "Reader"
    ```

1. Weisen Sie einer benutzerseitig zugewiesenen verwalteten Identität eine Rolle zu.

    ```powershell
    New-AzRoleAssignment `
        -ObjectId $UAMI.PrincipalId`
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName "Reader"
    ```

1. Zeigen Sie für die systemseitig zugewiesene verwaltete Identität den Wert `ClientId` an, und speichern Sie ihn für die spätere Verwendung.

   ```powershell
   $UAMI.ClientId
   ```

## <a name="create-the-runbook-to-send-an-email"></a>Erstellen des Runbooks zum Senden einer E-Mail

Wenn Sie einen Schlüsseltresor erstellt und Ihren `SendGrid`-API-Schlüssel gespeichert haben, können Sie das Runbook erstellen, das den API-Schlüssel abruft und eine E-Mail sendet. Wir verwenden ein Runbook, das die [systemseitig zugewiesene verwaltete Identität](./automation-security-overview.md#managed-identities) für die Authentifizierung bei Azure verwendet, um das Geheimnis aus Azure Key Vault abzurufen. Wir nennen das Runbook **Send-GridMailMessage**. Sie können das PowerShell-Skript ändern, das für verschiedene Szenarien verwendet wird.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrem Automation-Konto.

1. Wählen Sie auf Ihrer offenen Automation-Kontoseite unter **Prozessautomatisierung** die Option **Runbooks** aus.

1. Wählen Sie **+ Runbook erstellen** aus.
    1. Nennen Sie das Runbook `Send-GridMailMessage`.
    1. Wählen Sie in der Dropdownliste **Runbooktyp** den Wert **PowerShell** aus.
    1. Wählen Sie **Erstellen** aus.

   ![Runbook erstellen](./media/automation-send-email/automation-send-email-runbook.png)

1. Das Runbook wird erstellt, und die Seite „PowerShell-Runbook bearbeiten“ wird geöffnet.
   ![Bearbeiten des Runbooks](./media/automation-send-email/automation-send-email-edit.png)

1. Kopieren Sie das folgende PowerShell-Beispiel auf die Seite „Bearbeiten“. Stellen Sie sicher, dass der `VaultName` den Namen angibt, den Sie für Ihren Schlüsseltresor ausgewählt haben.

    ```powershell
    Param(
      [Parameter(Mandatory=$True)]
      [String] $destEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $fromEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $subject,
      [Parameter(Mandatory=$True)]
      [String] $content,
      [Parameter(Mandatory=$True)]
      [String] $ResourceGroupName
    )

    # Ensures you do not inherit an AzContext in your runbook
    Disable-AzContextAutosave -Scope Process
    
    # Connect to Azure with system-assigned managed identity
    $AzureContext = (Connect-AzAccount -Identity).context

    # set and store context
    $AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext 

    $VaultName = "<Enter your vault name>"

    $SENDGRID_API_KEY = Get-AzKeyVaultSecret `
        -VaultName $VaultName `
        -Name "SendGridAPIKey" `
        -AsPlainText -DefaultProfile $AzureContext

    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("Authorization", "Bearer " + $SENDGRID_API_KEY)
    $headers.Add("Content-Type", "application/json")

    $body = @{
    personalizations = @(
        @{
            to = @(
                    @{
                        email = $destEmailAddress
                    }
            )
        }
    )
    from = @{
        email = $fromEmailAddress
    }
    subject = $subject
    content = @(
        @{
            type = "text/plain"
            value = $content
        }
    )
    }

    $bodyJson = $body | ConvertTo-Json -Depth 4

    $response = Invoke-RestMethod -Uri https://api.sendgrid.com/v3/mail/send -Method Post -Headers $headers -Body $bodyJson
    ```

1. Wenn Sie möchten, dass das Runbook mit der systemseitig zugewiesenen verwalteten Identität ausgeführt wird, lassen Sie den Code unverändert. Wenn Sie lieber eine benutzerseitig zugewiesene verwaltete Identität verwenden möchten, gehen Sie wie folgt vor:
    1. Entfernen Sie `$AzureContext = (Connect-AzAccount -Identity).context` aus Zeile 18.
    1. Fügen Sie stattdessen `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context` ein.
    1. Geben Sie die zuvor abgerufene Client-ID ein.

1. Wählen Sie **Speichern**, anschließend **Veröffentlichen** und dann **Ja** aus, wenn Sie dazu aufgefordert werden.

Führen Sie die Schritte unter [Verwalten von Runbooks in Azure Automation](manage-runbooks.md#test-a-runbook) oder [Starten eines Runbooks in Azure Automation](start-runbooks.md) aus, um zu überprüfen, ob das Runbook ausgeführt wird.

Wird die Test-E-Mail zuerst nicht angezeigt, überprüfen Sie den **Junk-** und **Spam**-Ordner.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

1. Wenn das Runbook nicht mehr benötigt wird, wählen Sie es in der Runbookliste aus, und klicken Sie auf **Löschen**.

1. Löschen Sie den Schlüsseltresor mithilfe des Cmdlets [Remove-AzKeyVault](/powershell/module/az.keyvault/remove-azkeyvault).

    ```powershell
    $VaultName = "<your KeyVault name>"
    $resourceGroup = "<your ResourceGroup name>"
    Remove-AzKeyVault -VaultName $VaultName -ResourceGroupName $resourceGroup
    ```

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Senden von Runbookauftragsdaten an Ihren Log Analytics-Arbeitsbereich finden Sie unter [Weiterleiten von Azure Automation-Auftragsdaten an Azure Monitor-Protokolle](automation-manage-send-joblogs-log-analytics.md).
* Informationen zur Überwachung von Metriken und Protokollen auf Basisebene finden Sie unter [Verwenden einer Warnung zum Auslösen eines Azure Automation-Runbooks](automation-create-alert-triggered-runbook.md).
