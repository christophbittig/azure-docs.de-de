---
title: 'Bewährte Methoden: Fragen und Antworten'
description: Nutzen Sie diese bewährten Methoden, um Ihr Projekt zu verbessern und bessere Ergebnisse für die Endbenutzer*innen Ihrer Anwendung bzw. Ihres Chatbots zu liefern.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 552e3d014ffecf5f43977420c9aa8c6bb1b59080
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095656"
---
# <a name="question-answering-best-practices"></a>Bewährte Methoden: Fragen und Antworten

Nutzen Sie diese bewährten Methoden, um Ihre Wissensdatenbank zu optimieren und bessere Ergebnisse für die Endbenutzer Ihrer Clientanwendung bzw. Ihres Chatbots zu liefern.

## <a name="extraction"></a>Extraktion

Die Algorithmen zum Extrahieren von Frage-Antwort-Paaren aus Inhalten für das Feature „Fragen und Antworten“ werden kontinuierlich verbessert. Darüber hinaus wird die Liste der unterstützten Datei- und HTML-Formate stetig erweitert. Ganz allgemein sollten die Seiten mit häufig gestellten Fragen eigenständig bereitgestellt und nicht mit anderen Informationen kombiniert werden. Produkthandbücher sollten klare Überschriften und vorzugsweise eine Indexseite aufweisen.

## <a name="creating-good-questions-and-answers"></a>Formulieren guter Fragen und Antworten

### <a name="good-questions"></a>Gute Fragen

Die besten Fragen sind einfach. Überlegen Sie sich das Schlüsselwort oder den Ausdruck für jede Frage. Formulieren Sie dann eine einfache Frage zu diesem Schlüsselwort oder Ausdruck.

Fügen Sie so viele alternative Fragen hinzu, wie Sie benötigen, aber halten Sie die Änderungen einfach. Das Hinzufügen weiterer Wörter oder Ausdrücke, die nicht dem Hauptzweck der Frage dienen, hilft den Algorithmen für Fragen und Antworten nicht dabei, eine Übereinstimmung zu finden.

### <a name="add-relevant-alternative-questions"></a>Hinzufügen von relevanten alternativen Fragen

Ihr Benutzer kann entweder Fragen im Unterhaltungsstil eingeben (`How do I add a toner cartridge to my printer?`) oder eine Stichwortsuche (z. B. `toner cartridge`) verwenden. Das Projekt sollte über beide Arten von Fragen verfügen, damit jeweils die beste Antwort richtig zurückgegeben wird. Wenn Sie nicht sicher sind, welche Stichwörter Kund*innen eingeben, analysieren Sie Abfragen mithilfe von [Azure Monitor](../how-to/analytics.md)-Daten.

### <a name="good-answers"></a>Gute Antworten

Die besten Antworten sind einfache Antworten, solange diese nicht zu einfach gehalten sind. Verwenden Sie keine Antworten wie `yes` und `no`. Wenn Ihre Antwort auf andere Quellen verweisen oder mit Medien und Links umfassend gestaltet sein soll, sollten Sie Metadatentags verwenden, um zwischen Antworten zu unterscheiden. Übermitteln Sie die Abfrage dann mit Metadatentags in der `strictFilters`-Eigenschaft, um die richtige Antwortversion zu erhalten.

|Antwort|Folgeaufforderungen|
|--|--|
|Fahren Sie das Surface-Laptop mithilfe der Ein-Aus-Taste auf der Tastatur herunter.|* Tastenkombinationen für Standbymodus, Herunterfahren und Neustart.<br>* Vorgehensweise beim Hardboot eines Surface-Laptops<br>* Vorgehensweise zum Ändern des BIOS für einen Surface-Laptop<br>* Unterschiede zwischen Standbymodus, Herunterfahren und Neustart|
|Der Kundendienst steht über Telefon, Skype-und SMS-Nachrichten rund um die Uhr zur Verfügung.|* Kontaktinformationen für den Vertrieb.<br> * Büro- und Store-Standorte und Öffnungszeiten für einen persönlichen Besuch.<br> * Zubehör für einen Surface-Laptop.|

## <a name="chit-chat"></a>Geplauder

Fügen Sie Ihrem Bot Geplauder hinzu, um ihn mit geringem Aufwand gesprächiger und ansprechender zu gestalten. Beim Erstellen Ihres Projekts können Sie ganz einfach Smalltalk-Datenquellen für vordefinierte Persönlichkeiten hinzufügen und diese jederzeit ändern. Weitere Informationen zum [Hinzufügen von Geplauder zur Wissensdatenbank](../How-To/chit-chat.md).

