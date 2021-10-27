---
title: 'Tutorial: Konfigurieren von Überwachungsprotokollen mithilfe von Azure Database for MySQL Flexible Server'
description: In diesem Tutorial wird gezeigt, wie Sie Überwachungsprotokolle mithilfe von Azure Database for MySQL Flexible Server konfigurieren.
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: tutorial
ms.date: 10/01/2021
ms.openlocfilehash: 4a819814b8c5bb8cf6d4bea949463fddd05405a8
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130065861"
---
# <a name="tutorial-configure-audit-logs-by-using-azure-database-for-mysql-flexible-server"></a>Tutorial: Konfigurieren von Überwachungsprotokollen mithilfe von Azure Database for MySQL Flexible Server

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Sie können Azure Database for MySQL Flexible Server zum Konfigurieren von Überwachungsprotokollen verwenden. Überwachungsprotokolle können zum Nachverfolgen von Aktivitäten auf Datenbankebene verwendet werden. Zu diesen Aktivitäten zählen u. a. Ereignisse im Zusammenhang mit Verbindungen, der DDL (Data Definition Language, Datendefinitionssprache) und der DML (Data Manipulation Language, Datenbearbeitungssprache). Diese Arten von Protokollen werden häufig zu Compliancezwecken verwendet. Normalerweise verwenden Sie die Datenbanküberwachung für Folgendes:
* Erfassen aller Aktionen, die innerhalb eines bestimmten Schemas, einer bestimmten Tabelle oder einer bestimmten Zeile ausgeführt werden oder sich auf bestimmte Inhalte auswirken
* Verhindern, dass Benutzer (oder andere Personen) basierend auf ihrer Verantwortlichkeit nicht zulässige Aktionen ausführen
* Untersuchen verdächtiger Aktivitäten
* Überwachen und Sammeln von Daten zu bestimmten Datenbankaktivitäten
 
In diesem Artikel wird erläutert, wie Sie MySQL-Überwachungsprotokolle, Log Analytics-Tools oder eine Arbeitsmappenvorlage verwenden, um Überwachungsinformationen für Azure Database for MySQL Flexible Server zu visualisieren.

In diesem Tutorial lernen Sie Folgendes:
>[!div class="checklist"]
> * Konfigurieren der Überwachung mithilfe des Azure-Portals oder der Azure CLI
> * Einrichten der Diagnose
> * Anzeigen von Überwachungsprotokollen mithilfe von Log Analytics 
> * Anzeigen von Überwachungsprotokollen mithilfe von Arbeitsmappen  

## <a name="prerequisites"></a>Voraussetzungen

- [Erstellen einer Azure Database for MySQL Flexible Server-Instanz](./quickstart-create-server-portal.md)
- [Erstellen eines Log Analytics-Arbeitsbereichs](../../azure-monitor/logs/quick-create-workspace.md)

## <a name="configure-auditing-by-using-the-azure-portal"></a>Konfigurieren der Überwachung über das Azure-Portal 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Wählen Sie Ihre Flexible Server-Instanz aus.

1. Wählen Sie im Bereich auf der linken Seite unter **Einstellungen** die Option **Serverparameter** aus.

    :::image type="content" source="./media/tutorial-configure-audit/server-parameters.png" alt-text="Screenshot: Liste „Serverparameter“":::

1. Wählen Sie für den Parameter **audit_log_enabled** die Option **ON** aus.

    :::image type="content" source="./media/tutorial-configure-audit/audit-log-enabled.png" alt-text="Screenshot: Auf „ON“ festgelegter Parameter „audit_log_enabled":::

