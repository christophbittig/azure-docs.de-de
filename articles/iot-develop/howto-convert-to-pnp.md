---
title: Konvertieren eines vorhandenen Geräts zur Verwendung von IoT Plug & Play | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie Ihren vorhandenen Gerätecode zur Verwendung von IoT Plug & Play konvertieren, indem Sie ein Gerätemodell erstellen und dann die Modell-ID senden, wenn das Gerät eine Verbindung herstellt.
author: dominicbetts
ms.author: dobett
ms.date: 05/14/2021
ms.topic: how-to
ms.service: iot-develop
services: iot-develop
ms.openlocfilehash: 225b999053824513e41d29d80a47a596fdedf991
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114442567"
---
# <a name="how-to-convert-an-existing-device-to-be-an-iot-plug-and-play-device"></a>Konvertieren eines vorhandenen Geräts in ein IoT Plug & Play-Gerät

In diesem Artikel werden die Schritte erläutert, die Sie ausführen müssen, um ein vorhandenes Gerät in ein IoT Plug & Play-Gerät zu konvertieren. Es wird beschrieben, wie Sie das für jedes IoT Plug & Play-Gerät erforderliche Modell erstellen und welche Codeänderungen notwendig sind, damit das Gerät als IoT Plug & Play-Gerät verwendet werden kann.

Bei den Codebeispielen in diesem Artikel handelt es sich um C-Code, der eine MQTT-Bibliothek verwendet, um eine Verbindung mit einem IoT-Hub herzustellen. Sie können die in diesem Artikel beschriebenen Änderungen auf Geräte anwenden, die mit anderen Programmiersprachen und SDKs implementiert wurden.

So konvertieren Sie Ihr vorhandenes Gerät in ein IoT Plug & Play-Gerät:

1. Sehen Sie sich Ihren Gerätecode an, um die implementierten Telemetriedaten, Eigenschaften und Befehle zu verstehen.
1. Erstellen Sie ein Modell, das die von Ihrem Gerät implementierten Telemetriedaten, Eigenschaften und Befehle beschreibt.
1. Ändern Sie den Gerätecode, um die Modell-ID anzukündigen, wenn das Gerät eine Verbindung mit Ihrem Dienst herstellt.

## <a name="review-your-device-code"></a>Überprüfen des Gerätecodes

Bevor Sie ein Modell für Ihr Gerät erstellen, müssen Sie die vorhandenen Funktionen Ihres Geräts kennen:

- Die Telemetriedaten, die das Gerät regelmäßig sendet
- Die schreibgeschützten und beschreibbaren Eigenschaften, die das Gerät mit Ihrem Dienst synchronisiert
- Die vom Dienst aufgerufenen Befehle, auf die das Gerät reagiert

