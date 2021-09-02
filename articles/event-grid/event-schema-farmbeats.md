---
title: Azure FarmBeats als Event Grid-Quelle (Vorschau)
description: Beschreibt die Eigenschaften und das Schema, die für Azure FarmBeats-Ereignisse in Azure Event Grid verfügbar sind
ms.topic: conceptual
ms.date: 06/06/2021
ms.openlocfilehash: 552a676fcfc457a662276aa7b5948670f700d208
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122345989"
---
# <a name="azure-farmbeats-as-event-grid-source-preview"></a>Azure FarmBeats als Event Grid-Quelle (Vorschau)
In diesem Artikel werden die Eigenschaften und das Schema für Azure FarmBeats-Ereignisse beschrieben.  Eine Einführung in Ereignisschemas finden Sie unter [Azure Event Grid-Ereignisschema](event-schema.md). 

## <a name="available-event-types"></a>Verfügbare Ereignistypen

|Veranstaltungsname | BESCHREIBUNG|
|-----|----|
|Microsoft.AgFoodPlatform.FarmerChanged|Wird veröffentlicht, wenn ein Farmer erstellt/aktualisiert/gelöscht wird. 
|Microsoft.AgFoodPlatform.FarmChanged| Wird veröffentlicht, wenn eine Farm erstellt/aktualisiert/gelöscht wird.
|Microsoft.AgFoodPlatform.BoundaryChanged|Wird veröffentlicht, wenn eine Grenze erstellt/aktualisiert/gelöscht wird.
|Microsoft.AgFoodPlatform.FieldChanged|Wird veröffentlicht, wenn ein Feld erstellt/aktualisiert/gelöscht wird.
|Microsoft.AgFoodPlatform.SeasonalFieldChanged|Wird veröffentlicht, wenn ein saisonales Feld erstellt/aktualisiert/gelöscht wird.
|Microsoft.AgFoodPlatform.SeasonChanged|Wird veröffentlicht, wenn eine Jahreszeit erstellt/aktualisiert/gelöscht wird.
|Microsoft.AgFoodPlatform.CropChanged|Wird veröffentlicht, wenn ein Erntegut erstellt/aktualisiert/gelöscht wird.
|Microsoft.AgFoodPlatform.CropVarietyChanged|Wird veröffentlicht, wenn eine Erntegutvarietät erstellt/aktualisiert/gelöscht wird.
|Microsoft.AgFoodPlatform.SatelliteDataIngestionJobStatusChanged| Wird veröffentlicht, wenn sich der Status eines Satelliten-Datenerfassungsauftrags ändert, z. B. wenn ein Auftrag erstellt wird, ausgeführt oder abgeschlossen wurde.
|Microsoft.AgFoodPlatform.WeatherDataIngestionJobStatusChanged|Wird veröffentlicht, wenn sich der Status eines Wetter-Datenerfassungsauftrags ändert, z. B. wenn ein Auftrag erstellt wird, ausgeführt oder abgeschlossen wurde.
|Microsoft.AgFoodPlatform.FarmOperationDataIngestionJobStatusChanged| Wird veröffentlicht, wenn sich der Status eines Farmbetriebs-Datenerfassungsauftrags ändert, z. B. wenn ein Auftrag erstellt wird, ausgeführt oder abgeschlossen wurde.
|Microsoft.AgFoodPlatform.ApplicationDataChanged|Wird veröffentlicht, wenn Anwendungsdaten erstellt /aktualisiert/gelöscht werden. Dieses Ereignis wird Farmbetriebsdaten zugeordnet.
|Microsoft.AgFoodPlatform.HarvestingDataChanged|Wird veröffentlicht, wenn Erntedaten erstellt/aktualisiert/gelöscht werden. Dieses Ereignis ist Farmbetriebsdaten zugeordnet.
|Microsoft.AgFoodPlatform.TillageDataChanged|Wird veröffentlicht, wenn Ackerbaudaten erstellt, aktualisiert oder gelöscht werden. Dieses Ereignis wird Farmbetriebsdaten zugeordnet.
|Microsoft.AgFoodPlatform.PlantingDataChanged|Wird veröffentlicht, wenn Pflanzdaten erstellt/aktualisiert/gelöscht werden. Dieses Ereignis ist Farmbetriebsdaten zugeordnet.

