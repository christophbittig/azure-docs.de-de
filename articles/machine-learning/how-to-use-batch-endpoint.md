---
title: Verwenden von Batchendpunkten für die Batchbewertung
titleSuffix: Azure Machine Learning
description: In diesem Artikel erfahren Sie, wie Sie einen Batchendpunkt für die kontinuierliche Batchbewertung großer Datenmengen erstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: tracych
ms.author: tracych
ms.reviewer: laobri
ms.date: 8/11/2021
ms.custom: how-to, devplatv2
ms.openlocfilehash: b68ba3f0221aa97307e746d192de65b2915d2e4b
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/18/2021
ms.locfileid: "122419551"
---
# <a name="use-batch-endpoints-preview-for-batch-scoring"></a>Verwenden von Batchendpunkten (Vorschau) für die Batchbewertung

In diesem Artikel erfahren Sie, wie Sie Batchendpunkte (Vorschau) für die Batchbewertung verwenden. Batchendpunkte vereinfachen das Hosten Ihrer Modelle für die Batchbewertung, sodass Sie sich auf maschinelles Lernen konzentrieren können, anstatt sich mit der Infrastruktur zu befassen. Nachdem Sie einen Batchendpunkt erstellt haben, können Sie Batchbewertungsaufträge mit der Azure CLI oder über eine beliebige Plattform unter Verwendung einer HTTP-Bibliothek und der REST-API auslösen. Weitere Informationen finden Sie unter [Was sind Azure Machine Learning-Endpunkte (Vorschau)?](concept-endpoints.md).

In diesem Artikel lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines Batchendpunkts für das MLflow-Modell ohne Programmieraufwand
> * Überprüfen eines Batchendpunktdetails
> * Starten eines Batchbewertungsauftrags mithilfe der Azure CLI
> * Überwachen des Ausführungsstatus des Batchbewertungsauftrags und Überprüfen der Bewertungsergebnisse
> * Hinzufügen einer neuen Bereitstellung zu einem Batchendpunkt
> * Starten eines Batchbewertungsauftrags unter Verwendung von REST

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement: Sollten Sie über kein Azure-Abonnement verfügen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://azure.microsoft.com/free/) noch heute aus.

* Die Azure-Befehlszeilenschnittstelle (Command-Line Interface, CLI) und die ML-Erweiterung.

Für die Machine Learning-Erweiterung ist die Azure CLI-Version `>=2.15.0` erforderlich. Vergewissern Sie sich, dass diese Anforderung erfüllt ist:

```azurecli
az version
```

Upgraden Sie die Azure CLI bei Bedarf:

```azurecli
az upgrade
```

