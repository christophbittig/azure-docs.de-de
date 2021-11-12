---
title: Erstellen von Portalformularen für Vorlagenspezifikationen
description: Erfahren Sie, wie Sie Formulare erstellen, die in den Azure-Portalformularen angezeigt werden. Verwenden eines Formulars zum Bereitstellen einer Vorlagenspezifikation
ms.topic: tutorial
ms.date: 11/02/2021
ms.openlocfilehash: 3175264b8d3a5b9fd1698c8ccd1522ddb1f54b63
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479694"
---
# <a name="tutorial-create-azure-portal-forms-for-a-template-spec"></a>Tutorial: Erstellen von Azure-Portalformularen für eine Vorlagenspezifikation

Um die Benutzer bei der Bereitstellung einer [Vorlagenspezifikation](template-specs.md) zu unterstützen, können Sie ein Formular erstellen, das im Azure-Portal angezeigt wird. Mithilfe des Formulars können Benutzer Werte angeben, die als Parameter an die Vorlagenspezifikation übergeben werden.

Wenn Sie die Vorlagenspezifikation erstellen, fassen Sie das Formular und die Azure Resource Manager-Vorlage (ARM-Vorlage) in einem Paket zusammen. Wenn Sie die Vorlagenspezifikation über das Portal bereitstellen, wird das Formular automatisch gestartet.

:::image type="content" source="./media/template-specs-create-portal-forms/view-portal.png" alt-text="Screenshot: Formular zum Angeben von Werten für die Vorlagenspezifikation":::

## <a name="prerequisites"></a>Voraussetzungen

Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Verwenden Sie [mindestens Version 6.0.0](/powershell/azure/install-az-ps) von Azure PowerShell. Verwenden Sie für die Azure CLI [mindestens Version 2.24.0](/cli/azure/install-azure-cli).

## <a name="create-template"></a>Erstellen der Vorlage

Um die verschiedenen Portalelemente anzuzeigen, die in einem Formular verfügbar sind, verwenden Sie eine ARM-Vorlage mit mehreren Parametern. Die folgende Vorlage erstellt einen Schlüsseltresor, konfiguriert die Benutzerberechtigungen für den Schlüsseltresor und fügt ein Geheimnis hinzu.

