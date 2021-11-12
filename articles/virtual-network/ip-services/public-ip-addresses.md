---
title: Öffentliche IP-Adressen in Azure
titleSuffix: Azure Virtual Network
description: Erfahren Sie etwas über öffentliche IP-Adressen in Azure.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: conceptual
ms.date: 04/29/2021
ms.author: allensu
ms.openlocfilehash: a57c4ea8d058f0f565b6dbec7fdb74b07ea7564e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131471400"
---
# <a name="public-ip-addresses"></a>Öffentliche IP-Adressen

Öffentliche IP-Adressen ermöglichen Internetressourcen die eingehende Kommunikation mit Azure-Ressourcen. Öffentliche IP-Adressen ermöglichen Azure-Ressourcen, mit dem Internet und öffentlichen Azure-Diensten zu kommunizieren. Die Adresse ist für die Ressource reserviert, bis Sie die Zuweisung aufheben. Eine Ressource ohne zugewiesene öffentliche IP-Adresse kann ausgehend kommunizieren. Azure weist dynamisch eine verfügbare IP-Adresse zu, die nicht für die Ressource dediziert ist. Weitere Informationen zu ausgehenden Verbindungen in Azure finden Sie unter [Grundlegendes zu ausgehenden Verbindungen](../../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

In Azure-Ressourcen-Manager ist eine [öffentliche IP-Adresse](virtual-network-public-ip-address.md) eine Ressource, die über eigene Eigenschaften verfügt. Sie können eine öffentliche IP-Adressressource beispielsweise den folgenden Ressourcen zuordnen:

* Netzwerkschnittstellen für virtuelle Computer
* Load Balancer mit Internetzugriff
* Gateways für virtuelle Netzwerke (VPN/ER)
* NAT-Gateways
* Anwendungsgateways
* Azure Firewall
* Bastionhost

Für VM-Skalierungsgruppen verwenden Sie [öffentliche IP-Präfixe](public-ip-address-prefix.md).

## <a name="at-a-glance"></a>Auf einen Blick

Die folgende Tabelle zeigt die Eigenschaft einer öffentlichen IP-Adresse, die einer Ressource zugeordnet werden kann, und die Zuordnungsmethoden. Beachten Sie, dass die öffentliche IPv6-Unterstützung derzeit nicht für alle Ressourcentypen verfügbar ist.

| Ressource der obersten Ebene | Zuordnung der IP-Adresse | Dynamisches IPv4 | Statisches IPv4 | Dynamisches IPv6 | Statisches IPv6 |
| --- | --- | --- | --- | --- | --- |
| Virtueller Computer |Netzwerkschnittstelle |Ja | Ja | Ja | Ja |
| Lastenausgleich mit Internetzugriff |Front-End-Konfiguration |Ja | Ja | Ja |Ja |
| Gateway für virtuelle Netzwerke (VPN) |Gateway-IP-Konfiguration |Ja (nur Nicht-AZ) |Ja (nur AZ) | Nein |Nein |
| Gateway für virtuelle Netzwerke (ER) |Gateway-IP-Konfiguration |Ja | Nein | Ja (Vorschau) |Nein |
| NAT Gateway |Gateway-IP-Konfiguration |Nein |Ja | Nein |Nein |
| Anwendungsgateway |Front-End-Konfiguration |Ja (nur V1) |Ja (nur V2) | Nein | Nein |
| Azure Firewall | Front-End-Konfiguration | Nein | Ja | Nein | Nein |
| Bastionhost | Konfiguration der öffentlichen IP-Adresse | Nein | Ja | Nein | Nein |

## <a name="ip-address-version"></a>IP-Adressversion

Öffentliche IP-Adressen können mit einer IPv4- oder IPv6-Adresse erstellt werden. Möglicherweise haben Sie die Möglichkeit, eine Dual-Stack-Bereitstellung mit einer IPv4- und IPv6-Adresse zu erstellen.

## <a name="sku"></a>SKU

Öffentliche IP-Adressen werden mit einer der folgenden SKUs erstellt:

### <a name="standard"></a>Standard

Für öffentliche IP-Adressen mit Standard-SKU gilt Folgendes:

- Sie verwenden immer eine statische Zuordnungsmethode.
- Sie verfügen über ein anpassbares Leerlauftimeout für den ursprünglich eingehenden Datenfluss, das auf einen Wert zwischen vier und 30 Minuten (Standardwert: vier Minuten) festgelegt werden kann, sowie über ein vorgegebenes Leerlauftimeout für den ursprünglich ausgehenden Datenfluss von vier Minuten.
- Sie wurden in Übereinstimmung mit dem Modell „standardmäßig sicher“ konzipiert und sind bei Verwendung als Front-End für eingehenden Datenverkehr gesperrt.  Datenverkehr auf Datenebene mit einer [Netzwerksicherheitsgruppe](../../virtual-network/network-security-groups-overview.md#network-security-groups) (NSG) muss auf die Positivliste gesetzt werden (z. B. auf der NIC eines virtuellen Computers mit angefügter öffentlicher IP-Adresse der Standard-SKU).
- Sie können zonenredundant (von allen drei Zonen angekündigt), zonal (in einer bestimmten vorausgewählten Verfügbarkeitszone garantiert) oder zonenlos (keiner bestimmten vorausgewählten Verfügbarkeitszone zugeordnet) sein. Weitere Informationen zu Verfügbarkeitszonen finden Sie unter [Übersicht über Verfügbarkeitszonen in Azure](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) und [Azure Load Balancer Standard und Verfügbarkeitszonen](../../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json). **Zonenredundante IP-Adressen können nur in [Regionen mit drei aktiven Verfügbarkeitszonen](../../availability-zones/az-region.md) erstellt werden.** IP-Adressen, die vor der Aktivierung der Zonen erstellt wurden, sind nicht zonenredundant.
- Kann zusammen mit der [Routingpräferenz](routing-preference-overview.md) verwendet werden, um eine differenziertere Kontrolle darüber zu ermöglichen, wie der Datenverkehr zwischen Azure und dem Internet weitergeleitet wird.
- Können als Anycast-Front-End-IP-Adressen für [regionsübergreifende Load Balancer](../../load-balancer/cross-region-overview.md) verwendet werden (Vorschaufunktionalität).

> [!NOTE]
> Nur öffentliche IP-Adressen mit SKU-Typ „Basic“ stehen zur Verfügung, wenn der [Instance Metadata Service IMDS](../../virtual-machines/windows/instance-metadata-service.md) verwendet wird. Die SKU vom Typ „Standard“ wird nicht unterstützt.

> [!NOTE]
> Diagnoseeinstellungen werden nicht unter dem Blatt „Ressource“ angezeigt, wenn eine öffentliche IP-Adresse der Standard-SKU verwendet wird. Um die Protokollierung Ihrer öffentlichen Standard-IP-Adressenressource zu aktivieren, navigieren Sie unter dem Blatt „Azure Monitor“ zu „Diagnoseeinstellungen“, und wählen Sie Ihre IP-Adressenressource aus.

> [!NOTE]
> Die eingehende Kommunikation mit einer SKU-Standardressource ist erst erfolgreich, wenn Sie eine [Netzwerksicherheitsgruppe](../../virtual-network/network-security-groups-overview.md#network-security-groups) erstellen und zuordnen und den gewünschten eingehenden Datenverkehr explizit zulassen.

### <a name="basic"></a>Basic

Für Basic-SKU-Adressen gilt Folgendes:

- Für IPv4: Kann mithilfe der dynamischen oder statischen Zuordnungsmethode zugewiesen werden.  Für IPv6: Kann nur mithilfe der dynamischen Zuordnungsmethode zugewiesen werden.
- Sie verfügen über ein anpassbares Leerlauftimeout für den ursprünglich eingehenden Datenfluss, das auf einen Wert zwischen vier und 30 Minuten (Standardwert: vier Minuten) festgelegt werden kann, sowie über ein vorgegebenes Leerlauftimeout für den ursprünglich ausgehenden Datenfluss von vier Minuten.
- Sind standardmäßig geöffnet.  Netzwerksicherheitsgruppen werden empfohlen, sind aber zum Einschränken des ein- oder ausgehenden Datenverkehrs optional.
- Sie unterstützen keine Szenarien mit Verfügbarkeitszone. Verwenden Sie für Szenarien mit Verfügbarkeitszone eine öffentliche IP-Adresse mit Standard-SKU in den entsprechenden Regionen. Weitere Informationen zu Verfügbarkeitszonen finden Sie unter [Übersicht über Verfügbarkeitszonen in Azure](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) und [Azure Load Balancer Standard und Verfügbarkeitszonen](../../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Unterstützen Sie keine [Routingpräferenzen](routing-preference-overview.md) oder [regionsübergreifenden Lastausgleichsfunktionen](../../load-balancer/cross-region-overview.md).

> [!NOTE]
> Für IPv4-Adressen der Basic-SKU kann nach der Erstellung ein Upgrade auf die Standard-SKU durchgeführt werden.  Weitere Informationen zum SKU-Upgrade finden Sie unter [Ausführen eines Upgrades für öffentliche IP-Adressen](public-ip-upgrade-portal.md).

>[!IMPORTANT]
> Für Lastenausgleichs- und öffentliche IP-Adressressourcen müssen übereinstimmende SKUs verwendet werden. Eine Kombination aus Ressourcen der SKU-Typen „Basic“ und „Standard“ ist nicht möglich. Sie können eigenständige virtuelle Computer, virtuelle Computer in einer Ressource einer Verfügbarkeitsgruppe oder eine Ressource einer VM-Skalierungsgruppe an beide SKUs gleichzeitig anfügen.  Für neue Entwürfe sollte SKU-Standardressourcen verwendet werden.  Unter [Load Balancer Standard](../../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) finden Sie ausführliche Informationen.

## <a name="ip-address-assignment"></a>IP-Adresszuweisung

Öffentliche Standard-IPv4-Adressen, öffentliche Basic-IPv4-Adressen und öffentliche Standard-IPv6-Adressen unterstützen die **statische** Zuweisung.  Der Ressource wird bei der Erstellung eine IP-Adresse zugewiesen. Beim Löschen der Ressource wird die IP-Adresse wieder freigegeben.  

> [!NOTE]
> Auch wenn Sie die Zuordnungsmethode auf **statisch** festlegen, können Sie nicht die tatsächliche IP-Adresse angeben, die der öffentlichen IP-Adressressource zugewiesen ist. Azure weist die IP-Adresse aus einem Pool mit verfügbaren IP-Adressen an dem Azure-Standort zu, an dem die Ressource erstellt wird.
>

Statische öffentliche IP-Adressen werden häufig in den folgenden Szenarien verwendet:

* Sie müssen Firewallregeln für die Kommunikation mit Ihren Azure-Ressourcen aktualisieren.
* Bei der DNS-Namensauflösung erfordert eine Änderung einer IP-Adresse eine Aktualisierung von A-Datensätzen.
* Ihre Azure-Ressourcen kommunizieren mit anderen Apps oder Diensten, die ein auf IP-Adressen basierendes Sicherheitsmodell verwenden.
* Sie verwenden TLS/SSL-Zertifikate, die mit einer IP-Adresse verknüpft sind.

Öffentliche Basic-IPv4- und -IPv6-Adressen unterstützen eine **dynamische** Zuweisung.  Bei Auswahl von „Dynamisch“ wird die IP-Adresse **nicht** bei der Erstellung der Ressource zugewiesen.  Die IP-Adresse wird zugewiesen, wenn Sie der öffentlichen IP-Adresse eine Ressource zuordnen. Die IP-Adresse wird freigegeben, wenn Sie die Ressource beenden oder löschen.   Beispielsweise wird eine öffentliche IP-Adressressource von einer Ressource mit dem Namen **Ressource A** freigegeben. **Ressource A** erhält beim Starten eine andere IP-Adresse, wenn die öffentliche IP-Ressource neu zugewiesen wird. Jede zugeordnete IP-Adresse wird freigegeben, wenn die Zuordnungsmethode von **Statisch** in **Dynamisch** geändert wird. Jede zugeordnete IP-Adresse bleibt unverändert, wenn die Zuordnungsmethode von **Dynamisch** in **Statisch** geändert wird. Legen Sie die Zuordnungsmethode auf **Statisch** fest, damit die IP-Adresse unverändert bleibt.

> [!NOTE]
> Azure ordnet öffentliche IP-Adressen aus einem Bereich zu, der für jede Region in jeder Azure-Cloud eindeutig ist. Sie können die Liste von Bereichen (Präfixen) für die [öffentliche Azure-Cloud](https://www.microsoft.com/download/details.aspx?id=56519), die [Azure US Government-Cloud](https://www.microsoft.com/download/details.aspx?id=57063) sowie für die Azure-Cloud in [China](https://www.microsoft.com/download/details.aspx?id=57062) und [Deutschland](https://www.microsoft.com/download/details.aspx?id=57064) herunterladen.
>

## <a name="dns-name-label"></a>DNS-Namensbezeichnung

Wählen Sie die Option aus, um eine DNS-Bezeichnung für eine öffentliche IP-Ressource anzugeben. Diese Funktion funktioniert sowohl für IPv4-Adressen (32-Bit-A-Datensätze) als auch für IPv6-Adressen (128-Bit-AAAA-Datensätze).  Mit dieser Auswahl wird eine Zuordnung zwischen **domainnamelabel**.**location**.cloudapp.azure.com und der öffentlichen IP-Adresse im von Azure verwalteten DNS erstellt. 

Ein Beispiel ist eine öffentliche IP-Adresse mit:

* **contoso** als **domainnamelabel**
* **USA, Westen** als Azure-**Standort**

Der vollqualifizierte Domänenname (Fully Qualified Domain Name, FQDN) **contoso.westus.cloudapp.azure.com** wird in die öffentliche IP-Adresse der Ressource aufgelöst.

> [!IMPORTANT]
> Jede erstellte Domänennamensbezeichnung muss innerhalb des Azure-Standorts eindeutig sein.  

Wenn für Dienste, die eine öffentliche IP-Adresse nutzen, eine benutzerdefinierte Domäne gewünscht wird, können Sie [Azure DNS](../../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address) oder einen externen DNS-Anbieter für Ihren DNS-Eintrag verwenden.

## <a name="other-public-ip-address-features"></a>Weitere Features für öffentliche IP-Adressen

Es gibt andere Attribute, die für eine öffentliche IP-Adresse verwendet werden können.  

* Der **Tarif** „Global“ ermöglicht die Verwendung einer öffentlichen IP-Adresse mit regionsübergreifenden Lastenausgleichsmodulen. 
* Die Option **Internetroutingpräferenz** minimiert die Zeit, die der Datenverkehr im Microsoft-Netzwerk verbringt, und senkt so die Kosten für ausgehende Datenübertragungen.

> [!NOTE]
> Zurzeit sind sowohl das Feature **Tarif** als auch **Routingpräferenz** nur für Standard-SKU-IPv4-Adressen verfügbar.  Sie können auch nicht gleichzeitig für dieselbe IP-Adresse verwendet werden.
>

[!INCLUDE [ephemeral-ip-note.md](../../../includes/ephemeral-ip-note.md)]

## <a name="limits"></a>Einschränkungen 

Die Grenzwerte für die IP-Adressierung finden Sie in den vollständigen Informationen zu [Grenzwerten für Netzwerke](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) in Azure. Die Grenzwerte gelten pro Region und pro Abonnement. [Wenden Sie sich an den Support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade), um oben die Standardgrenzwerte je nach Ihren Unternehmensanforderungen zu erhöhen.

## <a name="pricing"></a>Preise

Für öffentliche IP-Adressen fällt eine Schutzgebühr an. Weitere Informationen zu den Preisen für IP-Adressen in Azure finden Sie auf der Seite [Preise für IP-Adressen](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="limitations-for-ipv6"></a>Einschränkungen für IPv6

* VPN-Gateways können in einem VNet mit aktiviertem IPv6 weder direkt noch durch Peering mit „UseRemoteGateway“ verwendet werden.
* Öffentliche IPv6-Adressen werden bei einem Leerlauftimeout von vier Minuten gesperrt.
* Azure unterstützt keine IPv6-Kommunikation für Container.
* Die Verwendung von ausschließlich IPv6-basierten virtuellen Computern oder VM-Skalierungsgruppen wird nicht unterstützt. Jede NIC muss mindestens eine IPv4-IP-Konfiguration enthalten (Dual-Stack).
* Beim Hinzufügen von IPv6 zu vorhandenen IPv4-Bereitstellungen können IPv6-Bereiche nicht zu einem virtuellen Netzwerk hinzugefügt werden, das über vorhandene Ressourcennavigationslinks verfügt.
* Forward DNS für IPv6 wird für öffentliches DNS von Azure unterstützt. Reverse DNS wird nicht unterstützt.
* Routingpräferenz und regionsübergreifender Lastenausgleich werden nicht unterstützt.

Weitere Informationen zu IPv6 in Azure finden Sie [hier](ipv6-overview.md).

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie etwas über [private IP-Adressen in Azure](private-ip-addresses.md).
* [Bereitstellen einer VM mit einer statischen öffentlichen IP-Adresse mithilfe des Azure-Portals](./virtual-network-deploy-static-pip-arm-portal.md)