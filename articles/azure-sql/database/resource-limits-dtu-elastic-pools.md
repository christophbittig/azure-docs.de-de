---
title: Pools für elastische Datenbanken mit DTU-Ressourcenlimits
description: Diese Seite beschreibt einige allgemeine DTU-Ressourcenlimits für Pools für elastische Datenbanken in Azure SQL-Datenbank.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: seo-lt-2019 sqldbrb=1 references_regions
ms.devlang: ''
ms.topic: reference
author: dimitri-furman
ms.author: dfurman
ms.reviewer: mathoma
ms.date: 10/12/2021
ms.openlocfilehash: 4c131b756a03622a2569f997fe427ebfdb661454
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129999718"
---
# <a name="resources-limits-for-elastic-pools-using-the-dtu-purchasing-model"></a>Grenzwerte für Ressourcen für Pools für elastische Datenbanken, die das DTU-Kaufmodell verwenden
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Dieser Artikel enthält ausführliche Angaben zu Ressourcenlimits für Datenbanken in Azure SQL-Datenbank, die in einem Pool für elastische Datenbanken enthalten sind und für die das DTU-Kaufmodell gilt.

* Weitere Informationen zu Ressourcenlimits für Singletons auf einem Server mit dem DTU-basierten Kaufmodell finden Sie unter [Übersicht über Ressourcenlimits auf einem Server](resource-limits-logical-server.md).
* Informationen zu Ressourcenlimits des DTU-Kaufmodells für Azure SQL-Datenbank finden Sie unter [DTU-Ressourcenlimits für einzelne Datenbanken (Singletons)](resource-limits-dtu-single-databases.md) und [DTU-Ressourcenlimits für Pools für elastische Datenbanken](resource-limits-dtu-elastic-pools.md).
* Informationen zu V-Kern-Ressourcenlimits finden Sie unter [V-Kern-Ressourcenlimits – Azure SQL-Datenbank](resource-limits-vcore-single-databases.md) und [V-Kern-Ressourcenlimits – Pools für elastische Datenbanken](resource-limits-vcore-elastic-pools.md).
* Weitere Informationen zu den verschiedenen Kaufmodellen finden Sie unter [Kaufmodelle und Dienstebenen](purchasing-models.md).

Jedes schreibgeschützte Replikat hat eigene Ressourcen wie DTUs, Worker und Sitzungen. Jedes schreibgeschützte Replikat unterliegt den Ressourcenlimits, die weiter unten in diesem Artikel beschrieben werden.

## <a name="elastic-pool-storage-sizes-and-compute-sizes"></a>Pool für elastische Datenbanken: Speicher- und Computegrößen

Die folgenden Tabellen enthalten die verfügbaren Ressourcen für Pools für elastische Azure SQL-Datenbank-Instanzen für alle Dienstebenen und Computegrößen. Sie haben zum Festlegen der Dienstebene, Computegröße und Speichermenge folgende Optionen:

