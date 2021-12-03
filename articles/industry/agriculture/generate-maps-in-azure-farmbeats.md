---
title: Generieren von Karten
description: In diesem Artikel wird beschrieben, wie Sie Karten in Azure FarmBeats generieren.
author: RiyazPishori
ms.topic: article
ms.date: 11/04/2019
ms.author: riyazp
ms.openlocfilehash: 0183e56d9bdf0c995dbbcf273914790eb0909e7d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132297543"
---
# <a name="generate-maps"></a>Generieren von Karten

Mit Azure FarmBeats können Sie die folgenden Karten erstellen, indem Sie Satellitenbilder und Sensordateneingaben verwenden. Karten dienen Ihnen als Hilfe bei der Anzeige der geografischen Lage Ihres landwirtschaftlichen Betriebs und ermöglichen die Ermittlung geeigneter Orte für Ihre Geräte.

  - **Karte zur Sensorplatzierung:** Enthält Empfehlungen dazu, wie viele Sensoren verwendet und wo diese auf der Fläche eines landwirtschaftlichen Betriebs platziert werden sollten.
  - **Karte zu Satellitenindizes:** Zeigt den Vegetations- und Wasserindex für einen landwirtschaftlichen Betrieb.
  - **Karte zur Bodenfeuchtigkeit:** Zeigt die Verteilung der Bodenfeuchtigkeit an, indem Satelliten- und Sensordaten zusammengefügt werden.

## <a name="sensor-placement-map"></a>Karte zur Sensorplatzierung

Die Karte zur Sensorplatzierung von FarmBeats dient Ihnen als Hilfe beim Platzieren der Sensoren für die Messung der Bodenfeuchtigkeit. Die Ausgabe der Karte umfasst eine Liste mit den Koordinaten für die Sensorbereitstellung. Die Eingaben dieser Sensoren werden zusammen mit Satellitenbildern verwendet, um das Wärmebild für die Bodenfeuchtigkeit zu generieren.

Die Ableitung dieser Karte entsteht durch Segmentieren des an unterschiedlichen Tagen des Jahres aufgenommenen Blätterdachs. Zu diesem Blätterdach zählen auch freie Bodenflächen und Gebäude. Sie können Sensoren entfernen, die an einem bestimmten Ort nicht benötigt werden. Diese Übersicht dient als Leitfaden, und Sie können die Position und die Zahlen anhand Ihrer individuellen Kenntnisse geringfügig ändern. Das Hinzufügen von Sensoren führt nicht zu einer Verschlechterung der Wärmebildergebnisse der Bodenfeuchtigkeit, aber es besteht bei einem Verringern der Anzahl von Sensoren die Möglichkeit, dass die Wärmebildgenauigkeit verschlechtert wird.

## <a name="before-you-begin"></a>Vorbereitung

Stellen Sie sicher, dass die folgenden Voraussetzungen erfüllt sind, bevor Sie versuchen, eine Karte zur Sensorplatzierung zu generieren:

- Der landwirtschaftliche Betrieb muss größer als 4.000 Quadratmeter sein.
- Die Anzahl von Sentinel-Szenen mit wolkenlosem Himmel muss für den ausgewählten Datumsbereich mehr als sechs betragen.
- Mindestens sechs Sentinel-Szenen ohne Wolken müssen einen NDVI (Normalized Difference Vegetation Index) größer oder gleich 0,3 aufweisen.

    > [!NOTE]
    > Bei Sentinel sind nur zwei gleichzeitige Threads pro Benutzer zulässig. Aus diesem Grund werden Aufträge in die Warteschlange eingereiht, sodass ihre Abarbeitung ggf. länger dauern kann.

### <a name="dependencies-on-sentinel"></a>Abhängigkeiten von Sentinel

Die folgenden Abhängigkeiten beziehen sich auf Sentinel:

- Wir benötigen die Sentinel-Leistung zum Herunterladen von Satellitenbildern. Überprüfen Sie den Status der Sentinel-Leistung und die [Wartungsaktivitäten](https://scihub.copernicus.eu/twiki/do/view/SciHubNews/WebHome).
- Bei Sentinel sind nur zwei gleichzeitige [Download-Threads](https://sentinels.copernicus.eu/web/sentinel/sentinel-data-access/typologies-and-services) zulässig.
- Die Generierung von Präzisionskarten ist von der [Sentinel-Abdeckung und -Aufrufhäufigkeit](https://sentinel.esa.int/web/sentinel/user-guides/sentinel-2-msi/revisit-coverage) abhängig.

## <a name="create-a-sensor-placement-map"></a>Erstellen einer Karte zur Sensorplatzierung
In diesem Abschnitt werden die Verfahren zum Erstellen von Karten zur Sensorplatzierung ausführlich beschrieben.

> [!NOTE]
> Sie können Karten zur Sensorplatzierung über die Seite **Maps** (Karten) oder auf der Seite **Farm Details** (Details zum landwirtschaftlichen Betrieb) im Dropdownmenü **Generate Precision Maps** (Präzisionskarten generieren) initiieren.

Führen Sie die folgenden Schritte aus.

1. Greifen Sie auf der Startseite im linken Navigationsmenü auf **Karten** zu.
2. Wählen Sie **Create Maps** (Karten erstellen) und im Dropdownmenü dann die Option **Sensor Placement** (Sensorplatzierung) aus.

    ![Auswählen von „Sensor Placement“ (Sensorplatzierung)](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-1.png)

3. Nachdem Sie **Sensor Placement** (Sensorplatzierung) ausgewählt haben, wird das Fenster **Sensor Placement** angezeigt.

    ![Fenster „Sensor Placement“ (Sensorplatzierung)](./media/get-sensor-data-from-sensor-partner/sensor-placement-1.png)

4. Wählen Sie im Dropdownmenü **Farm** (Landwirtschaftlicher Betrieb) einen Betrieb aus.
   Zum Ermitteln und Auswählen Ihres landwirtschaftlichen Betriebs können Sie in der Dropdownliste scrollen oder den Namen im Textfeld eingeben.
5. Wählen Sie die Option **Recommended** (Empfohlen) aus, um eine Karte für das letzte Jahr zu generieren.
6. Um eine Karte für einen benutzerdefinierten Datumsbereich zu generieren, wählen Sie die Option **Select Date Range** (Datumsbereich auswählen) aus. Geben Sie das Start- und Enddatum ein, für die Sie die Karte zur Sensorplatzierung generieren möchten.
7. Wählen Sie **Karten generieren** aus.
 Eine Bestätigungsmeldung mit den Auftragsdetails wird angezeigt.

  Weitere Informationen zum Auftragsstatus finden Sie unter **Aufträge anzeigen**. Wenn als Auftragsstatus *Fehler* angezeigt wird, wird in der QuickInfo des Status *Fehler* eine ausführliche Fehlermeldung angezeigt. Wiederholen Sie in diesem Fall die vorherigen Schritte, und versuchen Sie es noch einmal.

  Falls das Problem weiterhin besteht, lesen Sie den Abschnitt zur [Problembehandlung](troubleshoot-azure-farmbeats.md), oder wenden sich mit den relevanten Protokollen an das [Azure FarmBeats-Forum](https://aka.ms/FarmBeatsMSDN), um Hilfe zu erhalten.

### <a name="view-and-download-a-sensor-placement-map"></a>Anzeigen und Herunterladen einer Karte zur Sensorplatzierung

Führen Sie die folgenden Schritte aus.

1. Greifen Sie auf der Startseite im linken Navigationsmenü auf **Karten** zu.

    ![Auswählen von „Maps“ (Entitäten) im linken Navigationsmenü](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Wählen Sie im linken Navigationsbereich des Fensters die Option **Filter** aus.
  Im Fenster **Filter** werden Suchkriterien angezeigt.

    ![Fenster „Filter“](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Wählen Sie in den Dropdownmenüs die Werte **Type** (Typ), **Date** (Datum) und **Name** aus. Wählen Sie dann **Apply** (Anwenden) aus, um nach der gewünschten Karte zu suchen.
  Das Erstellungsdatum des Auftrags wird im Format „typ_betriebsname_JJJJ-MM-TT“ angezeigt.
4. Scrollen Sie mithilfe der Navigationsleisten am Ende der Seite durch die Liste mit den verfügbaren Karten.
5. Wählen Sie die Karte aus, die Sie anzeigen möchten. In einem Popupfenster wird eine Vorschau für die ausgewählte Karte angezeigt.
6. Wählen Sie **Herunterladen** aus, und laden Sie die GeoJSON-Datei mit den Sensorkoordinaten herunter.

    ![Vorschau der Karte zur Sensorplatzierung](./media/get-sensor-data-from-sensor-partner/download-sensor-placement-map-1.png)

## <a name="satellite-indices-map"></a>Karte „Satellitenindizes“

In den folgenden Abschnitten werden die Verfahren zum Erstellen und Anzeigen einer Karte vom Typ „Satellitenindizes“ beschrieben.

> [!NOTE]
> Sie können Karten vom Typ „Satellitenindizes“ über die Seite **Maps** (Karten) oder auf der Seite **Farm Details** (Details zum landwirtschaftlichen Betrieb) im Dropdownmenü **Generate Precision Maps** (Präzisionskarten generieren) initiieren.

Mit FarmBeats können Sie für landwirtschaftliche Betriebe Karten der Typen NDVI, EVI (Enhanced Vegetation Index) und NDWI (Normalized Difference Water Index) generieren. Mit diesen Indizes können Sie ermitteln, wie die Pflanzen derzeit wachsen bzw. in der zurückliegenden Zeit gewachsen sind und welche repräsentativen Wasserstände der Boden aufweist.


> [!NOTE]
> Für die Tage, für die die Karte generiert wird, ist ein Sentinel-Bild erforderlich.


## <a name="create-a-satellite-indices-map"></a>Erstellen einer Karte vom Typ „Satellitenindizes“

Führen Sie die folgenden Schritte aus.

1. Greifen Sie auf der Startseite im linken Navigationsmenü auf **Karten** zu.
2. Wählen Sie die Option **Create Maps** (Karten erstellen) und dann im Dropdownmenü **Satellite Indices** (Satellitenindizes) aus.

    ![Auswählen von „Satellite Indices“ (Satellitenindizes) im Dropdownmenü](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-satellite-indices-1.png)

3. Nachdem Sie **Satellite Indices** (Satellitenindizes) ausgewählt haben, wird das Fenster **Satellite Indices** angezeigt.

    ![Fenster „Satellite Indices“ (Satellitenindizes)](./media/get-sensor-data-from-sensor-partner/satellitte-indices-1.png)

4. Wählen Sie im Dropdownmenü einen landwirtschaftlichen Betrieb aus.
   Zum Ermitteln und Auswählen Ihres landwirtschaftlichen Betriebs können Sie in der Dropdownliste scrollen oder den Namen eingeben.   
5. Wählen Sie **This Week** (Diese Woche) aus, um eine Karte für die letzte Woche zu generieren.
6. Um eine Karte für einen benutzerdefinierten Datumsbereich zu generieren, wählen Sie die Option **Select Date Range** (Datumsbereich auswählen) aus. Geben Sie das Start- und das Enddatum ein, für die Sie die Karte „Satellitenindizes“ generieren möchten.
7. Wählen Sie **Karten generieren** aus.
    Eine Bestätigungsmeldung mit den Auftragsdetails wird angezeigt.

    ![Bestätigungsmeldung für die Karte „Satellitenindizes“](./media/get-sensor-data-from-sensor-partner/successful-satellitte-indices-1.png)

    Weitere Informationen zum Auftragsstatus finden Sie unter **Aufträge anzeigen**. Wenn als Auftragsstatus *Fehler* angezeigt wird, wird in der QuickInfo des Status *Fehler* eine ausführliche Fehlermeldung angezeigt. Wiederholen Sie in diesem Fall die vorherigen Schritte, und versuchen Sie es noch einmal.

    Falls das Problem weiterhin besteht, lesen Sie den Abschnitt zur [Problembehandlung](troubleshoot-azure-farmbeats.md), oder wenden sich mit den relevanten Protokollen an das [Azure FarmBeats-Forum](https://aka.ms/FarmBeatsMSDN), um Hilfe zu erhalten.

### <a name="view-and-download-a-map"></a>Anzeigen und Herunterladen einer Karte

Führen Sie die folgenden Schritte aus.

1. Greifen Sie auf der Startseite im linken Navigationsmenü auf **Karten** zu.

    ![Auswählen von Karten](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Wählen Sie im linken Navigationsbereich des Fensters die Option **Filter** aus. Im Fenster **Filter** werden Suchkriterien angezeigt.

    ![Angezeigte Suchkriterien im Fenster „Filter“](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Wählen Sie in den Dropdownmenüs die Werte **Type** (Typ), **Date** (Datum) und **Name** aus. Wählen Sie dann **Apply** (Anwenden) aus, um nach der gewünschten Karte zu suchen.
  Das Erstellungsdatum des Auftrags wird im Format „typ_betriebsname_JJJJ-MM-TT“ angezeigt.

4. Scrollen Sie mithilfe der Navigationsleisten am Ende der Seite durch die Liste mit den verfügbaren Karten.
5. Für jede Kombination aus **Name des landwirtschaftlichen Betriebs** und **Datum** sind die folgenden drei Karten verfügbar:
    - NDVI
    - EVI
    - NDWI
6. Wählen Sie die Karte aus, die Sie anzeigen möchten. In einem Popupfenster wird eine Vorschau für die ausgewählte Karte angezeigt.
7. Wählen Sie im Dropdownmenü **Download** aus, um das Downloadformat auszuwählen. Die Karte wird heruntergeladen und im lokalen Ordner Ihres Computers gespeichert.

    ![Vorschau der ausgewählten Karte „Satellitenindizes“](./media/get-sensor-data-from-sensor-partner/download-satellite-indices-map-1.png)

## <a name="soil-moisture-heatmap"></a>Wärmebild zur Bodenfeuchtigkeit

Die Bodenfeuchtigkeit ist das Wasser, das sich in den Zwischenräumen zwischen den Partikeln des Erdreichs befindet.Mit dem Wärmebild zur Bodenfeuchtigkeit können Sie sich für Ihren landwirtschaftlichen Betrieb eine Übersicht über die Bodenfeuchtigkeitsdaten verschaffen – in jeder Tiefe und in hoher Auflösung. Zum Generieren eines präzisen und verwertbaren Wärmebilds zur Bodenfeuchtigkeit ist eine einheitliche Bereitstellung von Sensoren erforderlich. Alle Sensoren müssen vom selben Anbieter stammen. Geräte unterschiedlicher Anbieter weisen Unterschiede bei der Messung der Bodenfeuchtigkeit und der Kalibrierung auf. Das Wärmebild wird für eine bestimmte Tiefe generiert, indem die in dieser Tiefe angeordneten Sensoren verwendet werden.

### <a name="before-you-begin"></a>Vorbereitung

Stellen Sie sicher, dass die folgenden Voraussetzungen erfüllt sind, bevor Sie versuchen, ein Wärmebild zur Bodenfeuchtigkeit zu generieren:

- Es müssen mindestens drei Sensoren zur Ermittlung der Bodenfeuchtigkeit verwendet werden. Erstellen Sie erst dann ein Wärmebild zur Bodenfeuchtigkeit, nachdem die Sensoren bereitgestellt und dem landwirtschaftlichen Betrieb zugeordnet wurden.
- Die Generierung eines Wärmebilds zur Bodenfeuchtigkeit wird von der Sentinel-Pfadabdeckung, der Wolkendecke und Wolkenschatten beeinflusst. Für den Tag, an dem die Erstellung des Wärmebilds zur Bodenfeuchtigkeit angefordert wurde, muss innerhalb der letzten 120 Tage mindestens eine Sentinel-Szene mit wolkenlosem Himmel verfügbar sein.
- Mindestens die Hälfte der auf der Fläche des landwirtschaftlichen Betriebs bereitgestellten Sensoren muss online sein und Daten an den Datenhub streamen.
- Das Wärmebild muss aus den Messergebnissen von Sensoren desselben Anbieters generiert werden.


## <a name="create-a-soil-moisture-heatmap"></a>Erstellen eines Wärmebilds zur Bodenfeuchtigkeit

Führen Sie die folgenden Schritte aus.

1. Wechseln Sie auf der Startseite im linken Navigationsmenü zu **Maps** (Karten) zu, um die Seite **Maps** anzuzeigen.
2. Wählen Sie die Option **Create Maps** (Karten erstellen) und im Dropdownmenü dann **Soil Moisture** (Bodenfeuchtigkeit) aus.

    ![Auswählen von „Soil Moisture“ (Bodenfeuchtigkeit) im Dropdownmenü](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-soil-moisture-1.png)

3. Nachdem Sie **Soil Moisture** (Bodenfeuchtigkeit) ausgewählt haben, wird das Fenster **Soil Moisture** angezeigt.

    ![Fenster „Soil Moisture“ (Bodenfeuchtigkeit)](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)

4. Wählen Sie im Dropdownmenü **Farm** (Landwirtschaftlicher Betrieb) einen Betrieb aus.
   Zum Ermitteln und Auswählen Ihres landwirtschaftlichen Betriebs können Sie in der Dropdownliste scrollen oder den Namen im Dropdownmenü **Select Farm** (Landwirtschaftlichen Betrieb auswählen) eingeben.
5. Wählen Sie im Dropdownmenü **Select Soil Moisture Sensor Measure** (Maß für Bodenfeuchtigkeitssensor auswählen) das Maß für den Bodenfeuchtigkeitssensor aus, für das Sie die Karte generieren möchten.
Navigieren Sie zur Ermittlung des Sensormaßes zu **Sensors** (Sensoren), und wählen Sie einen beliebigen Bodenfeuchtigkeitssensor aus. Verwenden Sie dann im Abschnitt **Sensor Properties** (Sensoreigenschaften) den Wert unter **Measure Name** (Name des Maßes).
6. Wählen Sie eine der Optionen aus, um eine Karte für **Today** (Heute) oder **This Week** (Dieser Woche) zu generieren.
7. Um eine Karte für einen benutzerdefinierten Datumsbereich zu generieren, wählen Sie die Option **Select Date Range** (Datumsbereich auswählen) aus. Geben Sie das Start- und Enddatum ein, für die Sie das Wärmebild zur Bodenfeuchtigkeit generieren möchten.
8. Wählen Sie **Karten generieren** aus.
 Eine Bestätigungsmeldung mit den Auftragsdetails wird angezeigt.

   ![Bestätigungsmeldung für Wärmebild zur Bodenfeuchtigkeit](./media/get-sensor-data-from-sensor-partner/successful-soil-moisture-1.png)

    Weitere Informationen zum Auftragsstatus finden Sie unter **Aufträge anzeigen**. Wenn als Auftragsstatus *Fehler* angezeigt wird, wird in der QuickInfo des Status *Fehler* eine ausführliche Fehlermeldung angezeigt. Wiederholen Sie in diesem Fall die vorherigen Schritte, und versuchen Sie es noch einmal.

    Falls das Problem weiterhin besteht, lesen Sie den Abschnitt zur [Problembehandlung](troubleshoot-azure-farmbeats.md), oder wenden sich mit den relevanten Protokollen an das [Azure FarmBeats-Forum](https://aka.ms/FarmBeatsMSDN), um Hilfe zu erhalten.

### <a name="view-and-download-a-map"></a>Anzeigen und Herunterladen einer Karte

Führen Sie die folgenden Schritte aus.

1. Greifen Sie auf der Startseite im linken Navigationsmenü auf **Karten** zu.

    ![Navigieren zu „Maps“ (Karten)](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Wählen Sie im linken Navigationsbereich des Fensters die Option **Filter** aus. Das Fenster **Filter** wird angezeigt, in dem Sie nach Karten suchen können.

    ![Auswählen von „Filter“ im linken Navigationsbereich](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3.  Wählen Sie in den Dropdownmenüs die Werte **Type** (Typ), **Date** (Datum) und **Name** aus. Wählen Sie dann **Apply** (Anwenden) aus, um nach der gewünschten Karte zu suchen. Das Erstellungsdatum des Auftrags wird im Format „typ_betriebsname_JJJJ-MM-TT“ angezeigt.
4. Wählen Sie das Symbol **Sortieren** neben den Tabellenüberschriften aus, um nach **Farm** (Landwirtschaftlicher Betrieb), **Date** (Datum), **Created On** (Erstellungsdatum), **Job ID** (Auftrags-ID) oder **Job Type** (Auftragstyp) zu sortieren.
5. Scrollen Sie über die Navigationsschaltfläche am Ende der Seite durch die Liste mit den verfügbaren Karten.
6. Wählen Sie die Karte aus, die Sie anzeigen möchten. In einem Popupfenster wird eine Vorschau für die ausgewählte Karte angezeigt.
7. Wählen Sie im Dropdownmenü **Download** aus, um das Downloadformat auszuwählen. Die Karte wird heruntergeladen und im angegebenen Ordner gespeichert.

    ![Vorschau des Wärmebilds zur Bodenfeuchtigkeit](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)
