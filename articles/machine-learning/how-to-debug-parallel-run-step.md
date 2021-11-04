---
title: Behandeln von Problemen mit „ParallelRunStep“
titleSuffix: Azure Machine Learning
description: Hier finden Sie Tipps zur Problembehandlung im Zusammenhang mit Fehlern bei der Verwendung von „ParallelRunStep“ in Machine Learning-Pipelines.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: troubleshooting
ms.custom: troubleshooting
ms.reviewer: larryfr, vaidyas, laobri, tracych
ms.author: pansav
author: psavdekar
ms.date: 10/21/2021
ms.openlocfilehash: 47ad08328588ca3b380298b6e796cc05d3af29fe
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131564326"
---
# <a name="troubleshooting-the-parallelrunstep"></a>Behandeln von Problemen mit „ParallelRunStep“

Dieser Artikel enthält eine Problembehandlungsanleitung für den Fall, dass bei der Verwendung der Klasse [ParallelRunStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep) aus dem [Azure Machine Learning SDK](/python/api/overview/azure/ml/intro) Fehler auftreten.

Allgemeine Tipps zur Problembehandlung für Pipelines finden Sie unter [Problembehandlung bei Machine Learning-Pipelines](how-to-debug-pipelines.md).

## <a name="testing-scripts-locally"></a>Lokales Testen von Skripts

 „ParallelRunStep“ wird als Schritt in ML-Pipelines ausgeführt. Als Erstes sollten Sie ggf. [Ihre Skripts lokal testen](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines).

## <a name="entry-script-requirements"></a>Einstiegsskriptanforderungen

Das Einstiegsskript für `ParallelRunStep` *muss* eine `run()`-Funktion enthalten und kann optional eine `init()`-Funktion enthalten:
- `init()`: Verwenden Sie diese Funktion für alle kostspieligen oder allgemeinen Vorbereitungsmaßnahmen für die spätere Verarbeitung. Ein Beispiel wäre etwa das Laden des Modells in ein globales Objekt. Diese Funktion wird nur einmal zu Beginn des Prozesses aufgerufen.
    > [!NOTE]
    > Wenn Ihre `init`-Methode ein Ausgabeverzeichnis erstellt, geben Sie `parents=True` und `exist_ok=True` an. Die `init`-Methode wird von jedem Workerprozess auf jedem Knoten aufgerufen, auf dem der Auftrag ausgeführt wird.
-  `run(mini_batch)`: Diese Funktion wird für jede Instanz vom Typ `mini_batch` ausgeführt.
    -  `mini_batch`: `ParallelRunStep` ruft die Run-Methode auf und übergibt entweder eine Liste oder einen Pandas-Datenrahmen (`DataFrame`) als Argument an die Methode. Jeder Eintrag in „mini_batch“ entspricht einem Dateipfad, wenn die Eingabe ein `FileDataset` ist, bzw. einem Pandas-Datenrahmen (`DataFrame`), wenn die Eingabe ein `TabularDataset` ist.
    -  `response`: Die Run-Methode sollte einen Pandas-Datenrahmen (`DataFrame`) oder ein Array zurückgeben. Bei Verwendung von „append_row output_action“ werden diese zurückgegebenen Elemente am Ende der allgemeinen Ausgabedatei hinzugefügt. Bei Verwendung von „summary_only“ wird der Inhalt der Elemente ignoriert. Bei allen Ausgabeaktionen geben die zurückgegebenen Ausgabeelemente jeweils eine erfolgreiche Ausführung für ein Eingabeelement aus dem Eingabeminibatch an. Stellen Sie sicher, dass das Ausführungsergebnis genügend Daten für eine Zuordnung zwischen der Eingabe und der Ausgabe des Ausführungsergebnisses enthält. Die Ausführungsausgabe wird in die Ausgabedatei geschrieben. Da hierbei nicht unbedingt die Reihenfolge eingehalten wird, müssen Sie einen Schlüssel in der Ausgabe verwenden, um sie der Eingabe zuzuordnen.
        > [!NOTE]
        > Für ein Eingabeelement wird ein Ausgabeelement erwartet.

