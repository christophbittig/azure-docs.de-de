---
title: Erhaltung von Metadaten und ACLs durch Kopiervorgänge
description: Erfahren Sie, wie Sie Metadaten und ACLs beibehalten, wenn Sie die Kopieraktivität in Azure Data Factory und Synapse Analytics-Pipelines verwenden.
titleSuffix: Azure Data Factory & Azure Synapse
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: jianleishen
ms.openlocfilehash: 64608834c5d5b22383242f6739747d955e2feb3a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124767373"
---
#  <a name="preserve-metadata-and-acls-using-copy-activity-in-azure-data-factory-or-synapse-analytics"></a>Erhaltung von Metadaten und ACLs durch Kopieraktivitäten in Azure Data Factory oder Synapse Analytics

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Wenn Sie die Azure Data Factory- oder Synapse Analytics-Pipelines-Kopieraktivität verwenden, um Daten von der Quelle zur Senke zu kopieren, können Sie in den folgenden Szenarien auch die Metadaten und ACLs mitnehmen.

## <a name="preserve-metadata-for-lake-migration"></a><a name="preserve-metadata"></a> Beibehalten von Metadaten für die Lake-Migration

Wenn Sie Daten von einem Data Lake zu einem anderen migrieren, einschließlich [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) und [Azure Files](connector-azure-file-storage.md), können Sie wählen, ob die Metadaten der Dateien zusammen mit den Daten erhalten bleiben sollen.

Die Kopieraktivität unterstützt die Beibehaltung der folgenden Attribute beim Kopieren von Daten:

- **Alle vom Kunden angegebenen Metadaten** 
- Und die folgenden **fünf integrierten Systemeigenschaften des Datenspeichers**: `contentType`, `contentLanguage` (mit Ausnahme von Amazon S3) `contentEncoding`, `contentDisposition`, `cacheControl`.

**Behandeln von Unterschieden in Metadaten:** Amazon S3 und Azure Storage erlauben unterschiedliche Zeichensätze in den Schlüsseln der vom Kunden angegebenen Metadaten. Wenn Sie sich dafür entscheiden, Metadaten mit Hilfe von Kopiervorgängen zu erhalten, ersetzt der Dienst die ungültigen Zeichen automatisch durch "_".

Wenn Sie Dateien unverändert von Amazon S3/Azure Data Lake Storage Gen2/Azure Blob/Azure File Storage in Azure Data Lake Storage Gen2/Azure Blob/Azure File Storage mit Binärformat kopieren, finden Sie die dazu erforderliche Option **Beibehalten** auf der Registerkarte **Kopieraktivität** > **Einstellungen** für die Aktivitätserstellung oder auf der Seite **Einstellungen** im Tool zum Kopieren von Daten.

:::image type="content" source="./media/copy-activity-preserve-metadata/copy-activity-preserve-metadata.png" alt-text="Kopieraktivität mit Beibehaltung von Metadaten":::

Hier ist ein Beispiel für die JSON-Konfiguration der Kopieraktivität (siehe `preserve`): 

```json
"activities":[
    {
        "name": "CopyAndPreserveMetadata",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AmazonS3ReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings": {
                    "type": "AzureBlobFSWriteSettings"
                }
            },
            "preserve": [
                "Attributes"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Binary dataset Amazon S3/Azure Blob/ADLS Gen2 source>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Binary dataset for Azure Blob/ADLS Gen2 sink>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="preserve-acls-from-data-lake-storage-gen1gen2-to-gen2"></a><a name="preserve-acls"></a> Beibehalten von Zugriffssteuerungslisten aus Data Lake Storage Gen1/Gen2 in Gen2

Beim Upgrade von Azure Data Lake Storage Gen1 auf Gen2 oder beim Kopieren von Daten zwischen ADLS Gen2 können Sie auswählen, dass die POSIX-Zugriffssteuerungslisten (Access Control Lists, ACLs) zusammen mit den Datendateien beibehalten werden sollen. Weitere Informationen zur Zugriffssteuerung finden Sie unter [Zugriffssteuerung in Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md) und [Zugriffssteuerung in Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

Die Kopieraktivität unterstützt die Beibehaltung der folgenden Arten von Zugriffssteuerungslisten beim Kopieren von Daten. Sie können einen oder mehrere Typen auswählen:

- **ACL**: Kopieren und Beibehalten von POSIX-Zugriffssteuerungslisten für Dateien und Verzeichnisse. Hierbei werden die vollständigen vorhandenen ACLs von der Quelle in die Senke kopiert. 
- **Besitzer:** Kopieren und Beibehalten des besitzenden Benutzers von Dateien und Verzeichnissen. Hierfür ist Administratorzugriff auf die Data Lake Storage Gen2-Senke erforderlich.
- **Gruppe**: Kopieren und Beibehalten der besitzenden Gruppe von Dateien und Verzeichnissen. Hierfür ist Administratorzugriff auf die Data Lake Storage Gen2-Senke oder der Zugriff des besitzenden Benutzers erforderlich (sofern der besitzende Benutzer auch Mitglied der Zielgruppe ist).

Wenn Sie angeben, dass aus einem Ordner kopiert werden soll, repliziert der Dienst die ACLs für den betreffenden Ordner und die Dateien und Verzeichnisse darunter, wenn `recursive` auf true gesetzt ist. Falls Sie das Kopieren aus einer einzelnen Datei angeben, werden die ACLs in dieser Datei kopiert.

>[!NOTE]
>Wenn Sie den Kopiervorgang verwenden, um ACLs von Data Lake Storage Gen1/Gen2 auf Gen2 zu übertragen, werden die vorhandenen ACLs auf den entsprechenden Ordnern/Dateien von Sink Gen2 überschrieben.

>[!IMPORTANT]
>Wenn Sie sich für die Beibehaltung von ACLs entscheiden, stellen Sie sicher, dass Sie ausreichend hohe Berechtigungen gewähren, damit der Dienst mit Ihrem Sink Data Lake Storage Gen2-Konto arbeiten kann. Verwenden Sie beispielsweise die Kontoschlüsselauthentifizierung, oder weisen Sie dem Dienstprinzipal bzw. der verwalteten Identität die Rolle „Besitzer von Speicherblobdaten“ zu.

Wenn Sie die Quelle als Data Lake Storage Gen1/Gen2 mit Binärformat oder der binären Kopieroption und die Senke als Data Lake Storage Gen2 mit Binärformat oder der binären Kopieroption konfigurieren, finden Sie die Option **Beibehalten** auf der Seite **Einstellungen** im Tool zum Kopieren von Daten oder auf der Registerkarte **Kopieraktivität** > **Einstellungen** für die Aktivitätserstellung.

:::image type="content" source="./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png" alt-text="Data Lake Storage Gen1/Gen2 in Gen2 – Beibehalten der Zugriffssteuerungsliste (ACL)":::

Hier ist ein Beispiel für die JSON-Konfiguration der Kopieraktivität (siehe `preserve`): 

```json
"activities":[
    {
        "name": "CopyAndPreserveACLs",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AzureDataLakeStoreReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings": {
                    "type": "AzureBlobFSWriteSettings"
                }
            },
            "preserve": [
                "ACL",
                "Owner",
                "Group"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen1/Gen2 source>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen2 sink>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den anderen Artikeln zur Kopieraktivität:

- [Kopieraktivität – Übersicht](copy-activity-overview.md)
- [Leistung der Kopieraktivität](copy-activity-performance.md)
