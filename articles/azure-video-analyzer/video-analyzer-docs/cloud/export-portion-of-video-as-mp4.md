---
title: Exportieren eines Teils eines von Azure Video Analyzer aufgezeichneten Videos in eine MP4-Datei
description: In diesem Tutorial erfahren Sie, wie Sie einen Teil eines Video Analyzer-Videos als MP4-Datei exportieren, die als Video Analyzer-Video gespeichert wird und außerhalb der Umgebung des Video Analyzer-Kontos heruntergeladen und verwendet werden kann.
ms.topic: tutorial
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7a947ecad80a1ec6a22458acc1b4b1cec636ad32
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132332210"
---
# <a name="tutorial-export-portion-of-recorded-video-as-an-mp4-file"></a>Tutorial: Exportieren eines Teils eines aufgezeichneten Videos als MP4-Datei

[!INCLUDE [header](includes/cloud-env.md)]

In diesem Tutorial erfahren Sie, wie Sie einen Teil des Videos exportieren, das im Azure Video Analyzer-Konto aufgezeichnet wurde.  Dieser exportierte Teil des Videos wird als MP4-Datei gespeichert, die außerhalb des Video Analyzer-Kontos heruntergeladen und verwendet werden kann.

## <a name="suggested-pre-reading"></a>Empfohlene Lektüre zur Vorbereitung  

Lesen Sie diese Artikel, bevor Sie beginnen:

* [Azure Video Analyzer-Übersicht](../overview.md)
* [Azure Video Analyzer-Begriffe](../terminology.md)
* [Video Analyzer-Pipelinekonzepte](../pipeline.md)
* [Ereignisbasierte Videoaufzeichnung](../event-based-video-recording-concept.md)

## <a name="prerequisites"></a>Voraussetzungen

Dies sind die Voraussetzungen für dieses Tutorial:

* Ein Azure-Konto das ein aktives Abonnement beinhaltet. [Erstellen Sie ein kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), falls Sie noch keines besitzen.
* [Video Analyzer-Konto](../create-video-analyzer-account.md)
* Sie haben den [Schnellstart: Erkennen von Bewegung, Aufzeichnen von Video in Video Analyzer](../edge/detect-motion-record-video-clips-cloud.md) oder eine beliebige Video Analyzer-Pipeline abgeschlossen, bei der die Aufzeichnung in einer Videosenke erfolgt.

## <a name="overview"></a>Übersicht

Video Analyzer kann Videos aus einer RTSP-Quelle aufzeichnen. Im Video Analyzer-Konto werden diese Videos in einem segmentierten Archiv aufgezeichnet.  Das segmentierte Archivformat ermöglicht eine ungebundene Dauer der Videoaufzeichnung. In einigen Fällen ist es jedoch erforderlich, einen Teil des Videos als MP4-Datei zu speichern, damit er außerhalb der Video Analyzer-Umgebung einzeln archiviert, heruntergeladen oder wiedergegeben werden kann.

In diesem Tutorial lernen Sie Folgendes:

* Informationen zu Batchpipelinetopologien und Batchpipelineaufträgen
* Erstellen einer Batchtopologie
* Erstellen eines Batchpipelineauftrags aus einem Video Analyzer-Videoarchiv, der eine MP4-Datei mit einem angegebenen Zeitwert (bis zu 24 Stunden) erstellt

## <a name="pipeline-topology-of-batch-kind"></a>Pipelinetopologie vom Typ **Batch**

