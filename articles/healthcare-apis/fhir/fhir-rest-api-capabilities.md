---
title: FHIR-Rest-API-Funktionen für den FHIR-Dienst der Azure Healthcare-APIs
description: In diesem Artikel werden die RESTful-Interaktionen und -Funktionen für den FHIR-Dienst der Azure Healthcare-APIs beschrieben.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 10/06/2021
ms.author: cavoeg
ms.openlocfilehash: bca8eb3714a685c9477f512a38406cb77f1c6886
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2021
ms.locfileid: "130187561"
---
# <a name="fhir-rest-api-capabilities-for-azure-healthcare-apis-fhir-service"></a>FHIR-Rest-API-Funktionen für den FHIR-Dienst der Azure Healthcare-APIs

In diesem Artikel werden einige der Nuancen der RESTful-Interaktionen des FHIR-Diensts der Azure Healthcare-APIs (auch als FHIR-Dienst bezeichnet) behandelt.


## <a name="conditional-createupdate"></a>Bedingtes Erstellen/Aktualisieren

Der FHIR-Dienst unterstützt das Erstellen, bedingte Erstellen, Aktualisieren und bedingte Update gemäß FHIR-Spezifikation. Ein nützlicher Header in diesen Szenarien ist der [If-Match-Header.](https://www.hl7.org/fhir/http.html#concurrency) Der `If-Match` Header wird verwendet und überprüft die version, die aktualisiert wird, bevor das Update erfolgt. Wenn nicht `ETag` mit dem erwarteten `ETag` übereinstimmt, wird die Fehlermeldung *412 Fehler bei Vorbedingung ausgegeben.* 

## <a name="delete"></a>Löschen

[Das](https://www.hl7.org/fhir/http.html#delete) durch die FHIR-Spezifikation definierte Löschen erfordert, dass nachfolgende nicht versionsspezifische Lesevorgänge einer Ressource nach dem Löschen den HTTP-Statuscode 410 zurückgibt und die Ressource nicht mehr durch Suchen gefunden wird. Darüber hinaus können Sie mit dem FHIR-Dienst die Ressource vollständig löschen (einschließlich des gesamten Verlaufs). Zum vollständigen Löschen der Ressource können Sie eine Einstellung des Parameters `hardDelete` auf TRUE (`DELETE {server}/{resource}/{id}?hardDelete=true`) übergeben. Wenn Sie diesen Parameter nicht übergeben oder auf FALSE festgelegt `hardDelete` haben, sind die historischen Versionen der Ressource weiterhin verfügbar.

> [!NOTE]
> Wenn Sie nur den Verlauf löschen möchten, unterstützt der FHIR-Dienst benutzerdefinierte Vorgänge, `$purge-history` , mit denen Sie den Verlauf einer Ressource löschen können. 

## <a name="conditional-delete"></a>Bedingtes Löschen

Zusätzlich zum Löschen unterstützt der FHIR-Dienst das bedingte Löschen, mit dem Sie suchkriterien zum Löschen einer Ressource übergeben können. Standardmäßig können Sie mit dem bedingten Löschen jeweils ein Element löschen. Sie können auch den `_count` Parameter angeben, um bis zu 100 Elemente gleichzeitig zu löschen. Im Folgenden finden Sie einige Beispiele für die Verwendung des bedingten Löschens.

Um ein einzelnes Element mit bedingtem Löschen zu löschen, müssen Sie Suchkriterien angeben, die ein einzelnes Element zurückgibt.

DELETE `https://{{hostname}}/Patient?identifier=1032704`

Sie können dieselbe Suche durchführen, aber hardDelete=true einschließen, um auch den gesamten Verlauf zu löschen.

DELETE `https://{{hostname}}/Patient?identifier=1032704&hardDelete=true`

Wenn Sie mehrere Ressourcen löschen möchten, können Sie einschließen, `_count=100` wodurch bis zu 100 Ressourcen gelöscht werden, die den Suchkriterien entsprechen.

DELETE `https://{{hostname}}/Patient?identifier=1032704&_count=100`

## <a name="patch-and-conditional-patch"></a>Patch und bedingter Patch

Patchen ist ein wertvoller RESTful-Vorgang, wenn Sie nur einen Teil der FHIR-Ressource aktualisieren müssen. Mit Patch können Sie die Elemente angeben, die Sie in der Ressource aktualisieren möchten, ohne den gesamten Datensatz aktualisieren zu müssen. FHIR definiert drei Arten von Methoden zum Patchen von Ressourcen in FHIR: JSON Patch, XML Patch und FHIR Path Patch. Der FHIR-Dienst unterstützt JSON-Patch und bedingten JSON-Patch (mit dem Sie eine Ressource basierend auf einem Suchkriterium anstelle einer ID patchen können). Einige Beispiele für die Verwendung von JSON Patch finden Sie in der [REST-Beispieldatei](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PatchRequests.http).

> [!NOTE]
> Wenn `PATCH` Sie für STU3 verwenden und ein Verlaufspaket anfordern, wird die gepatchte Ressource `Bundle.entry.request.method` `PUT` zugeordnet. Dies liegt daran, dass STU3 keine Definition für das `PATCH` Verb im [HTTPVerb-Wertsatz](http://hl7.org/fhir/STU3/valueset-http-verb.html)enthält.

### <a name="testing-patch"></a>Testen des Patches

In Patch gibt es einen Testvorgang, mit dem Sie überprüfen können, ob eine Bedingung erfüllt ist, bevor Sie den Patch durchführen. Wenn Sie z. B. einen Patienten als Patienten festlegen möchten (nur wenn er noch nicht als "Gezeichnete" gekennzeichnet ist), können Sie das folgende Beispiel verwenden: 

`http://{FHIR-SERVICE-NAME}/Patient/{PatientID}`PATCH-Inhaltstyp:`application/json-patch+json`

```
[
    {
        "op": "test",
        "path": "/deceasedBoolean",
        "value": false
    },
    {
        "op": "replace",
        "path": "/deceasedBoolean",
        "value": true
    }
]

```

### <a name="patch-in-bundles"></a>Patchen in Paketen

Standardmäßig wird JSON Patch in Bundle-Ressourcen nicht unterstützt. Dies liegt daran, dass ein Bundle nur mit FHIR-Ressourcen unterstützt und JSON Patch keine FHIR-Ressource ist. Um dies zu umgehen, behandeln wir Binärressourcen mit dem Inhaltstyp `"application/json-patch+json"` als Base64-Codierung der JSON-Zeichenfolge, wenn ein Bundle ausgeführt wird. Um Informationen zu dieser Problemumgehung zu erfahren, melden Sie sich bei [Zulip](https://chat.fhir.org/#narrow/stream/179166-implementers/topic/Transaction.20with.20PATCH.20request)an. 

Im folgenden Beispiel soll das Geschlecht des Patienten in "female" geändert werden. Wir haben den JSON-Patch verwendet `[{"op":"replace","path":"/gender","value":"female"}]` und in Base64 codiert.

`https://{FHIR-SERVICE-NAME}/`POST-Inhaltstyp:`application/json`

```
{
    "resourceType": "Bundle",
    "id": "bundle-batch",
    "type": "batch",
    "entry": [
        {
            "fullUrl": "Patient/{PatientID}",
            "resource": {
                "resourceType": "Binary",
                "contentType": "application/json-patch+json",
                "data": "W3sib3AiOiJyZXBsYWNlIiwicGF0aCI6Ii9nZW5kZXIiLCJ2YWx1ZSI6ImZlbWFsZSJ9XQ=="
            },
            "request": { 
                "method": "PATCH",
                "url": "Patient/{PatientID}"
            }
        }
    ]
}

```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie einige der REST-Funktionen des FHIR-Diensts kennengelernt. Als Nächstes erfahren Sie mehr über die wichtigsten Aspekte beim Durchsuchen von Ressourcen im FHIR-Dienst. 

>[!div class="nextstepaction"]
>[Übersicht über die FHIR-Suche](overview-of-search.md)

(FHIR&#174;) ist eine registrierte Marke von [HL7](https://hl7.org/fhir/) und wird mit der Berechtigung von HL7 verwendet.


