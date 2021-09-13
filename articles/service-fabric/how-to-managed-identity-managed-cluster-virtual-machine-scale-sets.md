---
title: Hinzufügen einer verwalteten Identität zu einem verwalteten Service Fabric-Clusterknotentyp
description: In diesem Artikel wird gezeigt, wie Sie einem verwalteten Service Fabric-Clusterknotentyp eine verwaltete Identität hinzufügen.
ms.topic: how-to
ms.date: 5/10/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6cf2d65fe90656fe3025e438a57ea60fe17abd0d
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/22/2021
ms.locfileid: "112453092"
---
# <a name="add-a-managed-identity-to-a-service-fabric-managed-cluster-node-type"></a>Hinzufügen einer verwalteten Identität zu einem verwalteten Service Fabric-Clusterknotentyp

Jeder Knotentyp in einem verwalteten Service Fabric-Cluster wird von einer VM-Skalierungsgruppe unterstützt. Damit verwaltete Identitäten mit einem verwalteten Clusterknotentyp verwendet werden können, wurde den Knotentypdefinitionen die `vmManagedIdentity`-Eigenschaft hinzugefügt. Diese enthält eine Liste der Identitäten, die verwendet werden können (`userAssignedIdentities`). Die Funktionalität ähnelt der, mit der verwaltete Identitäten in nicht verwalteten Clustern verwendet werden und die Sie z. B. bei der Verwendung einer verwalteten Identität mit der [Azure Key Vault-Erweiterung für VM-Skalierungsgruppen](../virtual-machines/extensions/key-vault-windows.md) finden.

Ein Beispiel für eine Bereitstellung eines verwalteten Service Fabric-Clusters, der eine verwaltete Identität für einen Knotentyp nutzt, finden Sie in [diesen Vorlagen](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-MI). Das Beispiel umfasst zwei Vorlagen:

1. **Verwaltete Identität und Rollenzuweisung:** Vorlage zum Erstellen der verwalteten Identität und der Rollenzuweisung, um Service Fabric RP die Zuweisung der Identität zur VM-Skalierungsgruppe des verwalteten Clusters zu ermöglichen. Diese sollte nur einmal bereitgestellt werden, bevor die verwaltete Identität für die Knotentypressource verwendet wird.

2. **Verwalteter Cluster und Knotentyp:** Vorlage für den verwalteten Service Fabric-Cluster und die Knotentypenressourcen, die die zuvor erstellte verwaltete Identität nutzen

> [!NOTE]
> Für dieses Feature werden derzeit nur vom Benutzer zugewiesene Identitäten unterstützt.

## <a name="prerequisites"></a>Voraussetzungen

Vorbereitungen

* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* Wenn Sie PowerShell verwenden möchten, [installieren](/cli/azure/install-azure-cli) Sie die Azure-Befehlszeilenschnittstelle, um CLI-Verweisbefehle auszuführen.

## <a name="1-create-identity-and-role-assignment"></a>1. Erstellen einer Identität und Rollenzuweisung

### <a name="create-a-user-assigned-managed-identity"></a>Erstellen einer benutzerseitig zugewiesenen verwalteten Identität

Eine vom Benutzer zugewiesene verwaltete Identität kann im Abschnitt „resources“ einer Azure Resource Manager-Vorlage (ARM) für die Erstellung bei der Bereitstellung definiert werden:

```JSON
{ 
    "type": "Microsoft.ManagedIdentity/userAssignedIdentities", 
    "name": "[parameters('userAssignedIdentityName')]", 
    "apiVersion": "2018-11-30", 
    "location": "[resourceGroup().location]"  
},
```

Sie kann auch mit PowerShell erstellt werden:

```powershell
 New-AzResourceGroup -Name <managedIdentityRGName> -Location <location>
New-AzUserAssignedIdentity -ResourceGroupName <managedIdentityRGName> -Name <userAssignedIdentityName>
```

### <a name="add-a-role-assignment-with-service-fabric-resource-provider"></a>Hinzufügen einer Rollenzuweisung mit dem Service Fabric-Ressourcenanbieter

Fügen Sie der verwalteten Identität mit der Service Fabric-Ressourcenanbieteranwendung eine Rollenzuweisung hinzu. Diese Zuweisung ermöglicht dem Service Fabric-Ressourcenanbieter das Zuweisen der im vorherigen Schritt erstellten Identität zur VM-Skalierungsgruppe des verwalteten Clusters. Dies ist eine einmalige Aktion.

Abrufen des Dienstprinzipals für eine Service Fabric-Ressourcenanbieteranwendung:

```powershell
Login-AzAccount
Select-AzSubscription -SubscriptionId <SubId>
Get-AzADServicePrincipal -DisplayName "Azure Service Fabric Resource Provider"
```

> [!NOTE]
> Stellen Sie sicher, dass Sie sich im richtigen Abonnement befinden. Die Prinzipal-ID ändert sich, wenn sich das Abonnement in einem anderen Mandanten befindet.

```powershell
ServicePrincipalNames : {74cb6831-0dbb-4be1-8206-fd4df301cdc2}
ApplicationId         : 74cb6831-0dbb-4be1-8206-fd4df301cdc2
ObjectType            : ServicePrincipal
DisplayName           : Azure Service Fabric Resource Provider
Id                    : 00000000-0000-0000-0000-000000000000
```

Verwenden Sie die **ID** der vorherigen Ausgabe als **principalId** und ggf. die Rollendefinitions-ID unten als **roleDefinitionId** für die Vorlage oder den PowerShell-Befehl:

