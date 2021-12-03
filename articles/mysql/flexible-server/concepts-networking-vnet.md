---
title: Übersicht über den Zugriff auf private Netzwerke – Flexibler Azure Database for MySQL-Server
description: Dieser Artikel enthält Informationen zu den Optionen für den Zugriff auf private Netzwerke in der Bereitstellungsoption „Flexibler Server“ für Azure Database for MySQL.
author: Madhusoodanan
ms.author: dimadhus
ms.service: mysql
ms.topic: conceptual
ms.date: 8/6/2021
ms.openlocfilehash: 961e1fbf4bc87355ce036fb9a5af7a1cd85c4e76
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131472711"
---
# <a name="private-network-access-for-azure-database-for-mysql---flexible-server"></a>Zugriff auf private Netzwerke für Azure Database for MySQL Flexible Server

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

In diesem Artikel wird die private Konnektivitätsoption für flexible Azure MySQL-Server beschrieben. Außerdem finden Sie hier ausführliche Informationen zu den VNet-Konzepten für flexible Azure Database for MySQL-Server, die für die sichere Erstellung eines Servers in Azure erforderlich sind.


## <a name="private-access-vnet-integration"></a>Privater Zugriff (VNET-Integration)

[Azure Virtual Network (VNET)](../../virtual-network/virtual-networks-overview.md) ist der grundlegende Baustein für Ihr privates Netzwerk in Azure. Die VNet-Integration (virtuelles Netzwerk) mit einem flexiblen Azure Database for MySQL-Server bringt Azure-Vorteile wie Netzwerksicherheit und Isolation mit sich.

Durch die VNet-Integration für einen flexiblen Azure Database for MySQL-Server können Sie den Zugriff auf den Server allein auf Ihre virtuelle Netzwerkinfrastruktur beschränken. Ihr virtuelles Netzwerk kann alle Ihre Anwendungs- und Datenbankressourcen in einem einzelnen virtuellen Netzwerk enthalten oder sich über verschiedene virtuelle Netzwerke in der gleichen Region oder in unterschiedlichen Regionen erstrecken. Nahtlose Konnektivität zwischen verschiedenen virtuellen Netzwerken kann mittels [Peering](../../virtual-network/virtual-network-peering-overview.md) erreicht werden. Dabei wird die private Backbone-Infrastruktur von Microsoft verwendet, die sich durch geringe Wartezeit und hohe Bandbreite auszeichnet. Die virtuellen Netzwerke werden für Verbindungszwecke als einzelnes Element angezeigt.

Flexible Azure Database for MySQL-Server unterstützen Clientkonnektivität von Folgendem:

* Virtuelle Netzwerke innerhalb der gleichen Azure-Region (VNets mit lokalem Peering)
* Virtuelle Netzwerke in verschiedenen Azure-Regionen (VNets mit globalem Peering)

Mithilfe von Subnetzen können Sie das virtuelle Netzwerk in eines oder mehrere Subnetze segmentieren und einen Teil des VNet-Adressraums zuordnen, in dem Sie dann Azure-Ressourcen bereitstellen können. Für flexible Azure Database for MySQL-Server ist ein [delegiertes Subnetz](../../virtual-network/subnet-delegation-overview.md) erforderlich. In einem delegierten Subnetz können nur flexible Azure Database for MySQL-Server gehostet werden. Durch die Delegierung des Subnetzes erhält der Dienst explizite Berechtigungen zum Erstellen dienstspezifischer Ressourcen im Subnetz, was die nahtlose Verwaltung Ihres flexiblen Azure Database for MySQL-Servers ermöglicht.

Der flexible Azure Database for MySQL-Server wird in [private DNS-Zonen](../../dns/private-dns-privatednszone.md) von Azure integriert, um einen zuverlässigen, sicheren DNS-Dienst zum Verwalten und Auflösen von Domänennamen in einem virtuellen Netzwerk zu bieten, ohne dass Sie eine benutzerdefinierte DNS-Lösung hinzufügen müssen. Die private DNS-Zone kann durch Erstellen [virtueller Netzwerkverbindungen](../../dns/private-dns-virtual-network-links.md) mit einem oder mehreren virtuellen Netzwerken verknüpft werden.


:::image type="content" source="./media/concepts-networking/vnet-diagram.png" alt-text="Flexibler Server: MySQL-VNet":::

Für dieses Diagramm gilt:

1. Flexible Server werden in ein delegiertes Subnetz eingefügt: 10.0.1.0/24 des VNet **VNet-1**.
2. Anwendungen, die in verschiedenen Subnetzen innerhalb desselben VNet bereitgestellt werden, können direkt auf die flexiblen Server zugreifen.
3. Anwendungen, die in einem anderen VNet (**VNet-2**) bereitgestellt werden, haben keinen direkten Zugriff auf flexible Server. Sie können erst nach einem [VNet-Peering mit einer privaten DNS-Zone](#private-dns-zone-and-vnet-peering) auf die flexiblen Server zugreifen.

## <a name="virtual-network-concepts"></a>Virtuelle Netzwerke: Konzepte

Nachstehend werden einige Konzepte erläutert, die Sie kennen sollten, wenn Sie virtuelle Netzwerke mit MySQL Flexible Server-Instanzen verwenden.

* **Virtuelles Netzwerk** -

   Ein virtuelles Azure-Netzwerk (Virtual Network, VNet) enthält einen privaten IP-Adressraum, der für Ihre Verwendung konfiguriert ist. Weitere Informationen zu virtuellen Azure-Netzwerken finden Sie unter [Überblick über Azure Virtual Network](../../virtual-network/virtual-networks-overview.md).

    Ihr virtuelles Netzwerk muss in derselben Azure-Region wie Ihr flexibler Server bereitgestellt werden.

* **Delegiertes Subnetz** -

   Ein virtuelles Netzwerk enthält Subnetze (untergeordnete Netzwerke). Subnetze bieten Ihnen die Möglichkeit, Ihr virtuelles Netzwerk in kleinere Adressräume einzuteilen. Azure-Ressourcen werden in bestimmten Subnetzen innerhalb eines virtuellen Netzwerks bereitgestellt.

   Ihre MySQL Flexible Server-Instanz muss sich in einem Subnetz befinden, das eigens für MySQL Flexible Server-Instanzen eingeteilt (**delegiert**) wurde. Diese Delegierung bedeutet, dass dieses Subnetz nur von Azure Database for MySQL Flexible Server-Instanzen genutzt werden kann. Im delegierten Subnetz können sich keine anderen Azure-Ressourcentypen befinden. Sie können ein Subnetz delegieren, indem Sie „Microsoft.DBforMySQL/flexibleServers“ als Delegierungseigenschaft festlegen.

* **Netzwerksicherheitsgruppen (NSGs)**

   Mit Sicherheitsregeln in Netzwerksicherheitsgruppen können Sie den Typ des ein- und ausgehenden Netzwerkdatenverkehrs von Subnetzen virtueller Netzwerke und Netzwerkschnittstellen filtern. Weitere Informationen finden Sie unter [Netzwerksicherheitsgruppen](../../virtual-network/network-security-groups-overview.md).

* **Integration in private DNS-Zonen** -

   Mit der Integration in private Azure-DNS-Zonen können Sie das private DNS innerhalb des aktuellen VNet oder eines beliebigen Peer-VNet in der gleichen Region auflösen, in dem die private DNS-Zone verknüpft ist.

* **Peering von virtuellen Netzwerken**

   Durch das Peering virtueller Netzwerke können mehrere virtuelle Netzwerke nahtlos mit Azure verbunden werden. Die peered virtuellen Netzwerke werden für Verbindungszwecke als einzelnes Element angezeigt. Der Datenverkehr zwischen virtuellen Computern in virtuellen Netzwerken mit Peering erfolgt über die Microsoft-Backboneinfrastruktur. Der Datenverkehr zwischen der Clientanwendung und dem flexiblem Server mittels peered VNets wird nur über das private Netzwerk von Microsoft weitergeleitet und ist nur in diesem Netzwerk isoliert.

## <a name="using-private-dns-zone"></a>Verwenden privater DNS-Zonen

* Wenn Sie das Azure-Portal oder die Azure CLI verwenden, um flexible Server mit VNet zu erstellen, wird pro Server in Ihrem Abonnement automatisch eine neue private DNS-Zone unter Verwendung des angegebenen Servernamens bereitgestellt. Alternativ können Sie auch eine eigene private DNS-Zone für die Verwendung mit dem flexiblen Server einrichten. Lesen Sie dazu die Dokumentation [Was ist privates Azure-DNS?](../../dns/private-dns-overview.md).
* Wenn Sie die Azure-API, eine ARM-Vorlage (Azure Resource Manager) oder Terraform verwenden, erstellen Sie private DNS-Zonen, die auf `mysql.database.azure.com` enden, und verwenden Sie sie beim Konfigurieren von flexiblen Servern mit privatem Zugriff. Weitere Informationen finden Sie unter [Was ist privates Azure-DNS?](../../dns/private-dns-overview.md).

   > [!IMPORTANT]
   > Namen privater DNS-Zonen müssen auf `mysql.database.azure.com` enden.

Unter den folgenden Links erfahren Sie, wie Sie einen flexiblen Server mit privatem Zugriff (VNET-Integration) im [Azure-Portal](how-to-manage-virtual-network-portal.md) oder über die [Azure CLI](how-to-manage-virtual-network-cli.md) erstellen.

## <a name="integration-with-custom-dns-server"></a>Integration mit einem benutzerdefinierten DNS-Server

Wenn Sie den benutzerdefinierten DNS-Server verwenden, müssen Sie eine DNS-Weiterleitung verwenden, um den FQDN von Azure Database for MySQL: Flexible Server aufzulösen. Die IP-Adresse der Weiterleitung sollte [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md) sein. Der benutzerdefinierte DNS-Server sollte sich innerhalb des VNet befinden oder über die DNS-Servereinstellung des VNet erreichbar sein. Weitere Informationen finden Sie unter [Namensauflösung mithilfe eines eigenen DNS-Servers](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

## <a name="private-dns-zone-and-vnet-peering"></a>Private DNS-Zone und VNet-Peering

Die Einstellungen für private DNS-Zonen und VNet-Peering sind voneinander unabhängig. Weitere Informationen zum Erstellen und Verwenden privater DNS-Zonen finden Sie weiter oben im Abschnitt [Verwenden privater DNS-Zonen](concepts-networking-vnet.md#using-private-dns-zone).

Wenn Sie von einem Client aus, der in einem anderen VNet in der gleichen oder in einer anderen Region bereitgestellt wurde, eine Verbindung mit dem flexiblen Server herstellen möchten, müssen Sie die private DNS-Zone mit dem VNet verknüpfen. Weitere Informationen finden Sie in der [Dokumentation zum Verknüpfen des virtuellen Netzwerks](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network).

> [!NOTE]
> Private DNS-Zonennamen, die mit `mysql.database.azure.com` enden, können nur verknüpft werden.

## <a name="connecting-from-on-premises-to-flexible-server-in-virtual-network-using-expressroute-or-vpn"></a>Herstellen einer Verbindung zwischen einem lokalen Standort und einem flexiblen Server in Virtual Network mithilfe von ExpressRoute oder VPN

Für Workloads, die Zugriff auf einen flexiblen Server in einem virtuellen Netzwerk über das lokale Netzwerk benötigen, benötigen Sie [ExpressRoute](/azure/architecture/reference-architectures/hybrid-networking/expressroute/) oder [VPN](/azure/architecture/reference-architectures/hybrid-networking/vpn/) und ein virtuelles Netzwerk[, das mit dem lokalen Standort ](/azure/architecture/reference-architectures/hybrid-networking/) verbunden ist. Wenn Sie diese Einrichtung durchgeführt haben, benötigen Sie eine DNS-Weiterleitung, um den flexiblen Servernamen aufzulösen, wenn Sie eine Verbindung von einer Clientanwendung (z. B. MySQL Workbench) herstellen möchten, die im lokalen virtuellen Netzwerk ausgeführt wird. Diese DNS-Weiterleitung ist dafür zuständig, alle DNS-Abfragen über eine Weiterleitung auf Serverebene zum von Azure bereitgestellten DNS-Dienst [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md) aufzulösen.

Für eine korrekte Konfiguration benötigen Sie die folgenden Ressourcen:

* Lokales Netzwerk
* Flexibler MySQL-Server mit privatem Zugriff (VNet-Integration)
* Virtuelles Netzwerk [mit Verbindung mit lokalem Standort](/azure/architecture/reference-architectures/hybrid-networking/)
* Nutzen Sie die in Azure bereitgestellte DNS-Weiterleitung [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md)

Anschließend können Sie den flexiblen Servernamen (FQDN) verwenden, um eine Verbindung zwischen der Clientanwendung in einem mit einem peered virtuellen Netzwerk oder einem lokalen Netzwerk und einem flexiblen Server herzustellen.

## <a name="unsupported-virtual-network-scenarios"></a>Nicht unterstützte virtuelle Netzwerkszenarios

* Öffentlicher Endpunkt (oder öffentliche IP-Adresse oder DNS): Ein flexibler Server, der in einem virtuellen Netzwerk bereitgestellt wird, kann keinen öffentlichen Endpunkt haben.
* Nachdem der flexible Server in einem virtuellen Netzwerk und Subnetz bereitgestellt wurde, können Sie ihn in kein anderes virtuelles Netzwerk oder Subnetz verschieben. Auch das virtuelle Netzwerk kann in keine andere Ressourcengruppe oder kein anderes Abonnement verschoben werden.
* Die Subnetzgröße (Adressräume) kann nicht erhöht werden, sobald Ressourcen im Subnetz vorhanden sind.
* Private Link wird von flexiblen Servern nicht unterstützt. Stattdessen wird die VNet-Einschleusung verwendet, um flexible Server innerhalb eines VNet verfügbar zu machen.

> [!NOTE]
> Wenn Sie den benutzerdefinierten DNS-Server verwenden, müssen Sie eine DNS-Weiterleitung verwenden, um den FQDN von Azure Database for MySQL: Flexible Server aufzulösen. Weitere Informationen finden Sie unter [Namensauflösung mithilfe eines eigenen DNS-Servers](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

## <a name="next-steps"></a>Nächste Schritte

* Unter den folgenden Links erfahren Sie, wie Sie den privaten Zugriff (VNET-Integration) über das [Azure-Portal](how-to-manage-virtual-network-portal.md) oder die [Azure CLI](how-to-manage-virtual-network-cli.md) aktivieren.
* Weitere Informationen zur [Verwendung der TLS](how-to-connect-tls-ssl.md)
