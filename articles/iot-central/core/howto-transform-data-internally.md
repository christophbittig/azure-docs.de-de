---
title: Transformieren von Daten in Azure IoT Central | Microsoft-Dokumentation
description: IoT-Geräte senden Daten in verschiedenen Formaten, die Sie ggf. transformieren müssen. In diesem Artikel wird beschrieben, wie Sie Daten in einer IoT Central-Anwendung transformieren, bevor Sie sie exportieren.
author: dominicbetts
ms.author: dobett
ms.date: 10/28/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: c886fd71399f84f04e7ac83ff24059dd658f0a4b
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131579403"
---
# <a name="transform-data-inside-your-iot-central-application-for-export"></a>Transformieren von Daten in Ihrer IoT Central-Anwendung für den Export

:::image type="content" source="media/howto-transform-data-internally/transformations.png" alt-text="Diagramm der Transformationsoptionen für den Datenexport." border="false":::

IoT-Geräte senden Daten in verschiedenen Formaten. Um die Gerätedaten in Ihrer IoT-Lösung verwenden zu können, müssen Sie sie möglicherweise transformieren, bevor sie in andere Dienste exportiert werden.

In diesem Artikel erfahren Sie, wie Sie Gerätedaten als Teil einer Datenexportdefinition in einer IoT Central-Anwendung transformieren.

Transformationen in einer IoT Central-Datenexportdefinition ermöglichen es Ihnen, das Format und die Struktur der Gerätedaten zu bearbeiten, bevor sie in ein Ziel exportiert werden. Sie können eine Transformation für jedes Ziel in einer Exportdefinition angeben. Jede Nachricht durchläuft die Transformation, um einen Ausgabedatensatz zu erstellen, der in das Ziel exportiert wird.

Mithilfe von Transformationen können Sie JSON-Nutzdaten umstrukturieren, Felder umbenennen, Felder herausfiltern und einfache Berechnungen für Telemetriewerte ausführen. Verwenden Sie eine Transformation beispielsweise, um Ihre Nachrichten in ein Tabellenformat zu konvertieren, das dem Schema eines Ziels entspricht, wie z. B. eine Azure Data Explorer-Tabelle.

## <a name="add-a-transformation"></a>Hinzufügen einer Transformation

Um eine Transformation für ein Ziel in Ihrem Datenexport hinzuzufügen, wählen Sie **+ Transformieren** aus, wie im folgenden Screenshot gezeigt wird:

:::image type="content" source="media/howto-transform-data-internally/add-transformation.png" alt-text="Screenshot: Hinzufügen einer Transformation zu einem Ziel.":::

Im Bereich **Datentransformation** können Sie die Transformation angeben. Im Abschnitt **1. Add your input message** (Ihre Eingabenachricht hinzufügen) können Sie eine Beispielnachricht eingeben, die Sie durch die Transformation übergeben möchten. Sie können eine Beispielnachricht auch generieren, indem Sie eine Gerätevorlage auswählen. Im Abschnitt **2. Build transformation query** (Transformationsabfrage erstellen) können Sie die Abfrage eingeben, die die Eingabenachricht transformiert. Im Abschnitt **3. Preview output messages(s)** (Vorschau der Ausgabenachricht(en) anzeigen) wird das Ergebnis der Transformation gezeigt:

:::image type="content" source="media/howto-transform-data-internally/transformation-editor.png" alt-text="Screenshot des Transformations-Editors in IoT Central.":::

> [!TIP]
> Wenn Sie das Format Ihrer Eingabenachricht nicht kennen, verwenden Sie `.` als Abfrage, um die Nachricht unverändert in ein Ziel, z. B. einen Webhook, zu exportieren. Fügen Sie dann die vom Webhook empfangene Nachricht in ***1. Add your input message** ein. Erstellen Sie anschließend eine Transformationsabfrage, um diese Nachricht in Ihr erforderliches Ausgabeformat zu transformieren.

## <a name="build-a-transformation-query"></a>Erstellen einer Transformationsabfrage

