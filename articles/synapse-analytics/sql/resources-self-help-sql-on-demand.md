---
title: Selbsthilfe für serverlose SQL-Pools
description: Dieser Abschnitt enthält Informationen, die Sie bei der Behandlung von Problemen mit einem serverlosen SQL-Pool unterstützen.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 8/31/2021
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 906f6a7a8e64c255c8b87219ef0549a553821783
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/07/2021
ms.locfileid: "123536479"
---
# <a name="self-help-for-serverless-sql-pool"></a>Selbsthilfe für serverlose SQL-Pools

In diesem Artikel erfahren Sie, wie Sie die häufigsten Probleme mit einem serverlosen SQL-Pool in Azure Synapse Analytics behandeln.

## <a name="synapse-studio"></a>Synapse Studio

### <a name="serverless-sql-pool-is-grayed-out-in-synapse-studio"></a>Serverloser SQL-Pool (Vorschauversion) in Synapse Studio abgeblendet

Wenn von Synapse Studio keine Verbindung mit einem serverlosen SQL-Pool hergestellt werden kann, ist der serverlose SQL-Pool abgeblendet oder hat den Status „Offline“. Dieses Problem tritt in der Regel in einem der folgenden Fälle auf:

1) Ihr Netzwerk verhindert die Kommunikation mit dem Azure Synapse-Back-End. Dies ist meistens auf eine Blockierung des Ports 1443 zurückzuführen. Heben Sie die Blockierung dieses Ports auf, damit ein serverloser SQL-Pool verwendet werden kann. Es gibt auch noch andere Probleme, die dazu führen können, dass ein serverloser SQL-Pool nicht funktioniert. Weitere Informationen finden Sie im [vollständigen Leitfaden zur Problembehandlung](../troubleshoot/troubleshoot-synapse-studio.md).
2) Sie verfügen über keine Anmeldeberechtigungen für einen serverlosen SQL-Pool. Um Zugriff zu erhalten, muss Sie einer der Azure Synapse-Arbeitsbereichsadministratoren der Arbeitsbereichsadministrator- oder SQL-Administratorrolle hinzufügen. [Weitere Informationen finden Sie im vollständigen Leitfaden zur Zugriffssteuerung.](../security/synapse-workspace-access-control-overview.md)

### <a name="query-fails-with-error-websocket-connection-was-closed-unexpectedly"></a>Fehler für Abfrage: Websocketverbindung wurde unerwartet geschlossen.

Das Auftreten der Fehlermeldung „Websocketverbindung wurde unerwartet geschlossen“ bedeutet, dass Ihre Browserverbindung mit Synapse Studio unterbrochen wurde, z. B. aufgrund eines Netzwerkproblems. 

Führen Sie die Abfrage erneut aus, um dieses Problem zu beheben. Falls diese Meldung in Ihrer Umgebung häufiger auftritt, sollten Sie Ihren Netzwerkadministrator um Hilfe bitten, die Firewalleinstellungen überprüfen und [diesen Leitfaden zur Problembehandlung](../troubleshoot/troubleshoot-synapse-studio.md) verwenden, um weitere Informationen zu erhalten. 

Falls das Problem weiterhin besteht, sollten Sie über das Azure-Portal ein [Supportticket](../../azure-portal/supportability/how-to-create-azure-support-request.md) erstellen und zur weiteren Untersuchung versuchen, die gleichen Abfragen nicht über Synapse Studio, sondern über [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) oder [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) auszuführen.

## <a name="query-execution"></a>Abfrageausführung

### <a name="query-fails-because-file-cannot-be-opened"></a>Abfrage nicht erfolgreich, da Datei nicht geöffnet werden kann

Wenn bei Ihrer Abfrage ein Fehler mit dem Hinweis auftritt, dass die Datei nicht geöffnet werden kann, da sie nicht vorhanden ist oder von einem anderen Prozess verwendet wird, Sie aber sicher sind, dass die Datei vorhanden ist und nicht von einem anderen Prozess verwendet wird, kann vom serverlosen SQL-Pool nicht auf die Datei zugegriffen werden. Dieses Problem tritt in der Regel auf, weil Ihre Azure Active Directory-Identität keine Zugriffsberechtigungen für die Datei hat oder weil eine Firewall den Zugriff auf die Datei blockiert. Vom serverlosen SQL-Pool wird standardmäßig versucht, unter Verwendung Ihrer Azure Active Directory-Identität auf die Datei zuzugreifen. Um dieses Problem zu beheben, benötigen Sie die entsprechenden Zugriffsrechte für die Datei. Dies können Sie am einfachsten erreichen, indem Sie sich die Rolle „Mitwirkender an Storage-Blobdaten“ für das Speicherkonto zuweisen, für das Sie Abfragen ausführen möchten. 
- [Weitere Informationen finden Sie im vollständigen Leitfaden zur Azure Active Directory-Zugriffssteuerung für Speicher.](../../storage/blobs/assign-azure-role-data-access.md) 
- Besuchen Sie [Steuern des Speicherkontozugriffs für einen serverlosen SQL-Pool in Azure Synapse Analytics](develop-storage-files-storage-access-control.md).

#### <a name="alternative-to-storage-blob-data-contributor-role"></a>Alternative zur Rolle „Mitwirkender an Storage-Blobdaten“

Anstatt die Rolle „Mitwirkender an Storage-Blobdaten“ zu gewähren, können Sie auch für eine Teilmenge der Dateien präzisere Berechtigungen erteilen. 

* Alle Benutzer, die Zugriff auf einige Daten in diesem Container benötigen, müssen auch über die Ausführungsberechtigung (EXECUTE) für alle übergeordneten Ordner bis hinauf zum Stammordner (Container) verfügen. Lesen Sie die weiteren Informationen dazu, [wie Sie ACLs in Azure Data Lake Storage Gen2 festlegen](../../storage/blobs/data-lake-storage-explorer-acl.md). 

> [!NOTE]
> Die Ausführungsberechtigung auf der Containerebene muss innerhalb von Azure Data Lake Gen2 festgelegt werden.
> Berechtigungen für den Ordner können in Synapse festgelegt werden. 


Wenn Sie in diesem Beispiel „data2.csv“ abfragen möchten, werden die folgenden Berechtigungen benötigt: 
   - Ausführungsberechtigung für Container
   - Ausführungsberechtigung für „folder1“ 
   - Leseberechtigung für „data2.csv“

![Zeichnung mit Berechtigungsstruktur für Data Lake](./media/resources-self-help-sql-on-demand/folder-structure-data-lake.png)

* Melden Sie sich bei Synapse als Administratorbenutzer an, der über die vollständigen Berechtigungen für die Daten verfügt, auf die Sie zugreifen möchten.

* Klicken Sie im Datenbereich mit der rechten Maustaste auf die Datei, und wählen Sie „ZUGRIFF VERWALTEN“ aus.

![Screenshot: Benutzeroberfläche für die Verwaltung des Zugriffs](./media/resources-self-help-sql-on-demand/manage-access.png)

* Wählen Sie mindestens die Berechtigung „Lesen“ aus, geben Sie den Benutzerprinzipalnamen oder die Objekt-ID des Benutzers ein, z. B. user@contoso.com, und klicken Sie auf „Hinzufügen“.

