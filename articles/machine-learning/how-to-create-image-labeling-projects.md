---
title: Einrichten eines Bildbeschriftungsprojekts
titleSuffix: Azure Machine Learning
description: Erstellen Sie ein Projekt zur Bildbeschriftung mithilfe des Datenbeschriftungstools. Aktivieren Sie zur Unterstützung bei dieser Aufgabe die ML-unterstützte Beschriftung sowie die Human-in-the-Loop-Beschriftung.
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.subservice: mldata
ms.topic: how-to
ms.date: 10/21/2021
ms.custom: data4ml, ignite-fall-2021
ms.openlocfilehash: b1fc133b02c667ae46d543da3fe020d116d6eb54
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131068891"
---
# <a name="create-an-image-labeling-project-and-export-labels"></a>Erstellen eines Datenbeschriftungsprojekts und Exportieren von Beschriftungen

Erfahren Sie, wie Sie Datenbeschriftungsprojekte erstellen und ausführen, um Bilder in Azure Machine Learning zu beschriften.  Verwenden Sie zur Unterstützung bei dieser Aufgabe die ML-gestützte Datenbeschriftung sowie die Human-in-the-Loop-Beschriftung.

Richten Sie Beschriftungen für Klassifizierung, Objekterkennung (Begrenzungsrahmen) oder Instanzsegmentierung (Polygon) ein.

Sie können auch das Datenbeschriftungstool verwenden, um [ein Bildbeschriftungsprojekt zu erstellen](how-to-create-text-labeling-projects.md).

## <a name="image-labeling-capabilities"></a>Bildbeschriftungsfunktionen

Die Azure Machine Learning-Datenbeschriftung ist ein zentraler Ort zum Erstellen, Verwalten und Überwachen von Datenbeschriftungsprojekten:

- Koordinieren von Daten, Beschriftungen und Teammitgliedern, um Beschriftungsaufgaben effizient zu verwalten
- Nachverfolgen des Fortschritts und Verwalten der Warteschlange mit unvollständigen Beschriftungsaufgaben
- Starten und Beenden des Projekts und Steuern des Beschriftungsfortschritts
- Überprüfen der beschrifteten Daten und Exportieren der beschrifteten Daten als Azure Machine Learning-Dataset

> [!Important]
> Datenbilder müssen als Dateien in einem Azure-Blobdatenspeicher verfügbar sein. (Wenn kein Datenspeicher vorhanden ist, können Sie Dateien während der Projekterstellung hochladen.)

Bilddaten können Dateien eines der folgenden Typen sein: JPG, JPEG, PNG, JPE, JFIF, BMP, TIF, TIFF, DCM, DICOM. Jede Datei ist ein zu beschriftendes Element.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [prerequisites](../../includes/machine-learning-data-labeling-prerequisites.md)]

## <a name="create-an-image-labeling-project"></a>Erstellen eines Bildbeschriftungsprojekts

[!INCLUDE [start](../../includes/machine-learning-data-labeling-start.md)]

1. Zum Erstellen eines Projekts wählen Sie **Projekt hinzufügen** aus. Geben Sie dem Projekt einen geeigneten Namen. Der Projektname kann nicht wiederverwendet werden, auch wenn das Projekt in Zukunft gelöscht wird.

