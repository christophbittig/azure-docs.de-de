---
title: Exportieren einer Vorlage in der Azure-Befehlszeilenschnittstelle
description: Verwenden Sie die Azure-Befehlszeilenschnittstelle, um eine Azure Resource Manager-Vorlage aus Ressourcen in Ihrem Abonnement zu exportieren.
ms.topic: conceptual
ms.date: 09/03/2021
ms.openlocfilehash: b60c7ae3787b2b22b09eb844a9206b9530fb68df
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/04/2021
ms.locfileid: "123480874"
---
# <a name="use-azure-cli-to-export-a-template"></a>Verwenden der Azure-Befehlszeilenschnittstelle zum Exportieren einer Vorlage

[!INCLUDE [Export template intro](../../../includes/resource-manager-export-template-intro.md)]

In diesem Artikel wird das Exportieren von Vorlagen über die **Azure-Befehlszeilenschnittstelle** veranschaulicht. Informationen zu anderen Optionen finden Sie unter:

* [Export template with Azure portal](export-template-portal.md) (Exportieren einer Vorlage über das Azure-Portal)
* [Export template with Azure PowerShell](export-template-powershell.md) (Exportieren einer Vorlage über Azure PowerShell)
* [REST API export from resource group](/rest/api/resources/resourcegroups/exporttemplate) (REST-API-Export aus einer Ressourcengruppe) und [REST API export from deployment history](/rest/api/resources/deployments/export-template) (REST-API-Export aus dem Bereitstellungsverlauf).

[!INCLUDE [Export template choose option](../../../includes/resource-manager-export-template-choose-option.md)]

[!INCLUDE [Export template limitations](../../../includes/resource-manager-export-template-limitations.md)]

## <a name="export-template-from-a-resource-group"></a>Exportieren einer Vorlage aus einer Ressourcengruppe

Nach dem erfolgreichen Einrichten der Ressourcengruppe können Sie eine Azure Resource Manager-Vorlage für die Ressourcengruppe exportieren.

Verwenden Sie [az group export](/cli/azure/group#az_group_export), und geben Sie den Namen der Ressourcengruppe an, um alle Ressourcen in einer Ressourcengruppe zu exportieren.

```azurecli-interactive
az group export --name demoGroup
```

Das Skript zeigt die Vorlage in der Konsole an. Zum Speichern in einer Datei verwenden Sie Folgendes:

```azurecli-interactive
az group export --name demoGroup > exportedtemplate.json
```

Anstatt alle Ressourcen in der Ressourcengruppe zu exportieren, können Sie die zu exportierenden Ressourcen auswählen.

Übergeben Sie die Ressourcen-ID, um eine Ressource zu exportieren.

```azurecli-interactive
storageAccountID=$(az resource show --resource-group demoGroup --name demostg --resource-type Microsoft.Storage/storageAccounts --query id --output tsv)
az group export --resource-group demoGroup --resource-ids $storageAccountID
```

Übergeben Sie die durch Leerzeichen getrennte Ressourcen-IDs, um mehrere Ressourcen zu exportieren. Wenn Sie alle Ressourcen exportieren möchten, geben Sie dieses Argument nicht an, oder geben Sie „*“ an.

```azurecli-interactive
az group export --resource-group <resource-group-name> --resource-ids $storageAccountID1 $storageAccountID2
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

Wenn Sie beim Exportieren der Vorlage den `--skip-resource-name-params`-Parameter verwenden, sind in der Vorlage keine Parameter für Ressourcennamen enthalten. Stattdessen wird der Ressourcenname der Ressource direkt auf den aktuellen Wert festgelegt. Sie können den Namen während der Bereitstellung nicht anpassen.

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

Wenn Sie beim Exportieren der Vorlage den `--include-parameter-default-value`-Parameter verwenden, enthält der Vorlagenparameter einen Standardwert, der auf den aktuellen Wert festgelegt ist. Sie können entweder diesen Standardwert verwenden oder den Standardwert überschreiben, indem Sie einen anderen Wert übergeben.

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

Um eine Vorlage aus einer Ressourcengruppenbereitstellung zu erhalten, verwenden Sie den Befehl [az deployment group export](/cli/azure/deployment/group#az_deployment_group_export). Sie geben den Namen der abzurufenden Bereitstellung an. Hilfe zum Erhalt des Namens einer Bereitstellung finden Sie unter [Anzeigen des Bereitstellungsverlaufs mit Azure Resource Manager](deployment-history.md).

```azurecli-interactive
az deployment group export --resource-group demoGroup --name demoDeployment 
```

Die Vorlage wird in der Konsole angezeigt. Verwenden Sie zum Speichern der Datei Folgendes:

```azurecli-interactive
az deployment group export --resource-group demoGroup --name demoDeployment > demoDeployment.json
```

Um Vorlagen zu erhalten, die auf anderen Ebenen bereitgestellt werden, verwenden Sie Folgendes:

* [az deployment sub export](/cli/azure/deployment/sub#az_deployment_sub_export) für Bereitstellungen für Abonnements
* [az deployment mg export](/cli/azure/deployment/mg#az_deployment_mg_export) für Bereitstellungen in Verwaltungsgruppen
* [az deployment tenant export](/cli/azure/deployment/tenant#az_deployment_tenant_export) für Bereitstellungen für Mandanten


## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie Vorlagen über das [Azure-Portal](export-template-portal.md), über [Azure PowerShell](export-template-powershell.md) oder über die [REST-API](/rest/api/resources/resourcegroups/exporttemplate) exportieren.
- Informationen zur Resource Manager-Vorlagensyntax finden Sie unter [Verstehen der Struktur und Syntax von Azure Resource Manager-Vorlagen](./syntax.md).
- Informationen zum Entwickeln von Vorlagen finden Sie in den [Schritt-für-Schritt-Tutorials](../index.yml).