---
title: Grundlegendes zum Device Update for Azure IoT Hub-Agent | Microsoft-Dokumentation
description: Grundlegendes zum Device Update for Azure IoT Hub-Agent.
author: ValOlson
ms.author: valls
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 6502779c4d301e760e10be8cf1a1766834ec2038
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2021
ms.locfileid: "122604150"
---
# <a name="device-update-for-iot-hub-agent-overview"></a>Device Update for IoT Hub-Agent: Übersicht

Der Device Update-Agent besteht aus zwei konzeptionellen Ebenen:

* Die Schnittstellenebene baut auf dem [Azure IoT Plug & Play](../iot-develop/overview-iot-plug-and-play.md) auf und ermöglicht das Übertragen von Nachrichten zwischen dem Device Update-Agent und den Device Update-Diensten.
* Die Plattformebene ist für die Aktualisierungsaktionen auf hoher Ebene wie Herunterladen, Installieren und Anwenden zuständig, die plattform- oder gerätespezifisch sein können.

:::image type="content" source="media/understand-device-update/client-agent-reference-implementations.png" alt-text="Agent-Implementierungen." lightbox="media/understand-device-update/client-agent-reference-implementations.png":::

## <a name="the-interface-layer"></a>Die Schnittstellenebene

Die Schnittstellenebene besteht aus der [ADU Core-Schnittstelle](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/adu_core_interface) und der [Geräteinformationsschnittstelle](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/device_info_interface).

Diese Schnittstellen basieren auf einer Konfigurationsdatei für Standardwerte. Die Standardwerte sind aduc_manufacturer und aduc_model für die AzureDeviceUpdateCore-Schnittstelle und model und manufacturer für die DeviceInformation-Schnittstelle. [Weitere Informationen](device-update-configuration-file.md) zur Konfigurationsdatei.

### <a name="adu-core-interface"></a>ADU Core-Schnittstelle

