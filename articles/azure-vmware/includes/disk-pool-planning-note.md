---
title: Hinweis zur Datenträgerpoolplanung für VNet
description: Wichtiger Hinweis dazu, wie wichtig es ist, ein VNet in geringerer Entfernung zu Azure VMware Solution-Hosts bereitzustellen.
ms.topic: include
ms.date: 07/14/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: ddee39189fd93d5651e8e879375b977a4222e9fc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729054"
---
Wenn Sie planen, Ihre Azure VMware Solution-Hosts mithilfe von [Azure-Datenträgerpools](../../virtual-machines/disks-pools.md) zu skalieren, ist es äußerst wichtig, das VNet in der Nähe Ihrer Hosts mit einem virtuellen ExpressRoute-Netzwerkgateway bereitzustellen. Je näher der Speicher sich an Ihren Hosts befindet, desto besser ist die Leistung.
