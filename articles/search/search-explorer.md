---
title: 'Schnellstart: Suchexplorer-Abfragetool'
titleSuffix: Azure Cognitive Search
description: Der Suchexplorer ist ein Abfragetool im Azure-Portal, das Abfrageanforderungen an einen Suchindex in Azure Cognitive Search sendet. Verwenden Sie es, um die Syntax zu erlernen, Abfrageausdrücke zu testen oder ein Suchdokument zu prüfen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 08/24/2021
ms.openlocfilehash: d246c9aad024b1086a531c31a2a9559dfa798642
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772828"
---
# <a name="quickstart-use-search-explorer-to-run-queries-in-the-portal"></a>Schnellstart: Verwenden des Suchexplorers zum Ausführen von Abfragen im Portal

Der **Suchexplorer** ist ein integriertes Abfragetool im Azure-Portal, das zum Ausführen von Abfragen über einen Suchindex in Azure Cognitive Search verwendet wird. Mit diesem Tool können Sie problemlos Abfragesyntax erlernen, einen Abfrage- oder Filterausdruck testen oder eine Datenaktualisierung bestätigen, indem Sie überprüfen, ob neue Inhalte im Index vorhanden sind.

In diesem Schnellstart wird der Suchexplorer anhand eines vorhandenen Index veranschaulicht. 

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen können, müssen die folgenden Voraussetzungen erfüllt werden:

+ Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/).

