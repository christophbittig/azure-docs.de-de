---
title: Azure IoT Hub als Event Grid-Quelle
description: In diesem Artikel werden die Eigenschaften und das Schema für Azure IoT Hub-Ereignisse beschrieben. Dabei werden die verfügbaren Ereignistypen, ein Beispielereignis und Ereigniseigenschaften aufgelistet.
ms.topic: conceptual
ms.date: 09/15/2021
ms.openlocfilehash: ac3cac72cc9998d4fb78ed0459e5e07df1125df0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128606321"
---
# <a name="azure-iot-hub-as-an-event-grid-source"></a>Azure IoT Hub als Event Grid-Quelle
In diesem Artikel werden die Eigenschaften und das Schema für Azure IoT Hub-Ereignisse beschrieben. Eine Einführung in Ereignisschemas finden Sie unter [Azure Event Grid-Ereignisschema](event-schema.md). 

## <a name="available-event-types"></a>Verfügbare Ereignistypen

Azure IoT Hub gibt die folgenden Ereignistypen aus:

| Ereignistyp | BESCHREIBUNG |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Wird ausgelöst, wenn ein Gerät bei einem IoT Hub registriert wird. |
| Microsoft.Devices.DeviceDeleted | Wird ausgelöst, wenn ein Gerät aus einem IoT Hub gelöscht wird. | 
| Microsoft.Devices.DeviceConnected | Wird ausgelöst, wenn ein Gerät mit einem IoT Hub verbunden wird. |
| Microsoft.Devices.DeviceDisconnected | Wird ausgelöst, wenn ein Gerät von einem IoT Hub getrennt wird. | 
| Microsoft.Devices.DeviceTelemetry | Wird veröffentlicht, wenn eine Telemetrienachricht an einen IoT-Hub gesendet wird. |

## <a name="example-event"></a>Beispielereignis

# <a name="event-grid-event-schema"></a>[Event Grid-Ereignisschema](#tab/event-grid-event-schema)

Die Schemas für DeviceConnected- und DeviceDisconnected-Ereignisse verfügen über die gleiche Struktur. Dieses Beispielereignis zeigt das Schema eines Ereignisses, das ausgelöst wird, wenn ein Gerät mit einem IoT Hub verbunden wird:

```json
[{
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8", 
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceConnected", 
  "eventTime": "2018-06-02T19:17:44.4383997Z", 
  "data": {
    "deviceConnectionStateEventInfo": {
      "sequenceNumber":
        "000000000000000001D4132452F67CE200000002000000000000000000000001"
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID"
  }, 
  "dataVersion": "1", 
  "metadataVersion": "1" 
}]
```

Das DeviceTelemetry-Ereignis wird ausgelöst, wenn ein Telemetrieereignis an einen IoT-Hub gesendet wird. Ein Beispielschema für dieses Ereignis wird unten gezeigt.

