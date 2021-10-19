---
title: 'Referenz: Fehler der Formularerkennung (2021-09-30-preview)'
titleSuffix: Azure Applied AI Services
description: Erfahren Sie, wie Fehler in der Formularerkennung dargestellt werden, und finden Sie eine Liste möglicher Fehler, die vom Dienst zurückgegeben werden.
author: paulhsu
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: reference
ms.date: 09/30/2021
ms.author: paulhsu
ms.custom: cog-serv-seo-aug-2020
keywords: Verarbeiten von Dokumenten
ms.openlocfilehash: a67c98189198cf8df0f8f7e6fa88c9a552812bfa
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2021
ms.locfileid: "129730618"
---
# <a name="form-recognizer-error-guide-v30-preview"></a>Formularerkennung: Fehlerleitfaden v3.0 (Vorschau)

Die Formularerkennung verwendet einen einheitlichen Entwurf, um alle Fehler darzustellen, die in den REST-APIs auftreten.  Wenn ein API-Vorgang den Statuscode 4xx oder 5xx zurückgibt, werden im JSON-Antworttext wie folgt zusätzliche Informationen zum Fehler zurückgegeben:

```json
{
  "error": {
    "code": "InvalidRequest",
    "message": "Invalid request.",
    "innererror": {
      "code": "InvalidContent",
      "message": "The file format is unsupported or corrupted. Refer to documentation for the list of supported formats."
    }
  }
}
```

Bei zeitintensiven Vorgängen, bei denen mehrere Fehler auftreten können, wird der Fehlercode der obersten Ebene auf den schwerwiegendsten Fehler festgelegt, wobei die einzelnen Fehler unter der Eigenschaft *error.details* aufgeführt werden.  In solchen Szenarien gibt die *target*-Eigenschaft jedes einzelnen Fehlers den Trigger des Fehlers an.

```json
{
    "status": "failed",
    "createdDateTime": "2021-07-14T10:17:51Z",
    "lastUpdatedDateTime": "2021-07-14T10:17:51Z",
    "error": {
        "code": "InternalServerError",
        "message": "An unexpected error occurred.",
        "details": [
            {
                "code": "InternalServerError",
                "message": "An unexpected error occurred."
            },
            {
                "code": "InvalidContentDimensions",
                "message": "The input image dimensions are out of range. Refer to documentation for supported image dimensions.",
                "target": "2"
            }
        ]
    }
}
```

Die *error.code*-Eigenschaft der obersten Ebene kann eine der folgenden Fehlercodemeldungen sein:

| Fehlercode           | Nachricht                                                | Http-Status |
| -------------------- | ------------------------------------------------------ | ----------- |
| InvalidRequest       | Ungültige Anforderung.                                       | 400         |
| InvalidArgument      | Ungültiges Argument.                                      | 400         |
| Verboten            | Der Zugriff ist aufgrund einer Richtlinie oder einer anderen Konfiguration unzulässig. | 403         |
| NotFound             | Die Ressource wurde nicht gefunden.                                    | 404         |
| MethodNotAllowed     | Die angeforderte HTTP-Methode ist nicht zulässig.              | 405         |
| Konflikt:             | Die Anforderung konnte aufgrund eines Konflikts nicht abgeschlossen werden.  | 409         |
| UnsupportedMediaType | Der Inhaltstyp der Anforderung wird nicht unterstützt.                 | 415         |
| InternalServerError  | Ein unerwarteter Fehler ist aufgetreten.                          | 500         |
| ServiceUnavailable   | Es ist ein vorübergehender Fehler aufgetreten. Versuchen Sie es erneut.      | 503         |

Wenn möglich, werden weitere Details in der *inner error*-Eigenschaft (Interner Fehler) angegeben.

