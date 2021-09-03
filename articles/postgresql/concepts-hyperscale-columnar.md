---
title: 'Spaltenbasierter Tabellenspeicher – Hyperscale (Citus): Azure Database for PostgreSQL'
description: Komprimieren von Daten mithilfe von spaltenbasiertem Speicher
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 08/03/2021
ms.openlocfilehash: 557fe7d2242b67f8fa53541aef25cfc44090e48b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122347032"
---
# <a name="columnar-table-storage"></a>Spaltenbasierter Tabellenspeicher

Azure Database for PostgreSQL: Spaltenbasierter Tabellenspeicher wird von Hyperscale (Citus) nur für Analyse- und Data Warehousing-Workloads unterstützt. Wenn Spalten (anstelle von Zeilen) zusammenhängend auf einem Datenträger gespeichert werden, lassen sich die Daten besser komprimieren, und bei Abfragen kann schneller eine Teilmenge von Spalten angefordert werden.

## <a name="usage"></a>Verwendung

Geben Sie beim Erstellen einer Tabelle `USING columnar` an, um spaltenbasierten Speicher zu verwenden:

```postgresql
CREATE TABLE contestant (
    handle TEXT,
    birthdate DATE,
    rating INT,
    percentile FLOAT,
    country CHAR(3),
    achievements TEXT[]
) USING columnar;
```

