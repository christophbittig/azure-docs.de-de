---
title: Zu beachtende Punkte bei der Verwendung von Azure Video Analyzer for Media (früher Video Indexer) im großen Stil – Azure
titleSuffix: Azure Video Analyzer for Media
description: In diesem Thema werden die zu beachtenden Punkte bei der Verwendung von Azure Video Analyzer for Media (früher Video Indexer) im großen Stil behandelt.
services: azure-video-analyzer
author: Juliako
manager: femila
ms.topic: how-to
ms.subservice: azure-video-analyzer-media
ms.date: 11/13/2020
ms.author: juliako
ms.openlocfilehash: 8784b82c59575a569730949d71473027cd30479a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355229"
---
# <a name="things-to-consider-when-using-video-analyzer-for-media-at-scale"></a>Zu beachtende Punkte bei der Verwendung von Video Analyzer for Media im großen Stil

Wenn Sie Azure Video Analyzer for Media (früher Video Indexer) zur Indizierung von Videos verwenden und Ihr Videoarchiv immer größer wird, sollten Sie eine Skalierung in Betracht ziehen. 

In diesem Artikel werden Fragen wie die folgenden beantwortet:

* Gibt es technologische Einschränkungen, die ich berücksichtigen muss?
* Gibt es eine intelligente und effiziente Vorgehensweise?
* Kann ich verhindern, dass im Prozess zu viel Geld ausgegeben wird?

Der Artikel enthält sechs bewährte Methoden für die Verwendung von Video Analyzer for Media im großen Stil.

## <a name="when-uploading-videos-consider-using-a-url-over-byte-array"></a>Erwägen Sie beim Hochladen von Videos die Verwendung einer URL anstelle eines Bytearrays.

