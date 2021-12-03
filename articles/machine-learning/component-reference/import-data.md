---
title: 'Daten importieren: Komponenten-Referenz'
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie die Komponente Datenimport in Azure Machine Learning verwenden, um Daten aus vorhandenen Cloud-Datendiensten in eine Pipeline für maschinelles Lernen zu laden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/13/2020
ms.openlocfilehash: ebc81695f8bb481f0f030e348a8a5f2c780a7729
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566429"
---
# <a name="import-data-component"></a>Komponente Daten importieren

Dieser Artikel beschreibt eine Komponente im Azure Machine Learning Designer.

Verwenden Sie diese Komponente, um Daten aus vorhandenen Cloud-Datendiensten in eine Pipeline für maschinelles Lernen zu laden. 

> [!Note]
> Alle von dieser Komponente bereitgestellten Funktionen können von **datastore** und **datasets** auf der worksapce-Landingpage ausgeführt werden. Die Verwendung von **Datenspeichern** und **Datasets** wird empfohlen, da hierbei zusätzliche Features wie Datenüberwachung eingeschlossen sind. Weitere Informationen finden Sie in den Artikeln [Zugreifen auf Daten](../how-to-access-data.md) und [Registrieren von Datasets](../how-to-create-register-datasets.md).
> Nachdem Sie ein Dataset registriert haben, finden Sie es auf der Designer-Oberfläche in der Kategorie **Datasets** -> **My Datasets** (Meine Datasets). Diese Komponente ist für Studio(classic)-Benutzer reserviert, um eine vertraute Erfahrung zu gewährleisten. 
>

Die Komponente **Datenimport** unterstützt das Lesen von Daten aus folgenden Quellen:

- URL über HTTP
- Azure-Cloudspeicher über [**Datenspeicher**](../how-to-access-data.md)
    - Azure-Blobcontainer
    - Azure-Dateifreigabe
    - Azure Data Lake
    - Azure Data Lake Gen2
    - Azure SQL-Datenbank
    - Azure PostgreSQL    

Bevor Sie Cloudspeicher verwenden können, müssen Sie zunächst einen Datenspeicher in Ihrem Azure Machine Learning-Arbeitsbereich registrieren. Weitere Informationen hierzu finden Sie unter [Zugreifen auf Daten](../how-to-access-data.md). 

Nachdem Sie die gewünschten Daten definiert und eine Verbindung mit der Quelle hergestellt haben, leitet **[Import Data](./import-data.md)** den Datentyp jeder Spalte basierend auf den darin enthaltenen Werten ab und lädt die Daten in Ihre Designer-Pipeline. **Import Data** gibt ein Dataset aus, das mit beliebigen Designer-Pipelines verwendet werden kann.

Wenn sich Ihre Quelldaten ändern, können Sie das Dataset aktualisieren und neue Daten hinzufügen, indem Sie [Import Data](./import-data.md) erneut ausführen.

> [!WARNING]
> Wenn sich Ihr Arbeitsbereich in einem virtuellen Netzwerk befindet, müssen Sie Ihre Datenspeicher so konfigurieren, dass sie die Features des Designers zur Datenvisualisierung verwenden. Weitere Informationen zur Verwendung von Datenspeichern und Datasets in virtuellen Netzwerken finden Sie unter [Verwenden von Azure Machine Learning Studio in einem virtuellen Azure-Netzwerk](../how-to-enable-studio-virtual-network.md).


## <a name="how-to-configure-import-data"></a>Konfigurieren von „Import Data“ (Daten importieren)

1. Fügen Sie die Komponente **Datenimport** zu Ihrer Pipeline hinzu. Sie finden diese Komponente in der Kategorie **Dateneingang und -ausgang** im Designer.

1. Wählen Sie die Komponente aus, um das rechte Fenster zu öffnen.

1. Wählen Sie **Datenquelle** und dann den Datenquellentyp aus. Dabei kann es sich um „HTTP“ oder „Datenspeicher“ handeln.

    Wenn Sie „Datenspeicher“ auswählen, können Sie vorhandene Datenspeicher auswählen, die bereits in Ihrem Azure Machine Learning-Arbeitsbereich registriert sind, oder einen neuen Datenspeicher erstellen. Anschließend definieren Sie den Pfad der zu importierenden Daten im Datenspeicher. Sie können den Pfad leicht durchsuchen, indem Sie auf **Pfad durchsuchen** klicken. ![Der Screenshot zeigt den Link „Pfad durchsuchen“, der das Dialogfeld für die Pfadauswahl öffnet.](media/module/import-data-path.png)

    > [!NOTE]
    > Die Komponente **Datenimport** ist nur für **Tabellendaten**.
    > Wenn Sie mehrere tabellarische Datendateien einmal importieren möchten, müssen folgende Bedingungen erfüllt sein, damit keine Fehler auftreten:
    > 1. Wenn Sie alle Datendateien im Ordner einschließen möchten, müssen Sie `folder_name/**` für **Path** (Pfad) eingeben.
    > 2. Alle Datendateien müssen in Unicode-8 codiert sein.
    > 3. Bei allen Datendateien müssen Spaltenanzahl und Spaltennamen identisch sein.
    > 4. Das Ergebnis des Importierens mehrerer Datendateien ist, alle Zeilen aus mehreren Dateien in der richtigen Reihenfolge zu verketten.

