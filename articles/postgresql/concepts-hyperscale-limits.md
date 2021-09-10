---
title: 'Grenzwerte und Einschränkungen: Hyperscale (Citus) – Azure Database for PostgreSQL'
description: Aktuelle Grenzwerte für Hyperscale (Citus)-Servergruppen
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 08/03/2021
ms.openlocfilehash: 5bf02173d105ab81807bdc4ee68e3b8f9bc8e0a4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122347139"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-limits-and-limitations"></a>Azure Database for PostgreSQL: Einschränkungen von Hyperscale (Citus)

In den folgenden Abschnitten werden die Kapazitäts- und funktionalen Beschränkungen in Hyperscale (Citus) beschrieben.

## <a name="maximum-connections"></a>Maximale Anzahl der Verbindungen

Jede PostgreSQL-Verbindung (auch inaktive Verbindungen) verwendet mindestens 10 MB Arbeitsspeicher, daher ist es wichtig, gleichzeitige Verbindungen einzuschränken. Dies sind die Grenzwerte, die wir ausgewählt haben, um Knoten fehlerfrei zu halten:

* Koordinatorknoten
   * Maximale Verbindungen
       * 300 für 0 – 3 virtuelle Kerne
       * 500 für 4 – 15 virtuelle Kerne
       * 1\.000 für 16 und mehr virtuelle Kerne
   * Maximale Anzahl von Benutzerverbindungen
       * 297 für 0–3 virtuelle Kerne
       * 497 für 4–15 virtuelle Kerne
       * 997 für 16 und mehr virtuelle Kerne
* Workerknoten
   * Maximale Verbindungen
       * 600

Verbindungsversuche über diese Grenzwerte hinaus führen zu einem Fehler. Das System reserviert drei Verbindungen für die Überwachung von Knoten. Aus diesem Grund sind für Benutzerabfragen drei Verbindungen weniger verfügbar als Verbindungen insgesamt.

### <a name="connection-pooling"></a>Verbindungspooling

Sie können Verbindungen über ein [Verbindungspooling](concepts-hyperscale-connection-pool.md) weiter skalieren. Hyperscale (Citus) bietet einen verwalteten pgBouncer-Verbindungspooler, der für bis zu 2.000 gleichzeitige Clientverbindungen konfiguriert ist.

## <a name="storage-scaling"></a>Speicherskalierung

Der Speicher auf Koordinator- und Workerknoten kann hochskaliert (vergrößert) werden, er kann aber nicht herunterskaliert werden.

## <a name="storage-size"></a>Speichergröße

Für Koordinator- und Workerknoten wird eine Speicherkapazität von bis zu 2 TiB unterstützt. Weitere Informationen finden Sie in den verfügbaren Speicheroptionen und der IOPS-Berechnung [oben](concepts-hyperscale-configuration-options.md#compute-and-storage) für Knoten- und Clustergrößen.

## <a name="database-creation"></a>Datenbankerstellung

Im Azure-Portal werden Anmeldeinformationen bereitgestellt, um eine Verbindung mit genau einer Datenbank pro Hyperscale (Citus)-Servergruppe herzustellen, der `citus`-Datenbank. Das Erstellen einer anderen Datenbank ist zurzeit nicht zulässig, und der CREATE DATABASE-Befehl verursacht einen Fehler.

## <a name="columnar-storage"></a>Spaltenbasierter Speicher

In Hyperscale (Citus) gelten derzeit die folgenden Einschränkungen für [spaltenbasierte Tabellen](concepts-hyperscale-columnar.md):

* Komprimierung auf dem Datenträger, nicht im Arbeitsspeicher
* Nur Anfügen (keine Unterstützung für UPDATE/DELETE)
* Keine Speicherplatzrückgewinnung (Transaktionen, für die ein Rollback ausgeführt wurde, können z. B. weiterhin Speicherplatz belegen)
* Keine Indexunterstützung, Indexscans oder Bitmapindexscans
* Keine TID-Scans
* Keine Stichprobenscans
* Keine TOAST-Unterstützung (große Werte werden inline unterstützt)
* Keine Unterstützung für ON CONFLICT-Anweisungen (mit Ausnahme von DO NOTHING-Aktionen ohne Angabe eines Ziels)
* Keine Unterstützung für Tupelsperren (SELECT... FOR SHARE, SELECT... FOR UPDATE)
* Keine Unterstützung für serialisierbare Isolationsstufen
* Unterstützung nur für PostgreSQL-Serverversionen ab Version 12
* Keine Unterstützung für Fremdschlüssel, UNIQUE-Einschränkungen oder Ausschlusseinschränkungen
* Keine Unterstützung für logische Decodierung
* Keine Unterstützung für knoteninterne parallele Scans
* Keine Unterstützung für AFTER... FOR EACH ROW-Trigger
* Keine nicht protokollierten spaltenbasierten Tabellen
* Keine temporären Spaltentabellen

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie [eine Hyperscale (Citus)-Servergruppe im Portal erstellen](quickstart-create-hyperscale-portal.md).
* Erfahren Sie mehr über das [Verbindungspooling](concepts-hyperscale-connection-pool.md).
