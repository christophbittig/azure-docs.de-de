---
title: Problembehandlung bei Schnellstarts für eingebettete Azure RTOS-Geräte
description: Schritte zum Beheben gängiger Probleme bei der Verwendung der Schnellstarts für eingebettete Azure RTOS-Geräte
author: JimacoMS4
ms.author: v-jbrannian
ms.service: iot-develop
ms.topic: troubleshooting
ms.date: 06/10/2021
ms.openlocfilehash: 26353064f929c382559216c28651757c7b4ee498
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/15/2021
ms.locfileid: "112124750"
---
# <a name="troubleshooting-the-azure-rtos-embedded-device-quickstarts"></a>Problembehandlung bei Schnellstarts für eingebettete Azure RTOS-Geräte

Wenn Sie die [Schnellstarts für die Entwicklung eingebetteter Geräte](quickstart-devkit-mxchip-az3166.md) befolgen, treten möglicherweise einige gängige Probleme auf. Im Allgemeinen sind die Probleme auf folgende Quellen zurückzuführen:

* **Ihre Umgebung**. Ihr Computer, Ihre Software oder die Netzwerkeinrichtung und -verbindung.
* **Ihre Azure IoT-Ressourcen**. Der IoT-Hub und das Gerät, das Sie zum Herstellen einer Verbindung mit Azure IoT erstellt haben.
* **Ihr Gerät**. Die Platine und ihre Konfiguration.

Dieser Artikel enthält Lösungsvorschläge für die am häufigsten auftretenden Probleme beim Abschließen der Schnellstarts.

## <a name="prerequisites"></a>Voraussetzungen

Alle Schritte zur Problembehandlung erfordern, dass Sie die folgenden Voraussetzungen für den verwendeten Schnellstart erfüllt haben:

* Sie haben alle erforderlichen Komponenten und Softwaretools für den Schnellstart installiert oder abgerufen.
* Sie haben gemäß der Schnellstartanleitung eine Azure IoT Hub- oder Azure IoT Central-Anwendung erstellt und ein Gerät registriert.
* Sie haben gemäß der Schnellstartanleitung ein Image für das Gerät erstellt.

## <a name="issue-the-source-directory-doesnt-contain-cmakeliststxt-file"></a>Problem: Datei „CMakeLists.txt“ ist im Quellverzeichnis nicht enthalten
### <a name="description"></a>BESCHREIBUNG
Dieses Problem kann beim Erstellen des Projekts auftreten. Es ist darauf zurückzuführen, dass das Projekt nicht ordnungsgemäß aus GitHub geklont wurde. Das Projekt enthält mehrere Untermodule, die standardmäßig nur dann geklont werden, wenn das Flag **--recursive** verwendet wird.

### <a name="resolution"></a>Lösung
* Wenn Sie das Repository mithilfe von Git klonen, vergewissern Sie sich, dass die Option **--recursive** vorhanden ist.

## <a name="issue-the-build-fails"></a>Problem: Fehler beim Build

### <a name="description"></a>BESCHREIBUNG

Das Problem kann auftreten, weil der Pfad zu einer Objektdatei die standardmäßig maximal zulässige Pfadlänge in Windows überschreitet. Überprüfen Sie die Buildausgabe auf eine Meldung ähnlich der folgenden:

```output
-- Configuring done
CMake Warning in C:/embedded quickstarts/areallyreallyreallylongpath/getting-started/core/lib/netxduo/addons/azure_iot/azure_iot_security_module/iot-security-module-core/CMakeLists.txt:
  The object file directory

    C:/embedded quickstarts/areallyreallyreallylongpath/getting-started/NXP/MIMXRT1060-EVK/build/lib/netxduo/addons/azure_iot/azure_iot_security_module/iot-security-module-core/CMakeFiles/asc_security_core.dir/./

  has 208 characters.  The maximum full path to an object file is 250
  characters (see CMAKE_OBJECT_PATH_MAX).  Object file

    src/serializer/extensions/custom_builder_allocator.c.obj

  cannot be safely placed under this directory.  The build may not work
  correctly.


-- Generating done
```

