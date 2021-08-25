---
title: Delta-Lake-ETL mit Datenflüssen
description: Dieses Tutorial enthält ausführliche Anleitungen für die Verwendung von Datenflüssen zum Transformieren und Analysieren von Daten in einem Delta-Lake.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 06/04/2021
ms.openlocfilehash: dff3972bfc9da83312a1fdf4fc7c6169f932c410
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122639886"
---
# <a name="transform-data-in-delta-lake-using-mapping-data-flows"></a>Transformieren von Daten in einem Delta-Lake mithilfe von Zuordnungsdatenflüssen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Falls Sie noch nicht mit Azure Data Factory vertraut sind, ist es ratsam, den Artikel [Einführung in Azure Data Factory](introduction.md) zu lesen.

In diesem Tutorial verwenden Sie die Datenflusscanvas zum Erstellen von Datenflüssen, mit denen Sie Daten in Azure Data Lake Storage Gen2 (ADLS) analysieren und transformieren und in einem Delta-Lake speichern können.

## <a name="prerequisites"></a>Voraussetzungen
* **Azure-Abonnement**. Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* **Azure-Speicherkonto**. Sie verwenden den ADLS-Speicher als *Quelldatenspeicher* und *Senkendatenspeicher*. Wenn Sie kein Speicherkonto besitzen, finden Sie unter [Informationen zu Azure-Speicherkonten](../storage/common/storage-account-create.md) Schritte zum Erstellen eines solchen Kontos.

Die Datei, die in diesem Tutorial transformiert wird, ist „MoviesDB.csv“, die sich [hier](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/moviesDB2.csv) befindet. Zum Abrufen der Datei aus GitHub kopieren Sie den Inhalt in einen Text-Editor Ihrer Wahl, und speichern Sie ihn lokal als CSV-Datei. Wenn Sie die Datei in Ihr Speicherkonto hochladen möchten, finden Sie Informationen dazu unter [Hochladen von Blobs mit dem Azure-Portal](../storage/blobs/storage-quickstart-blobs-portal.md). In den Beispielen wird auf einen Container mit dem Namen „sample-data“ verwiesen.

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory

In diesem Schritt erstellen Sie eine Data Factory und öffnen die Data Factory-Benutzeroberfläche, um eine Pipeline in der Data Factory zu erstellen.

1. Öffnen Sie **Microsoft Edge** oder **Google Chrome**. Die Data Factory-Benutzeroberfläche wird derzeit nur in den Webbrowsern Microsoft Edge und Google Chrome unterstützt.
1. Wählen Sie im Menü auf der linken Seite **Ressource erstellen** > **Integration** > **Data Factory** aus.
1. Geben Sie auf der Seite **Neue Data Factory** unter **Name** den Namen **ADFTutorialDataFactory** ein.
1. Wählen Sie das **Azure-Abonnement** aus, in dem die Data Factory erstellt werden soll.
1. Führen Sie unter **Ressourcengruppe** einen der folgenden Schritte aus:

    a. Wählen Sie die Option **Use existing**(Vorhandene verwenden) und dann in der Dropdownliste eine vorhandene Ressourcengruppe.

    b. Wählen Sie **Neu erstellen**, und geben Sie den Namen einer Ressourcengruppe ein. 
         
    Weitere Informationen zu Ressourcengruppen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-resource-manager/management/overview.md). 
1. Wählen Sie unter **Version** die Option **V2**.
1. Wählen Sie unter **Standort** einen Standort für die Data Factory aus. In der Dropdownliste werden nur unterstützte Standorte angezeigt. Datenspeicher (etwa Azure Storage und SQL-Datenbank) und Computeeinheiten (etwa Azure HDInsight), die von der Data Factory genutzt werden, können sich in anderen Regionen befinden.
1. Klicken Sie auf **Erstellen**.
1. Nach Abschluss der Erstellung wird der Hinweis im Benachrichtigungscenter angezeigt. Wählen Sie **Zu Ressource wechseln** aus, um zur Data Factory-Seite zu navigieren.
1. Klicken Sie auf **Erstellen und überwachen**, um die Data Factory-Benutzeroberfläche auf einer separaten Registerkarte zu starten.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Erstellen einer Pipeline mit einer Datenflussaktivität

