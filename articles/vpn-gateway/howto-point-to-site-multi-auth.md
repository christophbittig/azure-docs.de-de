---
title: 'Herstellen zu einem VNet mit dem P2S-VPN und mehreren Authentifizierungstypen: Portal'
titleSuffix: Azure VPN Gateway
description: Erfahren Sie, wie Sie über P2S unter Verwendung mehrerer Authentifizierungstypen eine Verbindung zu einem VNet herstellen.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 07/21/2021
ms.author: cherylmc
ms.openlocfilehash: f3a19958770e157ed31dc3104331bcf5f860041d
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/27/2021
ms.locfileid: "114719586"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-multiple-authentication-types-azure-portal"></a>Konfigurieren einer Point-to-Site-VPN-Verbindung mit einem VNet mithilfe mehrerer Authentifizierungstypen: Azure-Portal

Dieser Artikel enthält Informationen zum sicheren Verbinden von einzelnen Clients, auf denen Windows, Linux oder macOS ausgeführt wird, mit einem Azure-VNET. Eine P2S-VPN-Verbindung ist nützlich, wenn Sie an einem Remotestandort (beispielsweise bei der Telearbeit zu Hause oder in einer Konferenz) eine Verbindung mit Ihrem VNET herstellen möchten. Sie können anstelle einer Site-to-Site-VPN-Verbindung auch P2S verwenden, wenn nur einige wenige Clients eine Verbindung mit einem VNET herstellen müssen. Point-to-Site-Verbindungen erfordern weder ein VPN-Gerät noch eine öffentliche IP-Adresse. P2S erstellt die VPN-Verbindung entweder über SSTP (Secure Socket Tunneling Protocol) oder IKEv2. Weitere Informationen zu Point-to-Site-VPN-Verbindungen finden Sie unter [Informationen zu Point-to-Site-VPN](point-to-site-about.md).

:::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-diagram.png" alt-text="Herstellen einer Verbindung zwischen einem Computer und einem Azure VNET – Diagramm der Point-to-Site-Verbindung":::

Weitere Informationen zu Point-to-Site-VPN finden Sie unter [Informationen zu Point-to-Site-VPN](point-to-site-about.md). Informationen zum Erstellen dieser Konfiguration mithilfe von Azure PowerShell finden Sie unter [Konfigurieren eines Point-to-Site-VPN mit Azure PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md).

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie sicher, dass Sie über ein Azure-Abonnement verfügen. Wenn Sie noch kein Azure-Abonnement besitzen, können Sie Ihre [MSDN-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) aktivieren oder sich für ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial) registrieren.

Mehrere Authentifizierungstypen auf demselben VPN-Gateway werden nur mit dem OpenVPN-Tunneltyp unterstützt.

### <a name="example-values"></a><a name="example"></a>Beispielwerte

Sie können die folgenden Werte zum Erstellen einer Testumgebung oder zum besseren Verständnis der Beispiele in diesem Artikel nutzen:

* **VNet-Name:** VNet1
* **Adressraum:** 10.1.0.0/16<br>In diesem Beispiel verwenden wir nur einen einzelnen Adressraum. Sie können für Ihr VNet aber auch mehrere Adressräume verwenden.
* **Subnetzname:** FrontEnd
* **Subnetzadressbereich:** 10.1.0.0/24
* **Abonnement:** Falls Sie über mehrere Abonnements verfügen, vergewissern Sie sich, dass Sie das richtige Abonnement verwenden.
* **Ressourcengruppe:** TestRG1
* **Standort:** East US
* **GatewaySubnet:** 10.1.255.0/27<br>
* **SKU:** VpnGw2
* **Generation:** Generation 2
* **Gatewaytyp**: VPN
* **VPN-Typ:** routenbasiert
* **Öffentliche IP-Adresse:** VNet1GWpip
* **Verbindungstyp:** Punkt-zu-Standort
* **Clientadresspool:** 172.16.201.0/24<br>VPN-Clients, die über diese Point-to-Site-Verbindung eine Verbindung mit dem VNet herstellen, erhalten eine IP-Adresse aus dem Clientadresspool.

