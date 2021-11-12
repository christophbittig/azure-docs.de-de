---
title: Virtuelle Netzwerke und virtuelle Computer in Azure
description: Hier erfahren Sie mehr über Netzwerke in Bezug auf virtuelle Computer in Azure.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.topic: conceptual
ms.date: 08/17/2021
ms.author: allensu
ms.openlocfilehash: d8ca819e7a63c7a0c4d507709c8495b604d19002
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130257668"
---
# <a name="virtual-networks-and-virtual-machines-in-azure"></a>Virtuelle Netzwerke und virtuelle Computer in Azure

Beim Erstellen eines virtuellen Computers (VM) erstellen Sie ein [virtuelles Netzwerk](../virtual-network/virtual-networks-overview.md) oder verwenden ein bereits vorhandenes. Entscheiden Sie, wie auf Ihre virtuellen Computer im virtuellen Netzwerk zugegriffen werden soll. Es ist wichtig, [vor dem Erstellen von Ressourcen einen Plan aufzustellen](../virtual-network/virtual-network-vnet-plan-design-arm.md) und sicherzustellen, dass Sie die [Einschränkungen von Netzwerkressourcen](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) verstehen.

In der folgenden Abbildung werden virtuelle Computer als Webserver und Anwendungsserver dargestellt. Jede Gruppe von virtuellen Computern wird separaten Subnetzen des virtuellen Netzwerks zugewiesen.

:::image type="content" source="./media/network-overview/load-balancer.png" alt-text="Diagramm einer Anwendung mit mehreren Ebenen und mehreren Subnetzen" border="true":::

Sie können das virtuelle Netzwerk entweder vor oder während der Erstellung eines virtuellen Computer erstellen. 

Sie erstellen diese Ressourcen, um die Kommunikation mit einer VM zu unterstützen:

- Netzwerkschnittstellen
- IP-Adressen
- Virtuelles Netzwerk und Subnetze

Berücksichtigen Sie darüber hinaus die folgenden optionalen Ressourcen:

- Netzwerksicherheitsgruppen
- Load Balancer

## <a name="network-interfaces"></a>Netzwerkschnittstellen

Eine [Netzwerkschnittstelle (NIC)](../virtual-network/virtual-network-network-interface.md) ist die Verbindung zwischen einem virtuellen Computer und einem virtuellen Netzwerk. Ein virtueller Computer muss mindestens über eine NIC verfügen. Eine VM kann je nach Größe auch über mehrere Netzwerkschnittstellen verfügen. Informationen zur Anzahl der NICs, die von den jeweiligen VM-Größen unterstützt werden, finden Sie unter [VM-Größen](../virtual-machines/sizes.md).

Sie können eine VM mit mehreren Netzwerkkarten erstellen und über den Lebenszyklus einer VM hinweg Netzwerkkarten hinzufügen oder entfernen. Wenn eine VM über mehrere NICs verfügt, kann sie Verbindungen mit verschiedenen Subnetzen herstellen.

Jede NIC, die an eine VM angefügt ist, muss sich an demselben Standort und unter demselben Abonnement wie die VM befinden. Jede NIC muss mit einem VNET verbunden werden, das in derselben Azure-Region und unter demselben Abonnement wie die NIC vorhanden ist. Das Subnetz, mit dem die VM verbunden ist, kann nach der Erstellung geändert werden. Sie können das virtuelle Netzwerk nicht ändern. Jeder NIC, die an eine VM angefügt ist, wird eine MAC-Adresse zugewiesen, die unverändert bleibt, bis die VM gelöscht wird.

In dieser Tabelle sind die Methoden aufgeführt, die Sie zum Erstellen einer Netzwerkschnittstelle verwenden können.

