---
title: Verbinden von Azure Percept über 5G- oder LTE-Netzwerke
description: In diesem Artikel wird erläutert, wie Sie das Azure Percept DK über 5G- oder LTE-Netzwerke verbinden.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/23/2021
ms.custom: template-how-to
ms.openlocfilehash: 0df673937ebd195c250a0f0a8880ecf89d42c844
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129997229"
---
# <a name="connect-azure-percept-over-5g-or-lte-networks"></a>Verbinden von Azure Percept über 5G- oder LTE-Netzwerke

Die Verbindung von Edge-KI-Geräten über 5G- oder LTE-Netzwerke bietet zahlreiche Vorteile. Edge-KI ist in Szenarien besonders effektiv, in denen WLAN- und LAN-Konnektivität nur eingeschränkt vorhanden ist, z. B. in intelligenten Städten, autonomen Fahrzeugen und der Landwirtschaft. Darüber hinaus bieten 5G- oder LTE-Netzwerke mehr Sicherheit als ein WLAN. Und schließlich ermöglicht die Verwendung von IoT-Geräten, die KI am Edge ausführen, die Optimierung der Bandbreite von 5G- oder LTE-Netzwerken. Dabei werden nur notwendige Informationen an die Cloud gesendet, der größte Teil der Daten wird auf dem Gerät verarbeitet. Mittlerweile unterstützt Azure Percept DK sogar direkte Verbindungen mit 5G-/LTE-Netzwerken mithilfe eines einfachen USB-Modems. Im Folgenden finden Sie weitere Informationen zu den verschiedenen Optionen.

## <a name="options-for-connecting-azure-percept-dk-over-5g-or-lte-networks"></a>Optionen zum Verbinden des Azure Percept DK über 5G- oder LTE-Netzwerke
Mit zusätzlicher Hardware können Sie das Azure Percept DK über 5G/LTE-Konnektivität verbinden. Derzeit werden drei Optionen unterstützt. Hier finden Sie einen Link zu mehr Details für jede Option:
- **5G/LTE USB-Modem:** Ein neues SW-Image wurde veröffentlicht, das Open-Source-ModemManager SW unterstützt, das unserem Linux-Betriebssystem USB-Modemunterstützung hinzufügt. Auf diese Weise können Sie Azure Percept über LTE- oder 5G-Netzwerke über verschiedene, häufig kostengünstige USB-Modems verbinden. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mithilfe eines USB-Modems](./connect-over-cellular-usb.md).   
- **5G/LTE Ethernet-Gateway**: Hierbei wird Azure Percept über Ethernet mit dem 5G/LTE-Gateway verbunden. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mithilfe eines Mobilfunkgateways](./connect-over-cellular-gateway.md).
- **5G/LTE WLAN-Hotspot**: Hierbei wird Azure Percept mit dem WLAN verbunden, das vom WLAN-Hotspot bereitgestellt wird. In diesem Fall stellt das Development Kit die Verbindung genauso her wie mit jedem anderen WLAN auch. Weitere Informationen finden Sie im [Einrichtungsleitfaden für Azure Percept DK](./quickstart-percept-dk-set-up.md). Wählen Sie das 5G/LTE-WLAN aus, das vom Hotspot übertragen wird.


## <a name="considerations-when-selecting-a-5g-or-lte-device-in-general"></a>Allgemeine Überlegungen bei der Auswahl eines 5G- oder LTE-Geräts
5G/LTE-Geräte, unabhängig davon, ob es sich um USB-Modems oder Ethernet-Gateways handelt, unterstützen verschiedene Technologien, die sich auf die maximale Datenrate für Downloads und Uploads auswirken. Die beworbenen Datenraten können zur Orientierung bei der Entscheidung dienen, werden aber in aller Regel nicht erreicht. Im Folgenden finden Sie einige Tipps für die Auswahl des richtigen Geräts für Ihre Anforderungen.
 
- **LTE CAT-1** bietet bis zu 10 Mbit/s für Downloads und 5 Mbit/s für Uploads. Diese Übertragungsraten genügen für die Standardfeatures des Azure Percept DK wie etwa die Objekterkennung und das Erstellen eines Sprach-Assistenten. Für Lösungen, bei denen Videodaten in die Cloud gestreamt werden müssen, ist dieser Standard möglichweise nicht ausreichend.
- **LTE CAT-3 und 4** bieten bis zu 100 Mbit/s für Downloads und 50 Mbit/s für Uploads. Das genügt zum Streamen von Videodaten in die Cloud. Zum Streamen von Videos in Full-HD-Qualität reichen diese Raten jedoch nicht aus.
- **LTE CAT-5 und höher** bieten Datenraten, die zum Streamen von HD-Videodaten für ein einzelnes Gerät ausreichen. Wenn Sie mehrere Geräte an ein einziges Gateway anschließen müssen, sollten Sie 5G in Erwägung ziehen.
- **5G**-Gateways sind eine sichere Basis, damit Ihre Szenarien auch in Zukunft reibungslos funktionieren. Dieser Standard bietet Datenübertragungsraten und Bandbreiten, mit denen sich ein hoher Datendurchsatz für mehrere Geräte gleichzeitig realisieren lässt. Darüber hinaus sorgen sie auch für niedrigere Latenzen bei der Datenübertragung.


## <a name="next-steps"></a>Nächste Schritte
Je nachdem, auf welches Mobilfunkgerät Sie Zugriff haben, folgen Sie diesen Links, um Ihr Azure Percept Dev Kit zu verbinden:

[Verbinden über Mobilfunkgateway](./connect-over-cellular-gateway.md)

[Verbinden über USB-Modem](./connect-over-cellular-usb.md)
