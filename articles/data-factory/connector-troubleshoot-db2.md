---
title: Problembehandlung mit dem DB2-Connector
titleSuffix: Azure Data Factory & Azure Synapse
description: Erfahren Sie, wie Sie Probleme mit dem DB2-Connector in Azure Data Factory und Azure Synapse Analytics behandeln.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: f2a9e79fb3c01938eae52b6c34687943a9472024
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390514"
---
# <a name="troubleshoot-the-db2-connector-in-azure-data-factory-and-azure-synapse"></a>Problembehandlung für den DB2-Connector in Azure Data Factory und Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dieser Artikel enthält Vorschläge zur Behandlung häufiger Probleme mit dem Azure Database for PostgreSQL-Connector in Azure Data Factory und Azure Synapse.

## <a name="error-code-db2driverrunfailed"></a>Fehlercode: DB2DriverRunFailed

- **Meldung**: `Error thrown from driver. Sql code: '%code;'`

- **Ursache**: Wenn die Fehlermeldung die Zeichenfolge „SQLSTATE=51002 SQLCODE=-805“ enthält, befolgen Sie den Tipp unter [Copy data from DB2](./connector-db2.md#linked-service-properties) (Kopieren von Daten aus DB2).

- **Empfehlung**:  Versuchen Sie, „NULLID“ in der `packageCollection`-Eigenschaft festzulegen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Problembehandlung finden Sie in diesen Ressourcen:

- [Leitfaden zur Problembehandlung für Connectors](connector-troubleshoot-guide.md)
- [Data Factory-Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory-Funktionsanfragen](/answers/topics/azure-data-factory.html)
- [Azure-Videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Q&A-Seite von Microsoft](/answers/topics/azure-data-factory.html)
- [Stack Overflow-Forum für Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Twitter-Informationen über Data Factory](https://twitter.com/hashtag/DataFactory)
