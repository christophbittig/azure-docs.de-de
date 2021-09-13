---
title: Verwalten von VM-Tags auf Ihrem Azure Stack Edge Pro-GPU-Gerät über Azure PowerShell
description: Hier erfahren Sie, wie Tags virtueller Computer für VMs, die in Azure Stack Edge mithilfe von Azure PowerShell ausgeführt werden, erstellt und verwaltet werden.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/12/2021
ms.author: alkohli
ms.openlocfilehash: 605a438e44220bc4eb97cc3718188d0458ea24d7
ms.sourcegitcommit: aaaa6ee55f5843ed69944f5c3869368e54793b48
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/13/2021
ms.locfileid: "113664501"
---
# <a name="tag-vms-on-azure-stack-edge-via-azure-powershell"></a>Markieren von virtuellen Computern in Azure Stack Edge über Azure PowerShell

In diesem Artikel wird beschrieben, wie virtuelle Computer (VMs), die auf Ihren Azure Stack Edge Pro-GPU-Geräten ausgeführt werden, mit Azure PowerShell markiert werden. 

## <a name="about-tags"></a>Infos zu Tags

Tags sind benutzerdefinierte Schlüssel-Wert-Paare, die einer Ressource oder Ressourcengruppe zugewiesen werden können. Sie können Tags auf VMs anwenden, die auf Ihrem Gerät ausgeführt werden, um sie logisch in einer Taxonomie zu organisieren. Sie können Tags zum Zeitpunkt der Erstellung auf einer Ressource platzieren oder sie einer vorhandenen Ressource hinzufügen. Beispielsweise können Sie den Namen `Organization` und den Wert `Engineering` auf alle VMs anwenden, die von der Engineering-Abteilung in Ihrer Organisation verwendet werden.

Weitere Informationen zu Tags finden Sie unter [AzureRM.Tags](/powershell/module/azurerm.tags/?view=azurermps-6.13.0&preserve-view=true).

## <a name="prerequisites"></a>Voraussetzungen

Um einen virtuellen Computer mithilfe von PowerShell auf Ihrem Gerät bereitstellen zu können, muss Folgendes erfüllt sein:

- Sie haben Zugriff auf einen Client für die Verbindungsherstellung mit Ihrem Gerät.
    - Ihr Client verfügt über ein [unterstütztes Betriebssystem](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device).
    - Ihr Client ist so konfiguriert, dass er eine Verbindung mit der lokalen Azure Resource Manager-Instanz Ihres Geräts herstellt (siehe [Herstellen einer Verbindung mit Azure Resource Manager für Ihr Gerät](azure-stack-edge-gpu-connect-resource-manager.md)).


## <a name="verify-connection-to-local-azure-resource-manager"></a>Überprüfen der Verbindung mit der lokalen Azure Resource Manager-Instanz

[!INCLUDE [azure-stack-edge-gateway-verify-azure-resource-manager-connection](../../includes/azure-stack-edge-gateway-verify-azure-resource-manager-connection.md)]


## <a name="add-a-tag-to-a-vm"></a>Hinzufügen eines Tags zu einer VM

### <a name="az"></a>[Az](#tab/az)

1. Legen Sie einige Parameter fest.

    ```powershell
    $VMName = <VM Name>
    $VMRG = <VM Resource Group>
    $TagName = <Tag Name>
    $TagValue = <Tag Value>   
    ```

    Beispielausgabe:

    ```output
    PS C:\WINDOWS\system32> $VMName = "myazvm"
    PS C:\WINDOWS\system32> $VMRG = "myaseazrg"
    PS C:\WINDOWS\system32> $TagName = "Organization"
    PS C:\WINDOWS\system32> $TagValue = "Sales"
    ```

2. Rufen Sie das VM-Objekt und seine Tags ab.

   ```powershell
   $VirtualMachine = Get-AzVM -ResourceGroupName $VMRG -Name $VMName
   $tags = $VirtualMachine.Tags
   ```

