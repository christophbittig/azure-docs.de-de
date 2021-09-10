---
title: 'Migrieren lokaler MySQL-Instanzen zu Azure Database for MySQL: Datenmigration'
description: Vor dem Upgrade oder der Migration von Daten ist es sinnvoll, die Datenbank mithilfe von MySQL Workbench oder manuell über den Befehl „mysqldump“ zu exportieren.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: 9e53243424f4cb0b289c0ebba9c82cb9fb4853cc
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/30/2021
ms.locfileid: "113084935"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-data-migration"></a>Migrieren lokaler MySQL-Instanzen zu Azure Database for MySQL: Datenmigration

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="prerequisites"></a>Voraussetzungen

[Leistungsbaselines](07-performance-baselines.md)

## <a name="back-up-the-database"></a>Sichern der Datenbank

Vor dem Upgrade oder der Migration von Daten ist es sinnvoll, die Datenbank mithilfe von MySQL Workbench oder manuell über den Befehl `mysqldump` zu exportieren.

## <a name="offline-vs-online"></a>Offline im Vergleich zu Online

Vor der Auswahl eines Migrationstools sollte bestimmt werden, ob die Migration online oder offline erfolgen soll.

  - Bei **Offlinemigrationen** ist das System während der Migration nicht verfügbar. Bei dieser Option wird sichergestellt, dass keine Transaktionen ausgeführt werden und der Zustand der Daten genau dem entspricht, der bei der Wiederherstellung in Azure zu erwarten ist.

  - Bei **Onlinemigrationen** werden die Daten nahezu in Echtzeit migriert. Diese Option ist geeignet, wenn es für die Benutzer oder Anwendungen, die die Datenworkload nutzen, nur zu kurzen Ausfallzeiten kommen darf. Der Prozess umfasst die Replikation der Daten mit einer Replikationsmethode wie `binlog` o. ä.

Die Umgebung von WWI weist einige komplexe Netzwerk- und Sicherheitsanforderungen auf, die nicht zulassen, dass die entsprechenden Änderungen für eingehende und ausgehende Verbindungen im gewünschten Migrationszeitrahmen übernommen werden. Aufgrund dieser Komplexitäten und Anforderungen kommt eine Onlinemigration nicht in Betracht.

> [!NOTE]
> Weitere Informationen zur Offline- und Onlinemigration finden Sie in den Abschnitten zu Planung und Bewertung.

## <a name="data-drift"></a>Datendrift

In Offlinemigrationsstrategien kann es potenziell zu einer Datendrift kommen. Datendrift tritt auf, wenn neu geänderte Quelldaten nicht mehr mit migrierten Daten synchron sind. In diesem Fall ist ein vollständiger Export oder Deltaexport erforderlich. Sie können dieses Problem mildern, indem Sie den gesamten Datenverkehr an die Datenbank beenden und dann den Export durchführen. Wenn das Beenden des gesamten Datenänderungsverkehrs nicht möglich ist, müssen Sie die Drift berücksichtigen.

Die Ermittlung der Änderungen kann kompliziert werden, wenn die Datenbanktabellen keine Spalten wie numerische Primärschlüssel aufweisen oder in jeder Tabelle, die migriert werden muss, keinerlei Änderungs- und Erstellungsdatum vorhanden ist.

Wenn beispielsweise ein numerischer Primärschlüssel vorhanden ist und der Import der Migration in der Sortierreihenfolge erfolgt, ist es relativ einfach, zu ermitteln, wo der Import beendet wurde, und den Import von dieser Stelle aus neu zu starten. Wenn kein numerischer Schlüssel vorhanden ist, besteht die Möglichkeit, ein Änderungs- und Erstellungsdatum zu verwenden und den Import wiederum in der Sortierreihenfolge durchzuführen, sodass die Migration ab dem letzten im Ziel vorhandenen Datum neu gestartet werden kann.

## <a name="performance-recommendations"></a>Empfehlungen zur Leistung

