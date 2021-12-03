---
title: Transformieren von Daten im Designer
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie Daten in Azure Machine Learning-Designer importieren und transformieren, um eigene Datasets zu erstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
author: lgayhardt
ms.author: lagayhar
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: designer
ms.openlocfilehash: 42f70e55bf8c2e8b8d6186d26f5a3a1ba08f6a63
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131557450"
---
# <a name="transform-data-in-azure-machine-learning-designer"></a>Transformieren von Daten in Azure Machine Learning-Designer


In diesem Artikel erfahren Sie, wie Sie Datasets in Azure Machine Learning-Designer transformieren und speichern, um Ihre eigenen Daten für Machine Learning vorzubereiten.

Verwenden Sie das Datasetbeispiel [Adult Census Income Binary Classification](./samples-designer.md) (Binäres Klassifizierungsdataset der Einkommenserhebung für Erwachsene), um zwei Datasets vorzubereiten: ein Dataset mit Erhebungsinformationen von Erwachsenen nur aus den USA und ein weiteres Dataset mit Erhebungsinformationen von Erwachsenen außerhalb der USA.

In diesem Artikel werden folgende Vorgehensweisen behandelt:

1. Transformieren eines Datasets, um es für das Training vorzubereiten
1. Exportieren der resultierenden Datasets in einen Datenspeicher
1. Anzeigen der Ergebnisse

Die Ausführung der in dieser Anleitung beschriebenen Schritte ist Voraussetzung für die Schritte im Artikel [Erneutes Trainieren von Modellen mit Azure Machine Learning-Designer (Vorschau)](how-to-retrain-designer.md). In diesem Artikel erfahren Sie, wie Sie mithilfe von transformierten Datasets mehrere Modelle mit Pipelineparametern trainieren.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="transform-a-dataset"></a>Transformieren eines Datasets

In diesem Abschnitt erfahren Sie, wie Sie das Beispieldataset importieren und die Daten in US-Datasets und Nicht-US-Datasets unterteilen. Weitere Informationen zum Importieren eigener Daten in den Designer finden Sie unter [Importieren von Daten im Designer](how-to-designer-import-data.md).

### <a name="import-data"></a>Daten importieren

Führen Sie die folgenden Schritte aus, um das Beispieldataset zu importieren.

1. Melden Sie sich bei <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a> an, und wählen Sie den gewünschten Arbeitsbereich aus.

1. Wechseln Sie zum Designer. Wählen Sie **Benutzerfreundliche vorgefertigte Komponenten** aus, um eine neue Pipeline zu erstellen.

1. Wählen Sie ein Standardcomputeziel zum Ausführen der Pipeline aus.

1. Links neben dem Pipelinezeichenbereich befindet sich eine Palette mit Datasets und Komponenten. Wählen Sie die Option **Datasets**. Sehen Sie sich dann den Abschnitt **Beispiele** an.

1. Ziehen Sie das Dataset **Adult Census Income Binary classification** (Binäres Klassifizierungsdataset der Einkommenserhebung für Erwachsene) per Drag & Drop auf die Canvas.

1. Klicken Sie mit der rechten Maustaste auf die Datasetkomponente **Einkommen von Erwachsenen**, und wählen Sie **Visualisieren** > **Datasetausgabe** aus.

1. Verwenden Sie das Fenster „Datenvorschau“, um das Dataset zu untersuchen. Beachten Sie besonders die Werte in der Spalte „native-country“.

### <a name="split-the-data"></a>Teilen der Daten

In diesem Abschnitt verwenden Sie die Komponente [Daten aufteilen](algorithm-module-reference/split-data.md), um Zeilen zu identifizieren und aufzuteilen, die „United-States“ in der Spalte „native-country“ enthalten. 

1. Erweitern Sie in der Komponentenpalette auf der linken Seite des Zeichenbereich den Abschnitt **Datentransformation**, und suchen Sie nach der Komponente **Daten aufteilen**.

1. Ziehen Sie die Komponente **Daten aufteilen** per Drag & Drop unter die Datasetkomponente im Zeichenbereich.