| Oberster Fehlercode | Interner Fehlercode | Nachricht |
| -------------- | ---------------- | ------- |
| Konflikt: | ModelExists | Ein Modell mit dem angegebenen Namen ist bereits vorhanden. |
| Verboten | AuthorizationFailed | Fehler bei der Autorisierung: {details} |
| Verboten | InvalidDataProtectionKey | Der Datenschutzschlüssel ist ungültig: {details} |
| Verboten | OutboundAccessForbidden | Die Anforderung enthält einen Domänennamen, der von der aktuellen Zugriffssteuerungsrichtlinie nicht zugelassen wird. |
| InternalServerError | Unbekannt | Unbekannter Fehler. |
| InvalidArgument | InvalidContentSourceFormat | Ungültige Inhaltsquelle: {details} |
| InvalidArgument | InvalidParameter | Der Parameter {parameterName} ist ungültig: {details} |
| InvalidArgument | InvalidParameterLength | Die Länge des Parameters {parameterName} darf {maxChars} Zeichen nicht überschreiten. |
| InvalidArgument | InvalidSasToken | Die Shared Access Signature (SAS) ist ungültig: {details} |
| InvalidArgument | ParameterMissing | Der Parameter {parameterName} ist erforderlich. |
| InvalidRequest | ContentSourceNotAccessible | Auf den Inhalt kann nicht zugegriffen werden: {details} |
| InvalidRequest | ContentSourceTimeout | Timeout beim Empfangen der Datei vom Client. |
| InvalidRequest | DocumentModelLimit | Das Konto kann nicht mehr als {maximumModels} Modelle erstellen. |
| InvalidRequest | DocumentModelLimitComposed | Das Konto kann kein Modell mit mehr als {details} Komponentenmodellen erstellen. |
| InvalidRequest | InvalidContent | Die Datei ist beschädigt oder das Format wird nicht unterstützt. Eine Liste der unterstützten Formate finden Sie in der Dokumentation. |
| InvalidRequest | InvalidContentDimensions | Die Dimensionen des Eingabebilds liegen außerhalb des Bereichs. Informationen zu unterstützten Bilddimensionen finden Sie in der Dokumentation. |
| InvalidRequest | InvalidContentLength | Das Eingabebild ist zu groß. Die maximale Dateigröße finden Sie in der Dokumentation. |
| InvalidRequest | InvalidFieldsDefinition | Ungültige Felder: {details} |
| InvalidRequest | InvalidTrainingContentLength | Der Trainingsinhalt enthält {bytes} Bytes. Das Training ist auf {maxBytes} Bytes beschränkt. |
| InvalidRequest | InvalidTrainingContentPageCount | Der Trainingsinhalt enthält {pages} Seiten. Das Training ist auf {pages} Seiten beschränkt. |
| InvalidRequest | ModelAnalyzeError | Die Analyse mit einem benutzerdefinierten Modell konnte nicht ausgeführt werden: {details} |
| InvalidRequest | ModelBuildError | Das Modell konnte nicht erstellt werden: {details} |
| InvalidRequest | ModelComposeError | Das Modell konnte nicht zusammengestellt werden: {details} |
| InvalidRequest | ModelNotReady | Das Modell ist nicht bereit für den angeforderten Vorgang. Warten Sie, bis das Training abgeschlossen ist, oder prüfen Sie auf Vorgangsfehler. |
| InvalidRequest | ModelReadOnly | Das angeforderte Modell ist schreibgeschützt. |
| InvalidRequest | NotSupportedApiVersion | Der angeforderte Vorgang erfordert {minimumApiVersion} oder höher. |
| InvalidRequest | OperationNotCancellable | Der Vorgang kann nicht mehr abgebrochen werden. |
| InvalidRequest | TrainingContentMissing | Trainingsdaten fehlen: {details} |
| InvalidRequest | UnsupportedContent | Inhalt wird nicht unterstützt: {details} |
| NotFound | ModelNotFound | Das angeforderte Modell wurde nicht gefunden. Es wurde möglicherweise gelöscht oder wird noch erstellt. |
| NotFound | OperationNotFound | Der angeforderte Vorgang wurde nicht gefunden. Der Bezeichner ist möglicherweise ungültig, oder der Vorgang ist abgelaufen. |