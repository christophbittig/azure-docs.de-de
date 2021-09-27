---
title: Ermitteln der Tabellengröße mit Hyperscale (Citus) – Azure Database for PostgreSQL
description: Ermitteln der tatsächlichen Größe von verteilten Tabellen in einer Hyperscale (Citus)-Servergruppe
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 1/5/2021
ms.openlocfilehash: 10426baece6b14984dc10a6eadc1d5fc19db382c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631891"
---
# <a name="determine-table-and-relation-size"></a>Ermitteln der Tabellen- und Beziehungsgröße

Beim üblichen Verfahren zum Ermitteln von Tabellengrößen in PostgreSQL mit `pg_total_relation_size` wird die Größe von verteilten Tabellen in Hyperscale (Citus) viel zu gering angegeben.
Diese Funktion gibt in einer Hyperscale (Citus)-Servergruppe nur Aufschluss über die Größe der Tabellen auf dem Koordinatorknoten.  Tatsächlich befinden sich die Daten in verteilten Tabellen aber auf den Workerknoten (in Shards), nicht auf dem Koordinatorknoten. Ein richtiges Maß für die Größe einer verteilten Tabelle ist die Summe der Shardgrößen. Hyperscale (Citus) stellt Hilfsfunktionen bereit, mit denen diese Informationen abgefragt werden können.

<table>
<colgroup>
<col width="40%" />
<col width="59%" />
</colgroup>
<thead>
<tr class="header">
<th>Funktion</th>
<th>Gibt zurück</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>citus_relation_size(relation_name)</td>
<td><ul>
<li>Größe der tatsächlichen Daten in der Tabelle (dem <a href="https://www.postgresql.org/docs/current/static/storage-file-layout.html">Hauptfork</a>).</li>
<li>Eine Beziehung kann der Name einer Tabelle oder eines Indexes sein.</li>
</ul></td>
</tr>
<tr class="even">
<td>citus_table_size(relation_name)</td>
<td><ul>
<li><p>citus_relation_size plus:</p>
<blockquote>
<ul>
<li>Größe der <a href="https://www.postgresql.org/docs/current/static/storage-fsm.html">Zuordnung des freien Speicherplatzes</a></li>
<li>Größe der <a href="https://www.postgresql.org/docs/current/static/storage-vm.html">Sichtbarkeitszuordnung</a></li>
</ul>
</blockquote></li>
</ul></td>
</tr>
<tr class="odd">
<td>citus_total_relation_size(relation_name)</td>
<td><ul>
<li><p>citus_table_size plus:</p>
<blockquote>
<ul>
<li>Größe der Indizes</li>
</ul>
</blockquote></li>
</ul></td>
</tr>
</tbody>
</table>

Diese Funktionen sind analog zu drei der standardmäßigen [Objektgrößefunktionen](https://www.postgresql.org/docs/current/static/functions-admin.html#FUNCTIONS-ADMIN-DBSIZE) von PostgreSQL, mit einer Ausnahme: Wenn keine Verbindung mit einem Knoten hergestellt werden kann, tritt ein Fehler auf.

## <a name="example"></a>Beispiel

So listen Sie die Größen aller verteilten Tabellen auf:

``` postgresql
SELECT logicalrelid AS name,
       pg_size_pretty(citus_table_size(logicalrelid)) AS size
  FROM pg_dist_partition;
```

Ausgabe:

```
┌───────────────┬───────┐
│     name      │ size  │
├───────────────┼───────┤
│ github_users  │ 39 MB │
│ github_events │ 37 MB │
└───────────────┴───────┘
```

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie [eine Servergruppe skalieren](howto-hyperscale-scale-grow.md), damit mehr Daten gespeichert werden können.
* Lernen Sie die Unterschiede zwischen den [Tabellentypen](concepts-hyperscale-nodes.md) in einer Hyperscale (Citus)-Servergruppe kennen.
