---
title: Verwalten von Ressourcengruppen – Azure PowerShell
description: Verwenden Sie Azure PowerShell, um Ihre Ressourcengruppen über Azure Resource Manager zu verwalten. Hier wird gezeigt, wie Sie Ressourcengruppen erstellen, auflisten und löschen.
author: mumian
ms.topic: conceptual
ms.date: 09/10/2021
ms.author: jgao
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c3bb028186155cc3af47f8efb293b7dbe61e13c9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124731039"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-powershell"></a>Verwalten von Azure Resource Manager-Gruppen mithilfe von Azure PowerShell

Erfahren Sie, wie Sie mit Azure PowerShell und [Azure Resource Manager](overview.md) Ihre Azure-Ressourcengruppen verwalten. Informationen zum Verwalten von Azure-Ressourcen finden Sie unter [Verwalten von Azure-Ressourcen mithilfe von Azure PowerShell](manage-resources-powershell.md).

Weitere Artikel zum Verwalten von Ressourcengruppen:

- [Verwalten von Azure-Ressourcengruppen mithilfe des Azure-Portals](manage-resources-portal.md)
- [Verwalten von Azure-Ressourcengruppen mithilfe der Azure-Befehlszeilenschnittstelle](manage-resources-cli.md)

## <a name="what-is-a-resource-group"></a>Was ist eine Ressourcengruppe?

Eine Ressourcengruppe ist ein Container, der verwandte Ressourcen für eine Azure-Lösung enthält. Die Ressourcengruppe kann alle Ressourcen für die Lösung oder nur die Ressourcen enthalten, die Sie als Gruppe verwalten möchten. Sie entscheiden in Abhängigkeit davon, was für Ihre Organisation am sinnvollsten ist, wie Sie den Ressourcengruppen die Ressourcen hinzufügen möchten. Im Allgemeinen fügen Sie einer Ressourcengruppe Ressourcen hinzu, die den gleichen Lebenszyklus haben, damit Sie diese einfacher als Gruppe bereitstellen, aktualisieren und löschen können.

In der Ressourcengruppe werden Metadaten zu den Ressourcen gespeichert. Wenn Sie einen Standort für die Ressourcengruppe angeben, legen Sie fest, wo die Metadaten gespeichert werden. Aus Compliance-Gründen müssen Sie unter Umständen sicherstellen, dass Ihre Daten in einer bestimmten Region gespeichert werden.

## <a name="create-resource-groups"></a>Erstellen von Ressourcengruppe

Erstellen Sie mit [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Ressourcengruppe.

```azurepowershell-interactive
New-AzResourceGroup -Name exampleGroup -Location westus
```

## <a name="list-resource-groups"></a>Ressourcengruppen auflisten

Verwenden Sie zum Auflisten der Ressourcengruppen in Ihrem Abonnement den Befehl [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup).

```azurepowershell-interactive
Get-AzResourceGroup
```

Geben Sie den Namen einer Ressourcengruppe an, um diese Ressourcengruppe abzurufen.

```azurepowershell-interactive
Get-AzResourceGroup -Name exampleGroup
```

## <a name="delete-resource-groups"></a>Löschen von Ressourcengruppen

Verwenden Sie [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup), um eine Ressourcengruppe zu löschen.

```azurepowershell-interactive
Remove-AzResourceGroup -Name exampleGroup
```

Weitere Informationen dazu, in welcher Reihenfolge Ressourcenlöschungen in Azure Resource Manager durchgeführt werden, finden Sie unter [Azure Resource Manager: Löschvorgang von Ressourcengruppen](delete-resource-group.md).

## <a name="deploy-resources"></a>Bereitstellen von Ressourcen

Sie können Azure-Ressourcen mithilfe von Azure PowerShell oder durch Bereitstellen einer ARM-Vorlage (Azure Resource Manager) oder einer Bicep-Datei bereitstellen.

Im folgenden Beispiel wird ein Speicherkonto erstellt. Der Name, den Sie für das Speicherkonto angeben, muss in Azure eindeutig sein.

```azurepowershell-interactive
New-AzStorageAccount -ResourceGroupName exampleGroup -Name examplestore -Location westus -SkuName "Standard_LRS"
```

Verwenden Sie [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment), um eine ARM-Vorlage oder Bicep-Datei bereitzustellen.

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName exampleGroup -TemplateFile storage.bicep
```

Weitere Informationen zum Bereitstellen einer ARM-Vorlage finden Sie unter [Bereitstellen von Ressourcen mit ARM-Vorlagen und Azure PowerShell](../templates/deploy-powershell.md).

Weitere Informationen zum Bereitstellen einer Bicep-Datei finden Sie unter [Bereitstellen von Ressourcen mit Bicep und Azure PowerShell](../bicep/deploy-powershell.md).

## <a name="lock-resource-groups"></a>Ressourcengruppen sperren

Eine Sperre verhindert, dass andere Benutzer*innen in Ihrer Organisation versehentlich wichtige Ressourcen löschen oder ändern. 

Verwenden Sie [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock), um zu verhindern, dass eine Ressourcengruppe und die zugehörigen Ressourcen gelöscht werden.

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleGroup
```

Rufen Sie mithilfe von [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock) die Sperren für eine Ressourcengruppe ab.

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleGroup
```

Weitere Informationen finden Sie unter [Sperren von Ressourcen mit dem Azure-Ressourcen-Manager](lock-resources.md).

## <a name="tag-resource-groups"></a>Hinzufügen von Tags zu Ressourcengruppen

Sie können Ressourcengruppen und Ressourcen Tags zuordnen, um sie logisch zu organisieren. Weitere Informationen finden Sie unter [Verwenden von Tags zum Organisieren von Azure-Ressourcen](tag-resources.md#powershell).

## <a name="export-resource-groups-to-templates"></a>Exportieren von Ressourcengruppen in Vorlagen

Zur Unterstützung beim Erstellen von ARM-Vorlagen können Sie eine Vorlage aus vorhandenen Ressourcen exportieren. Weitere Informationen finden Sie unter [Verwenden von Azure PowerShell zum Exportieren einer Vorlage](../templates/export-template-powershell.md). 

## <a name="manage-access-to-resource-groups"></a>Verwalten des Zugriffs auf Ressourcengruppen

Der Zugriff auf Ressourcen in Azure wird mithilfe der [rollenbasierten Zugriffssteuerung in Azure (Azure RBAC)](../../role-based-access-control/overview.md) verwaltet. Weitere Informationen finden Sie unter [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen mithilfe der Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Azure Resource Manager finden Sie unter [Übersicht über den Azure Resource Manager](overview.md).
- Informationen zur Resource Manager-Vorlagensyntax finden Sie unter [Verstehen der Struktur und Syntax von Azure Resource Manager-Vorlagen](../templates/syntax.md).
