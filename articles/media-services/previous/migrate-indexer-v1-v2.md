---
title: Migrieren von Indexer v1 und v2 zu Azure Media Services Video Indexer | Microsoft-Dokumentation
description: In diesem Thema wird erläutert, wie die Migration von Azure Media Indexer v1 und v2 zu Azure Media Services Video Indexer erfolgt.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2021
ms.author: inhenkel
ms.openlocfilehash: 3a2871bd42e6b4da9ec20dc918c5c0ab79ed1a64
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/27/2021
ms.locfileid: "114721549"
---
# <a name="migrate-from-media-indexer-and-media-indexer-2-to-video-analyzer-for-media"></a>Migrieren von Media Indexer und Media Indexer 2 zu Video Analyzer for Media 

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!IMPORTANT]
> Es wird empfohlen, dass Kunden von Indexer v1 und Indexer v2 auf den [Media Services v3 AudioAnalyzerPreset Basic-Modus](../latest/analyze-video-audio-files-concept.md) umsteigen. Die Medienprozessoren [Azure Media Indexer](media-services-index-content.md) und [Azure Media Indexer 2 Preview](./legacy-components.md) werden außer Betrieb genommen. Die Daten zur Einstellung finden Sie unter dem Thema [Legacykomponenten](legacy-components.md).

Azure Video Analyzer for Media baut auf Azure Media Analytics, Azure Cognitive Search, Cognitive Services (darunter Gesichtserkennungs-API, Microsoft Translator, Maschinelles Sehen-API und Custom Speech-Dienst) auf. Sie ermöglicht Ihnen, mithilfe der Video- und Audiomodelle von Video Analyzer for Media Erkenntnisse aus Ihren Videos zu gewinnen. Informationen zu den Szenarien, in denen Azure Video Analyzer for Media eingesetzt werden kann, zu den bereitgestellten Features und zum Einstieg finden Sie unter [Was ist Video Analyzer for Media?](../../azure-video-analyzer/video-analyzer-for-media-docs/video-indexer-overview.md). 

