---
title: Ausführen von Stimmungsanalysen und Opinion Mining
titleSuffix: Azure Cognitive Services
description: In diesem Artikel erfahren Sie, wie Sie Stimmungen erkennen und Meinungen in Text suchen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: sample
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-sentiment-opinion-mining, ignite-fall-2021
ms.openlocfilehash: 32e67ba4c8462b48158a72273e297924867b73b2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131434273"
---
# <a name="how-to-use-sentiment-analysis-and-opinion-mining"></a>Anleitung: Verwenden von Stimmungsanalyse und Opinion Mining 

Stimmungsanalyse und Opinion Mining sind zwei Möglichkeiten, um positive und negative Stimmungen zu erkennen. Mithilfe der Stimmungsanalyse können Sie Stimmungsbezeichnungen (z. B. „negativ“, „neutral“ und „positiv“) und Konfidenzbewertungen auf Satz- und Dokumentebene ermitteln. Opinion Mining bietet detaillierte Informationen zu den Meinungen in Bezug auf Wörter (z. B. den Attributen von Produkten oder Dienstleistungen) im Text.

> [!TIP]
> Informationen zu den ersten Schritte zur Verwendung dieses Features finden Sie im [Schnellstartartikel](../quickstart.md). Sie können auch Beispielanforderungen in [Language Studio](../../language-studio.md) erstellen, ohne Code schreiben zu müssen.


## <a name="sentiment-analysis"></a>Standpunktanalyse

Die Stimmungsanalyse wendet Stimmungsbezeichnungen auf Texte an, die auf Satz- und Dokumentebene jeweils mit einer Konfidenzbewertung zurückgegeben werden. 

Die Bezeichnungen sind *positiv*, *negativ* und *neutral*. Auf Dokumentebene kann auch die Stimmungsbezeichnung *gemischt* zurückgegeben werden. Die Stimmung des Dokuments wird unten bestimmt:

| Stimmung von Sätzen                                                                            | Zurückgegebene Dokumentbezeichnung |
|-----------------------------------------------------------------------------------------------|-------------------------|
| Mindestens ein `positive`-Satz ist im Dokument enthalten. Die restlichen Sätze sind `neutral`. | `positive`              |
| Mindestens ein `negative`-Satz ist im Dokument enthalten. Die restlichen Sätze sind `neutral`. | `negative`              |
| Mindestens ein `negative`- und mindestens ein `positive`-Satz ist im Dokument enthalten.    | `mixed`                 |
| Alle Sätze im Dokument sind `neutral`.                                                  | `neutral`               |

Zuverlässigkeitsbewertungen liegen zwischen 1 und 0. Werte, die näher an 1 liegen, weisen auf eine höhere Zuverlässigkeit der Bezeichnungsklassifizierung hin, während niedrigere Bewertungen eine geringere Zuverlässigkeit bedeuten. Für jedes Dokument oder jeden Satz werden die vorhergesagten Bewertungen, die den Bezeichnungen zugeordnet sind (positiv, negativ und neutral), bis 1 hinzugefügt. Weitere Informationen finden Sie im [Transparenzhinweis zu verantwortungsbewusster KI](/legal/cognitive-services/text-analytics/transparency-note?context=/azure/cognitive-services/text-analytics/context/context). 

## <a name="opinion-mining"></a>Opinion Mining

Opinion Mining ist ein Feature der Stimmungsanalyse. Dieses Feature wird in der Verarbeitung natürlicher Sprache (Natural Language Processing, NLP) auch als aspektbasierte Standpunktanalyse bezeichnet und bietet feiner abgestufte Informationen zu den Meinungen in Bezug auf Attribute von Produkten oder Dienstleistungen im Texten. Die API zeigt Meinungen als Ziel (Substantiv oder Verb) und eine Bewertung (Adjektiv) an.

Wenn ein Kunde für ein Hotel beispielsweise Feedback wie „Das Zimmer war toll, aber das Personal war unfreundlich.“ hinterlässt, werden vom Opinion Mining Ziele (Aspekte) im Text sowie die zugehörigen Bewertungen (Meinungen) und Stimmungen ermittelt. Von der Standpunktanalyse wird unter Umständen nur eine negative Stimmung gemeldet.

:::image type="content" source="../media/opinion-mining.png" alt-text="Diagramm: Opinion Mining-Beispiel" lightbox="../media/opinion-mining.png":::

