---
title: Verwalten von Azure Machine Learning-Umgebungen mit der Befehlszeilenschnittstelle (v2)
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie Azure ML-Umgebungen mithilfe der Azure CLI-Erweiterung für Machine Learning verwalten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: mx-iao
ms.author: minxia
ms.date: 10/21/2021
ms.reviewer: laobri
ms.custom: devx-track-azurecli, devplatv2
ms.openlocfilehash: 2c88c8954e334b5c6ffc80a60b2bd85cd98bda7a
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132055529"
---
# <a name="manage-azure-machine-learning-environments-with-the-cli-v2-preview"></a>Verwalten von Azure Machine Learning-Umgebungen mit der Befehlszeilenschnittstelle (v2) (Vorschau)

Mithilfe von Azure Machine Learning-Umgebungen werden die Ausführungsumgebungen für Ihre Aufträge oder Bereitstellungen definiert und die Abhängigkeiten für Ihren Code gekapselt. Azure ML verwendet die Umgebungsspezifikation, um den Docker-Container zu erstellen, in dem Ihr Trainings- oder Bewertungscode auf dem angegebenen Computeziel ausgeführt wird. Sie können eine Umgebung in einer Conda-Spezifikation, einem Docker-Image oder einem Docker-Buildkontext definieren.

In diesem Artikel erfahren Sie, wie Sie Azure Machine Learning-Umgebungen mithilfe der Befehlszeilenschnittstelle (v2) erstellen und verwalten.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Voraussetzungen

- Für die Verwendung der CLI benötigen Sie ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://azure.microsoft.com/free/) noch heute aus.
- [Installieren und Einrichten der Azure CLI-Erweiterung für Machine Learning](how-to-configure-cli.md)

> [!TIP]
> Verwenden Sie für eine umfassende Entwicklungsumgebung Visual Studio Code und die [Azure Machine Learning-Erweiterung](how-to-setup-vs-code.md), um [Azure Machine Learning-Ressourcen zu verwalten](how-to-manage-resources-vscode.md) und [Machine Learning-Modelle zu trainieren](tutorial-train-deploy-image-classification-model-vscode.md).

### <a name="clone-examples-repository"></a>Repository für Klonbeispiele

Klonen Sie zum Ausführen der Trainingsbeispiele zunächst das Beispielerepository, und wechseln Sie in das `cli`-Verzeichnis:

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="git_clone":::

Beachten Sie, dass `--depth 1` nur den letzten Commit in das Repository klont, wodurch die Zeit zum Abschließen des Vorgangs reduziert wird.

## <a name="curated-environments"></a>Zusammengestellte Umgebungen

Es gibt zwei Umgebungstypen in Azure ML: kuratierte und benutzerdefinierte Umgebungen. Kuratierte (zusammengestellte) Umgebungen sind vordefinierte Umgebungen, die beliebte ML-Frameworks und -Tools enthalten. Benutzerdefinierte Umgebungen werden von den Benutzer*innen konfiguriert und können über `az ml environment create` erstellt werden.

Kuratierte Umgebungen werden von Azure Machine Learning bereitgestellt und sind standardmäßig in Ihrem Arbeitsbereich verfügbar. Azure ML aktualisiert diese Umgebungen regelmäßig mit den neuesten Frameworkversionsreleases und verwaltet sie mit Fehlerbehebungen und Sicherheitspatches. Sie werden durch zwischengespeicherte Docker-Images unterstützt, durch die sich die Kosten für die Auftragsvorbereitung und die Zeit für die Modellimplementierung reduzieren.

Sie können diese kuratierten Umgebungen unverändert für das Training oder die Bereitstellung verwenden, indem Sie mithilfe der Syntax `azureml:<curated-environment-name>:<version>` auf eine bestimmte Umgebung verweisen. Sie können sie auch als Verweis auf Ihre eigenen benutzerdefinierten Umgebungen verwenden, indem Sie die Dockerfiles für diese kuratierten Umgebungen ändern.

Sie können die verfügbaren kuratierten Umgebungen auf der Benutzeroberfläche von Azure ML Studio oder mithilfe der Befehlszeilenschnittstelle (v2) über `az ml environments list` anzeigen.

## <a name="create-an-environment"></a>Erstellen einer Umgebung

Sie können eine Umgebung in einer Conda-Spezifikation, einem Docker-Image oder einem Docker-Buildkontext definieren. Konfigurieren Sie die Umgebung mithilfe einer YAML-Spezifikationsdatei, und erstellen Sie die Umgebung mit dem folgenden CLI-Befehl:

```cli
az ml environment create --file my_environment.yml
```

Die YAML-Referenzdokumentation für Azure ML-Umgebungen finden Sie unter [CLI-Umgebung (v2): YAML-Schema](reference-yaml-environment.md).

### <a name="create-an-environment-from-a-docker-image"></a>Erstellen einer Umgebung aus einem Docker-Image

Um eine Umgebung aus einem Docker-Image zu definieren, geben Sie den Image-URI des Images an, das in einer Registrierung wie Docker Hub oder Azure Container Registry gehostet wird. 

Das folgende Beispiel ist eine YAML-Spezifikationsdatei für eine Umgebung, die aus einem Docker-Image definiert wurde. Ein Image aus dem offiziellen PyTorch-Repository auf Docker Hub wird über die `image`-Eigenschaft in der YAML-Datei angegeben.

:::code language="yaml" source="~/azureml-examples-main/cli/assets/environment/docker-image.yml":::

So erstellen Sie die Umgebung

```cli
az ml environment create --file assets/environment/docker-image.yml
```

> [!TIP]
> Azure ML stellt einige CPU- und GPU-Basisimages auf Grundlage von Ubuntu Linux mit allgemeinen Systemabhängigkeiten bereit. Die GPU-Images enthalten beispielsweise Miniconda, OpenMPI, CUDA, cuDNN und NCCL. Sie können diese Images für Ihre Umgebungen verwenden. Sie können auch die zugehörigen Dockerfiles als Referenz verwenden, wenn Sie eigene benutzerdefinierte Images erstellen.
>  
> Informationen zu den Basisimages und den zugehörigen Dockerfiles finden Sie im [Repository „AzureML-Containers“](https://github.com/Azure/AzureML-Containers).

### <a name="create-an-environment-from-a-docker-build-context"></a>Erstellen einer Umgebung aus einem Docker-Buildkontext

Anstatt eine Umgebung aus einem vordefinierten Image zu definieren, können Sie dafür auch einen [Docker-Buildkontext](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#understand-build-context) verwenden. Geben Sie hierzu das Verzeichnis an, das als Buildkontext dienen soll. Dieses Verzeichnis sollte ein Dockerfile und alle anderen Dateien enthalten, die zum Erstellen des Images erforderlich sind.

Das folgende Beispiel ist eine YAML-Spezifikationsdatei für eine Umgebung, die aus einem Buildkontext definiert wurde. Der lokale Pfad zum Buildkontextordner wird im Feld `build.local_path` angegeben, und der relative Pfad zum Dockerfile innerhalb dieses Buildkontextordners wird im Feld `build.dockerfile_path` angegeben. Wenn `build.dockerfile_path` in der YAML-Datei ausgelassen wird, sucht Azure ML im Stammverzeichnis des Buildkontexts nach einem Dockerfile mit dem Namen `Dockerfile`.

In diesem Beispiel enthält der Buildkontext ein Dockerfile namens `Dockerfile` und die Datei `requirements.txt`, auf die im Dockerfile zum Installieren von Python-Paketen verwiesen wird.

:::code language="yaml" source="~/azureml-examples-main/cli/assets/environment/docker-context.yml":::

So erstellen Sie die Umgebung

```cli
az ml environment create --file assets/environment/docker-context.yml
```

Azure ML beginnt mit dem Erstellen des Images aus dem Buildkontext, nachdem die Umgebung erstellt wurde. Sie können den Buildstatus überwachen und die Buildprotokolle auf der Studio-Benutzeroberfläche anzeigen.

### <a name="create-an-environment-from-a-conda-specification"></a>Erstellen einer Umgebung aus einer Conda-Spezifikation

Sie können eine Umgebung mithilfe einer standardmäßigen Conda-YAML-Konfigurationsdatei definieren, in der die Abhängigkeiten für die Conda-Umgebung enthalten sind. Informationen zu diesem Standardformat finden Sie unter [Manuelles Erstellen einer Umgebung](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#creating-an-environment-file-manually).

Sie müssen auch ein Docker-Basisimage für diese Umgebung angeben. Azure ML erstellt die Conda-Umgebung auf der Grundlage des bereitgestellten Docker-Images.

Das folgende Beispiel ist eine YAML-Spezifikationsdatei für eine Umgebung, die aus einer Conda-Spezifikation definiert wurde. Hier wird der relative Pfad zur Conda-Datei in der YAML-Datei der Azure ML-Umgebung über die `conda_file`-Eigenschaft angegeben. Alternativ können Sie die Conda-Spezifikation auch inline mithilfe der `conda_file`-Eigenschaft definieren, anstatt sie in einer separaten Datei anzugeben.

:::code language="yaml" source="~/azureml-examples-main/cli/assets/environment/docker-image-plus-conda.yml":::

So erstellen Sie die Umgebung

```cli
az ml environment create --file assets/environment/docker-image-plus-conda.yml
```

Azure ML erstellt das endgültige Docker-Image aus dieser Umgebungsspezifikation, wenn die Umgebung in einem Auftrag oder einer Bereitstellung verwendet wird. Sie können einen Build der Umgebung auch manuell auf der Studio-Benutzeroberfläche auslösen.

## <a name="manage-environments"></a>Verwalten von Umgebungen

Die Befehlszeilenschnittstelle (v2) bietet unter `az ml environment` einige Befehle zum Verwalten des Lebenszyklus von Ressourcen in Ihrer Azure ML-Umgebung.

### <a name="list"></a>List

Auflisten aller Umgebungen in Ihrem Arbeitsbereich:

```cli
az ml environment list
```

Auflisten aller Umgebungsversionen unter einem bestimmten Namen:

```cli
az ml environment list --name docker-image-example
```

### <a name="show"></a>Anzeigen

Abrufen von Details zu einer bestimmten Umgebung:

```cli
az ml environment list --name docker-image-example --version 1
```

### <a name="update"></a>Aktualisieren

Aktualisieren änderbarer Eigenschaften einer bestimmten Umgebung:

```cli
az ml environment update --name docker-image-example --version 1 --set description="This is an updated description."
```

> [!IMPORTANT]
> Bei Umgebungen können nur `description` und `tags` aktualisiert werden. Alle anderen Eigenschaften sind unveränderlich. Wenn Sie eine dieser Eigenschaften ändern möchten, müssen Sie eine neue Version der Umgebung erstellen.

### <a name="delete"></a>Löschen

Löschen einer bestimmten Umgebung:

```cli
az ml environment delete --name docker-image-example --version 1
```

## <a name="use-environments-for-training"></a>Verwenden von Umgebungen für das Training

Um eine Umgebung für einen Trainingsauftrag zu verwenden, geben Sie das Feld `environment` der YAML-Konfiguration des Auftrags an. Sie können entweder mit `environment: azureml:<environment-name>:<environment-version>` auf eine vorhandene, registrierte Azure ML-Umgebung verweisen oder inline eine Umgebungsspezifikation definieren. Wenn Sie eine Umgebung inline definieren, geben Sie die Felder `name` und `version` nicht an, da diese Umgebungen als „anonyme“ Umgebungen behandelt und nicht in der Ressourcenregistrierung Ihrer Umgebung nachverfolgt werden.

Wenn Sie einen Trainingsauftrag übermitteln, kann das Erstellen einer neuen Umgebung einige Minuten dauern. Die Dauer hängt von der Größe der erforderlichen Abhängigkeiten ab. Die Umgebungen werden vom Dienst zwischengespeichert. Sofern die Umgebungsdefinition unverändert bleibt, wird die gesamte Setupzeit nur einmal in Anspruch genommen.

Weitere Informationen zur Verwendung von Umgebungen in Aufträgen finden Sie unter [Trainieren von Modellen mit der Befehlszeilenschnittstelle (v2)](how-to-train-cli.md).

## <a name="use-environments-for-model-deployments"></a>Verwenden von Umgebungen für Modellimplementierungen

Sie können Umgebungen für Ihre Modellimplementierungen auch für die Online- und die Batchbewertung verwenden. Geben Sie dazu das Feld `environment` in der YAML-Konfiguration für die Bereitstellung an.

Weitere Informationen zur Verwendung von Umgebungen in Bereitstellungen finden Sie unter [Bereitstellen und Bewerten eines Machine Learning-Modells mit einem Onlineendpunkt](how-to-deploy-managed-online-endpoints.md).

## <a name="next-steps"></a>Nächste Schritte

- [Trainieren von Modellen (Erstellen von Aufträgen) mit der CLI (v2)](how-to-train-cli.md)
- [Bereitstellen und Bewerten eines Machine Learning-Modells mit einem Onlineendpunkt](how-to-deploy-managed-online-endpoints.md)
- [CLI-Umgebung (v2): YAML-Schema](reference-yaml-environment.md)
