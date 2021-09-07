---
title: Key Vault-Geheimnis mit Bicep
description: Informationen zum Übergeben eines geheimen Schlüssels aus einem Schlüsseltresor als Parameter während der Bicep-Bereitstellung.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/18/2021
ms.openlocfilehash: e940a812b4e010e9499a9a85cfd400b679d43781
ms.sourcegitcommit: 351279883100285f935d3ca9562e9a99d3744cbd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/19/2021
ms.locfileid: "112376304"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-bicep-deployment"></a>Verwenden von Azure Key Vault zum Übergeben eines sicheren Parameterwerts während der Bicep-Bereitstellung

Anstatt einen sicheren Wert (wie ein Kennwort) direkt in Ihre Bicep-Datei oder Parameterdatei einzufügen, können Sie den Wert während einer Bereitstellung aus einem [Azure Key Vault](../../key-vault/general/overview.md) abrufen. Wenn ein [Modul](./modules.md) einen `string`-Parameter mit `secure:true`-Modifizierer erwartet, können Sie die [getSecret-Funktion](bicep-functions-resource.md#getsecret) verwenden, um ein Schlüsseltresorgeheimnis abzurufen. Der Wert wird nie offengelegt, da Sie nur auf die Schlüsseltresor-ID verweisen.

> [!IMPORTANT]
> Der Schwerpunkt dieses Artikels liegt auf der Übergabe eines vertraulichen Werts als Vorlagenparameter. Wenn das Geheimnis als Parameter übergeben wird, kann sich der Schlüsseltresor in einem anderen Abonnement befinden als die Ressourcengruppe, die als Ziel für die Bereitstellung verwendet wird. 
>
> In diesem Artikel wird nicht erläutert, wie Sie eine VM-Eigenschaft auf die URL eines Zertifikats in einem Schlüsseltresor festlegen. Eine Schnellstartvorlage für dieses Szenario finden Sie unter [Installieren eines Zertifikats aus Azure Key Vault auf einem virtuellen Computer](https://github.com/Azure/azure-quickstart-templates/tree/master/demos/vm-winrm-keyvault-windows).

## <a name="deploy-key-vaults-and-secrets"></a>Bereitstellen von Schlüsseltresoren und Geheimnissen

Um während der Bicep-Bereitstellung auf einen Schlüsseltresor zuzugreifen, legen Sie `enabledForTemplateDeployment` für den Schlüsseltresor auf `true` fest.

Wenn Sie bereits über einen Schlüsseltresor verfügen, stellen Sie sicher, dass er Vorlagenbereitstellungen zulässt.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
az keyvault update  --name ExampleVault --enabled-for-template-deployment true
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName ExampleVault -EnabledForTemplateDeployment
```

---

Um einen neuen Schlüsseltresor zu erstellen und ein Geheimnis hinzuzufügen, verwenden Sie:

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
az group create --name ExampleGroup --location centralus
az keyvault create \
  --name ExampleVault \
  --resource-group ExampleGroup \
  --location centralus \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name ExampleVault --name "ExamplePassword" --value "hVFkk965BuUv"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name ExampleGroup -Location centralus
New-AzKeyVault `
  -VaultName ExampleVault `
  -resourceGroupName ExampleGroup `
  -Location centralus `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
$secret = Set-AzKeyVaultSecret -VaultName ExampleVault -Name 'ExamplePassword' -SecretValue $secretvalue
```

---

Als Besitzer des Schlüsseltresors haben Sie automatisch Zugriff auf die Erstellung von Geheimnissen. Wenn der Benutzer, der mit Geheimnissen arbeitet, nicht der Besitzer des Schlüsseltresors ist, gewähren Sie den Zugriff mit:

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
az keyvault set-policy \
  --upn <user-principal-name> \
  --name ExampleVault \
  --secret-permissions set delete get list
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$userPrincipalName = "<Email Address of the deployment operator>"

Set-AzKeyVaultAccessPolicy `
  -VaultName ExampleVault `
  -UserPrincipalName <user-principal-name> `
  -PermissionsToSecrets set,delete,get,list
```

---

Weitere Informationen zum Erstellen von Schlüsseltresoren und zum Hinzufügen von Geheimnissen finden Sie unter:

- [Festlegen und Abrufen eines Geheimnisses über die Befehlszeilenschnittstelle](../../key-vault/secrets/quick-create-cli.md)
- [Festlegen und Abrufen eines Geheimnisses mit PowerShell](../../key-vault/secrets/quick-create-powershell.md)
- [Festlegen und Abrufen eines Geheimnisses über das Portal](../../key-vault/secrets/quick-create-portal.md)
- [Festlegen und Abrufen eines Geheimnisses mit .NET](../../key-vault/secrets/quick-create-net.md)
- [Festlegen und Abrufen eines Geheimnisses mit Node.js](../../key-vault/secrets/quick-create-node.md)

## <a name="grant-access-to-the-secrets"></a>Gewähren des Zugriffs auf die Geheimnisse

Der Benutzer, der die Bicep-Datei bereitstellt, muss die Berechtigung `Microsoft.KeyVault/vaults/deploy/action` für den Bereich der Ressourcengruppe und des Schlüsseltresors besitzen. Die Rollen [Besitzer](../../role-based-access-control/built-in-roles.md#owner) und [Mitwirkender](../../role-based-access-control/built-in-roles.md#contributor) gewähren diesen Zugriff. Wenn Sie den Schlüsseltresor erstellt haben, sind Sie der Besitzer und verfügen somit über die Berechtigung.

Das folgende Verfahren zeigt das Erstellen einer Rolle mit der Mindestberechtigung und das Zuweisen des Benutzers.

1. Erstellen einer benutzerdefinierten Rollendefinition (JSON-Datei):

    ```json
    {
      "Name": "Key Vault Bicep deployment operator",
      "IsCustom": true,
      "Description": "Lets you deploy a Bicep file with the access to the secrets in the Key Vault.",
      "Actions": [
        "Microsoft.KeyVault/vaults/deploy/action"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```

    Ersetzen Sie „00000000-0000-0000-0000-000000000000“ durch die Abonnement-ID.

2. Erstellen Sie die neue Rolle mithilfe der JSON-Datei:

    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

    ```azurecli-interactive
    az role definition create --role-definition "<path-to-role-file>"
    az role assignment create \
      --role "Key Vault resource manager template deployment operator" \
      --assignee <user-principal-name> \
      --resource-group ExampleGroup
    ```

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell-interactive
    New-AzRoleDefinition -InputFile "<path-to-role-file>"
    New-AzRoleAssignment `
      -ResourceGroupName ExampleGroup `
      -RoleDefinitionName "Key Vault resource manager template deployment operator" `
      -SignInName <user-principal-name>
    ```

    ---

    Die Beispiele weisen dem Benutzer auf Ressourcengruppenebene die benutzerdefinierte Rolle zu.

Wenn Sie einen Schlüsseltresor mit der Bicep-Datei für eine [verwaltete Anwendung](../managed-applications/overview.md) verwenden, müssen Sie den Zugriff auf den **Geräte-Ressourcenanbieter**-Dienstprinzipal gewähren. Weitere Informationen finden Sie unter [Zugreifen auf das Geheimnis im Schlüsseltresor bei der Bereitstellung von Azure Managed Applications](../managed-applications/key-vault-access.md).

## <a name="use-getsecret-function"></a>Verwenden der getSecret-Funktion

Sie können die [getSecret-Funktion](./bicep-functions-resource.md#getsecret) verwenden, um ein Schlüsseltresorgeheimnis abzurufen und den Wert an einen `string`-Parameter eines Moduls zu übergeben. Die `getSecret`-Funktion kann nur für eine `Microsoft.KeyVault/vaults`-Ressource aufgerufen und nur mit einem Parameter mit dem `@secure()`-Decorator verwendet werden.

Die folgende Bicep-Datei erstellt einen Azure SQL Server. Der `adminPassword`-Parameter verfügt über einen `@secure()`-Decorator.

```bicep
param sqlServerName string
param adminLogin string

@secure()
param adminPassword string

resource sqlServer 'Microsoft.Sql/servers@2020-11-01-preview' = {
  name: sqlServerName
  location: resourceGroup().location
  properties: {
    administratorLogin: adminLogin
    administratorLoginPassword: adminPassword
    version: '12.0'
  }
}
```

Wir verwenden die vorangehende Bicep-Datei als Modul, vorausgesetzt, der Dateiname lautet *sql.bicep*, im selben Verzeichnis wie die Bicep-Hauptdatei.

Die folgende Bicep-Datei verwendet „sql.bicep“ als Modul.  Die Bicep-Datei verweist auf einen vorhandenen Schlüsseltresor, ruft die `getSecret`-Funktion auf, um das Schlüsseltresorgeheimnis abzurufen, und übergibt den Wert dann als Parameter an das Modul.

```bicep
param sqlServerName string
param adminLogin string

param subscriptionId string
param kvResourceGroup string
param kvName string

resource kv 'Microsoft.KeyVault/vaults@2019-09-01' existing = {
  name: kvName
  scope: resourceGroup(subscriptionId, kvResourceGroup )
}

module sql './sql.bicep' = {
  name: 'deploySQL'
  params: {
    sqlServerName: sqlServerName
    adminLogin: adminLogin
    adminPassword: kv.getSecret('vmAdminPassword')
  }
}
```

## <a name="reference-secrets-in-parameter-file"></a>Verweisen auf Geheimnisse in einer Parameterdatei

Wenn Sie kein Modul verwenden möchten, können Sie direkt in der Parameterdatei auf den Schlüsseltresor verweisen. Die folgende Abbildung zeigt, wie die Parameterdatei auf das Geheimnis verweist und diesen Wert an die Bicep-Datei übergibt.

![Diagramm der Resource Manager-Schlüsseltresorintegration](./media/key-vault-parameter/statickeyvault.png)

Die folgende Bicep-Datei stellt einen SQL-Server bereit, der ein Administratorkennwort enthält. Der Kennwortparameter ist auf eine sichere Zeichenfolge festgelegt. Die Bicep-Datei gibt jedoch nicht an, woher dieser Wert stammt.

```bicep
param adminLogin string

@secure()
param adminPassword string

param sqlServerName string

resource sqlServer 'Microsoft.Sql/servers@2020-11-01-preview' = {
  name: sqlServerName
  location: resourceGroup().location
  properties: {
    administratorLogin: adminLogin
    administratorLoginPassword: adminPassword
    version: '12.0'
  }
}
```

---

Erstellen Sie jetzt eine Parameterdatei für die vorherige Bicep-Datei. Geben Sie in der Parameterdatei einen Parameter an, der dem Namen des Parameters in der Bicep-Datei entspricht. Verweisen Sie für den Parameterwert auf das Geheimnis aus dem Schlüsseltresor. Sie verweisen auf den geheimen Schlüssel, indem Sie den Ressourcenbezeichner des Schlüsseltresors und den Namen des Geheimnisses übergeben:

In der folgenden Parameterdatei muss das Schlüsseltresorgeheimnis bereits vorhanden sein, und Sie geben einen statischen Wert für seine Ressourcen-ID an.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminLogin": {
      "value": "exampleadmin"
    },
    "adminPassword": {
      "reference": {
        "keyVault": {
          "id": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<vault-name>"
        },
        "secretName": "ExamplePassword"
      }
    },
    "sqlServerName": {
      "value": "<your-server-name>"
    }
  }
}
```

Wenn Sie eine andere Version des Geheimnisses als die aktuelle Version verwenden müssen, fügen Sie die `secretVersion`Eigenschaft ein.

```json
"secretName": "ExamplePassword",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Stellen Sie die Vorlage bereit, und übergeben Sie sie in der Parameterdatei:

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
az group create --name SqlGroup --location westus2
az deployment group create \
  --resource-group SqlGroup \
  --template-file <Bicep-file> \
  --parameters <parameter-file>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile <Bicep-file> `
  -TemplateParameterFile <parameter-file>
```

---

## <a name="next-steps"></a>Nächste Schritte

- Allgemeine Informationen zu Schlüsseltresoren finden Sie unter [Was ist der Azure Schlüsseltresor?](../../key-vault/general/overview.md)
- Vollständige Beispiele für das Referenzieren von Schlüsselgeheimnissen finden Sie unter [Beispiele für Schlüsseltresore](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples) auf GitHub.
- Ein Microsoft Learn-Modul, das das Übergeben eines sicheren Werts aus einem Schlüsseltresor behandelt, finden Sie unter [Verwalten komplexer Cloudbereitstellungen mithilfe erweiterter ARM-Vorlagenfunktionen](/learn/modules/manage-deployments-advanced-arm-template-features/).
