---
title: 'Tutorial: Verwenden von Azure Virtual WAN zum Erstellen einer Point-to-Site-Verbindung mit Azure'
description: In diesem Tutorial wird beschrieben, wie Sie Azure Virtual WAN verwenden, um eine Benutzer-VPN-Verbindung (Point-to-Site) mit Azure zu erstellen.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 08/23/2021
ms.author: cherylmc
ms.openlocfilehash: e08123ce666efebf7db2e4c07167125a76b46609
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122779899"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>Tutorial: Erstellen einer Benutzer-VPN-Verbindung per Azure Virtual WAN

In diesem Tutorial wird beschrieben, wie Sie Virtual WAN zum Verbinden Ihrer Ressourcen in Azure über eine OpenVPN- oder IPsec/IKE-VPN-Verbindung (IKEv2) nutzen. Dazu wird eine Benutzer-VPN-Konfiguration (P2S) verwendet. Für diese Art von Verbindung muss auf jedem Clientcomputer, der eine Verbindung herstellt, der native VPN-Client konfiguriert sein.
* Wenn Sie eine Benutzer-VPN-Verbindung mit Azure AD-Authentifizierung erstellen möchten, lesen Sie stattdessen die Informationen im Artikel [Konfigurieren der Azure Active Directory-Authentifizierung für ein Benutzer-VPN](virtual-wan-point-to-site-azure-ad.md).
* Weitere Informationen zu Virtual WAN finden Sie auf der Seite mit der [Übersicht über Virtual WAN](virtual-wan-about.md).

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines virtuellen WAN
> * Erstellen der Benutzer-VPN-Konfiguration
> * Erstellen eines virtuellen Hubs und Gateways
> * Generieren der Clientkonfigurationsdateien
> * VPN-Clients konfigurieren
> * Verbinden mit einem VNet
> * Anzeigen Ihrer Virtual WAN-Instanz
> * Ändern von Einstellungen

