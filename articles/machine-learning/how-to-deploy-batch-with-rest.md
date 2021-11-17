---
title: Bereitstellen von Modellen mithilfe von Batchendpunkten mit REST-APIs (Vorschau)
titleSuffix: Azure Machine Learning
description: In diesem Artikel erfahren Sie, wie Sie Modelle mithilfe von Batchendpunkten mit REST-APIs bereitstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: tracych
ms.author: tracych
ms.date: 10/21/2021
ms.reviewer: laobri
ms.custom: devplatv2
ms.openlocfilehash: 351855c870f3f3658488c66c401cef1c3c4b17c4
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132059807"
---
# <a name="deploy-models-with-rest-preview-for-batch-scoring"></a>Bereitstellen von Modellen mit REST (Vorschau) für die Batchbewertung 

Hier erfahren Sie, wie Sie die Azure Machine Learning-REST-API verwenden, um Modelle für die Batchbewertung (Vorschau) bereitzustellen.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

Von der REST-API werden HTTP-Standardverben genutzt, um Ressourcen zu erstellen, abzurufen, zu aktualisieren und zu löschen. Die REST-API funktioniert mit jeder Sprache oder jedem Tool, die bzw. das HTTP-Anforderungen ausführen kann. Durch die unkomplizierte Struktur von REST sind diese APIs eine gute Wahl in Skriptumgebungen und für die MLOps-Automatisierung.

In diesem Artikel wird beschrieben, wie Sie die neuen REST-APIs für folgende Zwecke verwenden:

> [!div class="checklist"]
> * Erstellen von Machine Learning-Ressourcen
> * Einen Batchendpunkt und eine Batchbereitstellung erstellen
> * Einen Batchendpunkt zum Starten eines Batchbewertungsauftrags aufrufen

## <a name="prerequisites"></a>Voraussetzungen

