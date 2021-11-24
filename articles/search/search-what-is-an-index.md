---
title: Indexübersicht
titleSuffix: Azure Cognitive Search
description: Erklärt, was ein Suchindex in Azure Cognitive Search ist und beschreibt Inhalt, Aufbau, physischen Ausdruck und das Indexschema.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/12/2021
ms.openlocfilehash: 51b075dbce189370cf502bce6d46471da6c58348
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490533"
---
# <a name="indexes-in-azure-cognitive-search"></a>Indizes in Azure Cognitive Search

In Azure Cognitive Search ist ein *Suchindex* Ihr durchsuchbarer Inhalt, der der Suchmaschine für Indizierung, Volltextsuche und gefilterte Abfragen zur Verfügung steht. Ein Index wird durch ein Schema definiert und im Suchdienst gespeichert, der Datenimport folgt in einem zweiten Schritt. Dieser Inhalt ist in Ihrem Suchdienst vorhanden, abgesehen von Ihren primären Datenspeichern, was für die in modernen Anwendungen erwarteten Antwortzeiten von Millisekunden notwendig ist. Außer in bestimmten Indizierungsszenarien wird der Suchdienst niemals eine Verbindung zu Ihren lokalen Daten herstellen oder diese abfragen.

Wenn Sie einen Suchindex erstellen und verwalten, wird Ihnen dieser Artikel helfen, das Folgende zu verstehen:

+ Inhalt (Dokumente und Schema)
+ Physische Darstellung
+ Einfache Vorgänge

Sie möchten am liebsten gleich selbst Hand anlegen? Siehe stattdessen [Erstellen eines Suchindexes](search-how-to-create-search-index.md).

## <a name="content-of-a-search-index"></a>Inhalt eines Suchindexes

Indizes in Cognitive Search enthalten *Suchdokumente*. Konzeptionell ist ein Dokument eine einzelne Einheit mit durchsuchbaren Daten im Index. Zum Beispiel könnte ein Einzelhändler ein Dokument für jedes Produkt haben, eine Nachrichtenorganisation ein Dokument für jeden Artikel, eine Reiseseite ein Dokument für jedes Hotel und jedes Reiseziel und so weiter. So lassen sich diese Konzepte vertrauteren Entsprechungen in der Datenbank zuordnen: Ein *Suchindex* entspricht einer *Tabelle*, und *Dokumente* entsprechen ungefähr den *Zeilen* einer Tabelle.

Die Struktur eines Dokuments wird durch das Indexschema bestimmt, wie unten dargestellt. Die Sammlung "fields" ist in der Regel der größte Teil eines Indexes, in dem jedes Feld benannt, einem [Datentyp](/rest/api/searchservice/Supported-data-types) zugeordnet und mit zulässigen Verhaltensweisen versehen ist, die bestimmen, wie es verwendet wird.

```json
{
  "name": "name_of_index, unique across the service",
  "fields": [
    {
      "name": "name_of_field",
      "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
      "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
      "filterable": true (default) | false,
      "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
      "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
      "key": true | false (default, only Edm.String fields can be keys),
      "retrievable": true (default) | false,
      "analyzer": "name_of_analyzer_for_search_and_indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
      "searchAnalyzer": "name_of_search_analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
      "indexAnalyzer": "name_of_indexing_analyzer", (only if 'searchAnalyzer' is set and 'analyzer' is not set)
      "synonymMaps": [ "name_of_synonym_map" ] (optional, only one synonym map per field is currently supported)
    }
  ],
  "suggesters": [ ],
  "scoringProfiles": [ ],
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) { },
  "encryptionKey":(optional){ }
  }
}
```

Andere Elemente sind der Kürze halber ausgeblendet, aber die folgenden Links können die Details liefern: 

