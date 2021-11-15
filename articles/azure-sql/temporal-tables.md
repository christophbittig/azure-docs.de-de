---
title: Erste Schritte mit temporalen Tabellen
description: Hier finden Sie Informationen zu den ersten Schritten mit temporalen Tabellen in Azure SQL-Datenbank und Azure SQL Managed Instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: MladjoA
ms.author: mlandzic
ms.reviewer: mathoma
ms.date: 10/18/2021
ms.openlocfilehash: 945afcb2a4158ee4c80bd9d36a9697d6692d0d19
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130250424"
---
# <a name="getting-started-with-temporal-tables-in-azure-sql-database-and-azure-sql-managed-instance"></a>Erste Schritte mit temporalen Tabellen in Azure SQL-Datenbank und Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Temporale Tabellen sind eine neue Programmierfunktion von Azure SQL-Datenbank und Azure SQL Managed Instance, mit der Sie den vollständigen Verlauf von Änderungen in Ihren Daten ohne benutzerdefinierte Codierung nachverfolgen und analysieren können. Temporale Tabellen enthalten Daten, die eng mit dem zeitlichen Kontext verbunden sind, sodass gespeicherte Fakten nur im angegebenen Zeitraum als gültig interpretiert werden können. Diese Eigenschaft von temporalen Tabellen ermöglicht eine effiziente zeitbasierte Analyse und Einblicke in die Datenentwicklung.

## <a name="temporal-scenario"></a>Szenario für temporale Tabellen

In diesem Artikel werden die Schritte zum Verwenden temporaler Tabellen in einem Anwendungsszenario beschrieben. Stellen Sie sich vor, Sie möchten die Benutzeraktivität auf einer neuen Website nachverfolgen, die von Grund auf neu entwickelt wird, oder auf einer vorhandenen Website, die Sie mit der Benutzeraktivitätsanalyse erweitern möchten. In diesem vereinfachten Beispiel gehen wir davon aus, dass die Anzahl der besuchten Webseiten während eines Zeitraums ein Indikator ist, der in der Websitedatenbank erfasst und überwacht werden muss, die in Azure SQL-Datenbank oder Azure SQL Managed Instance gehostet wird. Die Verlaufsanalyse der Benutzeraktivität soll Anregungen zum Neuentwurf der Website und bessere Erkenntnisse über die Besucher liefern.

Das Datenbankmodell für dieses Szenario ist sehr einfach – die Metrik der Benutzeraktivität wird nur mit dem Ganzzahlfeld **PageVisited** dargestellt und zusammen mit grundlegenden Informationen im Benutzerprofil erfasst. Außerdem verwalten Sie für die zeitbasierte Analyse eine Reihe von Zeilen für jeden Benutzer, wobei jede Zeile die Anzahl von Seiten darstellt, die ein bestimmter Benutzer innerhalb einer bestimmten Zeitspanne besucht.

![Schema](./media/temporal-tables/AzureTemporal1.png)

