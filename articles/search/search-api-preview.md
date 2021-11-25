---
title: Liste der Previewfunktionen
titleSuffix: Azure Cognitive Search
description: Previewfunktionen werden veröffentlicht, damit Kunden Feedback zu ihrer Auslegung und Nutzbarkeit geben können. Dieser Artikel stellt eine umfassende Liste aller Funktionen dar, die sich derzeit in der Vorschau befinden.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/21/2021
ms.openlocfilehash: 968e4320efc8e22a6a561b7c75c89e966045d6ac
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132305824"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Previewfunktionen in Azure Cognitive Search

Dieser Artikel stellt eine umfassende Liste aller Funktionen dar, die sich in der öffentlichen Vorschau befinden. Die Vorschaufunktion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Previewfunktionen, die in die allgemeine Verfügbarkeit übergehen, werden aus dieser Liste entfernt. Wenn eine Funktion im Folgenden nicht aufgeführt ist, können Sie davon ausgehen, dass sie allgemein verfügbar ist. Ankündigungen zur allgemeinen Verfügbarkeit finden Sie in den [Dienstupdates](https://azure.microsoft.com/updates/?product=search) sowie unter [Neuigkeiten](whats-new.md).

|Funktion&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Category | BESCHREIBUNG | Verfügbarkeit  |
|---------|------------------|-------------|---------------|
|  [**Azure Dateien-Indexer**](search-file-storage-integration.md) | Indexerdatenquelle | Zusätzliche REST-API-Unterstützung zum Erstellen von Indexern für [Azure Files](https://azure.microsoft.com/services/storage/files/) | Public Preview, [Search-REST-API 2021-04-30-Preview](/rest/api/searchservice/index-preview) |
| [**Search-REST-API 2021-04-30-Preview**](/rest/api/searchservice/index-preview) | Sicherheit | Ändert [Erstellen oder Aktualisieren einer Datenquelle](/rest/api/searchservice/preview-api/create-or-update-data-source), um verwaltete Identitäten unter Azure Active Directory für Indexer zu unterstützen, die eine Verbindung mit externen Datenquellen herstellen. | Public Preview, [Search-REST-API 2021-04-30-Preview](/rest/api/searchservice/index-preview) |
| [**Azure RBAC-Unterstützung**](search-security-rbac.md) | Sicherheit | Verwenden Sie neue integrierte Rollen, um den Zugriff auf Indizes und Indizierung zu steuern und so die Abhängigkeit von API-Schlüsseln zu vermeiden oder zu verringern. | Öffentliche Vorschau ([auf Anforderung](./search-security-rbac.md?tabs=config-svc-portal%2croles-portal%2ctest-portal#step-1-preview-sign-up)). Nachdem Ihr Abonnement integriert wurde, verwenden Sie das Azure-Portal oder die Verwaltungs-REST-API-Version 2021-04-01-Preview, um einen Suchdienst für die Authentifizierung auf Datenebene zu konfigurieren. |
| [**Verwaltungs-REST-API 2021-04-01-Preview**](/rest/api/searchmanagement/) | Sicherheit | Ändert [Dienst erstellen oder aktualisieren,](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update) um neue [DataPlaneAuthOptions](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#dataplaneauthoptions) zu unterstützen. | Öffentliche Vorschau, [Verwaltungs-REST-API](/rest/api/searchmanagement/), API-Version Vorschau 2021-04-01.|
| [**Zurücksetzen von Dokumenten**](search-howto-run-reset-indexers.md) | Indexerstellung | Erneute Verarbeitung von einzeln ausgewählten Suchdokumenten in Indexerworkloads. | Verwenden Sie die [REST-API zum Zurücksetzen von Dokumenten](/rest/api/searchservice/preview-api/reset-documents) oder die API-Version 2021-04-30-Preview oder 2020-06-30-Preview. |
|  [**Power Query-Connectors**](search-how-to-index-power-query-data-sources.md) | Indexerdatenquelle | Indexer können jetzt von anderen Cloudplattformen aus indizieren. Wenn Sie einen Indexer verwenden, um externe Datenquellen zur Indizierung zu crawlen, können Sie jetzt Power Query-Connectors verwenden, um eine Verbindung zu Amazon Redshift, Elasticsearch, PostgreSQL, Salesforce Objects, Salesforce Reports, Smartsheet und Snowflake herzustellen. | [Registrierung](https://aka.ms/azure-cognitive-search/indexer-preview) ist erforderlich, damit Unterstützung für Ihr Abonnement im Back-End aktiviert werden kann. Konfigurieren Sie diese Datenquelle mithilfe von [Erstellen oder Aktualisieren einer Datenquelle](/rest/api/searchservice/preview-api/create-or-update-data-source), der API-Version 2021-04-30-Preview oder 2020-06-30-Preview oder des Azure-Portals.|
| [**SharePoint Online-Indexer**](search-howto-index-sharepoint-online.md) | Indexerdatenquelle | Neue Datenquelle für die indexerbasierte Indizierung von SharePoint-Inhalten. | [Registrierung](https://aka.ms/azure-cognitive-search/indexer-preview) ist erforderlich, damit Unterstützung für Ihr Abonnement im Back-End aktiviert werden kann. Konfigurieren Sie diese Datenquelle mithilfe von [Erstellen oder Aktualisieren einer Datenquelle](/rest/api/searchservice/preview-api/create-or-update-data-source), der API-Version 2021-04-30-Preview oder 2020-06-30-Preview oder des Azure-Portals. |
|  [**MySQL-Indexerdatenquelle**](search-howto-index-mysql.md) | Indexerdatenquelle | Indizieren Sie Inhalte und Metadaten aus Azure MySQL-Datenquellen.| [Registrierung](https://aka.ms/azure-cognitive-search/indexer-preview) ist erforderlich, damit Unterstützung für Ihr Abonnement im Back-End aktiviert werden kann. Konfigurieren Sie diese Datenquelle mithilfe von [Erstellen oder Aktualisieren der Datenquelle](/rest/api/searchservice/preview-api/create-or-update-data-source), der API-Version 2021-04-30-Preview oder 2020-06-30-Preview, des [.NET SDK 11.2.1](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype.mysql) oder des Azure-Portals. |
| [**Cosmos DB-Indexer: MongoDB-API, Gremlin-API**](search-howto-index-cosmosdb.md) | Indexerdatenquelle | Für Cosmos DB ist die SQL-API allgemein verfügbar, aber die MongoDB- und Gremlin-APIs befinden sich in der Vorschauphase. | Für MongoDB und Gremlin müssen Sie sich [zuerst registrieren](https://aka.ms/azure-cognitive-search/indexer-preview), damit Unterstützung für Ihr Abonnement im Back-End aktiviert werden kann. MongoDB-Datenquellen können im Portal konfiguriert werden. Konfigurieren Sie diese Datenquelle mithilfe von [Erstellen oder Aktualisieren einer Datenquelle](/rest/api/searchservice/preview-api/create-or-update-data-source) oder der API-Version 2021-04-30-Preview oder 2020-06-30-Preview. |
| [**Natives vorläufiges Löschen von Blobs**](search-howto-index-changed-deleted-blobs.md) | Indexerdatenquelle | Der Azure Blob Storage-Indexer in Azure Cognitive Search erkennt Blobs, die sich im vorläufig gelöschten Zustand befinden. Das entsprechende Suchdokument wird während der Indizierung entfernt. | Konfigurieren Sie diese Datenquelle mithilfe von [Erstellen oder Aktualisieren einer Datenquelle](/rest/api/searchservice/preview-api/create-or-update-data-source) oder der API-Version 2021-04-30-Preview oder 2020-06-30-Preview. |
| [**Semantische Suche**](semantic-search-overview.md) | Relevanz (Bewertung) | Semantische Rangfolge von Ergebnissen, Beschriftungen und Antworten. | Konfigurieren Sie die semantische Suche mithilfe von [Durchsuchen von Dokumenten](/rest/api/searchservice/preview-api/search-documents), der API-Version 2021-04-30-Preview oder 2020-06-30-Preview und des Such-Explorers (Portal). |
| [**speller**](cognitive-search-aml-skill.md) | Abfrage | Optionale Rechtschreibkorrektur für Abfragebegriffeingaben für einfache, vollständige und semantische Abfragen. | [Vorschauversion der REST-API für die Suche](/rest/api/searchservice/preview-api/search-documents), API-Version 2021-04-30-Preview oder 2020-06-30-Preview und Such-Explorer (Portal) |
| [**Normalisierungsfunktionen**](search-normalizers.md) | Abfrage |  Normalisierungsfunktionen bieten einfache Textvorverarbeitung: konsistente Schreibweisen, Akzententfernung und ASCII-Faltung, ohne die Kette für die Volltextanalyse aufrufen zu müssen.| Verwenden Sie [Durchsuchen von Dokumenten](/rest/api/searchservice/preview-api/search-documents) oder die API-Version 2021-04-30-Preview oder 2020-06-30-Preview.|
| [**featuresMode-Parameter**](/rest/api/searchservice/preview-api/search-documents#query-parameters) | Relevanz (Bewertung) | Erweiterung der Relevanzbewertung um Details: Ähnlichkeitsbewertung pro Feld, Ausdruckshäufigkeit pro Feld und Anzahl der zugeordneten eindeutigen Token pro Feld. Sie können diese Datenpunkte in [benutzerdefinierten Bewertungslösungen](https://github.com/Azure-Samples/search-ranking-tutorial) verwenden. | Fügen Sie diesen Abfrageparameter mithilfe von [Durchsuchen von Dokumenten](/rest/api/searchservice/preview-api/search-documents) oder der API-Version 2021-04-30-Preview, 2020-06-30-Preview oder 2019-05-06-Preview hinzu. |
| [**AML-Skill (Azure Machine Learning)**](cognitive-search-aml-skill.md) | KI-Anreicherung (Skills) | Ein neuer Skilltyp zum Integrieren eines Rückschlussendpunkts aus Azure Machine Learning. Erste Schritte mit [diesem Tutorial](cognitive-search-tutorial-aml-custom-skill.md). | Verwenden Sie die [Vorschauversion der REST-API für die Suche](/rest/api/searchservice/) oder API-Version 2021-04-30-Preview, 2020-06-30-Preview oder 2019-05-06-Preview. Ebenfalls im Portal im Skillsetentwurf verfügbar. Dabei wird davon ausgegangen, das die Dienste Cognitive Search und Azure ML im gleichen Abonnement bereitgestellt wurden. |
| [**Inkrementelle Anreicherung**](cognitive-search-incremental-indexing-conceptual.md) | KI-Anreicherung (Skills) | Fügt einer Anreicherungspipeline die Zwischenspeicherung hinzu, sodass Sie vorhandene Ausgaben wiederverwenden können, wenn die Inhalte durch eine Zieländerung, z. B. eine Aktualisierung am Skillset oder einem anderen Objekt, nicht geändert werden. Die Zwischenspeicherung betrifft nur angereicherte Dokumente, die über ein Skillset erstellt werden.| Fügen Sie diese Konfigurationseinstellung mithilfe der [Vorschauversion der REST-API zum Erstellen oder Aktualisieren eines Indexers](/rest/api/searchservice/create-indexer) oder der API-Version 2021-04-30-Preview, 2020-06-30-Preview oder 2019-05-06-Preview hinzu. |
| [**Debugsitzungen**](cognitive-search-debug-session.md) | Portal, KI-Anreicherung (Skills) | Ein in die Sitzung integrierter Skillset-Editor, der zum Untersuchen und Beheben von Problemen bei einem Skillset verwendet wird. Während einer Debugsitzung angewendete Korrekturen können in einem Skillset im Dienst gespeichert werden. | Nur im Portal mithilfe von Links zur Seitenmitte zum Öffnen einer Debugsitzung. |
| [**moreLikeThis**](search-more-like-this.md) | Abfrage | Sucht Dokumente, die für ein bestimmtes Dokument relevant sind. Dieses Feature war in früheren Vorschauversionen enthalten. | Fügen Sie diesen Abfrageparameter mithilfe von Aufrufen der [Vorschauversion der REST-API zum Durchsuchen von Dokumenten](/rest/api/searchservice/search-documents) oder der API-Version 2021-04-30-Preview, 2020-06-30-Preview, 2019-05-06-Preview, 2016-09-01-Preview oder 2017-11-11-Preview hinzu. |

## <a name="how-to-call-a-preview-rest-api"></a>Aufrufen einer Vorschau-REST-API

Die kognitive Azure-Suche bietet immer zuerst Vorabversionen experimenteller Funktionen über die REST-API, dann über Vorabversionen des .NET SDK.

Vorschaufunktionen stehen für Tests und Experimente zur Verfügung, mit dem Ziel, Feedback zu Entwurf und Implementierung der Funktionen zu sammeln. Aus diesem Grund können sich Vorschaufunktionen mit der Zeit ändern – mitunter in einer Weise, die die Abwärtskompatibilität beeinträchtigt. Dies steht im Gegensatz zu Funktionen in der allgemein verfügbaren Version (GA), die stabil sind und sich bis auf kleinere abwärtskompatible Fehlerbehebungen und Verbesserungen eher nicht ändern. Außerdem werden Vorschaufunktionen nicht immer in der GA-Version übernommen.

Auch wenn einige Previewfunktionen im Portal und im .NET SDK verfügbar sein können, enthält die REST-API immer alle Previewfunktionen.

+ Für Suchvorgänge lautet die aktuelle Vorschauversion [ **`2021-04-30-Preview`**](/rest/api/searchservice/index-preview).

+ Für Verwaltungsvorgänge lautet die aktuelle Vorschauversion [ **`2021-04-01-Preview`**](/rest/api/searchmanagement/management-api-versions).

Ältere Vorschauversionen sind zwar noch betriebsbereit, veralten jedoch mit der Zeit. Wenn von Ihrem Code `api-version=2019-05-06-Preview`, `api-version=2016-09-01-Preview` oder `api-version=2017-11-11-Preview` aufgerufen wird, sind diese Aufrufe weiterhin gültig. Diese Versionen enthalten jedoch keine neuen Features, und es werden keine Fehlerbehebungen garantiert.

Die folgende Beispielsyntax veranschaulicht einen Aufruf der API-Vorschauversion.

```HTTP
POST https://[service name].search.windows.net/indexes/hotels-idx/docs/search?api-version=2021-04-30-Preview  
  Content-Type: application/json  
  api-key: [admin key]
```

Der Azure Cognitive Search-Dienst ist in mehreren Versionen und Clientbibliotheken verfügbar. Weitere Informationen finden Sie unter [API-Versionen](search-api-versions.md).

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die Referenzdokumentation für die Search-REST-Vorschau-API an. Wenn Probleme auftreten, können Sie sich über [Stack Overflow](https://stackoverflow.com/) an uns wenden oder [den Support kontaktieren](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Referenz zur REST-API für den Azure-Suchdienst (Vorschau)](/rest/api/searchservice/index-preview)
