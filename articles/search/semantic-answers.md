---
title: Zurückgeben einer semantischen Antwort
titleSuffix: Azure Cognitive Search
description: In diesem Artikel werden die Zusammensetzung einer semantischen Antwort und das Abrufen von Antworten aus einem Resultset beschrieben.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/27/2021
ms.openlocfilehash: 3a8c00dcb2bf016cf70f02dcfdebc5e55bf5d66c
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2021
ms.locfileid: "122356236"
---
# <a name="return-a-semantic-answer-in-azure-cognitive-search"></a>Zurückgeben einer semantischen Antwort in Azure Cognitive Search

> [!IMPORTANT]
> Die semantische Suche befindet sich in der öffentlichen Vorschau und unterliegt [zusätzlichen Nutzungsbedingungen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Sie ist über das Azure-Portal, die Vorschau-REST-API und Beta-SDKs verfügbar. Diese Features sind abrechenbar. Weitere Informationen finden Sie unter [Verfügbarkeit und Preise](semantic-search-overview.md#availability-and-pricing).

Beim Aufrufen von [semantischen Rangfolgen und Beschriftungen](semantic-how-to-query-request.md) können Sie optional Inhalte aus den am besten passenden Dokumenten extrahieren, die die Abfrage direkt „beantworten“. Das Abfrageergebnis kann aus einer oder mehreren Antworten bestehen, die Sie dann auf einer Suchseite rendern können, um die Benutzeroberfläche Ihrer App zu verbessern.

In diesem Artikel erfahren Sie, wie Sie eine semantische Antwort anfordern und das Abfrageergebnis entpacken sowie welche Inhaltsmerkmale am ehesten zu qualitativ hochwertigen Antworten führen.

## <a name="prerequisites"></a>Voraussetzungen

Alle für [Semantikabfragen](semantic-how-to-query-request.md#prerequisites) geltenden Voraussetzungen gelten auch für Antworten, einschließlich der [Dienstebene und der Region](semantic-search-overview.md#availability-and-pricing).

+ Die Abfragelogik muss die Semantikabfrageparameter „queryType=semantic“ sowie den Parameter „anwers“ enthalten. Die erforderlichen Parameter werden in diesem Artikel erläutert.

+ Vom Benutzer eingegebene Abfragezeichenfolgen müssen wie eine Frage formuliert werden (was, wo, wann, wie).

+ Die Dokumente für die Suche im Index müssen Text mit den Merkmalen einer Antwort enthalten, der in einem der unter searchFields aufgeführten Felder stehen muss. Wenn z. B. eine Abfrage „Was ist eine Hash-Tabelle“ lautet und keines der searchFields Passagen enthält, die „Eine Hash-Tabelle ist ...“ enthalten, ist es unwahrscheinlich, dass eine Antwort zurückgegeben wird.

## <a name="what-is-a-semantic-answer"></a>Was ist eine semantische Antwort?

Eine semantische Antwort ist eine Unterstruktur einer [Semantikabfrage-Antwort](semantic-how-to-query-request.md). Sie besteht aus einem oder mehreren wörtlich aus einem durchsuchten Dokument übernommenen Abschnitten, die als Antwort auf eine Abfrage formuliert sind, die wie eine Frage aussieht. Damit eine Antwort zurückgegeben werden kann, muss ein durchsuchtes Dokument Formulierungen oder Sätze enthalten, die die sprachlichen Merkmale einer Antwort aufweisen, und die Abfrage selbst muss eine Frage sein.

Cognitive Search verwendet für das Auswählen der besten Antworten ein Modell für maschinelles Leseverständnis. Dieses Modell erzeugt eine Reihe potenzieller Antworten aus dem verfügbaren Inhalt. Wenn eine ausreichend hohe Konfidenz erreicht wird, schlägt es schließlich eine Antwort vor.

Antworten werden als unabhängige Objekte der obersten Ebene in den Nutzdaten für das Abfrageergebnis zurückgegeben. Sie können auswählen, ob diese Objekte auf den Suchseiten zusammen mit den Suchergebnissen gerendert werden sollen. Strukturell gesehen handelt es sich um ein Array-Element innerhalb der Antwort, das aus Text, einem Dokumentenschlüssel und einem Vertrauensergebnis besteht.

<a name="query-params"></a>

## <a name="how-to-specify-answers-in-a-query-request"></a>Angeben von „Antworten“ in einer Abfrageanforderung

Damit eine semantische Antwort zurückgegeben wird, muss die Abfrage den semantischen Parameter „queryType“, „queryLanguage“, „searchFields“ und „Antworten“ enthalten. Mit der Angabe des Parameters „answers“ wird nicht garantiert, dass Sie eine Antwort erhalten. Die Anforderung muss diesen Parameter jedoch enthalten, damit die Antwortverarbeitung überhaupt aufgerufen wird.

Der Parameter „searchFields“ ist wichtig, damit in Bezug auf Inhalt und Reihenfolge eine qualitativ hochwertige Antwort zurückgegeben werden kann (siehe unten). 

```json
{
    "search": "how do clouds form",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "searchFields": "title,locations,content",
    "answers": "extractive|count-3",
    "count": "true"
}
```

+ Abfragezeichenfolgen dürfen nicht NULL sein und sollten als Fragen formuliert werden.

+ „queryType“ muss auf „semantic“ festgelegt werden.

+ „queryLanguage“ muss einer der Werte in der Liste der [unterstützten Sprachen (REST-API)](/rest/api/searchservice/preview-api/search-documents#queryLanguage) sein.

+ „searchFields“ bestimmt, welche Zeichenfolgenfelder Token für das Extraktionsmodell bereitstellen. Die gleichen Felder, die Beschriftungen liefern, liefern auch Antworten. Eine genaue Anleitung zum Festlegen dieses Felds, sodass es für Beschriftungen und Antworten funktioniert, finden Sie unter [Festlegen von searchFields](semantic-how-to-query-request.md#searchfields). 

+ Die grundlegende Struktur für den Parameter „Antworten“ ist `"answers": "extractive"`, wobei standardmäßig eine Antwort zurückgegeben wird. Sie können die Anzahl der Antworten erhöhen, indem Sie wie im obigen Beispiel gezeigt eine Anzahl (`count`) von maximal zehn hinzufügen.  Ob Sie mehr als eine Antwort benötigen, hängt von der Benutzeroberfläche Ihrer App ab sowie davon, wie die Ergebnisse gerendert werden sollen.

## <a name="deconstruct-an-answer-from-the-response"></a>Dekonstruieren einer Antwort aus dem Abfrageergebnis

Antworten werden im Array @search.answers bereitgestellt, das ganz oben im Abfrageergebnis angezeigt wird. Wenn keine Antwort gefunden wurde, wird als Abfrageergebnis `"@search.answers": []` angezeigt. Stellen Sie beim Entwerfen einer Suchergebnisseite, auf der Antworten angezeigt werden, sicher, dass Sie auch Fälle berücksichtigen, in denen keine Antwort gefunden wird.

In @search.answers ist mit „key“ der Dokumentschlüssel oder die Dokument-ID des Treffers gemeint. Mit einem Dokumentschlüssel können Sie die [API für die Dokumentsuche](/rest/api/searchservice/lookup-document) verwenden, um einzelne oder alle Teile des durchsuchten Dokuments abzurufen, um diese auf der Suchseite oder einer Detailseite anzuzeigen.

Der Inhalt von „text“ und „highlights“ ist identisch, einmal als Nur-Text und einmal mit Hervorhebungen. Hervorhebungen werden standardmäßig als `<em>` formatiert. Dies kann mit den Parametern highlightPreTag und highlightPostTag überschrieben werden. Wie bereits erwähnt, besteht eine Antwort aus wörtlich aus einem durchsuchten Dokument übernommenen Inhalten. Das Extraktionsmodell sucht nach Antwortmerkmalen, um die richtigen Inhalte zu finden, verfasst im Abfrageergebnis jedoch keinen neuen Text.

Bei dem Wert für „score“ handelt es sich um eine Konfidenzbewertung, die widerspiegelt, wie gut die entsprechende Antwort ist. Wenn das Abfrageergebnis mehrere Antworten enthält, wird basierend auf dieser Bewertung die Reihenfolge festgelegt. Die besten Antworten und Beschriftungen können aus verschiedenen durchsuchten Dokumenten stammen. So kann die beste Antwort aus einem Dokument sein und die beste Beschriftung aus einem anderen. Allgemein werden Sie jedoch in den einzelnen Arrays dieselben Dokumente an den höheren Positionen sehen.

Auf die Antworten folgt das Array „value“, das immer Bewertungen, Beschriftungen und alle Felder enthält, die standardmäßig abgerufen werden können. Wenn Sie den Parameter „select“ verwendet haben, enthält das Array „value“ nur die von Ihnen angegebenen Felder. Weitere Informationen zu den einzelnen Elementen in Abfrageergebnissen finden Sie unter [Erstellen einer Semantikabfrage in Azure Cognitive Search](semantic-how-to-query-request.md).

Bei der Abfrage „how do clouds form“ (Wie entstehen Wolken?) wird im Abfrageergebnis die folgende Antwort zurückgegeben:

```json
{
    "@search.answers": [
        {
            "key": "4123",
            "text": "Sunlight heats the land all day, warming that moist air and causing it to rise high into the   atmosphere until it cools and condenses into water droplets. Clouds generally form where air is ascending (over land in this case),   but not where it is descending (over the river).",
            "highlights": "Sunlight heats the land all day, warming that moist air and causing it to rise high into the   atmosphere until it cools and condenses into water droplets. Clouds generally form<em> where air is ascending</em> (over land in this case),   but not where it is<em> descending</em> (over the river).",
            "score": 0.94639826
        }
    ],
    "value": [
        {
            "@search.score": 0.5479723,
            "@search.rerankerScore": 1.0321671911515296,
            "@search.captions": [
                {
                    "text": "Like all clouds, it forms when the air reaches its dew point—the temperature at which an air mass is cool enough for its water vapor to condense into liquid droplets. This false-color image shows valley fog, which is common in the Pacific Northwest of North America.",
                    "highlights": "Like all<em> clouds</em>, it<em> forms</em> when the air reaches its dew point—the temperature at    which an air mass is cool enough for its water vapor to condense into liquid droplets. This false-color image shows valley<em> fog</em>, which is common in the Pacific Northwest of North America."
                }
            ],
            "title": "Earth Atmosphere",
            "content": "Fog is essentially a cloud lying on the ground. Like all clouds, it forms when the air reaches its dew point—the temperature at  \n\nwhich an air mass is cool enough for its water vapor to condense into liquid droplets.\n\nThis false-color image shows valley fog, which is common in the Pacific Northwest of North America. On clear winter nights, the \n\nground and overlying air cool off rapidly, especially at high elevations. Cold air is denser than warm air, and it sinks down into the \n\nvalleys. The moist air in the valleys gets chilled to its dew point, and fog forms. If undisturbed by winds, such fog may persist for \n\ndays. The Terra satellite captured this image of foggy valleys northeast of Vancouver in February 2010.\n\n\n",
            "locations": [
                "Pacific Northwest",
                "North America",
                "Vancouver"
            ]
    ]
        }
}

```

## <a name="tips-for-producing-high-quality-answers"></a>Tipps für qualitativ hochwertige Antworten

Für optimale Ergebnisse sollte der Korpus mit Dokumenten für die semantischen Antworten über die folgenden Eigenschaften verfügen:

+ „searchfields“ muss Felder bereitstellen, die ausreichenden Text bieten, in dem wahrscheinlich eine Antwort gefunden wird. Es kann nur ein wortgetreuer Text aus einem Dokument als Antwort angezeigt werden.

+ Abfragezeichenfolgen dürfen nicht NULL sein (search = `*`). Außerdem sollte die Zeichenfolge im Gegensatz zu einer Schlagwortsuche (Liste mit beliebigen aufeinanderfolgenden Begriffen oder Formulierungen) Fragemerkmale aufweisen. Wenn es keine Antwort auf die Abfragezeichenfolge zu geben scheint, wird die Antwortverarbeitung übersprungen, auch wenn in der Anforderung der Abfrageparameter „answers“ angegeben wurde.

+ Bei der semantischen Extraktion und Zusammenfassung gibt es Einschränkungen in Bezug darauf, wie viele Token pro Dokument schnell analysiert werden können. Für die Praxis bedeutet dies Folgendes: Wenn Sie über große Dokumente mit Hunderten von Seiten verfügen, sollten Sie zunächst versuchen, deren Inhalt in überschaubarere Teile aufzuteilen.

## <a name="next-steps"></a>Nächste Schritte

+ [Übersicht über die semantische Suche](semantic-search-overview.md)
+ [Semantische Priorisierung in Azure Cognitive Search](semantic-ranking.md)
+ [Ähnlichkeitsalgorithmus für die Rangfolge](index-ranking-similarity.md)
+ [Erstellen einer Semantikabfrage](semantic-how-to-query-request.md)
