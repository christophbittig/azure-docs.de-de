---
title: Erste Schritte mit Azure Video Analyzer-Livepipelines – Azure-Portal
description: In diesem Schnellstart werden die Schritte zum Erfassen und Aufzeichnen von Videos von einer RTSP-Kamera mithilfe von Livepipelines im Azure Video Analyzer-Dienst beschrieben.
ms.service: azure-video-analyzer
ms.topic: quickstart
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3f7c134174560d3222e0ba936378c37b931abee7
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132489925"
---
# <a name="quickstart-get-started-with-video-analyzer-live-pipelines-in-the-azure-portal"></a>Schnellstart: Erste Schritte mit Video Analyzer-Livepipelines im Azure-Portal

![Cloud-Symbol](media/env-icon/cloud.png)  
Lesen Sie alternativ die Informationen unter [Schnellstart: Erste Schritte mit Video Analyzer im Portal](../edge/get-started-detect-motion-emit-events-portal.md).

---

In diesem Schnellstart werden die Schritte zum Erfassen und Aufzeichnen von Videos von einer RTSP-Kamera (Real Time Streaming Protocol) mithilfe von Livepipelines im Azure Video Analyzer-Dienst beschrieben.
Sie erstellen im Azure-Portal ein Video Analyzer-Konto und die zugehörigen Ressourcen. Sie stellen einen RTSP-Kamerasimulator bereit, wenn Sie keinen Zugriff auf eine RTSP-Kamera haben (die über das Internet zugänglich gemacht werden kann). Anschließend stellen Sie die relevanten Video Analyzer-Ressourcen zum Aufzeichnen von Videos in Ihrem Video Analyzer-Konto bereit.

Die in diesem Dokument beschriebenen Schritte gelten für Kameras, die über das Internet zugänglich gemacht und nicht durch eine Firewall geschützt sind. Das folgende Diagramm stellt die [Livepipeline](../pipeline.md) grafisch dar, die Sie in Ihrem Video Analyzer-Konto bereitstellen.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/live-pipeline.svg" alt-text="Darstellung einer Livepipeline in der Cloud":::

## <a name="prerequisites"></a>Voraussetzungen

- Ein aktives Azure-Abonnement. Falls Sie nicht über ein Abonnement verfügen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/).

  [!INCLUDE [the video analyzer account and storage account must be in the same subscription and region](../includes/note-account-storage-same-subscription.md)]
- Entweder eine RTSP-Kamera, auf die über das Internet zugegriffen werden kann, oder eine Azure-Linux-VM (mit Administratorrechten) zum Hosten eines RTSP-Kamerasimulators

## <a name="sample-architecture---recording-video-from-a-camera-over-the-internet"></a>Beispielarchitektur: Aufzeichnen von Videos von einer Kamera über das Internet
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/public-camera-to-cloud-live-pipeline-arch.png" alt-text="Diagramm einer Beispielarchitektur eines Videofeeds von einer öffentlichen Kamera, der in die Video Analyzer-Livepipeline integriert ist, die Videos in der Cloud aufzeichnet":::

## <a name="rtsp-camera"></a>RTSP-Kamera

Sie benötigen Zugriff auf eine RTSP-fähige Kamera. Unterstützte Kameras finden Sie [hier](../quotas-limitations.md). Die Kamera sollte für die Codierung von Videos mit einer maximalen Bitrate von unter 3 MBit/s konfiguriert sein. Notieren Sie sich diese Einstellung für die maximale Bitrate. Darüber hinaus muss der RTSP-Server auf dieser Kamera über das öffentliche Internet erreichbar sein. Wenn Sie über eine solche Kamera verfügen, können Sie direkt mit dem Abschnitt „Erstellen von Azure-Ressourcen“ fortfahren. Andernfalls können Sie anhand des folgenden Abschnitts einen RTSP-Kamerasimulator bereitstellen.

## <a name="deploy-rtsp-camera-simulator"></a>Bereitstellen eines RTSP-Kamerasimulators

