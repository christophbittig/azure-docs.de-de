---
title: Problembehandlung von falsch klassifiziertem Workload in einem dedizierten SQL-Pool
description: Identifizierung und Problembehandlung von Szenarien, in denen Workloads in einem dedizierten SQL-Pool in Azure Synapse Analytics in nicht vorgesehene Workload-Gruppen falsch klassifiziert werden.
author: SudhirRaparla
ms.author: nvraparl
manager: craigg
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql-dw
ms.date: 10/01/2021
ms.custom: template-how-to
ms.reviewer: wiassaf
ms.openlocfilehash: 1fa0394f3e711ec172f5a3ad32288f63bcf55dce
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390556"
---
# <a name="troubleshooting-a-misclassified-workload-in-azure-synapse-analytics"></a>Problembehandlung eines falsch klassifizierten Workloads in Azure Synapse Analytics

Dieser Artikel enthält Anleitungen zur Problembehandlung eines falsch klassifizierten Workloads und zur Ermittlung des Grundes für die Klassifizierung von Workloads in einem dedizierten SQL-Pool.

Azure Synapse Analytics bietet Funktionen zur Verwaltung von Workloads wie die [Klassifizierung von Workloads in geeignete Workload-Gruppen](sql-data-warehouse-workload-classification.md), [ die ](sql-data-warehouse-workload-importance.md)Zuweisung der Wichtigkeit[ und die ](sql-data-warehouse-workload-isolation.md) Isolierung von Ressourcen, um SLAs zu erfüllen. 

In einigen Szenarien kann eine Kombination dieser Funktionen jedoch zu einer Workload-Klassifizierung führen, die nicht der Absicht des Benutzers entspricht. In diesem Artikel werden solche häufigen Szenarien und deren Problembehandlung aufgeführt. Zunächst sollten Sie grundlegende Informationen zur Problembehandlung von falsch klassifizierten Workload-Szenarien abfragen.

> [!NOTE]
> Dieser Artikel bezieht sich nicht auf serverlose SQL-Pools in Azure Synapse Analytics.

## <a name="basic-troubleshooting-information"></a>Basic Informationen zur Problembehandlung

Zur Problembehandlung eines falsch klassifizierten Workload-Szenarios werden die folgenden Informationen benötigt:

- Liste aller [Workloadgruppen](#workload-groups)
- Liste aller [Workloadklassifizierer](#workload-classifiers) und der dazugehörigen Workload-Gruppen
- Liste der [Benutzer und dedizierten Workloadgruppen](#users-and-mapped-resource-classes) (system- und benutzerdefiniert) 
- Liste der [Workloadgruppen und Klassifiziererdetails](#workload-group-and-classifier-details-of-a-request) einer Anforderung


### <a name="workload-groups"></a>Workloadgruppen

#### <a name="azure-portal"></a>Azure-Portal

So erhalten Sie eine Liste aller Workloadgruppen (einschließlich Systemworkloadgruppen) und der zugehörigen Details im Azure-Portal:

1. Gehen Sie zu dem Azure Synapse-Arbeitsbereich, in dem der dedizierte SQL-Pool erstellt wurde, der Sie interessiert.
1. Auf der linken Seite werden alle Pool-Typen aufgelistet, die unter dem Arbeitsbereich erstellt wurden. Wählen Sie **SQL Pools** unter dem Abschnitt **Analytical Pools**.
1. Wählen Sie den dedizierten SQL-Pool von Interesse.
1. Wählen Sie im linken Fensterbereich **Workload-Verwaltung** unter **Einstellungen**.
1. Im Abschnitt **Workloadgruppen** werden alle Workloads aufgelistet. Standardmäßig werden nur **Benutzerdefinierte Workloadgruppen** aufgelistet. Um eine Liste sowohl der benutzerdefinierten als auch der systemdefinierten Workloadgruppen anzuzeigen, bearbeiten Sie den Filter und **Alle Auswählen**.

:::image type="content" source="./media/sql-data-warehouse-how-to-troubleshoot-missed-classification/filter-all-workload-groups.png" alt-text="Workloadgruppenlistenfilter" lightbox="./media/sql-data-warehouse-how-to-troubleshoot-missed-classification/filter-all-workload-groups.png":::

#### <a name="t-sql"></a>T-SQL

Um Workload-Gruppen unter Verwendung von T-SQL anzuzeigen, [verbinden Sie sich mit dem dedizierten SQL Pool unter Verwendung von SQL Server Management Studio (SSMS)](../sql/get-started-ssms.md) und machen Sie folgende Abfrage:
 
```sql
SELECT * FROM sys.workload_management_workload_groups;
```

Weitere Informationen finden Sie unter [sys.workload_management_workload_groups](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-groups-transact-sql).


### <a name="workload-classifiers"></a>Workloadklassifizierer

#### <a name="azure-portal"></a>Azure-Portal

Um alle Workloadklassifizierer (einschließlich der systemdefinierten Klassifizierer) nach Workloadgruppe im Azure-Portal aufzulisten, sehen Sie sich die Nummern an, die in der Spalte **Klassifizierer** in der Workloadgruppentabelle aufgeführt sind (siehe vorheriger Abschnitt).

:::image type="content" source="./media/sql-data-warehouse-how-to-troubleshoot-missed-classification/view-workload-classifiers.png" alt-text="Liste der Worklodgruppenklassifizierungen" lightbox="./media/sql-data-warehouse-how-to-troubleshoot-missed-classification/view-workload-classifiers.png":::

#### <a name="t-sql"></a>T-SQL

Verwenden Sie die folgende Abfrage, um die Liste aller Workloadklassifizierungen (einschließlich systemdefinierte Klassifizierer) mit T-SQL abzurufen:

```sql
SELECT * FROM sys.workload_management_workload_classifiers;
```

Weitere Informationen finden Sie unter [sys.workload_management_workload_classifiers](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifiers-transact-sql).

### <a name="users-and-mapped-resource-classes"></a>Benutzer und zugeordnete Ressourcenklassen

Verwenden Sie die folgende Abfrage, um eine Liste der systemdefinierten Ressourcenklassen und zugeordneten Benutzer in T-SQL abzurufen:

```sql
SELECT  r.name AS [Resource Class]
,       m.name AS [Member Name]
FROM    sys.database_role_members AS rm
JOIN    sys.database_principals AS r ON rm.role_principal_id = r.principal_id
JOIN    sys.database_principals AS m ON rm.member_principal_id = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc','staticrc10','staticrc20','staticrc30','staticrc40','staticrc50','staticrc60','staticrc70','staticrc80');
```

### <a name="workload-group-and-classifier-details-of-a-request"></a>Workloadgruppen- und Klassifizierungsdetails einer Anforderung

Der erste Schritt bei der Problembehandlung bei einer falsch klassifizierten Workload besteht darin, die Workloadgruppe und den Workloadklassifizierer für eine Abfrage zu identifizieren. Verwenden Sie die dynamische Verwaltungssicht [sys.dm_pdw_exec_requests,](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) um übermittelte Abfragen und deren Klassifizierung anzuzeigen:

```sql
SELECT * FROM sys.dm_pdw_exec_requests;
```

Weitere Informationen finden Sie unter [sys.dm_pdw_exec_requests](/sql/relational-databases/system-catalog-views/sys-dm-pdw-exec-requests-transact-sql).

## <a name="common-scenarios-of-misclassified-workloads"></a>Häufige Szenarien falsch klassifizierter Workloads

Im Folgenden finden Sie einige der häufige Szenarien, in denen unbeabsichtigte falsch-klassifizierte Workloads vorkommen können:

### <a name="mixed-usage-of-resource-classes-and-user-defined-workload-management"></a>Gemischte Nutzung von Ressourcenklassen und benutzerdefiniertem Workload-Management

In Szenarien, in denen Ressourcenklassen und Workloadgruppen zusammen verwendet werden, können Zuordnungen von Benutzerrollen zu Ressourcenklassen zu Konflikten mit Workloadklassifizierungsregeln für Workloads führen und zu einer unbeabsichtigten Abfrageklassifizierung führen. 

Betrachten Sie folgendes Szenario:

1. Ein Datenbankbenutzer, DBAUser, ist unter Verwendung des Verfahrens `sp_addrolemember` einer größeren Ressourcenklassenrolle zugeordnet.
1. DBAUser hat eine neue Workloadgruppe und einen Klassifizierer unter Verwendung des Workload-Management erstellt.
1. Ein neu erstellter Workloadklassifizierer ordnet die Datenbankrolle DBARole der Ressourcenklasse mediumrc mit hoher Wichtigkeit zu. 
1. DBAUser ist ein Mitglied der Datenbankrolle DBARole.
1. Wenn DBAUser eine Abfrage ausführt, wird erwartet, dass die Abfrage auf der Grundlage des Klassifizierers für die Workload auf mediumrc läuft. Stattdessen wird sie largerc zugewiesen, da die **Benutzer**-Zuordnung Vorrang vor der **Rollenmitgliedschaft**-Zuordnung zu einem Klassifizierer hat.

Es ist am besten, die gemischte Verwendung von Ressourcenklassen und Workload-Managementgruppen zu vermeiden, um Workload-Management zu betreiben. Weitere Informationen über die Schritte zum Konvertieren von Ressourcenklassen in Workloads finden Sie unter [Konvertieren von Ressourcenklassen zu Workloadgruppen](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md). 

Es kann jedoch Situationen geben, in denen sowohl Ressourcenklassen als auch Workload-Management zusammen verwendet werden müssen. Um die Problembehandlung von Falsch-Klassifizierungen zu vereinfachen, empfehlen wir in solchen Szenarien, die Ressourcenklassen-Rollenzuordnungen beim Erstellen von Workloadklassifizierern zu entfernen. Der folgende Code gibt Ressourcenklassen-Rollenmitgliedschaften zurück. Führen Sie [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) für alle Mitgliedsnamen aus, die von der entsprechenden Ressourcenklasse zurückgegeben werden.

```sql
SELECT  r.name AS [Resource Class]
, m.name AS membername
FROM    sys.database_role_members AS rm
JOIN    sys.database_principals AS r ON rm.role_principal_id = r.principal_id
JOIN    sys.database_principals AS m ON rm.member_principal_id = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc','staticrc10','staticrc20','staticrc30','staticrc40','staticrc50','staticrc60','staticrc70','staticrc80');

--for each row returned run
EXEC sp_droprolemember '[Resource Class]', membername;
```

### <a name="some-administrative-users-are-always-mapped-to-smallrc-workload-group"></a>Einige administrative Benutzer werden immer der Workloadgruppe smallrc zugewiesen

Betrachten Sie ein Szenario für die Azure Synapse Workspace SQL Administrator-Anmeldung, den Azure Synapse Azure Active Directory-Administrator (Benutzer oder Gruppenmitglied) oder einen Datenbankbesitzer. Diese Benutzer haben möglicherweise noch einen Workloadklassifizierer oder wurden zu einer anderen Ressourcenklassenrolle als smallrc hinzugefügt. Alle Abfragen, die von diesen Benutzern ausgeführt werden, werden weiterhin auf der Ressourcenklasse smallrc ausgeführt, auch wenn der Benutzer einer anderen Ressourcenklasse oder Workloadgruppe zugewiesen ist. 

**Empfehlung**: Diese administrativen Benutzer können ihre standardmäßige Workloadgruppe nicht ändern. Weitere Informationen finden Sie unter [Workload-Management mit Ressourcenklassen](resource-classes-for-workload-management.md#default-resource-class). Es wird empfohlen, dass kritische oder leistungsempfindliche Workloads nicht als eine dieser administrativen Benutzer im dedizierten SQL-Pool ausgeführt werden.

Die Azure Synapse Workspace SQL-Administratoranmeldung und der Azure Synapse Azure Active Directory-Administrator (Benutzer oder Gruppenmitglied) werden im Azure-Portal angegeben:

:::image type="content" source="./media/sql-data-warehouse-how-to-troubleshoot-missed-classification/identify-sql-admin.png" alt-text="Identifizierung des Dienst-Administrators anhand des Feldes Arbeitsbereich SQL Administratoranmeldung" lightbox="./media/sql-data-warehouse-how-to-troubleshoot-missed-classification/identify-sql-admin.png":::

Ebenso dürfen die Datenbankrollen Datenbankbesitzer (dbo) und **db_owner** ihre standardmäßige Ressourcenklasse nicht ändern. Wenn ein Benutzer entweder der Datenbankbesitzer ist oder unter der Datenbankrolle **db_owner** hinzugefügt wurde, werden alle vom Benutzer ausgeführten Abfragen standardmäßig an smallrc gesendet. Diese Rollen können nicht zu einer anderen Ressourcenklasse als smallrc hinzugefügt werden. Wenn jedoch ein Benutzer, der dieser Rolle angehört, seine Abfragen in eine andere Workloadgruppe klassifizieren möchte, kann er die MEMBERNAME-Option in der [Definition des Workloadklassifizierers](sql-data-warehouse-workload-classification.md)verwenden.


### <a name="use-workload-group-precedence-for-better-classification"></a>Verwenden der Rangfolge von Workloadgruppen für eine bessere Klassifizierung

In Szenarien, in denen Workloadklassifizierungen mehreren Workloadgruppen zugeordnet sind oder ein Benutzer mehreren Ressourcenklassen zugeordnet ist, bestimmt die Rangfolge die ausgewählte Workloadgruppe und Ressourcenklasse. Liste der Rangfolgeregeln: 

> [!NOTE]
> Wie im Abschnitt [Mischen von Ressourcenklassen und benutzerdefinierter Workloadverwaltung](#mixed-usage-of-resource-classes-and-user-defined-workload-management) erwähnt, wird nicht empfohlen, die Verwendung von Ressourcenklassen und benutzerdefinierten Workloadgruppen/-klassen zu kombinieren.

#### <a name="if-resource-classes-are-being-used"></a>Wenn Ressourcenklassen verwendet werden:

In Szenarien, in denen Ressourcenklassen verwendet werden, ist es am besten, einen dedizierten Benutzer für jeden Typ der ausgeführten Workloads zu erstellen. Wenn ein Benutzer jedoch Mitglied mehrerer Ressourcenklassen ist, greifen die in [Resource Class Precedence](resource-classes-for-workload-management.md#resource-class-precedence) dokumentierten Vorrangregeln:

1. Die dynamische Ressourcenklasse hat Vorrang vor der statischen Ressourcenklasse. Wenn ein Benutzer beispielsweise Mitglied von mediumrc (dynamisch) und staticrc80 (statisch) ist, werden Benutzerabfragen mit mediumrc ausgeführt.
1. Größere Ressourcenklassen werden gegenüber kleineren Ressourcenklassen bevorzugt. Wenn ein Benutzer z. B. Mitglied von staticrc20 und staticrc80 ist, werden Benutzerabfragen mit staticrc80 ausgeführt.

#### <a name="if-workload-management-capabilities-are-used"></a>Wenn Workload-Managementfähigkeiten verwendet werden

WLM bietet die Möglichkeit, mehrere Workloadklassifizierer für denselben Benutzer oder dieselbe Workloadgruppe zu erstellen. Die Klassifizierer-Definitionsanweisung hat mehrere Parameter, anhand derer eingehende Anforderungen an Workloads zugewiesen werden. Diese Parameter haben eine **Gewichtung** s-Bewertung, wie unten gezeigt, und diese Bewertung bestimmt die Rangfolge: 

|**Klassifizierungsparameter** |**Weight**   |
|---------------------|---------|
|MEMBERNAME:USER      |64       |
|MEMBERNAME:ROLE      |32       |
|WLM_LABEL            |16       |
|WLM_CONTEXT          |8        |
|START_TIME/END_TIME  |4        |

Lassen Sie uns diese Rangfolgeregeln anhand eines Beispiels in Aktion sehen. Wenn ein Benutzer wie folgt zwei Workloadklassifizierer erstellt:

```sql
CREATE WORKLOAD CLASSIFIER CLASSIFIER-1 WITH  
( WORKLOAD_GROUP = 'wgDataLoad'
 ,MEMBERNAME     = 'USER-1'  
 ,WLM_LABEL      = 'dimension_loads' 
 ,IMPORTANCE     = 'High');

CREATE WORKLOAD CLASSIFIER CLASSIFIER-2 WITH  
( WORKLOAD_GROUP = 'wgUserqueries'
 ,MEMBERNAME     = 'USER-1'  
 ,START_TIME     = '18:00' 
 ,END_TIME       = '07:30'
 ,IMPORTANCE     = 'Low')
```

Abfragen, die von User-1 übermittelt werden, können über beide Klassifizierer übermittelt werden. Abfrage, die von User-1 mit der Bezeichnung "dimension_loads" zwischen 18:00 Uhr und 7:00 Uhr UTC wird zu `wgDashboards` zugewiesen, da die Gewichtungszahl von WLM_LABEL höher als START_TIME/END_TIME ist. Die Gewichtung von `CLASSIFIER-1` ist 80 (64 für Benutzer plus 16 für WLM_LABEL). Die Gewichtung von `CLASSIFIER-2` ist 68 (64 für Benutzer 4 für START_TIME/END_TIME). 

Weitere Informationen zur Workload-Klassifizierung finden Sie unter [Klassifizierungsgewichtung](sql-data-warehouse-workload-classification.md#classification-weighting) und [Abfrage-Bezeichnung](../sql/develop-label.md).

### <a name="what-happens-if-precedence-in-workload-classification-leads-to-a-tie"></a>Was geschieht, wenn der Vorrang bei der Workload-Klassifizierung zu einem Rang-Gleichstand führt?

Auch nach Anwendung der Vorrangigkeit der Workloadklassifizierer ist es möglich, dass eine Abfrage in mehrere Workloadgruppen klassifiziert wird. Betrachten Sie zum Beispiel die folgenden Klassifizierer:

```sql
CREATE WORKLOAD CLASSIFIER CLASSIFIER-1 WITH  
( WORKLOAD_GROUP = 'wgDataLoad'
 ,MEMBERNAME     = 'USER-1'  
 ,WLM_LABEL      = 'dimension_loads' 
 ,IMPORTANCE     = 'High');

CREATE WORKLOAD CLASSIFIER CLASSIFIER-2 WITH  
( WORKLOAD_GROUP = 'wgUserqueries'
 ,MEMBERNAME     = 'USER-1'  
 ,WLM_LABEL      = 'dimension_loads' 
 ,IMPORTANCE     = 'Low');
```

Wenn ein Benutzer eine Abfrage mit `OPTION (LABEL = 'dimension_loads')` ausführt, kann sie auf der Grundlage der Vorrangregeln entweder in `wgDataLoad` oder `wgUserqueries` klassifiziert werden. Aber welche Workloadgruppe wird gewählt?

#### <a name="workload-group-and-classifier-tie-breakers"></a>Workloadgruppe und Klassifizierer Tie-Breaker

Wenn es einen Rang-Gleichstand bei Workloadgruppen oder Klassifizierern gibt, kommt folgende Rangfolge zum Tragen:

1. Die Workloadgruppe mit der höchsten Ressourcenzuweisung wird gewählt. Dieses Verhalten optimiert die Leistung in Szenarien, in denen Anmeldungen Mitglieder mehrerer Ressourcenklassen sind. Dieses Verhalten gewährleistet auch die Abwärtskompatibilität.  
    
Betrachten Sie die folgenden zwei Workloadgruppen und Workloadklassifizierer:
    
```sql
-- Workload Groups
CREATE WORKLOAD GROUP wgDataLoad
WITH
( MIN_PERCENTAGE_RESOURCE = 26 
,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 2              
,CAP_PERCENTAGE_RESOURCE = 100 )

CREATE WORKLOAD GROUP wgUserqueries
WITH
( MIN_PERCENTAGE_RESOURCE = 15
,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5               
,CAP_PERCENTAGE_RESOURCE = 50 );

--Workload Classifiers
CREATE WORKLOAD CLASSIFIER CLASSIFIER-1 WITH  
( WORKLOAD_GROUP = 'wgDataLoad'
,MEMBERNAME     = 'USER-1'  
,WLM_LABEL      = 'dimension_loads' 
,IMPORTANCE     = 'High');

CREATE WORKLOAD CLASSIFIER CLASSIFIER-2 WITH  
( WORKLOAD_GROUP = 'wgUserqueries'
,MEMBERNAME     = 'USER-1'  
,WLM_LABEL      = 'dimension_loads' 
,IMPORTANCE     = 'High');
```
                  
Wenn ein Benutzer eine Abfrage mit `OPTION (LABEL = 'dimension_loads')` ausführt, erfüllt die Abfrage die Kriterien für beide Klassifizierer. Die Benutzeranforderung wird jedoch an die Workloadgruppe `wgUserQueries` geroutet, da sie über eine höhere minimale Ressourcenzuordnung pro Anforderung verfügt.

2. Als Nächstes werden die Parallelitätseinstellung und die verfügbare Parallelität für die jeweiligen Workloadgruppen für Tie-Breaker berücksichtigt. Die Workloadgruppe mit der höchsten verfügbaren Parallelität zu einem Zeitpunkt, zu dem die Anforderung eingetroffen ist, gibt der Abfrage die beste Chance, ausgeführt zu werden. 

Ziehen Sie die Folgenden zwei Workloadgruppen und Workloadklassifizierer in Betracht:

```sql
--Workload Groups
CREATE WORKLOAD GROUP wgDataLoad
WITH
( MIN_PERCENTAGE_RESOURCE = 30              
 ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 2 (concurrency of 15)
 ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 2
 ,CAP_PERCENTAGE_RESOURCE = 100 );

CREATE WORKLOAD GROUP wgUserqueries
WITH
( MIN_PERCENTAGE_RESOURCE = 30
 ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 2 (concurrency of 15)
 ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 2
 ,CAP_PERCENTAGE_RESOURCE = 100 );

-- Workload Classifiers
CREATE WORKLOAD CLASSIFIER CLASSIFIER-1 WITH  
( WORKLOAD_GROUP = 'wgDataLoad'
 ,MEMBERNAME     = 'USER-1'  
 ,WLM_LABEL      = 'dimension_loads' 
 ,IMPORTANCE     = 'High');

CREATE WORKLOAD CLASSIFIER CLASSIFIER-2 WITH  
( WORKLOAD_GROUP = 'wgUserqueries'
 ,MEMBERNAME     = 'USER-1'  
 ,WLM_LABEL      = 'dimension_loads' 
 ,IMPORTANCE     = 'High');
```
              
Wenn ein Benutzer eine Abfrage mit `OPTION (LABEL = 'dimension_loads')` ausführt, haben beide Klassifizierer einen Rang-Gleichstand, da die Abfrage die Kriterien für beide erfüllt. Wenn der Benutzer die Abfrage übermittelt, stellen Sie sich das Szenario vor, in dem fünf gleichzeitige Abfragen in der Gruppe `wgUserqueries` und zehn Abfragen in der Gruppe ausgeführt `wgDataLoad` werden. Die Anforderung des Benutzers wird unter Verwendung von `CLASSIFIER-2` an die Workloadgruppe `wgUserqueries` weitergeleitet, da die Workloadgruppe `wgUserqueries` zum Zeitpunkt der Abfrage des Benutzers eine höhere Gleichzeitigkeit aufweist.

3. Als Nächstes wird die Wichtigkeitseinstellung der Anforderung für Tie-Breaker berücksichtigt. Wenn es bei der Workload-Klassifizierung unter Verwendung von Vorrangregeln einen Rang-Gleichstand gab: Die Anforderung wird an die Workloadgruppe mit der höchsten Wichtigkeit weitergeleitet. Für weitere Informationen siehe [Workloadwichtigkeit](sql-data-warehouse-workload-importance.md).

4. Schließlich wird die Erstellungszeit der Workloadgruppe für Tie-Breaker berücksichtigt. Die Anforderung des Benutzers wird an die Workloadgruppe weitergeleitet, die zuletzt erstellt wurde. 


## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zur Workload-Klassifizierung finden Sie unter [ Workloadklassifizierung](sql-data-warehouse-workload-classification.md).
- Weitere Informationen zur Workload Wichtigkeit finden Sie unter [Workload Wichtigkeit](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Wechseln zu „Konfigurieren der Workloadpriorität“](sql-data-warehouse-how-to-configure-workload-importance.md)
 
