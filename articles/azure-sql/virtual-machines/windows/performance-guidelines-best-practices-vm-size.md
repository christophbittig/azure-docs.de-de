---
title: 'VM-Größe: Bewährte Methoden und Richtlinien zur Leistung'
description: Bietet bewährte Methoden und Richtlinien für die VM-Größe, um die Leistung Ihrer SQL Server-Instanz auf Azure Virtual Machines (VM) zu optimieren.
services: virtual-machines-windows
documentationcenter: na
author: bluefooted
editor: ''
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: performance
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: dpless
ms.reviewer: pamela
ms.openlocfilehash: c80cf00bf7beb98b8548f0eebfd1cfa241bd2136
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130161469"
---
# <a name="vm-size-performance-best-practices-for-sql-server-on-azure-vms"></a>VM-Größe: Bewährte Methoden zur Leistung für SQL Server auf Azure-VMs
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Dieser Artikel enthält eine Anleitung zur VM-Größe sowie eine Reihe bewährter Methoden und Richtlinien zur Optimierung der Leistung für Ihre SQL Server-Instanz auf Azure Virtual Machines (VMs).

In der Regel kommt es zu einem Kompromiss zwischen einer Kostenoptimierung und einer Leistungsoptimierung. Diese Folge von bewährten Methoden zur Leistung konzentriert sich darauf, die *beste* Leistung für SQL Server auf Azure Virtual Machines zu erzielen. Wenn Ihre Workload weniger anspruchsvoll ist, sind möglicherweise nicht alle empfohlenen Optimierungen erforderlich. Berücksichtigen Sie bei der Evaluierung dieser Empfehlungen Ihre Leistungsanforderungen und -kosten sowie Ihre Workloadmuster.

Ausführliche Informationen finden Sie in den anderen Artikeln dieser Reihe: [Checkliste](performance-guidelines-best-practices-checklist.md), [Speicher](performance-guidelines-best-practices-storage.md), [Sicherheit](security-considerations-best-practices.md), [HADR-Konfiguration](hadr-cluster-best-practices.md) und [Baseline erfassen](performance-guidelines-best-practices-collect-baseline.md). 


## <a name="checklist"></a>Checkliste

In der folgenden Prüfliste finden Sie eine kurze Übersicht über die bewährten Methoden zur VM-Größe, die im weiteren Verlauf des Artikels ausführlicher behandelt werden: 

