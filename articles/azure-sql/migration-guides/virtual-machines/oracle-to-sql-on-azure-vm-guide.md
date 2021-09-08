---
title: 'Oracle zu SQL Server auf Azure Virtual Machines: Migrationsleitfaden'
description: In diesem Leitfaden erfahren Sie, wie Sie Ihre Oracle-Schemata mithilfe von SQL Server Migration Assistant für Oracle zu SQL Server auf Azure Virtual Machines migrieren.
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: cawrites
ms.date: 11/06/2020
ms.openlocfilehash: 94239de790a23d2b300e52567fd456a012217928
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355230"
---
# <a name="migration-guide-oracle-to-sql-server-on-azure-virtual-machines"></a>Migrationsleitfaden: Oracle zu SQL Server auf Azure Virtual Machines
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

In diesem Leitfaden erfahren Sie, wie Sie Ihre Oracle-Schemata mithilfe von SQL Server Migration Assistant für Oracle zu SQL Server auf Azure Virtual Machines migrieren.

Weitere Migrationsleitfäden finden Sie im [Leitfaden zur Azure-Datenbankmigration](/data-migration).

## <a name="prerequisites"></a>Voraussetzungen

Für die Migration Ihres Oracle-Schemas zu SQL Server auf Azure Virtual Machines sind folgende Schritte bzw. Voraussetzungen erforderlich:

- Eine unterstützte Quellumgebung.
- [SQL Server Migration Assistant (SSMA) für Oracle](https://www.microsoft.com/download/details.aspx?id=54258)
- Eine [SQL Server-Ziel-VM](../../virtual-machines/windows/sql-vm-create-portal-quickstart.md).
- Richten Sie die [erforderlichen Berechtigungen für SSMA für Oracle](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) und den [Anbieter](/sql/ssma/oracle/connect-to-oracle-oracletosql) ein.
- Konnektivität und ausreichende Berechtigungen für den Zugriff auf die Quelle und das Ziel.


## <a name="pre-migration"></a>Vor der Migration

Überprüfen Sie bei der Vorbereitung Ihrer Migration zur Cloud, ob Ihre Quellumgebung unterstützt wird und alle Voraussetzungen erfüllt sind. Dadurch wird eine effiziente und erfolgreiche Migration sichergestellt.

Dieser Teil des Prozesses umfasst Folgendes:
- Durchführen einer Inventur für die Datenbanken, die Sie migrieren müssen.
- Bewerten dieser Datenbanken auf mögliche Migrationsprobleme oder -blockierungen.
- Beheben aller Probleme, die Sie aufdecken.

### <a name="discover"></a>Entdecken

Verwenden Sie das [MAP Toolkit](https://go.microsoft.com/fwlink/?LinkID=316883), um vorhandene Datenquellen und Details zu den von Ihrem Unternehmen verwendeten Features zu identifizieren. Dadurch erhalten Sie ein besseres Verständnis für die Migration und können diese besser planen. Bei diesem Vorgang wird das Netzwerk überprüft, um die Oracle-Instanzen Ihrer Organisation sowie die verwendeten Versionen und Features zu identifizieren.

Gehen Sie folgendermaßen vor, um mit dem MAP Toolkit eine Bestandsprüfung durchzuführen:


1. Öffnen Sie das [MAP Toolkit](https://go.microsoft.com/fwlink/?LinkID=316883).


1. Wählen Sie **Datenbank erstellen/auswählen** aus:

   ![Screenshot: Option „Datenbank erstellen/auswählen“](./media/oracle-to-sql-on-azure-vm-guide/select-database.png)

1. Wählen Sie **Bestandsdatenbank erstellen** aus. Geben Sie den Namen für die neue Bestandsdatenbank und eine kurze Beschreibung ein, und wählen Sie dann **OK** aus.

   :::image type="content" source="media/oracle-to-sql-on-azure-vm-guide/create-inventory-database.png" alt-text="Screenshot: Schnittstelle zum Erstellen einer Bestandsdatenbank":::

1. Wählen Sie **Inventardaten sammeln** aus, um den **Assistenten für Inventar und Bewertung** zu öffnen:

   :::image type="content" source="media/oracle-to-sql-on-azure-vm-guide/collect-inventory-data.png" alt-text="Screenshot: Link zum Erfassen von Bestandsdaten":::


1. Wählen Sie im **Assistenten für Inventar und Bewertung** die Option **Oracle** und dann **Weiter** aus:

   ![Screenshot: Seite für Bestandsszenarien des Assistenten für Inventar und Bewertung](./media/oracle-to-sql-on-azure-vm-guide/choose-oracle.png)

1. Wählen Sie die Option für die Computersuche aus, die sich am besten für Ihre Geschäftsanforderungen und Ihre Umgebung eignet, und wählen Sie dann **Weiter** aus:

   ![Screenshot: Seite für Ermittlungsmethoden des Assistenten für Inventar und Bewertung](./media/oracle-to-sql-on-azure-vm-guide/choose-search-option.png)

1. Geben Sie Anmeldeinformationen für die Systeme ein, die untersucht werden sollen, oder erstellen Sie neue Anmeldeinformationen, und wählen Sie dann **Weiter** aus:

    ![Screenshot: Seite „Alle Computeranmeldeinformationen“ des Assistenten für Inventar und Bewertung](./media/oracle-to-sql-on-azure-vm-guide/choose-credentials.png)


1. Legen Sie die Reihenfolge der Anmeldeinformationen fest, und wählen Sie dann **Weiter** aus: 

   ![Screenshot: Seite für die Reihenfolge der Anmeldeinformationen des Assistenten für Inventar und Bewertung](./media/oracle-to-sql-on-azure-vm-guide/set-credential-order.png)  


1. Geben Sie die Anmeldeinformationen für die einzelnen Computer ein, die ermittelt werden sollen. Sie können individuelle Anmeldeinformationen für jeden Computer bzw. jede VM oder die Liste aller Computeranmeldeinformationen auswählen.  

   ![Screenshot: Seite „Computer und Anmeldeinformationen angeben“ des Assistenten für Inventar und Bewertung](./media/oracle-to-sql-on-azure-vm-guide/specify-credentials-for-each-computer.png)


1. Überprüfen Sie Ihre Auswahl, und wählen Sie dann **Fertig** aus:

   ![Screenshot: Seite „Zusammenfassung“ des Assistenten für Inventar und Bewertung](./media/oracle-to-sql-on-azure-vm-guide/review-summary.png)


1. Nachdem die Überprüfung abgeschlossen ist, zeigen Sie die Zusammenfassung **Datensammlung** an. Die Überprüfung kann je nach Anzahl der Datenbanken einige Minuten dauern. Wählen Sie **Schließen** aus, wenn Sie fertig sind:

   ![Screenshot: Zusammenfassung der Datensammlung](./media/oracle-to-sql-on-azure-vm-guide/collection-summary-report.png)


1. Wählen Sie **Optionen** aus, um einen Bericht zur Oracle-Bewertung und zu den Datenbankdetails zu generieren. Wählen Sie beide Optionen aus, eine nach der anderen, um den Bericht zu erstellen.


### <a name="assess"></a>Bewerten

Nachdem Sie die Datenquellen identifiziert haben, verwenden Sie [SQL Server Migration Assistant für Oracle](https://www.microsoft.com/download/details.aspx?id=54258), um die Oracle-Instanzen zu bewerten, die zur SQL Server-VM migriert werden. Der Assistent hilft Ihnen, die Lücken zwischen der Quell- und Zieldatenbank zu verstehen. Sie können Datenbankobjekte und Daten überprüfen, Datenbanken für die Migration bewerten und Datenbankobjekte bzw. anschließend Daten zu SQL Server migrieren.

Führen Sie die folgenden Schritte aus, um eine Bewertung zu erstellen:


1. Öffnen Sie [SQL Server Migration Assistant für Oracle](https://www.microsoft.com/download/details.aspx?id=54258).
1. Wählen Sie im Menü **Datei** die Option **Neues Projekt** aus.
1. Geben Sie einen Projektnamen und einen Speicherort für das Projekt an, und wählen Sie dann in der Liste ein SQL Server-Migrationsziel aus. Wählen Sie **OK** aus:

   ![Screenshot: Dialogfeld „Neues Projekt“](./media/oracle-to-sql-on-azure-vm-guide/new-project.png)


1. Wählen Sie **Verbindung mit Oracle herstellen** aus. Geben Sie im Dialogfeld **Verbindung mit Oracle herstellen** Werte für die Oracle-Verbindung ein:

   ![Screenshot: Dialogfeld „Verbindung mit Oracle herstellen“](./media/oracle-to-sql-on-azure-vm-guide/connect-to-oracle.png)

   Wählen Sie die Oracle-Schemas aus, die migriert werden sollen:

   ![Screenshot: Liste der Oracle-Schemas, die migriert werden können](./media/oracle-to-sql-on-azure-vm-guide/select-schema.png)


1. Klicken Sie im **Metadaten-Explorer von Oracle** mit der rechten Maustaste auf das zu migrierende Oracle-Schema, und wählen Sie dann **Bericht erstellen** aus. Dadurch wird ein HTML-Bericht generiert. Alternativ können Sie die Datenbank auswählen und dann **Bericht erstellen** im oberen Menü auswählen.

   ![Screenshot: Erstellung eines Berichts](./media/oracle-to-sql-on-azure-vm-guide/create-report.png)

1. Überprüfen Sie den HTML-Bericht für Konvertierungsstatistiken, Fehler und Warnungen. Analysieren Sie diese Informationen, um Konvertierungsprobleme und mögliche Lösungen zu bestimmen.

    Sie können den Bericht auch in Excel öffnen, um ein Inventar der Oracle-Objekte sowie Informationen zum Aufwand für Schemakonvertierungen zu erhalten. Der Standardspeicherort für den Bericht ist der Berichtsordner in SSMAProjects.

   Beispiel: `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2016_11_12T02_47_55\`


   ![Screenshot: Konvertierungsbericht](./media/oracle-to-sql-on-azure-vm-guide/conversion-report.png)


### <a name="validate-data-types"></a>Überprüfen von Datentypen

Überprüfen Sie die standardmäßig festgelegten Datentypzuordnungen, und ändern Sie sie bei Bedarf basierend auf den Anforderungen. Gehen Sie dazu folgendermaßen vor:


1. Wählen Sie im Menü **Tools** die Option **Projekteinstellungen** aus.
1. Klicken Sie auf die Registerkarte **Typzuordnungen**.

   ![Screenshot: Registerkarte „Typzuordnungen“](./media/oracle-to-sql-on-azure-vm-guide/type-mappings.png)

1. Sie können die Typzuordnung für jede Tabelle ändern, indem Sie die Tabelle im **Metadaten-Explorer von Oracle** auswählen.

### <a name="convert-the-schema"></a>Konvertieren des Schemas

Führen Sie die folgenden Schritte aus, um das Schema zu konvertieren:

1. Optional: Zum Konvertieren von dynamischen Abfragen oder Ad-hoc-Abfragen klicken Sie mit der rechten Maustaste auf den Knoten, und wählen Sie **Anweisung hinzufügen** aus.

1. Wählen Sie **Verbindung mit SQL Server herstellen** im oberen Menü aus.
     1. Geben Sie die Verbindungsdetails für Ihren SQL Server auf dem virtuellen Azure-Computer ein.
     1. Wählen Sie Ihre Zieldatenbank aus der Liste aus, oder geben Sie einen neuen Namen ein. Wenn Sie einen neuen Namen angeben, wird eine Datenbank auf dem Zielserver erstellt.
     1. Geben Sie Details zur Authentifizierung an.
     1. Wählen Sie **Verbinden** aus.


   ![Screenshot: Herstellen einer Verbindung mit SQL Server](./media/oracle-to-sql-on-azure-vm-guide/connect-to-sql-vm.png)

1. Klicken Sie im **Metadaten-Explorer von Oracle** mit der rechten Maustaste auf das Oracle-Schema, und klicken Sie dann auf **Schema konvertieren**. Sie können auch **Schema konvertieren** im oberen Menü auswählen:

   ![Screenshot: Konvertierung des Schemas](./media/oracle-to-sql-on-azure-vm-guide/convert-schema.png)


1. Nachdem die Schemakonvertierung abgeschlossen ist, überprüfen Sie die konvertierten Objekte und vergleichen Sie sie mit den ursprünglichen Objekten, um mögliche Probleme zu identifizieren. Verwenden Sie die Empfehlungen, um eventuelle Probleme zu beheben:

   ![Screenshot: Vergleich zweier Schemas](./media/oracle-to-sql-on-azure-vm-guide/table-mapping.png)

   Vergleichen Sie den konvertierten Transact-SQL-Text mit den ursprünglichen gespeicherten Prozeduren, und prüfen Sie die Empfehlungen:

   ![Screenshot: Transact-SQL, gespeicherte Prozeduren und eine Warnung](./media/oracle-to-sql-on-azure-vm-guide/procedure-comparison.png)

   Sie können das Projekt lokal speichern, um offline eine Problembehandlung für das Schema durchzuführen. Wählen Sie dazu **Projekt speichern** im Menü **Datei** aus. Wenn Sie das Projekt lokal speichern, können Sie die Quell- und Zielschemas offline bewerten und die Problembehebung durchführen, bevor Sie das Schema auf SQL Server veröffentlichen.

1. Wählen Sie im Bereich **Ausgabe** die Option **Ergebnisse überprüfen** aus, und überprüfen Sie dann die Fehler im Bereich **Fehlerliste**.
1. Speichern Sie das Projekt für eine Übung zur Offlineschemakorrektur lokal. Wählen Sie **Projekt speichern** im Menü **Datei** aus. So können Sie das Quell- und Zielschema offline auswerten und Probleme behandeln, bevor Sie das Schema für SQL Server auf Azure Virtual Machines veröffentlichen.


## <a name="migrate"></a>Migrate

Wenn die Voraussetzungen erfüllt sind und Sie die Aufgaben vor der Migration ausgeführt haben, können Sie die Schema- und Datenmigration starten. Die Migration umfasst zwei Schritte: das Veröffentlichen des Schemas und das Migrieren der Daten.


Führen Sie die folgenden Schritte aus, um das Schema zu veröffentlichen und die Daten zu migrieren:

1. Veröffentlichen des Schemas: Klicken Sie im **Metadaten-Explorer für SQL Server** mit der rechten Maustaste auf die Datenbank, und wählen Sie **Mit Datenbank synchronisieren** aus. Dadurch wird das Oracle-Schema für SQL Server auf Azure Virtual Machines veröffentlicht.

   ![Screenshot: Befehl „Mit Datenbank synchronisieren“](./media/oracle-to-sql-on-azure-vm-guide/synchronize-database.png)

   Überprüfen Sie die Zuordnung zwischen dem Quellprojekt und dem Ziel:

   ![Screenshot: Synchronisierungsstatus](./media/oracle-to-sql-on-azure-vm-guide/synchronize-database-review.png)



1. Migrieren der Daten: Klicken Sie mit der rechten Maustaste auf die zu migrierende Datenbank oder das zu migrierende Objekt im **Metadaten-Explorer für Oracle**, und wählen Sie **Daten migrieren** aus. Oder Sie können die Registerkarte **Daten migrieren** auswählen. Aktivieren Sie das Kontrollkästchen neben dem Datenbanknamen, um Daten für eine gesamte Datenbank zu migrieren. Wenn Sie Daten aus einzelnen Tabellen migrieren möchten, erweitern Sie die Datenbank, erweitern Sie **Tabellen**, und aktivieren Sie dann das Kontrollkästchen neben der gewünschten Tabelle. Deaktivieren Sie die Kontrollkästchen, um Daten aus einzelnen Tabellen auszulassen.

   ![Screenshot: Befehl „Daten migrieren“](./media/oracle-to-sql-on-azure-vm-guide/migrate-data.png)

1. Geben Sie im Dialogfeld die Verbindungsdetails für Oracle und SQL Server auf Azure Virtual Machines ein.
1. Zeigen Sie nach Abschluss der Migration den **Bericht zur Datenmigration** an:

    ![Screenshot: Bericht zur Datenmigration](./media/oracle-to-sql-on-azure-vm-guide/data-migration-report.png)

1. Stellen Sie mit Ihrer Instanz von SQL Server auf Azure Virtual Machines eine Verbindung her, indem Sie [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) verwenden. Validieren Sie die Migration, indem Sie die Daten und das Schema überprüfen:


   ![Screenshot: SQL Server-Instanz in SSMA](./media/oracle-to-sql-on-azure-vm-guide/validate-in-ssms.png)

Anstatt SSMA zu verwenden, könnten Sie die SQL Server Integration Services (SSIS) verwenden, um die Daten zu migrieren. Weitere Informationen finden Sie unter:
- [SQL Server Integration Services](/sql/integration-services/sql-server-integration-services).
- [SSIS für Azure und Hybriddatenverschiebung](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx).


## <a name="post-migration"></a>Nach der Migration

Nachdem Sie die Migrationsphase abgeschlossen haben, müssen Sie eine Reihe von Aufgaben nach der Migration erledigen, um einen möglichst reibungslosen und effizienten Ablauf sicherzustellen.

### <a name="remediate-applications"></a>Korrigieren von Anwendungen

Nachdem die Daten in die Zielumgebung migriert wurden, müssen alle Anwendungen, die zuvor die Quelle verwendet haben, mit der Verwendung der Zielumgebung beginnen. Die Durchführung dieser Änderungen kann Änderungen an den Anwendungen erfordern.

Das [Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) ist eine Erweiterung für Visual Studio Code. Es ermöglicht Ihnen, Ihren Java-Quellcode zu analysieren und API-Aufrufe und -Abfragen für den Datenzugriff zu erkennen. Das Toolkit zeigt auf einen Blick, was für die Unterstützung des neuen Datenbank-Back-Ends erforderlich ist. Weitere Informationen finden Sie unter [Migrieren einer Java-Anwendung von Oracle](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727).

### <a name="perform-tests"></a>Durchführen von Tests

Führen Sie die folgenden Aktionen aus, um Ihre Datenbankmigration zu testen:

1. **Entwickeln der Validierungstests**. Für das Testen der Datenbankmigration müssen Sie SQL-Abfragen verwenden. Erstellen Sie die Validierungsabfragen, die für die Quell- und die Zieldatenbanken ausgeführt werden sollen. Ihre Validierungsabfragen sollten den von Ihnen definierten Bereich abdecken.

2. **Einrichten einer Testumgebung**. Die Testumgebung sollte eine Kopie der Quelldatenbank und der Zieldatenbank enthalten. Stellen Sie sicher, dass Sie die Testumgebung isolieren.

3. **Ausführen der Validierungstests**. Führen Sie die Validierungstests für die Quelle und das Ziel aus, und analysieren Sie anschließend die Ergebnisse.

4. **Ausführen der Leistungstests**. Führen Sie Leistungstests für die Quelle und das Ziel aus, und analysieren und vergleichen Sie anschließend die Ergebnisse.

### <a name="validate-migrated-objects"></a>Überprüfen migrierter Objekte

Microsoft SQL Server Migration Assistant for Oracle Tester (SSMA-Tester) ermöglicht Ihnen das Testen migrierter Datenbankobjekte. Der SSMA-Tester wird verwendet, um zu überprüfen, ob sich konvertierte Objekte auf die gleiche Weise verhalten.

#### <a name="create-test-case"></a>Testfall erstellen

1. Öffnen Sie SSMA für Oracle, und wählen Sie erst **Tester** und dann **Neuer Testfall** aus.

   :::image type="content" source="./media/oracle-to-sql-on-azure-vm-guide/ssma-tester-new.png" alt-text="Screenshot: Neuer Testfall":::

1. Geben Sie im Testfall-Assistenten die folgenden Informationen an:

   **Name**: Geben Sie den Namen ein, um den Testfall zu identifizieren.

   **Erstellungsdatum**:Das heutige aktuelle Datum, das automatisch festgelegt wird.

   **Datum der letzten Änderung**: Wird automatisch ausgefüllt und darf nicht geändert werden.

   **Beschreibung**: Geben Sie zusätzliche Informationen ein, um den Zweck des Testfalls zu bestimmen.

   :::image type="content" source="./media/oracle-to-sql-on-azure-vm-guide/tester-init-test-case.png" alt-text="Screenshot: Schritte zum Initialisieren eines Testfalls":::

1. Wählen Sie in der Oracle-Objektstruktur auf der linken Seite die Objekte aus, die zum Testfall gehören.

   :::image type="content" source="./media/oracle-to-sql-on-azure-vm-guide/tester-select-configure-objects.png" alt-text="Screenshot: Schritt zum Auswählen und Konfigurieren des Objekts":::

   In diesem Beispiel sind die gespeicherte Prozedur `ADD_REGION` und Tabelle `REGION` ausgewählt.

   Weitere Informationen finden Sie unter [Auswählen und Konfigurieren von zu testenden Objekten](/sql/ssma/oracle/selecting-and-configuring-objects-to-test-oracletosql).

1. Wählen Sie als nächstes im linken Fenster in der Oracle-Objektstruktur die Tabellen, Fremdschlüssel und andere abhängige Objekte aus.

   :::image type="content" source="./media/oracle-to-sql-on-azure-vm-guide/tester-select-configure-affected.png" alt-text="Screenshot: Schritt zum Auswählen und Konfigurieren des betroffenen Objekts":::

   Weitere Informationen finden Sie unter [Auswählen und Konfigurieren von betroffenen Objekten](/sql/ssma/oracle/selecting-and-configuring-affected-objects-oracletosql).

1. Überprüfen Sie die Auswertungssequenz von Objekten. Ändern Sie die Reihenfolge, indem Sie im Raster auf die Schaltflächen klicken.

   :::image type="content" source="./media/oracle-to-sql-on-azure-vm-guide/test-call-ordering.png" alt-text="Screenshot: Schritt zum Ablauf der Testobjektausführung":::

1. Schließen Sie den Testfall ab, indem Sie die in den vorherigen Schritten bereitgestellten Informationen überprüfen. Konfigurieren Sie die Testausführungsoptionen basierend auf dem Testszenario.

   :::image type="content" source="./media/oracle-to-sql-on-azure-vm-guide/tester-finalize-case.png" alt-text="Screenshot: Schritt zum Abschließen des Objekts":::

   Weitere Informationen zu Testfalleinstellungen finden Sie unter [Beenden der Vorbereitung von Testfällen](/sql/ssma/oracle/finishing-test-case-preparation-oracletosql).

1. Klicken Sie auf „Fertig stellen“, um den Testfall zu erstellen.

   :::image type="content" source="./media/oracle-to-sql-on-azure-vm-guide/tester-test-repo.png" alt-text="Screenshot: Schritt zum Testen des Repositorys":::

#### <a name="run-test-case"></a>Ausführen des Testfalls

Wenn der SSMA-Tester einen Testfall ausführt, führt die Test-Engine die zu Testzwecken ausgewählten Objekte aus und generiert einen Überprüfungsbericht.

1. Wählen Sie im Testrepository den Testfall aus, und klicken Sie dann auf „Ausführen“.
   :::image type="content" source="./media/oracle-to-sql-on-azure-vm-guide/tester-repo-run.png" alt-text="Screenshot: Überprüfen des Testrepositorys":::

1. Überprüfen Sie den Starttestfall, und klicken Sie auf „Ausführen“.

   :::image type="content" source="./media/oracle-to-sql-on-azure-vm-guide/tester-run-test-case.png" alt-text="Screenshot: Schritt zum Starten des Testfalls":::

1. Geben Sie als nächstes Anmeldeinformationen für die Oracle-Quelle an. Klicken Sie nach Eingabe der Anmeldeinformationen auf „Verbinden“.

   :::image type="content" source="./media/oracle-to-sql-on-azure-vm-guide/tester-oracle-connect.png" alt-text="Screenshot: Schritt zum Herstellen einer Verbindung mit der Oracle-Quelle":::

1. Geben Sie die SQL Server-Anmeldeinformationen an, und klicken Sie auf „Verbinden“.

   :::image type="content" source="./media/oracle-to-sql-on-azure-vm-guide/tester-sqlservervm-connect.png" alt-text="Screenshot: Schritt zum Herstellen einer Verbindung mit dem SQL Server-Ziel":::

   Bei Erfolg wechselt der Testfall in die Initialisierungsphase.

1. Eine Statusleiste in Echtzeit zeigt den Ausführungsstatus des Testlaufs.

   :::image type="content" source="./media/oracle-to-sql-on-azure-vm-guide/tester-run-status.png" alt-text="Screenshot: Testfortschritt des Testers":::

1. Überprüfen Sie den Bericht, nachdem der Test abgeschlossen wurde. Der Bericht enthält die Statistiken, alle Fehler während des Testlaufs und einen Detailbericht.

   :::image type="content" source="./media/oracle-to-sql-on-azure-vm-guide/tester-test-result.png" alt-text="Screenshot: Beispieltestbericht für Tester":::

7. Klicken Sie auf „Details“, um weitere Informationen zu erhalten.

   Beispiel einer erfolgreichen Datenüberprüfung.
   :::image type="content" source="./media/oracle-to-sql-on-azure-vm-guide/tester-test-success.png" alt-text="Screenshot: Erfolgsbericht eines Beispieltests":::

   Beispiel einer fehlgeschlagenen Datenüberprüfung.
   :::image type="content" source="./media/oracle-to-sql-on-azure-vm-guide/tester-test-failed.png" alt-text="Screenshot: Fehlerbericht des Testers":::

### <a name="optimize"></a>Optimieren

Die Phase nach der Migration ist entscheidend für den Abgleich von Problemen mit der Datengenauigkeit und die Überprüfung der Vollständigkeit. Sie ist auch entscheidend für die Behandlung von Leistungsproblemen mit der Workload.

> [!Note]
> Weitere Informationen zu diesen Problemen und spezifische Schritte zu ihrer Minimierung finden Sie im [Leitfaden zur Überprüfung und Optimierung nach der Migration](/sql/relational-databases/post-migration-validation-and-optimization-guide).


## <a name="migration-resources"></a>Migrationsressourcen

Weitere Hilfe beim Abschließen dieses Migrationsszenarios finden Sie in den folgenden Ressourcen, die zur Unterstützung eines realen Migrationsprojekts entwickelt wurden.

| **Titel/Link**                                                                                                                                          | **Beschreibung**                                                                                                                                                                                                                                                                                                   |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Data Workload Assessment Model and Tool](https://www.microsoft.com/download/details.aspx?id=103130) | Dieses Tool stellt für eine bestimmte Arbeitsauslastung Informationen zu empfohlenen optimalen Zielplattformen, zur Cloudbereitschaft und zum Korrekturbedarf für Anwendungen/Datenbanken bereit. Es bietet eine einfache Berechnung und Berichterstellung mit nur einem Klick, die Ihnen durch einen automatisierten und einheitlichen Entscheidungsprozess für die Zielplattform dabei helfen, Bewertungen von umfangreichen Datenbeständen zu beschleunigen.                                                          |
| [Oracle Inventory Script Artifacts](https://www.microsoft.com/download/details.aspx?id=103121)                 | Diese Ressource umfasst eine auf Oracle-Systemtabellen ausgerichtete PL/SQL-Abfrage, die die Anzahl der Objekte nach Schematyp, Objekttyp und Status bereitstellt. Darüber hinaus erhalten Sie eine grobe Schätzung zu den „Rohdaten“ sowie zur Größe der Tabellen in jedem Schema. Die Ergebnisse werden im CSV-Format gespeichert.                                                                                                               |
| [Automate SSMA Oracle Assessment Collection & Consolidation](https://www.microsoft.com/download/details.aspx?id=103120)                                             | Diese Ressourcensammlung verwendet eine CSV-Datei als Eingabe („sources.csv“ in den Projektordnern), um die XML-Dateien zu erstellen, die zum Ausführen der SSMA-Bewertung im Konsolenmodus benötigt werden. Sie stellen die Datei „source.csv“ bereit, indem Sie eine Inventur der vorhandenen Oracle-Instanzen durchführen. Die Ausgabedateien sind „AssessmentReportGeneration_source_1.xml“, „ServersConnectionFile.xml“ und „VariableValueFile.xml“.|
| [SSMA-Probleme und mögliche Abhilfemaßnahmen beim Migrieren von Oracle-Datenbanken](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | In Oracle kann eine nicht skalare Bedingung in einer WHERE-Klausel zugewiesen werden. Dieser Bedingungstyp wird von SQL Server nicht unterstützt. SSMA für Oracle konvertiert also keine Abfragen, die eine nicht skalare Bedingung in der WHERE-Klausel aufweisen. Stattdessen wird ein Fehler generiert: O2SS0001. In diesem Whitepaper finden Sie Einzelheiten zu diesem Problem sowie die erforderlichen Schritte zur Problembehandlung.          |
| [Handbuch für die Migration von Oracle zu SQL Server](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | In diesem Whitepaper sind die Aufgaben bei der Migration eines Oracle-Schemas zur neuesten Version von SQL Server beschrieben. Wenn für die Migration Änderungen an Features/Funktionen erforderlich sind, müssen Sie die möglichen Auswirkungen jeder Änderung auf die Anwendungen, die die Datenbank verwenden, sorgfältig berücksichtigen.                                                     |
|[Oracle zu SQL Server – Hilfsprogramm „Database Compare“](https://www.microsoft.com/download/details.aspx?id=103016)|SSMA für Oracle Tester ist das empfohlene Tool, um automatisch die Umwandlung von Datenbankobjekten und Datenmigration zu überprüfen. Außerdem enthält es eine Obermenge der Database Compare-Funktionen.<br /><br />Wenn Sie nach einer alternativen Datenüberprüfungsoption suchen, können Sie Daten mithilfe des Hilfsprogramms „Database Compare“ in ausgewählten oder allen Tabellen, Zeilen und Spalten bis auf Zeilen- oder Spaltenebene vergleichen.|


Das Data SQL Engineering-Team hat diese Ressourcen entwickelt. Die Hauptaufgabe dieses Teams ist die Aufhebung der Blockierung und Beschleunigung komplexer Modernisierungsprojekte für die Migration von Datenplattformen zur Microsoft Azure-Datenplattform.


## <a name="next-steps"></a>Nächste Schritte

- Die Verfügbarkeit der mit SQL Server kompatiblen Dienste finden Sie auf der Seite für die [globale Azure-Infrastruktur](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database).

- Eine Übersicht über die Dienste und Tools von Microsoft und Drittanbietern, mit denen Sie verschiedene Datenbank- und Datenmigrationsszenarios sowie Spezialaufgaben ausführen können, finden Sie unter [Dienste und Tools für die Datenmigration](../../../dms/dms-tools-matrix.md).

- Hier finden Sie weitere Informationen zu Azure SQL:
   - [Bereitstellungsoptionen](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [SQL Server auf virtuellen Azure-Computern](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Azure-Gesamtkostenrechner](https://azure.microsoft.com/pricing/tco/calculator/)


- Weitere Informationen zum Framework und zum Einführungszyklus von Cloudmigrationen finden Sie unter:
   -  [Cloud Adoption Framework für Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Bewährte Methoden für Kostenermittlung und Größenanpassung von zu Azure migrierten Workloads](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Hier finden Sie weitere Informationen zur Lizenzierung:
   - [Bring-Your-Own-License mit dem Azure-Hybridvorteil](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [Erweiterter Support für SQL Server 2008 und SQL Server 2008 R2](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)

- Für die Bewertung der Anwendungszugriffsebene verwenden Sie das [Data Access Migration Toolkit (Vorschauversion)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Ausführliche Informationen zum Ausführen von A/B-Tests auf Datenzugriffsebene finden Sie unter [Übersicht über den Assistenten für Datenbankexperimente](/sql/dea/database-experimentation-assistant-overview).
