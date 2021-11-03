---
title: Bereitstellen von Azure Monitor für SAP-Lösungen mit Azure PowerShell
description: Bereitstellen von Azure Monitor für SAP-Lösungen mit Azure PowerShell
author: sameeksha91
ms.author: sakhare
ms.topic: quickstart
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.date: 07/08/2021
ms.devlang: azurepowershell
ms.custom: devx-track-azurepowershell, mode-api
ms.openlocfilehash: e86f29291e52909eb1a531079a008c6ebefc47f4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131008406"
---
# <a name="quickstart-deploy-azure-monitor-for-sap-solutions-with-azure-powershell"></a>Schnellstart: Bereitstellen von Azure Monitor für SAP-Lösungen mit Azure PowerShell

In diesem Artikel wird erläutert, wie Sie Ressourcen für Azure Monitor für SAP-Lösungen mithilfe des PowerShell-Moduls [Az.HanaOnAzure](/powershell/module/az.hanaonazure/#sap-hana-on-azure) erstellen.

> [!CAUTION]
> Azure Monitor für SAP-Lösungen befindet sich derzeit in der Public Preview. Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt. Für Produktionsworkloads wird sie nicht empfohlen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="requirements"></a>Requirements (Anforderungen)

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

Wenn Sie die PowerShell lokal verwenden möchten, müssen Sie für diesen Artikel das Azure-PowerShell Modul installieren. Außerdem müssen Sie eine Verbindung mit Ihrem Azure-Konto unter Verwendung von [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) Cmdlet herstellen. Weitere Informationen zum Installieren des Az PowerShell-Moduls finden Sie unter [Installieren von Azure PowerShell](/powershell/azure/install-az-ps). Alternativ können Sie auch die Cloud Shell verwenden. Weitere Informationen zu Cloud Shell finden Sie in der [Übersicht über Azure Cloud Shell](../../../cloud-shell/overview.md).

> [!IMPORTANT]
> Solange nur eine Vorschauversion des PowerShell-Moduls **Az.HanaOnAzure** verfügbar ist, müssen Sie es separat mithilfe des Cmdlets `Install-Module` installieren. Sobald dieses PowerShell-Modul allgemein verfügbar ist, wird es in die zukünftigen Releases des Az PowerShell-Moduls integriert und in Azure Cloud Shell nativ zur Verfügung gestellt.

```azurepowershell-interactive
Install-Module -Name Az.HanaOnAzure
```

Wenn Sie über mehrere Azure-Abonnements verfügen, müssen Sie das entsprechende Abonnement auswählen, in dem die Ressourcen fakturiert werden sollen. Wählen Sie mit dem Cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext) ein bestimmtes Abonnement aus.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) Cmdlet eine [Azure-Ressourcengruppe](../../../azure-resource-manager/management/overview.md). Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen als Gruppe bereitgestellt und verwaltet werden.

Im folgenden Beispiel wird eine Ressourcengruppe mit dem angegebenen Namen am angegebenen Standort erstellt.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location westus2
```

## <a name="sap-monitor"></a>SAP-Monitor

Verwenden Sie das [New-AzSapMonitor](/powershell/module/az.hanaonazure/new-azsapmonitor) Cmdlet, um einen SAP-Monitor zu erstellen. Im folgenden Beispiel wird ein SAP-Monitor für das angegebene Abonnement, die Ressourcengruppe und den Ressourcennamen erstellt.

```azurepowershell-interactive
$Workspace = New-AzOperationalInsightsWorkspace -ResourceGroupName myResourceGroup -Name sapmonitor-test -Location westus2 -Sku Standard

$WorkspaceKey = Get-AzOperationalInsightsWorkspaceSharedKey -ResourceGroupName myResourceGroup -Name sapmonitor-test

