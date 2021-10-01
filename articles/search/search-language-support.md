---
title: Mehrsprachige Indizierung für nicht englische Suchabfragen
titleSuffix: Azure Cognitive Search
description: Erstellen Sie einen Index, der mehrsprachige Inhalte unterstützt, und erstellen Sie dann Abfragen für diesen Inhalt.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/08/2021
ms.openlocfilehash: 6511764697adcd6c8743efe41d276ccef3ff1bb0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124823895"
---
# <a name="create-an-index-for-multiple-languages-in-azure-cognitive-search"></a>Erstellen eines Index für mehrere Sprachen in Azure Cognitive Search

In einer mehrsprachigen Suchanwendung ist es wichtig, nach Ergebnissen in der Sprache des Benutzers suchen und entsprechende Ergebnisse abrufen zu können. In Azure Cognitive Search besteht eine Möglichkeit zum Erfüllen der Sprachanforderungen einer mehrsprachigen App beispielsweise darin, dedizierte Felder für das Speichern von Zeichenfolgen in einer bestimmten Sprache zu erstellen und dann bei der Abfrage die Volltextsuche auf eben diese Felder zu beschränken.

+ Legen Sie für Felddefinitionen ein [Sprachanalysetool](index-add-language-analyzers.md) fest, mit dem die linguistischen Regeln der Zielsprache abgerufen werden. 

+ Legen Sie in der Abfrageanforderung den `searchFields`-Parameter so fest, dass die Volltextsuche auf bestimmte Felder festgelegt ist, und verwenden Sie dann `select`, um nur die Felder mit kompatiblem Inhalt zurückzugeben.

Der Erfolg dieser Methode hängt von der Integrität der Feldinhalte ab. Azure Cognitive Search übersetzt keine Zeichenfolgen und führt keine Spracherkennung als Teil der Abfrageausführung durch. Sie müssen selbst dafür sorgen, dass die Felder die erwarteten Zeichenfolgen enthalten.

## <a name="define-fields-for-content-in-different-languages"></a>Definieren von Feldern für Inhalt in verschiedenen Sprachen

In der kognitiven Azure-Suche sind Abfragen auf einen einzelnen Index ausgerichtet. Entwickler, die sprachspezifische Zeichenfolgen in einer einzelnen Suchumgebung bereitstellen möchten, definieren üblicherweise dedizierte Felder, um die Werte zu speichern – also ein Feld für englische Zeichenfolgen, ein Feld für Französisch und so weiter.

Die „analyzer“-Eigenschaft für eine Felddefinition wird verwendet, um das [Sprachanalysetool](index-add-language-analyzers.md) festzulegen. Sie wird sowohl für die Indizierung als auch für die Abfrageausführung verwendet.

```JSON
{
  "name": "hotels-sample-index",
  "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "en.microsoft"
    },
    {
      "name": "Description_fr",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "fr.microsoft"
    },
```

## <a name="build-and-load-an-index"></a>Erstellen und Laden eines Index

