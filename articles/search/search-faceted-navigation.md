---
title: Hinzufügen einer Kategoriehierarchie für die Facettennavigation
titleSuffix: Azure Cognitive Search
description: Fügen Sie die Facettennavigation für das selbstgesteuerte Filtern in Anwendungen hinzu, die in Azure Cognitive Search integriert sind.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/30/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 55473848a8c8e8e7f9c6072566eb73d823feaba2
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131561307"
---
# <a name="add-faceted-navigation-to-a-search-app"></a>Hinzufügen der Facettennavigation zu einer Such-App

Die Facettennavigation wird zum selbstgesteuerten Filtern beim Drilldown in Abfrageergebnissen in einer Such-App verwendet, in der Ihre Anwendung Formularsteuerelemente zum Begrenzen der Suche auf bestimmte Gruppen von Dokumenten enthält (z. B. Kategorien oder Marken). Azure Cognitive Search stellt hierfür die zugrunde liegende Datenstruktur und Filter bereit. 

In diesem Artikel erfahren Sie mehr über die grundlegenden Schritte zum Erstellen einer Facettennavigationsstruktur in Azure Cognitive Search.

> [!div class="checklist"]
> * Festlegen von Feldattributen im Index
> * Strukturieren von Anforderung und Antwort
> * Hinzufügen von Navigationssteuerelementen und Filtern auf der Präsentationsebene

Code auf der Präsentationsebene übernimmt den Großteil der Arbeit bei der Facettennavigation. Die Demos und Beispiele, die am Ende dieses Artikels aufgeführt sind, enthalten funktionierenden Code, der zeigt, wie Sie alles zusammenführen.

## <a name="faceted-navigation-in-a-search-page"></a>Facettennavigation auf einer Suchseite

Facets sind dynamisch und werden für eine Abfrage zurückgegeben. Eine Suchantwort enthält alle Facettenkategorien zum Navigieren in den Dokumenten im Ergebnis. Die Abfrage wird zuerst ausgeführt, und dann werden Facetten aus den aktuellen Ergebnissen abgerufen und in einer Facettennavigationsstruktur zusammengestellt.

In Cognitive Search sind Facetten eine Ebene tief und können nicht hierarchisch sein. Wenn Sie nicht mit der strukturierten Facettennavigation vertraut sind, finden Sie im folgenden Beispiel eine auf der linken Seite. Die Zähler geben die Anzahl der Übereinstimmungen für jede Facette an. Das gleiche Dokument kann in mehreren Facetten dargestellt werden.

:::image source="media/tutorial-csharp-create-first-app/azure-search-facet-nav.png" alt-text="Ergebnisse der Facettensuche":::

Facetten unterstützen Sie bei der Suche und stellen sicher, dass Sie Ergebnisse erhalten. Als Entwickler können Sie mithilfe von Facets besonders hilfreiche Suchkriterien für die Navigation in Ihrem Suchindex verfügbar machen.

## <a name="enable-facets-in-the-index"></a>Aktivieren von Facetten im Index

Die Facettierung wird in einer Indexdefinition feldweise aktiviert, wenn Sie das Attribut „facetable“ auf TRUE festlegen.

Obwohl dies nicht unbedingt erforderlich ist, sollten Sie auch das Attribut „filterable“ festlegen, damit Sie die erforderlichen Filter für die Facettennavigation in Ihrer Suchanwendung erstellen können.

Das folgende Beispiel für den Beispielindex „hotels“ zeigt die Attribute „facetable“ und „filterable“ für Felder mit niedriger Kardinalität, die einzelne Werte oder kurze Ausdrücke enthalten: „Category“, „Tags“, „Rating“.

