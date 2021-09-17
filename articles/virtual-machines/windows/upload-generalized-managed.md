---
title: Erstellen einer VM aus einer hochgeladenen generalisierten Windows-VHD
description: Laden Sie eine generalisierte Windows-VHD in Azure hoch, und erstellen Sie damit neue VMs im Resource Manager-Bereitstellungsmodell.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/12/2019
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4f31fff34dcaaa8cc30b1a894e24f3eda8e5764d
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688585"
---
# <a name="upload-a-generalized-windows-vhd-and-use-it-to-create-new-vms-in-azure"></a>Hochladen einer generalisierten Windows-VHD und Verwendung dieser zum Erstellen neuer VMs in Azure

**Gilt für**: :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

In diesem Artikel wird erläutert, wie Sie mithilfe von PowerShell eine VHD eines generalisierten virtuellen Computers in Azure hochladen, ein Image anhand der VHD erstellen und einen neuen virtuellen Computer anhand dieses Images erstellen. Sie können eine VHD hochladen, die aus einem lokalen Virtualisierungstool oder aus einer anderen Cloud exportiert wurde. Durch Verwenden von [Managed Disks](../managed-disks-overview.md) für den neuen virtuellen Computer wird die VM-Verwaltung vereinfacht. Außerdem steigt die Verfügbarkeit, wenn der virtuelle Computer in einer Verfügbarkeitsgruppe platziert wird. 

Ein Beispielskript finden Sie unter [Beispielskript zum Hochladen einer generalisierten virtuellen Festplatte nach Azure und Erstellen eines neuen virtuellen Computers](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script).

## <a name="before-you-begin"></a>Voraussetzungen

- Bevor Sie eine VHD in Azure hochladen, befolgen Sie die Anweisungen unter [Vorbereiten einer Windows-VHD oder -VHDX zum Hochladen in Azure](prepare-for-upload-vhd-image.md).
- Lesen Sie [Planen der Migration zu Managed Disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) vor dem Starten der Migration zu [Managed Disks](../managed-disks-overview.md).

 
## <a name="generalize-the-source-vm-by-using-sysprep"></a>Generalisieren des virtuellen Quellcomputers mithilfe von Sysprep

Wenn Sie es noch nicht getan haben, müssen Sie die VM vor dem Hochladen der VHD auf Azure mithilfe von Sysprep vorbereiten. Sysprep entfernt unter anderem alle persönlichen Kontoinformationen, und bereitet den Computer darauf vor, als Image verwendet zu werden. Ausführliche Informationen zu Sysprep finden Sie unter [Sysprep (Systemvorbereitung) – Übersicht](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Stellen Sie sicher, dass die auf dem Computer ausgeführten Serverrollen von Sysprep unterstützt werden. Weitere Informationen finden Sie unter [Sysprep Support for Server Roles](/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles)(Sysprep-Unterstützung für Serverrollen).

> [!IMPORTANT]
> Wenn Sie Sysprep vor dem Hochladen der VHD in Azure zum ersten Mal ausführen möchten, stellen Sie sicher, dass Sie [Ihren virtuellen Computer vorbereitet](prepare-for-upload-vhd-image.md) haben. 
> 
> 

1. Melden Sie sich bei dem virtuellen Windows-Computer an.
1. Öffnen Sie das Eingabeaufforderungsfenster als Administrator. 
1. Löschen Sie das Verzeichnis „Panther“ (c:\Windows\Panther).
1. Wechseln Sie in das Verzeichnis „%windir%\system32\sysprep“, und führen Sie anschließend `sysprep.exe` aus.
1. Wählen Sie im Dialogfeld **Systemvorbereitungsprogramm** die Option **Out-of-Box-Experience (OOBE) für System aktivieren** aus, und vergewissern Sie sich, dass das Kontrollkästchen **Verallgemeinern** aktiviert ist.
1. Wählen Sie unter **Optionen für Herunterfahren** die Option **Herunterfahren** aus.
1. Klicken Sie auf **OK**.
   
    ![Starten von Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
1. Nach Abschluss von Sysprep wird der virtuelle Computer heruntergefahren. Starten Sie den virtuellen Computer nicht neu.


## <a name="upload-the-vhd"></a>Hochladen der VHD 

Sie können nun eine VHD direkt in einen verwalteten Datenträger hochladen. Eine Anleitung hierzu finden Sie unter [Hochladen einer VHD in Azure mithilfe von Azure PowerShell](disks-upload-vhd-to-managed-disk-powershell.md).



Sobald die VHD auf den verwalteten Datenträger hochgeladen ist, müssen Sie [Get-AzDisk](/powershell/module/az.compute/get-azdisk) verwenden, um den verwalteten Datenträger abzurufen.

```azurepowershell-interactive
$disk = Get-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="create-the-image"></a>Erstellen des Images
Erstellen Sie ein verwaltetes Image anhand Ihres generalisierten verwalteten Betriebssystemdatenträgers. Ersetzen Sie die folgenden Werte durch Ihre eigenen Informationen.

Legen Sie zuerst einige Variablen fest:

```powershell
$location = 'East US'
$imageName = 'myImage'
$rgName = 'myResourceGroup'
```

Erstellen Sie das Image mithilfe Ihres verwalteten Datenträgers.

```azurepowershell-interactive
$imageConfig = New-AzImageConfig `
   -Location $location
$imageConfig = Set-AzImageOsDisk `
   -Image $imageConfig `
   -OsState Generalized `
   -OsType Windows `
   -ManagedDiskId $disk.Id
```

Erstellen Sie das Image.

```azurepowershell-interactive
$image = New-AzImage `
   -ImageName $imageName `
   -ResourceGroupName $rgName `
   -Image $imageConfig
```

## <a name="create-the-vm"></a>Erstellen des virtuellen Computers

Nachdem Sie ein Image erstellt haben, können Sie anhand des Images eine oder mehrere neue VMs erstellen. In diesem Beispiel wird ein virtueller Computer namens *myVM* anhand von *myImage* in *myResourceGroup* erstellt.


```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Image $image.Id `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNSG" `
    -PublicIpAddressName "myPIP" `
    -OpenPorts 3389
```


## <a name="next-steps"></a>Nächste Schritte

Melden Sie sich bei Ihrem neuen virtuellen Computer an. Anweisungen dazu finden Sie unter [Herstellen einer Verbindung mit einem virtuellen Azure-Computer unter Windows und Anmelden bei diesem Computer](connect-logon.md).