3. Fügen Sie das Tag hinzu, und aktualisieren Sie die VM. Das Aktualisieren der VM kann einige Minuten dauern.

    Sie können das optionale Flag **-Force** verwenden, um den Befehl ohne Benutzerbestätigung auszuführen.

    ```powershell
    $tags.Add($TagName, $TagValue)
    Set-AzResource -ResourceId $VirtualMachine.Id -Tag $tags -Force
    ```

    Beispielausgabe:

    ```output
    PS C:\WINDOWS\system32> $VirtualMachine = Get-AzVM -ResourceGroupName $VMRG -Name $VMName
    PS C:\WINDOWS\system32> $tags = $VirtualMachine.Tags
    PS C:\WINDOWS\system32> $tags.Add($TagName, $TagValue)
    PS C:\WINDOWS\system32> Set-AzResource -ResourceID $VirtualMachine.ID -Tag $tags -Force   

    Name              : myazvm
    ResourceId        : /subscriptions/d64617ad-6266-4b19-45af-81112d213322/resourceGroups/myas
                        eazrg/providers/Microsoft.Compute/virtualMachines/myazvm
    ResourceName      : myazvm
    ResourceType      : Microsoft.Compute/virtualMachines
    ResourceGroupName : myaseazrg
    Location          : dbelocal
    SubscriptionId    : d64617ad-6266-4b19-45af-81112d213322
    Tags              : {Organization}
    Properties        : @{vmId=568a264f-c5d3-477f-a16c-4c5549eafa8c; hardwareProfile=;
                        storageProfile=; osProfile=; networkProfile=; diagnosticsProfile=;
                        provisioningState=Succeeded}
    ```



### <a name="azurerm"></a>[AzureRM](#tab/azurerm)

1. Legen Sie einige Parameter fest.

    ```powershell
    $VMName = <VM Name>
    $VMRG = <VM Resource Group>
    $TagName = <Tag Name>
    $TagValue = <Tag Value>   
    ```

    Beispielausgabe:

    ```output
    PS C:\WINDOWS\system32> $VMName = "myasetestvm1"
    PS C:\WINDOWS\system32> $VMRG = "myaserg2"
    PS C:\WINDOWS\system32> $TagName = "Organization"
    PS C:\WINDOWS\system32> $TagValue = "Sales"
    ```

2. Rufen Sie das VM-Objekt und seine Tags ab.

   ```powershell
   $VirtualMachine = Get-AzureRmVM -ResourceGroupName $VMRG -Name $VMName
   $tags = $VirtualMachine.Tags
   ```

3. Fügen Sie das Tag hinzu, und aktualisieren Sie die VM. Das Aktualisieren der VM kann einige Minuten dauern.

    Sie können das optionale Flag **-Force** verwenden, um den Befehl ohne Benutzerbestätigung auszuführen.

    ```powershell
    $tags.Add($TagName, $TagValue)
    Set-AzureRmResource -ResourceId $VirtualMachine.Id -Tag $tags -Force
    ```

    Beispielausgabe:

    ```output
    PS C:\WINDOWS\system32> $VirtualMachine = Get-AzureRMVM -ResourceGroupName $VMRG -Name $VMName
    PS C:\WINDOWS\system32> $tags = $VirtualMachine.Tags
    PS C:\WINDOWS\system32> $tags.Add($TagName, $TagValue)
    PS C:\WINDOWS\system32> Set-AzureRmResource -ResourceID $VirtualMachine.ID -Tag $tags -Force
    
    Name              : myasetestvm1
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg2/providers/Microsoft.Compute/virtua
                        lMachines/myasetestvm1
    ResourceName      : myasetestvm1
    ResourceType      : Microsoft.Compute/virtualMachines
    ResourceGroupName : myaserg2
    Location          : dbelocal
    SubscriptionId    : 992601bc-b03d-4d72-598e-d24eac232122
    Tags              : {Organization}
    Properties        : @{vmId=958c0baa-e143-4d8a-82bd-9c6b1ba45e86; hardwareProfile=; storageProfile=; osProfile=; networkProfile=;
                        provisioningState=Succeeded}
    
    PS C:\WINDOWS\system32>
    ```

Weitere Informationen finden Sie unter [Add-AzureRMTag](/powershell/module/azurerm.tags/remove-azurermtag?view=azurermps-6.13.0&preserve-view=true).

---

## <a name="view-tags-of-a-vm"></a>Anzeigen von Tags eines virtuellen Computers

### <a name="az"></a>[Az](#tab/az)

Sie können die Tags anzeigen, die auf einen bestimmten virtuellen Computer angewendet werden, der auf Ihrem Gerät ausgeführt wird. 

