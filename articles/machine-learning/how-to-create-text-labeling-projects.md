---
title: Textbeschriftungsprojekt einrichten
titleSuffix: Azure Machine Learning
description: Erstellen Sie ein Projekt zur Beschriftung von Text mit Hilfe des Datenbeschriftungswerkzeugs. Geben Sie entweder ein einzelnes Etikett oder mehrere Etiketten an, die auf jeden Textabschnitt angewendet werden sollen.
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.subservice: mldata
ms.topic: how-to
ms.date: 09/24/2021
ms.custom: data4ml
ms.openlocfilehash: e4d227cf215ab5d37af8a25e6e44e811f739f25b
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/04/2021
ms.locfileid: "129423707"
---
# <a name="create-a-text-labeling-project-and-export-labels-preview"></a>Ein Textbeschriftungsprojekt erstellen und Beschriftungen exportieren (Vorschau)

Erfahren Sie, wie Sie Datenbeschriftungsprojekte erstellen und ausführen, um Textdaten in Azure Machine Learning zu beschriften.  Geben Sie entweder ein einzelnes Etikett oder mehrere Etiketten an, die auf jeden Textabschnitt angewendet werden sollen.

Sie können auch das Datenbeschriftungswerkzeug verwenden, um [ ein Bildbeschriftungsprojekt](how-to-create-image-labeling-projects.md) zu erstellen.

> [!IMPORTANT]
> Die Textbeschriftung ist derzeit als öffentliche Vorschauversion verfügbar.
> Die Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="text-labeling-capabilities"></a>Funktionen zur Textbeschriftung

Azure Machine Learning Datenbeschriftung ist ein zentraler Ort zum Erstellen, Verwalten und Überwachen von Datenbeschriftungsprojekten:

- Koordinieren von Daten, Beschriftungen und Teammitgliedern, um Beschriftungsaufgaben effizient zu verwalten 
- Nachverfolgen des Fortschritts und Verwalten der Warteschlange mit unvollständigen Beschriftungsaufgaben
- Starten und Beenden des Projekts und Steuern des Beschriftungsfortschritts
- Überprüfen Sie die beschrifteten Daten und exportieren Sie die beschrifteten Daten als Azure Machine Learning-Datensatz.

> [!Important]
> Die Textdaten müssen in einem Azure Blob-Datenspeicher verfügbar sein. (Wenn kein Datenspeicher vorhanden ist, können Sie Dateien während der Projekterstellung hochladen.)

Textdaten können entweder TXT- oder CSV-Dateien sein.

* Bei TXT-Dateien stellt jede Datei ein zu beschriftendes Element dar.
* Bei ".csv"-Dateien stellt jede Zeile ein Element dar, das dem Beschriftungsprogramm vorgelegt wird.  Sie können eine oder mehrere Spalten anzeigen, die bei der Beschriftung dieser Zeile verwendet werden.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [prerequisites](../../includes/machine-learning-data-labeling-prerequisites.md)]

## <a name="create-a-text-labeling-project"></a>Erstellen Sie ein Textbeschriftungsprojekt

[!INCLUDE [start](../../includes/machine-learning-data-labeling-start.md)]

1. Zum Erstellen eines Projekts wählen Sie **Projekt hinzufügen** aus. Geben Sie dem Projekt einen geeigneten Namen. Der Projektname kann nicht wiederverwendet werden, auch wenn das Projekt in Zukunft gelöscht wird.

1. Wählen Sie **Text** aus, um ein Textbeschriftungsprojekt zu erstellen.

    :::image type="content" source="media/how-to-create-labeling-projects/text-labeling-creation-wizard.png" alt-text="Erstellen eines Beschriftungsprojekts für die Textbeschriftung.":::

    * Wählen Sie **Textklassifizierung mit mehreren Klassen (Vorschau)** für Projekte aus, in denen nur eine *einzige Beschriftung* aus einer Reihe von Beschriftungen auf das jeweilige Textstück angewendet werden soll.
    * Wählen Sie **Textklassifizierung mit mehreren Beschriftungen (Vorschau)** für Projekte aus, in denen *eine oder mehrere* Beschriftungen aus einer Reihe von Beschriftungen auf das jeweilige Textstück angewendet werden sollen.

1. Wählen Sie **Weiter**, wenn Sie bereit sind, fortzufahren.

## <a name="add-workforce-optional"></a>Arbeitskräfte hinzufügen (optional)

[!INCLUDE [outsource](../../includes/machine-learning-data-labeling-outsource.md)]

## <a name="specify-the-data-to-label"></a>Festlegen der zu beschriftenden Daten

