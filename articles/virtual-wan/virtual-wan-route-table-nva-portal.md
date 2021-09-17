---
title: 'Virtual WAN: Erstellen einer Routingtabelle für den virtuellen Hub in NVA: Azure-Portal'
description: Informationen zu einer Routingtabelle für einen virtuellen Hub für Virtual WAN zum Steuern des Datenverkehrs zu einem virtuellen Netzwerkgerät im Portal.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/19/2021
ms.author: cherylmc
ms.openlocfilehash: e57f419daeb112be0925158109697bb2a9b399e8
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122779979"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>Erstellen einer Routingtabelle für den Virtual WAN-Hub für virtuelle Netzwerkgeräte: Azure-Portal

In diesem Artikel erfahren Sie, wie Sie Datenverkehr von einer Niederlassung (lokaler Standort) steuern, die über ein virtuelles Netzwerkgerät (Network Virtual Appliance, NVA) und den Virtual WAN-Hub mit einem virtuellen Spoke-Netzwerk (VNET) verbunden ist.

![Virtual WAN-Diagramm](./media/virtual-wan-route-table-nva/vwanroute.png)

## <a name="before-you-begin"></a>Voraussetzungen

Vergewissern Sie sich, dass die folgenden Kriterien erfüllt sind:

*  Sie verfügen über ein virtuelles Netzwerkgerät. Ein virtuelles Netzwerkgerät ist Drittanbietersoftware Ihrer Wahl, die in der Regel über Azure Marketplace in einem virtuellen Netzwerk bereitgestellt wird.

    * Sie haben der Netzwerkschnittstelle des virtuellen Netzwerkgeräts eine private IP-Adresse zugewiesen.

    * Das virtuelle Netzwerkgerät wird nicht im virtuellen Hub bereitgestellt. Es muss in einem gesonderten virtuellen Netzwerk bereitgestellt werden.

    *  Mit dem virtuellen Netzwerk des virtuellen Netzwerkgeräts kann mindestens ein virtuelles Netzwerk verbunden sein. In diesem Artikel wird dieses virtuelle Netzwerk als „indirektes Spoke-VNET“ bezeichnet. Diese virtuellen Netzwerke können mithilfe von VNET-Peering mit dem VNET des virtuellen Netzwerkgeräts verbunden werden. Die VNET-Peeringlinks sind in der Abbildung oben als schwarze Pfeile zwischen VNET 1, VNET 2 und NVA-VNET dargestellt.
*  Sie haben zwei virtuelle Netzwerke erstellt. Diese werden als Spoke-VNETs verwendet.

    * Die VNET-Spoke-Adressräume lauten wie folgt: VNet1: 10.0.2.0/24 und VNET2: 10.0.3.0/24. Informationen zum Erstellen eines virtuellen Netzwerks finden Sie unter [Erstellen eines virtuellen Netzwerks](../virtual-network/quick-create-portal.md).

    * Stellen Sie sicher, dass in den VNETs keine Gateways für virtuelle Netzwerke vorhanden sind.

    * Die VNETs erfordern kein Gatewaysubnetz.

## <a name="1-sign-in"></a><a name="signin"></a>1. Anmelden

Navigieren Sie in einem Browser zum [Azure-Portal](https://portal.azure.com) , und melden Sie sich mit Ihrem Azure-Konto an.

## <a name="2-create-a-virtual-wan"></a><a name="vwan"></a>2. Erstellen eines virtuellen WAN

Erstellen Sie ein virtuelles WAN. Sie können die folgenden Beispiele nutzen oder diese mit ihren eigenen ersetzen.

* **Name des virtuellen WAN:** myVirtualWAN
* **Ressourcengruppe:** testRG
* **Standort:** USA (Westen)

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="3-create-a-hub"></a><a name="hub"></a>3. Erstellen eines Hubs

Erstellen Sie den Hub. Sie können die folgenden Beispiele nutzen oder diese mit ihren eigenen ersetzen.

* **Standort:** USA (Westen)
* **Name:** westushub
* **Privater Adressraum des Hubs:** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-empty-hub-include.md)]

## <a name="4-create-and-apply-a-hub-route-table"></a><a name="route"></a>4. Erstellen und Anwenden einer Routingtabelle für einen Hub

Aktualisieren Sie den Hub mit einer Routingtabelle für den Hub. Verwenden Sie die folgenden Beispielwerte:

* **Spoke-VNet-Adressräume:** (VNET1 und VNET2) 10.0.2.0/24 und 10.0.3.0/24
* **Private IP-Adressen der Netzwerkschnittstelle der DMZ des virtuellen Netzwerkgeräts:** 10.0.4.5

1. Navigieren Sie zu Ihrem virtuellen WAN.
2. Klicken Sie auf den Hub, für den Sie eine Routingtabelle erstellen möchten.
3. Klicken Sie auf **...** und dann auf **Virtuellen Hub bearbeiten**.
4. Scrollen Sie auf der Seite **Virtuellen Hub bearbeiten** nach unten, und aktivieren Sie das Kontrollkästchen **Tabelle für Routing verwenden**.
5. Fügen Sie der Spalte **Wenn das Zielpräfix folgendermaßen lautet** die Adressräume hinzu. Fügen Sie der Spalte **An Adresse für nächsten Hop senden** die private IP-Adresse der Netzwerkschnittstelle der DMZ des virtuellen Netzwerkgeräts hinzu.

   > [!NOTE]
   > Das DMZ NVA-Netzwerk kann für den lokalen Hub verwendet werden.
   
6. Klicken Sie auf **Bestätigen**, um die Hubressource mit den Einstellungen der Routingtabelle zu aktualisieren.

## <a name="5-create-the-vnet-connections"></a><a name="connections"></a>5. Herstellen der VNET-Verbindungen

Stellen Sie eine VNET-Verbindung zwischen jedem indirekten Spoke-VNET (VNET1 und VNET2) und dem Hub her. Die VNET-Verbindungen sind in der Abbildung oben durch blaue Pfeile dargestellt. Erstellen Sie anschließend eine VNET-Verbindung vom NVA-VNET zum Hub (schwarzer Pfeil in der Abbildung).

 Für diesen Schritt können Sie die folgenden Werte verwenden:

| Name des virtuellen Netzwerks| Verbindungsname|
| --- | --- |
| VNet1 | testconnection1 |
| VNet2 | testconnection2 |
| NVAVNet | testconnection3 |

Wiederholen Sie das folgende Verfahren für jedes virtuelle Netzwerk, mit dem Sie eine Verbindung herstellen möchten.

1. Klicken Sie auf der Seite für Ihr virtuelles WAN auf **Virtuelle Netzwerkverbindungen**.
2. Klicken Sie auf der Seite für die VNET-Verbindung auf **+Add connection** (+Verbindung hinzufügen).
3. Füllen Sie auf der Seite **Add connection** (Verbindung hinzufügen) die folgenden Felder aus:

    * **Verbindungsname**: Dies ist der Name Ihrer Verbindung.
    * **Hubs**: Wählen Sie den Hub aus, den Sie dieser Verbindung zuordnen möchten.
    * **Abonnement**: Überprüfen Sie das Abonnement.
    * **Virtuelles Netzwerk**: Wählen Sie das virtuelle Netzwerk aus, das Sie mit diesem Hub verbinden möchten. Für das virtuelle Netzwerk kann nicht bereits ein Gateway für virtuelle Netzwerke vorhanden sein.
4. Klicken Sie auf **OK**, um die Verbindung zu erstellen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Virtual WAN finden Sie auf der Seite mit der [Übersicht über Virtual WAN](virtual-wan-about.md).
