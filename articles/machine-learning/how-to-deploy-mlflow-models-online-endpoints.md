---
title: Bereitstellen von MLflow-Modellen auf einem verwalteten Onlineendpunkt (Vorschau)
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie Ihr MLflow-Modell als Webdienst bereitstellen, der automatisch von Azure verwaltet wird.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: ssambare
author: shivanissambare
ms.date: 11/03/2021
ms.topic: how-to
ms.reviewer: larryfr
ms.custom: deploy, mlflow, devplatv2, no-code-deployment
ms.openlocfilehash: a2328490185032e805a95f85b430d6742c76435c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132289886"
---
# <a name="deploy-mlflow-models-to-managed-online-endpoint-preview"></a>Bereitstellen von MLflow-Modellen auf einem verwalteten Onlineendpunkt (Vorschau)

In diesem Artikel erfahren Sie, wie Sie Ihr [MLflow-Modell](https://www.mlflow.org) auf einem [verwalteten Onlineendpunkt](concept-endpoints.md#managed-online-endpoints) (Vorschau) bereitstellen. Wenn Sie Ihr MLflow-Modell auf einem verwalteten Onlineendpunkt bereitstellen, ist dies eine Bereitstellung ohne Code. Es sind weder ein Bewertungsskript noch eine Umgebung erforderlich. 

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [basic cli prereqs](../../includes/machine-learning-cli-prereqs.md)]

* Sie müssen über ein MLflow-Modell verfügen. Die Beispiele in diesem Artikel basieren auf den Modellen aus [https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online/mlflow](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online/mlflow).

[!INCLUDE [clone repo & set defaults](../../includes/machine-learning-cli-prepare.md)]

In diesen Codeausschnitten, die in diesem Artikel verwendet werden, enthält die Umgebungsvariable `ENDPOINT_NAME` den Namen des zu erstellenden und zu verwendenden Endpunkts. Verwenden Sie zum Festlegen den folgenden Befehl der CLI. Ersetzen Sie `<YOUR_ENDPOINT_NAME>` durch den Namen Ihres Endpunkts.

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-mlflow.sh" ID="set_endpoint_name":::

## <a name="deploy-using-cli-v2"></a>Bereitstellung über CLI (v2)

Dieses Beispiel zeigt, wie Sie ein MLflow-Modell mithilfe der CLI (v2) auf einem verwalteten Onlineendpunkt bereitstellen können.

