---
title: Verwenden einer vom System zugewiesenen verwalteten Identität für ein Azure Automation Konto
description: In diesem Artikel wird beschrieben, wie Sie eine verwaltete Identität für Azure Automation-Konten einrichten.
services: automation
ms.subservice: process-automation
ms.date: 10/26/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 685126603c302a02d56aff51873cd34340947494
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131470754"
---
# <a name="using-a-system-assigned-managed-identity-for-an-azure-automation-account"></a>Verwenden einer vom System zugewiesenen verwalteten Identität für ein Azure Automation Konto

In diesem Artikel erfahren Sie, wie eine systemseitig zugewiesene verwaltete Identität für ein Azure Automation-Konto erstellt und für den Zugriff auf andere Ressourcen verwendet wird. Weitere Informationen zur Funktionsweise verwalteter Identitäten mit Azure Automation finden Sie unter [Verwaltete Identitäten](automation-security-overview.md#managed-identities).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure Automation-Konto. Eine Anleitung hierzu finden Sie unter [Erstellen eines Azure Automation-Kontos](./quickstarts/create-account-portal.md).

- Die neueste Version der Az PowerShell-Module „Az.Accounts“, „Az.Resources“, „Az.Automation“ und „Az.KeyVault“.

- Eine Azure-Ressource, auf die Sie über Ihr Automation-Runbook zugreifen möchten. Für diese Ressource muss eine Rolle für die verwaltete Identität definiert sein, mit der das Automation-Runbook den Zugriff auf die Ressource authentifizieren kann. Zum Hinzufügen von Rollen müssen Sie Besitzer der Ressource im entsprechenden Azure AD sein.

- Wenn Sie Hybridaufträge mit einer verwalteten Identität ausführen möchten, aktualisieren Sie die Hybrid Runbook Worker auf die neueste Version. Mindestens erforderliche Version:

  - Windows Hybrid Runbook Worker: Version 7.3.1125.0
  - Linux Hybrid Runbook Worker: Version 1.7.4.0

## <a name="enable-a-system-assigned-managed-identity-for-an-azure-automation-account"></a>Aktivieren einer systemseitig zugewiesenen verwalteten Identität für ein Azure Automation-Konto

Nach der Aktivierung werden der systemseitig zugewiesenen verwalteten Identität die folgenden Eigenschaften zugewiesen.

|Eigenschaft (JSON) | Wert | BESCHREIBUNG|
|----------|-----------|------------|
| principalid | \<principal-ID\> | Die GUID (Globally Unique Identifier) des Dienstprinzipalobjekts für die systemseitig zugewiesene verwaltete Identität, die das Automation-Konto im Azure AD-Mandanten angibt. Diese GUID wird manchmal als „Objekt-ID“ oder „objectID“ angezeigt. |
| tenantid | \<Azure-AD-tenant-ID\> | Die GUID (Globally Unique Identifier), die den Azure AD-Mandanten angibt, in dem das Automation-Konto nun Mitglied ist. Unter dem Azure AD-Mandanten hat der Dienstprinzipal den gleichen Namen wie das Automation-Konto. |

Sie können eine systemseitig zugewiesene verwaltete Identität für ein Azure Automation-Konto aktivieren, indem Sie das Azure-Portal, PowerShell, die Azure-REST-API oder eine ARM-Vorlage verwenden. Melden Sie sich für die Beispiele, in denen PowerShell verwendet wird, zunächst interaktiv mithilfe des Cmdlets [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) bei Azure an, und befolgen Sie die Anweisungen.

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
```

> [!IMPORTANT]
> Die neue Identität der Automation-Kontoebene überschreibt alle vorherigen vom System zugewiesenen Identitäten auf Ebene der virtuellen Computer, die unter [Verwendung der Runbook-Authentifizierung mit verwalteten Identitäten](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities) beschrieben sind. Wenn Sie Hybridaufträge auf virtuellen Azure-Computern ausführen, die die vom System zugewiesene Identität eines virtuellen Computers für den Zugriff auf Runbookressourcen verwenden, wird die Identität des Automation-Kontos für die Hybridaufträge verwendet. Dies bedeutet, dass ihre vorhandene Auftragsausführung beeinträchtigt werden kann, wenn Sie das CmK-Feature (Customer Managed Keys) Ihres Automation-Kontos verwendet haben.<br/><br/>Wenn Sie die verwaltete Identität des virtuellen Computers weiterhin verwenden möchten, sollten Sie die Identität auf Automation-Kontoebene nicht aktivieren. Wenn Sie dies bereits aktiviert haben, können Sie die systemseitig zugewiesene verwaltete Identität des Automation-Kontos deaktivieren. Weitere Informationen finden Sie unter [verwaltete Identität ihres Azure Automation-Kontos deaktivieren](./disable-managed-identity-for-automation.md).

### <a name="enable-using-the-azure-portal"></a>Aktivieren mithilfe des Azure-Portals

Führen Sie die folgenden Schritte aus:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Navigieren Sie im Azure-Portal zu Ihrem Automation-Konto.

1. Wählen Sie unter  **Kontoeinstellungen** die Option  **Identität** aus.

1. Legen Sie die Option **System zugewiesen** auf **Ein** fest und klicken Sie auf **Speichern**. Wenn Sie aufgefordert werden, die Auswahl zu bestätigen, klicken Sie auf **Ja**.

   :::image type="content" source="media/managed-identity/managed-identity-on.png" alt-text="Aktivieren der systemseitig zugewiesenen Identität im Azure-Portal.":::

   In Ihrem Automation-Konto kann nun die systemseitig zugewiesene Identität verwendet werden, die bei Azure Active Directory (Azure ID) registriert ist und durch eine Objekt-ID angegeben wird.

   :::image type="content" source="media/managed-identity/managed-identity-object-id.png" alt-text="Objekt-ID der verwalteten Identität.":::

### <a name="enable-using-powershell"></a>Aktivieren mithilfe von PowerShell

Verwenden Sie das PowerShell-Cmdlet [Set-AzAutomationAccount](/powershell/module/az.automation/set-azautomationaccount), um die systemseitig zugewiesene verwaltete Identität zu aktivieren.

```powershell
$output = Set-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount `
    -AssignSystemIdentity

$output
```

Die Ausgabe sollte in etwa wie folgt aussehen:

:::image type="content" source="media/enable-managed-identity-for-automation/set-azautomationaccount-output.png" alt-text="Ausgabe des Befehls „Set-AzAutomationAccount“.":::

Ändern Sie für eine zusätzliche Ausgabe das Beispiel so, dass Folgendes angegeben wird: `$output.identity | ConvertTo-Json`.

### <a name="enable-using-a-rest-api"></a>Aktivieren mithilfe einer REST-API

Syntax und Beispielschritte sind unten angegeben.

#### <a name="syntax"></a>Syntax

Der folgende Syntaxtextkörper ermöglicht eine systemseitig zugewiesene verwaltete Identität für ein vorhandenes Automation-Konto mithilfe der HTTP-Methode **PATCH**. Mit dieser Syntax werden jedoch alle vorhandenen benutzerseitig zugewiesenen verwalteten Identitäten entfernt, die dem Automation-Konto zugeordnet sind.

```json
{ 
 "identity": { 
   "type": "SystemAssigned" 
  } 
}
```

Wenn mehrere benutzerseitig zugewiesene Identitäten definiert sind, müssen Sie, um diese zu behalten und nur die systemseitig zugewiesene Identität zu entfernen, jede benutzerseitig zugewiesene Identität mithilfe einer durch Trennzeichen getrennten Liste angeben. Im folgenden Beispiel wird die HTTP-Methode **PATCH** verwendet.

```json
{ 
  "identity" : {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/cmkID": {},
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/cmkID2": {}
    }
  }
}

```

Die Syntax der API sieht folgendermaßen aus:

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

#### <a name="example"></a>Beispiel

Führen Sie die folgenden Schritte aus:

1. Kopieren Sie den Syntaxtextkörper, und fügen Sie ihn in eine Datei namens `body_sa.json` ein. Speichern Sie die Datei auf Ihrem lokalen Computer oder in einem Azure-Speicherkonto.

1. Aktualisieren Sie den variablen Wert unten, und führen Sie dann aus.

    ```powershell
    $file = "path\body_sa.json"
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
        "PrincipalId":  "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
        "TenantId":  "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
        "Type":  0,
        "UserAssignedIdentities":  null
    }
    ```

### <a name="enable-using-an-arm-template"></a>Aktivieren mithilfe einer ARM-Vorlage

Syntax und Beispielschritte sind unten angegeben.

#### <a name="template-syntax"></a>Vorlagensyntax

Die folgende Beispielvorlagensyntax aktiviert eine systemseitig zugewiesene verwaltete Identität für das vorhandene Automation-Konto. Mit dieser Syntax werden jedoch alle vorhandenen benutzerseitig zugewiesenen verwalteten Identitäten entfernt, die dem Automation-Konto zugeordnet sind.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Automation/automationAccounts",
      "apiVersion": "2020-01-13-preview",
      "name": "yourAutomationAccount",
      "location": "[resourceGroup().location]",
      "identity": {
        "type": "SystemAssigned"
        },
      "properties": {
        "sku": {
          "name": "Basic"
        }
      }
    }
  ]
}
```

#### <a name="example"></a>Beispiel

Führen Sie die folgenden Schritte aus:

1. Überarbeiten Sie die Syntax der obigen Vorlage so, dass Ihr Automation-Konto verwendet wird, und speichern Sie sie in einer Datei namens `template_sa.json`.

1. Aktualisieren Sie den variablen Wert unten, und führen Sie dann aus.

    ```powershell
    $templateFile = "path\template_sa.json"
    ```

1. Verwenden Sie das PowerShell-Cmdlet [New-AzureRmResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment), um die Vorlage bereitzustellen.

    ```powershell
    New-AzResourceGroupDeployment `
        -Name "SystemAssignedDeployment" `
        -ResourceGroupName $resourceGroup `
        -TemplateFile $templateFile
    ```

   Der Befehl erzeugt keine Ausgabe. Sie können jedoch den folgenden Code verwenden, um Folgendes zu überprüfen:

    ```powershell
    (Get-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount).Identity | ConvertTo-Json
    ```

   Die Ausgabe sieht in etwa wie die obige Ausgabe für das REST-API-Beispiel aus.

