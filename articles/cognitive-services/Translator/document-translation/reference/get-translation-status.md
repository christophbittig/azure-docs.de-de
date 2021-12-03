---
title: Abrufen des Übersetzungsstatus
titleSuffix: Azure Cognitive Services
description: Die Methode Get-Übersetzungsstatus gibt den Status für eine Dokumentübersetzungsanforderung zurück.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/21/2021
ms.openlocfilehash: f892f9d5e681b7895f7e3d9dc053a9abf0ff00e1
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061980"
---
# <a name="get-translation-status"></a>Abrufen des Übersetzungsstatus

Die Methode Get-Übersetzungsstatus gibt den Status für eine Dokumentübersetzungsanforderung zurück. Der Status enthält den gesamten Anforderungsstatus und den Status für Dokumente, die als Teil dieser Anforderung übersetzt werden.

## <a name="request-url"></a>Anfrage-URL

Sendet eine `GET`-Anforderung an:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0/batches/{id}
```

Erfahren Sie, wie Sie Ihren [benutzerdefinierten Domänennamen](../get-started-with-document-translation.md#find-your-custom-domain-name)finden.

> [!IMPORTANT]
>
> * **Für alle API-Anforderungen an den Dienst für die Dokumentübersetzung muss ein Endpunkt einer benutzerdefinierten Domäne verwendet werden**.
> * Zum Senden von HTTP-Anforderungen für die Dokumentübersetzung verwenden Sie nicht den Endpunkt, der im Azure-Portal auf der Ressourcenseite _Schlüssel und Endpunkt_ angegeben ist, und auch nicht den globalen Übersetzungsendpunkt `api.cognitive.microsofttranslator.com`.


## <a name="request-parameters"></a>Anforderungsparameter

Die folgenden Anforderungsparameter werden in der Abfragezeichenfolge übergeben:

|Query parameter (Abfrageparameter)|Erforderlich|BESCHREIBUNG|
|--- |--- |--- |
|id|Richtig|Vorgangs-ID.|

## <a name="request-headers"></a>Anforderungsheader

Anforderungsheader:

|Header|BESCHREIBUNG|
|--- |--- |
|Ocp-Apim-Subscription-Key|Erforderlicher Anforderungsheader|

## <a name="response-status-codes"></a>Antwortstatuscodes

Im Folgenden finden Sie die möglichen HTTP-Statuscodes, die eine Anforderung zurückgeben kann.

|Statuscode|BESCHREIBUNG|
|--- |--- |
|200|OK. Erfolgreiche Anforderung und gibt den Status des Batchübersetzungsvorgangs zurück. HeadersRetry-After: integerETag: Zeichenfolge|
|401|Nicht autorisiert. Anmeldeinformationen prüfen.|
|404|Ressource nicht gefunden.|
|500|Interner Serverfehler.|
|Andere Statuscodes|<ul><li>Zu viele Anforderungen</li><li>Server vorübergehend nicht verfügbar</li></ul>|

## <a name="get-translation-status-response"></a>Antwort auf den Übersetzungsstatus abrufen

### <a name="successful-get-translation-status-response"></a>Erfolgreiche „Abrufen des Übersetzungsstatus“-Antwort

Die folgenden Informationen werden bei erfolgreicher Antwort zurückgegeben.

|Name|type|BESCHREIBUNG|
|--- |--- |--- |
|id|Zeichenfolge|ID des Vorgangs.|
|createdDateTimeUtc|Zeichenfolge|Das Datum und die Uhrzeit des Vorgangs.|
|lastActionDateTimeUtc|Zeichenfolge|Datum und Uhrzeit, zu der der Status des Vorgangs aktualisiert wurde.|
|status|String|Liste möglicher Status für Auftrag oder Dokument: <ul><li>Canceled</li><li>Wird abgebrochen</li><li>Fehler</li><li>NotStarted</li><li>Wird ausgeführt</li><li>Erfolgreich</li><li>ValidationFailed</li></ul>|
|Zusammenfassung|StatusSummary|Zusammenfassung, die die unten aufgeführten Details enthält.|
|summary.total|integer|Gesamtanzahl.|
|summary.failed|integer|Fehlgeschlagene Zählung.|
|summary.success|integer|Anzahl der erfolgreichen Ausführungen.|
|summary.inProgress|integer|Anzahl der Dokumente in Bearbeitung.|
|summary.notYetStarted|integer|Anzahl noch nicht gestartet.|
|summary.cancelled|integer|Anzahl der abgebrochenen.|
|summary.totalCharacterCharged|integer|Zeichen, die von der API abgerechnet werden.|

### <a name="error-response"></a>Fehlerantwort

|Name|type|BESCHREIBUNG|
|--- |--- |--- |
|code|Zeichenfolge|Enumerationen, die High-Level-Fehlercodes enthalten. Mögliche Werte:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Nicht autorisiert</li></ul>|
|message|Zeichenfolge|Ruft High-Level-Fehlermeldung ab.|
|target|Zeichenfolge|Ruft die Ursache des Fehlers ab. Dies wäre z. B. „Dokumente“ oder „Dokument-ID“ im Falle eines ungültigen Dokuments.|
|innerError|InnerTranslationError|Neues internes Fehlerformat, das Cognitive Services API-Richtlinien entspricht. Enthält die erforderlichen Eigenschaften ErrorCode, Message und Optional Properties Target, Details (Key Value Pair), Inner Error (kann geschachtelt werden).|
|innerError.code|Zeichenfolge|Ruft Code der Fehlerzeichenfolge ab.|
|innerError.message|Zeichenfolge|Ruft High-Level-Fehlermeldung ab.|
|innerError.target|Zeichenfolge|Ruft die Ursache des Fehlers ab. Dies wäre z. B. „Dokumente“ oder „Dokument-ID“ im Falle eines ungültigen Dokuments.|

## <a name="examples"></a>Beispiele

### <a name="example-successful-response"></a>Beispiel für erfolgreiche Antwort

Das folgende JSON-Objekt ist ein Beispiel für eine erfolgreiche Antwort.

```JSON
{
  "id": "727bf148-f327-47a0-9481-abae6362f11e",
  "createdDateTimeUtc": "2020-03-26T00:00:00Z",
  "lastActionDateTimeUtc": "2020-03-26T01:00:00Z",
  "status": "Succeeded",
  "summary": {
    "total": 10,
    "failed": 1,
    "success": 9,
    "inProgress": 0,
    "notYetStarted": 0,
    "cancelled": 0,
    "totalCharacterCharged": 0
  }
}
```

### <a name="example-error-response"></a>Beispiel für Fehlerantwort

Das folgende JSON-Objekt ist ein Beispiel für eine Fehlerantwort. Das Schema für andere Fehlercodes ist identisch.

Statuscode: 401

```JSON
{
  "error": {
    "code": "Unauthorized",
    "message": "User is not authorized",
    "target": "Document",
    "innerError": {
      "code": "Unauthorized",
      "message": "Operation is not authorized"
    }
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

Befolgen Sie unsere Schnellstartanleitung, um mehr über die Verwendung der Dokumentübersetzung und der Clientbibliothek zu erfahren.

> [!div class="nextstepaction"]
> [Erste Schritte bei der Dokumentübersetzung](../get-started-with-document-translation.md)
