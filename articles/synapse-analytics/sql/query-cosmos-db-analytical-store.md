---
title: Abfragen von Azure Cosmos DB-Daten mithilfe eines serverlosen SQL-Pools in Azure Synapse Link
description: In diesem Artikel erfahren Sie, wie Sie Azure Cosmos DB unter Verwendung eines serverlosen SQL-Pools in Azure Synapse Link abfragen.
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 03/02/2021
ms.author: jovanpop
ms.reviewer: jrasnick
ms.custom: cosmos-db
ms.openlocfilehash: a0f3e5f707600933ce68e51634145cd3515c5d6a
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2021
ms.locfileid: "122343026"
---
# <a name="query-azure-cosmos-db-data-with-a-serverless-sql-pool-in-azure-synapse-link"></a>Abfragen von Azure Cosmos DB-Daten mit serverlosem SQL-Pool in Azure Synapse Link

Mit einem serverlosen SQL-Pool können Sie Daten in Ihren Azure Cosmos DB-Containern, die für [Azure Synapse Link](../../cosmos-db/synapse-link.md) aktiviert sind, nahezu in Echtzeit analysieren, ohne dass sich dies auf die Leistung Ihrer Transaktionsarbeitsauslastungen auswirkt. Dieses Verfahren bietet eine vertraute T-SQL-Syntax zum Abfragen von Daten aus dem [Analysespeicher](../../cosmos-db/analytical-store-introduction.md) sowie integrierte Konnektivität mit einer Vielzahl von BI-Tools (Business Intelligence) und Ad-hoc-Abfragetools über die T-SQL-Schnittstelle.

