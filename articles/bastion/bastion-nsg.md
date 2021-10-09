---
title: Arbeiten mit virtuellen Computern und Netzwerksicherheitsgruppen in Azure Bastion
description: Erfahren Sie mehr über die Verwendung von Netzwerksicherheitsgruppen mit Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/21/2021
ms.author: cherylmc
ms.openlocfilehash: adccd5873030adcc5c286ed8d23326796c27ab9c
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/27/2021
ms.locfileid: "129080976"
---
# <a name="working-with-nsg-access-and-azure-bastion"></a>Verwenden von NSG-Zugriff und Azure Bastion

Bei der Arbeit mit Azure Bastion können Sie Netzwerksicherheitsgruppen (NSGs) verwenden. Weitere Informationen finden Sie unter [Sicherheitsgruppen](../virtual-network/network-security-groups-overview.md).

:::image type="content" source="./media/bastion-nsg/figure-1.png" alt-text="NSG":::

In diesem Diagramm:

* Der Bastionhost wird im virtuellen Netzwerk bereitgestellt.
* Der Benutzer stellt in einem HTML5-Browser eine Verbindung mit dem Azure-Portal her.
* Der Benutzer navigiert zum virtuellen Azure-Computer, um eine RDP-/SSH-Verbindung herzustellen.
* Verbindungsintegration: RDP-/SSH-Sitzung innerhalb des Browsers mit nur einem Klick
* Für den virtuellen Azure-Computer ist keine öffentliche IP-Adresse erforderlich.

## <a name="network-security-groups"></a><a name="nsg"></a>Netzwerksicherheitsgruppen

In diesem Abschnitt wird der Netzwerkdatenverkehr zwischen dem Benutzer und Azure Bastion und weiter zu virtuellen Zielcomputern in Ihrem virtuellen Netzwerk gezeigt:

> [!IMPORTANT]
> Wenn Sie sich für die Verwendung einer NSG mit Ihrer Azure Bastion-Ressource entscheiden, **müssen** Sie alle folgenden Regeln für den eingehenden und ausgehenden Datenverkehr erstellen. Durch Auslassen einer der folgenden Regeln in Ihrer NSG wird verhindert, dass Ihre Azure Bastion-Ressource künftig erforderliche Updates empfängt. Damit ist Ihre Ressource anfällig für Sicherheitsrisiken.
> 

### <a name="azurebastionsubnet"></a><a name="apply"></a>AzureBastionSubnet

Azure Bastion wird speziell im Azure Bastion-Subnetz (***AzureBastionSubnet***) bereitgestellt.

* **Eingehender Datenverkehr:**

   * **Eingehender Datenverkehr über das öffentliche Internet:** Azure Bastion erstellt eine öffentliche IP-Adresse, für die Port 443 auf der öffentlichen IP-Adresse für den eingehenden Datenverkehr aktiviert sein muss. Ports 3389/22 müssen NICHT auf dem AzureBastionSubnet geöffnet sein.
   * **Eingehender Datenverkehr von der Azure Bastion-Steuerungsebene:** Aktivieren Sie für Steuerungsebenenkonnektivität den Eingangsport 443 über das Diensttag **GatewayManager**. Dadurch kann die Steuerungsebene (also der Gateway-Manager) mit Azure Bastion kommunizieren.
   * **Eingehender Datenverkehr von der Azure Bastion-Datenebene:** Aktivieren Sie für die Kommunikation auf Datenebene zwischen den zugrunde liegenden Komponenten von Azure Bastion die Ports 8080 und 5701 eingehend vom Diensttag **VirtualNetwork** zum Diensttag **VirtualNetwork**. Dadurch können die Azure Bastion-Komponenten miteinander kommunizieren.
   * **Eingehender Datenverkehr von Azure Load Balancer:** Aktivieren Sie für Integritätstests den eingehenden Port 443 aus dem Diensttag **AzureLoadBalancer**. Dadurch kann Azure Load Balancer die Konnektivität erkennen.


   :::image type="content" source="./media/bastion-nsg/inbound.png" alt-text="Screenshot mit eingehenden Sicherheitsregeln für Azure Bastion-Konnektivität." lightbox="./media/bastion-nsg/inbound.png":::

* **Ausgehender Datenverkehr:**

   * **Ausgehender Datenverkehr für virtuelle Zielcomputer:** Azure Bastion erreicht die Ziel-VMs über die private IP-Adresse. Die Netzwerksicherheitsgruppen müssen ausgehenden Datenverkehr für andere Ziel-VM-Subnetze für die Ports 3389 und 22 zulassen. Wenn Sie die benutzerdefinierte Port-Funktion als Teil der Standard-SKU verwenden, müssen die NSGs stattdessen den Egress-Verkehr zu anderen Ziel-VM-Subnetzen für den/die benutzerdefinierten Wert(e) zulassen, die Sie auf Ihren Ziel-VMs geöffnet haben.
   * **Ausgehender Datenverkehr an die Azure Bastion-Datenebene:** Aktivieren Sie für die Kommunikation auf Datenebene zwischen den zugrunde liegenden Komponenten von Azure Bastion die Ports 8080 und 5701 ausgehend vom Diensttag **VirtualNetwork** zum Diensttag **VirtualNetwork**. Dadurch können die Azure Bastion-Komponenten miteinander kommunizieren.
   * **Ausgehender Datenverkehr für andere öffentliche Endpunkte in Azure:** Azure Bastion muss eine Verbindung mit verschiedenen öffentlichen Endpunkten in Azure herstellen können, um beispielsweise Diagnose- und Messprotokolle zu speichern. Aus diesem Grund benötigt Azure Bastion ausgehende Konnektivität über den Port 443 mit dem Diensttag **AzureCloud**.
   * **Ausgehender Datenverkehr zum Internet:** Azure Bastion muss in der Lage sein, mit dem Internet zu kommunizieren, damit Sitzungen und Zertifikate überprüft werden können. Aus diesem Grund wird empfohlen, Port 80 ausgehend zum **Internet** zu aktivieren.


   :::image type="content" source="./media/bastion-nsg/outbound.png" alt-text="Screenshot mit ausgehenden Sicherheitsregeln für Azure Bastion-Konnektivität." lightbox="./media/bastion-nsg/outbound.png":::

### <a name="target-vm-subnet"></a>Ziel-VM-Subnetz
Dieses Subnetz enthält die Ziel-VM, mit der Sie eine RDP-/SSH-Verbindung herstellen möchten.

   * **Eingehender Datenverkehr von Azure Bastion:** Azure Bastion erreicht die Ziel-VM über die private IP-Adresse. RDP/SSH-Ports (Ports 3389/22 bzw. benutzerdefinierte Port-Werte, wenn Sie die benutzerdefinierte Port-Funktion als Teil der Standard-SKU verwenden) müssen auf der Seite der Ziel-VM über private IP geöffnet werden. Es empfiehlt sich, in dieser Regel den IP-Adressbereich des Azure Bastion-Subnetzes hinzuzufügen, damit diese Ports auf den virtuellen Zielcomputern in Ihrem Ziel-VM-Subnetz nur von Bastion geöffnet werden können.


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Bastion finden Sie in den [häufig gestellten Fragen](bastion-faq.md).
