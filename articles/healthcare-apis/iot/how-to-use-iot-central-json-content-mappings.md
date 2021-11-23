---
title: IotCentralJsonPathContentTemplate-Zuordnungen in IoT Connector Gerätezuordnungen – Azure Healthcare-APIs
description: In diesem Artikel wird beschrieben, wie IotCentralJsonPathContent-Zuordnungen mit IoT Connector Vorlagen für Gerätezuordnungen verwendet werden.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/22/2021
ms.author: jasteppe
ms.openlocfilehash: 9fb3331e70d6a9e132f2d7cd96768249f0de83fb
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2021
ms.locfileid: "132938852"
---
# <a name="how-to-use-iotcentraljsonpathcontenttemplate-mappings"></a>Verwenden von IotCentralJsonPathContentTemplate-Zuordnungen

> [!IMPORTANT]
> Azure Healthcare-APIs befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

> [!TIP]
> Sehen Sie sich das [Tool IoMT Connector Data Mapper](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) zum Bearbeiten, Testen und Beheben von Problemen mit Geräte- und FHIR-Zielzuordnungen des IoT-Connectors an. Exportieren Sie Zuordnungen zum Hochladen in den IoT-Connector im Azure-Portal oder verwenden Sie sie mit der [Open-Source-Version](https://github.com/microsoft/iomt-fhir) des IoT-Connectors.

In diesem Artikel wird beschrieben, wie IoTCentralJsonPathContentTemplate-Zuordnungen mit IoT-Connectorgerätezuordnungen verwendet werden.

## <a name="iotcentraljsonpathcontenttemplate"></a>IotCentralJsonPathContentTemplate

Die IotCentralJsonPathContentTemplate erfordert auch keine DeviceIdExpression und TimestampExpression. Sie wird verwendet, wenn die ausgewerteten Nachrichten über das Feature [Daten exportieren](../../iot-central/core/howto-export-data.md) von [Azure IoT Central](../../iot-central/core/overview-iot-central.md)gesendet werden. 

Wenn Sie das Datenexportfeature von Azure IoT Central und benutzerdefinierte Eigenschaften im Nachrichtentext für die Geräteidentität oder den Messzeitstempel verwenden, können Sie weiterhin jsonPathContentTemplate verwenden.

> [!NOTE]
> Bei Verwendung `IotCentralJsonPathContentTemplate` von sollte in die gesamte Nachricht als `TypeMatchExpression` JToken aufgelöst werden. Weitere Informationen finden Sie in den folgenden Beispielen:
 
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

> [!TIP]
> Informationen zum Beheben häufiger Fehler und Probleme finden Sie im Leitfaden zur [Problembehandlung](./iot-troubleshoot-guide.md) für IoT-Connectors.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie Gerätezuordnungen verwenden. Informationen zur Verwendung von FHIR-Zielzuordnungen finden Sie unter

>[!div class="nextstepaction"]
>[Verwenden von FHIR-Zielzuordnungen](how-to-use-fhir-mappings.md)

(FHIR&#174;) ist eine registrierte Marke von [HL7](https://hl7.org/fhir/) und wird mit der Berechtigung von HL7 verwendet.
