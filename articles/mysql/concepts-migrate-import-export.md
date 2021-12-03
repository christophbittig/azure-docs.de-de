---
title: Importieren und Exportieren – Azure Database for MySQL
description: In diesem Artikel werden gängige Möglichkeiten zum Importieren von Datenbanken in bzw. Exportieren von Datenbanken aus Azure-Datenbank für MySQL mithilfe von Tools wie MySQL Workbench erläutert.
author: savjani
ms.author: pariks
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: b26cfa516cc9080faac7d47c512b054e5909c1c6
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132345241"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>Migrieren der MySQL-Datenbank durch Import und Export

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

In diesem Artikel werden zwei gängige Vorgehensweisen beim Importieren von Daten zu bzw. Exportieren von Daten von einem Server für Azure-Datenbank für MySQL mithilfe von MySQL Workbench erläutert.

Ausführliche und umfassende Anleitungen für die Migration finden Sie in den [Ressourcen des Migrationsleitfadens](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide). 

Informationen zu anderen Migrationsszenarien finden Sie im [Leitfaden zur Datenbankmigration](https://datamigration.microsoft.com/).

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit dem Migrieren Ihrer MySQL-Datenbank zu SQL Server beginnen, führen Sie folgende Schritte aus:

- Erstellen Sie einen Server für [Azure Database for MySQL über das Azure-Portal](quickstart-create-mysql-server-database-using-azure-portal.md).
- Laden Sie [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) oder ein anderes MySQL-Tool von Drittanbietern zum Importieren und Exportieren herunter, und installieren Sie es.

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Erstellen einer Datenbank auf dem Server für Azure-Datenbank für MySQL

Erstellen Sie eine leere Datenbank auf dem Azure Database for MySQL-Server mithilfe von MySQL Workbench, Toad oder Navicat. Die Datenbank kann denselben Namen aufweisen wie die Datenbank, die die Sicherungsdaten enthält, Sie können aber auch eine Datenbank mit einem anderen Namen erstellen.

Gehen Sie wie folgt vor, um eine Verbindung herzustellen:

1. Suchen Sie im Azure-Portal im Bereich **Übersicht** Ihrer Azure Database for MySQL-Instanz nach den Verbindungsinformationen.

   :::image type="content" source="./media/concepts-migrate-import-export/1_server-overview-name-login.png" alt-text="Screenshot der Azure Database for MySQL-Serververbindungsinformationen im Azure-Portal.":::

1. Fügen Sie die Verbindungsinformationen in MySQL Workbench hinzu.

   :::image type="content" source="./media/concepts-migrate-import-export/2_setup-new-connection.png" alt-text="Screenshot der MySQL Workbench-Verbindungszeichenfolge.":::

## <a name="determine-when-to-use-import-and-export-techniques"></a>Bestimmen der Verwendung von Import- und Exportverfahren

> [!TIP]
> Für Szenarien, in denen Sie die gesamte Datenbank sichern und wiederherstellen möchten, verwenden Sie stattdessen den Ansatz [Sichern und Wiederherstellen](concepts-migrate-dump-restore.md).

Verwenden Sie in den folgenden Szenarien MySQL-Tools zum Importieren und Exportieren von Datenbanken in Ihre MySQL-Datenbank. Weitere Tools finden Sie im Abschnitt „Migrationsmethoden“ (Seite 22) des [Handbuchs zur Migration von MySQL zu Azure Database](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide). 

- Wenn Sie selektiv einige Tabellen zum Importieren aus einer vorhandenen MySQL-Datenbank in Ihre Azure MySQL Database-Instanz auswählen, empfiehlt es sich, das Import- und Exportverfahren zu nutzen. Damit können Sie nicht benötigte Tabellen aus der Migration ausschließen und auf diese Weise Zeit und Ressourcen sparen. Verwenden Sie beispielsweise die Option `--include-tables` oder `--exclude-tables` bei [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables) und die Option `--tables` bei [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables).
- Wenn Sie andere Datenbankobjekte als Tabellen verschieben, müssen Sie diese Objekte explizit erstellen. Schließen Sie Einschränkungen (Primärschlüssel, Fremdschlüssel und Indizes), Sichten, Funktionen, Prozeduren, Trigger und andere Datenbankobjekte ein, die migriert werden sollen.
- Erstellen Sie beim Migrieren von Daten aus anderen externen Datenquellen als einer MySQL-Datenbank Flatfiles, und importieren Sie diese mithilfe von [mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html).

> [!Important]
> Sowohl Einzelserver als auch flexible Server unterstützen nur die InnoDB-Speicher-Engine. Stellen Sie sicher, dass für alle Tabellen in der Datenbank die InnoDB-Speicher-Engine verwendet wird, wenn Sie Daten in Azure Database for MySQL laden.
>
> Wenn Ihre Quelldatenbank eine andere Speicher-Engine verwendet, wechseln Sie vor der Migration der Datenbank zur InnoDB-Engine. Angenommen, Sie verfügen über eine WordPress- oder Web-App, die die MyISAM-Engine verwendet. In diesem Fall müssen Sie erst die Tabellen konvertieren, indem Sie die Daten in InnoDB-Tabellen migrieren. Verwenden Sie die `ENGINE=INNODB`-Klausel, um die Engine festzulegen, mit der eine Tabelle erstellt wird, und übertragen Sie die Daten vor der Migration in die kompatible Tabelle.

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>Leistungsbezogene Empfehlungen für Import und Export

Um beim Importieren und Exportieren von Daten eine optimale Leistung zu erzielen, empfehlen wir Folgendes:

- Erstellen Sie vor dem Laden von Daten gruppierte Indizes und Primärschlüssel. Laden Sie die Daten in der Reihenfolge des Primärschlüssels.
- Warten Sie mit der Erstellung sekundärer Indizes, bis die Daten vollständig geladen wurden.
- Deaktivieren Sie Fremdschlüsseleinschränkungen, bevor Sie die Daten laden. Das Deaktivieren von Fremdschlüsselüberprüfungen führt zu erheblichen Leistungssteigerungen. Aktivieren Sie die Einschränkungen, und überprüfen Sie die Daten nach dem Laden, um die referentielle Integrität sicherzustellen.
- Laden Sie Daten parallel. Vermeiden Sie zu viel Parallelität, da dies zum Erreichen eines Ressourcenlimits führen kann, und überwachen Sie die Ressourcen mit den Metriken, die im Azure-Portal verfügbar sind.
- Verwenden Sie ggf. partitionierte Tabellen.

## <a name="import-and-export-data-by-using-mysql-workbench"></a>Importieren und Exportieren von Daten mit MySQL Workbench

Es gibt zwei Möglichkeiten zum Exportieren und Importieren von Daten in MySQL Workbench: über das Kontextmenü des Objektbrowsers oder über den Navigatorbereich. Jede Methode dient einem anderen Zweck.

> [!NOTE]
> Wenn Sie eine Verbindung zu einem MySQL-Einzelserver oder flexiblen Server in MySQL Workbench hinzufügen, sollten Sie wie folgt vorgehen:
>
> - Stellen Sie bei Verwendung eines MySQL-Einzelservers sicher, dass der Benutzername das Format *\<username@servername>* aufweist.
> - Verwenden Sie für einen flexiblen MySQL-Server nur *\<username>* . Wenn Sie *\<username@servername>* zur Verbindungsherstellung verwenden, tritt ein Fehler auf.

### <a name="run-the-table-data-export-and-import-wizards-from-the-object-browser-context-menu"></a>Ausführen der Assistenten zum Exportieren und Importieren von Tabellendaten über das Kontextmenü des Objektkatalogs

:::image type="content" source="./media/concepts-migrate-import-export/p1.png" alt-text="Screenshot der Befehle der Export- und Import-Assistenten von MySQL Workbench im Kontextmenü des Objektbrowsers.":::

Die Assistenten für Tabellendaten unterstützen Import- und Exportvorgänge unter Verwendung von CSV- und JSON-Dateien. Sie bieten eine Reihe von Konfigurationsoptionen, beispielsweise Trennzeichen, Spaltenauswahl und Codierungsauswahl. Sie können jeden Assistenten für lokal oder remote verbundene MySQL-Server ausführen. Die Importaktion schließt die Tabellen-, Spalten- und Typzuordnung ein.

Um über das Kontextmenü des Objektbrowsers auf diese Assistenten zuzugreifen, klicken Sie mit der rechten Maustaste auf eine Tabelle, und wählen Sie dann **Table Data Export Wizard** (Assistent zum Exportieren von Tabellendaten) bzw. **Table Data Import Wizard** (Assistent zum Importieren von Tabellendaten) aus.

#### <a name="the-table-data-export-wizard"></a>Assistent zum Exportieren von Tabellendaten

So exportieren Sie eine Tabelle in eine CSV-Datei

1. Klicken Sie mit der rechten Maustaste auf die Tabelle der zu exportierenden Datenbank.
1. Wählen Sie **Assistent zum Exportieren von Tabellendaten** aus. Wählen Sie die zu exportierenden Spalten, den Zeilenoffset (sofern vorhanden) und die Anzahl (sofern vorhanden) aus.
1. Klicken Sie im Bereich **Select data for export** (Daten für Export auswählen) auf **Weiter**. Wählen Sie den Dateipfad sowie den Dateityp CSV oder JSON aus. Wählen Sie darüber hinaus das Zeilentrennzeichen, die Methode zum Einschließen von Zeichenfolgen sowie das Feldtrennzeichen aus.
1. Klicken Sie im Bereich **Select output file location** (Speicherort der Ausgabedatei auswählen) auf **Weiter**.
1. Klicken Sie im Bereich **Export data** (Daten exportieren) auf **Weiter**.

#### <a name="the-table-data-import-wizard"></a>Assistent zum Importieren von Tabellendaten

So importieren Sie eine Tabelle aus einer CSV-Datei

1. Klicken Sie mit der rechten Maustaste auf die Tabelle der zu importierenden Datenbank.
1. Suchen Sie die zu importierende CSV-Datei, und wählen Sie sie aus. Klicken Sie anschließend auf **Weiter**.
1. Wählen Sie die Zieltabelle (neu oder vorhanden) aus, aktivieren oder deaktivieren Sie das Kontrollkästchen **Truncate table before import** (Tabelle vor Import abschneiden), und klicken Sie dann auf **Weiter**.
1. Wählen Sie die Codierung und die zu importierenden Spalten aus, und klicken Sie dann auf **Weiter**.
1. Klicken Sie im Bereich **Import data** (Daten importieren) auf **Weiter**. Der Assistent importiert die Daten.

### <a name="run-the-sql-data-export-and-import-wizards-from-the-navigator-pane"></a>Ausführen der Assistenten zum Exportieren und Importieren von SQL-Daten im Navigatorbereich

Verwenden Sie einen Assistenten zum Exportieren oder Importieren von SQL-Daten, die über MySQL Workbench oder den Befehl „mysqldump“ generiert wurden. Sie können über den Bereich **Navigator** auf die Assistenten zugreifen, oder Sie können im Hauptmenü **Server** auswählen.

#### <a name="export-data"></a>Exportieren von Daten

:::image type="content" source="./media/concepts-migrate-import-export/p2.png" alt-text="Screenshot der Verwendung des Navigatorbereichs zum Anzeigen des Bereichs „Data Export“ (Datenexport) in MySQL Workbench.":::

Sie können Ihre MySQL-Daten mithilfe des Bereichs **Data Export** (Datenexport) exportieren.

1. Wählen Sie in MySQL Workbench im Bereich **Navigator** die Option **Data Export** (Datenexport) aus.

1. Wählen Sie im Bereich **Data Export** (Datenexport) die einzelnen Schemas aus, die Sie exportieren möchten.
 
   Für jedes Schema können Sie bestimmte Schemaobjekte oder Tabellen für den Export auswählen. Zu den Konfigurationsoptionen zählen das Exportieren in einen Projektordner oder als eigenständige SQL-Datei, das Sichern gespeicherter Routinen und Ereignisse oder das Überspringen von Tabellendaten.

   Verwenden Sie alternativ die Option **Resultset exportieren**, um ein bestimmtes Resultset im SQL-Editor in einem anderen Format (z.B. CSV, JSON, HTML, XML) zu exportieren.

1. Wählen Sie die zu exportierenden Datenbankobjekte aus, und konfigurieren Sie die entsprechenden Optionen.
1. Klicken Sie auf **Refresh** (Aktualisieren), um die aktuellen Objekte zu laden.
1. Wählen Sie optional **Advanced Options** (Erweiterte Optionen) in der oberen rechten Ecke aus, um den Exportvorgang genauer zu definieren. Fügen Sie beispielsweise Tabellensperren hinzu, verwenden Sie `replace`- anstelle von `insert`-Anweisungen, und geben Sie Bezeichner mit Backtick-Zeichen an.
1. Klicken Sie auf **Start Export** (Export starten), um den Exportvorgang zu starten.


#### <a name="import-data"></a>Daten importieren

:::image type="content" source="./media/concepts-migrate-import-export/p3.png" alt-text="Screenshot der Verwendung des Navigatorbereichs zum Anzeigen des Bereichs „Datenimport“ in MySQL Workbench.":::

Über den Bereich **Data Import** (Datenimport) können Sie im Rahmen des Datenexportvorgangs oder mit dem Befehl „mysqldump“ exportierte Daten importieren oder wiederherstellen.

1. Wählen Sie in MySQL Workbench im Bereich **Navigator** die Option **Datenexport/-wiederherstellung** aus.
1. Wählen Sie den Projektordner oder die eigenständige SQL-Datei aus, wählen Sie das Zielschema für den Import aus, oder klicken Sie auf **Neu**, um ein neues Schema zu definieren.
1. Klicken Sie auf **Start Import** (Import starten), um den Importvorgang zu starten.

## <a name="next-steps"></a>Nächste Schritte

- Ein weiterer Migrationsansatz wird unter [Migrieren einer MySQL-Datenbank zu Azure Database for MySQL durch Sicherung und Wiederherstellung](concepts-migrate-dump-restore.md) beschrieben.
- Weitere Informationen zum Migrieren von Datenbanken zu Azure Database for MySQL finden Sie im [Leitfaden zur Datenbankmigration](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide).
