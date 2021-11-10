---
title: Azure Video Analyzer-Begriffe
description: Dieser Artikel vermittelt einen Überblick über die Begriffe des Azure Video Analyzers.
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 9010ae81606bcd84d37debc4c702a0bc53e328dd
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846955"
---
# <a name="azure-video-analyzer-terminology"></a>Azure Video Analyzer-Begriffe

Dieser Artikel vermittelt einen Überblick über die Begriffe des [ Azure Video Analyzers](overview.md).

## <a name="pipeline-topology"></a>Pipeline-Topologie

Mit einer [Pipeline-Topologie](pipeline.md) können Sie bestimmen, von wo aus Medien erfasst werden sollen, wie sie verarbeitet werden sollen und wohin die Ergebnisse geliefert werden sollen. Sie ermöglicht die Definition einer Pipeline, die aus Quellen, Prozessoren und Senkknoten besteht, mit denen Sie Live-Videoanalyseanwendungen erstellen können. 

## <a name="rtsp"></a>RTSP

[RTSP](https://tools.ietf.org/html/rfc2326) bezieht sich auf das Real-Time Streaming Protocol. Es handelt sich um ein Protokoll auf Anwendungsebene, mit dem die Übermittlung von Daten mit Echtzeiteigenschaften gesteuert werden kann. RTSP bietet ein erweiterbares Framework, um die kontrollierte, bedarfsgesteuerte Bereitstellung von Echtzeitdaten wie z. B. Audio- und Videodaten zu ermöglichen. Der Azure Video Analyzer [unterstützt](pipeline.md#rtsp-source) die Erfassung, das Analysieren und das Aufzeichnen von Videos von IP-Kameras, die RTSP unterstützen.

## <a name="vms"></a>VMS

[VMS](https://en.wikipedia.org/wiki/Video_management_system) ist die Abkürzung für Video Management System (Videoverwaltungssystem). Solche Systeme werden zur Konfiguration und Steuerung von CCTV-Kameras sowie zur Erfassung und Aufzeichnung von Videos verwendet. Diese Systeme bieten auch Client-Anwendungen zur Wiedergabe der aufgezeichneten Videos.

## <a name="recording"></a>Aufzeichnung

Im Zusammenhang mit einem Videoverwaltungssystem für Überwachungskameras bezieht sich die Videoaufzeichnung auf den Vorgang der Erfassung von Videos von den Kameras und deren Speicherung für die spätere Wiedergabe über mobile und Browser-Apps. Die Videoaufzeichnung kann in [fortlaufende Videoaufzeichnung](continuous-video-recording.md) und [ereignisbasierte Videoaufzeichnung](event-based-video-recording-concept.md) kategorisiert werden. Diese werden auf der Konzeptseite zur [Videoaufzeichnung](video-recording.md) ausführlicher erläutert.

## <a name="streaming"></a>Streaming

Sie können Azure Video Analyzer verwenden, um Videoaufzeichnungen mithilfe von Branchenstandards für HTTP-basierte Medienstreamingprotokolle wie [HTTP Live Streaming (HLS)](https://developer.apple.com/streaming/) und [MPEG-DASH](https://dashif.org/about/) an Clients zu streamen. Sie können die [Player-Widgets (Webkomponenten)](https://github.com/Azure/video-analyzer/blob/main/widgets/readme.md) vom Azure Video Analyzer verwenden, um Videoressourcen wiederzugeben. Zusätzlich wird HLS von Web-Playern wie [JW Player](https://www.jwplayer.com/), [hls.js](https://github.com/video-dev/hls.js/), [VideoJS](https://videojs.com/), dem [Shaka Player von Google](https://github.com/google/shaka-player) unterstützt, oder Sie können nativ in mobilen Apps mit dem [Exoplayer](https://github.com/google/ExoPlayer) von Android und der [AV Foundation](https://developer.apple.com/av-foundation/) von iOS wiedergeben. MPEG-DASH wird ebenfalls von einer [Liste von Clients auf dieser Seite unterstützt](https://dashif.org/tools/clients/).

## <a name="exporting"></a>Exportieren

Im Zusammenhang mit einem VMS für Überwachungskameras bezieht sich der Videoexport auf den Prozess der Aufnahme ausgewählter Teile einer Videoaufzeichnung und der Erstellung einer separaten Datei, typischerweise einer [MP4](https://en.wikipedia.org/wiki/MPEG-4_Part_14)-Datei, die dann extern weitergegeben werden kann. Die Videoaufzeichnung könnte zum Beispiel Aufnahmen enthalten, bei denen durch schnelles Handeln des Personals ein Sicherheitsvorfall verhindert wurde. Dieser Teil der Aufzeichnung kann in eine MP4-Datei exportiert werden, um ihn in zukünftigen Schulungen zu verwenden.

## <a name="video"></a>Video

Videos sind Ressourcen in Ihrem Video Analyzer-Konto, die VMS-Funktionen wie Aufzeichnung, Wiedergabe, Streaming und Exportieren ermöglichen. Videos können von RTSP-Kameras aufgezeichnet oder durch Exportieren von Teilen eines bereits aufgezeichneten Videos erstellt werden. Aufgezeichnete Videos können gestreamt und mit dem Video Analyzer-Player-Widget oder anderen kompatiblen Playern angezeigt werden. Die exportierten Videos können als MP4-Dateien heruntergeladen werden.

Wenn Sie Video Analyzer für die Aufzeichnung von einer RTSP-Kamera verwenden, sollten Sie diese Videoressource mit dieser Kamera verknüpfen. Sie können Videos von dieser Kamera kontinuierlich in dieser Videoressource aufzeichnen oder sporadisch auf der Grundlage von Ereignissen aufzeichnen - Video Analyzer unterstützt das Anhängen von Daten an eine vorhandene Videoressource. Dies setzt jedoch voraus, dass die Eigenschaften der RTSP-Kamera (Auflösung, Bildrate usw.) unverändert bleiben. Wenn Sie die Kameraeinstellungen ändern müssen, sollten Sie die Aufzeichnung auf eine neue Videoressource umstellen.

Wenn Sie ein Video Analyzer-Konto erstellen, müssen Sie ihm ein Azure-Speicherkonto zuordnen. Sowohl aufgenommene als auch exportierte Videos werden als Blobs in einem Container im Speicherkonto gespeichert. Jegliche Inhalte, die mit solchen Videoressourcen verbunden sind, werden als Blobs in den entsprechenden Containern gespeichert, während Azure Video Analyzer die Metadaten (z. B. Name, Beschreibung, Erstellungszeit) enthält.

## <a name="grpc"></a>gRPC

[gRPC](https://grpc.io/docs/guides/) ist ein sprachunabhängiges RPC-Framework mit hoher Leistung (Remote Procedure Call, Remoteprozeduraufruf). Es verwendet sitzungsbasierte strukturierte Schemas über [Protocol Buffers 3](https://developers.google.com/protocol-buffers/docs/proto3) als zugrunde liegendes Nachrichtenaustauschformat für die Kommunikation.

## <a name="low-latency-streaming"></a>Streaming mit geringer Latenz

Videostreaming mit geringer Latenz ist eine nützliche Fähigkeit eines VMS-Systems. Video Analyzer kann Live-Videos mit einer Verzögerung von etwa 2 Sekunden übertragen. Unter Latenz versteht man die Verzögerung zwischen dem Zeitpunkt, an dem ein Ereignis vor der Kamera stattfindet, und dem Zeitpunkt, an dem dieses Ereignis auf einem Wiedergabegerät zu sehen ist.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Pipelines](pipeline.md).
