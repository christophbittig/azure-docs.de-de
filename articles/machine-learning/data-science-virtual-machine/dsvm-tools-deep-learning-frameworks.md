---
title: Deep Learning- und KI-Frameworks
titleSuffix: Azure Data Science Virtual Machine
description: Verfügbare Deep Learning-Frameworks und -Tools unter Azure Data Science Virtual Machine
keywords: Data Science-Tools, virtuelle Computer für Data Science, Tools für Data Science, Linux Data Science
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
author: michalmar
ms.author: mimarusa
ms.topic: conceptual
ms.date: 07/27/2021
ms.openlocfilehash: a757151d17456c7ee9646bc0730a51f34088b255
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2021
ms.locfileid: "114729187"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>Deep Learning- und KI-Frameworks für Azure Data Science VM
Die in DSVM verfügbaren Deep Learning-Frameworks sind nachstehend aufgelistet.


## <a name="cuda-cudnn-nvidia-driver"></a>[CUDA-, cuDNN-, NVIDIA-Treiber](https://developer.nvidia.com/cuda-toolkit)

| Category | Wert |
|--|--|
| Unterstützte Versionen | 11 |
| Unterstützte DSVM-Editionen | Windows Server 2019<br>Ubuntu 18.04 |
| Konfiguration/Installation auf der DSVM | _nvidia-smi_ steht unter dem Systempfad zur Verfügung. |
| Zur Ausführung | Öffnen Sie eine Eingabeaufforderung (unter Windows) oder ein Terminal (unter Linux), und führen Sie dann _nvidia-smi_ aus. |
## <a name="horovod"></a>[Horovod](https://github.com/uber/horovod)

| Category | Wert |
| ------------- | ------------- |
| Unterstützte Versionen | 0.21.3|
| Unterstützte DSVM-Editionen      | Ubuntu 18.04 |
| Konfiguration/Installation auf der DSVM  | Horovod wird in Python 3.5 installiert. |
| Zur Ausführung      | Aktivieren Sie die richtige Umgebung am Terminal, und führen Sie dann Python aus. |


## <a name="nvidia-system-management-interface-nvidia-smi"></a>[NVidia System Management Interface (nvidia-smi)](https://developer.nvidia.com/nvidia-system-management-interface)

| Category | Wert |
|--|--|
| Unterstützte Versionen |  |
| Unterstützte DSVM-Editionen | Windows Server 2019<br>Ubuntu 18.04 |
| What is it for? (D2 und D4: Wozu dient es?). | NVIDIA-Tool zum Abfragen der GPU-Aktivität |
| Konfiguration/Installation auf der DSVM | `nvidia-smi` befindet sich im Systempfad. |
| Zur Ausführung | Öffnen Sie auf einem virtuellen Computer **mit GPUs** eine Eingabeaufforderung (unter Windows) oder ein Terminal (unter Linux), und führen Sie dann `nvidia-smi` aus. |

## <a name="pytorch"></a>[PyTorch](https://pytorch.org/)

| Category | Wert |
|--|--|
| Unterstützte Versionen | 1.9.0 (Ubuntu 18.04, Windows 2019) |
| Unterstützte DSVM-Editionen | Windows Server 2019<br>Ubuntu 18.04 |
| Konfiguration/Installation auf der DSVM | Installation in Python, Conda-Umgebungen „py38_default“, „py38_pytorch“ |
| Zur Ausführung | Terminal: Aktivieren Sie die richtige Umgebung, und führen Sie dann Python aus.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine): Stellen Sie eine Verbindung her, und öffnen Sie dann das PyTorch-Verzeichnis für Beispiele. |

## <a name="tensorflow"></a>[TensorFlow](https://www.tensorflow.org/)

| Category | Wert |
|--|--|
| Unterstützte Versionen | 2.5 |
| Unterstützte DSVM-Editionen | Windows Server 2019<br>Ubuntu 18.04 |
| Konfiguration/Installation auf der DSVM | Installation in Python, Conda-Umgebungen „py38_default“, „py38_tensorflow“ |
| Zur Ausführung | Terminal: Aktivieren Sie die richtige Umgebung, und führen Sie dann Python aus. <br/> * Jupyter: Stellen Sie eine Verbindung mit [Jupyter](provision-vm.md) oder [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine) her, und öffnen Sie dann das TensorFlow-Verzeichnis für Beispiele. |
