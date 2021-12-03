---
title: Beschränkungen für Azure Database for PostgreSQL – Flexible Server
description: In diesem Artikel werden die Beschränkungen in Azure Database for PostgreSQL – Flexible Server beschrieben, z. B. die Anzahl der Verbindungen und Optionen für die Speicher-Engine.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/26/2021
ms.openlocfilehash: c28e3b84251c645ff690098950066b3bb653f3df
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131467528"
---
# <a name="limits-in-azure-database-for-postgresql---flexible-server"></a>Beschränkungen in Azure Database for PostgreSQL – Flexible Server


In den folgenden Abschnitten werden die Kapazitäts- und funktionalen Beschränkungen im Datenbankdienst beschrieben. Informationen zu den Tarifen für Ressourcen (Compute, Arbeitsspeicher, Speicher) finden Sie im Artikel [Compute und Speicher](concepts-compute-storage.md).

## <a name="maximum-connections"></a>Maximale Anzahl der Verbindungen

Die folgende Tabelle enthält die maximale Anzahl von Verbindungen nach Tarif und virtuellen Kernen, wie unten gezeigt. Das Azure-System benötigt drei Verbindungen, um Azure Database for PostgreSQL – Flexible Server zu überwachen.

| SKU-Name             | V-Kerne | Arbeitsspeichergröße | Max. Anzahl von Verbindungen | Max. Benutzerverbindungen |
|----------------------|--------|-------------|-----------------|----------------------|
| **Burstfähig**        |        |             |                 |                      |
| B1ms                 | 1      | 2 GiB       | 50              | 47                   |
| B2s                  | 2      | 4 GiB       | 100             | 97                   |
| **Allgemeiner Zweck**  |        |             |                 |                      |
| D2s_v3  / D2ds_v4    | 2      | 8 GiB       | 859             | 856                  |
| D4s_v3  / D4ds_v4    | 4      | 16 GiB      | 1719            | 1716                 |
| D8s_v3  / D8ds_V4    | 8      | 32GiB      | 3438            | 3435                 |
| D16s_v3 / D16ds_v4   | 16     | 64GiB      | 5.000            | 4997                 |
| D32s_v3 / D32ds_v4   | 32     | 128 GB     | 5.000            | 4997                 |
| D48s_v3 / D48ds_v4   | 48     | 192 GiB     | 5.000            | 4997                 |
| D64s_v3 / D64ds_v4   | 64     | 256 GiB     | 5.000            | 4997                 |
| **Arbeitsspeicheroptimiert** |        |             |                 |                      |
| E2s_v3  / E2ds_v4    | 2      | 16 GiB      | 1719            | 1716                 |
| E4s_v3  / E4ds_v4    | 4      | 32GiB      | 3438            | 3433                 |
| E8s_v3  / E8ds_v4    | 8      | 64GiB      | 5.000            | 4997                 |
| E16s_v3 / E16ds_v4   | 16     | 128 GB     | 5.000            | 4997                 |
| E20ds_v4             | 20     | 160 GiB     | 5.000            | 4997                 |
| E32s_v3 / E32ds_v4   | 32     | 256 GiB     | 5.000            | 4997                 |
| E48s_v3 / E48ds_v4   | 48     | 384 GiB     | 5.000            | 4997                 |
| E64s_v3 / E64ds_v4   | 64     | 432 GiB     | 5.000            | 4997                 |

Wenn Verbindungen den Grenzwert übersteigen, erhalten Sie möglicherweise den folgenden Fehler:
> SCHWERWIEGEND: Es sind bereits zu viele Clients vorhanden.

> [!IMPORTANT]
> Für eine optimale Erfahrung empfehlen wir die Verwendung eines Connection Pool Managers wie PgBouncer, um Verbindungen effizient zu verwalten. Azure Database for PostgreSQL - Flexible Server bietet pgBouncer als [eingebaute Lösung zur Verwaltung von Verbindungspools](concepts-pgbouncer.md). 

