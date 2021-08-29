---
title: 'Tutorial: Erkennung von multivariaten Anomalien (Dauer eine Stunde)'
titleSuffix: Azure Cognitive Services
description: Ein End-to-End-Tutorial zur Erkennung von multivariaten Anomalien.
services: cognitive-services
author: juaduan
manager: conhua
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 06/27/2021
ms.author: juaduan
ms.openlocfilehash: bdebf242dcea8e7640d68afcb21d7ba7e2e49576
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292636"
---
# <a name="tutorial-learn-multivariate-anomaly-detection-in-one-hour"></a>Tutorial: Erkennung von multivariaten Anomalien (Dauer eine Stunde)

Die Anomalieerkennung mit MVAD (Erkennung von multivariaten Anomalien) ist ein erweitertes KI-Tool zum Erkennen von Anomalien aus einer Gruppe von Metriken auf nicht **überwachte** Weise.

Im Allgemeinen können Sie die folgenden Schritte ausführen, um MVAD zu verwenden:

  1. Erstellen Sie eine Anomalieerkennungsressource, die MVAD in Azure unterstützt.
  1. Bereiten Sie Ihre Daten vor.
  1. Trainieren Sie ein MVAD-Modell.
  1. Fragen Sie den Status Ihres Modells ab.
  1. Suchen Sie nach Anomalien mit dem trainierten MVAD-Modell.
  1. Rufen Sie die Rückschlussergebnisse ab und interpretieren Sie diese.

In diesem Tutorial gehen Sie wie folgt vor:

> [!div class="checklist"]
> * Erfahren Sie, wie Sie Ihre Daten in einem richtigen Format vorbereiten.
> * Erfahren Sie, wie Sie mit MVAD trainieren und Rückschlüsse ziehen können.
> * Lernen Sie die Eingabeparameter kennen und erfahren, wie Sie die Ausgabe in den Rückschlussergebnissen interpretieren.

## <a name="1-create-an-anomaly-detector-resource-that-supports-mvad"></a>1. Erstellen einer Anomalieerkennungsressource, die MVAD unterstützt.

