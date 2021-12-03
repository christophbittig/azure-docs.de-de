---
title: Azure Video Analyzer-Kontingente und -Einschränkungen – Azure
description: Dieser Artikel beschreibt die Kontingente und Einschränkungen von Azure Video Analyzer.
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: cad4ed3ffac3d9b8617150070579bd9bee5ad6b3
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563129"
---
# <a name="video-analyzer-quotas-and-limitations"></a>Video Analyzer-Kontingente und -Einschränkungen

Dieser Artikel beschreibt die Kontingente und Einschränkungen von Azure Video Analyzer.

## <a name="quotas-and-limitations---edge-module"></a>Kontingente und Einschränkungen – Edge-Modul

In diesem Abschnitt werden die Kontingente und Einschränkungen des Azure Video Analyzer-Edge-Moduls gelistet.

### <a name="maximum-period-of-disconnected-use"></a>Maximaler Verwendungszeitraum bei getrennter Verbindung

Das Edgemodul kann bei einem zeitlich begrenzten Verlust der Internetverbindung fortgesetzt werden. Wenn das Modul länger als 36 Stunden nicht angeschlossen ist, deaktiviert es alle aktiven Pipelines, die ausgeführt wurden. Alle weiteren direkten Methodenaufrufe werden blockiert.

Um das Edge-Modul wieder in einen betriebsbereiten Zustand zu versetzen, müssen Sie die Internetkonnektivität wiederherstellen, damit das Modul erfolgreich mit dem Azure Video Analyzer-Konto kommunizieren kann.

### <a name="maximum-number-of-live-pipelines"></a>Maximale Anzahl von aktiven Pipelines

Es werden maximal 1000 Live-Pipelines pro Modul (erstellt mit `livePipelineSet`) unterstützt.

### <a name="maximum-number-of-pipeline-topologies"></a>Maximale Anzahl von Pipeline-Topologien

Es werden maximal 50 Pipeline-Topologien pro Modul (erstellt mit `pipelineTopologySet` ) unterstützt.

### <a name="limitations-on-pipeline-topologies"></a>Beschränkungen von Pipeline-Topologien

Im Folgenden sind die Einschränkungen aufgeführt, wie verschiedene Knoten in einer Pipeline-Topologie miteinander verbunden werden können.

* RTSP-Quelle
   * Es ist nur eine RTSP-Quelle pro Pipeline-Topologie zulässig.
* Verarbeitungsknoten für die Bewegungserkennung
   * Muss direkt hinter dem RTSP-Quellknoten angeordnet sein.
   * Kann nicht im Anschluss an einen HTTP- oder gRPC-Erweiterungsprozessor verwendet werden.
* Signalgateprozessor
   * Muss direkt hinter dem RTSP-Quellknoten angeordnet sein.
   * Darf nicht vor einem HTTP- oder gRPC-Erweiterungsprozessor angeordnet sein.
* Objekt-Tracker-Prozessor
   * Muss einem HTTP- oder einem gRPC-Erweiterungsprozessor unmittelbar nachgeschaltet sein. Der Erweiterungsprozessor sollte nicht auf jede Abbildung des Live-Videos ein KI-Modell anwenden.
* Linienüberquerungs-Prozessor
   * Muss einem Objekt-Tracker-Prozessor unmittelbar nachgeschaltet sein.
* Videosenke 
   * Muss direkt hinter dem RTSP-Quellknoten oder Signalgateprozessor angeordnet sein.
* Dateisenke
   * Muss direkt hinter dem Signalgateprozessor angeordnet sein.
   * Kann nicht unmittelbar einem HTTP- oder einem gRPC-Erweiterungsprozessor oder einem Bewegungserkennungsprozessor nachgeschaltet sein.
* IoT Hub-Senke
   * Kann nicht unmittelbar einer IoT-Hub-Quelle nachgeschaltet sein.

