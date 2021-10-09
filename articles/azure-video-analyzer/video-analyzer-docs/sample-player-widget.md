---
title: Das Azure Video Analyzer-Player-Widget-Beispiel
description: Dieser Referenzartikel gibt einen kurzen Überblick über die Beispielanwendung des Video Analyzer Player-Widgets
ms.service: azure-video-analyzer
ms.topic: reference
ms.date: 08/13/2021
ms.openlocfilehash: abea3bdd1098dbb32be35d632c7a8b0ad51fc49e
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/26/2021
ms.locfileid: "129058489"
---
# <a name="azure-video-analyzer-player-widget-sample"></a>Azure Video Analyzer-Player-Widget-Beispiel

Diese Beispielanwendung zeigt die Integration des Player-Widgets von Video Analyzer mit Funktionen für die Videowiedergabe, das Zeichnen von Bereichen und die Erstellung von Videoclips.

* Klonen Sie das [AVA C# Beispiel-Repository](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp)
* Folgen Sie den Anweisungen in der [README im Ordner **„src/video-player“** ](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp/blob/main/src/video-player/README.md)

## <a name="get-started"></a>Erste Schritte
Nachdem Sie die README-Anweisungen befolgt haben und die App auf http://localhost:3000/ ausführen
1. Klicken Sie auf „Videos abrufen“.
2. Wählen Sie ein Video aus der Dropdown-Liste aller Videos in Ihrem Video Analyzer-Konto aus.

## <a name="video-player"></a>Videoplayer
Die Seite Videoplayer zeigt den typischen Player, wie er im Portal zu sehen ist.  
![Screenshot des Videoplayers.](./media/sample-player-widget/widget-video-player.png)

## <a name="zone-drawer"></a>Zonen-Drawer
Mit dem Zonenzeichner können Sie Zonen erstellen, indem Sie Polygone und Linien auf Ihrem Video zeichnen. Sie können diese Zonen und Linien auch speichern, um die Koordinaten Ihrer jeweiligen Zonen und Linien zu erhalten.  
  
**Beispiel:**
```json
  {
    "@type": "#Microsoft.VideoAnalyzer.NamedLineString",
    "name": "Line 1",
    "line": [
      {
        "x": 0.6987951807228916,
        "y": 0.4430992736077482
      },
      {
        "x": 0.6987951807228916,
        "y": 0.7046004842615012
      }
    ]
  }{
    "@type": "#Microsoft.VideoAnalyzer.NamedPolygonString",
    "name": "Zone 2",
    "polygon": [
      {
        "x": 0.10575635876840696,
        "y": 0.7481840193704601
      },
      {
        "x": 0.16599732262382866,
        "y": 0.7796610169491526
      },
      {
        "x": 0.07764390896921017,
        "y": 0.9007263922518159
      },
      {
        "x": 0.024096385542168676,
        "y": 0.8547215496368039
      }
    ]
  }
```
Das **Kopieren dieser Koordinaten** kann über die Schaltfläche In die Zwischenablage kopieren erfolgen. Zonen und Linien können umbenannt und gelöscht werden, indem Sie die drei Punkte neben jeder Zone und Linie verwenden.  
![Screenshot von Zonen-Drawer.](./media/sample-player-widget/widget-zone-drawer.png)

## <a name="video-clips"></a>Videoclips
Bei Videoclips können Sie ein Startdatum und eine Startzeit sowie ein Enddatum und eine Endzeit auswählen. Sie können den Videoclip über die Schaltfläche *Hinzufügen* erstellen.
Sie können jeden der von Ihnen erstellten Clips aus der Dropdown-Liste auswählen, wobei jeder Clip mit dem Start- und Enddatum und der Uhrzeit versehen ist.
(Beispiel: Format) Ihr Videoclip kann dann im typischen Videoplayer-Format angezeigt werden.  
![Screenshot von Videoclips.](./media/sample-player-widget/widget-video-clips.png)

## <a name="next-steps"></a>Nächste Schritte
Versuchen Sie, Ihr eigenes benutzerdefiniertes Video Analyzer-Player-Widget mit diesen [Anweisungen](./player-widget.md) zu erstellen

