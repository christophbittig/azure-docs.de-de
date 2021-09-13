---
title: Azure VMware Solution-Netzwerkbetrieb und -Konnektivität
description: Beschreibung von Azure VMware Solution-Netzwerkbetrieb und -Konnektivität
ms.topic: include
ms.date: 08/10/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: d758d42ff1e5c77ddd1e17811e2eea24717878aa
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122070723"
---
<!-- Used in introduction.md and concepts-networking.md -->

Azure VMware Solution stellt eine private Cloudumgebung bereit, auf die aus lokalen und Azure-basierten Ressourcen aus zugegriffen werden kann. Die Konnektivität wird über Dienste wie Azure ExpressRoute, über VPN-Verbindungen oder über Azure Virtual WAN bereitgestellt. Diese Dienste benötigen jedoch bestimmte Netzwerkadressbereiche und Firewallports für die Aktivierung der Dienste.

Wenn eine private Cloud bereitgestellt wird, werden private Netzwerke für Verwaltung, Bereitstellung und vMotion erstellt. Diese privaten Netzwerke werden verwendet, um auf vCenter und NSX-T Manager sowie auf vMotion oder die Bereitstellung virtueller Computer zuzugreifen.

[!INCLUDE [expressroute-global-reach](expressroute-global-reach.md)]

In der privaten Cloud bereitgestellte virtuelle Computer sind vom Internet aus über die [öffentliche IP-Adresse von Azure Virtual WAN](../enable-public-internet-access.md) zugänglich. Bei neuen privaten Clouds ist der Internetzugriff standardmäßig deaktiviert. 



