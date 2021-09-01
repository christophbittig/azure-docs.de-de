---
title: Grundlegendes zu IoT Plug & Play-Gerätemodellen | Microsoft-Dokumentation
description: Erfahren Sie mehr zur Modelliersprache DTDL (Digital Twins Definition Language) für IoT Plug & Play-Geräte. In diesem Artikel werden primitive und komplexe Datentypen, Wiederverwendungsmuster mit Komponenten und Vererbung sowie semantische Typen beschrieben. In diesem Artikel finden Sie Leitlinien zur Wahl des Modellbezeichners des Gerätezwillings und zu den für die Modellerstellung unterstützten Tools.
author: dominicbetts
ms.author: dobett
ms.date: 03/09/2021
ms.topic: conceptual
ms.service: iot-develop
services: iot-develop
ms.openlocfilehash: b79c72f6711519c87cb671ea5b7d72eaffabe7e2
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2021
ms.locfileid: "114406445"
---
# <a name="iot-plug-and-play-modeling-guide"></a>Leitfaden zur Modellierung mit IoT Plug & Play

Das Herzstück von IoT Plug & Play ist ein _Gerätemodell_, das die Funktionen eines Geräts für eine IoT Plug & Play-fähige Anwendung beschreibt. Dieses Modell ist als ein Satz von Schnittstellen strukturiert, die Folgendes definieren:

- _Eigenschaften_, die den schreibgeschützten oder schreibbaren Status eines Geräts oder einer anderen Entität darstellen. Beispielsweise kann eine Geräteseriennummer eine schreibgeschützte Eigenschaft sein und die Zieltemperatur eines Thermostats eine schreibbare Eigenschaft.
- _Telemetriefelder_, die die von einem Gerät ausgegebenen Daten bestimmen, und zwar unabhängig davon, ob es sich bei den Daten um einen herkömmlichen Datenstrom mit Sensormesswerten, einen gelegentlichen Fehler oder eine Informationsmeldung handelt.
- _Befehle_, die eine Funktion oder einen Vorgang beschreiben, die bzw. der auf einem Gerät ausgeführt werden kann. Durch einen Befehl kann beispielsweise ein Gateway neu gestartet oder ein Foto mit einer Remotekamera aufgenommen werden.

Weitere Informationen dazu, wie IoT Plug & Play mit Gerätemodellen arbeitet, finden Sie im [Leitfaden für IoT Plug & Play-Geräteentwickler](concepts-developer-guide-device.md) und [Leitfaden für IoT Plug & Play-Dienstentwickler](concepts-developer-guide-service.md).

