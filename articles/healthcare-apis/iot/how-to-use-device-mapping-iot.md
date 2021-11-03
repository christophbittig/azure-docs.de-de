---
title: Gerätezuordnungsvorlage in IoT Connector – Azure Healthcare-APIs
description: In diesem Artikel wird beschrieben, wie Sie die Gerätezuordnungsvorlage im IoT Connector.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 10/26/2021
ms.author: jasteppe
ms.openlocfilehash: ef22404d96599768ad55c3c3687e3df9fb4bbf35
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131040469"
---
# <a name="how-to-use-device-mapping"></a>Verwenden der Gerätezuordnung

> [!IMPORTANT]
> Azure Healthcare-APIs sind derzeit als VORSCHAUversion verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

Der IoT-Connector erfordert zwei Arten von JSON-basierten Zuordnungen. Der erste heißt **Gerätezuordnung** und ist dafür zuständig, die an den Azure Event Hubs-Endpunkt `devicedata` gesendeten Gerätenutzlasten zuzuordnen. Mit dieser Art von Vorlage werden Typen, Gerätebezeichner, gemessene Zeitdaten sowie Messwerte extrahiert. 

Der zweite Typ, **Fast Healthcare Interoperability Resources (FHIR&#174;),** steuert die Zuordnung für die FHIR-Ressource. Mit dieser Vorlagenart können Sie die Länge des Beobachtungszeitraums, den zum Speichern der Werte verwendeten FHIR-Datentyp sowie die Terminologiecodes konfigurieren. 

Die beiden Zuordnungstypen werden basierend auf ihrem Typ zu einem JSON-Dokument zusammengesetzt. Diese JSON-Dokumente werden ihrem IoT-Connector dann über die Azure-Portal. Das Dokument Gerätezuordnung wird über die Seite **Gerätezuordnung** und das FHIR-Zielzuordnungsdokument über die **Seite Ziel** hinzugefügt.

> [!NOTE]
> Zuordnungen werden in einem zugrunde liegenden Blobspeicher gespeichert und pro Computeausführung aus dem Blob geladen. Nach dem Update sollten sie sofort wirksam werden. 

> [!TIP]
> Sehen Sie sich das [IoMT Connector Data Mapper-Tool zum](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) Bearbeiten, Testen und Behandeln von Problemen bei Der IoT-Connector geräte- und FHIR-Zielzuordnungen an. Exportieren Sie Zuordnungen zum Hochladen in den IoT-Connector im Azure-Portal oder verwenden Sie sie mit der [Open-Source-Version des](https://github.com/microsoft/iomt-fhir) IoT-Connectors.

## <a name="device-mapping"></a>Gerätezuordnung

Die Gerätezuordnung ist die Zuordnungsfunktion zum Extrahieren von Geräteinhalten in ein gängiges Format zwecks weiterer Auswertung. Jede empfangene Nachricht wird für alle Vorlagen ausgewertet. So kann eine einzelne eingehende Nachricht auf mehrere ausgehende Nachrichten projiziert werden, die später verschiedenen Beobachtungen in FHIR zugeordnet werden. Das Ergebnis ist ein normalisiertes Datenobjekt, das den Wert oder die Werte darstellt, die von den Vorlagen analysiert werden. Das normalisierte Datenmodell verfügt über einige erforderliche Eigenschaften, die gefunden und extrahiert werden müssen:

| Eigenschaft             | Beschreibung                                                                                                                                                                                                                                                   |
|----------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Type**             | Der Name/Typ, der die Messung klassifiziert. Dieser Wert wird verwendet, um eine Bindung an die erforderliche FHIR-Zielzuordnung zu erstellen.  Mehrere Zuordnungen können an denselben Typ ausgegeben werden, sodass Sie verschiedene Darstellungen auf mehreren Geräten einer einzelnen gemeinsamen Ausgabe zuordnen können. |
| **OccurenceTimeUtc** | Der Zeitpunkt, zu dem die Messung vorgenommen wurde                                                                                                                                                                                                                            |
| **DeviceId**         | Der Bezeichner für das Gerät. Dieser Wert sollte mit einem Bezeichner für die Geräteressource übereinstimmen, die im Ziel-FHIR-Dienst vorhanden ist.                                                                                                                       |
| **Eigenschaften**       | Extrahieren Sie mindestens eine Eigenschaft, damit der Wert in der erstellten Observation-Ressource gespeichert werden kann.  Eigenschaften sind eine Sammlung von Schlüssel-Wert-Paaren, die bei der Normalisierung extrahiert wurden.                                                                                  |

Nachstehend finden Sie ein Beispieldiagramm für den Ablauf einer Normalisierung.

![Normalisierungsbeispiel](media/concepts-iot-mapping-templates/normalization-example.png#lightbox)

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
### <a name="mapping-with-json-path"></a>Zuordnen mit einem JSON-Pfad

Die drei derzeit unterstützten Geräteinhaltszuordnungstypen basieren auf JSON Path, um beide mit den erforderlichen Zuordnungs- und extrahierten Werten zu übereinstimmen. Weitere Informationen zum JSON-Pfad finden Sie [hier](https://goessner.net/articles/JsonPath/). Alle drei Vorlagentypen verwenden die [JSON.NET-Implementierung](https://www.newtonsoft.com/json/help/html/QueryJsonSelectTokenJsonPath.htm) zum Auflösen von JSON-Pfadausdrücken.

#### <a name="jsonpathcontenttemplate"></a>JsonPathContentTemplate

Mit JsonPathContentTemplate können Werte mithilfe des JSON-Pfads abgeglichen und aus einer Event Hubs-Nachricht extrahiert werden.

| Eigenschaft | BESCHREIBUNG |Beispiel |
| --- | --- | --- |
|**TypeName**|Der den Messungen zugewiesene Typ, die mit der Vorlage übereinstimmen|`heartrate`
|**TypeMatchExpression**|Der JSON-Pfadausdruck, der gegen die Event Hubs-Nutzlast ausgewertet wird. Wird ein entsprechendes JToken gefunden, gilt die Vorlage als Übereinstimmung. Alle nachfolgenden Ausdrücke werden für das extrahierte JToken ausgewertet, das hier abgeglichen wurde.|`$..[?(@heartRate)]`
|**TimestampExpression**|Der JSON-Pfadausdruck, mit dem der Zeitstempelwert für die Eigenschaft „OccurenceTimeUtc“ der Messung extrahiert wird|`$.endDate`
|**DeviceIdExpression**|Der JSON-Pfadausdruck zum Extrahieren des Gerätebezeichners|`$.deviceId`
|**PatientIdExpression**|*Optional*: Der JSON-Pfadausdruck zum Extrahieren des Patientenbezeichners|`$.patientId`
|**EncounterIdExpression**|*Optional*: Der JSON-Pfadausdruck zum Extrahieren des Untersuchungsbezeichners|`$.encounterId`
|**Values[].ValueName**|Der Name, der dem vom nachfolgenden Ausdruck extrahierten Wert zugeordnet werden soll. Wird verwendet, um den erforderlichen Wert/die erforderliche Komponente in der FHIR-Zielzuordnung zu binden. |`hr`
|**Values[].ValueExpression**|Der JSON-Pfadausdruck zum Extrahieren des erforderlichen Werts|`$.heartRate`
|**Values[].Required**|Mit dieser Eigenschaft wird festgelegt, ob der Wert in der Nutzlast vorhanden sein muss.  Wird er nicht gefunden, wird keine Messung generiert, und InvalidOperationException wird ausgelöst.|`true`

##### <a name="examples"></a>Beispiele

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
#### <a name="iotjsonpathcontenttemplate"></a>IotJsonPathContentTemplate

IotJsonPathContentTemplate ähnelt JsonPathContentTemplate, mit dem Unterschied, dass DeviceIdExpression und TimestampExpression nicht erforderlich sind.

Bei Verwendung dieser Vorlage wird davon ausgegangen, dass die zurzeit ausgewerteten Nachrichten mithilfe der [Azure IoT Hub-Geräte-SDKs](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) oder des Features [Daten exportieren (Legacy)](../../iot-central/core/howto-export-data-legacy.md) von [Azure IoT Central](../../iot-central/core/overview-iot-central.md) gesendet wurden. Bei Verwendung dieser SDKs sind die Geräteidentität (vorausgesetzt, die Geräte-ID von Azure Iot Hub/Central wird als Bezeichner für eine Geräteressource im Ziel-FHIR-Dienst registriert) und der Zeitstempel der Nachricht bekannt. Wenn Sie Azure IoT Hub-Geräte-SDKs verwenden, jedoch benutzerdefinierte Eigenschaften für die im Nachrichtentext angegebene Geräteidentität oder den Messungszeitstempel konfigurieren, können Sie dennoch JsonPathContentTemplate nutzen.

> [!NOTE]
> Bei Verwendung von `IotJsonPathContentTemplate` sollte `TypeMatchExpression` als JToken in die gesamte Nachricht auflösen. Weitere Informationen finden Sie in den folgenden Beispielen.

##### <a name="examples"></a>Beispiele

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
#### <a name="iotcentraljsonpathcontenttemplate"></a>IotCentralJsonPathContentTemplate

Für die IotCentralJsonPathContentTemplate sind auch DeviceIdExpression und TimestampExpression nicht erforderlich. Sie wird verwendet, wenn die ausgewerteten Nachrichten über das Feature [Daten](../../iot-central/core/howto-export-data.md) exportieren von Azure IoT [werden.](../../iot-central/core/overview-iot-central.md) Wenn Sie dieses Feature verwenden, sind die Geräteidentität (vorausgesetzt, die Geräte-ID von Azure Iot Central wird als Bezeichner für eine Geräteressource auf dem FHIR-Zielserver registriert) und der Zeitstempel der Nachricht bekannt. Wenn Sie das Feature „Datenexport“ von Azure IoT Central einsetzen, im Nachrichtentext für die Geräteidentität oder den Messungszeitstempel aber benutzerdefinierte Eigenschaften verwenden, können Sie JsonPathContentTemplate trotzdem nutzen.

> [!NOTE]
> Bei Verwendung von IotCentralJsonPathContentTemplate sollte TypeMatchExpression als JToken in die gesamte Nachricht auflösen. Weitere Informationen finden Sie in den folgenden Beispielen.
 
##### <a name="examples"></a>Beispiele

**Heart rate**

*Meldung*

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2021-08-05T22:26:55.455Z",
    "telemetry": {
        "HeartRate": "88",
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
    "enqueuedTime": "2021-08-05T22:26:55.455Z",
    "telemetry": {
        "BloodPressure": {
            "Diastolic": "87",
            "Systolic": "123"
        }
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

>[!div class="nextstepaction"]
>[Verwenden von FHIR-Zielzuordnungen](how-to-use-fhir-mapping-iot.md)

(FHIR&#174;) ist eine registrierte Marke von [HL7](https://hl7.org/fhir/) und wird mit der Berechtigung von HL7 verwendet.