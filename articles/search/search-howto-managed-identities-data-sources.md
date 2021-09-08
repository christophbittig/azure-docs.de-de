---
title: Einrichten einer Verbindung mit einer Datenquelle mithilfe einer verwalteten Identität
titleSuffix: Azure Cognitive Search
description: Erfahren Sie, wie Sie eine Indexerverbindung mit einer Datenquelle mithilfe einer verwalteten Identität einrichten
author: markheff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/02/2021
ms.openlocfilehash: bd4d10c32f1c850adf6dc886672b16937b553222
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2021
ms.locfileid: "122350379"
---
# <a name="set-up-an-indexer-connection-to-a-data-source-using-a-managed-identity"></a>Einrichten einer Indexerverbindung mit einer Datenquelle mithilfe einer verwalteten Identität

> [!IMPORTANT] 
> Das Einrichten einer Verbindung mit einer Datenquelle mithilfe einer verwalteten Identität mit dem Tarif „Free“ von Azure Cognitive Search wird nicht unterstützt.

Ein [Indexer](search-indexer-overview.md) in Azure Cognitive Search ist ein Crawler, mit dem Daten aus Ihrer Datenquelle in Azure Cognitive Search abgerufen werden können. Ein Indexer ruft eine Datenquellenverbindung aus dem von Ihnen erstellten Datenquellenobjekt ab. Das Datenquellenobjekt enthält im Allgemeinen Anmeldeinformationen für die Zieldatenquelle. Beispielsweise kann das Datenquellenobjekt einen Azure Storage-Kontoschlüssel enthalten, wenn Sie Daten aus einem Blobspeichercontainer indizieren möchten.

In vielen Fällen lassen sich Anmeldeinformationen problemlos direkt im Datenquellenobjekt bereitstellen, es können sich jedoch auch Fragen ergeben:
* Wie lassen sich die Anmeldeinformationen in meinem Code schützen, mit dem das Datenquellenobjekt erstellt wird?
* Wenn mein Kontoschlüssel oder Kennwort kompromittiert ist und geändert werden muss, muss ich dann meine Datenquellenobjekte mit dem neuen Kontoschlüssel oder Kennwort aktualisieren, damit der Indexer wieder eine Verbindung mit einer Datenquelle herstellen kann?

Diese Probleme lassen sich durch Einrichten der Verbindung mithilfe einer verwalteten Identität lösen.

## <a name="using-managed-identities"></a>Verwenden von verwalteten Identitäten

[Verwaltete Identitäten](../active-directory/managed-identities-azure-resources/overview.md) ist eine Funktion, die eine automatisch verwaltete Identität für Anwendungen in Azure Active Directory (Azure AD) bereitstellt. Sie können diese Funktion in Azure Cognitive Search verwenden, um ein Datenquellenobjekt mit einer Verbindungszeichenfolge zu erstellen, die keine Anmeldeinformationen enthält. Stattdessen wird dem Suchdienst durch die rollenbasierte Zugriffssteuerung in Azure (RBAC) Zugriff auf die Datenquelle erteilt.

Wenn Sie eine Datenquelle mithilfe einer verwalteten Identität einrichten, können Sie die Anmeldeinformationen der Datenquelle ändern, und die Indexer können weiterhin eine Verbindung mit der Datenquelle herstellen. Sie können außerdem Datenquellenobjekte in Ihrem Code erstellen, ohne einen Kontoschlüssel einfügen oder Key Vault zum Abrufen eines Kontoschlüssels verwenden zu müssen.

Es gibt zwei Typen von verwalteten Identitäten. Azure Cognitive Search unterstützt sowohl systemseitig zugewiesene als auch benutzerseitig zugewiesene verwaltete Identitäten.

### <a name="system-assigned-managed-identity"></a>Systemseitig zugewiesene verwaltete Identität

Eine [systemseitig zugewiesene verwaltete Identität](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) kann einem einzelnen Azure-Dienst zugewiesen werden. Sie können eine systemseitig zugewiesene verwaltete Identität einem einzelnen Azure Cognitive Search-Dienst zuweisen, und diese ist dann an den Lebenszyklus dieses Search-Diensts gebunden.

### <a name="user-assigned-managed-identity-preview"></a>Benutzerseitig zugewiesene verwaltete Identität (Vorschau)

> [!IMPORTANT]
>Dieses Feature befindet sich in der öffentlichen Vorschau und unterliegt den [zusätzlichen Nutzungsbedingungen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Dieses Feature steht in der REST-API-Version 2021-04-30-Preview und der [Verwaltungs-REST-API 2021-04-01-Preview](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update) zur Verfügung.

Eine [benutzerseitig zugewiesene verwaltete Identität](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) ist eine eigenständige Azure-Ressource, die einem oder mehreren Azure-Diensten zugewiesen werden kann. Einem einzelnen Azure Cognitive Search-Dienst kann eine oder mehrere benutzerseitig zugewiesene verwaltete Identitäten zugewiesen werden. Eine einzelne benutzerseitig zugewiesene verwaltete Identität kann mehreren Suchdiensten zugewiesen werden.

## <a name="limitations"></a>Einschränkungen

In folgenden Datenquellen können Indexerverbindungen mithilfe verwalteter Identitäten eingerichtet werden. 

* [Azure Blob Storage, Azure Data Lake Storage Gen2 (Vorschau), Azure Table Storage](search-howto-managed-identities-storage.md)
* [Azure Cosmos DB](search-howto-managed-identities-cosmos-db.md)
* [Azure SQL-Datenbank](search-howto-managed-identities-sql.md)

Bei folgenden Funktionen kann die Verbindung derzeit nicht mithilfe verwalteter Identitäten eingerichtet werden:
* Wissensspeicher
* Benutzerdefinierte Qualifikationen
 
## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Einrichten einer Indexerverbindung mithilfe verwalteter Identitäten:

* [Azure Blob Storage, Azure Data Lake Storage Gen2 (Vorschau), Azure Table Storage](search-howto-managed-identities-storage.md)
* [Azure Cosmos DB](search-howto-managed-identities-cosmos-db.md)
* [Azure SQL-Datenbank](search-howto-managed-identities-sql.md)