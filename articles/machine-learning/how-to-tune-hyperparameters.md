---
title: Optimierung der Hyperparameter eines Modells
titleSuffix: Azure Machine Learning
description: Mit Azure Machine Learning können Sie die Optimierung der Hyperparameter für Deep Learning- und Machine Learning-Modelle automatisieren.
ms.author: sgilley
author: sdgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 02/26/2021
ms.topic: how-to
ms.custom: devx-track-python, contperf-fy21q1
ms.openlocfilehash: 5621b4d96023da61bb21451bcb23b21c19b812eb
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2021
ms.locfileid: "129093968"
---
# <a name="hyperparameter-tuning-a-model-with-azure-machine-learning"></a>Optimierung der Hyperparameter eines Modells mit Azure Machine Learning

Automatisieren eine effiziente Hyperparameteroptimierung mithilfe des [HyperDrive-Pakets](/python/api/azureml-train-core/azureml.train.hyperdrive) von Azure Machine Learning. Hier erfahren Sie, wie Sie die Schritte durchführen, die zum Optimieren der Hyperparameter mit dem [Azure Machine Learning SDK](/python/api/overview/azure/ml/) erforderlich sind:

1. Definieren des Suchbereichs für Parameter
1. Festlegen einer zu optimierenden primären Metrik  
1. Festlegen einer Richtlinie für die frühzeitige Beendigung bei Ausführungen mit geringer Leistung
1. Erstellen und Zuweisen von Ressourcen
1. Starten eines Experiments mit der definierten Konfiguration
1. Visualisieren der Trainingsläufe
1. Auswählen der für Ihr Modell am besten geeigneten Konfiguration

## <a name="what-is-hyperparameter-tuning"></a>Was ist die Hyperparameteroptimierung?

**Hyperparameter** sind anpassbare Parameter, mit denen Sie den Modelltrainingsprozess steuern können. Bei neuronalen Netzen legen Sie beispielsweise die Anzahl der verborgenen Ebenen sowie die Anzahl der Knoten in den einzelnen Ebenen fest. Die Modellleistung hängt stark von Hyperparametern ab.

 Die **Hyperparameteroptimierung** ist der Prozess, bei dem nach der Hyperparameterkonfiguration gesucht wird, die die beste Leistung liefert. Der Prozess ist in der Regel rechenintensiv und muss manuell durchgeführt werden.

Mit Azure Machine Learning können Sie die Hyperparameteroptimierung automatisieren und parallel Experimente durchführen, um Hyperparameter effizient zu optimieren.


## <a name="define-the-search-space"></a>Definieren des Suchbereichs

Optimieren Sie Hyperparameter, indem Sie den für jeden Hyperparameter definierten Wertebereich untersuchen.

Hyperparameter können diskret oder kontinuierlich sein und weisen eine Verteilung von Werten auf, die durch einen [Parameterausdruck](/python/api/azureml-train-core/azureml.train.hyperdrive.parameter_expressions) beschrieben wird.

### <a name="discrete-hyperparameters"></a>Diskrete Hyperparameter

Diskrete Hyperparameter werden als eine `choice` unter diskreten Werten angegeben. `choice` kann Folgendes sein:

* Ein oder mehrere durch Trennzeichen getrennte Werte
* Ein `range`-Objekt
* Ein beliebiges `list`-Objekt