:::image type="content" source="./media/virtual-wan-about/virtualwanp2s.png" alt-text="Virtual WAN-Diagramm":::

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [Before beginning](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Erstellen eines Virtual WAN

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-user-vpn-configuration"></a><a name="p2sconfig"></a>Erstellen einer Benutzer-VPN-Konfiguration

Die Benutzer-VPN-Konfiguration (P2S) definiert die Parameter für das Herstellen der Verbindung mit Remoteclients. Welche Anweisungen Sie befolgen, hängt von der Authentifizierungsmethode ab, die Sie verwenden möchten.

Wenn Sie die Authentifizierungsmethode auswählen, stehen Ihnen in den folgenden Schritten drei Optionen zur Auswahl. Für jede Methode gelten bestimmte Anforderungen. Wählen Sie eine der folgenden Methoden aus, und führen Sie dann die Schritte aus.

* **Azure-Zertifikate:** Für diese Konfiguration sind Zertifikate erforderlich. Sie müssen Zertifikate entweder generieren oder abrufen. Ein Clientzertifikat wird für jeden Client benötigt. Außerdem müssen die Stammzertifikatinformationen (öffentlicher Schlüssel) hochgeladen werden. Weitere Informationen zu den erforderlichen Zertifikaten finden Sie unter [Generieren und Exportieren von Zertifikaten für Punkt-zu-Standort-Verbindungen mithilfe von PowerShell](certificates-point-to-site.md).

* **Azure Active Directory-Authentifizierung:** Lesen Sie den Artikel [Konfigurieren der Azure Active Directory-Authentifizierung für ein Benutzer-VPN](virtual-wan-point-to-site-azure-ad.md), in dem die für diese Konfiguration erforderlichen Schritte erläutert werden.

* **RADIUS-basierte Authentifizierung:** Abrufen der IP-Adresse des RADIUS-Servers, des RADIUS-Servergeheimnisses und der Zertifikatinformationen.

### <a name="configuration-steps"></a>Konfigurationsschritte

[!INCLUDE [Create P2S configuration](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-a-virtual-hub-and-gateway"></a><a name="hub"></a>Erstellen eines virtuellen Hubs und eines Gateways

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]

## <a name="generate-client-configuration-files"></a><a name="download"></a>Generieren der Clientkonfigurationsdateien

Wenn Sie mithilfe eines Benutzer-VPN (P2S) eine Verbindung mit einem VNet herstellen, verwenden Sie den VPN-Client, der nativ auf dem Betriebssystem installiert ist, von dem aus Sie eine Verbindung herstellen. Alle erforderlichen Konfigurationseinstellungen für die VPN-Clients sind in einer ZIP-Datei für die VPN-Clientkonfiguration enthalten. Mithilfe der Einstellungen in der ZIP-Datei können Sie die VPN-Clients einfach konfigurieren. Die von Ihnen generierten VPN-Clientkonfigurationsdateien gelten speziell für die Benutzer-VPN-Gatewaykonfiguration für Ihr Gateway. In diesem Abschnitt generieren Sie die zum Konfigurieren Ihrer VPN-Clients verwendeten Dateien und laden sie herunter.

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

## <a name="configure-vpn-clients"></a><a name="configure-client"></a>VPN-Clients konfigurieren

Verwenden Sie das heruntergeladene Profilpaket, um die VPN-Clients für den Remotezugriff zu konfigurieren. Die Vorgehensweise variiert für die jeweiligen Betriebssysteme. Befolgen Sie die Anweisungen, die für Ihr System gelten.
Sobald Sie die Konfiguration Ihres Clients abgeschlossen haben, können Sie eine Verbindung herstellen.

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="connect-vnet-to-hub"></a><a name="connect-vnet"></a>Verbinden eines VNET mit einem Hub

In diesem Abschnitt erstellen Sie eine Verbindung zwischen Ihrem virtuellen Hub und Ihrem VNet. Für dieses Tutorial müssen Sie die Routingeinstellungen nicht konfigurieren.

[!INCLUDE [Connect virtual network](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="view-a-virtual-wan"></a><a name="viewwan"></a>Anzeigen eines virtuellen WAN

1. Navigieren Sie zu Ihrem **virtuellen WAN**.

1. Auf der Seite **Übersicht** steht jeder Punkt auf der Karte für einen Hub.

1. Im Abschnitt **Hubs und Verbindungen** können Sie den Hubstatus, die Site, die Region, den VPN-Verbindungsstatus und die Anzahl von ein- und ausgehenden Bytes anzeigen.

## <a name="modify-settings"></a>Ändern von Einstellungen

### <a name="modify-client-address-pool"></a><a name="address-pool"></a>Ändern des Clientadresspools

[!INCLUDE [Modify client address pool](../../includes/virtual-wan-client-address-pool-include.md)]

### <a name="modify-dns-servers"></a><a name="dns"></a>Ändern der DNS-Server

1. Navigieren Sie zu **Virtueller Hub > Benutzer-VPN (Point-to-Site)** .

1. Klicken Sie auf den Wert neben **Custom DNS Servers** (Benutzerdefinierte DNS-Server), um die Seite **Edit User VPN gateway** (Benutzer-VPN-Gateway bearbeiten) zu öffnen.

1. Bearbeiten Sie auf der Seite **Edit User VPN gateway** (Benutzer-VPN-Gateway bearbeiten) das Feld **Custom DNS Servers** (Benutzerdefinierte DNS-Server). Geben Sie die IP-Adressen der DNS-Server in die Textfelder **Custom DNS Servers** (Benutzerdefinierte DNS-Server) ein. Sie können bis zu fünf DNS-Server angeben.

1. Klicken Sie unten auf der Seite auf **Bearbeiten**, um die Einstellungen zu überprüfen.

1. Klicken Sie auf **Bestätigen**, um Ihre Einstellungen zu speichern. Alle Änderungen auf dieser Seite können bis zu 30 Minuten in Anspruch nehmen.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Bereinigen von Ressourcen

Wenn Sie die von Ihnen erstellten Ressourcen nicht mehr benötigen, können Sie sie löschen. Einige Virtual WAN-Ressourcen müssen aufgrund von Abhängigkeiten in einer bestimmten Reihenfolge gelöscht werden. Das Löschen kann bis zu 30 Minuten dauern.

[!INCLUDE [Delete resources](../../includes/virtual-wan-resource-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte


> [!div class="nextstepaction"]
> * [Verwalten des sicheren Zugriffs auf Ressourcen in Spoke-VNets](manage-secure-access-resources-spoke-p2s.md)
