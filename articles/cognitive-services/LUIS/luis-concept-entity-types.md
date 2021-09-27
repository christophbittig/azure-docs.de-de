---
title: Entitätstypen – LUIS
description: Eine Entität extrahiert zur Vorhersagelaufzeit Daten aus einer Benutzeräußerung. Ein _optionaler_ sekundärer Zweck besteht darin, die Vorhersage der Absicht oder anderer Entitäten zu verstärken, indem die Entität als Feature verwendet wird.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/26/2021
ms.openlocfilehash: 7eb4affb75816789dca094f9ec6e2ae19a222285
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128582379"
---
# <a name="entities-in-luis"></a>Entitäten in LUIS

Eine Entität ist ein Element, das für die Absicht des Benutzers relevant ist. Entitäten definieren Daten, die aus der Äußerung extrahiert werden können und von entscheidender Bedeutung sind, um die angeforderte Aktion eines Benutzers durchführen zu können. Beispiel:

|Äußerung|Vorhergesagte Absicht|Extrahierte Entitäten|Erklärung|
|--|--|--|--|
|Hallo, wie geht es dir?|Greeting (Begrüßung)|-|Nichts zu extrahieren.|
|Ich möchte eine kleine Pizza bestellen.|orderPizza| „klein“ | Die Entität „Size“ (Größe) wird als „klein“ extrahiert.|
|Schalte das Licht im Schlafzimmer aus.|turnOff| „Schlafzimmer“ | Die Entität „Room“ (Zimmer) wird als „Schlafzimmer“ extrahiert.|
|Prüfe das Guthaben auf meinem Sparkonto, das auf 4406 endet.|checkBalance| „Sparkonto“, „4406“ | Die Entität „accountType“ wird als „Sparkonto“ und die Entität „accountNumber“ als „4406“ extrahiert.|
|Kaufe 3 Tickets nach New York|buyTickets| „3“, „New York“ | Die Entität „ticketsCount“ wird als „3“ und die Entität „Destination“ als „New York“ extrahiert.|

Entitäten sind optional, werden aber empfohlen. Sie müssen nicht für jedes Konzept in Ihrer App Entitäten erstellen, sondern nur für diejenigen, für die folgende Bedingungen gelten:

