---
title: Aktualisieren von Azure Percept DK über eine USB-C-Verbindung
description: Hier erfahren Sie, wie Sie Ihr Azure Percept DK-Gerät über eine USB-C-Kabelverbindung aktualisieren.
author: EthanChangAED
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/18/2021
ms.custom: template-how-to
ms.openlocfilehash: eef23597e6cb8f215fd7ce2374d66ff104cc00bc
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123226017"
---
# <a name="update-azure-percept-dk-over-a-usb-c-connection"></a>Aktualisieren von Azure Percept DK über eine USB-C-Verbindung

In dieser Anleitung wird beschrieben, wie Sie das Betriebssystem und die Firmware Ihres Development Kits erfolgreich über eine USB-Verbindung aktualisieren. Hier finden Sie eine Übersicht darüber, welche Schritte Sie im Rahmen dieses Verfahrens ausführen.

1. Herunterladen des Updatepakets auf einen Hostcomputer
1. Ausführen des Befehls, mit dem das Updatepaket an das Entwicklerkit übertragen wird
1. Versetzen des Development Kits in den USB-Modus mithilfe von SSH oder DIP-Schaltern
1. Verbinden des Development Kits mit dem Hostcomputer per USB-C-Kabel
1. Warten, bis das Update abgeschlossen ist

> [!WARNING]
> Wenn Sie Ihr Development Kit über eine USB-Verbindung aktualisieren, werden alle vorhandenen Daten auf dem Gerät gelöscht (einschließlich der KI-Modelle und Container).
>
> Befolgen Sie alle Anweisungen in der angegebenen Reihenfolge. Wenn Sie Schritte überspringen, kann Ihr Development Kit möglicherweise nicht mehr verwendet werden.

## <a name="prerequisites"></a>Voraussetzungen

- Azure Percept DK
- Ein Hostcomputer unter Windows oder Linux mit WLAN-Funktion und einem verfügbaren USB-C- oder USB-A-Anschluss
- Ein USB-C-zu-USB-A-Kabel (optional, separat erhältlich)
- Ein SSH-Anmeldekonto, das während des [Setups von Azure Percept DK](./quickstart-percept-dk-set-up.md) erstellt wurde
- Ein Sechskantschlüssel im Lieferumfang des Development Kits, um die Schrauben auf dessen Rückseite zu entfernen (bei Verwendung der DIP-Schaltermethode)

> [!NOTE]
> **Mac-Benutzer:** Das Aktualisieren des Azure Percept DK über eine USB-Verbindung funktioniert nicht mit einem Mac als Hostcomputer. 

## <a name="download-software-tools-and-update-files"></a>Herunterladen von Softwaretools und Updatedateien