Glücklicherweise erfordert die Verwaltung dieser Aktivitätsinformationen von Ihnen keinerlei Aufwand zur Bearbeitung Ihrer App. Bei temporalen Tabellen ist dieser Prozess automatisiert, sodass Sie beim Entwerfen der Website volle Flexibilität genießen und mehr Zeit haben, um sich auf die eigentliche Datenanalyse zu konzentrieren. Sie müssen einzig und allein sicherstellen, dass die Tabelle `WebSiteInfo` als [temporal mit Systemversionsverwaltung](/sql/relational-databases/tables/temporal-tables#what-is-a-system-versioned-temporal-table) konfiguriert ist. Nachfolgend werden die genauen Schritte zum Verwenden temporaler Tabellen in diesem Szenario beschrieben.

## <a name="step-1-configure-tables-as-temporal"></a>Schritt 1: Konfigurieren von Tabellen als temporal

Je nachdem, ob Sie eine neue Entwicklung beginnen oder eine vorhandene Anwendung aktualisieren, werden Sie entweder temporale Tabellen erstellen oder vorhandene ändern, indem Sie temporale Attribute hinzufügen. Im Allgemeinen kann Ihr Szenario eine Kombination dieser beiden Optionen sein. Führen Sie diese Aktion mit [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS), [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) (SSDT), [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) oder einem anderen Transact-SQL-Entwicklungstool aus.

> [!IMPORTANT]
> Es wird empfohlen, immer die aktuelle Version von Management Studio zu verwenden, um die Synchronisierung mit Updates von Azure SQL-Datenbank und Azure SQL Managed Instance sicherzustellen. [Aktualisieren Sie SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

### <a name="create-new-table"></a>Erstellen einer neuen Tabelle

Verwenden Sie das Kontextmenüelement „Neue Tabelle mit Systemversionsverwaltung“ im SSMS-Objekt-Explorer, um den Abfrage-Editor mit einem Vorlagenskript für eine temporale Tabelle zu öffnen, und füllen Sie die Vorlage dann mit „Werte für Vorlagenparameter angeben“ (STRG+UMSCHALT+M) auf:

![SSMSNewTable](./media/temporal-tables/AzureTemporal2.png)

Wählen Sie in SSDT beim Hinzufügen von neuen Elementen zum Datenbankprojekt die Vorlage „Temporale Tabelle (mit Systemversionsverwaltung)“ aus. Damit öffnen Sie den Tabellen-Designer, sodass Sie mühelos das Tabellenlayout festlegen können:

![SSDTNewTable](./media/temporal-tables/AzureTemporal3.png)

Sie können eine temporale Tabelle auch durch Angeben von Transact-SQL-Anweisungen direkt erstellen, wie im folgenden Beispiel gezeigt. Beachten Sie, dass die obligatorischen Elemente aller temporalen Tabellen die Definition von PERIOD und die SYSTEM_VERSIONING-Klausel mit einem Verweis auf eine andere Benutzertabelle sind, die Verlaufszeilenversionen speichert:

```sql
CREATE TABLE WebsiteUserInfo
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
```

Wenn Sie eine temporale Tabelle mit Systemversionsverwaltung erstellen, wird die zugehörige Verlaufstabelle mit der Standardkonfiguration automatisch erstellt. Die Standardverlaufstabelle enthält einen gruppierten B-Strukturindex für die Periodenspalten (Anfang, Ende) mit aktivierter Seitenkomprimierung. Diese Konfiguration ist optimal für die Mehrzahl der Szenarien, in denen temporale Tabellen insbesondere für die [Datenüberwachung](/sql/relational-databases/tables/temporal-table-usage-scenarios#enabling-system-versioning-on-a-new-table-for-data-audit)verwendet werden.

In diesem Fall soll über einen längeren Datenverlauf hinweg und mit größeren Datasets eine zeitbasierte Trendanalyse ausgeführt werden, darum wird als Speicher für die Verlaufstabelle ein gruppierter Columnstore-Index gewählt. Ein gruppierter Columnstore bietet sehr gute Komprimierung und Leistung für analytische Abfragen. Temporale Tabellen bieten Ihnen die Flexibilität, Indizes für die aktuellen und temporalen Tabellen vollständig unabhängig zu konfigurieren.

> [!NOTE]
> Columnstore-Indizes sind in den Dienstebenen „Unternehmenskritisch“, „Universell“, „Premium“ und „Standard“, in S3 und höher verfügbar.

Das folgende Skript zeigt, wie der Standardindex für die Verlaufstabelle in den gruppierten Columnstore geändert werden kann:

```sql
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
```

Temporale Tabellen werden im Objekt-Explorer zur leichteren Erkennung mit einem bestimmten Symbol dargestellt, während die zugehörige Verlaufstabelle als untergeordneter Knoten angezeigt wird.

![AlterTable](./media/temporal-tables/AzureTemporal4.png)

### <a name="alter-existing-table-to-temporal"></a>Ändern einer vorhandenen Tabelle in temporal

Wir behandeln nun das alternative Szenario, in dem die WebsiteUserInfo-Tabelle bereits vorhanden ist, jedoch nicht zum Speichern eines Änderungsverlaufs konzipiert wurde. In diesem Fall können Sie die vorhandene Tabelle, wie im folgenden Beispiel gezeigt, einfach zu einer temporalen erweitern:

```sql
ALTER TABLE WebsiteUserInfo
ADD
    ValidFrom datetime2 (0) GENERATED ALWAYS AS ROW START HIDDEN  
        constraint DF_ValidFrom DEFAULT DATEADD(SECOND, -1, SYSUTCDATETIME())
    , ValidTo datetime2 (0)  GENERATED ALWAYS AS ROW END HIDDEN
        constraint DF_ValidTo DEFAULT '9999.12.31 23:59:59.99'
    , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo);

ALTER TABLE WebsiteUserInfo  
SET (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
GO

CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
```

## <a name="step-2-run-your-workload-regularly"></a>Schritt 2: Reguläres Ausführen Ihrer Workload

Der Hauptvorteil von temporalen Tabellen besteht darin, dass Sie Ihre Website in keiner Weise ändern oder anpassen müssen, um Änderungen nachverfolgen zu können. Nach der Erstellung behalten temporale Tabellen alle vorherigen Zeilenversionen transparent bei, wenn Sie Änderungen an Ihren Daten vornehmen.

Um die automatische Änderungsnachverfolgung für dieses spezielle Szenario nutzen zu können, richten wir es ein, dass die Spalte **PagesVisited** jedes Mal aktualisiert wird, wenn ein Benutzer seine Sitzung auf der Website beendet:

```sql
UPDATE WebsiteUserInfo  SET [PagesVisited] = 5
WHERE [UserID] = 1;
```

Es ist wichtig zu beachten, dass die Aktualisierungsabfrage weder den genauen Zeitpunkt kennen muss, zu dem der eigentliche Vorgang auftrat, noch wie Verlaufsdaten für die zukünftige Analyse beibehalten werden. Beide Aspekte werden automatisch von Azure SQL-Datenbank und Azure SQL Managed Instance behandelt. Das folgende Diagramm veranschaulicht, wie Verlaufsdaten bei jedem Update generiert werden.

![TemporalArchitecture](./media/temporal-tables/AzureTemporal5.png)

## <a name="step-3-perform-historical-data-analysis"></a>Schritt 3: Ausführen der Verlaufsdatenanalyse

Da nun die temporale Systemversionsverwaltung aktiviert ist, ist die Analyse der Verlaufsdaten nur eine Abfrage von Ihnen entfernt. In diesem Artikel zeigen wir einige Beispiele, die allgemeine Analyseszenarien behandeln. Um alle Details zu erfahren, können Sie sich mit den verschiedenen Optionen vertraut machen, die mit der [FOR SYSTEM_TIME](/sql/relational-databases/tables/temporal-tables#how-do-i-query-temporal-data)-Klausel eingeführt werden.

Um die Top 10-Benutzer geordnet nach der Anzahl der besuchten Webseiten mit Stand vor einer Stunde anzuzeigen, führen Sie diese Abfrage aus:

```sql
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
SELECT TOP 10 * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME AS OF @hourAgo
ORDER BY PagesVisited DESC
```

Sie können diese Abfrage problemlos ändern, um die Websitebesuche vor einem Tag, einem Monat oder zu einem beliebigen Zeitpunkt in der Vergangenheit zu analysieren.

Um eine grundlegende statistische Analyse für den vorherigen Tag auszuführen, verwenden Sie das folgende Beispiel:

```sql
DECLARE @twoDaysAgo datetime2 = DATEADD(DAY, -2, SYSUTCDATETIME());
DECLARE @aDayAgo datetime2 = DATEADD(DAY, -1, SYSUTCDATETIME());

SELECT UserID, SUM (PagesVisited) as TotalVisitedPages, AVG (PagesVisited) as AverageVisitedPages,
MAX (PagesVisited) AS MaxVisitedPages, MIN (PagesVisited) AS MinVisitedPages,
STDEV (PagesVisited) as StDevViistedPages
FROM dbo.WebsiteUserInfo
FOR SYSTEM_TIME BETWEEN @twoDaysAgo AND @aDayAgo
GROUP BY UserId
```

Um nach Aktivitäten eines bestimmten Benutzers in einer Zeitspanne zu suchen, verwenden Sie die CONTAINED IN-Klausel:

```sql
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
DECLARE @twoHoursAgo datetime2 = DATEADD(HOUR, -2, SYSUTCDATETIME());
SELECT * FROM dbo.WebsiteUserInfo
FOR SYSTEM_TIME CONTAINED IN (@twoHoursAgo, @hourAgo)
WHERE [UserID] = 1;
```

Grafische Visualisierung ist besonders geeignet für temporale Abfragen, da Sie Trends und Verwendungsmuster unkompliziert in intuitiver Weise anzeigen können:

![TemporalGraph](./media/temporal-tables/AzureTemporal6.png)

## <a name="evolving-table-schema"></a>Entwicklung des Tabellenschemas

In der Regel müssen Sie das Schema der temporalen Tabelle während der App-Entwicklung ändern. Führen Sie dazu einfach die regulären ALTER TABLE-Anweisungen aus. Dann gibt Azure SQL-Datenbank oder Azure SQL Managed Instance die Änderungen entsprechend an die Verlaufstabelle weiter. Das folgende Skript zeigt, wie Sie ein zusätzliches Attribut für die Nachverfolgung hinzufügen können:

```sql
/*Add new column for tracking source IP address*/
ALTER TABLE dbo.WebsiteUserInfo
ADD  [IPAddress] varchar(128) NOT NULL CONSTRAINT DF_Address DEFAULT 'N/A';
```

Auf ähnliche Weise können Sie die Spaltendefinition ändern, während Ihre Workload aktiv ist:

```sql
/*Increase the length of name column*/
ALTER TABLE dbo.WebsiteUserInfo
    ALTER COLUMN  UserName nvarchar(256) NOT NULL;
```

Schließlich können Sie eine Spalte entfernen, die Sie nicht mehr benötigen.

```sql
/*Drop unnecessary column */
ALTER TABLE dbo.WebsiteUserInfo
    DROP COLUMN TemporaryColumn;
```

Alternativ verwenden Sie die aktuellen [SSDT](/sql/ssdt/download-sql-server-data-tools-ssdt) , um das Schema der temporalen Tabelle zu ändern, während Sie mit der Datenbank verbunden sind (Onlinemodus), oder als Teil des Datenbankprojekts (Offlinemodus).

## <a name="controlling-retention-of-historical-data"></a>Steuern der Aufbewahrung von Verlaufsdaten

Mit temporalen Tabellen mit Systemversionsverwaltung kann die Verlaufstabelle die Datenbankgröße stärker steigern als reguläre Tabellen. Eine große und ständig wachsende Verlaufstabelle kann zu einem Problem werden, sowohl aufgrund der reinen Speicherkosten als auch durch Leistungsbeeinträchtigungen aufgrund von temporalen Abfragen. Daher ist die Entwicklung einer Aufbewahrungsrichtlinie für die Verwaltung von Daten in der Verlaufstabelle ein wichtiger Aspekt der Planung und Verwaltung des Lebenszyklus jeder temporalen Tabelle. Mit Azure SQL-Datenbank und Azure SQL Managed Instance stehen Ihnen die folgenden Methoden zum Verwalten von Verlaufsdaten in der temporalen Tabelle zur Verfügung:

- [Tabellenpartitionierung](/sql/relational-databases/tables/manage-retention-of-historical-data-in-system-versioned-temporal-tables#using-table-partitioning-approach)
- [Benutzerdefiniertes Bereinigungsskript](/sql/relational-databases/tables/manage-retention-of-historical-data-in-system-versioned-temporal-tables#using-custom-cleanup-script-approach)

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu temporalen Tabellen finden Sie unter [Temporale Tabellen](/sql/relational-databases/tables/temporal-tables).
