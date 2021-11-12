---
title: Versionshinweise zu Azure Video Analyzer – Azure
description: Dieser Artikel enthält Versionshinweise zu Releases, Verbesserungen, Fehlerbehebungen und bekannten Problemen von Azure Video Analyzer.
ms.topic: conceptual
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 89ab651416237e06343f950d2a9202f082555e58
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131556443"
---
# <a name="azure-video-analyzer-release-notes"></a>Versionshinweise zu Azure Video Analyzer

>Sie können eine Benachrichtigung erhalten, wann auf dieser Seite Updates vorhanden sind, indem Sie die URL `https://docs.microsoft.com/api/search/rss?search=%22Azure+Video+Analyzer+on+IoT+Edge+release+notes%22&locale=en-us` kopieren und in Ihren RSS-Feedreader einfügen.

Dieser Artikel bietet Folgendes:

* Neueste Versionen
* Bekannte Probleme
* Behebung von Programmfehlern
* Veraltete Funktionen

<hr width=100%>

## <a name="november-2-2021"></a>2\. November 2021

Diese Version ist ein Update des Video Analyzer Edge-Moduls und des Video Analyzer-Dienstes. Das Release-Tag für das Edge-Modul lautet:

```
mcr.microsoft.com/media/video-analyzer:1.1.0
```

> [!NOTE]
> In den Schnellstarts und Tutorials verwenden die Bereitstellungsmanifeste das Tag „1“ (video-analyzer:1). Daher sollte eine einfache Neubereitstellung solcher Manifeste das Modul auf Ihren Edgegeräten aktualisieren, wenn neuere Tags veröffentlicht werden.

Die ARM-API-Version des Video Analyzer-Dienstes ist folgende:

```
2021-11-01-preview
```

### <a name="new-functionalities"></a>Neue Funktionalitäten

* Wenn Sie Video Analyzer mit dem KI-Dienst [Computer Vision für die räumliche Analyse](edge/computer-vision-for-spatial-analysis.md) von Cognitive Services verwenden, können Sie neue Erkenntnisse wie Geschwindigkeit, Orientierung und Spur von Personen im Live-Video generieren und anzeigen.
* Sie können im lokalen Subnetz Ihres Edge-Geräts [ONVIF-fähige Geräte erkennen](edge/camera-discovery.md).
* Sie können [Live-Videos direkt in der Cloud aufnehmen und aufzeichnen](cloud/connect-cameras-to-cloud.md).
  * Sie können [Streaming mit geringer Latenz](playback-recordings-how-to.md#low-latency-streaming) verwenden, um das Live-Video von der RTSP-Kamera mit End-to-End-Latenzen von etwa 2 Sekunden anzuzeigen
  * Sie können den [Video Analyzer IoT PnP-Vertrag](cloud/connect-devices.md) auf Ihrer RTSP-Kamera implementieren, um die Videoaufnahme von Ihrem Gerät an den Video Analyzer-Dienst zu ermöglichen.
* Sie können [den gewünschten Teil Ihres aufgenommenen Videos](cloud/export-portion-of-video-as-mp4.md) in eine MP4-Datei exportieren.
* Sie können für jedes Ihrer aufgenommenen Videos eine Aufbewahrungsrichtlinie festlegen, bei der der Dienst regelmäßig Inhalte kürzt, die älter als die angegebene Anzahl von Tagen sind.
* Videos, die mit dem Edge-Modul von Video Analyzer aufgenommen wurden, können regelmäßig [Vorschaubilder](edge/enable-video-preview-images.md) oder Miniaturansichten enthalten, um ein besseres Surferlebnis zu ermöglichen.

### <a name="known-issues"></a>Bekannte Probleme
* Beim Streaming mit niedriger Latenzzeit kann jeweils nur ein Client mit dem Dienst verbunden werden
* Bei Verwendung eines gRPC-Erweiterungsmoduls für die Rückschließen mit gemeinsamem Speicher sollten sowohl das Video Analyzer-Edge-Modul als auch das Erweiterungsmodul unter demselben [Benutzer und derselben Gruppe](https://docs.docker.com/engine/reference/builder/#user) laufen

## <a name="october-1-2021"></a>1\. Oktober 2021
Der Video Analyzer-Dienst ist jetzt (in der Vorschau) in Ost-Australien verfügbar. Aktuelle Informationen zur Verfügbarkeit finden Sie [hier](https://azure.microsoft.com/global-infrastructure/services/?products=video-analyzer&regions=all).

Ein neuer Artikel wurde hinzugefügt, um zu beschreiben, wie Sie [das Videoanalyse-Widget in Power BI einbetten](embed-player-in-power-bi.md).

## <a name="june-3-2021"></a>3\. Juni 2021

Das Releasetag für die Aktualisierung des Moduls im Juli 2021 lautet:

```
     mcr.microsoft.com/media/video-analyzer:1.0.1
```
> [!NOTE]
> In den Schnellstarts und Tutorials verwenden die Bereitstellungsmanifeste das Tag „1“ (video-analyzer:1). Daher sollte eine einfache Neubereitstellung solcher Manifeste das Modul auf Ihren Edgegeräten aktualisieren, wenn neuere Tags veröffentlicht werden.

### <a name="module-updates"></a>Modulupdates
* Unterstützt Unicode-Zeichen in den Anmeldeinformationen zum Herstellen einer Verbindung mit einer RTSP-Kamera
* Updates zum Aktivieren detaillierter Protokolle im Debugmodus

<hr width=100%>

## <a name="may-25-2021"></a>25. Mai 2021

Dieses Release ist die erste öffentliche Vorschauversion von Azure Video Analyzer. Das Releasetag ist

```
mcr.microsoft.com/media/video-analyzer:1.0.0
```

> [!NOTE]
> In den Schnellstarts und Tutorials verwenden die Bereitstellungsmanifeste das Tag „1“ (video-analyzer:1). Daher sollte eine einfache Neubereitstellung solcher Manifeste das Modul auf Ihren Edgegeräten aktualisieren, wenn neuere Tags veröffentlicht werden.

### <a name="supported-functionalities"></a>Unterstützte Funktionen

* Analyse von Live-Videostreams mithilfe von KI-Modulen Ihrer Wahl und optionales Aufzeichnen von Videosignalen auf dem Edge-Gerät oder in der Cloud
* Aufzeichnen von Videos zusammen mit Rückschlussmetadaten in der Cloud
* Auslösen von Ereignissen mit Ihrem eigenen Objekterkennungsmodell, wenn Objekte eine Grenze überschreiten
* Nachverfolgen von Objekten, die von Ihrem eigenen Erkennungsmodell erkannt wurden 
* Verwenden von Video Analyzer-Playerwidgets (Webkomponenten), um aufgezeichnete Video- und Rückschlussmetadaten wiederzugeben
* Bereitstellung und Konfiguration des Moduls über den IoT Hub mithilfe des Azure-Portals oder von Visual Studio Code
<!--REDIRECT* Manage [pipeline topologies](pipeline.md#pipeline-topologies) remotely or locally using [direct method](direct-methods.md) calls-->

## <a name="next-steps"></a>Nächste Schritte

[Übersicht](overview.md)
