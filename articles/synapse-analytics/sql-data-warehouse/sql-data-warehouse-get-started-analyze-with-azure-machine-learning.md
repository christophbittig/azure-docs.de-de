---
title: Analysieren von Daten mit Azure Machine Learning
description: Verwenden Sie Azure Machine Learning, um ein Predictive Machine Learning-Modell basierend auf in Azure Synapse gespeicherten Daten zu erstellen.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 07/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-Synapse
ms.openlocfilehash: 830c8e72517da4591b82edee8cf50dc2375f1c86
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131556823"
---
# <a name="analyze-data-with-azure-machine-learning"></a>Analysieren von Daten mit Azure Machine Learning

In diesem Tutorial wird [Azure Machine Learning-Designer](../../machine-learning/concept-designer.md) verwendet, um ein Predictive Machine Learning-Modell zu erstellen. Das Modell basiert auf den in Azure Synapse gespeicherten Daten. Das Szenario für das Tutorial besteht darin vorherzusagen, ob ein Kunde wahrscheinlich ein Fahrrad kauft oder nicht, damit Adventure Works, der Fahrradladen, eine gezielte Marketingkampagne erstellen kann.

## <a name="prerequisites"></a>Voraussetzungen

Zum Durchlaufen dieses Tutorials benötigen Sie Folgendes:

* einen SQL-Pool, der mit AdventureWorksDW-Beispieldaten vorab geladen wurde. Informationen zur Bereitstellung dieses SQL-Pools finden Sie unter [Erstellen eines SQL-Pools](create-data-warehouse-portal.md). Wählen Sie darin die Option zum Laden der Beispieldaten. Wenn Sie bereits ein Data Warehouse aber noch keine Beispieldaten haben, können Sie [Beispieldaten manuell laden](./load-data-from-azure-blob-storage-using-copy.md).
* einen Azure Machine Learning-Arbeitsbereich. Folgen Sie [diesem Tutorial](../../machine-learning/how-to-manage-workspace.md), um einen neuen zu erstellen.

## <a name="get-the-data"></a>Abrufen von Daten

Die verwendeten Daten befinden sich in „AdventureWorksDW“ in der Ansicht „dbo.vTargetMail“. Um den Datenspeicher in diesem Tutorial verwenden zu können, werden die Daten zuerst in das Azure Data Lake Storage-Konto exportiert, da Azure Synapse zurzeit keine Datasets unterstützt. Azure Data Factory kann zum Exportieren von Daten aus dem Data Warehouse in Azure Data Lake Storage mithilfe der [Kopieraktivität](../../data-factory/copy-activity-overview.md) genutzt werden. Verwenden Sie die folgende Abfrage für den Import:

```sql
SELECT [CustomerKey]
  ,[GeographyKey]
  ,[CustomerAlternateKey]
  ,[MaritalStatus]
  ,[Gender]
  ,cast ([YearlyIncome] as int) as SalaryYear
  ,[TotalChildren]
  ,[NumberChildrenAtHome]
  ,[EnglishEducation]
  ,[EnglishOccupation]
  ,[HouseOwnerFlag]
  ,[NumberCarsOwned]
  ,[CommuteDistance]
  ,[Region]
  ,[Age]
  ,[BikeBuyer]
FROM [dbo].[vTargetMail]
```

Sobald die Daten in Azure Data Lake Storage zur Verfügung stehen, werden Datenspeicher in Azure Machine Learning verwendet, um [eine Verbindung mit Azure Storage-Diensten herzustellen](../../machine-learning/how-to-access-data.md). Führen Sie die folgenden Schritte aus, um einen Datenspeicher und ein entsprechendes Dataset zu erstellen:

1. Starten Sie Azure Machine Learning Studio entweder über das Azure-Portal, oder melden Sie sich bei [Azure Machine Learning Studio](https://ml.azure.com/) an.

1. Klicken Sie im linken Bereich des Abschnitts **Verwalten** auf **Datenspeicher** und dann auf **Neuer Datenspeicher**.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/datastores-tab.png" alt-text="Screenshot des linken Bereichs der Azure Machine Learning-Schnittstelle":::

1. Geben Sie einen Namen für den Datenspeicher an, wählen Sie den Typ „Azure Blob Storage“ aus, und geben Sie den Speicherort und die Anmeldeinformationen an. Klicken Sie dann auf **Erstellen**.

1. Klicken Sie anschließend im Abschnitt **Ressourcen** im linken Bereich auf **Datasets**. Wählen Sie **Dataset erstellen** mit der Option **Aus Datenspeicher** aus.

1. Geben Sie den Namen des Datasets an, und wählen Sie als Typ **Tabellarisch** aus. Klicken Sie dann auf **Weiter**, um den Vorgang fortzusetzen.

1. Wählen Sie im Abschnitt **Datenspeicher auswählen oder erstellen** die Option **Zuvor erstellter Datenspeicher** aus. Wählen Sie den zuvor erstellten Datenspeicher aus. Klicken Sie auf „Weiter“, und geben Sie Einstellungen für Pfad und Datei an. Vergessen Sie nicht, die Spaltenüberschrift anzugeben, wenn es eine in den Dateien gibt.

1. Klicken Sie abschließend auf **Erstellen**, um das Dataset zu erstellen.

## <a name="configure-designer-experiment"></a>Konfigurieren des Designerexperiments

Führen Sie als Nächstes die folgenden Schritte für die Designerkonfiguration aus:

1. Klicken Sie im Abschnitt **Autor** im linken Bereich auf die Registerkarte **Designer**.

1. Wählen Sie **Benutzerfreundliche vorgefertigte Module** aus, um eine neue Pipeline zu erstellen.

1. Geben Sie im Bereich „Einstellungen“ rechts den Namen der Pipeline an.

1. Wählen Sie außerdem in der Schaltfläche „Einstellungen“ für einen zuvor bereitgestellten Cluster einen Zielcomputecluster für das gesamte Experiment aus. Schließen Sie den Bereich „Einstellungen“.

## <a name="import-the-data"></a>Importieren der Daten

1. Wählen Sie im linken Bereich unter dem Suchfeld die Unterregisterkarte **Datasets** aus.

1. Ziehen Sie das zuvor erstellte Dataset in den Zeichenbereich.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/import-dataset.png" alt-text="Screenshot der Datasetkomponente im Zeichenbereich":::

## <a name="clean-the-data"></a>Bereinigen der Daten

Löschen Sie Spalten, die für das Modell nicht relevant sind, um die Daten zu bereinigen. Führen Sie dafür die folgenden Schritte aus:

1. Wählen Sie im linken Bereich die Unterregisterkarte **Komponenten** aus.

1. Ziehen Sie unter **Datentransformation > Manipulation** die Komponente **Spalten im Dataset auswählen** in den Zeichenbereich. Verbinden Sie diese Komponente mit der Komponente **Dataset**.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/select-columns-zoomed-in.png" alt-text="Screenshot der Spaltenauswahlkomponente im Zeichenbereich" lightbox="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/select-columns-zoomed-out.png":::

1. Klicken Sie auf die Komponente, um den Eigenschaftenbereich zu öffnen. Klicken Sie auf „Spalte bearbeiten“, um anzugeben, welche Spalten gelöscht werden sollen.

1. Schließen Sie zwei Spalten aus: „CustomerAlternateKey“ und „GeographyKey“. Klicken Sie unten auf der Seite auf **Speichern**.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/drop-columns.png" alt-text="Screenshot der gelöschten Spalten.":::

## <a name="build-the-model"></a>Erstellen des Modells

Die Daten werden „80–20“ aufgeteilt: 80 % zum Trainieren eines Machine Learning-Modells und 20 % zum Testen des Modells. In diesem binären Klassifizierungsproblem werden „Two-Class“-Algorithmen verwendet.

1. Ziehen Sie die Komponente **Daten aufteilen** in den Zeichenbereich.

1. Geben Sie im Eigenschaftenbereich für **Fraction of rows in the first output dataset** (Anteil der Zeilen im ersten Ausgabedataset) den Wert „0,8“ ein.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/split-data.png" alt-text="Screenshot mit dem Aufteilungsverhältnis „0,8“.":::

1. Ziehen Sie die Komponente **Verstärkte Entscheidungsstruktur mit zwei Klassen** in den Zeichenbereich.

1. Ziehen Sie die Komponente **Modell trainieren** in den Zeichenbereich. Nehmen Sie Eingaben vor, indem Sie es mit den Modulen **Verstärkte Entscheidungsstruktur mit zwei Klassen** (ML-Algorithmus) und **Daten aufteilen** (Daten zum Trainieren des Algorithmus) verbinden.

1. Wählen Sie beim Modul „Train Model“ im Bereich „Eigenschaften“ die Option **Label column** (Spalte beschriften) und dann „Spalte bearbeiten“ aus. Wählen Sie die Spalte **BikeBuyer** (Fahrradkäufer) als die vorherzusagende Spalte und dann **Speichern** aus.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/label-column.png" alt-text="Screenshot von „Label column“, „BikeBuyer“ ausgewählt.":::

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/train-model.png" alt-text="Screenshot der Komponente „Train Model“, die mit den Komponenten „Verstärkte Entscheidungsstruktur mit zwei Klassen“ und „Daten aufteilen“ verbunden ist":::

## <a name="score-the-model"></a>Bewertung des Modells

Testen Sie jetzt die Leistung des Modells bei Testdaten. Es werden zwei verschiedene Algorithmen verglichen, um zu sehen, welche Leistung besser ist. Führen Sie dafür die folgenden Schritte aus:

1. Ziehen Sie die Komponente **Score Model** in den Zeichenbereich, und verbinden Sie sie mit den Komponenten **Train Model** und **Daten aufteilen**.

1. Ziehen Sie **Two-Class Averaged Perceptron** (Gemitteltes Perzeptron mit zwei Klassen) in den Experimentbereich. Sie werden jetzt die Leistung dieses Algorithmus mit dem Modul „Two-Class Boosted Decision Tree“ vergleichen.

1. Kopieren Sie die Komponenten **Train Model** und **Score Model**, und fügen Sie sie im Zeichenbereich ein.

1. Ziehen Sie die Komponente **Evaluate Model** in den Zeichenbereich, um die beiden Algorithmen zu vergleichen.

1. Klicken Sie auf **Übermitteln**, um die Pipelineausführung einzurichten.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/algo-comparison-zoomed-in.png" alt-text="Screenshot aller verbleibenden Komponente im Zeichenbereich" lightbox="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/algo-comparison-zoomed-out.png":::

1. Sobald die Ausführung abgeschlossen ist, klicken Sie mit der rechten Maustaste auf die Komponente **Evaluate Model**, und klicken Sie auf  **Auswertungsergebnisse visualisieren**.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/result-visualize-zoomed-out.png" alt-text="Screenshot der Ergebnisse.":::

Folgende Metriken stehen zur Verfügung: ROC-Kurve, Genauigkeit-Trefferquote-Diagramm und Prognosegütekurve. Beim Blick auf diese Metriken sehen Sie, dass mit dem ersten Modell eine bessere Leistung als mit dem zweiten erzielt wurde. Wenn Sie sich anschauen möchten, was das erste Modell vorhergesagt hat, klicken Sie mit der rechten Maustaste auf die Komponente „Score Model“, und klicken Sie auf das Dataset „Visualize Scored“, um die vorhergesagten Ergebnisse anzuzeigen.

Wie Sie sehen können, wurden Ihrem Testdatensatz zwei weitere Spalten hinzugefügt.

* Bewertete Wahrscheinlichkeiten: Die Wahrscheinlichkeit, dass es sich bei einem Kunden um einen Fahrradkäufer handelt.
* Bewertete Beschriftungen: die vom Modell vorgenommene Klassifizierung – Fahrradkäufer (1) oder kein Fahrradkäufer (0). Der Wahrscheinlichkeitsschwellenwert für die Beschriftung ist auf 50 Prozent festgelegt, kann aber angepasst werden.

Durch einen Vergleich der Spalte „BikeBuyer“ (tatsächliche Werte) mit „Bewertete Beschriftungen“ (Vorhersage) können Sie die Leistung des Modells ermitteln. Als Nächstes können Sie anhand dieses Modells Vorhersagen für neue Kunden treffen. Sie können [dieses Modell als Webdienst veröffentlichen](../../machine-learning/tutorial-designer-automobile-price-deploy.md) oder Ergebnisse in Azure Synapse zurückschreiben.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Machine Learning finden Sie unter [Einführung in Machine Learning in Azure](../../machine-learning/overview-what-is-azure-machine-learning.md).

[Hier](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true) erfahren Sie mehr über die integrierte Bewertung im Data Warehouse.