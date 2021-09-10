---
title: Erstellen von Metrikwarnungsmonitoren in Azure CLI
description: Erfahren Sie, wie Sie Metrikwarnungen in Azure Monitor mit Azure CLI Befehlen erstellen. In diesen Beispielen werden Warnungen für einen virtuellen Computer und einen App Service-Plan erstellt.
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 08/06/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 60d2b7f706c985eaa13c634e0323927eb39446ad
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117534"
---
# <a name="create-metric-alert-monitors-in-azure-cli"></a>Erstellen von Metrikwarnungsmonitoren in Azure CLI

In diesen Beispiele werden Metrikwarnungsmonitore in Azure Monitor mithilfe von Azure CLI Befehlen erstellt. Im ersten Beispiel wird eine Warnung für einen virtuellen Computer erstellt. Der zweite Befehl erstellt eine Warnung, die eine Dimension für einen App Service-Plan enthält.  

[!INCLUDE [Prepare your Azure CLI environment](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-an-alert"></a>Erstellen einer Warnung

Diese Warnung überwacht einen vorhandenen virtuellen Computer namens `VM07` in der Ressourcengruppe mit dem Namen `ContosoVMRG`.

Sie können eine Ressourcengruppe mithilfe des Befehls [az group create](/cli/azure/group#az-group-create) erstellen. Informationen zum Erstellen virtueller Computer finden Sie unter [Erstellen eines virtuellen Windows-Computers mit der Azure CLI](../virtual-machines/windows/quick-create-cli.md), [Erstellen eines virtuellen Linux-Computers mit der Azure CLI](../virtual-machines/linux/quick-create-cli.md) und dem Befehl [az vm create](/cli/azure/vm#az-vm-create).

```azurecli
# resource group name: ContosoVMRG
# virtual machine name: VM07

# Create scope
scope=$(az vm show --resource-group ContosoVMRG --name VM07 --output tsv --query id)

# Create action
action=$(az monitor action-group create --name ContosoWebhookAction \
  --resource-group ContosoVMRG --output tsv --query id \
  --action webhook https://alerts.contoso.com usecommonalertschema)

# Create condition
condition=$(az monitor metrics alert condition create --aggregation Average \
  --metric "Percentage CPU" --op GreaterThan --type static --threshold 90 --output tsv)

# Create metrics alert
az monitor metrics alert create --name alert-01 --resource-group ContosoVMRG \
  --scopes $scope --action $action --condition $condition --description "Test High CPU"
```

In diesem Beispiel wird der Ausgabetyp `tsv` verwendet, der keine unerwünschten Symbole wie Anführungszeichen enthält. Weitere Informationen finden Sie unter [Effektives Verwenden von Azure CLI](/cli/azure/use-cli-effectively).

## <a name="create-an-alert-with-a-dimension"></a>Erstellen einer Warnung mit einer Dimension

In diesem Beispiel wird ein App Service-Plan und dann eine Metrikwarnung dafür erstellt. Im Beispiel wird eine Dimension verwendet, um anzugeben, dass alle Instanzen des App Service-Plans unter diese Metrik fallen. Im Beispiel werden eine Ressourcengruppe und ein Anwendungsdienstplan erstellt.

```azurecli
# Create resource group
az group create --name ContosoRG --location eastus2
 
# Create application service plan
az appservice plan create --resource-group ContosoRG --name ContosoAppServicePlan \
   --is-linux --number-of-workers 4 --sku S1 
 
# Create scope
scope=$(az appservice plan show --resource-group ContosoRG --name ContosoAppServicePlan \
   --output tsv --query id) 
 
# Create dimension
dim01=$(az monitor metrics alert dimension create --name Instance --value * --op Include --output tsv)
 
# Create condition
condition=$(az monitor metrics alert condition create --aggregation Average \
   --metric CpuPercentage --op GreaterThan --type static --threshold 90 \
   --dimension $dim01 --output tsv)
```

Um eine Liste der möglichen Metriken anzuzeigen, führen Sie den Befehl [az monitor metrics list-definitions](/cli/azure/monitor/metrics#az_monitor_metrics_list_definitions) aus. Der Parameter `--output` zeigt die Werte in einem lesbaren Format an.


```azurecli
az monitor metrics list-definitions --resource $scope --output table 
 
# Create metrics alert
az monitor metrics alert create --name alert-02 --resource-group ContosoRG \
   --scopes $scope --condition $condition --description "Service Plan High CPU"
```

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Wenn Sie Ressourcengruppen zum Testen dieser Befehle erstellt haben, können Sie eine Ressourcengruppe und ihren gesamten Inhalt mit dem Befehl [az group delete](/cli/azure/group#az-group-delete) entfernen:

```azurecli
az group delete --name ContosoVMRG

az group delete --name ContosoRG
```

Wenn Sie vorhandene Ressourcen verwendet haben, die Sie behalten möchten, verwenden Sie den Befehl [az monitor metrics alert delete](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-delete), um Ihre Übungswarnungen zu löschen:

```azurecli
az monitor metrics alert delete --name alert-01

az monitor metrics alert delete --name alert-02
```

## <a name="azure-cli-commands-used-in-this-article"></a>In diesem Artikel verwendete Azure CLI-Befehle

In diesem Artikel werden die folgenden Azure CLI-Befehle verwendet:

- [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create)
- [az appservice plan show](/cli/azure/appservice/plan#az_appservice_plan_show)
- [az group create](/cli/azure/group#az-group-create)
- [az group delete](/cli/azure/group#az-group-delete)
- [az monitor action-group create](/cli/azure/monitor/action-group#az_monitor_action_group_create)
- [az monitor metrics alert condition create](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-condition-create)
- [az monitor metrics alert create](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-create)
- [az monitor metrics alert delete](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-delete)
- [az monitor metrics alert dimension create](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-dimension-create)
- [az monitor metrics list-definitions](/cli/azure/monitor/metrics#az_monitor_metrics_list_definitions)
- [az vm show](/cli/azure/vm#az_vm_show)

## <a name="next-steps"></a>Nächste Schritte

- [CLI-Beispiele für Azure Monitor](cli-samples.md)
- [Informationen zur Funktionsweise von Metrikwarnungen in Azure Monitor](alerts/alerts-metric-overview.md)
