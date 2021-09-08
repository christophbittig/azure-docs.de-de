---
title: 'Azure Virtual WAN: Erstellen eines virtuellen Netzwerkgeräts (NVA) im Hub'
description: Erfahren Sie, wie Sie ein virtuelles Netzwerkgerät im Virtual WAN-Hub bereitstellen.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 07/29/2021
ms.author: cherylmc
ms.openlocfilehash: 0252305167f0f77d310810af0a147106fae9e0c6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339269"
---
# <a name="how-to-create-a-network-virtual-appliance-in-an-azure-virtual-wan-hub"></a>Erstellen eines virtuellen Netzwerkgeräts in einem Azure Virtual WAN-Hub

In diesem Artikel wird veranschaulicht, wie Sie mit Virtual WAN über ein **virtuelles Netzwerkgerät** in Azure eine Verbindung mit Ihren Ressourcen in Azure herstellen. Für diese Art von Verbindung wird ein lokales VPN-Gerät benötigt, dem eine extern zugängliche, öffentliche IP-Adresse zugewiesen ist. Weitere Informationen zu Virtual WAN finden Sie unter [Was ist Virtual WAN?](virtual-wan-about.md).

Die Schritte in diesem Artikel unterstützen Sie bei der Erstellung eines virtuellen **Barracuda CloudGen-WAN**-Netzwerkgeräts im Virtual WAN-Hub. Um diese Übung abzuschließen, benötigen Sie ein lokales Barracuda-Gerät (Barracuda Cloud Premise Device, CPE) und eine Lizenz für das Barracuda CloudGen-WAN-Gerät, das Sie im Hub bereitstellen, bevor Sie beginnen.

Eine Dokumentation zur Bereitstellung von **Cisco SD-WAN** innerhalb von Azure Virtual WAN finden Sie unter [Cisco Cloud OnRamp für Multi-Cloud](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701). 

Eine Dokumentation zur Bereitstellung von **VMware SD-WAN** in Azure Virtual WAN finden Sie im [Bereitstellungsleitfaden für VMware SD-WAN im Virtual WAN-Hub](https://kb.vmware.com/s/article/82746).

## <a name="prerequisites"></a>Voraussetzungen

Vergewissern Sie sich vor Beginn der Konfiguration, dass die folgenden Voraussetzungen erfüllt sind bzw. Folgendes vorhanden ist:

* Erwerben Sie eine Lizenz für Ihr Barracuda CloudGen-WAN-Gateway. Weitere Informationen hierzu finden Sie in der [Barracuda CloudGen-WAN-Dokumentation](https://www.barracuda.com/products/cloudgenwan)

* Sie verfügen über ein virtuelles Netzwerk, mit dem Sie eine Verbindung herstellen möchten. Stellen Sie sicher, dass sich kein Subnetz Ihres lokalen Netzwerks mit den virtuellen Netzwerken für die Verbindungsherstellung überschneidet. Informationen zum Erstellen eines virtuellen Netzwerks im Azure-Portal finden Sie in der [Schnellstartanleitung](../virtual-network/quick-create-portal.md).

* Ihr virtuelles Netzwerk verfügt nicht über Gateways für virtuelle Netzwerke. Falls Ihr virtuelles Netzwerk über ein Gateway verfügt (entweder VPN oder ExpressRoute), müssen Sie alle Gateways entfernen. Für diese Konfiguration ist es erforderlich, dass virtuelle Netzwerke stattdessen mit dem Gateway des Virtual WAN-Hubs verbunden werden.

* Beschaffen Sie sich einen IP-Adressbereich für Ihre Hubregion. Der Hub ist ein virtuelles Netzwerk, das von Virtual WAN erstellt und verwendet wird. Der von Ihnen für den Hub angegebene Adressbereich darf sich nicht mit einem Ihrer vorhandenen virtuellen Netzwerke überlappen, mit denen Sie eine Verbindung herstellen. Außerdem ist keine Überlappung mit Ihren Adressbereichen möglich, mit denen Sie eine Verbindung zu Ihren lokalen Standorten herstellen. Falls Sie nicht mit den IP-Adressbereichen in Ihrer lokalen Netzwerkkonfiguration vertraut sind, sollten Sie sich an eine Person wenden, die Ihnen diese Informationen zur Verfügung stellen kann.

* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen.

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>Erstellen eines Virtual WAN

[!INCLUDE [Create virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-hub"></a><a name="hub"></a>Erstellen eines Hubs

Ein Hub ist ein virtuelles Netzwerk, das Gateways für Verbindungen vom Typ „Site-to-Site“, „ExpressRoute“, „Point-to-Site“ oder virtuelle Netzwerkgerätefunktionen enthalten kann. Nachdem der Hub erstellt wurde, werden Ihnen für den Hub auch dann Kosten berechnet, wenn Sie keine Websites zuordnen. Wenn Sie sich entschließen, ein Site-to-Site-VPN-Gateway zu erstellen, dauert es 30 Minuten, um das Site-to-Site-VPN-Gateway im virtuellen Hub zu erstellen. Im Gegensatz zu einer Verbindung vom Typ „Site-to-Site“, „ExpressRoute“ oder „Point-to-Site“ muss der Hub zuerst erstellt werden, bevor Sie ein virtuelles Netzwerkgerät im Hub-VNet bereitstellen können.

1. Suchen Sie das virtuelle WAN, das Sie erstellt haben. Wählen Sie auf der Seite **Virtual WAN** im Abschnitt **Konnektivität** die Option **Hubs** aus.
1. Wählen Sie auf der Seite **Hubs** „+ Neuer Hub“ aus, um die Seite **Virtuellen Hub erstellen** zu öffnen.

   :::image type="content" source="./media/how-to-nva-hub/vwan-hub.png" alt-text="Grundlagen":::
1. Füllen Sie auf der Seite **Virtuellen Hub erstellen** auf der Registerkarte **Grundlagen** die folgenden Felder aus:

   **Projektdetails**

   * Region (zuvor als „Standort“ bezeichnet)
   * Name
   * Privater Adressraum des Hubs. Der minimale Adressraum ist „/24“ zum Erstellen eines Hubs. Dies beinhaltet, dass ein Bereich von „/25“ bis „/32“ bei der Erstellung einen Fehler erzeugt. Azure Virtual WAN ist ein verwalteter Dienst von Microsoft, der die geeigneten Subnetze im virtuellen Hub für die verschiedenen Gateways/Dienste erstellt. (Zum Beispiel: virtuelle Netzwerkgeräte, VPN-Gateways, ExpressRoute-Gateways, Benutzer-VPN/Point-to-Site-Gateways, Firewall, Routing usw.). Dabei muss der Benutzer den Subnetzadressraum für die Dienste im virtuellen Hub nicht explizit planen, weil Microsoft dies im Rahmen des Diensts übernimmt.
1. Wählen Sie zum Überprüfen **Überprüfen + erstellen** aus.
1. Wählen Sie **Erstellen** aus, um den Hub zu erstellen.

## <a name="create-the-network-virtual-appliance-in-the-hub"></a>Erstellen des virtuellen Netzwerkgeräts im Hub

In diesem Schritt erstellen Sie ein virtuelles Netzwerkgerät im Hub. Die Prozedur für die einzelnen virtuellen Netzwerkgeräte variiert bei jedem Produkt gemäß dem virtuellen Netzwerkgerätepartner. In diesem Beispiel erstellen wir ein Barracuda CloudGen-WAN-Gateway.

1. Suchen Sie den im vorherigen Schritt erstellten Virtual WAN-Hub, und öffnen Sie ihn.

   :::image type="content" source="./media/how-to-nva-hub/nva-hub.png" alt-text="Virtueller Hub":::
1. Suchen Sie die Kachel „Virtuelle Netzwerkgeräte“, und wählen Sie den Link **Erstellen** aus.
1. Wählen Sie auf dem Blatt **Virtuelles Netzwerkgerät** die Option **Barracuda CloudGen WAN** aus, und wählen Sie dann die Schaltfläche **Erstellen** aus.

   :::image type="content" source="./media/how-to-nva-hub/select-nva.png" alt-text="NVA auswählen":::
1. Dadurch gelangen Sie zum Azure Marketplace-Angebot für das Barracuda CloudGen-WAN-Gateway. Lesen Sie die Bedingungen, und wählen Sie dann die Schaltfläche **Erstellen**, wenn Sie bereit sind.

   :::image type="content" source="./media/how-to-nva-hub/barracuda-create-basics.png" alt-text="Grundlagen zum virtuellen Barracuda-Netzwerkgerät":::
1. Auf der Seite **Grundlagen** müssen Sie die folgenden Informationen angeben:

   * **Abonnement**: Wählen Sie das Abonnement aus, mit dem Sie Virtual WAN und den Hub bereitgestellt haben.
   * **Ressourcengruppe**: Wählen Sie dieselbe Ressourcengruppe aus, die Sie zum Bereitstellen des Virtual WAN und Hubs verwendet haben.
   * **Region**: Wählen Sie dieselbe Region aus, in der sich Ihre virtuelle Hubressource befindet.
   * **Anwendungsname**: Das Barracuda NextGen-WAN ist eine verwaltete Anwendung. Wählen Sie einen Namen aus, mit dem sich diese Ressource leicht identifizieren lässt, da dies der Name ist, mit dem sie in Ihrem Abonnement angezeigt wird.
   * **Verwaltete Ressourcengruppe**: Dies ist der Name der verwalteten Ressourcengruppe, in der Barracuda Ressourcen bereitstellt, die von ihm verwaltet werden. Der Name sollte hierfür vorab aufgefüllt sein.
1. Wählen Sie unten auf der Seite die Schaltfläche **Next: CloudGen-WAN-Gateway** aus.

   :::image type="content" source="./media/how-to-nva-hub/barracuda-cloudgen-wan.png" alt-text="CloudGen-WAN-Gateway":::
1. Geben Sie hier die folgenden Informationen an:

   * **Virtual WAN Hub**: Der Virtual WAN-Hub, in dem dieses virtuelle Netzwerkgerät bereitgestellt werden soll.
   * **NVA-Infrastruktureinheiten**: Geben Sie die Anzahl der NVA-Infrastruktureinheiten an, mit denen Sie dieses virtuelle Netzwerkgerät bereitstellen möchten. Wählen Sie die Menge der aggregierten Bandbreitenkapazität aus, die Sie über alle Zweigstellen hinweg bereitstellen möchten, die über dieses virtuelle Netzwerkgerät eine Verbindung mit diesem Hub herstellen werden.
   * **Token**: Barracuda erfordert, dass Sie hier ein Authentifizierungstoken angeben, um sich selbst als registrierter Benutzer dieses Produkts zu identifizieren. Dieses müssen Sie von Barracuda abrufen.
1. Wählen Sie die Schaltfläche **Überprüfen und erstellen** aus, um fortzufahren.
1. Auf dieser Seite werden Sie aufgefordert, die Bedingungen der Vereinbarung für den Co-Admin-Zugriff zu akzeptieren. Dies ist Standard bei verwalteten Anwendungen, in denen der Herausgeber Zugriff auf Ressourcen in dieser Bereitstellung hat. Aktivieren Sie das Kontrollkästchen **Ich stimme den oben genannten Geschäftsbedingungen zu**, und wählen Sie anschließend **Erstellen** aus.

## <a name="connect-the-vnet-to-the-hub"></a><a name="vnet"></a>Herstellen einer Verbindung zwischen VNET und Hub

In diesem Abschnitt erstellen Sie eine Verbindung zwischen Ihrem Hub und einem VNet.

[!INCLUDE [Connect](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Virtual WAN finden Sie auf der Seite [Was ist Virtual WAN?](virtual-wan-about.md).
* Weitere Informationen zu virtuellen Netzwerkgeräten (NVAs) in einem Virtual WAN-Hub finden Sie unter [Informationen zum virtuellen Netzwerkgerät im Virtual WAN-Hub](about-nva-hub.md).