## <a name="event-properties"></a>Ereigniseigenschaften
Jedes FarmBeats-Ereignis besteht aus zwei Teilen: einem ereignisübergreifenden Teil und einem anderen Teil (einem Datenobjekt), das für jedes Ereignis spezifische Eigenschaften enthält. 

Der ereignisübergreifende Teil wird im folgenden Schema erläutert.

### <a name="event-grid-event-schema"></a>Event Grid-Ereignisschema
Ein Ereignis weist die folgenden Daten auf oberster Ebene aus:

| Eigenschaft | type | BESCHREIBUNG |
| -------- | ---- | ----------- |
| `topic` | Zeichenfolge | Vollständiger Ressourcenpfaf zur Ereignisquelle. Dieses Feld ist nicht beschreibbar. Dieser Wert wird von Event Grid bereitgestellt. |
| `subject` | Zeichenfolge | Vom Herausgeber definierter Pfad zum Ereignisbetreff |
| `eventType` | Zeichenfolge | Einer der registrierten Ereignistypen für die Ereignisquelle. |
| `eventTime` | Zeichenfolge | Die Zeit, in der das Ereignis generiert wird, basierend auf der UTC-Zeit des Anbieters. |
| `id` | Zeichenfolge | Eindeutiger Bezeichner für das Ereignis. |
| `data` | Objekt (object) | App Configuration-Ereignisdaten. |
| `dataVersion` | Zeichenfolge | Die Schemaversion des Datenobjekts. Der Herausgeber definiert die Schemaversion. |
| `metadataVersion` | Zeichenfolge | Die Schemaversion der Ereignismetadaten. Event Grid definiert das Schema der Eigenschaften der obersten Ebene. Dieser Wert wird von Event Grid bereitgestellt. |


In den folgenden Tabellen werden die Eigenschaften innerhalb des Datenobjekts für jedes Ereignis erläutert.

*Für die FarmBeats-Ereignisse CropChanged, FarmChanged, SeasonChanged, CropChanged, CropVarietyChanged enthält das Datenobjekt die folgenden Eigenschaften:*

|Eigenschaft | type| BESCHREIBUNG|
|----| ----| ----|
id| Zeichenfolge| Benutzerdefinierte ID der Ressource, z. B. Farm-ID, Farmer-ID usw.
actionType| Zeichenfolge| Gibt die Änderung an, die während der Veröffentlichung des Ereignisses ausgelöst wurde. Gültige Werte sind „Created“ (Erstellt), „Updated“ (Aktualisiert) und „Deleted“ (Gelöscht).
status| Zeichenfolge| Enthält den benutzerdefinierten Status der Ressource.
properties| Objekt (object)| Enthält benutzerdefinierte Schlüssel-Wert-Paare
modifiedDateTime| date-time|Datum und Uhrzeit der letzten Änderung der Ressource, Beispielformat: jjjj-MM-ttTHH:mm:ssZ.
createdDateTime|date-time|Datum und Uhrzeit der Erstellung der Ressource, Beispielformat: jjjj-MM-ttTHH:mm:ssZ.
eTag|   Zeichenfolge| Implementiert optimistische Parallelität
description| Zeichenfolge|    Textbeschreibung der Ressource.


*FarmBeats-Ereignisse vom Typ BoundaryChanged weisen das folgende Datenobjekt auf:*

