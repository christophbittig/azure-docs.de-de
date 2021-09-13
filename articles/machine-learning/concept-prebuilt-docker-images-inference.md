---
title: Vordefinierte Docker-Images
titleSuffix: Azure Machine Learning
description: Vordefinierte Docker-Images für Rückschlüsse (Bewertung) in Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: ssambare
author: shivanissambare
ms.date: 05/25/2021
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy, docker, prebuilt
ms.openlocfilehash: 0548b309f84f46d7e1a8c904115167d59cc6f10e
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/21/2021
ms.locfileid: "112417395"
---
# <a name="prebuilt-docker-images-for-inference-preview"></a>Vordefinierte Docker-Images für Rückschlüsse (Vorschau)

Vordefinierte Docker-Containerimages für Rückschlüsse [(Vorschau)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) werden beim Bereitstellen eines Modells mit Azure Machine Learning verwendet.  Die Images sind mit gängigen Frameworks für maschinelles Lernen und Python-Paketen vordefiniert. Sie können die Pakete auch erweitern, um andere Pakete hinzuzufügen. Verwenden Sie dazu eine der folgenden Methoden:

* [Hinzufügen von Python-Paketen](how-to-prebuilt-docker-images-inference-python-extensibility.md)
* [Verwenden eines vordefinierten Rückschlussimages als Grundlage für ein neues Dockerfile.](how-to-extend-prebuilt-docker-image-inference.md) Mit dieser Methode können Sie sowohl **Python-Pakete als auch apt-Pakete installieren.**

## <a name="why-should-i-use-prebuilt-images"></a>Was spricht für die Verwendung vordefinierter Images?

* Verringern der Wartezeit bei der Modellbereitstellung
* Verbessern der Erfolgsrate bei der Modellbereitstellung
* Vermeiden unnötiger Imageerstellungen während der Modellbereitstellung
* Sicherstellen, dass nur erforderliche Abhängigkeiten und Zugriffsrechte im Image/Container bereitgestellt werden 

## <a name="list-of-prebuilt-docker-images-for-inference"></a>Liste der vordefinierten Docker-Images für Rückschlüsse 

* Alle Docker-Images werden als Nicht-Root-Benutzer ausgeführt.

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

## <a name="next-steps"></a>Nächste Schritte

* [Hinzufügen von Python-Paketen zu vordefinierten Images](how-to-prebuilt-docker-images-inference-python-extensibility.md)
* [Verwenden eines vordefinierten Pakets als Basis für ein neues Dockerfile](how-to-extend-prebuilt-docker-image-inference.md)