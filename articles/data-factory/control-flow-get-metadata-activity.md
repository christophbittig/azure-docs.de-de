---
title: Aktivität „Metadaten abrufen“
titleSuffix: Azure Data Factory & Azure Synapse
description: Erfahren Sie, wie Sie die Aktivität „Metadaten abrufen“ in einer Azure Data Factory- oder Azure Synapse Analytics-Pipeline verwenden.
author: jianleishen
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 08/24/2021
ms.author: jianleishen
ms.openlocfilehash: 4f619266d225e4dd36bb68ca9ebcaaae366891cb
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122821887"
---
# <a name="get-metadata-activity-in-azure-data-factory-or-azure-synapse-analytics"></a>Aktivität „Metadaten abrufen“ in Azure Data Factory und Azure Synapse Analytics

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Sie können die Aktivität „Metadaten abrufen“ verwenden, um Metadaten zu beliebigen Daten in Azure Data Factory oder einer Synapse-Pipeline abzurufen. Sie können die Ausgabe der Aktivität „Get Metadata“ (Metadaten abrufen) in bedingten Ausdrücken verwenden, um die Validierung durchzuführen oder die Metadaten in nachfolgenden Aktivitäten zu nutzen.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Die Aktivität „Metadaten abrufen“ nutzt ein Dataset als Eingabe und gibt Metadateninformationen als Ausgabe zurück. Derzeit werden die folgenden Connectors und entsprechenden abrufbaren Metadaten unterstützt. Die maximale Größe der zurückgegebenen Metadaten beträgt **4 MB**.

### <a name="supported-connectors"></a>Unterstützte Connectors

**File Storage**