```json
{
  "name": "hotels",  
  "fields": [
    { "name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false },
    { "name": "Description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
    { "name": "HotelName", "type": "Edm.String", "facetable": false },
    { "name": "Category", "type": "Edm.String", "filterable": true, "facetable": true },
    { "name": "Tags", "type": "Collection(Edm.String)", "filterable": true, "facetable": true },
    { "name": "Rating", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "Location", "type": "Edm.GeographyPoint" }
  ]
}
```

### <a name="choosing-fields"></a>Auswählen von Feldern

Facetten können über Felder mit einzelnen Werten und auch über Sammlungen berechnet werden. Felder mit den folgenden Merkmalen funktionieren bei der Facettennavigation am besten:

* Niedrige Kardinalität (eine kleine Anzahl unterschiedlicher Werte, die sich in Dokumenten in Ihrem Suchkorpus wiederholen)

* Kurze beschreibende Werte (ein oder zwei Wörter), die in einer Navigationsstruktur gut gerendert werden können

Der Inhalt eines Felds und nicht das Feld selbst erzeugt die Facetten in einer Facettennavigationsstruktur. Wenn es sich bei der Facette um ein Zeichenfolgenfeld *Color* (Farbe) handelt, sind die Facetten die Werte „blau“, „grün“ usw. für dieses Feld.

Als bewährte Methode sollten Sie die Felder auf NULL-Werte, Rechtschreibfehler oder Abweichungen sowie auf Singular- und Pluralversionen desselben Worts überprüfen. Filter und Facetten durchlaufen keine lexikalische Analyse oder [Rechtschreibprüfung](speller-how-to-add.md). Das bedeutet, dass alle Werte eines Felds mit dem Attribut „facetable“ potenzielle Facetten sind, auch wenn sich die Wörter nur um ein Zeichen unterscheiden.

### <a name="defaults-in-rest-and-azure-sdks"></a>Standardwerte in REST- und Azure-SDKs

Wenn Sie eines der Azure-SDKs verwenden, müssen in Ihrem Code alle Feldattribute angegeben werden. Im Gegensatz dazu verfügt die REST-API über Standardwerte für Feldattribute, die auf dem [Datentyp](/rest/api/searchservice/supported-data-types) basieren. Die folgenden Datentypen sind standardmäßig „filterbar“ und „facettierbar“:

* `Edm.String`
* `Edm.DateTimeOffset`
* `Edm.Boolean`
* `Edm.Int32`, `Edm.Int64`, `Edm.Double`
* Sammlungen der obigen Typen (z. B. `Collection(Edm.String)` oder `Collection(Edm.Double)`)

`Edm.GeographyPoint`- oder `Collection(Edm.GeographyPoint)`-Felder eignen sich nicht für die Facettennavigation. Facetten funktionieren am besten mit Feldern mit einer geringen Kardinalität. Aufgrund der Auflösung von Geokoordinaten kommt es nur selten vor, dass zwei beliebige Gruppen von Koordinaten in einem gegebenen Dataset identisch sind. Daher werden Facets für Geokoordinaten nicht unterstützt. Sie benötigen ein Feld für einen Ort oder eine Region, um das Facettieren nach dem Standort durchzuführen.

> [!Tip]
> Die bewährte Methode zur Leistungs- und Speicheroptimierung besteht darin, das Faceting für Felder zu deaktivieren, die niemals als Facet genutzt werden sollten. Insbesondere Zeichenfolgenfelder für eindeutige Werte, z. B. eine ID oder ein Produktname, sollten auf `"facetable": false` festgelegt werden, damit sie nicht versehentlich (und ineffektiv) für die Facettennavigation verwendet werden. Dies gilt insbesondere für die REST-API, die standardmäßig Filter und Facetten aktiviert.

## <a name="facet-request-and-response"></a>Facettenanforderung und -antwort

Facetten werden in der Abfrage angegeben, und die Facettennavigationsstruktur wird oben in der Antwort zurückgegeben. Die Struktur einer Anforderung und einer Antwort ist relativ einfach. Tatsächlich erfolgt die eigentliche Arbeit bei der Facettennavigation auf der Präsentationsebene, die in einem späteren Abschnitt behandelt wird. 

