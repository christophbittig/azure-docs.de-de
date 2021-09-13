---
title: Verwalten einer öffentlichen IP-Adresse mit einem VPN-Gateway
titleSuffix: Azure Virtual Network
description: Hier finden Sie Informationen zur Verwendung einer öffentlichen IP-Adresse mit einem VPN-Gateway sowie zum Ändern der Konfiguration.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 06/28/2021
ms.custom: template-how-to
ms.openlocfilehash: 5333408e59edfacd34d1e4dd4b14d0c598f8f268
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/07/2021
ms.locfileid: "113439049"
---
# <a name="manage-a-public-ip-address-with-a-vpn-gateway"></a>Verwalten einer öffentlichen IP-Adresse mit einem VPN-Gateway

Öffentliche IP-Adressen sind in zwei SKUs verfügbar. „Standard“ und „Basic“. Die Features der IP-Adresse richten sich nach der Wahl der SKU. Die SKU bestimmt die Ressourcen, denen die IP-Adresse zugeordnet werden kann. 

Ein VPN-Gateway ist ein Gateway für virtuelle Netzwerke, das verwendet wird, um verschlüsselten Datenverkehr zwischen einem virtuellen Azure-Netzwerk und einem lokalen Standort über das öffentliche Internet zu senden. Ein VPN-Gateway kann aber auch verwendet werden, um verschlüsselten Datenverkehr zwischen virtuellen Azure-Netzwerken über das Microsoft-Netzwerk zu senden. Ein virtuelles Netzwerk kann jeweils nur über ein einzelnes VPN-Gateway verfügen. VPN Gateway unterstützt öffentliche IP-Adressen der SKUs „Standard“ und „Basic“, abhängig von der SKU des VPN-Gateways. Präfixe für öffentliche IP-Adressen werden nicht unterstützt.

Ein VPN-Gateway benötigt eine öffentliche IP-Adresse für die Konfiguration. Eine öffentliche IP-Adresse wird als externer Verbindungspunkt des VPN verwendet. 

In diesem Artikel erfahren Sie, wie Sie ein VPN-Gateway mit einer vorhandenen öffentlichen IP-Adresse in Ihrem Abonnement erstellen. 

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Eine öffentliche IP-Adresse der Standard-SKU in Ihrem Abonnement. Die IP-Adresse darf keinen Ressourcen zugeordnet sein. Weitere Informationen zum Erstellen einer öffentlichen IP-Adresse der Standard-SKU finden Sie unter [Erstellen einer öffentlichen IP-Adresse: Azure-Portal](create-public-ip-portal.md).
    - Benennen Sie die neuen öffentlichen IP-Adressen für die Beispiele in diesem Artikel mit **myStandardPublicIP**.

## <a name="create-vpn-gateway-existing-public-ip"></a>Erstellen eines VPN-Gateways mit einer vorhandenen öffentlichen IP-Adresse

In diesem Abschnitt erstellen Sie ein VPN-Gateway. Sie wählen die in den Voraussetzungen erstellte IP-Adresse als öffentliche IP-Adresse für das VPN-Gateway aus.

### <a name="create-virtual-network"></a>Virtuelles Netzwerk erstellen

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Geben Sie im Suchfeld oben im Portal den Suchbegriff **Virtuelles Netzwerk** ein.

3. Wählen Sie in den Suchergebnissen **Virtuelle Netzwerke** aus.

4. Wählen Sie **+ Erstellen** aus.

5. Geben Sie unter **Virtuelles Netzwerk erstellen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | **Projektdetails** |   |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Resource group | Wählen Sie **Neu erstellen**. </br> Geben Sie **myResourceGroupVPN** ein. </br> Klicken Sie auf **OK**. |
    | **Instanzendetails** |   |
    | Name | Geben Sie **myVNet** ein. |
    | Region | Wählen Sie **USA, Westen 2** aus. |
    
6. Wählen Sie die Registerkarte **Überprüfen + erstellen** oder die blaue Schaltfläche **Überprüfen + erstellen** aus.

