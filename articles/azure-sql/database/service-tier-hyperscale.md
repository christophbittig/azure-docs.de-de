---
title: Was ist die Hyperscale-Dienstebene?
description: In diesem Artikel wird die Hyperscale-Dienstebene im vCore-basierten Kaufmodell in Azure SQL-Datenbank beschrieben. Zudem werden die Unterschiede zu den Dienstebenen „Universell“ und „Unternehmenskritisch“ erläutert.
services: sql-database
ms.service: sql-database
ms.subservice: service-overview
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: mathoma
ms.date: 9/9/2021
ms.openlocfilehash: 12f445c1b532ff3989010ca2a4b747ca0c241aef
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132546486"
---
# <a name="hyperscale-service-tier"></a>Hyperscale-Dienstebene
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL-Datenbank basiert auf der an die Cloudumgebung angepasste Architektur der SQL Server-Datenbank-Engine, um die Verfügbarkeit von 99,99 % selbst bei Infrastrukturausfällen sicherzustellen. In Azure SQL-Datenbank werden drei Architekturmodelle verwendet:

- Universell/Standard
- Hyperscale
- Unternehmenskritisch/Premium

Die Dienstebene „Hyperscale“ in Azure SQL-Datenbank ist die neueste Dienstebene im vCore-basierten Kaufmodell. Diese Dienstebene bietet eine hochgradig skalierbare Speicher- und Computeleistung, mit der die Speicher- und Computeressourcen für eine Azure SQL-Datenbank-Instanz mithilfe der Azure-Architektur weit über die Limits der Dienstebenen „Universell“ und „Unternehmenskritisch“ hinaus aufskaliert werden können.

> [!NOTE]
>
> - Ausführliche Informationen zu den Dienstebenen „Universell“ und „Unternehmenskritisch“ im vCore-basierten Kaufmodell finden Sie in den Dienstebenen [Universell](service-tier-general-purpose.md) und [Unternehmenskritisch](service-tier-business-critical.md). Einen Vergleich zwischen vCore-basiertem Kaufmodell und DTU-basiertem Kaufmodell finden Sie unter [Kaufmodelle für Azure SQL-Datenbank und Ressourcen](purchasing-models.md).
> - Die Hyperscale-Dienstebene ist derzeit nur für Azure SQL-Datenbank und nicht für Azure SQL Managed Instance verfügbar.


