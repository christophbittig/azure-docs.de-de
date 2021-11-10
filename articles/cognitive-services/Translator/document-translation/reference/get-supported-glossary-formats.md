---
title: Rufen Sie unterstützte Glossarformat-Methoden ab
titleSuffix: Azure Cognitive Services
description: Die Methode Unterstütze Glossarformate Abrufen gibt die Liste der unterstützten Glossarformate zurück.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/21/2021
ms.openlocfilehash: 7e81f333b9291d58bd014f91a9d22b38d844466b
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132057250"
---
# <a name="get-supported-glossary-formats"></a>Abrufen unterstützter Glossarformate

Die unterstützte GET-Glossarformatmethode gibt eine Liste der Glossarformate zurück, die vom Dokumentübersetzungsdienst unterstützt werden. Die Liste enthält die verwendete allgemeine Dateierweiterung.

## <a name="request-url"></a>Anfrage-URL

Sendet eine `GET`-Anforderung an:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0/glossaries/formats
```

Erfahren Sie, wie Sie Ihren [benutzerdefinierten Domänennamen](../get-started-with-document-translation.md#find-your-custom-domain-name)finden.

> [!IMPORTANT]
>
> * **Für alle API-Anforderungen an den Dienst für die Dokumentübersetzung muss ein Endpunkt einer benutzerdefinierten Domäne verwendet werden**.
> * Zum Senden von HTTP-Anforderungen für die Dokumentübersetzung verwenden Sie nicht den Endpunkt, der im Azure-Portal auf der Ressourcenseite _Schlüssel und Endpunkt_ angegeben ist, und auch nicht den globalen Übersetzungsendpunkt `api.cognitive.microsofttranslator.com`.

## <a name="request-headers"></a>Anforderungsheader

Anforderungsheader:

|Header|BESCHREIBUNG|
|--- |--- |
|Ocp-Apim-Subscription-Key|Erforderlicher Anforderungsheader|

## <a name="response-status-codes"></a>Antwortstatuscodes

Im Folgenden finden Sie die möglichen HTTP-Statuscodes, die eine Anforderung zurückgeben kann.

|Statuscode|BESCHREIBUNG|
|--- |--- |
|200|OK. Gibt die Liste der unterstützten Glossarformate der Datei zurück.|
|500|Interner Serverfehler.|
|Andere Statuscodes|<ul><li>Zu viele Anforderungen</li><li>Server vorübergehend nicht verfügbar</li></ul>|


## <a name="get-supported-glossary-formats-response"></a>Rufen Sie die unterstützte Glossarformat-Antwort ab

Basistyp für die Listenrückgabe in der API Unterstützte Glossarformate abrufen.

### <a name="successful-get-supported-glossary-formats-response"></a>Abrufen der Antwort von Glossarformaten erfolgreich

Basistyp für die Listenrückgabe in der API Unterstützte Glossarformate abrufen.

|Name|type|BESCHREIBUNG|
|--- |--- |--- |
|value|FileFormat []|File Format [] enthält die unten aufgeführten Details.|
|value.contentTypes|string []|Unterstützte Inhaltstypen für dieses Format.|
|value.defaultVersion|Zeichenfolge|Standardversion, wenn keine angegeben ist|
|value.fileExtensions|string []| Unterstützte Dateierweiterung für dieses Format.|
|value.format|Zeichenfolge|Der Name des Formats.|
|value.versions|string []| Unterstützte Version.|

### <a name="error-response"></a>Fehlerantwort

|Name|type|BESCHREIBUNG|
|--- |--- |--- |
|code|Zeichenfolge|Enumerationen, die High-Level-Fehlercodes enthalten. Mögliche Werte:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Nicht autorisiert</li></ul>|
|message|Zeichenfolge|Ruft High-Level-Fehlermeldung ab.|
|innerError|InnerTranslationError|Neues internes Fehlerformat, das Cognitive Services API-Richtlinien entspricht. Enthält die erforderlichen Eigenschaften ErrorCode, Message und Optional Properties Target, Details (Key Value Pair), Inner Error (kann geschachtelt werden).|
|innerError.code|Zeichenfolge|Ruft Code der Fehlerzeichenfolge ab.|
|innerError.message|Zeichenfolge|Ruft High-Level-Fehlermeldung ab.|
|innerError.target|Zeichenfolge|Ruft die Ursache des Fehlers ab. Dies wäre z. B. „Dokumente“ oder „Dokument-ID“ im Falle eines ungültigen Dokuments.|

## <a name="examples"></a>Beispiele

### <a name="example-successful-response"></a>Beispiel für erfolgreiche Antwort

Das ist ein Beispiel für eine erfolgreiche Antwort.

```JSON
{
    "value": [
        {
            "format": "XLIFF",
            "fileExtensions": [
                ".xlf"
            ],
            "contentTypes": [
                "application/xliff+xml"
            ],
            "defaultVersion": "1.2",
            "versions": [
                "1.0",
                "1.1",
                "1.2"
            ]
        },
        {
            "format": "TSV",
            "fileExtensions": [
                ".tsv",
                ".tab"
            ],
            "contentTypes": [
                "text/tab-separated-values"
            ]
        },
        {
            "format": "CSV",
            "fileExtensions": [
                ".csv"
            ],
            "contentTypes": [
                "text/csv"
            ]
        }
    ]
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
