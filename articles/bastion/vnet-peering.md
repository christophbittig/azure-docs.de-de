---
title: VNET-Peering und die Azure Bastion-Architektur
description: Hier erfahren Sie, wie Sie mit VNET-Peering und Azure Bastion zusammen eine Verbindung mit VMs herstellen.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: cherylmc
ms.openlocfilehash: ccbee86e8f5ae2ab514c8ec7ece4d41e1e890e4e
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/14/2021
ms.locfileid: "113733284"
---
# <a name="vnet-peering-and-azure-bastion"></a>VNET-Peering und Azure Bastion

Azure Bastion kann zusammen mit VNET-Peering verwendet werden. Wenn VNET-Peering konfiguriert ist, müssen Sie Azure Bastion nicht in jedem Peering-VNET bereitstellen. Wenn Sie also in einem virtuellen Netzwerk (VNET) einen Azure Bastion-Host konfiguriert haben, kann dieser zum Herstellen einer Verbindung mit VMs in einem Peering-VNET verwendet werden, ohne dass ein zusätzlicher Bastionhost bereitgestellt werden muss. Weitere Informationen zum VNET-Peering finden Sie unter [Peering in virtuellen Netzwerken](../virtual-network/virtual-network-peering-overview.md).

Azure Bastion funktioniert mit den folgenden Peeringtypen:

* **Peering virtueller Netzwerke:** Herstellen von Verbindungen zwischen virtuellen Netzwerken in derselben Azure-Region.
* **Globales Peering virtueller Netzwerke:** Herstellen von Verbindungen zwischen virtuellen Netzwerken über Azure-Regionen hinweg.

## <a name="architecture"></a>Aufbau

Wenn VNET-Peering konfiguriert ist, kann Azure Bastion in Hub-and-Spoke- oder Full-Mesh-Topologien bereitgestellt werden. Diese Bereitstellung von Azure Bastion erfolgt pro virtuellem Netzwerk und nicht pro Abonnement/Konto oder virtuellem Computer.

Nachdem Sie den Azure Bastion-Dienst in Ihrem virtuellen Netzwerk bereitgestellt haben, steht die RDP-/SSH-Funktion für alle VMs im gleichen VNET und in allen Peering-VNETs zur Verfügung. So können Sie Bastion zentral in nur einem VNET bereitstellen und trotzdem VMs erreichen, die in einem Peering-VNET bereitgestellt wurden.

:::image type="content" source="./media/vnet-peering/design.png" alt-text="Diagramm von Design und Architektur":::

In dieser Abbildung ist die Architektur einer Azure Bastion-Bereitstellung in einem Hub-and-Spoke-Modell dargestellt. Sie sehen in diesem Diagramm die folgende Konfiguration:

* Der Bastionhost wird im zentralen virtuellen Netzwerk am Hub bereitgestellt.
* Eine zentrale Netzwerksicherheitsgruppe (NSG) wird bereitgestellt.
* Für die Azure-VM ist keine öffentliche IP-Adresse erforderlich.

**Schritte:**

1. Stellen Sie in einem HTML5-Browser eine Verbindung mit dem Azure-Portal her.
2. Stellen Sie sicher, dass Sie sowohl für die Ziel-VM als auch für das VNet mit Peering über **Lesezugriff** verfügen. Überprüfen Sie außerdem unter „IAM“, ob Sie Lesezugriff auf die folgenden Ressourcen haben:
   * Rolle „Leser“ für den virtuellen Computer
   * Rolle „Leser“ für den Netzwerkadapter mit privater IP-Adresse des virtuellen Computers
   * Rolle „Leser“ für die Azure Bastion-Ressource
   * Rolle „Leser“ im virtuellen Netzwerk (nicht erforderlich, wenn kein mittels Peering verbundenes virtuelles Netzwerk vorhanden ist)
3. Damit Bastion im Dropdownmenü **Verbinden** angezeigt wird, müssen Sie unter **Subscription > global subscription** (Abonnement > Globales Abonnement) die Abonnements auswählen, auf die Sie Zugriff haben.
4. Wählen Sie die VM aus, mit der Sie eine Verbindung herstellen möchten.
5. Azure Bastion wird nahtlos über das Peering-VNET erkannt.
6. Mit nur einem Klick wird die RDP- oder SSH-Sitzung im Browser geöffnet.

  :::image type="content" source="../../includes/media/bastion-vm-rdp/connect-vm.png" alt-text="Herstellen einer Verbindung":::

   Weitere Informationen zur Verbindung mit einer VM über Azure Bastion finden Sie unter:

   * [Herstellen einer Verbindung mit einem virtuellen Windows-Computer mit Azure Bastion](bastion-connect-vm-rdp.md)
   * [Herstellen einer SSH-Verbindung mit einem virtuellen Linux-Computer über Azure Bastion](bastion-connect-vm-ssh.md)

## <a name="faq"></a>Häufig gestellte Fragen

Häufig gestellte Fragen zum VNet-Peering finden Sie unter [Häufig gestellte Fragen zu Azure Bastion](bastion-faq.md#peering).

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die [häufig gestellten Fragen zu Bastion](bastion-faq.md).
