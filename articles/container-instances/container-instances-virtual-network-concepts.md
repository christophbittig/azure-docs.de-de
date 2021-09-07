---
title: Szenarien für die Verwendung eines virtuellen Netzwerks
description: Szenarien, Ressourcen und Einschränkungen für die Bereitstellung von Containergruppen in einem virtuellen Azure-Netzwerk.
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 4d56ba43480182077acc114200ebc69569835bca
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346892"
---
# <a name="virtual-network-scenarios-and-resources"></a>Szenarien und Ressourcen für virtuelle Azure-Netzwerke

Das virtuelle Azure-Netzwerk ([Azure Virtual Network](../virtual-network/virtual-networks-overview.md)) stellt ein sicheres, privates Netzwerk für Ihre Azure- und lokalen Ressourcen bereit. Durch die Bereitstellung von Containergruppen in einem virtuellen Azure-Netzwerk können Ihre Container sicher mit anderen Ressourcen im virtuellen Netzwerk kommunizieren. 

Dieser Artikel bietet Hintergrundinformationen zu Szenarien, Einschränkungen und Ressourcen für virtuelle Netzwerke. Beispiele für die Bereitstellung mithilfe von Azure CLI finden Sie unter [Bereitstellen von Containerinstanzen in einem virtuellen Azure-Netzwerk](container-instances-vnet.md).

> [!IMPORTANT]
> Die Bereitstellung von Containergruppen in einem virtuellen Netzwerk ist in den meisten Regionen, in denen Azure Container Instances verfügbar ist, für Linux-Container allgemein verfügbar. Details hierzu finden Sie unter [Verfügbarkeit von Regionen und Ressourcen](container-instances-region-availability.md). 

## <a name="scenarios"></a>Szenarien

Containergruppen, die in einem virtuellen Azure-Netzwerk bereitgestellt werden, ermöglichen folgende Szenarien:

* Direkte Kommunikation zwischen Containergruppen im gleichen Subnetz
* Senden einer [taskbasierten](container-instances-restart-policy.md) Workloadausgabe von Containerinstanzen an eine Datenbank im virtuellen Netzwerk
* Abrufen des Inhalts für Containerinstanzen von einem [Dienstendpunkt](../virtual-network/virtual-network-service-endpoints-overview.md) im virtuellen Netzwerk
* Aktivieren der Containerkommunikation mit lokalen Ressourcen über ein [VPN-Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) oder [ExpressRoute](../expressroute/expressroute-introduction.md)
* Integrieren in [Azure Firewall](../firewall/overview.md) zum Identifizieren des ausgehenden Datenverkehrs, der vom Container ausgeht 
* Auflösen von Namen über das interne Azure DNS für die Kommunikation mit Azure-Ressourcen im virtuellen Netzwerk, z. B. virtuelle Computer
* Verwenden von NSG-Regeln zum Steuern des Containerzugriffs auf Subnetze oder andere Netzwerkressourcen

## <a name="unsupported-networking-scenarios"></a>Nicht unterstützte Netzwerkszenarien 

* **Azure Load Balancer**: Das Positionieren eines Azure Load Balancer vor Containerinstanzen in einer vernetzten Containergruppe wird nicht unterstützt.
* **Globales Peering virtueller Netzwerke**: Globales Peering (Verbinden virtueller Netzwerke über Azure-Regionen hinweg) wird nicht unterstützt.
* **Öffentliche IP-Adresse oder DNS-Bezeichnung**: Containergruppen, die in einem virtuellen Netzwerk bereitgestellt werden, unterstützen derzeit keine direkte Bereitstellung von Containern im Internet mit einer öffentlichen IP-Adresse oder einem vollqualifizierten Domänennamen.
* **Virtual Network NAT**: Containergruppen, die in einem virtuellen Netzwerk bereitgestellt werden, unterstützen derzeit nicht die Verwendung einer NAT-Gatewayressource für die ausgehende Internetkonnektivität.

## <a name="other-limitations"></a>Weitere Einschränkungen

* Aktuell werden nur Linux-Container in einer Containergruppe unterstützt, die in einem virtuellen Netzwerk bereitgestellt ist.
* Um Containergruppen in einem Subnetz bereitstellen zu können, darf das Subnetz keine anderen Ressourcentypen enthalten. Entfernen Sie alle vorhandenen Ressourcen aus einem vorhandenen Subnetz, bevor Sie Containergruppen für dieses bereitstellen, oder erstellen Sie ein neues Subnetz.
* Sie können keine [verwaltete Identität](container-instances-managed-identity.md) in einer Containergruppe verwenden, die in einem virtuellen Netzwerk bereitgestellt wurde.
* Sie können weder einen [Livetest](container-instances-liveness-probe.md) noch einen [Bereitschaftstest](container-instances-readiness-probe.md) in einer Containergruppe verwenden, die in einem virtuellen Netzwerk bereitgestellt wurde.
* Aufgrund der zusätzlichen beteiligten Netzwerkressourcen erfolgen Bereitstellungen in einem virtuellen Netzwerk in der Regel langsamer als die Bereitstellung einer Standardcontainerinstanz.
* Ausgehende Verbindungen mit Port 25 werden derzeit nicht unterstützt.
* Wenn Sie eine Verbindung zwischen Ihrer Containergruppe und einem Azure Storage-Konto herstellen, müssen Sie dieser Ressource einen [Dienstendpunkt](../virtual-network/virtual-network-service-endpoints-overview.md) hinzufügen.
* [IPv6-Adressen](../virtual-network/ipv6-overview.md) werden derzeit nicht unterstützt. 

