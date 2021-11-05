---
title: Verwenden der REST-API zum Verwalten des Datenexports in Azure IoT Central
description: Hier erfahren Sie mehr über das Verwenden der IoT Central-REST-API zum Verwalten des Datenexports in einer Anwendung.
author: v-krishnag
ms.author: v-krishnag
ms.date: 10/18/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: cb538b6c8777a9aeb1d02fe705a87831f7aaf5b9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131433779"
---
# <a name="how-to-use-the-iot-central-rest-api-to-manage-data-exports"></a>Verwenden der IoT Central-REST-API zum Verwalten von Datenexporten

Mit der IoT Central-REST-API können Sie Clientanwendungen entwickeln, die in IoT Central-Anwendungen integriert werden. Mit der REST-API können Sie in Ihrer IoT Central-Anwendung [Datenexporte](howto-export-data.md) erstellen und verwalten.

Jeder IoT Central-REST-API-Aufruf erfordert einen Autorisierungsheader. Weitere Informationen finden Sie unter [Authentifizieren und Autorisieren von IoT Central-REST-API-Aufrufen](howto-authorize-rest-api.md).

Die Referenzdokumentation für die IoT Central-REST-API finden Sie unter [Azure IoT Central: Referenz zur REST-API](/rest/api/iotcentral/).

## <a name="data-export"></a>Datenexport

Sie können das IoT Central-Datenexportfeature verwenden, um Telemetriedaten, Eigenschaftsänderungen, Gerätekonnektivitätsereignisse, Gerätelebenszyklusereignisse und Lebenszyklusereignisse von Gerätevorlagen an Ziele wie Azure Event Hubs, Azure Service Bus, Azure Blob Storage und Webhookendpunkte zu streamen.

Jede Datenexportdefinition kann Daten an ein oder mehrere Ziele senden. Erstellen Sie die Zieldefinitionen, bevor Sie die Exportdefinition erstellen.

### <a name="create-or-update-a-destination"></a>Erstellen oder Aktualisieren eines Ziels

Verwenden Sie die folgende Anforderung, um eine Zieldefinition zu erstellen oder zu aktualisieren:

```http
PUT https://{subdomain}.{baseDomain}/api/dataExport/destinations/{destinationId}?api-version=1.1-preview
```

* destinationId: Dies ist die eindeutige ID für das Ziel.

Das folgende Beispiel zeigt einen Anforderungstext, der ein Blobspeicherziel erstellt:

```json
{
  "displayName": "Blob Storage Destination",
  "type": "blobstorage@v1",
  "connectionString": "DefaultEndpointsProtocol=https;AccountName=yourAccountName;AccountKey=********;EndpointSuffix=core.windows.net",
  "containerName": "central-data"
}
```

Der Anforderungstext enthält einige erforderliche Felder:

* `displayName`: Dies ist der Anzeigename des Ziels.
* `type`:  Dies ist der Typ des Zielobjekts, das eines der folgenden sein kann: `blobstorage@v1`, `dataexplorer@v1`, `eventhubs@v1`, `servicebusqueue@v1`, `servicebustopic@v1` oder `webhook@v1`.
* `connectionString`: Dies ist die Verbindungszeichenfolge für den Zugriff auf die Zielressource.
* `containerName`: Für ein Blobspeicherziel ist dies der Name des Containers, in den Daten geschrieben werden sollen.

Die Antwort auf diese Anforderung sieht wie das folgende Beispiel aus: 

```json
{
    "id": "8dbcdb53-c6a7-498a-a976-a824b694c150",
    "displayName": "Blob Storage Destination",
    "type": "blobstorage@v1",
    "connectionString": "DefaultEndpointsProtocol=https;AccountName=yourAccountName;AccountKey=********;EndpointSuffix=core.windows.net",
    "containerName": "central-data",
    "status": "waiting"
}
```

### <a name="get-a-destination-by-id"></a>Abrufen eines Ziels nach ID

Verwenden Sie die folgende Anforderung, um Details eines Ziels aus Ihrer Anwendung abzurufen:

```http
GET https://{subdomain}.{baseDomain}/api/dataExport/destinations/{destinationId}?api-version=1.1-preview
```

Die Antwort auf diese Anforderung sieht wie das folgende Beispiel aus:

```json
{
    "id": "8dbcdb53-c6a7-498a-a976-a824b694c150",
    "displayName": "Blob Storage Destination",
    "type": "blobstorage@v1",
    "connectionString": "DefaultEndpointsProtocol=https;AccountName=yourAccountName;AccountKey=********;EndpointSuffix=core.windows.net",
    "containerName": "central-data",
    "status": "waiting"
}
```