Ein (wahrscheinlich naheliegender) Zwischenschritt vor der Erstellung einer Abfrage ist das [Erstellen und Auffüllen des Index](search-get-started-dotnet.md). Er wird daher nur der Vollständigkeit halber erwähnt. Eine Möglichkeit zum Ermitteln der Indexverfügbarkeit besteht darin, die Liste der Indizes im [Portal](https://portal.azure.com) zu überprüfen.

> [!TIP]
> Die Spracherkennung und Textübersetzung werden bei der Datenerfassung durch die [KI-Anreicherung](cognitive-search-concept-intro.md) und [Skillsets](cognitive-search-working-with-skillsets.md) unterstützt. Wenn Sie über eine Azure-Datenquelle mit gemischtem Sprachinhalt verfügen, können Sie mithilfe des [Datenimport-Assistenten](cognitive-search-quickstart-blob.md) die Spracherkennungs- und Übersetzungsfeatures testen.

## <a name="constrain-the-query-and-trim-results"></a>Einschränken der Abfrage und Ausschließen von Ergebnissen

Abfrageparameter dienen dazu, die Suche auf bestimmte Felder einzugrenzen und anschließend die Ergebnisse von Feldern auszuschließen, die für Ihr Szenario nicht relevant sind. 

| Parameter | Zweck |
|-----------|--------------|
| **searchFields** | Grenzt die Volltextsuche auf die Liste der angegebenen Felder ein. |
| **$select** | Kürzt die Antwort, sodass nur die von Ihnen angegebenen Felder berücksichtigt werden. Standardmäßig werden alle abrufbaren Felder zurückgegeben. Mit dem Parameter **$select** können Sie auswählen, welche Felder berücksichtigt werden sollen. |

Wenn Sie die Suche also beispielsweise auf Felder mit französischen Zeichenfolgen einschränken möchten, richten Sie die Abfrage mithilfe von **searchFields** auf Felder aus, die Zeichenfolgen in dieser Sprache enthalten.

Die Angabe des Analysetools für eine Abfrageanforderung ist nicht erforderlich. Während der Abfrageverarbeitung wird immer ein Sprachanalysetool für die Felddefinition verwendet. Bei Abfragen, die mehrere Felder angeben, die verschiedene Sprachanalysetools aufrufen, werden die Begriffe oder Ausdrücke unabhängig von den zugewiesenen Analysetools für jedes Feld verarbeitet.

Standardmäßig werden bei einer Suche alle als abrufbar markierten Felder zurückgegeben. Es empfiehlt sich daher, Felder auszuschließen, die für die angestrebte sprachspezifische Sucherfahrung nicht geeignet sind. Wenn Sie die Suche auf ein Feld mit französischen Zeichenfolgen eingeschränkt haben, möchten Sie wahrscheinlich Felder mit englischen Zeichenfolgen aus Ihren Ergebnissen ausschließen. Mithilfe des Abfrageparameters **$select** können Sie steuern, welche Felder an die aufrufende Anwendung zurückgegeben werden sollen.

#### <a name="example-in-rest"></a>Beispiel in REST

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "animaux acceptés",
    "searchFields": "Tags, Description_fr",
    "select": "HotelName, Description_fr, Address/City, Address/StateProvince, Tags",
    "count": "true"
}
```

#### <a name="example-in-c"></a>Beispiel in C#

```csharp
private static void RunQueries(SearchClient srchclient)
{
    SearchOptions options;
    SearchResults<Hotel> response;

    options = new SearchOptions()
    {
        IncludeTotalCount = true,
        Filter = "",
        OrderBy = { "" }
    };

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Description_fr");
    options.SearchFields.Add("Tags");
    options.SearchFields.Add("Description_fr");

    response = srchclient.Search<Hotel>("*", options);
    WriteDocuments(response);
}
```

## <a name="boost-language-specific-fields"></a>Optimieren sprachspezifischer Felder

Manchmal ist die Sprache des Agents, der eine Abfrage ausgibt, nicht bekannt. In diesem Fall kann die Abfrage für alle Felder gleichzeitig ausgegeben werden. Die IA-Einstellung für Ergebnisse in einer bestimmten Sprache kann mithilfe von [Bewertungsprofilen](index-add-scoring-profiles.md) festgelegt werden. Im folgenden Beispiel erhalten die Übereinstimmungen, die in der Beschreibung in englischer Sprache gefunden werden, eine höhere Bewertung als Übereinstimmungen in anderen Sprachen:

```JSON
  "scoringProfiles": [
    {
      "name": "englishFirst",
      "text": {
        "weights": { "description": 2 }
      }
    }
  ]
```

Dann würden Sie das Bewertungsprofil in die Suchanforderung einschließen:

```http
POST /indexes/hotels/docs/search?api-version=2020-06-30
{
  "search": "pets allowed",
  "searchFields": "Tags, Description",
  "select": "HotelName, Tags, Description",
  "scoringProfile": "englishFirst",
  "count": "true"
}
```

## <a name="next-steps"></a>Nächste Schritte

+ [Hinzufügen eines Sprachanalysetools](index-add-language-analyzers.md)
+ [Funktionsweise der Volltextsuche in Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Search Documents (Azure Search Service REST API)](/rest/api/searchservice/search-documents) (Suchen nach Dokumenten (Azure Search Service-REST-API))
+ [Einführung in die KI in Azure Cognitive Search](cognitive-search-concept-intro.md)
+ [Skillsetübersicht](cognitive-search-working-with-skillsets.md)