---
title: Unterstützung für Azure Table Storage in Azure Cosmos DB
description: Hier erfahren Sie, wie die Azure Cosmos DB-Tabellen-API und Azure Storage-Tabellen durch Verwendung des gleichen Datenmodells und der gleichen Vorgänge zusammenarbeiten.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 11/03/2021
author: sakash279
ms.author: akshanka
ms.reviewer: sngun
ms.openlocfilehash: d0039773cc1ddf50d3c34466d3480ecbe753cf0e
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131555588"
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Entwickeln mit der Azure Cosmos DB-Tabellen-API und Azure Table Storage
[!INCLUDE[appliesto-table-api](../includes/appliesto-table-api.md)]

Die Azure Cosmos DB-Tabellen-API und Azure Table Storage nutzen dasselbe Tabellendatenmodell und dieselben Erstellungs-, Lösch-, Update- und Abfragevorgänge über ihre SDKs.

> [!NOTE]
> Der [serverlose Kapazitätsmodus](../serverless.md) ist nun in der Tabellen-API von Azure Cosmos DB verfügbar.

[!INCLUDE [storage-table-cosmos-comparison](../../../includes/storage-table-cosmos-comparison.md)]

## <a name="azure-sdks"></a>Azure SDKs

### <a name="current-release"></a>Aktuelles Release

Die folgenden SDK-Pakete können sowohl für die Azure Cosmos-Tabellen-API als auch für Azure Table Storage verwendet werden.

* **.NET**: Verwenden Sie das auf der NuGet-Website verfügbare Paket [Azure.Data.Tables](https://www.nuget.org/packages/Azure.Data.Tables/).

* **Python**: Verwenden Sie das auf der PyPi-Website verfügbare Paket [azure-data-tables](https://pypi.org/project/azure-data-tables/).

* **JavaScript/TypeScript**: Verwenden Sie das auf der npm.js-Website verfügbare Paket [@azure/data-tables](https://www.npmjs.com/package/@azure/data-tables).  

* **Java**: Verwenden Sie das auf der Maven-Website verfügbare Paket [azure-data-tables](https://mvnrepository.com/artifact/com.azure/azure-data-tables/12.0.0).

### <a name="prior-releases"></a>Frühere Releases

Die folgenden SDK-Pakete können nur für die Azure Cosmos DB-Tabellen-API verwendet werden.

* **.NET** - [Azure.Data.Tables](https://www.nuget.org/packages/Azure.Data.Tables/) verfügbar auf der NuGet-Website.  Die Azure Tables-Clientbibliothek kann nahtlos auf Azure Table Storage oder Azure Cosmos DB-Tabellendienstendpunkte ohne Codeänderungen ausgerichtet werden.

* **Python** - [azure-cosmosdb-table](https://pypi.org/project/azure-cosmosdb-table/) verfügbar auf der PyPi-Website. Dieses SDK stellt eine Verbindung mit Azure Table Storage sowie mit der Tabellen-API von Azure Cosmos DB her.

* **JavaScript/TypeScript** - [azure-storage](https://www.npmjs.com/package/azure-storage). Das Paket ist auf der npm.js-Website verfügbar. Dieses Azure Storage SDK kann mithilfe der Tabellen-API eine Verbindung mit Azure Cosmos DB-Konten herstellen.

* **Java** - [Microsoft Azure Storage Client SDK for Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage) verfügbar auf der Maven-Website. Dieses Azure Storage SDK kann mithilfe der Tabellen-API eine Verbindung mit Azure Cosmos DB-Konten herstellen.

* **C++**   - [Azure Storage-Clientbibliothek für C++](https://github.com/Azure/azure-storage-cpp/). Diese Clientbibliothek ermöglicht Ihnen die Erstellung von Anwendungen für Azure Storage.

* **Ruby** - [Azure Table Storage-Clientbibliothek für Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table). Dieses Projekt stellt ein Ruby-Paket bereit, das den Zugriff auf Azure Table Storage-Dienste erleichtert.

* **PHP** - [Azure Table Storage-Clientbibliothek für PHP](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table). Dieses Projekt stellt eine PHP-Clientbibliothek bereit, die den Zugriff auf Azure Table Storage-Dienste erleichtert.

* **PowerShell** - [AzureRmStorageTable-PowerShell-Modul](https://www.powershellgallery.com/packages/AzureRmStorageTable). Dieses PowerShell-Modul enthält Cmdlets für die Verwendung mit Speichertabellen.