1. Wählen Sie das Vorschauschema aus, um die Spalten zu filtern, die Sie einschließen möchten. Sie können auch erweiterte Einstellungen wie Trennzeichen in den Analyseoptionen definieren.

    ![import-data-preview](media/module/import-data.png)

1. Das Kontrollkästchen **Ausgabe neu generieren** entscheidet, ob die Komponente ausgeführt werden soll, um die Ausgabe während der Laufzeit neu zu generieren. 

    Das bedeutet, wenn die Komponente bereits mit denselben Parametern ausgeführt wurde, wird das System die Ausgabe des letzten Laufs wiederverwenden, um die Laufzeit zu verkürzen. 

    Wenn diese Option gewählt wird, führt das System die Komponente erneut aus, um die Nachricht neu zu generieren. Wählen Sie also diese Option aus, wenn die zugrunde liegenden Daten im Speicher aktualisiert werden, um die neuesten Daten zu erhalten.


1. Übermitteln Sie die Pipeline.

    Wenn die Daten durch Import Data in den Designer geladen werden, wird der Datentyp jeder Spalte basierend auf den darin enthaltenen Werten (numerisch oder kategorisch) abgeleitet.

    Sofern eine Spaltenüberschrift vorhanden ist, wird diese zur Benennung der Spalten im Ausgabedataset verwendet.

    Wenn keine Spaltenüberschriften in den Daten enthalten sind, werden neue Spaltennamen im Format col1, col2,... , coln* generiert.

## <a name="results"></a>Ergebnisse

Nachdem der Import abgeschlossen wurde, klicken Sie mit der rechten Maustaste auf das Ausgabedataset, und wählen Sie **Visualize** (Visualisieren) aus, um zu überprüfen, ob die Daten erfolgreich importiert wurden.

Wenn Sie die Daten zur Wiederverwendung speichern möchten, anstatt jedes Mal, wenn die Pipeline ausgeführt wird, einen neuen Datensatz zu importieren, wählen Sie das Symbol **Datensatz registrieren** unter der Registerkarte **Ausgaben+Protokolle** im rechten Bereich der Komponente. Wählen Sie einen Namen für das Dataset aus. Das gespeicherte Dataset enthält die Daten zum Zeitpunkt der Speicherung. Das Dataset wird bei erneuter Ausführung der Pipeline nicht aktualisiert. Das gilt auch, wenn sich das Dataset in der Pipeline ändert. Dies kann hilfreich sein, um Momentaufnahmen von Daten zu erstellen.

Nach dem Importieren der Daten können einige zusätzliche Vorbereitungen zur Modellierung und Analyse erforderlich sein:

- Verwenden Sie [Edit Metadata](./edit-metadata.md) (Metadaten bearbeiten), um Spaltennamen zu ändern, einen abweichenden Datentyp für eine Spalte festzulegen, oder um anzugeben, dass einige Spalten für Bezeichnungen oder Features stehen.

- Verwenden Sie [Select Columns in Dataset](./select-columns-in-dataset.md) (Spalten im Dataset auswählen), um eine Teilmenge von Spalten auszuwählen, die während der Modellierung transformiert oder verwendet werden sollen. Die umgewandelten oder entfernten Spalten können mit Hilfe der Komponente [Spalten hinzufügen](./add-columns.md) leicht wieder in den ursprünglichen Datensatz eingefügt werden.  

- Verwenden Sie [Partition and Sample](./partition-and-sample.md) (Partitionieren und Stichprobe entnehmen), um das Dataset zu unterteilen, ein Sampling durchzuführen oder um Top-N-Zeilen abzurufen.

## <a name="limitations"></a>Einschränkungen

Wenn Ihre Inferenzpipeline die Komponente **Datenimport** enthält, wird sie aufgrund der Zugriffsbeschränkung auf den Datenspeicher bei der Bereitstellung am Echtzeit-Endpunkt automatisch entfernt.

## <a name="next-steps"></a>Nächste Schritte

Siehe die [Sammlung der für Azure Machine Learning verfügbaren Komponenten](component-reference.md). 
