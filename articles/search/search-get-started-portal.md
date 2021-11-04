---
title: 'Schnellstart: Erstellen eines Suchindexes im Azure-Portal'
titleSuffix: Azure Cognitive Search
description: Verwenden Sie den Datenimport-Assistenten im Azure-Portal, um Ihren ersten Suchindex zu erstellen, zu laden und abzufragen. In diesem Schnellstart wird für die Beispieldaten ein Dataset mit fiktiven Hotels verwendet.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 08/24/2021
ms.openlocfilehash: 244b7070d73eb96f584a1a50b49e24b44ba41a7e
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131561402"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-the-azure-portal"></a>Schnellstart: Erstellen eines Index für Azure Cognitive Search im Azure-Portal

Erstellen Sie Ihren ersten Suchindex mithilfe des **Datenimport-Assitenten** und einer integrierten Beispieldatenquelle, die fiktive Hotels enthält. Der Assistent führt Sie durch die Erstellung eines Suchindex („hotels-sample-index“), sodass Sie innerhalb weniger Minuten interessante Abfragen schreiben können. 

Der Assistent enthält auch eine Seite für KI-Anreicherung, sodass Sie Text und eine Struktur aus Bilddateien und unstrukturiertem Text extrahieren können. In dieser Schnellstartanleitung werden diese Optionen jedoch nicht verwendet. Eine ähnliche exemplarische Vorgehensweise mit KI-Anreicherung finden Sie unter [Schnellstart: Übersetzen von Text und Erkennen von Entitäten mithilfe des Datenimport-Assistenten](cognitive-search-quickstart-blob.md) oder [Schnellstart: Anwenden von OCR und Bildanalyse mithilfe des Datenimport-Assistenten](cognitive-search-quickstart-ocr.md).

## <a name="prerequisites"></a>Voraussetzungen

+ Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/).

