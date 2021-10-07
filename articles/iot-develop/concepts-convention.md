---
title: IoT Plug & Play-Konventionen | Microsoft-Dokumentation
description: Beschreibung der Konventionen, bei denen IoT Plug & Play erwartet, dass sie von Geräten verwendet werden, wenn sie Telemetriedaten und Eigenschaften senden und Befehle und Aktualisierungen von Eigenschaften durchführen.
author: rido-min
ms.author: rmpablos
ms.date: 07/10/2020
ms.topic: conceptual
ms.service: iot-develop
services: iot-develop
ms.openlocfilehash: fc8992e8e602f4a92d870328b6da14dde06af087
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128670821"
---
# <a name="iot-plug-and-play-conventions"></a>IoT Plug & Play-Konventionen

Für IoT Plug & Play-Geräte muss beim Austauschen von Nachrichten mit einem IoT-Hub eine Reihe von Konventionen eingehalten werden. Für IoT Plug & Play-Geräte wird für die Kommunikation mit IoT Hub das MQTT-Protokoll verwendet.

Geräte können [Module](../iot-hub/iot-hub-devguide-module-twins.md) enthalten oder in einem [IoT Edge-Modul](../iot-edge/about-iot-edge.md) implementiert werden, das von der IoT Edge-Runtime gehostet wird.

Die Telemetriedaten, Eigenschaften und Befehle, die ein Gerät in IoT Plug & Play implementiert, werden mit einem [Digital Twins Definition Language v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)-_Modell_ beschrieben. In diesem Artikel werden zwei Modelltypen erläutert:

- **Ohne Komponenten**: Ein Modell ohne Komponenten. Bei diesem Modell werden Telemetriedaten, Eigenschaften und Befehle im Inhaltsabschnitt der Hauptschnittstelle als Eigenschaften der obersten Ebene deklariert. Im Azure IoT-Explorer-Tool wird dieses Modell als einzelne _Standardkomponente_ angezeigt.
- **Mit mehreren Komponenten**: Ein Modell, das aus mindestens zwei Schnittstellen besteht. Eine Hauptschnittstelle, die als _Standardkomponente_ mit Telemetriedaten, Eigenschaften und Befehlen angezeigt wird. Und aus mindestens einer Schnittstelle, die als Komponente mit zusätzlichen Telemetriedaten, Eigenschaften und Befehlen deklariert wird.

Weitere Informationen finden Sie unter [Leitfaden: Modellierung mit IoT Plug & Play](concepts-modeling-guide.md).

## <a name="identify-the-model"></a>Identifizieren des Modells

Zur Ankündigung des Modells, das vom IoT Plug & Play-Gerät oder -Modul implementiert wird, wird die Modell-ID in das MQTT-Verbindungspaket eingebunden, indem dem Feld `USERNAME` der Eintrag `model-id` hinzugefügt wird.

Um das von einem Gerät oder einem Modul implementierte Modell zu identifizieren, kann ein Dienst die Modell-ID vom folgenden Ort abrufen:

- Vom `modelId`-Feld des Gerätezwillings
- Vom `$metadata.$model`-Feld des digitalen Zwillings
- Aus einer Änderungsbenachrichtigung bei einem digitalen Zwilling

## <a name="telemetry"></a>Telemetrie

Telemetriedaten, die von einem Gerät ohne Komponenten gesendet werden, erfordern keine zusätzlichen Metadaten. Vom System wird die `dt-dataschema`-Eigenschaft hinzugefügt.

Telemetriedaten, die von einem Gerät mit mehreren Komponenten gesendet werden, müssen zusätzlich `$.sub` als Nachrichteneigenschaft enthalten. Vom System werden die Eigenschaften `dt-subject` und `dt-dataschema` hinzugefügt.

## <a name="read-only-properties"></a>Schreibgeschützte Eigenschaften

### <a name="sample-no-component-read-only-property"></a>Beispiel für eine schreibgeschützte Eigenschaft eines Geräts ohne Komponenten

Ein Gerät oder Modul kann eine gültige JSON-Nachricht senden, die den DTDL v2-Regeln entspricht.

DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:example: Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "temperature",
      "schema": "double"
    }
  ]
}
```

Beispiel für Nutzdaten der gemeldeten Eigenschaft:

```json
"reported" :
{
  "temperature" : 21.3
}
```

### <a name="sample-multiple-components-read-only-property"></a>Beispiel für eine schreibgeschützte Eigenschaft eines Geräts mit mehreren Komponenten

Das Gerät oder Modul muss zusätzlich den Marker `{"__t": "c"}` aufweisen, um anzugeben, dass das Element auf eine Komponente verweist.

DTDL zum Verweisen auf eine Komponente:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "contents": [
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1"
    }
  ]
}
```

