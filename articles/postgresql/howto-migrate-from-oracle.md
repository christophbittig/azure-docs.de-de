---
title: 'Oracle zu Azure Database for PostgreSQL: Migrationsleitfaden'
titleSuffix: Azure Database for PostgreSQL
description: In diesem Leitfaden erfahren Sie, wie Sie Ihr Oracle-Schema zu Azure Database for PostgreSQL migrieren.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 03/18/2021
ms.openlocfilehash: 758199dca165c301322631e0d29b1af2711dc56b
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129356933"
---
# <a name="migrate-oracle-to-azure-database-for-postgresql"></a>Onlinemigration von Oracle zu Azure Database for PostgreSQL

In diesem Leitfaden erfahren Sie, wie Sie Ihr Oracle-Schema zu Azure Database for PostgreSQL migrieren. 

Ausführliche und umfassende Anleitungen für die Migration finden Sie in den [Ressourcen des Migrationshandbuchs](https://github.com/microsoft/OrcasNinjaTeam/blob/master/Oracle%20to%20PostgreSQL%20Migration%20Guide/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Guide.pdf). 

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie Ihr Oracle-Schema zu Azure Database for PostgreSQL migrieren möchten, müssen Sie folgende Schritte ausführen: 

- Überprüfen, ob Ihre Quellumgebung unterstützt wird. 
- Laden Sie die neueste Version von [ora2pg](https://ora2pg.darold.net/) herunter. 
- Verfügbarkeit der aktuellsten Version des [DBD Moduls](https://www.cpan.org/modules/by-module/DBD/). 


## <a name="overview"></a>Übersicht

PostgreSQL ist eine der fortschrittlichsten Open Source-Datenbanken der Welt. In diesem Artikel wird beschrieben, wie Sie mit dem kostenlosen ora2pg-Tool eine Oracle-Datenbank zu PostgreSQL migrieren. Sie können ora2pg verwenden, um eine Oracle- oder MySQL-Datenbank zu einem PostgreSQL-kompatiblen Schema zu migrieren. 

Das ora2pg-Tool stellt eine Verbindung mit Ihrer Oracle-Datenbank her, überprüft sie automatisch und extrahiert ihre Struktur bzw. Ihre Daten. Anschließend generiert ora2pg SQL-Skripts, die Sie in Ihre PostgreSQL-Datenbank laden können. Sie können ora2pg für Aufgaben wie das Reverse Engineering einer Oracle-Datenbank, die Migration einer großen Unternehmensdatenbank oder einfach die Replikation einiger Oracle-Daten in eine PostgreSQL-Datenbank verwenden. Das Tool ist einfach zu verwenden und erfordert keine Oracle-Datenbankkenntnisse, außer der Fähigkeit, die für die Verbindung zur Oracle-Datenbank erforderlichen Parameter anzugeben.

> [!NOTE]
> Weitere Informationen zur Verwendung der neuesten Version von ora2pg finden Sie in der [ora2pg-Dokumentation](https://ora2pg.darold.net/documentation.html).

### <a name="typical-ora2pg-migration-architecture"></a>Typische ora2pg-Migrationsarchitektur

![Screenshot der or2pg-Migrationsarchitektur](media/howto-migrate-from-oracle/ora2pg-migration-architecture.png)

Nachdem Sie die VM und Azure Database for PostgreSQL bereitgestellt haben, benötigen Sie zwei Konfigurationen, um die Konnektivität zwischen ihnen zu aktivieren: **Zugriff auf Azure-Dienste zulassen** und **SSL-Verbindung erzwingen**: 

- Blatt **Verbindungssicherheit** > **Zugriff auf Azure-Dienste zulassen** > **EIN**

- Blatt **Verbindungssicherheit** > **SSL-Einstellungen** > **SSL-Verbindung erzwingen** > **DEAKTIVIERT**

### <a name="recommendations"></a>Empfehlungen

- Um die Leistung der Bewertungs- oder Exportvorgänge auf dem Oracle-Server zu verbessern, sammeln Sie Statistiken:

   ```
   BEGIN
   
     DBMS_STATS.GATHER_SCHEMA_STATS
     DBMS_STATS.GATHER_DATABASE_STATS
     DBMS_STATS.GATHER_DICTIONARY_STATS
     END;
   ```

- Exportieren Sie Daten, indem Sie den Befehl `COPY` anstelle von `INSERT` verwenden.

- Vermeiden Sie den Export von Tabellen mit ihren Fremdschlüsseln (FS), Beschränkungen und Indizes. Diese Elemente verlangsamen den Import von Daten in PostgreSQL.

- Erstellen Sie materialisierte Sichten mithilfe der *No Data-Klausel*. Aktualisieren Sie die Sichten später.

- Wenn möglich, verwenden Sie eindeutige Indizes in materialisierten Sichten. Diese Indizes können die Aktualisierung beschleunigen, wenn Sie die Syntax `REFRESH MATERIALIZED VIEW CONCURRENTLY` verwenden.


## <a name="pre-migration"></a>Vor der Migration 

Nachdem Sie sich vergewissert haben, dass Ihre Quellumgebung unterstützt wird und dass Sie alle Voraussetzungen erfüllt haben, können Sie die Prä-Migrationsphase starten. Vorbereitung: 

1. **Entdecken:** Inventarisieren Sie die Datenbanken, die Sie migrieren müssen. 
2. **Bewerten:** Bewerten Sie diese Datenbanken auf mögliche Migrationsprobleme oder -hindernisse.
3. **Konvertieren:** Beheben Sie alle Punkte, die Sie entdeckt haben. 
 
Bei heterogenen Migrationen, z. B. Oracle zu Azure Database for PostgreSQL, muss in dieser Phase auch die Kompatibilität der Schemata der Quelldatenbank mit der Zielumgebung erreicht werden.

### <a name="discover"></a>Entdecken

Das Ziel der Ermittlungsphase ist es, vorhandene Datenquellen und Details über die verwendeten Features zu identifizieren. Diese Phase hilft Ihnen, die Migration besser zu verstehen und zu planen. Bei diesem Vorgang ermitteln Sie u. a. alle Oracle-Instanzen Ihrer Organisation, die innerhalb des Netzwerks ausgeführt werden, sowie die jeweilige Version und die verwendeten Features.

Die Microsoft-Skripts für die Vorabbewertung für Oracle werden für die Oracle-Datenbank ausgeführt. Die Skripts für die Vorabbewertung fragen die Oracle-Metadaten ab. Die Skripts stellen Folgendes bereit:

- Einen Datenbankbestand, einschließlich der Anzahl von Objekten nach Schema, Typ und Status.
- Eine grobe Schätzung der Rohdaten in jedem Schema auf der Basis von Statistiken.
- Die Größe von Tabellen in jedem Schema.
- Die Anzahl der Codezeilen pro Paket, Funktion, Prozedur usw.

Laden Sie die zugehörigen Skripts von [GitHub](https://github.com/microsoft/DataMigrationTeam/tree/master/Whitepapers) herunter.

### <a name="assess"></a>Bewerten

Nachdem Sie eine Inventur der Oracle-Datenbanken durchgeführt haben, haben Sie eine Vorstellung von der Größe der Datenbank und den potenziellen Herausforderungen. Im nächsten Schritt führen Sie die Bewertung aus.

Das Einschätzen der Kosten für eine Migration von Oracle zu PostgreSQL ist nicht einfach. Zur Bewertung der Migrationskosten prüft ora2pg alle Datenbankobjekte, Funktionen und gespeicherten Prozeduren auf Objekte und PL/SQL-Code, die nicht automatisch konvertiert werden können.

Das ora2pg-Tool verfügt über einen Inhaltsanalysemodus, der die Oracle-Datenbank überprüft, um einen Textbericht zu generieren. In dem Bericht ist beschrieben, was die Oracle-Datenbank enthält und was nicht exportiert werden kann.

Um den *Analyse-und Berichtsmodus* zu aktivieren, verwenden Sie den exportierten Typ, `SHOW_REPORT` der im folgenden Befehl gezeigt wird:

```
ora2pg -t SHOW_REPORT
```

Das ora2pg-Tool kann SQL- und PL/SQL-Code von der Oracle-Syntax in PostgreSQL konvertieren. Nachdem die Datenbank analysiert wurde, kann ora2pg also die Schwierigkeiten im Code und die Zeit, die für die Migration einer kompletten Datenbank erforderlich ist, abschätzen.

Um die Migrationskosten in Personentagen abzuschätzen, können Sie in ora2pg eine Konfigurationsanweisung namens `ESTIMATE_COST` verwenden. Sie können diese Anweisung auch an einer Eingabeaufforderung aktivieren:

```
ora2pg -t SHOW_REPORT --estimate_cost
```

Die Standard-Migrationseinheit entspricht etwa fünf Minuten für einen PostgreSQL-Experten. Wenn dies Ihre erste Migration ist, können Sie die Standard-Migrationseinheit mit der Konfigurationsanweisung `COST_UNIT_VALUE` oder der Befehlszeilenoption `--cost_unit_value` erhöhen.

Die letzte Zeile des Berichts zeigt den gesamten geschätzten Migrationscode in Personentagen. Die Schätzung folgt der Anzahl der geschätzten Migrationseinheiten für jedes Objekt.

Im folgenden Codebeispiel werden einige Bewertungsvarianten angezeigt: 
* Bewertung von Tabellen
* Bewertung von Spalten
* Schemabewertung, bei der eine Standardkosteneinheit von 5 Minuten verwendet wird
* Schemabewertung, bei der eine Kosteneinheit von 10 Minuten verwendet wird

```
ora2pg -t SHOW_TABLE -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\tables.txt 
ora2pg -t SHOW_COLUMN -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\columns.txt
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf --dump_as_html --estimate_cost > c:\ts303\hr_migration\reports\report.html
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf –-cost_unit_value 10 --dump_as_html --estimate_cost > c:\ts303\hr_migration\reports\report2.html
```

Hier ist die Ausgabe der Schemabewertung für die Migrationsebene B-5:

* Migrationebenen:

  * A - Migration, die automatisch ausgeführt werden kann
    
  * B - Migration mit Code-Rewrite und einem Zeitaufwand von bis zu 5 Tagen
    
  * C - Migration mit Code-Rewrite und einem Zeitaufwand von über 5 Tagen
    
* Technische Ebenen:

   * 1 = Trivial: Keine gespeicherten Funktionen und keine Trigger

   * 2 = Easy: Keine gespeicherten Funktionen, aber Trigger, kein manuelles Umschreiben

   * 3 = Einfach: Gespeicherte Funktionen und/oder Trigger, kein manuelles Umschreiben

   * 4 = Manuell: Keine gespeicherten Funktionen, aber Trigger oder Ansichten mit Codeumschreibung

   * 5 = Schwierig: Gespeicherte Funktionen und/oder Trigger mit Codeumschreibung

Die Bewertung besteht aus Folgendem: 
* Ein Buchstabe (A oder B), um anzugeben, ob die Migration manuell umgeschrieben werden muss.

* Eine Zahl von 1 bis 5 zur Angabe der technischen Schwierigkeit. 

Eine weitere Option, `-human_days_limit`, gibt den Grenzwert der Personentage an. Legen Sie hier die Migrationsebene auf C fest, um anzuzeigen, dass die Migration einen hohen Arbeitsaufwand, ein umfassendes Projektmanagement und Migrationsunterstützung erfordert. Der Standardwert beträgt 10 Personentage. Sie können die Konfigurationsanweisung `HUMAN_DAYS_LIMIT` verwenden, um diesen Standardwert dauerhaft zu ändern.

Diese Schemabewertung wurde entwickelt, um Benutzern bei der Entscheidung zu helfen, welche Datenbank zuerst migriert werden soll und welche Teams mobilisiert werden müssen.

### <a name="convert"></a>Convert

Bei Migrationen mit minimaler Downtime ändert sich Ihre Migrationsquelle. Sie weicht nach der einmaligen Migration in Bezug auf Daten und Schema vom Ziel ab. Stellen Sie während der *Datensynchronisierungsphase* sicher, dass alle Änderungen in der Quelle erfasst und nahezu in Echtzeit auf das Ziel angewendet werden. Nachdem Sie überprüft haben, dass alle Änderungen auf das Ziel angewendet wurden, können Sie von der Quell- zur Zielumgebung *wechseln*.

In diesem Schritt der Migration werden der Oracle-Code und die DDL-Skripts in PostgreSQL konvertiert oder übersetzt. Das ora2pg Tool exportiert die Oracle-Objekte automatisch in ein PostgreSQL-Format. Einige der generierten Objekte können nicht ohne manuelle Änderungen in der PostgreSQL-Datenbank kompiliert werden.  

Um zu verstehen, welche Elemente einen manuellen Eingriff erfordern, kompilieren Sie zunächst die von ora2pg generierten Dateien anhand der PostgreSQL-Datenbank. Überprüfen Sie das Protokoll, und nehmen Sie dann alle notwendigen Änderungen vor, bis die Schemastruktur mit der PostgreSQL-Syntax kompatibel ist.


#### <a name="create-a-migration-template"></a>Erstellen einer Migrationsvorlage 

Es wird empfohlen, die von ora2pg bereitgestellte Migrationsvorlage zu verwenden. Wenn Sie die Optionen `--project_base` und `--init_project` verwenden, erstellt ora2pg eine Projektvorlage mit einer Arbeitsstruktur, einer Konfigurationsdatei und einem Skript zum Exportieren aller Objekte aus der Oracle-Datenbank. Weitere Informationen finden Sie in der [ora2pg Dokumentation](https://ora2pg.darold.net/documentation.html).

Verwenden Sie den folgenden Befehl: 

```
ora2pg --project_base /app/migration/ --init_project test_project
```

Beispielausgabe: 
   
```
ora2pg --project_base /app/migration/ --init_project test_project
        Creating project test_project.
        /app/migration/test_project/
                schema/
                        dblinks/
                        directories/
                        functions/
                        grants/
                        mviews/
                        packages/
                        partitions/
                        procedures/
                        sequences/
                        synonyms/
                        tables/
                        tablespaces/
                        triggers/
                        types/
                        views/
                sources/
                        functions/
                        mviews/
                        packages/
                        partitions/
                        procedures/
                        triggers/
                        types/
                        views/
                data/
                config/
                reports/

        Generating generic configuration file
        Creating script export_schema.sh to automate all exports.
        Creating script import_all.sh to automate all imports.
```

Das Verzeichnis `sources/` enthält den Oracle-Code. Das Verzeichnis `schema/` enthält den zu PostgreSQL übertragenen Code. Und das Verzeichnis `reports/` enthält die HTML-Berichte und die Migrationskostenbewertung.


Nachdem die Projektstruktur erstellt wurde, wird eine generische Konfigurationsdatei erstellt. Definieren Sie die Oracle-Datenbankverbindung sowie die entsprechenden Konfigurationsparameter in der Konfigurationsdatei. Weitere Informationen zur Konfigurationsdatei finden Sie in der [Dokumentation zu ora2pg](https://ora2pg.darold.net/documentation.html).


#### <a name="export-oracle-objects"></a>Konvertieren von Oracle-Objekten

Als nächstes exportieren Sie die Oracle-Objekte als PostgreSQL-Objekte, indem Sie die Datei *export_schema.sh* ausführen.

```
cd /app/migration/mig_project
./export_schema.sh
```

Führen Sie den folgenden Befehl manuell aus.

```
SET namespace="/app/migration/mig_project"

ora2pg -p -t DBLINK -o dblink.sql -b %namespace%/schema/dblinks -c %namespace%/config/ora2pg.conf
ora2pg -p -t DIRECTORY -o directory.sql -b %namespace%/schema/directories -c %namespace%/config/ora2pg.conf
ora2pg -p -t FUNCTION -o functions2.sql -b %namespace%/schema/functions -c %namespace%/config/ora2pg.conf 
ora2pg -p -t GRANT -o grants.sql -b %namespace%/schema/grants -c %namespace%/config/ora2pg.conf 
ora2pg -p -t MVIEW -o mview.sql -b %namespace%/schema/mviews -c %namespace%/config/ora2pg.conf
ora2pg -p -t PACKAGE -o packages.sql -b %namespace%/schema/packages -c %namespace%/config/ora2pg.conf
ora2pg -p -t PARTITION -o partitions.sql -b %namespace%/schema/partitions -c %namespace%/config/ora2pg.conf
ora2pg -p -t PROCEDURE -o procs.sql -b %namespace%/schema/procedures -c %namespace%/config/ora2pg.conf
ora2pg -p -t SEQUENCE -o sequences.sql -b %namespace%/schema/sequences -c %namespace%/config/ora2pg.conf
ora2pg -p -t SYNONYM -o synonym.sql -b %namespace%/schema/synonyms -c %namespace%/config/ora2pg.conf
ora2pg -p -t TABLE -o table.sql -b %namespace%/schema/tables -c %namespace%/config/ora2pg.conf 
ora2pg -p -t TABLESPACE -o tablespaces.sql -b %namespace%/schema/tablespaces -c %namespace%/config/ora2pg.conf
ora2pg -p -t TRIGGER -o triggers.sql -b %namespace%/schema/triggers -c %namespace%/config/ora2pg.conf 
ora2pg -p -t TYPE -o types.sql -b %namespace%/schema/types -c %namespace%/config/ora2pg.conf 
ora2pg -p -t VIEW -o views.sql -b %namespace%/schema/views -c %namespace%/config/ora2pg.conf
```

Um die Daten zu extrahieren, verwenden Sie den folgenden Befehl.

```
ora2pg -t COPY -o data.sql -b %namespace/data -c %namespace/config/ora2pg.conf
```

#### <a name="compile-files"></a>Dateien kompilieren

Abschließend kompilieren Sie alle Dateien für den Azure Database for PostgreSQL-Server. Sie können wählen, ob Sie die manuell generierten DDL-Dateien laden oder das zweite Skript *import_all.sh* verwenden, um diese Dateien interaktiv zu importieren.

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\ schema\sequences\create_sequences.log

psql -f %namespace%\schema\tables\table.sql -h server1-server.postgres.database.azure.com p 5432 -U username@server1-server -d database -l %namespace%\schema\tables\create_table.log
```

Hier folgt der Befehl für den Datenimport:

```
psql -f %namespace%\data\table1.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table1.log

psql -f %namespace%\data\table2.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table2.log
```

Während die Dateien kompiliert werden, überprüfen Sie die Protokolle, und korrigieren Sie jede Syntax, die ora2pg nicht selbständig konvertieren konnte.

Weitere Informationen finden Sie unter [Oracle to Azure Database for PostgreSQL Migration Workarounds](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf).

## <a name="migrate"></a>Migrate 

Nachdem Sie die notwendigen Voraussetzungen geschaffen und die Schritte vor der Migration abgeschlossen haben, können Sie die Schema- und Datenmigration starten.

### <a name="migrate-schema-and-data"></a>Migrieren von Schemas und Daten

Wenn Sie die notwendigen Korrekturen vorgenommen haben, ist ein stabiler Build der Datenbank bereit für die Bereitstellung. Führen Sie die `psql`-Importbefehle aus, die auf die Dateien verweisen, die den geänderten Code enthalten. Dieser Task kompiliert die Datenbankobjekte anhand der PostgreSQL-Datenbank und importiert die Daten.

Bei diesem Schritt können Sie ein gewisses Maß an Parallelität beim Importieren der Daten implementieren.

### <a name="sync-data-and-cut-over"></a>Synchronisieren von Daten und Übernahme

Bei Onlinemigrationen (mit minimaler Downtime) ändert sich die Migrationsquelle ständig. Sie weicht nach der einmaligen Migration in Bezug auf Daten und Schema vom Ziel ab. 

Stellen Sie während der *Datensynchronisierungsphase* sicher, dass alle Änderungen in der Quelle erfasst und nahezu in Echtzeit auf das Ziel angewendet werden. Nachdem Sie überprüft haben, dass alle Änderungen angewendet wurden, können Sie von der Quell- zur Zielumgebung wechseln.

Bei einer Onlinemigration wenden Sie sich an AskAzureDBforPostgreSQL@service.microsoft.com, um Unterstützung zu erhalten.

Verwenden Sie bei einer *Delta- bzw. inkrementellen Migration*, die ora2pg verwendet, für jede Tabelle eine Abfrage, die nach Datum, Uhrzeit oder einem anderen Parameter filtert (*ausschneidet*). Schließen Sie dann die Migration ab, indem Sie eine zweite Abfrage verwenden, die die restlichen Daten migriert.

Migrieren Sie in der Quelldatentabelle zunächst alle historischen Daten. Ein Beispiel:

```
select * from table1 where filter_data < 01/01/2019
```

Sie können die Änderungen seit der ersten Migration abfragen, indem Sie einen Befehl wie den folgenden ausführen:

```
select * from table1 where filter_data >= 01/01/2019
```

In diesem Fall wird empfohlen, die Validierung zu erweitern, indem die Datenparität auf beiden Seiten, der Quelle und dem Ziel, überprüft wird.

## <a name="post-migration"></a>Nach der Migration 

Führen Sie nach der *Migration* die entsprechenden nach der Migration auszuführenden Aufgaben durch, um sicherzustellen, dass alles so reibungslos und effizient wie möglich funktioniert.

### <a name="remediate-applications"></a>Korrigieren von Anwendungen

Wenn die Daten in die Zielumgebung migriert wurden, müssen alle Anwendungen, die zuvor die Quelle verwendet haben, beginnen das Ziel zu verwenden. Das Setup erfordert mitunter Änderungen an den Anwendungen.

### <a name="test"></a>Test

Nachdem die Daten zum Ziel migriert wurden, führen Sie Tests mit den Datenbanken durch, um zu überprüfen, ob die Anwendungen mit dem Ziel ordnungsgemäß funktionieren. Stellen Sie sicher, dass die Quelle und das Ziel ordnungsgemäß migriert wurden, indem Sie die manuellen Datenüberprüfungsskripts mit der Oracle-Quell- und PostgreSQL-Zieldatenbank ausführen.

Wenn die Quell- und Zieldatenbank einen Netzwerkpfad haben, sollte idealerweise ora2pg für die Datenvalidierung verwendet werden. Sie können die Aktion `TEST` verwenden, um sicherzustellen, dass alle Objekte aus der Oracle-Datenbank in PostgreSQL erstellt worden sind. 

Führen Sie den folgenden Befehl aus:

```
ora2pg -t TEST -c config/ora2pg.conf > migration_diff.txt
```

### <a name="optimize"></a>Optimieren

Die Phase nach der Migration ist entscheidend für den Abgleich von Problemen mit der Datengenauigkeit und die Überprüfung auf Vollständigkeit. In dieser Phase gehen Sie auch auf Leistungsprobleme mit der Workload ein.

## <a name="migration-assets"></a>Migrationsressourcen 

Weitere Informationen zu diesem Migrationsszenario finden Sie in den folgenden Ressourcen. Sie unterstützen den Einsatz in realen Migrationsprojekten.

| Resource | BESCHREIBUNG    |
| -------------- | ------------------ |
| [Cookbook für die Migration von Oracle zu Azure PostgreSQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf) | Dieses Dokument hilft Architekten, Beratern, Datenbankadministratoren und verwandten Rollen bei der schnellen Migration von Workloads von Oracle zu Azure Database for PostgreSQL unter Verwendung von ora2pg. |
| [Workarounds für die Migration von Oracle zu Azure PostgreSQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf) | Dieses Dokument hilft Architekten, Beratern, Datenbankadministratoren und verwandten Rollen bei der schnellen Behebung oder Umgehung von Problemen während der Migration von Workloads von Oracle zu Azure Database for PostgreSQL. |
| [Schritte zum Installieren von ora2pg unter Windows oder Linux](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)                       | Dieses Dokument enthält eine Schnellinstallationsanleitung für die Migration von Schema und Daten von Oracle zu Azure Database for PostgreSQL unter Verwendung von ora2pg unter Windows oder Linux. Weitere Informationen finden Sie in der [ora2pg Dokumentation](http://ora2pg.darold.net/documentation.html). |

Das Data SQL Engineering-Team hat diese Ressourcen entwickelt. Die Hauptaufgabe dieses Teams ist die Aufhebung der Blockierung und Beschleunigung komplexer Modernisierungsprojekte für die Migration von Datenplattformen zur Microsoft Azure-Datenplattform.

## <a name="more-support"></a>Weitere Unterstützung

Wenn Sie Hilfe zur Migration benötigen, die über den Umfang des ora2pg-Tools hinausgeht, wenden Sie sich an [@Ask Azure DB for PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com).

## <a name="next-steps"></a>Nächste Schritte

Eine Matrix der Dienste und Tools für die Datenbank- und Datenmigration sowie für Spezialaufgaben finden Sie unter [Dienste und Tools für die Datenmigration](../dms/dms-tools-matrix.md).

Dokumentation: 
- [Dokumentation zu Azure Database for PostgreSQL](./index.yml)
- [ora2pg-Dokumentation](https://ora2pg.darold.net/documentation.html)
- [PostgreSQL-Website](https://www.postgresql.org/)
- [Unterstützung für autonome Transaktionen in PostgreSQL](http://blog.dalibo.com/2016/08/19/Autonoumous_transactions_support_in_PostgreSQL.html) 
