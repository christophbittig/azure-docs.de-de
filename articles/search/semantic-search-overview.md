---
title: Semantische Suche
titleSuffix: Azure Cognitive Search
description: Hier finden Sie Informationen zur Verwendung von Deep Learning-Modellen für die semantische Suche von Bing in Cognitive Search, um intuitivere Suchergebnisse zu erhalten.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/01/2021
ms.custom: references_regions
ms.openlocfilehash: 18ed79768f1a803c4565426de67f6cde35a0a1fe
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131577990"
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

| Funktion | BESCHREIBUNG |
|---------|-------------|
| [Semantische Neubewertung](semantic-ranking.md) | Verwendet Kontext oder semantische Bedeutung, um eine neue Relevanzbewertung für vorhandene Ergebnisse zu berechnen. |
| [Semantische Titel und Markierungen](semantic-how-to-query-request.md) | Extrahiert Sätze und Ausdrücke aus einem Dokument, die den Inhalt am besten zusammenfassen, und hebt wichtige Passagen zum einfachen Überfliegen hervor. Titel, die ein Ergebnis zusammenfassen, sind nützlich, wenn einzelne Inhaltsfelder für die Ergebnisseite zu dicht sind. Der markierte Text hebt die relevantesten Begriffe und Ausdrücke hervor, sodass Benutzer schnell ermitteln können, warum eine Entsprechung als relevant eingestuft wurde. |
| [Semantische Antworten](semantic-answers.md) | Eine optionale und zusätzliche Unterstruktur, die von einer Semantikabfrage zurückgegeben wird. Sie bietet eine direkte Antwort auf eine Abfrage, die wie eine Frage aussieht. Ein Dokument muss Text mit den Merkmalen einer Antwort enthalten. |
| [Rechtschreibprüfung](speller-how-to-add.md) | Korrektur von Tippfehlern, bevor die Abfragebegriffe die Suchmaschine erreichen. |

## <a name="how-semantic-ranking-works"></a>Funktionsweise der semantischen Rangfolge

Die *semantische Rangfolge* sucht nach Kontext und Verwandtschaft zwischen Begriffen und stuft die Übereinstimmungen hoch, die gemäß Abfrage sinnvoller sind. Language Understanding findet Zusammenfassungen oder *Titel* und *Antworten* innerhalb Ihrer Inhalte und schließt diese in die Antwort mit ein, die dann für eine effektivere Suchfunktion auf einer Suchergebnisseite dargestellt werden kann.

