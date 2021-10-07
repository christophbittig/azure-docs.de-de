---
title: Verbinden des Raspberry Pi-Websimulators mit Azure IoT Hub (Node.js)
description: Verbinden Sie den Raspberry Pi-Websimulator mit Azure IoT Hub, damit Raspberry Pi Daten an die Azure-Cloud sendet.
author: wesmc7777
keywords: Raspberry Pi-Simulator, Azure IoT Raspberry Pi, Raspberry Pi IoT Hub, Raspberry Pi sendet Daten an Cloud, Raspberry Pi in der Cloud
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 05/27/2021
ms.author: wesmc
ms.custom:
- 'Role: Cloud Development'
- devx-track-js
ms.openlocfilehash: 72708e33aa4d3677bb6c2e424f56eb4d81a29f04
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128587964"
---
# <a name="connect-raspberry-pi-online-simulator-to-azure-iot-hub-nodejs"></a>Verbinden des Raspberry Pi-Onlinesimulators mit Azure IoT Hub (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

In diesem Tutorial erlernen Sie die Grundlagen der Verwendung des Raspberry Pi-Onlinesimulators. Anschließend erfahren Sie, wie Sie den Pi-Simulator mithilfe von [Azure IoT Hub](about-iot-hub.md) nahtlos mit der Cloud verbinden.

:::image type="content" source="media/iot-hub-raspberry-pi-web-simulator/3-banner.png" alt-text="Verbinden des Raspberry Pi-Websimulators mit Azure IoT Hub " border="false":::

[:::image type="content" source="media/iot-hub-raspberry-pi-web-simulator/6-button-default.png" alt-text="Starten des Raspberry Pi-Simulators":::](https://azure-samples.github.io/raspberry-pi-web-simulator/#getstarted)


Wenn Sie physische Geräte haben, finden Sie Informationen zu ersten Schritten unter [Verbinden von Raspberry Pi mit Azure IoT Hub](iot-hub-raspberry-pi-kit-node-get-started.md).

## <a name="what-you-do"></a>Aufgaben

* Erfahren Sie mehr zu den Grundlagen des Raspberry Pi-Onlinesimulators.

* Erstellen Sie einen IoT Hub.

* Registrieren Sie ein Gerät für Pi in Ihrem IoT Hub.

* Führen Sie eine Beispielanwendung in Pi aus, um simulierte Sensordaten an Ihren IoT Hub zu senden.

Verbinden Sie simuliertes Raspberry Pi mit einem von Ihnen erstellten IoT Hub. Führen Sie dann eine Beispielanwendung mit dem Simulator aus, um Sensordaten zu generieren. Senden Sie abschließend die Sensordaten an Ihren IoT Hub.

## <a name="what-you-learn"></a>Lerninhalt

* Erstellen eines Azure IoT Hubs und Abrufen der Verbindungszeichenfolge für Ihr neues Gerät Wenn Sie kein Azure-Konto besitzen, können Sie in nur wenigen Minuten ein [kostenloses Azure-Testkonto](https://azure.microsoft.com/free/) erstellen.

* Arbeiten mit dem Raspberry Pi-Onlinesimulator

* Senden von Sensordaten an Ihren IoT Hub

## <a name="overview-of-raspberry-pi-web-simulator"></a>Übersicht über den Raspberry Pi-Websimulator

Klicken Sie auf die Schaltfläche, um den Raspberry Pi-Onlinesimulator zu starten.

> [!div class="button"]
> <a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#GetStarted" target="_blank">Raspberry Pi-Simulator starten</a>

Im Websimulator werden drei Bereiche angezeigt.

1. Assembly-Bereich: Im Standardverfahren stellt Pi eine Verbindung mit einem BME280-Sensor und einer LED her. Der Bereich ist in der Vorschauversion gesperrt, daher sind zurzeit keine Anpassungen möglich.

2. Codingbereich: Ein Online-Code-Editor zum Codieren mit Raspberry Pi. Mit der Standard-Beispielanwendung können Sie Sensordaten von einem BME280-Sensor erfassen und diese an Azure IoT Hub senden. Die Anwendung ist vollständig mit echten Pi-Geräten kompatibel. 

3. Integriertes Konsolenfenster: Hier wird die Ausgabe des Codes angezeigt. Oben im Fenster werden drei Schaltflächen angezeigt.

   * **Ausführen:** Hiermit wird die Anwendung im Codingbereich ausgeführt.

   * **Zurücksetzen:** Hiermit wird der Codingbereich auf die Standard-Beispielanwendung zurückgesetzt.

   * **Minimieren/Maximieren:** Auf der rechten Seite befindet sich eine Schaltfläche, mit der Sie das Konsolenfenster minimieren/maximieren können.

> [!NOTE]
> Der Raspberry Pi-Websimulator ist jetzt als Vorschauversion verfügbar. Wir freuen uns über Ihr Feedback im [Gitter-Chatroom](https://gitter.im/Microsoft/raspberry-pi-web-simulator). Der Quellcode steht auf [GitHub](https://github.com/Azure-Samples/raspberry-pi-web-simulator) öffentlich zur Verfügung.

![Übersicht über den Pi-Onlinesimulator](media/iot-hub-raspberry-pi-web-simulator/0-overview.png)

## <a name="create-an-iot-hub"></a>Erstellen eines IoT-Hubs

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrieren eines neuen Geräts beim IoT-Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="run-a-sample-application-on-pi-web-simulator"></a>Ausführen einer Beispielanwendung im Pi-Websimulator

1. Stellen Sie im Codingbereich sicher, dass Sie mit der Standard-Beispielanwendung arbeiten. Ersetzen Sie den Platzhalter in Zeile 15 durch die Verbindungszeichenfolge für das Azure IoT Hub-Gerät.
1. 
   ![Ersetzen der Verbindungszeichenfolge für das Gerät](media/iot-hub-raspberry-pi-web-simulator/1-connectionstring.png)

2. Wählen Sie **Ausführen** aus, oder geben Sie `npm start` ein, um die Anwendung auszuführen.

Die folgende Ausgabe sollte angezeigt werden, die die Sensordaten und Nachrichten zeigt, die an Ihren IoT Hub gesendet werden.![Ausgabe: Von Raspberry Pi an IoT Hub gesendete Sensordaten](media/iot-hub-raspberry-pi-web-simulator/2-run-application.png)

## <a name="read-the-messages-received-by-your-hub"></a>Lesen der von Ihrem Hub empfangenen Nachrichten

Wenn Sie die von Ihrem IoT-Hub empfangenen Nachrichten vom simulierten Gerät aus überwachen möchten, können Sie dafür die Azure IoT-Tools für Visual Studio Code verwenden. Weitere Informationen finden Sie unter [Senden und Empfangen von Nachrichten zwischen Ihrem Gerät und IoT Hub mithilfe der Azure IoT-Tools für Visual Studio Code](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Weitere Informationen zum Verarbeiten von Daten, die von Ihrem Gerät gesendet wurden, finden Sie im nächsten Abschnitt.

## <a name="next-steps"></a>Nächste Schritte

Sie haben eine Beispielanwendung ausgeführt, die Sensordaten sammelt und an Ihren IoT Hub sendet.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