1. Definieren Sie die Parameter, die dem virtuellen Computer zugeordnet sind, dessen Tags Sie anzeigen möchten.

   ```powershell
   $VMName = <VM Name>
   $VMRG = <VM Resource Group>
   ```
    Beispielausgabe:

    ```output
    PS C:\WINDOWS\system32> $VMName = "myazvm"
    PS C:\WINDOWS\system32> $VMRG = "myaseazrg"
    ```
1. Rufen Sie das VM-Objekt ab, und zeigen Sie seine Tags an.

   ```powershell
   $VirtualMachine = Get-AzVM -ResourceGroupName $VMRG -Name $VMName
   $VirtualMachine.Tags
   ```
    Beispielausgabe:

    ```output
    PS C:\WINDOWS\system32>  $VirtualMachine = Get-AzVM -ResourceGroupName $VMRG -Name $VMName     
    PS C:\WINDOWS\system32> $VirtualMachine.Tags
    
    Key          Value
    ---          -----
    Organization Sales    
    
    PS C:\WINDOWS\system32>
    ```


### <a name="azurerm"></a>[AzureRM](#tab/azurerm)

Sie können die Tags anzeigen, die auf einen bestimmten virtuellen Computer angewendet werden, der auf Ihrem Gerät ausgeführt wird. 

1. Definieren Sie die Parameter, die dem virtuellen Computer zugeordnet sind, dessen Tags Sie anzeigen möchten.

   ```powershell
   $VMName = <VM Name>
   $VMRG = <VM Resource Group>
   ```
    Beispielausgabe:

    ```output
    PS C:\WINDOWS\system32> $VMName = "myasetestvm1"
    PS C:\WINDOWS\system32> $VMRG = "myaserg2"
    ```
1. Rufen Sie das VM-Objekt ab, und zeigen Sie seine Tags an.

   ```powershell
   $VirtualMachine = Get-AzureRmVM -ResourceGroupName $VMRG -Name $VMName
   $VirtualMachine.Tags
   ```
    Beispielausgabe:

    ```output
    PS C:\WINDOWS\system32> $VirtualMachine = Get-AzureRMVM -ResourceGroupName $VMRG -Name $VMName
    PS C:\WINDOWS\system32> $VirtualMachine

    ResourceGroupName : myaserg2
    Id                : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg2/providers/Microsoft.Compute/virtua
    lMachines/myasetestvm1
    VmId              : 958c0baa-e143-4d8a-82bd-9c6b1ba45e86
    Name              : myasetestvm1
    Type              : Microsoft.Compute/virtualMachines
    Location          : dbelocal
    Tags              : {"Organization":"Sales"}
    HardwareProfile   : {VmSize}
    NetworkProfile    : {NetworkInterfaces}
    OSProfile         : {ComputerName, AdminUsername, LinuxConfiguration, Secrets}
    ProvisioningState : Succeeded
    StorageProfile    : {ImageReference, OsDisk, DataDisks}
    
    PS C:\WINDOWS\system32>
    ```
---

## <a name="view-tags-for-all-resources"></a>Anzeigen von Tags für alle Ressourcen

### <a name="az"></a>[Az](#tab/az)

Verwenden Sie den Befehl `Get-AzTag`, um die aktuelle Liste der Tags für alle Ressourcen im lokalen Azure Resource Manager-Abonnement (unterscheidet sich von Ihrem Azure-Abonnement) Ihres Geräts anzuzeigen.


Im Folgenden finden Sie ein Beispiel für die Ausgabe, wenn mehrere VMs auf Ihrem Gerät ausgeführt werden und Sie alle Tags auf allen VMs anzeigen möchten.

```output
PS C:\WINDOWS\system32> Get-AzTag

Name         Count
----         -----
Organization 2

PS C:\WINDOWS\system32>
```

Die vorherige Ausgabe zeigt an, dass auf den virtuellen Computern, die auf Ihrem Gerät ausgeführt werden, zwei `Organization`-Tags vorhanden sind.

Verwenden Sie den `-Detailed`-Parameter, um weitere Details anzuzeigen.

```output
PS C:\WINDOWS\system32> Get-AzTag -Detailed |fl

Name        : Organization
ValuesTable :
              Name         Count
              ===========  =====
              Sales        1
              Engineering  1
Count       : 2
Values      : {Sales, Engineering}

PS C:\WINDOWS\system32>
```

Die obige Ausgabe zeigt an, dass ein virtueller Computer mit `Engineering` gekennzeichnet ist und der andere als zu `Sales` gehörend gekennzeichnet ist.

