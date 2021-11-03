---
title: Versionshinweise zu Azure Media Services v3
description: Damit Sie bezüglich der aktuellen Entwicklungen auf dem neuesten Stand bleiben, bietet dieser Artikel Informationen zu Azure Media Services v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: na
ms.custom: references_regions, ignite-fall-2021
ms.topic: article
ms.date: 03/17/2021
ms.author: inhenkel
ms.openlocfilehash: c744f43fee7a49406b29768b6a0519da578eca01
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131015312"
---
# <a name="azure-media-services-v3-release-notes"></a>Versionshinweise zu Azure Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Damit Sie bezüglich der aktuellen Entwicklungen auf dem neuesten Stand bleiben, bietet dieser Artikel Informationen zu Folgendem:

* Neueste Versionen
* Bekannte Probleme
* Behebung von Programmfehlern
* Veraltete Funktionen

## <a name="september-2021"></a>September 2021

### <a name="new-basic-pass-through-live-event-sku"></a>Neue Passthrough-Liveereignis-Basic-SKU

Mit der neuen Passthrough-Liveereignis-Basic-SKU können Kunden Liveereignisse zu einem [niedrigeren Preispunkt](https://azure.microsoft.com/pricing/details/media-services/) erstellen. Dies ähnelt den standardmäßigen Passthrough-Liveereignissen, hat jedoch geringere Grenzwerte für die Eingabebandbreite, weniger zulässige Liveausgaben, andere Grenzwerte für die DVR-Fensterlänge und bietet keinen Zugriff auf Livetranskription. Weitere Informationen finden Sie im Artikel [Vergleich von Liveereignistypen](./live-event-types-comparison-reference.md#types-comparison).

### <a name="improved-scale-management-and-monitoring-for-a-streaming-endpoint-in-the-portal"></a>Verbesserte Skalierungsverwaltung und -überwachung für einen Streamingendpunkt im Portal

Die Portalseite des Streamingendpunkts bietet Ihnen jetzt eine einfache Möglichkeit, Ihre Ausgangskapazität zu verwalten und die Reichweite Ihrer Zielgruppe mit und ohne konfigurierte CDN einzuschätzen.  Passen Sie einfach die Übermittlungsbitrate und die erwartete CDN-Cachetrefferquote an, um schnelle Schätzungen Ihrer Zielgruppengröße zu erhalten und zu ermitteln, ob Sie auf mehr Premium-Streamingendpunkte hochskalieren müssen.

   [ ![Skalieren und Überwachen von Streamingendpunkten im Portal](./media/release-notes/streaming-endpoint-monitor-inline.png) ](./media/release-notes/streaming-endpoint-monitor.png#lightbox)

### <a name="streaming-endpoint-portal-page-now-shows-cpu-egress-and-latency-metrics"></a>Die Portalseite des Streamingendpunkts zeigt jetzt CPU-, Ausgangs- und Latenzmetriken an.

Sie können nun die CPU-Last, die ausgehende Bandbreite und End-to-End-Latenzmetriken auf deren Streamingendpunkten im Azure-Portal visualisieren. Sie können jetzt Überwachungswarnungen basierend auf den CPU-, Ausgangs- oder Latenzmetriken direkt im Portal erstellen, indem Sie die Leistungsfähigkeit von Azure Monitor nutzen.

### <a name="user-assigned-managed-identities-support-for-media-services-accounts"></a>Unterstützung von benutzerseitig zugewiesene verwaltete Identitäten für Media Services Konten

Mit von benutzerseitig zugewiesenen, verwalteten Identitäten können Kunden jetzt die Sicherheit ihrer Speicherkonten und zugehörigen Schlüsseltresore optimieren. Der Zugriff auf das Kundenspeicherkonto und die Schlüsseltresore ist auf die benutzerseitig zugewiesene, verwaltete Identität beschränkt.  Sie haben die volle Kontrolle über die Lebensdauer der benutzerseitig zugewiesenen, verwalteten Identitäten und können den Zugriff des Mediendienstkontos auf ein bestimmtes Speicherkonto bei Bedarf einfach widerrufen.

### <a name="media-services-storage-accounts-page-in-the-portal-now-support-both-uami-and-sami"></a>Die Seite „Media Services-Speicherkonten“ im Portal unterstützt jetzt sowohl UAMI als auch SAMI.

Sie können jetzt benutzerseitig zugewiesene verwaltete Identitäten (User-Assigned Managed Identities, UAMI) oder systemseitig verwaltete Identitäten (System-Assigned Managed Identities, SAMI) für Ihre Speicherkonten direkt im Azure-Portal für Media Services zuweisen und verwalten.

### <a name="bring-your-own-key-page-now-also-supports-both-uami-and-sami"></a>Die Seite „Bring Your Own Key“ unterstützt jetzt auch UAMI und SAMI.
Die Seite des Schlüsselverwaltungsportals für Media Services unterstützt jetzt die Konfiguration und Verwaltung von benutzerseitig zugewiesene verwaltete Identitäten (UAMI) oder von systemseitig zugewiesenen verwalteten Identitäten (SAMI).

   [![Bring Your Own Keys für die Kontoverschlüsselung](./media/release-notes/byok-managed-identity.png)](./media/release-notes/byok-managed-identity.png)


### <a name="private-link-support-for-media-services"></a>Private Link-Unterstützung für Media Services
Sie können nun den öffentlichen Zugriff auf Ihre Liveereignisse, Streamingendpunkte und Schlüsselübermittlungsdienste-Endpunkte für den Inhaltsschutz und DRM einschränken, indem Sie einen privaten Endpunkt für jeden der Dienste erstellen. Dadurch wird der öffentliche Zugriff auf jeden dieser Dienste eingeschränkt. Nur Datenverkehr, der aus Ihrem konfigurierten virtuellen Netzwerk (VNet) stammt, das im privaten Endpunkt konfiguriert ist, kann diese Endpunkte erreichen.

### <a name="ip-allow-list-for-key-service"></a>Liste mit zugelassenen IP-Adressen für Schlüsseldienst
Sie können jetzt festlegen, dass bestimmte öffentliche IP-Adressen Zugriff auf den Schlüsselübermittlungsdienst für DRM und Inhaltsschutz haben sollen. Liveereignis- und Streamingendpunkte unterstützen bereits die Konfiguration der Liste mit zugelassenen IP-Adressen auf ihren jeweiligen Seiten.

Sie verfügen jetzt auch über ein Featureflag auf Kontoebene, um den öffentlichen Internetzugriff auf Ihr Media Services-Konto zuzulassen bzw. zu blockieren.

## <a name="july-2021"></a>Juli 2021

### <a name="net-sdk-microsoftazuremanagementmedia--500-release-available-in-nuget"></a>.NET SDK (Microsoft.Azure.Management.Media ) Release 5.0.0 in NuGet verfügbar

Das [Microsoft.Azure.Management.Media](https://www.nuget.org/packages/Microsoft.Azure.Management.Media/5.0.0) .NET SDK Version 5.0.0 wird jetzt auf NuGet veröffentlicht. Diese Version wird generiert, um mit der stabilen Version [2021-06-01](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2021-06-01) der Open-API (Swagger) ARM-REST-API zu arbeiten.

Ausführliche Informationen zu Änderungen ab Version 4.0.0 finden Sie im [Änderungsprotokoll](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/mediaservices/Microsoft.Azure.Management.Media/CHANGELOG.md).

#### <a name="changes-in-the-500-net-sdk-release"></a>Änderungen im 5.0.0 .NET SDK-Release

* Das Media Services-Konto unterstützt jetzt systemseitig und benutzerseitig zugewiesene verwaltete Identitäten.
* Die Option **PublicNetworkAccess** wurde Media Services-Konten hinzugefügt. Diese Option kann zusammen mit dem Private Link-Feature verwendet werden, um nur den Zugriff von privaten Netzwerken zuzulassen und den Zugriff auf öffentliche Netzwerke zu blockieren.
* Einfacher Passthrough: Ein neuer Liveereignistyp wird hinzugefügt. Liveereignisse mit einfachem Passthrough haben ähnliche Funktionen wie Liveereignisse mit Standard-Passthrough mit einigen Ein- und Ausgabeeinschränkungen und werden zu einem reduzierten Preis angeboten.
* **PresetConfigurations**: Ermöglicht Ihnen das Anpassen der Ausgabeeinstellungen sowie der minimalen und maximalen Bitraten, die für die [Voreinstellungen der inhaltsbezogenen Codierung](./encode-content-aware-concept.md) verwendet werden. Dies hilft Ihnen bei der besseren Schätzung und Planung für eine genauere Abrechnung, wenn Sie inhaltsbezogene Codierung mithilfe von begrenzten Ausgabenachverfolgungsnummern und Auflösungen verwenden.

#### <a name="breaking-changes-in-tht-500-net-sdk-release"></a>Breaking Changes in Version 5.0.0 des .NET SDK

* **ApiErrorException** wurde durch **ErrorResponseException** ersetzt, um mit allen anderen Azure SDKs konsistent zu sein. Der Ausnahmetext hat sich nicht geändert.
* Alle Aufrufe, die „404 Nicht gefunden“ zurückgeben, geben jetzt eine **ErrorResponseException** aus, anstatt NULL zurückzugeben. Diese Änderung wurde vorgenommen, um mit anderen Azure SDKs konsistent zu sein.
* Der Media Service-Konstruktor verfügt über einen neuen optionalen PublicNetworkAccess-Parameter hinter dem KeyDelivery-Parameter.
* Die Type-Eigenschaft in **MediaServiceIdentity** wurde von Enumeration (enum) in Zeichenfolge (string) für „ManagedIdentityType“ geändert, um mehrere durch Komma getrennte Typen zu ermöglichen. Gültige Zeichenfolgen sind **SystemAssigned** oder **UserAssigned**.

## <a name="june-2021"></a>Juni 2021

### <a name="additional-live-event-ingest-heartbeat-properties-for-improved-diagnostics"></a>Zusätzliche Liveereignis-Erfassungstakteigenschaften für verbesserte Diagnosen

Der Event Grid-Nachricht wurden zusätzliche Liveereignis-Erfassungstakteigenschaften hinzugefügt. Dazu gehören die folgenden neuen Felder, die bei der Diagnose von Problemen während der Liveerfassung helfen.  Der **ingestDriftValue** ist in Szenarien hilfreich, in denen Sie die Netzwerklatenz vom Quellerfassungsencoder überwachen müssen, der in das Liveereignis pusht. Wenn dieser Wert zu stark abweicht, kann dies ein Hinweis darauf sein, dass die Netzwerklatenz für ein erfolgreiches Livestreamingereignis zu hoch ist.

Weitere Informationen finden Sie unter [LiveEventIngestHeartbeat-Schema](./monitoring/media-services-event-schemas.md#liveeventingestheartbeat).

### <a name="private-links-support-is-now-ga"></a>Unterstützung für private Verbindungen ist jetzt allgemein verfügbar

Die Unterstützung für die Verwendung von Media Services mit [privaten Verbindungen](../../private-link/index.yml) ist jetzt allgemein (GA) und in allen Azure-Regionen verfügbar, einschließlich Azure Government-Clouds.
Mit Azure Private Link können Sie über einen privaten Endpunkt in Ihrem virtuellen Netzwerk auf Azure-PaaS-Dienste sowie auf in Azure gehostete kundeneigene Dienste/Partnerdienste zugreifen.
Der Datenverkehr zwischen Ihrem virtuellen Netzwerk und dem Dienst wird über das Microsoft-Backbone-Netzwerk übertragen und dadurch vom öffentlichen Internet isoliert.

Informationen zum Verwenden von Media Services mit privaten Verbindungen finden Sie unter [Erstellen eines Media Services- und Speicherkontos mit einem privaten Link](./security-private-link-how-to.md).

### <a name="new-us-west-3-region-is-ga"></a>Neue Region „USA, Westen 3“ ist allgemein verfügbar

Die Region „USA, Westen 3“ ist jetzt allgemein verfügbar und kann von Kunden verwendet werden, wenn sie neue Media Services-Konten erstellen.

### <a name="key-delivery-supports-ip-allow-list-restrictions"></a>Schlüsselübermittlung unterstützt Einschränkungen der Liste zugelassener IP-Adressen

Media Services-Konten können jetzt mit Einschränkungen der Liste zugelassener IP-Adressen für die Schlüsselübermittlung konfiguriert werden. Die neue Einstellung für die Positivliste ist auf der Media Services-Kontoressource über das SDK sowie im Portal und über die CLI verfügbar.
Dies ermöglicht es den Operatoren, die Bereitstellung von DRM-Lizenzen und symmetrischen AES-128-Schlüsseln auf bestimmte IPv4-Bereiche zu beschränken.

Dieses Feature kann auch verwendet werden, um alle öffentlichen Internetübermittlungen von DRM-Lizenzen oder AES-128-Schlüsseln zu deaktivieren und die Übermittlung auf einen privaten Endpunkt zu beschränken.

Weitere Informationen finden Sie im Artikel [Einschränken des Zugriffs auf die DRM-Lizenz- und AES-Schlüsselübermittlung mithilfe von Listen zugelassener IP-Adressen](./drm-content-protection-key-delivery-ip-allow.md).

### <a name="new-samples-for-python-and-nodejs-with-typescript"></a>Neue Beispiele für Python und Node.js (mit TypeScript)
Aktualisierte Beispiele für **Node.js**, die die neueste TypeScript-Unterstützung im Azure SDK verwenden.

|Beispiel|BESCHREIBUNG|
|---|---|
|[Livestreaming](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/Live/index.ts)| Einfaches Livestreaming-Beispiel. **WARNUNG**: Stellen Sie sicher, dass alle Ressourcen bereinigt sind und im Portal nicht mehr abgerechnet werden, wenn Sie Livestreaming verwenden.|
|[Hochladen und Streamen von HLS und DASH](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/StreamFilesSample/index.ts)| Einfaches Beispiel für das Hochladen einer lokalen Datei oder Codieren aus einer Quell-URL. Das Beispiel zeigt, wie Sie Storage SDK zum Herunterladen von Inhalten verwenden und wie Sie an einen Player streamen. |
|[Hochladen und Streamen von HLS und DASH mit Playready und Widevine DRM](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/StreamFilesWithDRMSample/index.ts)| Zeigt, wie Sie mithilfe von Widevine und PlayReady DRM codieren und streamen. |
|[Hochladen und Verwenden von KI zum Indizieren von Video- und Audiodaten](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/VideoIndexerSample/index.ts)| Beispiel für die Verwendung von Voreinstellungen für das Video- und Audioanalysetool zum Generieren von Metadaten und Erkenntnissen aus einer Video- oder Audiodatei. |


Neues **Python**-Beispiel, das zeigt, wie Azure Functions und Event Grid verwendet werden, um die Voreinstellung für die Gesichtsbearbeitung auszulösen.

|Beispiel|BESCHREIBUNG|
|---|---|
|[Gesichtsbearbeitung mithilfe von Ereignissen und Funktionen](https://github.com/Azure-Samples/media-services-v3-python/tree/main/VideoAnalytics/FaceRedactorEventBased) | Dies ist ein Beispiel für einen ereignisbasierten Ansatz, bei dem ein Azure Media Services-Auftrag zur Gesichtsbearbeitung für ein Video ausgelöst wird, sobald es in einem Azure Storage-Konto hinzugefügt wird. Für die Lösung werden Azure Media Services, Azure Functions, Event Grid und Azure Storage verwendet. Eine vollständige Beschreibung der Lösung finden Sie in der Datei [README.md](https://github.com/Azure-Samples/media-services-v3-python/blob/main/VideoAnalytics/FaceRedactorEventBased/README.md). |


## <a name="may-2021"></a>Mai 2021

### <a name="availability-zones-default-support-in-media-services"></a>Verfügbarkeitszonen: Standardunterstützung in Media Services

Media Services unterstützt jetzt [Verfügbarkeitszonen](concept-availability-zones.md), die fehlerisolierte Standorte innerhalb einer Azure-Region bieten.  Media Services-Konten sind jetzt standardmäßig zonenredundante Konten, und es sind keine zusätzlichen Konfigurationen oder Einstellungen erforderlich. Dies gilt nur für Regionen, in denen [Verfügbarkeitszonen unterstützt](../../availability-zones/az-region.md#azure-regions-with-availability-zones) werden.


## <a name="march-2021"></a>März 2021

### <a name="new-language-support-added-to-the-audioanalyzer-preset"></a>Hinzufügung der Unterstützung für neue Sprachen in der AudioAnalyzer-Voreinstellung

In der AudioAnalyzer-Voreinstellung (sowohl im Basic- als auch im Standard-Modus) sind jetzt für die Videotranskription und Untertitelung weitere Sprachen verfügbar.

* Englisch (Australien), „en-AU“
* Französisch (Kanada), „fr-CA“
* Arabisch (Bahrain), modernes Hocharabisch, „ar-BH“
* Arabisch (Ägypten), „ar-EG“
* Arabisch (Irak), „ar-IQ“
* Arabisch (Israel), „ar-IL“
* Arabisch (Jordanien), „ar-JO“
* Arabisch (Kuwait), „ar-KW“
* Arabisch (Libanon), „ar-LB“
* Arabisch (Oman), „ar-OM“
* Arabisch (Katar), „ar-QA“
* Arabisch (Saudi-Arabien), „ar-SA“
* Dänisch, „da-DK“
* Norwegisch, „nb-NO“
* Schwedisch, „sv-SE“
* Finnisch, „fi-FI“
* Thailändisch, „th-TH“
* Türkisch, „tr-TR“

Die neuesten verfügbaren Sprachen finden Sie im [Konzeptartikel zur Analyse von Video- und Audiodateien](analyze-video-audio-files-concept.md).

## <a name="february-2021"></a>Februar 2021

### <a name="hevc-encoding-support-in-standard-encoder"></a>Unterstützung von HEVC-Codierung im Standard-Encoder

Der Standard-Encoder bietet jetzt Unterstützung für 8-Bit-HEVC-Codierung (H.265). HEVC-Inhalte können mithilfe des hev1-Formats über die dynamische Paketerstellung übermittelt und gepackt werden.  

Ein neues Beispiel für eine benutzerdefinierte .NET-Codierung mit HEVC finden Sie im GitHub-Repository [media-services-v3-dotnet](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_HEVC).
Neben der benutzerdefinierten Codierung sind nun die folgenden neuen integrierten HEVC-Codierungsvoreinstellungen verfügbar:

- H265ContentAwareEncoding
- H265AdaptiveStreaming
- H265SingleBitrate720P
- H265SingleBitrate1080p
- H265SingleBitrate4K

Kunden, die zuvor HEVC im Premium-Encoder in Version 2 der API verwendet haben, sollten zur neuen HEVC-Codierungsunterstützung im Standard-Encoder migrieren.

### <a name="azure-media-services-v2-api-and-sdks-deprecation-announcement"></a>Ankündigung der Einstellung der API und SDKs für Azure Media Services v2

#### <a name="update-your-azure-media-services-rest-api-and-sdks-to-v3-by-29-february-2024"></a>Aktualisieren Sie Ihre REST-API und SDKs für Azure Media Services bis 29. Februar 2024 auf Version 3

Da Version 3 der REST-API und Client-SDKs für Azure Media Services für .NET und Java mehr Funktionen bietet als Version 2, stellen wir Version 2 der REST-API und Client-SDKs für Azure Media Services für .NET und Java ein.

Wir empfehlen Ihnen, die Umstellung früher vorzunehmen, um schnellstmöglich von den Vorteilen von Version 3 der REST-API und Client-SDKs für Azure Media Services für .NET und Java zu profitieren.
Version 3 bietet Folgendes:
 
- Liveereignisunterstützung rund um die Uhr
- ARM REST APIs, Client-SDKs für .NET Core, Node.js, Python, Java, Go und Ruby
- Kundenseitig verwaltete Schlüssel, vertrauenswürdige Speicherintegration, Unterstützung für private Verbindungen und [vieles mehr](./migrate-v-2-v-3-migration-benefits.md)

Im Rahmen des Updates der v3-API und SDKs werden Media Reserve Units (MRUs) nicht mehr für Media Services-Konten benötigt, da das System basierend auf der Last automatisch hoch- und herunterskaliert wird. Weitere Informationen finden Sie im [MRUs Migrationsleitfaden](./migrate-v-2-v-3-migration-scenario-based-media-reserved-units.md).

#### <a name="action-required"></a>Erforderliche Aktion

Um die Unterbrechung Ihrer Workloads zu minimieren, lesen Sie den [Migrationsleitfaden](./migrate-v-2-v-3-migration-introduction.md). Hier erhalten Sie Informationen zur Übertragung Ihres Codes von der API und den SDKs der Version 2 zur API und den SDKs der Version 3 vor dem 29. Februar 2024.
**Nach dem 29. Februar 2024** akzeptiert Azure Media Services keinen Datenverkehr mehr über die REST-API der Version 2, über die ARM-Kontoverwaltungs-API der Version 2015-10-01 oder aus .NET-Client-SDKs der Version 2. Dies betrifft auch alle Open-Source-Client-SDKs von Drittanbietern, die Version 2 der API aufrufen.  

Weitere Informationen finden Sie in der offiziellen [Ankündigung zu Azure-Updates](https://azure.microsoft.com/updates/update-your-azure-media-services-rest-api-and-sdks-to-v3-by-29-february-2024/).

### <a name="standard-encoder-support-for-v2-api-features"></a>Standard-Encoder-Unterstützung für Features von Version 2 der API

Neben der neu hinzugefügten Unterstützung der HEVC-Codierung (H.265) sind in Version 2020-05-01 (oder höher) der Codierungs-API jetzt die folgenden Features verfügbar.

- Das Zusammenfügen mehrerer Eingabedateien wird jetzt unterstützt dank der neuen **JobInputClip**-Unterstützung.
    - Es ist ein Beispiel für .NET verfügbar, in dem gezeigt wird, wie Sie [zwei Objekte zusammenfügen](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_StitchTwoAssets).
- Die Audiotitelauswahl ermöglicht es Kunden, die eingehenden Audiotitel auszuwählen, zuzuordnen und an die Ausgabe für die Codierung weiterzuleiten.
    - Details zu **AudioTrackDescriptor** und der Auswahl von Titeln finden Sie in der [OpenAPI-Datei für die REST-API](https://github.com/Azure/azure-rest-api-specs/blob/8d15dc681b081cca983e4d67fbf6441841d94ce4/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json#L385).
- Titelauswahl für Codierung: Dieses Feature ermöglicht Kunden das Auswählen von Titeln aus einer ABR-Quelldatei oder einem Livearchiv mit Titeln mit verschiedenen Bitraten. Dies ist äußerst hilfreich für das Generieren von MP4-Dateien aus den Archivdateien von Liveereignissen.
    - Weitere Informationen finden Sie unter [VideoTrackDescriptor](https://github.com/Azure/azure-rest-api-specs/blob/8d15dc681b081cca983e4d67fbf6441841d94ce4/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json#L1562).
- FaceDetector wurden Bearbeitungsfunktionen (Weichzeichner) hinzugefügt.
    - Sehen Sie sich die Modi [Redact](https://github.com/Azure/azure-rest-api-specs/blob/8d15dc681b081cca983e4d67fbf6441841d94ce4/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json#L634) (Bearbeiten) und [Combined](https://github.com/Azure/azure-rest-api-specs/blob/8d15dc681b081cca983e4d67fbf6441841d94ce4/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json#L649) (Kombiniert) der FaceDetector-Voreinstellung an.

### <a name="new-client-sdk-releases-for-2020-05-01-version-of-the-azure-media-services-api"></a>Neue Client-SDK-Releases für Version 2020-05-01 der Azure Media Services-API

Für alle unterstützten Sprachen sind jetzt neue Client-SDK-Versionen mit den oben aufgeführten Features verfügbar.
Führen Sie mit Ihrem Paket-Manager für Ihre Codebasis ein Update auf die aktuellen Client-SDKs durch.

- [.NET SDK-Paket 3.0.4](https://www.nuget.org/packages/Microsoft.Azure.Management.Media/)
- [Node.js TypeScript, Version 8.1.0](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [Python azure-mgmt-media 3.1.0](https://pypi.org/project/azure-mgmt-media/)
- [Java SDK 1.0.0-beta.2](https://search.maven.org/artifact/com.azure.resourcemanager/azure-resourcemanager-mediaservices/1.0.0-beta.2/jar)

### <a name="new-security-features-available-in-the-2020-05-01-version-of-the-azure-media-services-api"></a>Neue verfügbare Sicherheitsfeatures in Version 2020-05-01 der Azure Media Services-API

- **[Kundenseitig verwaltete Schlüssel:](concept-use-customer-managed-keys-byok.md)** Inhaltsschlüssel und andere Daten, die in Konten gespeichert sind, die mit Version 2020-05-01 der API erstellt wurden, werden mit einem Kontoschlüssel verschlüsselt. Kunden können einen Schlüssel zur Verschlüsselung des Kontoschlüssels angeben.

- **[Vertrauenswürdiger Speicher:](concept-trusted-storage.md)** Media Services kann so konfiguriert werden, dass über eine mit dem Media Services-Konto verknüpfte verwaltete Identität auf Azure Storage zugegriffen werden kann. Wenn über eine verwaltete Identität auf Speicherkonten zugegriffen wird, können Kunden restriktivere Netzwerk-ACLs für das Speicherkonto konfigurieren, ohne dass Media Services-Szenarios blockiert werden.

- **[Verwaltete Identitäten:](concept-managed-identities.md)** Kunden können eine systemseitig zugewiesene verwaltete Identität für ein Media Services-Konto aktivieren, um Zugriff auf Schlüsseltresore (für kundenseitig verwaltete Schlüssel) und Speicherkonten (für vertrauenswürdigen Speicher) zu gewähren.

### <a name="updated-typescript-nodejs-samples-using-isomorphic-sdk-for-javascript"></a>Aktualisierte Beispiele für TypeScript/Node.js mit isomorphem SDK für JavaScript

Die Node.js-Beispiele wurden so aktualisiert, dass das neueste isomorphe SDK verwendet wird. In ihnen wird jetzt die Verwendung von TypeScript gezeigt. Außerdem wurde ein neues Livestreamingbeispiel für Node.js/TypeScript hinzugefügt.

Sehen Sie sich die neuesten Beispiele im GitHub-Repository **[media-services-v3-node-tutorials](https://github.com/Azure-Samples/media-services-v3-node-tutorials)** an.

### <a name="new-live-stand-by-mode-to-support-faster-startup-from-warm-state"></a>Neuer Livestandbymodus zur Unterstützung eines schnelleren Starts aus dem warmen Zustand

Für Liveereignisse wird jetzt ein kostengünstigerer Abrechnungsmodus für den Standby unterstützt. Dadurch können Kunden für die Erstellung von heißen Pools Liveereignisse zu niedrigeren Kosten vorab zuordnen. Sie können dann die Liveereignisse im Standby schneller in den Ausführungszustand versetzen als beim Erstellen aus dem Kaltzustand.  So wird die für das Starten des Kanals benötigte Zeit deutlich reduziert und eine schnelle Zuteilung von ausgeführten Computern zu heißen Pools in einem günstigeren Modus ermöglicht.
Aktuelle ausführliche Informationen zu den Preisen finden Sie [hier](https://azure.microsoft.com/pricing/details/media-services).
Weitere Informationen zum Zustand „Standby“ und den anderen Zuständen von Liveereignissen finden Sie im Artikel [Zustände von Liveereignissen und Abrechnung](./live-event-states-billing-concept.md).

## <a name="december-2020"></a>Dezember 2020

### <a name="regional-availability"></a>Regionale Verfügbarkeit

Azure Media Services ist jetzt in der Region „Norwegen, Osten“ im Azure-Portal verfügbar.  RESTv2 ist in dieser Region nicht verfügbar.

## <a name="october-2020"></a>Oktober 2020

### <a name="basic-audio-analysis"></a>Audioanalyse im Tarif „Basic“

Die Voreinstellung für die Audioanalyse enthält jetzt einen Basic-Tarif. Der neue Basic-Modus für die Audioanalyse bietet eine kostengünstige Option zum Extrahieren von Transkriptionen für gesprochenen Text sowie zum Formatieren von Beschriftungstext und Untertiteln. In diesem Modus werden Transkriptionen von gesprochenen Texten erstellt und VTT-Dateien mit Untertiteln und Beschriftungstexten generiert. Die Ausgabe in diesem Modus beinhaltet eine Insights JSON-Datei, die nur die Stichwörter, die Transkription und Zeitinformationen enthält. Eine automatische Spracherkennung und Sprecherdiarisierung sind nicht Bestandteil dieses Modus. Mehr dazu finden Sie in der Liste der [unterstützten Sprachen](analyze-video-audio-files-concept.md#built-in-presets).

Kunden, die Indexer v1 und Indexer v2 verwenden, sollten zur Voreinstellung für die Audioanalyse im Tarif „Basic“ migrieren.

Weitere Informationen zum Basic-Modus für die Audioanalyse finden Sie unter [Analysieren von Video- und Audiodateien](analyze-video-audio-files-concept.md).  Informationen zur Verwendung des Basic-Modus für die Audioanalyse mit der REST-API finden Sie unter [Erstellen einer Audiotransformation im Tarif „Basic“](transform-create-basic-audio-how-to.md).

### <a name="live-events"></a>Liveereignisse

Aktualisierungen der meisten Eigenschaften sind jetzt zulässig, wenn Liveereignisse beendet werden. Darüber hinaus dürfen Benutzer ein Präfix für den statischen Hostnamen für die Eingabe- und Vorschau-URLs von Liveereignissen angeben. VanityUrl heißt jetzt `useStaticHostName`, um den Zweck der Eigenschaft besser widerzuspiegeln.

Liveereignisse verfügen jetzt über einen Standbyzustand.  Weitere Informationen finden Sie unter [Liveereignisse und Liveausgaben in Media Services](./live-event-outputs-concept.md).

Ein Liveereignis unterstützt den Empfang verschiedener Seitenverhältnisse bei Eingaben. Der Stretchingmodus ermöglicht es Kunden, das Stretchingverhalten für die Ausgabe anzugeben.

Live Encoding bietet jetzt die Möglichkeit, feste Keyframe-Intervallfragmente zwischen 0,5 und 20 Sekunden auszugeben.

### <a name="accounts"></a>Konten

> [!WARNING]
> Wenn Sie ein Media Services-Konto mit der API-Version 2020-05-01 erstellen, funktioniert es nicht mit RESTv2. 

## <a name="august-2020"></a>August 2020

### <a name="dynamic-encryption"></a>Dynamische Verschlüsselung

Unterstützung für die ältere PlayReady Protected Interoperable File Format-Verschlüsselung (PIFF 1.1) ist jetzt in Dynamic Packager verfügbar. Dies bietet Unterstützung für ältere Smart TV-Geräte von Samsung und LG, die die frühen Entwürfe der von Microsoft veröffentlichten Common Encryption-Standards (CENC) implementiert haben.  Das PIFF 1.1-Format ist auch als das Verschlüsselungsformat bekannt, das zuvor von der Silverlight-Clientbibliothek unterstützt wurde. Heutzutage besteht das einzige Anwendungsszenario für dieses Verschlüsselungsformat in älteren Smart TV-Geräten, da es in einigen Regionen eine nicht unerheblich Anzahl von Smart TVs gibt, die nur Smooth Streaming mit PIFF 1.1-Verschlüsselung unterstützen.

Wenn Sie die neue PIFF 1.1-Verschlüsselungsunterstützung verwenden möchten, ändern Sie den Verschlüsselungswert im URL-Pfad des Streaminglocators in „piff“. Weitere Informationen finden Sie in der [Übersicht über Content Protection](drm-content-protection-concept.md).
Beispiel: `https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=piff)`|

> [!NOTE]
> PIFF 1.1-Unterstützung wird als abwärtskompatible Lösung für ein Smart TV-Gerät (Samsung, LG) bereitgestellt, das die frühe „Silverlight“-Version von Common Encryption implementiert hat. Sie sollten das PIFF-Format nur dann verwenden, wenn es zur Unterstützung älterer, von 2009 bis 2015 ausgelieferter Samsung- oder LG-Smart TV-Geräte verwendet werden muss, die die PIFF 1.1-Version der PlayReady-Verschlüsselung unterstützten. 

## <a name="july-2020"></a>Juli 2020

### <a name="live-transcriptions"></a>Livetranskriptionen

Livetranskriptionen unterstützen jetzt 19 Sprachen und 8 Regionen.

### <a name="protecting-your-content-with-media-services-and-azure-ad"></a>Schützen Ihrer Inhalte mit Media Services und Azure AD

Wir haben das Tutorial [End-to-End-Inhaltsschutz mithilfe von Azure AD](./architecture-azure-ad-content-protection.md) veröffentlicht.

### <a name="high-availability"></a>Hohe Verfügbarkeit

Wir haben eine [Übersicht](./architecture-high-availability-encoding-concept.md) und ein [Beispiel](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/HighAvailabilityEncodingStreaming) für Hochverfügbarkeit bei Media Services und Video on Demand (VoD) veröffentlicht.

## <a name="june-2020"></a>Juni 2020

### <a name="live-video-analytics-on-iot-edge-preview-release"></a>Live Video Analytics in IoT Edge (Vorschauversion)

Die Vorschauversion von Live Video Analytics in IoT Edge wurde veröffentlicht. 

Live Video Analytics in IoT Edge ist eine Erweiterung der Media Services-Familie. Mit dieser Plattform können Sie Livevideos mit KI-Modellen Ihrer Wahl auf Ihren eigenen Edgegeräten analysieren und diese Videos optional aufzeichnen und speichern. Sie können nun Apps mit Echtzeit-Videoanalysen am Edge erstellen, ohne dass Sie sich Gedanken über die Komplexität des Erstellens und Ausführens einer Livevideo-Pipeline machen müssen.

## <a name="may-2020"></a>Mai 2020

Azure Media Services ist jetzt in den folgenden Regionen allgemein verfügbar: „Deutschland, Norden“, „Deutschland, Westen-Mitte“, „Schweiz, Norden“ und „Schweiz, Westen“. Kunden können Media Services mithilfe des Azure-Portals in diesen Regionen bereitstellen.

## <a name="april-2020"></a>April 2020

### <a name="improvements-in-documentation"></a>Verbesserungen bei der Dokumentation

Die Azure Media Player-Dokumentation wurde zur [Azure-Dokumentation](../azure-media-player/azure-media-player-overview.md) migriert.

## <a name="january-2020"></a>Januar 2020

### <a name="improvements-in-media-processors"></a>Verbesserungen bei Medienprozessoren

- Verbesserte Unterstützung für verschachtelte Quellen in der Videoanalyse: Diese Inhalte werden jetzt ordnungsgemäß entschachtelt, bevor sie an Inferenzmodule gesendet werden.
- Beim Generieren von Miniaturansichten im „besten“ Modus sucht der Encoder jetzt über 30 Sekunden, um einen nicht monochromen Frame auszuwählen.

### <a name="azure-government-cloud-updates"></a>Updates für die Azure Government-Cloud

Media Services ist nun in den folgenden Azure Government-Regionen allgemein verfügbar: *USGov Arizona* und *USGov Texas*.

## <a name="december-2019"></a>Dezember 2019

CDN-Unterstützung für *Origin-Assist Prefetch*-Header für Livestreaming und On-Demand-Videostreaming wurde hinzugefügt und ist für Kunden mit direktem Vertrag mit Akamai CDN verfügbar. Die Funktion „Origin-Assist CDN-Prefetch“ umfasst den folgenden HTTP-Header-Austausch zwischen Akamai CDN und dem Azure Media Services-Ursprung:

|HTTP-Header|Werte|Sender|Receiver|Zweck|
| ---- | ---- | ---- | ---- | ----- |
|CDN-Origin-Assist-Prefetch-Enabled | „1“ (Standardwert) oder „0“ |CDN|Origin|Angabe, dass CDN für den Vorabruf aktiviert ist|
|CDN-Origin-Assist-Prefetch-Path| Beispiel: <br/>Fragments(video=1400000000,format=mpd-time-cmaf)|Origin|CDN|Angabe des Vorabrufpfads zu CDN|
|CDN-Origin-Assist-Prefetch-Request|„1“ (Vorabrufanforderung) oder „0“ (reguläre Anforderung)|CDN|Origin|Angabe, dass die Anforderung von CDN ein Vorabruf ist|

Um einen Teil des Headeraustausches in Aktion zu sehen, können Sie folgende Schritte ausführen:

1. Verwenden Sie Postman oder cURL, um eine Anforderung an den Media Services-Ursprung für ein Audio- oder Videosegment oder Audio- oder Videofragment auszugeben. Fügen Sie den Header „CDN-Origin-Assist-Prefetch-Enabled: 1“ in der Anforderung hinzu.
2. In der Antwort sollte der Header „CDN-Origin-Assist-Prefetch-Path“ mit einem relativen Pfad als Wert angezeigt werden.

## <a name="november-2019"></a>November 2019

### <a name="live-transcription-preview"></a>Livetranskription Preview

Die Livetranskription befindet sich jetzt in der öffentlichen Vorschau und ist in der Region „USA, Westen 2“ verfügbar.

Die Livetranskription ist so konzipiert, dass sie zusammen mit Liveereignissen als Add-On-Funktion funktioniert.  Sie wird sowohl für Pass-Through-Liveereignisse als auch für Liveereignisse mit Standard- oder Premium-Codierung unterstützt.  Wenn dieses Feature aktiviert wird, nutzt der Dienst das [Spracherkennungsfeature](../../cognitive-services/speech-service/speech-to-text.md) von Cognitive Services, um den gesprochenen Text in der eingehenden Audiodatei in Text zu konvertieren. Dieser Text wird dann mitsamt Video- und Audiodaten in den MPEG-DASH- und HLS-Protokollen für die Übermittlung zur Verfügung gestellt. Die Abrechnung basiert auf einer neuen Add-On-Verbrauchseinheit, die sich als zusätzliche Kosten für das Liveereignis niederschlägt, wenn es sich im Zustand „Wird ausgeführt“ befindet.  Details zur Livetranskription und Abrechnung finden Sie unter [Livetranskription](live-event-live-transcription-how-to.md)

> [!NOTE]
> Derzeit ist die Livetranskription nur als Vorschaufunktion in der Region „USA, Westen 2“ verfügbar. Sie unterstützt zu diesem Zeitpunkt nur die Transkription von gesprochener englischer Sprache (en-us).

### <a name="content-protection"></a>Inhaltsschutz

Die Funktion *Verhindern der Tokenwiedergabe* (Token Replay Prevention), die in begrenzten Regionen im September veröffentlicht wurde, ist jetzt in allen Regionen verfügbar.
Media Services-Kunden können jetzt einen Grenzwert festlegen, der bestimmt, wie oft ein Token zum Anfordern eines Schlüssels oder einer Lizenz verwendet werden kann. Weitere Informationen finden Sie unter [Token Replay Prevention](drm-content-protection-concept.md#token-replay-prevention) (Verhindern der Tokenwiedergabe).

### <a name="new-recommended-live-encoder-partners"></a>Neue, empfohlene Liveencoder-Partner

Unterstützung für die folgenden neuen, empfohlenen Partner-Encoder für RTMP-Livestreaming wurde hinzugefügt:

- [Cambria Live 4.3](https://www.capellasystems.net/products/cambria-live/)
- [GoPro Hero7/8 und Max Action-Kameras](https://gopro.com/help/articles/block/getting-started-with-live-streaming)
- [Restream.io](https://restream.io/)

### <a name="file-encoding-enhancements"></a>Verbesserungen bei der Dateicodierung

- Eine neue Voreinstellung für Content Aware Encoding ist jetzt verfügbar. Sie erzeugt mithilfe der inhaltsbezogenen Codierung einen Satz von GOP-orientierten MP4s. Bei jeglichen eingegebenen Inhalten führt der Dienst eine erste einfache Analyse des eingegebenen Inhalts durch. Er verwendet diese Ergebnisse, um die optimale Anzahl der Ebenen, die geeignete Bitrate und die Auflösungseinstellungen für die Bereitstellung durch adaptives Streaming zu bestimmen. Diese Voreinstellung ist besonders effektiv für Videos mit geringer und mittlerer Komplexität, bei denen die Ausgabedateien mit niedrigeren Bitraten, aber in einer Qualität vorliegen, die dem Betrachter dennoch ein gutes Erlebnis bietet. Die Ausgabe enthält MP4-Dateien mit überlappendem Video und Audio. Weitere Informationen finden Sie unter [Öffnen der API-Spezifikationen](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json).
- Verbesserte Leistung und Multithreading für das Größenänderungsmodul in Media Encoder Standard. Unter bestimmten Bedingungen sollte der Kunde eine Leistungssteigerung zwischen 5 und 40 % VOD-Codierung sehen. Inhalte von geringer Komplexität, die in mehrere Bitraten codiert werden, erfahren die höchsten Leistungssteigerungen. 
- Die Standardcodierung behält nun bei Verwendung der zeitbasierten GOP-Einstellung einen regelmäßigen GOP-Rhythmus für Inhalte mit variablen Frameraten (VFR) bei der VOD-Codierung bei.  Dies bedeutet, dass Kunden, die Inhalte mit gemischten Bildfrequenzen übermitteln, die beispielsweise zwischen 15 und 30 fps variieren, nun feststellen sollten, dass bei der Ausgabe in MP4-Dateien mit Adaptive Bitrate Streaming regelmäßige GOP-Abstände berechnet werden. Hierdurch wird die Möglichkeit verbessert, nahtlos zwischen den Spuren zu wechseln, wenn über HLS oder DASH geliefert wird. 
-  Verbesserte AV-Synchronisierung für Quellinhalte mit variabler Framerate (VFR)

### <a name="azure-video-analyzer-for-media-video-analytics"></a>Azure Video Analyzer for Media, Videoanalyse

- Keyframes, die mithilfe der VideoAnalyzer-Voreinstellung extrahiert wurden, sind jetzt in der ursprünglichen Auflösung des Videos, anstatt dass ihre Größe angepasst wird. Die hochauflösende Keyframe-Extraktion bietet Ihnen Bilder in Originalqualität und ermöglicht Ihnen die Verwendung bildbasierter KI-Modelle (künstliche Intelligenz), die von den Microsoft-Diensten Maschinelles Sehen und Custom Vision bereitgestellt werden, um noch mehr Erkenntnisse zu Ihrem Video zu erhalten.

## <a name="september-2019"></a>September 2019

###  <a name="media-services-v3"></a>Media Services v3  

#### <a name="live-linear-encoding-of-live-events"></a>Lineare Livecodierung von Liveereignissen

Media Services v3 gibt die Vorschauversion von 24 Stunden x 365 Tagen linearer Livecodierung von Liveereignisssen bekannt.

###  <a name="media-services-v2"></a>Media Services v2  

#### <a name="deprecation-of-media-processors"></a>Einstellung von Medienprozessoren

Wir kündigen die Einstellung von *Azure Media Indexer* und *Azure Media Indexer 2 Preview* an. Die Daten zur Einstellung finden Sie im Artikel [Legacykomponenten](../previous/legacy-components.md). Azure Video Analyzer for Media ersetzt diese Legacy-Medienprozessoren.

Weitere Informationen finden Sie unter [Migration von Azure Media Indexer v1 und Azure Media Indexer v2 zu **Azure Media Services Video Indexer**](../previous/migrate-indexer-v1-v2.md).

## <a name="august-2019"></a>August 2019

###  <a name="media-services-v3"></a>Media Services v3  

#### <a name="south-africa-regional-pair-is-open-for-media-services"></a>Südafrika-Regionspaar für Media Services verfügbar 

Media Services ist jetzt in den Regionen „Südafrika, Norden“ und „Südafrika, Westen“ verfügbar.

Weiter Informationen finden Sie unter [Clouds und Regionen, in denen Media Services v3 verfügbar ist](azure-clouds-regions.md).

###  <a name="media-services-v2"></a>Media Services v2  

#### <a name="deprecation-of-media-processors"></a>Einstellung von Medienprozessoren

Wir kündigen die Einstellung der Medienprozessoren *Windows Azure Media Encoder* (WAME) und *Azure Media Encoder* (AME) an, die eingestellt werden. Die Daten zur Einstellung finden Sie im Artikel [Legacykomponenten](../previous/legacy-components.md).

Weitere Informationen finden Sie unter [Migrieren von WAME zu Media Encoder Standard](../previous/migrate-windows-azure-media-encoder.md) und [Migrieren von AME zu Media Encoder Standard](../previous/migrate-azure-media-encoder.md).
 
## <a name="july-2019"></a>Juli 2019

### <a name="content-protection"></a>Inhaltsschutz

Beim Streamen von Inhalten, die mittels Tokeneinschränkung geschützt sind, benötigen Endbenutzer ein Token, das im Rahmen der Schlüsselübermittlungsanforderung gesendet wird. Mit dem Feature zum *Verhindern der Tokenwiedergabe* können Media Services-Kunden einen Grenzwert festlegen, der bestimmt, wie oft ein Token zum Anfordern eines Schlüssels oder einer Lizenz verwendet werden kann. Weitere Informationen finden Sie unter [Token Replay Prevention](drm-content-protection-concept.md#token-replay-prevention) (Verhindern der Tokenwiedergabe).

Seit Juli war die Previewfunktion nur in „USA, Mitte“ und „USA, Westen-Mitte“ verfügbar.

## <a name="june-2019"></a>Juni 2019

### <a name="video-subclipping"></a>Video-Subclips

Sie können jetzt mithilfe eines [Auftrags](/rest/api/media/jobs) Videos beim Codieren zuschneiden oder Subclips für diese erstellen. 

Dies funktioniert mit jeder [Transformation](/rest/api/media/transforms), die entweder mit den [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset)-Voreinstellungen oder mit den [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset)-Voreinstellungen erstellt wird. 

Beispiele finden Sie hier:

* [Erstellen von Subclips mit .NET](transform-subclip-video-dotnet-how-to.md)
* [Erstellen von Subclips mit REST](transform-subclip-video-rest-how-to.md)

## <a name="may-2019"></a>Mai 2019

### <a name="azure-monitor-support-for-media-services-diagnostic-logs-and-metrics"></a>Azure Monitor-Unterstützung für Media Services-Diagnoseprotokolle und -Metriken

Sie können jetzt Azure Monitor verwenden, um die von Media Services ausgegebenen Telemetriedaten anzuzeigen.

* Nutzen Sie die Azure Monitor-Diagnoseprotokolle, um Anforderungen zu überwachen, die vom Endpunkt für die Media Services-Schlüsselbereitstellung gesendet werden. 
* Überwachen Sie die Metriken, die von den [Streamingendpunkten](stream-streaming-endpoint-concept.md) von Media Services ausgegeben werden.   

Ausführliche Informationen finden Sie unter [Überwachen von Media Services-Metriken und -Diagnoseprotokollen](monitoring/monitor-media-services-data-reference.md).

### <a name="multi-audio-tracks-support-in-dynamic-packaging"></a>Unterstützung von mehreren Audiospuren bei der dynamischen Paketerstellung 

Beim Streamen von Medienobjekten, die mehrere Audiospuren mit mehreren Codecs und Sprachen aufweisen, unterstützt die [dynamische Paketerstellung](encode-dynamic-packaging-concept.md) jetzt mehrere Audiospuren für die HLS-Ausgabe (Version 4 oder höher).

### <a name="korea-regional-pair-is-open-for-media-services"></a>Korea-Regionspaar für Media Services verfügbar 

Microsoft Azure Media Services ist jetzt in den Regionen „Südkorea, Mitte“ und „Südkorea, Süden“ verfügbar. 

Weiter Informationen finden Sie unter [Clouds und Regionen, in denen Media Services v3 verfügbar ist](azure-clouds-regions.md).

### <a name="performance-improvements"></a>Leistungsverbesserungen

Updates wurden hinzugefügt, die Verbesserungen an der Media Services-Leistung umfassen.

* Die maximal unterstützte Dateigröße für die Verarbeitung wurde aktualisiert. Weitere Informationen finden Sie unter [Kontingente und Grenzwerte](limits-quotas-constraints-reference.md).
* [Verbesserungen der Codierungsgeschwindigkeit](concept-media-reserved-units.md).

## <a name="april-2019"></a>April 2019

### <a name="new-presets"></a>Neue Voreinstellungen

* [FaceDetectorPreset](/rest/api/media/transforms/createorupdate#facedetectorpreset) wurde zu den Voreinstellungen des integrierten Analysetools hinzugefügt.
* [ContentAwareEncodingExperimental](/rest/api/media/transforms/createorupdate#encodernamedpreset) wurde zu den Voreinstellungen des integrierten Encoders hinzugefügt. Weitere Informationen finden Sie unter [Inhaltsbezogene Codierung](encode-content-aware-concept.md). 

## <a name="march-2019"></a>März 2019

Die dynamische Paketerstellung unterstützt jetzt Dolby Atmos. Weitere Informationen finden Sie unter [Von der dynamischen Paketerstellung unterstützte Audiocodecs](encode-dynamic-packaging-concept.md#audio-codecs-supported-by-dynamic-packaging).

Sie können nun eine Liste von Medienobjekt- oder Kontenfiltern angeben, die für Ihren Streaminglocator gelten würden. Weitere Informationen finden Sie unter [Zuordnen von Filtern mit Streaminglocator](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="february-2019"></a>Februar 2019

Media Services v3 wird jetzt in nationalen Azure-Clouds unterstützt. Noch sind nicht alle Funktionen in allen Clouds verfügbar. Ausführliche Informationen finden Sie unter [Clouds und Regionen, in denen Azure Media Services v3 verfügbar ist](azure-clouds-regions.md).

Das Ereignis [Microsoft.Media.JobOutputProgress](monitoring/media-services-event-schemas.md#monitoring-job-output-progress) wurde den Azure Event Grid-Schemas für Media Services hinzugefügt.

## <a name="january-2019"></a>Januar 2019

### <a name="media-encoder-standard-and-mpi-files"></a>Media Encoder Standard und MPI-Dateien 

Bei der Codierung mit Media Encoder Standard zum Erzeugen von MP4-Dateien wird eine neue MPI-Datei generiert und dem Ausgabeasset hinzugefügt. Diese MPI-Datei dient zum Verbessern der Leistung für die [dynamische Paketerstellung](encode-dynamic-packaging-concept.md) und Streamingszenarios.

Sie sollten weder die MPI-Datei ändern oder entfernen noch beliebige Abhängigkeiten vom Vorhandensein (oder Nichtvorhandensein) einer solchen Datei in Ihren Dienst integrieren.

## <a name="december-2018"></a>Dezember 2018

Zu den Updates in der GA-Version der V3-API gehören:
       
* Die **PresentationTimeRange**-Eigenschaften sind nicht mehr für **Medienobjektfilter** und **Kontofilter** erforderlich. 
* Die Abfrageoptionen „$top“ und „$skip“ für **Aufträge** und **Transformationen** wurden entfernt, und „$orderby“ wurde hinzugefügt. Beim Hinzufügen der neuen Sortierungsfunktionalität wurde festgestellt, dass die Optionen „$top“ und „$skip“ versehentlich zuvor verfügbar gemacht wurden, obwohl sie nicht implementiert sind.
* Enumerationserweiterbarkeit wurde erneut aktiviert. Dieses Feature wurde in den Vorschauversionen des SDK aktiviert und wurde in der GA-Version versehentlich deaktiviert.
* Zwei vordefinierte Streamingrichtlinien wurden umbenannt. **SecureStreaming** ist jetzt **MultiDrmCencStreaming**. **SecureStreamingWithFairPlay** ist jetzt **Predefined_MultiDrmStreaming**.

## <a name="november-2018"></a>November 2018

Das CLI 2.0-Modul ist ab sofort für [Azure Media Services v3 (allgemein verfügbar)](/cli/azure/ams) v2.0.50 verfügbar.

### <a name="new-commands"></a>Neue Befehle

- [az ams account](/cli/azure/ams/account)
- [az ams account-filter](/cli/azure/ams/account-filter)
- [az ams asset](/cli/azure/ams/asset)
- [az ams asset-filter](/cli/azure/ams/asset-filter)
- [az ams content-key-policy](/cli/azure/ams/content-key-policy)
- [az ams job](/cli/azure/ams/job)
- [az ams live-event](/cli/azure/ams/live-event)
- [az ams live-output](/cli/azure/ams/live-output)
- [az ams streaming-endpoint](/cli/azure/ams/streaming-endpoint)
- [az ams streaming-locator](/cli/azure/ams/streaming-locator)
- [az ams account mru:](/cli/azure/ams/account/mru) Hiermit können Sie reservierte Einheiten für Medien verwalten. Weitere Informationen finden Sie unter [Skalieren von reservierten Einheiten für Medien](media-reserved-units-cli-how-to.md).

### <a name="new-features-and-breaking-changes"></a>Neue Features und Breaking Changes

#### <a name="asset-commands"></a>Assetbefehle

- Die Argumente ```--storage-account``` und ```--container``` wurden hinzugefügt.
- Standardwerte für Ablaufzeit (aktueller Zeitpunkt + 23 Std.) und Berechtigungen (Lesen) im Befehl ```az ams asset get-sas-url``` wurden hinzugefügt.

#### <a name="job-commands"></a>Auftragsbefehle

- Die Argumente ```--correlation-data``` und ```--label``` wurden hinzugefügt.
- ```--output-asset-names``` wurde in ```--output-assets``` umbenannt. Ab sofort wird eine durch Leerzeichen getrennte Ressourcenliste im Format „assetName=Bezeichnung“ akzeptiert. Assets ohne Bezeichnung können wie folgt gesendet werden: „assetName=“.

#### <a name="streaming-locator-commands"></a>streaming locator-Befehle

- Der Basisbefehl ```az ams streaming locator``` wurde durch ```az ams streaming-locator``` ersetzt.
- Die Argumente ```--streaming-locator-id``` und ```--alternative-media-id support``` wurden hinzugefügt.
- Das Argument ```--content-keys argument``` wurde aktualisiert.
- ```--content-policy-name``` wurde in ```--content-key-policy-name``` umbenannt.

#### <a name="streaming-policy-commands"></a>streaming policy-Richtlinien

- Der Basisbefehl ```az ams streaming policy``` wurde durch ```az ams streaming-policy``` ersetzt.
- Unterstützung für Verschlüsselungsparameter für ```az ams streaming-policy create``` wurde hinzugefügt.

#### <a name="transform-commands"></a>transform-Befehle

- Das Argument ```--preset-names``` wurde durch ```--preset``` ersetzt. Ab sofort kann nur noch eine einzelne Ausgabe/Voreinstellung festgelegt werden. (Wenn Sie weitere hinzufügen möchten, müssen Sie ```az ams transform output add``` ausführen.) Darüber hinaus können Sie eine benutzerdefinierte Voreinstellung für den Standard-Encoder (StandardEncoderPreset) festlegen, indem Sie den Pfad an Ihr benutzerdefiniertes JSON-Objekt übergeben.
- Für ```az ams transform output remove``` kann der zu entfernende Ausgabeindex übergeben werden.
- Die Argumente ```--relative-priority, --on-error, --audio-language and --insights-to-extract``` wurden in ```az ams transform create``` und den Befehlen ```az ams transform output add``` hinzugefügt.

## <a name="october-2018---ga"></a>Oktober 2018 – Allgemeine Verfügbarkeit (GA)

In diesem Abschnitt werden die Oktober-Updates für Azure Media Services (AMS) beschrieben.

### <a name="rest-v3-ga-release"></a>REST v3-GA-Release

Das [REST v3-GA-Release](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) umfasst mehr APIs für den Livebetrieb, Manifestfilter für die Konto-/Ressourcenebene und DRM-Support.

#### <a name="azure-resource-management"></a>Azure-Ressourcenverwaltung 

Die Unterstützung der Azure-Ressourcenverwaltung ermöglicht eine einheitliche Verwaltung und Vorgangs-API (jetzt alles an einem Ort).

Ab dieser Version können Sie Resource Manager-Vorlagen verwenden, um Liveereignisse zu erstellen.

#### <a name="improvement-of-asset-operations"></a>Verbesserung von Medienobjektvorgängen 

Die folgenden Verbesserungen wurden eingeführt:

- Erfassen von HTTP(S)-URLs oder Azure Blob Storage-SAS-URLs
- Angeben von eigenen Containernamen für Medienobjekte 
- Einfachere Unterstützung der Ausgabe zur Erstellung von benutzerdefinierten Workflows mit Azure Functions

#### <a name="new-transform-object"></a>Neues Transform-Objekt

Mit dem neuen **Transform**-Objekt wird das Codierungsmodell vereinfacht. Es ist mit dem neuen Objekt einfach, Resource Manager-Vorlagen und -Voreinstellungen für die Codierung zu erstellen und gemeinsam zu nutzen. 

#### <a name="azure-active-directory-authentication-and-azure-rbac"></a>Azure Active Directory-Authentifizierung und Azure-RBAC

Die Azure AD-Authentifizierung und die rollenbasierte Zugriffssteuerung in Azure (Azure-RBAC) ermöglichen sichere Transformationen, Liveereignisse, Richtlinien für Inhaltsschlüssel oder den Zugriff auf Medienobjekte nach Rolle oder Benutzer in Azure AD.

#### <a name="client-sdks"></a>Client-SDKs  

In Media Services v3 unterstützte Sprachen: .NET Core, Java, Node.js, Ruby, TypeScript, Python, Go.

#### <a name="live-encoding-updates"></a>Updates der Livecodierung

Die folgenden Updates für die Livecodierung wurden eingeführt:

- Neuer Modus mit geringer Latenz für den Livebetrieb (10 Sekunden für den End-to-End-Vorgang).
- Verbesserte RTMP-Unterstützung (höhere Stabilität und bessere Unterstützung für Quellcodierer).
- Sichere RTMPS-Erfassung.

    Bei der Erstellung eines Liveereignisses erhalten Sie jetzt vier Erfassungs-URLs. Die vier Erfassungs-URLs sind nahezu identisch und verfügen über das gleiche Streamingtoken (AppId). Nur der Portnummernteil unterscheidet sich. Zwei der URLs dienen als primäre URL und Backup-URL für RTMPS. 
- Unterstützung der 24-Stunden-Transcodierung. 
- Verbesserte Unterstützung für Werbeeinblendungen in RTMP über SCTE35.

#### <a name="improved-event-grid-support"></a>Verbesserte Event Grid-Unterstützung

Folgende Verbesserungen der Event Grid-Unterstützung wurden eingeführt:

- Azure Event Grid-Integration, um die Entwicklung mit Logic Apps und Azure Functions zu vereinfachen. 
- Abonnieren von Ereignissen für Codierung, Livekanäle und mehr.

### <a name="cmaf-support"></a>CMAF-Unterstützung

CMAF- und „cbcs“-Verschlüsselungsunterstützung für Apple HLS- (iOS 11 und höher) und MPEG-DASH-Player, die CMAF unterstützen.

### <a name="video-indexer"></a>Video Indexer

Das Video Indexer-GA-Release wurde im August angekündigt. Neue Informationen zu derzeit unterstützten Funktionen finden Sie unter [Was ist Video Indexer?](../../azure-video-analyzer/video-analyzer-for-media-docs/video-indexer-overview.md?bc=%2fazure%2fmedia-services%2fvideo-indexer%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fmedia-services%2fvideo-indexer%2ftoc.json). 

### <a name="plans-for-changes"></a>Pläne für Änderungen

#### <a name="azure-cli-20"></a>Azure CLI 2.0
 
Das Azure CLI 2.0-Modul mit Vorgängen für alle Features (z.B. Livebetrieb, Richtlinien für Inhaltsschlüssel, Konto-/Ressourcenfilter, Streamingrichtlinien) ist in Kürze verfügbar. 

### <a name="known-issues"></a>Bekannte Probleme

Nur Kunden, die die Vorschauversion der API für Ressourcen- oder Kontofilter verwendet haben, sind vom folgenden Problem betroffen.

Wenn Sie zwischen dem 28.09. und dem 12.10. mit der Media Services v3 CLI oder den zugehörigen APIs Ressourcen- oder Kontofilter erstellt haben, müssen Sie aufgrund eines Versionskonflikts alle Ressourcen- und Kontofilter entfernen und neu erstellen. 

## <a name="may-2018---preview"></a>Mai 2018 – Vorschau

### <a name="net-sdk"></a>.NET SDK

Das .NET SDK enthält die folgenden Funktionen:

* **Transformationen** und **Aufträge** zum Codieren oder Analysieren von Medieninhalten. Beispiele finden Sie in den Tutorials zum [Streamen von Dateien](stream-files-tutorial-with-api.md) und [Analysieren](analyze-videos-tutorial.md).
* **Streaminglocators** zum Veröffentlichen und Streamen von Inhalten auf Endbenutzergeräten
* **Streamingrichtlinien** und **Inhaltsschlüssel-Richtlinien** zum Konfigurieren der Schlüsselübermittlung und des Inhaltsschutzes (DRM) bei der Inhaltsbereitstellung
* **Liveereignisse** und **Liveausgaben** zum Konfigurieren der Erfassung und Archivierung von Livestreaminginhalten
* **Medienobjekte** zum Speichern und Veröffentlichen von Medieninhalten in Azure Storage 
* **Streamingendpunkte** zum Konfigurieren und Skalieren der dynamischen Paketerstellung, der Verschlüsselung und des Streamings von Live- und On-Demand-Medieninhalten

### <a name="known-issues"></a>Bekannte Probleme

* Wenn Sie einen Auftrag übermitteln, können Sie angeben, dass Ihr Quellvideo mithilfe von HTTPS, URLs, SAS-URLs oder Pfaden zu Dateien in Azure Blob Storage erfasst wird. Media Services v3 unterstützt derzeit keine segmentierte Transfercodierung über HTTPS-URLs.

## <a name="ask-questions-give-feedback-get-updates"></a>Fragen stellen, Feedback geben, Updates abrufen

Im Artikel [Azure Media Services-Community](media-services-community.md) finden Sie verschiedene Möglichkeiten, Fragen zu stellen, Feedback zu geben und Updates zu Media Services zu bekommen.

## <a name="see-also"></a>Weitere Informationen

[Hinweise zur Migration von Media Services v2 zu v3](migrate-v-2-v-3-migration-introduction.md)

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht](media-services-overview.md)
- [Versionsanmerkungen zu Media Services v2](../previous/media-services-release-notes.md)