1. Wählen Sie **Bild** aus, um ein Bildbeschriftungsprojekt zu erstellen.

    :::image type="content" source="media/how-to-create-labeling-projects/labeling-creation-wizard.png" alt-text="Erstellen eines Beschriftungsprojekts für die Bildbeschriftung.":::

    * Wählen Sie **Bildklassifizierung mit mehreren Klassen** für Projekte aus, in denen nur eine *einzige Beschriftung* aus einer Reihe von Beschriftungen auf ein Bild angewendet werden soll.
    * Wählen Sie **Bildklassifizierung mit mehreren Beschriftungen** für Projekte aus, in denen *eine oder mehrere* Beschriftungen aus einer Reihe von Beschriftungen auf ein Bild angewendet werden sollen. Ein Foto eines Hunds z. B. kann sowohl mit *Hund* als auch mit *Tag* beschriftet werden.
    * Wählen Sie **Objektidentifikation (umgebendes Rechteck)** für Projekte aus, in denen jedem Objekt innerhalb eines Bilds eine Beschriftung und ein Begrenzungsrahmen zugewiesen werden soll.
    * Wählen Sie für Projekte **Instanzsegmentierung (Polygon)** aus, wenn Sie eine Bezeichnung zuweisen und ein Polygon um jedes Objekt innerhalb eines Bilds zeichnen möchten.

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
1. Der **Datasettyp** wird auf „Datei“ festgelegt. Für Bilder werden nur Dateien als Datasettypen unterstützt.
1. Wählen Sie den Datenspeicher aus.
1. Wenn Ihre Daten sich in einem Unterordner des Blobspeichers befinden, klicken Sie auf **Durchsuchen**, um den Ordnerpfad auszuwählen.
    * Fügen Sie „/**“ an den Pfad an, um alle Dateien in den Unterordnern des ausgewählten Pfads einzubeziehen.
    * Fügen Sie „* */* .*“ an, um alle Daten im aktuellen Container und den zugehörigen Unterordnern einzubeziehen.
1. (Optional) Geben Sie eine Beschreibung für Ihr Dataset an.
1. Wählen Sie **Weiter** aus.
1. Bestätigen Sie die Informationen. Wählen Sie **Zurück**, um Einstellungen zu ändern, oder **Erstellen**, um das Dataset zu erstellen.

### <a name="create-a-dataset-from-uploaded-data"></a>Erstellen eines Datasets aus hochgeladenen Daten

Direktes Hochladen Ihrer Daten:

1. Wählen Sie **Dataset erstellen** > **Aus lokalen Dateien** aus.
1. Weisen Sie Ihrem Dataset einen **Namen** zu.
1. Der **Datasettyp** wird auf „Datei“ festgelegt. Für Bilder werden nur Dateien als Datasettypen unterstützt.
1. (Optional) Geben Sie eine Beschreibung für Ihr Dataset an.
1. Wählen Sie **Weiter** aus.
1. (Optional) Wählen Sie einen Datenspeicher aus, oder erstellen Sie einen. Sie können auch die Standardeinstellung beibehalten und in den Standardblobspeicher (workspaceblobstore) Ihres Machine Learning-Arbeitsbereichs hochladen.
1. Wählen Sie **Durchsuchen** aus, um die lokalen Dateien oder Ordner zum Hochladen auszuwählen.
1. Wählen Sie **Weiter** aus.
1. Bestätigen Sie die Informationen. Wählen Sie **Zurück**, um Einstellungen zu ändern, oder **Erstellen**, um das Dataset zu erstellen.

## <a name="configure-incremental-refresh"></a><a name="incremental-refresh"> </a> Konfigurieren der inkrementellen Aktualisierung

[!INCLUDE [refresh](../../includes/machine-learning-data-labeling-refresh.md)]

## <a name="specify-label-classes"></a>Angeben von Beschriftungsklassen

[!INCLUDE [classes](../../includes/machine-learning-data-labeling-classes.md)]

## <a name="describe-the-image-labeling-task"></a>Beschreiben der Bildbeschriftungsaufgabe

[!INCLUDE [describe](../../includes/machine-learning-data-labeling-describe.md)]

Für Begrenzungsrahmen stellen sich folgende wichtige Fragen:

* Wie wird der Begrenzungsrahmen für diese Aufgabe definiert? Soll er vollständig im Inneren des Objekts befinden, oder soll er sich auf der Außenseite befinden? Soll er so weit wie möglich zugeschnitten werden, oder ist ein gewisser Spielraum akzeptabel?
* Welches Maß an Sorgfalt und Konsistenz erwarten Sie von den Beschriftungserstellern beim Definieren von Begrenzungsrahmen?
* Wie sollen Objekte beschriftet werden, die nur teilweise auf dem Bild zu sehen sind? 
* Wie sollen Objekte beschriftet werden, die teilweise von einem anderen Objekt verdeckt werden?

>[!NOTE]
> Beachten Sie, dass die Beschriftungsersteller die ersten 9 Beschriftungen mit den Zifferntasten 1–9 auswählen können.

## <a name="use-ml-assisted-data-labeling"></a>Verwenden der ML-gestützten Datenbeschriftung

Auf der Seite **Durch ML unterstützte Beschriftung** können Sie automatische Machine Learning-Modelle auslösen, um Beschriftungsaufgaben zu beschleunigen. Medizinische Bilder (DCM) sind nicht in der unterstützten Beschriftung enthalten.

Zu Beginn Ihres Beschriftungsprojekts werden die Elemente in eine zufällige Reihenfolge gebracht, um potenzielle Verzerrungen zu verringern. Im Dataset enthaltene Verzerrungen fließen jedoch in das trainierte Modell ein. Wenn es sich also beispielsweise bei 80 Prozent der Elemente um eine einzelne Klasse handelt, gehören ungefähr 80 Prozent der Daten, die zum Trainieren des Modells verwendet werden, zu dieser Klasse.

Wählen Sie *ML-gestützte Beschriftung aktivieren* aus, und geben Sie eine GPU an, um die Beschriftungsunterstützung zu aktivieren. Wenn Sie in Ihrem Arbeitsbereich noch keins haben, wird ein GPU-Cluster für Sie erstellt und Ihrem Arbeitsbereich hinzugefügt.   Der Cluster wird mit mindestens 0 (null) Knoten erstellt, was bedeutet, dass er nichts kostet, wenn er nicht verwendet wird.


Die ML-unterstützte Beschriftung umfasst zwei Phasen:

* Clustering
* Vorabbeschriftung

Die genaue Anzahl beschrifteter Daten, die zum Starten der Beschriftungsunterstützung erforderlich sind, ist kein fester Wert.  Er kann zwischen Beschriftungsprojekten stark variieren. Bei manchen Projekten werden Clustering- oder Vorabbeschriftungsaufgaben nach der manuellen Beschriftung von 300 Elementen angezeigt. Bei der ML-gestützten Beschriftung kommt ein Verfahren namens *Transferlernen* zum Einsatz. Hierbei wird ein vorab trainiertes Modell verwendet, um den Trainingsprozess zu beschleunigen. Wenn die Klassen Ihres Datasets den Klassen des vorab trainierten Modells ähneln, stehen Vorabbeschriftungen ggf. bereits nach wenigen hundert manuell beschrifteten Elementen zur Verfügung. Wenn sich Ihr Dataset erheblich von den Daten unterscheidet, mit denen das Modell vorab trainiert wurde, dauert es unter Umständen deutlich länger.

Da die abschließenden Beschriftungen weiterhin von den Eingaben des Beschriftungserstellers abhängig sind, wird diese Technologie manchmal auch als *Human-in-the-Loop*-Beschriftung bezeichnet.

> [!NOTE]
> Von der ML-gestützten Datenbeschriftung werden keine Standardspeicherkonten unterstützt, die hinter einem [virtuellen Netzwerk](how-to-network-security-overview.md) geschützt sind. Sie müssen ein nicht standardmäßiges Speicherkonto für die ML-unterstützte Datenbeschriftung verwenden. Das nicht standardmäßige Speicherkonto kann hinter dem virtuellen Netzwerk gesichert werden.

### <a name="clustering"></a>Clustering

