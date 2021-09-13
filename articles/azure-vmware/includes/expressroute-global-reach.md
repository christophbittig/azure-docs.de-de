---
title: Azure VMware Solution-Netzwerkbetrieb und -Konnektivität
description: Beschreibung von Azure VMware Solution-Netzwerkbetrieb und -Konnektivität
ms.topic: include
ms.date: 08/10/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: 569beec244e2ccf80aae3e4bb3f7a3e2cf65980a
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122070724"
---
<!-- Used in articles\azure-vmware\introduction.md and articles\azure-vmware\concepts-networking.md 

articles\azure-vmware\includes\azure-vmware-solution-networking-description.md

-->

[ExpressRoute Global Reach](../../expressroute/expressroute-global-reach.md) wird verwendet, um private Clouds mit lokalen Umgebungen zu verbinden. Die Verbindungen werden direkt auf MSEE-Ebene (Microsoft Enterprise Edge) hergestellt, sodass für die Verbindung ein virtuelles Netzwerk (VNet) mit ExpressRoute-Verbindung mit der lokalen Umgebung in Ihrem Abonnement erforderlich ist.  Der Grund dafür: VNet-Gateways (ExpressRoute-Gateways) können keinen Datenverkehr übertragen. Das bedeutet, dass Sie zwar zwei Verbindungen an das gleiche Gateway anfügen können, der Datenverkehr aber nicht von einer Verbindung an die andere gesendet wird.

>[!NOTE]
>Für Standorte, an denen ExpressRoute Global Reach nicht aktiviert ist (beispielsweise aufgrund örtlicher Bestimmungen), muss eine Routinglösung mit Azure-IaaS-VMs erstellt werden. Unter [AzureCAT-AVS/networking](https://github.com/Azure/AzureCAT-AVS/tree/main/networking) finden Sie einige Beispiele.