Zum Definieren eines Modells verwenden Sie DTDL (Digital Twins Definition Language). DTDL nutzt eine JSON-Variante namens [JSON-LD](https://json-ld.org/). Der folgende Codeausschnitt zeigt das Modell eines Thermostatgeräts mit folgenden Merkmalen:

- Eindeutige Modell-ID: `dtmi:com:example:Thermostat;1`
- Sendet Temperaturtelemetriedaten
- Beschreibbare Eigenschaft zum Festlegen der Zieltemperatur
- Schreibgeschützte Eigenschaft zum Melden der Maximaltemperatur seit dem letzten Neustart
- Reaktion auf einen Befehl, der die maximale, minimale und durchschnittliche Temperatur im Verlauf eines Zeitraums abfragt

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "targetTemperature",
      "schema": "double",
      "displayName": "Target Temperature",
      "description": "Allows to remotely specify the desired target temperature.",
      "unit": "degreeCelsius",
      "writable": true
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "maxTempSinceLastReboot",
      "schema": "double",
      "unit": "degreeCelsius",
      "displayName": "Max temperature since last reboot.",
      "description": "Returns the max temperature since last device reboot."
    },
    {
      "@type": "Command",
      "name": "getMaxMinReport",
      "displayName": "Get Max-Min report.",
      "description": "This command returns the max, min and average temperature from the specified time to the current time.",
      "request": {
        "name": "since",
        "displayName": "Since",
        "description": "Period to return the max-min report.",
        "schema": "dateTime"
      },
      "response": {
        "name": "tempReport",
        "displayName": "Temperature Report",
        "schema": {
          "@type": "Object",
          "fields": [
            {
              "name": "maxTemp",
              "displayName": "Max temperature",
              "schema": "double"
            },
            {
              "name": "minTemp",
              "displayName": "Min temperature",
              "schema": "double"
            },
            {
              "name": "avgTemp",
              "displayName": "Average Temperature",
              "schema": "double"
            },
            {
              "name": "startTime",
              "displayName": "Start Time",
              "schema": "dateTime"
            },
            {
              "name": "endTime",
              "displayName": "End Time",
              "schema": "dateTime"
            }
          ]
        }
      }
    }
  ]
}
```

Das Thermostatmodell hat nur eine Schnittstelle. In späteren Beispielen in diesem Artikel werden komplexere Modelle mit Komponenten und Vererbung vorgestellt.

In diesem Artikel wird beschrieben, wie Sie Ihre eigenen Modelle entwerfen und erstellen. Dabei werden Themen wie Datentypen, Modellstruktur und Tools behandelt.

Weitere Informationen finden Sie in der Spezifikation [Digital Twins Definition Language v2](https://github.com/Azure/opendigitaltwins-dtdl).

## <a name="model-structure"></a>Modellstruktur

Eigenschaften, Telemetriedaten und Befehle sind in Schnittstellen gruppiert. In diesem Abschnitt wird beschrieben, wie Sie mithilfe von Schnittstellen einfache und komplexe Modelle unter Verwendung von Komponenten und Vererbung beschreiben können.

### <a name="model-ids"></a>Modell-IDs

Jede Schnittstelle verfügt über einen eindeutigen Modellbezeichner für digitale Zwillinge (Digital Twin Model Identifier, DTMI). Komplexe Modelle verwenden DTMIs, um Komponenten zu bestimmen. Anwendungen können anhand der von Geräten gesendeten DTMIs in einem Repository nach Modelldefinitionen suchen.

DTMIs müssen der Benennungskonvention folgen, die vom [Repository für IoT Plug & Play-Modelle](https://github.com/Azure/iot-plugandplay-models) gefordert wird:

- Das DTMI-Präfix ist `dtmi:`.
- Das DTMI-Suffix ist die Versionsnummer des Modells, z. B. `;2`.
- Der Hauptteil des DTMI ist dem Ordner und der Datei im Modellrepository zugeordnet, in dem das Modell gespeichert ist. Die Versionsnummer ist Teil des Dateinamens.

Beispielsweise wird das vom DTMI `dtmi:com:Example:Thermostat;2` identifizierte Modell in der Datei *dtmi/com/example/thermostat-2.json* gespeichert.

Der folgende Ausschnitt zeigt den Aufbau einer Schnittstellendefinition mit ihrem eindeutigen DTMI:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;2",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    ...
  ]
}
```

### <a name="no-components"></a>Keine Komponenten

Ein einfaches Modell, wie das zuvor gezeigte Thermostat, hat weder eingebettete noch kaskadierende Komponenten. Telemetrie, Eigenschaften und Befehle werden im Knoten `contents` der Schnittstelle definiert.

