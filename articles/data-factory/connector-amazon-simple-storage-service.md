---
title: Kopieren und Transformieren von Daten in Amazon Simple Storage Service (S3)
titleSuffix: Azure Data Factory & Azure Synapse
description: Hier erfahren Sie, wie Sie Daten aus Amazon Simple Storage Service (S3) kopieren und Daten über Azure Data Factory- oder Azure Synapse Analytics-Pipelines in Amazon Simple Storage Service (S3) transformieren.
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 10/15/2021
ms.openlocfilehash: ffc3a58ef83d667c812ae1c4b9cc3f899a1aa03d
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130063999"
---
# <a name="copy-and-transform-data-in-amazon-simple-storage-service-using-azure-data-factory-or-azure-synapse-analytics"></a>Kopieren und Transformieren von Daten in Amazon Simple Storage Service mithilfe von Azure Data Factory oder Azure Synapse Analytics
> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
>
> * [Version 1](v1/data-factory-amazon-simple-storage-service-connector.md)
> * [Aktuelle Version](connector-amazon-simple-storage-service.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie mithilfe der Kopieraktivität Daten aus Amazon Simple Storage Service (Amazon S3) kopieren und Datenfluss zum Transformieren von Daten in Amazon S3 verwenden. Weitere Informationen finden Sie in den Einführungsartikeln zu [Azure Data Factory](introduction.md) und [Synapse Analytics](../synapse-analytics/overview-what-is.md).

>[!TIP]
>Weitere Informationen über das Szenario für die Datenmigration von Amazon S3 zu Azure Storage finden Sie unter [Migrieren von Daten von Amazon S3 zu Azure Storage](data-migration-guidance-s3-azure-storage.md).

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Dieser Amazon S3-Connector wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)
- [GetMetadata-Aktivität](control-flow-get-metadata-activity.md)
- [Delete-Aktivität](delete-activity.md)

