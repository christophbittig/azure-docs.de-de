---
title: Übersicht über GPU-VMs auf Ihrem Azure Stack Edge Pro-GPU-Gerät
description: Beschreibt die Verwendung virtueller Computer, die für GPU-beschleunigte Workloads auf Azure Stack Edge Pro mit GPU optimiert sind.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: alkohli
ms.openlocfilehash: d8660ace369fe0da0da384dceae6bff35594df74
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346788"
---
# <a name="gpu-virtual-machines-for-azure-stack-edge-pro-gpu-devices"></a>Virtuelle GPU-Computer für Azure Stack Edge Pro GPU-Geräte

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

GPU-beschleunigte Workloads auf einem Azure Stack Edge Pro GPU-Gerät erfordern einen virtuellen GPU-Computer. Dieser Artikel bietet eine Übersicht über GPU-VMs, einschließlich unterstützter Betriebssysteme, GPU-Treiber und VM-Größen. Bereitstellungsoptionen für GPU-VMs, die mit Kubernetes-Clustern verwendet werden, werden ebenfalls erläutert.

## <a name="about-gpu-vms"></a>Informationen zu GPU-VMs

Ihre Azure Stack Edge-Geräte können mit einer oder zwei Tesla T4-GPUs von Nvidia ausgestattet sein. Verwenden Sie zur Bereitstellung von VM-Workloads mit GPU-Beschleunigung auf diesen Geräten die GPU-optimierten VM-Größen. Beispielsweise sollte für das Bereitstellen von Rückschlussworkloads mit T4-GPUs die Serie NC T4 v3 verwendet werden. Weitere Informationen finden Sie unter [VMs der NC T4 v3-Serie](../virtual-machines/nct4-v3-series.md).