|Eigenschaft | type| BESCHREIBUNG|
|----| ----| ----|
id| Zeichenfolge| Benutzerdefinierte ID der Grenze
actionType| Zeichenfolge| Gibt die Änderung an, die während der Veröffentlichung des Ereignisses ausgelöst wird. Gültige Werte sind „Created“ (Erstellt), „Updated“ (Aktualisiert) und „Deleted“ (Gelöscht).
parentId|   Zeichenfolge| Die ID, die zur übergeordneten Grenze gehört.
parentType| Zeichenfolge| Der Typ, der zur übergeordneten Grenze gehört.
IsPrimary|  boolean|    Gibt an, ob die Grenze primär ist.
farmerId|   Zeichenfolge| Enthält die ID des der Grenze zugeordneten Farmers.
properties| Objekt (object)| Enthält benutzerdefinierte Schlüssel-Wert-Paare.
modifiedDateTime| date-time|Datum und Uhrzeit der letzten Änderung der Ressource, Beispielformat: jjjj-MM-ttTHH:mm:ssZ.
createdDateTime|date-time|Datum und Uhrzeit der Erstellung der Ressource, Beispielformat: jjjj-MM-ttTHH:mm:ssZ.
status| Zeichenfolge| Enthält den benutzerdefinierten Status der Ressource.
eTag|   Zeichenfolge| Implementiert optimistische Parallelität
description| Zeichenfolge|    Textbeschreibung der Ressource.

*FarmBeats-Ereignisse vom Typ FieldChanged weisen das folgende Datenobjekt auf:*

Eigenschaft|   type|   BESCHREIBUNG
|----| ----| ----|
id| Zeichenfolge| Benutzerdefinierte ID des Felds
farmId| Zeichenfolge| Benutzerdefinierte ID der Farm, der das Feld zugeordnet ist
farmerId|   Zeichenfolge| Benutzerdefinierte ID des Farmers, der dem Feld zugeordnet ist
name|   Zeichenfolge| Benutzerdefinierter Name des Felds
actionType| Zeichenfolge| Gibt die Änderung an, das die Veröffentlichung des Ereignisses ausgelöst hat. Gültige Werte sind „Created“ (Erstellt), „Updated“ (Aktualisiert) und „Deleted“ (Gelöscht).
properties| Objekt (object)| Enthält benutzerdefinierte Schlüssel-Wert-Paare
modifiedDateTime| date-time|Datum und Uhrzeit der letzten Änderung der Ressource, Beispielformat: jjjj-MM-ttTHH:mm:ssZ.
createdDateTime|date-time|Datum und Uhrzeit der Erstellung der Ressource, Beispielformat: jjjj-MM-ttTHH:mm:ssZ.
status| Zeichenfolge| Enthält den benutzerdefinierten Status der Ressource.
eTag|   Zeichenfolge| Implementiert optimistische Parallelität
description|Zeichenfolge| Textbeschreibung der Ressource.

*FarmBeats-Ereignisse vom Typ SeasonalFieldChanged weisen das folgende Datenobjekt auf:*

Eigenschaft|   type|   BESCHREIBUNG
|----| ----| ----|
id| Zeichenfolge| Benutzerdefinierte ID des saisonalen Felds
farmId| Zeichenfolge| Benutzerdefinierte ID der Farm, der das saisonale Feld zugeordnet ist
farmerId|   Zeichenfolge| Benutzerdefinierte ID des Farmers, dem das saisonale Feld zugeordnet ist
saisonId|   Zeichenfolge| Benutzerdefinierte ID der Jahreszeit, der das saisonale Feld zugeordnet ist
fieldId|    Zeichenfolge| Benutzerdefinierte ID des Felds, dem das saisonale Feld zugeordnet ist
name|   Zeichenfolge| Benutzerdefinierter Name des saisonalen Felds
actionType| Zeichenfolge| Gibt die Änderung an, das die Veröffentlichung des Ereignisses ausgelöst hat. Gültige Werte sind „Created“ (Erstellt), „Updated“ (Aktualisiert) und „Deleted“ (Gelöscht).
properties| Objekt (object)| Enthält benutzerdefinierte Schlüssel-Wert-Paare
modifiedDateTime| date-time|Datum und Uhrzeit der letzten Änderung der Ressource, Beispielformat: jjjj-MM-ttTHH:mm:ssZ.
createdDateTime|date-time|Datum und Uhrzeit der Erstellung der Ressource, Beispielformat: jjjj-MM-ttTHH:mm:ssZ.
status| Zeichenfolge| Enthält den benutzerdefinierten Status der Ressource.
eTag|   Zeichenfolge| Implementiert optimistische Parallelität
description| Zeichenfolge|    Textbeschreibung der Ressource.