- Verwenden Sie VM-Größen mit vier oder mehr vCPUs, z. B. [Standard_M8-4ms](../../../virtual-machines/m-series.md), [E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series) und [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) oder höher. 
- Verwenden Sie VM-Größen mit [Arbeitsspeicheroptimierung](../../../virtual-machines/sizes-memory.md), um die bestmögliche Leistung für SQL Server-Workloads zu erzielen. 
- Die Serien [DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md), [Edsv4](../../../virtual-machines/edv4-edsv4-series.md), [M](../../../virtual-machines/m-series.md) und [Mv2](../../../virtual-machines/mv2-series.md) führen zu dem optimalen Verhältnis von Arbeitsspeicher zu virtuellen Kernen, das für OLTP-Workloads benötigt wird. Beide VMs der M-Serie bieten das beste Verhältnis von Arbeitsspeicher zu virtuellen Kernen für unternehmenskritische Workloads und sind zudem ideal für Data Warehouse-Workloads geeignet. 
- Für unternehmenskritische und Data Warehouse-Workloads ist unter Umständen ein höheres Verhältnis von Arbeitsspeicher zu virtuellen Kernen erforderlich. 
- Nutzen Sie die Azure Virtual Machines-Marketplace-Images, da die SQL Server-Einstellungen und Speicheroptionen für eine optimale SQL Server-Leistung konfiguriert sind. 
- Erfassen Sie die Leistungsmerkmale der Zielworkload, und nutzen Sie sie zum Ermitteln der passenden VM-Größe für Ihr Unternehmen.
- Verwenden Sie das [SKU-Empfehlungstool](/sql/dma/dma-sku-recommend-sql-db) des [Datenmigrations-Assistenten](https://www.microsoft.com/download/details.aspx?id=53595), um die richtige VM-Größe für Ihre vorhandene SQL Server-Workload zu ermitteln.

Informationen zum Vergleichen der Prüfliste für die VM-Größe mit den anderen, finden Sie unter [Prüfliste für bewährte Methoden für die Leistung](performance-guidelines-best-practices-checklist.md). 

## <a name="overview"></a>Übersicht

Wenn Sie eine SQL Server-Instanz auf einer Azure-VM erstellen, sollten Sie die Art der erforderlichen Workload sorgfältig abwägen. Wenn Sie eine bestehende Umgebung migrieren, [sammeln Sie eine Leistungsbaseline](performance-guidelines-best-practices-collect-baseline.md), um Ihre Anforderungen an die SQL Server-Instanz auf der Azure-VM zu bestimmen. Wenn es sich um einen neuen virtuellen Computer handelt, erstellen Sie basierend auf den Anforderungen Ihres Anbieters die neue SQL Server-VM. 

Wenn Sie eine neue SQL Server-VM mit einer neuen Anwendung für die Cloud erstellen, können Sie die Größe Ihrer SQL Server-VM leicht anpassen, falls sich Ihre Daten- und Nutzungsanforderungen ändern sollten.
Starten Sie die Entwicklungsumgebungen mit der kleineren D-Serie, B-Serie oder Av2-Serie, und erweitern Sie Ihre Umgebung dann im Laufe der Zeit. 

Verwenden Sie die Marketplace-Images für SQL Server-VMs mit der Speicherkonfiguration im Portal. Dies vereinfacht die richtige Erstellung der Speicherpools, die zum Erzielen der erforderlichen Größe, IOPS-Werte und Durchsatzwerte für Ihre Workloads benötigt werden. Es ist wichtig, SQL Server-VMs auszuwählen, für die Storage Premium und die Storage Premium-Zwischenspeicherung unterstützt werden. Weitere Informationen finden Sie im Artikel [Speicher](performance-guidelines-best-practices-storage.md). 

Die empfohlene Mindestspezifikation für eine OLTP-Produktionsumgebung umfasst 4 virtuelle Kerne, 32 GB Arbeitsspeicher und ein Verhältnis von Arbeitsspeicher zu virtuellen Kernen (Kurzbezeichnung: Arbeitsspeicher/Kern-Verhältnis) von „8“. Beginnen Sie bei neuen Umgebungen mit Computern mit vier virtuellen Kernen, und skalieren Sie dann auf 8, 16, 32 oder mehr virtuelle Kerne hoch, wenn sich Ihre Daten- und Computeanforderungen ändern. Streben Sie für den OLTP-Durchsatz die Verwendung von SQL Server-VMs an, die über 5.000 IOPS pro virtuellem Kern verfügen. 

Für SQL Server Data Warehouse und unternehmenskritische Umgebungen muss der Wert „8“ für das Verhältnis von Arbeitsspeicher zu virtuellen Kernen häufig erhöht werden. Für mittelgroße Umgebungen ist es ratsam, ein Verhältnis von Arbeitsspeicher zu virtuellen Kernen von „16“ auszuwählen, während der Wert für größere Data Warehouse-Umgebungen „32“ betragen sollte. 

SQL Server Data Warehouse-Umgebungen profitieren häufig von der Parallelverarbeitung größerer Computer. Aus diesem Grund sind die M-Serie und die Mv2-Serie gute Optionen für größere Data Warehouse-Umgebungen.

Verwenden Sie die vCPU- und Arbeitsspeicherkonfiguration Ihres Quellcomputers als Baseline für die Migration einer aktuellen lokalen SQL Server-Datenbank zu SQL Server auf Azure-VMs. Verwenden Sie Ihre eigene Core-Lizenz für Azure, um vom [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-benefit/) zu profitieren und SQL Server-Lizenzierungskosten zu sparen.

## <a name="memory-optimized"></a>Arbeitsspeicheroptimiert

Die [VM-Größen mit Arbeitsspeicheroptimierung](../../../virtual-machines/sizes-memory.md) sind ein primäres Ziel für SQL Server-VMs und werden von Microsoft empfohlen. Virtuelle Computer mit Arbeitsspeicheroptimierung verfügen über bessere Arbeitsspeicher/CPU-Verhältnisse und ermöglichen die Zwischenspeicherung im mittleren und hohen Bereich. 

### <a name="m-mv2-and-mdsv2-series"></a>Serie „M“, „Mv2“ und „Mdsv2“

Die [M-Serie](../../../virtual-machines/m-series.md) verfügt über eine Anzahl virtueller Kerne und Arbeitsspeichermenge für einige der umfangreichsten SQL Server-Workloads.  

Die [Mv2-Serie](../../../virtual-machines/mv2-series.md) verfügt über die höchste Anzahl virtueller Kerne und Arbeitsspeichermenge und ist für unternehmenskritische und Data Warehouse-Workloads zu empfehlen. Bei Instanzen der Mv2-Serie handelt es sich um arbeitsspeicheroptimierte VM-Größen zur Bereitstellung einer unvergleichlichen Computeleistung, um große In-Memory-Datenbanken und Workloads zu unterstützen. Sie verfügen über ein hohes Arbeitsspeicher/CPU-Verhältnis, das perfekt für relationale Datenbankserver, große Caches und In-Memory-Analysen geeignet ist.

Bei [Standard_M64ms](../../../virtual-machines/m-series.md) beträgt der Wert für das Arbeitsspeicher/Kern-Verhältnis beispielsweise „28“.

Die [Mdsv2-Serie mit mittlerem Arbeitsspeicher](../../..//virtual-machines/msv2-mdsv2-series.md) ist eine neue M-Serie, die sich derzeit in der [Vorschauversion](https://aka.ms/Mv2MedMemoryPreview) befindet und eine Reihe von virtuellen Azure-Computern auf Niveau der M-Serie mit einem mittleren Arbeitsspeicherangebot bietet. Diese Computer eignen sich gut für SQL Server-Workloads mit einer Unterstützung eines Verhältnisses von Arbeitsspeicher zu virtuellen Kernen von mindestens „10“ bis hin zu einem Wert von „30“.

Beispiele für einige Features der M- und Mv2-Serie, die eine hohe SQL Server-Leistung ermöglichen, sind die Unterstützung von [Storage Premium](../../../virtual-machines/premium-storage-performance.md) und [Storage Premium-Zwischenspeicherung](../../../virtual-machines/premium-storage-performance.md#disk-caching), die Unterstützung von [Ultra-Datenträgern](../../../virtual-machines/disks-enable-ultra-ssd.md) und [Schreibbeschleunigung](../../../virtual-machines/how-to-enable-write-accelerator.md).

### <a name="edsv4-series"></a>Edsv4-Serie

Die [Edsv4-Serie](../../../virtual-machines/edv4-edsv4-series.md) ist auf arbeitsspeicherintensive Anwendungen ausgelegt. Diese VMs verfügen über eine große lokale SSD-Speicherkapazität, starke lokale Datenträger-IOPS und bis zu 504 GiB RAM. Bei den meisten dieser virtuellen Computer stehen fast durchgängig 8 GiB Arbeitsspeicher pro virtuellem Kern zur Verfügung, was ideal für SQL Server-Standardworkloads ist. 

Es gibt einen neuen virtuellen Computer in dieser Gruppe mit der [Standard_E80ids_v4](../../../virtual-machines/edv4-edsv4-series.md), der 80 virtuelle Kerne, 504 GBs Arbeitsspeicher und ein Verhältnis von Arbeitsspeicher zu virtuellen Kernen von „6“ bietet. Dieser virtuelle Computer ist bemerkenswert, da er [isoliert](../../../virtual-machines/isolation.md) ist, was bedeutet, dass er garantiert der einzige virtuelle Computer ist, der auf dem Host ausgeführt wird, und daher von anderen Kundenworkloads isoliert ist. Dieser weist ein Verhältnis von Arbeitsspeicher zu virtuellen Kernen auf, das niedriger ist als das für SQL Server empfohlene Verhältnis, daher sollte es nur verwendet werden, wenn die Isolation erforderlich ist.

Für die virtuellen Computer der Edsv4-Serie werden [Storage Premium](../../../virtual-machines/premium-storage-performance.md) und die [Storage Premium-Zwischenspeicherung](../../../virtual-machines/premium-storage-performance.md#disk-caching) unterstützt.

### <a name="dsv2-series-11-15"></a>DSv2-Serie 11-15

Die [DSv2-Serie 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) weist die gleichen Arbeitsspeicher- und Datenträgerkonfigurationen wie die vorherige D-Serie auf. Diese Serie verfügt für alle virtuellen Computer über ein einheitliches Arbeitsspeicher/CPU-Verhältnis von „7“. Dies ist der kleinste der speicheroptimierten Serie und ist eine gute, kostengünstige Option für SQL Server-Workloads der Einstiegsebene.

Für die [DSv2-Serie 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) werden [Storage Premium](../../../virtual-machines/premium-storage-performance.md) und die [Storage Premium-Zwischenspeicherung](../../../virtual-machines/premium-storage-performance.md#disk-caching) unterstützt. Dies wird dringend empfohlen, um die optimale Leistung zu erzielen.

## <a name="general-purpose"></a>Allgemeiner Zweck

Die [VM-Größen vom Typ „Universell“](../../../virtual-machines/sizes-general.md) sind darauf ausgelegt, ausgeglichene Arbeitsspeicher/Kern-Verhältnisse für weniger umfangreiche Workloads auf Einstiegsebene bereitzustellen, z. B. Entwicklung und Tests, Webserver und kleinere Datenbankserver. 

Aufgrund der niedrigeren Arbeitsspeicher/Kern-Verhältnisse bei den VMs vom Typ „Universell“ ist es wichtig, die arbeitsspeicherbasierten Leistungsindikatoren sorgfältig zu überwachen. So kann sichergestellt werden, dass SQL Server den benötigten Puffercache-Arbeitsspeicher erhält. Weitere Informationen finden Sie im Abschnitt zur [Baseline für die Arbeitsspeicherleistung](performance-guidelines-best-practices-collect-baseline.md#memory). 

Da für Produktionsworkloads ein anfängliches Arbeitsspeicher/Kern-Verhältnis von „8“ empfohlen wird, lautet die Mindestempfehlung für die Konfiguration einer VM vom Typ „Universell“ mit SQL Server wie folgt: 4 vCPUs und 32 GB Arbeitsspeicher. 

### <a name="ddsv4-series"></a>Ddsv4-Serie

Die [Ddsv4-Serie](../../../virtual-machines/ddv4-ddsv4-series.md) bietet eine ausgewogene Kombination in Bezug auf vCPU, Arbeitsspeicher und temporären Datenträger, verfügt aber über eine eingeschränkte Arbeitsspeicher/Kern-Unterstützung. 

Die Ddsv4-VMs ermöglichen eine geringere Latenz und lokale Speichervorgänge mit höherer Geschwindigkeit.

Diese Computer eignen sich ideal für parallele SQL- und App-Bereitstellungen, für die ein schneller Zugriff auf den temporären Speicher und die relationalen Datenbanken verschiedener Abteilungen benötigt wird. Für alle virtuellen Computer dieser Serie beträgt das Arbeitsspeicher/Kern-Verhältnis standardmäßig „4“. 

Aus diesem Grund empfehlen wir Ihnen, die VM „D8ds_v4“ als Start-VM dieser Serie zu nutzen. Sie verfügt über acht virtuelle Kerne und 32 GB Arbeitsspeicher. Der größte Computer ist der „D64ds_v4“ mit 64 virtuellen Kernen und 256 GB Arbeitsspeicher.

Für die virtuellen Computer der [Ddsv4-Serie](../../../virtual-machines/ddv4-ddsv4-series.md) werden [Storage Premium](../../../virtual-machines/premium-storage-performance.md) und die [Storage Premium-Zwischenspeicherung](../../../virtual-machines/premium-storage-performance.md#disk-caching) unterstützt.

> [!NOTE]
> Die [Ddsv4-Serie](../../../virtual-machines/ddv4-ddsv4-series.md) verfügt nicht über das Arbeitsspeicher/Kern-Verhältnis von „8“, das für SQL Server-Workloads empfohlen wird. Aus diesem Grund ist es ratsam, diese virtuellen Computer nur für kleinere Anwendungen und Entwicklungsworkloads zu verwenden.

### <a name="b-series"></a>B-Serie

Die Größen der [burstfähigen VMs der B-Serie](../../../virtual-machines/sizes-b-series-burstable.md) sind ideal für Workloads geeignet, für die keine einheitliche Leistung erforderlich ist, z. B. Proof of Concept und sehr kleine Anwendungs- und Entwicklungsserver. 

Die meisten [burstfähigen VMs der B-Serie](../../../virtual-machines/sizes-b-series-burstable.md) verfügen über ein Arbeitsspeicher/Kern-Verhältnis von „4“. Der größte Computer ist hierbei der [Standard_B20ms](../../../virtual-machines/sizes-b-series-burstable.md) mit 20 virtuellen Kernen und 80 GB Arbeitsspeicher.

Diese Serie ist einzigartig, weil für die Apps während der Geschäftszeiten **Bursts** erfolgen können, wobei der Umfang der Burstfähigkeit je nach Computergröße variiert. 

Wenn das Guthaben erschöpft ist, wird die VM auf die Baseline-Computerleistung zurückgestuft.

Der Vorteil der B-Serie sind die Computeeinsparungen, die Sie im Vergleich zu den anderen VM-Größen anderer Serien erzielen können. Dies gilt besonders, wenn Sie die Verarbeitungsleistung über den Tag verteilt nicht sehr häufig benötigen.

Bei dieser Serie wird [Storage Premium](../../../virtual-machines/premium-storage-performance.md) unterstützt, aber **nicht** [Storage Premium-Zwischenspeicherung](../../../virtual-machines/premium-storage-performance.md#disk-caching).

> [!NOTE] 
> Die [burstfähige B-Serie](../../../virtual-machines/sizes-b-series-burstable.md) verfügt nicht über das Arbeitsspeicher/Kern-Verhältnis von „8“, das für SQL Server-Workloads empfohlen wird. Aus diesem Grund ist es ratsam, diese virtuellen Computer nur für kleinere Anwendungen, Webserver und Entwicklungsworkloads zu verwenden.

### <a name="av2-series"></a>Av2-Serie

Die VMs der [Av2-Serie](../../../virtual-machines/av2-series.md) eignen sich am besten für Workloads auf Einstiegsebene, z. B. Entwicklung und Tests, Webserver mit wenig Datenverkehr, kleinere bis mittlere App-Datenbanken und Proof of Concept-Vorgänge.

Nur [Standard_A2m_v2](../../../virtual-machines/av2-series.md) (2 virtuelle Kerne und 16 GB Arbeitsspeicher), [Standard_A4m_v2](../../../virtual-machines/av2-series.md) (4 virtuelle Kerne und 32 GB Arbeitsspeicher) und [Standard_A8m_v2](../../../virtual-machines/av2-series.md) (8 virtuelle Kerne und 64 GB Arbeitsspeicher) verfügen über ein gutes Arbeitsspeicher/Kern-Verhältnis von „8“ für diese drei virtuellen Computer der obersten Ebene. 

Diese virtuellen Computer sind eine gute Option, wenn kleinere SQL Server-Computer für Entwicklungs- und Testzwecke benötigt werden. 

[Standard_A8m_v2](../../../virtual-machines/av2-series.md) mit acht virtuellen Kernen kann ebenfalls eine gute Option für kleine Anwendungen und Webserver sein.

> [!NOTE] 
> Für die Av2-Serie wird Storage Premium nicht unterstützt. Sie ist daher auch dann nicht für SQL Server-Produktionsworkloads zu empfehlen, wenn die virtuellen Computer mit einem Arbeitsspeicher/Kern-Verhältnis von „8“ verwendet werden.

## <a name="storage-optimized"></a>Speicheroptimiert

Die [VM-Größen vom Typ „Datenspeicheroptimiert“](../../../virtual-machines/sizes-storage.md) sind für spezifische Anwendungsfälle gedacht. Bei diesen virtuellen Computern wurden der Datenträgerdurchsatz und die E/A-Leistung speziell optimiert. 

### <a name="lsv2-series"></a>Lsv2-Reihe

Die [Lsv2-Serie](../../../virtual-machines/lsv2-series.md) bietet hohen Durchsatz, geringe Latenz und lokalen NVMe-Speicher. Die VMs der Lsv2-Reihe sind für die Verwendung des direkt an die VM angebundenen lokalen Datenträgers auf dem Knoten optimiert, statt für die Verwendung dauerhafter Datenträger. 

Diese virtuellen Computer stellen eine sehr gute Option für Big Data, Data Warehousing, Berichterstellung und ETL-Workloads dar. Der hohe Durchsatz und IOPS-Wert des lokalen NVMe-Speichers ist ein guter Anwendungsfall zur Verarbeitung von Dateien, die in Ihre Datenbank geladen werden, sowie für andere Szenarien, in denen die Daten aus dem Quellsystem oder anderen Repositorys, z. B. Azure-Blobspeicher oder Azure Data Lake, neu erstellt werden können. Die virtuellen Computer der [Lsv2-Serie](../../../virtual-machines/lsv2-series.md) können ihre Datenträgerleistung ebenfalls für jeweils bis zu 30 Minuten per Burst steigern.

Diese VM-Größen bewegen sich zwischen 8 und 80 vCPUs mit 8 GiB Arbeitsspeicher pro vCPU, und für acht vCPUs sind jeweils 1,92 TB an NVMe-SSD-Speicherplatz verfügbar. Dies bedeutet, dass für die größte VM dieser Serie ([L80s_v2](../../../virtual-machines/lsv2-series.md)) 80 vCPUs und 640 BiB Arbeitsspeicher mit 10 x 1,92 TB an NVMe-Speicherplatz zur Verfügung stehen.  Für all diese virtuellen Computer gilt einheitlich ein Arbeitsspeicher/Kern-Verhältnis von „8“.

Der NVMe-Speicher ist kurzlebig, d. h. die Daten auf diesen Datenträgern gehen verloren, wenn Sie die Zuordnung des virtuellen Computers aufheben oder wenn er zur Dienstreparatur auf einen anderen Host verschoben wird.

Die Serien Lsv2 und Ls unterstützen [Storage Premium](../../../virtual-machines/premium-storage-performance.md), aber keine Storage Premium-Zwischenspeicherung. Die Erstellung eines lokalen Caches zur Erhöhung des IOPS-Werts wird nicht unterstützt. 

> [!WARNING]
> Die Speicherung Ihrer Datendateien in kurzlebigem NVMe-Speicher kann zu Datenverlust führen, wenn die Zuordnung der VM aufgehoben wird. 

## <a name="constrained-vcores"></a>Eingeschränkte virtuelle Kerne

Für SQL Server-Workloads mit hohen Leistungsanforderungen werden häufig größere Mengen an Arbeitsspeicher, E/A-Leistung und Durchsatz ohne eine höhere Anzahl virtueller Kerne benötigt. 

Bei den meisten OLTP-Workloads handelt es sich um Anwendungsdatenbanken mit einer großen Zahl an kleineren Transaktionen. Bei OLTP-Workloads wird nur eine geringe Menge der Daten gelesen oder geändert, aber die Menge an Transaktionen basierend auf der Benutzeranzahl ist deutlich größer. Es ist wichtig, dass SQL Server-Arbeitsspeicher verfügbar ist, um Pläne zwischenzuspeichern, kürzlich genutzte Daten zur Verbesserung der Leistung zu speichern und sicherzustellen, dass physische Lesevorgänge schneller in den Arbeitsspeicher eingelesen werden können. 

Für diese OLTP-Umgebungen sind eine größere Menge an Arbeitsspeicher, schneller Speicher und die benötigte E/A-Bandbreite zur Erzielung einer optimalen Leistung erforderlich. 

Um diese Leistungsebene nutzen zu können, ohne dass die höheren SQL Server-Lizenzierungskosten anfallen, werden von Azure VM-Größen mit einer [eingeschränkten vCPU-Anzahl](../../../virtual-machines/constrained-vcpu.md) angeboten. 

Auf diese Weise sind die Lizenzierungskosten kontrollierbar, indem die verfügbaren virtuellen Kerne reduziert werden, während die Werte für den Arbeitsspeicher, den Speicher und die E/A-Bandbreite des übergeordneten virtuellen Computers beibehalten werden.

Die Anzahl von vCPUs kann auf die Hälfte oder ein Viertel der ursprünglichen VM-Größe beschränkt werden. Durch die Verringerung der für den virtuellen Computer verfügbaren virtuellen Kerne wird ein höheres Verhältnis von Arbeitsspeicher zu virtuellen Kernen erreicht, aber die Computekosten bleiben gleich.

Diese neuen VM-Größen verfügen über ein Suffix, das die Anzahl von aktiven vCPUs angibt, um die Identifizierung zu erleichtern. 

Beispielsweise müssen für [M64-32ms](../../../virtual-machines/constrained-vcpu.md) nur 32 virtuelle SQL Server-Kerne mit den Arbeitsspeicher-, E/A- und Durchsatzwerten von [M64ms](../../../virtual-machines/m-series.md) lizenziert werden, und für [M64-16ms](../../../virtual-machines/constrained-vcpu.md) ist nur die Lizenzierung von 16 virtuellen Kernen erforderlich.  Für [M64-16ms](../../../virtual-machines/constrained-vcpu.md) fällt dann zwar ein Viertel der SQL Server-Lizenzierungskosten von M64ms an, aber die Computekosten des virtuellen Computers sind identisch.

> [!NOTE] 
> - Mittlere bis große Data Warehouse-Workloads können trotzdem von [VMs mit eingeschränkten virtuellen Kernen](../../../virtual-machines/constrained-vcpu.md) profitieren. Data Warehouse-Workloads zeichnen sich häufig aber durch weniger Benutzer und Prozesse aus, bei denen größere Datenmengen mit parallel ausgeführten Abfrageplänen verarbeitet werden. 
> - Die Computekosten, zu denen auch die Lizenzierung für das Betriebssystem gehört, ändern sich gegenüber dem übergeordneten virtuellen Computer nicht. 



## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich auch die anderen Artikel in dieser Reihe an, um mehr zu erfahren:
- [Kurze Checkliste](performance-guidelines-best-practices-checklist.md)
- [Storage](performance-guidelines-best-practices-storage.md)
- [Security](security-considerations-best-practices.md)
- [HADR-Einstellungen](hadr-cluster-best-practices.md)
- [Baseline auswählen](performance-guidelines-best-practices-collect-baseline.md)

Bewährte Methoden für die Sicherheit finden Sie unter [Sicherheitsüberlegungen für SQL Server auf Azure Virtual Machines](security-considerations-best-practices.md).

Weitere Artikel zu virtuellen SQL Server-Computern finden Sie unter [Übersicht zu SQL Server auf virtuellen Azure-Computern](sql-server-on-azure-vm-iaas-what-is-overview.md). Falls Sie Fragen zu SQL Server-VMs haben, finden Sie in den [häufig gestellten Fragen](frequently-asked-questions-faq.yml) weitere Informationen.
