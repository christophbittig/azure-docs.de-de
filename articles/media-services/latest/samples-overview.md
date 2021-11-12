---
title: Media Services v3-Beispiele
description: Dieser Artikel enthält eine Liste aller verfügbaren Beispiele für Media Services V3, geordnet nach Methode und SDK.  Es gibt Beispiele für .NET, Node.JS, Python und Java sowie REST mit Postman.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: overview
ms.date: 03/24/2021
ms.author: inhenkel
ms.openlocfilehash: 1f7397423eef2969e3a3e61b2201770a1e54a2ed
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131438347"
---
# <a name="media-services-v3-samples"></a>Media Services v3-Beispiele

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Dieser Artikel enthält eine Liste aller verfügbaren Beispiele für Media Services, geordnet nach Methode und SDK.  Es gibt Beispiele für .NET, Node.js (TypeScript), Python und Java sowie REST mit Postman.

## <a name="samples-by-sdk"></a>Beispiele nach SDK

Auf den einzelnen Registerkarten finden Sie eine Beschreibung und Links zu den Beispielen, die Sie möglicherweise suchen.

## <a name="net"></a>[.NET](#tab/net/)

| Beispiel | BESCHREIBUNG |
|-------------|-------------|
| [Account/CreateAccount](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Account/CreateAccount)|Das Beispiel zeigt, wie Sie ein Media Services-Konto erstellen und das primäre Speicherkonto festlegen, sowie erweiterte Konfigurationseinstellungen, wie z. B. eine Liste zugelassener IP-Adressen für die Schlüsselübermittlung, verwaltete Identität, Speicherauthentifizierung und die Verwendung eigener Verschlüsselungsschlüssel.|
| [VideoEncoding/Encoding_PredefinedPreset](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_PredefinedPreset)|Das Beispiel zeigt die Vorgehensweise zum Übermitteln eines Auftrags mithilfe einer integrierten Voreinstellung und der Eingabe einer HTTP-URL, zum Veröffentlichen des Ausgabeobjekts für das Streaming und zum Herunterladen der Ergebnisse zur Überprüfung.|
| [VideoEncoding/Encoding_H264_ContentAware](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_H264_ContentAware) | Veranschaulicht die einfachste Nutzung der inhaltsbezogenen H.264-Codierung ohne Einschränkungen |
| [VideoEncoding/Encoding_H264_ContentAware_Constrained](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_H264_ContentAware_Constrained) | Veranschaulicht die Verwendung der PresetConfigurations-Klasse zum Einschränken des Ausgabeverhaltens der Voreinstellung.|
| [VideoEncoding/Encoding_H264](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_H264)|Das Beispiel zeigt die Vorgehensweise zum Übermitteln eines Auftrags mithilfe einer benutzerdefinierten H.264-Codierungsvoreinstellung und Eingabe einer HTTP-URL, zum Veröffentlichen des Ausgabeobjekts für das Streaming und zum Herunterladen der Ergebnisse zur Überprüfung.|
| [VideoEncoding/Encoding_HEVC_ContentAware](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_HEVC_ContentAware) | Zeigt die grundlegende Nutzung des HEVC-Codecs mit inhaltsbezogener Codierung und ohne Einschränkungen.  Die PresetConfigurations-Klasse wird auch für HEVC unterstützt und kann diesem Beispiel hinzugefügt werden.|
| [VideoEncoding/Encoding_HEVC](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_HEVC)|Das Beispiel zeigt die Vorgehensweise zum Übermitteln eines Auftrags mithilfe einer benutzerdefinierten HEVC-Codierungsvoreinstellung und Eingabe einer HTTP-URL, zum Veröffentlichen des Ausgabeobjekts für das Streaming und zum Herunterladen der Ergebnisse zur Überprüfung.|
| [VideoEncoding/Encoding_StitchTwoAssets](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_StitchTwoAssets)|Das Beispiel zeigt die Vorgehensweise zum Übermitteln eines Auftrags mithilfe einer JobInputSequence zum Zusammenfügen von zwei oder mehr Objekten, die möglicherweise durch die Start- oder Endzeit abgeschnitten werden. Die sich ergebende codierte Datei ist ein einzelnes Video, in dem alle Objekte zusammengefügt sind.  Im Beispiel werden zudem das Ausgabeobjekt für das Streaming veröffentlicht und die Ergebnisse zur Überprüfung heruntergeladen.|
| [VideoEncoding/Encoding_SpriteThumbnail](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_SpriteThumbnail)|Das Beispiel zeigt die Vorgehensweise zum Übermitteln eines Auftrags mithilfe einer benutzerdefinierten Voreinstellung mit Miniaturbild-Sprite und Eingabe einer HTTP-URL, zum Veröffentlichen des Ausgabeobjekts für das Streaming und zum Herunterladen der Ergebnisse zur Überprüfung.|
| [Live/LiveEventWithDVR](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Live/LiveEventWithDVR)|In diesem Beispiel wird zunächst gezeigt, wie Sie ein LiveEvent mit einem vollständigen Archiv von bis zu 25 Stunden und einen Filter für das Medienobjekt mit einem DVR-Fenster von 5 Minuten erstellen. Anschließend wird gezeigt, wie Sie den Filter verwenden, um einen Locator für das Streaming zu erstellen.|
| [VideoAnalytics/VideoAnalyzer](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoAnalytics/VideoAnalyzer)|Das Beispiel zeigt die Vorgehensweise zum Erstellen einer Videoanalysetransformation, zum Hochladen einer Videodatei in ein Eingabeobjekt, zum Einreichen eines Auftrags mit der Transformation und zum Herunterladen der Ergebnisse zur Überprüfung.|
| [AudioAnalytics/AudioAnalyzer](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/AudioAnalytics/AudioAnalyzer)|Das Beispiel zeigt die Vorgehensweise zum Erstellen einer Audioanalysetransformation, zum Hochladen einer Audiodatei in ein Eingabeobjekt, zum Einreichen eines Auftrags mit der Transformation und zum Herunterladen der Ergebnisse zur Überprüfung.|
| [ContentProtection/BasicAESClearKey](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicAESClearKey)|Das Beispiel zeigt die Vorgehensweise zum Erstellen einer Transformation mit integrierter AdaptiveStreaming-Voreinstellung, zum Übermitteln eines Auftrags, zum Erstellen einer ContentKeyPolicy mithilfe eines geheimen Schlüssels, zum Zuordnen der ContentKeyPolicy zum StreamingLocator, zum Abrufen eines Tokens und zum Drucken einer URL für die Wiedergabe in Azure Media Player. Wenn von einem Player ein Stream angefordert wird, verwendet Media Services den angegebenen Schlüssel, um Ihre Inhalte dynamisch mit AES-128 zu verschlüsseln, und Azure Media Player verwendet das Token zum Entschlüsseln.|
| [ContentProtection/BasicWidevine](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicWidevine)|Das Beispiel zeigt die Vorgehensweise zum Erstellen einer Transformation mit integrierter AdaptiveStreaming-Voreinstellung, zum Übermitteln eines Auftrags, zum Erstellen einer ContentKeyPolicy mit Widevine-Konfiguration mithilfe eines geheimen Schlüssels, zum Zuordnen der ContentKeyPolicy mit StreamingLocator, zum Abrufen eines Tokens und zum Drucken einer URL für die Wiedergabe in einem Widevine-Player. Wenn ein Benutzer die mit Widevine geschützten Inhalte anfordert, fordert die Playeranwendung eine Lizenz vom Media Services-Lizenzdienst an. Wenn die Playeranwendung autorisiert wurde, gibt der Media Services-Lizenzdienst eine Lizenz für den Player aus. Eine Widevine-Lizenz enthält den Entschlüsselungsschlüssel, der vom Clientplayer zum Entschlüsseln und Streamen des Inhalts verwendet werden kann.|
| [ContentProtection/BasicPlayReady](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicPlayReady)|Das Beispiel zeigt die Vorgehensweise zum Erstellen einer Transformation mit integrierter AdaptiveStreaming-Voreinstellung, zum Übermitteln eines Auftrags, zum Erstellen einer ContentKeyPolicy mit PlayReady-Konfiguration mithilfe eines geheimen Schlüssels, zum Zuordnen der ContentKeyPolicy mit StreamingLocator, zum Abrufen eines Tokens und zum Drucken einer URL für die Wiedergabe in einem Azure Media Player. Wenn ein Benutzer die mit PlayReady geschützten Inhalte anfordert, fordert die Playeranwendung eine Lizenz vom Media Services-Lizenzdienst an. Wenn die Playeranwendung autorisiert wurde, gibt der Media Services-Lizenzdienst eine Lizenz für den Player aus. Eine PlayReady-Lizenz enthält den Entschlüsselungsschlüssel, der vom Clientplayer zum Entschlüsseln und Streamen des Inhalts verwendet werden kann.|
| [ContentProtection/OfflinePlayReadyAndWidevine](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/OfflinePlayReadyAndWidevine)|Das Beispiel zeigt die Vorgehensweise zum dynamischen Verschlüsseln von Inhalten mit PlayReady- und Widevine-DRM und zum Wiedergeben der Inhalte ohne Anforderung einer Lizenz vom Lizenzdienst. Vorgehensweise zum Erstellen einer Transformation mit integrierter AdaptiveStreaming-Voreinstellung, zum Übermitteln eines Auftrags, zum Erstellen einer ContentKeyPolicy mit offener Einschränkung und persistenter PlayReady/Widevine-Konfiguration, zum Zuordnen einer ContentKeyPolicy zu einem StreamingLocator und zum Ausdrucken einer URL für die Wiedergabe.|
| [Streaming/AssetFilters](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/AssetFilters)|Das Beispiel zeigt die Vorgehensweise zum Erstellen einer Transformation mit integrierter AdaptiveStreaming-Voreinstellung, zum Übermitteln eines Auftrags, zum Erstellen eines Ressourcenfilters und Kontofilters, zum Zuordnen der Filter zu Streaming-Locators und zum Drucken von URLs für die Wiedergabe.|
| [Streaming/StreamHLSAndDASH](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/StreamHLSAndDASH)|Das Beispiel zeigt die Vorgehensweise zum Erstellen einer Transformation mit integrierter AdaptiveStreaming-Voreinstellung, zum Übermitteln eines Auftrags und zum Veröffentlichen eines Ausgabeobjekts für HLS- und DASH-Streaming.|
| [HighAvailabilityEncodingStreaming](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/HighAvailabilityEncodingStreaming/) | Das Beispiel bietet Leitfäden und bewährte Methoden für ein Produktionssystem, das bedarfsgesteuerte Codierung oder Analysen verwendet. Als Leser sollten Sie mit dem Begleitartikel [Hochverfügbarkeit mit Media Services und VOD](architecture-high-availability-encoding-concept.md) beginnen. Es gibt eine separate Lösungsdatei, die für das Beispiel [HighAvailabilityEncodingStreaming](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/HighAvailabilityEncodingStreaming/README.md) bereitgestellt wird. |
| [Azure Functions für Media Services](https://github.com/xpouyat/media-services-v3-dotnet-core-functions-integration/tree/main/Functions)|Dieses Projekt enthält Beispiele für Azure Functions, die zur Videoverarbeitung eine Verbindung mit Azure Media Services v3 herstellen. Sie können Visual Studio 2019 oder Visual Studio Code verwenden, um die Funktionen zu entwickeln und auszuführen. Eine ARM-Vorlage (Azure Resource Manager) und ein GitHub Actions-Workflow stehen für die Bereitstellung der Funktionsressourcen und zum Aktivieren von Continuous Deployment zur Verfügung.|

## <a name="nodejs"></a>[Node.JS](#tab/node/)

|Beispiel|BESCHREIBUNG|
|---|---|
|[Hallo Welt: Auflisten von Medienobjekten](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/HelloWorld-ListAssets/list-assets.ts)|Einfaches Beispiel zum Verbinden und Auflisten von Medienobjekten |
|[Livestreaming](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/Live/index.ts)| Einfaches Livestreaming-Beispiel. **WARNUNG**: Stellen Sie sicher, dass alle Ressourcen bereinigt sind und im Portal nicht mehr abgerechnet werden, wenn Sie Livestreaming verwenden.|
|[Hochladen und Streamen von HLS und DASH](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/StreamFilesSample/index.ts)| Einfaches Beispiel für das Hochladen einer lokalen Datei oder Codieren aus einer Quell-URL. Das Beispiel zeigt, wie Sie Storage SDK zum Herunterladen von Inhalten verwenden und wie Sie an einen Player streamen. |
|[Hochladen und Streamen von HLS und DASH mit PlayReady und Widevine DRM](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/StreamFilesWithDRMSample/index.ts)| Zeigt, wie Sie mithilfe von Widevine und PlayReady DRM codieren und streamen. |
|[Hochladen und Verwenden von KI zum Indizieren von Video- und Audiodaten](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/VideoIndexerSample/index.ts)| Beispiel für die Verwendung von Voreinstellungen für das Video- und Audioanalysetool zum Generieren von Metadaten und Erkenntnissen aus einer Video- oder Audiodatei. |

## <a name="python"></a>[Python](#tab/python)

|Beispiel|BESCHREIBUNG|
|---|---|
|[Grundlegende Codierung mit Python](https://github.com/Azure-Samples/media-services-v3-python)| Einfaches Beispiel für das Hochladen einer lokalen Datei oder Codieren aus einer Quell-URL. Das Beispiel zeigt, wie Sie Storage SDK zum Herunterladen von Inhalten verwenden und wie Sie an einen Player streamen. |
|[Gesichtsbearbeitung mithilfe von Ereignissen und Funktionen](https://github.com/Azure-Samples/media-services-v3-python/tree/main/VideoAnalytics/FaceRedactorEventBased)| Dies ist ein Beispiel für einen ereignisbasierten Ansatz, bei dem ein Azure Media Services-Auftrag zur Gesichtsbearbeitung für ein Video ausgelöst wird, sobald es in einem Azure Storage-Konto hinzugefügt wird. Für die Lösung werden Azure Media Services, Azure Functions, Event Grid und Azure Storage verwendet. Eine vollständige Beschreibung der Lösung finden Sie in der Datei [README.md](https://github.com/Azure-Samples/media-services-v3-python/blob/main/VideoAnalytics/FaceRedactorEventBased/README.md).|


## <a name="java"></a>[Java](#tab/java)

|Beispiel|BESCHREIBUNG|
|---|---|
|[AudioAnalytics/AudioAnalyzer/](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer)|Vorgehensweise zum Analysieren von Audiodaten in einer Mediendatei. |
|Content Protection|
|ContentProtection||
|[BasicAESClearKey](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicAESClearKey)|Vorgehensweise zum dynamischen Verschlüsseln von Inhalten mit AES-128.|
|[BasicPlayReady](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicPlayReady)|Vorgehensweise zum dynamischen Verschlüsseln von Inhalten mit PlayReady-DRM.|
|[BasicWidevine](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicWidevine)|Vorgehensweise zum dynamischen Verschlüsseln von Inhalten mit Widevine-DRM.|
|[OfflineFairPlay](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/OfflineFairPlay)|Vorgehensweise zum dynamischen Verschlüsseln von Inhalten mit FairPlay-DRM und zum Wiedergeben der Inhalte ohne Anforderung einer Lizenz vom Lizenzdienst.|
|[OfflinePlayReadyAndWidevine](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/OfflinePlayReadyAndWidevine)|Vorgehensweise zum dynamischen Verschlüsseln von Inhalten mit PlayReady- und Widevine-DRM und zum Wiedergeben der Inhalte ohne Anforderung einer Lizenz vom Lizenzdienst.|
|DynamicPackagingVODContent||
|[AssetFilters](https://github.com/Azure-Samples/media-services-v3-java/tree/master/DynamicPackagingVODContent/AssetFilters)|Vorgehensweise zum Filtern von Inhalten mithilfe von Objekt- und Kontofiltern.|
|[StreamHLSAndDASH](https://github.com/Azure-Samples/media-services-v3-java/tree/master/DynamicPackagingVODContent/StreamHLSAndDASH)|Vorgehensweise zum dynamischen Verpacken von VOD-Inhalten in HLS/Dash für das Streaming.|
|[LiveIngest/LiveEventWithDVR](https://github.com/Azure-Samples/media-services-v3-java/tree/master/LiveIngest/LiveEventWithDVR)|Vorgehensweise zum Erstellen Sie eines Liveereignisses mit einem vollständigen Archiv bis zu 25 Stunden und einem Filter für das Objekt mit einem 5-Minuten-DVR-Fenster und zum Erstellen eines Locators für das Streaming und Verwenden des Filters.|
|[VideoAnalytics/VideoAnalyzer](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer)|Vorgehensweise zum Erstellen Sie eines Liveereignisses mit einem vollständigen Archiv bis zu 25 Stunden und einem Filter für das Objekt mit einem 5-Minuten-DVR-Fenster und zum Erstellen eines Locators für das Streaming und Verwenden des Filters.|
|VideoEncoding||
|[EncodingWithMESCustomPreset](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoEncoding/EncodingWithMESCustomPreset)|Vorgehensweise zum Erstellen einer benutzerdefinierten Codierungstransformation mithilfe der StandardEncoderPreset-Einstellungen.|
|[EncodingWithMESPredefinedPreset](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoEncoding/EncodingWithMESPredefinedPreset)|Vorgehensweise zum Übermitteln eines Auftrags mithilfe einer integrierten Voreinstellung und der Eingabe einer HTTP-URL, zum Veröffentlichen des Ausgabeobjekts für das Streaming und zum Herunterladen der Ergebnisse zur Überprüfung.|

---

## <a name="rest-postman-collection"></a>REST Postman-Sammlung

Die [REST-Postman](https://github.com/Azure-Samples/media-services-v3-rest-postman)-Beispiele beinhalten eine Postman-Umgebung und -Sammlung, die Sie in den Postman-Client importieren können. Die Postman-Sammlungsbeispiele werden empfohlen, um sich mit der API-Struktur und der Funktionsweise mit Azure Resource Manager (ARM) sowie mit der Struktur von Aufrufen von Client-SDKs vertraut zu machen. 

[!INCLUDE [warning-rest-api-retry-policy.md](./includes/warning-rest-api-retry-policy.md)]
