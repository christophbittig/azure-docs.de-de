---
title: Benchmarktests für Ihre Anwendung in Azure Disk Storage
description: Erhalten Sie Informationen zu Benchmarktests für Ihre Anwendung in Azure.
author: roygara
ms.author: rogarana
ms.date: 06/29/2021
ms.topic: how-to
ms.service: storage
ms.subservice: disks
ms.openlocfilehash: c57047cdaf848bc27805d8819be515636d492676
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122687551"
---
# <a name="benchmark-a-disk"></a>Durchführen von Vergleichstest für einen Datenträger

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Bei Benchmarktests werden verschiedene Workloads Ihrer Anwendung simuliert und die Anwendungsleistung für jeden Workload gemessen. Unter Befolgung der in dem [Artikel „Entwerfen für hohe Leistung“](premium-storage-performance.md) beschriebenen Schritte haben Sie die Leistungsanforderungen der Anwendung gesammelt. Durch Ausführen von Benchmarktools in den VMs, die die Anwendung hosten, können Sie die Leistungsgrade bestimmen, die Ihre Anwendung mit SSD Premium-Datenträgern erreichen kann. In diesem Artikel bieten wir Beispiele für Benchmarktests einer Standard_D8ds_v4-VM, die mit SSD Premium-Datenträgern von Azure bereitgestellt wurde.

Wir haben für Windows und Linux gängige Benchmarktools wie DiskSpd und FIO verwendet. Diese Tools erzeugen mehrere Threads zum Simulieren eines produktionsähnlichen Workloads und Messen der Systemleistung. Mithilfe der Tools können Sie auch Parameter wie Blockgröße und Warteschlangenlänge konfigurieren, die Sie normalerweise nicht für eine Anwendung ändern können. Dadurch können Sie flexibler die Maximalleistung einer Hochleistungs-VM ermitteln, die für verschiedene Typen von Anwendungsworkloads mit SSD Premium-Datenträgern bereitgestellt ist. Weitere Informationen zu den einzelnen Benchmarktools finden Sie unter [DiskSpd](https://github.com/Microsoft/diskspd/wiki/) und [FIO](http://freecode.com/projects/fio).

Um die folgenden Beispiele zu befolgen, erstellen Sie eine Standard_D8ds_v4-VM und fügen Sie vier SSD Premium-Datenträger an die VM an. Konfigurieren Sie drei der vier Datenträger mit der Hostcache-Einstellung „None“, und fügen Sie sie einem Stripeset mit dem Namen „NoCacheWrites“ hinzu. Konfigurieren Sie auf dem verbleibenden Datenträger die Hostcache-Einstellung „ReadOnly“, und erstellen Sie ein Volume mit dem Namen „CacheReads“ mit diesem Datenträger. Mithilfe dieser Einrichtung können Sie die maximale Lese- und Schreibleistung einer Standard_D8ds_v4-VM ermitteln. Ausführliche Schritte zum Erstellen einer Standard_D8ds_v4-VM mit SSD Premium-Datenträgern finden Sie unter [Entwerfen für hohe Leistung](premium-storage-performance.md).

[!INCLUDE [virtual-machines-disks-benchmarking](../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie als nächstes unseren Artikel [Entwerfen für hohe Leistung](premium-storage-performance.md).

In diesem Artikel erstellen Sie eine Prüfliste ähnlich derjenigen für die vorhandene Anwendung für den Prototyp. Mithilfe von Benchmarktools können Sie die Workloads simulieren und die Leistung der Prototypanwendung messen. Auf diese Weise können Sie bestimmen, mit welchem Datenträgerangebot die Leistungsanforderungen Ihrer Anwendung erfüllt oder gar übertroffen werden. Anschließend können Sie dieselben Richtlinien für Ihre Produktionsanwendung implementieren.
