---
title: Verwenden der REST-API zum Abfragen von Geräten in Azure IoT Central
description: Hier erfahren Sie, wie Sie mit der REST-API in IoT Central Geräte in einer Anwendung abfragen.
author: dominicbetts
ms.author: dobett
ms.date: 10/12/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: b287bb463519d4b6ef76997dfb778ac4827014c2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095969"
---
# <a name="how-to-use-the-iot-central-rest-api-to-query-devices"></a>Verwenden der REST-API in IoT Central zum Abfragen von Geräten

Mit der IoT Central-REST-API können Sie Clientanwendungen entwickeln, die in IoT Central-Anwendungen integriert werden. Mit der REST-API können Sie in Ihrer IoT Central-Anwendung Geräte abfragen. Folgende Abfragen können Sie beispielsweise mit der REST-API durchführen:

- Abrufen der letzten zehn Telemetriewerte, die von einem Gerät gemeldet wurden.
- Abrufen der Daten der letzten 24 Stunden von Geräten, die sich in einem Raum befinden. Mit „Raum“ wird hier eine Geräte- oder Cloudeigenschaft bezeichnet.
- Identifizieren aller Geräte mit einem Fehlerstatus und veralteter Firmware.
- Abrufen der durchschnittlichen Telemetrietrends von Geräten in Zeitfenstern von zehn Minuten.
- Abrufen der aktuellen Firmwareversion aller Thermostatgeräte.

In diesem Artikel wird beschrieben, wie Sie Geräte mit der `/query`-API abfragen.

Ein Gerät kann die von ihm unterstützten Eigenschaften, Telemetriedaten und Befehle in _Komponenten_ und _Module_ gruppieren.

Jeder IoT Central-REST-API-Aufruf erfordert einen Autorisierungsheader. Weitere Informationen finden Sie unter [Authentifizieren und Autorisieren von IoT Central-REST-API-Aufrufen](howto-authorize-rest-api.md).

Die Referenzdokumentation für die IoT Central-REST-API finden Sie unter [Azure IoT Central: Referenz zur REST-API](/rest/api/iotcentral/).

## <a name="run-a-query"></a>Ausführen einer Abfrage

Führen Sie eine Abfrage mit der folgenden Anforderung aus:

```http
POST https://{your app subdomain}.azureiotcentral.com/api/query?api-version=1.1-preview
```

Die Abfrage befindet sich im Text der Anforderung und sieht wie im folgenden Beispiel aus:

```json
{
  "query": "SELECT $id, $ts, temperature, humidity FROM urn:modelDefinition:fupmoiu28b:ymju9efv9 WHERE WITHIN_WINDOW(P1D)"
}
```

Der Wert `urn:modelDefinition:fupmoiu28b:ymju9efv9` in der `FROM`-Klausel stellt eine *Gerätevorlagen-ID* dar. Navigieren Sie zum Anzeigen einer Gerätevorlagen-ID in Ihrer IoT Central-Anwendung zur Seite **Geräte**, und bewegen Sie den Mauszeiger über ein Gerät, das die Vorlage verwendet. Die Gerätevorlagen-ID wird auf der Karte angezeigt:

:::image type="content" source="media/howto-query-with-rest-api/show-device-template-id.png" alt-text="Screenshot: Anzeigen der Gerätevorlagen-ID in der Seiten-URL":::

Die Antwort auf diese Anforderung sieht wie das folgende Beispiel aus:

```json
{
  "results": [
    {
      "$id": "sample-003",
      "$ts": "2021-09-10T12:59:52.015Z",
      "temperature": 47.632160152311016,
      "humidity": 49.726422005390816
    },
    {
      "$id": "sample-001",
      "$ts": "2021-09-10T13:01:34.286Z",
      "temperature": 58.898120617808495,
      "humidity": 44.66125772328022
    },
    {
      "$id": "sample-001",
      "$ts": "2021-09-10T13:04:04.96Z",
      "temperature": 52.79601469228174,
      "humidity": 71.5067230188416
    },
    {
      "$id": "sample-002",
      "$ts": "2021-09-10T13:04:36.877Z",
      "temperature": 49.610062789623264,
      "humidity": 52.78538601804491
    }
  ]
}
```

