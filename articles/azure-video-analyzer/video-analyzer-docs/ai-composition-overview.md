---
title: KI-Kompositionen in Azure Video Analyzer
description: Dieser Artikel bietet eine allgemeine Übersicht über die Unterstützung von Azure Video Analyzer für drei Arten von KI-Kompositionen. Das Thema bietet auch Erläuterungen zu Szenarien für jede Art von KI-Komposition.
ms.topic: conceptual
ms.date: 11/04/2021
ms.openlocfilehash: 4195fd0dbcd44f0eeb273384e6d6cfad352aedb5
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131847107"
---
# <a name="ai-composition"></a>KI-Komposition

Dieser Artikel bietet eine allgemeine Übersicht über die Unterstützung von Azure Video Analyzer für drei Arten von KI-Kompositionen. 

* [Sequenziell](#sequential-ai-composition)
* [Parallel](#parallel-ai-composition)
* [Kombiniert](#combined-ai-composition)

## <a name="sequential-ai-composition"></a>Sequenzielle KI-Komposition

KI-Knoten können sequenziell zusammengesetzt werden. Auf diese Weise kann ein Downstreamknoten die von einem Upstreamknoten generierten Rückschlüsse ergänzen.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/ai-composition/sequential.svg" alt-text="Sequenzielle KI-Komposition":::
 
### <a name="key-aspects"></a>Schlüsselaspekte

* Pipelineerweiterungen fungieren als Medienpassthroughknoten und können so konfiguriert werden, dass externe KI-Server Frames mit unterschiedlichen Raten, Formaten und Auflösungen empfangen. Zusätzlich kann die Konfiguration so festgelegt werden, dass externe KI-Server alle Frames oder nur die Frames empfangen können, die bereits Rückschlüsse enthalten.
* Rückschlüsse werden zu den Frames hinzugefügt, während sie die verschiedenen Erweiterungsknoten durchlaufen, wobei eine unbegrenzte Anzahl solcher Knoten nacheinander hinzugefügt werden kann.
* Andere Szenarien wie die fortlaufende Videoaufzeichnung oder die ereignisbasierte Videoaufzeichnung können mit der sequenziellen KI-Komposition kombiniert werden.

    
## <a name="parallel-ai-composition"></a>Parallele KI-Komposition

AI-Knoten können auch parallel statt nacheinander zusammengesetzt werden. Auf diese Weise können unabhängige Rückschlüsse auf den aufgenommenen Videostream gezogen werden, wodurch die Erfassungsbandbreite am Edge eingespart wird.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/ai-composition/parallel.svg" alt-text="Parallele KI-Komposition":::
 
### <a name="key-aspects"></a>Schlüsselaspekte

* Das Video kann in eine beliebige Anzahl von parallelen Branches unterteilt werden, und diese Aufteilung kann an jedem beliebigen Punkt nach den folgenden Knoten erfolgen.
    
    * RTSP-Quelle
    * Motion Detector
    * Pipeline-Erweiterung

## <a name="combined-ai-composition"></a>Kombinierte KI-Komposition

Sowohl sequenzielle als auch parallele Kompositionskonstrukte können kombiniert werden, um komplexe zusammensetzbare KI-Pipelines zu entwickeln. Dies ist möglich, da AVA-Pipelines es gestatten, Erweiterungsknoten sequenziell und/oder mit einer parallelen Komposition unbegrenzt mit anderen unterstützten Knoten zu kombinieren.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/ai-composition/complex.svg" alt-text="Kombinierte KI-Komposition":::
 


## <a name="next-steps"></a>Nächste Schritte

[Analysieren von Livevideostreams mit mehreren KI-Modellen mithilfe der KI-Komposition](analyze-ai-composition.md)
