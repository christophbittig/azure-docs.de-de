---
title: Azure NetApp Files-Leistungsbenchmarks für Linux | Microsoft-Dokumentation
description: Beschreibt Leistungsbenchmarks, die von Azure NetApp Files für Linux bereitgestellt werden.
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
ms.date: 09/29/2021
ms.author: b-juche
ms.openlocfilehash: 814474418386041d2b20759c4fb2accc534e8b9d
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129355866"
---
# <a name="azure-netapp-files-performance-benchmarks-for-linux"></a>Azure NetApp Files-Leistungsbenchmarks für Linux

In diesem Artikel werden Leistungsbenchmarks beschrieben, die von Azure NetApp Files für Linux bereitgestellt werden.

## <a name="linux-scale-out"></a>Horizontale Skalierung in Linux

In diesem Abschnitt werden Leistungsbenchmarks für den Linux-Workloaddurchsatz und Linux-Workload-IOPS beschrieben.

### <a name="linux-workload-throughput"></a>Linux-Workloaddurchsatz  

Im folgenden Diagramm sind eine sequenzielle Workload von 64 Kibibytes (KiB) und ein Arbeitssatz mit einer Größe von 1 TiB dargestellt. Es veranschaulicht, dass ein einzelnes Azure NetApp Files-Volume zwischen ~1.600 MiB/s reine sequenzielle Schreibvorgänge und ~4.500 MiB/s reine sequenzielle Lesevorgänge verarbeiten kann.  

Aus dem Diagramm ist jeweils ein 10 %-iger Rückgang von reinen Lese- zu reinen Schreibvorgängen ersichtlich. Es demonstriert, was Sie bei Verwendung unterschiedlicher Lese-/Schreibverhältnisse erwarten können (100 %:0 %, 90 %:10 %, 80 %:20 % usw.).

![Linux-Workloaddurchsatz](../media/azure-netapp-files/performance-benchmarks-linux-workload-throughput.png)  

### <a name="linux-workload-iops"></a>Linux-Workload-IOPS  

Im folgenden Diagramm sind eine zufällige Workload von 4 Kibibytes (KiB) und ein Arbeitssatz mit einer Größe von 1 TiB dargestellt. Das Diagramm veranschaulicht, dass ein Azure NetApp Files-Volume zwischen ~130.000 reinen zufälligen Schreibvorgängen und ~460.000 reinen zufälligen Lesevorgängen verarbeiten kann.  

Aus dem Diagramm ist jeweils ein 10 %-iger Rückgang von reinen Lese- zu reinen Schreibvorgängen ersichtlich. Es demonstriert, was Sie bei Verwendung unterschiedlicher Lese-/Schreibverhältnisse erwarten können (100 %:0 %, 90 %:10 %, 80 %:20 % usw.).

![Linux-Workload-IOPS](../media/azure-netapp-files/performance-benchmarks-linux-workload-iops.png)  

## <a name="linux-scale-up"></a>Vertikale Skalierung in Linux  

Die Diagramme in diesem Abschnitt enthalten die Ergebnisse der Validierungstests für die clientseitige Bereitstellungsoption mit NFSv3. Weitere Informationen finden Sie im [Abschnitt `nconnect` der Einbindungsoptionen unter Linux](performance-linux-mount-options.md#nconnect).

In den Diagrammen werden die Vorteile von `nconnect` mit einem nicht über `connected`bereitgestellten Volume verglichen. In den Diagrammen generierte FIO die Workload aus einer einzelnen D32s_v4-Instanz in der Azure-Region „us-west2“ mit einer sequenziellen Workload von 64 KiB. Dies war zum Zeitpunkt der hier dargestellten Tests die maximale E/A-Größe, die von Azure NetApp Files unterstützt wurde. Mittlerweile unterstützt Azure NetApp Files größere E/A-Größen. Weitere Informationen finden Sie im [Abschnitt `rsize` und `wsize` der Einbindungsoptionen unter Linux](performance-linux-mount-options.md#rsize-and-wsize).

### <a name="linux-read-throughput"></a>Linux-Lesedurchsatz  

In den folgenden Diagrammen sind sequenzielle 64-KiB-Lesevorgänge von ~3.500 MiB/s mit `nconnect` dargestellt, was ungefähr dem 2,3-fachen von nicht `nconnect` entspricht.

![Linux-Lesedurchsatz](../media/azure-netapp-files/performance-benchmarks-linux-read-throughput.png)  

### <a name="linux-write-throughput"></a>Linux-Schreibdurchsatz  

Die folgenden Diagramme zeigen sequenzielle Schreibvorgänge. Sie deuten darauf hin, dass `nconnect` keinen erkennbaren Vorteil für sequenzielle Schreibvorgänge bietet. 1\.500 MiB/s ist ungefähr die Obergrenze sowohl für das sequenzielle Schreibvolumen als auch für ausgehende Daten der D32s_v4-Instanz.

![Linux-Schreibdurchsatz](../media/azure-netapp-files/performance-benchmarks-linux-write-throughput.png)  

### <a name="linux-read-iops"></a>Linux-Lese-IOPS  

In den folgenden Diagrammen sind zufällige 4-KiB-Lesevorgänge von ~200.000 Lese-IOPS mit `nconnect` dargestellt, was ungefähr dem 3-fachen von nicht `nconnect` entspricht.

![Linux-Lese-IOPS](../media/azure-netapp-files/performance-benchmarks-linux-read-iops.png)  

### <a name="linux-write-iops"></a>Linux-Schreib-IOPS  

In den folgenden Diagrammen sind zufällige 4-KiB-Schreibvorgänge von ~135.000 Schreib-IOPS mit `nconnect` dargestellt, was ungefähr dem 3-fachen von nicht `nconnect` entspricht.

![Linux-Schreib-IOPS](../media/azure-netapp-files/performance-benchmarks-linux-write-iops.png)  

## <a name="next-steps"></a>Nächste Schritte

- [Azure NetApp Files: Optimale Nutzung Ihres Cloudspeichers](https://cloud.netapp.com/hubfs/Resources/ANF%20PERFORMANCE%20TESTING%20IN%20TEMPLATE.pdf?hsCtaTracking=f2f560e9-9d13-4814-852d-cfc9bf736c6a%7C764e9d9c-9e6b-4549-97ec-af930247f22f)
