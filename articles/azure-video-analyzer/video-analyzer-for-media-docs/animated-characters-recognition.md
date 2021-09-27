---
title: Erkennung animierter Figuren mit Azure Video Analyzer for Media (vormals Video Indexer)
description: In diesem Artikel wird die Erkennung animierter Figuren mit Azure Video Analyzer for Media (vormals Video Indexer) veranschaulicht.
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: d12b7a1911bb0c63d7f546a8b7a30e0287a05759
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128610596"
---
# <a name="animated-character-detection-preview"></a>Erkennung animierter Figuren (Vorschauversion)

Azure Video Analyzer for Media (vormals Video Indexer) unterstützt die Erkennung und Gruppierung von Figuren in animierten Inhalten mittels Integration von [Cognitive Services: Custom Vision](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/). Diese Funktion steht sowohl über das Portal als auch über die API zur Verfügung.

Nach dem Hochladen eines animierten Videos mit einem bestimmten Animationsmodell extrahiert Analyzer for Media Keyframes, erkennt animierte Figuren in diesen Frames, gruppiert ähnliche Figuren und wählt das beste Beispiel aus. Anschließend werden die gruppierten Figuren an Custom Vision gesendet, um sie auf der Grundlage der trainierten Modelle zu identifizieren. 

Wenn Ihr Modell noch nicht trainiert wurde, werden die Figuren namenlos erkannt. Wenn Sie Namen hinzufügen und das Modell trainieren, erkennt Analyzer for Media die Figuren und benennt sie entsprechend.

## <a name="flow-diagram"></a>Flussdiagramm

Das folgende Diagramm zeigt den Ablauf des Erkennungsprozesses für animierte Figuren:

![Flussdiagramm](./media/animated-characters-recognition/flow.png)

## <a name="accounts"></a>Konten

Abhängig von der Art Ihres Analyzer for Media-Kontos stehen unterschiedliche Features zur Verfügung. Unter [Erstellen eines Video Analyzer for Media-Kontos mit Azure-Verbindung](connect-to-azure.md) erfahren Sie, wie Sie Ihr Konto mit Azure verbinden.

* Testkonto: Video Analyzer for Media verwendet ein internes Custom Vision-Konto, um ein Modell zu erstellen und mit Ihrem Video Analyzer for Media-Konto zu verknüpfen. 
* Kostenpflichtiges Konto: Sie verbinden Ihr Custom Vision-Konto mit Ihrem Video Analyzer for Media-Konto. (Sollten Sie noch nicht über ein Konto verfügen, müssen Sie erst ein Konto erstellen).

### <a name="trial-vs-paid"></a>Gegenüberstellung von Testkonto und kostenpflichtigem Konto

|Funktionalität|Testversion|Kostenpflichtig|
|---|---|---|
|Custom Vision-Konto|Dieses wird im Hintergrund von Video Analyzer for Media verwaltet. |Ihr Custom Vision-Konto ist mit Video Analyzer for Media verbunden.|
|Anzahl von Animationsmodellen|Eine|Bis zu 100 Modelle pro Konto (Custom Vision-Einschränkung)|
|Training des Modells|Video Analyzer for Media trainiert das Modell für neue Figuren anhand von zusätzlichen Beispielen vorhandener Figuren.|Der Kontobesitzer trainiert das Modell, wenn er bereit ist, Änderungen vorzunehmen.|
|Erweiterte Optionen in Custom Vision|Kein Zugriff auf das Custom Vision-Portal|Sie können die Modelle selbst im Custom Vision-Portal anpassen.|

## <a name="use-the-animated-character-detection-with-portal--and-api"></a>Verwenden der Erkennung animierter Figuren über das Portal und die API

Weitere Informationen finden Sie unter [Verwenden der Erkennung animierter Figuren über das Portal und die API](animated-characters-recognition-how-to.md).

## <a name="limitations"></a>Einschränkungen

* Die Animationserkennung wird momentan in der Region „Asien, Osten“ nicht unterstützt.
* Kleine oder weit entfernte Figuren werden in Videos mit schlechter Qualität möglicherweise nicht ordnungsgemäß erkannt.
* Es wird empfohlen, für jede Gruppe von animierten Figuren (beispielsweise pro animierter Serie) ein eigenes Modell zu verwenden.

## <a name="next-steps"></a>Nächste Schritte

[Video Analyzer for Media: Übersicht](video-indexer-overview.md)
