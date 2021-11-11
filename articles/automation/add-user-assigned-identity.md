---
title: Verwenden einer benutzerseitig zugewiesenen verwalteten Identität für ein Azure Automation-Konto
description: In diesem Artikel wird beschrieben, wie Sie eine benutzerseitig verwaltete Identität für Azure Automation-Konten einrichten.
services: automation
ms.subservice: process-automation
ms.date: 10/26/2021
ms.topic: conceptual
ms.openlocfilehash: 033f25f5d5902b339a2777cffc8c526a459ca587
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131435527"
---
# <a name="using-a-user-assigned-managed-identity-for-an-azure-automation-account"></a>Verwenden einer benutzerseitig zugewiesenen verwalteten Identität für ein Azure Automation-Konto

In diesem Artikel erfahren Sie, wie eine benutzerseitig zugewiesene verwaltete Identität für ein Azure Automation-Konto hinzugefügt und für den Zugriff auf andere Ressourcen verwendet wird. Weitere Informationen zur Funktionsweise verwalteter Identitäten mit Azure Automation finden Sie unter [Verwaltete Identitäten](automation-security-overview.md#managed-identities).

> [!NOTE]
> Benutzerseitig zugewiesene verwaltete Identitäten werden nur für Cloudaufträge unterstützt.  

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure Automation-Konto. Eine Anleitung hierzu finden Sie unter [Erstellen eines Azure Automation-Kontos](./quickstarts/create-account-portal.md).

- Eine systemseitig zugewiesene verwaltete Identität. Anleitungen finden Sie unter [Verwenden einer systemseitig zugewiesenen verwalteten Identität für ein Azure Automation-Konto](enable-managed-identity-for-automation.md).

- Eine vom Benutzer zugewiesene verwaltete Identität. Anleitungen finden Sie unter [Erstellen einer benutzerseitig zugewiesenen verwalteten Identität](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

- Die benutzerseitig zugewiesene verwaltete Identität und die Azure-Zielressourcen, die Ihr Runbook mit dieser Identität verwaltet, müssen sich im selben Azure-Abonnement befinden.

- Die neueste Version der Azure-Kontomodule. Derzeit ist dies 2.2.8. (Ausführliche Informationen zu dieser Version finden Sie unter [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/).)

- Eine Azure-Ressource, auf die Sie über Ihr Automation-Runbook zugreifen möchten. Für diese Ressource muss eine Rolle für die benutzerseitig zugewiesene verwaltete Identität definiert sein, mit der das Automation-Runbook den Zugriff auf die Ressource authentifizieren kann. Zum Hinzufügen von Rollen müssen Sie Besitzer der Ressource im entsprechenden Azure AD sein.

- Wenn Sie Hybridaufträge mit einer benutzerseitig zugewiesenen verwalteten Identität ausführen möchten, aktualisieren Sie die Hybrid Runbook Worker auf die neueste Version. Mindestens erforderliche Version:

  - Windows Hybrid Runbook Worker: Version 7.3.1125.0
  - Linux Hybrid Runbook Worker: Version 1.7.4.0

## <a name="add-user-assigned-managed-identity-for-azure-automation-account"></a>Hinzufügen einer benutzerseitig zugewiesenen verwalteten Identität für ein Azure Automation-Konto

Sie können eine benutzerseitig zugewiesene verwaltete Identität für ein Azure Automation-Konto hinzufügen, indem Sie das Azure-Portal, PowerShell, die Azure-REST-API oder eine ARM-Vorlage verwenden. Melden Sie sich für die Beispiele, in denen PowerShell verwendet wird, zunächst interaktiv mithilfe des Cmdlets [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) bei Azure an, und befolgen Sie die Anweisungen.

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

Initialisieren Sie dann einen Satz von Variablen, die in den Beispielen verwendet werden. Überarbeiten Sie die unten angegebenen Werte, und führen Sie dann aus.

```powershell
$subscriptionID = "subscriptionID"
$resourceGroup = "resourceGroupName"
$automationAccount = "automationAccountName"
$userAssignedOne = "userAssignedIdentityOne"
$userAssignedTwo = "userAssignedIdentityTwo"
```

### <a name="add-using-the-azure-portal"></a>Hinzufügen über das Azure-Portal

Führen Sie die folgenden Schritte aus:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Navigieren Sie im Azure-Portal zu Ihrem Automation-Konto.

1. Wählen Sie unter  **Kontoeinstellungen** die Option  **Identität** aus.

1. Wählen Sie die Registerkarte **Benutzerseitig zugewiesen** und dann **Hinzufügen** aus.

1. Wählen Sie Ihre vorhandene benutzerseitig zugewiesene verwaltete Identität und dann **Hinzufügen** aus. Daraufhin werden Sie zur Registerkarte **Benutzerseitig zugewiesen** zurückgeleitet.

   :::image type="content" source="media/add-user-assigned-identity/user-assigned-managed-identity.png" alt-text="Ausgabe des Portals.":::

### <a name="add-using-powershell"></a>Hinzufügen unter Verwendung von PowerShell

Verwenden Sie das PowerShell-Cmdlet [Set-AzAutomationAccount](/powershell/module/az.automation/set-azautomationaccount), um die benutzerseitig zugewiesenen verwalteten Identitäten hinzuzufügen. Sie müssen zunächst überlegen, ob eine systemseitig zugewiesene verwaltete Identität vorhanden ist. Im folgenden Beispiel werden einem vorhandenen Automation-Konto zwei bestehende benutzerseitig zugewiesene verwaltete Identitäten hinzugefügt, und, falls vorhanden, wird eine systemseitig zugewiesene verwaltete Identität deaktiviert.

```powershell
$output = Set-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount `
    -AssignUserIdentity "/subscriptions/$subscriptionID/resourcegroups/$resourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$userAssignedOne", `
        "/subscriptions/$subscriptionID/resourcegroups/$resourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$userAssignedTwo"

$output
```

Um eine vorhandene systemseitig zugewiesene verwaltete Identität beizubehalten, verwenden Sie Folgendes:

```powershell
$output = Set-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount `
    -AssignUserIdentity "/subscriptions/$subscriptionID/resourcegroups/$resourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$userAssignedOne", `
        "/subscriptions/$subscriptionID/resourcegroups/$resourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$userAssignedTwo" `
    -AssignSystemIdentity

$output
```

Die Ausgabe sollte in etwa wie folgt aussehen:

:::image type="content" source="media/add-user-assigned-identity/set-azautomationaccount-output.png" alt-text="Ausgabe des Befehls „Set-AzAutomationAccount“.":::

Führen Sie für eine zusätzliche Ausgabe `$output.identity | ConvertTo-Json` aus.

### <a name="add-using-a-rest-api"></a>Hinzufügen mithilfe einer REST-API

Syntax und Beispielschritte sind unten angegeben.

#### <a name="syntax"></a>Syntax

Die folgende Beispieltextsyntax aktiviert eine systemseitig zugewiesene verwaltete Identität, sofern sie noch nicht aktiviert ist, und weist dem vorhandenen Automation-Konto zwei bestehende benutzerseitig zugewiesene verwaltete Identitäten zu.

PATCH

```json
{
  "identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.ManagedIdentity/userAssignedIdentities/firstIdentity": {},
      "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.ManagedIdentity/userAssignedIdentities/secondIdentity": {}
    }
  }
}
```

Die Syntax der API sieht folgendermaßen aus:

```http
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview 
```

#### <a name="example"></a>Beispiel

Führen Sie die folgenden Schritte aus:

1. Überarbeiten Sie die Syntax des obigen Textkörpers in einer Datei namens `body_ua.json`. Speichern Sie die Datei auf Ihrem lokalen Computer oder in einem Azure-Speicherkonto.

1. Überarbeiten Sie den Variablenwert unten, und führen Sie dann aus.

    ```powershell
    $file = "path\body_ua.json"
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
    $response = Invoke-RestMethod -Uri $URI -Method PATCH -Headers $authHeader -Body $body
    
    # Review output
    $response.identity | ConvertTo-Json
    ```

    Die Ausgabe sollte in etwa wie folgt aussehen:

    ```json
    {
    "type": "SystemAssigned, UserAssigned",
    "principalId": "00000000-0000-0000-0000-000000000000",
    "tenantId": "00000000-0000-0000-0000-000000000000",
    "userAssignedIdentities":  {
        "/subscriptions/ContosoID/resourcegroups/ContosoLab/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ContosoUAMI1":  {
                "PrincipalId":  "00000000-0000-0000-0000-000000000000",
                "ClientId":  "00000000-0000-0000-0000-000000000000"
                    },
        "/subscriptions/ContosoID/resourcegroups/ContosoLab/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ContosoUAMI2":  {
                "PrincipalId":  "00000000-0000-0000-0000-000000000000",
                "ClientId":  "00000000-0000-0000-0000-000000000000"
                    }
        }
    }
    ```

### <a name="add-using-an-arm-template"></a>Hinzufügen mithilfe einer ARM-Vorlage

Syntax und Beispielschritte sind unten angegeben.

#### <a name="template-syntax"></a>Vorlagensyntax

Die folgende Beispielvorlagensyntax aktiviert eine systemseitig zugewiesene verwaltete Identität, sofern sie noch nicht aktiviert ist, und weist dem vorhandenen Automation-Konto zwei bestehende benutzerseitig zugewiesene verwaltete Identitäten zu.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "automationAccountName": {
     "defaultValue": "YourAutomationAccount",
      "type": "String",
      "metadata": {
        "description": "Automation account name"
      }
    },
    "userAssignedOne": {
     "defaultValue": "userAssignedOne",
      "type": "String",
      "metadata": {
        "description": "User-assigned managed identity"
      }
      },
    "userAssignedTwo": {
     "defaultValue": "userAssignedTwo",
      "type": "String",
      "metadata": {
        "description": "User-assigned managed identity"
      }
      }
   },
  "resources": [
    {
      "type": "Microsoft.Automation/automationAccounts",
      "apiVersion": "2020-01-13-preview",
      "name": "[parameters('automationAccountName')]",
      "location": "[resourceGroup().location]",
      "identity": {
        "type": "SystemAssigned, UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('userAssignedOne'))]": {},
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('userAssignedTwo'))]": {}
        }
      },
      "properties": {
        "sku": {
          "name": "Basic"
        },
        "encryption": {
          "keySource": "Microsoft.Automation",
          "identity": {}
        }
      }
    }
  ]
}
```