Beim folgenden REST-Beispiel handelt es sich um eine nicht qualifizierte Abfrage (`"search": "*"`), die auf dem gesamten Index (siehe [integriertes Beispiel „Hotels“](search-get-started-portal.md)) basiert. Facetten sind in der Regel eine Liste von Feldern, aber bei dieser Abfrage wird zur besseren Lesbarkeit der Antwort nur eine angezeigt.

```http
POST https://{{service_name}}.search.windows.net/indexes/hotels/docs/search?api-version={{api_version}}
{
    "search": "*",
    "queryType": "simple",
    "select": "",
    "searchFields": "",
    "filter": "",
    "facets": [ "Category"], 
    "orderby": "",
    "count": true
}
```

Es ist hilfreich, eine Suchseite mit einer offenen Abfrage zu initialisieren, um die Facettennavigationsstruktur vollständig auszufüllen. Sobald Sie in der Anforderung Abfragebegriffe übergeben, wird die Facettennavigationsstruktur auf die Übereinstimmungen in den Ergebnissen und nicht mehr auf den gesamten Index angewandt.

Die Antwort für das obige Beispiel enthält die Facettennavigationsstruktur oben. Die Struktur besteht aus „Category“-Werten und einer Anzahl der Hotels für jede Kategorie. Darauf folgen die restlichen Suchergebnisse, die hier gekürzt dargestellt werden. Dieses Beispiel funktioniert aus verschiedenen Gründen gut. Die Anzahl der Facetten für dieses Feld liegt unter dem Grenzwert (der Standardwert ist 10), sodass alle angezeigt werden. Außerdem wird jedes Hotel im Index der 50 Hotels in genau einer dieser Kategorien dargestellt.

```json
{
    "@odata.context": "https://demo-search-svc.search.windows.net/indexes('hotels')/$metadata#docs(*)",
    "@odata.count": 50,
    "@search.facets": {
        "Category": [
            {
                "count": 13,
                "value": "Budget"
            },
            {
                "count": 12,
                "value": "Resort and Spa"
            },
            {
                "count": 9,
                "value": "Luxury"
            },
            {
                "count": 7,
                "value": "Boutique"
            },
            {
                "count": 5,
                "value": "Suite"
            },
            {
                "count": 4,
                "value": "Extended-Stay"
            }
        ]
    },
    "value": [
        {
            "@search.score": 1.0,
            "HotelId": "1",
            "HotelName": "Secret Point Motel",
            "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
            "Category": "Boutique",
            "Tags": [
                "pool",
                "air conditioning",
                "concierge"
            ],
            "ParkingIncluded": false,
  . . .
```

## <a name="facets-syntax"></a>Facettensyntax