*Die FarmBeats-Ereignisse SatelliteDataIngestionJobChanged, WeatherDataIngestionJobChanged und FarmOperationsDataIngestionJobChanged weisen das folgende Datenobjekt auf:*

Eigenschaft|   type|   BESCHREIBUNG
|----|----|----|
id|String| Eindeutige ID des Auftrags.
name| Zeichenfolge| Benutzerdefinierter Name des Auftrags.
status|Zeichenfolge|Verschiedene Zustände, in denen sich ein Auftrag befinden kann.
isCancellationRequested| boolean|Flag, das festgelegt wird, wenn ein Auftragsabbruch angefordert wird.
description|Zeichenfolge| Textbeschreibung des Auftrags.
farmerId|Zeichenfolge| ID des Farmers, für den der Auftrag erstellt wurde.
message|Zeichenfolge| Statusmeldung, um weitere Details des Auftrags zu erfassen.
lastActionDateTime|date-time|Datum und Uhrzeit der letzten Aktion für den Auftrag, Beispielformat: jjjj-MM-ttTHH:mm:ssZ.
createdDateTime|date-time|Datum und Uhrzeit der Erstellung der Ressource, Beispielformat: jjjj-MM-ttTHH:mm:ssZ.


*FarmBeats-Datenänderungsereignisse für Farmbetrieb wie ApplicationDataChanged, HarvestingDataChanged, PlantingDataChanged und TillageDataChanged verfügen über das folgende Datenobjekt:*

Eigenschaft|   type|   BESCHREIBUNG
|----|----|----|
id| Zeichenfolge| Benutzerdefinierte ID der Ressource, z. B. Farm-ID, Farmer-ID usw.
status| Zeichenfolge| Enthält den Status des Auftrags. 
actionType|Zeichenfolge|
source| Zeichenfolge| Nachricht von FarmBeats mit Details zum Auftrag.    
modifiedDateTime| date-time|Datum und Uhrzeit der letzten Änderung der Ressource, Beispielformat: jjjj-MM-ttTHH:mm:ssZ.
createdDateTime|date-time|Datum und Uhrzeit der Erstellung der Ressource, Beispielformat: jjjj-MM-ttTHH:mm:ssZ.
eTag|   Zeichenfolge| Implementiert optimistische Parallelität
description|Zeichenfolge| Textbeschreibung der Ressource.


## <a name="sample-events"></a>Samplingereignisse 
Diese Ereignisbeispiele stellen eine Ereignisbenachrichtigung dar.

**Ereignistyp: Microsoft.AgFoodPlatform.FarmerChanged**

```json
{
    "data": {
      "actionType": "Created",
      "status": "Sample status",
      "modifiedDateTime": "2021-03-05T10:53:28Z",
      "eTag": "860197cc-0000-0700-0000-60420da80000",
      "id": "UNIQUE-FARMER-ID",
      "name": "sample farmer",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T10:53:28Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "81fbe1de-4ae4-4284-964f-59da80a6bfe7",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID",
    "eventType": "Microsoft.AgFoodPlatform.FarmerChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T10:53:28.2783745Z"
  }
```

**Ereignistyp: Microsoft.AgFoodPlatform.FarmChanged**

```json
  {
    "data": {
      "farmerId": "UNIQUE-FARMER-ID",
      "actionType": "Created",
      "status": "Sample status",
      "modifiedDateTime": "2021-03-05T10:55:57Z",
      "eTag": "8601e3d5-0000-0700-0000-60420e3d0000",
      "id": "UNIQUE-FARM-ID",
      "name": "Display name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T10:55:57Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "31a31be7-51fb-48f3-adfd-6fb4400be002",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/farms/UNIQUE-FARM-ID",
    "eventType": "Microsoft.AgFoodPlatform.FarmChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T10:55:57.6026173Z"
  }
```
**Ereignistyp: Microsoft.AgFoodPlatform.BoundaryChanged**

