---
title: 'Schnellstart: Multivariate Python-Clientbibliothek für die Anomalieerkennung'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/29/2021
ms.author: mbullwin
ms.openlocfilehash: 0ac9f337ed24a3e440fe877998a40181853d5657
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128908508"
---
Hier finden Sie Informationen zu den ersten Schritten mit der multivariaten Clientbibliothek für die Anomalieerkennung für Python. Führen Sie diese Schritte aus, um das Paket zu installieren und mit der Verwendung der vom Dienst zur Verfügung gestellten Algorithmen zu beginnen. Die neuen APIs für die multivariate Anomalieerkennung ermöglichen Entwicklern die einfache Integration fortschrittlicher KI zur Erkennung von Anomalien in Metrikgruppen ganz ohne Machine Learning-Kenntnisse oder gekennzeichnete Daten. Abhängigkeiten und Interkorrelationen zwischen verschiedenen Signalen werden automatisch als Schlüsselfaktoren gewertet. Dadurch können Sie Ihre komplexen Systeme leichter proaktiv vor Fehlern schützen.

Die Clientbibliothek für die multivariate Anomalieerkennung für Python kann für Folgendes verwendet werden:

* Erkennen von Anomalien auf Systemebene in einer Gruppe von Zeitreihen
* Betrachten sämtlicher Signale, um ein Problem zu erkennen, wenn die Betrachtung einzelner Zeitreihen nicht ausreicht
* Prädikative Wartung teurer physischer Ressourcen mit dutzenden bis hunderten verschiedenen Arten von Sensoren zur Messung diverser Aspekte der Systemintegrität