1. [NXP UUU-Tool:](https://github.com/NXPmicro/mfgtools/releases) Laden Sie das **neueste Release** der Datei „uuu.exe“ (Windows) bzw. der UUU-Datei (Linux) herunter. Diese finden Sie auf der Registerkarte **Ressourcen**. UUU ist ein Tool, das von NXP entwickelt wurde und zum Aktualisieren von NXP-Entwicklungsboards verwendet wird.

1. [Laden Sie die Updatedateien herunter.](https://go.microsoft.com/fwlink/?linkid=2155734) Sie befinden sich alle in einer ZIP-Datei, die Sie im nächsten Abschnitt extrahieren.

1. Stellen Sie sicher, dass alle drei Buildartefakte vorhanden sind:
    - Azure-Percept-DK- *&lt;Versionsnummer&gt;* .raw
    - fast-hab-fw.raw
    - emmc_full.txt

## <a name="set-up-your-environment"></a>Einrichten der Umgebung

1. Erstellen Sie einen Ordner bzw. ein Verzeichnis auf dem Hostcomputer an einem Speicherort, der über die Befehlszeile leicht zugänglich ist.

1. Kopieren Sie das UUU-Tool (**uuu.exe** oder **uuu**) in den neuen Ordner.

1. Extrahieren Sie die zuvor heruntergeladenen Updatedateien in den neuen Ordner, der das UUU-Tool enthält.

## <a name="run-the-command-that-transfers-the-update-package-to-the-dev-kit"></a>Ausführen des Befehls, mit dem das Updatepaket an das Entwicklerkit übertragen wird

1. Öffnen Sie eine Windows-Eingabeaufforderung (Start > cmd) oder ein Linux-Terminal, und **navigieren Sie zum Ordner mit den Updatedateien und dem UUU-Tool**. 

1. Geben Sie in der Befehlszeile oder im Terminal den folgenden Befehl ein:

    - Windows:

        ```console
        uuu -b emmc_full.txt fast-hab-fw.raw Azure-Percept-DK-<version number>.raw 
        ```

    - Linux:

        ```bash
        sudo ./uuu -b emmc_full.txt fast-hab-fw.raw Azure-Percept-DK-<version number>.raw
        ```

1. Im Eingabeaufforderungsfenster wird die folgende Meldung angezeigt: **Waiting for Known USB Device to Appear...** (Warten auf die Anzeige eines bekannten USB-Geräts...). Das UUU-Tool wartet nun, dass das Development Kit vom Hostcomputer erkannt wird. **Fahren Sie mit den nächsten Schritten fort, und versetzen Sie das Development Kit in den USB-Modus.**

## <a name="set-the-dev-kit-into-usb-mode"></a>Versetzen des Development Kits in den USB-Modus
Zum Versetzen des Development Kits in den USB-Modus gibt zwei Möglichkeiten: über SSH oder durch Umlegen der DIP-Schalter auf dem Development Kit. Wählen Sie die Methode, die für Sie am besten geeignet ist.

### <a name="using-ssh"></a>Verwenden von SSH
SSH ist die sicherste und bevorzugte Methode zum Versetzen des Development Kits in den USB-Modus. Allerdings ist es erforderlich, dass Sie sich mit dem WLAN-Zugriffspunkt des Development Kits verbinden können. Wenn Sie sich nicht mit dem WLAN-Zugriffspunkt des Development Kits verbinden können, müssen Sie die DIP-Schaltermethode verwenden.

1. Stecken Sie das mitgelieferte USB-C-Kabel in den USB-C-Anschluss des Entwicklerkits und in den USB-C-Anschluss des Hostcomputers. Wenn Ihr Computer nur über einen USB-A-Anschluss verfügt, verbinden Sie ein USB-C-zu-USB-A-Kabel (separat erhältlich) mit dem Entwicklerkit und dem Hostcomputer.

1. Stellen Sie eine SSH-Verbindung mit Ihrem Entwicklerkit her. Wenn Sie Hilfe zu SSH benötigen, befolgen Sie [diese Anweisungen](./how-to-ssh-into-percept-dk.md).

1. Geben Sie im SSH-Terminal die folgenden Befehle ein:

    1. Legen Sie für das Gerät den USB-Updatemodus fest:

        ```bash
        sudo flagutil    -wBfRequestUsbFlash    -v1
        ```

    1. Starten Sie das Gerät neu. Die Updateinstallation wird gestartet.

        ```bash
        sudo reboot -f
        ```

### <a name="using-the-dip-switch-method"></a>Verwenden der DIP-Schaltermethode
Wählen Sie die DIP-Schaltermethode, wenn Sie keine SSH-Verbindung mit dem Gerät erstellen können.

1. Ziehen Sie den Stecker des Entwicklungsboards, wenn es mit dem Stromkabel verbunden ist.
1. Entfernen Sie die vier Schrauben auf der Rückseite des Entwicklungsboards mit dem Sechskantschlüssel im Lieferumfang des Development Kits.

    :::image type="content" source="media/how-to-usb-update/dip-switch-01.jpg" alt-text="Entfernen der vier Schrauben auf der Rückseite des Entwicklungsboards":::

1. Schieben Sie das Entwicklungsboard behutsam in Richtung der LEDs. Der Kühlkörper bleibt an der Oberseite des Entwicklungsboards befestigt. Schieben Sie das Entwicklungsboard nur 2-3 Zentimeter, damit keine Kabel abgeklemmt werden.

    :::image type="content" source="media/how-to-usb-update/dip-switch-02.jpg" alt-text="Schieben des Boards über ein paar Zentimeter":::

1. Die DIP-Schalter befinden sich in der Ecke des Boards. Es gibt vier Schalter, die jeweils zwei Positionen haben: oben (1) oder unten (0). Die Standardpositionen der Schalter sind Oben-Unten-Unten-Oben (1001). Ändern Sie mit einer Büroklammer oder einem anderen spitzen Gegenstand die Position der Schalter auf Unten-Oben-Unten-Oben (0101).

    :::image type="content" source="media/how-to-usb-update/dip-switch-03.jpg" alt-text="Die Schalter befinden sich in der unteren Ecke des Boards":::

1. Das Development Kit befindet sich jetzt im USB-Modus, und Sie können mit den nächsten Schritten fortfahren. **Sobald die Aktualisierung abgeschlossen ist, ändern Sie die DIP-Schalter wieder in die Standardposition Oben-Unten-Unten-Oben (1001).** Schieben Sie dann das Entwicklungsboard zurück in seine Position, und bringen Sie die vier Schrauben auf der Rückseite wieder an.

## <a name="connect-the-dev-kit-to-the-host-computer-via-a-usb-c-cable"></a>Verbinden des Development Kits mit dem Hostcomputer per USB-C-Kabel
Bei diesem Verfahren wird der einzelne USB-C-Anschluss des Entwicklerkits für die Aktualisierung verwendet.  Wenn Ihr Computer über einen USB-C-Anschluss verfügt, können Sie das USB-C-zu-USB-C-Kabel im Lieferumfang des Development Kits verwenden.  Wenn Ihr Computer nur über einen USB-A-Anschluss verfügt, müssen Sie ein USB-C-zu-USB-A-Kabel (separat erhältlich) verwenden.

1. Verbinden Sie das Development Kit mithilfe des entsprechenden USB-C-Kabels mit dem Hostcomputer.
1. Der Hostcomputer sollte nun das Development Kit als USB-Gerät erkennen. Wenn Sie den Befehl, der das Updatepaket auf das Development Kit überträgt, erfolgreich ausgeführt haben und in der Eingabeaufforderung „Waiting for Known USB Device to Appear...“ (Warten auf die Anzeige eines bekannten USB-Geräts...)** steht, sollte das Update in etwa 10 Sekunden automatisch starten.

## <a name="wait-for-the-update-to-complete"></a>Warten, bis das Update abgeschlossen ist

1. Navigieren Sie zurück zur anderen Eingabeaufforderung oder zum anderen Terminal. Wenn das Update abgeschlossen ist, wird die Meldung ```Success 1    Failure 0``` angezeigt:

    > [!NOTE]
    > Nach der Aktualisierung wird Ihr Gerät auf die Werkseinstellungen zurückgesetzt, und die WLAN-Verbindung und die SSH-Anmeldung werden gelöscht.

1. Schalten Sie nach Abschluss des Updates das Entwicklerkit aus. Trennen Sie das USB-Kabel vom PC.
1. Wenn Sie die DIP-Schaltermethode verwendet haben, um das Development Kit in den USB-Modus zu versetzen, stellen Sie sicher, dass die DIP-Schalter wieder auf die Standardpositionen zurückgestellt werden. Schieben Sie dann das Entwicklungsboard zurück in seine Position, und ziehen Sie die vier Schrauben wieder auf der Rückseite an.   

## <a name="next-steps"></a>Nächste Schritte

Nutzen Sie die [Azure Percept DK-Setupfunktion](./quickstart-percept-dk-set-up.md), um Ihr Gerät neu zu konfigurieren.
