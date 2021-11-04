---
title: Aufzeichnen von Videos für die Wiedergabe mit Azure Video Analyzer
description: In diesem Artikel wird die Aufzeichnung von Videos für die Wiedergabe mit Azure Video Analyzer erläutert.
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 253db9fe0ad067f00647cb195995eaee4951a38a
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563946"
---
# <a name="record-video-for-playback"></a>Aufzeichnen von Video für die Wiedergabe

Im Kontext eines Videoverwaltungssystems für Überwachungskameras bezieht sich Videoaufzeichnung auf den Vorgang des Erfassens von Videosignalen von den Kameras und ihre Aufzeichnung für die anschließende Wiedergabe über mobile und Browser-Apps. Die Videoaufzeichnung kann in fortlaufende Videoaufzeichnung und ereignisbasierte Videoaufzeichnung kategorisiert werden.

## <a name="continuous-video-recording"></a>Fortlaufende Videoaufzeichnung

Fortlaufende Videoaufzeichnung (Continuous Video Recording, CVR) bezeichnet die kontinuierliche Aufzeichnung der gesamten von einer Videoquelle erfassten Videodaten. CVR stellt sicher, dass die gewünschte Dauer des Videos (vorgeschrieben durch die **[Aufbewahrungsrichtlinie](#retention-policy))** zur Analyse und/oder Prüfung zu einem späteren Zeitpunkt verfügbar ist.

## <a name="event-based-video-recording"></a>Ereignisbasierte Videoaufzeichnung

Ereignisbasierte Videoaufzeichnung (EVR) bezeichnet den Vorgang des Aufzeichnen von Videos mit Auslösung durch ein Ereignis. Das betreffende Ereignis kann bei der Verarbeitung des Videosignals selbst (z. B. Erkennung einer Bewegung) entstehen oder von einer unabhängigen Quelle (z. B. von einem Türsensor) stammen. EVR kann zu Speichereinsparung führen, erfordert jedoch zusätzliche Komponenten, die die Ereignisse generieren und die Videoaufzeichnung auslösen. Mit anderen Worten ist für EVR das Treffen zusätzlicher Entscheidungen bezüglich der Ereignisse, die eine Videoaufzeichnung auslösen sollen, und der Länge der Videoaufzeichnung erforderlich. Ein Beispiel könnte etwa lauten: Zeichne das Videosignal 2 Minuten lang auf, beginnend 30 Sekunden vor der Ereigniszeit.

## <a name="choosing-recording-modes"></a>Auswählen von Aufzeichnungsmodi

Die Entscheidung, ob CVR oder EVR verwendet werden soll, hängt von den Geschäftszielen ab. Azure Video Analyzer (AVA) bietet Plattformfunktionen sowohl für CVR als auch für EVR. Mehr dazu erfahren Sie in den Szenarioartikeln zu **[CVR](continuous-video-recording.md)** und **[EVR](event-based-video-recording-concept.md)** .

## <a name="retention-policy"></a>Aufbewahrungsrichtlinie

Die Aufbewahrungsrichtlinie bezieht sich auf die Richtlinien, die die Länge oder Dauer der aufbewahrten Videoaufzeichnung bestimmen. Die Aufbewahrungsrichtlinien ermöglichen es Ihnen, die Speicherkosten mit den Geschäftsanforderungen abzugleichen. Für das CVR-Szenario definiert die Aufbewahrungsrichtlinie, wie viele Tage das Video gespeichert werden soll (z.B. die letzten 7 Tage). Weitere Informationen dazu finden Sie im Artikel **[Aufbewahrungsrichtlinien verwalten](manage-retention-policy.md)** .

## <a name="next-steps"></a>Nächste Schritte

- [Informieren Sie sich über EVR-Szenarien.](event-based-video-recording-concept.md)
- [Informieren Sie sich über CVR-Szenarien.](continuous-video-recording.md)
