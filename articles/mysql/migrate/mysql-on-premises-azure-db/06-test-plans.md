---
title: 'Migrieren lokaler MySQL-Instanzen zu Azure Database for MySQL: Testpläne'
description: WWI hat einen Testplan erstellt, der eine Reihe von IT- und Geschäftsaufgaben umfasst. Erfolgreiche Migrationen erfordern, dass alle Tests ausgeführt werden.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: d13a6b63b297aff9504b00449f660f3ba55b6591
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/30/2021
ms.locfileid: "113084982"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-test-plans"></a>Migrieren lokaler MySQL-Instanzen zu Azure Database for MySQL: Testpläne

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="prerequisites"></a>Voraussetzungen

[Migrationsmethoden](05-migration-methods.md)

## <a name="overview"></a>Übersicht

WWI hat einen Testplan erstellt, der eine Reihe von IT- und Geschäftsaufgaben umfasst. Erfolgreiche Migrationen erfordern, dass alle Tests ausgeführt werden.

Tests:

  - Stellen Sie sicher, dass die migrierte Datenbank mit lokalen Tabellen konsistent ist (gleiche Datensatzanzahl und Abfrageergebnisse).

  - Stellen Sie sicher, dass die Leistung akzeptabel ist (sie sollte der Leistung bei lokaler Ausführung entsprechen).

  - Stellen Sie sicher, dass die Leistung von Zielabfragen die angegebenen Anforderungen erfüllt.

  - Stellen Sie eine akzeptable Netzwerkkonnektivität zwischen lokalem und Azure-Netzwerk sicher.

  - Stellen Sie sicher, dass alle identifizierten Anwendungen und Benutzer eine Verbindung zu der migrierten Dateninstanz herstellen können.

WWI hat ein Migrationswochenende und ein Zeitfenster ermittelt, das um 22:00 Uhr begann und um 2:00 Uhr endete (Pacific Time). Wenn die Migration nicht vor dem 2:00-Uhr-Ziel (4-Stunden-Downtimeziel) abgeschlossen wurde und alle Tests bestanden wurden, wurden die Rollbackverfahren gestartet. Probleme wurden für künftige Migrationsversuche dokumentiert. Alle Migrationsfenster wurden vorverlegt und basierend auf akzeptablen Geschäftszeitplänen neu geplant.

## <a name="sample-queries"></a>Beispielabfragen

Eine Reihe von Abfragen wurde für Quelle und Ziel ausgeführt, um zu überprüfen, ob die Datenbankmigration erfolgreich war. Mithilfe der folgenden Abfragen und Skripts können Sie ermitteln, ob alle erforderlichen Datenbankobjekte durch die Migrationsaktionen aus der Quelle in das Ziel verschoben wurden.

### <a name="table-rows"></a>Tabellenzeilen

Sie können diese Abfrage verwenden, um alle Tabellen sowie eine geschätzte Zeilenanzahl abzurufen:

```
SELECT SUM(TABLE_ROWS)
FROM INFORMATION_SCHEMA.TABLES
WHERE TABLE_SCHEMA = '{SchemaName}';
```

> [!NOTE]
> Die Tabelle `INFORMATION_SCHEMA` stellt eine Reihe von geschätzten Werten für die Tabellen bereit. Um eine genauere Ansicht von Metriken wie der Tabellengröße zu erhalten, erhöhen Sie die Seitenstichprobengröße mit dem Serverparameter `innodb_stats_transient_sample_pages`. Das Erhöhen dieses Serverwerts erzwingt die Analyse von mehr Seiten, wodurch genauere Ergebnisse geliefert werden.

Führen Sie die SQL-Anweisung `count(*)` für jede Tabelle aus, um eine genaue Anzahl von Zeilen zu erhalten. Das Ausführen dieses Befehls kann bei großen Tabellen viel Zeit in Anspruch nehmen. Das folgende Skript generiert eine Reihe von SQL-Anweisungen, die ausgeführt werden können, um die genaue Anzahl zu erhalten:

```
SELECT CONCAT( 
    'SELECT "', 
    table_name, 
    '" AS table_name, COUNT(*) AS exact_row_count FROM `', 
    table_schema, 
    '`.`', 
    table_name, 
    '` UNION ' 
)  
FROM INFORMATION_SCHEMA.TABLES 
WHERE table_schema = '**my_schema**';
```

### <a name="table-fragmentation"></a>Tabellenfragmentierung

Die Datentabellen werden mit fortgesetzter Anwendungsverwendung voraussichtlich größer. In einigen Fällen wachsen die Daten möglicherweise zwar nicht, ändern sich jedoch ständig durch Löschungen und Updates. In diesem Fall kann es zu zahlreichen Fragmentierungen in den Datenbankdateien kommen. Die MySQL-Anweisung OPTIMIZE TABLE kann den physischen Speicherplatzbedarf reduzieren und die E/A-Effizienz verbessern.

Sie können die [MySQL-Tabellen optimieren](https://dev.mysql.com/doc/refman/8.0/en/optimize-table.html), indem Sie Folgendes ausführen:

`optimize table TABLE_NAME`

### <a name="database-objects"></a>Datenbankobjekte

Verwenden Sie die folgende Abfrage, um sicherzustellen, dass alle anderen Datenbankobjekte berücksichtigt werden. Diese Abfragen können zwar die Anzahl der Tabellenzeilen berechnen, sie berücksichtigen aber möglicherweise nicht die Version des jeweiligen Datenbankobjekts. Es könnte also beispielsweise eine gespeicherte Prozedur vorhanden sein, die sich jedoch zwischen Beginn und Ende der Migration geändert hat. Es wird empfohlen, die Datenbankobjektentwicklung während der Migration einzufrieren.

**Benutzer**

```sql
SELECT DISTINCT 
        USER 
FROM 
        mysql.user 
WHERE 
        user <> '' order by user
```

**Indizes**

```sql
SELECT DISTINCT 
        INDEX_NAME 
FROM 
        information_schema.STATISTICS 
WHERE 
        TABLE_SCHEMA = '{SchemaName}'
```

**Einschränkungen**

```sql
SELECT DISTINCT 
        CONSTRAINT_NAME 
FROM 
        information_schema.TABLE_CONSTRAINTS 
WHERE 
        CONSTRAINT_SCHEMA = '{SchemaName}'
```

**Ansichten**

```sql
SELECT 
        TABLE_NAME 
FROM 
        information_schema.VIEWS 
WHERE 
        TABLE_SCHEMA = '{SchemaName}'
```

**Gespeicherte Prozeduren**

```sql
SELECT 
        ROUTINE_NAME 
FROM 
        information_schema.ROUTINES 
WHERE 
        ROUTINE_TYPE = 'FUNCTION' and 
        ROUTINE_SCHEMA = '{SchemaName}'
```

**Funktionen**

```sql
SELECT 
        ROUTINE_NAME 
FROM 
        information_schema.ROUTINES 
WHERE 
        ROUTINE_TYPE = 'PROCEDURE' and 
        ROUTINE_SCHEMA = '{SchemaName}'
```

**Ereignisse**

```sql
SELECT 
        EVENT_NAME 
FROM 
        INFORMATION_SCHEMA.EVENTS 
WHERE 
        EVENT_SCHEMA = '{SchemaName}'
```

## <a name="rollback-strategies"></a>Rollbackstrategien

Die obigen Abfragen stellen eine Liste von Objektnamen und Anzahlen bereit, die bei einer Rollbackentscheidung verwendet werden. Migrationsbenutzer können den ersten Schritt zur Objektüberprüfung unternehmen, indem sie die Anzahl von Quell- und Zielobjekten überprüfen. Eine Abweichung bei der Objektanzahl muss nicht unbedingt bedeuten, dass ein Rollback erforderlich ist. Eine ausführliche Auswertung kann darauf hinweisen, dass der Unterschied geringfügig und leicht wiederherstellbar ist. Die Lösung kann eine manuelle Migration einiger fehlerhafter Objekte sein. Wenn beispielsweise alle Tabellen und Datenzeilen migriert wurden und nur einige der Funktionen verpasst wurden, beheben Sie diese fehlerhaften Objekte, und schließen Sie die Migration ab. Wenn die Datenbank relativ klein ist, ist es möglich, die Azure Database for MySQL-Instanz zu löschen und die Migration neu zu starten. Große Datenbanken benötigen möglicherweise mehr Zeit als im Migrationsfenster verfügbar, um fehlende Objekte zu ermitteln. Die Migration muss beendet und es muss ein Rollback ausgeführt werden.

Die Ermittlung fehlender Datenbankobjekte muss schnell während eines Migrationsfensters erfolgen. Jede Minute zählt. Eine Möglichkeit kann sein, die Umgebungsobjektnamen in eine Datei zu exportieren und ein Datenvergleichstool zu verwenden, um fehlende Objekte schnell zu ermitteln. Eine weitere Möglichkeit kann darin bestehen, die Objektnamen der Quelldatenbank zu exportieren und die Daten in eine temporäre Tabelle der Zieldatenbankumgebung zu importieren. Vergleichen Sie die Daten mithilfe einer **skriptbasierten** und **getesteten** SQL-Anweisung. Geschwindigkeit und Genauigkeit der Datenüberprüfung sind für den Migrationsprozess von entscheidender Bedeutung. Verlassen Sie sich nicht darauf, während eines Migrationsfensters eine lange Liste von Datenbankobjekten manuell zu lesen und zu überprüfen. Die Möglichkeit von menschlichen Fehlern ist zu groß. Es ist am besten, wenn die Verwaltung per Ausnahme unter Verwendung von Tools erfolgt.

## <a name="wwi-scenario"></a>WWI-Szenario

Der WWI-CIO hat einen Bestätigungsbericht erhalten, dass alle Datenbankobjekte von der lokalen Datenbank zur Azure Database for MySQL-Instanz migriert wurden. Das Datenbankteam hat vor Beginn der Migration die oben genannten Abfragen für die Datenbank ausgeführt und alle Ergebnisse in einer Kalkulationstabelle gespeichert.

Die Genauigkeit des Zielmigrationsobjekts wurde anhand der Schemainformationen der Quelldatenbank überprüft.

## <a name="test-plans-checklist"></a>Prüfliste für Testpläne

  - Sorgen Sie dafür, dass Testabfragen skriptfähig, getestet und ausführungsbereit sind.

  - Erfahren Sie, wie lange die Ausführung von Testabfragen dauert, und machen Sie sie zu einem Teil der dokumentierten Migrationszeitachse.

  - Bereiten Sie eine Entschärfungs- und Rollbackstrategie für verschiedene potenzielle Ergebnisse vor.

  - Verfügen Sie über eine klar definierte Zeitachse von Ereignissen für die Migration.  


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Leistungsbaselines](./07-performance-baselines.md)