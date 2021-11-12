---
author: madsd
ms.service: app-service-web
ms.topic: include
ms.date: 10/01/2020
ms.author: madsd
ms.openlocfilehash: 2064586fafd82cfc67e14b289797a562e3512afd
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130245150"
---
* Die dedizierten Computetarife, einschließlich Basic, Standard, Premium, PremiumV2 und PremiumV3.
* Die App Service-Umgebung, die direkt in Ihrem VNet mit dedizierter unterstützender Infrastruktur bereitgestellt wird und die Tarife „Isolated“ und „IsolatedV2“ nutzt.

Das Feature für die VNet-Integration wird in dedizierten Computetarifen von App Service eingesetzt. In der [App Service-Umgebung](../articles/app-service/environment/overview.md) befindet sich Ihre App bereits in einem VNet und benötigt das VNet-Integrationsfeature nicht, um Ressourcen im selben VNet zu erreichen. Weitere Informationen zu allen Netzwerkfunktionen finden Sie unter [App Service-Netzwerkfunktionen](../articles/app-service/networking-features.md).

Die VNet-Integration ermöglicht Ihrer App den Zugriff auf Ressourcen in Ihrem VNet, gewährt aber keinen eingehenden privaten Zugriff auf Ihre App aus dem VNet. Privater Websitezugriff bezieht sich darauf, den Zugriff auf eine App nur über ein privates Netzwerk zuzulassen, z. B. über ein virtuelles Azure-Netzwerk. Die VNet-Integration wird nur für ausgehende Aufrufe aus Ihrer App an Ihr VNet genutzt. Das Feature für die VNet-Integration verhält sich bei Verwendung mit VNets in derselben Region und mit VNets in anderen Regionen unterschiedlich. Das Feature für die VNet-Integration umfasst zwei Varianten:

* **Regionale VNet-Integration**: Wenn Sie eine Verbindung mit virtuellen Netzwerken in derselben Region herstellen, benötigen Sie ein dediziertes Subnetz in dem VNet, das Sie integrieren.
* **VNet-Integration, die ein Gateway erfordert**: Wenn Sie eine Verbindung mit VNets in anderen Regionen oder mit einem klassischen virtuellen Netzwerk in derselben Region direkt herstellen, benötigen Sie ein Azure Virtual Network-Gateway, das im Ziel-VNet erstellt wurde.

Für das Feature zur VNet-Integration gilt Folgendes:

* Erfordert einen App Service-Tarif des Typs **Standard**, **Premium**, **PremiumV2**, **PremiumV3** oder **Elastic Premium**
* Unterstützt TCP und UDP
* Funktioniert mit Azure App Service-Apps und Funktions-Apps

Einige Vorgänge werden von der VNet-Integration nicht unterstützt, z. B.:

* Einbindung eines Laufwerks
* Windows Server Active Directory-Integration
* NetBIOS

Die VNet-Integration mit erforderlichem Gateway ermöglicht nur den Zugriff auf Ressourcen im Ziel-VNet oder in Netzwerken, die mit dem Ziel-VNet über ein Peering oder per VPN verbunden sind. Die VNet-Integration mit erforderlichem Gateway ermöglicht keinen Zugriff auf Ressourcen, die über Azure ExpressRoute-Verbindungen verfügbar sind oder mit Dienstendpunkten arbeiten.

Unabhängig von der verwendeten Version erhält Ihre App durch die VNet-Integration Zugriff auf Ressourcen in Ihrem VNet, es wird jedoch kein eingehender privater Zugriff auf Ihre App aus dem VNet gewährt. Privater Websitezugriff bezieht sich darauf, den Zugriff auf Ihre App nur über ein privates Netzwerk zuzulassen, z. B. über ein virtuelles Azure-Netzwerk. Die VNet-Integration dient nur zum Ausführen ausgehender Aufrufe von Ihrer App an Ihr VNet.
