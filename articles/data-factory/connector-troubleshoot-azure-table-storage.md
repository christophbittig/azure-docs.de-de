---
title: Problembehandlung für den Azure Table Storage-Connector
titleSuffix: Azure Data Factory & Azure Synapse
description: Erfahren Sie, wie Sie Probleme mit dem Azure Table Storage-Connector in Azure Data Factory und Azure Synapse Analytics behandeln.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: e99a31f4ca0380d8bbc12f941248d745e0c7f74c
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390515"
---
# <a name="troubleshoot-the-azure-table-storage-connector-in-azure-data-factory-and-azure-synapse"></a>Problembehandlung für den Azure Table Storage-Connector in Azure Data Factory und Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dieser Artikel enthält Vorschläge zur Behandlung häufiger Probleme mit dem Azure Table Storage-Connector in Azure Data Factory und Azure Synapse.

## <a name="error-code-azuretableduplicatecolumnsfromsource"></a>Fehlercode: AzureTableDuplicateColumnsFromSource

- **Meldung**: `Duplicate columns with same name '%name;' are detected from source. This is NOT supported by Azure Table Storage sink.`

- **Ursache:** Doppelte Quellspalten können aus einem der folgenden Gründe auftreten:
   * Sie verwenden die Datenbank als Quelle und haben Tabellenjoins angewendet.
   * Sie verfügen über unstrukturierte CSV-Dateien mit doppelten Spaltennamen in der Kopfzeile.

- **Empfehlung**:  Überprüfen Sie die Quellspalten, und beheben Sie ggf. Probleme.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Problembehandlung finden Sie in diesen Ressourcen:

- [Leitfaden zur Problembehandlung für Connectors](connector-troubleshoot-guide.md)
- [Data Factory-Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory-Funktionsanfragen](/answers/topics/azure-data-factory.html)
- [Azure-Videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Q&A-Seite von Microsoft](/answers/topics/azure-data-factory.html)
- [Stack Overflow-Forum für Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Twitter-Informationen über Data Factory](https://twitter.com/hashtag/DataFactory)