## <a name="create-a-virtual-network"></a><a name="createvnet"></a>Erstellen eines virtuellen Netzwerks

Stellen Sie zunächst sicher, dass Sie über ein Azure-Abonnement verfügen. Wenn Sie noch kein Azure-Abonnement besitzen, können Sie Ihre [MSDN-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) aktivieren oder sich für ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial) registrieren.

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="virtual-network-gateway"></a><a name="creategw"></a>Virtuelles Netzwerkgateway

In diesem Schritt erstellen Sie das virtuelle Netzwerkgateway für Ihr VNet. Häufig kann die Erstellung eines Gateways je nach ausgewählter Gateway-SKU mindestens 45 Minuten dauern.

>[!NOTE]
>Die Gateway-SKU „Basic“ unterstützt nicht den OpenVPN-Tunneltyp.
>

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gw-portal-include.md)]
[!INCLUDE [Configure PIP settings](../../includes/vpn-gateway-add-gw-pip-portal-include.md)]

Der Bereitstellungsstatus wird auf der Übersichtsseite für Ihr Gateway angezeigt. Die gesamte Erstellung und Bereitstellung eines Gateways kann häufig 45 Minuten oder länger dauern. Nach der Erstellung des Gateways können Sie die zugewiesene IP-Adresse unter dem Virtual Network im Portal anzeigen. Das Gateway wird als verbundenes Gerät angezeigt.

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="client-address-pool"></a><a name="addresspool"></a>Clientadresspool

Der Clientadresspool ist ein Bereich privater IP-Adressen, die Sie angeben. Den Clients, die eine Verbindung über ein P2S-VPN herstellen, wird dynamisch eine IP-Adresse aus diesem Bereich zugewiesen. Verwenden Sie einen privaten IP-Adressbereich, der sich nicht mit dem lokalen Standort überschneidet, aus dem Sie Verbindungen herstellen möchten. Der Bereich darf sich auch nicht mit dem VNET überschneiden, mit dem Sie Verbindungen herstellen möchten. Wenn Sie mehrere Protokolle konfigurieren und SSTP eines der Protokolle ist, wird der konfigurierte Adresspool gleichmäßig auf die konfigurierten Protokolle aufgeteilt.

1. Navigieren Sie nach Erstellung des Gateways für virtuelle Netzwerke auf dem Blatt des Gateways für virtuelle Netzwerke zum Abschnitt **Einstellungen**. Klicken Sie unter **Einstellungen** auf **Punkt-zu-Standort-Konfiguration**. Wählen Sie **Jetzt konfigurieren** aus, um die Konfigurationsseite zu öffnen.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png" alt-text="Screenshot der Seite einer Point-to-Site-Konfiguration" lightbox="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png":::
1. Auf der Seite **Point-to-Site-Konfiguration** können Sie verschiedene Einstellungen konfigurieren. Fügen Sie im Feld **Adresspool** den Bereich mit privaten IP-Adressen hinzu, den Sie verwenden möchten. VPN-Clients wird aus dem von Ihnen angegebenen Bereich dynamisch eine IP-Adresse zugewiesen. Der Wert der kleinstmöglichen Subnetzmaske beträgt 29 Bit für die Aktiv/Passiv-Konfiguration und 28 Bit für die Aktiv/Aktiv-Konfiguration.

   :::image type="content" source="./media/howto-point-to-site-multi-auth/address.jpg" alt-text="Screenshot eines Adresspools":::

1. Fahren Sie mit dem nächsten Abschnitt fort, um die Authentifizierungs- und Tunneltypen zu konfigurieren.

## <a name="authentication-and-tunnel-types"></a><a name="type"></a>Authentifizierungs- und Tunneltypen

