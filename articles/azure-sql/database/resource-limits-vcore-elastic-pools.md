---
title: 'Pool für elastische Datenbanken: V-Kern-Ressourcenlimits'
description: Diese Seite beschreibt einige allgemeine V-Kern-Ressourcenlimits für Pools für elastische Datenbanken in Azure SQL-Datenbank.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1, references_regions
ms.devlang: ''
ms.topic: reference
author: dimitri-furman
ms.author: dfurman
ms.reviewer: mathoma
ms.date: 06/23/2021
ms.openlocfilehash: 54a3e933cda054b8bd3f9e86f2db775fca7342f7
ms.sourcegitcommit: cd7d099f4a8eedb8d8d2a8cae081b3abd968b827
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/25/2021
ms.locfileid: "112964426"
---
# <a name="resource-limits-for-elastic-pools-using-the-vcore-purchasing-model"></a>Ressourcenlimits für Pools für elastische Datenbanken, die das V-Kern-Kaufmodell verwenden
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In diesem Artikel werden die detaillierten Ressourcenlimits für Pools für elastische Azure SQL-Datenbanken und in einem Pool zusammengefasste Datenbanken mithilfe des V-Kern-Kaufmodells mitgeteilt.

* Weitere Informationen zu Ressourcenlimits für Singletons auf einem Server mit dem DTU-basierten Kaufmodell finden Sie unter [Übersicht über Ressourcenlimits auf einem Server](resource-limits-logical-server.md).
* Informationen zu Ressourcenlimits des DTU-Kaufmodells für Azure SQL-Datenbank finden Sie unter [DTU-Ressourcenlimits für einzelne Datenbanken (Singletons)](resource-limits-dtu-single-databases.md) und [DTU-Ressourcenlimits für Pools für elastische Datenbanken](resource-limits-dtu-elastic-pools.md).
* Informationen zu V-Kern-Ressourcenlimits finden Sie unter [V-Kern-Ressourcenlimits – Azure SQL-Datenbank](resource-limits-vcore-single-databases.md) und [V-Kern-Ressourcenlimits – Pools für elastische Datenbanken](resource-limits-vcore-elastic-pools.md).
* Weitere Informationen zu den verschiedenen Kaufmodellen finden Sie unter [Kaufmodelle und Dienstebenen](purchasing-models.md).

> [!IMPORTANT]
> Unter bestimmten Umständen müssen Sie ggf. eine Datenbank verkleinern, um ungenutzten Speicherplatz freizugeben. Weitere Informationen finden Sie unter [Verwalten von Dateispeicherplatz in Azure SQL-Datenbank](file-space-manage.md).

Jedes schreibgeschützte Replikat eines Pools für elastische Datenbanken hat eigene Ressourcen wie virtuelle Kerne, Arbeitsspeicher, Daten-IOPS, TempDB, Worker und Sitzungen. Jedes schreibgeschützte Replikat unterliegt den Ressourcenlimits eines Pools für elastische Datenbanken, die weiter unten in diesem Artikel beschrieben werden.

Sie können die Dienstebene, Computegröße (Dienstziel) und Speichermenge mit folgenden Optionen festlegen:

* [Transact-SQL](elastic-pool-scale.md) über [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql#overview-sql-database)
* [Azure portal](elastic-pool-manage.md#azure-portal)
* [PowerShell](elastic-pool-manage.md#powershell)
* [Azure-Befehlszeilenschnittstelle](elastic-pool-manage.md#azure-cli)
* [REST-API](elastic-pool-manage.md#rest-api)

> [!IMPORTANT]
> Anleitungen und Überlegungen zur Skalierung finden Sie unter [Skalieren eines Pools für elastische Datenbanken](elastic-pool-scale.md).

Wenn alle virtuellen Kerne eines Pools für elastische Datenbanken verwendet werden, erhält jede Datenbank im Pool gleich viel Computeressourcen zum Verarbeiten von Abfragen. Azure SQL-Datenbank bietet eine faire gemeinsame Nutzung von Ressourcen durch Datenbanken, indem gleiche Slices an Computezeit zugesichert werden. Diese faire gemeinsame Nutzung in Pools für elastische Datenbanken ergänzt die Ressourcen, die jeder Datenbank auf andere Weise garantiert werden, wenn die Mindestanzahl von virtuellen Kernen pro Datenbank auf einen Wert ungleich null festgelegt ist.

## <a name="general-purpose---provisioned-compute---gen4"></a>Universell – bereitgestelltes Computing – Gen4

> [!IMPORTANT]
> Neue Gen4-Datenbanken werden in den Regionen „Australien, Osten“ und „Brasilien, Süden“ nicht mehr unterstützt.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>Universelle Dienstebene: Computeplattform der 4. Generation (Teil 1)

|Computegröße (Dienstziel)|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Computegeneration|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|V-Kerne|1|2|3|4|5|6|
|Arbeitsspeicher (GB)|7|14|21|28|35|42|
|Max. Anzahl von Datenbanken pro Pool <sup>1</sup>|100|200|500|500|500|500|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|–|–|–|–|–|–|
|Maximale Datengröße (GB)|512|756|1536|1536|1536|2048|
|Max. Protokollgröße <sup>2</sup>|154|227|461|461|461|614|
|Max. Datengröße von TempDB (GB)|32|64|96|128|160|192|
|Speichertyp|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|
|E/A-Wartezeit (ungefähr)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|
|Max. Daten-IOPS pro Pool <sup>3</sup> |400|800|1200|1600|2000|2400|
|Maximale Protokollrate pro Pool (MBit/s)|6|12|18|24|30|36|
|Max. gleichzeitige Worker pro Pool (Anforderungen) <sup>4</sup> |210|420|630|840|1050|1260|
|Max. Anzahl von gleichzeitigen Anmeldungen pro Pool <sup>4</sup> |210|420|630|840|1050|1260|
|Max. gleichzeitige Sitzungen|30.000|30.000|30.000|30.000|30.000|30.000|
|Min/Max. V-Kern-Auswahl pro Datenbank für Pools für elastische Datenbanken|0, 0,25, 0,5, 1|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1...3|0, 0,25, 0,5, 1...4|0, 0,25, 0,5, 1...5|0, 0,25, 0,5, 1...6|
|Anzahl von Replikaten|1|1|1|1|1|1|
|Multi-AZ|–|–|–|–|–|–|
|Horizontale Leseskalierung|–|–|–|–|–|–|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

<sup>1</sup> Weitere Überlegungen finden Sie unter [Ressourcenverwaltung in umfangreichen Pools für elastische Datenbanken](elastic-pool-resource-management.md).

<sup>2</sup> Für dokumentierte Werte der maximalen Datengröße. Bei einer Reduzierung der maximalen Datengröße reduziert sich die maximale Protokollgröße proportional.

<sup>3</sup> Der maximale Wert für E/A-Größen im Bereich von 8 KB bis 64 KB. Die tatsächlichen IOPS sind von der Arbeitsauslastung abhängig. Weitere Informationen finden Sie unter [Daten-E/A-Governance](resource-limits-logical-server.md#resource-governance).

<sup>4</sup> Den Wert für „Max. gleichzeitige Worker (Anforderungen)“ für einzelne Datenbanken finden Sie unter [Ressourcenlimits für Singletons mit dem auf virtuellen Kernen (V-Kernen) basierenden Kaufmodell](resource-limits-vcore-single-databases.md). Wenn für den Pool für elastische Datenbanken beispielsweise Gen5 verwendet wird und die maximale Anzahl von V-Kernen pro Datenbank auf 2 festgelegt ist, ist der Wert für die Anzahl von gleichzeitigen Workern 200.  Wenn die maximale Anzahl von V-Kernen pro Datenbank auf 0,5 festgelegt ist, beträgt der Wert für die Anzahl von gleichzeitigen Workern 50, da unter Gen5 maximal 100 gleichzeitige Worker pro V-Kern verwendet werden. Für andere Einstellungen zur maximalen Anzahl von V-Kernen pro Datenbank (1 V-Kern oder weniger), wird die maximale Anzahl von gleichzeitigen Workern auf ähnliche Weise neu skaliert.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Universelle Dienstebene: Computeplattform der 4. Generation (Teil 2)

|Computegröße (Dienstziel)|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Computegeneration|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|V-Kerne|7|8|9|10|16|24|
|Arbeitsspeicher (GB)|49|56|63|70|112|159,5|
|Max. Anzahl von Datenbanken pro Pool <sup>1</sup>|500|500|500|500|500|500|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|–|–|–|–|–|–|
|Maximale Datengröße (GB)|2048|2048|2048|2048|3.584|4096|
|Max. Protokollgröße (GB) <sup>2</sup>|614|614|614|614|1075|1229|
|Max. Datengröße von TempDB (GB)|224|256|288|320|512|768|
|Speichertyp|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|
|E/A-Wartezeit (ungefähr)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|
|Max. Daten-IOPS pro Pool <sup>3</sup>|2800|3200|3600|4000|6400|9600|
|Maximale Protokollrate pro Pool (MBit/s)|42|48|54|60|62,5|62,5|
|Max. gleichzeitige Worker pro Pool (Anforderungen) <sup>4</sup>|1470|1680|1890|2100|3360|5040|
|Max. Anzahl von gleichzeitigen Anmeldungen pro Pool (Anforderungen) <sup>4</sup>|1470|1680|1890|2100|3360|5040|
|Max. gleichzeitige Sitzungen|30.000|30.000|30.000|30.000|30.000|30.000|
|Min/Max. V-Kern-Auswahl pro Datenbank für Pools für elastische Datenbanken|0, 0,25, 0,5, 1...7|0, 0,25, 0,5, 1...8|0, 0,25, 0,5, 1...9|0, 0,25, 0,5, 1...10|0, 0,25, 0,5, 1...10, 16|0, 0,25, 0,5, 1...10, 16, 24|
|Anzahl von Replikaten|1|1|1|1|1|1|
|Multi-AZ|–|–|–|–|–|–|
|Horizontale Leseskalierung|–|–|–|–|–|–|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

<sup>1</sup> Weitere Überlegungen finden Sie unter [Ressourcenverwaltung in umfangreichen Pools für elastische Datenbanken](elastic-pool-resource-management.md).

<sup>2</sup> Für dokumentierte Werte der maximalen Datengröße. Bei einer Reduzierung der maximalen Datengröße reduziert sich die maximale Protokollgröße proportional.

<sup>3</sup> Der maximale Wert für E/A-Größen im Bereich von 8 KB bis 64 KB. Die tatsächlichen IOPS sind von der Arbeitsauslastung abhängig. Weitere Informationen finden Sie unter [Daten-E/A-Governance](resource-limits-logical-server.md#resource-governance).    

<sup>4</sup> Den Wert für „Max. gleichzeitige Worker (Anforderungen)“ für einzelne Datenbanken finden Sie unter [Ressourcenlimits für Singletons mit dem auf virtuellen Kernen (V-Kernen) basierenden Kaufmodell](resource-limits-vcore-single-databases.md). Wenn für den Pool für elastische Datenbanken beispielsweise Gen5 verwendet wird und die maximale Anzahl von V-Kernen pro Datenbank auf 2 festgelegt ist, ist der Wert für die Anzahl von gleichzeitigen Workern 200.  Wenn die maximale Anzahl von V-Kernen pro Datenbank auf 0,5 festgelegt ist, beträgt der Wert für die Anzahl von gleichzeitigen Workern 50, da unter Gen5 maximal 100 gleichzeitige Worker pro V-Kern verwendet werden. Für andere Einstellungen zur maximalen Anzahl von V-Kernen pro Datenbank (1 V-Kern oder weniger), wird die maximale Anzahl von gleichzeitigen Workern auf ähnliche Weise neu skaliert.

## <a name="general-purpose---provisioned-compute---gen5"></a>Universell – bereitgestelltes Computing – Gen5

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Universelle Dienstebene: Computeplattform der 5. Generation (Teil 1)

|Computegröße (Dienstziel)|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Computegeneration|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|V-Kerne|2|4|6|8|10|12|14|
|Arbeitsspeicher (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|Max. Anzahl von Datenbanken pro Pool <sup>1</sup>|100|200|500|500|500|500|500|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|–|–|–|–|–|–|–|
|Maximale Datengröße (GB)|512|756|1536|1536|1536|2048|2048|
|Max. Protokollgröße (GB) <sup>2</sup>|154|227|461|461|461|614|614|
|Max. Datengröße von TempDB (GB)|64|128|192|256|320|384|448|
|Speichertyp|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|
|E/A-Wartezeit (ungefähr)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|
|Max. Daten-IOPS pro Pool <sup>3</sup>|800|1600|2400|3200|4000|4800|5600|
|Maximale Protokollrate pro Pool (MBit/s)|12|24|36|48|60|62,5|62,5|
|Max. gleichzeitige Worker pro Pool (Anforderungen) <sup>4</sup>|210|420|630|840|1050|1260|1470|
|Max. Anzahl von gleichzeitigen Anmeldungen pro Pool (Anforderungen) <sup>4</sup>|210|420|630|840|1050|1260|1470|
|Max. gleichzeitige Sitzungen|30.000|30.000|30.000|30.000|30.000|30.000|30.000|
|Min/Max. V-Kern-Auswahl pro Datenbank für Pools für elastische Datenbanken|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1...4|0, 0,25, 0,5, 1...6|0, 0,25, 0,5, 1...8|0, 0,25, 0,5, 1...10|0, 0,25, 0,5, 1...12|0, 0,25, 0,5, 1...14|
|Anzahl von Replikaten|1|1|1|1|1|1|1|
|Multi-AZ|[In der Vorschau verfügbar](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[In der Vorschau verfügbar](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[In der Vorschau verfügbar](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[In der Vorschau verfügbar](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[In der Vorschau verfügbar](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[In der Vorschau verfügbar](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[In der Vorschau verfügbar](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|
|Horizontale Leseskalierung|–|–|–|–|–|–|–|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

<sup>1</sup> Weitere Überlegungen finden Sie unter [Ressourcenverwaltung in umfangreichen Pools für elastische Datenbanken](elastic-pool-resource-management.md).

<sup>2</sup> Für dokumentierte Werte der maximalen Datengröße. Bei einer Reduzierung der maximalen Datengröße reduziert sich die maximale Protokollgröße proportional.

<sup>3</sup> Der maximale Wert für E/A-Größen im Bereich von 8 KB bis 64 KB. Die tatsächlichen IOPS sind von der Arbeitsauslastung abhängig. Weitere Informationen finden Sie unter [Daten-E/A-Governance](resource-limits-logical-server.md#resource-governance).

<sup>4</sup> Den Wert für „Max. gleichzeitige Worker (Anforderungen)“ für einzelne Datenbanken finden Sie unter [Ressourcenlimits für Singletons mit dem auf virtuellen Kernen (V-Kernen) basierenden Kaufmodell](resource-limits-vcore-single-databases.md). Wenn für den Pool für elastische Datenbanken beispielsweise Gen5 verwendet wird und die maximale Anzahl von V-Kernen pro Datenbank auf 2 festgelegt ist, ist der Wert für die Anzahl von gleichzeitigen Workern 200.  Wenn die maximale Anzahl von V-Kernen pro Datenbank auf 0,5 festgelegt ist, beträgt der Wert für die Anzahl von gleichzeitigen Workern 50, da unter Gen5 maximal 100 gleichzeitige Worker pro V-Kern verwendet werden. Für andere Einstellungen zur maximalen Anzahl von V-Kernen pro Datenbank (1 V-Kern oder weniger), wird die maximale Anzahl von gleichzeitigen Workern auf ähnliche Weise neu skaliert.

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Universelle Dienstebene: Computeplattform der 5. Generation (Teil 2)

|Computegröße (Dienstziel)|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Computegeneration|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|V-Kerne|16|18|20|24|32|40|80|
|Arbeitsspeicher (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Max. Anzahl von Datenbanken pro Pool <sup>1</sup>|500|500|500|500|500|500|500|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|–|–|–|–|–|–|–|
|Maximale Datengröße (GB)|2048|3072|3072|3072|4096|4096|4096|
|Max. Protokollgröße (GB) <sup>2</sup>|614|922|922|922|1229|1229|1229|
|Max. Datengröße von TempDB (GB)|512|576|640|768|1024|1280|2560|
|Speichertyp|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|
|E/A-Wartezeit (ungefähr)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|
|Max. Daten-IOPS pro Pool <sup>3</sup> |6\.400|7\.200|8\.000|9\.600|12.800|16.000|16.000|
|Maximale Protokollrate pro Pool (MBit/s)|62,5|62,5|62,5|62,5|62,5|62,5|62,5|
|Max. gleichzeitige Worker pro Pool (Anforderungen) <sup>4</sup>|1680|1890|2100|2520|3360|4.200|8.400|
|Max. Anzahl von gleichzeitigen Anmeldungen pro Pool (Anforderungen) <sup>4</sup>|1680|1890|2100|2520|3360|4.200|8.400|
|Max. gleichzeitige Sitzungen|30.000|30.000|30.000|30.000|30.000|30.000|30.000|
|Min/Max. V-Kern-Auswahl pro Datenbank für Pools für elastische Datenbanken|0, 0,25, 0,5, 1...16|0, 0,25, 0,5, 1...18|0, 0,25, 0,5, 1...20|0, 0,25, 0,5, 1...20, 24|0, 0,25, 0,5, 1...20, 24, 32|0, 0,25, 0,5, 1...16, 24, 32, 40|0, 0,25, 0,5, 1...16, 24, 32, 40, 80|
|Anzahl von Replikaten|1|1|1|1|1|1|1|
|Multi-AZ|[In der Vorschau verfügbar](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[In der Vorschau verfügbar](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[In der Vorschau verfügbar](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[In der Vorschau verfügbar](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[In der Vorschau verfügbar](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[In der Vorschau verfügbar](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[In der Vorschau verfügbar](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|
|Horizontale Leseskalierung|–|–|–|–|–|–|–|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

<sup>1</sup> Weitere Überlegungen finden Sie unter [Ressourcenverwaltung in umfangreichen Pools für elastische Datenbanken](elastic-pool-resource-management.md).

<sup>2</sup> Für dokumentierte Werte der maximalen Datengröße. Bei einer Reduzierung der maximalen Datengröße reduziert sich die maximale Protokollgröße proportional.

<sup>3</sup> Der maximale Wert für E/A-Größen im Bereich von 8 KB bis 64 KB. Die tatsächlichen IOPS sind von der Arbeitsauslastung abhängig. Weitere Informationen finden Sie unter [Daten-E/A-Governance](resource-limits-logical-server.md#resource-governance).

<sup>4</sup> Den Wert für „Max. gleichzeitige Worker (Anforderungen)“ für einzelne Datenbanken finden Sie unter [Ressourcenlimits für Singletons mit dem auf virtuellen Kernen (V-Kernen) basierenden Kaufmodell](resource-limits-vcore-single-databases.md). Wenn für den Pool für elastische Datenbanken beispielsweise Gen5 verwendet wird und die maximale Anzahl von V-Kernen pro Datenbank auf 2 festgelegt ist, ist der Wert für die Anzahl von gleichzeitigen Workern 200.  Wenn die maximale Anzahl von V-Kernen pro Datenbank auf 0,5 festgelegt ist, beträgt der Wert für die Anzahl von gleichzeitigen Workern 50, da unter Gen5 maximal 100 gleichzeitige Worker pro V-Kern verwendet werden. Für andere Einstellungen zur maximalen Anzahl von V-Kernen pro Datenbank (1 V-Kern oder weniger), wird die maximale Anzahl von gleichzeitigen Workern auf ähnliche Weise neu skaliert.

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Universell – bereitgestelltes Computing – Fsv2-Serie

### <a name="fsv2-series-compute-generation-part-1"></a>Computegeneration der Fsv2-Serie (Teil 1)

|Computegröße (Dienstziel)|GP_Fsv2_8|GP_Fsv2_10|GP_Fsv2_12|GP_Fsv2_14| GP_Fsv2_16|
|:---| ---:|---:|---:|---:|---:|
|Computegeneration|Fsv2-Serie|Fsv2-Serie|Fsv2-Serie|Fsv2-Serie|Fsv2-Serie|
|V-Kerne|8|10|12|14|16|
|Arbeitsspeicher (GB)|15,1|18,9|22,7|26,5|30,2|
|Max. Anzahl von Datenbanken pro Pool <sup>1</sup>|500|500|500|500|500|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|–|–|–|–|–|
|Maximale Datengröße (GB)|1024|1024|1024|1024|1536|
|Max. Protokollgröße (GB) <sup>2</sup>|336|336|336|336|512|
|Max. Datengröße von TempDB (GB)|37|46|56|65|74|
|Speichertyp|Remote-SSD|Remote-SSD|Remote-SSD|Remote-SSD|Remote-SSD|
|E/A-Wartezeit (ungefähr)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|
|Max. Daten-IOPS pro Pool <sup>3</sup>|2560|3200|3840|4480|5120|
|Maximale Protokollrate pro Pool (MBit/s)|48|60|62,5|62,5|62,5|
|Max. gleichzeitige Worker pro Pool (Anforderungen) <sup>4</sup>|400|500|600|700|800|
|Max. Anzahl von gleichzeitigen Anmeldungen pro Pool (Anforderungen) <sup>4</sup>|800|1000|1200|1400|1600|
|Max. gleichzeitige Sitzungen|30.000|30.000|30.000|30.000|30.000|
|Min/Max. V-Kern-Auswahl pro Datenbank für Pools für elastische Datenbanken|0–8|0–10|0–12|0–14|0–16|
|Anzahl von Replikaten|1|1|1|1|1|
|Multi-AZ|–|–|–|–|–|
|Horizontale Leseskalierung|–|–|–|–|–|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

<sup>1</sup> Weitere Überlegungen finden Sie unter [Ressourcenverwaltung in umfangreichen Pools für elastische Datenbanken](elastic-pool-resource-management.md).

<sup>2</sup> Für dokumentierte Werte der maximalen Datengröße. Bei einer Reduzierung der maximalen Datengröße reduziert sich die maximale Protokollgröße proportional.

<sup>3</sup> Der maximale Wert für E/A-Größen im Bereich von 8 KB bis 64 KB. Die tatsächlichen IOPS sind von der Arbeitsauslastung abhängig. Weitere Informationen finden Sie unter [Daten-E/A-Governance](resource-limits-logical-server.md#resource-governance).

<sup>4</sup> Den Wert für „Max. gleichzeitige Worker (Anforderungen)“ für einzelne Datenbanken finden Sie unter [Ressourcenlimits für Singletons mit dem auf virtuellen Kernen (V-Kernen) basierenden Kaufmodell](resource-limits-vcore-single-databases.md). Wenn für den Pool für elastische Datenbanken beispielsweise Gen5 verwendet wird und die maximale Anzahl von V-Kernen pro Datenbank auf 2 festgelegt ist, ist der Wert für die Anzahl von gleichzeitigen Workern 200.  Wenn die maximale Anzahl von V-Kernen pro Datenbank auf 0,5 festgelegt ist, beträgt der Wert für die Anzahl von gleichzeitigen Workern 50, da unter Gen5 maximal 100 gleichzeitige Worker pro V-Kern verwendet werden. Für andere Einstellungen zur maximalen Anzahl von V-Kernen pro Datenbank (1 V-Kern oder weniger), wird die maximale Anzahl von gleichzeitigen Workern auf ähnliche Weise neu skaliert.

### <a name="fsv2-series-compute-generation-part-2"></a>Computegeneration der Fsv2-Serie (Teil 2)

|Computegröße (Dienstziel)|GP_Fsv2_18|GP_Fsv2_20|GP_Fsv2_24|GP_Fsv2_32| GP_Fsv2_36|GP_Fsv2_72|
|:---| ---:|---:|---:|---:|---:|---:|
|Computegeneration|Fsv2-Serie|Fsv2-Serie|Fsv2-Serie|Fsv2-Serie|Fsv2-Serie|Fsv2-Serie|
|V-Kerne|18|20|24|32|36|72|
|Arbeitsspeicher (GB)|34,0|37,8|45,4|60,5|68,0|136,0|
|Max. Anzahl von Datenbanken pro Pool <sup>1</sup>|500|500|500|500|500|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|–|–|–|–|–|–|
|Maximale Datengröße (GB)|1536|1536|1536|3072|3072|4096|
|Max. Protokollgröße (GB) <sup>2</sup>|512|512|512|1024|1024|1024|
|Max. Datengröße von TempDB (GB)|83|93|111|148|167|333|
|Speichertyp|Remote-SSD|Remote-SSD|Remote-SSD|Remote-SSD|Remote-SSD|Remote-SSD|
|E/A-Wartezeit (ungefähr)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|
|Max. Daten-IOPS pro Pool <sup>3</sup>|5760|6400|7680|10.240|11.520|12800|
|Maximale Protokollrate pro Pool (MBit/s)|62,5|62,5|62,5|62,5|62,5|62,5|
|Max. gleichzeitige Worker pro Pool (Anforderungen) <sup>4</sup>|900|1000|1200|1600|1800|3600|
|Max. Anzahl von gleichzeitigen Anmeldungen pro Pool (Anforderungen) <sup>4</sup>|1800|2000|2400|3200|3600|7200|
|Max. gleichzeitige Sitzungen|30.000|30.000|30.000|30.000|30.000|30.000|
|Min/Max. V-Kern-Auswahl pro Datenbank für Pools für elastische Datenbanken|0–18|0 - 20|0-24|0–32|0–36|0–72|
|Anzahl von Replikaten|1|1|1|1|1|1|
|Multi-AZ|–|–|–|–|–|–|
|Horizontale Leseskalierung|–|–|–|–|–|–|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

<sup>1</sup> Weitere Überlegungen finden Sie unter [Ressourcenverwaltung in umfangreichen Pools für elastische Datenbanken](elastic-pool-resource-management.md).

<sup>2</sup> Für dokumentierte Werte der maximalen Datengröße. Bei einer Reduzierung der maximalen Datengröße reduziert sich die maximale Protokollgröße proportional.

<sup>3</sup> Der maximale Wert für E/A-Größen im Bereich von 8 KB bis 64 KB. Die tatsächlichen IOPS sind von der Arbeitsauslastung abhängig. Weitere Informationen finden Sie unter [Daten-E/A-Governance](resource-limits-logical-server.md#resource-governance).

<sup>4</sup> Den Wert für „Max. gleichzeitige Worker (Anforderungen)“ für einzelne Datenbanken finden Sie unter [Ressourcenlimits für Singletons mit dem auf virtuellen Kernen (V-Kernen) basierenden Kaufmodell](resource-limits-vcore-single-databases.md). Wenn für den Pool für elastische Datenbanken beispielsweise Gen5 verwendet wird und die maximale Anzahl von V-Kernen pro Datenbank auf 2 festgelegt ist, ist der Wert für die Anzahl von gleichzeitigen Workern 200.  Wenn die maximale Anzahl von V-Kernen pro Datenbank auf 0,5 festgelegt ist, beträgt der Wert für die Anzahl von gleichzeitigen Workern 50, da unter Gen5 maximal 100 gleichzeitige Worker pro V-Kern verwendet werden. Für andere Einstellungen zur maximalen Anzahl von V-Kernen pro Datenbank (1 V-Kern oder weniger), wird die maximale Anzahl von gleichzeitigen Workern auf ähnliche Weise neu skaliert.

## <a name="general-purpose---provisioned-compute---dc-series"></a>Universell – bereitgestelltes Computing – DC-Serie

|Computegröße (Dienstziel)|GP_DC_2|GP_DC_4|GP_DC_6|GP_DC_8|
|:--- | --: |--: |--: |--: |
|Computegeneration|DC|DC|DC|DC|
|V-Kerne|2|4|6|8|
|Arbeitsspeicher (GB)|9|18|27|36|
|Max. Anzahl von Datenbanken pro Pool <sup>1</sup>|100|400|400|400|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|–|–|–|–|
|Maximale Datengröße (GB)|756|1536|2048|2048|
|Max. Protokollgröße (GB) <sup>2</sup>|227|461|614|614|
|Max. Datengröße von TempDB (GB)|64|128|192|256|
|Speichertyp|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|Storage Premium (Remote)|
|E/A-Wartezeit (ungefähr)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|5-7 ms (Schreiben)<br>5-10 ms (Lesen)|
|Max. Daten-IOPS pro Pool <sup>3</sup>|800|1600|2400|3200|
|Maximale Protokollrate pro Pool (MBit/s)|12|24|36|48|
|Max. gleichzeitige Worker pro Pool (Anforderungen) <sup>4</sup>|168|336|504|672|
|Max. Anzahl von gleichzeitigen Anmeldungen pro Pool (Anforderungen) <sup>4</sup>|168|336|504|672|
|Max. gleichzeitige Sitzungen|30.000|30.000|30.000|30.000|
|Min/Max. V-Kern-Auswahl pro Datenbank für Pools für elastische Datenbanken|2|2...4|2...6|2...8|
|Anzahl von Replikaten|1|1|1|1|
|Multi-AZ|–|–|–|–|
|Horizontale Leseskalierung|–|–|–|–|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

<sup>1</sup> Weitere Überlegungen finden Sie unter [Ressourcenverwaltung in umfangreichen Pools für elastische Datenbanken](elastic-pool-resource-management.md).

<sup>2</sup> Für dokumentierte Werte der maximalen Datengröße. Bei einer Reduzierung der maximalen Datengröße reduziert sich die maximale Protokollgröße proportional.

<sup>3</sup> Der maximale Wert für E/A-Größen im Bereich von 8 KB bis 64 KB. Die tatsächlichen IOPS sind von der Arbeitsauslastung abhängig. Weitere Informationen finden Sie unter [Daten-E/A-Governance](resource-limits-logical-server.md#resource-governance).

<sup>4</sup> Den Wert für „Max. gleichzeitige Worker (Anforderungen)“ für einzelne Datenbanken finden Sie unter [Ressourcenlimits für Singletons mit dem auf virtuellen Kernen (V-Kernen) basierenden Kaufmodell](resource-limits-vcore-single-databases.md). Wenn für den Pool für elastische Datenbanken beispielsweise Gen5 verwendet wird und die maximale Anzahl von V-Kernen pro Datenbank auf 2 festgelegt ist, ist der Wert für die Anzahl von gleichzeitigen Workern 200.  Wenn die maximale Anzahl von V-Kernen pro Datenbank auf 0,5 festgelegt ist, beträgt der Wert für die Anzahl von gleichzeitigen Workern 50, da unter Gen5 maximal 100 gleichzeitige Worker pro V-Kern verwendet werden. Für andere Einstellungen zur maximalen Anzahl von V-Kernen pro Datenbank (1 V-Kern oder weniger), wird die maximale Anzahl von gleichzeitigen Workern auf ähnliche Weise neu skaliert.

## <a name="business-critical---provisioned-compute---gen4"></a>Unternehmenskritisch – bereitgestelltes Computing – Gen4

> [!IMPORTANT]
> Neue Gen4-Datenbanken werden in den Regionen „Australien, Osten“ und „Brasilien, Süden“ nicht mehr unterstützt.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-1"></a>Dienstebene „Unternehmenskritisch“: Computeplattform der 4. Generation (Teil 1)

|Computegröße (Dienstziel)|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Computegeneration|Gen4|Gen4|Gen4|Gen4|Gen4|
|V-Kerne|2|3|4|5|6|
|Arbeitsspeicher (GB)|14|21|28|35|42|
|Max. Anzahl von Datenbanken pro Pool <sup>1</sup>|50|100|100|100|100|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|2|3|4|5|6|
|Speichertyp|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|Maximale Datengröße (GB)|1024|1024|1024|1024|1024|
|Max. Protokollgröße (GB) <sup>2</sup>|307|307|307|307|307|
|Max. Datengröße von TempDB (GB)|64|96|128|160|192|
|[Max. Größe des lokalen Speichers](resource-limits-logical-server.md#storage-space-governance) (GB)|1\.356|1\.356|1\.356|1\.356|1\.356|
|E/A-Wartezeit (ungefähr)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|
|Max. Daten-IOPS pro Pool <sup>3</sup>|9\.000|13.500|18.000|22.500|27.000|
|Maximale Protokollrate pro Pool (MBit/s)|20|30|40|50|60|
|Max. gleichzeitige Worker pro Pool (Anforderungen) <sup>4</sup>|420|630|840|1050|1260|
|Max. Anzahl von gleichzeitigen Anmeldungen pro Pool (Anforderungen) <sup>4</sup>|420|630|840|1050|1260|
|Max. gleichzeitige Sitzungen|30.000|30.000|30.000|30.000|30.000|
|Min/Max. V-Kern-Auswahl pro Datenbank für Pools für elastische Datenbanken|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1...3|0, 0,25, 0,5, 1...4|0, 0,25, 0,5, 1...5|0, 0,25, 0,5, 1...6|
|Anzahl von Replikaten|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|
|Horizontale Leseskalierung|Ja|Ja|Ja|Ja|Ja|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

<sup>1</sup> Weitere Überlegungen finden Sie unter [Ressourcenverwaltung in umfangreichen Pools für elastische Datenbanken](elastic-pool-resource-management.md).

<sup>2</sup> Für dokumentierte Werte der maximalen Datengröße. Bei einer Reduzierung der maximalen Datengröße reduziert sich die maximale Protokollgröße proportional.

<sup>3</sup> Der maximale Wert für E/A-Größen im Bereich von 8 KB bis 64 KB. Die tatsächlichen IOPS sind von der Arbeitsauslastung abhängig. Weitere Informationen finden Sie unter [Daten-E/A-Governance](resource-limits-logical-server.md#resource-governance).

<sup>4</sup> Den Wert für „Max. gleichzeitige Worker (Anforderungen)“ für einzelne Datenbanken finden Sie unter [Ressourcenlimits für Singletons mit dem auf virtuellen Kernen (V-Kernen) basierenden Kaufmodell](resource-limits-vcore-single-databases.md). Wenn für den Pool für elastische Datenbanken beispielsweise Gen5 verwendet wird und die maximale Anzahl von V-Kernen pro Datenbank auf 2 festgelegt ist, ist der Wert für die Anzahl von gleichzeitigen Workern 200.  Wenn die maximale Anzahl von V-Kernen pro Datenbank auf 0,5 festgelegt ist, beträgt der Wert für die Anzahl von gleichzeitigen Workern 50, da unter Gen5 maximal 100 gleichzeitige Worker pro V-Kern verwendet werden. Für andere Einstellungen zur maximalen Anzahl von V-Kernen pro Datenbank (1 V-Kern oder weniger), wird die maximale Anzahl von gleichzeitigen Workern auf ähnliche Weise neu skaliert.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Dienstebene „Unternehmenskritisch“: Computeplattform der 4. Generation (Teil 2)

|Computegröße (Dienstziel)|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Computegeneration|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|V-Kerne|7|8|9|10|16|24|
|Arbeitsspeicher (GB)|49|56|63|70|112|159,5|
|Max. Anzahl von Datenbanken pro Pool <sup>1</sup>|100|100|100|100|100|100|
|Columnstore-Unterstützung|–|–|–|–|–|–|
|In-Memory-OLTP-Speicher (GB)|7|8|9.5|11|20|36|
|Speichertyp|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|Maximale Datengröße (GB)|1024|1024|1024|1024|1024|1024|
|Max. Protokollgröße (GB) <sup>2</sup>|307|307|307|307|307|307|
|Max. Datengröße von TempDB (GB)|224|256|288|320|512|768|
|[Max. Größe des lokalen Speichers](resource-limits-logical-server.md#storage-space-governance) (GB)|1\.356|1\.356|1\.356|1\.356|1\.356|1\.356|
|E/A-Wartezeit (ungefähr)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|
|Max. Daten-IOPS pro Pool <sup>3</sup>|31.500|36.000|40.500|45.000|72.000|96.000|
|Maximale Protokollrate pro Pool (MBit/s)|70|80|80|80|80|80|
|Max. gleichzeitige Worker pro Pool (Anforderungen) <sup>4</sup>|1470|1680|1890|2100|3360|5040|
|Max. Anzahl von gleichzeitigen Anmeldungen pro Pool (Anforderungen) <sup>4</sup>|1470|1680|1890|2100|3360|5040|
|Max. gleichzeitige Sitzungen|30.000|30.000|30.000|30.000|30.000|30.000|
|Min/Max. V-Kern-Auswahl pro Datenbank für Pools für elastische Datenbanken|0, 0,25, 0,5, 1...7|0, 0,25, 0,5, 1...8|0, 0,25, 0,5, 1...9|0, 0,25, 0,5, 1...10|0, 0,25, 0,5, 1...10, 16|0, 0,25, 0,5, 1...10, 16, 24|
|Anzahl von Replikaten|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Horizontale Leseskalierung|Ja|Ja|Ja|Ja|Ja|Ja|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

<sup>1</sup> Weitere Überlegungen finden Sie unter [Ressourcenverwaltung in umfangreichen Pools für elastische Datenbanken](elastic-pool-resource-management.md).

<sup>2</sup> Für dokumentierte Werte der maximalen Datengröße. Bei einer Reduzierung der maximalen Datengröße reduziert sich die maximale Protokollgröße proportional.

<sup>3</sup> Der maximale Wert für E/A-Größen im Bereich von 8 KB bis 64 KB. Die tatsächlichen IOPS sind von der Arbeitsauslastung abhängig. Weitere Informationen finden Sie unter [Daten-E/A-Governance](resource-limits-logical-server.md#resource-governance).

<sup>4</sup> Den Wert für „Max. gleichzeitige Worker (Anforderungen)“ für einzelne Datenbanken finden Sie unter [Ressourcenlimits für Singletons mit dem auf virtuellen Kernen (V-Kernen) basierenden Kaufmodell](resource-limits-vcore-single-databases.md). Wenn für den Pool für elastische Datenbanken beispielsweise Gen5 verwendet wird und die maximale Anzahl von V-Kernen pro Datenbank auf 2 festgelegt ist, ist der Wert für die Anzahl von gleichzeitigen Workern 200.  Wenn die maximale Anzahl von V-Kernen pro Datenbank auf 0,5 festgelegt ist, beträgt der Wert für die Anzahl von gleichzeitigen Workern 50, da unter Gen5 maximal 100 gleichzeitige Worker pro V-Kern verwendet werden. Für andere Einstellungen zur maximalen Anzahl von V-Kernen pro Datenbank (1 V-Kern oder weniger), wird die maximale Anzahl von gleichzeitigen Workern auf ähnliche Weise neu skaliert.

## <a name="business-critical---provisioned-compute---gen5"></a>Unternehmenskritisch – bereitgestelltes Computing – Gen5

### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Dienstebene „Unternehmenskritisch“: Computeplattform der 5. Generation (Teil 1)

|Computegröße (Dienstziel)|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Computegeneration|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|V-Kerne|4|6|8|10|12|14|
|Arbeitsspeicher (GB)|20,8|31,1|41,5|51,9|62,3|72,7|
|Max. Anzahl von Datenbanken pro Pool <sup>1</sup>|50|100|100|100|100|100|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|3,14|4.71|6,28|8,65|11,02|13,39|
|Maximale Datengröße (GB)|1024|1536|1536|1536|3072|3072|
|Max. Protokollgröße (GB) <sup>2</sup>|307|307|461|461|922|922|
|Max. Datengröße von TempDB (GB)|128|192|256|320|384|448|
|[Max. Größe des lokalen Speichers](resource-limits-logical-server.md#storage-space-governance) (GB)|4829|4829|4829|4829|4829|4829|
|Speichertyp|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|E/A-Wartezeit (ungefähr)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|
|Max. Daten-IOPS pro Pool <sup>3</sup>|18.000|27.000|36.000|45.000|54.000|63.000|
|Maximale Protokollrate pro Pool (MBit/s)|60|90|120|120|120|120|
|Max. gleichzeitige Worker pro Pool (Anforderungen) <sup>4</sup>|420|630|840|1050|1260|1470|
|Max. Anzahl von gleichzeitigen Anmeldungen pro Pool (Anforderungen) <sup>4</sup>|420|630|840|1050|1260|1470|
|Max. gleichzeitige Sitzungen|30.000|30.000|30.000|30.000|30.000|30.000|
|Min/Max. V-Kern-Auswahl pro Datenbank für Pools für elastische Datenbanken|0, 0,25, 0,5, 1...4|0, 0,25, 0,5, 1...6|0, 0,25, 0,5, 1...8|0, 0,25, 0,5, 1...10|0, 0,25, 0,5, 1...12|0, 0,25, 0,5, 1...14|
|Anzahl von Replikaten|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Horizontale Leseskalierung|Ja|Ja|Ja|Ja|Ja|Ja|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

<sup>1</sup> Weitere Überlegungen finden Sie unter [Ressourcenverwaltung in umfangreichen Pools für elastische Datenbanken](elastic-pool-resource-management.md).

<sup>2</sup> Für dokumentierte Werte der maximalen Datengröße. Bei einer Reduzierung der maximalen Datengröße reduziert sich die maximale Protokollgröße proportional.

<sup>3</sup> Der maximale Wert für E/A-Größen im Bereich von 8 KB bis 64 KB. Die tatsächlichen IOPS sind von der Arbeitsauslastung abhängig. Weitere Informationen finden Sie unter [Daten-E/A-Governance](resource-limits-logical-server.md#resource-governance).

<sup>4</sup> Den Wert für „Max. gleichzeitige Worker (Anforderungen)“ für einzelne Datenbanken finden Sie unter [Ressourcenlimits für Singletons mit dem auf virtuellen Kernen (V-Kernen) basierenden Kaufmodell](resource-limits-vcore-single-databases.md). Wenn für den Pool für elastische Datenbanken beispielsweise Gen5 verwendet wird und die maximale Anzahl von V-Kernen pro Datenbank auf 2 festgelegt ist, ist der Wert für die Anzahl von gleichzeitigen Workern 200.  Wenn die maximale Anzahl von V-Kernen pro Datenbank auf 0,5 festgelegt ist, beträgt der Wert für die Anzahl von gleichzeitigen Workern 50, da unter Gen5 maximal 100 gleichzeitige Worker pro V-Kern verwendet werden. Für andere Einstellungen zur maximalen Anzahl von V-Kernen pro Datenbank (1 V-Kern oder weniger), wird die maximale Anzahl von gleichzeitigen Workern auf ähnliche Weise neu skaliert.

### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Dienstebene „Unternehmenskritisch“: Computeplattform der 5. Generation (Teil 2)

|Computegröße (Dienstziel)|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Computegeneration|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|V-Kerne|16|18|20|24|32|40|80|
|Arbeitsspeicher (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Max. Anzahl von Datenbanken pro Pool <sup>1</sup>|100|100|100|100|100|100|100|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|15,77|18,14|20,51|25,25|37,94|52,23|131,68|
|Maximale Datengröße (GB)|3072|3072|3072|4096|4096|4096|4096|
|Max. Protokollgröße (GB) <sup>2</sup>|922|922|922|1229|1229|1229|1229|
|Max. Datengröße von TempDB (GB)|512|576|640|768|1024|1280|2560|
|[Max. Größe des lokalen Speichers](resource-limits-logical-server.md#storage-space-governance) (GB)|4829|4829|4829|4829|4829|4829|4829|
|Speichertyp|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|E/A-Wartezeit (ungefähr)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|
|Max. Daten-IOPS pro Pool <sup>3</sup>|72.000|81.000|90.000|108.000|144.000|180.000|256.000|
|Maximale Protokollrate pro Pool (MBit/s)|120|120|120|120|120|120|120|
|Max. gleichzeitige Worker pro Pool (Anforderungen) <sup>4</sup>|1680|1890|2100|2520|3360|4.200|8.400|
|Max. Anzahl von gleichzeitigen Anmeldungen pro Pool (Anforderungen) <sup>4</sup>|1680|1890|2100|2520|3360|4.200|8.400|
|Max. gleichzeitige Sitzungen|30.000|30.000|30.000|30.000|30.000|30.000|30.000|
|Min/Max. V-Kern-Auswahl pro Datenbank für Pools für elastische Datenbanken|0, 0,25, 0,5, 1...16|0, 0,25, 0,5, 1...18|0, 0,25, 0,5, 1...20|0, 0,25, 0,5, 1...20, 24|0, 0,25, 0,5, 1...20, 24, 32|0, 0,25, 0,5, 1...20, 24, 32, 40|0, 0,25, 0,5, 1...20, 24, 32, 40, 80|
|Anzahl von Replikaten|4|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Horizontale Leseskalierung|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

<sup>1</sup> Weitere Überlegungen finden Sie unter [Ressourcenverwaltung in umfangreichen Pools für elastische Datenbanken](elastic-pool-resource-management.md).

<sup>2</sup> Für dokumentierte Werte der maximalen Datengröße. Bei einer Reduzierung der maximalen Datengröße reduziert sich die maximale Protokollgröße proportional.

<sup>3</sup> Der maximale Wert für E/A-Größen im Bereich von 8 KB bis 64 KB. Die tatsächlichen IOPS sind von der Arbeitsauslastung abhängig. Weitere Informationen finden Sie unter [Daten-E/A-Governance](resource-limits-logical-server.md#resource-governance).

<sup>4</sup> Den Wert für „Max. gleichzeitige Worker (Anforderungen)“ für einzelne Datenbanken finden Sie unter [Ressourcenlimits für Singletons mit dem auf virtuellen Kernen (V-Kernen) basierenden Kaufmodell](resource-limits-vcore-single-databases.md). Wenn für den Pool für elastische Datenbanken beispielsweise Gen5 verwendet wird und die maximale Anzahl von V-Kernen pro Datenbank auf 2 festgelegt ist, ist der Wert für die Anzahl von gleichzeitigen Workern 200.  Wenn die maximale Anzahl von V-Kernen pro Datenbank auf 0,5 festgelegt ist, beträgt der Wert für die Anzahl von gleichzeitigen Workern 50, da unter Gen5 maximal 100 gleichzeitige Worker pro V-Kern verwendet werden. Für andere Einstellungen zur maximalen Anzahl von V-Kernen pro Datenbank (1 V-Kern oder weniger), wird die maximale Anzahl von gleichzeitigen Workern auf ähnliche Weise neu skaliert.

## <a name="business-critical---provisioned-compute---m-series"></a>Unternehmenskritisch – bereitgestelltes Computing – M-Serie

### <a name="m-series-compute-generation-part-1"></a>Computegeneration der M-Serie (Teil 1)

|Computegröße (Dienstziel)|BC_M_8|BC_M_10|BC_M_12|BC_M_14|BC_M_16|BC_M_18|
|:---| ---:|---:|---:|---:|---:|---:|
|Computegeneration|M-Serie|M-Serie|M-Serie|M-Serie|M-Serie|M-Serie|
|V-Kerne|8|10|12|14|16|18|
|Arbeitsspeicher (GB)|235,4|294,3|353,2|412,0|470,9|529,7|
|Max. Anzahl von Datenbanken pro Pool <sup>1</sup>|100|100|100|100|100|100|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|64|80|96|112|128|150|
|Maximale Datengröße (GB)|512|640|768|896|1024|1152|
|Max. Protokollgröße (GB) <sup>2</sup>|171|213|256|299|341|384|
|Max. Datengröße von TempDB (GB)|256|320|384|448|512|576|
|[Max. Größe des lokalen Speichers](resource-limits-logical-server.md#storage-space-governance) (GB)|13.836|13.836|13.836|13.836|13.836|13.836|
|Speichertyp|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|E/A-Wartezeit (ungefähr)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|
|Max. Daten-IOPS pro Pool <sup>3</sup>|12.499|15.624|18.748|21.873|24.998|28.123|
|Maximale Protokollrate pro Pool (MBit/s)|48|60|72|84|96|108|
|Max. gleichzeitige Worker pro Pool (Anforderungen) <sup>4</sup>|800|1\.000|1\.200|1\.400|1\.600|1\.800|
|Max. Anzahl von gleichzeitigen Anmeldungen pro Pool (Anforderungen) <sup>4</sup>|800|1\.000|1\.200|1\.400|1\.600|1\.800|
|Max. gleichzeitige Sitzungen|30.000|30.000|30.000|30.000|30.000|30.000|
|Min/Max. V-Kern-Auswahl pro Datenbank für Pools für elastische Datenbanken|0–8|0–10|0–12|0–14|0–16|0–18|
|Anzahl von Replikaten|4|4|4|4|4|4|
|Multi-AZ|Nein|Nein|Nein|Nein|Nein|Nein|
|Horizontale Leseskalierung|Ja|Ja|Ja|Ja|Ja|Ja|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

<sup>1</sup> Weitere Überlegungen finden Sie unter [Ressourcenverwaltung in umfangreichen Pools für elastische Datenbanken](elastic-pool-resource-management.md).

<sup>2</sup> Für dokumentierte Werte der maximalen Datengröße. Bei einer Reduzierung der maximalen Datengröße reduziert sich die maximale Protokollgröße proportional.

<sup>3</sup> Der maximale Wert für E/A-Größen im Bereich von 8 KB bis 64 KB. Die tatsächlichen IOPS sind von der Arbeitsauslastung abhängig. Weitere Informationen finden Sie unter [Daten-E/A-Governance](resource-limits-logical-server.md#resource-governance).

<sup>4</sup> Den Wert für „Max. gleichzeitige Worker (Anforderungen)“ für einzelne Datenbanken finden Sie unter [Ressourcenlimits für Singletons mit dem auf virtuellen Kernen (V-Kernen) basierenden Kaufmodell](resource-limits-vcore-single-databases.md). Wenn für den Pool für elastische Datenbanken beispielsweise Gen5 verwendet wird und die maximale Anzahl von V-Kernen pro Datenbank auf 2 festgelegt ist, ist der Wert für die Anzahl von gleichzeitigen Workern 200.  Wenn die maximale Anzahl von V-Kernen pro Datenbank auf 0,5 festgelegt ist, beträgt der Wert für die Anzahl von gleichzeitigen Workern 50, da unter Gen5 maximal 100 gleichzeitige Worker pro V-Kern verwendet werden. Für andere Einstellungen zur maximalen Anzahl von V-Kernen pro Datenbank (1 V-Kern oder weniger), wird die maximale Anzahl von gleichzeitigen Workern auf ähnliche Weise neu skaliert.

### <a name="m-series-compute-generation-part-2"></a>Computegeneration der M-Serie (Teil 2)

|Computegröße (Dienstziel)|BC_M_20|BC_M_24|BC_M_32|BC_M_64|BC_M_128|
|:---| ---:|---:|---:|---:|---:|
|Computegeneration|M-Serie|M-Serie|M-Serie|M-Serie|M-Serie|
|V-Kerne|20|24|32|64|128|
|Arbeitsspeicher (GB)|588,6|706,3|941,8|1883,5|3767,0|
|Max. Anzahl von Datenbanken pro Pool <sup>1</sup>|100|100|100|100|100|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|172|216|304|704|1768|
|Maximale Datengröße (GB)|1280|1536|2048|4096|4096|
|Max. Protokollgröße (GB) <sup>2</sup>|427|512|683|1024|1024|
|Max. Datengröße von TempDB (GB)|640|768|1024|2048|4096|
|[Max. Größe des lokalen Speichers](resource-limits-logical-server.md#storage-space-governance) (GB)|13.836|13.836|13.836|13.836|13.836|
|Speichertyp|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|E/A-Wartezeit (ungefähr)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|
|Max. Daten-IOPS pro Pool <sup>3</sup>|31.248|37.497|49.996|99.993|160.000|
|Maximale Protokollrate pro Pool (MBit/s)|120|144|192|264|264|
|Max. gleichzeitige Worker pro Pool (Anforderungen) <sup>4</sup>|2\.000|2\.400|3\.200|6\.400|12.800|
|Max. Anzahl von gleichzeitigen Anmeldungen pro Pool (Anforderungen) <sup>4</sup>|2\.000|2\.400|3\.200|6\.400|12.800|
|Max. gleichzeitige Sitzungen|30.000|30.000|30.000|30.000|30.000|
|Anzahl von Replikaten|4|4|4|4|4|
|Multi-AZ|Nein|Nein|Nein|Nein|Nein|
|Horizontale Leseskalierung|Ja|Ja|Ja|Ja|Ja|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

<sup>1</sup> Weitere Überlegungen finden Sie unter [Ressourcenverwaltung in umfangreichen Pools für elastische Datenbanken](elastic-pool-resource-management.md).

<sup>2</sup> Für dokumentierte Werte der maximalen Datengröße. Bei einer Reduzierung der maximalen Datengröße reduziert sich die maximale Protokollgröße proportional.

<sup>3</sup> Der maximale Wert für E/A-Größen im Bereich von 8 KB bis 64 KB. Die tatsächlichen IOPS sind von der Arbeitsauslastung abhängig. Weitere Informationen finden Sie unter [Daten-E/A-Governance](resource-limits-logical-server.md#resource-governance).

<sup>4</sup> Den Wert für „Max. gleichzeitige Worker (Anforderungen)“ für einzelne Datenbanken finden Sie unter [Ressourcenlimits für Singletons mit dem auf virtuellen Kernen (V-Kernen) basierenden Kaufmodell](resource-limits-vcore-single-databases.md). Wenn für den Pool für elastische Datenbanken beispielsweise Gen5 verwendet wird und die maximale Anzahl von V-Kernen pro Datenbank auf 2 festgelegt ist, ist der Wert für die Anzahl von gleichzeitigen Workern 200.  Wenn die maximale Anzahl von V-Kernen pro Datenbank auf 0,5 festgelegt ist, beträgt der Wert für die Anzahl von gleichzeitigen Workern 50, da unter Gen5 maximal 100 gleichzeitige Worker pro V-Kern verwendet werden. Für andere Einstellungen zur maximalen Anzahl von V-Kernen pro Datenbank (1 V-Kern oder weniger), wird die maximale Anzahl von gleichzeitigen Workern auf ähnliche Weise neu skaliert.

## <a name="business-critical---provisioned-compute---dc-series"></a>Unternehmenskritisch – bereitgestelltes Computing – DC-Serie

|Computegröße (Dienstziel)|BC_DC_2|BC_DC_4|BC_DC_6|BC_DC_8|
|:--- | --: |--: |--: |--: |
|Computegeneration|DC|DC|DC|DC|
|V-Kerne|2|4|6|8|
|Arbeitsspeicher (GB)|9|18|27|36|
|Max. Anzahl von Datenbanken pro Pool <sup>1</sup>|50|100|100|100|
|Columnstore-Unterstützung|Ja|Ja|Ja|Ja|
|In-Memory-OLTP-Speicher (GB)|1.7|3,7|5.9|8,2|
|Maximale Datengröße (GB)|768|768|768|768|
|Max. Protokollgröße (GB) <sup>2</sup>|230|230|230|230|
|Max. Datengröße von TempDB (GB)|64|128|192|256|
|[Max. Größe des lokalen Speichers](resource-limits-logical-server.md#storage-space-governance) (GB)|1406|1406|1406|1406|
|Speichertyp|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|E/A-Wartezeit (ungefähr)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|1-2 ms (Schreiben)<br>1-2 ms (Lesen)|
|Max. Daten-IOPS pro Pool <sup>3</sup>|15750|31500|47250|56000|
|Maximale Protokollrate pro Pool (MBit/s)|20|60|90|120|
|Max. gleichzeitige Worker pro Pool (Anforderungen) <sup>4</sup>|168|336|504|672|
|Max. Anzahl von gleichzeitigen Anmeldungen pro Pool (Anforderungen) <sup>4</sup>|168|336|504|672|
|Max. gleichzeitige Sitzungen|30.000|30.000|30.000|30.000|
|Min/Max. V-Kern-Auswahl pro Datenbank für Pools für elastische Datenbanken|2|2...4|2...6|2...8|
|Anzahl von Replikaten|4|4|4|4|
|Multi-AZ|Nein|Nein|Nein|Nein|
|Horizontale Leseskalierung|Ja|Ja|Ja|Ja|
|Enthaltener Sicherungsspeicher|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|1 × Datenbankgröße|

<sup>1</sup> Weitere Überlegungen finden Sie unter [Ressourcenverwaltung in umfangreichen Pools für elastische Datenbanken](elastic-pool-resource-management.md).

<sup>2</sup> Für dokumentierte Werte der maximalen Datengröße. Bei einer Reduzierung der maximalen Datengröße reduziert sich die maximale Protokollgröße proportional.

<sup>3</sup> Der maximale Wert für E/A-Größen im Bereich von 8 KB bis 64 KB. Die tatsächlichen IOPS sind von der Arbeitsauslastung abhängig. Weitere Informationen finden Sie unter [Daten-E/A-Governance](resource-limits-logical-server.md#resource-governance).

<sup>4</sup> Den Wert für „Max. gleichzeitige Worker (Anforderungen)“ für einzelne Datenbanken finden Sie unter [Ressourcenlimits für Singletons mit dem auf virtuellen Kernen (V-Kernen) basierenden Kaufmodell](resource-limits-vcore-single-databases.md). Wenn für den Pool für elastische Datenbanken beispielsweise Gen5 verwendet wird und die maximale Anzahl von V-Kernen pro Datenbank auf 2 festgelegt ist, ist der Wert für die Anzahl von gleichzeitigen Workern 200.  Wenn die maximale Anzahl von V-Kernen pro Datenbank auf 0,5 festgelegt ist, beträgt der Wert für die Anzahl von gleichzeitigen Workern 50, da unter Gen5 maximal 100 gleichzeitige Worker pro V-Kern verwendet werden. Für andere Einstellungen zur maximalen Anzahl von V-Kernen pro Datenbank (1 V-Kern oder weniger), wird die maximale Anzahl von gleichzeitigen Workern auf ähnliche Weise neu skaliert.

## <a name="database-properties-for-pooled-databases"></a>Eigenschaften von Pooldatenbanken

Für jeden Pool für elastische Datenbanken können Sie optional pro Datenbank minimale und maximale virtuelle Kerne angeben, um Ressourcenverbrauchsmuster innerhalb des Pools zu ändern. Die angegebenen Mindest- und Höchstwerte gelten für alle Datenbanken im Pool. Das Anpassen der Mindest- und Höchstanzahl virtueller Kerne für einzelne Datenbanken im Pool wird nicht unterstützt. 

Sie können auch den maximalen Speicher pro Datenbank festlegen, um beispielsweise zu verhindern, dass eine Datenbank den gesamten Poolspeicher beansprucht. Diese Einstellung kann unabhängig für jede Datenbank konfiguriert werden.

In der folgenden Tabelle werden Eigenschaften für Pooldatenbanken beschrieben, die pro Datenbank festgelegt werden können: 

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| Maximale Anzahl virtueller Kerne pro Datenbank |Die maximale Anzahl von virtuellen Kernen, die jede Datenbank im Pool verwenden kann, sofern basierend auf der Nutzung durch andere Datenbanken im Pool verfügbar. Die maximale Anzahl von virtuellen Kernen pro Datenbank ist keine Ressourcengarantie für eine Datenbank. Wenn für die Workload in den einzelnen Datenbanken nicht alle verfügbaren Poolressourcen benötigt werden, um eine angemessene Leistung zu erzielen, empfiehlt es sich gegebenenfalls, die maximale Anzahl virtueller Kerne pro Datenbank festzulegen, um zu verhindern, dass eine einzelne Datenbank übermäßig viele Poolressourcen beansprucht. Sie sollten ein gewisses Maß an Mehrlast einplanen, da für den Pool im Allgemeinen von Nutzungsmustern starker und schwacher Auslastung ausgegangen wird, bei der aber nicht alle Datenbanken gleichzeitig stark ausgelastet sind. |
| Minimale Anzahl virtueller Kerne pro Datenbank |Die Mindestanzahl virtueller Kerne, die für jede Datenbank im Pool reserviert wird. Das Festlegen einer Mindestanzahl von virtuellen Kernen pro Datenbank empfiehlt sich gegebenenfalls, wenn Sie die Ressourcenverfügbarkeit für jede Datenbank unabhängig vom Ressourcenverbrauch durch andere Datenbanken im Pool gewährleisten möchten. Die Mindestanzahl von virtuellen Kernen pro Datenbank kann auf 0 festgelegt werden. Dies ist auch der Standardwert. Diese Eigenschaft ist auf einen Wert zwischen 0 und der durchschnittlichen Nutzung der virtuellen Kerne pro Datenbank festgelegt.|
| Max. Speicherkapazität pro Datenbank |Die maximale Datenbankgröße, die vom Benutzer für eine Datenbank in einem Pool festgelegt wird. Pooldatenbanken nutzen den zugeordneten Poolspeicher gemeinsam, daher ist die Größe, die eine Datenbank erreichen kann, auf den jeweils kleineren Wert des verbleibenden Poolspeichers oder die maximale Datenbankgröße beschränkt. Die maximale Datenbankgröße bezieht sich auf die maximale Größe der Datendateien und umfasst nicht den von der Protokolldatei belegten Speicherplatz. |
|||

> [!IMPORTANT]
> Da Ressourcen in einem Pool für elastische Datenbanken endlich sind, wird die Ressourcenauslastung durch die einzelnen Datenbanken implizit beschränkt, wenn Sie die Mindestanzahl virtueller Kerne pro Datenbank auf einen Wert größer null festlegen. Falls sich zu einem bestimmten Zeitpunkt die meisten Datenbanken in einem Pool im Leerlauf befinden, stehen Ressourcen, die zur Erfüllung der Mindestgarantie für virtuelle Kerne reserviert sind, für Datenbanken, die zu diesem Zeitpunkt aktiv sind, nicht zur Verfügung.
>
> Darüber hinaus wird die Anzahl von Datenbanken, die dem Pool hinzugefügt werden können, implizit beschränkt, wenn Sie die Mindestanzahl virtueller Kerne pro Datenbank auf einen Wert größer null festlegen. Wenn Sie also beispielsweise in einem Pool mit 20 virtuellen Kernen die Mindestanzahl virtueller Kerne auf „2“ festlegen, können dem Pool maximal 10 Datenbanken hinzugefügt werden, da für jede Datenbank 2 virtuelle Kerne reserviert werden.
> 

Die Eigenschaften pro Datenbank werden zwar in virtuellen Kernen ausgedrückt, steuern aber auch den Verbrauch anderer Ressourcentypen wie Daten-E/A, Protokoll-E/A und Arbeitsthreads. Wenn Sie Mindest- und Höchstwerte für virtuelle Kerne pro Datenbank anpassen, werden Reservierungen und Grenzwerte für alle Ressourcentypen proportional angepasst.

> [!NOTE]
> Die Ressourcengrenzwerte einzelner Datenbanken in Pools für elastische Datenbanken entsprechen im Allgemeinen den Grenzwerten einzelner Datenbanken außerhalb von Pools, welche die gleiche Computegröße (das gleiche Dienstziel) aufweisen. Auf eine GP_Gen4_1-Datenbank können z.B. max. 200 Worker gleichzeitig zugreifen. Entsprechend können auch maximal 200 Worker auf eine Datenbank in einem GP_Gen4_1-Pool zugreifen. Beachten Sie, dass die Gesamtanzahl gleichzeitiger Worker in einem GP_Gen4_1-Pool 210 beträgt.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu V-Kern-Ressourcenlimits für eine Einzeldatenbank finden Sie unter [Ressourcenlimits für Einzeldatenbanken, die das V-Kern-Kaufmodell verwenden](resource-limits-vcore-single-databases.md).
- Informationen zu DTU-Ressourcenlimits für einen Singleton finden Sie unter [Ressourcenlimits für Singletons, die das DTU-Kaufmodell verwenden](resource-limits-dtu-single-databases.md).
- Informationen zu DTU-Ressourcenlimits für Pools für elastische Datenbanken finden Sie unter [Ressourcenlimits für Pools für elastische Datenbanken, die das DTU-Kaufmodell verwenden](resource-limits-dtu-elastic-pools.md).
- Informationen zu den Ressourcenlimits für verwaltete Instanzen finden Sie unter [Ressourcenlimits bei verwalteten Instanzen](../managed-instance/resource-limits.md).
- Informationen zu allgemeinen Azure-Einschränkungen finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../../azure-resource-manager/management/azure-subscription-service-limits.md).
- Informationen zu Ressourcengrenzwerten auf Server- und Abonnementebene auf einem logischen SQL-Server finden Sie unter [Übersicht über Ressourcenlimits für einen logischen SQL-Server](resource-limits-logical-server.md).
