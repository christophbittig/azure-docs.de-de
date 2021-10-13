---
title: Datenträgerfunktionen der Hosts
description: Hosts, die zum Erstellen oder Skalieren von Clustern verwendet werden, stammen aus einem isolierten Hostpool.
ms.topic: include
ms.date: 04/23/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 29a16c506d4e381e25c9c12a424b3e1fba7ad08d
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638514"
---
<!-- Used in plan-private-cloud-deployment.md and concepts-private-cloud-clusters.md -->


Azure VMware Solution-Cluster basieren auf einer hyperkonvergenten Bare-Metal-Infrastruktur. Die RAM-, CPU- und Datenträgerkapazitäten des Hosts sind in der folgenden Tabelle angegeben.

| Hosttyp | CPU   | RAM (GB)  | vSAN NVMe-Cacheebene (TB, Raw)  | vSAN SSD-Kapazitätsebene (TB, Raw)  |
| :---      | :---: | :---:     | :---:                           | :---:                             |
| AV36     |  Intel Dual Core, 18 Kerne 2,3 GHz  |     576      |                3.2               |                15,20               |

Hosts, die zum Erstellen oder Skalieren von Clustern verwendet werden, stammen aus einem isolierten Hostpool. Diese Hosts wurden Hardwaretests unterzogen. Darüber hinaus wurden alle Daten sicher gelöscht. 