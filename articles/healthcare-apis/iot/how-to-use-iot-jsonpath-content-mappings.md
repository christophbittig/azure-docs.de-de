---
title: IotJsonPathContentTemplate-Zuordnungen in IoT Connector Gerätezuordnungen – Azure Healthcare-APIs
description: In diesem Artikel wird beschrieben, wie Sie IotJsonPathContentTemplate-Zuordnungen mit IoT Connector Gerätezuordnungsvorlagen verwenden.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/22/2021
ms.author: jasteppe
ms.openlocfilehash: 56304f99e786a06abdfa67495bea061d21ebabb4
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2021
ms.locfileid: "132936504"
---
# <a name="how-to-use-iotjsonpathcontenttemplate-mappings"></a>Verwenden von IotJsonPathContentTemplate-Zuordnungen

> [!IMPORTANT]
> Azure Healthcare-APIs sind derzeit als VORSCHAUversion verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

> [!TIP]
> Sehen Sie sich das [IoMT Connector Data Mapper-Tool zum](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) Bearbeiten, Testen und Behandeln von Problemen bei Der IoT-Connector geräte- und FHIR-Zielzuordnungen an. Exportieren Sie Zuordnungen zum Hochladen in den IoT-Connector im Azure-Portal oder verwenden Sie sie mit der [Open-Source-Version des](https://github.com/microsoft/iomt-fhir) IoT-Connectors.

In diesem Artikel wird beschrieben, wie Sie IoTJsonPathContentTemplate-Zuordnungen mit IoT-Connector-Gerätezuordnungsvorlagen verwenden.

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

> [!TIP]
> Informationen zur Behebung häufiger Fehler und [Probleme finden](./iot-troubleshoot-guide.md) Sie im Leitfaden zur Problembehandlung für IoT-Connectors.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie Gerätezuordnungen verwenden. Informationen zur Verwendung von FHIR-Zielzuordnungen finden Sie unter

>[!div class="nextstepaction"]
>[Verwenden von FHIR-Zielzuordnungen](how-to-use-fhir-mappings.md)

(FHIR&#174;) ist eine registrierte Marke von [HL7](https://hl7.org/fhir/) und wird mit der Berechtigung von HL7 verwendet.
