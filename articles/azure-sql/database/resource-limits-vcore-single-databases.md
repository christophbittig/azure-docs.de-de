---
title: 'Einzeldatenbank: V-Kern-Ressourcenlimits'
description: Auf dieser Seite werden einige allgemeine Ressourcenlimits für virtuelle Kerne für Singletons in Azure SQL-Datenbank beschrieben.
services: sql-database
ms.service: sql-database
ms.subservice: service-overview
ms.custom: sqldbrb=1, references_regions
ms.devlang: ''
ms.topic: reference
author: dimitri-furman
ms.author: dfurman
ms.reviewer: mathoma
ms.date: 07/21/2021
ms.openlocfilehash: d3980a490a5ce61d7c428f76b8503fc49a62fbb3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355455"
---
# <a name="resource-limits-for-single-databases-using-the-vcore-purchasing-model"></a>Ressourcenlimits für Singletons mit dem auf virtuellen Kernen (V-Kernen) basierenden Kaufmodell
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Dieser Artikel enthält ausführliche Angaben zu Ressourcenlimits für Singletons in Azure SQL-Datenbank, für die das vCore-basierte Kaufmodell verwendet wird.

* Weitere Informationen zu Ressourcenlimits für Singletons auf einem Server mit dem DTU-basierten Kaufmodell finden Sie unter [Übersicht über Ressourcenlimits auf einem Server](resource-limits-logical-server.md).
* Informationen zu Ressourcenlimits des DTU-Kaufmodells für Azure SQL-Datenbank finden Sie unter [DTU-Ressourcenlimits für einzelne Datenbanken (Singletons)](resource-limits-dtu-single-databases.md) und [DTU-Ressourcenlimits für Pools für elastische Datenbanken](resource-limits-dtu-elastic-pools.md).
* Informationen zu V-Kern-Ressourcenlimits finden Sie unter [V-Kern-Ressourcenlimits – Azure SQL-Datenbank](resource-limits-vcore-single-databases.md) und [V-Kern-Ressourcenlimits – Pools für elastische Datenbanken](resource-limits-vcore-elastic-pools.md).
* Weitere Informationen zu den verschiedenen Kaufmodellen finden Sie unter [Kaufmodelle und Dienstebenen](purchasing-models.md).

> [!IMPORTANT]
> Unter bestimmten Umständen müssen Sie ggf. eine Datenbank verkleinern, um ungenutzten Speicherplatz freizugeben. Weitere Informationen finden Sie unter [Verwalten von Dateispeicherplatz in Azure SQL-Datenbank](file-space-manage.md).

Jedes schreibgeschützte Replikat einer Datenbank verfügt über eigene Ressourcen wie virtuelle Kerne, Arbeitsspeicher, Daten-IOPS, TempDB, Worker und Sitzungen. Jedes schreibgeschützte Replikat unterliegt den Ressourcenlimits, die weiter unten in diesem Artikel beschrieben werden.

Sie können die Dienstebene, Computegröße (Dienstziel) und Speichermenge für eine Einzeldatenbank mithilfe folgender Optionen festlegen:

* [Transact-SQL](single-database-manage.md#transact-sql-t-sql) über [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql#overview-sql-database)
* [Azure portal](single-database-manage.md#the-azure-portal)
* [PowerShell](single-database-manage.md#powershell)
* [Azure-Befehlszeilenschnittstelle](single-database-manage.md#the-azure-cli)
* [REST-API](single-database-manage.md#rest-api)

> [!IMPORTANT]
> Anleitungen und Überlegungen zur Skalierung finden Sie unter [Skalieren eines Singletons](single-database-scale.md).

## <a name="general-purpose---serverless-compute---gen5"></a>Universell – serverloses Computing – Gen5

Der [serverlose Computetarif](serverless-tier-overview.md) ist derzeit nur auf Gen5-Hardware verfügbar.

### <a name="gen5-compute-generation-part-1"></a>Computegeneration Gen5 (Teil 1)

|Computegröße (Dienstziel)|GP_S_Gen5_1|GP_S_Gen5_2|GP_S_Gen5_4|GP_S_Gen5_6|GP_S_Gen5_8|
|:--- | --: |--: |--: |--: |--: |
|Computegeneration|Gen5|Gen5|Gen5|Gen5|Gen5|
|Min-Max V-Kerne|0,5 - 1|0,5 - 2|0,5 - 4|0,75 - 6|1,0 - 8|
|Min-Max-Arbeitsspeicher (GB)|2,02 - 3|2,05 - 6|2,10 - 12|2,25 - 18|3,00 - 24|
|Min. bis max. Verzögerung für automatische Pause (Minuten)|60-10080|60-10080|60-10080|60-10080|60-10080|
|Columnstore-Unterstützung|Ja<sup>1</sup>|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|–|–|–|–|–|
|Maximale Datengröße (GB)|512|1024|1024|1024|1536|
|Max. Protokollgröße (GB) <sup>2</sup>|154|307|307|307|461|
|Max. Datengröße von TempDB (GB)|32|64|128|192|256|
|Speichertyp|Remote-SSD|Remote-SSD|Remote-SSD|Remote-SSD|Remote-SSD|
|E/A-Wartezeit (ungefähr)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|
|Max. Daten-IOPS <sup>3</sup>|320|640|1280|1920|2560|
|Max. Protokollrate (MBit/s)|4,5|9|18|27|36|
|Max. gleichzeitige Worker (Anforderungen)|75|150|300|450|600|
|Max. gleichzeitige Sitzungen|30.000|30.000|30.000|30.000|30.000|
|Anzahl von Replikaten|1|1|1|1|1|
|Multi-AZ|–|–|–|–|–|
|Horizontale Leseskalierung|–|–|–|–|–|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

<sup>1</sup> Dienstziele mit niedrigeren Konfigurationen für die maximale Anzahl virtueller Kerne verfügen möglicherweise nicht über genügend Arbeitsspeicher zum Erstellen und Verwenden von Columnstore-Indizes.  Wenn beim Columnstore Leistungsprobleme auftreten, erhöhen Sie die Konfiguration für die maximalen virtuellen Kerne, um den maximal verfügbaren Arbeitsspeicher zu steigern.  

<sup>2</sup> Für dokumentierte Werte der maximalen Datengröße. Bei einer Reduzierung der maximalen Datengröße reduziert sich die maximale Protokollgröße proportional.

<sup>3</sup> Der maximale Wert für E/A-Größen im Bereich von 8 KB bis 64 KB. Die tatsächlichen IOPS sind von der Arbeitsauslastung abhängig. Weitere Informationen finden Sie unter [Daten-E/A-Governance](resource-limits-logical-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Computegeneration Gen5 (Teil 2)

|Computegröße (Dienstziel)|GP_S_Gen5_10|GP_S_Gen5_12|GP_S_Gen5_14|GP_S_Gen5_16|
|:--- | --: |--: |--: |--: |
|Computegeneration|Gen5|Gen5|Gen5|Gen5|
|Min-Max V-Kerne|1,25 - 10|1,50 - 12|1,75–14|2,00 - 16|
|Min-Max-Arbeitsspeicher (GB)|3,75 - 30|4,50 - 36|5,25 - 42|6,00 - 48|
|Min. bis max. Verzögerung für automatische Pause (Minuten)|60-10080|60-10080|60-10080|60-10080|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|–|–|–|–|
|Maximale Datengröße (GB)|1536|3072|3072|3072|
|Max. Protokollgröße (GB) <sup>1</sup>|461|461|461|922|
|Max. Datengröße von TempDB (GB)|320|384|448|512|
|Speichertyp|Remote-SSD|Remote-SSD|Remote-SSD|Remote-SSD|
|E/A-Wartezeit (ungefähr)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|
|Max. Daten-IOPS<sup>2</sup>|3200|3840|4480|5120|
|Max. Protokollrate (MBit/s)|45|50|50|50|
|Max. gleichzeitige Worker (Anforderungen)|750|900|1050|1200|
|Max. gleichzeitige Sitzungen|30.000|30.000|30.000|30.000|
|Anzahl von Replikaten|1|1|1|1|
|Multi-AZ|–|–|–|–|
|Horizontale Leseskalierung|–|–|–|–|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

<sup>1</sup> Für dokumentierte Werte der maximalen Datengröße. Bei einer Reduzierung der maximalen Datengröße reduziert sich die maximale Protokollgröße proportional.

<sup>2</sup> Der maximale Wert für E/A-Größen im Bereich von 8 KB bis 64 KB. Die tatsächlichen IOPS sind von der Arbeitsauslastung abhängig. Weitere Informationen finden Sie unter [Daten-E/A-Governance](resource-limits-logical-server.md#resource-governance).

### <a name="gen5-compute-generation-part-3"></a>Computegeneration Gen5 (Teil 3)

|Computegröße (Dienstziel)|GP_S_Gen5_18|GP_S_Gen5_20|GP_S_Gen5_24|GP_S_Gen5_32|GP_S_Gen5_40|
|:--- | --: |--: |--: |--: |--:|
|Computegeneration|Gen5|Gen5|Gen5|Gen5|Gen5|
|Min-Max V-Kerne|2,25 - 18|2,5-20|3-24|4-32|5-40|
|Min-Max-Arbeitsspeicher (GB)|6,75-54|7,5-60|9-72|12-96|15-120|
|Min. bis max. Verzögerung für automatische Pause (Minuten)|60-10080|60-10080|60-10080|60-10080|60-10080|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|–|–|–|–|–|
|Maximale Datengröße (GB)|3072|3072|4096|4096|4096|
|Max. Protokollgröße (GB) <sup>1</sup>|922|922|1024|1024|1024|
|Max. Datengröße von TempDB (GB)|576|640|768|1024|1280|
|Speichertyp|Remote-SSD|Remote-SSD|Remote-SSD|Remote-SSD|Remote-SSD|
|E/A-Wartezeit (ungefähr)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|
|Max. Daten-IOPS<sup>2</sup>|5760|6400|7680|10.240|12800|
|Max. Protokollrate (MBit/s)|50|50|50|50|50|
|Max. gleichzeitige Worker (Anforderungen)|1350|1500|1800|2400|3000|
|Max. gleichzeitige Sitzungen|30.000|30.000|30.000|30.000|30.000|
|Anzahl von Replikaten|1|1|1|1|1|
|Multi-AZ|–|–|–|–|–|
|Horizontale Leseskalierung|–|–|–|–|–|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

<sup>1</sup> Für dokumentierte Werte der maximalen Datengröße. Bei einer Reduzierung der maximalen Datengröße reduziert sich die maximale Protokollgröße proportional.

<sup>2</sup> Der maximale Wert für E/A-Größen im Bereich von 8 KB bis 64 KB. Die tatsächlichen IOPS sind von der Arbeitsauslastung abhängig. Weitere Informationen finden Sie unter [Daten-E/A-Governance](resource-limits-logical-server.md#resource-governance).


## <a name="hyperscale---provisioned-compute---gen4"></a>Hyperscale – bereitgestelltes Computing – Gen4

### <a name="gen4-compute-generation-part-1"></a>Computegeneration Gen4 (Teil 1)

|Computegröße (Dienstziel)|HS_Gen4_1|HS_Gen4_2|HS_Gen4_3|HS_Gen4_4|HS_Gen4_5|HS_Gen4_6|
|:--- | --: |--: |--: |---: | --: |--: |
|Computegeneration|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|V-Kerne|1|2|3|4|5|6|
|Arbeitsspeicher (GB)|7|14|21|28|35|42|
|[RBPEX](service-tier-hyperscale.md#compute)-Größe|3 × Arbeitsspeicher|3 × Arbeitsspeicher|3 × Arbeitsspeicher|3 × Arbeitsspeicher|3 × Arbeitsspeicher|3 × Arbeitsspeicher|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|–|–|–|–|–|–|
|Maximale Datengröße (TB)|100 |100 |100 |100 |100 |100|
|Maximale Protokollgröße (TB)|Unbegrenzt |Unbegrenzt |Unbegrenzt |Unbegrenzt |Unbegrenzt |Unbegrenzt |
|Max. Datengröße von TempDB (GB)|32|64|96|128|160|192|
|Speichertyp| [Hinweis 1](#notes) |[Hinweis 1](#notes)|[Hinweis 1](#notes) |[Hinweis 1](#notes) |[Hinweis 1](#notes) |[Hinweis 1](#notes) |
|Max. IOPS der lokalen SSD <sup>1</sup>|4000 |8.000 |12000 |16000 |20000 |24.000 |
|Max. Protokollrate (MBit/s)|100 |100 |100 |100 |100 |100 |
|E/A-Wartezeit (ungefähr)|[Hinweis 2](#notes)|[Hinweis 2](#notes)|[Hinweis 2](#notes)|[Hinweis 2](#notes)|[Hinweis 2](#notes)|[Hinweis 2](#notes)|
|Max. gleichzeitige Worker (Anforderungen)|200|400|600|800|1000|1200|
|Max. gleichzeitige Sitzungen|30.000|30.000|30.000|30.000|30.000|30.000|
|Sekundäre Replikate|0–4|0–4|0–4|0–4|0–4|0–4|
|Multi-AZ|–|–|–|–|–|–|
|Horizontale Leseskalierung|Ja|Ja|Ja|Ja|Ja|Ja|
|Vermerkdauer im Sicherungsspeicher|7 Tage|7 Tage|7 Tage|7 Tage|7 Tage|7 Tage|
|||

<sup>1</sup> Neben E/A-Vorgängen auf der lokalen SSD nutzen Workloads Remote-E/A-Vorgänge auf dem [Auslagerungsserver](service-tier-hyperscale.md#page-server). Der tatsächliche IOPS-Wert hängt von der Workload ab. Details dazu finden Sie unter [Daten-E/A-Governance](resource-limits-logical-server.md#resource-governance) und [Daten-E/A in Statistiken zur Ressourcenauslastung](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics).

### <a name="gen4-compute-generation-part-2"></a>Computegeneration Gen4 (Teil 2)

|Computegröße (Dienstziel)|HS_Gen4_7|HS_Gen4_8|HS_Gen4_9|HS_Gen4_10|HS_Gen4_16|HS_Gen4_24|
|:--- | ---: |--: |--: | --: |--: |--: |
|Computegeneration|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|V-Kerne|7|8|9|10|16|24|
|Arbeitsspeicher (GB)|49|56|63|70|112|159,5|
|[RBPEX](service-tier-hyperscale.md#compute)-Größe|3 × Arbeitsspeicher|3 × Arbeitsspeicher|3 × Arbeitsspeicher|3 × Arbeitsspeicher|3 × Arbeitsspeicher|3 × Arbeitsspeicher|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|–|–|–|–|–|–|
|Maximale Datengröße (TB)|100 |100 |100 |100 |100 |100 |
|Maximale Protokollgröße (TB)|Unbegrenzt |Unbegrenzt |Unbegrenzt |Unbegrenzt |Unbegrenzt |Unbegrenzt |
|Max. Datengröße von TempDB (GB)|224|256|288|320|512|768|
|Speichertyp| [Hinweis 1](#notes) |[Hinweis 1](#notes) |[Hinweis 1](#notes) |[Hinweis 1](#notes) |[Hinweis 1](#notes) |[Hinweis 1](#notes) |
|Max. IOPS der lokalen SSD <sup>1</sup>|28000 |32000 |36000 |40.000 |64000 |76800 |
|Max. Protokollrate (MBit/s)|100 |100 |100 |100 |100 |100 |
|E/A-Wartezeit (ungefähr)|[Hinweis 2](#notes)|[Hinweis 2](#notes)|[Hinweis 2](#notes)|[Hinweis 2](#notes)|[Hinweis 2](#notes)|[Hinweis 2](#notes)|
|Max. gleichzeitige Worker (Anforderungen)|1400|1600|1800|2000|3200|4800|
|Max. gleichzeitige Sitzungen|30.000|30.000|30.000|30.000|30.000|30.000|
|Sekundäre Replikate|0–4|0–4|0–4|0–4|0–4|0–4|
|Multi-AZ|–|–|–|–|–|–|
|Horizontale Leseskalierung|Ja|Ja|Ja|Ja|Ja|Ja|
|Vermerkdauer im Sicherungsspeicher|7 Tage|7 Tage|7 Tage|7 Tage|7 Tage|7 Tage|
|||

<sup>1</sup> Neben E/A-Vorgängen auf der lokalen SSD nutzen Workloads Remote-E/A-Vorgänge auf dem [Auslagerungsserver](service-tier-hyperscale.md#page-server). Der tatsächliche IOPS-Wert hängt von der Workload ab. Details dazu finden Sie unter [Daten-E/A-Governance](resource-limits-logical-server.md#resource-governance) und [Daten-E/A in Statistiken zur Ressourcenauslastung](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics).

## <a name="hyperscale---provisioned-compute---gen5"></a>Hyperscale – bereitgestelltes Computing – Gen5

### <a name="gen5-compute-generation-part-1"></a>Computegeneration Gen5 (Teil 1)

|Computegröße (Dienstziel)|HS_Gen5_2|HS_Gen5_4|HS_Gen5_6|HS_Gen5_8|HS_Gen5_10|HS_Gen5_12|HS_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Computegeneration|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|V-Kerne|2|4|6|8|10|12|14|
|Arbeitsspeicher (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|[RBPEX](service-tier-hyperscale.md#compute)-Größe|3 × Arbeitsspeicher|3 × Arbeitsspeicher|3 × Arbeitsspeicher|3 × Arbeitsspeicher|3 × Arbeitsspeicher|3 × Arbeitsspeicher|3 × Arbeitsspeicher|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|–|–|–|–|–|–|–|
|Maximale Datengröße (TB)|100 |100 |100 |100 |100 |100 |100|
|Maximale Protokollgröße (TB)|Unbegrenzt |Unbegrenzt |Unbegrenzt |Unbegrenzt |Unbegrenzt |Unbegrenzt |Unbegrenzt |
|Max. Datengröße von TempDB (GB)|64|128|192|256|320|384|448|
|Speichertyp| [Hinweis 1](#notes) |[Hinweis 1](#notes)|[Hinweis 1](#notes) |[Hinweis 1](#notes) |[Hinweis 1](#notes) |[Hinweis 1](#notes) |[Hinweis 1](#notes) |
|Max. IOPS der lokalen SSD <sup>1</sup>|8.000 |16000 |24.000 |32000 |40.000 |48000 |56000 |
|Max. Protokollrate (MBit/s)|100 |100 |100 |100 |100 |100 |100 |
|E/A-Wartezeit (ungefähr)|[Hinweis 2](#notes)|[Hinweis 2](#notes)|[Hinweis 2](#notes)|[Hinweis 2](#notes)|[Hinweis 2](#notes)|[Hinweis 2](#notes)|[Hinweis 2](#notes)|
|Max. gleichzeitige Worker (Anforderungen)|200|400|600|800|1000|1200|1400|
|Max. gleichzeitige Sitzungen|30.000|30.000|30.000|30.000|30.000|30.000|30.000|
|Sekundäre Replikate|0–4|0–4|0–4|0–4|0–4|0–4|0–4|
|Multi-AZ|–|–|–|–|–|–|–|
|Horizontale Leseskalierung|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Vermerkdauer im Sicherungsspeicher|7 Tage|7 Tage|7 Tage|7 Tage|7 Tage|7 Tage|7 Tage|
|||

<sup>1</sup> Neben E/A-Vorgängen auf der lokalen SSD nutzen Workloads Remote-E/A-Vorgänge auf dem [Auslagerungsserver](service-tier-hyperscale.md#page-server). Der tatsächliche IOPS-Wert hängt von der Workload ab. Details dazu finden Sie unter [Daten-E/A-Governance](resource-limits-logical-server.md#resource-governance) und [Daten-E/A in Statistiken zur Ressourcenauslastung](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics).

### <a name="gen5-compute-generation-part-2"></a>Computegeneration Gen5 (Teil 2)

|Computegröße (Dienstziel)|HS_Gen5_16|HS_Gen5_18|HS_Gen5_20|HS_Gen_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: |--: |--: |
|Computegeneration|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|V-Kerne|16|18|20|24|32|40|80|
|Arbeitsspeicher (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|[RBPEX](service-tier-hyperscale.md#compute)-Größe|3 × Arbeitsspeicher|3 × Arbeitsspeicher|3 × Arbeitsspeicher|3 × Arbeitsspeicher|3 × Arbeitsspeicher|3 × Arbeitsspeicher|3 × Arbeitsspeicher|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|–|–|–|–|–|–|–|
|Maximale Datengröße (TB)|100 |100 |100 |100 |100 |100 |100 |
|Maximale Protokollgröße (TB)|Unbegrenzt |Unbegrenzt |Unbegrenzt |Unbegrenzt |Unbegrenzt |Unbegrenzt |Unbegrenzt |
|Max. Datengröße von TempDB (GB)|512|576|640|768|1024|1280|2560|
|Speichertyp| [Hinweis 1](#notes) |[Hinweis 1](#notes)|[Hinweis 1](#notes)|[Hinweis 1](#notes) |[Hinweis 1](#notes) |[Hinweis 1](#notes) |[Hinweis 1](#notes) |
|Max. IOPS der lokalen SSD <sup>1</sup>|64000 |72000 |80.000 |96000 |128000 |160000 |204800 |
|Max. Protokollrate (MBit/s)|100 |100 |100 |100 |100 |100 |100 |
|E/A-Wartezeit (ungefähr)|[Hinweis 2](#notes)|[Hinweis 2](#notes)|[Hinweis 2](#notes)|[Hinweis 2](#notes)|[Hinweis 2](#notes)|[Hinweis 2](#notes)|[Hinweis 2](#notes)|
|Max. gleichzeitige Worker (Anforderungen)|1600|1800|2000|2400|3200|4000|8.000|
|Max. gleichzeitige Sitzungen|30.000|30.000|30.000|30.000|30.000|30.000|30.000|
|Sekundäre Replikate|0–4|0–4|0–4|0–4|0–4|0–4|0–4|
|Multi-AZ|–|–|–|–|–|–|–|
|Horizontale Leseskalierung|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Vermerkdauer im Sicherungsspeicher|7 Tage|7 Tage|7 Tage|7 Tage|7 Tage|7 Tage|7 Tage|
|||

<sup>1</sup> Neben E/A-Vorgängen auf der lokalen SSD nutzen Workloads Remote-E/A-Vorgänge auf dem [Auslagerungsserver](service-tier-hyperscale.md#page-server). Der tatsächliche IOPS-Wert hängt von der Workload ab. Details dazu finden Sie unter [Daten-E/A-Governance](resource-limits-logical-server.md#resource-governance) und [Daten-E/A in Statistiken zur Ressourcenauslastung](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics).

#### <a name="notes"></a>Notizen

**Hinweis 1:** Hyperscale ist eine mehrschichtige Architektur mit separaten Compute- und Speicherkomponenten: [Architektur der Dienstebene „Hyperscale“](service-tier-hyperscale.md#distributed-functions-architecture)

**Hinweis 2:** Die Latenz beträgt 1–2 ms für Daten auf der SSD für das lokale Computereplikat, die Datenseiten mit dem häufigsten Zugriff zwischenspeichert. Für Daten, die von Auslagerungsservern abgerufen werden, gilt eine höhere Latenz.

## <a name="hyperscale---provisioned-compute---dc-series"></a>Hyperscale – bereitgestelltes Computing – DC-Serie

|Computegröße (Dienstziel)|HS_DC_2|HS_DC_4|HS_DC_6|HS_DC_8|
|:--- | --: |--: |--: |--: |---: | 
|Computegeneration|DC-Serie|DC-Serie|DC-Serie|DC-Serie|
|V-Kerne|2|4|6|8|
|Arbeitsspeicher (GB)|9|18|27|36|
|[RBPEX](service-tier-hyperscale.md#compute)-Größe|3 × Arbeitsspeicher|3 × Arbeitsspeicher|3 × Arbeitsspeicher|3 × Arbeitsspeicher|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|–|–|–|–|
|Maximale Datengröße (TB)|100 |100 |100 |100 |
|Maximale Protokollgröße (TB)|Unbegrenzt |Unbegrenzt |Unbegrenzt |Unbegrenzt |
|Max. Datengröße von TempDB (GB)|64|128|192|256|
|Speichertyp| [Hinweis 1](#notes) |[Hinweis 1](#notes)|[Hinweis 1](#notes) |[Hinweis 1](#notes) |
|Max. IOPS der lokalen SSD <sup>1</sup>|14000|28000|42000|44800|
|Max. Protokollrate (MBit/s)|100 |100 |100 |100 |
|E/A-Wartezeit (ungefähr)|[Hinweis 2](#notes)|[Hinweis 2](#notes)|[Hinweis 2](#notes)|[Hinweis 2](#notes)|
|Max. gleichzeitige Worker (Anforderungen)|160|320|480|640|
|Max. gleichzeitige Sitzungen|30.000|30.000|30.000|30.000|
|Sekundäre Replikate|0–4|0–4|0–4|0–4|
|Multi-AZ|–|–|–|–|
|Horizontale Leseskalierung|Ja|Ja|Ja|Ja|
|Vermerkdauer im Sicherungsspeicher|7 Tage|7 Tage|7 Tage|7 Tage|
|||

<sup>1</sup> Neben E/A-Vorgängen auf der lokalen SSD nutzen Workloads Remote-E/A-Vorgänge auf dem [Auslagerungsserver](service-tier-hyperscale.md#page-server). Der tatsächliche IOPS-Wert hängt von der Workload ab. Details dazu finden Sie unter [Daten-E/A-Governance](resource-limits-logical-server.md#resource-governance) und [Daten-E/A in Statistiken zur Ressourcenauslastung](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics).

### <a name="notes"></a>Notizen

**Hinweis 1:** Hyperscale ist eine mehrschichtige Architektur mit separaten Compute- und Speicherkomponenten: [Architektur der Dienstebene „Hyperscale“](service-tier-hyperscale.md#distributed-functions-architecture)

**Hinweis 2:** Die Latenz beträgt 1–2 ms für Daten auf der SSD für das lokale Computereplikat, die Datenseiten mit dem häufigsten Zugriff zwischenspeichert. Für Daten, die von Auslagerungsservern abgerufen werden, gilt eine höhere Latenz.

## <a name="general-purpose---provisioned-compute---gen4"></a>Universell – bereitgestelltes Computing – Gen4

> [!IMPORTANT]
> Neue Gen4-Datenbanken werden in den Regionen „Australien, Osten“ und „Brasilien, Süden“ nicht mehr unterstützt.

### <a name="gen4-compute-generation-part-1"></a>Computegeneration Gen4 (Teil 1)

|Computegröße (Dienstziel)|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Computegeneration|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|V-Kerne|1|2|3|4|5|6|
|Arbeitsspeicher (GB)|7|14|21|28|35|42|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|–|–|–|–|–|–|
|Maximale Datengröße (GB)|1024|1024|1536|1536|1536|3072|
|Max. Protokollgröße (GB) <sup>1</sup>|307|307|461|461|461|922|
|Max. Datengröße von TempDB (GB)|32|64|96|128|160|192|
|Speichertyp|Remote-SSD|Remote-SSD|Remote-SSD|Remote-SSD|Remote-SSD|Remote-SSD|
|E/A-Wartezeit (ungefähr)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|
|Max. Daten-IOPS<sup>2</sup>|320|640|960|1280|1600|1920|
|Max. Protokollrate (MBit/s)|4,5|9|13,5|18|22,5|27|
|Max. gleichzeitige Worker (Anforderungen)|200|400|600|800|1000|1200|
|Max. gleichzeitige Sitzungen|30.000|30.000|30.000|30.000|30.000|30.000|
|Anzahl von Replikaten|1|1|1|1|1|1|
|Multi-AZ|–|–|–|–|–|–|
|Horizontale Leseskalierung|–|–|–|–|–|–|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

<sup>1</sup> Für dokumentierte Werte der maximalen Datengröße. Bei einer Reduzierung der maximalen Datengröße reduziert sich die maximale Protokollgröße proportional.

<sup>2</sup> Der maximale Wert für E/A-Größen im Bereich von 8 KB bis 64 KB. Die tatsächlichen IOPS sind von der Arbeitsauslastung abhängig. Weitere Informationen finden Sie unter [Daten-E/A-Governance](resource-limits-logical-server.md#resource-governance).

### <a name="gen4-compute-generation-part-2"></a>Computegeneration Gen4 (Teil 2)

|Computegröße (Dienstziel)|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Computegeneration|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|V-Kerne|7|8|9|10|16|24|
|Arbeitsspeicher (GB)|49|56|63|70|112|159,5|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|–|–|–|–|–|–|
|Maximale Datengröße (GB)|3072|3072|3072|3072|4096|4096|
|Max. Protokollgröße (GB) <sup>1</sup>|922|922|922|922|1229|1229|
|Max. Datengröße von TempDB (GB)|224|256|288|320|512|768|
|Speichertyp|Remote-SSD|Remote-SSD|Remote-SSD|Remote-SSD|Remote-SSD|Remote-SSD|
|E/A-Wartezeit (ungefähr)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)
|Max. Daten-IOPS<sup>2</sup>|2240|2560|2880|3200|5120|7680|
|Max. Protokollrate (MBit/s)|31,5|36|40,5|45|50|50|
|Max. gleichzeitige Worker (Anforderungen)|1400|1600|1800|2000|3200|4800|
|Max. gleichzeitige Sitzungen|30.000|30.000|30.000|30.000|30.000|30.000|
|Anzahl von Replikaten|1|1|1|1|1|1|
|Multi-AZ|–|–|–|–|–|–|
|Horizontale Leseskalierung|–|–|–|–|–|–|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

<sup>1</sup> Für dokumentierte Werte der maximalen Datengröße. Bei einer Reduzierung der maximalen Datengröße reduziert sich die maximale Protokollgröße proportional.

<sup>2</sup> Der maximale Wert für E/A-Größen im Bereich von 8 KB bis 64 KB. Die tatsächlichen IOPS sind von der Arbeitsauslastung abhängig. Weitere Informationen finden Sie unter [Daten-E/A-Governance](resource-limits-logical-server.md#resource-governance).

## <a name="general-purpose---provisioned-compute---gen5"></a>Universell – bereitgestelltes Computing – Gen5

### <a name="gen5-compute-generation-part-1"></a>Computegeneration Gen5 (Teil 1)

|Computegröße (Dienstziel)|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Computegeneration|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|V-Kerne|2|4|6|8|10|12|14|
|Arbeitsspeicher (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|–|–|–|–|–|–|–|
|Maximale Datengröße (GB)|1024|1024|1536|1536|1536|3072|3072|
|Max. Protokollgröße (GB) <sup>1</sup>|307|307|461|461|461|922|922|
|Max. Datengröße von TempDB (GB)|64|128|192|256|320|384|384|
|Speichertyp|Remote-SSD|Remote-SSD|Remote-SSD|Remote-SSD|Remote-SSD|Remote-SSD|Remote-SSD|
|E/A-Wartezeit (ungefähr)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|
|Max. Daten-IOPS<sup>2</sup>|640|1280|1920|2560|3200|3840|4480|
|Max. Protokollrate (MBit/s)|9|18|27|36|45|50|50|
|Max. gleichzeitige Worker (Anforderungen)|200|400|600|800|1000|1200|1400|
|Max. gleichzeitige Sitzungen|30.000|30.000|30.000|30.000|30.000|30.000|30.000|
|Anzahl von Replikaten|1|1|1|1|1|1|1|
|Multi-AZ|[In der Vorschau verfügbar](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[In der Vorschau verfügbar](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[In der Vorschau verfügbar](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[In der Vorschau verfügbar](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[In der Vorschau verfügbar](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[In der Vorschau verfügbar](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[In der Vorschau verfügbar](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|
|Horizontale Leseskalierung|–|–|–|–|–|–|–|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

<sup>1</sup> Für dokumentierte Werte der maximalen Datengröße. Bei einer Reduzierung der maximalen Datengröße reduziert sich die maximale Protokollgröße proportional.

<sup>2</sup> Der maximale Wert für E/A-Größen im Bereich von 8 KB bis 64 KB. Die tatsächlichen IOPS sind von der Arbeitsauslastung abhängig. Weitere Informationen finden Sie unter [Daten-E/A-Governance](resource-limits-logical-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Computegeneration Gen5 (Teil 2)

|Computegröße (Dienstziel)|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Computegeneration|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|V-Kerne|16|18|20|24|32|40|80|
|Arbeitsspeicher (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|–|–|–|–|–|–|–|
|Maximale Datengröße (GB)|3072|3072|3072|4096|4096|4096|4096|
|Max. Protokollgröße (GB) <sup>1</sup>|922|922|922|1024|1024|1024|1024|
|Max. Datengröße von TempDB (GB)|512|576|640|768|1024|1280|2560|
|Speichertyp|Remote-SSD|Remote-SSD|Remote-SSD|Remote-SSD|Remote-SSD|Remote-SSD|Remote-SSD|
|E/A-Wartezeit (ungefähr)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|
|Max. Daten-IOPS<sup>2</sup>|5120|5760|6400|7680|10.240|12800|12800|
|Max. Protokollrate (MBit/s)|50|50|50|50|50|50|50|
|Max. gleichzeitige Worker (Anforderungen)|1600|1800|2000|2400|3200|4000|8.000|
|Max. gleichzeitige Sitzungen|30.000|30.000|30.000|30.000|30.000|30.000|30.000|
|Anzahl von Replikaten|1|1|1|1|1|1|1|
|Multi-AZ|[In der Vorschau verfügbar](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[In der Vorschau verfügbar](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[In der Vorschau verfügbar](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[In der Vorschau verfügbar](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[In der Vorschau verfügbar](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[In der Vorschau verfügbar](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[In der Vorschau verfügbar](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|
|Horizontale Leseskalierung|–|–|–|–|–|–|–|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

<sup>1</sup> Für dokumentierte Werte der maximalen Datengröße. Bei einer Reduzierung der maximalen Datengröße reduziert sich die maximale Protokollgröße proportional.

<sup>2</sup> Der maximale Wert für E/A-Größen im Bereich von 8 KB bis 64 KB. Die tatsächlichen IOPS sind von der Arbeitsauslastung abhängig. Weitere Informationen finden Sie unter [Daten-E/A-Governance](resource-limits-logical-server.md#resource-governance).

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Universell – bereitgestelltes Computing – Fsv2-Serie

### <a name="fsv2-series-compute-generation-part-1"></a>Computegeneration der Fsv2-Serie (Teil 1)

|Computegröße (Dienstziel)|GP_Fsv2_8|GP_Fsv2_10|GP_Fsv2_12|GP_Fsv2_14| GP_Fsv2_16|
|:---| ---:|---:|---:|---:|---:|
|Computegeneration|Fsv2-Serie|Fsv2-Serie|Fsv2-Serie|Fsv2-Serie|Fsv2-Serie|
|V-Kerne|8|10|12|14|16|
|Arbeitsspeicher (GB)|15,1|18,9|22,7|26,5|30,2|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|–|–|–|–|–|
|Maximale Datengröße (GB)|1024|1024|1024|1024|1536|
|Max. Protokollgröße (GB) <sup>1</sup>|336|336|336|336|512|
|Max. Datengröße von TempDB (GB)|37|46|56|65|74|
|Speichertyp|Remote-SSD|Remote-SSD|Remote-SSD|Remote-SSD|Remote-SSD|
|E/A-Wartezeit (ungefähr)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|
|Max. Daten-IOPS<sup>2</sup>|2560|3200|3840|4480|5120|
|Max. Protokollrate (MBit/s)|36|45|50|50|50|
|Max. gleichzeitige Worker (Anforderungen)|400|500|600|700|800|
|Maximale Anzahl gleichzeitiger Anmeldungen|800|1000|1200|1400|1600|
|Max. gleichzeitige Sitzungen|30.000|30.000|30.000|30.000|30.000|
|Anzahl von Replikaten|1|1|1|1|1|
|Multi-AZ|–|–|–|–|–|
|Horizontale Leseskalierung|–|–|–|–|–|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

<sup>1</sup> Für dokumentierte Werte der maximalen Datengröße. Bei einer Reduzierung der maximalen Datengröße reduziert sich die maximale Protokollgröße proportional.

<sup>2</sup> Der maximale Wert für E/A-Größen im Bereich von 8 KB bis 64 KB. Die tatsächlichen IOPS sind von der Arbeitsauslastung abhängig. Weitere Informationen finden Sie unter [Daten-E/A-Governance](resource-limits-logical-server.md#resource-governance).

### <a name="fsv2-series-compute-generation-part-2"></a>Computegeneration der Fsv2-Serie (Teil 2)

|Computegröße (Dienstziel)|GP_Fsv2_18|GP_Fsv2_20|GP_Fsv2_24|GP_Fsv2_32| GP_Fsv2_36|GP_Fsv2_72|
|:---| ---:|---:|---:|---:|---:|---:|
|Computegeneration|Fsv2-Serie|Fsv2-Serie|Fsv2-Serie|Fsv2-Serie|Fsv2-Serie|Fsv2-Serie|
|V-Kerne|18|20|24|32|36|72|
|Arbeitsspeicher (GB)|34,0|37,8|45,4|60,5|68,0|136,0|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|–|–|–|–|–|–|
|Maximale Datengröße (GB)|1536|1536|1536|3072|3072|4096|
|Max. Protokollgröße (GB) <sup>1</sup>|512|512|512|1024|1024|1024|
|Max. Datengröße von TempDB (GB)|83|93|111|148|167|333|
|Speichertyp|Remote-SSD|Remote-SSD|Remote-SSD|Remote-SSD|Remote-SSD|Remote-SSD|
|E/A-Wartezeit (ungefähr)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|
|Max. Daten-IOPS<sup>2</sup>|5760|6400|7680|10.240|11.520|12800|
|Max. Protokollrate (MBit/s)|50|50|50|50|50|50|
|Max. gleichzeitige Worker (Anforderungen)|900|1000|1200|1600|1800|3600|
|Maximale Anzahl gleichzeitiger Anmeldungen|1800|2000|2400|3200|3600|7200|
|Max. gleichzeitige Sitzungen|30.000|30.000|30.000|30.000|30.000|30.000|
|Anzahl von Replikaten|1|1|1|1|1|1|
|Multi-AZ|–|–|–|–|–|–|
|Horizontale Leseskalierung|–|–|–|–|–|–|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

<sup>1</sup> Für dokumentierte Werte der maximalen Datengröße. Bei einer Reduzierung der maximalen Datengröße reduziert sich die maximale Protokollgröße proportional.

<sup>2</sup> Der maximale Wert für E/A-Größen im Bereich von 8 KB bis 64 KB. Die tatsächlichen IOPS sind von der Arbeitsauslastung abhängig. Weitere Informationen finden Sie unter [Daten-E/A-Governance](resource-limits-logical-server.md#resource-governance).

## <a name="general-purpose---provisioned-compute---dc-series"></a>Universell – bereitgestelltes Computing – DC-Serie

|Computegröße (Dienstziel)|GP_DC_2|GP_DC_4|GP_DC_6|GP_DC_8| 
|:---| ---:|---:|---:|---:|
|Computegeneration|DC-Serie|DC-Serie|DC-Serie|DC-Serie|
|V-Kerne|2|4|6|8|
|Arbeitsspeicher (GB)|9|18|27|36|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|–|–|–|–|
|Maximale Datengröße (GB)|1024|1536|3072|3072|
|Max. Protokollgröße (GB) <sup>1</sup>|307|461|922|922|
|Max. Datengröße von TempDB (GB)|64|128|192|256|
|Speichertyp|Remote-SSD|Remote-SSD|Remote-SSD|Remote-SSD|
|E/A-Wartezeit (ungefähr)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|
|Max. Daten-IOPS<sup>2</sup>|640|1280|1920|2560|
|Max. Protokollrate (MBit/s)|9|18|27|36|
|Max. gleichzeitige Worker (Anforderungen)|160|320|480|640|
|Max. gleichzeitige Sitzungen|30.000|30.000|30.000|30.000|
|Anzahl von Replikaten|1|1|1|1|
|Multi-AZ|–|–|–|–|
|Horizontale Leseskalierung|–|–|–|–|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

<sup>1</sup> Für dokumentierte Werte der maximalen Datengröße. Bei einer Reduzierung der maximalen Datengröße reduziert sich die maximale Protokollgröße proportional.

<sup>2</sup> Der maximale Wert für E/A-Größen im Bereich von 8 KB bis 64 KB. Die tatsächlichen IOPS sind von der Arbeitsauslastung abhängig. Weitere Informationen finden Sie unter [Daten-E/A-Governance](resource-limits-logical-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---gen4"></a>Unternehmenskritisch – bereitgestelltes Computing – Gen4

> [!IMPORTANT]
> Neue Gen4-Datenbanken werden in den Regionen „Australien, Osten“ und „Brasilien, Süden“ nicht mehr unterstützt.

### <a name="gen4-compute-generation-part-1"></a>Computegeneration Gen4 (Teil 1)

|Computegröße (Dienstziel)|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Computegeneration|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|V-Kerne|1|2|3|4|5|6|
|Arbeitsspeicher (GB)|7|14|21|28|35|42|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|1|2|3|4|5|6|
|Speichertyp|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|Maximale Datengröße (GB)|1024|1024|1024|1024|1024|1024|
|Max. Protokollgröße (GB) <sup>1</sup>|307|307|307|307|307|307|
|Max. Datengröße von TempDB (GB)|32|64|96|128|160|192|
|[Max. Größe des lokalen Speichers](resource-limits-logical-server.md#storage-space-governance) (GB)|1\.356|1\.356|1\.356|1\.356|1\.356|1\.356|
|E/A-Wartezeit (ungefähr)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|
|Max. Daten-IOPS<sup>2</sup>|4\.000|8\.000|12.000|16.000|20.000|24.000|
|Max. Protokollrate (MBit/s)|8|16|24|32|40|48|
|Max. gleichzeitige Worker (Anforderungen)|200|400|600|800|1000|1200|
|Maximale Anzahl gleichzeitiger Anmeldungen|200|400|600|800|1000|1200|
|Max. gleichzeitige Sitzungen|30.000|30.000|30.000|30.000|30.000|30.000|
|Anzahl von Replikaten|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Horizontale Leseskalierung|Ja|Ja|Ja|Ja|Ja|Ja|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

<sup>1</sup> Für dokumentierte Werte der maximalen Datengröße. Bei einer Reduzierung der maximalen Datengröße reduziert sich die maximale Protokollgröße proportional.

<sup>2</sup> Der maximale Wert für E/A-Größen im Bereich von 8 KB bis 64 KB. Die tatsächlichen IOPS sind von der Arbeitsauslastung abhängig. Weitere Informationen finden Sie unter [Daten-E/A-Governance](resource-limits-logical-server.md#resource-governance).

### <a name="gen4-compute-generation-part-2"></a>Computegeneration Gen4 (Teil 2)

|Computegröße (Dienstziel)|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Computegeneration|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|V-Kerne|7|8|9|10|16|24|
|Arbeitsspeicher (GB)|49|56|63|70|112|159,5|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|7|8|9.5|11|20|36|
|Speichertyp|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|Maximale Datengröße (GB)|1024|1024|1024|1024|1024|1024|
|Max. Protokollgröße (GB) <sup>1</sup>|307|307|307|307|307|307|
|Max. Datengröße von TempDB (GB)|224|256|288|320|512|768|
|[Max. Größe des lokalen Speichers](resource-limits-logical-server.md#storage-space-governance) (GB)|1\.356|1\.356|1\.356|1\.356|1\.356|1\.356|
|E/A-Wartezeit (ungefähr)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|
|Max. Daten-IOPS<sup>2</sup>|28.000|32.000|36.000|40.000|64.000|76.800|
|Max. Protokollrate (MBit/s)|56|64|64|64|64|64|
|Max. gleichzeitige Worker (Anforderungen)|1400|1600|1800|2000|3200|4800|
|Max. gleichzeitige Anmeldungen (Anforderungen)|1400|1600|1800|2000|3200|4800|
|Max. gleichzeitige Sitzungen|30.000|30.000|30.000|30.000|30.000|30.000|
|Anzahl von Replikaten|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Horizontale Leseskalierung|Ja|Ja|Ja|Ja|Ja|Ja|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

<sup>1</sup> Für dokumentierte Werte der maximalen Datengröße. Bei einer Reduzierung der maximalen Datengröße reduziert sich die maximale Protokollgröße proportional.

<sup>2</sup> Der maximale Wert für E/A-Größen im Bereich von 8 KB bis 64 KB. Die tatsächlichen IOPS sind von der Arbeitsauslastung abhängig. Weitere Informationen finden Sie unter [Daten-E/A-Governance](resource-limits-logical-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---gen5"></a>Unternehmenskritisch – bereitgestelltes Computing – Gen5

### <a name="gen5-compute-generation-part-1"></a>Computegeneration Gen5 (Teil 1)

|Computegröße (Dienstziel)|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Computegeneration|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|V-Kerne|2|4|6|8|10|12|14|
|Arbeitsspeicher (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|1,57|3,14|4.71|6,28|8,65|11,02|13,39|
|Maximale Datengröße (GB)|1024|1024|1536|1536|1536|3072|3072|
|Max. Protokollgröße (GB) <sup>1</sup>|307|307|461|461|461|922|922|
|Max. Datengröße von TempDB (GB)|64|128|192|256|320|384|448|
|[Max. Größe des lokalen Speichers](resource-limits-logical-server.md#storage-space-governance) (GB)|4829|4829|4829|4829|4829|4829|4829|
|Speichertyp|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|E/A-Wartezeit (ungefähr)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|
|Max. Daten-IOPS<sup>2</sup>|8.000|16.000|24.000|32.000|40.000|48.000|56.000|
|Max. Protokollrate (MBit/s)|24|48|72|96|96|96|96|
|Max. gleichzeitige Worker (Anforderungen)|200|400|600|800|1000|1200|1400|
|Maximale Anzahl gleichzeitiger Anmeldungen|200|400|600|800|1000|1200|1400|
|Max. gleichzeitige Sitzungen|30.000|30.000|30.000|30.000|30.000|30.000|30.000|
|Anzahl von Replikaten|4|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Horizontale Leseskalierung|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

<sup>1</sup> Für dokumentierte Werte der maximalen Datengröße. Bei einer Reduzierung der maximalen Datengröße reduziert sich die maximale Protokollgröße proportional.

<sup>2</sup> Der maximale Wert für E/A-Größen im Bereich von 8 KB bis 64 KB. Die tatsächlichen IOPS sind von der Arbeitsauslastung abhängig. Weitere Informationen finden Sie unter [Daten-E/A-Governance](resource-limits-logical-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Computegeneration Gen5 (Teil 2)

|Computegröße (Dienstziel)|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Computegeneration|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|V-Kerne|16|18|20|24|32|40|80|
|Arbeitsspeicher (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|15,77|18,14|20,51|25,25|37,94|52.23|131,64|
|Maximale Datengröße (GB)|3072|3072|3072|4096|4096|4096|4096|
|Max. Protokollgröße (GB) <sup>1</sup>|922|922|922|1024|1024|1024|1024|
|Max. Datengröße von TempDB (GB)|512|576|640|768|1024|1280|2560|
|[Max. Größe des lokalen Speichers](resource-limits-logical-server.md#storage-space-governance) (GB)|4829|4829|4829|4829|4829|4829|4829|
|Speichertyp|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|E/A-Wartezeit (ungefähr)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|
|Max. Daten-IOPS<sup>2</sup>|64.000|72.000|80.000|96.000|128.000|160.000|204.800|
|Max. Protokollrate (MBit/s)|96|96|96|96|96|96|96|
|Max. gleichzeitige Worker (Anforderungen)|1600|1800|2000|2400|3200|4000|8.000|
|Maximale Anzahl gleichzeitiger Anmeldungen|1600|1800|2000|2400|3200|4000|8.000|
|Max. gleichzeitige Sitzungen|30.000|30.000|30.000|30.000|30.000|30.000|30.000|
|Anzahl von Replikaten|4|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Horizontale Leseskalierung|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

<sup>1</sup> Für dokumentierte Werte der maximalen Datengröße. Bei einer Reduzierung der maximalen Datengröße reduziert sich die maximale Protokollgröße proportional.

<sup>2</sup> Der maximale Wert für E/A-Größen im Bereich von 8 KB bis 64 KB. Die tatsächlichen IOPS sind von der Arbeitsauslastung abhängig. Weitere Informationen finden Sie unter [Daten-E/A-Governance](resource-limits-logical-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---m-series"></a>Unternehmenskritisch – bereitgestelltes Computing – M-Serie

### <a name="m-series-compute-generation-part-1"></a>Computegeneration der M-Serie (Teil 1)

|Computegröße (Dienstziel)|BC_M_8|BC_M_10|BC_M_12|BC_M_14|BC_M_16|BC_M_18|
|:---| ---:|---:|---:|---:|---:|---:|
|Computegeneration|M-Serie|M-Serie|M-Serie|M-Serie|M-Serie|M-Serie|
|V-Kerne|8|10|12|14|16|18|
|Arbeitsspeicher (GB)|235,4|294,3|353,2|412,0|470,9|529,7|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|64|80|96|112|128|150|
|Maximale Datengröße (GB)|512|640|768|896|1024|1152|
|Max. Protokollgröße (GB) <sup>1</sup>|171|213|256|299|341|384|
|Max. Datengröße von TempDB (GB)|256|320|384|448|512|576|
|[Max. Größe des lokalen Speichers](resource-limits-logical-server.md#storage-space-governance) (GB)|13.836|13.836|13.836|13.836|13.836|13.836|
|Speichertyp|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|E/A-Wartezeit (ungefähr)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|
|Max. Daten-IOPS<sup>2</sup>|12.499|15.624|18.748|21.873|24.998|28.123|
|Max. Protokollrate (MBit/s)|48|60|72|84|96|108|
|Max. gleichzeitige Worker (Anforderungen)|800|1\.000|1\.200|1\.400|1\.600|1\.800|
|Maximale Anzahl gleichzeitiger Anmeldungen|800|1\.000|1\.200|1\.400|1\.600|1\.800|
|Max. gleichzeitige Sitzungen|30.000|30.000|30.000|30.000|30.000|30.000|
|Anzahl von Replikaten|4|4|4|4|4|4|
|Multi-AZ|Nein|Nein|Nein|Nein|Nein|Nein|
|Horizontale Leseskalierung|Ja|Ja|Ja|Ja|Ja|Ja|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

<sup>1</sup> Für dokumentierte Werte der maximalen Datengröße. Bei einer Reduzierung der maximalen Datengröße reduziert sich die maximale Protokollgröße proportional.

<sup>2</sup> Der maximale Wert für E/A-Größen im Bereich von 8 KB bis 64 KB. Die tatsächlichen IOPS sind von der Arbeitsauslastung abhängig. Weitere Informationen finden Sie unter [Daten-E/A-Governance](resource-limits-logical-server.md#resource-governance).

### <a name="m-series-compute-generation-part-2"></a>Computegeneration der M-Serie (Teil 2)

|Computegröße (Dienstziel)|BC_M_20|BC_M_24|BC_M_32|BC_M_64|BC_M_128|
|:---| ---:|---:|---:|---:|---:|
|Computegeneration|M-Serie|M-Serie|M-Serie|M-Serie|M-Serie|
|V-Kerne|20|24|32|64|128|
|Arbeitsspeicher (GB)|588,6|706,3|941,8|1883,5|3767,0|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|172|216|304|704|1768|
|Maximale Datengröße (GB)|1280|1536|2048|4096|4096|
|Max. Protokollgröße (GB) <sup>1</sup>|427|512|683|1024|1024|
|Max. Datengröße von TempDB (GB)|640|768|1024|2048|4096|
|[Max. Größe des lokalen Speichers](resource-limits-logical-server.md#storage-space-governance) (GB)|13.836|13.836|13.836|13.836|13.836|
|Speichertyp|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|E/A-Wartezeit (ungefähr)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|
|Max. Daten-IOPS<sup>2</sup>|31.248|37.497|49.996|99.993|160.000|
|Max. Protokollrate (MBit/s)|120|144|192|264|264|
|Max. gleichzeitige Worker (Anforderungen)|2\.000|2\.400|3\.200|6\.400|12.800|
|Maximale Anzahl gleichzeitiger Anmeldungen|2\.000|2\.400|3\.200|6\.400|12.800|
|Max. gleichzeitige Sitzungen|30.000|30.000|30.000|30.000|30.000|
|Anzahl von Replikaten|4|4|4|4|4|
|Multi-AZ|Nein|Nein|Nein|Nein|Nein|
|Horizontale Leseskalierung|Ja|Ja|Ja|Ja|Ja|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

<sup>1</sup> Für dokumentierte Werte der maximalen Datengröße. Bei einer Reduzierung der maximalen Datengröße reduziert sich die maximale Protokollgröße proportional.

<sup>2</sup> Der maximale Wert für E/A-Größen im Bereich von 8 KB bis 64 KB. Die tatsächlichen IOPS sind von der Arbeitsauslastung abhängig. Weitere Informationen finden Sie unter [Daten-E/A-Governance](resource-limits-logical-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---dc-series"></a>Unternehmenskritisch – bereitgestelltes Computing – DC-Serie

|Computegröße (Dienstziel)|BC_DC_2|BC_DC_4|BC_DC_6|BC_DC_8|
|:--- | --: |--: |--: |--: |
|Computegeneration|DC-Serie|DC-Serie|DC-Serie|DC-Serie|
|V-Kerne|2|4|6|8|
|Arbeitsspeicher (GB)|9|18|27|36|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|1.7|3,7|5.9|8,2|
|Maximale Datengröße (GB)|768|768|768|768|
|Max. Protokollgröße (GB) <sup>1</sup>|230|230|230|230|
|Max. Datengröße von TempDB (GB)|64|128|192|256|
|[Max. Größe des lokalen Speichers](resource-limits-logical-server.md#storage-space-governance) (GB)|1406|1406|1406|1406|
|Speichertyp|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|E/A-Wartezeit (ungefähr)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|
|Max. Daten-IOPS<sup>2</sup>|14000|28000|42000|44800|
|Max. Protokollrate (MBit/s)|24|48|72|96|
|Max. gleichzeitige Worker (Anforderungen)|200|400|600|800|
|Maximale Anzahl gleichzeitiger Anmeldungen|200|400|600|800|
|Max. gleichzeitige Sitzungen|30.000|30.000|30.000|30.000|
|Anzahl von Replikaten|4|4|4|4|
|Multi-AZ|Nein|Nein|Nein|Nein|
|Horizontale Leseskalierung|Nein|Nein|Nein|Nein|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

<sup>1</sup> Für dokumentierte Werte der maximalen Datengröße. Bei einer Reduzierung der maximalen Datengröße reduziert sich die maximale Protokollgröße proportional.

<sup>2</sup> Der maximale Wert für E/A-Größen im Bereich von 8 KB bis 64 KB. Die tatsächlichen IOPS sind von der Arbeitsauslastung abhängig. Weitere Informationen finden Sie unter [Daten-E/A-Governance](resource-limits-logical-server.md#resource-governance).

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu DTU-Ressourcenlimits für einen Singleton finden Sie unter [Ressourcenlimits für Singletons, die das DTU-Kaufmodell verwenden](resource-limits-dtu-single-databases.md).
- Informationen zu V-Kern-Ressourcenlimits für Pools für elastische Datenbanken finden Sie unter [Ressourcenlimits für Pools für elastische Datenbanken, die das V-Kern-Kaufmodell verwenden](resource-limits-vcore-elastic-pools.md).
- Informationen zu DTU-Ressourcenlimits für Pools für elastische Datenbanken finden Sie unter [Ressourcenlimits für Pools für elastische Datenbanken, die das DTU-Kaufmodell verwenden](resource-limits-dtu-elastic-pools.md).
- Informationen zu den Ressourcenlimits für verwaltete SQL-Instanzen finden Sie unter [Ressourcengrenzwerte für SQL Managed Instance](../managed-instance/resource-limits.md).
- Informationen zu allgemeinen Azure-Einschränkungen finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../../azure-resource-manager/management/azure-subscription-service-limits.md).
- Informationen zu Ressourcenlimits auf Server- und Abonnementebene auf einem Server finden Sie in der [Übersicht über Ressourcenlimits für einen Server](resource-limits-logical-server.md).
