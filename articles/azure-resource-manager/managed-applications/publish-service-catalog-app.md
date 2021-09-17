---
title: Veröffentlichen einer verwalteten Dienstkatalog-App
description: Erfahren Sie, wie Sie eine verwaltete Azure-Anwendung erstellen, die für Mitglieder Ihrer Organisation vorgesehen ist.
author: tfitzmac
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli, devx-track-azurepowershell, subject-rbac-steps
ms.date: 08/16/2021
ms.author: tomfitz
ms.openlocfilehash: 7722017e2cbe9c0f195ce24ee5452674ad0fd344
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253746"
---
# <a name="quickstart-create-and-publish-a-managed-application-definition"></a>Schnellstart: Erstellen und Veröffentlichen einer Definition für die verwaltete Anwendung

Diese Schnellstartanleitung enthält eine Einführung in die Verwendung von [Azure Managed Applications](overview.md). Sie können verwaltete Azure-Anwendungen erstellen und veröffentlichen, die für Mitglieder Ihrer Organisation vorgesehen sind.

Um eine verwaltete Anwendung im Dienstkatalog zu veröffentlichen, gehen Sie folgendermaßen vor:

* Erstellen Sie eine Vorlage, die definiert, welche Ressourcen mit der verwalteten Anwendung bereitgestellt werden.
* Definieren Sie die Elemente der Benutzeroberfläche für das Portal, wenn Sie die verwaltete Anwendung bereitstellen.
* Erstellen Sie ein _ZIP_-Paket, das die erforderlichen Vorlagendateien enthält.
* Entscheiden Sie, welche Benutzer, Gruppen oder Anwendungen Zugriff auf die Ressourcengruppe im Benutzerabonnement benötigen.
* Erstellen Sie die verwaltete Anwendungsdefinition, die auf das _ZIP_-Paket verweist und Zugriff auf die Identität anfordert.

## <a name="create-the-arm-template"></a>Erstellen der ARM-Vorlage

Jede Definition einer verwalteten Anwendung enthält eine Datei namens _mainTemplate.json_. Darin definieren Sie die bereitzustellenden Azure-Ressourcen. Die Vorlage unterscheidet sich nicht von anderen regulären ARM-Vorlagen.

Erstellen Sie eine Datei mit dem Namen _mainTemplate.json_. Beim Namen wird die Groß- und Kleinschreibung berücksichtigt.

Fügen Sie Ihrer Datei den folgenden JSON-Code hinzu. Er definiert die Parameter für das Erstellen eines Speicherkontos und legt die Eigenschaften des Speicherkontos fest.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountNamePrefix": {
      "type": "string"
    },
    "storageAccountType": {
      "type": "string"
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "variables": {
    "storageAccountName": "[concat(parameters('storageAccountNamePrefix'), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageEndpoint": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
    }
  }
}
```

Speichern Sie die Datei _mainTemplate.json_.

## <a name="define-your-create-experience"></a>Definieren Ihrer Benutzeroberfläche zur Erstellung

Als Herausgeber definieren Sie die Portalbenutzeroberfläche zum Erstellen der verwalteten Anwendung. Die _createUiDefinition.json_-Datei generiert die Portalbenutzeroberfläche. Sie definieren mithilfe von [Steuerelementen](create-uidefinition-elements.md) (etwa Dropdownmenüs, Textfeldern und Kennwortfeldern), wie Benutzer Werte für die einzelnen Parameter eingeben.

Erstellen Sie eine Datei mit dem Namen _createUiDefinition.json_. (Bei diesem Namen muss die Groß-/Kleinschreibung beachtet werden.)

Fügen Sie der Datei den folgenden JSON-Startercode hinzu, und speichern Sie sie:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "storageConfig",
        "label": "Storage settings",
        "subLabel": {
          "preValidation": "Configure the infrastructure settings",
          "postValidation": "Done"
        },
        "bladeTitle": "Storage settings",
        "elements": [
          {
            "name": "storageAccounts",
            "type": "Microsoft.Storage.MultiStorageAccountCombo",
            "label": {
              "prefix": "Storage account name prefix",
              "type": "Storage account type"
            },
            "defaultValue": {
              "type": "Standard_LRS"
            },
            "constraints": {
              "allowedTypes": [
                "Premium_LRS",
                "Standard_LRS",
                "Standard_GRS"
              ]
            }
          }
        ]
      }
    ],
    "outputs": {
      "storageAccountNamePrefix": "[steps('storageConfig').storageAccounts.prefix]",
      "storageAccountType": "[steps('storageConfig').storageAccounts.type]",
      "location": "[location()]"
    }
  }
}
```