```Python
    {
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

In diesem Fall wird für `batch_size` einer der Werte [16, 32, 64, 128] und für `number_of_hidden_layers` einer der Werte [1, 2, 3, 4] verwendet.

Die folgenden erweiterten diskreten Hyperparameter können ferner mithilfe einer Verteilung angegeben werden:

* `quniform(low, high, q)`: Gibt einen Wert wie „round(uniform(low, high) / q) * q“ zurück
* `qloguniform(low, high, q)`: Gibt einen Wert wie „round(exp(uniform(low, high)) / q) * q“ zurück
* `qnormal(mu, sigma, q)`: Gibt einen Wert wie „round(normal(mu, sigma) / q) * q“ zurück
* `qlognormal(mu, sigma, q)`: Gibt einen Wert wie „round(exp(normal(mu, sigma)) / q) * q“ zurück

### <a name="continuous-hyperparameters"></a>Kontinuierliche Hyperparameter 

Die kontinuierlichen Hyperparameter werden als Verteilung über einen kontinuierlichen Wertebereich angegeben:

* `uniform(low, high)`:Gibt einen gleichmäßig zwischen niedrig und hoch verteilen Wert zurück
* `loguniform(low, high)`: Gibt einen gemäß „exp(uniform(low, high))“ ermittelten Wert zurück, sodass der Logarithmus des Rückgabewerts gleichmäßig verteilt ist
* `normal(mu, sigma)`: Gibt einen realen Wert zurück, der mit einem Mittelwert mu und der Standardabweichung sigma normalverteilt ist
* `lognormal(mu, sigma)`: Gibt einen gemäß „exp(normal(mu, sigma))“ ermittelten Wert zurück, sodass der Logarithmus des Rückgabewerts normalverteilt ist

Im Folgenden sehen Sie ein Beispiel für die Definition des Parameterbereichs:

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

Dieser Code definiert einen Suchbereich mit zwei Parametern: `learning_rate` und `keep_probability`. `learning_rate` weist eine Normalverteilung mit dem Mittelwert 10 und der Standardabweichung 3 auf. `keep_probability` weist eine gleichmäßige Verteilung mit dem Mindestwert 0,05 und dem Höchstwert 0,1 auf.

### <a name="sampling-the-hyperparameter-space"></a>Stichprobenentnahme im Hyperparameterraum

Legen Sie die für den Hyperparameterbereich zu verwendende Parametersamplingmethode fest. Azure Machine Learning unterstützt die folgenden Methoden:

* Zufallssampling
* Rastersampling
* Bayessches Sampling

#### <a name="random-sampling"></a>Zufallssampling

[Zufallssampling](/python/api/azureml-train-core/azureml.train.hyperdrive.randomparametersampling) unterstützt diskrete und kontinuierliche Hyperparameter. Diese Methode unterstützt die Beendigung von Ausführungen mit geringer Leistung. Einige Benutzer führen eine erste Suche mit Zufallssampling durch und optimieren danach den Suchbereich zur Verbesserung der Ergebnisse.

Beim Zufallssampling werden Hyperparameterwerte zufällig aus dem definierten Suchbereich ausgewählt. 

```Python
from azureml.train.hyperdrive import RandomParameterSampling
from azureml.train.hyperdrive import normal, uniform, choice
param_sampling = RandomParameterSampling( {
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

#### <a name="grid-sampling"></a>Rastersampling

[Rastersampling](/python/api/azureml-train-core/azureml.train.hyperdrive.gridparametersampling) unterstützt diskrete Hyperparameter. Verwenden Sie das Rastersampling, wenn Sie es sich leisten können, Suchbereiche umfassend zu durchsuchen. Diese Methode unterstützt die Beendigung von Ausführungen mit geringer Leistung.

Beim Rastersampling wird eine einfache Rastersuche für alle möglichen Werte durchgeführt. Das Rastersampling kann nur für `choice`-Hyperparameter verwendet werden. Der folgende Bereich enthält insgesamt sechs Stichproben:

```Python
from azureml.train.hyperdrive import GridParameterSampling
from azureml.train.hyperdrive import choice
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>Bayessches Sampling

[Bayessches Sampling](/python/api/azureml-train-core/azureml.train.hyperdrive.bayesianparametersampling) basiert auf dem bayesschen Optimierungsalgorithmus. Bei dieser Methode werden Stichproben auf der Grundlage des Ergebnisses vorheriger Stichproben so gewählt, dass die primäre Metrik durch die neuen Stichproben verbessert wird.

Bayessches Sampling wird empfohlen, wenn Sie es sich leisten können, den Hyperparameterbereich zu erkunden. Optimale Ergebnisse erzielen Sie mit einer maximalen Anzahl von Ausführungen, die größer als oder gleich dem 20-fachen der Anzahl der zu optimierenden Hyperparameter ist. 

Die Anzahl der gleichzeitigen Ausführungen wirkt sich auf die Effektivität des Optimierungsprozesses aus. Eine kleinere Anzahl gleichzeitiger Ausführungen kann zu einer besseren Samplingkonvergenz führen, da durch den geringeren Parallelitätsgrad die Anzahl der Ausführungen zunimmt, die von früheren Ausführungen profitieren.

Bayessches Sampling unterstützt nur `choice`-, `uniform`- und `quniform`-Verteilungen über den Suchraum.

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
from azureml.train.hyperdrive import uniform, choice
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```



## <a name="specify-primary-metric"></a><a name="specify-primary-metric-to-optimize"></a> Angeben der primären Metrik

Legen Sie die [primäre Metrik](/python/api/azureml-train-core/azureml.train.hyperdrive.primarymetricgoal) fest, die durch die Hyperparameteroptimierung optimiert werden soll. Jeder Trainingslauf wird im Hinblick auf die primäre Metrik ausgewertet. Bei der Richtlinie zur vorzeitigen Beendigung wird die primäre Metrik zum Erkennen von Ausführungen mit geringer Leistung verwendet.

Legen Sie die folgenden Attribute für Ihre primäre Metrik fest:

* `primary_metric_name`: Der Name der primären Metrik muss exakt mit dem Namen der vom Trainingsskript protokollierten Metrik übereinstimmen
* `primary_metric_goal`: Kann entweder `PrimaryMetricGoal.MAXIMIZE` oder `PrimaryMetricGoal.MINIMIZE` sein und bestimmt, ob die primäre Metrik beim Auswerten der Läufe maximiert oder minimiert wird. 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

In diesem Beispiel wird "accuracy" maximiert.

### <a name="log-metrics-for-hyperparameter-tuning"></a><a name="log-metrics-for-hyperparameter-tuning"></a>Protokollieren von Metriken für die Hyperparameteroptimierung

Mit dem Trainingsskript für Ihr Modell **muss** die primäre Metrik während des Modelltrainings protokolliert werden, sodass HyperDrive bei der Hyperparameteroptimierung darauf zugreifen kann.

Protokollieren Sie die primäre Metrik mit dem folgenden Beispielcodeausschnitt in Ihrem Trainingsskript:

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

Mit dem Trainingsskript wird der `val_accuracy`-Wert berechnet und als die primäre Metrik „accuracy“ protokolliert. Jedes Mal, wenn die Metrik protokolliert wird, wird der Wert vom Dienst zur Hyperparameteroptimierung empfangen. Die Häufigkeit der Berichterstellung können Sie bestimmen.

Weitere Informationen zur Protokollierung von Werten bei Modelltrainingsausführungen finden Sie unter [Aktivieren der Protokollierung in Azure ML-Trainingsausführungen](how-to-log-view-metrics.md).

## <a name="specify-early-termination-policy"></a><a name="early-termination"></a> Festlegen einer Richtlinie für vorzeitige Beendigung

Ausführungen mit schlechter Leistung können mit einer Richtlinie für vorzeitige Beendigung automatisch beendet werden. Durch die vorzeitige Beendigung lässt sich die Recheneffizienz steigern.

Sie können die folgenden Parameter konfigurieren, mit denen die Anwendung einer Richtlinie gesteuert wird:

* `evaluation_interval`: die Anwendungshäufigkeit der Richtlinie. Jede Protokollierung der primären Metrik durch das Trainingsskript zählt als ein Intervall. Beim Wert 1 für `evaluation_interval` wird die Richtlinie jedes Mal angewendet, wenn das Trainingsskript die primäre Metrik meldet. Beim Wert 2 für `evaluation_interval` wird die Richtlinie bei jeder zweiten Meldung angewendet. Wenn kein Intervall angegeben wird, wird `evaluation_interval` standardmäßig auf 1 festgelegt.
* `delay_evaluation`: Verzögert die erste Auswertung der Richtlinie für eine angegebene Anzahl Intervalle. Dies ist ein optionaler Parameter, der zum Verhindern der vorzeitigen Beendigung von Trainingsausführungen die Ausführung aller Konfigurationen für eine anfängliche Minimalanzahl Intervalle ermöglicht. Wenn er angegeben wird, wird dir Richtlinie bei jedem Vielfachen von evaluation_interval angewendet, das größer als oder gleich groß wie delay_evaluation ist.

Azure Machine Learning unterstützt die folgenden Richtlinien für vorzeitige Beendigung:
* [Banditenrichtlinie](#bandit-policy)
* [Medianstopprichtlinie](#median-stopping-policy)
* [Kürzungsauswahlrichtlinie](#truncation-selection-policy)
* [Keine Beendigungsrichtlinie](#no-termination-policy-default)


### <a name="bandit-policy"></a>Banditenrichtlinie

Eine [Banditenrichtlinie](/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy#definition) basiert auf einem Pufferbereich/Pufferbetrag und einem Bewertungsintervall. Durch die Banditenrichtlinie werden Ausführungen beendet, wenn die primäre Metrik nicht innerhalb des angegebenen Pufferbereichs/Pufferbetrags der erfolgreichsten Ausführung liegt.

> [!NOTE]
> Beim bayesschen Sampling wird die frühzeitige Beendigung nicht unterstützt. Legen Sie bei Verwendung des bayesschen Sampling `early_termination_policy = None` fest.

Legen Sie die folgenden Konfigurationsparameter fest:

* `slack_factor` oder `slack_amount`: Der bezogen auf den Trainingslauf mit der besten Leistung zulässige Puffer. `slack_factor` gibt den zulässigen Puffer als Verhältnis an. `slack_amount` gibt den zulässigen Puffer als absoluten Betrag anstelle eines Verhältnisses an.

    Betrachten Sie als Beispiel die Anwendung einer Banditenrichtlinie auf das Intervall 10. Angenommen, die Ausführung mit der besten Leistung in Intervall 10 hat für das Ziel, die primäre Metrik zu maximieren, die primäre Metrik 0,8 gemeldet. Wenn die Richtlinie mit einem `slack_factor` von 0,2 angegeben wurde, werden alle Trainingsausführungen, deren beste Metrik im Intervall 10 kleiner als 0,66 (0,8/(1+`slack_factor`)) ist, beendet.
* `evaluation_interval`: (optional) die Anwendungshäufigkeit der Richtlinie
* `delay_evaluation`: (optional) verzögert die erste Auswertung der Richtlinie für eine angegebene Anzahl Intervalle


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

In diesem Beispiel wird die Richtlinie zur frühzeitigen Beendigung bei jedem Intervall angewendet, wenn Metriken gemeldet werden, beginnend bei Auswertungsintervall 5. Jeder Lauf, dessen beste Metrik kleiner als (1/(1+0,1) oder 91 % des Laufs mit der besten Leistung ist, wird beendet.

### <a name="median-stopping-policy"></a>Medianstopprichtlinie

Die [Medianstopprichtlinie](/python/api/azureml-train-core/azureml.train.hyperdrive.medianstoppingpolicy) ist eine Richtlinie für vorzeitige Beendigung, die auf dem gleitenden Durchschnitt der von den Ausführungen gemeldeten primären Metriken basiert. Diese Richtlinie berechnet den gleitenden Durchschnitt für alle Trainingsausführungen und beendet Ausführungen, bei denen der Wert der primären Metrik schlechter ist als der Median der Durchschnittswerte.

Diese Richtlinie akzeptiert die folgenden Konfigurationsparameter:
* `evaluation_interval`: die Anwendungshäufigkeit der Richtlinie (optionaler Parameter).
* `delay_evaluation`: verzögert die erste Auswertung der Richtlinie für eine angegebene Anzahl Intervalle (optionaler Parameter).


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

In diesem Beispiel wird die Richtlinie zur frühzeitigen Beendigung bei jedem Intervall angewendet, beginnend bei Auswertungsintervall 5. Eine Ausführung wird im fünften Intervall beendet, wenn die beste primäre Metrik schlechter als der Median des gleitenden Durchschnitts der Intervalle 1:5 für alle Trainingsausführungen ist.

### <a name="truncation-selection-policy"></a>Kürzungsauswahlrichtlinie

Die [Kürzungsauswahlrichtlinie](/python/api/azureml-train-core/azureml.train.hyperdrive.truncationselectionpolicy) bricht bei jedem Auswertungsintervall einen Prozentsatz der Ausführungen mit der schlechtesten Leistung ab. Ausführungen werden anhand der primären Metrik verglichen. 

Diese Richtlinie akzeptiert die folgenden Konfigurationsparameter:

* `truncation_percentage`: der Prozentsatz der Läufe mit der schwächsten Leistung, die bei jedem Auswertungsintervall beendet werden sollen. Eine ganze Zahl zwischen 1 und 99.
* `evaluation_interval`: (optional) die Anwendungshäufigkeit der Richtlinie
* `delay_evaluation`: (optional) verzögert die erste Auswertung der Richtlinie für eine angegebene Anzahl Intervalle


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

In diesem Beispiel wird die Richtlinie zur frühzeitigen Beendigung bei jedem Intervall angewendet, beginnend bei Auswertungsintervall 5. Eine Ausführung wird bei Intervall 5 beendet, wenn deren Leistung in den unteren 20 % der Leistung aller Ausführungen bei Intervall 5 liegt.

### <a name="no-termination-policy-default"></a>Keine Beendigungsrichtlinie (Standard)

Ist keine Richtlinie angegeben, lässt der Dienst zur Optimierung von Hyperparametern alle Trainingsläufe bis zum Abschluss ausführen.

```Python
policy=None
```

### <a name="picking-an-early-termination-policy"></a>Wählen einer Richtlinie für frühzeitige Beendigung

* Bei einer konservativen Richtlinie, die eine Ersparnis ohne Beendigung vielversprechender Aufträge ermöglicht, sollten Sie eine Medianstopprichtlinie mit den Werten 1 für `evaluation_interval` und 5 für `delay_evaluation` verwenden. Dies ist eine konservative Einstellung, die annähernd 25 %–35 % Ersparnis ohne Verluste bei der primären Metrik erbringen kann (bezogen auf unsere Auswertungsdaten).
* Bei aggressiveren Einsparungen verwenden Sie die Banditenrichtlinie mit einem strengeren (kleineren) zulässigen Puffer oder die Kürzungsauswahlrichtlinie mit einem höheren Kürzungsprozentsatz.

## <a name="create-and-assign-resources"></a>Erstellen und Zuweisen von Ressourcen

Legen Sie Ihr Ressourcenbudget fest, indem Sie die maximale Anzahl von Trainingsausführungen festlegen.

* `max_total_runs`: Maximale Anzahl von Trainingsausführungen. Muss eine ganze Zahl zwischen 1 und 1000 sein.
* `max_duration_minutes`: (optional) Die maximale Dauer des Experiments zur Hyperparameteroptimierung in Minuten. Ausführungen nach diesem Zeitraum werden abgebrochen.

>[!NOTE] 
>Wenn sowohl `max_total_runs` als auch `max_duration_minutes` angegeben werden, wird das Experiment zur Hyperparameteroptimierung beim Erreichen des ersten dieser beiden Schwellenwerte beendet.

Darüber hinaus können Sie die maximale Anzahl von Trainingsläufen festlegen, die während der Suche zur Hyperparameteroptimierung gleichzeitig ausgeführt werden.

* `max_concurrent_runs`: (optional) Maximale Anzahl von Ausführungen, die gleichzeitig ausgeführt werden können. Wenn dieser Wert nicht angegeben wird, werden alle Ausführungen parallel gestartet. Wenn dieser Wert angegeben wird, muss es sich dabei um eine ganze Zahl zwischen 1 und 100 handeln.

>[!NOTE] 
>Die Anzahl der gleichzeitigen Läufe wird durch die im angegebenen Computeziel verfügbaren Ressourcen beschränkt. Stellen Sie sicher, dass das Computeziel die verfügbaren Ressourcen für die gewünschte Parallelität aufweist.

```Python
max_total_runs=20,
max_concurrent_runs=4
```

Dieser Code konfiguriert das Experiment zur Hyperparameteroptimierung für eine Gesamtanzahl von maximal 20 Läufen, in denen jeweils vier Konfigurationen ausgeführt werden.

## <a name="configure-hyperparameter-tuning-experiment"></a>Konfigurieren des Experiments zur Hyperparameteroptimierung

Geben Sie Folgendes an, um [Ihr Experiment zur Hyperparameteroptimierung zu konfigurieren](/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverunconfig):
* Den definierten Suchbereich für Hyperparameter
* Ihre Richtlinie für vorzeitige Beendigung
* Die primäre Metrik
* Einstellungen für die Ressourcenzuordnung
* ScriptRunConfig `script_run_config`

„ScriptRunConfig“ ist das Trainingsskript, das mit den als Stichprobe entnommenen Hyperparametern ausgeführt wird. Damit werden die Ressourcen pro Auftrag (mit einem oder mehreren Knoten) und das zu verwendende Computeziel definiert.

> [!NOTE]
>Das in `script_run_config` verwendete Computeziel muss über genügend Ressourcen für Ihren Parallelitätsgrad verfügen. Weitere Informationen zu „ScriptRunConfig“ finden Sie unter [Konfigurieren einer Trainingsausführung](how-to-set-up-training-targets.md).

Konfigurieren Sie Ihr Experiment zur Hyperparameteroptimierung:

```Python
from azureml.train.hyperdrive import HyperDriveConfig
from azureml.train.hyperdrive import RandomParameterSampling, BanditPolicy, uniform, PrimaryMetricGoal

param_sampling = RandomParameterSampling( {
        'learning_rate': uniform(0.0005, 0.005),
        'momentum': uniform(0.9, 0.99)
    }
)

early_termination_policy = BanditPolicy(slack_factor=0.15, evaluation_interval=1, delay_evaluation=10)

hd_config = HyperDriveConfig(run_config=script_run_config,
                             hyperparameter_sampling=param_sampling,
                             policy=early_termination_policy,
                             primary_metric_name="accuracy",
                             primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                             max_total_runs=100,
                             max_concurrent_runs=4)
```

Durch `HyperDriveConfig` werden die an `ScriptRunConfig script_run_config` übergebenen Parameter festgelegt. Von `script_run_config` werden im Gegenzug Parameter an das Trainingsskript übergeben. Der obige Codeausschnitt stammt aus dem [Beispielnotebook zum Trainieren, Optimieren der Hyperparameter und Bereitstellen mit PyTorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch). In diesem Beispiel werden die Parameter `learning_rate` und `momentum` optimiert. Die frühzeitige Beendigung von Ausführungen wird durch eine Banditenrichtlinie (`BanditPolicy`) bestimmt. Bei dieser Richtlinie werden Ausführungen beendet, deren primäre Metrik außerhalb von `slack_factor` liegt. (Weitere Informationen finden Sie in der [Referenz zur BanditPolicy-Klasse](/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy).) 

Der folgende Code aus dem Beispiel zeigt, wie die zu optimierenden Werte empfangen, analysiert und an die Funktion `fine_tune_model` des Trainingsskripts übergeben werden:

```python
# from pytorch_train.py
def main():
    print("Torch version:", torch.__version__)

    # get command-line arguments
    parser = argparse.ArgumentParser()
    parser.add_argument('--num_epochs', type=int, default=25,
                        help='number of epochs to train')
    parser.add_argument('--output_dir', type=str, help='output directory')
    parser.add_argument('--learning_rate', type=float,
                        default=0.001, help='learning rate')
    parser.add_argument('--momentum', type=float, default=0.9, help='momentum')
    args = parser.parse_args()

    data_dir = download_data()
    print("data directory is: " + data_dir)
    model = fine_tune_model(args.num_epochs, data_dir,
                            args.learning_rate, args.momentum)
    os.makedirs(args.output_dir, exist_ok=True)
    torch.save(model, os.path.join(args.output_dir, 'model.pt'))
```

> [!Important]
> Das Training wird bei jeder Hyperparameterausführung von Grund auf neu gestartet (einschließlich Neuerstellung des Modells und _aller Datenlader_). Zur Kompensierung können Sie eine Azure Machine Learning-Pipeline oder einen manuellen Prozess verwenden, um einen möglichst großen Teil der Datenaufbereitung vor der Trainingsausführung durchzuführen. 

## <a name="submit-hyperparameter-tuning-experiment"></a>Übermitteln des Experiments zur Hyperparameteroptimierung

Nachdem Sie Ihre Konfiguration für die Hyperparameteroptimierung definiert haben, [übermitteln Sie das Experiment](/python/api/azureml-core/azureml.core.experiment%28class%29#submit-config--tags-none----kwargs-):

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hd_config)
```

## <a name="warm-start-hyperparameter-tuning-optional"></a>Warmstart für die Hyperparameteroptimierung (optional)

Die Suche nach den besten Hyperparameterwerten für Ihr Modell kann ein iterativer Vorgang sein. Zur Beschleunigung der Hyperparameteroptimierung können Sie das Wissen aus den fünf vorherigen Ausführungen wiederverwenden.

Der Warmstart erfolgt je nach Samplingmethode unterschiedlich:
- **Bayessches Sampling**: Versuche aus den früheren Ausführungen werden bei der Auswahl neuer Stichproben als Vorwissen und zur Verbesserung der primären Metrik verwendet.
- **Zufallssampling** oder **Rastersampling**:  Bei der vorzeitigen Beendigung wird Wissen aus früheren Ausführungen zum Beenden von Ausführungen mit schlechter Leistung verwendet. 

Geben Sie die Liste der übergeordneten Ausführungen für einen Warmstart an.

```Python
from azureml.train.hyperdrive import HyperDriveRun

warmstart_parent_1 = HyperDriveRun(experiment, "warmstart_parent_run_ID_1")
warmstart_parent_2 = HyperDriveRun(experiment, "warmstart_parent_run_ID_2")
warmstart_parents_to_resume_from = [warmstart_parent_1, warmstart_parent_2]
```

Wenn ein Experiment zur Optimierung von Hyperparametern abgebrochen wird, können Sie die Trainingsausführungen vom letzten Prüfpunkt aus fortsetzen. Ihr Trainingsskript muss jedoch die Prüfpunktlogik verarbeiten.

Für die Trainingsausführung muss dieselbe Hyperparameterkonfiguration verwendet werden. Zudem müssen die Ausgabeordner bereitgestellt werden. Das Trainingsskript muss das Argument `resume-from` akzeptieren, das die Prüfpunkt- oder Modelldateien enthält, mit denen die Trainingsausführung fortgesetzt werden kann. Mithilfe des folgenden Codeausschnitts können Sie einzelne Trainingsläufe fortsetzen:

```Python
from azureml.core.run import Run

resume_child_run_1 = Run(experiment, "resume_child_run_ID_1")
resume_child_run_2 = Run(experiment, "resume_child_run_ID_2")
child_runs_to_resume = [resume_child_run_1, resume_child_run_2]
```

Sie können Ihr Experiment zur Hyperparameteroptimierung so konfigurieren, dass es einen Warmstart aus einem früheren Experiment ermöglicht, oder einzelne Trainingsläufe mithilfe der optionalen Parameter `resume_from` und `resume_child_runs` in der Konfiguration fortsetzen:

```Python
from azureml.train.hyperdrive import HyperDriveConfig

hd_config = HyperDriveConfig(run_config=script_run_config,
                             hyperparameter_sampling=param_sampling,
                             policy=early_termination_policy,
                             resume_from=warmstart_parents_to_resume_from,
                             resume_child_runs=child_runs_to_resume,
                             primary_metric_name="accuracy",
                             primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                             max_total_runs=100,
                             max_concurrent_runs=4)
```

## <a name="visualize-hyperparameter-tuning-runs"></a>Visualisieren der Ausführungen der Hyperparameteroptimierung

Sie können Ihre Ausführungen der Hyperparameteroptimierung im Azure Machine Learning Studio visualisieren oder ein Notebook-Widget verwenden.

### <a name="studio"></a>Studio

Sie können alle Ihre Ausführungen der Hyperparameteroptimierung im [Azure Machine Learning Studio](https://ml.azure.com) visualisieren. Weitere Informationen zum Anzeigen von Experimenten im Portal finden Sie unter [Anzeigen von Ausführungsaufzeichnungen in Studio](how-to-log-view-metrics.md#view-the-experiment-in-the-web-portal).

- **Metrikdiagramm:** Diese Visualisierung dient zum Nachverfolgen der Metriken, die im Rahmen der Hyperparameteroptimierung für die einzelnen untergeordneten HyperDrive-Ausführungen protokolliert werden. Jede Zeile stellt eine untergeordnete Ausführung dar, und jeder Punkt misst den primären Metrikwert bei dieser Laufzeititeration.  

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-metrics.png" alt-text="Metrikdiagramm für die Hyperparameteroptimierung":::

- **Diagramm mit parallelen Koordinaten:** Diese Visualisierung zeigt die Korrelation zwischen der Leistung der primären Metrik und individuellen Hyperparameterwerten. Das Diagramm ist interaktiv. Da bedeutet, die Achsen können durch Klicken und Ziehen der Achsenbezeichnung verschoben werden. Außerdem können Werte auf einer einzelnen Achse hervorgehoben werden. (Klicken und ziehen Sie vertikal entlang einer einzelnen Achse, um einen gewünschten Wertebereich hervorzuheben.) Auf der rechten Seite des Diagramm mit parallelen Koordinaten befindet sich eine Achse, die den besten Metrikwert entsprechend der für diese Ausführungsinstanz festgelegten Hyperparameter kennzeichnet. Mit dieser Achse wird die Diagrammgradientenlegende besser lesbar auf die Daten projiziert.

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-parallel-coordinates.png" alt-text="Diagramm mit parallelen Koordinaten für die Hyperparameteroptimierung":::

- **Zweidimensionales Punktdiagramm:** Diese Visualisierung zeigt die Korrelation zwischen zwei individuellen Hyperparametern zusammen mit ihrem zugeordneten primären Metrikwert.

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-2-dimensional-scatter.png" alt-text="Zweidimensionales Punktdiagramm für die Hyperparameteroptimierung":::

- **Dreidimensionales Punktdiagramm:** Diese Visualisierung ist mit der zweidimensionalen Variante identisch, ermöglicht jedoch die Verwendung von drei Hyperparameterdimensionen für die Korrelation mit dem primären Metrikwert. Sie können auch auf das Diagramm klicken und ziehen, um die Ausrichtung des Diagramms zu ändern und verschiedene Korrelationen im dreidimensionalen Raum anzuzeigen.

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-3-dimensional-scatter.png" alt-text="Dreidimensionales Punktdiagramm für die Hyperparameteroptimierung":::

### <a name="notebook-widget"></a>Notebook-Widget

Verwenden Sie das [Notebook-Widget](/python/api/azureml-widgets/azureml.widgets.rundetails), um den Fortschritt Ihrer Trainingsausführungen zu visualisieren. Der folgende Codeausschnitt visualisiert alle Läufe zur Hyperparameteroptimierung in einem Jupyter Notebook:

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Dieser Code zeigt eine Tabelle mit Details zu den Trainingsläufen für jede der Hyperparameterkonfigurationen an.

:::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-table.png" alt-text="Tabelle für die Hyperparameteroptimierung":::

Ferner können Sie die Leistung jedes dieser Läufe während des fortschreitenden Trainings visualisieren.

## <a name="find-the-best-model"></a>Ermitteln des besten Modells

Nach Abschluss aller Ausführungen der Hyperparameteroptimierung ermitteln Sie die Konfiguration mit der besten Leistung und die Hyperparameterwerte:

```Python
best_run = hyperdrive_run.get_best_run_by_primary_metric()
best_run_metrics = best_run.get_metrics()
parameter_values = best_run.get_details()['runDefinition']['arguments']

print('Best Run Id: ', best_run.id)
print('\n Accuracy:', best_run_metrics['accuracy'])
print('\n learning rate:',parameter_values[3])
print('\n keep probability:',parameter_values[5])
print('\n batch size:',parameter_values[7])
```

## <a name="sample-notebook"></a>Beispielnotebook

Weitere Informationen finden Sie in den Notebooks zum Trainieren von Hyperparametern im folgenden Ordner:
* [how-to-use-azureml/ml-frameworks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nächste Schritte
* [Nachverfolgen eines Experiments](how-to-log-view-metrics.md)
* [Bereitstellen eines trainierten Modells](how-to-deploy-and-where.md)
