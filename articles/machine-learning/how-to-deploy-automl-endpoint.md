---
title: Bereitstellen eines AutoML-Modells mit einem verwalteten Onlineendpunkt (Vorschau)
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie Ihr AutoML-Modell als Webdienst bereitstellen, der automatisch von Azure verwaltet wird.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: ssambare
ms.reviewer: laobri
author: shivanissambare
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: how-to, devplatv2
ms.openlocfilehash: 5bc5208de912a6f11271681b754736bfafe17e8b
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566543"
---
# <a name="how-to-deploy-an-automl-model-to-an-online-endpoint-preview"></a>Bereitstellen eines AutoML-Modells an einem Onlineendpunkt (Vorschau)

In diesem Artikel erfahren Sie, wie Sie ein AutoML-trainiertes Machine Learning-Modell an einem Onlineendpunkt bereitstellen. Automatisiertes maschinelles Lernen, auch als automatisiertes ML oder AutoML bezeichnet, ist der Prozess des Automatisierens der zeitaufwändigen, iterativen Aufgaben der Entwicklung eines Machine Learning-Modells. Weitere Informationen finden Sie unter [Was ist automatisiertes maschinelles Lernen (AutoML)?](concept-automated-ml.md).

In diesem Artikel erfahren Sie, wie Sie ein AutoML-trainiertes Machine Learning-Modell mit Folgendem an Onlineendpunkten bereitstellen: 

- Azure Machine Learning Studio
- Azure Machine Learning CLI 2.0

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Voraussetzungen

Ein AutoML-trainiertes Machine Learning-Modell. Weitere Informationen finden Sie unter [Tutorial: Trainieren eines Klassifizierungsmodells mit AutoML ohne Schreiben von Code in Azure Machine Learning Studio](tutorial-first-experiment-automated-ml.md) oder [Tutorial: Vorhersage des Bedarfs mithilfe von automatisiertem maschinellem Lernen](tutorial-automated-ml-forecast.md).

## <a name="deploy-from-azure-machine-learning-studio-and-no-code"></a>Bereitstellen über Azure Machine Learning Studio und ohne Code

Die Bereitstellung eines mit AutoML trainierten Modells von der Seite „Automatisiertes ML“ aus ist ganz ohne Code möglich. Das bedeutet, Sie müssen kein Bewertungsskript und keine Umgebung vorbereiten. Beides wird automatisch generiert. 

1. Wechseln Sie zur Seite „Automatisiertes ML“ in Studio.
1. Wählen Sie Ihr Experiment und Ihre Ausführung aus.
1. Wählen Sie die Registerkarte „Modelle“ aus.
1. Wählen Sie das Modell aus, das Sie bereitstellen möchten. 
1. Nachdem Sie ein Modell ausgewählt haben, wird die Schaltfläche „Bereitstellen“ mit einem Dropdownmenü angezeigt.
1. Wählen Sie *Auf Echtzeitendpunkt bereitstellen (Vorschau)* aus.

   :::image type="content" source="media/how-to-deploy-automl-endpoint/deploy-button.png" lightbox="media/how-to-deploy-automl-endpoint/deploy-button.png" alt-text="Screenshot: Dropdownmenü der Schaltfläche „Bereitstellen“":::

   Das System generiert das Modell und die Umgebung, die für die Bereitstellung erforderlich sind. 

   :::image type="content" source="media/how-to-deploy-automl-endpoint/model.png" lightbox="media/how-to-deploy-automl-endpoint/model.png" alt-text="Screenshot: Generiertes Modell":::

   :::image type="content" source="media/how-to-deploy-automl-endpoint/environment.png" lightbox="media/how-to-deploy-automl-endpoint/environment.png" alt-text="Screenshot: Generierte Umgebung":::

5. Schließen Sie den Assistenten ab, um das Modell auf einem Echtzeitendpunkt bereitzustellen.

 :::image type="content" source="media/how-to-deploy-automl-endpoint/complete-wizard.png" lightbox="media/how-to-deploy-automl-endpoint/complete-wizard.png"  alt-text="Screenshot: Seite „Überprüfen und erstellen“":::


## <a name="deploy-manually-from-the-studio-or-command-line"></a>Manuelles Bereitstellen über Studio oder die Befehlszeile

Wenn Sie mehr Kontrolle über die Bereitstellung haben möchten, können Sie die Trainingsartefakte herunterladen und bereitstellen. 

So laden Sie die Komponenten herunter, die Sie für die Bereitstellung benötigen:

1. Wechseln Sie zu Ihrem AutoML-Experiment, und führen Sie es in Ihrem Machine Learning-Arbeitsbereich aus.
1. Wählen Sie die Registerkarte „Modelle“ aus.
1. Wählen Sie das Modell aus, das Sie verwenden möchten. Nachdem Sie ein Modell ausgewählt haben, wird die Schaltfläche *Herunterladen* aktiviert.
1. Wählen Sie *Herunterladen* aus.

:::image type="content" source="media/how-to-deploy-automl-endpoint/download-model.png" lightbox="media/how-to-deploy-automl-endpoint/download-model.png" alt-text="Screenshot: Auswahl des Modells und Schaltfläche „Herunterladen“":::

Sie erhalten eine ZIP-Datei, die Folgendes enthält:
* Eine Conda-Umgebungsspezifikationsdatei namens `conda_env_<VERSION>.yml`.
* Eine Python-Bewertungsdatei namens `scoring_file_<VERSION>.py`.
* Das Modell selbst in einer `.pkl`-Python-Datei namens `model.pkl`.

Für die Bereitstellung mithilfe der heruntergeladenen Dateien können Sie entweder Studio oder die Azure-Befehlszeilenschnittstelle verwenden.

