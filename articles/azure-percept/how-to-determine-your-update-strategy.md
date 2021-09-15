---
title: Bestimmen Ihrer Updatestrategie für Azure Percept DK
description: Vor- und Nachteile von Azure Percept DK OTA- oder USB-Kabel-Updates. Empfehlung für die Auswahl der besten Update-Methode für verschiedene Benutzer.
author: EthanChangAED
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 08/23/2021
ms.custom: template-concept
ms.openlocfilehash: c26de68bc30e8ebfa6de92c8e142e6ca69088b26
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123223343"
---
# <a name="determine-your-update-strategy-for-azure-percept-dk"></a>Bestimmen Ihrer Updatestrategie für Azure Percept DK

Um Ihre Azure Percept DK Software auf dem neuesten Stand zu halten, bietet Microsoft zwei Update-Methoden für das Dev-Kit an. **Update über USB-Kabel** oder **Over-the-air (OTA) Update**.

Das Update über USB-Kabel führt eine Neuinstallation des Dev-Kits durch. Vorhandene Konfigurationen und alle Benutzerdaten in jeder Partition werden nach der Bereitstellung des neuen Images gelöscht. Verbinden Sie dazu das Dev-Kit über ein Typ-C-USB-Kabel mit einem Hostsystem. Bei dem Host-System kann es sich um einen Windows/Linux Computer handeln.  Sie können diese Aktualisierungsmethode auch als Zurücksetzen auf Factoryeinstellungen verwenden. Dazu wird die exakt gleiche Version auf das Dev-Kit neu übertragen. Siehe [Update der Azure Percept DK über eine USB-C-Kabelverbindung](./how-to-update-via-usb.md) für Details über das USB-Kabel-Update.

Das OTA-Update basiert auf dem Azure-Dienst [Device Update for IoT Hub](https://docs.microsoft.com/azure/iot-hub-device-update/device-update-resources). Verbinden Sie das Dev-Kit mit Azure loT Hub, um diese Art von Update durchzuführen. Konfigurationen und Benutzerdaten bleiben nach dem OTA-Update erhalten. Siehe [Aktualisieren Ihres Azure Percept DK-Geräts per Funk (Over-the-Air-Update)](./how-to-update-over-the-air.md) für Details zur Durchführung des OTA-Updates.

Prüfen Sie die Vor- und Nachteile des USB-Kabel-Updates und des OTA-Updates und folgen Sie dann den Empfehlungen von Microsoft für die verschiedenen Szenarien.

## <a name="usb-cable-update"></a>USB-Kabel-Update

- Vorteile
  - Sie müssen das Dev-Kit nicht mit dem Internet/Azure verbinden.
  - Das neueste Image ist immer verfügbar, unabhängig davon, welche Software- und Firmware-Version gerade auf dem Dev-Kit geladen ist.
- Nachteile
  - Führt eine Reimaging des Geräts durch und entfernt Konfigurationen und Benutzerdaten.
  - Sie müssen OOBE erneut ausführen und jeden nicht vorgeladenen Container herunterladen.
  - Kann nicht remote durchgeführt werden.

## <a name="ota-update"></a>OTA-Aktualisierung

- Vorteile
  - Benutzerdaten, Konfigurationen und heruntergeladene Container bleiben erhalten. Das Dev-Kit funktioniert nach der OTA-Aktualisierung weiter wie zuvor.
  - Die Aktualisierung kann remote durchgeführt werden.
  - Mehrere ähnliche Geräte können gleichzeitig aktualisiert werden. Aktualisierungen können auch zeitlich festgelegt werden, z. B. für die Nachtstunden.
- Nachteile
  - Möglicherweise gibt es Hard-Stop-Versionen, die nicht übersprungen werden können. Siehe [Hard-Stop Version von OTA](./software-releases-over-the-air-updates.md#hard-stop-version-of-ota).
  - Das Gerät muss mit einem loT Hub verbunden sein, für den die Funktion "Device Update for IoT Hub" richtig konfiguriert wurde.
  - Bei einem Downgrade funktioniert das nicht gut.

> [!IMPORTANT]
> Device Update for IoT Hub blockiert nicht die Bereitstellung von Images, deren Version älter ist als die des aktuell laufenden Betriebssystems. Bei Dev-Kits führt dies jedoch zu einem Verlust von Daten und Funktionen.

## <a name="microsoft-recommendations"></a>Empfehlungen von Microsoft

|type|Szenario|Update-Methode|
|:---:|---|:---:|
|Produktion|Halten Sie das Dev-Kit auf dem neuesten Stand für die neuesten Fixes und Sicherheitspatches, während es bereits mit Ihrer Lösung läuft oder im Feld bereitgestellt wird.|OTA|
|Produktion/Entwicklung|Unboxen Sie ein neues Entwicklungskit und aktualisieren Sie es auf die neueste Software.|USB|
|Produktion/Entwicklung|Sie möchten auf die neueste Softwareversion aktualisieren und haben bereits mehrere Monatsversionen übersprungen.|USB|
|Produktion/Entwicklung|Einen Dev-Kit auf Faktoryeinstellungen zurücksetzen.|USB|
|Entwicklung|Während der Lösungsentwicklung möchten Sie das Betriebssystem und die Firmware des Dev-Kits auf dem neuesten Stand halten.|USB/OTA|
|Entwicklung|Springen Sie zu einer bestimmten (älteren) Version, um Probleme zu untersuchen/zu debuggen.|USB|

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich für eine Update-Methode entschieden haben, besuchen Sie die folgenden Seiten, um sich auf das Update vorzubereiten:

USB-Kabel-Update

- [Aktualisieren Sie das Azure Percept DK über eine USB-C-Kabelverbindung](./how-to-update-via-usb.md)
- [Azure Percept DK Software-Versionen für USB-Kabel-Update](./software-releases-usb-cable-updates.md)

OTA

- [Aktualisieren Ihres Azure Percept DK-Geräts per Funk (Over-the-Air-Update)](./how-to-update-over-the-air.md)
- [Azure Percept DK Software-Freigaben für OTA-Update](./software-releases-over-the-air-updates.md)
