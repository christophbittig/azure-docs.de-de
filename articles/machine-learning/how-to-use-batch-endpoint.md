---
title: Verwenden von Batchendpunkten für die Batchbewertung
titleSuffix: Azure Machine Learning
description: In diesem Artikel erfahren Sie, wie Sie einen Batchendpunkt für die kontinuierliche Batchbewertung großer Datenmengen erstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: conceptual
author: tracych
ms.author: tracych
ms.reviewer: laobri
ms.date: 10/21/2021
ms.custom: how-to, devplatv2
ms.openlocfilehash: 481227a747fca327f107049734a69008aa3c3bcf
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063777"
---
# <a name="use-batch-endpoints-preview-for-batch-scoring"></a>Verwenden von Batchendpunkten (Vorschau) für die Batchbewertung

Lernen Sie, wie Sie Batchendpunkte (Vorschau) für die Batchbewertung verwenden. Batchendpunkte vereinfachen das Hosten Ihrer Modelle für die Batchbewertung, sodass Sie sich auf maschinelles Lernen konzentrieren können, anstatt sich mit der Infrastruktur zu befassen. Weitere Informationen finden Sie unter [Was sind Azure Machine Learning-Endpunkte (Vorschau)?](concept-endpoints.md).

In diesem Artikel lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines Batchendpunkts und einer Standard-Batchbereitstellung
> * Starten eines Batchbewertungsauftrags mithilfe der Azure CLI
> * Überwachen des Ausführungsstatus des Batchbewertungsauftrags und Überprüfen der Bewertungsergebnisse
> * Bereitstellen eines neuen MLflow-Modells mit automatisch generiertem Code und Umgebung auf einem vorhandenen Endpunkt ohne Auswirkungen auf den vorhandenen Flow
> * Testen der neuen Bereitstellung und Festlegen als Standardbereitstellung
> * Löschen des Endpunkts und der Bereitstellung, die nicht verwendet werden

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Für die Verwendung von Azure Machine Learning ist ein Azure-Abonnement erforderlich. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://azure.microsoft.com/free/) noch heute aus.

* Installieren Sie die Azure CLI und die Erweiterung `ml`. Befolgen Sie die Installationsschritte unter [Installieren, Einrichten und Verwenden der CLI (v2) (Vorschau)](how-to-configure-cli.md).

* Erstellen Sie eine Azure-Ressourcengruppe, wenn Sie noch keine haben und Sie (oder der von Ihnen verwendete Dienstprinzipal) die Berechtigung `Contributor` benötigen. Weitere Informationen zur Erstellung einer Ressourcengruppe finden Sie unter [Installieren, Einrichten und Verwenden der CLI (v2) (Vorschau)](how-to-configure-cli.md). 

* Erstellen Sie einen Azure Machine Learning-Arbeitsbereich, sofern Sie noch keinen besitzen. Weitere Informationen zur Erstellung eines Arbeitsbereichs finden Sie unter [Installieren, Einrichten und Verwenden der CLI (v2) (Vorschau)](how-to-configure-cli.md). 

* Konfigurieren Sie Ihren Standardarbeitsbereich und Ihre Ressourcengruppe für die Azure CLI. Für Machine Learning-CLI-Befehle sind die Parameter `--workspace/-w` und `--resource-group/-g` erforderlich. Durch das Konfigurieren der Standardwerte wird vermieden, dass die Werte mehrmals übergeben werden. Sie können diese über die Befehlszeile überschreiben. Führen Sie den folgenden Code aus, um Ihre Standardwerte festzulegen. Weitere Informationen finden Sie unter [Installieren, Einrichten und Verwenden der CLI (v2) (Vorschauversion)](how-to-configure-cli.md).

```azurecli
az account set -s "<subscription ID>"
az configure --defaults group="<resource group>" workspace="<workspace name>" location="<location>"
```

### <a name="clone-the-example-repository"></a>Klonen des Beispielrepositorys

