---
title: Erfassen von VM-Gastprotokollen auf Azure Stack Edge Pro GPU
description: So erstellen Sie ein Supportpaket mit Gastprotokollen für VMs auf einem Azure Stack Edge Pro GPU-Gerät.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/30/2021
ms.author: alkohli
ms.openlocfilehash: 1c25ea8c35b81169119b0f10025b36319d4dc2c9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346977"
---
# <a name="collect-vm-guest-logs-on-an-azure-stack-edge-pro-gpu-device"></a>Erfassen von VM-Gastprotokollen auf Azure Stack Edge Pro GPU-Geräten

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Um VM-Bereitstellungsfehler auf Ihrem Azure Stack Edge Pro GPU-Gerät zu diagnostizieren, müssen Sie die Gastprotokolle für die fehlerhafte VM auswerten. In diesem Artikel wird beschrieben, wie Gastprotokolle für die VMs in einem Supportpaket gesammelt werden.

> [!NOTE]
> Sie können im Azure-Portal auch Aktivitätsprotokolle für virtuelle Computer überwachen. Weitere Informationen finden Sie unter [Überwachen der VM-Aktivität auf Ihrem Azure Stack Edge Pro GPU-Gerät](azure-stack-edge-gpu-monitor-virtual-machine-activity.md).


## <a name="collect-vm-guest-logs-in-support-package"></a>Erfassen von VM-Gastprotokollen im Supportpaket

Führen Sie folgende Schritte aus, um Gastprotokolle für ausgefallene VMs auf einem Azure Stack Edge Pro GPU-Gerät zu erfassen:

1. [Stellen Sie auf Ihrem Gerät eine Verbindung mit der PowerShell-Schnittstelle her](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

2. Erfassen Sie Gastprotokolle für ausgefallene VMs, und schließen Sie diese Protokolle in ein Supportpaket ein, indem Sie folgende Befehle ausführen:

   ```powershell
   Get-VMInGuestLogs -FailedVM
   Get-HcsNodeSupportPackage -Path “\\<network path>” -Include InGuestVMLogFiles -Credential “domain_name\user”
   ```

   Sie finden die Protokolle im Ordner `hcslogs\VmGuestLogs`.

3. Informationen zum VM-Bereitstellungsverlauf finden Sie in folgenden Protokollen:

   **Virtuelle Linux-Computer:**
   - /var/log/cloud-init-output.log
   - /var/log/cloud-init.log
   - /var/log/waagent.log

   **Virtuelle Windows-Computer:**
   - C:\Windows\Azure\Panther\WaSetup.xml

## <a name="next-steps"></a>Nächste Schritte

- [Überwachen der VM-Aktivität auf Ihrem Azure Stack Edge Pro GPU-Gerät](azure-stack-edge-gpu-monitor-virtual-machine-activity.md)
- [Behandeln von Problemen bei der Bereitstellung virtueller Computer in Azure Stack Edge Pro GPU](azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning.md)