1. Wählen Sie für den Parameter **audit_log_events** in der Dropdownliste die zu protokollierenden [Ereignistypen](concepts-audit-logs.md#configure-audit-logging) aus.

    :::image type="content" source="./media/tutorial-configure-audit/audit-log-events.png" alt-text="Screenshot: Ereignisoptionen in der Dropdownliste „audit_log_events“":::

1. Geben Sie für die Parameter **audit_log_exclude_users** und **audit_log_include_users** alle MySQL-Benutzer an, die in die Protokollierung einbezogen bzw. von ihr ausgeschlossen werden sollen, indem Sie ihre MySQL-Benutzernamen eingeben.

    :::image type="content" source="./media/tutorial-configure-audit/audit-log-exclude-users.png" alt-text="Screenshot: MySQL-Benutzernamen, die in die Protokollierung einbezogen bzw. von ihr ausgeschlossen werden sollen":::

1. Wählen Sie **Speichern** aus.

    :::image type="content" source="./media/tutorial-configure-audit/save-parameters.png" alt-text="Screenshot: Schaltfläche „Speichern“ zum Speichern der Änderungen in den Parameterwerten":::


## <a name="configure-auditing-by-using-the-azure-cli"></a>Konfigurieren der Überwachung mithilfe der Azure CLI
 
Alternativ können Sie die Überwachung für Ihren flexiblen Server über die Azure CLI mit dem folgenden Befehl aktivieren und konfigurieren: 

```azurecli
# Enable audit logs
az mysql flexible-server parameter set \
--name audit_log_enabled \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value ON
```


## <a name="set-up-diagnostics"></a>Einrichten der Diagnose

Überwachungsprotokolle sind in die Diagnoseeinstellungen von Azure Monitor integriert, sodass Sie Ihre Protokolle an eine von drei Datensenken weiterleiten können:
* Einen Log Analytics-Arbeitsbereich.
* Einen Event Hub
* Ein Speicherkonto

>[!Note]
>Es wird empfohlen, die Datensenken zu erstellen, bevor Sie die Diagnoseeinstellungen konfigurieren. Sie können auf die Überwachungsprotokolle in den von Ihnen konfigurierten Datensenken zugreifen. Es kann bis zu 10 Minuten dauern, bis die Protokolle angezeigt werden.

1. Wählen Sie im linken Bereich unter **Überwachung** die Option **Diagnoseeinstellungen** aus.

1. Wählen Sie im Bereich **Diagnoseeinstellungen** den Eintrag **Diagnoseeinstellung hinzufügen** aus.

    :::image type="content" source="./media/tutorial-configure-audit/add-diagnostic-setting.png" alt-text="Screenshot: Link „Diagnoseeinstellung hinzufügen“ im Bereich „Diagnoseeinstellungen“":::

1. Geben Sie im Feld **Name** einen Name für die Diagnoseeinstellung ein.

    :::image type="content" source="./media/tutorial-configure-audit/configure-diagnostic-setting.png" alt-text="Screenshot: Bereich „Diagnoseeinstellungen“ zum Auswählen von Konfigurationsoptionen":::

1. Geben Sie an, an welche Ziele (Log Analytics-Arbeitsbereich, Event Hub oder Speicherkonto) die Überwachungsprotokolle gesendet werden sollen, indem Sie die entsprechenden Kontrollkästchen aktivieren.

    >[!Note]
    > In diesem Tutorial senden Sie die Überwachungsprotokolle an einen Log Analytics-Arbeitsbereich.
    
1. Aktivieren Sie unter **Protokoll** für den Protokolltyp das Kontrollkästchen **MySqlAuditLogs**.

1. Nachdem Sie die Datensenken konfiguriert haben, an die die Überwachungsprotokolle weitergeleitet werden sollen, wählen Sie **Speichern** aus.

    :::image type="content" source="./media/tutorial-configure-audit/save-diagnostic-setting.png" alt-text="Screenshot: Schaltfläche „Speichern“ oben im Bereich „Diagnoseeinstellungen“":::

## <a name="view-audit-logs-by-using-log-analytics"></a>Anzeigen von Überwachungsprotokollen mithilfe von Log Analytics 

1. Wählen Sie in Log Analytics im linken Bereich unter **Überwachung** die Option **Protokolle** aus.

1. Schließen Sie das Fenster **Abfragen**.  

   :::image type="content" source="./media/tutorial-configure-audit/log-query.png" alt-text="Screenshot: Log Analytics-Bereich „Abfragen“":::

1. Im Abfragefenster können Sie die auszuführende Abfrage schreiben. Beispielsweise wurde die folgende Abfrage verwendet, um eine Zusammenfassung der überwachten Ereignisse auf einem bestimmten Server zu ermitteln: 

    ```kusto
    AzureDiagnostics
        |where Category =='MySqlAuditLogs' 
        |project TimeGenerated, Resource, event_class_s, event_subclass_s, event_time_t, user_s ,ip_s , sql_text_s 
        |summarize count() by event_class_s,event_subclass_s 
        |order by event_class_s 
    ```
    
    :::image type="content" source="./media/tutorial-configure-audit/audit-query.png" alt-text="Screenshot: Log Analytics-Beispielabfrage zum Ermitteln einer Zusammenfassung der überwachten Ereignisse auf einem bestimmten Server":::

## <a name="view-audit-logs-by-using-workbooks"></a>Anzeigen von Überwachungsprotokollen mithilfe von Arbeitsmappen 
 
Die für die Überwachung verwendete Arbeitsmappenvorlage setzt voraus, dass Sie Diagnoseeinstellungen erstellen, um Plattformprotokolle zu senden. 

1. Wählen Sie in Azure Monitor im linken Bereich **Aktivitätsprotokoll** und anschließend **Diagnoseeinstellungen** aus. 

    :::image type="content" source="./media/tutorial-configure-audit/activity-diagnostics.png" alt-text="Screenshot: Registerkarte „Diagnoseeinstellungen“ im Azure Monitor-Bereich „Aktivitätsprotokoll“":::

1. Im Bereich **Diagnoseeinstellung** können Sie eine neue Einstellung hinzufügen oder eine vorhandene Einstellung bearbeiten. Jede Einstellung kann jeweils höchstens einen der Zieltypen aufweisen.

    :::image type="content" source="./media/tutorial-configure-audit/activity-settings-diagnostic.png" alt-text="Screenshot: Azure Monitor-Bereich „Diagnoseeinstellung“ zum Auswählen der Protokollziele":::

    > [!Note]
    > Sie können auf die Protokolle für langsame Abfragen in den Datensenken (Log Analytics-Arbeitsbereich, Speicherkonto oder Event Hub) zugreifen, die Sie bereits konfiguriert haben. Es kann bis zu 10 Minuten dauern, bis die Protokolle angezeigt werden.

1. Wählen Sie im Azure-Portal im linken Bereich unter **Überwachung** für Ihre Azure Database for MySQL Flexible Server-Instanz die Option **Arbeitsmappen** aus.
1. Wählen Sie die Arbeitsmappe **Überwachung** aus. 

    :::image type="content" source="./media/tutorial-configure-audit/monitor-workbooks.png" alt-text="Screenshot: Alle Arbeitsmappen im Arbeitsmappenkatalog":::
    
In der Arbeitsmappe können Sie die folgenden Visualisierungen anzeigen: 
>[!div class="checklist"]
> * Administrative Aktionen für den Dienst
> * Zusammenfassung der Überwachung
> * Zusammenfassung der Überwachungsverbindungsereignisse
> * Überwachungsverbindungsereignisse
> * Zusammenfassung des Tabellenzugriffs
> * Identifizierte Fehler


:::image type="content" source="./media/tutorial-configure-audit/admin-events.png" alt-text="Screenshot: Arbeitsmappenvorlage für administrative Aktionen für den Dienst":::

:::image type="content" source="./media/tutorial-configure-audit/audit-summary.png" alt-text="Screenshot: Arbeitsmappenvorlage „Verbindungsereignisse überwachen“":::

>[!Note]
> * Sie können diese Vorlagen auch entsprechend Ihren Anforderungen bearbeiten und anpassen. Weitere Informationen finden Sie im Abschnitt „Bearbeitungsmodus“ der [Übersicht über Azure Monitor-Arbeitsmappen](../../azure-monitor/visualize/workbooks-overview.md#editing-mode).
> * Zur Schnellansicht können Sie die Arbeitsmappen oder die Log Analytics-Abfrage auch an Ihr Dashboard anheften. Weitere Informationen finden Sie unter [Erstellen eines Dashboards im Azure-Portal](../../azure-portal/azure-portal-dashboards.md). 

In der Ansicht für *administrative Aktionen für den Dienst* finden Sie Details zu Aktivitäten, die für den Dienst ausgeführt werden. Mithilfe dieser Details können Sie für alle Schreibvorgänge (PUT, POST, DELETE) für die Ressourcen Ihres Abonnements ermitteln, *welcher Benutzer welchen Vorgang zu welchem Zeitpunkt* ausgeführt hat. 

Sie können weitere Visualisierungen verwenden, um die Details der Datenbankaktivität nachzuvollziehen. Die Datenbanksicherheit setzt sich aus vier Teilen zusammen: 
* **Serversicherheit:** Mit der Serversicherheit soll verhindert werden, dass unbefugte Personen auf die Datenbank zugreifen.  
* **Datenbankverbindung:** Der Administrator muss überprüfen, ob Datenbankupdates von autorisierten Mitarbeitern durchgeführt wurden.
* **Tabellenzugriffssteuerung:** Hier werden die Zugriffsschlüssel der autorisierten Benutzer und die Tabellen in der Datenbank angezeigt, die sie jeweils verarbeiten dürfen.
* **Einschränkung des Datenbankzugriffs:** Dies ist insbesondere für Benutzer wichtig, die eine Datenbank ins Internet hochgeladen haben, und verhindert, dass externe Quellen auf Ihre Datenbank zugreifen können. 

## <a name="next-steps"></a>Nächste Schritte
- [Erfahren Sie mehr über Azure Monitor-Arbeitsmappen](../../azure-monitor/visualize/workbooks-overview.md#visualizations) und ihre umfangreichen Visualisierungsoptionen.
- [Informieren Sie sich über Überwachungsprotokolle.](concepts-audit-logs.md)
