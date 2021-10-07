---
title: Ressourcenklassen für die Workloadverwaltung
description: Enthält eine Anleitung für die Verwendung von Ressourcenklassen zum Verwalten der Parallelität und von Computeressourcen für Abfragen in Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: c7c7dccf94c1211ef318d538c3a5c74ae16e427e
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2021
ms.locfileid: "129388810"
---
# <a name="workload-management-with-resource-classes-in-azure-synapse-analytics"></a>Workloadverwaltung mit Ressourcenklassen in Azure Synapse Analytics

Dies ist eine Anleitung für die Verwendung von Ressourcenklassen zum Verwalten von Arbeitsspeicher und Parallelität für Synapse-SQL-Poolabfragen in Azure Synapse.  

## <a name="what-are-resource-classes"></a>Was sind Ressourcenklassen?

Die Leistungskapazität einer Abfrage richtet sich nach der Ressourcenklasse des Benutzers.  Ressourcenklassen sind vorab festgelegte Ressourcenlimits in Synapse-SQL-Pools, die Computeressourcen und Parallelität für die Abfrageausführung regeln. Ressourcenklassen können Sie beim Konfigurieren von Ressourcen für Ihre Abfragen unterstützen, indem sie Ihnen die Festlegung von Grenzwerten für die Anzahl der gleichzeitig ausgeführten Abfragen und für die Serverressourcen, die den einzelnen Abfragen zugewiesen sind, ermöglichen.  Dabei erfolgt ein Ausgleich zwischen Speicher und Parallelität.

- Kleinere Ressourcenklassen verringern den maximalen Speicher pro Abfrage, erhöhen jedoch die Parallelität.
- Größere Ressourcenklassen erhöhen den maximalen Speicher pro Abfrage, verringern jedoch die Parallelität.

Es gibt zwei Arten von Ressourcenklassen:

- Statische Ressourcenklassen, die gut für erhöhte Parallelität bei fester Datasetgröße geeignet sind.
- Dynamische Ressourcenklassen, die gut für wachsende Datasets geeignet sind, die aufgrund des zentral hochskalierten Servicelevels eine höhere Leistung erfordern.

