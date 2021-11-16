---
title: Modellbereitstellung mit hoher Leistung mit Triton (Vorschau)
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie Ihr Modell mit NVIDIA Triton Inference Server in Azure Machine Learning bereitstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 11/03/2021
ms.topic: how-to
ms.reviewer: larryfr
ms.author: ssambare
author: shivanissambare
ms.custom: deploy, devplatv2
ms.openlocfilehash: d4c65af505725f3f667dd2ad51902717352e4c42
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063663"
---
# <a name="high-performance-serving-with-triton-inference-server-preview"></a>Bereitstellung mit hoher Leistung mit Triton Inference Server (Vorschau) 

Erfahren Sie, wie Sie [NVIDIA Triton Inference Server](https://aka.ms/nvidia-triton-docs) in Azure Machine Learning mit [verwalteten Online-Endpunkten](concept-endpoints.md#managed-online-endpoints) verwenden.

Triton ist eine Open-Source-Software mit mehreren Frameworks, die für Inferenzen optimiert ist. Es unterstützt beliebte maschinelle Lernsysteme wie TensorFlow, ONNX Runtime, PyTorch, NVIDIA TensorRT und andere. Sie kann für Ihre CPU- oder GPU-Arbeitslasten verwendet werden.

In diesem Artikel erfahren Sie, wie Sie Triton und ein Modell auf einem verwalteten Online-Endpunkt einsetzen. Es werden Informationen zur Verwendung der CLI (Befehlszeile) und des Azure Machine Learning Studio bereitgestellt.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

> [!NOTE]
> [NVIDIA Triton Inference Server](https://aka.ms/nvidia-triton-docs) ist eine Open Source-Software eines Drittanbieters, die in Azure Machine Learning integriert ist.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [basic prereqs](../../includes/machine-learning-cli-prereqs.md)]

* Eine funktionierende Python 3.8 (oder höher) Umgebung.

* Zugang zu VMs der NCv3-Serie für Ihr Azure-Abonnement.

    > [!IMPORTANT]
    > Möglicherweise müssen Sie eine Quotenerhöhung für Ihr Abonnement beantragen, bevor Sie diese Reihe von VMs verwenden können. Weitere Informationen finden Sie unter [NCv3-series](/azure/virtual-machines/ncv3-series).

[!INCLUDE [clone repo & set defaults](../../includes/machine-learning-cli-prepare.md)]

NVIDIA Triton Inference Server erfordert eine spezielle Modell-Repository-Struktur, in der es ein Verzeichnis für jedes Modell und Unterverzeichnisse für die Modellversion gibt. Der Inhalt der einzelnen Unterverzeichnisse der Modellversionen wird durch den Typ des Modells und die Anforderungen des Backends, das das Modell unterstützt, bestimmt. Um die gesamte Struktur des Modell-Repository zu sehen [https://github.com/triton-inference-server/server/blob/main/docs/model_repository.md#model-files](https://github.com/triton-inference-server/server/blob/main/docs/model_repository.md#model-files)

Die Informationen in diesem Dokument basieren auf der Verwendung eines im ONNX-Format gespeicherten Modells, so dass die Verzeichnisstruktur des Modell-Repositorys `<model-repository>/<model-name>/1/model.onnx` lautet. Konkret führt dieses Modell eine Bildidentifizierung durch.

## <a name="deploy-using-cli-v2"></a>Bereitstellung über CLI (v2)

In diesem Abschnitt wird gezeigt, wie Sie Triton mit der Azure CLI und der Machine Learning-Erweiterung (v2) auf verwalteten Online-Endpunkten einsetzen können.

> [!IMPORTANT]
> Für die Bereitstellung ohne Code in Triton wird das **[Testen über lokale Endpunkte](how-to-deploy-managed-online-endpoints.md#deploy-and-debug-locally-by-using-local-endpoints)** derzeit nicht unterstützt.

1. Um zu vermeiden, dass Sie einen Pfad für mehrere Befehle eingeben müssen, verwenden Sie den folgenden Befehl, um eine `BASE_PATH`-Umgebungsvariable zu setzen. Diese Variable verweist auf das Verzeichnis, in dem sich das Modell und die zugehörigen YAML-Konfigurationsdateien befinden:

    ```azurecli
    BASE_PATH=endpoints/online/triton/single-model
    ```

1. Verwenden Sie den folgenden Befehl, um den Namen des zu erstellenden Endpunkts festzulegen. In diesem Beispiel wird ein zufälliger Name für den Endpunkt erstellt:

    :::code language="azurecli" source="~/azureml-examples-main/cli/deploy-triton-managed-online-endpoint.sh" ID="set_endpoint_name":::

1. Installieren Sie die Python-Anforderungen mit den folgenden Befehlen:

    ```azurecli
    pip install numpy
    pip install tritonclient[http]
    pip install pillow
    pip install gevent
    ```

1. Erstellen Sie eine YAML-Konfigurationsdatei für Ihren Endpunkt. Im folgenden Beispiel werden der Name und der Authentifizierungsmodus des Endpunkts konfiguriert. Der in den folgenden Befehlen verwendete Befehl befindet sich unter `/cli/endpoints/online/triton/single-model/create-managed-endpoint.yml` im azureml-examples-Repository, das Sie zuvor geklont haben:

    __create-managed-endpoint.yaml__

    :::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/triton/single-model/create-managed-endpoint.yaml":::

1. Um einen neuen Endpunkt unter Verwendung der YAML-Konfiguration zu erstellen, verwenden Sie den folgenden Befehl:

    :::code language="azurecli" source="~/azureml-examples-main/cli/deploy-triton-managed-online-endpoint.sh" ID="create_endpoint":::

1. Erstellen Sie eine YAML-Konfigurationsdatei für das Bereitstellen. Im folgenden Beispiel wird eine Bereitstellung mit dem Namen __blue__ für den im vorherigen Schritt erstellten Endpunkt konfiguriert. Der in den folgenden Befehlen verwendete Befehl befindet sich unter `/cli/endpoints/online/triton/single-model/create-managed-deployment.yml` im azureml-examples-Repository, das Sie zuvor geklont haben:

    > [!IMPORTANT]
    > Damit Triton no-code-deployment (NCD) funktioniert, muss **`model_format`** auf **`Triton`** gesetzt werden. Weitere Informationen finden Sie in [CLI (v2) model YAML schema](reference-yaml-model.md).
    >
    > Diese Bereitstellung verwendet eine Standard_NC6s_v3-VM. Möglicherweise müssen Sie eine Quotenerhöhung für Ihr Abonnement beantragen, bevor Sie diese VM nutzen können. Weitere Informationen finden Sie unter [NCv3-series](/azure/virtual-machines/ncv3-series).

    :::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/triton/single-model/create-managed-deployment.yaml":::

1. Um die Bereitstellung mit der YAML-Konfiguration zu erstellen, verwenden Sie den folgenden Befehl:

    :::code language="azurecli" source="~/azureml-examples-main/cli/deploy-triton-managed-online-endpoint.sh" ID="create_deployment":::

### <a name="invoke-your-endpoint"></a>Rufen Sie Ihren Endpunkt auf

Sobald die Bereitstellung abgeschlossen ist, verwenden Sie den folgenden Befehl, um eine Bewertungsanfrage an den bereitgestellten Endpunkt zu stellen. 

> [!TIP]
> Die Datei `/cli/endpoints/online/triton/single-model/triton_densenet_scoring.py` im azureml-examples Repo wird für die Bewertung verwendet. Das an den Endpunkt übermittelte Bild muss vorverarbeitet werden, um die Anforderungen an Größe, Typ und Format zu erfüllen, und nachverarbeitet werden, um das vorhergesagte Label anzuzeigen. Die `triton_densenet_scoring.py` verwendet die `tritonclient.http`-Bibliothek zur Kommunikation mit dem Triton-Inferenzserver.

1. Um die Endpunktbewertungsuri zu erhalten, verwenden Sie den folgenden Befehl:

    :::code language="azurecli" source="~/azureml-examples-main/cli/deploy-triton-managed-online-endpoint.sh" ID="get_scoring_uri":::

1. Um ein Authentifizierungs-Token zu erhalten, verwenden Sie den folgenden Befehl:

    :::code language="azurecli" source="~/azureml-examples-main/cli/deploy-triton-managed-online-endpoint.sh" ID="get_token":::

1. Um Daten mit dem Endpunkt zu bewerten, verwenden Sie den folgenden Befehl. Er übermittelt das Bild eines Pfaus (https://aka.ms/peacock-pic) ) an den Endpunkt:

    :::code language="azurecli" source="~/azureml-examples-main/cli/deploy-triton-managed-online-endpoint.sh" ID="check_scoring_of_model":::

    Die Antwort des Skripts ähnelt dem folgenden Text:

    ```
    Is server ready - True
    Is model ready - True
    /azureml-examples/cli/endpoints/online/triton/single-model/densenet_labels.txt
    84 : PEACOCK
    ```

### <a name="delete-your-endpoint-and-model"></a>Löschen Sie Ihren Endpunkt und Ihr Modell

Wenn Sie mit dem Endpunkt fertig sind, verwenden Sie den folgenden Befehl, um ihn zu löschen:

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-triton-managed-online-endpoint.sh" ID="delete_endpoint":::

Verwenden Sie den folgenden Befehl, um Ihr Modell zu löschen:

```azurecli
az ml model delete --name $MODEL_NAME --version $MODEL_VERSION
```

## <a name="deploy-using-azure-machine-learning-studio"></a>Bereitstellung mit Azure Machine Learning Studio

Dieser Abschnitt zeigt, wie Sie Triton mit [Azure Machine Learning Studio](https://ml.azure.com) auf verwalteten Online-Endpunkten einsetzen können.

1. Registrieren Sie Ihr Modell im Triton-Format mit dem folgenden YAML- und CLI-Befehl. Die YAML verwendet ein densenet-onnx-Modell von [https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online/triton/single-model](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online/triton/single-model)

    __create-triton-model.yaml__

    ```yml
    name: densenet-onnx-model
    version: 1
    local_path: ./models
    model_format: Triton
    description: Registering my Triton format model.
    ```

    ```azurecli
    az ml model create -f create-triton-model.yaml
    ```

    Der folgende Screenshot zeigt, wie Ihr registriertes Modell auf der Seite __Modelle__ von Azure Machine Learning Studio aussehen wird.

    :::image type="content" source="media/how-to-deploy-with-triton/triton-model-format.png" lightbox="media/how-to-deploy-with-triton/triton-model-format.png" alt-text="Screenshot, der das Triton-Modellformat auf der Seite Modelle zeigt.":::


1. Wählen Sie in [Studio](https://ml.azure.com) Ihren Arbeitsbereich und verwenden Sie dann entweder die Seite __Endpunkte__ oder __Modelle__, um die Endpunktverteilung zu erstellen:

    # <a name="endpoints-page"></a>[Seite "Endpunkte"](#tab/endpoint)

    1. Wählen Sie auf der Seite __Endpunkte__ die Optionen **+Erstellen (Vorschau)** .

        :::image type="content" source="media/how-to-deploy-with-triton/create-option-from-endpoints-page.png" lightbox="media/how-to-deploy-with-triton/create-option-from-endpoints-page.png" alt-text="Screenshot mit der Erstellungsoption auf der Seite Endpoints UI.":::

    1. Geben Sie einen Namen und einen Authentifizierungstyp für den Endpunkt an, und wählen Sie dann __Weiter__.
    1. Wenn Sie ein Modell auswählen, wählen Sie das zuvor registrierte Triton-Modell. Klicken Sie auf __Weiter__, um fortzufahren.

    1. Wenn Sie im Schritt Umgebung des Assistenten ein im Triton-Format registriertes Modell auswählen, benötigen Sie kein Scoring-Skript und keine Umgebung.

        :::image type="content" source="media/how-to-deploy-with-triton/ncd-triton.png" lightbox="media/how-to-deploy-with-triton/ncd-triton.png" alt-text="Screenshot, der zeigt, dass für Triton-Modelle kein Code und keine Umgebung benötigt werden":::

    1. Schließen Sie den Assistenten ab, um das Modell auf dem Endpunkt bereitzustellen.

        :::image type="content" source="media/how-to-deploy-with-triton/review-screen-triton.png" lightbox="media/how-to-deploy-with-triton/review-screen-triton.png" alt-text="Screenshot des NCD-Überprüfungsbildschirms":::

    # <a name="models-page"></a>[Modelle Seite](#tab/models)

    1. Wählen Sie das Triton-Modell aus und wählen Sie dann __Einsatz__. Wenn Sie dazu aufgefordert werden, wählen Sie __Auf Echtzeit-Endpunkt bereitstellen (Vorschau)__ .

        :::image type="content" source="media/how-to-deploy-with-triton/deploy-from-models-page.png" lightbox="media/how-to-deploy-with-triton/deploy-from-models-page.png" alt-text="Screenshot zeigt die Bereitstellung eines Modells über die Models UI":::

    1. Schließen Sie den Assistenten ab, um das Modell auf dem Endpunkt bereitzustellen.

    ---

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Bereitstellen von Modellen per REST (Vorschau)](how-to-deploy-with-rest.md)
- [Erstellen und Verwenden von verwalteten Onlineendpunkten (Vorschau) in Studio](how-to-use-managed-online-endpoint-studio.md)
- [Sicherer Rollout für Onlineendpunkte (Vorschau)](how-to-safely-rollout-managed-endpoints.md)
- [Wie verwaltete Online-Endpunkte automatisch skaliert werden](how-to-autoscale-endpoints.md)
- [Anzeigen der Kosten für einen verwalteten Azure Machine Learning-Onlineendpunkt (Vorschau)](how-to-view-online-endpoints-costs.md)
- [Zugang zu Azure-Ressourcen mit einem verwalteten Online-Endpunkt und einer verwalteten Identität (Vorschau)](how-to-access-resources-from-endpoints-managed-identities.md)
- [Problembehandlung für die Bereitstellung verwalteter Onlineendpunkte](how-to-troubleshoot-managed-online-endpoints.md)