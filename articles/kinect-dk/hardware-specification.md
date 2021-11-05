---
title: Hardwarespezifikationen für Azure Kinect DK
description: Hier finden Sie Informationen zu den Komponenten, Spezifikationen und Funktionen von Azure Kinect DK.
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 03/18/2021
ms.topic: article
keywords: Azure, Kinect, Spezifikationen, Hardware, DK, Funktionen, Tiefe, Farbe, RGB, IMU, Mikrofon, Array, Tiefe
ms.custom: CI 114092, CSSTroubleshooting
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: 45941395864bde4442748671c6b5c33da81b319d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131082491"
---
# <a name="azure-kinect-dk-hardware-specifications"></a>Hardwarespezifikationen für Azure Kinect DK

Dieser Artikel enthält Details zur Integration der neuesten Sensortechnologie von Microsoft in ein einzelnes USB-Zubehörgerät mithilfe von Azure Kinect-Hardware.

![Azure Kinect DK](./media/resources/hardware-specs-media/device-wire.png)

## <a name="terms"></a>Begriffe

In diesem Artikel werden folgende Abkürzungen verwendet:

- NFOV (Narrow Field Of View): Enges Sichtfeld (Tiefenmodus)
- WFOV (Wide Field Of View): Breites Sichtfeld (Tiefenmodus)
- FOV (Field Of View): Sichtfeld
- BpS: Bilder pro Sekunde
- IME: Inertiale Messeinheit
- FoI (Field of Interest): Interessenfeld

## <a name="product-dimensions-and-weight"></a>Abmessungen und Gewicht des Produkts

Das Azure Kinect-Gerät hat folgende Abmessungen und folgendes Gewicht:

- **Abmessungen:** 103 × 39 × 126 mm
- **Gewicht:** 440 g

![Azure Kinect DK: Abmessungen](./media/resources/hardware-specs-media/dimensions.png)

Eine STEP-Datei für das Azure Kinect-Gerät finden Sie [hier](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/assets). 

## <a name="operating-environment"></a>Betriebsumgebung

Azure Kinect DK ist für Entwickler und gewerbliche Unternehmen mit folgenden Umgebungsbedingungen konzipiert:

- **Temperatur:** 10–25 <sup>0</sup>C
- **Luftfeuchtigkeit:** 8–90 % relative Luftfeuchtigkeit (nicht kondensierend)

> [!NOTE]
> Unter anderen Umgebungsbedingungen funktioniert das Gerät möglicherweise nicht ordnungsgemäß oder gar nicht. Diese Umgebungsbedingungen gelten für die Umgebung in unmittelbarer Nähe des Geräts unter sämtlichen Betriebsbedingungen. Bei Verwendung mit einem externen Gehäuse wird die Verwendung einer aktiven Temperatursteuerung und/oder anderer Kühllösungen empfohlen, um sicherzustellen, dass das Gerät innerhalb der angegebenen Bereiche betrieben wird. Das Gerät verfügt über einen Kühlkanal zwischen Front und Rückseite. Dieser Kühlkanal darf bei der Implementierung des Geräts nicht blockiert werden.