### <a name="resolution"></a>Lösung

Versuchen Sie mit einer der folgenden Optionen, das Problem zu beheben:
* Klonen Sie das Repository in ein Verzeichnis mit einem kürzeren Pfad, und versuchen Sie es noch mal.
* Befolgen Sie die Anweisungen unter [Beschränkung der maximal zulässigen Pfadlänge](/windows/win32/fileio/maximum-file-path-limitation), um lange Pfade in Windows 10, Version 1607 und höher, zu aktivieren.

## <a name="issue-device-cant-connect-to-iot-hub"></a>Problem: Gerät kann keine Verbindung mit IoT Hub herstellen

### <a name="description"></a>BESCHREIBUNG

Das Problem kann nach dem Erstellen von Azure-Ressourcen und dem Flashen des Geräts auftreten. Wenn Sie versuchen, Ihr soeben geflashtes Gerät mit Azure IoT zu verbinden, wird eine Konsolenmeldung wie die folgende angezeigt:

```output
Unable to resolve DNS for MQTT Server
```

### <a name="resolution"></a>Lösung

* Überprüfen Sie in der Datei *azure_config.h* die Schreibweise und die Groß-/Kleinschreibung der Konfigurationswerte, die Sie für Ihre IoT-Konfiguration eingegeben haben. Bei den Werten für einige IoT-Ressourcenattribute wie `deviceID` und `primaryKey` ist die Groß-/Kleinschreibung relevant.

## <a name="issue--wi-fi-is-unable-to-connect"></a>Problem: WLAN kann keine Verbindung herstellen

### <a name="description"></a>BESCHREIBUNG

Nach dem Flashen eines Geräts, das eine WLAN-Verbindung verwendet, erhalten Sie beim Herstellen einer Verbindung mit Ihrem WLAN die Fehlermeldung, dass keine WLAN-Verbindung möglich ist.

### <a name="resolution"></a>Lösung

* Überprüfen Sie die WLAN-Netzwerkfrequenz und die Einstellungen. Die in den Schnellstarts für eingebettete Geräte verwendeten Geräte verwenden grundsätzlich 2,4 GHz. Vergewissern Sie sich, dass Ihr WLAN-Router für die Unterstützung eines 2,4-GHz-Netzwerks konfiguriert ist.
* Überprüfen Sie den WLAN-Modus. Überprüfen Sie, welche Einstellung in der Datei *azure_config.h* für die WIFI_MODE-Konstante verwendet wird. Überprüfen Sie Ihre Einstellungen für WLAN-Netzwerksicherheit oder Authentifizierung, um sicherzustellen, dass der WLAN-Sicherheitsmodus mit dem in der Konfigurationsdatei enthaltenen Modus übereinstimmt.

## <a name="issue-flashing-the-board-fails"></a>Problem: Fehler beim Flashen der Platine

### <a name="description"></a>BESCHREIBUNG

Der Vorgang zum Flashen Ihres Geräts kann nicht abgeschlossen werden. Dies stellen Sie fest, wenn eines der folgenden Symptome auftritt:

* Die von Ihnen erstellte *BIN*-Imagedatei wird nicht auf das Gerät kopiert.
* Das Hilfsprogramm, das Sie zum Flashen des Geräts verwenden, gibt eine Warnung oder einen Fehler aus.
* Das Hilfsprogramm, das Sie zum Flashen des Geräts verwenden, zeigt keine Meldung an, dass die Programmierung erfolgreich abgeschlossen wurde.

### <a name="resolution"></a>Lösung

* Stellen Sie sicher, dass Sie mit dem richtigen USB-Anschluss des Geräts verbunden sind. Einige Geräte verfügen über mehrere Anschlüsse.
* Verwenden Sie ein anderes Micro-USB-Kabel. Einige Geräte und Kabel sind nicht kompatibel.
* Verwenden Sie einen anderen USB-Anschluss an Ihrem Computer für die Verbindung. Ein USB-Anschluss kann intern getrennt oder in der Software deaktiviert sein oder sich vorübergehend in einem nicht verwendbaren Zustand befinden.
* Starten Sie den Computer neu.

