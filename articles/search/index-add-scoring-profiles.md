---
title: Hinzufügen von Bewertungsprofilen für maximale Suchscores
titleSuffix: Azure Cognitive Search
description: Maximieren Sie die Relevanzscores von Suchergebnissen in Azure Cognitive Search, indem Sie Bewertungsprofile hinzufügen.
manager: nitinme
author: shmed
ms.author: ramero
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/16/2021
ms.openlocfilehash: 757290560f921ee87cf9454037a4f8f199283f29
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131449486"
---
# <a name="add-scoring-profiles-to-a-search-index"></a>Hinzufügen von Bewertungsprofilen zu einem Suchindex

Bei Volltextsuchabfragen berechnet eine Suchmaschine einen Suchscore für jedes übereinstimmende Dokument, wodurch die Ergebnisse der Relevanz nach absteigend priorisiert werden können. Azure Cognitive Search verwendet einen Standardscoringalgorithmus, um einen Anfangsscore zu berechnen. Sie können die Berechnung jedoch über ein *Bewertungsprofil* anpassen.

Bewertungsprofile sind in Indexdefinitionen eingebettet und enthalten Eigenschaften, um den Score von Übereinstimmungen höher zu gewichten, wobei zusätzliche Kriterien im Profil die Logik der höheren Gewichtung ermöglichen. Mit dieser Logik können Sie Übereinstimmungen z. B. auf Grundlage des Umsatzpotentials, der Aktualität von Artikeln oder der Lagerzeit von Waren höher gewichten.  

Sind Sie noch nicht mit Relevanzkonzepten vertraut? Das folgende Video bietet eine schnelle Übersicht über die Funktionsweise von Bewertungsprofilen in Azure Cognitive Search und führt einige Grundkonzepte ein. Weitere Hintergrundinformationen finden Sie unter [Ähnlichkeitspriorisierung und -bewertung](index-similarity-and-scoring.md).

> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=463&end=970]

## <a name="what-is-a-scoring-profile"></a>Was ist ein Bewertungsprofil?

Ein Bewertungsprofil ist Teil der Indexdefinition und besteht aus gewichteten Feldern, Funktionen und Parametern. Ein Bewertungsprofil dient dazu, übereinstimmende Dokumente auf Basis der von Ihnen festgelegten Kriterien höher zu gewichten. 

Die folgende Definition zeigt ein einfaches Profil namens "geo". Es gewichtet Ergebnisse höher, die den Suchbegriff im Feld „hotelName“ enthalten. Darüber hinaus verwendet es die `distance`-Funktion, um Ergebnisse zu bevorzugen, die sich im Umkreis von zehn Kilometern um den aktuellen Standort befinden. Wenn nach dem Begriff „inn“ gesucht wird und „inn“ Teil des Hotelnamens ist, werden die Dokumente in den Suchergebnissen weiter oben angezeigt, die Hotels in einem Umkreis von 10 km des aktuellen Standorts mit „inn“ im Namen enthalten.  

```json
"scoringProfiles": [
  {  
    "name":"geo",
    "text": {  
      "weights": {  
        "hotelName": 5
      }                              
    },
    "functions": [
      {  
        "type": "distance",
        "boost": 5,
        "fieldName": "location",
        "interpolation": "logarithmic",
        "distance": {
          "referencePointParameter": "currentLocation",
          "boostingDistance": 10
        }                        
      }                                      
    ]                     
  }            
]
```  

Für die Verwendung dieses Bewertungsprofils wird Ihre Abfrage so formuliert, dass der scoringProfile-Parameter in der Anforderung angegeben wird.

```http
POST /indexes/hotels/docs&api-version=2020-06-30
{
    "search": "inn",
    "scoringProfile": "geo",
    "scoringParameter": currentLocation--122.123,44.77233
}
```  

