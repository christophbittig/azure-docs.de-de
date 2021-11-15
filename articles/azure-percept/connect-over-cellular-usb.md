---
title: Verbinden von Azure Percept DK über 5G- und LTE-Netzwerke mithilfe eines USB-Modems
description: In diesem Artikel wird erläutert, wie Sie Azure Percept DK über 5G- und LTE-Netzwerke mithilfe eines USB-Modems verbinden können.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 4bb866c25c81b08be7d4769d33b7d49b3ac6849f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131440018"
---
# <a name="connect-azure-percept-dk-over-5g-and-lte-networks-by-using-a-usb-modem"></a>Verbinden von Azure Percept DK über 5G- und LTE-Netzwerke mithilfe eines USB-Modems

In diesem Artikel wird erläutert, wie Sie Azure Percept DK mithilfe eines USB-Modems mit 5G- und LTE-Netzwerken verbinden können. 

> [!NOTE]
> Die Informationen in diesem Artikel gelten nur für spezielle Azure Percept DK Software, die Sie gemäß den Anweisungen im nächsten Abschnitt herunterladen können. Ein spezielles Azure Percept DK-Image enthält die Open-Source-Software ModemManager, die eine Vielzahl von USB-Modems unterstützt. Das Image unterstützt keine OTA-Updates (Over-the-Air) für das Betriebssystem oder andere Software. Mit der Open-Source-Software ModemManager können Sie ein einfaches, kostengünstiges LTE-USB-Modem oder komplexere 5G-Modems verwenden, um Azure Percept DK mit dem Internet und Azure zu verbinden. 
>
> Die Anweisungen in diesem Artikel sind für die Verwendung mit USB-Modems vorgesehen, die eine MBIM-Schnittstelle (Mobile Broadband Interface Model) unterstützen. Bevor Sie sich für ein USB-Modem entscheiden, stellen Sie sicher, dass es die MBIM-Schnittstelle unterstützt. Stellen Sie außerdem sicher, dass es in der ModemManager-Liste der unterstützten Modems aufgeführt ist. Die ModemManager-Software kann auch mit anderen Schnittstellen verwendet werden. In diesem Artikel konzentrieren wir uns allerdings nur auf die MBIM-Schnittstelle. Weitere Informationen finden Sie auf der Seite [freedesktop.org ModemManager](https://www.freedesktop.org/wiki/Software/ModemManager/).


:::image type="Image" source="media/connect-over-cellular/azure-percept-all-modems-v2.png" alt-text="Abbildung: Azure Precept DK mittels USB-Modems zum Herstellen einer Verbindung mit 5G- und LTE-Netzwerken":::

## <a name="set-up-azure-percept-dk-to-use-a-usb-modem"></a>Einrichten von Azure Percept DK für die Verwendung eines USB-Modems

1. [Laden Sie das Azure Percept 5G-Softwareimage](https://aka.ms/azpercept5gimage) herunter, das ModemManager unterstützt. 

   Diese drei Dateien sind erforderlich, um Ihre Azure Percept DK Software so zu aktualisieren, dass sie USB-Modems unterstützt.

1. [Aktualisieren Sie die Azure Percept DK Software](./how-to-update-via-usb.md) mit der speziellen 5G-/LTE-fähigen Azure Percept DK Software, die Sie im vorherigen Schritt heruntergeladen haben. 

   > [!IMPORTANT]
   > Befolgen Sie die Anweisungen unter [Aktualisieren von Azure Percept DK über eine USB-C-Verbindung](./how-to-update-via-usb.md). Verwenden Sie allerdings *nur* die Dateien, die Sie im vorherigen Schritt heruntergeladen haben, und nicht die in diesem Artikel erwähnten Dateien.

1. Befolgen Sie den normalen Prozess zum [Einrichten des Azure Percept DK Geräts](./quickstart-percept-dk-set-up.md), wenn es Ihnen nicht vertraut ist. 

   Das Vorgehen zum Einrichten ist das gleiche wie bei dieser ModemManager-fähigen Version von Azure Percept DK.

1. [Stellen Sie eine Verbindung mit Azure Percept DK mithilfe des SSH-Netzwerkprotokolls (Secure Shell) her](./how-to-ssh-into-percept-dk.md).

## <a name="connect-to-a-modem"></a>Verbindung mit einem Modem herstellen

Die nächsten drei Abschnitte enthalten Anweisungen, um Verbindungen mit verschiedenen USB-Modems herzustellen.  

### <a name="vodafone-usb-connect-4g-v2-modem"></a>Vodafone USB Connect 4G v2-Modem

Dieses USB-Modem von Vodafone ist ein einfacher LTE CAT-4 USB Dongle, der keine besonderen Funktionen aufweist. Die Anweisungen für dieses Modem können auch für andere ähnlichgeartete einfache kostengünstige USB-Modems verwendet werden.

:::image type="Image" source="media/connect-over-cellular/vodafone-usb-modem-75.png" alt-text="Abbildung: Obere und untere Ansicht eines USB-Modems der Version 4G v2 von Vodafone":::

Anweisungen zum Verbinden Ihrer Azure Percept DK mithilfe eines einfachen USB-Modems (z. B. USB Connect 4G v2 von Vodafone) finden Sie unter [Verbindung mithilfe des 4G v2 USB Modems von Vodafone herstellen](./connect-over-cellular-usb-vodafone.md).   

### <a name="multitech-multiconnect-usb-modem"></a>MultiTech Multiconnect-USB-Modem

Dieses MultiTech-USB-Modem bietet mehrere USB-Betriebsmodi. Für diese Art von Modem müssen Sie zunächst den richtigen USB-Modus aktivieren. Erst danach können Sie die von ModemManager unterstützte MBIM-Schnittstelle aktivieren.

:::image type="Image" source="media/connect-over-cellular/multitech-usb-modem-75.png" alt-text="Abbildung: MultiTech Multiconnect-USB-Modem":::

Um Azure Percept DK mithilfe eines einfachen USB-Modems wie dem MultiTech-USB-Modem (MTCM-LNA3-B03) zu verbinden, befolgen Sie die Anweisungen in [Verbindung mithilfe des MultiTech-USB-Modems herstellen](./connect-over-cellular-usb-multitech.md).

### <a name="quectel-5g-developer-kit"></a>Quectel 5G Developer Kit

Das dritte Modem ist das Quectel 5G DK. Es bietet auch mehrere Modi und Sie müssen zuerst den richtigen MBIM-Modus aktivieren.

:::image type="Image" source="media/connect-over-cellular/quectel-5-g-dk-75.png" alt-text="Abbildung: USB-Modem Quectel 5G D":::

Anweisungen zum Verbinden Ihrer Azure Percept DK mithilfe eines 5G-USB-Modems (z. B. Quectel RM500Q-GL) finden Sie unter [Verbindung mit dem Quectel 5G Developer Kit herstellen](./connect-over-cellular-usb-quectel.md). 

## <a name="help-your-5g-or-lte-connection-recover-from-reboot"></a>Wiederherstellen Ihrer 5G- oder LTE-Verbindung nach einem Neustart 
Sie können das USB-Modem für die Verbindung mit dem Netzwerk konfigurieren. Wenn Sie Ihr Gerät neu starten, müssen Sie die Verbindung allerdings manuell wiederherstellen. Wir arbeiten derzeit an einer Lösung, um diesen Prozess zu verbessern. Für weitere Informationen wenden Sie sich mit einem kurzen Hinweis auf dieses Problems an [unser Supportteam](mailto:azpercept5G@microsoft.com). 

## <a name="debugging-information"></a>Debuginformationen 
Vergewissern Sie sich, dass Ihre SIM-Karte auf der spezifischen Hardware funktioniert, die Sie verwenden möchten. Bei einigen Netzbetreibern können IoT-SIM-Karten, die auf reine Datenverbindungen beschränkt sind, nur für ein Gerät genutzt werden. Stellen Sie aus diesem Grund sicher, dass die IMEI (International Mobile Equipment Identity) oder die Seriennummer Ihres Geräts in der Liste der zulässigen SIM-Kartengeräte des Netzbetreibers aufgeführt ist.

### <a name="modemmanager-debug-mode"></a>ModemManager-Debugmodus

Sie können den ModemManager-Debugmodus aktivieren, indem Sie in der Datei */lib/systemd/system/ModemManager.service* in der Zeile `ExecStart=/usr/sbin/ModemManager [...]` `--debug` anfügen (siehe folgendes Beispiel):

```  
[...]  
ExecStart=/usr/sbin/ModemManager [...] --debug  
[...]  
```

Damit Ihre Änderungen wirksam werden, laden Sie (wie hier gezeigt) die Dienste erneut und starten Sie auch den ModemManager neu:

```
systemctl daemon-reload
systemctl restart ModemManager
```

Durch Ausführen der folgenden Befehle können Sie die Protokolle anzeigen und die Protokolldateien bereinigen:

```
journalctl -u ModemManager.service
journalctl --rotate
journalctl --vacuum-time=1s

```

### <a name="enhance-reliability-and-stability"></a>Verbesserung der Zuverlässigkeit und Stabilität

Um zu verhindern, dass ModemManager mit seriellen Schnittstellen interagiert, die kein Modem sind, können Sie Schnittstellen von der Untersuchung (zum Erkennen von Modems) ausschließen, indem Sie die [Filterrichtlinien](https://www.freedesktop.org/software/ModemManager/api/latest/ch03s02.html) ändern. 

Es empfiehlt sich, den Modus `STRICT` zu verwenden.

Bearbeiten Sie hierzu die Datei */lib/systemd/system/ModemManager.service* und fügen Sie in der Zeile `ExecStart=/usr/sbin/ModemManager [...]` wie in folgendem Beispiel gezeigt `--filter-policy=STRICT` an:

```
[...]
ExecStart=/usr/sbin/ModemManager --filter-policy=STRICT
[...]
```
Damit Ihre Änderungen wirksam werden, laden Sie (wie hier gezeigt) die Dienste erneut und starten Sie auch den ModemManager neu:

```
systemctl daemon-reload
systemctl restart ModemManager
```

## <a name="next-steps"></a>Nächste Schritte

* [Verbinden mittels 5G oder LTE](./connect-over-cellular.md)
* [Verbinden mithilfe eines Mobilfunkgateways](./connect-over-cellular-gateway.md)