Führen Sie die folgenden Befehle aus, um das [AzureML-Beispielrepository](https://github.com/Azure/azureml-examples) zu klonen und zum `cli`-Verzeichnis zu wechseln. In diesem Artikel werden die Objekte in `/cli/endpoints/batch` verwendet. Das End-to-End-Arbeitsbeispiel ist `/cli/batch-score.sh`.

```azurecli
git clone https://github.com/Azure/azureml-examples 
cd azureml-examples/cli
```

Legen Sie den Namen Ihres Endpunkts fest. Ersetzen Sie `YOUR_ENDPOINT_NAME` durch einen innerhalb einer Azure-Region eindeutigen Namen.

Führen Sie für Unix den folgenden Befehl aus:

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="set_variables" :::

Führen Sie für Windows den folgenden Befehl aus:

```azurecli
set ENDPOINT_NAME="<YOUR_ENDPOINT_NAME>"
```

> [!NOTE]
> Die Namen der Batchendpunkte müssen innerhalb einer Azure-Region eindeutig sein. Beispielsweise kann es nur einen Batchendpunkt mit dem Namen „mybatchendpoint“ in westus2 geben.

### <a name="create-compute"></a>Erstellen von Computeressourcen

Batchendpunkte werden nicht lokal, sondern ausschließlich auf Cloud Computing-Ressourcen ausgeführt. Eine Cloud Computing-Ressource ist ein wiederverwendbarer Cluster virtueller Computer. Führen Sie den folgenden Code aus, um einen Azure Machine Learning-Computecluster zu erstellen. In den folgenden Beispielen in diesem Artikel wird das hier erstellte Computecluster mit dem Namen `batch-cluster` verwendet. Passen Sie diese nach Bedarf an und verweisen Sie mit `azureml:<your-compute-name>` auf Ihre Computeressourcen.

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="create_compute" :::

> [!NOTE]
> Zu diesem Zeitpunkt werden Ihnen noch keine Computeressourcen in Rechnung gestellt, da der Cluster bei 0 Knoten verbleibt, bis ein Batchendpunkt aufgerufen und ein Batchbewertungsauftrag übermittelt wird. Erfahren Sie mehr über das [Verwalten und Optimieren von Kosten für AmlCompute](how-to-manage-optimize-cost.md#use-azure-machine-learning-compute-cluster-amlcompute).

## <a name="understand-batch-endpoints-and-batch-deployments"></a>Verstehen von Batchendpunkten und Batchbereitstellungen

Ein Batchendpunkt ist ein HTTPS-Endpunkt, den Clients aufrufen können, um einen Batchbewertungsauftrag auszulösen. Ein Batchbewertungsauftrag ist ein Auftrag, der mehrere Eingaben auswertet (weitere Informationen finden Sie unter [Was sind Batchendpunkte?](concept-endpoints.md#what-are-batch-endpoints-preview)). Eine Batchbereitstellung ist eine Gruppe von Computeressourcen, von denen das Modell gehostet wird, das die eigentlichen Batchbewertungen durchführt. Ein Batchendpunkt kann über mehrere Batchbereitstellungen verfügen. 

> [!TIP]
> Eine der Batchbereitstellungen dient als Standardbereitstellung für den Endpunkt. Die Standardbereitstellung wird zum Ausführen der eigentlichen Batchbewertung verwendet, wenn der Endpunkt aufgerufen wird. Weitere Informationen über [Batchendpunkte und Batchbereitstellungen](concept-endpoints.md#what-are-batch-endpoints-preview).

Die folgende YAML-Datei definiert einen Batchendpunkt, den Sie in den CLI-Befehl für die [Batchendpunkterstellung](#create-a-batch-endpoint) integrieren können. Im Repository befindet sich diese Datei unter `/cli/endpoints/batch/batch-endpoint.yml`.

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/batch/batch-endpoint.yml":::

In der folgenden Tabelle werden die wichtigsten Eigenschaften des Endpunkt-YAML beschrieben. Das vollständige YAML-Schema für den Batchendpunkt finden Sie unter [CLI (v2) Batchendpunkt-YAML-Schema](./reference-yaml-endpoint-batch.md).

| Schlüssel | BESCHREIBUNG |
| --- | ----------- |
| `$schema` | (Optional) Das YAML-Schema. Sie können das Schema aus dem obigen Beispiel in einem Browser betrachten, um alle verfügbaren Optionen für eine YAML-Batchendpunktdatei anzuzeigen. |
| `name` | Der Name des Batchendpunkts. Muss auf Azure-Regionsebene eindeutig sein.|
| `auth_mode` | Die Authentifizierungsmethode für den Batchendpunkt. Derzeit wird nur die tokenbasierte Authentifizierung von Azure Active Directory (`aad_token`) unterstützt. |
| `defaults.deployment_name` | Der Name der Bereitstellung, die als Standardbereitstellung für den Endpunkt dient. |

Um eine Batchbereitstellung zu erstellen, benötigen Sie jedes der folgenden Elemente:
* Modelldateien (oder ein registriertes Modell in Ihrem Arbeitsbereich, auf das mit `azureml:<model-name>:<model-version>` verwiesen wird). 
* den Code zur Bewertung des Modells.
* die Umgebung, in der das Modell ausgeführt wird. Dabei kann es sich um ein Docker-Image mit Conda-Abhängigkeiten oder um eine Umgebung handeln, die bereits in Ihrem Arbeitsbereich registriert ist und auf die mit `azureml:<environment-name>:<environment-version>` verwiesen wird.
* das vorab erstellte Compute, auf das mithilfe von `azureml:<compute-name>` und den Ressourceneinstellungen verwiesen wird.

Weitere Informationen zum Verweisen auf eine Azure ML-Entität finden Sie unter [Verweisen auf eine Azure ML-Entität](reference-yaml-core-syntax.md#referencing-an-azure-ml-entity).

Das Beispielrepository enthält alle erforderlichen Dateien. Die folgende YAML-Datei definiert eine Batchbereitstellung mit allen erforderlichen Eingaben und optionalen Einstellungen. Sie können diese Datei in Ihrem CLI-Befehl zum [Erstellen Ihrer Batchbereitstellung](#create-a-batch-deployment) verwenden. Im Repository befindet sich diese Datei unter `/cli/endpoints/batch/nonmlflow-deployment.yml`. 

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/batch/nonmlflow-deployment.yml":::

In der folgenden Tabelle werden die wichtigsten Eigenschaften des Bereitstellungs-YAML beschrieben. Das vollständige YAML-Schema für die Batchbereitstellung finden Sie unter [CLI (v2) Batchbereitstellungs-YAML-Schema](./reference-yaml-deployment-batch.md).

| Schlüssel | BESCHREIBUNG |
| --- | ----------- |
| `$schema` | (Optional) Das YAML-Schema. Sie können das Schema aus dem obigen Beispiel in einem Browser anzeigen lassen, um alle verfügbaren Optionen für eine YAML-Batchbereitstellungsdatei zu sehen. |
| `name` | Der Name der Bereitstellung |
| `endpoint_name` | Der Name des Endpunkts, unter dem die Bereitstellung erstellt werden soll. |
| `model` | Das Modell, das für die Batchbewertung verwendet werden soll. Im Beispiel wird ein Inline-Modell mit `local_path` definiert. Modelldateien werden automatisch hochgeladen und mit einem automatisch generierten Namen und einer automatisch generierten Version registriert. Befolgen Sie das [Modellschema](reference-yaml-model.md#yaml-syntax) für weitere Optionen. Als bewährtes Verfahren für Produktionsszenarien sollten Sie das Modell separat erstellen und hier darauf verweisen. Um auf ein bestehendes Modell zu verweisen, verwenden Sie die Syntax `azureml:<model-name>:<model-version>`. |
| `code_configuration.code.local_path` | Das Verzeichnis, das den gesamten Python-Quellcode für die Bewertung des Modells enthält. |
| `code_configuration.scoring_script` | Die Python-Datei im obigen Verzeichnis. Diese Datei muss über eine Funktion vom Typ `init()` und über eine Funktion vom Typ `run()` verfügen. Verwenden Sie die Funktion `init()` für die kostenintensive oder für die allgemeine Vorbereitung (z. B. wenn Sie das Modell in den Arbeitsspeicher laden). Die Funktion `init()` wird nur einmal zu Beginn des Prozesses aufgerufen. Verwenden Sie `run(mini_batch)`, um die einzelnen Einträge zu bewerten. Der Wert von `mini_batch` ist eine Liste mit Dateipfaden. Die Funktion `run()` sollte einen Pandas-Datenrahmen oder ein Array zurückgeben. Jedes zurückgegebene Element deutet auf eine erfolgreiche Ausführung eines Eingabeelements im `mini_batch` hin. Stellen Sie sicher, dass genügend Daten in Ihrer `run()`-Antwort enthalten sind, damit die Eingabe mit der Ausgabe korreliert werden kann. |
| `environment` | Die Umgebung zum Bewerten des Modells. Im Beispiel wird eine Inline-Umgebung mit `conda_file` und `image` definiert. Die `conda_file`-Abhängigkeiten werden zusätzlich zum `image` installiert. Die Umgebung wird automatisch mit einem automatisch generierten Namen und einer automatisch generierten Version registriert. Befolgen Sie das [Umgebungsschema](reference-yaml-environment.md#yaml-syntax) für weitere Optionen. Als bewährtes Verfahren für Produktionsszenarien sollten Sie die Umgebung separat erstellen und hier referenzieren. Verwenden Sie die Syntax `azureml:<environment-name>:<environment-version>`, um auf eine vorhandene Umgebung zu verweisen. |
| `compute` | Die Computeressource zum Ausführen der Batchbewertung. Im Beispiel wird der am Anfang erstellte `batch-cluster` verwendet, auf den mithilfe der Syntax `azureml:<compute-name>` verwiesen wird. |
| `resources.instance_count` | Die Anzahl der Instanzen, die für jeden Batchbewertungsauftrag verwendet werden sollen. |
| `max_concurrency_per_instance` | (Optional) Die maximale Anzahl von parallelen `scoring_script`-Läufen pro Instanz. |
| `mini_batch_size` | (Optional) Die Anzahl von Dateien, die von `scoring_script` in einem einzelnen Aufruf von `run()` verarbeitet werden können. |
| `output_action` | (Optional) Gibt an, wie die Ausgabe in der Ausgabedatei organisiert werden soll. `append_row` führt alle von `run()` zurückgegebenen Ausgabeergebnisse in einer einzelnen Datei mit dem Namen `output_file_name` zusammen. `summary_only` führt die Ausgabeergebnisse nicht zusammen, sondern berechnet nur `error_threshold`. |
| `output_file_name` | (Optional) Der Name der Ausgabedatei der Batchbewertung für `append_row` `output_action`. |
| `retry_settings.max_retries` | (Optional) Die maximale Anzahl von Versuchen bei einer nicht erfolgreichen Bewertungsskriptausführung (`scoring_script` `run()`). |
| `retry_settings.timeout` | (Optional) Die Zeitüberschreitung in Sekunden für `scoring_script` `run()` für das Erfassen eines Mini-Batches. |
| `error_threshold` | (Optional) Die Anzahl von Eingabedatei-Bewertungsfehlern, die ignoriert werden sollen. Wenn die Fehlerzahl für die gesamte Eingabe diesen Wert übersteigt, wird der Batchbewertungsauftrag abgebrochen. Im Beispiel wird `-1` verwendet. Dadurch wird angegeben, dass eine beliebige Anzahl von Fehlern zulässig ist, ohne den Batchbewertungsauftrag abzubrechen. | 
| `logging_level` | (Optional) Die Ausführlichkeit des Protokolls. Mögliche Werte nach zunehmender Ausführlichkeit sind „WARNING“ (Warnung), „INFO“ (Information) und „DEBUG“ (Debuggen). |

###  <a name="understand-the-scoring-script"></a>Grundlegendes zum Bewertungsskript

Wie bereits erwähnt, muss das `code_configuration.scoring_script` zwei Funktionen enthalten:

- `init()`: Verwenden Sie diese Funktion für kostenintensive oder allgemeine Vorbereitungen. Ein Beispiel wäre etwa das Laden des Modells in ein globales Objekt. Diese Funktion wird einmal am Anfang des Prozesses aufgerufen.
-  `run(mini_batch)`: Diese Funktion wird für jedes `mini_batch` aufgerufen, um die eigentliche Bewertung durchzuführen. 
    -  `mini_batch`: Der Wert `mini_batch` umfasst eine Liste mit Dateipfaden.
    -  `response`: Von der `run()`-Methode sollte ein Pandas-Datenrahmen oder ein Array zurückgegeben werden. Jedes zurückgegebene Ausgabeelement deutet auf eine erfolgreiche Ausführung eines Eingabeelements im Eingabe-`mini_batch` hin. Stellen Sie sicher, dass genügend Daten (z. B. ein Bezeichner für jedes Eingabeelement) in der Antwort `run()` enthalten sind, damit eine Eingabe mit einem Ausgabeergebnis korreliert werden kann. 

Im Beispiel wird `/cli/endpoints/batch/mnist/code/digit_identification.py` verwendet. Das Modell wird aus `AZUREML_MODEL_DIR` in `init()` geladen. Dies ist der Pfad zum Modellordner, der während der Bereitstellung erstellt wurde. `run(mini_batch)` durchläuft jede Datei in `mini_batch`, führt die eigentliche Modellbewertung durch und gibt dann Ausgabeergebnisse zurück.

## <a name="deploy-with-batch-endpoints-and-run-batch-scoring"></a>Bereitstellen mit Batchendpunkten und Ausführen der Batchbewertung

Nun stellen wir das Modell mit Batchendpunkten bereit und führen die Batchbewertung aus.

### <a name="create-a-batch-endpoint"></a>Erstellen eines Batchendpunkts

Die einfachste Möglichkeit zum Erstellen eines Batchendpunkts besteht darin, den folgenden Code auszuführen, für den nur ein `--name` bereitgestellt werden muss.

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="create_batch_endpoint" :::

Sie können einen Batchendpunkt auch mithilfe einer YAML-Datei erstellen. Fügen Sie im obigen Befehl den `--file`-Parameter hinzu und geben Sie den YAML-Dateipfad an.

### <a name="create-a-batch-deployment"></a>Erstellen einer Batchbereitstellung

Führen Sie den folgenden Code aus, um eine Batchbereitstellung mit dem Namen `nonmlflowdp` unter dem Batchendpunkt zu erstellen und diese als Standardbereitstellung festzulegen. 

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="create_batch_deployment_set_default" :::

> [!TIP]
> Der `--set-default`-Parameter legt die neu erstellte Bereitstellung als Standardbereitstellung des Endpunkts fest. Insbesondere beim erstmaligen Erstellen einer Bereitstellung ist dies eine praktische Möglichkeit, um eine neue Standardbereitstellung des Endpunkts zu erstellen. Als bewährte Methode für Produktionsszenarien können Sie auch eine neue Bereitstellung erstellen, die nicht als Standard festgelegt wird. Anschließend können Sie diese überprüfen und die Standardbereitstellung später entsprechend aktualisieren. Weitere Informationen finden Sie im Abschnitt [Bereitstellen eines neuen Modells](#deploy-a-new-model).

### <a name="check-batch-endpoint-and-deployment-details"></a>Überprüfen der Batchendpunkt- und Bereitstellungsdetails

Verwenden Sie `show`, um die Batchendpunkt- und Bereitstellungsdetails zu überprüfen.

Führen Sie den folgenden Code aus, um eine Batchbereitstellung zu überprüfen:

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="check_batch_deployment_detail" :::

Führen Sie den folgenden Code aus, um einen Batchendpunkt zu überprüfen: Da die neu erstellte Bereitstellung als Standardbereitstellung festgelegt ist, sollte als `defaults.deployment_name` der Antwort `nonmlflowdp` angezeigt werden.

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="check_batch_endpooint_detail" :::

### <a name="invoke-the-batch-endpoint-to-start-a-batch-scoring-job"></a>Aufrufen eines Batchendpunkts zum Starten eines Batchbewertungsauftrags

Durch das Aufrufen eines Batchendpunkts wird ein Batchbewertungsauftrag ausgelöst. Von der Aufrufantwort wird ein Auftrag `name` zurückgegeben. Dieser kann verwendet werden, um den Fortschritt der Batchbewertung nachzuverfolgen. Der Batchbewertungsauftrag wird in einem bestimmten Zeitraum ausgeführt. Er teilt die gesamten Eingaben in mehrere `mini_batch` auf und verarbeitet diese parallel auf dem Computecluster. Ein `scoring_scrip` `run()` benötigt ein `mini_batch` und verarbeitet dieses im Rahmen eines Prozesses auf einer Instanz. Die Ausgaben des Batchbewertungsauftrags werden im Cloudspeicher gespeichert, und zwar entweder im Standardblobspeicher des Arbeitsbereichs oder in dem von Ihnen angegebenen Speicher.

#### <a name="invoke-the-batch-endpoint-with-different-input-options"></a>Aufrufen des Batchendpunkts mit verschiedenen Eingabeoptionen

Sie können entweder die CLI oder REST verwenden, um den Endpunkt aufzurufen (`invoke`). Informationen über REST finden Sie unter [Verwenden von Batchendpunkten (Vorschauversion) mit REST](how-to-deploy-batch-with-rest.md).

Für die Angabe der Dateneingaben in CLI `invoke` stehen drei Optionen zur Verfügung:

* __Option 1: Daten in der Cloud__

    Verwenden Sie `--input-path`, um einen Ordner (Präfix `folder:` verwenden) oder eine Datei (Präfix `file:` verwenden) in einem registrierten Azure Machine Learning-Datenspeicher anzugeben. Die Syntax für den Daten-URI ist entweder `folder:azureml://datastores/<datastore-name>/paths/<data-path>/` für einen Ordner oder `file:azureml://datastores/<datastore-name>/paths/<data-path>/<file-name>` für eine bestimmte Datei. Weitere Informationen zum Daten-URI finden Sie unter [Azure Machine Learning-Datenverweis-URI](reference-yaml-core-syntax.md#azure-ml-data-reference-uri).

    Im Beispiel werden öffentlich verfügbare Daten in einem Ordner von `https://pipelinedata.blob.core.windows.net/sampledata/mnist` verwendet, der tausende von handschriftlichen Ziffern enthält. Der Name des Batchbewertungsauftrags wird von der Aufrufantwort zurückgegeben. Führen Sie den folgenden Code aus, um den Batchendpunkt mit diesen Daten aufzurufen. `--query name` wird hinzugefügt, um nur den Auftragsnamen aus der Aufrufantwort zurückzugeben. Später wird es verwendet, um den [Ausführungsstatus des Batchbewertungsauftrags zu überwachen](#monitor-batch-scoring-job-execution-progress) und um [Batchbewertungsergebnisse zu überprüfen](#check-batch-scoring-results). Entfernen Sie `--query name -o tsv`, wenn Sie die vollständige Aufrufantwort anzeigen möchten. Weitere Informationen über den Parameter `--query` finden Sie unter [Abfragen der Azure CLI-Befehlsausgabe](/cli/azure/query-azure-cli).

    :::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="start_batch_scoring_job" :::

* __Option 2: Registriertes Dataset__

    Verwenden Sie `--input-dataset`, um ein registriertes Azure Machine Learning-Dataset zu übergeben. Um ein Dataset zu erstellen, suchen Sie nach entsprechenden Anweisungen in `az ml dataset create -h` und befolgen Sie das [Dataset-Schema](reference-yaml-dataset.md#yaml-syntax).

    > [!NOTE]
    > FileDataset, das mit der vorherigen Version der CLI und des Python-SDK erstellt wurde, kann ebenfalls verwendet werden. TabularDataset wird nicht unterstützt. 

    ```azurecli
    az ml batch-endpoint invoke --name $ENDPOINT_NAME --input-dataset azureml:<dataset-name>:<dataset-version>
    ```

* __Option 3: Lokal gespeicherte Daten__

    Verwenden Sie `--input-local-path`, um lokal gespeicherte Datendateien zu übergeben. Die Datendateien werden automatisch hochgeladen und mit einem automatisch generierten Namen und einer automatisch generierten Version registriert.

    ```azurecli
    az ml batch-endpoint invoke --name $ENDPOINT_NAME --input-local-path <local-path>
    ```

#### <a name="configure-the-output-location-and-overwrite-settings"></a>Konfigurieren des Ausgabespeicherorts und Überschreiben von Einstellungen

Die Ergebnisse der Batchbewertung werden standardmäßig im Standardblobspeicher des Arbeitsbereichs in einem nach dem Auftrag benannten Ordner gespeichert. Bei dem Auftragsnamen handelt es sich um systemseitig generierte GUID. Beim Aufrufen eines Batchendpunkts können Sie konfigurieren, wo die Bewertungsausgaben gespeichert werden sollen. Verwenden Sie `--output-path`, um `folder:` in einem registrierten Azure Machine Learning-Dataset zu konfigurieren. Die Syntax für `--output-path` `folder:` ist identisch mit `--input-path` `folder:`. Verwenden Sie `--set output_file_name=<your-file-name>`, um einen neuen Ausgabedateinamen zu konfigurieren, wenn Sie eine Ausgabedatei mit allen Bewertungsergebnissen bevorzugen (Angabe von `output_action=append_row` in Ihrer YAML-Bereitstellung).

> [!IMPORTANT]
> Der Ausgabespeicherort muss eindeutig sein. Ist die Ausgabedatei bereits vorhanden, tritt für den Batchbewertungsauftrag ein Fehler auf. 

Beim Aufrufen eines Batchendpunkts können einige Einstellungen überschrieben werden, um die Computeressource optimal zu nutzen und die Leistung zu verbessern: 

* Verwenden Sie `--instance-count`, um `instance_count` zu überschreiben. Für eine größere Menge von Dateneingaben empfiehlt es sich beispielsweise, mehr Instanzen zu verwenden, um die End-to-End-Batchbewertung zu beschleunigen.
* Verwenden Sie `--mini-batch-size`, um `mini_batch_size` zu überschreiben. Die Anzahl der Minibatches wird durch die Gesamtzahl der Eingabedateien und durch mini_batch_size bestimmt. Eine kleinere mini_batch_size generiert mehr Minibatches. Minibatches können zwar parallel ausgeführt werden, aber es kann zu zusätzlichem Zeitplanungs- und Aufrufaufwand kommen. 
* Verwenden Sie `--set`, um andere Einstellungen zu überschreiben (einschließlich `max_retries`, `timeout` und `error_threshold`). Diese Einstellungen können sich auf die End-to-End-Batchbewertungszeit für verschiedene Workloads auswirken.

Führen Sie den folgenden Code aus, um den Ausgabespeicherort anzugeben und um Einstellungen beim Aufrufen zu überschreiben. Im Beispiel werden die Ausgaben in einem Ordner mit dem gleichen Namen wie der Endpunkt im Standardblobspeicher des Arbeitsbereichs gespeichert. Außerdem wird ein zufälliger Dateiname verwendet, um die Eindeutigkeit des Ausgabespeicherorts sicherzustellen. Der Code sollte in Unix funktionieren. Ersetzen Sie ihn durch Ihren eigenen eindeutigen Ordner- und Dateinamen.

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="start_batch_scoring_job_configure_output_settings" :::

### <a name="monitor-batch-scoring-job-execution-progress"></a>Überwachen des Ausführungsfortschritts des Batchbewertungsauftrags

Bei Batchbewertungsaufträgen dauert es in der Regel etwas, bis sämtliche Eingaben verarbeitet wurden. 

Sie können CLI `job show` verwenden, um den Auftrag anzuzeigen. Führen Sie den folgenden Code aus, um den Auftragsstatus des vorherigen Endpunktaufrufs zu überprüfen. Um mehr über Auftragsbefehle zu erfahren, führen Sie `az ml job -h` aus.

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="check_job_status" :::

### <a name="check-batch-scoring-results"></a>Überprüfen der Ergebnisse der Batchbewertung

Befolgen Sie die nachstehenden Schritte, um die Bewertungsergebnisse in Azure Storage-Explorer anzuzeigen, wenn der Auftrag abgeschlossen wurde:

1. Führen Sie den folgenden Code aus, um den Batchbewertungsauftrag in Azure Machine Learning Studio zu öffnen. Der Studio-Link des Auftrags ist ebenfalls in der Antwort von `invoke` als Wert von `interactionEndpoints.Studio.endpoint` enthalten.

    :::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="show_job_in_studio" :::

1. Wählen Sie im Ausführungsgraph den Schritt `batchscoring` aus.
1. Wählen Sie die Registerkarte __Ausgaben und Protokolle__ und dann **Datenausgaben anzeigen** aus.
1. Wählen Sie unter __Datenausgaben__ das entsprechende Symbol aus, um __Storage-Explorer__ zu öffnen.

:::image type="content" source="media/how-to-use-batch-endpoint/view-data-outputs.png" alt-text="Studio-Screenshot: Anzeige des Speicherorts der Datenausgaben" lightbox="media/how-to-use-batch-endpoint/view-data-outputs.png" :::

Die Bewertungsergebnisse in Storage-Explorer ähneln der folgenden Beispielseite:

:::image type="content" source="media/how-to-use-batch-endpoint/scoring-view.png" alt-text="Screenshot der Bewertungsausgabe" lightbox="media/how-to-use-batch-endpoint/scoring-view.png":::

## <a name="deploy-a-new-model"></a>Bereitstellen eines neuen Modells

Sobald Sie über einen Batchendpunkt verfügen, können Sie Ihr Modell weiter optimieren und neue Bereitstellungen hinzufügen.

### <a name="create-a-new-batch-deployment-hosting-an-mlflow-model"></a>Erstellen einer neuen Batchbereitstellung, die ein MLflow-Modell hostet

Führen Sie den folgenden Code aus, um eine neue Batchbereitstellung unter dem vorhandenen Batchendpunkt zu erstellen, aber nicht als Standardbereitstellung festzulegen:

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="create_new_deployment_not_default" :::

Beachten Sie, dass `--set-default` nicht verwendet wird. Wenn Sie den Batchendpunkt erneut anzeigen (`show`), sollte `defaults.deployment_name` unverändert sein. 

Im Beispiel wird ein Modell (`/cli/endpoints/batch/autolog_nyc_taxi`) verwendet, das mit MLflow trainiert und nachverfolgt wird. `scoring_script` und `environment` können automatisch mithilfe der Metadaten des Modells generiert werden, ohne dass sie in der YAML-Datei angegeben werden müssen. Weitere Informationen über MLflow finden Sie unter [Trainieren und Nachverfolgen von ML-Modellen mit MLflow und Azure Machine Learning (Vorschauversion)](how-to-use-mlflow.md).

Im Folgenden finden Sie die YAML-Datei, die im Beispiel für die Bereitstellung eines MLflow-Modells verwendet wird, das nur die minimal erforderlichen Eigenschaften enthält. Die Quelldatei im Repository ist `/cli/endpoints/batch/mlflow-deployment.yml`.

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/batch/mlflow-deployment.yml":::

> [!NOTE]
> Die automatische Generierung von `scoring_script` und `environment` unterstützt nur die Modellkonfiguration der Python-Funktion und die spaltenbasierte Modellsignatur.

### <a name="test-a-non-default-batch-deployment"></a>Testen einer nicht standardmäßigen Batchbereitstellung

Führen Sie den folgenden Code aus, um die neue nicht standardmäßige Bereitstellung zu testen. Im Beispiel wird ein anderes Modell verwendet, das eine öffentlich verfügbare CSV-Datei aus `https://pipelinedata.blob.core.windows.net/sampledata/nytaxi/taxi-tip-data.csv` akzeptiert.

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="test_new_deployment" :::

Beachten Sie, dass `--deployment-name` für die Angabe des neuen Bereitstellungsnamens verwendet wird. Mit diesem Parameter ist es möglich, eine nicht standardmäßige Bereitstellung aufzurufen (`invoke`), ohne die Standardbereitstellung des Batchendpunkts zu aktualisieren.

### <a name="update-the-default-batch-deployment"></a>Aktualisieren der Standard-Batchbereitstellung

Führen Sie den folgenden Code aus, um die Standard-Batchbereitstellung des Endpunkts zu aktualisieren:

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="update_default_deployment" :::

Wenn Sie den Batchendpunkt nun erneut anzeigen (`show`), sollte `defaults.deployment_name` auf `mlflowdp` festgelegt sein. Sie können den Batchendpunkt auch direkt ohne den Parameter `--deployment-name` aufrufen (`invoke`).

### <a name="optional-update-the-deployment"></a>(Optional) Aktualisieren der Bereitstellung

Wenn Sie die Bereitstellung aktualisieren möchten (z. B. den Code, das Modell, die Umgebung oder Ihre Skalierungseinstellungen), aktualisieren Sie zunächst die YAML-Datei und führen Sie dann `az ml batch-deployment update` aus. Sie können die Bereitstellung auch ohne die YAML-Datei aktualisieren, indem Sie `--set` verwenden. Unter `az ml batch-deployment update -h` finden Sie weitere Informationen.

## <a name="delete-the-batch-endpoint-and-the-deployment"></a>Löschen des Batchendpunkts und der Bereitstellung

Wenn Sie die alte Batchbereitstellung nicht weiter verwenden möchten, sollten Sie sie löschen, indem Sie den folgenden Code ausführen. `--yes` wird verwendet, um den Löschvorgang zu bestätigen.

::: code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="delete_deployment" :::

Führen Sie den folgenden Code aus, um den Batchendpunkt und alle zugrundeliegenden Bereitstellungen zu löschen. Batchbewertungsaufträge werden nicht gelöscht.

::: code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="delete_endpoint" :::

## <a name="next-steps"></a>Nächste Schritte

* [Batchendpunkte in Studio](how-to-use-batch-endpoints-studio.md)
* [Bereitstellen von Modellen mit REST (Vorschau) für die Batchbewertung](how-to-deploy-batch-with-rest.md)
* [Problembehandlung für Batchendpunkte](how-to-troubleshoot-batch-endpoints.md)