Die ADU Core-Schnittstelle ist der primäre Kommunikationskanal zwischen dem Device Update-Agent und den Diensten. [Weitere Informationen](device-update-plug-and-play.md#adu-core-interface) zu dieser Schnittstelle.

### <a name="device-information-interface"></a>Geräteinformationsschnittstelle

Die Geräteinformationsschnittstelle wird verwendet, um die `Azure IoT PnP DeviceInformation`-Schnittstelle zu implementieren. [Weitere Informationen](device-update-plug-and-play.md#device-information-interface) zu dieser Schnittstelle.

## <a name="the-platform-layer"></a>Plattformebene

Es gibt zwei Implementierungen der Plattformebene. Die Simulatorplattformebene verfügt über eine triviale Implementierung der Updateaktionen und wird zum schnellen Testen und Auswerten der Device Update for IoT Hub-Dienste und das Setup verwendet. Wenn der Device Update-Agent mit der Simulatorplattformebene erstellt wird, wird er als Device Update Simulator-Agent oder einfach als Simulator bezeichnet. [Weitere Informationen](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) zur Verwendung des Simulator-Agents. Die Linux-Plattformebene ist in [Übermittlungsoptimierung](https://github.com/microsoft/do-client) für Downloads integriert und wird in unserem Raspberry Pi-Referenzimage und allen Clients verwendet, die unter Linux-Systemen ausgeführt werden.

### <a name="simulator-platform-layer"></a>Simulatorplattformebene

Die Implementierung der Simulatorplattformschicht befindet sich in `src/platform_layers/simulator_platform_layer` und kann zum Testen und Auswerten von Device Update for IoT Hub verwendet werden.  Viele der Aktionen in der „Simulatorimplementierung“ werden simuliert, um physische Änderungen für das Experimentieren mit Device Update for IoT Hub zu verringern.  Ein „simuliertes“ End-to-End-Update kann mithilfe dieser Plattformebene ausgeführt werden. [Weitere Informationen](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) zum Ausführen des Simulator-Agents.

### <a name="linux-platform-layer"></a>Linux-Plattformebene

Die Implementierung der Linux-Plattformebene befindet sich in `src/platform_layers/linux_platform_layer` und ist in den [Bereitstellungsoptimierungsclient](https://github.com/microsoft/do-client/releases) für Downloads integriert. Sie wird in unserem Raspberry Pi-Referenzimage und allen Clients verwendet, die unter Linux-Systemen ausgeführt werden.

Diese Ebene kann in verschiedene Updatehandler integriert werden, um das Installationsprogramm zu implementieren. Beispielsweise ruft der `SWUpdate`-Updatehandler ein Shellskript auf, um die ausführbare Datei `SWUpdate` zum Ausführen eines Updates aufzurufen.

## <a name="update-handlers"></a>Updatehandler

Updatehandler sind Komponenten, die inhalts- oder installerspezifische Teile des Updates verarbeiten. Implementierungen von Updatehandlern finden Sie in `src/content_handlers`.

### <a name="simulator-update-handler"></a>Simulatorupdatehandler

Der Simulatorupdatehandler wird von der Simulatorplattformebene verwendet und kann mit der Linux-Plattformebene verwendet werden, um Interaktionen mit einem Inhaltshandler zu simulieren. Der Simulatorupdatehandler implementiert die Updatehandler-APIs größtenteils ohne Vorgänge. Die Implementierung des Simulatorupdatehandlers finden Sie unten:
* [Imageupdatesimulator](https://github.com/Azure/iot-hub-device-update/blob/main/src/content_handlers/swupdate_handler/inc/aduc/swupdate_simulator_handler.hpp)
* [Paketupdate-apt-Simulator](https://github.com/Azure/iot-hub-device-update/blob/main/src/content_handlers/apt_handler/inc/aduc/apt_simulator_handler.hpp)

Hinweis: Das Feld InstalledCriteria in der PNP-Schnittstelle AzureDeviceUpdateCore muss den SHA256-Hash des Inhalts enthalten. Dabei handelt es sich um denselben Hash, der im [Importmanifestobjekt](import-update.md#create-a-device-update-import-manifest) vorhanden ist. [Weitere Informationen](device-update-plug-and-play.md) zu `installedCriteria` und zur `AzureDeviceUpdateCore`-Schnittstelle.

### <a name="swupdate-update-handler"></a>`SWUpdate`-Updatehandler

Der `SWUpdate`-Updatehandler ist in die ausführbare Befehlszeilendatei `SWUpdate` und andere Shell-Befehle integriert, um A/B-Updates speziell für das Raspberry Pi-Referenzimage zu implementieren. Das aktuelle Raspberry Pi-Referenzimage finden Sie [hier](https://github.com/Azure/iot-hub-device-update/releases). Der `SWUpdate`-Updatehandler ist in [src/content_handlers/swupdate_content_handler](https://github.com/Azure/iot-hub-device-update/tree/main/src/content_handlers/swupdate_handler) implementiert.

### <a name="apt-update-handler"></a>APT-Updatehandler

Der APT-Updatehandler verarbeitet ein APT-spezifisches Updatemanifest und ruft APT auf, um die angegebenen Debian-Pakete zu installieren oder zu aktualisieren.

## <a name="self-update-device-update-agent"></a>Selbstupdate des Device Update-Agents

Der Geräteupdate-Agent und seine Abhängigkeiten können über die Device Update for IoT Hub-Pipeline aktualisiert werden. Wenn Sie ein imagebasiertes Update verwenden, schließen Sie den neuesten Geräteupdate-Agent in das neue Image ein. Wenn Sie ein paketbasiertes Update verwenden, schließen Sie den Geräteupdate-Agent und seine gewünschte Version wie jedes andere Paket in das APT-Manifest ein. [Weitere Informationen](device-update-apt-manifest.md) zum APT-Manifest. Sie können die installierte Version des Device Update-Agents und des Übermittlungsoptimierungs-Agents im Abschnitt „Device Properties“ (Geräteeigenschaften) Ihres [IoT-Gerätezwillings](../iot-hub/iot-hub-devguide-device-twins.md) überprüfen. [Weitere Informationen zu Geräteeigenschaften finden Sie unter ADU Core-Schnittstelle](device-update-plug-and-play.md#device-properties).

## <a name="next-steps"></a>Nächste Schritte
[Grundlegendes zur Konfigurationsdatei des Device Update-Agents](device-update-configuration-file.md)