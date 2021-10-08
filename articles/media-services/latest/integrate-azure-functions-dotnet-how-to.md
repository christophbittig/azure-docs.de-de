---
title: Entwickeln von Azure Functions mit Media Services v3
description: In diesem Artikel wird gezeigt, wie Sie in Visual Studio Code mit dem Entwickeln von Azure Functions mit Media Services v3 beginnen.
services: media-services
author: xpouyat
ms.service: media-services
ms.workload: media
ms.devlang: dotnet
ms.topic: article
ms.date: 06/09/2021
ms.author: xpouyat
ms.custom: devx-track-csharp
ms.openlocfilehash: 523a5dbfb503f47f15e44e7be1b61bf6cf05c471
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128661714"
---
# <a name="develop-azure-functions-with-media-services-v3"></a>Entwickeln von Azure Functions mit Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In diesem Artikel wird veranschaulicht, wie Sie mit der Erstellung von Azure Functions beginnen, für die Media Services verwendet werden. Die in diesem Artikel definierte Azure-Funktion codiert eine Videodatei mit Media Encoder Standard. Sobald der Codierungsauftrag erstellt wurde, gibt die Funktion den Auftragsnamen und den Namen des Ausgabeobjekts zurück. Informationen zu Azure Functions finden Sie unter [Übersicht](../../azure-functions/functions-overview.md) und anderen Themen im Abschnitt **Azure Functions**.

Wenn Sie vorhandene Azure Functions-Instanzen, die Azure Media Services verwenden, erkunden und bereitstellen möchten, sehen Sie sich [Media Services mit Azure Functions](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration). Dieses Repository enthält Beispiele, in denen Media Services verwendet wird, um Workflows zu veranschaulichen, bei denen es um das Erfassen von Inhalten direkt aus Blob Storage, die Codierung und Livestreamingvorgänge geht.

## <a name="prerequisites"></a>Voraussetzungen