- Ein **Azure-Abonnement**, für das Sie über Administratorrechte verfügen. Testen Sie das [kostenlose oder kostenpflichtige persönliche Abonnement](https://azure.microsoft.com/free/), falls Sie nicht über ein Abonnement dieser Art verfügen.
- Ein [Azure Machine Learning-Arbeitsbereich](how-to-manage-workspace.md).
- Ein Dienstprinzipal in Ihrem Arbeitsbereich. Für administrative REST-Anforderungen wird die [Dienstprinzipalauthentifizierung](how-to-setup-authentication.md#use-service-principal-authentication) verwendet.
- Ein Token für die Dienstprinzipalauthentifizierung. Führen Sie die Schritte unter [Abrufen eines Tokens für die Dienstprinzipalauthentifizierung](./how-to-manage-rest.md#retrieve-a-service-principal-authentication-token) aus, um dieses Token abzurufen. 
- Das **curl**-Hilfsprogramm. Das **curl**-Programm ist im [Windows-Subsystem für Linux](/windows/wsl/install-win10) oder jeder beliebigen UNIX-Distribution verfügbar. In PowerShell ist **curl** ein Alias für **Invoke-WebRequest**, und `curl -d "key=val" -X POST uri` wird zu `Invoke-WebRequest -Body "key=val" -Method POST -Uri uri`. 
- Der [jq](https://stedolan.github.io/jq/)-JSON-Prozessor

> [!IMPORTANT]
> In den Codeausschnitten in diesem Artikel wird davon ausgegangen, dass Sie die Bash-Shell verwenden.
>
> Die Codeausschnitte aus der `/cli/batch-score-rest.sh`-Datei im [Azure ML-Beispielrepository](https://github.com/Azure/azureml-examples) werden verwendet.

## <a name="set-endpoint-name"></a>Festlegen von Endpunktnamen

> [!NOTE]
> Batchendpunktnamen müssen auf Azure-Regionsebene eindeutig sein. Beispielsweise kann es nur einen Batchendpunkt mit dem Namen „mybatchendpoint“ in westus2 geben.

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="set_endpoint_name":::

## <a name="azure-machine-learning-batch-endpoints"></a>Batchendpunkte in Azure Machine Learning

[Batchendpunkte (Vorschau)](concept-endpoints.md#what-are-batch-endpoints-preview) vereinfacht das Hosten Ihrer Modelle für die Batchbewertung, sodass Sie sich auf maschinelles Lernen konzentrieren können, anstatt sich mit der Infrastruktur zu befassen. In diesem Artikel erstellen Sie einen Batchendpunkt und eine Bereitstellung. Sie rufen den Batchendpunkt auf, um einen Batchbewertungsauftrag zu starten. Zunächst müssen Sie jedoch die für die Bereitstellung erforderlichen Ressourcen registrieren, einschließlich Modell, Code und Umgebung.

Es gibt viele Möglichkeiten, einen Batchendpunkt in Azure Machine Learning zu erstellen, [einschließlich der Azure CLI](how-to-use-batch-endpoint.md), und visuell über [Azure Machine Learning Studio](how-to-use-batch-endpoints-studio.md). Im folgenden Beispiel wird ein Batchendpunkt und eine Bereitstellung mit der REST-API erstellt.

## <a name="create-machine-learning-assets"></a>Erstellen von Machine Learning-Ressourcen

Richten Sie zunächst Ihre Azure Machine Learning-Ressourcen ein, um Ihren Auftrag zu konfigurieren.

In den folgenden REST-API-Aufrufen verwenden wir `SUBSCRIPTION_ID`, `RESOURCE_GROUP`, `LOCATION` und `WORKSPACE` als Platzhalter. Ersetzen Sie die Platzhalter durch Ihre eigenen Werte. 

Für administrative REST-Anforderungen wird ein [Token für die Dienstprinzipalauthentifizierung](how-to-manage-rest.md#retrieve-a-service-principal-authentication-token) angefordert. Ersetzen Sie `TOKEN` durch Ihren eigenen Wert. Sie können dieses Token mit dem folgenden Befehl abrufen:

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" range="13":::

Der Dienstanbieter verwendet das `api-version`-Argument, um Kompatibilität zu gewährleisten. Das `api-version`-Argument variiert von Dienst zu Dienst. Legen Sie die API-Version zur Vorbereitung für zukünftige Versionen als Variable fest:

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" range="11":::

### <a name="create-compute"></a>Erstellen von Computeressourcen
Die Batchbewertung wird nicht lokal, sondern nur auf Cloud Computing-Ressourcen ausgeführt. Die Cloud Computing-Ressource ist ein wiederverwendbarer, virtueller Computecluster, in dem Sie Batchbewertungsworkflows ausführen können.

So erstellen Sie ein Computecluster:

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="create_compute":::

> [!TIP]
> Wenn Sie stattdessen einen vorhandenen Computecluster verwenden möchten, müssen Sie beim [Erstellen der Batchbereitstellung](#create-batch-deployment) die vollständige Azure Resource Manager-ID angeben. Die vollständige ID verwendet das Format `/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.MachineLearningServices/workspaces/$WORKSPACE/computes/<your-compute-name>`.

### <a name="get-storage-account-details"></a>Abrufen von Speicherkontodetails

Um das Modell und den Code zu registrieren, müssen sie zuerst in ein Speicherkonto hochgeladen werden. Die Details des Speicherkontos sind im Datenspeicher verfügbar. In diesem Beispiel rufen Sie den Standarddatenspeicher und das Azure Storage-Konto für Ihren Arbeitsbereich ab. Fragen Sie Ihren Arbeitsbereich mit einer GET-Anforderung ab, um eine JSON-Datei mit den Informationen abzurufen.

Sie können das Tool [jq](https://stedolan.github.io/jq/) verwenden, um das JSON-Ergebnis zu analysieren und die erforderlichen Werte zu ermitteln. Sie können diese Informationen auch über das Azure-Portal ermitteln:

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="get_storage_details":::

### <a name="upload--register-code"></a>Hochladen und Registrieren des Codes

Nachdem Sie über den Datenspeicher verfügen, können Sie das Bewertungsskript hochladen. Verwenden Sie die Azure Storage-CLI, um ein Blob in Ihren Standardcontainer hochzuladen:

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="upload_code":::

> [!TIP]
> Sie können auch andere Uploadmethoden verwenden, z. B. das Azure-Portal oder [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/).

Nachdem Sie Ihren Code hochgeladen haben, können Sie Ihren Code mit einer PUT-Anforderung angeben:

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="create_code":::

### <a name="upload-and-register-model"></a>Hochladen und Registrieren des Modells

Laden Sie die Modelldateien ähnlich wie den Code hoch:

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="upload_model":::

Registrieren Sie jetzt das Modell:

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="create_model":::

### <a name="create-environment"></a>Erstellen der Umgebung
Die Bereitstellung muss in einer Umgebung ausgeführt werden, die über die erforderlichen Abhängigkeiten verfügt. Erstellen Sie die Umgebung mit einer PUT-Anforderung. Verwenden Sie ein Docker-Image aus Microsoft Container Registry. Sie können das Docker-Image mit `image` konfigurieren und Conda-Abhängigkeiten mit `condaFile` hinzufügen.

Führen Sie den folgenden Code aus, um die im JSON-Format definierte `condaFile` zu lesen. Die Quelldatei befindet sich unter `/cli/endpoints/batch/mnist/environment/conda.json` im Beispielrepository:

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="read_condafile":::

Führen Sie nun den folgenden Codeausschnitt aus, um eine Umgebung zu erstellen:

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="create_environment":::

## <a name="deploy-with-batch-endpoints"></a>Bereitstellen mit Batchendpunkten

Erstellen Sie als Nächstes den Batchendpunkt, eine Bereitstellung, und legen Sie die Standardbereitstellung fest.

### <a name="create-batch-endpoint"></a>Erstellen eines Batchendpunkts

So erstellen Sie den Batchendpunkt:

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="create_endpoint":::

### <a name="create-batch-deployment"></a>Erstellen einer Batchbereitstellung

So erstellen Sie eine Batchbereitstellung unter dem Endpunkt:

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="create_deployment":::

### <a name="set-the-default-batch-deployment-under-the-endpoint"></a>Festlegen der Standardbatchbereitstellung unter dem Endpunkt

Es gibt nur eine Standardbatchbereitstellung unter einem Endpunkt, die beim Aufrufen zum Ausführen eines Batchbewertungsauftrags verwendet wird.

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="set_endpoint_defaults":::

## <a name="run-batch-scoring"></a>Ausführen der Batchbewertung

Das Aufrufen eines Batchendpunkts löst einen Batchbewertungsauftrag aus. Eine Auftrags-`id` wird in der Antwort zurückgegeben und kann zum Nachverfolgen der Fortschritts der Batchbewertung verwendet werden. Im folgenden Codeausschnitt wird `jq` verwendet, um die Auftrags-`id` zu erhalten.

### <a name="invoke-the-batch-endpoint-to-start-a-batch-scoring-job"></a>Aufrufen des Batchendpunkts zum Starten eines Batchbewertungsauftrags

Rufen Sie den Bewertungs-URI und das Zugriffstoken zum Aufrufen des Batchendpunkts ab. Rufen Sie zunächst den Bewertungs-URI ab:

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="get_endpoint":::

So rufen Sie das Zugriffstoken des Batchendpunkts ab:

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="get_access_token":::

Rufen Sie nun den Batchendpunkt auf, um einen Batchbewertungsauftrag zu starten. Im folgenden Beispiel werden Daten bewertet, die öffentlich in der Cloud verfügbar sind:

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="score_endpoint_with_data_in_cloud":::

Wenn Ihre Daten in einem registrierten Azure Machine Learning-Datenspeicher gespeichert sind, können Sie den Batchendpunkt mit einem Dataset aufrufen. Mit dem folgenden Code wird ein neues Dataset erstellt:

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="create_dataset":::

Verweisen Sie anschließend beim Aufrufen des Batchendpunkts auf das Dataset:

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="score_endpoint_with_dataset":::

Im vorherigen Codeausschnitt wird mithilfe von `datastoreId`, `path` und `outputFileName` ein benutzerdefinierter Ausgabespeicherort bereitgestellt. Mit diesen Einstellungen können Sie konfigurieren, wo die Ergebnisse der Batchbewertung gespeichert werden.

> [!IMPORTANT]
> Sie müssen einen eindeutigen Ausgabespeicherort angeben. Wenn die Ausgabedatei bereits vorhanden ist, kann der Batchbewertungsauftrag nicht ausgeführt werden.

In diesem Beispiel wird die Ausgabe im Standardblobspeicher für den Arbeitsbereich gespeichert. Der Ordnername ist identisch mit dem Endpunktnamen, und der Dateiname wird nach dem Zufallsprinzip durch den folgenden Code generiert:

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score-rest.sh" ID="unique_output" :::

### <a name="check-the-batch-scoring-job"></a>Überprüfen des Batchbewertungsauftrags

Bei Batchbewertungsaufträgen dauert es in der Regel etwas, bis sämtliche Eingaben verarbeitet wurden. Überwachen Sie den Auftragsstatus, und überprüfen Sie die Ergebnisse nach Abschluss:

> [!TIP]
> Im Beispiel wird die Standardbereitstellung des Batchendpunkts aufgerufen. Um eine nicht standardmäßige Bereitstellung aufzurufen, verwenden Sie den `azureml-model-deployment`-HTTP-Header, und legen Sie den Wert auf den Bereitstellungsnamen fest. Beispiel: Verwenden eines Parameters von `--header "azureml-model-deployment: $DEPLOYMENT_NAME"` mit cURL

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="check_job":::

### <a name="check-batch-scoring-results"></a>Überprüfen der Ergebnisse der Batchbewertung

Informationen zum Überprüfen der Ergebnisse finden Sie unter [Überprüfung von Ergebnissen der Batchbewertung](how-to-use-batch-endpoint.md#check-batch-scoring-results).

## <a name="delete-the-batch-endpoint"></a>Löschen des Batchendpunkts

Wenn Sie den Batchendpunkt nicht verwenden, sollten Sie ihn mit dem folgenden Befehl löschen (der Endpunkt und alle zugrunde liegenden Bereitstellungen werden gelöscht):

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="delete_endpoint":::

## <a name="next-steps"></a>Nächste Schritte

* Hier erfahren Sie, wie Sie Ihr Modell für die Batchbewertung [mithilfe der Azure CLI](how-to-use-batch-endpoint.md) bereitstellen.
* Hier erfahren Sie, wie Sie Ihr Modell für die Batchbewertung [mithilfe von Azure Machine Learning Studio](how-to-use-batch-endpoints-studio.md) bereitstellen.
* Hier erfahren Sie mehr über die [Problembehandlung bei Batchendpunkten](how-to-troubleshoot-batch-endpoints.md).
