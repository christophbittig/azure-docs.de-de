---
title: Bereitstellen eines benutzerdefinierten Containers mit einem verwalteten Onlineendpunkt
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie einen benutzerdefinierten Container verwenden, um Open-Source-Server in Azure Machine Learning einzusetzen.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.reviewer: larryfr
ms.date: 06/16/2021
ms.topic: how-to
ms.custom: deploy, devplatv2
ms.openlocfilehash: 4aa55ee69b1092b7a2cb8dd207faf577ac532e32
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/04/2021
ms.locfileid: "129428253"
---
# <a name="deploy-a-tensorflow-model-served-with-tf-serving-using-a-custom-container-in-a-managed-online-endpoint-preview"></a>Bereitstellen eines TensorFlow-Modells mit TF Serving mithilfe eines benutzerdefinierten Containers in einem verwalteten Onlineendpunkt (Vorschau)

Erfahren Sie, wie Sie einen benutzerdefinierten Container als verwalteten Onlineendpunkt in Azure Machine Learning bereitstellen.

Benutzerdefinierte Containerbereitstellungen können andere Webserver als den standardmäßigen Python Flask-Server verwenden, der von Azure Machine Learning verwendet wird. Benutzer dieser Bereitstellungen können weiterhin die Vorteile der in Azure Machine Learning integrierten Überwachung, Skalierung, Warnungen und Authentifizierung nutzen.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

> [!WARNING]
> Microsoft kann möglicherweise nicht helfen, Probleme zu beheben, die durch ein benutzerdefiniertes Image verursacht werden. Wenn Sie auf Probleme stoßen, werden Sie möglicherweise aufgefordert, das Standardimage oder eines der von Microsoft bereitgestellten Images zu verwenden, um zu sehen, ob das Problem von Ihrem Image verursacht wird.

## <a name="prerequisites"></a>Voraussetzungen

* Sie müssen die Azure CLI- und die ML-Erweiterung installieren und konfigurieren. Weitere Informationen finden Sie unter [Installieren, Einrichten und Verwenden der CLI (v2) (Vorschauversion)](how-to-configure-cli.md). 

* Sie müssen über eine Azure-Ressourcengruppe verfügen, für die Ihnen (oder dem von Ihnen genutzten Dienstprinzipal) die Zugriffsberechtigung `Contributor` (Mitwirkender) zugeordnet ist. Sie verfügen über eine Ressourcengruppe dieser Art, wenn Sie Ihre ML-Erweiterung gemäß dem obigen Artikel konfiguriert haben. 

* Sie müssen über einen Azure Machine Learning-Arbeitsbereich verfügen. Sie verfügen über einen Arbeitsbereich dieser Art, wenn Sie Ihre ML-Erweiterung gemäß dem obigen Artikel konfiguriert haben.

