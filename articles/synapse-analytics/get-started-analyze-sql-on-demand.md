---
title: 'Tutorial: Erste Schritte beim Analysieren von Daten mit einem serverlosen SQL-Pool'
description: In diesem Tutorial erfahren Sie, wie Sie Daten mit einem serverlosen SQL-Pool analysieren und dabei Daten in Spark-Datenbanken verwenden.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 04/15/2021
ms.openlocfilehash: 492f8d42a072418586a6646a9beed2e750849b31
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129996984"
---
# <a name="analyze-data-with-a-serverless-sql-pool"></a>Analysieren von Daten mit einem serverlosen SQL-Pool

In diesem Tutorial erfahren Sie, wie Sie Daten mit einem serverlosen SQL-Pool analysieren. 

## <a name="the-built-in-serverless-sql-pool"></a>Der serverlose SQL-Pool „Built-in“

Mit serverlosen SQL-Pools können Sie SQL verwenden, ohne dass Sie Kapazität reservieren müssen. Die Abrechnung für einen serverlosen SQL-Pool basiert auf der Menge der Daten, die zum Ausführen der Abfrage verarbeitet werden, und nicht auf der Anzahl von Knoten, die zum Ausführen der Abfrage verwendet werden.

Jeder Arbeitsbereich wird mit einem vorkonfigurierten serverlosen SQL-Pool namens **Built-in** (Integriert) bereitgestellt. 

## <a name="analyze-nyc-taxi-data-with-a-serverless-sql-pool"></a>Analysieren von NYC Taxi-Daten mit einem serverlosen SQL-Pool
 
> [!NOTE]
> Stellen Sie sicher, dass Sie die [Beispieldaten im primären Speicherkonto platziert haben](get-started-create-workspace.md#place-sample-data-into-the-primary-storage-account).

1. Navigieren Sie in Synapse Studio zum Hub **Entwickeln**.
1. Erstellen Sie ein neues SQL-Skript.
1. Fügen Sie den folgenden Code in das Skript ein:

    ```sql
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
            BULK 'https://contosolake.dfs.core.windows.net/users/NYCTripSmall.parquet',
            FORMAT='PARQUET'
        ) AS [result]
    ```
1. Klicken Sie auf **Ausführen**. 

Die Datenuntersuchung ist nur ein vereinfachtes Szenario, anhand dessen Sie die grundlegenden Merkmale Ihrer Daten nachvollziehen können. Weitere Informationen zur Datenuntersuchung und -analyse finden Sie in [diesem Tutorial](sql/tutorial-data-analyst.md).

## <a name="create-data-exploration-database"></a>Erstellen einer Datenbank für die Datenuntersuchung

Sie können den Inhalt der Dateien direkt über die `master`-Datenbank durchsuchen. Bei einigen einfachen Szenarien zur Datenuntersuchung müssen Sie keine separate Datenbank erstellen.
Wenn Sie die Datenuntersuchung fortsetzen, möchten Sie vielleicht jedoch einige Hilfsprogrammobjekte erstellen, etwa:
- Externe Datenquellen, die die benannten Verweise für Speicherkonten darstellen
- Datenbankweit gültige Anmeldeinformationen, mit denen Sie angeben können, wie die Authentifizierung bei einer externen Datenquelle durchgeführt werden soll
- Datenbankbenutzer mit Zugriffsberechtigungen für einige Datenquellen oder Datenbankobjekte
- Hilfsprogrammsichten, -prozeduren und -funktionen, die Sie in den Abfragen verwenden können

1. Verwenden Sie die `master`-Datenbank, um eine separate Datenbank für benutzerdefinierte Datenbankobjekte zu erstellen. Benutzerdefinierte Datenbankobjekte können nicht in der `master`-Datenbank erstellt werden.

    ```sql
    CREATE DATABASE DataExplorationDB 
                    COLLATE Latin1_General_100_BIN2_UTF8
    ```

   > [!IMPORTANT]
   > Verwenden Sie eine Sortierung mit dem Suffix `_UTF8`, um sicherzustellen, dass UTF-8-Text ordnungsgemäß in Spalten vom Typ `VARCHAR` konvertiert wird. `Latin1_General_100_BIN2_UTF8` bietet die beste Leistung bei Abfragen, die Daten aus Parquet-Dateien und Cosmos DB-Containern lesen.

2. Wechseln Sie zu `DataExplorationDB`. Dort können Sie Hilfsprogrammobjekte wie Anmeldeinformationen und Datenquellen erstellen.

    ```sql
    CREATE EXTERNAL DATA SOURCE ContosoLake
    WITH ( LOCATION = 'https://contosolake.dfs.core.windows.net')
    ```

   > [!NOTE]
   > Eine externe Datenquelle kann ohne Anmeldeinformationen erstellt werden. Wenn keine Anmeldeinformationen vorhanden sind, wird die Identität des Aufrufers für den Zugriff auf die externe Datenquelle verwendet.

3. Erstellen Sie mithilfe der master-Datenbank optional eine Anmeldung für einen Benutzer in `DataExplorationDB`, der auf externe Daten zugreift:

    ```sql
    CREATE LOGIN data_explorer WITH PASSWORD = 'My Very Strong Password 1234!';
    ```

    Erstellen Sie anschließend einen Datenbankbenutzer in `DataExplorationDB` für die Anmeldung, und erteilen Sie die Berechtigung `ADMINISTER DATABASE BULK OPERATIONS`.
    ```sql
    CREATE USER data_explorer FOR LOGIN data_explorer;
    GO
    GRANT ADMINISTER DATABASE BULK OPERATIONS TO data_explorer;
    GO
    ```

4. Untersuchen Sie den Inhalt der Datei mithilfe des relativen Pfads und der Datenquelle:

    ```sql
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
                BULK '/users/NYCTripSmall.parquet',
                DATA_SOURCE = 'ContosoLake',
                FORMAT='PARQUET'
        ) AS [result]
    ```

Die Datenbank für die Datenuntersuchung ist nur ein einfacher Platzhalter, in dem Sie Ihre Hilfsprogrammobjekte speichern können. Mit einem Synapse SQL-Pool haben Sie noch weitere Möglichkeiten und können ein logisches Data Warehouse erstellen – eine relationale Ebene, der Azure-Datenquellen zugrunde liegen. Weitere Informationen zum Erstellen eines logischen Data Warehouse finden Sie in [diesem Tutorial](sql/tutorial-data-analyst.md).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Analysieren von Daten mit einem serverlosen Spark-Pool](get-started-analyze-spark.md)
