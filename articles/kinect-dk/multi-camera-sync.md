---
title: Synchronisierung mehrerer Azure Kinect DK-Geräte
description: In diesem Artikel werden die Vorteile der Synchronisierung von mehreren Geräten und das Einrichten der zu synchronisierenden Geräte erläutert.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 02/20/2020
ms.topic: article
keywords: Azure, Kinect, Spezifikationen, Hardware, DK, Funktionen, Tiefe, Farbe, RGB, IMU, Array, Tiefe, mehrere, Synchronisierung
ms.openlocfilehash: 5e4936e5c7b795745b7f1b131efbf99a562d8ed0
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130254682"
---
# <a name="synchronize-multiple-azure-kinect-dk-devices"></a>Synchronisierung mehrerer Azure Kinect DK-Geräte

Jedes Azure Kinect DK-Gerät besitzt 3,5-mm-Synchronisierungsports (**Sync In** und **Sync Out**), mit denen Sie mehrere Geräte miteinander verbinden können. Nachdem Sie die Geräte angeschlossen haben, kann Ihre Software die Auslösezeiten zwischen ihnen koordinieren. 

In diesem Artikel wird beschrieben, wie Sie die Geräte verbinden und synchronisieren.

## <a name="benefits-of-using-multiple-azure-kinect-dk-devices"></a>Vorteile der Verwendung mehrerer Azure Kinect DK-Geräte

Es gibt viele Gründe für die Verwendung mehrerer Azure Kinect DK-Geräte einschließlich der folgenden:

- Einfügen von Verdeckungen. Obwohl die Azure Kinect DK-Datentransformationen ein einzelnes Bild hervorbringen, sind die beiden Kameras (Tiefe und RGB) tatsächlich etwas voneinander entfernt. Der Offset macht die Verdeckung möglich. Eine Okklusion tritt auf, wenn ein Vordergrundobjekt die Ansicht eines Teils eines Hintergrundobjekts für eine der beiden Kameras eines Geräts blockiert. Im resultierenden Farbbild scheint das Vordergrundobjekt einen Schatten auf das Hintergrundobjekt zu werfen.  
   Im folgenden Diagramm z. B. sieht die linke Kamera das graue Pixel „P2“. Allerdings blockieren die weißen Vordergrundobjektblöcke den IR-Strahl der rechten Kamera. Die rechte Kamera hat keine Daten für „P2“.  
   ![Diagramm: Zwei Kameras, die auf den gleichen Punkt gerichtet sind, und eine Kamera ist blockiert.](./media/occlusion.png)  
   Mit zusätzlichen synchronisierten Geräten können die verdeckten Daten bereitgestellt werden.
- Scannen von Objekten in drei Dimensionen.
- Erhöhen Sie die effektive Bildfrequenz auf einen Wert, der größer als 30 Frames pro Sekunde (FpS) ist.
- Zeichnen Sie mehrere 4K-Farbbilder der gleichen Szene auf, die alle innerhalb von 100 Mikrosekunden (&mu;s) von der Mitte der Aufnahme liegen.
- Erhöhen der Kameraabdeckung im Raum.

## <a name="plan-your-multi-device-configuration"></a>Planen Ihrer Konfiguration mit mehreren Geräten

Bevor Sie beginnen, lesen Sie unbedingt die Artikel zu [Azure Kinect DK-Hardwarespezifikationen](hardware-specification.md) und zur [Azure Kinect DK-Tiefenkamera](depth-camera.md).

> [!NOTE]  
> Nehmen Sie die äußere Plastikabdeckung ab, um an den Ein- und Ausgang für die Synchronisierung zu gelangen.

### <a name="select-a-device-configuration"></a>Auswählen einer Gerätekonfiguration

Sie können für Ihre Gerätekonfiguration einen der folgenden Ansätze wählen:

- **Verkettungskonfiguration**. Synchronisieren Sie ein Mastergerät und bis zu acht untergeordnete Geräte.  
   ![Diagramm, das die Verbindung von Azure Kinect DK-Geräten in einer Verkettungskonfiguration zeigt.](./media/multicam-sync-daisychain.png)