+ [Suggestoren](index-add-suggesters.md) unterstützen Vorausabfragen wie die automatische Vervollständigung
+ [Bewertungsprofile](index-add-scoring-profiles.md) werden für die Relevanzabstimmung verwendet
+ [Analysatoren](search-analyzers.md) werden verwendet, um Zeichenketten nach linguistischen Regeln oder anderen vom Analysator unterstützten Merkmalen in Token zu zerlegen
+ CORS (Cross-Origin-Remote-Scripting) [ wird](search-how-to-create-search-index.md#corsoptions) für Anwendungen verwendet, die Anfragen von verschiedenen Domänen aus stellen
+ [Verschlüsselungsschlüssel](search-security-manage-encryption-keys.md) wird für die doppelte Verschlüsselung von sensiblen Inhalten im Index verwendet.

### <a name="field-definitions"></a>Felddefinitionen

Ein Suchdokument wird durch die Sammlung "Felder" definiert. Sie benötigen Felder für die Dokumentidentifikation (Schlüssel), in denen durchsuchbarer Text gespeichert ist, und Felder zur Unterstützung von Filtern, Facetten und Sortierungen. Möglicherweise benötigen Sie auch Felder für Daten, die einem Benutzer nie angezeigt werden. Beispielsweise können Sie Felder für Gewinnspannen oder Marketingaktionen verwenden, mit denen Sie den Suchrang bearbeiten können.

Wenn die eingehenden Daten hierarchischer Natur sind, können sie in einem Index als [komplexer Typ](search-howto-complex-data-types.md) dargestellt werden, der zur Darstellung verschachtelter Strukturen verwendet wird. Das integrierte Beispieldataset „Hotels“ veranschaulicht komplexe Typen anhand einer Adresse (enthält mehrere untergeordnete Felder) mit einer 1:1-Beziehung zu den einzelnen Hotels verwenden und einer komplexen Zimmersammlung, wobei jedem Hotel mehrere Zimmer zugeordnet sind. 

<a name="index-attributes"></a>

### <a name="field-attributes"></a>Feldattribute

Feldattribute bestimmen, wie ein Feld verwendet wird, z.B. ob es in der Volltextsuche, in der Facettennavigation, für Sortiervorgänge usw. verwendet wird. 

Zeichenfolgenfelder werden häufig als „durchsuchbar“ und „abrufbar“ gekennzeichnet. Zu den Feldern zum Eingrenzen der Suchergebnisse gehören „sortierbar“, „filterbar“ und „facettenreich“.

|Attribut|BESCHREIBUNG|  
|---------------|-----------------|  
|„durchsuchbar“ |Volltextsuche ist möglich, unterliegt einer lexikalischen Analyse, z.B. Worttrennung, während der Indizierung. Wenn Sie ein durchsuchbares Feld auf einen Wert wie „sunny day“ festlegen, wird es intern in die einzelnen Token „sunny“ und „day“ unterteilt. Weitere Informationen finden Sie unter [Funktionsweise der Volltextsuche](search-lucene-query-architecture.md).|  
|„filterbar“ |Wird in $filter-Abfragen angegeben. Filterbare Felder vom Typ `Edm.String` oder `Collection(Edm.String)` werden keiner Worttrennung unterzogen, sodass nur nach exakten Übereinstimmungen gesucht wird. Beispiel: Wenn Sie ein solches Feld „f“ auf „sunny day“ festlegen, werden mit `$filter=f eq 'sunny'` keine Übereinstimmungen gefunden, während Sie mit `$filter=f eq 'sunny day'` Suchergebnisse erhalten. |  
|„sortierbar“ |Standardmäßig sortiert das System Ergebnisse nach Bewertung, aber Sie können die Sortierung auf Grundlage von Feldern in den Dokumenten konfigurieren. Felder vom Typ `Collection(Edm.String)` können nicht „sortierbar“ sein. |  
|„facettenreich“ |Wird in der Regel in einer Darstellung der Suchergebnisse verwendet, die eine Trefferanzahl nach Kategorie umfasst (z.B. Hotels in einer bestimmten Stadt). Diese Option kann nicht für Felder vom Typ `Edm.GeographyPoint` verwendet werden. Felder vom Typ `Edm.String`, die „filterbar“, „sortierbar“ oder „facettenreich“ sind, dürfen höchstens 32 KB lang sein. Weitere Details finden Sie unter [Erstellen des Index (REST API)](/rest/api/searchservice/create-index).|  
|„Schlüssel“ |Eindeutiger Bezeichner für Dokumente im Index. Es muss genau ein Feld als Schlüsselfeld ausgewählt werden, und es muss vom Typ `Edm.String` sein.|  
|„abrufbar“ |Legt fest, ob das Feld in einem Suchergebnis zurückgegeben werden kann. Dies ist hilfreich, wenn Sie ein Feld (z.B. *Gewinnspanne*) zum Filtern, Sortieren oder Bewerten verwenden möchten, das Feld jedoch für den Endbenutzer nicht sichtbar sein soll. Dieses Attribut muss für `key`-Felder auf `true` gesetzt sein.|  

Obwohl Sie jederzeit neue Felder hinzufügen können, sind vorhandene Felddefinitionen für die Lebensdauer des Index gesperrt. Aus diesem Grund verwenden Entwickler in der Regel das Portal zum Erstellen einfacher Indizes und zum Testen von Ideen. Oder sie verwenden Seiten des Portals, um eine Einstellung zu suchen. Häufige Iterationen über einen Indexentwurf sind effizienter, wenn Sie einem codebasierten Ansatz folgen, sodass Sie den Index einfach neu erstellen können.

> [!NOTE]
> Die APIs, die Sie zum Erstellen eines Index verwenden, verfügen über unterschiedliches Standardverhalten. Für die [REST-APIs](/rest/api/searchservice/Create-Index) sind die meisten Attribute standardmäßig aktiviert (z. B. sind „durchsuchbar“ und „abrufbar“ für Zeichenfolgenfelder auf TRUE festgelegt). Sie müssen sie häufig nur festlegen, wenn Sie sie deaktivieren möchten. Für das .NET SDK gilt das Gegenteil. Für jede Eigenschaft, die Sie nicht explizit festlegen, wird das entsprechende Suchverhalten standardmäßig deaktiviert, sofern Sie es nicht ausdrücklich aktivieren.

<a name="index-size"></a>

## <a name="physical-representation"></a>Physische Darstellung

In Azure Cognitive Search ist die physische Struktur eines Indexes weitgehend eine interne Implementierung. Sie können auf das Schema zugreifen, den Inhalt abfragen, die Größe überwachen und die Kapazität verwalten, aber die Cluster selbst (Indizes, Shards und andere Dateien und Ordner) sind tabu und werden intern von Microsoft in Ihrem Namen verwaltet.

Die Größe eines Indexes wird bestimmt durch:

+ Umfang und Zusammensetzung Ihrer Dokumente
+ Indexkonfiguration (insbesondere, ob Sie Suggestoren einbeziehen)
+ Attribute zu einzelnen Feldern

Sie können die Indexgröße auf der Registerkarte Indizes im Azure-Portal überwachen oder eine [GET INDEX-Anforderung](/rest/api/searchservice/get-index) an Ihren Suchdienst stellen.

### <a name="factors-influencing-index-size"></a>Faktoren, die die Indexgröße beeinflussen

Die Zusammensetzung und Menge der Dokumente hängt davon ab, was Sie importieren möchten. Denken Sie daran, dass ein Suchindex nur durchsuchbare Inhalte enthalten sollte. Wenn Quelldokumente binäre Felder enthalten, würden Sie diese Felder in der Regel aus dem Indexschema auslassen (es sei denn, Sie verwenden KI-Anreicherung, um den Inhalt zu knacken und zu analysieren, um durchsuchbare Textinformationen zu erstellen).

Die Indexkonfiguration kann neben Dokumenten auch andere Komponenten enthalten, z. B. Suggestoren, Kundenanalysatoren, Bewertungsprofile, CORS-Einstellungen und Informationen zu Verschlüsselungsschlüsseln. Aus der obigen Liste ist die einzige Komponente, die sich auf die Indexgröße auswirken kann, die der Suggestoren. [**Suggesters**](index-add-suggesters.md) sind Konstrukte, die Type-Ahead- oder Autocomplete-Abfragen unterstützen. Wenn Sie also einen Suggestor einbinden, erstellt der Indizierungsprozess die Datenstrukturen, die für wortwörtliche Zeichenübereinstimmungen erforderlich sind. Suggestoren werden auf Feldebene implementiert. Wählen Sie daher nur die Felder aus, die sich für die Vorauswahl eignen.

Feldattribute sind der dritte Aspekt der Indexgröße. Attribute bestimmen das Verhalten. Um diese Verhaltensweisen zu unterstützen, erstellt der Indizierungsprozess die entsprechenden Datenstrukturen. Zum Beispiel ruft "searchable" die [Volltextsuche](search-lucene-query-architecture.md) auf, die invertierte Indizes nach dem tokenisierten Begriff durchsucht. Im Gegensatz dazu unterstützt ein Attribut "filterable" oder "sortable" die Iteration über unveränderte Zeichenketten.

### <a name="example-demonstrating-the-storage-implications-of-attributes-and-suggesters"></a>Beispiel zur Veranschaulichung der Auswirkungen von Attributen und Suggestoren auf die Speicherung

Der folgende Screenshot veranschaulicht die Indexspeichermuster aus verschiedenen Kombinationen von Attributen. Der Index basiert auf dem **Immobilien-Musterindex**, den Sie mit dem Datenimport-Assistenten und den integrierten Beispieldaten leicht erstellen können. Obwohl die Indexschemas nicht angezeigt werden, können Sie die auf dem Indexnamen basierenden Attribute ableiten. Beispielsweise ist für den *realestate-searchable*-Index das searchable-Attribut ausgewählt und sonst nichts, für den *realestate-retrievable*-Index ist das retrievable-Attribut ausgewählt und nichts anderes usw.

![Indexgröße basierend auf der Attributauswahl](./media/search-what-is-an-index/realestate-index-size.png "Indexgröße basierend auf der Attributauswahl")

Obwohl diese Indexvarianten etwas künstlich sind, können wir sie für allgemeine Vergleiche der Auswirkungen von Attributen auf die Speicherung heranziehen. Setzt die Einstellung „abrufbar“ die Indexgröße herauf? Nein. Setzt das Hinzufügen von Feldern zu einer **Vorschlagsfunktion** die Indexgröße herauf? Ja. 

Wenn Sie ein Feld filterbar oder sortierbar machen, erhöht dies auch den Speicherverbrauch, da gefilterte und sortierte Felder nicht tokenisiert werden, sodass Zeichensequenzen ausführlich abgeglichen werden können.

Außerdem werden in der obigen Tabelle auch die Auswirkungen von [Analysetools](search-analyzers.md) nicht berücksichtigt. Wenn Sie den Tokenizer edgeNgram zum Speichern von direkten Zeichensequenzen (a, ab, abc, abcd) verwenden, ist der Index größer als bei Verwendung eines Standardanalysetools.

## <a name="basic-operations"></a>Einfache Vorgänge

Nachdem Sie nun eine bessere Vorstellung davon haben, was ein Index ist, werden in diesem Abschnitt die Operationen zur Laufzeit eines Index vorgestellt, einschließlich der Verbindung zu einem einzelnen Index und dessen Sicherung.

### <a name="index-isolation"></a>Index-Isolierung
  
In der kognitiven Suche arbeiten Sie jeweils mit einem Index, wobei alle indexbezogenen Operationen auf einen einzigen Index abzielen. Es gibt kein Konzept für verwandte Indizes oder die Verknüpfung unabhängiger Indizes, weder für die Indizierung noch für die Abfrage. 

Beachten Sie bei der Verwaltung eines Indexes, dass es keine Portal- oder API-Unterstützung für das Verschieben oder Kopieren eines Indexes gibt. Stattdessen richten die Kunden ihre Lösung zur Anwendungsbereitstellung in der Regel auf einen anderen Suchdienst aus (wenn sie denselben Indexnamen verwenden) oder ändern den Namen, um eine Kopie des aktuellen Suchdienstes zu erstellen, und bauen ihn dann auf.

### <a name="continuously-available"></a>Ständig verfügbar

Ein Index ist ständig verfügbar, ohne dass er angehalten oder offline genommen werden kann. Da er für einen kontinuierlichen Betrieb ausgelegt ist, erfolgen alle Aktualisierungen seines Inhalts oder Ergänzungen des Index selbst in Echtzeit. Daher kann es vorkommen, dass Abfragen vorübergehend unvollständige Ergebnisse liefern, wenn eine Anforderung mit einer Dokumentenaktualisierung zusammenfällt.

Beachten Sie, dass die Abfragekontinuität für Dokumentoperationen (Aktualisieren oder Löschen) und für Änderungen besteht, die sich nicht auf die bestehende Struktur und Integrität des aktuellen Index auswirken (z. B. Hinzufügen neuer Felder). Wenn Sie strukturelle Aktualisierungen vornehmen müssen (Änderung vorhandener Felder), werden diese in der Regel durch einen Drop-and-Rebuild-Workflow in einer Entwicklungsumgebung oder durch die Erstellung einer neuen Version des Indexes im Produktionsdienst verwaltet.

Um einen Neuaufbau zu vermeiden, entscheiden sich einige Kunden, die kleine Änderungen vornehmen, für eine "Version" eines Feldes, indem sie ein neues Feld erstellen, das neben einer früheren Version existiert. Im Laufe der Zeit führt dies zu verwaisten Inhalten in Form von veralteten Feldern oder veralteten benutzerdefinierten Analyzer-Definitionen, insbesondere in einem Produktionsindex, der teuer zu replizieren ist. Sie können diese Probleme bei geplanten Aktualisierungen des Index im Rahmen des Index-Lebenszyklusverwaltung angehen.

### <a name="endpoint-connection-and-security"></a>Endpunktverbindung und Sicherheit

Alle Indizierungs- und Abfrage Anforderung zielen auf einen Index ab. Endpunkte sind in der Regel einer der folgenden:

| Endpunkt | Verbindung und Zugangskontrolle |
|----------|-------------------------------|
| `<your-service>.search.windows.net/indexes` | Zielt auf die Sammlung der Indizes. Wird bei der Erstellung, Auflistung oder Löschung eines Index verwendet. Für diese Vorgänge sind Administratorrechte erforderlich, die über Administrator-API-Schlüssel oder eine Search Contributor-Rolle verfügbar sind. |
| `<your-service>.search.windows.net/indexes/<your-index>/docs` | Zielt auf die Dokumentensammlung eines einzelnen Indexes ab. Wird verwendet, wenn ein Index abgefragt wird. Leserechte sind ausreichend und über Abfrage-API-Schlüssel oder eine Datenleserrolle verfügbar. |

## <a name="next-steps"></a>Nächste Schritte

Praktische Erfahrungen mit der Erstellung eines Index können Sie mit fast jedem Beispiel oder jeder exemplarischen Vorgehensweise für Cognitive Search sammeln. Als Einstieg können Sie einen der Schnellstarts im Inhaltsverzeichnis auswählen.

Sie sollten sich aber auch mit Methoden zum Laden eines Index mit Daten vertraut machen. Strategien zur Indexdefinition und zum Datenimport werden zusammen definiert. Die folgenden Artikel bieten Informationen zum Erstellen und Laden eines Index.

+ [Erstellen von Suchindizes in Azure Cognitive Search](search-how-to-create-search-index.md)

+ [Übersicht über den Datenimport](search-what-is-data-import.md)

+ [Hinzufügen, Aktualisieren oder Löschen von Dokumenten (REST)](/rest/api/searchservice/addupdate-or-delete-documents) 