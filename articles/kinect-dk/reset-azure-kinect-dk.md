---
title: Zurücksetzen von Azure Kinect DK
description: Hier erfahren Sie, wie Sie ein Azure Kinect DK-Gerät auf das Werksimage zurücksetzen.
author: martinekuan
ms.author: martinek
ms.reviewer: jarrettr
ms.prod: kinect-dk
ms.date: 02/11/2020
ms.topic: how-to
keywords: Kinect, zurücksetzen
ms.custom: CI 113492, CSSTroubleshooting
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: 821b9e5b82f5fe3a227e3806c29917c06ebca51e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131079527"
---
# <a name="reset-azure-kinect-dk"></a>Zurücksetzen von Azure Kinect DK

Es kann vorkommen, dass Ihr Azure Kinect DK-Gerät auf das Werksimage zurückgesetzt werden muss – beispielsweise, wenn ein Firmwareupdate nicht ordnungsgemäß installiert wurde.

1. Schalten Sie Ihr Azure Kinect DK-Gerät aus. Entfernen Sie dazu das USB-Kabel und das Netzkabel.
  ![Diagramm mit der Position der Schraube, die sich über der Taste zum Zurücksetzen befindet](media/reset-azure-kinect-dk-diagram.png)
1. Entfernen Sie die Schraube, die sich in der Arretiervorrichtung für die Stativmontage befindet.
1. Schließen Sie das Netzkabel wieder an.
1. Führen Sie die Spitze einer aufgebogenen Büroklammer in das leere Schraubenloch der Arretiervorrichtung für die Stativmontage ein.
1. Drücken Sie mithilfe der Büroklammer vorsichtig die Taste zum Zurücksetzen, und halten Sie sie gedrückt.
1. Schließen Sie das USB-Kabel wieder an, während Sie die Taste zum Zurücksetzen weiter gedrückt halten.
1. Nach etwa drei Sekunden wird die Betriebsanzeige gelb. Lassen Sie daraufhin die Taste zum Zurücksetzen los.  
   
   Nach dem Loslassen der Taste zum Zurücksetzen blinkt die Betriebsanzeige abwechselnd weiß und gelb, während das Gerät zurückgesetzt wird. 
1. Warten Sie, bis die Betriebsanzeige weiß leuchtet.
1. Setzen Sie die Schraube wieder in die Arretiervorrichtung für die Stativmontage ein (über der Taste zum Zurücksetzen).
1. Vergewissern Sie sich im Azure Kinect Viewer, dass die Firmware zurückgesetzt wurde. Starten Sie hierzu den [Azure Kinect Viewer](azure-kinect-viewer.md), und wählen Sie **Device firmware version info** (Informationen zur Firmwareversion des Geräts) aus, um die auf Ihrem Azure Kinect DK-Gerät installierte Firmwareversion anzuzeigen.

Achten Sie darauf, dass auf dem Gerät immer die neueste Firmware installiert ist. Die aktuelle Firmwareversion kann mithilfe des Azure Kinect-Firmwaretools abgerufen werden. Weitere Informationen zum Überprüfen des Firmwarestatus finden Sie unter [Überprüfen der Firmwareversion des Geräts](azure-kinect-firmware-tool.md#check-device-firmware-version).

## <a name="related-topics"></a>Zugehörige Themen

- [Informationen zu Azure Kinect DK](about-azure-kinect-dk.md)
- [Schnellstart: Einrichten Ihres Azure Kinect DK-Geräts](set-up-azure-kinect-dk.md)
- [Hardwarespezifikationen Kinect Azure Kinect DK: Betriebsumgebung](hardware-specification.md#operating-environment)
- [Azure Kinect DK-Firmwaretool](azure-kinect-firmware-tool.md)
- [Azure Kinect Viewer](azure-kinect-viewer.md)
- [Geräteübergreifende Synchronisierung für Azure Kinect DK](multi-camera-sync.md)
