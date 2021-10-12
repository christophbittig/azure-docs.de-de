---
title: Einführung in Azure Cognitive Search
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search ist ein vollständig verwalteter Cloudsuchdienst von Microsoft. Hier erfahren Sie mehr über Anwendungsfälle, den Entwicklungsworkflow und Vergleiche mit anderen Microsoft-Suchprodukten. Außerdem erhalten Sie Informationen zu den ersten Schritten.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 09/28/2021
ms.custom: contperf-fy21q1
ms.openlocfilehash: b61ddd82671835e96b3115816ef57ca44fca942b
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129207810"
---
# <a name="what-is-azure-cognitive-search"></a>Was ist Azure Cognitive Search?

Azure Cognitive Search ([früher „Azure Search“](whats-new.md#new-service-name)) ist ein Cloudsuchdienst, der Entwicklern die Infrastruktur, APIs und Tools zum Erstellen umfangreicher Suchfunktionen für private, heterogene Inhalte in Web- und Unternehmensanwendungen sowie in mobilen Anwendungen bietet.

Search dient als wichtige Grundlage für jede App, mit der Textinhalte für Benutzer angezeigt werden. Häufige Szenarien sind die Katalog- oder Dokumentsuche, Onlinehandel oder das Knowledge Mining bei Data Science.

Zum Erstellen eines Suchdiensts werden folgende Funktionen verwendet:

+ Eine Suchmaschine für die Volltextsuche mit Speicher für benutzereigene Inhalte in einem Suchindex
+ Umfangreiche Indizierung mit [Textanalyse](search-analyzers.md) und [optionaler KI-Anreicherung](cognitive-search-concept-intro.md) für die erweiterte Extraktion und Transformation von Inhalten
+ Umfassende Abfragefunktionen mit einfacher Syntax, vollständiger Lucene-Syntax und Eingabevorschlägen
+ Programmierbarkeit über REST-APIs und Clientbibliotheken in Azure SDKs für .NET, Python, Java und JavaScript
+ Azure-Integration auf Datenebene, Machine Learning-Ebene und KI (Cognitive Services)

Aus architektonischer Sicht befindet sich ein Suchdienst zwischen den externen Datenspeichern, die Ihre nicht indizierten Daten enthalten, und Ihrer Client-App, die Abfrageanforderungen an einen Suchindex sendet und die Antwort verarbeitet.

![Architektur von Azure Cognitive Search](media/search-what-is-azure-search/azure-search-diagram.svg "Architektur von Azure Cognitive Search")

Auf der Azure-Plattform kann Cognitive Search mit anderen Azure-Diensten integriert werden. Dies erfolgt über *Indexer*, die das Erfassen/Abrufen von Daten aus Azure-Datenquellen automatisieren, und *Skillsets*, mit denen nutzbare KI-Daten aus Cognitive Services (z. B. Verarbeitung von Bildern und natürlicher Sprach) oder benutzerdefinierte KI-Daten, die Sie in Azure Machine Learning erstellen oder in Azure Functions einbinden, einbezogen werden.

## <a name="inside-a-search-service"></a>In einem Suchdienst

Die beiden primären Workloads des eigentlichen Suchdiensts sind *Indizierung* und *Abfrage*. 

+ Bei der [Indizierung](search-what-is-an-index.md) handelt es sich um einen Erfassungsprozess, bei dem Inhalte in Ihren Suchdienst geladen und durchsuchbar gemacht werden. Intern wird eingehender Text in Token verarbeitet und für schnelle Scans in invertierten Indizes gespeichert. Sie können beliebige Textinhalte hochladen, die als JSON-Dokumente vorliegen.

  Wenn Ihre Inhalte gemischte Dateien enthalten, haben Sie außerdem die Möglichkeit, *KI-Anreicherung* über [kognitive Skills](cognitive-search-working-with-skillsets.md) hinzuzufügen. Die KI-Erweiterung kann in Anwendungsdateien eingebetteten Text extrahieren und durch Analysieren des Inhalts auch Text und Struktur aus Dateien ableiten, die nicht im Textformat vorliegen. 

  Die Skills, die die Analyse bereitstellen, sind vordefinierte Funktionen von Microsoft oder von Ihnen erstellte benutzerdefinierte Skills. Die anschließenden Analysen und Transformationen können zu neuen Informationen und Strukturen führen, die zuvor nicht vorhanden waren und für viele Such- und Knowledge Mining-Szenarien von großem Nutzen sind.

+ [Abfragen](search-query-overview.md) können ausgeführt werden, sobald ein Index mit durchsuchbarem Text aufgefüllt wurde. Ihre Client-App sendet dann Abfrageanforderungen an einen Suchdienst und verarbeitet die Antworten. Die gesamte Abfrageausführung erfolgt über einem Suchindex, den Sie erstellen, besitzen und in Ihrem Dienst speichern. In Ihrer Client-App wird die Suchfunktion mithilfe von APIs aus Azure Cognitive Search definiert und kann Relevanzoptimierung, automatische Vervollständigung, Synonymabgleich, Fuzzyübereinstimmung, Musterabgleich, Filter und Sortierung umfassen.

Die Funktionalität wird über eine einfache [REST-API](/rest/api/searchservice/) oder ein [.NET SDK](search-howto-dotnet-sdk.md) bereitgestellt, sodass die inhärente Komplexität des Informationsabrufs verborgen bleibt. Sie können auch das Azure-Portal für die Dienst- und Inhaltsverwaltung mit Tools für die Prototyperstellung und Abfrage Ihrer Indizes und Qualifikationsgruppen verwenden. Da der Dienst in der Cloud ausgeführt wird, werden die Infrastruktur und Verfügbarkeit von Microsoft verwaltet.

## <a name="why-use-cognitive-search"></a>Gründe für die Verwendung von Cognitive Search

Azure Cognitive Search eignet sich sehr gut für die folgenden Anwendungsszenarien:

+ Konsolidierung heterogener Inhalte in einem privaten, benutzerdefinierten Suchindex. Auslagern von Indizierungs- und Abfrageworkloads in einen dedizierten Suchdienst

+ Einfache Implementierung von suchbezogenen Features: Relevanzoptimierung, Facettennavigation, Filter (z. B. geografisch-räumliche Suche), Synonymzuordnung und AutoVervollständigen.

+ Transformation von großen undifferenzierten Text-/Bild- oder Anwendungsdateien, die in Azure Blob Storage oder Cosmos DB gespeichert sind, in durchsuchbare JSON-Dokumente. Dies wird bei der Indizierung mit [kognitiven Skills](cognitive-search-concept-intro.md) erreicht, mit denen Funktionen für die externe Verarbeitung hinzugefügt werden.

+ Hinzufügung einer linguistischen oder benutzerdefinierten Textanalyse. Bei nicht englischsprachigen Inhalten unterstützt Azure Cognitive Search sowohl Lucene-Analysetools als auch die Microsoft-Prozessoren für die Verarbeitung natürlicher Sprache. Sie können Analysetools auch so konfigurieren, dass Rohdaten einer speziellen Verarbeitung unterzogen werden, um z. B. diakritische Zeichen herauszufiltern oder Muster in Zeichenfolgen zu erkennen und beizubehalten.

Weitere Informationen zu bestimmten Funktionen finden Sie unter [Azure Cognitive Search-Features](search-features-list.md).

## <a name="how-to-get-started"></a>Erste Schritte

Mithilfe der folgenden vier Schritte können Sie sich umfassend mit den wichtigsten Suchfunktionen vertraut machen:

1. [**Erstellen Sie einen Suchdienst**](search-create-service-portal.md) über den freigegebenen Free-Tarif, oder verwenden Sie einen [kostenpflichtigen Tarif](https://azure.microsoft.com/pricing/details/search/) für dedizierte Ressourcen, die nur von Ihrem Dienst genutzt werden. Alle Schnellstartanleitungen und Tutorials können über einen Dienst, der für die gemeinsame Nutzung freigegeben ist, durchgearbeitet werden.

1. [**Erstellen Sie einen Suchindex**](search-what-is-an-index.md), indem Sie das Portal, die [REST-API](/rest/api/searchservice/create-index), das [.NET SDK](search-howto-dotnet-sdk.md) oder ein anderes SDK verwenden. Das Indexschema definiert die Struktur von durchsuchbarem Inhalt.

1. [**Laden Sie Inhalt hoch**](search-what-is-data-import.md), indem Sie das [Pushmodell](tutorial-optimize-indexing-push-api.md) verwenden, um JSON-Dokumente aus einer beliebigen Quelle zu pushen. Verwenden Sie alternativ das [Pullmodell (Indexer)](search-indexer-overview.md), falls sich Ihre Quelldaten in Azure befinden.

1. [**Fragen Sie einen Index ab.**](search-query-overview.md) Verwenden Sie dazu den [Suchexplorer](search-explorer.md) im Portal, die [REST-API](search-get-started-rest.md), das [.NET SDK](/dotnet/api/azure.search.documents.searchclient.search) oder ein anderes SDK.

Beginnen Sie als Einstieg mit dem [**Datenimport-Assistenten**](search-get-started-portal.md) und einer integrierten Azure-Datenquelle, um innerhalb weniger Minuten einen Index zu erstellen, zu laden und abzufragen.

[**Wenden Sie sich an einen Partner**](resource-partners-knowledge-mining.md), der sich sehr gut mit der Cognitive Search-Technologie auskennt, um Hilfe zu komplexen oder benutzerdefinierten Lösungen zu erhalten.

## <a name="compare-search-options"></a>Vergleichen von Suchoptionen

Kunden interessieren sich häufig für einen Vergleich von Azure Cognitive Search mit anderen Suchlösungen. Die wesentlichen Unterschiede sind in der folgenden Tabelle zusammengefasst.

| Im Vergleich zu | Wesentliche Unterschiede |
|-------------|-----------------|
| Microsoft Search | [Microsoft Search](/microsoftsearch/overview-microsoft-search) ist für authentifizierte Microsoft 365-Benutzer bestimmt, die Abfragen für den Inhalt in SharePoint ausführen müssen. Sie wird als sofort einsatzbereite Suchoberfläche angeboten, die von Administratoren aktiviert und konfiguriert wird. Dabei besteht die Möglichkeit, über Connectors externe Inhalte von Microsoft und anderen Quellen zu akzeptieren. Wenn diese Beschreibung auf Ihr Szenario zutrifft, ist Microsoft Search mit Microsoft 365 eine attraktive Option.<br/><br/>Azure Cognitive Search hingegen führt Abfragen für einen von Ihnen definierten Index aus, der mit Ihren Daten und Dokumenten (häufig aus verschiedenen Quellen) gefüllt ist. Azure Cognitive Search verfügt über Crawlerfunktionen für einige Azure-Datenquellen über [Indexer](search-indexer-overview.md), aber Sie können jedes JSON-Dokument, das Ihrem Indexschema entspricht, mittels Pushübertragung in eine einzelne, konsolidierte durchsuchbare Ressource übertragen. Sie können die Indizierungspipeline auch so anpassen, dass sie Analysen für maschinelles Lernen und lexikalische Analysen umfasst. Da Cognitive Search als Plug-In-Komponente für umfangreichere Lösungen entwickelt wurde, können Sie die Suche in nahezu jede App auf jeder beliebigen Plattform integrieren.|
|Bing | Die [Bing-Websuche-API](../cognitive-services/bing-web-search/index.yml) durchsucht die Indizes auf „Bing.com“ nach Übereinstimmungen mit Begriffen, die Sie übermitteln. Die Indizes werden auf der Basis von HTML- und XML-Seiten sowie anderer Webinhalte aus öffentlich zugänglichen Websites erstellt. Die [benutzerdefinierte Bing-Suche](/azure/cognitive-services/bing-custom-search/) basiert auf der gleichen Grundlage und bietet die gleiche Crawlertechnologie für Webinhaltstypen beschränkt auf einzelne Websites.<br/><br/>In Cognitive Search können Sie den Index definieren und auffüllen. Sie können [Indexer](search-indexer-overview.md) verwenden, um Daten in Azure-Datenquellen aufzufüllen oder beliebige indexkonforme JSON-Dokumente per Push an Ihren Suchdienst zu übertragen. |
|Datenbanksuche | Zahlreiche Systemplattformen umfassen eine integrierte Suchfunktion. SQL Server verfügt über eine [Volltextsuche](/sql/relational-databases/search/full-text-search). Cosmos DB und ähnliche Technologien verfügen über abfragbare Indizes. Bei der Beurteilung von Produkten, die Suche und Speicher kombinieren, kann die Entscheidung eine Herausforderung darstellen. Viele Lösungen verwenden beides: DBMS als Speicher und Azure Cognitive Search für spezialisierte Suchfunktionen.<br/><br/>Im Vergleich zur DBMS-Suche speichert Azure Cognitive Search Inhalt aus heterogenen Quellen und bietet spezialisierte Textverarbeitungsfunktionen wie z. B. linguistikbasierte Textverarbeitung (Wortstammerkennung, Lemmatisierung, Wortformen) in [56 Sprachen](/rest/api/searchservice/language-support). Der Dienst unterstützt auch die Autokorrektur falsch geschriebener Wörter, [Synonyme](/rest/api/searchservice/synonym-map-operations), [Vorschläge](/rest/api/searchservice/suggestions), [Bewertungssteuerelemente](/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [Facets](search-faceted-navigation.md) und [benutzerdefinierte Tokenisierung](/rest/api/searchservice/custom-analyzers-in-azure-search). Das [Volltext-Suchmodul](search-lucene-query-architecture.md) in Azure Cognitive Search basiert auf Apache Lucene, einem Branchenstandard für den Informationsabruf. Azure Cognitive Search speichert Daten zwar in Form eines invertierten Index, doch ist dies kein Ersatz für eine echte Datenspeicherung und von der Verwendung für diesen Zweck wird abgeraten. Weitere Informationen finden Sie in diesem [Forumsbeitrag](https://stackoverflow.com/questions/40101159/can-azure-search-be-used-as-a-primary-database-for-some-data). <br/><br/>Die Ressourcenverwendung ist ein weiterer Wendepunkt in dieser Kategorie. Die Indizierung und einige Abfragevorgänge sind häufig rechenintensiv. Die Abladung der Suche vom DBMS auf eine dedizierte Lösung in der Cloud hält Systemressourcen für die Verarbeitung von Transaktionen frei. Wenn Sie die Suche nach außen verlagern, können Sie außerdem problemlos Ihrem Abfragevolumen gemäß skalieren.|
|Dedizierte Suchlösung | Wenn Sie sich für eine dedizierte Suche mit voller Funktionalität entschieden haben, erfolgt der endgültige Vergleich letztendlich zwischen einer lokalen Lösung und einem Cloud-Dienst. Viele Suchtechnologien bieten Kontrolle über Indizierungs- und Abfragepipelines, Zugriff auf umfangreichere Abfrage- und Filtersyntax, Kontrolle über Rangfolge und Relevanz sowie Funktionen für selbstgesteuerte und intelligente Suche. <br/><br/>Ein Clouddienst ist die richtige Wahl, wenn Sie eine sofort einsetzbare Lösung mit minimalem Aufwand, minimaler Wartung und anpassbarer Skalierung wünschen. <br/><br/>Innerhalb des Cloudparadigmas bieten einige Anbieter vergleichbare Basisfunktionen mit Volltextsuche, geografischer Suche und der Möglichkeit zur Verarbeitung einer gewissen Ungenauigkeit bei der Sucheingabe. Das passende Suchmodul ergibt sich in der Regel durch ein [spezialisiertes Feature](search-features-list.md) oder durch die Benutzerfreundlichkeit und Einfachheit von APIs, Tools und Verwaltungsfunktionen. |

Unter den Cloudanbietern eignet Azure Cognitive Search sich am besten für die Volltextsuche in Inhaltsspeichern und Datenbanken in Azure, für Apps, bei denen Informationsabruf und Inhaltsnavigation primär auf der Suche basieren. 

Wichtige Vorteile umfassen:

+ Azure-Datenintegration (Crawler) auf Indexebene
+ Azure Private Link-Integration zur Unterstützung von nicht internetbezogenen Sicherheitsanforderungen
+ Integration in die KI-Verarbeitung, um für nicht durchsuchbare Inhaltstypen die Textsuche zu ermöglichen
+ Linguistische und benutzerdefinierte Analyse, mit Analysemodulen für eine robuste Volltextsuche in 56 Sprachen
+ [Wichtige Features](search-features-list.md): umfangreiche Abfragesprache, Relevanzoptimierung, Facettierung, AutoVervollständigen, Synonyme, geografische Suche und Ergebniszusammensetzung
+ Skalierung, Zuverlässigkeit und erstklassige Verfügbarkeit von Azure

Unter unseren Kunden zählen diejenigen mit Onlinekatalogen, Branchenprogrammen und Anwendungen zur Dokumentermittlung zu denen, die am meisten von den umfassenden Features in Azure Cognitive Search profitieren.

## <a name="watch-this-video"></a>Video ansehen

In diesem 15-minütigen Video werden die wichtigsten Funktionen von Azure Cognitive Search erläutert.

>[!VIDEO https://www.youtube.com/embed/kOJU0YZodVk?version=3]