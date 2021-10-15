---
title: Problembehandlung für den Azure Database for PostgreSQL-Connector
titleSuffix: Azure Data Factory & Azure Synapse
description: Hier erfahren Sie, wie Sie Probleme mit dem Azure Database for PostgreSQL-Connector in Azure Data Factory und Azure Synapse Analytics behandeln.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 8613c6b17007f51caf437fb2d66d2d8e17965007
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390512"
---
# <a name="troubleshoot-the-azure-database-for-postgresql-connector-in-azure-data-factory-and-azure-synapse"></a>Problembehandlung für den Azure Database for PostgreSQL-Connector in Azure Data Factory und Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dieser Artikel enthält Vorschläge zur Behandlung häufiger Probleme mit dem Azure Database for PostgreSQL-Connector in Azure Data Factory und Azure Synapse.

## <a name="error-code-azurepostgresqlnpgsqldatatypenotsupported"></a>Fehlercode: AzurePostgreSqlNpgsqlDataTypeNotSupported

- **Meldung**: `The data type of the chosen Partition Column, '%partitionColumn;', is '%dataType;' and this data type is not supported for partitioning.`

- **Empfehlung**: Wählen Sie eine Partitionsspalte mit dem Datentyp „int“, „bigint“, „smallint“, „serial“, „bigserial“, „smallserial“, „timestamp with time zone“, „timestamp without time zone“, „time without time zone“ oder „date“.

## <a name="error-code-azurepostgresqlnpgsqlpartitioncolumnnamenotprovided"></a>Fehlercode: AzurePostgreSqlNpgsqlPartitionColumnNameNotProvided

- **Meldung**: `Partition column name must be specified.`

- **Ursache**: Es wurde kein Partitionsspaltenname angegeben, und es konnte nicht automatisch entschieden werden.
 
## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Problembehandlung finden Sie in diesen Ressourcen:

- [Leitfaden zur Problembehandlung für Connectors](connector-troubleshoot-guide.md)
- [Data Factory-Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory-Funktionsanfragen](/answers/topics/azure-data-factory.html)
- [Azure-Videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Q&A-Seite von Microsoft](/answers/topics/azure-data-factory.html)
- [Stack Overflow-Forum für Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Twitter-Informationen über Data Factory](https://twitter.com/hashtag/DataFactory)
