---
title: Verwenden der REST-API zum Verwalten von Aufträgen in Azure IoT Central
description: Verwenden der IoT Central-REST-API zum Erstellen und Verwalten von Aufträgen in einer Anwendung
author: dominicbetts
ms.author: dobett
ms.date: 08/30/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 47548b3c7e1fa7503e3f30c155755d6fe4f29031
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128667044"
---
# <a name="how-to-use-the-iot-central-rest-api-to-create-and-manage-jobs"></a>Verwenden der IoT Central-REST-API zum Erstellen und Verwalten von Aufträgen

Mit der IoT Central-REST-API können Sie Clientanwendungen entwickeln, die in IoT Central-Anwendungen integriert werden. Mit der REST-API können Sie in Ihrer IoT Central-Anwendung Aufträge erstellen und verwalten. Mit der REST-API können Sie Folgendes durchführen:

- Auflisten von Aufträgen und Anzeigen von Auftragsdetails in Ihrer Anwendung.
- Erstellen von Aufträgen in Ihrer Anwendung.
- Beenden, Fortsetzen und erneutes Ausführen von Aufträgen in Ihrer Anwendung.

> [!IMPORTANT]
> Die Auftrags-API befindet sich zurzeit in der Vorschau. Alle in diesem Artikel beschriebenen REST-API-Aufrufe sollten `?api-version=preview` enthalten.

In diesem Artikel wird beschrieben, wie Sie die Auftrags-API (`/jobs/{job_id}`) zum Steuern von Geräten in einem Massenvorgang verwenden. Sie können Geräte auch einzeln steuern.

Jeder IoT Central-REST-API-Aufruf erfordert einen Autorisierungsheader. Weitere Informationen finden Sie unter [Authentifizieren und Autorisieren von IoT Central-REST-API-Aufrufen](howto-authorize-rest-api.md).

Die Referenzdokumentation für die IoT Central-REST-API finden Sie unter [Azure IoT Central: Referenz zur REST-API](/rest/api/iotcentral/).

> [!TIP]
> Die [Vorschau-API](/rest/api/iotcentral/1.1-previewdataplane/jobs) enthält Unterstützung für das neue [Organisationsfeature](howto-create-organizations.md).

Informationen zum Erstellen und Verwalten von Aufträgen in der Benutzeroberfläche finden Sie unter [Verwalten von Geräten per Massenvorgang in Ihrer Azure IoT Central-Anwendung](howto-manage-devices-in-bulk.md).

## <a name="job-payloads"></a>Auftragsnutzlasten

Viele der in diesem Artikel beschriebenen APIs enthalten eine Definition, die wie der folgende JSON-Codeausschnitt aussieht:

```json
{
  "id": "job-014",
  "displayName": "Set target temperature",
  "description": "Set target temperature for all thermostat devices",
  "group": "833d7a7d-8f99-4e04-9e56-745806bdba6e",
  "batch": {
    "type": "percentage",
    "value": 25
  },
  "cancellationThreshold": {
    "type": "percentage",
    "value": 10,
    "batch": false
  },
  "data": [
    {
      "type": "PropertyJobData",
      "target": "dtmi:modelDefinition:zomtmdxh:eqod32zbyl",
      "path": "targetTemperature",
      "value": "56"
    }
  ],
  "status": "complete"
}
```

In der folgenden Tabelle werden die Felder im vorstehenden JSON-Codeausschnitt beschrieben:

