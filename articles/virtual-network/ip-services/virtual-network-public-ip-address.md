---
title: Erstellen, Ändern oder Löschen einer öffentlichen Azure-IP-Adresse
titleSuffix: Azure Virtual Network
description: Verwalten öffentlicher IP-Adressen Erfahren Sie, inwiefern eine öffentliche IP-Adresse eine Ressource mit konfigurierbaren Einstellungen ist.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/20/2021
ms.author: allensu
ms.openlocfilehash: 92f472afdb642633a093ebdf765b0d8ba968bd8a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130228335"
---
# <a name="create-change-or-delete-an-azure-public-ip-address"></a>Erstellen, Ändern oder Löschen einer öffentlichen Azure-IP-Adresse

Sie erhalten Informationen über öffentliche IP-Adressen und darüber, wie Sie diese erstellen, ändern und löschen können. Eine öffentliche IP-Adresse ist eine Ressource mit konfigurierbaren Einstellungen. Das Zuweisen einer öffentlichen IP-Adresse an eine Azure-Ressource, die öffentliche IP-Adressen unterstützt, ermöglicht Folgendes:

- Eingehende Kommunikation aus dem Internet an die Ressource, z.B. Azure Virtual Machines (VM), Azure Application Gateways, Azure Load Balancers, Azure VPN Gateways und weitere.
- Ausgehende Konnektivität mit dem Internet über eine vorhersagbare IP-Adresse.

[!INCLUDE [ephemeral-ip-note.md](../../../includes/ephemeral-ip-note.md)]

## <a name="create-a-public-ip-address"></a>Erstellen einer öffentlichen IP-Adresse

Anleitungen zum Erstellen öffentlicher IP-Adressen über das Portal, PowerShell, die CLI oder Resource Manager-Vorlagen finden Sie auf den folgenden Seiten:

 * [Erstellen öffentlicher IP-Adressen: Portal](./create-public-ip-portal.md?tabs=option-create-public-ip-standard-zones)
 * [Erstellen öffentlicher IP-Adressen: PowerShell](./create-public-ip-powershell.md?tabs=option-create-public-ip-standard-zones)
 * [Erstellen öffentlicher IP-Adressen: Azure CLI](./create-public-ip-cli.md?tabs=option-create-public-ip-standard-zones)
 * [Erstellen öffentlicher IP-Adressen: Vorlage](./create-public-ip-template.md)

>[!NOTE]
>Obwohl das Portal die Option bietet, zwei öffentliche IP-Adressen zu erstellen (eine IPv4- und eine IPv6-Adresse), erstellen die folgenden PowerShell- und CLI-Befehle eine Ressource mit einer Adresse für jeweils nur eine der IP-Versionen. Wenn Sie zwei öffentliche IP-Adressressourcen (eine für jede IP-Version) benötigen, müssen Sie den folgenden Befehl zweimal ausführen und bei jeder Ausführung unterschiedliche Namen und IP-Versionen für die öffentlichen IP-Adressressourcen angeben.

