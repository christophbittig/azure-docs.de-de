---
title: 'Tutorial: Übersetzer mit kognitivem Service'
description: Erfahren Sie, wie Sie den Übersetzer in Azure Synapse Analytics verwenden können.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: ''
ms.date: 11/02/2021
author: ruixinxu
ms.author: ruxu
ms.custom: ignite-fall-2021
ms.openlocfilehash: e0ae2aea56e7bfa255daba30c99b49bc66b5b4b5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131101655"
---
# <a name="tutorial-translator-with-cognitive-service"></a>Tutorial: Übersetzer mit kognitivem Service

[ Übersetzer ](../../cognitive-services/Translator/index.yml) ist ein [Azure Cognitive Service](../../cognitive-services/index.yml), mit dem Sie Sprachübersetzungen und andere sprachbezogene Operationen durchführen können. In diesem Tutorial lernen Sie, wie Sie mit [ Übersetzer ](../../cognitive-services/Translator/index.yml) verwenden, um intelligente, mehrsprachige Lösungen auf Azure Synapse Analytics zu erstellen.

Dieses Tutorial demonstriert die Verwendung von Translator mit [MMLSpark](https://github.com/Azure/mmlspark):

> [!div class="checklist"]
> - Text übersetzen
> - Transliteration von Text
> - Sprache erkennen
> - Satz unterbrechen
> - Wörterbuchsuche
> - Beispiel aus dem Wörterbuch

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.


## <a name="prerequisites"></a>Voraussetzungen

- [Azure Synapse Analytics-Arbeitsbereich](../get-started-create-workspace.md) mit einem als Standardspeicher konfigurierten Azure Data Lake Storage Gen2-Speicherkonto. Für das hier verwendete Data Lake Storage Gen2-Dateisystem müssen Sie über die Rolle *Mitwirkender an Storage-Blobdaten* verfügen.
- Spark-Pool in Ihrem Azure Synapse Analytics-Arbeitsbereich. Ausführliche Informationen finden Sie unter [Erstellen eines Spark-Pools in Azure Synapse](../quickstart-create-sql-pool-studio.md).
- Vorkonfigurationsschritte im Tutorial [Konfigurieren von Cognitive Services in Azure Synapse](tutorial-configure-cognitive-services-synapse.md)

## <a name="get-started"></a>Erste Schritte
Öffnen Sie Synapse Studio, und erstellen Sie ein neues Notebook. Importieren Sie zunächst [MMLSpark](https://github.com/Azure/mmlspark). 

```python
import mmlspark
from mmlspark.cognitive import *
from notebookutils import mssparkutils
from pyspark.sql.functions import col, flatten
```

## <a name="configure-translator"></a>Übersetzer konfigurieren

Verwenden Sie den verknüpften Übersetzer, den Sie in den [Vorkonfigurationsschritten](tutorial-configure-cognitive-services-synapse.md) konfiguriert haben. 

```python
cognitive_service_name = "<Your linked service for translator>"
```

## <a name="translate-text"></a>Text übersetzen
Der Kernvorgang des Textübersetzungsdiensts besteht im Übersetzen von Text. 

```python
df = spark.createDataFrame([
  (["Hello, what is your name?", "Bye"],)
], ["text",])

translate = (Translate()
    .setLinkedService(cognitive_service_name)
    .setTextCol("text")
    .setToLanguage(["zh-Hans", "fr"])
    .setOutputCol("translation")
    .setConcurrency(5))

display(translate
      .transform(df)
      .withColumn("translation", flatten(col("translation.translations")))
      .withColumn("translation", col("translation.text"))
      .select("translation"))
```

### <a name="expected-results"></a>Erwartete Ergebnisse

```json
["你好，你叫什么名字？","Bonjour, quel est votre nom?","再见","Au revoir"]
```

## <a name="transliterate-text"></a>Text transliterieren

Transliteration ist der Vorgang der Umwandlung eines Worts oder Ausdrucks aus der Schrift (Alphabet) einer Sprache in eine andere, ausgehend von der phonetischen Ähnlichkeit.

```python
transliterateDf =  spark.createDataFrame([
  (["こんにちは", "さようなら"],)
], ["text",])

transliterate = (Transliterate()
    .setLinkedService(cognitive_service_name)
    .setLanguage("ja")
    .setFromScript("Jpan")
    .setToScript("Latn")
    .setTextCol("text")
    .setOutputCol("result"))

display(transliterate
    .transform(transliterateDf)
    .withColumn("text", col("result.text"))
    .withColumn("script", col("result.script"))
    .select("text", "script"))
```

### <a name="expected-results"></a>Erwartete Ergebnisse

|text|script|
|--|--|
|"["Kon'nichiwa","sayonara"]"|"["Latn","Latn"]"|

## <a name="detect-language"></a>Sprache erkennen
Wenn Sie wissen, dass Sie eine Übersetzung benötigen, die Sprache des Texts aber nicht kennen, der an den Textübersetzungsdienst gesendet werden soll, können Sie den Sprachenerkennungsvorgang verwenden. 

```python
detectDf =  spark.createDataFrame([
  (["Hello, what is your name?"],)
], ["text",])

detect = (Detect()
    .setLinkedService(cognitive_service_name)
    .setTextCol("text")
    .setOutputCol("result"))

display(detect
    .transform(detectDf)
    .withColumn("language", col("result.language"))
    .select("language"))
```

### <a name="expected-results"></a>Erwartete Ergebnisse

```json
"["en"]"
```

## <a name="break-sentence"></a>Satz unterbrechen

Erkennt die Positionierung von Satzgrenzen in einem Textabschnitt.

```python
bsDf =  spark.createDataFrame([
  (["Hello, what is your name?"],)
], ["text",])

breakSentence = (BreakSentence()
    .setLinkedService(cognitive_service_name)
    .setTextCol("text")
    .setOutputCol("result"))

display(breakSentence
    .transform(bsDf)
    .withColumn("sentLen", flatten(col("result.sentLen")))
    .select("sentLen"))
```

### <a name="expected-results"></a>Erwartete Ergebnisse

```json
"[25]"
```

## <a name="dictionary-lookup-alternate-translations"></a>Wörterbuchsuche (Alternative Übersetzungen)
Mit dem Endpunkt können Sie alternative Übersetzungen für ein Wort oder einen Satz abrufen. 

```python
dictDf = spark.createDataFrame([
  (["fly"],)
], ["text",])

dictionaryLookup = (DictionaryLookup()
    .setLinkedService(cognitive_service_name)
    .setFromLanguage("en")
    .setToLanguage("es")
    .setTextCol("text")
    .setOutputCol("result"))

display(dictionaryLookup
    .transform(dictDf)
    .withColumn("translations", flatten(col("result.translations")))
    .withColumn("normalizedTarget", col("translations.normalizedTarget"))
    .select("normalizedTarget"))
```

### <a name="expected-results"></a>Erwartete Ergebnisse

|normalizedTarget|
|----|
|"["volar","mosca","operan","pilotar","moscas","marcha"]"|

## <a name="dictionary-examples-translations-in-context"></a>Wörterbuchbeispiele (Übersetzungen im Kontext)

Nachdem Sie eine Wörterbuchsuche durchgeführt haben, können Sie den Ausgangstext und die Übersetzung an den Endpunkt dictionary/examples übergeben, um eine Liste von Beispielen zu erhalten, die beide Begriffe im Kontext eines Satzes oder einer Phrase zeigen.

```python
dictDf = spark.createDataFrame([
  ([("fly", "volar")],)
], ["textAndTranslation",])

dictionaryExamples = (DictionaryExamples()
    .setLinkedService(cognitive_service_name)
    .setFromLanguage("en")
    .setToLanguage("es")
    .setTextAndTranslationCol("textAndTranslation")
    .setOutputCol("result"))

display(dictionaryExamples
    .transform(dictDf)
    .withColumn("examples", flatten(col("result.examples")))
    .select("examples"))
```

### <a name="expected-results"></a>Erwartete Ergebnisse

```json

[{"sourcePrefix":"I mean, for a guy who could ","sourceSuffix":".","targetPrefix":"Quiero decir, para un tipo que podía ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"Now it's time to make you ","sourceSuffix":".","targetPrefix":"Ahora es hora de que te haga ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"One happy thought will make you ","sourceSuffix":".","targetPrefix":"Uno solo te hará ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"They need machines to ","sourceSuffix":".","targetPrefix":"Necesitan máquinas para ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"That should really ","sourceSuffix":".","targetPrefix":"Eso realmente debe ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"It sure takes longer when you can't ","sourceSuffix":".","targetPrefix":"Lleva más tiempo cuando no puedes ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"I have to ","sourceSuffix":" home in the morning.","targetPrefix":"Tengo que ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":" a casa por la mañana."},{"sourcePrefix":"You taught me to ","sourceSuffix":".","targetPrefix":"Me enseñaste a ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"I think you should ","sourceSuffix":" with the window closed.","targetPrefix":"Creo que debemos ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":" con la ventana cerrada."},{"sourcePrefix":"They look like they could ","sourceSuffix":".","targetPrefix":"Parece que pudieran ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"But you can ","sourceSuffix":", for instance?","targetPrefix":"Que puedes ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":", por ejemplo."},{"sourcePrefix":"At least until her kids can be able to ","sourceSuffix":".","targetPrefix":"Al menos hasta que sus hijos sean capaces de ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"I thought you could ","sourceSuffix":".","targetPrefix":"Pensé que podías ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"I was wondering what it would be like to ","sourceSuffix":".","targetPrefix":"Me preguntaba cómo sería ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"But nobody else can ","sourceSuffix":".","targetPrefix":"Pero nadie puede ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."}]
```
## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Beenden Sie alle verbundenen Sitzungen (Notebooks), um sicherzustellen, dass die Spark-Instanz heruntergefahren wird. Der Pool wird heruntergefahren, wenn die im Apache Spark-Pool angegebene **Leerlaufzeit** erreicht wird. Sie können auch auf der Statusleiste am oberen Rand des Notebooks die Option **Sitzung beenden** auswählen.

![screenshot-showing-stop-session](./media/tutorial-build-applications-use-mmlspark/stop-session.png)

## <a name="next-steps"></a>Nächste Schritte

* [Beispiele für Synapse-Notebooks](https://github.com/Azure-Samples/Synapse/tree/main/MachineLearning) 
* [MMLSpark-GitHub-Repository](https://github.com/Azure/mmlspark)
