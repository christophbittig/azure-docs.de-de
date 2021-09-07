---
title: 'Hinzufügen von Bedingungen für die Azure-Rollenzuweisung mithilfe von Azure Resource Manager-Vorlagen (Vorschau): Azure ABAC'
description: Hier erfahren Sie, wie Sie Bedingungen der attributbasierten Zugriffssteuerung (Attribute-Based Access Control, ABAC) in Azure-Rollenzuweisungen unter Verwendung von Azure Resource Manager-Vorlagen und der rollenbasierten Zugriffssteuerung von Azure (Role-Based Access Control, Azure RBAC) hinzufügen.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.subservice: conditions
ms.topic: how-to
ms.workload: identity
ms.date: 06/29/2021
ms.author: rolyon
ms.openlocfilehash: 6e64afaab3b367ed807f77e5ebc0214904ed763f
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/30/2021
ms.locfileid: "113094822"
---
# <a name="add-azure-role-assignment-conditions-using-azure-resource-manager-templates-preview"></a>Hinzufügen von Bedingungen für die Azure-Rollenzuweisung mithilfe von Azure Resource Manager-Vorlagen (Vorschau)

> [!IMPORTANT]
> Azure RBAC und Azure-Rollenzuweisungsbedingungen befinden sich derzeit in der Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Eine [Azure-Rollenzuweisungsbedingung](conditions-overview.md) ist eine zusätzliche Überprüfung, die Sie Ihrer Rollenzuweisung optional hinzufügen können, um die Zugriffssteuerung präziser zu gestalten. So können Sie beispielsweise eine Bedingung hinzufügen, die festlegt, dass ein Objekt über ein bestimmtes Tag verfügen muss, damit das Objekt gelesen werden kann. In diesem Artikel wird beschrieben, wie Sie Bedingungen für Ihre Rollenzuweisungen mithilfe von Azure Resource Manager-Vorlagen hinzufügen.

## <a name="prerequisites"></a>Voraussetzungen

Informationen zu den Voraussetzungen für das Hinzufügen von Rollenzuweisungsbedingungen finden Sie unter [Voraussetzungen für Azure-Rollenzuweisungsbedingungen (Vorschau)](conditions-prerequisites.md).

## <a name="add-a-condition"></a>Bedingung hinzufügen

In der folgenden Vorlage wird gezeigt, wie Sie die Rolle [Storage-Blobdatenleser](built-in-roles.md#storage-blob-data-reader) mit einer Bedingung zuweisen. Die Bedingung überprüft, ob der Containername „blobs-example-container“ entspricht.

Um die Vorlage zu verwenden, müssen Sie Folgendes eingeben:

- Die ID eines Benutzers, einer Gruppe, einer verwalteten Identität oder einer Anwendung, dem bzw. der die Rolle zugewiesen wird
- Den Typ des Prinzipals, etwa `User`, `Group` oder `ServicePrincipal`. Weitere Informationen finden Sie unter [Neuer Dienstprinzipal](role-assignments-template.md#new-service-principal).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "Principal ID to assign the role to"
            }
        },
        "principalType": {
            "type": "string",
            "metadata": {
                "description": "Type of principal"
            }
        },
        "roleAssignmentGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "New GUID used to identify the role assignment"
            }
        }
    },
    "variables": {
        "StorageBlobDataReader": "[concat(subscription().Id, '/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1')]" // ID for Storage Blob Data Reader role, but can be any valid role ID
    },
    "resources": [
        {
            "name": "[parameters('roleAssignmentGuid')]",
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2020-04-01-preview", // API version to call the role assignment PUT.
            "properties": {
                "roleDefinitionId": "[variables('StorageBlobDataReader')]",
                "principalId": "[parameters('principalId')]",
                "principalType": "[parameters('principalType')]",
                "description": "Role assignment condition created with an ARM template",
                "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'))", // Role assignment condition
                "conditionVersion": "2.0"
            }
        }
    ]
}
```

Der Bereich der Rollenzuweisung wird von der Bereitstellungsebene bestimmt. Im Folgenden finden Sie Beispiele für die Befehle [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) und [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) zum Starten der Bereitstellung in einem Ressourcengruppenbereich.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName example-group -TemplateFile rbac-test.json -principalId $principalId -principalType "User"
```

```azurecli
az deployment group create --resource-group example-group --template-file rbac-test.json --parameters principalId=$principalId principalType="User"
```

## <a name="next-steps"></a>Nächste Schritte

- [Beispiele für Azure-Rollenzuweisungsbedingungen (Vorschau)](../storage/common/storage-auth-abac-examples.md)
- [Problembehandlung: Bedingungen für die Azure-Rollenzuweisung (Vorschau)](conditions-troubleshoot.md)
- [Zuweisen von Azure-Rollen mithilfe von Azure Resource Manager-Vorlagen](role-assignments-template.md)