### <a name="list-destinations"></a>Auflisten von Zielen

Verwenden Sie die folgende Anforderung, um eine Liste der Ziele aus Ihrer Anwendung abzurufen:

```http
GET https://{subdomain}.{baseDomain}/api/dataExport/destinations?api-version=1.1-preview
```

Die Antwort auf diese Anforderung sieht wie das folgende Beispiel aus: 

```json
{
    "value": [
        {
            "id": "8dbcdb53-c6a7-498a-a976-a824b694c150",
            "displayName": "Blob Storage Destination",
            "type": "blobstorage@v1",
            "authorization": {
                "type": "connectionString",
                "connectionString": DefaultEndpointsProtocol=https;AccountName=yourAccountName;AccountKey=********;EndpointSuffix=core.windows.net",
                "containerName": "central-data"
            },
            "status": "waiting"
        },
        {
            "id": "9742a8d9-c3ca-4d8d-8bc7-357bdc7f39d9",
            "displayName": "Webhook destination",
            "type": "webhook@v1",
            "url": "http://requestbin.net/r/f7x2i1ug",
            "headerCustomizations": {},
            "status": "error",
        }
        }
    ]
}
```

### <a name="patch-a-destination"></a>Patchen eines Ziels

```http
PATCH https://{subdomain}.{baseDomain}/api/dataExport/destinations/{destinationId}?api-version=1.1-preview
```

Sie können Folgendes verwenden, um ein inkrementelles Update für einen Export durchzuführen. Der Beispielanforderungstext sieht wie im folgenden Beispiel aus, in dem der `displayName` auf ein Ziel aktualisiert wird:

```json
{
  "displayName": "Blob Storage",
  "type": "blobstorage@v1",
  "connectionString": "DefaultEndpointsProtocol=https;AccountName=yourAccountName;AccountKey=********;EndpointSuffix=core.windows.net",
  "containerName": "central-data"
}
```

Die Antwort auf diese Anforderung sieht wie das folgende Beispiel aus:

```json
{
    "id": "8dbcdb53-c6a7-498a-a976-a824b694c150",
    "displayName": "Blob Storage",
    "type": "blobstorage@v1",
    "connectionString": "DefaultEndpointsProtocol=https;AccountName=yourAccountName;AccountKey=********;EndpointSuffix=core.windows.net",
    "containerName": "central-data",
    "status": "waiting"
}   
```

### <a name="delete-a-destination"></a>Löschen eines Ziels

Verwenden Sie die folgende Anforderung zum Löschen eines Ziels:

```http
DELETE https://{subdomain}.{baseDomain}/api/dataExport/destinations/{destinationId}?api-version=1.1-preview
```

### <a name="create-or-update-an-export-definition"></a>Erstellen oder Aktualisieren einer Exportdefinition

Verwenden Sie die folgende Anforderung, um eine Datenexportdefinition zu erstellen oder zu aktualisieren:

```http
PUT https://{subdomain}.{baseDomain}/api/dataExport/exports/{exportId}?api-version=1.1-preview
```

Das folgende Beispiel zeigt einen Anforderungstext, der eine Exportdefinition für Gerätetelemetriedaten erstellt:

```json
{
    "displayName": "Enriched Export",
    "enabled": true,
    "source": "telemetry",
    "enrichments": {
        "Custom data": {
            "value": "My value"
        }
    },
    "destinations": [
        {
            "id": "8dbcdb53-c6a7-498a-a976-a824b694c150"
        }
    ]
}
```

Der Anforderungstext enthält einige erforderliche Felder:

* `displayName`: Dies ist der Anzeigename des Exports.
* `enabled`: Hiermit können Sie einstellen, ob bei einem Export das Senden von Daten gestartet oder beendet werden soll.
* `source`: Dies ist der zu exportierende Datentyp.
* `destinations`: Dies ist die Liste der Ziele, an die beim Exportvorgang Daten gesendet werden sollen. Die Ziel-IDs müssen bereits in der Anwendung vorhanden sein.

Es gibt einige optionale Felder, mit denen Sie dem Export weitere Details hinzufügen können.

* `enrichments`: Dies sind zusätzliche Informationen, die in jede gesendete Nachricht eingeschlossen werden sollten. Daten werden als Schlüssel-Wert-Paare dargestellt, wobei der Schlüssel der Name der Erweiterung ist, die in der Ausgabenachricht angezeigt wird, und der Wert die zu sendenden Daten identifiziert.
* `filter`: Mit dieser Abfrage wird definiert, welche Ereignisse aus der Quelle exportiert werden sollen.