Video Analyzer for Media bietet Ihnen die Option, Videos über eine URL hochzuladen oder die Datei direkt als Bytearray zu senden, wobei letzteres mit einigen Einschränkungen verbunden ist. Weitere Informationen finden Sie unter [Überlegungen und Einschränkungen zum Hochladen](upload-index-videos.md#uploading-considerations-and-limitations).

Zunächst weist es Einschränkungen bei der Dateigröße auf. Die Größe der Bytearray-Datei ist auf 2 GB begrenzt, während die Größenbeschränkung beim Hochladen unter Verwendung einer URL bei 30 GB liegt.

Betrachten Sie zudem nur einige der Probleme, die Ihre Leistung und damit Ihre Möglichkeit zur Skalierung beeinflussen können:

* Das Versenden von Dateien in mehreren Teilen bedeutet eine hohe Abhängigkeit von Ihrem Netzwerk, 
* Dienstzuverlässigkeit, 
* Konnektivität, 
* Uploadgeschwindigkeit, 
* verlorene Pakete im World Wide Web.

:::image type="content" source="./media/considerations-when-use-at-scale/first-consideration.png" alt-text="Erste Überlegung zur Verwendung von Video Analyzer for Media im großen Stil":::

Wenn Sie Videos mithilfe einer URL hochladen, müssen Sie lediglich einen Pfad zum Speicherort einer Mediendatei angeben, und Video Indexer kümmert sich um den Rest (siehe das Feld `videoUrl` in der API zum [Hochladen von Videos](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video)).

> [!TIP]
> Verwenden Sie den optionalen Parameter `videoUrl` der API zum Hochladen von Videos.

Ein Beispiel für das Hochladen von Videos mithilfe einer URL finden Sie unter [diesem Beispiel](upload-index-videos.md#code-sample). Oder Sie können [AzCopy](../../storage/common/storage-use-azcopy-v10.md) als schnelle und zuverlässige Methode verwenden, um Ihren Inhalt auf ein Speicherkonto zu übertragen, von dem aus Sie ihn mit [SAS URL](../../storage/common/storage-sas-overview.md) an Video Analyzer for Media übermitteln können. Video Analyzer for Media empfiehlt, *schreibgeschützte* SAS-URLs zu verwenden.

## <a name="automatic-scaling-of-media-reserved-units"></a>Automatische Skalierung reservierter Einheiten für Medien 

Seit dem 1. August 2021 ermöglicht Azure Video Analyzer for Media (ehemals Video Indexer) die automatische Skalierung von [reservierten Einheiten](../../media-services/latest/concept-media-reserved-units.md)(Media Reservced Units, MRUs) durch [Azure Media Services](../../media-services/latest/media-services-overview.md) (AMS), sodass Sie diese nicht mehr über Azure Video Analyzer for Media verwalten müssen. Dies ermöglicht eine Preisoptimierung, z. B. in vielen Fällen eine Preissenkung, die sich an den Anforderungen Ihres Unternehmens orientiert, während es automatisch skaliert wird. 

## <a name="respect-throttling"></a>Respektieren von Einschränkungen

Video Analyzer for Media ist für die Indizierung im großen Stil konzipiert, und wenn Sie es optimal nutzen möchten, sollten Sie sich auch über die Funktionen des Systems im Klaren sein und Ihre Integration entsprechend gestalten. Sie möchten keine Uploadanforderung für einen Batch von Videos senden, nur um festzustellen, dass einige der Filme nicht hochgeladen wurden und Sie einen HTTP 429-Antwortcode erhalten (aufgrund zu vieler Anforderungen). Das kann dadurch bedingt sein, dass Sie mehr Anforderungen gesendet haben, als der [von uns unterstützte Grenzwert von Filmen pro Minute](upload-index-videos.md#uploading-considerations-and-limitations) gestattet. Video Analyzer for Media fügt in der HTTP-Antwort einen `retry-after`-Header hinzu, der angibt, wann Sie es das nächste Mal versuchen sollten. Stellen Sie sicher, dass Sie ihn beachten, bevor Sie es mit Ihrer nächsten Anforderung versuchen.

:::image type="content" source="./media/considerations-when-use-at-scale/respect-throttling.jpg" alt-text="Entwerfen Sie eine geeignete Integration, respektieren Sie die Einschränkung":::

## <a name="use-callback-url"></a>Verwenden einer Rückruf-URL

Wir empfehlen, dass Sie, anstatt den Status Ihrer Anforderung ab der Sekunde, in der Sie die Uploadanforderung gesendet haben, ständig abrufen, eine [Rückruf-URL](upload-index-videos.md#callbackurl) hinzufügen und warten, bis Sie von Video Analyzer for Media informiert werden. Sobald es eine Statusänderung in Ihrer Uploadanforderung gibt, erhalten Sie eine POST-Benachrichtigung an die von Ihnen angegebene URL.

Sie können eine Rückruf-URL als einen der Parameter der [API zum Hochladen von Videos](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) hinzufügen. Sehen Sie sich die Codebeispiele im [GitHub-Repository](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/) an. 

Als Rückruf-URL können Sie auch Azure Functions verwenden, eine serverlose ereignisgesteuerte Plattform, die über HTTP ausgelöst werden kann und einen folgenden Flow implementiert.

### <a name="callback-url-definition"></a>Definition der Rückruf-URL

[!INCLUDE [callback url](./includes/callback-url.md)]

## <a name="use-the-right-indexing-parameters-for-you"></a>Verwenden Sie die für Sie richtigen Indizierungsparameter

Wenn Sie Entscheidungen im Zusammenhang mit der Verwendung von Video Analyzer for Media im großen Stil treffen, schauen Sie sich an, wie Sie es mit den richtigen Parametern für Ihre Anforderungen optimal nutzen können. Denken Sie über Ihren Anwendungsfall nach. Mithilfe der Definition verschiedener Parameter können Sie Geld sparen und den Indizierungsprozess für Ihre Videos beschleunigen.

Bevor Sie Ihr Video hochladen und indizieren, lesen Sie diese kurze [Dokumentation](upload-index-videos.md), und überprüfen Sie [indexingPreset](upload-index-videos.md#indexingpreset) und [streamingPreset](upload-index-videos.md#streamingpreset), um eine bessere Vorstellung von Ihren Optionen zu erhalten.

Legen Sie z. B. die Voreinstellung nicht auf Streaming fest, wenn Sie nicht vorhaben, das Video anzuschauen, und indizieren Sie keine Erkenntnisse aus Videoaufnahmen, wenn Sie nur Erkenntnisse aus Audioaufnahmen benötigen.

## <a name="index-in-optimal-resolution-not-highest-resolution"></a>Indizieren in optimaler Auflösung, nicht in höchster Auflösung

Sie fragen sich vielleicht, welche Videoqualität Sie für die Indizierung Ihrer Videos benötigen. 

In vielen Fällen besteht bei der Indizierungsleistung fast kein Unterschied zwischen HD-Videos (720P) und 4K-Videos. Schließlich werden Sie fast dieselben Erkenntnisse mit derselben Konfidenz erhalten. Je höher die Qualität des hochgeladenen Films ist, desto größer ist auch die Dateigröße, was zu einer höheren Rechenleistung und einem höheren Zeitaufwand für das Hochladen des Videos führt.

Beispielsweise kann für das Feature zur Gesichtserkennung eine höhere Auflösung in dem Szenario helfen, in dem es viele kleine, aber kontextbezogen wichtige Gesichter gibt. Dies wird jedoch mit einer quadratischen Erhöhung der Laufzeit und einem erhöhten Risiko von falsch positiven Ergebnissen einhergehen.

Wir empfehlen Ihnen daher, sich zu vergewissern, dass Sie die richtigen Ergebnisse für Ihren Anwendungsfall erhalten, und diesen zunächst lokal zu testen. Laden Sie dasselbe Video in 720P und in 4K hoch, und vergleichen Sie die gewonnenen Erkenntnisse.

## <a name="next-steps"></a>Nächste Schritte

[Untersuchen der von der API erstellten Azure Video Analyzer for Media-Ausgabe](video-indexer-output-json-v2.md)