> [!IMPORTANT]
> Für die Bereitstellung ohne Code in MLflow wird das **[Testen über lokale Endpunkte](how-to-deploy-managed-online-endpoints.md#deploy-and-debug-locally-by-using-local-endpoints)** derzeit nicht unterstützt.

1. Erstellen Sie eine YAML-Konfigurationsdatei für Ihren Endpunkt. Im folgenden Beispiel werden der Name und der Authentifizierungsmodus des Endpunkts konfiguriert:

    __create-endpoint.yaml__

    :::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/mlflow/create-endpoint.yaml":::

1. Um einen neuen Endpunkt unter Verwendung der YAML-Konfiguration zu erstellen, verwenden Sie den folgenden Befehl:

    :::code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-mlflow.sh" ID="create_endpoint":::

1. Erstellen Sie eine YAML-Konfigurationsdatei für das Bereitstellen. Im folgenden Beispiel wird eine Bereitstellung des Modells `sklearn-diabetes` für den im vorherigen Schritt erstellten Endpunkt konfiguriert:

    > [!IMPORTANT]
    > Damit MLflow NCD (Bereitstellung ohne Code) funktioniert, muss **`model_format`** auf **`mlflow`** festgelegt werden. Weitere Informationen finden Sie unter [YAML-Schema des CLI-Modells (v2)](reference-yaml-model.md).

    __sklearn-deployment.yaml__

    :::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/mlflow/sklearn-deployment.yaml":::

1. Um die Bereitstellung mit der YAML-Konfiguration zu erstellen, verwenden Sie den folgenden Befehl:

    :::code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-mlflow.sh" ID="create_sklearn_deployment":::

### <a name="invoke-the-endpoint"></a>Aufrufen des Endpunkts

Sobald die Bereitstellung abgeschlossen ist, verwenden Sie den folgenden Befehl, um eine Bewertungsanforderung an den bereitgestellten Endpunkt zu stellen. Die in diesem Befehl verwendete Datei [sample-request-sklearn.json](https://github.com/Azure/azureml-examples/blob/main/cli/endpoints/online/mlflow/sample-request-sklearn.json) befindet sich im Verzeichnis `/cli/endpoints/online/mlflow` des „azure-examples“-Repositorys:

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-mlflow.sh" ID="test_sklearn_deployment":::

**sample-request-sklearn.json**

:::code language="json" source="~/azureml-examples-main/cli/endpoints/online/mlflow/sample-request-sklearn.json":::

Die Antwort sieht in etwa wie folgender Text aus:

```json
[ 
  11633.100167144921,
  8522.117402884991
]
```

### <a name="delete-endpoint"></a>Löschen eines Endpunkts

Wenn Sie mit dem Endpunkt fertig sind, verwenden Sie den folgenden Befehl, um ihn zu löschen:

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-mlflow.sh" ID="delete_endpoint":::

## <a name="deploy-using-azure-machine-learning-studio"></a>Bereitstellung mit Azure Machine Learning Studio

Dieses Beispiel zeigt, wie Sie ein MLflow-Modell mithilfe von [Azure Machine Learning Studio](https://ml.azure.com) auf einem verwalteten Onlineendpunkt bereitstellen können.

1. Registrieren Sie Ihr Modell im MLflow-Format mit dem folgenden YAML- und CLI-Befehl. Der YAML-Befehl verwendet ein scikit-learn MLflow-Modell von [https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online/mlflow](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online/mlflow).

    __sample-create-mlflow-model.yaml__

    ```yaml
    $schema: https://azuremlschemas.azureedge.net/latest/model.schema.json
    name: sklearn-diabetes-mlflow
    version: 1
    local_path: sklearn-diabetes/model
    model_format: mlflow
    description: Scikit-learn MLflow model.
    ```


    ```azurecli
    az ml model create -f sample-create-mlflow-model.yaml
    ```

2. Wählen Sie in [Studio](https://ml.azure.com) Ihren Arbeitsbereich aus, und verwenden Sie dann entweder die Seite __Endpunkte__ oder __Modelle__, um die Endpunktbereitstellung zu erstellen:

    # <a name="endpoints-page"></a>[Seite "Endpunkte"](#tab/endpoint)

    1. Wählen Sie auf der Seite __Endpunkte__ die Option **+Erstellen (Vorschau)** aus.

        :::image type="content" source="media/how-to-deploy-mlflow-models-online-endpoints/create-from-endpoints.png" lightbox="media/how-to-deploy-mlflow-models-online-endpoints/create-from-endpoints.png" alt-text="Screenshot: Option „Erstellen“ auf der Seite „Endpunkte“ der Benutzeroberfläche":::

    1. Geben Sie einen Namen und einen Authentifizierungstyp für den Endpunkt an, und wählen Sie dann __Weiter__ aus.
    1. Wenn Sie ein Modell auswählen, wählen Sie das zuvor registrierte MLflow-Modell. Klicken Sie auf __Weiter__, um fortzufahren.

    1. Wenn Sie im Schritt „Umgebung“ des Assistenten ein im MLflow-Format registriertes Modell auswählen, benötigen Sie kein Bewertungsskript und keine Umgebung.

        :::image type="content" source="media/how-to-deploy-mlflow-models-online-endpoints/ncd-wizard.png" lightbox="media/how-to-deploy-mlflow-models-online-endpoints/ncd-wizard.png" alt-text="Screenshot, der zeigt, dass für MLflow-Modelle kein Code und keine Umgebung benötigt werden":::

    1. Schließen Sie den Assistenten ab, um das Modell auf dem Endpunkt bereitzustellen.

        :::image type="content" source="media/how-to-deploy-mlflow-models-online-endpoints/review-screen-ncd.png" lightbox="media/how-to-deploy-mlflow-models-online-endpoints/review-screen-ncd.png" alt-text="Screenshot: NCD-Überprüfungsbildschirm":::

    # <a name="models-page"></a>[Seite „Modelle“](#tab/models)

    1. Wählen Sie das MLflow-Modell und dann __Bereitstellen__ aus. Wenn Sie dazu aufgefordert werden, wählen Sie __Auf Echtzeitendpunkt bereitstellen (Vorschau)__ aus.

        :::image type="content" source="media/how-to-deploy-mlflow-models-online-endpoints/deploy-from-models-ui.png" lightbox="media/how-to-deploy-mlflow-models-online-endpoints/deploy-from-models-ui.png" alt-text="Screenshot: Bereitstellung eines Modells über die Benutzeroberfläche „Modelle“":::

    1. Schließen Sie den Assistenten ab, um das Modell auf dem Endpunkt bereitzustellen.

    ---

## <a name="deploy-models-after-a-training-job"></a>Bereitstellen von Modellen nach einem Trainingsauftrag

In diesem Abschnitt erfahren Sie, wie Sie Modelle auf einem verwalteten Onlineendpunkt bereitstellen, nachdem Sie Ihren [Trainingsauftrag](how-to-train-cli.md) abgeschlossen haben.

1. Laden Sie die Ausgaben aus dem Trainingsauftrag herunter. Die Ausgaben enthalten den Modellordner.

    > [!NOTE]
    > Wenn Sie `mlflow.autolog()` in Ihrem Trainingsskript verwendet haben, werden Modellartefakte im Ausführungsverlauf des Auftrags angezeigt. Azure Machine Learning arbeitet mit der Nachverfolgungsfunktion von MLflow zusammen. Sie können `mlflow.autolog()` für mehrere gängige ML-Frameworks verwenden, um Modellparameter, Leistungsmetriken, Modellartefakte und sogar Featurerelevanzgraphen zu protokollieren.
    >
    > Weitere Informationen finden Sie unter [Trainieren von Modellen per CLI](how-to-train-cli.md#model-tracking-with-mlflow). Weitere Informationen finden Sie auch in den [Beispielen zu Trainingsaufträgen](https://github.com/Azure/azureml-examples/tree/main/cli/jobs/single-step) im GitHub-Repository.

    # <a name="azure-machine-learning-studio"></a>[Azure Machine Learning Studio](#tab/studio)

    :::image type="content" source="media/how-to-deploy-mlflow-models-online-endpoints/download-output-logs.png" lightbox="media/how-to-deploy-mlflow-models-online-endpoints/download-output-logs.png" alt-text="Screenshot: Herunterladen von Ausgaben und Protokollen aus der Ausführung „Experimentieren“":::

    # <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli)

    ```azurecli
    az ml job download -n $run_id --outputs
    ```

2. Für die Bereitstellung mithilfe der heruntergeladenen Dateien können Sie entweder Studio oder die Azure-Befehlszeilenschnittstelle verwenden. Verwenden Sie den Modellordner aus den Ausgaben für die Bereitstellung:

    * [Bereitstellung mit Azure Machine Learning Studio](how-to-deploy-mlflow-models-online-endpoints.md#deploy-using-azure-machine-learning-studio)
    * [Bereitstellung mit Azure Machine Learning CLI (v2)](how-to-deploy-mlflow-models-online-endpoints.md#deploy-using-cli-v2)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Bereitstellen von Modellen per REST (Vorschau)](how-to-deploy-with-rest.md)
- [Erstellen und Verwenden von verwalteten Onlineendpunkten (Vorschau) in Studio](how-to-use-managed-online-endpoint-studio.md)
- [Sicherer Rollout für Onlineendpunkte (Vorschau)](how-to-safely-rollout-managed-endpoints.md)
- [Automatisches Skalieren verwalteter Onlineendpunkte](how-to-autoscale-endpoints.md)
- [Verwenden von Batchendpunkten (Vorschau) für die Batchbewertung](how-to-use-batch-endpoint.md)
- [Anzeigen der Kosten für einen verwalteten Azure Machine Learning-Onlineendpunkt (Vorschau)](how-to-view-online-endpoints-costs.md)
- [Zugreifen auf Azure-Ressourcen mit einem verwalteten Onlineendpunkt und einer verwalteten Identität (Vorschau)](how-to-access-resources-from-endpoints-managed-identities.md)
- [Problembehandlung für die Bereitstellung verwalteter Onlineendpunkte](how-to-troubleshoot-managed-online-endpoints.md)