* Erteilen Sie diesem Benutzer die Leseberechtigung.
![Screenshot: Benutzeroberfläche zum Erteilen von Leseberechtigungen](./media/resources-self-help-sql-on-demand/grant-permission.png)

> [!NOTE]
> Für Gastbenutzer muss hierfür direkt der Azure Data Lake-Dienst verwendet werden, da die direkte Nutzung von Synapse nicht möglich ist. 

### <a name="query-fails-because-it-cannot-be-executed-due-to-current-resource-constraints"></a>Abfrage nicht erfolgreich, da sie aufgrund von aktuellen Ressourceneinschränkungen nicht ausgeführt werden kann 

Wenn bei Ihrer Abfrage ein Fehler mit der Beschreibung auftritt, dass die Abfrage aufgrund von aktuellen Ressourceneinschränkungen nicht ausgeführt werden kann, gibt es in diesem Moment Ressourceneinschränkungen, die verhindern, dass der serverlose SQL-Pool die Abfrage ausführt: 

- Vergewissern Sie sich, dass Datentypen mit passener Größe verwendet werden.  

- Bei Abfragen für Parquet-Dateien empfiehlt sich ggf. das Definieren von expliziten Typen für Zeichenfolgenspalten, da für sie standardmäßig „VARCHAR(8000)“ verwendet wird. [Abgeleitete Datentypen überprüfen](./best-practices-serverless-sql-pool.md#check-inferred-data-types)

- Bei Abfragen für CSV-Dateien empfiehlt sich ggf. das [Erstellen von Statistiken](develop-tables-statistics.md#statistics-in-serverless-sql-pool). 

- Informationen zur Abfrageoptimierung finden Sie in den [bewährten Methoden zum Verbessern der Leistung von serverlosen SQL-Pools](./best-practices-serverless-sql-pool.md).  

### <a name="could-not-allocate-tempdb-space-while-transferring-data-from-one-distribution-to-another"></a>tempdb-Speicherplatz konnte beim Übertragen von Daten von einer Verteilung in eine andere nicht zugeordnet werden.

Dieser Fehler ist ein Sonderfall im Hinblick auf generische [Abfragen, da die Abfrage aufgrund von aktuellen Ressourceneinschränkungen](#query-fails-because-it-cannot-be-executed-due-to-current-resource-constraints) nicht ausgeführt werden kann. Dieser Fehler wird zurückgegeben, wenn die der `tempdb` Datenbank zugeordneten Ressourcen nicht ausreichen, um die Abfrage auszuführen. 

Wenden Sie die gleichen Abhilfemaßnahmen und bewährten Methoden an, bevor Sie ein Supportticket erstellen.

### <a name="query-fails-with-error-while-handling-an-external-file"></a>Fehler für Abfrage beim Verarbeiten einer externen Datei. 

Das Auftreten einer Fehlermeldung der Art „Fehler beim Verarbeiten einer externen Datei: Maximale Fehleranzahl erreicht“ bedeutet, dass ein Konflikt in Bezug auf einen angegebenen Spaltentyp und die zu ladenden Daten besteht. Um weitere Informationen zum Fehler und zu den relevanten Zeilen und Spalten zu erhalten, sollten Sie die Parserversion von „2.0“ in „1.0“ ändern. 

#### <a name="example"></a>Beispiel
Wenn Sie die Datei „names.csv“ mit dieser Abfrage 1 abfragen möchten, wird von Synapse SQL (serverlos) dieser Fehler zurückgegeben. 

names.csv
```csv
Id,first name, 
1,Adam
2,Bob
3,Charles
4,David
5,Eva
```

Abfrage 1:
```sql
SELECT
    TOP 100 *
FROM
    OPENROWSET(
        BULK '[FILE-PATH OF CSV FILE]',
        FORMAT = 'CSV',
        PARSER_VERSION='2.0',
       FIELDTERMINATOR =';',
       FIRSTROW = 2
    ) 
    WITH (
    [ID] SMALLINT, 
    [Text] VARCHAR (1) COLLATE Latin1_General_BIN2 
)

    AS [result]
```
Ursachen:

```Error handling external file: ‘Max error count reached’. File/External table name: [filepath].```

Nachdem die Parserversion von Version 2.0 in Version 1.0 geändert wurde, dienen die Fehlermeldungen als Hilfe beim Identifizieren des Problems. Die neue Fehlermeldung lautet jetzt wie folgt: 

```Bulk load data conversion error (truncation) for row 1, column 2 (Text) in data file [filepath]```

Die Kürzung ist ein Hinweis darauf, dass der Spaltentyp zu klein für unsere Daten ist. Der längste Vorname in der Datei „names.csv“ hat sieben Zeichen. Aus diesem Grund sollte mindestens „VARCHAR(7)“ als Datentyp verwendet werden. Der Fehler wird durch diese Codezeile verursacht: 

```sql 
    [Text] VARCHAR (1) COLLATE Latin1_General_BIN2
```
Wenn Sie die Abfrage entsprechend ändern, wird der Fehler behoben: Ändern Sie nach dem Debuggen die Parserversion wieder in „2.0“, um die höchste Leistung zu erzielen. Weitere Informationen dazu, wann welche Parserversion verwendet werden sollte, finden Sie [hier](develop-openrowset.md). 

```sql 
SELECT
    TOP 100 *
FROM
    OPENROWSET(
        BULK '[FILE-PATH OF CSV FILE]',
        FORMAT = 'CSV',
        PARSER_VERSION='2.0',
        FIELDTERMINATOR =';',
        FIRSTROW = 2
    ) 
    WITH (
    [ID] SMALLINT, 
    [Text] VARCHAR (7) COLLATE Latin1_General_BIN2 
)

    AS [result]
```

### <a name="query-fails-with-conversion-error"></a>Konvertierungsfehler bei Abfrage
Eine Fehlermeldung der Art „Datenkonvertierungsfehler (Typkonflikte oder ungültiges Zeichen für die angegebene Codeseite) beim Massenladen für Zeile „n“, Spalte „m“ [Spaltenname] in der Datendatei [Dateipfad]“ für Ihre Abfrage bedeutet, dass Ihre Datentypen nicht zu den tatsächlichen Daten für die Zeilennummer „n“ und die Spalte „m“ gepasst haben. 

Falls Sie beispielsweise erwarten, dass Ihre Daten nur ganze Zahlen (Integer) enthalten, während Zeile „n“ aber ggf. eine Zeichenfolge enthält, erhalten Sie die folgende Fehlermeldung. Untersuchen Sie die Datei und die entsprechenden Datentypen, die Sie ausgewählt haben, um dieses Problem zu beheben. Überprüfen Sie auch, ob Ihre Einstellungen für Zeilentrennzeichen und Feldabschlusszeichen korrekt sind. Das folgende Beispiel zeigt, wie Sie die Überprüfung mit „VARCHAR“ als Spaltentyp durchführen können. Weitere Informationen zu Feldabschlusszeichen und Zeilentrennzeichen und zum Versehen von Anführungszeichen mit Escapezeichen finden Sie [hier](query-single-csv-file.md). 

#### <a name="example"></a>Beispiel 
Wenn Sie die Datei „names.csv“ mit dieser Abfrage 1 abfragen möchten, wird von Synapse SQL (serverlos) dieser Fehler zurückgegeben. 

names.csv
```csv
Id, first name, 
1,Adam
2,Bob
3,Charles
4,David
five,Eva
```

Abfrage 1:
```sql 
SELECT
    TOP 100 *
FROM
    OPENROWSET(
        BULK '[FILE-PATH OF CSV FILE]',
        FORMAT = 'CSV',
        PARSER_VERSION='1.0',
       FIELDTERMINATOR =',',
       FIRSTROW = 2
    ) 
    WITH (
    [ID] SMALLINT, 
    [Firstname] VARCHAR (25) COLLATE Latin1_General_BIN2 
)

    AS [result]
```

Verursacht den folgenden Fehler: ```Bulk load data conversion error (type mismatch or invalid character for the specified codepage) for row 6, column 1 (ID) in data file [filepath]```

Für die Behandlung dieses Problems ist es erforderlich, die Daten zu durchsuchen und eine fundierte Entscheidung zu treffen. Damit die Daten, die dieses Problem verursachen, untersucht werden können, muss zunächst der Datentyp geändert werden. Anstatt die Spalte „ID“ mit dem Datentyp „SMALLINT“ abzufragen, wird nun „VARCHAR(100)“ verwendet, um dieses Problem zu analysieren. Mit der geringfügig geänderten Abfrage 2 können die Daten jetzt verarbeitet werden, und die Liste mit den Namen wird angezeigt. 

Abfrage 2: 
```sql
SELECT
    TOP 100 *
FROM
    OPENROWSET(
        BULK '[FILE-PATH OF CSV FILE]',
        FORMAT = 'CSV',
        PARSER_VERSION='1.0',
       FIELDTERMINATOR =',',
       FIRSTROW = 2
    ) 
    WITH (
    [ID] VARCHAR(100), 
    [Firstname] VARCHAR (25) COLLATE Latin1_General_BIN2 
)

    AS [result]
```

names.csv
```csv
Id, first name, 
1,Adam
2,Bob
3,Charles
4,David
five,Eva
```

Es sieht so aus, als ob die Daten unerwartete Werte für „ID“ in der fünften Zeile enthalten. Unter solchen Umständen ist es wichtig, sich mit dem geschäftlichen Besitzer der Daten darüber abzustimmen, wie das Auftreten von beschädigten Daten dieser Art vermieden werden kann. Falls die Verhinderung auf Anwendungsebene nicht möglich ist und mehrere unterschiedliche Datentypen für „ID“ verarbeitet werden müssen, ist die Verwendung von „VARCHAR“ mit einer angemessenen Größe hier ggf. die einzige Option.

> [!Tip]
> Versuchen Sie, die Größe für „VARCHAR()“ möglichst gering zu halten. Vermeiden Sie nach Möglichkeit die Verwendung von „VARCHAR(MAX)“, da dies die Leistung beeinträchtigen kann. 

### <a name="the-result-table-does-not-look-like-expected-result-columns-are-empty-or-unexpected-loaded"></a>Die Ergebnistabelle sieht nicht wie erwartet aus. Ergebnisspalten sind leer oder werden unerwartet geladen. 

Wenn für Ihre Abfrage kein Fehler auftritt, Sie aber feststellen, dass die Ergebnistabelle nicht wie erwartet geladen wird, besteht die Ursache wahrscheinlich darin, dass das Zeilentrennzeichen oder Feldabschlusszeichen falsch ausgewählt wurde. Um dieses Problem zu beheben, müssen Sie sich die Daten noch einmal ansehen und die entsprechenden Einstellungen ändern. Da eine Ergebnistabelle angezeigt wird, ist das Debuggen dieser Abfrage einfach (siehe Beispiel unten). 

#### <a name="example"></a>Beispiel
Wenn Sie die Datei „names.csv“ mit dieser Abfrage 1 abfragen möchten, wird von Synapse SQL (serverlos) eine Ergebnistabelle zurückgegeben, die ungewöhnlich aussieht. 

names.csv
```csv
Id,first name, 
1,Adam
2,Bob
3,Charles
4,David
5,Eva
```

```sql
SELECT
    TOP 100 *
FROM
    OPENROWSET(
        BULK '[FILE-PATH OF CSV FILE]',
        FORMAT = 'CSV',
        PARSER_VERSION='1.0',
       FIELDTERMINATOR =';',
       FIRSTROW = 2
    ) 
    WITH (
    [ID] VARCHAR(100), 
    [Firstname] VARCHAR (25) COLLATE Latin1_General_BIN2 
)

    AS [result]
```

Führt zu dieser Ergebnistabelle:

| ID            |   firstname   | 
| ------------- |-------------  | 
| 1,Adam        | NULL | 
| 2,Bob         | NULL | 
| 3,Charles     | NULL | 
| 4,David       | NULL | 
| 5,Eva         | NULL | 

Die Spalte „firstname“ scheint keinen Wert zu enthalten. Stattdessen befinden sich alle Werte in der Spalte „ID“. Die Werte sind jeweils durch ein Komma voneinander getrennt. Das Problem wurde durch diese Codezeile verursacht, da das Komma anstelle des Semikolons als Feldabschlusszeichen ausgewählt werden muss:

```sql
FIELDTERMINATOR =';',
```

Das Problem kann behoben werden, indem nur dieses Zeichen geändert wird:

```sql
FIELDTERMINATOR =',',
```

Die von Abfrage 2 erstellte Ergebnistabelle sieht jetzt wie erwartet aus. 

Abfrage 2:
```sql
SELECT
    TOP 100 *
FROM
    OPENROWSET(
        BULK '[FILE-PATH OF CSV FILE]',
        FORMAT = 'CSV',
        PARSER_VERSION='1.0',
       FIELDTERMINATOR =',',
       FIRSTROW = 2
    ) 
    WITH (
    [ID] VARCHAR(100), 
    [Firstname] VARCHAR (25) COLLATE Latin1_General_BIN2 
)

    AS [result]
``` 

gibt Folgendes zurück:

| ID            |   firstname   | 
| ------------- |-------------  | 
| 1        | Adam | 
| 2         | Bob | 
| 3     | Charles | 
| 4       | David | 
| 5         | Eva | 


### <a name="query-fails-with-error-column-column-name-of-type-type-name-is--not-compatible-with-external-data-type-external-data-type-name"></a>Fehler für Abfrage: Spalte [Spaltenname] vom Typ [Typname] ist nicht kompatibel mit dem externen Datentyp [Name_des_externen_Datentyps] 

Wenn für Ihre Abfrage eine Fehlermeldung vom Typ „Spalte [Spaltenname] vom Typ [Typname] ist nicht kompatibel mit dem externen Datentyp […]“ angezeigt wird, wurde vermutlich versucht, einen PARQUET-Datentyp dem falschen SQL-Datentyp zuzuordnen. Wenn Ihre Parquet-Datei beispielsweise einen Spaltenpreis mit Gleitkommazahlen (z. B. 12,89) enthält und Sie versucht haben, die Zuordnung zu INT durchzuführen, wird diese Fehlermeldung angezeigt. 

Untersuchen Sie die Datei und die entsprechenden Datentypen, die Sie ausgewählt haben, um dieses Problem zu beheben. Als Hilfe beim Auswählen eines SQL-Datentyps kann Ihnen [diese Zuordnungstabelle](develop-openrowset.md#type-mapping-for-parquet) als Hilfe dienen. Hinweis zur bewährten Methode: Geben Sie die Zuordnung nur für Spalten an, für die andernfalls die Auflösung in den Datentyp „VARCHAR“ erfolgen würde. Sie können die Leistung von Abfragen verbessern, indem Sie die Verwendung von „VARCHAR“ nach Möglichkeit vermeiden. 

#### <a name="example"></a>Beispiel
Wenn Sie die Datei „taxi-data.parquet“ mit dieser Abfrage 1 abfragen möchten, wird von Synapse SQL (serverlos) dieser Fehler zurückgegeben.

taxi-data.parquet:

|PassengerCount |SumTripDistance|AvgTripDistance |
|---------------|---------------|----------------|
| 1 | 2635668.66000064 | 6.72731710678951 |
| 2 | 172174.330000005 | 2.97915543404919 |
| 3 | 296384.390000011 | 2.8991352022851  |
| 4 | 12544348.58999806| 6.30581582240281 |
| 5 | 13091570.2799993 | 111.065989028627 |

Abfrage 1:
```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK '<filepath>taxi-data.parquet',
        FORMAT='PARQUET'
    )  WITh
        (
        PassengerCount INT, 
        SumTripDistance INT, 
        AVGTripDistance FLOAT
        )

    AS [result]
```
Verursacht den folgenden Fehler: 

```Column 'SumTripDistance' of type 'INT' is not compatible with external data type 'Parquet physical type: DOUBLE', please try with 'FLOAT'. File/External table name: '<filepath>taxi-data.parquet'.```

Diese Fehlermeldung ist ein Hinweis darauf, dass Datentypen nicht kompatibel sind, und sie enthält bereits den Vorschlag, FLOAT anstelle von INT zu verwenden. Der Fehler wird daher durch diese Codezeile verursacht: 

```sql
SumTripDistance INT, 
```

Mit der geringfügig geänderten Abfrage 2 können die Daten jetzt verarbeitet werden, und es werden alle drei Spalten angezeigt. 

Abfrage 2: 
```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK '<filepath>taxi-data.parquet',
        FORMAT='PARQUET'
    )  WITh
        (
        PassengerCount INT, 
        SumTripDistance FLOAT, 
        AVGTripDistance FLOAT
        )

    AS [result]
```

## <a name="configuration"></a>Konfiguration

### <a name="query-fails-with-please-create-a-master-key-in-the-database-or-open-the-master-key-in-the-session-before-performing-this-operation"></a>Fehler für Abfrage: Erstellen Sie einen Hauptschlüssel in der Datenbank, oder öffnen Sie den Hauptschlüssel in der Sitzung, bevor Sie diesen Vorgang ausführen.

Das Auftreten der Fehlermeldung „Erstellen Sie einen Hauptschlüssel in der Datenbank, oder öffnen Sie den Hauptschlüssel in der Sitzung, bevor Sie diesen Vorgang ausführen.“ für Ihre Abfrage bedeutet, dass für Ihre Benutzerdatenbank derzeit kein Zugriff auf einen Hauptschlüssel besteht. 

Vermutlich haben Sie gerade eine neue Benutzerdatenbank erstellt, aber noch keinen Hauptschlüssel. 

Erstellen Sie einen Hauptschlüssel mit der folgenden Abfrage, um dies zu beheben:

```sql
CREATE MASTER KEY [ ENCRYPTION BY PASSWORD ='password' ];
```

> [!NOTE]
> Ersetzen Sie „password“ hier durch ein anderes Geheimnis. 

### <a name="create-statement-is-not-supported-in-master-database"></a>„CREATE STATEMENT“ wird in der Masterdatenbank nicht unterstützt

Wenn bei Ihrer Abfrage ein Fehler mit dem folgenden Hinweis auftritt:

> „Fehler beim Ausführen der Abfrage. Error: CREATE EXTERNAL TABLE/DATA SOURCE/DATABASE SCOPED CREDENTIAL/FILE FORMAT wird in der Masterdatenbank nicht unterstützt.“ 

bedeutet dies, dass die Masterdatenbank im serverlosen SQL-Pool die Erstellung von Folgendem nicht unterstützt:
  - Externe Tabellen
  - Externe Datenquellen
  - Datenbankweit gültige Anmeldeinformationen
  - Externe Dateiformate

Lösung:

  1. Erstellen Sie eine Benutzerdatenbank:

```sql
CREATE DATABASE <DATABASE_NAME>
```

  2. Führen Sie die CREATE-Anweisung im Kontext von <DATABASE_NAME> aus, die bei der Masterdatenbank zuvor fehlgeschlagen ist. 
  
  Beispiel für die Erstellung eines externen Dateiformats:
    
```sql
USE <DATABASE_NAME>
CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] 
WITH ( FORMAT_TYPE = PARQUET)
```

### <a name="operation-is-not-allowed-for-a-replicated-database"></a>Der Vorgang ist für eine replizierte Datenbank nicht zulässig.
   
Falls Sie versuchen, in einer Datenbank SQL-Objekte oder Benutzer zu erstellen oder Berechtigungen zu ändern, erhalten Sie ggf. Fehler der Art „Der Vorgang CREATE USER ist für eine replizierte Datenbank nicht zulässig“. Dieser Fehler wird bei dem Versuch zurückgegeben, Objekte in einer Datenbank zu erstellen, die [über einen Spark-Pool freigegeben werden](../metadata/database.md). Datenbanken, die aus Apache Spark-Pools repliziert werden, sind schreibgeschützt. Sie können keine neuen Objekte in einer replizierten Datenbank erstellen, indem Sie T-SQL verwenden.

Erstellen Sie eine separate Datenbank, und verweisen Sie mithilfe von dreiteiligen Namen und datenbankübergreifenden Abfragen auf die synchronisierten [Tabellen](../metadata/table.md).

## <a name="cosmos-db"></a>Cosmos DB

In der folgenden Tabelle werden mögliche Fehler und entsprechende Problembehebungsmaßnahmen aufgelistet.

| Fehler | Grundursache |
| --- | --- |
| Syntaxfehler:<br/> – Falsche Syntax in der Nähe von `Openrowset`.<br/> - `...` wird nicht als `BULK OPENROWSET`-Anbieteroption erkannt.<br/> – Falsche Syntax in der Nähe von `...`. | Mögliche Grundursachen:<br/> – „CosmosDB“ wird nicht als erster Parameter verwendet.<br/> – Verwendung eines Zeichenfolgenliterals anstelle eines Bezeichners im dritten Parameter.<br/> – Fehlende Angabe des dritten Parameters (Containername). |
| Fehler in der CosmosDB-Verbindungszeichenfolge. | – Keine Angabe von Konto, Datenbank oder Schlüssel. <br/> – Eine Verbindungszeichenfolge enthält eine nicht erkannte Option.<br/> – Am Ende einer Verbindungszeichenfolge befindet sich ein Semikolon (`;`). |
| Beim Auflösen des CosmosDB-Pfads ist der Fehler „Falscher Kontoname“ oder „Falscher Datenbankname“ aufgetreten. | Der angegebene Kontoname, Datenbankname oder Container wurde nicht gefunden, oder der Analysespeicher wurde nicht für die angegebene Sammlung aktiviert.|
| Beim Auflösen des CosmosDB-Pfads ist der Fehler „Falscher Geheimniswert“ oder „Geheimnis ist NULL oder leer“ aufgetreten. | Der Kontoschlüssel ist nicht gültig oder fehlt. |
| Die Spalte `column name` des Typs `type name` ist mit dem externen Datentyp `type name` nicht kompatibel. | Der in der `WITH`-Klausel angegebene Spaltentyp stimmt nicht mit dem Typ im Azure Cosmos DB-Container überein. Versuchen Sie, den Spaltentyp zu ändern, so wie im Abschnitt [Zuordnung von SQL-Typen zu Azure Cosmos DB](query-cosmos-db-analytical-store.md#azure-cosmos-db-to-sql-type-mappings) beschrieben, oder verwenden Sie den Typ `VARCHAR`. |
| Die Spalte enthält `NULL`-Werte in allen Zellen. | Möglicherweise falscher Spaltenname oder Pfadausdruck in der `WITH`-Klausel. Der Spaltenname (oder der Pfadausdruck nach dem Spaltentyp) in der `WITH`-Klausel muss mit einem Eigenschaftsnamen in der Azure Cosmos DB-Sammlung übereinstimmen. Beim Vergleich *wird die Groß-/Kleinschreibung beachtet*. Beispielsweise sind `productCode` und `ProductCode` unterschiedliche Eigenschaften. |

Auf der [Azure Synapse Analytics-Feedbackseite](https://feedback.azure.com/forums/307516-azure-synapse-analytics?category_id=387862) können Sie Vorschläge übermitteln und Probleme melden.

### <a name="utf-8-collation-warning-is-returned-while-reading-cosmosdb-string-types"></a>Beim Lesen von CosmosDB-Zeichenfolgentypen wird eine UTF-8-Sortierungswarnung zurückgegeben.

Ein serverloser SQL-Pool gibt eine Kompilierzeitwarnung zurück, wenn die `OPENROWSET`-Spaltensortierung keine UTF-8-Codierung aufweist. Sie können die Standardsortierung für alle `OPENROWSET`-Funktionen, die in der aktuellen Datenbank ausgeführt werden, ganz einfach mit der T-SQL-Anweisung `alter database current collate Latin1_General_100_CI_AS_SC_UTF8` ändern.

Die [Sortierung Latin1_General_100_BIN2_UTF8](best-practices-serverless-sql-pool.md#use-proper-collation-to-utilize-predicate-pushdown-for-character-columns) bietet die beste Leistung, wenn Sie Ihre Daten mithilfe von Zeichenfolgenprädikaten filtern.

### <a name="some-rows-are-not-returned"></a>Einige Zeilen werden nicht zurückgegeben

- Zwischen dem Transaktions- und Analysespeicher kommt es zu einer Synchronisierungsverzögerung. Das Dokument, das Sie in den Cosmos DB-Transaktionsspeicher eingegeben haben, wird unter Umständen erst nach zwei oder drei Minuten im Analysespeicher angezeigt.
- Es kann sein, dass das Dokument gegen einige [Schemaeinschränkungen](../../cosmos-db/analytical-store-introduction.md#schema-constraints) verstößt. 

### <a name="query-returns-null-values"></a>Abfrage gibt `NULL`-Werte zurück

Synapse SQL gibt in den folgenden Fällen `NULL` anstelle der Werte zurück, die im Transaktionsspeicher angezeigt werden:
- Zwischen dem Transaktions- und Analysespeicher kommt es zu einer Synchronisierungsverzögerung. Der Wert, den Sie in den Cosmos DB-Transaktionsspeicher eingegeben haben, wird unter Umständen erst nach zwei bis drei Minuten im Analysespeicher angezeigt.
- Möglicherweise falscher Spaltenname oder Pfadausdruck in der `WITH`-Klausel. Der Spaltenname (oder Pfadausdruck nach dem Spaltentyp) in der `WITH`-Klausel muss mit den Eigenschaftennamen in der Cosmos DB-Sammlung übereinstimmen. Beim Vergleich wird die Groß-/Kleinschreibung beachtet (z. B. sind `productCode` und `ProductCode` unterschiedliche Eigenschaften). Stellen Sie sicher, dass Ihre Spaltennamen genau mit den Cosmos DB-Eigenschaftennamen übereinstimmen.
- Die Eigenschaft wird unter Umständen nicht in den Analysespeicher verschoben, da sie gegen einige [Schemaeinschränkungen](../../cosmos-db/analytical-store-introduction.md#schema-constraints) verstößt, z. B. aufgrund von mehr als 1.000 Eigenschaften oder mehr als 127 Schachtelungsebenen.
- Wenn Sie eine klar definierte [Schemadarstellung](../../cosmos-db/analytical-store-introduction.md#schema-representation) verwenden, weist der Wert im Transaktionsspeicher ggf. einen falschen Typ auf. Bei Verwendung eines klar definierten Schemas werden die Typen für jede Eigenschaft gesperrt, indem Stichproben aus den Dokumenten entnommen werden. Jeder im Transaktionsspeicher hinzugefügte Wert, der nicht mit dem richtigen Typ übereinstimmt, wird als falscher Wert behandelt und nicht zum Analysespeicher migriert. 
- Stellen Sie bei der Nutzung einer [Schemadarstellung](../../cosmos-db/analytical-store-introduction.md#schema-representation) mit vollständiger Genauigkeit sicher, dass Sie nach Eigenschaftennamen wie `$.price.int64` ein Typsuffix hinzufügen. Falls für den referenzierten Pfad kein Wert angezeigt wird, sind die Daten unter Umständen unter einem anderen Pfadtyp gespeichert, z. B. `$.price.float64`. Informieren Sie sich über das [Abfragen von Cosmos DB-Sammlungen bei einem Schema mit vollständiger Genauigkeit](query-cosmos-db-analytical-store.md#query-items-with-full-fidelity-schema).

### <a name="column-is-not-compatible-with-external-data-type"></a>Spalte ist mit dem externen Datentyp nicht kompatibel

Der in der `WITH`-Klausel angegebene Wert stimmt nicht mit den zugrunde liegenden Cosmos DB-Typen im Analysespeicher überein und kann nicht implizit konvertiert werden. Verwenden Sie den Typ `VARCHAR` im Schema.

### <a name="cosmosdb-performance-issues"></a>CosmosDB-Leistungsprobleme

Wenn unerwartete Leistungsprobleme auftreten, vergewissern Sie sich, dass die bewährten Methoden angewandt wurden, z. B.:
- Stellen Sie sicher, dass Sie die Clientanwendung, den serverlosen Pool und den Cosmos DB-Analysespeicher in [derselben Region](best-practices-serverless-sql-pool.md#colocate-your-cosmosdb-analytical-storage-and-serverless-sql-pool) platziert haben.
- Stellen Sie sicher, dass Sie die `WITH`-Klausel mit den [optimalen Datentypen](best-practices-serverless-sql-pool.md#use-appropriate-data-types) verwenden.
- Stellen Sie sicher, dass Sie die Sortierung [Latin1_General_100_BIN2_UTF8](best-practices-serverless-sql-pool.md#use-proper-collation-to-utilize-predicate-pushdown-for-character-columns) verwenden, wenn Sie Ihre Daten mithilfe von Zeichenfolgenprädikaten filtern.
- Wenn Sie wiederholte Abfragen haben, die möglicherweise zwischengespeichert werden, versuchen Sie, [CETAS zum Speichern von Abfrageergebnissen in Azure Data Lake Storage](best-practices-serverless-sql-pool.md#use-cetas-to-enhance-query-performance-and-joins) zu verwenden.

## <a name="delta-lake"></a>Delta Lake

Die Delta Lake-Unterstützung befindet sich für serverlose SQL-Pools derzeit in der öffentlichen Vorschauphase. Es gibt einige bekannte Probleme, die bei Ihnen während der Vorschauphase ggf. auftreten können.
- Stellen Sie sicher, dass Sie auf den Delta Lake-Stammordner in der [OPENROWSET](./develop-openrowset.md)-Funktion oder an einem externen Tabellenspeicherort verweisen.
  - Der Stammordner muss über einen Unterordner mit dem Namen `_delta_log` verfügen. Für die Abfrage tritt ein Fehler auf, wenn der Ordner `_delta_log` nicht vorhanden ist. Falls dieser Ordner nicht angezeigt wird, verweisen Sie auf einfache Parquet-Dateien, die über Apache Spark-Pools [für Delta Lake konvertiert](../spark/apache-spark-delta-lake-overview.md?pivots=programming-language-python#convert-parquet-to-delta) werden müssen.
  - Geben Sie keine Platzhalter an, um das Partitionsschema zu beschreiben. Die Delta Lake-Partitionen werden von der Delta Lake-Abfrage automatisch identifiziert. 
- Delta Lake-Tabellen, die in den Apache Spark-Pools erstellt wurden, sind im serverlosen SQL-Pool nicht automatisch verfügbar. Um solche Delta Lake-Tabellen mit T-SQL Sprache abzufragen, führen Sie die Anweisung [CREATE EXTERNAL TABLE](https://docs.microsoft.com/azure/synapse-analytics/sql/create-use-external-tables#delta-lake-external-table) aus, und geben Sie als Format „Delta“ an.
- Externe Tabellen unterstützen keine Partitionierung. Verwenden Sie [partitionierte Sichten](create-use-views.md#delta-lake-partitioned-views) im Delta Lake-Ordner, um die Partitionsentfernung zu nutzen. Weiter unten finden Sie bekannte Probleme und Problemumgehungen.
- Serverlose SQL-Pools unterstützen keine Zeitreiseabfragen. Sie können auf der [Azure-Feedbackwebsite](https://feedback.azure.com/forums/307516-azure-synapse-analytics/suggestions/43656111-add-time-travel-feature-in-delta-lake) für dieses Feature abstimmen. Verwenden Sie Apache Spark-Pools in Azure Synapse Analytics, um [Verlaufsdaten zu lesen](../spark/apache-spark-delta-lake-overview.md?pivots=programming-language-python#read-older-versions-of-data-using-time-travel).
- Serverlose SQL-Pools unterstützen keine Aktualisierung von Delta Lake-Dateien. Sie können einen serverlosen SQL-Pool verwenden, um die neueste Version von Delta Lake abzufragen. Verwenden Sie Apache Spark-Pools in Azure Synapse Analytics, um [Delta Lake zu aktualisieren](../spark/apache-spark-delta-lake-overview.md?pivots=programming-language-python#update-table-data).
- Serverlose SQL-Pools in Synapse Analytics unterstützen keine Datasets mit dem [BLOOM-Filter](https://docs.microsoft.com/azure/databricks/delta/optimizations/bloom-filters).
- Delta Lake-Unterstützung ist in dedizierten SQL-Pools nicht verfügbar. Stellen Sie sicher, dass Sie serverlose Pools verwenden, um Delta Lake-Dateien abzufragen.

Auf der [Azure Synapse-Website für Feedback](https://feedback.azure.com/forums/307516-azure-synapse-analytics?category_id=171048) können Sie Vorschläge für Ideen und Verbesserungen einreichen.

### <a name="content-of-directory-on-path-cannot-be-listed"></a>Inhalt eines Verzeichnisses im Pfad kann nicht aufgelistet werden

Der folgende Fehler wird zurückgegeben, wenn der serverlose SQL-Pool den Delta Lake-Ordner mit dem Transaktionsprotokoll nicht lesen kann.

```
Msg 13807, Level 16, State 1, Line 6
Content of directory on path 'https://.....core.windows.net/.../_delta_log/*.json' cannot be listed.
```

Vergewissern Sie sich, dass der Ordner `_delta_log` vorhanden ist. (Es kann beispielsweise sein, dass Sie einfache Parquet-Dateien abfragen, die nicht in das Delta Lake-Format konvertiert wurden.)

Stellen Sie bei Vorhandensein des Ordners `_delta_log` sicher, dass Sie für die zugrunde liegenden Delta Lake-Ordner sowohl über die Berechtigung zum Lesen als auch zum Auflisten verfügen.
Versuchen Sie, \*.json-Dateien direkt mit „FORMAT='CSV'“ zu lesen (übergeben Sie Ihren URI im BULK-Parameter):

```sql
select top 10 * 
from openrowset(BULK 'https://.....core.windows.net/.../_delta_log/*.json', 
FORMAT='csv', FIELDQUOTE = '0x0b', FIELDTERMINATOR ='0x0b', ROWTERMINATOR = '0x0b') with (line varchar(max)) as logs
```

Wenn diese Abfrage zu einem Fehler führt, verfügt der Aufrufer nicht über die Berechtigung zum Lesen der zugrunde liegenden Speicherdateien. 

Dies können Sie am einfachsten erreichen, indem Sie sich die Rolle „Mitwirkender an Storage-Blobdaten“ für das Speicherkonto zuweisen, für das Sie Abfragen ausführen möchten. 
- [Weitere Informationen finden Sie im vollständigen Leitfaden zur Azure Active Directory-Zugriffssteuerung für Speicher.](../../storage/blobs/assign-azure-role-data-access.md) 
- Besuchen Sie [Steuern des Speicherkontozugriffs für einen serverlosen SQL-Pool in Azure Synapse Analytics](develop-storage-files-storage-access-control.md).

### <a name="partitioning-column-returns-null-values"></a>Partitionierungsspalte gibt NULL-Werte zurück

**Status**: Behoben.

**Release**: August 2021

### <a name="query-failed-because-of-a-topology-change-or-compute-container-failure"></a>Abfragefehler aufgrund einer Topologieänderung oder eines Computecontainerfehlers

**Status**: Behoben.

**Release**: August 2021

### <a name="column-of-type-varchar-is-not-compatible-with-external-data-type-parquet-column-is-of-nested-type"></a>Die Spalte des Typs „VARCHAR“ ist mit dem externen Datentyp „Parquet-Spalte hat geschachtelten Typ“ nicht kompatibel

Sie möchten Delta Lake-Dateien lesen, die einige Spalten vom Typ „Geschachtelt“ enthalten, ohne dass die WITH-Klausel angegeben ist (per automatischem Schemarückschluss).

```sql
SELECT TOP 10 *
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/delta-lake/data-set-with-complex-type/',
    FORMAT = 'delta') as rows;
```

Der automatische Schemarückschluss funktioniert für geschachtelte Spalten in Delta Lake nicht. Stellen Sie sicher, dass die Abfrage überhaupt Ergebnisse zurückgibt, wenn Sie „FORMAT='parquet'“ angeben und ** an den Pfad anfügen.

**Problemumgehung:** Verwenden Sie die `WITH`-Klausel, und weisen Sie den geschachtelten Spalten explizit den Typ `VARCHAR` zu. Beachten Sie, dass dies nicht funktioniert, wenn Ihr DataSet partitioniert ist. Dies liegt an einem anderen bekannten Problem, bei dem die `WITH` Klausel `NULL` für Partitionsspalten zurückgibt. Partitionierte DataSets mit komplexen Typspalten werden derzeit nicht unterstützt.

### <a name="cannot-parse-field-type-in-json-object"></a>Das Feld „type“ im JSON-Objekt kann nicht analysiert werden.

Sie möchten Delta Lake-Dateien lesen, die einige Spalten vom Typ „Geschachtelt“ enthalten, ohne dass die WITH-Klausel angegeben ist (per automatischem Schemarückschluss). 

```sql
SELECT TOP 10 *
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/delta-lake/data-set-with-complex-type/',
    FORMAT = 'delta') as rows;
```

Der automatische Schemarückschluss funktioniert für geschachtelte Spalten in Delta Lake nicht. Stellen Sie sicher, dass die Abfrage überhaupt Ergebnisse zurückgibt, wenn Sie „FORMAT='parquet'“ angeben und ** an den Pfad anfügen.

**Problemumgehung:** Verwenden Sie die `WITH`-Klausel, und weisen Sie den geschachtelten Spalten explizit den Typ `VARCHAR` zu. Beachten Sie, dass dies nicht funktioniert, wenn Ihr DataSet partitioniert ist. Dies liegt an einem anderen bekannten Problem, bei dem die `WITH` Klausel `NULL` für Partitionsspalten zurückgibt. Partitionierte DataSets mit komplexen Typspalten werden derzeit nicht unterstützt.

### <a name="cannot-find-value-of-partitioning-column-in-file"></a>Der Wert der Partitionierungsspalte kann in der Datei nicht gefunden werden 

Delta Lake-Datasets können in den Partitionierungsspalten ggf. `NULL`-Werte enthalten. Diese Partitionen werden im Ordner `HIVE_DEFAULT_PARTITION` gespeichert. Dies wird im serverlosen SQL-Pool derzeit nicht unterstützt. In diesem Fall erhalten Sie einen Fehler der folgenden Art:

```
Resolving Delta logs on path 'https://....core.windows.net/.../' failed with error:
Cannot find value of partitioning column '<column name>' in file 
'https://......core.windows.net/...../<column name>=__HIVE_DEFAULT_PARTITION__/part-00042-2c0d5c0e-8e89-4ab8-b514-207dcfd6fe13.c000.snappy.parquet'.
```

**Problemumgehung:** Versuchen Sie, Ihr Delta Lake-Dataset mit Apache Spark-Pools zu aktualisieren, und verwenden Sie einen Wert (leere Zeichenfolge oder `"null"`) anstelle von `null` in der Partitionierungsspalte.

### <a name="json-text-is-not-properly-formatted"></a>JSON-Text ist nicht korrekt formatiert.

Dieser Fehler gibt an, dass der serverlose SQL-Pool das Delta Lake-Transaktionsprotokoll nicht lesen kann. Der Fehler wird vermutlich wie der folgende angezeigt:

```
Msg 13609, Level 16, State 4, Line 1
JSON text is not properly formatted. Unexpected character '{' is found at position 263934.
Msg 16513, Level 16, State 0, Line 1
Error reading external metadata.
```
Stellen Sie zunächst sicher, dass Ihr Delta Lake-DataSet nicht beschädigt ist.
- Überprüfen Sie, ob Sie den Inhalt des Delta Lake-Ordners mithilfe des Apache Spark-Pools im Synapse- oder Databricks-Cluster lesen können. Auf diese Weise stellen Sie sicher, dass die `_delta_log` Datei nicht beschädigt ist.
- Stellen Sie sicher, dass Sie den Inhalt von Datendateien lesen können, indem Sie am Ende des URI-Pfads `FORMAT='PARQUET'` angeben und einen rekursiven Platzhalter `/**` verwenden. Wenn Sie alle Parquet-Dateien lesen können, liegt das Problem im `_delta_log` Transaktionsprotokollordner.

Einige häufige Fehler und Problemumgehungen:

- `JSON text is not properly formatted. Unexpected character '.'` – Es ist möglich, dass die zugrunde liegenden Parquet-Dateien Datentypen enthalten, die in serverlosen SQL-Pools nicht unterstützt werden.

**Problemumgehung**: Versuchen Sie, das WITH-Schema zu verwenden, das nicht unterstützte Typen ausschließt.

- `JSON text is not properly formatted. Unexpected character '{'` – Es ist möglich, dass Sie eine `_UTF8`-Datenbanksortierung verwenden. 

**Problemumgehung**: Versuchen Sie, eine Abfrage auf der `master`-Datenbank oder einer anderer Datenbank auszuführen, die nicht über eine UTF8-Sortierung verfügt. Wenn das Problem dadurch behoben wird, verwenden Sie eine Datenbank ohne `_UTF8` Sortierung. Geben Sie in der Spaltendefinition in der `WITH`-Klausel die `_UTF8`-Sortierung an.

**Allgemeine Problemumgehung**: Versuchen Sie, mithilfe eines Apache Spark-Pools einen Prüfpunkt für das Delta Lake-Dataset zu erstellen, und führen Sie die Abfrage erneut aus. Der Prüfpunkt aggregiert transaktionale JSON-Protokolldateien und behebt das Problem möglicherweise.

Wenn das Dataset gültig ist und die Problembehandlungen nicht helfen, melden Sie ein Supportticket, und stellen Sie dem Azure-Support eine Repro zur Verfügung:
- Nehmen Sie keine Änderungen vor (wie z. B. das Hinzufügen/Entfernen der Spalten oder das Optimieren der Tabelle), da dies den Status der Delta Lake-Transaktionsprotokolldateien ändern kann.
- Kopieren Sie den Inhalt des `_delta_log` Ordners in einen neuen leeren Ordner. Dateien **NICHT** kopieren`.parquet data`.
- Versuchen Sie, den Inhalt zu lesen, den Sie in den neuen Ordner kopiert haben, und stellen Sie sicher, dass Sie den gleichen Fehler erhalten.
- Jetzt können Sie den Delta Lake-Ordner mit dem Spark-Pool weiter verwenden. Sie stellen kopierte Daten an den Microsoft-Support zur Verfügung, wenn Sie die Berechtigung zur Freigabe haben.
- Senden Sie den Inhalt der kopierten `_delta_log` Datei an Azure-Support.

Das Azure-Team überprüft den Inhalt der `delta_log` Datei und stellt weitere Informationen zu möglichen Fehlern und Problemlösungen zur Verfügung.

### <a name="resolving-delta-log-on-path--failed-with-error-cannot-parse-json-object-from-log-file"></a>Fehler beim Auflösen der Deltaprotokolle im Pfad ...: Das JSON-Objekt aus der Protokolldatei kann nicht analysiert werden.

Dieser Fehler kann aufgrund der folgenden Ursachen/nicht unterstützten Features auftreten:
- [BLOOM-Filter](https://docs.microsoft.com/azure/databricks/delta/optimizations/bloom-filters) im Delta Lake-Dataset. Serverlose SQL-Pools in Synapse Analytics unterstützen keine Datasets mit dem [BLOOM-Filter](https://docs.microsoft.com/azure/databricks/delta/optimizations/bloom-filters).
- Spalte „float“ im Delta Lake-Dataset mit Statistiken.
- An einer „float“-Spalte partitioniertes Dataset.

**Problemumgehung**: [Entfernen Sie den BLOOM-Filter](https://docs.microsoft.com/azure/databricks/delta/optimizations/bloom-filters#drop-a-bloom-filter-index), wenn Sie den Delta Lake-Ordner mithilfe des serverlosen SQL-Pools lesen möchten. Wenn Sie über `float`-Spalten verfügen, die das Problem verursachen, müssten Sie das Dataset neu partitionieren oder die Statistiken entfernen.

## <a name="security"></a>Sicherheit

### <a name="aad-service-principal-login-failures-when-spi-is-creating-a-role-assignment"></a>AAD-Dienstprinzipal-Anmeldefehler, wenn der SPI eine Rollenzuweisung erstellt
Wenn Sie eine Rollenzuweisung für den Dienstprinzipalbezeichner/die AAD-App mithilfe eines anderen SPI erstellen möchten oder bereits erstellt haben, und diese/r sich nicht anmelden kann, erhalten Sie wahrscheinlich den folgenden Fehler:
```
Login error: Login failed for user '<token-identified principal>'.
```
Für Dienstprinzipale sollte die Anmeldung mit der Anwendungs-ID als SID (nicht mit der Objekt-ID) erstellt werden. Es gibt eine bekannte Einschränkung für Dienstprinzipale, die verhindert, dass der Synapse-Dienst die Anwendungs-ID beim Erstellen einer Rollenzuweisung für einen anderen SPI oder eine andere App aus Azure AD Graph abruft.  

#### <a name="solution-1"></a>Lösung 1
Navigieren Sie zum „Azure-Portal > Synapse Studio > Verwalten > Zugriffssteuerung“, und fügen Sie „Synapse-Administrator“ oder „Synapse SQL-Administrator“ für den gewünschten Dienstprinzipal manuell hinzu.

#### <a name="solution-2"></a>Lösung 2
Sie müssen eine ordnungsgemäße Anmeldung manuell mittels SQL Code erstellen:
```sql
use master
go
CREATE LOGIN [<service_principal_name>] FROM EXTERNAL PROVIDER;
go
ALTER SERVER ROLE sysadmin ADD MEMBER [<service_principal_name>];
go
```

#### <a name="solution-3"></a>Lösung 3
Sie können „Synapse-Administrator“ für den Dienstprinzipal auch mithilfe von PowerShell einrichten. Sie müssen das [Az.Synapse-Module](/powershell/module/az.synapse) installiert haben.
Die Lösung besteht darin, das Cmdlet „New-AzSynapseRoleAssignment“ mit `-ObjectId "parameter"` zu verwenden und in diesem Parameterfeld die Anwendungs-ID (anstelle der Objekt-ID) unter Verwendung der Anmeldeinformationen des Azure-Dienstprinzipals für den Arbeitsbereichsadministrator bereitzustellen. PowerShell-Skript:
```azurepowershell
$spAppId = "<app_id_which_is_already_an_admin_on_the_workspace>"
$SPPassword = "<application_secret>"
$tenantId = "<tenant_id>"
$secpasswd = ConvertTo-SecureString -String $SPPassword -AsPlainText -Force
$cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $spAppId, $secpasswd

Connect-AzAccount -ServicePrincipal -Credential $cred -Tenant $tenantId

New-AzSynapseRoleAssignment -WorkspaceName "<workspaceName>" -RoleDefinitionName "Synapse Administrator" -ObjectId "<app_id_to_add_as_admin>" [-Debug]
```

#### <a name="validation"></a>Überprüfen
Stellen Sie eine Verbindung mit dem serverlosen SQL Endpunkt her, und überprüfen Sie, ob die externe Anmeldung mit der SID `app_id_to_add_as_admin` erstellt wurde:
```sql
select name, convert(uniqueidentifier, sid) as sid, create_date
from sys.server_principals where type in ('E', 'X')
```
Oder versuchen Sie nur, sich mithilfe der gerade festgelegten Administrator-App beim serverlosen SQL-Endpunkt anzumelden.

## <a name="constraints"></a>Einschränkungen

Es gibt einige allgemeine Systemeinschränkungen, die sich auf Ihre Workload auswirken können:

| Eigenschaft | Einschränkung |
|---|---|
| Maximale Anzahl von Synapse-Arbeitsbereichen pro Abonnement | 20 |
| Maximale Anzahl von Datenbanken pro serverlosem Pool | 20 (ohne Datenbanken, die aus einem Apache Spark-Pool synchronisiert werden) |
| Maximale Anzahl von Datenbanken, die aus einem Apache Spark-Pool synchronisiert werden | Nicht begrenzt |
| Maximale Anzahl von Datenbankobjekten pro Datenbank | Die Summe aller Objekte in einer Datenbank darf 2.147.483.647 nicht überschreiten (siehe [Einschränkungen in SQL Server-Datenbank-Engine](/sql/sql-server/maximum-capacity-specifications-for-sql-server#objects)). |
| Maximale Bezeichnerlänge (in Zeichen) | 128 (siehe [Einschränkungen in SQL Server-Datenbank-Engine](/sql/sql-server/maximum-capacity-specifications-for-sql-server#objects))|
| Maximale Abfragedauer | 30 Min. |
| Maximale Größe des Resultsets | 80 GB (wird von allen derzeit ausgeführten gleichzeitigen Abfragen gemeinsam genutzt) |
| Maximale Parallelität | Nicht begrenzt und abhängig von der Komplexität der Abfrage und der Menge der gescannten Daten. Ein serverloser SQL-Pool kann gleichzeitig 1.000 aktive Sitzungen verarbeiten, die einfache Abfragen ausführen. Die Anzahl sinkt jedoch, wenn die Abfragen komplexer sind oder eine größere Datenmenge gescannt wird. |

## <a name="next-steps"></a>Nächste Schritte

Die folgenden Artikel enthalten weitere Informationen zur Verwendung eines serverlosen SQL-Pools:

- [Abfragen einer einzelnen CSV-Datei](query-single-csv-file.md)

- [Abfragen von Ordnern und mehreren CSV-Dateien](query-folders-multiple-csv-files.md)

- [Abfragen bestimmter Dateien](query-specific-files.md)

- [Abfragen von Parquet-Dateien](query-parquet-files.md)

- [Abfragen von geschachtelten Parquet-Typen](query-parquet-nested-types.md)

- [Abfragen von JSON-Dateien](query-json-files.md)

- [Erstellen und Verwenden von Sichten](create-use-views.md)

- [Erstellen und Verwenden externer Tabellen](create-use-external-tables.md)

- [Speichern von Abfrageergebnissen im Speicher](create-external-table-as-select.md)