Wenn Sie bereits ein Dataset mit Ihren Daten erstellt haben, wählen Sie dieses aus der Dropdownliste **Vorhandenes Dataset auswählen** aus. Oder wählen Sie **Dataset erstellen** aus, um einen vorhandenen Azure-Datenspeicher auszuwählen oder lokale Dateien hochzuladen.

> [!NOTE]
> Ein Projekt kann nicht mehr als 500.000 Dateien enthalten.  Wenn Ihr Dataset mehr umfasst, werden nur die ersten 500.000 Dateien geladen.  

### <a name="create-a-dataset-from-an-azure-datastore"></a>Erstellen eines Datasets aus einem Azure-Datenspeicher

In vielen Fällen ist es in Ordnung, einfach lokale Dateien hochzuladen. [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/) bietet jedoch eine schnellere und stabilere Methode zum Übertragen großer Datenmengen. Es wird empfohlen, Storage-Explorer als Standardmethode zum Verschieben von Dateien zu verwenden.

Erstellen eines Datasets aus Dateien, die Sie bereits in einem Azure-Blobspeicher gespeichert haben:

1. Wählen Sie **Dataset erstellen** > **Aus Datenspeicher**.
1. Weisen Sie Ihrem Dataset einen **Namen** zu.
1. Wählen Sie den **Datensatztyp**:
    * Wählen Sie **Tabellen**, wenn Sie eine .csv-Datei verwenden, bei der jede Zeile eine Antwort enthält.
    * Wählen Sie **Datei** aus, wenn Sie für jede Antwort separate TXT-Dateien verwenden.
1. (Optional) Geben Sie eine Beschreibung für Ihren Datensatz an.
1. Klicken Sie auf **Weiter**.
1. Wählen Sie den Datenspeicher aus.
1. Wenn Ihre Daten sich in einem Unterordner des Blobspeichers befinden, klicken Sie auf **Durchsuchen**, um den Ordnerpfad auszuwählen.
    * Fügen Sie „/**“ an den Pfad an, um alle Dateien in den Unterordnern des ausgewählten Pfads einzubeziehen.
    * Fügen Sie „* */* .*“ an, um alle Daten im aktuellen Container und den zugehörigen Unterordnern einzubeziehen.
1. Wählen Sie **Weiter** aus.
1. Bestätigen Sie die Informationen. Wählen Sie **Zurück**, um Einstellungen zu ändern, oder **Erstellen**, um das Dataset zu erstellen.

### <a name="create-a-dataset-from-uploaded-data"></a>Erstellen eines Datasets aus hochgeladenen Daten

Direktes Hochladen Ihrer Daten:

1. Wählen Sie **Dataset erstellen** > **Aus lokalen Dateien** aus.
1. Weisen Sie Ihrem Dataset einen **Namen** zu.
1. Wählen Sie den **Datasettyp** aus.
    * Wählen Sie **Tabellarisch** aus, wenn Sie eine CSV-Datei verwenden, in der jede Zeile eine Antwort darstellt.
    * Wählen Sie **Datei** aus, wenn Sie für jede Antwort separate TXT-Dateien verwenden.
1. (Optional) Geben Sie eine Beschreibung Ihres Datensatzes an.
1. Wählen Sie **Weiter** aus.
1. (Optional) Wählen oder erstellen Sie einen Datenspeicher. Sie können auch die Standardeinstellung beibehalten und in den Standard-Blobstore ("workspaceblobstore") Ihres Arbeitsbereichs für maschinelles Lernen hochladen.
1. Wählen Sie **Hochladen**, um die lokale(n) Datei(en) oder Ordner zum Hochladen auszuwählen.
1. Klicken Sie auf **Weiter**.
1. Wenn Sie .csv-Dateien hochladen:
    * Bestätigen Sie die Einstellungen und die Vorschau, wählen Sie **Weiter**.
    * Fügen Sie alle Textspalten ein, die der Beschrifter bei der Klassifizierung dieser Zeile sehen soll.
    * Wählen Sie **Weiter** aus.
1.  Bestätigen Sie die Informationen. Wählen Sie **Zurück**, um Einstellungen zu ändern, oder **Erstellen**, um das Dataset zu erstellen.


## <a name="configure-incremental-refresh"></a><a name="incremental-refresh"> </a> Konfigurieren der inkrementellen Aktualisierung

[!INCLUDE [refresh](../../includes/machine-learning-data-labeling-refresh.md)]

## <a name="specify-label-classes"></a>Angeben von Beschriftungsklassen

[!INCLUDE [classes](../../includes/machine-learning-data-labeling-classes.md)]

