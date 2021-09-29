---
title: 'Korrigieren falsch geschriebener Wörter: LUIS'
titleSuffix: Azure Cognitive Services
description: Korrigieren Sie falsch geschriebene Wörter in Äußerungen, indem Sie LUIS-Endpunktabfragen die Bing-Rechtschreibprüfungs-API V7 hinzufügen.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 01/12/2021
ms.openlocfilehash: 904a29278ea3efdabe6ed72892d2e14ccbf2e0e3
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124740765"
---
# <a name="correct-misspelled-words-with-bing-resource"></a>Korrigieren falsch geschriebener Wörter mit einer Bing-Ressource

Von der V3-Vorhersage-API wird jetzt die [Bing-Rechtschreibprüfungs-API](/bing/search-apis/bing-spell-check/overview) unterstützt. Fügen Sie Ihrer Anwendung eine Rechtschreibprüfung hinzu, indem Sie den Schlüssel für Ihre Bing-Suche-Ressource in den Header Ihrer Anforderungen aufnehmen. Sie können eine vorhandene Bing-Ressource verwenden, wenn Sie bereits über eine verfügen, oder [eine neue erstellen](https://portal.azure.com/#create/Microsoft.BingSearch), um dieses Feature zu verwenden. 

Beispiel für eine Vorhersageausgabe für eine falsch geschriebene Abfrage:

```json
{
  "query": "bouk me a fliht to kayro",
  "prediction": {
    "alteredQuery": "book me a flight to cairo",
    "topIntent": "book a flight",
    "intents": {
      "book a flight": {
        "score": 0.9480589
      }
      "None": {
        "score": 0.0332136229
      }
    },
    "entities": {}
  }
}
```

Korrekturen an der Rechtschreibung werden vor der LUIS-Äußerungsvorhersage für den Benutzer vorgenommen. In der Antwort können Sie alle Änderungen an der ursprünglichen Äußerung, einschließlich Rechtschreibung, sehen.

## <a name="create-bing-search-resource"></a>Erstellen einer Bing-Suche-Ressource

Gehen Sie wie folgt vor, um im Azure-Portal eine Bing-Suche-Ressource zu erstellen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an.

2. Wählen Sie in der linken oberen Ecke **Ressource erstellen** aus.

3. Geben Sie in das Suchfeld `Bing Search V7` ein, und wählen Sie den Dienst aus.

4. Auf der rechten Seite wird ein Informationsbereich angezeigt, der Informationen sowie die rechtlichen Hinweise enthält. Wählen Sie **Erstellen** aus, um mit der Abonnementerstellung zu beginnen.

> [!div class="mx-imgBorder"]
> ![Ressource Bing-Rechtschreibprüfungs-API, Version 7](./media/luis-tutorial-bing-spellcheck/bing-search-resource-portal.png)

5. Geben Sie im nächsten Bereich Ihre Diensteinstellungen an. Warten Sie, bis die Diensterstellung abgeschlossen wurde.

6. Navigieren Sie nach der Erstellung der Ressource zum Blatt **Keys and Endpoint** (Schlüssel und Endpunkt) auf der linken Seite. 

7. Kopieren Sie einen der Schlüssel, die dem Header Ihrer Vorhersageanforderung hinzugefügt werden sollen. Sie benötigen nur einen der beiden Schlüssel.

<!--
## Using the key in LUIS test panel
There are two places in LUIS to use the key. The first is in the [test panel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key isn't saved into LUIS but instead is a session variable. You need to set the key every time you want the test panel to apply the Bing Spell Check API v7 service to the utterance. See [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in the test panel for setting the key.
-->


## <a name="adding-the-key-to-the-endpoint-url"></a>Hinzufügen des Schlüssels zur Endpunkt-URL
Für jede Abfrage, auf die Sie die Rechtschreibprüfung anwenden möchten, muss der Endpunktabfrage der Schlüssel für die Bing-Rechtschreibprüfungs-Ressource im Abfrageheaderparameter übergeben werden. Ihr Chatbot könnte beispielsweise LUIS aufrufen, oder Sie rufen die LUIS-Endpunkt-API direkt auf. Unabhängig davon, wie der Endpunkt aufgerufen wird, muss jeder Aufruf die erforderlichen Informationen im Anforderungsheader enthalten, damit die Rechtschreibprüfung ordnungsgemäß funktioniert. Sie müssen den Wert mit **mkt-bing-spell-check-key** auf den Schlüsselwert festlegen.

|Headerschlüssel|Headerwert|
|--|--|
|`mkt-bing-spell-check-key`|Auf dem Blatt **Keys and Endpoint** (Schlüssel und Endpunkte) der Ressource gefundene Schlüssel|

## <a name="send-misspelled-utterance-to-luis"></a>Übermitteln falsch geschriebener Äußerungen an LUIS
1. Fügen Sie in der Vorhersageabfrage, die Sie senden, eine falsch geschriebene Äußerung hinzu, z. B. „How far is the mountainn?“. Im Englischen ist `mountain` mit einem `n` die richtige Schreibweise.

2. LUIS antwortet mit einem JSON-Ergebnis für `How far is the mountain?`. Wenn die Bing-Rechtschreibprüfungs-API v7 einen Rechtschreibfehler erkennt, enthält das Feld `query` in der JSON-Antwort der LUIS-App die ursprüngliche Abfrage und das Feld `alteredQuery` die korrigierte, an LUIS gesendete Abfrage.

```json
{
  "query": "How far is the mountainn?",
  "alteredQuery": "How far is the mountain?",
  "topScoringIntent": {
    "intent": "Concierge",
    "score": 0.183866
  },
  "entities": []
}
```

## <a name="ignore-spelling-mistakes"></a>Ignorieren von Rechtschreibfehlern

Wenn Sie den Dienst Bing-Suche-API, Version 7, nicht nutzen möchten, müssen Sie die richtige und falsche Schreibung hinzufügen.

Zwei Lösungen sind:

* Kennzeichnen Sie Beispieläußerungen, welche die verschiedenen Schreibweisen enthalten, damit LUIS sowohl die richtige Rechtschreibung als auch Tippfehler lernen kann. Diese Option erfordert einen höheren Aufwand für die Bezeichnung als die Verwendung einer Rechtschreibprüfung.
* Erstellen Sie eine Phrasenliste mit allen Wortvariationen. Mit dieser Lösung müssen Sie die Wortvariationen in den Beispieläußerungen nicht kennzeichnen.


> [!div class="nextstepaction"]
> [Weitere Informationen zu Beispieläußerungen](./luis-how-to-add-entities.md)
