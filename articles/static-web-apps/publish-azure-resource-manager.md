---
title: 'Tutorial: Veröffentlichen von Azure Static Web Apps mithilfe einer ARM-Vorlage'
description: Erstellen und Bereitstellen einer ARM-Vorlage für Static Web Apps
services: static-web-apps
author: petender
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 07/13/2021
ms.author: petender
ms.openlocfilehash: 06f415492157a52b3a258fa2191e1a88e211bccd
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2021
ms.locfileid: "122608837"
---
# <a name="tutorial-publish-azure-static-web-apps-using-an-arm-template"></a>Tutorial: Veröffentlichen von Azure Static Web Apps mithilfe einer ARM-Vorlage

Dieser Artikel beschreibt die Bereitstellung von [Azure Static Web Apps](./overview.md) mithilfe einer [Azure Resource Manager-Vorlage](../azure-resource-manager/templates/overview.md) (ARM-Vorlage).

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> - Erstellen einer ARM-Vorlage für Azure Static Web Apps
> - Bereitstellen der ARM-Vorlage zum Erstellen einer Azure Static Web App-Instanz

## <a name="prerequisites"></a>Voraussetzungen

- **Aktives Azure-Konto:** Sollten Sie über kein Konto verfügen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/).
- **GitHub-Konto:** Sollten Sie über kein Konto verfügen, können Sie ein [kostenloses GitHub-Konto erstellen](https://github.com).
- **Editor für ARM-Vorlagen:** Zum Überprüfen und Bearbeiten von Vorlagen ist ein JSON-Editor erforderlich. Visual Studio Code mit der [Erweiterung „Azure Resource Manager Tools“](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) eignet sich gut zum Bearbeiten von ARM-Vorlagen. Anweisungen zum Installieren und Konfigurieren von Visual Studio Code finden Sie unter [Schnellstart: Erstellen von ARM-Vorlagen mit Visual Studio Code](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md).

- **Azure CLI oder Azure PowerShell:** Zum Bereitstellen von ARM-Vorlagen ist ein Befehlszeilentool erforderlich. Die Installationsanweisungen finden Sie unter:
  - [Installieren der Azure CLI unter Windows](/cli/azure/install-azure-cli-windows)
  - [Installieren der Azure CLI unter Linux](/cli/azure/install-azure-cli-linux)
  - [Installieren der Azure CLI unter macOS](/cli/azure/install-azure-cli-macos)
  - [Installieren von Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="create-a-github-personal-access-token"></a>Erstellen eines persönlichen GitHub-Zugriffstokens

Einer der Parameter in der ARM-Vorlage ist `repositoryToken`. Dadurch kann der ARM-Bereitstellungsprozess mit dem GitHub-Repository interagieren, das den Quellcode der statischen Website enthält. 

1. Wählen Sie in Ihrem GitHub-Kontoprofil (in der oberen rechten Ecke) die Option **Settings** (Einstellungen) aus.

1. Wählen Sie **Developer Settings** (Entwicklereinstellungen) aus.

1. Wählen Sie **Personal Access Tokens** (Persönliche Zugriffstoken) aus.

1. Wählen Sie **Generate New Token** (Neues Token generieren) aus.

1. Geben Sie im Feld _Note_ (Hinweis) einen Namen für dieses Token an, z. B. *myfirstswadeployment*.

1. Geben Sie die folgenden *Bereiche* an: **repo, workflow, write:packages**.

1. Wählen Sie **Generate token** (Token generieren) aus.

1. Kopieren Sie den Tokenwert, und fügen Sie ihn zur späteren Verwendung in einen Text-Editor ein.

> [!IMPORTANT]
> Stellen Sie sicher, dass Sie dieses Token kopieren und an einem sicheren Ort speichern. Speichern Sie dieses Token in [Azure Key Vault](../azure-resource-manager/templates/template-tutorial-use-key-vault.md), und greifen Sie in Ihrer ARM-Vorlage darauf zu.

## <a name="create-a-github-repo"></a>Erstellen eines GitHub-Repositorys

In diesem Artikel wird ein GitHub-Vorlagenrepository verwendet, um Ihnen den Einstieg zu erleichtern. Die Vorlage enthält eine Starter-App, die für die Bereitstellung mit Azure Static Web Apps verwendet wird.

1. Navigieren Sie zum folgenden Speicherort, um ein neues Repository zu erstellen:
    1. [https://github.com/staticwebdev/vanilla-basic/generate](https://github.com/login?return_to=/staticwebdev/vanilla-basic/generate)

1. Nennen Sie Ihr Repository **myfirstswadeployment**.

    > [!NOTE]
    > Für Azure Static Web Apps wird mindestens eine HTML-Datei benötigt, um eine Web-App zu erstellen. Das in diesem Schritt erstellte Repository enthält nur eine Datei vom Typ _index.html_.

1. Wählen Sie **Create repository from template** (Repository aus Vorlage erstellen) aus.

    :::image type="content" source="./media/getting-started/create-template.png" alt-text="Create repository from template (Repository aus Vorlage erstellen)":::

## <a name="create-the-arm-template"></a>Erstellen der ARM-Vorlage

Wenn die Voraussetzungen erfüllt sind, besteht der nächste Schritt darin, die ARM-Bereitstellungsvorlagendatei zu definieren.

1. Erstellen Sie einen neuen Ordner für die ARM-Vorlagen.

1. Erstellen Sie eine neue Datei, und geben Sie ihr den Namen **azuredeploy.json**.

1. Fügen Sie den folgenden ARM-Vorlagenausschnitt in _azuredeploy.json_ ein:

    ```json
        {
            "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "name": {
                    "type": "string"
                },
                "location": {
                    "type": "string"
                },
                "sku": {
                    "type": "string"
                },
                "skucode": {
                    "type": "string"
                },
                "repositoryUrl": {
                    "type": "string"
                },
                "branch": {
                    "type": "string"
                },
                "repositoryToken": {
                    "type": "securestring"
                },
                "appLocation": {
                    "type": "string"
                },
                "apiLocation": {
                    "type": "string"
                },
                "appArtifactLocation": {
                    "type": "string"
                },
                "resourceTags": {
                    "type": "object"
                },
                "appSettings": {
                    "type": "object"
                }
            },
            "resources": [
                {
                    "apiVersion": "2021-01-15",
                    "name": "[parameters('name')]",
                    "type": "Microsoft.Web/staticSites",
                    "location": "[parameters('location')]",
                    "tags": "[parameters('resourceTags')]",
                    "properties": {
                        "repositoryUrl": "[parameters('repositoryUrl')]",
                        "branch": "[parameters('branch')]",
                        "repositoryToken": "[parameters('repositoryToken')]",
                        "buildProperties": {
                            "appLocation": "[parameters('appLocation')]",
                            "apiLocation": "[parameters('apiLocation')]",
                            "appArtifactLocation": "[parameters('appArtifactLocation')]"
                        }
                    },
                    "sku": {
                        "Tier": "[parameters('sku')]",
                        "Name": "[parameters('skuCode')]"
                    },
                    "resources":[
                        {
                            "apiVersion": "2021-01-15",
                            "name": "appsettings",
                            "type": "config",
                            "location": "[parameters('location')]",
                            "properties": "[parameters('appSettings')]",
                            "dependsOn": [
                                "[resourceId('Microsoft.Web/staticSites', parameters('name'))]"
                            ]
                        }
                    ]
                }
            ]
        }

    ```

1. Erstellen Sie eine neue Datei, und geben Sie ihr den Namen **azuredeploy.parameters.json**.

1. Fügen Sie den folgenden ARM-Vorlagenausschnitt in _azuredeploy.parameters.json_ ein:

    ```json
        {
            "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "name": {
                    "value": "myfirstswadeployment"
                },
                "location": { 
                    "value": "Central US"
                },   
                "sku": {
                    "value": "Free"
                },
                "skucode": {
                    "value": "Free"
                },
                "repositoryUrl": {
                    "value": "https://github.com/<YOUR-GITHUB-USER-NAME>/<YOUR-GITHUB-REPOSITORY-NAME>"
                },
                "branch": {
                    "value": "main"
                },
                "repositoryToken": {
                    "value": "<YOUR-GITHUB-PAT>" 
                },
                "appLocation": {
                    "value": "/"
                },
                "apiLocation": {
                    "value": ""
                },
                "appArtifactLocation": {
                    "value": "src"
                },
                "resourceTags": {
                    "value": {
                        "Environment": "Development",
                        "Project": "Testing SWA with ARM",
                        "ApplicationName": "myfirstswadeployment"
                    }
                },
                "appSettings": {
                    "value": {
                        "MY_APP_SETTING1": "value 1",
                        "MY_APP_SETTING2": "value 2"
                    }
                }
            }
        }
    ```

1. Aktualisieren Sie die folgenden Parameter:

    | Parameter | Erwarteter Wert |
    | --- | --- |
    | `repositoryUrl` | Geben Sie die URL zu Ihrem Static Web Apps GitHub-Repository an. |
    | `repositoryToken` | Geben Sie das persönliche GitHub-Zugriffstoken an. |

1. Speichern Sie die Updates, bevor Sie die Bereitstellung im nächsten Schritt ausführen.

## <a name="running-the-deployment"></a>Ausführen der Bereitstellung

Zum Bereitstellen der Vorlage benötigen Sie entweder die Azure CLI oder Azure PowerShell.

### <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich entweder bei der Azure CLI oder bei Azure PowerShell an, um eine Vorlage bereitzustellen.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az login
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

---

Wenn Sie über mehrere Azure-Abonnements verfügen, wählen Sie das Abonnement aus, das Sie verwenden möchten. Ersetzen Sie `<SUBSCRIPTION-ID-OR-SUBSCRIPTION-NAME>` durch Ihre Abonnementinformationen:

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az account set --subscription <SUBSCRIPTION-ID-OR-SUBSCRIPTION-NAME>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzContext <SUBSCRIPTION-ID-OR-SUBSCRIPTION-NAME>
```

---

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Beim Bereitstellen einer Vorlage geben Sie eine Ressourcengruppe an, die die bereitgestellten Ressourcen enthält. Erstellen Sie die Ressourcengruppe vor dem Ausführen des Bereitstellungsbefehls entweder mit der Azure CLI oder Azure PowerShell.

> [!NOTE]
> Die CLI-Beispiele in diesem Artikel sind für die Bash-Shell geschrieben.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
resourceGroupName="myfirstswadeployRG"

az group create \
  --name $resourceGroupName \
  --location "Central US"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$resourceGroupName = "myfirstswadeployRG"

New-AzResourceGroup `
  -Name $resourceGroupName `
  -Location "Central US"
```

---

## <a name="deploy-template"></a>Bereitstellen der Vorlage

Verwenden Sie eine der folgenden Bereitstellungsoptionen, um die Vorlage bereitzustellen:

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli

az deployment group create \
  --name DeployLocalTemplate \
  --resource-group $resourceGroupName \
  --template-file <PATH-TO-AZUREDEPLOY.JSON> \
  --parameters <PATH-TO-AZUREDEPLOY.PARAMETERS.JSON> \
  --verbose
```

Weitere Informationen zum Bereitstellen von Vorlagen mithilfe der Azure CLI finden Sie unter [Bereitstellen von Ressourcen mit ARM-Vorlagen und der Azure CLI](../azure-resource-manager/templates/deploy-cli.md).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$templateparameterFile = Read-Host -Prompt "Enter the template parameter file path and file name"

New-AzResourceGroupDeployment `
  -Name DeployLocalTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile $templateFile `
  -TemplateParameterFile $templateparameterfile `
  -verbose
```

Weitere Informationen zum Bereitstellen von Vorlagen mithilfe von Azure PowerShell finden Sie unter [Bereitstellen von Ressourcen mit ARM-Vorlagen und Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md).

---

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe, um die bereitgestellten Ressourcen zu bereinigen.

1. Wählen Sie im Azure-Portal im linken Menü die Option **Ressourcengruppe** aus.
2. Geben Sie den Namen der Ressourcengruppe in das Feld **Nach Name filtern** ein.
3. Klicken Sie auf den Namen der Ressourcengruppe.
4. Wählen Sie **Ressourcengruppe löschen** aus dem Menü ganz oben aus.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Konfigurieren Ihrer statischen Web-App](./configuration.md)