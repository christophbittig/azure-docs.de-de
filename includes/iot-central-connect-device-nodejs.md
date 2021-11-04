---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 03/31/2021
ms.openlocfilehash: 319567fba40d2412c882685b8d7d3b916b699968
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131521385"
---
[![Code durchsuchen](../articles/iot-central/core/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/javascript)

## <a name="prerequisites"></a>Voraussetzungen

Zum Ausführen der Schritte in diesem Artikel benötigen Sie folgende Ressourcen:

[!INCLUDE [iot-central-prerequisites-basic](iot-central-prerequisites-basic.md)]

- Einen Entwicklungscomputer, auf dem mindestens Version 6 von [Node.js](https://nodejs.org/) installiert ist. Sie können `node --version` in der Befehlszeile ausführen, um Ihre Version zu überprüfen. Bei den Anweisungen in diesem Tutorial wird davon ausgegangen, dass Sie den Befehl **node** an der Windows-Eingabeaufforderung ausführen. Node.js kann jedoch unter einer Reihe anderer Betriebssysteme verwendet werden.

- Eine lokale Kopie des GitHub-Repositorys mit dem [Microsoft Azure IoT SDK für Node.js](https://github.com/Azure/azure-iot-sdk-node), das den Beispielcode enthält. Laden Sie über den folgenden Link eine Kopie des Repositorys herunter: [ZIP herunterladen](https://github.com/Azure/azure-iot-sdk-node/archive/master.zip). Entzippen Sie anschließend die Datei an einem geeigneten Speicherort auf Ihrem lokalen Computer.

## <a name="review-the-code"></a>Überprüfen des Codes

Öffnen Sie in der Kopie des zuvor heruntergeladenen Microsoft Azure IoT SDK für Node.js die Datei *azure-iot-sdk-node/device/samples/javascript/pnp/pnpTemperatureController.js* in einem Text-Editor.

Wenn Sie das Beispiel ausführen, um eine Verbindung mit IoT Central herzustellen, wird Device Provisioning Service (DPS) zum Registrieren des Geräts und zum Generieren einer Verbindungszeichenfolge verwendet. Das Beispiel ruft die erforderlichen DPS-Verbindungsinformationen aus der Befehlszeilenumgebung ab.

Die `main`-Methode:

* Erstellen eines Objekts vom Typ `client` und Festlegen der Modell-ID `dtmi:com:example:TemperatureController;2` vor dem Öffnen der Verbindung IoT Central verwendet die Modell-ID zum Identifizieren oder Generieren der Gerätevorlage für dieses Gerät. Weitere Informationen finden Sie unter [Zuordnen eines Geräts zu einer Gerätevorlage](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template).
* Erstellen von Befehlshandlern für drei Befehle
* Starten einer Schleife für jede Thermostatkomponente, um alle fünf Sekunden Temperaturtelemetriedaten zu senden
* Starten einer Schleife für die Standardkomponente, um alle sechs Sekunden Telemetriedaten zur Arbeitssatzgröße zu senden
* Senden der Eigenschaft `maxTempSinceLastReboot` für jede Thermostatkomponente
* Senden der Geräteinformationseigenschaften
* Erstellen schreibbarer Eigenschaftenhandler für die drei Komponenten

```javascript
async function main() {
  // ...

  // fromConnectionString must specify a transport, coming from any transport package.
  const client = Client.fromConnectionString(deviceConnectionString, Protocol);
  console.log('Connecting using connection string: ' + deviceConnectionString);
  let resultTwin;

  try {
    // Add the modelId here
    await client.setOptions(modelIdObject);
    await client.open();
    console.log('Enabling the commands on the client');
    client.onDeviceMethod(commandNameGetMaxMinReport1, commandHandler);
    client.onDeviceMethod(commandNameGetMaxMinReport2, commandHandler);
    client.onDeviceMethod(commandNameReboot, commandHandler);

    // Send Telemetry after some interval
    let index1 = 0;
    let index2 = 0;
    let index3 = 0;
    intervalToken1 = setInterval(() => {
      const data = JSON.stringify(thermostat1.updateSensor().getCurrentTemperatureObject());
      sendTelemetry(client, data, index1, thermostat1ComponentName).catch((err) => console.log('error ', err.toString()));
      index1 += 1;
    }, 5000);

    intervalToken2 = setInterval(() => {
      const data = JSON.stringify(thermostat2.updateSensor().getCurrentTemperatureObject());
      sendTelemetry(client, data, index2, thermostat2ComponentName).catch((err) => console.log('error ', err.toString()));
      index2 += 1;
    }, 5500);


    intervalToken3 = setInterval(() => {
      const data = JSON.stringify({ workingset: 1 + (Math.random() * 90) });
      sendTelemetry(client, data, index3, null).catch((err) => console.log('error ', err.toString()));
      index3 += 1;
    }, 6000);

    // attach a standard input exit listener
    exitListener(client);

    try {
      resultTwin = await client.getTwin();
      // Only report readable properties
      const patchRoot = helperCreateReportedPropertiesPatch({ serialNumber: serialNumber }, null);
      const patchThermostat1Info = helperCreateReportedPropertiesPatch({
        maxTempSinceLastReboot: thermostat1.getMaxTemperatureValue(),
      }, thermostat1ComponentName);

      const patchThermostat2Info = helperCreateReportedPropertiesPatch({
        maxTempSinceLastReboot: thermostat2.getMaxTemperatureValue(),
      }, thermostat2ComponentName);

      const patchDeviceInfo = helperCreateReportedPropertiesPatch({
        manufacturer: 'Contoso Device Corporation',
        model: 'Contoso 47-turbo',
        swVersion: '10.89',
        osName: 'Contoso_OS',
        processorArchitecture: 'Contoso_x86',
        processorManufacturer: 'Contoso Industries',
        totalStorage: 65000,
        totalMemory: 640,
      }, deviceInfoComponentName);

      // the below things can only happen once the twin is there
      updateComponentReportedProperties(resultTwin, patchRoot, null);
      updateComponentReportedProperties(resultTwin, patchThermostat1Info, thermostat1ComponentName);
      updateComponentReportedProperties(resultTwin, patchThermostat2Info, thermostat2ComponentName);
      updateComponentReportedProperties(resultTwin, patchDeviceInfo, deviceInfoComponentName);
      desiredPropertyPatchListener(resultTwin, [thermostat1ComponentName, thermostat2ComponentName, deviceInfoComponentName]);
    } catch (err) {
      console.error('could not retrieve twin or report twin properties\n' + err.toString());
    }
  } catch (err) {
    console.error('could not connect Plug and Play client or could not attach interval function for telemetry\n' + err.toString());
  }
}
```

Die Funktion `provisionDevice` zeigt, wie das Gerät mithilfe von DPS die Registrierung bei IoT Central durchführt und eine Verbindung mit IoT Central herstellt. Die Nutzlast enthält die Modell-ID, die IoT Central zum [Zuordnen des Geräts zu einer Gerätevorlage](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template) verwendet:

```javascript
async function provisionDevice(payload) {
  var provSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
  var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvProtocol(), provSecurityClient);

  if (!!(payload)) {
    provisioningClient.setProvisioningPayload(payload);
  }

  try {
    let result = await provisioningClient.register();
    deviceConnectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
    console.log('registration succeeded');
    console.log('assigned hub=' + result.assignedHub);
    console.log('deviceId=' + result.deviceId);
    console.log('payload=' + JSON.stringify(result.payload));
  } catch (err) {
    console.error("error registering device: " + err.toString());
  }
}
```

Die Funktion `sendTelemetry` zeigt, wie das Gerät die Temperaturtelemetriedaten an IoT Central sendet: Für Telemetriedaten von Komponenten wird eine Eigenschaft namens `$.sub` mit dem Komponentennamen hinzugefügt:

```javascript
async function sendTelemetry(deviceClient, data, index, componentName) {
  if (!!(componentName)) {
    console.log('Sending telemetry message %d from component: %s ', index, componentName);
  } else {
    console.log('Sending telemetry message %d from root interface', index);
  }
  const msg = new Message(data);
  if (!!(componentName)) {
    msg.properties.add(messageSubjectProperty, componentName);
  }
  msg.contentType = 'application/json';
  msg.contentEncoding = 'utf-8';
  await deviceClient.sendEvent(msg);
}
```

Die `main`-Methode verwendet eine Hilfsmethode namens `helperCreateReportedPropertiesPatch`, um Nachrichten zu Eigenschaftsaktualisierungen zu erstellen. Diese Methode verwendet einen optionalen Parameter, um die Komponente anzugeben, die die Eigenschaft sendet:

```javascript
const helperCreateReportedPropertiesPatch = (propertiesToReport, componentName) => {
  let patch;
  if (!!(componentName)) {
    patch = { };
    propertiesToReport.__t = 'c';
    patch[componentName] = propertiesToReport;
  } else {
    patch = { };
    patch = propertiesToReport;
  }
  if (!!(componentName)) {
    console.log('The following properties will be updated for component: ' + componentName);
  } else {
    console.log('The following properties will be updated for root interface.');
  }
  console.log(patch);
  return patch;
};
```

Die `main`-Methode nutzt die folgende Methode, um Aktualisierungen der schreibbaren Eigenschaften aus IoT Central zu behandeln. Beachten Sie, wie die Methode die Antwort mit der Version und dem Statuscode erstellt:

```javascript
const desiredPropertyPatchListener = (deviceTwin, componentNames) => {
  deviceTwin.on('properties.desired', (delta) => {
    console.log('Received an update for device with value: ' + JSON.stringify(delta));
    Object.entries(delta).forEach(([key, values]) => {
      const version = delta.$version;
      if (!!(componentNames) && componentNames.includes(key)) { // then it is a component we are expecting
        const componentName = key;
        const patchForComponents = { [componentName]: {} };
        Object.entries(values).forEach(([propertyName, propertyValue]) => {
          if (propertyName !== '__t' && propertyName !== '$version') {
            console.log('Will update property: ' + propertyName + ' to value: ' + propertyValue + ' of component: ' + componentName);
            const propertyContent = { value: propertyValue };
            propertyContent.ac = 200;
            propertyContent.ad = 'Successfully executed patch';
            propertyContent.av = version;
            patchForComponents[componentName][propertyName] = propertyContent;
          }
        });
        updateComponentReportedProperties(deviceTwin, patchForComponents, componentName);
      }
      else if  (key !== '$version') { // individual property for root
        const patchForRoot = { };
        console.log('Will update property: ' + key + ' to value: ' + values + ' for root');
        const propertyContent = { value: values };
        propertyContent.ac = 200;
        propertyContent.ad = 'Successfully executed patch';
        propertyContent.av = version;
        patchForRoot[key] = propertyContent;
        updateComponentReportedProperties(deviceTwin, patchForRoot, null);
      }
    });
  });
};
```

Die `main`-Methode nutzt die folgenden Methoden, um Befehle aus IoT Central zu behandeln:

```javascript
const commandHandler = async (request, response) => {
  helperLogCommandRequest(request);
  switch (request.methodName) {
  case commandNameGetMaxMinReport1: {
    await sendCommandResponse(request, response, 200, thermostat1.getMaxMinReportObject());
    break;
  }
  case commandNameGetMaxMinReport2: {
    await sendCommandResponse(request, response, 200, thermostat2.getMaxMinReportObject());
    break;
  }
  case commandNameReboot: {
    await sendCommandResponse(request, response, 200, 'reboot response');
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
    console.log('Response to method: ' + request.methodName + ' sent successfully.' );
  } catch (err) {
    console.error('An error ocurred when sending a method response:\n' + err.toString());
  }
};
```

## <a name="get-connection-information"></a>Abrufen von Verbindungsinformationen

[!INCLUDE [iot-central-connection-configuration](iot-central-connection-configuration.md)]

## <a name="run-the-code"></a>Ausführen des Codes

Öffnen Sie zum Ausführen der Beispielanwendung eine Befehlszeilenumgebung, und navigieren Sie zum Ordner *azure-iot-sdk-node/device/samples/pnp*, der die Beispieldatei *pnpTemperatureController.js* enthält.

[!INCLUDE [iot-central-connection-environment](iot-central-connection-environment.md)]

Installieren Sie die erforderlichen Pakete:

```cmd/sh
npm install
```

Führen Sie das Beispiel aus:

```cmd/sh
node pnpTemperatureController.js
```

Die folgende Ausgabe zeigt, wie das Gerät registriert und eine Verbindung mit IoT Central hergestellt wird. In dem Beispiel wird dann die Eigenschaft `maxTempSinceLastReboot` aus den beiden Thermostatkomponenten gesendet, bevor mit dem Senden von Telemetriedaten begonnen wird:

```output
registration succeeded
assigned hub=iotc-....azure-devices.net
deviceId=sample-device-01
payload=undefined
Connecting using connection string: HostName=iotc-....azure-devices.net;DeviceId=sample-device-01;SharedAccessKey=qdv...IpAo=
Enabling the commands on the client
Please enter q or Q to exit sample.
The following properties will be updated for root interface.
{ serialNumber: 'alwinexlepaho8329' }
The following properties will be updated for component: thermostat1
{ thermostat1: { maxTempSinceLastReboot: 1.5902294191855972, __t: 'c' } }
The following properties will be updated for component: thermostat2
{ thermostat2: { maxTempSinceLastReboot: 16.181771928614545, __t: 'c' } }
The following properties will be updated for component: deviceInformation
{ deviceInformation:
   { manufacturer: 'Contoso Device Corporation',
     model: 'Contoso 47-turbo',
     swVersion: '10.89',
     osName: 'Contoso_OS',
     processorArchitecture: 'Contoso_x86',
     processorManufacturer: 'Contoso Industries',
     totalStorage: 65000,
     totalMemory: 640,
     __t: 'c' } }
executed sample
Received an update for device with value: {"$version":1}
Properties have been reported for component: thermostat1
Properties have been reported for component: thermostat2
Properties have been reported for component: deviceInformation
Properties have been reported for root interface.
Sending telemetry message 0 from component: thermostat1 
Sending telemetry message 0 from component: thermostat2 
Sending telemetry message 0 from root interface
```

[!INCLUDE [iot-central-monitor-thermostat](iot-central-monitor-thermostat.md)]

Sie können sehen, wie das Gerät auf Befehle und Eigenschaftsaktualisierungen reagiert. Der Befehl `getMaxMinReport` befindet sich in der Komponente `thermostat2`, der Befehl `reboot` ist in der Standardkomponente enthalten. Die schreibbare Eigenschaft `targetTemperature` wurde für die Komponente „thermostat2“ festgelegt:

```output
Received command request for command name: thermostat2*getMaxMinReport
The command request payload is:
2021-03-26T06:00:00.000Z
Response to method: thermostat2*getMaxMinReport sent successfully.

...

Received command request for command name: reboot
The command request payload is:
10
Response to method: reboot sent successfully.

...

Received an update for device with value: {"thermostat2":{"targetTemperature":76,"__t":"c"},"$version":2}
Will update property: targetTemperature to value: 76 of component: thermostat2
Properties have been reported for component: thermostat2
```