```json
  {
    "data": {
      "farmerId": "UNIQUE-FARMER-ID",
      "parentId": "OPTIONAL-UNIQUE-FIELD-ID",
      "isPrimary": true,
      "actionType": "Created",
      "modifiedDateTime": "2021-03-05T11:15:29Z",
      "eTag": "860109f7-0000-0700-0000-604212d10000",
      "id": "UNIQUE-BOUNDARY-ID",
      "name": "Display name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:15:29Z"
    },
    "id": "3d3453b2-5a94-45a7-98eb-fc2979a00317",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/boundaries/UNIQUE-BOUNDARY-ID",
    "eventType": "Microsoft.AgFoodPlatform.BoundaryChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:15:29.4797354Z"
  }
  ```

**Ereignistyp: Microsoft.AgFoodPlatform.FieldChanged**

```json
  {
    "data": {
      "farmerId": "UNIQUE-FARMER-ID",
      "farmId": "UNIQUE-FARM-ID",
      "actionType": "Created",
      "status": "Sample status",
      "modifiedDateTime": "2021-03-05T10:58:43Z",
      "eTag": "860124dc-0000-0700-0000-60420ee30000",
      "id": "UNIQUE-FIELD-ID",
      "name": "Display name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T10:58:43Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "1ad04ed0-ac05-4c4e-aa3d-87facb3cc97c",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/fields/UNIQUE-FIELD-ID",
    "eventType": "Microsoft.AgFoodPlatform.FieldChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T10:58:43.3222921Z"
  }
  ```
**Ereignistyp: Microsoft.AgFoodPlatform.SeasonalFieldChanged**
```json
  {
    "data": {
      "farmerId": "UNIQUE-FARMER-ID",
      "seasonId": "UNIQUE-SEASON-ID",
      "fieldId": "UNIQUE-FIELD-ID",
      "farmId": "UNIQUE-FARM-ID",
      "actionType": "Created",
      "status": "Sample status",
      "modifiedDateTime": "2021-03-05T11:24:56Z",
      "eTag": "8701300b-0000-0700-0000-604215080000",
      "id": "UNIQUE-SEASONAL-FIELD-ID",
      "name": "Display name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:24:56Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "ff59a0a3-6226-42c0-9e70-01da55efa797",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/seasonalFields/UNIQUE-SEASONAL-FIELD-ID",
    "eventType": "Microsoft.AgFoodPlatform.SeasonalFieldChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:24:56.4210287Z"
  }
```
**Ereignistyp: Microsoft.AgFoodPlatform.SeasonChanged**
```json
  {
    "data": {
      "actionType": "Created",
      "status": "Sample status",
      "modifiedDateTime": "2021-03-05T11:18:38Z",
      "eTag": "86019afd-0000-0700-0000-6042138e0000",
      "id": "UNIQUE-SEASON-ID",
      "name": "Display name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:18:38Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "63989475-397b-4b92-8160-8743bf8e5804",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/seasons/UNIQUE-SEASON-ID",
    "eventType": "Microsoft.AgFoodPlatform.SeasonChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:18:38.5804699Z"
  }
  ```

  **Ereignistyp: Microsoft.AgFoodPlatform.CropChanged**

```json
  {
    "data": {
      "actionType": "Created",
      "status": "Sample status",
      "modifiedDateTime": "2021-03-05T11:03:48Z",
      "eTag": "8601c4e5-0000-0700-0000-604210150000",
      "id": "UNIQUE-CROP-ID",
      "name": "Display name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:03:48Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "4c59a797-b76d-48ec-8915-ceff58628f35",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/crops/UNIQUE-CROP-ID",
    "eventType": "Microsoft.AgFoodPlatform.CropChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:03:49.0590658Z"
  }
  ```

**Ereignistyp: Microsoft.AgFoodPlatform.CropVarietyChanged**

