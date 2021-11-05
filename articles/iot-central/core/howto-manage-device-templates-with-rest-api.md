---
title: Verwenden der REST-API zum Hinzufügen von Gerätevorlagen in Azure IoT Central
description: Verwenden der IoT Central-REST-API zum Hinzufügen von Gerätevorlagen in einer Anwendung
author: v-krishnag
ms.author: v-krishnag
ms.date: 09/28/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: be2c84ed1b825fcc257dfce444c043a58a3efbc3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095970"
---
# <a name="how-to-use-the-iot-central-rest-api-to-manage-device-templates"></a>Verwenden der IoT Central-REST-API zum Verwalten von Gerätevorlagen

Mit der IoT Central-REST-API können Sie Clientanwendungen entwickeln, die in IoT Central-Anwendungen integriert werden. Sie können die REST-API verwenden, um [Gerätevorlagen](concepts-device-templates.md) in Ihrer IoT Central-Anwendung zu verwalten.

Jeder IoT Central-REST-API-Aufruf erfordert einen Autorisierungsheader. Weitere Informationen finden Sie unter [Authentifizieren und Autorisieren von IoT Central-REST-API-Aufrufen](howto-authorize-rest-api.md).

Die Referenzdokumentation für die IoT Central-REST-API finden Sie unter [Azure IoT Central: Referenz zur REST-API](/rest/api/iotcentral/).

## <a name="device-templates"></a>Gerätevorlagen

Eine Gerätevorlage enthält ein Gerätemodell, Cloudeigenschaftdefinitionen, Anpassungen und Ansichtsdefinitionen. Mit der REST-API können Sie das Gerätemodell, Cloudeigenschaftdefinitionen und Anpassungen verwalten. Verwenden Sie die Benutzeroberfläche, um Ansichten zu erstellen und zu verwalten.

Der Abschnitt zum Gerätemodell einer Gerätevorlage gibt die Funktionen eines Geräts an, das Sie mit Ihrer Anwendung verbinden möchten. Zu den Funktionen gehören Telemetrie, Eigenschaften und Befehle. Das Modell wird mit [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) definiert.

## <a name="device-templates-rest-api"></a>REST-API für Gerätevorlagen

Mit der IoT Central-REST-API können Sie folgende Aktionen ausführen:

* Hinzufügen einer Gerätevorlage zu Ihrer Anwendung
* Aktualisieren einer Gerätevorlage in Ihrer Anwendung
* Abrufen einer Liste der Gerätevorlagen in der Anwendung
* Abrufen einer Gerätevorlage nach ID
* Löschen einer Gerätevorlage in Ihrer Anwendung

### <a name="add-a-device-template"></a>Hinzufügen einer Gerätevorlage

Verwenden Sie die folgende Anforderung, um eine neue Gerätevorlage zu erstellen und zu veröffentlichen. Standardansichten werden automatisch für Gerätevorlagen generiert, die auf diese Weise erstellt werden.

```http
PUT https://{subdomain}.{baseDomain}/api/deviceTemplates/{deviceTemplateId}?api-version=1.0
```

