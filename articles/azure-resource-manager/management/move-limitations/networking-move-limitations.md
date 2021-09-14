---
title: Verschieben von Azure-Netzwerkressourcen in ein neues Abonnement oder eine neue Ressourcengruppe
description: Verwenden Sie Azure Resource Manager, um virtuelle Netzwerke und andere Netzwerkressourcen in eine neue Ressourcengruppe oder ein neues Abonnement zu verschieben.
ms.topic: conceptual
ms.date: 08/16/2021
ms.openlocfilehash: 5a609b8c6a08136827062e0ae4107905aa1b8fe8
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122343315"
---
# <a name="move-guidance-for-networking-resources"></a>Leitfaden zum Verschieben von Netzwerkressourcen

In diesem Artikel wird beschrieben, wie Sie virtuelle Netzwerke und andere Netzwerkressourcen für bestimmte Szenarien verschieben.

Während des Verschiebens werden Ihre Netzwerkressourcen ohne Unterbrechung ausgeführt.

## <a name="dependent-resources"></a>Abhängige Ressourcen

> [!NOTE]
> Beachten Sie bitte, dass VPN-Gateways, die mit Public IP Standard SKU-Adressen verbunden sind, derzeit nicht zwischen Ressourcengruppen oder Abonnements wechseln können.

Wenn Sie eine Ressource verschieben, müssen Sie auch ihre abhängigen Ressourcen (z. B. öffentliche IP-Adressen, virtuelle Netzwerkgateways, alle zugeordneten Verbindungsressourcen) verschieben. Gateways des lokalen Netzwerks können sich in einer anderen Ressourcengruppe befinden.

Um einen virtuellen Computer mit einer Netzwerkschnittstellenkarte zu einem neuen Abonnement zu verschieben, müssen Sie alle abhängigen Ressourcen verschieben. Sie verschieben das virtuelle Netzwerk für die Netzwerkschnittstellenkarte, alle anderen Netzwerkschnittstellenkarten für das virtuelle Netzwerk und die VPN-Gateways.

Weitere Informationen finden Sie unter [Szenario für eine abonnementübergreifende Verschiebung](../move-resource-group-and-subscription.md#scenario-for-move-across-subscriptions).

## <a name="peered-virtual-network"></a>Virtuelles Netzwerk mit Peering

Um ein mittels Peering verknüpftes virtuelles Netzwerk zu verschieben, müssen Sie zunächst das Peering des virtuellen Netzwerks deaktivieren. Nach der Deaktivierung können Sie das virtuelle Netzwerk verschieben. Aktivieren Sie nach der Verschiebung das Peering des virtuellen Netzwerks wieder.

## <a name="subnet-links"></a>Subnetzverknüpfungen

Ein virtuelles Netzwerk kann nicht in ein anderes Abonnement verschoben werden, wenn das virtuelle Netzwerk ein Subnetz mit Navigationslinks für Ressourcen enthält. Beispiel: Wenn eine Azure Cache for Redis-Ressource in einem Subnetz bereitgestellt wird, verfügt dieses Subnetz über einen Navigationslink für die Ressource.

## <a name="next-steps"></a>Nächste Schritte

Befehle zum Verschieben von Ressourcen finden Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](../move-resource-group-and-subscription.md).