| Feld | BESCHREIBUNG |
| ----- | ----------- |
| `id` | Eine eindeutige ID für den Auftrag in Ihrer Anwendung. |
| `displayName` | Der Anzeigename für den Auftrag in Ihrer Anwendung. |
| `description` | Eine Beschreibung des Auftrags. |
| `group` | Die ID der Gerätegruppe, für die der Auftrag gilt. Verwenden Sie die Vorschau-REST-API `deviceGroups`zum Abrufen einer Liste der Gerätegruppen in Ihrer Anwendung. |
| `status` | Der [Status](howto-manage-devices-in-bulk.md#view-job-status) des Auftrags. Einer der folgenden Werte: `complete`, `cancelled`, `failed`, `pending`, `running`, `stopped`. |
| `batch` | Falls vorhanden, wird in diesem Abschnitt definiert, wie die Geräte im Auftrag [batchweise ausgeführt werden](howto-manage-devices-in-bulk.md#create-and-run-a-job). |
| `batch/type` | Die Größe jedes Batches ist entweder ein `percentage` der gesamten Geräte in der Gruppe oder eine `number` von Geräten, |
| `batch/value` | also entweder der Prozentsatz der Geräte oder die Anzahl der Geräte in jedem Batch. |
| `cancellationThreshold` | Falls vorhanden, wird in diesem Abschnitt der [Abbruchschwellenwert](howto-manage-devices-in-bulk.md#create-and-run-a-job) für den Auftrag definiert. |
| `cancellationThreshold/batch` | `true` oder `false`. „true“ gibt an, dass der Abbruchschwellenwert für jeden Batch festgelegt wird. `false` gibt an, dass der Abbruchschwellenwert für den gesamten Auftrag gilt. |
| `cancellationThreshold/type` | Der Abbruchschwellenwert für den Auftrag ist entweder ein `percentage` oder eine `number` von Geräten, |
| `cancellationThreshold/value` | also entweder der Prozentsatz der Geräte oder die Anzahl der Geräte, die den Abbruchschwellenwert definieren. |
| `data` | Ein Array von Vorgängen, die der Auftrag ausführt. |
| `data/type` | Einer der folgenden Werte: `PropertyJobData`, `CommandJobData` oder `CloudPropertyJobData` |
| `data/target` | Die Modell-ID der Zielgeräte. |
| `data/path` | Der Name der Eigenschaft, des Befehls oder der Cloudeigenschaft. |
| `data/value` | Der festzulegende Eigenschaftswert oder der zu sendende Befehlsparameter. |

## <a name="get-job-information"></a>Abrufen von Auftragsinformationen

Mit der folgenden Anforderung können Sie die Liste der Aufträge in Ihrer Anwendung abrufen:

```http
GET https://{your app subdomain}.azureiotcentral.com/api/jobs?api-version=preview
```

Die Antwort auf diese Anforderung sieht wie das folgende Beispiel aus:

```json
{
  "value": [
    {
      "id": "job-006",
      "displayName": "Set customer name",
      "description": "Set the customer name cloud property",
      "group": "4fcbec3b-5ee8-4324-855d-0f03b56bcf7f",
      "data": [
        {
          "type": "CloudPropertyJobData",
          "target": "dtmi:modelDefinition:bojo9tfju:yfvu5gv2vl",
          "path": "CustomerName",
          "value": "Contoso"
        }
      ],
      "status": "complete"
    },
    {
      "id": "job-005",
      "displayName": "Set target temperature",
      "description": "Set target temperature device property",
      "group": "833d7a7d-8f99-4e04-9e56-745806bdba6e",
      "data": [
        {
          "type": "PropertyJobData",
          "target": "dtmi:modelDefinition:zomtmdxh:eqod32zbyl",
          "path": "targetTemperature",
          "value": 56
        }
      ],
      "status": "complete"
    },
    {
      "id": "job-004",
      "displayName": "Run device report",
      "description": "Call command to run the device reports",
      "group": "833d7a7d-8f99-4e04-9e56-745806bdba6e",
      "batch": {
        "type": "percentage",
        "value": 25
      },
      "cancellationThreshold": {
        "type": "percentage",
        "value": 10,
        "batch": false
      },
      "data": [
        {
          "type": "CommandJobData",
          "target": "dtmi:modelDefinition:zomtmdxh:eqod32zbyl",
          "path": "getMaxMinReport",
          "value": "2021-06-15T05:00:00.000Z"
        }
      ],
      "status": "complete"
    }
  ]
}
```

Mit der folgenden Anforderung können Sie einen einzelnen Auftrag anhand der ID abrufen:

```http
GET https://{your app subdomain}.azureiotcentral.com/api/jobs/job-004?api-version=preview
```

Die Antwort auf diese Anforderung sieht wie das folgende Beispiel aus:

```json
{
  "id": "job-004",
  "displayName": "Run device report",
  "description": "Call command to run the device reports",
  "group": "833d7a7d-8f99-4e04-9e56-745806bdba6e",
  "batch": {
    "type": "percentage",
    "value": 25
  },
  "cancellationThreshold": {
    "type": "percentage",
    "value": 10,
    "batch": false
  },
  "data": [
    {
      "type": "CommandJobData",
      "target": "dtmi:modelDefinition:zomtmdxh:eqod32zbyl",
      "path": "getMaxMinReport",
      "value": "2021-06-15T05:00:00.000Z"
    }
  ],
  "status": "complete"
}
```

Mit der folgenden Anforderung können Sie die Details der Geräte in einem Auftrag abrufen:

```http
GET https://{your app subdomain}.azureiotcentral.com/api/jobs/job-004/devices?api-version=preview
```

Die Antwort auf diese Anforderung sieht wie das folgende Beispiel aus:

```json
{
  "value": [
    {
      "id": "therm-01",
      "status": "completed"
    },
    {
      "id": "therm-02",
      "status": "completed"
    },
    {
      "id": "therm-03",
      "status": "completed"
    },
    {
      "id": "therm-04",
      "status": "completed"
    }
  ]
}
```

## <a name="create-a-job"></a>Erstellen eines Auftrags

Mit der folgenden Anforderung können Sie die Details der Geräte in einem Auftrag abrufen:

```http
PUT https://{your app subdomain}.azureiotcentral.com/api/jobs/job-006?api-version=preview
{
  "displayName": "Set target temperature",
  "description": "Set target temperature device property",
  "group": "833d7a7d-8f99-4e04-9e56-745806bdba6e",
  "batch": {
    "type": "percentage",
    "value": 25
  },
  "cancellationThreshold": {
    "type": "percentage",
    "value": 10,
    "batch": false
  },
  "data": [
    {
      "type": "PropertyJobData",
      "target": "dtmi:modelDefinition:zomtmdxh:eqod32zbyl",
      "path": "targetTemperature",
      "value": "55"
    }
  ]
}
```

Die Antwort auf diese Anforderung sieht wie das folgende Beispiel aus. Der anfängliche Auftragsstatus lautet `pending`:

```json
{
  "id": "job-006",
  "displayName": "Set target temperature",
  "description": "Set target temperature device property",
  "group": "833d7a7d-8f99-4e04-9e56-745806bdba6e",
  "batch": {
    "type": "percentage",
    "value": 25
  },
  "cancellationThreshold": {
    "type": "percentage",
    "value": 10,
    "batch": false
  },
  "data": [
    {
      "type": "PropertyJobData",
      "target": "dtmi:modelDefinition:zomtmdxh:eqod32zbyl",
      "path": "targetTemperature",
      "value": "55"
    }
  ],
  "status": "pending"
}
```

## <a name="stop-resume-and-rerun-jobs"></a>Beenden, Fortsetzen und erneutes Ausführen von Aufträgen

Mit der folgenden Anforderung können Sie einen zurzeit ausgeführten Auftrag beenden:

```http
POST https://{your app subdomain}.azureiotcentral.com/api/jobs/job-006/stop?api-version=preview
```

Wenn die Anforderung erfolgreich ist, wird eine `204 - No Content`-Antwort zurückgegeben.

Mit der folgenden Anforderung können Sie einen beendeten Auftrag fortsetzen:

```http
POST https://{your app subdomain}.azureiotcentral.com/api/jobs/job-006/resume?api-version=preview
```

Wenn die Anforderung erfolgreich ist, wird eine `204 - No Content`-Antwort zurückgegeben.

Mit dem folgenden Befehl können Sie einen vorhandenen Auftrag auf fehlerhaften Geräten erneut ausführen:

```http
PUT https://{your app subdomain}.azureiotcentral.com/api/jobs/job-006/rerun/rerun-001?api-version=preview
```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie erfahren haben, wie Sie Aufträge mit der REST-API verwalten können, wird als nächster Schritt das [Verwalten von IoT Central-Anwendungen mit der REST-API](/learn/modules/manage-iot-central-apps-with-rest-api/) empfohlen.
