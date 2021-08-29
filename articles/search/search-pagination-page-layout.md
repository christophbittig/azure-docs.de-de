---
title: Arbeiten mit Suchergebnissen
titleSuffix: Azure Cognitive Search
description: Strukturieren und Sortieren von Suchergebnissen, Abrufen der Dokumentanzahl und Hinzufügen einer Inhaltsnavigation zu den Suchergebnissen in der kognitiven Azure-Suche.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.openlocfilehash: e810a5d3a94ad4e356d0b44a422b757b5710a781
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2021
ms.locfileid: "122342986"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>Arbeiten mit Suchergebnissen in der kognitiven Azure-Suche

In diesem Artikel wird das Formulieren einer Abfrageantwort in Azure Cognitive Search erläutert. Die Struktur einer Antwort wird durch Parameter in der Abfrage bestimmt: [Suchdokument](/rest/api/searchservice/Search-Documents) in der REST-API oder [SearchResults-Klasse](/dotnet/api/azure.search.documents.models.searchresults-1) im .NET SDK. Die Parameter der Abfrage können verwendet werden, um das Resultset wie folgt zu strukturieren:

+ Begrenzen der Anzahl von Dokumenten in den Ergebnissen (standardmäßig 50) bzw. Zusammenfassen zu Batches
+ Auswählen der Felder, die in den Ergebnissen enthalten sein sollen
+ Sortieren der Ergebnisse
+ Hervorheben eines übereinstimmenden vollständigen Begriffs oder Teilbegriffs in den Suchergebnissen

## <a name="result-composition"></a>Ergebniszusammensetzung

Ein Suchdokument kann zwar aus einer Vielzahl von Feldern bestehen. In der Regel werden jedoch nur wenige zur Darstellung der einzelnen Dokumente im Resultset benötigt. Fügen Sie bei einer Abfrageanforderung `$select=<field list>` an, um anzugeben, welche Felder in der Antwort angezeigt werden. Ein Feld muss im Index durch das Attribut **retrievable** gekennzeichnet sein, damit es in ein Ergebnis einbezogen wird. 

Besonders gut eignen sich Felder, die Dokumente vergleichen und zwischen Dokumenten unterscheiden und so genügend Informationen liefern, um den Benutzer zu einer Antwort durch Klicken einzuladen. Auf einer E-Commerce-Website kann dies ein Produktname, eine Beschreibung, eine Marke, eine Farbe, eine Größe, ein Preis oder eine Bewertung sein. Bei dem integrierten Beispiel „hotels-sample-index“ können dies die Felder im folgenden Beispiel sein:

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30 
    {  
      "search": "sandy beaches",
      "select": "HotelId, HotelName, Description, Rating, Address/City"
      "count": true
    }
