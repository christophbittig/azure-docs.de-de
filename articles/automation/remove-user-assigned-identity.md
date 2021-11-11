---
title: Entfernen einer benutzerseitig zugewiesenen verwalteten Identität für ein Azure Automation-Konto
description: In diesem Artikel wird das Entfernen einer benutzerseitig zugewiesenen verwalteten Identität für ein Azure Automation-Konto erläutert.
services: automation
ms.subservice: process-automation
ms.date: 10/26/2021
ms.topic: conceptual
ms.openlocfilehash: 11db735a21e4d4007c35f1a82f1e5dfadb377fd1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131432451"
---
# <a name="remove-user-assigned-managed-identity-for-azure-automation-account"></a>Entfernen einer benutzerseitig zugewiesenen verwalteten Identität für ein Azure Automation-Konto

Sie können eine benutzerseitig zugewiesene verwaltete Identität in Azure Automation über das Azure-Portal, über PowerShell, über die Azure REST-API oder über eine ARM-Vorlage (Azure Resource Manager) entfernen.

## <a name="remove-using-the-azure-portal"></a>Entfernen einer Identität über das Azure-Portal

Die benutzerseitig zugewiesene verwaltete Identität kann unabhängig davon, wie sie ursprünglich hinzugefügt wurde, über das Azure-Portal deaktiviert werden.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Navigieren Sie zu Ihrem Automation-Konto, und wählen Sie unter **Kontoeinstellungen** die Option **Identität** aus.

1. Wählen Sie die Registerkarte **Benutzerseitig zugewiesen** aus.

1. Wählen Sie die benutzerseitig zugewiesene verwaltete Identität aus, die aus der Liste entfernt werden soll.

1. Wählen Sie **Entfernen**. Wenn Sie aufgefordert werden, die Auswahl zu bestätigen, klicken Sie auf **Ja**.

Die benutzerseitig zugewiesene verwaltete Identität wurde entfernt und hat keinen Zugriff mehr auf die Zielressource.

## <a name="remove-using-powershell"></a>Entfernen unter Verwendung von PowerShell

Verwenden Sie das PowerShell-Cmdlet [Set-AzAutomationAccount](/powershell/module/az.automation/set-azautomationaccount), um alle benutzerseitig zugewiesenen verwalteten Identitäten zu entfernen und eine vorhandene systemseitig zugewiesene verwaltete Identität beizubehalten.

1. Melden Sie sich interaktiv mithilfe des Cmdlets [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) bei Azure an, und befolgen Sie die Anweisungen.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    ```

1. Geben Sie einen geeigneten Wert für die Variablen an, und führen Sie dann das Skript aus.

    ```powershell
    $resourceGroup = "resourceGroupName"
    $automationAccount = "automationAccountName"
    ```

1. Führen Sie [Set-AzAutomationAccount](/powershell/module/az.automation/set-azautomationaccount) aus.

    ```powershell
    # Removes all UAs, keeps SA
    $output = Set-AzAutomationAccount `
        -ResourceGroupName $resourceGroup `
        -Name $automationAccount `
        -AssignSystemIdentity 
    
    $output.identity.Type
    ```

    Die Ausgabe lautet: `SystemAssigned`.

## <a name="remove-using-rest-api"></a>Entfernen einer Identität über die REST-API

Sie können eine benutzerseitig zugewiesene verwaltete Identität mit dem folgenden REST-API-Aufruf und Beispiel aus dem Automation-Konto entfernen.

### <a name="request-body"></a>Anforderungstext

Szenario: Die systemseitig zugewiesene verwaltete Identität ist aktiviert oder muss aktiviert werden. Eine von vielen benutzerseitig zugewiesenen verwalteten Identitäten muss entfernt werden. In diesem Beispiel wird die benutzerseitig zugewiesene verwaltete Identität `firstIdentity` mithilfe der HTTP-Methode **PATCH** entfernt.

```json
{
  "identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.ManagedIdentity/userAssignedIdentities/firstIdentity": null
    }
  }
}
```

Szenario: Die systemseitig zugewiesene verwaltete Identität ist aktiviert oder muss aktiviert werden. Alle benutzerseitig zugewiesenen verwalteten Identitäten sollen über die HTTP-Methode **PUT** entfernt werden.

```json
{
  "identity": {
    "type": "SystemAssigned"
  }
}
```

Szenario: Die systemseitig zugewiesene verwaltete Identität ist deaktiviert oder muss deaktiviert werden. Eine von vielen benutzerseitig zugewiesenen verwalteten Identitäten muss entfernt werden. In diesem Beispiel wird die benutzerseitig zugewiesene verwaltete Identität `firstIdentity` mithilfe der HTTP-Methode **PATCH** entfernt.

```json
{
  "identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.ManagedIdentity/userAssignedIdentities/firstIdentity": null
    }
  }
}

```