Das folgende Beispiel zeigt einen Teil eines einfachen Modells, das keine Komponenten verwendet:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
...
```

Tools wie Azure IoT Explorer und der IoT Central-Gerätevorlagen-Designer bezeichnen eine eigenständige Schnittstelle wie das Thermostat als _Standardkomponente_.

Der folgende Screenshot zeigt, wie das Modell im Azure IoT-Explorertool angezeigt wird:

:::image type="content" source="media/concepts-modeling-guide/default-component.png" alt-text="Standardkomponente im Azure IoT-Explorer":::

Der folgende Screenshot zeigt, wie das Modell im IoT Central-Gerätevorlagen-Designer als Standardkomponente angezeigt wird. Wählen Sie **Identität anzeigen** aus, um den DTMI des Modells anzuzeigen:

:::image type="content" source="media/concepts-modeling-guide/iot-central-model.png" alt-text="Screenshot des Thermostatmodells im IoT Central-Designer":::

Die Modell-ID wird in einer Eigenschaft für Gerätezwillinge gespeichert, wie der folgenden Screenshot zeigt:

:::image type="content" source="media/concepts-modeling-guide/twin-model-id.png" alt-text="Modell-ID in Eigenschaft für digitalen Zwilling":::

Ein DTDL-Modell ohne Komponenten ist eine nützliche Vereinfachung für Geräte oder IoT Edge-Module mit nur einer Gruppe von Telemetriedaten, Eigenschaften und Befehlen. Mit einem Modell, das keine Komponenten verwendet, können Sie aus einem vorhandenen Gerät oder Modul ganz einfach ein IoT Plug & Play-Gerät oder -Modul machen. Sie erstellen ein DTDL-Modell, das Ihr tatsächliches Gerät oder Modul beschreibt, ohne dass dabei Komponenten definiert werden müssen.

> [!TIP]
> Ein Modul kann ein [Gerätemodul](../iot-hub/iot-hub-devguide-module-twins.md) oder ein [IoT Edge-Modul](../iot-edge/about-iot-edge.md) sein.

### <a name="reuse"></a>Wiederverwenden

Es gibt zwei Möglichkeiten zur Wiederverwendung von Schnittstellendefinitionen. Lassen Sie mehrere Komponenten in einem Modell auf andere Schnittstellendefinitionen verweisen. Erweitern Sie Schnittstellendefinitionen mithilfe von Vererbung.

### <a name="multiple-components"></a>Mehrere Komponenten

Mithilfe von Komponenten können Sie eine Modellschnittstelle als eine Assembly aus anderen Schnittstellen erstellen.

Die [Thermostatschnittstelle](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) ist zum Beispiel als Modell definiert. Sie können diese Schnittstelle als eine oder mehrere Komponenten einbinden, wenn Sie das [Temperature Controller-Modell](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definieren. Im folgenden Beispiel werden diese Komponenten als `thermostat1` und `thermostat2` bezeichnet.

Ein DTDL-Modell mit mehreren Komponenten verfügt über mindestens zwei Komponentenabschnitte. In jedem Abschnitt ist `@type` auf `Component` festgelegt und ein expliziter Verweis auf ein Schema enthalten, wie im folgenden Codeausschnitt zu sehen:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "description": "Device with two thermostats and remote reboot.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "DataSize"
      ],
      "name": "workingSet",
      "displayName": "Working Set",
      "description": "Current working set of the device memory in KiB.",
      "schema": "double",
      "unit": "kibibyte"
    },
    {
      "@type": "Property",
      "name": "serialNumber",
      "displayName": "Serial Number",
      "description": "Serial number of the device.",
      "schema": "string"
    },
    {
      "@type": "Command",
      "name": "reboot",
      "displayName": "Reboot",
      "description": "Reboots the device after waiting the number of seconds specified.",
      "request": {
        "name": "delay",
        "displayName": "Delay",
        "description": "Number of seconds to wait before rebooting the device.",
        "schema": "integer"
      }
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1",
      "displayName": "Thermostat One",
      "description": "Thermostat One of Two."
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat2",
      "displayName": "Thermostat Two",
      "description": "Thermostat Two of Two."
    },
    {
      "@type": "Component",
      "schema": "dtmi:azure:DeviceManagement:DeviceInformation;1",
      "name": "deviceInformation",
      "displayName": "Device Information interface",
      "description": "Optional interface with basic device hardware information."
    }
  ]
}
```

Für dieses Modell sind im Inhaltsabschnitt drei Komponenten definiert: zwei `Thermostat`-Komponenten und eine `DeviceInformation`-Komponente. Der Inhaltsabschnitt enthält auch Eigenschafts-, Telemetrie- und Befehlsdefinitionen.