Das Geplauder wird in [vielen Sprachen](../how-to/chit-chat.md#language-support)unterstützt.

### <a name="choosing-a-personality"></a>Auswählen einer Persönlichkeit

Geplauder wird für verschiedene vordefinierte Persönlichkeiten unterstützt:

|Persönlichkeit |Datasetdatei für Fragen und Antworten |
|---------|-----|
|Professionell |[qna_chitchat_professional.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_professional.tsv) |
|Freundlich |[qna_chitchat_friendly.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_friendly.tsv) |
|Witzig |[qna_chitchat_witty.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_witty.tsv) |
|Mitfühlend |[qna_chitchat_caring.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_caring.tsv) |
|Begeistert |[qna_chitchat_enthusiastic.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_enthusiastic.tsv) |

Die Antworten reichen von formell bis informell und irrelevant. Sie sollten die Persönlichkeit auswählen, die am besten zu dem Sprachstil passt, den Sie für Ihren Bot wünschen. Sie können die [Datasets](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets) anzeigen und ein Dataset auswählen, das als Basis für Ihren Bot dient, und dann die Antworten anpassen.

### <a name="edit-bot-specific-questions"></a>Bearbeiten von botspezifische Fragen

Es sind einige botspezifische Fragen vorhanden, die Teil des Geplauderdatasets sind und mit generischen Antworten aufgefüllt wurden. Ändern Sie diese Antworten so, dass sie am besten zu den Details Ihres Bots passen.

Es wird empfohlen, die folgenden Frage-Antwort-Paare für Smalltalk spezifischer zu gestalten:

* Wer sind Sie?
* Was können Sie tun?
* Wie alt sind Sie?
* Wer hat Sie erstellt?
* Hallo

### <a name="adding-custom-chit-chat-with-a-metadata-tag"></a>Hinzufügen von benutzerdefiniertem Geplauder mit einem Metadatentag

Stellen Sie beim Hinzufügen Ihrer eigenen Frage-Antwort-Paare für Smalltalk sicher, dass Sie auch Metadaten hinzufügen, damit die Antworten zurückgegeben werden. Das Paar aus Metadatenname und -wert lautet `editorial:chitchat`.

## <a name="searching-for-answers"></a>Suchen nach Antworten

Die REST-API für Fragen und Antworten verwendet sowohl Fragen als auch Antworten, um nach den besten Antworten für die Abfrage eines Benutzers oder einer Benutzerin zu suchen.

### <a name="searching-questions-only-when-answer-is-not-relevant"></a>Suchen nach Fragen nur dann, wenn eine Antwort nicht relevant ist

Verwenden Sie [`RankerType=QuestionOnly`](#choosing-ranker-type), wenn Sie nicht nach Antworten suchen möchten.

Ein Beispiel hierfür ist, wenn die Wissensdatenbank aus einem Katalog mit Akronymen als Fragen und deren vollständigen Form als Antwort besteht. Der Wert der Antwort ist beim Suchen nach der passenden Antwort nicht hilfreich.

## <a name="rankingscoring"></a>Rangfolge/Bewertung

Stellen Sie sicher, dass Sie die unterstützten Rangfolgefeatures optimal nutzen. Damit erhöhen Sie die Wahrscheinlichkeit, dass eine bestimmten Benutzerfrage angemessen beantwortet wird.

### <a name="choosing-a-threshold"></a>Auswählen eines Schwellenwerts

Die standardmäßig als Schwellenwert verwendete [Zuverlässigkeitsbewertung](confidence-score.md) ist 0. Sie können den [Schwellenwert für Ihr Projekt jedoch an Ihre Bedürfnisse anpassen](confidence-score.md#set-threshold). Da jedes Projekt anders ist, sollten Sie verschiedene Schwellenwerte testen und den Wert auswählen, der für Ihr Projekt am besten geeignet ist.

### <a name="choosing-ranker-type"></a>Auswählen des Typs der Rangfolgefunktion

Standardmäßig durchsucht das Feature „Fragen und Antworten“ sowohl Fragen als auch Antworten. Wenn Sie nur Fragen durchsuchen möchten, um eine Antwort zu generieren, verwenden Sie im POST-Text der REST-API-Anforderung `RankerType=QuestionOnly`.

### <a name="add-alternate-questions"></a>Hinzufügen alternativer Fragen

Alternative Fragen verbessern die Wahrscheinlichkeit einer Übereinstimmung mit einer Benutzerabfrage. Alternative Fragen sind besonders dann nützlich, wenn es mehrere Möglichkeiten gibt, die gleiche Frage zu stellen. Dies können z.B. Änderungen in der Satzstruktur und in der Wortwahl sein.

|Ursprüngliche Abfrage|Alternative Abfragen|Change|
|--|--|--|
|Sind Parkplätze verfügbar?|Haben Sie einen Parkplatz?|Satzstruktur|
 |Hi|Yo<br>Hallo!|Wortwahl oder Slang|

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>Verwenden von Metadatentags zum Filtern von Fragen und Antworten

Mit Metadaten kann einer Clientanwendung mitgeteilt werden, dass nicht alle Antworten verwendet werden sollen, sondern stattdessen die Ergebnisse einer Benutzerabfrage basierend auf Metadatentags eingegrenzt werden sollen. Die Antwort aus dem Projekt bzw. der Wissensdatenbank kann je nach Metadatentag variieren, selbst wenn die Abfrage identisch ist. So gibt es beispielsweise auf die Frage *„Wo ist der Parkplatz?“* eine andere Antwort, wenn ein anderer Standort für eine Filiale der Restaurantkette verwendet wird: Die Metadaten sind für *Standort: Seattle* anders als für *Standort: Redmond*.

### <a name="use-synonyms"></a>Verwenden von Synonymen

Auch wenn für die englische Sprache Synonyme teilweise unterstützt werden, verwenden Sie [Wortvarianten](../tutorials/adding-synonyms.md) ohne Berücksichtigung der Groß- und Kleinschreibung, um Synonyme für Stichwörter mit verschiedenen Formen hinzuzufügen.

|Ursprüngliches Wort|Synonyme|
|--|--|
|kaufen|erwerben<br>Electronic Banking<br>E-Banking|

---

### <a name="use-distinct-words-to-differentiate-questions"></a>Verwenden unterschiedlicher Wörter für die Unterscheidung von Fragen

Der Rangfolgenalgorithmus, der eine Benutzerabfrage einer Frage im Projekt bzw. in der Wissensdatenbank zuordnet, funktioniert am besten, wenn jede Frage sich auf ein anderes Anliegen bezieht. Die Wiederholung derselben Wortgruppe in unterschiedlichen Fragen reduziert die Wahrscheinlichkeit, dass die richtige Antwort für eine bestimmte Benutzerfrage mit diesen Wörtern ausgewählt wird.

Möglicherweise verfügen Sie z. B. über zwei separate Frage-Antwort-Paare mit den folgenden Fragen:

|Fragen|
|--|
|Wo ist der *Standort* des Parkplatzes?|
|Wo ist der *Standort* des Geldautomaten?|

Da diese beiden Fragen sehr ähnlich formuliert sind, könnte diese Ähnlichkeit sehr ähnliche Bewertungen für viele Benutzerabfragen verursachen, die nach dem Muster *„Wo ist der Standort des `<x>`“* formuliert sind. Versuchen Sie stattdessen, mit Abfragen wie *„Wo ist der Parkplatz?“* und *„Wo ist der Geldautomat?“* klar zu unterscheiden, indem Sie Wörter wie „Standort“ vermeiden, die in vielen Fragen in Ihrem Projekt vorkommen könnten.

## <a name="collaborate"></a>Zusammenarbeiten

Das Feature „Fragen und Antworten“ ermöglicht es Benutzer*innen, zusammen an einem Projekt bzw. einer Wissensdatenbank zu arbeiten. Benutzer*innen benötigen Zugriff auf die zugeordnete Azure-Ressourcengruppe, um auf das Projekt bzw. die Wissensdatenbanken zugreifen zu können. Einige Organisationen lagern die Bearbeitung und Verwaltung ihrer Knowledge Base aus, möchten aber eventuell trotzdem weiterhin den Zugriff auf ihre Azure-Ressourcen schützen. Dieses Modell mit bearbeitender und genehmigender Person wird umgesetzt, indem zwei identische Sprachressourcen mit identischen Projekten mit Fragen und Antworten in verschiedenen Abonnements eingerichtet werden und eine dieser Ressourcen für den Bearbeitungstestzyklus ausgewählt wird. Nach Abschluss der Tests werden die Projektinhalte in einem [Import- und Exportvorgang](../how-to/migrate-knowledge-base.md) exportiert und an die Sprachressource der genehmigenden Person übertragen, die schließlich das Projekt bereitstellt und den Endpunkt aktualisiert.

## <a name="active-learning"></a>Aktives Lernen

[Aktives Lernen](../tutorials/active-learning.md) leistet die beste Arbeit beim Vorschlagen alternativer Fragen, wenn ein breites Spektrum an Qualität und Quantität von benutzerbezogenen Abfragen zur Verfügung steht. Es ist wichtig, dass die Benutzerabfragen von Clientanwendungen ohne Zensur an der Feedbackschleife des aktiven Lernens teilnehmen können. Sobald Fragen im Language Studio-Portal vorgeschlagen werden, können Sie diese überprüfen und akzeptieren oder ablehnen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Bearbeiten einer Knowledge Base](../How-to/manage-knowledge-base.md)