* Falls Sie die Standardwerte für die Azure CLI noch nicht festgelegt haben, sollten Sie Ihre Standardeinstellungen speichern. Führen Sie Folgendes aus, um zu vermeiden, dass Sie die Werte immer wieder neu übergeben müssen:

   ```azurecli
   az account set --subscription <subscription id>
   az configure --defaults workspace=<azureml workspace name> group=<resource group>

* To deploy locally, you must have [Docker engine](https://docs.docker.com/engine/install/) running locally. This step is **highly recommended**. It will help you debug issues.

## Download source code

To follow along with this tutorial, download the source code below.

```azurecli-interactive
git clone https://github.com/Azure/azureml-examples --depth 1
cd azureml-examples/cli
```

## <a name="initialize-environment-variables"></a>Initialisieren von Umgebungsvariablen

Definieren von Umgebungsvariablen:

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-tfserving.sh" id="initialize_variables":::

## <a name="download-a-tensorflow-model"></a>Herunterladen eines TensorFlow-Modells

Laden Sie ein Modell herunter, das eine Eingabe durch zwei dividiert und dem Ergebnis 2 hinzufügt, und entzippen Sie es:

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-tfserving.sh" id="download_and_unzip_model":::

## <a name="run-a-tf-serving-image-locally-to-test-that-it-works"></a>Lokales Ausführen eines TF Serving-Images, um zu testen, ob es funktioniert

Verwenden Sie Docker, um Ihr Image lokal zum Testen auszuführen:

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-tfserving.sh" id="run_image_locally_for_testing":::

### <a name="check-that-you-can-send-liveness-and-scoring-requests-to-the-image"></a>Überprüfen, ob Sie Verfügbarkeits- und Bewertungsanforderungen an das Image senden können

Überprüfen Sie zunächst, ob der Container „lebt“, d. h. der Prozess noch im Container ausgeführt wird. Sie sollten eine „200 (OK)“-Antwort erhalten.

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-tfserving.sh" id="check_liveness_locally":::

Überprüfen Sie dann, ob Sie Vorhersagen zu Daten ohne Bezeichnung erhalten:

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-tfserving.sh" id="check_scoring_locally":::

### <a name="stop-the-image"></a>Beenden des Images

Nachdem Sie das Image lokal getestet haben, beenden Sie es:

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-tfserving.sh" id="stop_image":::

## <a name="create-a-yaml-file-for-your-endpoint"></a>Erstellen einer YAML-Datei für Ihren Endpunkt

Sie können Ihre Cloudbereitstellung mit YAML konfigurieren. Sehen Sie sich das YAML-Beispiel für diesen Endpunkt an:

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/custom-container/tfserving-endpoint.yml":::

In dieser YAML-Datei sind einige wichtige Konzepte zu beachten:

### <a name="readiness-route-vs-liveness-route"></a>Bereitschaftsroute im Vergleich zur Verfügbarkeitsroute

Ein HTTP-Server kann optional sowohl für _Verfügbarkeit_ als auch für _Bereitschaft_ Pfade definieren. Eine Verfügbarkeitsroute wird verwendet, um zu überprüfen, ob der Server ausgeführt wird. Eine Bereitschaftsroute wird verwendet, um zu überprüfen, ob der Server arbeitsbereit ist. Im Machine Learning-Rückschluss könnte ein Server eine „200 OK“-Antwort auf eine Verfügbarkeitsanforderung geben, bevor er ein Modell lädt. Der Server kann erst dann mit „200 OK“ auf eine Bereitschaftsanforderung antworten, nachdem das Modell in den Arbeitsspeicher geladen wurde.

In der [Kubernetes-Dokumentation](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/) finden Sie weitere Informationen zu Verfügbarkeits- und Bereitschaftstests.

Beachten Sie, dass diese Bereitstellung sowohl für Verfügbarkeit als auch für Bereitschaft denselben Pfad verwendet, da TF Serving nur eine Verfügbarkeitsroute definiert.

### <a name="locating-the-mounted-model"></a>Suchen des eingebundenen Modells

Wenn Sie ein Modell als Echtzeit-Endpunkt bereitstellen, _bindet_ Azure Machine Learning Ihr Modell in Ihren Endpunkt ein. Mit der Modelleinbindung können Sie neue Versionen des Modells bereitstellen, ohne ein neues Docker-Image erstellen zu müssen. Standardmäßig befindet sich ein mit dem Namen *foo* und Version *1* registriertes Modell im folgenden Pfad in Ihrem bereitgestellten Container: `/var/azureml-app/azureml-models/foo/1`

Dies gilt beispielsweise, wenn Sie auf Ihrem lokalen Computer über die folgende Verzeichnisstruktur verfügen:

```
azureml-examples
  cli
    endpoints
      online
        custom-container
          half_plus_two
          tfserving-endpoint.yml    
```     

und `tfserving-endpoint.yml` enthält:

```
model:
    name: tfserving-mounted
    version: 1
    local_path: ./half_plus_two
```

dann befindet sich Ihr Modell in Ihrem Endpunkt an folgendem Speicherort:

```
var 
  azureml-app
    azureml-models
      tfserving-endpoint
        1
          half_plus_two
```

### <a name="create-the-endpoint"></a>Erstellen des Endpunkts

Nachdem Sie nun wissen, wie die YAML-Datei erstellt wurde, erstellen Sie Ihren Endpunkt. Die Ausführung dieses Befehls kann einige Minuten dauern.

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-tfserving.sh" id="create_endpoint":::

### <a name="invoke-the-endpoint"></a>Aufrufen des Endpunkts

Nachdem die Bereitstellung abgeschlossen ist, prüfen Sie, ob Sie eine Bewertungsanforderung an den bereitgestellten Endpunkt senden können.

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-tfserving.sh" id="invoke_endpoint":::

### <a name="delete-endpoint-and-model"></a>Löschen von Endpunkt und Modell

Nachdem Sie ihren Endpunkt erfolgreich bewertet haben, können Sie ihn löschen:

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-tfserving.sh" id="delete_endpoint_and_model":::

## <a name="next-steps"></a>Nächste Schritte

- [Sicherer Rollout für Onlineendpunkte (Vorschau)](how-to-safely-rollout-managed-endpoints.md)
- [Problembehandlung für die Bereitstellung verwalteter Onlineendpunkte](how-to-troubleshoot-managed-online-endpoints.md)
- [Torchserve-Beispiel](https://github.com/Azure/azureml-examples/blob/main/cli/deploy-torchserve.sh)