---
title: Fortlaufende Videoaufzeichnung aus dem Edgebereich – Azure Video Analyzer
description: Fortlaufende Videoaufzeichnung (Continuous Video Recording, CVR) bezeichnet die kontinuierliche Erfassung von einer Videoquelle. In diesem Thema wird erläutert, was CVR ist und wie sie mit Azure Video Analyzer verwendet wird.
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7d737536cca3e014b0412a00a99ce034f6d28e36
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131556557"
---
# <a name="continuous-video-recording"></a>Fortlaufende Videoaufzeichnung    

Fortlaufende Videoaufzeichnung (Continuous Video Recording, CVR) bezeichnet die kontinuierliche Erfassung von Videodaten von einer Videoquelle. Azure Video Analyzer unterstützt auf 24x7-Basis die kontinuierliche Aufzeichnung von Videos einer Überwachungskamera (CCTV) über eine videoverarbeitende [Pipelinetopologie](pipeline.md), die aus einem RTSP-Quellknoten und einem Senkenknoten für Medienobjekte besteht. Nachfolgend sehen Sie eine grafische Darstellung eines solchen Mediendiagramms. Die JSON-Darstellung der Topologie finden Sie in diesem [Dokument](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/cvr-video-sink/topology.json). Sie können eine solche Topologie verwenden, um beliebig lange Aufzeichnungen (sich über Jahre erstreckende Inhalte) zu erstellen. Die Zeitstempel für die Aufzeichnungen werden in UTC gespeichert.  

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/continuous-video-recording/continuous-video-recording-overview.svg" alt-text="Fortlaufende Videoaufzeichnung":::

Eine Instanz der oben dargestellten Pipelinetopologie kann auf einem Edgegerät im Video Analyzer-Dienst ausgeführt werden, wobei die Videosenke in einer [Videoressource](terminology.md#video)aufgezeichnet wird. Das Video wird so lange aufgezeichnet, wie die Pipeline im aktivierten Zustand bleibt. Aufgezeichnete Videos können mithilfe der Streamingfunktionen des Video Analyzer wiedergespielt werden. Weitere Informationen finden Sie unter [Wiedergabe von Videoaufzeichnungen](playback-recordings-how-to.md).

## <a name="suggested-pre-reading"></a>Empfohlene Lektüre zur Vorbereitung  

Es empfiehlt sich, die folgenden Artikel zu lesen, bevor Sie beginnen:

* [Konzept der Pipelinetopologie](pipeline.md)
* [Videoaufzeichnung: Konzepte](video-recording.md) 
 
## <a name="resilient-recording"></a>Resiliente Aufzeichnung

Das Video Analyzer-Edgemodul unterstützt den Betrieb unter Bedingungen, bei denen die Konnektivität zwischen Edgegerät und Cloud möglicherweise gelegentlich getrennt wird oder die verfügbare Bandbreite sinkt. Dazu wird das Video von der Quelle lokal in einem Cache aufgezeichnet und regelmäßig automatisch mit der Videoressource synchronisiert. Wenn Sie die [Pipelinetopologie](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/cvr-video-sink/topology.json) untersuchen, erkennen Sie, dass die folgenden Eigenschaften definiert wurden:

```
"segmentLength": "PT30S",
"localMediaCacheMaximumSizeMiB": "2048",
"localMediaCachePath": "/var/lib/videoanalyzer/tmp/",
```

Die letzteren beiden Eigenschaften sind für die resiliente Aufzeichnung relevant (beide Eigenschaften sind auch für einen Videosenkenknoten für Medienobjekte erforderlich). Die `localMediaCachePath`-Eigenschaft weist die Medienobjektsenke an, diesen Ordnerpfad zum Zwischenspeichern von Mediendaten vor dem Hochladen in die Cloud zu verwenden. In [diesem Artikel](../../iot-edge/how-to-access-host-storage-from-module.md) wird erläutert, wie das Edgemodul den lokalen Speicher Ihres Geräts verwenden kann. Die `localMediaCacheMaximumSizeMiB`-Eigenschaft definiert, wie viel Speicherplatz die Videosenke als Cache verwenden kann (1 MiB = 1024 × 1024 Byte). 

Wenn die Verbindung Ihres Edgemoduls für einen sehr langen Zeitraum getrennt wird und der im Cacheordner gespeicherte Inhalt den `localMediaCacheMaximumSizeMiB`-Wert erreicht, beginnt die Medienobjektsenke damit, Daten aus dem Cache zu entfernen. Sie beginnt dabei mit den ältesten Daten. Wenn die Geräteverbindung z. B. um 10:00 Uhr verloren gegangen ist und der Cache den maximalen Grenzwert um 18:00 Uhr erreicht, beginnt die Videosenke mit dem Löschen bei den Daten, die um 10:00 Uhr aufgezeichnet wurden. 

Wenn die Netzwerkverbindung wiederhergestellt wird, beginnt die Medienobjektsenke erneut, Daten aus dem Cache hochzuladen, beginnend mit den ältesten Daten. Nehmen Sie ausgehend vom obigen Beispiel an, dass zum Zeitpunkt der Wiederherstellung der Verbindung bereits 5 Minuten an Videodaten aus dem Cache entfernt werden mussten (z. B. um 18:02 Uhr). In diesem Fall beginnt die Videosenke ab der Markierung um 10:05 Uhr wieder mit dem Hochladen.

Lücken in Aufzeichnungen können auch auftreten, z. B. wenn Sie Pipelines aus einem beliebigen Grund neu starten. Sie können eine Pipeline auch beenden und zu einem späteren Zeitpunkt neu starten. Solange sich die Kameraeinstellungen nicht ändern, können Sie weiterhin die gleiche Video Analyzer-Videoressource aufzeichnen.

## <a name="segmented-recording"></a>Segmentierte Aufzeichnung  

Mit der oben gezeigten `segmentLength`-Eigenschaft können Sie die Kosten für Schreibtransaktionen steuern, die mit dem Schreiben von Daten in Ihr Speicherkonto verbunden sind, in dem die Videoressource aufgezeichnet wird. Wenn Sie beispielsweise den Wert von 30 Sekunden auf 5 Minuten erhöhen, fällt die Anzahl der Speichertransaktionen um den Faktor 10 (5 × 60 / 30).

Die Eigenschaft `segmentLength` stellt sicher, dass Videos höchstens einmal pro `segmentLength` Sekunden in das Speicherkonto geschrieben werden. Der Mindestwert dieser Eigenschaft beträgt 30 Sekunden (dies ist auch der Standardwert). Der Wert kann in 30-Sekunden-Schritten auf maximal 5 Minuten erhöht werden.

Diese Eigenschaft gilt sowohl für das Video Analyzer-Edgemodul als auch für den Video Analyzer-Dienst. Im Artikel [Wiedergeben von Videoaufzeichnungen](playback-recordings-how-to.md) finden Sie Informationen zu den Auswirkungen, die `segmentLength` auf das Wiedergeben hat.

## <a name="see-also"></a>Weitere Informationen

* [Ereignisbasierte Videoaufzeichnung](event-based-video-recording-concept.md) 
* [Wiedergeben von Aufzeichnungen](playback-recordings-how-to.md) 

## <a name="next-steps"></a>Nächste Schritte

[Tutorial zur fortlaufenden Videoaufzeichnung](edge/use-continuous-video-recording.md) 
