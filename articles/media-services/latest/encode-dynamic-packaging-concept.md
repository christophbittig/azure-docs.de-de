---
title: Dynamische Paketerstellung in Azure Media Services v3
description: Dieser Artikel bietet eine Übersicht über die dynamische Paketerstellung in Azure Media Services.
author: myoungerman
manager: femila
services: media-services
ms.service: media-services
ms.workload: media
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: inhenkel
ms.openlocfilehash: acc993f5690fbc250659830b52099f8d3d966421
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122343518"
---
# <a name="dynamic-packaging-in-media-services-v3"></a>Dynamische Paketerstellung in Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services bietet integrierte Ursprungsserver- und Paketerstellungsfunktionen zur Bereitstellung von Inhalten in HLS- und MPEG DASH-Streamingprotokollformaten. Bei AMS fungiert der [Streamingendpunkt](stream-streaming-endpoint-concept.md) als „Ursprungsserver“, der formatierte HLS- und DASH-Inhalte an Clientplayer sendet, die das Adaptive Bitrate Streaming mithilfe dieser gängigen Formate unterstützen. Der Streamingendpunkt unterstützt auch viele Features wie Just-In-Time, dynamische Paketerstellung mit oder ohne Inhaltsschutz, um alle wichtigen Geräte (wie iOS- und Android-Geräte) zu erreichen. 

