---
Titel: Hochladen, Codieren und Streamen mit Media Services v3 : Azure Media Services description: Tutorial zum Hochladen einer Datei, Codieren eines Videos und Streamen von Inhalten mit Media Services v3
services: media-services documentationcenter: '' author: IngridAtMicrosoft manager: femila editor: ''

ms.service: media-services ms.workload: ms.topic: tutorial ms.custom: mvc ms.date: 07/23/2021 ms.author: inhenkel
---

# <a name="tutorial-upload-encode-and-stream-videos-with-media-services-v3"></a>Tutorial: Hochladen, Codieren und Streamen von Videos mit Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

> [!NOTE]
> Auch wenn in diesem Tutorial die [.NET SDK](/dotnet/api/microsoft.azure.management.media.models.liveevent)-Beispiele verwendet werden, sind die allgemeinen Schritte für die [REST-API](/rest/api/media/liveevents), die [CLI](/cli/azure/ams/live-event) oder für andere unterstützte [SDKs](media-services-apis-overview.md#sdks) identisch.

Azure Media Services ermöglicht das Codieren Ihrer Mediendateien in Formaten, die mit einer Vielzahl von Browsern und Geräten wiedergegeben werden können. So können Sie Ihre Inhalte beispielsweise im HLS-Format von Apple oder im MPEG DASH-Format streamen. Vor dem Streamen sollten Sie Ihre digitale Mediendatei in hoher Qualität codieren. Hilfeinformationen zur Codierung finden Sie in der [Übersicht über die Codierung](encode-concept.md). In diesem Tutorial wird eine lokale Videodatei hochgeladen und die hochgeladene Datei codiert. Sie können auch Inhalte codieren, die Sie über eine HTTPS-URL zugänglich machen. Weitere Informationen finden Sie unter [Erstellen einer Auftragseingabe aus einer HTTP(S)-URL](job-input-from-http-how-to.md).

![Abspielen eines Videos mit Azure Media Player](./media/stream-files-tutorial-with-api/final-video.png)

Dieses Tutorial veranschaulicht folgende Vorgehensweisen:

> [!div class="checklist"]
> * Herunterladen der in diesem Thema beschriebenen Beispiel-App
> * Untersuchen des Codes zum Hochladen, Codieren und Streamen
> * Führen Sie die App aus.
> * Testen der Streaming-URL
> * Bereinigen der Ressourcen

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

- Installieren Sie [Visual Studio Code für Windows/macOS/Linux](https://code.visualstudio.com/) oder [Visual Studio 2019 für Windows oder Mac](https://visualstudio.microsoft.com/).
- Installieren Sie das [.NET 5.0 SDK](https://dotnet.microsoft.com/download).
- [Erstellen Sie ein Media Services-Konto.](./account-create-how-to.md) Wichtig: Kopieren Sie die Details für den **API-Zugriff** im JSON-Format, oder speichern Sie die Werte, die zum Herstellen einer Verbindung mit dem Media Services-Konto benötigt werden, im *ENV*-Dateiformat (wie in diesem Beispiel).
- Führen Sie die Schritte unter [Abrufen von Anmeldeinformationen für den Zugriff auf die Media Services-API](./access-api-howto.md) aus, und speichern Sie die Anmeldeinformationen. Sie müssen die Anmeldeinformationen für den Zugriff auf die API in diesem Beispiel verwenden oder im *ENV*-Dateiformat eingeben.

## <a name="download-and-configure-the-sample"></a>Herunterladen und Konfigurieren des Beispiels

Klonen Sie ein GitHub-Repository mit dem Beispiel zum .NET-Streaming auf Ihren Computer, indem Sie den folgenden Befehl verwenden:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

Das Beispiel befindet sich im Ordner [UploadEncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/main/AMSV3Tutorials/UploadEncodeAndStreamFiles).

[!INCLUDE [appsettings or .env file](./includes/note-appsettings-or-env-file.md)]

## <a name="examine-the-code-that-uploads-encodes-and-streams"></a>Überprüfen des Codes, mit dem hochgeladen, codiert und gestreamt wird

In diesem Abschnitt werden die Funktionen untersucht, die in der Datei „[Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/main/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs)“ des *UploadEncodeAndStreamFiles*-Projekts definiert werden.

Mit dem Beispiel werden die folgenden Aktionen durchgeführt:

1. Erstellen einer neuen **Transformation** (nach vorheriger Überprüfung, ob die angegebene Transformation vorhanden ist)
2. Erstellen eines **Ausgabemedienobjekts**, das als Ausgabe des **Codierungsauftrags** verwendet wird.
3. Erstellen eines **Eingabemedienobjekts** und Hochladen der angegebenen lokalen Videodatei in das Objekt. Das Medienobjekt wird als Eingabe für den Auftrag verwendet.
4. Übermittlung des Codierungsauftrag mit der erstellten Ein- und Ausgabe
5. Überprüfen des Auftragsstatus
6. Erstellen eines **Streaminglocators**
7. Erstellen von Streaming-URLs

### <a name="start-using-media-services-apis-with-the-net-sdk"></a>Beginnen mit der Verwendung von Media Services-APIs mit dem .NET SDK

Um mit der Verwendung von Media Services-APIs in .NET zu beginnen, müssen Sie ein `AzureMediaServicesClient`-Objekt erstellen. Zum Erstellen des Objekts müssen Sie Anmeldeinformationen für den Client bereitstellen, damit dieser per Azure Active Directory eine Verbindung mit Azure herstellen kann. Eine weitere Option ist die interaktive Authentifizierung, die in `GetCredentialsInteractiveAuthAsync` implementiert ist.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/Common_Utils/Authentication.cs#CreateMediaServicesClientAsync)]

Im Code, den Sie am Anfang des Artikels geklont haben, wird mit der Funktion `GetCredentialsAsync` das Objekt `ServiceClientCredentials` erstellt. Dies erfolgt basierend auf den Anmeldeinformationen in der lokalen Konfigurationsdatei (*appsettings.json*) oder über die Datei *.env* mit den Umgebungsvariablen im Stammverzeichnis des Repositorys.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/Common_Utils/Authentication.cs#GetCredentialsAsync)]

Bei der interaktiven Authentifizierung wird durch die Funktion `GetCredentialsInteractiveAuthAsync` das Objekt `ServiceClientCredentials` erstellt, und zwar basierend auf einer interaktiven Authentifizierung und den Verbindungsparametern, die in der lokalen Konfigurationsdatei (*appsettings.json*) oder über die Umgebungsvariablendatei ( *.env*) im Stammverzeichnis des Repositorys angegeben sind. In diesem Fall sind AADCLIENTID und AADSECRET in der Konfigurations- oder Umgebungsvariablendatei nicht erforderlich.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/Common_Utils/Authentication.cs#GetCredentialsInteractiveAuthAsync)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Erstellen eines Eingabeobjekts und Hochladen einer lokalen Datei in dieses

Die **CreateInputAsset**-Funktion erstellt ein neues [Eingabeobjekt](/rest/api/media/assets) und lädt die angegebene lokale Videodatei in dieses hoch. Dieses **Medienobjekt** wird als Eingabe für Ihren Codierungsauftrag verwendet. In Media Services v3 kann die Eingabe für einen **Auftrag** ein **Medienobjekt** sein, oder es kann sich um Inhalte handeln, die Sie für Ihr Media Services-Konto über HTTPS-URLs zur Verfügung stellen. Um zu erfahren, wie die Codierung aus einer HTTPS-URL erfolgt, lesen Sie [diesen](job-input-from-http-how-to.md) Artikel.

In Media Services v3 verwenden Sie Azure Storage-APIs zum Hochladen von Dateien. Im folgenden Codeausschnitt wird gezeigt, wie dies funktioniert.

Die folgende Funktion führt diese Aktionen aus:

* Erstellen eines **Medienobjekts**
* Abrufen einer nicht schreibgeschützten [SAS-URL](../../storage/common/storage-sas-overview.md) für den [Container](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-a-container) des Medienobjekts im Speicher

    Beachten Sie Folgendes: Wenn Sie die Funktion [ListContainerSas](/rest/api/media/assets/listcontainersas) des Medienobjekts zum Abrufen von SAS-URLs verwenden, gibt die Funktion mehrere SAS-URLs zurück, da für jedes Speicherkonto zwei Speicherkontoschlüssel vorhanden sind. Ein Speicherkonto verfügt über zwei Schlüssel, um eine nahtlose Rotation von Speicherkontoschlüsseln zu ermöglichen. (So kann beispielsweise ein Schlüssel geändert werden, während der andere in Gebrauch ist. Anschließend kann dann der neue Schlüssel verwendet werden, während der andere rotiert wird.) Die erste SAS-URL steht für Speicherschlüssel1 und die zweite für Speicherschlüssel2.
* Hochladen der Datei in den Container im Speicher mithilfe der SAS-URL

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-a-job"></a>Erstellen eines Ausgabeobjekts zum Speichern des Ergebnisses eines Auftrags

Das [Ausgabeobjekt](/rest/api/media/assets) speichert das Ergebnis Ihres Codierungsauftrags. Das Projekt definiert die **DownloadResults**-Funktion, die die Ergebnisse aus diesem Ausgabeobjekt in den Ordner „output“ herunterlädt, damit Sie sehen können, was Sie abgerufen haben.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>Erstellen einer Transformation und eines Auftrags, der die hochgeladene Datei codiert

Bei der Codierung oder Verarbeitung von Inhalten in Media Services ist es gängige Praxis, Codierungseinstellungen als Anleitung einzurichten. Anschließend übermitteln Sie einen **Auftrag**, um diese Anleitung auf ein Video anzuwenden. Durch die Übermittlung von neuen Aufträgen für jedes neue Video wenden Sie diese Anleitung auf alle Videos in Ihrer Bibliothek an. Eine solche Anleitung wird in Media Services als **Transformation** bezeichnet. Weitere Informationen finden Sie unter [Transformationen und Aufträge](./transform-jobs-concept.md). Das in diesem Tutorial beschriebene Beispiel definiert eine Anleitung, die das Video codiert, damit es auf eine Vielzahl von iOS- und Android-Geräte gestreamt werden kann.

#### <a name="transform"></a>Transformieren

Beim Erstellen einer neuen [Transformations](/rest/api/media/transforms)instanz müssen Sie angeben, was als Ausgabe generiert werden soll. Der erforderliche Parameter ist ein **TransformOutput**-Objekt, wie im folgenden Code gezeigt. Jedes **TransformOutput**-Objekt enthält eine **Voreinstellung**. Die **Voreinstellung** beschreibt die schrittweisen Anweisungen von Video- und/oder Audioverarbeitungsvorgängen, die verwendet werden sollen, um das gewünschte **TransformOutput**-Objekt zu generieren. Das in diesem Artikel beschriebene Beispiel verwendet eine integrierte Voreinstellung namens **AdaptiveStreaming**. Die Voreinstellung codiert das Eingabevideo in eine automatisch generierte Bitratenkette (Paare aus Bitrate und Auflösung) auf Basis der Eingabeauflösung und -bitrate und generiert ISO MP4-Dateien mit H.264-Video und AAC-Audio entsprechend jedem Paar aus Bitrate und Auflösung. Weitere Informationen zu dieser Voreinstellung finden Sie unter [Automatische Erstellen einer Bitratenkette](encode-autogen-bitrate-ladder.md).

Sie können ein integriertes EncoderNamedPreset-Objekt oder benutzerdefinierte Voreinstellungen verwenden. Weitere Informationen finden Sie unter [Anpassen von Encoder-Voreinstellungen](transform-custom-presets-how-to.md).

Beim Erstellen einer [Transformation](/rest/api/media/transforms) sollten Sie zunächst mit der **Get**-Methode überprüfen, ob eine solche bereits vorhanden ist. Der folgende Code zeigt dies. In Media Services v3 geben **Get**-Methoden für Entitäten **null** zurück, wenn die Entität nicht vorhanden ist (eine Überprüfung des Namens ohne Unterscheidung zwischen Groß-/Kleinschreibung ).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Auftrag

Wie bereits erwähnt, ist das [Transformations](/rest/api/media/transforms)objekt die Anleitung und ein [Auftrag](/rest/api/media/jobs) die tatsächliche Anforderung an Media Services, diese **Transformation** auf ein bestimmtes Eingabevideo oder einen Audioinhalt anzuwenden. Der **Auftrag** gibt Informationen wie den Speicherort des Eingabevideos und den Speicherort für die Ausgabe an.

In diesem Beispiel wurde das Eingabevideo vom lokalen Computer hochgeladen. Um zu erfahren, wie die Codierung aus einer HTTPS-URL erfolgt, lesen Sie [diesen](job-input-from-http-how-to.md) Artikel.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Warten auf den Abschluss des Auftrags

Der Abschluss des Auftrags nimmt einige Zeit in Anspruch. Wenn er erfolgt ist, möchten Sie benachrichtigt werden. Das Codebeispiel unten zeigt, wie der Status des [Auftrags](/rest/api/media/jobs) vom Dienst abgerufen wird. Der Abruf gehört aufgrund potenzieller Latenzzeiten nicht zu den empfohlenen Best Practices für Produktionsanwendungen. Der Abruf kann gedrosselt werden, wenn er für ein Konto im Übermaß verwendet wird. Entwickler sollten stattdessen Event Grid verwenden.

Event Grid ist für hohe Verfügbarkeit, konsistente Leistung und dynamische Skalierung ausgelegt. Mit Event Grid können Ihre Apps Ereignisse aus praktisch allen Azure-Diensten sowie aus benutzerdefinierten Quellen überwachen und darauf reagieren. Eine einfache, HTTP-basierte, reaktive Ereignisverarbeitung hilft Ihnen mit intelligentem Filtern und Routing von Ereignissen dabei, effiziente Lösungen zu erstellen.  Weitere Informationen finden Sie unter [Routing von Ereignissen an einen benutzerdefinierten Webendpunkt](monitoring/job-state-events-cli-how-to.md).

Der **Auftrag** durchläuft in der Regel die folgenden Zustände: **Geplant**, **In Warteschlange**, **Wird verarbeitet**, **Abgeschlossen** (Endzustand). Wenn für den Auftrag ein Fehler aufgetreten ist, erhalten Sie den Zustand **Fehler**. Wird der Auftrag gerade abgebrochen, befindet er sich im Zustand **Vorgang wird abgebrochen**. Nach Abschluss des Vorgangs lautet der Status **Abgebrochen**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#WaitForJobToFinish)]

### <a name="job-error-codes"></a>Auftragsfehlercodes

Weitere Informationen finden Sie unter [Fehlercodes](/rest/api/media/jobs/get#joberrorcode).

### <a name="get-a-streaming-locator"></a>Abrufen eines Streaminglocators

Nachdem die Codierung abgeschlossen ist, besteht der nächste Schritt darin, das Video im Ausgabeobjekt Clients für die Wiedergabe zur Verfügung zu stellen. Führen Sie hierzu diese zwei Schritte aus: Erstellen Sie zunächst einen [Streaminglocator](/rest/api/media/streaminglocators) und dann die Streaming-URLs, die Clients verwenden können.

Der Vorgang zum Erstellen eines **Streaminglocators** wird als „Veröffentlichen“ bezeichnet. Standardmäßig ist der **Streaminglocator** sofort nach Durchführung der API-Aufrufe gültig und bleibt es auch, bis er gelöscht wird (es sei denn, Sie konfigurieren die optionalen Start- und Endzeiten).

Beim Erstellen eines [StreamingLocator](/rest/api/media/streaminglocators) müssen Sie den gewünschten **StreamingPolicyName** angeben. In diesem Beispiel streamen Sie unverschlüsselte Inhalte, daher wird die vordefinierte Richtlinie für unverschlüsseltes Streaming (**PredefinedStreamingPolicy.ClearStreamingOnly**) verwendet.

> [!IMPORTANT]
> Wenn Sie eine benutzerdefinierte [Streamingrichtlinie](/rest/api/media/streamingpolicies) verwenden, sollten Sie eine begrenzte Sammlung solcher Richtlinien für Ihr Media Services-Konto erstellen und diese für Ihre Streaminglocators wiederverwenden, wenn dieselben Verschlüsselungsoptionen und Protokolle benötigt werden. Ihr Media Services-Konto weist ein Kontingent für die Anzahl von Streamingrichtlinieneinträgen auf. Sie sollten nicht für jeden Streaminglocator eine neue Streamingrichtlinie erstellen.

Im folgende Code wird vorausgesetzt, dass Sie die Funktion mit einem eindeutigen locatorName aufrufen.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

Das Beispiel in diesem Thema beschäftigt sich mit Streaming. Sie können den gleichen Aufruf jedoch auch verwenden, um einen Streaminglocator für die Übermittlung von Video über progressiven Download zu erstellen.

### <a name="get-streaming-urls"></a>Abrufen von Streaming-URLs

Nachdem der **Streaminglocator** erstellt wurde, können Sie die Streaming-URLs wie unter [GetStreamingURLs](/rest/api/media/streaminglocators) gezeigt abrufen. Zum Erstellen einer URL müssen Sie den Hostnamen des [Streamingendpunkts](/rest/api/media/streamingendpoints) und den Pfad des **Streaminglocators** miteinander verketten. In diesem Beispiel wird der *standardmäßige* **Streamingendpunkt** verwendet. Beim ersten Erstellen eines Media Services-Kontos weist dieser *standardmäßige* **Streamingendpunkt** den Status „Beendet“ auf. Sie müssen daher **Start** aufrufen.

> [!NOTE]
> In dieser Methode benötigen Sie den LocatorName, der beim Erstellen des **Streaminglocators** für das Ausgabeobjekt verwendet wurde.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

### <a name="clean-up-resources-in-your-media-services-account"></a>Bereinigen von Ressourcen in Ihrem Media Services-Konto

Im Allgemeinen sollten Sie alle Objekte bis auf diejenigen bereinigen, die Sie wiederverwenden möchten (in der Regel werden Transformationen wiederverwendet und StreamingLocators usw. dauerhaft gespeichert). Wenn Sie Ihr Konto nach dem Experimentieren bereinigen, sollten Sie alle Ressourcen löschen, die Sie nicht wiederverwenden möchten. Der folgende Code beispielsweise löscht den Auftrag, die erstellten Assets und die Inhaltsschlüsselrichtlinie:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>Ausführen der Beispiel-App

1. Drücken Sie STRG+F5, um die *EncodeAndStreamFiles*-App auszuführen.
2. Kopieren Sie eine der Streaming-URLs aus der Konsole.

Dieses Beispiel zeigt die URLs an, die zur Wiedergabe des Videos mit unterschiedlichen Protokollen verwendet werden können:

![Beispielausgabe mit URLs für Media Services-Streamingvideo](./media/stream-files-tutorial-with-api/output.png)

## <a name="test-the-streaming-url"></a>Testen der Streaming-URL

Um den Stream zu testen, wird in diesem Artikel Azure Media Player verwendet.

> [!NOTE]
> Wenn ein Player auf einer HTTPS-Website gehostet wird, stellen Sie sicher, die URL mit „https“ zu aktualisieren.

1. Öffnen Sie einen Webbrowser, und navigieren Sie zu [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Fügen Sie im Feld **URL:** eine der Streaming-URLs ein, die Sie beim Ausführen der App abgerufen haben.
3. Wählen Sie **Player aktualisieren**.

Azure Media Player kann zum Testen verwendet werden, sollte aber nicht in einer Produktionsumgebung zum Einsatz kommen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie keine Ressourcen in Ihrer Ressourcengruppe mehr benötigen, einschließlich der Media Services und Speicherkonten, die Sie für dieses Tutorial erstellt haben, löschen Sie die zuvor erstellte Ressourcengruppe.

Führen Sie den folgenden CLI-Befehl aus:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Multithreading

Die Azure Media Services v3 SDKs sind nicht threadsicher. Beim Entwickeln einer App mit Multithreading müssen Sie ein neues AzureMediaServicesClient-Objekt pro Thread generieren und verwenden.

## <a name="ask-questions-give-feedback-get-updates"></a>Fragen stellen, Feedback geben, Updates abrufen

Im Artikel [Azure Media Services-Community](media-services-community.md) finden Sie verschiedene Möglichkeiten, Fragen zu stellen, Feedback zu geben und Updates zu Media Services zu bekommen.

## <a name="next-steps"></a>Nächste Schritte

Da Sie nun wissen, wie Sie Ihr Video hochladen, codieren und streamen können, lesen Sie den folgenden Artikel: 

> [!div class="nextstepaction"]
> [Analysieren von Videos](analyze-videos-tutorial.md)
