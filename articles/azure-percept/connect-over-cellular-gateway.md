---
title: Verbinden von Azure Percept DK über 5G- und LTE-Netzwerke mithilfe eines Gateways
description: In diesem Artikel wird erläutert, wie Sie Azure Percept DK über 5G- und LTE-Netzwerke mithilfe eines Mobilfunkgateways verbinden können.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/23/2021
ms.custom: template-how-to
ms.openlocfilehash: 5b66885afdec8e32b938c735625bd48c9c62535a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131439968"
---
# <a name="connect-azure-percept-dk-over-5g-and-lte-networks-by-using-a-gateway"></a>Verbinden von Azure Percept DK über 5G- und LTE-Netzwerke mithilfe eines Gateways

Eine einfache Möglichkeit, Azure Percept mit dem Internet zu verbinden, ist das Verwenden eines Gateways, das über 5G oder LTE eine Verbindung mit dem Internet herstellt und Ethernet-Ports bereitstellt. In diesem Fall ist Azure Percept nicht einmal bewusst, dass eine Verbindung über 5G oder LTE besteht. Azure Percept „weiß“ nur, dass der Ethernet-Port über Konnektivität verfügt und den gesamten Datenverkehr über diesen Port leitet.  


## <a name="overview-of-5g-and-lte-gateway-topology"></a>Übersicht über die 5G- und LTE-Gatewaytopologie

Im folgenden Diagramm wird gezeigt, wie ein 5G- oder LTE-Gateway problemlos mit Azure Percept DK (Development Kit) gekoppelt werden kann.

:::image type="Image" source="media/connect-over-cellular/topology-v2.png" alt-text="Diagramm: Azure Percept DK stellt über das Ethernet eine Verbindung mit einem 5G- oder LTE-Gateway her" lightbox="media/connect-over-cellular/topology-expanded-v2.png":::

## <a name="if-youre-connecting-to-a-5g-or-lte-gateway"></a>Eine Verbindung mit einem 5G- oder LTE-Gateway herstellen

Wenn Sie Azure Percept DK mit einem 5G- oder einem LTE-Gateway verbinden, berücksichtigen Sie die folgenden wichtigen Punkte:
- Richten Sie zuerst das Gateway ein, und überprüfen Sie dann, ob es eine Verbindung über die SIM-Karte empfängt. Wenn Sie diese Reihenfolge befolgen, können Sie Probleme leichter behandeln, die beim Herstellen einer Verbindung mit Azure Percept DK auftreten.
- Stellen Sie sicher, dass beide Enden des Ethernet-Kabels fest mit dem Gateway und Azure Percept DK verbunden sind.
- Befolgen Sie die [Standardanweisungen](./how-to-connect-over-ethernet.md), um Azure Percept DK über Ethernet zu verbinden.
- Wenn Ihr 5G- oder LTE-Tarif über ein Kontingent verfügt, wird empfohlen, die Datenmenge zu optimieren, die Ihre Azure Percept DK-Modelle an die Cloud senden.
- Stellen Sie sicher, dass Sie über eine [ordnungsgemäß konfigurierte Firewall](./concept-security-configuration.md) verfügen, die eingehenden Datenverkehr externen Ursprungs blockiert.

## <a name="if-youre-connecting-to-the-dev-kit-via-ssh-protocol"></a>Herstellen einer Verbindung mit dem Development Kit über das SSH-Protokoll

Wenn Sie das Secure Shell-Netzwerkprotokoll (SSH) verwenden, um über ein 5G- oder LTE-Ethernet-Gateway eine Verbindung mit dem Development Kit herzustellen, verwenden Sie eine der folgenden Optionen:
- **Verwenden Sie den WLAN-Zugriffspunkt des Development Kits**: Wenn Sie die WLAN-Verbindung deaktiviert haben, können Sie sie erneut aktivieren, indem Sie Ihr Development Kit neu starten. Anschließend können Sie eine Verbindung mit dem WLAN-Zugriffspunkt des Development Kits herstellen und die Anweisungen in [Herstellen einer Verbindung mit Azure Percept DK über SSH](./how-to-ssh-into-percept-dk.md) befolgen.
- **Verwenden Sie eine Ethernet-Verbindung mit einem lokalen Netzwerk (LAN)** : Mit dieser Option trennen Sie Ihr Development Kit vom 5G- oder LTE-Gateway und schließen es an einen LAN-Router an. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit Azure Percept DK über Ethernet](./how-to-connect-over-ethernet.md). 
- **Verwenden Sie die Remotezugriffsfeatures des Gateways**: Viele 5G- und LTE-Gateways enthalten Remotezugriffs-Manager, die zum Herstellen einer Verbindung mit Geräten im Netzwerk über SSH verwendet werden können. Kontaktieren Sie den Hersteller des 5G- oder LTE-Gateways, um zu überprüfen, ob dieses Feature verfügbar ist. Ein Beispiel für einen Remotezugriffs-Manager finden Sie unter [5G- und LTE-Gateways von Cradlepoint](https://customer.cradlepoint.com/s/article/NCM-Remote-Connect-LAN-Manager).
- **Verwenden Sie den seriellen Port des Development Kits**: Azure Percept DK enthält einen seriellen Verbindungsport, der verwendet werden kann, um eine direkte Verbindung mit dem Gerät herzustellen. Weitere Informationen finden Sie unter [Herstellen einer seriellen Verbindung mit Azure Percept DK über ein serielles Kabel](./how-to-connect-to-percept-dk-over-serial.md).

## <a name="next-steps"></a>Nächste Schritte
Abhängig vom Mobilfunkgerät, auf das Sie Zugriff haben, können Sie auf zwei Arten eine Verbindung herstellen:

* [Verbinden mithilfe eines USB-Modems](./connect-over-cellular-usb.md)
* [Verbinden mit 5G oder LTE](./connect-over-cellular.md)
