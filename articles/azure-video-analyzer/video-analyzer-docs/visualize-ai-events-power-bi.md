---
title: Echtzeitvisualisierung von KI-Rückschlussereignissen mit Power BI
description: Sie können Azure Video Analyzer für fortlaufende oder ereignisbasierte Videoaufzeichnungen verwenden. In diesem Tutorial werden die Schritte zum Visualisieren von KI-Rückschlussereignissen von IoT Hub in Microsoft Power BI in Echtzeit vorgestellt.
ms.service: azure-video-analyzer
ms.topic: tutorial
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 571e89e1616f8e7ab2ee0eca021ea1020dea9140
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131564069"
---
# <a name="tutorial-real-time-visualization-of-ai-inference-events-with-power-bi"></a>Tutorial: Echtzeitvisualisierung von KI-Rückschlussereignissen mit Power BI

Azure Video Analyzer bietet die Möglichkeit zum Erfassen, Aufzeichnen und Analysieren von Livevideos sowie zum Veröffentlichen der Ergebnisse der Videoanalyse in Form von KI-Rückschlussereignissen im [IoT Edge Hub](../../iot-edge/iot-edge-runtime.md?view=iotedge-2020-11&preserve-view=true#iot-edge-hub). Diese KI-Rückschlussereignisse können dann an andere Ziele wie Visual Studio Code und Azure-Dienste wie Time Series Insights und Event Hubs geroutet werden.

Dashboards stellen eine aufschlussreiche Möglichkeit dar, Ihr Geschäft zu überwachen und Ihre wichtigsten Metriken zusammen in einer Übersicht anzuzeigen. Sie können KI-Rückschlussereignisse, die von Video Analyzer mithilfe von [Microsoft Power BI](https://powerbi.microsoft.com/) generiert wurden, über [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/#overview) visualisieren, um schnell Erkenntnisse zu gewinnen und Dashboards mit Kollegen in Ihrer Organisation zu teilen.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/power-bi/tutorial-block-diagram.svg" alt-text="Blockdiagramm zum Verbinden von Azure Video Analyzer mit Microsoft Power BI über Azure Stream Analytics.":::

In diesem Lernprogramm lernen Sie Folgendes:

- Erstellen und Ausführen eines Stream Analytics-Auftrags zum Abrufen der erforderlichen Daten bei IoT Hub und deren Senden an Power BI
- Ausführen einer Livepipeline, die Rückschlussereignisse generiert
- Erstellen eines Power BI Dashboards zum Visualisieren der KI-Rückschlüsse

## <a name="suggested-pre-reading"></a>Empfohlene Lektüre zur Vorbereitung

- [Überwachung und Protokollierung](monitor-log-edge.md) in Video Analyzer
- Lesen von [D2C-Nachrichten von den integrierten Endpunkten von IoT Hub](../../iot-hub/iot-hub-devguide-messages-read-builtin.md)
- Einführung in [Power BI-Dashboards](/power-bi/create-reports/service-dashboards)

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), falls Sie noch keins besitzen.
- Dieses Tutorial basiert auf der Verwendung des [Linienüberquerungsbeispiels](use-line-crossing.md), in dem erkannt werden soll, wenn Objekte in einem Livevideofeed eine virtuelle Linie überschreiten. Sie können sich entscheiden, Visualisierungen für andere Pipelines zu erstellen – **eine Pipeline mit einer IoT Hub-Nachrichtensenke ist Voraussetzung**. Vergewissern Sie sich, dass Sie die Livepipeline erstellt haben, aktivieren Sie sie jedoch erst, nachdem Sie einen Stream Analytics-Auftrag erstellt haben.

  > [!TIP]
  >
  > - Das [Linienüberquerungsbeispiel](use-line-crossing.md) verwendet eine fünfminütige Videoaufzeichnung. Zum Erzielen optimaler Ergebnisse bei der Visualisierung sollten Sie die 60-minütige Aufzeichnung von Fahrzeugen auf einem Freeway verwenden, die unter [Anderes Dataset](https://github.com/Azure/video-analyzer/tree/main/media#other-dataset) verfügbar ist.
  > - Informationen zum Hinzufügen von Beispielvideodateien zum RTSP-Simulator finden Sie im Abschnitt „Konfiguration und Bereitstellung“ unter [Häufig gestellte Fragen](faq-edge.yml). Bearbeiten Sie nach dem Hinzufügen den Wert von `rtspUrl`, sodass er auf die neue Videodatei verweist.
  > - Wenn Sie dem Linienüberquerungsbeispiel folgen und das [AVA-C#-Beispielrepository](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp) verwenden, ändern Sie in der Datei „operations.json“ unter „properties“ > „parameters“ > „value“ in `"rtsp://rtspsim:554/media/camera-3600s.mkv"`, um die Videoquelle in die 60-minütige Aufzeichnung zu ändern.

- Ein [Power BI](https://powerbi.microsoft.com/)-Konto.

## <a name="create-and-run-a-stream-analytics-job"></a>Erstellen und Ausführen eines Stream Analytics-Auftrags

[Stream Analytics](https://azure.microsoft.com/services/stream-analytics/#overview) ist ein vollständig verwalteter Echtzeitanalysedienst zum Analysieren und Verarbeiten schneller Datenströme. In diesem Abschnitt erstellen Sie einen Stream Analytics-Auftrag und definieren die Eingaben, Ausgaben sowie die Abfrage, die zum Abrufen der erforderlichen Daten verwendet wird.

### <a name="create-a-stream-analytics-job"></a>Erstellen eines Stream Analytics-Auftrags

1. Klicken Sie im [Azure-Portal](https://portal.azure.com/) auf **Ressource erstellen**. Geben Sie im Suchfeld den Suchbegriff _Stream Analytics-Auftrag_ ein, und wählen Sie ihn aus der Dropdownliste aus. Wählen Sie auf der Übersichtsseite **Stream Analytics-Auftrag** die Option **Erstellen** aus.
2. Geben Sie die folgenden Informationen für den Auftrag ein:

   - **Auftragsname**: Der Name des Auftrags. Der Name muss global eindeutig sein.
   - **Abonnement**: Wählen Sie dasselbe Abonnement aus, das Sie zum Einrichten des Linienüberquerungsbeispiels verwendet haben.
   - **Ressourcengruppe**: Verwenden Sie dieselbe Ressourcengruppe, die ihr IoT Edge Hub beim Einrichten des Linienüberquerungsbeispiels verwendet hat.
   - **Standort**: Verwenden Sie denselben Speicherort wie für Ihre Ressourcengruppe.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/power-bi/create-asa-job.png" alt-text="Screenshot: Erstellen eines neuen Stream Analytics-Auftrags.":::

3. Klicken Sie auf **Erstellen**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Hinzufügen einer Eingabe zum Stream Analytics-Auftrag

1. Öffnen Sie den Stream Analytics-Auftrag.
2. Wählen Sie unter Auftragstopologie die Option **Eingaben** aus.
3. Wählen Sie im Bereich Eingaben die Option **Datenstromeingabe hinzufügen** und dann **IoT Hub** aus der Dropdownliste aus. Geben Sie im neuen Eingabebereich die folgenden Informationen ein:

   - **Eingabealias**: Geben Sie einen eindeutigen Alias für die Eingabe ein, z. B. „iothubinput“.
   - **IoT Hub aus Ihrem Abonnement auswählen**: Wählen Sie dieses Optionsfeld aus.
   - **Abonnement**: Wählen Sie das Azure-Abonnement aus, das Sie für diesen Artikel verwenden.
   - **IoT Hub**: Wählen Sie den IoT Hub aus, den Sie zur Einrichtung des Linienüberquerungsbeispiels verwendet haben.
   - **Name der SAS-Richtlinie**: Wählen Sie den Namen der SAS-Richtlinie aus, die vom Stream Analytics-Auftrag für Ihren IoT Hub verwendet werden soll. Für dieses Tutorial können Sie „iothubowner“ auswählen. Weitere Informationen finden Sie unter [Access Control und Berechtigungen](../../iot-hub/iot-hub-dev-guide-sas.md#access-control-and-permissions).
   - **Schlüssel für SAS-Richtlinie**: Dieses Feld wird auf Grundlage des von Ihnen ausgewählten Namens der SAS-Richtlinie automatisch ausgefüllt.

   Übernehmen Sie bei allen anderen Feldern die Standardwerte.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/power-bi/add-iothub-input.png" alt-text="Screenshot: Hinzufügen der IoT Hub-Eingabe zum Stream Analytics-Auftrag.":::

4. Wählen Sie **Speichern** aus.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Hinzufügen einer Ausgabe zum Stream Analytics-Auftrag

1. Wählen Sie unter Auftragstopologie **Ausgaben** aus.
2. Wählen Sie im Bereich Ausgaben die Option **Hinzufügen** und dann **Power BI** aus der Dropdownliste aus.
3. Im „Bereich Power BI – Neue Ausgabe“:

   - **Ausgabealias**: Geben Sie einen eindeutigen Alias für die Ausgabe ein, z. B. „powerbioutput“.
   - **Gruppenarbeitsbereich**: Wählen Sie den Arbeitsbereich der Zielgruppe aus.
   - **Authentifizierungsmodus**: Lassen Sie die Standardeinstellung „Benutzertoken“ für Testzwecke unverändert.

   > [!NOTE]
   > Für Produktionsaufträge empfiehlt sich das [Verwenden von verwalteter Identität zum Authentifizieren Ihres Stream Analytics-Auftrags bei Power BI](../../stream-analytics/powerbi-output-managed-identity.md).

   - **Datasetname**: Geben Sie einen Datasetnamen ein.
   - **Tabellenname**: Geben Sie einen Tabellennamen ein.
   - **Autorisieren**: Wählen Sie „Autorisieren“ aus, und befolgen Sie die Anweisungen, um sich bei Ihrem Power BI-Konto anzumelden (das Token ist 90 Tage lang gültig).

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/power-bi/add-pbi-output.png" alt-text="Screenshot: Hinzufügen von Power BI-Ausgaben zum Stream Analytics-Auftrag.":::

4. Wählen Sie **Speichern** aus.

   > [!NOTE]
   > Weitere Informationen zur Verwendung von Power BI als Ausgabe für einen Stream Analytics-Auftrag finden Sie [hier](../../stream-analytics/power-bi-output.md). Erfahren Sie mehr über das [Erneuern der Authentifizierung](../../stream-analytics/stream-analytics-power-bi-dashboard.md#renew-authorization) für Ihr Power BI Konto.

### <a name="configure-the-query-for-stream-analytics-job"></a>Konfigurieren der Abfrage für Stream Analytics-Aufträge

1. Wählen Sie unter Auftragstopologie **Abfrage** aus.
2. Nehmen Sie die folgenden Änderungen an Ihrer Abfrage vor:

   ```SQL
   SELECT
       CAST(InferenceRecords.ArrayValue.event.properties.total AS bigint) as EventTotal,
       CAST(i.EventProcessedUtcTime AS datetime) as EventProcessedUtcTime
   INTO [YourOutputAlias]
   FROM [YourInputAlias] i
   CROSS APPLY GetArrayElements(inferences) AS InferenceRecords
   WHERE InferenceRecords.ArrayValue.subType = 'lineCrossing'
   ```

   > [!NOTE]
   >
   > - In der Abfrage oben ist das _i_ in der FROM-Klausel syntaktisch erforderlich, um den Wert von EventProcessedUtcTime abzurufen, der nicht im _Rückschlussarray_ geschachtelt ist.
   >   Die Abfrage oben ist dafür angepasst, KI-Rückschlüsse für das [Tutorial zur Linienüberquerung](use-line-crossing.md) zu liefern.
   > - Wenn Sie eine andere Pipeline ausführen, achten Sie darauf, die Abfrage entsprechend dem KI-Rückschlussschema der Pipeline anzupassen. Erfahren Sie mehr über das [Analysieren von JSON im Stream Analytics-Auftrag](../../stream-analytics/stream-analytics-parsing-json.md).

3. Ersetzen Sie [YourOutputAlias] durch den Ausgabealias, der im Schritt verwendet wird, um dem Stream Analytics-Auftrag eine Ausgabe hinzuzufügen, z. B. „powerbioutput“. Beachten Sie die richtige Reihenfolge von Ausgabe- und Eingabealiasen.
4. Ersetzen Sie [YourInputAlias] durch den Eingabealias, der im Schritt verwendet wird, um dem Stream Analytics-Auftrag eine Eingabe hinzuzufügen, z. B „iothubinput“.
5. Ihre Abfrage sollte in etwa dem folgenden Screenshot entsprechen. Klicken Sie auf **Testabfrage**, um die Testergebnisse als Tabelle aus EventTotal und den entsprechenden EventProcessedUtcTime-Werten zu überprüfen.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/power-bi/asa-query.png" alt-text="Screenshot: Testen und Speichern einer Abfrage im Stream Analytics Auftrag.":::

6. Wählen Sie **Abfrage speichern** aus.

### <a name="run-the-stream-analytics-job"></a>Ausführen des Stream Analytics-Auftrags

Wählen Sie im Stream Analytics-Auftrag „Overview“ und dann **Starten** > **Jetzt** > **Starten** aus. Sobald der Auftrag erfolgreich gestartet wurde, ändert sich der Status des Auftrags von „Erstellt“ in **Wird ausgeführt**.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/power-bi/start-asa.png" alt-text="Screenshot: Starten und Ausführen eines Stream Analytics-Auftrags.":::

> [!TIP]
> IoT Hub ermöglicht die Datenaufbewahrung in den integrierten Event Hubs, standardmäßig für einen und maximal für bis zu sieben Tage. Sie können die Aufbewahrungsdauer bei der Erstellung Ihres IoT Hubs festlegen. Die Datenaufbewahrungsdauer in IoT Hub hängt von Ihrer IoT Hub-Ebene und dem Typ der Einheit ab. Weitere Informationen finden Sie [hier](../../iot-hub/iot-hub-devguide-messages-read-builtin.md). Verwenden Sie für eine längere Datenaufbewahrung [Azure Storage als Ausgabe](../../stream-analytics/blob-storage-azure-data-lake-gen2-output.md), und stellen Sie dann eine Verbindung von Power BI mit den Dateien im Speicherkonto her.

## <a name="run-a-sample-pipeline"></a>Ausführen einer Beispielpipeline

Wenn sich der in den vorhergehenden Schritten erstellte Stream Analytics-Auftrag im Zustand **Wird ausgeführt** befindet, navigieren Sie zum Abschnitt [Ausführen des Beispielprogramms](use-line-crossing.md#run-the-sample-program) des Tutorials zur Linienüberquerung, und aktivieren Sie die Livepipeline. Die Livepipeline beginnt mit dem Senden von KI-Rückschlussergebnissen an IoT Hub, die dann vom Stream Analytics-Auftrag aufgenommen werden.

## <a name="create-a-power-bi-dashboard-to-visualize-ai-events"></a>Erstellen eines Power BI-Dashboards zum Visualisieren von KI-Ereignissen

In Power BI können Sie Streamingdaten auf zwei Arten visualisieren:

1. Berichte aus einer Tabelle erstellen, die für das Streaming von Datasets geschaffen wurde; die Berichte werden dann an das Dashboard angeheftet
2. Eine Dashboardkachel mit benutzerdefiniertem Dataset

   > [!NOTE]
   > In diesem Artikel verwenden wir die erste Methode, um Berichte zu erstellen, die wir dann an das Dashboard anheften. Diese Methode behält Daten im Visual für einen längeren Zeitraum bei und aggregiert automatisch auf der Grundlage der eingehenden Daten. Weitere Informationen zur zweiten Methode finden Sie unter [Einrichten eines benutzerdefinierten Streaming-Datasets in Power BI](/power-bi/connect-data/service-real-time-streaming#set-up-your-real-time-streaming-dataset-in-power-bi).

### <a name="create-and-publish-a-power-bi-report"></a>Erstellen und Veröffentlichen eines Power BI-Berichts

In den folgenden Schritten erfahren Sie, wie Sie einen Bericht mithilfe des Power BI-Diensts erstellen und veröffentlichen.

1. Vergewissern Sie sich, dass die Beispielpipeline auf Ihrem Gerät aktiviert ist (im vorherigen Schritt zum Ausführen einer Pipeline gestartet).
2. Melden Sie sich bei Ihrem [Power BI-Konto](https://powerbi.microsoft.com/) an, und wählen Sie im Menü oben die Option **Power BI-Dienst** aus.
3. Wählen Sie den von Ihnen verwendeten Arbeitsbereich im Seitenmenü aus.
4. Unterhalb der Registerkarte **Alle** oder der Registerkarte **Datasets + Dataflows** sollte das Dataset angezeigt werden, das Sie im Schritt zum _Hinzufügen einer Ausgabe zum Stream Analytics-Auftrag_ angegeben haben.
5. Zeigen Sie mit der Maus auf den Namen des Datasets, das Sie beim Erstellen der Stream Analytics-Ausgabe angegeben haben, wählen Sie das Menü **Weitere Optionen** (die drei Punkte rechts neben dem Datasetnamen) und dann **Bericht erstellen** aus.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/power-bi/create-report.png" alt-text="Screenshot: Erstellen eines Berichts in Power BI.":::

6. Erstellen Sie ein Liniendiagramm, um Linienüberquerungsereignisse in Echtzeit darzustellen.

   - Wählen Sie auf der Seite **Visualisierungen** der Seite zur Berichterstellung das Symbol **Liniendiagramm** aus, um ein Liniendiagramm hinzuzufügen.
   - Erweitern Sie im Bereich **Felder** die Tabelle, die Sie beim Erstellen der Ausgabe für den Stream Analytics-Auftrag angegeben haben.
   - Ziehen Sie **EventProcessedUtcTime** im Bereich **Visualisierungen** auf **Axis**.
   - Ziehen Sie **EventTotal** auf **Werte**.
   - Ein Liniendiagramm wird erstellt. Die x-Achse zeigt Datum und Uhrzeit in der Zeitzone UTC. Die y-Achse zeigt automatisch zusammengefasste Werte für **Summe** von EventTotal aus der Livepipeline an. Ändern Sie die Werte in **Maximum** von EventTotal, um den aktuellsten Wert von EventTotal zu erhalten. Sie können die Aggregationsfunktion ändern, um Durchschnitt, Anzahl (eindeutig) usw. anzuzeigen.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/power-bi/powerbi-report.png" alt-text="Screenshot: Linienüberquerungsbericht in Power BI.":::

7. Speichern Sie den Bericht, indem Sie oben rechts auf **Speichern** klicken.

### <a name="pin-visual-to-a-dashboard"></a>Anheften eines Visuals an ein Dashboard

Klicken Sie oben rechts auf **An ein Dashboard anheften**, und wählen Sie aus, wo Sie dieses Visual anheften möchten – an ein vorhandenes oder ein neues Dashboard, und befolgen Sie dann die entsprechenden Anweisungen.

Optional können Sie auch [ein Playerwidget einbetten](embed-player-in-power-bi.md), um die Videowiedergabe neben dem Bericht darzustellen.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/power-bi/pinned-dashboard.png" alt-text="Power BI Dashboard mit angeheftetem Bericht und in einer Kachel hinzugefügtem Widgetplayer.":::

> [!NOTE]
> Die Videowiedergabe und die Berichterstellung sind aufgrund der zum Generieren von Berichten verwendeten Methode nicht synchron. Informationen zu einem näher an Echtzeit orientierten Ergebnis finden Sie unter [Einrichten eines benutzerdefinierten Streaming-Datasets in Power BI](/power-bi/connect-data/service-real-time-streaming#set-up-your-real-time-streaming-dataset-in-power-bi).

## <a name="interpret-the-dashboard"></a>Interpretieren des Dashboards

Der Prozessorknoten für die Linienüberquerung erkennt, wenn ein Objekt eine Linie überquert, die in der Topologie mithilfe des Parameters „lineCoordinates“ angegeben ist. Wenn Objekte diese Koordinaten überqueren, wird ein Ereignis mit diesen Attributen ausgelöst:

- EventTotal: Die Gesamtzahl von Linienüberquerungen durch ein beliebiges Objekt in beliebiger Richtung (im Uhrzeigersinn oder gegen den Uhrzeigersinn) seit dem Anfang des Videos. Mehr dazu erfahren Sie unter [Rückschlüsse aus Linienüberquerungsereignissen](use-line-crossing.md#line-crossing-events).
- Event Processed UTC Time (UTC-Zeit der Ereignisverarbeitung): Die Uhrzeit, zu der das Ereignis durch den Stream Analytics-Auftrag verarbeitet wurde. Alternativ können Sie die Stream Analytics-Abfrage so anpassen, dass EventEnqueuedUtcTime abgerufen wird. Dies gibt den Zeitpunkt an, zu dem das Ereignis den IoT Hub für die weitere Verarbeitung erreicht hat.

Im Dashboard oben sehen Sie im Lauf der Zeit eine zunehmende Anzahl von **EventTotal**-Objekten, wenn immer mehr Objekte die virtuelle Linie überqueren. Mit dieser Visualisierung können Sie beispielsweise schnell erkennen, dass Fahrzeuge vermehrt zwischen 15:52:00 und 15:53:30 Uhr auf der Schnellstraße vorbeifahren. Sie können diese Erkenntnisse dann verwenden, um Ihre Analyse der Gründe für Verkehrsstaus auf dem Freeway zu bestimmten Zeiten einzugrenzen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie weitere Schnellstartanleitungen oder Tutorials ausprobieren möchten, behalten Sie die von Ihnen erstellten Ressourcen. Wechseln Sie andernfalls zum Azure-Portal, wechseln Sie zu Ihren Ressourcengruppen, wählen Sie die Ressourcengruppe aus, in der Sie die Voraussetzungen für diesen Artikel ausgeführt und den Stream Analytics-Auftrag erstellt haben, und wählen Sie dann **Ressourcengruppe löschen** aus.

Sie haben ein Dataset **LineCrossingDataset** in Power BI erstellt. Um es zu entfernen, melden Sie sich bei Ihrem [Power BI](https://powerbi.microsoft.com/)-Konto an. Wählen im Menü auf der linken Seite unter **Arbeitsbereiche** den von Ihnen verwendeten Arbeitsbereich aus. Zeigen Sie in der Liste der Datasets auf der Registerkarte **Datasets + Dataflows** mit der Maus auf das Dataset. Klicken Sie auf die drei Punkte rechts neben dem Datasetnamen, um das Menü **Weitere Optionen** zu öffnen. Klicken Sie dann auf **Löschen**, und folgen Sie den Anweisungen. Wenn Sie das Dataset entfernen, wird auch der Bericht entfernt.

## <a name="next-steps"></a>Nächste Schritte

- Kombinieren der Videowiedergabe mit Telemetriedaten durch [Einbetten eines Playerwidgets in Power BI](embed-player-in-power-bi.md)
- Weitere Informationen finden Sie unter [Überwachung und Protokollierung von Ereignissen](monitor-log-edge.md).