```json
[{
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceTelemetry",
  "eventTime": "2019-01-07T20:58:30.48Z",
  "data": {        
      "body": {            
          "Weather": {                
              "Temperature": 900            
          },
          "Location": "USA"        
      },
        "properties": {            
          "Status": "Active"        
        },
        "systemProperties": {            
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "d1",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "123455432199234570",
            "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
            "iothub-message-source": "Telemetry"        
        }    
    },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Das Schema für DeviceCreated- und DeviceDeleted-Ereignisse verfügen über die gleiche Struktur. Das Beispielereignis zeigt das Schema eines Ereignisses, das ausgelöst wird, wenn ein Gerät bei einem IoT Hub registriert wird:

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "null",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

# <a name="cloud-event-schema"></a>[Cloudereignisschema](#tab/cloud-event-schema)

Die Schemas für DeviceConnected- und DeviceDisconnected-Ereignisse verfügen über die gleiche Struktur. Dieses Beispielereignis zeigt das Schema eines Ereignisses, das ausgelöst wird, wenn ein Gerät mit einem IoT Hub verbunden wird:

```json
[{
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8", 
  "source": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "type": "Microsoft.Devices.DeviceConnected", 
  "time": "2018-06-02T19:17:44.4383997Z", 
  "data": {
    "deviceConnectionStateEventInfo": {
      "sequenceNumber":
        "000000000000000001D4132452F67CE200000002000000000000000000000001"
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID"
  }, 
  "specversion": "1.0"
}]
```

Das DeviceTelemetry-Ereignis wird ausgelöst, wenn ein Telemetrieereignis an einen IoT-Hub gesendet wird. Ein Beispielschema für dieses Ereignis wird unten gezeigt.

```json
[{
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "source": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "type": "Microsoft.Devices.DeviceTelemetry",
  "time": "2019-01-07T20:58:30.48Z",
  "data": {        
      "body": {            
          "Weather": {                
              "Temperature": 900            
          },
          "Location": "USA"        
      },
        "properties": {            
          "Status": "Active"        
        },
        "systemProperties": {            
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "d1",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "123455432199234570",
            "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
            "iothub-message-source": "Telemetry"        
        }    
    },
  "specversion": "1.0"
}]
```

Das Schema für DeviceCreated- und DeviceDeleted-Ereignisse verfügen über die gleiche Struktur. Das Beispielereignis zeigt das Schema eines Ereignisses, das ausgelöst wird, wenn ein Gerät bei einem IoT Hub registriert wird:

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "source": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "type": "Microsoft.Devices.DeviceCreated",
  "time": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "null",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice"
  },
  "specversion": "1.0"
}]
```

---


### <a name="event-properties"></a>Ereigniseigenschaften

# <a name="event-grid-event-schema"></a>[Event Grid-Ereignisschema](#tab/event-grid-event-schema)

Alle Ereignisse enthalten die gleichen Daten der obersten Ebene: 

| Eigenschaft | type | Beschreibung |
| -------- | ---- | ----------- |
| `id` | Zeichenfolge | Eindeutiger Bezeichner für das Ereignis. |
| `topic` | Zeichenfolge | Vollständiger Ressourcenpfaf zur Ereignisquelle. Dieses Feld ist nicht beschreibbar. Dieser Wert wird von Event Grid bereitgestellt. |
| `subject` | Zeichenfolge | Vom Herausgeber definierter Pfad zum Ereignisbetreff |
| `eventType` | Zeichenfolge | Einer der registrierten Ereignistypen für die Ereignisquelle. |
| `eventTime` | Zeichenfolge | Die Zeit, in der das Ereignis generiert wird, basierend auf der UTC-Zeit des Anbieters. |
| `data` | Objekt (object) | IoT Hub-Ereignisdaten.  |
| `dataVersion` | Zeichenfolge | Die Schemaversion des Datenobjekts. Der Herausgeber definiert die Schemaversion. |
| `metadataVersion` | Zeichenfolge | Die Schemaversion der Ereignismetadaten. Event Grid definiert das Schema der Eigenschaften der obersten Ebene. Dieser Wert wird von Event Grid bereitgestellt. |

# <a name="cloud-event-schema"></a>[Cloudereignisschema](#tab/cloud-event-schema)

Alle Ereignisse enthalten die gleichen Daten der obersten Ebene: 


| Eigenschaft | type | Beschreibung |
| -------- | ---- | ----------- |
| `id` | Zeichenfolge | Eindeutiger Bezeichner für das Ereignis. |
| `source` | Zeichenfolge | Vollständiger Ressourcenpfaf zur Ereignisquelle. Dieses Feld ist nicht beschreibbar. Dieser Wert wird von Event Grid bereitgestellt. |
| `subject` | Zeichenfolge | Vom Herausgeber definierter Pfad zum Ereignisbetreff |
| `type` | Zeichenfolge | Einer der registrierten Ereignistypen für die Ereignisquelle. |
| `time` | Zeichenfolge | Die Zeit, in der das Ereignis generiert wird, basierend auf der UTC-Zeit des Anbieters. |
| `data` | Objekt (object) | IoT Hub-Ereignisdaten.  |
| `specversion` | Zeichenfolge | Version der CloudEvents-Schemaspezifikation. |

---

Für IoT Hub-Ereignisse enthält das Datenobjekt die folgenden Eigenschaften:

| Eigenschaft | type | Beschreibung |
| -------- | ---- | ----------- |
| `hubName` | Zeichenfolge | Name des IoT Hubs, in dem das Gerät erstellt bzw. aus dem das Gerät gelöscht wurde. |
| `deviceId` | Zeichenfolge | Der eindeutige Bezeichner des Geräts. Eine Zeichenfolge mit Beachtung von Groß-/Kleinschreibung, die bis zu 128 Zeichen lang sein kann und alphanumerische 7-Bit-ASCII-Zeichen sowie die folgenden Sonderzeichen unterstützt: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |

Die Inhalte des Datenobjekts unterscheiden sich für jeden Ereignisherausgeber. 

Für die IoT Hub-Ereignisse **Gerät verbunden** und **Gerät getrennt** enthält das Datenobjekt die folgenden Eigenschaften:

| Eigenschaft | type | Beschreibung |
| -------- | ---- | ----------- |
| `moduleId` | Zeichenfolge | Der eindeutige Bezeichner des Moduls. Dieses Feld dient nur der Ausgabe für Modulgeräte. Eine Zeichenfolge mit Beachtung von Groß-/Kleinschreibung, die bis zu 128 Zeichen lang sein kann und alphanumerische 7-Bit-ASCII-Zeichen sowie die folgenden Sonderzeichen unterstützt: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |
| `deviceConnectionStateEventInfo` | Objekt (object) | Ereignisinformationen zum Verbindungsstatus des Geräts
| `sequenceNumber` | Zeichenfolge | Eine Zahl, die hilft, die Reihenfolge der Ereignisse „Gerät verbunden“ oder „Gerät getrennt“ anzugeben. Die letzten Ereignisse haben eine höhere Sequenznummer als frühere Ereignisse. Diese Zahl kann sich um mehr als 1 ändern, aber sie ist immer ansteigend. Weitere Informationen finden Sie unter [Verwenden der Sequenznummer](../iot-hub/iot-hub-how-to-order-connection-state-events.md). |

Für ein IoT Hub-Ereignis zur **Gerätetelemetrie** enthält das Datenobjekt die D2C-Nachricht im [IoT Hub-Nachrichtenformat](../iot-hub/iot-hub-devguide-messages-construct.md) und weist die folgenden Eigenschaften auf:

| Eigenschaft | type | BESCHREIBUNG |
| -------- | ---- | ----------- |
| `body` | Zeichenfolge | Der Inhalt der Nachricht vom Gerät. |
| `properties` | Zeichenfolge | Anwendungseigenschaften sind benutzerdefinierte Zeichenfolgen, die der Nachricht hinzugefügt werden können. Diese Felder sind optional. |
| `system properties` | Zeichenfolge | Mithilfe von [Systemeigenschaften](../iot-hub/iot-hub-devguide-routing-query-syntax.md#system-properties) werden der Inhalt und die Quelle von Nachrichten identifiziert. Gerätetelemetrienachrichten müssen in einem gültigen JSON-Format vorliegen, und in den Systemeigenschaften der Nachricht müssen contentType auf JSON und contentEncoding auf UTF-8 festgelegt sein. Wenn dies nicht festgelegt ist, schreibt IoT Hub die Nachrichten in Base64-codiertem Format.  |

Für die IoT Hub-Ereignisse **Gerät erstellt** und **Gerät gelöscht** enthält das Datenobjekt die folgenden Eigenschaften:

| Eigenschaft | type | BESCHREIBUNG |
| -------- | ---- | ----------- |
| `twin` | Objekt (object) | Informationen zum Gerätezwilling, der Clouddarstellung der Anwendungsgeräte-Metadaten. | 
| `deviceID` | Zeichenfolge | Der eindeutige Bezeichner des Gerätezwillings. | 
| `etag` | Zeichenfolge | Ein Validierungssteuerelement, mit dem die Konsistenz von Aktualisierungen eines Gerätezwillings sichergestellt wird. Jedes ETag ist pro Gerätezwilling garantiert eindeutig. |  
| `deviceEtag` | Zeichenfolge | Ein Validierungssteuerelement, mit dem die Konsistenz von Aktualisierungen einer Geräteregistrierung sichergestellt wird. Jedes deviceEtag ist pro Gerätezwilling garantiert eindeutig. |
| `status` | Zeichenfolge | Gibt an, ob der Gerätezwilling aktiviert oder deaktiviert ist. | 
| `statusUpdateTime` | Zeichenfolge | Der ISO8601-Zeitstempel der letzten Statusaktualisierung für den Gerätezwilling. |
| `connectionState` | Zeichenfolge | Gibt an, ob das Gerät verbunden oder getrennt ist. | 
| `lastActivityTime` | Zeichenfolge | Der ISO8601-Zeitstempel der letzten Aktivität. | 
| `cloudToDeviceMessageCount` | integer | Die Anzahl von Nachrichten, die von der Cloud an das Gerät gesendet wurden. | 
| `authenticationType` | Zeichenfolge | Der für dieses Gerät verwendete Authentifizierungstyp: entweder `SAS`, `SelfSigned` oder `CertificateAuthority`. |
| `x509Thumbprint` | Zeichenfolge | Der Fingerabdruck ist ein eindeutiger Wert für das x509-Zertifikat. Dieser wird üblicherweise zur Suche nach einem bestimmten Zertifikat in einem Zertifikatspeicher verwendet. Der Fingerabdruck wird mithilfe des SHA1-Algorithmus dynamisch generiert und ist nicht physisch im Zertifikat vorhanden. | 
| `primaryThumbprint` | Zeichenfolge | Der primäre Fingerabdruck für das x509-Zertifikat. |
| `secondaryThumbprint` | Zeichenfolge | Der sekundäre Fingerabdruck für das x509-Zertifikat. | 
| `version` | integer | Ein ganzzahliger Wert, der bei jeder Aktualisierung des Gerätezwillings um 1 erhöht wird. |
| `desired` | Objekt (object) | Ein Teil der Eigenschaften, der nur vom Anwendungs-Back-End geschrieben und vom Gerät gelesen werden kann. | 
| `reported` | Objekt (object) | Ein Teil der Eigenschaften, der nur vom Gerät geschrieben und vom Anwendungs-Back-End gelesen werden kann. |
| `lastUpdated` | Zeichenfolge | Der ISO8601-Zeitstempel der letzten Eigenschaftenaktualisierung für den Gerätezwilling. | 

## <a name="tutorials-and-how-tos"></a>Tutorials und Vorgehensweisen
|Titel  |BESCHREIBUNG  |
|---------|---------|
| [Senden von E-Mail-Benachrichtigungen zu Azure IoT Hub-Ereignissen mit Logic Apps](publish-iot-hub-events-to-logic-apps.md) | Eine Logik-App sendet jedes Mal eine E-Mail-Benachrichtigung, wenn Ihrer IoT Hub-Instanz ein Gerät hinzugefügt wird. |
| [Reagieren auf IoT Hub-Ereignisse mithilfe von Event Grid zum Auslösen von Aktionen](../iot-hub/iot-hub-event-grid.md) | Übersicht über die Integration von IoT Hub-Instanzen in Event Grid |
| [Sortieren von Ereignissen im Zusammenhang mit der Herstellung und Trennung von Geräteverbindungen](../iot-hub/iot-hub-how-to-order-connection-state-events.md) | Erfahren Sie, wie Sie Ereignisse zum Verbindungsstatus von Geräten sortieren. |

## <a name="next-steps"></a>Nächste Schritte

* Eine Einführung zu Azure Event Grid finden Sie unter [Einführung in Azure Event Grid](overview.md).
* Weitere Informationen zur Zusammenarbeit von IoT Hub und Event Grid finden Sie unter [Reagieren auf IoT Hub-Ereignisse mithilfe von Event Grid zum Auslösen von Aktionen](../iot-hub/iot-hub-event-grid.md).