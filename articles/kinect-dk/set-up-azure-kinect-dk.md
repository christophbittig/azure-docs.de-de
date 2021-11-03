---
title: 'Schnellstart: Einrichten von Azure Kinect DK'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie die Azure Kinect DK-Hardware einrichten.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.topic: quickstart
ms.date: 02/12/2020
keywords: Azure, Kinect, DevKit, Azure DK, einrichten, Hardware, schnell, USB, Strom, Viewer, Sensor, Streaming, Einrichtung, SDK, Firmware
ms.custom: CI 114092, CSSTroubleshooting
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: 1f7bd9a7f21a97fd0a9176fcd8a8f8ec40f1777e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131085884"
---
# <a name="quickstart-set-up-your-azure-kinect-dk"></a>Schnellstart: Einrichten Ihres Azure Kinect DK-Geräts

In dieser Schnellstartanleitung erfahren Sie, wie Sie Ihr Azure Kinect DK-Gerät einrichten. Es wird gezeigt, wie Sie die Visualisierung des Sensordatenstroms testen und den [Azure Kinect Viewer](azure-kinect-viewer.md) verwenden.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="system-requirements"></a>Systemanforderungen

Vergewissern Sie sich anhand der [Systemanforderungen](system-requirements.md), dass die Konfiguration Ihres Host-PCs alle Mindestanforderungen für Azure Kinect DK erfüllt.

## <a name="set-up-hardware"></a>Einrichten der Hardware

> [!NOTE]
> Entfernen Sie vor der Verwendung des Geräts zunächst die Schutzfolie von der Kamera.

1. Stecken Sie den Netzstecker am Netzanschluss auf der Rückseite des Geräts ein. Verbinden Sie das andere Ende des Kabels mit dem USB-Netzteil, und stecken Sie dieses in eine Steckdose.
2. Verbinden Sie das USB-Datenkabel mit Ihrem Gerät und anschließend mit einem USB 3.0-Anschluss Ihres PCs.
   >[!NOTE]
   >Um optimale Ergebnisse zu erzielen, verbinden Sie das Kabel direkt mit dem Gerät und dem PC. Verwenden Sie nach Möglichkeit keine Verlängerungen oder zusätzlichen Adapter.

3. Vergewissern Sie sich, dass die LED für die Betriebsanzeige (neben dem USB-Kabel) weiß leuchtet.
  
   Es dauert einige Sekunden, bis das Gerät eingeschaltet ist. Das Gerät kann verwendet werden, wenn die LED-Streaminganzeige auf der Vorderseite nicht mehr leuchtet.  

   Weitere Informationen zur LED für die Betriebsanzeige finden Sie unter [Was bedeutet die Anzeige?](hardware-specification.md#what-does-the-light-mean).

    ![Sämtliche Gerätefeatures](./media/quickstarts/full-device-features.png)

## <a name="download-the-sdk"></a>Herunterladen des SDK

1. Laden Sie [hier](sensor-sdk-download.md) das SDK herunter.
2. Installieren Sie das SDK auf Ihrem PC.

## <a name="update-firmware"></a>Aktualisieren der Firmware

Die Gerätefirmware muss auf dem neuesten Stand sein, damit das SDK ordnungsgemäß funktioniert. Eine Anleitung zum Überprüfen und Aktualisieren der Firmwareversion finden Sie unter [Aktualisieren der Azure Kinect DK-Firmware](update-device-firmware.md).

## <a name="verify-that-the-device-streams-data"></a>Überprüfen des Datenstreamings des Geräts

1. Starten Sie den [Azure Kinect Viewer](azure-kinect-viewer.md). Dieses Tool kann wie folgt gestartet werden:
   - Sie können den Viewer über die Befehlszeile oder per Doppelklick auf die ausführbare Datei starten. Die Datei `k4aviewer.exe` befindet sich im Verzeichnis „tools“ des SDK (also beispielsweise unter `C:\Program Files\Azure Kinect SDK vX.Y.Z\tools\k4aviewer.exe`, wobei `X.Y.Z` die installierte SDK-Version ist).
   - Sie können den Azure Kinect Viewer über das Startmenü des Geräts starten **.**
2. Wählen Sie in Azure Kinect Viewer **Gerät öffnen** > **Start** aus.

    ![Azure Kinect Viewer](./media/quickstarts/viewer.png)

3. Vergewissern Sie sich, dass alle Sensordatenströme im Tool visualisiert werden:
   - Tiefenkamera
   - Farbkamera
   - Infrarotkamera
   - IME
   - Mikrofone

    ![Visualisierungstool](./media/quickstarts/visualization-tool.png)

Damit ist die Einrichtung Ihres Azure Kinect DK-Geräts abgeschlossen. Sie können nun mit der Entwicklung Ihrer Anwendung oder mit der Integration von Diensten beginnen.

Lesen Sie bei Problemen den Artikel [Bekannte Probleme und Problembehandlung für Azure Kinect](troubleshooting.md).

## <a name="see-also"></a>Siehe auch

- [Hardwarespezifikationen für Azure Kinect DK](hardware-specification.md)
- [Aktualisieren der Gerätefirmware](update-device-firmware.md)
- [Azure Kinect Viewer](azure-kinect-viewer.md)

## <a name="next-steps"></a>Nächste Schritte

Wenn Azure Kinect DK fertig eingerichtet und einsatzbereit ist, können Sie mit dem folgenden Artikel fortfahren:
> [!div class="nextstepaction"]
> [Erfassen von Sensordatenströmen in einer Datei](record-sensor-streams-file.md)