## <a name="issue-device-fails-to-connect-to-port"></a>Problem: Gerät kann keine Verbindung mit Port herstellen

### <a name="description"></a>BESCHREIBUNG

Nachdem Sie Ihr Gerät geflasht und mit Ihrem Computer verbunden haben, erhalten Sie in Ihrer Terminalsoftware eine Meldung wie die folgende:

```output
Failed to initialize the port.
Please verify the COM port settings.
```

### <a name="resolution"></a>Lösung

* Überprüfen Sie in den Einstellungen für Ihre Terminalsoftware die Einstellung **Port**, um sicherzustellen, dass der richtige Port ausgewählt ist. Wenn mehrere Ports angezeigt werden, können Sie den Windows-Geräte-Manager öffnen und den Knoten **Ports** auswählen, um den richtigen Port für Ihr verbundenes Gerät zu suchen.

## <a name="issue-terminal-output-shows-garbled-text"></a>Problem: Terminalausgabe zeigt unleserlichen Text an

### <a name="description"></a>BESCHREIBUNG

Nachdem Sie Ihr Gerät erfolgreich geflasht und mit Ihrem Computer verbunden haben, wird in der Terminalsoftware eine unleserliche Textausgabe angezeigt.

### <a name="resolution"></a>Lösung

* Vergewissern Sie sich in den Einstellungen für Ihre Terminalsoftware, dass die Einstellung **Baudrate** auf *115.200* festgelegt ist.

## <a name="issue-terminal-output-shows-no-text"></a>Problem: Terminalausgabe zeigt keinen Text an

### <a name="description"></a>BESCHREIBUNG

Nachdem Sie Ihr Gerät erfolgreich geflasht und mit Ihrem Computer verbunden haben, wird in der Terminalsoftware keine Ausgabe angezeigt.

### <a name="resolution"></a>Lösung

* Vergewissern Sie sich, dass die Einstellungen in Ihrer Terminalsoftware mit den Einstellungen im Schnellstart übereinstimmen.
* Starten Sie Ihre Terminalsoftware neu.
* Drücken Sie an Ihrem Gerät die **Rücksetztaste**.
* Vergewissern Sie sich, dass Ihr USB-Kabel ordnungsgemäß angeschlossen ist.

## <a name="issue-communication-between-device-and-iot-hub-fails"></a>Problem: Fehler bei der Kommunikation zwischen Gerät und IoT Hub

### <a name="description"></a>BESCHREIBUNG

Nachdem Sie Ihr Gerät geflasht und mit Ihrem Computer verbunden haben, erhalten Sie in Ihrem Terminalfenster eine wiederholte Meldung wie die folgende:

```output
Failed to publish temperature
```

### <a name="resolution"></a>Lösung

* Stellen Sie sicher, dass als *Tarif und Skalierung* entweder *Free* oder *Standard* vorliegt. **Basic wird nicht unterstützt**, weil dieser Tarif die Kommunikation von Cloud zum Gerät und die Gerätezwillingskommunikation nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie die in diesem Artikel geschilderten Probleme überprüft haben, aber Ihr Gerät noch immer nicht in einem Terminal überwachen oder mit Azure IoT verbinden können, liegt möglicherweise ein Problem mit der Hardware oder der physischen Konfiguration Ihres Geräts vor. Auf der Seite des Geräteherstellers finden Sie eine Dokumentation und Supportoptionen.

* [STMicroelectronics B-L475E-IOT01](https://www.st.com/content/st_com/en/products/evaluation-tools/product-evaluation-tools/mcu-mpu-eval-tools/stm32-mcu-mpu-eval-tools/stm32-discovery-kits/b-l475e-iot01a.html)
* [NXP MIMXRT1060-EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/mimxrt1060-evk-i-mx-rt1060-evaluation-kit:MIMXRT1060-EVK)
* [Microchip ATSAME54-XPro](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro)