---
title: Geräteentwicklung für Azure IoT Central | Microsoft-Dokumentation
description: Azure IoT Central ist eine IoT-Anwendungsplattform, die die Erstellung von IoT-Lösungen vereinfacht. Dieser Artikel enthält eine Übersicht über die Entwicklung von Geräten zum Herstellen einer Verbindung mit Ihrer IoT Central-Anwendung. Von Geräten werden Telemetriedaten zum Senden von Streamingdaten und Eigenschaften verwendet, um den Gerätestatus zu melden. Mit IoT Central kann der Gerätestatus festgelegt werden, indem schreibbare Eigenschaften und Aufrufbefehle auf einem Gerät verwendet werden.
author: dominicbetts
ms.author: dobett
ms.date: 08/30/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- mvc
- device-developer
ms.openlocfilehash: f2131ec5a0b939172097494dcd457b9d661614ad
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/04/2021
ms.locfileid: "123473494"
---
# <a name="iot-central-device-development-guide"></a>Entwicklungshandbuch für IoT Central-Geräte

Mit einer IoT Central-Anwendung können Sie Millionen von Geräten während ihres gesamten Lebenszyklus überwachen und verwalten. Dieses Handbuch ist für Geräteentwickler konzipiert, die Code zur Ausführung auf Geräten implementieren, die eine Verbindung mit IoT Central herstellen.

Geräte interagieren mit einer IoT Central-Anwendung mithilfe der folgenden Primitiven (Grundtypen):

- _Telemetrie_ sind Daten, die ein Gerät an IoT Central sendet. Beispiel: ein Datenstrom von Temperaturwerten aus einem integrierten Sensor.
- _Eigenschaften_ sind Statuswerte, die ein Gerät an IoT Central meldet. Beispiel: die aktuelle Firmwareversion des Geräts. Sie können auch schreibbare Eigenschaften verwenden, die IoT Central auf dem Gerät aktualisieren kann, z. B. die Zieltemperatur.
- _Befehle_ werden aus IoT Central aufgerufen, um das Verhalten eines Geräts zu steuern. So könnte Ihre IoT Central-Anwendung beispielsweise einen Befehl zum Neustarten eines Geräts aufrufen.

Ein Lösungsentwickler ist zuständig für die Konfiguration von Dashboards und Geräteansichten in der IoT Central-Webbenutzeroberfläche zum Visualisieren von Telemetriedaten, Verwalten von Eigenschaften und Aufrufen von Befehlen.

## <a name="types-of-device"></a>Gerätetypen

In den folgenden Abschnitten werden die wichtigsten Gerätetypen beschrieben, die Sie mit einer IoT Central-Anwendung verbinden können:

### <a name="iot-device"></a>IoT-Gerät

Ein IoT-Gerät ist ein eigenständiges Gerät und stellt eine direkte Verbindung mit IoT Central her. Ein IoT-Gerät sendet normalerweise Telemetriedaten aus seinen integrierten oder verbundenen Sensoren an Ihre IoT Central-Anwendung. Eigenständige Geräte können auch Eigenschaftswerte melden, schreibbare Eigenschaftswerte empfangen und auf Befehle reagieren.

### <a name="iot-edge-device"></a>IoT Edge-Gerät

Ein IoT Edge-Gerät stellt eine direkte Verbindung mit IoT Central her. Ein IoT Edge-Gerät kann seine eigenen Telemetriedaten senden, seine Eigenschaften melden sowie auf Aktualisierungen von schreibbaren Eigenschaften und Befehle reagieren. IoT Edge-Module können Daten lokal auf dem IoT Edge-Gerät verarbeiten. Ein IoT Edge-Gerät kann auch als Vermittler für andere Geräte fungieren, die als Blattgeräte bezeichnet werden. IoT Edge-Geräte werden u. a. in den folgenden Szenarien verwendet:

- Aggregieren oder filtern Sie Telemetriedaten, bevor sie an IoT Central gesendet werden. Mithilfe dieses Ansatzes können Sie die Kosten für das Senden von Daten an IoT Central senken.
- Ermöglichen Sie es Geräten, die keine direkte Verbindung mit IoT Central herstellen können, dies über das IoT Edge-Gerät zu erledigen. So könnte ein Blattgerät beispielsweise über Bluetooth eine Verbindung mit dem IoT Edge-Gerät herstellen, das dann über das Internet eine Verbindung mit IoT Central herstellt.
- Steuern Sie Blattgeräte lokal, um die Latenz beim Herstellen einer Verbindung mit IoT Central über das Internet zu vermeiden.

IoT Central „sieht“ nur das IoT Edge-Gerät, nicht die mit ihm verbundenen Blattgeräte.

Weitere Informationen finden Sie unter [Hinzufügen eines Azure IoT Edge-Geräts zu Ihrer Azure IoT Central-Anwendung](./tutorial-add-edge-as-leaf-device.md).

### <a name="gateways"></a>Gateways

