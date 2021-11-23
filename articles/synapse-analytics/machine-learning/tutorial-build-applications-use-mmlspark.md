---
title: 'Tutorial: Erstellen von Machine Learning-Anwendungen mithilfe von Synapse Machine Learning'
description: Hier erfahren Sie, wie Sie mithilfe von Synapse Machine Learning Machine Learning-Anwendungen in Azure Synapse Analytics erstellen.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: ''
ms.date: 03/08/2021
author: ruixinxu
ms.author: ruxu
ms.openlocfilehash: 03d7aec55e7a6146346ebbcc746ecbbd75b81d3f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132318482"
---
# <a name="tutorial-build-machine-learning-applications-using-synapse-machine-learning"></a>Tutorial: Erstellen von Machine Learning-Anwendungen mithilfe von Synapse Machine Learning

In diesem Artikel erfahren Sie, wie Sie mithilfe von Synapse Machine Learning ([SynapseML](https://github.com/microsoft/SynapseML)) Machine Learning-Anwendungen erstellen. Mit SynapseML wird die Lösung für verteiltes maschinelles Lernen von Apache Spark erweitert, indem zahlreiche Deep Learning- und Data Science-Tools wie [Azure Cognitive Services](../../cognitive-services/big-data/cognitive-services-for-big-data.md), [OpenCV](https://opencv.org/), [LightGBM](https://github.com/Microsoft/LightGBM) usw. hinzugefügt werden.  SynapseML ermöglicht die Erstellung leistungsstarker und hochgradig skalierbarer Vorhersage- und Analysemodelle auf der Grundlage verschiedener Spark-Datenquellen.
Synapse Spark bietet integrierte SynapseML-Bibliotheken, u. a.:

- [Vowpal Wabbit:](https://github.com/VowpalWabbit/vowpal_wabbit) Bibliotheksdienste für maschinelles Lernen, um die Textanalyse (etwa Stimmungsanalyse) in Tweets zu ermöglichen
- [Cognitive Services in Spark:](https://arxiv.org/abs/1810.08744) Dient zum Kombinieren der Azure Cognitive Services-Features in SparkML-Pipelines, um einen Lösungsentwurf für kognitive Datenmodellierungsdienste wie Anomalieerkennung abzuleiten.
- [LightBGM:](https://github.com/Microsoft/LightGBM) LightGBM ist ein Gradient Boosting-Framework, das baumstrukturbasierte Lernalgorithmen nutzt. Es ist für Verteilung und höhere Effizienz vorgesehen.
- Bedingtes KNN-Modell: Skalierbare KNN-Modelle mit bedingten Abfragen
- HTTP in Spark: Ermöglicht die Orchestrierung verteilter Microservices bei der Integration von Spark und HTTP-basiertem Zugriff.

In diesem Tutorial werden Beispiele für die Verwendung von Azure Cognitive Services in SynapseML für Folgendes behandelt: 

- Textanalyse: Ermitteln der Stimmung (oder Laune) in einer Reihe von Sätzen
- Maschinelles Sehen: Abrufen der Tags (Einwortbeschreibungen) die einem Satz von Bildern zugeordnet sind
- Bing-Bildersuche: Suchen im Web nach Bildern im Zusammenhang mit einer Abfrage in natürlicher Sprache
- Anomalieerkennung: Erkennen von Anomalien innerhalb von Zeitreihendaten

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen 

- [Azure Synapse Analytics-Arbeitsbereich](../get-started-create-workspace.md) mit einem als Standardspeicher konfigurierten Azure Data Lake Storage Gen2-Speicherkonto. Für das hier verwendete Data Lake Storage Gen2-Dateisystem müssen Sie über die Rolle *Mitwirkender an Storage-Blobdaten* verfügen.
- Spark-Pool in Ihrem Azure Synapse Analytics-Arbeitsbereich. Ausführliche Informationen finden Sie unter [Erstellen eines Spark-Pools in Azure Synapse](../get-started-analyze-spark.md).
- Vorkonfigurationsschritte im Tutorial [Konfigurieren von Cognitive Services in Azure Synapse](./tutorial-configure-cognitive-services-synapse.md)


## <a name="get-started"></a>Erste Schritte
Importieren Sie zunächst SynapseML, und konfigurieren Sie Dienstschlüssel. 

```python
import synapse.ml

from synapse.ml.cognitive import *
from notebookutils import mssparkutils

# A general Cognitive Services key for Text Analytics and Computer Vision (or use separate keys that belong to each service)
cognitive_service_key = mssparkutils.credentials.getSecret("ADD_YOUR_KEY_VAULT_NAME", "ADD_YOUR_SERVICE_KEY","ADD_YOUR_KEY_VAULT_LINKED_SERVICE_NAME") 
# A Bing Search v7 subscription key
bingsearch_service_key = mssparkutils.credentials.getSecret("ADD_YOUR_KEY_VAULT_NAME", "ADD_YOUR_BING_SEARCH_KEY","ADD_YOUR_KEY_VAULT_LINKED_SERVICE_NAME")
# An Anomaly Dectector subscription key
anomalydetector_key = mssparkutils.credentials.getSecret("ADD_YOUR_KEY_VAULT_NAME", "ADD_YOUR_ANOMALY_KEY","ADD_YOUR_KEY_VAULT_LINKED_SERVICE_NAME")


```


## <a name="text-analytics-sample"></a>Beispiel für Textanalyse

Der [Textanalyse](../../cognitive-services/text-analytics/index.yml)-Dienst bietet mehrere Algorithmen zum Extrahieren intelligenter Erkenntnisse aus Text. Beispielsweise können wir die Stimmung eines Eingabetexts ermitteln. Der Dienst gibt eine Bewertung zwischen 0,0 und 1,0 zurück, wobei niedrige Scores auf eine negative Stimmung und hohe Scores auf eine positive Stimmung hindeuten. In diesem Beispiel werden drei einfache Sätze verwendet, und es wird jeweils die Stimmung zurückgegeben.

```python
from pyspark.sql.functions import col

# Create a dataframe that's tied to it's column names
df_sentences = spark.createDataFrame([
  ("I am so happy today, its sunny!", "en-US"), 
  ("this is a dog", "en-US"), 
  ("I am frustrated by this rush hour traffic!", "en-US") 
], ["text", "language"])

# Run the Text Analytics service with options
sentiment = (TextSentiment()
    .setTextCol("text")
    .setLocation("eastasia") # Set the location of your cognitive service
    .setSubscriptionKey(cognitive_service_key)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language"))

# Show the results of your text query in a table format

display(sentiment.transform(df_sentences).select("text", col("sentiment")[0].getItem("sentiment").alias("sentiment")))
```

### <a name="expected-results"></a>Erwartete Ergebnisse

|text | Stimmung|
|--|--|
| Ich bin frustriert über diesen Berufsverkehr. | Negativ |
| Dies ist ein Hund. | Neutral |
| Ich bin heute so glücklich, die Sonne scheint! | Positiv |

## <a name="computer-vision-sample"></a>Beispiel für maschinelles Sehen
[Maschinelles Sehen](../../cognitive-services/computer-vision/index.yml) analysiert Bilder, um die Struktur zu identifizieren, wie z. B. Gesichter, Objekte und Beschreibungen in natürlicher Sprache. In diesem Beispiel markieren wir das folgende Bild. Tags sind Einwortbeschreibungen der Dinge im Bild, wie z. B. erkennbare Objekte, Personen, Szenen und Aktionen.


![image](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg)

```python
# Create a dataframe with the image URL
df_images = spark.createDataFrame([
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg", )
    ], ["image", ])

# Run the Computer Vision service. Analyze Image extracts information from/about the images.
analysis = (AnalyzeImage()
    .setLocation("eastasia") # Set the location of your cognitive service
    .setSubscriptionKey(cognitive_service_key)
    .setVisualFeatures(["Categories","Color","Description","Faces","Objects","Tags"])
    .setOutputCol("analysis_results")
    .setImageUrlCol("image")
    .setErrorCol("error"))

# Show the results of what you wanted to pull out of the images.
display(analysis.transform(df_images).select("image", "analysis_results.description.tags"))
```
### <a name="expected-results"></a>Erwartete Ergebnisse

|image | tags|
|--|--|
| `https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg` | [Skaten, Person, Mann, draußen, Reiten, Sport, Skateboard, jung, Brett, Hemd, Luft, Park, Junge, Seite, Springen, Rampe, Trick, tun, Fliegen] |

## <a name="bing-image-search-sample"></a>Beispiel für Bing-Bildersuche
Die [Bing-Bildersuche](../../cognitive-services/bing-image-search/overview.md) durchsucht das Web nach Bildern im Zusammenhang mit einer Abfrage in natürlicher Sprache eines Benutzers. In diesem Beispiel verwenden wir eine Textabfrage, die nach Bildern mit Zitaten sucht. Sie gibt eine Liste mit Bild-URLs zurück, die Fotos im Zusammenhang mit unserer Abfrage enthalten.


```python
from pyspark.ml import PipelineModel

# Number of images Bing will return per query
imgsPerBatch = 2
# A list of offsets, used to page into the search results
offsets = [(i*imgsPerBatch,) for i in range(10)]
# Since web content is our data, we create a dataframe with options on that data: offsets
bingParameters = spark.createDataFrame(offsets, ["offset"])

# Run the Bing Image Search service with our text query
bingSearch = (BingImageSearch()
    .setSubscriptionKey(bingsearch_service_key)
    .setOffsetCol("offset")
    .setQuery("Martin Luther King Jr. quotes")
    .setCount(imgsPerBatch)
    .setOutputCol("images"))

# Transformer that extracts and flattens the richly structured output of Bing Image Search into a simple URL column
getUrls = BingImageSearch.getUrlTransformer("images", "url")
pipeline_bingsearch = PipelineModel(stages=[bingSearch, getUrls])

# Show the results of your search: image URLs
res_bingsearch = pipeline_bingsearch.transform(bingParameters)
display(res_bingsearch.dropDuplicates())
```

### <a name="expected-results"></a>Erwartete Ergebnisse

|image | 
|--|
|`http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-16.jpg` |
|`http://www.scrolldroll.com/wp-content/uploads/2017/06/6-25.png` |
| `http://abettertodaymedia.com/wp-content/uploads/2017/01/86783bd7a92960aedd058c91a1d10253.jpg`|
| `https://weneedfun.com/wp-content/uploads/2016/05/martin-luther-king-jr-quotes-11.jpg` |
| `http://www.sofreshandsogreen.com/wp-content/uploads/2012/01/martin-luther-king-jr-quote-sofreshandsogreendotcom.jpg` |
| `https://cdn.quotesgram.com/img/72/57/1104209728-martin_luther_king_jr_quotes_16.jpg` |
| `http://comicbookandbeyond.com/wp-content/uploads/2019/05/Martin-Luther-King-Jr.-Quotes.jpg` |
| `https://exposingthepain.files.wordpress.com/2015/01/martin-luther-king-jr-quotes-08.png` |
| `https://topmemes.me/wp-content/uploads/2020/01/Top-10-Martin-Luther-King-jr.-Quotes2-1024x538.jpg` |
| `http://img.picturequotes.com/2/581/580286/dr-martin-luther-king-jr-quote-1-picture-quote-1.jpg` |
| `http://parryz.com/wp-content/uploads/2017/06/Amazing-Martin-Luther-King-Jr-Quotes.jpg` |
| `http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes1.jpg` |
| `https://lessonslearnedinlife.net/wp-content/uploads/2020/05/Martin-Luther-King-Jr.-Quotes-2020.jpg` |
| `https://quotesblog.net/wp-content/uploads/2015/10/Martin-Luther-King-Jr-Quotes-Wallpaper.jpg` |

## <a name="anomaly-detector-sample"></a>Beispiel für Anomalieerkennung

Die [Anomalieerkennung](../../cognitive-services/anomaly-detector/index.yml) eignet sich hervorragend zum Erkennen von Unregelmäßigkeiten in ihren Zeitreihendaten In diesem Beispiel verwenden wir den Dienst, um Anomalien in der gesamten Zeitreihe zu suchen.

```python
from pyspark.sql.functions import lit

# Create a dataframe with the point data that Anomaly Detector requires
df_timeseriesdata = spark.createDataFrame([
    ("1972-01-01T00:00:00Z", 826.0),
    ("1972-02-01T00:00:00Z", 799.0),
    ("1972-03-01T00:00:00Z", 890.0),
    ("1972-04-01T00:00:00Z", 900.0),
    ("1972-05-01T00:00:00Z", 766.0),
    ("1972-06-01T00:00:00Z", 805.0),
    ("1972-07-01T00:00:00Z", 821.0),
    ("1972-08-01T00:00:00Z", 20000.0), # anomaly
    ("1972-09-01T00:00:00Z", 883.0),
    ("1972-10-01T00:00:00Z", 898.0),
    ("1972-11-01T00:00:00Z", 957.0),
    ("1972-12-01T00:00:00Z", 924.0),
    ("1973-01-01T00:00:00Z", 881.0),
    ("1973-02-01T00:00:00Z", 837.0),
    ("1973-03-01T00:00:00Z", 9000.0) # anomaly
], ["timestamp", "value"]).withColumn("group", lit("series1"))

# Run the Anomaly Detector service to look for irregular data
anamoly_detector = (SimpleDetectAnomalies()
  .setSubscriptionKey(anomalydetector_key)
  .setLocation("eastasia")
  .setTimestampCol("timestamp")
  .setValueCol("value")
  .setOutputCol("anomalies")
  .setGroupbyCol("group")
  .setGranularity("monthly"))

# Show the full results of the analysis with the anomalies marked as "True"
display(anamoly_detector.transform(df_timeseriesdata).select("timestamp", "value", "anomalies.isAnomaly"))

```

### <a name="expected-results"></a>Erwartete Ergebnisse

|timestamp | value | isAnomaly |
|--|--|--|
| 1972-01-01T00:00:00Z|826.0|false|
|1972-02-01T00:00:00Z|799.0|false|
|1972-03-01T00:00:00Z|890.0|false|
|1972-04-01T00:00:00Z|900.0|false|
|1972-05-01T00:00:00Z|766.0|false|
|1972-06-01T00:00:00Z|805.0|false|
|1972-07-01T00:00:00Z|821.0|false|
|1972-08-01T00:00:00Z|20000.0|true|
|1972-09-01T00:00:00Z|883.0|false|
|1972-10-01T00:00:00Z|898.0|false|
|1972-11-01T00:00:00Z|957.0|false|
|1972-12-01T00:00:00Z|924.0|false|
|1973-01-01T00:00:00Z|881.0|false|
|1973-02-01T00:00:00Z|837.0|false|
|1973-03-01T00:00:00Z|9000.0|true|


## <a name="speech-to-text-sample"></a>Beispiel für Spracherkennung

Der [Spracherkennungsdienst](../../cognitive-services/speech-service/index-text-to-speech.yml) konvertiert Streams oder Dateien gesprochener Audiodaten in Text. In diesem Beispiel transkribieren wir eine Audiodatei in Text. 

```python
# Create a dataframe with our audio URLs, tied to the column called "url"
df = spark.createDataFrame([("https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav",)
                           ], ["url"])

# Run the Speech-to-text service to translate the audio into text
speech_to_text = (SpeechToTextSDK()
    .setSubscriptionKey(service_key)
    .setLocation("northeurope") # Set the location of your cognitive service
    .setOutputCol("text")
    .setAudioDataCol("url")
    .setLanguage("en-US")
    .setProfanity("Masked"))

# Show the results of the translation
display(speech_to_text.transform(df).select("url", "text.DisplayText"))
```
### <a name="expected-results"></a>Erwartete Ergebnisse

|url | DisplayText |
|--|--|
| `https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav` | Custom Speech provides tools that allow you to visually inspect the recognition quality of a model by comparing audio data with the corresponding recognition result from the custom speech portal. You can playback uploaded audio and determine if the provided recognition result is correct. This tool allows you to quickly inspect quality of Microsoft's baseline speech to text model or a trained custom model without having to transcribe any audio data.|

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Beenden Sie alle verbundenen Sitzungen (Notebooks), um sicherzustellen, dass die Spark-Instanz heruntergefahren wird. Der Pool wird heruntergefahren, wenn die im Apache Spark-Pool angegebene **Leerlaufzeit** erreicht wird. Sie können auch auf der Statusleiste am oberen Rand des Notebooks die Option **Sitzung beenden** auswählen.

![screenshot-showing-stop-session](./media/tutorial-build-applications-use-mmlspark/stop-session.png)

## <a name="next-steps"></a>Nächste Schritte

* [Beispiele für Synapse-Notebooks](https://github.com/Azure-Samples/Synapse/tree/main/MachineLearning) 
* [SynapseML-GitHub-Repository](https://github.com/microsoft/SynapseML)
