---
title: Datenexport im Log Analytics-Arbeitsbereich in Azure Monitor (Vorschau)
description: Der Log Analytics-Datenexport ermöglicht es Ihnen, Daten ausgewählter Tabellen aus Ihrem Log Analytics-Arbeitsbereich bei der Sammlung fortlaufend in ein Azure Storage-Konto oder in Azure Event Hubs zu exportieren.
ms.topic: conceptual
ms.custom: references_regions, devx-track-azurecli, devx-track-azurepowershell
author: bwren
ms.author: bwren
ms.date: 05/07/2021
ms.openlocfilehash: b9efc6c8f568d054662f9084d63b83de7b39e776
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355326"
---
# <a name="log-analytics-workspace-data-export-in-azure-monitor-preview"></a>Datenexport im Log Analytics-Arbeitsbereich in Azure Monitor (Vorschau)
Der Datenexport im Log Analytics-Arbeitsbereich in Azure Monitor ermöglicht es Ihnen, Daten aus ausgewählten Tabellen in Ihrem Log Analytics-Arbeitsbereich bei der Sammlung fortlaufend in ein Azure Storage-Konto oder in Azure Event Hubs zu exportieren. In diesem Artikel werden dieses Feature und die Schritte zum Konfigurieren des Datenexports in Ihren Arbeitsbereichen ausführlich beschrieben.

## <a name="overview"></a>Übersicht
Nachdem der Datenexport für den Log Analytics-Arbeitsbereich konfiguriert wurde, werden alle neuen Daten, die an die ausgewählten Tabellen im Arbeitsbereich gesendet werden, automatisch in stündlichen Anfügeblobs in Ihr Speicherkonto oder nahezu in Echtzeit in Ihren Event Hub exportiert.

![Übersicht über den Datenexport](media/logs-data-export/data-export-overview.png)

Alle Daten aus eingeschlossenen Tabellen werden ohne Filter exportiert. Wenn Sie z. B. eine Datenexportregel für die Tabelle *SecurityEvent* konfigurieren, werden alle Daten, die an die Tabelle *SecurityEvent* gesendet werden, ab dem Zeitpunkt der Konfiguration exportiert.


## <a name="other-export-options"></a>Weitere Exportoptionen
Mit dem Datenexport im Log Analytics-Arbeitsbereich werden kontinuierlich Daten aus einem Log Analytics Arbeitsbereich exportiert. Darüber hinaus gibt es die folgenden Möglichkeiten zum Exportieren von Daten für bestimmte Szenarien:

- Geplanter Export aus einer Protokollabfrage mithilfe einer Logik-App. Dies ähnelt dem Datenexportfeature, ermöglicht Ihnen aber das Senden von gefilterten oder aggregierten Daten an Azure Storage. Diese Methode unterliegt jedoch [Beschränkungen für Protokollabfragen](../service-limits.md#log-analytics-workspaces). Informationen dazu finden Sie unter [Archivieren von Daten aus dem Log Analytics-Arbeitsbereich in Azure Storage mithilfe von Logic Apps](logs-export-logic-app.md).
- Einmaliger Export auf lokalen Computer mit einem PowerShell-Skript. Informationen hierzu finden Sie unter [Invoke-AzOperationalInsightsQueryExport](https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport).

## <a name="limitations"></a>Einschränkungen

- Die Konfiguration kann zurzeit mithilfe der Befehlszeilenschnittstelle oder über REST-Anforderungen vorgenommen werden. Azure-Portal oder PowerShell werden noch nicht unterstützt.
- Die Option `--export-all-tables` in CLI und REST wird nicht unterstützt und entfernt. Sie müssen die Liste der Tabellen in den Exportregeln explizit angeben.
- Unterstützte Tabellen sind zurzeit auf diejenigen beschränkt, die weiter unten im Abschnitt [Unterstützte Tabellen](#supported-tables) aufgeführt werden. Beispielsweise werden benutzerdefinierte Protokolltabellen derzeit nicht unterstützt.
- Wenn die Datenexportregel eine nicht unterstützte Tabelle umfasst, wird der Vorgang erfolgreich ausgeführt. Es werden jedoch für diese Tabelle keine Daten exportiert, bis die Tabelle unterstützt wird. 
- Wenn die Datenexportregel eine nicht vorhandene Tabelle enthält, verursacht sie den Fehler `Table <tableName> does not exist in the workspace`.
- Der Datenexport wird in allen Regionen verfügbar sein. In den folgenden Regionen ist er jedoch noch nicht verfügbar: „Schweiz, Norden“, „Schweiz, Westen“, „Deutschland, Westen-Mitte“, „Australien, Mitte 2“, „VAE, Mitte“, „VAE, Norden“, „Japan, Westen“, „Brasilien, Südosten“, „Norwegen, Osten“, „Norwegen, Westen“, „Frankreich, Süden“, „Indien, Süden“, „Südkorea, Süden“, „Jio, Indien, Mitte“, „Jio, Indien, Westen“, „Kanada, Osten“, „USA, Westen 3“, „Schweden, Mitte“, „Schweden, Süden“, Government-Clouds, China.
- In Ihrem Arbeitsbereich können bis zu 10 aktivierte Regeln definiert werden. Zusätzliche Regeln sind zulässig, werden jedoch deaktiviert. 
- Ein Ziel muss für alle Exportregeln in Ihrem Arbeitsbereich eindeutig sein.
- Das Zielspeicherkonto oder der Ziel-Event Hub muss sich in derselben Region wie der Log Analytics-Arbeitsbereich befinden.
- Die Namen der zu exportierenden Tabellen dürfen bei einem Speicherkonto nicht mehr als 60 Zeichen und bei einem Event Hub nicht mehr als 47 Zeichen umfassen. Tabellen mit längeren Namen werden nicht exportiert.

## <a name="data-completeness"></a>Datenvollständigkeit
Der Datenexport versucht bis zu 30 Minuten lang, Daten zu senden, wenn das Ziel nicht verfügbar ist. Wenn es nach 30 Minuten immer noch nicht verfügbar ist, werden die Daten verworfen, bis das Ziel wieder verfügbar ist.

## <a name="cost"></a>Cost
Für das Datenexportfeature fallen zurzeit keine zusätzlichen Gebühren an. Die Preise für den Datenexport werden später bekanntgegeben, und vor Abrechnungsbeginn wird eine Kündigungsfrist eingeräumt. Falls Sie sich dafür entscheiden, den Datenexport über den Benachrichtigungszeitraum hinaus zu verwenden, wird dies Ihnen zum entsprechenden Tarif in Rechnung gestellt.

## <a name="export-destinations"></a>Exportziele

### <a name="storage-account"></a>Speicherkonto
Daten werden an Speicherkonten gesendet, wenn sie Azure Monitor erreichen, und in stündlichen Anfügeblobs gespeichert. Die Datenexportkonfiguration erstellt für jede Tabelle im Speicherkonto einen Container mit dem Namen *am-* , gefolgt vom Namen der Tabelle. Beispielsweise würde die Tabelle *SecurityEvent* an einen Container mit dem Namen *am-SecurityEvent* gesendet.

Der Blobpfad im Speicherkonto lautet *WorkspaceResourceId=/subscriptions/subscription-id/resourcegroups/\<resource-group\>/providers/microsoft.operationalinsights/workspaces/\<workspace\>/y=\<four-digit numeric year\>/m=\<two-digit numeric month\>/d=\<two-digit numeric day\>/h=\<two-digit 24-hour clock hour\>/m=00/PT1H.json*. Da Anfügeblobs auf 50.000 Schreibvorgänge im Speicher beschränkt sind, kann sich die Anzahl der exportierten Blobs erhöhen, wenn die Anzahl der Anfügevorgänge hoch ist. Das Benennungsmuster für Blobs ist in diesem Fall „PT1H_#.json“, wobei # die inkrementelle Blobanzahl ist.

Das Speicherkonto-Datenformat ist [JSON Lines](../essentials/resource-logs-blob-format.md). Dies bedeutet, dass die einzelnen Datensätze jeweils durch einen Zeilenumbruch getrennt sind und dass kein externes Datensatzarray und keine Kommas zwischen JSON-Datensätzen verwendet werden. 

[![Speicherbeispieldaten](media/logs-data-export/storage-data.png)](media/logs-data-export/storage-data.png#lightbox)

Durch den Log Analytics-Datenexport können Anfügeblobs in unveränderliche Speicherkonten geschrieben werden, wenn bei zeitbasierten Aufbewahrungsrichtlinien die Einstellung *allowProtectedAppendWrites* aktiviert ist. Dadurch wird das Schreiben neuer Blöcke in ein Anfügeblob ermöglicht, wobei gleichzeitig der Unveränderlichkeitsschutz und die Konformität aufrechterhalten bleiben. Weitere Informationen finden Sie unter [Zulassen von Schreibvorgängen in geschützten Anfügeblobs](../../storage/blobs/immutable-time-based-retention-policy-overview.md#allow-protected-append-blobs-writes).

### <a name="event-hub"></a>Event Hub
Daten werden, sobald sie Azure Monitor erreichen, nahezu in Echtzeit an Event Hub gesendet. Für jeden Datentyp, den Sie exportieren, wird ein Event Hub mit dem Namen *am-* erstellt, gefolgt vom Namen der Tabelle. Beispielsweise würde die Tabelle *SecurityEvent* an einen Event Hub mit dem Namen *am-SecurityEvent* gesendet. Wenn für die exportierten Daten ein bestimmter Event Hub als Ziel verwendet werden soll oder Sie eine Tabelle mit einem Namen haben, der den Grenzwert von 47 Zeichen überschreitet, können Sie den Namen Ihrer eigenen Event Hub-Instanz angeben und alle Daten für definierte Tabelle in diese exportieren.

> [!IMPORTANT]
> Die [Anzahl der unterstützten Event Hubs pro 'Basic'- und 'Standard'-Namensraumstufe beträgt 10](../../event-hubs/event-hubs-quotas.md#common-limits-for-all-tiers). Wenn Sie mehr als 10 Tabellen exportieren, teilen Sie die Tabellen entweder auf mehrere Exportregeln zu verschiedenen Event-Hub-Namensräumen auf oder geben Sie den Event-Hub-Namen in der Exportregel an und exportieren alle Tabellen zu diesem Event-Hub.

Überlegungen:
1. Die Event Hub-SKU „Basic“ unterstützt einen niedrigeren [Grenzwert](../../event-hubs/event-hubs-quotas.md#basic-vs-standard-vs-premium-vs-dedicated-tiers) für die Ereignisgröße. Einige Protokolle in Ihrem Arbeitsbereich können diesen überschreiten und werden möglicherweise gelöscht. Es wird empfohlen, einen Event Hub der SKU „Standard“ oder „Dedicated“ als Exportziel zu verwenden.
2. Die Menge der exportierten Daten nimmt häufig im Laufe der Zeit zu, und die Event Hub-Skalierung muss erhöht werden, um größere Übertragungsraten zu bewältigen und Drosselungsszenarien und Datenlatenz zu vermeiden. Verwenden Sie das Feature „Automatische Vergrößerung“ von Event Hubs, um die Anzahl von Durchsatzeinheiten automatisch hochzuskalieren und so den Nutzungsanforderungen gerecht zu werden. Weitere Informationen finden Sie unter [Automatisches Hochskalieren von Azure Event Hubs-Durchsatzeinheiten](../../event-hubs/event-hubs-auto-inflate.md).

## <a name="prerequisites"></a>Voraussetzungen
Vor der Konfiguration des Log Analytics-Datenexports müssen folgende Voraussetzungen erfüllt sein:

- Die Ziele müssen vor der Konfiguration der Exportregel erstellt werden und sollten sich in demselben Bereich befinden wie Ihr Log Analytics-Arbeitsbereich. Wenn Sie Ihre Daten in andere Speicherkonten replizieren müssen, können Sie eine der [Azure Storage Redundanzoptionen](../../storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) verwenden, einschließlich georedundantem Speicher (GRS) und geozonenredundantem Speicher (GZRS).
- Beim Speicherkonto muss es sich um StorageV1 oder höher handeln. Klassischer Speicher wird nicht unterstützt.
- Wenn Sie Ihr Speicherkonto so konfiguriert haben, dass der Zugriff von ausgewählten Netzwerken aus möglich ist, müssen Sie eine Ausnahme in den Einstellungen Ihres Speicherkontos hinzufügen, damit Azure Monitor in den Speicher schreiben darf.

## <a name="enable-data-export"></a>Aktivieren des Datenexports
Die folgenden Schritte müssen ausgeführt werden, um den Log Analytics-Datenexport zu aktivieren. Ausführliche Informationen finden Sie in den entsprechenden Abschnitten weiter unten.

- Registrieren Sie den Ressourcenanbieter.
- Lassen Sie vertrauenswürdige Microsoft-Dienste zu.
- Erstellen Sie mindestens eine Datenexportregel, mit der die zu exportierenden Tabellen und deren Ziel definiert werden.

### <a name="register-resource-provider"></a>Registrieren des Ressourcenanbieters
Der folgende Azure-Ressourcenanbieter muss für Ihr Abonnement registriert werden, um den Log Analytics-Datenexport zu aktivieren. 

- Microsoft.Insights

Dieser Ressourcenanbieter ist bei den meisten Azure Monitor-Benutzern wahrscheinlich bereits registriert. Um dies zu überprüfen, gehen Sie im Azure-Portal zu **Abonnements**. Wählen Sie Ihr Abonnement aus, und klicken Sie dann im Bereich **Einstellungen** im Menü auf **Ressourcenanbieter**. Suchen Sie nach **Microsoft.Insights**. Wenn der Status **Registriert** lautet, ist die Registrierung bereits erfolgt. Andernfalls klicken Sie auf **Registrieren**, um die Registrierung vorzunehmen.

Sie können auch eine der verfügbaren Methoden zum Registrieren eines Ressourcenanbieters verwenden, die unter [Azure-Ressourcenanbieter und -typen](../../azure-resource-manager/management/resource-providers-and-types.md) beschrieben sind. Im Folgenden finden Sie einen Beispielbefehl für PowerShell:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.insights
```

### <a name="allow-trusted-microsoft-services"></a>Zulassen vertrauenswürdiger Microsoft-Dienste
Wenn Sie Ihr Speicherkonto so konfiguriert haben, dass der Zugriff von ausgewählten Netzwerken aus möglich ist, müssen Sie eine Ausnahme hinzufügen, damit Azure Monitor in das Konto schreiben darf. Wählen Sie unter **Firewalls und virtuelle Netzwerke** für Ihr Speicherkonto **Vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Speicherkonto erlauben** aus.

[![„Firewalls und virtuelle Netzwerke“ unter Ihrem Speicherkonto](media/logs-data-export/storage-account-vnet.png)](media/logs-data-export/storage-account-vnet.png#lightbox)

### <a name="create-or-update-data-export-rule"></a>Erstellen oder Aktualisieren der Datenexportregel
Eine Datenexportregel definiert die Tabellen, für die Daten exportiert werden, und das Ziel. Sie können 10 aktivierte Regeln in Ihrem Arbeitsbereich haben, wenn sich jede zusätzliche Regel über 10 im deaktivierten Zustand befinden muss. Ein Ziel muss für alle Exportregeln in Ihrem Arbeitsbereich eindeutig sein.

> [!NOTE]
> Der Datenexport sendet Protokolle an Ziele, deren Eigentümer Sie sind, während diese einige Einschränkungen aufweisen: die [Skalierbarkeit von Speicherkonten](../../storage/common/scalability-targets-standard-account.md#scale-targets-for-standard-storage-accounts), [Event Hub-Namespace-Kontingent](../../event-hubs/event-hubs-quotas.md). Es wird empfohlen, Ihre Ziele auf Drosselung zu überwachen und Maßnahmen anzuwenden, wenn sie sich dem Grenzwert nähern. Beispiel: 
> - Stellen Sie die automatische Vergrößerungsfunktion im Event-Hub ein, um die Anzahl der TUs (Durchsatzeinheiten) automatisch zu vergrößern und zu erhöhen. Sie können weitere TUs anfordern, wenn die automatische Erhöhung maximal ist
> - Aufteilen von Tabellen auf mehrere Exportregeln, wobei jede auf verschiedene Ziele verteilt ist

Die Exportregel sollte Tabellen enthalten, die in Ihrem Arbeitsbereich enthalten sind. Führen Sie diese Abfrage für eine Liste verfügbarer Tabellen in Ihrem Arbeitsbereich aus.

```kusto
find where TimeGenerated > ago(24h) | distinct Type
```

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

–

# <a name="powershell"></a>[PowerShell](#tab/powershell)

–

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie den folgenden Befehl, um mithilfe der CLI eine Datenexportregel für ein Speicherkonto zu erstellen.

```azurecli
$storageAccountResourceId = '/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name'
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $storageAccountResourceId
```

Verwenden Sie den folgenden Befehl, um mithilfe der CLI eine Datenexportregel für einen Event Hub zu erstellen. Für jede Tabelle wird ein separater Event Hub erstellt.

```azurecli
$eventHubsNamespacesResourceId = '/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.EventHub/namespaces/namespaces-name'
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $eventHubsNamespacesResourceId
```

Verwenden Sie den folgenden Befehl, um mithilfe der CLI eine Datenexportregel für einen bestimmten Event Hub zu erstellen. Alle Tabellen werden in den angegebenen Event Hub exportiert. 

```azurecli
$eventHubResourceId = '/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.EventHub/namespaces/namespaces-name/eventhubs/eventhub-name'
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $eventHubResourceId
```

# <a name="rest"></a>[REST](#tab/rest)

Verwenden Sie die folgende Anforderung, um mithilfe der REST-API eine Datenexportregel zu erstellen. Die Anforderung sollte die Bearertokenautorisierung und den Inhaltstyp „application/json“ verwenden.

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

Der Anforderungstext gibt das Tabellenziel an. Im Folgenden sehen Sie einen Beispieltext für die REST-Anforderung für ein Speicherkonto.

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name"
        },
        "tablenames": [
            "table1",
            "table2" 
        ],
        "enable": true
    }
}
```

Im Folgenden sehen Sie einen Beispieltext für die REST-Anforderung für einen Event Hub.

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.EventHub/namespaces/eventhub-namespaces-name"
        },
        "tablenames": [
            "table1",
            "table2"
        ],
        "enable": true
    }
}
```

Es folgt ein Beispieltext für die REST-Anforderung für einen Event Hub, bei dem der Event Hub-Name angegeben wird. In diesem Fall werden alle exportierten Daten an diese Event Hub-Instanz gesendet.

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.EventHub/namespaces/eventhub-namespaces-name",
            "metaData": {
                "EventHubName": "eventhub-name"
        },
        "tablenames": [
            "table1",
            "table2"
        ],
        "enable": true
    }
  }
}
```

# <a name="template"></a>[Vorlage](#tab/json)

Verwenden Sie den folgenden Befehl, um mithilfe der Vorlage eine Datenexportregel für ein Speicherkonto zu erstellen.

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "defaultValue": "workspace-name",
            "type": "String"
        },
        "workspaceLocation": {
            "defaultValue": "workspace-region",
            "type": "string"
        },
        "storageAccountRuleName": {
            "defaultValue": "storage-account-rule-name",
            "type": "string"
        },
        "storageAccountResourceId": {
            "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceLocation')]",
            "resources": [
                {
                  "type": "microsoft.operationalinsights/workspaces/dataexports",
                  "apiVersion": "2020-08-01",
                  "name": "[concat(parameters('workspaceName'), '/' , parameters('storageAccountRuleName'))]",
                  "dependsOn": [
                      "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
                  ],
                  "properties": {
                      "destination": {
                          "resourceId": "[parameters('storageAccountResourceId')]"
                      },
                      "tableNames": [
                          "Heartbeat",
                          "InsightsMetrics",
                          "VMConnection",
                          "Usage"
                      ],
                      "enable": true
                  }
              }
            ]
        }
    ]
}
```

Verwenden Sie den folgenden Befehl, um mithilfe der Vorlage eine Datenexportregel für einen Event Hub zu erstellen. Für jede Tabelle wird ein separater Event Hub erstellt.

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "defaultValue": "workspace-name",
            "type": "String"
        },
        "workspaceLocation": {
            "defaultValue": "workspace-region",
            "type": "string"
        },
        "eventhubRuleName": {
            "defaultValue": "event-hub-rule-name",
            "type": "string"
        },
        "namespacesResourceId": {
            "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/microsoft.eventhub/namespaces/namespaces-name",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceLocation')]",
            "resources": [
              {
                  "type": "microsoft.operationalinsights/workspaces/dataexports",
                  "apiVersion": "2020-08-01",
                  "name": "[concat(parameters('workspaceName'), '/', parameters('eventhubRuleName'))]",
                  "dependsOn": [
                      "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
                  ],
                  "properties": {
                      "destination": {
                          "resourceId": "[parameters('namespacesResourceId')]"
                      },
                      "tableNames": [
                          "Usage",
                          "Heartbeat"
                      ],
                      "enable": true
                  }
              }
            ]
        }
    ]
}
```

