---
title: Verwenden der REST-API zum Verwalten von Geräten in Azure IoT Central
description: Verwenden der IoT Central-REST-API zur Steuerung von Geräten in einer Anwendung
author: dominicbetts
ms.author: dobett
ms.date: 06/01/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 2e808ee2d36237a830877e7f043656ce9189bd89
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114461277"
---
# <a name="how-to-use-the-iot-central-rest-api-to-control-devices"></a>Verwenden der IoT Central-REST-API zur Steuerung von Geräten

Mit der IoT Central-REST-API können Sie Clientanwendungen entwickeln, die in IoT Central-Anwendungen integriert werden. Sie können die REST-API verwenden, um Geräte in Ihrer IoT Central-Anwendung zu steuern. Mit der REST-API können Sie Folgendes durchführen:

- Den letzten bekannten Telemetriewert aus einem Gerät lesen.
- Eigenschaftswerte von einem Gerät lesen.
- Schreibbare Eigenschaften auf einem Gerät festlegen.
- Befehle auf einem Gerät aufrufen.

In diesem Artikel wird beschrieben, wie Sie die `/devices/{device_id}`-API zum Steuern einzelner Geräte verwenden. Sie können auch Aufträge verwenden, um Geräte in einem Massenvorgang zu steuern.

Ein Gerät kann die von ihm unterstützten Eigenschaften, Telemetriedaten und Befehle in _Komponenten_ und _Module_ gruppieren.

Jeder IoT Central-REST-API-Aufruf erfordert einen Autorisierungsheader. Weitere Informationen finden Sie unter [Authentifizieren und Autorisieren von IoT Central-REST-API-Aufrufen](howto-authorize-rest-api.md).

Die Referenzdokumentation für die IoT Central-REST-API finden Sie unter [Azure IoT Central: Referenz zur REST-API](/rest/api/iotcentral/).

## <a name="components-and-modules"></a>Komponenten und Module

Mit Komponenten können Sie Gerätefunktionen gruppieren und wiederverwenden. Weitere Informationen zu Komponenten und Gerätemodellen finden Sie im [Leitfaden zur Modellierung mit IoT Plug & Play](../../iot-develop/concepts-modeling-guide.md).

