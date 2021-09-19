---
title: Blobspeicheranforderungen für Microsoft Azure Data Box | Microsoft-Dokumentation
description: Lernen Sie die unterstützten Versionen von APIs, SDKs und Clientbibliotheken für Azure Data Box-Blobspeicher kennen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 10/05/2020
ms.author: alkohli
ms.openlocfilehash: 4c62c1e854e55b8817724c78bc325badc703bc27
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123250671"
---
# <a name="azure-data-box-blob-storage-requirements"></a>Blobspeicheranforderungen für Azure Data Box

In diesem Artikel werden die Versionen der Azure-APIs, -Clientbibliotheken und -Tools aufgeführt, die mit dem Data Box-Blobspeicher unterstützt werden. Der Data Box-Blobspeicher bietet Blobverwaltungsfunktionen mit einer in Azure konsistenten Semantik. Dieser Artikel bietet auch eine Übersicht über die bekannten Unterschiede zwischen Azure Data Box-Blobspeicher und Azure Storage-Diensten.

Sie sollten die Informationen sorgfältig lesen, bevor Sie eine Verbindung mit dem Data Box-Blobspeicher herstellen. Auch später sollten Sie bei Bedarf als Referenz darauf zurückgreifen.


## <a name="storage-differences"></a>Speicherunterschiede

|     Funktion                                             |     Azure Storage                                     |     Data Box-Blobspeicher |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Azure Files                                   |    Cloudbasierte SMB- und NFS-Dateifreigaben werden unterstützt               |    Nicht unterstützt      |
|    Dienstverschlüsselung für ruhende Daten                  |    256-Bit-AES-Verschlüsselung                             |    256-Bit-AES-Verschlüsselung |
|    Speicherkontotyp                                 |    Allgemeine Speicherkonten und Azure Blob Storage-Konten    |    Nur Konten vom Typ „Universell V1“|
|    Blobname                                            |    1\.024 Zeichen (2.048 Bytes)                     |    880 Zeichen (1.760 Bytes)|
|    Maximale Blockblobgröße                              |    4,75 TiB (100 MB × 50.000 Blöcke)                   |    4,75 TiB (100 MB × 50.000 Blöcke) für Azure Data Box ab Version 3.0.|
|    Maximale Seitenblobgröße                               |    8 TiB                                               |    1TiB                   |
|    Seitenblob – Seitengröße                                  |    512 Bytes                                          |    4 KiB                   |

## <a name="supported-api-versions"></a>Unterstützte API-Versionen

Die folgenden Versionen der Azure Storage-Dienst-APIs werden mit Data Box-Blobspeicher unterstützt.

### <a name="azure-data-box-30-onwards"></a>Azure Data Box ab Version 3.0

[!INCLUDE [data-box-rest-supported-api-versions](../../includes/data-box-rest-supported-api-versions.md)]

## <a name="supported-azure-client-libraries"></a>Unterstützte Azure-Clientbibliotheken

Für Data Box-Blobspeicher sind bestimmte Clientbibliotheken vorhanden, und es gelten bestimmte Anforderungen an Endpunktsuffixe. Die Data Box-Blobspeicher-Endpunkte sind der neuesten Version der REST-API für Azure Blob Storage nicht vollständig gleichgestellt, siehe [Unterstützte Versionen für Azure Data Box ab Version 3.0](#supported-api-versions). Bei den Speicherclientbibliotheken müssen Sie auf die Version achten, die mit der REST-API kompatibel ist.

### <a name="azure-data-box-30-onwards"></a>Azure Data Box ab Version 3.0

Die folgenden Azure-Clientbibliotheksversionen werden für Data Box-Blobspeicher unterstützt.

[!INCLUDE [data-box-rest-supported-azure-client-libraries](../../includes/data-box-rest-supported-azure-client-libraries.md)]

### <a name="install-php-client-via-composer---current"></a>Installation des PHP-Clients per Composer – Aktuell

Installation per Composer: (Blob als Beispiel verwenden).
1. Erstellen Sie mit folgendem Code eine Datei mit dem Namen „composer.json“ im Stammverzeichnis des Projekts:

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. Laden Sie `composer.phar` in das Stammverzeichnis des Projekts herunter.

3. Führen Sie „php composer.phar install“ aus.

### <a name="endpoint-declaration"></a>Endpunktdeklaration

Im Data Box-Blobspeicher-SDK identifiziert das Endpunktsuffix – `<device serial number>.microsoftdatabox.com` – die Data Box-Domäne. Weitere Informationen zum Blobdienstendpunkt finden Sie unter [Herstellen einer Verbindung über Data Box-Blobspeicher](data-box-deploy-copy-data-via-rest.md).
 
## <a name="examples"></a>Beispiele

### <a name="net"></a>.NET

Das Endpunktsuffix für Data Box-Blobspeicher wird in der `app.config`-Datei angegeben:

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

Das Endpunktsuffix für Data Box-Blobspeicher wird beim Einrichten der Verbindungszeichenfolge angegeben:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

Das Endpunktsuffix für Data Box-Blobspeicher wird in der Deklarationsinstanz angegeben:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

Das Endpunktsuffix für Data Box-Blobspeicher wird beim Einrichten der Verbindungszeichenfolge angegeben:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

Das Endpunktsuffix für Data Box-Blobspeicher wird beim Einrichten der Verbindungszeichenfolge angegeben:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Das Endpunktsuffix für Data Box-Blobspeicher wird in der Deklarationsinstanz angegeben:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

Das Endpunktsuffix für Data Box-Blobspeicher wird beim Einrichten der Verbindungszeichenfolge angegeben:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen Ihrer Azure Data Box](data-box-deploy-ordered.md)