Ressourcenklassen verwenden Parallelitätsslots zum Messen des Ressourcenverbrauchs.  [Parallelitätsslots](#concurrency-slots) werden weiter unten in diesem Artikel erläutert.

- Die Ressourcenauslastung für die Ressourcenklassen finden Sie unter [Speicher- und Parallelitätsgrenzwerte](memory-concurrency-limits.md).
- Um die Ressourcenklasse anzupassen, können Sie die Abfrage unter einem anderen Benutzerkonto ausführen oder [Ressourcenklassenmitgliedschaft des aktuellen Benutzers ändern](#change-a-users-resource-class).

### <a name="static-resource-classes"></a>Statische Ressourcenklassen

Bei statischen Ressourcenklassen wird unabhängig von der aktuellen Leistungsstufe die gleiche Menge an Arbeitsspeicher zugeteilt. Diese wird in [Data Warehouse-Einheiten](what-is-a-data-warehouse-unit-dwu-cdwu.md) (Data Warehouse Units, DWUs) gemessen. Da die Speicherbelegung bei Abfragen unabhängig von der Leistungsstufe identisch ist, kann durch [horizontale Skalierung das Data Warehouse](quickstart-scale-compute-portal.md) eine größere Anzahl von Abfragen innerhalb einer Ressourcenklasse ausgeführt werden.  Statische Ressourcenklassen sind ideal, wenn das Datenvolumen bekannt und konstant ist.

Die statische Ressourcenklassen werden mit diesen vordefinierten Datenbankrollen implementiert:

- staticrc10
- staticrc20
- staticrc30
- staticrc40
- staticrc50
- staticrc60
- staticrc70
- staticrc80

### <a name="dynamic-resource-classes"></a>Dynamische Ressourcenklassen

Bei dynamischen Ressourcenklassen wird je nach aktueller Dienstebene eine variable Menge an Arbeitsspeicher zugeteilt. Während statische Ressourcenklassen für höhere Parallelität und statische Datenvolumen nützlich sind, sind dynamische Ressourcenklassen besser geeignet für eine wachsende oder variable Menge an Daten.  Ihre Abfragen erhalten so automatisch mehr Arbeitsspeicher, wenn Sie auf eine höhere Dienstebene hochskalieren.  

Die dynamischen Ressourcenklassen werden mit diesen vordefinierten Datenbankrollen implementiert:

- smallrc
- mediumrc
- largerc
- xlargerc

Die Speicherbelegung für jede Ressourcenklasse lautet wie folgt.

| Dienstebene  | smallrc           | mediumrc               | largerc                | xlargerc               |
|:--------------:|:-----------------:|:----------------------:|:----------------------:|:----------------------:|
| DW100c         | 25 %               | 25 %                    | 25 %                    | 70 %                    |
| DW200c         | 12,5 %             | 12,5 %                  | 22 %                    | 70 %                    |
| DW300c         | 8 %                | 10 %                    | 22 %                    | 70 %                    |
| DW400c         | 6,25 %             | 10 %                    | 22 %                    | 70 %                    |
| DW500c         | 5 %                | 10 %                    | 22 %                    | 70 %                    |
| DW1000c bis<br> DW30000c | 3 %       | 10 %                    | 22 %                    | 70 %                    |

### <a name="default-resource-class"></a>Standardressourcenklasse

Standardmäßig ist jeder Benutzer Mitglied der dynamischen Ressourcenklasse **smallrc**.

Die Ressourcenklasse des Dienstadministrators ist unter smallrc festgelegt und kann nicht geändert werden.  Der Dienstadministrator ist der Benutzer, der während des Bereitstellungsprozesses erstellt wird.  In diesem Kontext entspricht der Dienstadministrator der Anmeldung, die beim Erstellen eines neuen Synapse-SQL-Pools mit einem neuen Server unter „Serveradministratoranmeldung“ angegeben wurde.

> [!NOTE]
> Als „Active Directory-Administrator“ definierte Benutzer oder Gruppen sind auch Dienstadministratoren.

## <a name="resource-class-operations"></a>Ressourcenklassenvorgänge

Ressourcenklassen sind für die Verbesserung der Leistung für Datenverwaltungs- und -änderungsaktivitäten ausgelegt. Komplexe Abfragen können innerhalb einer großen Ressourcenklasse ebenfalls besser ausgeführt werden. Beispielsweise kann die Abfrageleistung für große Joins und Sortierungen verbessert werden, sofern die Ressourcenklasse für die Abfrageausführung im Speicher groß genug ist.

### <a name="operations-governed-by-resource-classes"></a>Über Ressourcenklassen gesteuerte Vorgänge

Die folgenden Vorgänge werden über Ressourcenklassen gesteuert:

- INSERT-SELECT, UPDATE, DELETE
- SELECT (wenn Benutzertabellen abgefragt werden)
- ALTER INDEX – REBUILD oder REORGANIZE
- ALTER TABLE REBUILD
- CREATE INDEX
- CREATE CLUSTERED COLUMNSTORE INDEX
- CREATE TABLE AS SELECT (CTAS)
- Laden von Daten
- Datenverschiebungsvorgänge, die vom DMS (Data Movement Service) ausgeführt werden

> [!NOTE]  
> SELECT-Anweisungen für dynamische Verwaltungssichten (Dynamic Management Views, DMVs) oder andere Systemsichten werden nicht durch Einschränkungen der Parallelität gesteuert. Sie können das System unabhängig von der Anzahl der darin ausgeführten Abfragen überwachen.

### <a name="operations-not-governed-by-resource-classes"></a>Nicht über Ressourcenklassen gesteuerte Vorgänge

Einige Abfragen werden immer in der Ressourcenklasse smallrc ausgeführt, und zwar auch dann, wenn der Benutzer Mitglied einer größeren Ressourcenklasse ist. Diese ausgenommenen Abfragen werden für das Parallelitätslimit nicht berücksichtigt. Wenn das Parallelitätslimit beispielsweise auf 16 festgelegt ist, können viele Benutzer eine Auswahl aus Systemsichten treffen, ohne dass sich dies auf die verfügbaren Parallelitätsslots auswirkt.

Die folgenden Anweisungen sind von Ressourcenklassen ausgenommen und werden immer in smallrc ausgeführt:

- CREATE oder DROP TABLE
- ALTER TABLE… SWITCH, SPLIT oder MERGE PARTITION
- ALTER INDEX DISABLE
- DROP INDEX
- CREATE, UPDATE oder DROP STATISTICS
- TRUNCATE TABLE
- ALTER AUTHORIZATION
- CREATE LOGIN
- CREATE, ALTER oder DROP USER
- CREATE, ALTER oder DROP PROCEDURE
- CREATE oder DROP VIEW
- INSERT VALUES
- SELECT aus Systemsichten und DMVs
- EXPLAIN
- DBCC

<!--
Removed as these two are not confirmed / supported under Azure Synapse Analytics
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## <a name="concurrency-slots"></a>Parallelitätsslots

Parallelitätsslots stellen eine einfache Möglichkeit dar, die verfügbaren Ressourcen für die Ausführung von Abfragen zu verfolgen. Sie ähneln Tickets für Sitzplätze in einem Konzert, die Sie erwerben, da die Sitzplatzanzahl begrenzt ist. Die Gesamtanzahl der Parallelitätsslots pro Data Warehouse richtet sich nach der Dienstebene. Bevor eine Abfrage ausgeführt werden kann, muss sie genügend Parallelitätsslots reservieren. Wenn eine Abfrage abgeschlossen wurde, werden ihre Parallelitätsslots freigegeben.  

- Eine Abfrage mit 10 Parallelitätsslots kann auf 5-mal mehr Computeressourcen als eine Abfrage mit 2 Parallelitätsslots zugreifen.
- Wenn jede Abfrage 10 Parallelitätsslots erfordert und es 40 Parallelitätsslots gibt, können nur 4 Abfragen gleichzeitig ausgeführt werden.

Nur von Ressourcen verwaltete Abfragen verbrauchen Parallelitätsslots. Systemabfragen und einige einfache Abfragen verbrauchen keine Slots. Die genaue Anzahl der verbrauchten Parallelitätsslots richtet sich nach der Ressourcenklasse der Abfrage.

## <a name="view-the-resource-classes"></a>Anzeigen der Ressourcenklassen

Ressourcenklassen werden mit vordefinierten Datenbankrollen implementiert. Es gibt zwei Arten von Ressourcenklassen: dynamisch und statisch. Um eine Liste der Ressourcenklassen anzuzeigen, verwenden Sie die folgende Abfrage:

```sql
SELECT name
FROM   sys.database_principals
WHERE  name LIKE '%rc%' AND type_desc = 'DATABASE_ROLE';
```

## <a name="change-a-users-resource-class"></a>Ändern der Ressourcenklasse eines Benutzers

Ressourcenklassen werden implementiert, indem Benutzer Datenbankrollen zugewiesen werden. Wenn ein Benutzer eine Abfrage ausführt, wird die Abfrage mit der Ressourcenklasse des Benutzers ausgeführt. Ist ein Benutzer beispielsweise Mitglied der Datenbankrolle „staticrc10“, werden die dazugehörigen Abfragen mit kleinen Arbeitsspeichermengen ausgeführt. Wenn ein Datenbankbenutzer Mitglied der Datenbankrollen „xlargerc“ oder „staticrc80“ ist, werden die dazugehörigen Abfragen mit großen Arbeitsspeichermengen ausgeführt.

Verwenden Sie [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), um die Ressourcenklasse eines Benutzers zu erhöhen und den Benutzer zu einer Datenbankrolle einer großen Ressourcenklasse hinzuzufügen.  Der folgende Code fügt einen Benutzer zur Datenbankrolle „largerc“ hinzu.  Jede Anforderung ruft 22 % des Systemspeichers ab.

```sql
EXEC sp_addrolemember 'largerc', 'loaduser';
```

Verwenden Sie [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), um die Ressourcenklasse herabzusetzen.  Wenn „Loaduser“ kein Mitglied und keine andere Ressourcenklasse ist, wechseln Sie in die Standardressourcenklasse „smallrc“ mit einer Speicherzuweisung von 3 %.  

```sql
EXEC sp_droprolemember 'largerc', 'loaduser';
```

## <a name="resource-class-precedence"></a>Rangfolge von Ressourcenklassen

Benutzer können Mitglieder mehrerer Ressourcenklassen sein. Wenn ein Benutzer mehr als einer Ressourcenklasse angehört:

- Dynamische Ressourcenklassen haben Vorrang vor statischen Ressourcenklassen. Wenn ein Benutzer beispielsweise sowohl Mitglied von mediumrc(dynamic) als auch von staticrc80 (static) ist, werden die Abfragen mit mediumrc ausgeführt.
- Größere Ressourcenklassen haben Vorrang vor kleineren Ressourcenklassen. Wenn ein Benutzer beispielsweise Mitglied von mediumrc und largerc ist, werden Abfragen mit largerc ausgeführt. Wenn ein Benutzer sowohl Mitglied von staticrc20 als auch von statirc80 ist, werden Abfragen entsprechend mit staticrc80-Ressourcenzuteilungen ausgeführt.

## <a name="recommendations"></a>Empfehlungen

>[!NOTE]
>Nutzen Sie die Vorteile der Workloadverwaltungsfunktionen ([Workloadisolation](sql-data-warehouse-workload-isolation.md), [Klassifizierung](sql-data-warehouse-workload-classification.md) und [Wichtigkeit](sql-data-warehouse-workload-importance.md)), um eine bessere Kontrolle über Ihre Workload und die vorhersagbare Leistung zu erhalten.  

Wir empfehlen Ihnen, einen Benutzer zu erstellen, der speziell für das Ausführen eines bestimmten Typs von Abfrage- oder Ladevorgängen dediziert ist. Versehen Sie den Benutzer mit einer permanenten Ressourcenklasse, anstatt die Ressourcenklasse häufig zu ändern. Da bei statischen Ressourcenklassen eine stärkere umfassende Steuerung der Workload möglich ist, sollten Sie diese zuerst verwenden, bevor Sie den Einsatz von dynamischen Ressourcenklassen erwägen.

### <a name="resource-classes-for-load-users"></a>Ressourcenklassen für Benutzer, die Ladevorgänge durchführen

Für `CREATE TABLE` werden standardmäßig Columnstore-Indizes verwendet. Das Komprimieren von Daten in einem Columnstore-Index ist ein speicherintensiver Vorgang, und eine hohe Arbeitsspeicherauslastung kann dazu führen, dass die Indexqualität nicht optimal ist. Speicherauslastung kann dazu führen, dass beim Laden von Daten eine höhere Ressourcenklasse erforderlich ist. Gehen Sie wie folgt vor, um sicherzustellen, dass für Ladevorgänge genügend Arbeitsspeicher vorhanden ist: Erstellen Sie einen Benutzer, der für das Ausführen von Ladevorgängen vorgesehen ist, und weisen Sie diesen Benutzer einer höheren Ressourcenklasse zu.

Der zum effizienten Verarbeiten von Lasten erforderliche Arbeitsspeicher richtet sich nach der Art der geladenen Tabelle und der Datengröße. Weitere Informationen zu Arbeitsspeicheranforderungen finden Sie unter [Maximieren der Zeilengruppenqualität für Columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

Nachdem Sie die Arbeitsspeicheranforderung ermittelt haben, können Sie auswählen, ob der Benutzer für Ladevorgänge einer statischen oder dynamischen Ressourcenklasse zugewiesen werden soll.

- Verwenden Sie eine statische Ressourcenklasse, wenn Tabellenspeicheranforderungen in einen bestimmten Bereich fallen. Ladevorgänge werden mit dem passenden Arbeitsspeicher ausgeführt. Wenn Sie das Data Warehouse skalieren, benötigen die Ladevorgänge nicht mehr Arbeitsspeicher. Mit einer statischen Ressourcenklasse bleiben die Arbeitsspeicherzuteilungen konstant. Bei diesem konsistenten Ansatz wird Arbeitsspeicher gespart, und es können mehr Abfragen gleichzeitig ausgeführt werden. Wir empfehlen Ihnen, für neue Lösungen zuerst statische Ressourcenklassen zu verwenden, da diese eine bessere Kontrolle ermöglichen.
- Nutzen Sie eine dynamische Ressourcenklasse, wenn die Tabellenspeicheranforderungen stark variieren. Für Ladevorgänge ist unter Umständen mehr Arbeitsspeicher erforderlich, als von der aktuellen DWU- oder cDWU-Ebene bereitgestellt werden. Durch das Skalieren des Data Warehouse wird mehr Arbeitsspeicher für Ladevorgänge hinzugefügt, sodass diese Vorgänge schneller durchgeführt werden können.

### <a name="resource-classes-for-queries"></a>Ressourcenklassen für Abfragen

Einige Abfragen sind rechenintensiv, während dies für andere nicht zutrifft.  

- Wählen Sie eine dynamische Ressourcenklasse, wenn Abfragen komplex sind, aber keine hohe Parallelität benötigen.  Das Erstellen von täglichen oder wöchentlichen Berichten ist für Ressourcen beispielsweise eine nur gelegentlich auftretende Anforderung. Wenn für die Berichte große Datenmengen verarbeitet werden, wird durch das Skalieren des Data Warehouse mehr Arbeitsspeicher für die vorhandene Ressourcenklasse des Benutzers bereitgestellt.
- Wählen Sie eine statische Ressourcenklasse, wenn sich die Anforderungen an Ressourcen im Laufe des Tages immer wieder ändern. Eine statische Ressourcenklasse ist beispielsweise gut geeignet, wenn das Data Warehouse von vielen Benutzern abgefragt wird. Beim Skalieren des Data Warehouse ändert sich die Arbeitsspeichermenge, die dem Benutzer zugeteilt ist, nicht. Daher können für das System mehr Abfragen parallel ausgeführt werden.

Die richtige Speicherzuweisung hängt von vielen Faktoren ab, z. B. der Menge der abgefragten Daten, der Art der Tabellenschemas und von verschiedenen Prädikaten für das Verknüpfen, Auswählen und Gruppieren. Im Allgemeinen ermöglicht das Zuteilen von mehr Arbeitsspeicher eine schnellere Durchführung von Abfragen, aber die allgemeine Parallelität wird verringert. Wenn Parallelität keine Rolle spielt, hat eine übermäßige Arbeitsspeicherzuweisung keine negativen Auswirkungen auf den Durchsatz.

Verwenden Sie zum Optimieren der Leistung unterschiedliche Ressourcenklassen. Im nächsten Abschnitt ist eine gespeicherte Prozedur angegeben, mit der Sie die beste Ressourcenklasse ermitteln können.

## <a name="example-code-for-finding-the-best-resource-class"></a>Beispielcode zum Ermitteln der besten Ressourcenklasse

Mithilfe der folgenden angegebenen gespeicherten Prozedur können Sie Parallelitäts- und Arbeitsspeicherzuweisung pro Ressourcenklasse für ein bestimmtes Servicelevelziel sowie die beste Ressourcenklasse für speicherintensive CCI-Vorgänge in einer nicht partitionierten CCI-Tabelle für eine bestimmte Ressourcenklasse ermitteln:

Hier wird der Zweck dieser gespeicherten Prozedur beschrieben:

1. Anzeigen der Parallelitäts- und Speicherzuweisung pro Ressourcenklasse für ein bestimmtes Servicelevelziel. Der Benutzer muss hierzu wie im Beispiel gezeigt NULL als Schema und Tabellenname angeben.  
2. Anzeigen der am besten geeigneten Ressourcenklasse für speicherintensive CCI-Vorgänge (Laden, Kopieren einer Tabelle, Indexneuerstellung usw.) in einer nicht partitionierten CCI-Tabelle für eine bestimmte Ressourcenklasse. Die gespeicherte Prozedur verwendet ein Tabellenschema, um die erforderliche Speicherzuweisung zu ermitteln.

### <a name="dependencies--restrictions"></a>Abhängigkeiten und Einschränkungen

- Diese gespeicherte Prozedur dient nicht zum Berechnen des Arbeitsspeicherbedarfs für eine partitionierte CCI-Tabelle.
- Diese gespeicherte Prozedur berücksichtigt keine Arbeitsspeicheranforderungen für den SELECT-Teil von CTAS/INSERT-SELECT und geht davon aus, dass es sich um SELECT handelt.
- Für diese gespeicherte Prozedur wird eine temporäre Tabelle verwendet, die in der Sitzung verfügbar ist, in der diese gespeicherte Prozedur erstellt wurde.
- Diese gespeicherte Prozedur hängt von den aktuellen Angeboten (z. B. Hardwarekonfiguration, DMS-Konfiguration) ab und funktioniert nicht richtig, wenn eines davon geändert wird.  
- Diese gespeicherte Prozedur hängt von vorhandenen Parallelitätslimitangeboten ab und funktioniert nicht richtig, wenn diese geändert werden.  
- Diese gespeicherte Prozedur hängt von vorhandenen Ressourcenklassenangeboten ab und funktioniert nicht richtig, wenn diese geändert werden.  

>[!NOTE]  
>Wenn Sie nach der Ausführung der gespeicherten Prozedur mit den bereitgestellten Parametern keine Ausgabe erhalten, kann dies zwei Gründe haben.
>
>1. Der DW-Parameter enthält einen ungültigen SLO-Wert.
>2. Für den CCI-Vorgang in der Tabelle ist keine passende Ressourcenklasse vorhanden.
>
>Beispielsweise beträgt bei DW100c die höchste verfügbare Arbeitsspeicherzuweisung auch dann 1 GB, wenn das Tabellenschema breit genug ist, um die Anforderung von 1 GB zu überschreiten.

### <a name="usage-example"></a>Verwendungsbeispiel

Syntax:  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`
  
1. @DWU:: Gibt entweder einen NULL-Parameter zum Extrahieren der aktuellen DWU aus der DW DB oder eine beliebige unterstützte DWU im Format „DW100c“ an.
2. @SCHEMA_NAME:: Gibt einen Schemanamen der Tabelle an.
3. @TABLE_NAME:: Gibt einen relevanten Tabellennamen an.

Beispiele für Ausführung dieser gespeicherte Prozedur:

```sql
EXEC dbo.prc_workload_management_by_DWU 'DW2000c', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000c', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```

Mit der folgenden Anweisung wird Table1 erstellt, die in den vorherigen Beispielen verwendet wird.
`CREATE TABLE Table1 (a int, b varchar(50), c decimal (18,10), d char(10), e varbinary(15), f float, g datetime, h date);`

### <a name="stored-procedure-definition"></a>Definition der gespeicherten Prozedur

```sql
-------------------------------------------------------------------------------
-- Dropping prc_workload_management_by_DWU procedure if it exists.
-------------------------------------------------------------------------------
IF EXISTS (SELECT * FROM sys.objects WHERE type = 'P' AND name = 'prc_workload_management_by_DWU')
DROP PROCEDURE dbo.prc_workload_management_by_DWU
GO

-------------------------------------------------------------------------------
-- Creating prc_workload_management_by_DWU.
-------------------------------------------------------------------------------
CREATE PROCEDURE dbo.prc_workload_management_by_DWU
(@DWU VARCHAR(8),
 @SCHEMA_NAME VARCHAR(128),
 @TABLE_NAME VARCHAR(128)
)
AS

IF @DWU IS NULL
BEGIN
-- Selecting proper DWU for the current DB if not specified.

SELECT @DWU = 'DW'+ CAST(CASE WHEN Mem> 4 THEN Nodes*500
  ELSE Mem*100
  END AS VARCHAR(10)) +'c'
    FROM (
      SELECT Nodes=count(distinct n.pdw_node_id), Mem=max(i.committed_target_kb/1000/1000/60)
        FROM sys.dm_pdw_nodes n
        CROSS APPLY sys.dm_pdw_nodes_os_sys_info i
        WHERE type = 'COMPUTE')A
END

-- Dropping temp table if exists.
IF OBJECT_ID('tempdb..#ref') IS NOT NULL
BEGIN
  DROP TABLE #ref;
END;

-- Creating ref. temp table (CTAS) to hold mapping info.
CREATE TABLE #ref
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
WITH
-- Creating concurrency slots mapping for various DWUs.
alloc
AS
(
SELECT 'DW100c' AS DWU,4 AS max_queries,4 AS max_slots,1 AS slots_used_smallrc,1 AS slots_used_mediumrc,2 AS slots_used_largerc,4 AS slots_used_xlargerc,1 AS slots_used_staticrc10,2 AS slots_used_staticrc20,4 AS slots_used_staticrc30,4 AS slots_used_staticrc40,4 AS slots_used_staticrc50,4 AS slots_used_staticrc60,4 AS slots_used_staticrc70,4 AS slots_used_staticrc80
  UNION ALL
   SELECT 'DW200c',8,8,1,2,4,8,1,2,4,8,8,8,8,8
  UNION ALL
   SELECT 'DW300c',12,12,1,2,4,8,1,2,4,8,8,8,8,8
  UNION ALL
   SELECT 'DW400c',16,16,1,4,8,16,1,2,4,8,16,16,16,16
  UNION ALL
   SELECT 'DW500c',20,20,1,4,8,16,1,2,4,8,16,16,16,16
  UNION ALL
   SELECT 'DW1000c',32,40,1,4,8,28,1,2,4,8,16,32,32,32
  UNION ALL
   SELECT 'DW1500c',32,60,1,6,13,42,1,2,4,8,16,32,32,32
  UNION ALL
   SELECT 'DW2000c',48,80,2,8,17,56,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW2500c',48,100,3,10,22,70,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW3000c',64,120,3,12,26,84,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW5000c',64,200,6,20,44,140,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW6000c',128,240,7,24,52,168,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW7500c',128,300,9,30,66,210,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW10000c',128,400,12,40,88,280,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW15000c',128,600,18,60,132,420,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW30000c',128,1200,36,120,264,840,1,2,4,8,16,32,64,128
)
-- Creating workload mapping to their corresponding slot consumption and default memory grant.
,map  
AS
(
  SELECT CONVERT(varchar(20), 'SloDWGroupSmall') AS wg_name, slots_used_smallrc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupMedium') AS wg_name, slots_used_mediumrc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupLarge') AS wg_name, slots_used_largerc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupXLarge') AS wg_name, slots_used_xlargerc AS slots_used FROM alloc WHERE DWU = @DWU
  UNION ALL
  SELECT 'SloDWGroupC00',1
  UNION ALL
    SELECT 'SloDWGroupC01',2
  UNION ALL
    SELECT 'SloDWGroupC02',4
  UNION ALL
    SELECT 'SloDWGroupC03',8
  UNION ALL
    SELECT 'SloDWGroupC04',16
  UNION ALL
    SELECT 'SloDWGroupC05',32
  UNION ALL
    SELECT 'SloDWGroupC06',64
  UNION ALL
    SELECT 'SloDWGroupC07',128
)

-- Creating ref based on current / asked DWU.
, ref
AS
(
  SELECT  a1.*
  ,       m1.wg_name          AS wg_name_smallrc
  ,       m1.slots_used * 250 AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.slots_used * 250 AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.slots_used * 250 AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.slots_used * 250 AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.slots_used * 250 AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.slots_used * 250 AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.slots_used * 250 AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.slots_used * 250 AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.slots_used * 250 AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.slots_used * 250 AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.slots_used * 250 AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.slots_used * 250 AS tgt_mem_grant_MB_staticrc80
  FROM alloc a1
  JOIN map   m1  ON a1.slots_used_smallrc     = m1.slots_used and m1.wg_name = 'SloDWGroupSmall'
  JOIN map   m2  ON a1.slots_used_mediumrc    = m2.slots_used and m2.wg_name = 'SloDWGroupMedium'
  JOIN map   m3  ON a1.slots_used_largerc     = m3.slots_used and m3.wg_name = 'SloDWGroupLarge'
  JOIN map   m4  ON a1.slots_used_xlargerc    = m4.slots_used and m4.wg_name = 'SloDWGroupXLarge'
  JOIN map   m5  ON a1.slots_used_staticrc10    = m5.slots_used and m5.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m6  ON a1.slots_used_staticrc20    = m6.slots_used and m6.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m7  ON a1.slots_used_staticrc30    = m7.slots_used and m7.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m8  ON a1.slots_used_staticrc40    = m8.slots_used and m8.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m9  ON a1.slots_used_staticrc50    = m9.slots_used and m9.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m10  ON a1.slots_used_staticrc60    = m10.slots_used and m10.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m11  ON a1.slots_used_staticrc70    = m11.slots_used and m11.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m12  ON a1.slots_used_staticrc80    = m12.slots_used and m12.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  WHERE   a1.DWU = @DWU
)
SELECT  DWU
,       max_queries
,       max_slots
,       slots_used
,       wg_name
,       tgt_mem_grant_MB
,       up1 as rc
,       (ROW_NUMBER() OVER(PARTITION BY DWU ORDER BY DWU)) as rc_id
FROM
(
    SELECT  DWU
    ,       max_queries
    ,       max_slots
    ,       slots_used
    ,       wg_name
    ,       tgt_mem_grant_MB
    ,       REVERSE(SUBSTRING(REVERSE(wg_names),1,CHARINDEX('_',REVERSE(wg_names),1)-1)) as up1
    ,       REVERSE(SUBSTRING(REVERSE(tgt_mem_grant_MBs),1,CHARINDEX('_',REVERSE(tgt_mem_grant_MBs),1)-1)) as up2
    ,       REVERSE(SUBSTRING(REVERSE(slots_used_all),1,CHARINDEX('_',REVERSE(slots_used_all),1)-1)) as up3
    FROM    ref AS r1
    UNPIVOT
    (
        wg_name FOR wg_names IN (wg_name_smallrc,wg_name_mediumrc,wg_name_largerc,wg_name_xlargerc,
        wg_name_staticrc10, wg_name_staticrc20, wg_name_staticrc30, wg_name_staticrc40, wg_name_staticrc50,
        wg_name_staticrc60, wg_name_staticrc70, wg_name_staticrc80)
    ) AS r2
    UNPIVOT
    (
        tgt_mem_grant_MB FOR tgt_mem_grant_MBs IN (tgt_mem_grant_MB_smallrc,tgt_mem_grant_MB_mediumrc,
        tgt_mem_grant_MB_largerc,tgt_mem_grant_MB_xlargerc, tgt_mem_grant_MB_staticrc10, tgt_mem_grant_MB_staticrc20,
        tgt_mem_grant_MB_staticrc30, tgt_mem_grant_MB_staticrc40, tgt_mem_grant_MB_staticrc50,
        tgt_mem_grant_MB_staticrc60, tgt_mem_grant_MB_staticrc70, tgt_mem_grant_MB_staticrc80)
    ) AS r3
    UNPIVOT
    (
        slots_used FOR slots_used_all IN (slots_used_smallrc,slots_used_mediumrc,slots_used_largerc,
        slots_used_xlargerc, slots_used_staticrc10, slots_used_staticrc20, slots_used_staticrc30,
        slots_used_staticrc40, slots_used_staticrc50, slots_used_staticrc60, slots_used_staticrc70,
        slots_used_staticrc80)
    ) AS r4
) a
WHERE   up1 = up2
AND     up1 = up3
;

-- Getting current info about workload groups.
WITH  
dmv  
AS  
(
  SELECT
          rp.name                                           AS rp_name
  ,       rp.max_memory_kb*1.0/1048576                      AS rp_max_mem_GB
  ,       (rp.max_memory_kb*1.0/1024)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_MB
  ,       (rp.max_memory_kb*1.0/1048576)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_GB
  ,       wg.name                                           AS wg_name
  ,       wg.importance                                     AS importance
  ,       wg.request_max_memory_grant_percent               AS request_max_memory_grant_percent
  FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
  JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
                                                                  AND wg.pool_id      = rp.pool_id
  WHERE   rp.name = 'SloDWPool'
  GROUP BY
          rp.name
  ,       rp.max_memory_kb
  ,       wg.name
  ,       wg.importance
  ,       wg.request_max_memory_grant_percent
)
-- Creating resource class name mapping.
,names
AS
(
  SELECT 'smallrc' as resource_class, 1 as rc_id
  UNION ALL
    SELECT 'mediumrc', 2
  UNION ALL
    SELECT 'largerc', 3
  UNION ALL
    SELECT 'xlargerc', 4
  UNION ALL
    SELECT 'staticrc10', 5
  UNION ALL
    SELECT 'staticrc20', 6
  UNION ALL
    SELECT 'staticrc30', 7
  UNION ALL
    SELECT 'staticrc40', 8
  UNION ALL
    SELECT 'staticrc50', 9
  UNION ALL
    SELECT 'staticrc60', 10
  UNION ALL
    SELECT 'staticrc70', 11
  UNION ALL
    SELECT 'staticrc80', 12
)
,base AS
(   SELECT  schema_name
    ,       table_name
    ,       SUM(column_count)                   AS column_count
    ,       ISNULL(SUM(short_string_column_count),0)   AS short_string_column_count
    ,       ISNULL(SUM(long_string_column_count),0)    AS long_string_column_count
    FROM    (   SELECT  sm.name                                             AS schema_name
                ,       tb.name                                             AS table_name
                ,       COUNT(co.column_id)                                 AS column_count
                           ,       CASE    WHEN co.system_type_id IN (36,43,106,108,165,167,173,175,231,239)
                                AND  co.max_length <= 32
                                THEN COUNT(co.column_id)
                        END                                                 AS short_string_column_count
                ,       CASE    WHEN co.system_type_id IN (165,167,173,175,231,239)
                                AND  co.max_length > 32 and co.max_length <=8000
                                THEN COUNT(co.column_id)
                        END                                                 AS long_string_column_count
                FROM    sys.schemas AS sm
                JOIN    sys.tables  AS tb   on sm.[schema_id] = tb.[schema_id]
                JOIN    sys.columns AS co   ON tb.[object_id] = co.[object_id]
                           WHERE tb.name = @TABLE_NAME AND sm.name = @SCHEMA_NAME
                GROUP BY sm.name
                ,        tb.name
                ,        co.system_type_id
                ,        co.max_length            ) a
GROUP BY schema_name
,        table_name
)
, size AS
(
SELECT  schema_name
,       table_name
,       75497472                                            AS table_overhead

,       column_count*1048576*8                              AS column_size
,       short_string_column_count*1048576*32                       AS short_string_size,       (long_string_column_count*16777216) AS long_string_size
FROM    base
UNION
SELECT CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as schema_name
         ,CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as table_name
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as table_overhead
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as column_size
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as short_string_size

,CASE WHEN COUNT(*) = 0 THEN 0 END as long_string_size
FROM   base
)
, load_multiplier as
(
SELECT  CASE
          WHEN FLOOR(8 * (CAST (CAST(REPLACE(REPLACE(@DWU,'DW',''),'c','') AS INT) AS FLOAT)/6000)) > 0
            AND CHARINDEX(@DWU,'c')=0
          THEN FLOOR(8 * (CAST (CAST(REPLACE(REPLACE(@DWU,'DW',''),'c','') AS INT) AS FLOAT)/6000))
          ELSE 1
        END AS multiplication_factor
)
       SELECT  r1.DWU
       , schema_name
       , table_name
       , rc.resource_class as closest_rc_in_increasing_order
       , max_queries_at_this_rc = CASE
             WHEN (r1.max_slots / r1.slots_used > r1.max_queries)
                  THEN r1.max_queries
             ELSE r1.max_slots / r1.slots_used
                  END
       , r1.max_slots as max_concurrency_slots
       , r1.slots_used as required_slots_for_the_rc
       , r1.tgt_mem_grant_MB  as rc_mem_grant_MB
       , CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) AS est_mem_grant_required_for_cci_operation_MB
       FROM    size
       , load_multiplier
       , #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Verwalten von Datenbankbenutzern und der Sicherheit finden Sie unter [Schützen einer Datenbank in Azure Synapse](sql-data-warehouse-overview-manage-security.md). Weitere Informationen dazu, wie größere Ressourcenklassen die Qualität des gruppierten Columnstore-Index verbessern können, finden Sie unter [Arbeitsspeicheroptimierung für Columnstore-Komprimierung](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).