* Die Clientanwendung benötigt die Daten. 
* Die Entität fungiert als Hinweis oder Signal für eine andere Entität oder Absicht. Weitere Informationen zu Entitäten als Features finden Sie unter [Entitäten als Features](#entities-as-features).

## <a name="entity-types"></a>Entitätstypen

Um eine Entität zu erstellen, müssen Sie ihr einen Namen und einen Typ zuweisen. Es gibt mehrere Arten von Entitäten in LUIS. 

## <a name="list-entity"></a>Entität vom Typ „List“

Eine Listenentität stellt einen festen, abgeschlossenen Satz verwandter Wörter zusammen mit ihren Synonymen dar. Sie können Listenentitäten verwenden, um mehrere Synonyme oder Variationen zu erkennen und dafür eine normalisierte Ausgabe zu extrahieren. Suchen Sie mithilfe der Option *Empfehlung* nach Vorschlägen für neue Wörter basierend auf der aktuellen Liste. 

Eine Listenentität wird nicht durch maschinelles Lernen erworben, daher findet LUIS keine zusätzlichen Werte für Listenentitäten. LUIS kennzeichnet jede Übereinstimmung für ein Element in einer Liste in der Antwort als eine Entität.

Beim Abgleich in Listenentitäten ist die Groß-/Kleinschreibung relevant, und nur genaue Übereinstimmungen werden extrahiert. Normalisierte Werte werden beim Abgleich der Listenentität ebenfalls verwendet. Beispiel:

|Normalisierter Wert|Synonyme|
|--|--|
|Klein|winzig, kleinste|
|Medium|normal, mittel, Durchschnitt, mittelgroß|
|Groß|größte|

Weitere Informationen finden Sie im [Referenzartikel zu Listenentitäten](reference-entity-list.md).

## <a name="regex-entity"></a>RegEx-Entität

Eine RegEx-Entität extrahiert eine Entität anhand eines regulären Ausdrucks, den Sie bereitstellen. Die Groß-/Kleinschreibung sowie die Kultur werden ignoriert. Reguläre Ausdrücke eignen sich am besten für strukturierten Text oder eine vordefinierte Sequenz alphanumerischer Werte, die in einem bestimmten Format erwartet werden. Beispiel:

|Entität|Regulärer Ausdruck|Beispiel|
|--|--|--|
|Flugnummer|Flug [A-Z]{2} [0-9]{4}| Flug AS 1234|
|Credit Card Number (Kreditkartennummer)|[0-9]{16}|5478789865437632|

Weitere Informationen finden Sie im [Referenzartikel zu RegEx-Entitäten](reference-entity-regular-expression.md).

## <a name="prebuilt-entity"></a>Vordefinierte Entität

LUIS bietet eine Reihe vordefinierter Entitäten zum Erkennen gängiger Datentypen wie Name, Datum, Zahl und Währung.  Das Verhalten der vordefinierten Entitäten kann nicht geändert werden. Die Unterstützung vordefinierter Entitäten variiert je nach Kultur der LUIS-App. Beispiel:

|Vordefinierte Entität|Beispielwert|
|--|--|
|PersonName|James, Bill, Tom|
|DatetimeV2|2019-05-02, 2. Mai, 8 Uhr am 2. Mai 2019|

Weitere Informationen finden Sie im [Referenzartikel zu vordefinierten Entitäten](./luis-reference-prebuilt-entities.md).

## <a name="patternany-entity"></a>Pattern.Any-Entität

Eine Pattern.Any-Entität ist ein Platzhalter variabler Länge, der nur in der Vorlagenäußerung eines Musters verwendet wird, um den Beginn und das Ende der Entität zu markieren. Diese Entität folgt einer bestimmten Regel oder einem bestimmten Muster und wird am besten für Sätze mit fester lexikalischer Struktur verwendet. Beispiel:

|Beispieläußerung|Muster|Entität|
|--|--|--|
|Ich hätte gerne einen Burger, bitte.|Ich hätte gerne {meal}[, bitte][.]| Burger
|Ich hätte gerne eine Pizza.|Ich hätte gerne {meal}[, bitte][.]| pizza
|Wo finde ich „Der große Gatsby“?|Wo finde ich „{bookName}“?| Der große Gatsby|

Weitere Informationen finden Sie im [Referenzartikel zu Pattern.Any-Entitäten](./reference-entity-pattern-any.md).

## <a name="machine-learned-ml-entity"></a>Durch maschinelles Lernen (ML) erworbene Entität

Eine durch maschinelles Lernen erworbene Entität verwendet Kontext, um Entitäten basierend auf beschrifteten Beispielen zu extrahieren. Diese Entität stellt die bevorzugte Entität für das Erstellen von LUIS-Anwendungen dar. Sie basiert auf Algorithmen für maschinelles Lernen und erfordert Beschriftungen, um erfolgreich auf Ihre Anwendung zugeschnitten zu werden. Verwenden Sie eine ML-Entität, um Daten zu identifizieren, die nicht immer optimal formatiert sind, aber dieselbe Bedeutung aufweisen. 

|Beispieläußerung|Extrahierte Entität für *Produkt*|
|--|--|
|Ich möchte ein Buch kaufen.|„Buch“|
|Kann ich bitte diese Schuhe haben?|„Schuhe“|
|Diese Shorts meinem Warenkorb hinzufügen|„Shorts“|

Weitere Informationen zu durch maschinelles Lernen erworbenen Entitäten finden Sie [hier](./reference-entity-machine-learned-entity.md).

Weitere Informationen finden Sie im [Referenzartikel zu durch maschinelles Lernen erworbenen Entitäten](./reference-entity-machine-learned-entity.md).

#### <a name="ml-entity-with-structure"></a>ML-Entität mit Struktur

Eine ML-Entität kann aus kleineren untergeordneten Entitäten bestehen, von denen jede über eigene Eigenschaften verfügen kann. Beispielsweise könnte *Adresse* die folgende Struktur aufweisen:

* Adresse: 4567 Main Street, NY, 98052, USA
    * Gebäudenummer: 4567
    * Straßenname: Main Street
    * Bundesstaat: NY
    * Postleitzahl: 98052
    * Land: USA


## <a name="building-effective-ml-entities"></a>Erstellen effektiver ML-Entitäten

Befolgen Sie die folgenden bewährten Methoden, um durch maschinelles Lernen erworbene Entitäten effektiv zu erstellen:

* Wenn Sie eine durch maschinelles Lernen erworbene Entität mit untergeordneten Entitäten verwenden, sollten Sie sicherstellen, dass die verschiedenen Reihenfolgen und Varianten der Entität und der untergeordneten Entitäten in den beschrifteten Äußerungen dargestellt werden. Beispieläußerungen mit Bezeichnungen sollten alle gültigen Formen sowie die Entitäten enthalten, die angezeigt werden, fehlen oder innerhalb der Äußerung neu angeordnet werden.

* Vermeiden Sie eine Überanpassung der Entitäten an eine feste Gruppe. Eine Überanpassung tritt auf, wenn das Modell nicht gut generalisiert wird. Dies ist ein gängiges Problem bei Machine Learning-Modellen. Es führt dazu, dass die App mit neuen Typen von Beispielen nicht richtig funktioniert. Daher sollten Sie die beschrifteten Beispieläußerungen variieren, damit die App über die von Ihnen angegebenen Beispiele hinaus generalisiert werden kann.

* Ihre Bezeichnungen sollten für alle Absichten einheitlich sein. Dazu gehören auch Äußerungen, die Sie in der Absicht *None* (Keine) angeben, die diese Entität umfasst. Andernfalls kann das Modell die Sequenz nicht effektiv bestimmen.

## <a name="entities-as-features"></a>Entitäten als Features

Eine weitere wichtige Funktion von Entitäten besteht darin, sie als Features oder Unterscheidungsmerkmale für andere Absichten oder Entitäten zu verwenden, damit Ihr System sie beobachtet und daraus lernt.

## <a name="entities-as-features-for-intents"></a>Entitäten als Features für Absichten

Sie können Entitäten als Signal für eine Absicht verwenden. Beispielsweise kann das Vorhandensein einer bestimmten Entität in der Äußerung kennzeichnen, welcher Absicht sie zuzuordnen ist.

|Beispieläußerung|Entität|Intent|
|--|--|--|
|Buche mir einen *Flug nach New York*.|City|Flug buchen|
|Buche mir den *Hauptkonferenzraum*.|Raum|Raum reservieren|

## <a name="entities-as-feature-for-entities"></a>Entitäten als Feature für Entitäten

Sie können Entitäten auch als Indikator für das Vorhandensein anderer Entitäten verwenden. Ein gängiges Beispiel hierfür ist die Verwendung einer vordefinierten Entität als Feature für eine andere ML-Entität.
Wenn Sie ein Flugbuchungssystem erstellen und Ihre Äußerung „Buche mir einen Flug von Kairo nach Seattle“ lautet, verfügen Sie über die ML-Entitäten *Abflugort* und *Zielort*. Eine bewährte Methode wäre die Verwendung der vordefinierten Entität `GeographyV2` als Feature für beide Entitäten.

Weitere Informationen finden Sie im [Referenzartikel zu GeographyV2-Entitäten](./luis-reference-prebuilt-geographyv2.md).

Sie können Entitäten auch als erforderliche Features für andere Entitäten verwenden. Dies hilft bei der Auflösung extrahierter Entitäten. Wenn Sie beispielsweise eine Anwendung zur Pizzabestellung erstellen und über eine ML-Entität `Size` verfügen, können Sie eine Listenentität `SizeList` erstellen und als erforderliches Feature für die Entität `Size` verwenden. Ihre Anwendung gibt den normalisierten Wert als extrahierte Entität aus der Äußerung zurück. 

Weitere Informationen zur Auflösung vordefinierter Entitäten, die in Ihrer Kultur verfügbar sind, finden Sie unter [Features](luis-concept-feature.md) und [Vordefinierte Entitäten](./luis-reference-prebuilt-entities.md). 


## <a name="entity-prediction-status-and-errors"></a>Status der Entitätsvorhersage und Fehler

Im LUIS-Portal wird Folgendes angezeigt, wenn die Entität eine andere Entitätsvorhersage aufweist als die Entität, die Sie für eine Beispieläußerung beschriftet haben. Der Unterschied beim Score kommt vom aktuellen trainierten Modell. 

:::image type="content" source="./media/luis-concept-entities/portal-entity-prediction-error.png" alt-text="Im LUIS-Portal sehen Sie, ob die Entität in einer Beispieläußerung eine andere Entitätsvorhersage aufweist als die Entität, die Sie für eine Beispieläußerung ausgewählt haben.":::

Der Text, der den Fehler verursacht, wird in der Beispieläußerung hervorgehoben, und die Beispieläußerungszeile weist rechts einen Fehlerindikator in Form eines roten Dreiecks auf. 

Probieren Sie zum Beheben von Entitätsfehlern einen oder mehrere der folgenden Schritte aus:

* Der markierte Text ist falsch bezeichnet. Um dies zu beheben, überprüfen Sie die Beschriftung, korrigieren Sie sie, und trainieren Sie die App erneut. 
* Erstellen Sie ein [Feature](luis-concept-feature.md) für die Entität, um das Konzept der Entität zu identifizieren.
* Fügen Sie weitere [Beispieläußerungen](luis-concept-utterance.md) hinzu, und beschriften Sie sie mit der Entität.
* [Überprüfen von Vorschlägen für aktives Lernen](luis-concept-review-endpoint-utterances.md) für Äußerungen, die vom Vorhersageendpunkt empfangen werden und zur Ermittlung des Konzepts der Entität beitragen können


## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über gute [Beispieläußerungen](luis-concept-utterance.md).
* Weitere Informationen zum Hinzufügen von Entitäten zu LUIS-Apps finden Sie unter [Hinzufügen von Entitäten](luis-how-to-add-entities.md).
* Erfahren Sie mehr über LUIS-[Anwendungsgrenzwerte](./luis-limits.md). 
* Erlernen Sie anhand eines [Tutorials](tutorial-machine-learned-entity.md), wie Sie strukturierte Daten mithilfe der ML-Entität aus einer Äußerung extrahieren.
