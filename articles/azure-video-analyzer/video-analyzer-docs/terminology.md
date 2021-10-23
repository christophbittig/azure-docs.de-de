---
title: Azure Video Analyzer-Begriffe
description: Dieser Artikel vermittelt einen Überblick über die Begriffe des Azure Video Analyzers.
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 1b9de2608d3b6ccb3922636f8cd473e79eff5e00
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130070240"
---
# <a name="azure-video-analyzer-terminology"></a>Azure Video Analyzer-Begriffe

Dieser Artikel vermittelt einen Überblick über die Begriffe des [ Azure Video Analyzers](overview.md).

## <a name="pipeline-topology"></a>Pipeline-Topologie

Mit einer [Pipeline-Topologie](pipeline.md) können Sie bestimmen, von wo aus Medien erfasst werden sollen, wie sie verarbeitet werden sollen und wohin die Ergebnisse geliefert werden sollen. Sie ermöglicht die Definition einer Pipeline, die aus Quellen, Prozessoren und Senkknoten besteht, mit denen Sie Live-Videoanalyseanwendungen erstellen können. 

## <a name="rtsp"></a>RTSP

[RTSP](https://tools.ietf.org/html/rfc2326) ist die Abkürzung für Real-Time Streaming Protocol (Echtzeit-Streamingprotokoll). Es handelt sich um ein Protokoll auf Anwendungsebene, mit dem die Übermittlung von Daten mit Echtzeiteigenschaften gesteuert werden kann. RTSP bietet ein erweiterbares Framework, um die kontrollierte, bedarfsgesteuerte Bereitstellung von Echtzeitdaten wie z. B. Audio- und Videodaten zu ermöglichen. Der Azure Video Analyzer [unterstützt](pipeline.md#rtsp-source) die Erfassung, das Analysieren und das Aufzeichnen von Videos von IP-Kameras, die RTSP unterstützen.


## <a name="recording"></a>Aufzeichnung

Im Zusammenhang mit einem Videoverwaltungssystem für Überwachungskameras bezieht sich die Videoaufzeichnung auf den Vorgang der Erfassung von Videos von den Kameras und deren Speicherung für die spätere Wiedergabe über mobile und Browser-Apps. Die Videoaufzeichnung kann in [fortlaufende Videoaufzeichnung](continuous-video-recording.md) und [ereignisbasierte Videoaufzeichnung](event-based-video-recording-concept.md) kategorisiert werden. Diese werden auf der Konzeptseite zur [Videoaufzeichnung](video-recording.md) ausführlicher erläutert.

## <a name="video"></a>Video

Wenn Sie ein Azure Video Analyzer-Konto erstellen, müssen Sie es einem Azure-Speicherkonto zuordnen. Sie können den Azure Video Analyzer verwenden, um das Live-Video von einer Kamera aufzuzeichnen und diese Daten entweder auf der Festplatte oder im Cloud-Speicher zu speichern. Im letzteren Fall werden Daten als Blobs in einem Container im Speicherkonto gespeichert. Videos sind Ressourcen in Ihrem Azure Video Analyzer-Konto, die solchen Blob-Containern zugeordnet sind. Jegliche Inhalte, die mit solchen Videoressourcen verbunden sind, werden als Blobs in den entsprechenden Containern gespeichert, während Azure Video Analyzer die Metadaten (z. B. Name, Beschreibung, Erstellungszeit) enthält.

Sie können Azure Video Analyzer verwenden, um Videoressourcen zu erstellen und vorhandenen Videos Daten hinzuzufügen. Dies ermöglicht die Szenarien der kontinuierlichen und ereignisbasierten Videoaufzeichnung und -Wiedergabe (mit Videoerfassung auf dem Edge-Gerät, Aufzeichnung im Azure Video Analyzer und Wiedergabe über die Azure Video Analyzer Streaming-Funktionen).

## <a name="grpc"></a>gRPC

[gRPC](https://grpc.io/docs/guides/) ist ein sprachunabhängiges RPC-Framework mit hoher Leistung (Remote Procedure Call, Remoteprozeduraufruf). Es verwendet sitzungsbasierte strukturierte Schemas über [Protocol Buffers 3](https://developers.google.com/protocol-buffers/docs/proto3) als zugrunde liegendes Nachrichtenaustauschformat für die Kommunikation.

## <a name="streaming"></a>Streaming

Sie können Azure Video Analyzer verwenden, um Videoaufzeichnungen mithilfe von Branchenstandards für HTTP-basierte Medienstreamingprotokolle wie [HTTP Live Streaming (HLS)](https://developer.apple.com/streaming/) und [MPEG-DASH](https://dashif.org/about/) an Clients zu streamen. Sie können die [Player-Widgets (Webkomponenten)](https://github.com/Azure/video-analyzer/blob/main/widgets/readme.md) vom Azure Video Analyzer verwenden, um Videoressourcen wiederzugeben. Zusätzlich wird HLS von Web-Playern wie [JW Player](https://www.jwplayer.com/), [hls.js](https://github.com/video-dev/hls.js/), [VideoJS](https://videojs.com/), dem [Shaka Player von Google](https://github.com/google/shaka-player) unterstützt, oder Sie können nativ in mobilen Apps mit dem [Exoplayer](https://github.com/google/ExoPlayer) von Android und der [AV Foundation](https://developer.apple.com/av-foundation/) von iOS wiedergeben. MPEG-DASH wird ebenfalls von einer [Liste von Clients auf dieser Seite unterstützt](https://dashif.org/members/).

## <a name="vms"></a>VMS

[VMS](https://en.wikipedia.org/wiki/Video_management_system) ist die Abkürzung für Video Management System (Videoverwaltungssystem). Solche Systeme werden zum Konfigurieren und Steuern von Überwachungskameras (CCTV) und zum Aufzeichnen von Videos von diesen verwendet. Diese Systeme bieten auch Client-Anwendungen zur Wiedergabe der aufgezeichneten Videos.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Pipelines](pipeline.md).
