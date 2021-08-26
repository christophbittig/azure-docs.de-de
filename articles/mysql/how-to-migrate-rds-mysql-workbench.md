---
title: Migrieren von Amazon RDS für MySQL zu Azure Database for MySQL mithilfe von MySQL Workbench
description: In diesem Artikel wird beschrieben, wie Sie mithilfe des MySQL Workbench-Migrations-Assistenten Amazon RDS für MySQL zu Azure Database for MySQL migrieren.
author: HJToland3
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 05/21/2021
ms.openlocfilehash: c744517dfab088fa4edb104b2c5aebda2818ab8f
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/30/2021
ms.locfileid: "122639719"
---
# <a name="migrate-amazon-rds-for-mysql-to-azure-database-for-mysql-using-mysql-workbench"></a>Migrieren von Amazon RDS für MySQL zu Azure Database for MySQL mithilfe von MySQL Workbench

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Sie können verschiedene Hilfsprogramme wie die MySQL Workbench-Funktionen zum Exportieren und Importieren, Azure Database Migration Service (DMS) und die MySQL-Funktionen zum Sichern und Wiederherstellen verwenden, um Amazon RDS für MySQL zu Azure Database for MySQL zu migrieren. Die Verwendung des MySQL Workbench-Migrations-Assistenten bietet jedoch eine einfache und bequeme Möglichkeit, Ihre Amazon RDS für MySQL-Datenbanken zu Azure Database for MySQL zu migrieren.

Mit dem Migrations-Assistenten können Sie bequem auswählen, welche Schemas und Objekte migriert werden sollen. Außerdem können Sie Serverprotokolle anzeigen, um Fehler und Engpässe in Echtzeit zu identifizieren. Daher können Sie Tabellen oder Datenbankstrukturen und -objekte während des Migrationsprozesses bearbeiten und ändern, wenn ein Fehler erkannt wird, und die Migration dann fortsetzen, ohne von Grund auf neu starten zu müssen.

> [!NOTE]
> Sie können den Migrations-Assistenten auch verwenden, um andere Quellen wie Microsoft SQL Server, Oracle, PostgreSQL, MariaDB usw. zu migrieren, die außerhalb des Rahmens dieses Artikels liegen.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit dem Migrationsvorgang beginnen, sollten Sie sicherstellen, dass mehrere Parameter und Features wie unten beschrieben ordnungsgemäß konfiguriert und eingerichtet sind.

- Stellen Sie sicher, dass der Zeichensatz der Quell- und Zieldatenbanken identisch ist.
- Legen Sie das Wartezeittimeout abhängig von der Datenmenge oder Workload, die Sie importieren oder migrieren möchten, auf einen angemessenen Zeitraum fest.
- Legen Sie `max_allowed_packet parameter` abhängig von der Größe der zu importierenden oder migrierenden Datenbank auf einen angemessenen Wert fest.
- Stellen Sie sicher, dass alle Ihre Tabellen InnoDB verwenden, da Azure Database for MySQL-Server nur die InnoDB-Speicher-Engine unterstützen.
- Entfernen, ersetzen oder ändern Sie alle Trigger, gespeicherten Prozeduren und andere Funktionen, die DEFINER-Elemente für Stammbenutzer oder Administratoren enthalten (Azure Database for MySQL unterstützt die Administratorberechtigung nicht). Führen Sie den folgenden Befehl aus, um die DEFINER-Elemente durch den Namen des Administratorbenutzers zu ersetzen, der den Importvorgang ausführt:

  ```
  DELIMITER; ;/*!50003 CREATE*/ /*!50017 DEFINER=`root`@`127.0.0.1`*/ /*!50003
  DELIMITER;
  /* Modified to */
  DELIMITER;
  /*!50003 CREATE*//*!50017 DEFINER=`AdminUserName`@`ServerName`*/ /*!50003
  DELIMITER;

  ```

- Wenn benutzerdefinierte Funktionen (User Defined Functions, UDFs) auf dem Datenbankserver ausgeführt werden, müssen Sie die Berechtigung für die mysql-Datenbank löschen. Verwenden Sie die folgende Abfrage, um zu ermitteln, ob UDFs auf Ihrem Server ausgeführt werden:

  ```
  SELECT * FROM mysql.func;
  ```

  Wenn Sie feststellen, dass UDFs ausgeführt werden, können Sie diese mithilfe der folgenden Abfrage löschen:

  ```
  DROP FUNCTION your_UDFunction;
  ```

