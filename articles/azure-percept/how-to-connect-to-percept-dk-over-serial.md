---
title: Herstellen einer seriellen Verbindung mit Azure Percept DK
description: Einrichten einer seriellen Verbindung mit Ihrem Azure Percept DK mit einem seriellen USB-zu-TTL-Kabel
author: MrHamlet
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/03/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 47c3c4d5a696087164ef4992ae75e5433de86722
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131006314"
---
# <a name="connect-to-azure-percept-dk-over-serial"></a>Herstellen einer seriellen Verbindung mit Azure Percept DK

Führen Sie die folgenden Schritte aus, um über [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)eine serielle Verbindung mit Ihrem Azure Percept DK einzurichten.

> [!WARNING]
> **Wenn Sie ein Development Kit in der privaten Vorschau haben**, sollten Sie **NICHT** versuchen, Ihr Dev Kit über die serielle Schnittstelle zu verbinden, außer in extremen Ausnahmefällen (z. B. wenn Sie Ihr Gerät funktionsuntüchtig gemacht haben). Zum Herstellen einer seriellen Verbindung muss das Development Kit in privater Vorschau für den Zugriff auf die GPIO-Stifte disassembliert werden. Das Zerlegen des Trägerplatinengehäuses ist sehr schwierig und könnte zur Beschädigung der WLAN-Antennenkabel führen.

## <a name="prerequisites"></a>Voraussetzungen

- [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)
- Host-PC
- Azure Percept DK
- [Serielles USB-zu-TTL-Kabel](https://www.adafruit.com/product/954)

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/usb-serial-cable.png" alt-text="Serielles USB-zu-TTL-Kabel.":::

## <a name="start-the-serial-connection"></a>Starten der seriellen Verbindung

1. Verbinden Sie wie unten gezeigt das [serielle USB-zu-TTL-Kabel](https://www.adafruit.com/product/954) mit den drei GPIO-Stiften auf der Hauptplatine.

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/apdk-serial-pins.jpg" alt-text="Serielle Stiftverbindungen auf der Trägerplatine.":::

1. Schalten Sie Ihr Development Kit ein, und verbinden Sie das USB-Ende des seriellen Kabels mit Ihrem PC.

1. Rufen Sie unter Windows **Start** -> **Windows Update-Einstellungen** -> **Optionale Updates anzeigen** -> **Treiberupdates** auf. Suchen Sie in der Liste nach einem Update für „Seriell zu USB“, aktivieren Sie das Kontrollkästchen neben dem Update, und wählen Sie **Herunterladen und installieren** aus.  

1. Öffnen Sie als nächstes den Windows-Geräte-Manager (**Start** -> **Geräte-Manager**). Wechseln Sie zu **Ports**, und wählen Sie **USB zu UART** aus, um die **Eigenschaften** zu öffnen. Beachten Sie, mit welchem COM-Port Ihr Gerät verbunden ist.

1. Wählen Sie die Registerkarte **Porteinstellungen** aus, und stellen Sie sicher, dass **Bits pro Sekunde** auf 115.200 festgelegt ist.

1. Öffnen Sie PuTTY. Geben Sie Folgendes ein, und wählen Sie **Öffnen** aus, um eine serielle Verbindung mit Ihrem DevKit herzustellen:

    1. Serielle Leitung: COM[port #]
    1. Geschwindigkeit: 115200
    1. Verbindungstyp: Seriell

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/putty-serial-session.png" alt-text="PuTTY-Sitzungsfenster mit ausgewählten seriellen Parametern.":::
