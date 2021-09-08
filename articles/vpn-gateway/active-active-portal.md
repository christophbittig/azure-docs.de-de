---
title: 'Konfigurieren von Aktiv/Aktiv-VPN-Gateways: Azure-Portal'
titleSuffix: Azure VPN Gateway
description: Erfahren Sie, wie Sie virtuelle Netzwerkgateways vom Typ „Aktiv-Aktiv“ mithilfe des Azure-Portals konfigurieren können.
services: vpn-gateway
author: JackStromberg
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 07/22/2021
ms.author: jstrom
ms.openlocfilehash: 1a5ca58b9e56826c7173c499b64ebabc80ddf11e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122340028"
---
# <a name="configure-active-active-vpn-gateways-using-the-portal"></a>Konfigurieren von Aktiv/Aktiv-VPN-Gateways über das Portal

Dieser Artikel hilft Ihnen, hochverfügbare Aktiv/Aktiv-VPN-Gateways mithilfe des [Resource Manager-Bereitstellungsmodells](../azure-resource-manager/management/deployment-models.md) und des Azure-Portals zu erstellen. Sie können auch ein Aktiv/Aktiv-Gateway mithilfe von [PowerShell](vpn-gateway-activeactive-rm-powershell.md) konfigurieren.

Um für lokale und VNET-zu-VNET-Verbindungen Hochverfügbarkeit zu erzielen, sollten Sie mehrere VPN-Gateways bereitstellen und mehrere parallele Verbindungen zwischen Ihren Netzwerken und Azure herstellen. Eine Übersicht über Verbindungsoptionen und die Topologie finden Sie unter [Standortübergreifende Verbindungen und VNet-zu-VNet-Verbindungen mit Hochverfügbarkeit](vpn-gateway-highlyavailable.md).

> [!IMPORTANT]
> Der Aktiv/Aktiv-Modus ist für alle SKUs mit Ausnahme von Basic oder Standard verfügbar. Weitere Informationen finden Sie unter [Konfigurationseinstellungen](vpn-gateway-about-vpn-gateway-settings.md#gwsku).
>

Die Schritte in diesem Artikel helfen Ihnen beim Konfigurieren eines VPN-Gateways im Aktiv/Aktiv-Modus. Es gibt einige Unterschiede zwischen dem Aktiv/Aktiv-Modus und dem Aktiv/Standby-Modus. Die anderen Eigenschaften sind mit den Eigenschaften von anderen Gateways identisch, die nicht den Aktiv/Aktiv-Modus aufweisen. 

* Aktiv/Aktiv-Gateways verfügen über zwei Gateway-IP-Konfigurationen und zwei öffentliche IP-Adressen.
* Für Aktiv/Aktiv-Gateways ist die Aktiv/Aktiv-Einstellung aktiviert.
* Die Gateway-SKU des virtuellen Netzwerks darf nicht Basic oder Standard sein.

Wenn Sie bereits über ein VPN-Gateway verfügen, können Sie ein [vorhandenes VPN-Gateway](#update) vom Aktiv/Standby-Modus in den Aktiv/Aktiv-Modus oder vom Aktiv/Aktiv-Modus in den Aktiv/Standby-Modus aktualisieren.

## <a name="create-a-vnet"></a><a name="vnet"></a>Erstellen eines VNET

Wenn Sie noch nicht über ein VNet verfügen, das Sie verwenden möchten, erstellen Sie ein VNet mit den folgenden Werten:

* **Ressourcengruppe:** TestRG1
* **Name:** VNet1
* **Region:** (USA) USA, Osten
* **IPv4-Adressraum**: 10.1.0.0/16
* **Subnetzname:** FrontEnd
* **Subnetzadressraum**: 10.1.0.0/24

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-an-active-active-vpn-gateway"></a><a name="gateway"></a>Erstellen eines Aktiv/Aktiv-VPN-Gateways

In diesem Schritt erstellen Sie ein Aktiv/Aktiv-VNet-Gateway (VPN-Gateway) für Ihr VNet. Häufig kann die Erstellung eines Gateways je nach ausgewählter Gateway-SKU mindestens 45 Minuten dauern.

Erstellen Sie ein virtuelles Netzwerkgateway mit den folgenden Werten:

* **Name:** VNet1GW
* **Region:** East US
* **Gatewaytyp**: VPN
* **VPN-Typ:** routenbasiert
* **SKU:** VpnGw2
* **Generation:** Generation2
* **Virtuelles Netzwerk:** VNet1
* **Adressbereich für Gatewaysubnetz**: 10.1.255.0/27
* **Öffentliche IP-Adresse**: Neu erstellen
* **Öffentliche IP-Adresse:** VNet1GWpip

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gw-portal-include.md)]
[!INCLUDE [Configure PIP settings](../../includes/vpn-gateway-add-gw-pip-active-portal-include.md)]

