---
title: Konfigurieren der NAT für Azure VPN Gateway
titleSuffix: Azure VPN Gateway
description: Hier erfahren Sie, wie Sie NAT für Azure VPN Gateway konfigurieren.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 06/24/2021
ms.author: yushwang
ms.openlocfilehash: 8b9d3d298986aa4aae6ead4e9733c9b0994b1f91
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/02/2021
ms.locfileid: "113214063"
---
# <a name="how-to-configure-nat-on-azure-vpn-gateways-preview"></a>Konfigurieren der NAT für Azure VPN Gateway-Instanzen (Vorschau)

In diesem Artikel wird beschrieben, wie Sie NAT (Network Address Translation, Netzwerkadressenübersetzung) in Azure VPN Gateway über das Azure-Portal konfigurieren.

## <a name="about-nat"></a><a name="about"></a>Informationen zur NAT

NAT definiert den Mechanismus, mit dem eine IP-Adresse in eine andere IP-Adresse in einem IP-Paket übersetzt wird. Sie wird häufig verwendet, um Netzwerke mit überlappenden IP-Adressbereichen zu verbinden. NAT-Regeln oder -Richtlinien auf den Gatewaygeräten, die die Netzwerke verbinden, geben die Adresszuordnungen für die Adressübersetzung in den Netzwerken an.

Weitere Informationen zur NAT-Unterstützung für Azure VPN Gateway finden Sie unter [Informationen zur Netzwerkadressenübersetzung in Azure VPN Gateway (Vorschau)](nat-overview.md).

> [!IMPORTANT] 
> Die Azure-Netzwerkadressenübersetzung für das VPN-Gateway befindet sich derzeit in der Vorschauphase. 
> * Lesen Sie auf jeden Fall den Abschnitt **[Einschränkungen der Vorschauversion](#limits)** in diesem Artikel.
> * Die Netzwerkadressenübersetzung in Azure VPN Gateway unterstützt nur statische 1:1-NAT-Regeln. Dynamische NAT-Regeln werden nicht unterstützt.
> * NAT wird für die folgenden SKUs unterstützt: VpnGw2~5, VpnGw2AZ~5AZ.

## <a name="getting-started"></a>Erste Schritte

Die einzelnen Teile dieses Artikels unterstützen Sie beim Erstellen eines grundlegenden Bausteins zum Konfigurieren der NAT in Ihrer Netzwerkverbindung. Wenn Sie alle drei Teile abgeschlossen haben, erstellen Sie die Topologie wie in Diagramm 1 gezeigt.

### <a name="diagram-1"></a><a name="diagram"></a>Diagramm 1

:::image type="content" source="./media/nat-overview/vpn-nat.png" alt-text="Screenshot: Diagramm 1" lightbox="./media/nat-overview/vpn-nat.png" border="false":::

### <a name="prerequisites"></a>Voraussetzungen

* Stellen Sie sicher, dass Sie über ein Azure-Abonnement verfügen. Wenn Sie noch kein Azure-Abonnement besitzen, können Sie Ihre [MSDN-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) aktivieren oder sich für ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) registrieren.
* Sehen Sie sich die [Einschränkungen der Vorschauversion](#limits) an.

## <a name="part-1-create-vnet-and-gateways"></a><a name ="vnet"></a>Teil 1: Erstellen von VNet und Gateways

In diesem Abschnitt erstellen Sie ein virtuelles Netzwerk, ein VPN-Gateway und die Ressourcen für Gateways des lokalen Netzwerks, die den in [Diagramm 1](#diagram) gezeigten Ressourcen entsprechen.

Führen Sie zum Erstellen dieser Ressourcen die Schritte im Artikel [Tutorial: Erstellen einer Site-to-Site-Verbindung im Azure-Portal](tutorial-site-to-site-portal.md) aus. Durchlaufen Sie die folgenden Abschnitte des Artikels, erstellen Sie jedoch keine Verbindungen.

* [VNET](tutorial-site-to-site-portal.md#CreatVNet)
* [VPN Gateway](tutorial-site-to-site-portal.md#VNetGateway)
* [Lokales Netzwerkgateway](tutorial-site-to-site-portal.md#LocalNetworkGateway)
* [Konfigurieren des VPN-Geräts](tutorial-site-to-site-portal.md#VPNDevice)


>[!IMPORTANT]
>  Erstellen Sie beim Ausführen der Schritte in den folgenden Artikeln nicht die **Verbindungsressourcen** in den Artikeln. Bei diesem Vorgang tritt ein Fehler auf, da die IP-Adressräume zwischen VNet, Filiale 1 und Filiale 2 identisch sind. Führen Sie die Schritte im folgenden Abschnitt aus, um die NAT-Regeln zu erstellen, und erstellen Sie dann die Verbindungen mit den NAT-Regeln.
>


Die folgenden Screenshots enthalten Beispiele für die zu erstellenden Ressourcen.

* **VNET**

   :::image type="content" source="./media/nat-howto/vnet.png" alt-text="Screenshot: VNet-Adressraum" lightbox="./media/nat-howto/vnet.png":::
* **VPN Gateway**

   :::image type="content" source="./media/nat-howto/vpn-gateway.png" alt-text="Screenshot: Gateway" lightbox="./media/nat-howto/vpn-gateway.png":::
* **Lokales Netzwerkgateway: Filiale 1**

   :::image type="content" source="./media/nat-howto/branch-1.png" alt-text="Screenshot: Lokales Netzwerkgateway Filiale 1" lightbox="./media/nat-howto/branch-1.png" :::

* **Lokales Netzwerkgateway: Filiale 2**

   :::image type="content" source="./media/nat-howto/branch-2.png" alt-text="Screenshot: Lokales Netzwerkgateway Filiale 2" lightbox="./media/nat-howto/branch-2.png":::

   > [!IMPORTANT] 
   > Wenn der Adressraum des lokalen Netzwerkgateways mit dem Adressraum des VNet identisch oder kleiner als dieser Adressraum ist, verwenden Sie während der Vorschauphase **BGP**, und lassen Sie das Feld für den Adressraum des lokalen Netzwerkgateways **leer**. Statisches Routing (ohne BGP) wird in diesem Szenario während der Vorschauphase nicht unterstützt.
   >

## <a name="part-2-create-nat-rules"></a><a name ="nat-rules"></a>Teil 2: Erstellen von NAT-Regeln

Vor dem Erstellen von Verbindungen müssen Sie NAT-Regeln für das VPN-Gateway erstellen und speichern. In der folgenden Tabelle sind die erforderlichen NAT-Regeln aufgeführt. Die Topologie finden Sie in [Diagramm 1](#diagram).

**Tabelle der NAT-Regeln**

| Name     | type   | Modus        | Intern    | Extern     | Verbindung          |
| ---      | ---    | ---         | ---         | ---          | ---                 |
| VNet     | Statisch | EgressSNAT  | 10.0.1.0/24 | 100.0.1.0/24 | Beide Verbindungen    | 
| Branch_1 | Statisch | IngressSNAT | 10.0.1.0/24 | 100.0.2.0/24 | Verbindung für Filiale 1 |
| Branch_2 | Statisch | IngressSNAT | 10.0.1.0/24 | 100.0.3.0/24 | Verbindung für Filiale 2 |

Führen Sie die folgenden Schritte aus, um alle NAT-Regeln für das VPN-Gateway zu erstellen:

1. Navigieren Sie im Azure-Portal zur Ressourcenseite **Gateway für virtuelle Netzwerke**, und wählen Sie **NAT Rules (Preview)** (NAT-Regeln (Vorschau)) aus.
1. Geben Sie anhand der obigen **Tabelle der NAT-Regeln** die entsprechenden Werte ein.

   :::image type="content" source="./media/nat-howto/nat-rules.png" alt-text="Screenshot: NAT-Regeln" lightbox="./media/nat-howto/nat-rules.png":::
1. Klicken Sie auf **Speichern**, um die NAT-Regeln für die VPN-Gatewayressource zu speichern. Dieser Vorgang kann bis zu zehn Minuten dauern.

## <a name="part-3-create-connections-and-link-nat-rules"></a><a name ="connections"></a>Teil 3: Erstellen von Verbindungen und Verknüpfen von NAT-Regeln

In diesem Abschnitt erstellen Sie die Verbindungen und ordnen dann die NAT-Regeln den Verbindungen zu, um die Beispieltopologie in [Diagramm 1](#diagram) zu implementieren.

### <a name="1-create-connections"></a>1. Erstellen von Verbindungen

Führen Sie die Schritte im Artikel [Tutorial: Erstellen einer Site-to-Site-Verbindung im Azure-Portal](tutorial-site-to-site-portal.md) aus, um die beiden Verbindungen wie nachfolgend gezeigt zu erstellen:

   :::image type="content" source="./media/nat-howto/connections.png" alt-text="Screenshot: Seite mit den Verbindungen" lightbox="./media/nat-howto/connections.png":::

### <a name="2-associate-nat-rules-with-the-connections"></a>2. Zuordnen von NAT-Regeln zu den Verbindungen

In diesem Schritt ordnen Sie die NAT-Regeln den einzelnen Verbindungsressourcen zu.

1. Navigieren Sie im Azure-Portal zu den Verbindungsressourcen, und wählen Sie **Konfiguration** aus.

1. Wählen Sie unter „Ingress NAT Rules“ (NAT-Eingangsregeln) die zuvor erstellten NAT-Regeln aus.

   :::image type="content" source="./media/nat-howto/config-nat.png" alt-text="Screenshot: Konfigurierte NAT-Regeln" lightbox="./media/nat-howto/config-nat.png":::

1. Klicken Sie auf **Speichern**, um die Konfigurationen auf die Verbindungsressource anzuwenden.

1. Wiederholen Sie die Schritte, um die NAT-Regeln für andere Verbindungsressourcen anzuwenden.

1. Aktivieren Sie bei Verwendung von BGP auf der Seite mit den NAT-Regeln die Option **BGP-Routenübersetzung aktivieren**, und klicken Sie auf **Speichern**. Beachten Sie, dass in der Tabelle jetzt die Verbindungen angezeigt werden, die mit den einzelnen NAT-Regeln verknüpft sind.

   :::image type="content" source="./media/nat-howto/nat-rules-linked.png" alt-text="Screenshot: Aktivieren von BGP" lightbox="./media/nat-howto/nat-rules-linked.png":::

Nach dem Ausführen dieser Schritte verfügen Sie über ein Setup, das der Topologie in [Diagramm 1](#diagram) entspricht.

## <a name="preview-limitations"></a><a name ="limits"></a>Einschränkungen der Vorschauversion

> [!IMPORTANT] 
> Während der Vorschauphase der NAT-Funktion gelten einige Einschränkungen. Einige davon werden vor der allgemeinen Verfügbarkeit behandelt.
>

* Die Netzwerkadressenübersetzung in Azure VPN Gateway unterstützt nur statische 1:1-NAT-Regeln. Dynamische NAT-Regeln werden nicht unterstützt.
* Die Netzwerkadressenübersetzung wird für die folgenden SKUs unterstützt: VpnGw2~5, VpnGw2AZ~5AZ.
* Die Netzwerkadressenübersetzung wird nur für standortübergreifende IPsec-/IKE-Verbindungen unterstützt. VNet-zu-VNet-Verbindungen oder P2S-Verbindungen werden nicht unterstützt.
* NAT-Regeln können während des Verbindungserstellungsprozesses nicht mit Verbindungsressourcen verknüpft werden. Erstellen Sie zuerst die Verbindungsressource, und ordnen Sie dann die NAT-Regeln auf der Seite mit der Verbindungskonfiguration zu.
* Verwenden Sie in der Vorschauversion **BGP**, und lassen Sie den Adressraum des lokalen Netzwerkgateways **leer**, wenn der Adressraum des lokalen Netzwerkgateways mit dem Adressraum des VNet identisch oder Teil dieses Adressraums ist. Statisches Routing (ohne BGP) wird aufgrund des Adresskonflikts zwischen lokalen Netzwerkgateways und dem VNet **nicht** unterstützt.
* Adressräume für verschiedene lokale Netzwerkgateways (lokale Netzwerke oder Filialen) können identisch sein, wenn Regeln vom Typ *IngressSNAT* für die Zuordnung zu nicht überlappenden Präfixen verwendet werden (siehe [Diagramm 1](#diagram)).
* NAT-Regeln werden für Verbindungen, für die *Use Policy Based Traffic Selectors* (Richtlinienbasierte Datenverkehrsselektoren verwenden) aktiviert ist, nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

Sobald die Verbindung hergestellt ist, können Sie Ihren virtuellen Netzwerken virtuelle Computer hinzufügen. Für diese Schritte finden Sie Informationen unter [Erstellen eines virtuellen Computers](../virtual-machines/windows/quick-create-portal.md) .
