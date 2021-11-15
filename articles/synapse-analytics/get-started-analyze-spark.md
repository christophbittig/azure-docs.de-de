---
title: 'Schnellstart: Erste Schritte beim Analysieren mit Spark'
description: In diesem Tutorial erfahren Sie, wie Sie Daten mit Apache Spark analysieren.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 03/24/2021
ms.openlocfilehash: e0d430c393637ff11e7315716b3fdd9818a61b8b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131467148"
---
# <a name="analyze-with-apache-spark"></a>Analysieren mit Apache Spark

In diesem Tutorial werden die grundlegenden Schritte zum Laden und Analysieren von Daten mit Apache Spark für Azure Synapse beschrieben.

## <a name="create-a-serverless-apache-spark-pool"></a>Erstellen eines serverlosen Apache Spark-Pools

1. Wählen Sie in Synapse Studio im linken Bereich die Option **Verwalten** > **Apache Spark-Pools** aus.
1. Wählen Sie **Neu** aus. 
1. Geben Sie unter **Name des Apache Spark-Pools** den Namen **Spark1** ein.
1. Geben Sie unter **Knotengröße** die Größe **Klein** ein.
1. Legen Sie unter **Knotenanzahl** den Mindestwert auf 3 und den Höchstwert auf 3 fest.
1. Wählen Sie **Bewerten + erstellen** > **Erstellen** aus. Ihr Apache Spark-Pool steht in wenigen Sekunden zur Verfügung.

## <a name="understanding-serverless-apache-spark-pools"></a>Grundlegendes zu serverlosen Apache Spark-Pools

Mit einem serverlosen Spark-Pool kann angegeben werden, wie ein Benutzer mit Spark arbeiten möchte. Wenn Sie mit der Verwendung eines Pools beginnen, wird bei Bedarf eine Spark-Sitzung erstellt. Der Pool steuert, wie viele Spark-Ressourcen von dieser Sitzung verwendet werden und wie lange die Sitzung dauert, bevor sie automatisch angehalten wird. Sie bezahlen für Spark-Ressourcen, die während der Sitzung verwendet werden, nicht für den Pool selbst. Auf diese Weise können Sie in einem Spark-Pool mit Spark arbeiten, ohne sich mit der Verwaltung von Clustern auseinandersetzen zu müssen. Dies ist vergleichbar mit einem serverlosen SQL-Pool.

## <a name="analyze-nyc-taxi-data-with-a-spark-pool"></a>Analysieren von NYC Taxi-Daten mit einem Spark-Pool

1. Navigieren Sie in Synapse Studio zum Hub **Entwickeln**.
2. Erstellen eines neuen Notebooks
3. Erstellen Sie eine neue Codezelle, und fügen Sie den folgenden Code in diese Zelle ein:
    ```py
    %%pyspark
    df = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTripSmall.parquet', format='parquet')
    display(df.limit(10))
    ```
1. Wählen Sie im Notebook im Menü **Anfügen an** den zuvor erstellten serverlosen Spark-Pool **Spark1** aus.
1. Wählen Sie für die Zelle **Ausführen** aus. Synapse startet bei Bedarf eine neue Spark-Sitzung zur Ausführung dieser Zelle. Ist eine neue Spark-Sitzung erforderlich, dauert ihre Erstellung ungefähr zwei Sekunden. 
1. Wenn Sie nur das Schema des Datenrahmens anzeigen möchten, führen Sie eine Zelle mit dem folgenden Code aus:

    ```py
    %%pyspark
    df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Laden der NYC Taxi-Daten in die Spark-Datenbank „nyctaxi“

Daten sind über den Datenrahmen mit dem Namen **df** verfügbar. Laden Sie sie in eine Spark-Datenbank mit dem Namen **nyctaxi**.

1. Fügen Sie dem Notebook eine neue Codezelle hinzu, und geben Sie dann den folgenden Code ein:

    ```py
    %%pyspark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```
## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analysieren der NYC Taxi-Daten mithilfe von Spark und Notebooks

1. Erstellen Sie eine neue Codezelle, und geben Sie den folgenden Code ein: 

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Führen Sie die Zelle aus, um die NYC Taxi-Daten anzuzeigen, die Sie in die Spark-Datenbank **nyctaxi** geladen haben.
1. Erstellen Sie eine neue Codezelle, und geben Sie den folgenden Code ein: Diese Daten werden analysiert, und die Ergebnisse werden in einer Tabelle namens **nyctaxi.passengercountstats** gespeichert.

   ```py
   %%pyspark
   df = spark.sql("""
      SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
      FROM nyctaxi.trip
      WHERE TripDistanceMiles > 0 AND PassengerCount > 0
      GROUP BY PassengerCount
      ORDER BY PassengerCount
   """) 
   display(df)
   df.write.saveAsTable("nyctaxi.passengercountstats")
   ```

1. Wählen Sie in den Zellenergebnissen **Diagramm** aus, um die visualisierten Daten anzuzeigen.


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Analysieren von Daten mit einem dedizierten SQL-Pool](get-started-analyze-sql-pool.md)
