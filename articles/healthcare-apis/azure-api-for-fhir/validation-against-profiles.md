---
title: $validate von FHIR-Ressourcen für Profile Azure API for FHIR
description: $validate von FHIR-Ressourcen für Profile
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 06/07/2021
ms.author: ginle
ms.openlocfilehash: 72fc5ab9c906453f88b510520b40fd03d41aa2ac
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339980"
---
# <a name="how-to-validate-fhir-resources-against-profiles-in-azure-api-for-fhir"></a>Überprüfen von FHIR-Ressourcen anhand von Profilen in Azure API for FHIR

HL7 FHIR definiert eine standardmäßige und interoperable Möglichkeit zum Speichern und Austauschen von Gesundheitsdaten. Auch innerhalb der FHIR-Basisspezifikation kann es hilfreich sein, zusätzliche Regeln oder Erweiterungen basierend auf dem Kontext zu definieren, in dem FHIR verwendet wird. Für solche kontextspezifischen Verwendungen von FHIR werden **FHIR-Profile** für die zusätzliche Ebene der Spezifikationen verwendet.

[Das FHIR-Profil](https://www.hl7.org/fhir/profiling.html) beschreibt zusätzlichen Kontext, z. B. Einschränkungen oder Erweiterungen, für eine Ressource, die als dargestellt `StructureDefinition` wird. Der HL7 FHIR-Standard definiert einen Satz von Basisressourcen, und diese Standardbasisressourcen verfügen über generische Definitionen. Mit dem FHIR-Profil können Sie Ressourcendefinitionen mithilfe von Einschränkungen und Erweiterungen ein- und anpassen.

Azure API for FHIR kann Ressourcen mit Profilen überprüfen, um zu überprüfen, ob die Ressourcen den Profilen entsprechen. In diesem Artikel werden die Grundlagen des FHIR-Profils und die Verwendung von zum Überprüfen von Ressourcen für die Profile beim Erstellen und `$validate` Aktualisieren von Ressourcen beschrieben.

## <a name="fhir-profile-the-basics"></a>FHIR-Profil: Grundlagen

Ein Profil legt zusätzlichen Kontext für die Ressource fest, die in der Regel als Ressource dargestellt `StructureDefinition` wird. `StructureDefinition` definiert einen Satz von Regeln für den Inhalt einer Ressource oder eines Datentyps, z. B. welche Felder eine Ressource enthält und welche Werte diese Felder verwenden können. Profile können z. B. die Kardinalität einschränken (z. B. indem die maximale Kardinalität auf 0 festgelegt wird, um das Element auszuschließen), den Inhalt eines Elements auf einen einzelnen festen Wert beschränken oder erforderliche Erweiterungen für die Ressource definieren. Sie kann auch zusätzliche Einschränkungen für ein vorhandenes Profil angeben. Ein `StructureDefinition` wird durch seine kanonische URL identifiziert:

```rest
http://hl7.org/fhir/StructureDefinition/{profile}
```

Geben Sie im `{profile}` Feld den Namen des Profils an.

Zum Beispiel:

- `http://hl7.org/fhir/StructureDefinition/patient-birthPlace` ist ein Basisprofil, das Informationen zur registrierten Geburtsdatum des Patienten erfordert.
- `http://hl7.org/fhir/StructureDefinition/bmi` ist ein weiteres Basisprofil, das definiert, wie BMI-Beobachtungen (Body Mass Index) repräsentiert werden.
- `http://hl7.org/fhir/us/core/StructureDefinition/us-core-allergyintolerance` ist ein US Core-Profil, das die Mindesterwartungen für ressourcen, die einem Patienten zugeordnet sind, und obligatorische Felder wie Erweiterungen und `AllergyIntolerance` Wertgruppen identifiziert.

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

Es gibt zwei Arten von Profilen: Basisprofil und benutzerdefiniertes Profil. Ein Basisprofil ist eine Basis, der eine Ressource entsprechen muss, und wurde von Basisressourcen wie `StructureDefinition` oder `Patient` `Observation` definiert. Ein BMI-Profil (Body Mass Index) würde beispielsweise `Observation` wie im folgenden Beispiel beginnen:

```json
{
  "resourceType" : "StructureDefinition",
  "id" : "bmi",
...
}
```

Ein benutzerdefiniertes Profil ist eine Reihe zusätzlich zu einem Basisprofil zusätzlicher Einschränkungen, die Ressourcenparameter einschränken oder hinzufügen, die nicht Teil der Basisspezifikation sind. Ein benutzerdefiniertes Profil ist nützlich, da Sie Ihre eigenen Ressourcendefinitionen anpassen können, indem Sie die Einschränkungen und Erweiterungen für die vorhandene Basisressource angeben. Beispielsweise können Sie ein Profil erstellen, das Ressourceninstanzen basierend auf Geschlecht zeigt. In diesem Fall würden Sie ein benutzerdefiniertes Profil zusätzlich zu einem vorhandenen Profil mit Profil `AllergyIntolerance` `Patient` `Patient` `AllergyIntolerance` erstellen.

> [!NOTE]
> Benutzerdefinierte Profile müssen auf der Basisressource aufbauen und dürfen keinen Konflikt mit der Basisressource haben. Wenn ein Element beispielsweise die Kardinalität 1..1 auf hat, kann es vom benutzerdefinierten Profil nicht optional werden.

Benutzerdefinierte Profile werden auch in verschiedenen Implementierungshandbüchern angegeben. Einige allgemeine Implementierungsleitfäden sind:

|Name |URL
|---- |----
Us Core |<https://www.hl7.org/fhir/us/core/>
CARIN Blue Button |<http://hl7.org/fhir/us/carin-bb/>
Da Payer Data Exchange |<http://hl7.org/fhir/us/davinci-pdex/>
Argonaut |<http://www.fhir.org/guides/argonaut/pd/>

## <a name="accessing-profiles-and-storing-profiles"></a>Zugreifen auf Profile und Speichern von Profilen

### <a name="storing-profiles"></a>Speichern von Profilen

Zum Speichern von Profilen auf dem Server können Sie eine Anforderung `POST` ausführen:

```rest
POST http://<your FHIR service base URL>/{Resource}
```

In dem das Feld durch ersetzt wird und Ihre Ressource dem Server im - oder `{Resource}` `StructureDefinition` `StructureDefinition` `POST` -Format `JSON` angezeigt `XML` wird. Wenn Sie z. B. das Profil speichern `us-core-allergyintolerance` möchten, gehen Sie wie hier vor:

```rest
POST http://my-fhir-server.azurewebsites.net/StructureDefinition?url=http://hl7.org/fhir/us/core/StructureDefinition/us-core-allergyintolerance
```

Hier wird das Profil "US Core-1011" gespeichert und abgerufen:

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

Die meisten Profile haben den Ressourcentyp , können aber auch die Typen und haben, bei denen es `StructureDefinition` `ValueSet` sich um `CodeSystem` [Terminologieressourcen](http://hl7.org/fhir/terminologies.html) handelt. Wenn Sie beispielsweise ein Profil in einem JSON-Formular erstellen, gibt der Server das gespeicherte Profil mit dem für das Profil zugewiesenen zurück, genau wie `POST` `ValueSet` bei `id` `StructureDefinition` . Im Folgenden finden Sie ein Beispiel, das Sie beim Hochladen eines Profils für den Bedingungsschweregrad erhalten, das die Kriterien für eine Bedingungs-/Diagnoseschweregrad-Bewertung angibt: [](https://www.hl7.org/fhir/valueset-condition-severity.html)

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

Sie können sehen, dass ein ist, und der für das Profil auch angibt, dass `resourceType` `ValueSet` `url` dies ein Typ `ValueSet` ist: `"http://hl7.org/fhir/ValueSet/condition-severity"` .

### <a name="viewing-profiles"></a>Anzeigen von Profilen

Sie können mithilfe einer Anforderung auf Ihre vorhandenen benutzerdefinierten Profile auf dem Server `GET` zugreifen. Auf alle gültigen Profile, z. B. profile mit gültigen kanonischen URLs in Implementierungshandbüchern, sollte durch Abfragen von zugegriffen werden können:

```rest
GET http://<your FHIR service base URL>/StructureDefinition?url={canonicalUrl} 
```

Dabei wird das `{canonicalUrl}` Feld durch die kanonische URL Ihres Profils ersetzt.

Wenn Sie beispielsweise das US `Goal` Core-Ressourcenprofil anzeigen möchten:

```rest
GET http://my-fhir-server.azurewebsites.net/StructureDefinition?url=http://hl7.org/fhir/us/core/StructureDefinition/us-core-goal
```

Dadurch wird die Ressource `StructureDefinition` für das Profil "US Core Goal" (US-Kernziel) zurück, die wie hier angezeigt wird:

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

Unser FHIR-Server gibt keine Instanzen für die Basisprofile zurück, sie sind jedoch problemlos auf der `StructureDefinition` HL7-Website zu finden, z. B.:

- `http://hl7.org/fhir/Observation.profile.json.html`
- `http://hl7.org/fhir/Patient.profile.json.html`


### <a name="profiles-in-the-capability-statement"></a>Profile in der Capability-Anweisung

Listet alle möglichen Verhaltensweisen Ihres FHIR-Servers auf, die als Anweisung der Serverfunktionalität verwendet werden sollen, z. B. `Capability Statement` Strukturdefinitionen und Wertsätze. Azure API for FHIR aktualisiert die Capability-Anweisung mit Informationen zu den hochgeladenen und gespeicherten Profilen in den folgenden Formen:

- `CapabilityStatement.rest.resource.profile`
- `CapabilityStatement.rest.resource.supportedProfile`

Diese zeigen alle Spezifikationen für das Profil an, die die allgemeine Unterstützung für die Ressource beschreiben, einschließlich einschränkungen der Kardinalität, Bindungen, Erweiterungen oder anderer Einschränkungen. Wenn Sie also ein Profil in Form von und die Ressourcenmetadaten verwenden, um die vollständige Funktions-Anweisung anzuzeigen, werden neben dem Parameter alle Details des profils angezeigt, das Sie `POST` `StructureDefinition` hochgeladen `GET` `supportedProfiles` haben.

Wenn Sie z. B. ein `POST` US Core Patient-Profil erstellen, das wie das folgende beginnt:

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

Senden Sie eine `GET` Anforderung für : `metadata`

```rest
GET http://<your FHIR service base URL>/metadata
```

Sie werden mit einem zurückgegeben, der die folgenden Informationen zum US Core Patient-Profil enthält, `CapabilityStatement` das Sie auf Ihren FHIR-Server hochgeladen haben:

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

## <a name="validating-resources-against-the-profiles"></a>Überprüfen von Ressourcen für die Profile

FHIR-Ressourcen wie oder können ihre Konformität `Patient` `Observation` mit bestimmten Profilen ausdrücken. Dadurch kann unser FHIR-Server **bestimmte Ressourcen** anhand der zugeordneten Profile oder der angegebenen Profile überprüfen. Das Überprüfen einer Ressource mit Profilen bedeutet, zu überprüfen, ob Ihre Ressource den Profilen entspricht, einschließlich der in oder in einem `Resource.meta.profile` Implementierungshandbuch aufgeführten Spezifikationen.

Es gibt zwei Möglichkeiten, ihre Ressource zu überprüfen. Zunächst können Sie den Vorgang `$validate` für eine Ressource verwenden, die sich bereits auf dem FHIR-Server befindet. Zweitens können Sie es als Teil einer Ressource `POST` oder eines Vorgangs auf `Update` den Server `Create` übertragen. In beiden Fällen können Sie über Ihre FHIR-Serverkonfiguration entscheiden, was zu tun ist, wenn die Ressource nicht dem gewünschten Profil entspricht.

### <a name="using-validate"></a>Verwenden $validate

Der Vorgang überprüft, ob das angegebene Profil gültig ist und ob die Ressource `$validate` dem angegebenen Profil entspricht. Wie in den [HL7 FHIR-Spezifikationen erwähnt,](https://www.hl7.org/fhir/resource-operation-validate.html)können Sie auch für `mode` `$validate` angeben, z. B. `create` und `update` :

- `create`: Der Server überprüft, ob der Profilinhalt von den vorhandenen Ressourcen eindeutig ist und ob es akzeptabel ist, als neue Ressource erstellt zu werden.
- `update`: überprüft, ob das Profil ein Update für die vorhandene vorhandene Ressource ist (z. B. dass keine Änderungen an den unveränderlichen Feldern vorgenommen werden).

Der Server gibt immer als `OperationOutcome` Überprüfungsergebnisse zurück.

#### <a name="validating-an-existing-resource"></a>Überprüfen einer vorhandenen Ressource

Um eine vorhandene Ressource zu überprüfen, verwenden Sie `$validate` in einer `GET` Anforderung:

```rest
GET http://<your FHIR service base URL>/{resource}/{resource ID}/$validate
```

Zum Beispiel:

```rest
GET http://my-fhir-server.azurewebsites.net/Patient/a6e11662-def8-4dde-9ebc-4429e68d130e/$validate
```

Im obigen Beispiel würden Sie die vorhandene Ressource `Patient` `a6e11662-def8-4dde-9ebc-4429e68d130e` überprüfen. Wenn sie gültig ist, erhalten Sie eine `OperationOutcome` wie die folgende:

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

Wenn die Ressource ungültig ist, erhalten Sie einen Fehlercode und eine Fehlermeldung mit Details dazu, warum die Ressource ungültig ist. Ein `4xx` - oder -Fehler bedeutet, dass die Überprüfung selbst nicht durchgeführt werden konnte, und es ist unbekannt, ob die Ressource `5xx` gültig ist oder nicht. Ein `OperationOutcome` Beispiel, das mit Fehlermeldungen zurückgegeben wird, könnte wie folgt aussehen:

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

In diesem Beispiel oben entspricht die Ressource nicht dem bereitgestellten Profil, das einen Patientenbezeichnerwert und `Patient` ein Geschlecht erfordert.

Wenn Sie ein Profil als Parameter angeben möchten, können Sie die kanonische URL für das Profil angeben, anhand derer überprüft werden soll, z. B. das folgende Beispiel für das HL7-Basisprofil für `heartrate` :

```rest
GET http://my-fhir-server.azurewebsites.net/Observation/12345678/$validate?profile=http://hl7.org/fhir/StructureDefinition/heartrate
```

#### <a name="validating-a-new-resource"></a>Überprüfen einer neuen Ressource

Wenn Sie eine neue Ressource überprüfen möchten, die Sie auf den Server hochladen, können Sie eine Anforderung `POST` ausführen:

```rest
POST http://<your FHIR service base URL>/{Resource}/$validate
```

Zum Beispiel:

```rest
POST http://my-fhir-server.azurewebsites.net/Patient/$validate 
```

Diese Anforderung erstellt die neue Ressource, die Sie in der Anforderungsnutzlast angeben, unabhängig davon, ob sie ein JSON- oder XML-Format hat, und überprüft die hochgeladene Ressource. Anschließend wird als Ergebnis der Überprüfung für die neue Ressource eine `OperationOutcome` -Ressource zurückgeben.

### <a name="validate-on-resource-create-or-resource-update"></a>Überprüfen der Ressource CREATE oder resource UPDATE

Sie können auswählen, wann Sie Ihre Ressource überprüfen möchten, z. B. unter Ressource ERSTELLEN oder AKTUALISIEREN. Sie können dies in der Serverkonfigurationseinstellung unter `CoreFeatures` angeben:

```json
{
   "FhirServer": {
      "CoreFeatures": {
            "ProfileValidationOnCreate": true,
            "ProfileValidationOnUpdate": false
        }
}
```

Wenn die Ressource dem bereitgestellten entspricht und das Profil im System vorhanden ist, wird der Server entsprechend der `Resource.meta.profile` oben genannten Konfigurationseinstellung agieren. Wenn das bereitgestellte Profil auf dem Server nicht vorhanden ist, wird die Validierungsanforderung ignoriert und in `Resource.meta.profile` gelassen.
Die Validierung ist in der Regel ein aufwendiger Vorgang, daher wird sie in der Regel nur auf Testservern oder auf einer kleinen Teilmenge von Ressourcen ausgeführt. Aus diesem Grund ist es wichtig, diese Möglichkeiten zum Aktivieren oder Deaktivieren der Validierung auf serverseitiger Seite zu verwenden. Wenn die Serverkonfiguration angibt, dass die Überprüfung für die Ressource Erstellen/Aktualisieren deaktivieren soll, kann der Benutzer das Verhalten überschreiben, indem er es in der `header` Create/Update-Anforderung angibt:

```rest
x-ms-profile-validation: true
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie mehr über FHIR-Profile erfahren und erfahren, wie Sie Ressourcen anhand von Profilen mithilfe von $validate. Weitere Informationen zu Azure API for FHIR unterstützten Features finden Sie hier:

>[!div class="nextstepaction"]
>[Von FHIR unterstützte Features](fhir-features-supported.md)