Wenn Sie die REST-API verwenden, um Opinion Mining in Ihre Ergebnisse einzubeziehen, müssen Sie das Flag `opinionMining=true` in eine Stimmungsanalyseanforderung einschließen. Die Opinion Mining-Ergebnisse werden in die Antwort der Stimmungsanalyse eingeschlossen. Opinion Mining ist eine Standpunktanalyse-Erweiterung und in Ihrem aktuellen [Tarif](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) enthalten.

## <a name="determine-how-to-process-the-data-optional"></a>Festlegen der Art der Datenverarbeitung (optional)

### <a name="specify-the-sentiment-analysis-model"></a>Angeben des Stimmungsanalysemodells

Standardmäßig verwendet die Stimmungsanalyse das neueste verfügbare KI-Modell für Ihren Text. Sie können Ihre API-Anforderungen auch für die Verwendung einer bestimmten Modellversion konfigurieren. Das angegebene Modell wird verwendet, um Stimmungsanalysevorgänge durchzuführen.

| Unterstützte Versionen | Neueste GA-Version | Neueste Vorschauversion |
|--|--|--|
| `2019-10-01`, `2020-04-01`, `2021-10-01-preview` | `2020-04-01`   | `2021-10-01-preview`   |

### <a name="using-a-preview-model-version"></a>Verwendung einer Vorschaumodellversion

Um eine Vorschaumodellversion in Ihren API-Aufrufen zu verwenden, müssen Sie die Modellversion mithilfe des Modellversionsparameters angeben. Wenn Sie z. B. eine Anforderung mithilfe von Python senden:

```python
result = text_analytics_client.analyze_sentiment(documents, show_opinion_mining=True, model_version="2021-10-01-preview")
```

oder wenn Sie die REST-API verwendet haben:

```rest
https://your-resource-name.cognitiveservices.azure.com/text/analytics/v3.1/sentiment?opinionMining=true&model-version=2021-10-01-preview
```

