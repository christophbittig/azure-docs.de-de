---
title: Überwachungslösungen in Azure Monitor | Microsoft-Dokumentation
description: Hier erhalten Sie Informationen zu den vorkonfigurierten Sammlungen von Logik-, Visualisierungs- und Datenerfassungsregeln für die verschiedenen Problembereiche.
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 10/16/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 57f5c3f3dace57b42ed5ac32e48c5d070517e03a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131059944"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Überwachungslösungen in Azure Monitor

> [!CAUTION]
> Überwachungslösungen befinden sich nicht mehr in der aktiven Entwicklung.  Die Ersatztechnologie heißt [Azure Monitor Insights](/azure/azure-monitor/monitor-reference#insights). Wir empfehlen Ihnen, die gewonnenen Erkenntnisse zu nutzen und keine neuen Instanzen von Lösungen einzusetzen.  

Überwachungslösungen in Azure Monitor ermöglichen eine Analyse des Betriebs einer Azure-Anwendung oder eines Azure-Diensts. Dieser Artikel gibt eine kurze Übersicht über Überwachungslösungen in Azure sowie Details zu deren Verwendung und Installation. 

Überwachungslösungen können Azure Monitor für beliebige Anwendungen und Dienste hinzugefügt werden, die Sie verwenden. Die Lösungen sind in der Regel kostenlos, aber durch ihre Datensammlung können Kosten entstehen.

## <a name="use-monitoring-solutions"></a>Verwenden von Überwachungslösungen

Auf der **Übersichtsseite** in Azure Monitor wird für jede in einem Log Analytics-Arbeitsbereich installierte Lösung jeweils eine Kachel angezeigt. Um diese Seite zu öffnen, navigieren Sie im [Azure-Portal](https://ms.portal.azure.com) zu **Azure Monitor**. Wählen Sie im Menü **Insights** die Option **Weitere** aus, um den **Insights-Hub** zu öffnen und klicken Sie dann auf **Log Analytics-Arbeitsbereiche**.

[![Ein Screenshot, der die Auswahlen für das Öffnen des Insights Hub](media/solutions/insights-hub.png)](media/solutions/insights-hub.png#lightbox) zeigt.


Ändern Sie über die Dropdownfelder am oberen Bildschirmrand den Arbeitsbereich oder den Zeitbereich, der für die Kacheln verwendet wird. Wählen Sie die Kachel für eine Lösung aus, um die dazugehörige Ansicht mit einer ausführlicheren Analyse ihrer gesammelten Daten anzuzeigen.

[![Ein Screenshot, der die Statistiken für die Überwachungslösungen im Azure-Portal](media/solutions/overview.png)](media/solutions/overview.png#lightbox) zeigt.

Die Überwachungslösungen können mehrere Arten von Azure-Ressourcen enthalten. Sie können alle Ressourcen, die in einer Lösung enthalten sind, wie jede andere Ressource anzeigen. Beispielsweise werden alle in der Lösung enthaltenen Protokollabfragen unter **Lösungsabfragen** im [Abfrage-Explorer](../logs/log-analytics-tutorial.md) aufgeführt. Sie können diese Abfragen bei der Durchführung von Ad-hoc-Analysen mit [Protokollabfragen](../logs/log-query-overview.md) verwenden.

## <a name="list-installed-monitoring-solutions"></a>Auflisten installierter Überwachungslösungen

### <a name="portal"></a>[Portal](#tab/portal)

So listen Sie die in Ihrem Abonnement installierten Überwachungslösungen auf:

1. Öffnen Sie das [Azure-Portal](https://ms.portal.azure.com). Suchen Sie nach **Lösungen**, und wählen Sie diese Option aus.

   Alle in Ihren Arbeitsbereichen installierten Lösungen werden aufgelistet. Auf den Namen der Lösung folgt der Name des Arbeitsbereichs, wo sie installiert ist.
1. Mit den Dropdownfeldern am oberen Bildschirmrand können Sie die Anzeige nach Abonnement oder Ressourcengruppe filtern.

![Screenshot: Auflistung der Lösungen.](media/solutions/list-solutions-all.png)

Wählen Sie den Namen einer Lösung, um die dazugehörige Zusammenfassungsseite zu öffnen. Auf dieser Seite werden alle in der Lösung enthaltenen Ansichten angezeigt und Optionen für die Lösung und den Arbeitsbereich bereitgestellt.

![Screenshot: Zusammenfassung der Informationen zu einer Lösung.](media/solutions/solution-properties.png)

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um die in Ihrem Abonnement installierten Überwachungslösungen aufzulisten, verwenden Sie den Befehl [az monitor log-analytics solution list](/cli/azure/monitor/log-analytics/solution#az_monitor_log_analytics_solution_list). Überprüfen Sie vor dem Ausführen des Befehls die Voraussetzungen in [Installieren einer Überwachungslösung](#install-a-monitoring-solution).

```azurecli
# List all log-analytics solutions in the current subscription.
az monitor log-analytics solution list

# List all log-analytics solutions for a specific subscription
az monitor log-analytics solution list --subscription MySubscription

# List all log-analytics solutions in a resource group
az monitor log-analytics solution list --resource-group MyResourceGroup
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Um die in Ihrem Abonnement installierten Überwachungslösungen anzuzeigen, verwenden Sie das Cmdlet [Get-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/get-azmonitorloganalyticssolution). Überprüfen Sie vor dem Ausführen von Cmdlet die Voraussetzungen in [Installieren einer Überwachungslösung](#install-a-monitoring-solution).

```azurepowershell-interactive
# List all Log Analytics solutions in the current subscription
Get-AzMonitorLogAnalyticsSolution

# List all Log Analytics solutions for a specific subscription
Get-AzMonitorLogAnalyticsSolution -SubscriptionId 00000000-0000-0000-0000-000000000000

# List all Log Analytics solutions in a resource group
Get-AzMonitorLogAnalyticsSolution -ResourceGroupName MyResourceGroup
```

* * *

## <a name="install-a-monitoring-solution"></a>Installieren einer Überwachungslösung

### <a name="portal"></a>[Portal](#tab/portal)

Die Überwachungslösungen von Microsoft und Partnern sind im [Azure Marketplace](https://azuremarketplace.microsoft.com) verfügbar. Sie können die verfügbaren Lösungen durchsuchen und sie mit dem folgenden Verfahren installieren. Wenn Sie eine Lösung installieren, müssen Sie einen [Log Analytics-Arbeitsbereich](../logs/manage-access.md) auswählen, in dem die Lösung installiert wird und in dem ihre Daten gesammelt werden.

1. Wählen Sie aus der [Liste mit den Lösungen für Ihr Abonnement](#list-installed-monitoring-solutions) die Option **Hinzufügen** aus.
1. Durchsuchen Sie eine Projektmappe oder suchen Sie sie. Sie können auch [diesen Suchlink](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions)verwenden.
1. Finden Sie die gewünschte Überwachungslösung, und lesen Sie ihre Beschreibung.
1. Wählen Sie **Erstellen**, um den Installationsprozess zu starten.
1. Wenn Sie aufgefordert werden, geben den Log Analytics-Arbeitsbereich an und stellen Sie die erforderliche Konfiguration für die Projektmappe bereit.

![Ein Screenshot, der die Lösungen in Azure Marketplace zeigt.](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>Installieren einer Lösung aus der Community

Mitglieder der Community können Verwaltungslösungen für Azure-Schnellstartvorlagen senden. Sie können diese Lösungen entweder direkt installieren oder die Vorlagen für eine spätere Installation herunterladen.

1. Führen Sie den unter [Log Analytics-Arbeitsbereich und Automation-Konto](#log-analytics-workspace-and-automation-account) beschriebenen Prozess aus, um einen Arbeitsbereich und ein Konto zu verknüpfen.
2. Gehen Sie zu [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/).
3. Suchen Sie nach einer Lösung, an der Sie interessiert sind.
4. Wählen Sie die Lösung aus den Ergebnissen aus, um ihre Details anzuzeigen.
5. Wählen Sie die Schaltfläche **In Azure bereitstellen**.
6. Sie werden aufgefordert, Informationen wie die Ressourcengruppe und den Standort sowie die Werte für die Parameter in der Lösung anzugeben.
7. Wählen Sie **Kaufen**, um die Lösung zu installieren.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

### <a name="prepare-your-environment"></a>Vorbereiten der Umgebung

1. Installieren Sie die Azure CLI.

   Vor dem Ausführen der CLI-Referenzbefehle müssen Sie die [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli) installieren. Auf Wunsch können Sie auch Azure Cloud Shell verwenden, um die Schritte in diesem Artikel auszuführen. Azure Cloud Shell ist eine interaktive Shellumgebung, die Sie über Ihren Browser nutzen können. Öffnen Sie Cloud Shell mit einer der folgenden Methoden:

   - Navigieren Sie zur [Webseite von Cloud Shell](https://shell.azure.com).

   - Wählen Sie im [Azure-Portal](https://portal.azure.com) rechts oben in der Menüleiste die Schaltfläche **Cloud Shell** aus. 

1. Melden Sie sich an.

   Melden Sie sich mit dem Befehl [az login](/cli/azure/reference-index#az_login) an, falls Sie eine lokale Installation der Befehlszeilenschnittstelle verwenden.  Führen Sie die in Ihrem Terminal angezeigten Schritte aus, um den Authentifizierungsprozess abzuschließen.

    ```azurecli
    az login
    ```

1. Installieren Sie die Erweiterung `log-analytics-solution`.

   Der Befehl `log-analytics-solution` ist eine experimentelle Erweiterung der eigentlichen Azure-Befehlszeilenschnittstelle. Weitere Informationen zu Erweiterungsverweisen finden Sie unter [Verwenden von Erweiterungen mit der Azure CLI](/cli/azure/azure-cli-extensions-overview?).

   ```azurecli
   az extension add --name log-analytics-solution
   ```

   Die folgende Warnung wird erwartet.

   ```output
   The installed extension `log-analytics-solution` is experimental and not covered by customer support.  Please use with discretion.
   ```

### <a name="install-a-solution-with-the-azure-cli"></a>Installieren einer Lösung mit der Azure-Befehlszeilenschnittstelle

Wenn Sie eine Lösung installieren, müssen Sie einen [Log Analytics-Arbeitsbereich](../logs/manage-access.md) auswählen, in dem die Lösung installiert wird und in dem ihre Daten gesammelt werden.  Mit der Azure-Befehlszeilenschnittstelle verwalten Sie Arbeitsbereiche mithilfe der Referenzbefehle [az monitor log-analytics workspace](/cli/azure/monitor/log-analytics/workspace).  Führen Sie den unter [Log Analytics-Arbeitsbereich und Automation-Konto](#log-analytics-workspace-and-automation-account) beschriebenen Prozess aus, um einen Arbeitsbereich und ein Konto zu verknüpfen.

Verwenden Sie den Befehl [Azure Monitor-Protokollanalysen erstellen](/cli/azure/monitor/log-analytics/solution), um eine Überwachungslösung zu installieren.  Parameter in eckigen Klammern sind optional.

```azurecli
az monitor log-analytics solution create --name
                                         --plan-product
                                         --plan-publisher
                                         --resource-group
                                         --workspace
                                         [--no-wait]
                                         [--tags]
```

Hier finden Sie ein Codebeispiel, mit dem eine Log Analytics-Lösung für das Planprodukt von OMSGallery/Containers erstellt wird.

```azurecli
az monitor log-analytics solution create --resource-group MyResourceGroup \
                                         --name Containers({SolutionName}) \
                                         --tags key=value \
                                         --plan-publisher Microsoft  \
                                         --plan-product "OMSGallery/Containers" \
                                         --workspace "/subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/ \
                                           Microsoft.OperationalInsights/workspaces/{WorkspaceName}"
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

### <a name="prepare-your-environment"></a>Vorbereiten der Umgebung

1. Installieren Sie Azure PowerShell.

   Vor dem Ausführen von PowerShell-Verweisbefehlen müssen Sie [Azure PowerShell installieren](/powershell/azure/install-az-ps). Wenn Sie möchten, können Sie auch Azure Cloud Shell verwenden, um die Schritte in diesem Artikel auszuführen. Azure Cloud Shell ist eine interaktive Shellumgebung, die Sie über Ihren Browser nutzen können. Öffnen Sie Cloud Shell mit einer der folgenden Methoden:

   - Navigieren Sie zur [Webseite von Cloud Shell](https://shell.azure.com).

   - Wählen Sie im [Azure-Portal](https://portal.azure.com) rechts oben in der Menüleiste die Schaltfläche **Cloud Shell** aus.

   > [!IMPORTANT]
   > Solange eine Vorschauversion des PowerShell-Moduls **Az.MonitoringSolutions** verfügbar ist, müssen Sie es separat mithilfe des Cmdlets `Install-Module` installieren. Sobald dieses PowerShell-Modul allgemein verfügbar ist, wird es in die zukünftigen Releases des Az PowerShell-Moduls integriert und in Azure Cloud Shell standardmäßig zur Verfügung gestellt.

   ```azurepowershell-interactive
   Install-Module -Name Az.MonitoringSolutions
   ```

1. Melden Sie sich an.

   Wenn Sie eine lokale Installation von PowerShell verwenden, melden Sie sich mithilfe des Cmdlets [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) an. Führen Sie die in PowerShell angezeigten Schritte aus, um den Authentifizierungsprozess abzuschließen.

   ```azurepowershell
   Connect-AzAccount
   ```

### <a name="install-a-solution-with-azure-powershell"></a>Installieren einer Lösung mit Azure PowerShell

Wenn Sie eine Lösung installieren, müssen Sie einen [Log Analytics-Arbeitsbereich](../logs/manage-access.md) auswählen, in dem die Lösung installiert wird und in dem ihre Daten gesammelt werden. Mit Azure PowerShell verwalten Sie Arbeitsbereiche mithilfe der Cmdlets im PowerShell-Modul [Az.MonitoringSolutions](/powershell/module/az.monitoringsolutions). Führen Sie den unter [Log Analytics-Arbeitsbereich und Automation-Konto](#log-analytics-workspace-and-automation-account) beschriebenen Prozess aus, um einen Arbeitsbereich und ein Konto zu verknüpfen.

Verwenden Sie das Cmdlet [New-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/new-azmonitorloganalyticssolution), um eine Überwachungslösung zu installieren. Parameter in eckigen Klammern sind optional.

```azurepowershell
New-AzMonitorLogAnalyticsSolution -ResourceGroupName <string> -Type <string> -Location <string>
-WorkspaceResourceId <string> [-SubscriptionId <string>] [-Tag <hashtable>]
[-DefaultProfile <psobject>] [-Break] [-HttpPipelineAppend <SendAsyncStep[]>]
[-HttpPipelinePrepend <SendAsyncStep[]>] [-Proxy <uri>] [-ProxyCredential <pscredential>]
[-ProxyUseDefaultCredentials] [-WhatIf] [-Confirm] [<CommonParameters>]
```

Im folgenden Beispiel wird eine Überwachungslösung für den Log Analytics-Arbeitsbereich erstellt.

```azurepowershell-interactive
$workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName MyResourceGroup -Name WorkspaceName
New-AzMonitorLogAnalyticsSolution -Type Containers -ResourceGroupName MyResourceGroup -Location $workspace.Location -WorkspaceResourceId $workspace.ResourceId
```

* * *

## <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics-Arbeitsbereich und Automation-Konto

Für alle Überwachungslösungen wird ein [Log Analytics-Arbeitsbereich](../logs/manage-access.md) zum Speichern der gesammelten Daten sowie zum Hosten der Protokollsuchvorgänge und Ansichten benötigt. Einige Lösungen erfordern auch ein [Automation-Konto](../../automation/automation-security-overview.md) für Runbooks und zugehörige Ressourcen. Der Arbeitsbereich und das Konto müssen den folgenden Anforderungen entsprechen:

* Eine Lösungsinstallation kann jeweils nur einen Log Analytics-Arbeitsbereich und ein Automation-Konto verwenden. Sie können die Lösung separat in mehreren Arbeitsbereichen installieren.
* Sollte eine Lösung ein Automation-Konto benötigen, müssen der Log Analytics-Arbeitsbereich und das Automation-Konto miteinander verknüpft werden. Ein Log Analytics-Arbeitsbereich kann nur mit einem Automation-Konto verknüpft werden. Ein Automation-Konto kann nur mit einem Log Analytics-Arbeitsbereich verknüpft werden.

Wenn Sie die Lösung über Azure Marketplace installieren, werden Sie zur Angabe eines Arbeitsbereichs und eines Automation-Kontos aufgefordert. Die Verknüpfung zwischen beiden wird für Sie erstellt, sofern sie noch nicht besteht.

Gehen Sie wie folgt vor, um die Verknüpfung zwischen einem Log Analytics-Arbeitsbereich und einem Automatisierungskonto zu überprüfen:

1. Wählen Sie im Azure-Portal das Automation-Konto aus.
1. Scrollen Sie zum Abschnitt **Zugehörige Ressourcen** des Menüs, und wählen Sie **Verknüpfter Arbeitsbereich** aus.
1. Wenn der Arbeitsbereich mit einem Automation-Konto verknüpft ist, wird auf dieser Seite der Arbeitsbereich aufgelistet, mit dem es verknüpft ist. Wenn Sie den Namen des aufgelisteten Arbeitsbereichs auswählen, werden Sie zur Übersichtsseite für diesen Arbeitsbereich weitergeleitet.

## <a name="remove-a-monitoring-solution"></a>Entfernen einer Überwachungslösung

### <a name="portal"></a>[Portal](#tab/portal)

Wenn Sie eine installierte Lösung über das Portal entfernen möchten, finden Sie sie in der [Liste mit den installierten Lösungen](#list-installed-monitoring-solutions). Wählen Sie den Namen der Lösung, um die dazugehörige Seite mit den Zusammenfassungen zu öffnen und wählen Sie dann **Löschen** aus.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie den Befehl [az monitor log-analytics solution delete](/cli/azure/monitor/log-analytics/solution#az_monitor_log_analytics_solution_delete), um eine installierte Lösung mithilfe der Azure-Befehlszeilenschnittstelle zu entfernen.

```azurecli
az monitor log-analytics solution delete --name
                                         --resource-group
                                         [--no-wait]
                                         [--yes]
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Verwenden Sie das Cmdlet [Remove-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/remove-azmonitorloganalyticssolution), um eine installierte Lösung mithilfe von Azure PowerShell zu entfernen.

```azurepowershell-interactive
Remove-AzMonitorLogAnalyticsSolution  -ResourceGroupName MyResourceGroup -Name WorkspaceName
```

* * *

## <a name="next-steps"></a>Nächste Schritte

* Rufen Sie eine [Liste mit Überwachungslösungen von Microsoft](../monitor-reference.md) ab.
* Machen Sie sich mit dem [Erstellen von Abfragen](../logs/log-query-overview.md) vertraut, um die von den Überwachungslösungen gesammelten Daten zu analysieren.
* Sehen Sie sich alle [Beispiel-CLI-Befehle für Azure Monitor](/cli/azure/azure-cli-reference-for-monitor) an.
