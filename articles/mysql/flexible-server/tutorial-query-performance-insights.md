---
title: 'Tutorial: Query Performance Insight für Azure Database for MySQL Flexible Server'
description: In diesem Artikel werden die Tools beschrieben, mit denen Sie Query Performance Insight für Azure Database for MySQL Flexible Server visualisieren können.
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: tutorial
ms.date: 10/01/2021
ms.openlocfilehash: 63cec8fa00af3e4711c4c6383c68cc09d32ce3d3
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130065709"
---
# <a name="tutorial-query-performance-insight-for-azure-database-for-mysql-flexible-server"></a>Tutorial: Query Performance Insight für Azure Database for MySQL Flexible Server
[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Query Performance Insight ermöglicht eine intelligente Abfrageanalyse für Datenbanken. Zu den bevorzugten Erkenntnissen zählen die Workloadmuster und zeitintensiveren Abfragen. Anhand dieser Erkenntnisse können Sie ermitteln, welche Abfragen optimiert werden müssen, um die Gesamtleistung zu verbessern und Ihre verfügbaren Ressourcen effizient zu nutzen. 

Query Performance Insight soll den Zeitaufwand für die Problembehandlung der Datenbankleistung reduzieren. Dazu stellt das Tool u. a. folgende Informationen bereit:
* Erste *N* zeitintensive Abfragen und ihr Trend
* Abfragedetails: Zeigen Sie den Abfragetext sowie den Ausführungsverlauf mit minimaler, maximaler und durchschnittlicher Abfragedauer sowie die Standardabweichung der Abfragedauer an.
* Ressourcenverwendung (CPU, Arbeitsspeicher und Speicher)
 
In diesem Artikel wird erläutert, wie Sie MySQL-Protokolle für langsame Abfragen, das Log Analytics-Tool und Arbeitsmappenvorlagen verwenden, um Query Performance Insight für Azure Database for MySQL Flexible Server zu visualisieren.

In diesem Tutorial lernen Sie Folgendes:
>[!div class="checklist"]
> * Konfigurieren der Protokolle für langsame Abfragen mithilfe des Azure-Portals oder der Azure CLI
> * Einrichten der Diagnose
> * Anzeigen der Protokolle für langsame Abfragen mithilfe von Log Analytics 
> * Anzeigen der Protokolle für langsame Abfragen mithilfe Arbeitsmappen 

## <a name="prerequisites"></a>Voraussetzungen

- [Erstellen einer Azure Database for MySQL Flexible Server-Instanz](./quickstart-create-server-portal.md)
- [Erstellen eines Log Analytics-Arbeitsbereichs](../../azure-monitor/logs/quick-create-workspace.md)


## <a name="configure-slow-query-logs-by-using-the-azure-portal"></a>Konfigurieren von Protokollen für langsame Abfragen mithilfe des Azure-Portals 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Wählen Sie Ihre Flexible Server-Instanz aus.

1. Wählen Sie im Bereich auf der linken Seite unter **Einstellungen** die Option **Serverparameter** aus.

   :::image type="content" source="./media//tutorial-query-performance-insights/server-parameters.png" alt-text="Screenshot: Liste „Serverparameter“":::

1. Wählen Sie für den Parameter **slow_query_log** die Option **ON** aus.

   :::image type="content" source="./media/tutorial-query-performance-insights/slow-query-log-enable.png" alt-text="Screenshot: Auf „ON“ festgelegter Parameter „slow_query_log“":::

1. Informationen zu den anderen Parametern (etwa **long_query_time** und **log_slow_admin_statements**) finden Sie in der Dokumentation zu [Protokollen für langsame Abfragen](./concepts-slow-query-logs.md#configure-slow-query-logging).  

   :::image type="content" source="./media/tutorial-query-performance-insights/long-query-time.png" alt-text="Screenshot: Aktualisierte Werte für die übrigen Parameter im Zusammenhang mit Protokollen für langsame Abfragen":::

1. Wählen Sie **Speichern** aus. 

   :::image type="content" source="./media/tutorial-query-performance-insights/save-parameters.png" alt-text="Screenshot: Schaltfläche „Speichern“ zum Speichern der Änderungen in den Parameterwerten":::

Sie können zur Liste der Protokolle zurückkehren, indem Sie die Seite **Serverparameter** schließen.

## <a name="configure-slow-query-logs-by-using-the-azure-cli"></a>Konfigurieren von Protokollen für langsame Abfragen mithilfe der Azure CLI
 
Alternativ können Sie Protokolle für langsame Abfragen für Ihren flexiblen Server über die Azure CLI mit dem folgenden Befehl aktivieren und konfigurieren: 

> [!IMPORTANT]
> Es wird empfohlen, nur die Ereignistypen und Benutzer zu protokollieren, die für Ihre Überwachungszwecke erforderlich sind, um sicherzustellen, dass die Leistung Ihres flexiblen Servers nicht stark beeinträchtigt wird.

```azurecli
# Turn on statement level log.

az mysql flexible-server parameter set \
--name log_statement \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value all


# Set log_min_duration_statement time to 10 seconds. This setting will log all queries that execute for more than 10 seconds. Adjust this threshold based on your definition for slow queries.

az mysql server configuration set \
--name log_min_duration_statement \
--resource-group myresourcegroup \
--server mydemoserver \
--value 10000

# Enable slow query logs.

az mysql flexible-server parameter set \
--name slow_query_log \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value ON
```

## <a name="set-up-diagnostics"></a>Einrichten der Diagnose

Protokolle für langsame Abfragen sind in die Diagnoseeinstellungen von Azure Monitor integriert, sodass Sie Ihre Protokolle an eine von drei Datensenken weiterleiten können:
* Einen Log Analytics-Arbeitsbereich.
* Einen Event Hub
* Ein Speicherkonto

>[!Note]
>Es wird empfohlen, die Datensenken zu erstellen, bevor Sie die Diagnoseeinstellungen konfigurieren. Sie können auf die Protokolle für langsame Abfragen in den von Ihnen konfigurierten Datensenken zugreifen. Es kann bis zu 10 Minuten dauern, bis die Protokolle angezeigt werden.

1. Wählen Sie im linken Bereich unter **Überwachung** die Option **Diagnoseeinstellungen** aus.

1. Wählen Sie im Bereich **Diagnoseeinstellungen** den Eintrag **Diagnoseeinstellung hinzufügen** aus.

   :::image type="content" source="./media/tutorial-query-performance-insights/add-diagnostic-setting.png" alt-text="Screenshot: Link „Diagnoseeinstellung hinzufügen“ im Bereich „Diagnoseeinstellungen“":::

1. Geben Sie im Feld **Name** einen Name für die Diagnoseeinstellung ein.

1. Geben Sie an, an welche Ziele (Log Analytics-Arbeitsbereich, Event Hub oder Speicherkonto) die Protokolle für langsame Abfragen gesendet werden sollen, indem Sie die entsprechenden Kontrollkästchen aktivieren.

    >[!Note]
    > In diesem Tutorial senden Sie die Protokolle für langsame Abfragen an einen Log Analytics-Arbeitsbereich.

1. Aktivieren Sie unter **Protokoll** für den Protokolltyp das Kontrollkästchen **MySqlSlowLogs**.

    :::image type="content" source="./media/tutorial-query-performance-insights/configure-diagnostic-setting.png" alt-text="Screenshot: Bereich „Diagnoseeinstellungen“ zum Auswählen von Konfigurationsoptionen":::

1. Nachdem Sie die Datensenken für die langsamen Abfrageprotokolle konfiguriert haben, wählen Sie **Speichern** aus.

    :::image type="content" source="./media/tutorial-query-performance-insights/save-diagnostic-setting.png" alt-text="Screenshot der Konfigurationsoptionen für Diagnoseeinstellungen mit hervorgehobener Option „Speichern“":::

## <a name="view-query-insights-by-using-log-analytics"></a>Anzeigen von Abfrageerkenntnissen mithilfe von Log Analytics 

1. Wählen Sie in Log Analytics im linken Bereich unter **Überwachung** die Option **Protokolle** aus.

1. Schließen Sie das geöffnete Fenster **Abfragen**.

   :::image type="content" source="./media/tutorial-query-performance-insights/log-query.png" alt-text="Screenshot: Log Analytics-Bereich „Abfragen“":::

1. Im Abfragefenster können Sie die auszuführende Abfrage schreiben. Zum Ermitteln von Abfragen auf einem bestimmten Server, die länger als zehn Sekunden dauern, wurde der folgende Code verwendet:

   ```kusto
   AzureDiagnostics
      | where Category == 'MySqlSlowLogs'
      | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
      | where query_time_d > 10
   ```
    
1. Wählen Sie einen Wert für **Zeitbereich** aus, und führen Sie die Abfrage aus. Die Ergebnisse sind in der folgenden Abbildung dargestellt:  

   :::image type="content" source="./media/tutorial-query-performance-insights/slow-query.png" alt-text="Screenshot: Protokoll für langsame Abfrage":::

## <a name="view-query-insights-by-using-workbooks"></a>Anzeigen von Abfrageerkenntnissen mithilfe von Arbeitsmappen 

1. Wählen Sie im Azure-Portal im linken Bereich unter **Überwachung** für Ihre Azure Database for MySQL Flexible Server-Instanz die Option **Arbeitsmappen** aus.

1.  Wählen Sie die Vorlage **Query Performance Insight** aus. 

    :::image type="content" source="./media/tutorial-query-performance-insights/monitor-workbooks.png" alt-text="Screenshot: Alle Arbeitsmappen im Arbeitsmappenkatalog":::

In der Arbeitsmappe können Sie die folgenden Visualisierungen anzeigen: 
>[!div class="checklist"]
> * Abfragelast
> * Aktive Verbindungen gesamt
> * Trend langsamer Abfragen (Abfragezeit >10 Sekunden)
> * Details zu langsamen Abfragen
> * 5 längste Abfragen auflisten
> * Zusammenfassen langsamer Abfragen nach minimaler, maximaler und durchschnittlicher Abfragezeit sowie der Abfragezeit mit Standardabweichung

    
:::image type="content" source="./media/tutorial-query-performance-insights/long-query.png" alt-text="Screenshot: Zwei lange Abfragen":::

>[!Note]
> * Zum Anzeigen der Ressourcenverwendung können Sie die Vorlage „Übersicht“ verwenden.
> * Sie können diese Vorlagen auch entsprechend Ihren Anforderungen bearbeiten und anpassen. Weitere Informationen finden Sie unter [Azure Monitor-Arbeitsmappen: Übersicht](../../azure-monitor/visualize/workbooks-overview.md#editing-mode).
> * Zur Schnellansicht können Sie die Arbeitsmappen oder die Log Analytics-Abfrage auch an Ihr Dashboard anheften. Weitere Informationen finden Sie unter [Erstellen eines Dashboards im Azure-Portal](../../azure-portal/azure-portal-dashboards.md). 

In Query Performance Insight können Ihnen die folgenden zwei Metriken helfen, potenzielle Engpässe zu finden: *Dauer* und *Anzahl von Ausführungen*. Abfragen mit langer Ausführungsdauer besitzen das größte Potenzial, Ressourcen länger zu blockieren, andere Benutzer zu blockieren und die Skalierbarkeit einzuschränken. 

In einigen Fällen kann eine hohe Anzahl von Ausführungen mehr Netzwerkroundtrips nach sich ziehen. Roundtrips beeinträchtigen die Leistung. Sie richten sich nach der Netzwerklatenz und der Serverlatenz bei Downstreams. Daher kann die Anzahl von Ausführungen helfen, häufig ausgeführte Abfragen zu ermitteln. Diese Abfragen sind auch die besten Kandidaten für eine Optimierung. 

## <a name="next-steps"></a>Nächste Schritte
- [Erfahren Sie mehr über Azure Monitor-Arbeitsmappen](../../azure-monitor/visualize/workbooks-overview.md#visualizations) und ihre umfangreichen Visualisierungsoptionen.
- [Erfahren Sie mehr über Protokolle für langsame Abfragen.](concepts-slow-query-logs.md)