Weitere Sicherheitsinformationen für das Produkt finden Sie [hier](https://support.microsoft.com/help/4023454/safety-information).

## <a name="depth-camera-supported-operating-modes"></a>Unterstützte Betriebsmodi der Tiefenkamera

Azure Kinect DK verfügt über eine von Microsoft entwickelte integrierte 1-Megapixel-Tiefenkamera, die auf dem Laufzeitverfahren (Time Of Flight, TOF) basiert und den [auf der ISSCC 2018 vorgestellten Bildsensor](/windows/mixed-reality/ISSCC-2018) verwendet. Die Tiefenkamera unterstützt folgende Modi:

 | Mode            | Lösung | FoI       | FPS                | Betriebsbereich* | Belichtungszeit |
|-----------------|------------|-----------|--------------------|------------------|---------------|
| NFOV unklassifiziert   | 640 × 576    | 75° × 65°   | 0, 5, 15, 30       | 0,5–3,86 m       | 12,8 ms        |
| NFOV 2 × 2 klassifiziert (SW) | 320 × 288    | 75° × 65°   | 0, 5, 15, 30       | 0,5–5,46 m       | 12,8 ms        |
| WFOV 2 × 2 klassifiziert | 512 × 512    | 120° × 120° | 0, 5, 15, 30       | 0,25–2,88 m      | 12,8 ms        |
| WFOV unklassifiziert   | 1\.024 × 1.024  | 120° × 120° | 0, 5, 15           | 0,25–2,21 m      | 20,3 ms        |
| Passives IR      | 1\.024 × 1.024  | –       | 0, 5, 15, 30       | –              | 1,6 ms         |

\*15–95 % Reflektivität bei 850 nm, 2,2 μW/cm<sup>2</sup>/nm, Zufallsfehler-Standardabweichung ≤ 17 mm, typischer systematischer Fehler < 11 mm + 0,1 % Entfernung ohne Mehrweg-Interferenz. Unter Umständen kann auch Tiefe außerhalb des oben angegebenen Betriebsbereichs erreicht werden. Dies hängt vom Reflexionsvermögen eines Objekts ab.

## <a name="color-camera-supported-operating-modes"></a>Unterstützte Betriebsmodi der Farbkamera

Azure Kinect DK verfügt über einen Rolling-Shutter-CMOS-Sensor vom Typ OV12A10 mit 12 MP. Im Anschluss finden Sie eine Liste der nativen Betriebsmodi:

|             RGB-Kameraauflösung (H × V)  |          Seitenverhältnis  |          Formatoptionen   |          Bildfrequenzen (BpS)  |          Nominales FOV (H × V) (nachbearbeitet)  |
|------------------------------------------|------------------------|---------------------------|-----------------------------|---------------------------------------------|
|       3\.840 × 2.160                          |          16:9          |          MJPEG            |          0, 5, 15, 30       |          90° × 59°                              |
|       2560x1440                          |          16:9          |          MJPEG            |          0, 5, 15, 30       |          90° × 59°                              |
|       1\.920 × 1.080                          |          16:9          |          MJPEG            |          0, 5, 15, 30       |          90° × 59°                              |
|       1\.280 × 720                           |          16:9          |          MJPEG/YUY2/NV12  |          0, 5, 15, 30       |          90° × 59°                              |
|       4\.096 × 3.072                          |          4:3           |          MJPEG             |          0, 5, 15           |          90° × 74,3°                            |
|       2048x1536                          |          4:3           |          MJPEG             |          0, 5, 15, 30       |          90° × 74,3°                            |

Die RGB-Kamera ist mit der USB-Videoklasse kompatibel und kann ohne das Sensor-SDK verwendet werden. Farbraum der RGB-Kamera: BT.601, gesamtes Spektrum [0–255]. Die MJPEG-[Farbunterabtastung](https://en.wikipedia.org/wiki/Chroma_subsampling) ist 4:2:2.

> [!NOTE]
> Das Sensor-SDK kann Farbbilder im BGRA-Pixelformat bereitstellen. Dieser Modus wird nicht nativ vom Gerät unterstützt und führt zu einer höheren CPU-Auslastung. Für die Konvertierung der vom Gerät übermittelten MJPEG-Bilder wird die Host-CPU verwendet.

## <a name="rgb-camera-exposure-time-values"></a>Belichtungszeitwerte der RGB-Kamera

Im Anschluss finden Sie die zulässigen manuellen Belichtungswerte für die RGB-Kamera:

| exp| 2^exp | 50 Hz   |60 Hz    |
|----|-------|--------|--------|
| -11|     488|    500|    500 |
| -10|     977|   1250|   1250 |
|  –9|    1\.953|   2500|   2500 |
|  -8|    3906|  10000|   8\.330 |
|  -7|    7813|  20000|  16.670 |
|  –6|   15625|  30.000|  33.330 |
|  -5|   31.250|  40.000|  41.670 |
|  –4|   62.500|  50000|  50000 |
|  -3|  125.000|  60000|  66.670 |
|  -2|  250.000|  80.000|  83.330 |
|  -1|  500000| 100.000| 100.000 |
|   0| 1000000| 120000| 116.670 |
|   1| 2\.000.000| 130.000| 133.330 |

## <a name="depth-sensor-raw-timing"></a>Rohzeiten für den Tiefensensor

Tiefenmodus | IR <br>Impulse | Puls <br>Breite  | Idle <br>Punkte| Leerlaufzeit | Belichtung <br> Time
-|-|-|-|-|-
NFOV unklassifiziert <br>  NFOV 2 × 2 klassifiziert <br> WFOV 2 × 2 klassifiziert | 9 | 125 us | 8 | 1\.450 us | 12,8 ms 
WFOV unklassifiziert                                            | 9 | 125 us | 8 | 2\.390 us | 20,3 ms


## <a name="camera-field-of-view"></a>Sichtfeld der Kamera

Die nächste Abbildung zeigt die Tiefe und das Sichtfeld der RGB-Kamera bzw. die von den Sensoren erfassbaren Blickwinkel. Das Diagramm zeigt die RGB-Kamera im 4:3-Modus.

![Kamerasichtfeld](./media/resources/hardware-specs-media/camera-fov.png)

In dieser Abbildung ist das Sichtfeld der Kamera in der Frontansicht aus einer Entfernung von 2.000 mm dargestellt.

![Kamerasichtfeld (Frontansicht)](./media/resources/hardware-specs-media/fov-front.png)

> [!NOTE]
> Im NFOV-Tiefenmodus ist die Pixelüberlappung der RGB-Kamera in 4:3-Auflösungen besser als in 16:9-Auflösungen.

## <a name="motion-sensor-imu"></a>Bewegungssensor (IME)

Bei der eingebetteten inertialen Messeinheit (IME) handelt es sich um eine Einheit vom Typ LSM6DSMUS, die sowohl mit einem Beschleunigungsmesser als auch mit einem Gyroskop ausgestattet ist. Beschleunigungsmesser und Gyroskop werden simultan mit einer Frequenz von 1,6 kHz abgetastet. Die Abtastwerte werden mit einer Frequenz von 208 Hz an den Host gemeldet.

## <a name="microphone-array"></a>Mikrofon-Array

Azure Kinect DK verfügt über ein hochwertiges eingebettetes Array mit sieben kreisförmig angeordneten Mikrofonen, das als USB-Standardaudiogerät der Klasse 2.0 erkannt wird. Auf alle sieben Kanäle kann zugegriffen werden. Leistungsspezifikationen:

- Empfindlichkeit: -22 dBFS (94 dB Schalldruckpegel, 1 kHz)
- Signal-Rausch-Verhältnis > 65 dB
- Akustischer Überlastpunkt: 116 dB

![Mikrofonblase](./media/resources/hardware-specs-media/mic-bubble.png)

## <a name="usb"></a>USB

Azure Kinect DK ist ein USB 3.0-Verbundgerät und macht folgende Hardwareendpunkte für das Betriebssystem verfügbar:

Die Anbieter-ID ist 0x045E (Microsoft). Die folgende Tabelle zeigt die Produkt-IDs:

|    USB-Schnittstelle        |    PNP-IP    |     Hinweise            |
|-------------------------|--------------|----------------------|
|    USB3.1-Hub (2. Generation)    |    0x097A    |    Haupthub    |
|    USB 2.0-Hub         |    0x097B    |    HS-USB          |
|    Tiefenkamera       |    0x097C    |    USB 3.0            |
|    Farbkamera       |    0x097D    |    USB 3.0            |
|    Mikrofone        |    0x097E    |    HS-USB          |

## <a name="indicators"></a>Indikatoren

Auf der Vorderseite des Geräts befindet sich eine Kamerastreaminganzeige. Diese kann mithilfe des Sensor-SDK programmgesteuert deaktiviert werden.

Die Status-LED auf der Rückseite des Geräts gibt Aufschluss über den Zustand des Geräts:

| LED-Status     | Bedeutung                                                   |
|-----------------------|------------------------------------------------------------|
| Weiß leuchtend           | Das Gerät ist eingeschaltet und funktioniert ordnungsgemäß.                         |
| Weiß blinkend        | Das Gerät ist eingeschaltet, verfügt aber über keine USB 3.0-Datenverbindung.   |
| Gelb blinkend        | Unzureichende Stromversorgung               |
| Gelb-weiß blinkend  | Aktive Firmwareaktualisierung oder Wiederherstellung                    |

## <a name="power-device"></a>Stromversorgung des Geräts

Das Gerät kann auf zwei Arten mit Strom versorgt werden:

1. Über das mitgelieferte Netzteil. Der Netzanschluss hat einen Außendurchmesser von 4,5 mm, einen Innendurchmesser von 3,0 mm und einen Pindurchmesser von 0,6 mm.
2. Über ein Typ-C-auf-Typ-C-Kabel (Stromversorgung und Daten).

Ein Typ-C-auf-Typ-C-Kabel ist im Lieferumfang von Azure Kinect DK nicht enthalten.

> [!NOTE]
> - Bei dem mitgelieferten Netzkabel handelt es sich um ein USB-Kabel vom Typ „Typ A auf Hohlstecker“. Verwenden Sie dieses Kabel mit dem mitgelieferten Steckdosennetzteil. Die Leistungsaufnahme des Geräts kann die Kapazität von zwei USB-Standardanschlüssen vom Typ A übersteigen.
> - Es empfiehlt sich, hochwertige USB-Kabel zu verwenden und sich vor der Remotebereitstellung zu vergewissern, dass alles funktioniert.

> [!TIP]
> Kriterien für ein gutes Typ-C-zu-Typ-C-Kabel:
> - Das [USB-zertifizierte Kabel](https://www.usb.org/products) muss sowohl Stromversorgung als auch Datenübertragung unterstützen.
> - Passive Kabel sollten kürzer als 1,5 m sein. Wird ein längeres Kabel benötigt, verwenden Sie ein aktives Kabel. 
> - Das Kabel muss mindestens 1,5 A unterstützen. Andernfalls muss ein externes Netzteil angeschlossen werden.

Überprüfen Sie das Kabel:

- Verbinden Sie das Gerät über das Kabel mit dem Host-PC.
- Vergewissern Sie sich, dass alle Geräte ordnungsgemäß im Windows-Geräte-Manager aufgelistet werden. Die Tiefen- und die RGB-Kamera sollten wie im folgenden Beispiel angezeigt werden:

  ![Azure Kinect DK im Geräte-Manager](./media/resources/hardware-specs-media/device-manager.png)

- Vergewissern Sie sich im Azure Kinect Viewer, dass das Streamen über das Kabel für alle Sensoren zuverlässig funktioniert. Verwenden Sie dabei die folgenden Einstellungen:

  - Tiefenkamera: NFOV unklassifiziert
  - RGB-Kamera: 2160p
  - Mikrofone und IME aktiviert

## <a name="what-does-the-light-mean"></a>Was bedeutet die Anzeige?

Die Betriebsanzeige ist eine LED auf der Rückseite des Azure Kinect DK-Geräts. Die Farbe der LED ändert sich abhängig vom Gerätestatus.

![Die Abbildung zeigt die Rückseite des Azure Kinect DK-Geräts. Sie enthält drei nummerierte Hervorhebungen: eine für die LED-Anzeige und darunter zwei für die Kabel.](./media/quickstarts/azure-kinect-dk-power-indicator.png)

In dieser Abbildung sind folgende Komponenten hervorgehoben:

1. Betriebsanzeige
1. Netzkabel (mit der Stromquelle verbunden)
1. USB-C-Datenkabel (mit dem PC verbunden)

Stellen Sie sicher, dass die Kabel wie in der Abbildung angeschlossen sind. Informieren Sie sich anschließend anhand der folgenden Tabelle über die Bedeutung der verschiedenen Zustände der Betriebsanzeige.

|LED-Status |Bedeutung |Empfohlene Aktion |
| ---| --- | --- |
|Weiß leuchtend |Das Gerät ist eingeschaltet und funktioniert ordnungsgemäß. |Verwenden Sie das Gerät. |
|Aus |Das Gerät ist nicht mit dem PC verbunden. |Vergewissern Sie sich, dass das Netzkabel mit dem runden Stecker mit dem Gerät und mit dem USB-Netzteil verbunden ist.<br /><br />Vergewissern Sie sich, dass das USB-C-Kabel mit dem Gerät und Ihrem PC verbunden ist. |
|Weiß blinkend |Das Gerät ist eingeschaltet, verfügt aber über keine USB 3.0-Datenverbindung. |Vergewissern Sie sich, dass das Netzkabel mit dem runden Stecker mit dem Gerät und mit dem USB-Netzteil verbunden ist.<br /><br />Vergewissern Sie sich, dass das USB-C-Kabel mit dem Gerät und mit einem USB 3.0-Anschluss Ihres PCs verbunden ist.<br /><br />Schließen Sie das Gerät an einen anderen USB 3.0-Anschluss Ihres PCs an.<br /><br />Öffnen Sie auf Ihrem PC den Geräte-Manager (**Start** > **Systemsteuerung** > **Geräte-Manager**), und vergewissern Sie sich, dass Ihr PC über einen unterstützten USB 3.0-Hostcontroller verfügt. |
|Gelb blinkend |Unzureichende Stromversorgung |Vergewissern Sie sich, dass das Netzkabel mit dem runden Stecker mit dem Gerät und mit dem USB-Netzteil verbunden ist.<br /><br />Vergewissern Sie sich, dass das USB-C-Kabel mit dem Gerät und Ihrem PC verbunden ist. |
|Gelb-weiß blinkend |Das Gerät ist eingeschaltet und erhält ein Firmwareupdate, oder die Werkseinstellungen des Geräts werden wiederhergestellt. |Warten Sie, bis die Betriebsanzeige weiß leuchtet. Weitere Informationen finden Sie unter [Zurücksetzen von Azure Kinect DK](reset-azure-kinect-dk.md). |

## <a name="power-consumption"></a>Stromverbrauch

Azure Kinect DK verbraucht bis zu 5,9 W. Der spezifische Stromverbrauch ist abhängig von Anwendungsfall.

## <a name="calibration"></a>Kalibrierung

Azure Kinect DK ist ab Werk kalibriert. Die Kalibrierungsparameter für visuelle Sensoren und Trägheitssensoren können programmgesteuert über das Sensor-SDK abgefragt werden.

## <a name="device-recovery"></a>Gerätewiederherstellung

Die Gerätefirmware kann mithilfe der Taste unter dem Arretierstift auf die ursprüngliche Firmware zurückgesetzt werden.

![Azure Kinect DK: Taste zum Wiederherstellen](./media/resources/hardware-specs-media/recovery.png)

Informationen zum Wiederherstellen des Geräts finden Sie [hier](reset-azure-kinect-dk.md).

## <a name="next-steps"></a>Nächste Schritte

- [Verwenden des Sensor-SDK für Azure Kinect](about-sensor-sdk.md)
- [Einrichten der Hardware](set-up-azure-kinect-dk.md)
