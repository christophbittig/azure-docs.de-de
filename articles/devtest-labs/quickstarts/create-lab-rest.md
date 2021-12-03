---
title: 'Schnellstart: Erstellen eines Labs mit der REST-API'
description: In dieser Schnellstartanleitung erstellen Sie mithilfe einer Azure-REST-API ein Lab in Azure DevTest Labs.
ms.topic: quickstart
ms.date: 10/27/2021
ms.openlocfilehash: 83a1509f36f53d51f63f7dbc39ea2d1f6794dc54
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479785"
---
# <a name="quickstart-create-a-lab-in-azure-devtest-labs-using-azure-rest-api"></a>Schnellstart: Erstellen eines Labs in Azure DevTest Labs mithilfe einer Azure-REST-API

Erste Schritte mit Azure DevTest Labs mithilfe der Azure-REST-API Azure DevTest Labs umfasst eine Gruppe von Ressourcen, etwa virtuelle Azure-Computer (VMs) und Netzwerke. Mit dieser Infrastruktur können Sie diese Ressourcen besser verwalten, indem Sie Grenzwerte und Kontingente angeben.  Mit der Azure-REST-API können Sie Vorgänge für Dienste verwalten, die auf der Azure-Plattform gehostet werden.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Das PowerShell-[Az-Modul](/powershell/azure/new-azureps-module-az) ist installiert. Stellen Sie sicher, dass Sie über die aktuelle Version verfügen. Führen Sie gegebenenfalls `Update-Module -Name Az` aus.

## <a name="prepare-request-body"></a>Vorbereiten des Anforderungstexts

Bereiten Sie den [Anforderungstext](/rest/api/dtl/labs/create-or-update.md#request-body) vor, der vom REST-Aufruf genutzt werden soll.

Kopieren Sie die folgende JSON-Syntax, und fügen Sie sie in eine Datei namens `body.json` ein. Speichern Sie die Datei auf Ihrem lokalen Computer oder in einem Azure-Speicherkonto.

```json
{
  "properties": {
    "labStorageType": "Standard"
  },
  "location": "westus2",
  "tags": {
    "Env": "alpha"
  }
}
```

## <a name="sign-in-to-your-azure-subscription"></a>Melden Sie sich bei Ihrem Azure-Abonnement an.

1. Geben Sie einen geeigneten Wert für die Variablen an, und führen Sie dann das Skript aus.

    ```powershell
    $subscription = "subscriptionID"
    $resourceGroup = "resourceGroupName"
    $labName = "labName"
    $file = "path\body.json"
    ```

1. Melden Sie sich auf Ihrer Arbeitsstation mit dem PowerShell-Cmdlet [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) bei Ihrem Azure-Abonnement an, und befolgen Sie die Anweisungen auf dem Bildschirm.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    
    # If you have multiple subscriptions, set the one to use
    # Set-AzContext -SubscriptionId $subscription
    ```

## <a name="build-request-body-for-submission"></a>Buildanforderungstext für die Übermittlung

Die Syntax für die PUT-Anforderung lautet wie folgt:<br>
 `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{name}?api-version=2018-09-15`.

 Führen Sie die folgenden PowerShell-Skripts aus, um den Anforderungswert an einen Parameter zu übergeben. Der Inhalt des Anforderungstexts wird ebenfalls an einen Parameter übergeben.

```powershell
# build URI
$URI = "https://management.azure.com/subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.DevTestLab/labs/$labName`?api-version=2018-09-15"

# build body
$body = Get-Content $file
```

## <a name="obtain-an-authentication-token"></a>Abrufen eines Authentifizierungstokens

Führen Sie die folgenden Befehle aus, um ein Authentifizierungstoken abzurufen:

```powershell
$azContext = Get-AzContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}
```

## <a name="invoke-the-rest-api"></a>Aufrufen der REST-API

Verwenden Sie die folgenden Befehle, um die REST-API aufzurufen und die Antwort zu überprüfen:

```powershell
# Invoke the REST API
$response = Invoke-RestMethod -Uri $URI -Method PUT -Headers $authHeader -Body $body

# Review output
$response | ConvertTo-Json
```

Die Antwort sollte etwa wie folgt aussehen:

```output
{
    "properties":  {
                       "labStorageType":  "Standard",
                       "mandatoryArtifactsResourceIdsLinux":  [

                                                              ],
                       "mandatoryArtifactsResourceIdsWindows":  [

                                                                ],
                       "createdDate":  "2021-10-27T20:22:49.7495913+00:00",
                       "premiumDataDisks":  "Disabled",
                       "environmentPermission":  "Reader",
                       "announcement":  {
                                            "title":  "",
                                            "markdown":  "",
                                            "enabled":  "Disabled",
                                            "expired":  false
                                        },
                       "support":  {
                                       "enabled":  "Disabled",
                                       "markdown":  ""
                                   },
                       "provisioningState":  "Creating",
                       "uniqueIdentifier":  "uniqueID"
                   },
    "id":  "/subscriptions/ContosoID/resourcegroups/groupcontoso/providers/microsoft.devtestlab/labs/myotherlab",

    "name":  "myOtherLab",
    "type":  "Microsoft.DevTestLab/labs",
    "location":  "westus2",
    "tags":  {
                 "Env":  "alpha"
             }
}
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie dieses Lab nicht weiterverwenden möchten, sollten Sie es wie folgt löschen:

1. Geben Sie einen geeigneten Wert für die Variablen an, und führen Sie dann das Skript aus.

    ```powershell
    $subscription = "subscriptionID"
    $resourceGroup = "resourceGroupName"
    $labName = "labName"
    ```

1. Führen Sie das folgende Skript aus, um das benannte Lab aus Azure DevTest Labs zu entfernen:

    ```powershell
    # build URI
    $URI = "https://management.azure.com/subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.DevTestLab/labs/$labName`?api-version=2018-09-15"
    
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
    Invoke-RestMethod -Uri $URI -Method DELETE -Headers $authHeader
    ```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie mithilfe der Azure-REST-API ein Lab erstellt. Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie auf das Lab zugreifen:

> [!div class="nextstepaction"]
> [Tutorial: Zugreifen auf das Lab](../tutorial-use-custom-lab.md)
