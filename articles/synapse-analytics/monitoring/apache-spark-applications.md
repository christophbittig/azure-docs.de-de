---
title: Überwachen von Apache Spark-Anwendungen mithilfe von Synapse Studio
description: Verwenden Sie Synapse Studio, um Ihre Apache Spark-Anwendungen zu überwachen.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.custom: contperf-fy21q4
ms.openlocfilehash: f83e84e3aa747c41d0e45a5650c704c796c62447
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/25/2021
ms.locfileid: "112968265"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-applications"></a>Verwenden von Synapse Studio zum Überwachen Ihrer Apache Spark-Anwendungen

Mit Azure Synapse Analytics können Sie mithilfe von Apache Spark Notebooks, Aufträge und andere Arten von Anwendungen in ihren Apache Spark-Pools in Ihrem Arbeitsbereich ausführen.

In diesem Artikel wird erläutert, wie Sie Ihre Apache Spark-Anwendungen überwachen, was es Ihnen gestattet, den aktuellen Status, die aktuellen Probleme und den Fortschritt im Auge zu behalten.

## <a name="view-apache-spark-applications"></a>Anzeigen von Apache Spark-Anwendungen 
Sie können alle Apache Spark-Anwendungen unter **Überwachen** -> **Apache Spark-Anwendungen** anzeigen.
   ![Apache Spark-Anwendungen](./media/how-to-monitor-spark-applications/apache-spark-applications.png)

## <a name="view-completed-apache-spark-application"></a>Anzeigen abgeschlossener Apache Spark-Anwendungen

Öffnen Sie **Überwachen**, und wählen Sie dann **Apache Spark-Anwendungen** aus. Um die Details zu abgeschlossenen Apache Spark-Anwendungen anzuzeigen, wählen Sie die Apache Spark-Anwendung aus, und zeigen Sie die Details an.

  ![Abgeschlossenen Auftrag auswählen](./media/how-to-monitor-spark-applications/select-completed-job.png)

1. Überprüfen Sie **Abgeschlossene Aufgaben**, **Status** und **Gesamtdauer**.

2. **Aktualisieren** Sie den Auftrag.