- **Sternkonfiguration**. Synchronisieren Sie ein Mastergerät und bis zu zwei untergeordnete Geräte.  
   ![Diagramm, das die Einrichtung mehrere Azure DK-Geräte in einer Sternkonfiguration zeigt.](./media/multicam-sync-star.png)

#### <a name="using-an-external-sync-trigger"></a>Verwenden eines externen Synchronisierungsauslösers

In beiden Konfigurationen stellt das Mastergerät das auslösende Signal für die untergeordneten Geräte bereit. Sie können jedoch eine benutzerdefinierte externe Quelle als Synchronisierungsauslöser verwenden. Beispielsweise können Sie mit dieser Option Bildaufzeichnungen mit anderen Geräten synchronisieren. Sowohl in der Verkettungs- als auch der Sternkonfiguration wird die externe Auslöserquelle mit dem Mastergerät verbunden.

Die externe Auslöserquelle muss genau so funktionieren wie das Mastergerät. Sie muss ein Synchronisierungssignal mit folgenden Eigenschaften senden:

- Aktiv hoch
- Impulsbreite: Mehr als 8&mu;s
- 5 V TTL/CMOS
- Maximale Steuerungskapazität: Nicht kleiner als 8 Milliampère (MA)
- Frequenzunterstützung: Genau 30 FPS, 15 FPS und 5 FPS (Frequenz des Farbkameramaster-VSYNC-Signals)

Die Auslöserquelle muss das Signal über ein 3,5-mm-Audiokabel an den **Sync In**-Port des Mastergeräts senden. Sie können ein Stereo- oder Monokabel verwenden. Das Azure Kinect DK schließt alle Schäfte und Ringe des Audiokabelsteckers kurz und erdet sie. Wie im folgenden Diagramm gezeigt, empfängt das Gerät das Synchronisierungssignal nur über die Steckerspitze.

![Kabelkonfigurationen für ein externes Auslösersignal](./media/resources/camera-trigger-signal.jpg)

Weitere Informationen zum Arbeiten mit externen Geräten finden Sie unter [Verwenden des Azure Kinect-Rekorders mit externen synchronisierten Geräten](record-external-synchronized-units.md).

> [!NOTE]  
> Bei dem Synchronisierungsausgang handelt es sich um die vertikale Synchronisierung für die RGB-Kamera. Die Zeitstempel für alle Geräte werden auf Null festgelegt und zählen vorwärts. Die Mindest- und Maximalbreite des Synchronisationsimpulses wurde von Microsoft nicht charakterisiert, und es wird empfohlen, den vom Synchronisierungsausgang eines Azure Kinect DK-Geräts generierten Impuls zu imitieren.

### <a name="plan-your-camera-settings-and-software-configuration"></a>Planen Ihrer Kameraeinstellungen und Softwarekonfiguration

Informationen zum Einrichten Ihrer Software zum Steuern der Kameras und zum Verwenden der Bilddaten finden Sie unter [Informationen zum Sensor-SDK für Azure Kinect](about-sensor-sdk.md).

In diesem Abschnitt werden verschiedene Faktoren erörtert, die synchronisierte Geräte (aber nicht einzelne Geräte) beeinflussen. Ihre Software sollte diese Faktoren berücksichtigen.

#### <a name="exposure-considerations"></a>Überlegungen zur Belichtung
Wenn Sie das genaue Timing der einzelnen Geräte steuern möchten, sollten Sie eine manuelle Belichtungseinstellung verwenden. Unter der automatischen Belichtungseinstellung kann jede Farbkamera die tatsächliche Belichtung dynamisch ändern. Da sich die Belichtung auf das Timing auswirkt, entfernen solche Änderungen die Kameras schnell aus der Synchronisierung.

Vermeiden Sie in der Bildaufzeichnungsschleife Wiederholungen der gleichen Belichtungseinstellung. Rufen Sie die API nur einmal auf, wenn sie benötigt wird.

#### <a name="avoiding-interference-between-multiple-depth-cameras"></a>Vermeiden von Störungen zwischen mehreren Tiefenkameras

Wenn mehrere Tiefenkameras sich überlappende Sichtfelder aufnehmen, muss jede Kamera ihren einen eigenen zugeordneten Laser aufnehmen. Um zu verhindern, dass die Laser einander stören, sollten die Kameraaufzeichnungen um mindestens 160 μs voneinander versetzt sein.

