---
title: Bewährte Methoden für das Schreiben in Dateien in Data Lake mit Datenflüssen
description: Dieses Tutorial bietet bewährte Methoden für das Schreiben in Dateien in Data Lake mit Datenflüssen.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 06/04/2021
ms.openlocfilehash: 91eb5d1072f1385c025d9d93b89466aaa9320ecd
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122639811"
---
# <a name="best-practices-for-writing-to-files-to-data-lake-with-data-flows"></a>Bewährte Methoden für das Schreiben in Dateien in Data Lake mit Datenflüssen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Falls Sie noch nicht mit Azure Data Factory vertraut sind, ist es ratsam, den Artikel [Einführung in Azure Data Factory](introduction.md) zu lesen.

In diesem Tutorial lernen Sie bewährte Methoden kennen, die beim Schreiben von Dateien in ADLS Gen2 oder Azure Blob Storage mithilfe von Datenflüssen angewendet werden können. Sie benötigen Zugriff auf ein Azure Blob Storage-Konto oder ein Azure Data Lake Store Gen2-Konto, um eine Parquet-Datei zu lesen und die Ergebnisse dann in Ordnern zu speichern.

## <a name="prerequisites"></a>Voraussetzungen
* **Azure-Abonnement**. Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* **Azure-Speicherkonto**. Sie verwenden den ADLS-Speicher als *Quelldatenspeicher* und *Senkendatenspeicher*. Wenn Sie kein Speicherkonto besitzen, finden Sie unter [Informationen zu Azure-Speicherkonten](../storage/common/storage-account-create.md) Schritte zum Erstellen eines solchen Kontos.

Bei den Schritten in diesem Tutorial wird davon ausgegangen, dass Sie 

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory

In diesem Schritt erstellen Sie eine Data Factory und öffnen die Data Factory-Benutzeroberfläche, um eine Pipeline in der Data Factory zu erstellen.

1. Öffnen Sie **Microsoft Edge** oder **Google Chrome**. Die Data Factory-Benutzeroberfläche wird derzeit nur in den Webbrowsern Microsoft Edge und Google Chrome unterstützt.
1. Wählen Sie im Menü auf der linken Seite **Ressource erstellen** > **Integration** > **Data Factory** aus.
1. Geben Sie auf der Seite **Neue Data Factory** unter **Name** den Namen **ADFTutorialDataFactory** ein.
1. Wählen Sie das **Azure-Abonnement** aus, in dem die Data Factory erstellt werden soll.
1. Führen Sie unter **Ressourcengruppe** einen der folgenden Schritte aus:

    a. Wählen Sie die Option **Use existing**(Vorhandene verwenden) und dann in der Dropdownliste eine vorhandene Ressourcengruppe.
    
    b. Wählen Sie **Neu erstellen** aus, und geben Sie den Namen einer Ressourcengruppe ein. Weitere Informationen zu Ressourcengruppen finden Sie unter [Was ist Azure Resource Manager?](../azure-resource-manager/management/overview.md)
    
1. Wählen Sie unter **Version** die Option **V2**.
1. Wählen Sie unter **Standort** einen Standort für die Data Factory aus. In der Dropdownliste werden nur unterstützte Standorte angezeigt. Datenspeicher (etwa Azure Storage und SQL-Datenbank) und Computeeinheiten (etwa Azure HDInsight), die von der Data Factory genutzt werden, können sich in anderen Regionen befinden.
1. Klicken Sie auf **Erstellen**.
1. Nach Abschluss der Erstellung wird der Hinweis im Benachrichtigungscenter angezeigt. Wählen Sie **Zu Ressource wechseln** aus, um zur Data Factory-Seite zu navigieren.
1. Klicken Sie auf **Erstellen und überwachen**, um die Data Factory-Benutzeroberfläche auf einer separaten Registerkarte zu starten.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Erstellen einer Pipeline mit einer Datenflussaktivität

In diesem Schritt erstellen Sie eine Pipeline mit einer Datenflussaktivität.

1. Klicken Sie auf der Homepage von Azure Data Factory auf **Orchestrate** (Orchestrieren).

   ![Screenshot, der die ADF-Homepage zeigt.](./media/doc-common-process/get-started-page.png)

