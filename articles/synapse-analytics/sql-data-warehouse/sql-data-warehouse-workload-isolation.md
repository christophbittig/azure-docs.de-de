---
title: Workloadisolation
description: Leitfaden zum Festlegen der Workloadisolation mit Arbeitsauslastungsgruppen in Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/16/2021
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 7b78fc9a0bb292cbc124e1629351ddd9cc2191e7
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132547581"
---
# <a name="azure-synapse-analytics-workload-group-isolation"></a>Azure Synapse Analytics – Workloadisolation mit Arbeitsauslastungsgruppen

In diesem Artikel wird erläutert, wie mithilfe von Arbeitsauslastungsgruppen die Workloadisolation konfiguriert, Ressourcen eingeschlossen und Laufzeitregeln für die Abfrageausführung angewendet werden können.

## <a name="workload-groups"></a>Workloadgruppen

Arbeitsauslastungsgruppen sind Container für eine Reihe von Anforderungen und bilden die Grundlage für die Konfiguration der Workloadverwaltung, einschließlich der Workloadisolation, auf einem System.  Arbeitsauslastungsgruppen werden mithilfe der Syntax [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) erstellt.  Bei einer einfachen Konfiguration der Workloadverwaltung können Datenladevorgänge und Benutzerabfragen verwaltet werden.  Beispielsweise werden mit einer Arbeitsauslastungsgruppe namens `wgDataLoads` Workloadaspekte für Daten definiert, die in das System geladen werden. Mit einer Arbeitsauslastungsgruppe namens `wgUserQueries` werden Workloadaspekte für Benutzer definiert, die Abfragen zum Lesen von Daten aus dem System ausführen.

In den folgenden Abschnitten wird erläutert, wie Arbeitsauslastungsgruppen die Möglichkeit zum Definieren der Isolation und Kapselung, Anfordern der Ressourcendefinition und Einhalten von Ausführungsregeln bietet.

## <a name="resource-governance"></a>Ressourcengovernance

Arbeitsauslastungsgruppen steuern Arbeitsspeicher- und CPU-Ressourcen.  Datenträger- und Netzwerk-E/A sowie tempdb werden nicht geregelt.  Die Ressourcen-Governance für Arbeitsspeicher und CPU lautet wie folgt:

Der Arbeitsspeicher wird auf Anforderungsebene bestimmt und während der gesamten Dauer der Anforderung gespeichert.  Weitere Informationen zum Konfigurieren der Arbeitsspeichermenge pro Anforderung finden Sie unter [Definition von Ressourcen pro Anforderung](#resources-per-request-definition).  Der Parameter MIN_PERCENTAGE_RESOURCE Für die Arbeitsauslastungsgruppe verwendet ausschließlich Arbeitsspeicher für diese Arbeitsauslastungsgruppe.  Der Parameter CAP_PERCENTAGE_RESOURCE für die Arbeitsauslastungsgruppe ist eine harte Grenze für den Arbeitsspeicher, den eine Arbeitsauslastungsgruppe nutzen kann.

CPU-Ressourcen werden auf Arbeitsauslastungsgruppenebene bestimmt und von allen Anforderungen innerhalb einer Arbeitsauslastungsgruppe gemeinsam genutzt.  CPU-Ressourcen sind im Vergleich zum Arbeitsspeicher, der für eine Anforderung für die Dauer der Ausführung ausschließlich verwendet wird, fließend.  Da es sich bei der CPU um eine fließende Ressource handelt, können nicht verwendete CPU-Ressourcen von allen Arbeitsauslastungsgruppen genutzt werden.  Dies bedeutet, dass die CPU-Auslastung den Parameter CAP_PERCENTAGE_RESOURCE für die Arbeitsauslastungsgruppe überschreiten kann.  Dies bedeutet auch, dass der Parameter MIN_PERCENTAGE_RESOURCE für die Arbeitsauslastungsgruppe keine harte Reservierung wie der Arbeitsspeicher ist.  Wenn CPU-Ressourcen in Konflikt stehen, wird die Verwendung an der Definition CAP_PERCENTAGE_RESOURCE für Arbeitsauslastungsgruppen ausgerichtet.


## <a name="workload-isolation"></a>Workloadisolation

Workloadisolation bedeutet, dass Ressourcen ausschließlich für eine Arbeitsauslastungsgruppe reserviert sind.  Workloadisolation wird erreicht, indem der Parameter MIN_PERCENTAGE_RESOURCE in der Syntax [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) auf einen Wert größer als 0 (null) festgelegt wird.  Bei Workloads mit kontinuierlicher Ausführung, bei denen strenge SLAs eingehalten werden müssen, wird durch Isolation sichergestellt, dass immer Ressourcen für die Arbeitsauslastungsgruppe verfügbar sind.

Durch Konfigurieren der Workloadisolation wird implizit ein garantierter Grad an Parallelität definiert. Wenn zum Beispiel für eine Arbeitsauslastungsgruppe ein MIN_PERCENTAGE_RESOURCE auf 30 % und REQUEST_MIN_RESOURCE_GRANT_PERCENT auf 2 % festgelegt wird, ist für die Arbeitsauslastungsgruppe ein Grad an Parallelität von 15 garantiert.  Der Grad an Parallelität ist sichergestellt, da jederzeit 15-2 % Ressourcenslots innerhalb der Arbeitsauslastungsgruppe reserviert sind (unabhängig davon, wie REQUEST_ *MAX* _RESOURCE_GRANT_PERCENT konfiguriert ist).  Wenn REQUEST_MAX_RESOURCE_GRANT_PERCENT größer als REQUEST_MIN_RESOURCE_GRANT_PERCENT und CAP_PERCENTAGE_RESOURCE größer als MIN_PERCENTAGE_RESOURCE ist, können zusätzliche Ressourcen pro Anforderung hinzugefügt werden (basierend auf der Ressourcenverfügbarkeit).  Wenn REQUEST_MAX_RESOURCE_GRANT_PERCENT und REQUEST_MIN_RESOURCE_GRANT_PERCENT gleich sind und CAP_PERCENTAGE_RESOURCE größer als MIN_PERCENTAGE_RESOURCE ist, ist zusätzliche Parallelität möglich.  Sehen Sie sich die folgende Methode zur Bestimmung der garantierten Parallelität an:

[Garantierte Parallelität] = [`MIN_PERCENTAGE_RESOURCE`]/[`REQUEST_MIN_RESOURCE_GRANT_PERCENT`]

> [!NOTE]
> Für min_percentage_resource gibt es bestimmte Mindestwerte für das Servicelevel.  Weitere Informationen finden Sie unter [Gültige Werte](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json?view=azure-sqldw-latest&preserve-view=true#effective-values).

Wenn keine Workloadisolation vorhanden ist, werden Anforderungen im [freigegebenen Pool](#shared-pool-resources) von Ressourcen ausgeführt.  Der Zugriff auf Ressourcen im freigegebenen Pool ist nicht garantiert und wird auf Basis der [Priorität](sql-data-warehouse-workload-importance.md) zugewiesen.

Die Workloadisolation sollte mit Vorsicht konfiguriert werden, da die Ressourcen der Arbeitsauslastungsgruppe auch dann zugeordnet werden, wenn keine aktiven Anforderungen in der Arbeitsauslastungsgruppe vorhanden sind. Eine überkonfigurierte Isolation kann zu einer verringerten Gesamtsystemauslastung führen.

Benutzer sollten eine Lösung für die Workloadverwaltung vermeiden, bei der eine Workloadisolation von 100 % konfiguriert ist: Eine Isolation von 100 % wird erreicht, wenn die Summe von MIN_PERCENTAGE_RESOURCE, die für alle Arbeitsauslastungsgruppen konfiguriert ist, 100 % beträgt.  Diese Art der Konfiguration ist zu restriktiv und starr und lässt wenig Raum für Ressourcenanforderungen, die versehentlich falsch klassifiziert werden. Es gibt eine Regelung, die es erlaubt, dass eine Anforderung aus Arbeitsauslastungsgruppen ausgeführt wird, die nicht für die Isolation konfiguriert sind. Die Ressourcen, die dieser Anforderung zugeordnet sind, werden in den DMVs des Systems als 0 (null) angezeigt und nehmen eine „smallrc“-Ebene der Ressourcenzuweisung von reservierten Ressourcen des Systems an.

> [!NOTE]
> Um optimale Ressourcennutzung zu gewährleisten, sollten Sie eine Lösung für die Workloadverwaltung in Betracht ziehen, die eine gewisse Isolation zur Sicherstellung, dass SLAs eingehalten werden, und eine Mischung mit freigegebenen Ressourcen, auf die basierend auf der [Workloadpriorität](sql-data-warehouse-workload-importance.md) zugegriffen wird, bietet.

## <a name="workload-containment"></a>Workloadkapselung

Workloadkapselung bezieht sich auf das Begrenzen der Ressourcenmenge, die von einer Arbeitsauslastungsgruppe genutzt werden kann.  Workloadkapselung wird erreicht, indem der Parameter CAP_PERCENTAGE_RESOURCE in der Syntax [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) auf einen Wert kleiner als 100 festgelegt wird.  Stellen Sie sich ein Szenario vor, in dem Benutzer Lesezugriff auf das System benötigen, damit eine Was-wäre-wenn-Analyse über Ad-hoc-Abfragen ausgeführt werden kann.  Diese Arten von Anforderungen können negative Auswirkungen auf andere Workloads haben, die auf dem System ausgeführt werden.  Durch Konfigurieren der Kapselung wird sichergestellt, dass die Ressourcenmenge begrenzt ist.

Durch Konfigurieren der Workloadkapselung wird implizit ein maximaler Grad an Parallelität definiert.  Wenn CAP_PERCENTAGE_RESOURCE auf 60 % und REQUEST_MIN_RESOURCE_GRANT_PERCENT auf 1 % festgelegt wird, ist für die Arbeitsauslastungsgruppe ein Grad an Parallelität von 60 zulässig.  Sehen Sie sich die folgende Methode zur Bestimmung der maximalen Parallelität an:

[Maximale Parallelität] = [`CAP_PERCENTAGE_RESOURCE`] / [`REQUEST_MIN_RESOURCE_GRANT_PERCENT`]

> [!NOTE]
> Der gültige Wert für CAP_PERCENTAGE_RESOURCE einer Arbeitsauslastungsgruppe erreicht keine 100 %, wenn Arbeitsauslastungsgruppen mit einem Wert für MIN_PERCENTAGE_RESOURCE höher als 0 (null) erstellt werden.  Informationen zu gültigen Laufzeitwerten finden Sie unter [sys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="resources-per-request-definition"></a>Definition von Ressourcen pro Anforderung

Arbeitsauslastungsgruppen bieten mit den Parametern REQUEST_MIN_RESOURCE_GRANT_PERCENT und REQUEST_MAX_RESOURCE_GRANT_PERCENT in der Syntax [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) einen Mechanismus zum Definieren der Mindest- und Höchstmenge an Ressourcen, die pro Anforderung zugewiesen werden.  Die Ressource ist in diesem Fall der Arbeitsspeicher. Die CPU-Ressourcen-Governance wird im Abschnitt [Ressourcen-Governance](#resource-governance) behandelt.

> [!NOTE]
> REQUEST_MAX_RESOURCE_GRANT_PERCENT ist ein optionaler Parameter, der standardmäßig auf den gleichen Wert gesetzt wird, der für REQUEST_MIN_RESOURCE_GRANT_PERCENT angegeben ist.

Wie beim Auswählen einer Ressourcenklasse wird beim Konfigurieren von REQUEST_MIN_RESOURCE_GRANT_PERCENT der Wert für die von einer Anforderung genutzten Ressourcen festgelegt.  Die Menge der Ressourcen, die durch den festgelegten Wert angegeben wird, ist für die Zuordnung zur Anforderung vor Beginn der Ausführung garantiert.  Kunden, die von Ressourcenklassen zu Arbeitsauslastungsgruppen migrieren, sollten als Ausgangspunkt dem Artikel zur [Vorgehensweise](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md) für die Zuordnung von Ressourcenklassen zu Arbeitsauslastungsgruppen folgen.

Wenn REQUEST_MAX_RESOURCE_GRANT_PERCENT auf einen höheren Wert als REQUEST_MIN_RESOURCE_GRANT_PERCENT festgelegt wird, kann das System mehr Ressourcen pro Anforderung zuordnen.  Beim Planen einer Anforderung bestimmt das System die tatsächliche Ressourcenzuordnung für die Anforderung, die zwischen REQUEST_MIN_RESOURCE_GRANT_PERCENT und REQUEST_MAX_RESOURCE_GRANT_PERCENT liegt, basierend auf der Verfügbarkeit von Ressourcen im freigegebenen Pool und der aktuellen Systemauslastung.  Die Ressourcen müssen beim Planen der Abfrage im [freigegebenen Pool](#shared-pool-resources) von Ressourcen vorhanden sein.  

> [!NOTE]
> REQUEST_MIN_RESOURCE_GRANT_PERCENT und REQUEST_MAX_RESOURCE_GRANT_PERCENT weisen gültige Werte auf, die von den gültigen Werten für MIN_PERCENTAGE_RESOURCE und CAP_PERCENTAGE_RESOURCE abhängig sind.  Informationen zu gültigen Laufzeitwerten finden Sie unter [sys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="execution-rules"></a>Ausführungsregeln

Bei Ad-hoc-Berichterstellungssystemen können Kunden versehentlich Endlosabfragen ausführen, die sich erheblich auf die Produktivität anderer Anwendungen auswirken.  Systemadministratoren sind gezwungen, Zeit mit dem Abbrechen von Endlosabfragen zu verbringen, um Systemressourcen freizugeben.  Arbeitsauslastungsgruppen bieten die Möglichkeit, eine Überschreitungsregel für die Abfrageausführung zu konfigurieren, um Abfragen abzubrechen, die den angegebenen Wert überschritten haben.  Die Regel wird durch Festlegen des Parameters QUERY_EXECUTION_TIMEOUT_SEC in der Syntax [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) konfiguriert.

## <a name="shared-pool-resources"></a>Ressourcen im freigegebenen Pool

Ressourcen im freigegebenen Pool sind die Ressourcen, die nicht für die Isolation konfiguriert sind.  Arbeitsauslastungsgruppen, bei denen MIN_PERCENTAGE_RESOURCE auf 0 (null) festgelegt ist, nutzen Ressourcen im freigegebenen Pool zum Ausführen von Anforderungen.  Arbeitsauslastungsgruppen mit einem höheren Wert für CAP_PERCENTAGE_RESOURCE als MIN_PERCENTAGE_RESOURCE verwenden ebenfalls freigegebene Ressourcen.  Die Menge der im freigegebenen Pool verfügbaren Ressourcen wird wie folgt berechnet.

[Freigegebener Pool] = 100 - [Summe von `MIN_PERCENTAGE_RESOURCE` für alle Arbeitsauslastungsgruppen]

Der Zugriff auf Ressourcen im freigegebenen Pool wird auf Basis der [Priorität](sql-data-warehouse-workload-importance.md) zugewiesen.  Anforderungen mit der gleichen Prioritätsstufe greifen auf Ressourcen im freigegebenen Pool nach dem FIFO-Prinzip (First in/First out) zu.

## <a name="next-steps"></a>Nächste Schritte

- [Schnellstart: Konfigurieren der Workloadisolation](quickstart-configure-workload-isolation-tsql.md)
- [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Konvertieren von Ressourcenklassen in Arbeitsauslastungsgruppen](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md)
- [Überwachung über das Workload-Verwaltungsportal](sql-data-warehouse-workload-management-portal-monitor.md)  
