---
title: Ausgleichen von Shards – Hyperscale (Citus) – Azure Database for PostgreSQL
description: Verteilen Sie Shards gleichmäßig auf Server, um die Leistung zu verbessern.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 07/20/2021
ms.openlocfilehash: 19b44306185dcfcdd3675898b9066019f686dc75
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114463555"
---
# <a name="rebalance-shards-in-hyperscale-citus-server-group"></a>Ausgleichen von Shards in einer Hyperscale (Citus)-Servergruppe

Um neu hinzugefügte Knoten optimal zu nutzen, müssen Sie die verteilte [Shard](concepts-hyperscale-distributed-data.md#shards)tabelle ausgleichen. Das bedeutet, dass einige Shards von vorhandenen Knoten auf neue Knoten verschoben werden. Hyperscale (Citus) bietet einen Ausgleich ohne Ausfallzeiten, d. h. Abfragen können während des Ausgleichens von Shards ohne Unterbrechung ausgeführt werden.

## <a name="determine-if-the-server-group-needs-a-rebalance"></a>Ermitteln, ob für die Servergruppe ein Ausgleich erforderlich ist

Über das Azure-Portal können Sie anzeigen, ob Daten gleichmäßig auf Workerknoten in einer Servergruppe verteilt werden. Wechseln Sie dazu im Menü **Servergruppenverwaltung** zur Seite **Shard rebalancer** (Shardausgleich). Wenn Daten zwischen Workern verzerrt werden, wird die Meldung **Rebalancing is recommended** (Ausgleich wird empfohlen) zusammen mit einer Liste der Größe der einzelnen Knoten angezeigt.

Wenn die Daten bereits ausgeglichen sind, wird die Meldung **Rebalancing is not recommended at this time** (Ausgleich wird zurzeit nicht empfohlen) angezeigt.

## <a name="run-the-shard-rebalancer"></a>Ausführen des Shardausgleichs

Zum Starten des Shardausgleichs müssen Sie eine Verbindung mit dem Koordinatorknoten der Servergruppe herstellen und die SQL-Funktion [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) für verteilte Tabellen ausführen. Die Funktion gleicht alle Tabellen in der Gruppe [Colocation](concepts-hyperscale-colocation.md) der im Argument genannten Tabelle aus. Daher müssen Sie die Funktion nicht für jede verteilte Tabelle aufrufen, sondern einfach nur für eine repräsentative Tabelle aus jeder Zusammenstellungsgruppe.

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

## <a name="monitor-rebalance-progress"></a>Überwachen des Ausgleichsfortschritts

Wenn Sie den Ausgleich nach dem Start überwachen möchten, wechseln Sie zum Azure-Portal. Öffnen Sie im Menü **Servergruppenverwaltung** die Seite **Shard rebalancer** (Shardausgleich). Die Meldung **Rebalancing is underway** (Ausgleich wird ausgeführt) wird zusammen mit zwei Tabellen angezeigt.

Die erste Tabelle zeigt die Anzahl der Shards, die in einen oder aus einem Knoten verschoben werden, z. B. „6 von 24 verschoben“. Die zweite Tabelle zeigt den Fortschritt pro Datenbanktabelle an: Name, betroffene Shardanzahl, betroffene Datengröße und Ausgleichsstatus.

Wählen Sie die Schaltfläche **Aktualisieren** aus, um die Seite zu aktualisieren. Wenn der Ausgleich abgeschlossen ist, wird erneut die Meldung **Rebalancing is not recommended at this time** angezeigt.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Leistungsoptionen](concepts-hyperscale-configuration-options.md) für Servergruppen.
- [Zentrales oder horizontales Hochskalieren einer Servergruppe](howto-hyperscale-scale-grow.md)
- Weitere Informationen finden Sie in den Referenzmaterialien zur [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards)-Funktion.