Verwenden Sie den folgenden Befehl, um mithilfe der Vorlage eine Datenexportregel für einen bestimmten Event Hub zu erstellen. Alle Tabellen werden in den angegebenen Event Hub exportiert.

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "defaultValue": "workspace-name",
            "type": "String"
        },
        "workspaceLocation": {
            "defaultValue": "workspace-region",
            "type": "string"
        },
        "eventhubRuleName": {
            "defaultValue": "event-hub-rule-name",
            "type": "string"
        },
        "namespacesResourceId": {
            "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/microsoft.eventhub/namespaces/namespaces-name",
            "type": "String"
        },
        "eventhubName": {
            "defaultValue": "event-hub-name",
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceLocation')]",
            "resources": [
              {
                  "type": "microsoft.operationalinsights/workspaces/dataexports",
                  "apiVersion": "2020-08-01",
                  "name": "[concat(parameters('workspaceName'), '/', parameters('eventhubRuleName'))]",
                  "dependsOn": [
                      "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
                  ],
                  "properties": {
                      "destination": {
                          "resourceId": "[parameters('namespacesResourceId')]",
                          "metaData": {
                              "eventHubName": "[parameters('eventhubName')]"
                          }
                      },
                      "tableNames": [
                          "Usage",
                          "Heartbeat"
                      ],
                      "enable": true
                  }
              }
            ]
        }
    ]
}
```

---

## <a name="view-data-export-rule-configuration"></a>Anzeigen der Konfiguration der Datenexportregel

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

–

# <a name="powershell"></a>[PowerShell](#tab/powershell)

–

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie den folgenden Befehl, um die Konfiguration einer Datenexportregel mithilfe der CLI anzuzeigen.

```azurecli
az monitor log-analytics workspace data-export show --resource-group resourceGroupName --workspace-name workspaceName --name ruleName
```

# <a name="rest"></a>[REST](#tab/rest)

Verwenden Sie die folgende Anforderung, um die Konfiguration einer Datenexportregel mithilfe der REST-API anzuzeigen. Die Anforderung sollte die Bearertokenautorisierung verwenden.

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

# <a name="template"></a>[Vorlage](#tab/json)

–

---

## <a name="disable-an-export-rule"></a>Deaktivieren einer Exportregel

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

–

# <a name="powershell"></a>[PowerShell](#tab/powershell)

–

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Exportregeln können deaktiviert werden, damit Sie den Export beenden können, wenn über einen bestimmten Zeitraum hinweg keine Daten aufbewahrt werden müssen, z. B. während des Testens. Verwenden Sie den folgenden Befehl, um eine Datenexportregel mithilfe der CLI zu deaktivieren.

```azurecli
az monitor log-analytics workspace data-export update --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --enable false
```

# <a name="rest"></a>[REST](#tab/rest)

Exportregeln können deaktiviert werden, damit Sie den Export beenden können, wenn über einen bestimmten Zeitraum hinweg keine Daten aufbewahrt werden müssen, z. B. während des Testens. Verwenden Sie die folgende Anforderung, um eine Datenexportregel mithilfe der REST-API zu deaktivieren. Die Anforderung sollte die Bearertokenautorisierung verwenden.

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
Authorization: Bearer <token>
Content-type: application/json

{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name"
        },
        "tablenames": [
"table1",
    "table2" 
        ],
        "enable": false
    }
}
```

