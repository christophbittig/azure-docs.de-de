---
title: Translator-Methode „Transliterate“
titleSuffix: Azure Cognitive Services
description: Konvertieren Sie Text in einer Sprache aus einem Skript in ein anderes Skript mithilfe der Translator-Methode „Transliterate“.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: lajanuar
ms.openlocfilehash: 11574542116fe90629f84fc572f404a1b42b078b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355076"
---
# <a name="translator-30-transliterate"></a>Translator 3.0: Transliterate

Konvertiert Text in einer Sprache aus einem Skript in ein anderes Skript.

## <a name="request-url"></a>Anfrage-URL

Sendet eine `POST`-Anforderung an:

```HTTP
https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0
```

## <a name="request-parameters"></a>Anforderungsparameter

Die folgenden Anforderungsparameter werden in der Abfragezeichenfolge übergeben:

| Query parameter (Abfrageparameter) | BESCHREIBUNG |
| --- | --- |
| api-version | *Erforderlicher Parameter*.<br/>Die vom Client angeforderte Version der API. Der Wert muss `3.0` sein. |
| language | *Erforderlicher Parameter*.<br/>Gibt die Sprache des Texts an, der von einem Skript in ein anderes konvertiert werden soll. Mögliche Sprachen sind im `transliteration`-Bereich aufgeführt, der durch Abfrage des Diensts nach den [unterstützten Sprachen](./v3-0-languages.md) abgerufen wird. |
| fromScript | *Erforderlicher Parameter*.<br/>Gibt das vom Eingabetext verwendete Skript an. Suchen Sie mithilfe des `transliteration`-Bereichs nach [unterstützten Sprachen](./v3-0-languages.md), um Eingabeskripts zu finden, die für die ausgewählte Sprache verfügbar sind. |
| toScript | *Erforderlicher Parameter*.<br/>Gibt das Ausgabeskript an. Suchen Sie mithilfe des `transliteration`-Bereichs nach [unterstützten Sprachen](./v3-0-languages.md), um Ausgabeskripts zu finden, die für die ausgewählte Kombination aus Sprache und Eingabeskript verfügbar sind. |

Anforderungsheader enthalten Folgendes:

| Header | BESCHREIBUNG |
| --- | --- |
| Authentifizierungsheader | <em>Erforderlicher Anforderungsheader</em>.<br/>Weitere Informationen finden Sie in den [verfügbaren Optionen für die Authentifizierung](./v3-0-reference.md#authentication). |
| Content-Type | *Erforderlicher Anforderungsheader*.<br/>Gibt den Inhaltstyp der Nutzlast an. Mögliche Werte sind: `application/json` |
| Content-Length | *Erforderlicher Anforderungsheader*.<br/>Die Länge des Anforderungstexts. |
| X-ClientTraceId | *Optional:*<br/>Eine vom Client erstellte GUID zur eindeutigen Identifizierung der Anforderung. Beachten Sie, dass Sie diesen Header weglassen können, wenn Sie die Ablaufverfolgungs-ID mithilfe eines Abfrageparameters namens `ClientTraceId` in die Abfragezeichenfolge einschließen. |

## <a name="request-body"></a>Anforderungstext

Der Anforderungstext ist ein JSON-Array. Jedes Arrayelement ist ein JSON-Objekt mit einer Zeichenfolgeneigenschaft namens `Text`, die die zu suchende Zeichenfolge repräsentiert.

```json
[
    {"Text":"こんにちは"},
    {"Text":"さようなら"}
]
```

Es gelten die folgenden Einschränkungen:

* Das Array darf höchstens 10 Elemente enthalten.
* Der Textwert eines Arrayelements darf 1.000 Zeichen (einschließlich Leerzeichen) nicht überschreiten.
* Der gesamte Text, der in der Anforderung enthalten ist, darf 5.000 Zeichen (einschließlich Leerzeichen) nicht überschreiten.

## <a name="response-body"></a>Antworttext

Eine erfolgreiche Antwort ist ein JSON-Array mit einem Ergebnis für jedes Element im Eingabearray. Ein Ergebnisobjekt enthält die folgenden Eigenschaften:

  * `text`: Eine Zeichenfolge, die aus der Konvertierung der Eingabezeichenfolge in das Ausgabeskript resultiert.
  
  * `script`: Eine Zeichenfolge, die das in der Ausgabe verwendete Skript angibt.

Eine JSON-Beispielantwort lautet wie folgt:

```json
[
    {"text":"konnnichiha","script":"Latn"},
    {"text":"sayounara","script":"Latn"}
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

Sollte ein Fehler auftreten, gibt die Anforderung auch eine JSON-Fehlerantwort zurück. Der Fehlercode ist eine 6-stellige Zahl, die aus dem 3-stelligen HTTP-Statuscode gefolgt von einer 3-stelligen Zahl zur Kategorisierung des Fehlers besteht. Häufige Fehlercodes finden Sie in der [Referenz zu Version 3 von Translator](./v3-0-reference.md#errors). 

## <a name="examples"></a>Beispiele

Das folgende Beispiel zeigt, wie Sie zwei japanische Zeichenfolgen in lateinische Buchstaben konvertieren.

Die JSON-Nutzlast für die Anforderung in diesem Beispiel:

```json
[{"text":"こんにちは","script":"jpan"},{"text":"さようなら","script":"jpan"}]
```

Wenn Sie cURL in einem Befehlszeilenfenster verwenden, das keine Unicode-Zeichen unterstützt, verwenden Sie die folgende JSON-Nutzlast, und speichern Sie sie in eine Datei namens `request.txt`. Stellen Sie sicher, dass Sie diese Datei mit `UTF-8`-Codierung speichern.

```
curl -X POST "https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0&language=ja&fromScript=Jpan&toScript=Latn" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d @request.txt
```