#### <a name="example"></a>Beispiel

Führen Sie die folgenden Schritte aus:

1. Kopieren Sie die Vorlage, und fügen Sie sie in eine Datei namens `template_ua.json` ein. Speichern Sie die Datei auf Ihrem lokalen Computer oder in einem Azure-Speicherkonto.

1. Überarbeiten Sie den Variablenwert unten, und führen Sie dann aus.

    ```powershell
    $templateFile = "path\template_ua.json"
    ```

1. Verwenden Sie das PowerShell-Cmdlet [New-AzureRmResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment), um die Vorlage bereitzustellen.

    ```powershell
    New-AzResourceGroupDeployment `
        -Name "UserAssignedDeployment" `
        -ResourceGroupName $resourceGroup `
        -TemplateFile $templateFile `
        -automationAccountName $automationAccount `
        -userAssignedOne $userAssignedOne `
        -userAssignedTwo $userAssignedTwo
    ```

   Der Befehl erzeugt keine Ausgabe. Sie können jedoch den folgenden Code verwenden, um Folgendes zu überprüfen:

    ```powershell
    (Get-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount).Identity | ConvertTo-Json
    ```

    Die Ausgabe sieht in etwa wie die obige Ausgabe für das REST-API-Beispiel aus.

## <a name="give-identity-access-to-azure-resources-by-obtaining-a-token"></a>Gewähren des Identitätszugriffs auf Azure-Ressourcen durch Abrufen eines Tokens

