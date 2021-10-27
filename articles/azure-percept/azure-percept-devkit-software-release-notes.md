---
title: Versionshinweise zur Azure Percept DK-Software
description: Dieser Artikel enthält Informationen zu Änderungen an der Azure Percept DK-Software.
author: EthanChangAED
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 08/23/2021
ms.custom: template-concept
ms.openlocfilehash: dd0d2c3e8466d0f5b4486be7c15b0092d7026a5e
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129996245"
---
# <a name="azure-percept-dk-software-release-notes"></a>Versionshinweise zur Azure Percept DK-Software

Diese Seite enthält Informationen zu Änderungen und Fixes für jedes Azure Percept DK-Betriebssystem und Firmwarerelease.

Navigieren Sie zu [Azure Percept DK-Softwarereleases zum Aktualisieren über USB](./software-releases-usb-cable-updates.md) oder [Azure Percept DK-Softwarereleases für das OTA-Update](./software-releases-over-the-air-updates.md), um die Updateimages herunterzuladen.

## <a name="september-2109-release"></a>Release vom September (2109)

- WLAN:
  - Verwenden eines festen Kanals anstelle der automatischen Kanalauswahl, um zu vermeiden, dass hostapd.service ständig wiederholen und neu starten muss.
- Setup:
  - OOBE-Serversystemfehler sind lokalisiert.
  - Unterstützung für mehrere IPv6-Routingtabellen.
- Betriebssystem
  - Neueste Sicherheitsfixes.
  - Der Nginx-Dienst wird als Nicht-Root-Benutzer ausgeführt.


## <a name="july-2107-release"></a>Release von Juli (2107)

> [!IMPORTANT]
> Aufgrund einer Codesignierungsänderung ist das OTA-Paket (Over-the-Air) für dieses Release nur mit der Azure Percept DK-Version kompatibel, die das Release 2106 ausführt. Für Azure Percept DK-Benutzer*innen, die derzeit eine ältere Softwarereleaseversion ausführen, empfiehlt Microsoft vor dem Update auf Version 2107 das Durchführen eines Updates über ein USB-Kabel oder ein OTA-Update auf Release 2106.

- WLAN:
  - Durch die Sicherheitshärtung wird sichergestellt, dass der WLAN-Zugriffspunkt nach Abschluss des Setups heruntergefahren wird.
  - Es wurde ein Problem behoben, bei dem das Klicken auf die Schaltfläche **Setup** im Development Kit dazu führte, dass der WLAN-Zugriffspunkt des Development Kit nicht mehr synchron mit dem Setup des Webdiensts war.
  - Die IPTables-Regeln für den WLAN-Zugriffspunkt wurden so optimiert, dass der Zugriffspunkt resilienter ist und unnötige Regeln entfernt werden.
  - Es wurde ein Problem behoben, bei dem mehrere WLAN-Verbindungen nicht ordnungsgemäß priorisiert wurden.
- Setup:
  - Die Lokalisierung für unterstütze Regionen wurde hinzugefügt, und der Text wurde für bessere Lesbarkeit aktualisiert.
  - Es wurde ein Problem behoben, bei dem das Setup in manchen Fällen auf der Ladeseite hängen geblieben ist.
- Allgemeines Netzwerk:
  - Es wurden Probleme mit dem IPv6 behoben, durch die keine gültige DHCP-Leasedauer abgerufen wurde.
- Betriebssystem:
  - Sicherheitskorrekturen.

## <a name="june-2106-release"></a>Release von Juni (2106)

- Der Imageüberprüfungsmechanismus für den OTA-Agent wurde aktualisiert.
- Die Benutzeroberfläche wurde optimiert, und Setupfehler wurden behoben.

## <a name="may-2105-release"></a>Release von Mai (2105)

- Sicherheitsupdates für das CBL-Mariner-Betriebssystem

## <a name="april-2104-release"></a>Release von April (2104)

- Es wurde ein Problem mit der Protokollrotation behoben, das möglicherweise dafür sorgt, dass der Azure Percept DK-Speicher voll ist.
- Im Setup wurde die TPM-basierte Bereitstellung in Azure aktiviert.
- Dem Setup wurde ein automatisches Timeout und ein WLAN-Zugriffspunkt hinzugefügt. Dieses wird nach 30 Minuten oder nach Abschluss des Setups aktiviert.
- Die SSID für den WLAN-Zugriffspunkt wurde von „**scz-[xxx]** “ in „**apd-[xxx]** “ geändert.

## <a name="next-steps"></a>Nächste Schritte

- [Bestimmen Ihrer Updatestrategie](./how-to-determine-your-update-strategy.md)
