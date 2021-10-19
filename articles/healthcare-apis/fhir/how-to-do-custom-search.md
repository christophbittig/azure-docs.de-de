---
title: Durchführen der benutzerdefinierten Suche im FHIR-Dienst
description: In diesem Artikel wird beschrieben, wie Sie ihre eigenen benutzerdefinierten Suchparameter definieren können, die in der Datenbank verwendet werden sollen.
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/03/2021
ms.author: cavoeg
ms.openlocfilehash: b299a7a60e65ede867bf9501f99b76294011cc93
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122779019"
---
# <a name="defining-custom-search-parameters"></a>Definieren von benutzerdefinierten Suchparametern

> [!IMPORTANT]
> Azure Healthcare-APIs befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

Die FHIR-Spezifikation definiert einen Satz von Suchparametern für alle Ressourcen und Suchparameter, die spezifisch für eine Ressource(n) sind. Es gibt jedoch Szenarien, in denen Sie möglicherweise nach einem Element in einer Ressource suchen möchten, das nicht durch die FHIR-Spezifikation als Standardsuchparameter definiert ist. In diesem Artikel wird beschrieben, wie Sie ihre eigenen [Suchparameter](https://www.hl7.org/fhir/searchparameter.html) definieren können, die im FHIR-Dienst in den Azure Healthcare-APIs verwendet werden (dies wird als FHIR-Dienst bezeichnet).

> [!NOTE]
> Jedes Mal, wenn Sie einen Suchparameter erstellen, aktualisieren oder löschen, müssen Sie einen [Neuindizierungsauftrag](how-to-run-a-reindex.md) ausführen, damit der Suchparameter in der Produktion verwendet werden kann. Im Folgenden wird beschrieben, wie Sie Suchparameter testen können, bevor Sie den gesamten FHIR-Dienst neu indizieren.

## <a name="create-new-search-parameter"></a>Erstellen eines neuen Suchparameters

Um einen neuen Suchparameter zu erstellen, verwenden Sie `POST` die Ressource für die `SearchParameter` Datenbank. Im folgenden Codebeispiel wird gezeigt, wie sie der Ressource den [Suchparameter "US Core Race"](http://hl7.org/fhir/us/core/STU3.1.1/SearchParameter-us-core-race.html) `Patient` hinzufügen.

```rest
POST {{FHIR_URL}}/SearchParameter

{
  "resourceType" : "SearchParameter",
  "id" : "us-core-race",
  "url" : "http://hl7.org/fhir/us/core/SearchParameter/us-core-race",
  "version" : "3.1.1",
  "name" : "USCoreRace",
  "status" : "active",
  "date" : "2019-05-21",
  "publisher" : "US Realm Steering Committee",
  "contact" : [
    {
      "telecom" : [
        {
          "system" : "other",
          "value" : "http://www.healthit.gov/"
        }
      ]
    }
  ],
  "description" : "Returns patients with a race extension matching the specified code.",
  "jurisdiction" : [
    {
      "coding" : [
        {
          "system" : "urn:iso:std:iso:3166",
          "code" : "US",
          "display" : "United States of America"
        }
      ]
    }
  ],
  "code" : "race",
  "base" : [
    "Patient"
  ],
  "type" : "token",
  "expression" : "Patient.extension.where(url = 'http://hl7.org/fhir/us/core/StructureDefinition/us-core-race').extension.value.code"
}

``` 

> [!NOTE]
> Der neue Suchparameter wird in der Funktions-Anweisung des FHIR-Diensts angezeigt, nachdem Sie den Suchparameter per POST an die Datenbank gesendet **und** Ihre Datenbank neu indiziert haben. Das Anzeigen `SearchParameter` von in der capability-Anweisung ist die einzige Möglichkeit, zu ermitteln, ob ein Suchparameter in Ihrem FHIR-Dienst unterstützt wird. Wenn Sie den Suchparameter finden können, indem Sie nach dem Suchparameter suchen, ihn aber nicht in der capability-Anweisung sehen, müssen Sie den Suchparameter trotzdem indiziert. Sie können mehrere Suchparameter per POST erstellen, bevor Sie einen Neuindizierungsvorgang auslösen.

Wichtige Elemente eines `SearchParameter` :

* **url**: Ein eindeutiger Schlüssel zum Beschreiben des Suchparameters. Viele Organisationen, z. B. HL7, verwenden ein Standard-URL-Format für die von ihnen definierten Suchparameter, wie oben im Us Core-Racesuchparameter gezeigt.

* **code:** Der im **Code** gespeicherte Wert wird beim Suchen verwendet. Im obigen Beispiel würden Sie mit suchen, `GET {FHIR_URL}/Patient?race=<code>` um alle Patienten eines bestimmten Races zu erhalten. Der Code muss für die Ressourcen eindeutig sein, für die der Suchparameter gilt.

* **base:** Beschreibt, für welche Ressourcen der Suchparameter gilt. Wenn der Suchparameter für alle Ressourcen gilt, können Sie `Resource` verwenden. Andernfalls können Sie alle relevanten Ressourcen auflisten.
 
* **type:** Beschreibt den Datentyp für den Suchparameter. Der Typ ist durch die Unterstützung für den FHIR-Dienst beschränkt. Dies bedeutet, dass Sie keinen Suchparameter vom Typ Special oder einen [zusammengesetzten Suchparameter](overview-of-search.md) definieren können, es sei denn, es handelt sich um eine unterstützte Kombination.

* **expression:** Beschreibt, wie der Wert für die Suche berechnet wird. Wenn Sie einen Suchparameter beschreiben, müssen Sie den Ausdruck einschließen, auch wenn er für die Spezifikation nicht erforderlich ist. Dies liegt daran, dass Sie entweder den Ausdruck oder die xpath-Syntax benötigen und der FHIR-Dienst die xpath-Syntax ignoriert.

## <a name="test-search-parameters"></a>Testsuchparameter

Obwohl Sie die Suchparameter erst in der Produktion verwenden können, wenn Sie einen Neuindizierungsauftrag ausführen, gibt es einige Möglichkeiten, Ihre Suchparameter zu testen, bevor Sie die gesamte Datenbank neu indizieren. 

Zunächst können Sie Den neuen Suchparameter testen, um zu sehen, welche Werte zurückgegeben werden. Wenn Sie den folgenden Befehl für eine bestimmte Ressourceninstanz ausführen (durch Eingabe ihrer ID), erhalten Sie eine Liste der Wertpaare mit dem Namen des Suchparameters und dem gespeicherten Wert. Dies schließt alle Suchparameter für die Ressource ein, und Sie können durchblättern, um den von Ihnen erstellten Suchparameter zu finden. Das Ausführen dieses Befehls ändert kein Verhalten in Ihrem FHIR-Dienst. 

```rest
GET https://{{FHIR_URL}}/{{RESOURCE}}/{{RESOUCE_ID}}/$reindex

```
So suchen Sie beispielsweise nach allen Suchparametern für einen Patienten:

```rest
GET https://{{FHIR_URL}}/Patient/{{PATIENT_ID}}/$reindex

```

Das Ergebnis sieht so aus:

```json
{
  "resourceType": "Parameters",
  "id": "8be24e78-b333-49da-a861-523491c3437a",
  "meta": {
    "versionId": "1"
  },
  "parameter": [
    {
      "name": "deceased",
      "valueString": "http://hl7.org/fhir/special-values|false"
    },
    {
      "name": "language",
      "valueString": "urn:ietf:bcp:47|en-US"
    },
    {
      "name": "race",
      "valueString": "2028-9"
    },
...
```
Sobald Sie sehen, dass der Suchparameter wie erwartet angezeigt wird, können Sie eine einzelne Ressource neu indizieren, um die Suche mit dem -Element zu testen. Zunächst werden Sie eine einzelne Ressource neu indizieren:

```rest
POST https://{{FHIR_URL}/{{RESOURCE}}/{{RESOURCE_ID}}/$reindex
```

Durch Ausführen dieses Parameters werden die Indizes für alle Suchparameter für die jeweilige Ressource festgelegt, die Sie für diesen Ressourcentyp definiert haben. Dadurch wird der FHIR-Dienst aktualisiert. Jetzt können Sie den Header "Use partial indices" (Partielle Indizes verwenden) auf "true" festlegen. Dies bedeutet, dass Ergebnisse zurückgegeben werden, bei denen einer der Ressourcen der Suchparameter indiziert ist, auch wenn nicht alle Ressourcen dieses Typs indiziert wurden. 

Wenn Sie mit unserem obigen Beispiel fortfahren, können Sie einen Patienten indiziere, um das US Core Race zu `SearchParameter` aktivieren:

```rest
POST https://{{FHIR_URL}/Patient/{{PATIENT_ID}}/$reindex
```

Suchen Sie dann nach Patienten mit einem bestimmten Race:

```rest
GET https://{{FHIR_URL}}/Patient?race=2028-9
x-ms-use-partial-indices: true
```

Nachdem Sie getestet haben und zufrieden sind, dass Ihr Suchparameter wie erwartet funktioniert, führen Sie ihren Neuindizierungsauftrag aus, oder planen Sie diesen, damit die Suchparameter im FHIR-Dienst für Anwendungsfälle in der Produktion verwendet werden können.

## <a name="update-a-search-parameter"></a>Aktualisieren eines Suchparameters

Verwenden Sie zum Aktualisieren eines Suchparameters , `PUT` um eine neue Version des Suchparameters zu erstellen. Sie müssen im `SearchParameter ID` `id` -Element des Anforderungstexts `PUT` und im Aufruf `PUT` einschließen.

> [!NOTE]
> Wenn Sie die ID für Ihren Suchparameter nicht kennen, können Sie danach suchen. Wenn Sie `GET {{FHIR_URL}}/SearchParameter` verwenden, werden alle benutzerdefinierten Suchparameter zurückgegeben, und Sie können durch den Suchparameter scrollen, um den benötigten Suchparameter zu finden. Sie können die Suche auch anhand des Namens einschränken. Im folgenden Beispiel können Sie mit nach dem Namen `USCoreRace: GET {{FHIR_URL}}/SearchParameter?name=USCoreRace` suchen.

```rest
PUT {{FHIR_ULR}}/SearchParameter/{SearchParameter ID}

{
  "resourceType" : "SearchParameter",
  "id" : "SearchParameter ID",
  "url" : "http://hl7.org/fhir/us/core/SearchParameter/us-core-race",
  "version" : "3.1.1",
  "name" : "USCoreRace",
  "status" : "active",
  "date" : "2019-05-21",
  "publisher" : "US Realm Steering Committee",
  "contact" : [
    {
      "telecom" : [
        {
          "system" : "other",
          "value" : "http://www.healthit.gov/"
        }
      ]
    }
  ],
  "description" : "New Description!",
  "jurisdiction" : [
    {
      "coding" : [
        {
          "system" : "urn:iso:std:iso:3166",
          "code" : "US",
          "display" : "United States of America"
        }
      ]
    }
  ],
  "code" : "race",
  "base" : [
    "Patient"
  ],
  "type" : "token",
  "expression" : "Patient.extension.where(url = 'http://hl7.org/fhir/us/core/StructureDefinition/us-core-race').extension.value.code"
}

```

Das Ergebnis ist ein aktualisierter `SearchParameter` , und die Version wird inkrementiert.

> [!Warning]
> Seien Sie vorsichtig, wenn Sie SearchParameters aktualisieren, die bereits in Ihrer Datenbank indiziert wurden. Das Ändern des Verhaltens eines vorhandenen SearchParameter kann Auswirkungen auf das erwartete Verhalten haben. Es wird empfohlen, sofort einen Neuindizierungsauftrag auszuführen.

## <a name="delete-a-search-parameter"></a>Löschen eines Suchparameters

Wenn Sie einen Suchparameter löschen müssen, verwenden Sie Folgendes:

```rest
Delete {{FHIR_URL}}/SearchParameter/{SearchParameter ID}
```

> [!Warning]
> Seien Sie vorsichtig, wenn Sie SearchParameters löschen, die bereits in Ihrer Datenbank indiziert wurden. Das Ändern des Verhaltens eines vorhandenen SearchParameter kann Auswirkungen auf das erwartete Verhalten haben. Es wird empfohlen, sofort einen Neuindizierungsauftrag auszuführen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie einen Suchparameter erstellen. Als Nächstes erfahren Sie, wie Sie Ihren FHIR-Dienst neu indizieren.

>[!div class="nextstepaction"]
>[Ausführen eines Auftrags zum Neuindizieren](how-to-run-a-reindex.md)