Ein Gatewaygerät verwaltet ein oder mehrere nachgeschaltete Geräte, die eine Verbindung mit ihrer IoT Central-Anwendung herstellen. Sie verwenden IoT Central zum Konfigurieren der Beziehungen zwischen den nachgeschalteten Geräten und dem Gatewaygerät. Sowohl IoT-Geräte als auch IoT Edge-Geräte können als Gateways fungieren. Weitere Informationen finden Sie unter [Definieren eines neuen IoT-Gatewaygerätetyps in Ihrer Azure IoT Central-Anwendung](./tutorial-define-gateway-device-type.md).

## <a name="connect-a-device"></a>Verbinden eines Geräts

Für Azure IoT Central wird der [Azure IoT Hub Device Provisioning Service (DPS)](../../iot-dps/about-iot-dps.md) zur Verwaltung aller Geräteregistrierungen und -verbindungen verwendet.

DPS ermöglicht Folgendes:

- IoT Central kann Onboarding und das Verbinden von Geräten nach Maß unterstützen.
- Sie können jetzt Geräteanmeldeinformationen generieren und die Geräte offline konfigurieren, ohne sie über IoT Central zu registrieren.
- Sie können Ihre eigenen Geräte-IDs verwenden, um Geräte in IoT Central zu registrieren. Die Verwendung Ihrer eigenen Geräte-IDs vereinfacht die Integration in vorhandene Backofficesysteme.
- Eine einheitliche Möglichkeit, Geräte mit IoT Central zu verbinden.

Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit Azure IoT Central](./concepts-get-connected.md) und [Bewährte Methoden](concepts-best-practices.md).

### <a name="security"></a>Sicherheit

