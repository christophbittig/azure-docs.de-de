---
title: Ausmusterung der Av1-Serie
description: Informationen zur Ausmusterung für die VM-Größen der Av1-Serie.
author: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 07/26/2021
ms.author: mimckitt
ms.openlocfilehash: cfc30046d33ad9e6f91b866dc5653fbbcbdbd995
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128577730"
---
# <a name="av1-series-retirement"></a>Ausmusterung der Av1-Serie

Am 31. August 2024 werden die Basic- und Standard-VMs der A-Serie ausgemustert. Vor diesem Datum müssen Sie Ihre Workloads auf VMs der Av2-Serie migrieren, die mehr Arbeitsspeicher pro virtueller CPU und schnelleren Speicher auf SSD-Laufwerken (Solid State Drive) bieten.

> [!NOTE]
> In einigen Fällen müssen Benutzer die Zuordnung der VM vor der Größenänderung aufheben. Dies ist möglicherweise der Fall, falls die neue Größe auf dem Hardwarecluster nicht verfügbar ist, auf dem die VM aktuell gehostet wird.


## <a name="migrate-workloads-from-basic-and-standard-a-series-vms-to-av2-series-vms"></a>Migrieren von Workloads von Basic- und Standard-VMs der A-Serie auf VMs der Av2-Serie 

Sie können die Größe Ihrer virtuellen Maschinen über das [Azure-Portal, die PowerShell oder die Befehlszeilenschnittstelle](resize-vm.md) auf die Av2-Serie umstellen. Im Folgenden finden Sie Beispiele dafür, wie Sie die Größe Ihrer VM über das Azure-Portal und mithilfe von PowerShell ändern können. 

> [!IMPORTANT]
> Die Größenänderung für den virtuellen Computer führt zu einem Neustart. Es wird empfohlen, Aktionen auszuführen, die außerhalb der Geschäftszeiten zu einem Neustart führen. 

### <a name="azure-portal"></a>Das Azure-Portal 
1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
1. Geben Sie in das Suchfeld **virtuelle Computer** ein.
1. Wählen Sie unter **Dienste** die Option **Virtuelle Computer** aus.
1. Wählen Sie auf der Seite **Virtuelle Computer** den virtuellen Computer aus, dessen Größe Sie ändern möchten.
1. Wählen Sie im linken Menü die Option **Größe** aus.
1. Wählen Sie in der Liste der verfügbaren Größen eine neue Av2-Größe und dann **Größe ändern** aus.

### <a name="azure-powershell"></a>Azure PowerShell
1. Legen Sie die Variablen für die Ressourcengruppe und den VM-Namen fest. Ersetzen Sie die Werte durch die Informationen der VM, deren Größe Sie ändern möchten. 

    ```powershell
    $resourceGroup = "myResourceGroup"
    $vmName = "myVM"
    ```
2. Listet die VM-Größen auf, die auf dem Hardwarecluster verfügbar sind, auf dem die VM gehostet wird.

    ```powershell
    Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName
    ```

3. Ändern Sie die Größe der VM auf die neue Größe.

    ```powershell
    $vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
    $vm.HardwareProfile.VmSize = "<newAv2VMsize>"
    Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
    ```

## <a name="help-and-support"></a>Hilfe und Support

Wenn Sie Fragen haben, stellen Sie diese den Communityexperten in [Microsoft Q&A](/answers/topics/azure-virtual-machines.html). Wenn Sie über einen Supportplan verfügen und technische Hilfe benötigen, erstellen Sie eine [Supportanfrage](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest):

1. Wählen Sie als „Problemtyp“ die Option „Technisch“ aus.
1. Wählen Sie unter Abonnement Ihr Abonnement aus.
1. Klicken Sie für „Dienst“ auf „Meine Dienste“.
1. Wählen Sie als „Diensttyp“ einen virtuellen Computer unter Windows/Linux aus.
1. Geben Sie unter „Zusammenfassung“ eine Zusammenfassung Ihrer Anfrage ein.
1. Wählen Sie als „Problemtyp“ die Option „Unterstützung bei der Größenänderung für meine VM“ aus.
1. Wählen Sie unter „Problemuntertyp“ die für Sie geltende Option aus.

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie mehr über die [VMs der Av2-Serie](av2-series.md).
