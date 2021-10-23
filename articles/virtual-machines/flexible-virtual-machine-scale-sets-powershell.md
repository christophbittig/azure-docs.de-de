---
title: Erstellen von VMs in einer flexiblen Skalierungsgruppe mit Azure PowerShell
description: Erfahren Sie, wie Sie im Orchestrierungsmodus „Flexibel“ eine VM-Skalierungsgruppe in PowerShell erstellen.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: flexible-scale-sets
ms.date: 08/05/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: 45f998dbba1ffac99fc8d491cb90694a76c11f98
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130165753"
---
# <a name="create-virtual-machines-in-a-flexible-scale-set-using-powershell"></a>Erstellen von VMs in einer flexiblen Skalierungsgruppe mit PowerShell

**Gilt für:** :heavy_check_mark: Flexible Skalierungsgruppen


In diesem Artikel wird beschrieben, wie Sie PowerShell verwenden sollten, um eine VM-Skalierungsgruppe im flexiblen Orchestrierungsmodus zu erstellen. Weitere Informationen über flexible Skalierungsgruppen finden Sie unter [Flexibler Orchestrierungsmodus für Skalierungsgruppen für VM](flexible-virtual-machine-scale-sets.md). 

> [!CAUTION]
> Der Orchestrierungsmodus wird beim Erstellen der Skalierungsgruppe festgelegt und kann später nicht mehr geändert oder aktualisiert werden.


## <a name="launch-azure-cloud-shell"></a>Starten von Azure Cloud Shell

Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel ausführen können. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert. 

Wählen Sie zum Öffnen von Cloud Shell oben rechts in einem Codeblock einfach die Option **Ausprobieren**. Sie können Cloud Shell auch auf einer separaten Browserregisterkarte starten, indem Sie zu [https://shell.azure.com/powershell](https://shell.azure.com/powershell) navigieren. Wählen Sie **Kopieren**, um die Blöcke mit dem Code zu kopieren. Fügen Sie ihn anschließend in Cloud Shell ein, und drücken Sie die EINGABETASTE, um ihn auszuführen.


## <a name="get-started-with-flexible-scale-sets"></a>Erste Schritte mit flexiblen Skalierungsgruppen

Erstellen einer flexiblen VM-Skalierungsgruppe mit Azure PowerShell.

### <a name="add-multiple-vms-to-a-scale-set"></a>Hinzufügen mehrerer VMs zu einer Skalierungsgruppe 

Im folgenden Beispiel wird ein Profil für VMs (VM-Typ, Netzwerkkonfiguration, Speichertyp usw.) und die Anzahl der zu erstellenden Instanzen (sku Kapazität = 2) angegeben. 

1. Erstellen Sie die IP-Adresse-Konfigurationen:

    ```azurepowershell-interactive
    $ipConfig = New-AzVmssIpConfig -Name "myIPConfig"
    -SubnetId "${vnetid}/subnets/default" `
    -LoadBalancerBackendAddressPoolsId $lb.BackendAddressPools[0].Id
    ```

1. Erstellen Sie ein Konfigurationsobjekt:

    Das Konfigurationsobjekt speichert die Kerninformationen zum Erstellen einer Skalierungsgruppe.

    ```azurepowershell-interactive
    $vmssConfig = New-AzVmssConfig -Location $loc
    -SkuCapacity 2 -SkuName "Standard_DS1_v2"
    -OrchestrationMode 'Flexible' `
    -PlatformFaultDomainCount 1
    ```

1. Erstellen Sie ein Bild einer VM über den Katalog:

    ```azurepowershell-interactive
    Set-AzVmssStorageProfile $vmssConfig -OsDiskCreateOption "FromImage"
    -ImageReferencePublisher "Canonical" -ImageReferenceOffer "UbuntuServer"
    -ImageReferenceSku "18.04-LTS" `
    -ImageReferenceVersion "latest"
    ```

1. Richten Sie Informationen für die Authentifizierung mit der VM ein:

    ```azurepowershell-interactive
    Set-AzVmssOsProfile $vmssConfig -AdminUsername $cred.UserName
    -AdminPassword $cred.Password -ComputerNamePrefix $vmname
    ```

1. Fügen Sie das virtuelle Netzwerk an das Konfigurationsobjekt an:

    ```azurepowershell-interactive
    Add-AzVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmssConfig
    -Name "network-config" -Primary $true
    -IPConfiguration $ipConfig `
    -NetworkApiVersion '2020-11-01'
    ```

1. Erstellen Sie die Skalierungsgruppe mit dem Konfigurationsobjekt:

    Dieser Schritt kann einige Minuten in Anspruch nehmen. 

    ```azurepowershell-interactive
    New-AzVmss -ResourceGroupName $rgname
    -Name $vmssName `
    -VirtualMachineScaleSet $vmssConfig
    ```

### <a name="add-a-single-vm-to-a-scale-set"></a>Hinzufügen einer VM zu einer Skalierungsgruppe

Das folgende Beispiel zeigt die Erstellung einer flexiblen Skalierungsgruppe ohne VM-Profil, wobei die Anzahl der Fehlerdomänen auf 1 gesetzt wird. Eine VM wird erstellt und dann der flexiblen Skalierungsgruppe hinzugefügt.

1. Melden Sie sich bei Azure PowerShell an, und geben Sie das Abonnement und die Variablen für die Bereitstellung an. 

    ```azurepowershell-interactive
    Connect-AzAccount
    Set-AzContext `
        -Subscription "00000000-0000-0000-0000-000000000" 
    
    $loc = "eastus" 
    $rgname = "myResourceGroupFlexible" 
    $vmssName = "myFlexibleVMSS" 
    $vmname = "myFlexibleVM"
    ```

1. Geben Sie keine VM-Profilparameter wie Netzwerke oder VM-SKUs an.

    ```azurepowershell-interactive
    $VmssConfigWithoutVmProfile = new-azvmssconfig -location $loc -platformfaultdomain 1 `
    $VmssFlex = new-azvmss -resourcegroupname $rgname -vmscalesetname $vmssName -virtualmachinescaleset $VmssConfigWithoutVmProfile 
    ```
 
1. Fügen Sie der flexiblen Skalierungsgruppe eine VM hinzu.

    ```azurepowershell-interactive
    $vm = new-azvm -resourcegroupname $rgname -location $loc -name $vmname -credential $cred -domainnamelabel $domainName -vmssid $VmssFlex.id 
    ```


## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Erfahren Sie, wie Sie eine flexible Skalierungsgruppe im Azure-Portal erstellen.](flexible-virtual-machine-scale-sets-portal.md)
