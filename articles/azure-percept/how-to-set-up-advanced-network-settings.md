---
title: Einrichten erweiterter Netzwerkeinstellungen in Azure Percept DK
description: In diesem Artikel wird der Benutzer während der Azure Percept DK-Einrichtungserfahrung durch die erweiterten Netzwerkeinstellungen geführt.
author: WendyAnn0079
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 7/19/2021
ms.custom: template-how-to
ms.openlocfilehash: ac06d331bcd587c1294c21388faac75cc6f8cfd6
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123219635"
---
# <a name="set-up-advanced-network-settings-on-the-azure-percept-dk"></a>Einrichten erweiterter Netzwerkeinstellungen in Azure Percept DK

Mit Azure Percept DK können Sie verschiedene Netzwerkkomponenten im Dev Kit steuern. Dies erfolgt über die erweiterten Netzwerkeinstellungen in der Einrichtungserfahrung. Um auf diese Einstellungen zuzugreifen, müssen Sie die [Einrichtungserfahrung starten](./quickstart-percept-dk-set-up.md) und auf der Seite **Netzwerkverbindung** die Option **Auf erweiterte Netzwerkeinstellungen zugreifen** auswählen.

:::image type="content" source="media/how-to-set-up-advanced-network-settings/advanced-ns-entry.png" alt-text="Starten der erweiterten Netzwerkeinstellungen über die Seite „Netzwerkverbindungen“.":::

## <a name="select-the-security-setting"></a>Auswählen der Sicherheitseinstellung
IPv4 und IPv6 werden beide im Azure Percept DK für lokale Konnektivität unterstützt.

