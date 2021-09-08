---
title: Verwenden von Gerätebefehlen in einer Azure IoT Central-Lösung
description: Erfahren Sie, wie Sie Gerätebefehle in einer Azure IoT Central-Lösung verwenden. In diesem Tutorial erfahren Sie, wie Sie Gerätebefehle in einer Client-App Ihrer Azure IoT Central-Anwendung verwenden.
author: dominicbetts
ms.author: dobett
ms.date: 01/07/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: d6a1d252a6b9068697fe738d4d2b1c6bbf6ceeb5
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/09/2021
ms.locfileid: "113588271"
---
# <a name="how-to-use-commands-in-an-azure-iot-central-solution"></a>Verwenden von Befehlen in einer Azure IoT Central-Lösung

In dieser Schrittanleitung erfahren Sie, wie Sie Befehle verwenden können, die in einer Gerätevorlage definiert sind.

Ein Operator kann über die Benutzeroberfläche von IoT Central einen Befehl auf einem Gerät aufrufen. Befehle steuern das Verhalten eines Geräts. Beispielsweise kann ein Operator einen Befehl zum Neustarten eines Geräts oder zum Sammeln von Diagnosedaten aufrufen.

Ein Gerät kann Folgendes durchführen:

* Sofortiges Reagieren auf einen Befehl
* Benachrichtigen von IoT Central beim Empfangen des Befehls und späteres Informieren von IoT Central über den Abschluss eines *zeitintensiven Befehls*

Standardmäßig wird in Befehlen erwartet, dass ein Gerät verbunden ist. Sie führen daher zu einem Fehler, wenn das Gerät nicht erreicht werden kann. Wenn Sie auf der Benutzeroberfläche der Gerätevorlage die Option **Warteschlange (falls offline)** auswählen, kann ein Befehl in die Warteschlange eingereiht werden, bis ein Gerät online geschaltet wird. Diese *Offlinebefehle* werden in einem separaten Abschnitt weiter unten in diesem Artikel beschrieben.

## <a name="define-your-commands"></a>Definieren Ihrer Befehle

Standardbefehle werden an ein Gerät gesendet, um das Gerät anzuweisen, eine Aktion auszuführen. Ein Befehl kann Parameter mit zusätzlichen Informationen enthalten. So könnte ein Befehl zum Öffnen eines Ventils auf einem Gerät z. B. einen Parameter enthalten, der angibt, wie weit das Ventil geöffnet werden soll. Befehle können auch einen Rückgabewert empfangen, wenn das Gerät den Befehl abschließt. Beispielsweise kann ein Befehl, der ein Gerät anweist, einige Diagnosen auszuführen, einen Diagnosebericht als Rückgabewert erhalten.

Befehle werden als Teil einer Gerätevorlage definiert. Der folgende Screenshot zeigt die Definition des Befehls **Get Max-Min report** in der Gerätevorlage **Thermostat**. Dieser Befehl weist sowohl Anforderungs- als auch Antwortparameter auf: 

:::image type="content" source="media/howto-use-commands/command-definition.png" alt-text="Screenshot des Befehls „Get Max-Min report“ in der Gerätevorlage „Thermostat“":::

In der folgenden Tabelle sind die Konfigurationseinstellungen für eine Befehlsfunktion angegeben:

| Feld             |BESCHREIBUNG|
|-------------------|-----------|
|Anzeigename       |Der auf Dashboardkacheln und Geräteformularen verwendete Befehlswert.|
| Name            | Der Name des Befehls. IoT Central generiert einen Wert für dieses Feld aus dem Anzeigenamen, Sie können aber ggf. einen eigenen Wert auswählen. Dieses Feld muss alphanumerisch sein. Der Wert von **Name** wird im Gerätecode verwendet.|
| Funktionstyp | Befehl.|
| Warteschlange (wenn offline) | Gibt an, ob dieser Befehl als *Offlinebefehl* ausgeführt werden soll. |
| BESCHREIBUNG     | Eine Beschreibung der Befehlsfunktion.|
| Comment     | Beliebige Kommentare zur Befehlsfunktion.|
| Anforderung     | Die Nutzdaten für den Gerätebefehl|
| Antwort     | Die Nutzdaten der Antwort auf den Gerätebefehl|

Der folgende Codeausschnitt zeigt die JSON-Darstellung des Befehls im Gerätemodell. In diesem Beispiel weist der Antwortwert den komplexen Typ **Object** mit mehreren Feldern auf:

```json
{
  "@type": "Command",
  "name": "getMaxMinReport",
  "displayName": "Get Max-Min report.",
  "description": "This command returns the max, min and average temperature from the specified time to the current time.",
  "request": {
    "name": "since",
    "displayName": "Since",
    "description": "Period to return the max-min report.",
    "schema": "dateTime"
  },
  "response": {
    "name" : "tempReport",
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
        },
        {
          "name" : "avgTemp",
          "displayName": "Average Temperature",
          "schema": "double"
        },
        {
          "name" : "startTime",
          "displayName": "Start Time",
          "schema": "dateTime"
        },
        {
          "name" : "endTime",
          "displayName": "End Time",
          "schema": "dateTime"
        }
      ]
    }
  }
}
```

> [!TIP]
> Sie können ein Gerätemodell auf der Seite der Gerätevorlage exportieren.

Anhand der folgenden Felder können Sie diese Befehlsdefinition mit dem Screenshot der Benutzeroberfläche verknüpfen:

* `@type` – zur Angabe des Funktionstyps: `Command`
* `name` als Befehlswert

Über optionale Felder, z. B. Anzeigename und Beschreibung, können Sie der Schnittstelle und den Funktionen weitere Details hinzufügen.

## <a name="standard-commands"></a>Standardbefehle

In diesem Abschnitt wird gezeigt, wie ein Gerät einen Antwortwert sendet, sobald der Befehl empfangen wird.

Der folgende Codeausschnitt zeigt, wie ein Gerät sofort auf einen Befehl reagieren kann, indem es einen Erfolgscode sendet:

> [!NOTE]
> In diesem Artikel wird der Einfachheit halber „Node.js“ verwendet. Beispiele für andere Programmiersprachen finden Sie im Tutorial [Erstellen einer Clientanwendung und Verbinden der Anwendung mit Ihrer Azure IoT Central-Anwendung](tutorial-connect-device.md).

```javascript
client.onDeviceMethod('getMaxMinReport', commandHandler);

// ...

const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case 'getMaxMinReport': {
    console.log('MaxMinReport ' + request.payload);
    await sendCommandResponse(request, response, 200, deviceTemperatureSensor.getMaxMinReportObject());
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};

const sendCommandResponse = async (request, response, status, payload) => {
  try {
    await response.send(status, payload);
    console.log('Response to method \'' + request.methodName +
              '\' sent successfully.' );
  } catch (err) {
    console.error('An error ocurred when sending a method response:\n' +
              err.toString());
  }
};
```

Mit dem Aufruf von `onDeviceMethod` wird die `commandHandler`-Methode eingerichtet. Dieser Befehlshandler führt Folgendes aus:

1. Er überprüft den Namen des Befehls.
1. Er ruft `getMaxMinReportObject` für den Befehl `getMaxMinReport` auf, um die Werte abzurufen, die in das Rückgabeobjekt eingefügt werden sollen.
1. Er ruft `sendCommandResponse` auf, um die Antwort zurück an IoT Central zu senden. Diese Antwort enthält den Antwortcode `200`, der einen Erfolg anzeigt.

Der folgende Screenshot zeigt, wie die Antwort bei einer erfolgreichen Befehlsausführung auf der Benutzeroberfläche von IoT Central angezeigt wird:

:::image type="content" source="media/howto-use-commands/simple-command-ui.png" alt-text="Screenshot der Anzeige von Befehlsnutzdaten für einen Standardbefehl":::

## <a name="long-running-commands"></a>Befehle mit langer Ausführungsdauer

In diesem Abschnitt wird gezeigt, wie ein Gerät das Senden einer Bestätigung zum Befehlsabschluss verzögern kann.

Der folgende Codeausschnitt zeigt, wie ein Gerät einen zeitintensiven Befehl implementieren kann:

> [!NOTE]
> In diesem Artikel wird der Einfachheit halber „Node.js“ verwendet. Beispiele für andere Programmiersprachen finden Sie im Tutorial [Erstellen einer Clientanwendung und Verbinden der Anwendung mit Ihrer Azure IoT Central-Anwendung](tutorial-connect-device.md).

