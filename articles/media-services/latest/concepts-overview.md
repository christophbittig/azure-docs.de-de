---
title: Media Services – Terminologie und Konzepte
description: Erfahren Sie mehr über Terminologie und Konzepte für Azure Media Services.
services: media-servicesgit
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
ms.openlocfilehash: 876e4c33f2f2b848b8155ef0be1650d75e1e9f49
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122351495"
---
# <a name="media-services-terminology-and-concepts"></a>Media Services – Terminologie und Konzepte

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Dieses Thema bietet eine kurze Übersicht über die Azure Media Services-Terminologie und -Konzepte. Darüber hinaus enthält der Artikel Links zu Artikeln mit ausführlicheren Erläuterungen zu Konzepten und Funktionen von Media Services v3.

Es empfiehlt sich, sich vor Entwicklungsbeginn mit den grundlegenden Konzepten vertraut zu machen, die in diesen Themen behandelt werden.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="media-services-v3-terminology"></a>Media Services v3 – Terminologie

|Begriff|BESCHREIBUNG|
|---|---|
|Liveereignis|Ein **Liveereignis** stellt eine Pipeline zum Erfassen, Transcodieren (optional) und Packen von Video- und Audiolivestreams sowie von Echtzeit-Metadaten dar.<br/><br/>Für Kunden, die von Media Services v2-APIs migrieren, ersetzt das **Liveereignis** die **Channel**-Entität in v2. Weitere Informationen finden Sie unter [Hinweise zur Migration von Media Services v2 zu v3](migrate-v-2-v-3-migration-introduction.md).|
|Streamingendpunkt/Paketerstellung/Ursprung|Ein **Streamingendpunkt** stellt einen dynamischen Just-in-Time-Paketerstellungs- und Ursprungsdienst dar, der Ihre Live- und On-Demand-Inhalte direkt in einer Clientplayer-Anwendung bereitstellen kann. Er verwendet eins der allgemeinen Protokolle für Streamingmedien (HLS oder DASH). Zudem sorgt der **Streamingendpunkt** für eine dynamische (Just-In-Time-)Verschlüsselung zu branchenführenden Digital Rights Management-Systemen (DRMs).<br/><br/>In der Medienstreamingbranche wird dieser Dienst häufig als **Paketerstellungs-Manager** oder **Ursprung** bezeichnet.  JITP (Just-in-time-Packager, Just-In-Time-Paketerstellungs-Manager) oder JITE (Just-in-time-Encryption, Just-In-Time-Verschlüsselung) sind weitere in der Branche häufig verwendete Begriffe für diese Funktion.

## <a name="media-services-v3-concepts"></a>Azure Media Services v3 – Konzepte

