---
title: Verbinden eines Azure Percept über 5G- oder LTE-Netzwerke per USB-Modem
description: Dieser Artikel beschreibt, wie Sie ein Azure Percept DK per USB-Modem über 5G- oder LTE-Netzwerke verbinden.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 2e9fb2ae534d11fb0a85199baf7256ab174e3f25
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007248"
---
# <a name="connect-azure-percept-over-5g-or-lte-networks-using-a-usb-modem"></a>Verbinden eines Azure Percept über 5G- oder LTE-Netzwerke per USB-Modem

Im Folgenden finden Sie Schritte zum Vorbereiten und Verbinden eines Azure Percept DK mit 5G- oder LTE-Netzwerken per USB-Modem. Diese Anweisungen gelten nur für die spezielle Azure Percept DK-Software, die Sie gemäß der folgenden Anweisungen herunterladen können. Dieses spezielle Azure Percept-Image enthält die Open-Source-Software ModemManager, die eine Vielzahl von USB-Modems unterstützt. HINWEIS: Dieses Image unterstützt keine OTA-Updates für das Betriebssystem oder andere Software. Mit dieser Software können Sie ein einfaches kostengünstiges LTE-USB-Modem oder ein fortgeschritteneres 5G-Modell verwenden, um Ihr Azure Percept mit dem Internet und Azure zu verbinden. 

> [!Note]
> Diese Anweisungen sind für die Verwendung mit USB-Modems vorgesehen, die die MBIM-Schnittstelle unterstützen. Stellen Sie sicher, ein ein USB-Modem zu erwerben, das die MBIM-Schnittstelle unterstützt. Stellen Sie dann sicher, dass es auf der Liste der von ModemManager unterstützten Modems aufgeführt ist. ModemManager kann mit anderen Schnittstellen verwendet werden, aber wir konzentrieren uns auf die MBIM-Schnittstelle. Weitere Informationen zu ModemManager finden Sie hier: https://www.freedesktop.org/wiki/Software/ModemManager/


:::image type="Image" source="media/connect-over-cellular/azure-percept-all-modems.png" alt-text="Mit einem Azure Percept verbundene USB-Modems":::

## <a name="setting-up-the-devkit-for-using-usb-modem"></a>Das Devkit für die Verwendung eines USB-Modems einrichten

