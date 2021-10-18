---
title: Serverparameter – Azure Database for PostgreSQL – Flexible Server
description: Beschreibung der Serverparameter in Azure Database for PostgreSQL – Flexible Server
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: d8690bd33f10744b6d9cc9cd671e1ee46b102793
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129711893"
---
# <a name="server-parameters-in-azure-database-for-postgresql---flexible-server"></a>Serverparameter in Azure Database for PostgreSQL – Flexible Server

> [!IMPORTANT]
> Azure Database for PostgreSQL – Flexible Server befindet sich in der Vorschau.

Azure Database for PostgreSQL bietet für jeden Server einen Satz konfigurierbarer Parameter. Weitere Informationen zu Postgres-Parametern finden Sie in der [PostgreSQL-Dokumentation](https://www.postgresql.org/docs/13/config-setting.html).

## <a name="an-overview-of-postgresql-parameters"></a>Übersicht über PostgreSQL-Parameter 

Azure Database for PostgreSQL-Server werden beim Erstellen mit optimalen Standardwerten für die einzelnen Parameter vorkonfiguriert. Statische Parameter erfordern einen Serverneustart, und Parameter, die Superuserzugriff erfordern, können vom Benutzer nicht konfiguriert werden. 

Um zu überprüfen, welche Parameter angezeigt oder geändert werden können, empfiehlt es sich, die Seite Serverparameter auf dem Azure-Portal aufzurufen. Mithilfe der `ALTER DATABASE`- oder `ALTER ROLE`-Befehle können Sie Parameter auch für individuelle Benutzer oder Datenbanken konfigurieren.

>[!NOTE]
> Da Azure Database for PostgreSQL ein verwalteter Datenbankdienst ist, erhalten Benutzer keinen Host- oder Betriebssystemzugriff, um Konfigurationsdateien wie `postgresql.conf` anzuzeigen oder zu ändern. Der Inhalt der Datei wird anhand Parameteränderungen auf der Seite „Serverparameter“ automatisch aktualisiert.

:::image type="content" source="./media/concepts-server-parameters/server-parameters.png" alt-text="Serverparameter – Portal":::

Liste mit einigen der Parameter:


   | Parametername             | Beschreibung |
|----------------------|--------|
| **max_connections** | Sie können max_connections in Postgres Flexible Server anpassen, wo es auf 5.000 Verbindungen gesetzt werden kann. Weitere Informationen finden Sie in der [Dokumentation zu Grenzwerten](concepts-limits.md). | 
| **shared_buffers**    | Die Einstellung shared_buffers ändert sich abhängig von der ausgewählten SKU. (Die SKU bestimmt den verfügbaren Arbeitsspeicher.) Universelle Server verfügen über 2 GB an „shared_buffers“ für zwei V-Kerne. Speicheroptimierte Server verfügen über 4 GB an „shared_buffers“ für zwei V-Kerne. Die Einstellung „shared_buffers“ wird linear (ungefähr) skaliert, wenn sich die Anzahl von V-Kernen in einem Tarif erhöht. | 
| **shared_preload_libraries** | Dieser Parameter ist für die Konfiguration mit einem vordefinierten Satz unterstützter Erweiterungen verfügbar. Beachten Sie, dass wir stets die Erweiterung `azure` (die für Wartungsaufgaben verwendet wird) sowie die Erweiterung `pg_stat_statements` laden  (Sie können den Parameter pg_stat_statements.track verwenden, um zu kontrollieren, ob die Erweiterung aktiv ist). |
| **connection_throttling** | Diese Einstellung aktiviert die temporäre Verbindungsdrosselung für einzelne IP-Adressen bei zu vielen Anmeldefehlern durch ungültige Kennwörter. |
 | **work_mem** | Dieser Parameter legt den Arbeitsspeicher fest, der von internen Sortiervorgängen und Hashtabellen vor dem Schreiben in temporäre Datenträgerdateien verwendet werden soll. Wenn Ihre Workload nur wenige Abfragen mit vielen komplexen Sortierungen enthält und Sie über viel verfügbaren Arbeitsspeicher verfügen, kann die Erhöhung dieses Parameters es Postgres ermöglichen, größere Überprüfungen im Arbeitsspeicher auszuführen (was schneller ist) und einen Überlauf auf den Datenträger zu vermeiden.  Seien Sie jedoch vorsichtig, da bei einer komplexen Abfrage möglicherweise viele Sortier- und Hashvorgängen gleichzeitig ausgeführt werden. Jeder dieser Vorgänge verwendet so viel Arbeitsspeicher, wie der Wert zulässt, bevor er mit dem Schreiben in datenträgerbasierte temporäre Dateien beginnt. Daher ist die Gesamtspeicherauslastung auf einem relativ ausgelasteten System ein Vielfaches des einzelnen „work_mem“-Parameters. Wenn Sie diesen Wert global optimieren möchten, können Sie die Formel „Gesamt-RAM * 0,25 / max_connections“ als Ausgangswert verwenden. Azure Database for PostgreSQL: Flexible Server unterstützt einen Bereich von 4096 bis 2097151 Bytes für diesen Parameter.|
| **effective_cache_size** |Der Parameter „effective_cache_size“ schätzt, wie viel Arbeitsspeicher für die Datenträgerzwischenspeicherung durch das Betriebssystem und innerhalb der Datenbank selbst verfügbar ist. Der PostgreSQL-Abfrageplaner entscheidet, ob er im RAM fixiert ist oder nicht. Indexscans werden höchstwahrscheinlich für höhere Werte verwendet. Andernfalls werden bei einem niedrigen Wert sequenzielle Scans ausgeführt. Empfohlen wird, ““Effective_cache_size“ auf 50 % des gesamten RAM des Computers festzulegen. |
| **maintenance_work_mem** | Der Parameter „maintenance_work_mem“ stellt im Grunde die maximale Menge an Arbeitsspeicher bereit, die von Wartungsvorgängen wie Vacuum, Index erstellen und „alter table add foreign key-Vorgängen verwendet werden soll.  Der Standardwert für diesen Parameter ist 64 KB. Es wird empfohlen, den Parameter auf einen höheren Wert als „work_mem“ festzulegen. Dies kann die Leistung beim Ausführen von Vacuum verbessern. |
| **effective_io_concurrency** | Legt die Anzahl der gleichzeitigen Datenträger-E/A-Vorgänge fest, die von deren gleichzeitiger Ausführung PostgreSQL ausgeht. Wenn Sie diesen Wert erhöhen, erhöht sich die Anzahl der E/A-Vorgänge, die jede einzelne PostgreSQL-Sitzung parallel zu initiieren versucht. Der zulässige Bereich liegt zwischen 1 und 1000 bzw. 0 (null), um die Ausgabe asynchroner E/A-Anforderungen zu deaktivieren. Derzeit wirkt sich diese Einstellung nur auf Bitmapheapscans aus. |
 |**require_secure_transport** | Wenn Ihre Anwendung keine SSL-Konnektivität mit dem Server unterstützt, können Sie optional den gesicherten Transport von Ihrem Client deaktivieren, indem Sie diesen Parameterwert auf `OFF` setzen. |

>[!NOTE]
> Wenn Sie Azure Database for PostgreSQL: Flexible Server-SKUs hoch- oder herunterskalieren, was sich auf den verfügbaren Arbeitsspeicher für den Server auswirkt, sollten Sie vielleicht Ihre globalen Parameter für den Arbeitsspeicher, z. B. „work_mem“ oder „effective_cache_size“ entsprechend den oben genannten Informationen, optimieren. 

 
## <a name="next-steps"></a>Nächste Schritte

Informationen zu unterstützten PostgreSQL-Erweiterungen finden Sie im [Dokument zu Erweiterungen](concepts-extensions.md).
