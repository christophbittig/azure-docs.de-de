---
title: Livestreaming mit Media Services, Node.js und TypeScript
titleSuffix: Azure Media Services
description: Erfahren Sie, wie Sie Liveereignisse mithilfe von Node.js, TypeScript und OBS Studio streamen.
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
ms.custom: mvc, devx-track-nodejs
ms.date: 04/15/2021
ms.author: inhenkel
ms.openlocfilehash: 2b553548111c732ca778c439232e949b0abb4707
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2021
ms.locfileid: "129388601"
---
# <a name="tutorial-stream-live-with-media-services-by-using-nodejs-and-typescript"></a>Tutorial: Livestreaming mit Media Services, Node.js und TypeScript

In Azure Media Services sind [Liveereignisse](/rest/api/media/liveevents) für die Verarbeitung von Livestreaminginhalten zuständig. Bei einem Liveereignis wird ein Eingabeendpunkt (Erfassungs-URL) bereitgestellt, den Sie dann für einen Liveencoder bereitstellen. Das Liveereignis empfängt Eingabestreams aus dem Liveencoder und stellt diese zum Streamen über einen oder mehrere [Streamingendpunkte](/rest/api/media/streamingendpoints) zur Verfügung. Zudem stellen Liveereignisse einen Vorschauendpunkt (Vorschau-URL) bereit, mit dem Sie eine Vorschau des Streams anzeigen und überprüfen können, bevor Sie ihn weiter verarbeiten und übermitteln. 

