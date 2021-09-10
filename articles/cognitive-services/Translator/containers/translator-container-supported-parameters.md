---
title: 'Container: Übersetzermethode „Translate“'
titleSuffix: Azure Cognitive Services
description: Hier finden Sie Informationen zu den Parametern, Headern und Textnachrichten für die Containermethode „Translate“ von Azure Cognitive Services Translator zur Übersetzung von Text.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/12/2021
ms.author: lajanuar
ms.openlocfilehash: b2c13a98c6b689594628a258faf18f8f9c0d8c69
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/23/2021
ms.locfileid: "112539554"
---
# <a name="container-translator-translate-method-preview"></a>Container: Übersetzermethode „Translate“ (Vorschau)

Übersetzen Sie Text.

## <a name="request-url"></a>Anfrage-URL

Sendet eine `POST`-Anforderung an:

```HTTP
http://localhost:{port}/translate?api-version=3.0
```

Beispiel: http://<span></span>localhost:5000/translate?api-version=3.0

## <a name="request-parameters"></a>Anforderungsparameter

Die folgenden Anforderungsparameter werden in der Abfragezeichenfolge übergeben:

### <a name="required-parameters"></a>Erforderliche Parameter

| Query parameter (Abfrageparameter) | BESCHREIBUNG |
| --- | --- |
| api-version | _Erforderlicher Parameter_.  <br>Die vom Client angeforderte Version der API. Der Wert muss `3.0` sein. |
| from | _Erforderlicher Parameter_.  <br>Gibt die Sprache des Eingabetexts an. Finden Sie heraus, aus welchen Sprachen Sie übersetzen können, indem Sie die [unterstützten Sprachen](../reference/v3-0-languages.md) mithilfe des`translation`-Bereichs.|
| zu  | _Erforderlicher Parameter_.  <br>Gibt die Sprache des Ausgabetexts an. Sie müssen eine der zum `translation`-Bereich hinzugefügten [unterstützten Sprachen](../reference/v3-0-languages.md) als Zielsprache auswählen. Verwenden Sie z.B. `to=de` für die Übersetzung ins Deutsche.  <br>Durch Wiederholen des Parameters in der Abfragezeichenfolge ist es möglich, in mehrere Sprachen gleichzeitig zu übersetzen. Verwenden Sie z.B. `to=de&to=it` für die Übersetzung ins Deutsche und Italienische. |

### <a name="optional-parameters"></a>Optionale Parameter

| Query parameter (Abfrageparameter) | BESCHREIBUNG |
| --- | --- |
| textType | _Optionaler Parameter_.  <br>Definiert, ob es sich bei dem zu übersetzenden Text um Nur-Text oder um HTML-Text handelt. Jede HTML muss ein wohlgeformtes vollständiges Element sein. Mögliche Werte sind: `plain` (Standard) oder `html`. |
| includeSentenceLength | _Optionaler Parameter_.  <br>Gibt an, ob Satzgrenzen für den eingegebenen und den übersetzten Text verwendet werden. Mögliche Werte sind: `true` oder `false` (Standard). |

Anforderungsheader enthalten Folgendes:

| Header | BESCHREIBUNG |
| --- | --- |
| Authentifizierungsheader | _Erforderlicher Anforderungsheader_.  <br>Weitere Informationen finden Sie in den [verfügbaren Optionen für die Authentifizierung](../reference/v3-0-reference.md#authentication). |
| Content-Type | _Erforderlicher Anforderungsheader_.  <br>Gibt den Inhaltstyp der Nutzlast an.  <br>Der zulässige Wert ist `application/json; charset=UTF-8`. |
| Content-Length | _Erforderlicher Anforderungsheader_.  <br>Die Länge des Anforderungstexts. |
| X-ClientTraceId | _Optional:_  <br>Eine vom Client erstellte GUID zur eindeutigen Identifizierung der Anforderung. Sie können diesen Header nur weglassen, wenn Sie die Ablaufverfolgungs-ID in die Abfragezeichenfolge über einen Abfrageparameter namens `ClientTraceId` einschließen. |

## <a name="request-body"></a>Anforderungstext

Der Anforderungstext ist ein JSON-Array. Jedes Arrayelement ist ein JSON-Objekt mit einer Zeichenfolgeneigenschaft namens `Text`, die die zu suchende Zeichenfolge repräsentiert.

```json
[
    {"Text":"I would really like to drive your car around the block a few times."}
]
```

Es gelten die folgenden Einschränkungen:

* Das Array kann höchstens über 100 Elemente verfügen.
* Der gesamte Anforderungstext darf nicht mehr als 10.000 Zeichen enthalten (einschließlich Leerzeichen).

## <a name="response-body"></a>Antworttext

Eine erfolgreiche Antwort ist ein JSON-Array mit einem Ergebnis für jede Zeichenfolge im Eingabearray. Ein Ergebnisobjekt enthält die folgenden Eigenschaften:

* `translations`: Ein Array von Übersetzungsergebnissen. Die Größe des Arrays entspricht der Anzahl der durch den `to`-Abfrageparameter angegebenen Zielsprachen. Jedes Element im Array enthält:

* `to`: Eine Zeichenfolge, die den Sprachcode der Zielsprache darstellt.

* `text`: Eine Zeichenfolge, die den übersetzten Text enthält.

* `sentLen`: Ein Objekt, das Satzgrenzen in den Eingabe- und Ausgabetexten zurückgibt.

* `srcSentLen`: Ein Integer-Array, das die Länge der Sätze im Eingabetext darstellt. Die Länge des Arrays stellt die Anzahl von Sätzen dar, und die Werte stehen jeweils für die Länge der einzelnen Sätze.

* `transSentLen`:  Ein Integer-Array, das die Länge der Sätze im übersetzten Text darstellt. Die Länge des Arrays stellt die Anzahl von Sätzen dar, und die Werte stehen jeweils für die Länge der einzelnen Sätze.

    Satzgrenzen sind nur enthalten, wenn der Anforderungsparameter `includeSentenceLength``true` ist.

  * `sourceText`: Ein Objekt mit einer einzelnen Zeichenfolgeneigenschaft namens `text`, das den Eingabetext im Standardskript der Quellsprache bereitstellt. Die Eigenschaft `sourceText` ist nur vorhanden, wenn die Eingabe in einem Skript ausgedrückt wird, das nicht das übliche Skript für die Sprache ist. Wenn die Eingabe z.B. ein arabischer Text ist, der im lateinischen Skript verfasst wurde, würde `sourceText.text` diesen arabischen Text in das arabische Skript konvertieren.

Beispiele für JSON-Antworten finden Sie im Abschnitt [Beispiele](#examples).

## <a name="response-headers"></a>Antwortheader

| Header | BESCHREIBUNG |
| --- | --- |
| X-RequestId | Der Wert, der vom Dienst für die Identifizierung der Anforderung generiert wird. Er wird zu Problembehandlungszwecken verwendet. |
| X-MT-System | Gibt den Systemtyp an, der für jede zur Übersetzung angeforderte Zielsprache für die Übersetzung verwendet wurde. Bei dem Wert handelt es sich um eine durch Trennzeichen getrennte Liste von Zeichenfolgen. Jede Zeichenfolge gibt einen Typ an:  </br></br>&FilledVerySmallSquare; Custom: Die Anforderung enthält ein benutzerdefiniertes System, und mindestens ein benutzerdefiniertes System wurde während der Übersetzung verwendet.</br>&FilledVerySmallSquare; Team: Alle anderen Anforderungen. |

## <a name="response-status-codes"></a>Antwortstatuscodes

Wenn ein Fehler auftritt, gibt die Anforderung auch eine JSON-Fehlerantwort zurück. Der Fehlercode ist eine 6-stellige Zahl, die aus dem 3-stelligen HTTP-Statuscode gefolgt von einer 3-stelligen Zahl zur Kategorisierung des Fehlers besteht. Häufige Fehlercodes finden Sie in der [Referenz zu Version 3 von Translator](../reference/v3-0-reference.md#errors).

## <a name="examples"></a>Beispiele

### <a name="translate-a-single-input"></a>Übersetzen einer einzelnen Eingabe

Dieses Beispiel zeigt, wie ein einzelner Satz aus dem Englischen ins Chinesische (vereinfacht) übersetzt wird.

```curl
curl -X POST "http://localhost:{port}/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Der Antworttext lautet:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    }
]
```

Das `translations` Array enthält ein Element, das die Übersetzung des einzelnen Textausschnitts in der Eingabe bereitstellt.

### <a name="translate-multiple-pieces-of-text"></a>Übersetzen mehrerer Textausschnitte

Um mehrere Strings gleichzeitig zu übersetzen, muss lediglich ein Array von Zeichenfolgen im Anforderungstext angegeben werden.

```curl
curl -X POST "http://localhost:{port}/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}, {'Text':'I am fine, thank you.'}]"
```

Die Antwort enthält die Übersetzung aller Textteile in exakt derselben Reihenfolge wie in der Anforderung.
Der Antworttext lautet:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    },
    {
        "translations":[
            {"text":"我很好，谢谢你。","to":"zh-Hans"}
        ]
    }
]
```

### <a name="translate-to-multiple-languages"></a>Übersetzen in mehrere Sprachen

Dieses Beispiel zeigt, wie in einer Anforderung ein- und dieselbe Eingabe in mehrere Sprachen übersetzt wird.

```curl
curl -X POST "http://localhost:{port}/translate?api-version=3.0&from=en&to=zh-Hans&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Der Antworttext lautet:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"},
            {"text":"Hallo, was ist dein Name?","to":"de"}
        ]
    }
]
```

### <a name="translate-content-with-markup-and-decide-whats-translated"></a>Übersetzen von Inhalten mit Markups und Auswählen des zu übersetzenden Texts

Häufig werden Inhalte übersetzt, die Markups enthalten, wie z.B. Inhalte von einer HTML-Seite oder aus einem XML-Dokument. Beziehen Sie `textType=html`-Abfrageparameter beim Übersetzen von Inhalten mit Tags ein. Manchmal ist es auch sinnvoll, bestimmten Inhalt von der Übersetzung auszuschließen. Sie können das Attribut `class=notranslate` verwenden, um Inhalt anzugeben, der in der Originalsprache übernommen werden soll. Im folgenden Beispiel wird der Inhalt im ersten `div`-Element nicht übersetzt, während der Inhalt im zweiten `div`-Element übersetzt wird.

```
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