1. Verbinden der Datasetkomponente mit der Komponente **Daten aufteilen**.

1. Wählen Sie das Modul **Daten aufteilen** aus.

1. Legen Sie rechts neben dem Zeichenbereich im Bereich mit den Komponentendetails die Option **Aufteilungsmodus** auf **Regulärer Ausdruck** fest.

1. Geben Sie den **regulären Ausdruck** ein: `\"native-country" United-States`.

    Der Modus **Regulärer Ausdruck** überprüft eine einzelne Spalte auf einen Wert. Weitere Informationen zur Komponente „Daten aufteilen“ finden Sie auf der entsprechenden [Referenzseite für Algorithmen und Komponenten](algorithm-module-reference/split-data.md).

Ihre Pipeline sollte wie folgt aussehen:

:::image type="content" source="./media/how-to-designer-transform-data/split-data.png" alt-text="Screenshot: Konfigurieren der Pipeline und des Moduls „Daten aufteilen“":::


## <a name="save-the-datasets"></a>Speichern der Datasets

Nachdem die Pipeline nun so eingerichtet ist, dass die Daten aufgeteilt werden, müssen Sie angeben, wo die Datasets gespeichert werden sollen. Verwenden Sie für dieses Beispiel die Komponente **Daten exportieren**, um das Dataset in einem Datenspeicher zu speichern. Weitere Informationen zu Datenspeichern finden Sie unter [Herstellen einer Verbindung mit Azure-Speicherdiensten](how-to-access-data.md)

1. Erweitern Sie in der Komponentenpalette auf der linken Seite des Zeichenbereichs den Abschnitt **Dateneingabe und -ausgabe**, und suchen Sie nach dem Modul **Daten exportieren**.

1. Ziehen Sie zwei Komponenten zum **Exportieren von Daten** per Drag &Drop unter die Komponente **Daten aufteilen**.

1. Verbinden Sie jeden Ausgabeport der Komponente **Daten aufteilen** mit jeweils einer **Daten exportieren**-Komponente.

    Ihre Pipeline sollte in etwa wie folgt aussehen:

    ![Screenshot: Verbinden der Komponenten zum Exportieren von Daten](media/how-to-designer-transform-data/export-data-pipeline.png).

1. Wählen Sie die Komponente **Daten exportieren** aus, die mit dem Port ganz *links* der Komponente zum **Aufteilen von Daten** verbunden ist.

    Die Reihenfolge der Ausgabeports ist für die Komponente **Daten aufteilen** von Bedeutung. Der erste Ausgabeport enthält die Zeilen, in denen der reguläre Ausdruck „true“ ist. In diesem Fall enthält der erste Port Zeilen für ein US-bezogenes Einkommen, und der zweite Port enthält Zeilen für Nicht-US-bezogenes Einkommen.

