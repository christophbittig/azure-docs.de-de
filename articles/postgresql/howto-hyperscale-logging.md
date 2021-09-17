---
title: 'Protokolle: Hyperscale (Citus) – Azure Database for PostgreSQL'
description: 'Zugreifen auf Datenbankprotokolle für Azure Database for PostgreSQL: Hyperscale (Citus)'
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 8/20/2021
ms.openlocfilehash: c65d2947e6e0f9505f1827ec8dbb32f59855d332
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/21/2021
ms.locfileid: "122633894"
---
# <a name="logs-in-azure-database-for-postgresql---hyperscale-citus"></a>Protokolle in Azure Database for PostgreSQL: Hyperscale (Citus)

Protokolle der PostgreSQL-Datenbankserver sind für jeden Knoten einer Hyperscale (Citus)-Servergruppe verfügbar. Sie können Protokolle an einen Speicherserver oder einen Analysedienst senden. Die Protokolle dienen zur Identifizierung, Behebung und Reparatur von Konfigurationsfehlern und suboptimaler Leistung.

## <a name="capturing-logs"></a>Aufzeichnen von Protokollen

Um auf PostgreSQL-Protokolle für einen Koordinator- oder -Workerknoten für Hyperscale (Citus) zuzugreifen, müssen Sie die Diagnoseeinstellung PostgreSQLLogs aktivieren. Öffnen Sie im Azure-Portal **Diagnoseeinstellungen**, und wählen Sie **+ Diagnoseeinstellung hinzufügen** aus.

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-settings.png" alt-text="Schaltfläche „Diagnoseeinstellungen hinzufügen“":::

Wählen Sie einen Namen für die neuen Diagnoseeinstellungen aus, und aktivieren Sie das Feld **PostgreSQLLogs** und dann das Feld **Send to Log Analytics workspace** (An Log Analytics-Arbeitsbereich senden).  Klicken Sie dann auf **Speichern**.

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-create-setting.png" alt-text="PostgreSQL-Protokolle auswählen":::

## <a name="viewing-logs"></a>Anzeigen von Protokollen

Zum Anzeigen und Filtern der Protokolle werden hier Kusto-Abfragen verwendet. Wählen Sie im Azure-Portal die Option **Protokolle** für Ihre Hyperscale (Citus)-Servergruppe aus. Wenn ein Auswahldialogfeld für Abfragen angezeigt wird, schließen Sie es:

:::image type="content" source="media/howto-hyperscale-logging/logs-dialog.png" alt-text="Seite „Protokolle“ mit geöffnetem Dialogfeld":::

Anschließend wird ein Eingabefeld für die Eingabe von Abfragen angezeigt.

:::image type="content" source="media/howto-hyperscale-logging/logs-query.png" alt-text="Eingabefeld zum Abfragen von Protokollen":::

Geben Sie die folgende Abfrage ein, und wählen Sie die Schaltfläche **Ausführen** aus.

```kusto
AzureDiagnostics
| project TimeGenerated, Message, errorLevel_s, LogicalServerName_s
```

Mit der obigen Abfrage listen Sie Protokollmeldungen von allen Knoten zusammen mit ihrem Schweregrad und Zeitstempel auf. Sie können `where`-Klauseln hinzufügen, um die Ergebnisse zu filtern. Um beispielsweise nur Fehler vom Koordinatorknoten anzuzeigen, filtern Sie den Fehlergrad und den Servernamen wie hier:

```kusto
AzureDiagnostics
| project TimeGenerated, Message, errorLevel_s, LogicalServerName_s
| where LogicalServerName_s == 'example-server-group-c'
| where errorLevel_s == 'ERROR'
```

Ersetzen Sie den Servernamen im obigen Beispiel durch den Namen Ihres Servers. Der Name des Koordinatorknotens hat das Suffix `-c`, und die Namen von Workerknoten haben das Suffix `-w0`, `-w1` usw.

## <a name="next-steps"></a>Nächste Schritte

- [Erste Schritte mit Log Analytics-Abfragen](../azure-monitor/logs/log-analytics-tutorial.md)
- Weitere Informationen zu [Azure Event Hubs](../event-hubs/event-hubs-about.md)