## <a name="give-access-to-azure-resources-by-obtaining-a-token"></a>Gewähren des Zugriffs auf Azure-Ressourcen durch Abrufen eines Tokens

Ein Automation-Konto kann mithilfe seiner systemseitig zugewiesenen verwalteten Identität Token für den Zugriff auf andere durch Azure AD geschützte Ressourcen wie z. B. Azure Key Vault abrufen. Diese Token repräsentieren keinen bestimmten Benutzer der Anwendung. Stattdessen stellen sie die Anwendung dar, die auf die Ressource zugreift. In diesem Fall stellt das Token beispielsweise ein Automation-Konto dar.

Bevor Sie die systemseitig verwaltete Identität für die Authentifizierung verwenden können, richten Sie den Zugriff dieser Identität auf die Azure-Ressource ein, in der Sie die Identität verwenden möchten. Für diese Aufgabe muss der Identität in der Azure-Zielressource die entsprechende Rolle zugewiesen werden.

Befolgen Sie das Prinzip der geringsten Berechtigung und weisen Sie sorgfältig nur die Berechtigungen zu, die für die Ausführung Ihres Runbooks erforderlich sind. Beispiel: Wenn das Automatisierungskonto nur zum Starten oder Stoppen einer Azure-VM erforderlich ist, dann müssen die dem Konto "Ausführen als" oder der verwalteten Identität zugewiesenen Berechtigungen nur zum Starten oder Stoppen der VM dienen. Weisen Sie ebenso Leseberechtigungen zu, wenn ein Runbook aus dem Blobspeicher liest.