Nachdem eine bestimmte Anzahl von Beschriftungen übermittelt wurde, beginnt das Machine Learning-Modell für die Klassifizierung damit, ähnliche Elemente zu gruppieren.  Diese ähnlichen Bilder werden den Beschriftungserstellern im gleichen Bildschirm angezeigt, um das manuelle Taggen zu beschleunigen. Das Clustering ist besonders hilfreich, wenn der Beschriftungsersteller ein Raster mit vier, sechs oder neun Bildern verwendet.

Nachdem ein Machine Learning-Modell mit Ihren manuell beschrifteten Daten trainiert wurde, wird das Modell auf die letzte vollständig verbundene Ebene gekürzt. Nicht beschriftete Bilder durchlaufen dann das gekürzte Modell in einem Prozess, der in der Regel als Einbettung oder Merkmalserstellung bezeichnet wird. Dadurch werden die einzelnen Bilder jeweils in einen hochdimensionalen, durch diese Modellebene definierten Raum eingebettet. Bilder mit Pixelwiederholung in dem Raum werden für Clusteringaufgaben verwendet. 

Die Clusteringphase wird für Objekterkennungsmodelle oder die Textklassifizierung nicht angezeigt.

### <a name="prelabeling"></a>Vorabbeschriftung

Nachdem genügend Beschriftungen übermittelt wurden, wird ein Klassifizierungsmodell verwendet, um Tags vorherzusagen. Oder es wird ein Objekterkennungsmodell verwendet, um Begrenzungsrahmen vorherzusagen. Dem Beschriftungsersteller werden nun Seiten angezeigt, auf denen bereits vorhergesagte Beschriftungen für die einzelnen Elemente vorhanden sind. Bei der Objekterkennung werden auch vorhergesagte Rahmen angezeigt. Diese Vorhersagen müssen dann überprüft und falsch beschriftete Bilder korrigiert werden, bevor die Seite übermittelt wird.  

Nachdem ein Machine Learning-Modell mit Ihren manuell beschrifteten Daten trainiert wurde, wird es anhand eines Testsatzes manuell beschrifteter Elemente ausgewertet, um seine Genauigkeit mit verschiedensten Konfidenzschwellenwerten zu bestimmen. Diese Auswertung dient zur Ermittlung eines Zuverlässigkeitsschwellenwerts, über dem das Modell genau genug ist, um Vorabbeschriftungen anzuzeigen. Anschließend wird das Modell anhand von nicht beschrifteten Daten ausgewertet. Elemente, bei denen die Vorhersagezuverlässigkeit über dem Schwellenwert liegt, werden für die Vorabbeschriftung verwendet.

## <a name="initialize-the-image-labeling-project"></a>Initialisieren des Bildbeschriftungsprojekts

[!INCLUDE [initialize](../../includes/machine-learning-data-labeling-initialize.md)]

## <a name="run-and-monitor-the-project"></a>Ausführen und Überwachen des Projekts

[!INCLUDE [run](../../includes/machine-learning-data-labeling-run.md)]

### <a name="dashboard"></a>Dashboard

Auf der Registerkarte **Dashboard** wird der Fortschritt der Beschriftungsaufgabe angezeigt.

:::image type="content" source="./media/how-to-create-labeling-projects/labeling-dashboard.png" alt-text="Dashboard für Datenbeschriftung":::

Die Fortschrittsübersicht zeigt, wie viele Elemente beschriftet, übersprungen oder noch nicht erledigt wurden bzw. noch überprüft werden müssen.  Bewegen Sie den Mauszeiger über das Diagramm, um die Anzahl der Artikel in jedem Abschnitt zu sehen.

Der mittlere Abschnitt zeigt die Warteschlange der Aufgaben, die noch zugewiesen werden müssen. Wenn die ML-gestützte Beschriftung deaktiviert ist, wird in diesem Abschnitt die Anzahl der manuellen Aufgaben angezeigt, die zugewiesen werden müssen. Wenn die ML-gestützte Beschriftung aktiviert ist, wird auch Folgendes angezeigt:

* Aufgaben, die gruppierte Elemente in der Warteschlange enthalten
* Aufgaben, die vorbeschriftete Elemente in der Warteschlange enthalten

Wenn die ML-gestützte Beschriftung aktiviert ist, wird außerdem in einer kleinen Statusleiste angezeigt, wann das nächste Training ausgeführt wird.  In den Abschnitten zu Experimenten werden Links zu den einzelnen Machine Learning-Ausführungen angezeigt.

* Training – trainiert ein Modell zum Vorhersagen der Beschriftungen.
* Validierung – bestimmt, ob die Vorhersage dieses Modells für das Vorbeschriften der Elemente verwendet wird. 
* Rückschluss – Vorhersageausführung für neue Elemente
* Featurisierung – Clusterelemente (nur für Bildklassifizierungsprojekte)

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
* Bearbeiten der Details der ML-gestützten Beschriftung einschließlich Aktivieren/Deaktivieren

### <a name="access-for-labelers"></a>Zugriff für Beschriftungsersteller

[!INCLUDE [access](../../includes/machine-learning-data-labeling-access.md)]

## <a name="add-new-label-class-to-a-project"></a>Hinzufügen einer neuen Beschriftungsklasse zu einem Projekt

[!INCLUDE [add-label](../../includes/machine-learning-data-labeling-add-label.md)]

## <a name="export-the-labels"></a>Exportieren der Beschriftungen

Verwenden Sie die Schaltfläche **Exportieren** auf der Seite **Projektdetails** Ihres Beschriftungsprojekts. Sie können die Beschriftungsdaten für Machine Learning-Experimente jederzeit exportieren. 

* Bildbeschriftungen können wie folgt exportiert werden:
    * [COCO-Format](http://cocodataset.org/#format-data). Die COCO-Datei wird im Standardblobspeicher des Azure Machine Learning-Arbeitsbereichs in einem Ordner unter *Labeling/export/coco* erstellt. 
    * Ein [Azure Machine Learning-Dataset mit Beschriftungen](how-to-use-labeled-dataset.md). 

Sie können im Abschnitt **Datasets** von Machine Learning auf das exportierte Azure Machine Learning-Dataset zugreifen. Die Seite mit Datasetdetails bietet auch Beispielcode für den Zugriff auf Ihre Beschriftungen aus Python.

![Exportiertes Dataset](./media/how-to-create-labeling-projects/exported-dataset.png)

Nachdem Sie Ihre beschrifteten Daten in ein Azure Machine Learning-Dataset exportiert haben, können Sie mithilfe des automatisierten ML Modelle für maschinelles Sehen erstellen, die mit Ihren beschrifteten Daten trainiert werden. Informationen zum Einrichten von automatisiertem ML für das Trainieren von Modellen für maschinelles Sehen mit Python (Vorschau) finden Sie [hier](how-to-auto-train-image-models.md).

## <a name="troubleshooting"></a>Problembehandlung

[!INCLUDE [troubleshooting](../../includes/machine-learning-data-labeling-troubleshooting.md)]

### <a name="object-detection-troubleshooting"></a>Problembehandlung bei der Objekterkennung

|Problem  |Lösung  |
|---------|---------|
|Wird während der Erstellung von Bezeichnungen für die Objekterkennung ESC gedrückt, wird in der linken oberen Ecke eine Bezeichnung mit der Größe null erstellt. In diesem Fall ist die Übermittlung von Bezeichnungen nicht erfolgreich.     |   Löschen Sie die Bezeichnung, indem Sie auf das daneben angezeigte Kreuzsymbol klicken.  |

## <a name="next-steps"></a>Nächste Schritte

* [Tutorial: Erstellen eines Beschriftungsprojekts für mehrklassige Bildklassifizierung](tutorial-labeling.md).
* [Markieren von Bildern](how-to-label-data.md)
