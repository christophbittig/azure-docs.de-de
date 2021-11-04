---
title: Erweitern eines vordefinierten Docker-Images
titleSuffix: Azure Machine Learning
description: Erweitern von vordefinierten Docker-Images in Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: ssambare
author: shivanissambare
ms.date: 10/21/2021
ms.topic: how-to
ms.reviewer: larryfr
ms.custom: deploy, docker, prebuilt
ms.openlocfilehash: a25416951d7cc124397d4701992ec9a04968d443
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131562407"
---
# <a name="extend-a-prebuilt-docker-image"></a>Erweitern eines vordefinierten Docker-Images

Es kann vorkommen, dass die [vordefinierten Docker-Images für Modellrückschlüsse](concept-prebuilt-docker-images-inference.md) und die [Erweiterbarkeitslösungen](./how-to-prebuilt-docker-images-inference-python-extensibility.md) für Azure Machine Learning Ihre Anforderungen an den Rückschlussdienst nicht vollständig erfüllen.

In diesem Fall können Sie ein Dockerfile verwenden, um ein neues Image zu erstellen, indem Sie eines der vordefinierten Images als Ausgangspunkt verwenden. Durch die Erweiterung eines vordefinierten Docker-Images können Sie den Netzwerkstapel und die Bibliotheken von Azure Machine Learning nutzen, ohne ein Image von Grund auf neu zu erstellen.

**Vor- und Nachteile**

Die Verwendung eines Dockerfile ermöglicht eine vollständige Anpassung des Images vor der Bereitstellung. So können sie genau steuern, welche Abhängigkeiten, Umgebungsvariablen und anderen Einstellungen im Container festgelegt sind.

