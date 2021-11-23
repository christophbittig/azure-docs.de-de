---
title: JsonPathContentTemplate-Zuordnungen in IoT-Connector-Gerätezuordnungen – Azure Healthcare-APIs
description: In diesem Artikel wird beschrieben, wie Sie JsonPathContentTemplate-Zuordnungen mit IoT-Connector-Gerätezuordnungsvorlagen verwenden.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/22/2021
ms.author: jasteppe
ms.openlocfilehash: 9c8ce02be3301bdfaf8d151ef84e1475edaa6b03
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2021
ms.locfileid: "132937258"
---
# <a name="how-to-use-jsonpathcontenttemplate-mappings"></a>Verwenden von JsonPathContentTemplate-Zuordnungen

> [!IMPORTANT]
> Azure Healthcare-APIs sind derzeit als VORSCHAUversion verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

> [!TIP]
> Sehen Sie sich das [IoMT Connector Data Mapper-Tool zum](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) Bearbeiten, Testen und Behandeln von Problemen bei Der IoT-Connector geräte- und FHIR-Zielzuordnungen an. Exportieren Sie Zuordnungen zum Hochladen in den IoT-Connector im Azure-Portal oder verwenden Sie sie mit der [Open-Source-Version des](https://github.com/microsoft/iomt-fhir) IoT-Connectors.

In diesem Artikel wird beschrieben, wie Sie JsonPathContentTemplate-Zuordnungen mit IoT-Connector-Gerätezuordnungsvorlagen verwenden.

## <a name="jsonpathcontenttemplate"></a>JsonPathContentTemplate

JsonPathContentTemplate ermöglicht das Abgleichen und Extrahieren von Werten aus einer Azure Event Hub-Nachricht mithilfe von JSONPath.

|Eigenschaft|BESCHREIBUNG|Beispiel|
|--------|-----------|-------|
|TypName|Der Typ, der Messungen, die mit der Vorlage übereinstimmen, zugeordnet werden soll.|`heartrate`|
|TypeMatchExpression|Der JSONPath-Ausdruck, der anhand der EventData-Nutzlast ausgewertet wird. Wird ein entsprechendes JToken gefunden, gilt die Vorlage als Übereinstimmung. Alle späteren Ausdrücke werden anhand des extrahierten JToken ausgewertet, das hier übereinstimmen.|`$..[?(@heartRate)]`|
|TimestampExpression|Der JSONPath-Ausdruck, um den Zeitstempelwert für occurrenceTimeUtc der Messung zu extrahieren.|`$.matchedToken.endDate`|
|DeviceIdExpression|Der JSONPath-Ausdruck zum Extrahieren des Gerätebezeichners.|`$.matchedToken.deviceId`|
|PatientIdExpression|*Erforderlich,* wenn identityResolution sich im **Erstellungsmodus** befindet, und *optional,* wenn IdentityResolution sich im **Suchmodus** befindet. Der Ausdruck zum Extrahieren des Patientenbezeichners.|`$.matchedToken.patientId`|
|EncounterIdExpression|*Optional:* Der Ausdruck zum Extrahieren des Bezeichners für die Erkennung.|`$.matchedToken.encounterId`|
|CorrelationIdExpression|*Optional:* Der Ausdruck zum Extrahieren des Korrelationsbezeichners. Diese Ausgabe kann verwendet werden, um Werte in einer einzelnen Beobachtung in den FHIR-Zielzuordnungen zu gruppieren.|`$.matchedToken.correlationId`|
|Values[].ValueName|Der Name, der dem vom nächsten Ausdruck extrahierten Wert zugeordnet werden soll. Wird verwendet, um den gesuchten Wert/die Komponente in der FHIR-Zielzuordnungsvorlage zu binden.|`hr`|
|Values[].ValueExpression|Der JSONPath-Ausdruck zum Extrahieren des gesuchten Werts.|`$.matchedToken.heartRate`|
|Values[].Required|Mit dieser Eigenschaft wird festgelegt, ob der Wert in der Nutzlast vorhanden sein muss. Wenn keine Messung gefunden wird, wird keine Messung generiert, und es wird eine InvalidOperationException erstellt.|`true`|

### <a name="examples"></a>Beispiele

**Heart rate**

*Meldung*

```json
{
    "Body": {
        "heartRate": "78",
        "endDate": "2021-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```

*Vorlage*

```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
**Blood pressure**

*Meldung*

```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87",
        "endDate": "2021-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```

*Vorlage*

```json
{
    "typeName": "bloodpressure",
    "typeMatchExpression": "$..[?(@systolic && @diastolic)]",
    "deviceIdExpression": "$.deviceId",
    "timestampExpression": "$.endDate",
    "values": [
        {
            "required": "true",
            "valueExpression": "$.systolic",
            "valueName": "systolic"
        },
        {
            "required": "true",
            "valueExpression": "$.diastolic",
            "valueName": "diastolic"
        }
    ]
}
```
**Projizieren mehrerer Messungen aus einer einzelnen Nachricht**

*Meldung*

```json
{
    "Body": {
        "heartRate": "78",
        "steps": "2",
        "endDate": "2021-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```

*Vorlage 1*

```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```

*Vorlage 2*

```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "stepcount",
        "typeMatchExpression": "$..[?(@steps)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.steps",
                "valueName": "steps"
            }
        ]
    }
}
```

**Projizieren mehrerer Messungen aus einem Array in einer Nachricht**

*Meldung*

```json
{
    "Body": [
        {
            "heartRate": "78",
            "endDate": "2021-02-01T22:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "81",
            "endDate": "2021-02-01T23:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "72",
            "endDate": "2021-02-01T24:46:01.8750000Z",
            "deviceId": "device123"
        }
    ],
    "Properties": {},
    "SystemProperties": {}
}
```
*Vorlage*

```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```

> [!TIP]
> Informationen zur Behebung häufiger Fehler und [Probleme finden](./iot-troubleshoot-guide.md) Sie im Leitfaden zur Problembehandlung für IoT-Connectors.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie Gerätezuordnungen verwenden. Informationen zur Verwendung von FHIR-Zielzuordnungen finden Sie unter

>[!div class="nextstepaction"]
>[Verwenden von FHIR-Zielzuordnungen](how-to-use-fhir-mappings.md)

(FHIR&#174;) ist eine registrierte Marke von [HL7](https://hl7.org/fhir/) und wird mit der Berechtigung von HL7 verwendet.
