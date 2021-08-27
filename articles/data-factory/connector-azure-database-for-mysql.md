---
title: Kopieren und Transformieren von Daten in Azure Database for MySQL
titleSuffix: Azure Data Factory & Azure Synapse
description: Hier erfahren Sie, wie Sie Daten in Azure Database for MySQL mithilfe von Azure Data Factory kopieren und transformieren.
ms.author: susabat
author: ssabat
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 03/10/2021
ms.openlocfilehash: 338405fd04617d3b1d563b7ac95d7233d23a8678
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122639974"
---
# <a name="copy-and-transform-data-in-azure-database-for-mysql-by-using-azure-data-factory"></a>Kopieren und Transformieren von Daten in Azure Database for MySQL mit Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie Daten mithilfe der Copy-Aktivität in Azure Data Factory aus und in Azure Database for MySQL kopieren sowie Daten mit einem Datenfluss in Azure Database for MySQL transformieren. Informationen zu Azure Data Factory finden Sie im [Einführungsartikel](introduction.md).

Dieser Connector ist speziell auf den [Dienst „Azure Database for MySQL“](../mysql/overview.md) ausgelegt. Verwenden Sie [MySQL-Connector](connector-mysql.md), um Daten aus der generischen MySQL-Datenbank zu kopieren, die lokal oder in der Cloud angeordnet ist.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Dieser Azure Database for MySQL-Connector wird für folgende Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Mapping Data Flow](concepts-data-flow-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)