### <a name="azurerm"></a>[AzureRM](#tab/azurerm)

Verwenden Sie den Befehl `Get-AzureRMTag`, um die aktuelle Liste der Tags für alle Ressourcen im lokalen Azure Resource Manager-Abonnement (unterscheidet sich von Ihrem Azure-Abonnement) Ihres Geräts anzuzeigen.


Im Folgenden finden Sie ein Beispiel für die Ausgabe, wenn mehrere VMs auf Ihrem Gerät ausgeführt werden und Sie alle Tags auf allen VMs anzeigen möchten.

```powershell
PS C:\WINDOWS\system32> Get-AzureRMTag

Name         Count
----         -----
Organization 3

PS C:\WINDOWS\system32>
```

Die vorherige Ausgabe zeigt an, dass auf den virtuellen Computern, die auf Ihrem Gerät ausgeführt werden, drei `Organization`-Tags vorhanden sind.

Verwenden Sie den `-Detailed`-Parameter, um weitere Details anzuzeigen.

```output
PS C:\WINDOWS\system32> Get-AzureRMTag -Detailed |fl

Name        : Organization
ValuesTable :
              Name         Count
              ===========  =====
              Engineering  2
              Sales        1

Count       : 3
Values      : {Engineering, Sales}

PS C:\WINDOWS\system32>
```

Die obige Ausgabe zeigt an, dass 2 VMs mit `Engineering` gekennzeichnet sind und eine als zu `Sales` gehörend gekennzeichnet ist.

---

## <a name="remove-a-tag-from-a-vm"></a>Entfernen eines Tags von einem virtuellen Computer

### <a name="az"></a>[Az](#tab/az)

1. Legen Sie einige Parameter fest. 

    ```powershell
    $VMName = <VM Name>
    $VMRG = <VM Resource Group>
    $TagName = <Tag Name>
   ``` 
    Beispielausgabe:

    ```output
    PS C:\WINDOWS\system32> $VMName = "myaselinuxvm1"
    PS C:\WINDOWS\system32> $VMRG = "myaserg1"
    PS C:\WINDOWS\system32> $TagName = "Organization" 
    ```
2. Rufen Sie das VM-Objekt ab.

    ```powershell
    $VirtualMachine = Get-AzVM -ResourceGroupName $VMRG -Name $VMName
    $VirtualMachine   
    ```   

    Beispielausgabe:

    ```output
    PS C:\WINDOWS\system32> $VirtualMachine = Get-AzVM -ResourceGroupName $VMRG -Name $VMName
    PS C:\WINDOWS\system32> $VirtualMachine
    
    ResourceGroupName  : myaseazrg
    Id                 : /subscriptions/d64617ad-6266-4b19-45af-81112d213322/resourceGroups/mya
    seazrg/providers/Microsoft.Compute/virtualMachines/myazvm
    VmId               : 568a264f-c5d3-477f-a16c-4c5549eafa8c
    Name               : myazvm
    Type               : Microsoft.Compute/virtualMachines
    Location           : dbelocal
    Tags               : {"Organization":"Sales"}
    DiagnosticsProfile : {BootDiagnostics}
    HardwareProfile    : {VmSize}
    NetworkProfile     : {NetworkInterfaces}
    OSProfile          : {ComputerName, AdminUsername, LinuxConfiguration, Secrets,
    AllowExtensionOperations, RequireGuestProvisionSignal}
    ProvisioningState  : Succeeded
    StorageProfile     : {ImageReference, OsDisk, DataDisks}
    
    PS C:\WINDOWS\system32>
    ```
3. Entfernen Sie das Tag, und aktualisieren Sie die VM. Führen Sie den Befehl mit dem optionalen Flag `-Force` ohne Benutzerbestätigung aus.

    ```powershell
    $tags = $VirtualMachine.Tags
    $tags.Remove($TagName)
    Set-AzResource -ResourceId $VirtualMachine.Id -Tag $tags -Force
    ```
    Beispielausgabe:

    ```output
    PS C:\WINDOWS\system32> $tags = $VirtualMachine.Tags
    PS C:\WINDOWS\system32> $tags.Remove($TagName)
    True
    PS C:\WINDOWS\system32> Set-AzResource -ResourceId $VirtualMachine.Id -Tag $tags -Force
    
    Name              : myazvm
    ResourceId        : /subscriptions/d64617ad-6266-4b19-45af-81112d213322/resourceGroups/myas
                        eazrg/providers/Microsoft.Compute/virtualMachines/myazvm
    ResourceName      : myazvm
    ResourceType      : Microsoft.Compute/virtualMachines
    ResourceGroupName : myaseazrg
    Location          : dbelocal
    SubscriptionId    : d64617ad-6266-4b19-45af-81112d213322
    Tags              : {}
    Properties        : @{vmId=568a264f-c5d3-477f-a16c-4c5549eafa8c; hardwareProfile=;
                        storageProfile=; osProfile=; networkProfile=; diagnosticsProfile=;
                        provisioningState=Succeeded}
    
    PS C:\WINDOWS\system32>
    ```

