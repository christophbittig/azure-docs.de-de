---
author: madsd
ms.service: app-service-web
ms.topic: include
ms.date: 10/01/2020
ms.author: madsd
ms.openlocfilehash: 2a95e92ce5bb6705fa42b2885c73480a9d649050
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892850"
---
* Die dedizierten Computetarife, einschließlich Basic, Standard, Premium, Premium v2 und Premium v3.
* Die App Service-Umgebung, die direkt in Ihrem virtuellen Netzwerk mit dedizierter unterstützender Infrastruktur bereitgestellt wird und die Tarife „Isoliert“ und „Isoliert v2“ nutzt.

Die VNet-Integrationsfunktion wird in dedizierten Computetarifen von App Service verwendet. In einer [App Service-Umgebung](../articles/app-service/environment/overview.md) befindet sich Ihre App bereits in einem virtuellen Netzwerk, und sie benötigt daher das VNet-Integrationsfeature nicht, um Ressourcen im selben virtuellen Netzwerk zu erreichen. Weitere Informationen zu allen Netzwerkfunktionen finden Sie unter [App Service-Netzwerkfunktionen](../articles/app-service/networking-features.md).

Die Integration virtueller Netzwerke ermöglicht Ihrer App den Zugriff auf Ressourcen in Ihrem virtuellen Netzwerk, gewährt aber keinen eingehenden privaten Zugriff auf Ihre App aus dem virtuellen Netzwerk. Privater Websitezugriff bezieht sich darauf, den Zugriff auf eine App nur über ein privates Netzwerk zuzulassen, z. B. über ein virtuelles Azure-Netzwerk. Die Integration virtueller Netzwerke wird nur für ausgehende Aufrufe aus Ihrer App an Ihr virtuelles Netzwerk verwendet. Die VNet-Integrationsfunktion verhält sich unterschiedlich, wenn sie mit virtuellen Netzwerken in derselben Region und mit virtuellen Netzwerken in anderen Regionen verwendet wird. Das Feature für die VNet-Integration umfasst zwei Varianten:

* **Integration regionaler virtueller Netzwerke**: Wenn Sie eine Verbindung mit virtuellen Netzwerken in derselben Region herstellen, benötigen Sie ein dediziertes Subnetz in dem virtuellen Netzwerk, in das Sie integrieren.
* **Integration virtueller Netzwerke mit erforderlichem Gateway**: Wenn Sie eine Verbindung mit virtuellen Netzwerken in anderen Regionen oder mit einem klassischen virtuellen Netzwerk in derselben Region direkt herstellen, benötigen Sie ein Azure Virtual Network-Gateway, das im virtuellen Zielnetzwerk erstellt wurde.

Für das Feature zur VNet-Integration gilt Folgendes:

* Erfordert einen App Service-Tarif des Typs Standard, Premium, Premium v2, Premium v3 oder Elastic Premium.
* Unterstützt TCP und UDP
* Funktioniert mit App Service-Apps und Funktions-Apps.

Einige Dinge werden von der Integration eines virtuellen Netzwerks nicht unterstützt, z. B.:

* Einbindung eines Laufwerks
* Windows Server Active Directory-Integration
* NetBIOS

Die Integration virtueller Netzwerke mit erforderlichem Gateway ermöglicht nur den Zugriff auf Ressourcen im virtuellen Zielnetzwerk oder in Netzwerken, die mit dem virtuellen Zielnetzwerk über Peering oder VPNs verbunden sind. Die Integration virtueller Netzwerke mit erforderlichem Gateway ermöglicht keinen Zugriff auf Ressourcen, die über Azure ExpressRoute-Verbindungen verfügbar sind oder mit Dienstendpunkten funktionieren.

Unabhängig von der verwendeten Version ermöglicht die Integration virtueller Netzwerke Ihrer App den Zugriff auf Ressourcen in Ihrem virtuellen Netzwerk, gewährt aber keinen eingehenden privaten Zugriff auf Ihre App aus dem virtuellen Netzwerk. Privater Websitezugriff bezieht sich darauf, den Zugriff auf Ihre App nur über ein privates Netzwerk zuzulassen, z. B. über ein virtuelles Azure-Netzwerk. Die Integration des virtuellen Netzwerks wird nur für ausgehende Aufrufe verwendet, die von Ihrer App an Ihr virtuelles Netzwerk gerichtet werden.
