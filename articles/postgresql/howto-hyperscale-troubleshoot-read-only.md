---
title: 'Beheben von Problemen mit dem schreibgeschützten Zugriff – Hyperscale (Citus): Azure Database for PostgreSQL'
description: Hier erfahren Sie, warum eine Hyperscale (Citus)-Servergruppe schreibgeschützt werden kann und welche Maßnahmen ergriffen werden können.
keywords: PostgreSQL-Verbindung,schreibgeschützt
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 08/03/2021
ms.openlocfilehash: 48ea5fbeacf1769f73b9d505e6cc8fb55e8c3a44
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339803"
---
# <a name="troubleshoot-read-only-access-to-azure-database-for-postgresql---hyperscale-citus"></a>Beheben von Problemen mit dem schreibgeschützten Zugriff auf Azure Database for PostgreSQL: Hyperscale (Citus)

PostgreSQL kann nicht auf einem Computer ohne freien Speicherplatz ausgeführt werden. Um den Zugriff auf PostgreSQL-Server aufrechtzuerhalten, muss verhindert werden, dass der Speicherplatz knapp wird.

In Hyperscale (Citus) wird für Knoten der schreibgeschützte Zustand (Read-Only, RO) festgelegt, wenn der Datenträger fast voll ist. Durch das Verhindern von Schreibvorgängen wird vermieden, dass der Datenträger weiter gefüllt wird, und der Knoten bleibt für Lesevorgänge verfügbar. Im schreibgeschützten Zustand können Sie Maßnahmen ergreifen, um mehr Speicherplatz auf dem Datenträger freizugeben.

Genauer gesagt wird ein Hyperscale (Citus)-Knoten schreibgeschützt, wenn weniger als 5 GiB freier Speicherplatz übrig ist. Wenn der Server schreibgeschützt wird, werden alle bestehenden Sitzungen getrennt, und für Transaktionen ohne Commit wird ein Rollback ausgeführt. Bei Schreibvorgängen und Transaktionscommits tritt ein Fehler auf, Leseabfragen funktionieren hingegen weiterhin.

## <a name="ways-to-recover-write-access"></a>Möglichkeiten zum Wiederherstellen des Schreibzugriffs

### <a name="on-the-coordinator-node"></a>Für den Koordinatorknoten

* [Erhöhen der Speichergröße](howto-hyperscale-scale-grow.md#increase-storage-on-nodes) für den Koordinatorknoten und/oder
* Verteilen lokaler Tabellen auf Workerknoten bzw. Löschen von Daten. Sie müssen `SET SESSION CHARACTERISTICS AS TRANSACTION READ WRITE` ausführen, nachdem Sie eine Verbindung mit der Datenbank hergestellt haben und bevor Sie andere Befehle ausführen.

### <a name="on-a-worker-node"></a>Für einen Workerknoten

* [Erhöhen der Speichergröße](howto-hyperscale-scale-grow.md#increase-storage-on-nodes) für die Workerknoten und/oder
* [Ausgleichen von Daten](howto-hyperscale-scale-rebalance.md) mit anderen Knoten bzw. Löschen einiger Daten
    * Sie müssen für den Workerknoten vorübergehend Lese-/Schreibzugriff festlegen. Sie können eine direkte Verbindung mit Workerknoten herstellen und wie oben beschrieben für `SET SESSION CHARACTERISTICS` den Koordinatorknoten verwenden.

## <a name="prevention"></a>Prävention

Sie sollten eine Benachrichtigung einrichten, damit Sie informiert werden, wenn sich der Serverspeicher dem Schwellenwert nähert. So können Sie rechtzeitig Maßnahmen ergreifen und den Wechsel in den schreibgeschützten Zustand vermeiden. Weitere Informationen finden Sie in der Dokumentation zu [empfohlenen Warnungen](howto-hyperscale-alert-on-metric.md#suggested-alerts).

## <a name="next-steps"></a>Nächste Schritte

* [Richten Sie Azure-Warnungen zur rechtzeitigen Benachrichtigung ein](howto-hyperscale-alert-on-metric.md#suggested-alerts), damit Sie Maßnahmen ergreifen können, bevor die Knoten in den schreibgeschützten Zustand wechseln.
* In der PostgreSQL-Dokumentation erfahren Sie mehr über die [Datenträgernutzung](https://www.postgresql.org/docs/current/diskusage.html).
* Die PostgreSQL-Dokumentation enthält weitere Informationen zu [Sitzungsmerkmalen](https://www.postgresql.org/docs/13/sql-set-transaction.html).
