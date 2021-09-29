---
title: Datentool zum schrittweisen Kopieren neuer und aktualisierter Dateien
description: Erstellen Sie eine Azure Data Factory, und verwenden Sie dann das Tool zum Kopieren von Daten, um neue Dateien auf Basis von „LastModifiedDate“ inkrementell zu laden.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/12/2021
ms.openlocfilehash: bd9bfff8faf8f5c5e2c81ea9a8abea23af0da6ca
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124771648"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Inkrementelles Kopieren neuer und geänderter Dateien auf Basis von LastModifiedDate und mithilfe des Tools zum Kopieren von Daten

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Tutorial verwenden Sie das Azure-Portal, um eine Data Factory zu erstellen. Anschließend verwenden Sie das Tool zum Kopieren von Daten, um eine Pipeline zu erstellen, die neue und geänderte Dateien inkrementell von einem in den anderen Azure-Blobspeicher kopiert. Hierbei wird `LastModifiedDate` verwendet, um die zu kopierenden Dateien zu bestimmen.

Nachdem Sie die hier beschriebenen Schritte ausgeführt haben, überprüft Azure Data Factory alle Dateien im Quellspeicher, wendet den Filter über `LastModifiedDate` an und kopiert nur diejenigen Dateien in den Zielspeicher, die seit dem letzten Mal neu erstellt oder aktualisiert wurden. Beachten Sie, dass Data Factory zum Überprüfen einer großen Anzahl von Dateien dennoch sehr viel Zeit benötigt. Die Dateiüberprüfung ist sehr zeitintensiv – selbst dann, wenn die Menge der kopierten Daten verringert wird.

> [!NOTE]
> Falls Sie noch nicht mit Data Factory vertraut sind, ist es ratsam, den Artikel [Einführung in Azure Data Factory](introduction.md) zu lesen.

Dieses Tutorial umfasst folgende Aufgaben:

> [!div class="checklist"]
> * Erstellen einer Data Factory.
> * Erstellen einer Pipeline mithilfe des Tools zum Kopieren von Daten
> * Überwachen der Pipeline- und Aktivitätsausführungen.

## <a name="prerequisites"></a>Voraussetzungen

* **Azure-Abonnement**: Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* **Azure Storage-Konto**: Verwenden Sie den Blobspeicher als Quell- und als Senkendatenspeicher. Falls Sie noch nicht über ein Azure Storage-Konto verfügen, finden Sie [hier](../storage/common/storage-account-create.md) eine Anleitung zum Erstellen eines Speicherkontos.

## <a name="create-two-containers-in-blob-storage"></a>Erstellen von zwei Containern im Blobspeicher

Bereiten Sie Ihren Blobspeicher folgendermaßen für das Tutorial vor:

1. Erstellen Sie einen Container mit dem Namen **source** (Quelle). Sie können für diese Aufgabe verschiedene Tools verwenden, z. B. [Azure Storage-Explorer](https://storageexplorer.com/).

2. Erstellen Sie einen Container mit dem Namen **destination** (Ziel).

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory

1. Wählen Sie im linken Bereich **Ressource erstellen**. Wählen Sie **Integration** > **Data Factory** aus:

   :::image type="content" source="./media/doc-common-process/new-azure-data-factory-menu.png" alt-text="Data Factory auswählen":::

2. Geben Sie auf der Seite **Neue Data Factory** unter **Name** den Namen **ADFTutorialDataFactory** ein.

   Der Name der Data Factory muss global eindeutig sein. Möglicherweise wird diese Fehlermeldung angezeigt:

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Fehlermeldung zu neuer Data Factory für doppelten Namen":::

   Wenn eine Fehlermeldung zum Namenswert angezeigt wird, geben Sie einen anderen Namen für die Data Factory ein. Verwenden Sie beispielsweise den Namen _**IhrName**_ **ADFTutorialDataFactory**. Benennungsregeln für Data Factory-Artefakte finden Sie im Thema [Data Factory – Benennungsregeln](naming-rules.md).
3. Wählen Sie unter **Abonnement** das Azure-Abonnement aus, in dem Sie die neue Data Factory erstellen werden.
4. Führen Sie unter **Ressourcengruppe** einen der folgenden Schritte aus:

    * Wählen Sie die Option **Vorhandene verwenden** und dann in der Liste eine vorhandene Ressourcengruppe aus.

    * Wählen Sie **Neu erstellen** aus, und geben Sie einen Namen für die Ressourcengruppe ein.
         
    Weitere Informationen zu Ressourcengruppen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-resource-manager/management/overview.md).