Die folgenden Gerätecodeausschnitte implementieren verschiedene Gerätefunktionen. Diese Beispiele basieren auf dem Beispiel in [PnPMQTTWin32-Before](https://github.com/Azure-Samples/IoTMQTTSample/tree/master/src/Windows/PnPMQTTWin32-Before):

Der folgende Codeausschnitt zeigt, wie das Gerät Temperaturtelemetriedaten sendet:

```c
#define TOPIC "devices/" DEVICEID "/messages/events/"

// ...

void Thermostat_SendCurrentTemperature()
{
  char msg[] = "{\"temperature\":25.6}";
  int msgId = rand();
  int rc = mosquitto_publish(mosq, &msgId, TOPIC, sizeof(msg) - 1, msg, 1, true);
  if (rc != MOSQ_ERR_SUCCESS)
  {
    printf("Error: %s\r\n", mosquitto_strerror(rc));
  }
}
```

Der Name des Telemetriefelds ist `temperature`, und der Typ ist „float“ oder „double“. Der folgende JSON-Code zeigt, wie die Modelldefinition für diesen Telemetrietyp aussieht. Weitere Informationen finden Sie im Abschnitt [Entwerfen eines Modells](#design-a-model) weiter unten.

```json
{
  "@type": [
    "Telemetry"
  ],
  "name": "temperature",
  "displayName": "Temperature",
  "description": "Temperature in degrees Celsius.",
  "schema": "double"
}
```

Der folgende Codeausschnitt zeigt, wie das Gerät einen Eigenschaftswert meldet:

```c
#define DEVICETWIN_MESSAGE_PATCH "$iothub/twin/PATCH/properties/reported/?$rid=patch_temp"

static void SendMaxTemperatureSinceReboot()
{
  char msg[] = "{\"maxTempSinceLastReboot\": 42.500}";
  int msgId = rand();
  int rc = mosquitto_publish(mosq, &msgId, DEVICETWIN_MESSAGE_PATCH, sizeof(msg) - 1, msg, 1, true);
  if (rc != MOSQ_ERR_SUCCESS)
  {
    printf("Error: %s\r\n", mosquitto_strerror(rc));
  }
}
```

Der Name der Eigenschaft ist `maxTempSinceLastReboot`, und der Typ ist „float“ oder „double“. Diese Eigenschaft wird vom Gerät gemeldet. Das Gerät empfängt nie eine Aktualisierung für diesen Wert vom Dienst. Der folgende JSON-Code zeigt, wie die Modelldefinition für diese Eigenschaft aussieht. Weitere Informationen finden Sie im Abschnitt [Entwerfen eines Modells](#design-a-model) weiter unten.

```json
{
  "@type": [
    "Property"
  ],
  "name": "maxTempSinceLastReboot",
  "schema": "double",
  "displayName": "Max temperature since last reboot.",
  "description": "Returns the max temperature since last device reboot."
}
```

Der folgende Codeausschnitt zeigt, wie das Gerät auf Nachrichten vom Dienst reagiert:

```c
void message_callback(struct mosquitto* mosq, void* obj, const struct mosquitto_message* message)
{
  printf("Message received: %s payload: %s \r\n", message->topic, (char*)message->payload);
  
  if (strncmp(message->topic, "$iothub/methods/POST/getMaxMinReport/?$rid=1",37) == 0)
  {
    char* pch;
    char* context;
    int msgId = 0;
    pch = strtok_s((char*)message->topic, "=",&context);
    while (pch != NULL)
    {
      pch = strtok_s(NULL, "=", &context);
      if (pch != NULL) {
        char * pEnd;
        msgId = strtol(pch,&pEnd,16 );
      }
    }
    char topic[64];
    sprintf_s(topic, "$iothub/methods/res/200/?$rid=%d", msgId);
    char msg[] = "{\"maxTemp\":83.51,\"minTemp\":77.68}";
    int rc = mosquitto_publish(mosq, &msgId, topic, sizeof(msg) - 1, msg, 1, true);
    if (rc != MOSQ_ERR_SUCCESS)
    {
      printf("Error: %s\r\n", mosquitto_strerror(rc));
    }
    delete pch;
  }

  if (strncmp(message->topic, "$iothub/twin/PATCH/properties/desired/?$version=1", 38) == 0)
  {
    char* pch;
    char* context;
    int version = 0; 
    pch = strtok_s((char*)message->topic, "=", &context);
    while (pch != NULL)
    {
      pch = strtok_s(NULL, "=", &context);
      if (pch != NULL) {
        char* pEnd;
        version = strtol(pch, &pEnd, 10);
      }
    }
    // To do: Parse payload and extract target value
    char msg[128];
    int value = 46;
    sprintf_s(msg, "{\"targetTemperature\":{\"value\":%d,\"ac\":200,\"av\":%d,\"ad\":\"success\"}}", value, version);
    int rc = mosquitto_publish(mosq, &version, DEVICETWIN_MESSAGE_PATCH, strlen(msg), msg, 1, true);
    if (rc != MOSQ_ERR_SUCCESS)
    {
      printf("Error: %s\r\n", mosquitto_strerror(rc));
    }
    delete pch;
  }
}
```

Das Thema `$iothub/methods/POST/getMaxMinReport/` empfängt vom Dienst eine Anforderung für den Befehl namens `getMaxMinReport`. Diese Anforderung kann Nutzdaten mit Befehlsparametern enthalten. Das Gerät sendet eine Antwort mit Nutzdaten, die `maxTemp`- und `minTemp`-Werte enthalten.

Das Thema `$iothub/twin/PATCH/properties/desired/` empfängt Eigenschaftsaktualisierungen vom Dienst. In diesem Beispiel wird davon ausgegangen, dass die Eigenschaftsaktualisierung für die `targetTemperature`-Eigenschaft gilt. Das Thema antwortet mit einer Bestätigung wie `{\"targetTemperature\":{\"value\":46,\"ac\":200,\"av\":12,\"ad\":\"success\"}}`.

Zusammengefasst implementiert das Beispiel die folgenden Funktionen:

| Name                   | Funktionstyp   | Details |
| ---------------------- | ----------------- | ------- |
| Temperatur            | Telemetrie         | In diesem Beispiel ist der Datentyp „double“. |
| maxTempSinceLastReboot | Eigenschaft          | In diesem Beispiel ist der Datentyp „double“. |
| targetTemperature      | Beschreibbare Eigenschaft | Der Datentyp ist „integer“. |
| getMaxMinReport        | Befehl           | Gibt JSON-Code mit den Feldern `maxTemp` und `minTemp` vom Typ „double“ zurück. |

## <a name="design-a-model"></a>Entwerfen eines Modells

Jedes IoT Plug & Play-Gerät verfügt über ein Modell, das die Features und Funktionen des Geräts beschreibt. Die Gerätefunktionen werden im Modell mit der [Digital Twin Definition Language (DTDL) V2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) beschrieben.

Verwenden Sie für ein einfaches Modell, das die vorhandenen Funktionen Ihres Geräts zuordnet, die DTDL-Elemente *Telemetry*, *Property* und *Command*.

Das DTDL-Modell für das im vorherigen Abschnitt beschriebene Beispiel sieht wie folgt aus:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:ConvertSample;1",
  "@type": "Interface",
  "displayName": "Simple device",
  "description": "Example that shows model for simple device converted to act as an IoT Plug and Play device.",
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
      "description": "This command returns the max and min temperature.",
      "request": {
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
            }
          ]
        }
      }
    }
  ]
}
```

Für dieses Modell gilt Folgendes:

- Die Werte `name` und `schema` entsprechen den Daten, die das Gerät sendet und empfängt.
- Alle Funktionen sind in einer einzigen Schnittstelle gruppiert.
- Die `@type`-Felder geben die DTDL-Typen wie **Property** und **Command** an.
- Felder wie `unit`, `displayName` und `description` stellen zusätzliche Informationen zur Verfügung, die vom Dienst verwendet werden können. IoT Central verwendet diese Werte beispielsweise beim Anzeigen von Daten in Gerätedashboards.

Weitere Informationen finden Sie unter [IoT Plug & Play-Konventionen](concepts-convention.md) und [Leitfaden zur Modellierung mit IoT Plug & Play](concepts-modeling-guide.md).

## <a name="update-the-code"></a>Aktualisieren des Codes

Wenn Ihr Gerät bereits mit IoT Hub oder IoT Central arbeitet, müssen Sie keine Änderungen an der Implementierung der Telemetrie-, Eigenschafts- und Befehlsfunktionen vornehmen. Damit das Gerät den IoT Plug & Play-Konventionen entspricht, ändern Sie die Art und Weise, wie das Gerät eine Verbindung mit Ihrem Dienst herstellt, sodass es die ID des erstellten Modells ankündigt. Mithilfe des Modells kann der Dienst dann die Gerätefunktionen verstehen. IoT Central kann die Modell-ID beispielsweise verwenden, um das Modell automatisch aus einem Repository abzurufen und eine Gerätevorlage für Ihr Gerät zu generieren.

IoT-Geräte stellen entweder über Device Provisioning Service (DPS) oder direkt mit einer Verbindungszeichenfolge eine Verbindung mit Ihrem IoT-Dienst her.

Wenn Ihr Gerät DPS zum Herstellen der Verbindung verwendet, schließen Sie die Modell-ID in die Nutzdaten ein, die Sie beim Registrieren des Geräts senden. Für das zuvor gezeigte Beispielmodell sehen die Nutzdaten wie folgt aus:

```json
{
  "modelId" : "dtmi:com:example:ConvertSample;1"
}
```

Weitere Informationen finden Sie unter [Runtimeregistrierung: Registrieren des Geräts](/rest/api/iot-dps/device/runtime-registration/register-device).

Wenn Ihr Gerät DPS zum Herstellen der Verbindung verwendet oder die Verbindung direkt mit einer Verbindungszeichenfolge herstellt, fügen Sie die Modell-ID an der Stelle hinzu, an der Ihr Code eine Verbindung mit IoT Hub herstellt. Beispiel:

```c
#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2020-09-30&model-id=dtmi:com:example:ConvertSample;1"

// ...

mosquitto_username_pw_set(mosq, USERNAME, PWD);

// ...

rc = mosquitto_connect(mosq, HOST, PORT, 10);
```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun wissen, wie Sie ein vorhandenes Gerät in ein IoT Plug & Play-Gerät konvertieren, sollten Sie als Nächstes den [Leitfaden zur Modellierung mit IoT Plug & Play](concepts-modeling-guide.md) lesen.
