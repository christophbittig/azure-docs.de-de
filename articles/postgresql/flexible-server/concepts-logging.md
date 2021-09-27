---
title: 'Protokolle – Azure Database for PostgreSQL: Flexible Server'
description: 'Beschreibung der Konfiguration, Speicherung und Analyse der Protokollierung in Azure Database for PostgreSQL: Flexible Server'
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 7c269c26710711c23dd200f688bf1eb55d3925fd
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128597966"
---
# <a name="logs-in-azure-database-for-postgresql---flexible-server"></a>Protokolle in Azure Database for PostgreSQL: Flexible Server



Azure Database for PostgreSQL ermöglicht es Ihnen, die Standardprotokolle von Postgres zu konfigurieren und darauf zuzugreifen. Die Protokolle dienen zur Identifizierung, Behebung und Reparatur von Konfigurationsfehlern und suboptimaler Leistung. Beispiele für Protokollierungsinformationen, für die die Konfiguration und der Zugriff möglich sind, sind Fehler, Abfrageinformationen, Autovacuum-Datensätze, Verbindungen und Prüfpunkte. (Der Zugriff auf Transaktionsprotokolle ist nicht verfügbar.)

Die Überwachungsprotokollierung wird über eine Postgres-Erweiterung (`pgaudit`) bereitgestellt. Weitere Informationen finden Sie im Artikel zu den [Konzepten zur Überwachung](concepts-audit.md).

## <a name="configure-logging"></a>Konfigurieren der Protokollierung

Sie können die Postgres-Standardprotokollierung auf dem Server mit Serverparametern für die Protokollierung konfigurieren. Weitere Informationen zu Postgres-Protokollparametern finden Sie in den Abschnitten zu den Themen [Wann wird protokolliert?](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHEN) und [Was wird protokolliert?](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHAT) der Postgres-Dokumentation. Die meisten (aber nicht alle) Postgres-Protokollierungsparameter sind für die Konfiguration in Azure Database for PostgreSQL verfügbar.

Weitere Informationen dazu, wie Sie Parameter in Azure Database for PostgreSQL konfigurieren, finden Sie in der Dokumentation zum [Portal](howto-configure-server-parameters-using-portal.md) bzw. zur [Befehlszeilenschnittstelle](howto-configure-server-parameters-using-cli.md).

> [!NOTE]
> Das Konfigurieren einer großen Menge von Protokollen, z. B. bei der Protokollierung von Anweisungen, kann zu einem erheblich höheren Leistungsaufwand führen. 

## <a name="accessing-logs"></a>Zugreifen auf Protokolle

Azure Database for PostgreSQL ist in die Azure Monitor-Diagnoseeinstellungen integriert. Mit Diagnoseeinstellungen können Sie Ihre Postgres-Protokolle im JSON-Format an Azure Monitor-Protokolle (Analyse und Warnungen), Event Hubs (Streaming) und Azure Storage (Archivierung) senden. 

### <a name="log-format"></a>Protokollformat

In der folgenden Tabelle sind die Felder für den Typ **PostgreSQLLogs** beschrieben. Je nach dem ausgewählten Ausgabeendpunkt können die enthaltenen Felder und ihre Reihenfolge variieren. 

|**Feld** | **Beschreibung** |
|---|---|
| TenantId | Ihre Mandanten-ID |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | Zeitstempel für den Aufzeichnungsbeginn des Protokolls in UTC |
| type | Typ des Protokolls Immer `AzureDiagnostics` |
| SubscriptionId | GUID für das Abonnement, zu dem der Server gehört |
| ResourceGroup | Name der Ressourcengruppe, zu der der Server gehört |
| ResourceProvider | Name des Ressourcenanbieters Immer `MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| resourceId | Ressourcen-URI |
| Resource | Name des Servers |
| Category | `PostgreSQLLogs` |
| Vorgangsname | `LogEvent` |
| errorLevel | Beispiel für die Protokollierungsstufe: LOG, ERROR, NOTICE |
| `Message` | Primäre Protokollmeldung | 
| Domain | Serverversion, Beispiel: postgres-10 |
| Detail | Sekundäre Protokollmeldung (falls zutreffend) |
| ColumnName | Name der Spalte (falls zutreffend) |
| SchemaName | Name des Schemas (falls zutreffend) |
| DatatypeName | Name des Datentyps (falls zutreffend) |
| LogicalServerName | Name des Servers | 
| _ResourceId | Ressourcen-URI |
| Präfix | Präfix der Protokollzeile |


## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen über die [Konfiguration von Zugriffsprotokollen](howto-configure-and-access-logs.md).
- Erfahren Sie mehr über die [Preise für Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).
- Informieren Sie sich über [Überwachungsprotokolle](concepts-audit.md).
