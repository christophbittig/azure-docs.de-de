---
title: 'Tutorial: Verwalten von Azure-Datenträgern mit Azure PowerShell'
description: In diesem Tutorial erfahren Sie, wie Sie Azure PowerShell zum Erstellen und Verwalten von Azure-Datenträgern für VMs verwenden.
author: roygara
ms.author: rogarana
ms.service: storage
ms.subservice: disks
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.date: 10/08/2021
ms.custom: template-tutorial, devx-track-azurepowershell
ms.openlocfilehash: a1ea898dd246977bbb7284a18349a265efd7655d
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063116"
---
# <a name="tutorial-manage-disks-with-azure-powershell"></a>Tutorial: Verwalten von Datenträgern mit Azure PowerShell

Virtuelle Azure-Computer verwenden Datenträger zum Speichern von Betriebssystemen, Anwendungen und Daten. Beim Erstellen eines virtuellen Computers muss darauf geachtet werden, eine für die erwartete Workload geeignete Datenträgergröße und -konfiguration auszuwählen.

In diesem Tutorial werden die Bereitstellung und Verwaltung der Datenträger von virtuellen Computern behandelt. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen, Anfügen und Initialisieren eines Datenträgers
> * Überprüfen des Status eines Datenträgers
> * Initialisieren eines Datenträgers
> * Erweitern und Aktualisieren eines Datenträgers
> * Trennen und Löschen eines Datenträgers

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen ein Azure-Konto mit einem aktiven Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-vm"></a>Erstellen einer VM

Für die Übungen in diesem Tutorial ist ein virtueller Computer erforderlich. Führen Sie zum Erstellen eines virtuellen Computers die Schritte in diesem Abschnitt aus.

Bevor Sie beginnen, suchen Sie die Variable `$azRegion` in der ersten Zeile des Beispielcodes, und aktualisieren Sie den Wert entsprechend der gewünschten Region. Verwenden Sie beispielsweise `$azRegion = "Central US"`, um die Region **USA, Mitte** anzugeben. Verwenden Sie als Nächstes den Code, um einen virtuellen Computer in einer neuen Ressourcengruppe bereitzustellen. Sie werden zur Eingabe von Werten für Benutzername und Kennwort für das lokale Administratorkonto des virtuellen Computers aufgefordert.

```azurepowershell-interactive
$azRegion = "[Your Region]"
$azResourceGroup = "myDemoResourceGroup"
$azVMName = "myDemoVM"
$azDataDiskName = "myDemoDataDisk"

New-AzVm `
    -Location $azRegion `
    -ResourceGroupName $azResourceGroup `
    -Name $azVMName `
    -Size "Standard_D2s_v3" `
    -VirtualNetworkName "myDemoVnet" `
    -SubnetName "myDemoSubnet" `
    -SecurityGroupName "myDemoNetworkSecurityGroup" `
    -PublicIpAddressName "myDemoPublicIpAddress"
```

In der Ausgabe wird die erfolgreiche Erstellung des virtuellen Computers bestätigt:

```Output
ResourceGroupName        : myDemoResourceGroup
Id                       : /subscriptions/{GUID}/resourceGroups/myDemoResourceGroup/providers/Microsoft.Compute/virtualMachines/myDemoTestVM
VmId                     : [{GUID}]
Name                     : myDemoVM
Type                     : Microsoft.Compute/virtualMachines
Location                 : centralus
Tags                     : {}
HardwareProfile          : {VmSize}
NetworkProfile           : {NetworkInterfaces}
OSProfile                : {ComputerName, AdminUsername, WindowsConfiguration, AllowExtensionOperations, RequireGuestProvisionSignal}
ProvisioningState        : Succeeded
StorageProfile           : {ImageReference, OsDisk, DataDisks}
FullyQualifiedDomainName : mydemovm-abc123.Central US.cloudapp.azure.com
```

Der virtuelle Computer wird bereitgestellt, und zwei Datenträger werden automatisch erstellt und angefügt:

- Ein **Betriebssystemdatenträger**, auf dem das Betriebssystem des virtuellen Computers gehostet wird
- Ein **temporärer Datenträger**, der hauptsächlich für Vorgänge wie die temporäre Datenverarbeitung verwendet wird

## <a name="add-a-data-disk"></a>Hinzufügen eines Datenträgers

Es wird empfohlen, nach Möglichkeit Anwendungs- und Benutzerdaten von Betriebssystemdaten zu trennen. Wenn Sie Benutzer- oder Anwendungsdaten auf Ihrem virtuellen Computer speichern müssen, erstellen Sie in der Regel zusätzliche Datenträger und fügen sie an.

Führen Sie die Schritte in diesem Abschnitt aus, um einen Datenträger auf dem virtuellen Computer zu erstellen, anzufügen und zu initialisieren.

### <a name="create-the-data-disk"></a>Erstellen des Datenträgers

In diesem Abschnitt erhalten Sie Informationen zur Erstellung eines Datenträgers.

1. Bevor ein Datenträger erstellt werden kann, müssen Sie zunächst ein Datenträgerobjekt erstellen. Im folgenden Codebeispiel wird das Cmdlet [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) zum Konfigurieren eines Datenträgerobjekts verwendet:

    ```azurepowershell-interactive
    $diskConfig = New-AzDiskConfig `
        -Location $azRegion `
        -CreateOption Empty `
        -DiskSizeGB 128 `
        -SkuName "Standard_LRS"
    ```

1. Verwenden Sie nach der Erstellung des Datenträgerobjekts das Cmdlet [New-AzDisk](/powershell/module/az.compute/new-azdisk), um einen Datenträger bereitzustellen:

    ```azurepowershell-interactive
    $dataDisk = New-AzDisk `
        -ResourceGroupName $azResourceGroup `
        -DiskName $azDataDiskName `
        -Disk $diskConfig
    ```

    Sie können mit dem Cmdlet [Get-AzDisk](/powershell/module/az.compute/get-azdisk) überprüfen, ob der Datenträger erstellt wurde:

    ```azurepowershell-interactive
    Get-AzDisk `
        -ResourceGroupName $azResourceGroup `
        -DiskName $azDataDiskName
    ```

    In diesem Beispiel bestätigt die Ausgabe, dass der Datenträger erstellt wurde. Die Eigenschaftswerte `DiskState` und `ManagedBy` geben an, dass der Datenträger noch nicht angefügt wurde.

    ```Output
    ResourceGroupName            : myDemoResourceGroup
    ManagedBy                    :
    ManagedByExtended            : {}
    OsType                       :
    DiskSizeGB                   : 128
    DiskSizeBytes                : 137438953472
    ProvisioningState            : Succeeded
    DiskIOPSReadWrite            : 500
    DiskMBpsReadWrite            : 60
    DiskState                    : Unattached
    Name                         : myDemoDataDisk
    ```

### <a name="attach-the-data-disk"></a>Anfügen des Datenträgers

Ein Datenträger muss an einen virtuellen Computer angefügt werden, damit der virtuelle Computer darauf zugreifen kann. Führen Sie die Schritte in diesem Abschnitt aus, um einen Verweis für den virtuellen Computer zu erstellen, den Datenträger zu verbinden und die Konfiguration des virtuellen Computers zu aktualisieren.

1. Rufen Sie den virtuellen Computer ab, an den Sie den Datenträger anfügen. Im folgenden Beispielcode wird mithilfe des Cmdlets [Get-AzVM](/powershell/module/az.compute/get-azvm) ein Verweis auf den virtuellen Computer erstellt:

    ```azurepowershell-interactive
    $vm = Get-AzVM `
        -ResourceGroupName $azResourceGroup `
        -Name $azVMName
    ```

1. Fügen Sie als Nächstes den Datenträger mit dem Cmdlet [Add-AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk) zur Konfiguration des virtuellen Computers hinzu:

    ```azurepowershell-interactive
    $vm = Add-AzVMDataDisk `
        -VM $vm `
        -Name $azDataDiskName `
        -CreateOption Attach `
        -ManagedDiskId $dataDisk.Id `
        -Lun 1
    ```