In diesem Beispiel wird Azure PowerShell verwendet, um zu zeigen, wie die Rolle Mitwirkender im Abonnement der Azure-Zielressource zugewiesen wird. Die Rolle Mitwirkender wird als Beispiel verwendet und ist in Ihrem Fall möglicherweise erforderlich.

```powershell
New-AzRoleAssignment `
    -ObjectId <automation-Identity-object-id> `
    -Scope "/subscriptions/<subscription-id>" `
    -RoleDefinitionName "Contributor"
```

## <a name="authenticate-access-with-system-assigned-managed-identity"></a>Authentifizieren des Zugriffs mit der systemseitig zugewiesenen verwalteten Identität

Nachdem Sie die verwaltete Identität für Ihr Automation-Konto aktiviert und einer Identität Zugriff auf die Zielressource gegeben haben, können Sie diese Identität in Runbooks für Ressourcen angeben, die die verwaltete Identität unterstützen. Verwenden Sie für die Identitätsunterstützung das Az cmdlet `Connect-AzAccount` cmdlet. Weitere Informationen finden Sie in der PowerShell-Referenz unter [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount).

```powershell
# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process

# Connect to Azure with system-assigned managed identity
$AzureContext = (Connect-AzAccount -Identity).context

# set and store context
$AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext
```

> [!NOTE]
> Wenn Ihre Organisation weiterhin die veralteten AzureRM-Cmdlets verwendet, können Sie `Connect-AzureRMAccount -Identity` verwenden.

## <a name="generate-an-access-token-without-using-azure-cmdlets"></a>Generieren eines Zugriffstokens ohne Verwendung von Azure-Cmdlets

Stellen Sie für HTTP-Endpunkte Folgendes sicher.

- Der Metadatenheader muss vorhanden sein und auf „true“ festgelegt werden.
- Eine Ressource muss zusammen mit der Anforderung als Abfrageparameter für eine GET-Anforderung und als Formulardaten für eine POST-Anforderung übergeben werden.
- Der X-IDENTITY-HEADER sollte auf den Wert der Umgebungsvariablen festgelegt werden, IDENTITY_HEADER für Hybrid Runbook Workers.
- Der Inhaltstyp für die Post-Anforderung muss „application/x-www-form-urlencoded“ sein.

### <a name="get-access-token-for-system-assigned-managed-identity-using-http-get"></a>Abrufen des Zugriffstokens für die systemseitig zugewiesene verwaltete Identität mithilfe von HTTP Get

```powershell
$resource= "?resource=https://management.azure.com/" 
$url = $env:IDENTITY_ENDPOINT + $resource 
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$Headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$Headers.Add("Metadata", "True") 
$accessToken = Invoke-RestMethod -Uri $url -Method 'GET' -Headers $Headers
Write-Output $accessToken.access_token
```

### <a name="get-access-token-for-system-assigned-identity-using-http-post"></a>Abrufen des Zugriffstokens für die systemseitig zugewiesene verwaltete Identität mithilfe von HTTP Post

```powershell
$url = $env:IDENTITY_ENDPOINT  
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$headers.Add("Metadata", "True") 
$body = @{resource='https://management.azure.com/' } 
$accessToken = Invoke-RestMethod $url -Method 'POST' -Headers $headers -ContentType 'application/x-www-form-urlencoded' -Body $body 
Write-Output $accessToken.access_token
```

### <a name="using-system-assigned-managed-identity-in-azure-powershell"></a>Verwenden der systemseitig zugewiesenen verwalteten Identität in Azure PowerShell

Weitere Informationen finden Sie unter [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret).

```powershell
Write-Output "Connecting to azure via  Connect-AzAccount -Identity" 
Connect-AzAccount -Identity 
Write-Output "Successfully connected with Automation account's Managed Identity" 
Write-Output "Trying to fetch value from key vault using MI. Make sure you have given correct access to Managed Identity" 
$secret = Get-AzKeyVaultSecret -VaultName '<KVname>' -Name '<KeyName>' 

