---
title: Terminologie zu Azure Live Video Analytics in IoT Edge
description: Dieser Artikel bietet eine Übersicht über die Terminologie zu Azure Live Video Analytics in IoT Edge.
ms.topic: conceptual
ms.date: 05/30/2020
ms.openlocfilehash: 69da3515987387b62b59d33657e9a0b0a28a1389
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130073903"
---
# <a name="live-video-analytics-terminology"></a>Terminologie für Live Video Analytics

[!INCLUDE [redirect to Azure Video Analyzer](./includes/redirect-video-analyzer.md)]

Dieser Artikel bietet eine Übersicht über die Terminologie im Zusammenhang mit [Live Video Analytics in IoT Edge](overview.md).

## <a name="azure-media-services"></a>Azure Media Services

Azure Media Services ist eine Medienplattform in der Cloud, die Ihnen das Erstellen von Medienlösungen ermöglicht. Weitere Informationen hierzu finden Sie in der Dokumentation zu [Azure Media Services](../latest/media-services-overview.md).

## <a name="asset"></a>Asset

Ein [Medienobjekt](../latest/assets-concept.md) ist eine Entität in Azure Media Services, die einem Blobcontainer in dem Azure Storage-Konto, das an ein Media Services-Konto angefügt ist, zugeordnet ist. Alle Dateien für ein Medienobjekt werden als Blobs in diesem Container gespeichert, während Media Services die Metadaten (z. B. Name, Beschreibung, Erstellungszeit) speichert, die dem Medienobjekt zugeordnet sind.

Live Video Analytics in IoT Edge kann Medienobjekte erstellen oder vorhandenen Medienobjekten Daten hinzufügen. Dies ermöglicht Szenarien mit kontinuierlicher und ereignisbasierter Videoaufzeichnung und -wiedergabe (mit Videoaufzeichnung auf dem Edgegerät, Aufzeichnung in Azure Media Services und Wiedergabe über vorhandene Azure Media Services-Streamingfunktionen).

## <a name="grpc"></a>gRPC

[gRPC](https://grpc.io/docs/guides/) ist ein sprachunabhängiges RPC-Framework mit hoher Leistung (Remote Procedure Call, Remoteprozeduraufruf). Es verwendet sitzungsbasierte strukturierte Schemas über [Protocol Buffers 3](https://developers.google.com/protocol-buffers/docs/proto3) als zugrunde liegendes Nachrichtenaustauschformat für die Kommunikation.

## <a name="media-graph"></a>Mediendiagramm

Mit einem [Mediendiagramm](media-graph-concept.md) können Sie definieren, von welchem Ort Medien erfasst, wie diese verarbeitet und wohin die Ergebnisse übermittelt werden sollen. Damit können Sie ein Diagramm definieren, das aus Quellen-, Verarbeitungs- und Senkenknoten besteht und somit die Möglichkeit bietet, Anwendungen zur Livevideoanalyse zu erstellen. Das Mediendiagramm wird auf der Konzeptseite zu Mediendiagrammen ausführlich behandelt.

## <a name="recording"></a>Aufzeichnung

Im Kontext eines Videoverwaltungssystems für Sicherheitskameras bezieht sich die Videoaufzeichnung auf das Erfassen von Videos von den Kameras und das Speichern dieser Aufzeichnungen in einer oder mehreren Dateien für die spätere Wiedergabe über mobile und Browser-Apps. Die Videoaufzeichnung kann in [fortlaufende Videoaufzeichnung](continuous-video-recording-concept.md) und [ereignisbasierte Videoaufzeichnung](event-based-video-recording-concept.md) kategorisiert werden. Diese werden auf der Konzeptseite zur [Videoaufzeichnung](video-recording-concept.md) ausführlicher erläutert.

## <a name="rtsp"></a>RTSP

[RTSP](https://tools.ietf.org/html/rfc2326) ist die Abkürzung für Real-Time Streaming Protocol (Echtzeit-Streamingprotokoll). Es handelt sich um ein Protokoll auf Anwendungsebene, mit dem die Übermittlung von Daten mit Echtzeiteigenschaften gesteuert werden kann. RTSP bietet ein erweiterbares Framework, um die kontrollierte, bedarfsgesteuerte Bereitstellung von Echtzeitdaten wie z. B. Audio- und Videodaten zu ermöglichen. 

## <a name="streaming"></a>Streaming

Wenn Sie sich ein Video von Diensten wie Netflix, YouTube oder anderen auf einem mobilen Gerät angesehen haben, handelte es sich um ein Streamingvideo. Die Wiedergabe beginnt kurz nach dem Auswählen von „Wiedergeben“ (sofern Sie über ausreichende Bandbreite verfügen), und Sie können auf der Zeitachse des Videos vor- und zurückspringen. Beim Streaming besteht die Idee darin, nur den Teil des Videos zu übermitteln, der gerade angesehen wird. Der Benutzer kann die Wiedergabe des Videos beginnen, während die Daten noch von einem Server an den Wiedergabeclient übertragen werden. Im Kontext von Azure Media Services bezieht sich [Streaming](https://en.wikipedia.org/wiki/Streaming_media) auf den Prozess der Übermittlung von Medien aus [Azure Media Services](../azure-media-player/azure-media-player-overview.md) an einen Streamingclient (z. B. Azure Media Player). Sie können Azure Media Services verwenden, um Videos mithilfe von Branchenstandards für HTTP-basierte Medienstreamingprotokolle wie [HTTP Live Streaming (HLS)](https://developer.apple.com/streaming/) und [MPEG-DASH](https://dashif.org/about/) an Clients zu streamen. HLS wird von Azure Media Player und Webplayern wie [JW-Player](https://www.jwplayer.com/), [hls.js](https://github.com/video-dev/hls.js/), [VideoJS](https://videojs.com/) und [Shaka Player von Google](https://github.com/google/shaka-player) unterstützt. Sie können aber auch das native Rendering in mobilen Apps wie dem [ExoPlayer](https://github.com/google/ExoPlayer) für Android oder [AV Foundation](https://developer.apple.com/av-foundation/) für iOS nutzen. MPEG-DASH wird von Azure Media Player ebenfalls unterstützt. Eine Liste mit Clients finden Sie auf [dieser Seite](https://dashif.org/members/). 

Wenn Sie [Mediendiagramme](#media-graph) verwenden, um Videos in einem Medienobjekt in Azure Media Services aufzuzeichnen, können Sie mithilfe der Streamingfunktionen von Media Services Videostreams in HLS und DASH bereitstellen. Weitere Informationen hierzu finden Sie im Artikel [Videowiedergabe](video-playback-concept.md).

## <a name="vms"></a>VMS

[VMS](https://en.wikipedia.org/wiki/Video_management_system) ist die Abkürzung für Video Management System (Videoverwaltungssystem). Solche Systeme werden zum Konfigurieren und Steuern von Überwachungskameras (CCTV) und zum Aufzeichnen von Videos von diesen verwendet. Außerdem stellen diese Systeme Clientanwendungen bereit, um die aufgezeichneten Videos wiederzugeben.

## <a name="next-steps"></a>Nächste Schritte

[Mediendiagramme](media-graph-concept.md)