In diesem Abschnitt konfigurieren Sie den Authentifizierungstyp und den Tunneltyp. Wenn auf der Seite **Point-to-Site-Konfiguration** weder der **Tunneltyp** noch der **Authentifizierungstyp** angezeigt wird, verwendet Ihr Gateway die Basic-SKU. Die Basic-SKU unterstützt keine IKEv2- oder RADIUS-Authentifizierung. Wenn Sie diese Einstellungen verwenden möchten, müssen Sie das Gateway löschen und mit einer anderen Gateway-SKU neu erstellen.

   :::image type="content" source="./media/howto-point-to-site-multi-auth/multiauth.jpg" alt-text="Screenshot des Authentifizierungstyps":::

### <a name="tunnel-type"></a><a name="tunneltype"></a>Tunneltyp

Wählen Sie auf der Seite **Point-to-Site-Konfiguration** **OpenVPN (SSL)** als Tunneltyp aus.

### <a name="authentication-type"></a><a name="authenticationtype"></a>Authentifizierungsart

Wählen Sie für de **Authentifizierungstyp** den gewünschten Typ aus. Folgende Optionen stehen zur Verfügung:

* Azure-Zertifikat
* RADIUS
* Azure Active Directory

Abhängig von den ausgewählten Authentifizierungstypen werden die unterschiedlichen Felder für die Konfigurationseinstellung angezeigt, die Sie ausfüllen müssen. Geben Sie die erforderlichen Informationen an, und klicken Sie oben auf der Seite auf **Speichern**, um alle Konfigurationseinstellungen zu speichern.

Weitere Informationen zum Authentifizierungstyp finden Sie auf den folgenden Seiten:

* [Azure-Zertifikat](vpn-gateway-howto-point-to-site-resource-manager-portal.md#type)
* [RADIUS](point-to-site-how-to-radius-ps.md)
* [Azure Active Directory](openvpn-azure-ad-tenant.md)

## <a name="vpn-client-configuration-package"></a><a name="clientconfig"></a>VPN-Clientkonfigurationspaket

VPN-Clients müssen mit Clientkonfigurationseinstellungen konfiguriert werden. Das VPN-Clientkonfigurationspaket enthält Dateien mit den Einstellungen zum Konfigurieren von VPN-Clients, damit sie über eine P2S-Verbindung mit einem VNet verbunden werden können.

Anweisungen zum Generieren und Installieren von Konfigurationsdateien für den VPN-Client finden Sie im Artikel, der sich auf Ihre Konfiguration bezieht:

* [Erstellen und Installieren von VPN-Clientkonfigurationsdateien für P2S-Konfigurationen mit nativer Azure-Zertifikatauthentifizierung](point-to-site-vpn-client-configuration-azure-cert.md)
* [Azure Active Directory-Authentifizierung: Konfigurieren eines VPN-Clients für P2S-OpenVPN-Protokollverbindungen](openvpn-azure-ad-client.md)

## <a name="point-to-site-faq"></a><a name="faq"></a>Point-to-Site – Häufig gestellte Fragen

In diesem Abschnitt finden Sie Informationen zu häufig gestellten Fragen (FAQ), die sich auf Point-to-Site-Konfigurationen beziehen. Weitere Informationen zu VPN Gateway finden Sie auch unter [Häufig gestellte Fragen zu VPN Gateway](vpn-gateway-vpn-faq.md).

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="next-steps"></a>Nächste Schritte

Sobald die Verbindung hergestellt ist, können Sie Ihren virtuellen Netzwerken virtuelle Computer hinzufügen. Weitere Informationen finden Sie unter [Virtuelle Computer](../index.yml) . Weitere Informationen zu Netzwerken und virtuellen Computern finden Sie unter [Azure- und Linux-VM-Netzwerke (Übersicht)](../virtual-machines/network-overview.md).

Informationen zur P2S-Problembehandlung finden Sie unter [Problembehandlung: Probleme mit Azure P2S-Verbindungen (Point-to-Site)](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