5. Wählen Sie unter **Version** die Option **V2**.
6. Wählen Sie unter **Standort** den Standort für die Data Factory aus. In der Liste werden nur unterstützte Standorte angezeigt. Die von der Data Factory genutzten Datenspeicher (z. B. Azure Storage und Azure SQL-Datenbank) und Computeressourcen (z. B. Azure HDInsight) können sich an anderen Standorten und in anderen Regionen befinden.
8. Klicken Sie auf **Erstellen**.
9. Nachdem die Data Factory erstellt wurde, wird die Startseite für die Data Factory angezeigt.
10. Zum Öffnen der Azure Data Factory-Benutzeroberfläche wählen Sie auf der Kachel **Open Azure Data Factory Studio** die Option **Öffnen** aus.

    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Startseite für Azure Data Factory mit der Kachel „Open Azure Data Factory Studio“":::

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Erstellen einer Pipeline mithilfe des Tools zum Kopieren von Daten

1. Wählen Sie auf der Azure Data Factory-Homepage die Kachel **Erfassen**, um das Tool zum Kopieren der Daten zu öffnen.

   :::image type="content" source="./media/doc-common-process/get-started-page.png" alt-text="Screenshot, der die ADF-Startseite zeigt.":::

2. Gehen Sie auf der Seite **Properties** (Eigenschaften) wie folgt vor:

    1. Wählen Sie unter **Task type** (Aufgabentyp) **Built-in copy task** (Integrierte Kopieraufgabe) aus.

    1. Wählen Sie unter **Task cadence or task schedule** (Aufgabenhäufigkeit oder Aufgabenzeitplan) **Tumbling window** (Rollierendes Fenster) aus.

    1. Geben Sie unter **Wiederholung** den Wert **15 Minuten** ein.

    1. Wählen Sie **Weiter** aus.

    :::image type="content" source="./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png" alt-text="Bereich „Eigenschaften“ von „Daten kopieren“":::

3. Führen Sie auf der Seite **Quelldatenspeicher** die folgenden Schritte aus:

    1. Wählen Sie **+ Neue Verbindung** aus, um eine Verbindung hinzuzufügen.

    1. Wählen Sie im Katalog **Azure Blob Storage** aus, und klicken Sie dann auf **Weiter**:

        :::image type="content" source="./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png" alt-text="Azure-Blobspeicher auswählen":::

    1. Wählen Sie auf der Seite **Neue Verbindung (Azure Blob Storage)** in der Liste **Azure-Abonnement** Ihr Azure-Abonnement und in der Liste **Speicherkontoname** Ihr Speicherkonto aus. Testen Sie die Verbindung, und klicken Sie auf **Erstellen**.

    1. Wählen Sie im Block **Verbindung** die neu erstellte Verbindung aus.

    1. Wählen Sie im Abschnitt **File or folder** (Datei oder Ordner) **Browse** (Durchsuchen) und wählen sie den Ordner **source** (Quelle) aus. Wählen Sie anschließend **OK**.

    1. Wählen Sie unter **File loading behavior** (Dateiladeverhalten) **Incremental load: LastModifiedDate** (Inkrementelles Laden: LastModifiedDate) aus und wählen Sie anschließend **Binary copy** (Binärkopie) aus.
    
    1. Wählen Sie **Weiter** aus.

    :::image type="content" source="./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page.png" alt-text="Screenshot, der die Seite „Quelldatenspeicher“ zeigt":::.

4. Führen Sie auf der Seite **Destination data store** (Zieldatenspeicher) die folgenden Schritte aus:
    1. Wählen Sie die **AzureBlobStorage**-Verbindung aus, die Sie erstellt haben. Dies ist dasselbe Speicherkonto wie der Quelldatenspeicher.

    1. Suchen Sie im Abschnitt **Folder path** (Ordnerpfad) nach dem Ordner **destination** (Ziel), wählen Sie ihn aus und klicken Sie anschließend auf **OK**.

    1. Wählen Sie **Weiter** aus.

    :::image type="content" source="./media/tutorial-incremental-copy-lastmodified-copy-data-tool/destination-data-store-page.png" alt-text="Screenshot, der die Seite „Zieldatenspeicher“ zeigt.":::

5. Geben Sie auf der Seite **Einstellungen** unter **Taskname** den Namen **DeltaCopyFromBlobPipeline** ein, und klicken Sie dann auf **Weiter**. Über die Data Factory-Benutzeroberfläche wird eine Pipeline mit dem angegebenen Aufgabennamen erstellt.

    :::image type="content" source="./media/tutorial-incremental-copy-lastmodified-copy-data-tool/settings-page.png" alt-text="Screenshot, der die Seite „Einstellungen“ zeigt.":::