[Referenzdokumentation der Bibliothek](/python/api/azure-ai-anomalydetector/azure.ai.anomalydetector) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/anomalydetector/azure-ai-anomalydetector) | [Paket (PyPi)](https://pypi.org/project/azure-ai-anomalydetector/3.0.0b3/) | [Beispielcode](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/anomalydetector/azure-ai-anomalydetector/samples/sample_multivariate_detect.py)

## <a name="prerequisites"></a>Voraussetzungen

* [Python 3.x](https://www.python.org/)
* Die [Pandas-Bibliothek für Datenanalyse](https://pandas.pydata.org/)
* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* Sobald Sie über Ihr Azure-Abonnement verfügen, erstellen Sie über <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Erstellen einer Anomalieerkennungsressource"  target="_blank"> eine Anomalieerkennungsressource </a> im Azure-Portal, um Ihren Schlüssel und Endpunkt zu erhalten. Warten Sie ihre Bereitstellung ab, und klicken Sie auf die Schaltfläche **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der Anomalieerkennungs-API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.


## <a name="setting-up"></a>Einrichten

### <a name="install-the-client-library"></a>Installieren der Clientbibliothek

Nach der Installation von Python, können Sie die Clientbibliothek mit folgendem Befehl installieren:

```console
pip install pandas
pip install --upgrade azure-ai-anomalydetector
```

### <a name="create-a-new-python-application"></a>Erstellen einer neuen Python-Anwendung

 Erstellen Sie eine neue Python-Datei, und importieren Sie die folgenden Bibliotheken:

```python
import os
import time
from datetime import datetime

from azure.ai.anomalydetector import AnomalyDetectorClient
from azure.ai.anomalydetector.models import DetectionRequest, ModelInfo
from azure.core.credentials import AzureKeyCredential
from azure.core.exceptions import HttpResponseError
```

Erstellen Sie Variablen für Ihren Schlüssel als Umgebungsvariable, den Pfad zu einer Zeitreihendatendatei und den Azure-Speicherort Ihres Abonnements. 

> [!NOTE]
> Sie haben stets die Möglichkeit, einen von zwei Schlüsseln zu verwenden. Dies ermöglicht eine sichere Schlüsselrotation. Verwenden Sie für diesen Schnellstart den ersten Schlüssel. 

```python
subscription_key = "ANOMALY_DETECTOR_KEY"
anomaly_detector_endpoint = "ANOMALY_DETECTOR_ENDPOINT"
```



## <a name="code-examples"></a>Codebeispiele

Diese Codeausschnitte veranschaulichen, wie folgende Vorgänge mit der Anomalieerkennungs-Clientbibliothek für Python ausgeführt werden:

* [Authentifizieren des Clients](#authenticate-the-client)
* [Modelltraining](#train-the-model)
* [Erkennen von Anomalien](#detect-anomalies)
* [Exportieren des Modells](#export-model)
* [Löschen des Modells](#delete-model)

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Um einen neuen Anomalieerkennungsclient zu instanziieren, müssen Sie den Abonnementschlüssel der Anomalieerkennung sowie den zugeordneten Endpunkt übergeben. Außerdem richten wir noch eine Datenquelle ein.  

Wenn Sie die multivariaten APIs für die Anomalieerkennung verwenden möchten, müssen Sie zunächst Ihre eigenen Modelle trainieren. Bei den Trainingsdaten handelt es sich um mehrere Zeitreihen, die die folgenden Anforderungen erfüllen:

Bei den Zeitreihen muss es sich jeweils um eine CSV-Datei mit genau zwei Spalten in der Headerzeile handeln: „timestamp“ und „value“ (in Kleinbuchstaben). Die „timestamp“-Werte müssen ISO 8601 entsprechen. Für „value“ können ganze Zahlen oder Dezimalzahlen mit einer beliebigen Anzahl von Dezimalstellen verwendet werden. Beispiel:

|timestamp | value|
|-------|-------|
|2019-04-01T00:00:00Z| 5|
|2019-04-01T00:01:00Z| 3.6|
|2019-04-01T00:02:00Z| 4|
|`...`| `...` |

Jede CSV-Datei muss nach einer anderen Variablen benannt werden, die für das Modelltraining verwendet wird. Beispiel: „temperature.csv“ und „humidity.csv“. Alle CSV-Dateien müssen ohne Unterordner in einer ZIP-Datei verpackt werden. Die ZIP-Datei kann einen beliebigen Namen haben. Die ZIP-Datei muss in Azure Blob Storage hochgeladen werden. Nach dem Generieren der Blob-SAS-URL (Shared Access Signature) für die ZIP-Datei kann sie für das Training verwendet werden. Informationen zum Generieren von SAS-URLs aus Azure Blob Storage finden Sie in diesem Dokument.

```python
class MultivariateSample():

def __init__(self, subscription_key, anomaly_detector_endpoint, data_source=None):
    self.sub_key = subscription_key
    self.end_point = anomaly_detector_endpoint

    # Create an Anomaly Detector client

    # <client>
    self.ad_client = AnomalyDetectorClient(AzureKeyCredential(self.sub_key), self.end_point)
    # </client>

    self.data_source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS"
```

## <a name="train-the-model"></a>Trainieren des Modells

Zunächst trainieren wir das Modell, überprüfen den Status des Modells während des Trainings, um zu ermitteln, wann das Training abgeschlossen ist, und rufen dann die neueste Modell-ID ab. Diese wird später in der Erkennungsphase benötigt.

```python
def train(self, start_time, end_time):
    # Number of models available now
    model_list = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    print("{:d} available models before training.".format(len(model_list)))
    
    # Use sample data to train the model
    print("Training new model...(it may take a few minutes)")
    data_feed = ModelInfo(start_time=start_time, end_time=end_time, source=self.data_source)
    response_header = \
    self.ad_client.train_multivariate_model(data_feed, cls=lambda *args: [args[i] for i in range(len(args))])[-1]
    trained_model_id = response_header['Location'].split("/")[-1]
    
    # Model list after training
    new_model_list = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    
    # Wait until the model is ready. It usually takes several minutes
    model_status = None
    while model_status != ModelStatus.READY and model_status != ModelStatus.FAILED:
        model_info = self.ad_client.get_multivariate_model(trained_model_id).model_info
        model_status = model_info.status
        time.sleep(10)

    if model_status == ModelStatus.FAILED:
        print("Creating model failed.")
        print("Errors:")
        if model_info.errors:
            for error in model_info.errors:
                print("Error code: {}. Message: {}".format(error.code, error.message))
        else:
            print("None")
        return None

    if model_status == ModelStatus.READY:
        # Model list after training
        new_model_list = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
        print("Done.\n--------------------")
        print("{:d} available models after training.".format(len(new_model_list)))

    # Return the latest model id
    return trained_model_id
```

## <a name="detect-anomalies"></a>Erkennen von Anomalien

Verwenden Sie `detect_anomaly` und `get_dectection_result`, um zu ermitteln, ob in Ihrer Datenquelle Anomalien vorhanden sind. Sie müssen die Modell-ID für das Modell übergeben, das Sie gerade trainiert haben.

```python
def detect(self, model_id, start_time, end_time):
    # Detect anomaly in the same data source (but a different interval)
    try:
        detection_req = DetectionRequest(source=self.data_source, start_time=start_time, end_time=end_time)
        response_header = self.ad_client.detect_anomaly(model_id, detection_req,
                                                        cls=lambda *args: [args[i] for i in range(len(args))])[-1]
        result_id = response_header['Location'].split("/")[-1]
    
        # Get results (may need a few seconds)
        r = self.ad_client.get_detection_result(result_id)
        while r.summary.status != DetectionStatus.READY and r.summary.status != DetectionStatus.FAILED:
            r = self.ad_client.get_detection_result(result_id)
            time.sleep(2)

        if r.summary.status == DetectionStatus.FAILED:
            print("Detection failed.")
            print("Errors:")
            if r.summary.errors:
                for error in r.summary.errors:
                    print("Error code: {}. Message: {}".format(error.code, error.message))
            else:
                print("None")
            return None
    except HttpResponseError as e:
        print('Error code: {}'.format(e.error.code), 'Error message: {}'.format(e.error.message))
    except Exception as e:
        raise e
    return r
```

## <a name="export-model"></a>Exportieren des Modells

> [!NOTE]
> Der Exportbefehl dient dazu, multivariate Modelle der Anomalieerkennung in einer containerisierten Umgebung ausführen zu können. Dies wird derzeit nicht für multivariate Modelle unterstützt, aber in Zukunft unterstützt werden.

Wenn Sie ein Modell exportieren möchten, verwenden Sie `export_model`, und übergeben Sie die Modell-ID des zu exportierenden Modells:

```python
def export_model(self, model_id, model_path="model.zip"):

    # Export the model
    model_stream_generator = self.ad_client.export_model(model_id)
    with open(model_path, "wb") as f_obj:
        while True:
            try:
                f_obj.write(next(model_stream_generator))
            except StopIteration:
                break
            except Exception as e:
                raise e
```

## <a name="delete-model"></a>Löschen des Modells

Wenn Sie ein Modell löschen möchten, verwenden Sie `delete_multivariate_model`, und übergeben Sie die Modell-ID des zu löschenden Modells:

```python
def delete_model(self, model_id):

    # Delete the mdoel
    self.ad_client.delete_multivariate_model(model_id)
    model_list_after_delete = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    print("{:d} available models after deletion.".format(len(model_list_after_delete)))
```

## <a name="run-the-application"></a>Ausführen der Anwendung

Vor dem Ausführen der Anwendung müssen wir Code hinzufügen, um unsere neu erstellten Funktionen aufzurufen.

```python
if __name__ == '__main__':
    subscription_key = "ANOMALY_DETECTOR_KEY"
    anomaly_detector_endpoint = "ANOMALY_DETECTOR_ENDPOINT"

    # Create a new sample and client
    sample = MultivariateSample(subscription_key, anomaly_detector_endpoint, data_source=None)

    # Train a new model
    model_id = sample.train(datetime(2021, 1, 1, 0, 0, 0), datetime(2021, 1, 2, 12, 0, 0))

    # Reference
    result = sample.detect(model_id, datetime(2021, 1, 2, 12, 0, 0), datetime(2021, 1, 3, 0, 0, 0))
    print("Result ID:\t", result.result_id)
    print("Result summary:\t", result.summary)
    print("Result length:\t", len(result.results))

    # Export model
    sample.export_model(model_id, "model.zip")

    # Delete model
    sample.delete_model(model_id)

```

Vor der Ausführung kann es hilfreich sein, Ihr Projekt anhand des [vollständigen Beispielcodes](https://github.com/Azure-Samples/AnomalyDetector/blob/master/ipython-notebook/API%20Sample/Multivariate%20API%20Demo%20Notebook.ipynb) zu überprüfen, von dem diese Schnellstartanleitung abgeleitet ist.

Darüber hinaus gibt es ein [ausführliches Jupyter Notebook-Beispiel](https://github.com/Azure-Samples/AnomalyDetector/blob/master/ipython-notebook/API%20Sample/Multivariate%20API%20Demo%20Notebook.ipynb), das Ihnen den Einstieg erleichtert.

Führen Sie die Anwendung mit dem Befehl `python` und Ihrem Dateinamen aus.


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Cognitive Services-Abonnement bereinigen und entfernen möchten, können Sie die Ressource oder die Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen Ressourcen gelöscht, die der Ressourcengruppe zugeordnet sind.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-Befehlszeilenschnittstelle](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nächste Schritte

* [Was ist die Anomalieerkennungs-API?](../../overview-multivariate.md)
* [Bewährte Methoden bei der Verwendung der Anomalieerkennungs-API](../../concepts/best-practices-multivariate.md)
