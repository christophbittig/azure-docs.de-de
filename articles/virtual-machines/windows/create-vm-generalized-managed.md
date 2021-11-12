---
title: Erstellen einer VM auf der Grundlage eines verwalteten Images in Azure
description: Erstellen Sie einen virtuellen Windows-Computer auf der Grundlage eines generalisierten verwalteten Images mit Azure PowerShell oder dem Azure-Portal.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: c4dec92555d52a1e405c9d27c4242237add47619
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131456459"
---
# <a name="create-a-vm-from-a-managed-image"></a>Erstellen eines virtuellen Computers aus einem verwalteten Image

**Gilt für**: :heavy_check_mark: Windows-VMs 

Sie können über das Azure-Portal oder mit PowerShell mehrere virtuelle Computer aus einem verwalteten Azure-VM-Image erstellen. Ein verwaltetes VM-Image enthält die erforderlichen Informationen zum Erstellen eines virtuellen Computers, einschließlich der Datenträger für Betriebssystem und Daten. Die virtuellen Festplatten, aus denen das Image besteht, einschließlich der Betriebssystem-Datenträger und sonstiger Datenträger, werden als verwaltete Datenträger gespeichert. 

Vor dem Erstellen eines neuen virtuellen Computers müssen Sie [ein verwaltetes VM-Image erstellen](capture-image-resource.md), das als Quellimage verwendet werden soll, und auf dem Image jedem Benutzer, der darauf zugreifen soll, Lesezugriff gewähren. 

Ein verwaltetes Image unterstützt bis zu 20 Bereitstellungen gleichzeitig. Wenn Sie versuchen, mehr als 20 VMs gleichzeitig aus demselben verwalteten Image zu erstellen, kann dies aufgrund der Einschränkungen bei der Speicherleistung einer einzelnen VHD zu Timeouts bei der Bereitstellung führen. Wenn Sie mehr als 20 virtuelle Computer gleichzeitig erstellen möchten, verwenden Sie ein Image für [Azure Compute Gallery](../shared-image-galleries.md) (früher Shared Image Gallery genannt), das mit jeweils einem Replikat pro 20 gleichzeitiger Bereitstellungen an virtuellen Computern konfiguriert wurde.

## <a name="use-the-portal"></a>Verwenden des Portals

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach einem verwalteten Image. Suchen Sie nach **Images**, und wählen Sie diese Option aus.
3. Wählen Sie das gewünschte Image aus der Liste aus. Die Seite **Übersicht** für Images wird geöffnet.
4. Klicken Sie im Menü auf **VM erstellen**.
5. Geben Sie die Informationen zum virtuellen Computer ein. Der hier eingegebene Benutzername und das Kennwort werden zum Anmelden beim virtuellen Computer verwendet. Wählen Sie **OK** aus, wenn Sie fertig sind. Sie können die neue VM in einer bestehenden Ressourcengruppe erstellen oder **Neu erstellen** auswählen, um eine neue Ressourcengruppe zum Speichern der VM zu erstellen.
6. Wählen Sie eine Größe für den virtuellen Computer. Wählen Sie die Option **Alle anzeigen**, oder ändern Sie den Filter **Supported disk type** (Unterstützter Datenträgertyp), um weitere Größen anzuzeigen. 
7. Nehmen Sie unter **Einstellungen** Änderungen nach Bedarf vor, und klicken Sie auf **OK**. 
8. Auf der Seite „Zusammenfassung“ sollte Ihr Imagename unter **Privates Image** aufgelistet werden. Klicken Sie auf **OK**, um die Bereitstellung des virtuellen Computers zu starten.


## <a name="use-powershell"></a>Verwenden von PowerShell

Sie können PowerShell verwenden, um eine VM aus einem Image zu erstellen, indem Sie den vereinfachten Parametersatz für das Cmdlet [New-AzVm](/powershell/module/az.compute/new-azvm) verwenden. Das Image muss sich in derselben Ressourcengruppe befinden, in der Sie die VM erstellen möchten.

 

Der vereinfachte Parametersatz für [New-AzVm](/powershell/module/az.compute/new-azvm) erfordert nur die Angabe eines Namens, einer Ressourcengruppe und eines Imagenamens, um eine VM aus einem Image zu erstellen. Mit „New-AzVm“ wird der Wert des Parameters **-Name** als Name für alle Ressourcen verwendet, die das Cmdlet automatisch erstellt. In diesem Beispiel geben wir ausführlichere Namen für die einzelnen Ressourcen an, lassen sie aber automatisch vom Cmdlet erstellen. Sie können Ressourcen, z.B. das virtuelle Netzwerk, auch im Voraus erstellen und den Ressourcennamen an das Cmdlet übergeben. Mit „New-AzVm“ werden die vorhandenen Ressourcen verwendet, wenn sie anhand des Namens gefunden werden können.

Im folgenden Beispiel wird eine VM mit dem Namen *myVMFromImage* in der Ressourcengruppe *myResourceGroup* aus dem Image *myImage* erstellt. 


```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVMfromImage" `
    -ImageName "myImage" `
    -Location "East US" `
    -VirtualNetworkName "myImageVnet" `
    -SubnetName "myImageSubnet" `
    -SecurityGroupName "myImageNSG" `
    -PublicIpAddressName "myImagePIP" `
    -OpenPorts 3389
```



## <a name="next-steps"></a>Nächste Schritte
[Erstellen und Verwalten von virtuellen Windows-Computern mit dem Azure PowerShell-Modul](tutorial-manage-vm.md)