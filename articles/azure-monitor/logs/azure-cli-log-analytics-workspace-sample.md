---
title: Verwalten von Azure Monitor-Protokollen in Azure CLI
description: Erfahren Sie, wie Sie Azure CLI Befehle verwenden, um einen Arbeitsbereich in Azure Monitor-Protokollen zu verwalten, einschließlich der Interaktionsweise von Arbeitsbereichen mit anderen Azure-Diensten.
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 08/16/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 548ddbc3fce6220e32de2df43024b7c4ed22f5e7
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123187890"
---
# <a name="managing-azure-monitor-logs-in-azure-cli"></a>Verwalten von Azure Monitor-Protokollen in Azure CLI

Verwenden Sie die hier beschriebenen Azure CLI-Befehle, um Ihren Log Analytics-Arbeitsbereich in Azure Monitor zu verwalten.

> [!NOTE]
> Am 31. August 2024 stellt Microsoft den Log Analytics-Agent ein. Im Anschluss daran können Sie den Azure Monitor-Agent verwenden. Weitere Informationen finden Sie unter [Übersicht über Azure Monitor-Agents](../agents/agents-overview.md).

[!INCLUDE [Prepare your Azure CLI environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-a-workspace-for-monitor-logs"></a>Erstellen eines Arbeitsbereichs für Monitor-Protokolle

Führen Sie den Befehl [az group create](/cli/azure/group#az_group_create) aus, um eine Ressourcengruppe zu erstellen, oder verwenden Sie eine vorhandene Ressourcengruppe. Verwenden Sie zum Erstellen eines Arbeitsbereichs den Befehl [az monitor log-analytics workspace create](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_create).

```azurecli
az group create --name ContosoRG --location eastus2
az monitor log-analytics workspace create --resource-group ContosoRG \
   --workspace-name ContosoWorkspace
```

Weitere Informationen zu Arbeitsbereichen finden Sie in der [Azure Monitor-Protokolle: Übersicht](/azure/azure-monitor/logs/data-platform-logs).

## <a name="list-tables-in-your-workspace"></a>Auflisten von Tabellen in Ihrem Arbeitsbereich

Jeder Arbeitsbereich enthält Tabellen mit Spalten, die über mehrere Datenzeilen verfügen. Jede Tabelle ist durch einen eindeutigen Satz von Spalten definiert, die von der Datenquelle bereitgestellt werden.

Um diese Tabellen in Ihrem Arbeitsbereich anzuzeigen, verwenden Sie den Befehl [az monitor log-analytics workspace table list](/cli/azure/monitor/log-analytics/workspace/table#az_monitor_log_analytics_workspace_table_list):

```azurecli
az monitor log-analytics workspace table list --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --output table
```

Der Ausgabewert `table` stellt die Ergebnisse in einem besser lesbaren Format dar. Weitere Informationen finden Sie unter [Ausgabeformatierung](/cli/azure/use-cli-effectively#output-formatting).

Um die Aufbewahrungsdauer für eine Tabelle zu ändern, führen Sie den Befehl [az monitor log-analytics workspace table update](/cli/azure/monitor/log-analytics/workspace/table#az_monitor_log_analytics_workspace_table_update) aus:

```azurecli
az monitor log-analytics workspace table update --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name Syslog --retention-time 45
```

Die Aufbewahrungsdauer liegt zwischen 30 und 730 Tagen.

Weitere Informationen zu Tabellen finden Sie unter [Datenstruktur](/azure/azure-monitor/logs/data-platform-logs#data-structure).

## <a name="export-data-from-selected-tables"></a>Exportieren von Daten aus ausgewählten Tabellen

Sie können kontinuierlich Daten aus ausgewählten Tabellen in ein Azure-Speicherkonto oder in Azure Event Hubs exportieren. Verwenden Sie den Befehl [az monitor log-analytics workspace data-export create](/cli/azure/monitor/log-analytics/workspace/data-export#az_monitor_log_analytics_workspace_data_export_create):

```azurecli
az monitor log-analytics workspace data-export create --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name DataExport --table Syslog \
   --destination /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Storage/storageAccounts/exportaccount \
   --enable
```

Um ihre Datenexporte anzuzeigen, verwenden Sie den Befehl [az monitor log-analytics workspace data-export list](/cli/azure/monitor/log-analytics/workspace/data-export#az_monitor_log_analytics_workspace_data_export_list).

```azurecli
az monitor log-analytics workspace data-export list --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --output table
```

Um ihre Datenexporte zu löschen, verwenden Sie den Befehl [az monitor log-analytics workspace data-export delete](/cli/azure/monitor/log-analytics/workspace/data-export#az_monitor_log_analytics_workspace_data_export_delete). Durch den Parameter `--yes` wird die Bestätigung übersprungen.

```azurecli
az monitor log-analytics workspace data-export delete --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name DataExport --yes
```

Weitere Informationen zum Exportieren von Daten finden Sie unter [Datenexport im Log Analytics-Arbeitsbereich in Azure Monitor](/azure/azure-monitor/logs/logs-data-export).

## <a name="manage-a-linked-service"></a>Verwalten eines verknüpften Diensts

Verknüpfte Dienste definieren eine Beziehung zwischen dem Arbeitsbereich und einer anderen Azure-Ressource. Azure Monitor-Protokolle und Azure-Ressourcen verwenden diese Verbindung für ihre Vorgänge. Beispielverwendungen verknüpfter Dienste, einschließlich eines Automation-Kontos und einer Arbeitsbereichszuordnung zu kundenseitig verwalteten Schlüsseln.

Um einen verknüpften Dienst zu erstellen, führen Sie den Befehl [az monitor log-analytics workspace linked-service create](/cli/azure/monitor/log-analytics/workspace/linked-service#az_monitor_log_analytics_workspace_linked_service_create) aus:

```azurecli
az monitor log-analytics workspace linked-service create --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name linkedautomation \
   --resource-id /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Web/sites/ContosoWebApp09

az monitor log-analytics workspace linked-service list --resource-group ContosoRG \
   --workspace-name ContosoWorkspace
```

Um eine verknüpfte Dienstbeziehung zu entfernen, führen Sie den Befehl [az monitor log-analytics workspace linked-service delete](/cli/azure/monitor/log-analytics/workspace/linked-service#az_monitor_log_analytics_workspace_linked_service_delete) aus:

```azurecli
az monitor log-analytics workspace linked-service delete --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name linkedautomation
```

Weitere Informationen finden Sie unter [az monitor log-analytics workspace linked-service](/cli/azure/monitor/log-analytics/workspace/linked-service).

## <a name="manage-linked-storage"></a>Verwalten von verknüpftem Speicher

Wenn Sie Ihr eigenes Speicherkonto für Log Analytics bereitstellen und verwalten, können Sie es mit diesen Azure CLI-Befehlen verwalten.

Um Ihren Arbeitsbereich mit einem Speicherkonto zu verknüpfen, führen Sie den Befehl [az monitor log-analytics workspace linked-storage create](/cli/azure/monitor/log-analytics/workspace/linked-storage#az_monitor_log_analytics_workspace_linked_storage_create) aus:

```azurecli
az monitor log-analytics workspace linked-storage create --resource-group ContosoRG \
   --workspace-name ContosoWorkspace \
   --storage-accounts /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Storage/storageAccounts/contosostorage \
   --type Alerts

az monitor log-analytics workspace linked-storage list --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --output table
```

Um die Verknüpfung mit einem Speicherkonto zu entfernen, führen Sie den Befehl [az monitor log-analytics workspace linked-storage delete](/cli/azure/monitor/log-analytics/workspace/linked-storage#az_monitor_log_analytics_workspace_linked_storage_delete) aus:

```azurecli
az monitor log-analytics workspace linked-storage delete --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --type Alerts
```

Weitere Informationen finden Sie unter [Verwenden von kundenseitig verwalteten Speicherkonten in Azure Monitor Log Analytics](/azure/azure-monitor/logs/private-storage).

## <a name="manage-intelligence-packs"></a>Verwalten von Intelligence Packs

Führen Sie den Befehl [az monitor log-analytics workspace pack list](/cli/azure/monitor/log-analytics/workspace/pack#az_monitor_log_analytics_workspace_pack_list) aus, um die verfügbaren Intelligence Packs anzuzeigen. Der Befehl teilt Ihnen auch mit, ob das Paket aktiviert ist.

```azurecli
az monitor log-analytics workspace pack list --resource-group ContosoRG \
   --workspace-name ContosoWorkspace
```

Verwenden Sie die Befehle [az monitor log-analytics workspace pack enable](/cli/azure/monitor/log-analytics/workspace/pack#az_monitor_log_analytics_workspace_pack_enable) oder [az monitor log-analytics workspace pack disable](/cli/azure/monitor/log-analytics/workspace/pack#az_monitor_log_analytics_workspace_pack_disable):

```azurecli
az monitor log-analytics workspace pack enable --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name NetFlow

az monitor log-analytics workspace pack disable --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name NetFlow
```

## <a name="manage-saved-searches"></a>Verwalten gespeicherter Suchvorgänge

Führen Sie zum Erstellen einer gespeicherten Suche den Befehl [az monitor log-analytics workspace saved-search](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_create) aus:

```azurecli
az monitor log-analytics workspace saved-search create --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name SavedSearch01 \
   --category "Log Management" --display-name SavedSearch01 \
   --saved-query "AzureActivity | summarize count() by bin(TimeGenerated, 1h)" --fa Function01 --fp "a:string = value"
```

Führen Sie zum Anzeigen Ihrer gespeicherten Suche den Befehl [az monitor log-analytics workspace saved-search show](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_show) aus. Führen Sie zum Anzeigen aller gespeicherten Suche den Befehl [az monitor log-analytics workspace saved-search list](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_list) aus.

```azurecli
az monitor log-analytics workspace saved-search show --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name SavedSearch01
az monitor log-analytics workspace saved-search list --resource-group ContosoRG \
   --workspace-name ContosoWorkspace
```

Führen Sie zum Löschen einer gespeicherten Suche den Befehl [az monitor log-analytics workspace saved-search delete](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_delete) aus:

```azurecli
az monitor log-analytics workspace saved-search delete --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name SavedSearch01 --yes
```

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Wenn Sie eine Ressourcengruppe zum Testen dieser Befehle erstellt haben, können Sie die Ressourcengruppe und ihren gesamten Inhalt mit dem Befehl [az group delete](/cli/azure/group#az-group-delete) entfernen:

```azurecli
az group delete --name ContosoRG
```

Wenn Sie einen neuen Arbeitsbereich aus einer vorhandenen Ressourcengruppe entfernen möchten, führen Sie den Befehl [az monitor log-analytics workspace delete](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_delete) aus:

```azurecli
az monitor log-analytics workspace delete --resource-group ContosoRG 
   --workspace-name ContosoWorkspace --yes
```

Log Analytics-Arbeitsbereiche verfügen über eine Option zum vorläufigen Löschen. Sie können einen gelöschten Arbeitsbereich zwei Wochen lang nach dem Löschen wiederherstellen. Führen Sie den Befehl [az monitor log-analytics workspace recover](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_recover) aus:

```azurecli
az monitor log-analytics workspace recover --resource-group ContosoRG 
   --workspace-name ContosoWorkspace
```

Fügen Sie im Befehl „delete“ den Parameter `--force` hinzu, um den Arbeitsbereich sofort zu löschen.

## <a name="azure-cli-commands-used-in-this-article"></a>In diesem Artikel verwendete Azure CLI-Befehle

- [az group create](/cli/azure/group#az_group_create)
- [az group delete](/cli/azure/group#az-group-delete)
- [az monitor log-analytics workspace create](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_create)
- [az monitor log-analytics workspace data-export create](/cli/azure/monitor/log-analytics/workspace/data-export#az_monitor_log_analytics_workspace_data_export_create)
- [az monitor log-analytics workspace data-export delete](/cli/azure/monitor/log-analytics/workspace/data-export#az_monitor_log_analytics_workspace_data_export_delete)
- [az monitor log-analytics workspace data-export list](/cli/azure/monitor/log-analytics/workspace/data-export#az_monitor_log_analytics_workspace_data_export_list)
- [az monitor log-analytics workspace delete](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_delete)
- [az monitor log-analytics workspace linked-service create](/cli/azure/monitor/log-analytics/workspace/linked-service#az_monitor_log_analytics_workspace_linked_service_create)
- [az monitor log-analytics workspace linked-service delete](/cli/azure/monitor/log-analytics/workspace/linked-service#az_monitor_log_analytics_workspace_linked_service_delete)
- [az monitor log-analytics workspace linked-storage create](/cli/azure/monitor/log-analytics/workspace/linked-storage#az_monitor_log_analytics_workspace_linked_storage_create)
- [az monitor log-analytics workspace linked-storage delete](/cli/azure/monitor/log-analytics/workspace/linked-storage#az_monitor_log_analytics_workspace_linked_storage_delete)
- [az monitor log-analytics workspace pack disable](/cli/azure/monitor/log-analytics/workspace/pack#az_monitor_log_analytics_workspace_pack_disable)
- [az monitor log-analytics workspace pack enable](/cli/azure/monitor/log-analytics/workspace/pack#az_monitor_log_analytics_workspace_pack_enable)
- [az monitor log-analytics workspace pack list](/cli/azure/monitor/log-analytics/workspace/pack#az_monitor_log_analytics_workspace_pack_list)
- [az monitor log-analytics workspace recover](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_recover)
- [az monitor log-analytics workspace saved-search delete](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_delete)
- [az monitor log-analytics workspace saved-search list](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_list)
- [az monitor log-analytics workspace saved-search show](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_show)
- [az monitor log-analytics workspace saved-search](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_create)
- [az monitor log-analytics workspace table list](/cli/azure/monitor/log-analytics/workspace/table#az_monitor_log_analytics_workspace_table_list)
- [az monitor log-analytics workspace table update](/cli/azure/monitor/log-analytics/workspace/table#az_monitor_log_analytics_workspace_table_update)

## <a name="next-steps"></a>Nächste Schritte

[Übersicht über Log Analytics in Azure Monitor](log-analytics-overview.md)
