---
title: Kopieren von Daten aus mit Amazon Simple Storage Service (S3) kompatiblem Speicher
description: Erfahren Sie, wie Sie mithilfe einer Kopieraktivität in einer Azure Data Factory- oder Synapse Analytics-Pipeline Daten aus kompatiblem Amazon S3-Speicher in unterstützte Senkendatenspeicher kopieren.
titleSuffix: Azure Data Factory & Azure Synapse
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 82abc8fcfacc09621b66192c1f40b8c33d73ae50
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128563458"
---
# <a name="copy-data-from-amazon-s3-compatible-storage-by-using-azure-data-factory-or-synapse-analytics"></a>Kopieren von Daten aus kompatiblem Amazon S3-Speicher mithilfe von Azure Data Factory oder Synapse Analytics


[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie Daten aus mit Amazon Simple Storage Service (Amazon S3) kompatiblem Speicher kopieren. Weitere Informationen finden Sie in den Einführungsartikeln zu [Azure Data Factory](introduction.md) und [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md).

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Dieser Amazon S3-kompatible Storage-Connector wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)
- [GetMetadata-Aktivität](control-flow-get-metadata-activity.md)
- [Delete-Aktivität](delete-activity.md)

Dieser Amazon S3-kompatible Storage-Connector unterstützt insbesondere das Kopieren von Dateien im jeweiligen Zustand oder das Analysieren von Dateien mit den [unterstützten Dateiformaten und Codecs für die Komprimierung](supported-file-formats-and-compression-codecs.md). Der Connector verwendet [AWS Signatur Version 4](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) zum Authentifizieren von Anforderungen in S3. Mit diesem Amazon S3-kompatiblen Storage-Connector können Sie Daten von allen S3-kompatiblen Speicheranbietern kopieren. Geben Sie die entsprechende Dienst-URL in der Konfiguration des verknüpften Diensts an.



## <a name="required-permissions"></a>Erforderliche Berechtigungen

Um Daten aus Amazon S3-kompatiblem Speicher kopieren zu können, müssen Sie sicherstellen, dass Ihnen die folgenden Berechtigungen für Amazon S3-Objektvorgänge erteilt wurden: `s3:GetObject` und `s3:GetObjectVersion`.

Wenn Sie die Benutzeroberfläche für die Erstellung verwenden, sind zusätzliche `s3:ListAllMyBuckets`- und `s3:ListBucket`/`s3:GetBucketLocation`-Berechtigungen für Vorgänge wie das Testen der Verbindung mit dem verknüpften Dienst und das Durchsuchen aus dem Stammverzeichnis erforderlich. Wenn Sie diese Berechtigungen nicht erteilen möchten, können Sie auf der Benutzeroberfläche die Option zum Testen der Verbindung mit dem Dateipfad oder zum Durchsuchen vom angegebenen Pfad auswählen.

Die vollständige Liste der Amazon S3-Berechtigungen finden Sie unter [Specifying Permissions in a Policy](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html) (Angeben von Berechtigungen in einer Richtlinie) auf der AWS-Website.

## <a name="getting-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-amazon-s3-compatible-storage-using-ui"></a>Erstellen eines verknüpften Diensts mit dem kompatiblen Amazon S3-Speicher über die Benutzeroberfläche

Führen Sie die folgenden Schritte aus, um über die Benutzeroberfläche des Azure-Portals einen verknüpften Dienst für den kompatiblen Amazon S3-Speicher zu erstellen.

1. Navigieren Sie in Ihrem Azure Data Factory- oder Synapse-Arbeitsbereich zu der Registerkarte „Verwalten“, wählen Sie „Verknüpfte Dienste“ aus und klicken Sie dann auf „Neu“:

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Erstellen Sie einen neuen verknüpften Dienst mithilfe der Azure Data Factory-Benutzeroberfläche.":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Erstellen Sie einen neuen verknüpften Dienst mithilfe der Azure Synapse Benutzeroberfläche.":::

2. Suchen Sie nach Amazon, und wählen Sie den Connector für „Amazon S3 Compatible Storage“ aus.

   :::image type="content" source="media/connector-amazon-s3-compatible-storage/amazon-s3-compatible-storage-connector.png" alt-text="Wählen Sie den Connector für den kompatiblen Amazon S3-Speicher aus.":::    


1. Konfigurieren Sie die Dienstdetails, testen Sie die Verbindung und erstellen Sie den neuen verknüpften Dienst.

   :::image type="content" source="media/connector-amazon-s3-compatible-storage/configure-amazon-s3-compatible-storage-linked-service.png" alt-text="Konfigurieren Sie einen verknüpften Diensts mit dem kompatiblen Amazon S3-Speicher.":::

## <a name="connector-configuration-details"></a>Details zur Connectorkonfiguration 

