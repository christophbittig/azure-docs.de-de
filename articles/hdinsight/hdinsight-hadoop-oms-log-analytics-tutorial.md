---
title: Verwenden von Azure Monitor-Protokollen zum Überwachen von Azure HDInsight-Clustern
description: Erfahren Sie, wie Sie Azure Monitor-Protokolle zum Überwachen von Aufträgen verwenden, die in einem HDInsight-Cluster ausgeführt werden.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-azurepowershell, references_regions
ms.date: 09/21/2021
ms.openlocfilehash: c4fc351105c82213549fdb357d19b480c5a51ed4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128648029"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Verwenden von Azure Monitor-Protokollen zum Überwachen von HDInsight-Clustern

In diesem Artikel erfahren Sie, wie Sie Azure Monitor-Protokolle zum Überwachen von Hadoop-Clustervorgängen in HDInsight verwenden. Außerdem erfahren Sie, wie Sie eine HDInsight-Überwachungslösung hinzufügen.

Bei [Azure Monitor-Protokollen](../azure-monitor/logs/log-query-overview.md) handelt es sich um einen Azure Monitor-Dienst, der Ihre Cloud- und lokalen Umgebungen überwacht. Die Überwachung dient der Aufrechterhaltung der Verfügbarkeit und Leistung Ihrer Umgebungen. Der Dienst sammelt Daten, die von Ressourcen in Ihrer Cloud- und in Ihrer lokalen Umgebung generiert werden, sowie von anderen Überwachungstools. Die Daten werden verwendet, um Analysen für mehrere Quellen bereitstellen zu können.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

#### <a name="new-azure-monitor-experience"></a>[Neue Azure Monitor-Erfahrung](#tab/new)

> [!Important]
> Die neue Azure Monitor-Erfahrung ist nur in „USA, Osten“ und „Europa, Westen“ als Previewfunktion verfügbar.  
>

## <a name="prerequisites"></a>Voraussetzungen

* Einen Log Analytics-Arbeitsbereich Sie können sich diesen Arbeitsbereich als einzigartige Azure Monitor-Protokollumgebung mit eigenem Datenrepository, eigenen Datenquellen und eigenen Lösungen vorstellen. Eine Anleitung hierzu finden Sie unter [Erste Schritte mit einem Log Analytics-Arbeitsbereich](../azure-monitor/vm/monitor-virtual-machine.md).

* Ein Azure HDInsight-Cluster. Derzeit können Sie Azure Monitor-Protokolle mit den folgenden HDInsight-Clustertypen verwenden:

  * Hadoop
  * hbase
  * Interactive Query
  * Kafka
  * Spark
  * Storm

  Anweisungen zum Erstellen von HDInsight-Clustern finden Sie unter [Hadoop-Tutorial: Erste Schritte bei der Verwendung von Hadoop in HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).  

* Bei Verwendung von PowerShell benötigen Sie das [Az-Modul](/powershell/azure/). Stellen Sie sicher, dass Sie über die aktuelle Version verfügen. Führen Sie gegebenenfalls `Update-Module -Name Az` aus.

* Wenn Sie die Azure-Befehlszeilenschnittstelle verwenden möchten, diese aber noch nicht installiert haben, lesen Sie [Installieren der Azure CLI](/cli/azure/install-azure-cli).

> [!NOTE]  
> Die neue Azure Monitor-Erfahrung ist nur in „USA, Osten“ und „Europa, Westen“ als Previewfunktion verfügbar. Es wird empfohlen, sowohl den HDInsight-Cluster als auch den Log Analytics-Arbeitsbereich in derselben Region anzuordnen, um die Leistung zu erhöhen. Azure Monitor-Protokolle sind nicht in allen Azure-Regionen verfügbar.
>

## <a name="enable-azure-monitor-using-the-portal"></a>Aktivieren von Azure Monitor über das Portal