# <a name="template"></a>[Vorlage](#tab/json)

Exportregeln können deaktiviert werden, damit Sie den Export beenden können, wenn über einen bestimmten Zeitraum hinweg keine Daten aufbewahrt werden müssen, z. B. während des Testens. Legen Sie ```"enable": false``` in der Vorlage fest, um einen Datenexport zu deaktivieren.

---

## <a name="delete-an-export-rule"></a>Löschen einer Exportregel

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

–

# <a name="powershell"></a>[PowerShell](#tab/powershell)

–

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie den folgenden Befehl, um eine Datenexportregel mithilfe der CLI zu löschen.

```azurecli
az monitor log-analytics workspace data-export delete --resource-group resourceGroupName --workspace-name workspaceName --name ruleName
```

# <a name="rest"></a>[REST](#tab/rest)

Verwenden Sie die folgende Anforderung, um eine Datenexportregel mithilfe der REST-API zu löschen. Die Anforderung sollte die Bearertokenautorisierung verwenden.

```rest
DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

# <a name="template"></a>[Vorlage](#tab/json)

–

---

## <a name="view-all-data-export-rules-in-a-workspace"></a>Anzeigen aller Datenexportregeln in einem Arbeitsbereich

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

–

# <a name="powershell"></a>[PowerShell](#tab/powershell)

–

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie den folgenden Befehl, um alle Datenexportregeln in einem Arbeitsbereich mithilfe der CLI anzuzeigen.

```azurecli
az monitor log-analytics workspace data-export list --resource-group resourceGroupName --workspace-name workspaceName
```

# <a name="rest"></a>[REST](#tab/rest)

Verwenden Sie die folgende Anforderung, um alle Datenexportregeln in einem Arbeitsbereich mithilfe der REST-API anzuzeigen. Die Anforderung sollte die Bearertokenautorisierung verwenden.

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports?api-version=2020-08-01
```

