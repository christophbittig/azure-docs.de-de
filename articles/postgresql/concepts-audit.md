---
title: Überwachungsprotokollierung – Azure Database for PostgreSQL (Einzelserver)
description: Konzepte für die pgAudit-Überwachungsprotokollierung in Azure Database for PostgreSQL (Einzelserver).
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: f15ffe95f002ac74553363485fe90d16f8864347
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128552521"
---
# <a name="audit-logging-in-azure-database-for-postgresql---single-server"></a>Überwachungsprotokollierung in Azure Database for PostgreSQL (Einzelserver)

Die Überwachungsprotokollierung von Datenbankaktivitäten in Azure Database for PostgreSQL (Einzelserver) ist über die PostgreSQL-Überwachungserweiterung [pgAudit](https://www.pgaudit.org/) verfügbar. pgAudit bietet eine ausführliche Sitzungs- und/oder Objektüberwachungsprotokollierung.

> [!NOTE]
> pgAudit befindet sich in Azure Database for PostgreSQL in der Vorschau.
> Die Erweiterung kann nur auf universellen und arbeitsspeicheroptimierten Servern aktiviert werden.

Wenn Sie Azure-Protokolle auf Ressourcenebene für Vorgänge wie Compute- und Speicherskalierung erfassen möchten, lesen Sie den Artikel zum [Azure-Aktivitätsprotokoll](../azure-monitor/essentials/platform-logs-overview.md).

## <a name="usage-considerations"></a>Überlegungen zur Verwendung
Standardmäßig werden pgAudit-Protokollanweisungen zusammen mit ihren regulären Protokollanweisungen mithilfe der Standardprotokollierungsfunktion von Postgres ausgegeben. In Azure Database for PostgreSQL können diese LOG-Dateien über das Azure-Portal oder die CLI herunterladen werden. Der maximale Speicherplatz für die Sammlung von Dateien beträgt 1 GB, und jede Datei ist für maximal sieben Tage verfügbar (der Standardwert ist drei Tage). Bei diesem Dienst handelt es sich um eine kurzfristige Speicheroption.

Alternativ können Sie alle Protokolle so konfigurieren, dass sie an den Azure Monitor-Protokollspeicher für spätere Analysen in Log Analytics gesendet werden. Wenn Sie die Azure Monitor-Ressourcenprotokollierung aktivieren, werden Ihre Protokolle abhängig von Ihrer Wahl automatisch (im JSON-Format) an Azure Storage, Event Hubs und/oder Azure Monitor-Protokolle gesendet.

Durch das Aktivieren von pgAudit wird ein großes Volumen von Protokollierung auf einem Server generiert, was sich auf die Leistung und den Protokollspeicher auswirkt. Es wird empfohlen, Azure Monitor-Protokolle zu verwenden, die langfristigere Speicheroptionen sowie Analyse- und Benachrichtigungsfunktionen bieten. Es wird empfohlen, die Standardprotokollierung zu deaktivieren, um die Auswirkungen der zusätzlichen Protokollierung auf die Leistung zu reduzieren:

   1. Legen Sie den Parameter `logging_collector` auf „OFF“ fest. 
   2. Starten Sie den Server neu, um diese Änderung zu übernehmen.

Weitere Informationen zum Einrichten der Protokollierung für Azure Storage, Event Hubs oder Azure Monitor-Protokolle finden Sie im Abschnitt „Ressourcenprotokolle“ des Artikels [Serverprotokolle](concepts-server-logs.md).

## <a name="installing-pgaudit"></a>Installieren von pgAudit

Um pgAudit installieren zu können, müssen Sie es in die freigegebenen im Voraus geladenen Bibliotheken des Servers einbeziehen. Damit eine Änderung des`shared_preload_libraries` -Parameters von PostgreSQL wirksam wird, ist ein Serverneustart erforderlich. Sie können Parameter mithilfe des [Microsoft Azure-Portals](howto-configure-server-parameters-using-portal.md), der [Azure-Befehlszeilenschnittstelle](howto-configure-server-parameters-using-cli.md) oder der [REST-API](/rest/api/postgresql/singleserver/configurations/createorupdate) ändern.

Verwenden des [Azure-Portals](https://portal.azure.com):

   1. Wählen Sie Ihre Azure Database for PostgreSQL-Server aus.
   2. Wählen Sie auf der Seitenleiste **Serverparameter** aus.
   3. Suchen Sie nach dem Parameter `shared_preload_libraries`.
   4. Wählen Sie **pgaudit** aus.
     :::image type="content" source="./media/concepts-audit/share-preload-parameter.png" alt-text="Screenshot: Azure Database for PostgreSQL : Aktivieren von shared_preload_libraries für pgaudit ":::
   5. Starten Sie den Server neu, um die Änderung zu übernehmen.
   6. Sie können überprüfen, ob **pgaudit** in shared_preload_libraries geladen wird, indem Sie die folgende Abfrage in psql ausführen:
        ```SQL
      show shared_preload_libraries;
      ```
      Im Abfrageergebnis, das die shared_preload_libraries wiedergibt, sollte **pgaudit** angezeigt werden.

   7. Verbinden Ihres Servers mithilfe eines Clients (wie psql) und Aktivieren der pgAudit-Erweiterung
      ```SQL
      CREATE EXTENSION pgaudit;
      ```

> [!TIP]
> Wenn ein Fehler angezeigt wird, vergewissern Sie sich, dass Sie nach dem Speichern von `shared_preload_libraries` Ihren Server neu gestartet haben.

## <a name="pgaudit-settings"></a>pgAudit-Einstellungen

pgAudit ermöglicht es Ihnen, die Sitzungs- oder Objektüberwachungsprotokollierung zu konfigurieren. Bei der [Sitzungsüberwachungsprotokollierung](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) werden detaillierte Protokolle von ausgeführten Anweisungen ausgegeben. Bei der [Objektüberwachungsprotokollierung](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) ist die Überwachung auf bestimmte Beziehungen gerichtet. Sie können auswählen, ob Sie einen oder beide Protokollierungstypen einrichten möchten. 

> [!NOTE]
> Die Einstellungen von pgAudit werden global angegeben, nicht auf Datenbank- oder Rollenebene.

Nachdem Sie [pgAudit installiert haben](#installing-pgaudit), können Sie die zugehörigen Parameter so konfigurieren, dass die Protokollierung gestartet wird. Zum Konfigurieren von pgAudit können Sie die folgenden Anweisungen befolgen. Verwenden des [Azure-Portals](https://portal.azure.com):

   1. Wählen Sie Ihre Azure Database for PostgreSQL-Server aus.
   2. Wählen Sie auf der Seitenleiste **Serverparameter** aus.
   3. Suchen Sie nach den `pg_audit`-Parametern.
   4. Wählen Sie den entsprechenden Einstellungsparameter aus, der bearbeitet werden soll. Legen Sie beipielsweise um mit der Protokollierung zu beginnen `pgaudit.log` auf `WRITE` fest. :::image type="content" source="./media/concepts-audit/pgaudit-config.png" alt-text="Screenshot: Azure Database for PostgreSQL – Protokollierung mit pgaudit konfigurieren":::
   5. Klicken Sie auf die Schaltfläche **Speichern**, um die Änderungen zu speichern.



Die [pgAudit-Dokumentation](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) stellt die Definition der einzelnen Parameter bereit. Testen Sie zuerst die Parameter, und vergewissern Sie sich, dass Sie das erwartete Verhalten aufweisen.


> [!NOTE]
> Wenn `pgaudit.log_client` auf „ON“ festgelegt wird, werden Protokolle an einen Clientprozess (z.B. psql) umgeleitet, anstatt in eine Datei geschrieben zu werden. Diese Einstellung sollte in der Regel deaktiviert bleiben. <br> <br>
> `pgaudit.log_level` ist nur aktiviert, wenn `pgaudit.log_client` aktiviert ist.

> [!NOTE]
> In Azure Database for PostgreSQL kann `pgaudit.log` nicht mit einer Minuszeichenverknüpfung („`-`“) festgelegt werden, wie in der pgAudit-Dokumentation beschrieben. Alle erforderlichen Anweisungsklassen (READ, WRITE usw.) sollten einzeln angegeben werden.

### <a name="audit-log-format"></a>Format der Überwachungsprotokolle
Jeder Überwachungseintrag wird durch `AUDIT:` am Anfang der Protokollzeile gekennzeichnet. Das Format des restlichen Eintrags wird in der [pgAudit-Dokumentation](https://github.com/pgaudit/pgaudit/blob/master/README.md#format) ausführlich erläutert.

Wenn Sie weitere Felder benötigen, um Ihre Überwachungsanforderungen zu erfüllen, verwenden Sie den Postgres-Parameter `log_line_prefix`. `log_line_prefix` ist eine Zeichenfolge, die am Anfang jeder Postgres-Protokollzeile ausgegeben wird. Die folgende `log_line_prefix`-Einstellung stellt beispielsweise den Zeitstempel, den Benutzernamen, den Datenbanknamen und die Prozess-ID bereit:

```
t=%m u=%u db=%d pid=[%p]:
```

Weitere Informationen zu `log_line_prefix` finden Sie in der [PostgreSQL-Dokumentation](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LINE-PREFIX).

### <a name="getting-started"></a>Erste Schritte
Legen Sie zum schnellen Einstieg `pgaudit.log` auf `WRITE` fest, und öffnen Sie Ihre Protokolle, um die Ausgabe zu überprüfen. 

## <a name="viewing-audit-logs"></a>Anzeigen von Überwachungsprotokollen
Wenn Sie LOG-Dateien verwenden, sind Ihre Überwachungsprotokolle in derselben Datei enthalten wie Ihre PostgreSQL-Fehlerprotokolle. Sie können Protokolldateien aus dem Azure-[Portal](howto-configure-server-logs-in-portal.md) oder der [CLI](howto-configure-server-logs-using-cli.md) herunterladen. 

Wenn Sie Azure-Ressourcenprotokollierung verwenden, hängt die Art und Weise, wie Sie auf die Protokolle zugreifen, vom gewählten Endpunkt ab. Informationen zu Azure Storage finden Sie im Artikel [Protokollspeicherkonto](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage). Informationen zu Event Hubs finden Sie im Artikel zum [Streamen von Azure-Protokollen](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs).

Bei Azure Monitor-Protokollen werden die Protokolle an den von Ihnen ausgewählten Arbeitsbereich gesendet. Für die Postgres-Protokolle wird der Sammlungsmodus **AzureDiagnostics** verwendet, damit sie über die Tabelle „AzureDiagnostics“ abgefragt werden können. Die Felder der Tabelle sind unten beschrieben. Weitere Informationen zu Abfragen und Warnungen finden Sie in der Übersicht über [Abfragen für Azure Monitor-Protokolle](../azure-monitor/logs/log-query-overview.md).

Sie können diese Abfrage für den Einstieg verwenden. Sie können Warnungen basierend auf Abfragen konfigurieren.

Suchen nach allen Postgres-Protokollen des letzten Tags für einen bestimmten Server
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>Nächste Schritte
- [Weitere Informationen zur Protokollierung in Azure Database for PostgreSQL](concepts-server-logs.md)
- Erfahren Sie, wie Sie Parameter mithilfe des [Microsoft Azure-Portals](howto-configure-server-parameters-using-portal.md), der [Azure-Befehlszeilenschnittstelle](howto-configure-server-parameters-using-cli.md) oder der [REST-API](/rest/api/postgresql/singleserver/configurations/createorupdate) festlegen.