# <a name="studio"></a>[Studio](#tab/Studio)

1. Wechseln Sie in Azure Machine Learning Studio zur Seite „Modelle“.

1. Klicken Sie auf die Option „+ Modell registrieren“.

1. Registrieren Sie das Modell, das Sie bei der AutoML-Ausführung heruntergeladen haben.

1. Wechseln Sie zur Seite „Umgebungen“, wählen Sie „Benutzerdefinierte Umgebung“ und dann die Option „+ Erstellen“ aus, um eine Umgebung für Ihre Bereitstellung zu erstellen. Verwenden Sie das heruntergeladene Conda-YAML, um eine benutzerdefinierte Umgebung zu erstellen.

1. Wählen Sie das Modell und dann in der Dropdownliste „Bereitstellen“ die Option „Auf Echtzeitendpunkt bereitstellen“ aus.

1. Führen Sie alle Schritte im Assistenten aus, um einen Onlineendpunkt und eine Bereitstellung zu erstellen.

 
# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

## <a name="configure-the-cli"></a>Konfigurieren der CLI 

Um eine Bereitstellung über die CLI zu erstellen, benötigen Sie die Azure CLI mit der ML v2-Erweiterung. Führen Sie den folgenden Befehl aus, um zu bestätigen, dass Sie über beides verfügen:

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_version":::

Wenn Sie eine Fehlermeldung erhalten oder `Extensions: ml` nicht angezeigt wird, führen Sie die Schritte unter [Installieren und Einrichten der CLI (v2)](how-to-configure-cli.md) aus.

Anmeldung:

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_login":::

Wenn Sie Zugriff auf mehrere Azure-Abonnements haben, können Sie Ihr aktives Abonnement festlegen:

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_account_set":::

Legen Sie die Standardressourcengruppe und den Standardarbeitsbereich auf den Ort fest, an dem Sie die Bereitstellung erstellen möchten:

:::code language="azurecli" source="~/azureml-examples-main/cli/setup.sh" id="az_configure_defaults":::

## <a name="put-the-scoring-file-in-its-own-directory"></a>Legen Sie die Bewertungsdatei in einem eigenen Verzeichnis ab.

Erstellen Sie ein Verzeichnis namens `src/`, und platzieren Sie darin die Bewertungsdatei, die Sie heruntergeladen haben. Dieses Verzeichnis wird in Azure hochgeladen und enthält den gesamten Quellcode, der zum Ziehen von Rückschlüssen erforderlich ist. Für ein AutoML-Modell gibt es nur die einzelne Bewertungsdatei. 

## <a name="create-the-endpoint-and-deployment-yaml-file"></a>Erstellen des Endpunkts und der YAML-Bereitstellungsdatei

Zum Erstellen eines verwalteten Onlineendpunkts über die Befehlszeile müssen Sie eine *endpoint.yml*- und eine *deployment.yml*-Datei erstellen. Der folgende Code aus dem Repository für [Azure Machine Learning-Beispiele](https://github.com/Azure/azureml-examples) zeigt das Verzeichnis _endpoints/online/managed/sample/_ an, das alle erforderlichen Eingaben erfasst:

__automl_endpoint.yml__

::: code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/managed/sample/endpoint.yml" :::

__automl_deployment.yml__

::: code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/managed/sample/blue-deployment.yml" :::

Sie müssen diese Datei ändern, um die Dateien zu verwenden, die Sie von der Seite für AutoML-Modelle heruntergeladen haben.

1. Erstellen Sie die Dateien `automl_endpoint.yml` und `automl_deployment.yml`, und fügen Sie den Inhalt des obigen Beispiels ein.

1. Ändern Sie den `name`-Wert des Endpunkts. Der Endpunktname muss innerhalb der Azure-Region eindeutig sein. Der Name eines Endpunkts muss mit einem Groß- oder Kleinbuchstaben beginnen und darf nur aus Bindestrichen (-) und alphanumerischen Zeichen bestehen.

1. Ändern Sie in der Datei `automl_deployment` den Wert der Schlüssel in den folgenden Pfaden:

    | Pfad | Ändern in |
    | --- | --- |
    | `model:local_path` | Der Pfad zur heruntergeladenen Datei `model.pkl`. |
    | `code_configuration:code:local_path` | Das Verzeichnis, in dem Sie die Bewertungsdatei platziert haben. | 
    | `code_configuration:scoring_script` | Der Name der Python-Bewertungsdatei (`scoring_file_<VERSION>.py`). |
    | `environment:conda_file` | Eine Datei-URL für die heruntergeladene Conda-Umgebungsdatei (`conda_env_<VERSION>.yml`). |

    > [!NOTE]
    > Eine vollständige Beschreibung zu YAML finden Sie in der [YAML-Referenz zu verwalteten Onlineendpunkten (Vorschauversion)](reference-yaml-endpoint-managed-online.md).

3. Führen Sie Folgendes an der Befehlszeile aus: 

    ```azurecli
    az ml online-endpoint create -f automl_endpoint.yml
    az ml online-deployment create -f automl_deployment.yml
    ```
    
---

Nachdem Sie eine Bereitstellung erstellt haben, können Sie sie, wie unter [Aufrufen des Endpunkts zum Bewerten von Daten mit Ihrem Modell](how-to-deploy-managed-online-endpoints.md#invoke-the-endpoint-to-score-data-by-using-your-model) beschrieben, auswerten.

## <a name="next-steps"></a>Nächste Schritte

- [Problembehandlung für die Bereitstellung verwalteter Onlineendpunkte](how-to-troubleshoot-managed-online-endpoints.md)
- [Sicherer Rollout für Onlineendpunkte](how-to-safely-rollout-managed-endpoints.md)
