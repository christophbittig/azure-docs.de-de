---
title: 'Speicher: Bewährte Methoden und Richtlinien zur Leistung'
description: Bietet bewährte Methoden und Richtlinien für den Speicher, um die Leistung Ihrer SQL Server-Instanz auf Azure Virtual Machines (VM) zu optimieren.
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
ms.openlocfilehash: 83d47a3b1d42233df6f90690e88a898feaccb70b
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130161508"
---
# <a name="storage-performance-best-practices-for-sql-server-on-azure-vms"></a>Speicher: Bewährte Methoden zur Leistung für SQL Server auf Azure-VMs
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In diesem Artikel werden bewährte Methoden und Richtlinien zum Speicher bereitgestellt, um die Leistung für Ihre SQL Server-Instanz auf Azure Virtual Machines (VMs) zu optimieren.

In der Regel kommt es zu einem Kompromiss zwischen einer Kostenoptimierung und einer Leistungsoptimierung. Diese Folge von bewährten Methoden zur Leistung konzentriert sich darauf, die *beste* Leistung für SQL Server auf Azure Virtual Machines zu erzielen. Wenn Ihre Workload weniger anspruchsvoll ist, sind möglicherweise nicht alle empfohlenen Optimierungen erforderlich. Berücksichtigen Sie bei der Evaluierung dieser Empfehlungen Ihre Leistungsanforderungen und -kosten sowie Ihre Workloadmuster.

Weitere Informationen finden Sie in den anderen Artikeln dieser Reihe: [Checkliste](performance-guidelines-best-practices-checklist.md), [VM-Größe](performance-guidelines-best-practices-vm-size.md), [Sicherheit](security-considerations-best-practices.md), [HADR-Konfiguration](hadr-cluster-best-practices.md) und [Baseline erfassen](performance-guidelines-best-practices-collect-baseline.md). 

## <a name="checklist"></a>Checkliste

In der folgenden Prüfliste finden Sie eine kurze Übersicht über die bewährten Methoden zur Speicherung, die im weiteren Verlauf des Artikels ausführlicher behandelt werden: 

