---
title: Datenintegration mit Azure Data Factory und Azure Data Share
description: Kopieren, Transformieren und Freigeben von Daten mit Azure Data Factory und Azure Data Share
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.subservice: tutorials
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 06/04/2021
ms.openlocfilehash: 4d99139e708f0b69b3ddacd40e380853dabf8b2b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131077950"
---
# <a name="data-integration-using-azure-data-factory-and-azure-data-share"></a>Datenintegration mit Azure Data Factory und Azure Data Share

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Kunden benötigen für ihre modernen Data Warehouse- und Analyseprojekte nicht nur mehr Daten, sondern für den gesamten Datenbestand auch einen besseren Einblick und Transparenz. In diesem Workshop wird beschrieben, wie die Datenintegration und -verwaltung in Azure aufgrund der Verbesserungen, die an Azure Data Factory und Azure Data Share vorgenommen wurden, vereinfacht wird. 

Von der Aktivierung von ETL/ELT-Vorgängen ohne Code bis zur Ermöglichung eines umfassenden Überblicks über Ihre Daten: Dank der Verbesserungen in Azure Data Factory können Ihre Datentechniker problemlos größere Datenmengen verarbeiten und so den Nutzen für Ihr Unternehmen erhöhen. Mit Azure Data Share haben Sie die volle Kontrolle über die B2B-Freigabe.

In diesem Workshop nutzen Sie Azure Data Factory (ADF) zum Erfassen von Daten aus Azure SQL-Datenbank in Azure Data Lake Storage Gen2 (ADLS Gen2). Nachdem die Daten im Lake angeordnet wurden, transformieren Sie sie mit Zuordnungsdatenflüssen (nativer Data Factory-Transformationsdienst) und binden sie in Azure Synapse Analytics ein. Anschließend geben Sie die Tabelle mit transformierten Daten sowie einigen zusätzlichen Daten per Azure Data Share frei. 