Ein Facettenabfrageparameter wird auf eine durch Trennzeichen getrennte Liste von Feldern mit dem Attribut „facetable“ festgelegt. Je nach Datentyp ist dann eine weiterführende Parametrisierung möglich, um Anzahl, Sortierreihenfolgen und Bereiche festzulegen: `count:<integer>`, `sort:<>`, `interval:<integer>` und `values:<list>`. Weitere Informationen zu Facettenparametern finden Sie unter [„Abfrageparameter“ in der REST-API](/rest/api/searchservice/search-documents#query-parameters).

```http
POST https://{{service_name}}.search.windows.net/indexes/hotels/docs/search?api-version={{api_version}}
{
    "search": "*",
    "facets": [ "Category", "Tags,count:5", "Rating,values:1|2|3|4|5"],
    "count": true
}
```

In jeder Facettennavigationsstruktur gilt ein Standardgrenzwert von zehn Facetten. Dieser Standardwert ist für Navigationsstrukturen sinnvoll, da die Werteliste dadurch überschaubar bleibt. Sie können den Standardwert überschreiben, indem Sie einen Wert für „count“ zuweisen. `"Tags,count:5"` reduziert z. B. die Anzahl der Tags im Abschnitt „Tags“ auf die ersten fünf.

Nur für numerische und DateTime-Werte können Sie explizit Werte für das Facettenfeld (z. B. `facet=Rating,values:1|2|3|4|5`) festlegen, um Ergebnisse in zusammenhängende Bereiche (basierend auf numerischen Werten oder Zeiträumen) zu untergliedern. Alternativ können Sie „interval:“ hinzufügen, wie z. B. in `facet=Rating,interval:1`. 

Jeder Bereich wird mit „0“ als Ausgangspunkt und einem Wert aus der Liste als Endpunkt erstellt und anschließend um den vorherigen Bereich gekürzt, um eigenständige Intervalle zu erhalten.

### <a name="discrepancies-in-facet-counts"></a>Abweichungen bei der Facettenanzahl

Unter bestimmten Umständen kann es vorkommen, dass die Facettenanzahl nicht den Resultsets entspricht (mehr auf der Q&A-Seite von Microsoft unter [Facettennavigation in Azure Cognitive Search](/answers/topics/azure-cognitive-search.html)).

Die Abweichung der Facettenanzahl kann auf die Sharding-Architektur zurückzuführen sein. Jeder Suchindex besitzt mehrere Shards, von denen jeweils die x relevantesten Facetten (auf der Grundlage der Dokumentanzahl) zurückgegeben werden. Diese werden dann zu einem einzelnen Ergebnis zusammengefasst. Falls nun für einige Shards viele und für andere Shards weniger Werte vorhanden sind, kann es vorkommen, dass einige Facettenwerte fehlen oder in den Ergebnissen nicht korrekt erfasst wurden.

Dieses Verhalten kann sich zwar jederzeit ändern, wenn Sie jedoch aktuell davon betroffen sind, können Sie das Problem umgehen, indem Sie „count:\<number>“ künstlich auf eine große Zahl erhöhen, um vollständige Berichte aus jeder Shard zu erzwingen. Wenn der Wert von „count:“ größer oder gleich der Anzahl eindeutiger Werte im Feld ist, erhalten Sie garantiert exakte Ergebnisse. Bei einer hohen Anzahl von Dokumenten ist jedoch mit Leistungseinbußen zu rechnen. Verwenden Sie die Option daher mit Bedacht.

## <a name="presentation-layer"></a>Darstellungsschicht

Im Anwendungscode werden Facettenabfrageparameter für die Rückgabe der Facettennavigationsstruktur und der Facettenergebnisse sowie ein $filter-Ausdruck verwendet.  Der Filterausdruck behandelt das Klickereignis und schränkt das Suchergebnis basierend auf der Facettenauswahl weiter ein.

### <a name="facet-and-filter-combination"></a>Kombinieren von Facetten und Filtern

Mit dem folgenden Codeausschnitt aus der Datei `JobsSearch.cs` im Demo „NYCJobs“ wird die Berufsbezeichnung dem Filter hinzugefügt, wenn Sie einen Wert aus der Berufsbezeichnungsfacette („business_title“) auswählen.

```cs
if (businessTitleFacet != "")
  filter = "business_title eq '" + businessTitleFacet + "'";
```

Hier ist ein weiteres Beispiel aus dem Hotelbeispiel. Mit dem folgenden Codeausschnitt wird categoryFacet dem Filter hinzugefügt, wenn Benutzer*innen einen Wert aus der Facette „category“ auswählen.

```csharp
if (!String.IsNullOrEmpty(categoryFacet))
    filter = $"category eq '{categoryFacet}'";
```

### <a name="html-for-faceted-navigation"></a>HTML für die Facettennavigation

Das folgende Beispiel stammt aus der Datei `index.cshtml` der Beispielanwendung NYCJobs und zeigt die statische HTML-Struktur zum Anzeigen einer Facettennavigation auf der Suchergebnisseite. Die Liste der Facetten wird erstellt oder dynamisch neu erstellt, wenn Sie einen Suchbegriff übermitteln oder eine Facette aktivieren bzw. deaktivieren.

```html
<div class="widget sidebar-widget jobs-filter-widget">
  <h5 class="widget-title">Filter Results</h5>
    <p id="filterReset"></p>
    <div class="widget-content">

      <h6 id="businessTitleFacetTitle">Business Title</h6>
      <ul class="filter-list" id="business_title_facets">
      </ul>

      <h6>Location</h6>
      <ul class="filter-list" id="posting_type_facets">
      </ul>

      <h6>Posting Type</h6>
      <ul class="filter-list" id="posting_type_facets"></ul>

      <h6>Minimum Salary</h6>
      <ul class="filter-list" id="salary_range_facets">
      </ul>

  </div>
</div>
```

### <a name="build-html-dynamically"></a>Dynamisches Erstellen von HTML-Code

Der folgende Codeausschnitt aus der Datei `index.cshtml` (ebenfalls aus der Demo NYCJobs) erstellt dynamisch den HTML-Code, um die erste Facette mit der Berufsbezeichnung anzuzeigen. Ähnliche Funktionen erstellen dynamisch den HTML-Code für die anderen Facetten. Jede Facette weist eine Bezeichnung und einen Zähler auf, der die Anzahl von für dieses Facettenergebnis gefundenen Elemente anzeigt.

```js
function UpdateBusinessTitleFacets(data) {
  var facetResultsHTML = '';
  for (var i = 0; i < data.length; i++) {
    facetResultsHTML += '<li><a href="javascript:void(0)" onclick="ChooseBusinessTitleFacet(\'' + data[i].Value + '\');">' + data[i].Value + ' (' + data[i].Count + ')</span></a></li>';
  }

  $("#business_title_facets").html(facetResultsHTML);
}
```

## <a name="tips-for-working-with-facets"></a>Tipps für die Arbeit mit Facetten

Dieser Abschnitt enthält eine Sammlung von Tipps und Problemumgehungen, die eventuell hilfreich sein können.

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>Beibehalten einer Struktur für die Facetnavigation asynchron zu gefilterten Ergebnissen

Eine der Herausforderungen bei der Facettennavigation in Azure Cognitive Search besteht darin, dass Facetten nur für aktuelle Ergebnisse vorhanden sind. In der Praxis ist die Beibehaltung einer statischen Gruppe von Facets üblich, damit Benutzer die Navigation in umgekehrter Richtung durchführen und die Schritte nachvollziehen können, um andere Wege durch die Suchinhalte zu erkunden. 

Dies ist zwar ein häufiger Anwendungsfall, aber dieser Vorgang ist noch nicht standardmäßig in der Facettennavigationsstruktur enthalten. Entwickler, die statische Facets benötigen, umgehen diese Einschränkung normalerweise, indem sie zwei gefilterte Abfragen durchführen: eine für die Ergebnisse und die andere zum Erstellen einer statischen Liste mit Facets zu Navigationszwecken.

### <a name="clear-facets"></a>Löschen von Facetten

Denken Sie bei der Entwicklung der Suchergebnisseite daran, einen Mechanismus zum Deaktivieren von Facetten hinzuzufügen. Wenn Sie Kontrollkästchen hinzufügen, können Sie ganz einfach anzeigen, wie die Filter gelöscht werden. Bei einem anderen Layout benötigen Sie unter Umständen eine Breadcrumb-Struktur oder einer anderen kreativen Ansatz. Im C#-Beispiel „hotels“ können Sie eine leere Suche senden, um die Seite zurückzusetzen. Im Gegensatz dazu stellt die Beispielanwendung NYCJobs nach einer ausgewählten Facette ein klickbares `[X]` bereit, um die Facette zu löschen. Dies ist ein deutlicherer visueller Hinweis für die Benutzer*innen.

### <a name="trim-facet-results-with-more-filters"></a>Kürzen Sie Facettenergebnisse mit weiteren Filtern

Facettenergebnisse sind Dokumente in den Suchergebnissen, die einem Facettenbegriff entsprechen. Im folgenden Beispiel weisen 254 Elemente der Suchergebnisse für *cloud computing* auch den Inhaltstyp *interne Spezifikation* auf. Die Elemente schließen sich nicht zwingend gegenseitig aus. Erfüllt ein Element die Kriterien beider Filter, wird es in jedem Filter gezählt. Diese Duplizierung ist möglich, wenn die Facette auf Feldern vom Typ `Collection(Edm.String)` basiert, die häufig zur Implementierung von Dokumententags verwendet werden.

```output
Search term: "cloud computing"
Content type
   Internal specification (254)
   Video (10)
```

Allgemein gilt: Wenn die Facettenergebnisse ständig zu umfangreich ausfallen, empfiehlt es sich, weitere Filter hinzuzufügen, um den Benutzern mehr Optionen zum Eingrenzen der Suche zu geben.

### <a name="a-facet-only-search-experience"></a>Eine Suchfunktion ausschließlich mit Facetten

Wenn Ihre Anwendung kein Suchfeld, sondern ausschließlich die Facettennavigation aufweist, können Sie das Feld mit `searchable=false`, `filterable=true` und `facetable=true` markieren, um einen kompakteren Index zu erhalten. Ihr Index enthält keine invertierten Indizes, und es wird keine Textanalyse oder Tokenisierung durchgeführt. Filter werden für genaue Übereinstimmungen auf Zeichenebene angewandt.

### <a name="validate-inputs-at-query-time"></a>Überprüfen von Eingaben zur Abfragezeit

Wenn Sie die Facettenliste dynamisch auf der Grundlage nicht vertrauenswürdiger Benutzereingaben erstellen, stellen Sie sicher, dass die Namen der Facettenfelder gültig sind, oder versehen Sie die Namen bei der URL-Erstellung mit Escapezeichen – entweder mithilfe von `Uri.EscapeDataString()` in .NET oder mithilfe einer Entsprechung für die gewählte Plattform.

## <a name="demos-and-samples"></a>Demos und Beispiele

Es gibt mehrere Beispiele, die eine Facettennavigation enthalten. In diesem Abschnitt finden Sie Links zu den Beispielen und Hinweise dazu, welche Clientbibliothek und Sprache jeweils verwendet wird.

### <a name="create-your-first-app-in-c-razor"></a>Erstellen Ihrer ersten App in C# (Razor)

Dieses Tutorial und die Beispielreihe in C# enthalten eine [Lektion mit Fokus auf der Facettennavigation](tutorial-csharp-facets.md). Die Lösung ist eine ASP.NET-MVC-App, und die Präsentationsebene verwendet die Razor-Clientbibliotheken.

### <a name="add-search-to-web-apps-react"></a>Hinzufügen von Suchfunktionen zu Web-Apps (React)

Tutorials und Beispiele in [C#](tutorial-csharp-overview.md), [Python](tutorial-python-overview.md) und [JavaScript](tutorial-javascript-overview.md) enthalten Facettennavigation sowie Filter, Vorschläge und AutoVervollständigen. Diese Beispiele verwenden React auf der Präsentationsebene.

### <a name="nycjobs-sample-code-and-demo-ajax"></a>Beispielcode und Demo NYCJobs (Ajax)

Das NYCJobs-Beispiel ist eine ASP.NET-MVC-Anwendung, die auf der Präsentationsebene Ajax verwendet. Es ist als [Live-Demo-App](https://aka.ms/azjobsdemo) und als Quellcode im [Repository „Azure-Samples“ auf GitHub](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) verfügbar.