7. Klicken Sie auf **Erstellen**.

8. Geben Sie im Suchfeld oben im Portal den Suchbegriff **Virtuelles Netzwerk** ein.

9. Wählen Sie in den Suchergebnissen **Virtuelle Netzwerke** aus.

10. Wählen Sie unter **Virtuelle Netzwerke** die Option **myVNET** aus.

11. Wählen Sie unter **Einstellungen** für **myVNET** die Option **Subnetze** aus.

12. Wählen Sie **+ Gatewaysubnetz** aus.

13. Ändern Sie unter **Subnetz hinzufügen** die Angabe unter **Subnetzadressbereich** von **/24** in **/27**.

14. Wählen Sie **Speichern** aus.

### <a name="create-vpn-gateway"></a>Erstellen eines VPN-Gateways


2. Geben Sie im Suchfeld oben im Portal den Suchbegriff **Gateway für virtuelle Netzwerke** ein.

3. Wählen Sie in den Suchergebnissen **Gateways für virtuelle Netzwerke** aus.

4. Wählen Sie **+ Erstellen** aus.

5. Geben Sie unter **Gateway für virtuelle Netzwerke erstellen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | **Projektdetails** |   |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | **Instanzendetails** |   |
    | Name | Geben Sie **myVPNGateway** ein. |
    | Region | Wählen Sie **USA, Westen 2** aus. |
    | Gatewaytyp | Übernehmen Sie den Standardwert **VPN**. |
    | VPN-Typ | Übernehmen Sie den Standardwert **Routenbasiert**. |
    | SKU | Wählen Sie **VpnGw1AZ** aus. |
    | Virtuelles Netzwerk | Wählen Sie **myVNet** aus. |
    | Subnet | Durch die Eingabe wird automatisch das zuvor erstellte Gatewaysubnetz (**GatewaySubnet**) ausgewählt. |
    | **Öffentliche IP-Adresse** |   |
    | Öffentliche IP-Adresse | Wählen Sie **Vorhandenes verwenden** aus. |
    | Öffentliche IP-Adresse wählen | Wählen Sie **myStandardPublicIP** oder Ihre öffentliche IP-Adresse aus. |
    | Aktiv/Aktiv-Modus aktivieren | Übernehmen Sie den Standardwert **Deaktiviert**. |
    | Configure BGP (BGP konfigurieren) | Übernehmen Sie den Standardwert **Deaktiviert**. |

6. Wählen Sie die Registerkarte **Überprüfen + erstellen** oder die blaue Schaltfläche **Überprüfen + erstellen** aus.

7. Klicken Sie auf **Erstellen**.

> [!NOTE]
> Dies ist eine einfache Bereitstellung einer VPN Gateway-Instanz. Informationen zur erweiterten Konfiguration und Einrichtung finden Sie unter [Tutorial: Erstellen und Verwalten eines VPN-Gateways über das Azure-Portal](../vpn-gateway/tutorial-create-gateway-portal.md).
>
> Weitere Informationen zu Azure VPN Gateway finden Sie unter [Was ist VPN Gateway?](../vpn-gateway/vpn-gateway-about-vpngateways.md).

## <a name="change-or-remove-public-ip-address"></a>Ändern oder Entfernen öffentlicher IP-Adressen

Das VPN-Gateway unterstützt das Ändern der öffentlichen IP-Adresse nach der Erstellung nicht.

## <a name="caveats"></a>Vorbehalte

* Öffentliche IPv6-Adressen werden derzeit für VPN-Gateways nicht unterstützt.
 
## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie eine VPN Gateway-Instanz erstellen und eine vorhandene öffentliche IP-Adresse verwenden. 

- Weitere Informationen zu öffentlichen IP-Adressen in Azure finden Sie unter [Öffentliche IP-Adressen](public-ip-addresses.md).
- Weitere Informationen zu VPN-Gateways finden Sie unter [Was ist VPN Gateway?](../vpn-gateway/vpn-gateway-about-vpngateways.md).