In diesem Abschnitt erfahren Sie, wie Sie einen RTSP-Kamerasimulator auf einer Azure-Linux-VM bereitstellen, auf der das Betriebssystem Ubuntu Server 18.04 ausgeführt wird. In diesem Simulator wird der [Live555 Media Server](http://www.live555.com/mediaServer/) verwendet.

> [!NOTE]
> Verweise auf Drittanbietersoftware dienen nur zu Informations- und Komfortzwecken. Microsoft leistet keine Unterstützung für die Drittanbietersoftware und stellt auch keine Rechte dafür bereit. Weitere Informationen finden Sie unter [Live555 Media Server](http://www.live555.com/mediaServer/).

> [!WARNING]
> Beachten Sie, dass dieser Endpunkt für den RTSP-Kamerasimulator über das Internet verfügbar gemacht wird und daher für jeden zugänglich ist, der die RTSP-URL kennt.

**Bereitstellungsschritte:**
1. Stellen Sie eine Azure-Linux-VM der Serie „Standard_D2s_v3“ mit dem Betriebssystem Ubuntu Server 18.04 bereit. Informationen zum Erstellen von VMs finden Sie [hier](../../../virtual-machines/linux/quick-create-portal.md). Sie müssen den in diesem Artikel genannten Webserver nicht installieren. Erlauben Sie auch den SSH-Port im Bereitstellungs-Assistenten, damit Sie eine SSH-Verbindung mit der VM herstellen können.
1. Aktivieren Sie eingehende Verbindungen für das RTSP-Protokoll. Öffnen Sie im Azure-Portal den Verwaltungsbereich für die Linux-VM, die Sie oben erstellt haben.

    1. Klicken Sie auf „Netzwerkbetrieb“. Daraufhin wird das Blatt mit den Regeln für eingehende Ports für die Netzwerksicherheitsgruppe (NSG) geöffnet, die für die Unterstützung eingehender SSH-Verbindungen erstellt wurde.
    1. Klicken Sie auf „Regel für eingehenden Port hinzufügen“, um eine neue Regel hinzuzufügen.
    1. Ändern Sie im daraufhin geöffneten Bereich die Zielportbereiche in 554. Wählen Sie einen Namen für die Regel aus, z. B. „RTSP“. Behalten Sie für alle anderen Werte die Standardwerte bei. Ausführlichere Informationen finden Sie [hier](../../../virtual-machines/windows/nsg-quickstart-portal.md).
1. Installieren Sie mithilfe [dieser Anweisungen](https://docs.docker.com/engine/install/ubuntu/) Docker auf der VM. Führen Sie nur die Schritte bis zur Überprüfung der Docker-Installation durch Ausführen des Images „hello-world“ aus.
1. Stellen Sie z. B. per SSH eine Verbindung mit Ihrer VM her. Erstellen Sie im Terminalfenster einen lokalen Ordner (z. B. „localmedia“), um Mediendateien zu hosten. Dieser lokale Ordner auf der VM wird zum Zuordnen zum Container auf dem RTSP-Mediaserver verwendet.
1. Kopieren Sie wie folgt eine MKV-Datei, die zum Simulieren des Kamerafeeds verwendet wird:

    ```
    cd localmedia
    wget https://avamedia.blob.core.windows.net/public/camera-1800s.mkv
    ```
1. Starten Sie wie folgt den RTSP-Server auf der VM mithilfe des vordefinierten Containerimages:

    ```    
    sudo docker run -d -p 554:554 -v ${PWD}:/live/mediaServer/media mcr.microsoft.com/ava-utilities/rtspsim-live555:1.2
    ```
1. Sobald der RTSP-Server ausgeführt wird, können Clients über eine RTSP-URL eine Verbindung mit ihm herstellen:

    - Wechseln Sie zur Seite „Übersicht“ Ihrer VM im Azure-Portal, und notieren Sie sich den Wert von „Öffentliche IP-Adresse“.
    
        - Die RTSP-URL lautet: rtsp://{Öffentliche IP-Adresse}:554/media/camera-1800s.mkv. Sie kann mit einem Desktopplayer wie VLC getestet werden.

## <a name="create-azure-resources"></a>Erstellen von Azure-Ressourcen

Im nächsten Schritt müssen die erforderlichen Azure-Ressourcen (Video Analyzer-Konto, Speicherkonto, benutzerseitig zugewiesene verwaltete Identität) erstellt werden.

### <a name="create-a-video-analyzer-account-in-the-azure-portal"></a>Erstellen eines Video Analyzer-Kontos im Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Geben Sie auf der Suchleiste im oberen Bereich **Video Analyzer** ein.
1. Wählen Sie unter **Dienste** die Option **Video Analyzer** aus.
1. Wählen Sie **Hinzufügen**.
1. Geben Sie im Abschnitt **Create Video Analyzer account** (Video Analyzer-Konto erstellen) die folgenden erforderlichen Werte ein:

   - **Abonnement**: Wählen Sie das Abonnement aus, das Sie zum Erstellen des Video Analyzer-Kontos verwenden möchten.
   - **Ressourcengruppe**: Wählen Sie eine Ressourcengruppe aus, in der Sie das Video Analyzer-Konto erstellen möchten, oder wählen Sie **Neu erstellen** aus, um eine Ressourcengruppe zu erstellen.
   - **Video Analyzer account name** (Name des Video Analyzer-Kontos): Geben Sie einen Namen für Ihr Video Analyzer-Konto ein. Der Name darf nur aus drei bis 24 Kleinbuchstaben oder Zahlen bestehen und darf keine Leerzeichen enthalten.
   - **Standort**: Wählen Sie einen Standort zum Bereitstellen Ihres Video Analyzer-Kontos aus (beispielsweise **USA, Westen 2**).
   - **Speicherkonto**: Erstellen Sie ein Speicherkonto. Es wird empfohlen, ein Speicherkonto vom Typ [Standard „Allgemein v2“](../../../storage/common/storage-account-overview.md#types-of-storage-accounts) auszuwählen.
   - **Benutzeridentität**: Erstellen Sie eine neue benutzerseitig zugewiesene verwaltete Identität, und geben Sie ihr einen Namen.
1. Wählen Sie am unteren Rand des Formulars die Option **Überprüfen und erstellen** aus.

### <a name="deploy-a-live-pipeline"></a>Bereitstellen einer Livepipeline

Nachdem das Video Analyzer-Konto erstellt wurde, können Sie mit den nächsten Schritten fortfahren und eine Livepipelinetopologie und eine Livepipeline erstellen.
1. Wechseln Sie zum Video Analyzer-Konto, suchen Sie links unten das Menüelement **Live**, und wählen Sie es aus. 
1. Wählen Sie im Bereich „Topologien“ oben die Option **Topologie erstellen** aus, um eine Livetopologie zu erstellen. Führen Sie die Schritte im Portal-Assistenten aus, um eine Livepipelinetopologie zu erstellen.

    - Der **Assistent zum Erstellen einer Pipelinetopologie** wird im Portal angezeigt.
    - Wählen Sie **Beispieltopologien ausprobieren**, anschließend die Topologie **Liveaufnahme, Aufzeichnung und Streamen von der RTSP-Kamera** und dann im Dialogfeld **Beispieltopologie laden** die Option „Fortfahren“ aus.
    - Der Assistent zum Erstellen der Livepipelinetopologie wird angezeigt. Darin ist der RTSP-Quellknoten zu sehen, der mit einem Knoten der Videosenke verbunden ist.
    - Füllen Sie die erforderlichen Felder aus, um die Topologie zu erstellen: 
    
        - **Topologiename:** Geben Sie den Namen für die Topologie ein. 
        - **Beschreibung** (optional): Geben Sie eine Kurzbeschreibung der Topologie an. 
        - **Typ** (vorausgefüllt mit „Live“)
        - Wählen Sie den Knoten **RTSP-Quelle** aus, und legen Sie dann „TCP“ für den Eigenschaftswert **Transport** fest.
        - Wählen Sie **Speichern** aus, um für die restlichen Eigenschaften die Standardkonfiguration anzuwenden.
1. Der nächste Schritt besteht im Erstellen einer Livepipeline mithilfe der Topologie, die im vorherigen Schritt erstellt wurde. 

    - Wählen **Pipelines**, anschließend **Pipeline erstellen** und dann die im vorherigen Schritt erstellte Livepipelinetopologie aus, um eine Pipeline zu erstellen. Klicken Sie nach dem Auswählen der Topologie auf **Erstellen**.
    - Der **Assistent zum Erstellen einer Livepipeline** wird im Portal angezeigt. Füllen Sie die erforderlichen Felder aus: 
    
        - **Livepipelinename:** Verwenden Sie einen eindeutigen Namen (zulässig sind alphanumerische Zeichen und Bindestriche).
        - **Bitrate:** Geben Sie die maximale Kapazität in KBit/s an, die für die Livepipeline reserviert ist. Der zulässige Bereich liegt zwischen 500 KBit/s und 3.000 KBit/s. Verwenden Sie den Standardwert 1.000 für die Datei „camera-1800s.mkv“ des RTSP-Kamerasimulators (dieser Wert sollte mit der verwendeten Beispielvideodatei übereinstimmen). 
        - **rtspUserNameParameter**, **rtspPasswordParameter**: Legen Sie Platzhalterwerte für diese Felder fest, wenn Sie den RTSP-Kamerasimulator verwenden. Geben Sie andernfalls die Anmeldeinformationen für die Authentifizierung beim tatsächlichen RTSP-Kamerastream ein.
        - **rtspUrlParameter:** Verwenden Sie `rtsp://<VMpublicIP>:554/media/camera-1800s.mkv` (für den RTSP-Kamerasimulator) oder die tatsächliche URL des RTSP-Kamerastreams.
        - **videoNameParameter:** Geben Sie einen eindeutigen Namen für die Videozielressource ein, die aufgezeichnet werden soll. Hinweis: Verwenden Sie für jede Kamera (oder MKV-Datei) eine eindeutige Videoressource.
    - Wählen Sie **Erstellen** aus. Daraufhin wird im Pipelineraster im Portal eine Pipeline erstellt.
    - Wählen Sie die erstellte Livepipeline im Raster aus, und wählen Sie rechts neben dem Bereich die Option **Aktivieren** aus, um die Livepipeline zu aktivieren. Dadurch wird Ihre Livepipeline gestartet und mit der Aufzeichnung des Videos begonnen.
1. Nun können Sie die Videoressource im Portal unter dem Video Analyzer-Konto im Bereich **Videos** anzeigen. Der Status lautet **Aufzeichnung**, da die Pipeline aktiv ist und den Livevideostream aufzeichnet.
1. Warten Sie einige Sekunden, und wählen Sie dann das Video aus. Sie können den [Stream mit niedriger Latenz anzeigen](../playback-recordings-how-to.md).

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/camera-1800s-mkv.png" alt-text="Abbildung des von der Livepipeline in der Cloud aufgezeichneten Videos":::

    > [!NOTE]
    > Wenn Sie einen RTSP-Kamerasimulator verwenden, ist es nicht möglich, die End-to-End-Latenz genau zu bestimmen. Außerdem wird der RTSP-Kamerasimulator beendet, wenn er das Ende der MKV-Datei erreicht. Die Livepipeline versucht nach einer Weile, die Verbindung wiederherzustellen, und der Simulator startet den Stream am Anfang der Datei neu. Wenn Sie diese Livepipeline viele Stunden lang laufen lassen, enthält die Videoaufzeichnung Lücken an den Stellen, an denen der Simulator beendet und neu gestartet wurde.
1. Bei Bedarf können Sie im Aktivitätsprotokoll Ihre Bereitstellungsvorgänge schnell überprüfen. Informationen zur Überwachung und zu Ereignisprotokollen finden Sie [hier](./monitor-log-cloud.md).
1. Um die Pipelineaufzeichnung zu deaktivieren, wechseln Sie zu Ihrem Video Analyzer-Konto. Wählen Sie im linken Bereich **Live**-> **Pipelines** und anschließend die zu deaktivierende Pipeline aus. Wählen Sie dann Im Pipelineraster **Deaktivieren** aus, um die Aufzeichnung zu beenden. 
1. Sie können auch die Pipeline und die Topologie löschen, wenn sie nicht benötigt werden.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie weitere Schnellstartanleitungen oder Tutorials ausprobieren möchten, behalten Sie die von Ihnen erstellten Ressourcen. Navigieren Sie andernfalls zum Azure-Portal, dort zu Ihren Ressourcengruppen, wählen Sie die Ressourcengruppe aus, unter der Sie diesen Schnellstart ausgeführt haben, und löschen Sie alle Ressourcen.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Verwalten der [Aufbewahrungsrichtlinie](../manage-retention-policy.md) für Videos
- [Hier](https://github.com/Azure/video-analyzer/tree/main/media) können Sie verschiedene MKV-Beispieldateien für den Mediensimulator ausprobieren. Die Bitrate der Beispieldatei sollte dem Pipelinesetup entsprechen.
- Weitere Informationen zum [Überwachen und Protokollieren von Ereignissen](./monitor-log-cloud.md)
