---
title: Daten von Amazon RDS für SQL Server kopieren
titleSuffix: Azure Data Factory & Azure Synapse
description: Hier erfahren Sie, wie Sie Daten mithilfe von Azure Data Factory oder Azure Synapse Analytics-Pipelines von Amazon RDS für eine SQL Server-Datenbank kopieren, die lokal oder in einer Azure-VM gehostet wird.
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 10/18/2021
ms.openlocfilehash: b75a59d758c5447cec0eb66855866e1349fbd085
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130176231"
---
# <a name="copy-data-from-amazon-rds-for-sql-server-by-using-azure-data-factory-or-azure-synapse-analytics"></a>Kopieren von Daten aus Amazon RDS für SQL Server mithilfe von Azure Data Factory oder Azure Synapse Analytics

In diesem Artikel wird beschrieben, wie Sie die Copy-Aktivität in Azure Data Factory- und Azure Synapse-Pipelines verwenden, um Daten aus Amazon RDS for SQL Server zu kopieren. Weitere Informationen finden Sie im Einführungsartikel zu [Azure Data Factory](introduction.md) oder [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md).

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Dieser Amazon RDS for SQL Server-Connector wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)
- [GetMetadata-Aktivität](control-flow-get-metadata-activity.md)

Sie können Daten aus einer Amazon RDS for SQL Server-Datenbank in jeden unterstützten Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die von der Copy-Aktivität als Senken unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

Insbesondere unterstützt dieser Amazon RDS for SQL Server-Connector:

- SQL Server, Version 2005 und höher.
- Kopieren von Daten unter Verwendung der SQL- oder Windows-Authentifizierung
- Als Quelle das Abrufen von Daten mithilfe einer SQL-Abfrage oder gespeicherten Prozedur Sie können sich auch für das parallele Kopieren aus der Amazon RDS for SQL Server-Quelle entscheiden. Einzelheiten finden Sie im Abschnitt [Paralleles Kopieren aus SQL-Datenbank](#parallel-copy-from-sql-database).

[SQL Server Express LocalDB](/sql/database-engine/configure-windows/sql-server-express-localdb) wird nicht unterstützt.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-an-amazon-rds-for-sql-server-linked-service-using-ui"></a>Erstellen eines verknüpften Amazon RDS for SQL Server-Diensts über die Benutzeroberfläche

Führen Sie die folgenden Schritte aus, um über die Benutzeroberfläche des Azure-Portals einen verknüpften Amazon RDS for SQL Server-Dienst zu erstellen.

1. Navigieren Sie in Ihrem Azure Data Factory- oder Synapse-Arbeitsbereich zur Registerkarte „Verwalten“, wählen Sie „Verknüpfte Dienste“ aus, und klicken Sie dann auf „Neu“:

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Screenshot: Erstellen eines neuen verknüpften Diensts über die Azure Data Factory-Benutzeroberfläche":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Screenshot: Erstellen eines neuen verknüpften Diensts mit der Azure Synapse-Benutzeroberfläche":::

2. Suchen Sie Amazon RDS for SQL Server, und wählen Sie den Amazon RDS for SQL Server-Connector aus.

    :::image type="content" source="media/connector-amazon-rds-for-sql-server/amazon-rds-for-sql-server-connector.png" alt-text="Screenshot: Amazon RDS for SQL Server-Connector.":::    

1. Konfigurieren Sie die Dienstdetails, testen Sie die Verbindung, und erstellen Sie den neuen verknüpften Dienst.

    :::image type="content" source="media/connector-amazon-rds-for-sql-server/configure-amazon-rds-for-sql-server-linked-service.png" alt-text="Screenshot: Konfiguration eines verknüpften Amazon RDS for SQL Server-Diensts.":::

## <a name="connector-configuration-details"></a>Details zur Connectorkonfiguration

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Entitäten in Data Factory- und Synapse-Pipelines speziell für den Amazon RDS for SQL Server-Datenbankconnector verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Die folgenden Eigenschaften werden für den verknüpften Amazon RDS for SQL Server-Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| Typ | Die Eigenschaft „Typ“ muss auf **AmazonRdsForSqlServer** festgelegt werden. | Ja |
| connectionString |Geben Sie **connectionString**-Informationen an, die zum Herstellen einer Verbindung mit der Amazon RDS for SQL Server-Datenbank mithilfe der SQL- oder Windows-Authentifizierung benötigt werden. Sehen Sie sich die folgenden Beispiele an.<br/>Sie können auch ein Kennwort in Azure Key Vault speichern. Bei Verwendung der SQL-Authentifizierung pullen Sie die `password`-Konfiguration aus der Verbindungszeichenfolge. Weitere Informationen finden Sie im JSON-Beispiel unter der Tabelle sowie unter [Speichern von Anmeldeinformationen in Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| userName |Geben Sie einen Benutzernamen an, wenn Sie die Windows-Authentifizierung verwenden. Ein Beispiel lautet **domainname\\username**. |Nein |
| password |Geben Sie das Kennwort für das Benutzerkonto an, das Sie für den Benutzernamen angegeben haben. Markieren Sie dieses Feld als **SecureString**, um es sicher zu speichern. Alternativ können Sie [auf ein in Azure Key Vault gespeichertes Geheimnis verweisen](store-credentials-in-key-vault.md). |Nein |
| Always Encrypted-Einstellungen | Geben Sie die erforderlichen **alwaysEncryptedSettings**-Informationen an, damit Always Encrypted vertrauliche Daten schützen kann, die in Amazon RDS for SQL Server mithilfe der verwalteten Identität oder des Dienstprinzipals gespeichert sind. Weitere Informationen finden Sie im JSON-Beispiel unter der Tabelle sowie in dem Bereich [Verwenden von Always Encrypted](#using-always-encrypted). Wenn keine Angabe erfolgt, ist die standardmäßige Always Encrypted-Einstellung deaktiviert. |Nein |
| connectVia | Diese [Integration Runtime](concepts-integration-runtime.md) wird zum Herstellen einer Verbindung mit dem Datenspeicher verwendet. Weitere Informationen finden Sie im Abschnitt [Voraussetzungen](#prerequisites). Wenn kein Wert angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. |Nein |

> [!NOTE]
> Amazon RDS for SQL Server [**Always Encrypted**](/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-ver15&preserve-view=true) wird in Datenflüssen nicht unterstützt. 

>[!TIP]
>Wenn ein Fehler mit dem Fehlercode „UserErrorFailedToConnectToSqlServer“ auftritt und eine Meldung wie „Das Sitzungslimit für die Datenbank ist XXX und wurde erreicht“ angezeigt wird, fügen Sie `Pooling=false` zu Ihrer Verbindungszeichenfolge hinzu, und versuchen Sie es erneut.

**Beispiel 1: SQL-Authentifizierung verwenden**

```json
{
    "name": "AmazonSqlLinkedService",
    "properties": {
        "type": "AmazonRdsForSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Beispiel 2: Verwenden der SQL-Authentifizierung mit einem Kennwort in Azure Key Vault**

```json
{
    "name": "AmazonSqlLinkedService",
    "properties": {
        "type": "AmazonRdsForSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;",
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

**Beispiel 3: Windows-Authentifizierung verwenden**

```json
{
    "name": "AmazonSqlLinkedService",
    "properties": {
        "type": "AmazonRdsForSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;",
            "userName": "<domain\\username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Beispiel 4: Verwenden von Always Encrypted**

```json
{
    "name": "AmazonSqlLinkedService",
    "properties": {
        "type": "AmazonRdsForSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
        },
        "alwaysEncryptedSettings": {
            "alwaysEncryptedAkvAuthType": "ServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
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

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die vom Amazon RDS for SQL Server-Dataset unterstützt werden.

Zum Kopieren von Daten aus einer Amazon RDS for SQL Server-Datenbank werden die folgenden Eigenschaften unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| Typ | Die type-Eigenschaft des Datasets muss auf **AmazonRdsForSqlServerTable** festgelegt werden. | Ja |
| schema | Name des Schemas. |Nein |
| table | Name der Tabelle/Ansicht. |Nein |
| tableName | Name der Tabelle/Ansicht mit Schema. Diese Eigenschaft wird aus Gründen der Abwärtskompatibilität weiterhin unterstützt. Verwenden Sie für eine neue Workload `schema` und `table`. | Nein |

**Beispiel**

```json
{
    "name": "AmazonRdsForSQLServerDataset",
    "properties":
    {
        "type": "AmazonRdsForSqlServerTable",
        "linkedServiceName": {
            "referenceName": "<Amazon RDS for SQL Server linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der Amazon RDS for SQL Server-Quelle unterstützt werden.

### <a name="amazon-rds-for-sql-server-as-a-source"></a>Amazon RDS for SQL Server als Quelle

>[!TIP]
>Weitere Informationen zum effizienten Laden von Daten aus Amazon RDS for SQL Server mittels Datenpartitionierung finden Sie unter [Paralleles Kopieren aus SQL-Datenbank](#parallel-copy-from-sql-database).

Legen Sie zum Kopieren von Daten aus Amazon RDS for SQL Server den Quelltyp in der Copy-Aktivität auf **AmazonRdsForSqlServerSource** fest. Die folgenden Eigenschaften werden im Abschnitt „source“ der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| Typ | Die type-Eigenschaft der Quelle der Copy-Aktivität muss auf **AmazonRdsForSqlServerSource** festgelegt werden. | Ja |
| sqlReaderQuery |Verwendet die benutzerdefinierte SQL-Abfrage zum Lesen von Daten. z. B. `select * from MyTable`. |Nein |
| sqlReaderStoredProcedureName |Diese Eigenschaft ist der Name der gespeicherten Prozedur, die Daten aus der Quelltabelle liest. Die letzte SQL-Anweisung muss eine SELECT-Anweisung in der gespeicherten Prozedur sein. |Nein |
| storedProcedureParameters |Diese Parameter werden für die gespeicherte Prozedur verwendet.<br/>Zulässige Werte sind Namen oder Name-Wert-Paare. Die Namen und die Groß-/Kleinschreibung von Parametern müssen den Namen und der Groß-/Kleinschreibung der Parameter der gespeicherten Prozedur entsprechen. |Nein |
| isolationLevel | Gibt das Sperrverhalten für Transaktionen für die SQL-Quelle an. Zulässige Werte sind: **ReadCommitted**, **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Snapshot**. Ohne Angabe wird die Standardisolationsstufe der Datenbank verwendet. Weitere Informationen finden Sie in [dieser Dokumentation](/dotnet/api/system.data.isolationlevel). | Nein |
| partitionOptions | Gibt die Datenpartitionierungsoptionen an, die zum Laden von Daten aus Amazon RDS for SQL Server verwendet werden. <br>Zulässige Werte sind: **None** (Standardwert), **PhysicalPartitionsOfTable** und **DynamicRange**.<br>Wenn eine Partitionsoption aktiviert ist (nicht `None`), wird der Grad an Parallelität zum gleichzeitigen Laden von Daten aus Amazon RDS for SQL Server durch die Einstellung [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) für die Copy-Aktivität gesteuert. | Nein |
| partitionSettings | Geben Sie die Gruppe der Einstellungen für die Datenpartitionierung an. <br>Verwenden Sie diese Option, wenn die Partitionsoption nicht `None` lautet. | Nein |
| ***Unter `partitionSettings`:*** | | |
| partitionColumnName | Geben Sie den Namen der Quellspalte als **„integer“ oder „date/datetime“** (`int`, `smallint`, `bigint`, `date`, `smalldatetime`, `datetime`, `datetime2` oder `datetimeoffset`) an, der von der Bereichspartitionierung für paralleles Kopieren verwendet wird. Ohne Angabe wird der Index oder der Primärschlüssel der Tabelle automatisch erkannt und als Partitionsspalte verwendet.<br>Verwenden Sie diese Option, wenn die Partitionsoption `DynamicRange` lautet. Wenn Sie die Quelldaten mithilfe einer Abfrage abrufen, integrieren Sie `?AdfDynamicRangePartitionCondition ` in die WHERE-Klausel. Ein Beispiel finden Sie im Abschnitt [Paralleles Kopieren aus SQL-Datenbank](#parallel-copy-from-sql-database). | Nein |
| partitionUpperBound | Der maximale Wert der Partitionsspalte für das Teilen des Partitionsbereichs. Dieser Wert wird zur Entscheidung über den Partitionssprung verwendet, nicht zum Filtern der Zeilen in der Tabelle. Alle Zeilen in der Tabelle oder im Abfrageergebnis werden partitioniert und kopiert. Wenn nicht angegeben, wird der Wert für die Kopieraktivität automatisch erkannt.  <br>Verwenden Sie diese Option, wenn die Partitionsoption `DynamicRange` lautet. Ein Beispiel finden Sie im Abschnitt [Paralleles Kopieren aus SQL-Datenbank](#parallel-copy-from-sql-database). | Nein |
| partitionLowerBound | Der minimale Wert der Partitionsspalte für das Teilen des Partitionsbereichs. Dieser Wert wird zur Entscheidung über den Partitionssprung verwendet, nicht zum Filtern der Zeilen in der Tabelle. Alle Zeilen in der Tabelle oder im Abfrageergebnis werden partitioniert und kopiert. Wenn nicht angegeben, wird der Wert für die Kopieraktivität automatisch erkannt.<br>Verwenden Sie diese Option, wenn die Partitionsoption `DynamicRange` lautet. Ein Beispiel finden Sie im Abschnitt [Paralleles Kopieren aus SQL-Datenbank](#parallel-copy-from-sql-database). | Nein |

**Beachten Sie folgende Punkte:**

- Wenn **sqlReaderQuery** für **AmazonRdsForSqlServerSource** angegeben ist, führt die Copy-Aktivität diese Abfrage für die Amazon RDS for SQL Server-Quelle aus, um die Daten abzurufen. Sie können auch eine gespeicherte Prozedur angeben, indem Sie **sqlReaderStoredProcedureName** und **storedProcedureParameters** angeben, sofern die gespeicherten Prozeduren Parameter verwenden.
- Wenn Sie eine gespeicherte Prozedur in der Quelle zum Abrufen von Daten verwenden und die gespeicherte Prozedur so konzipiert ist, dass beim Übergeben eines anderen Parameterwerts ein anderes Schema zurückgegeben wird, tritt möglicherweise ein Fehler oder ein unerwartetes Ergebnis auf, wenn Sie ein Schema von der Benutzeroberfläche importieren oder Daten in SQL-Datenbank mit automatischer Tabellenerstellung kopieren.

**Beispiel: Verwenden einer SQL-Abfrage**

```json
"activities":[
    {
        "name": "CopyFromAmazonRdsForSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Amazon RDS for SQL Server input dataset name>",
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
                "type": "AmazonRdsForSqlServerSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Beispiel: Verwenden einer gespeicherten Prozedur**

```json
"activities":[
    {
        "name": "CopyFromAmazonRdsForSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Amazon RDS for SQL Server input dataset name>",
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
                "type": "AmazonRdsForSqlServerSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Definition der gespeicherten Prozedur**

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

## <a name="parallel-copy-from-sql-database"></a>Paralleles Kopieren aus SQL-Datenbank

Der Amazon RDS for SQL Server-Connector in der Copy-Aktivität verfügt über eine integrierte Datenpartitionierung zum parallelen Kopieren von Daten. Die Datenpartitionierungsoptionen befinden sich auf der Registerkarte **Quelle** der Kopieraktivität.

:::image type="content" source="./media/connector-amazon-rds-for-sql-server/connector-amazon-rds-for-sql-partition-options.png" alt-text="Screenshot der Partitionierungsoptionen":::

Wenn Sie partitioniertes Kopieren aktivieren, führt die Copy-Aktivität parallele Abfragen für Ihre Amazon RDS for SQL Server-Quelle aus, um Daten partitionsweise zu laden. Der Parallelitätsgrad wird über die Einstellung [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) der Kopieraktivität gesteuert. Wenn Sie z. B. `parallelCopies` auf vier einstellen, generiert der Dienst gleichzeitig vier Abfragen, die auf der von Ihnen angegebenen Partitionsoption und den Einstellungen basieren, und führt sie aus, wobei jede Abfrage einen Teil der Daten aus Ihrer Amazon RDS for SQL Server-Datenbank abruft.

Es wird empfohlen, das parallele Kopieren mit Datenpartitionierung vor allem dann zu aktivieren, wenn Sie große Datenmengen aus Amazon RDS for SQL Server laden. Im Anschluss finden Sie empfohlene Konfigurationen für verschiedene Szenarien. Beim Kopieren von Daten in einen dateibasierten Datenspeicher wird empfohlen, mehrere Dateien in einen Ordner zu schreiben (nur den Ordnernamen anzugeben). In diesem Fall ist die Leistung besser als beim Schreiben in eine einzelne Datei.

| Szenario                                                     | Empfohlene Einstellungen                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Vollständiges Laden aus einer großen Tabelle mit physischen Partitionen        | **Partitionsoption:** Physische Partitionen der Tabelle. <br><br/>Während der Ausführung erkennt der Dienst automatisch die physischen Partitionen und kopiert Daten nach Partitionen. <br><br/>Um zu überprüfen, ob Ihre Tabelle eine physische Partition besitzt oder nicht, können Sie auf [diese Abfrage](#sample-query-to-check-physical-partition) verweisen. |
| Vollständiges Laden aus einer großen Tabelle ohne physische Partitionen, aber mit einer integer- oder datetime-Spalte für die Datenpartitionierung. | **Partitionsoptionen:** Dynamische Bereichspartitionierung<br>**Partitionsspalte** (optional): Geben Sie die Spalte für die Datenpartitionierung an. Ohne Angabe wird die Primärschlüsselspalte verwendet.<br/>**Obergrenze der Partition** und **Untergrenze der Partition** (optional): Geben Sie an, ob Sie den Partitionssprung bestimmen möchten. Dies dient nicht zum Filtern der Zeilen in der Tabelle; alle Zeilen in der Tabelle werden partitioniert und kopiert. Wenn keine Angabe erfolgt, erkennt die Copy-Aktivität die Werte automatisch und kann je nach MIN- und MAX-Werten lange dauern. Es wird empfohlen, Ober- und Untergrenzen anzugeben. <br><br>Wenn Ihre Partitionsspalte „ID“ z. B. einen Wertebereich von 1 bis 100 aufweist und Sie die untere Grenze auf 20 und die obere Grenze auf 80 sowie die Parallelkopie auf 4 festlegen, ruft der Dienst Daten nach 4 Partitionen ab – IDs im Bereich <=20, [21, 50], [51, 80] bzw. >=81. |
| Laden einer großen Datenmenge unter Verwendung einer benutzerdefinierten Abfrage ohne physische Partitionen, aber mit einer integer- oder date/datetime-Spalte für die Datenpartitionierung. | **Partitionsoptionen:** Dynamische Bereichspartitionierung<br>**Abfrage**: `SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br>**Partitionsspalte:** Geben Sie die Spalte für die Datenpartitionierung an.<br>**Obergrenze der Partition** und **Untergrenze der Partition** (optional): Geben Sie an, ob Sie den Partitionssprung bestimmen möchten. Dies dient nicht zum Filtern der Zeilen in der Tabelle; alle Zeilen im Abfrageergebnis werden partitioniert und kopiert. Wenn nicht angegeben, wird der Wert für die Kopieraktivität automatisch erkannt.<br><br>Während der Ausführung ersetzt der Dienst `?AdfRangePartitionColumnName` durch die tatsächlichen Spaltennamen und Wertebereiche für jede Partition und sendet sie an Amazon RDS for SQL Server. <br>Wenn Ihre Partitionsspalte „ID“ z. B. einen Wertebereich von 1 bis 100 aufweist und Sie die untere Grenze auf 20 und die obere Grenze auf 80 sowie die Parallelkopie auf 4 festlegen, ruft der Dienst Daten nach 4 Partitionen ab – IDs im Bereich <=20, [21, 50], [51, 80] bzw. >=81. <br><br>Hier finden Sie weitere Beispiele für verschiedene Szenarien:<br> 1. Abfrage der gesamten Tabelle: <br>`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition`<br> 2. Abfrage aus einer Tabelle mit Spaltenauswahl und zusätzlichen Where-Klausel-Filtern: <br>`SELECT <column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 3. Abfragen mit Unterabfragen: <br>`SELECT <column_list> FROM (<your_sub_query>) AS T WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 4. Abfrage mit Partition in Unterabfrage: <br>`SELECT <column_list> FROM (SELECT <your_sub_query_column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition) AS T`
|

Bewährte Methoden zum Laden von Daten mit Partitionierungsoption:

1. Wählen Sie eine aussagekräftige Spalte als Partitionsspalte (wie Primärschlüssel oder eindeutiger Schlüssel), um Datenabweichungen zu vermeiden. 
2. Wenn die Tabelle eine integrierte Partition aufweist, verwenden Sie die Partitionsoption „Physikalische Partitionen der Tabelle“, um eine bessere Leistung zu erzielen.    
3. Wenn Sie Azure Integration Runtime zum Kopieren von Daten verwenden, können Sie größere „[Data Integration Units (DIU)](copy-activity-performance-features.md#data-integration-units)“ festlegen (> 4), um mehr Computingressourcen zu nutzen. Prüfen Sie dort die anwendbaren Szenarien.
4. „[Grad der Kopierparallelität](copy-activity-performance-features.md#parallel-copy)“ steuert die Partitionsnummern. Ein zu großer Wert schadet manchmal der Leistung. Deshalb wird empfohlen, diesen Wert wie folgt festzulegen: (DIU oder Anzahl der selbstgehosteten IR-Knoten) × (2 bis 4).

**Beispiel: Vollständiges Laden aus einer großen Tabelle mit physischen Partitionen**

```json
"source": {
    "type": "AmazonRdsForSqlServerSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Beispiel: Abfrage mit dynamischer Bereichspartition**

```json
"source": {
    "type": "AmazonRdsForSqlServerSource",
    "query": "SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column (optional) to decide the partition stride, not as data filter>",
        "partitionLowerBound": "<lower_value_of_partition_column (optional) to decide the partition stride, not as data filter>"
    }
}
```

### <a name="sample-query-to-check-physical-partition"></a>Beispielabfrage zur Überprüfung der physischen Partition

```sql
SELECT DISTINCT s.name AS SchemaName, t.name AS TableName, pf.name AS PartitionFunctionName, c.name AS ColumnName, iif(pf.name is null, 'no', 'yes') AS HasPartition
FROM sys.tables AS t
LEFT JOIN sys.objects AS o ON t.object_id = o.object_id
LEFT JOIN sys.schemas AS s ON o.schema_id = s.schema_id
LEFT JOIN sys.indexes AS i ON t.object_id = i.object_id 
LEFT JOIN sys.index_columns AS ic ON ic.partition_ordinal > 0 AND ic.index_id = i.index_id AND ic.object_id = t.object_id 
LEFT JOIN sys.columns AS c ON c.object_id = ic.object_id AND c.column_id = ic.column_id 
LEFT JOIN sys.partition_schemes ps ON i.data_space_id = ps.data_space_id 
LEFT JOIN sys.partition_functions pf ON pf.function_id = ps.function_id 
WHERE s.name='[your schema]' AND t.name = '[your table name]'
```

Wenn die Tabelle eine physische Partition besitzt, würde „HasPartition“ wie folgt als „Yes“ (Ja) angezeigt werden.

:::image type="content" source="./media/connector-azure-sql-database/sql-query-result.png" alt-text="SQL-Abfrageergebnis":::

## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [Lookup-Aktivität](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Eigenschaften der GetMetadata-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [GetMetadata-Aktivität](control-flow-get-metadata-activity.md). 

## <a name="using-always-encrypted"></a>Verwenden von Always Encrypted

Führen Sie beim Kopieren von Daten aus bzw. nach Amazon RDS for SQL Server mit [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) die folgenden Schritte aus: 

1. Speichern Sie den [Spalten-Hauptschlüssel (Column Master Key, CMK)](/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted?view=sql-server-ver15&preserve-view=true) in einem [Azure Key Vault](../key-vault/general/overview.md). Weitere Informationen: [Konfigurieren von Always Encrypted mithilfe von Azure Key Vault](../azure-sql/database/always-encrypted-azure-key-vault-configure.md?tabs=azure-powershell)

2. Stellen Sie sicher, dass der Zugriff auf den Schlüsseltresor gewährt wird, in dem der [Spaltenhauptschlüssel (Column Master Key, CMK)](/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted?view=sql-server-ver15&preserve-view=true) gespeichert ist. Die erforderlichen Berechtigungen finden Sie in diesem [Artikel](/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted?view=sql-server-ver15&preserve-view=true#key-vaults).

3. Erstellen Sie einen verknüpften Dienst, um eine Verbindung mit der SQL-Datenbank herzustellen und aktivieren Sie die „Always Encrypted“-Funktion mithilfe einer verwalteten Identität oder eines Dienstprinzipals. 

## <a name="troubleshoot-connection-issues"></a>Beheben von Verbindungsproblemen

1. Konfigurieren Sie Ihre Amazon RDS for SQL Server-Instanz so, dass Remoteverbindungen zulässig sind. Starten Sie **Amazon RDS for SQL Server Management Studio**, klicken Sie mit der rechten Maustaste auf **Server**, und wählen Sie **Eigenschaften** aus. Wählen Sie in der Liste den Eintrag **Verbindungen** aus, und aktivieren Sie das Kontrollkästchen **Remoteverbindungen mit diesem Server zulassen**.

    :::image type="content" source="media/copy-data-to-from-sql-server/AllowRemoteConnections.png" alt-text="Aktivieren von Remoteverbindungen":::

    Ausführliche Schritte finden Sie unter [Konfigurieren der Serverkonfigurationsoption „Remotezugriff“](/sql/database-engine/configure-windows/configure-the-remote-access-server-configuration-option).

2. Starten Sie den **Amazon RDS for SQL Server-Konfigurations-Manager**. Erweitern Sie **Amazon RDS for SQL Server-Netzwerkkonfiguration** für die gewünschte Instanz, und wählen Sie **Protokolle für MSSQLSERVER** aus. Die Protokolle werden im rechten Bereich angezeigt. Aktivieren Sie TCP/IP, indem Sie mit der rechten Maustaste auf **TCP/IP** klicken und **Aktivieren** auswählen.

    :::image type="content" source="./media/copy-data-to-from-sql-server/EnableTCPProptocol.png" alt-text="Aktivieren von TCP/IP":::

    Weitere Informationen und alternative Methoden zum Aktivieren von TCP/IP finden Sie unter [Aktivieren oder Deaktivieren eines Servernetzwerkprotokolls](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol).

3. Doppelklicken Sie im gleichen Fenster auf **TCP/IP**, um das Fenster **TCP/IP-Eigenschaften** zu öffnen.
4. Wechseln Sie zur Registerkarte **IP-Adressen** . Scrollen Sie nach unten zum Abschnitt **IPAll**. Notieren Sie sich den **TCP-Port**. Der Standardport ist **1433**.
5. Erstellen Sie auf dem Computer eine **Regel für die Windows-Firewall** , um eingehenden Datenverkehr über diesen Port zuzulassen. 
6. **Verbindung überprüfen**: Verwenden Sie Amazon RDS for SQL Server Management Studio auf einem anderen Computer, um mit dem vollqualifizierten Namen eine Verbindung zu Amazon RDS for SQL Server herzustellen. z. B. `"<machine>.<domain>.corp.<company>.com,1433"`.

## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die als Quelles und Senken für die Kopieraktivität unterstützt werden, finden Sie in [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).
