---
title: Verwenden von Key Vault beim Bereitstellen einer verwalteten App
description: Zeigt die Verwendung von Zugriffsgeheimnissen in Azure Key Vault bei der Bereitstellung von verwalteten Anwendungen
author: tfitzmac
ms.custom: subject-rbac-steps
ms.topic: conceptual
ms.date: 08/16/2021
ms.author: tomfitz
ms.openlocfilehash: d5a6dc1de2ee574a69b8dab746a24bcf82b44d64
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122343339"
---
# <a name="access-key-vault-secret-when-deploying-azure-managed-applications"></a>Zugreifen auf das Geheimnis im Schlüsseltresor bei der Bereitstellung von Azure Managed Applications

Wenn Sie einen zu schützenden Wert (z.B. ein Kennwort) während der Bereitstellung als Parameter übergeben müssen, können Sie den Wert aus einer Instanz von [Azure Key Vault](../../key-vault/general/overview.md) abrufen. Um bei der Bereitstellung verwalteter Anwendungen auf den Schlüsseltresor zuzugreifen, müssen Sie Zugriff auf den Dienstprinzipal des **Applianceressourcenanbieters** gewähren. Der Managed Applications-Dienst verwendet diese Identität, um Vorgänge auszuführen. Um einen Wert während der Bereitstellung erfolgreich aus einem Schlüsseltresor abzurufen, muss der Dienstprinzipal auf den Schlüsseltresor zugreifen können.

Dieser Artikel beschreibt, wie Sie den Schlüsseltresor zum Arbeiten mit verwalteten Anwendungen konfigurieren.

## <a name="enable-template-deployment"></a>Aktivieren der Vorlagenbereitstellung

1. Wählen Sie im Portal Ihren Schlüsseltresor aus.

1. Klicken Sie auf **Zugriffsrichtlinien**.   

   ![Zugriffsrichtlinien auswählen](./media/key-vault-access/select-access-policies.png)

1. Wählen Sie **Klicken Sie, um erweiterte Zugriffsrichtlinien anzuzeigen.** aus.

   ![Erweiterte Zugriffsrichtlinien anzeigen](./media/key-vault-access/advanced.png)

1. Wählen Sie **Zugriff auf Azure Resource Manager für Vorlagenbereitstellung aktivieren** aus. Wählen Sie anschließend **Speichern** aus.

   ![Aktivieren der Vorlagenbereitstellung](./media/key-vault-access/enable-template.png)

## <a name="add-service-as-contributor"></a>Hinzufügen des Diensts als Mitwirkender

Weisen Sie die Rolle **Mitwirkender** dem Benutzer **Applianceressourcenanbieter** im Schlüsseltresorbereich zu.

Ausführliche Informationen finden Sie unter [Zuweisen von Azure-Rollen über das Azure-Portal](../../role-based-access-control/role-assignments-portal.md).

## <a name="reference-key-vault-secret"></a>Verweisen auf Schlüsseltresorgeheimnis

Um ein Geheimnis aus einem Schlüsseltresor an eine Vorlage in Ihrer verwalteten Anwendung zu übergeben, müssen Sie eine [verknüpfte oder geschachtelte Vorlage](../templates/linked-templates.md) verwenden und in den Parametern für die verknüpfte oder geschachtelte Vorlage auf den Schlüsseltresor verweisen. Geben Sie die Ressourcen-ID des Schlüsseltresors und den Namen des Geheimnisses an.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "The location where the resources will be deployed."
      }
    },
    "vaultName": {
      "type": "string",
      "metadata": {
        "description": "The name of the keyvault that contains the secret."
      }
    },
    "secretName": {
      "type": "string",
      "metadata": {
        "description": "The name of the secret."
      }
    },
    "vaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "The name of the resource group that contains the keyvault."
      }
    },
    "vaultSubscription": {
      "type": "string",
      "defaultValue": "[subscription().subscriptionId]",
      "metadata": {
        "description": "The name of the subscription that contains the keyvault."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminLogin": {
              "type": "string"
            },
            "adminPassword": {
              "type": "securestring"
            },
            "location": {
              "type": "string"
            }
          },
          "variables": {
            "sqlServerName": "[concat('sql-', uniqueString(resourceGroup().id, 'sql'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Sql/servers",
              "apiVersion": "2018-06-01-preview",
              "name": "[variables('sqlServerName')]",
              "location": "[parameters('location')]",
              "properties": {
                "administratorLogin": "[parameters('adminLogin')]",
                "administratorLoginPassword": "[parameters('adminPassword')]"
              }
            }
          ],
          "outputs": {
            "sqlFQDN": {
              "type": "string",
              "value": "[reference(variables('sqlServerName')).fullyQualifiedDomainName]"
            }
          }
        },
        "parameters": {
          "location": {
            "value": "[parameters('location')]"
          },
          "adminLogin": {
            "value": "ghuser"
          },
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

Sie haben Ihren Schlüsseltresor so konfiguriert, dass während der Bereitstellung einer verwalteten Anwendung darauf zugegriffen werden kann.

* Informationen zum Übergeben eines Werts aus einem Schlüsseltresor als Vorlagenparameter finden Sie unter [Verwenden von Azure Key Vault zum Übergeben eines sicheren Parameterwerts während der Bereitstellung](../templates/key-vault-parameter.md).
* Beispiele zu verwalteten Anwendungen finden Sie unter [Sample projects for Azure managed applications](sample-projects.md) (Beispielprojekte für verwaltete Azure-Anwendungen).
* Informationen zum Erstellen einer UI-Definitionsdatei für eine verwaltete Anwendung finden Sie unter [Erste Schritte mit „CreateUiDefinition“](create-uidefinition-overview.md).