In diesem Schritt erstellen Sie eine Pipeline mit einer Datenflussaktivität.

1. Wählen Sie auf der Startseite die Option **Orchestrieren** aus.

   ![Screenshot, der die ADF-Startseite zeigt.](./media/doc-common-process/get-started-page.png)

1. Geben Sie auf der Registerkarte **Allgemein** der Pipeline als **Namen** der Pipeline **DeltaLake** ein.
1. Erweitern Sie im Bereich **Aktivitäten** das Accordion-Element **Verschieben und transformieren**. Ziehen Sie die **Datenfluss**-Aktivität per Drag & Drop aus dem Bereich auf die Pipelinecanvas.

    ![Screenshot der Pipelinecanvas, auf der die Datenflussaktivität abgelegt werden kann](media/tutorial-data-flow/activity1.png)
1. Wählen Sie im Popupfenster **Datenfluss hinzufügen** die Option **Neuen Datenfluss erstellen** aus, und geben Sie dem Datenfluss den Namen **DeltaLake**. Klicken Sie anschließend auf „Fertig stellen“.

    ![Screenshot des Fensters, in dem beim Erstellen eines neuen Datenflusses der Name eingegeben wird](media/tutorial-data-flow/activity2.png)
1. Setzen Sie den Schieberegler **Datenfluss debuggen** in der oberen Pipeline-Canvas-Leiste auf „ein“. Der Debugmodus ermöglicht das interaktive Testen von Transformationslogik mit einem aktiven Spark-Cluster. Die Aufwärmphase von Datenflussclustern dauert 5 bis 7 Minuten. Den Benutzern wird empfohlen, zuerst den Debugmodus zu aktivieren, wenn sie die Entwicklung eines Datenflusses planen. Weitere Informationen finden Sie unter [Debugmodus](concepts-data-flow-debug-mode.md).

    ![Screenshot: Position des Schiebereglers „Datenflüsse debuggen“.](media/tutorial-data-flow/dataflow1.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas&quot;></a>Erstellen von Transformationslogik auf der Datenflusscanvas

In diesem Tutorial generieren Sie zwei Datenflüsse. Der erste Datenfluss verläuft einfach von der Quelle zur Senke, um aus der obigen CSV-Datei mit Filmen einen neuen Delta-Lake zu generieren. Abschließend erstellen Sie den Datenflussentwurf unten, um Daten im Delta-Lake zu aktualisieren.

![Abschließender Flow](media/data-flow/data-flow-tutorial-6.png &quot;Abschließender Flow")

### <a name="tutorial-objectives"></a>Ziele des Tutorials

1. Verwenden Sie als Quelle das Dataset „MoviesCSV“ von oben, und erstellen Sie damit einen neuen Delta-Lake.
1. Erstellen Sie die Logik zum Aktualisieren der Bewertungen für Filme aus dem Jahr 1988 in „1“.
1. Löschen Sie alle Filme aus dem Jahr 1950.
1. Fügen Sie neue Filme für das Jahr 2021 hinzu, indem Sie die Filme aus dem Jahr 1960 duplizieren.

### <a name="start-from-a-blank-data-flow-canvas"></a>Ausgangspunkt: leere Datenflusscanvas

1. Klicken Sie auf die Quelltransformation.
1. Klicken Sie im unteren Bereich 1 neben dem Dataset auf „Neu“, und erstellen Sie einen neuen verknüpften Dienst für ADLS Gen2.
1. Wählen Sie als Datasettyp „Durch Trennzeichen getrennter Text“ aus.
1. Benennen Sie das Dataset mit „MoviesCSV“. 
1. Zeigen Sie auf die Datei „MoviesCSV“, die Sie zuvor in den Speicher hochgeladen haben.
1. Legen Sie den Typ auf „Durch Trennzeichen getrennter Text“ fest, und fügen Sie den Header in der ersten Zeile ein. 
1. Wechseln Sie zur Registerkarte mit der Quellprojektion, und klicken Sie auf „Datentypen erkennen“.
1. Nachdem Sie nun die Projektion festgelegt haben, können Sie fortfahren. 
1. Fügen Sie eine Senkentransformation hinzu.
1. „Delta“ ist ein Inlinetyp für Datasets. Sie müssen auf Ihr ADLS Gen2-Speicherkonto verweisen.
   
   ![Inlinedataset](media/data-flow/data-flow-tutorial-5.png "Inline-Dataset")

1. Wählen Sie einen Ordnernamen in Ihrem Speichercontainer aus, in dem ADF den Delta-Lake erstellen soll.
1. Wechseln Sie zurück zum Pipeline-Designer, und klicken Sie auf „Debuggen“, um die Pipeline im Debugmodus mit nur dieser Datenflussaktivität auf der Canvas auszuführen. Dadurch wird Ihr neuer Delta-Lake in ADLS Gen2 generiert.
1. Klicken Sie in den Factoryressourcen auf „Neu“ und dann auf „Datenfluss“. 
1. Verwenden Sie die Datei „MoviesCSV“ erneut als Quelle, und klicken Sie wieder auf „Datentypen erkennen“.
1. Fügen Sie Ihrer Quelltransformation im Graphen eine Filtertransformation hinzu.
1. Lassen Sie nur Zeilen mit Filmen zu, die den drei Jahren entsprechen, mit denen Sie arbeiten: 1950, 1988 und 1960.
1. Aktualisieren Sie die Bewertungen für jeden Film aus dem Jahr 1988 in „1“, indem Sie nun der Filtertransformation eine Transformation für abgeleitete Spalten hinzufügen.
1. Erstellen Sie in derselben abgeleiteten Spalte Filme für das Jahr 2021, indem Sie ein vorhandenes Jahr in das Jahr 2021 ändern. Wählen Sie 1960 aus.
1. So sehen die drei abgeleiteten Spalten aus:

   ![Abgeleitete Spalte](media/data-flow/data-flow-tutorial-2.png "Abgeleitete Spalte")
   
1. Die Richtlinien ```Update, insert, delete, and upsert``` werden in der Transformation zum Ändern von Zeilen erstellt. Fügen Sie nach der abgeleiteten Spalte eine Transformation zum Ändern von Zeilen hinzu.
1. Die Richtlinien für Zeilenänderungen sollten wie folgt aussehen:

   ![Zeilenänderung](media/data-flow/data-flow-tutorial-3.png "Ändern einer Zeile")
   
1. Nachdem Sie die richtige Richtlinie für jeden Zeilenänderungstyp festgelegt haben, überprüfen Sie, ob die richtigen Aktualisierungsregeln für die Senkentransformation festgelegt wurden.

   ![Senke](media/data-flow/data-flow-tutorial-4.png "Senke")
   
1. Hier verwenden Sie den Delta-Lake als Senke für Ihren ADLS Gen2-Data Lake und erlauben Einfügungen, Aktualisierungen und Löschungen. 
1. Beachten Sie, dass es sich bei den Schlüsselspalten um einen zusammengesetzten Schlüssel handelt, der sich aus der Primärschlüsselspalte „movie“ und der Spalte „year“ zusammensetzt. Dies liegt daran, dass Sie „gefälschte“ Filme für das Jahr 2021 erstellt haben, indem Sie die Zeilen für das Jahr 1960 dupliziert haben. Dadurch werden Konflikte beim Suchen vorhandener Zeilen vermieden, da die Eindeutigkeit sichergestellt ist.

### <a name="download-completed-sample"></a>Herunterladen des vollständigen Beispiels
[Hier finden Sie eine Beispiellösung für die Delta-Pipeline mit einem Datenfluss für das Aktualisieren/Löschen von Zeilen im Lake: ](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/DeltaPipeline.zip)

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die [Ausdruckssprache für Datenflüsse](data-flow-expression-functions.md).
