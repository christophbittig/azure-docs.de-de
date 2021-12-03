---
title: Methode „Abbrechen der Übersetzung“
titleSuffix: Azure Cognitive Services
description: Die Methode „Abbrechen der Übersetzung“ (Cancel translation) bricht einen aktuell verarbeiteten oder in die Warteschlange eingereihten Vorgang ab.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 06/20/2021
ms.openlocfilehash: 8bf718da42dbd29c864736209cf441b03f3c9e1b
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132059258"
---
# <a name="cancel-translation"></a>Abbrechen der Übersetzung

Brechen Sie den derzeit ausgeführten oder einen in die Warteschlange eingereihten Auftrag ab. Ein Vorgang wird nicht abgebrochen, wenn er bereits abgeschlossen wurde, ein Fehler aufgetreten ist oder der Vorgang abgebrochen wird. Eine ungültige Anforderung wird zurückgegeben. Alle Dokumente, die übersetzt sind, werden nicht abgebrochen und werden in Rechnung gestellt. Alle ausstehenden Dokumente werden abgebrochen, wenn möglich.

## <a name="request-url"></a>Anfrage-URL

Sendet eine `DELETE`-Anforderung an:

```DELETE HTTP
https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0/batches/{id}
```

Erfahren Sie, wie Sie Ihren [benutzerdefinierten Domänennamen](../get-started-with-document-translation.md#find-your-custom-domain-name)finden.

> [!IMPORTANT]
>
> * **Für alle API-Anforderungen an den Dienst für die Dokumentübersetzung muss ein Endpunkt einer benutzerdefinierten Domäne verwendet werden**.
> * Zum Senden von HTTP-Anforderungen für die Dokumentübersetzung verwenden Sie nicht den Endpunkt, der im Azure-Portal auf der Ressourcenseite _Schlüssel und Endpunkt_ angegeben ist, und auch nicht den globalen Übersetzungsendpunkt `api.cognitive.microsofttranslator.com`.

## <a name="request-parameters"></a>Anforderungsparameter

Die folgenden Anforderungsparameter werden in der Abfragezeichenfolge übergeben:

|Query parameter (Abfrageparameter)|Erforderlich|BESCHREIBUNG|
|-----|-----|-----|
|id|Richtig|Vorgangs-ID|

## <a name="request-headers"></a>Anforderungsheader

Anforderungsheader:

|Header|BESCHREIBUNG|
|-----|-----|
|Ocp-Apim-Subscription-Key|Erforderlicher Anforderungsheader|

## <a name="response-status-codes"></a>Antwortstatuscodes

Im Folgenden finden Sie die möglichen HTTP-Statuscodes, die eine Anforderung zurückgeben kann.

| Statuscode| BESCHREIBUNG|
|-----|-----|
|200|OK. Ihre Abbruchanforderung wurde übermittelt.|
|401|Nicht autorisiert. Anmeldeinformationen prüfen.|
|404|Nicht gefunden: Ressource nicht gefunden. 
|500|Interner Serverfehler.
|Andere Statuscodes|<ul><li>Zu viele Anforderungen</li><li>Server vorübergehend nicht verfügbar</li></ul>|

## <a name="cancel-translation-response"></a>„Abbrechen der Übersetzung“-Antwort

### <a name="successful-response"></a>Erfolgreiche Antwort

Die folgenden Informationen werden bei erfolgreicher Antwort zurückgegeben.

|Name|type|BESCHREIBUNG|
|--- |--- |--- |
|id|Zeichenfolge|ID des Vorgangs.|
|createdDateTimeUtc|Zeichenfolge|Das Datum und die Uhrzeit des Vorgangs.|
|lastActionDateTimeUtc|Zeichenfolge|Datum und Uhrzeit, zu der der Status des Vorgangs aktualisiert wurde.|
|status|String|Liste möglicher Status für Auftrag oder Dokument: <ul><li>Canceled</li><li>Wird abgebrochen</li><li>Fehler</li><li>NotStarted</li><li>Wird ausgeführt</li><li>Erfolgreich</li><li>ValidationFailed</li></ul>|
|Zusammenfassung|StatusSummary|Zusammenfassung, die die unten aufgeführten Details enthält.|
|summary.total|integer|Gesamtanzahl der Dokumente.|
|summary.failed|integer|Anzahl der Dokumente mit Fehlern.|
|summary.success|integer|Anzahl der Dokumente, die erfolgreich übersetzt wurden.|
|summary.inProgress|integer|Zählung der Dokumente in Bearbeitung.|
|summary.notYetStarted|integer|Anzahl von Dokumenten, die noch nicht in Bearbeitung sind.|
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
|innerError.target|Zeichenfolge|Ruft die Ursache des Fehlers ab. Dies wäre z. B. „Dokumente“ oder „Dokument-ID“, falls ein ungültiges Dokument vorliegt.|

## <a name="examples"></a>Beispiele

### <a name="example-successful-response"></a>Beispiel für erfolgreiche Antwort

Das folgende JSON-Objekt ist ein Beispiel für eine erfolgreiche Antwort.

Statuscode: 200

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

Statuscode: 500

```JSON
{
  "error": {
    "code": "InternalServerError",
    "message": "Internal Server Error",
    "target": "Operation",
    "innerError": {
      "code": "InternalServerError",
      "message": "Unexpected internal server error has occurred"
    }
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

Befolgen Sie unsere Schnellstartanleitung, um mehr über die Verwendung der Dokumentübersetzung und der Clientbibliothek zu erfahren.

> [!div class="nextstepaction"]
> [Erste Schritte bei der Dokumentübersetzung](../get-started-with-document-translation.md)
