---
title: 'Schnellstart: Multivariate .NET-Clientbibliothek für die Anomalieerkennung'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/29/2021
ms.author: mbullwin
ms.openlocfilehash: ea010cd348973757c5f5cc0bb705bc52f0cd9e7a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121801410"
---
Hier finden Sie Informationen zu den ersten Schritten mit der multivariaten Clientbibliothek für die Anomalieerkennung für C#. Führen Sie die hier angegebenen Schritte aus, um das Paket zu installieren und mit der Verwendung der bereitgestellten Algorithmen zu beginnen. Die neuen APIs für die multivariate Anomalieerkennung ermöglichen Entwicklern die einfache Integration fortschrittlicher KI zur Erkennung von Anomalien in Metrikgruppen ganz ohne Machine Learning-Kenntnisse oder gekennzeichnete Daten. Abhängigkeiten und Interkorrelationen zwischen verschiedenen Signalen werden automatisch als Schlüsselfaktoren gewertet. Dadurch können Sie Ihre komplexen Systeme leichter proaktiv vor Fehlern schützen.

Die multivariate Clientbibliothek für die Anomalieerkennung für C# kann für Folgendes verwendet werden:

* Erkennen von Anomalien auf Systemebene in einer Gruppe von Zeitreihen
* Betrachten sämtlicher Signale, um ein Problem zu erkennen, wenn die Betrachtung einzelner Zeitreihen nicht ausreicht
* Prädikative Wartung teurer physischer Ressourcen mit dutzenden bis hunderten verschiedenen Arten von Sensoren zur Messung diverser Aspekte der Systemintegrität

