---
title: Zusammengestellte Umgebungen
titleSuffix: Azure Machine Learning
description: Erfahren Sie mehr über die für Azure Machine Learning zusammengestellten Umgebungen, eine Reihe von vorkonfigurierten Umgebungen, die dabei helfen, die Vorbereitungszeiten für Experimente und Bereitstellung zu verkürzen.
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.reviewer: luquinta
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.date: 07/08/2021
ms.openlocfilehash: bf67c2a23bf78960723efa37ea9bdc8f80c0c774
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123304714"
---
# <a name="azure-machine-learning-curated-environments"></a>Azure Machine Learning – zusammengestellte Umgebungen

In diesem Artikel werden die in Azure Machine Learning verfügbaren zusammengestellten Umgebungen aufgelistet. Zusammengestellte Umgebungen werden von Azure Machine Learning bereitgestellt und sind standardmäßig in Ihrem Arbeitsbereich verfügbar. Sie werden durch zwischengespeicherte Docker-Images unterstützt, in denen die neueste Version des Azure Machine Learning SDK verwendet wird. Damit werden die Kosten für die Vorbereitung der Ausführung reduziert und kürzere Bereitstellungszeiten ermöglicht. Verwenden Sie diese Umgebungen, um schnell mit verschiedenen Machine Learning-Frameworks zu beginnen.