- Bevor Sie Ihre erste Funktion erstellen können, müssen Sie über ein aktives Azure-Konto verfügen. Wenn Sie noch kein Azure-Konto haben, [erstellen Sie ein kostenloses Konto](https://azure.microsoft.com/free/).
- Es ist ratsam, wie [hier](account-create-how-to.md) beschrieben ein AMS-Konto zu erstellen, wenn Sie Azure Functions erstellen möchten, mit denen Aktionen in Ihrem AMS-Konto (Azure Media Services) durchgeführt werden oder auf von Media Services gesendete Ereignisse gelauscht wird.
- Installieren Sie [Visual Studio Code](https://code.visualstudio.com/) auf einer der [unterstützten Plattformen](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

In diesem Artikel wird erläutert, wie Sie eine C#-.NET 5-Funktion erstellen, die mit Azure Media Services kommuniziert. Informationen zum Erstellen einer Funktion in einer anderen Sprache finden Sie in diesem [Artikel](../../azure-functions/functions-develop-vs-code.md).

### <a name="run-local-requirements"></a>Anforderungen für die lokale Ausführung

Diese Voraussetzungen sind nur erforderlich, wenn Sie Ihre Funktionen lokal ausführen und debuggen. Sie sind nicht für das Erstellen oder Veröffentlichen von Projekten in Azure Functions erforderlich.

- [.NET Core 3.1 und .NET 5 SDKs](https://dotnet.microsoft.com/download/dotnet).

- [Azure Functions Core Tools](../../azure-functions/functions-run-local.md#install-the-azure-functions-core-tools), Version 3 oder höher. Das Core Tools-Paket wird für Sie automatisch heruntergeladen und installiert, wenn Sie das Projekt lokal starten. Die Core Tools beinhalten die gesamte Azure Functions-Runtime, daher können Download und Installation einige Zeit in Anspruch nehmen.

- [C#-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) für Visual Studio Code

## <a name="install-the-azure-functions-extension"></a>Installieren der Azure Functions-Erweiterung

Sie können die Azure Functions-Erweiterung verwenden, um Funktionen zu erstellen und zu testen und in Azure bereitzustellen.

1. Öffnen Sie in Visual Studio Code die Option **Erweiterungen**, und suchen Sie nach **Azure Functions**, oder wählen Sie in Visual Studio Code diesen Link aus: [`vscode:extension/ms-azuretools.vscode-azurefunctions`](vscode:extension/ms-azuretools.vscode-azurefunctions).

1. Wählen Sie **Installieren** aus, um die Erweiterung für Visual Studio Code zu installieren:

    ![Installieren der Erweiterung für Azure Functions](./Media/integrate-azure-functions-dotnet-how-to/vscode-install-extension.png)

1. Wählen Sie nach der Installation auf der Aktivitätsleiste das Azure-Symbol aus. Auf der Seitenleiste sollte ein Azure Functions-Bereich angezeigt werden.

    ![Azure Functions-Bereich auf der Seitenleiste](./Media/integrate-azure-functions-dotnet-how-to/azure-functions-window-vscode.png)

## <a name="create-an-azure-functions-project"></a>Erstellen eines Azure Functions-Projekts

Mithilfe der Functions-Erweiterung können Sie zugleich mit Ihrer ersten Funktion ein Funktions-App-Projekt erstellen. Die folgenden Schritte zeigen, wie Sie eine über HTTP ausgelöste Funktion in einem neuen Functions-Projekt erstellen. HTTP-Trigger ist die einfachste Funktionstriggervorlage und eignet sich daher gut zur Veranschaulichung.

1. Wählen Sie in **Azure Functions** das Symbol zum **Erstellen einer Funktion** aus:

    ![Erstellen einer Funktion](./Media/integrate-azure-functions-dotnet-how-to/create-function.png)

1. Wählen Sie den Ordner für Ihr Funktions-App-Projekt, und dann **wählen Sie C# für Ihr Funktions-App-Projekt** sowie **.NET 5 Isoliert** für die Runtime aus.

1. Wählen Sie Funktionsvorlage **HTTP-Trigger** aus.

    ![Auswählen der Vorlage für den HTTP-Trigger](./Media/integrate-azure-functions-dotnet-how-to/create-function-choose-template.png)

1. Geben Sie **HttpTriggerEncode** für den Funktionsnamen und dann EINGABE ein, übernehmen Sie **Company.Function** als Wert für den Namespace, und wählen Sie dann für die Zugriffsrechte **Function** aus. Für diese Autorisierungsebene müssen Sie beim Aufrufen des Funktionsendpunkts einen [Funktionsschlüssel](../../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) angeben.

    ![Auswählen von Funktionsautorisierung](./Media/integrate-azure-functions-dotnet-how-to/create-function-auth.png)

    Eine Funktion wird in Ihrer gewählten Sprache und in der Vorlage für eine über HTTP ausgelöste Funktion erstellt.

    ![Über HTTP ausgelöst Funktionsvorlage in Visual Studio Code](./Media/integrate-azure-functions-dotnet-how-to/new-function-full.png)

## <a name="install-media-services-and-other-extensions"></a>Installieren von Media Services und anderen Erweiterungen

Führen Sie den Befehl dotnet add package im Terminalfenster aus, um die Erweiterungspakete zu installieren, die Sie in Ihrem Projekt benötigen. Mit dem folgenden Befehl werden das Media Services-Paket und andere Erweiterungen installiert, die für das Beispiel erforderlich sind.

```bash
dotnet add package Azure.Storage.Blobs
dotnet add package Microsoft.Azure.Management.Media
dotnet add package Azure.Identity
```

## <a name="generated-project-files"></a>Generierte Projektdateien

Die Projektvorlage erstellt ein Projekt in Ihrer gewählten Sprache und installiert die erforderlichen Abhängigkeiten. Das neue Projekt weist diese Dateien auf:

* **host.json**: Ermöglicht das Konfigurieren des Functions-Hosts. Diese Einstellungen gelten, wenn Sie Funktionen lokal ausführen und sie in Azure ausführen. Weitere Informationen finden Sie in der [host.json-Referenz](./../../azure-functions/functions-host-json.md).

* **local.settings.json**: Behält Einstellungen beim lokalen Ausführen von Funktionen bei. Diese Einstellungen werden nur beim lokalen Ausführen von Funktionen verwendet.

    >[!IMPORTANT]
    >Da die Datei „local.settings.json“ Geheimnisse enthalten kann, müssen Sie sie aus der Quellcodeverwaltung Ihres Projekts ausschließen.

* **HttpTriggerEncode.cs**-Klassendatei, die die Funktion implementiert.

### <a name="httptriggerencodecs"></a>HttpTriggerEncode.cs

Dies ist der C#-Code für Ihre Funktion. Seine Rolle besteht darin, ein Media Services-Medienobjekt oder eine Quell-URL zu übernehmen und einen Codierungsauftrag in Media Services zu starten. Er verwendet eine Transformation, die erstellt wird, wenn sie nicht vorhanden ist. Falls sie erstellt wird, wird dazu die im Text der Eingabe angegebene Voreinstellung verwendet. 

>[!IMPORTANT]
>Ersetzen Sie den gesamten Inhalt der Datei HttpTriggerEncode.cs durch [`HttpTriggerEncode.cs` aus diesem Repository](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration/blob/main/Tutorial/HttpTriggerEncode.cs).

Nachdem Sie die Definition der Funktion abgeschlossen haben, wählen Sie **Speichern und ausführen** aus.

Der Quellcode für die **Run**-Methode der Funktion lautet:

[!code-csharp[Main](../../../media-services-v3-dotnet-core-functions-integration/Tutorial/HttpTriggerEncode.cs#Run)]

### <a name="localsettingsjson"></a>local.settings.json

Aktualisieren Sie die Datei mit dem folgenden Inhalt (und ersetzen Sie die Werte).

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "dotnet-isolated",
    "AadClientId": "00000000-0000-0000-0000-000000000000",
    "AadEndpoint": "https://login.microsoftonline.com",
    "AadSecret": "00000000-0000-0000-0000-000000000000",
    "AadTenantId": "00000000-0000-0000-0000-000000000000",
    "AccountName": "amsaccount",
    "ArmAadAudience": "https://management.core.windows.net/",
    "ArmEndpoint": "https://management.azure.com/",
    "ResourceGroup": "amsResourceGroup",
    "SubscriptionId": "00000000-0000-0000-0000-000000000000"
  }
}
```

## <a name="test-your-function"></a>Testen der Funktion

Wenn Sie die Funktion lokal in VS Code ausführen, sollte die Funktion wie folgt verfügbar gemacht werden: 

```url
http://localhost:7071/api/HttpTriggerEncode
```

Zum Testen können Sie Postman verwenden, um einen POST-Code für diese URL mithilfe eines JSON-Eingabetexts zu erstellen.

Beispiel für JSON-Eingabetext:

```json
{
    "inputUrl":"https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4",
    "transformName" : "TransformAS",
    "builtInPreset" :"AdaptiveStreaming"
 }
```

Die Funktion sollte 200 OK mit einem Ausgabetext zurückgeben, der die Namen des Auftrags und des Ausgabeobjekts enthält.

![Testen der Funktion mit Postman](./Media/integrate-azure-functions-dotnet-how-to/postman.png)

## <a name="next-steps"></a>Nächste Schritte

An diesem Punkt können Sie mit der Entwicklung von Funktionen beginnen, die Media Services-APIs aufrufen.

Weitere Informationen und ein vollständiges Beispiel für die Verwendung von Azure Functions mit Azure Media Services v3 finden Sie im [Media Services v3 Azure Functions-Beispiel](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration/tree/main/Functions).
