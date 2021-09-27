---
title: Push-Übertragung von Daten in einen Search-Index mithilfe von Data Factory
description: Erfahren Sie mehr über die Push-Übertragung von Daten in einen Azure Cognitive Search-Index mithilfe von Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 1ab7ed3435cd49e6671e5a05623db964ce551e86
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128676844"
---
# <a name="push-data-to-an-azure-cognitive-search-index-by-using-azure-data-factory"></a>Push-Übertragung von Daten in einen Azure Cognitive Search-Index mithilfe von Azure Data Factory
> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](data-factory-azure-search-connector.md)
> * [Version 2 (aktuelle Version)](../connector-azure-search.md)

> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Bei Verwendung der aktuellen Version des Data Factory-Diensts finden Sie weitere Informationen unter [Azure Cognitive Search-Connector in V2](../connector-azure-search.md).

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität verwenden können, um Daten aus einem unterstützten Quelldatenspeicher per Push in einen Azure Cognitive Search-Index zu übertragen. In der Spalte „Quelle“ der Tabelle [Unterstützte Datenquellen und Senken](data-factory-data-movement-activities.md#supported-data-stores-and-formats) sind unterstützte Quelldatenspeicher aufgelistet. Dieser Artikel baut auf dem Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) auf, der eine allgemeine Übersicht zur Datenverschiebung mit Kopieraktivität und unterstützten Datenspeicherkombinationen bietet.

## <a name="enabling-connectivity"></a>Herstellen der Verbindung
Installieren Sie das Datenverwaltungsgateway in Ihrer lokalen Umgebung, um die Verbindung des Data Factory-Diensts mit einem lokalen Datenspeicher zuzulassen. Sie können das Gateway auf dem Computer, der den Quelldatenspeicher hostet oder auf einem separaten Computer installieren, um zu vermeiden, dass der Computer mit dem Datenspeicher um Ressourcen konkurriert.

Das Datenverwaltungsgateway verbindet die lokalen Datenquellen mit Cloud-Diensten auf sichere und verwaltete Weise. Weitere Informationen zum Datenverwaltungsgateway finden Sie im Artikel [Verschieben von Daten zwischen lokalen Quellen und der Cloud](data-factory-move-data-between-onprem-and-cloud.md) .

## <a name="getting-started"></a>Erste Schritte
Sie können eine Pipeline mit einer Kopieraktivität erstellen, die Daten mithilfe verschiedener Tools/APIs aus einem Quelldatenspeicher per Push in einen Suchindex überträgt.

Am einfachsten erstellen Sie eine Pipeline mit dem **Kopier-Assistenten**. Siehe [Tutorial: Erstellen einer Pipeline mit dem Kopier-Assistenten](data-factory-copy-data-wizard-tutorial.md) finden Sie eine kurze exemplarische Vorgehensweise zum Erstellen einer Pipeline mithilfe des Assistenten zum Kopieren von Daten.

Sie können auch die folgenden Tools zum Erstellen einer Pipeline verwenden: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-Vorlage**, **.NET-API** und **REST-API**. Im [Tutorial zur Kopieraktivität](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) finden Sie detaillierte Anweisungen, wie Sie eine Pipeline mit einer Kopieraktivität erstellen können.

Unabhängig davon, ob Sie Tools oder APIs verwenden, führen Sie die folgenden Schritte aus, um eine Pipeline zu erstellen, die Daten aus einem Quelldatenspeicher in einen Senkendatenspeicher verschiebt:

1. Erstellen **verknüpfter Dienste** zum Verknüpfen von Eingabe- und Ausgabedatenspeichern mit Ihrer Data Factory.
2. Erstellen von **Datasets** zur Darstellung von Eingabe- und Ausgabedaten für den Kopiervorgang.
3. Erstellen einer **Pipeline** mit einer Kopieraktivität, die ein Dataset als Eingabe und ein Dataset als Ausgabe akzeptiert.

Wenn Sie den Assistenten verwenden, werden automatisch JSON-Definitionen für diese Data Factory-Entitäten (verknüpfte Diensten, Datasets und die Pipeline) erstellt. Bei Verwendung von Tools und APIs (mit Ausnahme der .NET-API) definieren Sie diese Data Factory-Entitäten im JSON-Format.  Ein Beispiel mit JSON-Definitionen für Data Factory-Entitäten, die zum Kopieren von Daten in einen Suchindex verwendet werden, finden Sie im Abschnitt [JSON-Beispiel: Kopieren von Daten aus SQL Server in einen Azure Cognitive Search-Index](#json-example-copy-data-from-sql-server-to-azure-cognitive-search-index) in diesem Artikel.

Die folgenden Abschnitte enthalten Details zu JSON-Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für einen Suchindex verwendet werden:

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Die folgende Tabelle enthält Beschreibungen der JSON-Elemente, die für den mit Azure Cognitive Search verknüpften Dienst spezifisch sind.

| Eigenschaft | Beschreibung | Erforderlich |
| -------- | ----------- | -------- |
| type | Die type-Eigenschaft muss auf Folgendes festgelegt werden: **AzureSearch**. | Ja |
| url | URL für den Suchdienst | Ja |
| Schlüssel | Administratorschlüssel für den Suchdienst | Ja |

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel [Erstellen von Datasets](data-factory-create-datasets.md) . Abschnitte wie „structure“, „availability“ und „policy“ des JSON-Codes eines Datasets sind bei allen Dataset-Typen ähnlich. Der Abschnitt **typeProperties** ist bei jeder Art von Dataset unterschiedlich. Der Abschnitt „typeProperties“ für ein Dataset vom Typ **AzureSearchIndex** hat die folgenden Eigenschaften:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| -------- | ----------- | -------- |
| type | Die Typeigenschaft muss auf **AzureSearchIndex** eingestellt sein.| Ja |
| indexName | Name des Suchindex. Data Factory erstellt den Index nicht. Der Index muss in Azure Cognitive Search vorhanden sein. | Ja |


## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität
Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Erstellen von Pipelines](data-factory-create-pipelines.md) . Eigenschaften wie Name, Beschreibung, Eingabe- und Ausgabetabellen und zahlreiche Richtlinien sind für alle Arten von Aktivitäten verfügbar. Eigenschaften im Abschnitt typeProperties hingegen können je nach Aktivitätstyp variieren. Für die Kopieraktivität variieren die Eigenschaften je nach Art der Quellen und Senken.

Wenn bei der Kopieraktivität die Senke den Typ **AzureSearchIndexSink** aufweist, sind im Abschnitt typeProperties die folgenden Eigenschaften verfügbar:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Gibt an, ob ein Dokument zusammengeführt oder ersetzt werden soll, wenn es bereits im Index vorhanden ist. Siehe [Eigenschaft „WriteBehavior“](#writebehavior-property).| Zusammenführen (Standard)<br/>Upload| Nein |
| WriteBatchSize | Lädt Daten in den Suchindex hoch, wenn die Puffergröße writeBatchSize erreicht. Einzelheiten finden Sie unter [Eigenschaft „WriteBatchSize“](#writebatchsize-property). | 1 bis 1.000. Der Standardwert ist 1000. | Nein |

### <a name="writebehavior-property"></a>Eigenschaft „WriteBehavior“
AzureSearchSink fügt Daten ein/aktualisiert beim Schreiben von Daten. Dies bedeutet, dass Azure Cognitive Search beim Schreiben eines Dokuments das bestehende Dokument aktualisiert, anstatt eine Konfliktausnahme auszulösen, wenn der Dokumentenschlüssel bereits im Suchindex vorhanden ist.

AzureSearchSink bietet die folgenden zwei Verhalten zum Einfügen/Aktualisieren (mithilfe des Azure Search SDK):

- **Merge** (Zusammenführen): kombiniert alle Spalten im neuen Dokument mit dem bestehenden. Bei Spalten mit Null-Wert im neuen Dokument wird der Wert im bestehenden Dokument beibehalten.
- **Upload** (Hochladen): Das neue Dokument ersetzt das bestehende. Bei Spalten, die nicht im neuen Dokument angegeben werden, wird der Wert auf Null gesetzt, unabhängig davon, ob ein Null-Wert im bestehenden Dokument vorhanden ist oder nicht.

Das Standardverhalten ist **Merge**.

### <a name="writebatchsize-property"></a>Eigenschaft „writeBatchSize“
Der Azure Cognitive Search-Dienst unterstützt das Schreiben von Dokumenten als Batch. Ein Batch kann 1 bis 1.000 Aktionen enthalten. Eine Aktion bearbeitet ein Dokument, um den Vorgang upload/merge auszuführen.

### <a name="data-type-support"></a>Datentypunterstützung
In der folgenden Tabelle wird angegeben, ob ein Azure Cognitive Search-Datentyp unterstützt wird oder nicht.

| Azure Cognitive Search-Datentyp | In Azure Cognitive Search-Senke unterstützt |
| ---------------------- | ------------------------------ |
| String | J |
| Int32 | J |
| Int64 | J |
| Double | J |
| Boolean | J |
| DataTimeOffset | J |
| String Array | N |
| GeographyPoint | N |

## <a name="json-example-copy-data-from-sql-server-to-azure-cognitive-search-index"></a>JSON-Beispiel: Kopieren von Daten aus SQL Server in einen Azure Cognitive Search-Index

Dieses Beispiel zeigt Folgendes:

1. Einen verknüpften Dienst des Typs [AzureSearch](#linked-service-properties).
2. Einen verknüpften Dienst des Typs [OnPremisesSqlServer](data-factory-sqlserver-connector.md#linked-service-properties)
3. Ein [Eingabedataset](data-factory-create-datasets.md) des Typs [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
4. Ein [Ausgabedataset](data-factory-create-datasets.md) des Typs [AzureSearchIndex](#dataset-properties).
4. Eine [Pipeline](data-factory-create-pipelines.md) mit Kopieraktivität, die [SqlSource](data-factory-sqlserver-connector.md#copy-activity-properties) und [AzureSearchIndexSink](#copy-activity-properties) verwendet.

In diesem Beispiel werden Zeitreihendaten aus einer SQL Server-Datenbank stündlich in einen Suchindex kopiert. Die in diesem Beispiel verwendeten JSON-Eigenschaften werden in den Abschnitten beschrieben, die auf die Beispiele folgen.

Als Erstes richten Sie das Datenverwaltungsgateway auf Ihrem lokalen Computer ein. Anweisungen dazu finden Sie im Artikel [Verschieben von Daten zwischen lokalen Standorten und Cloud](data-factory-move-data-between-onprem-and-cloud.md) .

**Mit Azure Cognitive Search verknüpften Dienst:**

```JSON
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": "<AdminKey>"
        }
    }
}
```

**Mit SQL Server verknüpfter Dienst**

```JSON
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```

**SQL Server-Eingabedataset**

Im Beispiel wird davon ausgegangen, dass Sie eine Tabelle namens „MyTable“ in SQL Server erstellt haben und dass sie eine Spalte namens „timestampcolumn“ für Zeitreihendaten enthält. Sie können mehrere Tabellen in derselben Datenbank mithilfe eines einzigen Datasets abfragen, aber für die typeProperty-Eigenschaft tableName des Datasets muss eine einzelne Tabelle verwendet werden.

Durch Festlegen von „external“ auf „true“ wird dem Data Factory-Dienst mitgeteilt, dass das Dataset für die Data Factory extern ist und nicht durch eine Aktivität in der Data Factory erzeugt wird.

```JSON
{
  "name": "SqlServerDataset",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Azure Cognitive Search-Ausgabedataset:**

In diesem Beispiel werden Daten in einen Azure Cognitive Search-Index namens **Produkte** kopiert. Data Factory erstellt den Index nicht. Um das Beispiel zu testen, erstellen Sie einen Index mit diesem Namen. Erstellen Sie den Suchindex mit der gleichen Anzahl Spalten wie im Eingabedataset. Neue Einträge werden stündlich zum Suchindex hinzugefügt.

```JSON
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
        "typeProperties" : {
            "indexName": "products",
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
    }
}
```

**Kopieraktivität in einer Pipeline mit SQL-Quelle und dem Azure Cognitive Search-Index als Senke:**

Die Pipeline enthält eine Kopieraktivität, die für die Verwendung der Ein- und Ausgabedatasets und für eine stündliche Ausführung konfiguriert ist. In der JSON-Definition der Pipeline ist der Typ **source** auf **SqlSource** und der Typ **sink** auf **AzureSearchIndexSink** festgelegt. Die für die **SqlReaderQuery** -Eigenschaft angegebene SQL-Abfrage wählt die Daten der letzten Stunde zum Kopieren aus.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "SqlServertoAzureSearchIndex",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": " SqlServerInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSearchIndexDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "AzureSearchIndexSink"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```

Wenn Sie Daten aus einem Clouddatenspeicher in Azure Cognitive Search kopieren, ist die `executionLocation`-Eigenschaft erforderlich. Im folgenden JSON-Codeausschnitt wird als Beispiel die Änderung gezeigt, die unter der Kopieraktivität `typeProperties` erforderlich ist. Im Abschnitt [Kopieren von Daten zwischen Clouddatenspeichern](data-factory-data-movement-activities.md#global) finden Sie Informationen zu den unterstützten Werten sowie weitere Details.

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```


## <a name="copy-from-a-cloud-source"></a>Kopieren aus einer Clouddatenquelle
Wenn Sie Daten aus einem Clouddatenspeicher in Azure Cognitive Search kopieren, ist die `executionLocation`-Eigenschaft erforderlich. Im folgenden JSON-Codeausschnitt wird als Beispiel die Änderung gezeigt, die unter der Kopieraktivität `typeProperties` erforderlich ist. Im Abschnitt [Kopieren von Daten zwischen Clouddatenspeichern](data-factory-data-movement-activities.md#global) finden Sie Informationen zu den unterstützten Werten sowie weitere Details.

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```

Sie können in der Definition der Kopieraktivität auch Spalten aus dem Quelldataset Spalten im Senkendataset zuordnen. Weitere Informationen finden Sie unter [Zuordnen von Datasetspalten in Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Leistung und Optimierung
Im [Handbuch zur Leistung und Optimierung der Kopieraktivität](data-factory-copy-activity-performance.md) werden wichtige Faktoren beschrieben, die sich auf die Leistung der Datenverschiebung (Kopieraktivität) auswirken, sowie verschiedene Möglichkeiten zur Leistungsoptimierung.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in folgenden Artikeln:

* [Kopieraktivität-Tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) für schrittweise Anleitungen zum Erstellen einer Pipeline mit einer Kopieraktivität.