* Erstellten Sie ein Azure-Abonnement, falls Sie über keins verfügen - [Kostenloses Konto erstellen](https://azure.microsoft.com/free/cognitive-services)
* Sobald Sie über Ihr Azure-Abonnement verfügen, erstellen Sie im Azure-Portal eine [Anomalieerkennungsressource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector), um Ihren API-Schlüssel und API-Endpunkt zu erhalten.

> [!NOTE]
> Während der Vorschauphase ist MVAD nur in eingeschränkten Regionen verfügbar. Setzen Sie die Textmarke [Neuerungen in der Anomalieerkennung](../whats-new.md), um im Hinblick auf die regionalen MVAD-Roll-Outs auf dem neuesten Stand zu bleiben. Sie können auch ein GitHub-Problem melden oder sich unter [AnomalyDetector@microsoft.com](mailto:AnomalyDetector@microsoft.com) an uns wenden, um eine Anfrage für bestimmte Regionen zu stellen.

## <a name="2-data-preparation"></a>2. Datenaufbereitung

Anschließend müssen Sie Ihre Trainingsdaten (und Rückschlussdaten) vorbereiten.

[!INCLUDE [mvad-data-schema](../includes/mvad-data-schema.md)]

### <a name="tools-for-zipping-and-uploading-data"></a>Tools zum Zippen und Hochladen von Daten

In diesem Abschnitt geben wir Beispielcode und Tools an, die Sie kopieren und bearbeiten können, um sie Ihrer eigenen Anwendungslogik hinzuzufügen, die MVAD-Eingabedaten nutzt.

#### <a name="compressing-csv-files-in-nix"></a>Komprimieren von CSV-Dateien in \*nix

```bash
zip -j series.zip series/*.csv
```

#### <a name="compressing-csv-files-in-windows"></a>Komprimieren von CSV-Dateien in Windows

* Navigieren Sie *in* den Ordner mit allen CSV-Dateien.
* Wählen Sie alle benötigten CSV-Dateien aus.
* Klicken Sie mit der rechten Maustaste auf eine der CSV-Dateien und wählen Sie `Send to` aus.
* Wählen Sie `Compressed (zipped) folder` in der Dropdownliste aus.
* Benennen Sie die ZIP-Datei je nach Bedarf um.

#### <a name="python-code-zipping--uploading-data-to-azure-blob-storage"></a>Zippen von Python-Code & Hochladen von Daten in Azure Blob Storage

Informationen zum Hochladen einer Datei auf Azure-Blob finden Sie in [diesem Dokument](../../../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob).

Sie können auch den folgenden Beispielcode verwenden, der das Zippen und Hochladen für Sie ausführen kann. Sie können den Python-Code in diesem Abschnitt als .py-Datei (z. B. `zipAndUpload.py`) kopieren und speichern und mithilfe von Befehlszeilen wie den folgenden ausführen:

* `python zipAndUpload.py -s "foo\bar" -z test123.zip -c {azure blob connection string} -n container_xxx`

    Mit diesem Befehl werden alle CSV-Dateien in `foo\bar` in einer einzelnen ZIP-Datei mit dem Namen `test123.zip` komprimiert. Hierdurch wird `test123.zip` in den Container `container_xxx` in Ihrem Blob hochgeladen.
* `python zipAndUpload.py -s "foo\bar" -z test123.zip -c {azure blob connection string} -n container_xxx -r` 

    Mit diesem Befehl wird das gleiche wie oben ausgeführt, aber die ZIP-Datei `test123.zip` wird nach dem erfolgreichen Hochladen gelöscht. 

Argumente:

* `--source-folder`, `-s`, Pfad zum Quellordner mit CSV-Dateien
* `--zipfile-name`, `-z`, Name der ZIP-Datei
* `--connection-string`, `-c`, Verbindungszeichenfolge für Ihren Blob
* `--container-name`, `-n`, Name des Containers
* `--remove-zipfile`, `-r`, wenn „an“ (on), entfernen Sie die ZIP-Datei

```python
import os
import argparse
import shutil
import sys

from azure.storage.blob import BlobClient
import zipfile


class ZipError(Exception):
    pass


class UploadError(Exception):
    pass


def zip_file(root, name):
    try:
        z = zipfile.ZipFile(name, "w", zipfile.ZIP_DEFLATED)
        for f in os.listdir(root):
            if f.endswith("csv"):
                z.write(os.path.join(root, f), f)
        z.close()
        print("Compress files success!")
    except Exception as ex:
        raise ZipError(repr(ex))


def upload_to_blob(file, conn_str, cont_name, blob_name):
    try:
        blob_client = BlobClient.from_connection_string(conn_str, container_name=cont_name, blob_name=blob_name)
        with open(file, "rb") as f:
            blob_client.upload_blob(f, overwrite=True)
        print("Upload Success!")
    except Exception as ex:
        raise UploadError(repr(ex))


if __name__ == "__main__":
    parser = argparse.ArgumentParser()
    parser.add_argument("--source-folder", "-s", type=str, required=True, help="path to source folder")
    parser.add_argument("--zipfile-name", "-z", type=str, required=True, help="name of the zip file")
    parser.add_argument("--connection-string", "-c", type=str, help="connection string")
    parser.add_argument("--container-name", "-n", type=str, help="container name")
    parser.add_argument("--remove-zipfile", "-r", action="store_true", help="whether delete the zip file after uploading")
    args = parser.parse_args()

    try:
        zip_file(args.source_folder, args.zipfile_name)
        upload_to_blob(args.zipfile_name, args.connection_string, args.container_name, args.zipfile_name)
    except ZipError as ex:
        print(f"Failed to compress files. {repr(ex)}")
        sys.exit(-1)
    except UploadError as ex:
        print(f"Failed to upload files. {repr(ex)}")
        sys.exit(-1)
    except Exception as ex:
        print(f"Exception encountered. {repr(ex)}")

    try:
        if args.remove_zipfile:
            os.remove(args.zipfile_name)
    except Exception as ex:
        print(f"Failed to delete the zip file. {repr(ex)}")
```

## <a name="3-train-an-mvad-model"></a>3. Trainieren eines MVAD-Modells

Hier sehen Sie einen bespielhaften Anforderungstext und den Beispielcode in Python zum Trainieren eines MVAD-Modells.

```json
// Sample Request Body
{
    "slidingWindow": 200,
    "alignPolicy": {
        "alignMode": "Outer",
        "fillNAMethod": "Linear", 
        "paddingValue": 0
    },
    // This could be your own ZIP file of training data stored on Azure Blob and a SAS url could be used here
    "source": "https://aka.ms/AnomalyDetector/MVADSampleData", 
    "startTime": "2021-01-01T00:00:00Z", 
    "endTime": "2021-01-02T12:00:00Z", 
    "displayName": "Contoso model"
}
```

```python
# Sample Code in Python
########### Python 3.x #############
import http.client, urllib.request, urllib.parse, urllib.error, base64

headers = {
    # Request headers
    'Content-Type': 'application/json',
    'Ocp-Apim-Subscription-Key': '{API key}',
}

params = urllib.parse.urlencode({})

try:
    conn = http.client.HTTPSConnection('{endpoint}')
    conn.request("POST", "/anomalydetector/v1.1-preview/multivariate/models?%s" % params, "{request body}", headers)
    response = conn.getresponse()
    data = response.read()
    print(data)
    conn.close()
except Exception as e:
    print("[Errno {0}] {1}".format(e.errno, e.strerror))

####################################
```

Der Antwortcode `201` gibt an, dass die Anforderung erfolgreich war.

[!INCLUDE [mvad-input-params](../includes/mvad-input-params.md)]

## <a name="4-get-model-status"></a>4. Status des Modells erhalten

Da die Trainings-API asynchron ist, erhalten Sie das Modell nicht sofort nach dem Aufruf der Trainings-API. Sie können den Status von Modellen jedoch entweder nach API-Schlüssel abfragen, der alle Modelle auflistet, oder nach Modell-ID, die Informationen zum jeweiligen Modell auflistet.

### <a name="list-all-the-models"></a>Auflisten aller Modelle

Informationen zur Anforderungs-URL und den Anforderungsheadern finden Sie auf [dieser Seite](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector-v1-1-preview/operations/ListMultivariateModel). Beachten Sie, dass wir nur 10 Modelle zurückgeben, die nach Aktualisierungszeit geordnet sind. Sie können jedoch andere Modelle besuchen, indem Sie die Parameter `$skip` und `$top` in der Anforderungs-URL festlegen. Wenn Ihre Anforderungs-URL beispielsweise `https://{endpoint}/anomalydetector/v1.1-preview/multivariate/models?$skip=10&$top=20` lautet, überspringen wir die letzten 10 Modelle und geben die nächsten 20 Modelle zurück.

Eine Beispielantwort lautet: 

```json
{
    "models": [
         {
             "createdTime":"2020-12-01T09:43:45Z",
             "displayName":"DevOps-Test",
             "lastUpdatedTime":"2020-12-01T09:46:13Z",
             "modelId":"b4c1616c-33b9-11eb-824e-0242ac110002",
             "status":"READY",
             "variablesCount":18
         },
         {
             "createdTime":"2020-12-01T09:43:30Z",
             "displayName":"DevOps-Test",
             "lastUpdatedTime":"2020-12-01T09:45:10Z",
             "modelId":"ab9d3e30-33b9-11eb-a3f4-0242ac110002",
             "status":"READY",
             "variablesCount":18
         }
    ],
    "currentCount": 1,
    "maxCount": 50, 
    "nextLink": "<link to more models>"
}
```

Die Antwort enthält 4 Felder: `models`, `currentCount`, `maxCount` und `nextLink`.

* `models` enthält den erstellten Zeitpunkt, die Uhrzeit der letzten Aktualisierung, die Modell-ID, den Anzeigenamen, die Variablenanzahl und den Status der einzelnen Modelle.
* `currentCount` enthält die Anzahl der trainierten multivariaten Modelle.
* `maxCount` ist die maximale Anzahl von Modellen, die von dieser Anomalieerkennungsressource unterstützt wird.
* `nextLink` kann zum Abrufen von mehr Modellen verwendet werden.

### <a name="get-models-by-model-id"></a>Modelle nach Modell-ID abholen

[Auf dieser Seite](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector-v1-1-preview/operations/GetMultivariateModel) wird die Anforderungs-URL zum Abfragen von Modellinformationen nach Modell-ID beschrieben. Eine Beispiel-Antwort sieht folgendermaßen aus:

```json
{
        "modelId": "45aad126-aafd-11ea-b8fb-d89ef3400c5f",
        "createdTime": "2020-06-30T00:00:00Z",
        "lastUpdatedTime": "2020-06-30T00:00:00Z",
        "modelInfo": {
          "slidingWindow": 300,
          "alignPolicy": {
            "alignMode": "Outer",
            "fillNAMethod": "Linear",
            "paddingValue": 0
          },
          "source": "<TRAINING_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS>",
          "startTime": "2019-04-01T00:00:00Z",
          "endTime": "2019-04-02T00:00:00Z",
          "displayName": "Devops-MultiAD",
          "status": "READY",
          "errors": [],
          "diagnosticsInfo": {
            "modelState": {
              "epochIds": [10, 20, 30, 40, 50, 60, 70, 80, 90, 100],
              "trainLosses": [0.6291328072547913, 0.1671326905488968, 0.12354248017072678, 0.1025966405868533, 
                              0.0958492755889896, 0.09069952368736267,0.08686016499996185, 0.0860302299260931,
                              0.0828735455870684, 0.08235538005828857],
              "validationLosses": [1.9232804775238037, 1.0645641088485718, 0.6031560301780701, 0.5302737951278687, 
                                   0.4698025286197664, 0.4395163357257843, 0.4182931482799006, 0.4057914316654053, 
                                   0.4056498706340729, 0.3849248886108984],
              "latenciesInSeconds": [0.3398594856262207, 0.3659665584564209, 0.37360644340515137, 
                                     0.3513407707214355, 0.3370304107666056, 0.31876277923583984, 
                                     0.3283309936523475, 0.3503587245941162, 0.30800247192382812,
                                     0.3327946662902832]
            },
            "variableStates": [
              {
                "variable": "ad_input",
                "filledNARatio": 0,
                "effectiveCount": 1441,
                "startTime": "2019-04-01T00:00:00Z",
                "endTime": "2019-04-02T00:00:00Z",
                "errors": []
              },
              {
                "variable": "ad_ontimer_output",
                "filledNARatio": 0,
                "effectiveCount": 1441,
                "startTime": "2019-04-01T00:00:00Z",
                "endTime": "2019-04-02T00:00:00Z",
                "errors": []
              },
              // More variables
            ]
          }
        }
      }
```

Sie erhalten ausführlichere Informationen zum abgefragten Modell. Die Antwort enthält Metainformationen über das Modell, seine Trainingsparameter und Diagnoseinformationen. Diagnoseinformationen sind nützlich zum Debuggen und Nachverfolgen des Trainingsfortschritts.

* `epochIds` gibt an, wie viele Epochen das Modell von insgesamt 100 Epochen trainiert hat. Wenn sich das Modell beispielsweise noch im Trainingsstatus befindet, kann `epochId` `[10, 20, 30, 40, 50]` sein, was bedeutet, dass es seine 50. Trainingsepoche abgeschlossen hat und nur noch 50 % zu absolvieren sind.
* `trainLosses` und `validationLosses` werden verwendet, um zu überprüfen, ob der Optimierungsfortschritt konvergiert. In diesem Fall sollten die beiden Verluste allmählich abnehmen.
* `latenciesInSeconds` enthält die Zeitkosten für jede Epoche und wird alle 10 Epochen aufgezeichnet. In diesem Beispiel dauert die 10. Epoche ungefähr 0,34 Sekunden. Dies ist hilfreich, um abzuschätzen, wann das Training beendet sein wird.
* `variableStates` fasst Informationen zu den einzelnen Variablen zusammen. Es handelt sich um eine Liste, die nach `filledNARatio` in absteigender Reihenfolge geordnet ist. Sie gibt an, wie viele Datenpunkte für jede Variable verwendet werden, und `filledNARatio` gibt an, wie viele Punkte fehlen. In der Regel müssen wir `filledNARatio` so viel wie möglich reduzieren.
Zu viele fehlende Datenpunkte verschlechtern die Genauigkeit des Modells.
* Fehler während der Datenverarbeitung werden in Feld `errors` eingeschlossen.

## <a name="5-inference-with-mvad"></a>5. Rückschlüsse mit MVAD

Um Rückschlüsse zu erstellen, geben Sie einfach die Blobquelle für die ZIP-Datei an, die die Rückschlussdaten, die Startzeit und die Endzeit enthält.

Der Rückschluss ist ebenfalls asynchron, sodass die Ergebnisse nicht sofort zurückgegeben werden. Beachten Sie, dass Sie den Link der Ergebnisse im **Antwortheader**, der `resultId` enthält, in einer Variablen speichern müssen, damit Sie wissen, woher Sie die Ergebnisse später erhalten.

Fehler werden in der Regel durch Modell- oder Datenprobleme verursacht. Sie können keinen Rückschluss ausführen, wenn das Modell nicht bereit oder der Datenlink ungültig ist. Stellen Sie sicher, dass die Trainings- und Rückschlussdaten konsistent sind. Das bedeutet, dass es sich um **genau** die gleichen Variablen handelt, die aber mit unterschiedlichen Zeitstempeln versehen sind. Mehr Variablen, weniger Variablen oder Rückschluss mit einem anderen Satz von Variablen passieren die Datenüberprüfung nicht, und es treten Fehler auf. Die Datenüberprüfung wird verzögert, sodass Sie nur dann eine Fehlermeldung erhalten, wenn Sie die Ergebnisse abfragen.

## <a name="6-get-inference-results"></a>6. Abholen von Rückschlussergebnissen

Sie benötigen `resultId`, um Ergebnisse zu erhalten. `resultId` wird aus dem Antwortheader beim Übermitteln der Rückschlussanforderung erhalten. [Diese Seite](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector-v1-1-preview/operations/GetDetectionResult) enthält Anweisungen zum Abfragen der Rückschlussergebnisse. 

Eine Beispiel-Antwort sieht folgendermaßen aus:

```json
 {
        "resultId": "663884e6-b117-11ea-b3de-0242ac130004",
        "summary": {
          "status": "READY",
          "errors": [],
          "variableStates": [
            {
              "variable": "ad_input",
              "filledNARatio": 0,
              "effectiveCount": 26,
              "startTime": "2019-04-01T00:00:00Z",
              "endTime": "2019-04-01T00:25:00Z",
              "errors": []
            },
            {
              "variable": "ad_ontimer_output",
              "filledNARatio": 0,
              "effectiveCount": 26,
              "startTime": "2019-04-01T00:00:00Z",
              "endTime": "2019-04-01T00:25:00Z",
              "errors": []
            },
            // more variables
          ],
          "setupInfo": {
            "source": "https://aka.ms/AnomalyDetector/MVADSampleData",
            "startTime": "2019-04-01T00:15:00Z",
            "endTime": "2019-04-01T00:40:00Z"
          }
        },
        "results": [
          {
            "timestamp": "2019-04-01T00:15:00Z",
            "errors": [
              {
                "code": "InsufficientHistoricalData",
                "message": "historical data is not enough."
              }
            ]
          },
          // more results
          {
            "timestamp": "2019-04-01T00:20:00Z",
            "value": {
              "contributors": [],
              "isAnomaly": false,
              "severity": 0,
              "score": 0.17805261260751692
            }
          },
          // more results
          {
            "timestamp": "2019-04-01T00:27:00Z",
            "value": {
              "contributors": [
                {
                  "contributionScore": 0.0007775013367514271,
                  "variable": "ad_ontimer_output"
                },
                {
                  "contributionScore": 0.0007989604079048129,
                  "variable": "ad_series_init"
                },
                {
                  "contributionScore": 0.0008900927229851369,
                  "variable": "ingestion"
                },
                {
                  "contributionScore": 0.008068144477478554,
                  "variable": "cpu"
                },
                {
                  "contributionScore": 0.008222036467507165,
                  "variable": "data_in_speed"
                },
                {
                  "contributionScore": 0.008674941549594993,
                  "variable": "ad_input"
                },
                {
                  "contributionScore": 0.02232242629793674,
                  "variable": "ad_output"
                },
                {
                  "contributionScore": 0.1583773213660846,
                  "variable": "flink_last_ckpt_duration"
                },
                {
                  "contributionScore": 0.9816531517495176,
                  "variable": "data_out_speed"
                }
              ],
              "isAnomaly": true,
              "severity": 0.42135109874230336,
              "score": 1.213510987423033
            }
          },
          // more results
        ]
      }
```

Die Antwort enthält den Ergebnisstatus, Variableninformationen, Rückschlussparameter und Rückschlussergebnisse.

* `variableStates` listet die Informationen der einzelnen Variablen in der Rückschlussanforderung auf.
* `setupInfo` ist der Anforderungstext, der für diesen Rückschluss übermittelt wird.
* `results` enthält die Erkennungsergebnisse. Es gibt drei typische Arten von Erkennungsergebnissen.
    1. Fehlercode `InsufficientHistoricalData`. Dies geschieht in der Regel nur mit den ersten Zeitstempeln, da das Modell den Datenrückschluss fensterbasiert durchführt und Verlaufsdaten benötigt, um eine Entscheidung zu treffen. Für die ersten Zeitstempel gibt es nicht genügend Verlaufsdaten, sodass kein Rückschluss auf sie ausgeführt werden kann. In diesem Fall kann die Fehlermeldung ignoriert werden.
    1. `"isAnomaly": false` gibt an, dass der aktuelle Zeitstempel keine Anomalie ist.
        * `severity ` gibt den relativen Schweregrad der Anomalie an; für normale Daten ist er immer 0.
        * `score` ist die Rohausgabe des Modells, für das das Modell eine Entscheidung trifft, die auch für normale Datenpunkte nicht-null sein kann.
    1. `"isAnomaly": true` gibt eine Anomalie zum aktuellen Zeitstempel an.
        * `severity ` gibt den relativen Schweregrad der Anomalie an; für abnormale Daten ist er immer größer als 0.
        * `score` ist die Rohausgabe des Modells, für das das Modell eine Entscheidung trifft. `severity` ist ein abgeleiteter Wert von `score`. Jeder Datenpunkt verfügt über einen `score`.
        * `contributors` ist eine Liste, die den Beitragsscore der einzelnen Variablen enthält. Höhere Beitragsscores deuten auf eine höhere Wahrscheinlichkeit der Grundursache hin. Diese Liste wird häufig zum Interpretieren von Anomalien sowie zum Diagnostizieren der Grundursachen verwendet.

> [!NOTE]
> Ein häufiger Fehler ist, alle Datenpunkte mit `isAnomaly`=`true` als Anomalien zu verwenden. Dies kann zu viele False Positives liefern.
> Sie sollten sowohl `isAnomaly` als auch `severity` (oder `score`) verwenden, um Anomalien zu erkennen, die nicht schwerwiegend sind, und (optional) die Gruppierung verwenden, um die Dauer der Anomalien zu überprüfen, um zufälliges Rauschen unterdrücken. Informationen zum Unterschied zwischen `severity` und `score` finden Sie in den [FAQ](../concepts/best-practices-multivariate.md#faq) im Dokument mit bewährten Methoden.

## <a name="next-steps"></a>Nächste Schritte

* [Bewährte Methoden: Empfohlene Vorgehensweisen für die Verwendung der APIs zur multivariaten Anomalieerkennung](../concepts/best-practices-multivariate.md)
* [Schnellstart: Verwenden der Clientbibliothek für die multivariate Anomalieerkennung](../quickstarts/client-libraries-multivariate.md)