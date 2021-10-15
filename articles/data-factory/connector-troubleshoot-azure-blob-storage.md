---
title: Problembehandlung für den Azure Blob Storage-Connector
titleSuffix: Azure Data Factory & Azure Synapse
description: Erfahren Sie, wie Sie Probleme mit dem Azure Blob Storage-Connector in Azure Data Factory und Azure Synapse Analytics behandeln.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: e9a1c085e3d1d88d0897d46b924c5a9a21c747a2
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390519"
---
# <a name="troubleshoot-the-azure-blob-storage-connector-in-azure-data-factory-and-azure-synapse"></a>Problembehandlung für den Azure Blob Storage-Connector in Azure Data Factory und Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dieser Artikel enthält Vorschläge zur Behandlung häufiger Probleme mit dem Azure Blob Storage-Connector in Azure Data Factory und Azure Synapse.

## <a name="error-code-azurebloboperationfailed"></a>Fehlercode: AzureBlobOperationFailed

- **Meldung:** „Fehler beim Blobvorgang. ContainerName: %containerName;, Pfad: %path;.“

- **Ursache:** Problem beim Speichervorgang für Blobs.

- **Empfehlung**:  Informationen zum Überprüfen der Fehlerdetails finden Sie unter [Blob Storage-Fehlercodes](/rest/api/storageservices/blob-service-error-codes). Wenden Sie sich an das Blob Storage-Team, um weitere Hilfe zu erhalten.


## <a name="invalid-property-during-copy-activity"></a>Ungültige Eigenschaft während der Copy-Aktivität

- **Meldung**: `Copy activity \<Activity Name> has an invalid "source" property. The source type is not compatible with the dataset \<Dataset Name> and its linked service \<Linked Service Name>. Please verify your input against.`

- **Ursache:** Der im Dataset definierte Typ stimmt nicht mit dem in der Copy-Aktivität definierten Quell- oder Senkentyp überein.

- **Lösung:** Bearbeiten Sie die JSON-Definition für das Dataset oder die Pipeline, um die Typen zu vereinheitlichen, und führen Sie die Bereitstellung dann erneut aus.

## <a name="error-code-fipsmodeisnotsupport"></a>Fehlercode: FIPSModeIsNotSupport

- **Meldung**: `Fail to read data form Azure Blob Storage for Azure Blob connector needs MD5 algorithm which can't co-work with FIPS mode. Please change diawp.exe.config in self-hosted integration runtime install directory to disable FIPS policy following https://docs.microsoft.com/en-us/dotnet/framework/configure-apps/file-schema/runtime/enforcefipspolicy-element.`

- **Ursache**: Die FIPS-Richtlinie ist auf dem virtuellen Computer aktiviert, auf dem die selbstgehostete Integration Runtime installiert wurde.

- **Empfehlung**: Deaktivieren Sie den FIPS-Modus auf dem virtuellen Computer, auf dem die selbstgehostete Integration Runtime installiert wurde. Unter Windows wird der FIPS-Modus nicht empfohlen.

## <a name="error-code-azureblobinvalidblocksize"></a>Fehlercode: AzureBlobInvalidBlockSize

- **Meldung**: `Block size should between %minSize; MB and 100 MB.`

- **Ursache**: Die Blockgröße überschreitet die maximale Blobgröße.

## <a name="error-code-azurestorageoperationfailedconcurrentwrite"></a>Fehlercode: AzureStorageOperationFailedConcurrentWrite

- **Meldung**: `Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`

- **Ursache**: Sie verfügen über mehrere gleichzeitige Kopieraktivitätsausführungen oder Anwendungen, die in dieselbe Datei schreiben.

## <a name="error-code-azureappendblobconcurrentwriteconflict"></a>Fehlercode: AzureAppendBlobConcurrentWriteConflict

- **Meldung**: `Detected concurrent write to the same append blob file, it's possible because you have multiple concurrent copy activities runs or applications writing to the same file '%name;'. Please check your ADF configuration and retry.`

- **Ursache**: Es treten mehrere gleichzeitige Schreibanforderungen auf, die Konflikte beim Dateiinhalt verursachen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Problembehandlung finden Sie in diesen Ressourcen:

- [Leitfaden zur Problembehandlung für Connectors](connector-troubleshoot-guide.md)
- [Data Factory-Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory-Funktionsanfragen](/answers/topics/azure-data-factory.html)
- [Azure-Videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Q&A-Seite von Microsoft](/answers/topics/azure-data-factory.html)
- [Stack Overflow-Forum für Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Twitter-Informationen über Data Factory](https://twitter.com/hashtag/DataFactory)
