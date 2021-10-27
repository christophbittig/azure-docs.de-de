---
title: Verbinden eines Azure Percept über 5G- oder LTE-Netzwerke per Gateway
description: In diesem Artikel wird beschrieben, wie Sie ein Azure Percept DK per Mobilfunkgateway über 5G- oder LTE-Netzwerke verbinden.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/23/2021
ms.custom: template-how-to
ms.openlocfilehash: 00596a23e086f801b152e1a3129ecaf7ef44a0df
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007317"
---
# <a name="connect-azure-percept-over-5g-or-lte-networks-using-a-gateway"></a>Verbinden eines Azure Percept über 5G- oder LTE-Netzwerke per Gateway
Ein Gateway, das über 5G oder TCP eine Verbindung mit dem Internet herstellt und über Ethernetports verfügt, stellt eine einfache Möglichkeit dar, Azure Percept mit dem Internet zu verbinden. In diesem Fall erfasst Azure Percept nicht einmal, dass es über 5G oder LTE verbunden ist. Es erkennt nur, dass sein Ethernetport über Konnektivität verfügt, und leitet den gesamten Datenverkehr über diesen.  


## <a name="5glte-gateway-topology-overview"></a>Überblick über die 5G-/LTE-Gatewaytopologie
Nachfolgend sehen Sie, wie ein 5G/LTE-Gateway ganz einfach mit dem Azure Percept DK gekoppelt werden kann.

:::image type="Image" source="media/connect-over-cellular/topology.png" alt-text="Dieses Diagramm zeigt, wie das Azure Percept DK über Ethernet eine Verbindung mit einem 5G/LTE-Gateway herstellt." lightbox="media/connect-over-cellular/topology-expanded.png":::

## <a name="considerations-when-connecting-to-a-5g-or-lte-gateway"></a>Überlegungen für Verbindungen mit einem 5G/LTE-Gateway
Im Folgenden finden Sie einige wichtige Punkte, die Sie berücksichtigen sollten, wenn Sie das Azure Percept DK mit einem 5G/LTE-Gateway verbinden.
- Richten Sie zuerst das Gateway ein, und überprüfen Sie, ob es über die SIM-Karte eine Verbindung empfängt. Damit wird es einfacher, mögliche Probleme beim Herstellen der Verbindung mit dem Azure Percept DK zu beheben.
- Stellen Sie sicher, dass beide Enden des Ethernet-Kabels fest mit dem Gateway und dem Azure Percept DK verbunden sind.
- Befolgen Sie die [Standardanweisungen](./how-to-connect-over-ethernet.md), um das Azure Percept DK über Ethernet zu verbinden.
- Wenn Ihr 5G/LTE-Tarif kontingentbasiert ist, empfiehlt es sich, die Menge an Daten zu optimieren, die von Ihren Azure Percept DK-Modellen an die Cloud gesendet werden.
- Stellen Sie sicher, dass Sie über eine [ordnungsgemäß konfigurierte Firewall](./concept-security-configuration.md) verfügen, die eingehenden Datenverkehr externen Ursprungs blockiert.

## <a name="considerations-when-doing-ssh-to-the-devkit"></a>Überlegungen beim Durchführen von SSH-Verbindungen mit dem DevKit
Um über ein 5G/LTE-Ethernet-Gateway eine SSH-Verbindung zwischen Development Kit und Ethernet-Netzwerk herzustellen, stehen Ihnen die folgenden Optionen zur Verfügung:
- **Verwenden des WLAN-Zugriffspunkts des Development Kits**. Wenn das WLAN deaktiviert ist, können Sie es erneut aktivieren, indem Sie das Development Kit neu starten. Danach können Sie eine Verbindung mit dem WLAN-Zugriffspunkt des Kits herstellen und [die Anleitung zum SSH-Verbindungen mit Azure Percept DK](./how-to-ssh-into-percept-dk.md) befolgen.
- **Verwenden einer Ethernet-Verbindung mit einem lokalen Netzwerk (LAN)** . Bei dieser Option trennen Sie das Development Kit physisch vom 5G/LTE-Gateway und verbinden es mit dem LAN-Router. Weitere Informationen finden Sie unter [Herstellen einer Ethernet-Verbindung](./how-to-connect-over-ethernet.md). 
- **Verwenden der Remotezugriffsfunktionen des Gateways**. Viele 5G/LTE-Gateways enthalten Verwaltungsprogramme für den Remotezugriff, über die eine SSH-Verbindung mit Geräten im Netzwerk hergestellt werden kann. Erkundigen Sie sich beim Hersteller Ihres 5G/LTE-Gateways, ob dieses eine solche Funktion bietet. Hier finden Sie ein Beispiel für ein Verwaltungsprogramm für den Remotezugriff für [5G/LTE-Gateways von Cradlepoint](https://customer.cradlepoint.com/s/article/NCM-Remote-Connect-LAN-Manager).
- **Verwenden des seriellen Anschlusses des Development Kits**. Das Azure Percept DK verfügt über einen seriellen Anschluss, über den eine direkte Verbindung mit dem Gerät hergestellt werden kann. Detaillierte Anweisungen finden Sie unter [Herstellen einer seriellen Verbindung mit Ihrem Azure Percept DK](./how-to-connect-to-percept-dk-over-serial.md).

## <a name="next-steps"></a>Nächste Schritte
Je nachdem, auf welches Mobilfunkgerät Sie möglicherweise Zugriff haben, sollten Sie eine Verbindung über einen USB-Modus in Erwägung ziehen:

[Verbinden über USB-Modem](./connect-over-cellular-usb.md).

Zurück zum Hauptartikel über 5G oder LTE:

[Verbinden über 5G oder LTE](./connect-over-cellular.md).