- Stellen Sie sicher, dass der Server, auf dem das Tool ausgeführt wird, und letztendlich der Exportspeicherort über ausreichend Speicherplatz und Computeleistung (virtuelle Kerne, CPU und Arbeitsspeicher) verfügen, um den Exportvorgang auszuführen, insbesondere beim Exportieren einer sehr großen Datenbank.
- Erstellen Sie einen Pfad zwischen der lokalen Instanz oder der AWS-Instanz und Azure Database for MySQL, wenn sich die Workload hinter Firewalls oder anderen Netzwerksicherheitsebenen befindet.

## <a name="begin-the-migration-process"></a>Starten des Migrationsprozesses

1. Melden Sie sich bei MySQL Workbench an, und klicken Sie dann auf das Startsymbol, um den Migrationsvorgang zu starten.
2. Klicken Sie wie im folgenden Screenshot gezeigt auf der linken Navigationsleiste auf das Symbol des Migrations-Assistenten.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/begin-the-migration.png" alt-text="Startbildschirm von MySQL Workbench":::

   Die Seite **Übersicht** des Migrations-Assistenten wird wie unten dargestellt angezeigt.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/migration-wizard-welcome.png" alt-text="Willkommensseite des MySQL Workbench-Migrations-Assistenten":::

3. Ermitteln Sie, ob ein ODBC-Treiber für MySQL-Server installiert ist, indem Sie auf **Open ODBC Administrator** (ODBC-Administrator öffnen) klicken.

   In diesem Fall werden Sie auf der Registerkarte **Treiber** feststellen, dass bereits zwei ODBC-Treiber für MySQL-Server installiert sind.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/obdc-administrator-page.png" alt-text="Seite „ODBC Data Source Administrator“ (ODBC-Datenquellenadministrator)":::

   Wenn ein MySQL-ODBC-Treiber nicht installiert ist, verwenden Sie für dessen Installation das MySQL-Installationsprogramm, mit dem Sie MySQL Workbench installiert haben. Weitere Informationen zur Installation des MySQL-ODBC-Treibers finden Sie in den folgenden Ressourcen:

   - [4.1 Installing Connector/ODBC on Windows (4.1 Installieren von Connectors/ODBC unter Windows)](https://dev.mysql.com/doc/connector-odbc/en/connector-odbc-installation-binary-windows.html)
   - [ODBC Driver for MySQL: How to Install and Set up Connection (Step-by-step) (ODBC-Treiber für MySQL: Ausführliche Anleitung zum Installieren und Einrichten der Verbindung) – {coding}Sight (codingsight.com)](https://codingsight.com/install-and-configure-odbc-drivers-for-mysql/)

4. Schließen Sie das Dialogfeld **ODBC Data Source Administrator** (ODBC-Datenquellenadministrator), und fahren Sie dann mit dem Migrationsvorgang fort.

## <a name="configure-source-database-server-connection-parameters"></a>Konfigurieren von Verbindungsparametern für den Quelldatenbankserver

1. Klicken Sie auf der Seite **Übersicht** auf **Migration starten**.

   Die Seite **Source Selection** (Quellauswahl) wird angezeigt. Geben Sie auf dieser Seite Informationen zum Managementsystem für relationale Datenbanken (RDBMS), von dem Sie migrieren, und die Parameter für die Verbindung an.

2. Wählen Sie im Feld **Database System** (Datenbanksystem) die Option **MySQL** aus.
3. Wählen Sie im Feld **Stored Connection** (Gespeicherte Verbindung) eine der gespeicherten Verbindungseinstellungen für dieses RDBMS aus.

   Sie können Verbindungen speichern, indem Sie das Kontrollkästchen am unteren Rand der Seite aktivieren und einen Namen Ihrer Wahl angeben.

4. Wählen Sie im Feld **Verbindungsmethode** die Option **Standard (TCP/IP)** aus.
5. Geben Sie im Feld **Hostname** den Namen des Quelldatenbankservers an.
6. Geben Sie im Feld **Port** **3306** an, und geben Sie dann den Benutzernamen und das Kennwort für die Verbindung mit dem Server ein.
7. Geben Sie im Feld **Datenbank** den Namen der Datenbank ein, die Sie migrieren möchten (falls bekannt). Lassen Sie dieses Feld andernfalls leer.
8. Klicken Sie auf **Verbindung testen**, um die Verbindung mit Ihrer MySQL-Serverinstanz zu testen.

   Wenn Sie die richtigen Parameter eingegeben haben, wird eine Meldung angezeigt, die darüber informiert, dass der Verbindungsversuch erfolgreich war.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/source-connection-parameters.png" alt-text="Seite mit den Parametern für die Quelldatenbankverbindung":::

9. Wählen Sie **Weiter** aus.

## <a name="configure-target-database-server-connection-parameters"></a>Konfigurieren von Verbindungsparametern für den Zieldatenbankserver

1. Legen Sie auf der Seite **Target Selection** (Zielauswahl) die Parameter fest, um eine Verbindung mit der MySQL-Zielserverinstanz herzustellen, indem Sie einen ähnlichen Vorgang wie beim Einrichten der Verbindung mit dem Quellserver durchführen.
2. Klicken Sie auf **Verbindung testen**, um zu überprüfen, ob die Verbindung erfolgreich hergestellt wurde.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/target-connection-parameters.png" alt-text="Seite mit den Parametern für die Zieldatenbankverbindung":::

3. Wählen Sie **Weiter** aus.

## <a name="select-the-schemas-to-migrate"></a>Auswählen der Schemas für die Migration

Der Migrations-Assistent kommuniziert mit Ihrer MySQL-Serverinstanz und ruft vom Quellserver eine Liste von Schemas ab.

1. Klicken Sie auf **Show logs** (Protokolle anzeigen), um diesen Vorgang anzuzeigen.

   Der folgende Screenshot zeigt, wie die Schemas vom Quelldatenbankserver abgerufen werden.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/retrieve-schemas.png" alt-text="Seite „Fetch schemas list“ (Schemaliste abrufen)":::

2. Klicken Sie auf **Weiter**, um zu überprüfen, ob alle Schemas erfolgreich abgerufen wurden.

   Der folgende Screenshot zeigt die Liste der abgerufenen Schemas.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/schemas-selection.png" alt-text="Seite „Schemas Selection“ (Schemaauswahl)":::

   Sie können nur Schemas migrieren, die in dieser Liste angezeigt werden.

3. Wählen Sie die Schemas aus, die Sie migrieren möchten, und klicken Sie dann auf **Weiter**.

## <a name="object-migration"></a>Objektmigration

Geben Sie als Nächstes die Objekte an, die Sie migrieren möchten.

1. Klicken Sie auf **Auswahl einblenden**, und wählen Sie dann unter **Verfügbare Objekte** die zu migrierenden Objekte aus, und fügen Sie sie hinzu.

   Wenn Sie die Objekte hinzugefügt haben, werden diese wie im folgenden Screenshot gezeigt unter **Zu migrierende Objekte** angezeigt.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/source-objects.png" alt-text="Seite zum Auswählen der Quellobjekte":::

   In diesem Szenario wurden alle Tabellenobjekte ausgewählt.

2. Wählen Sie **Weiter** aus.

## <a name="edit-data"></a>Bearbeiten von Daten

In diesem Abschnitt haben Sie die Möglichkeit, die zu migrierenden Objekte zu bearbeiten.

1. Beachten Sie auf der Seite **Manual Editing** (Manuelle Bearbeitung) das Dropdownmenü **Ansicht** in der Ecke rechts oben.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/manual-editing.png" alt-text="Seite zum Auswählen der manuellen Bearbeitung":::

   Die Dropdownfeld **Ansicht** umfasst drei Elemente:

   - **Alle Objekte:** Hiermit werden alle Objekte angezeigt. Mit dieser Option können Sie die generierten SQL-Objekte manuell bearbeiten, bevor Sie sie auf den Zieldatenbankserver anwenden. Wählen Sie hierzu das Objekt aus, und klicken Sie auf „Show Code and Messages“ (Code und Meldungen anzeigen). Sie können den generierten MySQL-Code, der dem ausgewählten Objekt entspricht, anzeigen (und bearbeiten).
   - **Migration problems** (Migrationsprobleme): Hier werden alle Probleme angezeigt, die während der Migration aufgetreten sind und überprüft werden können.
   - **Spaltenzuordnung:** Hiermit werden Spaltenzuordnungsinformationen angezeigt. Sie können diese Ansicht verwenden, um den Namen zu bearbeiten und die Spalte des Zielobjekts zu ändern.

2. Wählen Sie **Weiter** aus.

## <a name="create-the-target-database"></a>Erstellen der Zieldatenbank

1. Aktivieren Sie das Kontrollkästchen **Create schema in target RDBMS** (Schema in Ziel-RDBMS erstellen).

   Sie können auch vorhandene Schemas beibehalten, damit sie nicht geändert oder aktualisiert werden.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/create-target-database.png" alt-text="Seite „Target Creation Options“ (Zielerstellungsoptionen)":::

   In diesem Artikel wurde das Schema im Ziel-RDBMS erstellt, aber Sie können auch das Kontrollkästchen **Create a SQL script file** (SQL-Skriptdatei erstellen) aktivieren, um die Datei für andere Zwecke auf Ihrem lokalen Computer zu speichern.

2. Wählen Sie **Weiter** aus.

## <a name="run-the-mysql-script-to-create-the-database-objects"></a>Ausführen des MySQL-Skripts zum Erstellen der Datenbankobjekte

Da Sie das Schema im Ziel-RDBMS erstellen, wird das migrierte SQL-Skript auf dem MySQL-Zielserver ausgeführt. Sie können den Fortschritt wie im folgenden Screenshot dargestellt anzeigen:

:::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/create-schemas.png" alt-text="Seite „Create Schemas“ (Schemas erstellen)":::

1. Nachdem die Erstellung der Schemas und ihrer Objekte abgeschlossen ist, klicken Sie auf **Weiter**.

   Auf der Seite **Create Target Results** (Zielergebnisse erstellen) wird wie im folgenden Screenshot dargestellt eine Liste der erstellten Objekte und der Benachrichtigungen zu Fehlern angezeigt, die bei der Erstellung aufgetreten sind.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/create-target-results.png" alt-text="Seite „Create Target Results“ (Zielergebnisse erstellen)":::

2. Überprüfen Sie die Details auf dieser Seite, um zu überprüfen, ob alles wie beabsichtigt abgeschlossen wurde.

   Beim Beispiel in diesem Artikel treten keine Fehler auf. Wenn sie keine Fehlermeldungen beheben müssen, können Sie das Migrationsskript bearbeiten.

3. Wählen Sie im Feld **Objekt** das Objekt aus, das Sie bearbeiten möchten.
4. Ändern Sie unter **SQL CREATE Script for Selected Object** (SQL-Skript „CREATE“ für ausgewählte Objekte) das SQL-Skript, und klicken Sie dann auf **Anwenden**, um die Änderungen zu speichern.
5. Klicken Sie auf **Recreate Objects** (Objekte neu erstellen), um das Skript einschließlich Ihrer Änderungen auszuführen.

   Wenn bei der Ausführung des Skripts ein Fehler auftritt, müssen Sie möglicherweise das generierte Skript bearbeiten. Anschließend können Sie das SQL-Skript manuell korrigieren und alles noch mal ausführen. Im Beispiel in diesem Artikel werden keine Änderungen vorgenommen, weshalb das Skript unverändert bleibt.

6. Wählen Sie **Weiter** aus.

## <a name="transfer-data"></a>Übertragen von Daten

In diesem Teil des Vorgangs werden Daten aus der MySQL-Quellserverdatenbankinstanz in die neu erstellte MySQL-Zieldatenbankinstanz verschoben. Verwenden Sie die Seite **Data Transfer Setup** (Datenübertragungssetup), um diesen Vorgang zu konfigurieren.

:::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/data-transfer-setup.png" alt-text="Seite „Data Transfer Setup“ (Datenübertragungssetup)":::

Diese Seite enthält Optionen zum Einrichten der Datenübertragung. Für den Zweck dieses Artikels werden die Standardwerte übernommen.

1. Klicken Sie auf **Weiter**, um mit dem eigentlichen Vorgang zur Datenübertragung zu beginnen.

   Der Fortschritt des Datenübertragungsvorgangs wird wie im folgenden Screenshot dargestellt angezeigt.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/bulk-data-transfer.png" alt-text="Seite „Bulk Data Transfer“ (Massendatenübertragung)":::

   > [!NOTE]
   > Die Dauer des Datenübertragungsvorgangs hängt direkt mit der Größe der zu migrierenden Datenbank zusammen. Je größer die Quelldatenbank ist, desto länger dauert der Vorgang (bei größere Datenbanken möglicherweise mehrere Stunden).

2. Klicken Sie auf **Weiter**, wenn die Übertragung abgeschlossen wurde.

   Die Seite **Migrationsbericht** wird angezeigt und enthält wie im folgenden Screenshot dargestellt einen Bericht, der den gesamten Vorgang zusammenfasst:

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/migration-report.png" alt-text="Seite „Migration Progress Report“ (Bericht zum Migrationsfortschritt)":::

3. Klicken Sie auf **Fertig stellen**, um den Migrations-Assistenten zu schließen.

   Die Migration wurde nun erfolgreich abgeschlossen.

## <a name="verify-consistency-of-the-migrated-schemas-and-tables"></a>Überprüfen der Konsistenz der migrierten Schemas und Tabellen

1. Melden Sie sich als Nächstes bei Ihrer MySQL-Zieldatenbankinstanz an, um sicherzustellen, dass die migrierten Schemas und Tabellen mit Ihrer MySQL-Quelldatenbank übereinstimmen.

   In diesem Fall sehen Sie, dass alle Schemas (sakila, moda, items, customer, clothes, world und world_x) aus der Amazon RDS für MySQL-Datenbank **MyjolieDB** erfolgreich zur Azure Database for MySQL-Instanz **azmysql** migriert wurden.

2. Führen Sie die folgende Abfrage für beide Instanzen aus, um die Tabellen- und Zeilenanzahl zu überprüfen:

      `SELECT COUNT (*) FROM sakila.actor;`

   Im folgenden Screenshot sehen Sie, dass die Zeilenanzahl für Amazon RDS für MySQL 200 beträgt, was der Azure Database for MySQL-Instanz entspricht.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/table-row-size-source.png" alt-text="Tabellen- und Zeilengröße für die Quelldatenbank":::

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/table-row-size-target.png" alt-text="Tabellen- und Zeilengröße für die Zieldatenbank":::

   Sie können die obige Abfrage zwar für jedes einzelne Schema und jede Tabelle ausführen, dies erfordert jedoch einen hohen Aufwand, wenn Sie mit Hunderttausenden oder sogar Millionen von Tabellen arbeiten. Sie können die folgenden Abfragen verwenden, um stattdessen das Schema (Database) und die Tabellengröße zu überprüfen.

3. Führen Sie die folgende Abfrage aus, um die Datenbankgröße zu überprüfen:

    ```
   SELECT table_schema AS "Database", 
   ROUND(SUM(data_length + index_length) / 1024 / 1024, 2) AS "Size (MB)" 
   FROM information_schema.TABLES 
   GROUP BY table_schema;
   ```

4. Führen Sie die folgende Abfrage aus, um die Tabellengröße zu überprüfen:

   ```
   SELECT table_name AS "Table",
   ROUND(((data_length + index_length) / 1024 / 1024), 2) AS "Size (MB)"
   FROM information_schema.TABLES
   WHERE table_schema = "database_name"
   ORDER BY (data_length + index_length) DESC;
   ```

   In den folgenden Screenshots sehen Sie, dass die Schemagröße (Database) der Amazon RDS für MySQL-Quellinstanz mit der Größe der Azure Database for MySQL-Zielinstanz übereinstimmt.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/database-size-source.png" alt-text="Datenbankgröße für Zieldatenbank":::

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/database-size-target.png" alt-text="Datenbankgröße für Quelldatenbank":::

   Da die Schemagrößen (Database) in beiden Instanzen identisch sind, ist es nicht wirklich erforderlich, einzelne Tabellengrößen zu überprüfen. In jedem Fall können Sie die obige Abfrage immer verwenden, um die Tabellengrößen nach Bedarf zu überprüfen.

   Sie haben nun festgestellt, dass die Migration erfolgreich abgeschlossen wurde.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Migrieren von Datenbanken zu Azure Database for MySQL finden Sie im [Leitfaden zur Datenbankmigration](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide).
- Sehen Sie sich das Video [Easily migrate MySQL/PostgreSQL apps to Azure managed service](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201) (in englischer Sprache) an, das Ihnen anhand einer Demo erläutert, wie man MySQL-Anwendungen nach Azure Database for MySQL migriert.