1. Geben Sie auf der Registerkarte **Allgemein** der Pipeline als **Namen** der Pipeline **DeltaLake** ein.
1. Setzen Sie auf der oberen Leiste der Factory den Schieberegler **Datenfluss debuggen** auf „Ein“. Der Debugmodus ermöglicht das interaktive Testen von Transformationslogik mit einem aktiven Spark-Cluster. Die Aufwärmphase von Datenflussclustern dauert 5 bis 7 Minuten. Den Benutzern wird empfohlen, zuerst den Debugmodus zu aktivieren, wenn sie die Entwicklung eines Datenflusses planen. Weitere Informationen finden Sie unter [Debugmodus](concepts-data-flow-debug-mode.md).

    ![Datenflussaktivität](media/tutorial-data-flow/dataflow1.png)
1. Erweitern Sie im Bereich **Aktivitäten** das Accordion-Element **Verschieben und transformieren**. Ziehen Sie die **Datenfluss**-Aktivität per Drag & Drop aus dem Bereich auf die Pipelinecanvas.

    ![Screenshot der Pipelinecanvas, auf der die Datenflussaktivität abgelegt werden kann](media/tutorial-data-flow/activity1.png)
1. Wählen Sie im Popupfenster **Datenfluss hinzufügen** die Option **Neuen Datenfluss erstellen** aus, und geben Sie dem Datenfluss den Namen **DeltaLake**. Klicken Sie anschließend auf „Fertig stellen“.

    ![Screenshot des Fensters, in dem beim Erstellen eines neuen Datenflusses der Name eingegeben wird](media/tutorial-data-flow/activity2.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Erstellen von Transformationslogik auf der Datenflusscanvas

Sie verwenden alle Quelldaten (in diesem Tutorial wird eine Parquet-Dateiquelle verwendet) sowie eine Senkentransformation, um die Daten mithilfe der effektivsten Mechanismen für Data Lake ETL im Parquet-Format bereitzustellen.

![Abschließender Flow](media/data-flow/parts-final.png "Abschließender Flow")

### <a name="tutorial-objectives"></a>Ziele des Tutorials

1. Auswählen eines Ihrer Quelldatasets in einem neuen Datenfluss
1. Verwenden von Datenflüssen zum effektiven Partitionieren Ihres Senkendatasets
1. Bereitstellen Ihrer partitionierten Daten in ADLS Gen2 Lake-Ordnern

### <a name="start-from-a-blank-data-flow-canvas"></a>Ausgangspunkt: leere Datenflusscanvas

Zunächst richten Sie die Datenflussumgebung für jeden der unten beschriebenen Mechanismen zum Bereitstellen der Daten in ADLS Gen2 ein:

1. Klicken Sie auf die Quelltransformation.
1. Klicken Sie im unteren Bereich neben dem Dataset auf die neue Schaltfläche.
1. Wählen Sie ein Dataset aus, oder erstellen Sie ein neues. Für diese Demo verwenden Sie ein Parquet-Dataset namens Benutzerdaten.
1. Fügen Sie eine Transformation für abgeleitete Spalten hinzu. Damit werden die gewünschten Ordnernamen dynamisch festgelegt.
1. Fügen Sie eine Senkentransformation hinzu.
   
### <a name="hierarchical-folder-output"></a>Hierarchische Ordnerausgabe

Es ist üblich, eindeutige Werte in Ihren Daten zu verwenden, um Ordnerhierarchien zum Partitionieren Ihrer Daten im Lake zu erstellen. Dies ist eine optimale Methode zum Organisieren und Verarbeiten von Daten im Lake und in Spark (die Compute-Engine hinter Datenflüssen). Es fallen jedoch geringe Leistungskosten an, um Ihre Ausgabe auf diese Weise zu organisieren. Mit diesem Mechanismus in der Senke wird eine geringfügige Verringerung der Gesamtleistung der Pipeline erwartet.

1. Wechseln Sie zurück zum Datenfluss-Designer, und bearbeiten Sie den oben erstellten Datenfluss. Klicken Sie auf die Senkentransformation.
1. Klicken Sie auf „Optimieren“ > „Partitionierung festlegen“ > „Schlüssel“.
1. Wählen Sie die Spalte(n) aus, die Sie zum Festlegen Ihrer hierarchischen Ordnerstruktur verwenden möchten.
1. Beachten Sie, dass im folgenden Beispiel Jahr und Monat als Spalten für die Ordnerbenennung verwendet werden. Das Ergebnis sind Ordner in der Form ```releaseyear=1990/month=8```.
1. Wenn Sie auf die Datenpartitionen in einer Datenflussquelle zugreifen, zeigen Sie nur auf den Ordner der obersten Ebene oberhalb von ```releaseyear``` und verwenden ein Platzhaltermuster für jeden nachfolgenden Ordner. Beispiel: ```**/**/*.parquet```
1. Wenn Sie die Datenwerte bearbeiten möchten oder synthetische Werte für Ordnernamen generiert werden müssen, verwenden Sie die Transformation für abgeleitete Spalten, um die Werte zu erstellen, die Sie in Ihren Ordnernamen verwenden möchten.

![Schlüsselpartitionierung](media/data-flow/key-parts.png "Schlüsselpartitionierung")
   
### <a name="name-folder-as-data-values"></a>Name folder as data values (Ordnername aus Datenwerten)

Eine etwas leistungsstärkere Senkentechnik für Lake Data mithilfe von ADLS Gen2, die nicht den gleichen Vorteil wie die Schlüssel-Wert-Partitionierung bietet, ist ```Name folder as column data```. Während der Schlüsselpartitionierungsstil der hierarchischen Struktur Ihnen die Verarbeitung von Datenslices erleichtert, handelt es sich bei dieser Technik um eine vereinfachte Ordnerstruktur, mit der Daten schneller geschrieben werden können.

1. Wechseln Sie zurück zum Datenfluss-Designer, und bearbeiten Sie den oben erstellten Datenfluss. Klicken Sie auf die Senkentransformation.
1. Klicken Sie auf „Optimieren“ > „Partitionierung festlegen“ > „Aktuelle Partitionierung verwenden“.
1. Klicken Sie auf „Einstellungen“ > „Ordnername aus Spaltendaten“.
1. Wählen Sie die Spalte aus, die Sie zum Generieren von Ordnernamen verwenden möchten.
1. Wenn Sie die Datenwerte bearbeiten möchten oder synthetische Werte für Ordnernamen generiert werden müssen, verwenden Sie die Transformation für abgeleitete Spalten, um die Werte zu erstellen, die Sie in Ihren Ordnernamen verwenden möchten.

![Ordneroption](media/data-flow/folders.png "Ordner")

### <a name="name-file-as-data-values"></a>Name file as data values (Dateiname aus Datenwerten)

Die in den obigen Tutorials aufgeführten Techniken sind gute Anwendungsfälle zum Erstellen von Ordnerkategorien in Ihrem Data Lake. Das standardmäßige Dateibenennungsschema, das von diesen Techniken verwendet wird, ist die Verwendung der Spark-Executor-Auftrags-ID. Manchmal möchten Sie möglicherweise den Namen der Ausgabedatei in einer Datenflusstextsenke festlegen. Diese Technik wird nur für die Verwendung mit kleinen Dateien empfohlen. Das Zusammenführen von Partitionsdateien in eine einzelne Ausgabedatei ist ein zeitintensiver Prozess.

1. Wechseln Sie zurück zum Datenfluss-Designer, und bearbeiten Sie den oben erstellten Datenfluss. Klicken Sie auf die Senkentransformation.
1. Klicken Sie auf „Optimieren“ > „Partitionierung festlegen“ > „Einzelne Partition“. Es ist diese Anforderung einer einzelnen Partition, die einen Engpass im Ausführungsprozess verursacht, wenn Dateien zusammengeführt werden. Diese Option wird nur für kleine Dateien empfohlen.
1. Klicken Sie auf „Einstellungen“ > „Dateiname aus Spaltendaten“.
1. Wählen Sie die Spalte aus, die Sie zum Generieren von Dateinamen verwenden möchten.
1. Wenn Sie die Datenwerte bearbeiten möchten oder synthetische Werte für Dateinamen generiert werden müssen, verwenden Sie die Transformation für abgeleitete Spalten, um die Werte zu erstellen, die Sie in Ihren Dateinamen verwenden möchten.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu [Datenflusssenken](data-flow-sink.md)
