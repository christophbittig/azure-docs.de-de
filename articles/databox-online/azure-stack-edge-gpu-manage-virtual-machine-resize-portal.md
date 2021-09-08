---
title: Ändern der Größe von VMs auf Azure Stack Edge Pro-GPU-, Pro R- und Mini R-Geräten über das Azure-Portal
description: Erfahren Sie, wie Sie die Größe der virtuellen Computer (VMs), die auf Ihren Geräten vom Typ Azure Stack Edge Pro-GPU, Azure Stack Edge Pro R, and Azure Stack Edge Mini R ausgeführt werden, über das Azure-Portal ändern.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/08/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to resize VMs running on an Azure Stack Edge Pro device so that I can use it to run applications using Edge compute before sending it to Azure.
ms.openlocfilehash: 5e41edf0cf080d06a0829e31e7105a13a0035f93
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346518"
---
# <a name="use-the-azure-portal-to-resize-the-vms-on-your-azure-stack-edge-pro-gpu"></a>Verwenden des Azure-Portals zum Ändern der Größe von VMs auf Azure Stack Edge Pro-GPU-Geräten

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In diesem Artikel wird erläutert, wie Sie die Größe der auf Ihrem Azure Stack Edge Pro-GPU-Gerät bereitgestellten virtuellen Computer (VMs) ändern.

       
## <a name="about-vm-sizing"></a>Informationen zur VM-Größe

Die VM-Größe bestimmt den Umfang der Computeressourcen (CPU, GPU und Arbeitsspeicher), die für die VM zur Verfügung gestellt werden. Sie müssen VMs mit einer für die Anwendungsworkload geeigneten Größe erstellen. 

Auch wenn alle VMs auf derselben Hardware ausgeführt werden, gibt es bei den VM-Größen unterschiedliche Grenzwerte für den Datenträgerzugriff. Dies kann Ihnen helfen, den gesamten Datenträgerzugriff auf allen Ihren VMs zu verwalten. Wenn eine Workload zunimmt, können Sie auch die Größe einer vorhandenen VM ändern.

Weitere Informationen finden Sie unter [Unterstützte VM-Größen für Ihr Gerät](azure-stack-edge-gpu-virtual-machine-sizes.md).


## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie die Größe einer VM, die auf Ihrem Gerät ausgeführt wird, über das Azure-Portal ändern, stellen Sie Folgendes sicher:

1. Auf Ihrem Gerät ist mindestens eine VM bereitgestellt. Informationen zum Erstellen dieser VM finden Sie in den Anweisungen unter [Bereitstellen von VMs auf einem Azure Stack Edge Pro-GPU-Gerät über das Azure-Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).

1. Die VM sollte den Status **Beendet** aufweisen. Zum Beenden der VM navigieren Sie zu **Virtuelle Computer > Übersicht**, und wählen Sie die zu beendende VM aus. Wählen Sie auf der Seite „Übersicht“ die Option **Beenden** aus. Wenn Sie zur Bestätigung aufgefordert werden, wählen Sie **Ja** aus. Bevor Sie die Größe der VM ändern, müssen Sie die VM beenden.

    ![Screenshot des Bildschirms zum Beenden einer VM in der Übersicht für virtuelle Computer. Die Schaltfläche „Ja“ ist hervorgehoben.](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/stop-vm-2.png)


## <a name="resize-a-vm"></a>Ändern der Größe einer VM

Führen Sie die folgenden Schritte aus, um die Größe einer VM zu ändern, die auf Ihrem Gerät bereitgestellt wird. 

1. Wechseln Sie zu dem virtuellen Computer, den Sie beendet haben, und wählen Sie auf der Registerkarte **Details** für den virtuellen Computer die Option **VM-Größe (ändern)** aus.
    
    ![Screenshot der Registerkarte „Details“ für einen virtuellen Computer. Die Registerkarte „Details“ und die Option „VM-Größe“ sind hervorgehoben.](./media/azure-stack-edge-gpu-manage-virtual-machine-resize-portal/change-vm-size-1.png)

2. Wählen Sie auf dem Blatt **VM-Größe ändern** in der Befehlsleiste die **VM-Größe** und dann **Ändern** aus.

    ![Screenshot des Bildschirms „VM-Größe ändern“. Eine VM-Größe sowie die Schaltfläche „Ändern“ sind hervorgehoben.](./media/azure-stack-edge-gpu-manage-virtual-machine-resize-portal/change-vm-size-2.png)

3. Es wird eine Benachrichtigung angezeigt, dass die VM aktualisiert wird. Sobald die VM erfolgreich aktualisiert wurde, wird die Seite **Übersicht** aktualisiert, um die geänderte Größe der VM anzuzeigen.

    ![Screenshot der Seite „Übersicht“ für eine VM. Der Wert für „VM-Größe“ der geänderten VM ist hervorgehoben.](./media/azure-stack-edge-gpu-manage-virtual-machine-resize-portal/change-vm-size-3.png)


## <a name="next-steps"></a>Nächste Schritte

Informationen zum Bereitstellen von VMs auf Ihrem Azure Stack Edge Pro-Gerät finden Sie unter [Bereitstellen von VMs über das Azure-Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).
