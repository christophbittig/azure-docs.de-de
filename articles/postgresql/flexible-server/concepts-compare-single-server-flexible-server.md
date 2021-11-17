---
title: Vergleich von Azure Database for PostgreSQL – Einzelserver und Flexibler Server
description: Detaillierter Vergleich der Features und Funktionen von Azure Database for PostgreSQL – Einzelserver und Flexibler Server
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/02/2021
ms.openlocfilehash: c5b4f6611dc5ad5a514eaf1affd0517c14fcd353
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132337110"
---
# <a name="comparison-chart---azure-database-for-postgresql-single-server-and-flexible-server"></a>Vergleichsdiagramm: Azure Database for PostgreSQL – Einzelserver und Flexibler Server

> [!IMPORTANT]
> Azure Database for PostgreSQL – Flexible Server befindet sich in der Vorschau.

In der folgenden Tabelle werden allgemeine Features und Funktionen von Einzelserver und Flexibler Server verglichen.

| **Feature/Funktion** | **Single Server** | **Flexible Server** |
| ---- | ---- | ---- |
| **Allgemein**  | | |
| Allgemeine Verfügbarkeit | GA seit 2018 | Öffentliche Vorschau |
| PostgreSQL | Community | Community |
| Versionen | 9.6, 10, 11 | 11, 12, 13 |
| Zugrunde liegendes Betriebssystem | Windows | Linux  |
| VZ-Auswahl für Anwendungscolocation | Nein | Ja |
| Integrierte Verbindungspoolfunktion | Nein | Ja (PgBouncer)|
| **Konnektivität** | | |
| Benutzername in Verbindungszeichenfolge | `<user_name>@server_name`. Beispiel: `pgadmusr@mypgServer` | Nur Benutzername. Beispiel: `pgadmusr` | 
| lc_collate  | English_United States.1252 | en_US.utf8 |
| lc_ctype    | English_United States.1252 |en_US.utf8 |
|lc_messages | English_United States.1252 |en_US.utf8|
| lc_monetary | English_United States.1252 |  en_US.utf-8 |
| lc_numeric  | English_United States.1252 |  en_US.utf-8 |
| lc_time     | English_United States.1252 | en_US.utf8 |
| Verbindungsport | 5432 | 5432 (DB), 6432 (PgBouncer) |
| Maximal connections | 1982 | 5.000 |
| Verbindungslimit konfigurierbar? | Nein | Ja (`max_connections`-Parameter) |
| **Compute und Speicher** | | |
| Computetarife | Basic, Universell, Arbeitsspeicheroptimiert | Burstfähig, Universell, Arbeitsspeicheroptimiert |
| Burstfähige SKUs | Nein | Ja |
| Möglichkeit zum Skalieren über Computeebenen hinweg | Basic-Tarif nicht skalierbar | Ja. Ebenenübergreifend skalierbar |
| Beenden/Starten | Nein | Ja (für alle Compute-SKUs). Nur Compute wird beendet/gestartet |
| Maximal Speichergröße | 1 TB (Basic), 4 TB oder 16 TB (Universell, Arbeitsspeicheroptimiert). Hinweis: Nicht alle Regionen unterstützen 16 TB. | 16 TB |
| Min. Speichergröße | 5 GB (Basic), 100 GB (Universell, Arbeitsspeicheroptimiert) | 32 GB |
| Automatische Speichervergrößerung | Ja (1-GB-Inkremente) | Nein |
| Max. IOPS | Basic: variabel. Universell, Arbeitsspeicheroptimiert: max. 20.000  | Max. 20.000 |
| **Netzwerk/Sicherheit** | | |
| Unterstützte Netzwerkoptionen | Virtuelles Netzwerk, Private Link, öffentlicher Zugriff | Privater Zugriff (VNet-Einfügung in einem delegierten Subnetz), öffentlicher Zugriff |
| Steuerung des öffentlichen Zugriffs | Firewall | Firewall |
| Unterstützung für private Verbindungen | Ja |Nein|
| Unterstützung für private DNS-Zone | Nein | Ja |
| Möglichkeit zum Wechseln zwischen privatem und öffentlichem Zugriff | Nein | Nein |
| TLS-Unterstützung | TLS 1.2 | TLS 1.2, 1.3 erzwungen|
| SSL deaktivierbar | Ja | Nein |
| SCRAM-Authentifizierung | Nein | Ja |
| **Hochverfügbarkeit** | | |
| Zonenredundante Hochverfügbarkeit | Nein | Ja (synchroner Standbymodus wird in einer anderen Zone innerhalb einer Region eingerichtet) |
| Konfiguration für Hochverfügbarkeit | Speicherintegriert, an eine Zone angeheftet. Compute regionsübergreifend möglich. | Compute und Speicher physisch getrennt und über zwei Zonen hinweg bereitgestellt |
| Kosten | 1x | 2× (Compute und Speicher) |
| Verfügbarkeit mit Konfiguration ohne Hochverfügbarkeit | Automatischer Neustart, Computeverlagerung | Automatischer Neustart, Computeverlagerung
| Schutz vor Zonenausfällen | Compute: Ja. Speicher: Nein | Compute und Speicher: Ja |
| Schutz vor Regionsausfällen | Nein | Nein |
| Modus mit Hochverfügbarkeitsreplikation | – | Physische Streamingreplikation für Postgres im Synchronisierungsmodus
| Standby für Lesezwecke verwendbar | – | Nein |
| Auswirkungen auf Anwendungsleistung | Nein (nicht repliziert) | Ja (aufgrund der Synchronisierungsreplikation. Abhängig von der Workload) |
| Automatisches Failover | Ja (Wechsel zu anderem Server)| Ja |
| Anwendungsverbindungszeichenfolge nach Failover | Keine Änderung | Keine Änderung |
| **Logische Replikation** | | |
| Unterstützung für logische Decodierung | Ja | Ja |
| Unterstützung für native logische Replikation | Nein | Ja |
| Unterstützung für PgLogical-Erweiterung | Nein | Ja |
| Unterstützung für logische Replikation mit Hochverfügbarkeit | – | Eingeschränkt |
| **Notfallwiederherstellung** | | |
| Regionenübergreifende Notfallwiederherstellung | Unter Verwendung von Lesereplikaten, georedundante Sicherung | – |
| DR mit Replikat | Unter Verwendung der asynchronen physischen Replikation | – |
| Automatisches Failover | Nein | – |
| Gleicher Lese-/Schreib-Endpunkt verwendbar | Nein | – |
| **Sicherung und Wiederherstellung** | | |
| Automatisierte Sicherungen | Ja | Ja |
| Sicherungsaufbewahrung | 7–35 Tage | 7–35 Tage |
| PITR-Funktion zu jedem beliebigen Zeitpunkt innerhalb des Aufbewahrungszeitraums | Ja | Ja
| Möglichkeit zum Wiederherstellen in einer anderen Zone | – | Ja |
| Möglichkeit zum Wiederherstellen in einem anderen VNet | Nein | Ja |
| Möglichkeit zum Wiederherstellen in einer anderen Region | Ja (georedundant) | Nein |
| Möglichkeit zum Wiederherstellen eines gelöschten Servers | Eingeschränkt über die API | Nein |
| **Lesereplikat** | | |
| Unterstützung für Lesereplikate | Ja | Nein |
| Anzahl von Lesereplikaten | 5 | – |
| Modus der Replikation | Async | – |
| Regionsübergreifende Unterstützung | Ja | – |
| **Wartungsfenster** | | |
| Vom System geplantes Zeitfenster | Ja | Ja |
| Vom Kunden geplantes Zeitfenster| Nein | Ja (jede Stunde an beliebigem Tag auswählbar) |
| Benachrichtigungszeitraum | 3 Tage | 5 Tage |
| Wartungszeitraum | Jederzeit innerhalb eines 15-Stunden-Zeitfensters | Zeitfenster von 1 Stunde | 
| **Metriken** | | |
| Fehler | Verbindungsfehler | Verbindungsfehler|
| Latency | Max. Verzögerung zwischen Replikaten, Replikatverzögerung | – |
| Sättigung | Verwendeter Sicherungsspeicher, CPU %, E/A %, Arbeitsspeicher %, Speicherlimit für Serverprotokolle, Serverprotokollspeicher %, verwendeter Serverprotokollspeicher, Speicherlimit, Speicher %, verwendeter Speicher | Verwendeter Sicherungsspeicher, verbrauchtes CPU-Guthaben, verbleibendes CPU-Guthaben, CPU %, Warteschlangentiefe auf Datenträger, IOPS, Arbeitsspeicher %, Lese-IOPS, Lesedurchsatz Bytes/s, freier Speicher, Speicher %, verwendeter Speicher, verwendeter Transaktionsprotokollspeicher, Schreib-IOPS, Schreibdurchsatz Bytes/s |
| Verkehr | Aktive Verbindungen, Netzwerk eingehend, Netzwerk ausgehend | Aktive Verbindungen, max. verwendete Transaktions-ID, Netzwerk eingehend, Netzwerk ausgehend, erfolgreiche Verbindungen |
| **Erweiterungen** | | (bietet die neuesten Versionen)|
| TimescaleDB, orafce, plv8 | Ja | Nein |
| PgCron, lo, pglogical | Nein | Ja |
| pgAudit | Vorschau | Ja |
| **Weitere Features** | | |
| Kundenseitig verwalteter Schlüssel (BYOK) | Ja | Nein |
| Alerts | Ja | Ja |
| Microsoft Defender für Cloud | Ja | Nein |
| Ressourcenintegrität | Ja | Nein |
| Dienstintegrität | Ja | Ja |
| Einblicke in die Leistung (iPerf) | Ja | Ja (Vorschau) |
| Unterstützung für Upgrades von Hauptversionen | Nein | Nein |
| Upgrades auf Nebenversionen | Ja. Automatisch während des Wartungsfensters | Ja. Automatisch während des Wartungsfensters |


## <a name="next-steps"></a>Nächste Schritte

- Grundlegendes zu [verfügbaren Optionen für Compute- und Speicheroptionen bei flexiblen Servern](concepts-compute-storage.md)
- Informationen zu [unterstützten PostgreSQL-Datenbankversionen bei flexiblen Servern](concepts-supported-versions.md)
- Informationen zu [aktuellen Einschränkungen bei flexiblen Servern](concepts-limits.md)
