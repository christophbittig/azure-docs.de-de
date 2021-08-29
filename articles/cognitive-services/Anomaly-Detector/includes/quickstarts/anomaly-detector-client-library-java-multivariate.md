---
title: 'Schnellstart: Multivariate Java-Clientbibliothek für die Anomalieerkennung'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/29/2021
ms.author: mbullwin
ms.openlocfilehash: cbea7a93d80a0d8f68b23cbcfde92d34d5a0d1d0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121801869"
---
Hier finden Sie Informationen zu den ersten Schritten mit der multivariaten Clientbibliothek für die Anomalieerkennung für Java. Führen Sie diese Schritte aus, um das Paket zu installieren und mit der Verwendung der vom Dienst zur Verfügung gestellten Algorithmen zu beginnen. Die neuen APIs für die multivariate Anomalieerkennung ermöglichen Entwicklern die einfache Integration fortschrittlicher KI zur Erkennung von Anomalien in Metrikgruppen ganz ohne Machine Learning-Kenntnisse oder gekennzeichnete Daten. Abhängigkeiten und Interkorrelationen zwischen verschiedenen Signalen werden automatisch als Schlüsselfaktoren gewertet. Dadurch können Sie Ihre komplexen Systeme leichter proaktiv vor Fehlern schützen.

Die Clientbibliothek für die multivariate Anomalieerkennung für Java kann für Folgendes verwendet werden:

* Erkennen von Anomalien auf Systemebene in einer Gruppe von Zeitreihen
* Betrachten sämtlicher Signale, um ein Problem zu erkennen, wenn die Betrachtung einzelner Zeitreihen nicht ausreicht
* Prädikative Wartung teurer physischer Ressourcen mit dutzenden bis hunderten verschiedenen Arten von Sensoren zur Messung diverser Aspekte der Systemintegrität