Eine PostgreSQL-Verbindung kann selbst im Leerlauf ca. 10 MB Arbeitsspeicher belegen. Außerdem nimmt das Erstellen neuer Verbindungen Zeit in Anspruch. Die meisten Anwendungen fordern viele kurzlebige Verbindungen an, was diese Situation erschwert. Das Ergebnis sind weniger Ressourcen, die für ihre tatsächliche Workload verfügbar sind, was zu verringerter Leistung führt. Mithilfe des Verbindungspoolings können Verbindungen im Leerlauf verringert und vorhandene Verbindungen wiederverwendet werden. Weitere Informationen finden Sie in unserem [Blogbeitrag](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717).

## <a name="functional-limitations"></a>Funktionale Beschränkungen

### <a name="scale-operations"></a>Skalierungsvorgänge

- Zum Skalieren des Serverspeichers ist ein Neustart des Servers erforderlich.
- Serverspeicher kann nur in Verdopplungsschritten skaliert werden. Weitere Informationen finden Sie unter [Compute und Speicher](concepts-compute-storage.md).
- Die Verringerung der Größe des Serverspeichers wird zurzeit nicht unterstützt.

### <a name="server-version-upgrades"></a>Upgrades von Serverversionen

- Die automatisierte Migration zwischen Hauptversionen von Datenbank-Engines wird derzeit nicht unterstützt. Wenn Sie auf die nächste Hauptversion upgraden möchten, führen Sie eine [Sicherung und Wiederherstellung](../howto-migrate-using-dump-and-restore.md) auf einem Server aus, der mit der neuen Engine-Version erstellt wurde.

### <a name="storage"></a>Storage

- Nach der Konfiguration kann die Speichergröße nicht mehr verringert werden. Sie müssen einen neuen Server mit der gewünschten Speichergröße erstellen und einen manuellen [Sicherungs- und Wiederherstellungsprozess](../howto-migrate-using-dump-and-restore.md) zum Migrieren Ihrer Datenbanken zum neuen Server durchführen.
- Derzeit ist automatische Speichervergrößerung nicht verfügbar. Überwachen Sie die Nutzung, und vergrößern Sie den Speicher auf einen höheren Wert. 
- Wenn die Speichernutzung 95 % erreicht oder die verfügbare Kapazität weniger als 5 GiB beträgt, wird der Server automatisch in den **schreibgeschützten Modus** umgeschaltet, um Fehler im Zusammenhang mit vollen Datenträgern zu vermeiden. 
- Es wird empfohlen, Warnungsregeln für `storage used` oder `storage percent` festzulegen, wenn diese bestimmte Schwellenwerte überschreiten, damit Sie proaktiv Maßnahmen ergreifen können, z. B. eine Erhöhung der Speichergröße. Sie können z. B. eine Warnung festlegen, wenn der Speicherprozentsatz eine Auslastung von 80 % überschreitet.
  
### <a name="networking"></a>Netzwerk

- Das Verschieben in und aus einem VNET wird derzeit nicht unterstützt.
- Das Kombinieren des öffentlichen Zugriffs mit der Bereitstellung in einem VNET wird derzeit nicht unterstützt.
- Firewallregeln werden im VNET nicht unterstützt. Stattdessen können Netzwerksicherheitsgruppen verwendet werden.
- Datenbankserver mit öffentlichem Zugriff können z. B. über `postgres_fdw` eine Verbindung mit dem öffentlichen Internet herstellen. Dieser Zugriff kann nicht eingeschränkt werden. Für VNET-basierte Server kann der ausgehende Zugriff mithilfe von Netzwerksicherheitsgruppen eingeschränkt werden.

### <a name="high-availability-ha"></a>Hochverfügbarkeit