### <a name="azurerm"></a>[AzureRM](#tab/azurerm)

1. Legen Sie einige Parameter fest. 

    ```powershell
    $VMName = <VM Name>
    $VMRG = <VM Resource Group>
    $TagName = <Tag Name>
   ``` 
    Beispielausgabe:

    ```output
    PS C:\WINDOWS\system32> $VMName = "myaselinuxvm1"
    PS C:\WINDOWS\system32> $VMRG = "myaserg1"
    PS C:\WINDOWS\system32> $TagName = "Organization" 
    ```
2. Rufen Sie das VM-Objekt ab.

    ```powershell
    $VirtualMachine = Get-AzureRmVM -ResourceGroupName $VMRG -Name $VMName
    $VirtualMachine   
    ```   

    Beispielausgabe:

    ```output
    PS C:\WINDOWS\system32> $VirtualMachine = Get-AzureRMVM -ResourceGroupName $VMRG -Name $VMName
    ResourceGroupName : myaserg1
    Id                : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg1/providers/Microsoft.Compute/virtualMachines/myaselinuxvm1
    VmId              : 290b3fdd-0c99-4905-9ea1-cf93cd6f25ee
    Name              : myaselinuxvm1
    Type              : Microsoft.Compute/virtualMachines
    Location          : dbelocal
    Tags              : {"Organization":"Engineering"}
    HardwareProfile   : {VmSize}
    NetworkProfile    : {NetworkInterfaces}
    OSProfile         : {ComputerName, AdminUsername, LinuxConfiguration, Secrets}
    ProvisioningState : Succeeded
    StorageProfile    : {ImageReference, OsDisk, DataDisks}
                                                                              PS C:\WINDOWS\system32> 
    ```
3. Entfernen Sie das Tag, und aktualisieren Sie die VM. Führen Sie den Befehl mit dem optionalen Flag `-Force` ohne Benutzerbestätigung aus.

    ```powershell
    $tags = $VirtualMachine.Tags
    $tags.Remove($TagName)
    Set-AzureRmResource -ResourceId $VirtualMachine.Id -Tag $tags [-Force]
    ```
    Beispielausgabe:

    ```output
    PS C:\WINDOWS\system32> $tags = $Virtualmachine.Tags
    PS C:\WINDOWS\system32> $tags
    Key          Value
    ---          -----
    Organization Engineering
    PS C:\WINDOWS\system32> $tags.Remove($TagName)
    True
    PS C:\WINDOWS\system32> Set-AzureRMResource -ResourceID $VirtualMachine.ID -Tag $tags -Force
    Name              : myaselinuxvm1
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGrou
                        ps/myaserg1/providers/Microsoft.Compute/virtualMachines/myaselin
                        uxvm1
    ResourceName      : myaselinuxvm1
    ResourceType      : Microsoft.Compute/virtualMachines
    ResourceGroupName : myaserg1
    Location          : dbelocal
    SubscriptionId    : 992601bc-b03d-4d72-598e-d24eac232122
    Tags              : {}
    Properties        : @{vmId=290b3fdd-0c99-4905-9ea1-cf93cd6f25ee; hardwareProfile=;
                        storageProfile=; osProfile=; networkProfile=;
                        provisioningState=Succeeded}
    PS C:\WINDOWS\system32>
    ```
---

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [einen virtuellen Computer in Azure mithilfe von az-Cmdlets in PowerShell markieren](../virtual-machines/tag-powershell.md).
- Informieren Sie sich über die [Verwaltung von Tags über AzureRM-Cmdlets in PowerShell](/powershell/module/azurerm.tags/?view=azurermps-6.13.0&preserve-view=true).
