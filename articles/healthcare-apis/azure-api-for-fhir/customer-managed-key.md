---
title: Konfigurieren von kundenseitig verwalteten Schlüsseln für Azure API for FHIR
description: Unterstützung von Bring Your Own Key in Azure API for FHIR per Cosmos DB
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 05/04/2021
ms.author: ginle
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0c32725d020043c3cd62828e4bdccfafef2f53aa
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121783863"
---
# <a name="configure-customer-managed-keys-at-rest"></a>Konfigurieren kundenseitig verwalteter Schlüssel im Ruhezustand

Wenn Sie ein neues Azure API for FHIR-Konto erstellen, werden Ihre Daten standardmäßig mit von Microsoft verwalteten Schlüsseln verschlüsselt. Sie können jetzt eine zweite Verschlüsselungsebene für die Daten hinzufügen, indem Sie einen eigenen Schlüssel verwenden, den Sie selbst auswählen und verwalten.

In Azure wird dies normalerweise mit einem Verschlüsselungsschlüssel in der Azure Key Vault-Instanz des Kunden erreicht. Azure SQL, Azure Storage und Cosmos DB sind Beispiele für Dienste, die derzeit über diese Funktion verfügen. In Azure API for FHIR wird diese Unterstützung über Cosmos DB genutzt. Wenn Sie ein Konto erstellen, haben Sie die Möglichkeit, einen URI für einen Azure Key Vault-Schlüssel anzugeben. Dieser Schlüssel wird an Cosmos DB übergeben, wenn das DB-Konto bereitgestellt wird. Wenn eine FHIR-Anforderung gesendet wird, ruft Cosmos DB Ihren Schlüssel ab und verwendet ihn zum Ver- bzw. Entschlüsseln der Daten. 

Informationen zu den ersten Schritte finden Sie unter den folgenden Links:

- [Registrieren des Azure Cosmos DB-Ressourcenanbieters für Ihr Azure-Abonnement](../../cosmos-db/how-to-setup-cmk.md#register-resource-provider) 
- [Konfigurieren Ihrer Azure Key Vault-Instanz](../../cosmos-db/how-to-setup-cmk.md#configure-your-azure-key-vault-instance)
- [Hinzufügen einer Zugriffsrichtlinie zu Ihrer Azure Key Vault-Instanz](../../cosmos-db/how-to-setup-cmk.md#add-access-policy)
- [Generieren eines Schlüssels in Azure Key Vault](../../cosmos-db/how-to-setup-cmk.md#generate-a-key-in-azure-key-vault)

## <a name="using-azure-portal"></a>Verwenden des Azure-Portals

Wenn Sie Ihr Azure API for FHIR-Konto auf Azure-Portal erstellen, werden Sie auf der Registerkarte **Zusätzliche** Einstellungen unter dem **Einstellungen Datenbank** auf die Konfigurationsoption **Datenverschlüsselung** klicken. Standardmäßig wird die Option Vom Dienst verwalteter Schlüssel ausgewählt.

> [!Important]
> Die Datenverschlüsselungsoption ist nur verfügbar, wenn die Azure API for FHIR erstellt wird, und kann danach nicht mehr geändert werden. Sie können den Verschlüsselungsschlüssel jedoch anzeigen und aktualisieren, wenn die Option **Vom Kunden verwalteter Schlüssel** ausgewählt ist. 


Sie können Ihren Schlüssel in der Schlüsselauswahl auswählen:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-keypicker.png" alt-text="Schlüsselauswahl":::

Sie können hier auch Ihren Azure Key Vault Schlüssel angeben, indem Sie die Option **Kundenverwaltete Schlüssel** auswählen.
 
Sie können den Schlüssel-URI auch hier eingeben:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-create.png" alt-text="Erstellen einer Azure API for FHIR-Instanz":::

> [!Important]
> Stellen Sie sicher, dass alle Berechtigungen für Azure Key Vault entsprechend festgelegt sind. Weitere Informationen finden Sie unter [Hinzufügen einer Zugriffsrichtlinie zu Ihrer Azure Key Vault Instanz.](../../cosmos-db/how-to-setup-cmk.md#add-access-policy) Stellen Sie außerdem sicher, dass das soft delete in den Eigenschaften des Key Vault aktiviert ist. Wenn Sie diese Schritte nicht ausführen, tritt ein Bereitstellungsfehler auf. Weitere Informationen finden Sie unter Überprüfen, ob das soft delete für [einen Schlüsseltresor aktiviert ist, und Aktivieren von Soft Delete.](../../key-vault/general/key-vault-recovery.md?tabs=azure-portal#verify-if-soft-delete-is-enabled-on-a-key-vault-and-enable-soft-delete)

Für vorhandene FHIR-Konten können Sie die Auswahl der Schlüsselverschlüsselung **(vom Dienst verwalteter Schlüssel** oder vom **Kunden verwalteter Schlüssel)** auf dem Blatt **Datenbank** anzeigen, wie unten dargestellt. Die Konfigurationsoption kann nicht mehr geändert werden, nachdem sie ausgewählt wurde. Sie können aber Ihren Schlüssel ändern und aktualisieren.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-database.png" alt-text="Datenbank":::

Darüber hinaus können Sie auch eine neue Version des angegebenen Schlüssels erstellen. Nach der Erstellung werden Ihre Daten mit der neuen Version verschlüsselt, ohne dass es zu einer Dienstunterbrechung kommt. Sie können den Zugriff auf den Schlüssel auch entfernen, damit nicht auf die Daten zugegriffen werden kann. Wenn der Schlüssel deaktiviert ist, tritt bei Abfragen ein Fehler auf. Wird der Schlüssel wieder aktiviert, sind die Abfragen wieder erfolgreich.

## <a name="using-azure-powershell"></a>Verwenden von Azure PowerShell

Mit dem Azure Key Vault-Schlüssel-URI können Sie den CMK mithilfe von PowerShell konfigurieren. Führen Sie dazu den folgenden PowerShell-Befehl aus:

```powershell
New-AzHealthcareApisService
    -Name "myService"
    -Kind "fhir-R4"
    -ResourceGroupName "myResourceGroup"
    -Location "westus2"
    -CosmosKeyVaultKeyUri "https://<my-vault>.vault.azure.net/keys/<my-key>"
```

## <a name="using-azure-cli"></a>Verwenden der Azure-Befehlszeilenschnittstelle

Wie bei der Methode mit PowerShell können Sie den CMK konfigurieren, indem Sie den Azure Key Vault-Schlüssel-URI unter dem Parameter `key-vault-key-uri` übergeben und den folgenden CLI-Befehl ausführen: 

```azurecli-interactive
az healthcareapis service create
    --resource-group "myResourceGroup"
    --resource-name "myResourceName"
    --kind "fhir-R4"
    --location "westus2"
    --cosmos-db-configuration key-vault-key-uri="https://<my-vault>.vault.azure.net/keys/<my-key>"

```
## <a name="using-azure-resource-manager-template"></a>Verwenden von Azure Resource Manager-Vorlagen

Mit dem Azure Key Vault-Schlüssel-URI können Sie den CMK konfigurieren, indem Sie den URI unter der Eigenschaft **keyVaultKeyUri** im Objekt **properties** übergeben.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "services_myService_name": {
            "defaultValue": "myService",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.HealthcareApis/services",
            "apiVersion": "2020-03-30",
            "name": "[parameters('services_myService_name')]",
            "location": "westus2",
            "kind": "fhir-R4",
            "properties": {
                "accessPolicies": [],
                "cosmosDbConfiguration": {
                    "offerThroughput": 400,
                    "keyVaultKeyUri": "https://<my-vault>.vault.azure.net/keys/<my-key>"
                },
                "authenticationConfiguration": {
                    "authority": "https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db47",
                    "audience": "[concat('https://', parameters('services_myService_name'), '.azurehealthcareapis.com')]",
                    "smartProxyEnabled": false
                },
                "corsConfiguration": {
                    "origins": [],
                    "headers": [],
                    "methods": [],
                    "maxAge": 0,
                    "allowCredentials": false
                }
            }
        }
    ]
}
```

Darüber hinaus können Sie die Vorlage mithilfe des folgenden PowerShell-Skripts bereitstellen:

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$accountLocation = "West US 2"
$keyVaultKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-key>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "deploy.json" `
    -accountName $accountName `
    -location $accountLocation `
    -keyVaultKeyUri $keyVaultKeyUri
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie vom Kunden verwaltete Schlüssel im Ruhezustand mithilfe der Azure-Portal, PowerShell, CLI und Resource Manager Vorlage konfigurieren. Weitere Informationen finden Sie im Abschnitt Häufig gestellte Fragen zu Azure Cosmos DB. 
 
>[!div class="nextstepaction"]
>[Häufig gestellte Fragen](../../cosmos-db/how-to-setup-cmk.md#frequently-asked-questions)