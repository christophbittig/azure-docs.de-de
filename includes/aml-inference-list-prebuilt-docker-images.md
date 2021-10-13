---
title: Datei einfügen
description: Datei einfügen
services: machine-learning
author: shivanissambare
ms.service: machine-learning
ms.author: ssambare
ms.custom: include file
ms.topic: include
ms.date: 10/07/2021
ms.openlocfilehash: 32b18e9c98fe166d127e9fcad5f59794e8080751
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129712064"
---
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