Zum Abfragen von Azure Cosmos DB wird die gesamte [SELECT](/sql/t-sql/queries/select-transact-sql?view=azure-sqldw-latest&preserve-view=true)-Oberfläche durch die [OPENROWSET](develop-openrowset.md)-Funktion unterstützt, einschließlich eines Großteils der [SQL-Funktionen und -Operatoren](overview-features.md). Mit [CREATE EXTERNAL TABLE AS SELECT](develop-tables-cetas.md#cetas-in-serverless-sql-pool) (CETAS) können Sie die Ergebnisse der Abfrage, die Daten aus Azure Cosmos DB liest, auch zusammen mit Daten in Azure Blob Storage oder Azure Data Lake Storage speichern. Das Speichern der Abfrageergebnisse aus einem serverlosen SQL-Pool in Azure Cosmos DB mithilfe von CETAS ist derzeit nicht möglich.

In diesem Artikel erfahren Sie, wie Sie eine Abfrage für einen serverlosen SQL-Pool erstellen, die Daten aus für Azure Synapse Link aktivierten Azure Cosmos DB-Containern abfragt. Danach können Sie sich in [diesem Tutorial](./tutorial-data-analyst.md) darüber informieren, wie Sie Sichten serverloser SQL-Pools über Azure Cosmos DB-Container erstellen und diese Sichten mit Power BI-Modellen verbinden. In diesem Tutorial wird ein Container mit einem [genau definierten Azure Cosmos DB-Schema](../../cosmos-db/analytical-store-introduction.md#schema-representation) verwendet. Sie können sich auch das Learn-Modul zum [Abfragen von Azure Cosmos DB mit serverlosem SQL für Azure Synapse Analytics](/learn/modules/query-azure-cosmos-db-with-sql-serverless-for-azure-synapse-analytics/) ansehen.

## <a name="prerequisites"></a>Voraussetzungen

- Stellen Sie sicher, dass Sie den Analysespeicher vorbereitet haben:
  - Aktivieren Sie den Analysespeicher für [Ihre Cosmos DB-Container](../quickstart-connect-synapse-link-cosmos-db.md#enable-azure-cosmos-db-analytical-store).
  - Rufen Sie die Verbindungszeichenfolge mit einem schreibgeschützten Schlüssel ab, den Sie zum Abfragen des Analysespeichers verwenden werden. 
  - Rufen Sie den schreibgeschützten [Schlüssel ab, der für den Zugriff auf den Cosmos DB-Container verwendet wird](../../cosmos-db/database-security.md#primary-keys).
- Stellen Sie sicher, dass Sie alle [bewährten Methoden](best-practices-serverless-sql-pool.md) angewendet haben, z. B.:
  - Stellen Sie sicher, dass sich Ihr Cosmos DB-Analysespeicher in derselben Region wie der serverlose SQL-Pool befindet.
  - Stellen Sie sicher, dass sich die Clientanwendung (Power BI, Analysedienst) in derselben Region wie der serverlose SQL-Pool befindet.
  - Wenn Sie eine große Datenmenge (mehr als 80 GB) zurückgeben, sollten Sie die Verwendung einer Zwischenspeicherungsebene wie Analysis Services erwägen und die Partitionen, die kleiner als 80 GB sind, in das Analysis Services-Modell laden.
  - Wenn Sie Daten mithilfe von Zeichenfolgenspalten filtern, stellen Sie sicher, dass Sie die `OPENROWSET`-Funktion mit der expliziten `WITH`-Klausel verwenden, die über die kleinsten möglichen Typen verfügt (verwenden Sie z. B. nicht „VARCHAR(1000)“, wenn Sie wissen, dass die Eigenschaft höchstens 5 Zeichen enthält).

## <a name="overview"></a>Übersicht

Ein serverloser SQL-Pool ermöglicht es Ihnen, den Azure Cosmos DB-Analysespeicher mithilfe der `OPENROWSET`-Funktion abzufragen. 
- `OPENROWSET` mit Inlineschlüssel. Diese Syntax kann verwendet werden, um Azure Cosmos DB-Sammlungen abzufragen, ohne Anmeldeinformationen vorbereiten zu müssen.
- `OPENROWSET`, das auf Anmeldeinformationen verweist, die den Cosmos DB-Kontoschlüssel enthalten. Diese Syntax kann verwendet werden, um Sichten für Azure Cosmos DB-Sammlungen zu erstellen.

### <a name="openrowset-with-key"></a>[OPENROWSET mit Schlüssel](#tab/openrowset-key)

Um die Abfrage und Analyse von Daten in einem Azure Cosmos DB-Analysespeicher zu unterstützen, wird ein serverloser SQL-Pool verwendet. Der serverlose SQL-Pool verwendet die `OPENROWSET` SQL-Syntax, daher müssen Sie zuerst ihre Azure Cosmos DB-Verbindungszeichenfolge in dieses Format konvertieren:

```sql
OPENROWSET( 
       'CosmosDB',
       '<SQL connection string for Azure Cosmos DB>',
       <Container name>
    )  [ < with clause > ] AS alias
```

Die SQL-Verbindungszeichenfolge für Azure Cosmos DB übergibt den Namen des Azure Cosmos DB-Kontos, den Datenbanknamen, den Hauptschlüssel des Datenbankkontos sowie optional einen Regionsnamen an die `OPENROWSET`-Funktion. Einige dieser Informationen können aus der Standardverbindungszeichenfolge von Azure Cosmos DB werden.

Konvertieren aus dem Standardformat der Azure Cosmos DB-Verbindungszeichenfolge:

```
AccountEndpoint=https://<database account name>.documents.azure.com:443/;AccountKey=<database account master key>;
```

Die SQL-Verbindungszeichenfolge weist das folgende Format auf:

```sql
'account=<database account name>;database=<database name>;region=<region name>;key=<database account master key>'
```

Die Region ist optional. Wenn sie nicht angegeben wird, wird die primäre Region des Containers verwendet.

Der Name des Azure Cosmos DB-Containers wird in der `OPENROWSET`-Syntax ohne Anführungszeichen angegeben. Wenn der Containername Sonderzeichen (z. B. einen Bindestrich, „-“) enthält, sollte er in der `OPENROWSET`-Syntax mit `[]` (eckige Klammern) umschlossen werden.

### <a name="openrowset-with-credential"></a>[OPENROWSET mit Anmeldeinformationen](#tab/openrowset-credential)

Sie können die `OPENROWSET`-Syntax verwenden, die auf Anmeldeinformationen verweist:

```sql
OPENROWSET( 
       PROVIDER = 'CosmosDB',
       CONNECTION = '<SQL connection string for Azure Cosmos DB without account key>',
       OBJECT = '<Container name>',
       [ CREDENTIAL | SERVER_CREDENTIAL ] = '<credential name>'
    )  [ < with clause > ] AS alias
```

Die SQL-Verbindungszeichenfolge für Azure Cosmos DB enthält in diesem Fall keinen Schlüssel. Die Verbindungszeichenfolge weist das folgende Format auf:

```sql
'account=<database account name>;database=<database name>;region=<region name>'
```

Der Hauptschlüssel des Datenbankkontos wird in Anmeldeinformationen auf Serverebene oder in datenbankweit gültigen Anmeldeinformationen abgelegt. 

---

> [!IMPORTANT]
> Achten Sie darauf, dass Sie eine UTF-8-Datenbanksortierung (z. B. `Latin1_General_100_CI_AS_SC_UTF8`) verwenden, weil Zeichenfolgenwerte im Azure Cosmos DB-Analysespeicher als UTF-8-Text codiert werden.
> Ein Konflikt zwischen der Textcodierung in der Datei und der Sortierung kann zu unerwarteten Fehlern bei der Textkonvertierung führen.
> Die Standardsortierung der aktuellen Datenbank kann mit der T-SQL-Anweisung `alter database current collate Latin1_General_100_CI_AI_SC_UTF8` problemlos geändert werden.

> [!NOTE]
> Abfragen eines Azure Cosmos DB-Transaktionsspeichers werden im serverlosen SQL-Pool nicht unterstützt.

## <a name="sample-dataset"></a>Beispieldataset

Die Beispiele in diesem Artikel basieren auf Daten aus den Datasets [European Centre for Disease Prevention and Control (ECDC) COVID-19 Cases](../../open-datasets/dataset-ecdc-covid-cases.md) und [COVID-19 Open Research Dataset (CORD-19), doi:10.5281/zenodo.3715505](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/).

Auf diesen Seiten finden Sie die Lizenz und die Datenstruktur. Sie können auch Beispieldaten für die Datasets [ECDC](https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json) und [CORD-19](https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json) herunterladen.

Um die in diesem Artikel beschriebenen Schritte zum Abfragen von Azure Cosmos DB-Daten mit einem serverlosen SQL-Pool ausführen zu können, müssen Sie die folgenden Ressourcen erstellen:

* Ein Azure Cosmos DB-Datenbankkonto mit [aktiviertem Azure Synapse Link](../../cosmos-db/configure-synapse-link.md)
* Eine Azure Cosmos DB-Datenbank namens `covid`
* Zwei Azure Cosmos DB-Container namens `Ecdc` und `Cord19`, in die die obigen Beispieldatasets geladen wurden

Sie können die folgende Verbindungszeichenfolge zu Testzwecken verwenden: `Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==`. Beachten Sie, dass diese Verbindung keine Leistung gewährleistet, da sich dieses Konto möglicherweise in einer entfernten Region im Vergleich zu Ihrem Synapse SQL-Endpunkt befindet.

## <a name="explore-azure-cosmos-db-data-with-automatic-schema-inference"></a>Untersuchen von Azure Cosmos DB-Daten mit automatischem Schemarückschluss

Die einfachste Möglichkeit zum Untersuchen von Daten in Azure Cosmos DB ist die Funktion des automatischen Schemarückschlusses. Wenn Sie die `OPENROWSET`-Anweisung ohne die `WITH`-Klausel verwenden, können Sie den serverlosen SQL-Pool anweisen, das Schema des Analysespeichers des Azure Cosmos DB-Containers automatisch zu erkennen (Rückschlüsse zu ziehen).

### <a name="openrowset-with-key"></a>[OPENROWSET mit Schlüssel](#tab/openrowset-key)

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Ecdc) as documents
```

### <a name="openrowset-with-credential"></a>[OPENROWSET mit Anmeldeinformationen](#tab/openrowset-credential)

```sql
/*  Setup - create server-level or database scoped credential with Azure Cosmos DB account key:
    CREATE CREDENTIAL MyCosmosDbAccountCredential
    WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
*/
SELECT TOP 10 *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      SERVER_CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

---

Im obigen Beispiel haben wir den serverlosen SQL-Pool angewiesen, eine Verbindung mit der `covid`-Datenbank im Azure Cosmos DB-Konto `MyCosmosDbAccount` herzustellen. Die Authentifizierung erfolgte über den Azure Cosmos DB-Schlüssel (Platzhalterwert im obigen Beispiel). Danach haben wir auf den Analysespeicher des Containers `Ecdc` in der Region `West US 2` zugegriffen. Da keine Projektion bestimmter Eigenschaften vorgenommen wurde, gibt die `OPENROWSET`-Funktion alle Eigenschaften aus den Azure Cosmos DB-Elementen zurück.

Unter der Annahme, dass die Elemente im Azure Cosmos DB-Container die Eigenschaften `date_rep`, `cases` und `geo_id` besitzen, werden die Ergebnisse dieser Abfrage in der folgenden Tabelle aufgeführt:

| date_rep | cases | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

Wenn Sie Daten aus dem anderen Container in derselben Azure Cosmos DB-Datenbank untersuchen müssen, können Sie dieselbe Verbindungszeichenfolge verwenden und mit dem dritten Parameter auf den erforderlichen Container verweisen:

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Cord19) as cord19
```

## <a name="explicitly-specify-schema"></a>Explizites Angeben des Schemas

Der automatische Schemarückschluss in `OPENROWSET` ermöglicht zwar einfache und leicht zu verwendende Abfragen, aber für Ihre Geschäftsszenarien müssen Sie möglicherweise explizit das Schema für schreibgeschützte relevante Eigenschaften aus den Azure Cosmos DB-Daten angeben.

In der `OPENROWSET`-Funktion können Sie die Eigenschaften, die aus den Daten im Container gelesen werden sollen, sowie deren Datentypen explizit angeben.

Angenommen, Sie haben einige Daten aus dem [ECDC-COVID-Dataset](../../open-datasets/dataset-ecdc-covid-cases.md) mit der folgenden Struktur in Azure Cosmos DB importiert:

```json
{"date_rep":"2020-08-13","cases":254,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-12","cases":235,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-11","cases":163,"countries_and_territories":"Serbia","geo_id":"RS"}
```

Diese flachen JSON-Dokumente in Azure Cosmos DB können in Synapse SQL als Gruppe von Zeilen und Spalten dargestellt werden. In der `WITH`-Klausel der `OPENROWSET`-Funktion können Sie eine Teilmenge von Eigenschaften, die gelesen werden sollen, sowie die genauen Spaltentypen angeben:

### <a name="openrowset-with-key"></a>[OPENROWSET mit Schlüssel](#tab/openrowset-key)

```sql
SELECT TOP 10 *
FROM OPENROWSET(
      'CosmosDB',
      'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Ecdc
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

### <a name="openrowset-with-credential"></a>[OPENROWSET mit Anmeldeinformationen](#tab/openrowset-credential)

```sql
/*  Setup - create server-level or database scoped credential with Azure Cosmos DB account key:
    CREATE CREDENTIAL MyCosmosDbAccountCredential
    WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
*/
SELECT TOP 10 *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      SERVER_CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
   
```

---
Das Ergebnis dieser Abfrage könnte wie in der folgenden Tabelle aussehen:

| date_rep | cases | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

Weitere Informationen zu den SQL-Typen, die für Azure Cosmos DB-Werte verwendet werden sollten, finden Sie in den [Regeln für die SQL-Typzuordnung](#azure-cosmos-db-to-sql-type-mappings) am Ende dieses Artikels.

## <a name="create-view"></a>Erstellen einer Sicht

Das Erstellen von Sichten in Master- oder Standarddatenbanken wird weder empfohlen noch unterstützt. Deshalb müssen Sie eine Benutzerdatenbank für Ihre Sichten erstellen.

Nachdem Sie das Schema identifiziert haben, können Sie auf Basis Ihrer Azure Cosmos DB-Daten eine Sicht vorbereiten. Sie sollten Ihren Azure Cosmos DB-Kontoschlüssel in gesonderten Anmeldeinformationen ablegen aus der `OPENROWSET`-Funktion auf diese Anmeldeinformationen verweisen. Lassen Sie Ihren Kontoschlüssel nicht in der Sichtdefinition.

```sql
CREATE CREDENTIAL MyCosmosDbAccountCredential
WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
GO
CREATE OR ALTER VIEW Ecdc
AS SELECT *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      SERVER_CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

Verwenden Sie `OPENROWSET` nicht ohne explizit definiertes Schema, da sich dies auf Ihre Leistung auswirken könnte. Stellen Sie sicher, dass Sie die kleinsten möglichen Größen für ihre Spalten (z. B. VARCHAR(100) anstelle des Standards VARCHAR(8000)) verwenden. Sie sollten eine UTF-8-Sortierung als standardmäßige Datenbanksortierung verwenden oder sie als explizite Spaltensortierung festlegen, um [UTF-8-Konvertierungsprobleme](../troubleshoot/reading-utf8-text.md) zu vermeiden. Die Sortierung `Latin1_General_100_BIN2_UTF8` bietet die beste Leistung, wenn Sie Daten mithilfe von Zeichenfolgenspalten filtern.

## <a name="query-nested-objects-and-arrays"></a>Abfragen von geschachtelten Objekten und Arrays

Mit Azure Cosmos DB können Sie komplexere Datenmodelle in Form von geschachtelten Objekten oder Arrays darstellen. Die automatische Synchronisierungsfunktion von Azure Synapse Link für Azure Cosmos DB verwaltet standardmäßig die Schemadarstellung im Analysespeicher. Dies umfasst auch die Verarbeitung von geschachtelten Datentypen, was umfangreiche Abfragen aus dem serverlosen SQL-Pool ermöglicht.

Beispielsweise enthält das [CORD-19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/)-Dataset JSON-Dokumente mit folgender Struktur:

```json
{
    "paper_id": <str>,                   # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": <array of objects>    # list of author dicts, in order
        ...
     }
     ...
}
```

Die geschachtelten Objekte und Arrays in Azure Cosmos DB werden im Abfrageergebnis als JSON-Zeichenfolgen dargestellt, wenn sie von der `OPENROWSET`-Funktion gelesen werden. Sie können diese Pfade zu geschachtelten Werten in den Objekten angeben, wenn Sie die `WITH`-Klausel verwenden:

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Cord19)
WITH (  paper_id    varchar(8000),
        title        varchar(1000) '$.metadata.title',
        metadata     varchar(max),
        authors      varchar(max) '$.metadata.authors'
) AS docs;
```

Das Ergebnis dieser Abfrage könnte wie in der folgenden Tabelle aussehen:

| paper_id | title | metadata | authors |
| --- | --- | --- | --- |
| bb11206963e831f… | Supplementary Information An eco-epidemi… | `{"title":"Supplementary Informati…` | `[{"first":"Julien","last":"Mélade","suffix":"","af…`| 
| bb1206963e831f1… | The Use of Convalescent Sera in Immune-E… | `{"title":"The Use of Convalescent…` | `[{"first":"Antonio","last":"Lavazza","suffix":"", …` |
| bb378eca9aac649… | Tylosema esculentum (Marama) Tuber and B… | `{"title":"Tylosema esculentum (Ma…` | `[{"first":"Walter","last":"Chingwaru","suffix":"",…` | 

Erfahren Sie mehr über das Analysieren von [komplexen Datentypen in Azure Synapse Link](../how-to-analyze-complex-schema.md) und von [geschachtelten Strukturen in serverlosen SQL-Pools](query-parquet-nested-types.md).

> [!IMPORTANT]
> Wenn in Ihrem Text unerwartete Zeichen angezeigt werden, z. B. `MÃƒÂ©lade` statt `Mélade`, ist die Datenbanksortierung nicht auf [UTF-8](/sql/relational-databases/collations/collation-and-unicode-support#utf8)-Sortierung festgelegt.
> [Ändern Sie die Sortierung der Datenbank](/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) mithilfe einer SQL-Anweisung wie `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` in eine UTF-8-Sortierung.

## <a name="flatten-nested-arrays"></a>Vereinfachen geschachtelter Arrays

Azure Cosmos DB-Daten enthalten möglicherweise geschachtelte Unterarrays wie beispielsweise das Array „authors“ im [CORD-19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/)-Dataset:

```json
{
    "paper_id": <str>,                      # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": [                        # list of author dicts, in order
            {
                "first": <str>,
                "middle": <list of str>,
                "last": <str>,
                "suffix": <str>,
                "affiliation": <dict>,
                "email": <str>
            },
            ...
        ],
        ...
}
```

In einigen Fällen müssen Sie möglicherweise die Eigenschaften aus dem obersten Element (metadata) mit allen Elementen des Arrays (authors) verknüpfen. Ein serverloser SQL-Pool ermöglicht Ihnen das Vereinfachen von geschachtelten Strukturen durch Anwenden der `OPENJSON`-Funktion auf das geschachtelte Array:

```sql
SELECT
    *
FROM
    OPENROWSET(
      'CosmosDB',
      'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Cord19
    ) WITH ( title varchar(1000) '$.metadata.title',
             authors varchar(max) '$.metadata.authors' ) AS docs
      CROSS APPLY OPENJSON ( authors )
                  WITH (
                       first varchar(50),
                       last varchar(50),
                       affiliation nvarchar(max) as json
                  ) AS a
```

Das Ergebnis dieser Abfrage könnte wie in der folgenden Tabelle aussehen:

| title | authors | first | last | affiliation |
| --- | --- | --- | --- | --- |
| Supplementary Information An eco-epidemi… |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien | Mélade | `   {"laboratory":"Centre de Recher…` |
Supplementary Information An eco-epidemi… | `[{"first":"Nicolas","last":"4#","suffix":"","affiliation":{"laboratory":"","institution":"U…` | Nicolas | 4# |`{"laboratory":"","institution":"U…` | 
| Supplementary Information An eco-epidemi… |   `[{"first":"Beza","last":"Ramazindrazana","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Beza | Ramazindrazana | `{"laboratory":"Centre de Recher…` |
| Supplementary Information An eco-epidemi… |   `[{"first":"Olivier","last":"Flores","suffix":"","affiliation":{"laboratory":"UMR C53 CIRAD, …` | Olivier | Flores |`{"laboratory":"UMR C53 CIRAD, …` |     

> [!IMPORTANT]
> Wenn in Ihrem Text unerwartete Zeichen angezeigt werden, z. B. `MÃƒÂ©lade` statt `Mélade`, ist die Datenbanksortierung nicht auf [UTF-8](/sql/relational-databases/collations/collation-and-unicode-support#utf8)-Sortierung festgelegt. [Ändern Sie die Sortierung der Datenbank](/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) mithilfe einer SQL-Anweisung wie `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` in eine UTF-8-Sortierung.

## <a name="azure-cosmos-db-to-sql-type-mappings"></a>Zuordnung von SQL-Typen zu Azure Cosmos DB

Der Azure Cosmos DB-Transaktionsspeicher ist zwar schemaagnostisch, aber der Analysespeicher ist zur Optimierung der Leistung von Analyseabfragen schematisiert. Mit der automatischen Synchronisierungsfunktion von Azure Synapse Link verwaltet Azure Cosmos DB standardmäßig die Schemadarstellung im Analysespeicher. Dies umfasst auch die Verarbeitung von geschachtelten Datentypen. Da ein serverloser SQL-Pool den Analysespeicher abfragt, müssen Sie wissen, wie Azure Cosmos DB-Eingabedatentypen SQL-Datentypen zugeordnet werden.

Azure Cosmos DB-Konten für die SQL-API (Core-API) unterstützen als JSON-Eigenschaftstypen Zahlen, Zeichenfolgen, boolesche Werte, NULL, geschachtelte Objekte und Arrays. Wenn Sie die `WITH`-Klausel in `OPENROWSET` verwenden, müssen Sie SQL-Typen auswählen, die diesen JSON-Typen entsprechen. Die folgende Tabelle zeigt die SQL-Spaltentypen, die für verschiedene Eigenschaftstypen in Azure Cosmos DB verwendet werden sollten.

| Azure Cosmos DB-Eigenschaftstyp | SQL-Spaltentyp |
| --- | --- |
| Boolean | bit |
| Integer | BIGINT |
| Decimal | float |
| String | varchar (UTF-8-Datenbanksortierung) |
| DateTime (ISO-formatierte Zeichenfolge) | varchar(30) |
| DateTime (UNIX-Zeitstempel) | BIGINT |
| Null | `any SQL type` 
| Geschachteltes Objekt oder Array | varchar(max) (UTF-8-Datenbanksortierung), als JSON-Text serialisiert |

## <a name="full-fidelity-schema"></a>Schema mit vollständiger Genauigkeit

Das Azure Cosmos DB-Schema mit vollständiger Genauigkeit zeichnet sowohl Werte als auch deren beste Übereinstimmungstypen für jede Eigenschaft in einem Container auf. Die `OPENROWSET`-Funktion in einem Container mit einem Schema mit vollständiger Genauigkeit gibt sowohl den Typ als auch den tatsächlichen Wert in jeder Zelle an. Nehmen wir an, dass die folgende Abfrage die Elemente aus einem Container mit vollständigem Genauigkeitsschema liest:

```sql
SELECT *
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Ecdc
    ) as rows
```

Das Ergebnis dieser Abfrage gibt Typen und Werte zurück, die als JSON-Text formatiert sind:

| date_rep | cases | geo_id |
| --- | --- | --- |
| {"date":"2020-08-13"} | {"int32":"254"} | {"string":"RS"} |
| {"date":"2020-08-12"} | {"int32":"235"}| {"string":"RS"} |
| {"date":"2020-08-11"} | {"int32":"316"} | {"string":"RS"} |
| {"date":"2020-08-10"} | {"int32":"281"} | {"string":"RS"} |
| {"date":"2020-08-09"} | {"int32":"295"} | {"string":"RS"} |
| {"string":"2020/08/08"} | {"int32":"312"} | {"string":"RS"} |
| {"date":"2020-08-07"} | {"float64":"339.0"} | {"string":"RS"} |

Für jeden Wert können Sie den in einem Azure Cosmos DB-Containerelement identifizierten Typ sehen. Die meisten Werte für die `date_rep`-Eigenschaft enthalten `date`-Werte, einige werden jedoch in Azure Cosmos DB fälschlicherweise als Zeichenfolgen gespeichert. Das Schema mit vollständiger Genauigkeit gibt sowohl ordnungsgemäß typisierte `date`-Werte als auch falsch formatierte `string`-Werte zurück.
Die Anzahl der Fälle ist eine Information, die als `int32`-Wert gespeichert wird, allerdings gibt es einen Wert, der als Dezimalzahl eingegeben wird. Dieser Wert weist den Typ `float64` auf. Werte, die die größte `int32`-Zahl überschreiten, werden als `int64`-Typ gespeichert. Alle `geo_id`-Werte in diesem Beispiel werden als `string`-Typen gespeichert.

> [!IMPORTANT]
> Die `OPENROWSET`-Funktion ohne `WITH`-Klausel macht sowohl die Werte mit erwarteten Typen als auch die Werte mit falsch eingegebenen Typen verfügbar. Diese Funktion ist für das Durchsuchen von Daten konzipiert, nicht für die Berichterstellung. Analysieren Sie keine JSON-Werte, die von dieser Funktion zurückgegeben werden, um daraus Berichte zu erstellen. Verwenden Sie zum Erstellen Ihrer Berichte eine explizite [WITH-Klausel](#query-items-with-full-fidelity-schema). Sie müssen die Werte, die falsche Typen aufweisen, im Azure Cosmos DB-Container bereinigen, um Korrekturen im Analysespeicher mit vollständiger Genauigkeit anzuwenden.

Wenn Sie Azure Cosmos DB-Konten über die Mongo DB-API abfragen möchten, erfahren Sie unter [Was ist der Azure Cosmos DB-Analysespeicher?](../../cosmos-db/analytical-store-introduction.md#analytical-schema) mehr über die Schemadarstellung mit vollständiger Genauigkeit im Analysespeicher und die dabei zu verwendenden Namen für erweiterte Eigenschaften.

### <a name="query-items-with-full-fidelity-schema"></a>Abfragen von Elementen mit einem Schema mit vollständiger Genauigkeit

Beim Abfragen eines Schemas mit vollständiger Genauigkeit müssen Sie den SQL-Typ und den erwarteten Azure Cosmos DB-Eigenschaftstyp explizit in der `WITH`-Klausel angeben.

Im folgenden Beispiel gehen wir davon aus, dass `string` der richtige Typ für die `geo_id`-Eigenschaft und `int32` der richtige Typ für die `cases`-Eigenschaft ist:

```sql
SELECT geo_id, cases = SUM(cases)
FROM OPENROWSET(
      'CosmosDB'
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Ecdc
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string',
             cases INT '$.cases.int32'
    ) as rows
GROUP BY geo_id
```

Werte für `geo_id` und `cases`, die andere Typen aufweisen, werden als `NULL`-Werte zurückgegeben. Diese Abfrage verweist nur auf die `cases` mit dem im Ausdruck angegebenen Typ (`cases.int32`).

Wenn Sie Werte mit anderen Typen haben(`cases.int64`, `cases.float64`), die in einem Azure Cosmos DB-Container nicht bereinigt werden können, müssten Sie in einer `WITH`-Klausel explizit auf diese verweisen und die Ergebnisse kombinieren. Die folgende Abfrage aggregiert sowohl `int32`, `int64` als auch `float64`, die in der Spalte `cases` gespeichert sind:

```sql
SELECT geo_id, cases = SUM(cases_int) + SUM(cases_bigint) + SUM(cases_float)
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Ecdc
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string', 
             cases_int INT '$.cases.int32',
             cases_bigint BIGINT '$.cases.int64',
             cases_float FLOAT '$.cases.float64'
    ) as rows
GROUP BY geo_id
```

In diesem Fall wird die Anzahl der Fälle als `int32`-, `int64`- oder `float64`-Wert gespeichert. Alle Werte müssen extrahiert werden, damit die Anzahl von Fällen pro Land berechnet werden kann.

## <a name="troubleshooting"></a>Problembehandlung

Lesen Sie die Seite der [Selbsthilfe](resources-self-help-sql-on-demand.md#cosmos-db), um die bekannten Probleme oder Schritte zur Problembehandlung zu finden, die Ihnen helfen können, potenzielle Probleme mit Cosmos DB-Abfragen zu beheben.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Verwenden von Power BI und serverlosem Synapse SQL-Pool zum Analysieren von Azure Cosmos DB-Daten mit Synapse Link (Vorschau)](../../cosmos-db/synapse-link-power-bi.md)
- [Erstellen und Verwenden von Sichten in einem serverlosen SQL-Pool](create-use-views.md)
- [Tutorial: Analysieren von Azure Open Datasets und Visualisieren der Ergebnisse in Azure Synapse Studio mithilfe von SQL On-Demand](./tutorial-data-analyst.md)
- Besuchen Sie den [Synapse-Link zur Selbsthilfeseite für Cosmos DB](resources-self-help-sql-on-demand.md#cosmos-db), wenn Fehler oder Leistungsprobleme auftreten.
- Sehen Sie sich das Learn-Modul zum [Abfragen von Azure Cosmos DB mit serverlosem SQL für Azure Synapse Analytics](/learn/modules/query-azure-cosmos-db-with-sql-serverless-for-azure-synapse-analytics/) an.