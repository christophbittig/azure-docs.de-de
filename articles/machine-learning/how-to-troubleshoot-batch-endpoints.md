---
title: Problembehandlung für Batchendpunkte (Vorschau)
titleSuffix: Azure Machine Learning
description: Enthält Tipps zur erfolgreichen Verwendung von Batchendpunkten.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: troubleshooting
ms.custom: troubleshooting, devplatv2
ms.reviewer: laobri
ms.author: tracych
author: tracych
ms.date: 05/05/2021
ms.openlocfilehash: ba0dfb7c86250a55de5967bbffafd40823709dce
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/04/2021
ms.locfileid: "129425788"
---
# <a name="troubleshooting-batch-endpoints-preview"></a>Problembehandlung für Batchendpunkte (Vorschau)

In diesem Artikel wird beschrieben, wie Sie die Problembehandlung durchführen und häufige Fehler beheben oder umgehen, die bei der Verwendung von [Batchendpunkten](how-to-use-batch-endpoint.md) (Vorschau) für die Batchbewertung auftreten können.

 [!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

Die folgende Tabelle enthält Informationen zu häufigen Problemen und den zugehörigen Lösungen, die die Entwicklung und Nutzung von Batchendpunkten betreffen.

| Problem | Mögliche Lösung |
|--|--|
| Codekonfiguration oder Umgebung fehlt. | Stellen Sie sicher, dass Sie das Bewertungsskript und eine Umgebungsdefinition bereitstellen, wenn Sie ein anderes Modell als das MLflow-Modell verwenden. Die Bereitstellung ohne Code wird nur für das MLflow-Modell unterstützt. Weitere Informationen finden Sie im Artikel zum [Nachverfolgen von ML-Modellen mit MLflow und Azure Machine Learning](how-to-use-mlflow.md).|
| Fehlendes Update für das Modell, den Code, die Umgebung und die Computeressourcen eines vorhandenen Batchendpunkts. | Erstellen Sie einen neuen Batchendpunkt mit einem neuen Namen. Die Aktualisierung dieser Ressourcen für einen vorhandenen Batchendpunkt wird noch nicht unterstützt. |
| Die Ressource wurde nicht gefunden. | Stellen Sie sicher, dass Sie `--type batch` in Ihrem CLI-Befehl verwenden. Wenn dieses Argument nicht angegeben ist, wird der Standardtyp `online` verwendet.|
| Nicht unterstützte Eingabedaten. | Für Batchendpunkte werden drei Arten von Eingabedaten akzeptiert: 1) registrierte Daten, 2) Daten in der Cloud und 3) lokale Daten. Stellen Sie sicher, dass Sie die richtige Art von Daten verwenden. Weitere Informationen finden Sie unter [Verwenden von Batchendpunkten (Vorschau) für die Batchbewertung](how-to-use-batch-endpoint.md).|
| Der Name des bereitgestellten Endpunkts ist bereits vorhanden oder wurde gelöscht. | Erstellen Sie einen neuen Batchendpunkt mit einem neuen Namen. Mit dem Befehl `endpoint delete` wird der Endpunkt für das Löschen gekennzeichnet. Dieser Name kann nicht noch einmal verwendet werden, um einen neuen Endpunkt in derselben Region zu erstellen. |
| Die Ausgabe ist bereits vorhanden. | Stellen Sie beim Konfigurieren Ihres eigenen Ausgabespeicherorts sicher, dass Sie für jeden Endpunktaufruf eine neue Ausgabe bereitstellen. |

##  <a name="scoring-script-requirements"></a>Anforderungen an das Bewertungsskript

Wenn Sie kein MLflow-Modell verwenden, müssen Sie ein Bewertungsskript bereitstellen. Das Bewertungsskript muss zwei Funktionen enthalten:

- `init()`: Verwenden Sie diese Funktion für alle aufwendigen oder allgemeinen Vorbereitungsmaßnahmen für den späteren Rückschluss. Ein Beispiel wäre etwa das Laden des Modells in ein globales Objekt. Diese Funktion wird einmal am Anfang des Prozesses aufgerufen.
-  `run(mini_batch)`: Diese Funktion wird für jede `mini_batch`-Instanz ausgeführt.
    -  `mini_batch`: Der Wert `mini_batch` umfasst eine Liste mit Dateipfaden.
    -  `response`: Von der `run()`-Methode sollte ein Pandas-Datenrahmen oder ein Array zurückgegeben werden. Diese zurückgegebenen Elemente werden an die allgemeine Ausgabedatei angefügt. Die zurückgegebenen Ausgabeelemente stehen jeweils für eine erfolgreiche Ausführung eines Eingabeelements aus dem Eingabeminibatch. Stellen Sie sicher, dass das Ausführungsergebnis genügend Daten für eine Zuordnung zwischen einer einzelnen Eingabe und der Ausgabe des Ausführungsergebnisses enthält. Die Ausführungsausgabe wird in die Ausgabedatei geschrieben. Da hierbei aber nicht unbedingt die Reihenfolge eingehalten wird, müssen Sie einen Schlüssel in der Ausgabe verwenden, um sie der richtigen Eingabe zuzuordnen.

:::code language="python" source="~/azureml-examples-cli-preview/cli/endpoints/batch/mnist/code/digit_identification.py" :::

## <a name="understanding-logs-of-a-batch-scoring-job"></a>Grundlegendes zu Protokollen eines Batchbewertungsauftrags

### <a name="get-logs"></a>Abrufen von Protokollen

Nachdem Sie einen Batchendpunkt mit der Azure CLI oder per REST aufgerufen haben, wird der Batchbewertungsauftrag asynchron ausgeführt. Es gibt zwei Optionen, um die Protokolle für einen Batchbewertungsauftrag abzurufen.

1\. Option: Streamen von Protokollen an die lokale Konsole

Sie können den folgenden Befehl ausführen, um systemseitig generierte Protokolle an Ihre Konsole zu streamen. Nur Protokolle im Ordner `azureml-logs` werden gestreamt.

```bash
az ml job stream -name <job_name>
```

2\. Option: Anzeigen von Protokollen in Studio 

Führen Sie Folgendes aus, um den Link für die Ausführung in Studio abzurufen: 

```azurecli
az ml job show --name <job_name> --query interaction_endpoints.Studio.endpoint -o tsv
```

1. Öffnen Sie den Auftrag in Studio mit dem Wert, der vom obigen Befehl zurückgegeben wird. 
1. Wählen Sie die Option für die Batchbewertung (**batchscoring**) aus.
1. Öffnen Sie die Registerkarte **Ausgaben + Protokolle**. 
1. Wählen Sie die Protokolle aus, die Sie überprüfen möchten.

### <a name="understand-log-structure"></a>Grundlegendes zur Protokollstruktur

Es gibt zwei Protokollordner der obersten Ebene: `azureml-logs` und `logs`. 

Die Datei `~/azureml-logs/70_driver_log.txt` enthält Informationen des Controllers, von dem das Bewertungsskript gestartet wird.  

Aufgrund der verteilten Struktur von Batchbewertungsaufträgen sind Protokolle aus unterschiedlichen Quellen vorhanden. Es werden aber zwei kombinierte Dateien mit allgemeinen Informationen erstellt: 

- `~/logs/job_progress_overview.txt`: Diese Datei enthält allgemeine Informationen zur Anzahl von bisher erstellten und bisher verarbeiteten Minibatches (auch als „Aufgaben“ bezeichnet). Wenn die Verarbeitung der Minibatches abgeschlossen ist, werden die Ergebnisse des Auftrags im Protokoll aufgezeichnet. Wenn bei dem Auftrag ein Fehler aufgetreten ist, wird die Fehlermeldung angezeigt, und es wird beschrieben, wo mit der Problembehandlung begonnen werden sollte.