> [!NOTE]
>
> Der Befehl `az upgrade` wurde in Version 2.11.0 hinzugefügt und funktioniert nicht mit Versionen vor 2.11.0. Ältere Versionen können durch eine Neuinstallation (siehe [Installieren der Azure CLI](/cli/azure/update-azure-cli)) aktualisiert werden.
>
> Mit diesem Befehl werden auch alle installierten Erweiterungen standardmäßig aktualisiert. Weitere `az upgrade`-Optionen finden Sie auf der [Referenzseite für Befehle](/cli/azure/reference-index#az_upgrade).

Fügen Sie die Azure ML-Erweiterung hinzu, und konfigurieren Sie sie:

```azurecli
az extension add -n ml
```

Weitere Informationen zum Konfigurieren der ML-Erweiterung finden Sie unter [Installieren, Einrichten und Verwenden der CLI (v2) (Vorschau)](how-to-configure-cli.md).

* Das Beispielrepository

Klonen Sie das [AzureML-Beispielrepository](https://github.com/Azure/azureml-examples). In diesem Artikel werden die Ressourcen in `/cli/endpoints/batch` verwendet.

## <a name="create-a-compute-target"></a>Erstellen eines Computeziels

Die Batchbewertung wird nicht lokal, sondern nur auf Cloud Computing-Ressourcen ausgeführt. Die Cloud Computing-Ressource wird als „Computeziel“ bezeichnet. Ein Computeziel ist ein wiederverwendbarer virtueller Computer, auf dem Sie Batchbewertungsworkflows ausführen können.

Führen Sie den folgenden Code aus, um ein universelles Ziel vom Typ [`AmlCompute`](/python/api/azureml-core/azureml.core.compute.amlcompute(class)?view=azure-ml-py&preserve-view=true) zu erstellen. Weitere Informationen zu Computezielen finden Sie unter [Was sind Computeziele in Azure Machine Learning?](./concept-compute-target.md).

```azurecli
az ml compute create --name cpu-cluster --type amlcompute --min-instances 0 --max-instances 5
```

## <a name="create-a-batch-endpoint"></a>Erstellen eines Batchendpunkts

Bei Verwendung eines MLflow-Modells können Sie die Batchendpunkterstellung ohne Code durchführen. Das bedeutet, Sie müssen kein Bewertungsskript und keine Umgebung vorbereiten. Beides kann automatisch generiert werden. Weitere Informationen finden Sie unter [Trainieren und Nachverfolgen von ML-Modellen mit MLflow und Azure Machine Learning (Vorschauversion)](how-to-use-mlflow.md).

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="create_batch_endpoint" :::

Im Anschluss finden Sie die YAML-Datei zum Definieren des MLFlow-Batchendpunkts:

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/batch/create-batch-endpoint.yml":::

| Schlüssel | BESCHREIBUNG |
| --- | ----------- |
| $schema | (Optional) Das YAML-Schema. Sie können das Schema aus dem obigen Beispiel in einem Browser betrachten, um alle verfügbaren Optionen für eine YAML-Batchendpunktdatei anzuzeigen. |
| name | Der Name des Batchendpunkts. Muss innerhalb der Region eindeutig sein. Der Wert `name` wird als Teil des Bewertungs-URI verwendet. Der Wert muss mit einem englischsprachigen Zeichen beginnen, mit einer Kombination aus Zahlen, Zeichen und dem Symbol `-` weitergehen und mit einer Zahl oder einem Zeichen enden. Er muss mindestens drei Zeichen umfassen. Der reguläre Ausdruck für die Überprüfung lautet: `^[a-zA-Z][-a-zA-Z0-9]+[a-zA-Z0-9]$`.|
| Typ | Die Art des Endpunkts. Verwenden Sie für einen Batchendpunkt den Typ `batch`. |
| auth_mode | Verwenden Sie `aad_token` für die tokenbasierte Azure-Authentifizierung. |
| traffic | Der Prozentsatz des Datenverkehrs, der an diese Bereitstellung weitergeleitet wird. Bei Batchendpunkten ist für `traffic` nur der Wert `0` oder `100` gültig. Die Bereitstellung mit dem Datenverkehrswert `100` ist aktiv. Beim Aufrufen werden alle Daten an die aktive Bereitstellung gesendet. |
| deployments | Eine Liste der Bereitstellungen, die am Batchendpunkt erstellt werden sollen. Das Beispiel enthält nur eine einzelne Bereitstellung (`autolog-deployment`). |

Bereitstellungsattribute:

| Schlüssel | BESCHREIBUNG |
| --- | ----------- |
| name | Der Name der Bereitstellung |
| model | Das Modell, das für die Batchbewertung verwendet werden soll. Verwenden Sie `name`, `version` und `local_path`, um ein Modell von Ihrem lokalen Computer hochzuladen. Verwenden Sie das Präfix `azureml:`, um auf eine vorhandene Modellressource in Ihrem Arbeitsbereich zu verweisen. `azureml: autolog:1` verweist beispielsweise auf Version 1 eines Modells namens `autolog`. |
| compute.target | Das Computeziel. Verwenden Sie das Präfix `azureml:`, um auf eine vorhandene Computeressource in Ihrem Arbeitsbereich zu verweisen. `azureml:cpu-cluster` verweist beispielsweise auf ein Computeziel namens `cpu-cluster`. |
| compute.instance_count | Die Anzahl von Computeknoten, die für die Batchbewertung verwendet werden sollen. Der Standardwert ist `1`.|
| mini_batch_size | (Optional) Die Anzahl von Dateien, die von `scoring_script` in einem einzelnen Aufruf von `run()` verarbeitet werden können. Der Standardwert ist `10`. |
| output_file_name | (Optional) Der Name der Ausgabedatei der Batchbewertung. Der Standardwert ist `predictions.csv`. |
| retry_settings.max_retries | (Optional) Die maximale Anzahl von Versuchen bei einer nicht erfolgreichen Bewertungsskriptausführung (`scoring_script` `run()`). Der Standardwert ist `3`. |
| retry_settings.timeout | (Optional) Das Timeout in Sekunden für eine Bewertungsskriptausführung (`scoring_script` `run()`). Der Standardwert ist `30`. |
| error_threshold | (Optional) Die Anzahl von Dateifehlern, die ignoriert werden sollen. Übersteigt die Fehleranzahl für die gesamte Eingabe diesen Wert, wird der Auftrag beendet. Der Fehlerschwellenwert gilt für die gesamte Eingabe und nicht für den einzelnen Minibatch, der an die Methode `run()` gesendet wird. Der Standardwert ist `-1`. Das bedeutet, dass eine beliebige Anzahl von Fehlern zulässig ist, ohne dass die Ausführung beendet wird. | 
| logging_level | (Optional) Die Ausführlichkeit des Protokolls. Mögliche Werte nach zunehmender Ausführlichkeit sind „WARNING“ (Warnung), „INFO“ (Information) und „DEBUG“ (Debuggen). Der Standardwert ist „INFO“. |

## <a name="check-batch-endpoint-details"></a>Überprüfen von Batchendpunktdetails

Die Details eines erstellten Batchendpunkts können mithilfe von `show` überprüft werden. Verwenden Sie [`--query parameter`](/cli/azure/query-azure-cli), um nur bestimmte Attribute aus den zurückgegebenen Daten abzurufen.

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="check_batch_endpooint_detail" :::

## <a name="start-a-batch-scoring-job-using-the-azure-cli"></a>Starten eines Batchbewertungsauftrags mithilfe der Azure CLI

Eine Batchbewertungsworkload wird als Offlineauftrag ausgeführt. Die Batchbewertung ist auf die Verarbeitung großer Datenmengen ausgelegt. Eingaben werden im Computecluster parallel verarbeitet. Eine Datenpartition wird einem Prozess auf einem Knoten zugewiesen. Ein einzelner Knoten mit mehreren Prozessen verfügt über mehrere parallel ausgeführte Partitionen. Die Bewertungsausgaben der Batchbewertung werden standardmäßig in Blobspeicher gespeichert. Sie können einen Batchbewertungsauftrag über die Azure CLI starten, indem Sie die Dateneingaben übergeben. Sie können auch den Ausgabespeicherort konfigurieren und einige der Einstellungen überschreiben, um die bestmögliche Leistung zu erzielen.

### <a name="start-a-batch-scoring-job-with-different-input-options"></a>Starten eines Batchbewertungsauftrags mit verschiedenen Eingabeoptionen

Für die Angabe der Dateneingaben stehen drei Optionen zur Verfügung:

Option 1: Registrierte Daten

Verwenden Sie `--input-data`, um bei AML registrierte Daten zu übergeben.

> [!NOTE]
> Während der Vorschauphase wird nur „FileDataset“ unterstützt. 

```azurecli
az ml endpoint invoke --name mybatchedp --type batch --input-data azureml:<dataName>:<dataVersion>
```

Option 2: Daten in der Cloud

Verwenden Sie `--input-datastore`, um einen bei AML registrierten Datenspeicher anzugeben, und verwenden Sie `--input-path`, um den relativen Pfad im Datenspeicher anzugeben.

```azurecli
az ml endpoint invoke --name mybatchedp --type batch --input-datastore azureml:<datastoreName> --input-path <relativePath>
```

Sind Ihre Daten öffentlich verfügbar, verwenden Sie `--input-path`, um den öffentlichen Pfad anzugeben.

Wenn Sie das bereitgestellte Beispiel verwenden, können Sie den folgenden Befehl ausführen, um einen Batchbewertungsauftrag zu starten:

```azurecli
az ml endpoint invoke --name mybatchedp --type batch --input-path https://pipelinedata.blob.core.windows.net/sampledata/nytaxi/taxi-tip-data.csv
```

Option 3: Lokal gespeicherte Daten

```azurecli
az ml endpoint invoke --name mybatchedp --type batch --input-local-path <localPath>
```

### <a name="configure-the-output-location"></a>Konfigurieren des Ausgabespeicherorts

Die Ergebnisse der Batchbewertung werden standardmäßig im Standardblobspeicher des Arbeitsbereichs in einem nach dem Auftrag benannten Ordner gespeichert. Bei dem Auftragsnamen handelt es sich um systemseitig generierte GUID. Beim Starten eines Batchbewertungsauftrags können Sie konfigurieren, wo die Bewertungsausgaben gespeichert werden sollen. Verwenden Sie `--output-datastore`, um einen beliebigen registrierten Datenspeicher zu konfigurieren, und verwenden Sie `--output-path`, um den relativen Pfad zu konfigurieren. Verwenden Sie `--set output_file_name`, um einen neuen Ausgabedateinamen zu konfigurieren.

> [!IMPORTANT]
> Der Ausgabespeicherort muss eindeutig sein. Ist die Ausgabedatei bereits vorhanden, tritt für den Batchbewertungsauftrag ein Fehler auf. 

```azurecli
az ml endpoint invoke --name mybatchedp --type batch --input-path https://pipelinedata.blob.core.windows.net/sampledata/nytaxi/taxi-tip-data.csv --output-datastore azureml:workspaceblobstore --output-path mypath --set output_file_name=mypredictions.csv
```

### <a name="overwrite-settings"></a>Überschreiben von Einstellungen

Beim Starten eines Batchbewertungsauftrags können einige Einstellungen überschrieben werden, um die Computeressource optimal zu nutzen und die Leistung zu verbessern: 

* Verwenden Sie `--mini-batch-size`, um `mini_batch_size` zu überschreiben, wenn eine andere Eingabedatengröße verwendet wird. 
* Verwenden Sie `--instance-count`, um `instance_count` zu überschreiben, wenn für diesen Auftrag eine andere Computeressource benötigt wird. 
* Verwenden Sie `--set`, um andere Einstellungen zu überschreiben (einschließlich `max_retries`, `timeout` und `error_threshold`).

```azurecli
az ml endpoint invoke --name mybatchedp --type batch --input-path https://pipelinedata.blob.core.windows.net/sampledata/nytaxi/taxi-tip-data.csv --set retry_settings.max_retries=1
```
:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="start_batch_scoring_job_with_new_settings" :::

## <a name="check-batch-scoring-job-execution-progress"></a>Überprüfen des Ausführungsstatus des Batchbewertungsauftrags

Bei Batchbewertungsaufträgen dauert es in der Regel etwas, bis sämtliche Eingaben verarbeitet wurden. Sie können den Auftragsstatus von Azure Machine Learning Studio aus überprüfen. Der Studio-Link ist in der Antwort von `invoke` als Wert von `interactionEndpoints.Studio.endpoint` enthalten.

Über die Azure CLI können Sie neben dem Status auch Auftragsdetails überprüfen.

Rufen Sie den Auftragsnamen aus der Antwort von „invoke“ ab.

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="start_batch_scoring_job" :::

Verwenden Sie `job show`, um Details und den Status eines Batchbewertungsauftrags zu überprüfen.

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="check_job_status" :::

Streamen Sie die Auftragsprotokolle mithilfe von `job stream`.

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="stream_job_logs_to_console" :::


## <a name="check-batch-scoring-results"></a>Überprüfen der Ergebnisse der Batchbewertung

So zeigen Sie die Bewertungsergebnisse an:

1. Wechseln Sie zu Studio.
1. Navigieren Sie zu **Experimente**.
1. Navigieren Sie zur Ausführung Ihres Endpunkts (der Wert von `interactionEndpoints.Studio.endpoint` in der Antwort auf `endpoint invoke`).
1. Klicken Sie im Graphen der Ausführung auf den Schritt `batchscoring`.
1. Wählen Sie die Registerkarte „Ausgaben + Protokolle“ und anschließend **Datenausgaben anzeigen** aus. 
1. Wählen Sie das Symbol **Ausgabe anzeigen** aus. :::image type="content" source="media/how-to-use-batch-endpoint/view-data-outputs.png" alt-text="Screenshot: Studio mit der Option zum Anzeigen von Datenausgaben" lightbox="media/how-to-use-batch-endpoint/view-data-outputs.png" :::
1. Kopieren Sie im Popupbereich den Pfad, und wählen Sie den Link zum Öffnen des Datenspeichers aus.
1. Fügen Sie auf der daraufhin angezeigten Blobspeicherseite den obigen Pfad in das Suchfeld ein. Die Bewertungsausgaben befinden sich in dem Ordner.
:::image type="content" source="media/how-to-use-batch-endpoint/scoring-view.gif" alt-text="Screencast: Öffnen des Bewertungsordners und der Bewertungsausgabe" lightbox="media/how-to-use-batch-endpoint/scoring-view.gif":::

## <a name="add-a-deployment-to-the-batch-endpoint"></a>Hinzufügen einer Bereitstellung zum Batchendpunkt

Ein Batchendpunkt kann über mehrere Bereitstellungen verfügen. Von den einzelnen Bereitstellungen wird jeweils ein Modell für die Batchbewertung gehostet. 

### <a name="add-a-new-deployment"></a>Hinzufügen einer neuen Bereitstellung

Verwenden Sie den folgenden Befehl, um einem vorhandenen Batchendpunkt eine neue Bereitstellung hinzuzufügen.

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" range="65" :::

In diesem Beispiel wird ein MLflow-fremdes Modell verwendet. Bei Verwendung eines MLflow-fremden Modells müssen Sie in der YAML-Datei die Umgebung und ein Bewertungsskript angeben:

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/batch/add-deployment.yml" :::

Im Anschluss finden Sie weitere Bereitstellungsattribute für das MLflow-fremde Modell:

| Schlüssel | BESCHREIBUNG |
| --- | ----------- |
| code_configuration.code.local_path | Das Verzeichnis, das den gesamten Quellcode für die Bewertung des Modells enthält. |
| code_configuration.scoring_script | Die Python-Datei im obigen Verzeichnis. Diese Datei muss über eine Funktion vom Typ `init()` und über eine Funktion vom Typ `run()` verfügen. Verwenden Sie die Funktion `init()` für aufwendige oder allgemeine Vorbereitungen. Ein Beispiel wäre etwa das Laden des Modells in ein globales Objekt. Diese Funktion wird nur einmal zu Beginn des Prozesses aufgerufen. Verwenden Sie `run(mini_batch)`, um die einzelnen Einträge zu bewerten. Der Wert von `mini_batch` ist eine Liste mit Dateipfaden. Von der Methode `run()` sollte ein Pandas-Datenrahmen (`DataFrame`) oder ein Array zurückgegeben werden. Diese zurückgegebenen Elemente werden an die allgemeine Ausgabedatei angefügt. Die zurückgegebenen Ausgabeelemente geben jeweils eine erfolgreiche Ausführung des Eingabeelements aus dem Eingabeminibatch an. Stellen Sie sicher, dass Ihr Ausführungsergebnis genügend Daten enthält, um die Eingabe einem bestimmten Ergebnis der Ausführungsausgabe zuordnen zu können. Da die Ausführungsausgabe nicht unbedingt in der richtigen Reihenfolge in die Ausgabedatei geschrieben wird, müssen Sie einen Schlüssel in der Ausgabe verwenden, um sie der richtigen Eingabe zuzuordnen. |
| environment | Die Umgebung für die Bewertung des Modells am Computeziel. Sie können die Umgebung inline definieren, indem Sie Name, Version und Pfad angeben. Verwenden Sie `conda_file`, um Abhängigkeiten einzuschließen, die zusätzlich zu `docker.image` installiert werden. Verwenden Sie das Präfix `azureml:`, um auf eine vorhandene Umgebung zu verweisen. `azureml: mnist-env:1` verweist beispielsweise auf Version 1 einer Umgebung namens `mnist-env`. |

Die Details Ihrer Bereitstellung können Sie wie folgt überprüfen:

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="check_batch_endpooint_detail" :::

### <a name="activate-the-new-deployment"></a>Aktivieren der neuen Bereitstellung

Für den Batchrückschluss müssen 100 Prozent der Anfragen an die gewünschte Bereitstellung gesendet werden. Verwenden Sie Folgendes, um Ihre neu erstellte Bereitstellung als Ziel festzulegen:

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="switch_traffic" :::

Wenn Sie die Details Ihrer Bereitstellung erneut überprüfen, werden Ihre Änderungen angezeigt:

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="check_batch_endpooint_detail" :::

Nun können Sie einen Batchbewertungsauftrag mit dieser neuen Bereitstellung aufrufen:

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="start_batch_scoring_job_with_new_settings" :::

## <a name="start-a-batch-scoring-job-using-rest"></a>Starten eines Batchbewertungsauftrags unter Verwendung von REST

Batchendpunkte verfügen für den REST-Zugriff über Bewertungs-URIs. MIT REST können Sie eine beliebige HTTP-Bibliothek auf einer beliebigen Plattform verwenden, um einen Batchbewertungsauftrag zu starten.

1. Rufen Sie den Bewertungs-URI (`scoring_uri`) ab:  

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="get_scoring_uri" :::

2. Rufen Sie das Zugriffstoken ab:

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="get_token" :::


3. Verwenden Sie den Bewertungs-URI (`scoring_uri`), das Zugriffstoken und JSON-Daten, um per POST eine Anforderung zu veröffentlichen und einen Batchbewertungsauftrag zu starten:

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="start_batch_scoring_job_rest":::


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die erstellten Ressourcen nicht mehr benötigen, löschen Sie sie, damit Ihnen keine Kosten entstehen:

1. Wählen Sie im Azure-Portal im linken Menü die Option **Ressourcengruppen** aus.
1. Wählen Sie in der Liste der Ressourcengruppen die von Ihnen erstellte Ressourcengruppe aus.
1. Wählen Sie die Option **Ressourcengruppe löschen**.
1. Geben Sie den Ressourcengruppennamen ein. Wählen Sie dann **Löschen** aus.

Sie können die Ressourcengruppe auch behalten und einen einzelnen Arbeitsbereich löschen. Zeigen Sie die Eigenschaften des Arbeitsbereichs an, und wählen Sie **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie Batchendpunkte erstellen und aufrufen, um große Datenmengen zu bewerten. Weitere Informationen zu Azure Machine Learning finden Sie in den folgenden Artikeln:

* [Problembehandlung für Batchendpunkte](how-to-troubleshoot-batch-endpoints.md)
* [Bereitstellen und Bewerten eines Machine Learning-Modells mit einem verwalteten Onlineendpunkt (Vorschau)](how-to-deploy-managed-online-endpoints.md)
