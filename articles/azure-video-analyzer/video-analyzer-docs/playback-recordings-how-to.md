---
title: Anzeigen von Videos – Azure Video Analyzer
description: Sie können Azure Video Analyzer für die fortlaufende Videoaufzeichnung verwenden. Die Videoaufzeichnungen können Sie für Wochen oder Monate in der Cloud speichern. Sie können Ihre Aufzeichnung auch auf die für Sie interessanten Clips beschränken, indem Sie die ereignisbasierte Aufzeichnung verwenden. Wenn Sie den Video Analyzer-Dienst verwenden, um Videos von Kameras zu erfassen, können Sie das Video außerdem während der Erfassung streamen. In diesem Artikel wird beschrieben, wie Sie solche Videos anzeigen.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 552daea0a5a19c7c9e11aca9b47d61ea112a42b3
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846974"
---
# <a name="viewing-of-videos"></a>Anzeigen von Videos

## <a name="suggested-pre-reading"></a>Empfohlene Lektüre zur Vorbereitung

* [Videoressource des Video Analyzers](terminology.md#video)
* [Fortlaufende Videoaufzeichnung](continuous-video-recording.md)
* [Ereignisbasierte Videoaufzeichnung](event-based-video-recording-concept.md)

## <a name="background"></a>Hintergrund  

Sie können [Videoressourcen](terminology.md#video) in Ihrem Azure Video Analyzer-Konto erstellen, indem Sie entweder von einer RTSP-Kamera aufzeichnen oder einen Teil einer solchen Aufzeichnung. Wenn Sie einen [virtuellen Computer](terminology.md#vms) mit Video Analyzer-APIs erstellen, hilft Ihnen dieser Artikel, zu verstehen, wie Sie Videos anzeigen können. Nachdem Sie diesen Artikel gelesen haben, sollten Sie mit dem Artikel zu [Zugriffsrichtlinien](access-policies.md) und dem [Video Analyzer-Playerwidget](player-widget.md) fortfahren. 

Wenn Sie die Funktionen von Video Analyzer auswerten, können Sie eine der [Schnellstartanleitungen](edge/detect-motion-record-video-clips-cloud.md) oder [Tutorials](edge/use-continuous-video-recording.md) verwenden und sich des Azure-Portals bedienen, um die Videos anzusehen.
<!-- TODO - add a section here about 1P/3P SaaS and how to use widgets to allow end users to view videos without talking to ARM APIs -->

## <a name="creating-videos"></a>Erstellen von Videos

Im Folgenden finden Sie einige Möglichkeiten zum Erstellen von Videos mithilfe des Video Analyzer-Edgemoduls:

* Zeichnen [Sie kontinuierlich](continuous-video-recording.md) (CVR) von einer RTSP-Kamera für Wochen oder Monate oder länger auf.
* Zeichnen Sie nur die für Sie interessanten Teile über [die ereignisbasierte Videoaufzeichnung EVENT-Based Video Recording](event-based-video-recording-concept.md) (EVR) auf. 
 
Sie können auch den Video Analyzer-Dienst verwenden, um Videos mit CVR zu erstellen. Sie können den Dienst auch zum Erstellen eines Videos verwenden, indem Sie einen Teil einer Videoaufzeichnung exportieren. Diese Videos enthalten eine herunterladbare Datei (im MP4-Dateiformat).

## <a name="accessing-videos"></a>Zugreifen auf Videos

Sie können die ARM-API [`Videos`](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/resource-manager/Microsoft.Media/preview/2021-11-01-preview/Videos.json) abfragen, um die Eigenschaften der Videoressource anzeigen zu lassen. Mit Video Analyzer können Sie auch Anwendungen erstellen, in denen Endbenutzer Videos durchsuchen und anzeigen können, ohne ARM zu verwenden. Wie im Artikel zu öffentlichen [Endpunkten](access-public-endpoints-networking.md)gezeigt, haben Sie Zugriff auf sogenannte Client-APIs, über die Sie die Eigenschaften von Videos abfragen können. Diese APIs werden über einen Client-API-Endpunkt verfügbar gemacht, den Sie im Abschnitt Übersicht des Blatts Video Analyzer im Azure-Portal oder über die ARM-API [`VideoAnalyzers`](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/resource-manager/Microsoft.Media/preview/2021-11-01-preview/VideoAnalyzers.json) finden. Sie können den Zugriff auf diesen Endpunkt über [Zugriffsrichtlinien](access-policies.md)steuern. Dies wird im Artikel zum [Video Analyzer-Playerwidget](player-widget.md) veranschaulicht.

## <a name="determining-that-a-video-recording-is-ready-for-viewing"></a>Bestimmen, ob eine Videoaufzeichnung für die Anzeige bereit ist

Wenn Ihre Videoressource die Aufzeichnung von einer RTSP-Kamera darstellt, können Sie diesen [Inhalt streamen](terminology.md#streaming), entweder nach Abschluss oder auch während der Aufzeichnung. Dies wird über das Kennzeichen `canStream` angezeigt, das für die Videoressource auf `true` gesetzt wird. Beachten Sie, dass solche Videos von `type` auf `archive`zu setzen sind und der URL für Wiedergabe oder Streaming in `archiveBaseUrl` zurückgegeben wird. 

Wenn Sie einen Teil einer Videoaufzeichnung in eine MP4-Datei exportieren, ist die resultierende Videoressource von `type` auf `file` zu setzen, und sie kann nach Abschluss des Videoexportauftrags wiedergeben oder heruntergeladen werden. Der URL für die Wiedergabe oder den Download solcher Dateien wird in `downloadUrl` zurückgegeben.
   > [!NOTE]
   > Für die obigen URLs ist ein [Inhaber-Token](./access-policies.md#creating-a-token) erforderlich. Weitere Informationen finden [Sie in der Video Analyzer Playerwidget-Dokumentation](player-widget.md).

## <a name="recording-and-playback-latencies"></a>Wartezeiten bei Aufzeichnung und Wiedergabe

Wenn Sie das Video Analyzer-Edgemodul zum Aufzeichnen in einer Videoressource verwenden, geben Sie eine [`segmentLength` Eigenschaft ](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/data-plane/VideoAnalyzer.Edge/preview/1.0.0/AzureVideoAnalyzer.json) in Ihrer Pipelinetopologie an, die das Modul anweist, eine minimale Videodauer (in Sekunden) zu erfassen, bevor es in die Cloud geschrieben wird. Wenn `segmentLength` z. B. auf „300“ festgelegt ist, erfasst das Modul 5 Minuten an Videodaten, bevor ein 5-Minuten-Block hochgeladen wird. Anschließend wechselt es für die nächsten 5 Minuten in den Erfassungsmodus und lädt danach erneut Daten hoch. Wenn Sie `segmentLength` auf einen höheren Wert festlegen, verringern Sie damit die Transaktionskosten für Azure Storage, da die Anzahl von Lese- und Schreibvorgängen immer unter der angegebenen Anzahl von `segmentLength` Sekunden liegt. Wenn Sie den Video Analyzer-Dienst verwenden, hat die Pipelinetopologie die gleiche [`segmentLength` Eigenschaft](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/resource-manager/Microsoft.Media/preview/2021-11-01-preview/PipelineTopologies.json).

Folglich wird das Streaming des Videos vom Video Analyzer-Account mindestens um diese Zeit verzögert. 

Ein weiterer Faktor, der die Wiedergabelatenz bestimmt (die Verzögerung zwischen dem Zeitpunkt, zu dem ein Ereignis vor der Kamera auftritt, bis zu dem Zeitpunkt, an dem es auf einem Wiedergabegerät angezeigt werden kann), ist die [GOP](https://en.wikipedia.org/wiki/Group_of_pictures)-Dauer (Group-of-Pictures, d.h. Gruppe von Bildern). Wie [die Reduzierung der Verzögerung von Livestreams mithilfe von drei einfachen Techniken](https://medium.com/vrt-digital-studio/reducing-the-delay-of-live-streams-by-using-3-simple-techniques-e8e028b0a641) erklärt, bedeutet eine höhere GOP-Dauer auch eine größere Latenz. In Überwachungs- und Sicherheitsszenarien werden üblicherweise IP-Kameras eingesetzt, die mit einer GOP-Dauer von mehr als 30 Sekunden konfiguriert sind. Dies hat erhebliche Auswirkungen auf die Gesamtlatenz.

## <a name="low-latency-streaming"></a>Streaming mit geringer Latenz

Wenn Sie den Video Analyzer-Dienst zum Erfassen und Aufzeichnen von Videos von RTSP-Kameras verwenden, können Sie das Video mit Latenzen von etwa 2 Sekunden mit Streaming [geringer Latenz](terminology.md#low-latency-streaming) anzeigen. Der Dienst stellt einen Websockettunnel zur Verfügung, über den ein RTSP-fähiger Player wie das [Video Analyzer-Playerwidget](player-widget.md) Video mithilfe des [RTSP-Protokolls](https://datatracker.ietf.org/doc/html/rfc7826.html) empfangen kann. Beachten Sie, dass die Gesamtlatenz von der Netzwerkbandbreite zwischen Kamera und Cloud sowie zwischen der Cloud und dem Wiedergabegerät sowie von der Verarbeitungsleistung des Wiedergabegeräts abhängt. Der URL für Streaming mit geringer Latenz wird in `rtspTunnelUrl` zurückgegeben.

   > [!NOTE]
   > Für den obigen URL ist ein [Inhaber-Token](./access-policies.md#creating-a-token) erforderlich. Weitere Informationen finden [Sie in der Video Analyzer Playerwidget-Dokumentation](player-widget.md).

## <a name="video-analyzer-player-widget"></a>Video Analyzer-Playerwidget
Der Video Analyzer bietet Ihnen die erforderlichen Funktionen zum Übertragen von Streams über die Protokolle HLS und MPEG-DASH an Wiedergabegeräte (Clients). Sie verwenden das [Video Analyzer-Playerwidget](player-widget.md) zum Abrufen der Streaming-URL und des Wiedergabeautorisierungstokens und verwenden diese in Client-Apps, um die Video- und Rückschlussmetadaten wiedergeben zu können.

Sie können das Video Analyzer-Playerwidget zum Anzeigen von Videos installieren. Das Widget kann mithilfe von`npm` oder`yarn` installiert werden. Dadurch können Sie es in Ihre eigene clientseitige Anwendung hinzufügen. Führen Sie einen der folgenden Befehle aus, um das Widget in Ihre eigene Anwendung aufzunehmen:

NPM:
```
npm install –-save @azure/video-analyzer-widgets
```
YARN:
```
yarn add @azure/video-analyzer-widgets 
```
Alternativ können Sie ein vorhandenes Prä-Build-Skript einbetten, indem Sie type="module" zum Skriptelement hinzufügen, das auf den Prä-Build-Speicherort mithilfe des folgenden Beispiels referenziert:

```
<script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets"></script> 
``` 

## <a name="viewing-video-with-inference-results"></a>Anzeigen von Videos mit Rückschlussergebnissen
Wenn Ihre [Pipeline](pipeline.md) beim Aufzeichnen von Videos mithilfe des Video Analyzer-Edgemoduls KI zum Generieren von Rückschlussergebnissen verwendet, können Sie diese Ergebnisse zusammen mit dem Video aufzeichnen. Beim Anzeigen des Videos überlagert unter Umständen das Video Analyzer-Playerwidget die Ergebnisse im Video. Weitere Einzelheiten finden Sie in [diesem Tutorial](edge/record-stream-inference-data-with-video.md).

## <a name="next-steps"></a>Nächste Schritte

* [Verstehen von Zugriffsrichtlinien](access-policies.md)
* [Verwenden des Video Analyzer-Playerwidgets](player-widget.md)
* [Fortlaufende Videoaufzeichnung hochkant](edge/use-continuous-video-recording.md)
* [Kontinuierliche Videoaufzeichnung in der Cloud](cloud/get-started-livepipelines-portal.md)
