---
title: Hinweis zur Datenträgerpoolplanung für VNet
description: Wichtiger Hinweis dazu, wie wichtig es ist, ein VNet in geringerer Entfernung zu Azure VMware Solution-Hosts bereitzustellen.
ms.topic: include
ms.date: 07/14/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 04bd1b8b4faf697084dad49fa927394deaad3572
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638759"
---
Wenn Sie planen, Ihre Azure VMware Solution-Hosts mithilfe von [Azure-Datenträgerpools](../../virtual-machines/disks-pools.md) zu skalieren, ist es äußerst wichtig, das VNet in der Nähe Ihrer Hosts mit einem virtuellen ExpressRoute-Netzwerkgateway bereitzustellen. Je näher der Speicher sich an Ihren Hosts befindet, desto besser ist die Leistung.
