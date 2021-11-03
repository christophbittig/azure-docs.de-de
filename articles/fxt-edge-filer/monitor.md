---
title: Überwachen von Azure FXT Edge Filer
description: Überwachen des Hardwarestatus für den Azure FXT Edge Filer-Hybridspeichercache
author: femila
ms.service: fxt-edge-filer
ms.topic: how-to
ms.date: 06/20/2019
ms.author: femila
ms.openlocfilehash: c5475d39870f1f3003c2df78b92fd7b524ebf998
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131024313"
---
# <a name="monitor-azure-fxt-edge-filer-hardware-status"></a>Überwachen des Azure FXT Edge Filer-Hardwarestatus

Das Azure FXT Edge Filer-Hybridspeichercache-System verfügt über mehrere im Gehäuse integrierte Statusleuchten, die anhand deren Administratoren die Funktionsweise der Hardware verstehen können.

## <a name="system-health-status"></a>Systemintegritätsstatus

Zur Überwachung von Cachevorgängen auf einer höheren Ebene verwenden Sie die Seite **Dashboard** der Software-Einstellungen, wie in der [entsprechenden Anleitung](https://azure.github.io/Avere/legacy/dashboard/4_7/html/ops_dashboard_index.html) beschrieben.

## <a name="hardware-status-leds"></a>Hardwarestatus-LEDs

In diesem Abschnitt werden die verschiedenen in die Azure FXT Edge Filer-Hardware integrierten Statusleuchten erläutert.

### <a name="hard-drive-status-leds"></a>Festplattenstatus-LEDs

![Bild der Vorderseite der Festplatte, horizontal, mit Legendenbeschriftungen 2 (linke obere Ecke), 1 (linke untere Ecke) und 3 (rechte Seite)](media/fxt-monitor/fxt-drive-callouts.png)

Jeder Laufwerksträger weist zwei Status-LEDs auf: eine Aktivitätsanzeige (1) und eine Statusanzeige (2).

* Die Aktivitäts-LED (1) leuchtet, wenn das Laufwerk verwendet wird.
* Die Status-LED (2) gibt den Zustand des Laufwerks mit den Codes in der folgenden Tabelle an.

| Zustand der Laufwerksstatus-LED              | Bedeutung  |
|-------------------------------------|----------------------------------------------------------|
| Blinkt zweimal pro Sekunde grün      | Laufwerk wird identifiziert *oder* <br> Laufwerk wird für die Entfernung vorbereitet  |
| Aus (leuchtet nicht)                         | Systemstart wurde nicht abgeschlossen *oder* <br>Laufwerk kann entfernt werden |
| Blinkt grün, gelb, geht dann aus       | Laufwerkfehler vorhergesagt   |
| Blinkt viermal pro Sekunde gelb | Laufwerk ausgefallen   |
| Leuchtet grün                         | Laufwerk ist online |

Die rechte Seite des Laufwerks (3) ist mit der Kapazität und weiteren Informationen beschriftet.

Die Laufwerknummern sind im Zwischenraum zwischen den Laufwerken angegeben. Bei Azure FXT Edge Filer befindet sich das Laufwerk 0 ganz links oben und das Laufwerk 1 direkt darunter. Nummerierung wird in diesem Muster fortgesetzt.

![Foto eines Laufwerkschachts im FXT-Gehäuse mit Laufwerknummern und Kapazitätsanzeigen](media/fxt-drives-photo.png)

## <a name="left-control-panel"></a>Linkes Kontrollfeld

Auf dem linken Kontrollfeld vorn befinden sich verschiedene LED-Statusanzeigen (1) und eine große beleuchtete Systemzustandsanzeige (2).

![linkes Statusfeld; Beschriftungen der Statusanzeigen links unter 1, und Beschriftung der großen Leuchte der Systemzustandsanzeige rechts unter 2](media/fxt-monitor/fxt-control-panel-left.jpg)

### <a name="control-panel-status-indicators"></a>Statusanzeigen des Kontrollfelds

Die Statusanzeigen auf der linken Seite leuchten gelb, wenn in diesem System ein Fehler vorliegt. Die folgende Tabelle beschreibt mögliche Ursachen und Lösungen für die Fehler.

Wenn der Fehler nach dem Ausprobieren dieser Lösungen weiterhin besteht, [wenden Sie sich an den Support](support-ticket.md), um Hilfe zu erhalten.

| Symbol | BESCHREIBUNG | Fehlerzustand | Lösungsvorschläge |
|----------------|---------------|--------------------|----------------------|
| ![Laufwerksymbol](media/fxt-monitor/fxt-hd-icon.jpg) | Laufwerkstatus | Laufwerkfehler | Überprüfen Sie im Systemereignisprotokoll, ob ein Laufwerkfehler vorliegt, oder <br>Führen Sie den entsprechenden Onlinediagnosetest aus; starten Sie das System neu, und führen Sie eine eingebettete Diagnose (ePSA) durch, oder <br>Wenn die Laufwerke in einem RAID-Array konfiguriert sind, starten Sie das System neu, und geben Sie die das Hilfsprogramm für die Hostadapterkonfiguration ein. |
|![Temperatursymbol](media/fxt-monitor/fxt-temp-icon.jpg) | Temperaturstatus | Temperaturfehler – z. B. ein Lüfter ausgefallen oder Umgebungstemperatur liegt außerhalb des Bereichs | Überprüfen Sie die folgenden adressierbaren Bedingungen: <br>Ein Lüfter fehlt oder ist ausgefallen. <br>Die Abdeckung des Systems, die Luftschutzhaube, die Blindblende für das Speichermodul oder die Rückseite wurde entfernt. <br>Die Umgebungstemperatur ist zu hoch. <br>Der externe Luftstrom ist blockiert. |
|![Elektrizitätssymbol](media/fxt-monitor/fxt-electric-icon.jpg) | Elektrostatus | Elektrofehler – z. B. Spannung außerhalb des Bereichs, Netzteil oder Spannungsregler ausgefallen |  Überprüfen Sie das Systemereignisprotokoll oder Systemmeldungen auf das jeweilige Problem. Wenn ein Netzteilproblem vorliegt, überprüfen Sie die Status-LED des Netzteils, und setzen Sie das Netzteil ggf. wieder ein. |
|![Speichersymbol](media/fxt-monitor/fxt-memory-icon.jpg) | Speicherstatus | Speicherfehler | Überprüfen Sie das Systemereignisprotokoll oder Systemmeldungen auf die Position des fehlerhaften Speichers; setzen Sie das Speichermodul wieder ein. |
|![PCIe-Symbol](media/fxt-monitor/fxt-pcie-icon.jpg) | PCIe-Status | PCIe-Kartenfehler | Starten Sie das System neu; aktualisieren Sie die Treiber der PCIe-Karte; installieren Sie die Karte neu |

### <a name="system-health-status-indicator"></a>Statusanzeige der Systemintegrität

Die große beleuchtete Taste rechts neben dem linken Kontrollfeld zeigt den Gesamtsystemstatus an und wird System-ID-Modus auch als Geräteortungsleuchte verwendet.

Drücken Sie die Systemzustands- und ID-Taste, um zwischen dem System-ID-Modus und dem Systemzustandsmodus zu wechseln.

|Zustand der Systemintegritätsstatus | Bedingung |
|-------------------------------------------|-----------------------------------------------|
| Leuchtet blau | Normalbetrieb: das System eingeschaltet, funktioniert ordnungsgemäß, und der System-ID-Modus ist nicht aktiv. <br/>Drücken Sie die Systemzustands- und ID-Taste, um in den System-ID-Modus zu wechseln. |
| Blinkt blau | System-ID-Modus ist aktiv. Drücken Sie die Systemzustands- und System-ID-Taste, um in den Systemzustandsmodus zu wechseln. |
| Leuchtet gelb | Das System befindet sich im Ausfallsicherheitsmodus. Falls das Problem weiterhin besteht, [wenden Sie sich an den Microsoft-Kundendienst und -Support](support-ticket.md). |
| Blinkt gelb | Systemfehler. Überprüfen Sie das Systemereignisprotokoll auf bestimmte Fehlermeldungen. Informationen zu den Ereignis- und Fehlermeldungen, die von der Systemfirmware und Agents zur Überwachung von Systemkomponenten generiert werden, finden Sie auf der Fehlercode-Suchseite unter qrl.dell.com. |
