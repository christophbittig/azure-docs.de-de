---
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 07/22/2021
ms.author: allensu
ms.openlocfilehash: 35b4f49638d64a1de322fc712602a2d6cbad290a
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130019065"
---
> [!NOTE]
> Azure stellt eine Standard-IP-Adresse für den ausgehenden Zugriff für Azure Virtual Machines-Instanzen bereit, denen keine öffentliche IP-Adresse zugewiesen ist oder die sich im Back-End-Pool einer Azure Load Balancer-Instanz vom Typ „Basic“ befinden. Der Mechanismus für Standard-IP-Adressen für den ausgehenden Zugriff stellt eine ausgehende IP-Adresse bereit, die nicht konfigurierbar ist. 
>
> Weitere Informationen zum ausgehenden Standardzugriff finden Sie unter [Standardzugriff in ausgehender Richtung in Azure](../articles/virtual-network/ip-services/default-outbound-access.md).
>
>Die IP-Adresse für den ausgehenden Standardzugriff wird deaktiviert, wenn dem virtuellen Computer eine öffentliche IP-Adresse zugewiesen wird oder wenn der virtuelle Computer im Back-End-Pool einer Load Balancer Standard-Instanz mit oder ohne Ausgangsregeln platziert wird. Wird dem Subnetz des virtuellen Computers eine Gatewayressource vom Typ [Azure Virtual Network NAT](../articles/virtual-network/nat-gateway/nat-overview.md) zugewiesen, wird die Standard-IP-Adresse für den ausgehenden Zugriff deaktiviert.
>
> Virtuelle Computer, die von VM-Skalierungsgruppen im Orchestrierungsmodus „Flexibel“ erstellt werden, haben keinen ausgehenden Standardzugriff.
>
> Weitere Informationen zu ausgehenden Verbindungen in Azure finden Sie unter [Verwendung von SNAT (Source Network Address Translation) für ausgehende Verbindungen](../articles/load-balancer/load-balancer-outbound-connections.md).