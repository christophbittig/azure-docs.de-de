---
title: Verwenden der Voreinstellung für die inhaltsbezogene Codierung
description: In diesem Artikel wird die Verwendung der Voreinstellung für die inhaltsbezogene Codierung in Microsoft Azure Media Services v3 erläutert.
services: media-services
documentationcenter: ''
author: jiayali-ms
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 09/17/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 09d3f199cc17cc1457413f673afe3c6be0646f58
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128668619"
---
# <a name="how-to-use-the-content-aware-encoding-preset"></a>Verwenden der Voreinstellung für die inhaltsbezogene Codierung

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="introduction"></a>Einführung
Die [Voreinstellung für die inhaltsbezogene Codierung](encode-content-aware-concept.md) bereitet Inhalte besser für die Übermittlung durch [Adaptive Bitrate Streaming](encode-autogen-bitrate-ladder.md) vor, bei dem Videos mit mehreren Bitraten codiert werden müssen. Sie umfasst eine benutzerdefinierte Logik, die es dem Encoder ermöglicht, mit dem optimalen Bitratenwert für eine bestimmte Auflösung zu rendern, ohne dass umfangreiche Berechnungsanalysen erforderlich sind. Die Voreinstellung bestimmt die optimale Anzahl der Ebenen, die geeignete Bitrate und die Auflösungseinstellungen für die Bereitstellung durch adaptives Streaming.

Im Vergleich zur Voreinstellung adaptives Streaming ist die inhaltsbezogene Codierung für Videos mit niedriger und mittlerer Komplexität effektiver, bei denen die Ausgabedateien eine niedrigere Bitrate erhalten, aber dennoch eine Qualität aufweisen, die eine gute Anzeigeerfahrung bietet.

### <a name="using-the-content-aware-encoding-presets"></a>Verwenden der Voreinstellungen für die inhaltsbezogene Codierung

Verwenden Sie zum Codieren mit der inhaltsbezogenen Voreinstellung in Ihrem Code das Objekt [BuiltInStandardEncoderPreset](/dotnet/api/microsoft.azure.management.media.models.builtinstandardencoderpreset), und legen Sie die Eigenschaft [PresetName](/dotnet/api/microsoft.azure.management.media.models.builtinstandardencoderpreset.presetname) auf einen der folgenden Namen der integrierten Voreinstellungen fest.

- **ContentAwareEncoding**: Diese Voreinstellung unterstützt H.264.
- **H265ContentAwareEncoding**: Diese Voreinstellung unterstützt HEVC (H.265).

> [!NOTE]
> Achten Sie darauf, die Einstellung **ContentAwareEncoding**, nicht „ContentAwareEncodingExperimental“ zu verwenden. Wenn Sie mit HEVC codieren möchten, können Sie **H265ContentAwareEncoding** verwenden.

```csharp
TransformOutput[] output = new TransformOutput[]
{
   new TransformOutput
   {
      // The preset for the Transform is set to one of Media Services built-in sample presets.
      // You can customize the encoding settings by changing this to use "StandardEncoderPreset" class.
      Preset = new BuiltInStandardEncoderPreset()
      {
         // This sample uses the new preset for content-aware encoding
         PresetName = EncoderNamedPreset.ContentAwareEncoding
      }
   }
};
```

## <a name="configure-output-settings"></a>Konfigurieren von Ausgabeeinstellungen

Darüber hinaus können Entwickler auch die Bandbreite der Ausgaben steuern, die von der inhaltsbezogenen Codierungsvoreinstellung verwendet werden, wenn sie in der Streamingleiter mit adaptiver Bitrate die optimalen Einstellungen für die Codierung festlegen.

Mithilfe der **PresetConfigurations**-Klasse können Entwickler eine Reihe von Einschränkungen und Optionen an die inhaltsbezogene Codierungsvoreinstellung übergeben, um die vom Encoder generierten resultierenden Dateien zu steuern. Die Eigenschaften sind besonders nützlich in Situationen, in denen Sie die gesamte Codierung auf eine bestimmte maximale Auflösung beschränken möchten, um den vermittelten Eindruck oder die Kosten Ihrer Codierungsaufträge zu steuern.  Es ist außerdem nützlich, die maximale und minimale Bitrate steuern zu können, die Ihre Zielgruppe in einem Mobilfunknetz oder in einer globalen Region mit eingeschränkter Bandbreite unterstützen kann.

Mit der PresetConfigurations-Klasse können Sie die folgenden Einstellungen für die inhaltsbezogenen Codierungsvoreinstellungen anpassen. 


