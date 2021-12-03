---
title: 'ML Studio (Classic): Migrieren zu Azure Machine Learning – Neuerstellen von Datasets'
description: Neuerstellen von Studio (Classic)-Datasets im Azure Machine Learning-Designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 10/21/2021
ms.openlocfilehash: 410ffe44ac260ffe9208895ddac0da07a93c4a1d
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131552732"
---
# <a name="migrate-a-studio-classic-dataset-to-azure-machine-learning"></a>Migrieren eines Studio (Classic)-Datasets zu Azure Machine Learning

[!INCLUDE [ML Studio (classic) retirement](../../includes/machine-learning-studio-classic-deprecation.md)]

In diesem Artikel erfahren Sie, wie Sie ein Studio (Classic)-Dataset zu Azure Machine Learning migrieren. Weitere Informationen zur Migration aus Studio (Classic) finden Sie im Artikel mit der [Übersicht über die Migration](migrate-overview.md).

Sie haben drei Möglichkeiten, ein Dataset zu Azure Machine Learning zu migrieren. Lesen Sie die einzelnen Abschnitte, um zu bestimmen, welche Option sich am besten für Ihr Szenario eignet.


|Wo befinden sich die Daten? | Migrationsoption  |
|---------|---------|
|In Studio (Classic)     |  Option 1: [Herunterladen des Datasets aus Studio (Classic) und anschließendes Hochladen in Azure Machine Learning](#download-the-dataset-from-studio-classic)      |
|Cloudspeicher     | Option 2: [Registrieren eines Datasets aus einer Clouddatenquelle](#import-data-from-cloud-sources) <br><br>  Option 3: [Verwenden des Moduls „Daten importieren“ zum Abrufen von Daten aus einer Clouddatenquelle](#import-data-from-cloud-sources)        |

> [!NOTE]
> Azure Machine Learning unterstützt auch [Code First-Workflows](how-to-create-register-datasets.md) zum Erstellen und Verwalten von Datasets. 

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Ein Azure Machine Learning-Arbeitsbereich. [Erstellen Sie einen Azure Machine Learning-Arbeitsbereich](how-to-manage-workspace.md#create-a-workspace).
- Ein zu migrierendes Studio (Classic)-Dataset.


## <a name="download-the-dataset-from-studio-classic"></a>Herunterladen des Datasets aus Studio (Classic)

Die einfachste Methode zum Migrieren eines Studio (Classic)-Datasets zu Azure Machine Learning ist das Herunterladen des Datasets und seine Registrierung in Azure Machine Learning. Dadurch wird eine neue Kopie des Datasets erstellt und in einen Azure Machine Learning-Datenspeicher hochgeladen.

Die folgenden Studio (Classic)-Datasettypen können Sie direkt herunterladen.

* Nur-Text (.txt)
* Durch Trennzeichen getrennte Werte (CSV) mit einer Kopfzeile (.csv) oder ohne (.nh.csv)
* Durch Tabulator getrennte Werte (TSV) mit einer Kopfzeile (.tsv) oder ohne (.nh.tsv)
* Excel-Datei
* ZIP-Datei (.zip)

So laden Sie Datasets direkt herunter
1. Navigieren Sie zu Ihrem Studio (Classic)-Arbeitsbereich ([https://studio.azureml.net](https://studio.azureml.net)).
1. Wählen Sie auf der linken Navigationsleiste die Registerkarte **Datasets** aus.
1. Wählen Sie die Datasets aus, die heruntergeladen werden sollen.
1. Wählen Sie auf der unteren Aktionsleiste die Option **Herunterladen** aus.

    ![Screenshot, der das Herunterladen eines Datasets in Studio (Classic) zeigt](./media/migrate-register-dataset/download-dataset.png)

Bei den folgenden Datentypen müssen Sie zum Herunterladen von Datasets das Modul **In CSV konvertieren** verwenden.

* SVMLight-Daten (.svmlight) 
* ARFF-Daten (Attribute Relation File Format, .arff) 
* R-Objektdatei oder -Arbeitsbereichsdatei (.RData)
* Datasettyp (.data). Der Datasettyp ist der interne Studio (Classic)-Datentyp für die Modulausgabe.

So konvertieren Sie das Dataset in das CSV-Format und laden die Ergebnisse herunter

1. Navigieren Sie zu Ihrem Studio (Classic)-Arbeitsbereich ([https://studio.azureml.net](https://studio.azureml.net)).
1. Erstellen eines neuen Experiments.
1. Ziehen Sie das herunterzuladende Dataset auf die Canvas.
1. Fügen Sie ein **In CSV konvertieren**-Modul hinzu.
1. Verbinden Sie den Eingabeport von **In CSV konvertieren** mit dem Ausgabeport des Datasets.
1. Führen Sie das Experiment aus.
1. Klicken Sie mit der rechten Maustaste auf das Modul **In CSV konvertieren**.
1. Wählen Sie **Results dataset** (Ergebnisdataset) > **Herunterladen** aus.

    ![Screenshot der Einrichtung einer Pipeline zum Konvertieren in das CSV-Format](./media/migrate-register-dataset/csv-download-dataset.png)

### <a name="upload-your-dataset-to-azure-machine-learning"></a>Hochladen des Datasets in Azure Machine Learning

Nachdem Sie die Datendatei heruntergeladen haben, können Sie das Dataset in Azure Machine Learning registrieren:

1. Wechseln Sie zu Azure Machine Learning Studio ([ml.azure.com](https://ml.azure.com)).
1. Wählen Sie im linken Navigationsbereich die Registerkarte **Datasets** aus.
1. Wählen Sie **Dataset erstellen** > **Aus lokalen Dateien** aus.
    ![Screenshot der Registerkarte „Datasets“ und der Schaltfläche zum Erstellen einer lokalen Datei](./media/migrate-register-dataset/register-dataset.png)
1. Geben Sie einen Namen und eine Beschreibung an.
1. Wählen Sie unter **Datasettyp** die Option **Tabellarisch** aus.

    > [!NOTE]
    > Sie können auch ZIP-Dateien als Datasets hochladen. Wählen Sie zum Hochladen einer ZIP-Datei unter **Datasettyp** die Option **Datei** aus.

1. Wählen Sie unter **Datenspeicher- und Dateiauswahl** den Datenspeicher aus, in den die Datasetdatei hochgeladen werden soll.

    Standardmäßig wird das Dataset in Azure Machine Learning im Blobspeicher des Standardarbeitsbereichs gespeichert. Weitere Informationen zu Datenspeichern finden Sie unter [Herstellen einer Verbindung mit Speicherdiensten](how-to-access-data.md).

1. Legen Sie die Einstellungen für die Datenanalyse und das Schema für das Dataset fest. Bestätigen Sie anschließend die Einstellungen.

## <a name="import-data-from-cloud-sources"></a>Importieren von Daten aus Clouddatenquellen

Wenn sich Ihre Daten bereits in einem Cloudspeicherdienst befinden und am nativen Speicherort beibehalten werden sollen, haben Sie folgende Möglichkeiten:

|Erfassungsmethode|BESCHREIBUNG|
|---| --- |
|Registrieren eines Azure Machine Learning-Datasets|Die Daten werden aus lokalen und Onlinedatenquellen (Blob, ADLS Gen1, ADLS Gen2, Dateifreigabe, SQL DB) erfasst. <br><br>Ein Verweis auf die Datenquelle wird erstellt, der zur Laufzeit verzögert ausgewertet wird. Verwenden Sie diese Option, wenn Sie wiederholt auf das Dataset zugreifen und erweiterte Datenfunktionen wie die Datenversionsverwaltung und Datenüberwachung aktivieren möchten.
|Modul „Import Data“ (Daten importieren)|Die Daten werden aus Onlinedatenquellen (Blob, ADLS Gen1, ADLS Gen2, Dateifreigabe, SQL DB) erfasst. <br><br> Das Dataset wird nur in die aktuelle Designer-Pipelineausführung importiert.


>[!Note]
> Benutzer von Studio (Classic) sollten beachten, dass die folgenden Clouddatenquellen in Azure Machine Learning nicht nativ unterstützt werden:
> - Hive-Abfrage
> - Azure Table
> - Azure Cosmos DB
> - Lokale SQL-Datenbank
>
> Es wird empfohlen, dass Benutzer ihre Daten mithilfe von Azure Data Factory zu unterstützten Speicherdiensten migrieren.  

### <a name="register-an-azure-machine-learning-dataset"></a>Registrieren eines Azure Machine Learning-Datasets

Führen Sie die folgenden Schritte aus, um ein Dataset aus einem Clouddienst in Azure Machine Learning zu registrieren: 

1. [Erstellen Sie einen Datenspeicher](how-to-connect-data-ui.md#create-datastores), über den der Cloudspeicherdienst mit Ihrem Azure Machine Learning-Arbeitsbereich verbunden wird. 

1. [Registrieren Sie ein Dataset](how-to-connect-data-ui.md#create-datasets). Wenn Sie ein Studio (Classic)-Dataset migrieren, wählen Sie die Einstellung **Tabellarisch** als Datasettyp aus.

Nachdem Sie ein Dataset in Azure Machine Learning registriert haben, können Sie es im Designer verwenden:
 
1. Erstellen Sie einen neuen Designer-Pipelineentwurf.
1. Erweitern Sie in der Modulpalette links den Bereich **Datasets**.
1. Ziehen Sie das registrierte Dataset auf die Canvas. 

### <a name="use-the-import-data-module"></a>Verwenden des Moduls „Daten importieren“

Führen Sie die folgenden Schritte aus, um Daten direkt in die Designer-Pipeline zu importieren:

1. [Erstellen Sie einen Datenspeicher](how-to-connect-data-ui.md#create-datastores), über den der Cloudspeicherdienst mit Ihrem Azure Machine Learning-Arbeitsbereich verbunden wird. 

Nach dem Erstellen des Datenspeichers können Sie das Modul [**Daten importieren**](algorithm-module-reference/import-data.md) im Designer verwenden, um Daten daraus zu erfassen:

1. Erstellen Sie einen neuen Designer-Pipelineentwurf.
1. Suchen Sie in der Modulpalette links das Modul **Daten importieren**, und ziehen Sie es auf die Canvas.
1. Wählen Sie das Modul **Daten importieren** aus, und verwenden Sie die Einstellungen im rechten Bereich, um die Datenquelle zu konfigurieren.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie ein Studio (Classic)-Dataset zu Azure Machine Learning migrieren. Im nächsten Schritt [erstellen Sie eine Studio (Classic)-Trainingspipeline neu](migrate-rebuild-experiment.md).


Weitere Informationen finden Sie in den anderen Artikeln der Reihe zur Migration von Studio (Classic):

1. [Übersicht über die Migration](migrate-overview.md).
1. **Migrieren von Datasets**
1. [Neuerstellen einer Studio (Classic)-Trainingspipeline](migrate-rebuild-experiment.md)
1. [Neuerstellen eines Studio (Classic)-Webdiensts](migrate-rebuild-web-service.md)
1. [Integrieren eines Azure Machine Learning-Webdiensts in Clientanwendungen](migrate-rebuild-integrate-with-client-app.md)
1. [Migrieren von „R-Skript ausführen“-Modulen](migrate-execute-r-script.md)