> [!NOTE]
> Diese Liste wurde im August 2021 aktualisiert. Verwenden Sie das [Python SDK](how-to-use-environments.md), die [CLI](/cli/azure/ml/environment?view=azure-cli-latest&preserve-view=true#az_ml_environment_list) oder Azure Machine Learning [Studio](how-to-manage-environments-in-studio.md), um die neueste Liste der Umgebungen und ihre Abhängigkeiten abrufen. Weitere Informationen finden Sie im [Artikel zu Umgebungen](how-to-use-environments.md#use-a-curated-environment). Nach dem Release dieses neuen Satzes werden vorherige zusammengestellte Umgebungen ausgeblendet, können aber weiterhin verwendet werden. 

## <a name="pytorch"></a>PyTorch

**Name** - AzureML-pytorch-1.9-ubuntu18.04-py37-cuda11-gpu  
**Beschreibung**: Eine Umgebung für Deep Learning mit PyTorch, die das AzureML Python SDK und zusätzliche Python-Pakete enthält.  
**Dockerfile-Konfiguration**: Das folgende Dockerfile kann für Ihre persönlichen Workflows angepasst werden:

```dockerfile
FROM mcr.microsoft.com/azureml/openmpi4.1.0-cuda11.1-cudnn8-ubuntu18.04:20210806.v1

ENV AZUREML_CONDA_ENVIRONMENT_PATH /azureml-envs/pytorch-1.9

# Create conda environment
RUN conda create -p $AZUREML_CONDA_ENVIRONMENT_PATH \
    python=3.7 \
    pip=21.2.1 \
    pytorch=1.9.0 \
    torchvision=0.10.0 \
    torchaudio=0.9.0 \
    cudatoolkit=11.1.1 \
    nvidia-apex=0.1.0 \
    -c anaconda -c pytorch -c conda-forge

# Prepend path to AzureML conda environment
ENV PATH $AZUREML_CONDA_ENVIRONMENT_PATH/bin:$PATH

# Install pip dependencies
RUN HOROVOD_WITH_PYTORCH=1 \
    pip install 'matplotlib>=3.3,<3.4' \
                'psutil>=5.8,<5.9' \
                'tqdm>=4.59,<4.60' \
                'pandas>=1.1,<1.2' \
                'scipy>=1.5,<1.6' \
                'numpy>=1.10,<1.20' \
                'azureml-core==1.33.0' \
                'azureml-defaults==1.33.0' \
                'azureml-mlflow==1.33.0' \
                'azureml-telemetry==1.33.0' \
                'tensorboard==2.4.0' \
                'tensorflow-gpu==2.4.1' \
                'onnxruntime-gpu>=1.7,<1.8' \
                'horovod[pytorch]==0.21.3' \
                'future==0.17.1'

# This is needed for mpi to locate libpython
ENV LD_LIBRARY_PATH $AZUREML_CONDA_ENVIRONMENT_PATH/lib:$LD_LIBRARY_PATH
```

## <a name="lightgbm"></a>LightGBM

**Name**: AzureML-lightgbm-3.2-ubuntu18.04-py37-cpu  
**Beschreibung**: Eine Umgebung für maschinelles Lernen mit Scikit-learn, LightGBM, XGBoost, Dask einschließlich AzureML Python SDK und zusätzlichen Paketen.  
**Dockerfile-Konfiguration**: Das folgende Dockerfile kann für Ihre persönlichen Workflows angepasst werden:

```dockerfile
FROM mcr.microsoft.com/azureml/openmpi3.1.2-ubuntu18.04:20210806.v1

ENV AZUREML_CONDA_ENVIRONMENT_PATH /azureml-envs/lightgbm

# Create conda environment
RUN conda create -p $AZUREML_CONDA_ENVIRONMENT_PATH \
    python=3.7 pip=20.2.4

# Prepend path to AzureML conda environment
ENV PATH $AZUREML_CONDA_ENVIRONMENT_PATH/bin:$PATH

# Install pip dependencies
RUN HOROVOD_WITH_TENSORFLOW=1 \
    pip install 'matplotlib>=3.3,<3.4' \
                'psutil>=5.8,<5.9' \
                'tqdm>=4.59,<4.60' \
                'pandas>=1.1,<1.2' \
                'numpy>=1.10,<1.20' \
                'scipy~=1.5.0' \
                'scikit-learn~=0.24.1' \
                'xgboost~=1.4.0' \
                'lightgbm~=3.2.0' \
                'dask~=2021.6.0' \
                'distributed~=2021.6.0' \
                'dask-ml~=1.9.0' \
                'adlfs~=0.7.0' \
                'azureml-core==1.33.0' \
                'azureml-defaults==1.33.0' \
                'azureml-mlflow==1.33.0' \
                'azureml-telemetry==1.33.0'

# This is needed for mpi to locate libpython
ENV LD_LIBRARY_PATH $AZUREML_CONDA_ENVIRONMENT_PATH/lib:$LD_LIBRARY_PATH
```

## <a name="sklearn"></a>Sklearn
**Name**: AzureML-sklearn-0.24-ubuntu18.04-py37-cuda11-gpu  
**Beschreibung**: Eine Umgebung für Aufgaben wie Regression, Clustering und Klassifizierung mit Scikit-learn. Enthält das AzureML Python SDK und zusätzliche Python-Pakete.  
**Dockerfile-Konfiguration**: Das folgende Dockerfile kann für Ihre persönlichen Workflows angepasst werden:

```dockerfile
FROM mcr.microsoft.com/azureml/openmpi3.1.2-ubuntu18.04:20210806.v1

ENV AZUREML_CONDA_ENVIRONMENT_PATH /azureml-envs/sklearn-0.24.1

# Create conda environment
RUN conda create -p $AZUREML_CONDA_ENVIRONMENT_PATH \
    python=3.7 pip=20.2.4

# Prepend path to AzureML conda environment
ENV PATH $AZUREML_CONDA_ENVIRONMENT_PATH/bin:$PATH

# Install pip dependencies
RUN pip install 'matplotlib>=3.3,<3.4' \
                'psutil>=5.8,<5.9' \
                'tqdm>=4.59,<4.60' \
                'pandas>=1.1,<1.2' \
                'scipy>=1.5,<1.6' \
                'numpy>=1.10,<1.20' \
                'azureml-core==1.33.0' \
                'azureml-defaults==1.33.0' \
                'azureml-mlflow==1.33.0' \
                'azureml-telemetry==1.33.0' \
                'scikit-learn==0.24.1'

# This is needed for mpi to locate libpython
ENV LD_LIBRARY_PATH $AZUREML_CONDA_ENVIRONMENT_PATH/lib:$LD_LIBRARY_PATH
```

## <a name="tensorflow"></a>TensorFlow

**Name**: AzureML-tensorflow-2.4-ubuntu18.04-py37-cuda11-gpu  
**Beschreibung**: Eine Umgebung für Deep Learning mit Tensorflow, die das AzureML Python SDK und zusätzliche Python-Pakete enthält.  
**Dockerfile-Konfiguration**: Das folgende Dockerfile kann für Ihre persönlichen Workflows angepasst werden:

```dockerfile
FROM mcr.microsoft.com/azureml/openmpi4.1.0-cuda11.0.3-cudnn8-ubuntu18.04:20210806.v1

ENV AZUREML_CONDA_ENVIRONMENT_PATH /azureml-envs/tensorflow-2.4

# Create conda environment
RUN conda create -p $AZUREML_CONDA_ENVIRONMENT_PATH \
    python=3.7 pip=20.2.4

# Prepend path to AzureML conda environment
ENV PATH $AZUREML_CONDA_ENVIRONMENT_PATH/bin:$PATH

# Install pip dependencies
RUN HOROVOD_WITH_TENSORFLOW=1 \
    pip install 'matplotlib>=3.3,<3.4' \
                'psutil>=5.8,<5.9' \
                'tqdm>=4.59,<4.60' \
                'pandas>=1.1,<1.2' \
                'scipy>=1.5,<1.6' \
                'numpy>=1.10,<1.20' \
                'azureml-core==1.33.0' \
                'azureml-defaults==1.33.0' \
                'azureml-mlflow==1.33.0' \
                'azureml-telemetry==1.33.0' \
                'tensorboard==2.4.0' \
                'tensorflow-gpu==2.4.1' \
                'tensorflow-datasets==4.3.0' \
                'onnxruntime-gpu>=1.7,<1.8' \
                'horovod[tensorflow-gpu]==0.21.3'

# This is needed for mpi to locate libpython
ENV LD_LIBRARY_PATH $AZUREML_CONDA_ENVIRONMENT_PATH/lib:$LD_LIBRARY_PATH
```

## <a name="automated-ml-automl"></a>Automatisiertes ML (AutoML)

Azure ML-Pipelinetrainingsworkflows mit Verwendung von AutoML wählen basierend auf dem Computetyp und abhängig davon, ob DNN aktiviert ist, automatisch eine zusammengestellte Umgebung aus. AutoML stellt die folgenden zusammengestellten Umgebungen bereit:

| Name | Computetyp | DNN aktiviert |
| --- | --- | --- |
|AzureML-AutoML | CPU | Nein |
|AzureML-AutoML-DNN | CPU | Ja |
| AzureML-AutoML-GPU | GPU | Nein |
| AzureML-AutoML-DNN-GPU | GPU | Ja |

Weitere Informationen zu AutoML- und Azure ML-Pipelines finden Sie unter [Verwenden von automatisiertem ML in einer Azure Machine Learning-Pipeline in Python](how-to-use-automlstep-in-pipelines.md).

## <a name="inference-only-curated-environments-and-prebuilt-docker-images"></a>Rückschluss nur für zusammengestellte Umgebungen und vordefinierte Docker-Images

Weitere Informationen zum Rückschluss von ausschließlich zusammengestellten Umgebungen und zum MCR-Pfad für vordefinierte Docker-Images finden Sie unter [Vordefinierte Docker-Images für Rückschlüsse](concept-prebuilt-docker-images-inference.md#list-of-prebuilt-docker-images-for-inference).
