---
title: Einschränkungen für Azure VMware Solution
description: In diesem Artikel erhalten Sie Informationen zu Einschränkungen für Azure VMware Solution.
ms.topic: include
ms.date: 09/02/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: 081a704300fad62dbd66d602bbc65004215b20ff
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123454342"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md and concepts-networking.md -->

In der nachstehenden Tabelle werden die maximalen Grenzwerte für Azure VMware Solution beschrieben.

| **Ressource** | **Begrenzung** |
| :-- | :-- |
| Cluster pro privater Cloud | 12 |
| Mindestanzahl von Hosts pro Cluster | 3 |
| Höchstanzahl von Hosts pro Cluster | 16 |
| Hosts pro privater Cloud | 96 |
| vCenter-Instanzen pro privater Cloud | 1  |
| HCX-Standortkopplungen | 25 (beliebige Edition) |
| Maximale Anzahl verknüpfter privater AVS-ExpressRoute-Clouds | 4<br />Das verwendete Gateway für virtuelle Netzwerke bestimmt die tatsächliche maximale Anzahl von verknüpften privaten Clouds.  Weitere Informationen finden Sie unter [Informationen zu ExpressRoute-Gateways für virtuelle Netzwerke](../../expressroute/expressroute-about-virtual-network-gateways.md). | 
| AVS-ExpressRoute-Portgeschwindigkeit | 10 GBit/s<br />Das verwendete virtuelle Netzwerkgateway bestimmt die tatsächliche Bandbreite. Weitere Informationen finden Sie unter [Informationen zu ExpressRoute-Gateways für virtuelle Netzwerke](../../expressroute/expressroute-about-virtual-network-gateways.md). | 
| Über vWAN verfügbar gemachte öffentliche IPs | 100 |
| vSAN-Kapazitätsgrenzen | 75 Prozent der insgesamt nutzbaren Kapazität (25 Prozent werden für die SLA vorgehalten)  |

Verwenden Sie für andere VMware-spezifische Grenzwerte das Tool [VMware Configuration Maximum](https://configmax.vmware.com/).
