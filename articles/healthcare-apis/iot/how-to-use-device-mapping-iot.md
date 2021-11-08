---
title: Gerätezuordnungsvorlage in IoT Connector – Azure Healthcare-APIs
description: In diesem Artikel wird beschrieben, wie Sie die Gerätezuordnungsvorlage im IoT Connector verwenden.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/05/2021
ms.author: jasteppe
ms.openlocfilehash: f11770a05d2429c87647b65a828f6477f1b1b8cb
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131988185"
---
# <a name="how-to-use-device-mappings"></a>Verwenden von Gerätezuordnungen

> [!IMPORTANT]
> Azure Healthcare-APIs befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

Der IoT-Connector erfordert zwei Arten von JSON-basierten Zuordnungen. Der erste heißt **Gerätezuordnung** und ist dafür zuständig, die an den Azure Event Hubs-Endpunkt `devicedata` gesendeten Gerätenutzlasten zuzuordnen. Mit dieser Art von Vorlage werden Typen, Gerätebezeichner, gemessene Zeitdaten sowie Messwerte extrahiert. 

Der zweite Typ, **Fast Healthcare Interoperability Resources (FHIR&#174;),** steuert die Zuordnung für die FHIR-Ressource. Mit dieser Vorlagenart können Sie die Länge des Beobachtungszeitraums, den zum Speichern der Werte verwendeten FHIR-Datentyp sowie die Terminologiecodes konfigurieren. 

Die beiden Zuordnungstypen bestehen basierend auf ihrem Typ in einem JSON-Dokument. Diese JSON-Dokumente werden dann ihrem IoT-Connector über die Azure-Portal hinzugefügt. Das Dokument Gerätezuordnung wird über die Seite **Gerätezuordnung** und das FHIR-Zielzuordnungsdokument über die Seite **Ziel** hinzugefügt.

> [!NOTE]
> Zuordnungen werden in einem zugrunde liegenden Blobspeicher gespeichert und pro Computeausführung aus dem Blob geladen. Nach dem Update sollten sie sofort wirksam werden. 

> [!TIP]
> Sehen Sie sich das [Tool IoMT Connector Data Mapper](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) zum Bearbeiten, Testen und Beheben von Problemen mit Geräte- und FHIR-Zielzuordnungen für IoT-Connectors an. Exportieren Sie Zuordnungen zum Hochladen in den IoT-Connector im Azure-Portal oder verwenden Sie sie mit der [Open-Source-Version](https://github.com/microsoft/iomt-fhir) des IoT-Connectors.

## <a name="device-mappings-overview"></a>Übersicht über Gerätezuordnungen

Gerätezuordnungen bieten Funktionen zum Extrahieren von Gerätenachrichteninhalten in ein gemeinsames Format für die weitere Auswertung. Jede empfangene Gerätenachricht wird anhand aller Gerätezuordnungsvorlagen ausgewertet. 

Eine einzelne eingehende Gerätenachricht kann in mehrere ausgehende Nachrichten unterteilt werden, die später verschiedenen Beobachtungen im FHIR-Dienst zugeordnet werden. 

Das Ergebnis ist ein normalisiertes Datenobjekt, das den Wert oder die Werte darstellt, die von den Vorlagen analysiert werden. 

Das normalisierte Datenmodell verfügt über einige erforderliche Eigenschaften, die gefunden und extrahiert werden müssen:

|Eigenschaft|Beschreibung|
|--------|-----------|
|**Type**|Der Name/Typ, der die Messung klassifiziert. Dieser Wert wird zum Binden an die erforderliche FHIR-Zielzuordnung verwendet. Mehrere Zuordnungen können an denselben Typ ausgegeben werden, sodass Sie verschiedene Darstellungen auf mehreren Geräten einer einzelnen gemeinsamen Ausgabe zuordnen können.|
|**OccurenceTimeUtc**|Der Zeitpunkt, zu dem die Messung vorgenommen wurde|
|**DeviceId**|Der Bezeichner für das Gerät. Dieser Wert sollte mit einem Bezeichner für die Geräteressource übereinstimmen, die im FHIR-Zieldienst vorhanden ist.|
|**Eigenschaften**|Extrahieren Sie mindestens eine Eigenschaft, damit der Wert in der erstellten Observation-Ressource gespeichert werden kann. Eigenschaften sind eine Sammlung von Schlüssel-Wert-Paaren, die bei der Normalisierung extrahiert wurden.|

> [!IMPORTANT]
> Das vollständig normalisierte Modell wird durch die [IMeasurement-Schnittstelle](https://github.com/microsoft/iomt-fhir/blob/master/src/lib/Microsoft.Health.Fhir.Ingest.Schema/IMeasurement.cs) definiert.

Im Folgenden finden Sie konzeptionelle Beispiele dafür, was während des Normalisierungs- und Transformationsprozesses innerhalb des IoT-Connectors geschieht:

:::image type="content" source="media/iot-data-normalization-high-level.png" alt-text="IoT-Datennormalisierungsfluss – Beispiel1" lightbox="media/iot-data-normalization-high-level.png":::

:::image type="content" source="media/concepts-iot-mapping-templates/normalization-example.png" alt-text="IoT-Datennormalisierungsfluss – Beispiel2" lightbox="media/concepts-iot-mapping-templates/normalization-example.png":::

Die Inhaltsnutzlast selbst ist eine Azure Event Hubs-Nachricht, die aus drei Teilen besteht: Body, Properties und SystemProperties. `Body` besteht aus einem Bytearray, das eine UTF-8-codierte Zeichenfolge darstellt. Während der Vorlagenauswertung wird das Bytearray automatisch in den Zeichenfolgenwert konvertiert. `Properties` ist eine Schlüssel-Wert-Sammlung, die vom Nachrichtenersteller verwendet wird. `SystemProperties` ist ebenfalls eine Schlüssel-Wert-Sammlung, die vom Azure Event Hubs-Framework reserviert wird und deren Einträge automatisch aufgefüllt werden.

```json
{
    "Body": {
        "content": "value"
    },
    "Properties": {
        "key1": "value1",
        "key2": "value2"
    },
    "SystemProperties": {
        "x-opt-sequence-number": 1,
        "x-opt-enqueued-time": "2021-02-01T22:46:01.8750000Z",
        "x-opt-offset": 1,
        "x-opt-partition-key": "1"
    }
}
```
## <a name="mapping-with-jsonpath"></a>Zuordnung mit JSONPath

Die fünf derzeit unterstützten Typen der Geräteinhaltszuordnung basieren auf JSONPath, um sowohl die erforderlichen Zuordnungs- als auch die extrahierten Werte zu berücksichtigen. Weitere Informationen zu JSONPath finden Sie [hier.](https://goessner.net/articles/JsonPath/) Alle fünf Vorlagentypen verwenden die [JSON.NET-Implementierung](https://www.newtonsoft.com/json/help/html/QueryJsonSelectTokenJsonPath.htm) zum Auflösen von JSONPath-Ausdrücken.

Sie können eine oder mehrere Vorlagen in der Vorlage Gerätezuordnung definieren. Jede empfangene Event Hub-Gerätenachricht wird anhand aller Gerätezuordnungsvorlagen ausgewertet. 

Eine einzelne eingehende Gerätenachricht kann in mehrere ausgehende Nachrichten unterteilt werden, die später verschiedenen Beobachtungen im FHIR-Dienst zugeordnet werden. 

Es gibt verschiedene Vorlagentypen, die beim Erstellen der Gerätezuordnungsdatei verwendet werden können.

|Name                                                                     | BESCHREIBUNG                                                                   |  
|-------------------------------------------------------------------------|-------------------------------------------------------------------------------|
|[JsonPathContentTemplate](#jsonpathcontenttemplate)                      |Eine Vorlage, die das Schreiben von Ausdrücken mit jsonPath unterstützt                  
|[CollectionContentTemplate](#collectioncontenttemplate)                  |Eine Vorlage, die verwendet wird, um eine Liste von Vorlagen darzustellen, die während der Normalisierung verwendet werden.                                                            |                                                           
|[CalculatedContentTemplate](#calculatedcontenttemplate)                  |Eine Vorlage, die das Schreiben von Ausdrücken mit einer von mehreren Ausdruckssprachen unterstützt. Unterstützt die Datentransformation über die Verwendung von JmesPath-Funktionen.|
|[IotJsonPathContentTemplate](#iotjsonpathcontenttemplate)                |Eine Vorlage, die Nachrichten unterstützt, die von Azure IoT Hub oder dem Legacy-Exportdatenfeature von Azure IoT Central gesendet werden.                                        |
|[IotCentralJsonPathContentTemplate](#iotcentraljsonpathcontenttemplate)  |Eine Vorlage, die Nachrichten unterstützt, die über das Feature Daten exportieren von Azure IoT Central gesendet werden.|  

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

## <a name="collectioncontenttemplate"></a>CollectionContentTemplate

Die CollectionContentTemplate kann verwendet werden, um eine Liste von Vorlagen darzustellen, die während der Normalisierung verwendet werden.
                                                             
### <a name="example"></a>Beispiel

```json
{
  "templateType": "CollectionContent",
  "template": [
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
    },
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "stepcount",
        "typeMatchExpression": "$..[?(@steps)]",
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
  ]
}
```

## <a name="calculatedcontenttemplate"></a>CalculatedContentTemplate

Der IoT-Connector stellt eine ausdrucksbasierte Inhaltsvorlage bereit, die sowohl der gewünschten Vorlage entspricht als auch Werte extrahiert. **Ausdrücke** können entweder von JSONPath oder JmesPath verwendet werden. Jeder Ausdruck innerhalb der Vorlage kann eine eigene Ausdruckssprache auswählen. 

> [!NOTE]
> Wenn keine Ausdruckssprache definiert ist, wird die für die Vorlage konfigurierte Standardausdruckssprache verwendet. Der Standardwert ist JSONPath, kann aber bei Bedarf überschrieben werden.

Ein Ausdruck ist wie folgt definiert:

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

| Ausdruckssprache | Wert        |
|---------------------|--------------|
| JSONPath            | **JsonPath** |
| JmesPath            | **JmesPath** |

>[!TIP]
>Weitere Informationen zu JSONPath finden Sie unter [JSONPath](https://goessner.net/articles/JsonPath/). [CalculatedContentTemplate](#calculatedcontenttemplate) verwendet die [JSON.NET-Implementierung](https://www.newtonsoft.com/json/help/html/QueryJsonSelectTokenJsonPath.htm) zum Auflösen von JSONPath-Ausdrücken.
>
>Weitere Informationen zu JmesPath finden Sie unter [JmesPath](https://jmespath.org/specification.html). [CalculatedContentTemplate](#calculatedcontenttemplate) verwendet die [JmesPath .NET-Implementierung](https://github.com/jdevillard/JmesPath.Net) zum Auflösen von JmesPath-Ausdrücken.

### <a name="custom-functions"></a>Benutzerdefinierte Funktionen

Eine Reihe benutzerdefinierter Funktionen des IoT-Connectors ist ebenfalls verfügbar. Diese benutzerdefinierten Funktionen befinden sich außerhalb der Funktionen, die als Teil der JmesPath-Spezifikation bereitgestellt werden. Weitere Informationen zu benutzerdefinierten Funktionen des IoT-Connectors finden Sie unter Kundenfunktionen des [IoT-Connectors.](./iot-connector-custom-functions.md)

### <a name="matched-token"></a>Übereinstimmende Token

**TypeMatchExpression** wird anhand der eingehenden EventData-Nutzlast ausgewertet. Wird ein entsprechendes JToken gefunden, gilt die Vorlage als Übereinstimmung. 

Alle späteren Ausdrücke werden für ein neues JToken ausgewertet. Dieses neue JToken enthält sowohl die ursprüngliche EventData-Nutzlast als auch das extrahierte JToken, das hier übereinstimmt. 

Auf diese Weise sind die ursprüngliche Nutzlast und das übereinstimmende Objekt für jeden späteren Ausdruck verfügbar. Das extrahierte JToken ist als eigenschaft **matchedToken** verfügbar.

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

**Project Mehrere Messungen aus einer einzelnen Nachricht**

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

**Project Mehrere Messungen aus array in Message**

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

Im folgenden Beispiel werden Die Höhendaten in Zoll oder Meter eintreffen. Wir möchten, dass alle normalisierten Höhendaten in Metern gespeichert werden. Um dieses Ergebnis zu erzielen, erstellen wir eine Vorlage, die nur auf Höhendaten in Zoll abzielt und diese in Meter transformiert. Eine andere Vorlage zielt auf Höhendaten in Metern ab und speichert sie einfach so, wie sie sind.

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
## <a name="iotjsonpathcontenttemplate"></a>IotJsonPathContentTemplate

Die IotJsonPathContentTemplate ähnelt der JsonPathContentTemplate, außer dass `DeviceIdExpression` und `TimestampExpression` nicht erforderlich sind.

Bei Verwendung dieser Vorlage wird davon ausgegangen, dass die ausgewerteten Nachrichten mithilfe der [Azure IoT Hub-Geräte-SDKs](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) oder des Features Daten [exportieren (Legacy)](../../iot-central/core/howto-export-data-legacy.md) von [Azure IoT Central gesendet wurden.](../../iot-central/core/overview-iot-central.md) 

Wenn Sie diese SDKs verwenden, sind die Geräteidentität und der Zeitstempel der Nachricht bekannt.

>[!IMPORTANT]
>Stellen Sie sicher, dass Sie eine Geräte-ID von Azure Iot Hub oder Azure IoT Central verwenden, die als Bezeichner für eine Geräteressource im Ziel-FHIR-Dienst registriert ist.

Wenn Sie Azure IoT Hub-Geräte-SDKs verwenden, können Sie weiterhin jsonPathContentTemplate verwenden, vorausgesetzt, Sie verwenden benutzerdefinierte Eigenschaften im Nachrichtentext für die Geräteidentität oder den Zeitstempel der Messung.

> [!NOTE]
> Bei Verwendung `IotJsonPathContentTemplate` von sollte in die gesamte Nachricht als `TypeMatchExpression` JToken auflösen. Weitere Informationen finden Sie in den folgenden Beispielen:

### <a name="examples"></a>Beispiele

**Heart rate**

*Meldung*

```json
{
    "Body": {
        "heartRate": "78"        
    },
    "Properties": {
        "iothub-creation-time-utc" : "2021-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```

*Vorlage*

```json

    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@Body.heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.Body.heartRate",
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
        "diastolic" : "87"
    },
    "Properties": {
        "iothub-creation-time-utc" : "2021-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```

*Vorlage*

```json
{
    "typeName": "bloodpressure",
    "typeMatchExpression": "$..[?(@Body.systolic && @Body.diastolic)]",
    "values": [
        {
            "required": "true",
            "valueExpression": "$.Body.systolic",
            "valueName": "systolic"
        },
        {
            "required": "true",
            "valueExpression": "$.Body.diastolic",
            "valueName": "diastolic"
        }
    ]
}
```
## <a name="iotcentraljsonpathcontenttemplate"></a>IotCentralJsonPathContentTemplate

Für IotCentralJsonPathContentTemplate sind auch DeviceIdExpression und TimestampExpression nicht erforderlich. Sie wird verwendet, wenn die ausgewerteten Nachrichten über das Feature [Daten](../../iot-central/core/howto-export-data.md) exportieren von Azure IoT [werden.](../../iot-central/core/overview-iot-central.md) 

Wenn Sie das Datenexportfeature von Azure IoT Central und benutzerdefinierte Eigenschaften im Nachrichtentext für die Geräteidentität oder den Messungszeitstempel verwenden, können Sie weiterhin JsonPathContentTemplate verwenden.

> [!NOTE]
> Bei Verwendung `IotCentralJsonPathContentTemplate` von sollte in die gesamte Nachricht als `TypeMatchExpression` JToken auflösen. Weitere Informationen finden Sie in den folgenden Beispielen:
 
### <a name="examples"></a>Beispiele

**Heart rate**

*Meldung*

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "Activity": "running",
        "BloodPressure": {
            "Diastolic": 7,
            "Systolic": 71
        },
        "BodyTemperature": 98.73447010562934,
        "HeartRate": 88,
        "HeartRateVariability": 17,
        "RespiratoryRate": 13
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```

*Vorlage*

```json
{
    "templateType": "IotCentralJsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@telemetry.HeartRate)]",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.telemetry.HeartRate",
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
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "Activity": "running",
        "BloodPressure": {
            "Diastolic": 7,
            "Systolic": 71
        },
        "BodyTemperature": 98.73447010562934,
        "HeartRate": 88,
        "HeartRateVariability": 17,
        "RespiratoryRate": 13
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```

*Vorlage*

```json
{
    "templateType": "IotCentralJsonPathContent",
    "template": {
        "typeName": "bloodPressure",
        "typeMatchExpression": "$..[?(@telemetry.BloodPressure.Diastolic && @telemetry.BloodPressure.Systolic)]",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.telemetry.BloodPressure.Diastolic",
                "valueName": "bp_diastolic"
            },
            {
                "required": "true",
                "valueExpression": "$.telemetry.BloodPressure.Systolic",
                "valueName": "bp_systolic"
            }
        ]
    }
}
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie Gerätezuordnungen verwenden. Informationen zur Verwendung von FHIR-Zielzuordnungen finden Sie unter

>[!div class="nextstepaction"]
>[Verwenden von FHIR-Zielzuordnungen](how-to-use-fhir-mapping-iot.md)

(FHIR&#174;) ist eine registrierte Marke von [HL7](https://hl7.org/fhir/) und wird mit der Berechtigung von HL7 verwendet.
