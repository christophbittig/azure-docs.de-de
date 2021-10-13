---
title: Azure VMware Solution-Netzwerkbetrieb und -Konnektivität
description: Beschreibung von Azure VMware Solution-Netzwerkbetrieb und -Konnektivität
ms.topic: include
ms.date: 08/10/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 0ee52f437027690787ca5dc95eda2705b796b604
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638639"
---
<!-- Used in introduction.md and concepts-networking.md -->

Azure VMware Solution stellt eine private Cloudumgebung bereit, auf die aus lokalen und Azure-basierten Ressourcen aus zugegriffen werden kann. Die Konnektivität wird über Dienste wie Azure ExpressRoute, über VPN-Verbindungen oder über Azure Virtual WAN bereitgestellt. Diese Dienste benötigen jedoch bestimmte Netzwerkadressbereiche und Firewallports für die Aktivierung der Dienste.

Wenn eine private Cloud bereitgestellt wird, werden private Netzwerke für Verwaltung, Bereitstellung und vMotion erstellt. Diese privaten Netzwerke werden verwendet, um auf vCenter und NSX-T Manager sowie auf vMotion oder die Bereitstellung virtueller Computer zuzugreifen.

[!INCLUDE [expressroute-global-reach](expressroute-global-reach.md)]

In der privaten Cloud bereitgestellte virtuelle Computer sind vom Internet aus über die [öffentliche IP-Adresse von Azure Virtual WAN](../enable-public-internet-access.md) zugänglich. Bei neuen privaten Clouds ist der Internetzugriff standardmäßig deaktiviert. 