1. Wählen Sie rechts neben dem Zeichenbereich im Bereich mit den Komponentendetails die folgenden Optionen aus:
    
    **Datenspeichertyp**: Azure Blob Storage

    **Datenspeicher**: Wählen Sie einen vorhandenen Datenspeicher aus, oder klicken Sie auf „Neuer Datenspeicher“, um einen neuen Datenspeicher zu erstellen.

    **Pfad**: `/data/us-income`

    **Dateiformat**: CSV

    > [!NOTE]
    > In diesem Artikel wird davon ausgegangen, dass Sie Zugriff auf einen Datenspeicher haben, der für den aktuellen Azure Machine Learning-Arbeitsbereich registriert ist. Anweisungen zum Einrichten eines Datenspeichers finden Sie unter [Herstellen einer Verbindung mit Azure-Speicherdiensten](how-to-connect-data-ui.md#create-datastores).

    Wenn Sie über keinen Datenspeicher verfügen, können Sie nun einen erstellen. In diesem Artikel werden die Datasets beispielsweise im standardmäßigen BLOB-Speicherkonto gespeichert, das dem Arbeitsbereich zugeordnet ist. Die Datasets werden im `azureml`-Container in einem neuen Ordner mit dem Namen `data` gespeichert.

1.  Wählen Sie die Komponente **Daten exportieren** aus, die mit dem Port ganz *links* der Komponente zum **Aufteilen von Daten** verbunden ist.

1. Wählen Sie rechts neben dem Zeichenbereich im Bereich mit den Komponentendetails die folgenden Optionen aus:
    
    **Datenspeichertyp**: Azure Blob Storage

    **Datenspeicher**: Wählen Sie den gleichen Datenspeicher wie oben aus.

    **Pfad**: `/data/non-us-income`

    **Dateiformat**: CSV

1. Überprüfen Sie, ob die Komponente **Daten exportieren**, die mit dem linken Port der Komponente zum **Aufteilen von Dateien** verbunden ist, den **Pfad** `/data/us-income` aufweist.

1. Überprüfen Sie, ob die Komponente **Daten exportieren**, die mit dem rechten Port verbunden ist, den **Pfad** `/data/non-us-income` aufweist.

    Die Pipeline und die Einstellungen sollten wie folgt aussehen:
    
    ![Screenshot: Konfigurieren der Komponenten zum Exportieren von Daten](media/how-to-designer-transform-data/us-income-export-data.png).

### <a name="submit-the-run"></a>Übermitteln der Ausführung

Nachdem die Pipeline nun so eingerichtet ist, dass die Daten aufgeteilt und exportiert werden, übermitteln Sie die Pipelineausführung.

1. Wählen Sie im oberen Bereich der Canvas die Option **Senden** aus.

1. Wählen Sie im Dialogfeld **Pipelineausführung einrichten** die Option **Neu erstellen** aus, um ein Experiment zu erstellen.

    Mit Experimenten werden zugehörige Pipelineausführungen logisch gruppiert. Wenn Sie diese Pipeline in Zukunft ausführen, sollten Sie für die Protokollierung und Nachverfolgung dasselbe Experiment verwenden.

1. Geben Sie einen beschreibenden Namen für das Experiment wie „split-census-data“ an.

1. Klicken Sie auf **Submit** (Senden).

## <a name="view-results"></a>Anzeigen der Ergebnisse

Nach der Pipelineausführung können Sie die Ergebnisse anzeigen, indem Sie im Azure-Portal zu Ihrem BLOB-Speicher navigieren. Anhand der Zwischenergebnisse der Komponente **Daten aufteilen** können Sie zudem überprüfen, ob Ihre Daten korrekt aufgeteilt wurden.

1. Wählen Sie das Modul **Daten aufteilen** aus.

1. Wählen Sie rechts neben dem Zeichenbereich im Bereich mit den Komponentendetails die Option **Ausgaben und Protokolle** aus. 

1. Wählen Sie das Symbol zum ![Visualisieren](media/how-to-designer-transform-data/visualize-icon.png) neben **Results dataset1** (Ergebnisse von Dataset 1) aus. 

1. Vergewissern Sie sich, dass die Spalte „native-country“ nur den Wert „United-States“ enthält.

1. Wählen Sie das Symbol zum ![Visualisieren](media/how-to-designer-transform-data/visualize-icon.png) neben **Results dataset2** (Ergebnisse von Dataset 2) aus. 

1. Vergewissern Sie sich, dass die Spalte „native-country“ nicht den Wert „United-States“ enthält.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Überspringen Sie diesen Abschnitt, wenn Sie mit Teil 2 der Anleitung unter [Erneutes Trainieren von Modellen mit Azure Machine Learning-Designer](how-to-retrain-designer.md) weitermachen möchten.

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie ein Dataset transformieren und in einem registrierten Datenspeicher speichern.

Machen Sie mit dem nächsten Teil der Anleitung unter [Erneutes Trainieren von Modellen mit Azure Machine Learning-Designer](how-to-retrain-designer.md) weiter, um die transformierten Datasets und Pipelineparameter zum Trainieren von Machine Learning-Modellen zu verwenden.