---
title: Trainieren von Vowpal Wabbit-Modellen
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie mit der Komponente „Train Vowpal Wabbit Model“ (Vowpal Wabbit-Modell trainieren) ein Machine Learning-Modell mit einer Vowpal Wabbit-Instanz erstellen.
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/02/2020
ms.openlocfilehash: 09128c5d25c45d7d8a35157ae2625a451fe16ddd
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566553"
---
# <a name="train-vowpal-wabbit-model"></a>Trainieren von Vowpal Wabbit-Modellen
In diesem Artikel wird beschrieben, wie Sie die Komponente **Train Vowpal Wabbit Model** (Vowpal Wabbit-Modell trainieren) im Azure Machine Learning-Designer verwenden, um mithilfe von Vowpal Wabbit ein Machine Learning-Modell zu erstellen.  

Formatieren Sie Ihre Eingabe gemäß den Anforderungen von Vowpal Wabbit, und bereiten Sie die Daten im erforderlichen Format vor, um Vowpal Wabbit für maschinelles Lernen zu verwenden. Verwenden Sie diese Komponente, um Vowpal Wabbit-Befehlszeilenargumente anzugeben. 

Beim Ausführen der Pipeline wird eine Vowpal Wabbit-Instanz zusammen mit den angegebenen Daten in die Experimentruntime geladen. Nach Abschluss des Trainings wird das Modell zurück in den Arbeitsbereich serialisiert. Sie können das Modell sofort verwenden, um Daten zu bewerten. 

Wenn Sie ein bereits vorhandenes Modell mit neuen Daten inkrementell trainieren möchten, stellen Sie eine Verbindung zwischen einem gespeicherten Modell und dem Eingabeport **Pre-trained Vowpal Wabbit model** (Vortrainiertes Vowpal Wabbit-Modell) von **Train Vowpal Wabbit Model** (Vowpal Wabbit-Modell trainieren) her, und fügen Sie die neuen Daten zum anderen Eingabeport hinzu.  

## <a name="what-is-vowpal-wabbit"></a>Was ist Vowpal Wabbit?  

Vowpal Wabbit (VW) ist ein schnelles paralleles Framework für maschinelles Lernen, das von Yahoo! Research für verteilte Verarbeitung entwickelt und später nach Windows portiert und von John Langford (Microsoft Research) für wissenschaftliche Verarbeitung in Parallelarchitekturen angepasst wurde.  

Wichtige Features von Vowpal Wabbit für maschinelles Lernen sind unter anderem stetiges Lernen (Onlinelernen), Verringern der Dimensionalität und interaktives Lernen. Vowpal Wabbit ist auch eine Lösung für Probleme, wenn die Modelldaten nicht in den Arbeitsspeicher passen.  

Die primären Benutzer von Vowpal Wabbit sind Data Scientists, die das Framework bereits für Machine-Learning-Tasks wie Klassifizierung, Regression, Themenmodellierung und Matrixfaktorisierung verwendet haben. Die Leistungsmerkmale des Azure-Wrappers für Vowpal Wabbit ähneln denen der lokalen Version sehr, sodass Sie die leistungsstarken Features und die native Leistung von Vowpal Wabbit nutzen und das trainierte Modell problemlos als operationalisierten Dienst veröffentlichen können.  

Die Komponente [Feature Hashing](feature-hashing.md) enthält ebenfalls von Vowpal Wabbit bereitgestellte Funktionen, mit denen Sie Textdatasets mithilfe eines Hashalgorithmus in binäre Features umwandeln können.  

## <a name="how-to-configure-vowpal-wabbit-model"></a>Konfigurieren eines Vowpal Wabbit-Modells  

In diesem Abschnitt wird erläutert, wie Sie ein neues Modell trainieren und neue Daten zu einem vorhandenen Modell hinzufügen.

Im Gegensatz zu anderen Komponenten im Designer gibt diese Komponente die Komponentenparameter an und trainiert außerdem das Modell. Wenn Sie über ein bereits vorhandenes Modell verfügen, können Sie es als optionale Eingabe hinzufügen, um das Modell inkrementell zu trainieren.