> [!div class="nextstepaction"]
> [Umfrage zur Verbesserung von Azure SQL](https://aka.ms/AzureSQLSurveyNov2021)

## <a name="what-are-the-hyperscale-capabilities"></a>Welche Funktionen bietet Hyperscale?

Die Dienstebene „Hyperscale“ in Azure SQL-Datenbank bietet folgende zusätzliche Funktionen:

- Unterstützung für eine Datenbankgröße von bis zu 100 TB
- Nahezu sofortige Datenbanksicherungen (basierend auf in Azure Blob Storage gespeicherten Dateimomentaufnahmen) unabhängig von der Größe und ohne E/A-Auswirkung auf Computeressourcen  
- Schnelle Datenbankwiederherstellungen (basierend auf Dateimomentaufnahmen) in Minuten statt Stunden oder Tagen (kein von der Datengröße abhängiger Vorgang)
- Höhere Gesamtleistung aufgrund eines höheren Transaktionsprotokolldurchsatzes und schnellerer Transaktionscommits unabhängig von Datenmengen
- Schnelle Aufskalierung: Sie können ein oder mehrere [schreibgeschützte Replikate](service-tier-hyperscale-replicas.md) zum Abladen Ihrer Leseworkload und zur Verwendung als unmittelbar betriebsbereite Standbyserver bereitstellen.
- Schnelle Hochskalierung: Sie können Ihre Computeressourcen in konstanter Zeit hochskalieren, um hohe Workloads nach Bedarf zu bewältigen, und anschließend wieder herunterskalieren, sobald sie nicht mehr benötigt werden.

Die Dienstebene „Hyperscale“ beseitigt viele praktische Einschränkungen, die normalerweise für Clouddatenbanken gelten. Während die meisten anderen Datenbanken durch die auf einem einzelnen Knoten verfügbaren Ressourcen eingeschränkt werden, gelten in der Dienstebene „Hyperscale“ keine solchen Limits. Aufgrund der flexiblen Speicherarchitektur wächst der Speicher nach Bedarf. Hyperscale-Datenbanken werden ohne Definition einer maximalen Größe erstellt. Eine Hyperscale-Datenbank wächst nach Bedarf – und Ihnen wird nur die tatsächlich verwendete Kapazität in Rechnung gestellt. Für leseintensive Workloads bietet die Dienstebene „Hyperscale“ eine schnelle horizontale Skalierung, indem nach Bedarf zusätzliche Replikate zur Abladung von Leseworkloads bereitgestellt werden.

Darüber hinaus ist die Zeit, die zum Erstellen von Datenbanksicherungen oder zum Hoch- oder Herunterskalieren erforderlich ist, nicht mehr an die Menge der Daten in der Datenbank gebunden. Hyperscale-Datenbanken können praktisch sofort gesichert werden. Außerdem können Sie eine Datenbank in Minutenschnelle in der Größenordnung von zig Terabyte hoch- oder herunterskalieren. Durch diese Funktion müssen Sie nicht befürchten, durch Ihre Auswahl bei der Anfangskonfiguration eingeschränkt zu werden.

Weitere Informationen zu den Computegrößen für die Dienstebene „Hyperscale“ finden Sie unter [Merkmale der Dienstebene](service-tiers-vcore.md#service-tiers).

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Wer sollte die Dienstebene „Hyperscale“ in Betracht ziehen?

Die Dienstebene „Hyperscale“ ist für die meisten geschäftlichen Workloads vorgesehen, da sie große Flexibilität und Hochleistung mit unabhängig skalierbaren Compute- und Speicherressourcen bietet. Mit der Fähigkeit, Speicher automatisch bis zu 100 TB zu skalieren, ist sie eine gute Wahl für Kunden, auf die Folgendes zutrifft:

- Sie verfügen lokal über große Datenbanken und möchten ihre Anwendungen modernisieren, indem Sie sie in die Cloud verschieben.
- Sie befinden sich bereits in der Cloud und sind durch die Beschränkungen der maximalen Datenbankgröße anderer Dienstebenen eingeschränkt (1 bis 4 TB).
- Sie verwenden kleinere Datenbanken, benötigen aber eine schnelle vertikale und horizontale Computeskalierung, Hochleistung, sofortige Sicherung und schnelle Datenbankwiederherstellung.

Die Hyperscale-Dienstebene unterstützt eine breite Palette von SQL Server-Workloads (von reinem OLTP bis zu reinen Analysen), ist aber in erster Linie für OLTP- und hybride Transaktions- und Analyseverarbeitungsworkloads (HTAP) optimiert.

> [!IMPORTANT]
> Pools für elastische Datenbanken bieten für die Dienstebene „Hyperscale“ keine Unterstützung.

## <a name="hyperscale-pricing-model"></a>Preismodell für „Hyperscale“

Die Dienstebene „Hyperscale“ ist nur im [V-Kern-Modell](service-tiers-vcore.md) verfügbar. In Anpassung an die neue Architektur ist das Preismodell etwas anders gestaltet als bei den Dienstebenen „Universell“ oder „Unternehmenskritisch“:

- **Compute**:

  Der Preis für Compute-Einheiten bei „Hyperscale“ ist pro Replikat. Der Preis für den [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-benefit/) wird automatisch auf Hochverfügbarkeits- und benannte Replikate angewandt. Standardmäßig werden ein primäres Replikat und ein sekundäres [Hochverfügbarkeitsreplikat](service-tier-hyperscale-replicas.md) pro Hyperscale-Datenbank erstellt.  Benutzer können die Gesamtanzahl von Hochverfügbarkeitsreplikaten abhängig von der gewünschten [SLA](https://azure.microsoft.com/support/legal/sla/azure-sql-database/) zwischen von 0 und 4 festlegen. 

- **Storage**:

  Beim Konfigurieren einer Hyperscale-Datenbank müssen Sie keine maximale Datengröße angeben. Im Hyperscale-Tarif werden Gebühren für den Speicher Ihrer Datenbank basierend auf der tatsächlichen Zuordnung berechnet. Speicher zwischen 40 GB und 100 TB wird in Schritten von 10 GB automatisch zugewiesen. Mehrere Datendateien können bei Bedarf gleichzeitig wachsen. Eine Hyperscale-Datenbank wird mit einer Startgröße von 10 GB erstellt und wächst dann alle 10 Minuten um 10 GB, bis die Größe von 40 GB erreicht ist.

Weitere Informationen zu den Hyperscale-Preisen finden Sie unter [Preise für Azure SQL-Datenbank](https://azure.microsoft.com/pricing/details/sql-database/single/).

## <a name="distributed-functions-architecture"></a>Architektur mit verteilten Funktionen

Im Gegensatz zu herkömmlichen Datenbank-Engines, in denen alle Datenverwaltungsfunktionen an einem Standort/in einem Prozess zentralisiert sind (selbst die so genannten verteilten Datenbanken in modernen Produktionsumgebung verfügen über mehrere Kopien einer monolithischen Daten-Engine), trennt eine Hyperscale-Datenbank die Abfrageverarbeitungs-Engine, in der die Semantik verschiedener Daten-Engines abweicht, von den Komponenten, die eine langfristige Speicherung und Dauerhaftigkeit für die Daten bereitstellen. Auf diese Weise kann die Speicherkapazität nahtlos nach Bedarf horizontal hochskaliert werden. (Das Anfangsziel ist 100 TB). Hochverfügbarkeits- und benannte Replikate nutzen dieselben Speicherkomponenten gemeinsam, daher ist keine Datenkopie erforderlich, um ein neues Replikat zu starten.

Das folgende Diagramm veranschaulicht die verschiedenen Typen von Knoten in einer Hyperscale-Datenbank:

![Architektur](./media/service-tier-hyperscale/hyperscale-architecture.png)

Eine Hyperscale-Datenbank enthält die folgenden verschiedenen Komponententypen:

### <a name="compute"></a>Compute

Die relationale Engine befindet sich auf dem Computeknoten. Hier erfolgt die Sprach-, Abfrage- und Transaktionsverarbeitung. Alle Benutzerinteraktionen mit einer Hyperscale-Datenbank erfolgen über diese Computeknoten. Computeknoten umfassen SSD-basierte Caches (mit der Bezeichnung „RBPEX – Resilient Buffer Pool Extension“ im obigen Diagramm), um die Anzahl von Netzwerkroundtrips zu minimieren, die zum Abrufen einer Datenseite erforderlich sind. Es gibt einen primären Computeknoten, in dem alle Lese-/Schreibworkloads und Transaktionen verarbeitet werden. Mindestens ein sekundärer Computeknoten fungiert als unmittelbar betriebsbereiter Standbyserver für Failoverzwecke sowie als schreibgeschützter Computeknoten für die Abladung von Leseworkloads (falls diese Funktion gewünscht ist).

Die Datenbank-Engine, die auf Hyperscale-Computeknoten ausgeführt wird, ist die gleiche wie bei anderen Azure SQL-Datenbank-Dienstebenen. Wenn Benutzer mit der Datenbank-Engine auf Hyperscale-Computeknoten interagieren, sind unterstützte Oberfläche und Engine-Verhalten identisch mit denen in anderen Dienstebenen mit Ausnahme von [bekannten Einschränkungen](#known-limitations).

### <a name="page-server"></a>Seitenserver

Seitenserver sind Systeme, die eine horizontale hochskalierte Speicher-Engine darstellen.  Jeder Seitenserver ist für eine Teilmenge der Seiten in der Datenbank zuständig.  Nominell steuert jeder Seitenserver bis zu 128 GB bzw. bis zu 1 TB Daten. Es werden keine Daten auf mehreren Seitenservern gemeinsam genutzt (abgesehen von Seitenserverreplikaten, die für Redundanz und Verfügbarkeit gespeichert werden). Die Aufgabe eines Seitenservers besteht darin, Datenbankseiten nach Bedarf für die Computeknoten bereitzustellen und die Seiten auf dem neuesten Stand zu halten, wenn die Daten in Transaktionen aktualisiert werden. Seitenserver werden durch die Wiedergabe von Transaktionsprotokoll-Datensätzen aus dem Protokolldienst auf dem aktuellen Stand gehalten. Außerdem verwalten Seitenserver abdeckende SSD-basierte Caches zum Verbessern der Leistung. Eine langfristige Speicherung von Datenseiten wird in Azure Storage beibehalten, um eine zusätzliche Zuverlässigkeit zu gewährleisten.

### <a name="log-service"></a>Protokolldienst

Der Protokolldienst akzeptiert Transaktionsprotokoll-Datensätze vom primären Computereplikat, speichert sie in einem permanenten Cache und leitet die Protokolldatensätze an die übrigen Computereplikate (zur Aktualisierung ihrer Caches) sowie an die entsprechenden Seitenserver weiter, damit die Daten dort aktualisiert werden können. Auf diese Weise werden alle Datenänderungen vom primären Computereplikat über den Protokolldienst an alle sekundären Computereplikate und Seitenserver weitergegeben. Schließlich werden die Transaktionsprotokoll-Datensätze per Push in den langfristigen Speicher in Azure Storage übertragen. Hierbei handelt es sich um ein praktisch unbegrenztes Speicherrepository. Durch diesen Mechanismus entfällt die Notwendigkeit einer häufigen Protokollkürzung. Der Protokolldienst weist außerdem lokale Arbeitsspeicher- und SSD-Caches auf, um den Zugriff auf Protokolldatensätze zu beschleunigen.

### <a name="azure-storage"></a>Azure-Speicher

Azure Storage enthält alle Datendateien in einer Datenbank. Seitenserver halten Datendateien in Azure Storage auf dem neuesten Stand. Dieser Speicher wird zu Sicherungszwecken sowie für die Replikation zwischen Azure-Regionen verwendet. Sicherungen werden mithilfe von Speichermomentaufnahmen von Datendateien implementiert. Wiederherstellungsvorgänge mithilfe von Momentaufnahmen sind unabhängig von der Datengröße schnell. Die Daten können bis zu einem beliebigen Zeitpunkt innerhalb des Aufbewahrungszeitraums der Datenbank wiederhergestellt werden.

## <a name="backup-and-restore"></a>Sichern und Wiederherstellen

Sicherungen basieren auf Dateimomentaufnahmen und erfolgen daher nahezu unmittelbar. Durch die Trennung von Speicher- und Computeressourcen kann der Sicherungs-/Wiederherstellungsvorgang auf die Speicherebene verlegt werden, um die Verarbeitungslast auf das primäre Computereplikat zu reduzieren. Folglich wirkt sich die Datenbanksicherung nicht auf die Leistung des primären Computeknotens aus. Auf ähnliche Weise wird die Zeitpunktwiederherstellung (Point In Time Recovery, PITR) durch Wiederherstellen von Dateimomentaufnahmen durchgeführt. Dies ist kein von der Datengröße abhängiger Vorgang. Die Wiederherstellung einer Hyperscale-Datenbank in derselben Azure-Region ist ein konstanter Vorgang, und sogar Datenbanken mit einer Größe von mehreren TB können innerhalb von Minuten statt Stunden oder Tagen wiederhergestellt werden. Die Erstellung neuer Datenbanken durch Wiederherstellen einer vorhandenen Sicherung nutzt diese Funktion ebenfalls: Das Erstellen von Datenbankkopien für Entwicklungs- oder Testzwecke (auch von Datenbanken mit mehreren Terabyte) ist innerhalb weniger Minuten möglich.

Informationen zur Geowiederherstellung von Hyperscale-Datenbanken finden Sie unter [Wiederherstellen einer Hyperscale-Datenbank in einer anderen Region](#restoring-a-hyperscale-database-to-a-different-region).

## <a name="scale-and-performance-advantages"></a>Skalierungs- und Leistungsvorteile

Mit der Möglichkeit, weitere Computeknoten schnell hoch- bzw. herunterzufahren, erlaubt die Hyperscale-Architektur eine erhebliche horizontale Leseskalierung und kann außerdem den primären Computeknoten freigeben, um weitere Schreibanforderungen zu verarbeiten. Darüber hinaus können die Computeknoten aufgrund der Hyperscale-Architektur mit freigegebenem Speicher schnell zentral hoch- oder herunterskaliert werden.

## <a name="create-a-hyperscale-database"></a>Erstellen einer Hyperscale-Datenbank

Eine Hyperscale-Datenbank kann über das [Azure-Portal](https://portal.azure.com), mit [T-SQL](/sql/t-sql/statements/create-database-transact-sql), [PowerShell](/powershell/module/azurerm.sql/new-azurermsqldatabase) oder der [CLI](/cli/azure/sql/db#az_sql_db_create) erstellt werden. Hyperscale-Datenbanken stehen nur bei Verwendung des [vCore-basierten Kaufmodells](service-tiers-vcore.md) zur Verfügung.

Mit dem folgenden T-SQL-Befehl wird eine Hyperscale-Datenbank erstellt. Sie müssen sowohl die Edition als auch das Dienstziel in der `CREATE DATABASE`-Anweisung angeben. Eine Liste mit gültigen Dienstzielen finden Sie in den [Ressourceneinschränkungen](./resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen4).

```sql
-- Create a Hyperscale Database
CREATE DATABASE [HyperscaleDB1] (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

Dadurch wird eine Hyperscale-Datenbank auf Gen5-Hardware mit vier Kernen erstellt.

## <a name="upgrade-existing-database-to-hyperscale"></a>Upgraden einer vorhandenen Datenbank auf Hyperscale

Sie können Ihre vorhandenen Azure SQL-Datenbank-Instanzen über das [Azure-Portal](https://portal.azure.com) oder mit [T-SQL](/sql/t-sql/statements/alter-database-transact-sql), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase) oder der [CLI](/cli/azure/sql/db#az_sql_db_update) auf Hyperscale umstellen. Dies erfolgt zurzeit im Rahmen einer unidirektionalen Migration. Sie können Datenbanken nicht aus „Hyperscale“ in eine andere Dienstebene verschieben. Dies kann nur durch Exportieren und Importieren von Daten erfolgen. Es wird empfohlen, eine Kopie Ihrer Produktionsdatenbanken zu erstellen und für Proof of Concepts (POCs) eine Migration der Kopie zu Hyperscale durchzuführen. Das Migrieren einer vorhandenen Azure SQL-Datenbank-Instanz zur Hyperscale-Ebene ist ein von der Größe der Daten abhängiger Vorgang.

Mit dem folgenden T-SQL-Befehl wird eine Datenbank in die Dienstebene „Hyperscale“ verschoben. Sie müssen sowohl die Edition als auch das Dienstziel in der `ALTER DATABASE`-Anweisung angeben.

```sql
-- Alter a database to make it a Hyperscale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

> [!NOTE]
> Um eine Datenbank, die Teil einer [Georeplikationsbeziehung](active-geo-replication-overview.md) ist, entweder als primäre oder als sekundäre Datenbank nach Hyperscale zu verschieben, müssen Sie die Replikation beenden. Datenbanken in einer [Failovergruppe](auto-failover-group-overview.md) müssen zuerst aus der Gruppe entfernt werden.
>
> Nachdem eine Datenbank nach Hyperscale verschoben wurde, können Sie ein neues Hyperscale-Georeplikat für diese Datenbank erstellen. Die Georeplikation für Hyperscale befindet sich in der Vorschauversion mit bestimmten [Einschränkungen](active-geo-replication-overview.md).

## <a name="database-high-availability-in-hyperscale"></a>Hochverfügbarkeit von Datenbanken in Hyperscale

Wie bei allen anderen Dienstebenen gewährleistet Hyperscale die Dauerhaftigkeit von Daten für committete Transaktionen unabhängig von der Verfügbarkeit des Computereplikats. Das Ausmaß der Ausfallzeiten aufgrund der Nichtverfügbarkeit des primären Replikats hängt von der Art des Failovers (geplant oder ungeplant) und vom Vorhandensein mindestens eines Hochverfügbarkeitsreplikats ab. Bei einem geplanten Failover (d. h. einem Wartungsereignis) erstellt das System entweder das neue primäre Replikat, bevor ein Failover initiiert wird, oder es verwendet ein vorhandenes Hochverfügbarkeitsreplikat als Failoverziel. Bei einem ungeplanten Failover (d. h. bei einem Hardwarefehler auf dem primären Replikat) verwendet das System ein Hochverfügbarkeitsreplikat als Failoverziel (sofern vorhanden), oder es erstellt ein neues primäres Replikat aus dem Pool der verfügbaren Computekapazität. Im letzteren Fall ist die Dauer der Ausfallzeit länger, da zusätzliche Schritte erforderlich sind, um das neue primäre Replikat zu erstellen.

Weitere Informationen zur Hyperscale-SLA finden Sie unter [SLA für Azure SQL-Datenbank](https://azure.microsoft.com/support/legal/sla/azure-sql-database).

## <a name="disaster-recovery-for-hyperscale-databases"></a>Notfallwiederherstellung für Hyperscale-Datenbanken

### <a name="restoring-a-hyperscale-database-to-a-different-region"></a>Wiederherstellen einer Hyperscale-Datenbank in einer anderen Region

Wenn Sie im Rahmen der Wiederherstellung einer Hyperscale-Datenbank in Azure SQL-Datenbank im Notfall oder im Rahmen einer Übung, wegen eines Umzugs oder aus einem sonstigen Grund in einer anderen Region wiederherstellen müssen als der, in der sie gerade gehostet wird, besteht die primäre Methode in einer Geowiederherstellung der Datenbank. Die Schritte sind die gleichen wie beim Wiederherstellen jeder anderen Datenbank in SQL-Datenbank in einer anderen Region:

1. Erstellen Sie einen [Server](logical-servers.md) in der Zielregion, wenn Sie dort noch keinen geeigneten Server haben.  Dieser Server muss zu demselben Abonnement wie der ursprüngliche Server (Quelle) gehören.
2. Befolgen Sie die Anweisungen im Thema [Geowiederherstellung](./recovery-using-backups.md#geo-restore) der Seite zum Wiederherstellen einer Datenbank in Azure SQL-Datenbank aus automatischen Sicherungen.

> [!NOTE]
> Weil Quelle und Ziel sich in unterschiedlichen Regionen befinden, kann die Datenbank nicht wie in Nicht-Geowiederherstellungen Momentaufnahmenspeicher mit der Quelldatenbank gemeinsam nutzen, was unabhängig von der Datenbankgröße zu einem schnellen Abschluss führt. Bei einer Geowiederherstellung einer Hyperscale-Datenbank werden auch dann Anpassungen des Datenumfangs durchgeführt, wenn das Ziel sich in der gekoppelten Region des georeplizierten Speichers befindet. Darum ist die für eine Geowiederherstellung erforderliche Zeit proportional zur Größe der wiederhergestellten Datenbank. Wenn das Ziel in der gekoppelten Region liegt, findet die Datenübertragung innerhalb einer Region statt, was wesentlich schneller ist als eine regionsübergreifende Datenübertragung, es handelt sich aber immer noch um einen zeitintensiven Vorgang.

## <a name="available-regions"></a><a name=regions></a>Verfügbare Regionen

Die Dienstebene „Hyperscale“ von Azure SQL-Datenbank ist in den meisten Azure-Regionen aktiviert. Wenn Sie eine Hyperscale-Datenbank in einer Region erstellen möchten, wo Hyperscale nicht standardmäßig unterstützt wird, können Sie eine Onboardinganforderung über das Azure-Portal senden. Anweisungen finden Sie unter [Anfordern von Kontingenterhöhungen für Azure SQL-Datenbank](quota-increase-request.md). Beachten Sie beim Übermitteln Ihrer Anforderung die folgenden Richtlinien:

- Verwenden Sie den SQL-Datenbank-Kontingenttyp [Regionszugriff](quota-increase-request.md#region).
- Fügen Sie in der Beschreibung die Compute-SKU/Gesamtanzahl der Kerne einschließlich Hochverfügbarkeits- und benannter Replikate hinzu, und geben Sie an, dass Sie Hyperscale-Kapazität anfordern.
- Geben Sie außerdem eine Prognose der Gesamtgröße aller Datenbanken im Zeitverlauf in TB an.


## <a name="known-limitations"></a>Bekannte Einschränkungen

Hierbei handelt es sich um die aktuellen Einschränkungen der Hyperscale-Dienstebene bezüglich der GA.  Wir arbeiten daran, möglichst viele dieser Einschränkungen zu beseitigen.

| Problem | BESCHREIBUNG |
| :---- | :--------- |
| Im Bereich „Sicherungen verwalten“ für einen Server werden keine Hyperscale-Datenbanken angezeigt. Diese sind aus der Ansicht herausgefiltert.  | Hyperscale verfügt über eine separate Methode zum Verwalten von Sicherungen, sodass die Einstellungen für Langzeitaufbewahrung und Aufbewahrung von Point-in-Time-Sicherungen nicht gelten. Daher werden Hyperscale-Datenbanken nicht im Bereich „Sicherungen verwalten“ angezeigt.<br><br>Bei Datenbanken, die von anderen Dienstebenen von Azure SQL-Datenbank zu Hyperscale migriert wurden, werden Sicherungen vor der Migration so lange aufbewahrt, wie in der [Beibehaltungsdauer für Sicherungen](automated-backups-overview.md#backup-retention) der Quelldatenbank angegeben. Mit diesen Sicherungen kann die Quelldatenbank auf einen Zeitpunkt vor der Migration [wiederhergestellt](recovery-using-backups.md#programmatic-recovery-using-automated-backups) werden.|
| Wiederherstellung bis zu einem bestimmten Zeitpunkt | Datenbanken mit einer anderen Dienstebene als Hyperscale können nicht als Hyperscale-Datenbanken wiederhergestellt werden, und eine Hyperscale-Datenbank kann nicht als eine Datenbank mit einer anderen Dienstebene wiederhergestellt werden. Bei Datenbanken mit einer anderen Dienstebene als Hyperscale, die durch Ändern der Dienstebene zu Hyperscale migriert wurden, wird die Wiederherstellung bis zu einem bestimmten Zeitpunkt vor der Migration und innerhalb des Aufbewahrungszeitraums für die Sicherung der Datenbank [programmgesteuert](recovery-using-backups.md#programmatic-recovery-using-automated-backups) unterstützt. Die wiederhergestellte Datenbank gehört dann aber nicht der Dienstebene „Hyperscale“ an. |
| Beim Ändern der Dienstebene für Azure SQL-Datenbank in Hyperscale tritt ein Fehler auf, wenn die Datenbank Datendateien enthält, die größer als 1 TB sind. | In einigen Fällen kann es möglich sein, dieses Problem zu umgehen, indem die großen Dateien vor dem Versuch, die Dienstebene in Hyperscale zu ändern, auf weniger als 1 TB [verkleinert](file-space-manage.md#shrinking-data-files) werden. Verwenden Sie die folgende Abfrage, um die aktuelle Größe von Datenbankdateien zu ermitteln. `SELECT file_id, name AS file_name, size * 8. / 1024 / 1024 AS file_size_GB FROM sys.database_files WHERE type_desc = 'ROWS'`;|
| Verwaltete SQL-Instanz | Azure SQL Managed Instance wird für Hyperscale-Datenbanken derzeit nicht unterstützt. |
| Pools für elastische Datenbanken |  Pools für elastische Datenbanken werden mit Hyperscale derzeit nicht unterstützt.|
| Migration zu „Hyperscale“ ist derzeit ein unidirektionaler Vorgang | Nach der Migration einer Datenbank zu Hyperscale kann sie nicht direkt zu einer anderen Dienstebene migriert werden. Derzeit besteht die einzige Möglichkeit zum Migrieren einer Datenbank aus Hyperscale zu Nicht-Hyperscale darin, sie mithilfe einer BACPAC-Datei oder anderer Datenverschiebungstechnologien (Massenkopieren, Azure Data Factory, Azure Databricks, SSIS usw.) zu exportieren/importieren. Der BACPAC-Export/-Import über das Azure-Portal, über PowerShell mithilfe von [New-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) oder [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport), über die Azure-Befehlszeilenschnittstelle mithilfe von [az sql db export](/cli/azure/sql/db#az_sql_db_export) oder [az sql db import](/cli/azure/sql/db#az_sql_db_import) sowie über die [REST-API](/rest/api/sql/) wird nicht unterstützt. Der BACPAC-Import/-Export für kleinere Hyperscale-Datenbanken (bis zu 200 GB) wird über SSMS und [SqlPackage](/sql/tools/sqlpackage) Version 18.4 und höher unterstützt. Bei größeren Datenbanken kann der BACPAC-Export/-Import sehr lange dauern und aus verschiedenen Gründen zu Fehlern führen.|
| Migration von Datenbanken mit In-Memory-OLTP-Objekten | Hyperscale unterstützt eine Teilmenge von In-Memory-OLTP-Objekten, einschließlich speicheroptimierter Tabellentypen, Tabellenvariablen und systemintern kompilierter Module. Wenn aber In-Memory-OLTP-Objekte von beliebiger Art in der gerade migrierten Datenbank vorhanden sind, wird die Migration von Premium- und unternehmenskritischen Dienstebenen zu Hyperscale nicht unterstützt. Für die Migration solch einer Datenbank zu Hyperscale müssen alle In-Memory-OLTP-Objekte und deren Abhängigkeiten gelöscht werden. Nachdem die Datenbank migriert wurde, können diese Objekte neu erstellt werden. Speicheroptimierte dauerhafte und nicht dauerhafte Tabellen werden zurzeit in Hyperscale nicht unterstützt und müssen in Datenträgertabellen geändert werden.|
| Georeplikation  | Die [Geo-Replikation](active-geo-replication-overview.md) auf Hyperscale ist jetzt in der öffentlichen Vorschau. |
| Intelligente Datenbankfeatures | Mit Ausnahme der Option „Plan erzwingen“ werden alle anderen Optionen zur automatischen Optimierung für Hyperscale noch nicht unterstützt. Optionen scheinen möglicherweise aktiviert zu sein, es erfolgen jedoch keine Empfehlungen oder Aktionen. |
| Statistik zur Abfrageleistung | Die Statistik zur Abfrageleistung wird derzeit nicht für Hyperscale-Datenbanken unterstützt. |
| Verkleinern der Datenbank | DBCC SHRINKDATABASE und DBCC SHRINKFILE werden derzeit für Hyperscale-Datenbanken nicht unterstützt. |
| Datenbankintegritätsprüfung | DBCC CHECKDB wird für Hyperscale-Datenbanken derzeit nicht unterstützt. DBCC CHECKFILEGROUP und DBCC CHECKTABLE können als Problemumgehung verwendet werden. Ausführliche Informationen zur Datenintegritätsverwaltung in Azure SQL-Datenbank finden Sie unter [Datenintegrität in Azure SQL-Datenbank](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/). |
| Elastische Aufträge | Die Verwendung einer Hyperscale-Datenbank als Auftragsdatenbank wird nicht unterstützt. Elastische Aufträge können jedoch Hyperscale-Datenbanken auf die gleiche Weise wie jede andere Azure SQL-Datenbank-Instanz als Ziel verwenden. |

## <a name="next-steps"></a>Nächste Schritte

- Häufig gestellte Fragen zu diesem Thema finden Sie unter [Häufig gestellte Fragen zu Hyperscale](service-tier-hyperscale-frequently-asked-questions-faq.yml).
- Informationen zu Dienstebenen finden Sie unter [Dienstebenen](purchasing-models.md).
- Informationen zu Grenzwerten auf Server- und Abonnementebene finden Sie unter [Übersicht über Ressourcenlimits auf einem Server](resource-limits-logical-server.md).
- Informationen zu Einschränkungen des Kaufmodells für eine Einzeldatenbank finden Sie unter [Limits des vCore-basierten Kaufmodells für eine Einzeldatenbank in Azure SQL-Datenbank](resource-limits-vcore-single-databases.md).
- Eine Liste der Features und einen Funktionsvergleich finden Sie unter [Allgemeine SQL-Features](features-comparison.md).