>[!NOTE]
>Gerätevorlagen-IDs folgen der [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#digital-twin-model-identifier)-Namenskonvention, wie z. B.: `dtmi:contoso:mythermostattemplate;1`

Im folgenden Beispiel wird ein Anforderungstext angezeigt, der eine Gerätevorlage für ein Thermostatgerät hinzufügt. `capabilityModel` enthält Temperaturtelemetrie, zwei Eigenschaften und einen Befehl. Die Gerätevorlage definiert die `CustomerName`-Cloudeigenschaft und passt die `targetTemperature`-Eigenschaft mit `decimalPlaces`, `displayUnit`, `maxValue` und `minValue` an. Der Wert der `@id`-Gerätevorlage muss mit dem `deviceTemplateId`-Wert in der URL übereinstimmen. Der Wert der `@id`-Gerätevorlage ist nicht mit dem Wert des `capabilityModel` `@id`-Werts identisch.

```json
{
    "displayName": "Thermostat",

    "@id": "dtmi:contoso:mythermostattemplate;1",
    "@type": [
        "ModelDefinition",
        "DeviceModel"
    ],
    "@context": [
        "dtmi:iotcentral:context;2",
        "dtmi:dtdl:context;2"
    ],
    "capabilityModel": {
        "@id": "dtmi:contoso:Thermostat;1",
        "@type": "Interface",
        "contents": [
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
            },
            {
                "@type": [
                    "Property",
                    "Temperature"
                ],
                "description": "Allows to remotely specify the desired target temperature.",
                "displayName": "Target Temperature",
                "name": "targetTemperature",
                "schema": "double",
                "unit": "degreeCelsius",
                "writable": true,
                "decimalPlaces": 1,
                "displayUnit": "C",
                "maxValue": 80,
                "minValue": 50
            },
            {
                "@type": [
                    "Property",
                    "Temperature"
                ],
                "description": "Returns the max temperature since last device reboot.",
                "displayName": "Max temperature since last reboot.",
                "name": "maxTempSinceLastReboot",
                "schema": "double",
                "unit": "degreeCelsius"
            },
            {
                "@type": "Command",
                "description": "This command returns the max, min and average temperature from the specified time to the current time.",
                "displayName": "Get report",
                "name": "getMaxMinReport",
                "request": {
                    "@type": "CommandPayload",
                    "description": "Period to return the max-min report.",
                    "displayName": "Since",
                    "name": "since",
                    "schema": "dateTime"
                },
                "response": {
                    "@type": "CommandPayload",
                    "displayName": "Temperature Report",
                    "name": "tempReport",
                    "schema": {
                        "@type": "Object",
                        "fields": [
                            {
                                "displayName": "Max temperature",
                                "name": "maxTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Min temperature",
                                "name": "minTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Average Temperature",
                                "name": "avgTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Start Time",
                                "name": "startTime",
                                "schema": "dateTime"
                            },
                            {
                                "displayName": "End Time",
                                "name": "endTime",
                                "schema": "dateTime"
                            }
                        ]
                    }
                }
            },
            {
                "@type": [
                    "Property",
                    "Cloud",
                    "StringValue"
                ],
                "displayName": "Customer Name",
                "name": "CustomerName",
                "schema": "string"
            }
        ],
        "description": "Reports current temperature and provides desired temperature control.",
        "displayName": "Thermostat"
    }
}
```

Der Anforderungstext enthält einige erforderliche Felder:

* `@id`: eine eindeutige ID im Format eines einfachen Uniform Resource Name
* `@type`: deklariert dieses Objekt als Schnittstelle
* `@context`: gibt die für die Schnittstelle verwendete DTDL-Version an
* `contents`: listet die Eigenschaften, Telemetriedaten und Befehle auf, die das Gerät ausmachen Die Funktionen können in mehreren Schnittstellen definiert werden.
* `capabilityModel`: Jede Gerätevorlage verfügt über ein Funktionsmodell. Zwischen den einzelnen Modulfunktionsmodellen und einem Gerätemodell wird eine Beziehung eingerichtet. Ein Funktionsmodell implementiert eine oder mehrere Modulschnittstellen.

Es gibt einige optionale Felder, über die Sie dem Funktionsmodell weitere Details hinzufügen können, z. B. den Anzeigenamen und eine Beschreibung.

Jeder Eintrag in der Liste der Schnittstellen im Abschnitt „implements“ enthält jeweils Folgendes:

* `name`: der Programmiername der Schnittstelle
* `schema`: die Schnittstelle, die mit dem Funktionsmodell implementiert wird

Die Antwort auf diese Anforderung sieht wie das folgende Beispiel aus: 

```json
{
    "etag": "\"~F27cqSo0ON3bfOzwgZxAl89/JVvM80+dds6y8+mZh5M=\"",
    "displayName": "Thermostat",
    "capabilityModel": {
        "@id": "dtmi:contoso:Thermostat;1",
        "@type": "Interface",
        "contents": [
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
            },
            {
                "@type": [
                    "Property",
                    "Temperature",
                    "NumberValue"
                ],
                "description": "Allows to remotely specify the desired target temperature.",
                "displayName": "Target Temperature",
                "name": "targetTemperature",
                "schema": "double",
                "unit": "degreeCelsius",
                "writable": true,
                "decimalPlaces": 1,
                "displayUnit": "C",
                "maxValue": 80,
                "minValue": 50
            },
            {
                "@type": [
                    "Property",
                    "Temperature"
                ],
                "description": "Returns the max temperature since last device reboot.",
                "displayName": "Max temperature since last reboot.",
                "name": "maxTempSinceLastReboot",
                "schema": "double",
                "unit": "degreeCelsius"
            },
            {
                "@type": "Command",
                "description": "This command returns the max, min and average temperature from the specified time to the current time.",
                "displayName": "Get report",
                "name": "getMaxMinReport",
                "request": {
                    "@type": "CommandPayload",
                    "description": "Period to return the max-min report.",
                    "displayName": "Since",
                    "name": "since",
                    "schema": "dateTime"
                },
                "response": {
                    "@type": "CommandPayload",
                    "displayName": "Temperature Report",
                    "name": "tempReport",
                    "schema": {
                        "@type": "Object",
                        "fields": [
                            {
                                "displayName": "Max temperature",
                                "name": "maxTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Min temperature",
                                "name": "minTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Average Temperature",
                                "name": "avgTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Start Time",
                                "name": "startTime",
                                "schema": "dateTime"
                            },
                            {
                                "displayName": "End Time",
                                "name": "endTime",
                                "schema": "dateTime"
                            }
                        ]
                    }
                }
            },
            {
                "@type": [
                    "Property",
                    "Cloud",
                    "StringValue"
                ],
                "displayName": "Customer Name",
                "name": "CustomerName",
                "schema": "string"
            }
        ],
        "description": "Reports current temperature and provides desired temperature control.",
        "displayName": "Thermostat"
    },
    "@id": "dtmi:modelDefinition:spzeut3n:n2lteu39u6",
    "@type": [
        "ModelDefinition",
        "DeviceModel"
    ],
    "@context": [
        "dtmi:iotcentral:context;2",
        "dtmi:dtdl:context;2"
    ]
}

```

### <a name="get-a-device-template"></a>Abrufen einer Gerätevorlage

Verwenden Sie die folgende Anforderung, um Details einer Gerätevorlage aus Ihrer Anwendung abzurufen:

```http
GET https://{subdomain}.{baseDomain}/api/deviceTemplates/{deviceTemplateId}?api-version=1.0
```

>[!NOTE]
> Sie können die `deviceTemplateId` von der IoT Central-Anwendungsbenutzeroberfläche abrufen, indem Sie mit der Maus auf ein Gerät zeigen.

Die Antwort auf diese Anforderung sieht wie das folgende Beispiel aus:

```json
{
    "etag": "\"~F27cqSo0ON3bfOzwgZxAl89/JVvM80+dds6y8+mZh5M=\"",
    "displayName": "Thermostat",
    "capabilityModel": {
        "@id": "dtmi:contoso:Thermostat;1",
        "@type": "Interface",
        "contents": [
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
            },
            {
                "@type": [
                    "Property",
                    "Temperature",
                    "NumberValue"
                ],
                "description": "Allows to remotely specify the desired target temperature.",
                "displayName": "Target Temperature",
                "name": "targetTemperature",
                "schema": "double",
                "unit": "degreeCelsius",
                "writable": true,
                "decimalPlaces": 1,
                "displayUnit": "C",
                "maxValue": 80,
                "minValue": 50
            },
            {
                "@type": [
                    "Property",
                    "Temperature"
                ],
                "description": "Returns the max temperature since last device reboot.",
                "displayName": "Max temperature since last reboot.",
                "name": "maxTempSinceLastReboot",
                "schema": "double",
                "unit": "degreeCelsius"
            },
            {
                "@type": "Command",
                "description": "This command returns the max, min and average temperature from the specified time to the current time.",
                "displayName": "Get report",
                "name": "getMaxMinReport",
                "request": {
                    "@type": "CommandPayload",
                    "description": "Period to return the max-min report.",
                    "displayName": "Since",
                    "name": "since",
                    "schema": "dateTime"
                },
                "response": {
                    "@type": "CommandPayload",
                    "displayName": "Temperature Report",
                    "name": "tempReport",
                    "schema": {
                        "@type": "Object",
                        "fields": [
                            {
                                "displayName": "Max temperature",
                                "name": "maxTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Min temperature",
                                "name": "minTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Average Temperature",
                                "name": "avgTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Start Time",
                                "name": "startTime",
                                "schema": "dateTime"
                            },
                            {
                                "displayName": "End Time",
                                "name": "endTime",
                                "schema": "dateTime"
                            }
                        ]
                    }
                }
            },
            {
                "@type": [
                    "Property",
                    "Cloud",
                    "StringValue"
                ],
                "displayName": "Customer Name",
                "name": "CustomerName",
                "schema": "string"
            }
        ],
        "description": "Reports current temperature and provides desired temperature control.",
        "displayName": "Thermostat"
    },
    "@id": "dtmi:modelDefinition:spzeut3n:n2lteu39u6",
    "@type": [
        "ModelDefinition",
        "DeviceModel"
    ],
    "@context": [
        "dtmi:iotcentral:context;2",
        "dtmi:dtdl:context;2"
    ]
}
```

### <a name="list-device-templates"></a>Auflisten von Gerätevorlagen

Verwenden Sie die folgende Anforderung, um eine Liste der Gerätevorlagen aus Ihrer Anwendung abzurufen:

```http
GET https://{subdomain}.{baseDomain}/api/deviceTemplates?api-version=1.0
```

Die Antwort auf diese Anforderung sieht wie das folgende Beispiel aus: 

```json
{
    "value": [
        {
            "etag": "\"~F27cqSo0ON3bfOzwgZxAl89/JVvM80+dds6y8+mZh5M=\"",
            "displayName": "Thermostat",
            "capabilityModel": {
                "@id": "dtmi:contoso:Thermostat;1",
                "@type": "Interface",
                "contents": [
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
                    },
                    {
                        "@type": [
                            "Property",
                            "Temperature",
                            "NumberValue"
                        ],
                        "description": "Allows to remotely specify the desired target temperature.",
                        "displayName": "Target Temperature",
                        "name": "targetTemperature",
                        "schema": "double",
                        "unit": "degreeCelsius",
                        "writable": true,
                        "decimalPlaces": 1,
                        "displayUnit": "C",
                        "maxValue": 80,
                        "minValue": 50
                    },
                    {
                        "@type": [
                            "Property",
                            "Temperature"
                        ],
                        "description": "Returns the max temperature since last device reboot.",
                        "displayName": "Max temperature since last reboot.",
                        "name": "maxTempSinceLastReboot",
                        "schema": "double",
                        "unit": "degreeCelsius"
                    },
                    {
                        "@type": "Command",
                        "description": "This command returns the max, min and average temperature from the specified time to the current time.",
                        "displayName": "Get report",
                        "name": "getMaxMinReport",
                        "request": {
                            "@type": "CommandPayload",
                            "description": "Period to return the max-min report.",
                            "displayName": "Since",
                            "name": "since",
                            "schema": "dateTime"
                        },
                        "response": {
                            "@type": "CommandPayload",
                            "displayName": "Temperature Report",
                            "name": "tempReport",
                            "schema": {
                                "@type": "Object",
                                "fields": [
                                    {
                                        "displayName": "Max temperature",
                                        "name": "maxTemp",
                                        "schema": "double"
                                    },
                                    {
                                        "displayName": "Min temperature",
                                        "name": "minTemp",
                                        "schema": "double"
                                    },
                                    {
                                        "displayName": "Average Temperature",
                                        "name": "avgTemp",
                                        "schema": "double"
                                    },
                                    {
                                        "displayName": "Start Time",
                                        "name": "startTime",
                                        "schema": "dateTime"
                                    },
                                    {
                                        "displayName": "End Time",
                                        "name": "endTime",
                                        "schema": "dateTime"
                                    }
                                ]
                            }
                        }
                    },
                    {
                        "@type": [
                            "Property",
                            "Cloud",
                            "StringValue"
                        ],
                        "displayName": "Customer Name",
                        "name": "CustomerName",
                        "schema": "string"
                    }
                ],
                "description": "Reports current temperature and provides desired temperature control.",
                "displayName": "Thermostat"
            },
            "@id": "dtmi:modelDefinition:spzeut3n:n2lteu39u6",
            "@type": [
                "ModelDefinition",
                "DeviceModel"
            ],
            "@context": [
                "dtmi:iotcentral:context;2",
                "dtmi:dtdl:context;2"
            ]
        },
        {
            "etag": "\"~XS5GovPNzJqFIwkkV/vyWW5U/6if2NwC/NqUlDxExAY=\"",
            "displayName": "Thermostat2",
            "capabilityModel": {
                "@id": "dtmi:contoso:Thermostat2;1",
                "@type": "Interface",
                "contents": [
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
                    },
                    {
                        "@type": [
                            "Property",
                            "Temperature",
                            "NumberValue"
                        ],
                        "description": "Allows to remotely specify the desired target temperature.",
                        "displayName": "Target Temperature",
                        "name": "targetTemperature",
                        "schema": "double",
                        "unit": "degreeCelsius",
                        "writable": true,
                        "decimalPlaces": 1,
                        "displayUnit": "C",
                        "maxValue": 80,
                        "minValue": 50
                    },
                    {
                        "@type": [
                            "Property",
                            "Temperature"
                        ],
                        "description": "Returns the max temperature since last device reboot.",
                        "displayName": "Max temperature since last reboot.",
                        "name": "maxTempSinceLastReboot",
                        "schema": "double",
                        "unit": "degreeCelsius"
                    },
                    {
                        "@type": "Command",
                        "description": "This command returns the max, min and average temperature from the specified time to the current time.",
                        "displayName": "Get report",
                        "name": "getMaxMinReport",
                        "request": {
                            "@type": "CommandPayload",
                            "description": "Period to return the max-min report.",
                            "displayName": "Since",
                            "name": "since",
                            "schema": "dateTime"
                        },
                        "response": {
                            "@type": "CommandPayload",
                            "displayName": "Temperature Report",
                            "name": "tempReport",
                            "schema": {
                                "@type": "Object",
                                "fields": [
                                    {
                                        "displayName": "Max temperature",
                                        "name": "maxTemp",
                                        "schema": "double"
                                    },
                                    {
                                        "displayName": "Min temperature",
                                        "name": "minTemp",
                                        "schema": "double"
                                    },
                                    {
                                        "displayName": "Average Temperature",
                                        "name": "avgTemp",
                                        "schema": "double"
                                    },
                                    {
                                        "displayName": "Start Time",
                                        "name": "startTime",
                                        "schema": "dateTime"
                                    },
                                    {
                                        "displayName": "End Time",
                                        "name": "endTime",
                                        "schema": "dateTime"
                                    }
                                ]
                            }
                        }
                    },
                    {
                        "@type": [
                            "Property",
                            "Cloud",
                            "StringValue"
                        ],
                        "displayName": "Customer Name",
                        "name": "CustomerName",
                        "schema": "string"
                    }
                ],
                "description": "Reports current temperature and provides desired temperature control.",
                "displayName": "Thermostat"
            },
            "@id": "dtmi:modelDefinition:spzeut3n:n2lteu39u67",
            "@type": [
                "ModelDefinition",
                "DeviceModel"
            ],
            "@context": [
                "dtmi:iotcentral:context;2",
                "dtmi:dtdl:context;2"
            ]
        }
    ]
}
```

### <a name="update-a-device-template"></a>Aktualisieren einer Gerätevorlage

```http
PATCH https://{subdomain}.{baseDomain}/api/deviceTemplates/{deviceTemplateId}?api-version=1.0
```

>[!NOTE] 
>`{deviceTemplateId}` sollte mit der `@id` in den Nutzdaten identisch sein.

Der Beispielanforderungstext sieht wie im folgenden Beispiel aus, in dem der Gerätevorlage die `LastMaintenanceDate`-Cloudeigenschaft hinzugefügt wird:

```json
{
    "displayName": "Thermostat",

    "@id": "dtmi:contoso:mythermostattemplate",
    "@type": [
        "ModelDefinition",
        "DeviceModel"
    ],
    "@context": [
        "dtmi:iotcentral:context;2",
        "dtmi:dtdl:context;2"
    ],
    "capabilityModel": {
        "@id": "dtmi:contoso:Thermostat;1",
        "@type": "Interface",
        "contents": [
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
            },
            {
                "@type": [
                    "Property",
                    "Temperature"
                ],
                "description": "Allows to remotely specify the desired target temperature.",
                "displayName": "Target Temperature",
                "name": "targetTemperature",
                "schema": "double",
                "unit": "degreeCelsius",
                "writable": true,
                "decimalPlaces": 1,
                "displayUnit": "C",
                "maxValue": 80.0,
                "minValue": 50.0
            },
            {
                "@type": [
                    "Property",
                    "Temperature"
                ],
                "description": "Returns the max temperature since last device reboot.",
                "displayName": "Max temperature since last reboot.",
                "name": "maxTempSinceLastReboot",
                "schema": "double",
                "unit": "degreeCelsius"
            },
            {
                "@type": "Command",
                "description": "This command returns the max, min and average temperature from the specified time to the current time.",
                "displayName": "Get report",
                "name": "getMaxMinReport",
                "request": {
                    "@type": "CommandPayload",
                    "description": "Period to return the max-min report.",
                    "displayName": "Since",
                    "name": "since",
                    "schema": "dateTime"
                },
                "response": {
                    "@type": "CommandPayload",
                    "displayName": "Temperature Report",
                    "name": "tempReport",
                    "schema": {
                        "@type": "Object",
                        "fields": [
                            {
                                "displayName": "Max temperature",
                                "name": "maxTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Min temperature",
                                "name": "minTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Average Temperature",
                                "name": "avgTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Start Time",
                                "name": "startTime",
                                "schema": "dateTime"
                            },
                            {
                                "displayName": "End Time",
                                "name": "endTime",
                                "schema": "dateTime"
                            }
                        ]
                    }
                }
            },
            {
                "@type": [
                    "Property",
                    "Cloud",
                    "StringValue"
                ],
                "displayName": "Customer Name",
                "name": "CustomerName",
                "schema": "string"
            },
            {
                "@type": [
                    "Property",
                    "Cloud",
                    "StringValue"
                ],
                "displayName": "Last Maintenance Date",
                "name": "LastMaintenanceDate",
                "schema": "dateTime"
            }
        ],
        "description": "Reports current temperature and provides desired temperature control.",
        "displayName": "Thermostat"
    }
}

```

Die Antwort auf diese Anforderung sieht wie das folgende Beispiel aus:

```json
{
    "etag": "\"~6Ku691rHAgw/yw8u+ygZJGAKjSN4P4q/KxCU2xskrmk=\"",
    "displayName": "Thermostat",
    "capabilityModel": {
        "@id": "dtmi:contoso:Thermostat;1",
        "@type": "Interface",
        "contents": [
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
            },
            {
                "@type": [
                    "Property",
                    "Temperature"
                ],
                "description": "Allows to remotely specify the desired target temperature.",
                "displayName": "Target Temperature",
                "name": "targetTemperature",
                "schema": "double",
                "unit": "degreeCelsius",
                "writable": true,
                "decimalPlaces": 1,
                "displayUnit": "C",
                "maxValue": 80,
                "minValue": 50
            },
            {
                "@type": [
                    "Property",
                    "Temperature"
                ],
                "description": "Returns the max temperature since last device reboot.",
                "displayName": "Max temperature since last reboot.",
                "name": "maxTempSinceLastReboot",
                "schema": "double",
                "unit": "degreeCelsius"
            },
            {
                "@type": "Command",
                "description": "This command returns the max, min and average temperature from the specified time to the current time.",
                "displayName": "Get report",
                "name": "getMaxMinReport",
                "request": {
                    "@type": "CommandPayload",
                    "description": "Period to return the max-min report.",
                    "displayName": "Since",
                    "name": "since",
                    "schema": "dateTime"
                },
                "response": {
                    "@type": "CommandPayload",
                    "displayName": "Temperature Report",
                    "name": "tempReport",
                    "schema": {
                        "@type": "Object",
                        "fields": [
                            {
                                "displayName": "Max temperature",
                                "name": "maxTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Min temperature",
                                "name": "minTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Average Temperature",
                                "name": "avgTemp",
                                "schema": "double"
                            },
                            {
                                "displayName": "Start Time",
                                "name": "startTime",
                                "schema": "dateTime"
                            },
                            {
                                "displayName": "End Time",
                                "name": "endTime",
                                "schema": "dateTime"
                            }
                        ]
                    }
                }
            },
            {
                "@type": [
                    "Property",
                    "Cloud",
                    "StringValue"
                ],
                "displayName": "Customer Name",
                "name": "CustomerName",
                "schema": "string"
            },
            {
                "@type": [
                    "Property",
                    "Cloud",
                    "DateTimeValue"
                ],
                "displayName": "Last Maintenance Date",
                "name": "LastMaintenanceDate",
                "schema": "dateTime"
            }
        ],
        "description": "Reports current temperature and provides desired temperature control.",
        "displayName": "Thermostat"
    },
    "@id": "dtmi:modelDefinition:spzeut3n:n2lteu39u6",
    "@type": [
        "ModelDefinition",
        "DeviceModel"
    ],
    "@context": [
        "dtmi:iotcentral:context;2",
        "dtmi:dtdl:context;2"
    ]
}
```

### <a name="delete-a-device-template"></a>Löschen einer Gerätevorlage

Verwenden Sie die folgende Anforderung, um eine Gerätevorlage zu löschen:

```http
DELETE https://{subdomain}.{baseDomain}/api/deviceTemplates/{deviceTemplateId}?api-version=1.0
```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun erfahren haben, wie Sie Gerätevorlagen mit der REST-API verwalten, wird als nächster Schritt das [Erstellen von Gerätevorlagen aus der IoT Central-GUI](howto-set-up-template.md#create-a-device-template) empfohlen.
