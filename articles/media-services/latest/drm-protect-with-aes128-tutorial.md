---
Titel: Verschlüsseln von Videos mit AES-128 : Azure Media Services description: Erfahren Sie, wie Sie Videos mit der Bit-Verschlüsselung AES-128 verschlüsseln, und wie Sie den Schlüsselübermittlungsdienst in Azure Media Services verwenden.
services: media-services documentationcenter: '' author: IngridAtMicrosoft manager: femila editor: ''

ms.service: media-services ms.workload: media ms.tgt_pltfrm: na ms.devlang: na ms.topic: tutorial ms.date: 05/25/2021 ms.author: inhenkel

---
# <a name="tutorial-encrypt-video-with-aes-128-and-use-the-key-delivery-service"></a>Tutorial: Verschlüsseln von Videos mit AES-128 und Verwenden des Schlüsselübermittlungsdienstes

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

> [!NOTE]
> Obwohl in diesem Tutorial die [.NET SDK](/dotnet/api/microsoft.azure.management.media.models.liveevent)-Beispiele verwendet werden, sind die allgemeinen Schritte für die [REST-API](/rest/api/media/liveevents), die [CLI](/cli/azure/ams/live-event) oder für andere unterstützte [SDKs](media-services-apis-overview.md#sdks) dieselben.

Mithilfe von Media Services können Sie HTTP Live Streaming (HLS), MPEG-DASH und Smooth Streaming, die mit dem AES verschlüsselt sind, mithilfe von 128-Bit-Verschlüsselungsschlüsseln bereitstellen. Media Services stellt außerdem den Schlüsselübermittlungsdienst bereit, der Verschlüsselungsschlüssel an autorisierte Benutzer übermittelt. Wenn Ihr Video von Media Services dynamisch verschlüsselt werden soll, ordnen Sie den Verschlüsselungsschlüssel einem Streaminglocator zu und konfigurieren außerdem die Richtlinie für den Inhaltsschlüssel. Wenn ein Stream von einem Player angefordert wird, verwendet Media Services den angegebenen Schlüssel, um Ihren Inhalt dynamisch mit AES-128 zu verschlüsseln. Um den Stream zu entschlüsseln, fordert der Player den Schlüssel vom Schlüsselübermittlungsdienst an. Um zu ermitteln, ob der Benutzer berechtigt ist, den Schlüssel zu erhalten, wertet der Dienst die Richtlinie für Inhaltsschlüssel aus, die Sie für den Schlüssel angegeben haben.

Sie können jedes Medienobjekt mit mehreren Verschlüsselungstypen (AES-128, PlayReady, Widevine, FairPlay) verschlüsseln. Informationen zu sinnvollen Kombinationen finden Sie unter [Streamingprotokolle und Verschlüsselungstypen](drm-content-protection-concept.md#streaming-protocols-and-encryption-types). Weitere Informationen finden Sie auch unter [Schützen mit DRM](drm-protect-with-drm-tutorial.md).

Die Ausgabe des Beispiels in diesem Artikel enthält eine URL zum Azure Media Player, eine Manifest-URL und das AES-Token, das für die Wiedergabe des Inhalts erforderlich ist. Im Beispiel wird der Ablauf des JSON Web Token (JWT) auf 1 Stunde festgelegt. Sie können einen Browser öffnen und die resultierende URL einfügen, um die Azure Media Player-Demoseite mit der URL und dem bereits ausgefüllten Token (im Format ```https://ampdemo.azureedge.net/?url= {dash Manifest URL} &aes=true&aestoken=Bearer%3D{ JWT Token here}```) zu starten.

Dieses Tutorial veranschaulicht folgende Vorgehensweisen:

> [!div class="checklist"]
> * Herunterladen des im Artikel beschriebenen Beispiels [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/main/AMSV3Tutorials/EncryptWithAES)
> * Starten der Verwendung von Media Services-APIs mit dem .NET SDK
> * Erstellen eines Ausgabemedienobjekts
> * Erstellen einer Codierungstransformation.
> * Übermitteln eines Auftrags
> * Warten auf den Abschluss des Auftrags
> * Erstellen einer Richtlinie für Inhaltsschlüssel
> * Konfigurieren der Richtlinie zur Verwendung von JWT-Tokeneinschränkungen
> * Erstellen eines Streaminglocators
> * Konfigurieren des Streaminglocators zur Verschlüsselung des Videos mit AES (ClearKey)
> * Abrufen eines Testtokens
> * Erstellen einer Streaming-URL
> * Bereinigen der Ressourcen

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Zum Abschließen dieses Lernprogramms müssen folgende Voraussetzungen erfüllt sein:

* Lesen Sie den Artikel [Übersicht über den Inhaltsschutz](drm-content-protection-concept.md).
* Installieren Sie Visual Studio Code oder Visual Studio.
* [Erstellen Sie ein Media Services-Konto.](./account-create-how-to.md)
* Rufen Sie die zur Verwendung von Media Services-APIs erforderlichen Anmeldeinformationen ab (siehe [Zugreifen auf APIs](./access-api-howto.md)).
* Legen Sie die entsprechenden Werte in der App-Konfigurationsdatei („appsettings.json“ oder ENV-Datei) fest.

## <a name="download-and-configure-the-sample"></a>Herunterladen und Konfigurieren des Beispiels

Klonen Sie ein GitHub-Repository, das das vollständige in diesem Artikel beschriebene .NET-Beispiel enthält, mit dem folgenden Befehl auf Ihrem Computer:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

Das Beispiel „Encrypt with AES-128“ befindet sich im Ordner [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/main/AMSV3Tutorials/EncryptWithAES).

[!INCLUDE [appsettings or .env file](./includes/note-appsettings-or-env-file.md)]

> [!NOTE]
> Mit dem Beispiel werden bei jeder Ausführung der App eindeutige Ressourcen erstellt. Normalerweise verwenden Sie vorhandene Ressourcen wie Transformationen und Richtlinien nochmal (wenn vorhandene Ressourcen über erforderliche Konfigurationen verfügen).

### <a name="start-using-media-services-apis-with-the-net-sdk"></a>Beginnen mit der Verwendung von Media Services-APIs mit dem .NET SDK

Um mit der Verwendung von Media Services-APIs in .NET zu beginnen, müssen Sie ein `AzureMediaServicesClient`-Objekt erstellen. Zum Erstellen des Objekts müssen Sie Anmeldeinformationen für den Client bereitstellen, damit dieser per Azure Active Directory eine Verbindung mit Azure herstellen kann. Eine weitere Option ist die interaktive Authentifizierung, die in `GetCredentialsInteractiveAuthAsync` implementiert ist.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/Common_Utils/Authentication.cs#CreateMediaServicesClientAsync)]

Im Code, den Sie am Anfang des Artikels geklont haben, wird mit der Funktion `GetCredentialsAsync` das Objekt `ServiceClientCredentials` erstellt. Dies erfolgt basierend auf den Anmeldeinformationen in der lokalen Konfigurationsdatei (*appsettings.json*) oder über die Datei *.env* mit den Umgebungsvariablen im Stammverzeichnis des Repositorys.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/Common_Utils/Authentication.cs#GetCredentialsAsync)]

Bei der interaktiven Authentifizierung wird durch die Funktion `GetCredentialsInteractiveAuthAsync` das Objekt `ServiceClientCredentials` erstellt, und zwar basierend auf einer interaktiven Authentifizierung und den Verbindungsparametern, die in der lokalen Konfigurationsdatei (*appsettings.json*) oder über die Umgebungsvariablendatei ( *.env*) im Stammverzeichnis des Repositorys angegeben sind. In diesem Fall sind AADCLIENTID und AADSECRET in der Konfigurations- oder Umgebungsvariablendatei nicht erforderlich.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/Common_Utils/Authentication.cs#GetCredentialsInteractiveAuthAsync)]

## <a name="create-an-output-asset"></a>Erstellen eines Ausgabemedienobjekts  

Das [Ausgabeobjekt](/rest/api/media/assets) speichert das Ergebnis Ihres Codierungsauftrags.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>Abrufen oder Erstellen einer Codierungstransformation

Beim Erstellen einer neuen [Transformations](/rest/api/media/transforms)instanz müssen Sie angeben, was als Ausgabe generiert werden soll. Der erforderliche Parameter ist ein **TransformOutput**-Objekt, wie im folgenden Code gezeigt. Jedes **TransformOutput**-Objekt enthält eine **Voreinstellung**. Die **Voreinstellung** beschreibt die schrittweisen Anweisungen von Video- und/oder Audioverarbeitungsvorgängen, die verwendet werden sollen, um das gewünschte **TransformOutput**-Objekt zu generieren. Das in diesem Artikel beschriebene Beispiel verwendet eine integrierte Voreinstellung namens **AdaptiveStreaming**. Die Voreinstellung codiert das Eingabevideo in eine automatisch generierte Bitratenkette (Paare aus Bitrate und Auflösung) auf Basis der Eingabeauflösung und -bitrate und generiert dann ISO MP4-Dateien mit H.264-Video und AAC-Audio entsprechend jedem Paar aus Bitrate und Auflösung.

Überprüfen Sie zunächst vor dem Erstellen einer neuen [Transformation](/rest/api/media/transforms) mit der **Get**-Methode, ob eine solche bereits vorhanden ist. Der folgende Code veranschaulicht dies. In Media Services v3 geben **Get**-Methoden für Entitäten **null** zurück, wenn die Entität nicht vorhanden ist (eine Überprüfung des Namens ohne Unterscheidung zwischen Groß-/Kleinschreibung ).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Auftrag übermitteln

Wie bereits erwähnt, ist das [Transformations](/rest/api/media/transforms)objekt die Anleitung und ein [Auftrag](/rest/api/media/jobs) die tatsächliche Anforderung an Media Services, diese **Transformation** auf ein bestimmtes Eingabevideo oder einen Audioinhalt anzuwenden. Der **Auftrag** gibt Informationen wie den Speicherort des Eingabevideos und den Speicherort für die Ausgabe an.

In diesem Tutorial wird die Auftragseingabe basierend auf einer Datei erstellt, die direkt aus einer [HTTPS-Quell-URL](job-input-from-http-how-to.md) erfasst wird.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Warten auf den Abschluss des Auftrags

Die Auftragsausführung dauert einige Zeit. Nach Abschluss des Auftrags möchten Sie eine Benachrichtigung erhalten. Das Codebeispiel unten zeigt, wie der Status des [Auftrags](/rest/api/media/jobs) vom Dienst abgerufen wird. Der Abruf gehört aufgrund potenzieller Latenzzeiten nicht zu den empfohlenen Best Practices für Produktionsanwendungen. Der Abruf kann gedrosselt werden, wenn er für ein Konto im Übermaß verwendet wird. Entwickler sollten stattdessen Event Grid verwenden. Weitere Informationen finden Sie unter [Routing von Ereignissen an einen benutzerdefinierten Webendpunkt](monitoring/job-state-events-cli-how-to.md).

Der **Auftrag** durchläuft in der Regel die folgenden Zustände: **Geplant**, **In Warteschlange**, **Wird verarbeitet**, **Abgeschlossen** (Endzustand). Wenn für den Auftrag ein Fehler aufgetreten ist, wird der Zustand **Fehler** angezeigt. Wird der Auftrag gerade abgebrochen, befindet er sich im Zustand **Vorgang wird abgebrochen**. Nach Abschluss des Vorgangs lautet der Status **Abgebrochen**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Erstellen einer Richtlinie für Inhaltsschlüssel

Ein Inhaltsschlüssel ermöglicht den sicheren Zugriff auf Ihre Medienobjekte. Sie müssen eine **Richtlinie für den Inhaltsschlüssel** erstellen, mit der konfiguriert wird, wie der Inhaltsschlüssel an Endclients übermittelt wird. Der Inhaltsschlüssel ist dem **Streaminglocator** zugeordnet. Media Services stellt außerdem den Schlüsselübermittlungsdienst bereit, der Verschlüsselungsschlüssel an autorisierte Benutzer übermittelt.

Wenn ein Datenstrom von einem Player angefordert wird, verwendet Media Services den angegebenen Schlüssel, um Ihren Inhalt dynamisch zu verschlüsseln (in diesem Fall mit der AES-Verschlüsselung). Um den Stream zu entschlüsseln, fordert der Player den Schlüssel vom Schlüsselübermittlungsdienst an. Um zu ermitteln, ob der Benutzer berechtigt ist, den Schlüssel zu erhalten, wertet der Dienst die Richtlinie für Inhaltsschlüssel aus, die Sie für den Schlüssel angegeben haben.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Erstellen eines Streaminglocators

Nachdem die Codierung abgeschlossen ist und die Richtlinie für den Inhaltsschlüssel festgelegt wurde, besteht der nächste Schritt darin, das Video im Ausgabeobjekt Clients für die Wiedergabe zur Verfügung zu stellen. Sie stellen das Video in zwei Schritten zur Verfügung:

1. Erstellen eines [Streaminglocators](/rest/api/media/streaminglocators)
2. Erstellen der Streaming-URLs, die von Clients verwendet werden können

Der Vorgang des Erstellens eines **StreamingLocator** wird als „Veröffentlichen“ bezeichnet. Standardmäßig ist der **Streaminglocator** sofort nach den API-Aufrufen gültig. Wenn Sie keine optionale Start- und Endzeit eingestellt haben, dann bleibt der Streaminglocator bis zu seiner Löschung bestehen.

Beim Erstellen eines [Streaminglocators](/rest/api/media/streaminglocators) müssen Sie den gewünschten **StreamingPolicyName** angeben. In diesem Tutorial verwenden wir eine der PredefinedStreamingPolicies, mit der Azure Media Services angewiesen wird, wie der Inhalt für das Streaming zu veröffentlichen ist. In diesem Beispiel wird die AES-Umschlagverschlüsselung angewandt (wird auch als ClearKey-Verschlüsselung bezeichnet, da keine DRM-Lizenz, sondern der Schlüssel über HTTPS an den Wiedergabeclient übermittelt wird).

> [!IMPORTANT]
> Wenn Sie eine benutzerdefinierte [Streamingrichtlinie](/rest/api/media/streamingpolicies) verwenden, sollten Sie eine begrenzte Menge solcher Richtlinien für Ihr Media Services-Konto erstellen und diese für Ihre Streaminglocators wiederverwenden, wenn dieselben Verschlüsselungsoptionen und -protokolle benötigt werden. Ihr Media Services-Konto weist ein Kontingent für die Anzahl von StreamingPolicy-Einträgen auf. Sie sollten nicht für jeden Streaminglocator eine neue StreamingPolicy erstellen.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Abrufen eines Testtokens

In diesem Tutorial wird für die Richtlinie für den Inhaltsschlüssel eine Tokeneinschränkung angegeben. Eine durch Token eingeschränkte Richtlinie gilt nur zusammen mit einem Token, das von einem Sicherheitstokendienst (Security Token Service, STS) ausgestellt wurde. Media Services unterstützt Token im [JWT](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_3)-Format. Dieses Format wird auch im Beispiel konfiguriert.

In **ContentKeyPolicy** wird „ContentKeyIdentifierClaim“ verwendet, d. h., das an den Schlüsselbereitstellungsdienst übergebene Token muss den Bezeichner des Inhaltsschlüssels enthalten. Im Beispiel geben wir beim Erstellen von Streaminglocator keinen Inhaltsschlüssel an, er wird im System zufällig erstellt. Die ContentKeyId muss abgerufen werden, die in den Anspruch ContentKeyIdentifierClaim eingefügt werden muss, um das Testtoken zu generieren.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>Erstellen einer DASH-Streaming-URL

Nachdem der [Streaminglocator](/rest/api/media/streaminglocators) erstellt wurde, können Sie die Streaming-URLs abrufen. Um eine URL zu erstellen, müssen Sie den [StreamingEndpoint](/rest/api/media/streamingendpoints)-Hostnamen und den **Streaminglocator**-Pfad miteinander verketten. In diesem Beispiel wird der *standardmäßige* **Streamingendpunkt** verwendet. Beim ersten Erstellen eines Media Services-Kontos weist dieser *standardmäßige* **Streamingendpunkt** den Status „Beendet“ auf. Sie müssen daher **Start** aufrufen.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>Bereinigen von Ressourcen in Ihrem Media Services-Konto

Im Allgemeinen sollten Sie alles mit Ausnahme der Objekte bereinigen, die Sie wiederverwenden möchten. (In der Regel werden Transformationen, Streaminglocators usw. wiederverwendet.) Wenn Sie Ihr Konto nach dem Experimentieren bereinigen, sollten Sie alle Ressourcen löschen, die Sie nicht wiederverwenden möchten. Der folgende Code beispielsweise löscht den Auftrag, die erstellten Assets und die Inhaltsschlüsselrichtlinie:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie keine Ressourcen in Ihrer Ressourcengruppe mehr benötigen, einschließlich der Media Services und Speicherkonten, die Sie für dieses Tutorial erstellt haben, löschen Sie die zuvor erstellte Ressourcengruppe.

Führen Sie den folgenden CLI-Befehl aus:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="ask-questions-give-feedback-get-updates"></a>Fragen stellen, Feedback geben, Updates abrufen

Im Artikel [Azure Media Services-Community](media-services-community.md) finden Sie verschiedene Möglichkeiten, Fragen zu stellen, Feedback zu geben und Updates zu Media Services zu bekommen.

## <a name="additional-notes"></a>Zusätzliche Hinweise

* Widevine ist ein von Google Inc. bereitgestellter Dienst, der den Vertragsbedingungen und der Datenschutzrichtlinie von Google, Inc. unterliegt.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Schutz mit DRM](drm-protect-with-drm-tutorial.md)
