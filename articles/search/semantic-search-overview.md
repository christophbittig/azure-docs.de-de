---
title: Semantische Suche
titleSuffix: Azure Cognitive Search
description: Hier finden Sie Informationen zur Verwendung von Deep Learning-Modellen für die semantische Suche von Bing in Cognitive Search, um intuitivere Suchergebnisse zu erhalten.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/21/2021
ms.custom: references_regions
ms.openlocfilehash: 1b50fbbdd38d1bb24c1732c465784c3ddb757e3f
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114454780"
---
# <a name="semantic-search-in-azure-cognitive-search"></a>Semantische Suche in Azure Cognitive Search

> [!IMPORTANT]
> Die semantische Suche befindet sich in der öffentlichen Vorschau und unterliegt den [zusätzlichen Nutzungsbedingungen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Sie ist über das Azure-Portal, die Vorschau-REST-API und Beta-SDKs verfügbar. Diese Features sind abrechenbar. Weitere Informationen finden Sie unter [Verfügbarkeit und Preise](semantic-search-overview.md#availability-and-pricing).

Bei der semantischen Suche handelt es sich um eine Sammlung abfragebezogener Funktionen, die semantische Relevanz und Sprachverständnis in die Suchergebnisse einbringen. Dieser Artikel enthält eine allgemeine Einführung in die semantische Suche und Beschreibungen der einzelnen Features und ihrer Funktionsweise und gemeinsamen Verwendung. Das eingebettete Video enthält eine Beschreibung der Technologie, und im letzten Abschnitt geht es um die Verfügbarkeit und die Preise.

Die semantische Suche ist ein Premium-Feature. Es wird empfohlen, diesen Artikel zu lesen, um Hintergrundinformationen zu erhalten. Falls Sie aber direkt loslegen möchten, können Sie die folgenden Schritte ausführen:

> [!div class="checklist"]
> * [Überprüfen Sie die regionalen Anforderungen und die Dienstebenenanforderungen.](#availability-and-pricing)
> * [Registrieren Sie sich für das Vorschauprogramm.](https://aka.ms/SemanticSearchPreviewSignup) Die Verarbeitung der Anfrage kann bis zu zwei Werktage dauern.
> * Nach der Annahme können Sie Abfragen erstellen oder ändern, um [semantische Beschriftungen und Hervorhebungen](semantic-how-to-query-request.md) zurückzugeben.
> * Fügen Sie einige weitere Abfrageeigenschaften hinzu, um auch [semantische Antworten](semantic-answers.md) zurückzugeben.
> * Lösen Sie optional eine [Rechtschreibprüfung](speller-how-to-add.md) aus, um Genauigkeit und Treffer zu verbessern.

## <a name="what-is-semantic-search"></a>Was ist die semantische Suche?

Die semantische Suche ist eine Sammlung von Features, die die Qualität von Suchergebnissen verbessern. Wenn diese für Ihren Suchdienst aktiviert ist, erweitert sie die herkömmliche Abfrageausführungspipeline auf zwei Arten. Zum einen wird einem anfänglichen Resultset eine sekundäre Bewertung hinzugefügt, wodurch die Ergebnisse mit der höchsten semantischen Relevanz ganz oben in der Liste angezeigt werden. Zweitens werden Beschriftungen und Antworten extrahiert und zurückgegeben, die Sie auf einer Suchseite ausgeben können, um das Benutzererlebnis bei der Suche zu verbessern.

## <a name="how-semantic-ranking-works"></a>Funktionsweise der semantischen Rangfolge

Die *semantische Rangfolge* sucht nach Kontext und Verwandtschaft zwischen Begriffen und stuft die Übereinstimmungen hoch, die gemäß Abfrage sinnvoller sind. Language Understanding findet Zusammenfassungen oder *Titel* und *Antworten* innerhalb Ihrer Inhalte und schließt diese in die Antwort mit ein, die dann für eine effektivere Suchfunktion auf einer Suchergebnisseite dargestellt werden kann.

Moderne vorab trainierte Modelle werden für Zusammenfassung und Rangfolge verwendet. Um die hohe Leistung zu gewährleisten, die Benutzer von der Suche erwarten, werden semantische Zusammenfassung und Rangfolge auf die ersten 50 Ergebnisse gemäß der Bewertung durch den standardmäßigen [Ähnlichkeitsalgorithmus für die Rangfolge](index-similarity-and-scoring.md#similarity-ranking-algorithms) angewendet. Wenn diese Ergebnisse als Dokumentkorpus verwendet werden, bewertet die semantische Rangfolge diese Ergebnisse basierend auf der semantischen Stärke der Entsprechung neu.

Die zugrunde liegende Technologie stammt von Bing und Microsoft Research und ist als Add-On-Feature in die Cognitive Search-Infrastruktur integriert. Weitere Informationen zu den Forschungs- und KI-Investitionen, die hinter der semantischen Suche stehen, finden Sie unter [How AI from Bing is powering Azure Cognitive Search (Microsoft Research Blog)](https://www.microsoft.com/research/blog/the-science-behind-semantic-search-how-ai-from-bing-is-powering-azure-cognitive-search/) (Wie die KI von Bing die Azure Cognitive Search unterstützt).

Im folgenden Video erhalten Sie eine Übersicht zu den Funktionen.

> [!VIDEO https://www.youtube.com/embed/yOf0WfVd_V0]

## <a name="features-in-semantic-search"></a>Features der semantischen Suche

Die semantische Suche verbessert Genauigkeit und Treffer durch diese neuen Funktionen:

| Funktion | BESCHREIBUNG |
|---------|-------------|
| [Rechtschreibprüfung](speller-how-to-add.md) | Korrektur von Tippfehlern, bevor die Abfragebegriffe die Suchmaschine erreichen. |
| [Semantische Rangfolge](semantic-ranking.md) | Verwendung von Kontext oder semantischer Bedeutung, um eine Relevanzbewertung zu berechnen. |
| [Semantische Titel und Markierungen](semantic-how-to-query-request.md) | Sätze und Ausdrücke aus einem Dokument, die den Inhalt am besten zusammenfassen, mit Hervorhebungen wichtiger Passagen zum einfachen Scannen. Titel, die ein Ergebnis zusammenfassen, sind nützlich, wenn einzelne Inhaltsfelder für die Ergebnisseite zu dicht sind. Der markierte Text hebt die relevantesten Begriffe und Ausdrücke hervor, sodass Benutzer schnell ermitteln können, warum eine Entsprechung als relevant eingestuft wurde. |
| [Semantische Antworten](semantic-answers.md) | Eine optionale und zusätzliche Unterstruktur, die von einer Semantikabfrage zurückgegeben wird. Sie bietet eine direkte Antwort auf eine Abfrage, die wie eine Frage aussieht. |

### <a name="order-of-operations"></a>Reihenfolge der Vorgänge

Komponenten der semantischen Suche erweitern die vorhandene Pipeline zur Abfrageausführung in beide Richtungen. Wenn Sie die Rechtschreibkorrektur aktivieren, korrigiert die [Rechtschreibprüfung](speller-how-to-add.md) die Tippfehler zu Beginn der Abfrage, bevor die Begriffe das Suchmodul erreichen.

:::image type="content" source="media/semantic-search-overview/semantic-workflow.png" alt-text="Semantikkomponenten in der Abfrageausführung" border="true":::

Die Ausführung der Abfrage verläuft wie gewohnt, mit Begriffsanalyse, Analyse und Scans über die invertierten Indizes. Die Engine ruft Dokumente mithilfe der Tokenübereinstimmung ab und bewertet die Ergebnisse mithilfe des standardmäßigen [Ähnlichkeitsalgorithmus für die Rangfolge](index-similarity-and-scoring.md#similarity-ranking-algorithms). Die Ergebnisse werden basierend auf dem Grad der linguistischen Ähnlichkeit zwischen Abfragebegriffen und übereinstimmenden Begriffen im Index berechnet. Bewertungsprofile werden in dieser Phase ebenfalls angewandt, sofern Sie sie definiert haben. Die Ergebnisse werden dann an das semantische Suchsubsystem weitergeleitet.

Im Vorbereitungsschritt wird der vom ursprünglichen Resultset zurückgegebene Dokumentkorpus auf Satz- und Absatzebene analysiert, um Passagen zu finden, die die einzelnen Dokumente zusammenfassen. Anders als bei der Schlüsselwortsuche verwendet dieser Schritt maschinelles Lesen und Verstehen, um den Inhalt auszuwerten. In dieser Phase der Inhaltsverarbeitung gibt eine semantische Abfrage [Titel](semantic-how-to-query-request.md) und [Antworten](semantic-answers.md) zurück. Um sie zu formulieren, nutzt die semantische Suche die Sprachrepräsentation, um Schlüsselpassagen zu extrahieren und hervorzuheben, die ein Ergebnis am besten zusammenfassen. Wenn die Suchabfrage eine Frage ist und Antworten angefordert wurden, enthält das Ergebnis auch eine Textpassage, die die von der Suchabfrage ausgedrückte Frage am besten beantwortet. 

Für Titel und Antworten wird vorhandener Text in der Formulierung verwendet. Die Semantikmodelle erstellen keine neuen Sätze oder Ausdrücke aus dem verfügbaren Inhalt und wenden auch keine Logik an, um neue Schlussfolgerungen zu treffen. Kurz gesagt, gibt das System niemals Inhalt zurück, der nicht bereits vorhanden ist.

Die Ergebnisse werden dann basierend auf der [konzeptionellen Ähnlichkeit](semantic-ranking.md) der Abfragebegriffe neu bewertet.

Um semantische Funktionen in Abfragen zu verwenden, müssen Sie geringfügige Änderungen an der [Suchanforderung](semantic-how-to-query-request.md) vornehmen. Es ist aber keine zusätzliche Konfiguration oder Neuindizierung erforderlich.

## <a name="semantic-capabilities-and-limitations"></a>Semantische Funktionen und Einschränkungen

Die semantische Suche ist eine neuere Technologie, weshalb es wichtig ist, festzulegen, was von ihr erwartet werden kann und was nicht. Die Qualität der Suchergebnisse wird auf zwei Arten verbessert:

* Erstens werden Übereinstimmungen heraufgestuft, die semantisch näher an der Absicht der ursprünglichen Abfrage liegen.

* Zweitens können Ergebnisse einfacher genutzt werden, wenn Beschriftungen und potenziell auch Antworten auf der Seite vorhanden sind.

Die semantische Suche ist nicht in jedem Szenario von Vorteil. Stellen Sie daher zunächst sicher, dass Sie über Inhalte verfügen, die die zugehörigen Funktionen nutzen können. Die Sprachmodelle in der semantischen Suche funktionieren am besten für durchsuchbare Inhalte, die viele Informationen enthalten und in offener Textform strukturiert sind. Bei der Auswertung Ihrer Inhalte auf Antworten suchen die Modelle beispielsweise nach einer langen Zeichenfolge, die wie eine Antwort aussieht, und extrahieren diese. Es werden aber keine neuen Zeichenfolgen als Antworten auf eine Abfrage oder als Beschriftungen für ein passendes Dokument erstellt. Um die Frage „Welches Auto weist den niedrigsten Kraftstoffverbrauch auf?“ zu beantworten, sollte ein Index Sätze wie „Hybridautos weisen den niedrigsten Kraftstoffverbrauch aller auf dem Markt befindlichen Autos auf“ enthalten.

Die semantische Suche kann keine Informationen aus verschiedenen Inhaltsteilen in einem Dokument oder Dokumentkorpus ableiten. Bei der Abfrage „Resorthotels in einer Wüste“ ohne geografische Eingabe findet die Engine beispielsweise keine Übereinstimmungen für Hotels in Arizona oder Nevada, obwohl es in beiden Staaten Wüsten gibt. Ebenso berechnet die Engine für die Rückgabe keinen Zeitintervall basierend auf dem aktuellen Datum, wenn die Abfrage die Klausel „in den letzten fünf Jahren“ enthält. In Cognitive Search umfassen Mechanismen, die für die oben genannten Szenarios hilfreich sein können, sogenannte [Synonymzuordnungen](search-synonyms.md), mit denen Sie Zuordnungen zwischen sich nach außen hin unterscheidenden Begriffen erstellen können, und [Datenfilter](search-query-odata-filter.md), die als OData-Ausdruck angegeben sind.

## <a name="availability-and-pricing"></a>Verfügbarkeit und Preismodell

Die semantische Suche ist nach der [Registrierung](https://aka.ms/SemanticSearchPreviewSignup) verfügbar. Die Registrierung gilt sowohl für die semantische Suche als auch für die Rechtschreibprüfung.

| Komponente | Tarif | Region | Registrieren | Preise |
|---------|------|--------|---------------------|-------------------|
| Semantische Suche (Titel, Hervorhebungen, Antworten) | Standardebene (S1, S2, S3) | „USA, Norden-Mitte“, „USA, Westen“, „USA, Westen 2“, „USA, Osten 2“, „Europa, Norden“, „Europa, Westen“ | Erforderlich | [Preisübersicht für Cognitive Search](https://azure.microsoft.com/pricing/details/search/)  |
| Rechtschreibprüfung | Any | „USA, Norden-Mitte“, „USA, Westen“, „USA, Westen 2“, „USA, Osten 2“, „Europa, Norden“, „Europa, Westen“ | Erforderlich | Keine (kostenlos) |

Sie können die Rechtschreibprüfung ohne semantische Suche kostenlos verwenden. Gebühren für die semantische Suche werden erhoben, wenn Abfrageanforderungen `queryType=semantic` enthalten und die Suchzeichenfolge nicht leer ist (z. B. `search=pet friendly hotels in new york`). Leere Suchen (Abfragen der Form `search=*`) werden nicht in Rechnung gestellt, selbst wenn „queryType“ auf `semantic` festgelegt ist.

Wenn Sie keine semantische Suche für Ihren Suchdienst wünschen, können Sie die [semantische Suche deaktivieren](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#searchsemanticsearch), um eine versehentliche Nutzung und Gebühren zu vermeiden.

## <a name="next-steps"></a>Nächste Schritte

Führen Sie die [Registrierung](https://aka.ms/SemanticSearchPreviewSignup) für die Vorschauversion über einen Suchdienst durch, der die tarif- und regionsbezogenen Anforderungen aus dem vorherigen Abschnitt erfüllt.

Die Verarbeitung der Anfrage kann bis zu zwei Werktage dauern. Sobald Ihr Dienst bereit ist, [erstellen Sie eine semantische Abfrage](semantic-how-to-query-request.md), um die Leistung für Ihren Inhalt zu bewerten.