$SapMonitorParams = @{
  Name = 'ps-sapmonitor-t01'
  ResourceGroupName = 'myResourceGroup'
  Location = 'westus2'
  EnableCustomerAnalytic = $true
  MonitorSubnet = '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/vnet-sap/subnets/mysubnet'
  LogAnalyticsWorkspaceSharedKey = $WorkspaceKey.PrimarySharedKey
  LogAnalyticsWorkspaceId = $Workspace.CustomerId
  LogAnalyticsWorkspaceResourceId = $Workspace.ResourceId
}
New-AzSapMonitor @SapMonitorParams
```

Verwenden Sie das [Get-AzSapMonitor](/powershell/module/az.hanaonazure/get-azsapmonitor) Cmdlet, um die Eigenschaften eines SAP-Monitors abzurufen. Im folgenden Beispiel werden die Eigenschaften eines SAP-Monitors für das angegebene Abonnement, die Ressourcengruppe und den Ressourcennamen abgerufen.

```azurepowershell-interactive
Get-AzSapMonitor -ResourceGroupName myResourceGroup -Name ps-spamonitor-t01
```

## <a name="provider-instance"></a>Anbieterinstanz

Verwenden Sie das [New-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/new-azsapmonitorproviderinstance) Cmdlet, um eine Anbieterinstanz zu erstellen. Im folgenden Beispiel wird eine Anbieterinstanz für das angegebene Abonnement, die Ressourcengruppe und den Ressourcennamen erstellt.

```azurepowershell-interactive
$SapProviderParams = @{
  ResourceGroupName = 'myResourceGroup'
  Name = 'ps-sapmonitorins-t01'
  SapMonitorName = 'yemingmonitor'
  ProviderType = 'SapHana'
  HanaHostname = 'hdb1-0'
  HanaDatabaseName = 'SYSTEMDB'
  HanaDatabaseSqlPort = '30015'
  HanaDatabaseUsername = 'SYSTEM'
  HanaDatabasePassword = (ConvertTo-SecureString 'Manager1' -AsPlainText -Force)
}
New-AzSapMonitorProviderInstance @SapProviderParams
```

Verwenden Sie das [Get-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/get-azsapmonitorproviderinstance) Cmdlet, um die Eigenschaften einer Anbieterinstanz abzurufen. Das folgende Beispiel zeigt, von welchen Instanzen die Eigenschaften abgerufen werden: 
- Eine Anbieterinstanz für das angegebene Abonnement
- Die Ressourcengruppe
- Der SAP-Monitorname
- Der Ressourcenname

```azurepowershell-interactive
Get-AzSapMonitorProviderInstance -ResourceGroupName myResourceGroup -SapMonitorName ps-spamonitor-t01
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die in diesem Artikel erstellten Ressourcen nicht benötigt werden, können Sie sie löschen, indem Sie die folgenden Beispiele ausführen.

### <a name="delete-the-provider-instance"></a>Löschen der Anbieterinstanz

Verwenden Sie das [Remove-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/remove-azsapmonitorproviderinstance) Cmdlet, um eine Anbieterinstanz zu entfernen. Im folgenden Beispiel wird eine Anbieterinstanz für das angegebene Abonnement, die Ressourcengruppe, den SapMonitor-Namen und den Ressourcennamen gelöscht.

```azurepowershell-interactive
Remove-AzSapMonitorProviderInstance -ResourceGroupName myResourceGroup -SapMonitorName ps-spamonitor-t01 -Name ps-sapmonitorins-t02
```

### <a name="delete-the-sap-monitor"></a>Löschen des SAP-Monitors

Verwenden Sie das [Remove-AzSapMonitor](/powershell/module/az.hanaonazure/remove-azsapmonitor) Cmdlet, um einen SAP-Monitor zu entfernen. Im folgenden Beispiel wird ein SAP-Monitor für das angegebene Abonnement, die Ressourcengruppe und den Monitornamen gelöscht.

```azurepowershell
Remove-AzSapMonitor -ResourceGroupName myResourceGroup -Name ps-sapmonitor-t02
```

### <a name="delete-the-resource-group"></a>Löschen der Ressourcengruppe

> [!CAUTION]
> Im folgenden Beispiel werden die angegebene Ressourcengruppe und alle darin enthaltenen Ressourcen gelöscht.
> Falls in der angegebenen Ressourcengruppe Ressourcen enthalten sind, die nicht zum Umfang dieses Artikels gehören, werden sie ebenfalls gelöscht.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Monitor für SAP-Lösungen:

> [!div class="nextstepaction"]
> [Überwachen von SAP in Azure](monitor-sap-on-azure.md)