[Referenzdokumentation der Bibliothek](/dotnet/api/azure.ai.anomalydetector) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/anomalydetector/Azure.AI.AnomalyDetector) | [Paket (NuGet)](https://www.nuget.org/packages/Azure.AI.AnomalyDetector/3.0.0-preview.3)

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* Aktuelle Version von [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)
* Sobald Sie über Ihr Azure-Abonnement verfügen, erstellen Sie über <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Erstellen einer Anomalieerkennungsressource"  target="_blank"> eine Anomalieerkennungsressource </a> im Azure-Portal, um Ihren Schlüssel und Endpunkt zu erhalten. Warten Sie die Bereitstellung ab, und wählen Sie dann die Schaltfläche **Zu Ressource wechseln** aus.
    * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der Anomalieerkennungs-API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.

## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-net-core-application"></a>Erstellen einer neuen .NET Core-Anwendung

Verwenden Sie in einem Konsolenfenster (z. B. cmd, PowerShell oder Bash) den Befehl `dotnet new` zum Erstellen einer neuen Konsolen-App mit dem Namen `anomaly-detector-quickstart-multivariate`. Dieser Befehl erstellt ein einfaches „Hallo Welt“-Projekt mit einer einzigen C#-Quelldatei: *Program.cs*.

```dotnetcli
dotnet new console -n anomaly-detector-quickstart-multivariate
```

Wechseln Sie zum Ordner der neu erstellten App. Sie können die Anwendung mit folgendem Befehl erstellen:

```dotnetcli
dotnet build
```

Die Buildausgabe sollte keine Warnungen oder Fehler enthalten.

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Installieren der Clientbibliothek

Installieren Sie im Anwendungsverzeichnis mit dem folgenden Befehl die Anomalieerkennungs-Clientbibliothek für .NET:

```dotnetcli
dotnet add package Azure.AI.AnomalyDetector --version 3.0.0-preview.3
```

Öffnen Sie über das Projektverzeichnis die Datei *program.cs*, und fügen Sie mithilfe von `directives` Folgendes hinzu:

```csharp
using System;
using System.Collections.Generic;
using System.Drawing.Text;
using System.IO;
using System.Linq;
using System.Linq.Expressions;
using System.Net.NetworkInformation;
using System.Reflection;
using System.Text;
using System.Threading.Tasks;
using Azure.AI.AnomalyDetector.Models;
using Azure.Core.TestFramework;
using Microsoft.Identity.Client;
using NUnit.Framework;
```

Erstellen Sie in der Methode `main()` der Anwendung Variablen für den Azure-Endpunkt Ihrer Ressource sowie für Ihren API-Schlüssel und für eine benutzerdefinierte Datenquelle.

> [!NOTE]
> Sie haben stets die Möglichkeit, einen von zwei Schlüsseln zu verwenden. Dies ermöglicht eine sichere Schlüsselrotation. Verwenden Sie für diesen Schnellstart den ersten Schlüssel. 

```csharp
string endpoint = "YOUR_API_KEY";
string apiKey =  "YOUR_ENDPOINT";
string datasource = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
```

Wenn Sie die multivariaten APIs für die Anomalieerkennung verwenden möchten, müssen Sie zunächst Ihre eigenen Modelle trainieren. Bei den Trainingsdaten handelt es sich um mehrere Zeitreihen, die die folgenden Anforderungen erfüllen:

Bei den Zeitreihen muss es sich jeweils um eine CSV-Datei mit genau zwei Spalten in der Headerzeile handeln: „timestamp“ und „value“ (in Kleinbuchstaben). Die „timestamp“-Werte müssen ISO 8601 entsprechen. Für „value“ können ganze Zahlen oder Dezimalzahlen mit einer beliebigen Anzahl von Dezimalstellen verwendet werden. Beispiel:

|timestamp | value|
|-------|-------|
|2019-04-01T00:00:00Z| 5|
|2019-04-01T00:01:00Z| 3.6|
|2019-04-01T00:02:00Z| 4|
|`...`| `...` |

Jede CSV-Datei muss nach einer anderen Variablen benannt werden, die für das Modelltraining verwendet wird. Beispiel: „temperature.csv“ und „humidity.csv“. Alle CSV-Dateien müssen ohne Unterordner in einer ZIP-Datei verpackt werden. Die ZIP-Datei kann einen beliebigen Namen haben. Die ZIP-Datei muss in Azure Blob Storage hochgeladen werden. Nach dem Generieren der Blob-SAS-URL (Shared Access Signature) für die ZIP-Datei kann sie für das Training verwendet werden. Informationen zum Generieren von SAS-URLs aus Azure Blob Storage finden Sie in diesem Dokument.

## <a name="code-examples"></a>Codebeispiele

Diese Codeausschnitte veranschaulichen, wie folgende Vorgänge mit der multivariaten Clientbibliothek für die Anomalieerkennung für .NET durchgeführt werden:

* [Authentifizieren des Clients](#authenticate-the-client)
* [Modelltraining](#train-the-model)
* [Erkennen von Anomalien](#detect-anomalies)
* [Exportieren des Modells](#export-model)
* [Löschen des Modells](#delete-model)

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Instanziieren Sie einen Anomalieerkennungsclient mit Ihrem Endpunkt und Schlüssel.

```csharp
var endpointUri = new Uri(endpoint);
var credential = new AzureKeyCredential(apiKey)

AnomalyDetectorClient client = new AnomalyDetectorClient(endpointUri, credential);
```

## <a name="train-the-model"></a>Trainieren des Modells

Erstellen Sie wie im Anschluss beschrieben eine neue private asynchrone Aufgabe zur Behandlung des Modelltrainings. Sie verwenden `TrainMultivariateModel`, um das Modell zu trainieren, und `GetMultivariateModelAysnc`, um zu überprüfen, wann das Training abgeschlossen ist.

```csharp
private async Task<Guid?> trainAsync(AnomalyDetectorClient client, string datasource, DateTimeOffset start_time, DateTimeOffset end_time)
{
    try
    {
        Console.WriteLine("Training new model...");

        int model_number = await getModelNumberAsync(client, false).ConfigureAwait(false);
        Console.WriteLine(String.Format("{0} available models before training.", model_number));

        ModelInfo data_feed = new ModelInfo(datasource, start_time, end_time);
        Response response_header = client.TrainMultivariateModel(data_feed);
        response_header.Headers.TryGetValue("Location", out string trained_model_id_path);
        Guid trained_model_id = Guid.Parse(trained_model_id_path.Split('/').LastOrDefault());
        Console.WriteLine(trained_model_id);

        // Wait until the model is ready. It usually takes several minutes
        Response<Model> get_response = await client.GetMultivariateModelAsync(trained_model_id).ConfigureAwait(false);
        while (get_response.Value.ModelInfo.Status != ModelStatus.Ready & get_response.Value.ModelInfo.Status != ModelStatus.Failed)
        {
            System.Threading.Thread.Sleep(10000);
            get_response = await client.GetMultivariateModelAsync(trained_model_id).ConfigureAwait(false);
            Console.WriteLine(String.Format("model_id: {0}, createdTime: {1}, lastUpdateTime: {2}, status: {3}.", get_response.Value.ModelId, get_response.Value.CreatedTime, get_response.Value.LastUpdatedTime, get_response.Value.ModelInfo.Status));
        }

        if (get_response.Value.ModelInfo.Status != ModelStatus.Ready)
        {
            Console.WriteLine(String.Format("Trainig failed."));
            IReadOnlyList<ErrorResponse> errors = get_response.Value.ModelInfo.Errors;
            foreach (ErrorResponse error in errors)
            {
                Console.WriteLine(String.Format("Error code: {0}.", error.Code));
                Console.WriteLine(String.Format("Error message: {0}.", error.Message));
            }
            throw new Exception("Training failed.");
        }

        model_number = await getModelNumberAsync(client).ConfigureAwait(false);
        Console.WriteLine(String.Format("{0} available models after training.", model_number));
        return trained_model_id;
    }
    catch (Exception e)
    {
        Console.WriteLine(String.Format("Train error. {0}", e.Message));
        throw new Exception(e.Message);
    }
}
```

## <a name="detect-anomalies"></a>Erkennen von Anomalien

Um Anomalien mit dem neu trainierten Modell zu erkennen, erstellen Sie eine private asynchrone Aufgabe (`private async Task`) namens `detectAsync`. Sie erstellen eine neue Erkennungsanforderung (`DetectionRequest`) und übergeben sie als Parameter an `DetectAnomalyAsync`.

```csharp
private async Task<DetectionResult> detectAsync(AnomalyDetectorClient client, string datasource, Guid model_id,DateTimeOffset start_time, DateTimeOffset end_time)
{
    try
    {
        Console.WriteLine("Start detect...");
        Response<Model> get_response = await client.GetMultivariateModelAsync(model_id).ConfigureAwait(false);

        DetectionRequest detectionRequest = new DetectionRequest(datasource, start_time, end_time);
        Response result_response = await client.DetectAnomalyAsync(model_id, detectionRequest).ConfigureAwait(false);
        var ok = result_response.Headers.TryGetValue("Location", out string result_id_path);
        Guid result_id = Guid.Parse(result_id_path.Split('/').LastOrDefault());
        // get detection result
        Response<DetectionResult> result = await client.GetDetectionResultAsync(result_id).ConfigureAwait(false);
        while (result.Value.Summary.Status != DetectionStatus.Ready & result.Value.Summary.Status != DetectionStatus.Failed)
        {
            System.Threading.Thread.Sleep(2000);
            result = await client.GetDetectionResultAsync(result_id).ConfigureAwait(false);
        }

        if (result.Value.Summary.Status != DetectionStatus.Ready)
        {
            Console.WriteLine(String.Format("Inference failed."));
            IReadOnlyList<ErrorResponse> errors = result.Value.Summary.Errors;
            foreach (ErrorResponse error in errors)
            {
                Console.WriteLine(String.Format("Error code: {0}.", error.Code));
                Console.WriteLine(String.Format("Error message: {0}.", error.Message));
            }
            return null;
        }

        return result.Value;
    }
    catch (Exception e)
    {
        Console.WriteLine(String.Format("Detection error. {0}", e.Message));
        throw new Exception(e.Message);
    }
}
```

## <a name="export-model"></a>Exportieren des Modells

> [!NOTE]
> Der Exportbefehl dient dazu, multivariate Modelle der Anomalieerkennung in einer containerisierten Umgebung ausführen zu können. Dies wird derzeit nicht für multivariate Modelle unterstützt, aber in Zukunft unterstützt werden.

Erstellen Sie zum Exportieren des zuvor trainierten Modells eine private asynchrone Aufgabe (`private async Task`) namens `exportAysnc`. Sie verwenden `ExportModelAsync` und übergeben die Modell-ID des Modells, das Sie exportieren möchten.

```csharp
private async Task exportAsync(AnomalyDetectorClient client, Guid model_id, string model_path = "model.zip")
{
    try
    {
        Stream model = await client.ExportModelAsync(model_id).ConfigureAwait(false);
        if (model != null)
        {
            var fileStream = File.Create(model_path);
            model.Seek(0, SeekOrigin.Begin);
            model.CopyTo(fileStream);
            fileStream.Close();
        }
    }
    catch (Exception e)
    {
        Console.WriteLine(String.Format("Export error. {0}", e.Message));
        throw new Exception(e.Message);
    }
}
```

## <a name="delete-model"></a>Löschen des Modells

Verwenden Sie zum Löschen eines zuvor erstellten Modells `DeleteMultivariateModelAsync`, und übergeben Sie die Modell-ID des zu löschenden Modells. Zum Abrufen einer Modell-ID können Sie `getModelNumberAsync` verwenden:

```csharp
private async Task deleteAsync(AnomalyDetectorClient client, Guid model_id)
{
    await client.DeleteMultivariateModelAsync(model_id).ConfigureAwait(false);
    int model_number = await getModelNumberAsync(client).ConfigureAwait(false);
    Console.WriteLine(String.Format("{0} available models after deletion.", model_number));
}
private async Task<int> getModelNumberAsync(AnomalyDetectorClient client, bool delete = false)
{
    int count = 0;
    AsyncPageable<ModelSnapshot> model_list = client.ListMultivariateModelAsync(0, 10000);
    await foreach (ModelSnapshot x in model_list)
    {
        count += 1;
        Console.WriteLine(String.Format("model_id: {0}, createdTime: {1}, lastUpdateTime: {2}.", x.ModelId, x.CreatedTime, x.LastUpdatedTime));
        if (delete & count < 4)
        {
            await client.DeleteMultivariateModelAsync(x.ModelId).ConfigureAwait(false);
        }
    }
    return count;
}
```

## <a name="main-method"></a>Main-Methode

Nachdem Sie nun über alle Komponententeile verfügen, müssen Sie Ihrer main-Methode zusätzlichen Code hinzufügen, um Ihre neu erstellten Aufgaben aufzurufen.

```csharp

{
    //read endpoint and apiKey
     string endpoint = "YOUR_API_KEY";
    string apiKey =  "YOUR_ENDPOINT";
    string datasource = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
    Console.WriteLine(endpoint);
    var endpointUri = new Uri(endpoint);
    var credential = new AzureKeyCredential(apiKey);

    //create client
    AnomalyDetectorClient client = new AnomalyDetectorClient(endpointUri, credential);

    // train
    TimeSpan offset = new TimeSpan(0);
    DateTimeOffset start_time = new DateTimeOffset(2021, 1, 1, 0, 0, 0, offset);
    DateTimeOffset end_time = new DateTimeOffset(2021, 1, 2, 12, 0, 0, offset);
    Guid? model_id_raw = null;
    try
    {
        model_id_raw = await trainAsync(client, datasource, start_time, end_time).ConfigureAwait(false);
        Console.WriteLine(model_id_raw);
        Guid model_id = model_id_raw.GetValueOrDefault();

        // detect
        start_time = end_time;
        end_time = new DateTimeOffset(2021, 1, 3, 0, 0, 0, offset);
        DetectionResult result = await detectAsync(client, datasource, model_id, start_time, end_time).ConfigureAwait(false);
        if (result != null)
        {
            Console.WriteLine(String.Format("Result ID: {0}", result.ResultId));
            Console.WriteLine(String.Format("Result summary: {0}", result.Summary));
            Console.WriteLine(String.Format("Result length: {0}", result.Results.Count));
        }

        // export model
        await exportAsync(client, model_id).ConfigureAwait(false);

        // delete
        await deleteAsync(client, model_id).ConfigureAwait(false);
    }
    catch (Exception e)
    {
        String msg = String.Format("Multivariate error. {0}", e.Message);
        if (model_id_raw != null)
        {
            await deleteAsync(client, model_id_raw.GetValueOrDefault()).ConfigureAwait(false);
        }
        Console.WriteLine(msg);
        throw new Exception(msg);
    }
}

```

## <a name="run-the-application"></a>Ausführen der Anwendung

Führen Sie die Anwendung mit dem Befehl `dotnet run` aus dem Anwendungsverzeichnis aus.

```dotnetcli
dotnet run
```
## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Cognitive Services-Abonnement bereinigen und entfernen möchten, können Sie die Ressource oder die Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen Ressourcen gelöscht, die der Ressourcengruppe zugeordnet sind.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-Befehlszeilenschnittstelle](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nächste Schritte

* [Was ist die Anomalieerkennungs-API?](../../overview-multivariate.md)
* [Bewährte Methoden bei der Verwendung der Anomalieerkennungs-API](../../concepts/best-practices-multivariate.md)