---
title: Rest-API-Funktionen von FHIR für Azure API for FHIR
description: In diesem Artikel werden die RESTful-Interaktionen und -Funktionen für Azure API for FHIR.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 10/06/2021
ms.author: cavoeg
ms.openlocfilehash: c69c3838693c3e59aa7a024c0a67c28c6f1a5d21
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2021
ms.locfileid: "130187551"
---
# <a name="fhir-rest-api-capabilities-for-azure-api-for-fhir"></a>Rest-API-Funktionen von FHIR für Azure API for FHIR

In diesem Artikel werden einige der Nuancen der RESTful-Interaktionen von Azure API for FHIR.


## <a name="conditional-createupdate"></a>Bedingte Erstellung/Aktualisierung

Azure API for FHIR unterstützt die Erstellung, bedingte Erstellung, Aktualisierung und bedingte Aktualisierung gemäß der Definition in der FHIR-Spezifikation. Ein nützlicher Header in diesen Szenarien ist der [If-Match-Header.](https://www.hl7.org/fhir/http.html#concurrency) Der `If-Match` -Header wird verwendet und überprüft die version, die aktualisiert wird, bevor das Update vor dem Update wird. Wenn nicht mit dem erwarteten übereinstimmen, wird die Fehlermeldung `ETag` `ETag` *412 Fehler bei der Vorbedingung ausgegeben.* 

## <a name="delete"></a>Löschen

[](https://www.hl7.org/fhir/http.html#delete) Das löschen, das durch die FHIR-Spezifikation definiert ist, erfordert, dass nach dem Löschen nachfolgende nicht versionsspezifische Leses einer Ressource den HTTP-Statuscode 410 zurückgibt, und die Ressource wird durch die Suche nicht mehr gefunden. Darüber hinaus Azure API for FHIR Sie die Ressource vollständig löschen (einschließlich aller Verlaufsverlaufe). Zum vollständigen Löschen der Ressource können Sie eine Einstellung des Parameters `hardDelete` auf TRUE (`DELETE {server}/{resource}/{id}?hardDelete=true`) übergeben. Wenn Sie diesen Parameter nicht übergeben oder auf FALSE festlegen, sind die historischen Versionen der Ressource `hardDelete` weiterhin verfügbar.

> [!NOTE]
> Wenn Sie nur den Verlauf löschen möchten, Azure API for FHIR benutzerdefinierte Vorgänge unterstützt, mit denen Sie den Verlauf einer `$purge-history` Ressource löschen können. 

## <a name="conditional-delete"></a>Bedingtes Löschen

Zusätzlich zum Löschen unterstützt Azure API for FHIR bedingtes Löschen, wodurch Sie ein Suchkriterium zum Löschen einer Ressource übergeben können. Standardmäßig können Sie beim bedingten Löschen ein Element nach dem anderen löschen. Sie können auch den Parameter `_count` angeben, um bis zu 100 Elemente gleichzeitig zu löschen. Im Folgenden finden Sie einige Beispiele für die Verwendung des bedingten Löschens.

Um ein einzelnes Element mit bedingtem Löschen zu löschen, müssen Sie Suchkriterien angeben, die ein einzelnes Element zurückgibt.

DELETE `https://{{hostname}}/Patient?identifier=1032704`

Sie können dieselbe Suche, aber hardDelete=true verwenden, um auch den ganzen Verlauf zu löschen.

DELETE `https://{{hostname}}/Patient?identifier=1032704&hardDelete=true`

Wenn Sie mehrere Ressourcen löschen möchten, können Sie hinzufügen, wodurch bis zu 100 Ressourcen gelöscht werden, `_count=100` die den Suchkriterien entsprechen.

DELETE `https://{{hostname}}/Patient?identifier=1032704&_count=100`

## <a name="patch-and-conditional-patch"></a>Patchen und bedingtes Patchen

Patch ist ein wertvoller RESTful-Vorgang, wenn Sie nur einen Teil der FHIR-Ressource aktualisieren müssen. Mit Patch können Sie die Elemente angeben, die Sie in der Ressource aktualisieren möchten, ohne den gesamten Datensatz aktualisieren zu müssen. FHIR definiert drei Arten von Möglichkeiten zum Patchen von Ressourcen in FHIR: JSON Patch, XML Patch und FHIR Path Patch. Der FHIR-Dienst unterstützt JSON Patch und bedingten JSON-Patch (mit dem Sie eine Ressource basierend auf einem Suchkriterium anstelle einer ID patchen können). Einige Beispiele für die Verwendung von JSON Patch finden Sie in der [REST-Beispieldatei](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PatchRequests.http).

> [!NOTE]
> Wenn Sie für DIE VERWENDUNG für DIE 3 verwenden und ein History-Paket anfordern, wird die gepatchte Ressource `PATCH` `Bundle.entry.request.method` `PUT` zugeordnet. Dies liegt daran, dass DIE 3-Datei keine Definition für das Verb `PATCH` im [HTTPVerb-Wertsatz enthält.](http://hl7.org/fhir/STU3/valueset-http-verb.html)

### <a name="testing-patch"></a>Testen des Patches

Innerhalb von Patch gibt es einen Testvorgang, mit dem Sie vor dem Patchen überprüfen können, ob eine Bedingung erfüllt ist. Wenn Sie z. B. einen Patienten nur dann festlegen möchten, wenn er nicht bereits als "besend" markiert wurde, können Sie das folgende Beispiel verwenden: 

`http://{FHIR-SERVICE-NAME}/Patient/{PatientID}`PATCH-Inhaltstyp:`application/json-patch+json`

```
[
    {
        “op”: “test”,
        “path”: “/deceasedBoolean”,
        “value”: false
    },
    {
        “op”: “replace”
        “path”: “/deceasedBoolean”,
        “value”: true
    }
]

```

### <a name="patch-in-bundles"></a>Patchen in Paketen

Standardmäßig wird JSON Patch in Bundle-Ressourcen nicht unterstützt. Dies liegt daran, dass ein Bundle nur mit FHIR-Ressourcen unterstützt und JSON Patch keine FHIR-Ressource ist. Um dies zu behandeln, behandeln wir binäre Ressourcen mit einem Inhaltstyp von als Base64-Codierung der JSON-Zeichenfolge, wenn ein `"application/json-patch+json"` Bundle ausgeführt wird. Um Informationen zu dieser Problemumgehung zu erhalten, melden Sie sich bei [Zulip an.](https://chat.fhir.org/#narrow/stream/179166-implementers/topic/Transaction.20with.20PATCH.20request) 

Im folgenden Beispiel möchten wir das Geschlecht des Patienten in "Female" ändern. Wir haben den JSON-Patch `[{"op":"replace","path":"/gender","value":"female"}]` verwendet und in base64 codiert.

`https://{FHIR-SERVICE-NAME}/`POST-Inhaltstyp:`application/json`

```
{
    “resourceType”: “Bundle”
    “id”: “bundle-batch”,
    “type”: “batch”
    “entry”: [
        {
            “fullUrl”: “Patient/{PatientID}”,
            “resource”: {
                “resourceType”: “Binary”,
                “contentType”: “application/json-patch+json”,
                “data”: "W3sib3AiOiJyZXBsYWNlIiwicGF0aCI6Ii9nZW5kZXIiLCJ2YWx1ZSI6ImZlbWFsZSJ9XQ=="
            },
            “request”: { 
                “method”: “PATCH”,
                “url”: “Patient/{PatientID}”
            }
        }
    ]
}

```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie einige der REST-Funktionen von Azure API for FHIR. Als Nächstes erfahren Sie mehr über die wichtigsten Aspekte der Suche nach Ressourcen in FHIR. 

>[!div class="nextstepaction"]
>[Übersicht über die Suche in Azure API for FHIR](overview-of-search.md)

(FHIR&#174;) ist eine registrierte Marke von [HL7](https://hl7.org/fhir/) und wird mit der Berechtigung von HL7 verwendet.