Diese Abfrage sucht nach dem Begriff "inn" und übergibt den aktuellen Standort. Beachten Sie, dass diese Abfrage weitere Parameter enthält, z. B. scoringParameter. Eine Beschreibung der Abfrageparameter finden Sie unter [Durchsuchen von Dokumenten (REST-API)](/rest/api/searchservice/Search-Documents).  

Im Abschnitt [Erweitertes Beispiel](#bkmk_ex) finden Sie ein ausführlicheres Beispiel eines Bewertungsprofils.  

<a name=what-is-default-scoring></a>

## <a name="how-scores-are-computed"></a>Berechnen von Scores

Scores werden für Volltextsuchabfragen zu dem Zweck berechnet, die relevantesten Übereinstimmungen zu priorisieren und sie am Anfang der Antwort zurückzusenden. Der Gesamtscore eines jeden Dokuments ist die Summe der einzelnen Feldscores, wobei der individuelle Score jedes Felds aus der Begriffs- und Dokumenthäufigkeit der durchsuchten Begriffe in diesem Feld berechnet wird ([TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) oder „Term Frequency-Inverse Document Frequency“ (Vorkommenshäufigkeit, inverse Dokumenthäufigkeit) genannt). 

> [!Tip]
> Mithilfe des Parameters [featuresMode](index-similarity-and-scoring.md#featuresmode-parameter-preview) können Sie zusätzliche Bewertungsdetails mit den Suchergebnissen (einschließlich der Feldebenenscores) anfordern.

## <a name="when-to-add-scoring-logic"></a>Wann Bewertungslogik hinzugefügt werden soll

Wenn das standardmäßige Rangfolgeverhalten Ihre Geschäftsziele nicht ausreichend unterstützt, sollten Sie ein oder mehrere Bewertungsprofile erstellen. Sie können beispielsweise festlegen, dass neu hinzugefügte Elemente hinsichtlich der Suchrelevanz bevorzugt werden sollen. Möglicherweise verfügen Sie auch über ein Feld, das die Gewinnspanne enthält, oder ein anderes Feld, das das Umsatzpotenzial angibt. Das Höhergewichten von Ergebnissen, die relevanter für Ihre Benutzer oder das Unternehmen sind, ist häufig der entscheidende Faktor bei der Einführung von Bewertungsprofilen.

Die relevanzbasierte Sortierung auf einer Suchseite wird ebenfalls über Bewertungsprofile implementiert. Betrachten Sie Suchergebnisseiten, die Sie in der Vergangenheit verwendet haben, mit denen Sie nach Preis, Datum, Bewertung oder Relevanz sortieren können. In Azure Cognitive Search wird Relevanz durch Bewertungsprofile gefördert. Die Definition von Relevanz wird von Ihnen gesteuert und basiert auf Ihren Geschäftszielen und der Suchfunktionalität, die Sie bereitstellen möchten.  

<a name="bkmk_ex"></a>

## <a name="extended-example"></a>Erweitertes Beispiel

Im folgenden Beispiel wird das Schema eines Indexes mit zwei Bewertungsprofilen (`boostGenre`, `newAndHighlyRated`) gezeigt. Jede Abfrage für diesen Index, die eines der Profile als Abfrageparameter enthält, verwendet das Profil zum Bewerten des Resultsets. 

Das Profil `boostGenre` verwendet gewichtete Textfelder und fördert Übereinstimmungen, die in den Feldern „albumTitle“, „genre“ und „interpretName“ gefunden werden. Die Felder werden jeweils um den Faktor 1,5, 5 und 2 höher gewichtet. Warum wird "genre" so viel stärker als die anderen Felder erhöht? Wird die Suche in relativ homogenen Daten durchgeführt (wie bei „genre“ in musicstoreindex), ist bei den relativen Gewichtungen möglicherweise eine größere Varianz erforderlich. In musicstoreindex ist „rock“ z. B. sowohl als „genre“ als auch in identisch formulierten Genrebeschreibungen aufgeführt. Wenn "genre" schwerer wiegen soll als die Genrebeschreibung, dann benötigt das Feld "genre" eine viel höhere relative Gewichtung.

```json
{  
  "name": "musicstoreindex",  
  "fields": [  
    { "name": "key", "type": "Edm.String", "key": true },  
    { "name": "albumTitle", "type": "Edm.String" },  
    { "name": "albumUrl", "type": "Edm.String", "filterable": false },  
    { "name": "genre", "type": "Edm.String" },  
    { "name": "genreDescription", "type": "Edm.String", "filterable": false },  
    { "name": "artistName", "type": "Edm.String" },  
    { "name": "orderableOnline", "type": "Edm.Boolean" },  
    { "name": "rating", "type": "Edm.Int32" },  
    { "name": "tags", "type": "Collection(Edm.String)" },  
    { "name": "price", "type": "Edm.Double", "filterable": false },  
    { "name": "margin", "type": "Edm.Int32", "retrievable": false },  
    { "name": "inventory", "type": "Edm.Int32" },  
    { "name": "lastUpdated", "type": "Edm.DateTimeOffset" }  
  ],  
  "scoringProfiles": [  
    {  
      "name": "boostGenre",  
      "text": {  
        "weights": {  
          "albumTitle": 1.5,  
          "genre": 5,  
          "artistName": 2  
        }  
      }  
    },  
    {  
      "name": "newAndHighlyRated",  
      "functions": [  
        {  
          "type": "freshness",  
          "fieldName": "lastUpdated",  
          "boost": 10,  
          "interpolation": "quadratic",  
          "freshness": {  
            "boostingDuration": "P365D"  
          }  
        },  
        {
          "type": "magnitude",  
          "fieldName": "rating",  
          "boost": 10,  
          "interpolation": "linear",  
          "magnitude": {  
            "boostingRangeStart": 1,  
            "boostingRangeEnd": 5,  
            "constantBoostBeyondRange": false  
          }  
        }  
      ]  
    }  
  ],  
  "suggesters": [  
    {  
      "name": "sg",  
      "searchMode": "analyzingInfixMatching",  
      "sourceFields": [ "albumTitle", "artistName" ]  
    }  
  ]   
}  
```  

## <a name="steps-for-adding-a-scoring-profile"></a>Schritte zum Hinzufügen eines Bewertungsprofils

Um ein benutzerdefiniertes Bewertungsverhalten zu implementieren, fügen Sie dem Schema, das den Index definiert, ein Bewertungsprofil hinzu. Sie können in einem Index bis zu 100 Bewertungsprofile verwenden (siehe [Diensteinschränkungen](search-limits-quotas-capacity.md)), aber Sie können in einer Abfrage jeweils nur ein Profil angeben.

1. Beginnen Sie mit einer Indexdefinition. Sie können Bewertungsprofile für einen bestehenden Index hinzufügen und aktualisieren, ohne diesen neu erstellen zu müssen. Verwenden Sie eine [Update Index](/rest/api/searchservice/update-index)-Anforderung (Index aktualisieren), um Ihre Überarbeitung zu veröffentlichen.

1. Fügen Sie [Vorlage](#bkmk_template) aus diesem Artikel ein.  

1. Geben Sie einen Namen ein. Bewertungsprofile sind optional, wenn Sie jedoch ein Profil hinzufügen, ist der Name erforderlich. Achten Sie darauf, die [Namenskonventionen](/rest/api/searchservice/naming-rules) von Cognitive Search einzuhalten (mit einem Buchstaben beginnen, Sonderzeichen und reservierte Wörter vermeiden).  

1. Geben Sie die Relevanzkriterien an. Ein einzelnes Profil kann [gewichtete Felder](#weighted-fields), [Funktionen](#functions) oder beides enthalten. 

Sie sollten iterativ mit einem Dataset arbeiten, das Ihnen hilft, die Effektivität eines bestimmten Profils nachzuweisen oder zu widerlegen.

Bewertungsprofile können wie im folgenden Screenshot gezeigt im Azure-Portal oder programmgesteuert über [REST-APIs](/rest/api/searchservice/update-index) oder über Azure SDKs definiert werden, z. B. über die [ScoringProfile](/dotnet/api/azure.search.documents.indexes.models.scoringprofile)-Klasse im Azure SDK für .NET.

   :::image type="content" source="media/scoring-profiles/portal-add-scoring-profile-small.png" alt-text="Seite „Add scoring profiles“ (Bewertungsprofile hinzufügen)" lightbox="media/scoring-profiles/portal-add-scoring-profile.png" border="true":::

<a name="weighted-fields"></a>

### <a name="using-weighted-fields"></a>Verwenden gewichteter Felder

Verwenden Sie gewichtete Felder, wenn der Feldkontext wichtig ist und Abfragen eine Volltextsuche ausführen (auch Freiformtextsuche genannt). Wenn eine Abfrage z. B. den Begriff "flughafen" enthält, sollten "flughafen" im Beschreibungsfeld eine höhere Gewichtung haben als im Feld „HotelName“. 

Gewichtete Felder bestehen aus einem durchsuchbaren Feld und einer positiven Zahl, die als Multiplikator verwendet wird. Wenn der ursprüngliche Feldscore von HotelName 3 lautet, entspricht der höher gewichtete Score dieses Felds 6, was zu einem höheren Gesamtscore des übergeordneten Dokuments beiträgt.

```json
"scoringProfiles": [  
{  
  "name": "boostKeywords",  
  "text": {  
    "weights": {  
      "HotelName": 2,  
      "Description": 5 
    }  
  }  
}
```

<a name="functions"></a>

### <a name="using-functions"></a>Verwenden von Funktionen

Verwenden Sie Funktionen, wenn einfache relative Gewichtungen nicht ausreichen oder sich nicht anwenden lassen. Dies ist z. B. im Fall von distance und freshness der Fall, da es sich hier um Berechnungen für numerische Daten handelt. Sie können pro Bewertungsprofil mehrere Funktionen angeben.

| Funktion | BESCHREIBUNG |
|-|-|
| "freshness" | Höhere Gewichtung auf Basis der Werte in einem DateTime-Feld (Edm.DataTimeOffset). Diese Funktion verfügt über ein `boostingDuration`-Attribut, sodass Sie einen Wert für die Zeitspanne angeben können, in der die höhere Gewichtung erfolgt. | 
| "magnitude" | Höhere Gewichtung auf Basis der Größe eines numerischen Werts. Szenarien, die diese Funktion erforderlich machen, umfassen die Verstärkung nach Gewinnspanne, Höchstpreis, Mindestpreis oder Downloadanzahl. Diese Funktion kann nur mit Edm.Double- und Edm.Int-Feldern verwendet werden. Für die magnitude-Funktion können Sie den Bereich umkehren, wenn Sie das umgekehrte Muster anwenden möchten (z. B. um preiswerteren Artikeln eine höhere Relevanz als teureren zuzuweisen). Bei einem Preisbereich von 100 bis 1 USD würden Sie "boostingRangeStart" auf 100 und "boostingRangeEnd" auf 1 festlegen, um die preiswerteren Artikel höher zu gewichten. | 
| "distance"  | Höhere Gewichtung auf Basis der Nähe oder des geografischen Standorts. Diese Funktion kann nur mit Edm.GeographyPoint-Feldern verwendet werden. | 
| "tag"  | Höhere Gewichtung auf Basis von Tags, die sowohl für Suchdokumente als auch für Abfragezeichenfolgen gebräuchlich sind. Tags werden als `tagsParameter` bereitgestellt. Diese Funktion kann nur mit Edm.String- und Collection(Edm.String)-Feldern verwendet werden. | 

### <a name="rules-for-using-functions"></a>Regeln für die Verwendung von Funktionen

+ Funktionen können nur auf Felder angewendet werden, die als filterbar attribuiert sind.
+ Der Funktionstyp ("freshness", "magnitude", "distance", "tag") muss in Kleinbuchstaben angegeben werden.
+ Funktionen dürfen nicht Null sein oder leere Werte enthalten.

<a name="bkmk_template"></a>

## <a name="template"></a>Vorlage

 In diesem Abschnitt wird die Syntax und die Vorlage für die Bewertungsprofile veranschaulicht. Beschreibungen zu den Attributen von Bewertungsprofilen finden Sie im nächsten Abschnitt unter [Eigenschaftenreferenz](#bkmk_indexref).  

```json
"scoringProfiles": [  
  {   
    "name": "name of scoring profile",   
    "text": (optional, only applies to searchable fields) {   
      "weights": {   
        "searchable_field_name": relative_weight_value (positive #'s),   
        ...   
      }   
    },   
    "functions": (optional) [  
      {   
        "type": "magnitude | freshness | distance | tag",   
        "boost": # (positive number used as multiplier for raw score != 1),   
        "fieldName": "(...)",   
        "interpolation": "constant | linear (default) | quadratic | logarithmic",   

        "magnitude": {
          "boostingRangeStart": #,   
          "boostingRangeEnd": #,   
          "constantBoostBeyondRange": true | false (default)
        }  

        // ( - or -)  

        "freshness": {
          "boostingDuration": "..." (value representing timespan over which boosting occurs)   
        }  

        // ( - or -)  

        "distance": {
          "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)   
          "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)   
        }   

        // ( - or -)  

        "tag": {
          "tagsParameter":  "..."(parameter to be passed in queries to specify a list of tags to compare against target field)   
        }
      }
    ],   
    "functionAggregation": (optional, applies only when functions are specified) "sum (default) | average | minimum | maximum | firstMatching"   
  }   
],   
"defaultScoringProfile": (optional) "...", 
```  

<a name="bkmk_indexref"></a> 

## <a name="property-reference"></a>Eigenschaftsverweis

|Attribut|BESCHREIBUNG|  
|---------------|-----------------|  
| name | Erforderlich. Dies ist der Name des Bewertungsprofils. Er folgt denselben Benennungskonventionen, die für Felder gelten. Er muss mit einem Buchstaben beginnen, darf keine Punkte, Doppelpunkte oder @-Symbole enthalten und darf nicht mit dem Begriff „azureSearch“ (Groß-/Kleinschreibung wird beachtet) beginnen.|  
| text | Enthält die weights-Eigenschaft.|  
| weights | Optional. Name-Wert-Paare, die ein durchsuchbares Feld und eine positive ganze Zahl oder Gleitkommazahl angeben, um die der Score eines Felds verstärkt werden soll. Die positive ganze Zahl wird zu einem Multiplikator für den ursprünglichen Feldscore, der vom Priorisierungsalgorithmus generiert wird. Wenn ein Feldscore beispielsweise 2 und der Gewichtungswert 3 ist, lautet der höher gewichtete Score des Felds 6. Einzelne Feldscores werden dann aggregiert, um einen Dokumentfeldscore zu berechnen, mit dem dann das Dokument im Resultset bewertet wird. |  
| functions | Optional. Eine Bewertungsfunktion kann nur auf filterbare Felder angewendet werden.|  
| functions > type | Erforderlich für Bewertungsfunktionen. Gibt den Typ der zu verwendenden Funktion an. Gültige Werte sind „magnitude“, „freshness“, „distance“ und „tag“. Sie können in jedem Bewertungsprofil mehrere Funktionen einbeziehen. Der Funktionsname muss in Kleinbuchstaben angegeben werden.|  
| functions > boost | Erforderlich für Bewertungsfunktionen. Eine positive Zahl, die als Multiplikator für die unformatierte Bewertung verwendet wird. Sie darf nicht gleich 1 sein.|  
| functions > fieldname | Erforderlich für Bewertungsfunktionen. Eine Bewertungsfunktion kann nur auf Felder angewendet werden, die Teil der Feldauflistung für den Index und filterbar sind. Darüber hinaus führt jeder Funktionstyp zusätzliche Einschränkungen ein („freshness“ wird für Datetime-Felder, „magnitude“ für Integer- oder Double-Felder und „distance“ für Location-Felder verwendet). Sie können pro Funktionsdefinition nur ein einzelnes Feld angeben. Um z. B. "magnitude" zweimal in demselben Profil verwenden zu können, müssen Sie zwei Definitionen für "magnitude", eine für jedes Feld, einbeziehen.|  
| functions > interpolation | Erforderlich für Bewertungsfunktionen. Definiert die Steigung, für die die Bewertungsverstärkung vom Anfang bis zum Ende des Bereichs zunimmt. Gültige Werte sind „Linear“ (Standard), „Constant“, „Quadratic“ und „Logarithmic“. Details finden Sie unter [Festlegen von Interpolationen](#bkmk_interpolation) .|  
| functions > magnitude | Die Bewertungsfunktion für die Größe wird dazu verwendet, um Rangfolgen auf Basis des Wertebereichs für ein numerisches Feld zu ändern. Einige der gängigsten Nutzungsbeispiele sind: </br></br>Sternebewertungen: Ändern Sie die Bewertung basierend auf dem Wert innerhalb des Felds "Star Rating" (Sternebewertung). Wenn zwei Elemente relevant sind, wird das Element mit der höheren Bewertung zuerst angezeigt. </br>Gewinnspanne: Wenn zwei Dokumente relevant sind, müssen Einzelhändler möglicherweise Dokumente höher gewichten, die eine höhere Gewinnspanne aufweisen. </br>Anzahl der Klicks: Für Anwendungen, in denen Klicks auf Produkte oder Seiten nachverfolgt werden, könnten Sie mithilfe der magnitude-Funktion Artikel höher gewichten, die tendenziell den meisten Datenverkehr verzeichnen. </br>Anzahl der Downloads: Für Anwendungen, in denen Downloads nachverfolgt werden, können Sie mit der magnitude-Funktion Artikel höher gewichten, die am meisten heruntergeladen wurden.|  
| functions > magnitude > boostingRangeStart | Legt den Anfangswert des Bereichs fest, über den die Größe bewertet wird. Der Wert muss vom Typ „Integer“ oder „Gleitkomma“ sein. Für Sternbewertungen von 1 bis 4 wäre dies die 1. Für Gewinnspannen von über 50 % wäre dies die 50.|  
| functions > magnitude > boostingRangeEnd | Legt den Endwert des Bereichs fest, über den die Größe bewertet wird. Der Wert muss vom Typ „Integer“ oder „Gleitkomma“ sein. Für Sternbewertungen von 1 bis 4 wäre dies die 4.|  
| functions > magnitude > constantBoostBeyondRange | Gültige Werte sind "true" oder "false" (Standard). Bei "true" wird die vollständige Verstärkung weiterhin auf Dokumente angewendet, die einen Wert für das Zielfeld aufweisen, der über dem oberen Ende des Bereichs liegt. Bei "false" wird die Verstärkung dieser Funktion nicht auf Dokumente angewendet, die einen Wert für das Zielfeld aufweisen, der außerhalb des Bereichs liegt.|  
| functions > freshness | Die Bewertungsfunktion für die Aktualität wird dazu verwendet, um Rangfolgenbewertungen für Elemente auf Basis von Werten in DateTimeOffset-Feldern zu ändern. Ein Element mit einem aktuelleren Datum kann z. B. höher als ältere Elemente eingestuft werden. </br></br>Elemente wie z. B. Kalenderereignisse können mit in der Zukunft liegenden Daten so eingestuft werden, dass Ereignisse mit geringerem Abstand zur Gegenwart höher als Ereignisse eingestuft werden, die weiter in der Zukunft liegen. </br></br>Im aktuellen Service Release wird ein Ende des Bereichs auf die aktuelle Zeit festgelegt. Das andere Ende ist ein Zeitpunkt in der Vergangenheit, der auf boostingDuration basiert. Wenn Sie einen Bereich von zukünftigen Zeitpunkten höher gewichten möchten, verwenden Sie einen negativen Wert für boostingDuration. </br></br>Die Rate, mit der die Verstärkung von einem maximalen und minimalen Bereich wechselt, wird durch die Interpolation bestimmt, die auf das Bewertungsprofil angewendet wird (siehe folgende Abbildung). Wählen Sie zum Umkehren des angewendeten Verstärkungsfaktors einen Verstärkungsfaktor, der kleiner ist als 1.|  
| functions > freshness > boostingDuration | Legt eine Ablaufdauer fest, nach der die Verstärkung für ein bestimmtes Dokument beendet wird. Informationen zur Syntax und Beispiele finden Sie im folgenden Abschnitt unter [Festlegen von boostingDuration](#bkmk_boostdur).|  
| functions > distance | Die Bewertungsfunktion für den Abstand wird dazu verwendet, um auf Basis des Abstands relativ zu einem geografischen Standort Einfluss auf die Bewertung von Dokumenten zu nehmen. Die Referenzposition wird als Teil der Abfrage in einem Parameter (mithilfe des Abfrageparameters „scoringParameter“) als lon,lat-Argument angegeben.|  
|functions >  distance > referencePointParameter | Ein Parameter, der als Referenzposition in Abfragen übergeben werden soll (mit dem Abfrageparameter „scoringParameter“). Beschreibungen zu den Abfrageparametern finden Sie unter [Durchsuchen von Dokumenten (REST-API)](/rest/api/searchservice/Search-Documents).|  
| functions > distance > boostingDistance | Dies muss eine ganze Zahl sein. Legt den Abstand in Kilometern vom Referenzstandort fest, an dem der Verstärkungsbereich endet.|  
| functions > tag | Die Bewertungsfunktion "tag" wird verwendet, um die Bewertung von Dokumenten auf Grundlage der Tags in Dokumenten und Suchabfragen zu beeinflussen. Dokumente, die dieselben Tags wie die Suchabfrage enthalten, werden verstärkt. Die Tags für die Suchabfrage werden in jeder Suchanforderung (mithilfe des Abfrageparameters „scoringParameter“) als Bewertungsparameter bereitgestellt. |  
| functions > tag > tagsParameter | Ein Parameter, der in Abfragen übergeben werden soll, um (mit dem Abfrageparameter „scoringParameter“) Tags für eine bestimmte Anforderung anzugeben. Beschreibungen zu den Abfrageparametern finden Sie unter [Durchsuchen von Dokumenten (REST-API)](/rest/api/searchservice/Search-Documents).|  
| functions > functionAggregation | Optional. Gilt nur, wenn Funktionen angegeben sind. Gültige Werte sind: „sum“ (Standard), „average“, „minimum“, „maximum“ und „firstMatching“. Eine Suchbewertung entspricht einem einzelnen Wert, der aus mehreren Variablen berechnet wird, einschließlich mehrerer Funktionen. Dieses Attribut gibt an, wie die Verstärkungen aller Funktionen zu einer einzelnen Gesamtverstärkung zusammengefasst werden, die dann auf die Basisdokumentbewertung angewendet wird. Die Basisbewertung basiert auf dem [tf-idf](http://www.tfidf.com/)-Wert, der aus dem Dokument und der Suchabfrage berechnet wird.|  
| defaultScoringProfile | Beim Ausführen einer Suchanforderung wird die Standardbewertung verwendet (nur [tf-idf](http://www.tfidf.com/)), wenn kein Bewertungsprofil angegeben ist. </br></br>Sie können die integrierte Standardeinstellung außer Kraft setzen und dabei ein benutzerdefiniertes Profil als zu verwendendes Profil ersetzen, wenn in der Suchanforderung kein bestimmtes Profil angegeben wird.|  

<a name="bkmk_interpolation"></a> 

## <a name="set-interpolations"></a>Festlegen von Interpolationen

Interpolationen ermöglichen Ihnen das Festlegen der Form der für die Bewertung verwendete Steigung. Da Bewertung von hoch zu niedrig verläuft, nimmt die Steigung immer ab, die Interpolation bestimmt jedoch die Kurve des Gefälles. Die folgenden Interpolationen können verwendet werden:  

| Interpolation | BESCHREIBUNG |  
|-|-|  
|`linear`|Für Elemente, die sich innerhalb des maximalen und minimalen Bereichs befinden, erfolgt die auf das Element angewendete Verstärkung mit konstant abnehmender Stärke. "Linear" ist die Standardinterpolation für ein Bewertungsprofil.|  
|`constant`|Für Elemente, die sich innerhalb des Anfangs- und Endbereichs befinden, wird eine konstante Verstärkung auf die Rangfolgeergebnisse angewendet.|  
|`quadratic`|Im Vergleich zur linearen Interpolation, die eine konstant abnehmende Verstärkung aufweist, erfolgt die Abnahme bei „Quadratic“ anfänglich mit geringerer Geschwindigkeit, während zum Bereichsende hin die Abnahme in viel größeren Schritten erfolgt. Diese Interpolationsoption ist in Tag-Bewertungsfunktionen nicht zulässig.|  
|`logarithmic`|Im Vergleich zur linearen Interpolation, die eine konstant abnehmende Verstärkung aufweist, erfolgt die Abnahme bei „Logarithmic“ anfänglich mit höherer Geschwindigkeit, während zum Bereichsende hin die Abnahme in viel kleineren Schritten erfolgt. Diese Interpolationsoption ist in Tag-Bewertungsfunktionen nicht zulässig.|  

 ![Linien „constant“, „linear“, „quadratic“, „log10“ im Diagramm](media/scoring-profiles/azuresearch_scorefunctioninterpolationgrapht.png "AzureSearch_ScoreFunctionInterpolationGrapht")  

<a name="bkmk_boostdur"></a> 

## <a name="set-boostingduration"></a>Festlegen von boostingDuration

`boostingDuration` ist ein Attribut der Funktion `freshness`. Sie können damit eine Ablaufdauer festlegen, nach der die Verstärkung für ein bestimmtes Dokument beendet wird. Um beispielsweise eine Produktlinie oder Marke für einen zehntägigen Werbezeitraum zu verstärken, können Sie den zehntägigen Zeitraum für diese Dokumente z. B. als "P10D" angeben.  

`boostingDuration` muss als XSD-Wert "dayTimeDuration" formatiert sein (eine eingeschränkte Teilmenge eines ISO 8601-Zeitdauerwerts). Das Muster dafür ist: „P[nD][T[nH][nM][nS]]“.  

Die folgende Tabelle enthält einige Beispiele.  

|Duration|boostingDuration|  
|--------------|----------------------|  
|1 Tag|"P1D"|  
|2 Tage und 12 Stunden|"P2DT12H"|  
|15 Minuten|"PT15M"|  
|30 Tage, 5 Stunden, 10 Minuten und 6,334 Sekunden|"P30DT5H10M6.334S"|  

Weitere Beispiele finden Sie unter [XML-Schema: Datentypen (W3.org-Website)](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration).  

## <a name="see-also"></a>Weitere Informationen

+ [Ähnlichkeit und Bewertung in Azure Cognitive Search](index-similarity-and-scoring.md)
+ [REST-API-Referenz](/rest/api/searchservice/)
+ [Index erstellen (Azure Cognitive Search-REST-API)](/rest/api/searchservice/create-index)
+ [.NET SDK für die kognitive Azure-Suche](/dotnet/api/overview/azure/search?)
+ [Was sind Bewertungsprofile?](https://social.technet.microsoft.com/wiki/contents/articles/26706.azure-search-what-are-scoring-profiles.aspx)
