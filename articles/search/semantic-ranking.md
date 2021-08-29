---
title: Semantische Rangfolge
titleSuffix: Azure Cognitive Search
description: Erfahren Sie, wie der semantische Ranking-Algorithmus in Azure Cognitive Search funktioniert.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2021
ms.openlocfilehash: 61fb6de73567b7a83e2c4db2010c717a160aebe0
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114473986"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Semantische Rangfolge in Azure Cognitive Search

> [!IMPORTANT]
> Die semantische Suche befindet sich in der öffentlichen Vorschau und unterliegt den [zusätzlichen Nutzungsbedingungen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Sie ist über das Azure-Portal, die Vorschau-REST-API und Beta-SDKs verfügbar. Diese Features sind abrechenbar. Weitere Informationen finden Sie unter [Verfügbarkeit und Preise](semantic-search-overview.md#availability-and-pricing).

Das semantische Ranking ist eine Erweiterung der Abfrageausführungspipeline, welche die Präzision verbessert, indem sie die Top-Treffer eines anfänglichen Ergebnissatzes neu ordnet. Das semantische Ranking wird von großen transformatorbasierten Netzwerken unterstützt, die für die Erfassung der semantischen Bedeutung von Abfragebegriffen trainiert wurden, im Gegensatz zum linguistischen Abgleich auf Schlüsselwörter. Im Gegensatz zum [Standardalgorithmus zum Priorisieren nach Ähnlichkeit](index-ranking-similarity.md) werden für die semantische Rangfolge der Kontext und die Bedeutung von Wörtern herangezogen, um die Relevanz zu bestimmen.

Das semantische Ranking ist sowohl ressourcen- als auch zeitintensiv. Um die Verarbeitung innerhalb der erwarteten Latenzzeit eines Abfragevorgangs abzuschließen, werden die Eingaben für den semantischen Ranker konsolidiert und reduziert, damit die zugrundeliegenden Zusammenfassungs- und Neuordnungsschritte so schnell wie möglich abgeschlossen werden können.

## <a name="pre-processing"></a>Vorabverarbeitung

Vor der Bewertung der Relevanz muss der Inhalt auf eine überschaubare Anzahl von Eingaben reduziert werden, die vom semantischen Ranker effizient verarbeitet werden können.

1. Zunächst beginnt die Inhaltsreduzierung mit der anfänglichen Ergebnismenge, die vom standardmäßigen [Ähnlichkeits-Ranking-Algorithmus](index-ranking-similarity.md) für die Stichwortsuche zurückgegeben wird. Für eine beliebige Abfrage können die Ergebnisse aus einer Handvoll Dokumente bestehen, bis zum maximalen Grenzwert von 1.000. Da die Verarbeitung einer großen Anzahl von Übereinstimmungen zu viel Zeit in Anspruch nimmt, gelangen nur die Top 50 in das semantische Ranking.

   Unabhängig von der Anzahl der Dokumente, ob eins oder 50, bildet die erste Ergebnismenge die erste Iteration des Dokumentenkorpus für das semantische Ranking.

1. Nachfolgend werden im gesamten Korpus die Inhalte der einzelnen Felder in "searchFields" extrahiert und zu einer langen Zeichenkette kombiniert.

1. Nach der Zeichen folgen Konsolidierung werden alle Zeichen folgen, die übermäßig lang sind, gekürzt, um sicherzustellen, dass die Gesamtlänge den Eingabe Anforderungen des Zusammenfassungs-Schritts entspricht.

   Aufgrund dieses Trimmvorgangs ist es wichtig, prägnante Felder zuerst in "searchFields" zu positionieren, um sicherzustellen, dass sie in der Zeichenkette enthalten sind. Wenn Sie sehr große Dokumente mit textlastigen Feldern haben, wird alles nach der Maximalgrenze ignoriert.

Jedes der 50 Dokumente wird nun durch eine einzelne lange Zeichenkette dargestellt.

Die Zeichenkette besteht aus Token, nicht aus Zeichen oder Wörtern. Die maximale Tokenanzahl beträgt 128 eindeutige Token. Zu Schätzungszwecken können Sie davon ausgehen, dass 128 Token ungefähr einer Zeichenfolge mit einer Länge von 128 Wörtern entsprechen. 

> [!NOTE]
>Die Tokenisierung wird teilweise von der Analyzer-Zuweisung für durchsuchbare Felder bestimmt. Wenn Sie spezialisierte Analysetools verwenden, z. b. nGram oder EdgeNGramm, sollten Sie dieses Feld aus den Suchfeldern ausschließen. Für Einblicke in die Tokenisierung von Zeichenketten, können Sie die Tokenausgabe eines Analyzers mithilfe der [Test Analyzer-REST-API](/rest/api/searchservice/test-analyzer)überprüfen.

## <a name="extraction"></a>Extraktion

Nach der Zeichenketten-Reduzierung ist es nun möglich, die reduzierten Eingaben durch maschinelle Lese-Interpretations- und Sprachrepräsentationsmodelle zu leiten, um festzustellen, welche Sätze und Phrasen das Dokument in Bezug auf die Anfrage am besten zusammenfassen. Diese Phase extrahiert Inhalt aus der Zeichenkette, die an den semantischen Ranker weitergeleitet wird.

Eingaben für die Zusammenfassung sind die langen Zeichenketten, die für jedes Dokument in der Vorbereitungsphase abgerufen werden. Aus jeder Zeichenkette findet das Zusammenfassungsmodell eine Passage, die am repräsentativsten ist. Diese Passage bildet auch eine [semantische Beschriftung](semantic-how-to-query-request.md) für das Dokument. Jede Beschriftung ist in einer reinen Textversion und einer Highlight-Version verfügbar und umfasst häufig weniger als 200 Wörter pro Dokument.

Eine [semantische Antwort](semantic-answers.md) wird auch zurückgegeben, wenn Sie den Parameter „Antworten" angegeben haben, wenn die Abfrage als Frage gestellt wurde, und wenn eine Passage in der langen Zeichenkette gefunden werden kann, die wahrscheinlich eine Antwort auf die Frage liefert.

## <a name="semantic-ranking"></a>Semantische Rangfolge

1. Beschriftungen werden hinsichtlich ihrer konzeptioneller und semantischer Relevanz bewertet, in Bezug zur bereitgestellten Abfrage.

   Im folgenden Diagramm wird veranschaulicht, was die „Semantik-Relevanz" bedeutet. Beachten Sie den Begriff "Kapital", der im Zusammenhang mit Finanzen, Gesetzen, Geografie oder Grammatik verwendet werden kann. Wenn eine Abfrage Begriffe aus dem gleichen Vektorraum enthält (z. B. „Kapital" und „Investition"), wird ein Dokument, das auch Token im gleichen Cluster enthält, höher bewertet als eines, bei dem dies nicht zutrifft.

   :::image type="content" source="media/semantic-search-overview/semantic-vector-representation.png" alt-text="Vektordarstellung für Kontext" border="true":::

1. Der @search.rerankerScore wird jedem Dokument auf der Grundlage der semantischen Beschriftungsrelevanz zugewiesen.

1. Nachdem alle Dokumente bewertet wurden, werden sie in absteigender Reihenfolge nach der Bewertung aufgelistet und in die Nutzlast der Abfrageantwort aufgenommen. Die Nutzlast umfasst Antworten, nur-Text und markierte Beschriftungen sowie alle Felder, die Sie als abrufbar markiert oder in einer Select-Klausel angegeben haben.

## <a name="next-steps"></a>Nächste Schritte

Die semantische Rangfolge wird in Standard-Tarifen in bestimmten Regionen angeboten. Weitere Informationen zur Verfügbarkeit und Registrierung finden Sie unter [Verfügbarkeit und Preisgestaltung](semantic-search-overview.md#availability-and-pricing). Ein neuer Abfragetyp ermöglicht die Rangfolge nach Relevanz, sowie Antwortstrukturen für die semantische Suche. [Erstellen Sie eine Semantikabfrage](semantic-how-to-query-request.md), um zu beginnen.

Alternativ können Sie auch die folgenden Artikel über die Standard-Rangliste analysieren. Das semantische Ranking hängt vom Ähnlichkeitsranking ab, um die ersten Ergebnisse zu liefern. Das Wissen über die Ausführung von Abfragen und das Ranking vermittelt Ihnen ein erweitertes Verständnis dafür, wie der gesamte Prozess funktioniert.nis der Funktionsweise des gesamten Prozesses.

+ [Volltextsuche in Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Ähnlichkeit und Bewertung in Azure Cognitive Search](index-similarity-and-scoring.md)
+ [Analysetools für Textverarbeitung in Azure Cognitive Search](search-analyzers.md)