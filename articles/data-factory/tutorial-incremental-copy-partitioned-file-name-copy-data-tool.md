---
title: Inkrementelles Kopieren neuer Dateien basierend auf dem zeitpartitionierten Dateinamen
description: Erstellen Sie eine Azure Data Factory, und verwenden Sie dann das Tool „Daten kopieren“, um neue Dateien nur basierend auf dem zeitpartitionierten Dateinamen zu laden.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/15/2021
ms.openlocfilehash: f2fe1e854027cb6fd15b6d0e2e659cc62528a2e2
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124757855"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>Inkrementelles Kopieren neuer Dateien basierend auf dem zeitpartitionierten Dateinamen und mithilfe des Tools „Daten kopieren“

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Tutorial verwenden Sie das Azure-Portal, um eine Data Factory zu erstellen. Anschließend verwenden Sie das Tool „Daten kopieren“, um eine Pipeline zu erstellen, die neue Dateien basierend auf dem zeitpartitionierten Dateinamen aus einem Azure-Blobspeicher in einen anderen kopiert.

> [!NOTE]
> Falls Sie noch nicht mit Azure Data Factory vertraut sind, ist es ratsam, den Artikel [Einführung in Azure Data Factory](introduction.md) zu lesen.

In diesem Tutorial führen Sie die folgenden Schritte aus:

> [!div class="checklist"]
> * Erstellen einer Data Factory.
> * Erstellen einer Pipeline mithilfe des Tools zum Kopieren von Daten
> * Überwachen der Pipeline- und Aktivitätsausführungen.

## <a name="prerequisites"></a>Voraussetzungen

* **Azure-Abonnement**: Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* **Azure-Speicherkonto**: Verwenden Sie den Blobspeicher als _Quelldatenspeicher_ und als _Senkendatenspeicher_. Falls Sie noch nicht über ein Azure-Speicherkonto verfügen, finden Sie Anweisungen dazu unter [Erstellen eines Speicherkontos](../storage/common/storage-account-create.md).

### <a name="create-two-containers-in-blob-storage"></a>Erstellen von zwei Containern im Blobspeicher

Bereiten Sie Ihren Blobspeicher folgendermaßen auf das Tutorial vor.