Ein Automation-Konto kann mithilfe seiner benutzerseitig zugewiesenen verwalteten Identität Token für den Zugriff auf andere durch Azure AD geschützte Ressourcen wie z. B. Azure Key Vault abrufen. Diese Token repräsentieren keinen bestimmten Benutzer der Anwendung. Stattdessen stellen sie die Anwendung dar, die auf die Ressource zugreift. In diesem Fall stellt das Token beispielsweise ein Automation-Konto dar.

Bevor Sie die benutzerseitig zugewiesene verwaltete Identität für die Authentifizierung verwenden können, richten Sie den Zugriff dieser Identität auf die Azure-Ressource ein, in der Sie die Identität verwenden möchten. Für diese Aufgabe muss der Identität in der Azure-Zielressource die entsprechende Rolle zugewiesen werden.

Befolgen Sie das Prinzip der geringsten Berechtigung und weisen Sie sorgfältig nur die Berechtigungen zu, die für die Ausführung Ihres Runbooks erforderlich sind. Beispiel: Wenn das Automatisierungskonto nur zum Starten oder Stoppen einer Azure-VM erforderlich ist, dann müssen die dem ausführenden Konto oder der verwalteten Identität zugewiesenen Berechtigungen nur zum Starten oder Beenden der VM dienen. Weisen Sie ebenso Lesezugriffsberechtigungen zu, wenn ein Runbook aus Blobspeicher liest.

In diesem Beispiel wird Azure PowerShell verwendet, um zu zeigen, wie die Rolle Mitwirkender im Abonnement der Azure-Zielressource zugewiesen wird. Die Rolle „Mitwirkender“ wird als Beispiel verwendet und kan in Ihrem Fall erforderlich sein oder auch nicht. Alternativ können Sie der Azure-Zielressource die Rolle auch im [Azure-Portal](../role-based-access-control/role-assignments-portal.md) zuweisen.

```powershell
New-AzRoleAssignment `
    -ObjectId <automation-Identity-object-id> `
    -Scope "/subscriptions/<subscription-id>" `
    -RoleDefinitionName "Contributor"