+ Ein Azure Cognitive Search-Dienst [Erstellen Sie einen Dienst](search-create-service-portal.md), oder suchen Sie in Ihrem aktuellen Abonnement [nach einem vorhandenen Dienst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Für diesen Schnellstart können Sie einen kostenlosen Dienst verwenden. 

+ *realestate-us-sample-index* wird für diesen Schnellstart verwendet. Verwenden Sie die Informationen unter [Schnellstart: Erstellen eines Index](search-import-data-portal.md), um den Index mit Standardwerten zu erstellen. Über eine integrierte Beispieldatenquelle (**realestate-us-sample**), die von Microsoft gehostet wird, werden die Daten bereitgestellt.

## <a name="start-search-explorer"></a>Starten des Suchexplorers

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) im Dashboard die Übersichtsseite für die Suche, oder [suchen Sie nach Ihrem Dienst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

1. Öffnen Sie den Suchexplorer über die Befehlsleiste:

   :::image type="content" source="media/search-explorer/search-explorer-cmd2.png" alt-text="Befehl für den Suchexplorer im Portal" border="true":::

    Oder verwenden Sie die eingebettete Registerkarte **Suchexplorer** in einem geöffneten Index:

   :::image type="content" source="media/search-explorer/search-explorer-tab.png" alt-text="Registerkarte „Suchexplorer“" border="true":::

## <a name="unspecified-query"></a>Abfrage ohne Angabe

Im Suchexplorer werden Anforderungen mit der [Search-REST-API](/rest/api/searchservice/search-documents) formuliert, und Antworten werden als ausführliche JSON-Dokumente zurückgegeben.

Führen Sie für einen ersten Blick auf den Inhalt eine leere Suche aus, indem Sie ohne Angabe von Begriffen auf **Suchen** klicken. Eine leere Suche ist eine sinnvolle erste Abfrage, da sie vollständige Dokumente zurückgibt, sodass Sie sich mit dem Aufbau des Dokuments vertraut machen können. Bei einer leeren Suche gibt es keinen Suchrang, und die Dokumente werden in beliebiger Reihenfolge (`"@search.score": 1` für alle Dokumente) zurückgegeben. Standardmäßig werden in einer Suchanforderung 50 Dokumente zurückgegeben.

Die äquivalente Syntax für eine leere Suche ist `*` oder `search=*`.
   
   ```http
   search=*
   ```

   **Ergebnisse**
   
   :::image type="content" source="media/search-explorer/search-explorer-example-empty.png" alt-text="Beispiel für unqualifizierte oder leere Abfrage" border="true":::

## <a name="free-text-search"></a>Freitextsuche

Freiformabfragen mit oder ohne Operatoren sind nützlich zum Simulieren von benutzerdefinierten Abfragen, die von einer benutzerdefinierten App an die kognitive Azure-Suche gesendet werden. Nur die Felder, die in der Indexdefinition als **Durchsuchbar** gekennzeichnet sind, werden auf Übereinstimmungen überprüft. 

Beachten Sie, dass bei der Angabe von Suchkriterien wie z. B. Abfragebegriffen oder -ausdrücken der Suchrang eine Rolle spielt. Im folgenden Beispiel wird eine Freitextsuche veranschaulicht.

   ```http
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **Ergebnisse**

   Sie können die Ergebnisse mit STRG+F nach bestimmten Begriffen durchsuchen.

   :::image type="content" source="media/search-explorer/search-explorer-example-freetext.png" alt-text="Beispiel für Freitextabfrage" border="true":::

## <a name="count-of-matching-documents"></a>Anzahl übereinstimmender Dokumente 

Fügen Sie **$count=true** hinzu, um die Anzahl der Übereinstimmungen in einem Index abzurufen. Bei einer leeren Suche ist die Anzahl die Gesamtanzahl der Dokumente im Index. Bei einer qualifizierten Suche ist es die Anzahl der Dokumente, die der eingegebenen Abfrage entsprechen. Beachten Sie hierbei, dass vom Dienst standardmäßig die ersten 50 Übereinstimmungen zurückgegeben werden. Daher kann es sein, dass der Index mehr Übereinstimmungen enthält, als im Ergebnis aufgeführt werden.

   ```http
   $count=true
   ```

   **Ergebnisse**

   :::image type="content" source="media/search-explorer/search-explorer-example-count.png" alt-text="Beispiel für die Anzahl der übereinstimmenden Dokumente" border="true":::

## <a name="limit-fields-in-search-results"></a>Beschränken der Felder in den Suchergebnissen

Fügen Sie [ **$select**](search-query-odata-select.md) hinzu, um die Ergebnisse auf die explizit benannten Felder zu beschränken und damit die Lesbarkeit der Ausgabe im **Suchexplorer** zu erhöhen. Um die Suchzeichenfolge und **$count=true** beizubehalten, stellen Sie den Argumenten das Präfix **&** voran. 

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **Ergebnisse**

   :::image type="content" source="media/search-explorer/search-explorer-example-selectfield.png" alt-text="Beschränken der Felder in den Suchergebnissen" border="true":::

## <a name="return-next-batch-of-results"></a>Zurückgeben des nächsten Batches von Ergebnissen

Die kognitive Azure-Suche gibt die ersten 50 Übereinstimmungen basierend auf dem Suchrang zurück. Um den nächsten Satz von übereinstimmenden Dokumenten abzurufen, fügen Sie **$top=100,&$skip=50** an, um das Resultset auf 100 Dokumente zu vergrößern (Standardwert 50, Höchstwert 1.000) und die ersten 50 Dokumente zu überspringen. Sie können den Dokumentschlüssel (listingID) überprüfen, um ein Dokument zu identifizieren. 

Denken Sie daran, dass Sie Suchkriterien angeben müssen, z.B. einen Abfragebegriff oder -ausdruck, um priorisierte Ergebnisse zu erhalten. Beachten Sie, dass sich die Suchbewertungen verringern, je weiter Sie in die Suchergebnisse vordringen.

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100&$skip=50
   ```

   **Ergebnisse**

   :::image type="content" source="media/search-explorer/search-explorer-example-topskip.png" alt-text="Zurückgeben des nächsten Batches von Suchergebnissen" border="true":::

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>Filterausdrücke (größer als, kleiner als, gleich)

Verwenden Sie den Parameter [ **$filter**](search-query-odata-filter.md), wenn Sie anstelle einer Freitextsuche präzise Kriterien angeben möchten. Das Feld muss im Index als **Filterbar**  gekennzeichnet sein. In diesem Beispiel wird für die Anzahl von Schlafzimmern nach einem Wert größer als 3 gesucht:

   ```http
   search=seattle condo&$filter=beds gt 3&$count=true
   ```
   
   **Ergebnisse**

   :::image type="content" source="media/search-explorer/search-explorer-example-filter.png" alt-text="Filtern nach Kriterien" border="true":::

## <a name="order-by-expressions"></a>Sortierausdrücke

Fügen Sie [ **$orderby**](search-query-odata-orderby.md) hinzu, um die Ergebnisse nach einem anderen Feld als der Suchbewertung zu sortieren. Das Feld muss im Index als **Sortierbar**  gekennzeichnet sein. Als Beispielausdruck zum Testen können Sie den folgenden verwenden:

   ```http
   search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc
   ```
   
   **Ergebnisse**

   :::image type="content" source="media/search-explorer/search-explorer-example-ordery.png" alt-text="Ändern der Sortierreihenfolge" border="true":::

Die Ausdrücke **$filter** und **$orderby** sind OData-Konstrukte. Weitere Informationen finden Sie unter [OData Expression Syntax for Azure Search](/rest/api/searchservice/odata-expression-syntax-for-azure-search) (OData-Ausdruckssyntax für Azure Search).

<a name="start-search-explorer"></a>

## <a name="takeaways"></a>Wesentliche Punkte

In diesem Schnellstart haben Sie den **Suchexplorer** verwendet, um einen Index mithilfe der REST-API abzufragen.

+ Ergebnisse werden als ausführliche JSON-Dokumente zurückgegeben, sodass Sie den Dokumentaufbau und den Inhalt vollständig anzeigen können. Mit dem Parameter **$select** in einem Abfrageausdruck kann eingeschränkt werden, welche Felder zurückgegeben werden.

+ Dokumente bestehen aus allen Feldern, die im Index als **abrufbar** gekennzeichnet sind. Klicken Sie zum Anzeigen von Indexattributen im Portal auf der Übersichtsseite der Suche in der Liste **Indizes** auf *realestate-us-sample*.

+ Freiformabfragen ähneln der Eingabe in kommerziellen Webbrowsern und eignen sich zum Testen der Endbenutzererfahrung. Beim integrierten Beispielimmobilienindex könnten Sie z.B. „Seattle apartments lake washington“ eingeben und dann mit STRG+F Begriffe in den Suchergebnissen finden. 

+ Abfrage- und Filterausdrücke werden in einer Syntax formuliert, die von Azure Cognitive Search implementiert wird. Der Standardwert ist eine [einfache Syntax](/rest/api/searchservice/simple-query-syntax-in-azure-search), Sie können aber optional für leistungsstarke Abfragen die [vollständige Lucene-Syntax](/rest/api/searchservice/lucene-query-syntax-in-azure-search) verwenden. [Filterausdrücke](/rest/api/searchservice/odata-expression-syntax-for-azure-search) folgen einer OData-Syntax.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie in Ihrem eigenen Abonnement arbeiten, sollten Sie sich am Ende eines Projekts überlegen, ob Sie die erstellten Ressourcen noch benötigen. Ressourcen, die weiterhin ausgeführt werden, können Sie Geld kosten. Sie können entweder einzelne Ressourcen oder aber die Ressourcengruppe löschen, um den gesamten Ressourcensatz zu entfernen.

Ressourcen können im Portal über den Link **Alle Ressourcen** oder **Ressourcengruppen** im linken Navigationsbereich gesucht und verwaltet werden.

Denken Sie bei Verwendung eines kostenlosen Diensts an die Beschränkung auf maximal drei Indizes, Indexer und Datenquellen. Sie können einzelne Elemente über das Portal löschen, um unter dem Limit zu bleiben. 

## <a name="next-steps"></a>Nächste Schritte

Um mehr über Abfragestrukturen und -syntax zu erfahren, verwenden Sie Postman oder ein gleichwertiges Tool, um Abfrageausdrücke zu erstellen, die mehr Teile der API nutzen. Die [Search-REST-API](/rest/api/searchservice/search-documents) ist besonders hilfreich für das Lernen und Erkunden.

> [!div class="nextstepaction"]
> [Erstellen einer einfachen Abfrage in Postman](search-get-started-rest.md)