Der Amazon S3-Connector unterstützt insbesondere das Kopieren von Dateien im jeweiligen Zustand oder Analysieren von Dateien mit den [unterstützten Dateiformaten und Codecs für die Komprimierung](supported-file-formats-and-compression-codecs.md). Sie können auch festlegen, dass [Dateimetadaten beim Kopieren beibehalten werden](#preserve-metadata-during-copy). Der Connector verwendet [AWS Signatur Version 4](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) zum Authentifizieren von Anforderungen in S3.

>[!TIP]
>Wenn Sie Daten von einem *beliebigen S3-kompatiblen Speicheranbieter* kopieren möchten, finden Sie weitere Informationen unter [Amazon S3-kompatibler Speicher](connector-amazon-s3-compatible-storage.md).

## <a name="required-permissions"></a>Erforderliche Berechtigungen

Um Daten von Amazon S3 zu kopieren, müssen Sie sicherstellen, dass Ihnen die folgenden Berechtigungen für Amazon S3-Objektvorgänge erteilt wurden: `s3:GetObject` und `s3:GetObjectVersion`

Wenn Sie die Data Factory-Benutzeroberfläche für die Erstellung verwenden, sind zusätzliche die Berechtigungen `s3:ListAllMyBuckets` und `s3:ListBucket`/`s3:GetBucketLocation` für Vorgänge wie das Testen der Verbindung mit dem verknüpften Dienst und das Durchsuchen vom Stamm erforderlich. Wenn Sie diese Berechtigungen nicht erteilen möchten, können Sie auf der Benutzeroberfläche die Option zum Testen der Verbindung mit dem Dateipfad oder zum Durchsuchen vom angegebenen Pfad auswählen.

Die vollständige Liste der Amazon S3-Berechtigungen finden Sie unter [Specifying Permissions in a Policy](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html) (Angeben von Berechtigungen in einer Richtlinie) auf der AWS-Website.

## <a name="getting-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)] 

## <a name="create-an-amazon-simple-storage-service-s3-linked-service-using-ui"></a>Erstellen eines mit Amazon Simple Storage Service (S3) verknüpften Dienstes über die Benutzeroberfläche

Verwenden Sie die folgenden Schritte, um einen mit Amazon S3 verknüpften Service in der Benutzeroberfläche des Azure-Portals zu erstellen.

1. Navigieren Sie in Ihrem Azure Data Factory- oder Synapse-Arbeitsbereich zu der Registerkarte „Verwalten“, wählen Sie „Verknüpfte Dienste“ aus und klicken Sie dann auf „Neu“:

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Screenshot: Erstellen eines neuen verknüpften Diensts über die Azure Data Factory-Benutzeroberfläche":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Ein Screenshot, der das Erstellen eines neuen verknüpften Diensts mit der Azure Synapse Benutzeroberfläche zeigt.":::

2. Suchen Sie nach Amazon, und wählen Sie den Amazon S3-Connector aus.

    :::image type="content" source="media/connector-amazon-simple-storage-service/amazon-simple-storage-service-connector.png" alt-text="Screenshot vom Amazon S3-Connector.":::    

1. Konfigurieren Sie die Dienstdetails, testen Sie die Verbindung und erstellen Sie den neuen verknüpften Dienst.

    :::image type="content" source="media/connector-amazon-simple-storage-service/configure-amazon-simple-storage-service-linked-service.png" alt-text="Screenshot von der Konfiguration für einen verknüpften S3 verknüpften Dienst.":::

## <a name="connector-configuration-details"></a>Details zur Connector-Konfiguration

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren spezifischer Data Factory-Entitäten für Amazon S3 verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für einen mit Amazon S3 verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft muss auf **AmazonS3** festgelegt werden. | Ja |
| authenticationType | Geben Sie den Authentifizierungstyp an, der für die Verbindung mit Amazon S3 verwendet wird. Sie können Zugriffsschlüssel für ein AWS-IAM-Konto (Identity & Access Management) oder [temporäre Sicherheitsanmeldeinformationen](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) verwenden.<br>Zulässige Werte sind `AccessKey` (Standard) und `TemporarySecurityCredentials`. |Nein  |
| accessKeyId | ID des geheimen Zugriffsschlüssels. |Ja |
| secretAccessKey | Der geheime Zugriffsschlüssel selbst. Markieren Sie dieses Feld als **SecureString**, um es sicher zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). |Ja |
| sessionToken | Trifft zu, wenn für die Authentifizierung [temporäre Sicherheitsanmeldeinformationen](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) verwendet werden. Erfahren Sie, wie Sie [temporäre Sicherheitsanmeldeinformationen](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_request.html#api_getsessiontoken) von AWS anfordern.<br>Beachten Sie, dass die temporären AWS-Anmeldeinformationen je nach Einstellung nach 15 Minuten bis 36 Stunden ablaufen. Stellen Sie sicher, dass Ihre Anmeldeinformationen während der Aktivitätsausführung gültig sind. Dies gilt insbesondere für operationalisierte Workloads. Sie können sie z. B. regelmäßig aktualisieren und in Azure Key Vault speichern.<br>Markieren Sie dieses Feld als **SecureString**, um es sicher zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). |Nein |
| serviceUrl | Geben Sie den benutzerdefinierten S3-Endpunkt `https://<service url>` an. | Nein |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Sie können die Azure Integration Runtime oder eine selbstgehostete Integration Runtime verwenden (sofern sich Ihr Datenspeicher in einem privaten Netzwerk befindet). Wenn diese Eigenschaft nicht angegeben ist, verwendet der Dienst die normale Azure Integration Runtime. |Nein |


**Beispiel: Verwenden der Authentifizierung mit Speicherzugriffsschlüssel**

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AmazonS3",
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

**Beispiel: Verwenden der Authentifizierung mit temporären Sicherheitsanmeldeinformationen**

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "authenticationType": "TemporarySecurityCredentials",
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            },
            "sessionToken": {
                "type": "SecureString",
                "value": "<session token>"
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

Folgende Eigenschaften werden für Amazon S3 unter `location`-Einstellungen in einem formatbasierten Dataset unterstützt:

| Eigenschaft   | BESCHREIBUNG                                                  | Erforderlich |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Die **type**-Eigenschaft unter `location` in einem Dataset muss auf **AmazonS3Location** festgelegt werden. | Ja      |
| bucketName | Der Name des S3-Buckets.                                          | Ja      |
| folderPath | Der Pfad zum Ordner unter dem angegebenen Bucket. Wenn Sie einen Platzhalter verwenden möchten, um den Ordner zu filtern, überspringen Sie diese Einstellung, und geben Sie dies in den entsprechenden Aktivitätsquelleneinstellungen an. | Nein       |
| fileName   | Der Name der Datei unter dem angegebenen Bucket und Ordnerpfad. Wenn Sie einen Platzhalter verwenden möchten, um Dateien zu filtern, überspringen Sie diese Einstellung, und geben Sie dies in den entsprechenden Aktivitätsquelleneinstellungen an. | Nein       |
| version | Die Version des S3-Objekts, wenn die S3-Versionsverwaltung aktiviert ist. Wenn dies nicht angegeben ist, wird die neueste Version abgerufen. |Nein |

**Beispiel:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AmazonS3Location",
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

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die die Amazon S3-Quelle unterstützt.

### <a name="amazon-s3-as-a-source-type"></a>Amazon S3 als Quelltyp

[!INCLUDE [data-factory-v2-file-formats](includes/data-factory-v2-file-formats.md)] 

Folgende Eigenschaften werden für Amazon S3 unter `storeSettings`-Einstellungen in einer formatbasierten Kopierquelle unterstützt:

| Eigenschaft                 | BESCHREIBUNG                                                  | Erforderlich                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | Die **type**-Eigenschaft unter `storeSettings` muss auf **AmazonS3ReadSettings** festgelegt werden. | Ja                                                         |
| ***Suchen Sie die zu kopierenden Dateien:*** |  |  |
| OPTION 1: statischer Pfad<br> | Kopieren Sie aus dem im Dataset angegebenen Bucket oder Ordner/Dateipfad. Wenn Sie alle Dateien aus einem Bucket oder Ordner kopieren möchten, geben Sie zusätzlich für `wildcardFileName` den Wert `*` an. |  |
| OPTION 2: S3-Präfix<br>– prefix | Präfix für den S3-Schlüsselnamen unter dem angegebenen Bucket, konfiguriert in einem Dataset zum Filtern von S3-Quelldateien. Es werden die S3-Schlüssel ausgewählt, deren Namen mit `bucket_in_dataset/this_prefix` beginnen. Es wird der dienstseitige Filter von S3 verwendet, dessen Leistung im Vergleich zu Platzhalterfiltern besser ist.<br/><br/>Wenn Sie das Präfix verwenden und in eine dateibasierte Senke mit Beibehaltung der Hierarchie kopieren, wird der Unterpfad nach dem letzten „/“ im Präfix beibehalten. Wenn Sie beispielsweise `bucket/folder/subfolder/file.txt` als Quelle haben und das Präfix als `folder/sub` konfigurieren, lautet der beibehaltene Dateipfad `subfolder/file.txt`. | Nein |
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
        "name": "CopyFromAmazonS3",
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
                    "type": "AmazonS3ReadSettings",
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

## <a name="preserve-metadata-during-copy"></a>Beibehalten von Metadaten beim Kopieren

Beim Kopieren von Dateien von Amazon S3 nach Azure Data Lake Storage Gen2 oder Azure Blob Storage können Sie festlegen, dass die Dateimetadaten zusätzlich zu den Daten beibehalten werden sollen. Weitere Informationen finden Sie unter [Beibehalten von Metadaten](copy-activity-preserve-metadata.md#preserve-metadata).

## <a name="mapping-data-flow-properties"></a>Eigenschaften von Mapping Data Flow

Wenn Sie Daten in Zuordnungsdatenflüsse transformieren, können Sie Dateien aus Amazon S3 in den folgenden Formaten lesen:

- [Avro](format-avro.md#mapping-data-flow-properties)
- [Text mit Trennzeichen](format-delimited-text.md#mapping-data-flow-properties)
- [Delta](format-delta.md#mapping-data-flow-properties)
- [Excel](format-excel.md#mapping-data-flow-properties)
- [JSON](format-json.md#mapping-data-flow-properties)
- [Parquet](format-parquet.md#mapping-data-flow-properties)

Formatspezifische Einstellungen finden Sie in der Dokumentation für das jeweilige Format. Weitere Informationen finden Sie im Artikel zur [Quelltransformation im Zuordnungsdatenfluss](data-flow-source.md).

> [!NOTE]
> Die Amazon S3-Quelltransformation wird jetzt nur im **Azure Synapse Analytics**-Arbeitsbereich unterstützt.

### <a name="source-transformation"></a>Quellentransformation

Bei der Quelltransformation können Sie in Amazon S3 Daten aus einem Container, Ordner oder einer einzelnen Datei lesen. Über die Registerkarte **Source options** (Quellenoptionen) können Sie verwalten, wie die Dateien gelesen werden. 

:::image type="content" source="media/data-flow/sourceOptions1.png" alt-text="Screenshot der Quelloptionen":::

**Platzhalterpfade:** Mithilfe eines Platzhaltermusters wird der Dienst angewiesen, die einzelnen übereinstimmenden Ordner und Dateien in einer einzigen Quelltransformation zu durchlaufen. Dies ist eine effektive Methode zur Verarbeitung von mehreren Dateien in einem einzigen Datenfluss. Über das Pluszeichen (+), das angezeigt wird, wenn Sie mit dem Cursor auf Ihr vorhandenes Platzhaltermuster zeigen, können Sie weitere Platzhaltermuster hinzufügen.

Wählen Sie in Ihrem Quellcontainer eine Reihe von Dateien aus, die einem Muster entsprechen. Es kann nur ein Container im Dataset angegeben werden. Daher muss Ihr Platzhalterpfad auch den Ordnerpfad des Stammordners enthalten.

Beispiele für Platzhalter:

- `*`: stellt eine beliebige Zeichenfolge dar
- `**`: stellt eine rekursive Verzeichnisschachtelung dar
- `?`: ersetzt ein Zeichen
- `[]`: stimmt mit mindestens einem Zeichen in den Klammern überein

- `/data/sales/**/*.csv`: ruft alle CSV-Dateien unter „/data/sales“ ab
- `/data/sales/20??/**/`: ruft alle Dateien aus dem 20. Jahrhundert ab
- `/data/sales/*/*/*.csv`: ruft CSV-Dateien auf zwei Ebenen unter „/data/sales“ ab
- `/data/sales/2004/*/12/[XY]1?.csv`: ruft alle CSV-Dateien von Dezember 2004 ab, die mit X oder Y und einer zweistelligen Zahl als Präfix beginnen

**Partitionsstammpfad:** Wenn Ihre Dateiquelle partitionierte Ordner mit dem Format `key=value` (z. B. `year=2019`) enthält, können Sie die oberste Ebene dieser Ordnerstruktur einem Spaltennamen im Datenstrom Ihres Datenflusses zuweisen.

Legen Sie zunächst einen Platzhalter fest, um darin alle Pfade, die die partitionierten Ordner sind, sowie die Blattdateien einzuschließen, die gelesen werden sollen.

:::image type="content" source="media/data-flow/partfile2.png" alt-text="Screenshot der Einstellungen der Partitionsquelledatei":::

Verwenden Sie die Einstellung **Partition root path** (Partitionsstammpfad), um zu definieren, was die oberste Ebene der Ordnerstruktur ist. Wenn Sie die Inhalte Ihrer Daten über die Datenvorschau anzeigen, sehen Sie, dass der Dienst die aufgelösten Partitionen hinzufügen wird, die auf Ihren einzelnen Ordnerebenen gefunden werden.

:::image type="content" source="media/data-flow/partfile1.png" alt-text="Screenshot der Partitionierungsoptionen":::

**Liste der Dateien**: Dies ist eine Dateigruppe. Erstellen Sie eine Textdatei mit einer Liste der relativen Pfade der zu verarbeitenden Dateien. Verweisen Sie auf diese Textdatei.

**Spalte für die Speicherung im Dateinamen**: Speichern Sie den Namen der Quelldatei in einer Spalte in den Daten. Geben Sie hier einen neuen Spaltennamen ein, um die Zeichenfolge für den Dateinamen zu speichern.

**Nach der Fertigstellung**: Wählen Sie aus, ob Sie nach dem Ausführen des Datenflusses nichts mit der Quelldatei anstellen, die Quelldatei löschen oder die Quelldateien verschieben möchten. Die Pfade für das Verschieben sind relative Pfade.

Um Quelldateien an einen anderen Speicherort nach der Verarbeitung zu verschieben, wählen Sie zuerst für den Dateivorgang die Option „Verschieben“ aus. Legen Sie dann das Quellverzeichnis („from“/„aus“) fest. Wenn Sie keine Platzhalter für Ihren Pfad verwenden, entspricht die Einstellung „from“ dem Quellordner.

Wenn Sie über einen Quellpfad mit Platzhalter verfügen, sieht Ihre Syntax ähnlich wie hier aus:

`/data/sales/20??/**/*.csv`

Geben Sie „from“ beispielsweise wie folgt an:

`/data/sales`

„To“ können Sie wie folgt angeben:

`/backup/priorSales`

In diesem Fall werden alle Dateien, die aus `/data/sales` erstellt wurden, in `/backup/priorSales` verschoben.

> [!NOTE]
> Die Dateivorgänge werden nur ausgeführt, wenn der Datenfluss anhand der Aktivität zum Ausführen des Datenflusses in einer Pipeline über eine Pipelineausführung ausgeführt wird (Debuggen der Pipeline oder Ausführung). Dateivorgänge werden *nicht* im Datenfluss-Debugmodus ausgeführt.

**Nach der letzten Änderung filtern**: Sie können einen Datumsbereich angeben, um die zu verarbeitenden Dateien nach der letzten Änderung zu filtern. Alle Datums-/Uhrzeitangaben erfolgen in UTC. 

## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [Lookup-Aktivität](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Eigenschaften der GetMetadata-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [GetMetadata-Aktivität](control-flow-get-metadata-activity.md). 

## <a name="delete-activity-properties"></a>Eigenschaften der Delete-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [Delete-Aktivität](delete-activity.md).

## <a name="legacy-models"></a>Legacy-Modelle

>[!NOTE]
>Die folgenden Modelle werden aus Gründen der Abwärtskompatibilität weiterhin unverändert unterstützt. Es wird empfohlen, das oben erwähnte neue Modell zu verwenden. Die Erstellungsbenutzeroberfläche für die Erstellung ist zum Generieren des neuen Modells gewechselt.

### <a name="legacy-dataset-model"></a>Legacy-Datasetmodell

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft des Datasets muss auf **AmazonS3Object** festgelegt werden. |Ja |
| bucketName | Der Name des S3-Buckets. Der Platzhalterfilter wird nicht unterstützt. |„Ja“ für die Kopier- oder Lookup-Aktivität, „Nein“ für die GetMetadata-Aktivität. |
| Schlüssel | Der Name oder Platzhalterfilter des S3-Objektschlüssels unter dem angegebenen Bucket. Ist nur anwendbar, wenn die **prefix**-Eigenschaft nicht angegeben ist. <br/><br/>Der Platzhalterfilter wird sowohl für den Ordner- als auch für den Dateinamen unterstützt. Folgende Platzhalter sind zulässig: `*` (entspricht null [0] oder mehr Zeichen) und `?` (entspricht null [0] oder einem einzelnen Zeichen).<br/>- Beispiel 1: `"key": "rootfolder/subfolder/*.csv"`<br/>- Beispiel 2: `"key": "rootfolder/subfolder/???20180427.txt"`<br/>Weitere Beispiele finden Sie unter [Beispiele für Ordner- und Dateifilter](#folder-and-file-filter-examples). Verwenden Sie `^` als Escapezeichen, wenn der tatsächliche Ordner- oder Dateiname einen Platzhalter oder dieses Escapezeichen enthält. |Nein |
| prefix | Präfix für den S3-Objektschlüssel. Objekte, deren Schlüssel mit diesem Präfix beginnen, werden ausgewählt. Ist nur anwendbar, wenn die **key**-Eigenschaft nicht angegeben ist. |Nein |
| version | Die Version des S3-Objekts, wenn die S3-Versionsverwaltung aktiviert ist. Wenn keine Version angegeben ist, wird die neueste Version abgerufen. |Nein |
| modifiedDatetimeStart | Die Dateien werden anhand des Attributs „Letzte Änderung“ gefiltert. Die Dateien werden ausgewählt, wenn der Zeitpunkt der letzten Änderung innerhalb des Zeitbereichs zwischen `modifiedDatetimeStart` und `modifiedDatetimeEnd` liegt. Die Zeit wird auf die UTC-Zeitzone im Format „2018-12-01T05:00:00Z“ angewandt. <br/><br/> Hinweis: Die Aktivierung dieser Einstellung wirkt sich auf die Gesamtleistung der Datenverschiebung aus, wenn Sie große Dateimengen filtern möchten. <br/><br/> Die Eigenschaften können **NULL** sein, was bedeutet, dass kein Dateiattributfilter auf das Dataset angewendet wird.  Wenn `modifiedDatetimeStart` einen datetime-Wert aufweist, aber `modifiedDatetimeEnd` **NULL** ist, werden die Dateien ausgewählt, deren Attribut für die letzte Änderung größer oder gleich dem datetime-Wert ist.  Wenn `modifiedDatetimeEnd` den datetime-Wert aufweist, aber `modifiedDatetimeStart` NULL ist, werden die Dateien ausgewählt, deren Attribut für die letzte Änderung kleiner als der datetime-Wert ist.| Nein |
| modifiedDatetimeEnd | Die Dateien werden anhand des Attributs „Letzte Änderung“ gefiltert. Die Dateien werden ausgewählt, wenn der Zeitpunkt der letzten Änderung innerhalb des Zeitbereichs zwischen `modifiedDatetimeStart` und `modifiedDatetimeEnd` liegt. Die Zeit wird auf die UTC-Zeitzone im Format „2018-12-01T05:00:00Z“ angewandt. <br/><br/> Hinweis: Die Aktivierung dieser Einstellung wirkt sich auf die Gesamtleistung der Datenverschiebung aus, wenn Sie große Dateimengen filtern möchten. <br/><br/> Die Eigenschaften können **NULL** sein, was bedeutet, dass kein Dateiattributfilter auf das Dataset angewendet wird.  Wenn `modifiedDatetimeStart` einen datetime-Wert aufweist, aber `modifiedDatetimeEnd` **NULL** ist, werden die Dateien ausgewählt, deren Attribut für die letzte Änderung größer oder gleich dem datetime-Wert ist.  Wenn `modifiedDatetimeEnd` den datetime-Wert aufweist, aber `modifiedDatetimeStart` **NULL** ist, werden die Dateien ausgewählt, deren Attribut für die letzte Änderung kleiner als der datetime-Wert ist.| Nein |
| format | Wenn Sie Dateien unverändert zwischen dateibasierten Speichern kopieren möchten (binäre Kopie), können Sie den Formatabschnitt bei den Definitionen von Eingabe- und Ausgabedatasets überspringen.<br/><br/>Für das Analysieren oder Generieren von Dateien mit einem bestimmten Format werden die folgenden Dateiformattypen unterstützt: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** und **ParquetFormat**. Sie müssen die **type**-Eigenschaft unter **format** auf einen dieser Werte festlegen. Weitere Informationen finden Sie in den Abschnitten [Textformat](supported-file-formats-and-compression-codecs-legacy.md#text-format), [JSON-Format](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro-Format](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Orc-Format](supported-file-formats-and-compression-codecs-legacy.md#orc-format) und [Parquet-Format](supported-file-formats-and-compression-codecs-legacy.md#parquet-format). |Nein (nur für Szenarien mit Binärkopien) |
| compression | Geben Sie den Typ und den Grad der Komprimierung für die Daten an. Weitere Informationen finden Sie unter [Unterstützte Dateiformate und Codecs für die Komprimierung](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Unterstützte Typen sind **GZip**, **Deflate**, **BZIP2** und **ZipDeflate**.<br/>Unterstützte Grade sind **Optimal** und **Schnellste**. |Nein |

>[!TIP]
>Um alle Dateien in einem Ordner zu kopieren, geben Sie **bucketName** für den Bucket und **prefix** für den Ordner an.
>
>Um eine einzelne Datei mit einem angegebenen Namen zu kopieren, geben Sie **bucketName** für den Bucket und **key** für den Ordner mit dem Dateinamen an.
>
>Um eine Teilmenge der Dateien in einem Ordner zu kopieren, geben Sie **bucketName** für den Bucket und **key** für den Ordner mit dem Platzhalterfilter an.

**Beispiel: Verwenden eines Präfixes**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "prefix": "testFolder/test",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

**Beispiel: Verwenden eines Schlüssels und einer Version (optional)**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "key": "testFolder/testfile.csv.gz",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

### <a name="legacy-source-model-for-the-copy-activity"></a>Legacy-Quellmodell für die Kopieraktivität

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft der Quelle der Kopieraktivität muss auf **FileSystemSource** festgelegt werden. |Ja |
| recursive | Gibt an, ob die Daten rekursiv aus den Unterordnern oder nur aus dem angegebenen Ordner gelesen werden. Beachten Sie Folgendes: Wenn **recursive** auf **true** festgelegt ist und es sich bei der Senke um einen dateibasierten Speicher handelt, wird ein leerer Ordner oder Unterordner nicht in die Senke kopiert und dort auch nicht erstellt.<br/>Zulässige Werte sind **true** (Standard) und **false**. | Nein |
| maxConcurrentConnections |Die Obergrenze gleichzeitiger Verbindungen mit dem Datenspeicher während der Aktivitätsausführung. Geben Sie diesen Wert nur an, wenn Sie die Anzahl der gleichzeitigen Verbindungen begrenzen möchten.| Nein |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Amazon S3 input dataset name>",
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
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die diese Kopieraktivität als Quellen und Senken unterstützt, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).