Um die GPU-Funktionen von Azure-VMs der N-Serie nutzen zu können, müssen Nvidia-GPU-Treiber installiert werden. Mit der Nvidia-GPU-Treibererweiterung werden die entsprechende Nvidia-CUDA- oder -GRID-Treiber installiert. Sie können die [GPU-Erweiterungen mithilfe von Vorlagen oder über das Azure-Portal installieren](#gpu-vm-deployment).

Sie können nach der VM-Bereitstellung die [Erweiterung mithilfe von Azure Resource Manager-Vorlagen installieren und verwalten](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md). Im Azure-Portal können Sie die GPU-Erweiterung während oder nach der Bereitstellung einer VM installieren. Anweisungen dazu finden Sie unter [Bereitstellen von GPU-VMs auf Ihrem Azure Stack Edge-Gerät](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md).

Wenn auf Ihrem Gerät ein Kubernetes-Cluster konfiguriert werden soll, lesen Sie vor der Bereitstellung von GPU-VMs die [Überlegungen zur Bereitstellung für Kubernetes-Cluster](#gpu-vms-and-kubernetes).

## <a name="supported-os-and-gpu-drivers"></a>Unterstützte Betriebssysteme und GPU-Treiber 

Die Nvidia-GPU-Treibererweiterungen für Windows und Linux unterstützen die folgenden Betriebssystemversionen.

### <a name="supported-os-for-gpu-extension-for-windows"></a>Von der GPU-Erweiterung für Windows unterstützte Betriebssysteme

Diese Erweiterung unterstützt die folgenden Betriebssysteme. Andere Versionen funktionieren zwar möglicherweise ebenfalls, wurden jedoch nicht intern auf GPU-VMs getestet, die auf Azure Stack Edge-Geräten ausgeführt werden.

| Distribution | Version |
|---|---|
| Windows Server 2019 | Core |
| Windows Server 2016 | Core |

### <a name="supported-os-for-gpu-extension-for-linux"></a>Von der GPU-Erweiterung für Linux unterstützte Betriebssysteme

Diese Erweiterung unterstützt die folgenden Betriebssystem-Distributionen, abhängig von der Treiberunterstützung für bestimmte Betriebssystemversionen. Andere Versionen funktionieren zwar möglicherweise ebenfalls, wurden jedoch nicht intern auf GPU-VMs getestet, die auf Azure Stack Edge-Geräten ausgeführt werden.

| Distribution | Version |
|---|---|
| Ubuntu | 18.04 LTS |
| Red Hat Enterprise Linux | 7.4 |

## <a name="gpu-vm-deployment"></a>GPU-VM-Bereitstellung

Sie können eine GPU-VM über das Azure-Portal oder anhand von Azure Resource Manager-Vorlagen bereitstellen. Die GPU-Erweiterung wird nach dem Erstellen der VM installiert.<!--Wording still needs work!-->

- **Portal:** Im Azure-Portal können Sie die [GPU-Erweiterung schnell beim Erstellen einer VM](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#create-gpu-vms) oder [nach der VM-Bereitstellung installieren]().<!--Can they remove the GPU extension. Tomorrow, create a new GPU VM to test.-->

- **Vorlagen:** Mithilfe von Azure Resource Manager-Vorlagen [erstellen Sie eine VM](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#install-gpu-extension-after-deployment) und [installieren dann die GPU-Erweiterung](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md).


## <a name="gpu-vms-and-kubernetes"></a>GPU-VMs und Kubernetes

Beachten Sie vor der Bereitstellung von GPU-VMs auf Ihrem Gerät die folgenden Überlegungen, wenn darauf Kubernetes konfiguriert ist.

#### <a name="for-1-gpu-device"></a>Für Geräte mit einer GPU: 

- **Sie erstellen zuerst eine GPU-VM auf Ihrem Gerät und konfigurieren danach Kubernetes:** In diesem Szenario werden sowohl die GPU-VM-Erstellung als auch die Kubernetes-Konfiguration erfolgreich abgeschlossen. Kubernetes hat in diesem Fall keinen Zugriff auf die GPU.

- **Sie konfigurieren zuerst Kubernetes auf Ihrem Gerät und erstellen danach eine GPU-VM:** In diesem Szenario beansprucht Kubernetes die GPU auf Ihrem Gerät, sodass die VM-Erstellung zu einem Fehler führt, da keine GPU-Ressourcen verfügbar sind.

#### <a name="for-2-gpu-device"></a>Für Geräte mit zwei GPUs:

- **Sie erstellen zuerst eine GPU-VM auf Ihrem Gerät und konfigurieren danach Kubernetes:** In diesem Szenario beansprucht die erstellte GPU-VM eine GPU auf Ihrem Gerät, und die Kubernetes-Konfiguration ist ebenfalls erfolgreich und beansprucht die verbleibende GPU. 

- **Sie erstellen zuerst zwei GPU-VMs auf Ihrem Gerät und konfigurieren danach Kubernetes:** In diesem Szenario beanspruchen die beiden GPU-VMs die beiden GPUs auf dem Gerät. Kubernetes wird zwar erfolgreich konfiguriert, kann aber keine GPUs nutzen. 

- **Sie konfigurieren zuerst Kubernetes auf Ihrem Gerät und erstellen danach eine GPU-VM:** In diesem Szenario beansprucht Kubernetes beide GPUs auf Ihrem Gerät, sodass die VM-Erstellung zu einem Fehler führt, da keine GPU-Ressourcen mehr verfügbar sind.

<!--Li indicated that this is fixed. If you have GPU VMs running on your device and Kubernetes is also configured, then anytime the VM is deallocated (when you stop or remove a VM using Stop-AzureRmVM or Remove-AzureRmVM), there is a risk that the Kubernetes cluster will claim all the GPUs available on the device. In such an instance, you will not be able to restart the GPU VMs deployed on your device or create GPU VMs. -->

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie, wie Sie [GPU-VMs bereitstellen](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md).
- Erfahren Sie, wie Sie die [GPU-Erweiterung auf den GPU-VMs installieren](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md), die auf Ihrem Gerät ausgeführt werden.
