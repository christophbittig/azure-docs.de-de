---
title: Azure Automanage-Konto
description: Erfahren Sie, wie ein Automanage-Konto funktioniert, und wie Sie eins erstellen.
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: d418cd8c6ad764ee68cf8defd697ed168ee4136c
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129455093"
---
# <a name="automanage-accounts"></a>Automanage Konten

Das Automanage-Konto ist die Identität, die vom Automanage-Dienst zum Ausführen seiner automatisierten Vorgänge verwendet wird.

Wenn Sie in der Azure-Portalerfahrung die automatische Verwaltung auf Ihren VMs aktivieren, gibt es eine Dropdownliste „Erweitert“ auf dem Blatt **Bewährte Methode zum Aktivieren einer Azure-VM**, mit der Sie das Konto für die automatische Verwaltung zuweisen oder manuell erstellen können.

Dem Automanage-Konto wird sowohl die Rolle **Mitwirkender** als auch **Mitwirkender bei Ressourcenrichtlinien** zu den Abonnements gewährt, die die Computer enthalten, die Sie in Automanage integrieren. Sie können das gleiche Automanage-Konto über mehrere Abonnements hinweg auf Computern verwenden. Dadurch werden diesem Automanage-Konto die Rollen **Mitwirkender** und **Mitwirkender bei Ressourcenrichtlinien** für alle Abonnements erteilt.

Wenn Ihr virtueller Computer mit einem Log Analytics-Arbeitsbereich in einem anderen Abonnement verbunden ist, wird dem Automanage-Konto sowohl die Rolle **Mitwirkender** als auch **Mitwirkender bei Ressourcenrichtlinien** in diesem anderen Abonnement gewährt.

Wenn Sie Automanage mit einem neuen Automanage-Konto aktivieren, benötigen Sie die folgenden Berechtigungen in Ihrem Abonnement: Rolle **Besitzer** oder **Mitwirkender** sowie Rollen vom Typ **Benutzerzugriffsadministrator**

Wenn Sie Automanage mit einem vorhandenen Automanage-Konto aktivieren, müssen Sie über die Rolle **Mitwirkender** für die Ressourcengruppe verfügen, die Ihre virtuellen Computer enthält.

> [!NOTE]
> Wenn Sie bewährte Methoden für Automanage deaktivieren, bleiben die Berechtigungen des Automanage-Kontos für alle zugeordneten Abonnements erhalten. Entfernen Sie die Berechtigungen manuell, indem Sie die IAM-Seite des Abonnements aufrufen oder das Automanage-Konto löschen. Das Automanage-Konto kann nicht gelöscht werden, wenn es noch Computer verwaltet.

## <a name="create-an-automanage-account"></a>Erstellen eines Automanage-Kontos
Sie können ein Automanage-Konto über das Portal oder mithilfe einer ARM-Vorlage erstellen.

### <a name="portal"></a>Portal
1. Navigieren Sie zum Blatt **Automanage** im Portal.
1. Klicken Sie auf **Für vorhandenen Computer aktivieren**.
1. Klicken Sie unter **Erweitert** auf „Neues Konto erstellen“.
1. Füllen Sie die erforderlichen Felder aus, und klicken Sie auf **Erstellen**.

### <a name="arm-template"></a>ARM-Vorlage
Zum Erstellen eines Automanage-Kontos mithilfe einer ARM-Vorlage sind zwei Schritte erforderlich:
1. Erstellen des Automanage-Kontos
1. Erteilen ausreichender Berechtigungen für das Konto, damit es Vorgänge für Sie ausführen darf
    1. Sie benötigen die Objekt-ID des Kontos, das Sie für diesen Schritt erstellt haben.
        1. Schritte zum Ermitteln von Details zum Dienstprinzipal Ihres Kontos (einschließlich der Objekt-ID) finden Sie [hier](../active-directory/managed-identities-azure-resources/how-to-view-managed-identity-service-principal-portal.md#view-the-service-principal).
    1. Nachdem Sie Ihren Dienstprinzipal gefunden haben, kopieren Sie die **Objekt-ID**. Speichern Sie diese, da Sie sie weiter unten noch zum Delegieren von Berechtigungen benötigen.

#### <a name="1-create-automanage-account-does-not-grant-permissions-to-it"></a>1. Erstellen eines Automanage-Kontos (gewährt diesem keine Berechtigungen)
Speichern Sie zum Erstellen eines Automanage-Kontos die folgende ARM-Vorlage unter `azuredeploy.json`, und führen Sie den folgenden Azure CLI-Befehl aus. Sobald Sie fertig sind, fahren Sie mit der zweiten Vorlage unten fort, um ausreichende Berechtigungen an das Konto zu delegieren.

```azurecli-interactive
az deployment group create --resource-group <resource group name> --template-file azuredeploy.json
```

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "automanageAccountName": {
            "type": "String"
        },
        "location": {
            "type": "String"
        }
    },
    "resources": [
        {
            "apiVersion": "2020-06-30-preview",
            "type": "Microsoft.Automanage/accounts",
            "name": "[parameters('automanageAccountName')]",
            "location": "[parameters('location')]",
            "identity": {
                "type": "SystemAssigned"
            }
        }
    ]
}
```
#### <a name="2-grant-permissions-to-the-automanage-account"></a>2. Erteilen von Berechtigungen für das Automanage-Konto
Um dem Automanage-Konto ausreichende Berechtigungen zu gewähren, müssen Sie folgende Schritte ausführen:
1. Speichern Sie die folgende ARM-Vorlage unter `azuredeploy2.json`, und führen Sie den folgenden Azure CLI Befehl aus.
1. Wenn Sie dazu aufgefordert werden, geben Sie die Objekt-ID des Automanage-Kontos ein, das Sie erstellt und gespeichert haben.

```azurecli-interactive
az deployment sub create --location <location> --template-file azuredeploy2.json
```

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "dateTime": {
            "type": "string",
            "defaultValue": "[utcNow()]"
        }
    },
    "variables": {
        "contributorRoleDefinitionID": "/providers/Microsoft.Authorization/roledefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
        "resourcePolicyContributorRoleDefinitionID": "/providers/Microsoft.Authorization/roledefinitions/36243c78-bf99-498c-9df9-86d9f8d28608"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2020-04-01-preview",
            "name": "[guid(concat(parameters('dateTime'), variables('contributorRoleDefinitionID')))]",
            "properties": {
                "roleDefinitionId": "[variables('contributorRoleDefinitionID')]",
                "principalId": "[parameters('principalId')]"
            }
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2020-04-01-preview",
            "name": "[guid(concat(parameters('dateTime'), variables('resourcePolicyContributorRoleDefinitionID')))]",
            "properties": {
                "roleDefinitionId": "[variables('resourcePolicyContributorRoleDefinitionID')]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

## <a name="next-steps"></a>Nächste Schritte
* Informationen zu Automanage-Diensten für [Linux](./automanage-linux.md) und [Windows](./automanage-windows-server.md)