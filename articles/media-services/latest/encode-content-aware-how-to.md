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
ms.date: 08/17/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: b6537547db2bb3371b6d7b4cd36ebf9ffc48530c
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122429883"
---
# <a name="how-to-use-the-content-aware-encoding-preset"></a>Verwenden der Voreinstellung für die inhaltsbezogene Codierung

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="introduction"></a>Einführung
Die [Voreinstellung für die inhaltsbezogene Codierung](encode-content-aware-how-to.md) bereitet Inhalte besser für die Übermittlung durch [Adaptive Bitrate Streaming](encode-autogen-bitrate-ladder.md) vor, bei dem Videos mit mehreren Bitraten codiert werden müssen. Es umfasst eine benutzerdefinierte Logik, die es dem Encoder ermöglicht, den optimalen Bitratenwert für eine bestimmte Auflösung zu finden, ohne dass umfangreiche Berechnungsanalysen erforderlich sind. Die Voreinstellung bestimmt die optimale Anzahl der Ebenen, die geeignete Bitrate und die Auflösungseinstellungen für die Bereitstellung durch adaptives Streaming. 

Im Vergleich zur Voreinstellung für adaptives Streaming ist die inhaltsbezogene Codierung für Videos mit niedriger und mittlerer Komplexität effektiver, bei denen die Ausgabedateien eine niedrigere Bitrate erhalten, aber dennoch eine Qualität aufweisen, die eine gute Anzeigeerfahrung bietet.

## <a name="use-the-content-aware-encoding-preset"></a>Verwenden der Voreinstellung für die inhaltsbezogene Codierung
Sie können Transformationen erstellen, die diese Voreinstellung wie folgt verwenden.

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

Unter [Nächste Schritte](#next-steps) finden Sie Links zu Tutorials für die Ausgabetransformation. Das Ausgabemedienobjekt kann von Media Services-Streamingendpunkten in Protokollen wie MPEG-DASH und HLS bereitgestellt werden (wie in den Tutorials gezeigt).

> [!NOTE]
> Codierungsaufträge mit der Voreinstellung `ContentAwareEncoding` werden ausschließlich auf der Grundlage der Ausgabeminuten abgerechnet. AMS verwendet eine Codierung mit zwei Durchläufen und es fallen keine zusätzlichen Kosten an, wenn Sie eine der Voreinstellungen verwenden, abgesehen von dem, was auf unserer [Seite mit der Preisübersicht](https://azure.microsoft.com/pricing/details/media-services/#overview) aufgeführt ist.
  
## <a name="next-steps"></a>Nächste Schritte

* [Tutorial: Hochladen, Codieren und Streamen von Videos mit Media Services v3](stream-files-tutorial-with-api.md)
* [Tutorial: Codieren einer Remotedatei anhand einer URL und Streamen des Videos über – REST](stream-files-tutorial-with-rest.md)
* [Tutorial: Codieren einer Remotedatei anhand einer URL und Streamen des Videos – CLI](stream-files-cli-quickstart.md)
* [Tutorial: Codieren einer Remotedatei anhand einer URL und Streamen des Videos – .NET](stream-files-dotnet-quickstart.md)
* [Tutorial: Codieren einer Remotedatei anhand einer URL und Streamen des Videos – Node.js](stream-files-nodejs-quickstart.md)
