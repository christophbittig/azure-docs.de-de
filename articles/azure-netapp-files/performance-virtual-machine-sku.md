---
title: Bewährte Methoden in Bezug auf Azure-VM-SKUs für Azure NetApp Files | Microsoft-Dokumentation
description: Beschreibt bewährte Methoden für Azure NetApp Files im Hinblick auf Azure-VM-SKUs, einschließlich der Unterschiede innerhalb und zwischen SKUs.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/02/2021
ms.author: b-juche
ms.openlocfilehash: 6644a52ee094ce0076293b37fa79db28568c9c67
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/02/2021
ms.locfileid: "113233478"
---
# <a name="azure-virtual-machine-skus-best-practices-for-azure-netapp-files"></a>Bewährte Methoden in Bezug auf Azure-VM-SKUs für Azure NetApp Files

Dieser Artikel beschreibt bewährte Methoden für Azure NetApp Files im Hinblick auf Azure-VM-SKUs, einschließlich der Unterschiede innerhalb und zwischen SKUs.   

## <a name="sku-selection-considerations"></a>Überlegungen zur SKU-Auswahl

Die Speicherleistung umfasst mehr als nur die Geschwindigkeit des Speichers selbst. Die Prozessorgeschwindigkeit und -architektur spielen bei der Gesamtleistung eines bestimmten Computeknotens eine große Rolle. Im Rahmen des Auswahlprozesses für eine bestimmte SKU sollten Sie die folgenden Faktoren berücksichtigen:

* AMD oder Intel: Beispielsweise verwendet SAS eine Math Kernel Library, die speziell für Intel-Prozessoren entwickelt wurde.  In diesem Fall werden Intel-SKUs der AMD-SKU vorgezogen.
* Die Computertypen F2, E_v3 und D_v3 basieren jeweils auf mehreren Chipsätzen.  Bei Verwendung von Azure Dedicated Hosts können Sie bestimmte Modelle auswählen (z. B. Broadwell, Cascade Lake oder Skylake, wenn Sie den E-Typ auswählen). Andernfalls ist die Chipsatzauswahl nicht deterministisch.  Wenn Sie einen HPC-Cluster bereitstellen und bestandsübergreifend eine konsistente Oberfläche wichtig ist, können Sie einzelne Azure Dedicated Host-Instanzen in Betracht ziehen oder SKUs mit einem einzelnen Chipsatz verwenden, z. B. E_v4 oder D_v4.
* In Tests wurden sowohl bei den Intel Broadwell-basierten SKUs als auch bei den AMD EPYC™ 7551-basierten SKUs Leistungsschwankungen beim NAS-Speicher (Network Attached Storage) beobachtet. Es wurden zwei Probleme festgestellt:
    * Wenn die beschleunigte Netzwerkschnittstelle fälschlicherweise einem suboptimalen NUMA-Knoten zugeordnet ist, verringert sich die Leseleistung erheblich.   Für neuere SKUs ist es von Vorteil, die beschleunigte Netzwerkschnittstelle einem bestimmten NUMA-Knoten zuzuordnen, aber für SKUs mit diesen Chipsätzen (Lv2|E_v3|D_v3) ist diese Zuordnung sogar als Voraussetzung zu betrachten.
    * VMs, die auf Lv2 oder E_v3 bzw. D_v3 auf einem Broadwell-Chipsatz ausgeführt werden, sind anfälliger für Ressourcenkonflikte als bei Ausführung auf anderen SKUs.  Beim Testen anhand mehrerer VMs, die innerhalb einer einzelnen Azure Dedicated Host-Instanz ausgeführt werden, wurde festgestellt, dass sich durch die Ausführung netzwerkbasierter Speicherworkloads von einer VM aus die Leistung netzwerkbasierter Speicherworkloads verringert, die über eine zweite VM ausgeführt werden. Die Verringerung ist deutlicher, wenn die beschleunigte Netzwerkschnittstelle/der NUMA-Knoten auf einer der VMs auf dem Knoten nicht optimal zugeordnet wurde.  Beachten Sie, dass die SKUs E_v3 und D_V3 möglicherweise auf Haswell, Broadwell, Cascade Lake oder Skylake eingesetzt werden. 

Für die konsistenteste Leistung wählen Sie bei der Auswahl von VMs aus SKUs mit einem einzelnen Chipsatztyp aus. Neuere SKUs sind den älteren Modellen vorzuziehen, falls vorhanden.  Wenn Sie nicht gerade einen dedizierten Host verwenden, ist es unmöglich, richtig vorherzusagen, auf welcher Art von Hardware E_v3- oder D_v3-VMs eingesetzt werden.  Bei Verwendung der E_v3- oder D_v3-SKU gilt:

* Wenn eine VM ausgeschaltet, die Zuordnung aufgehoben und die VM dann wieder eingeschaltet wird, wechselt die VM wahrscheinlich den Host und damit auch das Hardwaremodell.
* Wenn Anwendungen auf mehreren VMs bereitgestellt werden, müssen Sie damit rechnen, dass die VMs auf heterogener Hardware ausgeführt werden.

## <a name="differences-within-and-between-skus"></a>Unterschiede innerhalb und zwischen SKUs
 
In der folgenden Tabelle werden die Unterschiede innerhalb und zwischen SKUs aufgeführt.  Beachten Sie beispielsweise, dass der Chipsatz der zugrunde liegenden E_v3- und D_v3-SKUs zwischen Broadwell, Cascade Lake, Skylake variieren kann.  

|     Familie    |     Version    |   BESCHREIBUNG     |     Frequenz (GHz)    |
|-|-|-|-|
|     E    |     V3    |     Intel® Xeon® E5-2673   v4 (Broadwell)    |     2.3 (3.6)    |
|     E    |     V3    |     Intel® Xeon®   Platinum 8272CL (Cascade Lake)    |     2.6 (3.7)    |
|     E    |     V3    |     Intel® Xeon® Platinum   8171M (Skylake)    |     2.1 (3.8)    |
|     E    |     V4    |     Intel® Xeon®   Platinum 8272CL (Cascade Lake)    |     2.6 (3.7)    |
|     Ea    |     V4    |     AMD EPYC™ 7452    |     2.35 (3.35)    |
|     D    |     V3    |     Intel® Xeon®   E5-2673 v4 (Broadwell)    |     2.3 (3.6)    |
|     D    |     V3    |     Intel® Xeon® E5-2673   v3 (Haswell)    |     2.3 (2.3)    |
|     D    |     V3    |     Intel® Xeon®   Platinum 8272CL (Cascade Lake)    |     2.6 (3.7)    |
|     D    |     V3    |     Intel® Xeon® Platinum   8171M (Skylake)    |     2.1 (3.8)    |
|     D    |     V4    |     Intel® Xeon® Platinum   8272CL (Cascade Lake)    |     2.6 (3.7)    |
|     Da    |     V4    |     AMD EPYC™ 7452    |     2.35 (3.35)    |
|     L    |     V2    |     AMD EPYC™   7551    |     2.0 (3.2)    |
|     F    |     1    |     Intel Xeon® E5-2673 v3 (Haswell)     |     2.3 (2.3)    |
|     F    |     2    |     Intel® Xeon®   Platinum 8168M (Cascade Lake)    |     2.7 (3.7)    |
|     F    |     2    |     Gen 2 Intel® Xeon® Platinum 8272CL (Skylake)    |     2.1 (3.8)   |

Wenn Sie eine SAS-GRID-Umgebung mit mehreren Knoten für die Produktion vorbereiten, stellen Sie möglicherweise eine wiederholbare Abweichung von einer Stunde und 15 Minuten zwischen den Analyseläufen fest, wobei der einzige Unterschied in der zugrunde liegenden Hardware besteht.  

|     SKU und Hardwareplattform    |     Ausführungszeiten des Auftrags    |
|-|-|
|     E32-8_v3 (Broadwell)    |     5,5 Stunden    |
|     E32-8_v3 (Cascade   Lake)    |     4,25 Stunden    |

In beiden Testsätzen wurde eine E32-8_v3-SKU ausgewählt, und RHEL 8.3 wurde zusammen mit der Bereitstellungsoption `nconnect=8` verwendet.

## <a name="best-practices"></a>Bewährte Methoden 

* Wählen Sie nach Möglichkeit die SKUs E_v4, D_v4 oder neuer anstelle der SKUs E_v3 oder D_v3 aus.  
* Wählen Sie nach Möglichkeit die SKUs Ed_v4, Dd_v4 oder neuer anstelle der SKU L2 aus.

## <a name="next-steps"></a>Nächste Schritte  

* [Bewährte Methoden in Bezug auf direkte E/A unter Linux für Azure NetApp Files](performance-linux-direct-io.md)
* [Bewährte Methoden in Bezug auf den Linux-Dateisystemcache für Azure NetApp Files](performance-linux-filesystem-cache.md)
* [Einbindungsoptionen für NFS unter Linux: bewährte Methoden für Azure NetApp Files](performance-linux-mount-options.md)
* [Best Practices für die Linux-Parallelität](performance-linux-concurrency-session-slots.md)
* [Bewährte Methoden für NFS-Read-Ahead unter Linux](performance-linux-nfs-read-ahead.md)
* [Leistungsbenchmarks für Linux](performance-benchmarks-linux.md) 
