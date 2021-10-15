---
title: Problembehandlung für den Azure Files-Connector
titleSuffix: Azure Data Factory & Azure Synapse
description: Hier erfahren Sie, wie Sie Probleme mit dem Azure Files-Connector in Azure Data Factory und Azure Synapse Analytics behandeln.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 809bf30f1e11eaa7741ac0581881479d854ed4cb
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390530"
---
# <a name="troubleshoot-the-azure-files-connector-in-azure-data-factory-and-azure-synapse"></a>Problembehandlung für den Azure Files-Connector in Azure Data Factory und Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dieser Artikel enthält Vorschläge zur Behandlung häufiger Probleme mit dem Azure Files-Connector in Azure Data Factory und Azure Synapse.

## <a name="error-code-azurefileoperationfailed"></a>Fehlercode: AzureFileOperationFailed

- **Meldung**: `Azure File operation Failed. Path: %path;. ErrorMessage: %msg;.`

- **Ursache:** Problem beim Speichervorgang von Azure Files.

- **Empfehlung**:  Informationen zum Überprüfen der Fehlerdetails finden Sie unter [Azure Files-Hilfe](/rest/api/storageservices/file-service-error-codes). Um weitere Unterstützung zu erhalten, nehmen Sie Kontakt mit dem Azure Files-Team auf.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Problembehandlung finden Sie in diesen Ressourcen:

- [Leitfaden zur Problembehandlung für Connectors](connector-troubleshoot-guide.md)
- [Data Factory-Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory-Funktionsanfragen](/answers/topics/azure-data-factory.html)
- [Azure-Videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Q&A-Seite von Microsoft](/answers/topics/azure-data-factory.html)
- [Stack Overflow-Forum für Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Twitter-Informationen über Data Factory](https://twitter.com/hashtag/DataFactory)
