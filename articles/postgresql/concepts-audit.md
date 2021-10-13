---
title: Überwachungsprotokollierung in Azure Database for PostgreSQL (Einzelserver)
description: Konzepte für die pgAudit-Überwachungsprotokollierung in Azure Database for PostgreSQL (Einzelserver).
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 8a60c1db1ca3b3037aded6ed7d7a88f237edfe4e
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129458698"
---
# <a name="audit-logging-in-azure-database-for-postgresql---single-server"></a>Überwachungsprotokollierung in Azure Database for PostgreSQL (Einzelserver)

Die Überwachungsprotokollierung von Datenbankaktivitäten in Azure Database for PostgreSQL (Einzelserver) ist über die PostgreSQL-Überwachungserweiterung [pgAudit](https://www.pgaudit.org/) verfügbar. Die pgAudit-Erweiterung bietet detaillierte Sitzungs- und Objektüberwachungsprotokollierung.

> [!NOTE]
> Die pgAudit-Erweiterung befindet sich in Azure Database for PostgreSQL in der Vorschau. Sie kann nur auf universellen und arbeitsspeicheroptimierten Servern aktiviert werden.

Wenn Sie Azure-Protokolle auf Ressourcenebene für Vorgänge wie Compute- und Speicherskalierung erfassen möchten, lesen Sie [Übersicht über Protokolle der Azure-Plattform](../azure-monitor/essentials/platform-logs-overview.md).

## <a name="usage-considerations"></a>Überlegungen zur Verwendung

Standardmäßig werden pgAudit-Protokollanweisungen zusammen mit Ihren regulären Protokollanweisungen mithilfe der Postgres-Standardprotokollierungsfunktion ausgegeben. In Azure Database for PostgreSQL können diese LOG-Dateien über das Azure-Portal oder die Azure-Befehlszeilenschnittstelle heruntergeladen werden. Der maximale Speicher für die Sammlung von Dateien beträgt 1 GB. Jede Datei ist maximal sieben Tage lang verfügbar. Der Standardwert ist drei Tage. Bei diesem Dienst handelt es sich um eine kurzfristige Speicheroption.

Alternativ können Sie alle Protokolle so konfigurieren, dass sie an den Azure Monitor-Protokollspeicher für spätere Analysen in Log Analytics gesendet werden. Wenn Sie die Monitor-Ressourcenprotokollierung aktivieren, werden Ihre Protokolle abhängig von Ihrer Wahl automatisch im JSON-Format an Azure Storage, Azure Event Hubs oder Monitor-Protokolle gesendet.

Durch das Aktivieren von pgAudit wird ein großes Volumen von Protokollierung auf einem Server generiert, was Auswirkungen auf die Leistung und den Protokollspeicher hat. Es wird empfohlen, Monitor-Protokolle zu verwenden, die langfristigere Speicheroptionen sowie Analyse- und Benachrichtigungsfunktionen bieten. Deaktivieren Sie die Standardprotokollierung, um die Auswirkungen der zusätzlichen Protokollierung auf die Leistung zu reduzieren:

   1. Legen Sie den Parameter `logging_collector` auf **OFF** (AUS) fest.
   1. Starten Sie den Server neu, um diese Änderung zu übernehmen.

Informationen zum Einrichten der Protokollierung für Storage, Event Hubs oder Monitor-Protokolle finden Sie im Abschnitt „Ressourcenprotokolle“ unter [Protokolle in Azure Database for PostgreSQL – Einzelserver](concepts-server-logs.md).

## <a name="install-pgaudit"></a>Installieren von pgAudit

Um pgAudit installieren zu können, müssen Sie es in die freigegebenen im Voraus geladenen Bibliotheken des Servers einbeziehen. Damit eine Änderung des Postgres-Parameters `shared_preload_libraries` wirksam wird, ist ein Serverneustart erforderlich. Sie können Parameter mithilfe des [Portals](howto-configure-server-parameters-using-portal.md), der [Befehlszeilenschnittstelle](howto-configure-server-parameters-using-cli.md) oder der [REST-API](/rest/api/postgresql/singleserver/configurations/createorupdate) ändern.

So verwenden Sie das [Portal](https://portal.azure.com)

   1. Wählen Sie Ihre Azure Database for PostgreSQL-Server aus.
   1. Wählen Sie links unter **Einstellungen** die Option **Serverparameter** aus.
   1. Suchen Sie nach **shared_preload_libraries**.
   1. Wählen Sie **PGAUDIT** aus.
   
      :::image type="content" source="./media/concepts-audit/share-preload-parameter.png" alt-text="Screenshot: Azure Database for PostgreSQL: Aktivieren von shared_preload_libraries für PGAUDIT":::

   1. Starten Sie den Server neu, um die Änderung zu übernehmen.
   1. Vergewissern Sie sich, dass `pgaudit` in `shared_preload_libraries` geladen ist, indem Sie die folgende Abfrage in psql ausführen:
   
        ```SQL
      show shared_preload_libraries;
      ```
      Sie sollten `pgaudit` im Abfrageergebnis sehen, das `shared_preload_libraries` zurückgibt.

   1. Verbinden Sie Ihren Server mithilfe eines Clients wie psql, und aktivieren Sie die pgAudit-Erweiterung:
   
      ```SQL
      CREATE EXTENSION pgaudit;
      ```

> [!TIP]
> Wenn ein Fehler angezeigt wird, vergewissern Sie sich, dass Sie nach dem Speichern von `shared_preload_libraries` Ihren Server neu gestartet haben.

## <a name="pgaudit-settings"></a>pgAudit-Einstellungen

Mit pgAudit können Sie die Sitzungs- oder Objektüberwachungsprotokollierung konfigurieren. Bei der [Sitzungsüberwachungsprotokollierung](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) werden detaillierte Protokolle von ausgeführten Anweisungen ausgegeben. Bei der [Objektüberwachungsprotokollierung](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) ist die Überwachung auf bestimmte Beziehungen gerichtet. Sie können auswählen, ob Sie einen oder beide Protokollierungstypen einrichten möchten.

> [!NOTE]
> Die Einstellungen von pgAudit werden global angegeben, nicht auf Datenbank- oder Rollenebene.

Nachdem Sie [pgAudit installiert haben](#install-pgaudit), können Sie die zugehörigen Parameter so konfigurieren, dass die Protokollierung gestartet wird.

So konfigurieren Sie pgAudit im [Portal](https://portal.azure.com)

   1. Wählen Sie Ihre Azure Database for PostgreSQL-Server aus.
   1. Wählen Sie links unter **Einstellungen** die Option **Serverparameter** aus.
   1. Suchen Sie nach den **pg_audit**-Parametern.
   1. Wählen Sie die entsprechenden Einstellungsparameter aus, die bearbeitet werden sollen. Legen Sie beispielsweise zum Starten der Protokollierung **pgaudit.log** auf **WRITE** fest.
   
       :::image type="content" source="./media/concepts-audit/pgaudit-config.png" alt-text="Screenshot: Azure Database for PostgreSQL – Konfigurieren der Protokollierung mit pgAudit":::
   1. Wählen Sie **Speichern**, um Ihre Änderungen zu speichern.

Die [pgAudit-Dokumentation](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) stellt die Definition der einzelnen Parameter bereit. Testen Sie zuerst die Parameter, und vergewissern Sie sich, dass sie das erwartete Verhalten aufweisen. Beispiel:

- Wenn die Einstellung **pgaudit.log_client** aktiviert wird, werden Protokolle an einen Clientprozess wie psql umgeleitet, anstatt in eine Datei geschrieben zu werden. Lassen Sie diese Einstellung im Allgemeinen deaktiviert.
- Der Parameter **pgaudit.log_level** wird nur aktiviert, wenn **pgaudit.log_client** aktiviert ist.

> [!NOTE]
> In Azure Database for PostgreSQL kann **pgaudit.log** nicht mit einer Minuszeichenverknüpfung (`-`) festgelegt werden, wie in der pgAudit-Dokumentation beschrieben. Alle erforderlichen Anweisungsklassen, wie READ und WRITE, sollten einzeln angegeben werden.

### <a name="audit-log-format"></a>Format der Überwachungsprotokolle

Jeder Überwachungseintrag wird durch `AUDIT:` am Anfang der Protokollzeile gekennzeichnet. Das Format des restlichen Eintrags wird in der [pgAudit-Dokumentation](https://github.com/pgaudit/pgaudit/blob/master/README.md#format) ausführlich erläutert.

Wenn Sie weitere Felder benötigen, um Ihre Überwachungsanforderungen zu erfüllen, verwenden Sie den Postgres-Parameter `log_line_prefix`. Die Zeichenfolge `log_line_prefix` wird am Anfang jeder Postgres-Protokollzeile ausgegeben. Die folgende `log_line_prefix`-Einstellung stellt beispielsweise den Zeitstempel, den Benutzernamen, den Datenbanknamen und die Prozess-ID bereit:

```
t=%m u=%u db=%d pid=[%p]:
```

Weitere Informationen zu `log_line_prefix` finden Sie in der [PostgreSQL-Dokumentation](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LINE-PREFIX).

### <a name="get-started"></a>Erste Schritte

Legen Sie für den schnellen Einstieg **pgaudit.log** auf **WRITE** fest. Öffnen Sie dann Ihre Protokolle, um die Ausgabe zu überprüfen.

## <a name="view-audit-logs"></a>Anzeigen von Überwachungsprotokollen

Wenn Sie LOG-Dateien verwenden, sind Ihre Überwachungsprotokolle in derselben Datei enthalten wie Ihre PostgreSQL-Fehlerprotokolle. Sie können Protokolldateien über das [Portal](howto-configure-server-logs-in-portal.md) oder die [Befehlszeilenschnittstelle](howto-configure-server-logs-using-cli.md) herunterladen.

Wenn Sie Azure-Ressourcenprotokollierung verwenden, hängt die Art und Weise, wie Sie auf die Protokolle zugreifen, vom gewählten Endpunkt ab. Für Storage lesen Sie [Azure-Ressourcenprotokolle](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage). Für Event Hubs lesen Sie ebenfalls [Azure-Ressourcenprotokolle](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs).

Für Monitor-Protokolle werden die Protokolle an den von Ihnen ausgewählten Arbeitsbereich gesendet. Für die Postgres-Protokolle wird der Sammlungsmodus `AzureDiagnostics` verwendet, damit sie über die Tabelle `AzureDiagnostics` abgefragt werden können, wie dargestellt. Weitere Informationen zu Abfragen und Warnungen finden Sie unter [Protokollabfragen in Azure Monitor](../azure-monitor/logs/log-query-overview.md).

Verwenden Sie diese Abfrage für den Einstieg. Sie können Warnungen basierend auf Abfragen konfigurieren.

Suchen Sie nach allen Postgres-Protokollen des letzten Tags für einen bestimmten Server:

```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>Nächste Schritte

- [Weitere Informationen zur Protokollierung in Azure Database for PostgreSQL](concepts-server-logs.md).
- Erfahren Sie, wie Sie Parameter mithilfe des [Azure-Portals](howto-configure-server-parameters-using-portal.md), der [Azure-Befehlszeilenschnittstelle](howto-configure-server-parameters-using-cli.md) oder der [REST-API](/rest/api/postgresql/singleserver/configurations/createorupdate) festlegen.
