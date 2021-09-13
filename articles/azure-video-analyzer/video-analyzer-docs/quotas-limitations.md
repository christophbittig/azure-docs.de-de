---
title: Azure Video Analyzer-Kontingente und -Einschränkungen – Azure
description: Dieser Artikel beschreibt die Kontingente und Einschränkungen von Azure Video Analyzer.
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: a94ebd36728519b7ae73d9cc48c82097dcfdbb21
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/23/2021
ms.locfileid: "114602083"
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

Azure Video Analyzer unterstützt das RTSP nur mit [ineinander verzahnten RTP-Streams]( https://datatracker.ietf.org/doc/html/rfc2326#section-10.12). In diesem Modus wird RTP-Datenverkehr über die RTSP-TCP-Verbindung getunnelt. RTP-Datenverkehr über UDP wird nicht unterstützt.

### <a name="support-for-video-ai"></a>Unterstützung für Video-KI
Die Erweiterungsprozessoren HTTP oder gRPC unterstützen nur das Senden von Bild-/Videobilddaten mit einem externen KI-Modul. Aufgrund dessen wird das Rückschließen auf Audiodaten nicht unterstützt. Daher verwenden Prozessorknoten in Pipeline-Topologien, zu denen ein RTSP-Quellknoten als `inputs` gehört, ebenfalls eine `outputSelectors`-Eigenschaft, um sicherzustellen, dass nur Video in den Prozessor geleitet wird. Sehen Sie sich diese [Topologie](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/evr-grpcExtension-video-sink/topology.json) als Beispiel an.

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