```

> [!NOTE]
> Wenn Sie in ein Ergebnis Bilddateien wie etwa ein Produktfoto oder ein Logo einbeziehen möchten, speichern Sie diese außerhalb von Azure Cognitive Search. Fügen Sie jedoch in Ihren Index ein Feld ein, das auf die Bild-URL im Suchdokument verweist. Beispielindizes, die in Ergebnissen Bilder unterstützen, enthalten die Demo **realestate-sample-us**, die in dieser [Schnellstartanleitung](search-create-app-portal.md) vorgestellt wird, sowie die Demo-App [New York City Jobs](https://aka.ms/azjobsdemo).

### <a name="tips-for-unexpected-results"></a>Tipps zu unerwarteten Ergebnissen

Es kann vorkommen, dass der Inhalt und nicht die Struktur der Ergebnisse unerwartet ist. Wenn die Abfrageergebnisse nicht wie erwartet ausfallen, können Sie es mit diesen Änderungen der Abfragen versuchen, um zu ermitteln, ob sich die Ergebnisqualität verbessert:

+ Ändern Sie **`searchMode=any`** (Standard) in **`searchMode=all`** , damit Übereinstimmungen mit allen Kriterien erforderlich sind, nicht nur mit einem oder mehreren Kriterien. Dies gilt besonders, wenn die Abfrage boolesche Operatoren enthält.

+ Experimentieren Sie mit verschiedenen lexikalischen oder benutzerdefinierten Analysetools, um festzustellen, ob sich das Abfrageergebnis ändert. Das Standardanalysetool unterbricht Wörter mit Bindestrichen und reduziert die Wörter auf ihre Stammformen. Dadurch wird die Stabilität einer Abfrageantwort in der Regel verbessert. Wenn Sie jedoch Bindestriche beibehalten müssen oder wenn die Zeichenfolgen Sonderzeichen enthalten, müssen Sie eventuell benutzerdefinierte Analysetools konfigurieren, um sicherzustellen, dass der Index Token im richtigen Format enthält. Weitere Informationen finden Sie unter [Suche nach Teilausdrücken und Mustern mit Sonderzeichen (Bindestriche, Platzhalter, reguläre Ausdrücke, Muster)](search-query-partial-matching.md).

## <a name="paging-results"></a>Auslagerungsergebnisse

Das Suchmodul gibt standardmäßig die durch die Suchbewertung bestimmten ersten 50 Treffer zurück, wenn es sich bei der Abfrage um eine Volltextsuche handelt. Bei Abfragen mit dem Kriterium der genauen Übereinstimmung werden die Treffer in beliebiger Reihenfolge zurückgegeben.

Wenn eine andere Anzahl an übereinstimmenden Dokumenten zurückgegeben werden soll, fügen Sie der Abfrageanforderung die Parameter `$top` und `$skip` hinzu. In der folgenden Liste wird die Logik erläutert.

+ Fügen Sie `$count=true` hinzu, um die Gesamtzahl von übereinstimmenden Dokumenten abzurufen.

+ Damit wird die erste Gruppe von 15 übereinstimmenden Dokumenten sowie die Gesamtzahl der Übereinstimmungen zurückgegeben: `GET /indexes/<INDEX-NAME>/docs?search=<QUERY STRING>&$top=15&$skip=0&$count=true`

+ Damit wird die zweite Gruppe zurückgegeben, wobei die ersten 15 übersprungen und die nächsten 15 abgerufen werden: `$top=15&$skip=15`. Damit geschieht dasselbe für die dritte Gruppe von 15 Dokumenten: `$top=15&$skip=30`

Es ist nicht sicher, dass die Ergebnisse von paginierten Abfragen stabil sind, wenn sich der zugrunde liegende Index ändert. Beim Paging ändert sich der Wert von `$skip` für die einzelnen Seiten. Dabei sind die einzelnen Abfragen jedoch unabhängig und werden für die aktuelle Ansicht der Daten ausgeführt, die im Index zur Abfragezeit vorhanden sind. (Das bedeutet, es gibt kein Zwischenspeichern bzw. keine Momentaufnahmen der Ergebnisse wie etwa bei universellen Datenbanken.)
 
Mit dem folgenden Beispiel werden möglicherweise Duplikate zurückgegeben. Stellen Sie sich einen Index mit vier Dokumenten vor:

```text
{ "id": "1", "rating": 5 }
{ "id": "2", "rating": 3 }
{ "id": "3", "rating": 2 }
{ "id": "4", "rating": 1 }
```
 
Stellen Sie sich nun vor, es sollen immer je zwei Ergebnisse nach Bewertung sortiert zurückgegeben werden. Sie würden die folgende Abfrage ausführen, um die erste Seite mit Ergebnissen abzurufen: `$top=2&$skip=0&$orderby=rating desc`. Damit werden die folgenden Ergebnisse erzeugt:

```text
{ "id": "1", "rating": 5 }
{ "id": "2", "rating": 3 }
```
 
Stellen Sie sich nun vor, dass bei dem Dienst zwischen den Abfragen `{ "id": "5", "rating": 4 }` als fünftes Dokument in den Index aufgenommen wird.  Kurze Zeit später führen Sie eine Abfrage aus, um die zweite Seite abzurufen: `$top=2&$skip=2&$orderby=rating desc`. Damit werden die folgenden Ergebnisse abgerufen:

```text
{ "id": "2", "rating": 3 }
{ "id": "3", "rating": 2 }
```
 
Wie Sie sehen, wurde Dokument 2 zweimal abgerufen. Der Grund dafür ist, dass das neue Dokument 5 einen größeren Wert für die Bewertung aufweist, sodass es in der Reihenfolge vor Dokument 2 kommt und daher auf der ersten Seite landet. Dieses Verhalten ist zwar möglicherweise unerwartet, jedoch typisch für das Verhalten eines Suchmoduls.

## <a name="ordering-results"></a>Sortieren von Ergebnissen

Bei Volltextsuchabfragen werden Ergebnisse automatisch nach einer Suchbewertung geordnet, die basierend auf Begriffshäufigkeit und -nähe berechnet wird (abgeleitet von [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)), wobei Dokumente mit mehr oder stärkeren Übereinstimmungen bei einem Suchbegriff höher bewertet werden. 

Suchergebnisse vermitteln einen allgemeinen Eindruck von Relevanz, der die Stärke der Übereinstimmung im Vergleich zu anderen Dokumenten im selben Resultset widerspiegelt. Bewertungen sind jedoch von einer Abfrage zur nächsten nicht immer konsistent, sodass Sie möglicherweise geringfügige Abweichungen in der Reihenfolge der Suchdokumente feststellen werden. Hierfür kann es verschiedene Ursachen geben.

| Ursache | BESCHREIBUNG |
|-----------|-------------|
| Datenflüchtigkeit | Der Indexinhalt variiert, wenn Sie Dokumente hinzufügen, ändern oder löschen. Begriffshäufigkeiten ändern sich, wenn im Laufe der Zeit Indexaktualisierungen verarbeitet werden, die sich auf die Suchbewertungen für übereinstimmende Dokumente auswirken. |
| Mehrere Replikate | Bei Diensten, die mehrere Replikate verwenden, werden Abfragen parallel für jedes Replikat ausgegeben. Die zum Berechnen einer Suchbewertung verwendete Indexstatistik wird pro Replikat berechnet, wobei die Ergebnisse zusammengeführt und in der Abfrageantwort sortiert werden. Replikate sind größtenteils Spiegelungen. Statistiken können sich jedoch aufgrund geringfügiger Zustandsabweichungen voneinander unterscheiden. So könnten beispielsweise in einem Replikat Dokumente gelöscht worden sein, die aus anderen Replikaten zusammengeführt wurden, was zur jeweiligen Statistik beiträgt. Unterschiede in Statistiken, die pro Replikat berechnet werden, fallen in kleineren Indizes deutlicher auf. |
| Identische Bewertungen | Wenn mehrere Dokumente dieselbe Bewertung aufweisen, wird eine davon zuerst angezeigt.  |

### <a name="how-to-get-consistent-ordering"></a>Sicherstellen einer konsistenten Reihenfolge

Wenn eine konsistente Reihenfolge eine Anforderung der Anwendung ist, können Sie explizit einen [ **`$orderby`** -Ausdruck](query-odata-filter-orderby-syntax.md) in einem Feld definieren. Nur Felder, die als **`sortable`** indiziert sind, können zum Sortieren von Ergebnissen verwendet werden. Felder, die häufig in **`$orderby`** verwendet werden, sind „rating“, „date“ und „location“, wenn Sie den Wert des **`orderby`** -Parameters angeben, um Feldnamen und Aufrufe der [ **`geo.distance()`-Funktion**](query-odata-filter-orderby-syntax.md) für räumliche Daten einzuschließen.

Einen weiteren Ansatz für mehr Konsistenz stellt die Verwendung eines [benutzerdefinierten Bewertungsprofils](index-add-scoring-profiles.md) dar. Mit Bewertungsprofilen haben Sie mehr Kontrolle über die Bewertung von Elementen in Suchergebnissen und können in bestimmten Feldern gefundene Übereinstimmungen aufwerten. Mithilfe der zusätzlichen Bewertungslogik können geringfügige Unterschiede zwischen Replikaten außer Kraft gesetzt werden, da die Suchbewertungen für die einzelnen Dokumente weiter auseinander liegen. Für diesen Ansatz wird der [Rangfolgenalgorithmus](index-ranking-similarity.md) empfohlen.

## <a name="hit-highlighting"></a>Treffermarkierung

Die Treffermarkierung bezieht sich auf die Textformatierung (wie Fett- oder Gelbmarkierung), die auf Treffer in einem Ergebnis angewendet werden, sodass die Übereinstimmungen leicht zu erkennen sind. Anweisungen zur Treffermarkierung werden in der [Abfrageanforderung](/rest/api/searchservice/search-documents) bereitgestellt. 

Um die Treffermarkierung zu aktivieren, fügen Sie `highlight=[comma-delimited list of string fields]` hinzu, um anzugeben, für welche Felder die Markierung verwendet wird. Die Markierung eignet sich für längere Inhaltsfelder, z. B. ein Beschreibungsfeld, bei denen die Übereinstimmung nicht sofort ersichtlich ist. Für die Treffermarkierung eignen sich nur Felddefinitionen, die das Attribut **durchsuchbar** aufweisen.

Azure Cognitive Search gibt standardmäßig bis zu fünf Markierungen pro Feld zurück. Sie können die Anzahl anpassen, indem Sie einen Bindestrich gefolgt von einer ganzen Zahl an das Feld anfügen. `highlight=Description-10` gibt beispielsweise bis zu 10 Markierungen zu übereinstimmenden Inhalten im Beschreibungsfeld zurück.

Die Formatierung wird auf Abfragen kompletter Begriffe angewendet. Der Formatierungstyp wird durch Tags (`highlightPreTag` und `highlightPostTag`) bestimmt, und die Antwort wird vom Code verarbeitet (z. B., wenn eine Schriftart fett formatiert oder ein gelber Hintergrund angewendet werden soll).

Im folgenden Beispiel sind die Begriffe „sandy“, „sand“, „beaches“ und „beach“ im Beschreibungsfeld zur Markierung mit Tags versehen. Bei Abfragen, die eine Abfrageerweiterung in der Engine auslösen, wie z. B. Fuzzy- und Platzhaltersuche, wird die Treffermarkierung nur begrenzt unterstützt.

```http
GET /indexes/hotels-sample-index/docs/search=sandy beaches&highlight=Description?api-version=2020-06-30 
```

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30 
    {  
      "search": "sandy beaches",  
      "highlight": "Description"
    }
```

