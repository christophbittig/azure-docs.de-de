---
title: Kopieren von Daten aus Azure Blob Storage nach SQL mithilfe des Tools zum Kopieren von Daten
description: Erstellen Sie eine Azure Data Factory-Instanz, und verwenden Sie dann das Tool zum Kopieren von Daten, um Daten aus Azure Blob Storage in SQL-Datenbank zu kopieren.
author: jianleishen
ms.author: jianleishen
ms.service: data-factory
ms.subservice: tutorials
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 07/08/2021
ms.openlocfilehash: c47443068aadef73a75e68d97de24981125fd973
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124736992"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-the-copy-data-tool"></a>Kopieren von Daten aus Azure Blob Storage in eine SQL-Datenbank mithilfe des Tools zum Kopieren von Daten

> [!div class="op_single_selector" title1="Wählen Sie die Version des Data Factory-Diensts aus, den Sie verwenden:"]
> * [Version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuelle Version](tutorial-copy-data-tool.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Tutorial verwenden Sie das Azure-Portal, um eine Data Factory zu erstellen. Anschließend erstellen Sie mithilfe des Tools zum Kopieren von Daten eine Pipeline, die Daten aus Azure Blob Storage in eine SQL-Datenbank kopiert.

> [!NOTE]
> Falls Sie noch nicht mit Azure Data Factory vertraut sind, ist es ratsam, den Artikel [Einführung in Azure Data Factory](introduction.md) zu lesen.

In diesem Tutorial führen Sie die folgenden Schritte aus:
> [!div class="checklist"]
> * Erstellen einer Data Factory.
> * Erstellen einer Pipeline mithilfe des Tools zum Kopieren von Daten
> * Überwachen der Pipeline- und Aktivitätsausführungen.

## <a name="prerequisites"></a>Voraussetzungen

* **Azure-Abonnement**: Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* **Azure Storage-Konto**: Verwenden Sie Blob Storage als _Quelldatenspeicher_. Falls Sie noch nicht über ein Azure Storage-Konto verfügen, finden Sie [hier](../storage/common/storage-account-create.md) eine Anleitung zum Erstellen eines Speicherkontos.
* **Azure SQL-Datenbank**: Verwenden Sie eine SQL-Datenbank als _Senkendatenspeicher_. Sollten Sie über keine SQL-Datenbank verfügen, lesen Sie die Anweisungen unter [Erstellen einer Azure SQL-Datenbank im Azure-Portal](../azure-sql/database/single-database-create-quickstart.md).

### <a name="create-a-blob-and-a-sql-table"></a>Erstellen eines Blobs und einer SQL-Tabelle

Bereiten Sie Ihre Blob Storage-Instanz und Ihre SQL-Datenbank wie folgt für das Tutorial vor:

#### <a name="create-a-source-blob"></a>Erstellen eines Quellblobs

1. Starten Sie **Editor**. Kopieren Sie den folgenden Text, und speichern Sie ihn in einer Datei namens **inputEmp.txt** auf einem Datenträger:

   ```text
   FirstName|LastName
   John|Doe
   Jane|Doe
   ```

1. Erstellen Sie einen Container namens **adfv2tutorial**, und laden Sie die Datei „inputEmp.txt“ in den Container hoch. Sie können für diese Aufgaben das Azure-Portal oder verschiedene Tools verwenden, etwa [Azure Storage-Explorer](https://storageexplorer.com/).

#### <a name="create-a-sink-sql-table"></a>Erstellen einer SQL-Senkentabelle

1. Verwenden Sie das folgende SQL-Skript, um eine Tabelle namens **dbo.emp** in Ihrer SQL-Datenbank zu erstellen:

   ```sql
   CREATE TABLE dbo.emp
   (
       ID int IDENTITY(1,1) NOT NULL,
       FirstName varchar(50),
       LastName varchar(50)
   )
   GO
   CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
   ```

2. Gewähren Sie Azure-Diensten den Zugriff auf SQL Server. Vergewissern Sie sich, dass für den Server, auf dem SQL-Datenbank ausgeführt wird, die Einstellung **Anderen Azure-Diensten und -Ressourcen den Zugriff auf diesen Server gestatten** aktiviert ist. Diese Einstellung ermöglicht der Data Factory das Schreiben von Daten in Ihre Datenbankinstanz. Navigieren Sie zum Überprüfen und Aktivieren dieser Einstellung zu „Logischer SQL Server“ > „Sicherheit“ > „Firewalls und virtuelle Netzwerke“, und legen Sie die Option **Anderen Azure-Diensten und -Ressourcen den Zugriff auf diesen Server gestatten** auf **EIN** fest.

   > [!NOTE]
   > Die Option **Anderen Azure-Diensten und -Ressourcen den Zugriff auf diesen Server gestatten** ermöglicht den Netzwerkzugriff auf Ihre SQL Server-Instanz über jede Azure-Ressource, nicht nur über die Ressourcen in Ihrem Abonnement. Weitere Informationen finden Sie unter [Azure SQL-Datenbank- und Azure Synapse-IP-Firewallregeln](../azure-sql/database/firewall-configure.md). Stattdessen können Sie [private Endpunkte](../private-link/private-endpoint-overview.md) verwenden, um eine Verbindung mit Azure-PaaS-Diensten herzustellen, ohne öffentliche IP-Adressen zu verwenden.

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory

1. Wählen Sie im Menü auf der linken Seite **Ressource erstellen** > **Integration** > **Data Factory** aus:

   :::image type="content" source="./media/doc-common-process/new-azure-data-factory-menu.png" alt-text="Erstellen einer neuen Data Factory":::

1. Geben Sie auf der Seite **Neue Data Factory** unter **Name** den Namen **ADFTutorialDataFactory** ein.

   Der Name der Data Factory muss _global eindeutig_ sein. Sie erhalten unter Umständen die folgende Fehlermeldung:

   :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Fehlermeldung zu neuer Data Factory für doppelten Namen":::

   Wenn eine Fehlermeldung zum Namenswert angezeigt wird, geben Sie einen anderen Namen für die Data Factory ein. Verwenden Sie beispielsweise den Namen _**IhrName**_ **ADFTutorialDataFactory**. Benennungsregeln für Data Factory-Artefakte finden Sie im Thema [Data Factory – Benennungsregeln](naming-rules.md).

1. Wählen Sie das **Azure-Abonnement** aus, in dem die neue Data Factory erstellt werden soll.

1. Führen Sie unter **Ressourcengruppe** einen der folgenden Schritte aus:

   a. Wählen Sie die Option **Use existing**(Vorhandene verwenden) und dann in der Dropdownliste eine vorhandene Ressourcengruppe.

   b. Wählen Sie **Neu erstellen**, und geben Sie den Namen einer Ressourcengruppe ein.

   Weitere Informationen zu Ressourcengruppen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-resource-manager/management/overview.md).

1. Wählen Sie unter **Version** die Option **V2**.

1. Wählen Sie unter **Standort** den Standort für die Data Factory aus. In der Dropdownliste werden nur unterstützte Standorte angezeigt. Die Datenspeicher (etwa Azure Storage und SQL-Datenbank) und Computeeinheiten (etwa Azure HDInsight), die von der Data Factory genutzt werden, können sich an anderen Standorten und in anderen Regionen befinden.

1. Klicken Sie auf **Erstellen**.

1. Nach Abschluss der Erstellung wird die Startseite von **Data Factory** angezeigt.

   :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Homepage für Azure Data Factory mit der Kachel „Open Azure Data Factory Studio“.":::

1. Zum Starten des Azure Data Factory-Benutzers wählen Sie auf der Kachel **Open Azure Data Factory Studio** die Option **Öffnen** aus.

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Erstellen einer Pipeline mithilfe des Tools zum Kopieren von Daten

1. Wählen Sie auf der Homepage von Azure Data Factory die Kachel **Erfassung** aus, um das Tool „Daten kopieren“ zu starten.

   :::image type="content" source="./media/doc-common-process/get-started-page.png" alt-text="Screenshot der Azure Data Factory-Homepage":::

1. Wählen Sie auf der Seite **Eigenschaften** des Tools zum Kopieren von Daten unter **Aufgabentyp** den Typ **Integrierte Kopieraufgabe** aus, und wählen Sie dann **Weiter** aus.

     :::image type="content" source="./media/tutorial-copy-data-tool/copy-data-tool-properties-page.png" alt-text="Screenshot der Eigenschaftenseite":::
    
1. Führen Sie auf der Seite **Quelldatenspeicher** die folgenden Schritte aus:

   a. Wählen Sie **+ Neue Verbindung erstellen** aus, um eine Verbindung hinzuzufügen.

   b. Wählen Sie im Katalog **Azure Blob Storage** aus, und klicken Sie dann auf **Fortsetzen**.

   c. Wählen Sie auf der Seite **Neue Verbindung (Azure Blob Storage)** in der Liste **Azure-Abonnement** Ihr Azure-Abonnement und in der Liste **Speicherkontoname** Ihr Speicherkonto aus. Testen Sie die Verbindung, und wählen Sie **Fertig stellen** aus.

   d. Wählen Sie m Block **Verbindung** den neu erstellten verknüpften Dienst als Quelle aus.

   e. Wählen Sie im Abschnitt **Datei oder Ordner** die Option **Durchsuchen** aus, um zum Ordner **adfv2tutorial** zu navigieren, wählen Sie die Datei **inputEmp.txt** aus, und klicken Sie dann auf **OK**.

   f. Wählen Sie **Weiter** aus, um mit dem nächsten Schritt fortzufahren.

   :::image type="content" source="./media/tutorial-copy-data-tool/source-data-store.png" alt-text="Konfigurieren Sie die Quelle.":::

1. Aktivieren Sie auf der Seite **File format settings** (Dateiformateinstellungen) das Kontrollkästchen *Erste Zeile als Header verwenden*. Beachten Sie, dass das Tool die Spalten- und Zeilentrennzeichen automatisch erkennt, und Sie eine Vorschau der Daten und das Schema der Eingabedaten anzeigen können, indem Sie auf dieser Seite die Schaltfläche **Datenvorschau** auswählen. Wählen Sie **Weiter** aus. 

   :::image type="content" source="./media/tutorial-copy-data-tool/file-format-settings-page.png" alt-text="Dateiformateinstellungen":::

1. Führen Sie auf der Seite **Zieldatenspeicher** die folgenden Schritte aus:

   a. Wählen Sie **+ Neue Verbindung erstellen** aus, um eine Verbindung hinzuzufügen.

   b. Wählen Sie im Katalog **Azure SQL-Datenbank** und dann **Weiter** aus.

   c. Wählen Sie auf der Seite **Neue Verbindung (Azure SQL-Datenbank)** in der Dropdownliste Ihr Azure-Abonnement, den Servernamen und den Datenbanknamen aus. Wählen Sie dann unter **SQL Authentifizierungstyp** die Option **SQL-Authentifizierung** aus, und geben Sie den Benutzernamen und das Kennwort an. Testen Sie die Verbindung, und wählen Sie **Erstellen** aus.

   :::image type="content" source="./media/tutorial-copy-data-tool/config-azure-sql-db.png" alt-text="Konfigurieren von Azure SQL-Datenbank":::

   d. Wählen Sie den neu erstellten verknüpften Dienst als Senke und anschließend **Weiter** aus.

1. Klicken Sie auf der Seite **Zieldatenspeicher** auf **Vorhandene Tabelle verwenden**, und wählen Sie die Tabelle **dbo.emp** aus. Wählen Sie **Weiter** aus.

1. Auf der Seite **Spaltenzuordnung** sehen Sie, dass die zweite und dritte Spalte in der Eingabedatei den Spalten **FirstName** und **LastName** der Tabelle **emp** zugeordnet werden. Passen Sie die Zuordnung an, um sicherzustellen, dass sie keine Fehler enthält, und wählen Sie anschließend **Weiter** aus.

   :::image type="content" source="./media/tutorial-copy-data-tool/column-mapping.png" alt-text="Seite „Spaltenzuordnung“":::

1. Geben Sie auf der Seite **Einstellungen** unter **Taskname** den Namen **CopyFromBlobToSqlPipeline** ein, und klicken Sie dann auf **Weiter**.

   :::image type="content" source="./media/tutorial-copy-data-tool/settings.png" alt-text="Konfigurieren Sie die Einstellungen.":::

1. Überprüfen Sie auf der Seite **Zusammenfassung** die Einstellungen, und klicken Sie anschließend auf **Weiter**.

1. Klicken Sie auf der Seite **Bereitstellung** auf **Überwachen**, um die Pipeline (Task) zu überwachen.

   :::image type="content" source="./media/tutorial-copy-data-tool/monitor-pipeline.png" alt-text="Überwachen der Pipeline":::

1. Wählen Sie auf der Seite „Pipelineausführungen“ die Option **Aktualisieren** aus, um die Liste zu aktualisieren. Wählen Sie den Link unter **Pipelinename** aus, um Details zur Aktivitätsausführung anzuzeigen oder die Pipeline erneut auszuführen. 

   :::image type="content" source="./media/tutorial-copy-data-tool/pipeline-run.png" alt-text="Ausführen der Pipeline":::

1. Wählen Sie auf der Seite „Aktivitätsausführungen“ unter der Spalte **Aktivitätsname** den Link **Details** (Brillensymbol) aus, um weitere Details zum Kopiervorgang anzuzeigen. Wählen Sie im Breadcrumb-Menü den Link **Alle Pipelineausführungen** aus, um zur Ansicht „Pipelineausführungen“ zurückzukehren. Klicken Sie zum Aktualisieren der Ansicht auf **Aktualisieren**.

   :::image type="content" source="./media/tutorial-copy-data-tool/activity-monitoring.png" alt-text="Überwachung der Aktivitätsausführungen":::

1. Vergewissern Sie sich, dass die Daten in die Tabelle **dbo.emp** in Ihrer SQL-Datenbank eingefügt werden.

1. Klicken Sie im linken Bereich auf die Registerkarte **Autor**, um in den Bearbeitungsmodus zu wechseln. Sie können die vom Tool erstellten verknüpften Dienste, Datasets und Pipelines mit dem Editor aktualisieren. Ausführliche Informationen zum Bearbeiten dieser Entitäten über die Data Factory-Benutzeroberfläche finden Sie in der [Azure-Portal-Version dieses Tutorials](tutorial-copy-data-portal.md).

   :::image type="content" source="./media/tutorial-copy-data-tool/author-tab.png" alt-text="Auswählen der Registerkarte „Autor“":::

## <a name="next-steps"></a>Nächste Schritte
Die Pipeline in diesem Beispiel kopiert Daten aus Blob Storage in eine SQL-Datenbank. Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen einer Data Factory.
> * Erstellen einer Pipeline mithilfe des Tools zum Kopieren von Daten
> * Überwachen der Pipeline- und Aktivitätsausführungen.

Fahren Sie mit dem folgenden Tutorial fort, um zu erfahren, wie Sie Daten von einem lokalen Speicherort in die Cloud kopieren:

>[!div class="nextstepaction"]
>[Tutorial: Kopieren von Daten aus einer lokalen SQL Server-Datenbank nach Azure Blob Storage](tutorial-hybrid-copy-data-tool.md)