Die Antwort auf diese Anforderung sieht wie das folgende Beispiel aus: 

```json
{
    "id": "6e93df53-1ce5-45e4-ad61-3eb0d684b3a5",
    "displayName": "Enriched Export",
    "enabled": true,
    "source": "telemetry",
    "enrichments": {
        "Custom data": {
            "value": "My value"
        }
    },
    "destinations": [
        {
            "id": "9742a8d9-c3ca-4d8d-8bc7-357bdc7f39d9"
        }
    ],
    "status": "starting"
}
```

### <a name="get-an-export-by-id"></a>Abrufen eines Exports nach ID

Verwenden Sie die folgende Anforderung zum Abrufen von Details zu einer Exportdefinition aus Ihrer Anwendung:

```http
GET https://{subdomain}.{baseDomain}/api/dataExport/exports/{exportId}?api-version=1.1-preview
```

Die Antwort auf diese Anforderung sieht wie das folgende Beispiel aus:

```json
{
    "id": "8dbcdb53-c6a7-498a-a976-a824b694c150",
    "displayName": "Blob Storage Destination",
    "type": "blobstorage@v1",
    "connectionString": "DefaultEndpointsProtocol=https;AccountName=yourAccountName;AccountKey=********;EndpointSuffix=core.windows.net",
    "containerName": "central-data",
    "status": "waiting"
}
```

### <a name="list-export-definitions"></a>Auflisten von Exportdefinitionen

Verwenden Sie die folgende Anforderung, um eine Liste der Exportdefinitionen aus Ihrer Anwendung abzurufen:

```http
GET https://{subdomain}.{baseDomain}/api/dataExport/exports?api-version=1.1-preview
```

Die Antwort auf diese Anforderung sieht wie das folgende Beispiel aus: 

```json
{
  "value": [
    {
      "id": "6e93df53-1ce5-45e4-ad61-3eb0d684b3a5",
      "displayName": "Enriched Export",
      "enabled": true,
      "source": "telemetry",
      "enrichments": {
        "Custom data": {
          "value": "My value"
        }
      },
      "destinations": [
        {
          "id": "9742a8d9-c3ca-4d8d-8bc7-357bdc7f39d9"
        }
      ],
      "status": "starting"
    },
    {
      "id": "802894c4-33bc-4f1e-ad64-e886f315cece",
      "displayName": "Enriched Export",
      "enabled": true,
      "source": "telemetry",
      "enrichments": {
        "Custom data": {
          "value": "My value"
        }
      },
      "destinations": [
        {
          "id": "9742a8d9-c3ca-4d8d-8bc7-357bdc7f39d9"
        }
      ],
      "status": "healthy"
    }
  ]
}
```

### <a name="patch-an-export-definition"></a>Patchen einer Exportdefinition

```http
PATCH https://{subdomain}.{baseDomain}/dataExport/exports/{exportId}?api-version=1.1-preview
```

Sie können Folgendes verwenden, um ein inkrementelles Update für einen Export durchzuführen. Der Beispielanforderungstext sieht wie im folgenden Beispiel aus, in dem `enrichments` auf einen Export aktualisiert wird:

```json
{
    "displayName": "Enriched Export",
    "enabled": true,
    "source": "telemetry",
    "enrichments": {
        "Custom data": {
            "value": "My value 2"
        }
    },
    "destinations": [
        {
            "id": "9742a8d9-c3ca-4d8d-8bc7-357bdc7f39d9"
        }
    ]
}
```

Die Antwort auf diese Anforderung sieht wie das folgende Beispiel aus:

```json
{
    "id": "6e93df53-1ce5-45e4-ad61-3eb0d684b3a5",
    "displayName": "Enriched Export",
    "enabled": true,
    "source": "telemetry",
    "enrichments": {
        "Custom data": {
            "value": "My"
        }
    },
    "destinations": [
        {
            "id": "9742a8d9-c3ca-4d8d-8bc7-357bdc7f39d9"
        }
    ],
    "status": "healthy"
}
```

### <a name="delete-an-export-definition"></a>Löschen einer Exportdefinition

Verwenden Sie die folgende Anforderung, um eine Exportdefinition zu löschen:

```http
DELETE https://{subdomain}.{baseDomain}/api/dataExport/destinations/{destinationId}?api-version=1.1-preview
```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie erfahren haben, wie Sie Datenexporte mit der REST-API verwalten können, empfiehlt sich als nächster Schritt das [Verwenden der IoT Central-REST-API zum Verwalten von Gerätevorlagen](howto-manage-device-templates-with-rest-api.md).