- `~/logs/sys/master_role.txt`: Diese Datei stellt die Prinzipalknotenansicht (auch als Orchestrator bezeichnet) des laufenden Auftrags bereit. Dieses Protokoll enthält Informationen zur Aufgabenerstellung, zur Statusüberwachung und zum Ausführungsergebnis.

Für ein präzises Verständnis von Fehlern in Ihrem Skript gibt es Folgendes:

- `~/logs/user/error.txt`: Diese Datei wird versuchen, die Fehler in Ihrem Skript zusammenzufassen.

Weitere Informationen zu Fehlern in Ihrem Skript finden Sie hier:

- `~/logs/user/error/`: Diese Datei enthält vollständige Stapelüberwachungen von Ausnahmen, die beim Laden und Ausführen des Eingabeskripts ausgelöst werden.

Wenn Sie detailliert erfahren möchten, wie die einzelnen Knoten das Bewertungsskript ausgeführt haben, sehen Sie sich die jeweiligen Prozessprotokolle für jeden Knoten an. Die Prozessprotokolle befinden sich im Ordner `sys/node`, gruppiert nach Workerknoten:

- `~/logs/sys/node/<ip_address>/<process_name>.txt`: Diese Datei enthält ausführliche Informationen zu den einzelnen Minibatches, die von einem Worker abgerufen oder abgeschlossen werden. Für jeden Minibatch umfasst diese Datei Folgendes:

    - Die IP-Adresse und die PID des Workerprozesses. 
    - Die Gesamtzahl der Elemente, die Anzahl von erfolgreich verarbeiteten Elementen und die Anzahl von Elementen mit Fehlern.
    - Die Startzeit, Dauer, Verarbeitungszeit und die Zeit der run-Methode.

Sie können für jeden Knoten auch die Ergebnisse regelmäßiger Überprüfungen der Ressourcennutzung anzeigen. Die Protokoll- und Setupdateien befinden sich in diesem Ordner:

- `~/logs/perf`: Legen Sie `--resource_monitor_interval` fest, um das Prüfintervall in Sekunden zu ändern. Das Standardintervall ist `600` (ungefähr 10 Minuten). Um die Überwachung zu beenden, legen Sie den Wert auf `0` fest. Jeder Ordner `<ip_address>` enthält Folgendes:

    - `os/`: Informationen zu allen laufenden Prozessen im Knoten. Bei einer Prüfung wird ein Betriebssystembefehl ausgeführt und das Ergebnis in einer Datei gespeichert. Unter Linux ist der Befehl `ps`.
        - `%Y%m%d%H`: Der Name des Unterordners ist die Uhrzeit zur vollen Stunde.
            - `processes_%M`: Die Datei endet mit der Minute der Prüfzeit.
    - `node_disk_usage.csv`: Detaillierte Datenträgernutzung des Knotens.
    - `node_resource_usage.csv`: Übersicht über die Ressourcennutzung des Knotens.
    - `processes_resource_usage.csv`: Übersicht über die Ressourcennutzung der einzelnen Prozesse.

### <a name="how-to-log-in-scoring-script"></a>Protokollieren im Bewertungsskript

Sie können in Ihrem Bewertungsskript die Python-Protokollierung verwenden. Die Protokolle werden unter `logs/user/stdout/<node_id>/processNNN.stdout.txt` gespeichert. 

```python
import argparse
import logging

# Get logging_level
arg_parser = argparse.ArgumentParser(description="Argument parser.")
arg_parser.add_argument("--logging_level", type=str, help="logging level")
args, unknown_args = arg_parser.parse_known_args()
print(args.logging_level)

# Initialize python logger
logger = logging.getLogger(__name__)
logger.setLevel(args.logging_level.upper())
logger.info("Info log statement")
logger.debug("Debug log statement")
```