---
title: $validate von FHIR-Ressourcen für Profile im FHIR-Dienst in Azure Healthcare-APIs
description: $validate von FHIR-Ressourcen für Profile im FHIR-Dienst
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/03/2021
ms.author: cavoeg
ms.openlocfilehash: b52389b6007c436614840a9bad568a0e81cf7fa2
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122779568"
---
# <a name="how-to-validate-fhir-resources-against-profiles"></a>Überprüfen von FHIR-Ressourcen anhand von Profilen

> [!IMPORTANT]
> Azure Healthcare-APIs befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

HL7 FHIR definiert eine standardmäßige und interoperable Methode zum Speichern und Austauschen von Gesundheitsdaten. Selbst innerhalb der FHIR-Basisspezifikation kann es hilfreich sein, zusätzliche Regeln oder Erweiterungen basierend auf dem Kontext zu definieren, in dem FHIR verwendet wird. Für solche kontextspezifischen Verwendungen von FHIR werden **FHIR-Profile** für die zusätzliche Spezifikationsebene verwendet.

[Das FHIR-Profil](https://www.hl7.org/fhir/profiling.html) beschreibt zusätzlichen Kontext, z. B. Einschränkungen oder Erweiterungen, für eine Ressource, die als dargestellt `StructureDefinition` wird. Der HL7 FHIR-Standard definiert einen Satz von Basisressourcen, und diese Standardbasisressourcen verfügen über generische Definitionen. Mithilfe des FHIR-Profils können Sie Ressourcendefinitionen mithilfe von Einschränkungen und Erweiterungen eingrenzen und anpassen.

Der FHIR-Dienst in den Azure Healthcare-APIs (auch als FHIR-Dienst bezeichnet) ermöglicht die Überprüfung von Ressourcen anhand von Profilen, um festzustellen, ob die Ressourcen den Profilen entsprechen. In diesem Artikel werden die Grundlagen des FHIR-Profils und die Verwendung von zum Überprüfen von Ressourcen anhand der Profile beim Erstellen und Aktualisieren von `$validate` Ressourcen beschrieben.

## <a name="fhir-profile-the-basics"></a>FHIR-Profil: Grundlagen

Ein Profil legt zusätzlichen Kontext für die Ressource fest, der normalerweise als Ressource dargestellt `StructureDefinition` wird. `StructureDefinition` definiert einen Satz von Regeln für den Inhalt einer Ressource oder eines Datentyps, z. B. welche Felder eine Ressource enthält und welche Werte diese Felder annehmen können. Profile können beispielsweise die Kardinalität einschränken (z. B. das Festlegen der maximalen Kardinalität auf 0, um das Element auszuschließen), den Inhalt eines Elements auf einen einzelnen festen Wert beschränken oder erforderliche Erweiterungen für die Ressource definieren. Sie kann auch zusätzliche Einschränkungen für ein vorhandenes Profil angeben. Eine `StructureDefinition` wird durch ihre kanonische URL identifiziert:

```rest
http://hl7.org/fhir/StructureDefinition/{profile}
```

Geben Sie im `{profile}` Feld den Namen des Profils an.

Zum Beispiel:

- `http://hl7.org/fhir/StructureDefinition/patient-birthPlace` ist ein Basisprofil, das Informationen zur registrierten Adresse der Geburtsdatum des Patienten erfordert.
- `http://hl7.org/fhir/StructureDefinition/bmi` ist ein weiteres Basisprofil, das definiert, wie BMI-Beobachtungen (Body Mass Index) dargestellt werden.
- `http://hl7.org/fhir/us/core/StructureDefinition/us-core-allergyintolerance` ist ein US Core-Profil, das mindeste Erwartungen an die einem Patienten zugeordnete Ressource festlegt `AllergyIntolerance` und obligatorische Felder wie Erweiterungen und Wertsätze identifiziert.

Wenn eine Ressource einem Profil entspricht, wird ihr Profil in einer Ressource im Feld `profile` angegeben.

```json
{
  "resourceType" : "Patient",
  "id" : "ExamplePatient1",
  "meta" : {
    "lastUpdated" : "2020-10-30T09:48:01.8512764-04:00",
    "source" : "Organization/PayerOrganizationExample1",
    "profile" : [
      "http://hl7.org/fhir/us/carin-bb/StructureDefinition/C4BB-Patient"
    ]
  },
```

### <a name="base-profile-and-custom-profile"></a>Basisprofil und benutzerdefiniertes Profil

Es gibt zwei Arten von Profilen: Basisprofil und benutzerdefiniertes Profil. Ein Basisprofil ist eine `StructureDefinition` Basis, der eine Ressource entsprechen muss, und wurde durch Basisressourcen wie `Patient` oder `Observation` definiert. Beispielsweise würde ein BMI-Profil (Body Mass Index) `Observation` wie folgt beginnen:

```json
{
  "resourceType" : "StructureDefinition",
  "id" : "bmi",
...
}
```

Ein benutzerdefiniertes Profil ist ein Satz zusätzlich zu einem Basisprofil, das Ressourcenparameter einschränkt oder hinzufügt, die nicht Teil der Basisspezifikation sind. Ein benutzerdefiniertes Profil ist nützlich, da Sie Ihre eigenen Ressourcendefinitionen anpassen können, indem Sie die Einschränkungen und Erweiterungen für die vorhandene Basisressource angeben. Sie können z. B. ein Profil erstellen, das `AllergyIntolerance` Ressourceninstanzen basierend auf `Patient` Geschlecht anzeigt. In diesem Fall erstellen Sie ein benutzerdefiniertes Profil zusätzlich zu einem vorhandenen `Patient` Profil mit `AllergyIntolerance` Profil.

> [!NOTE]
> Benutzerdefinierte Profile müssen auf der Basisressource aufbauen und können keinen Konflikt mit der Basisressource verursachen. Wenn ein Element beispielsweise eine Kardinalität von 1..1 auf hat, kann es vom benutzerdefinierten Profil nicht optional sein.

Benutzerdefinierte Profile werden auch in verschiedenen Implementierungshandbüchern angegeben. Einige gängige Implementierungshandbücher sind:

|Name |URL
|---- |----
Us Core |<https://www.hl7.org/fhir/us/core/>
CARIN Blue Button |<http://hl7.org/fhir/us/carin-bb/>
Da Payer Data Exchange |<http://hl7.org/fhir/us/davinci-pdex/>
Argonaut |<http://www.fhir.org/guides/argonaut/pd/>

## <a name="accessing-profiles-and-storing-profiles"></a>Zugreifen auf Profile und Speichern von Profilen

### <a name="storing-profiles"></a>Speichern von Profilen

Zum Speichern von Profilen auf dem Server können Sie eine `POST` Anforderung durchführen:

```rest
POST http://<your FHIR service base URL>/StructureDefinition
```

Wenn Sie beispielsweise ein Profil speichern `us-core-allergyintolerance` möchten, gehen Sie folgendermaßen vor:

```rest
POST https://myworkspace-myfhirserver.fhir.azurehealthcareapis.com/StructureDefinition?url=http://hl7.org/fhir/us/core/StructureDefinition/us-core-allergyintolerance
```

Hier würde das Profil "US Core All jetzt 2016" gespeichert und abgerufen werden:

```json
{
    "resourceType" : "StructureDefinition",
    "id" : "us-core-allergyintolerance",
    "text" : {
        "status" : "extensions"
    },
    "url" : "http://hl7.org/fhir/us/core/StructureDefinition/us-core-allergyintolerance",
    "version" : "3.1.1",
    "name" : "USCoreAllergyIntolerance",
    "title" : "US  Core AllergyIntolerance Profile",
    "status" : "active",
    "experimental" : false,
    "date" : "2020-06-29",
        "publisher" : "HL7 US Realm Steering Committee",
    "contact" : [
    {
      "telecom" : [
        {
          "system" : "url",
          "value" : "http://www.healthit.gov"
        }
      ]
    }
  ],
    "description" : "Defines constraints and extensions on the AllergyIntolerance resource for the minimal set of data to query and retrieve allergy information.",

...
```

Die meisten Profile verfügen über den Ressourcentyp `StructureDefinition` , können aber auch die Typen und aufweisen, bei denen es sich um `ValueSet` `CodeSystem` [Terminologieressourcen](http://hl7.org/fhir/terminologies.html) handelt. Wenn Sie beispielsweise `POST` ein Profil in einem `ValueSet` JSON-Formular verwenden, gibt der Server das gespeicherte Profil mit dem für das Profil zugewiesenen `id` zurück, genau wie bei `StructureDefinition` . Im Folgenden finden Sie ein Beispiel, das Sie erhalten, wenn Sie ein Profil für den Schweregrad der [Bedingung](https://www.hl7.org/fhir/valueset-condition-severity.html) hochladen, das die Kriterien für die Bewertung des Schweregrads einer Bedingung/Diagnose angibt:

```json
{
    "resourceType": "ValueSet",
    "id": "35ab90e5-c75d-45ca-aa10-748fefaca7ee",
    "meta": {
        "versionId": "1",
        "lastUpdated": "2021-05-07T21:34:28.781+00:00",
        "profile": [
            "http://hl7.org/fhir/StructureDefinition/shareablevalueset"
        ]
    },
    "text": {
        "status": "generated"
    },
    "extension": [
        {
            "url": "http://hl7.org/fhir/StructureDefinition/structuredefinition-wg",
            "valueCode": "pc"
        }
    ],
    "url": "http://hl7.org/fhir/ValueSet/condition-severity",
    "identifier": [
        {
            "system": "urn:ietf:rfc:3986",
            "value": "urn:oid:2.16.840.1.113883.4.642.3.168"
        }
    ],
    "version": "4.0.1",
    "name": "Condition/DiagnosisSeverity",
    "title": "Condition/Diagnosis Severity",
    "status": "draft",
    "experimental": false,
    "date": "2019-11-01T09:29:23+11:00",
    "publisher": "FHIR Project team",
...
```

Sie sehen, dass `resourceType` ein `ValueSet` ist, und der `url` für das Profil gibt auch an, dass dies ein Typ `ValueSet` ist: `"http://hl7.org/fhir/ValueSet/condition-severity"` .

### <a name="viewing-profiles"></a>Anzeigen von Profilen

Sie können mithilfe einer Anforderung auf Ihre vorhandenen benutzerdefinierten Profile auf dem Server `GET` zugreifen. Auf alle gültigen Profile, z. B. die Profile mit gültigen kanonischen URLs in Implementierungshandbüchern, sollte durch Abfragen zugegriffen werden können:

```rest
GET http://<your FHIR service base URL>/StructureDefinition?url={canonicalUrl} 
```

Hier wird das Feld `{canonicalUrl}` durch die kanonische URL Ihres Profils ersetzt.

Wenn Sie z. B. das US Core-Ressourcenprofil anzeigen `Goal` möchten:

```rest
GET https://myworkspace-myfhirserver.fhir.azurehealthcareapis.com/StructureDefinition?url=http://hl7.org/fhir/us/core/StructureDefinition/us-core-goal
```

Dadurch wird die Ressource für das `StructureDefinition` US-Kernzielprofil zurückgegeben, die wie folgt beginnt:

```json
{
  "resourceType" : "StructureDefinition",
  "id" : "us-core-goal",
  "url" : "http://hl7.org/fhir/us/core/StructureDefinition/us-core-goal",
  "version" : "3.1.1",
  "name" : "USCoreGoalProfile",
  "title" : "US Core Goal Profile",
  "status" : "active",
  "experimental" : false,
  "date" : "2020-07-21",
  "publisher" : "HL7 US Realm Steering Committee",
  "contact" : [
    {
      "telecom" : [
        {
          "system" : "url",
          "value" : "http://www.healthit.gov"
        }
      ]
    }
  ],
  "description" : "Defines constraints and extensions on the Goal resource for the minimal set of data to query and retrieve a patient's goal(s).",
...
```

Der FHIR-Dienst gibt keine `StructureDefinition` Instanzen für die Basisprofile zurück, sie sind jedoch leicht auf der HL7-Website zu finden, z. B.:

- `http://hl7.org/fhir/Observation.profile.json.html`
- `http://hl7.org/fhir/Patient.profile.json.html`


### <a name="profiles-in-the-capability-statement"></a>Profile in der Capability-Anweisung

`Capability Statement`Listet alle möglichen Verhaltensweisen Ihres FHIR-Diensts auf, die als Anweisung der Serverfunktionalität verwendet werden sollen, z. B. Strukturdefinitionen und Wertsätze. Der FHIR-Dienst aktualisiert die Capability-Anweisung mit Informationen zu den hochgeladenen und gespeicherten Profilen in form von:

- `CapabilityStatement.rest.resource.profile`
- `CapabilityStatement.rest.resource.supportedProfile`

Diese zeigen die gesamte Spezifikation für das Profil, das die allgemeine Unterstützung für die Ressource beschreibt, einschließlich einschränkungen für Kardinalität, Bindungen, Erweiterungen oder andere Einschränkungen. Wenn Sie also `POST` ein Profil in Form von und die `StructureDefinition` `GET` Ressourcenmetadaten zum Anzeigen der vollständigen Funktionsanweisung verwenden, werden neben dem Parameter alle Details des profils angezeigt, `supportedProfiles` das Sie hochgeladen haben.

Wenn Sie z. B. `POST` ein US Core Patient-Profil haben, das wie folgt beginnt:

```json
{
  "resourceType": "StructureDefinition",
  "id": "us-core-patient",
  "url": "http://hl7.org/fhir/us/core/StructureDefinition/us-core-patient",
  "version": "3.1.1",
  "name": "USCorePatientProfile",
  "title": "US Core Patient Profile",
  "status": "active",
  "experimental": false,
  "date": "2020-06-27",
  "publisher": "HL7 US Realm Steering Committee",
...
```

Und senden Sie eine `GET` Anforderung für Ihre `metadata` :

```rest
GET http://<your FHIR service base URL>/metadata
```

Sie werden mit einem `CapabilityStatement` zurückgegeben, das die folgenden Informationen zum PROFIL "US Core Patient" enthält, das Sie auf Ihren FHIR-Server hochgeladen haben:

```json
...
{
    "type": "Patient",
    "profile": "http://hl7.org/fhir/StructureDefinition/Patient",
    "supportedProfile":[
        "http://hl7.org/fhir/us/core/StructureDefinition/us-core-patient"
    ],
...
```

## <a name="validating-resources-against-the-profiles"></a>Überprüfen von Ressourcen anhand der Profile

FHIR-Ressourcen wie `Patient` oder können ihre Konformität mit `Observation` bestimmten Profilen ausdrücken. Dadurch kann der FHIR-Dienst bestimmte Ressourcen anhand der zugeordneten Profile oder der angegebenen Profile **überprüfen.** Das Überprüfen einer Ressource anhand von Profilen bedeutet, zu überprüfen, ob Ihre Ressource den Profilen entspricht, einschließlich der in oder in einem Implementierungshandbuch aufgeführten `Resource.meta.profile` Spezifikationen.

Es gibt zwei Möglichkeiten, Ihre Ressource zu überprüfen. Zunächst können Sie `$validate` den Vorgang für eine Ressource verwenden, die sich bereits im FHIR-Dienst befindet. Zweitens können Sie `POST` es als Teil einer Ressource oder eines Vorgangs an den Server `Update` `Create` übertragen. In beiden Fällen können Sie über Ihre FHIR-Dienstkonfiguration entscheiden, was zu tun ist, wenn die Ressource nicht ihrem gewünschten Profil entspricht.

### <a name="using-validate"></a>Verwenden von $validate

Der `$validate` Vorgang überprüft, ob das angegebene Profil gültig ist und ob die Ressource dem angegebenen Profil entspricht. Wie in den [HL7 FHIR-Spezifikationen](https://www.hl7.org/fhir/resource-operation-validate.html)erwähnt, können Sie auch `mode` für `$validate` angeben, z. B. `create` und `update` :

- `create`: Der Server überprüft, ob der Profilinhalt von den vorhandenen Ressourcen eindeutig ist und dass es akzeptabel ist, als neue Ressource erstellt zu werden.
- `update`: überprüft, ob das Profil ein Update für die angegebene vorhandene Ressource ist (z. B. dass keine Änderungen an den unveränderlichen Feldern vorgenommen werden)

Der Server gibt immer als `OperationOutcome` Validierungsergebnisse zurück.

#### <a name="validating-an-existing-resource"></a>Überprüfen einer vorhandenen Ressource

Um eine vorhandene Ressource zu überprüfen, verwenden Sie `$validate` in einer `GET` Anforderung:

```rest
GET http://<your FHIR service base URL>/{resource}/{resource ID}/$validate
```

Zum Beispiel:

```rest
GET https://myworkspace-myfhirserver.fhir.azurehealthcareapis.com/Patient/a6e11662-def8-4dde-9ebc-4429e68d130e/$validate
```

Im obigen Beispiel würden Sie die vorhandene `Patient` Ressource `a6e11662-def8-4dde-9ebc-4429e68d130e` überprüfen. Wenn sie gültig ist, erhalten Sie eine `OperationOutcome` wie die folgende:

```json
{
    "resourceType": "OperationOutcome",
    "issue": [
        {
            "severity": "information",
            "code": "informational",
            "diagnostics": "All OK"
        }
    ]
}
```

Wenn die Ressource ungültig ist, erhalten Sie einen Fehlercode und eine Fehlermeldung mit Details dazu, warum die Ressource ungültig ist. Ein `4xx` - oder `5xx` -Fehler bedeutet, dass die Überprüfung selbst nicht ausgeführt werden konnte, und es ist unbekannt, ob die Ressource gültig ist oder nicht. Ein `OperationOutcome` Beispiel, das mit Fehlermeldungen zurückgegeben wird, könnte wie folgt aussehen:

```json
{
    "resourceType": "OperationOutcome",
    "issue": [
        {
            "severity": "error",
            "code": "invalid",
            "details": {
                "coding": [
                    {
                        "system": "http://hl7.org/fhir/dotnet-api-operation-outcome",
                        "code": "1028"
                    }
                ],
                "text": "Instance count for 'Patient.identifier.value' is 0, which is not within the specified cardinality of 1..1"
            },
            "location": [
                "Patient.identifier[1]"
            ]
        },
        {
            "severity": "error",
            "code": "invalid",
            "details": {
                "coding": [
                    {
                        "system": "http://hl7.org/fhir/dotnet-api-operation-outcome",
                        "code": "1028"
                    }
                ],
                "text": "Instance count for 'Patient.gender' is 0, which is not within the specified cardinality of 1..1"
            },
            "location": [
                "Patient"
            ]
        }
    ]
}
```

In diesem Beispiel oben entsprach die Ressource nicht dem bereitgestellten `Patient` Profil, das einen Patientenbezeichnerwert und ein Geschlecht erforderte.

Wenn Sie ein Profil als Parameter angeben möchten, können Sie die kanonische URL für das Zu überprüfende Profil angeben, z. B. das folgende Beispiel für das HL7-Basisprofil für `heartrate` :

```rest
GET https://myworkspace-myfhirserver.fhir.azurehealthcareapis.com/Observation/12345678/$validate?profile=http://hl7.org/fhir/StructureDefinition/heartrate
```

#### <a name="validating-a-new-resource"></a>Überprüfen einer neuen Ressource

Wenn Sie eine neue Ressource überprüfen möchten, die Sie auf den Server hochladen, können Sie eine `POST` Anforderung durchführen:

```rest
POST http://<your FHIR service base URL>/{Resource}/$validate
```

Zum Beispiel:

```rest
POST https://myworkspace-myfhirserver.fhir.azurehealthcareapis.com/Patient/$validate 
```

Diese Anforderung erstellt die neue Ressource, die Sie in der Anforderungsnutzlast angeben, unabhängig davon, ob sie im JSON- oder XML-Format vorliegt, und überprüft die hochgeladene Ressource. Anschließend wird als Ergebnis der Validierung für die neue Ressource ein `OperationOutcome` zurückgegeben.

### <a name="validate-on-resource-create-or-resource-update"></a>Überprüfen der Ressource CREATE oder resource UPDATE

Sie können auswählen, wann Sie Ihre Ressource überprüfen möchten, z. B. für ressource CREATE oder UPDATE. Sie können dies in der Serverkonfigurationseinstellung unter `CoreFeatures` angeben:

```json
{
   "FhirServer": {
      "CoreFeatures": {
            "ProfileValidationOnCreate": true,
            "ProfileValidationOnUpdate": false
        }
}
```

Wenn die Ressource dem bereitgestellten entspricht `Resource.meta.profile` und das Profil im System vorhanden ist, reagiert der Server entsprechend der oben genannten Konfigurationseinstellung. Wenn das bereitgestellte Profil nicht auf dem Server vorhanden ist, wird die Validierungsanforderung ignoriert und in `Resource.meta.profile` belassen.
Die Validierung ist in der Regel ein aufwendiger Vorgang, daher wird sie in der Regel nur auf Testservern oder auf einer kleinen Teilmenge von Ressourcen ausgeführt. Daher ist es wichtig, diese Möglichkeiten zum Aktivieren oder Deaktivieren der Validierung auf serverseitiger Seite zu verwenden. Wenn die Serverkonfiguration angibt, dass die Überprüfung für ressource Create/Update nicht mehr durchgeführt werden soll, kann der Benutzer das Verhalten überschreiben, indem er es in `header` der Create/Update-Anforderung angibt:

```rest
x-ms-profile-validation: true
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie mehr über FHIR-Profile erfahren und erfahren, wie Sie Ressourcen mithilfe von $validate anhand von Profilen überprüfen. Weitere Informationen zu den anderen unterstützten Features des FHIR-Diensts finden Sie unter:

>[!div class="nextstepaction"]
>[Von FHIR unterstützte Features](fhir-features-supported.md)
