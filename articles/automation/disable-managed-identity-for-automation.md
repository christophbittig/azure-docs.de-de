---
title: Deaktivieren einer systemseitig zugewiesenen verwalteten Identität für ein Azure Automation-Konto
description: In diesem Artikel wird erläutert, wie Sie eine systemseitig zugewiesene verwaltete Identität für ein Azure Automation deaktivieren.
services: automation
ms.subservice: process-automation
ms.date: 10/26/2021
ms.topic: conceptual
ms.openlocfilehash: b3bf648914635e6ea9345a234c49bfb64c757bc1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131435413"
---
# <a name="disable-system-assigned-managed-identity-for-azure-automation-account"></a>Deaktivieren einer systemseitig zugewiesenen verwalteten Identität für ein Azure Automation-Konto

Sie können eine systemseitig zugewiesene verwaltete Identität in Azure Automation über das Azure-Portal oder unter Verwendung der REST-API deaktivieren.

## <a name="disable-using-the-azure-portal"></a>Deaktivierung über das Azure-Portal

Unabhängig davon, wie die systemseitig zugewiesene verwaltete Identität ursprünglich eingerichtet wurde, kann die systemseitig zugewiesene verwaltete Identität mithilfe des Azure-Portals deaktiviert werden.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Navigieren Sie zu Ihrem Automation-Konto, und wählen Sie unter **Kontoeinstellungen** die Option **Identität** aus.

1. Klicken Sie auf der Registerkarte **Systemseitig zugewiesen** unterhalb der Schaltfläche **Status** auf **Aus** und dann auf **Speichern**. Wenn Sie aufgefordert werden, die Auswahl zu bestätigen, klicken Sie auf **Ja**.

Die systemseitig zugewiesene verwaltete Identität ist jetzt deaktiviert und kann nicht mehr auf die Zielressource zugreifen.

## <a name="disable-using-rest-api"></a>Deaktivierung mithilfe der REST-API

Syntax und Beispielschritte sind unten angegeben.

### <a name="request-body"></a>Anforderungstext

Der folgende Anforderungstext deaktiviert die systemseitig zugewiesene verwaltete Identität und entfernt alle benutzerseitig zugewiesenen verwalteten Identitäten mithilfe der HTTP-**PATCH**-Methode.

```json
{ 
 "identity": { 
   "type": "None" 
  } 
}

```

Wenn mehrere benutzerseitig zugewiesene Identitäten definiert sind und Sie diese beibehalten und nur die systemseitig zugewiesene Identität entfernen möchten, müssen Sie jede benutzerseitig zugewiesene Identität über eine durch Trennzeichen getrennte Liste angeben. Im folgenden Beispiel wird die HTTP-Methode **PATCH** verwendet.

```json
{ 
"identity" : {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/firstIdentity": {},
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/secondIdentity": {}
        }
    }
}
```

Nachfolgend wird der REST-API-Anforderungs-URI zum Senden der PATCH-Anforderung gezeigt.

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

### <a name="example"></a>Beispiel

Führen Sie die folgenden Schritte aus:

1. Kopieren Sie den Anforderungstext, und fügen Sie ihn je nach auszuführendem Vorgang in eine Datei namens `body_remove_sa.json` ein. Speichern Sie die Datei auf Ihrem lokalen Computer oder in einem Azure-Speicherkonto.

1. Melden Sie sich interaktiv mithilfe des Cmdlets [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) bei Azure an, und befolgen Sie die Anweisungen.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    
    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"
    ```

1. Geben Sie einen geeigneten Wert für die Variablen an, und führen Sie dann das Skript aus.

    ```powershell
    $subscriptionID = "subscriptionID"
    $resourceGroup = "resourceGroupName"
    $automationAccount = "automationAccountName"
    $file = "path\body_remove_sa.json"
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

    Abhängig von der verwendeten Syntax ist die Ausgabe `UserAssigned` oder leer.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Aktivieren von verwalteten Identitäten in Azure Automation finden Sie unter [Aktivieren und Verwenden der verwalteten Identität für Automation](enable-managed-identity-for-automation.md).

- Eine Übersicht über die Automation-Kontosicherheit finden Sie unter [Übersicht über die Automation-Kontoauthentifizierung.](automation-security-overview.md)