```

## <a name="authenticate-access-with-user-assigned-managed-identity"></a>Authentifizieren des Zugriffs mit der benutzerseitig zugewiesenen verwalteten Identität

Nachdem Sie die benutzerseitig zugewiesene verwaltete Identität für Ihr Automation-Konto aktiviert und einer Identität Zugriff auf die Zielressource gegeben haben, können Sie diese Identität in Runbooks für Ressourcen angeben, die die verwaltete Identität unterstützen. Verwenden Sie für die Identitätsunterstützung das Az-Cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount).

```powershell
# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process

# Connect to Azure with user-assigned managed identity
$AzureContext = (Connect-AzAccount -Identity -AccountId <user-assigned-identity-ClientId>).context

# set and store context
$AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext
```

## <a name="generate-an-access-token-without-using-azure-cmdlets"></a>Generieren eines Zugriffstokens ohne Verwendung von Azure-Cmdlets

Stellen Sie für HTTP-Endpunkte Folgendes sicher.
- Der Metadatenheader muss vorhanden sein und auf „true“ festgelegt werden.
- Eine Ressource muss zusammen mit der Anforderung als Abfrageparameter für eine GET-Anforderung und als Formulardaten für eine POST-Anforderung übergeben werden.
- Der Inhaltstyp für die Post-Anforderung muss `application/x-www-form-urlencoded` sein.

### <a name="get-access-token-for-user-assigned-managed-identity-using-http-get"></a>Abrufen des Zugriffstokens für die benutzerseitig zugewiesene verwaltete Identität mithilfe von HTTP Get  

```powershell
$resource= "?resource=https://management.azure.com/"
$client_id="&client_id=<ClientId of USI>"
$url = $env:IDENTITY_ENDPOINT + $resource + $client_id 
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"  
$Headers.Add("Metadata", "True")
$accessToken = Invoke-RestMethod -Uri $url -Method 'GET' -Headers $Headers
Write-Output $accessToken.access_token 
```

### <a name="get-access-token-for-user-assigned-managed-identity-using-http-post"></a>Abrufen des Zugriffstokens für die benutzerseitig zugewiesene verwaltete Identität mithilfe von HTTP Post

```powershell
$url = $env:IDENTITY_ENDPOINT
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Metadata", "True")
$body = @{'resource'='https://management.azure.com/' 
'client_id'='<ClientId of USI>'}
$accessToken = Invoke-RestMethod $url -Method 'POST' -Headers $headers -ContentType 'application/x-www-form-urlencoded' -Body $body
Write-Output $accessToken.access_token 
```

### <a name="using-user-assigned-managed-identity-in-azure-powershell"></a>Verwenden der benutzerseitig zugewiesenen verwalteten Identität in Azure PowerShell

```powershell
Write-Output "Connecting to azure via  Connect-AzAccount -Identity -AccountId <ClientId of USI>"  
Connect-AzAccount -Identity -AccountId <ClientId of USI> 
Write-Output "Successfully connected with Automation account's Managed Identity"  
Write-Output "Trying to fetch value from key vault using User Assigned Managed identity. Make sure you have given correct access to Managed Identity"  
$secret = Get-AzKeyVaultSecret -VaultName '<KVname>' -Name '<KeyName>'  
$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue)  
try {  
  $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr)  
    Write-Output $secretValueText  
} finally {  
    [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr)  
} 
```

### <a name="using-user-assigned-managed-identity-in-python-runbook"></a>Verwenden der benutzerseitig zugewiesenen verwalteten Identität in einem Python-Runbook

```python
#!/usr/bin/env python3  
import os  
import requests   

resource = "?resource=https://management.azure.com/" 
client_id = "&client_id=<ClientId of USI>" 
endPoint = os.getenv('IDENTITY_ENDPOINT')+ resource +client_id 
payload={}  
headers = {  
  'Metadata': 'True'  
}  
response = requests.request("GET", endPoint, headers=headers, data=payload)  
print(response.text) 
```

## <a name="next-steps"></a>Nächste Schritte

- Wenn Ihre Runbooks nicht erfolgreich abgeschlossen werden, lesen Sie [Behandlung von Problemen mit der verwalteten Azure Automation-Identität](troubleshoot/managed-identity.md).

- Wenn Sie eine verwaltete Identität deaktivieren müssen, finden Sie weitere Informationen unter [Deaktivieren der verwalteten Identität Ihres Azure Automation-Kontos](disable-managed-identity-for-automation.md).

- Eine Übersicht über die Azure Automation-Kontosicherheit finden Sie unter [Übersicht über die Automation-Kontoauthentifizierung.](automation-security-overview.md)