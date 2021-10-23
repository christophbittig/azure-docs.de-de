---
title: 'Baseline auswählen: Bewährte Methoden und Richtlinien zur Leistung'
description: Enthält Schritte zum Auswählen einer Leistungsbaseline als Richtlinie zur Optimierung der Leistung Ihrer SQL Server-Instanz auf Azure Virtual Machine (VM).
services: virtual-machines-windows
documentationcenter: na
author: bluefooted
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.subservice: performance
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: pamela
ms.reviewer: mathoma
ms.openlocfilehash: 7c223dfd6629f315d3d25b47ffe78f709aa4b864
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130165218"
---
# <a name="collect-baseline-performance-best-practices-for-sql-server-on-azure-vm"></a>Baseline auswählen: Bewährte Methoden zur Leistung für SQL Server auf Azure-VM
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Dieser Artikel enthält eine Informationen zum Auswählen einer Leistungsbaseline sowie eine Reihe bewährter Methoden und Richtlinien zur Optimierung der Leistung für Ihre SQL Server-Instanz auf Azure Virtual Machines (VMs).

In der Regel kommt es zu einem Kompromiss zwischen einer Kostenoptimierung und einer Leistungsoptimierung. Diese Folge von bewährten Methoden zur Leistung konzentriert sich darauf, die *beste* Leistung für SQL Server auf Azure Virtual Machines zu erzielen. Wenn Ihre Workload weniger anspruchsvoll ist, sind möglicherweise nicht alle empfohlenen Optimierungen erforderlich. Berücksichtigen Sie bei der Evaluierung dieser Empfehlungen Ihre Leistungsanforderungen und -kosten sowie Ihre Workloadmuster.

## <a name="overview"></a>Übersicht

Ein ausführlicher Ansatz besteht darin, per Systemmonitor/Logman die Daten von Leistungsindikatoren zu sammeln und SQL Server-Wartestatistiken zu erfassen, um allgemeine Belastungen und potenzielle Engpässe der Quellumgebung besser zu verstehen. 

