---
title: Azure Virtual Network – Konzepte und bewährte Methoden
description: Erfahren Sie mehr über Azure Virtual Network-Konzepte und bewährte Methoden.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/03/2020
ms.author: kumud
ms.openlocfilehash: be9bae5d67b7ce341e7444a183788ab1f158ce72
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130217447"
---
# <a name="azure-virtual-network-concepts-and-best-practices"></a>Azure Virtual Network – Konzepte und bewährte Methoden

Dieser Artikel beschreibt die wichtigsten Konzepte und bewährten Methoden für Azure Virtual Network (VNet).

## <a name="vnet-concepts"></a>VNET-Konzepte

- **Adressraum:** Beim Erstellen eines VNET müssen Sie einen benutzerdefinierten privaten IP-Adressraum mit öffentlichen und privaten Adressen (RFC 1918) eingeben. Azure weist Ressourcen in einem virtuellen Netzwerk eine private IP-Adresse aus dem von Ihnen zugewiesenen Adressraum zu. Wenn Sie beispielsweise einen virtuellen Computer in einem VNET mit dem Adressraum 10.0.0.0/16 bereitstellen, wird er einer privaten IP-Adresse zugewiesen, z. B. 10.0.0.4.
- **Subnetze:** Mithilfe von Subnetzen können Sie das virtuelle Netzwerk in ein oder mehrere Subnetze segmentieren und jedem Subnetz einen Teil des Adressraums des virtuellen Netzwerks zuordnen. Dann können Sie Azure-Ressourcen in einem bestimmten Subnetz bereitstellen. Wie in einem herkömmlichen Netzwerk können Sie mit Subnetzen Ihren VNET-Adressraum in Segmente unterteilen, die für das interne Netzwerk des Unternehmens geeignet sind. Dadurch wird auch die Adresszuordnung optimiert. Sie können Ressourcen in Subnetzen mit Netzwerksicherheitsgruppen sichern. Weitere Informationen finden Sie unter [Netzwerksicherheitsgruppen](./network-security-groups-overview.md).
- **Regionen:** Ein VNET ist auf eine Region oder einen Standort begrenzt. Mehrere virtuelle Netzwerke aus verschiedenen Regionen können jedoch über Peering virtueller Netzwerke miteinander verbunden werden.
- **Abonnement:** Ein VNET ist auf ein Abonnement begrenzt. Sie können in jedem Azure-[Abonnement](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) und in jeder Azure-[Region](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region) mehrere virtuelle Netzwerke implementieren.

## <a name="best-practices"></a>Bewährte Methoden

Beim Erstellen Ihres Netzwerks in Azure sind folgende allgemeine Entwurfsprinzipien zu beachten:

- Stellen Sie sicher, dass sich Adressräume nicht überschneiden. Stellen Sie sicher, dass der Adressraum Ihres VNET (CIDR-Block) sich nicht mit anderen Netzwerkbereichen Ihrer Organisation überschneidet.
- Die Subnetze sollten nicht den gesamten Adressraum des VNET ausmachen. Planen Sie voraus, und reservieren Sie Adressraum für die Zukunft.
- Es empfiehlt sich, einige große VNETs anstelle vieler kleiner VNETs zu erstellen. Dadurch verringert sich der Verwaltungsaufwand.
- Schützen Sie Ihre VNets, indem Sie den untergeordneten Subnetzen Netzwerksicherheitsgruppen (NSGs) zuweisen. Weitere Informationen zu Netzwerksicherheitskonzepten finden Sie unter [Übersicht über die Netzwerksicherheit in Azure](../security/fundamentals/network-overview.md).

## <a name="next-steps"></a>Nächste Schritte

 Erstellen Sie als Nächstes ein virtuelles Netzwerk, stellen Sie einige virtuelle Computer darin bereit, und kommunizieren Sie zwischen den virtuellen Computern, um sich mit der Verwendung virtueller Netzwerke vertraut zu machen. Eine entsprechende Anleitung finden Sie unter [Erstellen eines virtuellen Netzwerks im Azure-Portal](quick-create-portal.md).
