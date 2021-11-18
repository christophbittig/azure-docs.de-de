---
title: JsonPathContentTemplate-Zuordnungen in Gerätezuordnungen des IoT-Connectors – Azure Healthcare-APIs
description: In diesem Artikel wird beschrieben, wie Sie JsonPathContentTemplate-Zuordnungen mit Gerätezuordnungsvorlagen des IoT-Connectors verwenden.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/16/2021
ms.author: jasteppe
ms.openlocfilehash: 7def9d932f36a7720d0da7e19a29cf72f2ef0b98
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2021
ms.locfileid: "132733412"
---
# <a name="how-to-use-jsonpathcontenttemplate-mappings"></a>Verwenden von JsonPathContentTemplate-Zuordnungen

> [!IMPORTANT]
> Azure Healthcare-APIs befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

> [!TIP]
> Sehen Sie sich das [Tool IoMT Connector Data Mapper](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) zum Bearbeiten, Testen und Beheben von Problemen mit Geräte- und FHIR-Zielzuordnungen des IoT-Connectors an. Exportieren Sie Zuordnungen zum Hochladen in den IoT-Connector im Azure-Portal oder verwenden Sie sie mit der [Open-Source-Version](https://github.com/microsoft/iomt-fhir) des IoT-Connectors.

In diesem Artikel wird beschrieben, wie Sie JsonPathContentTemplate-Zuordnungen mit Gerätezuordnungsvorlagen des IoT-Connectors verwenden.

## <a name="jsonpathcontenttemplate"></a>JsonPathContentTemplate

JsonPathContentTemplate ermöglicht das Abgleichen und Extrahieren von Werten aus einer Azure Event Hub-Nachricht mithilfe von JSONPath.

|Eigenschaft|BESCHREIBUNG|Beispiel|
|--------|-----------|-------|
|TypName|Der Typ, der Messungen zugeordnet werden soll, die mit der Vorlage übereinstimmen|`heartrate`|
|TypeMatchExpression|Der JSONPath-Ausdruck, der für die EventData-Nutzlast ausgewertet wird. Wird ein entsprechendes JToken gefunden, gilt die Vorlage als Übereinstimmung. Alle späteren Ausdrücke werden anhand des extrahierten JToken ausgewertet, das hier übereinstimmt.|`$..[?(@heartRate)]`|
|TimestampExpression|Der JSONPath-Ausdruck zum Extrahieren des Zeitstempelwerts für occurrenceTimeUtc der Messung.|`$.matchedToken.endDate`|
|DeviceIdExpression|Der JSONPath-Ausdruck zum Extrahieren des Gerätebezeichners.|`$.matchedToken.deviceId`|
|PatientIdExpression|*Erforderlich,* wenn IdentityResolution sich im **Erstellungsmodus** befindet, und *optional,* wenn IdentityResolution sich im **Suchmodus** befindet. Der Ausdruck zum Extrahieren des Patientenbezeichners.|`$.matchedToken.patientId`|
|EncounterIdExpression|*Optional:* Der Ausdruck zum Extrahieren des Erkennungsbezeichners.|`$.matchedToken.encounterId`|
|CorrelationIdExpression|*Optional:* Der Ausdruck zum Extrahieren des Korrelationsbezeichners. Diese Ausgabe kann verwendet werden, um Werte in einer einzelnen Beobachtung in den FHIR-Zielzuordnungen zu gruppieren.|`$.matchedToken.correlationId`|
|Values[].ValueName|Der Name, der dem vom nächsten Ausdruck extrahierten Wert zugeordnet werden soll. Wird verwendet, um den gewünschten Wert/die gewünschte Komponente in der FHIR-Zielzuordnungsvorlage zu binden.|`hr`|
|Values[].ValueExpression|Der JSONPath-Ausdruck zum Extrahieren des gewünschten Werts.|`$.matchedToken.heartRate`|
|Values[].Required|Mit dieser Eigenschaft wird festgelegt, ob der Wert in der Nutzlast vorhanden sein muss. Wenn sie nicht gefunden wird, wird keine Messung generiert, und eine InvalidOperationException wird erstellt.|`true`|

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

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie Gerätezuordnungen verwenden. Informationen zur Verwendung von FHIR-Zielzuordnungen finden Sie unter

>[!div class="nextstepaction"]
>[Verwenden von FHIR-Zielzuordnungen](how-to-use-fhir-mappings.md)

(FHIR&#174;) ist eine registrierte Marke von [HL7](https://hl7.org/fhir/) und wird mit der Berechtigung von HL7 verwendet.