## <a name="describe-the-text-labeling-task"></a>Beschreiben Sie die Aufgabe der Textbeschriftung

[!INCLUDE [describe](../../includes/machine-learning-data-labeling-describe.md)]

>[!NOTE]
> Beachten Sie, dass die Beschriftungsersteller die ersten 9 Beschriftungen mit den Zifferntasten 1–9 auswählen können.


## <a name="initialize-the-text-labeling-project"></a>Initialisieren Sie das Textbeschriftungsprojekt

[!INCLUDE [initialize](../../includes/machine-learning-data-labeling-initialize.md)]

## <a name="run-and-monitor-the-project"></a>Ausführen und Überwachen des Projekts

[!INCLUDE [run](../../includes/machine-learning-data-labeling-run.md)]

### <a name="dashboard"></a>Dashboard


Auf der Registerkarte **Dashboard** wird der Fortschritt der Beschriftungsaufgabe angezeigt.

:::image type="content" source="./media/how-to-create-labeling-projects/text-labeling-dashboard.png" alt-text="Dashboard zur Beschriftung von Textdaten":::


Die Fortschrittsübersicht zeigt, wie viele Aufgaben gekennzeichnet, übersprungen, überarbeitet oder noch nicht erledigt wurden.  Bewegen Sie den Mauszeiger über das Diagramm, um die Anzahl der Artikel in jedem Abschnitt zu sehen.

Der mittlere Abschnitt zeigt die Warteschlange der Aufgaben, die noch zugewiesen werden müssen. 


Auf der rechten Seite befindet sich eine Verteilung der Beschriftungen für abgeschlossene Aufgaben.  Beachten Sie, dass ein Element in einigen Projekttypen mehrere Beschriftungen aufweisen kann. In diesem Fall kann die Gesamtanzahl der Beschriftungen größer als die Gesamtanzahl der Elemente sein.

### <a name="data-tab"></a>Registerkarte „Daten“

Auf der Registerkarte **Daten** können Sie Ihr Dataset anzeigen und beschriftete Daten überprüfen. Blättern Sie durch die beschrifteten Daten, um die Beschriftungen zu sehen. Wenn Sie falsch beschriftete Daten sehen, können Sie diese auswählen und auf **Ablehnen** klicken. Dadurch werden die Beschriftungen entfernt und die Daten wieder in die Warteschlange der unbeschrifteten Daten eingereiht.

### <a name="details-tab"></a>Registerkarte „Details“

Zeigen Sie Details zu Ihrem Projekt an.  Auf dieser Registerkarte haben Sie folgende Optionen:

* Anzeigen von Projektdetails und Eingabedatasets
* Aktivieren der inkrementellen Aktualisierung
* Anzeigen von Details zum Speichercontainer, in dem beschriftete Ausgaben in Ihrem Projekt gespeichert werden
* Hinzufügen von Beschriftungserstellern zu Ihrem Projekt
* Bearbeiten von Anweisungen zu Ihren Beschriftungen

### <a name="access-for-labelers"></a>Zugriff für Beschriftungsersteller

[!INCLUDE [access](../../includes/machine-learning-data-labeling-access.md)]

## <a name="add-new-label-class-to-a-project"></a>Hinzufügen einer neuen Beschriftungsklasse zu einem Projekt

[!INCLUDE [add-label](../../includes/machine-learning-data-labeling-add-label.md)]

## <a name="export-the-labels"></a>Exportieren der Beschriftungen
 
Verwenden Sie die Schaltfläche **Exportieren** auf der Seite **Projektdetails** Ihres Beschriftungsprojekts. Sie können die Beschriftungsdaten für Machine Learning-Experimente jederzeit exportieren.

Sie können folgende Inhalte exportieren:

* Eine CSV-Datei. Die CSV-Datei wird im Standardblobspeicher des Azure Machine Learning-Arbeitsbereichs in einem Ordner unter *Labeling/export/csv* erstellt. 
* Ein [Azure Machine Learning-Dataset mit Beschriftungen](how-to-use-labeled-dataset.md). 

Sie können im Abschnitt **Datasets** von Machine Learning auf das exportierte Azure Machine Learning-Dataset zugreifen. Die Seite mit Datasetdetails bietet auch Beispielcode für den Zugriff auf Ihre Beschriftungen aus Python.

![Exportiertes Dataset](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="troubleshooting"></a>Problembehandlung

[!INCLUDE [troubleshooting](../../includes/machine-learning-data-labeling-troubleshooting.md)]


## <a name="next-steps"></a>Nächste Schritte

* [Wie man Text markiert](how-to-label-data.md#label-text)

