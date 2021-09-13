---
title: Codieren von Video- und Audiodaten mit Media Services
description: In diesem Artikel wird erläutert, wie Sie Video- und Audiodaten mit Azure Media Services codieren.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 7b4b3806ed00955096939c4b996c9a9d3daec004
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122343615"
---
# <a name="encoding-video-and-audio-with-media-services"></a>Codieren von Video- und Audiodaten mit Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Der Begriff Codierung beschreibt in Media Services den Prozess der Konvertierung von Dateien, die digitale Video- und/oder Audiodateien enthalten, von einem Format in ein anderes Format. Ziel ist es, (a) die Größe der Dateien zu verringern und/oder (b) ein Format zu erzeugen, das mit einer Vielzahl von Geräten und Anwendungen kompatibel ist. Dieser Prozess wird auch als Videokomprimierung oder Transcodierung bezeichnet. Weitere Erläuterungen zu diesem Konzept finden Sie unter [Datenkomprimierung](https://en.wikipedia.org/wiki/Data_compression) und [Was bedeutet Codierung und Transcodierung?](https://www.streamingmedia.com/Articles/Editorial/What-Is-/What-Is-Encoding-and-Transcoding-75025.aspx).

Videos werden in der Regel durch einen [progressiven Download](https://en.wikipedia.org/wiki/Progressive_download) oder per [Adaptive Bitrate Streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) an Geräte und Apps übermittelt.

> [!IMPORTANT]
> Bei Media Services erfolgt keine Abrechnung für abgebrochene oder fehlerhafte Aufträge. Beispielsweise wird ein Auftrag, der einen Status von 50 % erreicht hat und abgebrochen wird, nicht mit 50 % der Auftragsminuten berechnet. Ihnen werden nur abgeschlossene Aufträge in Rechnung gestellt.

* Zur Bereitstellung mittels progressivem Download können Sie Azure Media Services zum Konvertieren einer digitalen Mediendatei (Mezzanine) in eine [MP4](https://en.wikipedia.org/wiki/MPEG-4_Part_14)-Datei verwenden. Diese enthält das Video, das mit dem [H.264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC)-Codec codiert wurde, sowie die Audiodatei, die mit dem [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding)-Codec codiert wurde. Diese MP4-Datei wird in ein Medienobjekt in Ihrem Speicherkonto geschrieben. Sie können die Azure Storage-APIs oder -SDKs verwenden (z.B. [Storage-REST-API](../../storage/common/storage-rest-api-auth.md) oder [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)), um die Datei direkt herunterzuladen. Wenn Sie das Ausgabemedienobjekt mit einem bestimmten Containernamen im Speicher erstellt haben, verwenden Sie die diesen Speicherort. Andernfalls können Sie Media Services verwenden, um [die Medienobjektcontainer-URLs aufzulisten](/rest/api/media/assets/listcontainersas). 
* Die Mezzanine-Datei muss mit mehreren Bitraten (hoch bis niedrig) codiert werden, um Inhalte für die Übermittlung durch das Adaptive Bitrate Streaming vorzubereiten. Um eine gleichmäßige Qualitätsabnahme zu gewährleisten, wird mit sinkender Bitrate auch die Auflösung des Videos verringert. Daraus ergibt sich eine sogenannte Codierungsleiter, also eine Tabelle mit Auflösungen und Bitraten. (Lesen Sie dazu den Artikel [Codieren mit einer automatisch generierten Reihe von Bitraten-/Auflösungspaaren](encode-autogen-bitrate-ladder.md), oder verwenden Sie die empfohlene Voreinstellung für die inhaltsbezogene Codierung.) Sie können mithilfe von Media Services Ihre Mezzanine-Dateien mit verschiedenen Bitraten codieren. Auf diese Weise erhalten Sie einen Satz MP4-Dateien und zugehörige Konfigurationsdateien für das Streaming, die in ein Medienobjekt in Ihrem Speicherkonto geschrieben werden. Sie können dann die Funktion [Dynamische Paketerstellung](encode-dynamic-packaging-concept.md) in Media Services verwenden, um das Video über Streamingprotokolle wie [MPEG-DASH](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP) oder [HLS](https://en.wikipedia.org/wiki/HTTP_Live_Streaming) bereitzustellen. Dazu müssen Sie einen [Streaminglocator](stream-streaming-locators-concept.md) und Streaming-URLs erstellen, die dem unterstützten Protokoll entsprechen. Dieses kann dann je nach Funktionen von Geräten/Apps an diese übergeben werden.

Das folgende Diagramm zeigt den Workflow für bedarfsgesteuerte Codierung mit der dynamischen Paketerstellung.

![Workflows für On-Demand-Streaming mit dynamischer Paketerstellung](./media/encode-dynamic-packaging-concept/media-services-dynamic-packaging.svg)

In diesem Thema erhalten Sie Anleitungen zum Codieren Ihrer Inhalte mit Media Services v3.

## <a name="transforms-and-jobs"></a>Transformationen und Aufträge

Um mit Media Services v3 codieren zu können, müssen Sie eine [Transformation](/rest/api/media/transforms) und einen [Auftrag](/rest/api/media/jobs) erstellen. Die Transformation definiert eine Anweisung für die Codierungseinstellungen und -ausgaben. Der Auftrag ist eine Instanz der Anweisung. Weitere Informationen finden Sie unter [Transformationen und Aufträge](transform-jobs-concept.md).

Bei der Codierung mit Media Services verwenden Sie Voreinstellungen, um dem Encoder mitzuteilen, wie die eingegebenen Mediendateien verarbeitet werden sollen. In Media Services v3 verwenden Sie den Standard-Encoder zum Codieren Ihrer Dateien. Beispielsweise können Sie die gewünschte Videoauflösung und/oder die Anzahl der Audiokanäle für den codierten Inhalt angeben.

Mit einer der empfohlenen integrierten Voreinstellungen basierend auf in der Branche bewährten Vorgehensweisen können Sie schnell einsteigen. Alternativ können Sie eine benutzerdefinierte Voreinstellung für Ihr spezielles Szenario oder Ihre Geräteanforderungen erstellen. Weitere Informationen finden Sie unter [Codieren mit einer benutzerdefinierten Transformation](transform-custom-presets-how-to.md).

Ab Januar 2019 wird bei der Codierung mit dem Standard-Encoder zum Erzeugen von MP4-Dateien eine neue MPI-Datei generiert und dem Ausgabeasset hinzugefügt. Diese MPI-Datei dient zum Verbessern der Leistung für die [dynamische Paketerstellung](encode-dynamic-packaging-concept.md) und Streamingszenarios.

> [!NOTE]
> Sie sollten die MPI-Datei nicht ändern oder entfernen und auch keine Abhängigkeiten vom Vorhandensein (oder Nichtvorhandensein) einer solchen Datei in Ihren Dienst integrieren.

### <a name="creating-job-input-from-an-https-url"></a>Erstellen einer Auftragseingabe aus einer HTTPS-URL

Wenn Sie Aufträge zur Verarbeitung von Videos übermitteln, müssen Sie Media Services mitteilen, wo sich das Eingabevideo befindet. Eine der Optionen ist die Angabe einer HTTPS-URL als Auftragseingabe. Media Services v3 unterstützt aktuell keine segmentierte Übertragungscodierung über HTTPS-URLs.

#### <a name="examples"></a>Beispiele

* [Codieren über eine HTTPS-URL mit .NET](stream-files-dotnet-quickstart.md)
* [Codieren über eine HTTPS-URL mit REST](stream-files-tutorial-with-rest.md)
* [Codieren über eine HTTPS-URL mit einer CLI](stream-files-cli-quickstart.md)
* [Codieren über eine HTTPS-URL mit Node.js](stream-files-nodejs-quickstart.md)

### <a name="creating-job-input-from-a-local-file"></a>Erstellen einer Auftragseingabe aus einer lokalen Datei

Das Eingabevideo kann als Media Service-Medienobjekt gespeichert werden. In diesem Fall erstellen Sie ein Eingabemedienobjekt basierend auf einer Datei (die lokal oder in Azure Blob Storage gespeichert ist).

#### <a name="examples"></a>Beispiele

[Codieren einer lokalen Datei mithilfe von integrierten Voreinstellungen](job-input-from-local-file-how-to.md)

### <a name="creating-job-input-with-subclipping"></a>Erstellen von Auftragseingaben mithilfe von Subclips

Beim Codieren eines Videos können Sie ebenfalls angeben, dass die Quelldatei zugeschnitten oder in Subclips unterteilt werden soll. Dadurch erzeugen Sie eine Ausgabe, die nur den gewünschten Teil des Eingabevideos enthält. Dies funktioniert mit jeder [Transformation](/rest/api/media/transforms), die entweder mit den [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset)-Voreinstellungen oder mit den [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset)-Voreinstellungen erstellt wird.

Sie können angeben, dass ein [Auftrag](/rest/api/media/jobs/create) mit einem einzelnen Clip eines On-Demand-Videos oder aus einem Livearchiv (einem aufgezeichneten Ereignis) erstellt werden soll. Bei der Auftragseingabe kann es sich um ein Objekt oder um eine HTTPS-URL handeln.

> [!TIP]
> Wenn Sie einen Subclip Ihres Videos streamen möchten, ohne das Video erneut zu codieren, erwägen Sie das [Vorfiltern von Manifesten mithilfe des dynamischen Packagers](filters-dynamic-manifest-concept.md).

#### <a name="examples"></a>Beispiele

Beispiele finden Sie hier:

* [Erstellen von Subclips mit .NET](transform-subclip-video-dotnet-how-to.md)
* [Erstellen von Subclips mit REST](transform-subclip-video-rest-how-to.md)

## <a name="built-in-presets"></a>Integrierte Voreinstellungen

Media Services unterstützt die folgenden integrierten Codierungsvoreinstellungen:  

### <a name="builtinstandardencoderpreset"></a>BuiltInStandardEncoderPreset

[BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) wird zum Festlegen einer integrierten Voreinstellung für die Codierung des Eingabevideos mit dem Standard-Encoder verwendet.

Die folgenden integrierten Voreinstellungen werden derzeit unterstützt:

- **EncoderNamedPreset.AACGoodQualityAudio**: Erzeugt eine einzelne MP4-Datei, die nur mit 192 KBit/s codierte Stereo-Audiodaten enthält.
- **EncoderNamedPreset.AdaptiveStreaming** (empfohlen): unterstützt die adaptive Bitratencodierung H.264. Weitere Informationen finden Sie unter [Automatisches Generieren einer Reihe von Bitraten-/Auflösungspaaren](encode-autogen-bitrate-ladder.md).
- **EncoderNamerPreset.H265AdaptiveStreaming:** ähnelt der Voreinstellung AdaptiveStreaming, verwendet aber den HEVC-Codec (H.265). Erzeugt mehrere auf GOP ausgerichtete MP4-Dateien mit Video- und Stereo-AAC-Audio für H.265. Generiert automatisch eine Reihe von Bitraten, die auf der eingegebenen Auflösung, der Bitrate und der Bildfrequenz basieren. Die automatisch generierte Voreinstellung wird nie höher als die eingegebene Auflösung sein. Wenn beispielsweise 720p eingegeben wird, bleibt die Ausgabe am besten auch bei 720p.
- **EncoderNamedPreset.ContentAwareEncoding:** Stellt eine Voreinstellung für die inhaltsbezogene H.264-Codierung zur Verfügung. Erzeugt mithilfe der inhaltsbezogenen Codierung verschiedene GOP-orientierte MP4s. Der Dienst führt eine einfache Erstanalyse für den Eingabeinhalt aus und ermittelt anhand der Ergebnisse die optimale Anzahl von Ebenen, die geeignete Bitrate und die Auflösungseinstellungen für die Bereitstellung durch adaptives Streaming. Diese Voreinstellung ist besonders effektiv für Videos mit geringer und mittlerer Komplexität, bei denen die Ausgabedateien mit niedrigeren Bitraten, aber in einer Qualität vorliegen, die dem Betrachter dennoch ein gutes Ergebnis bietet. Die Ausgabe enthält MP4-Dateien mit überlappendem Video und Audio. Mit dieser Voreinstellung werden nur Ausgaben bis zu 1080p HD erzeugt. Wenn eine 4K-Ausgabe erforderlich ist, können Sie die Voreinstellung mit [PresetConfigurations](https://github.com/Azure/azure-rest-api-specs/blob/7026463801584950d4ccbaa6b05b15d29555dd3a/specification/mediaservices/resource-manager/Microsoft.Media/stable/2021-06-01/Encoding.json#L2397) konfigurieren, indem Sie die Eigenschaft „maxBitrateBps“ verwenden. Weitere Informationen finden Sie unter [Inhaltsbezogene Codierung](encode-content-aware-concept.md).
- **EncoderNamedPreset.H265ContentAwareEncoding**: Stellt eine Voreinstellung für die inhaltsbezogene HEVC (H.265)-Codierung zur Verfügung. Erzeugt mithilfe der inhaltsbezogenen Codierung verschiedene GOP-orientierte MP4s. Der Dienst führt eine einfache Erstanalyse für den Eingabeinhalt aus und ermittelt anhand der Ergebnisse die optimale Anzahl von Ebenen, die geeignete Bitrate und die Auflösungseinstellungen für die Bereitstellung durch adaptives Streaming. Diese Voreinstellung ist besonders effektiv für Videos mit geringer und mittlerer Komplexität, bei denen die Ausgabedateien mit niedrigeren Bitraten, aber in einer Qualität vorliegen, die dem Betrachter dennoch ein gutes Ergebnis bietet. Die Ausgabe enthält MP4-Dateien mit überlappendem Video und Audio. Mit dieser Voreinstellung können Ausgaben bis zu 4K HD erzeugt werden. Wenn eine 8K-Ausgabe erforderlich ist, können Sie die Voreinstellung mit [PresetConfigurations](https://github.com/Azure/azure-rest-api-specs/blob/7026463801584950d4ccbaa6b05b15d29555dd3a/specification/mediaservices/resource-manager/Microsoft.Media/stable/2021-06-01/Encoding.json#L2397) konfigurieren, indem Sie die Eigenschaft „maxBitrateBps“ verwenden.
  > [!NOTE]
  > Verwenden Sie unbedingt **ContentAwareEncoding**, nicht ContentAwareEncodingExperimental, da diese Voreinstellung jetzt veraltet ist.

- **EncoderNamedPreset.H264MultipleBitrate1080p**: Erzeugt einen Satz aus acht MP4-Dateien mit GOP-Ausrichtung und Werten zwischen 6.000 KBit/s und 400 KBit/s sowie Stereo-AAC-Audio. Die Auflösung beginnt bei 1080p und reduziert sich auf 360p.
- **EncoderNamedPreset.H264MultipleBitrate720p**: Erzeugt einen Satz aus sechs MP4-Dateien mit GOP-Ausrichtung und Werten zwischen 3.400 KBit/s und 400 KBit/s sowie Stereo-AAC-Audio. Die Auflösung beginnt bei 720p und reduziert sich auf 360p.
- **EncoderNamedPreset.H264MultipleBitrateSD**: Erzeugt einen Satz aus fünf MP4-Dateien mit GOP-Ausrichtung und Werten zwischen 1.600 KBit/s und 400 KBit/s sowie Stereo-AAC-Audio. Die Auflösung beginnt bei 480p und reduziert sich auf 360p.
- **EncoderNamedPreset.H264SingleBitrate1080p**: Erzeugt eine MP4-Datei, bei der das Video mit dem H.264-Codec mit 6.750 KBit/s und einer Bildhöhe von 1.080 Pixeln codiert wird, während die Stereo-Audiodaten mit dem AAC-LC-Codec mit 64 KBit/s codiert werden.
- **EncoderNamedPreset.H264SingleBitrate720p**: Erzeugt eine MP4-Datei, bei der das Video mit dem H.264-Codec mit 4.500 KBit/s und einer Bildhöhe von 720 Pixeln codiert wird, während die Stereo-Audiodaten mit dem AAC-LC-Codec mit 64 KBit/s codiert werden.
- **EncoderNamedPreset.H264SingleBitrateSD**: Erzeugt eine MP4-Datei, bei der das Video mit dem H.264-Codec mit 2.200 KBit/s und einer Bildhöhe von 480 Pixeln codiert wird, während die Stereo-Audiodaten mit dem AAC-LC-Codec mit 64 KBit/s codiert werden.
- **EncoderNamedPreset.H265SingleBitrate720P:** erzeugt eine MP4-Datei, bei der das Video mit dem HEVC-Codec (H.265) mit 1800 KBit/s und einer Bildhöhe von 720 Pixeln codiert wird, während die Stereoaudiodaten mit dem AAC-LC-Codec mit 128 KBit/s codiert werden.
- **EncoderNamedPreset.H265SingleBitrate1080p:** erzeugt eine MP4-Datei, bei der das Video mit dem HEVC-Codec (H.265) mit 3500 KBit/s und einer Bildhöhe von 1080 Pixeln codiert wird, während die Stereoaudiodaten mit dem AAC-LC-Codec mit 128 KBit/s codiert werden.
- **EncoderNamedPreset.H265SingleBitrate4K:** erzeugt eine MP4-Datei, bei der das Video mit dem HEVC-Codec (H.265) mit 9500 KBit/s und einer Bildhöhe von 2160 Pixeln codiert wird, während die Stereoaudiodaten mit dem AAC-LC-Codec mit 128 KBit/s codiert werden.

Die aktuellste Liste der Voreinstellungen finden Sie unter [Integrierte Voreinstellungen zum Codieren von Videos](/rest/api/media/transforms/createorupdate#encodernamedpreset).

Informationen zur Verwendung der Voreinstellungen finden Sie unter [Hochladen, Codieren und Streamen von Dateien](stream-files-tutorial-with-api.md).

### <a name="standardencoderpreset"></a>StandardEncoderPreset

[StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) beschreibt die Einstellungen, die beim Codieren des Eingabevideos mit dem Standard-Encoder verwendet werden sollen. Verwenden Sie diese Voreinstellung, wenn Sie Transformationseinstellungen anpassen.

#### <a name="considerations"></a>Überlegungen

Beim Erstellen von benutzerdefinierten Voreinstellungen gelten die folgenden Überlegungen:

- Alle Werte für Höhe und Breite in AVC-Inhalten müssen ein Vielfaches von 4 sein.
- In Azure Media Services v3 werden alle Codierungsbitraten in Bits pro Sekunde angegeben. Dies unterscheidet sich von den Voreinstellungen bei unseren v2-APIs. Dort wurden Kilobits pro Sekunde (KBit/s) als Einheit verwendet. Wenn beispielsweise die Bitrate in v2 als 128 (Kilobits/Sekunde) angegeben wurde, würde sie in v3 auf 128.000 (Bits/Sekunde) festgelegt.

### <a name="customizing-presets"></a>Anpassen von Voreinstellungen

Media Services unterstützt die vollständige Anpassung aller Werte in Voreinstellungen zum Erfüllen Ihrer spezifischen Codierungsanforderungen. Beispiele zum Anpassen von Encodervoreinstellungen finden Sie in der nachstehenden Liste:

#### <a name="examples"></a>Beispiele

- [Anpassen von Voreinstellungen mit .NET](transform-custom-presets-how-to.md)
- [Anpassen von Voreinstellungen mit der CLI](transform-custom-preset-cli-how-to.md)
- [Anpassen von Voreinstellungen mit REST](transform-custom-preset-rest-how-to.md)


## <a name="preset-schema"></a>Voreinstellungsschemas

In Media Services v3 sind Voreinstellungen stark typisierte Entitäten in der API selbst. Die „Schema“-Definition für diese Objekte finden Sie in [Offene API-Spezifikation (oder Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01). Sie können die voreingestellten Definitionen (wie **StandardEncoderPreset**) auch in der [REST-API](/rest/api/media/transforms/createorupdate#standardencoderpreset), im [.NET SDK](/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset) oder eine andere Referenzdokumentation für das Media Services v3 SDK anzeigen.

## <a name="scaling-encoding-in-v3"></a>Skalieren der Codierung in v3

Informationen zum Skalieren der Medienverarbeitung finden Sie unter [Skalieren mit CLI](media-reserved-units-cli-how-to.md).
Für Konten, die in der Version **2020-05-01** der API oder über das Azure-Portal erstellt werden, sind Skalierungseinheiten und reservierte Einheiten für Medien nicht mehr erforderlich. Die Skalierung erfolgt automatisch und erfolgt intern durch den Dienst.

## <a name="billing"></a>Abrechnung

Bei Media Services erfolgt keine Abrechnung für abgebrochene oder fehlerhafte Aufträge. Beispielsweise wird ein Auftrag, der einen Status von 50 % erreicht hat und abgebrochen wird, nicht mit 50 % der Auftragsminuten berechnet. Ihnen werden nur abgeschlossene Aufträge in Rechnung gestellt.

Weitere Informationen finden Sie unter [Azure Data Lake Storage – Preise](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="ask-questions-give-feedback-get-updates"></a>Fragen stellen, Feedback geben, Updates abrufen

Im Artikel [Azure Media Services-Community](media-services-community.md) finden Sie verschiedene Möglichkeiten, Fragen zu stellen, Feedback zu geben und Updates zu Media Services zu bekommen.

## <a name="next-steps"></a>Nächste Schritte

* [Hochladen, Codieren und Streamen mit Media Services](stream-files-tutorial-with-api.md)
* [Codieren aus einer HTTPS-URL mithilfe von integrierten Voreinstellungen](job-input-from-http-how-to.md)
* [Codieren einer lokalen Datei mithilfe von integrierten Voreinstellungen](job-input-from-local-file-how-to.md)
* [Entwickeln einer benutzerdefinierten Voreinstellung für Ihr spezielles Szenario oder Ihre Geräteanforderungen](transform-custom-presets-how-to.md)