Bei den in diesem Lab verwendeten Daten handelt es sich um New York City-Taxidaten. Laden Sie die [BACPAC-Datei „taxi-data“](https://github.com/djpmsft/ADF_Labs/blob/master/sample-data/taxi-data.bacpac) herunter, um diese Daten in Ihre Datenbank in Azure SQL-Datenbank zu importieren.

## <a name="prerequisites"></a>Voraussetzungen

* **Azure-Abonnement**: Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

* **Azure SQL-Datenbank**: Falls Sie nicht über eine SQL-Datenbank verfügen, können Sie sich über das [Erstellen eines SQL DB-Kontos](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal) informieren.

* **Azure Data Lake Storage Gen2-Speicherkonto**: Falls Sie nicht über ein ADLS Gen2-Speicherkonto verfügen, können Sie sich über das [Erstellen eines ADLS Gen2-Speicherkontos](../storage/common/storage-account-create.md) informieren.

* **Azure Synapse Analytics**: Wenn Sie nicht über eine Azure Synapse Analytics-Instanz verfügen, können Sie sich über das [Erstellen einer Azure Synapse Analytics-Instanz](../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md) informieren.

* **Azure Data Factory**: Informieren Sie sich über das [Erstellen einer Data Factory](./quickstart-create-data-factory-portal.md), falls Sie noch keine Data Factory erstellt haben.

* **Azure Data Share**: Informieren Sie sich über das [Erstellen einer Data Share-Ressource](../data-share/share-your-data.md#create-a-data-share-account), falls Sie noch keine Data Share-Ressource erstellt haben.

## <a name="set-up-your-azure-data-factory-environment"></a>Einrichten Ihrer Azure Data Factory-Umgebung

In diesem Abschnitt wird beschrieben, wie Sie über das Azure-Portal auf die Benutzeroberfläche von Azure Data Factory (ADF UX) zugreifen. Auf der ADF-Benutzeroberfläche konfigurieren Sie drei verknüpfte Dienste für die von uns genutzten Datenspeicher: Azure SQL-Datenbank, ADLS Gen2 und Azure Synapse Analytics.

Unter den verknüpften Azure Data Factory-Diensten definieren Sie die Informationen für Verbindungen mit externen Ressourcen. Azure Data Factory unterstützt derzeit mehr als 85 Connectors.

### <a name="open-the-azure-data-factory-ux"></a>Öffnen der Azure Data Factory-Benutzeroberfläche

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com) in Microsoft Edge oder Google Chrome.
1. Verwenden Sie die Suchleiste oben auf der Seite, um nach „Data Factorys“ zu suchen.

    :::image type="content" source="media/lab-data-flow-data-share/portal1.png" alt-text="Portal 1":::
1. Klicken Sie auf Ihre Data Factory-Ressource, um das zugehörige Ressourcenblatt zu öffnen.

    :::image type="content" source="media/lab-data-flow-data-share/portal2.png" alt-text="Portal 2":::
1. Klicken Sie auf **Erstellen und überwachen**, um die ADF-Benutzeroberfläche zu öffnen. Sie können auch unter „adf.azure.com“ auf die ADF-Benutzeroberfläche zugreifen.

    :::image type="content" source="media/lab-data-flow-data-share/portal3.png" alt-text="Portal 3":::
1. Sie werden auf die Startseite der ADF-Benutzeroberfläche umgeleitet. Diese Seite enthält Schnellstartanleitungen, Lehrvideos und Links zu Tutorials, damit Sie auf Informationen zu den Data Factory-Konzepten zugreifen können. Klicken Sie links in der Seitenleiste auf das Stiftsymbol, um mit der Erstellung zu beginnen.

    :::image type="content" source="./media/doc-common-process/get-started-page-author-button.png" alt-text="Portal: Konfigurieren":::

### <a name="create-an-azure-sql-database-linked-service"></a>Erstellen eines verknüpften Azure SQL-Datenbank-Diensts

1. Wählen Sie zum Erstellen eines verknüpften Diensts auf der linken Seitenleiste den Hub **Verwalten** und im Bereich **Verbindungen** die Option **Verknüpfte Dienste** aus. Wählen Sie anschließend **Neu** aus, um einen neuen verknüpften Dienst hinzuzufügen.

    :::image type="content" source="media/lab-data-flow-data-share/configure2.png" alt-text="Portal: Konfigurieren 2":::
1. Der erste verknüpfte Dienst, den Sie konfigurieren, ist eine Azure SQL-Datenbank. Sie können die Suchleiste verwenden, um die Datenspeicherliste zu filtern. Klicken Sie auf die Kachel **Azure SQL-Datenbank** und dann auf „Weiter“.

    :::image type="content" source="media/lab-data-flow-data-share/configure-4.png" alt-text="Portal: Konfigurieren 4":::
1. Geben Sie im Bereich für die SQL DB-Konfiguration „SQLDB“ als Namen für Ihren verknüpften Dienst ein. Geben Sie Ihre Anmeldeinformationen ein, damit Data Factory eine Verbindung mit Ihrer Datenbank herstellen kann. Geben Sie bei Verwendung der SQL-Authentifizierung den Servernamen, die Datenbank, Ihren Benutzernamen und das Kennwort ein. Sie können die Korrektheit Ihrer Verbindungsinformationen überprüfen, indem Sie auf **Verbindung testen** klicken. Klicken Sie auf **Erstellen**, nachdem der Vorgang abgeschlossen wurde.

    :::image type="content" source="media/lab-data-flow-data-share/configure5.png" alt-text="Portal: Konfigurieren 5":::

### <a name="create-an-azure-synapse-analytics-linked-service"></a>Erstellen eines verknüpften Azure Synapse Analytics-Diensts

1. Wiederholen Sie den Vorgang, um einen verknüpften Azure Synapse Analytics-Dienst hinzuzufügen. Klicken Sie auf der Registerkarte „Verbindungen“ auf **Neu**. Wählen Sie die Kachel **Azure Synapse Analytics** aus, und klicken Sie auf „Weiter“.

    :::image type="content" source="media/lab-data-flow-data-share/configure-6.png" alt-text="Portal: Konfigurieren 6":::
1. Geben Sie im Konfigurationsbereich für verknüpfte Dienste „SQLDW“ als Namen für den verknüpften Dienst ein. Geben Sie Ihre Anmeldeinformationen ein, damit Data Factory eine Verbindung mit Ihrer Datenbank herstellen kann. Geben Sie bei Verwendung der SQL-Authentifizierung den Servernamen, die Datenbank, Ihren Benutzernamen und das Kennwort ein. Sie können die Korrektheit Ihrer Verbindungsinformationen überprüfen, indem Sie auf **Verbindung testen** klicken. Klicken Sie auf **Erstellen**, nachdem der Vorgang abgeschlossen wurde.

    :::image type="content" source="media/lab-data-flow-data-share/configure-7.png" alt-text="Portal: Konfigurieren 7":::

### <a name="create-an-azure-data-lake-storage-gen2-linked-service"></a>Erstellen eines verknüpften Azure Data Lake Storage Gen2-Diensts

1. Der letzte verknüpfte Dienst, der für dieses Lab benötigt wird, ist Azure Data Lake Storage Gen2.  Klicken Sie auf der Registerkarte „Verbindungen“ auf **Neu**. Wählen Sie die Kachel **Azure Data Lake Storage Gen2** aus, und klicken Sie auf „Weiter“.

    :::image type="content" source="media/lab-data-flow-data-share/configure8.png" alt-text="Portal: Konfigurieren 8":::
1. Geben Sie im Konfigurationsbereich für den verknüpften Dienst „ADLSGen2“ als Namen für den verknüpften Dienst ein. Wählen Sie bei Verwendung der Kontoschlüsselauthentifizierung in der Dropdownliste **Speicherkontoname** Ihr ADLS Gen2-Speicherkonto aus. Sie können die Korrektheit Ihrer Verbindungsinformationen überprüfen, indem Sie auf **Verbindung testen** klicken. Klicken Sie auf **Erstellen**, nachdem der Vorgang abgeschlossen wurde.

    :::image type="content" source="media/lab-data-flow-data-share/configure9.png" alt-text="Portal: Konfigurieren 9":::

### <a name="turn-on-data-flow-debug-mode"></a>Aktivieren des Debugmodus für Datenflüsse

Im Abschnitt *Transformieren von Daten per Zuordnung von Datenflüssen* erstellen Sie Zuordnungsdatenflüsse. Eine bewährte Methode vor dem Erstellen von Zuordnungsdatenflüssen ist das Aktivieren des Debugmodus. Dies ermöglicht Ihnen das Testen der Transformationslogik innerhalb weniger Sekunden in einem aktiven Spark-Cluster.

Klicken Sie zum Aktivieren des Debuggens auf der oberen Leiste der Datenfluss- oder Pipelinecanvas auf den Schieberegler **Datenflüsse debuggen**, wenn **Datenflussaktivitäten** vorhanden sind. Klicken Sie auf **OK**, wenn das Bestätigungsdialogfeld angezeigt wird. Der Cluster wird in etwa fünf bis sieben Minuten gestartet. Fahren Sie mit dem *Erfassen von Daten aus Azure SQL-Datenbank in ADLS Gen2 per Copy-Aktivität* fort, während der Initialisierungsvorgang läuft.

:::image type="content" source="media/lab-data-flow-data-share/configure10.png" alt-text="Portal: Konfigurieren 10":::

:::image type="content" source="media/lab-data-flow-data-share/configure-11.png" alt-text="Screenshot: Position des Schiebereglers „Datenflüsse debuggen“":::

## <a name="ingest-data-using-the-copy-activity"></a>Erfassen von Daten mithilfe der Kopieraktivität

In diesem Abschnitt erstellen Sie eine Pipeline mit einer Copy-Aktivität, bei der eine Tabelle aus einer Azure SQL-Datenbank in einem ADLS Gen2-Speicherkonto erfasst wird. Es wird beschrieben, wie Sie eine Pipeline hinzufügen, ein Dataset konfigurieren und eine Pipeline über die ADF-Benutzeroberfläche debuggen. Das in diesem Abschnitt verwendete Konfigurationsmuster kann auf das Kopieren aus einem relationalen Datenspeicher in einen dateibasierten Datenspeicher angewendet werden.

In Azure Data Factory ist eine Pipeline eine logische Gruppierung von Aktivitäten, die zusammen eine Aufgabe bilden. Mit einer Aktivität wird ein Vorgang definiert, der für Ihre Daten durchgeführt werden soll. Ein Dataset verweist auf die Daten, die Sie in einem verknüpften Dienst verwenden möchten.

### <a name="create-a-pipeline-with-a-copy-activity"></a>Erstellen einer Pipeline mit einer Kopieraktivität

1. Klicken Sie im Bereich mit den Factory-Ressourcen auf das Plussymbol, um das Menü „Neue Ressource“ zu öffnen. Wählen Sie **Pipeline** aus.

    :::image type="content" source="media/lab-data-flow-data-share/copy1.png" alt-text="Portal: Kopieren 1":::
1. Geben Sie Ihrer Pipeline in der Pipelinecanvas auf der Registerkarte **Allgemein** einen beschreibenden Namen, z. B. „IngestAndTransformTaxiData“.

    :::image type="content" source="media/lab-data-flow-data-share/copy2.png" alt-text="Portal: Kopieren 2":::
1. Öffnen Sie auf der Pipelinecanvas unter „Aktivitäten“ den Accordion-Bereich **Move and Transform** (Verschieben und transformieren), und ziehen Sie die Aktivität **Daten kopieren** in die Canvas. Geben Sie der Kopieraktivität einen beschreibenden Namen, z. B. „IngestIntoADLS“.

    :::image type="content" source="media/lab-data-flow-data-share/copy3.png" alt-text="Portal: Kopieren 3":::

### <a name="configure-azure-sql-db-source-dataset"></a>Konfigurieren eines Quelldatasets für die Azure SQL-Datenbank

1. Klicken Sie in der Kopieraktivität auf die Registerkarte **Quelle**. Klicken Sie auf **Neu**, um ein neues Dataset zu erstellen. Ihre Quelle ist die Tabelle „dbo.TripData“, die sich unter dem weiter oben konfigurierten verknüpften Dienst „SQLDB“ befindet.

    :::image type="content" source="media/lab-data-flow-data-share/copy4.png" alt-text="Portal: Kopieren 4":::
1. Suchen Sie nach **Azure SQL-Datenbank**, und klicken Sie auf „Weiter“.

    :::image type="content" source="media/lab-data-flow-data-share/copy-5.png" alt-text="Portal: Kopieren 5":::
1. Geben Sie Ihrem Dataset den Namen „TripData“. Wählen Sie „SQLDB“ als verknüpften Dienst aus. Wählen Sie in der Dropdownliste mit den Tabellennamen den Eintrag „dbo.TripData“ aus. Importieren Sie das Schema **Aus Verbindung/Speicher**. Klicken Sie auf „OK“, wenn der Vorgang abgeschlossen ist.

    :::image type="content" source="media/lab-data-flow-data-share/copy6.png" alt-text="Portal: Kopieren 6":::

Sie haben Ihr Quelldataset erfolgreich erstellt. Stellen Sie in den Quelleinstellungen sicher, dass im Feld für „Abfrage verwenden“ der Standardwert **Tabelle** ausgewählt ist.

### <a name="configure-adls-gen2-sink-dataset"></a>Konfigurieren des ADLS Gen2-Senkendatasets

1. Klicken Sie in der Kopieraktivität auf die Registerkarte **Senke**. Klicken Sie auf **Neu**, um ein neues Dataset zu erstellen.

    :::image type="content" source="media/lab-data-flow-data-share/copy7.png" alt-text="Portal: Kopieren 7":::
1. Suchen Sie nach **Azure Data Lake Storage Gen2**, und klicken Sie auf „Weiter“.

    :::image type="content" source="media/lab-data-flow-data-share/copy8.png" alt-text="Portal: Kopieren 8":::
1. Wählen Sie im ausgewählten Formatbereich beim Schreiben in eine CSV-Datei die Option **DelimitedText**. Klicken Sie auf „Weiter“.

    :::image type="content" source="media/lab-data-flow-data-share/copy9.png" alt-text="Portal: Kopieren 9":::
1. Geben Sie Ihrem Senkendataset den Namen „TripDataCSV“. Wählen Sie „ADLSGen2“ als verknüpften Dienst aus. Geben Sie ein, an welchen Speicherort Ihre CSV-Datei geschrieben werden soll. Sie können Ihre Daten beispielsweise in die Datei `trip-data.csv` im Container `staging-container` schreiben. Legen Sie **Erste Zeile als Header verwenden** auf „true“ fest, da Ihre Ausgabedaten Header enthalten sollen. Legen Sie **Schema importieren** auf **Keine** fest, da am Zielort noch keine Datei vorhanden ist. Klicken Sie auf „OK“, wenn der Vorgang abgeschlossen ist.

    :::image type="content" source="media/lab-data-flow-data-share/copy10.png" alt-text="Portal: Kopieren 10":::

### <a name="test-the-copy-activity-with-a-pipeline-debug-run"></a>Testen der Kopieraktivität mit einer Debugausführung der Pipeline

1. Klicken Sie zum Überprüfen, ob Ihre Kopieraktivität richtig funktioniert, oben in der Pipelinecanvas auf **Debuggen**, um eine Debugausführung durchzuführen. Mit einer Debugausführung können Sie Ihre Pipeline entweder von Anfang bis Ende oder bis zu einem Breakpoint testen, bevor Sie sie im Data Factory-Dienst veröffentlichen.

    :::image type="content" source="media/lab-data-flow-data-share/copy11.png" alt-text="Portal: Kopieren 11":::
1. Navigieren Sie in der Pipelinecanvas zur Registerkarte **Ausgabe**, um Ihre Debugausführung zu überwachen. Der Überwachungsbildschirm wird alle 20 Sekunden automatisch aktualisiert (oder wenn Sie auf die Schaltfläche „Aktualisieren“ klicken). Die Copy-Aktivität verfügt über eine spezielle Überwachungsansicht, auf die Sie zugreifen können, indem Sie in der Spalte **Aktionen** auf das Brillensymbol klicken.

    :::image type="content" source="media/lab-data-flow-data-share/copy12.png" alt-text="Portal: Kopieren 12":::
1. Die Überwachungsansicht für den Kopiervorgang enthält die Ausführungsdetails und Leistungsmerkmale zur Aktivität. Sie können Informationen zu „gelesene/geschriebene Daten“, „gelesene/geschriebene Zeilen“, „gelesene/geschriebene Dateien“ und „Durchsatz“ anzeigen. Wenn Sie alles richtig konfiguriert haben, sollten Sie verfolgen können, dass 49.999 Zeilen in eine Datei in Ihrer ADLS-Senke geschrieben werden.

    :::image type="content" source="media/lab-data-flow-data-share/copy13.png" alt-text="Portal: Kopieren 13":::
1. Bevor Sie mit dem nächsten Abschnitt fortfahren, wird das Veröffentlichen Ihrer Änderungen über den Data Factory-Dienst vorgeschlagen. Hierzu klicken Sie in der oberen Factory-Leiste auf **Alle veröffentlichen**. In diesem Lab ist keine Beschreibung enthalten, aber für Azure Data Factory wird die vollständige Git-Integration unterstützt. Die Git-Integration ermöglicht Versionskontrolle, iteratives Speichern in einem Repository und Zusammenarbeit über eine Data Factory. Weitere Informationen finden Sie unter [Quellcodeverwaltung in Azure Data Factory](./source-control.md#troubleshooting-git-integration).

    :::image type="content" source="media/lab-data-flow-data-share/publish1.png" alt-text="Portal: Veröffentlichen 1":::

## <a name="transform-data-using-mapping-data-flow"></a>Transformieren von Daten per Zuordnung von Datenflüssen

Nachdem Sie Daten nun erfolgreich in Azure Data Lake Storage kopiert haben, können Sie diese Daten verknüpfen und in einem Data Warehouse aggregieren. Hierfür verwenden wir Zuordnungsdatenflüsse. Dies ist der visuell konzipierte Transformationsdienst von Azure Data Factory. Mit Zuordnungsdatenflüssen können Benutzer Transformationslogik ohne Code entwickeln und diese in Spark-Clustern ausführen, die vom ADF-Dienst verwaltet werden.

Mit dem in diesem Schritt erstellten Datenfluss wird für das Dataset „TripDataCSV“ aus dem vorherigen Abschnitt basierend auf vier Schlüsselspalten ein Vorgang vom Typ „Innerer Join“ mit der Tabelle „dbo.TripFares“ durchgeführt, die in „SQLDB“ gespeichert ist. Anschließend werden die Daten basierend auf der Spalte `payment_type` aggregiert, um den Mittelwert für bestimmte Felder zu berechnen, und in eine Azure Synapse Analytics-Tabelle geschrieben.

### <a name="add-a-data-flow-activity-to-your-pipeline"></a>Hinzufügen einer Datenflussaktivität zu Ihrer Pipeline

1. Öffnen Sie auf der Pipelinecanvas unter „Aktivitäten“ den Accordion-Bereich **Move and Transform** (Verschieben und transformieren), und ziehen Sie die Aktivität **Datenfluss** in die Canvas.

    :::image type="content" source="media/lab-data-flow-data-share/dataflow1.png" alt-text="Portal: Datenfluss 1":::
1. Wählen Sie im daraufhin geöffneten Seitenbereich die Option **Neuen Datenfluss erstellen** und dann **Zuordnungsdatenfluss** aus. Klicken Sie auf **OK**.

    :::image type="content" source="media/lab-data-flow-data-share/dataflow2.png" alt-text="Portal: Datenfluss 2":::
1. Sie werden an die Datenflusscanvas weitergeleitet, in der Sie Ihre Transformationslogik erstellen. Geben Sie Ihrem Datenfluss auf der Registerkarte „Allgemein“ den Namen „JoinAndAggregateData“.

    :::image type="content" source="media/lab-data-flow-data-share/dataflow3.png" alt-text="Portal: Datenfluss 3":::

### <a name="configure-your-trip-data-csv-source"></a>Konfigurieren der CSV-Quelle für Ihre Fahrtdaten

1. Es ist ratsam, als Erstes Ihre beiden Quelltransformationen zu konfigurieren. Die erste Quelle verweist auf das Dataset „TripDataCSV“ vom Typ „DelimitedText“. Klicken Sie in der Canvas auf das Feld **Quelle hinzufügen**, um eine Quelltransformation hinzuzufügen.

    :::image type="content" source="media/lab-data-flow-data-share/dataflow4.png" alt-text="Portal: Datenfluss 4":::
1. Geben Sie Ihrer Quelle den Namen „TripDataCSV“, und wählen Sie in der Dropdownliste der Quelle das Dataset „TripDataCSV“ aus. Hinweis: Sie haben beim Erstellen dieses Datasets ursprünglich kein Schema importiert, weil keine Daten vorhanden waren. Da `trip-data.csv` jetzt vorhanden ist, können Sie auf **Bearbeiten** klicken, um zur Registerkarte „Dataseteinstellungen“ zu navigieren.

    :::image type="content" source="media/lab-data-flow-data-share/dataflow5.png" alt-text="Portal: Datenfluss 5":::
1. Navigieren Sie zur Registerkarte **Schema**, und klicken Sie auf **Schema importieren**. Wählen Sie **Aus Verbindung/Speicher**, um den direkten Import aus dem Dateispeicher durchzuführen. Es sollten 14 Spalten vom Typ „Zeichenfolge“ angezeigt werden.

    :::image type="content" source="media/lab-data-flow-data-share/dataflow6.png" alt-text="Portal: Datenfluss 6":::
1. Navigieren Sie zurück zum Datenfluss „JoinAndAggregateData“. Wenn Ihr Debugcluster gestartet wurde (grüner Kreis neben dem Debugschieberegler), können Sie auf der Registerkarte **Datenvorschau** eine Momentaufnahme der Daten anzeigen. Klicken Sie auf **Aktualisieren**, um eine Datenvorschau abzurufen.

    :::image type="content" source="media/lab-data-flow-data-share/dataflow7.png" alt-text="Portal: Datenfluss 7":::

> [!Note]
> Von der Datenvorschau werden keine Daten geschrieben.

### <a name="configure-your-trip-fares-sql-db-source"></a>Konfigurieren Ihrer Fahrpreise: Quelle für SQL-Datenbank

1. Die zweite Quelle, die Sie hinzufügen, verweist auf die Tabelle „dbo.TripFares“ der SQL-Datenbank. Unter der Quelle „TripDataCSV“ wird ein weiteres Feld **Quelle hinzufügen** angezeigt. Klicken Sie auf das Feld, um eine neue Quelltransformation hinzuzufügen.

    :::image type="content" source="media/lab-data-flow-data-share/dataflow8.png" alt-text="Portal: Datenfluss 8":::
1. Geben Sie dieser Quelle den Namen „TripFaresSQL“. Klicken Sie neben dem Feld mit dem Quelldataset auf **Neu**, um ein neues SQL DB-Dataset zu erstellen.

    :::image type="content" source="media/lab-data-flow-data-share/dataflow9.png" alt-text="Portal: Datenfluss 9":::
1. Wählen Sie die Kachel **Azure SQL-Datenbank** aus, und klicken Sie anschließend auf „Weiter“. *Hinweis: Unter Umständen stellen Sie fest, dass viele Connectors in Data Factory für Zuordnungsdatenflüsse nicht unterstützt werden. Erfassen Sie Daten per Kopieraktivität in einer unterstützten Quelle, um diese aus einer dieser Quellen zu transformieren*.

    :::image type="content" source="media/lab-data-flow-data-share/dataflow-10.png" alt-text="Portal: Datenfluss 10":::
1. Geben Sie Ihrem Dataset den Namen „TripFares“. Wählen Sie „SQLDB“ als verknüpften Dienst aus. Wählen Sie in der Dropdownliste mit den Tabellennamen den Eintrag „dbo.TripFares“ aus. Importieren Sie das Schema **Aus Verbindung/Speicher**. Klicken Sie auf „OK“, wenn der Vorgang abgeschlossen ist.

    :::image type="content" source="media/lab-data-flow-data-share/dataflow11.png" alt-text="Portal: Datenfluss 11":::
1. Rufen Sie zum Überprüfen Ihrer Daten auf der Registerkarte **Datenvorschau** eine Datenvorschau ab.

    :::image type="content" source="media/lab-data-flow-data-share/dataflow12.png" alt-text="Portal: Datenfluss 12":::

### <a name="inner-join-tripdatacsv-and-tripfaressql"></a>Innerer Join: TripDataCSV und TripFaresSQL

1. Klicken Sie zum Hinzufügen einer neuen Transformation in „TripDataCSV“ unten rechts auf das Plussymbol. Wählen Sie unter **Multiple inputs/outputs** (Mehrere Eingaben/Ausgaben) die Option **Join** aus.

    :::image type="content" source="media/lab-data-flow-data-share/join1.png" alt-text="Portal: Join 1":::
1. Geben Sie Ihrer Join-Transformation den Namen „InnerJoinWithTripFares“. Wählen Sie in der Dropdownliste des rechten Streams „TripFaresSQL“ aus. Wählen Sie **Innerer** als Join-Typ aus. Weitere Informationen zu den unterschiedlichen Join-Typen im Zuordnungsdatenfluss finden Sie unter [Join-Typen](./data-flow-join.md#join-types).

    Wählen Sie über die Dropdownliste mit den **Verknüpfungsbedingungen** aus, welche Spalten für jeden Stream abgeglichen werden sollen. Klicken Sie zum Hinzufügen einer weiteren Verknüpfungsbedingung neben einer vorhandenen Bedingung auf das Pluszeichen. Standardmäßig werden alle Verknüpfungsbedingungen mit einem AND-Operator kombiniert. Das bedeutet, dass alle Bedingungen erfüllt sein müssen, damit sich eine Übereinstimmung ergibt. In diesem Lab möchten wir die Spalten `medallion`, `hack_license`, `vendor_id` und `pickup_datetime` verwenden.

    :::image type="content" source="media/lab-data-flow-data-share/join2.png" alt-text="Portal: Join 2":::
1. Vergewissern Sie sich anhand einer Datenvorschau, dass Sie 25 Spalten erfolgreich verknüpft haben.

    :::image type="content" source="media/lab-data-flow-data-share/join3.png" alt-text="Portal: Join 3":::

### <a name="aggregate-by-payment_type"></a>Aggregieren nach „payment_type“

1. Fügen Sie nach Abschluss Ihrer Join-Transformation eine Aggregattransformation hinzu, indem Sie neben „InnerJoinWithTripFares“ auf das Pluszeichen klicken. Wählen Sie unter **Schemamodifizierer** die Option **Aggregieren** aus.

    :::image type="content" source="media/lab-data-flow-data-share/agg1.png" alt-text="Portal: Aggregieren 1":::
1. Geben Sie Ihrer Aggregattransformation den Namen „AggregateByPaymentType“. Wählen Sie `payment_type` als Spalte aus, nach der gruppiert werden soll.

    :::image type="content" source="media/lab-data-flow-data-share/agg2.png" alt-text="Portal: Aggregieren 2":::
1. Wechseln Sie zur Registerkarte **Aggregate**. Hier geben Sie zwei Aggregationen an:
    * Durchschnittlicher Fahrpreis gruppiert nach Zahlungstyp
    * Gesamte Fahrtstrecke gruppiert nach Zahlungstyp

    Als Erstes erstellen Sie den Ausdruck für den durchschnittlichen Fahrpreis. Geben Sie im Textfeld **Add or select a column** (Spalte hinzufügen oder auswählen) den Namen „average_fare“ ein.

    :::image type="content" source="media/lab-data-flow-data-share/agg3.png" alt-text="Portal: Aggregieren 3":::
1. Klicken Sie zum Eingeben eines Aggregationsausdrucks auf das blaue Feld **Ausdruck eingeben**. Der Ausdrucks-Generator für Datenflüsse wird geöffnet. Hierbei handelt es sich um ein Tool, das zum visuellen Erstellen von Datenflussausdrücken mit Eingabeschema, integrierten Funktionen und Vorgängen sowie benutzerdefinierten Parametern verwendet wird. Weitere Informationen zu den Funktionen des Ausdrucks-Generators finden Sie in der [Dokumentation des Ausdrucks-Generators](./concepts-data-flow-expression-builder.md).

    Verwenden Sie zum Abrufen des durchschnittlichen Fahrpreises die Aggregationsfunktion `avg()`, um die Spalte `total_amount` zu aggregieren, die per `toInteger()` in eine Integer umgewandelt wurde. In der Ausdruckssprache für Datenflüsse ist dies als `avg(toInteger(total_amount))` definiert. Klicken Sie auf **Speichern und beenden**, wenn Sie fertig sind.

    :::image type="content" source="media/lab-data-flow-data-share/agg4.png" alt-text="Portal: Aggregieren 4":::
1. Klicken Sie auf das Pluszeichen neben `average_fare`, um einen weiteren Aggregationsausdruck hinzuzufügen. Wählen Sie die Option **Spalte hinzufügen** aus.

    :::image type="content" source="media/lab-data-flow-data-share/agg5.png" alt-text="Portal: Aggregieren 5":::
1. Geben Sie im Textfeld **Add or select a column** (Spalte hinzufügen oder auswählen) den Namen „total_trip_distance“ ein. Öffnen Sie wie im letzten Schritt den Ausdrucks-Generator, um den Ausdruck einzugeben.

    Verwenden Sie zum Abrufen der gesamten Fahrstrecke die Aggregationsfunktion `sum()`, um die Spalte `trip_distance` zu aggregieren, die per `toInteger()` in eine Integer umgewandelt wurde. In der Ausdruckssprache für Datenflüsse ist dies als `sum(toInteger(trip_distance))` definiert. Klicken Sie auf **Speichern und beenden**, wenn Sie fertig sind.

    :::image type="content" source="media/lab-data-flow-data-share/agg6.png" alt-text="Portal: Aggregieren 6":::
1. Testen Sie Ihre Transformationslogik auf der Registerkarte **Datenvorschau**. Sie sehen, dass deutlich weniger Zeilen und Spalten als vorher vorhanden sind. Nur die drei Spalten vom Typ „Gruppieren nach“ und „Aggregation“, die in dieser Transformation definiert sind, werden weiter genutzt. Da das Beispiel nur fünf Zahlungstypgruppen enthält, werden nur fünf Zeilen ausgegeben.

    :::image type="content" source="media/lab-data-flow-data-share/agg7.png" alt-text="Portal: Aggregieren 7":::

### <a name="configure-you-azure-synapse-analytics-sink"></a>Konfigurieren der Azure Synapse Analytics-Senke

1. Nachdem wir die Transformationslogik fertiggestellt haben, können wir unsere Daten in eine Azure Synapse Analytics-Tabelle einbinden. Fügen Sie im Abschnitt **Ziel** eine Senkentransformation hinzu.

    :::image type="content" source="media/lab-data-flow-data-share/sink1.png" alt-text="Portal: Senke 1":::
1. Geben Sie Ihrer Senke den Namen „SQLDWSink“. Klicken Sie neben dem Feld „Senkendataset“ auf **Neu**, um ein neues Azure Synapse Analytics-Dataset zu erstellen.

    :::image type="content" source="media/lab-data-flow-data-share/sink2.png" alt-text="Portal: Senke 2":::

1. Wählen Sie die Kachel **Azure Synapse Analytics** aus, und klicken Sie auf „Weiter“.

    :::image type="content" source="media/lab-data-flow-data-share/sink-3.png" alt-text="Portal: Senke 3":::
1. Geben Sie Ihrem Dataset den Namen „AggregatedTaxiData“. Wählen Sie „SQLDW“ als verknüpften Dienst aus. Wählen Sie **Neue Tabelle erstellen** aus, und geben Sie der neuen Tabelle den Namen „dbo.AggregateTaxiData“. Klicken Sie anschließend auf „OK“.

    :::image type="content" source="media/lab-data-flow-data-share/sink4.png" alt-text="Portal: Senke 4":::
1. Navigieren Sie zur Registerkarte **Einstellungen** der Senke. Da wir eine neue Tabelle erstellen, müssen wir als Tabellenaktion die Option **Tabelle neu erstellen** auswählen. Deaktivieren Sie die Option **Staging aktivieren**. Mit dieser Option wird angegeben, ob das Einfügen Zeile für Zeile oder als Batch durchgeführt wird.

    :::image type="content" source="media/lab-data-flow-data-share/sink5.png" alt-text="Portal: Senke 5":::

Sie haben Ihren Datenfluss erfolgreich erstellt. Als Nächstes führen Sie ihn in einer Pipelineaktivität aus.

### <a name="debug-your-pipeline-end-to-end"></a>Debuggen Ihrer Pipeline von Anfang bis Ende

1. Wechseln Sie zurück zur Registerkarte für die Pipeline **IngestAndTransformData**. Achten Sie auf das grüne Feld der Kopieraktivität „IngestIntoADLS“. Ziehen Sie es auf die Datenflussaktivität „JoinAndAggregateData“. Ein Element vom Typ „Bei Erfolg“ wird erstellt. Es bewirkt, dass die Datenflussaktivität nur ausgeführt wird, wenn der Kopiervorgang erfolgreich ist.

    :::image type="content" source="media/lab-data-flow-data-share/pipeline1.png" alt-text="Portal: Pipeline 1":::
1. Klicken Sie auf **Debuggen**, um eine Debugausführung durchzuführen (wie für die Kopieraktivität). Für Debugausführungen wird von der Datenflussaktivität der aktive Debugcluster verwendet und kein neuer Cluster erstellt. Die Ausführung dieser Pipeline dauert etwas mehr als eine Minute.

    :::image type="content" source="media/lab-data-flow-data-share/pipeline2.png" alt-text="Portal: Pipeline 2":::
1. Wie die Kopieraktivität auch, verfügt der Datenfluss über eine spezielle Überwachungsansicht, auf die nach Abschluss des Vorgangs über das Brillensymbol zugegriffen werden kann.

    :::image type="content" source="media/lab-data-flow-data-share/pipeline3.png" alt-text="Portal: Pipeline 3":::
1. In der Überwachungsansicht wird in jeder Ausführungsphase ein vereinfachter Datenflussgraph mit den Ausführungszeiten und -zeilen angezeigt. Wenn dies korrekt durchgeführt wird, sollten bei dieser Aktivität 49.999 Zeilen in fünf Zeilen aggregiert worden sein.

    :::image type="content" source="media/lab-data-flow-data-share/pipeline4.png" alt-text="Portal: Pipeline 4":::
1. Sie können auf eine Transformation klicken, um weitere Details zur Ausführung abzurufen, z. B. Partitionierungsinformationen und neue/aktualisierte/gelöschte Spalten.

    :::image type="content" source="media/lab-data-flow-data-share/pipeline5.png" alt-text="Portal: Pipeline 5":::

Sie haben den Data Factory-Teil dieses Labs jetzt abgeschlossen. Veröffentlichen Sie Ihre Ressourcen, falls Sie diese mit Triggern operationalisieren möchten. Sie haben erfolgreich eine Pipeline ausgeführt, mit der Daten mit der Kopieraktivität aus Azure SQL-Datenbank in Azure Data Lake Storage erfasst wurden, und diese Daten anschließend in einer Azure Synapse Analytics-Instanz aggregiert. Sie können überprüfen, ob das Schreiben der Daten erfolgreich war, indem Sie sich die SQL Server-Instanz ansehen.

## <a name="share-data-using-azure-data-share"></a>Freigeben von Daten mithilfe von Azure Data Share

In diesem Abschnitt wird beschrieben, wie Sie mit dem Azure-Portal eine neue Datenfreigabe einrichten. Dies umfasst das Erstellen einer neuen Datenfreigabe, die Datasets aus Azure Data Lake Store Gen2 und Azure Synapse Analytics enthält. Anschließend konfigurieren Sie eine Momentaufnahme, damit die Datenconsumer über eine Option zum automatischen Aktualisieren der freigegebenen Daten verfügen. Anschließend laden Sie Empfänger für Ihre Datenfreigabe ein. 

Nachdem Sie eine Datenfreigabe erstellt haben, wechseln Sie dann die Seite und werden zum *Datenconsumer*. Als Datenconsumer führen Sie die folgenden Schritte aus: Akzeptieren einer Einladung für die Datenfreigabe, Konfigurieren des Orts für den Datenempfang und Zuordnen von Datasets zu unterschiedlichen Speicherorten. Anschließend lösen Sie eine Momentaufnahme aus. Bei diesem Vorgang werden die für Sie freigegebenen Daten an das Ziel kopiert, das Sie angegeben haben. 

### <a name="sharing-data-data-provider-flow"></a>Freigeben von Daten (Datenanbieterfluss)

1. Öffnen Sie das Azure-Portal in Microsoft Edge oder Google Chrome.

1. Verwenden Sie die Suchleiste oben auf der Seite, um nach **Datenfreigaben** zu suchen.

    :::image type="content" source="media/lab-data-flow-data-share/portal-ads.png" alt-text="Portal: ADF":::

1. Wählen Sie das Datenfreigabekonto aus, dessen Name „Provider“ enthält. Beispiel: **DataProvider0102**. 

1. Wählen Sie **Freigabe Ihrer Daten starten** aus.

    :::image type="content" source="media/lab-data-flow-data-share/ads-start-sharing.png" alt-text="Starten der Freigabe":::

1. Wählen Sie **+ Erstellen** aus, um mit dem Konfigurieren Ihrer neuen Datenfreigabe zu beginnen. 

1. Geben Sie unter **Freigabename** einen Namen Ihrer Wahl an. Dies ist der Freigabename, der für Ihren Datenconsumer angezeigt wird. Daher ist es ratsam, einen beschreibenden Namen zu verwenden, z. B. „TaxiData“.

1. Geben Sie unter **Beschreibung** einen Satz ein, mit dem der Inhalt der Datenfreigabe beschrieben wird. Die Datenfreigabe enthält Daten zu weltweiten Taxifahrten, die sich in unterschiedlichen Speichern befinden, z. B. Azure Synapse Analytics und Azure Data Lake Store. 

1. Geben Sie unter **Nutzungsbedingungen** die Bedingungen an, an die sich Ihr Datenconsumer halten soll. Beispiele hierfür sind „Daten nicht außerhalb Ihres Unternehmens weitergeben“ oder „Siehe Vertrag“. 

    :::image type="content" source="media/lab-data-flow-data-share/ads-details.png" alt-text="Details zu Freigaben":::

1. Wählen Sie **Weiter**. 

1. Wählen Sie die Option **Datasets hinzufügen** aus. 

    :::image type="content" source="media/lab-data-flow-data-share/add-dataset.png" alt-text="Datasets hinzufügen 1":::

1. Wählen Sie **Azure Synapse Analytics** aus, um in Azure Synapse Analytics eine Tabelle auszuwählen, in der Ihre ADF-Transformationen enthalten sind.

    :::image type="content" source="media/lab-data-flow-data-share/add-dataset-sql.png" alt-text="Datasets hinzufügen: SQL":::


1. Sie erhalten ein Skript, das Sie vor dem Fortfahren ausführen können. Mit dem bereitgestellten Skript wird in der SQL-Datenbank ein Benutzer erstellt, damit die verwaltete Dienstidentität von Azure Data Share im Namen des Benutzers die Authentifizierung durchführen kann. 

> [!IMPORTANT]
> Vor dem Ausführen des Skripts müssen Sie sich selbst als Active Directory-Administrator für die SQL Server-Instanz festlegen. 

1. Öffnen Sie eine neue Registerkarte, und navigieren Sie zum Azure-Portal. Kopieren Sie das bereitgestellte Skript, um einen Benutzer in der Datenbank zu erstellen, aus der Sie Daten freigeben möchten. Melden Sie sich hierfür per Abfrage-Explorer (Vorschauversion) an der EDW-Datenbank an, indem Sie die AAD-Authentifizierung verwenden. 

    Sie müssen das Skript so ändern, dass der erstellte Benutzer in Klammern gesetzt ist. Beispiel:
    
    create user [dataprovider-xxxx] from external login; exec sp_addrolemember db_owner, [dataprovider-xxxx];
    
1. Wechseln Sie zurück zur Azure Data Share-Instanz, in der Sie Ihrer Datenfreigabe Datasets hinzugefügt haben. 

1. Wählen Sie **EDW** und anschließend **AggregatedTaxiData** für die Tabelle aus. 

1. Wählen Sie **Dataset hinzufügen** aus.

    Wir verfügen jetzt über eine SQL-Tabelle, die Teil unseres Datasets ist. Als Nächstes fügen wir weitere Datasets aus Azure Data Lake Storage hinzu. 

1. Wählen Sie **Dataset hinzufügen** und **Azure Data Lake Storage Gen2** aus.

    :::image type="content" source="media/lab-data-flow-data-share/add-dataset-adls.png" alt-text="Dataset hinzufügen: ADLS":::

1. Wählen Sie **Weiter** aus.

1. Erweitern Sie *wwtaxidata*. Erweitern Sie *Boston Taxi Data*. Beachten Sie, dass Sie Daten bis hinunter auf Dateiebene freigeben können. 

1. Wählen Sie den Ordner *Boston Taxi Data* aus, um den gesamten Ordner für Ihre Datenfreigabe hinzuzufügen. 

1. Wählen Sie die Option **Datasets hinzufügen** aus.

1. Überprüfen Sie die hinzugefügten Datasets. Sie sollten für Ihre Datenfreigabe über eine SQL-Tabelle und einen ADLS Gen2-Ordner verfügen. 

1. Wählen Sie **Weiter**.

1. Auf diesem Bildschirm können Sie Ihrer Datenfreigabe Empfänger hinzufügen. Die von Ihnen hinzugefügten Empfänger erhalten Einladungen für Ihre Datenfreigabe. Für dieses Lab müssen Sie zwei E-Mail-Adressen hinzufügen:

    1. Die E-Mail-Adresse des von Ihnen genutzten Azure-Abonnements. 

        :::image type="content" source="media/lab-data-flow-data-share/add-recipients.png" alt-text="Hinzufügen von Empfängern":::

    1. Fügen Sie den fiktiven Datenconsumer mit dem Namen *janedoe@fabrikam.com* hinzu.

1. Auf diesem Bildschirm können Sie eine Momentaufnahmeeinstellung für Ihren Datenconsumer konfigurieren. Dies ermöglicht es dem Datenconsumer, nach einem von Ihnen definierten Intervall regelmäßige Updates Ihrer Daten zu erhalten. 

1. Aktivieren Sie **Momentaufnahmezeitplan**, und konfigurieren Sie über die Dropdownliste *Serie* eine stündliche Aktualisierung Ihrer Daten.  

1. Klicken Sie auf **Erstellen**.

    Sie verfügen jetzt über eine aktive Datenfreigabe. Wir sehen uns nun an, was Ihnen als Datenanbieter beim Erstellen einer Datenfreigabe angezeigt wird. 

1. Wählen Sie die von Ihnen erstellte Datenfreigabe mit dem Namen **DataProvider** aus. Wählen Sie unter **Datenfreigabe** die Option **Gesendete Freigaben** aus, um zur Datenfreigabe zu navigieren. 

1. Klicken Sie auf „Momentaufnahmezeitplan“. Sie können den Momentaufnahmezeitplan bei Bedarf deaktivieren. 

1. Wählen Sie als Nächstes die Registerkarte **Datasets** aus. Sie können dieser Datenfreigabe nach der Erstellung weitere Datasets hinzufügen. 

1. Wählen Sie die Registerkarte **Freigabeabonnements** aus. Es sind noch keine Freigabeabonnements vorhanden, da Ihr Datenconsumer Ihre Einladung noch nicht akzeptiert hat.

1. Navigieren Sie zur Registerkarte **Einladungen**. Auf der Registerkarte wird eine Liste mit den ausstehenden Einladungen angezeigt. 

    :::image type="content" source="media/lab-data-flow-data-share/pending-invites.png" alt-text="Ausstehende Einladungen":::

1. Wählen Sie die Einladung für *janedoe@fabrikam.com* aus. Wählen Sie „Löschen“ aus. Wenn die Empfängerin die Einladung noch nicht akzeptiert hat, kann sie dies nun nicht mehr tun. 

1. Wählen Sie die Registerkarte **Verlauf** . Es wird noch nichts angezeigt, weil der Datenconsumer Ihre Einladung noch nicht akzeptiert und eine Momentaufnahme ausgelöst hat. 

### <a name="receiving-data-data-consumer-flow"></a>Empfangen von Daten (Datenconsumerfluss)

Nachdem wir unsere Datenfreigabe nun überprüft haben, können wir den Kontext ändern und zum Datenconsumer wechseln. 

Sie sollten in Ihrem Posteingang jetzt eine Azure Data Share-Einladung von Microsoft Azure finden. Starten Sie Outlook Web Access (outlook.com), und melden Sie sich mit den Anmeldeinformationen für Ihr Azure-Abonnement an.

Klicken Sie in der E-Mail, die Sie erhalten haben, auf „Einladung anzeigen >“. An diesem Punkt simulieren Sie auf der Datenconsumer-Benutzeroberfläche den Vorgang, bei dem die Einladung eines Datenanbieters für die Datenfreigabe akzeptiert wird. 

:::image type="content" source="media/lab-data-flow-data-share/email-invite.png" alt-text="E-Mail-Einladung":::

Unter Umständen werden Sie aufgefordert, ein Abonnement auszuwählen. Achten Sie darauf, dass Sie das Abonnement auswählen, mit dem Sie in diesem Lab gearbeitet haben. 

1. Klicken Sie auf die Einladung mit dem Namen *DataProvider*. 

1. Im angezeigten Bereich „Einladung“ sehen Sie verschiedene Details zu der Datenfreigabe, die Sie zuvor als Datenanbieter konfiguriert haben. Überprüfen Sie die Details, und akzeptieren Sie die Nutzungsbedingungen, falls diese angegeben sind.

1. Wählen Sie das Abonnement und dann die Ressourcengruppe aus, die für das Lab bereits vorhanden ist. 

1. Wählen Sie unter **Datenfreigabekonto** die Option **DataConsumer** aus. Sie können auch ein neues Datenfreigabekonto erstellen. 

1. Sie sehen, dass neben **Received share name** (Name der empfangenen Freigabe) als Standardfreigabename der Name angezeigt wird, der vom Datenanbieter angegeben wurde. Geben Sie der Freigabe einen Anzeigenamen, der die zu empfangenden Daten beschreibt, z. B. **TaxiDataShare**.

    :::image type="content" source="media/lab-data-flow-data-share/consumer-accept.png" alt-text="Akzeptieren der Einladung":::

1. Sie können die Option **Accept and configure now** (Akzeptieren und jetzt konfigurieren) oder **Accept and configure later** (Akzeptieren und später konfigurieren) auswählen. Wenn Sie sich für das Akzeptieren mit sofortiger Konfiguration entscheiden, geben Sie ein Speicherkonto an, in das alle Daten kopiert werden sollen. Wenn Sie sich für das Akzeptieren mit späterer Konfiguration entscheiden, sind die Datasets der Freigabe nicht zugeordnet, und Sie müssen die Zuordnung später manuell durchführen. Wir entscheiden uns für die spätere Konfiguration. 

1. Wählen Sie die Option **Akzeptieren und später konfigurieren** aus. 

    Beim Konfigurieren dieser Option wird ein Freigabeabonnement erstellt, aber da kein Ziel zugeordnet wurde, ist kein Ort für die Freigabe der Daten vorhanden. 

    Als Nächstes konfigurieren wir Datasetzuordnungen für die Datenfreigabe. 

1. Wählen Sie die empfangene Freigabe aus (Name, den Sie in Schritt 5 angegeben haben).

    **Momentaufnahme auslösen**  ist abgeblendet, aber die Freigabe ist „Aktiv“. 

1. Wählen Sie die Registerkarte **Datasets** aus. Beachten Sie, dass für alle Datasets „Nicht zugeordnet“ angegeben ist. Dies bedeutet, dass kein Ziel zum Kopieren der Daten vorhanden ist. 

    :::image type="content" source="media/lab-data-flow-data-share/unmapped.png" alt-text="Nicht zugeordnete Datasets":::

1. Wählen Sie die Azure Synapse Analytics-Tabelle und anschließend **+ Dem Ziel zuordnen** aus.

1. Wählen Sie auf der rechten Seite des Bildschirms die Dropdownliste **Zieldatentyp** aus. 

    Sie können die SQL-Daten einem großen Bereich von Datenspeichern zuordnen. In diesem Fall führen wir die Zuordnung zu einer Azure SQL-Datenbank durch.

    :::image type="content" source="media/lab-data-flow-data-share/mapping-options.png" alt-text="mapping":::
    
    (Optional) Wählen Sie **Azure Data Lake Storage Gen2** als Zieldatentyp aus. 
    
    (Optional) Wählen Sie das von Ihnen verwendete Abonnement, die Ressourcengruppe und das Speicherkonto aus. 
    
    (Optional) Sie können angeben, ob Sie die Daten in Ihrer Data Lake-Instanz im CSV- oder Parquet-Format empfangen möchten. 

1. Wählen Sie neben **Zieldatentyp** die Option „Azure SQL-Datenbank“ aus. 

1. Wählen Sie das von Ihnen verwendete Abonnement, die Ressourcengruppe und das Speicherkonto aus. 

    :::image type="content" source="media/lab-data-flow-data-share/map-to-sqldb.png" alt-text="Zuordnen zu SQL":::

1. Bevor Sie fortfahren können, müssen Sie in der SQL Server-Instanz einen neuen Benutzer erstellen, indem Sie das angegebene Skript ausführen. Kopieren Sie zuerst das bereitgestellte Skript in die Zwischenablage. 

1. Öffnen Sie im Azure-Portal eine neue Registerkarte. Schließen Sie die bereits geöffnete Registerkarte nicht, da Sie sie gleich noch benötigen. 

1. Navigieren Sie auf der neuen Registerkarte zu **SQL-Datenbanken**.

1. Wählen Sie die SQL-Datenbank aus (unter Ihrem Abonnement sollte nur ein Eintrag vorhanden sein). Achten Sie darauf, dass Sie nicht das Data Warehouse auswählen. 

1. Wählen Sie **Abfrage-Editor (Vorschau)** aus.

1. Verwenden Sie die AAD-Authentifizierung, um sich beim Abfrage-Editor anzumelden. 

1. Führen Sie die unter der Datenfreigabe bereitgestellte Abfrage aus (in Schritt 14 in die Zwischenablage kopiert). 

    Mit diesem Befehl kann der Azure Data Share-Dienst verwaltete Identitäten für Azure-Dienste nutzen, um die Authentifizierung für die SQL Server-Instanz durchzuführen, damit dafür Daten kopiert werden können. 

1. Wechseln Sie zurück zur ursprünglichen Registerkarte, und wählen Sie **Dem Ziel zuordnen** aus.

1. Wählen Sie als Nächstes den Ordner „Azure Data Lake Gen2“ aus, der Teil des Datasets ist, und ordnen Sie ihn einem Azure Blob Storage-Konto zu. 

    :::image type="content" source="media/lab-data-flow-data-share/storage-map.png" alt-text="storage":::

    Nachdem alle Datasets zugeordnet wurden, können Sie mit dem Empfang der Daten vom Datenanbieter beginnen. 

    :::image type="content" source="media/lab-data-flow-data-share/all-mapped.png" alt-text="Zugeordnet":::
    
1. Wählen Sie **Details** aus. 

    Beachten Sie, dass die Option **Momentaufnahme auslösen** nicht mehr abgeblendet ist, weil die Datenfreigabe jetzt über Ziele für den Kopiervorgang verfügt.

1. Wählen Sie „Momentaufnahme auslösen“ > „Vollständige Kopie“ aus. 

    :::image type="content" source="media/lab-data-flow-data-share/trigger-full.png" alt-text="trigger":::

    Der Vorgang zum Kopieren der Daten in Ihr neues Datenfreigabekonto wird gestartet. In einem realen Szenario stammen diese Daten von einem Drittanbieter. 

    Es dauert ungefähr 3 bis 5 Minuten, bis die Übertragung der Daten abgeschlossen ist. Sie können den Status überwachen, indem Sie auf die Registerkarte **Verlauf** klicken. 

    Navigieren Sie während des Wartezeitraums zur ursprünglichen Datenfreigabe (DataProvider), und zeigen Sie den Status der Registerkarten **Freigabeabonnements** und **Verlauf** an. Sie sehen, dass jetzt ein aktives Abonnement vorhanden ist. Als Datenanbieter können Sie auch überwachen, wann für den Datenconsumer der Empfang der Daten, die für ihn freigegeben wurden, begonnen hat. 

1. Navigieren Sie zurück zur Datenfreigabe des Datenconsumers. Wenn der Status des Triggers „Erfolg“ lautet, können Sie zur SQL-Zieldatenbank und zur Data Lake-Instanz navigieren, um sich zu vergewissern, dass die Daten in die entsprechenden Speicher übertragen wurden. 

Glückwunsch! Sie haben das Lab nun abgeschlossen.