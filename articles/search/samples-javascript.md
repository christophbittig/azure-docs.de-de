---
title: JavaScript-Beispiele
titleSuffix: Azure Cognitive Search
description: Hier finden Sie JavaScript-Democodebeispiele für Azure Cognitive Search, für die das Azure .NET SDK für JavaScript verwendet wird.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/11/2021
ms.openlocfilehash: 6bc5a504f1716ff10b56fd30b8991f6d9e4009c7
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122770212"
---
# <a name="javascript-code-samples-for-azure-cognitive-search"></a>JavaScript-Codebeispiele für Azure Cognitive Search

Erfahren Sie mehr über die JavaScript-Codebeispiele, die die Funktionen und den Workflow einer Azure Cognitive Search-Lösung veranschaulichen. In diesen Beispielen wird die [**Azure Cognitive Search-Clientbibliothek**](/javascript/api/overview/azure/search-documents-readme) für das [**Azure SDK für JavaScript**](/azure/developer/javascript/) verwendet, die Sie über die folgenden Links erkunden können.

| Ziel | Link |
|--------|------|
| Paketdownload | [www.npmjs.com/package/@azure/search-documents](https://www.npmjs.com/package/@azure/search-documents) |
| API-Referenz | [@azure/search-documents](/javascript/api/@azure/search-documents/)  |
| API-Testfälle | [github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test) |
| Quellcode | [github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents)  |

## <a name="sdk-samples"></a>SDK-Beispiele

Die Codebeispiele vom Azure SDK-Entwicklungsteam veranschaulichen die API-Verwendung. Sie finden diese Beispiele auf GitHub unter [**azure-sdk-for-js/tree/master/sdk/search/search-documents/samples**](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples).

### <a name="javascript-sdk-samples"></a>JavaScript SDK-Beispiele

| Beispiele | BESCHREIBUNG |
|---------|-------------|
| [Indizes](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/search/search-documents/samples/v11/javascript) | Veranschaulicht, wie [Suchindizes](search-what-is-an-index.md) erstellt, aktualisiert, abgerufen, aufgelistet und gelöscht werden. Diese Beispielkategorie enthält auch ein Beispiel für eine Dienststatistik. |
| [dataSourceConnections (für Indexer)](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/search/search-documents/samples/v11/javascript/dataSourceConnectionOperations.js) | Veranschaulicht das Erstellen, Aktualisieren, Abrufen, Auflisten und Löschen von Indexerdatenquellen, die für die indexerbasierte Indizierung [unterstützter Azure-Datenquellen](search-indexer-overview.md#supported-data-sources) erforderlich sind. |
| [Indexer](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/search/search-documents/samples/v11/javascript) |  Veranschaulicht, wie [Indexer](search-indexer-overview.md) erstellt, aktualisiert, abgerufen, aufgelistet, zurückgesetzt und gelöscht werden.|
| [skillSet](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/search/search-documents/samples/v11/javascript) |   Veranschaulicht, wie [Skillsets](cognitive-search-working-with-skillsets.md) erstellt, aktualisiert, abgerufen, aufgelistet und gelöscht werden, bei denen es sich um angefügte Indexer handelt und die während der Indizierung eine KI-basierte Anreicherung durchführen. |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/search/search-documents/samples/v11/javascript) | Veranschaulicht, wie [Synonymzuordnungen](search-synonyms.md) erstellt, aktualisiert, abgerufen, aufgelistet und gelöscht werden.  |

### <a name="typescript-samples"></a>TypeScript-Beispiele

| Beispiele | BESCHREIBUNG |
|---------|-------------|
| [Indizes](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/search/search-documents/samples/v11/typescript/src) | Veranschaulicht, wie [Suchindizes](search-what-is-an-index.md) erstellt, aktualisiert, abgerufen, aufgelistet und gelöscht werden. Diese Beispielkategorie enthält auch ein Beispiel für eine Dienststatistik. |
| [dataSourceConnections (für Indexer)](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/search/search-documents/samples/v11/typescript/src/dataSourceConnectionOperations.ts) | Veranschaulicht das Erstellen, Aktualisieren, Abrufen, Auflisten und Löschen von Indexerdatenquellen, die für die indexerbasierte Indizierung [unterstützter Azure-Datenquellen](search-indexer-overview.md#supported-data-sources) erforderlich sind. |
| [Indexer](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/search/search-documents/samples/v11/typescript/src) |  Veranschaulicht, wie [Indexer](search-indexer-overview.md) erstellt, aktualisiert, abgerufen, aufgelistet, zurückgesetzt und gelöscht werden.|
| [skillSet](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/search/search-documents/samples/v11/typescript/src/skillSetOperations.ts) |   Veranschaulicht, wie [Skillsets](cognitive-search-working-with-skillsets.md) erstellt, aktualisiert, abgerufen, aufgelistet und gelöscht werden, bei denen es sich um angefügte Indexer handelt und die während der Indizierung eine KI-basierte Anreicherung durchführen. |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/search/search-documents/samples/v11/typescript/src/synonymMapOperations.ts) | Veranschaulicht, wie [Synonymzuordnungen](search-synonyms.md) erstellt, aktualisiert, abgerufen, aufgelistet und gelöscht werden.  |

## <a name="doc-samples"></a>Dokumentationsbeispiele

Die Codebeispiele vom Cognitive Search-Team veranschaulichen die Funktionen und Workflows. Auf viele dieser Beispiele wird in Tutorials, Schnellstarts und Anleitungen verwiesen. Sie finden diese Beispiele auf GitHub unter [**Azure-Samples/azure-search-javascript-samples**](https://github.com/Azure-Samples/azure-search-javascript-samples).

| Beispiele | Artikel |
|---------|---------|
| [quickstart](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11) | Quellcode für [Schnellstart: Erstellen eines Suchindex in JavaScript](search-get-started-javascript.md). Hier wird der allgemeine Workflow zum Erstellen, Laden und Abfragen eines Suchindex unter Verwendung von Beispieldaten beschrieben. |
| [search-website](https://github.com/azure-samples/azure-search-javascript-samples/tree/master/search-website) | Quellcode für [Tutorial: Hinzufügen der Suche zu Web-Apps](tutorial-javascript-overview.md). Veranschaulicht eine End-to-End-Such-App, die einen Rich-Client sowie Komponenten zum Hosting der App und zur Verarbeitung von Suchanforderungen enthält.|

> [!Tip]
> Testen Sie den [Beispielbrowser](/samples/browse/?languages=javascript&products=azure-cognitive-search), um GitHub nach Microsoft-Codebeispielen zu durchsuchen (gefiltert nach Produkt, Dienst und Sprache).

## <a name="other-samples"></a>Weitere Beispiele

Die folgenden Beispiele werden ebenfalls vom Cognitive Search-Team veröffentlicht, ohne dass jedoch in der Dokumentation auf sie Bezug genommen wird. Die zugehörigen Infodateien enthalten Anweisungen zu ihrer Verwendung.

| Beispiele | BESCHREIBUNG |
|---------|-------------|
| [azure-search-react-template](https://github.com/dereklegenzoff/azure-search-react-template) | React-Vorlage für Azure Cognitive Search (github.com) |