Weitere Informationen finden Sie unter [Erstellen einer Benutzeroberfläche im Azure-Portal für die verwaltete Anwendung](create-uidefinition-overview.md).

## <a name="package-the-files"></a>Erstellen eines Pakets aus den Dateien

Fügen Sie die beiden Dateien einer _ZIP_-Datei mit dem Namen _app.zip_ hinzu. Die zwei Dateien müssen sich auf der Stammebene der _ZIP_-Datei befinden. Wenn Sie sie in einem Ordner speichern, erhalten Sie beim Erstellen der Definition der verwalteten Anwendung eine Fehlermeldung mit dem Hinweis, dass die erforderlichen Dateien nicht vorhanden sind.

Laden Sie das Paket an einen zugänglichen Speicherort hoch, wo es verwendet werden kann. Sie müssen einen eindeutigen Namen für das Speicherkonto angeben.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name storageGroup -Location eastus

$storageAccount = New-AzStorageAccount `
  -ResourceGroupName storageGroup `
  -Name "mystorageaccount" `
  -Location eastus `
  -SkuName Standard_LRS `
  -Kind StorageV2

$ctx = $storageAccount.Context

New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob

Set-AzStorageBlobContent `
  -File "D:\myapplications\app.zip" `
  -Container appcontainer `
  -Blob "app.zip" `
  -Context $ctx
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
az group create --name storageGroup --location eastus

az storage account create \
    --name mystorageaccount \
    --resource-group storageGroup \
    --location eastus \
    --sku Standard_LRS \
    --kind StorageV2

az storage container create \
    --account-name mystorageaccount \
    --name appcontainer \
    --public-access blob

az storage blob upload \
    --account-name mystorageaccount \
    --container-name appcontainer \
    --name "app.zip" \
    --file "D:\myapplications\app.zip"

```

---

## <a name="create-the-managed-application-definition"></a>Erstellen der Definition für die verwaltete Anwendung

### <a name="create-an-azure-active-directory-user-group-or-application"></a>Erstellen einer Azure Active Directory-Benutzergruppe oder -Anwendung