Weitere Informationen finden Sie in der Referenzdokumentation.
* [REST-API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/Sentiment)
* [.NET](https://docs.microsoft.com/dotnet/api/azure.ai.textanalytics.analyzesentimentaction?view=azure-dotnet#properties)
* [Python](https://docs.microsoft.com/python/api/azure-ai-textanalytics/azure.ai.textanalytics.textanalyticsclient?view=azure-python#analyze-sentiment-documents----kwargs-)
* [Java](https://docs.microsoft.com/java/api/com.azure.ai.textanalytics.models.analyzesentimentoptions.setmodelversion?view=azure-java-stable#com_azure_ai_textanalytics_models_AnalyzeSentimentOptions_setModelVersion_java_lang_String_)
* [JavaScript](https://docs.microsoft.com/javascript/api/@azure/ai-text-analytics/analyzesentimentoptions?view=azure-node-latest)

### <a name="input-languages"></a>Eingabesprachen

Wenn Sie Dokumente übermitteln, die von der Stimmungsanalyse verarbeitet werden sollen, können Sie angeben, in welcher [unterstützten Sprachen](../language-support.md) die Dokumente geschrieben sind. Wenn Sie keine Sprache angeben, verwendet die Stimmungsanalyse standardmäßig Englisch. Die API gibt möglicherweise Offsets in der Antwort zurück, um verschiedene [mehrsprachige und Emoji-Codierungen](../../concepts/multilingual-emoji-support.md) zu unterstützen. 

## <a name="submitting-data"></a>Übermitteln der Daten

Standpunktanalyse und Opinion Mining liefern bessere Ergebnisse, wenn die zu verarbeitenden Texte kleiner sind. Bei anderen Feature, z. B. der Schlüsselbegriffserkennung, verhält es sich genau umgekehrt: Sie funktioniert besser, wenn sie für große Textblöcke durchgeführt wird. 

Um eine API-Anforderung zu senden, benötigen Sie Endpunkt und Schlüssel für Ihre Sprachressource.

> [!NOTE]
> Sie finden Schlüssel und Endpunkt für Ihre Sprachressource im Azure-Portal. Sie befinden sich auf der Seite mit dem **Schlüssel und Endpunkt** der Ressource unter **Ressourcenverwaltung**. 

Die Analyse erfolgt, wenn die Anforderung eingeht. Informationen zur Größe und Anzahl von Anforderungen, die Sie pro Minute und Sekunde senden können, finden Sie in den nachstehend aufgeführten Datengrenzwerten.

Die synchrone Verwendung der Features für Stimmungsanalyse und Opinion Mining ist zustandslos. Auf Ihrem Konto werden keine Daten gespeichert, und die Ergebnisse werden sofort in der Antwort zurückgegeben.

Wenn Sie diese Features asynchron verwenden, sind die API-Ergebnisse ab der Erfassung der Anforderung wie in der Antwort angegeben 24 Stunden lang verfügbar. Nach diesem Zeitraum werden die Ergebnisse endgültig gelöscht und stehen nicht mehr zum Abruf zur Verfügung.

## <a name="getting-sentiment-analysis-and-opinion-mining-results"></a>Abrufen von Stimmungsanalyse- und Opinion Mining-Ergebnissen

Wenn Sie Ergebnisse von der API erhalten, wird die Reihenfolge der zurückgegebenen Schlüsselbegriffe intern durch das Modell bestimmt. Sie können die Ergebnisse an eine Anwendung streamen, oder die Ausgabe in einer Datei im lokalen System speichern.

Die Standpunktanalyse gibt eine Stimmungsbezeichnung und eine Zuverlässigkeitsbewertung für das gesamte Dokument und jeden Satz darin zurück. Werte, die näher an 1 liegen, weisen auf eine höhere Zuverlässigkeit der Bezeichnungsklassifizierung hin, während niedrigere Bewertungen eine geringere Zuverlässigkeit bedeuten. Ein Dokument kann mehrere Sätze enthalten, und die Zuverlässigkeitsbewertungen in jedem Dokument oder Satz ergeben addiert 1.

Beim Opinion Mining werden Ziele (Substantive oder Verben) im Text sowie die zugehörige Bewertung (Adjektiv) ermittelt. Der Satz *Im Restaurant gab es großartiges Essen, und die Bedienung war freundlich.* weist z. B. zwei Ziele auf: *Essen* und *Bedienung*. Jedes Ziel verfügt über eine Bewertung. Die Bewertung für *Essen* wäre beispielsweise *großartige* und die Bewertung für die *Bedienung* wäre *freundlich*.

Die API gibt Meinungen als Ziel (Substantiv oder Verb) und eine Bewertung (Adjektiv) zurück.


## <a name="data-limits"></a>Datengrenzwerte

> [!NOTE]
> * Wenn Sie größere Dokumente analysieren müssen, als der Grenzwert zulässt, können Sie den Text in kleinere Textabschnitte aufteilen, bevor Sie ihn an die API senden. 
> * Ein Dokument ist eine einzelne aus Textzeichen bestehende Zeichenfolge.  

| Begrenzung | Wert |
|------------------------|---------------|
| Maximale Größe eines einzelnen Dokuments (synchron) | 5\.120 Zeichen (gemessen von [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements)).  |
| Maximale Zeichenanzahl pro Anforderung (asynchron) | 125.000 Zeichen für alle übermittelten Dokumente, gemessen mithilfe von [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements).  |
| Maximale Größe der gesamte Anforderung | 1 MB. |
| Max. Anzahl von Dokumenten pro Anforderung | 10 |

Wenn bei einem Dokument das Zeichenlimit überschritten wird, verhält sich die API abhängig vom verwendeten Feature unterschiedlich:

* Asynchron:
  * Die API lehnt die gesamte Anforderung ab und gibt einen Fehler vom Typ `400 bad request` zurück, wenn ein enthaltenes Dokument die maximal zulässige Größe überschreitet.
* Synchron:  
  * Dokumente, die die maximal zulässige Größe überschreiten, werden von der API nicht verarbeitet, und es wird jeweils ein Fehler mit einem Hinweis auf ein ungültiges Dokument zurückgegeben. Wenn eine API-Anforderung mehrere Dokumente umfasst, fährt die API mit deren Verarbeitung fort, sofern sie sich innerhalb des Zeichenlimits befinden.

Wenn die maximale Anzahl von Dokumenten überschritten wird, die Sie in einer einzelnen Anforderung senden können, wird ein HTTP 400-Fehlercode generiert.

### <a name="rate-limits"></a>Ratenbegrenzungen

Die Ratenbegrenzung variiert je nach [Tarif](https://aka.ms/unifiedLanguagePricing).

| Tarif          | Anforderungen pro Sekunde | Anforderungen pro Minute |
|---------------|---------------------|---------------------|
| S/Mehrere Dienste | 1000                | 1000                |
| S0/F0         | 100                 | 300                 |

## <a name="see-also"></a>Siehe auch

* [Übersicht über Stimmungsanalyse und Opinion Mining](../overview.md)
