---
title: Verwenden des Azure Kinect-Rekorders mit externen synchronisierten Geräten
description: Erfahren Sie, wie Sie mithilfe des Azure Kinect-Rekorders Daten von Geräten aufzeichnen, die für die externe Synchronisierung konfiguriert sind.
author: tesych
ms.author: tesych
ms.reviewer: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Sensor, Viewer, externe Synchronisierung, Phasenverzögerung, Tiefe, RGB, Kamera, Audiokabel, Rekorder
ms.openlocfilehash: fd751c9979cf2dc1fa2698deff0e2b207fe518c5
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124771382"
---
# <a name="use-azure-kinect-recorder-with-external-synchronized-devices"></a>Verwenden des Azure Kinect-Rekorders mit externen synchronisierten Geräten

In diesem Artikel wird beschrieben, wie mit dem [Azure Kinect-Rekorder](azure-kinect-recorder.md) Daten von für die externe Synchronisierung konfigurierten Geräten aufgezeichnet werden können.

## <a name="prerequisites"></a>Voraussetzungen

- [Richten Sie mehrere Azure Kinect DK-Einheiten für die externe Synchronisierung ein](https://support.microsoft.com/help/4494429).

## <a name="external-synchronization-constraints"></a>Einschränkungen bei der externen Synchronisierung

- Am Mastergerät kann kein SYNC IN-Kabel angeschlossen werden.
- Das Mastergerät muss eine RGB-Kamera streamen, damit die Synchronisierung aktiviert wird.
- Für alle Einheiten muss dieselbe Kamerakonfiguration verwendet werden (Bildrate und Auflösung).
- Auf allen Einheiten muss dieselbe Gerätefirmware ausgeführt werden (Anweisungen zum [Aktualisieren der Firmware](update-device-firmware.md)).
- Alle untergeordneten Geräte müssen vor dem Mastergerät gestartet werden.
- Auf allen Geräten muss der gleiche Belichtungswert festgelegt werden.
- Die Einstellung *Delay off master* (Verzögerung gegenüber Master) der einzelnen untergeordneten Geräte ist relativ zum Mastergerät festgelegt.

## <a name="record-when-each-unit-has-a-host-pc"></a>Aufzeichnen, wenn jede Einheit über einen Host-PC verfügt

Im folgenden Beispiel verfügt jedes Gerät über einen eigenen dedizierten Host-PC.
Es wird empfohlen, Geräte mit dedizierten PCs zu verbinden, um Probleme mit der USB-Bandbreite und der CPU/GPU-Nutzung zu vermeiden.

### <a name="subordinate-1"></a>Untergeordnetes Gerät 1

1. Richten Sie den Rekorder für die erste Einheit ein.

      `k4arecorder.exe --external-sync sub -e -8 -r 5 -l 10 sub1.mkv`

2. Das Gerät beginnt zu warten.

    ```console
    Device serial number: 000011590212
    Device version: Rel; C: 1.5.78; D: 1.5.60[6109.6109]; A: 1.5.13
    Device started
    [subordinate mode] Waiting for signal from master
    ```

### <a name="subordinate-2"></a>Untergeordnetes Gerät 2

1. Richten Sie den Rekorder für die zweite Einheit ein.

    `k4arecorder.exe --external-sync sub -e -8 -r 5 -l 10 sub2.mkv`

2. Das Gerät beginnt zu warten.

    ```console
    Device serial number: 000011590212
    Device version: Rel; C: 1.5.78; D: 1.5.60[6109.6109]; A: 1.5.13
    Device started
    [subordinate mode] Waiting for signal from master
    ```

### <a name="master"></a>Master

1. Starten Sie die Aufzeichnung auf dem Mastergerät.

    `>k4arecorder.exe --external-sync master -e -8 -r 5 -l 10 master.mkv`

2. Warten Sie, bis die Aufzeichnung beendet ist.

## <a name="recording-when-multiple-units-connected-to-single-host-pc"></a>Aufzeichnen, wenn mehrere Einheiten mit einem einzelnen Host-PC verbunden sind

Sie können mehrere Azure Kinect DK-Einheiten mit einem einzelnen Host-PC verbinden. Dies kann jedoch eine sehr hohe Auslastung für USB-Bandbreite und Hostcompute bedeuten. So verringern Sie die Auslastung

- Verbinden Sie jedes Gerät mit einem eigenen USB-Hostcontroller.
- Verwenden Sie eine leistungsstarke GPU, die die Tiefen-Engine für die einzelnen Geräte verarbeiten kann.
- Zeichnen Sie nur erforderliche Sensoren auf, und verwenden Sie eine niedrigere Bildrate.

Starten Sie die untergeordneten Geräte immer zuerst und das Mastergerät zuletzt.

## <a name="subordinate-1"></a>Untergeordnetes Gerät 1

1. Starten Sie den Rekorder auf dem untergeordneten Gerät.

    `>k4arecorder.exe --device 1 --external-sync subordinate --imu OFF -e -8 -r 5 -l 5 output-2.mkv`

2. Das Gerät wechselt in den Wartezustand.

## <a name="master"></a>Master

1. Starten Sie das Mastergerät.

    `>k4arecorder.exe --device 0 --external-sync master --imu OFF -e -8 -r 5 -l 5 output-1.mkv`

2. Warten Sie, bis die Aufzeichnung beendet ist.

## <a name="playing-recording"></a>Wiedergeben der Aufzeichnung

Sie können den [Azure Kinect Viewer](azure-kinect-viewer.md) verwenden, um die Aufzeichnung wiederzugeben.



## <a name="tips"></a>Tipps

- Verwenden Sie die manuelle Belichtung für die Aufzeichnung von synchronisierten Kameras. Die automatische Belichtung von RGB-Kameras kann die Zeitsynchronisierung beeinträchtigen.
- Durch das Neustarten des untergeordneten Geräts wird die Synchronisierung unterbrochen.
- Einige [Kameramodi](hardware-specification.md#depth-camera-supported-operating-modes) unterstützen maximal 15 BpS. Es wird empfohlen, die Modi und Bildfrequenzen zwischen Geräten nicht zu mischen.
- Durch das Verbinden mehrerer Einheiten mit einem einzelnen PC kann die USB-Bandbreite ausgelastet werden. Verwenden Sie stattdessen einen separaten Host-PC pro Gerät. Achten Sie auch auf CPU/GPU-Compute.
- Deaktivieren Sie das Mikrofon und die IMU, wenn sie nicht benötigt werden, um die Zuverlässigkeit zu erhöhen.

Informationen zu Problemen finden Sie unter [Problembehandlung](troubleshooting.md).

## <a name="see-also"></a>Weitere Informationen

- [Einrichten der externen Synchronisierung](https://support.microsoft.com/help/4494429/sync-multiple-devices)
- [Azure Kinect-Rekorder:](azure-kinect-recorder.md) Rekordereinstellungen und zusätzliche Informationen
- [Azure Kinect Viewer:](azure-kinect-viewer.md) Wiedergeben von Aufzeichnungen oder Festlegen von Eigenschaften für RGB-Kameras, die über den Rekorder nicht verfügbar sind
- [Azure Kinect-Firmwaretool:](azure-kinect-firmware-tool.md) Aktualisieren der Gerätefirmware
