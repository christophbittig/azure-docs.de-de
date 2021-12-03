---
title: Was ist automatisiertes maschinelles Lernen? AutoML
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Azure Machine Learning mithilfe des automatisierten maschinellen Lernens und von Ihnen bereitgestellter Parameter und Kriterien ein Modell generieren kann.
services: machine-learning
ms.service: machine-learning
ms.subservice: automl
ms.topic: conceptual
author: cartacioS
ms.author: sacartac
ms.date: 10/21/2021
ms.custom: automl
ms.openlocfilehash: 16d96eb508725e22bc1956a8b78d003f1512487b
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132518658"
---
# <a name="what-is-automated-machine-learning-automl"></a>Was ist automatisiertes maschinelles Lernen (AutoML)?

Automatisiertes maschinelles Lernen, auch als automatisiertes ML oder AutoML bezeichnet, ist der Prozess der Automatisierung von zeitaufwändigen, iterativen Aufgaben bei der Entwicklung eines Machine Learning-Modells. Es versetzt Data Scientists, Analysten und Entwickler in die Lage, ML-Modelle mit hoher Skalierbarkeit, Effizienz und Produktivität zu erstellen und gleichzeitig die Modellqualität zu erhalten. Automatisiertes maschinelles Lernen in Azure Machine Learning basiert auf einem Durchbruch der [Microsoft Research-Abteilung](https://www.microsoft.com/research/project/automl/).

Die Entwicklung traditioneller Machine Learning-Modelle ist ressourcenintensiv und erfordert viel Fachwissen und Zeit, um Dutzende von Modellen zu erstellen und zu vergleichen. Mit automatisiertem maschinellem Lernen verkürzen Sie die Zeit, die benötigt wird, um produktionsbereite ML-Modelle mit großer Leichtigkeit und Effizienz zu erhalten.

<a name="parity"></a>

## <a name="ways-to-use-automl-in-azure-machine-learning"></a>Möglichkeiten zur Verwendung des automatisierten maschinellen Lernens in Azure Machine Learning

Azure Machine Learning bietet die folgenden beiden Möglichkeiten für die Arbeit mit automatisiertem maschinellen Lernen. In den folgenden Abschnitten finden Sie Informationen zur [Featureverfügbarkeit bei beiden Verfahren](#parity).

* Für Kunden mit Programmiererfahrung: [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro).  Erste Schritte mit dem [Tutorial: Vorhersagen von Preisen für Taxifahrten mit automatisiertem maschinellen Lernen](tutorial-auto-train-models.md).

* Für Kunden mit begrenzten oder keinen Programmiererfahrungen: Azure Machine Learning Studio unter [https://ml.azure.com](https://ml.azure.com/).  Erste Schritte mit folgenden Tutorials:
    * [Tutorial: Erstellen eines Klassifizierungsmodells mit automatisiertem maschinellem Lernen in Azure Machine Learning](tutorial-first-experiment-automated-ml.md).
    *  [Tutorial: Vorhersage des Bedarfs mithilfe von automatisiertem maschinellem Lernen](tutorial-automated-ml-forecast.md)

### <a name="experiment-settings"></a>Einstellungen für das Experiment 

Mit den folgenden Einstellungen können Sie Ihr Experiment für automatisiertes maschinelles Lernen konfigurieren. 

| |Python SDK|Die Studioweboberfläche|
|----|:----:|:----:|
|**Aufteilen der Daten in Trainings-/Validierungssätze**| ✓|✓
|**Unterstützt Aufgaben für maschinelles Lernen: Klassifizierung, Regression und Vorhersage**| ✓| ✓
|**Unterstützt Aufgaben für maschinelles Sehen (Vorschau): Bildklassifizierung, Objekterkennung und Instanzsegmentierung**| ✓| 
|**Optimiert auf Grundlage der primären Metrik**| ✓| ✓
|**Unterstützt Azure ML-Compute als Computeziel** | ✓|✓
|**Konfigurieren des Vorhersagehorizonts, der Zielverzögerungen und des rollierenden Fensters**|✓|✓
|**Festlegen der Beendigungskriterien** |✓|✓ 
|**Festlegen gleichzeitiger Iterationen**| ✓|✓
|**Löschen von Spalten**| ✓|✓
|**Blockieren von Algorithmen**|✓|✓
|**Kreuzvalidierung im Vergleich** |✓|✓
|**Unterstützt das Training für Azure Databricks-Cluster**| ✓|
|**Anzeigen der Namen der technischen Features**|✓|
|**Featurezusammenfassung**| ✓|
|**Featurisierung für Feiertage**|✓|
|**Ausführlichkeitsgrade der Protokolldatei**| ✓|

### <a name="model-settings"></a>Modelleinstellungen

Diese Einstellungen können auf das beste Modell als Ergebnis Ihres Experiments für das automatisierte maschinelle Lernen angewendet werden.

| |Python SDK|Die Studioweboberfläche|
|----|:----:|:----:|
|**Registrierung, Bereitstellung, Erklärbarkeit des besten Modells**| ✓|✓|
|**Aktivieren von Abstimmungsensemble- und Stapelensemble-Modellen**| ✓|✓|
|**Anzeigen des besten Modells auf der Basis von nicht primärer Metrik**|✓||
|**Aktivieren/Deaktivieren der Kompatibilität des ONNX-Modells**|✓||
|**Testen des Modells** | ✓| ✓ (Vorschau)|

### <a name="run-control-settings"></a>Ausführen der Steuerelementeinstellungen

Mit diesen Einstellungen können Sie Ihre Experimentausführungen und die Ausführungen der ihnen untergeordneten Elemente überprüfen und steuern. 

| |Python SDK|Die Studioweboberfläche|
|----|:----:|:----:|
|**Ausführen der Zusammenfassungstabelle**| ✓|✓|
|**Abbrechen von Ausführungen und untergeordneten Ausführungen**| ✓|✓|
|**Abrufen von Schutzmaßnahmen**| ✓|✓|
|**Anhalten und Fortsetzen von Ausführungen**| ✓| |

## <a name="when-to-use-automl-classification-regression-forecasting--computer-vision"></a>Anwendungsfälle für automatisiertes maschinelles Lernen: Klassifizierung, Regression, Vorhersage und maschinelles Sehen

Sie arbeiten mit automatisiertem ML, wenn Sie möchten, dass Azure Machine Learning mit der von Ihnen angegebenen Zielmetrik ein Modell für Sie trainiert und optimiert. Automatisiertes ML demokratisiert den Entwicklungsprozess eines Machine Learning-Modells und befähigt seine Benutzer, unabhängig von deren Data Science-Kenntnissen, eine durchgängige Machine Learning-Pipeline für jedes Problem zu bestimmen.

Data Scientists, Analysten und Entwickler aus den verschiedensten Branchen können automatisiertes ML für Folgendes verwenden:
+ Implementieren von ML-Lösungen ohne umfangreiche Programmierkenntnisse
+ Sparen von Zeit und Ressourcen
+ Nutzen von bewährten Methoden aus der Data Science
+ Bereitstellen flexibler Problemlösungen

### <a name="classification"></a>Klassifizierung

Die Klassifizierung ist ein gängiger Machine Learning-Task. Klassifizierung ist eine Art des überwachten Lernens, bei der Modelle anhand von Trainingsdaten lernen und diese Erkenntnisse auf neue Daten anwenden. Azure Machine Learning bietet Featurebereitstellungen speziell für diese Aufgaben, z. B. Textfeaturizer für Deep Neural Network zur Klassifizierung. Erfahren Sie mehr über die [Optionen für die Featurebereitstellung](how-to-configure-auto-features.md#featurization). 

Das Hauptziel von Klassifizierungsmodellen besteht darin, auf der Grundlage der Erkenntnisse aus den Trainingsdaten vorherzusagen, in welche Kategorien neue Daten fallen werden. Zu den gängigen Klassifizierungsbeispielen gehören Betrugserkennung, Handschrifterkennung und Objekterkennung. Weitere Informationen und ein Beispiel finden Sie unter [Erstellen eines Klassifizierungsmodells mit automatisiertem maschinellem Lernen](tutorial-first-experiment-automated-ml.md).

Weitere Beispiele für Klassifizierung und automatisiertes Machine Learning finden Sie in den folgenden Python-Notebooks: [Fraud Detection](https://github.com/Azure/azureml-examples/blob/main/python-sdk/tutorials/automl-with-azureml/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb) (Betrugserkennung), [Marketing Prediction](https://github.com/Azure/azureml-examples/blob/main/python-sdk/tutorials/automl-with-azureml/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) (Marketingprognose) und [Newsgroup Data Classification](https://github.com/Azure/azureml-examples/tree/main/python-sdk/tutorials/automl-with-azureml/classification-text-dnn) (Klassifizierung von Diskussionsgruppendaten)

### <a name="regression"></a>Regression

Ähnlich der Klassifizierung sind Regressionsaufgaben auch ein gängiger überwachter Lerntask. Azure Machine Learning bietet [spezielle Featurisierungen für diesen Task](how-to-configure-auto-features.md#featurization).

Anders als bei der Klassifizierung, bei der die vorhergesagten Ausgabewerte kategorisch sind, sagen Regressionsmodelle numerische Ausgabewerte auf der Grundlage unabhängiger Vorhersagefaktoren voraus. Bei der Regression besteht das Ziel darin, die Beziehung zwischen diesen unabhängigen Vorhersagevariablen herzustellen, indem geschätzt wird, wie eine Variable die anderen beeinflusst. Beispiel: Der Fahrzeugpreis basierend auf Merkmalen wie Kraftstoffverbrauch, Sicherheitseinstufung, usw. Hier erhalten Sie weitere Informationen und ein Beispiel für die [Regression mit automatisiertem maschinellen Lernen](tutorial-auto-train-models.md).

Weitere Beispiele für Regression und automatisiertes Machine Learning für Vorhersagen finden Sie in den folgenden Python-Notebooks: [CPU-Leistungsvorhersage](https://github.com/Azure/azureml-examples/tree/main/python-sdk/tutorials/automl-with-azureml/regression-explanation-featurization), 

### <a name="time-series-forecasting"></a>Zeitreihenvorhersagen

Die Erstellung von Vorhersagen ist ein integraler Bestandteil jedes Unternehmens, unabhängig davon, ob es sich um Einnahmen, Lagerbestände, Umsätze oder Kundennachfrage handelt. Sie können automatisiertes maschinelles Lernen verwenden, um verschiedene Techniken und Ansätze zu kombinieren. Außerdem erhalten Sie dabei eine beliebte und hochwertige Zeitreihenprognose. Hier erhalten Sie weitere Informationen zur Vorgehensweise: [Automatisches Trainieren eines Modells für die Zeitreihenprognose](how-to-auto-train-forecast.md). 

Automatisierte Zeitreihenexperimente werden als multivariate Regressionsprobleme behandelt. Zeitreihenwerte aus der Vergangenheit werden „pivotiert“ und dienen so zusammen mit anderen Vorhersageelementen als zusätzliche Dimensionen für den Regressor. Dieser Ansatz hat im Gegensatz zu klassischen Zeitreihenmethoden den Vorteil, dass mehrere kontextbezogene Variablen und deren Beziehungen zueinander beim Training auf natürliche Weise integriert werden. Beim automatisierten maschinellen Lernen wird ein zwar einfaches, aber häufig in interne Verzweigungen unterteiltes Modell für alle Elemente im Dataset und in den Vorhersagehorizonten erlernt. Dadurch sind mehr Daten verfügbar, um Modellparameter zu schätzen, und die Generalisierung von unbekannten Reihen wird möglich.

Die erweiterte Vorhersagekonfiguration umfasst Folgendes:
* Feiertagserkennung und Erstellen zusätzlicher Merkmale (Featurization)
* Zeitreihen und DNN-Lernmodule (Auto-ARIMA, Prophet, ForecastTCN)
* Unterstützung vieler Modelle mithilfe von Gruppierungen
* Kreuzvalidierung mit rollierendem Ursprung (Rolling Origin Validation)
* Konfigurierbare Verzögerungen (Lags)
* Aggregierte Zeitfenstermerkmale (Rolling Window Features)


Weitere Beispiele für Regression und automatisiertes Machine Learning für Vorhersagen finden Sie in den folgenden Python-Notebooks: [Sales Forecasting](https://github.com/Azure/azureml-examples/blob/main/python-sdk/tutorials/automl-with-azureml/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb) (Verkaufsprognose), [Demand Forecasting](https://github.com/Azure/azureml-examples/blob/main/python-sdk/tutorials/automl-with-azureml/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb) (Nachfrageprognose) und [Beverage Production Forecast](https://github.com/Azure/azureml-examples/blob/main/python-sdk/tutorials/automl-with-azureml/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb) (Getränkeproduktionsprognose).

### <a name="computer-vision-preview"></a>Maschinelles Sehen (Vorschau)

> [!IMPORTANT]
> Dieses Feature ist zurzeit als öffentliche Preview verfügbar. Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Automatisiertes ML für Bilder (Vorschau) unterstützt maschinelles Sehen und ermöglicht die einfache Erstellung von Modellen, die mit Bilddaten für Szenarien wie Bildklassifizierung und Objekterkennung trainiert wurden. 

Diese Funktion ermöglicht Folgendes: 
 
* Nahtlose Integration mit der Funktion zur [Azure Machine Learning-Datenbeschriftung](./how-to-create-image-labeling-projects.md)
* Verwenden von beschrifteten Daten zum Generieren von Bildmodellen
* Optimieren der Modellleistung durch Angabe des Modellalgorithmus und durch Abstimmen der Hyperparameter 
* Herunterladen oder Bereitstellen des resultierenden Modells als Webdienst in Azure Machine Learning 
* Operationalisierung im großen Stil durch Nutzung der [MLOps](concept-model-management-and-deployment.md)- und [ML Pipelines](concept-ml-pipelines.md)-Funktionen von Azure Machine Learning 

Das Erstellen von AutoML-Modellen für Aufgaben des maschinellen Sehens wird über das Azure ML Python-SDK unterstützt Auf die resultierenden Versuchsausführungen, Modelle und Ergebnisse kann über die Benutzeroberfläche von Azure Machine Learning Studio zugegriffen werden.

Erfahren Sie, wie Sie das [AutoML-Training für Modelle des maschinellen Sehens einrichten](how-to-auto-train-image-models.md).

![Beispiele für Aufgaben des maschinellen Sehens. Bild von: http://cs231n.stanford.edu/slides/2021/lecture_15.pdf ](./media/concept-automated-ml/automl-computer-vision-tasks.png) Bild von: http://cs231n.stanford.edu/slides/2021/lecture_15.pdf

Automatisiertes ML für Bilder unterstützt die folgenden Aufgaben für maschinelles Sehen: 

Aufgabe | BESCHREIBUNG
----|----
Bildklassifizierung mit mehreren Klassen | Aufgaben, bei denen ein Bild nur mit einer einzelnen Bezeichnung aus einer Reihe von Klassen klassifiziert wird – z. B. wird jedes Bild entweder als Bild einer „Katze“ oder eines „Hundes“ oder einer „Ente“ klassifiziert.
Bildklassifizierung mit mehreren Beschriftungen | Aufgaben, bei denen ein Bild eine oder mehrere Beschriftungen aus einer Reihe von Beschriftungen besitzen könnte – z. B. könnte ein Bild sowohl mit „Katze“ als auch mit „Hund“ beschriftet werden.
Objekterkennung| Aufgaben zur Identifizierung von Objekten in einem Bild und Lokalisierung der einzelnen Objekte mit einem Begrenzungsrahmen, z. B. die Lokalisierung aller Hunde und Katzen in einem Bild und Zeichnen eines Begrenzungsrahmens um jedes Objekt.
Instanzsegmentierung | Aufgaben zur Identifizierung von Objekten in einem Bild auf Pixelebene, indem ein Polygon um jedes Objekt im Bild gezeichnet wird.

## <a name="how-automated-ml-works"></a>Funktionsweise von automatisiertem ML

Während des Trainings erstellt Azure Machine Learning parallel eine Reihe von Pipelines, die unterschiedliche Algorithmen und Parameter für Sie ausprobieren. Der Dienst durchläuft die ML-Algorithmen iterativ im Zusammenspiel mit der jeweiligen Featureauswahl, wobei für jede Iteration ein Modell mit einer Trainingsbewertung erzeugt wird. Je höher die Bewertung ist, desto besser wird das Modell als „passend“ für Ihre Daten angesehen.  Die Ausführung wird beendet, sobald die im Experiment definierten Beendigungskriterien erreicht werden. 

Mithilfe von **Azure Machine Learning** können Sie automatisierte ML-Trainingsexperimente mit den folgenden Schritten entwerfen und ausführen:

1. **Bestimmen Sie das zu lösende ML-Problem**: Klassifizierung, Vorhersage, Regression oder maschinelles Sehen (Vorschau).

1. **Wählen Sie, ob Sie das Python SDK oder die Studioweboberfläche verwenden möchten**: Erfahren Sie mehr über die Parität zwischen dem [Python SDK und der Studioweboberfläche](#parity).

   * Wenn Sie nur über eingeschränkte oder gar keine Erfahrung mit Code verfügen, testen Sie die Studioweboberfläche von Azure Machine Learning unter [https://ml.azure.com](https://ml.azure.com/).  
   * Informieren Sie sich als Python-Entwickler über das [Python SDK von Azure Machine Learning](how-to-configure-auto-train.md). 
    
1. **Angeben der Quelle und des Formats der bezeichneten Trainingsdaten**: NumPy-Arrays oder Pandas-Datenrahmen.

1. **Konfigurieren des Computeziels für das Modelltraining**, z.B. [lokaler Computer, Azure Machine Learning Computes, Remote-VMs oder Azure Databricks](how-to-set-up-training-targets.md).

1. **Konfigurieren der automatisierten Machine Learning-Parameter**, die die Anzahl der Iterationen über verschiedene Modelle, die Hyperparametereinstellungen, erweiterte Vorverarbeitung/Featurebereitstellung und die Metriken bestimmen, die bei der Ermittlung des besten Modells zu berücksichtigen sind.  
1. **Übermitteln der Trainingsausführung.**

1. **Überprüfen der Ergebnisse** 

Dieser Prozess wird anhand des folgenden Diagramms veranschaulicht. 
![Automatisiertes maschinelles Lernen](./media/concept-automated-ml/automl-concept-diagram2.png)


Sie können die protokollierten Ausführungsinformationen auch untersuchen. Sie enthalten die während der Ausführung [erfassten Metriken](how-to-understand-automated-ml.md). Bei der Trainingsausführung wird ein serialisiertes Python-Objekt (`.pkl`-Datei) generiert, das die Vorabverarbeitung des Modells und der Daten enthält.

Obwohl die Modellerstellung automatisiert ist, können Sie auch [ermitteln, wie wichtig oder relevant Features für die generierten Modelle sind](how-to-configure-auto-train.md#explain).

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="local-remote"></a>

## <a name="guidance-on-local-vs-remote-managed-ml-compute-targets"></a>Leitfaden zu lokal verwalteten ML-Computezielen im Vergleich zu remote verwalteten ML-Computezielen

Die Weboberfläche für automatisiertes ML verwendet immer ein [Remotecomputeziel](concept-compute-target.md).  Wenn Sie jedoch das Python SDK verwenden, wählen Sie entweder ein lokales Computeziel oder ein Remotecomputeziel für automatisiertes ML-Training aus.

* **Lokales Computeziel:** Das Training erfolgt auf Ihrem lokalen Computer oder VM-Compute. 
* **Remotecomputeziel:** Das Training erfolgt auf Azure Machine Learning-Computeclustern.  

### <a name="choose-compute-target"></a>Auswählen eines Computeziels
Berücksichtigen Sie die folgenden Faktoren bei der Auswahl Ihres Computeziels:

 * **Wählen Sie ein lokales Computeziel aus:** Wenn Ihr Szenario anfängliche Untersuchungen oder Demos mithilfe weniger Daten und kurzen Trainingsprozessen (d. h. Sekunden oder wenige Minuten pro untergeordnete Ausführung) umfasst, kann sich das Training auf Ihrem lokalen Computer als besser erweisen.  Es gibt keine Einrichtungszeit, die Infrastrukturressourcen (Ihr Computer oder Ihre VM) sind sofort verfügbar.
 * **Wählen Sie ein Remote-ML-Computecluster aus**: Wenn Sie wie beim Erstellen von Modellen für Produktionstraining Training mit größeren Datasets durchführen, die längere Trainingszeiträume erfordern, bietet ein Remotecomputeziel eine bessere Gesamtdauer, da `AutoML` Trainingsprozesse über Clusterknoten hinweg parallelisiert. Auf einem Remotecomputeziel werden etwa 1,5 Minuten pro untergeordnete Ausführung durch die Startzeit für die interne Infrastruktur hinzugefügt. Außerdem werden weitere Minuten für die Clusterinfrastruktur addiert, wenn die VMs noch nicht ausgeführt werden.

### <a name="pros-and-cons"></a>Vor- und Nachteile
Wägen Sie die folgenden Vor- und Nachteile gegeneinander ab, wenn Sie zwischen einem lokalen und einem Remotecomputeziel entscheiden.

|  | Vorteile  |Nachteile  |
|---------|---------|---------|---------|
|**Lokales Computeziel** |  <li> Keine Startzeit für die Umgebung   | <li>  Teilmenge der Features<li>  Keine Parallelisierung der Ausführungen <li> Schlechter für große Datenmengen <li>Kein Datenstreaming während des Trainings <li>  Keine DNN-basierte Featurisierung <li> Nur Python SDK |
|**Remote-ML-Computecluster**|  <li> Alle Features <li> Parallelisierung untergeordneter Ausführungen <li>   Unterstützung großer Datasets<li>  DNN-basierte Featurisierung <li>  Dynamische Skalierbarkeit von Computeclustern nach Bedarf <li> Webbenutzeroberfläche zur Verwendung ohne Programmiererfahrung  |  <li> Startzeit für Clusterknoten <li> Startzeit für jede untergeordnete Ausführung    |

### <a name="feature-availability"></a>Verfügbarkeit von Funktionen 

Wie in der folgenden Tabelle gezeigt stehen Ihnen mehr Features zur Verfügung, wenn Sie Remotecompute verwenden. 

| Funktion                                                    | Remote | Lokal | 
|------------------------------------------------------------|--------|-------|
| Datenstreaming (Unterstützung großer Datenmengen, bis zu 100 GB)          | ✓      |       | 
| DNN-BERT-basierte Textfeaturisierung und -training             | ✓      |       |
| Direkt einsatzbereite GPU-Unterstützung (Training und Rückschlüsse)        | ✓      |       |
| Unterstützung von Bildklassifizierung und Bezeichnungen                  | ✓      |       |
| Auto-ARIMA-, Prophet- und ForecastTCN-Modelle für Vorhersagen | ✓      |       | 
| Mehrere parallele Ausführungen/Iterationen                       | ✓      |       |
| Erstellen von Modellen mit Interpretierbarkeit in der AutoML Studio-Webbenutzeroberfläche      | ✓      |       |
| Anpassung des Feature Engineering in der AutoML Studio-Webbenutzeroberfläche| ✓      |       |
| Azure ML-Hyperparameteroptimierung                             | ✓      |       |
| Unterstützung von Azure ML-Pipelineworkflows                         | ✓      |       |
| Fortsetzen einer Ausführung                                             | ✓      |       |
| Vorhersagen                                                | ✓      | ✓     |
| Erstellen und Ausführen von Experimenten in Notebooks                    | ✓      | ✓     |
| Registrieren und Visualisieren der Informationen und Metriken von Experimenten auf der Benutzeroberfläche | ✓      | ✓     |
| Schutzmaßnahmen für Daten                                            | ✓      | ✓     |

## <a name="training-validation-and-test-data"></a>Trainieren, Überprüfen und Testen von Daten 

Mit automatisierten ML Sie die **Trainingsdaten** zum Trainieren ML Und Sie können angeben, welche Art von Modellvalidierung sie ausführen soll. Automatisiertes ML führt die Modellvalidierung im Rahmen des Trainings aus. Das heißt, dass automatisiertes ML **Validierungsdaten** verwendet, um Modellhyperparameter basierend auf dem angewendeten Algorithmus zu optimieren, um die beste Kombination zu finden, die am besten zu den Trainingsdaten passt. Allerdings werden dieselben Validierungsdaten für jede Iteration der Optimierung verwendet, was zu einer Voreingenommenheit bei der Modellauswertung führt, da das Modell sich weiter verbessert und an die Validierungsdaten anpasst. 

Um zu bestätigen, dass diese Verzerrungen nicht auf das endgültige empfohlene Modell angewendet werden, unterstützt das automatisierte ML die Verwendung von **Testdaten**, um das endgültige Modell zu bewerten, das automatisiertes ML am Ende Ihres Experiments empfiehlt. Wenn Sie Testdaten als Teil Ihrer Experimentkonfiguration für automatisiertes maschinelles Lernen bereitstellen, wird dieses empfohlene Modell standardmäßig am Ende des Experiments (Vorschauversion) getestet. 

>[!IMPORTANT]
> Das Testen Ihrer Modelle mit einem Testdatensatz zur Bewertung der generierten Modelle ist eine Previewfunktion. Diese Funktion ist eine [experimentelle](/python/api/overview/azure/ml/#stable-vs-experimental) Previewfunktion, die jederzeit geändert werden kann.

Erfahren Sie, wie Sie [Experimente für automatisiertes maschinelles Lernen für die Verwendung von Testdaten (Vorschauversion) mit dem SDK](how-to-configure-cross-validation-data-splits.md#provide-test-data-preview) oder mit [Azure Machine Learning](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment) Studio konfigurieren.

Sie können auch [alle vorhandenen automatisierten ML-Modelle (Vorschauversion) testen](how-to-configure-auto-train.md#test-existing-automated-ml-model), einschließlich Modellen aus untergeordneten Läufen, indem Sie Ihre eigenen Testdaten bereitstellen oder einen Teil Ihrer Trainingsdaten abgesehen haben. 

## <a name="feature-engineering"></a>Featureentwicklung

Beim Feature Engineering werden Domänenkenntnisse der Daten zum Erstellen von Features verwendet, mit denen ML-Algorithmen besser lernen können. In Azure Machine Learning werden für das Feature Engineering Skalierungs- und Normalisierungstechniken angewendet. Zusammen werden diese Techniken und das Feature Engineering als Featurisierung bezeichnet.

Bei automatisierten Machine Learning-Experimenten wird die Featurisierung automatisch angewendet, sie kann aber auch basierend auf Ihren Daten angepasst werden. [Weitere Informationen zur enthaltenen Featurebereitstellung](how-to-configure-auto-features.md#featurization).  

> [!NOTE]
> Die Schritte zur Featurebereitstellung bei automatisiertem maschinellen Lernen (Featurenormalisierung, Behandlung fehlender Daten, Umwandlung von Text in numerische Daten usw.) werden Teil des zugrunde liegenden Modells. Bei Verwendung des Modells für Vorhersagen werden die während des Trainings angewendeten Schritte zur Featurebereitstellung automatisch auf Ihre Eingabedaten angewendet.

### <a name="automatic-featurization-standard"></a>Automatische Featurisierung (Standard)

In jedem automatisierten Machine Learning-Experiment werden Ihre Daten automatisch skaliert oder normalisiert, damit die Algorithmen gut funktionieren. Während des Modelltrainings wird eine der folgenden Skalierungs- oder Normalisierungstechniken auf jedes Modell angewendet. Erfahren Sie, wie AutoML dazu beiträgt, die [Überanpassung und Unausgeglichenheit von Daten](concept-manage-ml-pitfalls.md) in Ihren Modellen zu vermeiden.

|Skalierung und Verarbeitung| BESCHREIBUNG |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Standardisieren von Features durch Entfernen des Mittelwerts und Skalierung auf Einheitenvarianz.  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Transformieren von Features durch Skalieren jedes Features anhand des Mindest- und Maximalwerts dieser Spalte.  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Skalieren jedes Features anhand seines maximalen Absolutwerts. |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) | Skaliert Features nach ihrem Quantilbereich. |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Lineare Dimensionalitätsreduzierung unter Verwendung von Singulärwertzerlegung der Daten, um sie auf einen niedrigeren Dimensionsbereich zu projizieren. |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Diese Transformation führt Dimensionalitätsreduzierung durch abgeschnittene Singulärwertzerlegung (SVD) durch. Im Gegensatz zu PCA zentriert diese Schätzfunktion die Daten nicht vor der Berechnung der Singulärwertzerlegung, d. h., dass scipy.sparse-Matrizen effizient verwendet werden können. |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Jede Stichprobe (d.h. jede Zeile der Datenmatrix) mit mindestens einer Komponente ungleich Null wird unabhängig von anderen Stichproben neu skaliert, sodass ihre Norm (l1 oder l2) gleich eins ist. |

### <a name="customize-featurization"></a>Anpassen der Featurisierung

Es stehen auch weitere Feature Engineering-Techniken wie etwa Codierung und Transformationen zur Verfügung. 

Diese Einstellung kann aktiviert werden über:

+ Azure Machine Learning Studio: Aktivieren Sie die **Automatische Merkmalserstellung** im Abschnitt **Konfigurationsausführung** [mit diesen Schritten](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

+ Python SDK: Geben Sie `"feauturization": 'auto' / 'off' / 'FeaturizationConfig'` in Ihrem Objekt vom Typ [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) an. Weitere Informationen zur [Aktivierung der Featurisierung](how-to-configure-auto-features.md). 

## <a name="ensemble-models"></a><a name="ensemble"></a> Ensemblemodelle

Automatisiertes Machine Learning unterstützt Ensemblemodelle, die standardmäßig aktiviert sind. Das Lernen mit Ensembles verbessert die Ergebnisse des maschinellen Lernens und die Vorhersageleistung, da nicht einzelne Modelle verwendet, sondern mehrere Modelle kombiniert werden. Die Ensemble-Iterationen erfolgen als abschließende Iterationen Ihrer Ausführung. Automatisiertes Machine Learning verwendet die beiden Ensemble-Methoden „voting“ (Abstimmen) und „stacking“ (Stapeln) gemeinsam, um Modelle zu kombinieren:

* **Voting**: Trifft Vorhersagen auf Grundlage des gewichteten Durchschnitts der vorhergesagten Klassenwahrscheinlichkeiten (für Klassifizierungsaufgaben) oder auf Grundlage der vorhergesagten Regressionsziele (für Regressionsaufgaben).
* **Stacking**: Stacking kombiniert heterogene Modelle und trainiert ein Metamodell, basierend auf der Ausgabe der einzelnen Modelle. Die aktuellen Standardmetamodelle sind LogisticRegression für Klassifizierungsaufgaben und ElasticNet für Regressions-/Vorhersageaufgaben.

Der [Caruana-Algorithmus für die Ensembleauswahl](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf) mit sortierter Ensembleinitialisierung wird verwendet, um zu entscheiden, welche Modell innerhalb des Ensembles verwendet werden sollen. Generell initialisiert dieser Algorithmus das Ensemble mit bis zu fünf Modellen mit den besten Einzelbewertungen und überprüft, ob diese Modelle innerhalb des 5 %-Schwellenwerts der besten Bewertung liegen, um ein schlechtes Ausgangsensemble zu vermeiden. Dann wird für jede Ensemble-Iteration ein neues Modell zum vorhandenen Ensemble hinzugefügt, und die resultierende Bewertung wird berechnet. Wenn ein neues Modell die vorhandene Ensemblebewertung verbessert hat, wird das Ensemble so aktualisiert, dass es das neue Modell aufnimmt.

Informationen zum Ändern der Standard-Ensembleeinstellungen beim automatisierten Machine Learning finden Sie unter [Gewusst wie:](how-to-configure-auto-train.md#ensemble).

<a name="use-with-onnx"></a>

## <a name="automl--onnx"></a>AutoML und ONNX

Mit Azure Machine Learning können Sie automatisiertes ML verwenden, um ein Python-Modell zu erstellen und in das ONNX-Format zu konvertieren. Sobald die Modelle im ONNX-Format vorliegen, können sie auf einer Vielzahl von Plattformen und Geräten ausgeführt werden. Erfahren Sie mehr über das [Beschleunigen von ML-Modellen mit ONNX](concept-onnx.md).

Informationen zum Konvertieren in das ONNX-Format finden Sie in [diesem Jupyter Notebook-Beispiel](https://github.com/Azure/azureml-examples/tree/main/python-sdk/tutorials/automl-with-azureml/classification-bank-marketing-all-features). Erfahren Sie, welche [Algorithmen in ONNX unterstützt werden](how-to-configure-auto-train.md#supported-models).

Die ONNX-Runtime unterstützt auch C#, sodass Sie das erstellte Modell automatisch in Ihren C#-Apps verwenden können, ohne es neu codieren oder die Netzwerklatenzen in Kauf nehmen zu müssen, die REST-Endpunkte mit sich bringen. Erfahren Sie mehr über die [Verwendung eines AutoML ONNX-Modells in einer .NET-Anwendung mit ML.NET](./how-to-use-automl-onnx-model-dotnet.md) und das [Rückschließen von ONNX-Modellen mit der C#-API für die ONNX-Runtime](https://onnxruntime.ai/docs/api/csharp-api.html). 

## <a name="next-steps"></a>Nächste Schritte

Es gibt mehrere Ressourcen, um Sie mit AutoML vertraut zu machen. 

### <a name="tutorials-how-tos"></a>Tutorials/Anleitungen
Tutorials sind einführende End-to-End-Beispiele für AutoML-Szenarien.
+ **Für das codegesteuerte Verfahren** befolgen Sie das [Tutorial: Trainieren eines Regressionsmodells mit AutoML und Python](tutorial-auto-train-models.md).

+ **Für die Herangehensweise mit wenig/keinem Code** sehen Sie sich das [Tutorial: Trainieren eines Klassifizierungsmodells mit AutoML ohne Schreiben von Code in Azure Machine Learning Studio](tutorial-first-experiment-automated-ml.md) an.

+ Informationen zum **Verwenden von AutoML zum Trainieren von Modellen für maschinelles Sehen** finden Sie im [Tutorial: Trainieren eines Objekterkennungsmodells (Vorschau) mit AutoML und Python](tutorial-auto-train-image-models.md).
   
Anleitungen bieten zusätzliche Einzelheiten zu den Funktionen des automatisierten maschinellen Lernens. Beispiel: 

+ Konfigurieren der Einstellungen für automatische Trainingsexperimente
    + [Ohne Code in Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md) 
    + [Mit dem Python SDK](how-to-configure-auto-train.md)

+  Erfahren Sie, wie Sie [Vorhersagemodelle mit Zeitreihendaten trainieren](how-to-auto-train-forecast.md).

+  Erfahren Sie, wie Sie [Modelle für maschinelles Sehen mit Python trainieren](how-to-auto-train-image-models.md).
   
### <a name="jupyter-notebook-samples"></a>Jupyter Notebook-Beispiele 

Überprüfen Sie detaillierte Codebeispiele und Anwendungsfälle im [GitHub-Notebook-Repository für Beispiele zum automatisierten maschinellen Lernen](https://github.com/Azure/azureml-examples/tree/main/python-sdk/tutorials/automl-with-azureml).

### <a name="python-sdk-reference"></a>Referenz zum Python SDK

Vertiefen Sie Ihre Kenntnisse über SDK-Entwurfsmuster und Klassenspezifikationen mit der [AutoML-Klassenreferenzdokumentation](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig). 

> [!Note]
> Die Funktionen des automatisierten maschinellen Lernens sind auch in anderen Lösungen von Microsoft verfügbar: [ML.NET](/dotnet/machine-learning/automl-overview), [HDInsight](../hdinsight/spark/apache-spark-run-machine-learning-automl.md), [Power BI](/power-bi/service-machine-learning-automated) und [SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)