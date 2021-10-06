---
title: Kopieren von Daten aus oder nach MongoDB
titleSuffix: Azure Data Factory & Azure Synapse
description: Erfahren Sie, wie Sie mithilfe einer Copy-Aktivität in einer Azure Data Factory- oder Synapse Analytics-Pipeline Daten aus MongoDB in unterstützte Senkendatenspeicher oder Daten aus unterstützten Quelldatenspeichern nach MongoDB kopieren.
author: jianleishen
ms.author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 5642577cf8b8e1edf741c09bf4e1968d5cd69770
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124831723"
---
# <a name="copy-data-from-or-to-mongodb-using-azure-data-factory-or-synapse-analytics"></a>Kopieren von Daten aus oder nach MongoDB mithilfe von Azure Data Factory oder Synapse Analytics

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie die Copy-Aktivität in Azure Data Factory- oder Azure Synapse Analytics-Pipelines verwenden, um Daten aus einer MongoDB-Datenbank oder in eine MongoDB-Datenbank zu kopieren. Er baut auf dem Artikel zur [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

>[!IMPORTANT]
>Der neue MongoDB-Connector umfasst eine bessere native MongoDB-Unterstützung. Wenn Sie den älteren MongoDB-Connector in Ihrer Lösung verwenden, der lediglich aus Gründen der Abwärtskompatibilität unverändert unterstützt wird, finden Sie entsprechende Informationen im Artikel [MongoDB-Connector (Legacy)](connector-mongodb-legacy.md).


## <a name="supported-capabilities"></a>Unterstützte Funktionen

Sie können Daten aus einer MongoDB-Datenbank in einen unterstützten Senkendatenspeicher oder Daten aus einem unterstützten Quellendatenspeicher in eine MongoDB-Datenbank kopieren. Eine Liste der Datenspeicher, die als Quellen oder Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

Dieser MongoDB-Connector unterstützt insbesondere die **Versionen bis 4.2**.


## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)]


## <a name="getting-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-mongodb-using-ui"></a>Erstellen eines verknüpften Diensts für MongoDB über die Benutzeroberfläche

Führen Sie die folgenden Schritte aus, um einen verknüpften Dienst für MongoDB in der Benutzeroberfläche des Azure-Portals zu erstellen.

1. Navigieren Sie in Ihrem Azure Data Factory- oder Synapse-Arbeitsbereich zur Registerkarte „Verwalten“, wählen Sie „Verknüpfte Dienste“ aus, und klicken Sie dann auf „Neu“:

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Erstellen Sie einen neuen verknüpften Dienst mithilfe der Azure Data Factory-Benutzeroberfläche.":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Erstellen Sie einen neuen verknüpften Dienst mithilfe der Azure Synapse-Benutzeroberfläche.":::

2. Suchen Sie nach MongoDB, und wählen Sie den MongoDB-Connector aus.

    :::image type="content" source="media/connector-mongodb/mongodb-connector.png" alt-text="Wählen Sie den MongoDB-Connector aus.":::    

1. Konfigurieren Sie die Dienstdetails, testen Sie die Verbindung, und erstellen Sie den neuen verknüpften Dienst.

    :::image type="content" source="media/connector-mongodb/configure-mongodb-linked-service.png" alt-text="Konfigurieren Sie einen mit MongoDB verknüpften Dienst.":::

## <a name="connector-configuration-details"></a>Details zur Connectorkonfiguration

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für den MongoDB-Connector verwendet werden:


## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit MongoDB verknüpften Dienst unterstützt:

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| type |Die type-Eigenschaft muss auf Folgendes festgelegt werden: **MongoDbV2** |Ja |
| connectionString |Geben Sie die MongoDB-Verbindungszeichenfolge an, z.B. `mongodb://[username:password@]host[:port][/[database][?options]]`. Weitere Informationen finden Sie im [MongoDB-Handbuch zur Verbindungszeichenfolge](https://docs.mongodb.com/manual/reference/connection-string/). <br/><br /> Sie können eine Verbindungszeichenfolge auch in Azure Key Vault speichern. Ausführlichere Informationen finden Sie unter [Speichern von Anmeldeinformationen in Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| database | Der Name der Datenbank, auf die Sie zugreifen möchten. | Ja |
| connectVia | Die [Integrationslaufzeit](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden muss. Weitere Informationen finden Sie im Abschnitt [Voraussetzungen](#prerequisites). Wenn keine Option angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. |Nein |

**Beispiel:**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDbV2",
        "typeProperties": {
            "connectionString": "mongodb://[username:password@]host[:port][/[database][?options]]",
            "database": "myDatabase"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie unter [Datasets und verknüpfte Dienste](concepts-datasets-linked-services.md). Folgende Eigenschaften werden für das MongoDB-Dataset unterstützt:

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft des Datasets muss auf folgenden Wert festgelegt werden: **MongoDbV2Collection** | Ja |
| collectionName |Der Name der Sammlung in der MongoDB-Datenbank |Ja |

**Beispiel:**

```json
{
    "name": "MongoDbDataset",
    "properties": {
        "type": "MongoDbV2Collection",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```


## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der MongoDB-Quelle und -Senke unterstützt werden.

### <a name="mongodb-as-source"></a>MongoDB als Quelle

Folgende Eigenschaften werden im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf Folgendes festgelegt werden: **MongoDbV2Source** | Ja |
| filter | Gibt den Auswahlfilter mit Abfrageoperatoren an. Um alle Dokumente in einer Sammlung zurückzugeben, lassen Sie diesen Parameter aus oder übergeben Sie ein leeres Dokument ({}). | Nein |
| cursorMethods.project | Gibt die Felder an, die in den Dokumenten für die Projektion zurückgegeben werden sollen. Um alle Felder in den entsprechenden Dokumenten zurückzugeben, lassen Sie diesen Parameter aus. | Nein |
| cursorMethods.sort | Gibt die Reihenfolge an, in der übereinstimmende Dokumente von der Abfrage zurückgegeben werden. Siehe [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Nein |
| cursorMethods.limit | Gibt die maximale Anzahl von Dokumenten an, die vom Server zurückgegeben werden. Siehe [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Nein |
| cursorMethods.skip | Gibt die Anzahl von Dokumenten an, die übersprungen werden sollen und ab denen MongoDB mit der Rückgabe von Ergebnissen beginnt. Siehe [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Nein |
| batchSize | Gibt die Anzahl von Dokumenten an, die in jedem Batch der Antwort von der MongoDB-Instanz zurückgegeben werden sollen. In den meisten Fällen wirkt sich eine Änderung der Batchgröße nicht auf den Benutzer oder die Anwendung aus. In Cosmos DB ist die maximale Größe von Batches auf 40 MB begrenzt. Dies entspricht der Summe des batchSize-Werts für die Anzahl von Dokumenten. Verringern Sie deshalb diesen Wert, falls Ihre Dokumente groß sind. | Nein<br/>(der Standardwert ist **100**) |

>[!TIP]
>Der Dienst unterstützt die Nutzung eines BSON-Dokuments im **Strict-Modus**. Stellen Sie sicher, dass die Filterabfrage den Strict-Modus und nicht den Shell-Modus aufweist. Eine weitere Beschreibung finden Sie im [MongoDB-Handbuch](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromMongoDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB input dataset name>",
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
                "type": "MongoDbV2Source",
                "filter": "{datetimeData: {$gte: ISODate(\"2018-12-11T00:00:00.000Z\"),$lt: ISODate(\"2018-12-12T00:00:00.000Z\")}, _id: ObjectId(\"5acd7c3d0000000000000000\") }",
                "cursorMethods": {
                    "project": "{ _id : 1, name : 1, age: 1, datetimeData: 1 }",
                    "sort": "{ age : 1 }",
                    "skip": 3,
                    "limit": 3
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="mongodb-as-sink"></a>MongoDB als Senke

Die folgenden Eigenschaften werden im Abschnitt **sink** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| Typ | Die **type**-Eigenschaft der Senke der Kopieraktivität muss auf **MongoDbV2Sink** festgelegt werden. |Ja |
| writeBehavior |Beschreibt, wie Daten in MongoDB geschrieben werden. Zulässige Werte: **insert** und **upsert**.<br/><br/>Das Verhalten von **upsert** besteht darin, das Dokument zu ersetzen, wenn ein Dokument mit dem gleichen `_id`-Typ bereits vorhanden ist. Andernfalls wird das Dokument eingefügt.<br /><br />**Hinweis**: Der Dienst generiert automatisch eine `_id` für ein Dokument, wenn eine `_id` weder im Originaldokument noch durch eine Spaltenzuordnung angegeben wird. Dies bedeutet, dass Sie sicherstellen müssen, dass Ihr Dokument eine ID besitzt, damit **upsert** wie erwartet funktioniert. |Nein<br />(der Standardwert ist **insert**) |
| writeBatchSize | Die **writeBatchSize**-Eigenschaft steuert die Größe der in jeden Batch zu schreibenden Dokumente. Sie können versuchen, den Wert für **writeBatchSize** zu erhöhen, um die Leistung zu verbessern, oder den Wert verringern, falls Ihre Dokumente groß sind. |Nein<br />(der Standardwert ist **10.000**) |
| writeBatchTimeout | Die Wartezeit, bis der Batch-Einfügevorgang beendet ist, bevor er eine Zeitüberschreitung verursacht. Der zulässige Wert ist timespan. | Nein<br/>(der Standardwert ist **00:30:00** – 30 Minuten) |

>[!TIP]
>Informationen zum Importieren von JSON-Dokumenten in unveränderter Form finden Sie im Abschnitt [Importieren oder Exportieren von JSON-Dokumenten](#import-and-export-json-documents). Informationen zum Kopieren aus tabellarisch strukturierten Daten finden Sie unter [Schemazuordnung](#schema-mapping).

**Beispiel**

```json
"activities":[
    {
        "name": "CopyToMongoDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "MongoDbV2Sink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

## <a name="import-and-export-json-documents"></a>Importieren und Exportieren von JSON-Dokumenten

Sie können diesen MongoDB-Connector verwenden, um die folgenden Aufgaben auf einfache Weise zu erledigen:

* Dokumente zwischen zwei MongoDB-Sammlungen unverändert kopieren.
* Importieren von JSON-Dokumenten aus verschiedenen Quellen in MongoDB, z. B. aus Azure Cosmos DB, Azure Blob Storage, Azure Data Lake Store und anderen unterstützten dateibasierten Speichern.
* Exportieren von JSON-Dokumenten aus einer MongoDB-Sammlung in verschiedene dateibasierte Speicher.

Um eine solche vom Schema unabhängige Kopie zu erzielen, überspringen Sie den Abschnitt „structure“ (auch *schema* genannt) im Dataset und die Schemazuordnung in der Kopieraktivität.


## <a name="schema-mapping"></a>Schemazuordnung

Informationen zum Kopieren von Daten aus MongoDB in eine tabellarische Senke oder umgekehrt finden Sie unter [Schemazuordnung](copy-activity-schema-and-type-mapping.md#schema-mapping).


## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die als Quelles und Senken für die Kopieraktivität unterstützt werden, finden Sie in der Dokumentation für [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).
