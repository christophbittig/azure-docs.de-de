---
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 07/22/2021
ms.author: allensu
ms.openlocfilehash: 03080e4518fdc1997740dd156135202dfc24f778
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2021
ms.locfileid: "122179874"
---
> [!NOTE]
> Azure stellt eine Standard-IP-Adresse für den ausgehenden Zugriff für Azure Virtual Machines-Instanzen bereit, denen keine öffentliche IP-Adresse zugewiesen ist oder die sich im Back-End-Pool einer Azure Load Balancer-Instanz vom Typ „Basic“ befinden. Der Mechanismus für Standard-IP-Adressen für den ausgehenden Zugriff stellt eine ausgehende IP-Adresse bereit, die nicht konfigurierbar ist. 
>
>Die Standard-IP-Adresse für den ausgehenden Zugriff wird deaktiviert, wenn dem virtuellen Computer eine öffentliche IP-Adresse zugewiesen wird oder wenn der virtuelle Computer im Back-End-Pool eines Load Balancer Standard-Instanz mit oder ohne Ausgangsregeln platziert wird. Wird dem Subnetz des virtuellen Computers eine Gatewayressource vom Typ [Azure Virtual Network NAT](../articles/virtual-network/nat-gateway/nat-overview.md) zugewiesen, wird die Standard-IP-Adresse für den ausgehenden Zugriff deaktiviert.
>
> Weitere Informationen zu ausgehenden Verbindungen in Azure finden Sie unter [Verwenden von SNAT für ausgehende Verbindungen](../articles/load-balancer/load-balancer-outbound-connections.md).