|Name der Rollendefinition|Rollendefinitions-ID|
|----|-------------------------------------|
|Operator für verwaltete Identität|f1a07417-d97a-45cb-824c-7a7467783830|


Diese Rollenzuweisung kann im Abschnitt „resources“ der Vorlage mithilfe der Prinzipal-ID und der Rollendefinitions-ID definiert werden:

```JSON
{
    "type": "Microsoft.Authorization/roleAssignments", 
    "apiVersion": "2020-04-01-preview",
    "name": "[parameters('vmIdentityRoleNameGuid')]",
    "scope": "[concat('Microsoft.ManagedIdentity/userAssignedIdentities', '/', parameters('userAssignedIdentityName'))]",
    "dependsOn": [ 
        "[concat('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]"
    ], 
    "properties": {
        "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'f1a07417-d97a-45cb-824c-7a7467783830')]",
        "principalId": "00000000-0000-0000-0000-000000000000" 
    } 
}, 
```
> [!NOTE]
> vmIdentityRoleNameGuid muss eine gültige GUID sein. Wenn Sie die gleiche Vorlage einschließlich dieser Rollenzuweisung erneut bereitstellen, stellen Sie sicher, dass die GUID mit der ursprünglich verwendeten identisch ist, oder entfernen Sie diese Ressource, da sie nur einmal erstellt werden muss.

Sie kann auch mithilfe der Prinzipal-ID und des Rollendefinitionsnamens über PowerShell erstellt werden:

```powershell
New-AzRoleAssignment -PrincipalId 00000000-0000-0000-0000-000000000000 -RoleDefinitionName "Managed Identity Operator" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<userAssignedIdentityName>"
```

### <a name="deploy-managed-identity-and-role-assignment"></a>Bereitstellen der verwalteten Identität und der Rollenzuweisung
Führen Sie das Cmdlet „New-AzResourceGroupDeployment“ aus, um die verwaltete Identität zu erstellen und die Rollenzuweisung hinzuzufügen:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <managedIdentityRGName> -TemplateFile ".\MangedIdentityAndSfrpRoleAssignment.json" -TemplateParameterFile ".\MangedIdentityAndSfrpRoleAssignment.Parameters.json" -Verbose
```

## <a name="2-assign-identity-to-the-node-type-resource"></a>2. Zuweisen der Identität zur Knotentypressource

### <a name="add-managed-identity-properties-to-node-type-definition"></a>Hinzufügen von Eigenschaften der verwalteten Identität zur Knotentypdefinition

Fügen Sie abschließend die Eigenschaften `vmManagedIdentity` und `userAssignedIdentities` zur Knotentypdefinition des verwalteten Clusters mit der vollständigen Ressourcen-ID der Identität hinzu, die im ersten Schritt erstellt wurde. Stellen Sie sicher, dass Sie als `apiVersion` **2021-05-01** oder höher verwenden.

```json

 {
    "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
    "apiVersion": "2021-05-01",
    ...
    "properties": {
        "isPrimary" : true,
        "vmInstanceCount": 5,
        "dataDiskSizeGB": 100,
        "vmSize": "Standard_D2_v2",
        "vmImagePublisher" : "MicrosoftWindowsServer",
        "vmImageOffer" : "WindowsServer",
        "vmImageSku" : "2019-Datacenter",
        "vmImageVersion" : "latest",
        "vmManagedIdentity": {
            "userAssignedIdentities": [
                "[parameters('userAssignedIdentityResourceId')]"
            ]
        }
    }
}
```

### <a name="deploy-the-node-type-resource-assigning-the-identity"></a>Bereitstellen der Knotentypressource, die die Identität zu weist

Führen Sie das Cmdlet „New-AzResourceGroupDeployment“ aus, um die Vorlage für den verwalteten Service Fabric-Cluster bereitzustellen, mit der die verwaltete Identität der Knotentypressource zugewiesen wird.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <sfmcRGName> -TemplateFile ".\SfmcVmMangedIdentity.json" -TemplateParameterFile ".\SfmcVmMangedIdentity.Parameters.json" -Verbose
```

Nach der Bereitstellung wurde die erstellte verwaltete Identität der VM-Skalierungsgruppe des angegebenen Knotentyps hinzugefügt und kann erwartungsgemäß wie in einem nicht verwalteten Cluster verwendet werden.

## <a name="troubleshooting"></a>Problembehandlung

Wenn eine Rollenzuweisung nicht ordnungsgemäß ausgeführt werden kann, tritt bei der Bereitstellung der folgende Fehler auf:

:::image type="content" source="media/how-to-managed-identity-managed-cluster-vmss/role-assignment-error.png" alt-text="Bereitstellungsfehler im Azure-Portal mit dem Client und der Objekt-/Anwendungs-ID eines SFRP, der nicht über die Berechtigung zum Ausführen der Identitätsverwaltungsaktivität verfügt":::

Stellen Sie in diesem Fall sicher, dass die Rollenzuweisung mit der Rolle „Operator für verwaltete Identität“ erfolgreich erstellt wurde. Die Rollenzuweisung finden Sie im Azure-Portal in der Zugriffssteuerung für die Ressource der verwalteten Identität, wie nachfolgend gezeigt:

:::image type="content" source="media/how-to-managed-identity-managed-cluster-vmss/role-assignment-portal.png" alt-text="Rollenzuweisungseigenschaften des Service Fabric-Ressourcenanbieters für die benutzerseitig zugewiesene verwaltete Identität im Azure-Portal":::

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Bereitstellen einer App in einem verwalteten Service Fabric-Cluster](./tutorial-managed-cluster-deploy-app.md)