Die Verbindung zwischen einem Gerät und Ihrer IoT Central-Anwendung wird entweder mithilfe von [Shared Access Signatures](./concepts-get-connected.md#sas-group-enrollment) (SAS) oder [X.509-Zertifikaten](./concepts-get-connected.md#x509-group-enrollment) nach Branchenstandard gesichert.

### <a name="communication-protocols"></a>Kommunikationsprotokolle

Kommunikationsprotokolle, über die ein Gerät eine Verbindung mit IoT Central herstellen kann, sind MQTT, AMQP und HTTPS. IoT Central verwendet intern einen IoT-Hub, um Gerätekonnektivität ermöglichen. Weitere Informationen zu den Kommunikationsprotokollen, die IoT Hub für die Gerätekonnektivität unterstützt, finden Sie unter [Auswählen eines Kommunikationsprotokolls](../../iot-hub/iot-hub-devguide-protocols.md).

## <a name="implement-the-device"></a>Implementieren des Geräts

Eine IoT Central-Gerätevorlage enthält ein _Modell_, mit dem die Verhaltensweisen angegeben werden, die von einem Gerät des entsprechenden Typs implementiert werden sollen. Diese Verhaltensweisen betreffen die Bereiche Telemetrie, Eigenschaften und Befehle.

Weitere Informationen zu bewährten Methoden zum Bearbeiten eines Modells finden Sie unter [Bearbeiten einer vorhandenen Gerätevorlage](howto-edit-device-template.md).

> [!TIP]
> Sie können das Modell als JSON-Datei im Format [Digital Twins Definition Language (DTDL) v2](https://github.com/Azure/opendigitaltwins-dtdl) aus IoT Central exportieren.

Jedes Modell verfügt über einen eindeutigen _Gerätezwilling-Modellbezeichner_ (Device Twin Model Identifier, DTMI), z. B. `dtmi:com:example:Thermostat;1`. Wenn ein Gerät eine Verbindung mit IoT Central herstellt, sendet es den Gerätezwilling-Modellbezeichner des implementierten Modells. IoT Central kann dem Gerät dann die richtige Gerätevorlage zuordnen.

Bei [IoT Plug & Play](../../iot-develop/overview-iot-plug-and-play.md) wird eine Reihe von [Konventionen](../../iot-develop/concepts-convention.md) definiert, die von einem Gerät eingehalten werden müssen, wenn es ein DTDL-Modell implementiert.

Die [Azure IoT-Geräte-SDKs](#languages-and-sdks) verfügen auch über Unterstützung für die IoT Plug & Play-Konventionen.

### <a name="device-model"></a>Gerätemodell

Ein Gerätemodell wird per [DTDL](https://github.com/Azure/opendigitaltwins-dtdl) definiert. Mit dieser Sprache können Sie Folgendes definieren:

- Die vom Gerät gesendeten Telemetriedaten. Die Definition umfasst den Namen und den Datentyp der Telemetriedaten. Beispiel: Ein Gerät sendet die Telemetriedaten für die Temperatur als Wert vom Typ „double“.
- Die Eigenschaften, die vom Gerät an IoT Central gemeldet werden. Eine Eigenschaftsdefinition umfasst den Namen und den Datentyp. Beispiel: Ein Gerät meldet den Zustand eines Ventils als booleschen Wert.
- Die Eigenschaften, die das Gerät von IoT Central empfangen kann. Optional können Sie eine Eigenschaft als schreibbar markieren. Beispiel: Von IoT Central wird eine Zieltemperatur als Wert vom Typ „double“ an ein Gerät gesendet.
- Die Befehle, auf die ein Gerät reagiert. Die Definition enthält den Namen des Befehls sowie die Namen und Datentypen beliebiger Parameter. Beispiel: Ein Gerät reagiert auf einen Neustartbefehl, mit dem angegeben wird, wie viele Sekunden vor dem Neustarten gewartet werden soll.

Bei einem DTDL-Modell kann es sich um ein Modell _ohne Komponenten_ oder _mit mehreren Komponenten_ handeln:

- Modell ohne Komponenten: Ein einfaches Modell verwendet keine eingebetteten oder weitergegebenen Komponenten. Alle Telemetriedaten, Eigenschaften und Befehle werden als einzelne _Stammkomponente_ definiert. Ein Beispiel hierfür finden Sie unter dem [Thermostat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)-Modell.
- Modell mit mehreren Komponenten: Ein komplexeres Modell, das mindestens zwei Komponenten umfasst. Zu diesen Komponenten gehören eine einzelne Stammkomponente und mindestens eine geschachtelte Komponente. Ein Beispiel hierfür finden Sie unter dem [Temperature Controller](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)-Modell.

Weitere Informationen finden Sie im [Modellhandbuch für IoT Plug & Play](../../iot-develop/concepts-modeling-guide.md).

### <a name="conventions"></a>Konventionen

Ein Gerät sollte die IoT Plug & Play-Konventionen einhalten, wenn es Daten mit IoT Central austauscht. Die Konventionen umfassen Folgendes:

- Senden des Gerätezwilling-Modellbezeichners bei der Verbindungsherstellung mit IoT Central
- Senden Sie von richtig formatierten JSON-Nutzlasten und -Metadaten an IoT Central
- Korrektes Reagieren auf schreibbare Eigenschaften und Befehle von IoT Central
- Einhalten der Namenskonventionen für Komponentenbefehle

> [!NOTE]
> Derzeit werden die DTDL-Datentypen **Array** und **Geospatial** von IoT Central nicht vollständig unterstützt.

Weitere Informationen zum Format der JSON-Nachrichten, die ein Gerät mit IoT Central austauscht, finden Sie unter [Telemetrie-, Eigenschaften- und Befehlsnutzlasten](concepts-telemetry-properties-commands.md).

Weitere Informationen zu den IoT Plug & Play-Konventionen finden Sie unter [IoT Plug & Play-Konventionen](../../iot-develop/concepts-convention.md).

### <a name="device-sdks"></a>Geräte-SDKs

Mithilfe eines der [Azure IoT-Geräte-SDKs](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) können Sie das Verhalten Ihres Geräts implementieren. Der Code sollte folgende Aufgaben erfüllen:

- Das Gerät mit DPS registrieren und mithilfe der Informationen von DPS eine Verbindung mit dem internen IoT-Hub in Ihrer IoT Central-Anwendung herstellen.
- Ankündigen des Gerätezwilling-Modellbezeichners des vom Gerät implementierten Modells.
- Senden von Telemetriedaten in dem Format, das vom Gerätemodell angegeben wird. Von IoT Central wird das Modell in der Gerätevorlage genutzt, um zu ermitteln, wie die Telemetriedaten für Visualisierungen und Analysen verwendet werden sollen.
- Synchronisieren von Eigenschaftswerten zwischen dem Gerät und IoT Central. Das Modell gibt die Eigenschaftsnamen und Datentypen an, damit IoT Central die Informationen anzeigen kann.
- Implementieren von Befehlshandlern für die im Modell angegebenen Befehle. Das Modell gibt die Befehlsnamen und Parameter an, die das Gerät verwenden sollte.

Weitere Informationen zur Aufgabe von Gerätevorlagen finden Sie unter [Was sind Gerätevorlagen?](./concepts-device-templates.md).

Beispielcode finden Sie unter [Tutorial: Erstellen einer Clientanwendung und Verbinden dieser Anwendung mit Ihrer Azure IoT Central-Anwendung](./tutorial-connect-device.md).

### <a name="languages-and-sdks"></a>Sprachen und SDKs

Weitere Informationen zu den unterstützten Sprachen und SDKs finden Sie unter [Understand and use Azure IoT Hub device SDKs](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) (Verstehen und Verwenden von Azure IoT Hub-Geräte-SDKs).

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie Geräteentwickler sind und sich näher mit dem Code beschäftigen möchten, wird als nächster Schritt das [Erstellen und Verbinden einer Clientanwendung mit Ihrer Azure IoT Central-Anwendung](./tutorial-connect-device.md) empfohlen.

Wenn Sie mehr zur Verwendung von IoT Central erfahren möchten, sollten Sie als Nächstes die Schnellstarts ausprobieren, beginnend mit dem [Erstellen einer Azure IoT Central-Anwendung](./quick-deploy-iot-central.md).
