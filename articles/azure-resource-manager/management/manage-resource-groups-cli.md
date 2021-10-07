---
title: Verwalten von Ressourcengruppen – Azure CLI
description: Verwenden Sie die Azure CLI, um Ihre Ressourcengruppen über Azure Resource Manager zu verwalten. Hier wird gezeigt, wie Sie Ressourcengruppen erstellen, auflisten und löschen.
author: mumian
ms.topic: conceptual
ms.date: 09/10/2021
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2088f1b4f201f149fc4d51bf608cb5868802d4d7
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124731129"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>Verwalten von Azure Resource Manager-Gruppen mithilfe der Azure CLI

Erfahren Sie, wie Sie mit der Azure CLI und [Azure Resource Manager](overview.md) Ihre Azure-Ressourcengruppen verwalten. Informationen zum Verwalten von Azure-Ressourcen finden Sie unter [Verwalten von Azure-Ressourcen mithilfe der Azure CLI](manage-resources-cli.md).

Weitere Artikel zum Verwalten von Ressourcengruppen:

- [Verwalten von Azure-Ressourcengruppen mithilfe des Azure-Portals](manage-resources-portal.md)
- [Verwalten von Azure-Ressourcengruppen mithilfe von Azure PowerShell](manage-resources-powershell.md)

## <a name="what-is-a-resource-group"></a>Was ist eine Ressourcengruppe?

Eine Ressourcengruppe ist ein Container, der verwandte Ressourcen für eine Azure-Lösung enthält. Die Ressourcengruppe kann alle Ressourcen für die Lösung oder nur die Ressourcen enthalten, die Sie als Gruppe verwalten möchten. Sie entscheiden in Abhängigkeit davon, was für Ihre Organisation am sinnvollsten ist, wie Sie den Ressourcengruppen die Ressourcen hinzufügen möchten. Im Allgemeinen fügen Sie einer Ressourcengruppe Ressourcen hinzu, die den gleichen Lebenszyklus haben, damit Sie diese einfacher als Gruppe bereitstellen, aktualisieren und löschen können.

In der Ressourcengruppe werden Metadaten zu den Ressourcen gespeichert. Wenn Sie einen Standort für die Ressourcengruppe angeben, legen Sie fest, wo die Metadaten gespeichert werden. Aus Compliance-Gründen müssen Sie unter Umständen sicherstellen, dass Ihre Daten in einer bestimmten Region gespeichert werden.

## <a name="create-resource-groups"></a>Erstellen von Ressourcengruppe

Verwenden Sie zum Erstellen einer Ressourcengruppe [az group create](/cli/azure/group#az_group_create).

```azurecli-interactive
az group create --name demoResourceGroup --location westus
```

## <a name="list-resource-groups"></a>Ressourcengruppen auflisten

Verwenden Sie [az group list](/cli/azure/group#az_group_list), um die Ressourcengruppen in Ihrem Abonnement aufzulisten.

```azurecli-interactive
az group list
```

Verwenden Sie [az group show](/cli/azure/group#az_group_show), um eine Ressourcengruppe abzurufen.

```azurecli-interactive
az group show --name exampleGroup
```

## <a name="delete-resource-groups"></a>Löschen von Ressourcengruppen

Verwenden Sie [az group delete](/cli/azure/group#az_group_delete), um eine Ressourcengruppe zu löschen.

```azurecli-interactive
az group delete --name exampleGroup
```

Weitere Informationen dazu, in welcher Reihenfolge Ressourcenlöschungen in Azure Resource Manager durchgeführt werden, finden Sie unter [Azure Resource Manager: Löschvorgang von Ressourcengruppen](delete-resource-group.md).

## <a name="deploy-resources"></a>Bereitstellen von Ressourcen

Sie können mithilfe der Azure CLI oder durch das Bereitstellen einer ARM-Vorlage (Azure Resource Manager) oder Bicep-Datei Azure-Ressourcen bereitstellen.

Im folgenden Beispiel wird ein Speicherkonto erstellt. Der Name, den Sie für das Speicherkonto angeben, muss in Azure eindeutig sein.

```azurecli-interactive
az storage account create --resource-group exampleGroup --name examplestore --location westus --sku Standard_LRS --kind StorageV2
```

Verwenden Sie [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create), um eine ARM-Vorlage oder Bicep-Datei bereitzustellen.

```azurecli-interactive
az deployment group create --resource-group exampleGroup --template-file storage.bicep
```

Weitere Informationen zum Bereitstellen von ARM-Vorlagen finden Sie unter [Bereitstellen von Ressourcen mit ARM-Vorlagen und der Azure CLI](../templates/deploy-cli.md).

Weitere Informationen zum Bereitstellen einer Bicep-Datei finden Sie unter [Bereitstellen von Ressourcen mit Bicep und Azure CLI](../bicep/deploy-cli.md).

## <a name="lock-resource-groups"></a>Ressourcengruppen sperren

Eine Sperre verhindert, dass andere Benutzer in Ihrer Organisation versehentlich wichtige Ressourcen löschen oder ändern.

Verwenden Sie [az lock create](/cli/azure/lock#az_lock_create), um zu verhindern, dass eine Ressourcengruppe und die zugehörigen Ressourcen gelöscht werden.

```azurecli-interactive
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleGroup
```

Rufen Sie die Sperren für eine Ressourcengruppe mit [az lock list](/cli/azure/lock#az_lock_list) ab.

```azurecli-interactive
az lock list --resource-group exampleGroup
```

Verwenden Sie [az lock delete](/cli/azure/lock#az_lock_delete), um eine Sperre zu löschen.

```azurecli-interactive
az lock delete --name exampleLock --resource-group exampleGroup
```

Weitere Informationen finden Sie unter [Sperren von Ressourcen mit dem Azure-Ressourcen-Manager](lock-resources.md).

## <a name="tag-resource-groups"></a>Hinzufügen von Tags zu Ressourcengruppen

Sie können Ressourcengruppen und Ressourcen Tags zuordnen, um sie logisch zu organisieren. Weitere Informationen finden Sie unter [Verwenden von Tags zum Organisieren von Azure-Ressourcen](tag-resources.md#azure-cli).

## <a name="export-resource-groups-to-templates"></a>Exportieren von Ressourcengruppen in Vorlagen

Zur Unterstützung beim Erstellen von ARM-Vorlagen können Sie eine Vorlage aus vorhandenen Ressourcen exportieren. Weitere Informationen finden Sie unter [Verwenden der Azure-Befehlszeilenschnittstelle zum Exportieren einer Vorlage](../templates/export-template-cli.md). 

## <a name="manage-access-to-resource-groups"></a>Verwalten des Zugriffs auf Ressourcengruppen

Verwenden Sie die [rollenbasierte Zugriffssteuerung in Azure (Azure RBAC)](../../role-based-access-control/overview.md), um den Zugriff auf eine Ressourcengruppe zu verwalten. Weitere Informationen finden Sie unter [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen mithilfe der Azure CLI](../../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Azure Resource Manager finden Sie unter [Übersicht über den Azure Resource Manager](overview.md).
- Informationen zur Resource Manager-Vorlagensyntax finden Sie unter [Verstehen der Struktur und Syntax von Azure Resource Manager-Vorlagen](../templates/syntax.md).