6. Überprüfen Sie auf der Seite **Zusammenfassung** die Einstellungen, und klicken Sie anschließend auf **Weiter**.

    :::image type="content" source="./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png" alt-text="Seite „Zusammenfassung“":::

7. Wählen Sie auf der Seite **Bereitstellung** die Option **Überwachen** aus, um die Pipeline (Aufgabe) zu überwachen.

    :::image type="content" source="./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png" alt-text="Bereitstellungsseite":::

8. Beachten Sie, dass die Registerkarte **Überwachen** auf der linken Seite automatisch ausgewählt ist. Die Anwendung wechselt zur Registerkarte **Überwachen**. Dort wird der Status der Pipeline angezeigt. Klicken Sie zum Aktualisieren der Liste auf **Aktualisieren**. Klicken Sie auf den Link unter **Pipelinename**, um Details zur Aktivitätsausführung anzuzeigen oder die Pipeline erneut auszuführen.

    :::image type="content" source="./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs-1.png" alt-text="Aktualisieren der Liste und Anzeigen von Details zur Aktivitätsausführung":::

9. Da die Pipeline nur eine Aktivität (die Kopieraktivität) enthält, wird nur ein Eintrag angezeigt. Wenn Sie Details zum Kopiervorgang anzeigen möchten, wählen Sie auf der Seite **Activity runs** (Aktivitätsausführungen) den Link **Details** (das Brillensymbol) aus der Spalte **Activity name** (Aktivitätsname) aus. Einzelheiten zu den Eigenschaften finden Sie in der [Übersicht zur Kopieraktivität](copy-activity-overview.md).

    :::image type="content" source="./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png" alt-text="Kopieraktivität in der Pipeline":::

    Da sich im Quellcontainer in Ihrem Blobspeicherkonto keine Datei befindet, wird in Ihrem Blobspeicherkonto keine Datei angezeigt, die in den Zielcontainer kopiert wurde:

    :::image type="content" source="./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png" alt-text="Keine Dateien in Quell- oder Zielcontainer":::

10. Erstellen Sie eine leere Textdatei, und nennen Sie sie **file1.txt**. Laden Sie diese Textdatei in den Quellcontainer in Ihrem Speicherkonto hoch. Sie können für diese Aufgaben verschiedene Tools verwenden, beispielsweise [Azure Storage-Explorer](https://storageexplorer.com/).

    :::image type="content" source="./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png" alt-text="Erstellen von „file1.txt“ und Hochladen in den Quellcontainer":::

11. Wenn Sie zur Ansicht **Pipelineausführungen** zurückkehren möchten, wählen Sie den Link **Alle Pipelineausführungen** im Breadcrumb-Menü auf der Seite **Aktivitätsausführungen** aus, und warten Sie, bis dieselbe Pipeline erneut automatisch ausgelöst wird.  

12. Wenn die zweite Pipelineausführung abgeschlossen ist, führen Sie die zuvor genannten Schritte aus, um die Details zur Aktivitätsausführung zu überprüfen.  

    Sie werden sehen, dass eine Datei („file1.txt“) aus dem Quellcontainer in den Zielcontainer Ihres Blobspeicherkontos kopiert wurde:

    :::image type="content" source="./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png" alt-text="Datei „file1.txt“ wurde aus dem Quellcontainer in den Zielcontainer kopiert":::

13. Erstellen Sie eine weitere leere Textdatei, und benennen Sie diese **file2.txt**. Laden Sie diese Textdatei in den Quellcontainer in Ihrem Blobspeicherkonto hoch.

14. Wiederholen Sie die Schritte 11 und 12 für die zweite Textdatei. Sie werden bemerken, dass nur die neue Datei („file2.txt“) bei der nächsten Pipelineausführung vom Quellcontainer in den Zielcontainer Ihres Speicherkontos kopiert wurde.  

    Sie können dies auch bestätigen, indem Sie die Dateien mit dem [Azure Storage-Explorer](https://storageexplorer.com/) überprüfen:

    :::image type="content" source="./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png" alt-text="Überprüfen von Dateien mit dem Azure Storage-Explorer":::

## <a name="next-steps"></a>Nächste Schritte
Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie mithilfe eines Apache Spark-Clusters in Azure Daten transformieren:

> [!div class="nextstepaction"]
>[Transformieren von Daten in der Cloud mithilfe von Apache Spark-Clustern](tutorial-transform-data-spark-portal.md)
