---
title: Analysieren von Videos mit Media Services v3
description: Hier erfahren Sie, wie Sie Videos mithilfe von Azure Media Services analysieren.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: tutorial
ms.date: 07/26/2021
ms.author: inhenkel
ms.openlocfilehash: 1427e97ceb3cd592a105bc06174e2627e3bc53bd
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129659011"
---
# <a name="tutorial-analyze-videos-with-media-services-v3"></a>Tutorial: Analysieren von Videos mit Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In diesem Tutorial wird gezeigt, wie Videos mit Azure Media Services analysiert werden. Es gibt viele Szenarien, in denen Sie umfassende Einblicke in aufgezeichnete Videos oder Audioinhalte erlangen möchten. Um beispielsweise eine höhere Kundenzufriedenheit zu erreichen, können Unternehmen eine Spracherkennung ausführen, um Kundensupportaufzeichnungen in einen durchsuchbaren Katalog mit Indizes und Dashboards umzuwandeln.

Dieses Tutorial veranschaulicht folgende Vorgehensweisen:

> [!div class="checklist"]
> * Herunterladen der in diesem Thema beschriebenen Beispiel-App
> * Untersuchen des Codes für die Analyse des angegebenen Videos
> * Führen Sie die App aus.
> * Untersuchen der Ausgabe
> * Bereinigen der Ressourcen

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="compliance-privacy-and-security"></a>Compliance, Datenschutz und Sicherheit
 
Wichtiger Hinweis: Wir möchten Sie daran erinnern, dass Sie bei der Verwendung des Azure Video Analyzer for Media (früher Video Indexer) alle geltenden Gesetze einhalten müssen. Sie dürfen weder den Azure Video Analyzer for Media noch einen anderen Azure-Dienst auf eine Weise verwenden, die die Rechte Dritter verletzt. Bevor Sie Videos, einschließlich biometrischer Daten, zur Verarbeitung und Speicherung in den Azure Video Analyzer for Media-Dienst hochladen, müssen Sie über alle erforderlichen Rechte, einschließlich aller entsprechenden Zustimmungen der Personen im Video, verfügen. Weitere Informationen zu Compliance, Datenschutz und Sicherheit in Zusammenhang mit dem Azure Video Analyzer for Media finden Sie in den [Azure Cognitive Services-Bestimmungen](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/). Informationen zu den Datenschutzauflagen und zur Behandlung Ihrer Daten durch Microsoft finden Sie in den [Datenschutzbestimmungen](https://privacy.microsoft.com/PrivacyStatement), in den [Bestimmungen für Onlinedienste](https://www.microsoft.com/licensing/product-licensing/products) (Online Services Terms, OST) und im [Nachtrag zur Datenverarbeitung](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) (Data Processing Addendum, DPA). Weitere Datenschutzinformationen (unter anderem zu Datenaufbewahrung und Datenlöschung/-vernichtung) finden Sie in den Bestimmungen für Onlinedienste und [hier](../../azure-video-analyzer/video-analyzer-for-media-docs/faq.yml). Wenn Sie den Azure Video Analyzer for Media verwenden, akzeptieren Sie die Cognitive Services-Bedingungen, die OST, die DPA und die Datenschutzbestimmungen.

## <a name="prerequisites"></a>Voraussetzungen

- Installieren Sie [Visual Studio Code für Windows/macOS/Linux](https://code.visualstudio.com/) oder [Visual Studio 2019 für Windows oder Mac](https://visualstudio.microsoft.com/).
- Installieren Sie das [.NET 5.0 SDK](https://dotnet.microsoft.com/download).
- [Erstellen Sie ein Media Services-Konto.](./account-create-how-to.md) Wichtig: Kopieren Sie die Details für den **API-Zugriff** im JSON-Format, oder speichern Sie die Werte, die zum Herstellen einer Verbindung mit dem Media Services-Konto benötigt werden, im *ENV*-Dateiformat (wie in diesem Beispiel).
- Führen Sie die Schritte unter [Abrufen von Anmeldeinformationen für den Zugriff auf die Media Services-API](./access-api-howto.md) aus, und speichern Sie die Anmeldeinformationen. Sie müssen die Anmeldeinformationen für den Zugriff auf die API in diesem Beispiel verwenden oder im *ENV*-Dateiformat eingeben.

## <a name="download-and-configure-the-sample"></a>Herunterladen und Konfigurieren des Beispiels

Klonen Sie ein GitHub-Repository auf Ihren Computer, das das .NET-Beispiel enthält, indem Sie den folgenden Befehl verwenden:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

Das Beispiel befindet sich im Ordner [AnalyzeVideos](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/main/AMSV3Tutorials/AnalyzeVideos).

[!INCLUDE [appsettings or .env file](./includes/note-appsettings-or-env-file.md)]

## <a name="examine-the-code-that-analyzes-the-specified-video"></a>Untersuchen des Codes für die Analyse des angegebenen Videos

In diesem Abschnitt werden die Funktionen untersucht, die in der Datei „[Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/main/AMSV3Tutorials/AnalyzeVideos/Program.cs)“ des *AnalyzeVideos*-Projekts definiert werden.

Das Beispiel führt die folgenden Aktionen aus:

1. Erstellen einer **Transformation** und eines **Auftrags** für die Videoanalyse.
2. Erstellen eines **Eingangsmedienobjekts** und Hochladen des Videos in dieses Objekt. Das Medienobjekt wird als Eingabe für den Auftrag verwendet.
3. Erstellen eines Ausgabemedienobjekts zum Speichern der Auftragsausgabe
4. Übermitteln des Auftrags
5. Überprüfen des Auftragsstatus
6. Herunterladen der durch die Auftragsausführung entstandenen Dateien

### <a name="start-using-media-services-apis-with-the-net-sdk"></a>Beginnen mit der Verwendung von Media Services-APIs mit dem .NET SDK

Um mit der Verwendung von Media Services-APIs in .NET zu beginnen, müssen Sie ein `AzureMediaServicesClient`-Objekt erstellen. Zum Erstellen des Objekts müssen Sie Anmeldeinformationen für den Client bereitstellen, damit dieser per Azure Active Directory eine Verbindung mit Azure herstellen kann. Eine weitere Option ist die interaktive Authentifizierung, die in `GetCredentialsInteractiveAuthAsync` implementiert ist.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/Common_Utils/Authentication.cs#CreateMediaServicesClientAsync)]

Im Code, den Sie am Anfang des Artikels geklont haben, wird mit der Funktion `GetCredentialsAsync` das Objekt `ServiceClientCredentials` erstellt. Dies erfolgt basierend auf den Anmeldeinformationen in der lokalen Konfigurationsdatei (*appsettings.json*) oder über die Datei *.env* mit den Umgebungsvariablen im Stammverzeichnis des Repositorys.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/Common_Utils/Authentication.cs#GetCredentialsAsync)]

Bei der interaktiven Authentifizierung wird durch die Funktion `GetCredentialsInteractiveAuthAsync` das Objekt `ServiceClientCredentials` erstellt, und zwar basierend auf einer interaktiven Authentifizierung und den Verbindungsparametern, die in der lokalen Konfigurationsdatei (*appsettings.json*) oder über die Umgebungsvariablendatei ( *.env*) im Stammverzeichnis des Repositorys angegeben sind. In diesem Fall sind „AADCLIENTID“ und „AADSECRET“ in der Konfigurations- oder Umgebungsvariablendatei nicht erforderlich.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/Common_Utils/Authentication.cs#GetCredentialsInteractiveAuthAsync)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Erstellen eines Eingabeobjekts und Hochladen einer lokalen Datei in dieses 

Die **CreateInputAsset**-Funktion erstellt ein neues [Eingabeobjekt](/rest/api/media/assets) und lädt die angegebene lokale Videodatei in dieses hoch. Dieses Objekt wird als Eingabe für Ihren Codierungsauftrag verwendet. In Media Services v3 kann die Eingabe in einen Auftrag ein Objekt sein, oder es kann sich um Inhalte handeln, die Sie für Ihr Media Services-Konto über HTTPS-URLs zur Verfügung stellen. Um zu erfahren, wie die Codierung aus einer HTTPS-URL erfolgt, lesen Sie [diesen](job-input-from-http-how-to.md) Artikel.  

In Media Services v3 verwenden Sie Azure Storage-APIs zum Hochladen von Dateien. Im folgenden Codeausschnitt wird gezeigt, wie dies funktioniert.

Die folgende Funktion führt diese Aktionen aus:

* Erstellen eines Medienobjekts
* Abrufen einer nicht schreibgeschützten [SAS-URL](../../storage/common/storage-sas-overview.md) für den [Container](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-a-blob-to-a-container) des Medienobjekts im Speicher

    Beachten Sie Folgendes: Wenn Sie die Funktion [ListContainerSas](/rest/api/media/assets/listcontainersas) des Medienobjekts zum Abrufen von SAS-URLs verwenden, gibt die Funktion mehrere SAS-URLs zurück, da für jedes Speicherkonto zwei Speicherkontoschlüssel vorhanden sind. Ein Speicherkonto verfügt über zwei Schlüssel, um eine nahtlose Rotation von Speicherkontoschlüsseln zu ermöglichen. (So kann beispielsweise ein Schlüssel geändert werden, während der andere in Gebrauch ist. Anschließend kann dann der neue Schlüssel verwendet werden, während der andere rotiert wird.) Die erste SAS-URL steht für Speicherschlüssel1 und die zweite für Speicherschlüssel2.
* Hochladen der Datei in den Container im Speicher mithilfe der SAS-URL

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-the-job"></a>Erstellen eines Ausgabemedienobjekts zum Speichern des Auftragsergebnisses

Das [Ausgabeobjekt](/rest/api/media/assets) speichert das Ergebnis Ihres Auftrags. Das Projekt definiert die **DownloadResults**-Funktion, die die Ergebnisse aus diesem Ausgabeobjekt in den Ordner „output“ herunterlädt, damit Sie sehen können, was Sie abgerufen haben.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-analyzes-videos"></a>Erstellen einer Transformation und eines Auftrags, der Videos analysiert

Bei der Codierung oder Verarbeitung von Inhalten in Media Services ist es gängige Praxis, Codierungseinstellungen als Anleitung einzurichten. Anschließend übermitteln Sie einen **Auftrag**, um diese Anleitung auf ein Video anzuwenden. Durch die Übermittlung von neuen Aufträgen für jedes neue Video wenden Sie diese Anleitung auf alle Videos in Ihrer Bibliothek an. Eine solche Anleitung wird in Media Services als **Transformation** bezeichnet. Weitere Informationen finden Sie unter [Transformationen und Aufträge](./transform-jobs-concept.md). Das Beispiel in diesem Tutorial definiert eine Anleitung, die das angegebene Video analysiert.

#### <a name="transform"></a>Transformieren

Beim Erstellen einer neuen [Transformations](/rest/api/media/transforms)instanz müssen Sie angeben, was als Ausgabe generiert werden soll. **TransformOutput** ist ein erforderlicher Parameter. Jedes **TransformOutput**-Objekt enthält eine **Voreinstellung**. Die **Voreinstellung** beschreibt schrittweise Anweisungen von Video- und/oder Audioverarbeitungsvorgängen, die verwendet werden sollen, um das gewünschte **TransformOutput**-Objekt zu generieren. In diesem Beispiel wird die **VideoAnalyzerPreset**-Voreinstellung verwendet und die Sprache („en-US“) an ihren Konstruktor übergeben (`new VideoAnalyzerPreset("en-US")`). Diese Voreinstellung ermöglicht Ihnen, mehrere Audio- und Videoinformationen aus einer Videodatei zu extrahieren. Sie können die **AudioAnalyzerPreset**-Voreinstellung verwenden, wenn Sie mehrere Audioinformationen aus einem Video extrahieren müssen.

Überprüfen Sie beim Erstellen einer **Transformation** zunächst mit der **Get**-Methode, ob eine solche bereits vorhanden ist. Der folgende Code zeigt dies. In Media Services v3 geben **Get**-Methoden für Entitäten **null** zurück, wenn die Entität nicht vorhanden ist (eine Überprüfung des Namens ohne Unterscheidung zwischen Groß-/Kleinschreibung ).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Auftrag

Wie bereits erwähnt, ist das [Transformations](/rest/api/media/transforms)objekt die Anleitung und ein [Auftrag](/rest/api/media/jobs) die tatsächliche Anforderung an Media Services, diese **Transformation** auf ein bestimmtes Eingabevideo oder einen Audioinhalt anzuwenden. Der **Auftrag** gibt Informationen wie den Speicherort des Eingabevideos und den Speicherort für die Ausgabe an. Sie können den Speicherort Ihres Videos wie folgt angeben: Über HTTPS-URLs, SAS-URLs oder Medienobjekte in Ihrem Media Service-Konto.

In diesem Beispiel ist die Auftragseingabe ein lokales Video.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Warten auf den Abschluss des Auftrags

Die Auftragsausführung dauert einige Zeit. Nach Abschluss des Auftrags möchten Sie eine Benachrichtigung erhalten. Es bestehen verschiedene Optionen, sich beim Abschluss des [Auftrags](/rest/api/media/jobs) benachrichtigen zu lassen. Die einfachste Möglichkeit (die hier gezeigt wird) ist die Verwendung von Abrufvorgängen.

Der Abruf gehört aufgrund potenzieller Latenzzeiten nicht zu den empfohlenen Best Practices für Produktionsanwendungen. Der Abruf kann gedrosselt werden, wenn er für ein Konto im Übermaß verwendet wird. Entwickler sollten stattdessen Event Grid verwenden.

Event Grid ist für hohe Verfügbarkeit, konsistente Leistung und dynamische Skalierung ausgelegt. Mit Event Grid können Ihre Apps Ereignisse aus praktisch allen Azure-Diensten sowie aus benutzerdefinierten Quellen überwachen und darauf reagieren. Eine einfache, HTTP-basierte, reaktive Ereignisverarbeitung hilft Ihnen mit intelligentem Filtern und Routing von Ereignissen dabei, effiziente Lösungen zu erstellen. Weitere Informationen finden Sie unter [Routing von Ereignissen an einen benutzerdefinierten Webendpunkt](monitoring/job-state-events-cli-how-to.md).

Der **Auftrag** durchläuft in der Regel die folgenden Zustände: **Geplant**, **In Warteschlange**, **Wird verarbeitet**, **Abgeschlossen** (Endzustand). Wenn für den Auftrag ein Fehler aufgetreten ist, wird der Zustand **Fehler** angezeigt. Wird der Auftrag gerade abgebrochen, befindet er sich im Zustand **Vorgang wird abgebrochen**. Nach Abschluss des Vorgangs lautet der Status **Abgebrochen**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#WaitForJobToFinish)]

### <a name="job-error-codes"></a>Auftragsfehlercodes

Weitere Informationen finden Sie unter [Fehlercodes](/rest/api/media/jobs/get#joberrorcode).

### <a name="download-the-result-of-the-job"></a>Herunterladen des Ergebnisses des Auftrags

Die folgende Funktion lädt die Ergebnisse aus dem [Ausgabeobjekt](/rest/api/media/assets) in den Ordner „output“ herunter, damit Sie die Ergebnisse des Auftrags untersuchen können.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#DownloadResults)]

### <a name="clean-up-resources-in-your-media-services-account"></a>Bereinigen von Ressourcen in Ihrem Media Services-Konto

[!INCLUDE [clean-up-warning](includes/clean-up-warning.md)]

Im Allgemeinen sollten Sie alles mit Ausnahme der Objekte bereinigen, die Sie wiederverwenden möchten. (In der Regel werden Transformationen wiederverwendet und Streaminglocators gespeichert.) Wenn Sie Ihr Konto nach dem Experimentieren bereinigen, sollten Sie alle Ressourcen löschen, die Sie nicht wiederverwenden möchten. Mit dem folgenden Code wird beispielsweise das Auftrags- und Ausgabemedienobjekt gelöscht:

### <a name="delete-resources-with-code"></a>Löschen von Ressourcen mit Code

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CleanUp)]

Sie können auch die Befehlszeilenschnittstelle verwenden.

[!INCLUDE [clean-up-resources-cli](includes/clean-up-resources-cli.md)]

## <a name="run-the-sample-app"></a>Ausführen der Beispiel-App

Drücken Sie STRG+F5, um die *AnalyzeVideos*-App auszuführen.

Wenn das Programm ausgeführt wird, generiert der Auftrag Miniaturansichten für jedes Gesicht, das im Video gefunden wird. Außerdem wird die Datei „insights.json“ generiert.

## <a name="examine-the-output"></a>Untersuchen der Ausgabe

Die Ausgabedatei der Analyse von Videos wird als „insights.json“ bezeichnet. Diese Datei enthält Informationen zu Ihrem Video. Sie finden eine Beschreibung der Elemente in der JSON-Datei im Artikel [Medienintelligenz](./analyze-video-audio-files-concept.md).

## <a name="multithreading"></a>Multithreading

> [!WARNING]
> Die Azure Media Services v3 SDKs sind nicht threadsicher. Beim Arbeiten mit einer Multithreadanwendung sollten Sie ein neues AzureMediaServicesClient-Objekt pro Thread generieren.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Hochladen, Codieren und Streamen von Dateien](stream-files-tutorial-with-api.md)