Die Transformations-Engine verwendet den Open-Source-JSON-Prozessor [JQ](https://stedolan.github.io/jq/) zum Umstrukturieren und Formatieren von JSON-Nutzdaten. Zur Angabe einer Transformation schreiben Sie eine JQ-Abfrage, die die integrierten Filter, Funktionen und Features von JQ verwenden kann. Einige Abfragebeispiele finden Sie unter [Beispiele für Transformationsabfragen](#example-transformation-queries). Weitere Informationen zum Schreiben von JQ-Abfragen finden Sie im [JQ-Handbuch](https://stedolan.github.io/jq/manual/).

## <a name="pre-transformation-message-structure"></a>Nachrichtenstruktur vor der Transformation

Sie können die folgenden Datenströme aus IoT Central exportieren: Telemetriedaten, Eigenschaftsänderungen, Ereignisse der Gerätekonnektivität, Ereignisse im Gerätelebenszyklus und Ereignisse im Lebenszyklus von Gerätevorlagen. Jeder Datentyp hat eine bestimmte Struktur, die Informationen wie Telemetriewerte, Anwendungsinformationen, Gerätemetadaten und Eigenschaftswerte enthält.

Das folgende Beispiel zeigt die Form einer Telemetrienachricht. Alle diese Daten sind für Ihre Transformation verfügbar. Die Struktur der Nachricht ist bei anderen Nachrichtentypen ähnlich, aber es gibt einige typspezifische Felder. Sie können mithilfe des Features **Add your input message** eine Beispielnachricht generieren, die auf einer Gerätevorlage in Ihrer Anwendung basiert.

```json
{
  "applicationId": "93d68c98-9a22-4b28-94d1-06625d4c3d0f",
  "device": {
    "id": "31edabe6-e0b9-4c83-b0df-d12e95745b9f",
    "name": "Scripted Device - 31edabe6-e0b9-4c83-b0df-d12e95745b9f",
    "cloudProperties": [],
    "properties": {
      "reported": [
        {
          "id": "urn:smartKneeBrace:Smart_Vitals_Patch_wr:FirmwareVersion:1",
          "name": "FirmwareVersion",
          "value": 1.0
        }
      ]
    },
    "templateId": "urn:sbq3croo:modelDefinition:nf7st1wn3",
    "templateName": "Smart Knee Brace"
  },
  "telemetry": [
      {
        "id": "urn:continuousPatientMonitoringTemplate:Smart_Knee_Brace_6wm:Acceleration:1",
        "name": "Acceleration",
        "value": {
          "x": 19.212770659918583,
          "y": 20.596296675217335,
          "z": 54.04859440697045
        }
      },
      {
        "id": "urn:continuousPatientMonitoringTemplate:Smart_Knee_Brace_6wm:RangeOfMotion:1",
        "name": "RangeOfMotion",
        "value": 110
      }
  ],
  "enqueuedTime": "2021-03-23T19:55:56.971Z",
  "enrichments": {
      "your-enrichment-key": "enrichment-value"
  },
  "messageProperties": {
      "prop1": "prop-value"
  },
  "messageSource": "telemetry"
}
```

> [!TIP]
> Verwenden Sie das Feature **Add your input message** in der Benutzeroberfläche der IoT Central-Anwendung, um Beispielnachrichtenstrukturen für andere Datenexporttypen, z. B. Eigenschaftsänderungen, anzuzeigen.

## <a name="example-transformation-queries"></a>Beispiele für Transformationsabfragen

In den folgenden Abfragebeispielen wird die im vorhergehenden Abschnitt gezeigte Telemetrienachricht verwendet.

**Beispiel 1**: Die folgende JQ-Abfrage gibt jede Telemetrie aus der Eingabenachricht als separate Ausgabenachricht aus:

```jq
.telemetry[]
```

JSON-Ausgabe:

```json
{
  "id": "urn:continuousPatientMonitoringTemplate:Smart_Knee_Brace_6wm:Acceleration:1",
  "name": "Acceleration",
  "value": {
    "x": 19.212770659918583,
    "y": 20.596296675217335,
    "z": 54.04859440697045
  }
},
{
  "id": "urn:continuousPatientMonitoringTemplate:Smart_Knee_Brace_6wm:RangeOfMotion:1",
  "name": "RangeOfMotion",
  "value": 110
}
```

> [!TIP]
> Mithilfe der Abfrage `.telemetry` können Sie die Ausgabe in eine einzelne Nachricht mit einem Array von Telemetrietypen ändern.

**Beispiel 2**: Die folgende JQ-Abfrage konvertiert das Eingabetelemetrie-Array in ein Objekt mit Telemetrienamen als Schlüsseln:

```jq
.telemetry | map({ key: .name, value: .value }) | from_entries
```

JSON-Ausgabe:

```json
{
  "Acceleration": {
    "x": 19.212770659918583,
    "y": 20.596296675217335,
    "z": 54.04859440697045
  },
  "RangeOfMotion": 110
}
```

**Beispiel 3**: Die folgende JQ-Abfrage sucht den Telemetriewert **RangeOfMotion** und konvertiert ihn mithilfe der Formel `rad = degree * pi / 180` von Grad in Bogenmaß. Diese Abfrage zeigt auch, wie Sie das Modul `iotc`importieren und verwenden können:

```jq
import "iotc" as iotc;
{
  rangeOfMotion: (
    .telemetry
    | iotc::find(.name == "RangeOfMotion").value
    | . * 3.14159265358979323846 / 180
  )
}
```

JSON-Ausgabe:

```json
{
  "rangeOfMotion": 1.9198621771937625
}
```

**Beispiel 4**: Um die Eingabenachricht in ein Tabellenformat zu ändern, können Sie jede exportierte Nachricht in einer oder mehreren *Zeilen* zuordnen. Die Zeilenausgabe wird logisch als JSON-Objekt dargestellt, wobei der Spaltenname der Schlüssel und der Spaltenwert der Wert ist:

```json
{
    "<column 1 name>": "<column 1 value>",
    "<column 2 name>": "<column 2 value>",
    ...
}
```

> [!TIP]
> Verwenden Sie beim Exportieren in Azure Data Explorer ein Tabellenformat.

Die folgende JQ-Abfrage schreibt Zeilen in eine Tabelle, in der die **rangeOfMotion**-Telemetriedaten auf verschiedenen Geräten gespeichert werden. Die Abfrage ordnet die Geräte-ID, den Zeitpunkt der Einreihung in die Warteschlange und den Bewegungsbereich in einer Tabelle mit den folgenden Spalten zu:

```jq
import "iotc" as iotc;
{
    deviceId: .deviceId,
    timestamp: .enqueuedTime,
    rangeOfMotion: .telemetry | iotc::find(.name == "RangeOfMotion").value
}
```

Ausgabe im JSON-Format:

```json
{
  "deviceId": "31edabe6-e0b9-4c83-b0df-d12e95745b9f",
  "timestamp": "2021-03-23T19:55:56.971Z",
  "rangeOfMotion": 110
}
```

## <a name="iot-central-module"></a>IoT Central-Modul

Ein JQ-Modul ist eine Sammlung von benutzerdefinierten Funktionen. Als Teil Ihrer Transformationsabfrage können Sie ein integriertes IoT Central-spezifisches Modul importieren, das Funktionen enthält, damit Sie Ihre Abfragen einfacher schreiben können. Importieren Sie das IoT Central-Modul mithilfe der folgenden Anweisung:

```jq
import "iotc" as iotc;
```

Das IoT Central-Modul enthält die folgenden Funktionen:

`find(expression)`: Mit der Funktion `find` können Sie ein bestimmtes Arrayelement finden, z. B. einen Telemetriewert oder einen Eigenschaftseintrag in Ihren Nutzdaten. Die Funktionseingabe ist ein Array, und der Parameter definiert einen JQ-Filter, der für jedes Element im Array ausgeführt werden soll. Die Funktion gibt jedes Arrayelement zurück, bei dem der Filter als „true“ ausgewertet wird:

So finden Sie beispielsweise einen bestimmten Telemetriewert namens `RangeOfMotion`:

```jq
.telemetry | iotc::find(.name == "RangeOfMotion")
```

## <a name="scenarios"></a>Szenarien

In den folgenden Szenarien wird die Transformationsfunktion verwendet, um das Gerätedatenformat für ein bestimmtes Ziel anzupassen.

### <a name="scenario-1-export-device-data-to-azure-data-explorer"></a>Szenario 1: Exportieren von Gerätedaten in Azure Data Explorer

In diesem Szenario transformieren Sie Gerätedaten so, dass sie dem festen Schema in Azure Data Explorer entsprechen. Dabei wird jeder Telemetriewert als eine Spalte in der Tabelle angezeigt, und jede Zeile stellt eine einzelne Nachricht dar. Zum Beispiel:

| deviceId | Timestamp | T1 | T2 |T3 |
| :------------- | :---------- | :----------- | :---------- | :----------- |
| „31edabe6-e0b9-4c83-b0df-d12e95745b9f“ |„2021-03-23T19:55:56.971Z | 1,18898 | 1,434709 | 2,97008 |

Zum Exportieren von Daten, die mit dieser Tabelle kompatibel sind, muss jede exportierte Nachricht wie das folgende Objekt aussehen. Das -Objekt stellt eine einzelne Zeile dar, wobei die Schlüssel Spaltennamen und die Werte der Wert sind, der in jeder Spalte eingefügt werden soll:

```json
{
    "Timestamp": <value-of-Timestamp>,
    "DeviceId": <value-of-deviceId>,
    "T1": <value-of-T1>,
    "T2": <value-of-T2>,
    "T3": <value-of-T3>,
}
```

In diesem Szenario sendet das Gerät die Telemetriewerte `t1`, `t2` und `t3` in einer Eingabenachricht, die wie im folgenden Beispiel aussieht:

```json
{
  "applicationId": "c57fe8d9-d15d-4659-9814-d3cc38ca9e1b",
  "enqueuedTime": "1933-01-26T03:10:44.480001324Z",
  "messageSource": "telemetry",
  "telemetry": [
    {
      "id": "dtmi:temperaturesensor288:sensors1lr:t1;1",
      "name": "t1",
      "value": 1.1889838348731093e+308
    },
    {
      "id": "dtmi:temperaturesensor288:sensors1lr:t2;1",
      "name": "t2",
      "value": 1.4347093391531383e+308
    },
    {
      "id": "dtmi:temperaturesensor288:sensors1lr:t3;1",
      "name": "t3",
      "value": 2.9700885230380616e+307
    }
  ],
  "device": {
    "id": "oozrnl1zs857",
    "name": "haptic alarm",
    "templateId": "dtmi:modelDefinition:nhhbjotee:qytxnp8hi",
    "templateName": "hapticsensors",
    "properties": {
      "reported": []
    },
    "cloudProperties": [],
    "simulated": true,
    "approved": false,
    "blocked": false,
    "provisioned": true
  }
}
```

Die folgende JQ-Abfrage gibt die Telemetriewerte `T1`, `T2` und `T3`, den `Timestamp` und die `deviceId` als Nachricht mit Schlüssel-Wert-Paaren aus, die dem Azure Data Explorer-Tabellenschema entsprechen:

```jq
import "iotc" as iotc;
{
  deviceId: .device.id,
  Timestamp: .enqueuedTime,
  T1: .telemetry | iotc::find(.name == "t1").value,
  T2: .telemetry | iotc::find(.name == "t2").value,
  T3: .telemetry | iotc::find(.name == "t3").value,
}
```

JSON-Ausgabe:

```json
{
  "T1": 1.1889838348731093e+308,
  "T2": 1.4347093391531383e+308,
  "T3": 2.9700885230380616e+307,
  "Timestamp": "1933-01-26T03:10:44.480001324Z",
  "deviceId": "oozrnl1zs857"
}
```

Weitere Informationen zum Hinzufügen eines Azure Data Explorer-Clusters und einer Azure Data Explorer-Datenbank als Exportziel finden Sie unter [Erstellen eines Azure Data Explorer-Ziels](howto-export-data.md#create-an-azure-data-explorer-destination).

### <a name="scenario-2-breaking-apart-a-telemetry-array"></a>Szenario 2: Aufteilen eines Telemetriedaten-Arrays

In diesem Szenario sendet das Gerät das folgende Array von Telemetriedaten in einer einzigen Nachricht:

```json
{
  "applicationId": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
  "enqueuedTime": "1909-10-10T07:11:56.078161042Z",
  "messageSource": "telemetry",
  "telemetry": [
    {
      "id": "dtmi:sample1:data;1",
      "name": "data",
      "value": [
        {
          "id": "subdevice1",
          "values": {
              "running": true,
              "cycleCount": 2315
          }
        },
        {
          "id": "subdevice2",
          "values": {
              "running": false,
              "cycleCount": 824567
          }
        }
      ]
    },
    {
      "id": "dtmi:sample1:parentStatus;1",
      "name": "parentStatus",
      "value": "healthy"
    }
  ],
  "device": {
    "id": "9xwhr7khkfri",
    "name": "wireless port",
    "templateId": "dtmi:hpzy1kfcbt2:umua7dplmbd",
    "templateName": "Smart Vitals Patch",
    "properties": {
      "reported": [
        {
          "id": "dtmi:sample1:prop;1",
          "name": "Connectivity",
          "value": "Tenetur ut quasi minus ratione voluptatem."
        }
      ]
    },
    "cloudProperties": [],
    "simulated": true,
    "approved": true,
    "blocked": false,
    "provisioned": false
  }
}
```

Sie möchten diese Gerätedaten so transformieren, dass sie dem folgenden Tabellenschema entsprechen:

| cycleCount | deviceId | enqueuedTime | parentStatus | „Wird ausgeführt“ | subdeviceId |
| :--------- | :------- | :----------- | :----------- | :------ | :---------- |
| 2315   | „9xwhr7khkfri“ | „1909-10-10T07:11:56.078161042Z“ | „Fehlerfrei“ | true | „subdevice1“ |
| 824567 | „9xwhr7khkfri“ | „1909-10-10T07:11:56.078161042Z“ | „Fehlerfrei“ | false | „subdevice2“ |

Die folgende JQ-Abfrage erstellt eine separate Ausgabenachricht für jeden „subdevice“-Eintrag in der Nachricht und enthält einige allgemeine Informationen aus der Basisnachricht und dem übergeordneten Gerät. Diese Abfrage vereinfacht die Ausgabe und grenzt logische Unterteilungen in Ihren Daten ab, die als eine einzelne Nachricht eingetroffen sind:

```jq
import "iotc" as iotc;
{
    enqueuedTime: .enqueuedTime,
    deviceId: .device.id,
    parentStatus: .telemetry | iotc::find(.name == "parentStatus").value
} + (
    .telemetry
    | iotc::find(.name == "data").value[]
    | {
        subdeviceId: .id,
        running: .values.running,
        cycleCount: .values.cycleCount
    }
)
```

JSON-Ausgabe:

```json
{
    "cycleCount": 2315,
    "deviceId": "9xwhr7khkfri",
    "enqueuedTime": "1909-10-10T07:11:56.078161042Z",
    "parentStatus": "healthy",
    "running": true,
    "subdeviceId": "subdevice1"
},
{
    "cycleCount": 824567,
    "deviceId": "9xwhr7khkfri",
    "enqueuedTime": "1909-10-10T07:11:56.078161042Z",
    "parentStatus": "healthy",
    "running": false,
    "subdeviceId": "subdevice2"
}
```

### <a name="scenario-3-power-bi-streaming"></a>Szenario 3: Power BI-Streaming

Mit dem Feature „Power BI Echtzeitstreaming“ können Sie Daten in einem Dashboard anzeigen, das in Echtzeit mit geringer Latenz aktualisiert wird. Weitere Informationen finden Sie unter [Echtzeit-Streaming in Power BI](/power-bi/connect-data/service-real-time-streaming).

Um IoT Central bei Power BI Streaming verwenden zu können, richten Sie einen Webhookexport ein, der Anforderungstexte in einem bestimmten Format sendet. In diesem Beispiel wird davon ausgegangen, dass Sie ein Power BI Streaming-Dataset mit dem folgenden Schema haben:

```json
[
  {
    "bloodPressureDiastolic": 161438124,
    "bloodPressureSystolic": -966387879,
    "deviceId": "9xwhr7khkfri",
    "deviceName": "wireless port",
    "heartRate": -633994413,
    "heartRateVariability": -37514094,
    "respiratoryRate": 1582211310,
    "timestamp": "1909-10-10T07:11:56.078161042Z"
  }
]
```

Zum Erstellen des Webhook-Exportziels benötigen Sie den REST-API-URL-Endpunkt für Ihr Power BI Streaming-Dataset.

In diesem Szenario sendet das Gerät Telemetrienachrichten, die wie im folgenden Beispiel aussehen:

```json
{
  "applicationId": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
  "enqueuedTime": "1909-10-10T07:11:56.078161042Z",
  "messageSource": "telemetry",
  "telemetry": [
    {
      "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:HeartRate;1",
      "name": "HeartRate",
      "value": -633994413
    },
    {
      "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:RespiratoryRate;1",
      "name": "RespiratoryRate",
      "value": 1582211310
    },
    {
      "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:HeartRateVariability;1",
      "name": "HeartRateVariability",
      "value": -37514094
    },
    {
      "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:BodyTemperature;1",
      "name": "BodyTemperature",
      "value": 5.323322666478241e+307
    },
    {
      "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:FallDetection;1",
      "name": "FallDetection",
      "value": "Earum est nobis at voluptas id qui."
    },
    {
      "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:BloodPressure;1",
      "name": "BloodPressure",
      "value": {
        "Diastolic": 161438124,
        "Systolic": -966387879
      }
    }
  ],
  "device": {
    "id": "9xwhr7khkfri",
    "name": "wireless port",
    "templateId": "dtmi:hpzy1kfcbt2:umua7dplmbd",
    "templateName": "Smart Vitals Patch",
    "properties": {
      "reported": [
        {
          "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_wr:DeviceStatus;1",
          "name": "DeviceStatus",
          "value": "Id optio iste vero et neque sit."
        }
      ]
    },
    "cloudProperties": [],
    "simulated": true,
    "approved": true,
    "blocked": false,
    "provisioned": false
  }
}
```

Die folgende JQ-Abfrage transformiert die Eingabenachricht in ein für den Webhook geeignetes Format, um sie an das Power BI Streaming-Dataset zu senden. Dieses Beispiel enthält eine Filterbedingung, damit nur Nachrichten für eine bestimmte Gerätevorlage ausgegeben werden. Sie könnten mithilfe der Datenexportfilterfunktion nach Gerätevorlage filtern:

```jq
import "iotc" as iotc;
if .device.templateId == "dtmi:hpzy1kfcbt2:umua7dplmbd" then 
    [{
        deviceId: .device.id,
        timestamp: .enqueuedTime,
        deviceName: .device.name,
        bloodPressureSystolic: .telemetry | iotc::find(.name == "BloodPressure").value.Systolic,
        bloodPressureDiastolic: .telemetry | iotc::find(.name == "BloodPressure").value.Diastolic,
        heartRate: .telemetry | iotc::find(.name == "HeartRate").value,
        heartRateVariability: .telemetry | iotc::find(.name == "HeartRateVariability").value,
        respiratoryRate: .telemetry | iotc::find(.name == "RespiratoryRate").value
    }]
else
    empty
end
```

JSON-Ausgabe:

```json
{
  "bloodPressureDiastolic": 161438124,
  "bloodPressureSystolic": -966387879,
  "deviceId": "9xwhr7khkfri",
  "deviceName": "wireless port",
  "heartRate": -633994413,
  "heartRateVariability": -37514094,
  "respiratoryRate": 1582211310,
  "timestamp": "1909-10-10T07:11:56.078161042Z"
}
```

### <a name="scenario-4-export-data-to-azure-data-explorer-and-visualize-it-in-power-bi"></a>Szenario 4: Exportieren von Daten in Azure Data Explorer und ihr Visualisieren in Power BI

In diesem Szenario exportieren Sie Daten in Azure Data Explorer und visualisieren Sie dann mithilfe eines Connectors in Power BI. Weitere Informationen zum Hinzufügen eines Azure Data Explorer-Clusters und einer Azure Data Explorer-Datenbank als Exportziel finden Sie unter [Erstellen eines Azure Data Explorer-Ziels](howto-export-data.md#create-an-azure-data-explorer-destination).

In diesem Szenario wird eine Azure Data Explorer-Tabelle mit dem folgenden Schema verwendet:

``` kusto
.create table smartvitalspatch (
  EnqueuedTime:datetime,
  Message:string,
  Application:string,
  Device:string,
  Simulated:boolean,
  Template:string,
  Module:string,
  Component:string,
  Capability:string,
  Value:dynamic
)
```

In diesem Szenario sendet das Gerät Telemetrienachrichten, die wie im folgenden Beispiel aussehen:

```json
{
    "applicationId": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
    "enqueuedTime": "1909-10-10T07:11:56.078161042Z",
    "messageSource": "telemetry",
    "telemetry": [
        {
            "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:HeartRate;1",
            "name": "HeartRate",
            "value": -633994413
        },
        {
            "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:RespiratoryRate;1",
            "name": "RespiratoryRate",
            "value": 1582211310
        },
        {
            "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:HeartRateVariability;1",
            "name": "HeartRateVariability",
            "value": -37514094
        },
        {
            "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:BodyTemperature;1",
            "name": "BodyTemperature",
            "value": 5.323322666478241e+307
        },
        {
            "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:FallDetection;1",
            "name": "FallDetection",
            "value": "Earum est nobis at voluptas id qui."
        },
        {
            "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:BloodPressure;1",
            "name": "BloodPressure",
            "value": {
                "Diastolic": 161438124,
                "Systolic": -966387879
            }
        }
    ],
    "device": {
        "id": "9xwhr7khkfri",
        "name": "wireless port",
        "templateId": "dtmi:hpzy1kfcbt2:umua7dplmbd",
        "templateName": "Smart Vitals Patch",
        "properties": {
            "reported": [
                {
                    "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_wr:DeviceStatus;1",
                    "name": "DeviceStatus",
                    "value": "Id optio iste vero et neque sit."
                }
            ]
        },
        "cloudProperties": [],
        "simulated": true,
        "approved": true,
        "blocked": false,
        "provisioned": false
    }
}
```

Die folgende JQ-Abfrage transformiert die Eingabenachricht in eine separate Ausgabenachricht für jeden Telemetriewert. Diese Transformation erzeugt eine Ausgabe, die dem Azure Data Explorer-Tabellenschema entspricht. Die Transformation verwendet ein „Entity-Attribute-Value“-Schema, bei dem jede Zeile einen einzelnen Telemetriewert enthält und der Name der Telemetriedaten ein Wert in einer separaten Spalte in derselben Zeile ist:

```jq
. as $in | .telemetry[] | {
  EnqueuedTime: $in.enqueuedTime,
  Message: $in.messageId,
  Application: $in.applicationId,
  Device: $in.device.id,
  Simulated: $in.device.simulated,
  Template: ($in.device.templateName // ""),
  Module: ($in.module // ""),
  Component: ($in.component // ""),
  Capability: .name,
  Value: .value
}
```

JSON-Ausgabe:

```json
{
  "Application": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
  "Capability": "HeartRate",
  "Component": "",
  "Device": "9xwhr7khkfri",
  "EnqueuedTime": "1909-10-10T07:11:56.078161042Z",
  "Message": null,
  "Module": "",
  "Simulated": true,
  "Template": "Smart Vitals Patch",
  "Value": -633994413
},
{
  "Application": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
  "Capability": "RespiratoryRate",
  "Component": "",
  "Device": "9xwhr7khkfri",
  "EnqueuedTime": "1909-10-10T07:11:56.078161042Z",
  "Message": null,
  "Module": "",
  "Simulated": true,
  "Template": "Smart Vitals Patch",
  "Value": 1582211310
},
{
  "Application": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
  "Capability": "HeartRateVariability",
  "Component": "",
  "Device": "9xwhr7khkfri",
  "EnqueuedTime": "1909-10-10T07:11:56.078161042Z",
  "Message": null,
  "Module": "",
  "Simulated": true,
  "Template": "Smart Vitals Patch",
  "Value": -37514094
},
{
  "Application": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
  "Capability": "BodyTemperature",
  "Component": "",
  "Device": "9xwhr7khkfri",
  "EnqueuedTime": "1909-10-10T07:11:56.078161042Z",
  "Message": null,
  "Module": "",
  "Simulated": true,
  "Template": "Smart Vitals Patch",
  "Value": 5.323322666478241e+307
},
{
  "Application": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
  "Capability": "FallDetection",
  "Component": "",
  "Device": "9xwhr7khkfri",
  "EnqueuedTime": "1909-10-10T07:11:56.078161042Z",
  "Message": null,
  "Module": "",
  "Simulated": true,
  "Template": "Smart Vitals Patch",
  "Value": "Earum est nobis at voluptas id qui."
},
{
  "Application": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
  "Capability": "BloodPressure",
  "Component": "",
  "Device": "9xwhr7khkfri",
  "EnqueuedTime": "1909-10-10T07:11:56.078161042Z",
  "Message": null,
  "Module": "",
  "Simulated": true,
  "Template": "Smart Vitals Patch",
  "Value": {
      "Diastolic": 161438124,
      "Systolic": -966387879
  }
}
```

Die Ausgabedaten werden in Ihren Azure Data Explorer-Cluster exportiert. Führen Sie die folgenden Schritte aus, um die exportierten Daten in Power BI zu visualisieren:

1. Installieren Sie die Power BI-Anwendung. Sie können die Power BI-Desktopanwendung aus [Go from data to insight to action with Power BI Desktop](https://powerbi.microsoft.com/desktop/) (Den Weg von Daten über Erkenntnis zur praktischen Umsetzung mit Power BI-Desktop gehen) herunterladen.
1. Laden Sie die Power BI-Desktopdatei [IoT Central ADX Connector.pbit](https://github.com/Azure-Samples/iot-central-docs-samples/raw/master/azure-data-explorer-power-bi/IoT%20Central%20ADX%20Connector.pbit) aus GitHub herunter.
1. Öffnen Sie mithilfe der Power BI-Desktop-App die im vorhergehenden Schritt heruntergeladene Datei *IoT Central ADX Connector.pbit*. Geben Sie bei der entsprechenden Aufforderung den Azure Data Explorer-Cluster, die Azure Data Explorer-Datenbank und die Tabelleninformationen ein, die Sie sich zuvor notiert haben.

Jetzt können Sie die Daten in Power BI visualisieren:

:::image type="content" source="media/howto-transform-data-internally/powerbi-report.png" alt-text="Screenshot des Power BI-Berichts mit Daten aus IoT Central." border="false":::

## <a name="next-steps"></a>Nächste Schritte

Sie wissen jetzt, wie Sie Daten in IoT Central transformieren können. Als Nächstes sollten Sie sich über die [Verwendung von Analysen in IoT Central](./howto-create-analytics.md) informieren.