Zeilen werden beim Einfügen von Hyperscale (Citus) in Form von „Stripes“ in spaltenbasierten Speicher konvertiert. Jeder Stripe enthält Daten einer einzelnen Transaktion oder 150.000 Zeilen, je nachdem, was geringer ist.  (Die Stripegröße und andere Parameter einer spaltenbasierten Tabelle können mithilfe der Funktion [alter_columnar_table_set](reference-hyperscale-functions.md#alter_columnar_table_set) geändert werden.)

Durch die folgende Anweisung werden beispielsweise alle fünf Zeilen in den gleichen Stripe eingefügt, da alle Werte in einer einzelnen Transaktion eingefügt werden:

```postgresql
-- insert these values into a single columnar stripe

INSERT INTO contestant VALUES
  ('a','1990-01-10',2090,97.1,'XA','{a}'),
  ('b','1990-11-01',2203,98.1,'XA','{a,b}'),
  ('c','1988-11-01',2907,99.4,'XB','{w,y}'),
  ('d','1985-05-05',2314,98.3,'XB','{}'),
  ('e','1995-05-05',2236,98.2,'XC','{a}');
```

Verwenden Sie nach Möglichkeit große Stripes, da spaltenbasierte Daten von Hyperscale (Citus) jeweils separat pro Stripe komprimiert werden. Informationen zu einer spaltenbasierten Tabelle (beispielsweise die Komprimierungsrate, die Anzahl von Stripes und die durchschnittliche Anzahl von Zeilen pro Stripe) können mithilfe von `VACUUM VERBOSE` angezeigt werden:

```postgresql
VACUUM VERBOSE contestant;
```
```
INFO:  statistics for "contestant":
storage id: 10000000000
total file size: 24576, total data size: 248
compression rate: 1.31x
total row count: 5, stripe count: 1, average rows per stripe: 5
chunk count: 6, containing data for dropped columns: 0, zstd compressed: 6
```

Der Ausgabe können Sie entnehmen, dass von Hyperscale (Citus) der zstd-Komprimierungsalgorithmus verwendet wurde, um eine 1,31-fache Datenkomprimierung zu erreichen. Die Komprimierungsrate gibt das Verhältnis zwischen der Größe der eingefügten, im Arbeitsspeicher gestageten Daten und der Größe der gleichen Daten nach der Komprimierung im späteren Stripe an.

Aufgrund der Art der Messung entspricht die Komprimierungsrate möglicherweise nicht dem Größenunterschied zwischen zeilen- und spaltenbasiertem Speicher für eine Tabelle. Zur Ermittlung des tatsächlichen Unterschieds müssen Sie eine zeilen- und eine spaltenbasierte Tabelle mit den gleichen Daten erstellen und sie vergleichen.

## <a name="measuring-compression"></a>Messen der Komprimierung

Erstellen Sie ein neues Beispiel mit mehr Daten, um die Komprimierungseinsparungen zu vergleichen.

```postgresql
-- first a wide table using row storage
CREATE TABLE perf_row(
  c00 int8, c01 int8, c02 int8, c03 int8, c04 int8, c05 int8, c06 int8, c07 int8, c08 int8, c09 int8,
  c10 int8, c11 int8, c12 int8, c13 int8, c14 int8, c15 int8, c16 int8, c17 int8, c18 int8, c19 int8,
  c20 int8, c21 int8, c22 int8, c23 int8, c24 int8, c25 int8, c26 int8, c27 int8, c28 int8, c29 int8,
  c30 int8, c31 int8, c32 int8, c33 int8, c34 int8, c35 int8, c36 int8, c37 int8, c38 int8, c39 int8,
  c40 int8, c41 int8, c42 int8, c43 int8, c44 int8, c45 int8, c46 int8, c47 int8, c48 int8, c49 int8,
  c50 int8, c51 int8, c52 int8, c53 int8, c54 int8, c55 int8, c56 int8, c57 int8, c58 int8, c59 int8,
  c60 int8, c61 int8, c62 int8, c63 int8, c64 int8, c65 int8, c66 int8, c67 int8, c68 int8, c69 int8,
  c70 int8, c71 int8, c72 int8, c73 int8, c74 int8, c75 int8, c76 int8, c77 int8, c78 int8, c79 int8,
  c80 int8, c81 int8, c82 int8, c83 int8, c84 int8, c85 int8, c86 int8, c87 int8, c88 int8, c89 int8,
  c90 int8, c91 int8, c92 int8, c93 int8, c94 int8, c95 int8, c96 int8, c97 int8, c98 int8, c99 int8
);

-- next a table with identical columns using columnar storage
CREATE TABLE perf_columnar(LIKE perf_row) USING COLUMNAR;
```

Füllen Sie beide Tabellen mit demselben großen Dataset aus:

```postgresql
INSERT INTO perf_row
  SELECT
    g % 00500, g % 01000, g % 01500, g % 02000, g % 02500, g % 03000, g % 03500, g % 04000, g % 04500, g % 05000,
    g % 05500, g % 06000, g % 06500, g % 07000, g % 07500, g % 08000, g % 08500, g % 09000, g % 09500, g % 10000,
    g % 10500, g % 11000, g % 11500, g % 12000, g % 12500, g % 13000, g % 13500, g % 14000, g % 14500, g % 15000,
    g % 15500, g % 16000, g % 16500, g % 17000, g % 17500, g % 18000, g % 18500, g % 19000, g % 19500, g % 20000,
    g % 20500, g % 21000, g % 21500, g % 22000, g % 22500, g % 23000, g % 23500, g % 24000, g % 24500, g % 25000,
    g % 25500, g % 26000, g % 26500, g % 27000, g % 27500, g % 28000, g % 28500, g % 29000, g % 29500, g % 30000,
    g % 30500, g % 31000, g % 31500, g % 32000, g % 32500, g % 33000, g % 33500, g % 34000, g % 34500, g % 35000,
    g % 35500, g % 36000, g % 36500, g % 37000, g % 37500, g % 38000, g % 38500, g % 39000, g % 39500, g % 40000,
    g % 40500, g % 41000, g % 41500, g % 42000, g % 42500, g % 43000, g % 43500, g % 44000, g % 44500, g % 45000,
    g % 45500, g % 46000, g % 46500, g % 47000, g % 47500, g % 48000, g % 48500, g % 49000, g % 49500, g % 50000
  FROM generate_series(1,50000000) g;

INSERT INTO perf_columnar
  SELECT
    g % 00500, g % 01000, g % 01500, g % 02000, g % 02500, g % 03000, g % 03500, g % 04000, g % 04500, g % 05000,
    g % 05500, g % 06000, g % 06500, g % 07000, g % 07500, g % 08000, g % 08500, g % 09000, g % 09500, g % 10000,
    g % 10500, g % 11000, g % 11500, g % 12000, g % 12500, g % 13000, g % 13500, g % 14000, g % 14500, g % 15000,
    g % 15500, g % 16000, g % 16500, g % 17000, g % 17500, g % 18000, g % 18500, g % 19000, g % 19500, g % 20000,
    g % 20500, g % 21000, g % 21500, g % 22000, g % 22500, g % 23000, g % 23500, g % 24000, g % 24500, g % 25000,
    g % 25500, g % 26000, g % 26500, g % 27000, g % 27500, g % 28000, g % 28500, g % 29000, g % 29500, g % 30000,
    g % 30500, g % 31000, g % 31500, g % 32000, g % 32500, g % 33000, g % 33500, g % 34000, g % 34500, g % 35000,
    g % 35500, g % 36000, g % 36500, g % 37000, g % 37500, g % 38000, g % 38500, g % 39000, g % 39500, g % 40000,
    g % 40500, g % 41000, g % 41500, g % 42000, g % 42500, g % 43000, g % 43500, g % 44000, g % 44500, g % 45000,
    g % 45500, g % 46000, g % 46500, g % 47000, g % 47500, g % 48000, g % 48500, g % 49000, g % 49500, g % 50000
  FROM generate_series(1,50000000) g;

VACUUM (FREEZE, ANALYZE) perf_row;
VACUUM (FREEZE, ANALYZE) perf_columnar;
```

Für diese Daten sehen Sie ein Komprimierungsverhältnis von mehr als dem 8-fachen in der Spaltentabelle.

```postgresql
SELECT pg_total_relation_size('perf_row')::numeric/
       pg_total_relation_size('perf_columnar') AS compression_ratio;
 compression_ratio
--------------------
 8.0196135873627944
(1 row)
```

## <a name="example"></a>Beispiel

Spaltenbasierter Speicher funktioniert gut mit Tabellenpartitionierung. Ein Beispiel finden Sie in der Communitydokumentation zur Citus-Engine: [Archivieren mit spaltenbasiertem Speicher](https://docs.citusdata.com/en/stable/use_cases/timeseries.html#archiving-with-columnar-storage).

## <a name="gotchas"></a>Häufige Fehler

* Bei spaltenbasiertem Speicher werden die Daten pro Stripe komprimiert. Stripes werden pro Transaktion erstellt. Wenn Sie also eine einzelne Zeile pro Transaktion einfügen, werden die einzelnen Zeilen jeweils in eigenen Stripes platziert. Komprimierung und Leistung einzeiliger Stripes sind schlechter als eine zeilenbasierte Tabelle. Fügen Sie Daten immer per Massenvorgang in eine spaltenbasierte Tabelle ein.
* Wenn Sie den Fehler machen und eine Reihe kleiner Stripes in Spalten konvertieren, kommen Sie nicht mehr weiter.
  In diesem Fall können Sie nur noch eine neue spaltenbasierte Tabelle erstellen und die Originaldaten in einer einzelnen Transaktion kopieren:
  ```postgresql
  BEGIN;
  CREATE TABLE foo_compacted (LIKE foo) USING columnar;
  INSERT INTO foo_compacted SELECT * FROM foo;
  DROP TABLE foo;
  ALTER TABLE foo_compacted RENAME TO foo;
  COMMIT;
  ```
* Grundsätzlich nicht komprimierbare Daten können zwar ein Problem darstellen, spaltenbasierter Speicher ist jedoch auch nützlich, wenn spezifische Spalten ausgewählt werden. Die anderen Spalten müssen nicht in den Arbeitsspeicher geladen werden.
* Bei partitionierten Tabellen mit einer Mischung aus zeilen- und spaltenbasierten Partitionen müssen Aktualisierungen sorgfältig ausgerichtet werden. Verwenden Sie Filter, um den Vorgang auf die Zeilenpartitionen zu beschränken.
   * Wenn der Vorgang auf eine bestimmte Zeilenpartition (beispielsweise `UPDATE p2 SET i = i + 1`) ausgerichtet wird, ist er erfolgreich. Wird er dagegen auf eine bestimmte Spaltenpartition (beispielsweise `UPDATE p1 SET i = i + 1`) ausgerichtet, tritt ein Fehler auf.
   * Wenn der Vorgang auf die partitionierte Tabelle ausgerichtet wird und über eine WHERE-Klausel verfügt, durch die alle Spaltenpartitionen ausgeschlossen werden (beispielsweise `UPDATE parent SET i = i + 1 WHERE timestamp = '2020-03-15'`), ist er erfolgreich.
   * Wird der Vorgang auf die partitionierte Tabelle ausgerichtet, ohne jedoch nach den Partitionsschlüsselspalten zu filtern, tritt ein Fehler auf. Selbst wenn WHERE-Klauseln vorhanden sind, die dafür sorgen, dass Zeilen nur in Spaltenpartitionen abgeglichen werden, reicht das nicht aus. Der Partitionsschlüssel muss ebenfalls gefiltert werden.

## <a name="limitations"></a>Einschränkungen

Dieses Feature weist weiterhin erhebliche Einschränkungen auf. Weitere Informationen finden Sie unter [Spaltenbasierter Speicher](concepts-hyperscale-limits.md#columnar-storage).

## <a name="next-steps"></a>Nächste Schritte

* Ein Beispiel für spaltenbasierten Speicher finden Sie in einem [Zeitreihentutorial](https://docs.citusdata.com/en/stable/use_cases/timeseries.html#archiving-with-columnar-storage) (externer Link) von Citus.
