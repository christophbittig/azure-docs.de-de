---
title: Hochladen und Indizieren von Videos mit Azure Video Analyzer for Media (früher Video Indexer)
description: In diesem Thema wird veranschaulicht, wie Sie APIs zum Hochladen und Indizieren Ihrer Videos mit Azure Video Analyzer for Media (früher Video Indexer) verwenden.
ms.service: azure-video-analyzer
ms.topic: article
ms.date: 05/12/2021
ms.openlocfilehash: 4020050cdcc1f9910ab14117b45bb007f5915dca
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128658771"
---
# <a name="upload-and-index-your-videos"></a>Hochladen und Indizieren Ihrer Videos  

Nachdem Ihr Video hochgeladen wurde, kann Azure Video Analyzer for Media (früher Video Indexer) das Video, wie im Artikel erläutert, codieren (optional). Beim Anlegen eines Video Analyzer for Media-Kontos können Sie ein kostenloses Testkonto (für das Sie eine bestimmte Anzahl von kostenlosen Indizierungsminuten erhalten) oder eine kostenpflichtige Option (bei der Sie nicht aufgrund des Kontingents begrenzt sind) wählen. Bei der kostenlosen Testversion stellt der Video Analyzer bis zu 600 Minuten an kostenloser Indizierungszeit für Websitebenutzer und bis zu 2400 Minuten an kostenloser Indizierungszeit für API-Benutzer bereit. Bei der kostenpflichtigen Option erstellen Sie ein Azure Video Analyzer for Media-Konto, [das mit Ihrem Azure-Abonnement und einem Azure Media Services-Konto verbunden ist](connect-to-azure.md). Sie bezahlen pro Minute Indizierungszeit. Weitere Informationen finden Sie unter [Media Services – Preise](https://azure.microsoft.com/pricing/details/media-services/).

Beim Hochladen von Videos mit der Video Analyzer for Media-API haben Sie die folgenden Optionen: 

* Hochladen des Videos von einer URL (bevorzugt)
* Senden der Videodatei als Bytearray im Hauptteil der Anforderung
* Verwenden Sie ein vorhandenes Azure Media Services-Medienobjekt, indem Sie die [Medienobjekt-ID](../../media-services/latest/assets-concept.md) angeben (wird nur in kostenpflichtigen Konten unterstützt).

In diesem Artikel wird gezeigt, wie Sie Ihre Videos mit den folgenden Optionen hochladen und indizieren:

* Mit der Video Analyzer for Media-Website 
* Mit den Video Analyzer for Media-APIs 

## <a name="supported-file-formats-for-video-analyzer-for-media"></a>Unterstützte Dateiformate für Azure Video Analyzer for Media

Im Artikel [Eingabecontainer/Dateiformate](../../media-services/latest/encode-media-encoder-standard-formats-reference.md) finden Sie eine Liste der Dateiformate, die Sie mit dem Azure Video Analyzer for Media verwenden können.

## <a name="video-files-storage"></a>Videodateispeicher

- Bei einem kostenpflichtigen Video Analyzer for Media-Konto erstellen Sie ein Azure Video Analyzer for Media-Konto, das mit Ihrem Azure-Abonnement und einem Azure Media Services-Konto verbunden ist. Weitere Informationen finden Sie im Artikel zum [Erstellen eines Video Analyzer for Media-Kontos mit Azure-Verbindung](connect-to-azure.md).
- Videodateien werden von Azure Media Services in Azure Storage gespeichert. Es gibt keine Zeitbeschränkung.
- Sie können Ihre Video- und Audiodateien sowie Metadaten und Erkenntnisse, die von Video Analyzer for Media daraus extrahiert wurden, jederzeit löschen. Nach dem Löschen einer Datei aus Video Analyzer for Media sind die Datei sowie die zugehörigen Metadaten und Erkenntnisse endgültig aus Video Analyzer for Media entfernt. Wenn Sie jedoch eine eigene Sicherungslösung im Azure-Speicher implementiert haben, verbleibt die Datei in Ihrem Azure-Speicher.
- Die Persistenz eines Videos ist identisch, unabhängig davon, ob der Upload auf der Video Analyzer for Media-Website oder über die Upload-API erfolgt.
   
## <a name="upload-and-index-a-video-using-the-video-analyzer-for-media-website"></a>Hochladen und Indizieren eines Videos über die Azure Video Analyzer for Media-Website

> [!NOTE]
> Der Name des Videos darf nicht mehr als 80 Zeichen umfassen.

1. Melden Sie sich auf der [Azure Video Analyzer for Media](https://www.videoindexer.ai/) Website an.
1. Klicken Sie auf die Schaltfläche bzw. den Link **Hochladen**, um ein Video hochzuladen.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/video-indexer-upload.png" alt-text="Upload":::
1. Nachdem Ihr Video hochgeladen wurde, beginnt der Azure Video Analyzer for Media mit dem Indizieren und Analysieren des Videos.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/progress.png" alt-text="Uploadstatus":::
1. Sobald der Azure Video Analyzer for Media die Analyse abgeschlossen hat, erhalten Sie eine E-Mail mit einem Link zu Ihrem Video und einer kurzen Beschreibung dazu, was in Ihrem Video gefunden wurde. Beispiel: Personen, Themen, OCR-Daten.

## <a name="upload-and-index-with-api"></a>Hochladen und Indizieren mithilfe der API

Verwenden Sie die API zum [Hochladen eines Videos](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video), um Ihre Videos über eine URL hochzuladen und zu indizieren. Das nachstehende Codebeispiel enthält auch auskommentierten Code, der das Hochladen des Bytearrays zeigt. 

### <a name="configurations-and-params"></a>Konfigurationen und Parameter

In diesem Abschnitt werden einige optionale Parameter und ihre Anwendung beschrieben. Die aktuellsten Parameterinformationen finden Sie unter der API zum [Hochladen von Videos](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video).

#### <a name="externalid"></a>externalID 

Dieser Parameter ermöglicht Ihnen das Angeben einer ID, die dem Video zugeordnet wird. Die ID kann auf die externe Systemintegration für die Verwaltung der Videoinhalte (Video Content Management, VCM) angewendet werden. Die Videos, die im Video Analyzer for Media-Portal enthalten sind, können anhand der angegebenen externen ID durchsucht werden.

#### <a name="callbackurl"></a>callbackUrl

[!INCLUDE [callback url](./includes/callback-url.md)]

##### <a name="other-considerations"></a>Weitere Überlegungen

- Video Analyzer for Media gibt vorhandene Parameter aus der ursprünglichen URL zurück.
- Die angegebene URL muss codiert werden.

#### <a name="indexingpreset"></a>indexingPreset

Verwenden Sie diesen Parameter, um das KI-Paket zu definieren, das Sie auf Ihre Audio- oder Videodatei anwenden möchten. Dieser Parameter wird verwendet, um den Indizierungsprozess zu konfigurieren. Sie können die folgenden Werte angeben:

- `AudioOnly`: Indizieren und Extrahieren von Erkenntnissen ausschließlich für Audiodaten (Videodaten werden ignoriert)
- `VideoOnly`: Indizieren und Extrahieren von Erkenntnissen ausschließlich für Videodaten (Audiodaten werden ignoriert)
- `Default`: Indizieren und Extrahieren von Erkenntnissen für Audio- und Videodaten
- `DefaultWithNoiseReduction`: Indizieren und Extrahieren von Erkenntnissen aus Audio- und Videodaten mit Anwendung von Algorithmen für die Rauschunterdrückung auf den Audiodatenstrom

    Der Wert `DefaultWithNoiseReduction` ist nun der Standardvoreinstellung (veraltet) zugeordnet.
- `BasicAudio`: Indizieren und Extrahieren von Erkenntnissen ausschließlich für Audiodaten (Videodaten werden ignoriert) und nur unter Verwendung grundlegender Audiofunktionen (Transkription, Übersetzung, Formatieren von Beschriftungstext und Untertiteln).
- `AdvancedAudio`: Indizieren und Extrahieren von Erkenntnissen ausschließlich für Audiodaten (Videodaten werden ignoriert) unter Verwendung erweiterter Audiofunktionen (Audioereigniserkennung) zusätzlich zur standardmäßigen Audioanalyse
- `AdvancedVideo`: Indizieren und Extrahieren von Erkenntnissen ausschließlich für Videodaten (Audiodaten werden ignoriert) unter Verwendung erweiterter Videofunktionen (Verfolgung von beobachteten Personen) zusätzlich zur standardmäßigen Videoanalyse
- `AdvancedVideoAndAudio`: Indizieren und Extrahieren von Erkenntnissen mit erweiterter Audio- und erweiterter Videoanalyse 

> [!NOTE]
> Erweiterte Voreinstellungen (oben aufgeführt) umfassen Modelle, die sich in der öffentlichen Vorschau befinden. Sobald diese Modelle allgemeine verfügbar werden, kann dies Auswirkungen auf den Preis haben. 

Video Indexer deckt bis zu zwei Audiospuren ab. Wenn sich in der Datei mehr Audiospuren befinden, werden sie als eine Spur behandelt.<br/>
Wenn Sie die Spuren separat indizieren möchten, müssen Sie die relevante Audiodatei extrahieren und als `AudioOnly` indizieren.

Der Preis richtet sich nach der gewählten Indizierungsoption. Weitere Informationen finden Sie unter [Media Services – Preise](https://azure.microsoft.com/pricing/details/media-services/).

#### <a name="priority"></a>priority

Videos werden von Video Analyzer for Media gemäß ihrer Priorität indiziert. Geben Sie mithilfe des Parameters **priority** die Indexpriorität an. Die folgenden Werte sind gültig: **Niedrig**, **Normal** (Standard) und **Hoch**.

Der Parameter **priority** wird nur in kostenpflichtigen Konten unterstützt.

#### <a name="streamingpreset"></a>streamingPreset

Nachdem Ihr Video hochgeladen wurde, kann das Video optional von Video Analyzer for Media codiert werden. Anschließend wird der Vorgang mit dem Indizieren und Analysieren des Videos fortgesetzt. Nachdem Video Analyzer for Media die Analyse abgeschlossen hat, erhalten Sie eine Benachrichtigung mit der Video-ID.  

Bei Verwendung der [Upload video](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video)- oder [Re-Index Video](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video)-API lautet einer der optionalen Parameter `streamingPreset`. Wenn Sie `streamingPreset` auf `Default`, `SingleBitrate` oder `AdaptiveBitrate` festlegen, wird der Codierungsprozess ausgelöst. Wenn die Indizierung und Codierung von Aufträgen abgeschlossen ist, wird das Video veröffentlicht, damit Sie Ihr Video auch streamen können. Der Streamingendpunkt, von dem aus Sie das Video streamen möchten, muss sich im Status **Wird ausgeführt** befinden.

Für SingleBitrate werden die Kosten für den Standardencoder pro Ausgabe berechnet. Wenn das Videoformat größer oder gleich 720 ist, codiert Video Analyzer for Media dieses als 1280 ×­ 720. Andernfalls wird die Einstellung 640 × 468 verwendet.
Die Standardeinstellung ist die [inhaltsbezogene Codierung](../../media-services/latest/encode-content-aware-concept.md).

Zum Ausführen der Indizierung und Codierung von Aufträgen sind für das [Azure Media Services-Konto, das mit Ihrem Video Analyzer for Media-Konto verbunden ist](connect-to-azure.md), reservierte Einheiten (Reserved Units, RUs) erforderlich. Weitere Informationen finden Sie unter [Übersicht über das Skalieren der Medienverarbeitung](../../media-services/previous/media-services-scale-media-processing-overview.md). Da es sich hierbei um rechenintensive Aufträge handelt, wird dringend die Verwendung des Einheitentyps S3 empfohlen. Die Anzahl von RUs definiert die maximale Anzahl von Aufträgen, die parallel ausgeführt werden können. Die Baseline-Empfehlung lautet: zehn RUs vom Typ S3. 

Wenn Sie Ihr Video nicht codieren, sondern nur indizieren möchten, können Sie `streamingPreset`auf `NoStreaming` festlegen.

#### <a name="videourl"></a>videoUrl

Eine URL der zu indizierenden Video-/Audiodatei. Die URL muss auf eine Mediendatei zeigen (HTML-Seiten werden nicht unterstützt). Die Datei kann durch ein Zugriffstoken als Teil des URI geschützt werden, und der Endpunkt für die Datei muss mit TLS 1.2 oder höher gesichert werden. Die URL muss codiert sein. 

Wenn `videoUrl` nicht angegeben ist, erwartet Video Analyzer for Media, dass Sie die Datei als „multipart/form“ im Hauptteil übergeben.

### <a name="code-sample"></a>Codebeispiel

Mit dem folgenden C#-Codeausschnitt wird die Nutzung aller Video Analyzer for Media-APIs zusammen veranschaulicht.

**Anweisungen zum Ausführen des folgenden Codebeispiels**

Nachdem Sie diesen Code auf Ihre Entwicklungsplattform kopiert haben, müssen Sie zwei Parameter angeben: API Management-Authentifizierungsschlüssel und Video-URL.

* API-Schlüssel: Der API-Schlüssel ist Ihr persönlicher API Management-Abonnementschlüssel, mit dem Sie ein Zugriffstoken abrufen können, um Vorgänge für Ihr Video Analyzer for Media-Konto auszuführen. 

    Um Ihren API-Schlüssel abzurufen, gehen Sie wie folgt vor:

    * Navigieren Sie zu https://api-portal.videoindexer.ai/.
    * Anmeldename
    * Navigieren Sie zu **Produkte** -> **Autorisierung** -> **Autorisierungsabonnement**.
    * Kopieren Sie den **Primärschlüssel**.
* Video-URL: Eine URL der zu indizierenden Video-/Audiodatei. Die URL muss auf eine Mediendatei zeigen (HTML-Seiten werden nicht unterstützt). Die Datei kann durch ein Zugriffstoken als Teil des URI geschützt werden, und der Endpunkt für die Datei muss mit TLS 1.2 oder höher gesichert werden. Die URL muss codiert sein.

Das Ergebnis der erfolgreichen Ausführung des Codebeispiels umfasst eine Insight-Widget-URL und eine Player-Widget-URL, mit der Sie die Einblicke bzw. das hochgeladene Video untersuchen können. 


```csharp
public async Task Sample()
{
    var apiUrl = "https://api.videoindexer.ai";
    var apiKey = "..."; // replace with API key taken from https://aka.ms/viapi

    System.Net.ServicePointManager.SecurityProtocol =
        System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

    // create the http client
    var handler = new HttpClientHandler();
    handler.AllowAutoRedirect = false;
    var client = new HttpClient(handler);
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);

    // obtain account information and access token
    string queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"generateAccessTokens", "true"},
            {"allowEdit", "true"},
        });
    HttpResponseMessage result = await client.GetAsync($"{apiUrl}/auth/trial/Accounts?{queryParams}");
    var json = await result.Content.ReadAsStringAsync();
    var accounts = JsonConvert.DeserializeObject<AccountContractSlim[]>(json);
    
    // take the relevant account, here we simply take the first, 
    // you can also get the account via accounts.First(account => account.Id == <GUID>);
    var accountInfo = accounts.First();

    // we will use the access token from here on, no need for the apim key
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // upload a video
    var content = new MultipartFormDataContent();
    Console.WriteLine("Uploading...");
    // get the video from URL
    var videoUrl = "VIDEO_URL"; // replace with the video URL

    // as an alternative to specifying video URL, you can upload a file.
    // remove the videoUrl parameter from the query params below and add the following lines:
    //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
    //byte[] buffer =new byte[video.Length];
    //video.Read(buffer, 0, buffer.Length);
    //content.Add(new ByteArrayContent(buffer));

    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"name", "video_name"},
            {"description", "video_description"},
            {"privacy", "private"},
            {"partition", "partition"},
            {"videoUrl", videoUrl},
        });
    var uploadRequestResult = await client.PostAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos?{queryParams}", content);
    var uploadResult = await uploadRequestResult.Content.ReadAsStringAsync();

    // get the video ID from the upload result
    string videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
    Console.WriteLine("Uploaded");
    Console.WriteLine("Video ID:");
    Console.WriteLine(videoId);

    // wait for the video index to finish
    while (true)
    {
        await Task.Delay(10000);

        queryParams = CreateQueryString(
            new Dictionary<string, string>()
            {
                {"accessToken", accountInfo.AccessToken},
                {"language", "English"},
            });

        var videoGetIndexRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/Index?{queryParams}");
        var videoGetIndexResult = await videoGetIndexRequestResult.Content.ReadAsStringAsync();

        string processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

        Console.WriteLine("");
        Console.WriteLine("State:");
        Console.WriteLine(processingState);

        // job is finished
        if (processingState != "Uploaded" && processingState != "Processing")
        {
            Console.WriteLine("");
            Console.WriteLine("Full JSON:");
            Console.WriteLine(videoGetIndexResult);
            break;
        }
    }

    // search for the video
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"id", videoId},
        });

    var searchRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/Search?{queryParams}");
    var searchResult = await searchRequestResult.Content.ReadAsStringAsync();
    Console.WriteLine("");
    Console.WriteLine("Search:");
    Console.WriteLine(searchResult);

    // Generate video access token (used for get widget calls)
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
    var videoTokenRequestResult = await client.GetAsync($"{apiUrl}/auth/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/AccessToken?allowEdit=true");
    var videoAccessToken = (await videoTokenRequestResult.Content.ReadAsStringAsync()).Replace("\"", "");
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // get insights widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
            {"widgetType", "Keywords"},
            {"allowEdit", "true"},
        });
    var insightsWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/InsightsWidget?{queryParams}");
    var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
    Console.WriteLine("Insights Widget url:");
    Console.WriteLine(insightsWidgetLink);

    // get player widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
        });
    var playerWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/PlayerWidget?{queryParams}");
    var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
     Console.WriteLine("");
     Console.WriteLine("Player Widget url:");
     Console.WriteLine(playerWidgetLink);
     Console.WriteLine("\nPress Enter to exit...");
     String line = Console.ReadLine();
     if (line == "enter")
     {
         System.Environment.Exit(0);
     }

}

private string CreateQueryString(IDictionary<string, string> parameters)
{
    var queryParameters = HttpUtility.ParseQueryString(string.Empty);
    foreach (var parameter in parameters)
    {
        queryParameters[parameter.Key] = parameter.Value;
    }

    return queryParameters.ToString();
}

public class AccountContractSlim
{
    public Guid Id { get; set; }
    public string Name { get; set; }
    public string Location { get; set; }
    public string AccountType { get; set; }
    public string Url { get; set; }
    public string AccessToken { get; set; }
}
```

### <a name="common-errors"></a>Häufige Fehler

Die in der folgenden Tabelle aufgeführten Statuscodes können über den Uploadvorgang zurückgegeben werden.

|Statuscode|ErrorType (im Antworttext)|BESCHREIBUNG|
|---|---|---|
|409|VIDEO_INDEXING_IN_PROGRESS|Dasselbe Video wird unter dem angegebenen Konto bereits verarbeitet.|
|400|VIDEO_ALREADY_FAILED|Dasselbe Video konnte unter dem angegebenen Konto vor weniger als zwei Stunden nicht verarbeitet werden. API-Clients sollten mindestens zwei Stunden warten, bevor ein Video erneut hochgeladen wird.|
|429||Für Testkonten sind 5 Uploads pro Minute zulässig. Für kostenpflichtige Konten sind 50 Uploads pro Minute zulässig.|

## <a name="uploading-considerations-and-limitations"></a>Überlegungen und Einschränkungen zum Hochladen
 
- Der Name des Videos darf nicht mehr als 80 Zeichen umfassen.
- Wenn Sie das Video über eine URL hochladen (bevorzugt), muss der Endpunkt mit TLS 1.2 (oder höher) gesichert werden.
- Die Uploadgröße ist mit der URL-Option auf 30 GB begrenzt.
- Die Länge der Anforderungs-URL ist auf 6.144 Zeichen beschränkt, wobei die Länge der Abfragezeichenfolge-URL auf 4.096 Zeichen beschränkt ist.
- Die Uploadgröße mit der Bytearray-Option ist auf 2 GB begrenzt.
- Bei der Bytearray-Option tritt ein Timeout nach 30 Minuten auf.
- Die im Parameter `videoURL` angegebene URL muss codiert sein.
- Bei der Indizierung von Media Services-Medienobjekten gilt die gleiche Begrenzung wie bei der Indizierung aus einer URL.
- Video Analyzer for Media weist eine maximale Dauer von 4 Stunden für eine einzelne Datei auf.
- Die URL muss zugänglich sein (z. B. eine öffentliche URL). 

    Wenn es sich um eine private URL handelt, muss das Zugriffstoken in der Anforderung bereitgestellt werden.
- Die URL muss auf eine gültige Mediendatei und nicht auf eine Webseite verweisen, z. B. als Link auf die Seite `www.youtube.com`.
- In einem kostenpflichtigen Konto können Sie bis zu 50 Filme pro Minute und in einem Testkonto bis zu 5 Filme pro Minute hochladen.

> [!Tip]
> Es wird empfohlen, .NET Framework-Version 4.6.2 oder höher zu verwenden, da für frühere .NET Frameworks nicht standardmäßig TLS 1.2 genutzt wird.
>
> Falls Sie frühere .NET Frameworks verwenden müssen, sollten Sie Ihrem Code eine Zeile hinzufügen, bevor Sie den REST-API-Aufruf durchführen:  <br/> System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls12;

## <a name="firewall"></a>Firewall

Weitere Informationen finden Sie unter [Speicherkonto befindet sich hinter einer Firewall](faq.yml#can-a-storage-account-connected-to-the-media-services-account-be-behind-a-firewall).

## <a name="next-steps"></a>Nächste Schritte

[Untersuchen der von der API erstellten Azure Video Analyzer for Media-Ausgabe](video-indexer-output-json-v2.md)