```python
%%writefile digit_identification.py
# Snippets from a sample script.
# Refer to the accompanying digit_identification.py
# (https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines/parallel-run)
# for the implementation script.

import os
import numpy as np
import tensorflow as tf
from PIL import Image
from azureml.core import Model


def init():
    global g_tf_sess

    # Pull down the model from the workspace
    model_path = Model.get_model_path("mnist")

    # Construct a graph to execute
    tf.reset_default_graph()
    saver = tf.train.import_meta_graph(os.path.join(model_path, 'mnist-tf.model.meta'))
    g_tf_sess = tf.Session()
    saver.restore(g_tf_sess, os.path.join(model_path, 'mnist-tf.model'))


def run(mini_batch):
    print(f'run method start: {__file__}, run({mini_batch})')
    resultList = []
    in_tensor = g_tf_sess.graph.get_tensor_by_name("network/X:0")
    output = g_tf_sess.graph.get_tensor_by_name("network/output/MatMul:0")

    for image in mini_batch:
        # Prepare each image
        data = Image.open(image)
        np_im = np.array(data).reshape((1, 784))
        # Perform inference
        inference_result = output.eval(feed_dict={in_tensor: np_im}, session=g_tf_sess)
        # Find the best probability, and add it to the result list
        best_result = np.argmax(inference_result)
        resultList.append("{}: {}".format(os.path.basename(image), best_result))

    return resultList
```

Wenn das Verzeichnis mit Ihrem Rückschlussskript noch andere Dateien oder Ordner enthält, können Sie einen Verweis darauf erstellen, indem Sie das aktuelle Arbeitsverzeichnis ermitteln.

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

### <a name="parameters-for-parallelrunconfig"></a>Parameter für ParallelRunConfig

`ParallelRunConfig` ist die Hauptkonfiguration für die `ParallelRunStep`-Instanz innerhalb der Azure Machine Learning-Pipeline. Sie wird verwendet, um Ihr Skript zu umschließen und die erforderlichen Parameter, einschließlich der folgenden Einträge, zu konfigurieren:
- `entry_script`: Ein Benutzerskript als lokaler Dateipfad, das parallel auf mehreren Knoten ausgeführt wird. Ist `source_directory` vorhanden, verwenden Sie einen relativen Pfad. Verwenden Sie andernfalls einen beliebigen Pfad, auf den auf dem Computer zugegriffen werden kann.
- `mini_batch_size`: Die Größe des Minibatchs, der an einen einzelnen Aufruf von `run()` übergeben wird (optional; Standardwerte sind `10` Dateien für `FileDataset` und `1MB` für `TabularDataset`).
    - Bei `FileDataset` handelt es sich um die Anzahl von Dateien (Mindestwert: `1`). Mehrere Dateien können zu einem Minibatch zusammengefasst werden.
    - Bei `TabularDataset` handelt es sich um die Datengröße. Beispielwerte: `1024`, `1024KB`, `10MB`, `1GB`. Empfohlener Wert: `1MB`. Der Minibatch auf der Grundlage von `TabularDataset` geht nie über Dateigrenzen hinaus. Ein Beispiel: Angenommen, Sie verfügen über unterschiedlich große CSV-Dateien. Die kleinste Datei hat eine Größe von 100 KB, die größte Datei ist 10 MB groß. Wenn Sie nun `mini_batch_size = 1MB` festlegen, werden Dateien mit einer Größe von weniger als 1 MB als einzelner Minibatch behandelt. Dateien mit einer Größe von mehr als 1 MB werden dagegen in mehrere Minibatches aufgeteilt.
        > [!NOTE]
        > TabularDatasets, die mit SQL gesichert sind, können nicht partitioniert werden.
        > TabularDatasets aus einer einzelnen Parquet-Datei und einer einzelnen Zeilengruppe können nicht partitioniert werden.

- `error_threshold`: Die Anzahl von Datensatzfehlern für `TabularDataset` bzw. Dateifehlern für `FileDataset`, die während der Verarbeitung ignoriert werden sollen. Übersteigt die Fehleranzahl für die gesamte Eingabe diesen Wert, wird der Auftrag abgebrochen. Der Fehlerschwellenwert gilt für die gesamte Eingabe und nicht für den einzelnen Minibatch, der an die Methode `run()` gesendet wird. Zulässiger Bereich: `[-1, int.max]`. Der Teil `-1` gibt an, dass bei der Verarbeitung alle Fehler ignoriert werden sollen.
- `output_action`: Gibt an, wie die Ausgabe strukturiert werden soll:
    - `summary_only`: Das Benutzerskript speichert die Ausgabe. `ParallelRunStep` verwendet die Ausgabe nur zur Berechnung des Fehlerschwellenwerts.
    - `append_row`: Für alle Eingaben wird lediglich eine einzelne Datei im Ausgabeordner erstellt, in der alle Ausgaben angefügt werden (getrennt durch eine Zeile).