> [!NOTE]
> Azure IoTHub [unterstützt IPv6 nicht](../iot-hub/iot-hub-understand-ip-address.md#support-for-ipv6). IPv4 muss für die Kommunikation mit IoTHub verwendet werden.
1. Aktivieren Sie das Optionsfeld „IPv4“, und wählen Sie dann ein Elemente unter „Netzwerkeinstellungen“ aus, seine IPv4-Einstellungen zu ändern.
1. Aktivieren Sie das Optionsfeld „IPv6“, und wählen Sie dann ein Elemente unter „Netzwerkeinstellungen“ aus, seine IPv6-Einstellungen zu ändern.
1. Die Optionen für die **Netzwerkeinstellung** können sich abhängig von Ihrer Auswahl ändern.

:::image type="content" source="media/how-to-set-up-advanced-network-settings/advanced-ns-security.png" alt-text="Auswählen eines Sicherheitsprotokolls, um die Liste der Netzwerkoptionen anzuzeigen.":::

## <a name="define-a-static-ip-address"></a>Definieren einer statischen IP-Adresse

1. Wählen Sie auf der Seite **Erweiterte Netzwerkeinstellungen** aus der Liste die Option **Statische IP-Adresse definieren** aus.
1. Wählen Sie im Dropdownmenü Ihre **Netzwerkschnittstelle** aus.
1. Deaktivieren Sie **Dynamische IP-Adresse**.
1. Geben Sie Ihre statische IP-Adresse ein.
1. Geben Sie die IP-Adresse Ihres Subnetzes ein (auch als Subnetzmaske bekannt).
1. Geben Sie die IP-Adresse Ihres Gateways ein (auch als Ihr Standardgateway bekannt).
1. Geben Sie ggf. Ihre DNS-Adresse ein.
1. Wählen Sie **Speichern** aus.
1. Wählen Sie **Zurück** aus, um zur Hauptseite von **Erweiterte Netzwerkeinstellungen** zurückzukehren.

## <a name="define-dns-server-for-docker"></a>Definieren eines DNS-Servers für Docker
Mit diesen Einstellungen können Sie Docker-DNS-IP-Adressen ändern oder neue hinzufügen.

> [!NOTE]
> Der Docker-Dienst ist so konfiguriert, dass er nur IPv4-DNS-Einträge akzeptiert.  Über die IPv6-Bildschirme hinzugefügte Einträge werden ignoriert.

1. Wählen Sie auf der Seite **Erweiterte Netzwerkeinstellungen** aus der Liste die Option **DNS-Server für Docker definieren** aus.
1. Eingeben Ihrer Docker-IPv4-DNS-Adresse
1. Wählen Sie **Speichern** aus.
1. Wählen Sie **Zurück** aus, um zur Hauptseite von **Erweiterte Netzwerkeinstellungen** zurückzukehren.

## <a name="define-bridge-internet-protocol-for-docker"></a>Definieren des Brückeninternetprotokolls (BIP) für Docker
In den Bildschirmen des Brückeninternetprotokolls können Sie den IPv4-Adressraum für Docker-Container ändern.

Wenn die IP-Adresse Ihres Geräts dieselbe Route wie der Docker-Dienst des Azure Percept Devkits (172.17.x.x) verwendet, müssen Sie die Docker-Brücke in eine andere Route ändern, um die Kommunikation zwischen Docker-Containern und Azure IoTHub zu ermöglichen.  

1. Wählen Sie auf der Seite **Erweiterte Netzwerkeinstellungen** aus der Liste die Option **Brückeninternetprotokoll für Docker definieren** aus.
1. Geben Sie die IPv4-Adresse des Docker-Brückeninternetprotokolls (BIP) ein.
1. Wählen Sie **Speichern** aus.
1. Wählen Sie **Zurück** aus, um zur Hauptseite von **Erweiterte Netzwerkeinstellungen** zurückzukehren.

## <a name="define-an-internet-proxy-server"></a>Definieren eines Internetproxyservers
Mit dieser Option können Sie einen Proxyserver definieren.    

1. Wählen Sie auf der Seite **Erweiterte Netzwerkeinstellungen** aus der Liste die Option **Internetproxyserver definieren** aus.
1. Aktivieren Sie das Kontrollkästchen **Proxyserver verwenden**, um diese Option zu aktivieren.
1. Geben Sie die **HTTP-Adresse** Ihres Proxyservers ein (falls zutreffend).
1. Geben Sie die **HTTPS-Adresse** Ihres Proxyservers ein (falls zutreffend).
1. Geben Sie die **FTP-Adresse** Ihres Proxyservers ein (falls zutreffend).
1. Geben Sie im Feld **Keine Proxyadressen** alle IP-Adressen ein, für die der Proxyserver nicht verwendet werden soll.
1. Wählen Sie **Speichern** aus.
1. Wählen Sie **Zurück** aus, um zur Hauptseite von **Erweiterte Netzwerkeinstellungen** zurückzukehren.

## <a name="setup-zero-touch-provisioning"></a>Einrichten der Bereitstellung ohne Benutzereingreifen

> [!IMPORTANT]
> Die Einstellungen von **Bereitstellung ohne Benutzereingreifen einrichten** sind derzeit nicht funktionsfähig.

Mit dieser Option können Sie Ihr Azure Percept DK in einen [Wi-Fi Easy Connect<sup>TM</sup>-Massenkonfigurator](https://techcommunity.microsoft.com/t5/internet-of-things/simplify-wi-fi-iot-device-onboarding-with-zero-touch/ba-p/2161129#:~:text=A%20Wi-Fi%20Easy%20Connect%E2%84%A2%20Configurator%2C%20paired%20with%20the,device%20to%20any%20WPA2-Personal%20or%20WPA3-Personal%20wireless%20LAN.) für das gleichzeitige Onboarding mehrerer Geräte in Ihrer WLAN-Infrastruktur verwandeln.  

## <a name="define-access-point-passphrase"></a>Definieren der Passphrase des Zugriffspunkts 
Mit dieser Option können Sie die Passphrase für den WLAN-Zugriffspunkt des Azure Percept DK aktualisieren.  

> [!CAUTION]
> Nach dem Speichern Ihrer neuen Passphrase werden Sie sofort vom WLAN-Zugriffspunkt getrennt.  Stellen Sie die Verbindung mithilfe der neuen Passphrase wieder her, um erneut Zugriff zu erhalten.  

Anforderungen an die Passphrase:
- Muss zwischen 12 und 123 Zeichen umfassen.
- Muss mindestens einen Kleinbuchstaben, einen Großbuchstaben, eine Zahl und ein Sonderzeichen enthalten.

1. Wählen Sie auf der Seite **Erweiterte Netzwerkeinstellungen** aus der Liste die Option **Passphrase des Zugriffspunkts definieren** aus.
1. Eingeben einer neuen Passphrase
1. Wählen Sie **Speichern** aus.
1. Wählen Sie **Zurück** aus, um zur Hauptseite von **Erweiterte Netzwerkeinstellungen** zurückzukehren.

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie die Änderungen an den **Erweiterten Netzwerkeinstellungen** vorgenommen haben, wählen Sie die Schaltfläche **Zurück** aus, um [die Azure Percept DK-Einrichtungserfahrung fortzusetzen](./quickstart-percept-dk-set-up.md).