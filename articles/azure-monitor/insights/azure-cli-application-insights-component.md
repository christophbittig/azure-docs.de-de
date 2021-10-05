---
title: Verwalten von Application Insights-Komponenten in der Azure CLI
description: Verwenden Sie diesen Beispielcode, um Komponenten in Application Insights zu verwalten. Dieses Feature ist Teil von Azure Monitor.
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 09/10/2012
ms.custom: devx-track-azurecli
ms.openlocfilehash: b8c7b81847a1ac2484a1eaa0df56bde35eec6385
ms.sourcegitcommit: 3ef5a4eed1c98ce76739cfcd114d492ff284305b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128709099"
---
# <a name="manage-application-insights-components-by-using-azure-cli"></a>Verwalten von Application Insights-Komponenten mit der Azure CLI

Komponenten sind in Azure Monitor unabhängig bereitstellbare Teile Ihrer verteilten Anwendung oder Microserviceanwendung. Verwenden Sie diese Azure CLI-Befehle, um Komponenten in Application Insights zu verwalten.

Die Beispiele in diesem Artikel führen die folgenden Verwaltungsaufgaben aus:

- Erstellen einer Komponente.
- Verbinden einer Komponente mit einer Web-App.
- Verknüpfen einer Komponente mit einem Speicherkonto mit einer Komponente.
- Erstellen einer Konfiguration für den fortlaufenden Export für eine Komponente.