Der Bereitstellungsstatus wird auf der Übersichtsseite für Ihr Gateway angezeigt. Nach der Erstellung des Gateways können Sie die zugewiesene IP-Adresse unter dem Virtual Network im Portal anzeigen. Das Gateway wird als verbundenes Gerät angezeigt.

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="update-an-existing-vpn-gateway"></a><a name ="update"></a> Aktualisieren eines vorhandenen VPN-Gateways

Dieser Abschnitt zeigt Ihnen, wie Sie den Modus eines vorhandenen Azure-VPN-Gateways vom Aktiv/Standby-Modus in den Aktiv/Aktiv-Modus und vom Aktiv/Aktiv-Modus in den Aktiv/Standby-Modus ändern. Wenn Sie ein Aktiv/Standby- in ein Aktiv/Aktiv-Gateway ändern, erstellen Sie eine weitere öffentliche IP-Adresse und fügen anschließend eine zweite Gateway-IP-Konfiguration hinzu. 

### <a name="change-active-standby-to-active-active"></a>Ändern des Aktiv/Standby-Modus in den Aktiv/Aktiv-Modus

Führen Sie die folgenden Schritte aus, um das Gateway im Aktiv/Standby-Modus in den Aktiv/Aktiv-Modus zu konvertieren. Wenn Ihr Gateway mithilfe des [Resource Manager-Bereitstellungsmodells](../azure-resource-manager/management/deployment-models.md) erstellt wurde, können Sie das Upgrade der SKU auch auf dieser Seite durchführen.

1. Navigieren Sie zu der Seite für Ihr Gateway für virtuelle Netzwerke.

1. Wählen Sie im linken Menü die Option **Konfiguration** aus.

1. Konfigurieren Sie auf der Seite **Konfiguration** die folgenden Einstellungen: 

   * Ändern Sie den Aktiv/Aktiv-Modus in **Aktiviert**.
   * Klicken Sie auf **Weitere Gateway-IP-Konfiguration erstellen**.

   :::image type="content" source="./media/active-active-portal/configuration.png" alt-text="Screenshot: Konfigurationsseite":::

1. Geben Sie auf der Seite **Öffentliche IP-Adresse auswählen** entweder eine vorhandene öffentliche IP-Adresse an, die den Kriterien entspricht, oder wählen Sie **+Neu erstellen** aus, um eine neue öffentliche IP-Adresse zu erstellen, die für die zweite VPN-Gatewayinstanz verwendet wird.

1. Wählen Sie auf der Seite **Öffentliche IP-Adresse erstellen** die SKU **Basic** aus, und klicken Sie dann auf **OK**.

1. Klicken Sie oben auf der Seite **Konfiguration** auf **Speichern**. Diese Aktualisierung kann 30 bis 45 Minuten dauern.

### <a name="change-active-active-to-active-standby"></a>Ändern des Aktiv/Aktiv-Modus in den Aktiv/Standby-Modus

Führen Sie die folgenden Schritte aus, um das Gateway im Aktiv/Aktiv-Modus in den Aktiv/Standby-Modus zu konvertieren.

1. Navigieren Sie zu der Seite für Ihr Gateway für virtuelle Netzwerke.

1. Wählen Sie im linken Menü die Option **Konfiguration** aus.

1. Ändern Sie auf der Seite **Konfiguration** den Aktiv/Aktiv-Modus in **Deaktiviert**.

1. Klicken Sie oben auf der Seite **Konfiguration** auf **Speichern**.

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Konfigurieren der Verbindungen finden Sie in den folgenden Artikeln:

* [Site-to-Site-VPN-Verbindungen](./tutorial-site-to-site-portal.md)
* [VNET-to-VNET-Verbindungen](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md#configure-the-vnet1-gateway-connection)