Zur Veranschaulichung finden Sie Folgenden ein Beispiel einer Anforderung.

```curl
curl -X POST "http://localhost:{port}/translate?api-version=3.0&from=en&to=zh-Hans&textType=html" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'<div class=\"notranslate\">This will not be translated.</div><div>This will be translated.</div>'}]"
```

Die Antwort lautet:

```
[
    {
        "translations":[
            {"text":"<div class=\"notranslate\">This will not be translated.</div><div>这将被翻译。</div>","to":"zh-Hans"}
        ]
    }
]
```

### <a name="translate-with-dynamic-dictionary"></a>Übersetzen mit dynamischem Wörterbuch

Wenn Ihnen die Übersetzung eines Worts oder eines Ausdrucks bereits bekannt ist, können Sie diese als Markup in der Anforderung angeben. Das dynamische Wörterbuch ist nur für Eigennamen wie Personen- und Produktnamen sicher.

Das anzugebende Markup verwendet die folgende Syntax.

```
<mstrans:dictionary translation="translation of phrase">phrase</mstrans:dictionary>
```

Sehen Sie sich hierzu den englischen Satz „The word wordomatic is a dictionary entry.“ an. Um das Wort _wordomatic_ in der Übersetzung beizubehalten, senden Sie die folgende Anforderung:

```
curl -X POST "http://localhost:{port}/translate?api-version=3.0&from=en&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.'}]"
```

Es wird folgendes Ergebnis ausgegeben:

```
[
    {
        "translations":[
            {"text":"Das Wort \"wordomatic\" ist ein Wörterbucheintrag.","to":"de"}
        ]
    }
]
```

Diese Funktion lässt sich gleichermaßen mit `textType=text` und ohne `textType=html` ausführen. Sie sollte in Maßen eingesetzt werden. Für die Anpassung einer Übersetzung ist Custom Translator deutlich besser geeignet. Custom Translator nutzt den Kontext und statistische Wahrscheinlichkeiten in vollem Umfang. Sie erhalten viel bessere Ergebnisse, wenn Sie über Trainingsdaten verfügen oder sich die Erstellung von Trainingsdaten leisten können, die den Kontext des Worts oder des Ausdrucks zeigen. [Hier finden Sie weitere Informationen zu Custom Translator](../customization.md).

## <a name="request-limits"></a>Anforderungsgrenzwerte

Jede Übersetzungsanforderung ist auf 10.000 Zeichen in allen von Ihnen verwendeten Zielsprachen beschränkt. Beispiel: Das Senden einer Übersetzungsanforderung von 3.000 Zeichen für die Übersetzung in drei verschiedene Sprachen ergibt eine Anforderungsgröße von 3.000 × 3 = 9.000 Zeichen. Dies liegt unterhalb des Anforderungsgrenzwerts. Die Abrechnung erfolgt nach der Anzahl der Zeichen, nicht nach der Anzahl der Anforderungen. Es wird empfohlen, kürzere Anforderungen zu senden.

In der folgenden Tabelle sind Arrayelement- und Zeichengrenzwerte für den Übersetzervorgang **translation** aufgeführt.

| Vorgang | Maximale Größe des Arrayelements | Maximale Anzahl von Arrayelementen | Maximale Anforderungsgröße (Zeichen) |
|:----|:----|:----|:----|
| translate | 10.000 | 100 | 10.000 |