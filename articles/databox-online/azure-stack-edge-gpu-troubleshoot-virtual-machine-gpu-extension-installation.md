---
title: Behandeln von Problemen mit der GPU-Erweiterung für GPU-VMs auf einem Azure Stack Edge Pro GPU-Gerät
description: Beschreibt die Behandlung von Problemen bei der Installation der GPU-Erweiterung für GPU-VMs auf einem Azure Stack Edge Pro GPU-Gerät.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/02/2021
ms.author: alkohli
ms.openlocfilehash: a2e6996bd2b86da470b364da1349248e26b2fa58
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122347187"
---
# <a name="troubleshoot-gpu-extension-issues-for-gpu-vms-on-azure-stack-edge-pro-gpu"></a>Behandeln von Problemen mit der GPU-Erweiterung für GPU-VMs auf einem Azure Stack Edge Pro GPU-Gerät

[!INCLUDE [applies-to-gpu-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Dieser Artikel enthält Anleitungen zum Beheben der häufigsten Probleme, die zu Fehlern bei der Installation der GPU-Erweiterung für GPU-VMs auf einem Azure Stack Edge Pro GPU-Gerät führen.

Die Installationsschritte finden Sie unter [Installieren der GPU-Erweiterung](./azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md?tabs=linux).

## <a name="vm-size-is-not-gpu-vm-size"></a>VM-Größe ist keine GPU-VM-Größe

**Fehlerbeschreibung:** Eine GPU-VM muss entweder die Größe Standard_NC4as_T4_v3 oder Standard_NC8as_T4_v3 aufweisen. Wenn eine andere VM-Größe verwendet wird, kann die GPU-Erweiterung nicht angefügt werden.

**Vorgeschlagene Lösung:** Erstellen Sie eine VM mit der Größe Standard_NC4as_T4_v3 oder Standard_NC8as_T4_v3. Weitere Informationen finden Sie unter [Unterstützte VM-Größen für GPU-VMs](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview). Informationen zum Angeben der Größe finden Sie unter [Erstellen von GPU-VMs](./azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#create-gpu-vms).


## <a name="image-os-is-not-supported"></a>Betriebssystem des Images wird nicht unterstützt

**Fehlerbeschreibung:** Die GPU-Erweiterung unterstützt das auf dem VM-Image installierte Betriebssystem nicht. 

**Vorgeschlagene Lösung:** Bereiten Sie ein neues VM-Image mit einem Betriebssystem vor, das von der GPU-Erweiterung unterstützt wird. 

* Eine Liste der unterstützten Betriebssysteme finden Sie unter [Unterstützte Betriebssysteme und GPU-Treiber für GPU-VMs](./azure-stack-edge-gpu-overview-gpu-virtual-machines.md#supported-os-and-gpu-drivers).

* Anforderungen an die Imagevorbereitung für eine GPU-VM finden Sie unter [Erstellen von GPU-VMs](./azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#create-gpu-vms).


## <a name="extension-parameter-is-incorrect"></a>Erweiterungsparameter ist falsch

**Fehlerbeschreibung:** Beim Bereitstellen der GPU-Erweiterung auf einer Linux-VM wurden falsche Erweiterungseinstellungen verwendet. 

**Vorgeschlagene Lösung:** Bearbeiten Sie die Parameterdatei, bevor Sie die GPU-Erweiterung bereitstellen. Weitere Informationen finden Sie unter [Installieren der GPU-Erweiterung](./azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md?tabs=linux).


## <a name="vm-extension-installation-failed-in-downloading-package"></a>Fehler während der Installation der VM-Erweiterung beim Herunterladen des Pakets

**Fehlerbeschreibung:** Beim Bereitstellen der Erweiterung ist während der Erweiterungsinstallation oder im Aktivierungsstatus ein Fehler aufgetreten.

1. Überprüfen Sie das Gastprotokoll auf den entsprechenden Fehler. Informationen zum Erfassen der Gastprotokolle finden Sie unter [Erfassen von VM-Gastprotokollen auf Azure Stack Edge Pro GPU-Geräten](azure-stack-edge-gpu-collect-virtual-machine-guest-logs.md).

   Auf einer Linux-VM:
   * Sehen Sie in `/var/log/waagent.log` oder `/var/log/azure/nvidia-vmext-status` nach.

   Auf einer Windows-VM:
   * Ermitteln Sie den Fehlerstatus in `C:\Packages\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverWindows\1.3.0.0\Status`.
   * Überprüfen Sie das vollständige Ausführungsprotokoll: `C:\WindowsAzure\Logs\WaAppAgent.txt`.

   Wenn bei der Installation während des Herunterladens des Pakets ein Fehler aufgetreten ist, deutet dies darauf hin, dass die VM nicht auf das öffentliche Netzwerk zugreifen konnte, um den Treiber herunterzuladen.

**Vorgeschlagene Lösung**:

1.  Aktivieren Sie Compute an einem Port, der mit dem Internet verbunden ist. Anleitungen dazu finden Sie unter [Erstellen von GPU-VMs](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#create-gpu-vms).

1.  Heben Sie die Zuordnung der VM auf, indem Sie die VM im Portal beenden. Zum Beenden der VM navigieren Sie zu **Virtuelle Computer** > **Übersicht**, und wählen Sie die VM aus. Anschließend wählen Sie auf der Seite mit den VM-Eigenschaften die Option **Beenden** aus.<!--Follow-up (formatting): Create an include file for stopping a VM. Use it here and in prerequisites for "Use the Azure portal to manage network interfaces on the VMs" (https://docs.microsoft.com/azure/databox-online/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal#prerequisites).-->
 
1.  Erstellen Sie einen neuen virtuellen Computer.


## <a name="vm-extension-failed-with-error-dpkg-is-usedyum-lock-is-used-linux-vm"></a>Fehler `dpkg is used/yum lock is used` bei der VM-Erweiterung (Linux-VM)

**Fehlerbeschreibung:** Beim Bereitstellen der GPU-Erweiterung auf einer Linux-VM ist ein Fehler aufgetreten, weil ein anderer Prozess `dpkg` verwendete oder ein anderer Prozess `yum lock` erstellt hat. 

**Vorgeschlagene Lösung:** Führen Sie zur Behebung des Problems die folgenden Schritte aus:

1.  Um herauszufinden, welcher Prozess die Sperre anwendet, suchen Sie im Protokoll „\var\log\azure\nvidia-vmext-status“ nach einem Fehler wie „dpkg wird von einem anderen Prozess verwendet“ oder „Eine andere App enthält `yum lock`“.

1. Warten Sie entweder, bis der Prozess abgeschlossen ist, oder beenden Sie den Prozess.

1.  [Installieren Sie die GPU-Erweiterung](./azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md?tabs=linux) erneut.

1.  Wenn bei der Erweiterungsbereitstellung erneut ein Fehler auftritt, erstellen Sie eine neue VM, und vergewissern Sie sich, dass die Sperre nicht vorhanden ist, bevor Sie die GPU-Erweiterung installieren.


## <a name="next-steps"></a>Nächste Schritte

[Erfassen von Gastprotokollen und Erstellen eines Supportpakets](azure-stack-edge-gpu-collect-virtual-machine-guest-logs.md)
