---
title: Problembehandlung für den Azure Video Analyzer-Dienst
description: Dieser Artikel beschreibt Schritte zur Problembehandlung für den Azure Video Analyzer-Dienst.
ms.topic: troubleshooting
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3760b17d71dc3526d9a16283084095174d7bb724
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132398400"
---
# <a name="troubleshoot-azure-video-analyzer-service"></a>Problembehandlung für den Azure Video Analyzer-Dienst

![Cloud-Symbol](media/env-icon/cloud.png)  
Alternativ können Sie sich [die Problembehandlung im Edgebereich](../edge/troubleshoot.md)ansehen.

---

In diesem Artikel werden Schritte zur Problembehandlung für häufige Fehlerszenarien beschrieben, die bei der Verwendung des Diensts vorkommen können.

## <a name="enable-diagnostics"></a>Aktivieren der Diagnosefunktion

[Überwachung und Protokollierung](./monitor-log-cloud.md) sollten dabei helfen, die Taxonomie von Video Analyzer-Dienstereignissen und das Erstellen von Protokollen zu verstehen, die bei Debugproblemen eine Hilfe darstellen.
- Navigieren Sie im Azure-Portal zum Abschnitt **Überwachung** Ihres Video Analyzer-Kontos, und wählen Sie **Diagnoseeinstellungen** aus. 
- Klicken Sie auf **Diagnoseeinstellung hinzufügen**, um die Protokolle der gewünschten Ereignistypen zu aktivieren: `Diagnostics`, `Audit`, `Operational`. Ausführlichere Informationen finden Sie [hier](./monitor-log-cloud.md).


## <a name="view-diagnostics"></a>Diagnose anzeigen

Nachdem Sie die Diagnose aktiviert haben, können Sie wie folgt auf die Protokolle zugreifen:

> [!TIP]
> Es wird empfohlen, mindestens eine Livepipeline zum Ausgeben von Ereignissen zu aktivieren. 

- Suchen Sie im Portal nach dem Speicherkonto, in das die Protokolle geschrieben wurden.
- Öffnen Sie das Verwaltungsblatt für dieses Speicherkonto.
- Navigieren Sie zum Speicher-Explorer > klappen Sie Ihr Speicherkonto auf, und dann sehen Sie einen Blobcontainer „insights-logs-category“. Dieses Blob enthält Protokolle im JSON-Dateiformat. Sie können diese Protokolle herunterladen, um das Problem zu untersuchen.

## <a name="view-metrics"></a>Anzeigen von Metriken 

Die Videoanalyse gibt auch Metriken für Erfassung und Pipelines aus, die in folgender Weise bei der Identifizierung von Problemen helfen können:
- IngressBytes: Die Gesamtzahl der von einer Pipeline empfangenen Bytes. Ein stetig steigender Wert ist ein Hinweis darauf, dass die Pipeline fehlerfrei ist und Videodaten von der RTSP-Kamera empfängt.
- Pipelines: Hilft bei der Überprüfung des Pipelinestatus und der Anzahl.

## <a name="view-activity-logs"></a>Anzeigen von Aktivitätsprotokollen

Ein **Aktivitätsprotokoll** wird automatisch generiert; der Zugriff erfolgt über den Abschnitt „Aktivitätsprotokoll“ des Blatts „Kontoverwaltung“ von Video Analyzer im Azure-Portal. Sie können den Verlauf der ARM-API-Aufrufe an Ihr Konto sowie relevante Details anzeigen.

## <a name="common-error-scenarios"></a>Allgemeine Fehlerszenarien

Einige der häufigen Fehler, die beim Video Analyzer-Dienst auftreten, werden unten beschrieben.

### <a name="unable-to-play-video-after-activating-live-pipeline"></a>Nach dem Aktivieren einer Livepipeline kann kein Video wiedergegeben werden