```javascript
client.onDeviceMethod('rundiagnostics', commandHandler);

// ...

const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case 'rundiagnostics': {
    console.log('Starting long-running diagnostics run ' + request.payload);
    await sendCommandResponse(request, response, 202, 'Diagnostics run started');

    // Long-running operation here
    // ...

    const patch = {
      rundiagnostics: {
        value: 'Diagnostics run complete at ' + new Date().toLocaleString()
      }
    };

    deviceTwin.properties.reported.update(patch, function (err) {
      if (err) throw err;
      console.log('Properties have been reported for component');
    });
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};
```

Mit dem Aufruf von `onDeviceMethod` wird die `commandHandler`-Methode eingerichtet. Dieser Befehlshandler führt Folgendes aus:

1. Er überprüft den Namen des Befehls.
1. Er ruft `sendCommandResponse` auf, um die Antwort zurück an IoT Central zu senden. Diese Antwort enthält den Antwortcode `202`, der ausstehende Ergebnisse anzeigt.
1. Er schließt den zeitintensiven Vorgang ab.
1. Er informiert IoT Central mit einer gemeldeten Eigenschaft, die denselben Namen wie der Befehl hat, dass der Befehl abgeschlossen wurde.

Der folgende Screenshot zeigt, wie die Antwort eines Befehls mit dem Antwortcode 202 auf der Benutzeroberfläche von IoT Central angezeigt wird:

:::image type="content" source="media/howto-use-commands/long-running-start.png" alt-text="Screenshot mit sofortiger Antwort vom Gerät":::

Der folgende Screenshot zeigt die Benutzeroberfläche von IoT Central, wenn der Dienst als Update der Eigenschaft die Angabe empfängt, dass der Befehl abgeschlossen wurde:

:::image type="content" source="media/howto-use-commands/long-running-finish.png" alt-text="Screenshot des Abschlusses eines zeitintensiven Befehls":::

## <a name="offline-commands"></a>Offlinebefehle

In diesem Abschnitt wird gezeigt, wie Geräte Offlinebefehle verarbeiten. Wenn ein Gerät online ist, kann es einen Offlinebefehl direkt beim Empfang verarbeiten. Ist das Gerät jedoch offline, verarbeitet es den Offlinebefehl erst, wenn es das nächste Mal eine Verbindung mit IoT Central herstellt. Geräte können keinen Rückgabewert als Antwort auf einen Offlinebefehl senden.

> [!NOTE]
> In diesem Artikel wird der Einfachheit halber „Node.js“ verwendet.

Der folgende Screenshot zeigt den Offlinebefehl **GenerateDiagnostics**. Der Anforderungsparameter ist ein Objekt mit der DateTime-Eigenschaft **StartTime** und der ganzzahligen Enumerationseigenschaft **Bank**:

:::image type="content" source="media/howto-use-commands/offline-command.png" alt-text="Screenshot der Benutzeroberfläche bei einem Offlinebefehl":::

Der folgende Codeausschnitt zeigt, wie ein Client auf Offlinebefehle lauschen und den Nachrichteninhalt anzeigen kann:

```javascript
client.on('message', function (msg) {
  console.log('Body: ' + msg.data);
  console.log('Properties: ' + JSON.stringify(msg.properties));
  client.complete(msg, function (err) {
    if (err) {
      console.error('complete error: ' + err.toString());
    } else {
      console.log('complete sent');
    }
  });
});
```

Die Ausgabe vom vorherigen Codeausschnitt zeigt die Nutzdaten mit den Werten für **StartTime** und **Bank**. Die Eigenschaftenliste enthält den Befehlsnamen im Listenelement **method-name**:

```output
Body: {"StartTime":"2021-01-06T06:00:00.000Z","Bank":2}
Properties: {"propertyList":[{"key":"iothub-ack","value":"none"},{"key":"method-name","value":"GenerateDiagnostics"}]}
```

> [!NOTE]
> Die standardmäßige Gültigkeitsdauer für Offlinebefehle beträgt 24 Stunden. Danach läuft die Nachricht ab.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie gelernt haben, wie Sie Befehle in Azure IoT Central-Anwendungen verwenden, finden Sie unter [Nutzdaten](concepts-telemetry-properties-commands.md) weitere Informationen zu Befehlsparametern und unter [Erstellen einer Clientanwendung und Verbinden dieser Anwendung mit Ihrer Azure IoT Central-Anwendung](tutorial-connect-device.md) umfassende Codebeispiele in verschiedenen Programmiersprachen.
