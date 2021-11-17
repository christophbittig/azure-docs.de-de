---
title: Kopieren von Daten aus SAP HANA
titleSuffix: Azure Data Factory & Azure Synapse
description: Hier erfahren Sie, wie Daten aus SAP HANA mithilfe einer Copy-Aktivität in eine Azure Data Factory- oder Synapse Analytics-Pipeline in unterstützte Senkendatenspeicher kopiert werden.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: a773828f74205c982ebdbef7f74f98c93f2c167c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132327537"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory-or-synapse-analytics"></a>Kopieren von Daten aus SAP HANA mithilfe von Azure Data Factory oder Synapse Analytics
> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](v1/data-factory-sap-hana-connector.md)
> * [Aktuelle Version](connector-sap-hana.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie die Copy-Aktivität in Azure Data Factory- und Azure Synapse Analytics-Pipelines verwenden, um Daten aus einer SAP HANA-Datenbank zu kopieren. Er baut auf dem Artikel zur [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

>[!TIP]
>Informationen zur allgemeinen Unterstützung des SAP-Datenintegrationsszenarios finden Sie im [Whitepaper zur SAP-Datenintegration](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf). Dieses Whitepaper bietet eine detaillierte Einführung in die einzelnen SAP-Connectors, einen Vergleich und Leitfäden.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Dieser SAP HANA-Connector wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)

Sie können Daten aus einer SAP HANA-Datenbank in beliebige unterstützte Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die als Quellen oder Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

Dieser SAP HANA-Connector unterstützt insbesondere Folgendes:

- Kopieren von Daten von einer SAP HANA-Datenbank mit einer beliebigen Version
- Kopieren von Daten aus **HANA-Informationsmodellen** (z.B. Analyse- und Berechnungsansichten) und aus **Zeile/Spalte-Tabellen**.
- Kopieren von Dateien unter Verwendung der **Standard**- oder **Windows**-Authentifizierung
- Paralleles Kopieren von einer SAP HANA-Quelle. Weitere Informationen finden Sie im Abschnitt [Paralleles Kopieren von SAP HANA](#parallel-copy-from-sap-hana).

> [!TIP]
> Verwenden Sie zum Kopieren von Daten **in** SAP HANA-Datenspeicher einen generischen ODBC-Connector. Ausführliche Informationen hierzu finden Sie im Abschnitt [SAP HANA-Senke](#sap-hana-sink). Beachten Sie, dass die verknüpften Dienste für SAP HANA-Connectors und ODBC-Connectors unterschiedlichen Typs sind und daher nicht wiederverwendet werden können.

## <a name="prerequisites"></a>Voraussetzungen

Zum Verwenden dieses SAP HANA-Connectors müssen Sie folgende Schritte durchführen:

- Richten Sie eine selbstgehostete Integration Runtime ein. Im Artikel [Selbstgehostete Integration Runtime](create-self-hosted-integration-runtime.md) finden Sie Details.
- Installieren des SAP HANA-ODBC-Treibers auf dem Computer mit der Integrationslaufzeit. Sie können den SAP HANA-ODBC-Treiber aus dem [SAP Software Download Center](https://support.sap.com/swdc) herunterladen. Suche Sie mit dem Schlüsselwort **SAP HANA CLIENT for Windows**.

## <a name="getting-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-sap-hana-using-ui"></a>Erstellen eines verknüpften Diensts mit SAP HANA über die Benutzeroberfläche

Verwenden Sie die folgenden Schritte, um einen verknüpften Dienst mit SAP HANA auf der Azure-Portal-Benutzeroberfläche zu erstellen.

1. Navigieren Sie in Ihrem Azure Data Factory- oder Synapse-Arbeitsbereich zu der Registerkarte „Verwalten“, wählen Sie „Verknüpfte Dienste“ aus und klicken Sie dann auf „Neu“:

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Screenshot: Erstellen eines neuen verknüpften Diensts über die Azure Data Factory-Benutzeroberfläche":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Ein Screenshot, der das Erstellen eines neuen verknüpften Diensts mit der Azure Synapse Benutzeroberfläche zeigt.":::

2. Suchen Sie nach SAP, und wählen Sie die SAP HANA-Connector aus.

    :::image type="content" source="media/connector-sap-hana/sap-hana-connector.png" alt-text="Screenshot des SAP HANA Connectors.":::    

1. Konfigurieren Sie die Dienstdetails, testen Sie die Verbindung und erstellen Sie den neuen verknüpften Dienst.

    :::image type="content" source="media/connector-sap-hana/configure-sap-hana-linked-service.png" alt-text="Ein Screenshot, der die Konfiguration des verknüpften Diensts für SAP HANA zeigt.":::

## <a name="connector-configuration-details"></a>Details zur Connector-Konfiguration


Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für den SAP HANA-Connector verwendet werden:

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit SAP HANA verknüpften Dienst unterstützt:

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf Folgendes festgelegt werden: **SapHana** | Ja |
| connectionString | Geben Sie die Informationen an, die zum Herstellen einer Verbindung mit SAP HANA mithilfe der **Standardauthentifizierung** oder **Windows-Authentifizierung** benötigt werden. Sehen Sie sich die folgenden Beispiele an.<br>In der Verbindungszeichenfolge ist Server/Port obligatorisch (der Standardport ist 30015), und Benutzername und Kennwort sind bei der Verwendung von Standardauthentifizierung obligatorisch. Weitere erweiterte Einstellungen finden Sie unter [Eigenschaften der SAP Hana ODBC-Verbindung](https://help.sap.com/viewer/0eec0d68141541d1b07893a39944924e/2.0.02/en-US/7cab593774474f2f8db335710b2f5c50.html).<br/>Sie können das Kennwort auch in Azure Key Vault speichern und die Kennwortkonfiguration aus der Verbindungszeichenfolge pullen. Ausführlichere Informationen finden Sie im Artikel [Speichern von Anmeldeinformationen in Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| userName | Geben Sie den Benutzernamen an, wenn Sie Windows-Authentifizierung verwenden. Beispiel: `user@domain.com` | Nein |
| password | Geben Sie ein Kennwort für das Benutzerkonto an. Markieren Sie dieses Feld als einen „SecureString“, um es sicher zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Nein |
| connectVia | Die [Integrationslaufzeit](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden muss. Eine selbstgehostete Integrationslaufzeit ist erforderlich, wie unter [Voraussetzungen](#prerequisites) erwähnt wird. |Ja |

**Beispiel: Verwenden von Standardauthentifizierung**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;UID=<userName>;PWD=<Password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Beispiel: Verwenden von Windows-Authentifizierung**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;",
            "userName": "<username>", 
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

Wenn Sie den verknüpften SAP HANA-Dienst mit der folgenden Nutzlast verwendet haben, wird er weiterhin unverändert unterstützt. Es wird jedoch empfohlen, zukünftig die neue Version zu verwenden.

**Beispiel:**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server>:<port (optional)>",
            "authenticationType": "Basic",
            "userName": "<username>",
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

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die vom SAP HANA-Dataset unterstützt werden.

Beim Kopieren von Daten aus SAP HANA werden die folgenden Eigenschaften unterstützt.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft des Datasets muss auf folgenden Wert festgelegt werden: **SapHanaTable** | Ja |
| schema | Der Name des Schemas in der SAP HANA-Datenbank. | Nein (wenn „query“ in der Aktivitätsquelle angegeben ist) |
| table | Der Name der Tabelle in der SAP HANA-Datenbank. | Nein (wenn „query“ in der Aktivitätsquelle angegeben ist) |

**Beispiel:**

```json
{
    "name": "SAPHANADataset",
    "properties": {
        "type": "SapHanaTable",
        "typeProperties": {
            "schema": "<schema name>",
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Wenn Sie das Datenset vom Typ `RelationalTable` verwenden, wird es weiterhin unverändert unterstützt. Es wird jedoch empfohlen, zukünftig die neue Version zu verwenden.

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der SAP HANA-Quelle unterstützt werden.

### <a name="sap-hana-as-source"></a>SAP HANA als Quelle

>[!TIP]
>Weitere Informationen zum effizienten Erfassen von Daten von SAP HANA mithilfe der Datenpartitionierung finden Sie im Abschnitt [Paralleles Kopieren von SAP HANA](#parallel-copy-from-sap-hana).

Wenn Sie Daten aus SAP HANA kopieren möchten, werden die folgenden Eigenschaften im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf Folgendes festgelegt werden: **SapHanaSource** | Ja |
| Abfrage | Gibt die SQL-Abfrage an, mit der Daten aus der SAP HANA-Instanz gelesen werden. | Ja |
| partitionOptions | Gibt die Datenpartitionierungsoptionen an, mit denen Daten von SAP HANA erfasst werden. Weitere Informationen finden Sie im Abschnitt [Paralleles Kopieren von SAP HANA](#parallel-copy-from-sap-hana).<br>Zulässige Werte sind:  **None** (Standard),  **PhysicalPartitionsOfTable**, **SapHanaDynamicRange**. Weitere Informationen finden Sie im Abschnitt [Paralleles Kopieren von SAP HANA](#parallel-copy-from-sap-hana). `PhysicalPartitionsOfTable` kann nur beim Kopieren von Daten aus einer Tabelle, jedoch nicht bei Abfragen verwendet werden. <br>Wenn eine Partitionsoption aktiviert ist (nicht `None`), wird der Grad an Parallelität zum gleichzeitigen Laden von Daten von SAP HANA durch die Einstellung [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) für die Kopieraktivität gesteuert. | False |
| partitionSettings | Geben Sie die Gruppe der Einstellungen für die Datenpartitionierung an.<br>Verwenden Sie ihn, wenn die Partitionsoption `SapHanaDynamicRange` lautet. | False |
| partitionColumnName | Geben Sie den Namen der Quellspalte an, die von der Partitionierung für den parallelen Kopiervorgang verwendet wird. Ohne Angabe wird der Index oder der Primärschlüssel der Tabelle automatisch erkannt und als Partitionsspalte verwendet.<br>Verwenden Sie diese Option, wenn die Partitionsoption `SapHanaDynamicRange` lautet. Wenn Sie die Quelldaten mithilfe einer Abfrage abrufen, integrieren Sie  `?AdfHanaDynamicRangePartitionCondition` in die WHERE-Klausel. Ein Beispiel finden Sie im Abschnitt [Paralleles Kopieren von SAP HANA](#parallel-copy-from-sap-hana). | Ja, wenn die Partitionierung `SapHanaDynamicRange` verwendet wird. |
| packetSize | Gibt die Netzwerkpaketgröße (in KB) an, mit der Daten auf mehrere Blöcke aufgeteilt werden. Wenn Sie über eine große Datenmenge verfügen, die kopiert werden soll, kann das Erhöhen der Paketgröße in den meisten Fällen die Lesegeschwindigkeit aus SAP Hana erhöhen. Leistungstests werden empfohlen, wenn die Paketgröße angepasst wird. | Nein.<br>Der Standardwert ist 2.048 (2MB). |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromSAPHANA",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP HANA input dataset name>",
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
                "type": "SapHanaSource",
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Wenn Sie die Kopierquelle vom Typ `RelationalSource` verwenden, wird sie weiterhin unverändert unterstützt. Es wird jedoch empfohlen, zukünftig die neue Version zu verwenden.

## <a name="parallel-copy-from-sap-hana"></a>Paralleles Kopieren von SAP HANA

Der SAP HANA-Connector bietet eine integrierte Datenpartitionierung zum parallelen Kopieren von Daten von SAP HANA. Die Datenpartitionierungsoptionen befinden sich auf der Registerkarte **Quelle** der Kopieraktivität.

:::image type="content" source="./media/connector-sap-hana/connector-sap-hana-partition-options.png" alt-text="Screenshot der Partitionierungsoptionen":::

Wenn Sie partitioniertes Kopieren aktivieren, führt Data Factory parallele Abfragen für Ihre SAP HANA-Quelle aus, um Daten anhand von Partitionen abzurufen. Der Parallelitätsgrad wird über die Einstellung [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) der Kopieraktivität gesteuert. Wenn Sie z. B. `parallelCopies` auf vier (4) festlegen, generiert der Dienst gleichzeitig vier Abfragen auf der Grundlage der von Ihnen angegebenen Partitionsoption und -einstellungen und führt sie aus. Dabei ruft jede Abfrage einen Teil der Daten aus Ihrer SAP HANA-Instanz ab.

Es wird empfohlen, das parallele Kopieren mit Datenpartitionierung zu aktivieren. Das gilt insbesondere, wenn Sie große Datenmengen von Ihrer SAP HANA-Instanz erfassen. Im Anschluss finden Sie empfohlene Konfigurationen für verschiedene Szenarien. Beim Kopieren von Daten in einen dateibasierten Datenspeicher wird empfohlen, mehrere Dateien in einen Ordner zu schreiben (nur den Ordnernamen anzugeben). In diesem Fall ist die Leistung besser als beim Schreiben in eine einzelne Datei.

| Szenario                                           | Empfohlene Einstellungen                                           |
| -------------------------------------------------- | ------------------------------------------------------------ |
| Vollständiges Laden aus großer Tabelle                        | **Partitionsoption:** Physische Partitionen der Tabelle. <br><br/>Bei der Ausführung erkennt der Dienst automatisch den physischen Partitionstyp der angegebenen SAP HANA-Tabelle und wählt die entsprechende Partitionierungsstrategie aus:<br>- **Bereichspartitionierung:** Ruft die für die Tabelle definierte Partitionsspalte und die Partitionsbereiche ab und kopiert dann die Daten nach Bereich. <br>- **Hashpartitionierung**: Verwenden Sie den Hashpartitionsschlüssel als Partitionsspalte. Danach partitionieren und kopieren Sie die Daten auf der Grundlage der vom Dienst berechneten Bereiche. <br>- **Roundrobin-Partitionierung** oder **Keine Partitionierung**: Verwenden Sie den Primärschlüssel als Partitionsspalt. Danach partitionieren und kopieren Sie die Daten auf der Grundlage der vom Dienst berechneten Bereiche. |
| Laden einer großen Datenmenge unter Verwendung einer benutzerdefinierten Abfrage | **Partitionsoption:** Dynamische Bereichspartitionierung<br>**Abfrage**: `SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>`<br>**Partitionsspalte:** Gibt die Spalte an, die zum Anwenden der dynamischen Bereichspartitionierung verwendet wird. <br><br>Bei der Ausführung berechnet der Dienst zuerst die Wertebereiche der angegebenen Partitionsspalte, indem die Zeilen in mehreren Buckets entsprechend der Anzahl der unterschiedlichen Partitionsspaltenwerte und der Einstellung für paralleles Kopieren gleichmäßig verteilt werden. Anschließend wird `?AdfHanaDynamicRangePartitionCondition` ersetzt, indem der Wertebereich der Partitionsspalte für jede Partition gefiltert wird, und das Ergebnis wird an SAP HANA gesendet.<br><br>Wenn Sie mehrere Spalten als Partitionsspalten verwenden möchten, können Sie die Werte jeder Spalte in der Abfrage in einer Spalte verketten und als Partitionsspalte angeben, z. B. `SELECT * FROM (SELECT *, CONCAT(<KeyColumn1>, <KeyColumn2>) AS PARTITIONCOLUMN FROM <TABLENAME>) WHERE ?AdfHanaDynamicRangePartitionCondition`. |

**Beispiel: Abfrage mit physischen Partitionen einer Tabelle**

```json
"source": {
    "type": "SapHanaSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Beispiel: Abfrage mit dynamischer Bereichspartition**

```json
"source": {
    "type": "SapHanaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "SapHanaDynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<Partition_column_name>"
    }
}
```

## <a name="data-type-mapping-for-sap-hana"></a>Datentypzuordnung für SAP HANA

Beim Kopieren von Daten aus SAP HANA werden die folgenden Zuordnungen von SAP HANA-Datentypen zu den vom Dienst intern verwendeten Zwischendatentypen verwendet. Unter [Schema- und Datentypzuordnungen](copy-activity-schema-and-type-mapping.md) erfahren Sie, wie Sie Aktivitätszuordnungen für Quellschema und Datentyp in die Senke kopieren.

| SAP HANA-Datentyp | Zwischendatentyp des Diensts |
| ------------------ | ------------------------------ |
| ALPHANUM           | String                         |
| bigint             | Int64                          |
| BINARY             | Byte[]                         |
| BINTEXT            | String                         |
| BLOB               | Byte[]                         |
| BOOL               | Byte                           |
| CLOB               | String                         |
| DATE               | Datetime                       |
| DECIMAL            | Decimal                        |
| Double             | Double                         |
| GLEITKOMMAZAHL              | Double                         |
| INTEGER            | Int32                          |
| NCLOB              | String                         |
| NVARCHAR           | String                         |
| real               | Single                         |
| SECONDDATE         | Datetime                       |
| SHORTTEXT          | String                         |
| SMALLDECIMAL       | Decimal                        |
| SMALLINT           | Int16                          |
| STGEOMETRYTYPE     | Byte[]                         |
| STPOINTTYPE        | Byte[]                         |
| TEXT               | String                         |
| TIME               | TimeSpan                       |
| TINYINT            | Byte                           |
| VARCHAR            | String                         |
| timestamp          | Datetime                       |
| VARBINARY          | Byte[]                         |

## <a name="sap-hana-sink"></a>SAP HANA-Senke

Der SAP HANA-Connector wird zurzeit nicht als Senke unterstützt, aber Sie können den generischen ODBC-Connector mit dem SAP HANA-Treiber verwenden, um Daten in SAP HANA zu schreiben. 

Beachten Sie die [Voraussetzungen](#prerequisites), um zuerst die selbstgehostete Integration Runtime einzurichten und den SAP HANA ODBC-Treiber zu installieren. Erstellen Sie einen verknüpften ODBC-Dienst, um eine Verbindung mit Ihrem SAP HANA-Datenspeicher herzustellen, wie im folgenden Beispiel gezeigt wird. Erstellen Sie dann entsprechend die Senke für die DataSet- und Kopieraktivität mit dem ODBC-Typ. Weitere Informationen finden Sie im Artikel [ODBC-Connector](connector-odbc.md).

```json
{
    "name": "SAPHANAViaODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": "Driver={HDBODBC};servernode=<HANA server>.clouddatahub-int.net:30015",
            "authenticationType": "Basic",
            "userName": "<username>",
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

## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [Lookup-Aktivität](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die als Quelles und Senken für die Kopieraktivität unterstützt werden, finden Sie in der Dokumentation für [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).
