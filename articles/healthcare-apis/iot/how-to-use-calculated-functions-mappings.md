---
title: CalculatedContentTemplate-Zuordnungen in IoT Connector Gerätezuordnungen – Azure Healthcare-APIs
description: In diesem Artikel wird beschrieben, wie Sie CalculatedContentTemplate-Zuordnungen mit Gerätezuordnungsvorlagen des IoT-Connectors verwenden.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/22/2021
ms.author: jasteppe
ms.openlocfilehash: 6e92acd7cc537b9723b1b114b11d8408cf941443
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2021
ms.locfileid: "132940983"
---
# <a name="how-to-use-calculatedcontenttemplate-mappings"></a>Verwenden von CalculatedContentTemplate-Zuordnungen

> [!IMPORTANT]
> Azure Healthcare-APIs befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

> [!TIP]
> Sehen Sie sich das [Tool IoMT Connector Data Mapper](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) zum Bearbeiten, Testen und Beheben von Problemen mit Geräte- und FHIR-Zielzuordnungen des IoT-Connectors an. Exportieren Sie Zuordnungen zum Hochladen in den IoT-Connector im Azure-Portal oder verwenden Sie sie mit der [Open-Source-Version](https://github.com/microsoft/iomt-fhir) des IoT-Connectors.

In diesem Artikel wird beschrieben, wie Sie CalculatedContentTemplate-Zuordnungen mit Gerätezuordnungsvorlagen des IoT-Connectors verwenden.

## <a name="calculatedcontenttemplate"></a>CalculatedContentTemplate

Der IoT-Connector stellt eine ausdrucksbasierte Inhaltsvorlage bereit, die sowohl der gewünschten Vorlage entspricht als auch Werte extrahiert. **Ausdrücke** können entweder von JSONPath oder JmesPath verwendet werden. Jeder Ausdruck innerhalb der Vorlage kann eine eigene Ausdruckssprache auswählen. 

> [!NOTE]
> Wenn keine Ausdruckssprache definiert ist, wird die für die Vorlage konfigurierte Standardausdruckssprache verwendet. Der Standardwert ist JSONPath, kann aber bei Bedarf überschrieben werden.

Ein Ausdruck wird wie folgt definiert:

```json
<name of expression> : {
        "value" : <the expression>,
        "language": <the expression language>
    }
```

Im folgenden Beispiel wird *typeMatchExpression* wie folgt definiert:

```json
"templateType": "CalculatedContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": {
            "value" : "$..[?(@heartRate)]",
            "language": "JsonPath"
        },
        ...
    }
```
> [!TIP]
> Die Standardausdruckssprache, die für eine Gerätezuordnungsvorlage verwendet werden soll, ist JsonPath. Wenn Sie JsonPath verwenden möchten, kann der Ausdruck allein angegeben werden.

```json
"templateType": "CalculatedContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        ...
    }
```

Die Standardausdruckssprache, die für eine Vorlage verwendet werden soll, kann explizit mit dem -Parameter festgelegt `defaultExpressionLanguage` werden:

```json
"templateType": "CalculatedContent",
    "template": {
        "typeName": "heartrate",
        "defaultExpressionLanguage": "JsonPath",
        "typeMatchExpression": "$..[?(@heartRate)]",
        ...
    }
```

CalculatedContentTemplate ermöglicht das Abgleichen und Extrahieren von Werten aus einer Azure Event Hub-Nachricht mithilfe von **Ausdrücken,** wie unten definiert:

|Eigenschaft|BESCHREIBUNG|Beispiel|
|--------|-----------|-------|
|TypName|Der Typ, der Messungen zugeordnet werden soll, die mit der Vorlage übereinstimmen|`heartrate`|
|TypeMatchExpression|Der Ausdruck, der für die EventData-Nutzlast ausgewertet wird. Wird ein entsprechendes JToken gefunden, gilt die Vorlage als Übereinstimmung. Alle späteren Ausdrücke werden anhand des extrahierten JToken ausgewertet, das hier übereinstimmt.|`$..[?(@heartRate)]`|
|TimestampExpression|Der Ausdruck zum Extrahieren des Zeitstempelwerts für occurrenceTimeUtc der Messung.|`$.matchedToken.endDate`|
|DeviceIdExpression|Der Ausdruck, mit dem der Gerätebezeichner extrahiert werden soll.|`$.matchedToken.deviceId`|
|PatientIdExpression|*Erforderlich,* wenn IdentityResolution sich im **Erstellungsmodus** befindet, und *optional,* wenn IdentityResolution sich im **Suchmodus** befindet. Der Ausdruck zum Extrahieren des Patientenbezeichners.|`$.matchedToken.patientId`|
|EncounterIdExpression|*Optional:* Der Ausdruck zum Extrahieren des Erkennungsbezeichners.|`$.matchedToken.encounterId`|
|CorrelationIdExpression|*Optional:* Der Ausdruck zum Extrahieren des Korrelationsbezeichners. Diese Ausgabe kann verwendet werden, um Werte in einer einzelnen Beobachtung in den FHIR-Zielzuordnungen zu gruppieren.|`$.matchedToken.correlationId`|
|Values[].ValueName|Der Name, der dem vom nächsten Ausdruck extrahierten Wert zugeordnet werden soll. Wird verwendet, um den gewünschten Wert/die gewünschte Komponente in der FHIR-Zielzuordnungsvorlage zu binden.|`hr`|
|Values[].ValueExpression|Der Ausdruck zum Extrahieren des gewünschten Werts.|`$.matchedToken.heartRate`|
|Values[].Required|Mit dieser Eigenschaft wird festgelegt, ob der Wert in der Nutzlast vorhanden sein muss. Wenn sie nicht gefunden wird, wird keine Messung generiert, und eine InvalidOperationException wird erstellt.|`true`|

### <a name="expression-languages"></a>Ausdruckssprachen

Wenn Sie die Sprache angeben, die für den Ausdruck verwendet werden soll, sind die folgenden Werte gültig:

| Ausdruckssprache | value        |
|---------------------|--------------|
| JSONPath            | **JsonPath** |
| JmesPath            | **JmesPath** |

>[!TIP]
>Weitere Informationen zu JSONPath finden Sie unter [JSONPath](https://goessner.net/articles/JsonPath/). [CalculatedContentTemplate](#calculatedcontenttemplate) verwendet die [JSON.NET-Implementierung](https://www.newtonsoft.com/json/help/html/QueryJsonSelectTokenJsonPath.htm) zum Auflösen von JSONPath-Ausdrücken.
>
>Weitere Informationen zu JmesPath finden Sie unter [JmesPath](https://jmespath.org/specification.html). [CalculatedContentTemplate](#calculatedcontenttemplate) verwendet die [JmesPath .NET-Implementierung](https://github.com/jdevillard/JmesPath.Net) zum Auflösen von JmesPath-Ausdrücken.

### <a name="custom-functions"></a>Benutzerdefinierte Funktionen

Eine Reihe von benutzerdefinierten Funktionen des IoT-Connectors ist ebenfalls verfügbar. Diese benutzerdefinierten Funktionen befinden sich außerhalb der Funktionen, die als Teil der JmesPath-Spezifikation bereitgestellt werden. Weitere Informationen zu benutzerdefinierten Funktionen finden Sie unter [Benutzerdefinierte Funktionen des IoT-Connectors.](./how-to-use-custom-functions.md)

### <a name="matched-token"></a>Übereinstimmende Token

**TypeMatchExpression** wird anhand der eingehenden EventData-Nutzlast ausgewertet. Wird ein entsprechendes JToken gefunden, gilt die Vorlage als Übereinstimmung. 

Alle späteren Ausdrücke werden für ein neues JToken ausgewertet. Dieses neue JToken enthält sowohl die ursprüngliche EventData-Nutzlast als auch das extrahierte JToken, das hier übereinstimmt. 

Auf diese Weise sind die ursprüngliche Nutzlast und das übereinstimmende Objekt für jeden späteren Ausdruck verfügbar. Das extrahierte JToken ist als **matchedToken-Eigenschaft** verfügbar.

Diese Beispielmeldung gibt Folgendes an:

*Meldung*

```json
{
  "Body": {
    "deviceId": "device123",
    "data": [
      {
        "systolic": "120", // Match
        "diastolic": "80", // Match 
        "date": "2021-07-13T17:29:01.061144Z"
      },
      {
        "systolic": "122", // Match
        "diastolic": "82", // Match
        "date": "2021-07-13T17:28:01.061122Z"
      }
    ]
  },
  "Properties": {},
  "SystemProperties": {}
}
```

*Vorlage*

```json
{
  "templateType": "CollectionContent",
  "template": [
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@systolic && @diastolic)]", // Expression
        "deviceIdExpression": "$.Body.deviceId", // This accesses the attribute 'deviceId' which belongs to the original event data
        "timestampExpression": "$.matchedToken.date", 
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.systolic",
            "valueName": "systolic"
          },
          {
            "required": "true",
            "valueExpression": "$.matchedToken.diastolic",
            "valueName": "diastolic"
          }
        ]
      }
    }
  ]
}
```

Zwei Übereinstimmungen werden mit dem obigen Ausdruck extrahiert und zum Erstellen von JTokens verwendet. Spätere Ausdrücke werden mithilfe der folgenden JTokens ausgewertet:

```json
{
  "Body": {
    "deviceId": "device123",
    "data": [
      {
        "systolic": "120", 
        "diastolic": "80",
        "date": "2021-07-13T17:29:01.061144Z"
      },
      {
        "systolic": "122",
        "diastolic": "82",
        "date": "2021-07-13T17:28:01.061122Z"
      }
    ]
  },
  "Properties": {},
  "SystemProperties": {},
  "matchedToken" : {
      "systolic": "120",
      "diastolic": "80",
      "date": "2021-07-13T17:29:01.061144Z"
  }
}
```

And

```json
{
  "Body": {
    "deviceId": "device123",
    "data": [
      {
        "systolic": "120",
        "diastolic": "80",
        "date": "2021-07-13T17:29:01.061144Z"
      },
      {
        "systolic": "122", 
        "diastolic": "82", 
        "date": "2021-07-13T17:28:01.061122Z"
      }
    ]
  },
  "Properties": {},
  "SystemProperties": {},
  "matchedToken" : {
      "systolic": "122",
      "diastolic": "82",
      "date": "2021-07-13T17:28:01.061122Z"
    }
  }
}
```

### <a name="examples"></a>Beispiele

**Herzfrequenz**

*Meldung*

```json
{
  "Body": {
    "heartRate": "78",
    "endDate": "2019-02-01T22:46:01.8750000Z",
    "deviceId": "device123"
  },
  "Properties": {},
  "SystemProperties": {}
}
```

*Vorlage*

```json
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.matchedToken.deviceId",
        "timestampExpression": "$.matchedToken.endDate",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.heartRate",
            "valueName": "hr"
          }
        ]
      }
    }
```

**Blutdruck**

*Meldung*

```json
{
    "Body": {
        "systolic": "123", // Match
        "diastolic" : "87", // Match
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```

*Vorlage*

```json
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "bloodpressure",
        "typeMatchExpression": "$..[?(@systolic && @diastolic)]", // Expression
        "deviceIdExpression": "$.matchedToken.deviceId",
        "timestampExpression": "$.matchedToken.endDate",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.systolic",
            "valueName": "systolic"
          },
          {
            "required": "true",
            "valueExpression": "$.matchedToken.diastolic",
            "valueName": "diastolic"
          }
        ]
      }
    }
```

**Project mehrerer Messungen aus einer einzelnen Nachricht**

*Meldung*

```json
{
    "Body": {
        "heartRate": "78", // Match (Template 1)
        "steps": "2", // Match (Template 2)
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```

*Vorlage 1*

```json
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]", // Expression
        "deviceIdExpression": "$.matchedToken.deviceId",
        "timestampExpression": "$.matchedToken.endDate",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.heartRate",
            "valueName": "hr"
          }
        ]
      }
    },
```

*Vorlage 2*

```json
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "stepcount",
        "typeMatchExpression": "$..[?(@steps)]", // Expression
        "deviceIdExpression": "$.matchedToken.deviceId",
        "timestampExpression": "$.matchedToken.endDate",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.steps",
            "valueName": "steps"
          }
        ]
      }
    }
```

**Project mehrerer Messungen aus array in Message**

*Meldung*

```json
{
  "Body": [
    {
      "heartRate": "78", // Match
      "endDate": "2019-02-01T20:46:01.8750000Z",
      "deviceId": "device123"
    },
    {
      "heartRate": "81", // Match
      "endDate": "2019-02-01T21:46:01.8750000Z",
      "deviceId": "device123"
    },
    {
      "heartRate": "72", // Match
      "endDate": "2019-02-01T22:46:01.8750000Z",
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
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]", // Expression
        "deviceIdExpression": "$.matchedToken.deviceId",
        "timestampExpression": "$.matchedToken.endDate",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.heartRate",
            "valueName": "hr"
          }
        ]
      }
    }
```

**Project Daten aus übereinstimmendem Token und ursprünglichem Ereignis**

*Meldung*

```json
{
  "Body": {
    "deviceId": "device123",
    "data": [
      {
        "systolic": "120", // Match
        "diastolic": "80", // Match 
        "date": "2021-07-13T17:29:01.061144Z"
      },
      {
        "systolic": "122", // Match
        "diastolic": "82", // Match
        "date": "2021-07-13T17:28:01.061122Z"
      }
    ]
  },
  "Properties": {},
  "SystemProperties": {}
}
```

*Vorlage*

```json
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@systolic && @diastolic)]", // Expression
        "deviceIdExpression": "$.Body.deviceId", // This accesses the attribute 'deviceId' which belongs to the original event data
        "timestampExpression": "$.matchedToken.date", 
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.systolic",
            "valueName": "systolic"
          },
          {
            "required": "true",
            "valueExpression": "$.matchedToken.diastolic",
            "valueName": "diastolic"
          }
        ]
      }
    }
```

**Auswählen und Transformieren eingehender Daten**

Im folgenden Beispiel werden die Höhendaten in Zoll oder Metern eintreffen. Wir möchten, dass alle normalisierten Höhendaten in Metern sind. Um dieses Ergebnis zu erreichen, erstellen wir eine Vorlage, die nur höhendaten in Zoll anzielt und in Meter transformiert. Eine andere Vorlage zielt auf Höhendaten in Metern ab und speichert sie einfach wie bewahrt.

*Meldung*

```json
{
  "Body": [
    {
      "height": "78",
      "unit": "inches", // Match (Template 1)
      "endDate": "2019-02-01T22:46:01.8750000Z",
      "deviceId": "device123"
    },
    {
      "height": "1.9304",
      "unit": "meters", // Match (Template 2)
      "endDate": "2019-02-01T23:46:01.8750000Z",
      "deviceId": "device123"
    }
  ],
  "Properties": {},
  "SystemProperties": {}
}
```

*Vorlage 1*

```json
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "heightInMeters",
        "typeMatchExpression": "$..[?(@unit == 'inches')]",
        "deviceIdExpression": "$.matchedToken.deviceId",
        "timestampExpression": "$.matchedToken.endDate",
        "values": [
          {
            "required": "true",
            "valueExpression": {
              "value": "multiply(to_number(matchedToken.height), `0.0254`)", // Convert inches to meters. Notice we utilize JmesPath as that gives us access to transformation functions
              "language": "JmesPath"
            },
            "valueName": "height"
          }
        ]
      }
    }
```

*Vorlage 2*

```json
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "heightInMeters",
        "typeMatchExpression": "$..[?(@unit == 'meters')]",
        "deviceIdExpression": "$.matchedToken.deviceId",
        "timestampExpression": "$.matchedToken.endDate",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.height", // Simply extract the height as it is already in meters
            "valueName": "height"
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