- **Laden Sie das Azure Percept-Image mit ModemManager-Unterstützung herunter,**  - [Gezippte Azure Percept 5G-Imagedateien](https://aka.ms/azpercept5gimage), um die drei Dateien zu erhalten, die Sie für das Update Ihres Azure Percept zur Unterstützung von USB-Modems benötigen.
- **Aktualisieren Sie Ihr Azure Percept mit den heruntergeladenen Dateien mithilfe der USB-Methode**: Folgen Sie den [Anweisungen zum Update per USB](./how-to-update-via-usb.md), um Ihr DevKit mit der speziellen LTE/5G-fähigen Software zu aktualisieren, die Sie im vorherigen Schritt heruntergeladen haben. Denken Sie daran, NUR die Dateien zu verwenden, die Sie im *vorherigen Schritt* heruntergeladen haben, und nicht die in den Anweisungen zum Update per USB beschriebenen Dateien.
- **Gehen Sie den normalen Einrichtungsprozess durch und verwenden Sie** - [Einrichten des Azure Percept DK-Geräts](./quickstart-percept-dk-set-up.md), wenn Ihnen dieser nicht bekannt ist. Das Vorgehen zum Einrichten ist das gleiche wie bei dieser ModemManager-fähigen Version von Azure Percept.
- **Verbinden Sie sich gemäß der Anweisungen unter** - [Herstellen einer Verbindung mit Azure Percept DK über SSH](./how-to-ssh-into-percept-dk.md) über SSH mit Ihrem Azure Percept.

## <a name="step-by-step-instructions-for-connecting-three-different-modems"></a>Schrittweise Anleitungen zum Verbinden drei verschiedener Modems

Im Folgenden finden Sie Anweisungen für drei verschiedene USB-Modems. Der erste ist ein einfaches USB-Dongle mit LTE CAT-4 (Vodafone) ohne besondere Features. Die Anweisungen für dieses Modem können auch für ähnliche einfache kostengünstige USB-Modems verwendet werden. Das zweite (MultiTech) ist ein Beispiel für ein USB-Modem mit unterschiedlichen USB-Betriebsmodi. Für diese Art von Modem müssen Sie zuerst den richtigen USB-Modus aktivieren, um die von ModemManager unterstützt MBIM-Schnittstelle zu nutzen. Das dritte ist ein 5G-Modem (Quectel DK) und verfügt ebenfalls über unterschiedliche Modi. Sie müssen zuerst den MBIM-Modus aktivieren.  

### <a name="vodafone-usb-connect-4g-v2-modem"></a>Vodafone USB Connect 4G v2-Modem
:::image type="Image" source="media/connect-over-cellular/vodafone-usb-modem-75.png" alt-text="USB-Modem von Vodafone":::

Dies sind die Schritte zum Verbinden Ihres Azure Percept DK mithilfe eines einfachen USB-Modems wie dem Vodafone USB Connect 4G v2.

[Verbinden über ein Vodafone Connect 4G-USB-Modem](./connect-over-cellular-usb-vodafone.md)   

### <a name="multitech-multiconnect-usb-modem"></a>MultiTech Multiconnect-USB-Modem
:::image type="Image" source="media/connect-over-cellular/multitech-usb-modem-75.png" alt-text="MultiTech-USB-Modem":::

Anweisungen zum Verbinden Ihres Azure Percept DK mithilfe eines einfachen USB-Modems wie dem MultiTech-USB-Modem (MTCM-LNA3-B03) finden Sie hier:

[Verbinden mit einem Multitech-USB-Modem](./connect-over-cellular-usb-multitech.md)   

### <a name="quectel-5g-developer-kit"></a>Quectel 5G Developer Kit
:::image type="Image" source="media/connect-over-cellular/quectel-5-g-dk-75.png" alt-text="Quectel 5G DK":::

Anweisungen zum Verbinden Ihres Azure Percept DK mithilfe eines 5G-USB-Modems wie dem Quectel RM500Q-GL finden Sie hier:

[Verbinden mit einem Quectel 5G Developer Kit](./connect-over-cellular-usb-quectel.md) 

## <a name="how-to-make-your-lte5g-connection-recover-from-reboot"></a>Wiederherstellen der LTE/5G-Verbindung nach einem Neustart 
Mit den obigen Anweisungen können Sie das USB-Modem für die Verbindung mit dem Netzwerk konfigurieren, aber wenn Sie Ihr Gerät neu starten, müssen Sie die Verbindung manuell wiederherstellen. Wir arbeiten an einer Lösung, um dies zu verbessern. Wenn Sie weitere Informationen erhalten möchten, senden Sie eine E-Mail mit einem Verweis auf diesen Artikel an azpercept5G@microsoft.com. 

## <a name="debugging-information"></a>Debuginformationen 
Denken Sie immer daran, zu überprüfen, ob Ihre SIM-Karte mit der spezifischen Hardware funktioniert, die Sie verwenden möchten. Bei einigen Netzbetreiben können auf Datenverbindungen beschränkte IoT-SIM-Karten nur für ein Gerät genutzt werden. Wenn dies der Fall ist, müssen Sie sicherstellen, dass der Netzbetreiber die IMEI/Seriennummer Ihres Geräts in der „Liste zugelassener Geräte“ der SIM-Karte aufgeführt hat.

### <a name="modemmanager-debug-mode"></a>ModemManager-Debugmodus

Der Debugmodus von ModemManager kann aktiviert werden, indem die Datei `/lib/systemd/system/ModemManager.service` in der Zeile `ExecStart=/usr/sbin/ModemManager [...]` bearbeitet wird, um `--debug` anzufügen, wie im folgenden Beispiel gezeigt:
```  
[...]  
ExecStart=/usr/sbin/ModemManager [...] --debug  
[...]  
```
Anschließend müssen Sie Dienste erneut laden und ModemManager neu starten, damit die Änderungen wirksam werden:
```
systemctl daemon-reload
systemctl restart ModemManager
```
Im Folgenden finden Sie einige Befehle, mit denen Sie die Protokolle anzeigen und die Protokolldateien bereinigen können:
```
journalctl -u ModemManager.service
journalctl --rotate
journalctl --vacuum-time=1s

```

### <a name="reliability-and-stability-enhancement"></a>Verbesserung der Zuverlässigkeit und Stabilität

#### <a name="strict-mode"></a>Strict-Modus
Um zu verhindern, dass ModemManager mit seriellen Schnittstellen interagiert, die kein Modem sind, können Sie Schnittstellen von der Untersuchung (zum Erkennen von Modems) ausschließen, indem Sie die [Filterrichtlinien ändern](https://www.freedesktop.org/software/ModemManager/api/latest/ch03s02.html). Wir empfehlen die Verwendung des `STRICT`-Modus.

Dazu müssen Sie `/lib/systemd/system/ModemManager.service` in der Zeile `ExecStart=/usr/sbin/ModemManager [...]` bearbeiten und `--filter-policy=STRICT` hinzufügen, wie im folgenden Beispiel gezeigt:
```
[...]
ExecStart=/usr/sbin/ModemManager --filter-policy=STRICT
[...]
```
Anschließend müssen Sie Dienste erneut laden und ModemManager neu starten, damit die Änderungen wirksam werden:
```
systemctl daemon-reload
systemctl restart ModemManager
```

## <a name="next-steps"></a>Nächste Schritte
Sehen Sie sich nun die Anweisungen für verschiedene USB-Modems an:

[Verbinden über Vodafone Connect 4G und USB-Modem](./connect-over-cellular-usb-vodafone.md)

[Verbinden über Multitech-USB-Modem](./connect-over-cellular-usb-multitech.md)

[Verbinden über Quectel 5G Developer Kit](./connect-over-cellular-usb-quectel.md)

Zurück zum Hauptartikel über 5G oder LTE:

[Verbinden über 5G oder LTE](./connect-over-cellular.md)

[Verbinden über Mobilfunkgateway](./connect-over-cellular-gateway.md)