Szenario: Die systemseitig zugewiesene verwaltete Identität ist deaktiviert oder muss deaktiviert werden. Alle benutzerseitig zugewiesenen verwalteten Identitäten sollen über die HTTP-Methode **PUT** entfernt werden.

```json
{
  "identity": {
    "type": "None"
  }
}
```

Nachfolgend ist der REST-API-Anforderungs-URI zum Senden der PATCH-Anforderung gezeigt.

```http
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

### <a name="example"></a>Beispiel

Führen Sie die folgenden Schritte aus:

1. Kopieren Sie den Anforderungstext, und fügen Sie ihn je nach auszuführendem Vorgang in eine Datei `body_remove_ua.json` ein. Nehmen Sie alle erforderlichen Änderungen vor, und speichern Sie die Datei dann auf Ihrem lokalen Computer oder in einem Azure-Speicherkonto.

1. Melden Sie sich interaktiv mithilfe des Cmdlets [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) bei Azure an, und befolgen Sie die Anweisungen.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount -Subscription
    }
    ```

1. Geben Sie einen geeigneten Wert für die Variablen an, und führen Sie dann das Skript aus.

    ```powershell
    $subscriptionID = "subscriptionID"
    $resourceGroup = "resourceGroupName"
    $automationAccount = "automationAccountName"
    $file = "path\body_remove_ua.json"
    ```

1. In diesem Beispiel wird das PowerShell-Cmdlet [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) verwendet, um die PATCH-Anforderung an Ihr Automation-Konto zu senden.

    ```powershell
    # build URI
    $URI = "https://management.azure.com/subscriptions/$subscriptionID/resourceGroups/$resourceGroup/providers/Microsoft.Automation/automationAccounts/$automationAccount`?api-version=2020-01-13-preview"
    
    # build body
    $body = Get-Content $file
    
    # obtain access token
    $azContext = Get-AzContext
    $azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
    $profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
    $token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
    $authHeader = @{
        'Content-Type'='application/json'
        'Authorization'='Bearer ' + $token.AccessToken
    }
    
    # Invoke the REST API
    Invoke-RestMethod -Uri $URI -Method PATCH -Headers $authHeader -Body $body
    
    # Confirm removal
    (Get-AzAutomationAccount `
        -ResourceGroupName $resourceGroup `
        -Name $automationAccount).Identity.Type
    ```

   Abhängig von der verwendeten Syntax ist die Ausgabe `SystemAssignedUserAssigned`, `SystemAssigned`, `UserAssigned` oder leer.

## <a name="remove-using-azure-resource-manager-template"></a>Entfernen einer Identität über eine Azure Resource Manager-Vorlage

Wenn Sie die benutzerseitig zugewiesene verwaltete Identität für Ihr Automation-Konto über eine Azure Resource Manager-Vorlage hinzugefügt haben, können Sie sie entfernen, indem Sie die Vorlage ändern und dann erneut ausführen.

Szenario: Die systemseitig zugewiesene verwaltete Identität ist aktiviert oder muss aktiviert werden. Eine von zwei benutzerseitig zugewiesenen verwalteten Identitäten muss entfernt werden. Mit diesem Syntaxausschnitt werden **alle** benutzerseitig zugewiesenen verwalteten Identitäten **mit Ausnahme** der Identität entfernt, die als Parameter an die Vorlage übergeben wurde.

```json
...
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('userAssignedOne'))]": {}
    }
},
...
```

Szenario: Die systemseitig zugewiesene verwaltete Identität ist aktiviert oder muss aktiviert werden. Alle benutzerseitig zugewiesenen verwalteten Identitäten müssen entfernt werden.

```json
...
"identity": {
    "type": "SystemAssigned"
},
...
```

Szenario: Die systemseitig zugewiesene verwaltete Identität ist deaktiviert oder muss deaktiviert werden. Eine von zwei benutzerseitig zugewiesenen verwalteten Identitäten muss entfernt werden. Mit diesem Syntaxausschnitt werden **alle** benutzerseitig zugewiesenen verwalteten Identitäten **mit Ausnahme** der Identität entfernt, die als Parameter an die Vorlage übergeben wurde.

```json
...
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('userAssignedOne'))]": {}
    }
},
...
```

Verwenden Sie zur Überprüfung das Cmdlet [Get-AzAutomationAccount](/powershell/module/az.automation/get-azautomationaccount). Abhängig von der verwendeten Syntax ist die Ausgabe `SystemAssignedUserAssigned`, `SystemAssigned` oder `UserAssigned`.

```powershell
(Get-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount).Identity.Type
```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Aktivieren von verwalteten Identitäten in Azure Automation finden Sie unter [Aktivieren und Verwenden der verwalteten Identität für Automation](enable-managed-identity-for-automation.md).

- Eine Übersicht über die Automation-Kontosicherheit finden Sie unter [Übersicht über die Automation-Kontoauthentifizierung.](automation-security-overview.md)