```json
  {
    "data": {
      "cropId": "UNIQUE-CROP-ID",
      "actionType": "Created",
      "status": "string",
      "modifiedDateTime": "2021-03-05T11:10:21Z",
      "eTag": "860130ef-0000-0700-0000-6042119d0000",
      "id": "UNIQUE-CROP-VARIETY-ID",
      "name": "Sample status",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:10:21Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "29aefdb9-d648-442c-81f8-694f3f47583c",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/cropVarieties/UNIQUE-CROP-VARIETY-ID",
    "eventType": "Microsoft.AgFoodPlatform.CropVarietyChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:10:21.4572495Z"
  }
```
**Ereignistyp: Microsoft.AgFoodPlatform.SatelliteDataIngestionJobStatusChanged**
```json
[
  {
    "data": {
      "farmerId": "UNIQUE - FARMER - ID",
      "message": "Created job 'job1' to fetch satellite data for boundary 'boundary1' from startDate '06/01/2021' to endDate '06/01/2021' (both inclusive).",
      "status": "Waiting",
      "lastActionDateTime": "2021-06-01T11:25:37.8634096Z",
      "isCancellationRequested": false,
      "id": "UNIQUE - JOB - ID",
      "name": "samplejob",
      "description": "Sample for testing events",
      "createdDateTime": "2021-06-01T11:25:32.3421173Z",
      "properties": {
        "key1": "testvalue1",
        "key2": 123.45
      }
    },
    "id": "925c6be2-6561-4572-b7dd-0f3084a54567",
    "topic": "/subscriptions/{Subscription -ID}/resourceGroups/{RESOURCE - GROUP - NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/{UNIQUE-FARMER-ID}/satelliteDataIngestionJobs/{UNIQUE-JOB-ID}",
    "eventType": "Microsoft.AgFoodPlatform.SatelliteDataIngestionJobStatusChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-06-01T11:25:37.8634764Z"
  }
]
```
**Ereignistyp: Microsoft.AgFoodPlatform.WeatherDataIngestionJobStatusChanged**
```json
[
  {
    "data": {
      "farmerId": "UNIQUE-FARMER-ID",
      "message": "Created job to fetch weather data for job name 'job2', farmer id 'farmer2' and boundary id 'boundary2'.",
      "status": "Running",
      "lastActionDateTime": "2021-06-01T11:22:27.9031003Z",
      "isCancellationRequested": false,
      "id": "UNIQUE-JOB-ID",
      "createdDateTime": "2021-06-01T07:13:54.8843617Z"
    },
    "id": "ec30313a-ff2f-4b50-882b-31188113c15b",
    "topic": "/subscriptions/{Subscription -ID}/resourceGroups/{RESOURCE - GROUP - NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/weatherDataIngestionJobs/UNIQUE-JOB-ID",
    "eventType": "Microsoft.AgFoodPlatform.WeatherDataIngestionJobStatusChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-06-01T11:22:27.9031302Z"
  }
]

```
**Ereignistyp: Microsoft.AgFoodPlatform.FarmOperationDataIngestionJobStatusChanged**
```json
[
  {
    "data": {
      "farmerId": "UNIQUE-FARMER-ID",
      "message": "Job completed successfully. Data statistics:{ Processed operations count = 6, Organizations count = 1, Processed organizations count = 1, Processed fields count = 2, Operations count = 6, ShapefileAttachmentsCount = 0, Fields count = 2 }",
      "status": "Succeeded",
      "lastActionDateTime": "2021-06-01T11:30:54.733625Z",
      "isCancellationRequested": false,
      "id": "UNIQUE-JOB-ID",
      "name": "sample-job",
      "description": "sample description",
      "createdDateTime": "2021-06-01T11:30:39.0905288Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "ebdbb7a1-ad28-4af7-b3a2-a4a3a2dd1b4f",
    "topic": "/subscriptions/{Subscription -ID}/resourceGroups/{RESOURCE - GROUP - NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/farmOperationDataIngestionJobs/UNIQUE-JOB-ID",
    "eventType": "Microsoft.AgFoodPlatform.FarmOperationDataIngestionJobStatusChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-06-01T11:30:54.733671Z"
  }
]

```
**Ereignistyp: Microsoft.AgFoodPlatform.ApplicationDataChanged**

