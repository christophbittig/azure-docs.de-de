---
title: Einschränken des ausgehenden IoT Hub-Netzwerkzugriffs und Verhindern von Datenverlust
description: 'Entwicklerhandbuch: Konfigurieren von IoT Hub für ausgehende Daten nur an vertrauenswürdige Speicherorte.'
author: jlian
manager: briz
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/24/2021
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: f4f8044237c82212723a54b677d77bc5aecb95a5
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867790"
---
# <a name="restrict-outbound-network-access-for-azure-iot-hub"></a>Einschränken des ausgehenden Netzwerkzugriffs für Azure IoT Hub

IoT Hub unterstützt ausgehende Daten zu anderen Diensten durch [Routing an benutzerdefinierte Endpunkte](iot-hub-devguide-messages-d2c.md), [Dateiupload](iot-hub-devguide-file-upload.md)und [Geräteidentitätsexport](iot-hub-bulk-identity-mgmt.md). Wenn Sie zusätzliche Sicherheit in einer Unternehmensumgebung schaffen möchten, verwenden Sie die API `restrictOutboundNetworkAccess`. Damit werden ausgehende IoT-Hub-Daten auf nur explizit genehmigte Ziele eingeschränkt. Dieses Feature steht im Azure-Portal zurzeit nicht zur Verfügung.

## <a name="enabling-the-restriction"></a>Aktivieren der Einschränkung

Wenn Sie das Feature aktivieren möchten, verwenden Sie eine beliebige Methode zum Aktualisieren der IoT Hub-Ressourceneigenschaften (ein `PUT`), um `restrictOutboundNetworkAccess` auf `true` festzulegen, während Sie ein `allowedFqdnList` mit voll qualifizierten Domänennamen (Fully Qualified Domain Names, FQDNs) als Array einbeziehen. 

Ein Beispiel für die JSON-Darstellung, die bei der [„Create“- oder „Update“-Methode](/rest/api/iothub/iothubresource/createorupdate) verwendet werden soll:

```json
{
...
            "properties": {
              ...
                "restrictOutboundNetworkAccess": true,
                "allowedFqdnList": [
                    "my-eventhub.servicebus.windows.net",
                    "iothub-ns-built-in-endpoint-2917414-51ea2487eb.servicebus.windows.net"
                ]
              ...
            },
            "sku": {
                "name": "S1",
                "capacity": 1
            }
        }
    }
}
```
Wenn Sie dasselbe Update über die Azure CLI durchführen möchten, führen Sie Folgendes aus:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --set properties.restrictOutboundNetworkAccess=true properties.allowedFqdnList="['my-eventhub.servicebus.windows.net','iothub-ns-built-in-endpoint-2917414-51ea2487eb.servicebus.windows.net']"
```

## <a name="restricting-outbound-network-access-with-existing-routes"></a>Einschränken des ausgehenden Netzwerkzugriffs bei vorhandenen Routen

Sobald `restrictOutboundNetworkAccess` auf `true` festgelegt wird, schlagen Versuche fehl, Daten an Ziele außerhalb der zulässigen FQDNs auszugeben. Selbst vorhandene konfigurierte Routen funktionieren nicht mehr, wenn der benutzerdefinierte Endpunkt in der Liste zulässiger FQDNs nicht enthalten ist.

## <a name="built-in-endpoint"></a>Integrierter Endpunkt

Wenn `restrictOutboundNetworkAccess` auf `true` festgelegt ist, wird der integrierte Event Hub-kompatible Endpunkt von der Einschränkung nicht ausgenommen. Anders ausgedrückt: Sie müssen den FQDN für den integrierten Endpunkt in die Liste der zulässigen FQDNs aufnehmen, damit er weiterhin funktioniert.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zur Verwendung von verwalteter Identität für ausgehende Daten finden Sie unter [IoT Hub-Unterstützung für verwaltete Identitäten](iot-hub-managed-identity.md).
- Informationen zum Einschränken des eingehenden Netzwerkzugriffs finden Sie unter [Verwalten des Zugriffs über öffentliche Netzwerke für Ihren IoT-Hub](iot-hub-public-network-access.md) und unter [IoT Hub-Unterstützung für virtuelle Netzwerke mit Private Link](virtual-network-support.md).