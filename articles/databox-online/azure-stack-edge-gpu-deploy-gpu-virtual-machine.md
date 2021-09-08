---
title: Bereitstellen von GPU-VMs auf Ihrem Azure Stack Edge Pro-GPU-Gerät
description: Erfahren Sie, wie GPU-VMs auf einem Azure Stack Edge Pro-GPU-Gerät über das Azure-Portal oder anhand von Vorlagen erstellt und bereitgestellt werden.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/03/2021
ms.author: alkohli
ms.openlocfilehash: 958c87a78551555d2994c37e2b72c75cb989a834
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346793"
---
# <a name="deploy-gpu-vms-on-your-azure-stack-edge-pro-gpu-device"></a>Bereitstellen von GPU-VMs auf Ihrem Azure Stack Edge Pro-GPU-Gerät

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

In diesem Artikel wird beschrieben, wie Sie eine GPU-VM im Azure-Portal oder mithilfe der Azure Resource Manager-Vorlagen erstellen.

Verwenden Sie das Azure-Portal, um schnell eine einzelne GPU-VM bereitzustellen. Sie können die GPU-Erweiterung während oder nach der VM-Erstellung installieren. Sie können auch Azure Resource Manager-Vorlagen verwenden, um mehrere GPU-VMs effizient bereitzustellen und zu verwalten.

## <a name="create-gpu-vms"></a>Erstellen von GPU-VMs

Sie können eine GPU-VM über das Portal oder anhand von Azure Resource Manager-Vorlagen bereitstellen.