### <a name="exporting"></a>Exportieren

  - Verwenden Sie ein Exporttool, das in einem Multithreadmodus ausgeführt werden kann, z. B. mydumper.

  - Verwenden Sie bei Verwendung von MySQL 8.0 ggf. [partitionierte Tabellen](https://dev.mysql.com/doc/refman/8.0/en/partitioning-overview.html), um die Geschwindigkeit von Exporten zu erhöhen.

### <a name="importing"></a>Importieren

  - Erstellen Sie nach dem Laden der Daten gruppierte Indizes und Primärschlüssel. Laden Sie Daten in Primärschlüsselreihenfolge oder in einer anderen Sortierreihenfolge, wenn der Primärschlüssel eine Datumsspalte ist (z. B. Änderungsdatum oder Erstellungsdatum).

  - Warten Sie mit der Erstellung sekundärer Indizes, bis die Daten vollständig geladen wurden. Erstellen Sie alle sekundären Indizes nach dem Laden.

  - Deaktivieren Sie vor dem Laden Fremdschlüsseleinschränkungen. Das Deaktivieren von Fremdschlüsselüberprüfungen führt zu erheblichen Leistungssteigerungen. Aktivieren Sie die Einschränkungen, und überprüfen Sie die Daten nach dem Laden, um die referentielle Integrität sicherzustellen.

  - Laden Sie Daten parallel. Vermeiden Sie zu viel Parallelität, da dies zu Ressourcenkonflikten führen kann, und überwachen Sie die Ressourcen mit den Metriken, die im Azure-Portal verfügbar sind.

## <a name="perform-the-migration"></a>Durchführen der Migration

  - Sichern der Datenbank

  - Erstellen und Überprüfen der Azure-Zielzone

  - Konfigurieren der Quellserverparameter

  - Konfigurieren der Zielserverparameter

  - Exportieren der Datenbankobjekte (Schema, Benutzer usw.)

  - Exportieren der Daten

  - Importieren der Datenbankobjekte

  - Importieren der Daten

  - Überprüfen

  - Zurücksetzen der Zielserverparameter

  - Migrieren der Anwendung(en)

## <a name="common-steps"></a>Allgemeine Schritte

Unabhängig von jeweils verwendeten Pfads gibt es allgemeine Schritte, die durchgeführt werden müssen:

  - Durchführen eines Upgrades auf eine unterstützte Azure MySQL-Version

  - Inventarisieren von Datenbankobjekten

  - Exportieren von Benutzern und Berechtigungen

## <a name="migrate-to-latest-mysql-version"></a>Migrieren zur neuesten MySQL-Version

Da die WWI Conference-Datenbank Version 5.5 verwendet, muss ein Upgrade durchgeführt werden. Der CIO hat gefordert, ein Upgrade auf die neueste Version von MySQL (derzeit 8.0) durchzuführen.

Es gibt zwei Upgrademöglichkeiten auf Version 8.0:

  - Direkt

  - Export/Import

Bei der Entscheidung, ein Upgrade durchzuführen, muss unbedingt das Tool zur Upgradeprüfung (**upgrade checker**) ausgeführt werden, um festzustellen, ob Konflikte vorliegen. Beim Upgrade auf MySQL Server 8.0 überprüft das Tool beispielsweise, ob folgende Konflikte vorhanden sind:

  - Datenbankobjektnamen, die mit reservierten Wörtern in MySQL 8.0 in Konflikt stehen

  - Verwendung des utf8mb3-Zeichensatzes

  - Verwendung der ZEROFILL-/Anzeigelängentypattribute

  - Tabellennamen, die mit Tabellen in 8.0 in Konflikt stehen

  - Verwendung temporaler Typen

  - Namen von Fremdschlüsseleinschränkungen, die länger als 64 Zeichen sind

Wenn die Upgradeprüfung keine Probleme meldet, kann problemlos ein direktes Upgrade durchgeführt werden, indem MySQL-Binärdateien ersetzt werden. Datenbanken mit Problemen müssen exportiert, und die Probleme müssen behoben werden.

## <a name="wwi-scenario"></a>WWI-Szenario

Nach der erfolgreichen Migration der MySQL-Instanz nach 8.0 hat das WWI-Migrationsteam festgestellt, dass der ursprüngliche [Database Migration Service (DMS)](https://datamigration.microsoft.com/scenario/mysql-to-azuremysql?step=1)-Migrationspfad nicht mehr verwendet werden konnte, da das DMS-Tool derzeit nur Version 5.6 und 5.7 unterstützt. Für DMS ist Netzwerkzugriff erforderlich. Das WWI-Migrationsteam war noch nicht in der Lage, die komplexen Netzwerkprobleme zu beheben. Aufgrund dieser Umgebungsprobleme wurde die möglichen Migrationstools auf MySQL Workbench eingeschränkt.

## <a name="database-objects"></a>Datenbankobjekte

Wie im Abschnitt zu Testplänen beschrieben, sollte vor und nach der Migration ein Inventar der Datenbankobjekte erstellt werden, um sicherzustellen, dass alles migriert wurde.

Wenn Sie eine gespeicherte Prozedur ausführen möchten, um diese Informationen zu generieren, können Sie eine ähnliche gespeicherte Prozedur wie folgende verwenden:

```
DELIMITER // 
CREATE PROCEDURE `Migration_PerformInventory`(IN schemaName CHAR(64)) 
BEGIN 

        DECLARE finished INTEGER DEFAULT 0; 
          DECLARE tableName varchar(100) DEFAULT ""; 

        #get all tables 
            DECLARE curTableNames 
                CURSOR FOR 
                    SELECT TABLE_NAME FROM information_schema.tables where TABL
E_SCHEMA = schemaName; 
        
            -- declare NOT FOUND handler 
            DECLARE CONTINUE HANDLER 
                FOR NOT FOUND SET finished = 1; 
        
            DROP TABLE IF EXISTS MIG_INVENTORY; 

                CREATE TABLE MIG_INVENTORY 
                ( 
                      REPORT_TYPE VARCHAR(1000), 
                      OBJECT_NAME VARCHAR(1000), 
                  PARENT_OBJECT_NAME VARCHAR (1000), 
                      OBJECT_TYPE VARCHAR(1000), 
                      COUNT INT
                ) 
                ROW_FORMAT=DYNAMIC, 
                ENGINE='InnoDB';
              INSERT INTO MIG_INVENTORY (REPORT_TYPE,OBJECT_NAME, OBJECT_TYPE, COUNT) 
                SELECT
                     'OBJECTCOUNT', 'TABLES', 'TABLES', COUNT(*)
              FROM 
                     information_schema.tables 
                where 
                     TABLE_SCHEMA = schemaName;
                #### Constraints
              INSERT INTO MIG_INVENTORY (REPORT_TYPE,OBJECT_NAME, OBJECT_TYPE, COUNT) 
                SELECT 
                      'OBJECTCOUNT', 'STATISTICS', 'STATISTICS', COUNT(*) 
                FROM 
                      information_schema.STATISTICS 
                WHERE 
                      TABLE_SCHEMA = schemaName; 
                INSERT INTO MIG_INVENTORY (REPORT_TYPE,OBJECT_NAME, OBJECT_TYPE, COUNT) 
                SELECT 
                      'OBJECTCOUNT', 'VIEWS', 'VIEWS', COUNT(*) 
                FROM 
                      information_schema.VIEWS 
                WHERE 
                      ROUTINE_TYPE = 'FUNCTION' and 
                      ROUTINE_SCHEMA = schemaName;

                INSERT INTO MIG_INVENTORY (REPORT_TYPE,OBJECT_NAME, OBJECT_TYPE, COUNT) 
                SELECT 
                      'OBJECTCOUNT', 'PROCEDURES', 'PROCEDURES', COUNT(*) 
                FROM 
                      information_schema.ROUTINES 
                WHERE 
                      ROUTINE_TYPE = 'PROCEDURE' and 
                      ROUTINE_SCHEMA = schemaName; 

                INSERT INTO MIG_INVENTORY (REPORT_TYPE,OBJECT_NAME, OBJECT_TYPE, COUNT) 
                SELECT 
                       'OBJECTCOUNT', 'EVENTS', 'EVENTS', COUNT(*) 
                FROM 
                       information_schema.EVENTS 
                WHERE 
                       EVENT_SCHEMA = schemaName; 

                INSERT INTO MIG_INVENTORY (REPORT_TYPE,OBJECT_NAME, OBJECT_TYPE, COUNT) 
                SELECT 
                       'OBJECTCOUNT', 'USER DEFINED FUNCTIONS', 'USER DEFINED FUNCTIONS'
        , COUNT(*) 
                FROM 
                        mysql.func; 

                INSERT INTO MIG_INVENTORY (REPORT_TYPE,OBJECT_NAME, OBJECT_TYPE, COUNT) 
                SELECT 
                        'OBJECTCOUNT', 'USERS', 'USERS', COUNT(*) 
                FROM 
                        mysql.user 
                WHERE 
                        user <> '' order by user; 

                OPEN curTableNames; 
        
                getTableName: LOOP 
                        FETCH curTableNames INTO tableName; 
                        IF finished = 1 THEN 
                              LEAVE getTableName; 
                        END IF; 

                   SET @s = CONCAT('SELECT COUNT(*) into @TableCount FROM ', schemaName, 
'.', tableName); 
        #SELECT @s; 
            PREPARE stmt FROM @s; 
        EXECUTE stmt;
        INSERT INTO MIG_INVENTORY (REPORT_TYPE,OBJECT_NAME, OBJECT_TYPE, COUNT) 
            
                SELECT 
                    'TABLECOUNT', tableName, 'TABLECOUNT', @TableCount; 
        DEALLOCATE PREPARE stmt; 
        
     END LOOP getTableName; 
     CLOSE curTableNames; 
          
   SELECT * FROM MIG_INVENTORY; 
END // 

DELIMITER ; 

CALL Migration_PerformInventory('reg_app');
```

  - Wenn Sie diese Prozedur für die Quelldatenbank aufrufen, wird die folgende (gekürzte) Ausgabe angezeigt:

![Gekürzte Ausgabe](./media/image4.jpg)

  - Das Ergebnis der Prozedur für die Zieldatenbank sollte nach Durchführung der Migration ähnlich wie folgt aussehen. Beachten Sie, dass in der Datenbank keine Funktionen vorhanden sind. Funktionen wurden vor der Migration entfernt.

![Datenbankfunktionen](./media/image5.jpg)

## <a name="users-and-permissions"></a>Benutzer und Berechtigungen

Für eine erfolgreiche Migration müssen die zugehörigen Benutzer und Berechtigungen in die Zielumgebung migriert werden.

Exportieren Sie alle Benutzer mit den jeweils zugewiesenen Berechtigungen mithilfe des folgenden PowerShell-Skripts:

```
$username = "yourusername"; 
$password = "yourpassword"; 
mysql -u$username -p$password --skip-column-names -A -e"SELECT CONCAT('SHOW G
RANTS FOR ''',user,'''@''',host,''';') FROM mysql.user WHERE user<>''" > Show
Grants.sql; 

$lines = get-content "ShowGrants.sql" 

foreach ($line in $lines) 
{ 
mysql -u$username -p$password --skip-column-names -A -e"$line" >> AllGrants.sql 
}
```

  - Erstellen Sie ein neues PowerShell-Skript mithilfe der PowerShell ISE (siehe Setupdokument).

  - Legen Sie **Ihren Benutzernamen** auf „root“ und **Ihr Kennwort** auf das Kennwort des root-Benutzers fest.

Anschließend können Sie das Skript `AllGrants.sql` für die neue Azure Database for MySQL-Instanz ausführen:

```
$username = "yourusername"; 
$password = "yourpassword"; 
$server = "serverDNSname"; 
$lines = get-content "AllGrants.sql" 

foreach ($line in $lines)
{ 
mysql -u$username -p$password -h$server --ssl-ca=c:\temp\BaltimoreCyberTrus
tRoot.crt.cer --skip-column-names -A -e"$line" 
}
```

Sie können Benutzer in Azure Database for MySQL auch mit PowerShell erstellen: /en-us/azure/mysql/howto-create-users.

## <a name="execute-migration"></a>Ausführen der Migration

Wenn die grundlegenden Migrationskomponenten vorhanden sind, ist es jetzt möglich, mit der Datenmigration fortzufahren. Zuvor wurden mehrere Tools und Methoden vorgestellt. WWI verwendet den MySQL Workbench-Pfad, um die Daten zu exportieren und dann in Azure Database for MySQL zu importieren.

## <a name="data-migration-checklist"></a>Checkliste für die Datenmigration

  - Ermitteln Sie die Komplexität der Umgebung und die Möglichkeit einer Onlinemigration.

  - Berücksichtigen Sie die Datendrift. Das Beenden des Datenbankdiensts kann eine mögliche Datendrift beseitigen.

  - Konfigurieren Sie die Quellparameter für schnellen Export.

  - Konfigurieren Sie die Zielparameter für schnellen Import.

  - Testen Sie Migrationen, die eine andere Quellversion als das Ziel aufweisen.

  - Migrieren Sie alle nicht datenbasierten Objekte wie Benutzernamen und Berechtigungen.

  - Achten Sie darauf, dass alle Aufgaben dokumentiert und abgehakt werden, während die Migration ausgeführt wird.  


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Datenmigration mit MySQL Workbench](./09-data-migration-with-mySQL-workbench.md)