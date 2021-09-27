---
title: Exportieren einer Vorlage in Azure PowerShell
description: Verwenden Sie Azure PowerShell, um eine Azure Resource Manager-Vorlage aus Ressourcen in Ihrem Abonnement zu exportieren.
ms.topic: conceptual
ms.date: 09/03/2021
ms.openlocfilehash: 15a81b845811f870be783807cf680c724413bf6c
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/04/2021
ms.locfileid: "123479153"
---
# <a name="use-azure-powershell-to-export-a-template"></a>Verwenden von Azure PowerShell zum Exportieren einer Vorlage

[!INCLUDE [Export template intro](../../../includes/resource-manager-export-template-intro.md)]

In diesem Artikel wird das Exportieren von Vorlagen über **Azure PowerShell** veranschaulicht. Informationen zu anderen Optionen finden Sie unter:

* [Export template with Azure CLI](export-template-cli.md) (Exportieren einer Vorlage über die Azure-Befehlszeilenschnittstelle)
* [Export template with Azure portal](export-template-portal.md) (Exportieren einer Vorlage über das Azure-Portal)
* [REST API export from resource group](/rest/api/resources/resourcegroups/exporttemplate) (REST-API-Export aus einer Ressourcengruppe) und [REST API export from deployment history](/rest/api/resources/deployments/export-template) (REST-API-Export aus dem Bereitstellungsverlauf).

[!INCLUDE [Export template choose option](../../../includes/resource-manager-export-template-choose-option.md)]

[!INCLUDE [Export template limitations](../../../includes/resource-manager-export-template-limitations.md)]

## <a name="export-template-from-a-resource-group"></a>Exportieren einer Vorlage aus einer Ressourcengruppe

Nach dem Einrichten der Ressourcengruppe können Sie eine Azure Resource Manager-Vorlage für die Ressourcengruppe exportieren. 

Verwenden Sie das Cmdlet [Export-AzResourceGroup](/powershell/module/az.resources/Export-AzResourceGroup), und geben Sie den Namen der Ressourcengruppe an, um alle Ressourcen in einer Ressourcengruppe zu exportieren.

```azurepowershell-interactive
Export-AzResourceGroup -ResourceGroupName demoGroup
```

Die Vorlage wird als lokale Datei gespeichert.

Anstatt alle Ressourcen in der Ressourcengruppe zu exportieren, können Sie die zu exportierenden Ressourcen auswählen.

Übergeben Sie die Ressourcen-ID, um eine Ressource zu exportieren.

```azurepowershell-interactive
$resource = Get-AzResource `
  -ResourceGroupName <resource-group-name> `
  -ResourceName <resource-name> `
  -ResourceType <resource-type>
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource $resource.ResourceId
```

Übergeben Sie die Ressourcen-IDs in einem Array, um mehr als eine Ressource zu exportieren.

```azurepowershell-interactive
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource @($resource1.ResourceId, $resource2.ResourceId)
```

Wenn Sie die Vorlage exportieren, können Sie angeben, ob in der Vorlage Parameter verwendet werden sollen. Parameter für Ressourcennamen sind zwar standardmäßig enthalten, haben jedoch keinen Standardwert.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "type": "String"
  }
}
```

Wenn Sie beim Exportieren der Vorlage den `-SkipResourceNameParameterization`-Parameter verwenden, sind in der Vorlage keine Parameter für Ressourcennamen enthalten. Stattdessen wird der Ressourcenname der Ressource direkt auf den aktuellen Wert festgelegt. Sie können den Namen während der Bereitstellung nicht anpassen.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "demoHostPlan",
    ...
  }
]
```

Wenn Sie beim Exportieren der Vorlage den `-IncludeParameterDefaultValue`-Parameter verwenden, enthält der Vorlagenparameter einen Standardwert, der auf den aktuellen Wert festgelegt ist. Sie können entweder diesen Standardwert verwenden oder den Standardwert überschreiben, indem Sie einen anderen Wert übergeben.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": "demoHostPlan",
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": "webSite3bwt23ktvdo36",
    "type": "String"
  }
}
```

## <a name="save-template-from-deployment-history"></a>Speichern einer Vorlage aus dem Bereitstellungsverlauf

Sie können eine Vorlage aus einer Bereitstellung im Bereitstellungsverlauf speichern. Die Vorlage, die Sie erhalten, ist genau dieselbe, die für die Bereitstellung verwendet wurde. 

Verwenden Sie das Cmdlet [Save-AzResourceGroupDeploymentTemplate](/powershell/module/az.resources/save-azresourcegroupdeploymenttemplate), um eine Vorlage aus einer Ressourcengruppenbereitstellung zu erhalten. Sie geben den Namen der abzurufenden Bereitstellung an. Hilfe zum Erhalt des Namens einer Bereitstellung finden Sie unter [Anzeigen des Bereitstellungsverlaufs mit Azure Resource Manager](deployment-history.md).

```azurepowershell-interactive
Save-AzResourceGroupDeploymentTemplate -ResourceGroupName demoGroup -DeploymentName demoDeployment
```

Die Vorlage wird als lokale Datei mit dem Namen der Bereitstellung gespeichert.

Um Vorlagen zu erhalten, die auf anderen Ebenen bereitgestellt werden, verwenden Sie Folgendes:

* [Save-AzDeploymentTemplate](/powershell/module/az.resources/save-azdeploymenttemplate) für Bereitstellungen für Abonnements
* [Save-AzManagementGroupDeploymentTemplate](/powershell/module/az.resources/save-azmanagementgroupdeploymenttemplate) für Bereitstellungen in Verwaltungsgruppen
* [Save-AzTenantDeploymentTemplate](/powershell/module/az.resources/save-aztenantdeploymenttemplate) für Bereitstellungen für Mandanten

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie Vorlagen über die [Azure-Befehlszeilenschnittstelle](export-template-cli.md), das [Azure-Portal](export-template-portal.md) oder die [REST-API](/rest/api/resources/resourcegroups/exporttemplate) exportieren.
- Informationen zur Resource Manager-Vorlagensyntax finden Sie unter [Verstehen der Struktur und Syntax von Azure Resource Manager-Vorlagen](./syntax.md).
- Informationen zum Entwickeln von Vorlagen finden Sie in den [Schritt-für-Schritt-Tutorials](../index.yml).