1. Erstellen Sie einen Container mit dem Namen **source** (Quelle).  Erstellen Sie in Ihrem Container den Ordnerpfad **2021/07/15/06**. Erstellen Sie eine leere Textdatei, und nennen Sie sie **file1.txt**. Laden Sie die Datei „file1.txt“ in den Ordnerpfad **source/2021/07/15/06** in Ihrem Speicherkonto hoch.  Sie können für diese Aufgaben verschiedene Tools verwenden, etwa [Azure Storage-Explorer](https://storageexplorer.com/).

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png" alt-text="Hochladen von Dateien":::

    > [!NOTE]
    > Passen Sie den Ordnernamen an Ihre UTC-Zeit an.  Wenn die aktuelle UTC-Zeit beispielsweise „15. Juli 2021, 06:10 Uhr“ lautet, können Sie gemäß dem Format **source/{Jahr}/{Monat}/{Tag}/{Stunde}/** den Ordnerpfad **source/2021/07/15/06/** erstellen.

2. Erstellen Sie einen Container mit dem Namen **destination** (Ziel). Sie können für diese Aufgaben verschiedene Tools verwenden, etwa [Azure Storage-Explorer](https://storageexplorer.com/).

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory

1. Wählen Sie im Menü auf der linken Seite **Ressource erstellen** > **Integration** > **Data Factory** aus:

   :::image type="content" source="./media/doc-common-process/new-azure-data-factory-menu.png" alt-text="Auswahl von Data Factory im Bereich &quot;Neu&quot;":::

2. Geben Sie auf der Seite **Neue Data Factory** unter **Name** den Namen **ADFTutorialDataFactory** ein.

    Der Name der Data Factory muss _global eindeutig_ sein. Sie erhalten unter Umständen die folgende Fehlermeldung:

   :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Fehlermeldung zu neuer Data Factory für doppelten Namen":::

   Wenn eine Fehlermeldung zum Namenswert angezeigt wird, geben Sie einen anderen Namen für die Data Factory ein. Verwenden Sie beispielsweise den Namen _**IhrName**_ **ADFTutorialDataFactory**. Benennungsregeln für Data Factory-Artefakte finden Sie im Thema [Data Factory – Benennungsregeln](naming-rules.md).
3. Wählen Sie das **Azure-Abonnement** aus, in dem die neue Data Factory erstellt werden soll.
4. Führen Sie unter **Ressourcengruppe** einen der folgenden Schritte aus:

    a. Wählen Sie die Option **Use existing**(Vorhandene verwenden) und dann in der Dropdownliste eine vorhandene Ressourcengruppe.

    b. Wählen Sie **Neu erstellen**, und geben Sie den Namen einer Ressourcengruppe ein. 
         
    Weitere Informationen zu Ressourcengruppen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-resource-manager/management/overview.md).

5. Wählen Sie unter **Version** die Option **V2**.
6. Wählen Sie unter **Standort** den Standort für die Data Factory aus. In der Dropdownliste werden nur unterstützte Standorte angezeigt. Die Datenspeicher (etwa Azure Storage und SQL-Datenbank) und Computeeinheiten (etwa Azure HDInsight), die von der Data Factory genutzt werden, können sich an anderen Standorten und in anderen Regionen befinden.
7. Klicken Sie auf **Erstellen**.
8. Nach Abschluss der Erstellung wird die Startseite von **Data Factory** angezeigt.
9. Zum Starten des Azure Data Factory-Benutzers wählen Sie auf der Kachel **Open Azure Data Factory Studio** die Option **Öffnen** aus.

    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Startseite für Azure Data Factory mit der Kachel „Open Azure Data Factory Studio“":::

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Erstellen einer Pipeline mithilfe des Tools zum Kopieren von Daten

1. Wählen Sie auf der Azure Data Factory-Homepage den Titel **Erfassen**, um das Tool zum Kopieren der Daten zu starten.

   :::image type="content" source="./media/doc-common-process/get-started-page.png" alt-text="Screenshot, der die ADF-Startseite zeigt.":::

2. Gehen Sie auf der Seite **Properties** (Eigenschaften) wie folgt vor:
    1. Wählen Sie unter **Task type** (Aufgabentyp) **Built-in copy task** (Integrierte Kopieraufgabe) aus.

    1. Wählen Sie unter **Task cadence or task schedule** (Aufgabenhäufigkeit oder Aufgabenzeitplan) **Tumbling window** (Rollierendes Fenster) aus.

    1. Geben Sie unter **Recurrence** (Wiederholung) **1 Hour(s)** (1 Stunde[n]) ein.

    1. Wählen Sie **Weiter** aus.

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png" alt-text="Seite „Eigenschaften“":::
3. Führen Sie auf der Seite **Quelldatenspeicher** die folgenden Schritte aus:

    a. Wählen Sie **+ New connection** (Neue Verbindung) aus, um eine Verbindung hinzuzufügen.
    
    b. Wählen Sie im Katalog **Azure Blob Storage** aus, und klicken Sie dann auf **Fortsetzen**.
    
    c. Geben Sie auf der Seite **New connection (Azure Blob Storage)** (Neue Verbindung [Azure Blob Storage]) einen Namen für die Verbindung ein. Wählen Sie Ihr Azure-Abonnement und anschließend in der Liste **Speicherkontoname** Ihr Speicherkonto aus. Testen Sie die Verbindung, und wählen Sie **Fertig stellen** aus.

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-connection.png" alt-text="Seite „Quelldatenspeicher“":::

    d. Wählen Sie auf der Seite **Source data store** (Quelldatenspeicher) die neu erstellte Verbindung in dem Abschnitt **Verbindung** aus.

    e. Suchen Sie im Abschnitt **File or folder** (Datei oder Ordner) nach dem Container **source** (Quelle) und wählen Sie ihn aus. Wählen Sie anschließend **OK**.

    f. Wählen Sie unter **File loading behavior** (Dateiladeverhalten) die Option **Incremental load: time-partitioned folder/file names** (Inkrementell laden: zeitpartionierter Ordner/Dateinamen).

    g. Schreiben Sie den dynamischen Ordnerpfad im Format **source/{Jahr}/{Monat}/{Tag}/{Stunde}/** , und ändern Sie das Format wie im folgenden Screenshot. 
    
    h. Aktivieren Sie das Kontrollkästchen **Binary copy** (Binärkopie), und wählen Sie **Next** (Weiter) aus.

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page.png" alt-text="Ein Screenshot, der die Konfiguration der Seite „Source data store“ (Quelldatenspeicher) zeigt.":::


4. Führen Sie auf der Seite **Zieldatenspeicher** die folgenden Schritte aus:
    1. Wählen Sie **AzureBlobStorage** aus, bei dem es sich um das gleiche Speicherkonto wie im Datenquellspeicher handelt.

    1. Suchen Sie nach dem Ordner **destination** (Ziel), wählen Sie diesen aus, und wählen Sie anschließend **OK**.

    1. Schreiben Sie den dynamischen Ordnerpfad im Format **destination/{Jahr}/{Monat}/{Tag}/{Stunde}/** , und ändern Sie das Format wie im folgenden Screenshot.

    1. Wählen Sie **Weiter** aus.

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store.png" alt-text="Ein Screenshot, der die Konfiguration der Seite „Destination data store“ (Zieldatenspeicher) zeigt":::.

5. Geben Sie auf der Seite **Settings** (Einstellungen) unter **Taskname** den Namen **DeltaCopyFromBlobPipeline** ein, und klicken Sie dann auf **Weiter**. Über die Data Factory-Benutzeroberfläche wird eine Pipeline mit dem angegebenen Tasknamen erstellt.

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/settings-page.png" alt-text="Screenshot, der die Konfiguration der Seite „Einstellungen“ zeigt.":::

6. Überprüfen Sie auf der Seite **Zusammenfassung** die Einstellungen, und klicken Sie anschließend auf **Weiter**.

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png" alt-text="Seite „Zusammenfassung“":::

7. Wählen Sie auf der Seite **Bereitstellung** die Option **Überwachen** aus, um die Pipeline (Aufgabe) zu überwachen.
    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png" alt-text="Bereitstellungsseite":::

8. Beachten Sie, dass die Registerkarte **Überwachen** auf der linken Seite automatisch ausgewählt ist.  Sie müssen auf die Ausführung der Pipeline warten, wenn sie (etwa nach einer Stunde) automatisch ausgelöst wird. Wählen Sie während der Ausführung den Link auf dem Pipelinenamen **DeltaCopyFromBlobPipeline**, um Details zur Aktivitätsausführung aufzurufen oder die Pipeline erneut auszuführen. Klicken Sie zum Aktualisieren der Liste auf **Aktualisieren**.

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs-1.png" alt-text="Screenshot des Bereichs „Pipelineausführungen“":::
9. Da die Pipeline nur eine Aktivität (Copy-Aktivität) enthält, wird nur ein Eintrag angezeigt. Passen Sie die Breite der Spalten **Quelle** und **Ziel** bei Bedarf an, um weitere Details anzuzeigen. Ihnen wird angezeigt, dass die Quelldatei (file1.txt) mit demselben Dateinamen von *source/2021/07/15/06/* nach *destination/2021/07/15/06/* kopiert wurde. 

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png" alt-text="Screenshot der Details zur Pipelineausführung":::

    Sie können dies auch bestätigen, indem Sie den Azure Storage-Explorer (https://storageexplorer.com/) ) verwenden, um die Dateien zu überprüfen.

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png" alt-text="Screenshot der Details zur Pipelineausführung für das Ziel":::

10. Erstellen Sie eine weitere leere Textdatei, und nennen Sie sie **file2.txt**. Laden Sie die Datei „file2.txt“ in den Ordnerpfad **source/2021/07/15/07** in Ihrem Speicherkonto hoch. Sie können für diese Aufgaben verschiedene Tools verwenden, etwa [Azure Storage-Explorer](https://storageexplorer.com/).

    > [!NOTE]
    > Möglicherweise muss ein neuer Ordnerpfad erstellt werden. Passen Sie den Ordnernamen an Ihre UTC-Zeit an.  Wenn zum Beispiel die aktuelle koordinierte Weltzeit (UTC) am 15. Juli 2021 7:30 Uhr lautet, können Sie gemäß dem Format **{Jahr}/{Monat}/{Tag}/{Stunde}/** den Ordnerpfad **source/2021/07/15/07/** erstellen.

11. Wählen Sie **All Pipelines runs** (Alle Pipelineausführungen) aus, und warten Sie, bis die gleiche Pipeline nach einer Stunde wiederholt automatisch ausgelöst wird, um zur Ansicht **Pipeline Runs** (Pipelineausführungen) zurückzukehren.  

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png" alt-text="Screenshot des Links „Alle Pipelineausführungen“ für die Rückkehr zu dieser Seite":::

12. Klicken Sie für die zweite Pipelineausführung auf den neuen Link **DeltaCopyFromBlobPipeline**, wenn es soweit ist, und tun Sie dasselbe, um Details anzuzeigen. Ihnen wird angezeigt, dass die Quelldatei (file2.txt) mit demselben Dateinamen von **source/2021/07/15/07/** nach **destination/2021/07/15/07/** kopiert wurde. Sie können dies auch überprüfen, indem Sie den Azure Storage-Explorer (https://storageexplorer.com/) ) verwenden, um die Dateien im Container **destination** zu überprüfen.


## <a name="next-steps"></a>Nächste Schritte
Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie mithilfe eines Spark-Clusters in Azure Daten transformieren:

> [!div class="nextstepaction"]
>[Transform data using Spark cluster in cloud (Transformieren von Daten mit Spark-Cluster in der Cloud)](tutorial-transform-data-spark-portal.md).