$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue) 
try { 
  $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr) 
    Write-Output $secretValueText 
} finally { 
    [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr) 
}
```

### <a name="using-system-assigned-managed-identity-in-python-runbook"></a>Verwenden der systemseitig zugewiesenen verwalteten Identität in einem Python-Runbook

```python
#!/usr/bin/env python3 
import os 
import requests  
# printing environment variables 
endPoint = os.getenv('IDENTITY_ENDPOINT')+"?resource=https://management.azure.com/" 
identityHeader = os.getenv('IDENTITY_HEADER') 
payload={} 
headers = { 
  'X-IDENTITY-HEADER': identityHeader,
  'Metadata': 'True' 
} 
response = requests.request("GET", endPoint, headers=headers, data=payload) 
print(response.text) 
```

### <a name="using-system-assigned-managed-identity-to-access-sql-database"></a>Verwenden der systemseitig zugewiesenen verwalteten Identität für den Zugriff auf SQL-Datenbank

Ausführliche Informationen zum Bereitstellen des Zugriffs auf eine Azure SQL-Datenbank finden Sie unter [Bereitstellen eines Azure AD-Administrators (SQL-Datenbank)](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database).

```powershell
$queryParameter = "?resource=https://database.windows.net/" 
$url = $env:IDENTITY_ENDPOINT + $queryParameter
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$Headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$Headers.Add("Metadata", "True") 
$content =[System.Text.Encoding]::Default.GetString((Invoke-WebRequest -UseBasicParsing -Uri $url -Method 'GET' -Headers $Headers).RawContentStream.ToArray()) | ConvertFrom-Json 
$Token = $content.access_token 
echo "The managed identities for Azure resources access token is $Token" 
$SQLServerName = "<ServerName>"    # Azure SQL logical server name  
$DatabaseName = "<DBname>"     # Azure SQL database name 
Write-Host "Create SQL connection string" 
$conn = New-Object System.Data.SqlClient.SQLConnection  
$conn.ConnectionString = "Data Source=$SQLServerName.database.windows.net;Initial Catalog=$DatabaseName;Connect Timeout=30" 
$conn.AccessToken = $Token 
Write-host "Connect to database and execute SQL script" 
$conn.Open()  
$ddlstmt = "CREATE TABLE Person( PersonId INT IDENTITY PRIMARY KEY, FirstName NVARCHAR(128) NOT NULL)" 
Write-host " " 
Write-host "SQL DDL command" 
$ddlstmt 
$command = New-Object -TypeName System.Data.SqlClient.SqlCommand($ddlstmt, $conn) 
Write-host "results" 
$command.ExecuteNonQuery() 
$conn.Close()
```

## <a name="migrate-from-existing-run-as-accounts-to-managed-identity"></a>Migrieren von vorhandenen ausführenden Konten zu einer verwalteten Identität

Azure Automation bietet eine Authentifizierung für die Verwaltung von Azure Resource Manager-Ressourcen oder von Ressourcen, die im klassischen Bereitstellungsmodell mit einem ausführendem Konto bereitgestellt wurden. Führen Sie die folgenden Schritte aus, um von einem ausführenden Konto zu einer verwalteten Identität für Ihre Runbook-Authentifizierung zu wechseln.

1. Aktivieren Sie [eine vom System zugewiesene](enable-managed-identity-for-automation.md), vom Benutzer [zugewiesene](add-user-assigned-identity.md)oder beide Typen von verwalteten Identitäten.
1. Gewähren Sie der verwalteten Identität die gleichen Berechtigungen für die Azure-Ressourcen, die mit dem übereinstimmen, was dem ausführenden Konto zugewiesen wurde.
1. Aktualisieren Sie Ihre Runbooks für die Authentifizierung mithilfe der verwalteten Identität.
1. Ändern Sie Runbooks so, dass die verwaltete Identität verwendet wird. Verwenden Sie für die Identitätsunterstützung das Az cmdlet `Connect-AzAccount` cmdlet. Weitere Informationen finden Sie in der PowerShell-Referenz unter [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount).

   - Wenn Sie AzureRM-Module verwenden, aktualisieren Sie `AzureRM.Profile` auf die neueste Version, und ersetzen Sie sie mithilfe von  `Add-AzureRMAccount` Cmdlets durch `Connect-AzureRMAccount –Identity`.
   - Wenn Sie Az-Module verwenden, aktualisieren Sie auf die neueste Version, indem Sie die Schritte im Abschnitt [Azure PowerShell Module aktualisieren](automation-update-azure-modules.md#update-az-modules) ausführen.

## <a name="next-steps"></a>Nächste Schritte

- Wenn Ihre Runbooks nicht erfolgreich abgeschlossen werden, lesen Sie [Behandlung von Problemen mit der verwalteten Azure Automation-Identität](troubleshoot/managed-identity.md).

- Wenn Sie eine verwaltete Identität deaktivieren müssen, finden Sie weitere Informationen unter [Deaktivieren der verwalteten Identität Ihres Azure Automation-Kontos](disable-managed-identity-for-automation.md).

- Eine Übersicht über die Azure Automation-Kontosicherheit finden Sie unter [Übersicht über die Automation-Kontoauthentifizierung.](automation-security-overview.md)