+ Ein Azure Cognitive Search-Dienst (beliebige Dienstebene, beliebige Region). [Erstellen Sie einen Dienst](search-create-service-portal.md), oder suchen Sie in Ihrem aktuellen Abonnement [nach einem vorhandenen Dienst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Für diesen Schnellstart können Sie einen kostenlosen Dienst verwenden. 

### <a name="check-for-space"></a>Überprüfen des Speicherplatzes

Viele Kunden beginnen mit dem kostenlosen Dienst (Free). Der Free-Tarif ist auf drei Indizes, drei Datenquellen und drei Indexer beschränkt. Stellen Sie sicher, dass Sie über ausreichend Platz für zusätzliche Elemente verfügen, bevor Sie beginnen. In diesem Tutorial wird jeweils eine Instanz von jedem Objekt erstellt.

Auf der Übersichtsseite des Diensts erfahren Sie, wie viele Indizes, Indexer und Datenquellen Sie bereits haben. 

:::image type="content" source="media/search-get-started-portal/tiles-indexers-datasources.png" alt-text="Listen mit Indizes, Indexern und Datenquellen":::

## <a name="create-an-index-and-load-data"></a><a name="create-index"></a> Erstellen eines Index und Laden von Daten

Suchabfragen durchlaufen einen [*Index*](search-what-is-an-index.md) mit durchsuchbaren Daten, Metadaten und zusätzlichen Konstrukten, die bestimmte Suchverhaltensweisen optimieren.

Für dieses Tutorial verwenden Sie ein integriertes Beispieldataset, das über den [Assistenten **Daten importieren**](search-import-data-portal.md) mit einem [*Indexer*](search-indexer-overview.md) durchforstet werden kann. Ein Indexer ist ein quellspezifischer Crawler, mit dem Metadaten und Inhalte von unterstützten Azure-Datenquellen gelesen werden können. Normalerweise werden Indexer programmgesteuert verwendet, aber im Portal können Sie über den **Daten importieren**-Assistenten auf sie zugreifen. 

### <a name="step-1---start-the-import-data-wizard-and-create-a-data-source"></a>Schritt 1: Starten des „Daten importieren“-Assistenten und Erstellen einer Datenquelle

1. Melden Sie sich mit Ihrem Azure-Konto beim [Azure-Portal](https://portal.azure.com/) an.

1. [Suchen Sie Ihren Suchdienst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/), und klicken Sie auf der Übersichtsseite auf der Befehlsleiste auf **Daten importieren**, um einen Suchindex zu erstellen und aufzufüllen.

   :::image type="content" source="media/search-import-data-portal/import-data-cmd.png" alt-text="Screenshot des Befehls „Daten importieren“" border="true":::

1. Klicken Sie im Assistenten auf **Mit Ihren Daten verbinden** > **Beispiele** > **hotels-sample**. Diese Datenquelle ist integriert. Wenn Sie eine eigene Datenquelle erstellen, müssen Sie einen Namen, einen Typ und Verbindungsinformationen angeben. Nach der Erstellung wird sie zu einer vorhandenen Datenquelle, die in anderen Importvorgängen wiederverwendet werden kann.

   :::image type="content" source="media/search-get-started-portal/import-datasource-sample.png" alt-text="Auswählen des Beispieldatasets":::

1. Wechseln Sie zur nächsten Seite.

### <a name="step-2---skip-the-enrich-content-page"></a>Schritt 2: Überspringen der Seite „Inhalte anreichern“

Der Assistent unterstützt die Erstellung einer [Pipeline für KI-Anreicherung](cognitive-search-concept-intro.md), um die Cognitive Services-KI-Algorithmen in die Indizierung einzubeziehen. 

Überspringen Sie diesen Schritt vorerst, und wechseln Sie direkt zu **Zielindex anpassen**.

   :::image type="content" source="media/search-get-started-portal/skip-cog-skill-step.png" alt-text="Überspringen des Schritts zu kognitiven Qualifikationen":::

> [!TIP]
> Sie können ein Beispiel für die AI-Indizierung in einem [Schnellstart](cognitive-search-quickstart-blob.md) oder [Tutorial](cognitive-search-tutorial-blob.md) durchgehen.

### <a name="step-3---configure-index"></a>Schritt 3: Konfigurieren des Index

Für den integrierten Beispielhotelindex wird ein Standardindexschema für Sie definiert. Mit Ausnahme einiger erweiterter Filterbeispiele werden Abfragen in der Dokumentation und den Beispielen für den Beispielhotelindex in dieser Indexdefinition ausgeführt:

:::image type="content" source="media/search-get-started-portal/hotelsindex.png" alt-text="Generierter Hotelindex":::

In der Regel erfolgt vor dem Laden der jeweiligen Daten eine codebasierte Indexerstellung. Der Datenimport-Assistent fasst diese Schritte zusammen, indem er einen einfachen Index für jede Datenquelle erstellt, die er durchsuchen kann. Für einen Index sind mindestens ein Name und eine Felderauflistung erforderlich, wobei ein Feld als Dokumentschlüssel gekennzeichnet sein muss, um die einzelnen Dokumente eindeutig zu identifizieren. Außerdem können Sie Sprachanalysen oder Vorschlagsfunktionen angeben, wenn Sie Abfragen mit AutoVervollständigen oder vorgeschlagene Abfragen verwenden möchten.

Felder besitzen Datentypen und Attribute. Die Kontrollkästchen im oberen Bereich sind *Indexattribute*, mit denen die Verwendung des Felds gesteuert wird.

+ **Abrufbar** bedeutet, dass es in der Liste mit den Suchergebnissen angezeigt wird. Indem Sie dieses Kontrollkästchen deaktivieren, können Sie einzelne Felder aus den Suchergebnissen ausschließen, z.B. wenn Felder nur in Filterausdrücken verwendet werden.
+ **Schlüssel** ist der eindeutige Bezeichner des Dokuments. Er ist immer eine Zeichenfolge, und er ist erforderlich.
+ Mit **Filterbar**, **Sortierbar** und **Facettenreich** wird festgelegt, ob Felder in einer Filter-, Sortier- oder Facettennavigationsstruktur verwendet werden können.
+ **Durchsuchbar** bedeutet, dass ein Feld in die Volltextsuche einbezogen wird. Zeichenfolgen sind durchsuchbar. Numerische Felder und boolesche Felder werden häufig als „Nicht durchsuchbar“ markiert.

Speicheranforderungen ändern sich nicht als Folge Ihrer Auswahl. Wenn Sie z. B. das Attribut **Abrufbar** für mehrere Felder festlegen, erhöhen sich die Speicheranforderungen nicht.

Standardmäßig durchsucht der Assistent die Datenquelle nach eindeutigen Bezeichnern als Grundlage für das Schlüsselfeld. *Zeichenfolgen* werden durch Attribute als **Abrufbar** und **Durchsuchbar** gekennzeichnet. *Ganze Zahlen* werden durch Attribute als **Abrufbar**, **Filterbar**, **Sortierbar** und **Facettierbar** gekennzeichnet.

1. Übernehmen Sie die Standardeinstellungen.

   Wenn Sie den Assistenten ein zweites Mal unter Verwendung einer vorhandenen Quelle mit Hoteldaten ausführen, wird der Index nicht mit Standardattributen konfiguriert. Sie müssen Attribute für künftige Importe manuell auswählen. 

1. Wechseln Sie zur nächsten Seite.

### <a name="step-4---configure-indexer"></a>Schritt 4: Konfigurieren des Indexers

Klicken Sie im Assistenten **Daten importieren** auf **Indexer** > **Name**, und geben Sie einen Namen für den Indexer ein.

Mit diesem Objekt wird ein ausführbarer Prozess definiert. Sie könnten zwar auch einen wiederkehrenden Zeitplan einrichten, in diesem Tutorial verwenden Sie allerdings die Standardoption, um den Indexer sofort einmalig auszuführen.

Klicken Sie auf **Senden**, um den Indexer zu erstellen und gleichzeitig auszuführen.

  :::image type="content" source="media/search-get-started-portal/hotels-indexer.png" alt-text="Hotelindexer":::

## <a name="monitor-progress"></a>Fortschritt überwachen

Der Assistent sollte die Liste der Indexer anzeigen, in der Sie den Fortschritt überwachen können. Zur eigenen Navigation wechseln Sie zur Seite „Übersicht“ und klicken auf die Registerkarte **Indexer**.

Es kann einige Minuten dauern, bis die Seite im Portal aktualisiert ist, aber der neu erstellte Indexer sollte in der Liste angezeigt werden. Der Status sollte „In Bearbeitung“ oder „Erfolgreich“ lauten, und die Anzahl indizierter Dokumente sollte angegeben sein.

   :::image type="content" source="media/search-get-started-portal/indexers-inprogress.png" alt-text="Statusmeldung des Indexers":::

## <a name="view-the-index"></a>Anzeigen des Index

Die Übersichtsseite des Diensts enthält Links zu den Ressourcen, die in Ihrem Azure Cognitive Search-Dienst erstellt wurden.  Klicken Sie zum Anzeigen des gerade erstellten Index in der Liste mit den Links auf **Indizes**. 

Warten Sie, bis die Portalseite aktualisiert wurde. Nach einigen Minuten sollte der Index mit der Dokumentanzahl und der Speichergröße angezeigt werden.

   :::image type="content" source="media/search-get-started-portal/indexes-list.png" alt-text="Liste „Indizes“ im Dashboard des Diensts":::

In dieser Liste können Sie auf den gerade erstellten Index für *hotels-sample* klicken und das Indexschema anzeigen. Optional können Sie auch neue Felder hinzufügen. 

Auf der Registerkarte **Felder** wird das Indexschema angezeigt. Für die Erstellung von Abfragen und die Überprüfung, ob ein Feld gefiltert oder sortiert werden kann, werden auf dieser Registerkarte die Attribute angezeigt.

Scrollen Sie in der Liste nach unten, um ein neues Feld einzugeben. Sie können zwar immer ein neues Feld erstellen, vorhandene Felder können aber in den meisten Fällen nicht geändert werden. Vorhandene Felder verfügen über eine physische Darstellung in Ihrem Suchdienst und können daher nicht geändert werden (auch nicht im Code). Wenn Sie ein vorhandenes Feld grundlegend ändern möchten, erstellen Sie einen neuen Index, wobei der ursprüngliche Index verworfen wird.

   :::image type="content" source="media/search-get-started-portal/sample-index-def.png" alt-text="Beispiel für Indexdefinition":::

Andere Konstrukte, z.B. Bewertungsprofile und CORS-Optionen, können jederzeit hinzugefügt werden.

Nehmen Sie sich kurz Zeit, um die Optionen für die Indexdefinition anzuzeigen und sich damit vertraut zu machen, was Sie beim Entwerfen des Index bearbeiten können. Wenn Optionen ausgegraut sind, ist dies ein Hinweis darauf, dass ein Wert nicht geändert oder gelöscht werden kann. 

## <a name="query-using-search-explorer"></a><a name="query-index"></a> Abfragen mit dem Suchexplorer

Sie sollten nun über einen Suchindex verfügen, der über die integrierte [**Suchexplorer**](search-explorer.md)-Abfrageseite abgefragt werden kann. Sie enthält ein Suchfeld, über das Sie beliebige Abfragezeichenfolgen testen können.

Der **Suchexplorer** ist nur auf die Verarbeitung von [REST-API-Anforderungen](/rest/api/searchservice/search-documents) ausgelegt. Er akzeptiert aber auch [einfache Abfragesyntax](/rest/api/searchservice/simple-query-syntax-in-azure-search) und Syntax für den [vollständigen Lucene-Abfrageparser](/rest/api/searchservice/lucene-query-syntax-in-azure-search) sowie alle Suchparameter, die für Vorgänge mit der [REST-API zum Durchsuchen von Dokumenten](/rest/api/searchservice/search-documents#bkmk_examples) verfügbar sind.

1. Klicken Sie auf der Befehlsleiste auf **Suchexplorer** .

   :::image type="content" source="media/search-get-started-portal/search-explorer-cmd.png" alt-text="Suchexplorerbefehl":::

1. Wählen Sie in der Dropdownliste **Index** den Eintrag *hotels-sample-index* aus. Klicken Sie auf die Dropdownliste **API-Version**, um herauszufinden, welche REST-APIs verfügbar sind. Verwenden Sie für die Abfragen unten die allgemein verfügbare Version (2020-06-30).

   :::image type="content" source="media/search-get-started-portal/search-explorer-changeindex.png" alt-text="Index- und API-Befehle":::

1. Fügen Sie über die Suchleiste die folgenden Abfragezeichenfolgen ein, und klicken Sie auf **Suchen**.

   :::image type="content" source="media/search-get-started-portal/search-explorer-query-string-example.png" alt-text="Abfragezeichenfolge und Schaltfläche „Suchen“":::

## <a name="example-queries"></a>Beispielabfragen

Sie können Begriffe und Ausdrücke eingeben, ähnlich wie bei einer Bing- oder Google-Suche, oder Sie können vollständig formulierte Abfrageausdrücke eingeben. Die Ergebnisse werden als ausführliche JSON-Dokumente zurückgegeben.

### <a name="simple-query-with-top-n-results"></a>Einfache Abfrage mit Top N-Ergebnissen

#### <a name="example-string-query-searchspa"></a>Beispiel (Abfragezeichenfolge): `search=spa`

+ Der Parameter **search** dient zum Eingeben eines Stichworts für die Volltextsuche. In diesem Fall werden Daten für Hotels zurückgegeben, die in einem beliebigen durchsuchbaren Feld des Dokuments den Begriff *spa* enthalten.

+ Der **Suchexplorer** gibt Ergebnisse im JSON-Format zurück. Dieses Format ist sehr ausführlich und in Dokumenten mit einer dichten Struktur nur schwer lesbar. Dies ist so gewollt. Die Sichtbarkeit in das gesamte Dokument ist wichtig für Entwicklungszwecke, vor allem beim Testen. Zur Verbesserung der Benutzerfreundlichkeit müssen Sie Code schreiben, mit dem [Suchergebnisse verarbeitet werden](search-pagination-page-layout.md), um wichtige Elemente hervorzuheben.

+ Dokumente bestehen aus allen Feldern, die im Index als „abrufbar“ gekennzeichnet sind. Klicken Sie zum Anzeigen von Indexattributen im Portal in der Liste **Indizes** auf *hotels-sample*.

#### <a name="example-parameterized-query-searchspacounttruetop10"></a>Beispiel (parametrisierte Abfrage): `search=spa&$count=true&$top=10`

+ Das Symbol **&** dient zum Anfügen von Suchparametern. Diese können in beliebiger Reihenfolge angegeben werden.

+ Der Parameter **$count=true** gibt die Gesamtanzahl aller zurückgegebenen Dokumente zurück. Dieser Wert wird oben im Bereich mit den Suchergebnissen angezeigt. Zur Überprüfung von Filterabfragen können Sie von **$count=true** gemeldete Änderungen überwachen. Niedrigere Zahlen weisen darauf hin, dass Ihr Filter funktioniert.

+ **$top=10** gibt die 10 Dokumente mit dem höchsten Rang zurück. Standardmäßig gibt Azure Cognitive Search die ersten 50 besten Treffer zurück. Die Menge kann mithilfe von **$top** erhöht oder verringert werden.

### <a name="filter-the-query"></a><a name="filter-query"></a> Filtern der Abfrage

Filter werden in Suchanfragen eingefügt, wenn Sie den Parameter **$filter** anfügen. 

#### <a name="example-filtered-searchbeachfilterrating-gt-4"></a>Beispiel (gefiltert): `search=beach&$filter=Rating gt 4`

+ Der Parameter **$filter** gibt Ergebnisse zurück, die den angegebenen Kriterien entsprechen. In diesem Fall: höhere Bewertungen als 4.

+ Bei der Filtersyntax handelt es sich um eine OData-Konstruktion. Weitere Informationen finden Sie unter [OData Expression Syntax for Azure Search](/rest/api/searchservice/odata-expression-syntax-for-azure-search) (OData-Ausdruckssyntax für Azure Search).

### <a name="facet-the-query"></a><a name="facet-query"></a> Facettieren der Abfrage

Facettenfilter werden in Suchanfragen eingebunden. Sie können den Parameter „facet“ verwenden, um eine aggregierte Anzahl von Dokumenten zurückzugeben, die mit einem von Ihnen angegebenen Facetwert übereinstimmen.

#### <a name="example-faceted-with-scope-reduction-searchfacetcategorytop2"></a>Beispiel (facettiert mit Verringerung des Umfangs): `search=*&facet=Category&$top=2`

+ **search=** * ist eine leere Suche. Bei einer leeren Suche wird alles durchsucht. Eine leere Abfrage kann beispielsweise übermittelt werden, um den vollständigen Satz von Dokumenten zu filtern oder zu facettieren – etwa, wenn eine Navigationsstruktur mit Facetten alle Hotels im Index enthalten soll.
+ **facet** gibt eine Navigationsstruktur zurück, die Sie an ein Benutzeroberflächenelement übergeben können. Er gibt Kategorien und eine Anzahl zurück. In diesem Fall basieren alle Kategorien auf einem Feld, das praktischerweise *Kategorie* heißt. In Azure Cognitive Search steht zwar keine Aggregation zur Verfügung, mit `facet` lässt sich jedoch eine Art Aggregation erreichen, die eine Anzahl von Dokumenten in den einzelnen Kategorien liefert.

+ **$top=2** gibt zwei Dokumente zurück und zeigt, dass Sie mithilfe von `top` die Ergebnisse sowohl verringern als auch erhöhen können.

#### <a name="example-facet-on-numeric-values-searchspafacetrating"></a>Beispiel (Facette für numerische Werte): `search=spa&facet=Rating`

+ Bei dieser Abfrage handelt es sich um eine Facette für die Bewertung bei einer Textsuche nach *spa*. Der Begriff *Bewertung* kann als Facette angegeben werden, da das Feld im Index als abrufbar, filterbar und facettierbar markiert ist und die enthaltenen Werte (numerische Werte von 1 bis 5) für die Kategorisierung von Angeboten in Gruppen geeignet ist.

+ Nur filterbare Felder können facettiert werden. In den Ergebnissen können nur abrufbare Felder zurückgegeben werden.

+ Das Feld *Bewertung* ist eine Gleitkommazahl mit doppelter Genauigkeit. Weitere Informationen zum Gruppieren per Intervall (z. B. „Bewertungen mit 3 Sternen“, „Bewertungen mit 4 Sternen“ usw.) finden Sie unter [„Abfrageparameter“ in der REST-API](/rest/api/searchservice/search-documents#query-parameters).

### <a name="highlight-search-results"></a><a name="highlight-query"></a> Hervorheben von Suchergebnissen

Bei Treffermarkierungen wird Text, der dem Schlüsselwort entspricht, mit einer Formatierung versehen (sofern Treffer in einem bestimmten Feld gefunden werden). Falls Ihr Suchbegriff Teil einer umfangreicheren Beschreibung ist, können Sie ihn mithilfe einer Treffermarkierung hervorheben.

#### <a name="example-highlighter-searchbeachhighlightdescription"></a>Beispiel (Textmarker): `search=beach&highlight=Description`

+ In diesem Beispiel ist das formatierte Wort *beach* im Beschreibungsfeld leichter zu erkennen.

#### <a name="example-linguistic-analysis-searchbeacheshighlightdescription"></a>Beispiel (linguistische Analyse): `search=beaches&highlight=Description`

+ Die Volltextsuche erkennt grundlegende Varianten von Wortformen. Die Suchergebnisse für die Stichwortsuche nach „beaches“ enthalten in diesem Fall markierten Text für „beach“ für Hotels, in deren durchsuchbaren Feldern dieses Wort vorkommt. Dank linguistischer Analyse können in den Ergebnissen unterschiedliche Formen des gleichen Worts erscheinen. 

+ Azure Cognitive Search unterstützt 56 Analysetools von Lucene und Microsoft. Standardmäßig verwendet Azure Cognitive Search die Standardanalyse von Lucene.

### <a name="try-fuzzy-search"></a><a name="fuzzy-search"></a> Ausprobieren der Fuzzysuche

Für falsch geschriebene Wörter wie *seatle* für „Seattle“ werden bei einer herkömmlichen Suche standardmäßig keine Treffer zurückgegeben. Im folgenden Beispiel werden keine Ergebnisse zurückgegeben.

#### <a name="example-misspelled-term-unhandled-searchseatle"></a>Beispiel (falsch geschriebener Begriff, keine Verarbeitung): `search=seatle`

Sie können die Fuzzysuche verwenden, damit auch Begriffe mit Rechtschreibfehlern einbezogen werden. Die Fuzzysuche wird aktiviert, wenn Sie die vollständige Lucene-Abfragesyntax verwenden. Dies ist der Fall, wenn Sie zwei Schritte ausführen: Festlegen von **queryType=full** für die Abfrage und Anfügen von **~** an die Suchzeichenfolge.

#### <a name="example-misspelled-term-handled-searchseatlequerytypefull"></a>Beispiel (falsch geschriebener Begriff, Verarbeitung): `search=seatle~&queryType=full`

In diesem Beispiel werden jetzt Dokumente zurückgegeben, die Treffer zu „Seattle“ enthalten.

Ohne Angabe von **queryType** wird der standardmäßige Parser für einfache Abfragen verwendet. Der Parser für einfache Abfragen ist zwar schneller, Fuzzysuche, reguläre Ausdrücke, NEAR-Suche und andere erweiterte Abfragetypen stehen jedoch nur bei Verwendung der vollständigen Syntax zur Verfügung.

Die Fuzzysuche und die Platzhaltersuche haben Auswirkungen auf die Suchausgabe. Die linguistische Analyse wird für diese Abfrageformate nicht durchgeführt. Bevor Sie die Fuzzy- und Platzhaltersuche verwenden, sollten Sie sich unter [Funktionsweise der Volltextsuche in Azure Cognitive Search – Phase 2: Lexikalische Analyse](search-lucene-query-architecture.md#stage-2-lexical-analysis) den Abschnitt zu den Ausnahmen bei der lexikalischen Analyse durchlesen.

Weitere Informationen zu möglichen Abfrageszenarien mit dem Parser für vollständige Abfragen finden Sie unter [Lucene-Abfragesyntax in Azure Cognitive Search](/rest/api/searchservice/lucene-query-syntax-in-azure-search).

### <a name="try-geospatial-search"></a><a name="geo-search"></a> Ausprobieren der Geosuche

Die Geosuche wird über den [Datentyp „edm.GeographyPoint“](/rest/api/searchservice/supported-data-types) für ein Feld mit Koordinaten unterstützt. Die Geosuche ist ein Filtertyp und wird in der [OData-Ausdruckssyntax für Azure Search](/rest/api/searchservice/odata-expression-syntax-for-azure-search) angegeben.

#### <a name="example-geo-coordinate-filters-searchcounttruefiltergeodistancelocationgeographypoint-12212-4767-le-5"></a>Beispiel (Geokoordinatenfilter): `search=*&$count=true&$filter=geo.distance(Location,geography'POINT(-122.12 47.67)') le 5`

Die Beispielabfrage filtert alle Ergebnisse nach Positionsdaten und gibt Ergebnisse zurück, die weniger als fünf Kilometer von einem bestimmten Punkt (angegeben als Koordinaten mit Breiten-und Längengrad) entfernt sind. Durch Hinzufügen von **$count** sehen Sie, wie viele Ergebnisse zurückgegeben werden, wenn Sie die Entfernung oder die Koordinaten ändern.

Die Geosuche ist hilfreich, wenn Ihre Suchanwendung über eine Umgebungssuche oder Kartennavigation verfügt. Es ist allerdings keine Volltextsuche. Falls eine Suche nach Städtename oder Länder-/Regionsname möglich sein muss, fügen Sie zusätzlich zu den Koordinaten Felder mit Städtenamen oder Länder-/Regionsnamen hinzu.

## <a name="takeaways"></a>Wesentliche Punkte

Dieses Tutorial enthält eine kurze Einführung zur Verwendung von Azure Cognitive Search über das Azure-Portal.

Sie haben erfahren, wie Sie mithilfe des Assistenten **Daten importieren** einen Suchindex erstellen. Sie haben sich über [Indexer](search-indexer-overview.md) und über den grundlegenden Workflow für das Entwerfen von Indizes informiert, z.B. [unterstützte Änderungen an einem veröffentlichten Index](/rest/api/searchservice/update-index).

Unter Verwendung des **Suchexplorers** im Azure-Portal haben Sie eine grundlegende Abfragesyntax anhand von praktischen Beispielen erlernt, und es wurden wichtige Funktionen wie Filter, Treffermarkierung, Fuzzysuche und die geografische Suche veranschaulicht.

Außerdem haben Sie gelernt, wie Indizes, Indexer und Datenquellen im Portal zu finden sind. Wenn Sie zukünftig Datenquellen verwenden, können Sie schnell und mit minimalem Aufwand über das Portal ihre Definitionen oder Felderauflistungen überprüfen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie in Ihrem eigenen Abonnement arbeiten, sollten Sie sich am Ende eines Projekts überlegen, ob Sie die erstellten Ressourcen noch benötigen. Ressourcen, die weiterhin ausgeführt werden, können Sie Geld kosten. Sie können entweder einzelne Ressourcen oder aber die Ressourcengruppe löschen, um den gesamten Ressourcensatz zu entfernen.

Ressourcen können im Portal über den Link **Alle Ressourcen** oder **Ressourcengruppen** im linken Navigationsbereich gesucht und verwaltet werden.

Denken Sie bei Verwendung eines kostenlosen Diensts an die Beschränkung auf maximal drei Indizes, Indexer und Datenquellen. Sie können einzelne Elemente über das Portal löschen, um unter dem Limit zu bleiben. 

## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie einen Portal-Assistenten, um eine sofort einsatzbereite Web-App zu erstellen, die in einem Browser ausgeführt wird. Sie können diesen Assistenten mit dem soeben von Ihnen erstellten kleinen Index testen oder eins der integrierten Beispieldatasets für umfassendere Suchfunktionen verwenden.

> [!div class="nextstepaction"]
> [Erstellen einer Demo-App im Portal](search-create-app-portal.md)