## <a name="syntax"></a>Syntax

Die Abfragesyntax ähnelt der SQL-Syntax und besteht aus den folgenden Klauseln:

- `SELECT` (erforderlich): Definiert die Daten, die Sie abrufen möchten, z. B. die Telemetriewerte eines Geräts.
- `FROM` (erforderlich): Identifiziert den Gerätetyp, den Sie abfragen. Mit dieser Klausel wird die Gerätevorlagen-ID angegeben.
- `WHERE` (optional): Ermöglicht das Filtern der Ergebnisse.
- `ORDER BY` (optional): Ermöglicht das Sortieren der Ergebnisse.
- `GROUP BY` (optional): Ermöglicht das Aggregieren der Ergebnisse.

In den folgenden Abschnitten werden diese Klauseln genauer beschrieben.

## <a name="select-clause"></a>SELECT-Klausel

Mit der `SELECT`-Klausel werden die Datenwerte aufgelistet, die in der Abfrageausgabe enthalten sein müssen. Sie kann die folgenden Elemente enthalten:

- Telemetrie. Verwenden Sie die Telemetrienamen aus der Gerätevorlage.
- Gemeldete Eigenschaften Verwenden Sie die Eigenschaftennamen aus der Gerätevorlage.
- Cloudeigenschaften: Verwenden Sie die Namen der Cloudeigenschaften aus der Gerätevorlage.
- `$id`. Der Geräte-ID
- `$provisioned`. Ein boolescher Wert, der angibt, ob das Gerät bereits bereitgestellt wurde.
- `$simulated`. Ein boolescher Wert, der angibt, ob es sich um ein simuliertes Gerät handelt.
- `$ts`. Der Zeitstempel, der einem Telemetriewert zugeordnet ist.

Nutzt Ihre Gerätevorlage Komponenten wie die Komponente **Geräteinformationen**, verweisen Sie auf die in der Komponente definierten Telemetriedaten oder Eigenschaften wie folgt:

```json
{
  "query": "SELECT deviceInformation.model, deviceInformation.swVersion FROM urn:modelDefinition:fupmoiu28b:ymju9efv9"
}
```

Den Komponentennamen finden Sie in der Gerätevorlage:

:::image type="content" source="media/howto-query-with-rest-api/show-component-name.png" alt-text="Screenshot: Anzeigen des Komponentennamens":::

In der `SELECT`-Klausel gelten die folgenden Einschränkungen:

- Es gibt keinen Platzhalteroperator.
- Die Auswahlliste darf nicht mehr als 15 Elemente enthalten.
- In einer einzelnen Abfrage können Sie entweder Telemetriedaten oder Eigenschaften auswählen, nicht beides. Eine Eigenschaftenabfrage kann sowohl gemeldete Eigenschaften als auch Cloudeigenschaften enthalten.

### <a name="aliases"></a>Aliase

Mit dem Schlüsselwort `AS` definieren Sie einen Alias für ein Element in der `SELECT`-Klausel. Der Alias wird in der Abfrageausgabe verwendet. Sie können ihn aber auch an einer anderen Stelle in der Abfrage verwenden. Zum Beispiel:

```json
{
  "query": "SELECT $id as ID, $ts as timestamp, temperature as t, pressure as p FROM urn:modelDefinition:fupmoiu28b:ymju9efv9 WHERE WITHIN_WINDOW(P1D) AND t > 0 AND p > 50"
}
```

> [!TIP]
> Es ist nicht möglich, in der Auswahlliste ein anderes Element als Alias zu verwenden. Folgender Code ist beispielsweise nicht zulässig: `SELECT id, temp AS id...`.

Das Ergebnis sieht in etwa wie folgt aus:

```json
{
  "results": [
    {
      "ID": "sample-002",
      "timestamp": "2021-09-10T11:40:29.188Z",
      "t": 40.20355053736378,
      "p": 79.26806508746755
    },
    {
      "ID": "sample-001",
      "timestamp": "2021-09-10T11:43:42.61Z",
      "t": 68.03536237975348,
      "p": 58.33517075380311
    }
  ]
}
```

### <a name="top"></a>TOP