In den folgenden Abschnitten finden Sie Details zu Eigenschaften, die zum Definieren von Entitäten verwendet werden, die für kompatiblen Amazon S3-Speicher spezifisch sind.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Für einen mit Amazon S3 kompatiblen verknüpften Dienst werden die folgenden Eigenschaften unterstützt:

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| Typ | Die **type**-Eigenschaft muss auf **AmazonS3Compatible** festgelegt werden. | Ja |
| accessKeyId | ID des geheimen Zugriffsschlüssels. |Ja |
| secretAccessKey | Der geheime Zugriffsschlüssel selbst. Markieren Sie dieses Feld als **SecureString**, um es sicher zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). |Ja |
| serviceUrl | Geben Sie den benutzerdefinierten S3-Endpunkt `https://<service url>` an. | Nein |
| forcePathStyle | Gibt an, ob anstelle des Zugriffs mit virtuellem Hosting der [Zugriff im S3-Pfadstil](https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html#path-style-access) verwendet wird. Zulässige Werte sind **false** (Standard) und **true**.<br> Sehen Sie in der Dokumentation des jeweiligen Datenspeichers nach, ob der Zugriff im Pfadstil verwendet werden muss. |Nein |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Sie können die Azure Integration Runtime oder eine selbstgehostete Integration Runtime verwenden (sofern sich Ihr Datenspeicher in einem privaten Netzwerk befindet). Wenn diese Eigenschaft nicht angegeben ist, verwendet der Dienst die normale Azure Integration Runtime. |Nein |



**Beispiel:**

```json
{
    "name": "AmazonS3CompatibleLinkedService",
    "properties": {
        "type": "AmazonS3Compatible",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
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

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). 

[!INCLUDE [data-factory-v2-file-formats](includes/data-factory-v2-file-formats.md)] 

Die folgenden Eigenschaften werden für Amazon S3-kompatiblen Speicher unter `location`-Einstellungen in einem formatbasierten Dataset unterstützt:

| Eigenschaft   | BESCHREIBUNG                                                  | Erforderlich |
| ---------- | ------------------------------------------------------------ | -------- |
| Typ       | Die **type**-Eigenschaft unter `location` in einem Dataset muss auf **AmazonS3CompatibleLocation** festgelegt werden. | Ja      |
| bucketName | Der Bucketname des S3-kompatiblen Speichers.                                          | Ja      |
| folderPath | Der Pfad zum Ordner unter dem angegebenen Bucket. Wenn Sie einen Platzhalter verwenden möchten, um den Ordner zu filtern, überspringen Sie diese Einstellung, und geben Sie dies in den entsprechenden Aktivitätsquelleneinstellungen an. | Nein       |
| fileName   | Der Name der Datei unter dem angegebenen Bucket und Ordnerpfad. Wenn Sie einen Platzhalter verwenden möchten, um Dateien zu filtern, überspringen Sie diese Einstellung, und geben Sie dies in den entsprechenden Aktivitätsquelleneinstellungen an. | Nein       |
| version | Die Version des S3-kompatiblen Speicherobjekts, wenn die Versionsverwaltung für S3-kompatible Speicher aktiviert ist. Wenn dies nicht angegeben ist, wird die neueste Version abgerufen. |Nein |

**Beispiel:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 Compatible Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AmazonS3CompatibleLocation",
                "bucketName": "bucketname",
                "folderPath": "folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). In diesem Abschnitt finden Sie eine Liste der Eigenschaften, die von der Amazon S3-kompatiblen Speicherquelle unterstützt werden.

### <a name="amazon-s3-compatible-storage-as-a-source-type"></a>Amazon S3-kompatibler Speicher als Quelltyp

[!INCLUDE [data-factory-v2-file-formats](includes/data-factory-v2-file-formats.md)] 

Die folgenden Eigenschaften werden für Amazon S3-kompatiblen Speicher unter `storeSettings`-Einstellungen in einer formatbasierten Kopierquelle unterstützt:

| Eigenschaft                 | BESCHREIBUNG                                                  | Erforderlich                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| Typ                     | Die **type**-Eigenschaft unter `storeSettings` muss auf **AmazonS3CompatibleReadSettings** festgelegt werden. | Ja                                                         |
| ***Suchen Sie die zu kopierenden Dateien:*** |  |  |
| OPTION 1: statischer Pfad<br> | Kopieren Sie aus dem im Dataset angegebenen Bucket oder Ordner/Dateipfad. Wenn Sie alle Dateien aus einem Bucket oder Ordner kopieren möchten, geben Sie zusätzlich für `wildcardFileName` den Wert `*` an. |  |
| OPTION 2: Präfix für S3-kompatiblen Speicher<br>– prefix | Das Präfix für den Namen des S3-kompatiblen Speicherschlüssels unter dem angegebenen Bucket, konfiguriert in einem Dataset zum Filtern von S3-kompatiblen Quellspeicherdateien. Es werden die S3-kompatiblen Speicherschlüssel ausgewählt, deren Namen mit `bucket_in_dataset/this_prefix` beginnen. Es wird der dienstseitige Filter des S3-kompatiblen Speichers verwendet, dessen Leistung im Vergleich zu Platzhalterfiltern besser ist.<br/><br/>Wenn Sie das Präfix verwenden und in eine dateibasierte Senke mit Beibehaltung der Hierarchie kopieren, wird der Unterpfad nach dem letzten „/“ im Präfix beibehalten. Wenn Sie beispielsweise `bucket/folder/subfolder/file.txt` als Quelle haben und das Präfix als `folder/sub` konfigurieren, lautet der beibehaltene Dateipfad `subfolder/file.txt`. | Nein |
| OPTION 3: Platzhalter<br>– wildcardFolderPath | Der Ordnerpfad mit Platzhalterzeichen unter dem angegebenen Bucket, der in einem Dataset für das Filtern von Quellordnern konfiguriert ist. <br>Folgende Platzhalter sind zulässig: `*` (entspricht null [0] oder mehr Zeichen) und `?` (entspricht null [0] oder einem einzelnen Zeichen). Verwenden Sie `^` als Escapezeichen, wenn Ihr Ordnername einen Platzhalter oder dieses Escapezeichen enthält. <br>Weitere Beispiele finden Sie unter [Beispiele für Ordner- und Dateifilter](#folder-and-file-filter-examples). | Nein                                            |
| OPTION 3: Platzhalter<br>– wildcardFileName | Der Dateiname mit Platzhalterzeichen unter dem angegebenen Bucket und Ordnerpfad (oder Platzhalterordnerpfad) für das Filtern von Quelldateien. <br>Folgende Platzhalter sind zulässig: `*` (entspricht null [0] oder mehr Zeichen) und `?` (entspricht null [0] oder einem einzelnen Zeichen). Verwenden Sie `^` als Escapezeichen, wenn der tatsächliche Dateiname einen Platzhalter oder dieses Escapezeichen enthält.  Weitere Beispiele finden Sie unter [Beispiele für Ordner- und Dateifilter](#folder-and-file-filter-examples). | Ja |
| OPTION 4: eine Liste von Dateien<br>– fileListPath | Gibt an, dass eine bestimmte Dateigruppe kopiert werden soll. Verweisen Sie auf eine Textdatei, die eine Liste der zu kopierenden Dateien enthält, und zwar eine Datei pro Zeile. Dies ist der relative Pfad zu dem im Dataset konfigurierten Pfad.<br/>Wenn Sie diese Option verwenden, geben Sie keinen Dateinamen im Dataset an. Weitere Beispiele finden Sie unter [Beispiele für Dateilisten](#file-list-examples). |Nein |
| ***Zusätzliche Einstellungen:*** |  | |
| recursive | Gibt an, ob die Daten rekursiv aus den Unterordnern oder nur aus dem angegebenen Ordner gelesen werden. Beachten Sie Folgendes: Wenn **recursive** auf **TRUE** festgelegt ist und es sich bei der Senke um einen dateibasierten Speicher handelt, wird ein leerer Ordner oder Unterordner nicht in die Senke kopiert und dort auch nicht erstellt. <br>Zulässige Werte sind **true** (Standard) und **false**.<br>Diese Eigenschaft gilt nicht, wenn Sie `fileListPath` konfigurieren. |Nein |
| deleteFilesAfterCompletion | Gibt an, ob die Binärdateien nach dem erfolgreichen Verschieben in den Zielspeicher aus dem Quellspeicher gelöscht werden. Die Dateien werden einzeln gelöscht, sodass Sie bei einem Fehler der Kopieraktivität feststellen werden, dass einige Dateien bereits ins Ziel kopiert und aus der Quelle gelöscht wurden, wohingegen sich andere weiter im Quellspeicher befinden. <br/>Diese Eigenschaft ist nur im Szenario zum Kopieren von Binärdateien gültig. Standardwert: FALSE. |Nein |
| modifiedDatetimeStart    | Die Dateien werden anhand des Attributs „Letzte Änderung“ gefiltert. <br>Die Dateien werden ausgewählt, wenn der Zeitpunkt der letzten Änderung innerhalb des Zeitbereichs zwischen `modifiedDatetimeStart` und `modifiedDatetimeEnd` liegt. Die Zeit wird auf die UTC-Zeitzone im Format „2018-12-01T05:00:00Z“ angewendet. <br> Die Eigenschaften können **NULL** sein, was bedeutet, dass kein Dateiattributfilter auf das Dataset angewendet wird.  Wenn `modifiedDatetimeStart` einen datetime-Wert aufweist, aber `modifiedDatetimeEnd` **NULL** ist, werden die Dateien ausgewählt, deren Attribut für die letzte Änderung größer oder gleich dem datetime-Wert ist.  Wenn `modifiedDatetimeEnd` den datetime-Wert aufweist, aber `modifiedDatetimeStart` **NULL** ist, werden die Dateien ausgewählt, deren Attribut für die letzte Änderung kleiner als der datetime-Wert ist.<br/>Diese Eigenschaft gilt nicht, wenn Sie `fileListPath` konfigurieren. | Nein                                            |
| modifiedDatetimeEnd      | Wie oben.                                               | Nein                                                          |
| enablePartitionDiscovery | Geben Sie bei partitionierten Dateien an, ob die Partitionen anhand des Dateipfads analysiert und als zusätzliche Quellspalten hinzugefügt werden sollen.<br/>Zulässige Werte sind **false** (Standard) und **true**. | Nein                                            |
| partitionRootPath | Wenn die Partitionsermittlung aktiviert ist, geben Sie den absoluten Stammpfad an, um partitionierte Ordner als Datenspalten zu lesen.<br/><br/>Ohne Angabe gilt standardmäßig Folgendes:<br/>- Wenn Sie den Dateipfad im Dataset oder die Liste der Dateien in der Quelle verwenden, ist der Partitionsstammpfad der im Dataset konfigurierte Pfad.<br/>Wenn Sie einen Platzhalterordnerfilter verwenden, ist der Stammpfad der Partition der Unterpfad vor dem ersten Platzhalter.<br/>Wenn Sie Präfix verwenden, ist der Stammpfad der Partition ein Unterpfad vor dem letzten „/“. <br/><br/>Angenommen, Sie konfigurieren den Pfad im Dataset als „root/folder/year=2020/month=08/day=27“:<br/>- Wenn Sie den Stammpfad der Partition als „root/folder/year=2020“ angeben, generiert die Kopieraktivität zusätzlich zu den Spalten in den Dateien die beiden weiteren Spalten `month` und `day` mit den Werten „08“ bzw. „27“.<br/>- Wenn kein Stammpfad für die Partition angegeben ist, wird keine zusätzliche Spalte generiert. | Nein                                            |
| maxConcurrentConnections |Die Obergrenze gleichzeitiger Verbindungen mit dem Datenspeicher während der Aktivitätsausführung. Geben Sie diesen Wert nur an, wenn Sie die Anzahl der gleichzeitigen Verbindungen begrenzen möchten.| Nein                                                          |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3CompatibleStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
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
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AmazonS3CompatibleReadSettings",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Beispiele für Ordner- und Dateifilter

Dieser Abschnitt beschreibt das sich ergebende Verhalten für den Ordnerpfad und den Dateinamen mit Platzhalterfiltern.

| bucket | Schlüssel | recursive | Quellordnerstruktur und Filterergebnis (Dateien mit Fettformatierung werden abgerufen.)|
|:--- |:--- |:--- |:--- |
| bucket | `Folder*/*` | false | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AndererOrdnerB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei6.csv |
| bucket | `Folder*/*` | true | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AndererOrdnerB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei6.csv |
| bucket | `Folder*/*.csv` | false | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AndererOrdnerB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei6.csv |
| bucket | `Folder*/*.csv` | true | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AndererOrdnerB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei6.csv |

### <a name="file-list-examples"></a>Beispiele für Dateilisten

In diesem Abschnitt wird das resultierende Verhalten beschrieben, wenn ein Dateilistenpfad in der Quelle einer Kopieraktivität verwendet wird.

Angenommen, Sie haben die folgende Quellordnerstruktur und möchten die Dateien kopieren, deren Namen fett formatiert sind:

| Beispielquellstruktur                                      | Inhalt in „FileListToCopy.txt“                             | Konfiguration |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadaten<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | Datei1.csv<br>Unterordner1/Datei3.csv<br>Unterordner1/Datei5.csv | **Im Dataset:**<br>– Bucket: `bucket`<br>– Ordnerpfad: `FolderA`<br><br>**In der Quelle der Kopieraktivität:**<br>– Dateilistenpfad: `bucket/Metadata/FileListToCopy.txt` <br><br>Der Dateilistenpfad verweist auf eine Textdatei im selben Datenspeicher, der eine Liste der zu kopierenden Dateien enthält, und zwar eine Datei pro Zeile. Diese enthält den relativen Pfad zu dem im Dataset konfigurierten Pfad. |


## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [Lookup-Aktivität](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Eigenschaften der GetMetadata-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [GetMetadata-Aktivität](control-flow-get-metadata-activity.md). 

## <a name="delete-activity-properties"></a>Eigenschaften der Delete-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [Delete-Aktivität](delete-activity.md).


## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die diese Kopieraktivität als Quellen und Senken unterstützt, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).