Der zweite Schritt besteht darin, eine Benutzergruppe, einen Benutzer oder eine Anwendung für das Verwalten der Ressourcen für den Kunden zu erstellen. Diese Identität verfügt über Berechtigungen für die verwaltete Ressourcengruppe, die durch die zugewiesene Rolle festgelegt sind. Die Rolle kann eine beliebige integrierte Azure-Rolle wie „Besitzer“ oder „Mitwirkender“ sein. Weitere Informationen zum Erstellen einer neuen Active Directory-Benutzergruppe finden Sie unter [Erstellen einer Gruppe und Hinzufügen von Mitgliedern in Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Sie benötigen zum Verwalten der Ressourcen die Objekt-ID der Benutzergruppe.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$groupID=(Get-AzADGroup -DisplayName mygroup).Id
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
groupid=$(az ad group show --group mygroup --query objectId --output tsv)
```

---

### <a name="get-the-role-definition-id"></a>Abrufen der Rollendefinitions-ID

Als Nächstes benötigen Sie die Rollendefinitions-ID der integrierten Azure-Rolle, der Sie Zugriff auf den Benutzer, die Benutzergruppe oder Anwendung gewähren möchten. In der Regel verwenden Sie die Rolle „Besitzer, „Mitwirkender“ oder „Leser“. Der folgende Befehl zeigt, wie die Rollendefinitions-ID für die Rolle „Besitzer“ abgerufen wird:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$ownerID=(Get-AzRoleDefinition -Name Owner).Id
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
ownerid=$(az role definition list --name Owner --query [].name --output tsv)
```

---

### <a name="create-the-managed-application-definition"></a>Erstellen der Definition für die verwaltete Anwendung

Falls Sie noch nicht über eine Ressourcengruppe zum Speichern der Definition Ihrer verwalteten Anwendung verfügen, erstellen Sie jetzt eine:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name appDefinitionGroup -Location westcentralus
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
az group create --name appDefinitionGroup --location westcentralus
```

---

Erstellen Sie nun die Definitionsressource für die verwaltete Anwendung.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$blob = Get-AzStorageBlob -Container appcontainer -Blob app.zip -Context $ctx

New-AzManagedApplicationDefinition `
  -Name "ManagedStorage" `
  -Location "westcentralus" `
  -ResourceGroupName appDefinitionGroup `
  -LockLevel ReadOnly `
  -DisplayName "Managed Storage Account" `
  -Description "Managed Azure Storage Account" `
  -Authorization "${groupID}:$ownerID" `
  -PackageFileUri $blob.ICloudBlob.StorageUri.PrimaryUri.AbsoluteUri
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
blob=$(az storage blob url --account-name mystorageaccount --container-name appcontainer --name app.zip --output tsv)

az managedapp definition create \
  --name "ManagedStorage" \
  --location "westcentralus" \
  --resource-group appDefinitionGroup \
  --lock-level ReadOnly \
  --display-name "Managed Storage Account" \
  --description "Managed Azure Storage Account" \
  --authorizations "$groupid:$ownerid" \
  --package-file-uri "$blob"
```

---

Nachdem die Ausführung des Befehls abgeschlossen ist, verfügen Sie in Ihrer Ressourcengruppe über eine Definition für die verwaltete Anwendung.

Im vorigen Beispiel werden u.a. folgende Parameter verwendet:

* **Ressourcengruppe**: Der Name der Ressourcengruppe, in der die Definition für die verwaltete Anwendung erstellt wird.
* **Sperrebene**: Der Typ der Sperre, der auf die verwaltete Ressourcengruppe angewendet wird. Er verhindert, dass der Kunde unerwünschte Vorgänge für diese Ressourcengruppe durchführt. Derzeit ist „ReadOnly“ die einzige unterstützte Sperrebene. Wenn „ReadOnly“ festgelegt wird, sind die in der verwalteten Ressourcengruppe vorhandenen Ressourcen für den Kunden schreibgeschützt. Dies gilt nicht für Herausgeberidentitäten, denen Zugriff auf die verwaltete Ressourcengruppe gewährt wird.
* **authorizations**: Beschreibt die Prinzipal-ID und die Rollendefinitions-ID, die zum Erteilen von Berechtigungen für die verwaltete Ressourcengruppe verwendet werden. Diese ist im Format `<principalId>:<roleDefinitionId>` angegeben. Wenn mehr als ein Wert erforderlich ist, können Sie diese in der Form `<principalId1>:<roleDefinitionId1>,<principalId2>:<roleDefinitionId2>` angeben. Die Werte werden hierbei durch ein Komma voneinander getrennt.
* **Paketdatei-URI**: Der Speicherort eines _ZIP_-Pakets, das die erforderlichen Dateien enthält.

## <a name="bring-your-own-storage-for-the-managed-application-definition"></a>Bereitstellen eines eigenen Speichers (Bring Your Own Storage) für die Definition der verwalteten Anwendung

Sie können die Definition der verwalteten Anwendung in einem von Ihnen während der Erstellung angegebenen Speicherkonto speichern. Dadurch können sein Speicherort und der Zugriff gemäß Ihrer gesetzlichen Anforderungen vollständig von Ihnen verwaltet werden.

> [!NOTE]
> Die Bereitstellung eines eigenen Speichers wird nur bei Bereitstellungen der Definition der verwalteten Anwendung über ARM-Vorlagen oder REST-APIs unterstützt.

### <a name="select-your-storage-account"></a>Wählen Sie Ihr Speicherkonto aus.

Sie müssen [ein Speicherkonto erstellen](../../storage/common/storage-account-create.md), in dem die Definition der verwalteten Anwendung zur Verwendung mit dem Dienstkatalog gespeichert wird.

Kopieren Sie die Ressourcen-ID des Speicherkontos. Sie wird später bei der Bereitstellung der Definition verwendet.

### <a name="set-the-role-assignment-for-appliance-resource-provider-in-your-storage-account"></a>Festlegen der Rollenzuweisung für „Applianceressourcenanbieter“ in Ihrem Speicherkonto

Bevor die Definition Ihrer verwalteten Anwendung in Ihrem Speicherkonto bereitgestellt werden kann, weisen Sie dem **Applianceressourcenanbieter**-Benutzer die Rolle **Mitwirkender** im Speicherkonto-Gültigkeitsbereich zu. Mit dieser Zuweisung kann die Identität Definitionsdateien in den Container Ihres Speicherkontos schreiben.

Ausführliche Informationen finden Sie unter [Zuweisen von Azure-Rollen über das Azure-Portal](../../role-based-access-control/role-assignments-portal.md).

### <a name="deploy-the-managed-application-definition-with-an-arm-template"></a>Bereitstellen der Definition der verwalteten Anwendung mit einer ARM-Vorlage

Stellen Sie mithilfe der folgenden ARM-Vorlage Ihre gepackte verwaltete Anwendung als neue Definition der verwalteten Anwendung im Dienstkatalog bereit, dessen Definitionsdateien in Ihrem eigenen Speicherkonto gespeichert und verwaltet werden:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "applicationName": {
      "type": "string",
      "metadata": {
        "description": "Managed Application name"
      }
    },
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
    "definitionStorageResourceID": {
      "type": "string",
      "metadata": {
        "description": "Storage account resource ID for where you're storing your definition"
      }
    },
    "_artifactsLocation": {
      "type": "string",
      "metadata": {
        "description": "The base URI where artifacts required by this template are located."
      }
    }
  },
  "variables": {
    "lockLevel": "None",
    "description": "Sample Managed application definition",
    "displayName": "Sample Managed application definition",
    "managedApplicationDefinitionName": "[parameters('applicationName')]",
    "packageFileUri": "[parameters('_artifactsLocation')]",
    "defLocation": "[parameters('definitionStorageResourceID')]",
    "managedResourceGroupId": "[concat(subscription().id,'/resourceGroups/', concat(parameters('applicationName'),'_managed'))]",
    "applicationDefinitionResourceId": "[resourceId('Microsoft.Solutions/applicationDefinitions',variables('managedApplicationDefinitionName'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Solutions/applicationDefinitions",
      "apiVersion": "2020-08-21-preview",
      "name": "[variables('managedApplicationDefinitionName')]",
      "location": "[parameters('location')]",
      "properties": {
        "lockLevel": "[variables('lockLevel')]",
        "description": "[variables('description')]",
        "displayName": "[variables('displayName')]",
        "packageFileUri": "[variables('packageFileUri')]",
        "storageAccountId": "[variables('defLocation')]"
      }
    }
  ],
  "outputs": {}
}
```

Wir haben den `applicationDefinitions`-Eigenschaften eine neue Eigenschaft namens `storageAccountId` hinzugefügt und die ID des Speicherkontos, in dem die Definition gespeichert werden soll, als Wert dafür angegeben:

Sie können überprüfen, ob die Anwendungsdefinitionsdateien in Ihrem bereitgestellten Speicherkonto in einem Container mit dem Namen `applicationDefinitions` gespeichert sind.

> [!NOTE]
> Um für mehr Sicherheit zu sorgen, können Sie eine Definition für verwaltete Anwendungen erstellen und in einem [Azure-Speicherkontoblob mit aktivierter Verschlüsselung](../../storage/common/storage-service-encryption.md) speichern. Der Definitionsinhalt wird mithilfe der Verschlüsselungsoptionen des Speicherkontos verschlüsselt. Nur Benutzer mit Berechtigungen für die Datei können die Definition im Dienstkatalog anzeigen.

## <a name="make-sure-users-can-see-your-definition"></a>Sicherstellen, dass Benutzer Ihre Definition sehen können

Sie haben zwar Zugriff auf die Definition der verwalteten Anwendung, möchten aber sicherstellen, dass andere Benutzer in Ihrer Organisation darauf zugreifen können. Erteilen Sie ihnen mindestens die Leserrolle für die Definition. Diese Zugriffsebene haben sie möglicherweise vom Abonnement oder von der Ressourcengruppe geerbt. Informationen zum Überprüfen, wer Zugriff auf die Definition hat, sowie zum Hinzufügen von Benutzern oder Gruppen finden Sie unter [Zuweisen von Azure-Rollen über das Azure-Portal](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Nächste Schritte

Sie haben die Definition für die verwaltete Anwendung veröffentlicht. Als Nächstes erfahren Sie, wie Sie eine Instanz dieser Definition bereitstellen.

> [!div class="nextstepaction"]
> [Schnellstart: Bereitstellen einer Dienstkatalog-App](deploy-service-catalog-quickstart.md)