Mit `TOP` können Sie die Anzahl der von der Abfrage zurückgegebenen Ergebnisse begrenzen. Die folgende Abfrage gibt z. B. die ersten zehn Ergebnisse zurück:

```json
{
    "query": "SELECT TOP 10 $id as ID, $ts as timestamp, temperature, humidity FROM urn:modelDefinition:fupmoiu28b:ymju9efv9"
}
```

Ohne `TOP` gibt die Abfrage maximal 10.000 Ergebnisse zurück.

Mit [ORDER BY](#order-by-clause) können Sie die Ergebnisse sortieren, bevor deren Anzahl mit `TOP` begrenzt wird.

## <a name="from-clause"></a>FROM-Klausel

Die `FROM`-Klausel muss eine Gerätevorlagen-ID enthalten. Mit der `FROM`-Klausel geben Sie den Gerätetyp an, den Sie abfragen.

Navigieren Sie zum Anzeigen einer Gerätevorlagen-ID in Ihrer IoT Central-Anwendung zur Seite **Geräte**, und bewegen Sie den Mauszeiger über ein Gerät, das die Vorlage verwendet. Die Gerätevorlagen-ID wird auf der Karte angezeigt:

:::image type="content" source="media/howto-query-with-rest-api/show-device-template-id.png" alt-text="Screenshot: Anzeigen der Gerätevorlagen-ID in der Seiten-URL":::

Sie können die Gerätevorlagen-ID für ein Gerät auch mithilfe des REST-API-Aufrufs [Geräte – Abrufen](/rest/api/iotcentral/1.1-previewdataplane/devices/get) abrufen.

## <a name="where-clause"></a>WHERE-Klausel

Mit der `WHERE`-Klausel können Sie Werte und Zeitfenster zum Filtern der Ergebnisse verwenden:

### <a name="time-windows"></a>Zeitfenster

Verwenden Sie `WITHIN_WINDOW` als Teil der `WHERE`-Klausel, um Telemetriedaten abzurufen, die von Ihrer Anwendung innerhalb eines angegebenen Zeitfensters empfangen wurden. Mit der folgenden Abfrage können Sie beispielsweise Telemetriedaten zu Temperatur und Luftfeuchtigkeit des vorherigen Tages abrufen:

```json
{
  "query": "SELECT $id, $ts, temperature, humidity FROM urn:modelDefinition:fupmoiu28b:ymju9efv9 WHERE WITHIN_WINDOW(P1D)"
}
```

Zur Angabe des Zeitfensterwerts wird das Format [ISO 8601 für Zeitspannen](https://en.wikipedia.org/wiki/ISO_8601#Durations) verwendet. Die folgende Tabelle enthält einige Beispiele:

| Beispiel | BESCHREIBUNG                |
| ------- | -------------------------- |
| PT10M   | Letzte 10 Minuten           |
| P1D     | Letzter Tag                   |
| P2DT12H | Letzte 2 Tage und 12 Stunden |
| P1W     | Letzte Woche                  |
| PT5H    | Letzte 5 Stunden            |
| '2021-06-13T13:00:00Z/2021-06-13T15:30:00Z' | Bestimmte Zeitspanne |

> [!NOTE]
> Zeitfenster können nur zur Abfrage von Telemetriedaten verwendet werden.

### <a name="value-comparisons"></a>Wertvergleiche

Sie können Telemetriedaten oder Eigenschaftswerte abrufen, die auf bestimmten Werten basieren. Die folgende Abfrage gibt beispielsweise alle Meldungen mit einer Temperatur größer als 0, einem Druck größer als 50 und einer Geräte-ID von **sample-002** oder **sample-003** zurück:

```json
{
  "query": "SELECT $id, $ts, temperature AS t, pressure AS p FROM urn:modelDefinition:fupmoiu28b:ymju9efv9 WHERE WITHIN_WINDOW(P1D) AND t > 0 AND p > 50 AND $id IN ['sample-002', 'sample-003']"
}
```

Die folgenden Operatoren werden unterstützt:

- Die logischen Operatoren `AND` und `OR`
- Die Vergleichsoperatoren `=`, `!=`, `>`, `<`, `>=`, `<=`, `<>` und `IN`

> [!NOTE]
> Der `IN`-Operator kann nur mit Telemetriedaten und `$id` verwendet werden.

In der `WHERE`-Klausel gelten die folgenden Einschränkungen:

- In einer einzelnen Abfrage können maximal zehn Operatoren verwendet werden.
- In einer Telemetrieabfrage darf die `WHERE`-Klausel nur Filter für Telemetriedaten und Gerätemetadaten enthalten.
- In einer Eigenschaftenabfrage darf die `WHERE`-Klausel nur Filter für gemeldete Eigenschaften, Cloudeigenschaften und Gerätemetadaten enthalten.

## <a name="aggregations-and-group-by-clause"></a>Aggregationen und die GROUP BY-Klausel

Mit Aggregationsfunktionen können Sie für Telemetriedaten innerhalb eines Zeitfensters Werte wie den Durchschnitts-, Maximal- und Minimalwert berechnen. Mit der folgenden Abfrage wird beispielsweise die durchschnittliche Temperatur und Luftfeuchtigkeit des Geräts `sample-001` in Zeitfenstern von zehn Minuten berechnet:

```json
{
  "query": "SELECT AVG(temperature), AVG(pressure) FROM urn:modelDefinition:fupmoiu28b:ymju9efv9 WHERE WITHIN_WINDOW(P1D) AND $id='{{DEVICE_ID}}' GROUP BY WINDOW(PT10M)"
}
```

Die Ergebnisse sehen in etwa wie folgt aus:

```json
{
    "results": [
        {
            "$ts": "2021-09-14T11:40:00Z",
            "avg_temperature": 49.212146114456104,
            "avg_pressure": 48.590304135023764
        },
        {
            "$ts": "2021-09-14T11:30:00Z",
            "avg_temperature": 52.44844454703927,
            "avg_pressure": 52.25973211022142
        },
        {
            "$ts": "2021-09-14T11:20:00Z",
            "avg_temperature": 50.14626272506926,
            "avg_pressure": 48.98400386898757
        }
    ]
}
```

Die folgenden Aggregatfunktionen werden unterstützt: `SUM`, `MAX`, `MIN`, `COUNT`, `AVG`, `FIRST` und `LAST`.

Geben Sie die Fenstergröße mit `GROUP BY WINDOW` an. Ohne `GROUP BY WINDOW` aggregiert die Abfrage die Telemetriedaten der letzten 30 Tage.

> [!NOTE]
> Es können nur Telemetriewerte aggregiert werden.

## <a name="order-by-clause"></a>ORDER BY-Klausel

Mit der `ORDER BY`-Klausel können Sie die Abfrageergebnisse nach einem Telemetriewert, dem Zeitstempel oder der Geräte-ID sortieren. Dabei können Sie die Ergebnisse in aufsteigender oder absteigender Reihenfolge sortieren. Bei der folgenden Abfrage werden beispielsweise zuerst die neuesten Ergebnisse zurückgegeben:

```json
{
  "query": "SELECT $id as ID, $ts as timestamp, temperature, humidity FROM urn:modelDefinition:fupmoiu28b:ymju9efv9 ORDER BY timestamp DESC"
}
```

> [!TIP]
> Kombinieren Sie `ORDER BY` mit `TOP`, um die Anzahl der nach dem Sortieren zurückgegebenen Ergebnisse zu begrenzen.

## <a name="limits"></a>Einschränkungen

Die folgenden Einschränkungen gelten aktuell für Abfragen:

- Die `SELECT`-Klauselliste darf maximal 15 Elemente enthalten.
- Die `WHERE`-Klausel darf maximal zehn logische Operatoren enthalten.
- Abfragen geben maximal 10.000 Datensätze zurück.
- Eine Abfragezeichenfolge darf maximal 350 Zeichen lang sein.
- In der `SELECT`-Klauselliste dürfen keine Platzhalter (`*`) verwendet werden.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun erfahren haben, wie Sie mithilfe der REST-API Geräte abfragen, können Sie mit dem Artikel [Verwenden der IoT Central-REST-API zum Verwalten von Benutzern und Rollen](howto-manage-users-roles-with-rest-api.md) fortfahren.