Der Hauptnachteil dieses Ansatzes ist, dass während der Bereitstellung ein zusätzlicher Buildvorgang für Images durchgeführt wird. Hierdurch wird der Bereitstellungsprozess verlangsamt. Wenn Sie die Möglichkeit haben, das [Erweiterbarkeitsverfahren für Python-Pakete](./how-to-prebuilt-docker-images-inference-python-extensibility.md) zu verwenden, ist der Zeitaufwand für die Bereitstellung geringer.
## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure Machine Learning-Arbeitsbereich. Ein Tutorial zur Erstellung eines Arbeitsbereichs finden Sie unter [Erste Schritte mit Azure Machine Learning](quickstart-create-resources.md).
* Kenntnisse in Bezug auf die Erstellung eines [Dockerfiles](https://docs.docker.com/engine/reference/builder/).
* Entweder eine lokal funktionsfähige Installation von [Docker](https://www.docker.com/), einschließlich `docker`-CLI, **ODER** eine ACR-Instanz (Azure Container Registry), die Ihrem Azure Machine Learning-Arbeitsbereich zugeordnet ist.

    > [!WARNING]
    > Die Azure Container Registry für Ihren Arbeitsbereich wird erstellt, wenn Sie ein Modell zum ersten Mal über den Arbeitsbereich trainieren oder bereitstellen. Wenn Sie einen neuen Arbeitsbereich, aber kein Training oder Modell erstellt haben, existiert für den Arbeitsbereich keine Azure Container Registry.
## <a name="create-and-build-dockerfile"></a>Durchführen der Erstellung und des Buildvorgangs für ein Dockerfile

Hier ist ein Beispiel für ein Dockerfile angegeben, bei dem ein auf Azure Machine Learning basierendes vordefiniertes Docker-Image als Basisimage verwendet wird:

```Dockerfile
FROM mcr.microsoft.com/azureml/<image_name>:<tag>

COPY requirements.txt /tmp/requirements.txt

RUN pip install –r /tmp/requirements.txt
```

Anschließend fügen Sie das obige Dockerfile mit allen erforderlichen Dateien in das Verzeichnis ein und führen den folgenden Befehl aus, um den Buildvorgang für das Image durchzuführen:

```bash
docker build -f <above dockerfile> -t <image_name>:<tag> .
```

> [!TIP]
> Ausführlichere Informationen zu `docker build` finden Sie in der [Docker-Dokumentation](https://docs.docker.com/engine/reference/commandline/build/).

Wenn der Befehl `docker build` lokal nicht verfügbar ist, sollten Sie die ACR-Instanz (Azure Container Registry) Ihres Azure Machine Learning-Arbeitsbereichs nutzen, um das Docker-Image in der Cloud zu kompilieren. Weitere Informationen finden Sie unter [Tutorial: Erstellen und Bereitstellen von Containerimages mit Azure Container Registry](../container-registry/container-registry-tutorial-quick-task.md).

> [!IMPORTANT]
> Die Empfehlung von Microsoft lautet, dass Sie zunächst überprüfen sollten, ob Ihr Dockerfile lokal funktioniert. Versuchen Sie erst dann, über die Azure Container Registry-Instanz ein benutzerdefiniertes Basisimage zu erstellen.

Die folgenden Abschnitte enthalten ausführlichere Details zum Dockerfile.

## <a name="install-extra-packages"></a>Installieren von zusätzlichen Paketen

Falls noch weitere `apt`-Pakete im Ubuntu-Container installiert werden müssen, können Sie sie im Dockerfile hinzufügen. Im folgenden Beispiel wird veranschaulicht, wie Sie den Befehl `apt-get` aus einem Dockerfile verwenden:

```Dockerfile
FROM <prebuilt docker image from MCR>

# Switch to root to install apt packages
USER root:root

RUN apt-get update && \
    apt-get install -y \
    <package-1> \
    ... 
    <package-n> && \
    apt-get clean -y && \
    rm -rf /var/lib/apt/lists/*

# Switch back to non-root user
USER dockeruser
```

Sie können auch zusätzliche pip-Pakete über einen Dockerfile installieren. Im folgenden Beispiel wird die Verwendung von `pip install` veranschaulicht:

```Dockerfile
RUN pip install <library>
```

<a id="buildmodel"></a>

## <a name="build-model-and-code-into-images"></a>Einbinden des Modells und Codes in Images

Wenn Sie das Modell und den Code in das Image einbinden möchten, müssen Sie im Dockerfile die folgenden Umgebungsvariablen festlegen:

* `AZUREML_ENTRY_SCRIPT`: Das Eingabeskript Ihres Codes. Diese Datei enthält die Methoden `init()` und `run()`.
* `AZUREML_MODEL_DIR`: Das Verzeichnis, in dem die Modelldateien enthalten sind. Im Eingabeskript sollte dieses Verzeichnis als Stammverzeichnis des Modells verwendet werden.

Im folgenden Beispiel wird das Festlegen dieser Umgebungsvariablen im Dockerfile veranschaulicht:

```Dockerfile
FROM <prebuilt docker image from MCR>

# Code
COPY <local_code_directory> /var/azureml-app
ENV AZUREML_ENTRY_SCRIPT=<entryscript_file_name>

# Model
COPY <model_directory> /var/azureml-app/azureml-models
ENV AZUREML_MODEL_DIR=/var/azureml-app/azureml-models
```

## <a name="example-dockerfile"></a>Beispiel für Dockerfile

Im folgenden Beispiel wird das Installieren von `apt`-Paketen, Festlegen von Umgebungsvariablen und Einbinden von Code und Modellen in das Dockerfile veranschaulicht:

```Dockerfile
FROM mcr.microsoft.com/azureml/pytorch-1.6-ubuntu18.04-py37-cpu-inference:latest 

USER root:root

# Install libpng-tools and opencv
RUN apt-get update && \
    apt-get install -y \
    libpng-tools \
    python3-opencv && \
    apt-get clean -y && \
    rm -rf /var/lib/apt/lists/*

# Switch back to non-root user
USER dockeruser

# Code
COPY code /var/azureml-app
ENV AZUREML_ENTRY_SCRIPT=score.py

# Model
COPY model /var/azureml-app/azureml-models
ENV AZUREML_MODEL_DIR=/var/azureml-app/azureml-models
```

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Verwendung eines Dockerfiles mit dem Python-SDK für Azure Machine Learning finden Sie in den folgenden Dokumenten:

* [Verwenden Ihres eigenen lokalen Dockerfiles](how-to-use-environments.md#use-your-own-dockerfile)
* [Verwenden eines vordefinierten Docker-Images und Erstellen eines benutzerdefinierten Basisimages](how-to-use-environments.md#use-a-prebuilt-docker-image)

Weitere Informationen zum Bereitstellen eines Modells finden Sie in [diesem Artikel](how-to-deploy-and-where.md).

Informationen zur Problembehandlung für Bereitstellungen von vordefinierten Docker-Images finden Sie in [diesem Artikel](how-to-troubleshoot-prebuilt-docker-image-inference.md).