### <a name="new-behavior-starting-july-15"></a>Neues Verhalten (ab 15. Juli)

Dienste, die nach dem 15. Juli 2020 erstellt werden, verfügen über ein anderes Markierungsverhalten. Bei Diensten, die vor diesem Datum erstellt werden, bleibt das Markierungsverhalten unverändert. 

Für das neue Verhalten gilt:

+ Es werden nur Ausdrücke zurückgegeben, die der vollständigen Ausdrucksabfrage entsprechen. Der Abfrageausdruck „super bowl“ führt zu Markierungen wie diesen:

  ```json
  "@search.highlights": {
      "sentence": [
          "The <em>super</em> <em>bowl</em> is super awesome with a bowl of chips"
     ]
  ```

  Beachten Sie, dass andere Instanzen von *super* und *bowl* keine Markierungen aufweisen, da diese Instanzen nicht mit dem vollständigen Ausdruck identisch sind.

Berücksichtigen Sie diese Änderung, wenn Sie Clientcode mit einer Treffermarkierungsimplementierung schreiben. Beachten Sie, dass dies nur dann für Sie relevant ist, wenn Sie einen vollständig neuen Suchdienst erstellen.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie schnell eine Suchseite für Ihren Client erstellen möchten, probieren Sie die folgenden Optionen aus:

+ Mit dem [Anwendungsgenerator](search-create-app-portal.md) im Portal können Sie eine HTML-Seite mit einer Suchleiste, einer Facettennavigation und einem Ergebnisbereich erstellen.
+ [Erstellen der ersten App in C#](tutorial-csharp-create-first-app.md) ist ein Tutorial, mit dem Sie einen funktionalen Client erstellen können. Der Beispielcode veranschaulicht paginierte Abfragen, Treffermarkierung und Sortierung.

Einige Codebeispiele beinhalten eine Web-Front-End-Schnittstelle, die Sie hier finden: [Jobs in New York City (Demo-App)](https://aka.ms/azjobsdemo) oder [JavaScript-Beispielcode mit einer Livedemo-Website](https://github.com/liamca/azure-search-javascript-samples).