Die folgenden Screenshots zeigen, wie dieses Modell in IoT Central angezeigt wird. Die Eigenschafts-, Telemetrie- und Befehlsdefinitionen im Temperaturregler werden in der **Standardkomponente** auf der obersten Ebene gezeigt. Die Eigenschafts-, Telemetrie- und Befehlsdefinitionen für die einzelnen Thermostate werden in den Komponentendefinitionen gezeigt:

:::image type="content" source="media/concepts-modeling-guide/temperature-controller.png" alt-text="Screenshot mit der Gerätevorlage für den Temperaturregler in IoT Central":::

:::image type="content" source="media/concepts-modeling-guide/temperature-controller-components.png" alt-text="Screenshot mit den Thermostatkomponenten in der Gerätevorlage für den Temperaturregler in IoT Central":::

Informationen zum Schreiben von Gerätecode, der mit Komponenten interagiert, finden Sie im [Leitfaden für IoT Plug & Play-Geräteentwickler](concepts-developer-guide-device.md).

Informationen zum Schreiben von Dienstcode, der mit Komponenten auf einem Gerät interagiert, finden Sie im [Leitfaden für IoT Plug & Play-Dienstentwickler](concepts-developer-guide-service.md).

### <a name="inheritance"></a>Vererbung

Durch Vererbung können Sie die Funktionsmöglichkeiten einer Basisschnittstelle zum Erweitern der Funktionsmöglichkeiten einer Schnittstelle wiederverwenden. Zum Beispiel können mehrere Gerätemodelle Funktionsmöglichkeiten wie eine Seriennummer gemeinsam nutzen:

:::image type="content" source="media/concepts-modeling-guide/inheritance.png" alt-text="Beispiel der Vererbung in einem Gerätemodell mit einem Thermostat und Mengenregler, die Funktionsmöglichkeiten einer Basisschnittstelle gemeinsam nutzen." border="false":::

Der folgende Ausschnitt zeigt ein DTML-Modell, in dem mit dem Schlüsselwort `extends` die im vorherigen Diagramm gezeigte Vererbungsbeziehung definiert wird:

```json
[
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:Thermostat;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double",
        "unit": "degreeCelsius"
      },
      {
        "@type": "Property",
        "name": "targetTemperature",
        "schema": "double",
        "unit": "degreeCelsius",
        "writable": true
      }
    ],
    "extends": [
      "dtmi:com:example:baseDevice;1"
    ]
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:baseDevice;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Property",
        "name": "SerialNumber",
        "schema": "double",
        "writable": false
      }
    ]
  }
]
```

Der folgende Screenshot zeigt dieses Modell in der Umgebung der IoT Central-Gerätevorlage:

:::image type="content" source="media/concepts-modeling-guide/iot-central-inheritance.png" alt-text="Screenshot der Schnittstellenvererbung in IoT Central":::

Wenn Sie geräte- oder dienstseitigen Code schreiben, muss Ihr Code für die Handhabung vererbter Schnittstellen nichts Besonderes tun. In dem in diesem Abschnitt gezeigten Beispiel meldet Ihr Gerätecode die Seriennummer so, als ob sie Teil der Thermostatschnittstelle wäre.

### <a name="tips"></a>Tipps

Beim Erstellen eines Modells können Sie Komponenten und Vererbung kombinieren. Das folgende Diagramm zeigt ein `thermostat`-Modell, das von einer `baseDevice`-Schnittstelle erbt. Die `baseDevice`-Schnittstelle hat eine Komponente, die wiederum von einer anderen Schnittstelle erbt:

:::image type="content" source="media/concepts-modeling-guide/inheritance-components.png" alt-text="Diagramm eines Modells, in dem sowohl Komponenten als auch Vererbung zum Einsatz kommen." border="false":::

Der folgende Ausschnitt zeigt ein DTML-Modell, in dem mit den Schlüsselworten `extends` und `component` die im vorherigen Diagramm gezeigte Vererbungsbeziehung und Verwendung von Komponenten definiert wird:

```json
[
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:Thermostat;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double",
        "unit": "degreeCelsius"
      },
      {
        "@type": "Property",
        "name": "targetTemperature",
        "schema": "double",
        "unit": "degreeCelsius",
        "writable": true
      }
    ],
    "extends": [
      "dtmi:com:example:baseDevice;1"
    ]
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:baseDevice;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Property",
        "name": "SerialNumber",
        "schema": "double",
        "writable": false
      },
      {
        "@type" : "Component",
        "schema": "dtmi:com:example:baseComponent;1",
        "name": "baseComponent"
      }
    ]
  }
]
```

## <a name="data-types"></a>Datentypen

Mit Datentypen können Sie Telemetrie, Eigenschaften und Befehlsparameter definieren. Datentypen können primitiv oder komplex sein. Komplexe Datentypen arbeiten mit primitiven oder anderen komplexen Typen. Die maximale Tiefe komplexer Typen ist fünf Ebenen.

### <a name="primitive-types"></a>Einfache Typen

Die folgende Tabelle zeigt die Gruppe einfacher Typen, die Sie verwenden können:

| Primitiver Typ | BESCHREIBUNG |
| --- | --- |
| `boolean` | Boolescher Wert |
| `date` | Vollständiges Datum gemäß Definition im [Abschnitt 5.6 von RFC 3339](https://tools.ietf.org/html/rfc3339#section-5.6) |
| `dateTime` | Datum und Uhrzeit gemäß Definition in [RFC 3339](https://tools.ietf.org/html/rfc3339) |
| `double` | Gleitkommazahl mit 8 Byte gemäß IEEE |
| `duration` | Dauer im ISO 8601-Format |
| `float` | Gleitkommazahl mit 4 Byte gemäß IEEE |
| `integer` | Ganze Zahl mit 4 Byte und Vorzeichen |
| `long` | Ganze Zahl mit 8 Byte und Vorzeichen |
| `string` | UTF-8-Zeichenfolge |
| `time` | Vollständige Uhrzeit gemäß Definition im [Abschnitt 5.6 von RFC 3339](https://tools.ietf.org/html/rfc3339#section-5.6) |

Der folgende Ausschnitt zeigt ein Beispiel einer Telemetriedefinition mit dem Typ `double` im Feld `schema`:

```json
{
  "@type": "Telemetry",
  "name": "temperature",
  "displayName": "Temperature",
  "schema": "double"
}
```

### <a name="complex-datatypes"></a>Komplexe Datentypen

Komplexe Datentypen haben den Typ *Array*, *Enumeration*, *Zuordnung*, *Objekt* oder einen räumlich Typ.

#### <a name="arrays"></a>Arrays

Ein Array ist ein indizierbarer Datentyp, bei dem alle Elemente den gleichen Typ haben. Der Elementtyp kann einfach oder komplex sein.

Der folgende Ausschnitt zeigt ein Beispiel einer Telemetriedefinition mit dem Typ `Array` im Feld `schema`. Die Elemente des Arrays sind boolesche Werte:

```json
{
  "@type": "Telemetry",
  "name": "ledState",
  "schema": {
    "@type": "Array",
    "elementSchema": "boolean"
  }
}
```

#### <a name="enumerations"></a>Enumerationen

Eine Enumeration beschreibt einen Typ mit einer Gruppe benannter Bezeichnungen, die Werten zugeordnet werden. Die Werte können entweder ganze Zahlen oder Zeichenfolgen sein, die Bezeichnungen sind jedoch stets Zeichenfolgen.

Der folgende Ausschnitt zeigt ein Beispiel einer Telemetriedefinition mit dem Typ `Enum` im Feld `schema`. Die Werte in der Enumeration sind ganze Zahlen:

```json
{
  "@type": "Telemetry",
  "name": "state",
  "schema": {
    "@type": "Enum",
    "valueSchema": "integer",
    "enumValues": [
      {
        "name": "offline",
        "displayName": "Offline",
        "enumValue": 1
      },
      {
        "name": "online",
        "displayName": "Online",
        "enumValue": 2
      }
    ]
  }
}
```

#### <a name="maps"></a>Maps

Eine Zuordnung ist ein Typ mit Schlüssel-Wert-Paaren, bei dem die Werte alle den gleichen Typ haben. Der Schlüssel in einer Zuordnung muss eine Zeichenfolge sein. Die Werte in einer Zuordnung können einen beliebigen Typ haben, auch einen anderen komplexen Typ.

Der folgende Ausschnitt zeigt ein Beispiel einer Eigenschaftsdefinition mit dem Typ `Map` im Feld `schema`. Die Werte in der Zuordnung sind Zeichenfolgen:

```json
{
  "@type": "Property",
  "name": "modules",
  "writable": true,
  "schema": {
    "@type": "Map",
    "mapKey": {
      "name": "moduleName",
      "schema": "string"
    },
    "mapValue": {
      "name": "moduleState",
      "schema": "string"
    }
  }
}
```

### <a name="objects"></a>Objekte

Ein Objekttyp besteht aus benannten Feldern. Die Typen der Felder in einer Objektzuordnung können primitiv oder komplex sein.

Der folgende Ausschnitt zeigt ein Beispiel einer Telemetriedefinition mit dem Typ `Object` im Feld `schema`. Die Felder im Objekt haben die Typen `dateTime`, `duration` und `string`:

```json
{
  "@type": "Telemetry",
  "name": "monitor",
  "schema": {
    "@type": "Object",
    "fields": [
      {
        "name": "start",
        "schema": "dateTime"
      },
      {
        "name": "interval",
        "schema": "duration"
      },
      {
        "name": "status",
        "schema": "string"
      }
    ]
  }
}
```

#### <a name="geospatial-types"></a>Räumliche Typen

DTDL bietet eine Reihe räumlicher auf [GeoJSON](https://geojson.org/) basierender Typen zur Modellierung geografischer Datenstrukturen: `point`, `multiPoint`, `lineString`, `multiLineString`, `polygon`, und `multiPolygon`. Diese Typen sind vordefinierte geschachtelte Strukturen von Arrays, Objekten und Enumerationen.

Der folgende Ausschnitt zeigt ein Beispiel einer Telemetriedefinition mit dem Typ `point` im Feld `schema`:

```json
{
  "@type": "Telemetry",
  "name": "location",
  "schema": "point"
}
```

Da die räumlichen Typen arraybasiert sind, werden sie derzeit nicht in Eigenschaftsdefinitionen unterstützt.

## <a name="semantic-types"></a>Semantische Typen

Der Datentyp einer Eigenschafts- oder Telemetriedefinition legt das Format der Daten fest, die ein Gerät mit einem Dienst austauscht. Der semantische Typ liefert Informationen zu Telemetrie und Eigenschaften, anhand derer eine Anwendung bestimmen kann, wie ein Wert verarbeitet oder angezeigt werden soll. Zu jedem semantischen Typ gehören eine oder mehrere Einheiten. Beispielsweise sind Celsius und Fahrenheit Einheiten des semantischen Typs „Temperatur“. IoT Central-Dashboards und -Analysen können anhand der Informationen des semantischen Typs bestimmen, wie Telemetrie- oder Eigenschaftswerte dargestellt und Einheiten angezeigt werden sollen. Informationen zum Lesen der semantischen Typen mithilfe des Modellparsers finden Sie unter [Informationen zum Modellparser „Digital Twins“](concepts-model-parser.md).

Der folgende Ausschnitt zeigt ein Beispiel einer Telemetriedefinition mit Informationen des semantischen Typs. Der semantische Typ `Temperature` wird dem Array `@type` hinzugefügt. Der `unit`-Wert `degreeCelsius` ist eine der gültigen Einheiten für den semantischen Typ:

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

## <a name="localization"></a>Lokalisierung

Anwendungen wie IoT Central können mithilfe der Informationen im Modell dynamisch eine Benutzeroberfläche anhand der Daten erstellen, die mit einem IoT Plug & Play-Gerät ausgetauscht werden. Beispielsweise können die Kacheln auf einem Dashboard Namen und Beschreibungen für Telemetrie, Eigenschaften und Befehle aufweisen.

Die optionalen Felder `description` und `displayName` im Modell enthalten Zeichenfolgen für die Verwendung auf einer Benutzeroberfläche. Diese Felder können lokalisierte Zeichenfolgen enthalten, mit denen eine Anwendung eine lokalisierte Benutzeroberfläche rendern kann.

Der folgende Ausschnitt zeigt ein Beispiel einer Telemetriedefinition für Temperatur mit lokalisierten Zeichenfolgen:

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "description": {
    "en": "Temperature in degrees Celsius.",
    "it": "Temperatura in gradi Celsius."
  },
  "displayName": {
    "en": "Temperature",
    "it": "Temperatura"
  },
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

Das Hinzufügen lokalisierter Zeichenfolgen ist optional. Das folgende Beispiel enthält nur eine einzige Standardsprache:

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "description": "Temperature in degrees Celsius.",
  "displayName": "Temperature",
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

## <a name="lifecycle-and-tools"></a>Lebenszyklus und Tools

Die vier Phasen im Lebenszyklus eines Gerätemodells sind *Erstellung*, *Veröffentlichung*, *Nutzung* und *Versionsverwaltung*:

### <a name="author"></a>Autor

DTML-Gerätemodelle sind JSON-Dokumente, die in einem Texteditor erstellt werden können. In IoT Central können Sie jedoch ein DTML-Modell über die grafische Benutzeroberfläche der Gerätevorlage erstellen. IoT Central ermöglicht Folgendes:

- Erstellen von Schnittstellen, die Eigenschaften, Telemetrie und Befehle definieren
- Zusammenfügen mehrerer Schnittstellen mithilfe von Komponenten
- Definieren von Vererbungsbeziehungen zwischen Schnittstellen
- Importieren und Exportieren von DTML-Modelldateien

Weitere Informationen finden Sie unter [Definieren eines neuen IoT-Gerätetyps in Ihrer Azure IoT Central-Anwendung](../iot-central/core/howto-set-up-template.md).

Es gibt DTDL-Erstellungserweiterungen sowohl für Visual Studio Code als auch für Visual Studio 2019.

Wechseln Sie zur Installation der DTDL-Erweiterung für Visual Studio Code zum [DTDL-Editor für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl). Sie können auch in Visual Studio Code in der Ansicht **Erweiterungen** nach **DTDL** suchen.

Wenn Sie die Erweiterung installiert haben, erstellen Sie DTDL-Modelldateien in Visual Studio Code damit:

- Die Erweiterung bietet Syntaxüberprüfung in DTDL-Modelldateien und hebt Fehler hervor, wie im folgenden Screenshot zu sehen ist:

    :::image type="content" source="media/concepts-modeling-guide/model-validation.png" alt-text="Modellüberprüfung in VS Code":::

- Verwenden Sie IntelliSense und das Feature „AutoVervollständigen“ beim Bearbeiten von DTDL-Modellen:

    :::image type="content" source="media/concepts-modeling-guide/model-intellisense.png" alt-text="Verwenden von IntelliSense für DTDL-Modelle in VS Code":::

- Erstellen Sie eine neue DTDL-Schnittstelle. Der Befehl **DTDL: Schnittstelle erstellen** erstellt eine JSON-Datei mit einer neuen Schnittstelle. Die Schnittstelle enthält Beispiele für Telemetrie, Eigenschaften und Befehlsdefinitionen.

Wechseln Sie zur Installation der DTDL-Erweiterung für Visual Studio 2019 zur [DTDL-Sprachunterstützung für Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16dtdllanguagesupport). Sie können auch in Visual Studio unter **Erweiterungen verwalten** nach **DTDL** suchen.

Wenn Sie die Erweiterung installiert haben, erstellen Sie DTDL-Modelldateien in Visual Studio damit:

- Die Erweiterung bietet Syntaxüberprüfung in DTDL-Modelldateien und hebt Fehler hervor, wie im folgenden Screenshot zu sehen ist:

    :::image type="content" source="media/concepts-modeling-guide/model-validation-2.png" alt-text="Modellüberprüfung in Visual Studio":::

- Verwenden Sie IntelliSense und das Feature „AutoVervollständigen“ beim Bearbeiten von DTDL-Modellen:

    :::image type="content" source="media/concepts-modeling-guide/model-intellisense-2.png" alt-text="Verwenden von IntelliSense für DTDL-Modelle in Visual Studio":::

### <a name="publish"></a>Veröffentlichen

Damit Ihre DTML-Modelle gemeinsam genutzt und gefunden werden können, veröffentlichen Sie sie in einem Repository für Gerätemodelle.

Bevor Sie ein Modell im öffentlichen Repository veröffentlichen, können Sie die `dmr-client`-Tools verwenden, um Ihr Modell zu überprüfen.

Weitere Informationen finden Sie unter [Gerätemodellrepository](concepts-model-repository.md).

### <a name="use"></a>Zweck

Anwendungen wie IoT Central nutzen Gerätemodelle. In IoT Central ist ein Modell Teil der Gerätevorlage, die die Fähigkeiten des Geräts beschreibt. IoT Central erstellt anhand der Gerätevorlage dynamisch eine Benutzeroberfläche für das Gerät, einschließlich Dashboards und Analysen.

Eine benutzerdefinierte Lösung kann den [Modellparser „Digital Twins“](concepts-model-parser.md) verwenden, um die Funktionsmöglichkeiten eines Geräts zu verstehen, mit dem das Modell implementiert wird. Weitere Informationen finden Sie unter [Verwenden von IoT Plug & Play-Modellen in einer IoT-Lösung](concepts-model-discovery.md).

### <a name="version"></a>Version

Um sicherzustellen, dass Geräte und serverseitige Lösungen, die Modelle verwenden, weiterhin funktionieren, sind veröffentlichte Modelle unveränderbar.

Der DTMI enthält eine Versionsnummer, mit der Sie mehrere Versionen eines Modells erstellen können. Geräte und serverseitige Lösungen können die spezifische Version verwenden, die für sie vorgesehen ist.

IoT Central implementiert für Gerätemodelle weitere Versionsverwaltungsregeln. Wenn Sie eine Gerätevorlage und ihr Modell in IoT Central mit einer Version versehen, können Sie Geräte von älteren Versionen zu neueren Versionen migrieren. Migrierte Geräte können die neuen Funktionsmöglichkeiten jedoch nicht ohne ein Upgrade der Firmware nutzen. Weitere Informationen finden Sie unter [Bearbeiten einer Gerätevorlage](../iot-central/core/howto-edit-device-template.md).

## <a name="limits-and-constraints"></a>Grenzen und Einschränkungen

In der folgenden Liste sind einige wichtige Einschränkungen und Obergrenzen für Modelle zusammengefasst:

- Derzeit beträgt die maximale Tiefe für Arrays, Zuordnungen und Objekte fünf Ebenen der Tiefe.
- Arrays werden in Eigenschaftsdefinitionen nicht unterstützt.
- Sie können Schnittstellen auf eine Tiefe von 10 Ebenen erweitern.
- Eine Schnittstelle kann höchstens zwei weitere Schnittstellen erweitern.
- Eine Komponente kann keine andere Komponente enthalten.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich mit der Gerätemodellierung vertraut gemacht haben, finden Sie weitere Informationen in folgenden Artikeln:

- [Digital Twins Definition Language](https://github.com/Azure/opendigitaltwins-dtdl)
- [Azure IoT-Modellrepository](./concepts-model-repository.md)