## <a name="getting-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für den Azure Database for MySQL-Connector verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den verknüpften Azure Database for MySQL-Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf Folgendes festgelegt werden: **AzureMySql** | Ja |
| connectionString | Geben Sie Informationen an, die zum Herstellen einer Verbindung mit der Azure Database for MySQL-Instanz erforderlich sind. <br/> Sie können auch das Kennwort in Azure Key Vault speichern und die `password`-Konfiguration aus der Verbindungszeichenfolge pullen. Ausführlichere Informationen finden Sie in den folgenden Beispielen und im Artikel [Speichern von Anmeldeinformationen in Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| connectVia | Die [Integrationslaufzeit](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden muss. Sie können die Azure-Integrationslaufzeit oder selbstgehostete Integrationslaufzeit verwenden (sofern sich Ihr Datenspeicher in einem privaten Netzwerk befindet). Wenn keine Option angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. |Nein |

Eine typische Verbindungszeichenfolge ist `Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>`. Weitere Eigenschaften, die Sie für Ihren Fall festlegen können:

| Eigenschaft | BESCHREIBUNG | Tastatur | Erforderlich |
|:--- |:--- |:--- |:--- |
| SSLMode | Diese Option gibt an, ob der Treiber beim Herstellen der Verbindung mit MySQL die TLS-Verschlüsselung und -Überprüfung verwendet. Beispiel: `SSLMode=<0/1/2/3/4>`| DISABLED (0) / PREFERRED (1) **(Standard)** / REQUIRED (2) / VERIFY_CA (3) / VERIFY_IDENTITY (4) | Nein |
| UseSystemTrustStore | Diese Option gibt an, ob ein Zertifizierungsstellenzertifikat aus dem Vertrauensspeicher des Systems oder aus einer angegebenen PEM-Datei verwendet werden soll. Beispiel: `UseSystemTrustStore=<0/1>;`| Enabled (1) / Disabled (0) **(Standard)** | Nein |

**Beispiel:**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Beispiel: Speichern des Kennworts in Azure Key Vault**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). Dieser Abschnitt enthält eine Liste mit den Eigenschaften, die vom Azure Database for MySQL-Dataset unterstützt werden.

Legen Sie die „type“-Eigenschaft des Datasets auf **AzureMySqlTable** fest, um Daten von Azure Database for MySQL zu kopieren. Folgende Eigenschaften werden unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft des Datasets muss auf folgenden Wert festgelegt werden: **AzureMySqlTable** | Ja |
| tableName | Name der Tabelle in der MySQL-Datenbank. | Nein (wenn „query“ in der Aktivitätsquelle angegeben ist) |

**Beispiel**

```json
{
    "name": "AzureMySQLDataset",
    "properties": {
        "type": "AzureMySqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure MySQL linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste mit den Eigenschaften, die von der Azure Database for MySQL-Quelle und -Senke unterstützt werden.

### <a name="azure-database-for-mysql-as-source"></a>Azure Database for MySQL als Quelle

Beim Kopieren von Daten aus Azure Database for MySQL werden die folgenden Eigenschaften im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf Folgendes festgelegt werden: **AzureMySqlSource** | Ja |
| Abfrage | Verwendet die benutzerdefinierte SQL-Abfrage zum Lesen von Daten. Beispiel: `"SELECT * FROM MyTable"`. | Nein (wenn „tableName“ im Dataset angegeben ist) |
| queryCommandTimeout | Die Wartezeit vor dem Timeout der Abfrageanforderung. Die Standardeinstellung ist 120 Minuten (02:00:00). | Nein |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromAzureDatabaseForMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure MySQL input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzureMySqlSource",
                "query": "<custom query e.g. SELECT * FROM MyTable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-mysql-as-sink"></a>Azure Database for MySQL als Senke

Beim Kopieren von Daten nach Azure Database for MySQL werden die folgenden Eigenschaften im Abschnitt **sink** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Senke der Kopieraktivität muss auf Folgendes festgelegt sein: **AzureMySqlSink** | Ja |
| preCopyScript | Geben Sie eine SQL-Abfrage für die Kopieraktivität an, die ausgeführt werden soll, bevor bei jeder Ausführung Daten in Azure Database for MySQL geschrieben werden. Sie können diese Eigenschaft nutzen, um die vorab geladenen Daten zu bereinigen. | Nein |
| writeBatchSize | Fügt Daten in die Azure Database for MySQL-Tabelle ein, wenn die Puffergröße „writeBatchSize“ erreicht.<br>Als Wert ist eine ganze Zahl (Integer) zulässig, mit der die Anzahl von Zeilen angegeben wird. | Nein (Standardwert ist 10.000) |
| writeBatchTimeout | Die Wartezeit für den Abschluss der Batcheinfügung, bis das Timeout wirksam wird.<br>Zulässige Werte sind Timespan-Werte. Beispiel: 00:30:00 (30 Minuten). | Nein (Standardwert ist 00:00:30) |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure MySQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureMySqlSink",
                "preCopyScript": "<custom SQL script>",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Eigenschaften von Mapping Data Flow

Beim Transformieren von Daten in einen Zuordnungsdatenfluss können Sie über Azure Database for MySQL Tabellen lesen und in diese schreiben. Weitere Informationen finden Sie unter [Quellentransformation](data-flow-source.md) und [Senkentransformation](data-flow-sink.md) in Zuordnungsdatenflüssen. Sie können ein Azure Database for MySQL-Dataset oder ein [Inlinedataset](data-flow-source.md#inline-datasets) als Quell- und Senkentyp verwenden.

### <a name="source-transformation"></a>Quellentransformation

In der folgenden Tabelle werden die von der Azure Database for PostgreSQL-Quelle unterstützten Eigenschaften aufgeführt. Sie können diese Eigenschaften auf der Registerkarte **Quelloptionen** bearbeiten.

| Name | BESCHREIBUNG | Erforderlich | Zulässige Werte | Datenflussskript-Eigenschaft |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Tabelle | Wenn Sie „Tabelle“ als Eingabe auswählen, ruft der Datenfluss alle Daten aus der im Dataset angegebenen Tabelle ab. | Nein | - |*(nur für Inlinedataset)*<br>tableName |
| Abfrage | Wenn Sie „Abfrage“ als Eingabe auswählen, geben Sie eine SQL-Abfrage zum Abrufen von Daten aus der Quelle an, die Vorrang vor jeder im Dataset angegebenen Tabelle hat. Die Verwendung von Abfragen stellt eine gute Möglichkeit dar, um die Zeilen für Tests oder Suchvorgänge zu verringern.<br><br>Die **Order By**-Klausel wird nicht unterstützt. Sie können aber eine vollständige SELECT FROM-Anweisung festlegen. Sie können auch benutzerdefinierte Tabellenfunktionen verwenden. **select * from udfGetData()** ist eine benutzerdefinierte Funktion in SQL, mit der eine Tabelle zurückgegeben wird, die Sie im Datenfluss verwenden können.<br>Abfragebeispiel: `select * from mytable where customerId > 1000 and customerId < 2000` oder `select * from "MyTable"`.| Nein | String | Abfrage |
| Batchgröße | Geben Sie eine Batchgröße an, um große Datenmengen in Batches zu segmentieren. | Nein | Integer | batchSize |
| Isolationsstufe | Wählen Sie eine der folgenden Isolationsstufen aus:<br>– Lesen zugesichert<br>– Lesen nicht zugesichert (Standard)<br>– Wiederholbarer Lesevorgang<br>– Serialisierbar<br>– Keine (Isolationsstufe ignorieren) | Nein | <small>READ_COMMITTED<br/>READ_UNCOMMITTED<br/>REPEATABLE_READ<br/>SERIALIZABLE<br/>NONE</small> |isolationLevel |

#### <a name="azure-database-for-mysql-source-script-example"></a>Beispielskript für die Azure DB for MySQL-Quelle

Wenn Sie Azure Database for MySQL als Quelltyp verwenden, sieht das zugehörige Datenflussskript wie folgt aus:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    isolationLevel: 'READ_UNCOMMITTED',
    query: 'select * from mytable',
    format: 'query') ~> AzureMySQLSource
```

### <a name="sink-transformation"></a>Senkentransformation

In der folgenden Tabelle werden die Eigenschaften aufgeführt, die von der Azure Database for MySQL-Senke unterstützt werden. Sie können diese Eigenschaften auf der Registerkarte **Senkenoptionen** bearbeiten.

| Name | BESCHREIBUNG | Erforderlich | Zulässige Werte | Datenflussskript-Eigenschaft |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Updatemethode | Geben Sie an, welche Vorgänge für das Datenbankziel zulässig sind. Standardmäßig sind lediglich Einfügevorgänge zulässig.<br>Um Aktualisierungs-, Upsert- oder Löschaktionen auf Zeilen anzuwenden, muss eine [Zeilenänderungstransformation](data-flow-alter-row.md) zum Kennzeichnen von Zeilen für diese Aktionen erfolgen. | Ja | `true` oder `false` | deletable <br/>insertable <br/>updateable <br/>upsertable |
| Schlüsselspalten | Für Update-, Upsert- und Löschvorgänge müssen Schlüsselspalten festgelegt werden, um die Zeile zu bestimmen, die geändert werden soll.<br>Der Spaltenname, den Sie als Schlüssel auswählen, wird als Teil der nachfolgenden Update-, Upsert- und Löschvorgänge verwendet. Daher müssen Sie eine Spalte auswählen, die in der Senkenzuordnung vorhanden ist. | Nein | Array | keys |
| Schreiben von Schlüsselspalten überspringen | Wenn Sie den Wert nicht in die Schlüsselspalte schreiben möchten, wählen Sie „Schreiben von Schlüsselspalten überspringen“ aus. | Nein | `true` oder `false` | skipKeyWrites |
| Aktion table |Bestimmt, ob die Zieltabelle vor dem Schreiben neu erstellt werden soll oder alle Zeilen aus der Zieltabelle entfernt werden sollen.<br>- **Keine**: Es wird keine Aktion an der Tabelle vorgenommen.<br>- **Neu erstellen**: Die Tabelle wird gelöscht und neu erstellt. Erforderlich, wenn eine neue Tabelle dynamisch erstellt wird.<br>- **Abschneiden**: Alle Zeilen werden aus der Zieltabelle entfernt. | Nein | `true` oder `false` | Neu erstellen<br/>truncate |
| Batchgröße | Geben Sie an, wie viele Zeilen in die einzelnen Batches geschrieben werden. Durch größere Batches werden zwar Komprimierung und Arbeitsspeicheroptimierung verbessert, beim Zwischenspeichern von Daten besteht aber die Gefahr, dass Ausnahmen wegen unzureichenden Arbeitsspeichers auftreten. | Nein | Integer | batchSize |
| Pre- und Post-SQL-Skripts | Geben Sie mehrzeilige SQL-Skripts an, die ausgeführt werden, bevor Daten in die Senkendatenbank geschrieben werden (Vorverarbeitung) und danach (Nachbearbeitung). | Nein | String | preSQLs<br>postSQLs |

#### <a name="azure-database-for-mysql-sink-script-example"></a>Beispielskript für die Azure DB for MySQL-Senke

Wenn Sie Azure Database for MySQL als Senkentyp verwenden, sieht das zugehörige Datenflussskript wie folgt aus:

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    deletable:false,
    insertable:true,
    updateable:true,
    upsertable:true,
    keys:['keyColumn'],
    format: 'table',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> AzureMySQLSink
```

## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [Lookup-Aktivität](control-flow-lookup-activity.md).

## <a name="data-type-mapping-for-azure-database-for-mysql"></a>Datentypzuordnung für Azure Database for MySQL

Beim Kopieren von Daten aus Azure Database for MySQL werden die folgenden Zuordnungen von MySQL-Datentypen zu Azure Data Factory-Zwischendatentypen verwendet. Unter [Schema- und Datentypzuordnungen](copy-activity-schema-and-type-mapping.md) erfahren Sie, wie Sie Aktivitätszuordnungen für Quellschema und Datentyp in die Senke kopieren.

| Azure Database for MySQL-Datentyp | Data Factory-Zwischendatentyp |
|:--- |:--- |
| `bigint` |`Int64` |
| `bigint unsigned` |`Decimal` |
| `bit` |`Boolean` |
| `bit(M), M>1`|`Byte[]`|
| `blob` |`Byte[]` |
| `bool` |`Int16` |
| `char` |`String` |
| `date` |`Datetime` |
| `datetime` |`Datetime` |
| `decimal` |`Decimal, String` |
| `double` |`Double` |
| `double precision` |`Double` |
| `enum` |`String` |
| `float` |`Single` |
| `int` |`Int32` |
| `int unsigned` |`Int64`|
| `integer` |`Int32` |
| `integer unsigned` |`Int64` |
| `long varbinary` |`Byte[]` |
| `long varchar` |`String` |
| `longblob` |`Byte[]` |
| `longtext` |`String` |
| `mediumblob` |`Byte[]` |
| `mediumint` |`Int32` |
| `mediumint unsigned` |`Int64` |
| `mediumtext` |`String` |
| `numeric` |`Decimal` |
| `real` |`Double` |
| `set` |`String` |
| `smallint` |`Int16` |
| `smallint unsigned` |`Int32` |
| `text` |`String` |
| `time` |`TimeSpan` |
| `timestamp` |`Datetime` |
| `tinyblob` |`Byte[]` |
| `tinyint` |`Int16` |
| `tinyint unsigned` |`Int16` |
| `tinytext` |`String` |
| `varchar` |`String` |
| `year` |`Int32` |

## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).