- Überwachen Sie die Anwendung, und [bestimmen Sie die Anforderungen an die Speicherbandbreite und Wartezeit](../../../virtual-machines/premium-storage-performance.md#counters-to-measure-application-performance-requirements) für SQL Server-Daten-, Protokoll- und tempdb-Dateien, bevor Sie den Datenträgertyp auswählen. 
- Um die Speicherleistung zu optimieren, planen Sie für die höchsten verfügbaren nicht zwischengespeicherten IOPS, und verwenden Sie Datenzwischenspeicherung als Leistungsmerkmal für Datenlesevorgänge, während Sie die [Begrenzung virtueller Computer und Datenträger](../../../virtual-machines/premium-storage-performance.md#throttling) vermeiden.
- Platzieren Sie Daten-, Protokoll- und tempdb-Dateien auf separaten Laufwerken.
    - Verwenden Sie für das Datenlaufwerk nur [Premium P30- und P40-Datenträger](../../../virtual-machines/disks-types.md#premium-ssds), um die Verfügbarkeit der Cacheunterstützung sicherzustellen.
    - Für das Protokolllaufwerk planen Sie die Kapazität und testen die Leistung im Verhältnis zu den Kosten, während Sie die [Premium-Datenträger P30-P80](../../../virtual-machines/disks-types.md#premium-ssds) bewerten.
      - Wenn eine Speicherwartezeit von weniger als einer Millisekunde erforderlich ist, verwenden Sie [Azure Ultra-Datenträger](../../../virtual-machines/disks-types.md#ultra-disks) für das Transaktionsprotokoll.
      - Für Bereitstellungen von virtuellen Computern der M-Serie sollten Sie eine [Schreibbeschleunigung](../../../virtual-machines/how-to-enable-write-accelerator.md) der Verwendung von Azure-Ultra-Datenträgern vorziehen.
    - Platzieren Sie [tempdb](/sql/relational-databases/databases/tempdb-database) auf das lokale kurzlebige SSD-Laufwerk `D:\` für die meisten SQL Server-Workloads, nachdem Sie die optimale VM-Größe gewählt haben. 
      - Wenn die Kapazität des lokalen Laufwerks für „tempdb“ nicht ausreicht, können Sie die Vergrößerung der VM in Erwägung ziehen. Weitere Informationen finden Sie unter [Richtlinien für das Zwischenspeichern von Datendateien](#data-file-caching-policies).
- Erstellen Sie ein Stripeset mehrerer Azure-Datenträger mithilfe von [Speicherplätzen](/windows-server/storage/storage-spaces/overview), um die E/A-Bandbreite bis zum IOPS- und Durchsatzlimit des virtuellen Zielcomputers zu erhöhen.
- Legen Sie [Hostzwischenspeicherung](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) für Datenträger mit Datendateien auf „Schreibgeschützt“ fest.
- Legen Sie [Hostzwischenspeicherung](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) für Protokolldateidatenträger auf „Keine“ fest.
    - Aktivieren Sie nicht das Zwischenspeichern von Lese-/Schreibzugriff auf Datenträgern mit SQL Server-Dateien. 
    - Vor dem Ändern der Cacheeinstellungen Ihres Datenträgers sollten Sie den SQL Server-Dienst immer anhalten.
- Für Workloads in Entwicklungs- und Testumgebungen sowie für die langfristige Archivierung von Sicherungskopien sollten Sie Standardspeicher verwenden. Es wird nicht empfohlen, Standard-HDD/SDD für Produktionsworkloads zu verwenden.
- [Auf Guthaben basierendes Datenträgerbursting](../../../virtual-machines/disk-bursting.md#credit-based-bursting) (P1-P20) sollte nur für kleinere Dev-/Test-Workloads und Abteilungssysteme in Betracht gezogen werden.
- Formatieren Sie den Datenträger, um die Blockgröße (Größe der Zuordnungseinheiten) von 64 KB für alle Datendateien zu verwenden, die auf einem anderen Laufwerk als dem temporären Laufwerk `D:\` abgelegt werden (Standardwert: 4 KB). Virtuelle SQL Server-Computer, die über Azure Marketplace bereitgestellt werden, werden mit Datenträgern geliefert, die mit der Blockgröße und dem Interleave für den Speicherpool auf 64 KB formatiert sind. 

Informationen zum Vergleichen der Prüfliste für den Speicher mit den anderen, finden Sie unter [Prüfliste für bewährte Methoden für die Leistung](performance-guidelines-best-practices-checklist.md). 

## <a name="overview"></a>Übersicht

Beginnen Sie mit der [Messung der Speicherleistung Ihrer Geschäftsanwendung](performance-guidelines-best-practices-collect-baseline.md#storage), um die effektivste Konfiguration für SQL Server-Workloads auf einer Azure-VM zu finden. Sobald die Speicheranforderungen bekannt sind, wählen Sie einen virtuellen Computer aus, der die erforderlichen IOPS und den Durchsatz mit dem entsprechenden Verhältnis von Arbeitsspeicher zu virtuellen Kernen unterstützt. 

Wählen Sie eine VM-Größe mit ausreichender Speicherskalierbarkeit für Ihre Workload und eine Mischung aus Datenträgern (normalerweise in einem Speicherpool) aus, die den Kapazitäts- und Leistungsanforderungen Ihres Unternehmens entsprechen. 

Der Typ des Datenträgers hängt sowohl von dem Dateityp ab, der auf dem Datenträger gehostet wird, als auch von Ihren Anforderungen an die Spitzenleistung.

> [!TIP]
> Die Bereitstellung einer SQL Server-VM über das Azure-Portal führt Sie durch den Speicherkonfigurationsprozess und implementiert die meisten bewährten Methoden, z. B. das Erstellen separater Speicherpools für Ihre Daten und Protokolldateien, die Ausrichtung von tempdb auf das Laufwerk `D:\` und die Aktivierung der optimalen Richtlinie für die Zwischenspeicherung. Weitere Informationen zum Bereitstellen und Konfigurieren von Speicher finden Sie unter [SQL-VM-Speicherkonfiguration](storage-configuration.md). 

## <a name="vm-disk-types"></a>VM-Datenträgertypen

Sie haben die Wahl bei der Leistungsstufe für Ihre Datenträger. Die Typen der verwalteten Datenträger, die als zugrunde liegender Speicher verfügbar sind (aufgelistet nach aufsteigender Leistungsfähigkeit), sind Standard-Festplatten (HDD), SSD Standard-, SSD Premium-Datenträger (SSD) und Ultra Disks. 

Die Leistung des Datenträgers steigt mit der Kapazität, gruppiert nach [Premium-Datenträgerbezeichnungen](../../../virtual-machines/disks-types.md#premium-ssds) wie der P1 mit 4 GiB Speicherplatz und 120 IOPS bis zum P80 mit 32 TiB Speicherplatz und 20.000 IOPS. Premium-Speicher unterstützt einen Speichercache, der die Lese- und Schreibleistung für einige Workloads verbessert. Weitere Informationen finden Sie in der [Übersicht über verwaltete Datenträger](../../../virtual-machines/managed-disks-overview.md). 

Es gibt auch drei hauptsächliche [Datenträgertypen](../../../virtual-machines/managed-disks-overview.md#disk-roles), die Sie für Ihre SQL Server-Instanz auf Azure VM in Betracht ziehen sollten – einen Datenträger für das Betriebssystem, einen temporären Datenträger und Ihre Datenträger für Daten. Wählen Sie sorgfältig aus, was auf dem Betriebssystemlaufwerk `(C:\)` und dem kurzlebigen temporären Laufwerk `(D:\)` gespeichert wird. 

### <a name="operating-system-disk"></a>Betriebssystem-Datenträger

Ein Betriebssystemdatenträger ist eine virtuelle Festplatte, die als aktive Version eines Betriebssystems gestartet und eingebunden werden kann, und die als Laufwerk `C:\` bezeichnet ist. Wenn Sie einen virtuellen Azure-Computer erstellen, wird von der Plattform mindestens ein Datenträger als Betriebssystemdatenträger an den virtuellen Computer angefügt. Das Laufwerk `C:\` ist der Standardspeicherort für Anwendungsinstallationen und Dateikonfigurationen. 

Verwenden Sie in SQL Server-Produktionsumgebungen nicht den Betriebssystemdatenträger für Datendateien, Protokolldateien und Fehlerprotokolle. 

### <a name="temporary-disk"></a>Temporärer Datenträger

Viele virtuelle Azure-Computer enthalten einen weiteren Datenträgertyp, der als temporärer Datenträger bezeichnet wird (Bezeichnung als Laufwerk `D:\`). Abhängig von der Serie und Größe des virtuellen Computers variiert die Kapazität dieses Datenträgers. Der temporäre Datenträger ist kurzlebig. Dies bedeutet, dass der Datenträgerspeicher neu erstellt wird (d. h. er wird freigegeben und erneut zugeordnet), wenn der virtuelle Computer neu gestartet oder auf einen anderen Host verschoben wird (z. B. für die [Dienstreparatur](/troubleshoot/azure/virtual-machines/understand-vm-reboot)). 

Das temporäre Speicherlaufwerk wird in Remotespeicher nicht persistent gespeichert und sollte daher keine Benutzerdatenbankdateien, Transaktionsprotokolldateien oder anderen Daten speichern, die beibehalten werden müssen. 

Legen Sie tempdb auf dem lokalen temporären SSD-Laufwerk `D:\` für SQL Server-Workloads ab, es sei denn, die Verwendung des lokalen Cache ist ein Problem. Wenn Sie einen virtuellen Computer verwenden, der [keinen temporären Datenträger](../../../virtual-machines/azure-vms-no-temp-disk.yml) aufweist, wird empfohlen, tempdb auf einem eigenen isolierten Datenträger oder in einem Speicherpool zu platzieren, wobei die Zwischenspeicherung auf „Schreibgeschützt“ festgelegt ist. Weitere Informationen finden Sie unter [Richtlinien zum Zwischenspeichern von tempdb-Daten](performance-guidelines-best-practices-storage.md#data-file-caching-policies).

### <a name="data-disks"></a>Datenträger

Bei Datenträgern für Daten handelt es sich um Remotespeicherdatenträger, die häufig in [Speicherpools](/windows-server/storage/storage-spaces/overview) erstellt werden, um die Kapazität und Leistung zu übertreffen, die ein einzelner Datenträger dem virtuellen Computer bieten könnte.

Fügen Sie die Mindestanzahl von Datenträgern an, die den IOPS-, Durchsatz- und Kapazitätsanforderungen Ihrer Workload entsprechen. Überschreiten Sie nicht die maximale Anzahl der Datenträger für Daten des kleinsten virtuellen Computers, dessen Größe Sie ändern möchten.

Legen Sie Daten und Protokolldateien auf Datenträgern für Daten ab, die den Leistungsanforderungen am besten entsprechen. 

Formatieren Sie den Datenträger, um die Größe der Zuordnungseinheiten für 64 KB für alle Datendateien zu verwenden, die auf einem anderen Laufwerk als dem temporären Laufwerk abgelegt werden `D:\` (Standardwert: 4 KB). SQL Server über Azure Marketplace bereitgestellten virtuellen Computer verfügen über Datenträger, die mit der Größe der Zuordnungseinheit formatiert sind, und Interleave für den Speicherpool auf 64 KB. 

> [!NOTE]
> Es ist auch möglich, Ihre SQL Server-Datenbankdateien direkt in [Azure Blob Storage](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure) oder im [SMB-Speicher](/sql/database-engine/install-windows/install-sql-server-with-smb-fileshare-as-a-storage-option) (z. B. [Azure Premium-Dateifreigaben](../../../storage/files/storage-how-to-create-file-share.md)) zu hosten. Es wird jedoch empfohlen, [verwaltete Azure-Datenträger](../../../virtual-machines/managed-disks-overview.md) zu verwenden, um die beste Leistung, Zuverlässigkeit und Funktionsverfügbarkeit zu erzielen.

## <a name="premium-disks"></a>Premium-Datenträger

Verwenden Sie hochwertige SSD-Datenträger für Daten und Protokolldateien für SQL Server-Workloads in der Produktionsumgebung. IOPS und Bandbreite für SSD Premium-Datenträger variieren je nach [Datenträgergröße und -typ](../../../virtual-machines/disks-types.md). 

Verwenden Sie für Produktionsworkloads die Datenträger P30 und/oder P40 für SQL Server-Datendateien, um die Unterstützung der Zwischenspeicherung sicherzustellen, und verwenden Sie die Datenträger P30 bis P80 für SQL Server-Transaktionsprotokolldateien.  Um die optimalen Gesamtkosten zu erzielen, beginnen Sie mit P30-Datenträgern (5000 IOPS/200 MBit/s) für Daten und Protokolldateien, und wählen Sie nur dann höhere Kapazitäten aus, wenn Sie die Anzahl der Datenträger des virtuellen Computers steuern müssen.

Stimmen Sie bei OLTP-Workloads die Ziel-IOPS pro Datenträger (oder Speicherpool) mit Ihren Leistungsanforderungen ab, indem Sie Workloads zu Spitzenzeiten und die Leistungsindikatoren `Disk Reads/sec` + `Disk Writes/sec` verwenden. Stimmen Sie bei Data Warehouse- und Berichtsworkloads den Zieldurchsatz anhand von Workloads zu Spitzenzeiten und den Leistungsindikatoren `Disk Read Bytes/sec` + `Disk Write Bytes/sec` ab. 

Verwenden Sie Speicherplätze, um eine optimale Leistung zu erreichen, konfigurieren Sie zwei Pools, einen für die Protokolldatei(en) und den anderen für die Datendateien. Wenn Sie kein Datenträgerstriping verwenden, sollten Sie zwei Datenträger vom Typ SSD Premium verwenden, die separaten Datenträgern zugeordnet sind, wobei sich auf einem Laufwerk die Protokolldatei und auf dem anderen die Daten befinden.

Die [bereitgestellten IOPS und der Durchsatz](../../../virtual-machines/disks-types.md#premium-ssds) pro Datenträger, die als Teil Ihres Speicherpools verwendet werden. Die kombinierte IOPS- und Durchsatzleistung der Datenträger ist die maximale Leistung bis zu den Durchsatzgrenzwerten des virtuellen Computers.

Die bewährte Methode besteht darin, die geringstmögliche Anzahl von Datenträgern zu verwenden und gleichzeitig die Mindestanforderungen an IOPS (und Durchsatz) und Kapazität zu erfüllen. Das Verhältnis von Preis und Leistung ist jedoch tendenziell besser bei einer großen Anzahl kleiner Datenträger als bei einer kleinen Anzahl großer Datenträger.

### <a name="scaling-premium-disks"></a>Skalieren von Premium-Datenträgern

Wenn ein verwalteter Azure-Datenträger zum ersten Mal bereitgestellt wird, basiert die Leistungsstufe für diesen Datenträger auf der bereitgestellten Datenträgergröße. Bestimmen Sie die Leistungsstufe bei der Bereitstellung oder ändern Sie sie nachträglich, ohne die Größe des Datenträgers zu ändern. Wenn die Nachfrage steigt, können Sie die Leistungsstufe erhöhen, um Ihre Geschäftsanforderungen zu erfüllen. 

Das Ändern der Leistungsstufe ermöglicht es Administratoren, sich auf höhere Anforderungen vorzubereiten und diese zu erfüllen, ohne sich auf das [Datenträgerbursting](../../../virtual-machines/disk-bursting.md#credit-based-bursting) zu verlassen. 

Verwenden Sie die höhere Leistung so lange wie nötig, wenn die Abrechnung auf die Speicherleistungsstufe ausgelegt ist. Führen Sie ein Upgrade der Leistungsstufe durch, um die Leistungsanforderungen zu erfüllen, ohne die Kapazität zu erhöhen. Kehren Sie zur ursprünglichen Stufe zurück, wenn die zusätzliche Leistung nicht mehr benötigt wird.

Diese kostengünstige und zeitlich begrenzte Leistungserweiterung ist ein starker Anwendungsfall für gezielte Ereignisse wie Einkaufen, Leistungstests, Schulungsveranstaltungen und andere kurze Zeitfenster, in denen nur kurzfristig mehr Leistung benötigt wird. 

Weitere Informationen finden Sie unter [Leistungsstufen für verwaltete Datenträger](../../../virtual-machines/disks-change-performance.md). 

## <a name="azure-ultra-disk"></a>Azure Ultra-Datenträger

Wenn Antwortzeiten unterhalb des Millisekundenbereichs mit reduzierter Latenz erforderlich sind, ist die Verwendung von [Azure Ultra-Datenträgern](../../../virtual-machines/disks-types.md#ultra-disks) für das SQL Server-Protokolllaufwerk oder sogar das Datenlaufwerk für Anwendungen, die extrem empfindlich auf die E/A-Latenz reagieren, zu erwägen.

Ultra Disk kann so konfiguriert werden, dass Kapazität und IOPS unabhängig voneinander skaliert werden können. Mit Ultra Disk können Administratoren einen Datenträger mit den Anforderungen an Kapazität, IOPS und Durchsatz basierend auf den Anwendungsanforderungen bereitstellen. 

Ultra Disk wird nicht von allen VM-Serien unterstützt und hat weitere Einschränkungen wie Regionsverfügbarkeit, Redundanz und Unterstützung für Azure Backup. Eine vollständige Liste der Einschränkungen finden Sie unter [Verwendung von Azure Ultra-Datenträgern](../../../virtual-machines/disks-enable-ultra-ssd.md).

## <a name="standard-hdds-and-ssds"></a>Datenträger vom Typ „HDD Standard“ und „SSD Standard“

Datenträger vom Typ [HDD Standard](../../../virtual-machines/disks-types.md#standard-hdds) und SSD Standard verfügen über unterschiedliche Latenzen und Bandbreiten und sollten nur für Dev/Test-Workloads verwendet werden. Für Produktionsworkloads sollte SSD Premium verwendet werden. Wenn Sie einen SSD Standard-Datenträger verwenden (Dev/Test-Szenarien), sollten Sie die maximale Anzahl von Datenträgern für Daten hinzufügen, die von Ihrer [VM-Größe](../../../virtual-machines/sizes.md?toc=/azure/virtual-machines/windows/toc.json) unterstützt wird, und Datenträgerstriping mit Speicherplätzen verwenden, um die beste Leistung zu erzielen.

## <a name="caching"></a>Zwischenspeicherung

Virtuelle Computer, die die Premium-Storage-Zwischenspeicherung unterstützen, können ein zusätzliches Feature namens Azure BlobCache oder Hostzwischenspeicherung nutzen, um die IOPS- und Durchsatzleistungen eines virtuellen Computers zu erweitern. Virtuelle Computer, die sowohl für Storage Premium als auch für Storage Premium-Zwischenspeicherung aktiviert sind, verfügen über diese beiden unterschiedlichen Grenzwerte für die Speicherbandbreite, die zusammen verwendet werden können, um die Speicherleistung zu verbessern.

Der IOPS- und MBit/s-Durchsatz ohne Zwischenspeicherung wird auf die Grenzwerte des Datenträgerdurchsatzes eines virtuellen Computers ohne Zwischenspeicherung angerechnet. Die maximalen Grenzwerte mit Zwischenspeicherung bieten einen zusätzlichen Puffer für Lesevorgänge, der bei Wachstum und unerwarteten Spitzenwerten hilft.

Aktivieren Sie die Premium-Zwischenspeicherung, wenn die Option unterstützt wird, um die Leistung für Lesevorgänge auf dem Datenlaufwerk ohne zusätzliche Kosten deutlich zu verbessern. 

Lese- und Schreibvorgänge auf den Azure BlobCache (zwischengespeicherte IOPS und Durchsatz) werden nicht auf die nicht zwischengespeicherten IOPS- und Durchsatzgrenzwerte des virtuellen Computers angerechnet.

> [!NOTE]
> Die Zwischenspeicherung von Datenträgern wird nicht für Datenträger mit einer Größe von 4 TiB und höher unterstützt (P50 und höher). Wenn mehrere Datenträger an Ihre VM angefügt sind, unterstützt jeder Datenträger mit 4 TiB oder weniger das Zwischenspeichern. Weitere Informationen finden Sie unter [Nutzung des Datenträgercaches](../../../virtual-machines/premium-storage-performance.md#disk-caching). 

### <a name="uncached-throughput"></a>Nicht zwischengespeicherter Durchsatz

Die maximale IOPS- und Durchsatzrate für Datenträger ohne Zwischenspeicherung ist die maximale Remotespeicherbegrenzung, die der virtuelle Computer verarbeiten kann. Dieser Grenzwert wird auf dem virtuellen Computer definiert und ist kein Grenzwert des zugrunde liegenden Datenträgerspeichers. Diese Begrenzung gilt nur für E/A bei Datenlaufwerken, die remote an den virtuellen Computer angefügt sind, nicht für die lokale E/A für das temporäre Laufwerk (Laufwerk `D:\`) oder das Betriebssystemlaufwerk.

Die für einen virtuellen Computer zur Verfügung stehende Menge der IOPS und des Durchsatzes ohne Zwischenspeicherung können Sie in der Dokumentation zu Ihrem virtuellen Computer überprüfen.

Die Dokumentation der [M-Serie](../../../virtual-machines/m-series.md) zeigt z. B., dass der maximale Durchsatz ohne Zwischenspeicherung für den virtuellen Computer vom Typ „Standard_M8ms“ 5000 IOPS und 125 MBit/s Datenträgerdurchsatz ohne Zwischenspeicherung beträgt. 

![Screenshot: Dokumentation zum Datenträgerdurchsatz ohne Zwischenspeicherung der M-Serie](./media/performance-guidelines-best-practices/m-series-table.png)

Ebenso können Sie sehen, dass der Typ „Standard_M32ts“ 20.000 Datenträger-IOPS ohne Zwischenspeicherung und 500 MBit/s Datenträgerdurchsatz ohne Zwischenspeicherung unterstützt. Diese Begrenzung wird auf der Ebene des virtuellen Computers geregelt, unabhängig von dem zugrunde liegenden Premium-Datenträgerspeicher.

Weitere Informationen finden Sie unter [Nicht zwischengespeicherte und zwischengespeicherte Grenzwerte](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits).


### <a name="cached-and-temp-storage-throughput"></a>Zwischengespeicherter und temporärer Speicherdurchsatz

Der Grenzwert für den maximalen Durchsatz von zwischengespeichertem und temporärem Speicher ist ein von dem Grenzwert für den nicht zwischengespeicherten Durchsatz des virtuellen Computers getrennter Grenzwert. Der Azure BlobCache besteht aus einer Kombination aus dem Arbeitsspeicher (RAM) des Hosts des virtuellen Computers und einer lokal angefügten SSD. Das temporäre Laufwerk (Laufwerk `D:\`) innerhalb des virtuellen Computers wird ebenfalls auf dieser lokalen SSD gehostet.

Die maximale zwischengespeicherte und temporäre Speicherdurchsatzgrenze regelt die E/A für das lokale temporäre Laufwerk (Laufwerk `D:\`) und den Azure BlobCache **nur**, wenn die Hostzwischenspeicherung aktiviert ist. 

Wenn die Zwischenspeicherung auf Storage Premium aktiviert ist, können virtuelle Computer über die Grenzen des nicht zwischengespeicherten VM-IOPS und -Durchsatzes des Remotespeichers hinaus skalieren.  

Nur bestimmte virtuelle Computer unterstützen sowohl Storage Premium als auch Storage Premium-Zwischenspeicherung (dies muss in der Dokumentation des virtuellen Computers überprüft werden). Die Dokumentation der [M-Serie](../../../virtual-machines/m-series.md) gibt z. B. an, dass sowohl Storage Premium als auch Storage Premium-Zwischenspeicherung unterstützt werden: 

![Screenshot: Storage Premium-Unterstützung der M-Serie](./media/performance-guidelines-best-practices/m-series-table-premium-support.png)

Die Grenzwerte des Caches hängen von der Größe des virtuellen Computers ab. Der virtuelle Computer vom Typ „Standard_M8ms“ unterstützt z. B. 10.000 Datenträger-IOPS mit Zwischenspeicherung und 1000 MBit/s Datenträgerdurchsatz mit Zwischenspeicherung bei einer Gesamtgröße des Caches von 793 GiB. In ähnlicher Weise unterstützt der virtuelle Computer vom Typ „Standard_M32ts“ 40.000 Datenträger-IOPS mit Zwischenspeicherung und 400 MBit/s Datenträgerdurchsatz mit Zwischenspeicherung bei einer Gesamtgröße des Caches von 3.174 GiB. 

![Screenshot: Dokumentation zum Datenträgerdurchsatz mit Zwischenspeicherung der M-Serie](./media/performance-guidelines-best-practices/m-series-table-cached-temp.png)

Sie können die Hostzwischenspeicherung auf einem vorhandenen virtuellen Computer manuell aktivieren. Beenden Sie alle Anwendungsworkloads und die SQL Server-Dienste, bevor Sie Änderungen an der Richtlinie für die Zwischenspeicherung des virtuellen Computers vornehmen. Wenn Sie eine der Cacheeinstellungen des virtuellen Computers ändern, wird der Zieldatenträger getrennt und nach der Anwendung der Einstellungen wieder angefügt.

### <a name="data-file-caching-policies"></a>Richtlinien für die Zwischenspeicherung von Datendateien

Die Richtlinie für die Zwischenspeicherung hängt von der Art der SQL Server-Datendateien ab, die auf dem Laufwerk gehostet werden. 

Die folgende Tabelle enthält eine Zusammenfassung der empfohlenen Richtlinien für die Zwischenspeicherung basierend auf dem Typ der SQL Server-Daten: 

|SQL Server-Datenträger |Empfehlung |
|---------|---------|
| **Datenträger für Daten** | Aktivieren Sie die `Read-only`-Zwischenspeicherung für die Datenträger, die SQL Server-Datendateien hosten. <br/> Die Lesevorgänge aus dem Cache sind schneller als die nicht zwischengespeicherten Lesevorgänge von dem Datenträger für Daten. <br/> IOPS und Durchsatz ohne Zwischenspeicherung sowie IOPS und Durchsatz mit Zwischenspeicherung ergeben die gesamte mögliche Leistung, die vom virtuellen Computer innerhalb der VM-Grenzwerte zur Verfügung steht, aber die tatsächliche Leistung variiert je nach der Fähigkeit der Workload, den Cache zu nutzen (Cachetrefferquote). <br/>|
|**Transaktionsprotokolldatenträger**|Legen Sie die Richtlinie für die Zwischenspeicherung für Datenträger, die das Transaktionsprotokoll hosten, auf `None` fest.  Das Aktivieren der Zwischenspeicherung für den Transaktionsprotokolldatenträger bringt keinen Leistungsvorteil, und tatsächlich kann das Aktivieren der `Read-only`- oder `Read/Write`-Zwischenspeicherung auf dem Protokolllaufwerk die Leistung der Schreibvorgänge auf dem Laufwerk verschlechtern und den Umfang des für Lesevorgänge auf dem Datenlaufwerk verfügbaren Caches verringern.  |
|**Betriebssystemdatenträger** | Die Standardrichtlinie für die Zwischenspeicherung für das Betriebssystemlaufwerk ist `Read/write`. <br/> Es wird nicht empfohlen, den Grad der Zwischenspeicherung für das Betriebssystemlaufwerk zu ändern.  |
| **tempdb**| Wenn tempdb aus Kapazitätsgründen nicht auf dem kurzlebigen Laufwerk `D:\` platziert werden kann, ändern Sie entweder die Größe des virtuellen Computers, um ein größeres kurzlebiges Laufwerk zu erhalten, oder platzieren Sie tempdb auf einem separaten Datenlaufwerk mit konfigurierter `Read-only`-Zwischenspeicherung. <br/> Der Cache des virtuellen Computers und das kurzlebige Laufwerk verwenden beide die lokale SSD. Beachten Sie dies bei der Dimensionierung, da die E/A von tempdb auf die Grenzwerte des virtuellen Computers für IOPS und den Durchsatz mit Zwischenspeicherung angerechnet werden, wenn das Hosting auf dem kurzlebigen Laufwerk erfolgt.| 
| | | 

> [!IMPORTANT]
> Durch Ändern der Cacheeinstellung eines Azure-Datenträgers wird der Zieldatenträger getrennt und erneut angefügt. Wenn Sie die Cacheeinstellung für einen Datenträger ändern, der Daten-, Protokoll- oder Anwendungsdateien für SQL Server hostet, müssen Sie unbedingt den SQL Server-Dienst sowie alle anderen verwandten Diensten beenden, um Datenbeschädigungen zu vermeiden.

Weitere Informationen finden Sie unter [Nutzung des Datenträgercaches](../../../virtual-machines/premium-storage-performance.md#disk-caching). 


## <a name="disk-striping"></a>Datenträgerstriping

Analysieren Sie den Durchsatz und die Bandbreite, die für Ihre SQL-Datendateien erforderlich sind, um die Anzahl der Datenträger für Daten, einschließlich der Protokolldatei und tempdb, zu bestimmen. Die Grenzwerte für Durchsatz und Bandbreite variieren je nach VM-Größe. Weitere Informationen finden Sie unter [VM-Größe](../../../virtual-machines/sizes.md).

Fügen Sie zusätzliche Datenträger für Daten hinzu, und verwenden Sie das Datenträgerstriping für einen höheren Durchsatz. Eine Anwendung, die 12.000 IOPS und 180 MBit/s Durchsatz erfordert, kann z. B. drei P30-Stripesetdatenträger verwenden, um 15.000 IOPS und 600 MBit/s Durchsatz zu liefern. 

Informationen zum Konfigurieren des Datenträgerstripings finden Sie unter [Datenträgerstriping](storage-configuration.md#disk-striping). 

## <a name="disk-capping"></a>Datenträgerbegrenzung 

Es gibt Durchsatzgrenzwerte sowohl auf Datenträgerebene als auch auf der Ebene des virtuellen Computers. Die maximalen IOPS-Grenzwerte pro virtuellem Computer und pro Datenträger sind unterschiedlich und unabhängig voneinander.

Anwendungen, die über diese Grenzwerte hinaus Ressourcen verwenden, werden gedrosselt (auch als „begrenzt“ bezeichnet). Wählen Sie einen virtuellen Computer und eine Datenträgergröße in einem Datenträgerstripeset aus, die den Anforderungen der Anwendung entspricht und keiner Begrenzung unterliegt. Um der Begrenzung entgegenzuwirken, verwenden Sie die Zwischenspeicherung, oder stimmen Sie die Anwendung so ab, dass weniger Durchsatz erforderlich ist.

Eine Anwendung, die 12.000 IOPS und 180 MB/s benötigt, kann z. B. wie folgt vorgehen: 
- Verwenden des Typs [Standard_M32ms](../../../virtual-machines/m-series.md), der einen maximalen Datenträgerdurchsatz ohne Zwischenspeicherung von 20.000 IOPS und 500 MBit/s aufweist.
- Stripeset von drei P30-Datenträgern, um 15.000 IOPS und 600 MBit/s Durchsatz zu erreichen.
- Verwenden Sie einen virtuellen Computer vom Typ [Standard_M16ms](../../../virtual-machines/m-series.md), und verwenden Sie die Hostzwischenspeicherung, um den lokalen Cache zu nutzen, anstatt den Durchsatz zu verbrauchen. 

Virtuelle Computer, die so konfiguriert sind, dass sie in Zeiten hoher Auslastung hochskaliert werden, sollten Speicher mit genügend IOPS und Durchsatz bereitstellen, um die maximale VM-Größe zu unterstützen, während die Gesamtzahl der Datenträger kleiner oder gleich der maximalen Anzahl ist, die von der kleinsten VM-SKU unterstützt wird, die verwendet werden soll.

Weitere Informationen zu den Einschränkungen der Datenträgerbegrenzung und zur Verwendung der Zwischenspeicherung zum Vermeiden der Begrenzung finden Sie unter [Obergrenzen der Datenträger-E/A](../../../virtual-machines/disks-performance.md).

> [!NOTE] 
> Eine gewisse Datenträgerbegrenzung kann immer noch zu einer zufriedenstellenden Leistung für die Benutzer führen. Stimmen Sie die Workloads ab und behalten Sie sie bei, anstatt die Größe der VM zu erhöhen, um ein Gleichgewicht zwischen Kosten und Leistung für das Unternehmen zu erreichen. 


## <a name="write-acceleration"></a>Schreibbeschleunigung

Die Schreibbeschleunigung ist ein Datenträgerfeature, das nur für die virtuellen Computer (VMs) der [M-Serie](../../../virtual-machines/m-series.md) verfügbar ist. Der Zweck der Schreibbeschleunigung besteht darin, die E/A-Wartezeit von Schreibvorgängen für Azure Storage Premium zu verbessern, wenn Sie aufgrund von unternehmenskritischen OLTP-Workloads mit hohem Volumen oder Data Warehouse-Umgebungen eine einstellige E/A-Wartezeit benötigen. 

Verwenden Sie die Schreibbeschleunigung, um die Schreibwartezeit für das Laufwerk zu verbessern, auf dem die Protokolldateien gespeichert sind. Verwenden Sie die Schreibbeschleunigung nicht für SQL Server-Datendateien. 

Für Datenträger mit Schreibbeschleunigung gilt derselbe IOPS-Grenzwert wie für den virtuellen Computer. Angefügte Datenträger dürfen den IOPS-Grenzwert des Schreibbeschleunigers für eine VM nicht überschreiten.  

Die folgende Tabelle bietet eine Übersicht über die pro virtuellem Computer unterstützte Anzahl der Datenträger für Daten und die IOPS: 

| VM-SKU  | Anzahl der Datenträger mit Schreibbeschleunigung  | Datenträger-IOPS der Schreibbeschleunigung pro VM  |
|---|---|---|
| M416ms_v2, M416s_v2  | 16  | 20000  |
| M128ms, M128s  | 16  | 20000  |
| M208ms_v2, M208s_v2  | 8  | 10000  |
| M64ms, M64ls, M64s  |  8 | 10000 |
| M32ms, M32ls, M32ts, M32s  | 4  | 5.000  |
| M16ms, M16s  | 2 | 2500 |
| M8ms, M8s  | 1 | 1250 |

Bei der Verwendung der Schreibbeschleunigung gibt es eine Reihe von Einschränkungen. Weitere Informationen finden Sie unter [Einschränkungen bei der Verwendung der Schreibbeschleunigung](../../../virtual-machines/how-to-enable-write-accelerator.md#restrictions-when-using-write-accelerator).


### <a name="comparing-to-azure-ultra-disk"></a>Vergleich mit Azure Ultra-Datenträger

Der größte Unterschied zwischen der Schreibbeschleunigung und Azure Ultra-Datenträgern ist, dass die Schreibbeschleunigung ein Feature für virtuelle Computer ist, das nur für die M-Serie verfügbar ist, während Azure Ultra-Datenträger eine Speicheroption darstellt. Die Schreibbeschleunigung ist ein schreiboptimierter Cache mit eigenen Einschränkungen, die auf der Größe des virtuellen Computers basieren. Azure Ultra-Datenträger sind eine Speicheroption für Datenträger mit niedriger Wartezeit für Azure Virtual Machines. 

Verwenden Sie nach Möglichkeit die Schreibbeschleunigung anstelle von Ultra-Datenträgern für den Transaktionsprotokolldatenträger. Für virtuelle Computer, die keine Schreibbeschleunigung unterstützen, aber eine geringe Wartezeit für das Transaktionsprotokoll erfordern, verwenden Sie Azure Ultra-Datenträger. 

## <a name="monitor-storage-performance"></a>Überwachen der Speicherleistung

Um die Speicheranforderungen und die Leistung des Speichers zu ermitteln, müssen Sie wissen, was zu messen ist und was diese Indikatoren bedeuten. 

[IOPS (Eingabe/Ausgabe pro Sekunde)](../../../virtual-machines/premium-storage-performance.md#iops) entspricht der Anzahl der Anforderungen, die die Anwendung pro Sekunde an den Speicher stellt. Messen Sie die IOPS mit den Leistungsmonitorindikatoren `Disk Reads/sec` und `Disk Writes/sec`. [OLTP-Anwendungen (Onlinetransaktionsverarbeitung)](/azure/architecture/data-guide/relational-data/online-transaction-processing) müssen höhere IOPS aufweisen, um eine optimale Leistung zu erzielen. Anwendungen wie Zahlungsverarbeitungssysteme, Online-Shopping und Points of Sale im Einzelhandel sind alles Beispiele für OLTP-Anwendungen.

Der [Durchsatz](../../../virtual-machines/premium-storage-performance.md#throughput) ist die Datenmenge, die an den zugrunde liegenden Speicher gesendet wird, oft gemessen in Megabyte pro Sekunde. Messen Sie den Durchsatz mit den Leistungsmonitorindikatoren `Disk Read Bytes/sec` und `Disk Write Bytes/sec`. [Data Warehousing](/azure/architecture/data-guide/relational-data/data-warehousing) ist hinsichtlich der Maximierung des Durchsatzes gegenüber IOPS optimiert. Anwendungen wie Datenspeicher für Analysen, Berichterstellung, ETL-Workstreams und andere Business Intelligence-Ziele sind alles Beispiele für Data Warehousing-Anwendungen.

Die Größe der E/A-Einheiten beeinflusst die IOPS- und Durchsatzleistung, da kleinere E/A-Größen höhere IOPS und höhere E/A-Größen einen höheren Durchsatz ergeben. SQL Server wählt automatisch die optimale E/A-Größe aus. Weitere Informationen finden Sie unter [Optimieren von IOPS, Durchsatz und Wartezeit für Ihre Anwendungen](../../../virtual-machines/premium-storage-performance.md#optimize-iops-throughput-and-latency-at-a-glance). 

Es gibt bestimmte Azure Monitor-Metriken, die von großem Nutzen sind, um die Begrenzung auf der Ebene des virtuellen Computers und der Datenträgerebene sowie den Verbrauch und die Integrität des AzureBlob-Caches zu entdecken. Informationen zum Identifizieren von wichtigen Indikatoren, die Sie zu Ihrer Überwachungslösung und dem Dashboard des Azure-Portals hinzufügen sollten, finden Sie unter [Metriken zur Speicherauslastung](../../../virtual-machines/disks-metrics.md#storage-io-utilization-metrics). 

> [!NOTE]
> Azure Monitor bietet derzeit keine Metriken auf Datenträgerebene für das kurzlebige temporäre Laufwerk `(D:\)`. „Verbrauchte von der VM zwischengespeicherte IOPS in Prozent“ und „Prozentsatz der von der VM beanspruchten zwischengespeicherten Bandbreite“ spiegeln die IOPS und den Durchsatz vom kurzlebigen temporären Laufwerk `(D:\)` und der Hostzwischenspeicherung zusammen wider.


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu bewährten Methoden für die Leistung finden Sie in den anderen Artikeln dieser Reihe:
- [Kurze Checkliste](performance-guidelines-best-practices-checklist.md)
- [Größe des virtuellen Computers](performance-guidelines-best-practices-vm-size.md)
- [Security](security-considerations-best-practices.md)
- [HADR-Einstellungen](hadr-cluster-best-practices.md)
- [Baseline auswählen](performance-guidelines-best-practices-collect-baseline.md)

Bewährte Methoden für die Sicherheit finden Sie unter [Sicherheitsüberlegungen für SQL Server auf Azure Virtual Machines](security-considerations-best-practices.md).

Ausführliche Informationen zum Testen der SQL Server-Leistung auf virtuellen Azure-Computern mit TPC-E- und TPC_C-Benchmarks finden Sie im Blog [Optimieren der OLTP-Leistung](https://techcommunity.microsoft.com/t5/sql-server/optimize-oltp-performance-with-sql-server-on-azure-vm/ba-p/916794).

Weitere Artikel zu virtuellen SQL Server-Computern finden Sie unter [Übersicht zu SQL Server auf virtuellen Azure-Computern](sql-server-on-azure-vm-iaas-what-is-overview.md). Falls Sie Fragen zu SQL Server-VMs haben, finden Sie in den [häufig gestellten Fragen](frequently-asked-questions-faq.yml) weitere Informationen.