Erfassen Sie zunächst die Daten zu CPU, Arbeitsspeicher, [IOPS](../../../virtual-machines/premium-storage-performance.md#iops), [Durchsatz](../../../virtual-machines/premium-storage-performance.md#throughput) und [Latenz](../../../virtual-machines/premium-storage-performance.md#latency) der Quellworkload zu Spitzenzeiten, indem Sie die [Prüfliste für Anforderungen an die Anwendungsleistung](../../../virtual-machines/premium-storage-performance.md#application-performance-requirements-checklist) verwenden. 

Erfassen Sie Daten während der Spitzenzeiten, z. B. von Workloads eines typischen Geschäftstags, aber auch andere Prozesse mit hoher Auslastung, z. B. Verarbeitungsabläufe am Ende des Tages und ETL-Workloads an Wochenenden. Erwägen Sie, Ihre Ressourcen für außergewöhnlich hohe Workloads, z. B. die Verarbeitung für den Quartalsabschluss, hochzuskalieren und nach Abarbeitung der Workload dann wieder herunterzuskalieren. 

Verwenden Sie die Leistungsanalyse, um die [VM-Größe](../../../virtual-machines/sizes-memory.md) auszuwählen, mit der die erforderliche Skalierung für die Leistungsanforderungen der Workload möglich ist.


## <a name="storage"></a>Storage

Die Leistung von SQL Server hängt stark vom E/A-Subsystem ab und die Speicherleistung wird anhand von IOPS und Durchsatz gemessen. Sofern Ihre Datenbank nicht in den physischen Speicher passt, werden Datenbankseiten von SQL Server ständig in den Pufferpool hinein und wieder heraus verschoben. Die Datendateien für SQL Server sollten anders behandelt werden. Der Zugriff auf Protokolldateien wird sequenziell durchgeführt. Wenn für eine Transaktion aber ein Rollback ausgeführt werden muss, wird auf die Datendateien, einschließlich „tempdb“, nach dem Zufallsprinzip zugegriffen. Falls Sie über ein langsames E/A-Subsystem verfügen, kann es für Ihre Benutzer zu Leistungsproblemen kommen, z. B. zu langen Reaktionszeiten und Fehlern bei Aufgaben aufgrund von Timeouts. 

Die virtuellen Azure Marketplace-Computer verfügen über Protokolldateien auf einem physischen Datenträger, der standardmäßig getrennt von den Datendateien vorhanden ist. Anzahl und Größe der tempdb-Datendateien sind an den bewährten Methoden ausgerichtet und zielen auf das kurzlebige Laufwerk `D:\` ab. 

Die folgenden Leistungsindikatoren des Systemmonitors können als Hilfe beim Überprüfen des E/A-Durchsatzes dienen, der für Ihre SQL Server-Instanz erforderlich ist: 
* **\LogicalDisk\Disk Reads/Sec** (Lese-IOPS)
* **\LogicalDisk\Disk Writes/Sec** (Schreib-IOPS) 
* **\LogicalDisk\Disk Read Bytes/Sec** (Durchsatzanforderungen für Lesevorgänge für die Daten-, Protokoll- und tempdb-Dateien)
* **\LogicalDisk\Disk Write Bytes/Sec** (Durchsatzanforderungen für Schreibvorgänge für die Daten-, Protokoll- und tempdb-Dateien)

Evaluieren Sie anhand der IOPS- und Durchsatzanforderungen zu Spitzenzeiten die VM-Größen, deren Kapazität zu Ihren Messwerten passt. 

Wenn Ihre Workload 20 K Lese-IOPS und 10 K Schreib-IOPS zu Spitzenzeiten erfordert, können Sie entweder E16s_v3 (mit bis zu 32 K zwischengespeicherten und 25.600 nicht zwischengespeicherten IOPS) oder M16_s (mit bis zu 20 K zwischengespeicherten und 10 K nicht zwischengespeicherten IOPS) mit zwei M30-Datenträgern mit „Speicherplätze“ auswählen. 

Stellen Sie sicher, dass Sie sowohl die Durchsatz- als auch die IOPS-Anforderungen der Workload verstehen, da VMs unterschiedliche Skalierungsgrenzen für IOPS und Durchsatz aufweisen.

## <a name="memory"></a>Arbeitsspeicher

Verfolgen Sie sowohl den vom Betriebssystem verwendeten externen Arbeitsspeicher als auch den von SQL Server intern verwendeten Arbeitsspeicher. Wenn Sie die Belastung für eine der Komponenten ermitteln, können Sie die Größe der virtuellen Computer leichter auswählen und Optimierungsmöglichkeiten identifizieren. 

Die folgenden Leistungsindikatoren des Systemmonitors können Ihnen als Hilfe beim Überprüfen der Speicherintegrität eines virtuellen SQL Server-Computers dienen: 
* \Arbeitsspeicher\Verfügbare MB
* [\SQLServer:Speicher-Manager\Zielserverspeicher (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer:Speicher-Manager\Serverspeicher gesamt (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [SQLServer:Puffer-Manager\Verzögerte Schreibvorgänge/Sekunde](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer:Puffer-Manager\Seitenlebenserwartung](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)

## <a name="compute"></a>Compute

Die Computeverwaltung wird in Azure anders als in einer lokalen Umgebung durchgeführt. Lokale Server sind auf mehrere Jahre ohne Upgrade ausgelegt. Dies liegt am Mehraufwand für die Verwaltung und an den Kosten für die Beschaffung neuer Hardware. Durch die Virtualisierung werden einige dieser Probleme beseitigt, aber die Anwendungen sind so optimiert, dass die zugrunde liegende Hardware bestmöglich genutzt wird. Dies bedeutet, dass bei jeder größeren Änderung des Ressourcenverbrauchs die gesamte physische Umgebung neu abgestimmt werden muss. 

In Azure ist dies nicht schwierig, weil ein neuer virtueller Computer auf anderer Hardware – oder sogar in einer anderen Region – leicht eingerichtet werden kann. 

In Azure besteht das Ziel darin, so viele VM-Ressourcen wie möglich zu nutzen. Daher sollten virtuelle Azure-Computer so konfiguriert sein, dass die durchschnittliche CPU-Auslastung so hoch wie möglich ist, ohne dass dadurch die Workload beeinträchtigt wird. 

Die folgenden Leistungsindikatoren des Systemmonitors können Ihnen als Hilfe beim Überprüfen der Computeintegrität eines virtuellen SQL Server-Computers dienen:
* **\Processor Information(_Total)\% Processor Time**
* **\Process(sqlservr)\% Processor Time**

> [!NOTE] 
> Idealerweise sollten Sie versuchen, 80 % Ihrer Computeleistung zu nutzen, während die Spitzenauslastung oberhalb von 90 % liegen kann, ohne längere Zeit 100 % zu erreichen. Im Grunde genommen sollten Sie nur die Computeleistung bereitstellen, die von der Anwendung benötigt wird, und dann planen, je nach den geschäftlichen Anforderungen hoch- bzw. herunterzuskalieren. 


## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich auch die anderen Artikel in dieser Reihe an, um mehr zu erfahren:
- [Kurze Checkliste](performance-guidelines-best-practices-checklist.md)
- [Größe des virtuellen Computers](performance-guidelines-best-practices-vm-size.md)
- [Storage](performance-guidelines-best-practices-storage.md)
- [Security](security-considerations-best-practices.md)
- [HADR-Einstellungen](hadr-cluster-best-practices.md)


Bewährte Methoden für die Sicherheit finden Sie unter [Sicherheitsüberlegungen für SQL Server auf Azure Virtual Machines](security-considerations-best-practices.md).

Weitere Artikel zu virtuellen SQL Server-Computern finden Sie unter [Übersicht zu SQL Server auf virtuellen Azure-Computern](sql-server-on-azure-vm-iaas-what-is-overview.md). Falls Sie Fragen zu SQL Server-VMs haben, finden Sie in den [häufig gestellten Fragen](frequently-asked-questions-faq.yml) weitere Informationen.
