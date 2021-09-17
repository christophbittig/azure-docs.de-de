---
title: 'Streamen von Videodateien mit Azure Media Services: .NET'
description: Führen Sie die in diesem Tutorial beschriebenen Schritte aus, um mithilfe von .NET ein neues Azure Media Services-Konto zu erstellen, eine Datei zu codieren und in Azure Media Player zu streamen.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
keywords: azure media services, streamen
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: mvc
ms.date: 07/23/2021
ms.author: inhenkel
ms.openlocfilehash: 079575148585c508224b6767b2eba61a16463fa0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732747"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---net"></a>Tutorial: Codieren einer Remotedatei anhand einer URL und Streamen des Videos – .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Dieses Tutorial zeigt, wie einfach es ist, Videos zu codieren und mit deren Streaming für eine Vielzahl von Browsern und Geräten mit Azure Media Services zu beginnen. Ein Eingabeinhalt kann über HTTPS-URLs, SAS-URLs oder Pfade zu Dateien in Azure Blob Storage angegeben werden.
Das Beispiel in diesem Thema codiert Inhalte, die Sie über eine HTTPS-URL zugänglich machen. Beachten Sie, dass AMS v3 derzeit keine segmentierte Transfercodierung über HTTPS-URLs unterstützt.

Am Ende des Tutorials sind Sie in der Lage, ein Video zu streamen.  

![Video abspielen](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

- Installieren Sie [Visual Studio Code für Windows/macOS/Linux](https://code.visualstudio.com/) oder [Visual Studio 2019 für Windows oder Mac](https://visualstudio.microsoft.com/).
- Installieren Sie das [.NET 5.0 SDK](https://dotnet.microsoft.com/download).
- [Erstellen Sie ein Media Services-Konto.](./account-create-how-to.md) Wichtig: Kopieren Sie die Details für den **API-Zugriff** im JSON-Format, oder speichern Sie die Werte, die zum Herstellen einer Verbindung mit dem Media Services-Konto benötigt werden, im *ENV*-Dateiformat (wie in diesem Beispiel).
- Führen Sie die Schritte unter [Abrufen von Anmeldeinformationen für den Zugriff auf die Media Services-API](./access-api-howto.md) aus. Sie müssen die *Anmeldeinformationen speichern*. Sie müssen die Anmeldeinformationen für den Zugriff auf die API in diesem Beispiel verwenden oder im *ENV*-Dateiformat eingeben.

## <a name="download-and-configure-the-sample"></a>Herunterladen und Konfigurieren des Beispiels

Klonen Sie ein GitHub-Repository auf Ihren Computer, das das .NET-Streamingbeispiel enthält, indem Sie den folgenden Befehl verwenden:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts.git
 ```

Das Beispiel befindet sich unter AMSV3Quickstarts im Ordner [EncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/tree/master/AMSV3Quickstarts/EncodeAndStreamFiles).

[!INCLUDE [appsettings or .env file](./includes/note-appsettings-or-env-file.md)]

Mit dem Beispiel werden die folgenden Aktionen durchgeführt:

1. Erstellen einer **Transformation** (nach vorheriger Überprüfung, ob die angegebene Transformation vorhanden ist) 
2. Erstellen eines **Ausgabemedienobjekts**, das als Ausgabe des **Codierungsauftrags** verwendet wird
3. Erstellen der auf einer HTTPS-URL basierenden **Auftragseingabe**
4. Übermitteln des **Codierungsauftrags** mit der zuvor erstellten Ein- und Ausgabe
5. Überprüfen des Auftragsstatus
6. Erstellen eines **Streaminglocators**
7. Erstellen von Streaming-URLs

Um eine Erläuterung dazu zu erhalten, was jede Funktion im Beispiel bewirkt, untersuchen Sie den Code, und sehen Sie sich die Kommentare in [dieser Quelldatei](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) an.

## <a name="run-the-sample-app"></a>Ausführen der Beispiel-App

Wenn Sie die App ausführen, werden URLs angezeigt, die zur Wiedergabe des Videos mit verschiedenen Protokollen verwendet werden können. 

1. Öffnen Sie AMSV3Quickstarts in VSCode.
2. Drücken Sie STRG+F5 zum Ausführen der *EncodeAndStreamFiles*-Anwendung mit .NET. Dies kann einige Minuten dauern.
3. Die App gibt drei URLs aus. Sie verwenden diese URLs, um den Stream im nächsten Schritt zu testen.

![Screenshot: Ausgabe der EncodeAndStreamFiles-App in Visual Studio mit drei Streaming-URLs für die Verwendung in Azure Media Player](./media/stream-files-tutorial-with-api/output.png)

Im [Quellcode](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) des Beispiels können Sie sehen, wie die URL erstellt wird. Um sie zu erstellen, müssen Sie den Hostnamen des Streamingendpunkts und den Streaminglocatorpfad miteinander verketten.  

## <a name="test-with-azure-media-player"></a>Testen mit Azure Media Player

Um den Stream zu testen, wird in diesem Artikel Azure Media Player verwendet. 

> [!NOTE]
> Wenn ein Player auf einer HTTPS-Website gehostet wird, stellen Sie sicher, die URL mit „https“ zu aktualisieren.

1. Öffnen Sie einen Webbrowser, und navigieren Sie zu [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Fügen Sie im Feld **URL:** einen der URL-Streamingwerte ein, die Sie bei der Ausführung der Anwendung abgerufen haben. 
 
     Sie können die URL im HLS-, Dash- oder Smooth-Format einfügen. Der Azure Media Player wechselt auf Ihrem Gerät automatisch zu einem geeigneten Streamingprotokoll für die Wiedergabe.
3. Drücken Sie **Player aktualisieren**. Dadurch sollte die Wiedergabe der Videodatei im Repository gestartet werden.

Azure Media Player kann zum Testen verwendet werden, sollte jedoch nicht in einer Produktionsumgebung zum Einsatz kommen. 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcen in der Ressourcengruppe einschließlich der in diesem Tutorial erstellten Media Services- und Speicherkonten nicht mehr benötigen, löschen Sie die Ressourcengruppe.

Führen Sie den folgenden CLI-Befehl aus:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="examine-the-code"></a>Untersuchen des Codes

Um eine Erläuterung dazu zu erhalten, was jede Funktion im Beispiel bewirkt, untersuchen Sie den Code, und sehen Sie sich die Kommentare in [dieser Quelldatei](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) an.

Das Tutorial zum [Hochladen, Codieren und Streamen von Dateien](stream-files-tutorial-with-api.md) bietet ein erweitertes Streamingbeispiel mit detaillierten Erläuterungen. 

### <a name="job-error-codes"></a>Auftragsfehlercodes

Weitere Informationen finden Sie unter [Fehlercodes](/rest/api/media/jobs/get#joberrorcode).

## <a name="multithreading"></a>Multithreading

Die Azure Media Services v3 SDKs sind nicht threadsicher. Beim Arbeiten mit einer Multithreadanwendung sollten Sie ein neues AzureMediaServicesClient-Objekt pro Thread generieren.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Hochladen, Codieren und Streamen von Dateien](stream-files-tutorial-with-api.md)