|Konzepte|BESCHREIBUNG|Links|
|---|---|---|
|Medienobjekte und Hochladen von Inhalten|Um mit dem Verwalten, Verschlüsseln, Codieren, Analysieren und Streamen von Medieninhalten in Azure beginnen zu können, müssen Sie ein Media Services-Konto erstellen und Ihre digitalen Dateien in **Medienobjekte** hochladen.|[Clouduploads und Cloudspeicherung](storage-account-concept.md)<br/><br/>[Medienobjektkonzept](assets-concept.md)|
|Codieren von Inhalt|Sobald Sie Ihre hochwertigen digitalen Mediendateien in Medienobjekten hochgeladen haben, können Sie sie in Formaten codieren, die mit einer Vielzahl von Browsern und Geräten wiedergegeben werden können. <br/><br/>Um mit Media Services v3 codieren zu können, müssen Sie **Transformationen** und **Aufträge** erstellen.|[Transformationen und Aufträge](transform-jobs-concept.md)<br/><br/>[Codierung mit Media Services](encode-concept.md)|
|Analysieren von Inhalten (Video Analyzer für Medien)|Mit Media Services v 3 können Sie Inhalte aus Video- und Audiodateien mit Voreinstellungen von Media Services v3 extrahieren. Um Ihre Inhalte mit den Voreinstellungen von Media Services v3 zu analysieren, müssen Sie **Transformationen** und **Aufträge** erstellen.<br/><br/>Wenn Sie ausführlichere Erkenntnisse benötigen, verwenden Sie [Video Analyzer für Medien](../../azure-video-analyzer/video-analyzer-for-media-docs/index.yml) direkt.|[Analysieren von Video- und Audiodateien](analyze-video-audio-files-concept.md)|
|Verpackung und Bereitstellung|Sobald Ihre Inhalte codiert sind, können Sie die Vorteile der **dynamischen Paketerstellung** nutzen. Bei Media Services ist ein **Streamingendpunkt** der dynamische Paketerstellungsdienst, mit dem Medieninhalte für Clientplayer bereitgestellt werden. Um Videos im Ausgabeobjekt für die Wiedergabe durch Clients verfügbar zu machen, müssen Sie einen **Streaminglocator** erstellen und dann die Streaming-URLs erstellen. <br/><br/>Beim Erstellen des **Streaminglocators** müssen Sie zusätzlich zum Namen des Objekts eine **Streamingrichtlinie** angeben. Mithilfe von **Streamingrichtlinien** können Sie Streamingprotokolle und Verschlüsselungsoptionen (falls zutreffend) für Ihre **Streaminglocators** definieren. Die dynamische Paketerstellung wird unabhängig davon verwendet, ob Sie für Ihre Inhalte das Live- oder On-Demand-Streaming nutzen. <br/><br/>Sie können **dynamische Manifeste** von Media Services verwenden, um nur eine bestimmte Wiedergabe oder Subclips Ihres Videos zu streamen. Außerdem können Sie Inhalte mit den AMS-Ursprungsdiensten streamen, wenn Sie über vorab codierte Inhalte oder bereits von einem Drittanbieterencoder codierte Inhalte verfügen. Ein Beispiel für die Verwendung einer vorcodierten Quelldatei finden Sie im Beispiel zum [Streamen einer vorhandenen MP4-Datei](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/StreamExistingMp4). |[Dynamische Paketerstellung](encode-dynamic-packaging-concept.md)<br/><br/>[Streamingendpunkte](stream-streaming-endpoint-concept.md)<br/><br/>[Streaminglocators](stream-streaming-locators-concept.md)<br/><br/>[Streamingrichtlinien](stream-streaming-policy-concept.md)<br/><br/>[Dynamische Manifeste](filters-dynamic-manifest-concept.md)<br/><br/>[Filter](filters-concept.md)|
|Inhaltsschutz|Mit Media Services können Sie Ihre zu übermittelnden Live- und On-Demand-Inhalte dynamisch mit Advanced Encryption Standard (AES-128) oder/und einem der drei wichtigsten DRM-Systeme verschlüsseln: Microsoft PlayReady, Google Widevine und Apple FairPlay. Media Services bietet auch einen Dienst für die Übermittlung von AES-Schlüsseln und DRM-Lizenzen (PlayReady, Widevine und FairPlay) an autorisierte Clients. <br/><br/>Wenn Sie Verschlüsselungsoptionen für Ihren Datenstrom festlegen, erstellen Sie die **Inhaltsschlüsselrichtlinie** und verknüpfen sie mit Ihrem **Streaminglocator**. Mit der **Richtlinie für den Inhaltsschlüssel** können Sie konfigurieren, wie der Inhaltsschlüssel an Endclients verteilt wird.<br/><br/> Versuchen Sie, Richtlinien immer dann wiederzuverwenden, wenn dieselben Optionen erforderlich sind.| [Richtlinien für Inhaltsschlüssel](drm-content-key-policy-concept.md)<br/><br/>[Inhaltsschutz](drm-content-protection-concept.md)|
|Livestreaming|Mit Media Services können Sie Ihren Kunden Liveereignisse in der Azure-Cloud anbieten. **Liveereignisse** sorgen für das Erfassen und Verarbeiten von Livevideofeeds. Wenn Sie ein **Liveereignis** erstellen, wird ein Eingangsendpunkt erstellt, mit dem Sie ein Livesignal von einem Remoteencoder senden können. Wenn der Stream an das **Liveereignis** übertragen wird, können Sie das Streamingereignis starten, indem Sie ein **Medienobjekt**, eine **Liveausgabe** und einen **Streaminglocator** erstellen. Durch die **Liveausgabe** wird der Datenstrom in das **Medienobjekt** archiviert und über den **Streamingendpunkt** für die Zuschauer verfügbar gemacht. Ein Liveereignis kann entweder auf eine *Pass-Through*- (ein lokaler Liveencoder sendet einen Stream mit mehreren Bitraten) oder *Livecodierung* (ein lokaler Liveencoder sendet einen Stream mit Einzelbitrate) festgelegt werden. |[Übersicht über das Livestreaming](stream-live-streaming-concept.md)<br/><br/>[Liveereignisse und Liveausgaben](live-event-outputs-concept.md)|
|Überwachen mit Event Grid|Verwenden Sie **Event Grid**, um den Auftragsfortschritt anzuzeigen. Media Services gibt auch die Live-Ereignistypen aus. Mit Event Grid können Ihre Apps Ereignisse aus praktisch allen Azure-Diensten sowie aus benutzerdefinierten Quellen überwachen und darauf reagieren. |[Behandeln von Event Grid-Ereignissen](monitoring/reacting-to-media-services-events.md)<br/><br/>[Schemas](monitoring/media-services-event-schemas.md)|
|Überwachen mit Azure Monitor|Überwachen von Metriken und Diagnoseprotokollen, die Ihnen zu verstehen helfen, wie sich Ihre Apps mit Azure Monitor verhalten.|[Metriken und Diagnoseprotokolle](monitoring/monitor-media-services-data-reference.md)<br/><br/>[Schemas für Diagnoseprotokolle](monitoring/monitor-media-services-data-reference.md)|
|Beteiligte Kunden|Sie können ein beliebiges Playerframework verwenden, das das HLS- oder DASH-Streamingprotokoll unterstützt. Es gibt viele Open-Source-Player oder kommerzielle Player auf dem Markt (Shaka, Hls.js, Video.js, Theo Player, Bitmovin Player usw.) sowie integrierte Unterstützung für HLS und DASH für das Streaming in nativen Browsern und auf Betriebssystemebene.  Mit dem Azure Media Player können Sie ebenfalls von Media Services gestreamte Medieninhalte in einer Vielzahl von Browsern wiedergeben. Der Azure Media Player nutzt Industriestandards wie HTML5, MSE (Media Source Extensions) und EME (Encrypted Media Extensions) für ein adaptives Streaming. |[Übersicht über Azure Media Player](player-use-azure-media-player-how-to.md)|

## <a name="ask-questions-give-feedback-get-updates"></a>Fragen stellen, Feedback geben, Updates abrufen

Im Artikel [Azure Media Services-Community](media-services-community.md) finden Sie verschiedene Möglichkeiten, Fragen zu stellen, Feedback zu geben und Updates zu Media Services zu bekommen.

## <a name="next-steps"></a>Nächste Schritte

* [Codieren von Remotedateien und Streamen von Videos: REST](stream-files-tutorial-with-rest.md)
* [Codieren von hochgeladenen Dateien und Streamen von Videos: .NET](stream-files-tutorial-with-api.md)
* [Livestreaming: .NET](stream-live-tutorial-with-api.md)
* [Analysieren Ihres Videos: .NET](analyze-videos-tutorial.md)
* [Dynamische AES-128-Verschlüsselung: .NET](drm-playready-license-template-concept.md)
* [Dynamische Verschlüsselung mit Multi-DRM: .NET](drm-protect-with-drm-tutorial.md)