- Wenn Sie das Video Analyzer-Widget für die Wiedergabe des Videos verwenden, versuchen Sie, es stattdessen im Azure-Portal wiederzugeben, falls Sie Zugriff darauf haben. Wenn das Video im Azure-Portal wiedergegeben wird, aber nicht im Widget, sollten Sie mit dem [Widgetabschnitt](#playback-error-with-the-widget) unten fortfahren.

- Wenn das Video nicht im Azure-Portal wiedergegeben wird, prüfen Sie, ob der Video Analyzer-Dienst Videodaten von der RTSP-Kamera empfängt. Wählen Sie die Metrik „Ingress Bytes“ aus, indem Sie im Portal zum Abschnitt „Überwachung > Metriken“ navigieren. Wenn die Aggregation zunimmt, ist die Verbindung zwischen der RTSP-Kamera und dem Dienst fehlerfrei. Die Summe von Ingress Bytes ist unterhalb des Diagramms verfügbar. 

- Wenn der Dienst keine Videodaten von der RTSP-Kamera empfängt, besteht der nächste Schritt im [Anzeigen der relevanten Diagnoseprotokolle](#view-diagnostics). Wahrscheinlich finden Sie einen Fehler in der Art von [ProtocolError](#diagnostic-logs-have-a-protocolerror-with-code-401), dann können Sie die Problembehandlung wie unten beschrieben fortsetzen.

### <a name="diagnostic-logs-have-a-protocolerror-with-code-401"></a>Diagnoseprotokolle weisen einen ProtocolError mit dem Code 401 auf.

- Wenn Microsoft.VideoAnalyzer.Diagnostics.ProtocolError in Diagnoseprotokollen angezeigt wird und der Code wie im Folgenden beschrieben auf 401 festgelegt ist, besteht der erste Schritt im erneuten Überprüfen der RTSP-Anmeldeinformationen. Ein typisches Beispielereignis ist unten angegeben:

   ```
   {
       "time": "2021-10-15T02:56:18.7890000Z",
       "resourceId": "/SUBSCRIPTIONS/{GUID}/RESOURCEGROUPS/8AVA/PROVIDERS/MICROSOFT.MEDIA/VIDEOANALYZERS/AVASAMPLEZ2OHI3VBIRQPC",
       "region": "westcentralus",
       "category": "Diagnostics",
       "operationName": "Microsoft.VideoAnalyzer.Diagnostics.ProtocolError",
       "operationVersion": "1.0",
       "level": "Error",
       "traceContext": "{\n  \"traceId\": \"f728d155-b4fd-4aec-8307-bbe2a324f4c3\"\n}",
       "properties": {
           "subject": "/livePipelines/your-pipeline/sources/rtspSource",
           "body": {
               "code": "401",
               "target": "rtsp://127.0.0.1:33643/some-path",
               "protocol": "rtsp"
           }
       }
   }

   ```

- Wenn Sie eine Livepipeline verwenden, um eine Verbindung mit einer Kamera herzustellen, auf die über das Internet zugegriffen werden kann, überprüfen Sie die RTSP-URL, den Benutzernamen und das Kennwort, die beim Erstellen der Livepipeline verwendet wurden. Sie können GET in der Livepipeline aufrufen, um die URL und den Benutzernamen anzuzeigen, das Kennwort wird aber nicht zurückgegeben. Sie sollten den Code überprüfen, der zum Erstellen der Livepipeline verwendet wurde.

- Wenn Sie einen [Remotegeräteadapter](./use-remote-device-adapter.md) verwenden, führen Sie die folgenden Schritte aus.

   - Überprüfen Sie, ob Ihr [IoT-Hub mit Ihrem Video Analyzer-Konto verbunden](../create-video-analyzer-account.md#post-deployment-steps) ist. Dies ist für die Verwendung eines Remotegeräteadapters erforderlich.
   - Führen Sie die direkte Methode `remoteDeviceAdapterList` auf dem Edgemodul aus, und überprüfen Sie die IP-Adresse. Eine Beispielanforderung und -antwort finden Sie [hier](../edge/direct-methods.md).
   - Untersuchen Sie die Antwort für den Remotegeräteadapter, den Sie in der Livepipeline verwenden, bei der das Problem besteht, und vergleichen Sie sie mit dem Beispiel in [diesem Artikel](use-remote-device-adapter.md). Überprüfen Sie, ob die IP-Adresse der Kamera richtig ist.
   - Wechseln Sie zum Azure-Portal > Video Analyzer-Konto > Live > Pipelines > Livepipeline bearbeiten > geben Sie den RTSP-Benutzernamen und das Kennwort erneut ein. Überprüfen Sie, ob die von Ihnen eingegebene RTSP-URL mit `rtsp://localhost:554/…` beginnt. Hier ist die Verwendung von `localhost` erforderlich.

- Wenn sich das Problem mithilfe der oben genannten Schritte nicht beheben lässt und die Videowiedergabe weiterhin nicht funktioniert, melden Sie sich beim Azure-Portal an, und öffnen Sie ein Supportticket. Möglicherweise müssen Sie die Protokolle aus dem Video Analyzer-Edgemodul anfügen. Weitere Informationen finden Sie im Abschnitt „Verwenden des support-bundle-Befehls“ im [Edge-Dokument zur Problembehandlung](../edge/troubleshoot.md#common-error-resolutions).

### <a name="unable-to-record-to-a-video-resource"></a>Aufzeichnen in einer Videoressource ist nicht möglich

Bei Video Analyzer sollten Sie für die Aufzeichnung von einer distinkten RTSP-Kamera eine eigene Videoressource verwenden. Sie müssten außerdem zu einer neuen Videoressource wechseln, wenn Sie die Kameraeinstellungen (z. B. die Auflösung) ändern. Einige der Beispielpipelinetopologien weisen in den Knoteneigenschaften der Videosenke hart codierte Namen für die Videoressource auf. Wenn Sie diese Topologien direkt mit verschiedenen Kameras verwenden, tritt dieses Problem auf. Ändern Sie die `videoName`-Eigenschaft im Knoten der Videosenke, um Eindeutigkeit sicherzustellen.

### <a name="interrupted-recording-or-playback"></a>Unterbrochene Aufzeichnung oder Wiedergabe

Beim Erstellen einer Livepipeline müssen Sie die maximale Bitrate (`bitrateKbps`) angeben, mit der die RTSP-Kamera Videodaten an den Dienst sendet.
Wenn die Kamera diesen Grenzwert überschreitet, wird Video Analyzer-Dienst kurzzeitig von der Kamera getrennt. Er kann dann versuchen, erneut eine Verbindung zur Kamera herzustellen, falls es sich um eine vorübergehende Spitze in der Bitrate gehandelt hat. Sie können diese Situation erkennen, indem Sie in den Diagnoseprotokollen nach dem Ereignis Microsoft.VideoAnalyzer.Diagnostics.RtspIngestionSessionEnded mit einem Fehlercode `SourceBitrateExceeded` suchen.
Zum Beheben dieses Problems setzen Sie entweder die Bitrateneinstellung für die Kamera herab, oder erhöhen Sie den Wert `bitrateKbps` der Livepipeline, damit er mit der Kameraeinstellung übereinstimmt.

### <a name="playback-error-with-the-widget"></a>Wiedergabefehler im Widget

Wenn im Video Analyzer-Widget der Fehler „Zugriff verboten“ angezeigt wird, sollte im Überwachungsprotokoll ein Warnungsereignis angezeigt werden.

- Vergewissern Sie sich, dass Sie das Client-API-JWT-Token und die entsprechende Zugriffsrichtlinie generiert haben. Lesen Sie dazu die Dokumentation für [creating-a-token](../access-policies.md) und [creating-an-access-policy](../access-policies.md#creating-an-access-policy). Der Player funktioniert nicht, wenn die Zugriffsrichtlinie nicht ordnungsgemäß eingerichtet wurde und das JWT-Token nicht mit der Richtlinie übereinstimmt. 
- Wenn das Problem nicht behoben wurde, sammeln Sie die Widgetprotokolle, und erstellen Sie ein Supportticket im Azure-Portal
- Sammeln von Widgetprotokollen:
    - Drücken Sie F12, um die Browser-Entwicklungstools zu aktivieren, wechseln Sie zur Registerkarte „Konsole“, und aktivieren Sie die Protokollierung für „Alle Ebenen“.   
    - Wählen Sie über das Einstellungen-Symbol „Einstellungen“ > „Konsole“ > „Zeitstempel anzeigen“ aus. Speichern Sie die Protokolle.

## <a name="collect-logs-for-submitting-a-support-ticket"></a>Sammeln von Protokollen zum Übermitteln eines Supporttickets
   
Wenn sich das Problem durch die Schritte zur Self-Service-Problembehandlung nicht lösen lässt und es weitere Probleme gibt, bei denen Sie Hilfe benötigen, öffnen Sie im Azure-Portal ein Supportticket mit den relevanten Details und Protokollen. Sie können uns außerdem erreichen, indem Sie eine E-Mail an videoanalyzerhelp@microsoft.com senden.
   
> [!WARNING]
> Es kann sein, dass diese Protokolldateien personenbezogene Informationen (Personally Identifiable Information, PII) enthalten, beispielsweise Ihre IP-Adresse. Alle lokalen Kopien der Protokolle werden gelöscht, sobald wir die Untersuchung abgeschlossen haben und das Supportticket schließen.
   
## <a name="next-steps"></a>Nächste Schritte

[Häufig gestellte Fragen](./faq.yml)