Sie können Erkenntnisse aus Ihren Video- und Audiodateien ziehen, indem Sie die [Azure Media Services v3-Analysevoreinstellungen](../latest/analyze-video-audio-files-concept.md) nutzen oder direkt die [Video Analyzer for Media-APIs](https://api-portal.videoindexer.ai/) verwenden. Derzeit gibt es eine Überschneidung bei den Features der APIs von Video Analyzer for Media und der APIs von Media Services v3.

> [!NOTE]
> Die Unterschiede zwischen den Voreinstellungen von Video Analyzer for Media und denen des Media Services-Analysetools werden im zugehörigen [Vergleichsdokument](../../azure-video-analyzer/video-analyzer-for-media-docs/compare-video-indexer-with-media-services-presets.md) erläutert.

In diesem Artikel werden die Schritte zum Migrieren von Azure Media Indexer und Azure Media Indexer 2 zu Video Analyzer for Media erläutert.  

## <a name="migration-options"></a>Migrationsoptionen

|Anforderung  |Erforderliche Vorgehensweise |
|---|---|
|eine Lösung, die eine Spracherkennungs-Transkription für beliebige Mediendatei Formate in einem Untertitel-Dateiformat bereitstellt: VTT, SRT oder TTLL<br/>sowie zusätzliche Erkenntnisse über die Audiospur, wie etwa: Schlüsselwörter, Schließen auf Themen, akustische Ereignisse, Sprecherdiarisierung, Extrahierung von Entitäten und Übersetzung| Aktualisieren Sie Ihre Anwendungen über die Video Analyzer for Media v2-REST-API oder über Azure Media Services v3 AudioAnalyzerPreset für die Verwendung der Video Analyzer for Media-Funktionen.|
|Spracherkennungsfunktionen| verwenden Sie direkt die Cognitive Services Speech-API.|  

## <a name="getting-started-with-video-analyzer-for-media"></a>Erste Schritte mit Video Analyzer for Media

Der folgende Abschnitt verweist auf relevante Links: [Wie kann ich mich mit Video Analyzer for Media vertraut machen?](../../azure-video-analyzer/video-analyzer-for-media-docs/video-indexer-overview.md#how-can-i-get-started-with-video-analyzer-for-media) 

## <a name="getting-started-with-media-services-v3-apis"></a>Erste Schritte mit Media Services v3-APIs

Mit der Azure Media Services (AMS) v3-API können Sie Inhalte aus Video- und Audiodateien mit den [Azure Media Services v3-Analyse-Einstellungen](../latest/analyze-video-audio-files-concept.md) extrahieren.

**AudioAnalyzerPreset** ermöglicht Ihnen, mehrere Audioinformationen aus einer Audio- oder Videodatei zu extrahieren. Die Ausgabe beinhaltet eine VTT- oder TTML-Datei für das Audiotranskript und eine JSON-Datei (mit allen zusätzlichen Audio-Erkenntnissen). Zu den Audio-Erkenntnissen gehören Schlüsselwörter, Sprecherindizierung und Sprachempfindungsanalyse. AudioAnalyzerPreset unterstützt außerdem für bestimmte Sprachen die Spracherkennung. Ausführliche Informationen finden Sie unter [Transformationen](/rest/api/media/transforms/createorupdate#audioanalyzerpreset).

### <a name="get-started"></a>Erste Schritte

Informationen zu den ersten Schritten finden Sie unter:

* [Tutorial](../latest/analyze-videos-tutorial.md)
* AudioAnalyzerPreset-Beispiele: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer) oder [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer)
* VideoAnalyzerPreset-Beispiele: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer) oder [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/VideoAnalytics/VideoAnalyzer)

## <a name="getting-started-with-cognitive-services-speech-services"></a>Erste Schritte mit den Cognitive Services-Spracherkennungsdiensten

[Azure Cognitive Services](../../cognitive-services/index.yml) bietet einen Spracherkennungsdienst, der Audiodatenströme in Echtzeit in Text umwandelt, der von Ihren Anwendungen, Tools oder Geräten genutzt oder angezeigt werden kann. Sie können die Spracherkennung verenden, um [Ihr eigenes Akustikmodell, Sprachmodell oder Aussprachemodell anzupassen](../../cognitive-services/speech-service/how-to-custom-speech-train-model.md). Weitere Informationen finden Sie unter [Cognitive Services-Spracherkennung](../../cognitive-services/speech-service/speech-to-text.md). 

> [!NOTE] 
> Der Spracherkennungsdienst akzeptiert keine Videodateiformate und auch nur [bestimmte Audioformate](../../cognitive-services/speech-service/rest-speech-to-text.md#audio-formats). 

Weitere Informationen zum Spracherkennungsdienst und Ihren ersten Schritten damit finden Sie unter [Was ist die Spracherkennung?](../../cognitive-services/speech-service/speech-to-text.md)

## <a name="known-differences-from-deprecated-services"></a>Bekannte Unterschiede zu veralteten Diensten

Sie werden feststellen, dass Video Analyzer for Media, Azure Media Services v3 AudioAnalyzerPreset und die Cognitive Services-Spracherkennungsdienste zuverlässiger sind und Ausgaben höherer Qualität erzeugen als die außer Betrieb genommenen Azure Media Indexer 1- und Azure Media Indexer 2-Prozessoren.  

Dies sind einige der bekannten Unterschiede:

* Die Cognitive Services-Spracherkennungsdienste unterstützen keine Extrahierung von Schlüsselwörtern. Jedoch bieten sowohl Video Analyzer for Media als auch Media Services v3 AudioAnalyzerPreset eine stabilere Menge von Schlüsselwörtern im JSON-Dateiformat.

## <a name="support"></a>Support

Sie können ein Supportticket erstellen, indem Sie zu [Neue Supportanfrage ](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) navigieren.

## <a name="next-steps"></a>Nächste Schritte

* [Legacy-Komponenten](legacy-components.md)
* [Seite mit der Preisübersicht](https://azure.microsoft.com/pricing/details/media-services/#encoding)