Nicht alle Gerätevorlagen verwenden Komponenten. Der folgende Screenshot zeigt die Gerätevorlage für ein einfaches [Thermostat](https://github.com/Azure/iot-plugandplay-models/blob/main/dtmi/com/example/thermostat-2.json), bei dem alle Funktionen in einer einzigen Schnittstelle definiert sind, die als **Stammkomponente** bezeichnet wird:

:::image type="content" source="media/howto-control-devices-with-rest-api/thermostat-device.png" alt-text="Screenshot eines einfachen Thermostatgeräts ohne Komponente":::

Der folgende Screenshot zeigt eine Gerätevorlage für den [Temperaturcontroller](https://github.com/Azure/iot-plugandplay-models/blob/main/dtmi/com/example/temperaturecontroller-2.json), die Komponenten verwendet. Der Temperaturcontroller verfügt über zwei Thermostatkomponenten und eine Geräteinformationskomponente:

:::image type="content" source="media/howto-control-devices-with-rest-api/temperature-controller-device.png" alt-text="Screenshot: Temperaturcontrollergerät mit zwei Thermostatkomponenten und einer Geräteinformationskomponente":::

In IoT Central bezieht sich ein Modul auf ein IoT Edge-Modul, das auf einem verbundenen IoT Edge-Gerät ausgeführt wird. Ein Modul kann über ein einfaches Modell verfügen, z. B. das Thermostat, das keine Komponenten verwendet. Ein Modul kann auch Komponenten verwenden, um einen komplexeren Satz von Funktionen zu organisieren. Der folgende Screenshot zeigt ein Beispiel für eine Gerätevorlage, die Module verwendet. Das Umgebungssensorgerät verfügt über ein Modul namens `SimulatedTemperatureSensor` und eine geerbte Schnittstelle namens `management`:

:::image type="content" source="media/howto-control-devices-with-rest-api/environmental-sensor-device.png" alt-text="Screenshot eines Umgebungssensorgeräts mit einem Modul":::

## <a name="get-a-device-component"></a>Abrufen einer Gerätekomponente

Verwenden Sie die folgende Anforderung, um die Komponenten von einem Gerät namens `temperature-controller-01` abzurufen:

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/temperature-controller-01/components?api-version=1.0
```

Die Antwort auf diese Anforderung sieht wie das folgende Beispiel aus. Das `value`-Array enthält Details zu den einzelnen Gerätekomponenten:

```json
{
  "value": [
    {
      "@type": "Component",
      "name": "thermostat1",
      "displayName": "Thermostat One",
      "description": "Thermostat One of Two."
    },
    {
      "@type": "Component",
      "name": "thermostat2",
      "displayName": "Thermostat Two",
      "description": "Thermostat Two of Two."
    },
    {
      "@type": "Component",
      "name": "deviceInformation",
      "displayName": "Device Information interface",
      "description": "Optional interface with basic device hardware information."
    }
  ]
}
```

## <a name="get-a-device-module"></a>Abrufen eines Gerätemoduls

Verwenden Sie die folgende Anforderung, um eine Liste der Module abzurufen, die auf einem verbundenen IoT Edge-Gerät namens ausgeführt `environmental-sensor-01` werden:

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/environmental-sensor-01/modules?api-version=1.0
```

Die Antwort auf diese Anforderung sieht wie das folgende Beispiel aus. Das Modularray enthält nur benutzerdefinierte Module, die auf dem IoT Edge-Gerät ausgeführt werden, nicht die integrierten `$edgeAgent`- und `$edgeHub`-Module:

```json
{
  "value": [
    {
      "@type": [
        "Relationship",
        "EdgeModule"
      ],
      "name": "SimulatedTemperatureSensor",
      "displayName": "SimulatedTemperatureSensor"
    }
  ]
}
```

Verwenden Sie die folgende Anforderung, um eine Liste der Komponenten in einem Modul namens `SimulatedTemperatureSensor` abzurufen:

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/environmental-sensor-01/modules?api-version=1.0
```

## <a name="read-telemetry"></a>Lesen der Telemetrie

Verwenden Sie die folgende Anforderung, um den letzten bekannten Telemetriewert von einem Gerät abzurufen, das keine Komponenten verwendet. In diesem Beispiel wird das Gerät als `thermostat-01` und die Telemetrie als `temperature` bezeichnet:

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/thermostat-01/telemetry/temperature?api-version=1.0
```

Die Antwort auf diese Anforderung sieht wie das folgende Beispiel aus:

```json
{
  "timestamp": "2021-03-24T12:33:15.223Z",
  "value": 40.10993804456927
}
```

Verwenden Sie die folgende Anforderung, um den letzten bekannten Telemetriewert von einem Gerät abzurufen, das Komponenten verwendet. In diesem Beispiel wird das Gerät als `temperature-controller-01`, die Komponente als `thermostat2` und die Telemetrie als `temperature` bezeichnet:

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/temperature-controller-01/components/thermostat2/telemetry/temperature?api-version=1.0
```

Die Antwort auf diese Anforderung sieht wie das folgende Beispiel aus:

```json
{
  "timestamp": "2021-03-24T12:43:44.968Z",
  "value": 70.29168040339141
}
```

Wenn es sich bei dem Gerät um ein IoT Edge-Gerät handelt, verwenden Sie die folgende Anforderung, um den letzten bekannten Telemetriewert aus einem Modul abzurufen. In diesem Beispiel wird ein Gerät namens `environmental-sensor-01` mit einem Modul namens `SimulatedTemperatureSensor` und einer Telemetrie namens `ambient` verwendet. Der `ambient`-Telemetrietyp verfügt über Temperatur- und Luftfeuchtigkeitswerte:

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/environmental-sensor-01/modules/SimulatedTemperatureSensor/telemetry/ambient?api-version=1.0
```

Die Antwort auf diese Anforderung sieht wie das folgende Beispiel aus:

```json
{
  "timestamp": "2021-03-25T15:44:34.955Z",
  "value": {
    "temperature": 21.18032378129676,
    "humidity": 25
  }
}
```

> [!TIP]
> Verwenden Sie `/api/devices/{deviceId}/modules/{moduleName}/components/{componentName}/telemetry/{telemetryName}`, um von einer Komponente in einem Modul aus auf die Telemetriedaten zuzugreifen.

## <a name="read-properties"></a>Lesen von Eigenschaften

Verwenden Sie die folgende Anforderung, um die Eigenschaftswerte von einem Gerät abzurufen, das keine Komponenten verwendet. In diesem Beispiel wird das Gerät als `thermostat-01` bezeichnet.

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/thermostat-01/properties?api-version=1.0
```

Die Antwort auf diese Anforderung sieht wie das folgende Beispiel aus. Es zeigt, dass das Gerät einen einzelnen Eigenschaftswert meldet:

```json
{
  "maxTempSinceLastReboot": 93.95907131817654,
  "$metadata": {
    "maxTempSinceLastReboot": {
      "lastUpdateTime": "2021-03-24T12:47:46.7571438Z"
    }
  }
}
```

Verwenden Sie die folgende Anforderung, um Eigenschaftswerte von allen Komponenten abzurufen. In diesem Beispiel wird das Gerät als `temperature-controller-01` bezeichnet:

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/temperature-controller-01/properties?api-version=1.0
```

Die Antwort auf diese Anforderung sieht wie das folgende Beispiel aus:

```json
{
  "serialNumber": "Explicabo animi nihil qui facere sit explicabo nisi.",
  "$metadata": {
    "serialNumber": {
      "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
    }
  },
  "thermostat1": {
    "maxTempSinceLastReboot": 79.7290121339184,
    "$metadata": {
      "maxTempSinceLastReboot": {
        "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
      }
    }
  },
  "thermostat2": {
    "maxTempSinceLastReboot": 54.214860556320424,
    "$metadata": {
      "maxTempSinceLastReboot": {
        "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
      }
    }
  },
  "deviceInformation": {
    "manufacturer": "Eveniet culpa sed sit omnis.",
    "$metadata": {
      "manufacturer": {
        "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
      },
      "model": {
        "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
      },
      "swVersion": {
        "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
      },
      "osName": {
        "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
      },
      "processorArchitecture": {
        "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
      },
      "processorManufacturer": {
        "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
      },
      "totalStorage": {
        "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
      },
      "totalMemory": {
        "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
      }
    },
    "model": "Necessitatibus id ab dolores vel eligendi fuga.",
    "swVersion": "Ut minus ipsum ut omnis est asperiores harum.",
    "osName": "Atque sit omnis eum sapiente eum tenetur est dolor.",
    "processorArchitecture": "Ratione enim dolor iste iure.",
    "processorManufacturer": "Aliquam eligendi sit ipsa.",
    "totalStorage": 36.02825898541592,
    "totalMemory": 55.442695395750505
  }
}
```

Verwenden Sie die folgende Anforderung, um einen Eigenschaftswert aus einer einzelnen Komponente abzurufen. In diesem Beispiel wird das Gerät als `temperature-controller-01` und die Komponente als `thermostat2` bezeichnet:

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/temperature-controller-01/components/thermostat2/properties?api-version=1.0
```

Die Antwort auf diese Anforderung sieht wie das folgende Beispiel aus:

```json
{
  "maxTempSinceLastReboot": 24.445128131004935,
  "$metadata": {
    "maxTempSinceLastReboot": {
      "lastUpdateTime": "2021-03-24T14:03:53.787491Z"
    }
  }
}
```

Wenn es sich bei dem Gerät um ein IoT Edge-Gerät handelt, verwenden Sie die folgende Anforderung, um Eigenschaftswerte aus einem Modul abzurufen. In diesem Beispiel wird ein Gerät namens `environmental-sensor-01` mit einem Modul namens `SimulatedTemperatureSensor` verwendet:

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/environmental-sensor-01/modules/SimulatedTemperatureSensor/properties?api-version=1.0
```

Die Antwort auf diese Anforderung sieht wie das folgende Beispiel aus:

```json
{
  "$metadata": {
    "SendData": {
      "desiredValue": true,
      "desiredVersion": 1
    },
    "SendInterval": {
      "desiredValue": 10,
      "desiredVersion": 1
    }
  }
}
```

> [!TIP]
> Verwenden Sie `/devices/{deviceId}/modules/{moduleName}/components/{componentName}/properties`, um von einer Komponente in einem Modul aus auf die Eigenschaften zuzugreifen.

## <a name="write-properties"></a>Schreibzugriffeigenschaften

Einige Eigenschaften sind beschreibbar. Im Thermostatmodell ist die Eigenschaft `targetTemperature` z. B. eine beschreibbare Eigenschaft.

Verwenden Sie die folgende Anforderung, um einen einzelnen Eigenschaftswert auf ein Gerät zu schreiben, das keine Komponenten verwendet. In diesem Beispiel wird das Gerät als `thermostat-01` bezeichnet.

```http
PATCH https://{your app subdomain}.azureiotcentral.com/api/devices/thermostat-01/properties?api-version=1.0
```

Der Anforderungstext sieht wie das folgende Beispiel aus:

```json
{
  "targetTemperature": 65.5
}
```

Die Antwort auf diese Anforderung sieht wie das folgende Beispiel aus:

```json
{
  "$metadata": {
    "targetTemperature": {
      "desiredValue": 65.5
    }
  }
}
```

> [!TIP]
> Um alle Eigenschaften auf einem Gerät zu aktualisieren, verwenden Sie `PUT` anstelle von `PATCH`.

Verwenden Sie die folgende Anforderung, um einen einzelnen Eigenschaftswert auf ein Gerät zu schreiben, das Komponenten verwendet. In diesem Beispiel wird das Gerät als `temperature-controller-01` und die Komponente als `thermostat2` bezeichnet:

```http
PATCH https://{your app subdomain}.azureiotcentral.com/api/devices/temperature-controller-01/components/thermostat2/properties?api-version=1.0
```

Der Anforderungstext sieht wie das folgende Beispiel aus:

```json
{
  "targetTemperature": 65.5
}
```

Die Antwort auf diese Anforderung sieht wie das folgende Beispiel aus:

```json
{
  "$metadata": {
    "targetTemperature": {
      "desiredValue": 65.5
    }
  }
}
```

> [!TIP]
> Um alle Eigenschaften für eine Komponente zu aktualisieren, verwenden Sie `PUT` anstelle von `PATCH`.

Wenn es sich bei dem Gerät um ein IoT Edge-Gerät handelt, verwenden Sie die folgende Anforderung, um einen einzelnen Eigenschaftswert in ein Modul zu schreiben. In diesem Beispiel werden ein Gerät namens `environmental-sensor-01`, ein Modul namens `SimulatedTemperatureSensor` und eine Eigenschaft namens `SendInterval` verwendet:

```http
PUT https://{your app subdomain}.azureiotcentral.com/api/devices/environmental-sensor-01/modules/SimulatedTemperatureSensor/properties?api-version=1.0
```

Der Anforderungstext sieht wie das folgende Beispiel aus:

```json
{
  "SendInterval": 20
}
```

Die Antwort auf diese Anforderung sieht wie das folgende Beispiel aus:

```json
{
  "$metadata": {
    "SendInterval": {
      "desiredValue": 20
    }
  }
}
```

> [!TIP]
> Um alle Eigenschaften für ein Modul zu aktualisieren, verwenden Sie `PUT` anstelle von `PATCH`.

### <a name="update-module-properties"></a>Aktualisieren von Moduleigenschaften

Wenn Sie ein IoT Edge-Gerät verwenden, verwenden Sie die folgende Anforderung, um Eigenschaftswerte aus einem Modul abzurufen:

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/{deviceId}/modules/{moduleName}/properties?api-version=1.0
```

Wenn Sie ein IoT Edge-Gerät verwenden, verwenden Sie die folgende Anforderung, um Eigenschaftswerte aus einer Komponente in einem Modul abzurufen:

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/{deviceId}/modules/{moduleName}/components/{componentName}/properties?api-version=1.0
```

## <a name="call-commands"></a>Aufrufen von Befehlen

Sie können die REST-API verwenden, um Gerätebefehle aufzurufen und den Geräteverlauf abzurufen.

Verwenden Sie die folgende Anforderung, um einen Befehl auf einem Gerät aufzurufen, das keine Komponenten verwendet. In diesem Beispiel wird das Gerät als `thermostat-01` und der Befehl als `getMaxMinReport` bezeichnet:

```http
POST https://{your app subdomain}.azureiotcentral.com/api/devices/thermostat-01/commands/getMaxMinReport?api-version=1.0
```

Der Anforderungstext sieht wie das folgende Beispiel aus:

```json
{
  "since": "2021-03-24T12:55:20.789Z"
}
```

Die Antwort auf diese Anforderung sieht wie das folgende Beispiel aus:

```json
{
  "response": {
    "maxTemp": 21.002000799562367,
    "minTemp": 73.09674605264892,
    "avgTemp": 59.54553991653756,
    "startTime": "2022-02-28T15:02:56.789Z",
    "endTime": "2021-05-05T03:50:56.412Z"
  },
  "responseCode": 200
}
```

Verwenden Sie die folgende Anforderung, um den Verlauf für diesen Befehl anzuzeigen:

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/thermostat-01/commands/getMaxMinReport?api-version=1.0
```

Die Antwort auf diese Anforderung sieht wie das folgende Beispiel aus:

```json
{
  "value": [
    {
      "response": {
        "maxTemp": 71.43744908819954,
        "minTemp": 51.29986610160005,
        "avgTemp": 39.577384387771744,
        "startTime": "2021-06-20T00:38:17.620Z",
        "endTime": "2022-01-07T22:30:41.104Z"
      },
      "responseCode": 200
    }
  ]
}
```

Verwenden Sie die folgende Anforderung, um einen Befehl auf einem Gerät aufzurufen, das Komponenten verwendet. In diesem Beispiel wird das Gerät als `temperature-controller-01`, die Komponente als `thermostat2` und der Befehl als `getMaxMinReport` bezeichnet:

```http
POST https://{your app subdomain}.azureiotcentral.com/api/devices/temperature-controller-01/components/thermostat2/commands/getMaxMinReport?api-version=1.0
```

Die Formate der Anforderungsnutzdaten und Antworten sind identisch mit denen für ein Gerät, das keine Komponenten verwendet.

Verwenden Sie die folgende Anforderung, um den Verlauf für diesen Befehl anzuzeigen:

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/temperature-controller-01/components/thermostat2/commands/getMaxMinReport?api-version=1.0
```

> [!TIP]
> Verwenden Sie `/devices/{deviceId}/modules/{moduleName}/components/{componentName}/commands/{commandName}`, um Befehle in einer Komponente in einem Modul aufzurufen.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun erfahren haben, wie Sie Geräte mit der REST-API steuern, wird als nächster Schritt das [Erstellen und Verwalten von Aufträgen mithilfe der IoT Central-REST-API](howto-manage-jobs-with-rest-api.md) empfohlen.
