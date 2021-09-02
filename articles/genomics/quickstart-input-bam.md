---
title: Übermitteln eines Workflows mit einer BAM-Eingabedatei
titleSuffix: Microsoft Genomics
description: In diesem Artikel erfahren Sie, wie Sie einen Workflow an den Microsoft Genomics-Dienst übermitteln, wenn es sich bei Ihrer Eingabedatei um eine einzelne BAM-Datei handelt.
services: genomics
author: vigunase
manager: cgronlun
ms.author: vigunase
ms.service: genomics
ms.topic: conceptual
ms.date: 12/07/2017
ms.openlocfilehash: 09e06e528f2e47338871d7a564b763534a363766
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123255760"
---
# <a name="submit-a-workflow-using-a-bam-file-input"></a>Übermitteln eines Workflows mit einer BAM-Eingabedatei

In diesem Artikel erfahren Sie, wie Sie einen Workflow an den Microsoft Genomics-Dienst übermitteln, wenn es sich bei Ihrer Eingabedatei um eine einzelne BAM-Datei handelt. In diesem Thema wird vorausgesetzt, dass Sie den `msgen`-Client bereits installiert und ausgeführt haben und mit der Verwendung von Azure Storage vertraut sind. Wenn Sie erfolgreich einen Workflow mit den bereitgestellten Beispieldaten übermittelt haben, können Sie mit diesem Artikel fortfahren. 

## <a name="set-up-upload-your-bam-file-to-azure-storage"></a>Einrichten: Hochladen Ihrer BAM-Datei in Azure Storage
Angenommen, Sie haben eine einzelne BAM-Datei (*reads.bam*) als **https://<span></span>myaccount.blob.core <span></span>.windows <span></span>.net <span></span>/inputs/reads <span></span>.bam <span></span>** in Ihr Azure-Speicherkonto *myaccount* hochgeladen. Sie verfügen über die API-URL und den Zugriffsschlüssel. Als Ausgabeziel möchten Sie **https://<span></span>myaccount.blob.core <span></span>.windows <span></span>.net <span></span>/outputs <span></span>** verwenden.



## <a name="submit-your-job-to-the-msgen-client"></a>Übermitteln Ihres Auftrags an den `msgen`-Client 


Der `msgen`-Client benötigt mindestens folgende Argumente. Zur besseren Übersichtlichkeit wurden Zeilenumbrüche eingefügt:

Windows:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads.bam ^
  --output-storage-account-name myaccount ^
  --output-storage-account-key <storage access key to "myaccount"> ^
  --output-storage-account-container outputs
```


Unix:

```
msgen submit \
  --api-url-base <Genomics API URL> \
  --access-key <Genomics access key> \
  --process-args R=b37m1 \
  --input-storage-account-name myaccount \
  --input-storage-account-key <storage access key to "myaccount"> \
  --input-storage-account-container inputs \
  --input-blob-name-1 reads.bam \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


Falls Sie lieber eine Konfigurationsdatei verwenden möchten, muss diese Folgendes enthalten:

``` config.txt
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads.bam
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

Übermitteln Sie die Datei `config.txt` mithilfe des folgenden Aufrufs: `msgen submit -f config.txt`

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie eine BAM-Datei in Azure Storage hochgeladen und einen Workflow über den `msgen`-Python-Client an den Microsoft Genomics-Dienst übermittelt. Weitere Informationen zur Workflowübermittlung sowie zu anderen Befehlen für den Microsoft Genomics-Dienst finden Sie in den [häufig gestellten Fragen](frequently-asked-questions-genomics.yml). 