Weitere Details zu den spezifischen Attributen einer öffentlichen IP-Adresse während der Erstellung finden Sie in der folgenden Tabelle:

   |Einstellung|Erforderlich?|Details|
   |---|---|---|
   |IP-Version|Ja| Wählen Sie „IPv4“, „IPv6“ oder „Beide“ aus. Wenn Sie „Beide“ auswählen, werden zwei öffentliche IP-Adressen erstellt: eine IPv4-Adresse und eine IPv6-Adresse. Weitere Informationen finden Sie unter [Was ist IPv6 für Azure Virtual Network? (Vorschau)](ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|
   |SKU|Ja|Alle öffentlichen IP-Adressen, die vor der Einführung von SKUs erstellt wurden, sind öffentliche IP-Adressen für eine **Basic**-SKU. Sie können die SKU nicht ändern, nachdem die öffentliche IP-Adresse erstellt wurde. Ein eigenständiger virtueller Computer, virtuelle Computer innerhalb einer Verfügbarkeitsgruppe oder VM-Skalierungsgruppen können Basic- oder Standard-SKUs verwenden. Das Mischen von SKUs zwischen virtuellen Computern in Verfügbarkeitsgruppen oder Skalierungsgruppen oder eigenständigen VMs ist nicht zulässig. **Basic**-SKU: Wenn Sie eine öffentliche IP-Adresse in einer Region erstellen, die Verfügbarkeitszonen unterstützt, wird die Einstellung **Verfügbarkeitszone** standardmäßig auf *Keine* festgelegt. Öffentliche IP-Adressen des Typs „Basic“ unterstützen keine Verfügbarkeitszonen. **Standard**-SKU: Eine öffentliche IP-Adresse für eine Standard-SKU kann dem Front-End eines virtuellen Computers oder Load Balancers zugeordnet werden. Wenn Sie eine öffentliche IP-Adresse in einer Region erstellen, die Verfügbarkeitszonen unterstützt, wird die Einstellung **Verfügbarkeitszone** standardmäßig auf *Zonenredundant* festgelegt. Weitere Informationen zu Verfügbarkeitszonen finden Sie im Abschnitt **Verfügbarkeitszone**. Die Standard-SKU ist erforderlich, wenn Sie die Adresse einem Standard-Load Balancer zuordnen. Weitere Informationen zum Standard-Load Balancer finden Sie unter [Azure Load Balancer Standard overview](../../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Übersicht über den Standard-Azure Load Balancer, in englischer Sprache). Wenn Sie der Netzwerkschnittstelle eines virtuellen Computers eine öffentliche IP-Adresse für eine Standard-SKU zuweisen, müssen Sie den geplanten Datenverkehr explizit mit einer [Netzwerksicherheitsgruppe](../../virtual-network/network-security-groups-overview.md#network-security-groups) zulassen. Die Kommunikation mit der Ressource schlägt fehl, bis Sie eine Netzwerksicherheitsgruppe erstellen und zuordnen und den gewünschten Datenverkehr explizit zulassen.|
   |Tarif|Ja|Zeigt an, ob die IP-Adresse einer Region zugeordnet ist (**Regional**), oder ob es sich um einen "Anycast" aus mehreren Regionen handelt (**Global**). *Beachten Sie, dass es sich bei IP-Adressen der „globalen Ebene“ um Vorschaufunktionalität für Standard-IPs handelt, die derzeit nur für regionsübergreifende Load Balancer* verwendet wird.|
   |Name|Ja|Der Name muss innerhalb der ausgewählten Ressourcengruppe eindeutig sein.|
   |IP-Adresszuweisung|Ja|**Dynamisch**: Dynamische Adressen werden zugewiesen, nachdem eine öffentliche IP-Adresse einer Azure-Ressource zugeordnet und erstmalig gestartet wurde. Dynamische Adressen können sich ändern, wenn eine Ressource wie z. B. eine VM beendet (Zuordnung aufgehoben) und dann neu gestartet wird. Die Adresse bleibt unverändert, wenn eine VM neu gestartet oder beendet wird. Durch das Entfernen einer öffentlichen IP-Adressressource von einer Ressource wird die dynamische Adresse freigegeben.<br> **Statisch:** Statische Adressen werden zugewiesen, wenn eine öffentliche IP-Adresse erstellt wird. Statische Adressen werden erst freigegeben, wenn eine öffentliche IP-Adressressource gelöscht wird. <br>Hinweis: Wenn Sie *IPv6* als **IP-Version** auswählen, muss für die SKU „Basic“ als Zuweisungsmethode *Dynamisch* ausgewählt werden.  Adressen der SKU „Standard“ sind sowohl für IPv4 als auch für IPv6 *statisch*. |
  |Routingpräferenz |Ja| Standardmäßig ist die Routingpräferenz für öffentliche IP-Adressen auf „Microsoft-Netzwerk“ festgelegt. Damit wird der Datenverkehr über das globale Microsoft-WAN an den Benutzer übertragen.  Durch Auswahl von „Internet“ wird der Datenverkehr im Microsoft-Netzwerk minimiert, anstatt den Datenverkehr über das Transit-ISP-Netzwerk zu optimierten Kosten zu übertragen.  Eine Routingpräferenz für öffentliche IP-Adressen kann nach dem Erstellen nicht mehr geändert werden.  Weitere Informationen zu den Routingpräferenzen finden Sie unter [Was ist Routingpräferenz (Vorschau)?](routing-preference-overview.md)   |
   |Leerlaufzeitüberschreitung (Minuten)|Nein|Gibt an, wie viele Minuten eine TCP- oder HTTP-Verbindung geöffnet bleiben soll, ohne dass Clients Keep-Alive-Meldungen senden müssen. Wenn Sie IPv6 als **IP-Version** auswählen, ist dieser Wert auf 4 Minuten festgelegt und kann nicht geändert werden. |
   |DNS-Namensbezeichnung|Nein|Muss in dem Azure-Standort, in dem Sie den Namen erstellen, eindeutig sein (über alle Abonnements und Kunden hinweg). Azure registriert den Namen und die IP-Adresse automatisch im DNS, sodass Sie über den Namen eine Verbindung mit der Ressource herstellen können. Azure fügt ein Standardsubnetz wie *location.cloudapp.azure.com* an den von Ihnen bereitgestellten Namen an, um den vollqualifizierten DNS-Namen zu erstellen. Wenn Sie beide Adressversionen für die Erstellung ausgewählt haben, wird sowohl der IPv4- als auch der IPv6-Adresse der gleiche DNS-Name zugewiesen. Das Standard-DNS von Azure enthält sowohl IPv4-A- als auch IPv6-AAAA-Namenseinträge. Beim DNS-Lookup antwortet das Standard-DNS mit beiden Einträgen. Der Client wählt die Adresse (IPv4 oder IPv6) für die Kommunikation aus. Sie können den Azure DNS-Dienst verwenden, um einen DNS-Namen mit einem benutzerdefinierten Suffix zu konfigurieren, der in die öffentliche IP-Adresse aufgelöst wird. Weitere Informationen finden Sie unter [Bereitstellen von benutzerdefinierten Domäneneinstellungen für einen Azure-Dienst mit Azure DNS](../../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|
   |Name (nur sichtbar, wenn Sie als IP-Version **Beide** auswählen)|Ja, wenn Sie als IP-Version **Beide** auswählen|Der Name muss sich von dem Namen unterscheiden, den Sie als ersten **Namen** in diese Liste eingeben. Wenn Sie sowohl eine IPv4- als auch eine IPv6-Adresse erstellen, erstellt das Portal zwei separate öffentliche IP-Adressressourcen, wobei jeder dieser Ressourcen eine IP-Adressversion zugewiesen ist.|
   |IP-Adresszuweisung (nur sichtbar, wenn Sie als IP-Version **Beide** auswählen)|Ja, wenn Sie als IP-Version **Beide** auswählen| Dieselben Einschränkungen wie bei der IP-Adresszuweisung oben. |
   |Subscription|Ja|Muss im selben [Abonnement](../../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) wie die Ressource vorhanden sein, der Sie die öffentlichen IP-Adressen zuordnen.|
   |Resource group|Ja|Kann in derselben [Ressourcengruppe](../../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) wie die Ressource, der Sie die öffentlichen IP-Adressen zuordnen, oder in einer anderen Ressourcengruppe vorhanden sein.|
   |Standort|Ja|Muss am selben [Standort](https://azure.microsoft.com/regions) (auch als Region bezeichnet) wie die Ressource vorhanden sein, der Sie die öffentlichen IP-Adressen zuordnen.|
   |Verfügbarkeitszone| Nein | Diese Einstellung wird nur angezeigt, wenn Sie einen unterstützten Standort und IP-Adresstyp auswählen. Verfügbarkeitszonen werden von öffentlichen IP-Adressen der SKU **Basic** und von öffentlichen IP-Adressen des Tarifs **Global** nicht unterstützt. Sie können „no-zone“ (Standardoption), eine bestimmte Zone oder Zonenredundanz auswählen. Die Auswahl hängt von Ihren spezifischen Domänenfehleranforderungen ab. Eine Liste unterstützter Standorte und weitere Informationen zu Verfügbarkeitszonen finden Sie unter [Übersicht über Verfügbarkeitszonen](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="view-modify-settings-for-or-delete-a-public-ip-address"></a>Anzeigen, Ändern von Einstellungen oder Löschen einer öffentlichen IP-Adresse

   - **Anzeigen/Auflisten**: Überprüfen Sie die Einstellungen für eine öffentliche IP-Adresse, einschließlich der SKU, der Adresse und einer beliebigen Zuordnung. Zuordnungen können Lastenausgleichs-Front-Ends, VMs und andere Azure-Ressourcen sein.
   - [Ändern](#create-a-public-ip-address): Ändern Sie die Einstellungen anhand der Informationen unter **Erstellen einer öffentlichen IP-Adresse**. Einstellungen wie Leerlauftimeout, DNS-Namensbezeichnung oder Zuweisungsmethode. Den gesamten Vorgang zum Upgrade einer öffentlichen IP-Adresse von der SKU „Basic“ auf die SKU „Standard“ finden Sie unter [Upgrade von öffentlichen Azure-IP-Adressen](./public-ip-upgrade-portal.md).
   
   >[!WARNING]
   >Entfernen Sie die Adresse von den jeweiligen IP-Konfigurationen (siehe Abschnitt **Löschen**), um die Zuweisung für eine öffentliche IP-Adresse von „Statisch“ in „Dynamisch“ zu ändern. Wenn Sie die Zuweisungsmethode von „Statisch“ in „Dynamisch“ ändern, geht die IP-Adresse verloren, die der öffentlichen IP-Adressressource zugewiesen war. Während die öffentlichen DNS-Server von Azure eine Zuordnung zwischen statischen oder dynamischen Adressen und jeder DNS-Namensbezeichnung (sofern eine solche definiert ist) verwalten, kann sich eine dynamische IP-Adresse ändern, wenn der virtuelle Computer gestartet wird, nachdem er sich im Status „Beendet (Zuordnung aufgehoben)“ befunden hat. Möchten Sie verhindern, dass sich die Adresse ändert, weisen Sie eine statische IP-Adresse zu.
   
|Vorgang|Azure-Portal|Azure PowerShell|Azure CLI|
|---|---|---|---|
|Sicht | Im Abschnitt **Übersicht** einer öffentlichen IP-Adresse |[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) zum Abrufen eines öffentlichen IP-Adressobjekts und zum Anzeigen seiner Einstellungen| [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) zum Anzeigen von Einstellungen|
|List | Unter der Kategorie **Öffentliche IP-Adressen** |[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) zum Abrufen von mindestens einem öffentlichen IP-Adressobjekt und zum Anzeigen seiner Einstellungen|[az network public-ip list](/cli/azure/network/public-ip#az_network_public_ip_list), um öffentliche IP-Adressen aufzulisten|
|Ändern | Wählen Sie für eine IP-Adresse mit aufgehobener Zuordnung die Option **Konfiguration** aus, um Folgendes durchzuführen: </br> Ändern des Leerlauftimeouts. </br> Ändern der DNS-Namensbezeichnung. </br> Ändern der Zuweisung einer IP-Adresse von „Statisch“ in „Dynamisch“. </br> Durchführen des Upgrades einer IP-Adresse von „Basic“ auf „Standard“. |[Set-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress) zum Aktualisieren von Einstellungen |[az network public-ip update](/cli/azure/network/public-ip#az_network_public_ip_update) zum Aktualisieren |

   - **Löschen**: Das Löschen öffentlicher IP-Adressen erfordert, dass das öffentliche IP-Objekt keiner IP-Konfiguration und keiner VM-Netzwerkschnittstelle zugeordnet ist. Ausführlichere Informationen finden Sie in der unten stehenden Tabelle.

|Resource|Azure-Portal|Azure PowerShell|Azure CLI|
|---|---|---|---|
|[Virtueller Computer](./remove-public-ip-address-vm.md)|Wählen Sie **Trennen** aus, um die IP-Adresse von der NIC-Konfiguration zu trennen, und wählen Sie dann **Löschen** aus.|[Set-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress), um die IP-Adresse von der NIC-Konfiguration zu trennen, [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) zum Löschen.|[az network public-ip update mit dem Parameter „--remove“](/cli/azure/network/public-ip#az_network_public_ip_update), um die IP-Adresse aus der Konfiguration der Netzwerkschnittstelle zu entfernen. Verwenden Sie [az network public-ip delete](/cli/azure/network/public-ip#az_network_public_ip_delete), um die öffentliche IP-Adresse zu löschen. |
|Lastenausgleichs-Front-End | Navigieren Sie zu einer nicht verwendeten öffentlichen IP-Adresse, und wählen Sie **Zuordnen** aus. Wählen Sie den Lastenausgleich mit der relevanten Front-End-IP-Konfiguration aus, um die IP-Adresse zu ersetzen. Die alte IP-Adresse kann mit derselben Methode wie eine VM gelöscht werden.  | Verwenden Sie [Set-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/set-azloadbalancerfrontendipconfig), um einem öffentlichen Lastenausgleich eine neue Front-End-IP-Konfiguration zuzuordnen. Verwenden Sie [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress), um eine öffentliche IP-Adresse zu löschen. Sie können auch [Remove-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/remove-azloadbalancerfrontendipconfig) verwenden, um eine Front-End-IP-Konfiguration zu entfernen, falls mehrere vorliegen. | Verwenden Sie [az network lb frontend-ip update](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_update), um einem öffentlichen Lastenausgleich eine neue Front-End-IP-Konfiguration zuzuordnen. Verwenden Sie [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress), um eine öffentliche IP-Adresse zu löschen. Sie können auch [az network lb frontend-ip delete](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_delete) verwenden, um eine Front-End-IP-Konfiguration zu entfernen, falls mehrere vorliegen. |
|Firewall|–| [Deallocate()](../../firewall/firewall-faq.yml#how-can-i-stop-and-start-azure-firewall), um die Zuordnung der Firewall aufzuheben und alle IP-Konfigurationen zu entfernen. | Verwenden Sie [az network firewall ip-config delete](/cli/azure/network/firewall/ip-config#az_network_firewall_ip_config_delete), um die IP-Adresse zu entfernen. Verwenden Sie PowerShell, um zuerst die Zuordnung aufzuheben. |

## <a name="virtual-machine-scale-sets"></a>Virtual Machine Scale Sets

Bei Verwendung einer VM-Skalierungsgruppe mit öffentlichen IP-Adressen sind den einzelnen VM-Instanzen keine separaten öffentlichen IP-Objekte zugeordnet. Allerdings kann ein öffentliches IP-Präfixobjekt verwendet werden, um die [Instanz-IPs zu generieren](https://azure.microsoft.com/resources/templates/vmms-with-public-ip-prefix/).

Um die öffentlichen IP-Adressen für eine VM-Skalierungsgruppe aufzulisten, können Sie PowerShell ([Get-AzPublicIpAddress -VirtualMachineScaleSetName](/powershell/module/az.network/get-azpublicipaddress)) oder die CLI ([az virtual machine scale set list-instance-public-ips](/cli/azure/vmss#az_vmss_list_instance_public_ips)) verwenden.

Weitere Informationen finden Sie unter [Netzwerk für Azure-VM-Skalierungsgruppen](../../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine).

## <a name="assign-a-public-ip-address"></a>Zuweisen einer öffentlichen IP-Adresse

Erfahren Sie, wie Sie eine öffentliche IP-Adresse den folgenden Ressourcen zuweisen:

- [Windows](../../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)- oder [Linux](../../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)-VMs bei Erstellung. Hinzufügen einer IP-Adresse zu einer [vorhandenen VM](./virtual-network-network-interface-addresses.md#add-ip-addresses).
- [Öffentlicher Load Balancer](../../load-balancer/quickstart-load-balancer-standard-public-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Application Gateway](../../application-gateway/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Site-to-Site-Verbindung über VPN Gateway](../../vpn-gateway/tutorial-site-to-site-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [VM-Skalierungsgruppe](../../virtual-machine-scale-sets/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [NAT Gateway](../nat-gateway/tutorial-create-nat-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Bastion](../../bastion/quickstart-host-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Firewall](../../firewall/tutorial-firewall-deploy-portal-policy.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Regionsübergreifender Lastenausgleich](../../load-balancer/tutorial-cross-region-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Berechtigungen

Um öffentliche IP-Adressen zu verwalten, muss Ihr Konto der Rolle [Netzwerkmitwirkender](../../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) zugewiesen sein. Auch eine [benutzerdefinierte](../../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Rolle wird unterstützt. Der benutzerdefinierten Rolle müssen die entsprechenden Aktionen zugewiesen werden, die in der folgenden Tabelle aufgeführt sind:

| Aktion                                                             | Name                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft.Network/publicIPAddresses/read                           | Lesen einer öffentlichen IP-Adresse                                          |
| Microsoft.Network/publicIPAddresses/write                          | Schreiben oder Aktualisieren einer öffentlichen IP-Adresse                           |
| Microsoft.Network/publicIPAddresses/delete                         | Löschen einer öffentlichen IP-Adresse                                     |
| Microsoft.Network/publicIPAddresses/join/action                    | Zuordnen einer öffentlichen IP-Adresse zu einer Ressource                    |

## <a name="next-steps"></a>Nächste Schritte

Für öffentliche IP-Adressen fällt eine Schutzgebühr an. Informationen zu den Preisen finden Sie auf der Seite [Preise für IP-Adressen](https://azure.microsoft.com/pricing/details/ip-addresses).

- Erstellen einer öffentlichen IP-Adresse mithilfe von [PowerShell](../../virtual-network/powershell-samples.md)- oder [Azure CLI](../../virtual-network/cli-samples.md)-Beispielskripts oder mithilfe von Azure [Resource Manager-Vorlagen](../../virtual-network/template-samples.md)
- Erstellen und Zuweisen von [Azure Policy-Definitionen](../../virtual-network/policy-reference.md) für öffentliche IP-Adressen