| Connector/Metadaten | itemName<br>(Datei/Ordner) | itemType<br>(Datei/Ordner) | size<br>(Datei) | created<br>(Datei/Ordner) | lastModified<sup>1</sup><br>(Datei/Ordner) |childItems<br>(Ordner) |contentMD5<br>(Datei) | structure<sup>2</sup><br/>(Datei) | columnCount<sup>2</sup><br>(Datei) | exists<sup>3</sup><br>(Datei/Ordner) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| [Amazon S3](connector-amazon-simple-storage-service.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Amazon S3 Compatible Storage](connector-amazon-s3-compatible-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Google Cloud Storage](connector-google-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Oracle Cloud Storage](connector-oracle-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Blob Storage](connector-azure-blob-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | √ | √ | √ | √/√ |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | √ | √ | √ | √/√ |
| [Azure Files](connector-azure-file-storage.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [Dateisystem](connector-file-system.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [SFTP](connector-sftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [FTP](connector-ftp.md) | √/√ | √/√ | √ | x/x | x/x | √ | x | √ | √ | √/√ |

<sup>1</sup> `lastModified`-Metadaten:
- Für Amazon S3, Amazon S3-kompatiblen Speicher, Google Cloud Storage und Oracle Cloud Storage gilt `lastModified` für den Bucket und den Schlüssel, aber nicht für den virtuellen Ordner. `exists` gilt für den Bucket und den Schlüssel, aber nicht für das Präfix oder den virtuellen Ordner. 
- Für Azure Blob Storage gilt `lastModified` für den Container und das Blob, aber nicht für den virtuellen Ordner.

<sup>2</sup> Die Metadaten `structure` und `columnCount` werden beim Abrufen von Metadaten aus Binär-, JSON- oder XML-Dateien nicht unterstützt.

<sup>3</sup> `exists`-Metadaten: Für Amazon S3, Amazon S3-kompatiblen Speicher, Google Cloud Storage und Oracle Cloud Storage gilt `exists` für den Bucket und den Schlüssel, aber nicht für das Präfix oder den virtuellen Ordner.

Beachten Sie Folgendes:

- Wenn Sie die Aktivität „Metadaten abrufen“ für einen Ordner verwenden möchten, vergewissern Sie sich, dass Sie für den angegebenen Ordner über die Berechtigung zum Auflisten/Ausführen (LIST/EXECUTE) verfügen.
- Platzhalterfilter für Ordner/Dateien werden für die Aktivität „Metadaten abrufen" nicht unterstützt.
- Die Filter `modifiedDatetimeStart` und `modifiedDatetimeEnd` sind für den Connector festgelegt:

    - Diese zwei Eigenschaften werden beim Abrufen von Metadaten aus einem Ordner zum Filtern der untergeordneten Elemente verwendet. Sie werden beim Abrufen von Metadaten aus einer Datei nicht angewendet.
    - Wenn ein solcher Filter verwendet wird, enthält `childItems` in der Ausgabe nur die geänderten Dateien im festgelegten Bereich, aber keine Ordner.
    - Zum Anwenden eines solchen Filters durchläuft die GetMetadata-Aktivität alle Dateien im angegebenen Ordner und überprüft die Änderungszeit. Vermeiden Sie Verweise auf einen Ordner mit sehr vielen Dateien, selbst wenn Sie eine geringe Anzahl qualifizierter Dateien erwarten. 

**Relationale Datenbank**

| Connector/Metadaten | structure | columnCount | exists |
|:--- |:--- |:--- |:--- |
| [Azure SQL-Datenbank](connector-azure-sql-database.md) | √ | √ | √ |
| [Verwaltete Azure SQL-Datenbank-Instanz](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) | √ | √ | √ |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | √ | √ | √ |
| [SQL Server](connector-sql-server.md) | √ | √ | √ |

### <a name="metadata-options"></a>Metadatenoptionen

Sie können die folgenden Metadatentypen in der Feldliste der Aktivität „Metadaten abrufen“ angeben, um die entsprechenden Informationen abzurufen:

| Metadatentyp | BESCHREIBUNG |
|:--- |:--- |
| itemName | Name der Datei oder des Ordners. |
| itemType | Typ der Datei oder des Ordners. Der zurückgegebene Wert lautet `File` oder `Folder`. |
| size | Größe der Datei in Bytes. Gilt nur für Dateien. |
| created | Datum/Uhrzeit der Erstellung der Datei oder des Ordners. |
| lastModified | Datum/Uhrzeit der letzten Änderung der Datei oder des Ordners. |
| childItems | Liste der Unterordner und Dateien im angegebenen Ordner. Gilt nur für Ordner. Der Rückgabewert ist eine Liste mit den Namen und Typen der einzelnen untergeordneten Elemente. |
| contentMD5 | MD5 der Datei. Gilt nur für Dateien. |
| structure | Datenstruktur der Datei oder Tabelle in der relationalen Datenbank. Der Rückgabewert ist eine Liste von Spaltennamen und Spaltentypen. |
| columnCount | Anzahl der Spalten in der Datei oder relationalen Tabelle. |
| exists| Gibt an, ob eine Datei, ein Ordner oder eine Tabelle vorhanden ist. Wenn `exists` in der Feldliste der Aktivität „Metadaten abrufen“ angegeben ist, führt die Aktivität selbst dann nicht zu einem Fehler, wenn die Datei, der Ordner oder die Tabelle nicht vorhanden ist. Stattdessen wird `exists: false` in der Ausgabe zurückgegeben. |

> [!TIP]
> Wenn Sie überprüfen möchten, ob eine Datei, ein Ordner oder eine Tabelle vorhanden ist, geben Sie `exists` in der Feldliste der Aktivität „Metadaten abrufen“ an. Anschließend können Sie das Ergebnis (`exists: true/false`) in der Ausgabe der Aktivität überprüfen. Falls `exists` nicht in der Feldliste angegeben ist, schlägt die Aktivität „Metadaten abrufen“ fehl, wenn das Objekt nicht gefunden wird.

> [!NOTE]
> Wenn Sie Metadaten aus Dateispeichern abrufen und `modifiedDatetimeStart` oder `modifiedDatetimeEnd` konfigurieren, enthält `childItems` in der Ausgabe nur Dateien im angegebenen Pfad, deren letzte Änderung im angegebenen Zeitraum liegt. Elemente in Unterordnern werden nicht eingeschlossen.

> [!NOTE]
> Damit die Feldliste **Struktur** die tatsächliche Datenstruktur für Datasets im Textformat mit Trennzeichen und im Excel-Format bereitstellt, müssen Sie die Eigenschaft `First Row as Header` aktivieren, die nur für diese Datenquellen unterstützt wird.

## <a name="syntax"></a>Syntax

**Aktivität „Metadaten abrufen“**

```json
{
    "name":"MyActivity",
    "type":"GetMetadata",
    "dependsOn":[

    ],
    "policy":{
        "timeout":"7.00:00:00",
        "retry":0,
        "retryIntervalInSeconds":30,
        "secureOutput":false,
        "secureInput":false
    },
    "userProperties":[

    ],
    "typeProperties":{
        "dataset":{
            "referenceName":"MyDataset",
            "type":"DatasetReference"
        },
        "fieldList":[
            "size",
            "lastModified",
            "structure"
        ],
        "storeSettings":{
            "type":"AzureBlobStorageReadSettings"
        },
        "formatSettings":{
            "type":"JsonReadSettings"
        }
    }
}
```

**Dataset**

```json
{
    "name":"MyDataset",
    "properties":{
        "linkedServiceName":{
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[

        ],
        "type":"Json",
        "typeProperties":{
            "location":{
                "type":"AzureBlobStorageLocation",
                "fileName":"file.json",
                "folderPath":"folder",
                "container":"container"
            }
        }
    }
}
```

## <a name="type-properties"></a>Typeigenschaften

Die Aktivität „Metadaten abrufen“ kann zurzeit die folgenden Arten von Metadateninformationen zurückgeben:

Eigenschaft | BESCHREIBUNG | Erforderlich
-------- | ----------- | --------
fieldList | Die erforderlichen Arten von Metadateninformationen. Ausführliche Informationen zu unterstützten Metadaten finden Sie im Abschnitt [Metadatenoptionen](#metadata-options) dieses Artikels. | Ja 
dataset | Das Referenzdataset, dessen Metadaten von der Aktivität „Metadaten abrufen“ abgerufen werden sollen. Weitere Informationen zu unterstützten Connectors finden Sie im Abschnitt [Funktionen](#supported-capabilities). Ausführliche Informationen zur Syntax von Datasets finden Sie in den jeweiligen Connectorthemen. | Ja
formatSettings | Wird bei Verwendung eines formatierten Datasets angewendet. | Nein
storeSettings | Wird bei Verwendung eines formatierten Datasets angewendet. | Nein

## <a name="sample-output"></a>Beispielausgabe

Die Ergebnisse von „Metadaten abrufen“ werden in der Ausgabe der Aktivität angezeigt. Im Folgenden zwei Beispiele für umfassende Metadatenoptionen. Um die Ergebnisse in nachfolgenden Aktivitäten zu nutzen, verwenden Sie das Muster `@{activity('MyGetMetadataActivity').output.itemName}`.

### <a name="get-a-files-metadata"></a>Abrufen der Metadaten einer Datei

```json
{
  "exists": true,
  "itemName": "test.csv",
  "itemType": "File",
  "size": 104857600,
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "contentMD5": "cMauY+Kz5zDm3eWa9VpoyQ==",
  "structure": [
    {
        "name": "id",
        "type": "Int64"
    },
    {
        "name": "name",
        "type": "String"
    }
  ],
  "columnCount": 2
}
```

### <a name="get-a-folders-metadata"></a>Abrufen der Metadaten eines Ordners

```json
{
  "exists": true,
  "itemName": "testFolder",
  "itemType": "Folder",
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "childItems": [
    {
      "name": "test.avro",
      "type": "File"
    },
    {
      "name": "folder hello",
      "type": "Folder"
    }
  ]
}
```

## <a name="next-steps"></a>Nächste Schritte
Informationen zu weiteren unterstützten Ablaufsteuerungsaktivitäten:

- [Aktivität „Pipeline ausführen“](control-flow-execute-pipeline-activity.md)
- [ForEach-Aktivität](control-flow-for-each-activity.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)
- [Webaktivität](control-flow-web-activity.md)