Für jede Tiefenkameraaufzeichnung wird der Laser neun Mal eingeschaltet und ist jeweils nur für 125&mu;s aktiv. Der Laser ist dann abhängig vom Betriebsmodus für 1.450&mu;s oder 2.390&mu;s inaktiv. Darum ist der Ausgangspunkt für die Offsetberechnung 125&mu;s.

Außerdem erhöhen die Unterschiede zwischen der Kamerauhr und der Gerätefirmwareuhr den minimalen Offset auf 160&mu;s. Um einen präziseren Offset für Ihre Konfiguration zu berechnen, notieren Sie sich den von Ihnen verwendeten Tiefenmodus, und nutzen Sie die [Rohtiminingtabelle des Tiefensensors](hardware-specification.md#depth-sensor-raw-timing). Anhand der Daten aus dieser Tabelle können Sie den minimalen Offset (die Belichtungszeit jeder Kamera) mithilfe der folgenden Gleichung berechnen:

> *Belichtungszeit* = (*IR-Impulse* &times; *Impulsbreite*) + (*Inaktivitätszeiträume* &times; *Inaktivitätszeit*)

Wenn Sie einen Offset von 160&mu;s verwenden, können Sie bis zu neun zusätzliche Tiefenkameras konfigurieren, sodass jeder Laser eingeschaltet wird, während die anderen Laser inaktiv sind.

Verwenden Sie in Ihrer Software ```depth_delay_off_color_usec``` oder ```subordinate_delay_off_master_usec```, um sicherzustellen, dass jeder IR-Laser in seinem eigenen 160&mu;s-Fenster oder in einem anderen Sichtfeld ausgelöst wird.

> [!NOTE]  
> Die tatsächliche Impulsbreite beträgt 125&mu;s, es werden jedoch 160 Mikrosekunden angegeben, um einen gewissen Spielraum zu haben. Bei NFOV UNBINNED folgen beispielsweise auf jeden 125&mu;s-Impuls 1.450&mu;s Inaktivität. Die Summe, (9 x 125) + (8 x 1.450), ergibt die Belichtungszeit von 12,8 ms. Sie können die Belichtung von 2 Geräten am engsten verschachteln, wenn der erste Impuls der zweiten Kamera in den ersten Leerlaufzeitraum der ersten Kamera fällt. Die Verzögerung zwischen der ersten und der zweiten Kamera könnte nur von 125&mu;s betragen (die Breite eines Impulses), aber ein gewisser Spielraum wird empfohlen, daher die Angabe von 160&mu;s. 160&mu;s ermöglichen es Ihnen, die Belichtungszeiten von bis zu 10 Kameras zu überlappen.

## <a name="prepare-your-devices-and-other-hardware"></a>Vorbereiten von Geräten und anderer Hardware

Außer mehreren Azure Kinect DK-Geräten haben Sie möglicherweise zusätzliche Hostcomputer und andere Hardware, um die Konfiguration zu unterstützen, die Sie erstellen möchten. Bevor Sie mit der Einrichtung beginnen, stellen Sie anhand der Informationen in diesem Abschnitt sicher, dass alle Geräte und Hardware bereit sind.

### <a name="azure-kinect-dk-devices"></a>Azure Kinect DK-Geräte

Führen Sie für jedes Azure Kinect DK-Gerät, das Sie synchronisieren möchten, folgende Schritte aus:

- Stellen Sie sicher, dass auf dem Gerät die neueste Firmware installiert ist. Weitere Informationen zum Aktualisieren Ihrer Geräte finden Sie unter [Aktualisieren der Azure Kinect DK-Firmware](update-device-firmware.md). 
- Entfernen Sie die Geräteabdeckung, um die Synchronisierungsports anzuzeigen.
- Notieren Sie sich die Seriennummer jedes Geräts. Sie werden diese Nummer später beim Setup verwenden.

### <a name="host-computers"></a>Hostcomputer

In der Regel verwendet jedes Azure Kinect DK seinen eigenen Hostcomputer. Je nachdem, wie Sie das Gerät verwenden und wie viele Daten über die USB-Verbindung übertragen werden, können Sie einen dedizierten Hostcontroller verwenden. 

Stellen Sie sicher, dass das Azure Kinect Sensor SDK auf den einzelnen Hostcomputern installiert ist. Weitere Informationen über die Installation des Sensor SDK finden Sie unter [Schnellstart: Einrichten Ihres Azure Kinect DK-Geräts](set-up-azure-kinect-dk.md). 

#### <a name="linux-computers-usb-memory-on-ubuntu"></a>Linux-Computer: USB-Arbeitsspeicher unter Ubuntu

Standardmäßig weisen Linux-basierte Hostcomputer dem USB-Controller nur 16 MB an Kernelspeicher zu, um USB-Übertragungen zu verarbeiten. Diese Menge reicht in der Regel aus, um ein einzelnes Azure Kinect DK zu unterstützen. Zur Unterstützung mehrerer Geräte benötigt der USB-Controller jedoch mehr Arbeitsspeicher. Befolgen Sie diese Schritte, um den Arbeitsspeicher heraufzusetzen:

1. Bearbeiten Sie **/etc/default/grub**.
1. Suchen Sie die folgende Zeile:
   ```cmd
   GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
   ```
   Ersetzen Sie sie durch folgende Zeile:
   ```cmd
   GRUB_CMDLINE_LINUX_DEFAULT="quiet splash usbcore.usbfs_memory_mb=32"
   ```
   > [!NOTE]  
   > Mit diesen Befehlen wird der USB-Speicher auf 32 MB festgelegt. Diese Beispieleinstellung entspricht dem doppelten Standardwert. Sie können je nach Lösung einen viel größeren Wert festlegen.
1. Führen Sie **sudo update-grub** aus.
1. Starten Sie den Computer neu.

### <a name="cables"></a>Kabel

Um die Geräte miteinander und mit den Hostcomputern zu verbinden, müssen Sie 3,5-mm-Kabel Stecker an Stecker (auch als 3,5-mm-Audiokabel bezeichnet) verwenden. Die Kabel (Stereo oder Mono) sollten weniger als 10 Meter lang sein.

Wie viele Kabel Sie benötigen, hängt von der Anzahl der verwendeten Geräte und der spezifischen Gerätekonfiguration ab. Im Azure Kinect DK-Lieferumfang sind keine Kabel enthalten. Sie müssen sie separat kaufen.

Wenn Sie die Geräte in der Sternkonfiguration verbinden, benötigen Sie auch einen Kopfhörersplitter.

## <a name="connect-your-devices"></a>Verbinden von Geräten

**So verbinden Sie Azure Kinect DK-Geräte in einer Verkettungskonfiguration**

1. Verbinden Sie jedes Azure Kinect DK mit der Stromversorgung.
1. Verbinden Sie jedes Gerät mit seinem Host-PC. 
1. Wählen Sie ein Gerät als Mastergerät aus, und schließen Sie ein 3,5-mm-Audiokabel an seinen **Sync Out**-Port an.
1. Schließen Sie das andere Ende des Kabels an den **Sync In**-Port des ersten untergeordneten Geräts an.
1. Um ein anderes Gerät zu verbinden, schließen Sie ein weiteres Kabel an den **Sync Out**-Port des ersten untergeordneten Geräts und an den **Sync In**-Port des nächsten Geräts an.
1. Wiederholen Sie den vorherigen Schritt, bis alle Geräte verbunden sind. Das letzte Gerät sollte nur über eine Kabelverbindung verfügen. Seine **Synch Out**-Ports sollten leer sein.

**So verbinden Sie Azure Kinect DK-Geräte in einer Sternkonfiguration**

1. Verbinden Sie jedes Azure Kinect DK mit der Stromversorgung.
1. Verbinden Sie jedes Gerät mit seinem Host-PC. 
1. Wählen Sie ein Gerät als Mastergerät aus, und schließen Sie das einzelne End des Kopfhörersplitters an seinen **Sync Out**-Port an.
1. Verbinden Sie 3,5-mm-Audiokabel mit den „Split“-Enden des Kopfhörersplitters.
1. Schließen Sie das andere Ende jedes Kabels an den **Sync In**-Port eines der untergeordneten Geräte an.

## <a name="verify-that-the-devices-are-connected-and-communicating"></a>Überprüfen, ob die Geräte verbunden sind und kommunizieren

Überprüfen Sie mit dem [Azure Kinect Viewer](azure-kinect-viewer.md), ob die Geräte ordnungsgemäß verbunden sind. Wiederholen Sie diesen Vorgang so oft wie nötig, um jedes untergeordnete Gerät in Kombination mit dem Mastergerät zu testen.

> [!IMPORTANT]  
> Für diesen Vorgang müssen Sie die Seriennummer jedes Azure Kinect DK kennen.

1. Öffnen Sie zwei Instanzen von Azure Kinect Viewer.
1. Wählen Sie unter **Gerät öffnen** die Seriennummer des untergeordneten Geräts aus, das Sie testen möchten.  
   ![Gerät öffnen](./media/open-devices.png)
   > [!IMPORTANT]  
   > Um eine exakte Bildaufzeichnungsordnung zwischen allen Geräten zu erzielen, müssen Sie das Mastergerät zuletzt starten.  
1. Wählen Sie unter **Externe Synchronisierung** die Option **Sub** aus.  
   ![Start der untergeordneten Kamera](./media/sub-device-start.png)
1.  Wählen Sie **Starten** aus.  
    > [!NOTE]  
    > Da es sich hierbei um ein untergeordnetes Gerät handelt, zeigt Azure Kinect Viewer nach dem Start des Geräts kein Bild an. Es wird erst dann ein Bild angezeigt, wenn das untergeordnete Gerät ein Synchronisierungssignal vom Mastergerät empfängt.
1. Öffnen Sie das Mastergerät nach dem Start des untergeordneten Geräts mit der anderen Instanz von Azure Kinect Viewer.
1. Wählen Sie unter **Externe Synchronisierung** die Option **Master** aus.
1. Wählen Sie **Starten** aus.

Wenn das Azure Kinect-Mastergerät gestartet wird, sollten beide Instanzen von Azure Kinect Viewer Bilder anzeigen.

## <a name="calibrate-the-devices-as-a-synchronized-set"></a>Kalibrieren der Geräte als synchronisierte Gruppe

Nachdem Sie überprüft haben, ob die Geräte ordnungsgemäß kommunizieren, können Sie sie kalibrieren, um Bilder in einer einzelnen Domäne zu produzieren.

Auf einem einzelnen Gerät werden die Tiefen- und RGB-Kameras werkseitig für die Zusammenarbeit kalibriert. Wenn jedoch mehrere Geräte zusammenarbeiten müssen, müssen sie kalibriert werden, um festzulegen, wie ein Bild von der Domäne der Kamera, die es aufgezeichnet hat, in die Domäne der Kamera transformiert wird, die Sie zur Bildverarbeitung verwenden möchten.

Es gibt mehrere Optionen für die übergreifende Kalibrierung von Geräten. Microsoft stellt das [GitHub-Green-Screen-Codebeispiel](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/green_screen) bereit, bei dem die OpenCV-Methode verwendet wird. Die Infodatei für dieses Codebeispiel enthält weitere Details und Anweisungen zum Kalibrieren der Geräte.

Weitere Informationen zur Kalibrierung finden Sie unter [Verwenden von Azure Kinect-Kalibrierungsfunktionen](use-calibration-functions.md).

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie synchronisierte Geräte eingerichtet haben, informieren Sie sich auch über die Verwendung der
> [!div class="nextstepaction"]
> [Aufnahme- und Wiedergabe-API des Sensor-SDK für Azure Kinect](record-playback-api.md)

## <a name="related-topics"></a>Verwandte Themen

- [Informationen zum Sensor-SDK für Azure Kinect](about-sensor-sdk.md)
- [Hardwarespezifikationen für Azure Kinect DK](hardware-specification.md) 
- [Schnellstart: Einrichten Ihres Azure Kinect DK-Geräts](set-up-azure-kinect-dk.md) 
- [Aktualisieren der Azure Kinect DK-Firmware](update-device-firmware.md) 
- [Zurücksetzen von Azure Kinect DK](reset-azure-kinect-dk.md) 
- [Azure Kinect Viewer](azure-kinect-viewer.md) 
