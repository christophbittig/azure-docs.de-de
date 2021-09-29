---
title: Erstellen und Bereitstellen von Vorlagenspezifikationen
description: Erfahren Sie, wie Sie eine Vorlagenspezifikation aus einer ARM-Vorlage erstellen. Stellen Sie dann die Vorlagenspezifikation für eine Ressourcengruppe in Ihrem Abonnement bereit.
author: tfitzmac
ms.date: 05/04/2021
ms.topic: quickstart
ms.author: tomfitz
ms.openlocfilehash: f44c485d0a7eb7f546c1b227ca121d906225b43e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631208"
---
# <a name="quickstart-create-and-deploy-template-spec"></a>Schnellstart: Erstellen und Bereitstellen von Vorlagenspezifikationen

Dieser Schnellstart zeigt Ihnen, wie Sie eine Azure Resource Manager-Vorlage (ARM-Vorlage) in eine [Vorlagenspezifikation](template-specs.md) packen. Anschließend stellen Sie diese Vorlagenspezifikation bereit. Ihre Vorlagenspezifikation enthält eine ARM-Vorlage, mit der ein Speicherkonto bereitgestellt wird.

## <a name="prerequisites"></a>Voraussetzungen

Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> Um Vorlagenspezifikationen mit Azure PowerShell zu verwenden, müssen Sie [mindestens Version 5.0.0](/powershell/azure/install-az-ps) installieren. Zur Verwendung mit der Azure CLI verwenden Sie [mindestens Version 2.14.2](/cli/azure/install-azure-cli).

## <a name="create-template"></a>Erstellen der Vorlage

Sie erstellen eine Vorlagenspezifikation aus einer lokalen Vorlage. Kopieren Sie die folgende Vorlage, und speichern Sie sie lokal in einer Datei namens **azuredeploy.json**. In dieser Schnellstartanleitung wird davon ausgegangen, dass die Speicherung im Pfad **c:\Templates\azuredeploy.json** erfolgt ist, aber Sie können einen beliebigen Pfad verwenden.

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.storage/storage-account-create/azuredeploy.json":::

## <a name="create-template-spec"></a>Erstellen von Vorlagenspezifikationen

Die Vorlagenspezifikation ist ein Ressourcentyp namens `Microsoft.Resources/templateSpecs`. Verwenden Sie zum Erstellen einer Vorlagenspezifikation PowerShell, die Azure CLI, das Portal oder eine ARM-Vorlage.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Erstellen Sie eine neue Ressourcengruppe, die die Vorlagenspezifikation enthält.

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location westus2
    ```

1. Erstellen Sie die Vorlagenspezifikation in dieser Ressourcengruppe. Weisen Sie der neuen Vorlagenspezifikation den Namen **storageSpec** zu.

    ```azurepowershell
    New-AzTemplateSpec `
      -Name storageSpec `
      -Version "1.0" `
      -ResourceGroupName templateSpecRG `
      -Location westus2 `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/azure-cli)

1. Erstellen Sie eine neue Ressourcengruppe, die die Vorlagenspezifikation enthält.

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location westus2
    ```

1. Erstellen Sie die Vorlagenspezifikation in dieser Ressourcengruppe. Weisen Sie der neuen Vorlagenspezifikation den Namen **storageSpec** zu.

    ```azurecli
    az ts create \
      --name storageSpec \
      --version "1.0" \
      --resource-group templateSpecRG \
      --location "westus2" \
      --template-file "c:\Templates\azuredeploy.json"
    ```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach **Vorlagenspezifikationen**. Wählen Sie **Vorlagenspezifikationen** den verfügbaren Optionen aus.

    :::image type="content" source="./media/quickstart-create-template-specs/search-template-spec.png" alt-text="Suchen nach Vorlagenspezifikationen":::

1. Wählen Sie **Vorlage importieren** aus.

    :::image type="content" source="./media/quickstart-create-template-specs/import-template.png" alt-text="Vorlage importieren":::

1. Wählen Sie das Ordnersymbol aus.

    :::image type="content" source="./media/quickstart-create-template-specs/open-folder.png" alt-text="Ordner öffnen":::

1. Navigieren Sie zur lokalen Vorlage, die Sie gespeichert haben, und wählen Sie diese aus. Klicken Sie auf **Öffnen**.
1. Wählen Sie **Importieren** aus.

    :::image type="content" source="./media/quickstart-create-template-specs/select-import.png" alt-text="Importoption auswählen":::

1. Geben Sie außerdem die folgenden Werte an:

    - **Name**: Geben Sie einen Namen für die Vorlagenspezifikation ein.  Beispiel: **storageSpec**
    - **Abonnement**: Wählen Sie ein Azure-Abonnement für die Erstellung der Vorlagenspezifikation aus.
    - **Ressourcengruppe**: Wählen Sie **Neu erstellen** aus, und geben Sie einen neuen Ressourcengruppennamen ein.  Beispiel: **templateSpecRG**
    - **Standort**: Wählen Sie einen Standort für die Ressourcengruppe aus. Beispiel: **USA, Westen 2**
    - **Version**: Geben Sie eine Version für die Vorlagenspezifikation ein. Verwenden Sie **1.0**.

1. Klicken Sie auf **Überprüfen + erstellen**.
1. Klicken Sie auf **Erstellen**.

# <a name="arm-template"></a>[ARM-Vorlage](#tab/azure-resource-manager)

> [!NOTE]
> Anstatt eine ARM-Vorlage zu verwenden, wird empfohlen, dass Sie PowerShell oder die CLI verwenden, um Ihre Vorlagenspezifikation zu erstellen. Diese Tools konvertieren verknüpfte Vorlagen automatisch in Artefakte, die mit ihrer Hauptvorlage verbunden sind. Wenn Sie eine ARM-Vorlage zum Erstellen der Vorlagenspezifikation verwenden, müssen Sie diese verknüpften Vorlagen manuell als Artefakte hinzufügen. Dies kann kompliziert sein.

1. Wenn Sie eine ARM-Vorlage zum Erstellen der Vorlagenspezifikation verwenden, wird die Vorlage in die Ressourcendefinition eingebettet. An Ihrer lokalen Vorlage müssen einige Änderungen vorgenommen werden. Kopieren Sie die folgende Vorlage, und speichern Sie sie lokal als **azuredeploy.json**.

    > [!NOTE]
    > In der eingebetteten Vorlage müssen alle [Vorlagenausdrücke](template-expressions.md) eine zweite linke eckige Klammer als Escapezeichen verwenden. Verwenden Sie `"[[` anstelle von `"[`. JSON-Arrays verwenden weiterhin eine einzelne linke eckige Klammer.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/templateSpecs",
          "apiVersion": "2021-05-01",
          "name": "storageSpec",
          "location": "westus2",
          "properties": {
            "displayName": "Storage template spec"
          },
          "tags": {},
          "resources": [
            {
              "type": "versions",
              "apiVersion": "2021-05-01",
              "name": "1.0",
              "location": "westus2",
              "dependsOn": [ "storageSpec" ],
              "properties": {
                "mainTemplate": {
                  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                  "contentVersion": "1.0.0.0",
                  "parameters": {
                    "storageAccountType": {
                      "type": "string",
                      "defaultValue": "Standard_LRS",
                      "allowedValues": [
                        "Standard_LRS",
                        "Standard_GRS",
                        "Standard_ZRS",
                        "Premium_LRS"
                      ],
                      "metadata": {
                        "description": "Storage Account type"
                      }
                    },
                    "location": {
                      "type": "string",
                      "defaultValue": "[[resourceGroup().location]",
                      "metadata": {
                        "description": "Location for all resources."
                      }
                    }
                  },
                  "variables": {
                    "storageAccountName": "[[concat('store', uniquestring(resourceGroup().id))]"
                  },
                  "resources": [
                    {
                      "type": "Microsoft.Storage/storageAccounts",
                      "apiVersion": "2021-04-01",
                      "name": "[[variables('storageAccountName')]",
                      "location": "[[parameters('location')]",
                      "sku": {
                        "name": "[[parameters('storageAccountType')]"
                      },
                      "kind": "StorageV2",
                      "properties": {}
                    }
                  ],
                  "outputs": {
                    "storageAccountName": {
                      "type": "string",
                      "value": "[[variables('storageAccountName')]"
                    }
                  }
                }
              },
              "tags": {}
            }
          ]
        }
      ],
      "outputs": {}
    }
    ```

1. Verwenden Sie die Azure CLI oder PowerShell, um eine neue Ressourcengruppe zu erstellen.

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location westus2
    ```

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location westus2
    ```

1. Stellen Sie Ihre Vorlage mit der Azure CLI oder mit PowerShell bereit.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName templateSpecRG `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

    ```azurecli
    az deployment group create \
      --name templateSpecRG \
      --template-file "c:\Templates\azuredeploy.json"
    ```

---

## <a name="deploy-template-spec"></a>Bereitstellen der Vorlagenspezifikationen

Verwenden Sie zum Bereitstellen einer Vorlagenspezifikation dieselben Bereitstellungsbefehle wie zum Bereitstellen einer Vorlage. Übergeben Sie die Ressourcen-ID der bereitzustellenden Vorlagenspezifikation.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Erstellen Sie eine Ressourcengruppe, die das neue Speicherkonto enthält.

    ```azurepowershell
    New-AzResourceGroup `
      -Name storageRG `
      -Location westus2
    ```

1. Rufen Sie die Ressourcen-ID der Vorlagenspezifikation ab.

    ```azurepowershell
    $id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name storageSpec -Version "1.0").Versions.Id
    ```

1. Stellen Sie die Vorlagenspezifikation bereit.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -TemplateSpecId $id `
      -ResourceGroupName storageRG
    ```

1. Sie geben die Parameter genau wie bei einer ARM-Vorlage an. Stellen Sie die Vorlagenspezifikation mit einem Parameter für den Speicherkontotyp erneut bereit.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -TemplateSpecId $id `
      -ResourceGroupName storageRG `
      -storageAccountType Standard_GRS
    ```

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/azure-cli)

1. Erstellen Sie eine Ressourcengruppe, die das neue Speicherkonto enthält.

    ```azurecli
    az group create \
      --name storageRG \
      --location westus2
    ```

1. Rufen Sie die Ressourcen-ID der Vorlagenspezifikation ab.

    ```azurecli
    id=$(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")
    ```

    > [!NOTE]
    > Es gibt ein bekanntes Problem beim Abrufen der Vorlagenspezifikations-ID und deren Zuweisung zu einer Variablen in Windows PowerShell.

1. Stellen Sie die Vorlagenspezifikation bereit.

    ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id
    ```

1. Sie geben die Parameter genau wie bei einer ARM-Vorlage an. Stellen Sie die Vorlagenspezifikation mit einem Parameter für den Speicherkontotyp erneut bereit.

    ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id \
      --parameters storageAccountType='Standard_GRS'
    ```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Wählen Sie die von Ihnen erstellte Vorlagenspezifikation.

    :::image type="content" source="./media/quickstart-create-template-specs/select-template-spec.png" alt-text="Vorlagenspezifikation auswählen":::

1. Klicken Sie auf **Bereitstellen**.

    :::image type="content" source="./media/quickstart-create-template-specs/deploy-template-spec.png" alt-text="Vorlagenspezifikationen bereitstellen":::

1. Geben Sie außerdem die folgenden Werte an:

    - **Abonnement**: Wählen Sie ein Azure-Abonnement für die Ressourcenerstellung aus.
    - **Ressourcengruppe**: Wählen Sie **Neu erstellen** aus, und geben Sie **storageRG** ein.
    - **Speicherkontotyp**: Wählen Sie **Standard_GRS** aus.

1. Klicken Sie auf **Überprüfen + erstellen**.
1. Klicken Sie auf **Erstellen**.

# <a name="arm-template"></a>[ARM-Vorlage](#tab/azure-resource-manager)

1. Kopieren Sie die folgende Vorlage, und speichern Sie sie lokal in einer Datei namens **storage.json**.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/deployments",
          "apiVersion": "2021-04-01",
          "name": "demo",
          "properties": {
            "templateLink": {
              "id": "[resourceId('templateSpecRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0')]"
            },
            "parameters": {
            },
            "mode": "Incremental"
          }
        }
      ],
      "outputs": {}
    }
    ```

1. Verwenden Sie die Azure CLI oder PowerShell, um eine neue Ressourcengruppe für das Speicherkonto zu erstellen.

    ```azurepowershell
    New-AzResourceGroup `
      -Name storageRG `
      -Location westus2
    ```

    ```azurecli
    az group create \
      --name storageRG \
      --location westus2
    ```

1. Stellen Sie Ihre Vorlage mit der Azure CLI oder mit PowerShell bereit.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName storageRG `
      -TemplateFile "c:\Templates\storage.json"
    ```

    ```azurecli
    az deployment group create \
      --name storageRG \
      --template-file "c:\Templates\storage.json"
    ```

---

## <a name="grant-access"></a>Gewähren von Zugriff

Wenn Sie anderen Benutzern in Ihrer Organisation die Bereitstellung Ihrer Vorlagenspezifikation gestatten möchten, müssen Sie ihnen Lesezugriff gewähren. Sie können die Rolle „Leser“ einer Azure AD-Gruppe für die Ressourcengruppe zuweisen, die freizugebende Vorlagenspezifikationen enthält. Weitere Informationen finden Sie im [Tutorial: Gewähren des Zugriffs auf Azure-Ressourcen für eine Gruppe mithilfe von Azure PowerShell](../../role-based-access-control/tutorial-role-assignments-group-powershell.md).

## <a name="update-template"></a>Aktualisieren der Vorlage

Angenommen, Sie haben eine Änderung identifiziert, die Sie an der Vorlage in Ihrer Vorlagenspezifikation vornehmen möchten. Die folgende Vorlage ähnelt Ihrer früheren Vorlage, mit der Ausnahme, dass sie ein Präfix für den Speicherkontonamen hinzufügt. Kopieren Sie die folgende Vorlage, und aktualisieren Sie die Datei „azuredeploy.json“.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/templatespecs/azuredeploy2.json":::

## <a name="update-template-spec-version"></a>Aktualisieren der Vorlagenspezifikationsversion

Anstatt eine neue Vorlagenspezifikation für die überarbeitete Vorlage zu erstellen, fügen Sie der vorhandenen Vorlagenspezifikation eine neue Version mit dem Namen `2.0` hinzu. Benutzer können eine beliebige bereitzustellende Version auswählen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Erstellen Sie eine neue Version der Vorlagenspezifikation.

   ```powershell
   New-AzTemplateSpec `
     -Name storageSpec `
     -Version "2.0" `
     -ResourceGroupName templateSpecRG `
     -Location westus2 `
     -TemplateFile "c:\Templates\azuredeploy.json"
   ```

1. Um die neue Version bereitzustellen, müssen Sie die Ressourcen-ID für die `2.0`-Version abrufen.

   ```powershell
   $id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name storageSpec -Version "2.0").Versions.Id
   ```

1. Stellen Sie diese Version bereit. Geben Sie ein Präfix für den Namen des Speicherkontos an.

   ```powershell
   New-AzResourceGroupDeployment `
     -TemplateSpecId $id `
     -ResourceGroupName storageRG `
     -namePrefix "demoaccount"
   ```

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/azure-cli)

