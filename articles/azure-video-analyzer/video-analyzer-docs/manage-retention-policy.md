---
title: Verwalten der Aufzeichnungsrichtlinie mit Azure Video Analyzer
description: In diesem Thema wird erklärt, wie Sie die Aufzeichnungsrichtlinie mit Azure Video Analyzer verwalten können.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0bb6ad0f3b9cb5ddea876969da3b6429e2f2ba32
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131849385"
---
# <a name="manage-retention-policy-with-video-analyzer"></a>Verwalten der Aufzeichnungsrichtlinie mit Azure Video Analyzer

Sie können Azure Video Analyzer verwenden, um Inhalte über einen längeren Zeitraum zu archivieren, der von wenigen Sekunden bis hin zu Videoinhalten aus einer einzelnen Quelle reicht. Sie können die Aufbewahrungsrichtlinie Für Ihre Inhalte explizit steuern, wodurch sichergestellt wird, dass ältere Inhalte regelmäßig gekürzt werden. Sie können unterschiedliche Richtlinien auf verschiedene Archive anwenden. Beispielsweise können Sie die aufzeichnungen der letzten 3 Tage von einer Kamera auf dem Parkplatz und die letzten 30 Tage der Aufzeichnungen von der Kamera hinter dem Schalter des Kassierers aufbewahren.

## <a name="retention-period"></a>Beibehaltungsdauer

Für jede [Videoressource](terminology.md#video) in Ihrem Video Analyzer-Konto können Sie optional eine Aufbewahrungsrichtlinie anwenden. Wenn Sie einen Aufbewahrungszeitraum angeben (wie unten beschrieben), kürzet der Dienst regelmäßig Inhalte, die älter als dieser Zeitraum sind. Wenn Sie keine Richtlinie angeben, werden Inhalte unbegrenzt gespeichert.

Der Aufbewahrungszeitraum wird in der Regel in den Eigenschaften eines Videosenkenknotens festgelegt, wenn eine Pipelinetopologie erstellt wird. Sie finden die `retentionPeriod` -Eigenschaft im `videoCreationProperties` Abschnitt. Wie der Name schon sagt, wird die Eigenschaft verwendet, wenn eine Livepipeline mit dieser Topologie aktiviert wird und eine neue Videoressource erstellt wird. Diese Erstellungseigenschaften werden nicht verwendet, wenn die Videoressource bereits vorhanden ist (z. B. wenn eine Pipeline reaktiviert wird). Im Folgenden finden Sie ein Beispiel dafür, wie eine 3-Tage-Aufbewahrungsrichtlinie auf alle Kameras des Parkraums angewendet werden kann.

```
{
  "@type": "#Microsoft.VideoAnalyzer.VideoSink",
  "name": "{nodeName}",         
  "videoName": "{nameForVideoResource}",
  "videoCreationProperties":
  {
    "title": "{titleForVideo}",
    "description": "{descriptionForVideo}",
    "segmentLength": "PT30S",
    "retentionPeriod": "P3D"
  },
}
```

Sie können auch die `retentionPeriod` -Eigenschaft einer Videoressource festlegen oder aktualisieren, indem Sie Azure-Portal oder die [REST-API](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/resource-manager/Microsoft.Media/preview/2021-11-01-preview/Videos.json) verwenden. Im Folgenden finden Sie ein Beispiel für das Festlegen einer 3-Tage-Aufbewahrungsrichtlinie.

```
"archival":
{
   "retentionPeriod": "P3D",
},
```

## <a name="rules-and-limitations"></a>Regeln und Einschränkungen

* Es kann bis zu 24 Stunden dauern, bis die Aufbewahrungsrichtlinie wirksam wird.
* Format:  Die Eigenschaft nimmt den Wert der ISO 8601-Dauer an, der mehrere Tage lang sein muss. Zulässige Beispielwerte: P1D, P20D, P1M, P365D, P1Y, P5Y
    * Der Mindestwert ist P1D, und der Höchstwert ist P5Y.
    * Wenn diese Einstellung auf Null festgelegt ist, wird die Aufbewahrungsrichtlinie gelöscht, und der Videoinhalt wird unbegrenzt gespeichert.
* Wenn die Aufbewahrungsrichtlinie verwendet wird, fallen zusätzliche Azure Storage-Transaktionskosten an, wenn der Dienst Blobs sucht und löscht.

## <a name="next-steps"></a>Nächste Schritte

[Wiedergeben von Aufzeichnungen](playback-recordings-how-to.md)