In diesem Tutorial erfahren Sie, wie Sie unter Verwendung von Node.js und TypeScript ein Liveereignis vom Typ *Passthrough* erstellen und mit [OBS Studio](https://obsproject.com/download) einen Livestream dafür übertragen.

In diesem Lernprogramm lernen Sie Folgendes:

> [!div class="checklist"]
> * Laden Sie den Beispielcode herunter.
> * Untersuchen des Codes zum Konfigurieren und Durchführen des Livestreamings
> * Sehen Sie sich das Ereignis mit [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) auf der [Media Player Demowebsite](https://ampdemo.azureedge.net) an.
> * Bereinigen der Ressourcen


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> In diesem Tutorial werden Node.js-Beispiele verwendet. Die allgemeinen Schritte sind jedoch auch bei Verwendung der [REST-API](/rest/api/media/liveevents), der [CLI](/cli/azure/ams/live-event) oder anderer unterstützter [SDKs](media-services-apis-overview.md#sdks) gleich. 

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

- Installieren Sie [Node.js](https://nodejs.org/en/download/).
- Installieren Sie [TypeScript](https://www.typescriptlang.org/).
- [Erstellen Sie ein Media Services-Konto.](./create-account-howto.md) Merken Sie sich die Werte, die Sie für die Namen von Ressourcengruppe und Media Services-Konto verwendet haben.
- Führen Sie die Schritte unter [Zugreifen auf die Azure Media Services-API mit der Azure CLI](./access-api-howto.md) aus und speichern Sie die Anmeldeinformationen. Sie werden für den Zugriff auf die API sowie zum Konfigurieren Ihrer Umgebungsvariablendatei benötigt.
- Lesen Sie sich zunächst die Anleitung zum [Konfigurieren und Verbinden mit Node.js](./configure-connect-nodejs-howto.md) durch, um sich mit der Verwendung der Node.js-Client-SDK vertraut zu machen.
- Installieren Sie Visual Studio Code oder Visual Studio.
- [Richten Sie Ihre Visual Studio Code-Umgebung ein](https://code.visualstudio.com/Docs/languages/typescript), damit sie die TypeScript-Sprache unterstützt.

Sie benötigen diese zusätzlichen Elemente für Livestreamingsoftware:

- Eine Kamera oder ein Gerät (beispielsweise ein Laptop) zum Übertragen eines Ereignisses.
- Einen lokalen Softwareencoder, der Ihren Kameradatenstrom codiert und über das Real-Time Messaging Protocol (RTMP) an den Media Services-Livestreamingdienst sendet. Weitere Informationen finden Sie unter [Empfohlene lokale Liveencoder](encode-recommended-on-premises-live-encoders.md). Der Datenstrom muss das Format RTMP oder Smooth Streaming haben.

  In diesem Beispiel wird davon ausgegangen, dass Sie OBS Studio (Open Broadcaster Software) verwenden, um RTMP an den Erfassungsendpunkt zu übertragen. [Installieren Sie OBS Studio](https://obsproject.com/download). 

  Verwenden Sie in OBS Studio die folgenden Codierungseinstellungen:

  - Encoder: NVIDIA NVENC (sofern verfügbar) oder x264
  - Ratensteuerung: CBR
  - Bitrate: 2.500 KBit/s (bzw. für Ihren Computer geeignete Rate)
  - Keyframe-Intervall: 2 Sekunden (oder 1 Sekunde für geringe Latenzzeit)  
  - Voreinstellung: „Low-Latency Quality“ (Geringe Wartezeit: Qualität) oder „Low-Latency Performance“ (Geringe Wartezeit: Leistung) bei Verwendung von NVENC oder „veryfast“ (sehr schnell) bei Verwendung von x264
  - Profil: „high“ (hoch)
  - GPU: 0 (Auto)
  - Max. B-Frames: 2

> [!TIP]
> Lesen Sie [Livestreaming mit Azure Media Services v3](stream-live-streaming-concept.md), bevor Sie mit diesem Tutorial fortfahren.

## <a name="download-and-configure-the-sample"></a>Herunterladen und Konfigurieren des Beispiels

Klonen Sie mit folgendem Befehl das GitHub-Repository auf Ihren Computer, welches das Beispiel für Livestreaming mit Node.js enthält:  

```bash
git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
```

Das Livestreaming-Beispiel befindet sich im Ordner [Live](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/Live).

Kopieren Sie im Ordner [AMSv3Samples](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples) die Datei *sample.env* in eine neue Datei mit dem Namen *.env*, um die Einstellungen Ihrer Umgebungsvariablen zu speichern, die Sie im Artikel [Mit Azure CLI auf Azure Media Services-API zugreifen](./access-api-howto.md) gesammelt haben.
Achten Sie darauf, dass der Dateiname den Punkt (.) vor „env“ enthält, damit das Codebeispiel ordnungsgemäß funktioniert.

In der [ENV-Datei](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/sample.env) befinden sich Azure Active Directory-Anwendungsschlüssel (Azure AD) und -geheimnis. Sie enthält zudem Kontonamen und Abonnementinformationen, die zum Authentifizieren des SDK-Zugriffs auf Ihr Media Services-Konto erforderlich sind. Die Datei *.gitignore* ist bereits konfiguriert, um das Veröffentlichen dieser Datei auf Ihr geforktes Repository zu verhindern. Achten Sie auf die Sicherheit dieser Anmeldeinformationen, da diese wichtige Geheimnisse Ihres Kontos sind.

> [!IMPORTANT]
> In diesem Beispiel wird für jede Ressourcen ein eindeutiges Suffix verwendet. Wenn Sie das Debuggen abbrechen oder die App beenden, ohne das Beispiel vollständig zu durchlaufen, werden in Ihrem Konto mehrere Liveereignisse generiert. 
>
> Stellen Sie sicher, dass Sie die ausgeführten Liveereignisse beenden. Andernfalls *fallen für die Ereignisse Kosten an*! Am Ende der Programmausführung werden die Ressourcen automatisch bereinigt. Falls Programm bzw. Debugger vorzeitig beendet werden und dadurch die Programmausführung abgebrochen wird, vergewissern Sie sich im Portal, dass keine Liveereignisse im Ausführungs- oder Standby-Zustand vorhanden sind, um unerwünschte Abrechnungsgebühren zu vermeiden.

## <a name="examine-the-typescript-code-for-live-streaming"></a>Untersuchen des TypeScript-Codes für Livestreaming

In diesem Abschnitt werden die in der Datei [index.ts](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/Live/index.ts) des Projekts *Live* definierten Funktionen untersucht.

Das Beispiel erstellt für jede Ressource ein eindeutiges Suffix, damit keine Namenskonflikte auftreten, wenn Sie das Beispiel ohne Bereinigung der Ressourcen mehrmals ausführen.

### <a name="start-using-the-media-services-sdk-for-nodejs-with-typescript"></a>Erste Schritte mit dem Media Services SDK für Node.js mit TypeScript

Um Media Services-APIs mit Node.js verwenden zu können, muss zunächst das SDK-Modul [@azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices) mithilfe des npm-Paket-Managers hinzugefügt werden.

```bash
npm install @azure/arm-mediaservices
```

In der Datei *package.json* ist dies bereits für Sie konfiguriert. Sie müssen nur `npm install` ausführen, um die Module und Abhängigkeiten zu laden:

1. Öffnen Sie eine Eingabeaufforderung und navigieren Sie zum Verzeichnis für das Beispiel.
1. Wechseln Sie zum Ordner *AMSv3Samples*.

    ```bash
    cd AMSv3Samples
    ```

1. Installieren Sie die Pakete, die in der Datei *packages.json* verwendet werden.

    ```bash
    npm install 
    ```

1. Starten Sie Visual Studio Code aus dem Ordner *AMSv3Samples*. (Dies ist erforderlich, damit das Starten aus dem Ordner erfolgt, in dem sich der Ordner *.vscode* und die *tsconfig.json*-Dateien befinden.)

    ```bash
    cd ..
    code .
    ```

Öffnen Sie den Ordner für *Live*, und öffnen Sie die Datei *index.ts* im Visual Studio Code-Editor.

Drücken Sie in der Datei *index.ts* die Taste F5, um den Debugger zu öffnen.

### <a name="create-the-media-services-client"></a>Erstellen des Media Services-Clients

Im folgenden Codeausschnitt wird die Erstellung des Media Services-Clients in Node.js gezeigt.

In diesem Code ändern Sie die `longRunningOperationRetryTimeout`-Eigenschaft von `AzureMediaServicesOptions` vom Standardwert (30 Sekunden) zu 2 Sekunden. Diese Änderung reduziert die Zeit, die benötigt wird, um den Status eines Vorgangs mit langer Ausführungsdauer auf dem Azure Resource Manager-Endpunkt abzufragen. Es verkürzt die Zeit zum Abschließen wichtiger Vorgänge wie das Erstellen von Liveereignissen, das Starten und Beenden, bei denen es sich um asynchrone Aufrufe handelt. Für die meisten Szenarien wird ein Wert von 2 Sekunden empfohlen.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>Erstellen eines Liveereignisses

In diesem Abschnitt wird das Erstellen eines Liveereignisses vom Typ *Passthrough* der SKU „Basic“ (`LiveEventEncodingType` auf `PassthroughBasic` festgelegt) beschrieben. Weitere Informationen zu den verfügbaren Typen finden Sie unter [Liveereignistypen](live-event-outputs-concept.md#live-event-types). Neben Passthrough der SKU „Basic“ oder „Standard“ können Sie ein Livecodierungsereignis für eine Cloudcodierung mit adaptiver Bitrate (720p oder 1080p) verwenden.
 
Beim Erstellen des Liveereignisses können Sie beispielsweise Folgendes angeben:

* **Erfassungsprotokoll für das Liveereignis**: Derzeit werden die Protokolle RTMP, RTMPS und Smooth Streaming unterstützt. Die Protokolloption kann nicht geändert werden, während das Liveereignis oder die zugehörigen Liveausgaben aktiv sind. Sollten Sie verschiedene Protokolle benötigen, erstellen Sie für jedes Streamingprotokoll ein separates Liveereignis.  
* **IP-Einschränkungen für Erfassung und Vorschau**: Sie können die IP-Adressen definieren, die ein Video für dieses Liveereignis erfassen dürfen. Zulässige IP-Adressen können als eine der folgenden Optionen angegeben werden:

  * Einzelne IP-Adresse (z. B. `10.0.0.1`)
  * Ein IP-Adressbereich, für den eine IP-Adresse und eine CIDR-Subnetzmaske (Classless Inter-Domain Routing) verwendet werden (z. B. `10.0.0.1/22`)
  * Ein IP-Adressbereich, für den eine IP-Adresse und Subnetzmaske in Dezimalschreibweise mit Punkten (z. B. `10.0.0.1(255.255.252.0)`) verwendet werden

  Wenn keine IP-Adressen angegeben sind und es keine Regeldefinition gibt, sind keine IP-Adressen zulässig. Erstellen Sie eine Regel, und geben Sie `0.0.0.0/0` an, um alle IP-Adressen zuzulassen. Die IP-Adressen müssen in einem der folgenden Formate vorliegen: IPv4-Adresse mit vier Ziffern oder CIDR-Adressbereich.
* **Autostart für ein Ereignis, während Sie es erstellen**: Wenn für den automatischen Start `true` festgelegt ist, wird das Liveereignis nach der Erstellung gestartet. Dies bedeutet, dass die Abrechnung beginnt, sobald die Ausführung des Liveereignisses startet. Sie müssen für die Liveereignisressource explizit `Stop` auswählen, damit keine weiteren Gebühren anfallen. Weitere Informationen finden Sie unter [Zustandswerte von Liveereignissen und Abrechnung](live-event-states-billing-concept.md).

  Standbymodi sind verfügbar, um das Liveereignis in einem kostengünstigeren „zugeordneten“ Zustand zu starten, der den Wechsel in einen Ausführungszustand beschleunigt. Dies ist etwa im Falle von Pools der heißen Ebene hilfreich, die schnell Kanäle für Streamer bereitstellen müssen.
* **Ein statischer Hostname und eine eindeutige GUID**: Legen Sie die Eigenschaft `useStaticHostname` auf `true` fest, um eine vorhersagbare Erfassungs-URL zu erhalten, die zudem einfacher in einem hardwarebasierten Liveencoder verwaltet werden kann. Verwenden Sie für `accessToken` eine benutzerdefinierte, eindeutige GUID. Ausführliche Informationen finden Sie unter [Erfassungs-URLs für Liveereignisse](live-event-outputs-concept.md#live-event-ingest-urls).

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateLiveEvent)]

### <a name="create-an-asset-to-record-and-archive-the-live-event"></a>Erstellen eines Assets zum Aufzeichnen und Archivieren des Liveereignisses

Im folgenden Codeblock wird ein leeres Asset als „Videokassette“ zum Aufzeichnen Ihres Liveereignisarchivs erstellt.

Beim Lernen dieser Konzepte ist es hilfreich, wenn Sie sich dieses Medienobjekt als „Videokassette“ vorstellen, wie sie früher einmal in einen Videorekorder eingelegt wurde. Die Liveausgabe fungiert hierbei als „Videorekorder“. Das Liveereignis ist das vom Computer ausgegebene „Videosignal“.

Das Medienobjekt (die „Videokassette“) kann zu einem beliebigen Zeitpunkt erstellt werden. Sie werden das leere Objekt an das Liveausgabeobjekt, den „Videorekorder“ in dieser Analogie, übertragen.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateAsset)]

### <a name="create-the-live-output"></a>Erstellen der Liveausgabe

In diesem Abschnitt wird eine Liveausgabe erstellt, die den Medienobjektnamen als Eingabe verwendet, um anzugeben, wo das Liveereignis aufgezeichnet werden soll. Darüber hinaus wird das Timeshift-Fenster (DVR) für die Aufzeichnung eingerichtet.

Im Beispielcode wird die Einrichtung eines Timeshift-Fensters von 1 Stunde gezeigt. Dadurch können Clients die letzte Stunde des Ereignisses ab einer beliebigen Stelle wiedergeben. Darüber hinaus wird nur die letzte Stunde des Liveereignisses im Archiv gespeichert. Dieser Wert kann auf bis zu 25 Stunden erhöht werden.  Beachten Sie auch, dass Sie die Benennung der Ausgabemanifeste steuern können, welche die Manifeste für HTTP LIVE STREAMING (HLS) und das dynamische adaptive Streaming per HTTP (Dynamic Adaptive Streaming over HTTP, DASH) bei der Veröffentlichung in Ihren URL-Pfaden verwenden.

Die Liveausgabe (in dieser Analogie der „Videorekorder“) kann ebenfalls zu einem beliebigen Zeitpunkt erstellt werden. Sie können also eine Liveausgabe vor oder nach dem Starten der Signalübertragung erstellen. Wenn es schnell gehen muss, empfiehlt es sich häufig, die Ausgabe vor dem Starten der Signalübertragung zu erstellen.

Liveausgaben beginnen, wenn sie erstellt werden, und werden beim Löschen angehalten.  Wenn Sie die Liveausgabe löschen, bleiben das zugrunde liegende Medienobjekt und dessen Inhalt erhalten. Der Vorgang ist mit dem Auswerfen der Videokassette vergleichbar. Das Medienobjekt mit der Aufzeichnung bleibt so lange gespeichert, wie Sie möchten. Wenn es ausgeworfen wird (d. h. wenn die Liveausgabe gelöscht wird), ist sie sofort für die bedarfsbasierte Anzeige verfügbar.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateLiveOutput)]


### <a name="get-ingest-urls"></a>Abrufen von Erfassungs-URLs

Sobald das Liveereignis erstellt wurde, können Sie Erfassungs-URLs abrufen, die Sie dem Liveencoder bereitstellen. Der Encoder gibt mit diesen URLs einen Livestream per RTMP-Protokoll ein.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#GetIngestURL)]

### <a name="get-the-preview-url"></a>Abrufen der Vorschau-URL

Rufen Sie mit `previewEndpoint` die Vorschau-URL ab, und überprüfen Sie, ob die Eingabe des Encoders empfangen wird.

> [!IMPORTANT]
> Vergewissern Sie sich vor dem Fortfahren, dass das Video an die Vorschau-URL übertragen wird.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#GetPreviewURL)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>Erstellen und Verwalten von Liveereignissen und Liveausgaben

Sobald der Stream bei Ihrem Liveereignis eingeht, können Sie das Streamingereignis starten, indem Sie einen Streaminglocator für Ihre Clientplayer veröffentlichen. Dadurch wird er über den Streamingendpunkt für die Zuschauer verfügbar gemacht.

Erstellen Sie zunächst das Signal, indem Sie das Liveereignis erstellen. Das Signal wird erst übertragen, wenn Sie dieses Liveereignis starten und Ihren Encoder mit der Eingabe verbinden.

Um die „Videoaufnahme“ zu beenden, rufen Sie `delete` bei `LiveOutput` auf. Durch diese Aktion wird der *Inhalt* Ihres Archivs auf dem „Video“ (Asset) nicht gelöscht. Es wird nur der „Videorekorder“ gelöscht und die Archivierung beendet. Das Medienobjekt bleibt mit dem archivierten Videoinhalt erhalten, bis Sie explizit `delete` für das Medienobjekt aufrufen. Nach dem Löschen von `LiveOutput` kann der aufgezeichnete Inhalt des Medienobjekts weiterhin über alle bereits veröffentlichten Streaminglocator-URLs wiedergegeben werden. 

Wenn Sie verhindern möchten, dass ein Client die archivierten Inhalte abspielen kann, müssen Sie zunächst alle Locatoren vom Asset entfernen. Leeren Sie auch den CDN-Cache (Content Delivery Network) im URL-Pfad, wenn Sie ein CDN für die Übermittlung verwenden. Andernfalls bleibt der Inhalt im CDN-Cache erhalten für den Zeitraum, der im CDN als Standardgültigkeitsdauer festgelegt ist (bis zu 72 Stunden).

#### <a name="create-a-streaming-locator-to-publish-hls-and-dash-manifests"></a>Erstellen eines Streaminglocators zum Veröffentlichen von HLS- und DASH-Manifesten

> [!NOTE]
> Beim Erstellen Ihres Media Services-Kontos wird dem Konto ein Standard-Streamingendpunkt im Zustand „Beendet“ hinzugefügt. Um mit dem Streamen Ihrer Inhalte zu beginnen und die [dynamische Paketerstellung](encode-dynamic-packaging-concept.md) und dynamische Verschlüsselung zu nutzen, muss sich der Streamingendpunkt, von dem Sie Inhalte streamen möchten, im Zustand „Wird ausgeführt“ befinden.

Wenn Sie das Medienobjekt mit einem Streaminglocator veröffentlichen, kann das Liveereignis (maximal bis zur DVR-Fensterlänge) bis zum Ablaufen oder Löschen des Streaminglocators (je nachdem, was zuerst eintritt) weiterhin angezeigt werden. Auf diese Weise können Sie die virtuelle Aufzeichnung so verfügbar machen, dass sie von Ihrer Zielgruppe live und „On-Demand“ angesehen werden kann. Mit der gleichen URL kann das Liveereignis, das DVR-Fenster oder das On-Demand-Medienobjekt angesehen werden, wenn die Aufzeichnung abgeschlossen ist (nach dem Löschen der Liveausgabe).

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateStreamingLocator)]

#### <a name="build-the-paths-to-the-hls-and-dash-manifests"></a>Erstellen der Pfade zu den HLS- und DASH-Manifesten

Die Methode `BuildManifestPaths` im Beispiel zeigt die deterministische Erstellung der Streamingpfade für die HLS- oder DASH-Übermittlung an verschiedene Clients und Playerframeworks.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#BuildManifestPaths)]

## <a name="watch-the-event"></a>Ansehen des Ereignisses

Kopieren Sie zum Wiedergeben des Ereignisses die Streaming-URL, die Sie beim Ausführen des Codes unter „Erstellen eines Streaminglocators“ erhalten haben. Sie können einen Medienplayer Ihrer Wahl verwenden. [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) ist verfügbar, um Ihren Datenstrom auf der [Media Player-Demowebsite](https://ampdemo.azureedge.net) zu testen.

Für ein Liveereignis werden Ereignisse bei der Beendigung automatisch in On-Demand-Inhalte konvertiert. Auch nach dem Beenden und Löschen des Ereignisses können die Benutzer archivierte Inhalte als Video auf Abruf streamen, solange Sie das Medienobjekt nicht löschen. Ein Medienobjekt kann nicht gelöscht werden, wenn es ein Ereignis verwendet. Zuerst muss das betreffende Ereignis gelöscht werden.

## <a name="clean-up-resources-in-your-media-services-account"></a>Bereinigen von Ressourcen in Ihrem Media Services-Konto

Wenn Sie die Anwendung bis zum Ende ausführen, werden in der Funktion `cleanUpResources` automatisch alle verwendeten Ressourcen bereinigt. Stellen Sie sicher, dass die Anwendung bzw. der Debugger bis zum Ende ausgeführt wird. Andernfalls kommt es möglicherweise zu einem Ressourcenleck sowie zu aktiven Liveereignissen in Ihrem Konto. Vergewissern Sie sich im Azure-Portal, dass alle Ressourcen in Ihrem Media Services-Konto bereinigt wurden. 

Sehen Sie sich im Beispielcode die Methode `cleanUpResources` an, um weitere Details zu erhalten.

> [!IMPORTANT]
> Wenn Sie das Liveereignis nicht beenden, fallen weiter Kosten dafür an. Beachten Sie, dass das Liveereignis möglicherweise im Abrechnungszustand verbleibt, wenn das Projekt oder Programm nicht mehr reagiert oder aufgrund eines anderen Problems geschlossen wird.

## <a name="ask-questions-give-feedback-get-updates"></a>Fragen stellen, Feedback geben, Updates abrufen

Im Artikel [Azure Media Services-Community](media-services-community.md) finden Sie verschiedene Möglichkeiten, Fragen zu stellen, Feedback zu geben und Updates zu Media Services zu bekommen.

## <a name="more-developer-documentation-for-nodejs-on-azure"></a>Weitere Entwicklerdokumentation für Node.js in Azure

- [Azure für JavaScript- und Node.js-Entwickler](/azure/developer/javascript/)
- [Media Services-Quellcode im @azure/azure-sdk-for-jsGitHub-Repository](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Dokumentation zum Azure-Paket für Node.js-Entwickler](/javascript/api/overview/azure/)


## <a name="next-steps"></a>Nächste Schritte

[Streamen von Dateien](stream-files-tutorial-with-api.md)