### <a name="supported-cameras"></a>Unterstützte Kameras
Es können nur IP-Kameras verwendet werden, die das RTSP-Protokoll unterstützen. IP-Kameras, die RTSP unterstützen, finden Sie auf der [Seite mit den ONVIF-konformen Produkten](https://www.onvif.org/conformant-products). Suchen Sie nach Geräten, die mit den Profilen G, S oder T konform sind.

Sie sollten diese Kameras so konfigurieren, dass H.264-Videos und AAC-Audios verwendet werden. Andere Codecs werden zurzeit nicht unterstützt.

Azure Video Analyzer unterstützt das RTSP nur mit [ineinander verzahnten RTP-Streams](https://datatracker.ietf.org/doc/html/rfc2326#section-10.12). In diesem Modus wird RTP-Datenverkehr über die RTSP-TCP-Verbindung getunnelt. RTP-Datenverkehr über UDP wird nicht unterstützt. 

### <a name="support-for-video-ai"></a>Unterstützung für Video-KI
Die Erweiterungsprozessoren HTTP oder gRPC unterstützen nur das Senden von Bild-/Videobilddaten mit einem externen KI-Modul. Aufgrund dessen wird das Rückschließen auf Audiodaten nicht unterstützt. Daher verwenden Prozessorknoten in Pipeline-Topologien, zu denen ein RTSP-Quellknoten als `inputs` gehört, ebenfalls eine `outputSelectors`-Eigenschaft, um sicherzustellen, dass nur Video in den Prozessor geleitet wird. Sehen Sie sich diese [Topologie](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/evr-grpcExtension-video-sink/topology.json) als Beispiel an.

## <a name="quotas-and-limitations---live-and-batch-pipeline"></a>Kontingente und Einschränkungen – Live- und Batch-Pipeline

In diesem Abschnitt werden die Kontingente und Einschränkungen der Azure Video Analyzer-Cloud Pipelines aufgeführt. 

### <a name="maximum-number-of-pipeline-topologies"></a>Maximale Anzahl von Pipeline-Topologien 

Pro Video Analyzer-Konto werden höchstens 5 Pipeline-Topologien unterstützt. 

### <a name="limits-on-concurrent-activation-of-live-pipelines"></a>Grenzwerte für die gleichzeitige Aktivierung von Live-Pipelines 

Maximal 10 Live-Pipelines können über ein Zeitfenster von 5 Minuten aktiviert werden, und höchstens 10 solcher Pipelines können sich zu einem bestimmten Zeitpunkt in einem `Activating` Zustand befinden.

Diese Grenzwerte gelten nicht für Pipelineaufträge.  

### <a name="maximum-live-pipelines-per-topology"></a>Maximale Anzahl von Live-Pipelines pro Topologie  

Es werden höchstens 50 Live-Pipelines pro Topologie unterstützt.  

### <a name="concurrent-low-latency-streaming-sessions"></a>Gleichzeitige Streamingsitzungen mit geringer Latenz  

Für jede aktive Live-Pipeline kann höchstens eine Clientanwendung den [Stream mit geringer Latenz](playback-recordings-how-to.md#low-latency-streaming) anzeigen. Wenn ein anderer Client versucht, eine Verbindung herzustellen, wird die Anforderung abgelehnt.  

### <a name="limitations-on-designing-pipeline-topologies"></a>Einschränkungen beim Entwerfen von Pipeline-Topologien 

Nachfolgend sind die verschiedenen Knoten aufgeführt, die in einer Pipeline-Topologie verbunden werden können, sowie deren Einschränkungen: 

* RTSP-Quelle
   * Es ist nur eine RTSP-Quelle pro Pipeline-Topologie zulässig.
* Videoquelle
   * Es ist nur eine Videoquelle pro Pipeline-Topologie zulässig.
   * Kann nur in Pipeline-Topologien der [Batchart](pipeline.md#batch-pipeline) verwendet werden. Sie kann nur eine Videoressource vom Typ `archive` akzeptieren. 
* Encoderprozessor 
   * Pro Pipeline-Topologie ist nur ein Encoderprozessor zulässig.
   * Kann nur in Pipelinetopologien der [Batchart](pipeline.md#batch-pipeline) verwendet werden und muss sich in solchen Topologien unmittelbar vor dem Knoten der Videovideosenke befinden.
   * Es unterstützt nur das Codieren von Videos mit dem H.264-Codec und Audio mit dem AAC-Codec.
   * Ermöglicht dem Benutzer das Angeben von Codierungseigenschaften beim Konvertieren des aufgezeichneten Videos in das gewünschte Format für die Downstreamverarbeitung. Zwei Typen: (a) Systemvoreinstellung (b) Benutzerdefinierte Voreinstellung. Die zulässigen Konfigurationen für jede Voreinstellung sind in der folgenden Tabelle aufgeführt: 
     
     | Konfiguration       | Systemvoreinstellung        | Benutzerdefinierte Voreinstellung |
     | -----------         | -----------          |----------- |
     | Bitrate-KBit/s des Videoencoders      | identisch mit der Quelle      | 200 bis 16.000 KBit/s |
     | Bildfrequenz       | identisch mit der Quelle      | 0 bis 300 |
     | Höhe    | identisch mit der Quelle        | 1 bis 4320 |
     | Breite    | identisch mit der Quelle       | 1 bis 8192 |
     | Mode   | Pad        | Pad, PreserveAspectRatio, Stretch |     
     | Audioencoderbitraten-KBit/s  | identisch mit der Quelle        | Zulässige Werte: 96, 112, 128, 160, 192, 224, 256 |
     
* Videosenke 
   *  Es ist nur eine Videosenke pro Pipeline-Topologie zulässig.
   *  Muss direkt hinter der RTSP-Quelle oder Encoderprozessorknoten angeordnet sein. 
   *  Bei Verwendung in einer Pipelinetopologie der Batchart wird eine MP4-Datei als Ausgabe erzeugt.

### <a name="support-for-batch-video-export"></a>Unterstützung für den Batchvideoexport 

* Segmentauswahl 
   * Die Zeitsequenz, d. h. der Start- und Endzeitstempel des zu exportierenden Teils des archivierten Videos, sollte in UTC-Zeit angegeben werden. 
   * Die maximale Zeitspanne der Zeitsequenz (Endzeitstempel – Startzeitstempel) muss kleiner oder gleich 24 Stunden sein. 

### <a name="supported-cameras"></a>Unterstützte Kameras

Es können nur IP-Kameras verwendet werden, die das RTSP-Protokoll unterstützen. IP-Kameras, die RTSP unterstützen, finden Sie auf der [Seite mit den ONVIF-konformen Produkten](https://www.onvif.org/conformant-products). Suchen Sie nach Geräten, die mit den Profilen G, S oder T konform sind. 

Sie sollten diese Kameras so konfigurieren, dass H.264-Videos und AAC-Audios verwendet werden. Andere Codecs werden zurzeit nicht unterstützt. Die Bitrate der Videocodierung muss zwischen 500 und 3.000 KBit/s betragen. Wenn die tatsächliche Erfassungsbitrate diesen Schwellenwert überschreitet, wird die Erfassung getrennt und mit exponentiellen Backoffs erneut verbunden.

Azure Video Analyzer unterstützt das RTSP nur mit [ineinander verzahnten RTP-Streams](https://datatracker.ietf.org/doc/html/rfc2326#section-10.12). In diesem Modus wird RTP-Datenverkehr über die RTSP-TCP-Verbindung getunnelt. RTP-Datenverkehr über UDP wird nicht unterstützt. 

### <a name="support-for-video-ai"></a>Unterstützung für Video-KI 

Die Analyse von Livevideos oder aufgezeichneten Videos wird derzeit nicht unterstützt.

## <a name="quotas-and-limitations---service"></a>Kontingente und Einschränkungen – Service

In diesem Abschnitt werden die Kontingente und Einschränkungen des Azure Video Analyzer-Kontos aufgeführt.

### <a name="limitations-on-service-operations-at-preview"></a>Einschränkungen des Servicebetriebs bei der Vorschau

Zum Zeitpunkt der Vorschauversion unterstützt der Azure Video Analyzer-Dienst Folgendes nicht:

* Die Möglichkeit, das Konto ohne Unterbrechung von einem Abonnement zu einem anderen zu migrieren.
* Die Möglichkeit, mehr als ein Speicherkonto mit dem Konto zu verwenden.
* Die Möglichkeit, ein Speicherkonto in einer anderen Region als das Azure Video Analyzer-Konto zu verwenden.

### <a name="limits-on-video-resources"></a>Einschränkungen der Video-Ressourcen
Zum Zeitpunkt der Vorschauversion kann jedes Azure Video Analyzer-Konto über maximal 10.000 Videoressourcen verfügen. Wenn Sie ein höheres Limit benötigen, öffnen Sie im Azure-Portal ein Supportticket.

### <a name="limits-on-access-policies"></a>Beschränkungen der Zugriffsrichtlinien
Zum Zeitpunkt der Vorschauversion kann jedes Azure Video Analyzer-Konto über maximal 20 Zugriffsrichtlinien verfügen.

### <a name="limits-on-registered-edge-modules"></a>Einschränkungen auf registrierte Edge-Module
Zum Zeitpunkt der Vorschauveröffentlichung kann jedes Azure Video Analyzer-Konto maximal 1.000 Edge-Module registriert haben. Wenn Sie einen geschäftlichen Bedarf für ein höheres Limit haben, öffnen Sie ein Support-Ticket im Azure-Portal.

### <a name="limits-on-client-api-calls"></a>Einschränkungen für Client-API-Aufrufe
Die folgenden Einschränkungen gelten für die Client-APIs in der Vorschauversion:

* Bis zu 50 Anforderungen innerhalb von 10 Sekunden
* Bis zu 600 Anforderungen pro Sekunde

## <a name="limitations---video-analyzer-player-widgets"></a>Einschränkungen – Azure Video Analyzer-Player-Widgets

Zum Zeitpunkt der Vorschauversion wird die Wiedergabe auf iOS-Geräten nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

[Übersicht](overview.md)