Eine Liste der unterstützten Betriebssysteme, Treiber und VM-Größen für GPU-VMs finden Sie unter [Was sind GPU-VMs?](azure-stack-edge-gpu-overview-gpu-virtual-machines.md) Überlegungen zur Bereitstellung finden Sie unter [GPU-VMs und Kubernetes](azure-stack-edge-gpu-overview-gpu-virtual-machines.md#gpu-vms-and-kubernetes).


> [!IMPORTANT]
> Wenn auf Ihrem Gerät Kubernetes ausgeführt werden soll, konfigurieren Sie Kubernetes erst nach der Bereitstellung Ihrer GPU-VMs. Wenn Sie Kubernetes zuerst konfigurieren, werden alle verfügbaren GPU-Ressourcen beansprucht, und die Erstellung der GPU-VM führt zu einem Fehler. Überlegungen zur Kubernetes-Bereitstellung auf 1-GPU- und 2-GPU-Geräten finden Sie unter [GPU-VMs und Kubernetes](azure-stack-edge-gpu-overview-gpu-virtual-machines.md#gpu-vms-and-kubernetes).

### <a name="portal"></a>[Portal](#tab/portal)

Führen Sie die folgenden Schritte aus, um auf Ihrem Gerät GPU-VMs über das Azure-Portal bereitzustellen:

1. Führen Sie zum Erstellen von GPU-VMs alle Schritte unter [Bereitstellen von VMs auf einem Azure Stack Edge Pro-GPU-Gerät über das Azure-Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md) mit den folgenden Konfigurationsanforderungen aus:

    - Wählen Sie auf der Registerkarte **Grundlagen** eine [VM-Größe aus der NCasT4-v3-Serie](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview) aus.

       ![Screenshot der Registerkarte „Grundlagen“ unter „Virtuellen Computer hinzufügen“ in Azure Stack Edge. Die Größenoption mit einer unterstützten VM-Größe für GPU-VMs ist hervorgehoben.](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/basics-vm-size-for-gpu.png)

    - Um die GPU-Erweiterung während der Bereitstellung zu installieren, wählen Sie auf der Registerkarte **Erweitert** die Option **Zu installierende Erweiterung auswählen** aus. Wählen Sie dann eine zu installierende GPU-Erweiterung aus. GPU-Erweiterungen sind nur für einen virtuellen Computer mit einer [VM-Größe aus der NCasT4-v3-Serie](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview) verfügbar.
        
        > [!NOTE]
        > Wenn Sie ein Red Hat-Image verwenden, müssen Sie die GPU-Erweiterung nach der VM-Bereitstellung installieren. Führen Sie die Schritte unter [Installieren der GPU-Erweiterung](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md) aus.
    
       ![Abbildung der Schritte zum Hinzufügen einer GPU-Erweiterung auf der Registerkarte „Erweitert“ unter „Virtuellen Computer hinzufügen“. Die Optionen zum Auswählen und Hinzufügen einer Erweiterung sind hervorgehoben.](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/add-extension-01.png)

       Auf der Registerkarte **Erweitert** wird die ausgewählte Erweiterung angezeigt.

       ![Screenshot der Registerkarte „Erweitert“ unter „Virtuellen Computer hinzufügen“. Eine installierte GPU-Erweiterung ist hervorgehoben.](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/add-extension-02.png)

1. Nachdem die GPU-VM erstellt wurde, können Sie sie in der Liste der VMs in Ihrer Azure Stack Edge-Ressource im Azure-Portal anzeigen.

    ![Screenshot der Ansicht „Virtuelle Computer“ in Azure Stack Edge mit hervorgehobener neu erstellter GPU-VM.](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/list-virtual-machines-01.png)

    Wählen Sie die VM aus, und zeigen Sie die Detailinformationen an. Stellen Sie sicher, dass die GPU-Erweiterung den Status **Erfolgreich** aufweist.

    ![Screenshot des Bereichs „Details“ für eine Azure Stack Edge-VM. Der Bereich „Installierte Erweiterungen“ mit einer installierten GPU-Erweiterung ist hervorgehoben.](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/vm-details-extension-installed.png)


### <a name="templates"></a>[Vorlagen](#tab/templates)

Führen Sie die folgenden Schritte aus, wenn Sie GPU-VMs auf Ihrem Gerät mithilfe von Azure Resource Manager-Vorlagen bereitstellen:

1. [Laden Sie die VM-Vorlagen und Parameterdateien](https://aka.ms/ase-vm-templates) auf Ihrem Clientcomputer herunter. Entpacken Sie die Vorlagen und Dateien in einem Verzeichnis, das Sie als Arbeitsverzeichnis verwenden.

1. Bevor Sie virtuelle Computer auf Ihrem Azure Stack Edge-Gerät bereitstellen können, müssen Sie den Client so konfigurieren, dass er über Azure Resource Manager über Azure PowerShell eine Verbindung mit dem Gerät herstellt. Eine ausführliche Anleitung finden Sie unter [Herstellen einer Verbindung mit Azure Resource Manager auf Ihrem Azure Stack Edge-Gerät](azure-stack-edge-gpu-connect-resource-manager.md).

1. Führen Sie zum Erstellen von GPU-VMs alle Schritte unter [Bereitstellen von VMs auf Ihrem Azure Stack Edge-Gerät über Vorlagen](azure-stack-edge-gpu-deploy-virtual-machine-templates.md) mit den folgenden Konfigurationsanforderungen aus: 
            
    - Verwenden Sie bei der Angabe der GPU-VM-Größen die NCasT4-v3-Serie in der Datei `CreateVM.parameters.json`, die für GPU-VMs unterstützt wird. Weitere Informationen finden Sie unter [Unterstützte VM-Größen für GPU-VMs](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview).

       ```json
           "vmSize": {
         "value": "Standard_NC4as_T4_v3"
       },
       ```

    Nachdem die GPU-VM erstellt wurde, können Sie sie in der Liste der VMs in Ihrer Azure Stack Edge-Ressource im Azure-Portal anzeigen.

    ![Screenshot der Ansicht „Virtuelle Computer“ in Azure Stack Edge. Eine neu erstellte GPU-VM ist hervorgehoben.](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/list-virtual-machines-01.png)

1. Wählen Sie die VM aus, und zeigen Sie die Detailinformationen an. Kopieren Sie die der VM zugewiesene IP-Adresse.

    ![Screenshot des Bereichs „Details“ für einen virtuellen Azure Stack Edge-Computer. Die IP-Adresse unter „Netzwerk“ ist hervorgehoben.](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/get-ip-of-virtual-machine.png)

Nachdem die VM erstellt wurde, können Sie die [GPU-Erweiterung mithilfe der Erweiterungsvorlage bereitstellen](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md?tabs=linux).

---

> [!NOTE]
> Wenn Sie die Softwareversion Ihres Geräts von 2012 auf eine höhere Version aktualisieren, müssen Sie die GPU-VMs manuell beenden.

## <a name="install-gpu-extension-after-deployment"></a>Installieren der GPU-Erweiterung nach der Bereitstellung

Um die GPU-Funktionen von Azure-VMs der N-Serie nutzen zu können, müssen Nvidia-GPU-Treiber installiert werden. Im Azure-Portal können Sie die GPU-Erweiterung während oder nach der VM-Bereitstellung installieren. Wenn Sie Vorlagen verwenden, installieren Sie die GPU-Erweiterung, nachdem Sie die VM erstellt haben.

---

### <a name="portal"></a>[Portal](#tab/portal)

Wenn Sie die GPU-Erweiterung beim Erstellen der VM nicht installiert haben, führen Sie die folgenden Schritte aus, um sie auf der bereitgestellten VM zu installieren:

1. Navigieren Sie zu dem virtuellen Computer, dem Sie die GPU-Erweiterung hinzufügen möchten.

    ![Screenshot der Ansicht „Virtuelle Computer“ für ein Azure Stack Edge-Gerät mit einem hervorgehobenen virtuellen Computer in der Liste der VMs.](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/add-extension-after-deployment-01.png)
  
1. Wählen Sie unter **Details** die Option **+ Erweiterung hinzufügen** aus. Wählen Sie dann eine zu installierende GPU-Erweiterung aus.

    GPU-Erweiterungen sind nur für einen virtuellen Computer mit einer [VM-Größe aus der NCasT4-v3-Serie](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview) verfügbar. Wenn Sie möchten, können Sie die [GPU-Erweiterung nach der Bereitstellung installieren](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#install-gpu-extension-after-deployment).

![Abbildung der zwei Schritte zur Verwendung der Schaltfläche „+ Erweiterung hinzufügen“ im Bereich „Details“ des virtuellen Computers, um einer VM auf einem Azure Stack Edge-Gerät eine GPU-Erweiterung hinzuzufügen.](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/add-extension-after-deployment-02.png)

> [!Note]
> Sie können eine GPU-Erweiterung nicht über das Portal entfernen. Verwenden Sie stattdessen das Cmdlet [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension?view=azurermps-6.13.0&preserve-view=true) in Azure PowerShell. Anweisungen finden Sie unter [Entfernen der GPU-Erweiterung](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md#remove-gpu-extension).

### <a name="templates"></a>[Vorlagen](#tab/templates)

Wenn Sie eine GPU-VM mithilfe von Vorlagen erstellen, installieren Sie die GPU-Erweiterung nach der Bereitstellung. Ausführliche Schritte zur Verwendung von Vorlagen zum Bereitstellen einer GPU-Erweiterung auf einem virtuellen Windows-Computer oder einem virtuellen Linux-Computer finden Sie unter [Installieren der GPU-Erweiterung](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md).

---

## <a name="next-steps"></a>Nächste Schritte

- [Problembehandlung bei der VM-Bereitstellung](azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning.md)
- [Behandeln von Problemen mit der GPU-Erweiterung](azure-stack-edge-gpu-troubleshoot-virtual-machine-gpu-extension-installation.md)
- [Überwachen der VM-Aktivität auf Ihrem Gerät](azure-stack-edge-gpu-monitor-virtual-machine-activity.md)
- [Überwachen von CPU und Arbeitsspeicher auf einer VM](azure-stack-edge-gpu-monitor-virtual-machine-metrics.md)
