---
title: Übersicht über Machine Learning – Azure HDInsight
description: Übersicht über Machine Learning-Optionen mit Big Data für Cluster in Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: a911e468443fe49bb1edc18af3d3412edc8eb8cc
ms.sourcegitcommit: 16580bb4fbd8f68d14db0387a3eee1de85144367
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/24/2021
ms.locfileid: "112678214"
---
# <a name="machine-learning-on-hdinsight"></a>Machine Learning in HDInsight

HDInsight ermöglicht Machine Learning mit Big Data und bietet so die Möglichkeit, wertvolle Erkenntnisse aus großen Mengen (Petabytes, ja sogar Exabytes) strukturierter, unstrukturierter und sich schnell verändernder Daten zu gewinnen. HDInsight umfasst mehrere Optionen für maschinelles Lernen: SparkML und Apache Spark MLlib, Apache Hive und das Microsoft Cognitive Toolkit.

## <a name="sparkml-and-mllib"></a>SparkML und MLlib

[HDInsight Spark](spark/apache-spark-overview.md) ist ein in Azure gehostetes Angebot von [Apache Spark](https://spark.apache.org/), einem einheitlichen Open-Source-Framework für die Parallelverarbeitung von Daten, das die arbeitsspeicherinterne Verarbeitung unterstützt, um die Leistung von Big Data-Analysen zu steigern. Die Spark-Verarbeitungs-Engine ist auf Geschwindigkeit, einfache Nutzung und anspruchsvolle Analysen ausgelegt. Dank seiner verteilten In-Memory-Datenverarbeitungsfunktionen eignet sich Spark besonders für iterative Algorithmen beim maschinellen Lernen und für Graphberechnungen. Es gibt zwei skalierbare Bibliotheken für maschinelles Lernen, die algorithmische Modellierungsfunktionen in dieser verteilten Umgebung bereitstellen: MLlib und SparkML. MLlib enthält die Original-API, die auf Grundlage von RDDs erstellt wurde. SparkML ist ein neueres Paket, das eine API auf höherer Ebene bereitstellt, die auf Dataframes basiert, um ML-Pipelines zu erstellen. SparkML unterstützt noch nicht alle Funktionen von MLlib, ersetzt jedoch MLlib als Machine Learning-Standardbibliothek in Spark.

Die Microsoft Machine Learning-Bibliothek für Apache Spark ist [MMLSpark](https://github.com/Azure/mmlspark). Diese Bibliothek ist darauf ausgelegt, die Produktivität von Datenanalysten mit Spark zu steigern, die Experimentierrate zu erhöhen und innovative Verfahren für das maschinelle Lernen, einschließlich Deep Learning, in sehr großen Datasets zu nutzen. MMLSpark bietet zusätzlich zu den SparkML-APIs auf niedriger Ebene eine weitere Ebene beim Erstellen skalierbarer ML-Modelle, z.B. zum Indizieren von Zeichenfolgen, Umwandeln von Daten in ein von Algorithmen für maschinelles Lernen erwartetes Layout und Zusammenstellen von Featurevektoren. Die MMLSpark-Bibliothek vereinfacht diese und andere gängige Aufgaben zum Erstellen von Modellen in PySpark.

## <a name="azure-machine-learning-and-apache-hive"></a>Azure Machine Learning und Apache Hive

Azure Machine Learning verfügt über Tools zur Modellierung von Predictive Analytics-Lösungen und einen vollständig verwalteten Dienst, über den Sie Ihre Vorhersagemodelle als sofort nutzbare Webdienste bereitstellen können. Azure Machine Learning ist eine vollständige Predictive Analytics-Lösung in der Cloud, die Sie zum Erstellen, Testen, Operationalisieren und Verwalten von Vorhersagemodelle verwenden können. Wählen Sie aus einer großen Algorithmusbibliothek aus, verwenden Sie ein webbasiertes Studio zum Erstellen von Modellen, und stellen Sie Ihr Modell ganz einfach als Webdienst bereit.

:::image type="content" source="./media/hdinsight-machine-learning-overview/azure-machine-learning.png" alt-text="Microsoft Azure Machine Learning – Übersicht" border="false":::

Sie können Features für Daten in einem HDInsight Hadoop-Cluster mit [Hive-Abfragen](/azure/architecture/data-science-process/create-features-hive) erstellen. Bei der *Featureentwicklung* wird versucht, die Vorhersageleistung der Lernalgorithmen durch Erstellen von Merkmalen aus Rohdaten zu verbessern und mit diesen den Lernprozess zu vereinfachen. Mithilfe des [Import Data-Moduls](../machine-learning/classic/import-data.md) können Sie HiveQL-Abfragen von Azure Machine Learning Studio (klassisch) ausführen und auf in Hive verarbeitete und in Blob Storage gespeicherte Daten zugreifen.

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

[Deep Learning](https://www.microsoft.com/en-us/research/group/dltc/) ist ein Nebenzweig des maschinellen Lernens, der neuronale Netze verwendet, die an die biologischen Prozesse des menschlichen Gehirns angelehnt sind. Viele Forscher sehen Deep Learning als vielversprechenden Ansatz zur Weiterentwicklung der künstlichen Intelligenz. Beispiele für Deep Learning sind Übersetzungssysteme für gesprochene Sprache, Bilderkennungssysteme und Machine Reasoning (maschinelles logisches Schlussfolgern).

Um seine eigenen Bestrebungen im Deep Learning voranzutreiben, hat Microsoft das kostenlose und bedienfreundliche Open-Source-[Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) entwickelt. Dieses Toolkit wird von einer Vielzahl verschiedener Microsoft-Produkte sowie von Firmen weltweit verwendet, die Deep Learning skaliert bereitstellen müssen, sowie von Studenten, die an den neuesten Algorithmen und Methoden interessiert sind.

## <a name="see-also"></a>Weitere Informationen

### <a name="scenarios"></a>Szenarien

* [Apache Spark mit Machine Learning: Analysieren von Gebäudetemperaturen mithilfe von Spark in HDInsight und HVAC-Daten](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark mit Machine Learning: Vorhersage von Lebensmittelkontrollergebnissen mithilfe von Spark in HDInsight](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Generieren von Filmempfehlungen mithilfe von Apache Mahout](hadoop/apache-hadoop-mahout-linux-mac.md)
* [Apache Hive und Azure Machine Learning](/azure/architecture/data-science-process/create-features-hive)
* [Apache Hive und Azure Machine Learning End-to-End](/azure/architecture/data-science-process/hive-walkthrough)
* [Machine Learning mit Apache Spark in HDInsight](/azure/architecture/data-science-process/spark-overview)

### <a name="deep-learning-resources"></a>Deep Learning-Ressourcen

* [Verwenden des Microsoft Cognitive Toolkit-Modells für intensives Lernen mit einem Azure HDInsight Spark-Cluster](spark/apache-spark-microsoft-cognitive-toolkit.md)
* [Deep Learning- und KI-Frameworks für die Data Science Virtual Machine (DSVM)](../machine-learning/data-science-virtual-machine/dsvm-tools-deep-learning-frameworks.md)
