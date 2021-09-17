---
title: Livestreaming mit Media Services unter Verwendung von .NET 5.0
titleSuffix: Azure Media Services
description: Hier finden Sie eine Anleitung zum Livestreaming mit .NET 5.0.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 06/13/2019
ms.author: inhenkel
ms.openlocfilehash: f10ef55a44aa917fd8f0fb3783dcd29284512d7e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732671"
---
# <a name="tutorial-stream-live-with-media-services-by-using-net-50"></a>Tutorial: Livestreaming mit Media Services unter Verwendung von .NET 5.0

In Azure Media Services sind [Liveereignisse](/rest/api/media/liveevents) für die Verarbeitung von Livestreaminginhalten zuständig. Bei einem Liveereignis wird ein Eingabeendpunkt (Erfassungs-URL) bereitgestellt, den Sie dann für einen Liveencoder bereitstellen. Das Liveereignis empfängt Eingabestreams aus dem Liveencoder und stellt diese zum Streamen über einen oder mehrere [Streamingendpunkte](/rest/api/media/streamingendpoints) zur Verfügung. Zudem stellen Liveereignisse einen Vorschauendpunkt (Vorschau-URL) bereit, mit dem Sie eine Vorschau des Streams anzeigen und überprüfen können, bevor Sie diesen weiter verarbeiten und übermitteln. 

In diesem Tutorial erfahren Sie, wie Sie unter Verwendung von .NET 5.0 ein Liveereignis vom Typ *Passthrough* erstellen. In diesem Lernprogramm lernen Sie Folgendes:

> [!div class="checklist"]
> * Herunterladen einer Beispiel-App
> * Untersuchen des Codes für das Livestreaming
> * Ansehen des Ereignisses mit [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) auf der [Media Player-Demowebsite](https://ampdemo.azureedge.net)
> * Bereinigen der Ressourcen

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> Auch wenn in diesem Tutorial die [.NET SDK](/dotnet/api/microsoft.azure.management.media.models.liveevent)-Beispiele verwendet werden, sind die allgemeinen Schritte für die [REST-API](/rest/api/media/liveevents), die [CLI](/cli/azure/ams/live-event) oder für andere unterstützte [SDKs](media-services-apis-overview.md#sdks) identisch. 

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

- Installieren Sie [Visual Studio Code für Windows/macOS/Linux](https://code.visualstudio.com/) oder [Visual Studio 2019 für Windows oder Mac](https://visualstudio.microsoft.com/).
- Installieren Sie das [.NET 5.0 SDK](https://dotnet.microsoft.com/download).
- [Erstellen Sie ein Media Services-Konto.](./account-create-how-to.md) Wichtig: Kopieren Sie die Details für den **API-Zugriff** im JSON-Format, oder speichern Sie die Werte, die zum Herstellen einer Verbindung mit dem Media Services-Konto benötigt werden, im *ENV*-Dateiformat (wie in diesem Beispiel).
- Führen Sie die Schritte unter [Abrufen von Anmeldeinformationen für den Zugriff auf die Media Services-API](./access-api-howto.md) aus, und speichern Sie die Anmeldeinformationen. Sie müssen die Anmeldeinformationen für den Zugriff auf die API in diesem Beispiel verwenden oder im *ENV*-Dateiformat eingeben. 

Sie benötigen diese zusätzlichen Elemente für Livestreamingsoftware:

- Eine Kamera oder ein Gerät (beispielsweise ein Laptop) zum Übertragen eines Ereignisses.
- Einen lokalen Softwareencoder, der Ihren Kameradatenstrom codiert und über das Real-Time Messaging Protocol (RTMP) an den Media Services-Livestreamingdienst sendet. Weitere Informationen finden Sie unter [Empfohlene lokale Liveencoder](encode-recommended-on-premises-live-encoders.md). Der Datenstrom muss das Format RTMP oder Smooth Streaming haben.  

  In diesem Beispiel wird davon ausgegangen, dass Sie OBS Studio (Open Broadcaster Software) verwenden, um RTMP an den Erfassungsendpunkt zu übertragen. [Installieren Sie OBS Studio](https://obsproject.com/download). 

> [!TIP]
> Lesen Sie [Livestreaming mit Azure Media Services v3](stream-live-streaming-concept.md), bevor Sie fortfahren. 

## <a name="download-and-configure-the-sample"></a>Herunterladen und Konfigurieren des Beispiels

Klonen Sie mit dem folgenden Befehl das GitHub-Repository mit dem Beispiel für das Livestreaming mit .NET auf Ihren Computer:  

```bash
git clone https://github.com/Azure-Samples/media-services-v3-dotnet.git
```

Das Livestreaming-Beispiel befindet sich im Ordner [Live](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Live).

[!INCLUDE [appsettings or .env file](./includes/note-appsettings-or-env-file.md)]

> [!IMPORTANT]
> In diesem Beispiel wird für jede Ressourcen ein eindeutiges Suffix verwendet. Wenn Sie das Debuggen abbrechen oder die App beenden, ohne das Beispiel vollständig zu durchlaufen, werden in Ihrem Konto mehrere Liveereignisse generiert.
>
> Stellen Sie sicher, dass Sie die ausgeführten Liveereignisse beenden. Andernfalls *fallen für die Ereignisse Kosten an*!

## <a name="examine-the-code-that-performs-live-streaming"></a>Untersuchen des Codes für Livestreaming

In diesem Abschnitt werden Funktionen untersucht, die in den Dateien [Authentication.cs](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/Common_Utils/Authentication.cs) (im Ordner „Common_Utils“) und [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/Live/LiveEventWithDVR/Program.cs) des Projekts *LiveEventWithDVR* definiert werden.

Das Beispiel erstellt für jede Ressource ein eindeutiges Suffix, damit keine Namenskonflikte auftreten, wenn Sie das Beispiel ohne Bereinigung der Ressourcen mehrmals ausführen.


### <a name="start-using-media-services-apis-with-the-net-sdk"></a>Beginnen mit der Verwendung von Media Services-APIs mit dem .NET SDK

Mit „Authentication.cs“ wird unter Verwendung der Anmeldeinformationen aus den lokalen Konfigurationsdateien („appsettings.json“ oder „.env)“ ein `AzureMediaServicesClient`-Objekt erstellt.

Mit einem `AzureMediaServicesClient`-Objekt können Sie mit der Nutzung von Media Services-APIs mit .NET beginnen. Zum Erstellen des Objekts müssen Sie Anmeldeinformationen für den Client bereitstellen, damit dieser per Azure Active Directory eine Verbindung mit Azure herstellen kann. Dies wird in `GetCredentailsAsync` implementiert. Eine weitere Option ist die interaktive Authentifizierung, die in `GetCredentialsInteractiveAuthAsync` implementiert ist.

[!code-csharp[Main](../../../media-services-v3-dotnet/Common_Utils/Authentication.cs#CreateMediaServicesClientAsync)]

Im Code, den Sie am Anfang des Artikels geklont haben, wird mit der Funktion `GetCredentialsAsync` das Objekt `ServiceClientCredentials` erstellt. Dies erfolgt basierend auf den Anmeldeinformationen in der lokalen Konfigurationsdatei (*appsettings.json*) oder über die Datei *.env* mit den Umgebungsvariablen im Stammverzeichnis des Repositorys.

[!code-csharp[Main](../../../media-services-v3-dotnet/Common_Utils/Authentication.cs#GetCredentialsAsync)]

Bei der interaktiven Authentifizierung wird durch die Funktion `GetCredentialsInteractiveAuthAsync` das Objekt `ServiceClientCredentials` erstellt, und zwar basierend auf einer interaktiven Authentifizierung und den Verbindungsparametern, die in der lokalen Konfigurationsdatei (*appsettings.json*) oder über die Umgebungsvariablendatei ( *.env*) im Stammverzeichnis des Repositorys angegeben sind. In diesem Fall sind „AADCLIENTID“ und „AADSECRET“ in der Konfigurations- oder Umgebungsvariablendatei nicht erforderlich.

[!code-csharp[Main](../../../media-services-v3-dotnet/Common_Utils/Authentication.cs#GetCredentialsInteractiveAuthAsync)]


### <a name="create-a-live-event"></a>Erstellen eines Liveereignisses

In diesem Abschnitt wird das Erstellen eines Liveereignisses vom Typ *Passthrough* (`LiveEventEncodingType` auf `None` festgelegt) beschrieben. Weitere Informationen zu den verfügbaren Typen finden Sie unter [Liveereignistypen](live-event-outputs-concept.md#live-event-types). Zusätzlich zu Passthrough können Sie ein Ereignis mit Livetranscodierung für eine Cloudcodierung mit adaptiver Bitrate (720p oder 1080p) verwenden.
 
Beim Erstellen des Liveereignisses können Sie beispielsweise Folgendes angeben:

* **Erfassungsprotokoll für das Liveereignis**: Derzeit werden die Protokolle RTMP, RTMPS und Smooth Streaming unterstützt. Die Protokolloption kann nicht geändert werden, während das Liveereignis oder die zugehörigen Liveausgaben aktiv sind. Sollten Sie verschiedene Protokolle benötigen, erstellen Sie für jedes Streamingprotokoll ein separates Liveereignis. 
* **IP-Einschränkungen für Erfassung und Vorschau**: Sie können die IP-Adressen definieren, die ein Video für dieses Liveereignis erfassen dürfen. Zulässige IP-Adressen können als eine der folgenden Optionen angegeben werden:

  * Einzelne IP-Adresse (z. B. `10.0.0.1`)
  * Ein IP-Adressbereich, für den eine IP-Adresse und eine CIDR-Subnetzmaske (Classless Inter-Domain Routing) verwendet werden (z. B. `10.0.0.1/22`)
  * Ein IP-Adressbereich, für den eine IP-Adresse und Subnetzmaske in Dezimalschreibweise mit Punkten (z. B. `10.0.0.1(255.255.252.0)`) verwendet werden

  Wenn keine IP-Adressen angegeben sind und es keine Regeldefinition gibt, sind keine IP-Adressen zulässig. Erstellen Sie eine Regel, und geben Sie `0.0.0.0/0` an, um alle IP-Adressen zuzulassen. Die IP-Adressen müssen in einem der folgenden Formate vorliegen: IPv4-Adresse mit vier Ziffern oder CIDR-Adressbereich.  
* **Autostart für ein Ereignis, während Sie es erstellen**: Wenn für den automatischen Start `true` festgelegt ist, wird das Liveereignis nach der Erstellung gestartet. Dies bedeutet, dass die Abrechnung beginnt, sobald die Ausführung des Liveereignisses startet. Sie müssen für die Liveereignisressource explizit `Stop` auswählen, damit keine weiteren Gebühren anfallen. Weitere Informationen finden Sie unter [Zustandswerte von Liveereignissen und Abrechnung](live-event-states-billing-concept.md).

  Standbymodi sind verfügbar, um das Liveereignis in einem kostengünstigeren „zugeordneten“ Zustand zu starten, der den Wechsel in einen Ausführungszustand beschleunigt. Dies ist etwa im Falle von Pools der heißen Ebene hilfreich, die schnell Kanäle für Streamer bereitstellen müssen.
* **Ein statischer Hostname und eine eindeutige GUID**: Legen Sie die Eigenschaft `useStaticHostname` auf `true` fest, um eine vorhersagbare Erfassungs-URL zu erhalten, die zudem einfacher in einem hardwarebasierten Liveencoder verwaltet werden kann. Ausführliche Informationen finden Sie unter [Erfassungs-URLs für Liveereignisse](live-event-outputs-concept.md#live-event-ingest-urls).

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateLiveEvent)]

### <a name="get-ingest-urls"></a>Abrufen von Erfassungs-URLs

Nachdem das Liveereignis erstellt wurde, können Sie Erfassungs-URLs abrufen, um diese für den Liveencoder bereitzustellen. Diese URLs werden vom Encoder zur Eingabe eines Livedatenstroms verwendet.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#GetIngestURL)]

### <a name="get-the-preview-url"></a>Abrufen der Vorschau-URL

Rufen Sie mit `previewEndpoint` die Vorschau-URL ab, und überprüfen Sie, ob die Eingabe des Encoders empfangen wird.

> [!IMPORTANT]
> Vergewissern Sie sich vor dem Fortfahren, dass das Video an die Vorschau-URL übertragen wird.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#GetPreviewURLs)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>Erstellen und Verwalten von Liveereignissen und Liveausgaben

Sobald der Stream an das Liveereignis übertragen wird, können Sie das Streamingereignis starten, indem Sie ein Medienobjekt, eine Liveausgabe und einen Streaminglocator erstellen. Dadurch wird der Datenstrom archiviert und über den Streamingendpunkt für die Zuschauer verfügbar gemacht.

Beim Erlernen dieser Konzepte ist es hilfreich, wenn Sie sich dieses Medienobjekt als „Videokassette“ vorstellen, wie sie früher einmal in einen Videorekorder eingelegt wurde. Die Liveausgabe fungiert hierbei als „Videorekorder“. Das Liveereignis ist das vom Videorekorder ausgegebene Videosignal.

Als Erstes erstellen Sie das Signal, indem Sie das Liveereignis erstellen. Das Signal wird erst übertragen, wenn Sie dieses Liveereignis starten und Ihren Encoder mit der Eingabe verbinden.

Die Videokassette kann zu einem beliebigen Zeitpunkt erstellt werden. Sie stellt lediglich ein leeres Medienobjekt dar, das an das Objekt für die Liveausgabe (in dieser Analogie: der Videorekorder) übergeben wird.

Der Videorekorder kann ebenfalls zu einem beliebigen Zeitpunkt erstellt werden. Sie können eine Liveausgabe vor oder nach dem Starten der Signalübertragung erstellen. Wenn es schnell gehen muss, kann es hilfreich sein, die Ausgabe vor dem Starten der Signalübertragung zu erstellen.

Um die „Videoaufnahme“ zu beenden, rufen Sie `delete` für `LiveOutput` auf. Mit dieser Aktion wird der *Inhalt* auf dem „Video“ (Medienobjekt) nicht gelöscht. Das Medienobjekt bleibt zusammen mit dem archivierten Videoinhalt erhalten, bis Sie direkt für das Medienobjekt explizit `delete` aufrufen. 

Im nächsten Abschnitt wird die Erstellung des Medienobjekts und der Liveausgabe Schritt für Schritt beschrieben.

#### <a name="create-an-asset"></a>Erstellen eines Medienobjekts

Erstellen Sie ein Medienobjekt, das von der Liveausgabe verwendet werden kann. Im Rahmen unserer Analogie ist dies die Videokassette zum Aufzeichnen des Livevideosignals. Benutzer können sich den Inhalt live oder bedarfsgesteuert (On-Demand) über diese virtuelle Videokassette ansehen.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateAsset)]

#### <a name="create-a-live-output"></a>Erstellen einer Liveausgabe

Liveausgaben beginnen, wenn sie erstellt werden, und enden, wenn sie gelöscht werden.  Wenn Sie die Liveausgabe löschen, bleiben das zugrunde liegende Medienobjekt und dessen Inhalt erhalten. Der Vorgang ist mit dem Auswerfen der Videokassette vergleichbar. Das Medienobjekt mit der Aufzeichnung bleibt so lange gespeichert, wie Sie möchten. Wenn es ausgeworfen wird (Löschen der Liveausgabe), ist es sofort für die bedarfsgesteuerte Anzeige verfügbar.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateLiveOutput)]

#### <a name="create-a-streaming-locator"></a>Erstellen Sie eines Streaminglocators

> [!NOTE]
> Beim Erstellen Ihres Media Services-Kontos wird dem Konto ein Standard-Streamingendpunkt im Zustand „Beendet“ hinzugefügt. Um mit dem Streamen Ihrer Inhalte zu beginnen und die [dynamische Paketerstellung](encode-dynamic-packaging-concept.md) und dynamische Verschlüsselung zu nutzen, muss sich der Streamingendpunkt, von dem Sie Inhalte streamen möchten, im Zustand „Wird ausgeführt“ befinden.

Wenn Sie das Medienobjekt mit einem Streaminglocator veröffentlichen, kann das Liveereignis (maximal bis zur DVR-Fensterlänge) bis zum Ablaufen oder Löschen des Streaminglocators (je nachdem, was zuerst eintritt) weiterhin angezeigt werden. Auf diese Weise können Sie die virtuelle Aufzeichnung so verfügbar machen, dass sie von Ihrer Zielgruppe live und „On-Demand“ angesehen werden kann. Über dieselbe URL kann das Liveereignis, das DVR-Fenster oder das On-Demand-Medienobjekt angesehen werden, nachdem die Aufzeichnung abgeschlossen ist (nach dem Löschen der Liveausgabe).

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateStreamingLocator)]

```csharp

// Get the URL to stream the output
ListPathsResponse paths = await client.StreamingLocators.ListPathsAsync(resourceGroupName, accountName, locatorName);

foreach (StreamingPath path in paths.StreamingPaths)
{
    UriBuilder uriBuilder = new UriBuilder();
    uriBuilder.Scheme = "https";
    uriBuilder.Host = streamingEndpoint.HostName;

    uriBuilder.Path = path.Paths[0];
    // Get the URL from the uriBuilder: uriBuilder.ToString()
}
```

### <a name="clean-up-resources-in-your-media-services-account"></a>Bereinigen von Ressourcen in Ihrem Media Services-Konto

Verwenden Sie das folgende Verfahren, wenn die Streamingereignisse beendet sind und Sie die zuvor bereitgestellten Ressourcen bereinigen möchten:

1. Beenden Sie die Datenstromeingabe vom Encoder.
1. Beenden Sie das Liveereignis. Nach Beendigung des Liveereignisses fallen dafür keine weiteren Kosten an. Wenn Sie den Kanal erneut starten, weist er die gleiche Erfassungs-URL auf, damit Sie den Encoder nicht erneut konfigurieren müssen.
1. Beenden Sie Ihren Streamingendpunkt, sofern Sie das Archiv Ihres Liveereignisses nicht als bedarfsgesteuerten Datenstrom bereitstellen möchten. Wenn sich das Liveereignis im Zustand „Beendet“ befindet, fallen dafür keine weiteren Kosten an.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CleanupLiveEventAndOutput)]

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CleanupLocatorAssetAndStreamingEndpoint)]

## <a name="watch-the-event"></a>Ansehen des Ereignisses

Drücken Sie **STRG+F5**, um den Code auszuführen. Dadurch werden Streaming-URLs ausgegeben, die Sie zum Ansehen Ihres Liveereignisses verwenden können. Kopieren Sie die Streaming-URL, die Sie zum Erstellen eines Streaminglocators erhalten haben. Sie können einen Medienplayer Ihrer Wahl verwenden. [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) ist verfügbar, um Ihren Datenstrom auf der [Media Player-Demowebsite](https://ampdemo.azureedge.net) zu testen.

Für ein Liveereignis werden Ereignisse bei der Beendigung automatisch in On-Demand-Inhalte konvertiert. Auch nach dem Beenden und Löschen des Ereignisses können die Benutzer archivierte Inhalte als Video auf Abruf streamen, solange Sie das Medienobjekt nicht löschen. Ein Medienobjekt kann nicht gelöscht werden, wenn es von einem Ereignis verwendet wird. Zuerst muss das betreffende Ereignis gelöscht werden.

## <a name="clean-up-remaining-resources"></a>Bereinigen der restlichen Ressourcen

Wenn Sie keine Ressourcen in Ihrer Ressourcengruppe mehr benötigen, einschließlich der Media Services und Speicherkonten, die Sie für dieses Tutorial erstellt haben, sollten Sie die zuvor erstellte Ressourcengruppe löschen.

Führen Sie den folgenden CLI-Befehl aus:

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> Wenn Sie das Liveereignis nicht beenden, fallen weiter Kosten dafür an. Beachten Sie, dass das Liveereignis möglicherweise im Abrechnungszustand verbleibt, wenn das Projekt oder Programm nicht mehr reagiert oder aufgrund eines anderen Problems geschlossen wird.

## <a name="ask-questions-give-feedback-get-updates"></a>Fragen stellen, Feedback geben, Updates abrufen

Im Artikel [Azure Media Services-Community](media-services-community.md) finden Sie verschiedene Möglichkeiten, Fragen zu stellen, Feedback zu geben und Updates zu Media Services zu bekommen.

## <a name="next-steps"></a>Nächste Schritte

[Streamen von Dateien](stream-files-tutorial-with-api.md)
 
