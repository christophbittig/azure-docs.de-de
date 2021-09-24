---
title: Legacy-Komponenten von Azure Media Services | Microsoft-Dokumentation
description: In diesem Thema werden die Legacy-Komponenten von Azure Media Services behandelt.
services: media-services
documentationcenter: ''
author: jiayali-ms
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2021
ms.author: inhenkel
ms.openlocfilehash: d86a77e724bffeaea6cb39ffc1a20e8737ef6d81
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122835538"
---
# <a name="azure-media-services-legacy-components"></a>Legacy-Komponenten von Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Im Laufe der Zeit verbessern wir Media Service-Komponenten und nehmen Legacy-Komponenten außer Betrieb. Dieser Artikel unterstützt Sie bei der Migration Ihrer Anwendung von einer Legacy- zu einer aktuellen Komponente.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Deaktivierungspläne für Legacy-Komponenten und Hinweise zur Migration

Die Medienprozessoren *Windows Azure Media Encoder* (WAME) und *Azure Media Encoder* (AME) sind veraltet.

* [Migrieren von Windows Azure Media Encoder zu Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Migrieren von Azure Media Encoder zu Media Encoder Standard](migrate-azure-media-encoder.md)

Die folgenden Media Analytics-Medienprozessoren sind entweder veraltet oder gelten bald als veraltet:

 
| **Medienprozessorname** | **Datum der Deaktivierung** | **Zusätzliche Hinweise** |
| --- | --- | ---|
| Azure Media Indexer | 1\. Januar 2020 | Dieser Medienprozessor wird durch den [AudioAnalyzerPreset-Standardmodus von Media Services v3](../latest/analyze-video-audio-files-concept.md) ersetzt. Weitere Informationen finden Sie unter [Migration von Azure Media Indexer 2 zu Azure Video Analyzer for Media](migrate-indexer-v1-v2.md) (ehemals Video Indexer). |
| Azure Media Indexer 2 | 1\. März 2023 | Dieser Medienprozessor wird durch den [AudioAnalyzerPreset-Standardmodus von Media Services v3](../latest/analyze-video-audio-files-concept.md) ersetzt. Weitere Informationen finden Sie unter [Migration von Azure Media Indexer 2 zu Azure Video Analyzer for Media](migrate-indexer-v1-v2.md) (ehemals Video Indexer). |
| Bewegungserkennung | 1\. Juni 2020|Derzeit keine Austauschpläne. |
| Videozusammenfassung |1\. Juni 2020|Derzeit keine Austauschpläne.|
| Optische Zeichenerkennung in Video | 1\. Juni 2020 |Dieser Medienprozessor wurde durch Azure Video Analyzer for Media ersetzt. Erwägen Sie auch den Einsatz der [Azure Media Services v3-API](../latest/analyze-video-audio-files-concept.md). <br/>Weitere Informationen finden Sie unter [Vergleich der Voreinstellungen von Azure Media Services v3 und Video Analyzer for Media](../../azure-video-analyzer/video-analyzer-for-media-docs/compare-video-indexer-with-media-services-presets.md). |
| Face Detector | 1\. Juni 2020 | Dieser Medienprozessor wurde durch Azure Video Analyzer for Media ersetzt. Erwägen Sie auch den Einsatz der [Azure Media Services v3-API](../latest/analyze-video-audio-files-concept.md). <br/>Weitere Informationen finden Sie unter [Vergleich der Voreinstellungen von Azure Media Services v3 und Video Analyzer for Media](../../azure-video-analyzer/video-analyzer-for-media-docs/compare-video-indexer-with-media-services-presets.md). |
| Content Moderator | 1\. Juni 2020 |Dieser Medienprozessor wurde durch Azure Video Analyzer for Media ersetzt. Erwägen Sie auch den Einsatz der [Azure Media Services v3-API](../latest/analyze-video-audio-files-concept.md). <br/>Weitere Informationen finden Sie unter [Vergleich der Voreinstellungen von Azure Media Services v3 und Video Analyzer for Media](../../azure-video-analyzer/video-analyzer-for-media-docs/compare-video-indexer-with-media-services-presets.md). |
| Media Encoder Premium Workflow | 29. Februar 2024 | Der Premium-Encoder wird von der AMS v2-API nicht mehr unterstützt. Wenn Sie zuvor den workflowbasierten Premium-Encoder für die HEVC-Codierung verwendet haben, sollten Sie zum neuen [v3-Standard-Encoder](../latest/encode-media-encoder-standard-formats-reference.md) mit Unterstützung für die HEVC-Codierung migrieren. <br/> Wenn Sie die erweiterten Workflowfunktionen des Premium-Encoders benötigen, sollten Sie einen der erweiterten Partner für die Codierung in Azure von [Imagine Communications](https://imaginecommunications.com/), [Telestream](https://telestream.net) oder [Bitmovin](https://bitmovin.com) einsetzen. |

## <a name="next-steps"></a>Nächste Schritte

[Hinweise zur Migration von Media Services v2 zu v3](../latest/migrate-v-2-v-3-migration-introduction.md)