DTDL zum Definieren der Komponente:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "temperature",
      "schema": "double"
    }
  ]
}
```

Beispiel für Nutzdaten der gemeldeten Eigenschaft:

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "temperature": 21.3
  }
}
```

## <a name="writable-properties"></a>Schreibbare Eigenschaften

Das Gerät oder Modul muss bestätigen, dass es die Eigenschaft erhalten hat, indem es eine gemeldete Eigenschaft sendet. Die gemeldete Eigenschaft muss Folgendes enthalten:

- `value`: den tatsächlichen Wert der Eigenschaft (in der Regel der empfangene Wert, aber das Gerät kann bei Bedarf einen anderen Wert melden).
- `ac`: einen Bestätigungscode, der einen HTTP-Statuscode enthält
- `av`: eine Bestätigungsversion, die auf die `$version` der gewünschten Eigenschaft verweist. Sie finden diesen Wert in den JSON-Nutzdaten der gewünschten Eigenschaft.
- `ad`: eine optionale Beschreibung der Bestätigung

Nachdem ein Gerät gestartet wurde, sollte es den Gerätezwilling anfordern und eine Überprüfung auf Updates für schreibbare Eigenschaften durchführen. Wenn sich die Versionsnummer einer schreibbaren Eigenschaft erhöht hat, während sich das Gerät im Offlinezustand befunden hat, sollte vom Gerät eine Antwort vom Typ „Gemeldete Eigenschaft“ gesendet werden, um den Empfang des Updates zu bestätigen.

Wenn ein Gerät zum ersten Mal gestartet wird, kann es einen Anfangswert für eine gemeldete Eigenschaft senden, falls es vom Hub keine anfängliche gewünschte Eigenschaft erhält. In diesem Fall sollte `av` vom Gerät auf `1` festgelegt werden. Beispiel:

```json
"reported": {
  "targetTemperature": {
    "value": 20.0,
    "ac": 200,
    "av": 1,
    "ad": "initialize"
  }
}
```

Ein Gerät kann die gemeldete Eigenschaft verwenden, um weitere Informationen für den Hub bereitzustellen. Beispielsweise kann das Gerät mit einer Reihe von Meldungen vom Typ „Wird ausgeführt“ antworten, z. B.:

```json
"reported": {
  "targetTemperature": {
    "value": 35.0,
    "ac": 202,
    "av": 3,
    "ad": "In-progress - reporting current temperature"
  }
}
```

Wenn das Gerät die Zieltemperatur erreicht, sendet es die folgende Meldung:

```json
"reported": {
  "targetTemperature": {
    "value": 20.0,
    "ac": 200,
    "av": 3,
    "ad": "Reached target temperature"
  }
}
```

Ein Gerät kann einen Fehler melden, z. B.:

```json
"reported": {
  "targetTemperature": {
    "value": 120.0,
    "ac": 500,
    "av": 3,
    "ad": "Target temperature out of range. Valid range is 10 to 99."
  }
}
```

### <a name="object-type"></a>Objekttyp

Wenn eine schreibbare Eigenschaft als Objekt definiert wird, muss der Dienst ein vollständiges Objekt an das Gerät senden. Das Gerät sollte das Update bestätigen, indem es ausreichende Informationen zurück an den Dienst sendet, damit er nachvollziehen kann, wie das Gerät bei dem Update reagiert hat. Diese Antwort könnte Folgendes enthalten:

- Das gesamte Objekt.
- Nur die Felder, die das Gerät aktualisiert hat.
- Eine Teilmenge der Felder.

Bei großen Objekten sollten Sie erwägen, die Größe des Objekts, das Sie in die Bestätigung einbeziehen, zu minimieren.

Das folgende Beispiel zeigt eine schreibbare Eigenschaft, die als `Object` mit vier Feldern definiert wurde:

DTDL:

```json
{
  "@type": "Property",
  "name": "samplingRange",
  "schema": {
    "@type": "Object",
    "fields": [
      {
        "name": "startTime",
        "schema": "dateTime"
      },
      {
        "name": "lastTime",
        "schema": "dateTime"
      },
      {
        "name": "count",
        "schema": "integer"
      },
      {
        "name": "errorCount",
        "schema": "integer"
      }
    ]
  },
  "displayName": "Sampling range"
  "writable": true
}
```

Senden Sie zum Aktualisieren dieser schreibbaren Eigenschaft ein vollständiges Objekt aus dem Dienst, das so aussieht:

```json
{
  "samplingRange": {
    "startTime": "2021-08-17T12:53:00.000Z",
    "lastTime": "2021-08-17T14:54:00.000Z",
    "count": 100,
    "errorCount": 5
  }
}
```

Das Gerät antwortet mit einer Bestätigung, die so aussieht:

```json
{
  "samplingRange": {
    "ac": 200,
    "av": 5,
    "ad": "Weighing status updated",
    "value": {
      "startTime": "2021-08-17T12:53:00.000Z",
      "lastTime": "2021-08-17T14:54:00.000Z",
      "count": 100,
      "errorCount": 5
    }
  }
}
```

### <a name="sample-no-component-writable-property"></a>Beispiel für eine schreibbare Eigenschaft eines Geräts ohne Komponenten

Wenn ein Gerät mehrere gewünschte Eigenschaften in einem einzelnen Nutzdatenelement empfängt, kann es die gemeldeten Eigenschaftenantworten über mehrere Nutzdatenelemente hinweg senden oder die Antworten in einem einzelnen Nutzdatenelement kombinieren.

Ein Gerät oder Modul kann eine gültige JSON-Nachricht senden, die den DTDL v2-Regeln entspricht:

DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:example: Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "targetTemperature",
      "schema": "double",
      "writable": true
    },
    {
      "@type": "Property",
      "name": "targetHumidity",
      "schema": "double",
      "writable": true
    }
  ]
}
```

Beispiel für Nutzdaten einer gewünschten Eigenschaft:

```json
"desired" :
{
  "targetTemperature" : 21.3,
  "targetHumidity" : 80,
  "$version" : 3
}
```

Beispiel für erstes Nutzdatenelement der gemeldeten Eigenschaft:

```json
"reported": {
  "targetTemperature": {
    "value": 21.3,
    "ac": 200,
    "av": 3,
    "ad": "complete"
  }
}
```

Beispiel für zweites Nutzdatenelement der gemeldeten Eigenschaft:

```json
"reported": {
  "targetHumidity": {
    "value": 80,
    "ac": 200,
    "av": 3,
    "ad": "complete"
  }
}
```

> [!NOTE]
> Sie könnten diese beiden gemeldeten Eigenschaftennutzdaten in einem einzelnen Nutzdatenelement kombinieren.

### <a name="sample-multiple-components-writable-property"></a>Beispiel für eine schreibbare Eigenschaft eines Geräts mit mehreren Komponenten

Das Gerät oder Modul muss zusätzlich den Marker `{"__t": "c"}` aufweisen, um anzugeben, dass das Element auf eine Komponente verweist.

Der Marker wird nur für Updates von Eigenschaften gesendet, die in einer Komponente definiert sind. Updates von Eigenschaften, die in der Standardkomponente definiert sind, enthalten den Marker nicht. Weitere Informationen finden Sie unter [Beispiel für eine schreibbare Eigenschaft eines Geräts ohne Komponenten](#sample-no-component-writable-property).

Wenn ein Gerät mehrere gemeldete Eigenschaften in einem einzelnen Nutzdatenelement empfängt, kann es die gemeldeten Eigenschaftenantworten über mehrere Nutzdatenelemente hinweg senden oder die Antworten in einem einzelnen Nutzdatenelement kombinieren.

Das Gerät oder Modul muss bestätigen, dass es die Eigenschaften erhalten hat, indem es gemeldete Eigenschaften sendet:

DTDL zum Verweisen auf eine Komponente:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "contents": [
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1"
    }
  ]
}
```

DTDL zum Definieren der Komponente:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "targetTemperature",
      "schema": "double",
      "writable": true
    }
  ]
}
```

Beispiel für Nutzdaten einer gewünschten Eigenschaft:

```json
"desired": {
  "thermostat1": {
    "__t": "c",
    "targetTemperature": 21.3,
    "targetHumidity": 80,
    "$version" : 3
  }
}
```

Beispiel für erstes Nutzdatenelement der gemeldeten Eigenschaft:

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "targetTemperature": {
      "value": 23,
      "ac": 200,
      "av": 3,
      "ad": "complete"
    }
  }
}
```

Beispiel für zweites Nutzdatenelement der gemeldeten Eigenschaft:

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "targetHumidity": {
      "value": 80,
      "ac": 200,
      "av": 3,
      "ad": "complete"
    }
  }
}
```

> [!NOTE]
> Sie könnten diese beiden gemeldeten Eigenschaftennutzdaten in einem einzelnen Nutzdatenelement kombinieren.

## <a name="commands"></a>Befehle

Bei Schnittstellen ohne Komponenten wird der Befehlsname ohne Präfix verwendet.

Bei einem Gerät oder Modul werden bei Schnittstellen mit mehreren Komponenten Befehlsnamen im folgenden Format verwendet: `componentName*commandName`.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich mit IoT Plug & Play-Konventionen vertraut gemacht haben, finden Sie weitere Informationen in folgenden Artikeln:

- [Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C-Geräte-SDK](/azure/iot-hub/iot-c-sdk-ref/)
- [IoT-REST-API](/rest/api/iothub/device)
- [Leitfaden: Modellierung mit IoT Plug & Play](concepts-modeling-guide.md)