```json
  {
    "data": {
      "actionType": "Updated",
      "farmerId": "UNIQUE-FARMER-ID",
      "source": "Sample source",
      "modifiedDateTime": "2021-03-05T11:27:24Z",
      "eTag": "87011311-0000-0700-0000-6042159c0000",
      "id": "UNIQUE-APPLICATION-DATA-ID",
      "status": "Sample status",
      "name": "sample name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:27:24Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "e499f6c4-63ba-4217-8261-0c6cb0e398d2",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/applicationData/UNIQUE-APPLICATION-DATA-ID",
    "eventType": "Microsoft.AgFoodPlatform.ApplicationDataChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:27:24.164612Z"
  }
```

**Ereignistyp: Microsoft.AgFoodPlatform.HarvestDataChanged**
```json
  {
    "data": {
      "actionType": "Created",
      "farmerId": "UNIQUE-FARMER-ID",
      "source": "Sample source",
      "modifiedDateTime": "2021-03-05T11:33:41Z",
      "eTag": "8701141b-0000-0700-0000-604217150000",
      "id": "UNIQUE-HARVEST-DATA-ID",
      "status": "Sample status",
      "name": "sample name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:33:41Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "dc3837c0-1eed-4bfa-88b6-d018cf6af4db",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/harvestData/UNIQUE-HARVEST-DATA-ID",
    "eventType": "Microsoft.AgFoodPlatform.HarvestDataChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:33:41.3434992Z"
  }
```
**Ereignistyp: Microsoft.AgFoodPlatform.TillageDataChanged**
```json
  {
    "data": {
      "actionType": "Updated",
      "farmerId": "UNIQUE-FARMER-ID",
      "source": "sample source",
      "modifiedDateTime": "2021-06-15T10:31:07Z",
      "eTag": "6405f027-0000-0100-0000-60c8816b0000",
      "id": "c9858c3f-fb94-474a-a6de-103b453df976",
      "createdDateTime": "2021-06-15T10:31:07Z",
      "name": "sample name",
      "description":"sample description"
      "properties": {
        "_orgId": "498221",
        "_fieldId": "e61b83f4-3a12-431e-8010-596f2466dc27",
        "_cropSeason": "2010"
      }
    },
    "id": "f06f6686-1fa8-41fd-be99-46f40f495cce",
    "topic": "/subscriptions/da9091ec-d18f-456c-9c21-5783ee7f4645/resourceGroups/internal-farmbeats-resources/providers/Microsoft.AgFoodPlatform/farmBeats/internal-eus",
    "subject": "/farmers/10e3d7bf-c559-48be-af31-4e00df83bfcd/tillageData/c9858c3f-fb94-474a-a6de-103b453df976",
    "eventType": "Microsoft.AgFoodPlatform.TillageDataChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-06-15T10:31:07.6778047Z"
  }
```

**Ereignistyp: Microsoft.AgFoodPlatform.PlantingDataChanged**
```json
  {
    "data": {
      "actionType": "Created",
      "farmerId": "UNIQUE-FARMER-ID",
      "source": "Sample source",
      "modifiedDateTime": "2021-03-05T11:41:18Z",
      "eTag": "8701242a-0000-0700-0000-604218de0000",
      "id": "UNIQUE-PLANTING-DATA-ID",
      "status": "Sample status",
      "name": "sample name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:41:18Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "42589c7f-4e16-4a4d-9314-d611c822f7ac",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/plantingData/UNIQUE-PLANTING-DATA-ID",
    "eventType": "Microsoft.AgFoodPlatform.PlantingDataChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:41:18.1744322Z"
  }
```



## <a name="next-steps"></a>Nächste Schritte
* Eine Einführung zu Azure Event Grid finden Sie unter [Einführung in Azure Event Grid](overview.md).
