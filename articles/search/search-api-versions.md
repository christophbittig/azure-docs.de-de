---
title: API-Versionen
titleSuffix: Azure Cognitive Search
description: Versionsrichtlinie für REST-APIs für die kognitive Azure-Suche und die Clientbibliothek im .NET SDK.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: 9a53eba2b46333f3be08432ce69b22cfb3d9ef9c
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130073523"
---
# <a name="api-versions-in-azure-cognitive-search"></a>API-Versionen in der kognitiven Azure-Suche

Für die kognitive Azure-Suche sind regelmäßig Featureupdates verfügbar. Manchmal, aber nicht immer, erfordern diese Updates eine neue Version der API, um die Abwärtskompatibilität zu gewährleisten. Die Veröffentlichung einer neuen Version ermöglicht Ihnen, zu steuern, wann und wie Sie Suchdienstupdates in Ihren Code integrieren.

In der Regel versucht das Team für die kognitive Azure-Suche, neue Versionen nur im Bedarfsfall zu veröffentlichen, da es einen gewissen Aufwand erfordern kann, wenn Sie Ihren Code aktualisieren, um eine neue API-Version zu verwenden. Eine neue Version ist nur dann erforderlich, wenn sich ein Aspekt der API in einer Weise geändert hat, die die Abwärtskompatibilität beeinträchtigt. Ursache für solche Änderungen können Fehlerbehebungen bei vorhandenen Funktionen sein oder neue Funktionen, die vorhandene API-Oberflächenbereiche ändern.

<a name="unsupported-versions"></a>

## <a name="unsupported-versions"></a>Nicht unterstützte Versionen

Einige API-Versionen werden nicht mehr unterstützt und von einem Suchdienst abgelehnt:

+ **2015-02-28**
+ **2015-02-28-Preview**
+ **2014-07-31-Preview**
+ **2014-10-20-Preview**

Darüber hinaus werden Versionen des Azure Cognitive Search .NET SDK vor [**3.0.0-rc**](https://www.nuget.org/packages/Microsoft.Azure.Search/3.0.0-rc), ebenfalls eingestellt, da sie auf eine dieser REST-API-Versionen ausgerichtet sind.

Die Unterstützung für die oben aufgeführten Versionen werden am 15. Oktober 2020 eingestellt. Wenn Sie über Code verfügen, der eine Version verwendet, deren Unterstützung eingestellt wurde, können Sie vorhandenen Code zu einer [neueren REST-API-Version](/rest/api/searchservice/) oder einem neueren Azure-SDK [migrieren](search-api-migration.md).

## <a name="rest-apis"></a>REST-APIs

| REST-API | Link |
|----------|------|
| Suchdienst (Datenebene) | [API-Versionen](/rest/api/searchservice/search-service-api-versions) |
| Verwaltung (Steuerungsebene) | [API-Versionen](/rest/api/searchmanagement/management-api-versions) |

## <a name="azure-sdk-for-net"></a>Azure SDK für .NET

In der folgenden Tabelle finden Sie Links zu neueren SDK-Versionen. 

| SDK-Version | Status | BESCHREIBUNG |
|-------------|--------|------------------------------|
| [Azure.Search.Documents 11](/dotnet/api/overview/azure/search.documents-readme) | Stable | Neue Clientbibliothek aus dem Azure .NET SDK, erstmalig veröffentlicht im Juli 2020. |
| [Microsoft.Azure.Search 10](https://www.nuget.org/packages/Microsoft.Azure.Search/) | Stable | Veröffentlichung: Mai 2019 Dies ist die letzte Version des Microsoft.Azure.Search-Pakets. Sie wird von Azure.Search.Documents abgelöst. |
| [Microsoft.Azure.Management.Search 4.0.0](/dotnet/api/overview/azure/search/management) | Stable | Gilt für die Verwaltungs-REST-API-Version 2020-08-01.  |
| Microsoft.Azure.Management.Search 3.0.0 | Stable | Gilt für die Management REST-API, api-version=2015-08-19.  |

## <a name="azure-sdk-for-java"></a>Azure SDK für Java

| SDK-Version | Status | BESCHREIBUNG  |
|-------------|--------|------------------------------|
| [Java azure-search-documents 11](https://newreleases.io/project/github/Azure/azure-sdk-for-java/release/azure-search-documents_11.1.0) | Stable | Neue Clientbibliothek aus dem Azure Java SDK, veröffentlicht im Juli 2020. Gilt für die Such-REST-API, api-version=2019-05-06. |
| [Java Management Client 1.35.0](/java/api/overview/azure/search/management) | Stable | Gilt für die Management REST-API, api-version=2015-08-19. |

## <a name="azure-sdk-for-javascript"></a>Azure SDK für JavaScript

| SDK-Version | Status | BESCHREIBUNG  |
|-------------|--------|------------------------------|
| [JavaScript @azure/search-documents 11.0](https://www.npmjs.com/package/@azure/search-documents) | Stable | Neue Clientbibliothek aus dem Azure JavaScript SDK und TypesScript SDK, veröffentlicht im Juli 2020. Gilt für die Such-REST-API, api-version=2016-09-01. |
| [JavaScript @azure/arm-search](https://www.npmjs.com/package/@azure/arm-search) | Stable | Gilt für die Management REST-API, api-version=2015-08-19. |

## <a name="azure-sdk-for-python"></a>Azure SDK für Python

| SDK-Version | Status | BESCHREIBUNG  |
|-------------|--------|------------------------------|
| [Python azure-search-documents 11.0](https://pypi.org/project/azure-search-documents/) | Stable | Neue Clientbibliothek aus dem Azure Python SDK, veröffentlicht im Juli 2020. Gilt für die Such-REST-API, api-version=2019-05-06. |
| [Python azure-mgmt-search 8.0](https://pypi.org/project/azure-mgmt-search/) | Stable | Gilt für die Management REST-API, api-version=2015-08-19. |

## <a name="all-azure-sdks"></a>Alle Azure SDKs

Wenn Sie nach Beta-Clientbibliotheken und -Dokumentation suchen, enthält [diese Seite](https://azure.github.io/azure-sdk/releases/latest/index.html) Links zu allen Paketen, Codes und Dokumenten der Azure SDK-Bibliothek. 
