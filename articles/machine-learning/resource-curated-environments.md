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
ms.openlocfilehash: b66f76d80d3dad6b24eefa6fb7bbd88cfce082e0
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129360017"
---
# <a name="azure-machine-learning-curated-environments"></a>Azure Machine Learning – zusammengestellte Umgebungen

In diesem Artikel werden die in Azure Machine Learning verfügbaren zusammengestellten Umgebungen mit den neuesten Frameworkversionen aufgelistet. Zusammengestellte Umgebungen werden von Azure Machine Learning bereitgestellt und sind standardmäßig in Ihrem Arbeitsbereich verfügbar. Sie werden durch zwischengespeicherte Docker-Images unterstützt, in denen die neueste Version des Azure Machine Learning SDK verwendet wird. Damit werden die Kosten für die Vorbereitung der Ausführung reduziert und kürzere Bereitstellungszeiten ermöglicht. Verwenden Sie diese Umgebungen, um schnell mit verschiedenen Machine Learning-Frameworks zu beginnen.

> [!NOTE]
> Verwenden Sie das [Python SDK](how-to-use-environments.md), die [CLI](/cli/azure/ml/environment?view=azure-cli-latest&preserve-view=true#az_ml_environment_list) oder Azure Machine Learning [Studio](how-to-manage-environments-in-studio.md), um die komplette Liste an Umgebungen und deren Abhängigkeiten zu erhalten. Weitere Informationen finden Sie im [Artikel zu Umgebungen](how-to-use-environments.md#use-a-curated-environment). 

## <a name="training-curated-environments"></a>Trainieren von zusammengestellten Umgebungen

### <a name="pytorch"></a>PyTorch

**Name**: AzureML-pytorch-1.9-ubuntu18.04-py37-cuda11-gpu  
**Beschreibung**: Eine Umgebung für Deep Learning mit PyTorch, die das AzureML Python SDK und zusätzliche Python-Pakete enthält.  

Das folgende Dockerfile kann für Ihre persönlichen Workflows angepasst werden:

```dockerfile
FROM mcr.microsoft.com/azureml/openmpi4.1.0-cuda11.1-cudnn8-ubuntu18.04:20210922.v1

ENV AZUREML_CONDA_ENVIRONMENT_PATH /azureml-envs/pytorch-1.9

# Create conda environment
RUN conda create -p $AZUREML_CONDA_ENVIRONMENT_PATH \
    python=3.7 \
    pip=20.2.4 \
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
                'ipykernel~=6.0' \
                'azureml-core==1.34.0' \
                'azureml-defaults==1.34.0' \
                'azureml-mlflow==1.34.0' \
                'azureml-telemetry==1.34.0' \
                'tensorboard==2.4.0' \
                'tensorflow-gpu==2.4.1' \
                'onnxruntime-gpu>=1.7,<1.8' \
                'horovod[pytorch]==0.21.3' \
                'future==0.17.1' \
                'torch-tb-profiler==0.2.1'


# This is needed for mpi to locate libpython
ENV LD_LIBRARY_PATH $AZUREML_CONDA_ENVIRONMENT_PATH/lib:$LD_LIBRARY_PATH
```

Andere verfügbare PyTorch-Umgebungen:
* AzureML-pytorch-1.8-ubuntu18.04-py37-cuda11-gpu
* AzureML-pytorch-1.7-ubuntu18.04-py37-cuda11-gpu

### <a name="lightgbm"></a>LightGBM

**Name**: AzureML-lightgbm-3.2-ubuntu18.04-py37-cpu  
**Beschreibung**: Eine Umgebung für maschinelles Lernen mit Scikit-learn, LightGBM, XGBoost, Dask einschließlich dem AzureML Python SDK und zusätzlichen Paketen.  

Das folgende Dockerfile kann für Ihre persönlichen Workflows angepasst werden:

```dockerfile
FROM mcr.microsoft.com/azureml/openmpi3.1.2-ubuntu18.04:20210922.v1

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
                'ipykernel~=6.0' \
                'azureml-core==1.34.0' \
                'azureml-defaults==1.34.0' \
                'azureml-mlflow==1.34.0' \
                'azureml-telemetry==1.34.0'

# This is needed for mpi to locate libpython
ENV LD_LIBRARY_PATH $AZUREML_CONDA_ENVIRONMENT_PATH/lib:$LD_LIBRARY_PATH
```

### <a name="sklearn"></a>Sklearn
**Name**: AzureML-sklearn-0.24-ubuntu18.04-py37-cuda11-gpu  
**Beschreibung**: Eine Umgebung für Aufgaben wie Regression, Clustering und Klassifizierung mit Scikit-learn. Enthält das AzureML Python SDK und zusätzliche Python-Pakete.  

Das folgende Dockerfile kann für Ihre persönlichen Workflows angepasst werden:

```dockerfile
FROM mcr.microsoft.com/azureml/openmpi3.1.2-ubuntu18.04:20210922.v1

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
                'ipykernel~=6.0' \
                'azureml-core==1.34.0' \
                'azureml-defaults==1.34.0' \
                'azureml-mlflow==1.34.0' \
                'azureml-telemetry==1.34.0' \
                'scikit-learn==0.24.1'

# This is needed for mpi to locate libpython
ENV LD_LIBRARY_PATH $AZUREML_CONDA_ENVIRONMENT_PATH/lib:$LD_LIBRARY_PATH
```

### <a name="tensorflow"></a>TensorFlow

**Name**: AzureML-tensorflow-2.4-ubuntu18.04-py37-cuda11-gpu  
**Beschreibung**: Eine Umgebung für Deep Learning mit Tensorflow, die das AzureML Python SDK und zusätzliche Python-Pakete enthält.  

Das folgende Dockerfile kann für Ihre persönlichen Workflows angepasst werden:

```dockerfile
FROM mcr.microsoft.com/azureml/openmpi4.1.0-cuda11.0.3-cudnn8-ubuntu18.04:20210922.v1

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
                'ipykernel~=6.0' \
                'azureml-core==1.34.0' \
                'azureml-defaults==1.34.0' \
                'azureml-mlflow==1.34.0' \
                'azureml-telemetry==1.34.0' \
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

* Alle Docker-Images werden als Nicht-Root-Benutzer ausgeführt.
* Es wird empfohlen, für Docker-Images das Tag `latest` zu verwenden. Vordefinierte Docker-Images für Rückschlüsse werden in der Microsoft-Containerregistrierung (MCR) veröffentlicht. Um eine Liste der verfügbaren Tags abzufragen, befolgen Sie die [Anweisungen im entsprechenden GitHub-Repository](https://github.com/microsoft/ContainerRegistry#browsing-mcr-content).

### <a name="tensorflow"></a>TensorFlow

Frameworkversion | CPU/GPU | Vorinstallierte Pakete | MCR-Pfad | Zusammengestellte Umgebung
 --- | --- | --- | --- | --- |
 1.15 | CPU | pandas==0.25.1 </br> numpy=1.20.1 | `mcr.microsoft.com/azureml/tensorflow-1.15-ubuntu18.04-py37-cpu-inference:latest`  | AzureML-tensorflow-1.15-ubuntu18.04-py37-cpu-inference | 
2.4 | CPU | numpy>=1.16.0 </br> pandas~=1.1.x | `mcr.microsoft.com/azureml/tensorflow-2.4-ubuntu18.04-py37-cpu-inference:latest` | AzureML-tensorflow-2.4-ubuntu18.04-py37-cpu-inference |
2.4 | GPU | numpy >= 1.16.0 </br> pandas~=1.1.x </br> CUDA==11.0.3 </br> CuDNN==8.0.5.39 | `mcr.microsoft.com/azureml/tensorflow-2.4-ubuntu18.04-py37-cuda11.0.3-gpu-inference:latest` | AzureML-tensorflow-2.4-ubuntu18.04-py37-cuda11.0.3-gpu-inference |

### <a name="pytorch"></a>PyTorch

Frameworkversion | CPU/GPU | Vorinstallierte Pakete | MCR-Pfad | Zusammengestellte Umgebung
 --- | --- | --- | --- | --- |
 1.6 | CPU | numpy==1.20.1 </br> pandas==0.25.1 | `mcr.microsoft.com/azureml/pytorch-1.6-ubuntu18.04-py37-cpu-inference:latest` | AzureML-pytorch-1.6-ubuntu18.04-py37-cpu-inference |
1.7 | CPU | numpy>=1.16.0 </br> pandas~=1.1.x | `mcr.microsoft.com/azureml/pytorch-1.7-ubuntu18.04-py37-cpu-inference:latest` | AzureML-pytorch-1.7-ubuntu18.04-py37-cpu-inference |

### <a name="scikit-learn"></a>SciKit-Learn

Frameworkversion | CPU/GPU | Vorinstallierte Pakete | MCR-Pfad | Zusammengestellte Umgebung
 --- | --- | --- | --- | --- |
0.24.1  | CPU | scikit-learn==0.24.1 </br> numpy>=1.16.0 </br> pandas~=1.1.x | `mcr.microsoft.com/azureml/sklearn-0.24.1-ubuntu18.04-py37-cpu-inference:latest` | AzureML-sklearn-0.24.1-ubuntu18.04-py37-cpu-inference |

### <a name="onnx-runtime"></a>ONNX-Runtime

Frameworkversion | CPU/GPU | Vorinstallierte Pakete | MCR-Pfad | Zusammengestellte Umgebung
 --- | --- | --- | --- | --- |
1.6 | CPU | numpy>=1.16.0 </br> pandas~=1.1.x | `mcr.microsoft.com/azureml/onnxruntime-1.6-ubuntu18.04-py37-cpu-inference:latest` |AzureML-onnxruntime-1.6-ubuntu18.04-py37-cpu-inference |

### <a name="xgboost"></a>XGBoost

Frameworkversion | CPU/GPU | Vorinstallierte Pakete | MCR-Pfad | Zusammengestellte Umgebung
 --- | --- | --- | --- | --- |
0.9 | CPU | scikit-learn==0.23.2 </br> numpy==1.20.1 </br> pandas==0.25.1 | `mcr.microsoft.com/azureml/xgboost-0.9-ubuntu18.04-py37-cpu-inference:latest` | AzureML-xgboost-0.9-ubuntu18.04-py37-cpu-inference | 

### <a name="no-framework"></a>Kein Framework

Frameworkversion | CPU/GPU | Vorinstallierte Pakete | MCR-Pfad | Zusammengestellte Umgebung
 --- | --- | --- | --- | --- |
Nicht verfügbar | CPU | Nicht verfügbar | `mcr.microsoft.com/azureml/minimal-ubuntu18.04-py37-cpu-inference:latest` | AzureML-minimal-ubuntu18.04-py37-cpu-inference  |


## <a name="security"></a>Sicherheit
Versionsupdates für unterstützte Umgebungen werden alle zwei Wochen veröffentlicht, um Sicherheitsrisiken zu entfernen, die nicht älter als 30 Tage sind. 