- Weitere Informationen finden Sie auf der Dokumentationsseite [Einschränkungen der zonenredundanten Hochverfügbarkeit](concepts-high-availability.md#zone-redundant-high-availability---limitations).

### <a name="availability-zones"></a>Verfügbarkeitszonen

- Manuelles Verschieben von Servern in eine andere Verfügbarkeitszone wird derzeit nicht unterstützt.
- Die Verfügbarkeitszone des Standbyservers mit Hochverfügbarkeit kann nicht manuell konfiguriert werden.

### <a name="postgres-engine-extensions-and-pgbouncer"></a>Postgres-Engine, Erweiterungen und PgBouncer

- Postgres 10 und niedriger werden nicht unterstützt. Die Option [Einzelserver](../overview-single-server.md) wird empfohlen, wenn Sie ältere Postgres-Versionen benötigen.
- Die Erweiterungsunterstützung ist derzeit auf die Postgres-Erweiterungen des Typs `contrib` beschränkt.
- Der integrierte Verbindungspooler PgBouncer ist derzeit für Server im Tarif „Burstfähig“ nicht verfügbar.
- Die SCRAM-Authentifizierung wird mit Konnektivität mit integriertem PgBouncer nicht unterstützt.

### <a name="stopstart-operation"></a>Vorgang „Anhalten/Starten“

- Der Server kann nicht länger als sieben Tage angehalten werden.

### <a name="scheduled-maintenance"></a>Geplante Wartung

- Die Änderung des Wartungsfensters in weniger als fünf Tage vor einem bereits geplanten Upgrade hat keinen Einfluss auf dieses Upgrade. Änderungen werden erst bei der nächsten geplanten Wartung wirksam.

### <a name="backing-up-a-server"></a>Sichern eines Servers

- Sicherungen werden vom System verwaltet. Es gibt derzeit keine Möglichkeit, diese Sicherungen manuell auszuführen. Stattdessen wird die Verwendung von `pg_dump` empfohlen.
- Sicherungen sind stets auf Momentaufnahmen basierende vollständige Sicherungen (keine differenziellen Sicherungen), was möglicherweise zu einer höheren Belegung des Sicherungsspeichers führt. Beachten Sie, dass Transaktionsprotokolle (Write-Ahead-Protokolle, WAL) von den vollständigen/differentiellen Sicherungen getrennt sind und kontinuierlich archiviert werden.

### <a name="restoring-a-server"></a>Wiederherstellen eines Servers

- Bei Verwenden des Features „Zeitpunktwiederherstellung“ wird der neue Server mit der dem zugrunde liegende Server entsprechenden Compute- und Speicherkonfiguration erstellt.
- VNET-basierte Datenbankserver werden im selben VNET wiederhergestellt, wenn Sie sie aus einer Sicherung wiederherstellen.
- Der neue Server, der während einer Wiederherstellung erstellt wird, weist nicht die Firewallregeln auf, die auf dem ursprünglichen Server vorhanden waren. Firewallregeln müssen für den neuen Server separat erstellt werden.
- Die Wiederherstellung eines gelöschten Servers wird nicht unterstützt.
- Eine regionsübergreifende Wiederherstellung wird nicht unterstützt.

### <a name="other-features"></a>Andere Funktionen

* Azure AD-Authentifizierung wird noch nicht unterstützt. Wenn Sie die Azure AD-Authentifizierung benötigen, wird die Option [Einzelserver](../overview-single-server.md) empfohlen.
* Lesereplikate werden noch nicht unterstützt. Wenn Sie die Lesereplikate benötigen, wird die Option [Einzelserver](../overview-single-server.md) empfohlen.
* Das Verschieben von Ressourcen in ein anderes Abonnement wird nicht unterstützt. 


## <a name="next-steps"></a>Nächste Schritte

- Grundlegendes zu [verfügbaren Optionen für Compute- und Speicheroptionen](concepts-compute-storage.md)
- Informationen zu [unterstützten PostgreSQL-Datenbankversionen](concepts-supported-versions.md)
- Informationen zum [Sichern und Wiederherstellen eines Servers in Azure Database for PostgreSQL mithilfe des Azure-Portals](how-to-restore-server-portal.md)