# <a name="template"></a>[Vorlage](#tab/json)

–

---

## <a name="unsupported-tables"></a>Nicht unterstützte Tabellen
Wenn die Datenexportregel eine nicht unterstützte Tabelle umfasst, wird die Konfiguration erfolgreich ausgeführt, es werden jedoch für diese Tabelle keine Daten exportiert. Wenn die Tabelle zu einem späteren Zeitpunkt unterstützt wird, werden die entsprechenden Daten dann exportiert.

Wenn die Datenexportregel eine nicht vorhandene Tabelle umfasst, tritt der Fehler „Tabelle <tableName> ist im Arbeitsbereich nicht vorhanden“ auf.


## <a name="supported-tables"></a>Unterstützte Tabellen
Die Unterstützung für Tabellen ist zurzeit auf die unten genannten beschränkt. Es werden alle Daten aus der Tabelle exportiert, sofern keine Einschränkungen angegeben werden. Diese Liste wird aktualisiert, sobald weitere Tabellen unterstützt werden.

| Tabelle | Einschränkungen |
|:---|:---|
| AACAudit |  |
| AACHttpRequest |  |
| AADDomainServicesAccountLogon |  |
| AADDomainServicesAccountManagement |  |
| AADDomainServicesDirectoryServiceAccess |  |
| AADDomainServicesLogonLogoff |  |
| AADDomainServicesPolicyChange |  |
| AADDomainServicesPrivilegeUse |  |
| AADManagedIdentitySignInLogs |  |
| AADNonInteractiveUserSignInLogs |  |
| AADProvisioningLogs |  |
| AADRiskyUsers |  |
| AADServicePrincipalSignInLogs |  |
| AADUserRiskEvents |  |
| ABSBotRequests |  |
| ACSAuthIncomingOperations |  |
| ACSBillingUsage |  |
| ACSChatIncomingOperations |  |
| ACSSMSIncomingOperations |  |
| ADAssessmentRecommendation |  |
| ADFActivityRun |  |
| ADFPipelineRun |  |
| ADFSSignInLogs |  |
| ADFTriggerRun |  |
| ADPAudit |  |
| ADPDiagnostics |  |
| ADPRequests |  |
| ADReplicationResult |  |
| ADSecurityAssessmentRecommendation |  |
| ADTDigitalTwinsOperation |  |
| ADTEventRoutesOperation |  |
| ADTModelsOperation |  |
| ADTQueryOperation |  |
| ADXCommand |  |
| ADXQuery |  |
| AegDeliveryFailureLogs |  |
| AegPublishFailureLogs |  |
| AEWAuditLogs |  |
| Warnung |  |
| AmlOnlineEndpointConsoleLog |  |
| ApiManagementGatewayLogs |  |
| AppCenterError |  |
| AppPlatformSystemLogs |  |
| AppServiceAppLogs |  |
| AppServiceAuditLogs |  |
| AppServiceConsoleLogs |  |
| AppServiceFileAuditLogs |  |
| AppServiceHTTPLogs |  |
| AppServicePlatformLogs |  |
| AuditLogs |  |
| AutoscaleEvaluationsLog |  |
| AutoscaleScaleActionsLog |  |
| AWSCloudTrail |  |
| AWSGuardDuty |  |
| AWSVPCFlow |  |
| AzureAssessmentRecommendation |  |
| AzureDevOpsAuditing |  |
| BehaviorAnalytics |  |
| BlockchainApplicationLog |  |
| BlockchainProxyLog |  |
| CDBCassandraRequests |  |
| CDBControlPlaneRequests |  |
| CDBDataPlaneRequests |  |
| CDBGremlinRequests |  |
| CDBMongoRequests |  |
| CDBPartitionKeyRUConsumption |  |
| CDBPartitionKeyStatistics |  |
| CDBQueryRuntimeStatistics |  |
| CommonSecurityLog |  |
| ComputerGroup |  |
| ConfigurationData | Teilweise unterstützt: Einige Daten werden durch interne Dienste erfasst, die für den Export nicht unterstützt werden. Dieser Teil fehlt derzeit im Export. |
| ContainerImageInventory |  |
| ContainerInventory |  |
| ContainerLog |  |
| ContainerLogV2 |  |
| ContainerNodeInventory |  |
| ContainerServiceLog |  |
| CoreAzureBackup |  |
| DatabricksAccounts |  |
| DatabricksClusters |  |
| DatabricksDBFS |  |
| DatabricksInstancePools |  |
| DatabricksJobs |  |
| DatabricksNotebook |  |
| DatabricksSecrets |  |
| DatabricksSQLPermissions |  |
| DatabricksSSH |  |
| DatabricksWorkspace |  |
| DeviceFileEvents |  |
| DeviceNetworkEvents |  |
| DeviceNetworkInfo |  |
| DeviceProcessEvents |  |
| DeviceRegistryEvents |  |
| DnsEvents |  |
| DnsInventory |  |
| DummyHydrationFact |  |
| Dynamics365Activity |  |
| EmailAttachmentInfo |  |
| EmailEvents |  |
| EmailPostDeliveryEvents |  |
| EmailUrlInfo |  |
| Ereignis | Teilweise Unterstützung: Daten, die vom Log Analytics-Agent (MMA) oder vom Azure Monitor-Agent (AMA) eintreffen, werden beim Exportvorgang vollständig unterstützt. Daten, die über den Diagnoseerweiterungs-Agent eingehen, werden über den Speicher gesammelt. Dieser Pfad wird beim Export jedoch nicht unterstützt.2 |
| ExchangeAssessmentRecommendation |  |
| FailedIngestion |  |
| FunctionAppLogs |  |
| HDInsightAmbariClusterAlerts |  |
| HDInsightAmbariSystemMetrics |  |
| HDInsightHadoopAndYarnLogs |  |
| HDInsightHadoopAndYarnMetrics |  |
| HDInsightHBaseLogs |  |
| HDInsightHBaseMetrics |  |
| HDInsightHiveAndLLAPLogs |  |
| HDInsightHiveAndLLAPMetrics |  |
| HDInsightHiveTezAppStats |  |
| HDInsightJupyterNotebookEvents |  |
| HDInsightKafkaLogs |  |
| HDInsightKafkaMetrics |  |
| HDInsightOozieLogs |  |
| HDInsightSecurityLogs |  |
| HDInsightSparkApplicationEvents |  |
| HDInsightSparkBlockManagerEvents |  |
| HDInsightSparkEnvironmentEvents |  |
| HDInsightSparkExecutorEvents |  |
| HDInsightSparkJobEvents |  |
| HDInsightSparkLogs |  |
| HDInsightSparkSQLExecutionEvents |  |
| HDInsightSparkStageEvents |  |
| HDInsightSparkStageTaskAccumulables |  |
| HDInsightSparkTaskEvents |  |
| Heartbeat |  |
| HuntingBookmark |  |
| InsightsMetrics | Teilweise unterstützt: Einige Daten werden durch interne Dienste erfasst, die für den Export nicht unterstützt werden. Dieser Teil fehlt derzeit im Export. |
| IntuneAuditLogs |  |
| IntuneDevices |  |
| IntuneOperationalLogs |  |
| KubeEvents |  |
| KubeHealth |  |
| KubeMonAgentEvents |  |
| KubeNodeInventory |  |
| KubePodInventory |  |
| KubeServices |  |
| LAQueryLogs |  |
| McasShadowItReporting |  |
| MCCEventLogs |  |
| MicrosoftAzureBastionAuditLogs |  |
| MicrosoftDataShareReceivedSnapshotLog |  |
| MicrosoftDataShareSentSnapshotLog |  |
| MicrosoftDataShareShareLog |  |
| MicrosoftHealthcareApisAuditLogs |  |
| NWConnectionMonitorPathResult |  |
| NWConnectionMonitorTestResult |  |
| OfficeActivity | In Government-Clouds teilweise unterstützt: Einige der Daten werden über Webhooks von O365 in LA erfasst. Dieser Teil fehlt derzeit im Export. |
| Vorgang | Teilweise unterstützt: Einige Daten werden durch interne Dienste erfasst, die für den Export nicht unterstützt werden. Dieser Teil fehlt derzeit im Export. |
| Perf | Teilweise unterstützt: Zurzeit werden nur Windows-Leistungsdaten unterstützt. Linux-Leistungsdaten fehlen derzeit im Export. |
| PowerBIDatasetsWorkspace |  |
| PurviewScanStatusLogs |  |
| SCCMAssessmentRecommendation |  |
| SCOMAssessmentRecommendation |  |
| SecurityAlert |  |
| SecurityBaseline |  |
| SecurityBaselineSummary |  |
| SecurityCef |  |
| SecurityDetection |  |
| SecurityEvent | Teilweise Unterstützung: Daten, die vom Log Analytics-Agent (MMA) oder vom Azure Monitor-Agent (AMA) eintreffen, werden beim Exportvorgang vollständig unterstützt. Daten, die über den Diagnoseerweiterungs-Agent eingehen, werden über den Speicher gesammelt. Dieser Pfad wird beim Export jedoch nicht unterstützt.2 |
| SecurityIncident |  |
| SecurityIoTRawEvent |  |
| SecurityNestedRecommendation |  |
| SecurityRecommendation |  |
| SentinelHealth |  |
| SfBAssessmentRecommendation |  |
| SfBOnlineAssessmentRecommendation |  |
| SharePointOnlineAssessmentRecommendation |  |
| SignalRServiceDiagnosticLogs |  |
| SigninLogs |  |
| SPAssessmentRecommendation |  |
| SQLAssessmentRecommendation |  |
| SQLSecurityAuditEvents |  |
| SucceededIngestion |  |
| SynapseBigDataPoolApplicationsEnded |  |
| SynapseBuiltinSqlPoolRequestsEnded |  |
| SynapseGatewayApiRequests |  |
| SynapseIntegrationActivityRuns |  |
| SynapseIntegrationPipelineRuns |  |
| SynapseIntegrationTriggerRuns |  |
| SynapseRbacOperations |  |
| SynapseSqlPoolDmsWorkers |  |
| SynapseSqlPoolExecRequests |  |
| SynapseSqlPoolRequestSteps |  |
| SynapseSqlPoolSqlRequests |  |
| SynapseSqlPoolWaits |  |
| syslog | Teilweise Unterstützung: Daten, die vom Log Analytics-Agent (MMA) oder vom Azure Monitor-Agent (AMA) eintreffen, werden beim Exportvorgang vollständig unterstützt. Daten, die über den Diagnoseerweiterungs-Agent eingehen, werden über den Speicher gesammelt. Dieser Pfad wird beim Export jedoch nicht unterstützt.2 |
| ThreatIntelligenceIndicator |  |
| Aktualisieren | Teilweise unterstützt: Einige Daten werden durch interne Dienste erfasst, die für den Export nicht unterstützt werden. Dieser Teil fehlt derzeit im Export. |
| UpdateRunProgress |  |
| UpdateSummary |  |
| Verwendung |  |
| UserAccessAnalytics |  |
| UserPeerAnalytics |  |
| Watchlist |  |
| WindowsEvent |  |
| WindowsFirewall |  |
| WireData | Teilweise unterstützt: Einige Daten werden durch interne Dienste erfasst, die für den Export nicht unterstützt werden. Dieser Teil fehlt derzeit im Export. |
| WorkloadDiagnosticLogs |  |
| WVDAgentHealthStatus |  |
| WVDCheckpoints |  |
| WVDConnections |  |
| WVDErrors |  |
| WVDFeeds |  |
| WVDManagement |  |

## <a name="next-steps"></a>Nächste Schritte

- [Abfragen der exportierten Daten aus Azure Data Explorer](../logs/azure-data-explorer-query-storage.md)