1. Aktualisieren Sie abschließend die Konfiguration des virtuellen Computers mithilfe des Cmdlets [Update-AzVM](/powershell/module/az.compute/add-azvmdatadisk):

    ```azurepowershell-interactive
    Update-AzVM `
        -ResourceGroupName $azResourceGroup `
        -VM $vm
    ```

    Nach einer kurzen Pause wird in der Ausgabe der erfolgreiche Anfügevorgang bestätigt:

    ```Output
    RequestId IsSuccessStatusCode StatusCode ReasonPhrase
    --------- ------------------- ---------- ------------
                             True         OK OK
    ```

### <a name="initialize-the-data-disk"></a>Initialisieren des Datenträgers

Nachdem ein Datenträger an den virtuellen Computer angefügt wurde, muss das Betriebssystem für die Verwendung des Datenträgers konfiguriert werden. Der folgende Abschnitt enthält eine Anleitung zum Herstellen einer Verbindung mit dem virtuellen Remotecomputer und zum Konfigurieren des ersten hinzugefügten Datenträgers.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Suchen Sie den virtuellen Computer, an den Sie den Datenträger angefügt haben. Erstellen Sie eine RDP-Verbindung (Remotedesktopprotokoll), und melden Sie sich als lokaler Administrator an.

1. Nach dem Herstellen einer RDP-Verbindung mit dem virtuellen Remotecomputer wählen Sie das Windows-**Startmenü** aus. Geben Sie **PowerShell** in das Suchfeld ein, und wählen Sie **Windows PowerShell** aus, um ein PowerShell-Fenster zu öffnen.

    [![Abbildung: Fenster einer Remotedesktopverbindung](media\tutorial-manage-data-disk\initialize-disk-sml.png)](media\tutorial-manage-data-disk\initialize-disk-lrg.png#lightbox)

1. Führen Sie im geöffneten PowerShell-Fenster das folgende Skript aus:

    ```powershell
    Get-Disk | Where PartitionStyle -eq 'raw' |
        Initialize-Disk -PartitionStyle MBR -PassThru |
        New-Partition -AssignDriveLetter -UseMaximumSize |
        Format-Volume -FileSystem NTFS -NewFileSystemLabel "myDemoDataDisk" -Confirm:$false
    ```

    In der Ausgabe wird eine erfolgreiche Initialisierung bestätigt:

    ```Output
    DriveLetter FileSystemLabel FileSystem DriveType HealthStatus OperationalStatus SizeRemaining   Size
    ----------- --------------- ---------- --------- ------------ ----------------- -------------   ----
    F           myDemoDataDisk  NTFS       Fixed     Healthy      OK                    127.89 GB 128 GB
    ```

## <a name="expand-a-disk"></a>Erweitern eines Datenträgers

Sie können Azure-Datenträger erweitern, um zusätzliche Speicherkapazität bereitzustellen, wenn ihr virtueller Computer nicht über genügend Speicherplatz verfügt.

In einigen Szenarien müssen Daten auf dem Betriebssystemdatenträger gespeichert werden. Möglicherweise müssen Legacyanwendungen unterstützt werden, die Komponenten auf dem Betriebssystemlaufwerk installieren. Ggf. müssen Sie auch einen lokalen physischen PC oder einen virtuellen Computer mit einem größeren Betriebssystemlaufwerk migrieren. In solchen Fällen kann es erforderlich werden, den Betriebssystemdatenträger eines virtuellen Computers zu erweitern.

Das Verkleinern eines vorhandenen Datenträgers wird nicht unterstützt und kann möglicherweise zu Datenverlusten führen.

### <a name="update-the-disks-size"></a>Aktualisieren der Datenträgergröße

Führen Sie die folgenden Schritte aus, um die Größe des Betriebssystemdatenträgers oder eines Datenträgers für Daten zu ändern.

1. Wählen Sie mit dem Cmdlet `Get-AzVM` den virtuellen Computer mit dem Datenträger aus, dessen Größe Sie ändern möchten:

    ```azurepowershell-interactive
     $vm = Get-AzVM `
       -ResourceGroupName $azResourceGroup `
       -Name $azVMName
    ```

1. Damit Sie die Größe des Datenträgers eines virtuellen Computers ändern können, müssen Sie den virtuellen Computer beenden. Verwenden Sie das Cmdlet `Stop-AzVM` zum Beenden des virtuellen Computers. Sie werden zur Bestätigung aufgefordert.

    > [!IMPORTANT]
    > Vergewissern Sie sich vor dem Herunterfahren eines virtuellen Computers immer, dass darauf keine wichtigen Ressourcen oder Daten vorhanden sind, die verloren gehen könnten.

    ```azurepowershell-interactive
    Stop-AzVM `
        -ResourceGroupName $azResourceGroup `
        -Name $azVMName
    ```

    Nach einer kurzen Pause wird in der Ausgabe bestätigt, dass der Computer erfolgreich beendet wurde:

    ```Output
    OperationId : abcd1234-ab12-cd34-123456abcdef
    Status      : Succeeded
    StartTime   : 9/13/2021 7:10:23 PM
    EndTime     : 9/13/2021 7:11:12 PM
    Error       :
    ```

1. Nach dem Beenden des virtuellen Computers rufen Sie mit dem Cmdlet `Get-AzDisk` einen Verweis auf den Betriebssystemdatenträger oder den Datenträger für Daten ab, der an den virtuellen Computer angefügt ist.

    Im folgenden Beispiel wird der Betriebssystemdatenträger des virtuellen Computers ausgewählt:

    ```azurepowershell-interactive
    $disk= Get-AzDisk `
        -ResourceGroupName $azResourceGroup `
        -DiskName $vm.StorageProfile.OsDisk.Name
    ```

    Im folgenden Beispiel wird der erste Datenträger des virtuellen Computers ausgewählt:

    ```azurepowershell-interactive
        $disk= Get-AzDisk `
            -ResourceGroupName $azResourceGroup `
            -DiskName $vm.StorageProfile.DataDisks[0].Name
    ```

1. Nachdem Sie nun über einen Verweis auf den Datenträger verfügen, legen Sie die Größe des Datenträgers auf 250 GiB fest.

    > [!IMPORTANT]
    > Die neue Größe sollte die Größe des vorhandenen Datenträgers überschreiten. Der zulässige Höchstwert für Betriebssystemdatenträger liegt bei 4.095 GiB.

    ```azurepowershell-interactive
    $disk.DiskSizeGB = 250
    ```

1. Aktualisieren Sie als Nächstes mit dem Cmdlet `Update-AzDisk` das Datenträgerimage:

    ```azurepowershell-interactive
    Update-AzDisk `
        -ResourceGroupName $azResourceGroup `
        -Disk $disk -DiskName $disk.Name
    ```

    Das Datenträgerimage wird aktualisiert, und in der Ausgabe wird die neue Größe des Datenträgers bestätigt:

    ```Output
    ResourceGroupName            : myDemoResourceGroup
    ManagedBy                    : /subscriptions/{GUID}/resourceGroups/myDemoResourceGroup/providers/Microsoft.Compute/virtualMachines/myDemoVM
    Sku                          : Microsoft.Azure.Management.Compute.Models.DiskSku
    TimeCreated                  : 9/135/2021 6:41:10 PM
    CreationData                 : Microsoft.Azure.Management.Compute.Models.CreationData
    DiskSizeGB                   : 250
    DiskSizeBytes                : 268435456000
    UniqueId                     : {GUID}
    ProvisioningState            : Succeeded
    DiskIOPSReadWrite            : 500
    DiskMBpsReadWrite            : 60
    DiskState                    : Reserved
    Encryption                   : Microsoft.Azure.Management.Compute.Models.Encryption
    Id                           : /subscriptions/{GUID}/resourceGroups/myDemoResourceGroup/providers/Microsoft.Compute/disks/myDemoDataDisk
    Name                         : myDemoDataDisk
    Type                         : Microsoft.Compute/disks
    Location                     : centralus

1. Finally, restart the VM with the `Start-AzVM` cmdlet.

    ```azurepowershell-interactive
    Start-AzVM `
        -ResourceGroupName $azResourceGroup `
        -Name $azVMName
    ```

    Nach einer kurzen Pause wird in der Ausgabe bestätigt, dass der Computer erfolgreich gestartet wurde:

    ```Output
    OperationId : abcd1234-ab12-cd34-123456abcdef
    Status      : Succeeded
    StartTime   : 9/13/2021 7:44:54 PM
    EndTime     : 9/13/2021 7:45:15 PM
    Error       :
    ```

### <a name="expand-the-disk-volume-in-the-os"></a>Erweitern des Datenträgervolumes im Betriebssystem

Bevor Sie die neue Datenträgergröße nutzen können, müssen Sie das Volume innerhalb des Betriebssystems erweitern. Führen Sie die folgenden Schritte aus, um das Datenträgervolume zu erweitern und die neue Datenträgergröße zu nutzen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Navigieren Sie zu dem virtuellen Computer, an den Sie den Datenträger angefügt haben. Erstellen Sie eine RDP-Verbindung (Remotedesktopprotokoll), und melden Sie sich an. Wenn Sie keinen Zugriff mehr auf ein Administratorkonto haben, erstellen Sie mit dem Cmdlet [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) ein Anmeldeinformationsobjekt für einen angegebenen Benutzernamen und ein angegebenes Kennwort.

1. Nach dem Herstellen einer RDP-Verbindung mit dem virtuellen Remotecomputer wählen Sie das Windows-**Startmenü** aus. Geben Sie **PowerShell** in das Suchfeld ein, und wählen Sie **Windows PowerShell** aus, um ein PowerShell-Fenster zu öffnen:

    [![Abbildung: Fenster einer Remotedesktopverbindung](media\tutorial-manage-data-disk\initialize-disk-sml.png)](media\tutorial-manage-data-disk\initialize-disk-lrg.png#lightbox)

1. Öffnen Sie PowerShell, und führen Sie das folgende Skript aus. Ändern Sie den Wert der Variablen `-DriveLetter` nach Bedarf. Verwenden Sie beispielsweise `$driveLetter = "F"`, um die Größe der Partition auf dem Laufwerk **F:** zu ändern.

    ```powershell
    $driveLetter = "[Drive Letter]" `
    $size = (Get-PartitionSupportedSize -DriveLetter $driveLetter) `
    Resize-Partition `
        -DriveLetter $driveLetter `
        -Size $size.SizeMax
    ```

1. Minimieren Sie das RDP-Fenster, und wechseln Sie zurück zu Azure Cloud Shell. Überprüfen Sie mithilfe des Cmdlets `Get-AzDisk`, ob die Größe des Datenträgers erfolgreich geändert wurde:

    ```azurepowershell-interactive
    Get-AzDisk `
        -ResourceGroupName $azResourceGroup | Out-Host -Paging
    ```

## <a name="upgrade-a-disk"></a>Aktualisieren eines Datenträgers

Es gibt mehrere Möglichkeiten, auf Änderungen bei den Workloads Ihrer Organisation zu reagieren. Sie können beispielsweise eine HDD Standard-Instanz auf SSD Premium aktualisieren, um den erhöhten Bedarf zu bewältigen.

Führen Sie die Schritte in diesem Abschnitt aus, um das Upgrade eines verwalteten Datenträgers von Standard auf Premium durchzuführen.

1. Wählen Sie mit dem Cmdlet `Get-AzVM` den virtuellen Computer mit dem Datenträger aus, dessen Größe Sie ändern möchten.

    ```azurepowershell-interactive
     $vm = Get-AzVM `
       -ResourceGroupName $azResourceGroup `
       -Name $azVMName
    ```

1. Damit Sie den Datenträger eines virtuellen Computers aktualisieren können, müssen Sie den virtuellen Computer beenden. Verwenden Sie das Cmdlet `Stop-AzVM` zum Beenden des virtuellen Computers. Sie werden zur Bestätigung aufgefordert.

    > [!IMPORTANT]
    > Vergewissern Sie sich vor dem Herunterfahren eines virtuellen Computers immer, dass darauf keine wichtigen Ressourcen oder Daten vorhanden sind, die verloren gehen könnten.

    ```azurepowershell-interactive
    Stop-AzVM `
        -ResourceGroupName $azResourceGroup `
        -Name $azVMName
    ```

    Nach einer kurzen Pause wird in der Ausgabe bestätigt, dass der Computer erfolgreich beendet wurde:

    ```Output
    OperationId : abcd1234-ab12-cd34-123456abcdef
    Status      : Succeeded
    StartTime   : 9/13/2021 7:10:23 PM
    EndTime     : 9/13/2021 7:11:12 PM
    Error       :
    ```

1. Nach dem Beenden des virtuellen Computers rufen Sie mit dem Cmdlet `Get-AzDisk` einen Verweis auf den Betriebssystemdatenträger oder den Datenträger für Daten ab, der an den virtuellen Computer angefügt ist.

    Im folgenden Beispiel wird der Betriebssystemdatenträger des virtuellen Computers ausgewählt:

    ```azurepowershell-interactive
    $disk= Get-AzDisk `
        -ResourceGroupName $azResourceGroup `
        -DiskName $vm.StorageProfile.OsDisk.Name
    ```

    Im folgenden Beispiel wird der erste Datenträger des virtuellen Computers ausgewählt:

    ```azurepowershell-interactive
        $disk= Get-AzDisk `
            -ResourceGroupName $azResourceGroup `
            -DiskName $vm.StorageProfile.DataDisks[0].Name
    ```

1. Nachdem Sie nun über einen Verweis auf den Datenträger verfügen, legen Sie die SKU des Datenträgers auf **Premium_LRS** fest.

    ```azurepowershell-interactive
    $disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new('Premium_LRS')
    ```

1. Aktualisieren Sie als Nächstes mit dem Cmdlet `Update-AzDisk` das Datenträgerimage:

    ```azurepowershell-interactive
    Update-AzDisk `
        -ResourceGroupName $azResourceGroup `
        -Disk $disk -DiskName $disk.Name
    ```

    Das Datenträgerimage wird aktualisiert. Verwenden Sie den folgenden Beispielcode, um zu überprüfen, ob die SKU des Datenträgers aktualisiert wurde:

    ```azurepowershell-interactive
    $disk.Sku.Name
    ```

    In der Ausgabe wird die neue SKU des Datenträgers bestätigt:

    ```Output
    Premium_LRS
    ```

1. Starten Sie den virtuellen Computer schließlich mit dem Cmdlet `Start-AzVM` neu:

    ```azurepowershell-interactive
    Start-AzVM `
        -ResourceGroupName $azResourceGroup `
        -Name $azVMName
    ```

    Nach einer kurzen Pause wird in der Ausgabe bestätigt, dass der Computer erfolgreich gestartet wurde:

    ```Output
    OperationId : abcd1234-ab12-cd34-123456abcdef
    Status      : Succeeded
    StartTime   : 9/13/2021 7:44:54 PM
    EndTime     : 9/13/2021 7:45:15 PM
    Error       :
    ```

## <a name="detach-a-data-disk"></a>Trennen eines Datenträgers

Sie können einen Datenträger von einem virtuellen Computer trennen, wenn Sie ihn an einen anderen virtuellen Computer anfügen möchten oder wenn er nicht mehr benötigt wird. Standardmäßig werden getrennte Datenträger nicht gelöscht, um unbeabsichtigte Datenverluste zu verhindern. Für einen getrennten Datenträger fallen weiterhin Speichergebühren an, bis er gelöscht wird.

1. Wählen Sie zunächst mit dem Cmdlet `Get-AzVM` den virtuellen Computer aus, an den der Datenträger angefügt ist:

    ```azurepowershell-interactive
    $vm = Get-AzVM `
       -ResourceGroupName $azResourceGroup `
       -Name $azVMName
    ```

1. Trennen Sie als Nächstes den Datenträger mit dem Cmdlet `Remove-AzVMDataDisk` vom virtuellen Computer:

    ```azurepowershell-interactive
    Remove-AzVMDataDisk `
        -VM $vm `
        -Name $azDataDiskName
    ```

1. Aktualisieren Sie den Status des virtuellen Computers mit dem Cmdlet `Update-AzVM`, um den Datenträger zu entfernen:

    ```azurepowershell-interactive
    Update-AzVM `
        -ResourceGroupName $azResourceGroup `
        -VM $vm
    ```

    Nach einer kurzen Pause wird in der Ausgabe bestätigt, dass der Computer erfolgreich aktualisiert wurde:

    ```output
    RequestId IsSuccessStatusCode StatusCode ReasonPhrase
    --------- ------------------- ---------- ------------
                             True         OK OK
    ```

## <a name="delete-a-data-disk"></a>Löschen eines Datenträgers

Wenn Sie einen virtuellen Computer löschen, bleiben die an den virtuellen Computer angefügten Datenträger bereitgestellt und verursachen weiterhin Gebühren, bis sie gelöscht werden. Dieses Standardverhalten trägt dazu bei, Datenverluste zu verhindern, die durch unbeabsichtigtes Löschen verursacht werden.

Sie können das folgende PowerShell-Beispielskript verwenden, um nicht angefügte Datenträger zu löschen. Der Abruf von Datenträgern ist auf **myDemoResourceGroup** beschränkt, da der Switch `-ResourceGroupName` mit dem Cmdlet `Get-AzDisk` verwendet wird.

```azurepowershell
# Get all disks in resource group $azResourceGroup
$allDisks = Get-AzDisk -ResourceGroupName $azResourceGroup

# Determine the number of disks in the collection
if($allDisks.Count -ne 0) {

    Write-Host "Found $($allDisks.Count) disks."

    # Iterate through the collection
    foreach ($disk in $allDisks) {

        # Use the disk's "ManagedBy" property to determine if it is unattached
        if($disk.ManagedBy -eq $null) {

            # Confirm that the disk can be deleted
            Write-Host "Deleting unattached disk $($disk.Name)."
            $confirm = Read-Host "Continue? (Y/N)"
            if ($confirm.ToUpper() -ne 'Y') { break }
            else {

                # Delete the disk
                $disk | Remove-AzDisk -Force 
                Write-Host "Unattached disk $($disk.Name) deleted."
            }
        }
    }
}
```

Der nicht angefügte Datenträger wird wie in der Ausgabe gezeigt gelöscht:

```output
Name      : abcd1234-ab12-cd34-ef56-abcdef123456
StartTime : 9/13/2021 10:14:05 AM
EndTime   : 9/13/2021 10:14:35 AM
Status    : Succeeded
Error     :
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe, den virtuellen Computer und alle dazugehörigen Ressourcen, wenn Sie sie nicht mehr benötigen. Sie können das folgende PowerShell-Beispielskript verwenden, um die zuvor in diesem Tutorial erstellte Ressourcengruppe zu löschen.  

> [!CAUTION]
> Seien Sie vorsichtig, wenn Sie eine Ressourcengruppe löschen. Um den Verlust wichtiger Daten zu vermeiden, vergewissern Sie sich immer, dass in der Ressourcengruppe keine wichtigen Ressourcen oder Daten enthalten sind, bevor Sie sie löschen.

```azurepowershell-interactive
    Remove-AzResourceGroup -Name $azResourceGroup
```

Sie werden aufgefordert, diesen Schritt zu bestätigen. Nach einer kurzen Pause wird mit der Antwort `True` bestätigt, dass **myDemoResourceGroup** erfolgreich gelöscht wurde:
    
```Output
Confirm
Are you sure you want to remove resource group 'myDemoResourceGroup'
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
True
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen, Anfügen und Initialisieren eines Datenträgers
> * Überprüfen des Status eines Datenträgers
> * Initialisieren eines Datenträgers
> * Erweitern und Aktualisieren eines Datenträgers
> * Trennen und Löschen eines Datenträgers

Im nächsten Tutorial erfahren Sie, wie die VM-Konfiguration automatisiert werden kann:

> [!div class="nextstepaction"]
> [Automatisieren der VM-Konfiguration](./tutorial-automate-vm-deployment.md)