- `append_row_file_name`: Zum Anpassen des Namens der Ausgabedatei für append_row output_action (optional; der Standardwert ist `parallel_run_step.txt`).
- `source_directory`: Pfade zu Ordnern mit allen Dateien, die am Computeziel ausgeführt werden sollen (optional).
- `compute_target`: Nur `AmlCompute` wird unterstützt.
- `node_count`: Die Anzahl von Computeknoten, die zum Ausführen des Benutzerskripts verwendet werden sollen.
- `process_count_per_node`: Die Anzahl der Workerprozesse pro Knoten zum parallelen Ausführen des Eingabeskripts. Für einen GPU-Computer ist der Standardwert 1. Für einen CPU-Computer ist der Standardwert die Anzahl der Kerne pro Knoten. Ein Workerprozess ruft `run()` wiederholt auf, indem er den von ihm abgerufenen Minibatch übergibt. Die Gesamtanzahl der Workerprozesse in Ihrem Auftrag ist `process_count_per_node * node_count`, wodurch die maximale Anzahl von `run()` für die parallele Ausführung bestimmt wird.
- `environment`: Die Python-Umgebungsdefinition. Sie kann für die Verwendung einer vorhandenen Python-Umgebung oder für die Einrichtung einer temporären Umgebung konfiguriert werden. Die Definition kann auch zum Festlegen der erforderlichen Anwendungsabhängigkeiten verwendet werden (optional).
- `logging_level`: Die Ausführlichkeit des Protokolls. Mögliche Werte (mit zunehmender Ausführlichkeit): `WARNING`, `INFO`, `DEBUG`. (optional; Standardwert: `INFO`)
- `run_invocation_timeout`: Das Timeout für den Aufruf der Methode `run()` in Sekunden. (optional; Standardwert `60`)
- `run_max_try`: Maximale Anzahl der Versuche von `run()` für einen Minibatch. Ein `run()` ist fehlgeschlagen, wenn eine Ausnahme ausgelöst wird oder beim Erreichen von `run_invocation_timeout` nichts zurückgegeben wird (optional; der Standardwert ist `3`).

Sie können `mini_batch_size`, `node_count`, `process_count_per_node`, `logging_level`, `run_invocation_timeout` und `run_max_try` als `PipelineParameter` festlegen, sodass Sie die Parameterwerte beim erneuten Senden einer Pipelineausführung optimieren können. In diesem Beispiel verwenden Sie `PipelineParameter` für `mini_batch_size` und `Process_count_per_node`, und Sie ändern diese Werte, wenn Sie erneut eine Ausführung senden.

#### <a name="cuda-devices-visibility"></a>Sichtbarkeit von CUDA-Geräten
Für Computeziele, die mit GPUs ausgestattet sind, wird die Umgebungsvariable `CUDA_VISIBLE_DEVICES` in Workerprozessen festgelegt. In AmlCompute finden Sie die Gesamtzahl der GPU-Geräte in der Umgebungsvariablen `AZ_BATCHAI_GPU_COUNT_FOUND`, die automatisch festgelegt wird. Wenn jeder Workerprozess über eine dedizierte GPU verfügen soll, legen Sie `process_count_per_node` auf die Anzahl der GPU-Geräte auf einem Computer fest. Jeder Workerprozess weist `CUDA_VISIBLE_DEVICES` einen eindeutigen Index zu. Wenn ein Workerprozess aus irgendeinem Grund beendet wird, verwendet der nächste gestartete Workerprozess den freigegebenen GPU-Index.

Wenn die Gesamtzahl der GPU-Geräte kleiner als `process_count_per_node` ist, wird den Workerprozessen ein GPU-Index zugewiesen, bis alle verwendet wurden.

Wenn beispielsweise die Gesamtzahl der GPU-Geräte 2 und `process_count_per_node = 4` ist, weisen Prozess 0 und Prozess 1 den Index 0 und 1 auf. Für Prozess 2 und 3 ist keine Umgebungsvariable vorhanden. Bei einer Bibliothek, die diese Umgebungsvariable für die GPU-Zuweisung verwendet, verfügen Prozess 2 und 3 nicht über GPUs und versuchen nicht, GPU-Geräte zu beziehen. Wenn Prozess 0 beendet wird, wird der GPU-Index 0 freigegeben. Dem nächsten Prozess (Prozess 4) wird GPU-Index 0 zugewiesen.