In diesem Abschnitt konfigurieren Sie einen vorhandenen HDInsight Hadoop-Cluster zur Verwendung eines Azure Log Analytics-Arbeitsbereichs, z. B. zum Überwachen von Aufträgen und Debugprotokollen.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihren Cluster aus. Der Cluster wird auf einer neuen Portalseite geöffnet.

2. Wählen Sie links unter „Überwachung“ die  **Monitor-Integration** aus. 

3. Wählen Sie in der Hauptansicht unter  **Azure Monitor für HDInsight-Clusterintegration** die Option  **Aktivieren** aus. 

4. Wählen Sie in der Dropdownliste  **Arbeitsbereich auswählen**  einen vorhandenen Log Analytics-Arbeitsbereich aus. 

5. Wählen Sie **Speichern** aus. Das Speichern der Einstellung dauert einige Zeit. 

    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-azure-monitor.png" alt-text="Aktivieren der Überwachung für HDInsight-Cluster":::

Wenn Sie Azure Monitor deaktivieren möchten, können Sie dies auch in diesem Portal tun. 

## <a name="enable-azure-monitor-using-azure-powershell"></a>Aktivieren von Azure Monitor mit Azure PowerShell

Sie können die Azure Monitor-Protokolle im Azure PowerShell Az-Modul mit dem Cmdlet [Enable-AzHDInsightAzureMonitor](/powershell/module/az.hdinsight/enable-azhdinsightazuremonitor?view=azps-6.2.1&preserve-view=true) aktivieren.

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables monitoring and relevant logs will be sent to the specified workspace.
Enable-AzHDInsightAzureMonitor `
    -ResourceGroupName $resourceGroup `
    -ClusterName $cluster `
    -WorkspaceId $WorkspaceId `
    -PrimaryKey $PrimaryKey

# Gets the status of monitoring installation on the cluster.
Get-AzHDInsightAzureMonitor `
    -ResourceGroupName $resourceGroup `
    -ClusterName $cluster
```

Zum Deaktivieren verwenden Sie das Cmdlet [Disable-AzHDInsightAzureMonitor](/powershell/module/az.hdinsight/disable-azhdinsightazuremonitor?view=azps-6.2.1&preserve-view=true):

```powershell
Disable-AzHDInsightAzureMonitor -ResourceGroupName $resourceGroup `
-ClusterName $cluster
```

## <a name="enable-azure-monitor-using-azure-cli"></a>Aktivieren von Azure Monitor mit der Azure-Befehlszeilenschnittstelle

Sie können Azure Monitor-Protokolle mit dem Azure CLI-Befehl [`az hdinsight azure-monitor enable`](/cli/azure/hdinsight/azure-monitor?view=azure-cli-latest&preserve-view=true) aktivieren.

```azurecli
# set variables
export resourceGroup=RESOURCEGROUPNAME
export cluster=CLUSTERNAME
export LAW=LOGANALYTICSWORKSPACENAME

# Enable the Azure Monitor logs integration on an HDInsight cluster.
az hdinsight azure-monitor enable --name $cluster --resource-group $resourceGroup --workspace $LAW

# Get the status of Azure Monitor logs integration on an HDInsight cluster.
az hdinsight azure-monitor show --name $cluster --resource-group $resourceGroup
```