Moderne vorab trainierte Modelle werden für Zusammenfassung und Rangfolge verwendet. Um die hohe Leistung zu gewährleisten, die Benutzer von der Suche erwarten, werden semantische Zusammenfassung und Rangfolge auf die ersten 50 Ergebnisse gemäß der Bewertung durch den standardmäßigen [Ähnlichkeitsalgorithmus für die Rangfolge](index-similarity-and-scoring.md#similarity-ranking-algorithms) angewendet. Wenn diese Ergebnisse als Dokumentkorpus verwendet werden, bewertet die semantische Rangfolge diese Ergebnisse basierend auf der semantischen Stärke der Entsprechung neu.

Die zugrunde liegende Technologie stammt von Bing und Microsoft Research und ist als Add-On-Feature in die Cognitive Search-Infrastruktur integriert. Weitere Informationen zu den Forschungs- und KI-Investitionen, die hinter der semantischen Suche stehen, finden Sie unter [How AI from Bing is powering Azure Cognitive Search (Microsoft Research Blog)](https://www.microsoft.com/research/blog/the-science-behind-semantic-search-how-ai-from-bing-is-powering-azure-cognitive-search/) (Wie die KI von Bing die Azure Cognitive Search unterstützt).

Im folgenden Video erhalten Sie eine Übersicht zu den Funktionen.

> [!VIDEO https://www.youtube.com/embed/yOf0WfVd_V0]

### <a name="order-of-operations"></a>Reihenfolge der Vorgänge

Komponenten der semantischen Suche erweitern die vorhandene Pipeline zur Abfrageausführung in beide Richtungen. Wenn Sie die Rechtschreibkorrektur aktivieren, korrigiert die [Rechtschreibprüfung](speller-how-to-add.md) die Tippfehler zu Beginn der Abfrage, bevor die Begriffe das Suchmodul erreichen.

:::image type="content" source="media/semantic-search-overview/semantic-workflow.png" alt-text="Semantikkomponenten in der Abfrageausführung" border="true":::

Die Ausführung der Abfrage verläuft wie gewohnt, mit Begriffsanalyse, Analyse und Scans über die invertierten Indizes. Die Engine ruft Dokumente mithilfe der Tokenübereinstimmung ab und bewertet die Ergebnisse mithilfe des standardmäßigen [Ähnlichkeitsalgorithmus für die Rangfolge](index-similarity-and-scoring.md#similarity-ranking-algorithms). Die Ergebnisse werden basierend auf dem Grad der linguistischen Ähnlichkeit zwischen Abfragebegriffen und übereinstimmenden Begriffen im Index berechnet. Bewertungsprofile werden in dieser Phase ebenfalls angewandt, sofern Sie sie definiert haben. Die Ergebnisse werden dann an das semantische Suchsubsystem weitergeleitet.

Im Vorbereitungsschritt wird der vom ursprünglichen Resultset zurückgegebene Dokumentkorpus auf Satz- und Absatzebene analysiert, um Passagen zu finden, die die einzelnen Dokumente zusammenfassen. Anders als bei der Schlüsselwortsuche verwendet dieser Schritt maschinelles Lesen und Verstehen, um den Inhalt auszuwerten. In dieser Phase der Inhaltsverarbeitung gibt eine semantische Abfrage [Titel](semantic-how-to-query-request.md) und [Antworten](semantic-answers.md) zurück. Um sie zu formulieren, nutzt die semantische Suche die Sprachrepräsentation, um Schlüsselpassagen zu extrahieren und hervorzuheben, die ein Ergebnis am besten zusammenfassen. Wenn die Suchabfrage eine Frage ist und Antworten angefordert wurden, enthält das Ergebnis auch eine Textpassage, die die von der Suchabfrage ausgedrückte Frage am besten beantwortet. 

Für Titel und Antworten wird vorhandener Text in der Formulierung verwendet. Die Semantikmodelle erstellen keine neuen Sätze oder Ausdrücke aus dem verfügbaren Inhalt und wenden auch keine Logik an, um neue Schlussfolgerungen zu treffen. Kurz gesagt, gibt das System niemals Inhalt zurück, der nicht bereits vorhanden ist.

Die Ergebnisse werden dann basierend auf der [konzeptionellen Ähnlichkeit](semantic-ranking.md) der Abfragebegriffe neu bewertet.

Um semantische Funktionen in Abfragen zu verwenden, müssen Sie geringfügige Änderungen an der [Suchanforderung](semantic-how-to-query-request.md) vornehmen. Es ist aber keine zusätzliche Konfiguration oder Neuindizierung erforderlich.

## <a name="semantic-capabilities-and-limitations"></a>Semantische Funktionen und Einschränkungen

Die semantische Suche ist eine neuere Technologie, weshalb es wichtig ist, festzulegen, was von ihr erwartet werden kann und was nicht. Sie kann beispielsweise die Qualität von Suchvorgängen verbessern:

* Übereinstimmungen, die semantisch näher an der Absicht der ursprünglichen Abfrage liegen, werden heraufgestuft.

* In jedem Ergebnis werden Zeichenfolgen gesucht, die als Beschriftungen und möglicherweise auch als Antworten verwendet werden können, die auf einer Suchergebnisseite gerendert werden können.

Sie kann eine Abfrage nicht erneut über den gesamten Korpus hinweg ausführen, um semantisch relevante Ergebnisse zu finden. Die semantische Suche bewertet das *vorhandene* Resultset neu, das aus den 50 besten Ergebnissen besteht, die vom Standardalgorithmus für die Rangfolge ermittelt wurden. Darüber hinaus kann die semantische Suche keine neuen Informationen oder Zeichenfolgen erstellen. Beschriftungen und Antworten werden wörtlich aus Ihren Inhalten extrahiert. Wenn die Ergebnisse also keinen Text enthalten, der einer Antwort ähnelt, erzeugen die Sprachmodelle keine Antwort.

Obwohl die semantische Suche nicht in jedem Szenario von Vorteil ist, können bestimmte Inhalte erheblich von ihren Funktionen profitieren. Die Sprachmodelle in der semantischen Suche funktionieren am besten für durchsuchbare Inhalte, die viele Informationen enthalten und in offener Textform strukturiert sind. Für Wissensdatenbanken, Onlinedokumentationen oder Dokumente, die beschreibende Inhalte enthalten, bieten die Funktionen der semantischen Suche die meisten Vorteile.

## <a name="availability-and-pricing"></a>Verfügbarkeit und Preismodell

Die semantische Suche ist nach der [Registrierung](https://aka.ms/SemanticSearchPreviewSignup) verfügbar. Die Registrierung gilt sowohl für die semantische Suche als auch für die Rechtschreibprüfung.

| Komponente | Tarif | Region | Registrieren | Preise |
|---------|------|--------|---------------------|-------------------|
| Semantische Suche (Rang, Titel, Hervorhebungen, Antworten) | Standardebene (S1, S2, S3) | „USA, Norden-Mitte“, „USA, Westen“, „USA, Westen 2“, „USA, Osten 2“, „Europa, Norden“, „Europa, Westen“ | Erforderlich | [Preisübersicht für Cognitive Search](https://azure.microsoft.com/pricing/details/search/)  |
| Rechtschreibprüfung | Basic<sup>1</sup> und höher  | All | Keine | Keine (kostenlos) |

<sup>1</sup> Aufgrund der Bereitstellungsmechanismen und der Lebensdauer freigegebener (kostenloser) Suchdienste verfügt eine kleine Anzahl von Diensten über eine kostenlose Rechtschreibprüfung. Die Verfügbarkeit der Rechtschreibprüfung für Dienste im Free-Tarif ist jedoch nicht garantiert und sollte nicht erwartet werden.

Gebühren für die semantische Suche werden erhoben, wenn Abfrageanforderungen „queryType=semantic“ enthalten und die Suchzeichenfolge nicht leer ist (z. B. „search=pet friendly hotels in new york“). Wenn Ihre Suchzeichenfolge leer ist („search=*“), werden Ihnen auch dann keine Gebühren berechnet, wenn „queryType“ auf „semantic“ festgelegt ist.

## <a name="disable-semantic-search"></a>Deaktivieren der semantischen Suche

Um den vollständigen Schutz vor einer versehentlichen Nutzung und den entsprechenden Gebühren sicherzustellen, können Sie die [semantische Suche deaktivieren](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#searchsemanticsearch). Verwenden Sie hierfür die API zum Erstellen oder Aktualisieren von Diensten in Ihrem Suchdienst. Nachdem das Feature deaktiviert wurde, werden alle Anforderungen abgelehnt, die den Typ „Semantikabfrage“ enthalten.

* Version 2021-04-01-Preview der Verwaltungs-REST-API bietet diese Option.

* Zum Deaktivieren von Features sind die Berechtigungen „Besitzer“ oder „Mitwirkender“ erforderlich.

```http
PUT https://management.azure.com/subscriptions/{{subscriptionId}}/resourcegroups/{{resource-group}}/providers/Microsoft.Search/searchServices/{{search-service-name}}?api-version=2021-04-01-Preview
    {
      "location": "{{region}}",
      "sku": {
        "name": "standard"
      },
      "properties": {
        "semanticSearch": "disabled"
      }
    }
```

Um die semantische Suche erneut zu aktivieren, führen Sie die obige Anforderung erneut aus, und legen Sie „semanticSearch“ entweder auf „free“ (Standardwert) oder „standard“ fest.

> [!TIP]
> Aufrufe der Verwaltungs-REST-API werden über Azure Active Directory authentifiziert. Anleitungen zum Einrichten eines Sicherheitsprinzipals und einer Anforderung finden Sie in diesem Blogbeitrag zu [Azure-REST-APIs mit Postman (2021)](https://blog.jongallant.com/2021/02/azure-rest-apis-postman-2021/). Das vorherige Beispiel wurde anhand der Anweisungen und der Postman-Sammlung im Blogbeitrag getestet.

## <a name="next-steps"></a>Nächste Schritte

Führen Sie die [Registrierung](https://aka.ms/SemanticSearchPreviewSignup) für die Vorschauversion über einen Suchdienst durch, der die tarif- und regionsbezogenen Anforderungen aus dem vorherigen Abschnitt erfüllt.

Die Verarbeitung der Anfrage kann bis zu zwei Werktage dauern. Sobald Ihr Dienst bereit ist, [erstellen Sie eine semantische Abfrage](semantic-how-to-query-request.md), um die Leistung für Ihren Inhalt zu bewerten.