## <a name="required-network-resources"></a>Erforderliche Netzwerkressourcen

Es gibt drei Azure Virtual Network-Ressourcen, die für die Bereitstellung von Containergruppen in einem virtuellen Netzwerk erforderlich sind: das [virtuelle Netzwerk](#virtual-network) selbst, ein [delegiertes Subnetz](#subnet-delegated) innerhalb des virtuellen Netzwerks und ein [Netzwerkprofil](#network-profile). 

### <a name="virtual-network"></a>Virtuelles Netzwerk

Ein virtuelles Netzwerk definiert den Adressraum, in dem Sie mindestens ein Subnetz erstellen. Anschließend stellen Sie Azure-Ressourcen (z.B. Containergruppen) in den Subnetzen in Ihrem virtuellen Netzwerk bereit.

### <a name="subnet-delegated"></a>Subnetz (delegiert)

Subnetze unterteilen das virtuelle Netzwerk in getrennte Adressräume, die von den Azure-Ressourcen, die Sie darin platzieren, verwendet werden können. Sie erstellen eine oder mehrere Subnetze innerhalb eines virtuellen Netzwerks.

Das Subnetz, das Sie für Containergruppen verwenden, darf nur Containergruppen enthalten. Wenn Sie zuerst eine Containergruppe für ein Subnetz bereitstellen, delegiert Azure das Subnetz an Azure Container Instances. Nach erfolgter Delegierung kann das Subnetz nur für Containergruppen verwendet werden. Wenn Sie versuchen, andere Ressourcen als Containergruppen für ein delegiertes Subnetz bereitzustellen, tritt ein Fehler beim Vorgang auf.

### <a name="network-profile"></a>Netzwerkprofil

Ein Netzwerkprofil ist eine Netzwerkkonfigurationsvorlage für Azure-Ressourcen. Es gibt bestimmte Netzwerkeigenschaften für die Ressource an, z.B. das Subnetz, in dem diese bereitgestellt werden sollen. Wenn Sie den Befehl [az container create][az-container-create] zum ersten Mal zum Bereitstellen einer Containergruppe in einem Subnetz (und somit in einem virtuellen Netzwerk) verwenden, erstellt Azure ein Netzwerkprofil für Sie. Sie können dieses Netzwerkprofil dann für zukünftige Bereitstellungen in dem Subnetz verwenden. 

Sie müssen die vollständige Resource Manager-Ressourcen-ID eines Netzwerkprofils angeben, um eine Resource Manager-Vorlage, YAML-Datei oder programmgesteuerte Methode zum Bereitstellen einer Containergruppe in einem Subnetz verwenden zu können. Sie können ein Profil verwenden, das Sie zuvor mithilfe des Befehls [az container create][az-container-create] erstellt haben, oder ein Profil mithilfe einer Resource Manager-Vorlage erstellen (Informationen dazu finden Sie im [Vorlagenbeispiel](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.containerinstance/aci-vnet) und in der [Referenz](/azure/templates/microsoft.network/networkprofiles)). Verwenden Sie den Befehl [az network profile list][az-network-profile-list], um die ID eines zuvor erstellten Profils abzurufen. 

In der folgenden Abbildung wurden mehrere Containergruppen für ein Subnetz bereitgestellt, das an Azure Container Instances delegiert wurde. Nachdem Sie eine Containergruppe für ein Subnetz bereitgestellt haben, können Sie zusätzliche Containergruppen für dieses bereitstellen, indem Sie das gleiche Netzwerkprofil angeben.

![Containergruppen in einem virtuellen Netzwerk][aci-vnet-01]

## <a name="next-steps"></a>Nächste Schritte

* Beispiele für die Bereitstellung mithilfe von Azure CLI finden Sie unter [Bereitstellen von Containerinstanzen in einem virtuellen Azure-Netzwerk](container-instances-vnet.md).
* Informationen zum Bereitstellen eines neuen virtuellen Netzwerks, eines Subnetzes, eines Netzwerkprofils und einer Containergruppe mithilfe einer Resource Manager-Vorlage finden Sie unter [Create an Azure container group with VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.containerinstance/aci-vnet
) (Erstellen einer Azure-Containergruppe mit einem virtuellen Netzwerk).
* Wenn Sie das [Azure-Portal](container-instances-quickstart-portal.md) zum Erstellen einer Containerinstanz verwenden, können Sie auf der Registerkarte **Netzwerk** auch Einstellungen für ein neues oder vorhandenes virtuelles Netzwerk angeben.


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-virtual-network-concepts/aci-vnet-01.png

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-network-profile-list]: /cli/azure/network/profile#az_network_profile_list