[!INCLUDE [Prepare your Azure CLI environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-a-component"></a>Erstellen einer Komponente

Wenn Sie noch nicht über eine Ressourcengruppe und einen Arbeitsbereich verfügen, erstellen Sie diese Elemente mit [az group create](/cli/azure/group#az_group_create) und [az monitor log-analytics workspace create](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_create):

```azurecli
az group create --name ContosoAppInsightRG --location eastus2
az monitor log-analytics workspace create --resource-group ContosoAppInsightRG \
   --workspace-name AppInWorkspace
```

Führen Sie zum Erstellen einer Komponente den Befehl [az monitor app-insights component create](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_create) aus. Der Befehl [az monitor app-insights component show](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_show) zeigt die Komponente an.

```azurecli
az monitor app-insights component create --resource-group ContosoAppInsightRG \
   --app ContosoApp --location eastus2 --kind web --application-type web \
   --retention-time 120
az monitor app-insights component show --resource-group ContosoAppInsightRG --app ContosoApp
```

## <a name="connect-a-webapp"></a>Verbinden einer Web-App

In diesem Beispiel wird ihre Komponente mit einer Web-App verbunden. Sie können eine Web-App erstellen, indem Sie die Befehle [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create) und [az webapp create](/cli/azure/webapp#az_webapp_create) verwenden:

```azurecli
az appservice plan create --resource-group ContosoAppInsightRG --name ContosoAppService
az webapp create --resource-group ContosoAppInsightRG --name ContosoApp \
   --plan ContosoAppService --name ContosoApp8765
```

Führen Sie den Befehl [az monitor app-insights component connect-webapp](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_connect_webapp) aus, um Ihre Komponente mit der Web-App zu verbinden:

```azurecli
az monitor app-insights component connect-webapp --resource-group ContosoAppInsightRG \
   --app ContosoApp --web-app ContosoApp8765 --enable-debugger false --enable-profiler false
```

Sie können stattdessen eine Verbindung mit einer Azure-Funktion herstellen, indem Sie den Befehl [az monitor app-insights component connect-function](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_connect_function) verwenden.

## <a name="link-a-component-to-storage"></a>Verknüpfen einer Komponente mit dem Speicher

Sie können eine Komponente mit einem Speicherkonto verknüpfen. Verwenden Sie zum Erstellen eines Speicherkontos den Befehl [az storage account create](/cli/azure/storage/account#az_storage_account_create):

```azurecli
az storage account create --resource-group ContosoAppInsightRG \
   --name contosolinkedstorage --location eastus2 --sku Standard_LRS
```

Um Ihre Komponente mit dem Speicherkonto zu verknüpfen, führen Sie den Befehl [az monitor app-insights component linked-storage link](/cli/azure/monitor/app-insights/component/linked-storage#az_monitor_app_insights_component_linked_storage_link) aus. Sie können die vorhandenen Links mit dem Befehl [az monitor app-insights component linked-storage show](/cli/azure/monitor/app-insights/component/linked-storage#az_monitor_app_insights_component_linked_storage_show) anzeigen:


```azurecli
az monitor app-insights component linked-storage link  --resource-group ContosoAppInsightRG \
   --app ContosoApp --storage-account contosolinkedstorage
az monitor app-insights component linked-storage show --resource-group ContosoAppInsightRG \
   --app ContosoApp
```

Um die Verknüpfung mit dem Speicherkonto aufzuheben, führen Sie den Befehl [az monitor app-insights component linked-storage unlink](/cli/azure/monitor/app-insights/component/linked-storage#az_monitor_app_insights_component_linked_storage_unlink) aus:

```AzureCLI
az monitor app-insights component linked-storage unlink  \
   --resource-group ContosoAppInsightRG --app ContosoApp
```

## <a name="set-up-continuous-export"></a>Einrichten des fortlaufenden Exports

Beim fortlaufenden Export werden Ereignisse aus dem Application Insights-Portal in einem Speichercontainer im JSON-Format gespeichert.

> [!NOTE]
> Der fortlaufende Export wird nur für klassische Application Insights Ressourcen unterstützt. [Für arbeitsbereichsbasierte Application Insights-Ressourcen](../app/create-workspace-resource.md) müssen [Diagnoseeinstellungen](../app/create-workspace-resource.md#export-telemetry) verwendet werden.
>

Um einen Speichercontainer zu erstellen, führen Sie den Befehl [az storage container create](/cli/azure/storage/container#az_storage_container_create) aus. 

```azurecli
az storage container create --name contosostoragecontainer --account-name contosolinkedstorage \
   --public-access blob 
```

Sie benötigen schreibgeschützten Zugriff auf den Container. Führen Sie das Cmdlet [az storage container policy create](/cli/azure/storage/container/policy#az_storage_container_policy_create) aus:

```azurecli
az storage container policy create --container-name contosostoragecontainer \
   --account-name contosolinkedstorage --name WAccessPolicy --permissions w
```

Erstellen Sie mit dem Befehl [az storage container generate-sas](/cli/azure/storage/container#az_storage_container_generate_sas) einen SAS-Schlüssel. Achten Sie darauf, den Parameterwert `--output tsv` zu verwenden, um den Schlüssel ohne unerwünschte Formatierungen wie Anführungszeichen zu speichern. Weitere Informationen finden Sie unter [Effektives Verwenden von Azure CLI](/cli/azure/use-cli-effectively).

```azurecli
containersas=$(az storage container generate-sas --name contosostoragecontainer \
   --account-name contosolinkedstorage --permissions w --output tsv)
```

Führen Sie zum Erstellen eines fortlaufenden Exports den Befehl [az monitor app-insights component continues-export create](/cli/azure/monitor/app-insights/component/continues-export#az_monitor_app_insights_component_continues_export_create) aus:

```azurecli
az monitor app-insights component continues-export create --resource-group ContosoAppInsightRG \
   --app ContosoApp --record-types Event --dest-account contosolinkedstorage \
   --dest-container contosostoragecontainer --dest-sub-id 00000000-0000-0000-0000-000000000000 \
   --dest-sas $containersas
```

Sie können einen konfigurierten fortlaufenden Export löschen, indem Sie den Befehl [az monitor app-insights component continues-export delete](/cli/azure/monitor/app-insights/component/continues-export#az_monitor_app_insights_component_continues_export_delete) verwenden: 

```azurecli
az monitor app-insights component continues-export list \
   --resource-group ContosoAppInsightRG --app ContosoApp
az monitor app-insights component continues-export delete \
   --resource-group ContosoAppInsightRG --app ContosoApp --id abcdefghijklmnopqrstuvwxyz=
```

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Wenn Sie eine Ressourcengruppe zum Testen dieser Befehle erstellt haben, können Sie die Ressourcengruppe und ihren gesamten Inhalt mit dem Befehl [az group delete](/cli/azure/group#az_group_delete) entfernen:

```azurecli
az group delete --name ContosoAppInsightRG 
```

## <a name="azure-cli-commands-used-in-this-article"></a>In diesem Artikel verwendete Azure CLI-Befehle

- [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create)
- [az group create](/cli/azure/group#az_group_create)
- [az group delete](/cli/azure/group#az_group_delete)
- [az monitor app-insights component connect-webapp](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_connect_webapp)
- [az monitor app-insights component continues-export create](/cli/azure/monitor/app-insights/component/continues-export#az_monitor_app_insights_component_continues_export_create)
- [az monitor app-insights component continues-export delete](/cli/azure/monitor/app-insights/component/continues-export#az_monitor_app_insights_component_continues_export_delete)
- [az monitor app-insights component continues-export list](/cli/azure/monitor/app-insights/component/continues-export#az_monitor_app_insights_component_continues_export_list)
- [az monitor app-insights component create](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_create)
- [az monitor app-insights component linked-storage link](/cli/azure/monitor/app-insights/component/linked-storage#az_monitor_app_insights_component_linked_storage_link)
- [az monitor app-insights component linked-storage unlink](/cli/azure/monitor/app-insights/component/linked-storage#az_monitor_app_insights_component_linked_storage_unlink)
- [az monitor app-insights component show](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_show)
- [az monitor log-analytics workspace create](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_create)
- [az storage account create](/cli/azure/storage/account#az_storage_account_create)
- [az storage container create](/cli/azure/storage/container#az_storage_container_create)
- [az storage container generate-sas](/cli/azure/storage/container#az_storage_container_generate_sas)
- [az storage container policy create](/cli/azure/storage/container/policy#az_storage_container_policy_create)
- [az webapp create](/cli/azure/webapp#az_webapp_create)

## <a name="next-steps"></a>Nächste Schritte

[CLI-Beispiele für Azure Monitor](../cli-samples.md)

[Suchen nach und Diagnostizieren von Leistungsproblemen](../app/tutorial-performance.md)

[Überwachen der Anwendungsintegrität und Ausgeben von Warnungen](../app/tutorial-alert.md)
