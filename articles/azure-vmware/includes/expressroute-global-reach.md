---
title: Azure VMware Solution-Netzwerkbetrieb und -Konnektivität
description: Beschreibung von Azure VMware Solution-Netzwerkbetrieb und -Konnektivität
ms.topic: include
ms.date: 08/10/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: add146bb5b64e12b09bec89c080e1222154d4e2f
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638519"
---
<!-- Used in articles\azure-vmware\introduction.md and articles\azure-vmware\concepts-networking.md 

articles\azure-vmware\includes\azure-vmware-solution-networking-description.md

-->

[ExpressRoute Global Reach](../../expressroute/expressroute-global-reach.md) wird verwendet, um private Clouds mit lokalen Umgebungen zu verbinden. Es verbindet die Leitungen direkt auf der MSEE-Ebene (Microsoft Enterprise Edge). Die Verbindung erfordert ein virtuelles Netzwerk (VNet) mit einer ExpressRoute-Leitung zur lokalen Umgebung in Ihrem Abonnement.  Der Grund dafür: VNet-Gateways (ExpressRoute-Gateways) können keinen Datenverkehr übertragen. Das bedeutet, dass Sie zwar zwei Verbindungen an das gleiche Gateway anfügen können, der Datenverkehr aber nicht von einer Verbindung an die andere gesendet wird.

Jede Azure VMware Solution-Umgebung ist eine eigene ExpressRoute-Region (ein eigenes virtuelles MSEE-Gerät), mit der Sie Global Reach mit dem „lokalen“ Peeringstandort verbinden können.  Es ermöglicht Ihnen, mehrere Azure VMware Solution-Instanzen in einer Region mit demselben Peeringstandort zu verbinden. 

>[!NOTE]
>Für Standorte, an denen ExpressRoute Global Reach nicht aktiviert ist (beispielsweise aufgrund örtlicher Bestimmungen), muss eine Routinglösung mit Azure-IaaS-VMs erstellt werden. Unter [AzureCAT-AVS/networking](https://github.com/Azure/AzureCAT-AVS/tree/main/networking) finden Sie einige Beispiele.

