---
title: Überwachen von Azure Load Balancer
description: Hier erfahren Sie mehr zur Überwachung von Load Balancer.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.custom: subject-monitoring
ms.date: 06/29/2021
ms.openlocfilehash: 62885e889c64c40c051f838178f9a96d4ac4a8ee
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114291183"
---
# <a name="monitoring-load-balancer"></a>Überwachen von Load Balancer

Wenn Sie über unternehmenskritische Anwendungen und Geschäftsprozesse verfügen, die auf Azure-Ressourcen beruhen, sollten Sie Verfügbarkeit, Leistung und Betrieb dieser Ressourcen überwachen. 

In diesem Artikel werden die von Load Balancer generierten Überwachungsdaten erläutert. Load Balancer verwendet [Azure Monitor](../azure-monitor/overview.md). Wenn Sie nicht mit den Features von Azure Monitor vertraut sind, die Bestandteil aller Azure-Dienste sind, für die Azure Monitor verwendet wird, lesen Sie den Artikel [Überwachen von Azure-Ressourcen mit Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).

## <a name="load-balancer-insights"></a>Erkenntnisse aus Load Balancer

Für einige Dienste in Azure enthält das Azure-Portal ein spezielles, vorgefertigtes Überwachungsdashboard, das als Ausgangspunkt für die Überwachung Ihres Diensts dient. Diese speziellen Dashboards werden als „Insights“ (Erkenntnisse) bezeichnet.

Load Balancer-Erkenntnisse:

* Ansicht der funktionalen Abhängigkeiten
* Metrikdashboard
* Registerkarte „Übersicht“
* Registerkarten „Front-End-Verfügbarkeit“ und „Back-End-Verfügbarkeit“
* Registerkarte „Datendurchsatz“
* Flowverteilung
* Verbindungsmonitore
* Metrikdefinitionen

Weitere Informationen zu Load Balancer-Erkenntnissen finden Sie unter [Verwenden von Erkenntnissen zum Überwachen und Konfigurieren Ihrer Azure Load Balancer-Instanz](./load-balancer-insights.md).

## <a name="monitoring-data"></a>Überwachungsdaten 

Load Balancer erfasst dieselben Arten von Überwachungsdaten wie andere Azure-Ressourcen, die unter [Überwachungsdaten von Azure-Ressourcen](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data) beschrieben werden. 

Ausführliche Informationen zu den von Load Balancer erstellten Metriken und Protokollmetriken finden Sie in der [Referenz für Load Balancer-Überwachungsdaten](monitor-load-balancer.md).

Load Balancer stellt mit den folgenden Mitteln zusätzliche Überwachungsdaten bereit:

* [Integritätstests](./load-balancer-custom-probe-overview.md)
* [Ressourcenintegritätsstatus](./load-balancer-standard-diagnostics.md#resource-health-status)
* [REST-API](./load-balancer-query-metrics-rest-api.md)

## <a name="collection-and-routing"></a>Sammlung und Routing

Plattformmetriken und das Aktivitätsprotokoll werden automatisch erfasst und gespeichert, können jedoch mithilfe einer Diagnoseeinstellung an andere Speicherorte weitergeleitet werden.  

Ressourcenprotokolle werden erst erfasst und gespeichert, sobald Sie eine Diagnoseeinstellung erstellt und an einen oder mehrere Standorte weitergeleitet haben.

## <a name="creating-a-diagnostic-setting"></a>Erstellen einer Diagnoseeinstellung

Sie können eine Diagnoseeinstellung über das Azure-Portal, PowerShell oder die Azure CLI erstellen.


Eine allgemeine Anleitung finden Sie unter [Erstellen einer Diagnoseeinstellung zum Erfassen von Plattformprotokollen und Metriken in Azure](../azure-monitor/essentials/diagnostic-settings.md).

Wenn Sie eine Diagnoseeinstellung erstellen, legen Sie fest, welche Kategorien von Protokollen gesammelt werden sollen. Die Kategorie für Load Balancer lautet **AllMetrics**.

### <a name="portal"></a>Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)

2. Geben Sie am oberen Rand des Portals den Suchbegriff **Lastenausgleich** in das Suchfeld ein.

3. Wählen Sie in den Suchergebnissen **Lastenausgleichsmodule** aus.

4. Wählen Sie Ihren Lastenausgleich aus. Für dieses Beispiel wird **myLoadBalancer** verwendet.

5. Wählen Sie im Abschnitt **Überwachung** von **myLoadBalancer** die Option **Diagnoseeinstellungen** aus.

6. Wählen Sie auf der Seite **Diagnoseeinstellungen** den Eintrag **+ Diagnoseeinstellung hinzufügen** aus.

7. Geben Sie die folgenden Informationen für die **Diagnoseeinstellung** an, oder wählen Sie sie aus.

    | Einstellung | Wert |
    | ------- | ----- |
    | Name der Diagnoseeinstellung | Geben Sie einen Namen für die Diagnoseeinstellung ein. |
    | **Kategoriedetails** |   |
    | Metrik | Wählen Sie **AllMetrics** aus. |

8. Wählen Sie die **Zieldetails** aus. Auswahl der Zieloptionen:
    * **An Log Analytics senden**
        * Wählen Sie das **Abonnement** und den **Log Analytics-Arbeitsbereich** aus.
    * **In einem Speicherkonto archivieren**
        * Wählen Sie das **Abonnement** und das **Speicherkonto** aus.
    * **An einen Event Hub streamen**
        * Wählen Sie das **Abonnement**, den **Event Hub-Namespace**, den **Event Hub-Namen** (optional) und den **Event Hub-Richtliniennamen** aus.

9. Wählen Sie **Speichern** aus.

### <a name="powershell"></a>PowerShell

Melden Sie sich bei Azure PowerShell an:

```azurepowershell
Connect-AzAccount 
```

#### <a name="log-analytics-workspace"></a>Log Analytics-Arbeitsbereich

Geben Sie diese Befehle ein, um Diagnoseprotokolle für einen Log Analytics-Arbeitsbereich zu aktivieren. Ersetzen Sie die Werte in Klammern durch Ihre Werte:

```azurepowershell
## Place the load balancer in a variable. ##
$lbpara = @{
    ResourceGroupName = <your-resource-group-name>
    Name = <your-load-balancer-name>
}
$lb = Get-AzLoadBalancer @lbpara
    
## Place the workspace in a variable. ##
$wspara = @{
    ResourceGroupName = <your-resource-group-name>
    Name = <your-log-analytics-workspace-name>
}
$ws = Get-AzOperationalInsightsWorkspace @wspara
    
## Enable the diagnostic setting. ##
Set-AzDiagnosticSetting `
    -ResourceId $lb.id `
    -Name <your-diagnostic-setting-name> `
    -Enabled $true `
    -MetricCategory 'AllMetrics' `
    -WorkspaceId $ws.ResourceId
```

#### <a name="storage-account"></a>Speicherkonto

Geben Sie die folgenden Befehle ein, um Diagnoseprotokolle in einem Speicherkonto zu aktivieren. Ersetzen Sie die Werte in Klammern durch Ihre Werte:

```azurepowershell
## Place the load balancer in a variable. ##
$lbpara = @{
    ResourceGroupName = <your-resource-group-name>
    Name = <your-load-balancer-name>
}
$lb = Get-AzLoadBalancer @lbpara
    
## Place the storage account in a variable. ##
$storpara = @{
    ResourceGroupName = <your-resource-group-name>
    Name = <your-storage-account-name>
}
$storage = Get-AzStorageAccount @storpara
    
## Enable the diagnostic setting. ##
Set-AzDiagnosticSetting `
    -ResourceId $lb.id `
    -Name <your-diagnostic-setting-name> `
    -StorageAccountId $storage.id `
    -Enabled $true `
    -MetricCategory 'AllMetrics'
```

#### <a name="event-hub"></a>Event Hub

Geben Sie diese Befehle ein, um Diagnoseprotokolle für einen Event Hub-Namespace zu aktivieren. Ersetzen Sie die Werte in Klammern durch Ihre Werte:

```azurepowershell
## Place the load balancer in a variable. ##
$lbpara = @{
    ResourceGroupName = <your-resource-group-name>
    Name = <your-load-balancer-name>
}
$lb = Get-AzLoadBalancer @lbpara
    
## Place the event hub in a variable. ##
$hubpara = @{
    ResourceGroupName = <your-resource-group-name>
    Name = <your-event-hub-name>
}
$eventhub = Get-AzEventHubNamespace @hubpara

## Place the event hub authorization rule in a variable. ##    
$hubrule = @{
    ResourceGroupName = 'myResourceGroup'
    Namespace = 'myeventhub8675'
}
$eventhubrule = Get-AzEventHubAuthorizationRule @hubrule

## Enable the diagnostic setting. ##
Set-AzDiagnosticSetting `
    -ResourceId $lb.Id `
    -Name 'myDiagSetting-event'`
    -EventHubName $eventhub.Name `
    -EventHubAuthorizationRuleId $eventhubrule.Id `
    -Enabled $true `
    -MetricCategory 'AllMetrics'
```
### <a name="azure-cli"></a>Azure CLI

Anmelden bei der Azure CLI an:

```azurecli
az login
```

#### <a name="log-analytics-workspace"></a>Log Analytics-Arbeitsbereich

Geben Sie diese Befehle ein, um Diagnoseprotokolle für einen Log Analytics-Arbeitsbereich zu aktivieren. Ersetzen Sie die Werte in Klammern durch Ihre Werte:

```azurecli
lbid=$(az network lb show \
    --name <your-load-balancer-name> \
    --resource-group <your-resource-group> \
    --query id \
    --output tsv)

wsid=$(az monitor log-analytics workspace show \
    --resource-group <your-resource-group> \
    --workspace-name <your-log-analytics-workspace-name> \
    --query id \
    --output tsv)
    
az monitor diagnostic-settings create \
    --name <your-diagnostic-setting-name> \
    --resource $lbid \
    --metrics '[{"category": "AllMetrics","enabled": true}]' \
    --workspace $wsid
```

#### <a name="storage-account"></a>Speicherkonto

Geben Sie die folgenden Befehle ein, um Diagnoseprotokolle in einem Speicherkonto zu aktivieren. Ersetzen Sie die Werte in Klammern durch Ihre Werte:

```azurecli
lbid=$(az network lb show \
    --name <your-load-balancer-name> \
    --resource-group <your-resource-group> \
    --query id \
    --output tsv)

storid=$(az storage account show \
        --name <your-storage-account-name> \
        --resource-group <your-resource-group> \
        --query id \
        --output tsv)
    
az monitor diagnostic-settings create \
    --name <your-diagnostic-setting-name> \
    --resource $lbid \
    --metrics '[{"category": "AllMetrics","enabled": true}]' \
    --storage-account $storid
```

#### <a name="event-hub"></a>Event Hub

Geben Sie diese Befehle ein, um Diagnoseprotokolle für einen Event Hub-Namespace zu aktivieren. Ersetzen Sie die Werte in Klammern durch Ihre Werte:

```azurecli
lbid=$(az network lb show \
    --name <your-load-balancer-name> \
    --resource-group <your-resource-group> \
    --query id \
    --output tsv)

az monitor diagnostic-settings create \
    --name myDiagSetting-event \
    --resource $lbid \
    --metrics '[{"category": "AllMetrics","enabled": true}]' \
    --event-hub-rule /subscriptions/<your-subscription-id>/resourceGroups/<your-resource-group>/providers/Microsoft.EventHub/namespaces/<your-event-hub-namespace>/authorizationrules/RootManageSharedAccessKey
```

In den folgenden Abschnitten werden die Metriken und Protokolle behandelt, die Sie erfassen können.

## <a name="analyzing-metrics"></a>Analysieren von Metriken

Sie können im Metrik-Explorer Metriken für Load Balancer mithilfe von Metriken aus anderen Azure-Diensten analysieren, indem Sie im Menü **Azure Monitor** die Option **Metriken** öffnen. Ausführliche Informationen zur Verwendung dieses Tools finden Sie unter [Erste Schritte mit dem Azure-Metrik-Explorer](../azure-monitor/essentials/metrics-getting-started.md). 

Eine Liste der Plattformmetriken, die für die Load Balancer gesammelt wurden, finden Sie unter [Referenzmetriken zur Überwachung von Load Balancer-Daten](monitor-load-balancer-reference.md#metrics).  

Sie können zur Referenz auf eine Liste [aller in Azure Monitor unterstützter Ressourcenmetriken](../azure-monitor/essentials/metrics-supported.md) anzeigen.

## <a name="analyzing-logs"></a>Analysieren von Protokollen

Daten in Azure Monitor-Protokollen werden in Tabellen gespeichert, wobei jede Tabelle ihren eigenen Satz an eindeutigen Eigenschaften hat.  

Das [Aktivitätsprotokoll](../azure-monitor/essentials/activity-log.md) ist eine Art von Plattformprotokoll, das Erkenntnisse zu Ereignissen auf Abonnementebene liefert. Sie können es unabhängig anzeigen oder an Azure Monitor-Protokolle weiterleiten, in denen Sie mithilfe von Log Analytics viel komplexere Abfragen durchführen können.  

Eine Liste der Tabellen, die von Azure Monitor-Protokollen verwendet und von Log Analytics abgefragt werden können, finden Sie in der [Referenz zur Überwachung von Load Balancer-Daten](monitor-load-balancer-reference.md#azure-monitor-logs-tables).  

### <a name="sample-kusto-queries"></a>Kusto-Beispielabfragen

> [!NOTE]
> Derzeit liegt ein Problem mit Kusto-Abfragen vor, das verhindert, dass Daten aus Load Balancer-Protokollen abgerufen werden.


## <a name="alerts"></a>Alerts

Azure Monitor-Warnungen informieren Sie proaktiv, wenn wichtige Bedingungen in Ihren Überwachungsdaten gefunden werden. Sie ermöglichen Ihnen, Probleme in Ihrem System zu identifizieren und zu beheben, bevor Ihre Kunden sie bemerken. Sie können Warnungen für [Metriken](../azure-monitor/alerts/alerts-metric-overview.md), [Protokolle](../azure-monitor/alerts/alerts-unified-log.md) und das [Aktivitätsprotokoll](../azure-monitor/alerts/activity-log-alerts.md) festlegen. Verschiedene Arten von Warnungen haben jeweils ihre Vor- und Nachteile.

Wenn Sie eine Anwendung erstellen oder ausführen, die Load Balancer verwendet, bietet [Azure Monitor Application Insights](../azure-monitor/overview.md#application-insights) möglicherweise zusätzliche Warnungen.


In der folgenden Tabelle sind gängige und empfohlene Warnungsregeln für Load Balancer aufgeführt.

| Warnungstyp | Bedingung | BESCHREIBUNG  |
|:---|:---|:---|
| Lastenausgleichsregel aufgrund nicht verfügbarer VMs nicht verfügbar | Wenn die Verfügbarkeit des Datenpfads nach Front-End-IP-Adresse und Front-End-Port (alle bekannten und zukünftigen Werte) und der Status des Integritätstests gleich 0 (null) sind, werden Warnungen auslösen. | Diese Warnung bestimmt, ob die Datenpfadverfügbarkeit für konfigurierte Lastenausgleichsregeln keinen Datenverkehr verarbeitet, da alle VMs im zugeordneten Back-End-Pool durch den konfigurierten Integritätstest heruntergefahren werden. Lesen Sie den [Leitfaden zur Problembehandlung](load-balancer-troubleshoot.md) für Load Balancer, um die potenzielle Grundursache zu untersuchen. |
| VM-Verfügbarkeit erheblich reduziert | Wenn der nach Back-End-IP-Adresse und Back-End-Port aufgeteilte Status des Integritätstests dem benutzerdefinierten Prozentsatz für die Gesamtgröße des Pools entspricht (d. h. 25 % werden untersucht), wird eine Warnung ausgelöst. | Diese Warnung bestimmt, ob weniger VMs als für die Verarbeitung des Datenverkehrs benötigt verfügbar sind. |
| Fehler bei ausgehenden Verbindungen mit dem Internetendpunkt | Wenn „Anzahl von SNAT-Verbindungen“ nach „Verbindungsstatus = Fehler“ gefiltert wird und das Ergebnis größer als 0 (null) ist, wird eine Warnung ausgelöst. | Diese Warnung wird ausgelöst, wenn die SNAT-Ports erschöpft sind und VMs keine ausgehenden Verbindungen initiieren können. |
| Bevorstehende SNAT-Erschöpfung | Wenn „Verwendete SNAT-Ports“ größer als die benutzerdefinierte Zahl ist, wird eine Warnung ausgelöst. | Für diese Warnung ist eine statische ausgehende Konfiguration erforderlich, bei der immer die gleiche Anzahl von Ports zugeordnet wird. Sie wird dann ausgelöst, wenn ein gewisser Prozentsatz der zugeordneten Ports verwendet wird. |

## <a name="next-steps"></a>Nächste Schritte

- Eine Referenz zu den Metriken, Protokollen und anderen wichtigen Werten, die von Load Balancer erstellt werden, finden Sie in der [Referenz zur Überwachung von Load Balancer-Daten](monitor-load-balancer-reference.md).
- Ausführliche Informationen zur Überwachung von Azure-Ressourcen finden Sie unter [Überwachen von Azure-Ressourcen mit Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).