| Eigenschaft                   | Beschreibung                                                                                                                                                                         |
| ------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Komplexität              | Speed, Balanced oder Quality Ermöglicht Ihnen das Konfigurieren der Encodereinstellungen, um das Gleichgewicht zwischen Geschwindigkeit und Qualität zu steuern. Beispiel: Legen Sie Komplexität auf „Speed“ fest, um die Codierung zu beschleunigen, jedoch um den Preis einer geringeren Effizienz der Komprimierung. |
| interleaveOutput          |    Steuert das Format der MP4-Ausgabedatei.  Sie kann sowohl Audio als auch Video verschachtelt enthalten, was das Teilen der einzelnen Dateien vereinfacht, oder sie kann in Audio- und Videodateien aufgeteilt sein, was das späte Binden zusätzlicher Sprachaudiospuren oder das spätere Ändern der Audiospuren erleichtert.                                                                                                                                                                                 |
| keyFrameIntervalInSeconds |        Legt den Keyframeabstand fest, der beim Codieren der Dateien für jede Gruppe von Bildern (GOP) verwendet wird.  Typische moderne adaptive Streamingprotokolle verwenden 2 Sekunden.  Abhängig davon, unter welchen Netzwerkbedingungen Sie übertragen, kann es manchmal sinnvoll sein, längere Intervalle zu verwenden. Wenden Sie sich an Ihren CDN-Anbieter, oder führen Sie in Ihren eigenen Netzwerken Experimente durch.                                                                                                                                                                             |
| maxBitrateBps             |               Schränkt die höchste Bitrate der adaptiven Streamingleiter ein.  Diese Einschränkung ist nützlich, um die Kosten für Speicher und Netzwerkbereitstellung zu steuern.  Darüber hinaus können dadurch auch die von Ihnen übermittelten Bitraten in einem Bereich gehalten werden, der von Ihren Clients unterstützt wird.                                                                                                                                                                       |
| maxHeight                 |       Die höchste Auflösung, die in der adaptiven Leiter codiert werden darf.  Dies ist ebenfalls nützlich, um die Kosten oder die Erfahrung Ihrer Zielgruppe zu steuern. Sie können alle Codierungen auf Standarddefinition oder bei Bedarf auf max. 720P beschränken.                                                                                                                                                                              |
| maxLayers                 |        Diese Eigenschaft steuert die Anzahl der Stufen in der adaptiven Streamingleiter. Dies steuert definitionsgemäß auch die Anzahl der Dateien, die als MP4 in den Speichercontainer ausgegeben werden.  Durch das Herabsetzen dieses Werts werden zugleich die Kosten gesenkt – da Sie weniger Ausgabedarstellungen erzeugen, wird die Gesamtzahl der Codierungsminuten reduziert. Verwenden Sie dies in Kombination mit maxHeight und maxBitrateBps, um die Kosten während der Codierung im Griff zu behalten und Ihre Abrechnung besser vorhersagbar zu machen.                                                                                                                                                                             |
| minBitrateBps             |                           Hiermit wird die niedrigste Bitrate gesteuert, die vom Encoder ausgewählt wird.  Verwenden Sie dies, um die Qualität Ihrer Darstellungen mit niedriger Bitrate und niedriger Auflösung zu optimieren.                                                                                                                                                         |
| minHeight                 |     Diese Einstellung steuert die kleinste Auflösung, die von der Adaptive Bitrate-Leiter erzeugt wird. Dies hilft zu vermeiden, dass Clients eine Darstellung mit sehr geringer Auflösung auswählen, die für den Typ Inhalt, den Sie codieren, möglicherweise zu unscharf ist. Dies hilft deutlich, die Qualität der Benutzererfahrung sicherzustellen, die Sie bereitstellen.                                                                                                                                                                                |
|

### <a name="example-code-for-configuring-content-aware-preset"></a>Beispielcode zum Konfigurieren einer inhaltsbezogenen Voreinstellung

Der folgende Codeausschnitt stammt aus dem Beispiel [Codieren mit inhaltsbezogener Voreinstellung, H.264 und Einschränkungen](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_H264_ContentAware_Constrained).

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoEncoding/Encoding_H264_ContentAware_Constrained/Program.cs#PresetConfigurations)]

## <a name="samples"></a>Beispiele

Die folgenden Beispiele veranschaulichen die verschiedenen Codecs und Einschränkungen, die verwendet werden können, die Instanziierung der Voreinstellung und die Übermittlung und Überwachung eines Codierungsauftrags.

### <a name="net"></a>.NET

| Beispiel | BESCHREIBUNG|
|---------|------------------|
| [Codieren mit inhaltsbezogener Voreinstellung und H.264](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_H264_ContentAware) | Veranschaulicht die einfachste Nutzung der inhaltsbezogenen H.264-Codierung ohne Einschränkungen |
| [Codieren mit inhaltsbezogener Voreinstellung, H.246 und Einschränkungen](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_H264_ContentAware_Constrained) | Veranschaulicht die Verwendung der PresetConfigurations-Klasse zum Einschränken des Ausgabeverhaltens der Voreinstellung.|
| [Codieren mit inhaltsbezogener Voreinstellung und HEVC (H265)](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_HEVC_ContentAware) | Zeigt die grundlegende Nutzung des HEVC-Codecs mit inhaltsbezogener Codierung und ohne Einschränkungen.  Die PresetConfigurations-Klasse wird auch für HEVC unterstützt und kann diesem Beispiel hinzugefügt werden.|

> [!NOTE]
> Codierungsaufträge mit der Voreinstellung `ContentAwareEncoding` werden ausschließlich auf der Grundlage der Ausgabeminuten abgerechnet. AMS verwendet eine Codierung mit zwei Durchläufen und es fallen keine zusätzlichen Kosten an, wenn Sie eine der Voreinstellungen verwenden, abgesehen von dem, was auf unserer [Seite mit der Preisübersicht](https://azure.microsoft.com/pricing/details/media-services/#overview) aufgeführt ist.
  
## <a name="next-steps"></a>Nächste Schritte

* [Tutorial: Hochladen, Codieren und Streamen von Videos mit Media Services v3](stream-files-tutorial-with-api.md)
* [Tutorial: Codieren einer Remotedatei anhand einer URL und Streamen des Videos über – REST](stream-files-tutorial-with-rest.md)
* [Tutorial: Codieren einer Remotedatei anhand einer URL und Streamen des Videos – CLI](stream-files-cli-quickstart.md)
* [Tutorial: Codieren einer Remotedatei anhand einer URL und Streamen des Videos – .NET](stream-files-dotnet-quickstart.md)
* [Tutorial: Codieren einer Remotedatei anhand einer URL und Streamen des Videos – Node.js](stream-files-nodejs-quickstart.md)