Kopieren Sie diese Datei, und speichern Sie sie lokal. In dieser Anleitung wird davon ausgegangen, dass der Name der Datei **keyvault.json** lautet, aber Sie können ihr jeden beliebigen Namen geben.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Specifies the Azure location where the key vault should be created."
      }
    },
    "enabledForDeployment": {
      "type": "bool",
      "defaultValue": false,
      "allowedValues": [
        true,
        false
      ],
      "metadata": {
        "description": "Specifies whether Azure Virtual Machines are permitted to retrieve certificates stored as secrets from the key vault."
      }
    },
    "enabledForDiskEncryption": {
      "type": "bool",
      "defaultValue": false,
      "allowedValues": [
        true,
        false
      ],
      "metadata": {
        "description": "Specifies whether Azure Disk Encryption is permitted to retrieve secrets from the vault and unwrap keys."
      }
    },
    "enabledForTemplateDeployment": {
      "type": "bool",
      "defaultValue": false,
      "allowedValues": [
        true,
        false
      ],
      "metadata": {
        "description": "Specifies whether Azure Resource Manager is permitted to retrieve secrets from the key vault."
      }
    },
    "tenantId": {
      "type": "string",
      "defaultValue": "[subscription().tenantId]",
      "metadata": {
        "description": "Specifies the Azure Active Directory tenant ID that should be used for authenticating requests to the key vault. Get it by using Get-AzSubscription cmdlet."
      }
    },
    "objectId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the object ID of a user, service principal or security group in the Azure Active Directory tenant for the vault. The object ID must be unique for the list of access policies. Get it by using Get-AzADUser or Get-AzADServicePrincipal cmdlets."
      }
    },
    "keysPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to keys in the vault. Valid values are: all, encrypt, decrypt, wrapKey, unwrapKey, sign, verify, get, list, create, update, import, delete, backup, restore, recover, and purge."
      }
    },
    "secretsPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to secrets in the vault. Valid values are: all, get, list, set, delete, backup, restore, recover, and purge."
      }
    },
    "skuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "Specifies whether the key vault is a standard vault or a premium vault."
      }
    },
    "secretName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the secret that you want to create."
      }
    },
    "secretValue": {
      "type": "securestring",
      "metadata": {
        "description": "Specifies the value of the secret that you want to create."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('keyVaultName')]",
      "location": "[parameters('location')]",
      "properties": {
        "enabledForDeployment": "[parameters('enabledForDeployment')]",
        "enabledForDiskEncryption": "[parameters('enabledForDiskEncryption')]",
        "enabledForTemplateDeployment": "[parameters('enabledForTemplateDeployment')]",
        "tenantId": "[parameters('tenantId')]",
        "accessPolicies": [
          {
            "objectId": "[parameters('objectId')]",
            "tenantId": "[parameters('tenantId')]",
            "permissions": {
              "keys": "[parameters('keysPermissions')]",
              "secrets": "[parameters('secretsPermissions')]"
            }
          }
        ],
        "sku": {
          "name": "[parameters('skuName')]",
          "family": "A"
        },
        "networkAcls": {
          "defaultAction": "Allow",
          "bypass": "AzureServices"
        }
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults/secrets",
      "apiVersion": "2019-09-01",
      "name": "[concat(parameters('keyVaultName'), '/', parameters('secretName'))]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.KeyVault/vaults', parameters('keyVaultName'))]"
      ],
      "properties": {
        "value": "[parameters('secretValue')]"
      }
    }
  ]
}
```

## <a name="create-default-form"></a>Erstellen eines Standardformulars

Das Azure-Portal bietet eine Sandbox für die Erstellung und Vorschau von Formularen. Diese Sandbox kann ein Formular aus einer vorhandenen ARM-Vorlage generieren. Sie verwenden dieses Standardformular, um mit dem Erstellen eines Formulars für Ihre Vorlagenspezifikation zu beginnen.

1. Öffnen Sie [Sandbox für Formularansicht](https://aka.ms/form/sandbox).

1. Legen Sie den **Pakettyp** auf **Benutzerdefinierte Vorlage** fest.

   :::image type="content" source="./media/template-specs-create-portal-forms/package-type.png" alt-text="Screenshot: Festlegen des Pakettyps auf eine benutzerdefinierte Vorlage":::

1. Wählen Sie das Symbol zum Öffnen einer vorhandenen Vorlage aus.

   :::image type="content" source="./media/template-specs-create-portal-forms/open-template.png" alt-text="Screenshot: Symbol zum Öffnen einer Datei":::

1. Navigieren Sie zu der Schlüsseltresorvorlage, die Sie lokal gespeichert haben. Wählen Sie sie aus, und klicken Sie auf **Öffnen**.
1. Wenn Sie gefragt werden, ob die aktuellen Änderungen überschrieben werden sollen, klicken Sie auf **Ja**.
1. Das automatisch generierte Formular wird im Codefenster angezeigt. Um zu sehen, ob das Formular auch ohne Modifikationen funktioniert, wählen Sie **Vorschau** aus.

   :::image type="content" source="./media/template-specs-create-portal-forms/preview-form.png" alt-text="Screenshot: Auswählen der Vorschau":::

1. Die Sandbox zeigt das Formular an. Es enthält Felder zum Auswählen eines Abonnements, einer Ressourcengruppe und einer Region. Außerdem werden Felder für alle Parameter aus der Vorlage angezeigt.

   Die meisten Felder sind Textfelder, aber einige Felder sind spezifisch für den Typ des Parameters. Wenn Ihre Vorlage zulässige Werte für einen Parameter enthält, verwendet das automatisch generierte Formular ein Dropdownelement. Das Dropdownelement wird vorab mit den zulässigen Werten aufgefüllt.

   > [!WARNING]
   > Wählen Sie nicht **Erstellen** aus, da dies eine tatsächliche Bereitstellung auslöst. Sie haben später in diesem Tutorial die Möglichkeit, die Vorlagenspezifikation bereitzustellen.

## <a name="customize-form"></a>Anpassen des Formulars

Das Standardformular ist ein guter Ausgangspunkt für das Verständnis von Formularen, aber in der Regel möchten Sie es anpassen. Sie können das Formular in der Sandbox oder in Visual Studio Code bearbeiten. Die Vorschauoption ist nur in der Sandbox verfügbar.

1. Legen wir nun das richtige Schema fest. Ersetzen Sie den Schematext durch den folgenden:

   ::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/keyvaultform.json" range="1-2" highlight="2" :::

1. Geben Sie dem Formular einen Titel (**title**), der seine Verwendung beschreibt.

   ::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/keyvaultform.json" range="1-6" highlight="6" :::

1. In Ihrem Standardformular wurden alle Felder für Ihre Vorlage in einem Schritt namens **Basics** zusammengefasst. Damit Benutzer die angegebenen Werte besser verstehen können, unterteilen Sie das Formular in Schritte. Jeder Schritt enthält Felder, die sich auf einen logischen Teil der bereitzustellenden Lösung beziehen.

   Suchen Sie den Schritt mit der Bezeichnung **Basics**. Sie behalten diesen Schritt bei, fügen aber darunter Schritte hinzu. Die neuen Schritte konzentrieren sich auf das Konfigurieren des Schlüsseltresors, das Festlegen von Benutzerberechtigungen und das Angeben des Geheimnisses. Stellen Sie sicher, dass Sie nach dem Schritt „Basics“ ein Komma hinzufügen.

   ::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/steps.json" highlight="15-32" :::

   > [!IMPORTANT]
   > Für Eigenschaften im Formular muss die Groß-/Kleinschreibung beachtet werden. Stellen Sie sicher, dass Sie die in den Beispielen gezeigte Groß-/Kleinschreibung verwenden.

1. Wählen Sie **Vorschau** aus. Sie sehen die Schritte, aber die meisten von enthalten keine Elemente.

   :::image type="content" source="./media/template-specs-create-portal-forms/view-steps.png" alt-text="Screenshot: Formularschritte":::

1. Verschieben Sie nun Elemente in die geeigneten Schritte. Beginnen Sie mit den Elementen **Secret Name** und **Secret Value**. Entfernen Sie diese Elemente aus dem Schritt **Basics**, und fügen Sie in den Schritt **Secret** ein.

   ```json
   {
     "name": "secret",
     "label": "Secret",
     "elements": [
       {
         "name": "secretName",
         "type": "Microsoft.Common.TextBox",
         "label": "Secret Name",
         "defaultValue": "",
         "toolTip": "Specifies the name of the secret that you want to create.",
         "constraints": {
           "required": true,
           "regex": "",
           "validationMessage": ""
         },
         "visible": true
       },
       {
         "name": "secretValue",
         "type": "Microsoft.Common.PasswordBox",
         "label": {
           "password": "Secret Value",
           "confirmPassword": "Confirm password"
         },
         "toolTip": "Specifies the value of the secret that you want to create.",
         "constraints": {
           "required": true,
           "regex": "",
           "validationMessage": ""
         },
         "options": {
           "hideConfirmation": true
         },
         "visible": true
       }
     ]
   }
   ```

1. Wenn Sie Elemente verschieben, müssen Sie den Abschnitt `outputs` korrigieren. Derzeit verweist der Abschnitt „outputs“ auf diese Elemente, als ob sie sich noch im Schritt „Grundlagen“ befinden würden. Korrigieren Sie die Syntax, sodass sie auf die Elemente im Schritt `secret` verweist.

   ```json
   "outputs": {
     "parameters": {
       ...
       "secretName": "[steps('secret').secretName]",
       "secretValue": "[steps('secret').secretValue]"
     }
   ```

1. Fahren Sie mit dem Verschieben von Elementen in die entsprechenden Schritte fort. Anstatt jeden Schritt einzeln durchzugehen, sehen Sie sich das aktualisierte Formular an.

   ::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/keyvaultform.json" :::

Speichern Sie diese Datei lokal unter dem Namen **keyvaultform.json**.

## <a name="create-template-spec"></a>Erstellen von Vorlagenspezifikationen

Wenn Sie die Vorlagenspezifikation erstellen, geben Sie beide Dateien an.

Verwenden Sie für PowerShell [New-AzTemplateSpec](/powershell/module/az.resources/new-aztemplatespec), und geben Sie das Formular im Parameter `-UIFormDefinitionFile` an.

```azurepowershell
New-AzTemplateSpec `
  -name keyvaultspec `
  -version 1 `
  -ResourceGroupName templateSpecRG `
  -location westus2 `
  -templatefile keyvault.json `
  -UIFormDefinitionFile keyvaultform.json
```

Bei Verwendung der Azure CLI verwenden Sie [az ts create](/cli/azure/ts#az_ts_create) und geben das Formular im Parameter `--ui-form-definition` an.

```azurecli
az ts create \
  --name keyvaultspec \
  --version 1 \
  --resource-group templatespecRG \
  --location westus2 \
  --template-file keyvault.json \
  --ui-form-definition keyvaultform.json
```

## <a name="deploy-through-portal"></a>Bereitstellung über das Portal

Navigieren Sie zum Testen des Formulars im Portal zu Ihrer Vorlagenspezifikation. Wählen Sie **Bereitstellen** aus.

:::image type="content" source="./media/template-specs-create-portal-forms/deploy-template-spec.png" alt-text="Screenshot: Übersicht der Vorlagenspezifikationen mit Bereitstellungsoption":::

Das von Ihnen erstellte Formular wird angezeigt. Gehen Sie die Schritte durch, und geben Sie Werte für die Felder an.

Im Schritt **Basics** wird ein Feld für die **Region** angezeigt. Dieses Feld wird für den Standort der Ressourcengruppe verwendet. Im Schritt **Key Vault** wird ein Feld **Location** angezeigt. Dieses Feld wird für den Standort des Schlüsseltresors verwendet.

Im Schritt **Permissions** können Sie Ihre eigene Benutzer-ID für die Objekt-ID angeben. Verwenden Sie den Standardwert (`["list"]`) für Schlüssel- und Geheimnisberechtigungen. Sie werden diese Option im nächsten Abschnitt verbessern.

Nachdem Sie alle anderen Werte angegeben haben, wählen Sie **Erstellen** aus, um die Vorlagenspezifikation bereitzustellen.

## <a name="improve-the-form"></a>Verbessern des Formulars

Im vorherigen Abschnitt haben Sie Schritte hinzugefügt und Elemente verschoben, jedoch keine der Standardverhaltensweisen geändert. In diesem Abschnitt nehmen Sie Änderungen vor, um die Benutzerfreundlichkeit Ihrer Vorlagenspezifikation zu verbessern.

Zuvor waren die zwei Berechtigungsfelder Textfelder. Nun verwenden Sie ein Dropdownelement. Legen Sie den Typ auf `Microsoft.Common.DropDown` fest.

Aktualisieren Sie `keysPermissions`:

::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/keyvaultform2.json" range="169-171" highlight="3" :::

Und `secretsPermissions`:

::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/keyvaultform2.json" range="253-255" highlight="3" :::

Diese Felder müssen ein Array an die Vorlage übergeben. Ein normales Dropdownelement funktioniert nicht, weil Sie nur einen Wert auswählen können. Um mehr als einen Wert auszuwählen und als Array zu übergeben, fügen Sie das Feld `multiselect` hinzu und legen es auf `true` fest.

::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/keyvaultform2.json" range="169-173" highlight="5" :::

::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/keyvaultform2.json" range="253-257" highlight="5" :::

Abschließend müssen Sie die zulässigen Werte für das Dropdownelement und einen Standardwert angeben.

::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/keyvaultform2.json" range="169-304" highlight="6-8,12-81,90-92,96-133" :::

Erstellen Sie eine neue Version der Vorlagenspezifikation.

Mit PowerShell:

```azurepowershell
New-AzTemplateSpec `
  -name keyvaultspec `
  -version 2 `
  -ResourceGroupName templateSpecRG `
  -location westus2 `
  -templatefile keyvault.json `
  -UIFormDefinitionFile keyvaultform.json
```

Oder der Azure CLI:

```azurecli
az ts create \
  --name keyvaultspec \
  --version 2 \
  --resource-group templatespecRG \
  --location westus2 \
  --template-file keyvault.json \
  --ui-form-definition keyvaultform.json
```

Stellen Sie Ihre Vorlagenspezifikation mit dem verbesserten Portalformular erneut bereit.

:::image type="content" source="./media/template-specs-create-portal-forms/view-portal.png" alt-text="Screenshot: Formular zum Angeben von Werten für die Vorlagenspezifikation":::

Beachten Sie, dass Ihre Berechtigungsfelder jetzt in einem Dropdownelement enthalten sind, das mehrere Werte zulässt.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Bereitstellung einer Vorlagenspezifikation als verknüpfte Vorlage finden Sie unter [Tutorial: Bereitstellen einer Vorlagenspezifikation als verknüpfte Vorlage](template-specs-deploy-linked-template.md).
