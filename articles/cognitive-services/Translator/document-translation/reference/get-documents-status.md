---
title: Abrufen des Dokumentstatus
titleSuffix: Azure Cognitive Services
description: Die Methode „Abrufen des Dokumentstatus“ (Get Operation Documents Status) gibt den Status aller Dokumente in einer Dokumentübersetzungs-Batchanforderung zurück.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/21/2021
ms.openlocfilehash: 4da15c603028524c0501819c0ecbc3884080ca6f
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132053460"
---
# <a name="get-documents-status"></a>Abrufen des Dokumentstatus

Wenn die Anzahl der Dokumente in der Antwort das Paging-Limit überschreitet, wird serverseitiges Paging verwendet. Paging-Antworten geben ein Teilergebnis an und enthalten ein Fortsetzungstoken in der Antwort. Das Fehlen eines Fortsetzungstokens bedeutet, dass keine weiteren Seiten verfügbar sind.

Die Abfrageparameter $top, $skip und $maxpagesize können verwendet werden, um eine Reihe von Ergebnissen, die zurückgegeben werden sollen, und einen Offset für die Auflistung anzugeben.

Mit $top wird die Gesamtzahl der Datensätze angegeben, die der Benutzer über alle Seiten hinweg zurückgegeben haben möchte. Mit $skip wird die Anzahl der Datensätze angegeben, die aus der Liste der Dokumentstatusangaben übersprungen werden sollen, die vom Server basierend auf der angegebenen Sortiermethode bereitgestellt wird. Standardmäßig wird nach absteigender Startzeit sortiert. Mit $maxpagesize wird die maximale Anzahl der Elemente angegeben, die in einer Seite zurückgegeben werden. Wenn über $top weitere Elemente angefordert werden (oder $top nicht angegeben ist und weitere Elemente zurückgegeben werden sollen), enthält @nextLink den Link zur nächsten Seite.

Der Abfrageparameter $orderBy kann verwendet werden, um die zurückgegebene Liste zu sortieren (Beispiel: „$orderBy=createdDateTimeUtc asc“ oder „$orderBy=createdDateTimeUtc desc“). Die Standardsortierung ist absteigend nach „createdDateTimeUtc“. Einige Abfrageparameter können verwendet werden, um die zurückgegebene Liste zu filtern (z. B. gibt „status=Succeeded,Cancelled“ nur die erfolgreichen und abgebrochenen Dokumente zurück). „createdDateTimeUtcStart“ und „createdDateTimeUtcEnd“ können kombiniert oder separat verwendet werden, um einen Datumsbereich anzugeben, nach dem die zurückgegebene Liste gefiltert wird. Die unterstützten Filterabfrageparameter sind (status, IDs, createdDateTimeUtcStart, createdDateTimeUtcEnd).

Wenn sowohl $top als auch $skip enthalten sind, sollte der Server zuerst $skip anwenden und dann auf die Sammlung $top. 

> [!NOTE]
> Wenn der Server $top und/oder $skip nicht berücksichtigt, muss der Server einen Fehler an den Client zurückgeben, der darüber informiert, anstatt nur die Abfrageoptionen zu ignorieren. Dadurch wird das Risiko verringert, dass der Client Annahmen über die zurückgegebenen Daten vornimmt.

## <a name="request-url"></a>Anfrage-URL