Zum Deaktivieren verwenden Sie den Befehl [`az hdinsight monitor disable`](/cli/azure/hdinsight/monitor#az_hdinsight_monitor_disable).

```azurecli
az hdinsight azure-monitor disable --name $cluster --resource-group $resourceGroup
```

## <a name="use-hdinsight-out-of-box-insights-to-monitor-a-single-cluster"></a>Verwenden der sofort einsatzbereiten HDInsight-Erkenntnisse zum Überwachen eines einzelnen Clusters

HDInsight bietet workloadspezifische Arbeitsmappen, mit denen Sie schnell Erkenntnisse gewinnen können. Diese Arbeitsmappe erfasst wichtige Leistungsmetriken aus Ihrem HDInsight-Cluster und stellt die Visualisierungen und Dashboards für die gängigsten Szenarien bereit. Die vordefinierten Erkenntnisse bieten eine vollständige Ansicht eines einzelnen HDInsight-Clusters, einschließlich Ressourcennutzung und Anwendungsstatus.

Verfügbare HDInsight-Arbeitsmappen:

- HDInsight Spark-Arbeitsmappe
- HDInsight Kafka-Arbeitsmappe
- HDInsight HBase-Arbeitsmappe
- HDInsight Hive/LLAP-Arbeitsmappe
- HDInsight Storm-Arbeitsmappe

Screenshot der Spark-Arbeitsmappe :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-spark-workbook.png" alt-text="Screenshot: Arbeitsmappe „Spark“":::

## <a name="use-at-scale-insights-to-monitor-multiple-clusters"></a>Verwenden von Erkenntnissen im großen Stil zur Überwachung mehrerer Cluster

Sie können sich beim Azure-Portal anmelden und „Überwachung“ auswählen. Wählen Sie im Abschnitt **Insights** die Option **Insights-Hub** aus. Anschließend suchen Sie HDInsight-Cluster.

In dieser Ansicht können Sie mehrere HDInsight-Cluster an einem Ort überwachen.
    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-monitor-insights.png" alt-text="Screenshot: Clustermonitor-Erkenntnisse":::

Sie können das Abonnement und die HDInsight-Cluster auswählen, die Sie überwachen möchten. 
 - Unter **Überwachte Cluster** wird die Anzahl der Cluster angezeigt, für die Sie Azure Monitor-Integration aktiviert haben.
 - Unter **Nicht überwachte Cluster** wird die Anzahl der Cluster angezeigt, für die Sie Azure Monitor-Integration nicht aktiviert haben.

Die Detailclusterliste lässt sich in jedem Abschnitt anzeigen. 

Auf der Registerkarte **Übersicht** unter **Überwachte Cluster** können Sie den Clustertyp, kritische Warnungen und Ressourcenauslastungen anzeigen.
    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-cluster-alerts.png" alt-text="Screenshot: Warnungen des Clustermonitors":::

Außerdem können Sie die Cluster in jedem Workloadtyp anzeigen, einschließlich Spark, HBase, Hive, Kafka und Storm.

Die übergeordneten Metriken der einzelnen Workloadtypen werden dargestellt, einschließlich der Anzahl aktiver Knoten-Manager, der Anzahl ausgeführter Anwendungen usw.

:::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-tutorial/spark-metrics.png" alt-text="Clustermonitor: Spark-Metriken":::

## <a name="configuring-performance-counters"></a>Konfigurieren von Leistungsindikatoren

Azure Monitor unterstützt das Sammeln und Analysieren von Leistungsmetriken für die Knoten in Ihrem Cluster. Weitere Informationen finden Sie unter [Leistungsindikatoren von Linux](../azure-monitor/agents/data-sources-performance-counters.md#linux-performance-counters).

## <a name="cluster-auditing"></a>Clusterüberwachung

HDInsight unterstützt die Clusterüberwachung mit Azure Monitor-Protokollen, indem die folgenden Protokolltypen importiert werden:

* `log_gateway_audit_CL`: Diese Tabelle enthält Überwachungsprotokolle von Clustergatewayknoten, in denen erfolgreiche und fehlerhafte Anmeldeversuche aufgezeichnet sind.
* `log_auth_CL`: Diese Tabelle enthält SSH-Protokolle mit erfolgreichen und fehlerhaften Anmeldeversuchen.
* `log_ambari_audit_CL`: Diese Tabelle enthält Überwachungsprotokolle von Ambari.
* `log_ranger_audti_CL`: Diese Tabelle enthält Überwachungsprotokolle von Apache Ranger auf Clustern mit Enterprise-Sicherheitspaket.


#### <a name="classic-azure-monitor-experience"></a>[Klassische Azure Monitor-Erfahrung](#tab/previous)

## <a name="prerequisites"></a>Voraussetzungen

* Einen Log Analytics-Arbeitsbereich Sie können sich diesen Arbeitsbereich als einzigartige Azure Monitor-Protokollumgebung mit eigenem Datenrepository, eigenen Datenquellen und eigenen Lösungen vorstellen. Eine Anleitung hierzu finden Sie unter [Erste Schritte mit einem Log Analytics-Arbeitsbereich](../azure-monitor/vm/monitor-virtual-machine.md).

* Wenn Sie beabsichtigen, Azure Monitor in einem Cluster hinter einer Firewall zu verwenden, führen Sie die [Voraussetzungen für Cluster hinter einer Firewall](#oms-with-firewall) aus.

* Ein Azure HDInsight-Cluster. Derzeit können Sie Azure Monitor-Protokolle mit den folgenden HDInsight-Clustertypen verwenden:

  * Hadoop
  * hbase
  * Interactive Query
  * Kafka
  * Spark
  * Storm

  Anweisungen zum Erstellen von HDInsight-Clustern finden Sie unter [Hadoop-Tutorial: Erste Schritte bei der Verwendung von Hadoop in HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).  

* Bei Verwendung von PowerShell benötigen Sie das [Az-Modul](/powershell/azure/). Stellen Sie sicher, dass Sie über die aktuelle Version verfügen. Führen Sie gegebenenfalls `Update-Module -Name Az` aus.

* Wenn Sie die Azure-Befehlszeilenschnittstelle verwenden möchten, diese aber noch nicht installiert haben, lesen Sie [Installieren der Azure CLI](/cli/azure/install-azure-cli).

> [!NOTE]  
> Es wird empfohlen, sowohl den HDInsight-Cluster als auch den Log Analytics-Arbeitsbereich in derselben Region anzuordnen, um die Leistung zu erhöhen. Azure Monitor-Protokolle sind nicht in allen Azure-Regionen verfügbar.

## <a name="enable-azure-monitor-using-the-portal"></a>Aktivieren von Azure Monitor über das Portal

In diesem Abschnitt konfigurieren Sie einen vorhandenen HDInsight Hadoop-Cluster zur Verwendung eines Azure Log Analytics-Arbeitsbereichs, z. B. zum Überwachen von Aufträgen und Debugprotokollen.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihren Cluster aus. Der Cluster wird auf einer neuen Portalseite geöffnet.

1. Wählen Sie links unter **Überwachung** die Option **Azure Monitor** aus.

1. Wählen Sie in der Hauptansicht unter **Azure Monitor-Integration** die Option **Aktivieren** aus.

1. Wählen Sie in der Dropdownliste **Arbeitsbereich auswählen** einen vorhandenen Log Analytics-Arbeitsbereich aus.

1. Wählen Sie **Speichern** aus.  Das Speichern der Einstellung dauert einige Zeit.

    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-tutorial/azure-portal-monitoring.png" alt-text="Aktivieren der Überwachung für HDInsight-Cluster":::

## <a name="enable-azure-monitor-using-azure-powershell"></a>Aktivieren von Azure Monitor mit Azure PowerShell

Sie können die Azure Monitor-Protokolle im Azure PowerShell Az-Modul mit dem Cmdlet [Enable-AzHDInsightMonitoring](/powershell/module/az.hdinsight/enable-azhdinsightmonitoring) aktivieren.

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables monitoring and relevant logs will be sent to the specified workspace.
Enable-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster `
    -WorkspaceId $WorkspaceId `
    -PrimaryKey $PrimaryKey

# Gets the status of monitoring installation on the cluster.
Get-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster
```

Zum Deaktivieren verwenden Sie das Cmdlet [Disable-AzHDInsightMonitoring](/powershell/module/az.hdinsight/disable-azhdinsightmonitoring):

```powershell
Disable-AzHDInsightMonitoring -Name "<your-cluster>"
```

## <a name="enable-azure-monitor-using-azure-cli"></a>Aktivieren von Azure Monitor mit der Azure-Befehlszeilenschnittstelle

Sie können Azure Monitor-Protokolle mit dem Azure CLI-Befehl `[az hdinsight monitor enable`](/cli/azure/hdinsight/monitor#az_hdinsight_monitor_enable) aktivieren.

```azurecli
# set variables
export resourceGroup=RESOURCEGROUPNAME
export cluster=CLUSTERNAME
export LAW=LOGANALYTICSWORKSPACENAME

# Enable the Azure Monitor logs integration on an HDInsight cluster.
az hdinsight monitor enable --name $cluster --resource-group $resourceGroup --workspace $LAW

# Get the status of Azure Monitor logs integration on an HDInsight cluster.
az hdinsight monitor show --name $cluster --resource-group $resourceGroup
```

Zum Deaktivieren verwenden Sie den Befehl [`az hdinsight monitor disable`](/cli/azure/hdinsight/monitor#az_hdinsight_monitor_disable).

```azurecli
az hdinsight monitor disable --name $cluster --resource-group $resourceGroup
```
## <a name=""></a><a name="oms-with-firewall">Voraussetzungen für Cluster hinter einer Firewall</a>

Damit sie die Azure Monitor-Integration in HDInsight hinter einer Firewall erfolgreich einrichten können, müssen einige Kunden möglicherweise die folgenden Endpunkte aktivieren:

|Agent-Ressource | Ports | Direction | Umgehung der HTTPS-Überprüfung |
|---|---|---|---|
| \*.ods.opinsights.azure.com | Port 443 | Ausgehend | Ja |
| \*.oms.opinsights.azure.com |Port 443 | Ausgehend | Ja |
| \*.azure-automation.net | Port 443 | Ausgehend | Ja |

Wenn Sicherheitseinschränkungen in Bezug auf die Aktivierung von Platzhalterspeicherendpunkten vorliegen, gibt es eine alternative Option. Sie können stattdessen wie folgt vorgehen:

1. Erstellen Sie ein dediziertes Speicherkonto.
2. Konfigurieren Sie das dedizierte Speicherkonto in ihrem Arbeitsbereich für die Protokollanalyse.
3. Aktivieren Sie das dedizierte Speicherkonto in ihrer Firewall.

### <a name="data-collection-behind-a-firewall"></a>Datensammlung hinter einer Firewall
Sobald das Setup erfolgreich ausgeführt wurde, ist es wichtig, die erforderlichen Endpunkte für die Datenerfassung zu aktivieren. Es wird empfohlen, den Endpunkt „\*.blob.core.windows.net“ zu aktivieren, damit die Datenerfassung erfolgreich ist.


## <a name="install-hdinsight-cluster-management-solutions"></a>Installieren von HDInsight-Clusterverwaltungslösungen

HDInsight bietet clusterspezifische Verwaltungslösungen, die Sie zu Azure Monitor-Protokollen hinzufügen können. [Verwaltungslösungen](../azure-monitor/insights/solutions.md) erweitern den Funktionsumfang von Azure Monitor-Protokollen und stellen mehr Daten und Analysetools bereit. Diese Lösungen sammeln wichtige Leistungsmetriken aus Ihren HDInsight-Clustern. Außerdem stellen sie die Tools bereit, um die Metriken durchsuchen zu können. Außerdem bieten diese Lösungen Visualisierungen und Dashboards für die meisten in HDInsight unterstützten Clustertypen. Anhand der mit der Lösung erfassten Kennzahlen können Sie benutzerdefinierte Überwachungsregeln und -warnungen erstellen.

Verfügbare HDInsight-Lösungen:

* HDInsight Hadoop-Überwachung
* HDInsight HBase-Überwachung
* HDInsight Interactive Query-Überwachung
* HDInsight Kafka-Überwachung
* HDInsight Spark-Überwachung
* HDInsight Storm-Überwachung

Eine Anleitung für eine Verwaltungslösung finden Sie unter [Installieren einer Überwachungslösung](../azure-monitor/insights/solutions.md#install-a-monitoring-solution). Installieren Sie zum Experimentieren eine HDInsight Hadoop-Überwachungslösung. Wenn der Vorgang abgeschlossen ist, wird unter **Zusammenfassung** die Kachel **HDInsightHadoop** angezeigt. Wählen Sie die Kachel **HDInsightHadoop** aus. Die HDInsightHadoop-Lösung sieht wie folgt aus:

:::image type="content" source="media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png" alt-text="HDInsight-Überwachungslösung – Ansicht":::

Im Bericht werden keine Aktivitäten angezeigt, da es sich um einen brandneuen Cluster handelt.

## <a name="configuring-performance-counters"></a>Konfigurieren von Leistungsindikatoren

Azure Monitor unterstützt das Sammeln und Analysieren von Leistungsmetriken für die Knoten in Ihrem Cluster. Weitere Informationen finden Sie unter [Leistungsindikatoren von Linux](../azure-monitor/agents/data-sources-performance-counters.md#linux-performance-counters).

## <a name="cluster-auditing"></a>Clusterüberwachung

HDInsight unterstützt die Clusterüberwachung mit Azure Monitor-Protokollen, indem die folgenden Protokolltypen importiert werden:

* `log_gateway_audit_CL`: Diese Tabelle enthält Überwachungsprotokolle von Clustergatewayknoten, in denen erfolgreiche und fehlerhafte Anmeldeversuche aufgezeichnet sind.
* `log_auth_CL`: Diese Tabelle enthält SSH-Protokolle mit erfolgreichen und fehlerhaften Anmeldeversuchen.
* `log_ambari_audit_CL`: Diese Tabelle enthält Überwachungsprotokolle von Ambari.
* `log_ranger_audti_CL`: Diese Tabelle enthält Überwachungsprotokolle von Apache Ranger auf Clustern mit Enterprise-Sicherheitspaket.

---

## <a name="update-the-log-analytics-oms-agent-used-by-hdinsight-azure-monitor-integration"></a>Aktualisieren des von der HDInsight Azure Monitor Integration verwendeten Log Analytics-Agents (OMS)

Wenn Azure Monitor Integration in einem Cluster aktiviert ist, wird der Log Analytics-Agent oder OMS-Agent (Operations Management Suite) im Cluster installiert und nicht aktualisiert, es sei denn, Sie deaktivieren und aktivieren Azure Monitor Integration erneut. Führen Sie die folgenden Schritte aus, wenn Sie den OMS-Agent im Cluster aktualisieren müssen. Wenn Sie sich hinter einer Firewall befinden, müssen Sie möglicherweise die [Voraussetzungen für Cluster hinter einer Firewall](#oms-with-firewall) erfüllen, bevor Sie diese Schritte ausführen.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihren Cluster aus. Der Cluster wird auf einer neuen Portalseite geöffnet.
1. Wählen Sie links unter **Überwachung** die Option **Azure Monitor** aus.
1. Notieren Sie sich den Namen Ihres aktuellen Log Analytics-Arbeitsbereichs.
1. Deaktivieren Sie in der Hauptansicht unter **Azure Monitor Integration** den Umschalter, und wählen Sie dann **Speichern** aus. 
1. Nachdem die Einstellung gespeichert wurde, aktivieren Sie den Umschalter für **Azure Monitor Integration** erneut, und stellen Sie sicher, dass derselbe Log Analytics-Arbeitsbereich ausgewählt ist, und wählen Sie dann **Speichern** aus.

Wenn Sie Azure Monitor Integration in einem Cluster aktiviert haben, wird beim Aktualisieren des OMS-Agents auch die OMI-Version (Open Management Infrastructure) aktualisiert. Sie können die OMI-Version im Cluster überprüfen, indem Sie den folgenden Befehl ausführen: 

```
 sudo /opt/omi/bin/omiserver –version
```

## <a name="next-steps"></a>Nächste Schritte

* [Abfragen von Azure Monitor-Protokollen zum Überwachen von HDInsight-Clustern](hdinsight-hadoop-oms-log-analytics-use-queries.md)
* [Überwachen der Clusterverfügbarkeit mit Apache Ambari und Azure Monitor-Protokollen](./hdinsight-cluster-availability.md)
