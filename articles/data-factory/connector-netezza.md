---
title: Kopieren von Daten aus Netezza
description: Erfahren Sie, wie Daten aus Netezza mithilfe einer Copy-Aktivität in einer Azure Data Factory- oder Synapse Analytics-Pipeline in unterstützte Senkendatenspeicher kopiert werden.
titleSuffix: Azure Data Factory & Azure Synapse
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: jianleishen
ms.openlocfilehash: 588700126d87361e4530c073cdcaf1a5a9339511
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124828161"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory-or-synapse-analytics"></a>Kopieren von Daten aus Netezza mithilfe von Azure Data Factory oder Synapse Analytics
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie die Copy-Aktivität in Azure Data Factory- oder Azure Synapse Analytics-Pipelines verwenden, um Daten aus Netezza zu kopieren. Dieser Artikel baut auf dem Artikel zur [Kopieraktivität](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

>[!TIP]
>Ein Szenario für die Datenmigration von Netezza zu Azure finden Sie im Artikel zum [Migrieren von Daten von einem lokalen Netezza-Server zu Azure](data-migration-guidance-netezza-azure-sqldw.md).

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Dieser Netezza-Connector wird für folgende Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)


Sie können Daten aus Netezza in beliebige unterstützte Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die die Kopieraktivität als Quellen und Senken unterstützt, finden Sie unter [Unterstützte Datenspeicher und Formate](copy-activity-overview.md#supported-data-stores-and-formats).

Der Netezza-Connector unterstützt das parallele Kopieren aus einer Quelle. Weitere Informationen finden Sie im Abschnitt [Paralleles Kopieren aus Netezza](#parallel-copy-from-netezza).

Der Dienst enthält einen integrierten Treiber zum Herstellen der Konnektivität. Sie müssen Treiber für die Verwendung dieses Connectors nicht manuell installieren.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Erste Schritte

Sie können mit dem .NET SDK, Python SDK, Azure PowerShell, der REST-API oder einer Azure Resource Manager-Vorlage eine Pipeline erstellen, die eine Kopieraktivität verwendet. Eine ausführliche Anleitung zum Erstellen einer Pipeline mit einer Kopieraktivität finden Sie im [Tutorial zur Kopieraktivität](quickstart-create-data-factory-dot-net.md).

## <a name="create-a-linked-service-to-netezza-using-ui"></a>Erstellen eines mit Netezza verknüpften Diensts über die Benutzeroberfläche

Führen Sie die folgenden Schritte aus, um einen mit Netezza verknüpften Dienst über die Benutzeroberfläche des Azure-Portals zu erstellen.

1. Navigieren Sie in Ihrem Azure Data Factory- oder Synapse-Arbeitsbereich zu der Registerkarte „Verwalten“. Wählen Sie die Option „Verknüpfte Dienste“ aus, und klicken Sie anschließend auf „Neu“:

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Screenshot: Erstellen eines neuen verknüpften Diensts über die Azure Data Factory-Benutzeroberfläche":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Screenshot: Erstellen eines neuen verknüpften Diensts über die Azure Synapse-Benutzeroberfläche":::

2. Suchen Sie nach Netezza, und wählen Sie den Netezza-Connector aus.

   :::image type="content" source="media/connector-netezza/netezza-connector.png" alt-text="Screenshot des Netezza-Connectors":::    


1. Konfigurieren Sie die Dienstdetails, testen Sie die Verbindung, und erstellen Sie den neuen verknüpften Dienst.

   :::image type="content" source="media/connector-netezza/configure-netezza-linked-service.png" alt-text="Screenshot der Konfiguration des mit Netezza verknüpften Diensts":::

## <a name="connector-configuration-details"></a>Details zur Connectorkonfiguration

In den folgenden Abschnitten finden Sie Details zu den Eigenschaften, mit denen Sie die Entitäten definieren können, die spezifisch für den Netezza-Connector sind.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit Netezza verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft muss auf **Netezza** festgelegt werden. | Ja |
| connectionString | Eine ODBC-Verbindungszeichenfolge zum Herstellen einer Verbindung mit Netezza. <br/>Sie können auch das Kennwort in Azure Key Vault speichern und die `pwd`-Konfiguration aus der Verbindungszeichenfolge pullen. Ausführlichere Informationen finden Sie in den folgenden Beispielen und im Artikel [Speichern von Anmeldeinformationen in Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Weitere Informationen finden Sie im Abschnitt [Voraussetzungen](#prerequisites). Wenn keine Option angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. |Nein |

Eine typische Verbindungszeichenfolge ist `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`. Die folgende Tabelle beschreibt weitere Eigenschaften, die Sie festlegen können:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| SecurityLevel | Der Sicherheitsgrad, den der Treiber für die Verbindung mit dem Datenspeicher verwendet. Der Treiber unterstützt SSL-Verbindungen mit unidirektionaler Authentifizierung unter Verwendung der SSL-Version 3. <br>Beispiel: `SecurityLevel=preferredSecured`. Diese Werte werden unterstützt:<br/>- **Nur ungesichert** (**onlyUnSecured**): Der Treiber verwendet SSL nicht.<br/>- **Bevorzugt ungesichert (preferredUnSecured) (Standard)** : Wenn der Server die Wahl zulässt, verwendet der Treiber SSL nicht. <br/>- **Bevorzugt gesichert (preferredSecured)** : Wenn der Server die Wahl zulässt, verwendet der Treiber SSL. <br/>- **Nur gesichert (onlySecured)** : Der Treiber stellt nur dann eine Verbindung her, wenn eine SSL-Verbindung verfügbar ist. | Nein |
| CaCertFile | Der vollständige Pfad zum vom Server verwendeten SSL-Zertifikat. Beispiel: `CaCertFile=<cert path>;`| Ja, wenn SSL aktiviert ist |

**Beispiel**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
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
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;",
            "pwd": { 
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

Dieser Abschnitt enthält eine Liste der Eigenschaften, die das Netezza-Dataset unterstützt.

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie unter [Datasets](concepts-datasets-linked-services.md).

Legen Sie zum Kopieren von Daten aus Netezza die **type**-Eigenschaft des Datasets auf **NetezzaTable** fest. Folgende Eigenschaften werden unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft des Datasets muss auf folgenden Wert festgelegt werden: **NetezzaTable** | Ja |
| schema | Name des Schemas. |Nein (wenn „query“ in der Aktivitätsquelle angegeben ist)  |
| table | Der Name der Tabelle. |Nein (wenn „query“ in der Aktivitätsquelle angegeben ist)  |
| tableName | Name der Tabelle mit Schema. Diese Eigenschaft wird aus Gründen der Abwärtskompatibilität weiterhin unterstützt. Verwenden Sie `schema` und `table` für eine neue Workload. | Nein (wenn „query“ in der Aktivitätsquelle angegeben ist) |

**Beispiel**

```json
{
    "name": "NetezzaDataset",
    "properties": {
        "type": "NetezzaTable",
        "linkedServiceName": {
            "referenceName": "<Netezza linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Dieser Abschnitt enthält eine Liste der Eigenschaften, die die Netezza-Quelle unterstützt.

Eine vollständige Liste mit den verfügbaren Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie unter [Pipelines](concepts-pipelines-activities.md).

### <a name="netezza-as-source"></a>Netezza als Quelle

>[!TIP]
>Weitere Informationen zum effizienten Laden von Daten aus Netezza mithilfe der Datenpartitionierung finden Sie unter [Paralleles Kopieren aus Netezza](#parallel-copy-from-netezza).

Legen Sie zum Kopieren von Daten aus Netezza den **Quelltyp** in der Kopieraktivität auf **NetezzaSource** fest. Die folgenden Eigenschaften werden im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft der Quelle der Kopieraktivität muss auf **NetezzaSource** festgelegt werden. | Ja |
| Abfrage | Verwendet die benutzerdefinierte SQL-Abfrage zum Lesen von Daten. Beispiel: `"SELECT * FROM MyTable"` | Nein (wenn „tableName“ im Dataset angegeben ist) |
| partitionOptions | Gibt die Datenpartitionierungsoptionen an, mit denen Daten aus Netezza geladen werden. <br>Zulässige Werte sind: **None** (Standardwert), **DataSlice** und **DynamicRange**.<br>Wenn eine Partitionsoption aktiviert ist (d.h. nicht `None`), wird der Grad an Parallelität zum gleichzeitigen Laden von Daten aus einer Netezza-Datenbank durch die Einstellung [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) für die Kopieraktivität gesteuert. | Nein |
| partitionSettings | Geben Sie die Gruppe der Einstellungen für die Datenpartitionierung an. <br>Verwenden Sie diese Option, wenn die Partitionsoption nicht `None` lautet. | Nein |
| partitionColumnName | Geben Sie den Namen der Quellspalte als **Integer** an, der von der Bereichspartitionierung für den parallelen Kopiervorgang verwendet wird. Ohne Angabe wird der Primärschlüssel der Tabelle automatisch erkannt und als Partitionsspalte verwendet. <br>Verwenden Sie diese Option, wenn die Partitionsoption `DynamicRange` lautet. Wenn Sie die Quelldaten mithilfe einer Abfrage abrufen, integrieren Sie `?AdfRangePartitionColumnName` in die WHERE-Klausel. Ein Beispiel finden Sie im Abschnitt [Paralleles Kopieren aus Netezza](#parallel-copy-from-netezza). | Nein |
| partitionUpperBound | Der Höchstwert der Partitionsspalte zum Herauskopieren von Daten. <br>Verwenden Sie ihn, wenn die Partitionsoption `DynamicRange` lautet. Wenn Sie Quelldaten per Abfrage abrufen, integrieren Sie `?AdfRangePartitionUpbound` in die WHERE-Klausel. Ein Beispiel finden Sie im Abschnitt [Paralleles Kopieren aus Netezza](#parallel-copy-from-netezza). | Nein |
| partitionLowerBound | Der Mindestwert der Partitionsspalte zum Herauskopieren von Daten. <br>Verwenden Sie diese Option, wenn die Partitionsoption `DynamicRange` lautet. Wenn Sie die Quelldaten mithilfe einer Abfrage abrufen, integrieren Sie `?AdfRangePartitionLowbound` in die WHERE-Klausel. Ein Beispiel finden Sie im Abschnitt [Paralleles Kopieren aus Netezza](#parallel-copy-from-netezza). | Nein |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromNetezza",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Netezza input dataset name>",
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
                "type": "NetezzaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-netezza"></a>Paralleles Kopieren aus Netezza

Der Data Factory-Netezza-Connector verfügt über eine integrierte Datenpartitionierung zum parallelen Kopieren von Daten aus Netezza. Die Datenpartitionierungsoptionen befinden sich auf der Registerkarte **Quelle** der Kopieraktivität.

:::image type="content" source="./media/connector-netezza/connector-netezza-partition-options.png" alt-text="Screenshot der Partitionierungsoptionen":::

Wenn Sie partitioniertes Kopieren aktivieren, führt der Dienst parallele Abfragen an die Netezza-Quelle aus, um Daten partitionsweise zu laden. Der Parallelitätsgrad wird über die Einstellung [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) der Kopieraktivität gesteuert. Wenn Sie z. B. `parallelCopies` auf vier einstellen, generiert der Dienst gleichzeitig vier Abfragen auf der Grundlage der von Ihnen angegebenen Partitionsoption und -einstellungen und führt sie aus, wobei jede Abfrage einen Teil der Daten aus Ihrer Netezza-Datenbank abruft.

Es wird empfohlen, das parallele Kopieren mit Datenpartitionierung zu aktivieren, vor allem, wenn Sie große Datenmengen aus Ihrer Netezza-Datenbank laden. Im Anschluss finden Sie empfohlene Konfigurationen für verschiedene Szenarien. Beim Kopieren von Daten in einen dateibasierten Datenspeicher wird empfohlen, mehrere Dateien in einen Ordner zu schreiben. (Geben Sie nur den Ordnernamen an.) In diesem Fall ist die Leistung besser als beim Schreiben in eine einzelne Datei.

| Szenario                                                     | Empfohlene Einstellungen                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Vollständiges Laden aus großer Tabelle                                   | **Partitionsoption:** Datenslice <br><br/>Der Dienst partitioniert während der Ausführung automatisch die Daten basierend auf den [integrierten Datenslices von Netezza](https://www.ibm.com/support/knowledgecenter/en/SSULQD_7.2.1/com.ibm.nz.adm.doc/c_sysadm_data_slices_parts_disks.html) und kopiert die Daten partitionsweise. |
| Laden einer großen Datenmenge unter Verwendung einer benutzerdefinierten Abfrage                 | **Partitionsoption:** Datenslice<br>**Abfrage**: `SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>`<br>Der Dienst ersetzt `?AdfPartitionCount` (bei festgelegter Anzahl paralleler Kopien für Copy-Aktivität) und `?AdfDataSliceCondition` während der Ausführung durch die Datenslice-Partitionierungslogik und sendet die Daten dann an Netezza. |
| Laden einer großen Datenmenge unter Verwendung einer benutzerdefinierten Abfrage, wenn eine Integerspalte mit gleichmäßig verteilten Werten für die Bereichspartitionierung vorhanden ist | **Partitionsoptionen:** Dynamische Bereichspartitionierung<br>**Abfrage**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`<br>**Partitionsspalte:** Geben Sie die Spalte für die Datenpartitionierung an. Die Partitionierung kann auf der Grundlage der Spalte mit dem Datentyp „Integer“ erfolgen.<br>**Obergrenze der Partition** und **Untergrenze der Partition**: Geben Sie an, ob Sie anhand der Partitionsspalte filtern möchten, um nur Daten zwischen der Ober- und der Untergrenze zu erhalten.<br><br>Der Dienst `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound` und `?AdfRangePartitionLowbound` während der Ausführung durch die tatsächlichen Spaltennamen und Wertebereiche für jede Partition und sendet sie an Netezza. <br>Wenn z. B. für die Partitionsspalte „ID“ die untere Grenze auf 1 und die obere Grenze auf 80 festgelegt ist und die Parallelkopie auf 4 eingestellt ist, ruft der Dienst Daten für jeweils 4 Partitionen ab. Die ID-Bereiche sehen dann wie folgt aus: [1–20], [21–40], [41–60] und [61–80]. |

**Beispiel: Abfrage mit Datenslicepartition**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>",
    "partitionOption": "DataSlice"
}
```

**Beispiel: Abfrage mit dynamischer Bereichspartition**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [Lookup-Aktivität](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Nächste Schritte

Eine Liste der Datenspeicher, die die Kopieraktivität als Quellen und Senken unterstützt, finden Sie unter [Unterstützte Datenspeicher und Formate](copy-activity-overview.md#supported-data-stores-and-formats).