3. Klicken Sie auf **Anwendungen vergleichen**, um das Vergleichsfeature zu verwenden. Weitere Informationen zu diesem Feature finden Sie unter [**Vergleichen von Apache Spark-Anwendungen**](#compare-apache-spark-applications).

4. Klicken Sie auf **Spark-Verlaufsserver**, um die Seite „Verlaufsserver“ zu öffnen.

5. Überprüfen Sie die Informationen unter **Zusammenfassung**.

6. Überprüfen Sie die Diagnose auf der Registerkarte **Diagnose**.

7. Überprüfen Sie die **Protokolle**. Sie können die vollständigen **Livy**-, **Prelaunch**- und **Driver**-Protokolle einsehen, indem Sie in der Dropdownliste verschiedene Optionen auswählen. Sie können die gewünschten protokollierten Informationen auch direkt über eine Stichwortsuche abrufen. Klicken Sie auf **Protokoll herunterladen**, um die Protokollinformationen lokal herunterzuladen. Aktivieren Sie das Kontrollkästchen **Fehler und Warnungen filtern**, um die gewünschten Fehler und Warnungen herauszufiltern.

8. Sie sehen eine Übersicht Ihres Auftrags in dem generierten Auftragsdiagramm. Standardmäßig werden in dem Diagramm alle Aufträge angezeigt. Sie können diese Ansicht anhand der **Auftrags-ID** filtern.

9. Standardmäßig ist die Anzeige **Status** ausgewählt. Sie können den Datenfluss überprüfen, indem Sie **Progress (Fortschritt)** /**Read (Gelesen)** /**Written (Geschrieben)** /**Duration (Dauer)** in der Dropdownliste **Views** (Ansicht) auswählen.

10. Klicken Sie zur Wiedergabe des Auftrags auf die Schaltfläche **Wiedergabe**. Sie können jederzeit auf die Schaltfläche **Beenden** klicken, um den Vorgang anzuhalten.

11. Vergrößern oder verkleinern Sie den Auftragsgraphen über die Scrollleiste. Sie können auch **Zoom anpassen** auswählen, um ihn an den Bildschirm anzupassen.

    [![Abgeschlossenen Auftrag anzeigen](./media/how-to-monitor-spark-applications/view-completed-job.png)](./media/how-to-monitor-spark-applications/view-completed-job.png#lightbox)


12. Der Auftragsdiagrammknoten zeigt die folgenden Informationen zu den einzelnen Phasen an:

    - Auftrags-ID
    - Nummer der Aufgabe
    - Dauer
    - Zeilenanzahl
    - Gelesene Daten: die Summe der Eingabegröße und der Shuffle-Lesegröße
    - Geschriebene Daten: die Summe der Ausgabegröße und Größe der Shuffleschreibvorgänge
    - Phasennummer

       ![Knoten des Auftragsgraphenknoten](./media/how-to-monitor-spark-applications/job-graph-node.png)

13. Wenn Sie mit der Maus auf einen Auftrag zeigen, werden die Auftragsdetails in der QuickInfo angezeigt:
    
    - Symbol des Auftragsstatus: Wenn der Auftragsstatus „Erfolgreich“ ist, wird ein grünes „√“ angezeigt. Wenn der Auftrag ein Problem erkennt, wird ein gelbes „!“ angezeigt.
    - Auftrags-ID.
    - Allgemeiner Teil:
      - Fortschritt
      - Dauer
      - Gesamtanzahl der Aufgaben
    - Datenteil:
      - Gesamtanzahl der Zeilen
      - Lesegröße
      - Geschriebene Größe
    - Teil „Schiefe“:
      - Datenschiefe
      - Zeitabweichung
    - Phasennummer

      ![Zeigen auf einen Auftrag](./media/how-to-monitor-spark-applications/hover-a-job.png)

14. Klicken Sie auf **Phasennummer**, um alle im Auftrag enthaltenen Phasen aufzuklappen. Klicken Sie neben der Auftrags-ID auf **Zuklappen**, um alle Phasen des Auftrags zuzuklappen.

15. Klicken Sie im Graphen einer Phase auf **Details anzeigen**, um die Details für die Phase anzuzeigen.

    [![Aufklappen aller Phasen](./media/how-to-monitor-spark-applications/expand-all-the-stages.png)](./media/how-to-monitor-spark-applications/expand-all-the-stages.png#lightbox)
    
## <a name="monitor-running-apache-spark-application"></a>Überwachen aktiver Apache Spark-Anwendungen

Öffnen Sie **Überwachen**, und wählen Sie dann **Apache Spark-Anwendungen** aus. Um die Details zu den ausgeführten Apache Spark-Anwendungen anzuzeigen, wählen Sie die sendende Apache Spark-Anwendung aus, und zeigen Sie die Details an. Wenn die Apache Spark-Anwendung noch ausgeführt wird, können Sie den Fortschritt überwachen.

   ![Ausgeführten Auftrag auswählen](./media/how-to-monitor-spark-applications/select-running-job.png)

1. Überprüfen Sie **Abgeschlossene Aufgaben**, **Status** und **Gesamtdauer**.

2. **Brechen** Sie die Apache Spark-Anwendung ab.

3. **Aktualisieren** Sie den Auftrag.

4. Klicken Sie auf die Schaltfläche für die **Spark-Benutzeroberfläche**, um zur Seite „Spark-Auftrag“ zu wechseln.

5. Für **Auftragsdiagramm**, **Zusammenfassung**, **Diagnose**, **Protokolle**. Sie sehen eine Übersicht Ihres Auftrags in dem generierten Auftragsdiagramm. Weitere Informationen finden Sie in den Schritten 5-15 unter [Anzeigen abgeschlossener Apache Spark-Anwendungen](#view-completed-apache-spark-application). 

    [![Ausgeführten Auftrag anzeigen](./media/how-to-monitor-spark-applications/view-running-job.png)](./media/how-to-monitor-spark-applications/view-running-job.png#lightbox)

## <a name="view-canceled-apache-spark-application"></a>Anzeigen abgebrochener Apache Spark-Anwendungen

Öffnen Sie **Überwachen**, und wählen Sie dann **Apache Spark-Anwendungen** aus. Um die Details zu abgebrochenen Apache Spark-Anwendungen anzuzeigen, wählen Sie die Apache Spark-Anwendung aus, und zeigen Sie die Details an.

 ![Abgebrochenen Auftrag auswählen](./media/how-to-monitor-spark-applications/select-cancelled-job.png) 

1. Überprüfen Sie **Abgeschlossene Aufgaben**, **Status** und **Gesamtdauer**.

2. **Aktualisieren** Sie den Auftrag.

3. Klicken Sie auf **Anwendungen vergleichen**, um das Vergleichsfeature zu verwenden. Weitere Informationen zu diesem Feature finden Sie unter [**Vergleichen von Apache Spark-Anwendungen**](#compare-apache-spark-applications).

4. Öffnen Sie den Link zum Apache Spark-Verlaufsserver, indem Sie auf **Spark-Verlaufsserver** klicken.

5. Sehen Sie sich das Diagramm an. Sie sehen eine Übersicht Ihres Auftrags in dem generierten Auftragsdiagramm. Weitere Informationen finden Sie in den Schritten 5-15 unter [Anzeigen abgeschlossener Apache Spark-Anwendungen](#view-completed-apache-spark-application).

  [![Abgebrochenen Auftrag anzeigen](./media/how-to-monitor-spark-applications/view-cancelled-job.png)](./media/how-to-monitor-spark-applications/view-cancelled-job.png#lightbox)

## <a name="debug-failed-apache-spark-application"></a>Debuggen von Fehlern bei Apache Spark-Anwendungen

Öffnen Sie **Überwachen**, und wählen Sie dann **Apache Spark-Anwendungen** aus. Um die Details zu Apache Spark-Anwendungen mit Fehlern anzuzeigen, wählen Sie die Apache Spark-Anwendung aus, und zeigen Sie die Details an.

![Auftrag mit Fehlern auswählen](./media/how-to-monitor-spark-applications/select-failed-job.png)

1. Überprüfen Sie **Abgeschlossene Aufgaben**, **Status** und **Gesamtdauer**.

2. **Aktualisieren** Sie den Auftrag.

3. Klicken Sie auf **Anwendungen vergleichen**, um das Vergleichsfeature zu verwenden. Weitere Informationen zu diesem Feature finden Sie unter [**Vergleichen von Apache Spark-Anwendungen**](#compare-apache-spark-applications).

4. Öffnen Sie den Link zum Apache Spark-Verlaufsserver, indem Sie auf **Spark-Verlaufsserver** klicken.

5. Sehen Sie sich das Diagramm an. Sie sehen eine Übersicht Ihres Auftrags in dem generierten Auftragsdiagramm. Weitere Informationen finden Sie in den Schritten 5-15 unter [Anzeigen abgeschlossener Apache Spark-Anwendungen](#view-completed-apache-spark-application).

   [![Informationen zu Auftrag mit Fehlern](./media/how-to-monitor-spark-applications/failed-job-info.png)](./media/how-to-monitor-spark-applications/failed-job-info.png#lightbox)


## <a name="view-input-dataoutput-data-for-apache-spark-application"></a>Anzeigen von Ein-/Ausgabedaten für Apache Spark-Anwendungen

Wählen Sie eine Apache Spark-Anwendung aus, und klicken Sie auf die Registerkarte **Eingabedaten/Ausgabedaten**, um die Datumsangaben der Ein- und Ausgabe für die Apache Spark-Anwendung anzuzeigen. Die Funktion kann Sie beim Debuggen des Spark-Auftrags besser unterstützen. Die Datenquelle unterstützt außerdem drei Speichermethoden: „gen1“, „gen2“ und „blob“.
    
**Registerkarte „Eingabedaten“**
     
1. Klicken Sie auf die Schaltfläche **Eingabe kopieren**, um die Eingabedatei in die lokale Datei einzufügen.

2. Klicken Sie auf die Schaltfläche **In CSV exportieren**, um die Eingabedatei im CSV-Format zu exportieren.

3. Sie können Dateien nach Eingabeschlüsselwörtern im **Suchfeld** durchsuchen (Schlüsselwörter umfassen Dateiname, Leseformat und Pfad).

4. Sie können die Eingabedateien sortieren, indem Sie auf **Name**, **Leseformat** und **Pfad** klicken.

5. Zeigen Sie mit dem Mauszeiger auf eine Eingabedatei, damit das Symbol der Schaltfläche **Herunterladen/Pfad kopieren/Mehr** angezeigt wird.

   ![Registerkarte „Eingabe“](./media/how-to-monitor-spark-applications/input-tab.png)

6. Klicken Sie auf die Schaltfläche **Mehr**, um das Kontextmenü **Pfad kopieren/Im Explorer anzeigen/Eigenschaften** anzuzeigen.
      
    ![Eingabe „Mehr“](./media/how-to-monitor-spark-applications/input-more.png)

   * Pfad kopieren: kann den **vollständigen Pfad** und den **relativen Pfad** kopieren.
   * Im Explorer anzeigen: kann zum verknüpften Speicherkonto springen („Daten -> Verknüpft“).
   * Eigenschaften: Anzeigen der grundlegenden Eigenschaften der Datei (Dateiname/Dateipfad/Leseformat/Größe/Änderungszeipunkt).

     ![Abbildung der Eigenschaften](./media/how-to-monitor-spark-applications/properties.png)

**Registerkarte „Ausgabedaten“**

   Verfügt über dieselben Features wie die „Eingabe“.

   ![Abbildung der „Ausgabe“](./media/how-to-monitor-spark-applications/output.png)

## <a name="compare-apache-spark-applications"></a>Vergleichen von Apache Spark-Anwendungen

Es gibt zwei Möglichkeiten, Anwendungen zu vergleichen. Sie können vergleichen, indem Sie eine **Vergleichsanwendung** auswählen, oder klicken Sie auf die Schaltfläche **Im Notebook vergleichen**, um sie im Notebook anzuzeigen.

### <a name="compare-by-choosing-an-application"></a>Vergleichen durch Wählen einer Anwendung

Klicken Sie auf die Schaltfläche **Anwendungen vergleichen**, und wählen Sie eine Anwendung aus, um die Leistung zu vergleichen. Sie erkennen intuitiv den Unterschied zwischen den beiden Anwendungen.

![Anwendungen vergleichen](./media/how-to-monitor-spark-applications/compare-applications.png)

![Details: Anwendungen vergleichen](./media/how-to-monitor-spark-applications/details-compare-applications.png)

1. Zeigen Sie mit der Maus auf eine Anwendung, woraufhin das Symbol **Anwendungen vergleichen** angezeigt wird.

2. Klicken Sie auf das Symbol **Anwendungen vergleichen**, woraufhin die Seite „Anwendungen vergleichen“ angezeigt wird.

3. Klicken Sie auf die Schaltfläche **Anwendung auswählen**, um die Seite **Vergleichsanwendung auswählen** zu öffnen.

4. Bei der Auswahl der Vergleichsanwendung müssen Sie entweder die Anwendungs-URL eingeben oder eine Auswahl aus der wiederkehrenden Liste treffen. Klicken Sie dann auf die Schaltfläche **OK**. 

   ![Vergleichsanwendung auswählen](./media/how-to-monitor-spark-applications/choose-comparison-application.png)

5. Das Vergleichsergebnis wird auf der Seite „Anwendungen vergleichen“ angezeigt.

   ![Vergleichsergebnis](./media/how-to-monitor-spark-applications/comparison-result.png)

### <a name="compare-by-compare-in-notebook"></a>Vergleichen durch Vergleichen im Notebook

Klicken Sie auf der Seite **Anwendungen vergleichen** auf die Schaltfläche **Im Notebook vergleichen**, um das Notebook zu öffnen. Der Standardname der *IPYNB*-Datei ist **Wiederkehrende Anwendungsanalyse**.

![Vergleichen im Notebook](./media/how-to-monitor-spark-applications/compare-in-notebook.png)

In der Datei „Notebook: Wiederkehrende Anwendungsanalyse“ können Sie den Vergleich direkt nach dem Festlegen von Spark-Pool und Sprache ausführen.

![Wiederkehrende Anwendungsanalyse](./media/how-to-monitor-spark-applications/recurrent-application-analytics.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Überwachen von Pipelineausführungen finden Sie im Artikel [Überwachen von Pipelineausführungen mithilfe von Synapse Studio](how-to-monitor-pipeline-runs.md).