| Methode | BESCHREIBUNG |
| ------ | ----------- |
| [Azure portal](https://portal.azure.com) | Beim Erstellen einer VM im Azure-Portal wird automatisch eine Netzwerkschnittstelle für Sie erstellt. Das Portal erstellt eine VM mit nur einer NIC. Falls Sie eine VM mit mehr als einer NIC erstellen möchten, müssen Sie eine andere Methode verwenden. |
| [Azure PowerShell](../virtual-machines/windows/multiple-nics.md) | Verwenden Sie [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) mit dem Parameter **`-PublicIpAddressId`** , um den Bezeichner der zuvor erstellten öffentlichen IP-Adresse anzugeben. |
| [Azure-Befehlszeilenschnittstelle](../virtual-machines/linux/multiple-nics.md) | Verwenden Sie zum Angeben des Bezeichners der zuvor erstellten öffentlichen IP-Adresse [az network nic create](/cli/azure/network/nic) mit dem Parameter **`--public-ip-address`** . |
| [Vorlage](../virtual-network/template-samples.md) | Weitere Informationen zum Bereitstellen einer Netzwerkschnittstelle mit einer Vorlage finden Sie unter [Netzwerkschnittstelle in einem virtuellen Netzwerk mit öffentlicher IP-Adresse](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/nic-publicip-dns-vnet). |

## <a name="ip-addresses"></a>IP-Adressen

Sie können diese Arten von [IP-Adressen](./ip-services/public-ip-addresses.md) einer Netzwerkschnittstelle in Azure zuweisen:

- **Öffentliche IP-Adressen**: werden für die eingehende und ausgehende Kommunikation (ohne Netzwerkadressübersetzung) mit dem Internet und anderen Azure-Ressourcen verwendet, die nicht mit einem virtuellen Netzwerk verbunden sind. Das Zuweisen einer öffentlichen IP-Adresse zu einer NIC ist optional. Für öffentliche IP-Adressen wird eine geringe Gebühr berechnet, und es kann nur eine bestimmte Anzahl dieser IP-Adressen pro Abonnement verwendet werden.
- **Private IP-Adressen**: werden für die Kommunikation in einem virtuellen Netzwerk, Ihrem lokalen Netzwerk und dem Internet (mit Netzwerkadressübersetzung) verwendet. Einem virtuellen Computer muss mindestens eine private IP-Adresse zugewiesen werden. Weitere Informationen zu NAT in Azure finden Sie unter [Grundlegendes zu ausgehenden Verbindungen in Azure](../load-balancer/load-balancer-outbound-connections.md).

Den folgenden Komponenten können Sie öffentliche IP-Adressen zuweisen:

* Virtuelle Computer
* Öffentliche Lastenausgleichsmodule

Den folgenden Komponenten können Sie private IP-Adressen zuweisen:

* Virtuelle Computer
* Interner Lastenausgleich

Die Zuweisung von IP-Adressen zu einer VM erfolgt über eine Netzwerkschnittstelle.

Es gibt zwei Methoden für das Zuweisen einer IP-Adresse zu einer Ressource: dynamisch oder statisch. Die von Azure bereitgestellte Standardmethode für das Zuweisen von IP-Adressen ist dynamisch. Eine IP-Adresse wird nicht während ihrer Erstellung zugewiesen. Die IP-Adresse wird erst zugewiesen, wenn Sie eine VM erstellen oder eine beendete VM starten. Die IP-Adresse wird freigegeben, wenn Sie die VM beenden oder löschen.

Damit die IP-Adresse für die VM unverändert bleibt, können Sie die Zuordnungsmethode explizit auf „Statisch“ festlegen. In diesem Fall wird sofort eine IP-Adresse zugewiesen. Sie wird nur freigegeben, wenn Sie die VM löschen oder deren Zuordnungsmethode in „Dynamisch“ ändern.

In dieser Tabelle sind die Methoden aufgeführt, die Sie zum Erstellen einer IP-Adresse verwenden können.

| Methode | BESCHREIBUNG |
| ------ | ----------- |
| [Azure portal](./ip-services/virtual-network-deploy-static-pip-arm-portal.md) | Öffentliche IP-Adressen sind standardmäßig dynamisch. Die IP-Adresse kann sich ändern, wenn die VM beendet oder gelöscht wird. Erstellen Sie eine statische öffentliche IP-Adresse, um sicherzustellen, dass für die VM immer die gleiche öffentliche IP-Adresse verwendet wird. Standardmäßig wird einer NIC beim Erstellen einer VM vom Portal eine dynamische private IP-Adresse zugewiesen. Sie können diese IP-Adresse in „Statisch“ ändern, nachdem die VM erstellt wurde.|
| [Azure PowerShell](./ip-services/virtual-network-deploy-static-pip-arm-ps.md) | Sie verwenden [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) mit dem Parameter **`-AllocationMethod`** mit dem Wert „Dynamic“ oder „Static“. |
| [Azure-Befehlszeilenschnittstelle](./ip-services/virtual-network-deploy-static-pip-arm-cli.md) | Sie verwenden [az network public-ip create](/cli/azure/network/public-ip) mit dem Parameter **`--allocation-method`** mit dem Wert „Dynamic“ oder „Static“. |
| [Vorlage](../virtual-network/template-samples.md) | Weitere Informationen zum Bereitstellen einer öffentlichen IP-Adresse mit einer Vorlage finden Sie unter [Netzwerkschnittstelle in einem virtuellen Netzwerk mit öffentlicher IP-Adresse](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/nic-publicip-dns-vnet). |

Nach dem Erstellen einer öffentlichen IP-Adresse können Sie diese einer VM zuordnen, indem Sie sie einer NIC zuweisen.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="virtual-network-and-subnets"></a>Virtuelles Netzwerk und Subnetze

Ein Subnetz ist ein Bereich von IP-Adressen im virtuellen Netzwerk. Sie können ein virtuelles Netzwerk aus Organisations- und Sicherheitsgründen in mehrere Subnetze unterteilen. Jede NIC einer VM ist mit einem Subnetz in einem virtuellen Netzwerk verbunden. NICs, die mit (gleichen oder anderen) Subnetzen in einem virtuellen Netzwerk verbunden sind, können ohne zusätzliche Konfiguration miteinander kommunizieren.

Beim Einrichten eines virtuellen Netzwerks geben Sie die Topologie an, einschließlich der verfügbaren Adressräume und Subnetze. Wählen Sie Adressbereiche aus, die sich nicht überlappen, wenn das virtuelle Netzwerk mit anderen virtuellen oder lokalen Netzwerken verbunden werden soll. Die IP-Adressen sind privat und können nicht über das Internet aufgerufen werden. Azure behandelt jeden Adressbereich als Teil des privaten IP-Adressraums des virtuellen Netzwerks. Der Adressbereich ist nur innerhalb des virtuellen Netzwerks, innerhalb miteinander verbundener virtueller Netzwerke und von Ihrem lokalen Standort aus erreichbar.

Wenn Sie in einer Organisation arbeiten, in der eine andere Person als Sie für die internen Netzwerke zuständig ist, müssen Sie sich mit dieser Person in Verbindung setzen, bevor Sie den Adressraum auswählen. Stellen Sie sicher, dass es keine Überlappung der Adressräume gibt. Informieren Sie die Person über den gewünschten Adressraum, damit dieser Bereich von IP-Adressen nur einmal verwendet wird.

Standardmäßig gibt es zwischen Subnetzen keine Sicherheitsgrenzen. Virtuelle Computer in jedem dieser Subnetze können miteinander kommunizieren. Wenn für Ihre Bereitstellung Sicherheitsgruppen erforderlich sind, nutzen Sie **Netzwerksicherheitsgruppen (NSGs)** , um den Datenverkehrsfluss für Subnetze und VMs in eingehender und ausgehender Richtung zu steuern.

In dieser Tabelle sind die Methoden aufgeführt, die Sie zum Erstellen eines virtuellen Netzwerks und von Subnetzen verwenden können.

| Methode | BESCHREIBUNG |
| ------ | ----------- |
| [Azure portal](../virtual-network/quick-create-portal.md) | Wenn Sie bei der Erstellung einer VM zulassen, dass von Azure ein virtuelles Netzwerk erstellt wird, ist der Name eine Kombination aus dem Namen der Ressourcengruppe, die das virtuelle Netzwerk enthält, und dem Zusatz **`-vnet`** . Der Adressraum ist 10.0.0.0/24, der erforderliche Subnetzname lautet **default**, und der Adressbereich des Subnetzes ist 10.0.0.0/24. |
| [Azure PowerShell](../virtual-network/quick-create-powershell.md) | Sie verwenden [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworkSubnetConfig) und [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork), um ein Subnetz und ein virtuelles Netzwerk zu erstellen. Sie können auch [Add-AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Add-AzVirtualNetworkSubnetConfig) verwenden, um ein Subnetz in einem vorhandenen virtuellen Netzwerk hinzuzufügen. |
| [Azure-Befehlszeilenschnittstelle](../virtual-network/quick-create-cli.md) | Das Subnetz und das virtuelle Netzwerk werden gleichzeitig erstellt. Geben Sie für [azure network vnet create](/cli/azure/network/vnet) den Parameter **`--subnet-name`** mit dem Subnetznamen an. |
| [Vorlage](../virtual-network/template-samples.md) | Weitere Informationen über die Verwendung einer Vorlage zum Erstellen eines virtuellen Netzwerks mit Subnetzen finden Sie unter [Virtuelles Netzwerk mit zwei Subnetzen](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/vnet-two-subnets). |

## <a name="network-security-groups"></a>Netzwerksicherheitsgruppen

Eine [Netzwerksicherheitsgruppe (NSG)](../virtual-network/virtual-network-vnet-plan-design-arm.md) enthält eine Liste mit ACL-Regeln (Access Control List, Zugriffssteuerungsliste) zum Zulassen oder Verweigern von Netzwerkdatenverkehr an Subnetze, NICs oder beides. NSGs können entweder Subnetzen oder einzelnen NICs zugeordnet werden, die mit einem Subnetz verbunden sind. Wenn eine NSG einem Subnetz zugeordnet ist, gelten die ACL-Regeln für alle VMs in diesem Subnetz. Der Datenverkehr zu einer einzelnen NIC kann beschränkt werden, indem eine NSG direkt einer NIC zugewiesen wird.

NGSs enthalten zwei Regelsätze: für eingehenden und für ausgehenden Datenverkehr. Die Priorität für eine Regel muss innerhalb jedes Satzes eindeutig sein. 

Jede Regel verfügt über die folgenden Eigenschaften:

* Protocol
* Quell- und Zielportbereiche
* Adresspräfixe
* Richtung des Datenverkehrs
* Priorität
* Zugriffstyp

Alle NSGs enthalten eine Gruppe von Standardregeln. Die Standardregeln können nicht gelöscht werden. Ihnen wird die niedrigste Priorität zugewiesen, und sie können nicht durch von Ihnen erstellte Regeln überschrieben werden.

Wenn Sie eine NSG zu einer Netzwerkkarte zuordnen, werden die Netzwerkzugriffsregeln in der NSG nur auf diese Netzwerkkarte angewendet. Wenn eine NSG auf eine einzelne NIC auf einer VM mit mehreren NICs angewendet wird, hat dies keine Auswirkung auf den Datenverkehr zu den anderen NICs. Sie können verschiedene NSGs einer NIC (oder einer VM, je nach Bereitstellungsmodell) und dem Subnetz zuordnen, an das eine Netzwerkkarte oder ein virtueller Computer gebunden ist. Die Priorität richtet sich nach der Richtung des Datenverkehrs.

Achten Sie darauf, Ihre NSGs zu [planen](../virtual-network/virtual-network-vnet-plan-design-arm.md), wenn Sie Ihre VMs und virtuellen Netzwerke planen.

In dieser Tabelle sind die Methoden aufgeführt, die Sie zum Erstellen einer Netzwerksicherheitsgruppe verwenden können.

| Methode | BESCHREIBUNG |
| ------ | ----------- |
| [Azure portal](../virtual-network/tutorial-filter-network-traffic.md) | Wenn Sie im Azure-Portal eine VM erstellen, wird automatisch eine NSG erstellt und der vom Portal erstellten NIC zugeordnet. Der Name der NSG ist eine Kombination aus dem Namen der VM und dem Zusatz **`-nsg`** . </br> Diese NSG enthält eine Regel für eingehenden Datenverkehr: </br> Mit einer Priorität von 1000. </br> Der Dienst ist auf RDP festgelegt. </br> Das Protokoll ist auf TCP festgelegt. </br> Der Port ist auf 3389 festgelegt. </br> Die Aktion ist auf **Zulassen** festgelegt. </br> Wenn Sie anderen eingehenden Datenverkehr auf dem virtuellen Computer zulassen möchten, müssen Sie eine weitere Regel bzw. weitere Regeln erstellen. |
| [Azure PowerShell](../virtual-network/tutorial-filter-network-traffic.md) | Verwenden Sie [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig), und geben Sie die erforderlichen Informationen für die Regel an. Verwenden Sie [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup), um die NSG zu erstellen. Verwenden Sie [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig), um die NSG für das Subnetz zu konfigurieren. Verwenden Sie [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork), um die NSG im virtuellen Netzwerk hinzuzufügen. |
| [Azure-Befehlszeilenschnittstelle](../virtual-network/tutorial-filter-network-traffic-cli.md) | Verwenden Sie [az network nsg create](/cli/azure/network/nsg) für die erste Erstellung der NSG. Verwenden Sie [az network nsg rule create](/cli/azure/network/nsg/rule), um der NSG Regeln hinzuzufügen. Verwenden Sie [az network vnet subnet update](/cli/azure/network/vnet/subnet), um die NSG dem Subnetz hinzuzufügen. |
| [Vorlage](../virtual-network/template-samples.md) | Verwenden Sie [Create a Network Security Group](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/security-group-create) (Erstellen einer Netzwerksicherheitsgruppe) als Anleitung zum Bereitstellen einer Netzwerksicherheitsgruppe mit einer Vorlage. |

## <a name="load-balancers"></a>Load Balancer

Der [Azure Load Balancer](../load-balancer/load-balancer-overview.md) bietet Hochverfügbarkeit und Netzwerkleistung für Ihre Anwendungen. Ein Load Balancer (Lastenausgleich) kann konfiguriert werden, um [eingehenden Internet-Datenverkehr für VMs auszugleichen](../load-balancer/components.md#frontend-ip-configurations) oder [Datenverkehr zwischen VMs in einem VNet auszugleichen](../load-balancer/components.md#frontend-ip-configurations). Ein Lastenausgleich kann außerdem den Datenverkehr zwischen lokalen Computern und VMs in einem standortübergreifenden Netzwerk ausgleichen oder externen Datenverkehr an eine bestimmte VM weiterleiten.

Der Lastenausgleich ist für die Zuordnung von eingehendem und ausgehendem Datenverkehr zwischen den folgenden Komponenten zuständig:  

* der öffentlichen IP-Adresse und dem Port des Lastenausgleichs.

* der privaten IP-Adresse und dem Port der VM.

Beim Erstellen eines Lastenausgleichs müssen Sie auch die folgenden Konfigurationselemente berücksichtigen:

- **Front-End-IP-Konfiguration**: Ein Lastenausgleichsmodul kann eine oder mehrere Front-End-IP-Adressen umfassen. Diese IP-Adressen dienen als Eingang für den Datenverkehr.
- **Back-End-Adresspool**: IP-Adressen, die der NIC zugeordnet sind, auf die die Last verteilt wird.
- **[Portweiterleitung](../load-balancer/tutorial-load-balancer-port-forwarding-portal.md)** : Definiert mithilfe von NAT-Regeln für eingehenden Datenverkehr, wie eingehender Datenverkehr durch die Front-End-IP-Adressen geleitet und an die Back-End-IP-Adressen verteilt wird.
- **Lastenausgleichsregeln**: Dienen zum Zuordnen einer bestimmten Front-End-IP-/Port-Kombination zu einer Back-End-IP-Adressen-/Port-Kombination. Ein einzelnes Lastenausgleichsmodul kann mehrere Lastenausgleichsregeln umfassen. Jede Regel ist eine Kombination aus der Front-End-IP und dem Front-End-Port sowie der Back-End-IP und dem Back-End-Port von virtuellen Computern.
- **[Tests](../load-balancer/load-balancer-custom-probe-overview.md)** : Dienen zum Überwachen der Integrität von VMs. Wenn ein Test nicht reagiert, beendet der Lastenausgleich das Senden neuer Verbindungen an die fehlerhafte VM. Die vorhandenen Verbindungen sind nicht betroffen, und neue Verbindungen werden an fehlerfreie VMs gesendet.
- **[Ausgangsregeln](../load-balancer/load-balancer-outbound-connections.md#outboundrules)** : Eine Ausgangsregel konfiguriert die Netzwerkadressenübersetzung (Network Address Translation, NAT) für ausgehenden Datenverkehr für alle virtuellen Computer oder Instanzen, die vom Back-End-Pool Ihrer Load Balancer Standard-Instanz für die Front-End-Übersetzung identifiziert wurden.

In dieser Tabelle sind die Methoden aufgeführt, die Sie zum Erstellen eines Lastenausgleichs für den Internetzugriff verwenden können.

| Methode | Beschreibung |
| ------ | ----------- |
| Azure-Portal |  Sie können [einen Lastausgleich für den Internetdatenverkehr an virtuelle Computer mit dem Azure-Portal vornehmen](../load-balancer/quickstart-load-balancer-standard-public-portal.md). |
| [Azure PowerShell](../load-balancer/quickstart-load-balancer-standard-public-powershell.md) | Verwenden Sie zum Angeben des Bezeichners der zuvor erstellten öffentlichen IP-Adresse [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) mit dem Parameter **`-PublicIpAddress`** . Verwenden Sie [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig), um die Konfiguration des Back-End-Adresspools zu erstellen. Verwenden Sie [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig), um NAT-Regeln für den eingehenden Datenverkehr zu erstellen, die der von Ihnen erstellten Front-End-IP-Konfiguration zugeordnet sind. Verwenden Sie [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig), um die erforderlichen Tests zu erstellen. Verwenden Sie [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig), um die Konfiguration für den Lastenausgleich zu erstellen. Verwenden Sie [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer), um den Lastenausgleich zu erstellen.|
| [Azure-Befehlszeilenschnittstelle](../load-balancer/quickstart-load-balancer-standard-public-cli.md) | Verwenden Sie [az network lb create](/cli/azure/network/lb), um die erste Konfiguration für den Lastenausgleich zu erstellen. Verwenden Sie [az network lb frontend-ip create](/cli/azure/network/lb/frontend-ip), um die zuvor erstellte öffentliche IP-Adresse hinzuzufügen. Verwenden Sie [az network lb address-pool create](/cli/azure/network/lb/address-pool), um die Konfiguration des Back-End-Adresspools hinzuzufügen. Verwenden Sie [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule), um NAT-Regeln hinzuzufügen. Verwenden Sie [az network lb rule create](/cli/azure/network/lb/rule), um die Lastenausgleichsregeln hinzuzufügen. Verwenden Sie [az network lb probe create](/cli/azure/network/lb/probe), um die Tests hinzuzufügen. |
| [Vorlage](../load-balancer/quickstart-load-balancer-standard-public-template.md) | Verwenden Sie [Create an Internet-facing Standard Load Balancer with three VMs](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/load-balancer-standard-create) (Erstellen eines Lastenausgleichs mit Internetzugriff mit drei VMs, in englischer Sprache) als Leitfaden zum Bereitstellen eines Lastenausgleichs mit einer Vorlage. |

In dieser Tabelle sind die Methoden aufgeführt, die Sie zum Erstellen eines internen Lastenausgleichs verwenden können.

| Methode | Beschreibung |
| ------ | ----------- |
| Azure-Portal | Sie können die [interne Datenverkehrslast mithilfe eines Lastenausgleichsmoduls im Azure-Portal ausgleichen](../load-balancer/quickstart-load-balancer-standard-internal-portal.md). |
| [Azure PowerShell](../load-balancer/quickstart-load-balancer-standard-internal-powershell.md) | Verwenden Sie zum Angeben einer privaten IP-Adresse im Netzwerksubnetz [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) mit dem Parameter **`-PrivateIpAddress`** . Verwenden Sie [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig), um die Konfiguration des Back-End-Adresspools zu erstellen. Verwenden Sie [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig), um NAT-Regeln für den eingehenden Datenverkehr zu erstellen, die der von Ihnen erstellten Front-End-IP-Konfiguration zugeordnet sind. Verwenden Sie [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig), um die erforderlichen Tests zu erstellen. Verwenden Sie [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig), um die Konfiguration für den Lastenausgleich zu erstellen. Verwenden Sie [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer), um den Lastenausgleich zu erstellen.|
| [Azure-Befehlszeilenschnittstelle](../load-balancer/quickstart-load-balancer-standard-internal-cli.md) | Verwenden Sie den Befehl [az network lb create](/cli/azure/network/lb), um die erste Konfiguration für den Lastenausgleich zu erstellen. Verwenden Sie zum Definieren der privaten IP-Adresse [az network lb frontend-ip create](/cli/azure/network/lb/frontend-ip) mit dem Parameter **`--private-ip-address`** . Verwenden Sie [az network lb address-pool create](/cli/azure/network/lb/address-pool), um die Konfiguration des Back-End-Adresspools hinzuzufügen. Verwenden Sie [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule), um NAT-Regeln hinzuzufügen. Verwenden Sie [az network lb rule create](/cli/azure/network/lb/rule), um die Lastenausgleichsregeln hinzuzufügen. Verwenden Sie [az network lb probe create](/cli/azure/network/lb/probe), um die Tests hinzuzufügen.|
| [Vorlage](../load-balancer/quickstart-load-balancer-standard-internal-template.md) | Verwenden Sie [Create 2 Virtual Machines under an Internal Load balancer and configure Load Balancing rules for the VMs](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/2-vms-internal-load-balancer) (Erstellen von 2 VMs unter einem internen Lastenausgleich und Konfigurieren von Lastenausgleichsregeln für die VMs, in englischer Sprache) als Leitfaden zum Bereitstellen eines Lastenausgleichs mit einer Vorlage. |

## <a name="virtual-machines"></a>Virtuelle Computer

Virtuelle Computer können in demselben virtuellen Netzwerk erstellt werden, und die Verbindung dazwischen kann über private IP-Adressen hergestellt werden. Virtuelle Computer können ebenfalls eine Verbindung herstellen, wenn sie sich in unterschiedlichen Subnetzen befinden. Für das Herstellen einer Verbindung ist es nicht notwendig, ein Gateway zu konfigurieren oder eine öffentliche IP-Adresse zu verwenden. Um virtuelle Computer in ein virtuelles Netzwerk zu integrieren, müssen Sie zunächst das virtuelle Netzwerk erstellen. Sobald Sie einen virtuellen Computer erstellen, können Sie ihn dem virtuellen Netzwerk und dem Subnetz zuweisen. Virtuelle Computer beschaffen sich ihre Netzwerkeinstellungen während der Bereitstellung oder des Startvorgangs.

Virtuellen Computern wird bei der Bereitstellung eine IP-Adresse zugewiesen. Wenn Sie mehrere VMs in einem virtuellen Netzwerk oder Subnetz bereitstellen, werden diesen beim Starten IP-Adressen zugewiesen. Sie können einer VM auch eine statische IP zuweisen. Erwägen Sie beim Zuweisen einer statischen IP die Verwendung eines spezifischen Subnetzes, um zu verhindern, dass eine statische IP versehentlich für eine andere VM wiederverwendet wird.

Wenn Sie eine VM erstellen und diese später in ein virtuelles Netzwerk migrieren möchten, ist dies keine einfache Änderung der Konfiguration. Stellen Sie den virtuellen Computer erneut in dem virtuellen Netzwerk bereit. Die einfachste Möglichkeit zur erneuten Bereitstellung ist das Löschen der VM, ohne dass aber die daran angefügten Datenträger gelöscht werden. Anschließend wird die VM mit den Originaldatenträgern im virtuellen Netzwerk neu erstellt.

In dieser Tabelle sind die Methoden aufgeführt, die Sie zum Erstellen einer VM in einem VNet verwenden können.

| Methode | BESCHREIBUNG |
| ------ | ----------- |
| [Azure portal](../virtual-machines/windows/quick-create-portal.md) | Es werden die bereits erwähnten Standard-Netzwerkeinstellungen verwendet, um eine VM mit einer einzelnen NIC zu erstellen. Zum Erstellen einer VM mit mehreren NICs müssen Sie eine andere Methode verwenden. |
| [Azure PowerShell](../virtual-machines/windows/tutorial-manage-vm.md) | Umfasst die Verwendung von [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface) zum Hinzufügen der zuvor erstellten NIC zur VM-Konfiguration. |
| [Azure-Befehlszeilenschnittstelle](../virtual-machines/linux/create-cli-complete.md) | Erstellen Sie eine VM, und stellen Sie dafür eine Verbindung mit einem virtuellen Netzwerk, einem Subnetz und einer NIC her, indem Sie die einzelnen erforderlichen Schritte ausführen. |
| [Vorlage](../virtual-machines/windows/ps-template.md) | Verwenden Sie [Very simple deployment of a Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-simple-windows) (Sehr einfache Bereitstellung einer Windows-VM) als Anleitung zum Bereitstellen einer VM mit einer Vorlage. |

## <a name="virtual-network-nat"></a>Virtual Network NAT

Virtual Network NAT (Network Address Translation, Netzwerkadressenübersetzung) vereinfacht für virtuelle Netzwerke die Einrichtung von ausschließlich ausgehenden Internetverbindungen. Bei der Konfiguration in einem Subnetz werden für die gesamte Konnektivität in ausgehender Richtung die von Ihnen angegebenen statischen öffentlichen IP-Adressen verwendet. Die ausgehende Konnektivität ist möglich, ohne dass ein Lastenausgleich oder öffentliche IP-Adressen direkt virtuellen Computern zugeordnet werden. NAT ist vollständig verwaltet und äußerst resilient.

Die ausgehende Konnektivität kann für jedes Subnetz mit NAT definiert werden. Mehrere Subnetze desselben virtuellen Netzwerks können über unterschiedliche NATs verfügen. Ein Subnetz wird konfiguriert, indem angegeben wird, welche NAT-Gatewayressource verwendet werden soll. Für alle ausgehenden UDP- und TCP-Datenflüsse einer VM-Instanz wird NAT genutzt.
NAT ist mit Ressourcen für öffentliche IP-Adressen und Präfixressourcen für öffentliche IP-Adressen der Standard-SKU kompatibel (bzw. einer Kombination dieser Ressourcen). Sie können das Präfix einer öffentlichen IP-Adresse direkt nutzen, oder die öffentlichen IP-Adressen des Präfixes auf mehrere NAT-Gatewayressourcen verteilen. Mit NAT wird der gesamte Datenverkehr auf den IP-Adressbereich des Präfixes bereinigt. Das Filtern von IP-Adressen Ihrer Bereitstellungen ist einfacher.

Der gesamte ausgehende Datenverkehr für das Subnetz wird per NAT ohne Konfiguration durch den Kunden automatisch verarbeitet. Benutzerdefinierte Routen sind nicht erforderlich. NAT hat Vorrang vor anderen Ausgangsszenarien und ersetzt das Standard-Internetziel eines Subnetzes.

Virtuelle Computer, die von VM-Skalierungsgruppen im Orchestrierungsmodus „Flexibel“ erstellt werden, haben keinen ausgehenden Standardzugriff. Virtual Network NAT ist die empfohlene Methode für den ausgehenden Zugriff für den flexiblen Orchestrierungsmodus von VM-Skalierungssätzen.

Weitere Informationen über die NAT-Gatewayressource und Virtual Network NAT finden Sie unter [Was ist Azure Virtual Network NAT?](./nat-gateway/nat-overview.md).

In dieser Tabelle sind die Methoden aufgeführt, die Sie zum Erstellen einer NAT-Gatewayressource verwenden können.

| Methode | BESCHREIBUNG |
| ------ | ----------- |
| [Azure portal](./nat-gateway/tutorial-create-nat-gateway-portal.md) | Erstellt ein virtuelles Netzwerk, ein Subnetz, eine öffentliche IP-Adresse, ein NAT-Gateway und einen virtuellen Computer zum Testen der NAT-Gatewayressource. |
| [Azure PowerShell](./nat-gateway/tutorial-create-nat-gateway-powershell.md) | Nutzt [New-AzNatGateway](/powershell/module/az.network/new-aznatgateway) zum Erstellen einer NAT-Gatewayressource. Erstellt ein virtuelles Netzwerk, ein Subnetz, eine öffentliche IP-Adresse, ein NAT-Gateway und einen virtuellen Computer zum Testen der NAT-Gatewayressource. |
| [Azure-Befehlszeilenschnittstelle](./nat-gateway/tutorial-create-nat-gateway-cli.md) | Nutzt [az network nat gateway create](/cli/azure/network/nat#az_network_nat_gateway_create) zum Erstellen einer NAT-Gatewayressource. Erstellt ein virtuelles Netzwerk, ein Subnetz, eine öffentliche IP-Adresse, ein NAT-Gateway und einen virtuellen Computer zum Testen der NAT-Gatewayressource. |
| [Vorlage](./nat-gateway/quickstart-create-nat-gateway-template.md) | Erstellt ein virtuelles Netzwerk, ein Subnetz, eine öffentliche IP-Adresse und eine NAT-Gatewayressource. |

## <a name="azure-bastion"></a>Azure Bastion 

Azure Bastion wird bereitgestellt, um eine sichere Verwaltungskonnektivität für virtuelle Computer in einem virtuellen Netzwerk zu gewährleisten. Mit dem Azure Bastion-Dienst können Sie sichere und nahtlose RDP- oder SSH-Verbindungen mit den virtuellen Computern in Ihrem virtuellen Netzwerk herstellen. Azure Bastion ermöglicht Verbindungen ohne Verfügbarmachung einer öffentlichen IP-Adresse auf dem virtuellen Computer. Verbindungen werden direkt über das Azure-Portal hergestellt – ganz ohne zusätzlichen Client/Agent oder zusätzliche Software. Azure Bastion unterstützt öffentliche IP-Adressen der Standard-SKU.

Weitere Informationen zu Azure Bastion finden Sie unter [Was ist Azure Bastion?](../bastion/bastion-overview.md).

In dieser Tabelle sind die Methoden aufgeführt, die Sie zum Erstellen einer Azure Bastion-Bereitstellung verwenden können.

| Methode | BESCHREIBUNG |
| ------ | ----------- |
| [Azure portal](../bastion/quickstart-host-portal.md) | Erstellt ein virtuelles Netzwerk, Subnetze, eine öffentliche IP-Adresse, einen Bastionhost und virtuelle Computer. |
| [Azure PowerShell](../bastion/bastion-create-host-powershell.md) | Erstellt ein virtuelles Netzwerk, Subnetze, eine öffentliche IP-Adresse und einen Bastionhost. Nutzt [New-AzBastion](/powershell/module/az.network/new-azbastion), um den Bastionhost zu erstellen. |
| [Azure-Befehlszeilenschnittstelle](../bastion/create-host-cli.md) | Erstellt ein virtuelles Netzwerk, Subnetze, eine öffentliche IP-Adresse und einen Bastionhost. Nutzt [az network bastion create](/cli/azure/network/bastion#az_network_bastion_create), um den Bastionhost zu erstellen. |
| [Vorlage](../virtual-network/template-samples.md) | Ein Beispiel für die Bereitstellung einer Vorlage, mit der ein Azure Bastion-Host in eine Beispielbereitstellung integriert wird, finden Sie unter [Schnellstart: Erstellen eines öffentlichen Lastenausgleichs für den Lastenausgleich virtueller Computer mithilfe einer ARM-Vorlage](../load-balancer/quickstart-load-balancer-standard-public-template.md). |

## <a name="next-steps"></a>Nächste Schritte
Informationen zu den VM-spezifischen Schritten zum Verwalten von virtuellen Azure-Netzwerken für VMs finden Sie in den Tutorials für [Windows](../virtual-machines/windows/tutorial-virtual-network.md) oder [Linux](../virtual-machines/linux/tutorial-virtual-network.md).

Es gibt auch Tutorials dazu, wie Sie den Lastenausgleich für VMs durchführen und hoch verfügbare Anwendungen für [Windows](../virtual-machines/windows/tutorial-load-balancer.md) oder [Linux](../virtual-machines/linux/tutorial-load-balancer.md) erstellen.

- Informieren Sie sich, wie Sie [benutzerdefinierte Routen und die IP-Weiterleitung](../virtual-network/virtual-networks-udr-overview.md) konfigurieren.
- Informieren Sie sich, wie Sie [VNet-zu-VNet-Verbindungen](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) konfigurieren.
- Lesen Sie die Informationen zur [Problembehandlung bei Routen](../virtual-network/diagnose-network-routing-problem.md).
- Weiter Informationen über den [Netzwerkdurchsatz virtueller Computer](../virtual-network/virtual-machine-network-throughput.md)