1. Erstellen Sie eine neue Version der Vorlagenspezifikation.

   ```azurecli
   az ts create \
     --name storageSpec \
     --version "2.0" \
     --resource-group templateSpecRG \
     --location "westus2" \
     --template-file "c:\Templates\azuredeploy.json"
   ```

1. Um die neue Version bereitzustellen, müssen Sie die Ressourcen-ID für die `2.0`-Version abrufen.

   ```azurecli
   id=$(az ts show --name storageSpec --resource-group templateSpecRG --version "2.0" --query "id")
   ```

1. Stellen Sie diese Version bereit. Geben Sie ein Präfix für den Namen des Speicherkontos an.

   ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id \
      --parameters namePrefix='demoaccount'
    ```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Wählen Sie in der Vorlagenspezifikation **Neue Version erstellen** aus.

   :::image type="content" source="./media/quickstart-create-template-specs/select-versions.png" alt-text="Neue Version erstellen":::

1. Nennen Sie die neue Version `2.0`, und fügen Sie optional Notizen hinzu. Wählen Sie **Vorlage bearbeiten** aus.

   :::image type="content" source="./media/quickstart-create-template-specs/add-version-name.png" alt-text="Neue Version benennen":::

1. Ersetzen Sie den Inhalt der Vorlage durch die aktualisierte Vorlage. Klicken Sie auf **Überprüfen und speichern**.
1. Klicken Sie auf **Save changes** (Änderungen speichern).

1. Wählen Sie **Versionen** aus, um die neue Version bereitzustellen.

   :::image type="content" source="./media/quickstart-create-template-specs/see-versions.png" alt-text="Versionen auflisten":::

1. Wählen Sie für die Version, die Sie bereitstellen möchten, die drei Punkte und dann **Bereitstellen** aus.

   :::image type="content" source="./media/quickstart-create-template-specs/deploy-version.png" alt-text="Bereitzustellende Version auswählen":::

1. Füllen Sie die Felder wie bei der Bereitstellung der früheren Version aus.
1. Klicken Sie auf **Überprüfen + erstellen**.
1. Klicken Sie auf **Erstellen**.

# <a name="arm-template"></a>[ARM-Vorlage](#tab/azure-resource-manager)

1. Auch hier müssen Sie einige Änderungen an der lokalen Vorlage vornehmen, damit Sie mit Vorlagenspezifikationen funktioniert. Kopieren Sie die folgende Vorlage, und speichern Sie sie lokal als azuredeploy.json.

   ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/templateSpecs",
          "apiVersion": "2021-05-01",
          "name": "storageSpec",
          "location": "westus2",
          "properties": {
            "displayName": "Storage template spec"
          },
          "tags": {},
          "resources": [
            {
              "type": "versions",
              "apiVersion": "2021-05-01",
              "name": "2.0",
              "location": "westus2",
              "dependsOn": [ "storageSpec" ],
              "properties": {
                "mainTemplate": {
                  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                  "contentVersion": "1.0.0.0",
                  "parameters": {
                    "storageAccountType": {
                      "type": "string",
                      "defaultValue": "Standard_LRS",
                      "allowedValues": [
                        "Standard_LRS",
                        "Standard_GRS",
                        "Standard_ZRS",
                        "Premium_LRS"
                      ],
                      "metadata": {
                        "description": "Storage Account type"
                      }
                    },
                    "location": {
                      "type": "string",
                      "defaultValue": "[[resourceGroup().location]",
                      "metadata": {
                        "description": "Location for all resources."
                      }
                    },
                    "namePrefix": {
                      "type": "string",
                      "maxLength": 11,
                      "defaultValue": "store",
                      "metadata": {
                        "description": "Prefix for storage account name"
                      }
                    }
                  },
                  "variables": {
                    "storageAccountName": "[[concat(parameters('namePrefix'), uniquestring(resourceGroup().id))]"
                  },
                  "resources": [
                    {
                      "type": "Microsoft.Storage/storageAccounts",
                      "apiVersion": "2021-04-01",
                      "name": "[[variables('storageAccountName')]",
                      "location": "[[parameters('location')]",
                      "sku": {
                        "name": "[[parameters('storageAccountType')]"
                      },
                      "kind": "StorageV2",
                      "properties": {}
                    }
                  ],
                  "outputs": {
                    "storageAccountName": {
                      "type": "string",
                      "value": "[[variables('storageAccountName')]"
                    }
                  }
                }
              },
              "tags": {}
            }
          ]
        }
      ],
      "outputs": {}
    }
    ```