[Referenzdokumentation der Bibliothek](/java/api/com.azure.ai.anomalydetector) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/anomalydetector/azure-ai-anomalydetector) | [Paket (Maven)](https://repo1.maven.org/maven2/com/azure/azure-ai-anomalydetector/3.0.0-beta.2/) | [Beispielcode](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/anomalydetector/azure-ai-anomalydetector/src/samples/java/com/azure/ai/anomalydetector/MultivariateSample.java)

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* Aktuelle Version des [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle-Buildtool](https://gradle.org/install/) oder einen anderen Abhängigkeit-Manager
* Sobald Sie über Ihr Azure-Abonnement verfügen, erstellen Sie über <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Erstellen einer Anomalieerkennungsressource"  target="_blank"> eine Anomalieerkennungsressource </a> im Azure-Portal, um Ihren Schlüssel und Endpunkt zu erhalten. Warten Sie ihre Bereitstellung ab, und klicken Sie auf die Schaltfläche **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der Anomalieerkennungs-API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.

## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-gradle-project"></a>Erstellen eines neuen Gradle-Projekts

In dieser Schnellstartanleitung wird der Gradle-Abhängigkeits-Manager verwendet. Weitere Informationen zur Clientbibliothek finden Sie im [Maven Central Repository](https://search.maven.org/artifact/com.azure/azure-ai-metricsadvisor).

Erstellen Sie in einem Konsolenfenster (etwa cmd, PowerShell oder Bash) ein neues Verzeichnis für Ihre App, und rufen Sie es auf. 

```console
mkdir myapp && cd myapp
```

Führen Sie den Befehl `gradle init` in Ihrem Arbeitsverzeichnis aus. Mit diesem Befehl werden grundlegende Builddateien für Gradle erstellt, u. a. die Datei *build.gradle.kts*. Diese Datei wird zur Laufzeit zum Erstellen und Konfigurieren Ihrer Anwendung verwendet.

```console
gradle init --type basic
```

Wenn Sie zur Auswahl einer **DSL** aufgefordert werden, wählen Sie **Kotlin** aus.

### <a name="install-the-client-library"></a>Installieren der Clientbibliothek

Navigieren Sie zur Datei *build.gradle.kts*, und öffnen Sie sie in Ihrer bevorzugten IDE bzw. Ihrem bevorzugten Text-Editor. Kopieren Sie anschließend diese Buildkonfiguration. Achten Sie darauf, dass Sie die Projektabhängigkeiten einbinden.

```kotlin
dependencies {
    compile("com.azure:azure-ai-anomalydetector")
}
```

### <a name="create-a-java-file"></a>Erstellen einer Java-Datei

Erstellen Sie einen Ordner für Ihre Beispiel-App. Führen Sie in Ihrem Arbeitsverzeichnis den folgenden Befehl aus:

```console
mkdir -p src/main/java
```

Navigieren Sie zum neuen Ordner, und erstellen Sie eine Datei mit dem Namen *MetricsAdvisorQuickstarts.java*. Öffnen Sie sie in Ihrem bevorzugten Editor bzw. Ihrer bevorzugten IDE, und fügen Sie die folgenden `import`-Anweisungen hinzu:

```java
package com.azure.ai.anomalydetector;

import com.azure.ai.anomalydetector.models.*;
import com.azure.core.credential.AzureKeyCredential;
import com.azure.core.http.*;
import com.azure.core.http.policy.*;
import com.azure.core.http.rest.PagedIterable;
import com.azure.core.http.rest.PagedResponse;
import com.azure.core.http.rest.Response;
import com.azure.core.http.rest.StreamResponse;
import com.azure.core.util.Context;
import reactor.core.publisher.Flux;

import java.io.FileOutputStream;
import java.io.IOException;
import java.io.UncheckedIOException;
import java.nio.ByteBuffer;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.time.*;
import java.time.format.DateTimeFormatter;
import java.util.Iterator;
import java.util.List;
import java.util.UUID;
import java.util.stream.Collectors;
```

Erstellen Sie Variablen für den Azure-Endpunkt und -Schlüssel Ihrer Ressource. Erstellen Sie eine weitere Variable für die Beispieldatendatei.

> [!NOTE]
> Sie haben stets die Möglichkeit, einen von zwei Schlüsseln zu verwenden. Dies ermöglicht eine sichere Schlüsselrotation. Verwenden Sie für diesen Schnellstart den ersten Schlüssel. 

```java
String key = "YOUR_API_KEY";
String endpoint = "YOUR_ENDPOINT";
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

Diese Codeausschnitte veranschaulichen, wie folgende Vorgänge mit der Anomalieerkennungs-Clientbibliothek für Node.js durchgeführt werden:

* [Authentifizieren des Clients](#authenticate-the-client)
* [Trainieren eines Modells](#train-a-model)
* [Erkennen von Anomalien](#detect-anomalies)
* [Exportieren des Modells](#export-model)
* [Löschen des Modells](#delete-model)

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Instanziieren Sie ein `anomalyDetectorClient`-Objekt mit ihrem Endpunkt und Ihren Anmeldeinformationen.

```java
HttpHeaders headers = new HttpHeaders()
    .put("Accept", ContentType.APPLICATION_JSON);

HttpPipelinePolicy authPolicy = new AzureKeyCredentialPolicy(key,
    new AzureKeyCredential(key));
AddHeadersPolicy addHeadersPolicy = new AddHeadersPolicy(headers);

HttpPipeline httpPipeline = new HttpPipelineBuilder().httpClient(HttpClient.createDefault())
    .policies(authPolicy, addHeadersPolicy).build();
// Instantiate a client that will be used to call the service.
HttpLogOptions httpLogOptions = new HttpLogOptions();
httpLogOptions.setLogLevel(HttpLogDetailLevel.BODY_AND_HEADERS);

AnomalyDetectorClient anomalyDetectorClient = new AnomalyDetectorClientBuilder()
    .pipeline(httpPipeline)
    .endpoint(endpoint)
    .httpLogOptions(httpLogOptions)
    .buildClient();
```

## <a name="train-a-model"></a>Trainieren eines Modells

### <a name="construct-a-model-result-and-train-model"></a>Erstellen eines Modellergebnisses und Trainieren des Modells

Zunächst müssen wir eine Modellanforderung erstellen. Achten Sie darauf, dass Start- und Endzeit mit Ihrer Datenquelle übereinstimmen.

Um die multivariaten APIs für die Anomalieerkennung verwenden zu können, müssen wir unser eigenes Modell vor der Erkennung trainieren. Für das Training wird ein Batch von Zeitreihendaten verwendet. Die einzelnen Zeitreihen müssen jeweils in einer CSV-Datei mit nur zwei Spalten enthalten sein: **timestamp** und **value**. (Die Spaltennamen müssen genau übereinstimmen.) Jede CSV-Datei sollte jeweils nach den einzelnen Variablen für die Zeitreihe benannt werden. Alle Zeitreihen müssen in einer ZIP-Datei zusammengefasst und in [Azure Blob Storage](../../../../storage/blobs/storage-blobs-introduction.md#blobs) hochgeladen werden. Für den Namen der ZIP-Datei gelten keine besonderen Anforderungen. Alternativ kann eine zusätzliche Datei vom Typ „meta.json“ in die ZIP-Datei eingeschlossen werden, wenn sich der Name der Variablen vom Namen der ZIP-Datei unterscheiden soll. Nach dem Generieren der [BLOB-SAS-URL (Shared Access Signatures)](../../../../storage/common/storage-sas-overview.md) können wir die URL der ZIP-Datei für das Training verwenden.

```java
Path path = Paths.get("test-data.csv");
List<String> requestData = Files.readAllLines(path);
List<TimeSeriesPoint> series = requestData.stream()
    .map(line -> line.trim())
    .filter(line -> line.length() > 0)
    .map(line -> line.split(",", 2))
    .filter(splits -> splits.length == 2)
    .map(splits -> {
        TimeSeriesPoint timeSeriesPoint = new TimeSeriesPoint();
        timeSeriesPoint.setTimestamp(OffsetDateTime.parse(splits[0]));
        timeSeriesPoint.setValue(Float.parseFloat(splits[1]));
        return timeSeriesPoint;
    })
    .collect(Collectors.toList());

Integer window = 28;
AlignMode alignMode = AlignMode.OUTER;
FillNAMethod fillNAMethod = FillNAMethod.LINEAR;
Integer paddingValue = 0;
AlignPolicy alignPolicy = new AlignPolicy()
                                .setAlignMode(alignMode)
                                .setFillNAMethod(fillNAMethod)
                                .setPaddingValue(paddingValue);
String source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
OffsetDateTime startTime = OffsetDateTime.of(2021, 1, 2, 0, 0, 0, 0, ZoneOffset.UTC);
OffsetDateTime endTime = OffsetDateTime.of(2021, 1, 3, 0, 0, 0, 0, ZoneOffset.UTC);
String displayName = "Devops-MultiAD";

ModelInfo request = new ModelInfo()
                        .setSlidingWindow(window)
                        .setAlignPolicy(alignPolicy)
                        .setSource(source)
                        .setStartTime(startTime)
                        .setEndTime(endTime)
                        .setDisplayName(displayName);
TrainMultivariateModelResponse trainMultivariateModelResponse = anomalyDetectorClient.trainMultivariateModelWithResponse(request, Context.NONE);
String header = trainMultivariateModelResponse.getDeserializedHeaders().getLocation();
String[] substring = header.split("/");
UUID modelId = UUID.fromString(substring[substring.length - 1]);
System.out.println(modelId);

//Check model status until the model is ready
Response<Model> trainResponse;
while (true) {
    trainResponse = anomalyDetectorClient.getMultivariateModelWithResponse(modelId, Context.NONE);
    ModelStatus modelStatus = trainResponse.getValue().getModelInfo().getStatus();
    if (modelStatus == ModelStatus.READY || modelStatus == ModelStatus.FAILED) {
        break;
    }
    TimeUnit.SECONDS.sleep(10);
}

if (trainResponse.getValue().getModelInfo().getStatus() != ModelStatus.READY){
    System.out.println("Training failed.");
    List<ErrorResponse> errorMessages = trainResponse.getValue().getModelInfo().getErrors();
    for (ErrorResponse errorMessage : errorMessages) {
        System.out.println("Error code:  " + errorMessage.getCode());
        System.out.println("Error message:  " + errorMessage.getMessage());
    }
}
```

## <a name="detect-anomalies"></a>Erkennen von Anomalien

```java
DetectionRequest detectionRequest = new DetectionRequest().setSource(source).setStartTime(startTime).setEndTime(endTime);
DetectAnomalyResponse detectAnomalyResponse = anomalyDetectorClient.detectAnomalyWithResponse(modelId, detectionRequest, Context.NONE);
String location = detectAnomalyResponse.getDeserializedHeaders().getLocation();
String[] substring = location.split("/");
UUID resultId = UUID.fromString(substring[substring.length - 1]);

DetectionResult detectionResult;
while (true) {
    detectionResult = anomalyDetectorClient.getDetectionResult(resultId);
    DetectionStatus detectionStatus = detectionResult.getSummary().getStatus();;
    if (detectionStatus == DetectionStatus.READY || detectionStatus == DetectionStatus.FAILED) {
        break;
    }
    TimeUnit.SECONDS.sleep(10);
}

if (detectionResult.getSummary().getStatus() != DetectionStatus.READY){
    System.out.println("Inference failed");
    List<ErrorResponse> detectErrorMessages = detectionResult.getSummary().getErrors();
    for (ErrorResponse errorMessage : detectErrorMessages) {
        System.out.println("Error code:  " + errorMessage.getCode());
        System.out.println("Error message:  " + errorMessage.getMessage());
    }
}
```

## <a name="export-model"></a>Exportieren des Modells

> [!NOTE]
> Der Exportbefehl dient dazu, multivariate Modelle der Anomalieerkennung in einer containerisierten Umgebung ausführen zu können. Dies wird derzeit nicht für multivariate Modelle unterstützt, aber in Zukunft unterstützt werden.

Verwenden Sie `exportModelWithResponse`, um Ihr trainiertes Modell zu exportieren.

```java
StreamResponse response_export = anomalyDetectorClient.exportModelWithResponse(model_id, Context.NONE);
Flux<ByteBuffer> value = response_export.getValue();
FileOutputStream bw = new FileOutputStream("result.zip");
value.subscribe(s -> write(bw, s), (e) -> close(bw), () -> close(bw));
```

## <a name="delete-model"></a>Löschen des Modells

Verwenden Sie die Funktion `deleteMultivariateModelWithResponse`, um ein vorhandenes Modell zu löschen, das für die aktuelle Ressource verfügbar ist.

```java
Response<Void> deleteMultivariateModelWithResponse = anomalyDetectorClient.deleteMultivariateModelWithResponse(model_id, Context.NONE);
```

## <a name="run-the-application"></a>Ausführen der Anwendung

Sie können die App mit folgendem Befehl erstellen:

```console
gradle build
```
### <a name="run-the-application"></a>Ausführen der Anwendung

Vor der Ausführung kann es hilfreich sein, Ihren Code anhand des [vollständigen Beispielcodes](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/anomalydetector/azure-ai-anomalydetector/src/samples/java/com/azure/ai/anomalydetector/MultivariateSample.java) zu überprüfen.

Führen Sie die Anwendung mit dem Ziel `run` aus:

```console
gradle run
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Cognitive Services-Abonnement bereinigen und entfernen möchten, können Sie die Ressource oder die Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen Ressourcen gelöscht, die der Ressourcengruppe zugeordnet sind.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-Befehlszeilenschnittstelle](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nächste Schritte

* [Was ist die Anomalieerkennungs-API?](../../overview-multivariate.md)
* [Bewährte Methoden bei der Verwendung der Anomalieerkennungs-API](../../concepts/best-practices-multivariate.md)