Mit einer Pipelinetopologie vom Typ „Batch“ können Sie basierend auf Ihren benutzerdefinierten Anforderungen beschreiben, wie aufgezeichnete Videos über drei miteinander verbundene Knoten für den Export verarbeitet werden sollen.  Eine Pipelinetopologie vom Typ „Batch“ stellt die Basis für Pipelineaufträge dar.  Ein Pipelineauftrag ist die einzelne Instanz einer Pipelinetopologie vom Typ „Batch“.  Der Pipelineauftrag importiert das aufgezeichnete Video Analyzer-Video und speichert es als herunterladbare MP4-Datei im Speicherkonto von Video Analyzer.  Die Pipelinetopologie vom Typ „Batch“ verwendet einen [Videoquellenknoten](../pipeline.md#video-source), der eine Verbindung mit einem [Encoderprozessorknoten](../pipeline.md#encoder-processor) und dann mit einem [Knoten der Videosenke](../pipeline.md#video-sink) herstellt.

> [!NOTE]
> Weitere Informationen zu Quellen, Prozessoren und Senken finden Sie unter [Quellen, Prozessoren und Senken](../pipeline.md#sources-processors-and-sinks). Weitere Informationen zu Pipelineaufträgen finden Sie unter [Pipelineaufträge](../pipeline.md#batch-pipeline).

## <a name="create-a-pipeline-job-from-videos"></a>Erstellen eines Pipelineauftrags (aus Videos)

1. Navigieren Sie im Azure-Portal zu Ihrem Video Analyzer-Konto.
1. Wählen Sie unter **Videos** den Abschnitt `Video Analyzer` und dann den Videostream aus, aus dem ein Teil des Videos exportiert werden soll.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/export-portion-of-video-as-mp4/video-analyzer-video.png" alt-text="Abbildung des Menüabschnitts „Video Analyzer“ von Azure Video Analyzer mit hervorgehobener Auswahl von „Videos“":::
1. Klicken Sie oben auf dem Blatt des Videowidgetplayers auf **Auftrag erstellen**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/export-portion-of-video-as-mp4/create-job.png" alt-text="Abbildung des Videowidgetblatts von Azure Video Analyzer mit hervorgehobener Auswahl von „Auftrag erstellen“":::
1. Wählen Sie auf dem Flyoutblatt `Create Job` Folgendes aus:

   1. Wählen Sie für die `Batch topology` die Option **Create from sample** (aus Beispiel erstellen) aus.
   1. Wählen Sie in der Dropdownliste `Batch topology name` die Beispieltopologie **Videoexport** aus.
   1. Geben Sie in `Batch topology name` einen Namen ein, unter dem die Topologie gespeichert werden soll.

        > [!NOTE]
        > Die Beispieltopologie wird unter dem oben eingegebenen Namen gespeichert.  Sie kann beim nächsten Export eines Videos wiederverwendet werden.
1. Geben Sie im Abschnitt `Name your job` einen Auftragsnamen in das Feld **Auftragsname** und eine Beschreibung für den Auftrag in das Feld **Beschreibung** ein (die Beschreibung ist optional).
1. Gehen Sie im `Define parameters`-Abschnitt folgendermaßen vor:
    1. Geben Sie den Namen des aufgezeichneten Video Analyzer-Videos in das Feld `sourceVideoName` ein.
    1. Wählen Sie im Feld `videoSourceTimeSequenceParameter` das Start- und Enddatum aus, indem Sie auf das Kalendersymbol klicken und die Datumsangaben für jeden Wert auswählen.  Geben Sie in die Zeitfelder die Start- und Endzeiten für den Pipelineauftrag ein, mit dem der Videoclip erstellt werden soll.
        > [!NOTE]
        > Der Zeitwert für ein bestimmtes aufgezeichnetes Video wird rechts oben im Videowidgetplayer angezeigt.  Dieser Zeitwert wird in der folgenden Abbildung mit einem roten Rahmen angezeigt.  Das Kalendersymbol wird in der folgenden Abbildung mit einem grünen Rahmen angezeigt.

    1. Geben Sie einen Namen für die exportierte MP4-Datei in das Feld `exportedVideoName` ein.
  
      > [!div class="mx-imgBorder"]
      > :::image type="content" source="./media/export-portion-of-video-as-mp4/video-widget-job-creation.png" alt-text="Abbildung des Videoplayerwidgets und des Flyoutblatts für Pipelineaufträge von Azure Video Analyzer mit Hervorhebungen für den Videozeitstempel (roter Rahmen) und das Kalendersymbol (grüner Rahmen)":::
1. Klicken Sie unten auf dem Flyoutblatt `Create a job` auf **Erstellen**.  
    
    Navigieren Sie zum Überwachen des Pipelineauftrags zur `Batch Jobs tab`.
1. Wählen Sie im Abschnitt `Video Analyzer` die Option **Batch** aus.
1. Klicken Sie oben auf dem Blatt „Batch“ auf die Registerkarte `Jobs`.
  
    Der Batchauftrag wechselt in den Verarbeitungszustand. Nach dem erfolgreichem Abschluss ändert sich der Status in `Completed`.  So zeigen Sie die zugeordnete MP4-Videodatei an
1. Klicken Sie im Abschnitt `Video Analyzer` auf **Videos**.
1. Klicken Sie auf den Videonamen, der dem zuvor in Schritt 5 verwendeten Batchauftragsnamen entspricht.  

Der Videowidgetplayer sollte mit der Wiedergabe der MP4-Datei beginnen.  Klicken Sie oben auf dem Blatt auf `Download video`, um die MP4-Datei herunterzuladen.  Dadurch wird die MP4-Datei auf einer neuen Browserregisterkarte geöffnet. Klicken Sie mit der rechten Maustaste auf das Video, und klicken Sie dann auf **Speichern unter**.

## <a name="cancel-a-pipeline-job"></a>Abbrechen eines Pipelineauftrags

Nachdem ein Pipelineauftrag in den Verarbeitungszustand gewechselt ist, kann er abgebrochen werden.  So brechen Sie einen Pipelineauftrag ab

1. Navigieren Sie zu Ihrem Video Analyzer-Konto, und wählen Sie im Abschnitt **Video Analyzer** die Option **Batch** aus.
1. Wählen Sie oben auf dem Blatt „Batch“ die Registerkarte **Aufträge** aus.
1. Auf der Registerkarte „Aufträge“ finden Sie eine Liste der Aufträge in den unterschiedlichen Status. Suchen Sie den Auftrag, den Sie im Verarbeitungszustand abbrechen möchten, wählen Sie rechts auf der Registerkarte „Aufträge“ für die Batch-Pipeline die Option **Abbrechen** aus, und klicken Sie dann auf **Ja**.

    > [!NOTE]
    > Ein fehlerhafter Pipelineauftrag kann nicht abgebrochen werden.

## <a name="delete-a-pipeline-job"></a>Löschen eines Pipelineauftrags

Wenn ein Pipelineauftrag den Status „Abgeschlossen“ oder „Fehlerhaft“ hat, kann er gelöscht werden.  So löschen Sie einen Pipelineauftrag

1. Navigieren Sie zu Ihrem Video Analyzer-Konto, und wählen Sie im Abschnitt **Video Analyzer** die Option **Batch** aus.
1. Wählen Sie oben auf dem Blatt „Batch“ die Registerkarte **Aufträge** aus.
1. Auf der Registerkarte „Aufträge“ finden Sie eine Liste der Aufträge in den unterschiedlichen Status.  Suchen Sie den Auftrag, den Sie löschen möchten (mit dem Status „Abgebrochen“, „Abgeschlossen“ oder „Fehlerhaft“), wählen Sie auf der rechten Seite der Pipelineaufträge die Option **Löschen** aus, und klicken Sie dann auf **Löschen**.

## <a name="delete-a-pipeline-topology-of-batch-kind"></a>Löschen einer Pipelinetopologie vom Typ „Batch“

Um eine Pipelinetopologie vom Typ „Batch“ zu löschen, müssen alle Pipelineaufträge gelöscht werden, die der Pipelinetopologie zugeordnet sind.  So löschen Sie eine Pipelinetopologie vom Typ „Batch“

1. Navigieren Sie zu Ihrem Video Analyzer-Konto.
2. Klicken Sie im Abschnitt `Video Analyzer` auf die Option **Batch**.
3. Suchen Sie auf der Registerkarte „Topologien“ die zu löschende Pipelinetopologie vom Typ „Batch“, und klicken Sie auf der rechten Seite der Pipelinetopologie vom Typ „Batch“ auf **...** .
4. Klicken Sie auf **Topologie löschen**.

    > [!NOTE]
    > Sie müssen zunächst sämtliche Pipelineaufträge aus einer Pipelinetopologie vom Typ „Batch“ löschen, bevor diese gelöscht werden kann.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie weitere Schnellstartanleitungen oder Tutorials ausprobieren möchten, behalten Sie die von Ihnen erstellten Ressourcen. Navigieren Sie andernfalls zum Azure-Portal, dort zu Ihren Ressourcengruppen, wählen Sie die Ressourcengruppe aus, unter der Sie diese Schnellstartanleitung ausgeführt haben, und löschen Sie alle Ressourcen.

## <a name="next-steps"></a>Nächste Schritte

* [Verbinden Sie Kameras direkt mit der Cloud](./connect-cameras-to-cloud.md), um Videos mithilfe von [Cloudpipelines](../pipeline.md) zu erfassen und aufzuzeichnen.
* [Verbinden Sie Kameras mit dem Video Analyzer-Dienst über das Video Analyzer-Edgemodul, das als transparentes Gateway für Videopakete über das RTSP-Protokoll fungiert.](./use-remote-device-adapter.md)