1. Um die neue Version Ihrer Vorlagenspezifikation hinzuzufügen, stellen Sie Ihre Vorlage mit der Azure CLI oder mit PowerShell bereit.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName templateSpecRG `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

    ```azurecli
    az deployment group create \
      --name templateSpecRG \
      --template-file "c:\Templates\azuredeploy.json"
    ```

1. Kopieren Sie die folgende Vorlage, und speichern Sie sie lokal in einer Datei namens **storage.json**.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/deployments",
          "apiVersion": "2021-04-01",
          "name": "demo",
          "properties": {
            "templateLink": {
              "id": "[resourceId('templateSpecRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '2.0')]"
            },
            "parameters": {
            },
            "mode": "Incremental"
          }
        }
      ],
      "outputs": {}
    }
    ```

1. Stellen Sie Ihre Vorlage mit der Azure CLI oder mit PowerShell bereit.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName storageRG `
      -TemplateFile "c:\Templates\storage.json"
    ```

    ```azurecli
    az deployment group create \
      --name storageRG \
      --template-file "c:\Templates\storage.json"
    ```

---

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie zum Bereinigen der in dieser Schnellstartanleitung bereitgestellten Ressource beide Ressourcengruppen, die Sie erstellt haben.

1. Wählen Sie im Azure-Portal im linken Menü die Ressourcengruppe aus.

1. Geben Sie den Ressourcengruppennamen (templateSpecRG und storageRG) in das Feld „Nach Name filtern“ ein.

1. Klicken Sie auf den Namen der Ressourcengruppe.

1. Wählen Sie im Menü ganz oben den Befehl zum Löschen der Ressourcengruppe aus.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Erstellen einer Vorlagenspezifikation, die verknüpfte Vorlagen umfasst, finden Sie unter [Erstellen einer Vorlagenspezifikation einer verknüpften Vorlage](template-specs-create-linked.md).
