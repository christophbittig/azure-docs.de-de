---
title: Erstellen eines Azure Video Analyzer-Kontos
description: In diesem Thema wird erläutert, wie Sie ein Konto für Azure Video Analyzer erstellen.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: ae759d67273461dff2c4fb8b37de222603bdcb7d
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563984"
---
# <a name="create-a-video-analyzer-account"></a>Erstellen eines Video Analyzer-Kontos

Um Azure Video Analyzer verwenden zu können, müssen Sie ein Video Analyzer-Konto erstellen. Das Konto muss einem Speicherkonto und mindestens einer [benutzerseitig zugewiesenen verwalteten Identität][docs-uami] (UAMI) zugeordnet werden. Die UAMI muss über die Berechtigungen der Rolle [Mitwirkender an Storage-Blobdaten][docs-storage-access] und der Rolle [Leser][docs-role-reader] für das Speicherkonto verfügen. Optional können Sie Ihrem Video Analyzer IoT Hub Konto eine Verbindung zuordnen. Dies ist erforderlich, wenn Sie das Video Analyzer-Edgemodul als [transparentes Gateway](./cloud/use-remote-device-adapter.md) verwenden. In diesem Fall müssen Sie eine UAMI hinzufügen, die über Berechtigungen für die Rolle [Mitwirkender](../../role-based-access-control/built-in-roles.md#contributor) verfügt. Sie können sowohl die gleiche UAMI für Speicherkonto und IoT Hub als auch separate UAMIs verwenden.

In diesem Artikel werden die Schritte zum Erstellen eines neuen Video Analyzer-Kontos beschrieben. Sie können das Azure-Portal oder eine [ARM-Vorlage (Azure Resource Manager)][docs-arm-template] verwenden. Wählen Sie die Registerkarte für die Methode aus, die Sie verwenden möchten.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="portal"></a>[Portal](#tab/portal/)

[!INCLUDE [the video analyzer account and storage account must be in the same subscription and region](./includes/note-account-storage-same-subscription.md)]

### <a name="create-a-video-analyzer-account-in-the-azure-portal"></a>Erstellen eines Video Analyzer-Kontos im Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Geben Sie in der Suchleiste im oberen Bereich **Video Analyzer** ein.
1. Klicken Sie unter *Dienste* auf *Video Analyzers*.
1. Klicken Sie auf **Erstellen**.
1. Geben Sie im Abschnitt zum **Erstellen eines Video Analyzer-Kontos** die erforderlichen Werte ein.

    | Name | BESCHREIBUNG |
    | ---|---|
    |**Abonnement**|Wenn Sie über mehrere Abonnements verfügen, wählen Sie eines aus der Liste der Azure-Abonnements aus, auf die Sie Zugriff haben.|
    |**Ressourcengruppe**|Wählen Sie eine vorhandene Ressource aus, oder erstellen Sie eine neue. Eine Ressourcengruppe ist eine Sammlung von Ressourcen mit gleichem Lebenszyklus, gleichen Berechtigungen und gleichen Richtlinien. [Hier](../../azure-resource-manager/management/overview.md#resource-groups)erhalten Sie weitere Informationen.|
    |**Name des Video Analyzer-Kontos**|Geben Sie den Namen des neuen Video Analyzer-Kontos ein. Der Name eines Video Analyzer-Kontos darf nur Kleinbuchstaben oder Ziffern ohne Leerzeichen enthalten und muss aus 3 bis 24 Zeichen bestehen.|
    |**Location**|Wählen Sie die geografische Region aus, in der die Video- und Metadatendatensätze für das Video Analyzer-Konto gespeichert werden sollen. Im Dropdownlistenfeld werden nur die verfügbaren Video Analyzer-Regionen angezeigt. |
    |**Speicherkonto**|Wählen Sie ein Speicherkonto aus, das als Blobspeicher für die Videoinhalte Ihres Video Analyzer-Konto dienen soll. Sie können ein vorhandenes Speicherkonto in derselben geografischen Region wie Ihr Video Analyzer-Konto auswählen oder ein neues Speicherkonto erstellen. Ein neues Speicherkonto wird in derselben Region erstellt. Für Namen von Speicherkonten gelten die gleichen Regeln wie für Namen von Video Analyzer-Konten.<br/>|
    |**Verwaltete Identität**|Wählen Sie eine benutzerseitig zugewiesene verwaltete Identität aus, die das neue Video Analyzer-Konto für den Zugriff auf das Speicherkonto verwendet. Sie können eine vorhandene benutzerseitig zugewiesene verwaltete Identität auswählen oder eine neue erstellen. Der benutzerseitig zugewiesenen verwalteten Identität werden die Rollen [Mitwirkender an Storage-Blobdaten][docs-storage-access] und [Leser][docs-role-reader] für das Speicherkonto zugewiesen.

1. Klicken Sie unten im Formular auf **Überprüfen + erstellen**.

### <a name="post-deployment-steps"></a>Schritte nach der Bereitstellung
Sie können einen vorhandenen IoT Hub an das Video Analyzer-Konto anfügen. Hierfür ist eine vorhandene UAMI erforderlich.

1. Klicken Sie auf **Zu Ressource wechseln**, nachdem die Bereitstellung der Ressource abgeschlossen ist.
1. Wählen Sie unter „Einstellungen“ **IoT Hub** aus, und klicken Sie dann auf **Anfügen**.  Geben Sie auf dem Flyoutblatt **IoT Hub anfügen** die erforderlichen Werte ein:
     - Abonnement: Wählen Sie den Namen des Azure-Abonnements aus, unter dem der IoT Hub erstellt wurde.
     - IoT Hub: Wählen Sie die den gewünschten IoT Hub aus.
     - Verwaltete Identität: Wählen Sie die vorhandene UAMI aus, die für den Zugriff auf den IoT Hub verwendet werden soll.
1. Klicken Sie auf **Speichern**, um IoT Hub mit Ihrem Video Analyzer-Konto zu verknüpfen.

## <a name="template"></a>[Vorlage](#tab/template/)

[!INCLUDE [the video analyzer account and storage account must be in the same subscription and region](./includes/note-account-storage-same-subscription.md)]

### <a name="create-a-video-analyzer-account-using-a-template"></a>Erstellen eines Video Analyzer-Kontos mithilfe einer Vorlage

Die folgenden Ressourcen sind in der Vorlage definiert:

- [**Microsoft.Media/videoAnalyzers**](/azure/templates/Microsoft.Media/videoAnalyzers): die Kontoressource für Video Analyzer.
- [**Microsoft.Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts): das Speicherkonto, das von Video Analyzer zum Speichern von Videos und Metadaten verwendet wird.
- [**Microsoft.ManagedIdentity/userAssignedIdentities**](/azure/templates/Microsoft.ManagedIdentity/userAssignedIdentities): die benutzerseitig zugewiesene verwaltete Identität, die Video Analyzer für den Zugriff auf den Speicher verwendet.
- [**Microsoft.Storage/storageAccounts/providers/roleAssignments**](/azure/templates/microsoft.authorization/roleassignments): die Rollenzuweisungen, die Video Analyzer den Zugriff auf das Speicherkonto ermöglichen.

<!-- TODO replace with a reference like this:
:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.compute/vm-simple-linux/azuredeploy.json":::
-->

```json

{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "namePrefix": {
            "metadata": {
                "description": "Used to qualify the names of all of the resources created in this template."
            },
            "defaultValue": "avasample",
            "type": "string",
            "minLength": 3,
            "maxLength": 13
        }
    },
    "variables": {
        "storageAccountName": "[concat(parameters('namePrefix'),uniqueString(resourceGroup().id))]",
        "accountName": "[concat(parameters('namePrefix'),uniqueString(resourceGroup().id))]",
        "managedIdentityName": "[concat(parameters('namePrefix'),'-',resourceGroup().name,'-storage-access-identity')]"
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-10-01",
            "name": "deploy-storage-and-identity",
            "properties": {
                "mode": "Incremental",
                "expressionEvaluationOptions": {
                    "scope": "Inner"
                },
                "parameters": {
                    "namePrefix": {
                        "value": "[parameters('namePrefix')]"
                    },
                    "managedIdentityName": {
                        "value": "[variables('managedIdentityName')]"
                    }
                },
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "namePrefix": {
                            "type": "string"
                        },
                        "managedIdentityName": {
                            "type": "string"
                        }
                    },
                    "variables": {
                        "storageAccountName": "[concat(parameters('namePrefix'),uniqueString(resourceGroup().id))]",
                        "managedIdentityName": "[parameters('managedIdentityName')]",
                        "storageBlobDataContributorAssignment": "[guid('Storage Blob Data Contributor',variables('managedIdentityName'))]",
                        "storageBlobDataContributorDefinitionId": "[concat(resourceGroup().id, '/providers/Microsoft.Authorization/roleDefinitions/', 'ba92f5b4-2d11-453d-a403-e96b0029c9fe')]",
                        "readerAssignment": "[guid('Reader',variables('managedIdentityName'))]",
                        "readerDefinitionId": "[concat(resourceGroup().id, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
                    },
                    "resources": [
                        {
                            "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
                            "name": "[variables('managedIdentityName')]",
                            "apiVersion": "2015-08-31-preview",
                            "location": "[resourceGroup().location]"
                        },
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "apiVersion": "2019-04-01",
                            "name": "[variables('storageAccountName')]",
                            "location": "[resourceGroup().location]",
                            "sku": {
                                "name": "Standard_LRS"
                            },
                            "kind": "StorageV2",
                            "properties": {
                                "accessTier": "Hot"
                            }
                        },
                        {
                            "name": "[concat(variables('storageAccountName'), '/Microsoft.Authorization/', variables('storageBlobDataContributorAssignment'))]",
                            "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
                            "apiVersion": "2021-04-01-preview",
                            "dependsOn": [
                                "[variables('managedIdentityName')]",
                                "[variables('storageAccountName')]"
                            ],
                            "properties": {
                                "roleDefinitionId": "[variables('storageBlobDataContributorDefinitionId')]",
                                "principalId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities',variables('managedIdentityName')), '2018-11-30').principalId]",
                                "principalType": "ServicePrincipal"
                            }
                        },
                        {
                            "name": "[concat(variables('storageAccountName'), '/Microsoft.Authorization/', variables('readerAssignment'))]",
                            "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
                            "apiVersion": "2021-04-01-preview",
                            "dependsOn": [
                                "[variables('managedIdentityName')]",
                                "[variables('storageAccountName')]"
                            ],
                            "properties": {
                                "roleDefinitionId": "[variables('readerDefinitionId')]",
                                "principalId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities',variables('managedIdentityName')), '2018-11-30').principalId]",
                                "principalType": "ServicePrincipal"
                            }
                        }
                    ],
                    "outputs": {}
                }
            }
        },
        {
            "type": "Microsoft.Media/videoAnalyzers",
            "comments": "The Azure Video Analyzer account",
            "apiVersion": "2021-05-01-preview",
            "name": "[variables('accountName')]",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "deploy-storage-and-identity"
            ],
            "properties": {
                "storageAccounts": [
                    {
                        "id": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
                        "identity": {
                            "userAssignedIdentity": "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities',variables('managedIdentityName'))]"
                        }
                    }
                ]
            },
            "identity": {
                "type": "UserAssigned",
                "userAssignedIdentities": {
                    "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities',variables('managedIdentityName'))]": {}
                }
            }
        }
    ],
    "outputs": { }
}
```

> [!NOTE]
> Die Vorlage verwendet eine geschachtelte Bereitstellung für die Rollenzuweisungen, um sicherzustellen, dass sie verfügbar sind, bevor die Video Analyzer-Kontoressource bereitgestellt wird.

### <a name="deploy-the-template"></a>Bereitstellen der Vorlage

1. Klicken Sie auf die Schaltfläche *In Azure bereitstellen*, um sich bei Azure anzumelden und eine Vorlage zu öffnen.

    [![In Azure bereitstellen](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

1. Wählen Sie die folgenden Werte aus, bzw. geben Sie sie ein. Verwenden Sie die Standardwerte, sofern diese verfügbar sind.

    - **Abonnement**: Wählen Sie ein Azure-Abonnement aus.
    - **Ressourcengruppe**: Wählen Sie in der Dropdownliste eine bereits vorhandene Ressourcengruppe aus, oder wählen Sie **Neu erstellen** aus, geben Sie einen eindeutigen Namen für die Ressourcengruppe ein, und klicken Sie anschließend auf **OK**.
    - **Standort**: Wählen Sie einen Standort aus.  Beispiel: **USA, Westen 2**.
    - **Namenspräfix**: Geben Sie eine Zeichenfolge an, die als Präfix für den Namen der Ressourcen verwendet wird (die Standardwerte werden empfohlen).

1. Klicken Sie auf **Überprüfen + erstellen**. Wählen Sie nach Abschluss der Überprüfung **Erstellen** aus, um die Vorlage zu erstellen und bereitzustellen.

In der Anleitung oben wird zum Bereitstellen der Vorlage das Azure-Portal verwendet. Neben dem Azure-Portal können Sie auch die Azure CLI, Azure PowerShell und die REST-API verwenden. Informationen zu anderen Bereitstellungsmethoden finden Sie unter [Bereitstellen von Vorlagen](../../azure-resource-manager/templates/deploy-cli.md).

### <a name="post-deployment-steps"></a>Schritte nach der Bereitstellung
Sie können einen vorhandenen IoT Hub an das Video Analyzer-Konto anfügen. Hierfür ist eine vorhandene UAMI erforderlich.

1. Klicken Sie auf **Zu Ressource wechseln**, nachdem die Bereitstellung der Ressource abgeschlossen ist.
1. Wählen Sie unter „Einstellungen“ **IoT Hub** aus, und klicken Sie dann auf **Anfügen**.  Geben Sie auf dem Flyoutblatt **IoT Hub anfügen** die erforderlichen Werte ein:
     - Abonnement: Wählen Sie den Namen des Azure-Abonnements aus, unter dem der IoT Hub erstellt wurde.
     - IoT Hub: Wählen Sie die den gewünschten IoT Hub aus.
     - Verwaltete Identität: Wählen Sie die vorhandene UAMI aus, die für den Zugriff auf den IoT Hub verwendet werden soll.
1. Klicken Sie auf **Speichern**, um IoT Hub mit Ihrem Video Analyzer-Konto zu verknüpfen.

### <a name="review-deployed-resources"></a>Überprüfen der bereitgestellten Ressourcen

Im Azure-Portal können Sie das Vorhandensein des Kontos und der anderen erstellten Ressourcen überprüfen. Wählen Sie nach Abschluss der Bereitstellung die Option **Zu Ressourcengruppe wechseln** aus, um das Konto und die anderen Ressourcen anzuzeigen.

### <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe, wenn Sie die Ressourcen nicht mehr benötigen. Dadurch werden das Konto und alle in der Ressourcengruppe enthaltenen Ressourcen gelöscht.

1. Wählen Sie die **Ressourcengruppe** aus.
1. Wählen Sie auf der Seite für die Ressourcengruppe die Option **Löschen** aus.
1. Geben Sie bei entsprechender Aufforderung den Namen der Ressourcengruppe ein, und klicken Sie dann auf **Löschen**.

---

### <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie [Video Analyzer auf einem IoT Edge-Gerät bereitstellen][docs-deploy-on-edge].
* Erfahren Sie, wie Sie [Videos direkt in der Cloud erfassen und aufzeichnen](cloud/get-started-livepipelines-portal.md).

<!-- links -->
[docs-uami]: ../../active-directory/managed-identities-azure-resources/overview.md
[docs-storage-access]: ../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor
[docs-role-reader]: ../../role-based-access-control/built-in-roles.md#reader
[docs-arm-template]: ../../azure-resource-manager/templates/overview.md
[docs-deploy-on-edge]: deploy-iot-edge-device.md
