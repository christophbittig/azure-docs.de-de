---
title: Präfix für öffentliche Azure-IP-Adressen
titleSuffix: Azure Virtual Network
description: Erfahren Sie, was ein Präfix für öffentliche Azure-IP-Adressen ist und wie Sie mit einem solchen Präfix Ihren Ressourcen öffentliche IP-Adressen zuweisen können.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: conceptual
ms.date: 05/20/2021
ms.author: allensu
ms.openlocfilehash: 4908810bc6311593adaa157a88355e182aeba7e8
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368230"
---
# <a name="public-ip-address-prefix"></a>Präfix für öffentliche IP-Adressen

Ein Präfix für öffentliche IP-Adressen ist ein reservierter Bereich von [öffentlichen IP-Adressen](public-ip-addresses.md#public-ip-addresses) in Azure. Präfixe für öffentliche IP-Adressen werden aus einem Pool mit Adressen in jeder Azure-Region zugewiesen. Sie erstellen ein Präfix für eine öffentliche IP-Adresse in einer Azure-Region und einem Abonnement, indem Sie einen Namen und eine Präfixgröße angeben. Die Präfixgröße entspricht der Anzahl der verfügbaren Adressen. Präfixe für öffentliche IP-Adressen bestehen aus IPv4- oder IPv6-Adressen.  In Regionen mit Verfügbarkeitszonen können Präfixe für öffentliche IP-Adressen zonenredundant erstellt oder einer bestimmten Verfügbarkeitszone zugeordnet werden.  Nachdem das Präfix für öffentliche IP-Adressen erstellt wurde, können Sie öffentliche IP-Adressen erstellen.

## <a name="benefits"></a>Vorteile

- Erstellung von statischen öffentlichen IP-Adressressourcen aus einem bekannten Bereich Adressen, die Sie mithilfe des Präfixes erstellen, können jeder Azure-Ressource zugewiesen werden, der Sie eine öffentliche IP-Adresse der SKU-Standard zuweisen können.
- Wenn Sie die einzelnen öffentlichen IP-Adressen löschen, werden sie *wieder* in den reservierten Bereich aufgenommen und können später wiederverwendet werden. Die IP-Adressen in Ihrem Präfix für öffentliche IP-Adressen sind für Sie reserviert, bis Sie Ihr Präfix löschen.
- Sie können sehen, welche IP-Adressen innerhalb des Präfixbereichs vergeben und verfügbar sind.

## <a name="prefix-sizes"></a>Präfixgrößen

Die folgenden Präfixgrößen für öffentliche IP-Adressen sind verfügbar:

-  /28 (IPv4) oder /124 (IPv6) = 16 Adressen
-  /29 (IPv4) oder /125 (IPv6) = 8 Adressen
-  /30 (IPv4) oder /126 (IPv6) = 4 Adressen
-  /31 (IPv4) oder /127 (IPv6) = 2 Adressen

Die Präfixgröße wird als CIDR-Maskengröße (Classless Inter-Domain Routing) angegeben.

Es gibt keine Einschränkungen hinsichtlich der Anzahl von Präfixen, die in einem Abonnement erstellt wurden. Die Anzahl der erstellten Bereiche kann nicht mehr statische öffentliche IP-Adressen umfassen, als in Ihrem Abonnement zulässig sind. Weitere Informationen finden Sie unter [Azure-Grenzwerte](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="scenarios"></a>Szenarien
Sie können die folgenden Ressourcen einer statischen öffentlichen IP-Adresse aus einem Präfix zuweisen:

|Resource|Szenario|Schritte|
|---|---|---|
|Virtuelle Computer| Indem Sie Ihren VMs in Azure öffentliche IP-Adressen aus einem Präfix zuweisen, reduzieren Sie den Verwaltungsaufwand beim Hinzufügen der Adressen zu einer Positivliste in der Firewall. Sie können ganz einfach ein ganzes Präfix mit einer einzigen Firewallregel aufnehmen. Wenn Sie Ihre virtuellen Computer in Azure skalieren, können Sie IP-Adressen aus dem gleichen Präfix zuweisen und so viel Zeit, Geld und Verwaltungsaufwand sparen.| So weisen Sie Ihrem virtuellen Computer IP-Adressen aus einem Präfix zu </br> 1. [Erstellen Sie ein Präfix](manage-public-ip-address-prefix.md). </br> 2. [Erstellen Sie eine IP-Adresse aus dem Präfix](manage-public-ip-address-prefix.md). </br> 3. [Weisen Sie die IP-Adresse der Netzwerkschnittstelle Ihres virtuellen Computers zu](../../virtual-network/virtual-network-network-interface-addresses.md#add-ip-addresses). </br> Sie können [die IPs auch einem Virtual Machine Scale Set zuordnen](https://azure.microsoft.com/resources/templates/vmms-with-public-ip-prefix/).
| Standardmäßige Lastenausgleichsmodule | Indem Sie Ihrer Front-End-IP-Konfiguration oder der Ausgangsregel eines Lastenausgleichsmoduls öffentliche IP-Adressen aus einem Präfix zuweisen, vereinfachen Sie die Verwaltung Ihres öffentlichen IP-Adressraums für Azure. Vereinfachen Sie das Szenario, indem Sie für ausgehende Verbindungen IP-Adressen aus einem Bereich zusammenhängender IP-Adressen verwenden. | So weisen Sie Ihrem Lastenausgleichsmodul IP-Adressen aus einem Präfix zu: </br> 1. [Erstellen Sie ein Präfix](manage-public-ip-address-prefix.md). </br> 2. [Erstellen Sie eine IP-Adresse aus dem Präfix](manage-public-ip-address-prefix.md). </br> 3. Wenn Sie das Lastenausgleichsmodul erstellen, wählen Sie die in Schritt 2 erstellte IP-Adresse als Front-End-IP-Adresse Ihres Lastenausgleichsmoduls aus, oder aktualisieren Sie die Adresse. |
| Azure Firewall | Sie können eine öffentliche IP-Adresse aus einem Präfix für ausgehende SNAT-Vorgänge verwenden. Der gesamte ausgehende VNET-Datenverkehr wird in die öffentliche IP-Adresse von [Azure Firewall](../../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) übersetzt. | So weisen Sie Ihrer Firewall eine IP-Adresse aus einem Präfix zu: </br> 1. [Erstellen Sie ein Präfix](manage-public-ip-address-prefix.md). </br> 2. [Erstellen Sie eine IP-Adresse aus dem Präfix](manage-public-ip-address-prefix.md). </br> 3. Wenn Sie [die Azure Firewall bereitstellen](../../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall), stellen Sie sicher, dass Sie die IP-Adresse auswählen, die Sie zuvor aus dem Präfix zugeordnet haben.|
| VPN-Gateway (AZ-SKU), Application Gateway v2, NAT Gateway | Sie können eine öffentliche IP-Adresse aus einem Präfix für Ihr Gateway verwenden. | So weisen Sie Ihrem Gateway eine IP-Adresse zu: </br> 1. [Erstellen Sie ein Präfix](manage-public-ip-address-prefix.md). </br> 2. [Erstellen Sie eine IP-Adresse aus dem Präfix](manage-public-ip-address-prefix.md). </br> 3. Wenn Sie das [VPN Gateway](../../vpn-gateway/tutorial-create-gateway-portal.md), [Application Gateway](../../application-gateway/quick-create-portal.md#create-an-application-gateway) oder [NAT Gateway](../nat-gateway/tutorial-create-nat-gateway-portal.md) bereitstellen, stellen Sie sicher, dass Sie die IP-Adresse, die Sie zuvor angegeben haben, aus dem Präfix auswählen.|

Darüber hinaus kann die Präfixressource für öffentliche IP-Adressen direkt von bestimmten Ressourcen verwendet werden:

Resource|Szenario|Schritte|
|---|---|---|
|VM-Skalierungsgruppen | Sie können ein Präfix für öffentliche IP-Adressen verwenden, um IP-Adressen auf Instanzebene in einer VM-Skalierungsgruppe zu generieren, obwohl keine einzelnen öffentlichen IP-Ressourcen erstellt werden. | Verwenden Sie eine [Vorlage](https://azure.microsoft.com/resources/templates/vmms-with-public-ip-prefix/) mit Anweisungen zur Verwendung dieses Präfixes für die öffentliche IP-Konfiguration im Rahmen der Erstellung der Skalierungsgruppe. |
| Standardmäßige Lastenausgleichsmodule | Ein Präfix für öffentliche IP-Adressen kann zur Skalierung eines Lastenausgleichs verwendet werden, indem [alle IP-Adressen in diesem Bereich für ausgehende Verbindungen verwendet werden](../../load-balancer/outbound-rules.md#scale). | So weisen Sie Ihrem Lastenausgleich ein Präfix zu </br> 1. [Erstellen Sie ein Präfix](manage-public-ip-address-prefix.md). </br> 2. Wählen Sie beim Erstellen des Lastenausgleichs das Präfix für IP-Adressen aus, das dem Front-End Ihres Lastenausgleichs zugeordnet ist. |
| NAT Gateway | Ein Präfix für öffentliche IP-Adressen kann zur Skalierung eines NAT Gateway verwendet werden, indem die öffentlichen IP-Adressen im Präfix für ausgehende Verbindungen verwendet werden. | So ordnen Sie Ihrem NAT Gateway ein Präfix zu </br> 1. [Erstellen Sie ein Präfix](manage-public-ip-address-prefix.md). </br> 2. Wählen Sie beim Erstellen des NAT Gateway das Präfix für IP-Adressen als ausgehende IP-Adresse aus. |
| VPN Gateway (Azure-SKU) oder Application Gateway v2 | Sie können eine öffentliche IP-Adresse aus einem Präfix für Ihr zonenredundantes VPN oder Ihre Application Gateway v2-Instanz verwenden. | So weisen Sie Ihrem Gateway eine IP-Adresse zu: </br> 1. [Erstellen Sie ein Präfix](manage-public-ip-address-prefix.md). </br> 2. [Erstellen Sie eine IP-Adresse aus dem Präfix](manage-public-ip-address-prefix.md). </br> 3. Wenn Sie das [VPN Gateway](../../vpn-gateway/tutorial-create-gateway-portal.md) oder [Application Gateway](../../application-gateway/quick-create-portal.md#create-an-application-gateway) einsetzen, stellen Sie sicher, dass Sie die IP, die Sie zuvor angegeben haben, aus dem Präfix auswählen.|

## <a name="limitations"></a>Einschränkungen

- Sie können die IP-Adressen für das Präfix nicht angeben. Azure ordnet die IP-Adressen für das Präfix basierend auf der von Ihnen angegebenen Größe zu.  Zusätzlich müssen sich alle aus dem Präfix erstellten öffentlichen IP-Adressen in der gleichen Azure-Region und im gleichen Azure-Abonnement wie das Präfix befinden. Die Adressen müssen Ressourcen in der gleichen Region und im gleichen Abonnement zugewiesen werden.
- Sie können ein Präfix von bis zu 16 IP-Adressen erstellen. Weitere Informationen finden Sie unter den [Anforderungen zum Erhöhen der Netzwerkgrenzwerte](../../azure-portal/supportability/networking-quota-requests.md) und den [Einschränkungen bei Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Die Größe des Bereichs kann nach dem Erstellen des Präfixes nicht mehr geändert werden.
- Nur statische öffentliche IP-Adressen, die mit der Standard-SKU erstellt wurden, können aus dem Bereich des Präfix zugewiesen werden. Weitere Informationen zu SKUs für öffentliche IP-Adressen finden Sie unter [Öffentliche IP-Adressen](public-ip-addresses.md#public-ip-addresses).
- Adressen aus dem Bereich können nur Azure Resource Manager-Ressourcen zugewiesen werden. Adressen können keinen Ressourcen im klassischen Bereitstellungsmodell zugewiesen werden.
- Sie können ein Präfix nicht löschen, wenn darin befindliche IP-Adressen öffentlichen IP-Adressressourcen zugewiesen sind, die einer Ressource zugeordnet sind. Heben Sie zuerst die Zuordnung aller öffentlichen IP-Adressressourcen auf, denen IP-Adressen aus dem Präfix zugewiesen sind. Weitere Informationen zum Aufheben der Zuordnung von öffentlichen IP-Adressen finden Sie unter [Verwalten öffentlicher IP-Adressen](virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address).
- Die Präfixe für öffentliche IP-Adressen sind derzeit nicht mit der *Internet***routingpräferenz** oder dem **Tarif** *Global* (für den regionsübergreifenden Lastenausgleich) kompatibel.
- IPv6 wird nur für einfache öffentliche IP-Adressen mit **dynamischer** Zuordnung unterstützt. Dynamische Zuweisung bedeutet, dass sich die IPv6-Adresse ändert, wenn Sie Ihre Ressource in Azure löschen und erneut bereitstellen. 
- Öffentliche IPv6-IP-Standardadressen unterstützen die statische (reservierte) Zuordnung. 
- Interne Standardlastenausgleichsmodule unterstützen die dynamische Zuordnung innerhalb des Subnetzes, dem sie zugewiesen sind.  

## <a name="pricing"></a>Preise
 
Informationen zu den Kosten im Zusammenhang mit der Nutzung von öffentlichen Azure-IP-Adressen – sowohl einzelnen IP-Adressen als auch IP-Bereichen – finden Sie unter [Preise für öffentliche IP-Adressen](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen](manage-public-ip-address-prefix.md) des Präfix für öffentliche IP-Adressen