Die meisten Browser und mobilen Geräte auf dem Markt unterstützen und verstehen die HLS- oder DASH-Streamingprotokolle. Für iOS müssen Streams z. B. im HLS-Format (HTTP Live Streaming) geliefert werden, und Android-Geräte unterstützen HLS sowie bei bestimmten Modellen auch MPEG DASH (oder mithilfe des Players auf Anwendungsebene, [Exoplayer](https://exoplayer.dev/), für Android-Geräte).

In Media Services repräsentiert ein [Streamingendpunkt](stream-streaming-endpoint-concept.md) (Ursprung) einen dynamischen Just-In-Time-Paketerstellungs- und Ursprungsdienst, der Ihre Live- und On-Demand-Inhalte direkt in einer Clientplayer-App bereitstellen kann. Der Streamingendpunkt verwendet hierbei eines der im nächsten Abschnitt beschriebenen allgemeinen Streamingmedienprotokolle. Die Funktion für die *dynamische Paketerstellung* ist standardmäßig auf allen Streamingendpunkten vorhanden.

Die Vorteile der Just-In-Time-Paketerstellung sind folgende:

* Sie können alle Dateien im MP4-Standarddateiformat speichern.
* Sie müssen nicht mehrere Kopien von statisch gepackten HLS- und DASH-Formaten im Blobspeicher speichern, um den Umfang der gespeicherten Videoinhalte zu reduzieren und die Gesamtkosten für den Speicher zu senken.
* Sie können neue Protokollaktualisierungen und Änderungen an den Spezifikationen, die sich im Laufe der Zeit entwickeln, sofort nutzen, ohne dass Sie den statischen Inhalt in Ihrem Katalog neu verpacken müssen.
* Sie können Inhalte ohne Verschlüsselung und DRM mithilfe derselben MP4-Dateien im Speicher zustellen.
* Sie können die Manifeste mit einfachen Filtern auf Medienobjektebene oder globalen Filtern dynamisch filtern oder ändern, um bestimmte Spuren, Auflösungen oder Sprachen zu entfernen oder kürzere Highlightclips aus denselben MP4-Dateien bereitzustellen, ohne den Inhalt neu zu codieren oder zu rendern. 

## <a name="to-prepare-your-source-files-for-delivery"></a>So bereiten Sie die Quelldateien für die Bereitstellung vor

Um die dynamische Paketerstellung nutzen zu können, müssen Sie Ihre Eingabedatei (Quelldatei) in einen Satz von MP4-Dateien (ISO Base Media 14496-12) mit einzelner Bitrate oder mehreren Bitraten [codieren](encode-concept.md). Sie müssen über ein [Medienobjekt](assets-concept.md) mit den codierten MP4- und Streamingkonfigurationsdateien verfügen, die für die dynamische Paketerstellung von Media Services erforderlich sind. Aus diesem Satz von MP4-Dateien können Sie mithilfe der dynamischen Paketerstellung über die im Anschluss beschriebenen Streamingmedienprotokolle Videos bereitstellen.

In der Regel verwenden Sie den Standardencoder von Azure Media Services, um diese Inhalte mithilfe der Voreinstellungen für die inhaltsbezogene Codierung oder der Voreinstellungen für die adaptive Bitrate zu generieren.  Beide generieren eine Reihe von MP4-Dateien, die für das Streaming und die dynamische Paketerstellung bereit sind.  Alternativ können Sie sich für die Codierung in einem externen Dienst, lokal oder auf Ihren eigenen VMs oder serverlosen Funktions-Apps entscheiden. Extern codierte Inhalte können in ein Medienobjekt zum Streaming hochgeladen werden, sofern sie die Codierungsanforderungen für Adaptive Bitrate Streaming-Formate erfüllen. Ein Beispielprojekt für das Hochladen einer vorcodierten MP4-Datei zum Streaming ist in den .NET SDK-Beispielen verfügbar – siehe [Stream Existing Mp4 files](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/StreamExistingMp4) (Streamen vorhandener MP4-Dateien).


Die dynamische Paketerstellung von Azure Media Services unterstützt nur Video- und Audiodateien im MP4-Containerformat. Audiodateien müssen ebenfalls in einen MP4-Container codiert werden, wenn alternative Codecs wie Dolby verwendet werden.  

> [!TIP]
> Zum Abrufen der MP4-Dateien und Streamingkonfigurationsdateien können Sie beispielsweise [Ihre Zwischendatei (Quelldatei) mit Media Services codieren](#encode-to-adaptive-bitrate-mp4s).  Wir empfehlen, die [Voreinstellung für inhaltsbezogene Codierung](encode-content-aware-concept.md) zu verwenden, um die besten adaptiven Streamingebenen und -einstellungen für Ihre Inhalte zu generieren. Weitere Informationen finden Sie unter den Codebeispielen zur Codierung mit dem [.NET SDK im VideoEncoding-Ordner](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding). 

Um die Videos im codierten Medienobjekt für Clients zur Wiedergabe zur Verfügung zu stellen, müssen Sie das Medienobjekt mithilfe eines [Streaminglocators](stream-streaming-locators-concept.md) veröffentlichen und die entsprechenden HLS- und DASH-Streaming-URLs erstellen. Indem Sie das verwendete Protokoll in der URL-Formatabfrage ändern, liefert der Dienst das entsprechende Streamingmanifest (HLS, MPEG DASH).

Folglich müssen Sie die Dateien nur in einem einzelnen Speicherformat (MP4) speichern und dafür bezahlen. Media Services generiert und liefert die entsprechenden HLS- oder DASH-Manifeste auf der Grundlage der Anforderungen Ihrer Clientplayer.

Wenn Sie Ihre Inhalte mit der dynamischen Verschlüsselung von Media Services schützen möchten, finden Sie unter [Streamingprotokolle und Verschlüsselungstypen](drm-content-protection-concept.md#streaming-protocols-and-encryption-types) weitere Informationen.

## <a name="deliver-hls"></a>Bereitstellen von HLS
### <a name="hls-dynamic-packaging"></a>HLS: Dynamische Paketerstellung

Ihr Streamingclient kann folgende HLS-Formate angeben. Es wird empfohlen, das CMAF-Format zu verwenden, um die Kompatibilität mit den neuesten Playern und iOS-Geräten sicherzustellen.  Bei Legacygeräten sind auch die Formate „v4“ und „v3“ verfügbar, indem Sie einfach die Formatabfragezeichenfolge ändern.

|Protocol| Formatzeichenfolge| Beispiel|
|---|---|---|
|HLS CMAF (empfohlen)| format=m3u8-cmaf | `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`|
|HLS V4 |  format=m3u8-aapl | `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`|
|HLS V3 | format=m3u8-aapl-v3 | `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`|


> [!NOTE]
> In früheren Richtlinien von Apple wurde als Fallback für Netzwerke mit geringer Bandbreite ein reiner Audiodatenstrom empfohlen.  Der Media Services-Encoder generiert derzeit automatisch eine reine Audiospur.  Jetzt besagen die Apple-Richtlinien, dass die reine Audiospur *nicht* enthalten sein soll, insbesondere bei der Distribution für Apple TV.  Damit der Player nicht auf als Standard auf eine reine Audiospur zurückgreift, wird empfohlen, das Tag „audio-only=false“ in der URL anzugeben. Dadurch wird die reine Audiowiedergabe in HLS entfernt. Sie können auch einfach HLS-V3 verwenden. Beispiel: `http://host/locator/asset.ism/manifest(format=m3u8-aapl,audio-only=false)`.


### <a name="hls-packing-ratio-for-vod"></a>HLS-Komprimierungsverhältnis für VOD

Um das Komprimierungsverhältnis von VOD-Inhalten für ältere HLS-Formate zu steuern, können Sie das Metadatentag **fragmentsPerHLSSegment** in der ISM-Datei festlegen, um das Standardkomprimierungsverhältnis von 3:1 für TS-Segmente zu steuern, die von älteren HLS-Formatmanifesten vom Typ „v3“ und „v4“ bereitgestellt werden. Diese Einstellungsänderung erfordert, dass Sie die ISM-Datei im Speicher direkt ändern, um das Komprimierungsverhältnis anzupassen.

ISM-Beispielservermanifest, bei dem **fragmentsPerHLSSegment** auf 1 festgelegt ist. 
``` xml
   <?xml version="1.0" encoding="utf-8" standalone="yes"?>
   <smil xmlns="http://www.w3.org/2001/SMIL20/Language">
      <head>
         <meta name="formats" content="mp4" />
         <meta name="fragmentsPerHLSSegment" content="1"/>
      </head>
      <body>
         <switch>
         ...
         </switch>
      </body>
   </smil>
```

## <a name="deliver-dash"></a>Bereitstellen von DASH
### <a name="dash-dynamic-packaging"></a>DASH: Dynamische Paketerstellung

Ihr Streamingclient kann folgende MPEG-DASH-Formate angeben:

|Protocol| Formatzeichenfolge| Beispiel|
|---|---|---|
|MPEG-DASH CMAF (empfohlen)| format=mpd-time-cmaf | `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` |
|MPEG-DASH CSF (Legacy)| format=mpd-time-csf | `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` |

## <a name="deliver-smooth-streaming-manifests"></a>Bereitstellen von Smooth Streaming-Manifesten
### <a name="smooth-streaming-dynamic-packaging"></a>Smooth Streaming: Dynamische Paketerstellung

Ihr Streamingclient kann folgende Smooth Streaming-Formate angeben:

|Protocol|Hinweise/Beispiele| 
|---|---|
|Smooth Streaming| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`|
|Smooth Streaming 2.0 (Legacymanifest)|Das Smooth Streaming-Manifestformat enthält standardmäßig das Wiederholungstag (r-Tag). Einige Player unterstützen `r-tag` jedoch nicht. Clients mit diesen Playern können ein Format verwenden, das das r-Tag deaktiviert:<br/><br/>`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=fmp4-v20)`|

> [!NOTE]
> Smooth Streaming erfordert, dass Ihr Stream sowohl Audio- als auch Videodaten enthält.

## <a name="on-demand-streaming-workflow"></a>Workflow für das On-Demand-Streaming

Die folgenden Schritte zeigen einen allgemeinen Media Services-Streamingworkflow mit dynamischer Paketerstellung und dem Standard-Encoder in Azure Media Services.

1. [Laden Sie eine Eingabedatei hoch](job-input-from-http-how-to.md), etwa eine MP4-, QuickTime- oder MOV-Datei oder ein anderes unterstütztes Dateiformat. Diese Datei wird auch als Mezzanine- oder Quelldatei bezeichnet. Eine Liste der unterstützten Formate finden Sie unter [Media Encoder Standard-Formate und -Codecs](encode-media-encoder-standard-formats-reference.md).
1. [Codieren](#encode-to-adaptive-bitrate-mp4s) Sie Ihre Mezzaninedatei als H.264/AAC-MP4-Satz mit adaptiver Bitrate.

    Falls Sie bereits codierte Dateien besitzen und die Dateien nur kopieren und streamen möchten, verwenden Sie Folgendes: APIs [CopyVideo](/rest/api/media/transforms/createorupdate#copyvideo) und [CopyAudio](/rest/api/media/transforms/createorupdate#copyaudio). Daraufhin wird eine neue MP4-Datei mit einem Streamingmanifest (ISM-Datei) erstellt.

    Darüber hinaus können Sie einfach die ISM- und ISMC-Datei in einer vorcodierten Datei generieren, solange sie mit den richtigen Einstellungen für Adaptive Bitrate Streaming codiert wird (dies sind in der Regel 2-Sekunden-GOPs, Keyframeentfernungen von 2s Min. und Max. und Codierung im CBR-Modus (konstante Bitrate)).  

    Ausführliche Informationen zum Generieren der ISM-Datei (Servermanifest) und der ISMC-Datei (Clientmanifeste) für das Streaming aus einer vorhandenen, vorcodierten MP4-Datei finden Sie im [.NET SDK-Beispiel zum Streamen vorhandener MP4-Dateien](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/StreamExistingMp4).

1. Veröffentlichen Sie das Ausgabemedienobjekt, das den MP4-Satz mit adaptiver Bitrate enthält. Zur Veröffentlichung wird ein [Streaminglocator](stream-streaming-locators-concept.md) erstellt.
1. Erstellen Sie URLs für unterschiedliche Formate (HLS, MPEG-DASH und Smooth Streaming). Der *Streamingendpunkt* sorgt dafür, dass das korrekte Manifest bereitgestellt wird und die Anforderungen für alle diese Formate korrekt verarbeitet werden.
    
Das folgende Diagramm zeigt das On-Demand-Streaming mit dem Workflow der dynamischen Paketerstellung.

![Diagramm eines Workflows für das On-Demand-Streaming mit dynamischer Paketerstellung](./media/encode-dynamic-packaging-concept/media-services-dynamic-packaging.svg)

Der Downloadpfad in der obigen Abbildung ist nur angegeben, um zu veranschaulichen, dass Sie eine MP4-Datei direkt über den *Streamingendpunkt* (Ursprung) herunterladen können. (Sie geben die herunterladbare [Streamingrichtlinie](stream-streaming-policy-concept.md) im Streaminglocator an.)<br/>Der dynamische Paketerstellungs-Manager verändert die Datei nicht. Optional können Sie die Azure Blob Storage-APIs verwenden, um für den progressiven Download direkt auf eine MP4-Datei zuzugreifen, wenn Sie die Features für den *Streamingendpunkt* (Ursprung) umgehen möchten. 

### <a name="encode-to-adaptive-bitrate-mp4s"></a>Codieren als MP4-Dateien mit adaptiver Bitrate

Beispiele für die [Codierung mit Media Services](encode-concept.md) finden Sie in den folgenden Artikeln:

* [Verwenden der inhaltsbezogenen Codierung](encode-content-aware-concept.md)
* [Codieren aus einer HTTPS-URL mithilfe integrierter Voreinstellungen](job-input-from-http-how-to.md)
* [Codieren einer lokalen Datei mithilfe integrierter Voreinstellungen](job-input-from-local-file-how-to.md)
* [Entwickeln einer benutzerdefinierten Voreinstellung für Ihr spezielles Szenario oder Ihre Geräteanforderungen](transform-custom-presets-how-to.md)
* [Codebeispiele für die Codierung mit dem Standardencoder mithilfe von .NET](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding)

Weitere Informationen finden Sie in der Liste der unterstützten [Formate und Codecs](encode-media-encoder-standard-formats-reference.md) für die Standardencodereingabe.

## <a name="live-streaming-workflow"></a>Workflow für das Livestreaming

Ein Liveereignis kann entweder auf eine *Pass-Through*- (ein lokaler Liveencoder sendet einen Stream mit mehreren Bitraten) oder *Livecodierung* (ein lokaler Liveencoder sendet einen Stream mit Einzelbitrate) festgelegt werden. 

Nachfolgend wird ein allgemeiner Workflow für das Livestreaming mit *dynamischer Paketerstellung* beschrieben:

1. Erstellen Sie ein [Liveereignis](live-event-outputs-concept.md).
1. Rufen Sie die Erfassungs-URL ab, und konfigurieren Sie Ihren lokalen Encoder so, dass die URL zum Senden des Beitragsfeeds verwendet wird.
1. Rufen Sie die Vorschau-URL ab, und verwenden Sie sie, um zu überprüfen, ob die Eingabe des Encoders empfangen wird.
1. Erstellen Sie ein neues Medienobjekt.
1. Erstellen Sie eine Liveausgabe, und verwenden Sie den Namen des erstellten Medienobjekts.<br />Die Liveausgabe archiviert den Datenstrom im Medienobjekt.
1. Erstellen Sie einen Streaminglocator mit den integrierten Typen von Streamingrichtlinien.<br />Wenn Sie beabsichtigen, den Inhalt zu verschlüsseln, lesen Sie [Übersicht über den Inhaltsschutz](drm-content-protection-concept.md).
1. Listen Sie die Pfade für den Streaminglocator so auf, dass die zu verwendenden URLs abgerufen werden.
1. Rufen Sie den Hostnamen für den Streamingendpunkt ab, von dem aus Sie streamen möchten.
1. Erstellen Sie URLs für unterschiedliche Formate (HLS, MPEG-DASH und Smooth Streaming). Der *Streamingendpunkt* sorgt dafür, dass das korrekte Manifest bereitgestellt wird und die Anforderungen für die verschiedenen Formate korrekt verarbeitet werden.

Im folgenden Diagramm ist der Workflow für das Livestreaming mit *dynamischer Paketerstellung* dargestellt:

![Diagramm eines Workflows für die Pass-Through-Codierung mit dynamischer Paketerstellung](./media/live-streaming/pass-through.svg)

Informationen zum Livestreaming in Media Services v3 finden Sie unter [Übersicht über das Livestreaming](stream-live-streaming-concept.md).

## <a name="video-codecs-supported-by-dynamic-packaging"></a>Von der dynamischen Paketerstellung unterstützte Videocodecs

Die dynamische Paketerstellung unterstützt Videodateien, die im MP4-Containerdateiformat vorliegen und Videodaten enthalten, die mit [H.264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (MPEG-4 AVC oder AVC1) oder [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (HEVC, hev1 oder hvc1) codiert sind.

> [!NOTE]
> Mit der *dynamischen Paketerstellung* wurden Auflösungen von bis zu 4K und Bildfrequenzen von bis zu 60 Frames/Sekunde getestet.

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Von der dynamischen Paketerstellung unterstützte Audiocodecs

Darüber hinaus unterstützt die dynamische Paketerstellung Audiodateien, die im MP4-Dateicontainerformat gespeichert sind und codierte Audiostreams in einem der folgenden Codecs enthalten:

* [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1 oder HE-AAC v2) 
* [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (Enhanced AC-3 oder E-AC3).  Die codierte Audiodatei muss im MP4-Containerformat gespeichert werden, damit die dynamische Paketerstellung funktioniert.
* Dolby Atmos

   Das Streaming von Dolby Atmos-Inhalten wird für Standards wie das MPEG-DASH-Protokoll mit fragmentiertem CSF- (Common Streaming Format) oder CMAF-MP4 (Common Media Application Format) und über HTTP Live Streaming (HLS) mit CMAF unterstützt.
* [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29)<br />
   Folgende DTS-Codecs werden von den Paketerstellungsformaten DASH-CSF, DASH-CMAF, HLS-M2TS und HLS-CMAF unterstützt:  

    * DTS Digital Surround (dtsc)
    * DTS-HD High Resolution und DTS-HD Master Audio (dtsh)
    * DTS Express (dtse)
    * DTS-HD Lossless ohne Core (dtsl)

Die dynamische Paketerstellung unterstützt mehrere Audiospuren mit DASH oder HLS (ab Version 4) für Streamingmedienobjekte, die mehrere Audiospuren mit mehreren Codecs und Sprachen besitzen.

Bei allen oben genannten Codecs muss die codierte Audiodatei im MP4-Containerformat gespeichert sein, damit die dynamische Paketerstellung funktioniert. Der Dienst unterstützt keine unformatierten elementaren Datenstrom-Dateiformate im Blobspeicher. (Folgendes wird beispielsweise nicht unterstützt: .dts, .ac3.) 

Nur Dateien mit der Erweiterung „.mp4“ oder „.mp4a“ werden für die Audiopaketerstellung unterstützt. 

### <a name="limitations"></a>Einschränkungen

#### <a name="ios-limitation-on-aac-51-audio"></a>Einschränkungen unter iOS für AAC 5.1-Audio

Apple iOS-Geräte unterstützen den 5.1 AAC-Audiocodec nicht. Mehrkanal-Audio muss mit Dolby Digital- oder Dolby Digital Plus-Codecs codiert werden.

Ausführliche Informationen finden Sie unter [HLS-Erstellungsspezifikation für Apple-Geräte](https://developer.apple.com/documentation/http_live_streaming/hls_authoring_specification_for_apple_devices).

> [!NOTE]
> Media Services unterstützt nicht die Codierung der Mehrkanal-Audioformate Dolby Digital, Dolby Digital Plus oder Dolby Digital Plus mit Dolby Atmos.

#### <a name="dolby-digital-audio"></a>Dolby Digital-Audio

Die dynamische Paketerstellung von Media Services unterstützt derzeit keine Dateien, die [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital)-Audio (AC3) enthalten, da dies als Legacy-Codec von Dolby betrachtet wird.

## <a name="manifests"></a>Manifeste

Bei der *dynamischen Paketerstellung* von Media Services werden die Streamingclientmanifeste für HLS, MPEG-DASH und Smooth Streaming dynamisch basierend auf der **Formatabfrage** in der URL generiert.  

Eine Manifestdatei enthält Streamingmetadaten wie etwa Typ (Audio, Video oder Text), Titelname, Start- und Endzeit, Bitrate (Qualität), Sprachen, Präsentationsfenster (gleitendes Fenster mit fester Dauer) und Videocodec (FourCC). Sie weist den Player zudem zum Abrufen des nächsten Fragments an, indem Informationen zu den nächsten für die Wiedergabe verfügbaren Videofragmenten und den zugehörigen Speicherorten angegeben werden. Fragmente (oder Segmente) sind die eigentlichen „Blöcke“ von Videoinhalten.

### <a name="examples"></a>Beispiele

#### <a name="hls"></a>HLS

Beispiel für eine HLS-Manifestdatei, auch als HLS Master Playlist bezeichnet: 

```
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-MEDIA:TYPE=AUDIO,GROUP-ID="audio",NAME="aac_eng_2_128041_2_1",LANGUAGE="eng",DEFAULT=YES,AUTOSELECT=YES,URI="QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)"
#EXT-X-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d,mp4a.40.2",AUDIO="audio"
QualityLevels(381048)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d",URI="QualityLevels(381048)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015,mp4a.40.2",AUDIO="audio"
QualityLevels(721495)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015",URI="QualityLevels(721495)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e,mp4a.40.2",AUDIO="audio"
QualityLevels(1154816)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e",URI="QualityLevels(1154816)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f,mp4a.40.2",AUDIO="audio"
QualityLevels(2217354)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f",URI="QualityLevels(2217354)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020,mp4a.40.2",AUDIO="audio"
QualityLevels(3579827)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020",URI="QualityLevels(3579827)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=139017,CODECS="mp4a.40.2",AUDIO="audio"
QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)
```

#### <a name="mpeg-dash"></a>MPEG-DASH

Beispiel für eine MPEG-DASH-Manifestdatei, auch als MPEG-DASH Media Presentation Description (MPD) bezeichnet:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT1M10.315S" minBufferTime="PT7S">
   <Period>
      <AdaptationSet id="1" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60160000" r="10" />
               <S d="41386666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="5_A_aac_eng_2_128041_2_1_1" bandwidth="128041" audioSamplingRate="48000" />
      </AdaptationSet>
      <AdaptationSet id="2" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60060000" r="10" />
               <S d="42375666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="1_V_video_1" bandwidth="3579827" width="1280" height="720" />
         <Representation id="1_V_video_2" bandwidth="2217354" codecs="avc1.64001F" width="960" height="540" />
         <Representation id="1_V_video_3" bandwidth="1154816" codecs="avc1.64001E" width="640" height="360" />
         <Representation id="1_V_video_4" bandwidth="721495" codecs="avc1.640015" width="480" height="270" />
         <Representation id="1_V_video_5" bandwidth="381048" codecs="avc1.64000D" width="320" height="180" />
      </AdaptationSet>
   </Period>
</MPD>
```
#### <a name="smooth-streaming"></a>Smooth Streaming

Beispiel für eine Smooth Streaming-Manifestdatei:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="703146666" TimeScale="10000000">
   <StreamIndex Chunks="12" Type="audio" Url="QualityLevels({bitrate})/Fragments(aac_eng_2_128041_2_1={start time})" QualityLevels="1" Language="eng" Name="aac_eng_2_128041_2_1">
      <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="128041" FourCC="AACL" CodecPrivateData="1190" Channels="2" PacketSize="4" SamplingRate="48000" />
      <c t="0" d="60160000" r="11" />
      <c d="41386666" />
   </StreamIndex>
   <StreamIndex Chunks="12" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="5">
      <QualityLevel Index="0" Bitrate="3579827" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="0000000167640020ACD9405005BB011000003E90000EA600F18319600000000168EBECB22C" />
      <QualityLevel Index="1" Bitrate="2217354" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FACD940F0117EF01100000303E90000EA600F1831960000000168EBECB22C" />
      <QualityLevel Index="2" Bitrate="1154816" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EACD940A02FF9701100000303E90000EA600F162D960000000168EBECB22C" />
      <QualityLevel Index="3" Bitrate="721495" FourCC="H264" MaxWidth="480" MaxHeight="270" CodecPrivateData="0000000167640015ACD941E08FEB011000003E90000EA600F162D9600000000168EBECB22C" />
      <QualityLevel Index="4" Bitrate="381048" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DACD941419F9F011000003E90000EA600F14299600000000168EBECB22C" />
      <c t="0" d="60060000" r="11" />
      <c d="42375666" />
   </StreamIndex>
</SmoothStreamingMedia>
```

### <a name="naming-of-tracks-in-the-manifest"></a>Benennen von Spuren im Manifest

Wird in der ISM-Datei ein Audiospurname angegeben, wird von Media Services ein `Label`-Element innerhalb eines `AdaptationSet`-Elements hinzugefügt, um die Strukturinformationen für die bestimmte Audiospur anzugeben. Beispiel für das DASH-Ausgabemanifest:

```xml
<AdaptationSet codecs="mp4a.40.2" contentType="audio" lang="en" mimeType="audio/mp4" subsegmentAlignment="true" subsegmentStartsWithSAP="1">
  <Label>audio_track_name</Label>
  <Role schemeIdUri="urn:mpeg:dash:role:2011" value="main"/>
  <Representation audioSamplingRate="48000" bandwidth="131152" id="German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg">
    <BaseURL>German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg.mp4</BaseURL>
  </Representation>
</AdaptationSet>
```

Der Player kann das Element `Label` für die Anzeige auf der Benutzeroberfläche verwenden.

### <a name="signaling-audio-description-tracks"></a>Kennzeichnen von Spuren für die Audiobeschreibung

Sie können dem Video eine Audiokommentarspur hinzufügen, damit Benutzer mit Sehbehinderung dem Video folgen können, indem sie sich den Audiokommentar anhören. Sie müssen eine Audiospur als Audiobeschreibung im Manifest kommentieren. Fügen Sie dafür in der ISM-Datei die Parameter „accessibility“ und „role“ hinzu. Es liegt in Ihrer Verantwortung, diese Parameter korrekt festzulegen, um eine Audiospur als Audiobeschreibung zu signalisieren. Fügen Sie beispielsweise `<param name="accessibility" value="description" />` und `<param name="role" value="alternate"` zur ISM-Datei für eine bestimmte Audiospur hinzu. 

Weitere Informationen finden Sie im Beispiel [Signalisieren einer beschreibenden Audiospur](signal-descriptive-audio-howto.md).

#### <a name="smooth-streaming-manifest"></a>Smooth Streaming-Manifest

Bei der Wiedergabe eines Smooth Streaming-Streams enthält das Manifest Werte in den `Accessibility`- und `Role`-Attributen für diese Audiospur. Beispielsweise durch Hinzufügen von `Role="alternate" Accessibility="description"` zum `StreamIndex`-Element angegeben werden, dass es sich um eine Audiobeschreibung handelt.

#### <a name="dash-manifest"></a>DASH-Manifest

Für das DASH-Manifest werden die folgenden beiden Elemente hinzugefügt, um die Audiobeschreibung zu signalisieren:

```xml
<Accessibility schemeIdUri="urn:mpeg:dash:role:2011" value="description"/>
<Role schemeIdUri="urn:mpeg:dash:role:2011" value="alternate"/>
```

#### <a name="hls-playlist"></a>HLS-Wiedergabeliste

Bei HLS Version 7 und höher `(format=m3u8-cmaf)` enthält die Wiedergabeliste `AUTOSELECT=YES,CHARACTERISTICS="public.accessibility.describes-video"`, wenn die Audiobeschreibungsspur signalisiert wird.



#### <a name="example"></a>Beispiel

Weitere Informationen finden Sie unter [Signalisieren einer beschreibenden Audiospur](signal-descriptive-audio-howto.md).

## <a name="dynamic-manifest-filtering"></a>Dynamische Manifestfilterung

Zur Steuerung der Anzahl von Spuren sowie der Formate, Bitraten und Präsentationszeitfenster können Sie die dynamische Filterung mit dem dynamischen Packager von Media Services verwenden. Weitere Informationen finden Sie unter [Vorfiltern von Manifesten mit dem dynamischen Packager](filters-dynamic-manifest-concept.md).

## <a name="dynamic-encryption-for-drm"></a>Dynamische Verschlüsselung für DRM

Mit der *dynamischen Verschlüsselung* können Sie Ihre Live- oder On-Demand-Inhalte mit AES-128 oder einem der drei hauptsächlichen DRM-Systeme (Digital Rights Management) dynamisch verschlüsseln: Microsoft PlayReady, Google Widevine und Apple FairPlay. Media Services bietet außerdem einen Dienst für die Übermittlung von AES-Schlüsseln und DRM-Lizenzen an autorisierte Clients. Weitere Informationen finden Sie unter [Dynamische Verschlüsselung](drm-content-protection-concept.md).

> [!NOTE]
> Widevine ist ein von Google Inc. bereitgestellter Dienst, der den Vertragsbedingungen und der Datenschutzrichtlinie von Google, Inc. unterliegt.

## <a name="more-information"></a>Weitere Informationen

Unter [Azure Media Services-Community](media-services-community.md) finden Sie verschiedene Möglichkeiten, Fragen zu stellen, Feedback zu geben und Updates zu Media Services abzurufen.

## <a name="need-help"></a>Sie brauchen Hilfe?

Sie können ein Supportticket erstellen, indem Sie zu [Neue Supportanfrage](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) navigieren.

## <a name="next-steps"></a>Nächste Schritte

[Hochladen, Codieren und Streamen von Videos](stream-files-tutorial-with-api.md)