+ [Vorbereiten der Eingabedaten in einem der erforderlichen Formate](#prepare-the-input-data)
+ [Trainieren eines neuen Modells](#create-and-train-a-vowpal-wabbit-model)
+ [Inkrementelles Trainieren eines vorhandenen Modells](#retrain-an-existing-vowpal-wabbit-model)

### <a name="prepare-the-input-data"></a>Vorbereiten der Eingabedaten

Damit Sie ein Modell mit dieser Komponente trainieren können, muss das Eingabedataset aus einer einzelnen Textspalte in einem der beiden unterstützten Formate bestehen: **SVMLight** oder **VW**. Dies bedeutet nicht, dass Vowpal Wabbit nur Textdaten analysiert, sondern lediglich, dass die Features und Werte im erforderlichen Textdateiformat vorbereitet werden müssen.  

Die Daten können aus zwei Arten von Datasets gelesen werden: einem Dateidataset oder einem Tabellendataset. Beide Arten von Datasets müssen entweder im SVMLight- oder im VW-Format sein. Das Vowpal Wabbit-Datenformat hat den Vorteil, dass es kein Spaltenformat erfordert, wodurch sich Speicherplatz sparen lässt, wenn dünn besetzte Daten verarbeitet werden. Weitere Informationen zu diesem Format finden Sie auf der [Vowpal Wabbit-Wikiseite](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format).  

### <a name="create-and-train-a-vowpal-wabbit-model"></a>Erstellen und Trainieren eines Vowpal Wabbit-Modells

1. Fügen Sie Ihrem Experiment die Komponente **Train Vowpal Wabbit Model** (Vowpal Wabbit-Modell trainieren) hinzu. 
  
2. Fügen Sie das Trainingsdataset hinzu, und verbinden Sie es mit **Training data** (Trainingsdaten). Wenn es sich bei dem Trainingsdataset um ein Verzeichnis handelt, das die Trainingsdatendatei enthält, geben Sie den Namen der Trainingsdatendatei unter **Name of the training data file** (Name der Trainingsdatendatei) an. Wenn das Trainingsdataset eine einzelne Datei ist, lassen Sie das Feld **Name of the training data file** (Name der Trainingsdatendatei) leer.

3. Geben Sie im Textfeld **VW arguments** (VW-Argumente) die Befehlszeilenargumente für die ausführbare Datei für Vowpal Wabbit an.

     Beispielsweise können Sie *`–l`* hinzufügen, um die Lerngeschwindigkeit anzugeben, oder *`-b`* , um die Anzahl von Hashingbits anzugeben.  

     Weitere Informationen finden Sie im Abschnitt über [Vowpal Wabbit-Parameter](#supported-and-unsupported-parameters).  

4. **Name of the training data file** (Name der Trainingsdatendatei): Geben Sie den Namen der Datei an, die die Eingabedaten enthält. Dieses Argument wird nur verwendet, wenn es sich bei dem Trainingsdataset um ein Verzeichnis handelt.

5. **Specify file type** (Dateityp angeben): Geben Sie an, welches Format für Ihre Trainingsdaten verwendet wird. Vowpal Wabbit unterstützt die folgenden beiden Eingabedateiformate:  

    - **VW** ist das von Vowpal Wabbit verwendete interne Format. Ausführliche Informationen finden Sie auf der [Vowpal Wabbit-Wikiseite](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format). 
    - **SVMLight** ist ein von einigen anderen Machine-Learning-Tools verwendetes Format. 

6. **Output readable model file** (Lesbare Modelldatei ausgeben): Wählen Sie diese Option aus, wenn die Komponente das lesbare Modell in den Ausführungsaufzeichnungen speichern soll. Dieses Argument entspricht dem Parameter `--readable_model` in der VW-Befehlszeile.  

7. **Output inverted hash file** (Datei mit invertiertem Hash ausgeben): Wählen Sie diese Option aus, wenn die Komponente die invertierte Hashfunktion in einer Datei in den Ausführungsaufzeichnungen speichern soll. Dieses Argument entspricht dem Parameter `--invert_hash` in der VW-Befehlszeile.  

8. Übermitteln Sie die Pipeline.

### <a name="retrain-an-existing-vowpal-wabbit-model"></a>Neutrainieren eines vorhandenen Vowpal Wabbit-Modells

Vowpal Wabbit unterstützt inkrementelles Training durch Hinzufügen neuer Daten zu einem vorhandenen Modell. Es gibt zwei Möglichkeiten, ein vorhandenes Modell zum Neutrainieren zu erhalten:

+ Verwenden Sie die Ausgabe einer anderen **Train Vowpal Wabbit Model**-Komponente (Vowpal Wabbit-Modell trainieren) in derselben Pipeline.  
  
+ Suchen Sie in der Kategorie **Datasets** im linken Navigationsbereich des Designers nach einem gespeicherten Modell, und ziehen Sie es in die Pipeline.  

1. Fügen Sie Ihrer Pipeline die Komponente **Train Vowpal Wabbit Model** (Vowpal Wabbit-Modell trainieren) hinzu.  
2. Stellen Sie eine Verbindung zwischen dem zuvor trainierten Modell und dem Eingabeport **Pre-trained Vowpal Wabbit Model** (Vortrainiertes Vowpal Wabbit-Modell) der Komponente her.
3. Verbinden Sie die neuen Trainingsdaten mit dem Eingabeport **Training data** (Trainingsdaten) der Komponente.
4. Geben Sie im Parameterbereich von **Train Vowpal Wabbit Model** (Vowpal Wabbit-Modell trainieren) das Format der neuen Trainingsdaten an sowie den Namen der Trainingsdatendatei, wenn es sich beim Eingabedataset um ein Verzeichnis handelt.
5. Wählen Sie die Optionen **Output readable model file** (Lesbare Modelldatei ausgeben) und **Output inverted hash file** (Datei mit invertiertem Hash ausgeben) aus, wenn die entsprechenden Dateien in den Ausführungsaufzeichnungen gespeichert werden müssen.

6. Übermitteln Sie die Pipeline.  
7. Wählen Sie die Komponente aus und klicken Sie auf **Register dataset** (Dataset registrieren) im rechten Bereich der Registerkarte **Outputs+logs** (Ausgaben und Protokolle), um das aktualisierte Modell in Ihrem Azure Machine Learning-Arbeitsbereich beizubehalten.  Wenn Sie keinen neuen Namen angeben, wird das vorhandene gespeicherte Modell durch das aktualisierte Modell überschrieben.

## <a name="results"></a>Ergebnisse

+ Verwenden Sie zum Generieren von Bewertungen das Modul [Vowpal Wabbit-Modell bewerten](score-vowpal-wabbit-model.md).

> [!NOTE]
> Wenn Sie das trainierte Modell im Designer bereitstellen müssen, vergewissern Sie sich, dass in der Rückschlusspipeline die Option [Vowpal Wabbit-Modell bewerten](score-vowpal-wabbit-model.md) anstelle von **Modell bewerten** mit der Eingabe der [Komponente Webdienstausgabe](web-service-input-output.md) verbunden ist.

## <a name="technical-notes"></a>Technische Hinweise

Dieser Abschnitt enthält Implementierungsdetails, Tipps und Antworten auf häufig gestellte Fragen.

### <a name="advantages-of-vowpal-wabbit"></a>Vorteile von Vowpal Wabbit

Vowpal Wabbit bietet äußerst schnelles Lernen mithilfe von nichtlinearen Features wie N-Grammen.  

Das Framework verwendet Techniken für *Onlinelernen*, z. B. den stochastischen Gradientenabstieg (Stochastic Gradient Descent, SGD), um ein Modell datensatzweise anzupassen. Daher durchläuft Vowpal Wabbit Rohdaten sehr schnell und kann schneller einen guten Prädiktor entwickeln als die meisten anderen Modelle. Mit diesem Ansatz wird außerdem vermieden, dass alle Trainingsdaten in den Arbeitsspeicher gelesen werden müssen.  

Vowpal Wabbit konvertiert alle Daten in Hashes, d. h. nicht nur Textdaten, sondern auch andere Kategorievariablen. Die Verwendung von Hashes macht Suchvorgänge für Regressionsgewichtungen effizienter, was für einen effektiven stochastischen Gradientenabstieg unverzichtbar ist.  

###  <a name="supported-and-unsupported-parameters"></a>Unterstützte und nicht unterstützte Parameter 

In diesem Abschnitt wird auf die Unterstützung von Vowpal Wabbit-Befehlszeilenparametern im Azure Machine Learning-Designer eingegangen. 

Grundsätzlich werden bis auf ein paar wenige Ausnahmen alle Argumente unterstützt. Eine vollständige Liste der Argumente finden Sie auf der [Vowpal Wabbit-Wikiseite](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments).    

Die folgenden Parameter werden nicht unterstützt:

-   Die unter [https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments) aufgeführten Eingabe-/Ausgabeoptionen.  
  
     Diese Eigenschaften werden bereits automatisch von der Komponente konfiguriert.  
  
-   Außerdem sind alle Optionen, die mehrere Ausgaben generieren oder mehrere Eingaben erfordern, nicht zulässig. Dazu zählen *`--cbt`* , *`--lda`* und *`--wap`* .  
  
-   Nur überwachte Lernalgorithmen werden unterstützt. Daher werden unter anderem die folgenden Optionen nicht unterstützt: *`–active`* , `--rank` und *`--search`* . 

### <a name="restrictions"></a>Beschränkungen

Da das Ziel des Diensts ist, erfahrene Benutzer von Vowpal Wabbit zu unterstützen, müssen die Eingabedaten vorab im nativen Vowpal Wabbit-Textformat vorbereitet werden und nicht mit einem von anderen Komponenten verwendeten Datasetformat.

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die [Sammlung der verfügbaren Komponenten](component-reference.md) für Azure Machine Learning an. 