* [Transact-SQL](elastic-pool-scale.md) über [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql#overview-sql-database)
* [Azure portal](elastic-pool-manage.md#azure-portal)
* [PowerShell](elastic-pool-manage.md#powershell)
* [Azure-Befehlszeilenschnittstelle](elastic-pool-manage.md#azure-cli)
* [REST-API](elastic-pool-manage.md#rest-api)


> [!IMPORTANT]
> Anleitungen und Überlegungen zur Skalierung finden Sie unter [Skalieren eines Pools für elastische Datenbanken](elastic-pool-scale.md).

Die Ressourcengrenzwerte einzelner Datenbanken in Pools für elastische Datenbanken entsprechen im Allgemeinen hinsichtlich DTU-Anzahl und Dienstebene denen einzelner Datenbanken außerhalb von Pools. Auf eine S2-Datenbank können z.B. max. 120 Mitarbeiter gleichzeitig zugreifen. Also können auch 120 Mitarbeiter gleichzeitig auf eine Datenbank in einem Standard-Pool zugreifen, wenn max. 50 DTUs pro Datenbank im Pool zulässig sind (entspricht S2).
 
Bei derselben Anzahl von DTUs überschreiten die für einen Pool für elastische Datenbanken bereitgestellten Ressourcen möglicherweise die Ressourcen, die für eine einzelne Datenbank außerhalb eines Pools für elastische Datenbanken bereitgestellt wurden. Dies bedeutet: Es ist möglich, dass die eDTU-Auslastung eines Pools für elastische Datenbanken je nach Workloadmuster kleiner als die Summe der DTU-Auslastung auf allen Datenbanken innerhalb des Pools ist. So ist es beispielsweise in einem Extremfall mit nur einer einzigen Datenbank in einem Pool für elastische Datenbanken, bei dem die DTU-Auslastung der Datenbank 100 % beträgt, möglich, dass die Pool-eDTU-Auslastung bei bestimmten Workloadmustern 50 % beträgt. Dies kann sogar dann geschehen, wenn die maximale DTU-Anzahl pro Datenbank den maximal unterstützten Wert für die angegebene Poolgröße beibehält.

> [!NOTE]
> Das Speicherressourcenlimit pro Pool in den folgenden Tabellen gilt nicht für die Speicher „tempdb“ und „log“.

### <a name="basic-elastic-pool-limits"></a>Grenzwerte für Pools für elastische Datenbanken – Basic

| eDTUs pro Pool | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Inbegriffener Speicher pro Pool (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Max. Speicherkapazität pro Pool (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Max. In-Memory-OLTP-Speicher pro Pool (GB) | – | – | – | – | – | – | – | – |
| Max. Anzahl Datenbanken pro Pool <sup>1</sup> | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Max. gleichzeitige Worker (Anforderungen) pro Pool <sup>2</sup> | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Max. gleichzeitige Sitzungen pro Pool <sup>2</sup> | 30.000 | 30.000 | 30.000 | 30.000 |30.000 | 30.000 | 30.000 | 30.000 |
| Min. DTU-Anzahl pro Datenbankauswahl | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 |
| Max. DTU-Anzahl pro Datenbankauswahl | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| Max. Speicherkapazität pro Datenbank (GB) | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 |
||||||||

<sup>1</sup> Weitere Überlegungen finden Sie unter [Ressourcenverwaltung in umfangreichen Pools für elastische Datenbanken](elastic-pool-resource-management.md).

<sup>2</sup> Den Wert für „Max. gleichzeitige Worker (Anforderungen)“ für einzelne Datenbanken finden Sie unter [Ressourcenlimits für Singletons mit dem auf virtuellen Kernen (V-Kernen) basierenden Kaufmodell](resource-limits-vcore-single-databases.md). Wenn für den Pool für elastische Datenbanken beispielsweise Gen5 verwendet wird und die maximale Anzahl von V-Kernen pro Datenbank auf 2 festgelegt ist, ist der Wert für die Anzahl von gleichzeitigen Workern 200.  Wenn die maximale Anzahl von V-Kernen pro Datenbank auf 0,5 festgelegt ist, beträgt der Wert für die Anzahl von gleichzeitigen Workern 50, da unter Gen5 maximal 100 gleichzeitige Worker pro V-Kern verwendet werden. Für andere Einstellungen zur maximalen Anzahl von V-Kernen pro Datenbank (1 V-Kern oder weniger), wird die maximale Anzahl von gleichzeitigen Workern auf ähnliche Weise neu skaliert.

### <a name="standard-elastic-pool-limits"></a>Grenzwerte für Pools für elastische Datenbanken – Standard

| eDTUs pro Pool | **50** | **100** | **200** | **300** | **400** | **800**|
|:---|---:|---:|---:| ---: | ---: | ---: |
| Inbegriffener Speicher pro Pool (GB) <sup>1</sup> | 50 | 100 | 200 | 300 | 400 | 800 |
| Max. Speicherkapazität pro Pool (GB) | 500 | 750 | 1024 | 1280 | 1536 | 2048 |
| Max. In-Memory-OLTP-Speicher pro Pool (GB) | – | – | – | – | – | – |
| Max. Anzahl Datenbanken pro Pool <sup>2</sup> | 100 | 200 | 500 | 500 | 500 | 500 |
| Max. gleichzeitige Worker (Anforderungen) pro Pool <sup>3</sup> | 100 | 200 | 400 | 600 | 800 | 1600 |
| Max. gleichzeitige Sitzungen pro Pool <sup>3</sup> | 30.000 | 30.000 | 30.000 | 30.000 | 30.000 | 30.000 |
| Min. DTU-Anzahl pro Datenbankauswahl | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| Max. DTU-Anzahl pro Datenbankauswahl | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 |
| Max. Speicherkapazität pro Datenbank (GB) | 1024 | 1024 | 1024 | 1024 | 1024 | 1024 |
||||||||

<sup>1</sup> Unter [Preise für Azure SQL-Datenbank](https://azure.microsoft.com/pricing/details/sql-database/elastic/) finden Sie weitere Informationen zu zusätzlichen Kosten, die durch den zusätzlich bereitgestellten Speicherplatz entstehen.

<sup>2</sup> Weitere Überlegungen finden Sie unter [Ressourcenverwaltung in umfangreichen Pools für elastische Datenbanken](elastic-pool-resource-management.md).

<sup>3</sup> Den Wert für die maximale Anzahl von gleichzeitigen Workern (Anforderungen) für einzelne Datenbanken finden Sie unter [Ressourceneinschränkungen für einzelne Datenbanken](resource-limits-vcore-single-databases.md). Wenn für den Pool für elastische Datenbanken beispielsweise Gen5 verwendet wird und die maximale Anzahl von V-Kernen pro Datenbank auf 2 festgelegt ist, ist der Wert für die Anzahl von gleichzeitigen Workern 200.  Wenn die maximale Anzahl von V-Kernen pro Datenbank auf 0,5 festgelegt ist, beträgt der Wert für die Anzahl von gleichzeitigen Workern 50, da unter Gen5 maximal 100 gleichzeitige Worker pro V-Kern verwendet werden. Für andere Einstellungen zur maximalen Anzahl von V-Kernen pro Datenbank (1 V-Kern oder weniger), wird die maximale Anzahl von gleichzeitigen Workern auf ähnliche Weise neu skaliert.

### <a name="standard-elastic-pool-limits-continued"></a>Grenzwerte für Pools für elastische Datenbanken – Standard (Fortsetzung)

| eDTUs pro Pool | **1200** | **1600** | **2000** | **2500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| Inbegriffener Speicher pro Pool (GB) <sup>1</sup> | 1200 | 1600 | 2000 | 2500 | 3000 |
| Max. Speicherkapazität pro Pool (GB) | 2560 | 3072 | 3.584 | 4096 | 4096 |
| Max. In-Memory-OLTP-Speicher pro Pool (GB) | – | – | – | – | – |
| Max. Anzahl Datenbanken pro Pool <sup>2</sup> | 500 | 500 | 500 | 500 | 500 |
| Max. gleichzeitige Worker (Anforderungen) pro Pool <sup>3</sup> | 2400 | 3200 | 4000 | 5.000 | 6000 |
| Max. gleichzeitige Sitzungen pro Pool <sup>3</sup> | 30.000 | 30.000 | 30.000 | 30.000 | 30.000 |
| Min. DTU-Anzahl pro Datenbankauswahl | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Max. DTU-Anzahl pro Datenbankauswahl | 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Max. Speicherkapazität pro Datenbank (GB) | 1024 | 1536 | 1792 | 2304 | 2816 |
|||||||

<sup>1</sup> Unter [Preise für Azure SQL-Datenbank](https://azure.microsoft.com/pricing/details/sql-database/elastic/) finden Sie weitere Informationen zu zusätzlichen Kosten, die durch den zusätzlich bereitgestellten Speicherplatz entstehen.

<sup>2</sup> Weitere Überlegungen finden Sie unter [Ressourcenverwaltung in umfangreichen Pools für elastische Datenbanken](elastic-pool-resource-management.md).

<sup>3</sup> Den Wert für die maximale Anzahl von gleichzeitigen Workern (Anforderungen) für einzelne Datenbanken finden Sie unter [Ressourceneinschränkungen für einzelne Datenbanken](resource-limits-vcore-single-databases.md). Wenn für den Pool für elastische Datenbanken beispielsweise Gen5 verwendet wird und die maximale Anzahl von V-Kernen pro Datenbank auf 2 festgelegt ist, ist der Wert für die Anzahl von gleichzeitigen Workern 200.  Wenn die maximale Anzahl von V-Kernen pro Datenbank auf 0,5 festgelegt ist, beträgt der Wert für die Anzahl von gleichzeitigen Workern 50, da unter Gen5 maximal 100 gleichzeitige Worker pro V-Kern verwendet werden. Für andere Einstellungen zur maximalen Anzahl von V-Kernen pro Datenbank (1 V-Kern oder weniger), wird die maximale Anzahl von gleichzeitigen Workern auf ähnliche Weise neu skaliert.

### <a name="premium-elastic-pool-limits"></a>Grenzwerte für Pools für elastische Datenbanken – Premium

| eDTUs pro Pool | **125** | **250** | **500** | **1000** | **1500**|
|:---|---:|---:|---:| ---: | ---: |
| Inbegriffener Speicher pro Pool (GB) <sup>1</sup> | 250 | 500 | 750 | 1024 | 1536 |
| Max. Speicherkapazität pro Pool (GB) | 1024 | 1024 | 1024 | 1024 | 1536 |
| Max. In-Memory-OLTP-Speicher pro Pool (GB) | 1 | 2 | 4 | 10 | 12 |
| Max. Anzahl Datenbanken pro Pool <sup>2</sup> | 50 | 100 | 100 | 100 | 100 |
| Max. gleichzeitige Worker pro Pool (Anforderungen) <sup>3</sup> | 200 | 400 | 800 | 1600 | 2400 |
| Max. gleichzeitige Sitzungen pro Pool <sup>3</sup> | 30.000 | 30.000 | 30.000 | 30.000 | 30.000 |
| Min. Anz. von eDTUs pro Datenbank | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 | 0, 25, 50, 75, 125, 250, 500, 1000|
| Max. Anz. von eDTUs pro Datenbank | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 25, 50, 75, 125, 250, 500, 1000|
| Max. Speicherkapazität pro Datenbank (GB) | 1024 | 1024 | 1024 | 1024 | 1536 |
|||||||

<sup>1</sup> Unter [Preise für Azure SQL-Datenbank](https://azure.microsoft.com/pricing/details/sql-database/elastic/) finden Sie weitere Informationen zu zusätzlichen Kosten, die durch den zusätzlich bereitgestellten Speicherplatz entstehen.

<sup>2</sup> Weitere Überlegungen finden Sie unter [Ressourcenverwaltung in umfangreichen Pools für elastische Datenbanken](elastic-pool-resource-management.md).

<sup>3</sup> Den Wert für die maximale Anzahl von gleichzeitigen Workern (Anforderungen) für einzelne Datenbanken finden Sie unter [Ressourceneinschränkungen für einzelne Datenbanken](resource-limits-vcore-single-databases.md). Wenn für den Pool für elastische Datenbanken beispielsweise Gen5 verwendet wird und die maximale Anzahl von V-Kernen pro Datenbank auf 2 festgelegt ist, ist der Wert für die Anzahl von gleichzeitigen Workern 200.  Wenn die maximale Anzahl von V-Kernen pro Datenbank auf 0,5 festgelegt ist, beträgt der Wert für die Anzahl von gleichzeitigen Workern 50, da unter Gen5 maximal 100 gleichzeitige Worker pro V-Kern verwendet werden. Für andere Einstellungen zur maximalen Anzahl von V-Kernen pro Datenbank (1 V-Kern oder weniger), wird die maximale Anzahl von gleichzeitigen Workern auf ähnliche Weise neu skaliert.

### <a name="premium-elastic-pool-limits-continued"></a>Grenzwerte für Pools für elastische Datenbanken – Premium (Fortsetzung)

| eDTUs pro Pool | **2000** | **2500** | **3000** | **3500** | **4000**|
|:---|---:|---:|---:| ---: | ---: |
| Inbegriffener Speicher pro Pool (GB) <sup>1</sup> | 2048 | 2560 | 3072 | 3548 | 4096 |
| Max. Speicherkapazität pro Pool (GB) | 2048 | 2560 | 3072 | 3548 | 4096|
| Max. In-Memory-OLTP-Speicher pro Pool (GB) | 16 | 20 | 24 | 28 | 32 |
| Max. Anzahl Datenbanken pro Pool <sup>2</sup> | 100 | 100 | 100 | 100 | 100 |
| Max. gleichzeitige Worker (Anforderungen) pro Pool <sup>3</sup> | 3200 | 4000 | 4800 | 5600 | 6400 |
| Max. gleichzeitige Sitzungen pro Pool <sup>3</sup> | 30.000 | 30.000 | 30.000 | 30.000 | 30.000 |
| Min. DTU-Anzahl pro Datenbankauswahl | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 |
| Max. DTU-Anzahl pro Datenbankauswahl | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 |
| Max. Speicherkapazität pro Datenbank (GB) | 2048 | 2560 | 3072 | 3.584 | 4096 |
|||||||

<sup>1</sup> Unter [Preise für Azure SQL-Datenbank](https://azure.microsoft.com/pricing/details/sql-database/elastic/) finden Sie weitere Informationen zu zusätzlichen Kosten, die durch den zusätzlich bereitgestellten Speicherplatz entstehen.

<sup>2</sup> Weitere Überlegungen finden Sie unter [Ressourcenverwaltung in umfangreichen Pools für elastische Datenbanken](elastic-pool-resource-management.md).

<sup>3</sup> Den Wert für die maximale Anzahl von gleichzeitigen Workern (Anforderungen) für einzelne Datenbanken finden Sie unter [Ressourceneinschränkungen für einzelne Datenbanken](resource-limits-vcore-single-databases.md). Wenn für den Pool für elastische Datenbanken beispielsweise Gen5 verwendet wird und die maximale Anzahl von V-Kernen pro Datenbank auf 2 festgelegt ist, ist der Wert für die Anzahl von gleichzeitigen Workern 200.  Wenn die maximale Anzahl von V-Kernen pro Datenbank auf 0,5 festgelegt ist, beträgt der Wert für die Anzahl von gleichzeitigen Workern 50, da unter Gen5 maximal 100 gleichzeitige Worker pro V-Kern verwendet werden. Für andere Einstellungen zur maximalen Anzahl von V-Kernen pro Datenbank (1 V-Kern oder weniger), wird die maximale Anzahl von gleichzeitigen Workern auf ähnliche Weise neu skaliert.

> [!IMPORTANT]
> In allen Regionen außer den folgenden ist im Premium-Tarif derzeit mehr als 1 TB Speicher verfügbar: China, Osten; China, Norden; Deutschland, Mitte; Deutschland, Nordosten. In diesen Regionen ist der Speicher im Tarif „Premium“ auf 1 TB begrenzt.  Weitere Informationen finden Sie unter [Einschränkungen von P11 und P15](single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).

Wenn alle DTUs eines Pools für elastische Datenbanken verwendet werden, erhält jede Datenbank im Pool gleich viel Ressourcen zum Verarbeiten von Abfragen. Der SQL-Datenbank-Dienst bietet eine faire gemeinsame Nutzung von Ressourcen durch Datenbanken, indem gleiche Slices an Computezeit zugesichert werden. Diese faire gemeinsame Nutzung in Pools für elastische Datenbanken ergänzt jegliche Ressourcen, die jeder Datenbank auf andere Weise garantiert werden, wenn das DTU-Minimum pro Datenbank auf einen Wert ungleich null festgelegt ist.

> [!NOTE]
> Weitere Informationen zu `tempdb`-Einschränkungen finden Sie unter [tempdb-Grenzwerte](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
>
> Weitere Informationen zu Speichergrenzwerten der Dienstebene „Premium“ finden Sie unter [Speicherplatzgovernance](resource-limits-logical-server.md#storage-space-governance).

### <a name="database-properties-for-pooled-databases"></a>Eigenschaften von Pooldatenbanken

Für jeden Pool für elastische Datenbanken können Sie optional pro Datenbank minimale und maximale DTUs angeben, um Ressourcenverbrauchsmuster innerhalb des Pools zu ändern. Die angegebenen Mindest- und Höchstwerte gelten für alle Datenbanken im Pool. Das Anpassen der Mindest- und Höchstanzahl von DTUs für einzelne Datenbanken im Pool wird nicht unterstützt. 

Sie können auch den maximalen Speicher pro Datenbank festlegen, um beispielsweise zu verhindern, dass eine Datenbank den gesamten Poolspeicher beansprucht. Diese Einstellung kann unabhängig für jede Datenbank konfiguriert werden.

In der folgenden Tabelle werden Eigenschaften für Pooldatenbanken beschrieben, die pro Datenbank festgelegt werden können: 

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| Maximale Anzahl von DTUs pro Datenbank |Die maximale Anzahl von DTUs, die von einer beliebigen Datenbank im Pool verwendet werden können, sofern sie verfügbar sind (abhängig von der Nutzung durch andere Datenbanken im Pool). Die maximale Anzahl von DTUs pro Datenbank ist keine Ressourcengarantie für eine Datenbank. Wenn für die Workload in den einzelnen Datenbanken nicht alle verfügbaren Poolressourcen benötigt werden, um eine angemessene Leistung zu erzielen, empfiehlt es sich gegebenenfalls, die maximale Anzahl von DTUs pro Datenbank festzulegen, um zu verhindern, dass eine einzelne Datenbank übermäßig viele Poolressourcen beansprucht. Sie sollten ein gewisses Maß an Mehrlast einplanen, da für den Pool im Allgemeinen von Nutzungsmustern starker und schwacher Auslastung ausgegangen wird, bei der aber nicht alle Datenbanken gleichzeitig stark ausgelastet sind. |
| Mindestanzahl von DTUs pro Datenbank |Die Mindestanzahl von DTUs, die für jede Datenbank im Pool reserviert wird. Das Festlegen einer Mindestanzahl von DTUs pro Datenbank empfiehlt sich gegebenenfalls, wenn Sie die Ressourcenverfügbarkeit für jede Datenbank unabhängig vom Ressourcenverbrauch durch andere Datenbanken im Pool gewährleisten möchten. Die Mindestanzahl von DTUs pro Datenbank kann auf „0“ festgelegt werden. Das ist auch der Standardwert. Diese Eigenschaft wird auf einen Wert zwischen null und der durchschnittlichen DTU-Nutzung pro Datenbank festgelegt.|
| Max. Speicherkapazität pro Datenbank |Die maximale Datenbankgröße, die vom Benutzer für eine Datenbank in einem Pool festgelegt wird. Pooldatenbanken nutzen den zugeordneten Poolspeicher gemeinsam. Daher ist die Größe, die eine Datenbank erreichen kann, auf den jeweils kleineren Wert des verbleibenden Poolspeichers oder die maximale Datenbankgröße beschränkt. Die maximale Datenbankgröße bezieht sich auf die maximale Größe der Datendateien und umfasst nicht den von der Protokolldatei belegten Speicherplatz. |
|||

> [!IMPORTANT]
> Da Ressourcen in einem Pool für elastische Datenbanken endlich sind, wird die Ressourcenauslastung durch die einzelnen Datenbanken implizit beschränkt, wenn Sie die Mindestanzahl von DTUs pro Datenbank auf einen Wert größer null festlegen. Falls sich zu einem bestimmten Zeitpunkt die meisten Datenbanken in einem Pool im Leerlauf befinden, stehen Ressourcen, die zur Erfüllung der Mindest-DTU-Garantie reserviert sind, für Datenbanken, die zu diesem Zeitpunkt aktiv sind, nicht zur Verfügung.
>
> Darüber hinaus wird die Anzahl von Datenbanken, die dem Pool hinzugefügt werden können, implizit beschränkt, wenn Sie die Mindestanzahl von DTUs pro Datenbank auf einen Wert größer null festlegen. Wenn Sie also beispielsweise in einem Pool mit 400 DTUs die Mindestanzahl von DTUs auf „100“ festlegen, können dem Pool maximal vier Datenbanken hinzugefügt werden, da für jede Datenbank 100 DTUs reserviert werden.
> 

Die Eigenschaften pro Datenbank werden zwar in DTUs ausgedrückt, steuern aber auch den Verbrauch anderer Ressourcentypen wie Daten-E/A, Protokoll-E/A, Pufferpoolspeicher und Arbeitsthreads. Wenn Sie Mindest- und Höchstwerte für DTUs pro Datenbank anpassen, werden Reservierungen und Grenzwerte für alle Ressourcentypen proportional angepasst.

Die Minimal- und Maximalwerte der DTU pro Datenbank gelten für den Ressourcenverbrauch durch Benutzerworkloads, aber nicht für den Ressourcenverbrauch durch interne Prozesse. Bei einer Datenbank mit einer maximalen DTU pro Datenbank, die auf die Hälfte der Pool-eDTU festgelegt ist, kann die Benutzerworkload beispielsweise nicht mehr als die Hälfte des Pufferpoolspeichers beanspruchen. Diese Datenbank kann jedoch weiterhin Seiten im Pufferpool nutzen, die von internen Prozessen geladen wurden. Weitere Informationen finden Sie unter [Ressourcenverbrauch durch Benutzerworkloads und interne Prozesse](resource-limits-logical-server.md#resource-consumption-by-user-workloads-and-internal-processes).

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu V-Kern-Ressourcenlimits für eine Einzeldatenbank finden Sie unter [Ressourcenlimits für Einzeldatenbanken, die das V-Kern-Kaufmodell verwenden](resource-limits-vcore-single-databases.md).
* Informationen zu DTU-Ressourcenlimits für einen Singleton finden Sie unter [Ressourcenlimits für Singletons, die das DTU-Kaufmodell verwenden](resource-limits-dtu-single-databases.md).
* Informationen zu V-Kern-Ressourcenlimits für Pools für elastische Datenbanken finden Sie unter [Ressourcenlimits für Pools für elastische Datenbanken, die das V-Kern-Kaufmodell verwenden](resource-limits-vcore-elastic-pools.md).
* Informationen zu den Ressourcenlimits für verwaltete Instanzen in Azure SQL Managed Instance finden Sie unter [Ressourcengrenzwerte für SQL Managed Instance](../managed-instance/resource-limits.md).
* Informationen zu allgemeinen Azure-Einschränkungen finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../../azure-resource-manager/management/azure-subscription-service-limits.md).
* Informationen zu Ressourcengrenzwerten auf Server- und Abonnementebene auf einem logischen SQL-Server finden Sie unter [Übersicht über Ressourcenlimits für einen logischen SQL-Server](resource-limits-logical-server.md).