---
title: 'Tutorial: Erstellen eines logischen Data Warehouse unter Verwendung eines serverlosen SQL-Pools'
description: In diesem Tutorial erfahren Sie, wie Sie mithilfe eines serverlosen SQL-Pools ganz einfach ein logisches Data Warehouse für Azure-Datenquellen erstellen.
services: synapse-analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 08/20/2021
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: de0374f2ea26e3fa1dc7d25e7c837187f8914918
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123254737"
---
# <a name="tutorial-create-logical-data-warehouse-with-serverless-sql-pool"></a>Tutorial: Erstellen eines logischen Data Warehouse mit serverlosem SQL-Pool

In diesem Tutorial erfahren Sie, wie Sie ein logisches Data Warehouse (LDW) erstellen, das auf Azure Storage und Azure Cosmos DB basiert.

LDW ist eine relationale Ebene, der Azure-Datenquellen wie Azure Data Lake Storage (ADLS), Azure Cosmos DB-Analysespeicher oder Azure Blob Storage zugrunde liegen.

## <a name="create-an-ldw-database"></a>Erstellen einer LDW-Datenbank

Sie müssen eine benutzerdefinierte Datenbank erstellen, in der Sie Ihre externen Tabellen und Sichten speichern können, von denen auf externe Datenquellen verwiesen wird.

```sql
CREATE DATABASE Ldw
      COLLATE Latin1_General_100_BIN2_UTF8;
```

Diese Sortierung bietet die optimale Leistung beim Lesen von Parquet- und Cosmos DB-Daten. Wenn Sie die Datenbanksortierung nicht angeben möchten, muss die Sortierung in der Spaltendefinition angegeben werden.

## <a name="configure-data-sources-and-formats"></a>Konfigurieren von Datenquellen und -formaten

Als Erstes müssen Sie die Datenquelle konfigurieren und das Dateiformat remote gespeicherter Daten angeben.

### <a name="create-data-source"></a>Erstellen der Datenquelle

Datenquellen stellen Verbindungszeichenfolgeninformationen dar, die beschreiben, wo Ihre Daten platziert werden und wie Sie sich bei Ihrer Datenquelle authentifizieren.

Das folgende Beispiel zeigt eine Datenquellendefinition mit Verweis auf das öffentliche [ECDC-COVID 19-Dataset aus Azure Open Datasets](../../open-datasets/dataset-ecdc-covid-cases.md):

```sql
CREATE EXTERNAL DATA SOURCE ecdc_cases WITH (
    LOCATION = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/'
);
```

Ein Aufrufer kann ohne Anmeldeinformationen auf die Datenquelle zugreifen, wenn ein Besitzer der Datenquelle anonymen Zugriff zugelassen hat oder der Azure AD-Identität des Aufrufers explizit Zugriff gewährt.

Sie können explizit benutzerdefinierte Anmeldeinformationen definieren, die beim Zugriff auf Daten in einer externen Datenquelle verwendet werden.
- [Verwaltete Identität](develop-storage-files-storage-access-control.md?tabs=managed-identity) des Synapse-Arbeitsbereichs
- [Shared Access Signature](develop-storage-files-storage-access-control.md?tabs=shared-access-signature) des Azure-Speichers
- Schreibgeschützter Cosmos DB-Kontoschlüssel, mit dem Sie analytischen Cosmos DB-Speicher lesen können

Zunächst muss in der Datenbank ein Hauptschlüssel erstellt werden:
```sql
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'Setup you password - you need to create master key only once';
```

In der folgenden externen Datenquelle soll vom Synapse SQL-Pool eine verwaltete Identität des Arbeitsbereichs verwendet werden, um auf Daten im Speicher zuzugreifen.

```sql
CREATE DATABASE SCOPED CREDENTIAL WorkspaceIdentity
WITH IDENTITY = 'Managed Identity';
GO
CREATE EXTERNAL DATA SOURCE ecdc_cases WITH (
    LOCATION = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/',
    CREDENTIAL = WorkspaceIdentity
);
```

Für den Zugriff auf den Cosmos DB-Analysespeicher müssen Anmeldeinformationen definiert werden, die einen schreibgeschützten Cosmos DB-Schlüssel enthalten.

```sql
CREATE DATABASE SCOPED CREDENTIAL MyCosmosDbAccountCredential
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
```

Jeder Benutzer mit der Rolle „Synapse-Administrator“ kann diese Anmeldeinformationen verwenden, um auf Azure Data Lake Storage oder analytischen Cosmos DB-Speicher zuzugreifen. Wenn Sie Benutzer mit niedrigen Berechtigungen haben, die nicht über die Rolle „Synapse-Administrator“ verfügen, müssten Sie ihnen eine explizite Berechtigung zum Verweisen auf diese datenbankweit gültigen Anmeldeinformationen erteilen:

```sql
GRANT REFERENCES ON DATABASE SCOPED CREDENTIAL::WorkspaceIdentity TO <user>
GO
GRANT REFERENCES ON DATABASE SCOPED CREDENTIAL::MyCosmosDbAccountCredential TO <user>
GO
```

Weitere Details finden Sie auf der Seite [Gewähren von Berechtigungen für DATENBANKWEIT GÜLTIGE ANMELDEINFORMATIONEN](/sql/t-sql/statements/grant-database-scoped-credential-transact-sql).

### <a name="define-external-file-formats"></a>Definieren externer Dateiformate

Externe Dateiformate dienen zum Definieren der Struktur der Dateien, die in einer externen Datenquelle gespeichert sind. Sie können externe Dateiformate für Parquet und CSV definieren:

```sql
CREATE EXTERNAL FILE FORMAT ParquetFormat WITH (  FORMAT_TYPE = PARQUET );
GO
CREATE EXTERNAL FILE FORMAT CsvFormat WITH (  FORMAT_TYPE = DELIMITEDTEXT );
```

Weitere Informationen finden Sie in [diesem Artikel](develop-tables-external-tables.md?tabs=native#syntax-for-create-external-file-format).

## <a name="explore-your-data"></a>Untersuchen Ihrer Daten

Nachdem Sie Ihre Datenquellen eingerichtet haben, können Sie Ihre Daten mithilfe der Funktion `OPENROWSET` erkunden. Die Funktion [OPENROWSET](develop-openrowset.md) liest den Inhalt einer Remotedatenquelle (z. B. eine Datei) und gibt ihn als eine Reihe von Zeilen zurück.

```sql
select top 10  *
from openrowset(bulk 'latest/ecdc_cases.parquet',
                data_source = 'ecdc_cases',
                format='parquet') as a
```

Mit der Funktion `OPENROWSET` erhalten Sie Informationen zu den Spalten in den externen Dateien oder Containern und können ein Schema Ihrer externen Tabellen und Sichten definieren.

## <a name="create-external-tables-on-azure-storage"></a>Erstellen externer Tabellen für Azure-Speicher

Nach der Schemaerkennung können Sie externe Tabellen und Sichten auf der Grundlage Ihrer externen Datenquellen erstellen. Es empfiehlt sich, Ihre Tabellen und Sichten in Datenbankschemas zu strukturieren. In der folgenden Abfrage können Sie ein Schema erstellen, bei dem alle Objekte, von denen auf das ECDC-COVID-Dataset zugegriffen wird, in Azure Data Lake Storage platziert werden:

```sql
create schema ecdc_adls;
```

Die Datenbankschemas ermöglichen das Gruppieren der Objekte sowie das Definieren schemaspezifischer Berechtigungen. 

Nachdem Sie die Schemas definiert haben, können Sie externe Tabellen erstellen, von denen auf die Dateien verwiesen wird.
In der folgenden externen Tabelle wird auf die ECDC-COVID-Parquet-Datei im Azure-Speicher verwiesen:

```sql
create external table ecdc_adls.cases (
    date_rep                   date,
    day                        smallint,
    month                      smallint,
    year                       smallint,
    cases                      smallint,
    deaths                     smallint,
    countries_and_territories  varchar(256),
    geo_id                     varchar(60),
    country_territory_code     varchar(16),
    pop_data_2018              int,
    continent_exp              varchar(32),
    load_date                  datetime2(7),
    iso_country                varchar(16)
) with (
    data_source= ecdc_cases,
    location = 'latest/ecdc_cases.parquet',
    file_format = ParquetFormat
);
```

Verwenden Sie für Zeichenfolgen- und Zahlenspalten jeweils den kleinstmöglichen Typ, um die Leistung Ihrer Abfragen zu optimieren.

## <a name="create-views-on-azure-cosmos-db"></a>Erstellen von Sichten für Azure Cosmos DB

Als Alternative zu externen Tabellen können Sie Sichten auf der Grundlage Ihrer externen Daten erstellen. 

Die Sichten sollten ähnlich wie bei den Tabellen im vorherigen Beispiel in separaten Schemas platziert werden:

```sql
create schema ecdc_cosmosdb;
```

Nun können Sie eine Sicht im Schema mit Verweis auf einen Cosmos DB-Container erstellen:

```sql
CREATE OR ALTER VIEW ecdc_cosmosdb.Ecdc
AS SELECT *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) WITH
     ( date_rep varchar(20), 
       cases bigint,
       geo_id varchar(6) 
     ) as rows
```

Zur Optimierung der Leistung empfiehlt es sich, in der Schemadefinition `WITH` die kleinstmöglichen Typen zu verwenden.

> [!NOTE]
> Platzieren Sie Ihren Azure Cosmos DB-Kontoschlüssel in separaten Anmeldeinformationen, und verwenden Sie die Funktion `OPENROWSET`, um auf diese Anmeldeinformationen zu verweisen.
> Lassen Sie Ihren Kontoschlüssel nicht in der Sichtdefinition.

## <a name="access-and-permissions"></a>Zugriff und Berechtigungen

Zum Schluss sollten Sie noch Datenbankbenutzer erstellen, die auf Ihr LDW zugreifen können, und ihnen Berechtigungen zum Auswählen von Daten aus den externen Tabellen und Sichten erteilen.
Im folgenden Skript erfahren Sie, wie Sie einen neuen Benutzer hinzufügen, der mithilfe einer Azure AD-Identität authentifiziert wird:

```sql
CREATE USER [jovan@contoso.com] FROM EXTERNAL PROVIDER;
GO
```

Anstelle von Azure AD-Prinzipalen können Sie SQL-Prinzipale erstellen, die sich mit dem Anmeldenamen und Kennwort authentifizieren.

```sql
CREATE LOGIN [jovan] WITH PASSWORD = 'My Very strong Password ! 1234';
CREATE USER [jovan] FROM LOGIN [jovan];
```

In beiden Fällen können Sie den Benutzern Berechtigungen zuweisen.

```sql
DENY ADMINISTER DATABASE BULK OPERATIONS TO [jovan@contoso.com]
GO
GRANT SELECT ON SCHEMA::ecdc_adls TO [jovan@contoso.com]
GO
GRANT SELECT ON OBJECT::ecdc_cosmosDB.cases TO [jovan@contoso.com]
GO
GRANT REFERENCES ON DATABASE SCOPED CREDENTIAL::MyCosmosDbAccountCredential TO [jovan@contoso.com]
GO
```

Die Sicherheitsregeln hängen von Ihren Sicherheitsrichtlinien ab. Einige allgemeine Richtlinien lauten:
- Verweigern Sie den neuen Benutzern die Berechtigung `ADMINISTER DATABASE BULK OPERATIONS`, da sie nur Daten aus den von Ihnen vorbereiteten externen Tabellen und Sichten lesen können sollen.
- Gewähren Sie die Berechtigung `SELECT` nur für die Tabellen, die für einige Benutzer nutzbar sein sollen.
- Wenn Sie Datenzugriff mithilfe der Sichten gewähren, erteilen Sie den Anmeldeinformationen, die für den Zugriff auf externe Datenquellen verwendet werden, die Berechtigung `REFERENCES`.

Dieser Benutzer verfügt über die Mindestberechtigungen, die zum Abfragen externer Daten erforderlich sind. Wenn Sie einen Poweruser erstellen möchten, der Berechtigungen, externe Tabellen und Ansichten einrichten kann, können Sie dem Benutzer die Berechtigung `CONTROL` erteilen:

```sql
GRANT CONTROL TO [jovan@contoso.com]
```

### <a name="role-based-security"></a>Rollenbasierte Sicherheit

Anstatt den einzelnen Benutzern Berechtigungen zu erteilen, ist es eine bewährte Methode, die Benutzer in Rollen zu organisieren und Berechtigungen auf Rollenebene zu verwalten.
Das folgende Codebeispiel erstellt eine neue Rolle, die die Personen darstellt, die COVID-19-Fälle analysieren können, und fügt dieser Rolle drei Benutzer hinzu:

```sql
CREATE ROLE CovidAnalyst;

ALTER ROLE CovidAnalyst ADD MEMBER [jovan@contoso.com];
ALTER ROLE CovidAnalyst ADD MEMBER [milan@contoso.com];
ALTER ROLE CovidAnalyst ADD MEMBER [petar@contoso.com];
```

Sie können die Berechtigungen allen Benutzern zuweisen, die zur Gruppe gehören:

```sql
GRANT SELECT ON SCHEMA::ecdc_cosmosdb TO [CovidAnalyst];
GO
DENY SELECT ON SCHEMA::ecdc_adls TO [CovidAnalyst];
GO
DENY ADMINISTER DATABASE BULK OPERATIONS TO [CovidAnalyst];
```

Diese rollenbasierte Sicherheitszugriffssteuerung kann die Verwaltung Ihrer Sicherheitsregeln vereinfachen.

## <a name="next-steps"></a>Nächste Schritte

- Im Tutorial [Verwenden eines serverlosen SQL-Pools mit Power BI Desktop und Erstellen eines Berichts](tutorial-connect-power-bi-desktop.md) erfahren Sie, wie Sie einen serverlosen SQL-Pool mit Power BI Desktop verbinden und Berichte erstellen.
- Informationen dazu, wie externe Tabellen in einem serverlosen SQL-Pool verwendet werden, finden Sie unter [Verwenden externer Tabellen mit Synapse SQL](develop-tables-external-tables.md?tabs=sql-pool).
