---
title: Kopieren lokaler Daten mithilfe des Azure-Tools zum Kopieren von Daten
description: Erstellen Sie eine Azure Data Factory-Instanz, und verwenden Sie dann das Tool zum Kopieren von Daten, um Daten aus einer SQL Server-Datenbank in Azure Blob Storage zu kopieren.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.subservice: tutorials
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 07/08/2021
ms.openlocfilehash: 3adb1c084276bed35ec7a2f542ee7f9d319f7b2b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122638322"
---
# <a name="copy-data-from-a-sql-server-database-to-azure-blob-storage-by-using-the-copy-data-tool"></a>Kopieren von Daten aus einer SQL Server-Datenbank in Azure Blob Storage mithilfe des Tools zum Kopieren von Daten
> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuelle Version](tutorial-hybrid-copy-data-tool.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Tutorial verwenden Sie das Azure-Portal, um eine Data Factory zu erstellen. Anschließend erstellen Sie mithilfe des Tools zum Kopieren von Daten eine Pipeline, die Daten aus einer SQL Server-Datenbank in Azure Blob Storage kopiert.

> [!NOTE]
> - Falls Sie noch nicht mit Azure Data Factory vertraut sind, ist es ratsam, den Artikel [Einführung in Azure Data Factory](introduction.md) zu lesen.

In diesem Tutorial führen Sie die folgenden Schritte aus:

> [!div class="checklist"]
> * Erstellen einer Data Factory.
> * Erstellen einer Pipeline mithilfe des Tools zum Kopieren von Daten
> * Überwachen der Pipeline- und Aktivitätsausführungen.

## <a name="prerequisites"></a>Voraussetzungen
### <a name="azure-subscription"></a>Azure-Abonnement
Wenn Sie nicht bereits ein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

### <a name="azure-roles"></a>Azure-Rollen
Damit Sie Data Factory-Instanzen erstellen können, muss dem Benutzerkonto, mit dem Sie sich bei Azure anmelden, die Rolle *Mitwirkender* oder *Besitzer* zugewiesen sein, oder es muss ein *Administrator* des Azure-Abonnements sein.

Die Berechtigungen, über die Sie im Abonnement verfügen, können Sie im Azure-Portal einsehen. Wählen Sie oben rechts Ihren Benutzernamen und dann **Berechtigungen** aus. Wenn Sie Zugriff auf mehrere Abonnements besitzen, wählen Sie das entsprechende Abonnement aus. Beispielanleitungen zum Hinzufügen eines Benutzers zu einer Rolle finden Sie im Artikel [Hinzufügen von Azure-Rollenzuweisungen über das Azure-Portal](../role-based-access-control/role-assignments-portal.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 und 2017
In diesem Tutorial verwenden Sie eine SQL Server-Datenbank als *Quelldatenspeicher*. Die Pipeline in der in diesem Tutorial erstellten Data Factory kopiert Daten aus dieser SQL Server-Datenbank (Quelle) in Blob Storage (Senke). Anschließend erstellen Sie eine Tabelle mit dem Namen **emp** in Ihrer SQL Server-Datenbank und fügen einige Beispieleinträge in die Tabelle ein.

1. Starten Sie SQL Server Management Studio. Falls die Suite auf Ihrem Computer noch nicht installiert wurde, rufen Sie [Herunterladen von SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) auf.

1. Stellen Sie eine Verbindung mit Ihrer SQL Server-Instanz her, indem Sie Ihre Anmeldeinformationen verwenden.

1. Erstellen Sie eine Beispieldatenbank. Klicken Sie in der Strukturansicht mit der rechten Maustaste auf **Datenbanken**, und wählen Sie anschließend **Neue Datenbank**.

1. Geben Sie im Fenster **Neue Datenbank** einen Namen für die Datenbank ein, und wählen Sie dann **OK**.

1. Führen Sie das folgende Abfrageskript für die Datenbank aus, um die Tabelle **emp** zu erstellen und einige Beispieldaten einzufügen. Klicken Sie in der Strukturansicht mit der rechten Maustaste auf die von Ihnen erstellte Datenbank, und wählen Sie anschließend **Neue Abfrage**.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    INSERT INTO emp (FirstName, LastName) VALUES ('John', 'Doe')
    INSERT INTO emp (FirstName, LastName) VALUES ('Jane', 'Doe')
    GO
    ```

### <a name="azure-storage-account"></a>Azure-Speicherkonto
Sie verwenden in diesem Tutorial ein Azure Storage-Allzweckkonto (Blob Storage) als Ziel-/Senkendatenspeicher. Falls Sie noch nicht über ein allgemeines Speicherkonto verfügen, erfahren Sie unter [Erstellen Sie ein Speicherkonto.](../storage/common/storage-account-create.md), wie Sie eins erstellen. Die Pipeline in der Data Factory, die Sie in diesem Tutorial erstellen, kopiert Daten aus der SQL Server-Datenbank (Quelle) in diese Blob Storage-Instanz (Senke). 

#### <a name="get-the-storage-account-name-and-account-key"></a>Abrufen des Speicherkontonamens und des Kontoschlüssels
In diesem Tutorial verwenden Sie Name und Schlüssel Ihres Speicherkontos. Den Namen und den Schlüssel Ihres Speicherkontos ermitteln Sie anhand der folgenden Schritte:

1. Melden Sie sich mit Ihrem Azure-Benutzernamen und dem dazugehörigen Kennwort beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie im linken Bereich **Alle Dienste** aus. Filtern Sie nach dem Schlüsselwort **Speicher**, und wählen Sie dann **Speicherkonten** aus.

    ![Speicherkontosuche](media/doc-common-process/search-storage-account.png)

1. Filtern Sie in der Liste mit den Speicherkonten ggf. nach Ihrem Speicherkonto. Wählen Sie dann Ihr Speicherkonto aus.

1. Wählen Sie im Fenster **Speicherkonto** die Option **Zugriffsschlüssel**.


1. Kopieren Sie in den Feldern **Speicherkontoname** und **key1** die Werte, und fügen Sie sie zur späteren Verwendung im Tutorial in einen Editor ein.

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory

1. Wählen Sie im Menü auf der linken Seite **Ressource erstellen** > **Integration** > **Data Factory** aus:

   ![Erstellen einer neuen Data Factory](./media/doc-common-process/new-azure-data-factory-menu.png)

1. Geben Sie auf der Seite **Neue Data Factory** unter **Name** den Namen **ADFTutorialDataFactory** ein.

   Der Name der Data Factory muss *global eindeutig* sein. Sollte für das Feld „Name“ die folgende Fehlermeldung angezeigt werden, ändern Sie den Namen der Data Factory (beispielsweise in „<IhrName>ADFTutorialDataFactory“). Benennungsregeln für Data Factory-Artefakte finden Sie im Thema [Azure Data Factory – Benennungsregeln](naming-rules.md).

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Fehlermeldung zu neuer Data Factory für doppelten Namen":::
1. Wählen Sie das **Azure-Abonnement** aus, in dem die Data Factory erstellt werden soll.
1. Führen Sie unter **Ressourcengruppe** einen der folgenden Schritte aus:

   - Wählen Sie die Option **Use existing**(Vorhandene verwenden) und dann in der Dropdownliste eine vorhandene Ressourcengruppe.

   - Wählen Sie **Neu erstellen**, und geben Sie den Namen einer Ressourcengruppe ein. 
        
     Weitere Informationen zu Ressourcengruppen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-resource-manager/management/overview.md).
1. Wählen Sie unter **Version** die Option **V2**.
1. Wählen Sie unter **Standort** den Standort für die Data Factory aus. In der Dropdownliste werden nur unterstützte Standorte angezeigt. Die Datenspeicher (etwa Azure Storage und SQL-Datenbank) und Computeeinheiten (etwa Azure HDInsight), die von Data Factory genutzt werden, können sich an anderen Standorten und in anderen Regionen befinden.
1. Klicken Sie auf **Erstellen**.

1. Nach Abschluss der Erstellung wird die Seite **Data Factory** wie in der Abbildung angezeigt:

    Die :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Startseite für die Azure Data Factory mit der Kachel „Open Azure Data Factory Studio“.":::

1. Wählen Sie auf der Kachel **Open Azure Data Factory Studio** die Option **Öffnen** um das Data Factory Benutzerinterface in einem separaten Tab zu starten.

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Erstellen einer Pipeline mithilfe des Tools zum Kopieren von Daten

1. Wählen Sie auf der Azure Data Factory Startseite die Option **Erfassen** um das Tool zum Kopieren der Daten zu starten.

   ![Screenshot der Azure Data Factory-Homepage](./media/doc-common-process/get-started-page.png)

1. Navigieren Sie zu der Seite **Eigenschaften** des Tools zum Kopieren von Daten und wählen Sie die Option **Integrierte Kopieraufgabe** unter dem **Aufgabentyp** aus. Wählen Sie nun unter **Aufgabenintervall oder Aufgabenzeitplan** die Option **Jetzt einmal ausführen** und dann **Weiter** aus.

1. Navigieren Sie zu der Seite **Quelldatenspeicher** und wählen Sie **+ Neue Verbindung erstellen** aus.

1. Suchen Sie auf der Seite **Neue Verbindung** die Option **SQL Server** und wählen Sie dann **Weiter** aus.

1. Geben Sie im Dialogfeld **Neue SQL Server Verbindung** unter der Option **Name** die Zeichenfolge **SqlServerLinkedService** ein. Wählen Sie unter **Connect via integration runtime** (Verbindung per Integration Runtime herstellen) die Option **+Neu**. Sie müssen eine selbstgehostete Integration Runtime erstellen, auf Ihren Computer herunterladen und bei Data Factory registrieren. Die selbstgehostete Integration Runtime kopiert Daten zwischen Ihrer lokalen Umgebung und der Cloud.

1. Wählen Sie im Dialogfeld **Integration Runtime-Setup** die Option **Selbstgehostet** aus. Klicken Sie anschließend auf **Weiter**.

   ![Erstellen von Integrationslaufzeit](./media/tutorial-hybrid-copy-data-tool/create-self-hosted-integration-runtime.png)

1. Geben Sie im Dialogfeld **Integration Runtime-Setup** unter der Option **Name** die Zeichenfolge **TutorialIntegrationRuntime** ein. Klicken Sie anschließend auf **Erstellen**.

1. Wählen Sie im Dialogfeld **Integration Runtime-Setup** die Option **Klicken Sie hier, um das Express-Setup für diesen Computer zu starten** aus. Dadurch wird die Integration Runtime auf Ihrem Computer installiert und bei Data Factory registriert. Alternativ können Sie die Installationsdatei über die manuelle Setupoption herunterladen, die Datei ausführen und die Integration Runtime mithilfe des Schlüssels registrieren.

1. Führen Sie die heruntergeladene Anwendung aus. Der Status des Express-Setups wird im Fenster angezeigt.

    ![Status des Express-Setups](./media/tutorial-hybrid-copy-data-tool/express-setup-status.png)

1. Stellen Sie im Dialogfeld **Neuer mit dem SQL Server verknüpfter Dienst**  sicher, dass die Zeichenfolge **TutorialIntegrationRuntime** unter **Verbindung per Integration Runtime herstellen** ausgewählt ist. Führen Sie dann die folgenden Schritte aus:

    a. Geben Sie unter **Name** die Zeichenfolge **SqlServerLinkedService** ein.

    b. Geben Sie unter **Servername** den Namen Ihrer SQL Server-Instanz ein.

    c. Geben Sie unter **Datenbankname** den Namen Ihrer lokalen Datenbank ein.

    d. Wählen Sie unter **Authentifizierungstyp** eine geeignete Authentifizierung aus.

    e. Geben Sie unter **Benutzername** den Namen eines Benutzers mit Zugriff auf SQL Server ein.

    f. Geben Sie das **Kennwort** für den Benutzer ein.

    g. Testen Sie die Verbindung, und wählen Sie **Erstellen** aus.

      ![Integration Runtime ausgewählt](./media/tutorial-hybrid-copy-data-tool/integration-runtime-selected.png)

1. Stellen Sie auf der Seite **Quelldatenspeicher** sicher, dass die neu erstellte **SQL Server Verbindung** in dem **Verbindungsblock** ausgewählt ist. Navigieren Sie dann im Abschnitt **Quelltabellen** zu der Option **EXISTIERENDE TABELLEN** und wählen Sie die Tabelle **dbo.emp** aus der Liste aus, und wählen Sie dann **Weiter** aus. Basierend auf Ihrer Datenbank können Sie eine beliebige andere Tabelle auswählen.

1. Auf der Seite **Filter anwenden** können Sie eine Datenvorschau anzeigen und das Schema der Eingabedaten einsehen, indem Sie die Schaltfläche **Datenvorschau** auswählen. Wählen Sie **Weiter** aus.

1. Wählen Sie auf der Seite **Zieldatenspeicher** die Option **+ Eine Neue Verbindung erstellen** aus

1. Navigieren Sie unter **Neuer verknüpfter Dienst** zu **Azure Blob Storage** und wählen Sie dann **Weiter** aus.

   ![Auswählen von Blob Storage](./media/tutorial-hybrid-copy-data-tool/select-destination-data-store.png)

1. Führen Sie im Dialogfenster **Neuer mit Azure Blob Storage verknüpfter Dienst** die folgenden Schritte aus:

   a. Geben Sie unter **Name** den Namen **AzureStorageLinkedService** ein.

   b. Navigieren Sie unter **Verbinden über Integrationslaufzeit** zu dem Eintrag **TutorialIntegrationRuntime** und wählen Sie unter **Authentifizierungsmethode** den Eintrag **Kontoschlüssel** aus.
   
   c. Navigieren Sie zu **Azure-Abonnement** und wählen Sie Ihr Azure-Abonnement aus der Dropdownliste aus.

   d. Wählen Sie in der Dropdownliste unter **Speicherkontoname** Ihr Speicherkonto aus.

   e. Testen Sie die Verbindung, und wählen Sie **Erstellen** aus.

1. Stellen Sie im Dialogfeld **Zieldatenspeicher** sicher, dass die neu erstellte **Azure Blob Storage-Verbindung** im Block **Verbindung** ausgewählt ist. Geben Sie unter der Option **Ordnerpfad** die Zeichenfolge **adftutorial/fromonprem** ein. Den Container **adftutorial** haben Sie im Rahmen der Vorbereitung erstellt. Ist der Ausgabeordner (in diesem Fall **fromonprem**) nicht vorhanden, wird er von Data Factory automatisch erstellt. Sie können auch auf die Schaltfläche **Durchsuchen** klicken und zum Blobspeicher und den dazugehörigen Containern/Ordnern navigieren. Wenn Sie unter **Dateiname** keinen Wert angeben, wird standardmäßig der Name aus der Quelle (in diesem Fall **dbo.emp**) verwendet.

   :::image type="content" source="./media/tutorial-hybrid-copy-data-tool/destination-data-store.png" alt-text="Ein Screenshot, der die Konfiguration der Seite „Zieldatenspeicher“ zeigt":::.

1. Klicken Sie im Dialogfenster **Dateiformateinstellungen** auf **Weiter**.

1. Geben Sie im Dialogfeld **Einstellungen** unter der Option **Aufgabenname** die Zeichenfolge **CopyFromOnPremSqlToAzureBlobPipeline** ein und wählen Sie dann **Weiter** aus. Das Tool zum Kopieren von Daten erstellt eine Pipeline mit dem Namen, den Sie in diesem Feld angeben.

1. Überprüfen Sie im Dialogfenster **Zusammenfassung** die Werte sämtlicher Einstellungen, und klicken Sie anschließend auf **Weiter**.

1. Wählen Sie auf der Seite **Bereitstellung** die Option **Überwachen** aus, um die Pipeline (Aufgabe) zu überwachen. 

1. Nach Abschluss der Pipelineausführung können Sie den Status der erstellten Pipeline anzeigen. 

1. Wählen Sie auf der Seite „Pipeline-Ausführungen“ die Option **Aktualisieren** aus, um die Liste zu aktualisieren. Wählen Sie den Link unter **PIPELINENAME** aus, um Details zur Aktivitätsausführung anzuzeigen oder die Pipeline erneut auszuführen. 

    :::image type="content" source="./media/tutorial-hybrid-copy-data-tool/pipeline-runs.png" alt-text="Ein Screenshot, der die Seite Pipeline-Ausführungen zeigt.":::

1. Wählen Sie auf der Seite „Aktivitätsausführungen“ unter der Spalte **Aktivitätsname** den Link **Details** (Brillensymbol) aus, um weitere Details zum Kopiervorgang anzuzeigen. Wählen Sie im Breadcrumb-Menü den Link **Alle Pipeline-Ausführungen** aus, um zur Seite „Pipeline-Ausführungen“ zurückzukehren. Klicken Sie zum Aktualisieren der Ansicht auf **Aktualisieren**.

    :::image type="content" source="./media/tutorial-hybrid-copy-data-tool/activity-details.png" alt-text="Ein Screenshot, der die Aktivitätsdetails zeigt.":::

1. Vergewissern Sie sich, dass die Ausgabedatei im Ordner **fromonprem** des Containers **adftutorial** enthalten ist.

1. Klicken Sie im linken Bereich auf die Registerkarte **Autor**, um in den Bearbeitungsmodus zu wechseln. Sie können die vom Tool erstellten verknüpften Dienste, Datasets und Pipelines mit dem Editor aktualisieren. Klicken Sie auf **Code**, um den JSON-Code für die im Editor geöffnete Entität anzuzeigen. Ausführliche Informationen zum Bearbeiten dieser Entitäten über die Data Factory-Benutzeroberfläche finden Sie in der [Azure-Portal-Version dieses Tutorials](tutorial-copy-data-portal.md).

    :::image type="content" source="./media/tutorial-hybrid-copy-data-tool/author-tab.png" alt-text="Ein Screenshot, der die Registerkarte „Autor“ zeigt.":::

## <a name="next-steps"></a>Nächste Schritte
Die Pipeline in diesem Beispiel kopiert Daten aus einer SQL Server-Datenbank in Blob Storage. Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen einer Data Factory.
> * Erstellen einer Pipeline mithilfe des Tools zum Kopieren von Daten
> * Überwachen der Pipeline- und Aktivitätsausführungen.

Eine Liste mit den von Data Factory unterstützten Datenspeichern finden Sie unter [Unterstützte Datenspeicher und Formate](copy-activity-overview.md#supported-data-stores-and-formats).

Fahren Sie mit dem folgenden Tutorial fort, um mehr über das Kopieren von Daten per Massenvorgang aus einer Quelle in ein Ziel zu erfahren:

> [!div class="nextstepaction"]
>[Massenkopieren von Daten](tutorial-bulk-copy-portal.md)
