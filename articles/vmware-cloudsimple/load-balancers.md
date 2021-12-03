---
title: 'Azure VMware Solution by CloudSimple: Auswählen einer Lastenausgleichslösung für private CloudSimple-Clouds'
description: Beschreibt die Lastenausgleichsoptionen beim Bereitstellen einer Anwendung in einer privaten Cloud.
author: suzizuber
ms.author: v-szuber
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: da744c44dfd20266304fcf2991c26e92028fa40b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132339236"
---
# <a name="choose-a-load-balancing-solution-for-cloudsimple-private-clouds"></a>Auswählen einer Lastenausgleichslösung für private CloudSimple-Clouds

Wenn Sie eine Anwendung in einer privaten CloudSimple-Cloud bereitstellen, können Sie eine der verschiedenen Optionen für Lastenausgleich auswählen.

Sie können einen virtuellen oder softwarebasierten Lastenausgleich in ihrer privaten CloudSimple-Cloud auswählen oder sogar den Azure L7-Lastenausgleich, der in Ihrem Azure-Abonnement ausgeführt wird, als Front-End für die Webschicht-VMS verwenden, die in der privaten CloudSimple-Cloud ausgeführt werden. Einige der Optionen, die verfügbar sind:

## <a name="virtual-load-balancers"></a>Virtueller Lastenausgleich

Sie können virtuelle Lastenausgleichsgeräte in Ihrer VMware-Umgebung über die vCenter-Schnittstelle bereitstellen und diese so konfigurieren, dass Sie als Front-End für Ihren Anwendungsdatenverkehr dienen.

Einige beliebte Anbieter sind: NGINX: http://nginx.org/en/docs/http/load_balancing.html F5-BigIP-Traffic Manager: https://www.f5.com/products/big-ip-services/virtual-editions Citrix ADC: https://www.citrix.com/products/citrix-adc/

## <a name="azure-l7-load-balancer"></a>Azure L7-Lastenausgleich

Wenn Sie Azure Application Gateway als L7-Lastenausgleich für Ihre Anwendung verwenden, die in einer privaten Cloud ausgeführt wird, müssen Sie die Lastenausgleichssoftware nicht verwalten. Die Lastenausgleichssoftware wird von Azure verwaltet. Alle Webschicht-VMs in der privaten Cloud verwenden private IP-Adressen und erfordern keine zusätzlichen NAT-Regeln oder öffentlichen IP-Adressen zum Auflösen von Namen. VMs der Webschicht kommunizieren mit Azure Application Gateway über eine private Verbindung mit niedriger Latenz und hoher Bandbreite.

Weitere Informationen zum Konfigurieren dieser Lösung finden Sie im Lösungsleitfaden zum Verwenden von Azure Application Gateway als L7-Lastenausgleich.

## <a name="azure-internal-load-balancer"></a>Interner Azure-Lastenausgleich

Wenn Sie Ihre Anwendung in einer Hybridbereitstellung ausführen möchten, bei der die Web-Front-End-Schicht innerhalb eines Azure vNet in Ihrem Azure-Abonnement und die DB-Schicht der Anwendung in VMware-VMs in der privaten CloudSimple-Cloud ausgeführt wird, können Sie den internen Lastausgleich (L4-Lastenausgleich) von Azure vor Ihren VMs der DB-Schicht für die Datenverkehrsverwaltung verwenden.

Weitere Informationen finden Sie in der Azure-Dokumentation zum [internen Lastenausgleich](../load-balancer/components.md#frontend-ip-configurations).

## <a name="global-server-load-balancer"></a>Globaler Serverlastenausgleich

Wenn Sie nach einem DNS-basierten Lastenausgleich suchen, können Sie entweder Lösungen von Drittanbietern verwenden, die in Azure Marketplace verfügbar sind, oder die native Azure-Lösung nutzen.

Azure Traffic Manager ist ein DNS-basierter Lastenausgleich für Datenverkehr, der es Ihnen ermöglicht, den Datenverkehr optimal auf Dienste in den globalen Azure-Regionen und in der lokalen Umgebung zu verteilen, und gleichzeitig Hochverfügbarkeit und Reaktionsfähigkeit sicherzustellen. Weitere Informationen finden Sie in der Azure-Dokumentation zu [Traffic Manager](../traffic-manager/traffic-manager-configure-geographic-routing-method.md).
