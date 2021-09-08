---
title: Translator-Methode „Detect“
titleSuffix: Azure Cognitive Services
description: Ermitteln Sie die Sprache eines Texts mit der Methode „Detect“ von Azure Cognitive Services Translator.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: lajanuar
ms.openlocfilehash: 36691229ebe59a6b3b5dd59776b6af41e326a3c2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122340217"
---
# <a name="translator-30-detect"></a>Translator 3.0: Detect

Identifiziert die Sprache eines Textabschnitts.

## <a name="request-url"></a>Anfrage-URL

Sendet eine `POST`-Anforderung an:

```HTTP
https://api.cognitive.microsofttranslator.com/detect?api-version=3.0
```

## <a name="request-parameters"></a>Anforderungsparameter

Die folgenden Anforderungsparameter werden in der Abfragezeichenfolge übergeben:

| Query parameter (Abfrageparameter) | BESCHREIBUNG |
| --- | --- |
| api-version | *Erforderlicher Parameter*.<br/>Die vom Client angeforderte Version der API. Der Wert muss `3.0` sein. |

Anforderungsheader enthalten Folgendes:

| Header | BESCHREIBUNG |
| --- | --- |
| Authentifizierungsheader | <em>Erforderlicher Anforderungsheader</em>.<br/>Weitere Informationen finden Sie in den [verfügbaren Optionen für die Authentifizierung](./v3-0-reference.md#authentication)</a>. |
| Content-Type | *Erforderlicher Anforderungsheader*.<br/>Gibt den Inhaltstyp der Nutzlast an. Mögliche Werte: `application/json`. |
| Content-Length | *Erforderlicher Anforderungsheader*.<br/>Die Länge des Anforderungstexts. |
| X-ClientTraceId | *Optional:*<br/>Eine vom Client erstellte GUID zur eindeutigen Identifizierung der Anforderung. Beachten Sie, dass Sie diesen Header weglassen können, wenn Sie die Ablaufverfolgungs-ID mithilfe eines Abfrageparameters namens `ClientTraceId` in die Abfragezeichenfolge einschließen. |

## <a name="request-body"></a>Anforderungstext

Der Anforderungstext ist ein JSON-Array. Jedes Arrayelement ist ein JSON-Objekt mit einer Zeichenfolgeneigenschaft namens `Text`. Die Spracherkennung wird auf den Wert der `Text`-Eigenschaft angewendet. Ein längerer Eingabetext erhöht die Zuverlässigkeit der automatischen Spracherkennung. Ein Beispiel-Anforderungstext sieht folgendermaßen aus:

```json
[
    { "Text": "Ich würde wirklich gerne Ihr Auto ein paar Mal um den Block fahren." }
]
```

Es gelten die folgenden Einschränkungen:

* Das Array kann höchstens über 100 Elemente verfügen.
* Der gesamte Text, der in einer Anforderung enthalten ist, kann 50.000 Zeichen (einschließlich Leerzeichen) nicht überschreiten.

## <a name="response-body"></a>Antworttext

Eine erfolgreiche Antwort ist ein JSON-Array mit einem Ergebnis für jede Zeichenfolge im Eingabearray. Ein Ergebnisobjekt enthält die folgenden Eigenschaften:

  * `language`: Code der erkannten Sprache.

  * `score`: Ein float-Wert, der die Zuverlässigkeit des Ergebnisses angibt. Die Bewertung bewegt sich zwischen 0 (null) und 1, und eine niedrige Bewertung gibt an, dass die Zuverlässigkeit zweifelhaft ist.

  * `isTranslationSupported`: Ein boolescher Wert, der TRUE lautet, wenn die erkannte Sprache eine der für die Textübersetzung unterstützten Sprachen ist.

  * `isTransliterationSupported`: Ein boolescher Wert, der TRUE lautet, wenn die erkannte Sprache eine der für die Transliteration unterstützten Sprachen ist.
  
  * `alternatives`: Ein Array anderer möglicher Sprachen. Jedes Element des Arrays ist ein anderes Objekt mit den gleichen Eigenschaften wie oben: `language`, `score`, `isTranslationSupported` und `isTransliterationSupported`.

Eine JSON-Beispielantwort lautet wie folgt:

```json
[

    {

        "language": "de",

        "score": 1.0,

        "isTranslationSupported": true,

        "isTransliterationSupported": false

    }

]
```

## <a name="response-headers"></a>Antwortheader

| Header | BESCHREIBUNG |
| --- | --- |
| X-RequestId | Der Wert, der vom Dienst für die Identifizierung der Anforderung generiert wird. Er wird zu Problembehandlungszwecken verwendet. |

## <a name="response-status-codes"></a>Antwortstatuscodes

Im Folgenden finden Sie die möglichen HTTP-Statuscodes, die eine Anforderung zurückgeben kann. 

| Statuscode | BESCHREIBUNG |
| --- | --- |
| 200 | Erfolg. |
| 400 | Einer der Abfrageparameter fehlt oder ist ungültig. Korrigieren Sie die Anforderungsparameter, bevor Sie es erneut versuchen. |
| 401 | Die Anforderung konnte nicht authentifiziert werden. Überprüfen Sie, ob die Anmeldeinformationen angegeben und gültig sind. |
| 403 | Die Anforderung ist nicht autorisiert. Weitere Informationen finden Sie in der Fehlermeldung. Diese weist oft darauf hin, dass alle kostenlosen Übersetzungen, die mit einer Testversion bereitgestellt wurden, aufgebraucht sind. |
| 429 | Der Server hat die Anforderung abgelehnt, da der Client die Anforderungsgrenzwerte überschritten hat. |
| 500 | Ein unerwarteter Fehler ist aufgetreten. Wenn der Fehler weiterhin besteht, melden Sie ihn, und gebe Sie Folgendes an: Datum und Zeitpunkt des Fehlers, Anforderungsbezeichner aus dem Anforderungsheader `X-RequestId` und Clientbezeichner aus dem Anforderungsheader `X-ClientTraceId`. |
| 503 | Der Server ist vorübergehend nicht verfügbar. Wiederholen Sie die Anforderung. Wenn der Fehler weiterhin besteht, melden Sie ihn, und gebe Sie Folgendes an: Datum und Zeitpunkt des Fehlers, Anforderungsbezeichner aus dem Anforderungsheader `X-RequestId` und Clientbezeichner aus dem Anforderungsheader `X-ClientTraceId`. |

Wenn ein Fehler auftritt, gibt die Anforderung auch eine JSON-Fehlerantwort zurück. Der Fehlercode ist eine 6-stellige Zahl, die aus dem 3-stelligen HTTP-Statuscode gefolgt von einer 3-stelligen Zahl zur Kategorisierung des Fehlers besteht. Häufige Fehlercodes finden Sie in der [Referenz zu Version 3 von Translator](./v3-0-reference.md#errors). 

## <a name="examples"></a>Beispiele

Im folgenden Beispiel wird dargestellt, wie Sprachen abgerufen werden, die für die Textübersetzung unterstützt werden.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/detect?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'What language is this text written in?'}]"
```