Sendet eine `GET`-Anforderung an:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0/batches/{id}/documents
```

Erfahren Sie, wie Sie Ihren [benutzerdefinierten Domänennamen](../get-started-with-document-translation.md#find-your-custom-domain-name)finden.

> [!IMPORTANT]
>
> * **Für alle API-Anforderungen an den Dienst für die Dokumentübersetzung muss ein Endpunkt einer benutzerdefinierten Domäne verwendet werden**.
> * Zum Senden von HTTP-Anforderungen für die Dokumentübersetzung verwenden Sie nicht den Endpunkt, der im Azure-Portal auf der Ressourcenseite _Schlüssel und Endpunkt_ angegeben ist, und auch nicht den globalen Übersetzungsendpunkt `api.cognitive.microsofttranslator.com`.

## <a name="request-parameters"></a>Anforderungsparameter

Die folgenden Anforderungsparameter werden in der Abfragezeichenfolge übergeben:

|Query parameter (Abfrageparameter)|Geben Sie in|Erforderlich|type|BESCHREIBUNG|
|--- |--- |--- |--- |--- |
|id|path|True|Zeichenfolge|Vorgangs-ID.|
|$maxpagesize|Abfrage|False|integer int32|Mit $maxpagesize wird die maximale Anzahl der Elemente angegeben, die in einer Seite zurückgegeben werden. Wenn über $top weitere Elemente angefordert werden (oder $top nicht angegeben ist und weitere Elemente zurückgegeben werden sollen), enthält @nextLink den Link zur nächsten Seite. Clients KÖNNEN servergesteuertes Paging mit einer bestimmten Seitengröße anfordern, indem sie eine $maxpagesize-Einstellung angeben. Der Server SOLLTE diese Einstellung berücksichtigen, wenn die angegebene Seitengröße kleiner als die Standardseitengröße des Servers ist.|
|$orderBy|Abfrage|False|array|Die Sortierabfrage für die Auflistung (Beispiel: „CreatedDateTimeUtc asc“, „CreatedDateTimeUtc desc“).|
|$skip|Abfrage|False|integer int32|Mit $skip wird die Anzahl der Datensätze angegeben, die aus der Liste der Datensätze übersprungen werden sollen, die vom Server basierend auf der angegebenen Sortiermethode bereitgestellt wird. Standardmäßig wird nach absteigender Startzeit sortiert. Clients KÖNNEN $top- und $skip-Abfrageparameter verwenden, um eine Reihe von Ergebnissen, die zurückgegeben werden sollen, und einen Offset für die Auflistung anzugeben. Wenn sowohl $top als auch $skip vom Client angegeben werden, SOLLTE der Server zuerst $skip und dann $top auf die Auflistung anwenden. Hinweis: Wenn der Server $top und/oder $skip nicht berücksichtigt, MUSS der Server einen Fehler an den Client zurückgeben, der darüber informiert, anstatt nur die Abfrageoptionen zu ignorieren.|
|$top|Abfrage|False|integer int32|Mit $top wird die Gesamtzahl der Datensätze angegeben, die der Benutzer über alle Seiten hinweg zurückgegeben haben möchte. Clients KÖNNEN $top- und $skip-Abfrageparameter verwenden, um eine Reihe von Ergebnissen, die zurückgegeben werden sollen, und einen Offset für die Auflistung anzugeben. Wenn sowohl $top als auch $skip vom Client angegeben werden, SOLLTE der Server zuerst $skip und dann $top auf die Auflistung anwenden. Hinweis: Wenn der Server $top und/oder $skip nicht berücksichtigt, MUSS der Server einen Fehler an den Client zurückgeben, der darüber informiert, anstatt nur die Abfrageoptionen zu ignorieren.|
|createdDateTimeUtcEnd|Abfrage|False|string Datum/Uhrzeit|Der Endzeitpunkt (Datum/Uhrzeit), vor dem Elemente abgerufen werden sollen.|
|createdDateTimeUtcStart|Abfrage|False|string Datum/Uhrzeit|Der Startzeitpunkt (Datum/Uhrzeit), nach dem Elemente abgerufen werden sollen.|
|ids|Abfrage|False|array|IDs, die beim Filtern verwendet werden.|
|statuses|Abfrage|False|array|Statusangaben, die beim Filtern verwendet werden.|

## <a name="request-headers"></a>Anforderungsheader

Anforderungsheader:

|Header|BESCHREIBUNG|
|--- |--- |
|Ocp-Apim-Subscription-Key|Erforderlicher Anforderungsheader|

## <a name="response-status-codes"></a>Antwortstatuscodes

Im Folgenden finden Sie die möglichen HTTP-Statuscodes, die eine Anforderung zurückgeben kann.

|Statuscode|BESCHREIBUNG|
|--- |--- |
|200|OK. Die Anforderung wurde erfolgreich ausgeführt, und der Status aller Dokumente wird zurückgegeben. HeadersRetry-After: integerETag: Zeichenfolge|
|400|Ungültige Anforderung. Eingabeparameter prüfen.|
|401|Nicht autorisiert. Anmeldeinformationen prüfen.|
|404|Ressource nicht gefunden.|
|500|Interner Serverfehler.|
|Andere Statuscodes|<ul><li>Zu viele Anforderungen</li><li>Server vorübergehend nicht verfügbar</li></ul>|


## <a name="get-documents-status-response"></a>„Abrufen des Dokumentstatus“-Antwort

### <a name="successful-get-documents-status-response"></a>Erfolgreiche „Abrufen des Dokumentstatus“-Antwort

Die folgenden Informationen werden bei erfolgreicher Antwort zurückgegeben.

|Name|type|BESCHREIBUNG|
|--- |--- |--- |
|@nextLink|Zeichenfolge|Die URL für die nächste Seite. Null, wenn keine weiteren Seiten verfügbar sind.|
|Wert|DocumentStatus []|Der detaillierte Status der einzelnen unten aufgeführten Dokumente.|
|value.path|Zeichenfolge|Speicherort des Dokuments oder des Ordners.|
|value.sourcePath|Zeichenfolge|Speicherort des Quelldokuments.|
|value.createdDateTimeUtc|Zeichenfolge|Das Datum und die Uhrzeit des Vorgangs.|
|value.lastActionDateTimeUtc|Zeichenfolge|Datum und Uhrzeit, zu der der Status des Vorgangs aktualisiert wurde.|
|value.status|status|Liste möglicher Status für Auftrag oder Dokument:<ul><li>Canceled</li><li>Wird abgebrochen</li><li>Fehler</li><li>NotStarted</li><li>Wird ausgeführt</li><li>Erfolgreich</li><li>ValidationFailed</li></ul>|
|value.to|Zeichenfolge|In Sprache.|
|value.progress|number|Der Fortschritt der Übersetzung, falls verfügbar.|
|value.id|Zeichenfolge|Dokument-ID|
|value.characterCharged|integer|Zeichen, die von der API abgerechnet werden.|

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

Das ist ein Beispiel für eine erfolgreiche Antwort.

```JSON
{
  "value": [
    {
      "path": "https://myblob.blob.core.windows.net/destinationContainer/fr/mydoc.txt",
      "sourcePath": "https://myblob.blob.core.windows.net/sourceContainer/fr/mydoc.txt",
      "createdDateTimeUtc": "2020-03-26T00:00:00Z",
      "lastActionDateTimeUtc": "2020-03-26T01:00:00Z",
      "status": "Running",
      "to": "fr",
      "progress": 0.1,
      "id": "273622bd-835c-4946-9798-fd8f19f6bbf2",
      "characterCharged": 0
    }
  ],
  "@nextLink": "https://westus.cognitiveservices.azure.com/translator/text/batch/v1.0/operation/0FA2822F-4C2A-4317-9C20-658C801E0E55/documents?$top=5&$skip=15"
}
```

### <a name="example-error-response"></a>Beispiel für Fehlerantwort

Das folgende Beispiel veranschaulicht eine Fehlerantwort. Das Schema für andere Fehlercodes ist identisch.

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