Weitere Informationen finden Sie unter [CUDA Pro Tip: Control GPU Visibility with CUDA_VISIBLE_DEVICES](https://developer.nvidia.com/blog/cuda-pro-tip-control-gpu-visibility-cuda_visible_devices/).

### <a name="parameters-for-creating-the-parallelrunstep"></a>Parameter zum Erstellen von ParallelRunStep

Erstellen Sie ParallelRunStep mithilfe von Skript, Umgebungskonfiguration und Parametern. Geben Sie das Computeziel an, das Sie bereits als Ausführungsziel für Ihr Rückschlussskript an Ihren Arbeitsbereich angefügt haben. Verwenden Sie `ParallelRunStep`, um den Batchrückschluss-Pipelineschritt mit folgenden Parametern zu erstellen:
- `name`: Der Name des Schritts. Benennungseinschränkungen: eindeutig, 3–32 Zeichen und regulärer Ausdruck ^\[a-z\]([-a-z0-9]*[a-z0-9])?$.
- `parallel_run_config`: Ein Objekt vom Typ `ParallelRunConfig`, wie zuvor definiert.
- `inputs`: Mindestens ein Azure Machine Learning-Dataset mit einem einzelnen Typ, das für die Parallelverarbeitung partitioniert werden soll.
- `side_inputs`: Verweisdaten oder Datasets, die als Seiteneingabe verwendet werden und für die keine Partitionierung erforderlich ist.
- `output`: Ein `OutputFileDatasetConfig`-Objekt, das den Verzeichnispfad darstellt, in dem die Ausgabedaten gespeichert werden.
- `arguments`: Eine Liste mit Argumenten, die an das Benutzerskript übergeben werden. Verwenden Sie „unknown_args“, um sie in Ihrem Einstiegsskript zu nutzen (optional).
- `allow_reuse`: Angabe, ob bei dem Schritt vorherige Ergebnisse wiederverwendet werden sollen, wenn er mit den gleichen Einstellungen/Eingaben ausgeführt wird. Ist der Parameter auf `False` festgelegt, wird für diesen Schritt bei der Pipelineausführung immer eine neue Ausführung generiert. (optional; Standardwert `True`)

```python
from azureml.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="predict-digits-mnist",
    parallel_run_config=parallel_run_config,
    inputs=[input_mnist_ds_consumption],
    output=output_dir,
    allow_reuse=True
)
```

## <a name="debugging-scripts-from-remote-context"></a>Debuggen von Skripts aus Remotekontext

Der Übergang vom lokalen Debuggen eines Bewertungsskripts zum Debuggen eines Bewertungsskripts in einer echten Pipeline kann einen schwierigen Schritt darstellen. Wie Sie Ihre Protokolle im Portal finden, erfahren Sie im [Abschnitt zu Pipelines des maschinellen Lernens über das Debuggen von Skripts aus einem Remotekontext](how-to-debug-pipelines.md). Die Informationen in diesem Abschnitt gelten auch für ParallelRunStep.

Die Protokolldatei `70_driver_log.txt` enthält z. B. Informationen vom Controller, der den ParallelRunStep-Code startet.

Aufgrund der verteilten Struktur von ParallelRunStep-Aufträgen gibt es Protokolle aus mehreren verschiedenen Quellen. Es werden jedoch zwei konsolidierte Dateien erstellt, die allgemeine Informationen bieten:

- `~/logs/job_progress_overview.txt`: Diese Datei enthält allgemeine Informationen zur Anzahl der bisher erstellten Minibatches (auch als Aufgaben bezeichnet) und der Anzahl der bisher verarbeiteten Minibatches. Am Ende wird das Ergebnis des Auftrags aufgeführt. Wenn bei dem Auftrag ein Fehler aufgetreten ist, wird die Fehlermeldung angezeigt, und es wird beschrieben, wo mit der Problembehandlung begonnen werden sollte.

- `~/logs/sys/master_role.txt`: Diese Datei stellt die Prinzipalknotenansicht (auch als Orchestrator bezeichnet) des laufenden Auftrags bereit. Dies umfasst die Aufgabenerstellung, die Fortschrittsüberwachung und das Ergebnis der Ausführung.

Protokolle, die mit dem EntryScript-Hilfsprogramm aus dem Eingabeskript generiert wurden, sowie Druckanweisungen finden Sie in den folgenden Dateien:

- `~/logs/user/entry_script_log/<node_id>/<process_name>.log.txt`: Diese Dateien sind die Protokolle, die von „entry_script“ mithilfe des EntryScript-Hilfsprogramms geschrieben wurden.

- `~/logs/user/stdout/<node_id>/<process_name>.stdout.txt`: Bei diesen Dateien handelt es sich um die Protokolle von stdout (z. B. eine Pprint-Anweisung) von entry_script.

- `~/logs/user/stderr/<node_id>/<process_name>.stderr.txt`: Diese Dateien sind die Protokolle von stderr von entry_script.

Für ein präzises Verständnis von Fehlern in Ihrem Skript gibt es Folgendes:

- `~/logs/user/error.txt`: Diese Datei wird versuchen, die Fehler in Ihrem Skript zusammenzufassen.

Weitere Informationen zu Fehlern in Ihrem Skript finden Sie hier:

- `~/logs/user/error/`: Enthält vollständige Stapelüberwachungen von Ausnahmen, die beim Laden und Ausführen des Eingabeskripts ausgelöst werden.

Wenn Sie detailliert erfahren möchten, wie die einzelnen Knoten das Bewertungsskript ausgeführt haben, sehen Sie sich die jeweiligen Prozessprotokolle für jeden Knoten an. Die Prozessprotokolle befinden sich im Ordner `sys/node`, gruppiert nach Workerknoten:

- `~/logs/sys/node/<node_id>/<process_name>.txt`: Diese Datei enthält ausführliche Informationen zu den einzelnen Minibatches, die von einem Worker abgerufen oder abgeschlossen werden. Für jeden Minibatch umfasst diese Datei Folgendes:

    - Die IP-Adresse und die PID des Workerprozesses.
    - Die Gesamtzahl der Elemente, die Anzahl der erfolgreich verarbeiteten Elemente und die Anzahl der nicht erfolgreichen Elemente.
    - Die Startzeit, die Dauer, die Verarbeitungszeit und die Laufzeit der Methode.

Sie können für jeden Knoten auch die Ergebnisse regelmäßiger Prüfungen der Ressourcennutzung anzeigen. Die Protokoll- und Setupdateien befinden sich in diesem Ordner:

- `~/logs/perf`: Legen Sie `--resource_monitor_interval` fest, um das Prüfintervall in Sekunden zu ändern. Das Standardintervall ist `600` (ungefähr 10 Minuten). Um die Überwachung zu beenden, legen Sie den Wert auf `0` fest. Jeder Ordner `<node_id>` enthält Folgendes:

    - `os/`: Informationen zu allen laufenden Prozessen im Knoten. Bei einer Prüfung wird ein Betriebssystembefehl ausgeführt und das Ergebnis in einer Datei gespeichert. Unter Linux ist der Befehl `ps`. Verwenden Sie für Windows `tasklist`.
        - `%Y%m%d%H`: Der Name des Unterordners ist die Uhrzeit zur vollen Stunde.
            - `processes_%M`: Die Datei endet mit der Minute der Prüfzeit.
    - `node_disk_usage.csv`: Detaillierte Datenträgernutzung des Knotens.
    - `node_resource_usage.csv`: Übersicht über die Ressourcennutzung des Knotens.
    - `processes_resource_usage.csv`: Übersicht über die Ressourcennutzung der einzelnen Prozesse.

## <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Wie protokolliere ich mein Benutzerskript aus einem Remotekontext?

ParallelRunStep kann mehrere Prozesse auf einem Knoten entsprechend dem process_count_per_node ausführen. Wir empfehlen die unten gezeigte Verwendung der ParallelRunStep-Protokollierung, um die Protokolle der einzelnen Prozesse auf dem Knoten zu organisieren und die Druck- und Protokollanweisungen zu kombinieren. Sie können eine Protokollierung von EntryScript abrufen, damit die Protokolle im Ordner **logs/user** im Portal aufgeführt werden.

**Ein Beispiel für Eingabeskript mit der Protokollierung:**
```python
from azureml_user.parallel_run import EntryScript

def init():
    """Init once in a worker process."""
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug("This will show up in files under logs/user on the Azure portal.")


def run(mini_batch):
    """Call once for a mini batch. Accept and return the list back."""
    # This class is in singleton pattern and will return same instance as the one in init()
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug(f"{__file__}: {mini_batch}.")
    ...

    return mini_batch
```

## <a name="where-does-the-message-from-python-logging-sink-to"></a>In welche Senke gelangt die Nachricht von `logging` in Python?
„ParallelRunStep“ legt einen Handler für die Stammprotokollierung fest, der die Nachricht an die Senke `logs/user/stdout/<node_id>/processNNN.stdout.txt` schickt.

`logging` hat standardmäßig die Stufe `WARNING`. Standardmäßig werden niedrigere Stufen als `WARNING` nicht angezeigt, also z. B. `INFO` oder `DEBUG`.

## <a name="how-could-i-write-to-a-file-to-show-up-in-the-portal"></a>Wie kann ich in eine Datei schreiben, die im Portal angezeigt werden soll?
Dateien im Ordner `logs` werden ins Portal hochgeladen und dort angezeigt.
Sie können den Ordner `logs/user/entry_script_log/<node_id>` wie unten gezeigt abrufen und ihren Dateipfad zum Schreiben zusammenstellen:

```python
from pathlib import Path
from azureml_user.parallel_run import EntryScript

def init():
    """Init once in a worker process."""
    entry_script = EntryScript()
    log_dir = entry_script.log_dir
    log_dir = Path(entry_script.log_dir)  # logs/user/entry_script_log/<node_id>/.
    log_dir.mkdir(parents=True, exist_ok=True) # Create the folder if not existing.

    proc_name = entry_script.agent_name  # The process name in pattern "processNNN".
    fil_path = log_dir / f"{proc_name}_<file_name>" # Avoid conflicting among worker processes with proc_name.
```

## <a name="how-to-handle-log-in-new-processes"></a>Wie wird die Anmeldung neuer Prozesse gehandhabt?
Sie können mit dem [`subprocess`](https://docs.python.org/3/library/subprocess.html)-Modul neue Prozesse in Ihrem Einstiegsskript erzeugen, eine Verbindung mit ihren Eingabe-/Ausgabe-/Fehlerpipes herstellen und ihre Rückgabecodes abrufen.

Es wird empfohlen, die [`run()`](https://docs.python.org/3/library/subprocess.html#subprocess.run)-Funktion mit `capture_output=True` zu verwenden. Fehler werden in `logs/user/error/<node_id>/<process_name>.txt` angezeigt.

Wenn Sie `Popen()` verwenden möchten, sollten Sie „stdout/stderr“ wie folgt zu Dateien umleiten:
```python
from pathlib import Path
from subprocess import Popen

from azureml_user.parallel_run import EntryScript


def init():
    """Show how to redirect stdout/stderr to files in logs/user/entry_script_log/<node_id>/."""
    entry_script = EntryScript()
    proc_name = entry_script.agent_name  # The process name in pattern "processNNN".
    log_dir = Path(entry_script.log_dir)  # logs/user/entry_script_log/<node_id>/.
    log_dir.mkdir(parents=True, exist_ok=True) # Create the folder if not existing.
    stdout_file = str(log_dir / f"{proc_name}_demo_stdout.txt")
    stderr_file = str(log_dir / f"{proc_name}_demo_stderr.txt")
    proc = Popen(
        ["...")],
        stdout=open(stdout_file, "w"),
        stderr=open(stderr_file, "w"),
        # ...
    )

```

> [!NOTE]
> Ein Workerprozess führt den „Systemcode“ und den Code des Einstiegsskripts im selben Prozess aus.
>
> Wenn kein `stdout` oder `stderr` angegeben wird, erbt ein Unterprozess, der mit `Popen()` in Ihrem Einstiegsskript erstellt wird, die Einstellung des Workerprozesses.
>
> `stdout` schreibt in `logs/sys/node/<node_id>/processNNN.stdout.txt` und `stderr` in `logs/sys/node/<node_id>/processNNN.stderr.txt`.


## <a name="how-do-i-write-a-file-to-the-output-directory-and-then-view-it-in-the-portal"></a>Wie kann ich eine Datei in das Ausgabeverzeichnis schreiben und dann im Portal anzeigen?

Sie können das Ausgabeverzeichnis aus der Klasse `EntryScript` abrufen und in dieses schreiben. Zum Anzeigen der geschriebenen Dateien wählen Sie im Azure Machine Learning-Portal in der Schrittansicht „Ausführen“ die Registerkarte **Ausgaben + Protokolle** aus. Klicken Sie auf den Link **Datenausgaben**, und führen Sie dann die im Dialogfeld beschriebenen Schritte aus.

Verwenden Sie `EntryScript` in Ihrem Eingabeskript, wie im folgenden Beispiel gezeigt:

```python
from pathlib import Path
from azureml_user.parallel_run import EntryScript

def run(mini_batch):
    output_dir = Path(entry_script.output_dir)
    (Path(output_dir) / res1).write...
    (Path(output_dir) / res2).write...
```

## <a name="how-can-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>Wie kann ich eine seitliche Eingabe, z. B. eine oder mehrere Dateien mit einer Nachschlagetabelle, an alle meine Mitarbeiter weitergeben?

Der Benutzer kann mit dem Parameter side_inputs von ParalleRunStep Verweisdaten an das Skript übergeben. Alle als side_inputs bereitgestellten Datasets werden auf den einzelnen Workerknoten eingebunden. Der Benutzer kann den Speicherort der Eingabe durch Übergeben des Arguments erhalten.

Konstruieren Sie ein [Dataset](/python/api/azureml-core/azureml.core.dataset.dataset), das die Verweisdaten enthält, geben Sie einen lokalen Mount-Pfad an und registrieren Sie es in Ihrem Arbeitsbereich. Übergeben Sie es an den Parameter `side_inputs` für Ihr `ParallelRunStep`. Zusätzlich können Sie den zugehörigen Pfad im Abschnitt `arguments` hinzufügen, um einfach auf den eingebundenen Pfad zuzugreifen.

> [!NOTE]
> Verwenden Sie FileDatasets nur für side_inputs.

```python
local_path = "/tmp/{}".format(str(uuid.uuid4()))
label_config = label_ds.as_named_input("labels_input").as_mount(local_path)
batch_score_step = ParallelRunStep(
    name=parallel_step_name,
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    arguments=["--labels_dir", label_config],
    side_inputs=[label_config],
    parallel_run_config=parallel_run_config,
)
```

Danach können Sie in Ihrem Rückschlussskript (z. B. in Ihrer init()-Methode) wie folgt darauf zugreifen:

```python
parser = argparse.ArgumentParser()
parser.add_argument('--labels_dir', dest="labels_dir", required=True)
args, _ = parser.parse_known_args()

labels_path = args.labels_dir
```

## <a name="how-to-use-input-datasets-with-service-principal-authentication"></a>Wie werden Eingabedatasets mit Dienstprinzipalauthentifizierung verwendet?
Der Benutzer kann Eingabedatasets mit Dienstprinzipalauthentifizierung übergeben, die im Arbeitsbereich verwendet werden. Das Verwenden eines solchen Datasets in ParallelRunStep erfordert, dass das Dataset dafür registriert wird, damit die ParallelRunStep-Konfiguration erstellt werden kann.

```python
service_principal = ServicePrincipalAuthentication(
    tenant_id="***",
    service_principal_id="***",
    service_principal_password="***")

ws = Workspace(
    subscription_id="***",
    resource_group="***",
    workspace_name="***",
    auth=service_principal
    )

default_blob_store = ws.get_default_datastore() # or Datastore(ws, '***datastore-name***')
ds = Dataset.File.from_files(default_blob_store, '**path***')
registered_ds = ds.register(ws, '***dataset-name***', create_new_version=True)
```

## <a name="how-to-check-progress-and-analyze-it"></a>Überprüfen und Analysieren des Fortschritts
In diesem Abschnitt erfahren Sie, wie Sie den Fortschritt eines ParallelRunStep-Auftrags überprüfen und die Ursache für unerwartetes Verhalten untersuchen können.

### <a name="how-to-check-job-progress"></a>Überprüfen des Auftragsfortschritts
Neben dem Gesamtstatus von StepRun können die Anzahl der geplanten/verarbeiteten Minibatches und der Fortschritt der Ausgabegenerierung in `~/logs/job_progress_overview.<timestamp>.txt` angezeigt werden. Die Datei wird täglich gewechselt. Sie können die Datei mit dem größten Zeitstempel auf die neuesten Informationen überprüfen.

### <a name="what-should-i-check-if-there-is-no-progress-for-a-while"></a>Was sollte ich überprüfen, wenn es eine Weile keinen Fortschritt gibt?
Sie können zu `~/logs/sys/errror` wechseln, um festzustellen, ob eine Ausnahme vorliegt. Ist dies nicht der Fall, nimmt Ihr Eingabeskript wahrscheinlich viel Zeit in Anspruch. Sie können Fortschrittsinformationen in Ihrem Code drucken, um den zeitaufwendigen Teil zu finden, oder `"--profiling_module", "cProfile"` zu den `arguments` von `ParallelRunStep` hinzufügen, um ein Profil namens `<process_name>.profile` im Ordner `~/logs/sys/node/<node_id>` zu generieren.

### <a name="when-will-a-job-stop"></a>Wann wird ein Auftrag beendet?
Wenn der Auftrag nicht abgebrochen wird, wird er mit folgendem Status beendet:
- Abgeschlossen. Wenn alle Minibatches verarbeitet wurden und die Ausgabe für den Modus `append_row` generiert wurde.
- Fehler. Wenn `error_threshold` in [`Parameters for ParallelRunConfig`](#parameters-for-parallelrunconfig) überschritten wird oder während des Auftrags ein Systemfehler aufgetreten ist.

### <a name="where-to-find-the-root-cause-of-failure"></a>Wo ist die Ursache des Fehlers zu finden?
Sie können dem Lead in `~logs/job_result.txt` folgen, um die Ursache und das ausführliche Fehlerprotokoll zu finden.

### <a name="will-node-failure-impact-the-job-result"></a>Wirkt sich ein Knotenfehler auf das Auftragsergebnis aus?
Nicht, wenn im angegebenen Computecluster andere verfügbare Knoten vorhanden sind. Der Orchestrator startet einen neuen Knoten als Ersatz, und ParallelRunStep ist gegenüber einem solchen Vorgang resilient.

### <a name="what-happens-if-init-function-in-entry-script-fails"></a>Was geschieht, wenn die Funktion `init` im Eingabeskript fehlschlägt?
ParallelRunStep verfügt über einen Mechanismus, mit dem Wiederholungsversuche für einen bestimmten Zeitraum ausgeführt werden können, um nach vorübergehenden Problemen eine Wiederherstellung zu ermöglichen, ohne den Auftragsfehler zu lange zu verzögern. Der Mechanismus funktioniert wie folgt:
1. Wenn `init` nach dem Starten eines Knotens auf allen Agents kontinuierlich fehlschlägt, werden alle weiteren Versuche nach `3 * process_count_per_node` Fehlern aufgegeben.
2. Wenn `init` nach dem Starten des Auftrags auf allen Agents aller Knoten kontinuierlich fehlschlägt, werden alle weiteren Versuche aufgegeben, wenn der Auftrag länger als 2 Minuten ausgeführt wird und `2 * node_count * process_count_per_node` Fehler auftreten.
3. Wenn alle Agents länger als `3 * run_invocation_timeout + 30` Sekunden bei `init` hängenbleiben, würde der Auftrag fehlschlagen, weil es zu lange keinen Fortschritt gab.

### <a name="what-will-happen-on-outofmemory-how-can-i-check-the-cause"></a>Was geschieht bei OutOfMemory? Wie kann ich die Ursache überprüfen?
ParallelRunStep setzt den aktuellen Versuch, den Minibatch zu verarbeiten, auf den Fehlerstatus und versucht, den fehlgeschlagenen Prozess neu zu starten. Sie können `~logs/perf/<node_id>` überprüfen, um den speicherintensiven Prozess zu finden.

### <a name="why-do-i-have-a-lot-of-processnnn-files"></a>Warum habe ich viele processNNN-Dateien?
ParallelRunStep startet neue Workerprozesse als Ersatz für diejenigen, die auf ungewöhnliche Weise beendet wurden, und für jeden Prozess wird eine `processNNN`-Datei als Protokoll generiert. Wenn der Prozess jedoch aufgrund einer Ausnahme während der Funktion `init` des Benutzerskripts nicht ausgeführt werden konnte und sich dieser Fehler kontinuierlich `3 * process_count_per_node` Male wiederholte, wird kein neuer Workerprozess gestartet.

## <a name="next-steps"></a>Nächste Schritte

* Sehen Sie sich diese [Jupyter-Notebooks zur Veranschaulichung von Azure Machine Learning-Pipelines](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines) an.

* Hilfe zum Paket [azureml-pipeline-steps](/python/api/azureml-pipeline-steps/azureml.pipeline.steps) finden Sie in der SDK-Referenz.

* Lesen Sie die [Referenzdokumentation](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunconfig) für die Klasse „ParallelRunConfig“ und die [Dokumentation](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunstep) für die Klasse „ParallelRunStep“.

* Folgen Sie dem [erweiterten Tutorial](tutorial-pipeline-batch-scoring-classification.md) zur Verwendung von Pipelines mit ParallelRunStep. Das Tutorial zeigt, wie eine andere Datei als seitliche Eingabe übergeben werden kann.
