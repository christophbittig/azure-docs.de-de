---
title: 'Überwachungsprotokollierung: Azure Database for PostgreSQL – Hyperscale (Citus)'
description: Konzepte für die Überwachungsprotokollierung mit pgAudit in Azure Database for PostgreSQL – Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 08/03/2021
ms.openlocfilehash: 349af1634f71f74dee85f99f69341b96cdb11b9e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131084405"
---
# <a name="audit-logging-in-azure-database-for-postgresql---hyperscale-citus"></a>Überwachungsprotokollierung in Azure Database for PostgreSQL – Hyperscale (Citus)

> [!IMPORTANT]
> Die pgAudit-Erweiterung in Hyperscale (Citus) befindet sich derzeit in der Vorschauphase. Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
>
> Eine vollständige Liste der anderen neuen Features finden Sie unter [Previewfunktionen für PostgreSQL – Hyperscale (Citus)](./hyperscale-product-updates.md).

Die Überwachungsprotokollierung von Datenbankaktivitäten in Azure Database for PostgreSQL – Hyperscale (Citus) ist über die PostgreSQL-Überwachungserweiterung [pgAudit](https://www.pgaudit.org/) verfügbar. pgAudit bietet eine ausführliche Sitzungs- bzw. Objektüberwachungsprotokollierung.

Wenn Sie Azure-Protokolle auf Ressourcenebene für Vorgänge wie Compute- und Speicherskalierung erfassen möchten, lesen Sie den Artikel zum [Azure-Aktivitätsprotokoll](../azure-monitor/essentials/platform-logs-overview.md).

## <a name="usage-considerations"></a>Überlegungen zur Verwendung
Standardmäßig werden pgAudit-Protokollanweisungen zusammen mit ihren regulären Protokollanweisungen mithilfe der Standardprotokollierungsfunktion von Postgres ausgegeben. Sie können in Azure Database for PostgreSQL – Hyperscale (Citus) konfigurieren, dass alle Protokolle für spätere Analysen in Log Analytics an den Azure Monitor-Protokollspeicher gesendet werden. Wenn Sie die Azure Monitor-Ressourcenprotokollierung aktivieren, werden Ihre Protokolle abhängig von Ihrer Wahl automatisch (im JSON-Format) an Azure Storage, Event Hubs bzw. Azure Monitor-Protokolle gesendet.

## <a name="enabling-pgaudit"></a>Aktivieren von pgAudit

Die pgAudit-Erweiterung ist auf den meisten Knoten der Servergruppe mit Hyperscale (Citus) vorinstalliert und aktiviert. Wenn sie auf Ihren Knoten nicht aktiviert ist, stellen Sie eine [Supportanfrage](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="pgaudit-settings"></a>pgAudit-Einstellungen

pgAudit ermöglicht es Ihnen, die Sitzungs- oder Objektüberwachungsprotokollierung zu konfigurieren. Bei der [Sitzungsüberwachungsprotokollierung](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) werden detaillierte Protokolle von ausgeführten Anweisungen ausgegeben. Bei der [Objektüberwachungsprotokollierung](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) ist die Überwachung auf bestimmte Beziehungen gerichtet. Sie können auswählen, ob Sie einen oder beide Protokollierungstypen einrichten möchten. 

> [!NOTE]
> Die Einstellungen von pgAudit werden global angegeben, nicht auf Datenbank- oder Rollenebene.
>
> Außerdem werden pgAudit-Einstellungen innerhalb einer Servergruppe knotenweise angegeben. Wenn Sie eine Änderung an allen Knoten vornehmen möchten, müssen Sie diese einzeln auf jeden Knoten anwenden.

Sie müssen pgAudit-Parameter konfigurieren, um die Protokollierung zu starten. Die [pgAudit-Dokumentation](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) stellt die Definition der einzelnen Parameter bereit. Testen Sie zuerst die Parameter, und vergewissern Sie sich, dass Sie das erwartete Verhalten aufweisen.

> [!NOTE]
> Wenn `pgaudit.log_client` auf „ON“ festgelegt wird, werden Protokolle an einen Clientprozess (z.B. psql) umgeleitet, anstatt in eine Datei geschrieben zu werden. Diese Einstellung sollte in der Regel deaktiviert bleiben. <br> <br>
> `pgaudit.log_level` ist nur aktiviert, wenn `pgaudit.log_client` aktiviert ist.

> [!NOTE]
> In Azure Database for PostgreSQL – Hyperscale (Citus) kann `pgaudit.log` nicht mit einem Minuszeichen (`-`) festgelegt werden. Dies ist auch in der Dokumentation zu pgAudit beschrieben. Alle erforderlichen Anweisungsklassen (READ, WRITE usw.) sollten einzeln angegeben werden.

## <a name="audit-log-format"></a>Format der Überwachungsprotokolle
Jeder Überwachungseintrag wird durch `AUDIT:` am Anfang der Protokollzeile gekennzeichnet. Das Format des restlichen Eintrags wird in der [pgAudit-Dokumentation](https://github.com/pgaudit/pgaudit/blob/master/README.md#format) ausführlich erläutert.

## <a name="getting-started"></a>Erste Schritte
Legen Sie für einen schnellen Einstieg `pgaudit.log` auf `WRITE` fest, und öffnen Sie Ihre Serverprotokolle, um die Ausgabe zu überprüfen. 

## <a name="viewing-audit-logs"></a>Anzeigen von Überwachungsprotokollen
Die Art und Weise, wie Sie auf die Protokolle zugreifen, hängt vom gewählten Endpunkt ab. Informationen zu Azure Storage finden Sie im Artikel [Protokollspeicherkonto](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage). Informationen zu Event Hubs finden Sie im Artikel zum [Streamen von Azure-Protokollen](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs).

Bei Azure Monitor-Protokollen werden die Protokolle an den von Ihnen ausgewählten Arbeitsbereich gesendet. Für die Postgres-Protokolle wird der Sammlungsmodus **AzureDiagnostics** verwendet, damit sie über die Tabelle „AzureDiagnostics“ abgefragt werden können. Die Felder der Tabelle sind unten beschrieben. Weitere Informationen zu Abfragen und Warnungen finden Sie in der Übersicht über [Abfragen für Azure Monitor-Protokolle](../azure-monitor/logs/log-query-overview.md).

Sie können diese Abfrage für den Einstieg verwenden. Sie können Warnungen basierend auf Abfragen konfigurieren.

Suchen in den Postgres-Protokollen eines bestimmten Servers für den letzten Tag nach allen pgAudit-Einträgen.
```kusto
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>Nächste Schritte

- [Weitere Informationen zur Einrichtung der Protokollierung in Azure Database for PostgreSQL – Hyperscale (Citus) und zum Zugriff auf Protokolle](howto-hyperscale-logging.md)