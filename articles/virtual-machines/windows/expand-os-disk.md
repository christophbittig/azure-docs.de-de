---
title: Erweitern virtueller Festplatten, die an eine Windows VM in Azure angefügt sind
description: Erweitern Sie die Größe des Betriebssystemlaufwerks eines virtuellen Computers mit Azure PowerShell im Resource Manager-Bereitstellungsmodell.
author: kirpasingh
manager: roshar
ms.service: virtual-machines
ms.collection: windows
ms.topic: article
ms.date: 11/02/2021
ms.author: kirpas
ms.subservice: disks
ms.custom: devx-track-azurepowershell, references_regions, ignite-fall-2021
ms.openlocfilehash: d744bf5ec5619985aaa899183657504c7e3afeff
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131466673"
---
# <a name="how-to-expand-virtual-hard-disks-attached-to-a-windows-virtual-machine"></a>Erweitern virtueller Festplatten, die an einen Windows virtuellen Computer angefügt sind

**Gilt für:** :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

Beim Erstellen eines neuen virtuellen Computers in einer Ressourcengruppe durch Bereitstellen eines Images von [Azure Marketplace](https://azure.microsoft.com/marketplace/) hat das Standard-Betriebssystemlaufwerk häufig eine Größe von 127 GB (bei manchen Images sind die Standard-Betriebssystemlaufwerke kleiner). Obwohl es möglich ist, der VM Datenträger hinzuzufügen (die Anzahl hängt von der SKU ab, die Sie ausgewählt haben), und empfohlen wird, Anwendungen und CPU-intensive Workloads auf diesen zusätzlichen Datenträgern zu installieren, müssen Kunden oft das Betriebssystemlaufwerk erweitern, um spezielle Szenarien zu unterstützen:

- Unterstützung von Legacyanwendungen, die Komponenten auf dem Betriebssystemlaufwerk installieren.
- Migrieren eines lokalen physischen PC oder einer lokalen VM mit einem größeren Betriebssystemlaufwerk.

> [!IMPORTANT]
> Ein Ändern der Größe des Datenträgers mit dem Betriebssystem oder den Daten eines virtuellen Azure-Computers erfordert, dass die Zuordnung des virtuellen Computers aufgehoben wird.
>
> Das Verkleinern eines vorhandenen Datenträgers wird nicht unterstützt und kann möglicherweise zu Datenverlusten führen.
> 
> Wenn Sie die Datenträger erweitern, müssen Sie [das Volume innerhalb des Betriebssystems erweitern](#expand-the-volume-within-the-os), um den größere Datenträger nutzen zu können.

## <a name="resize-without-downtime-preview"></a>Ändern der Größe ohne Ausfallzeiten (Vorschau)

Sie können nun die Größe Ihrer verwalteten Datenträger ändern, ohne Ihre VM zu deallokieren.

Die Vorschau für diesen Bereich hat folgende Einschränkungen:

[!INCLUDE [virtual-machines-disks-expand-without-downtime-restrictions](../../../includes/virtual-machines-disks-expand-without-downtime-restrictions.md)]

Um sich für die Funktion zu registrieren, verwenden Sie den folgenden Befehl:

```azurepowershell
Register-AzProviderFeature -FeatureName "LiveResize" -ProviderNamespace "Microsoft.Compute"
```

Es kann ein paar Minuten dauern, bis die Registrierung abgeschlossen ist. Um zu bestätigen, dass Sie sich registriert haben, verwenden Sie den folgenden Befehl:

```azurepowershell
Register-AzProviderFeature -FeatureName "LiveResize" -ProviderNamespace "Microsoft.Compute"
```

## <a name="resize-a-managed-disk-in-the-azure-portal"></a>Ändern der Größe eines verwalteten Datenträgers im Azure-Portal

> [!IMPORTANT]
> Wenn Sie **LiveResize** aktiviert haben und Ihre Festplatte die Anforderungen in [Resize ohne Ausfallzeit (Vorschau)](#resize-without-downtime-preview) erfüllt, können Sie Schritt 1 überspringen. Um die Größe eines Datenträgers ohne Ausfallzeiten im Azure-Portal zu ändern, müssen Sie den folgenden Link verwenden: [https://aka.ms/iaasexp/DiskLiveResize](https://aka.ms/iaasexp/DiskLiveResize)

1. Navigieren Sie im [Azure-Portal](https://aka.ms/iaasexp/DiskLiveResize) zu dem virtuellen Computer, auf dem Sie den Datenträger erweitern möchten. Wählen Sie **Beenden** aus, um die Zuordnung der VM aufzuheben.
1. Wählen Sie im Menü links unter **Einstellungen** die Option **Datenträger aus**.

    :::image type="content" source="./media/expand-os-disk/select-disks.png" alt-text="Screenshot: Im Abschnitt „Einstellungen“ des Menüs ist die Option „Datenträger“ ausgewählt.":::

 
1. Wählen Sie unter **Name des Datenträgers** den Datenträger aus, dessen Größe Sie ändern möchten.

    :::image type="content" source="./media/expand-os-disk/disk-name.png" alt-text="Screenshot des Bereichs „Datenträger“, in dem der Name eines Datenträgers ausgewählt ist":::

1. Klicken Sie im Menü auf der linken Seite unter **Einstellungen** auf **Größe und Leistung**.

    :::image type="content" source="./media/expand-os-disk/configuration.png" alt-text="Screenshot: Ausgewählte Option „Größe und Leistung“ im Abschnitt „Einstellungen“ des Menüs":::

1. Wählen Sie unter **Größe und Leistung** die gewünschte Datenträgergröße aus.
   
   > [!WARNING]
   > Die neue Größe sollte die Größe des vorhandenen Datenträgers überschreiten. Der zulässige Höchstwert für Betriebssystemdatenträger liegt bei 4.095 GB. (Sie können auch ein größeres VHD-Blob verwenden, das Betriebssystem arbeitet jedoch nur mit den ersten 4.095 GB.)
   > 

    :::image type="content" source="./media/expand-os-disk/size.png" alt-text="Screenshot: Bereich „Größe und Leistung“ mit ausgewählter Datenträgergröße":::

1. Klicken Sie unten auf der Seite auf **Größe ändern**.

    :::image type="content" source="./media/expand-os-disk/save.png" alt-text="Screenshot: Bereich „Größe und Leistung“ mit ausgewählter Schaltfläche „Größe ändern“":::


## <a name="resize-a-managed-disk-by-using-powershell"></a>Ändern der Größe eines verwalteten Datenträgers mithilfe von PowerShell

Öffnen Sie die PowerShell ISE oder das PowerShell-Fenster im Administratormodus, und führen Sie die folgenden Schritte aus:

1. Melden Sie sich im Ressourcenverwaltungsmodus bei Ihrem Microsoft Azure-Konto an, und wählen Sie Ihr Abonnement aus:
   
    ```powershell
    Connect-AzAccount
    Select-AzSubscription –SubscriptionName 'my-subscription-name'
    ```

1. Legen Sie den Namen der Ressourcengruppe und den Namen der VM fest:
   
    ```powershell
    $rgName = 'my-resource-group-name'
    $vmName = 'my-vm-name'
    ```

1. Rufen Sie einen Verweis auf die VM ab:
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

    > [!IMPORTANT]
    > Wenn Sie **LiveResize** aktiviert haben und Ihre Festplatte die Anforderungen in [Resize ohne Ausfallzeit (Vorschau)](#resize-without-downtime-preview) erfüllt, können Sie Schritt 4 und 6 überspringen.

1. Halten Sie die VM vor dem Ändern der Größe des Datenträgers an:
   
    ```powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

1. Rufen Sie einen Verweis auf den verwalteten Betriebssystem-Datenträger ab. Legen Sie die Größe des verwalteten Betriebssystem-Datenträgers auf den gewünschten Wert fest, und aktualisieren Sie den Datenträger:
   
    ```powershell
    $disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
    $disk.DiskSizeGB = 1023
    Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
    ```   
    > [!WARNING]
    > Die neue Größe sollte die Größe des vorhandenen Datenträgers überschreiten. Der zulässige Höchstwert für Betriebssystemdatenträger liegt bei 4.095 GB. (Sie können auch ein größeres VHD-Blob verwenden, das Betriebssystem arbeitet jedoch nur mit den ersten 4.095 GB.)
    > 
         
1. Das Aktualisieren der VM kann einige Sekunden dauern. Starten Sie die VM nach Abschluss der Befehlsausführung neu:
   
    ```powershell
    Start-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

Das ist alles! Stellen Sie jetzt eine RDP-Verbindung mit der VM her, öffnen Sie die Computerverwaltung (oder die Datenträgerverwaltung), und erweitern Sie das Laufwerk mit dem neu zugewiesenen Speicherplatz.

## <a name="resize-an-unmanaged-disk-by-using-powershell"></a>Ändern der Größe eines nicht verwalteten Datenträgers mithilfe von PowerShell

Öffnen Sie die PowerShell ISE oder das PowerShell-Fenster im Administratormodus, und führen Sie die folgenden Schritte aus:

1. Melden Sie sich im Ressourcenverwaltungsmodus bei Ihrem Microsoft Azure-Konto an, und wählen Sie Ihr Abonnement aus:
   
    ```powershell
    Connect-AzAccount
    Select-AzSubscription –SubscriptionName 'my-subscription-name'
    ```

1. Legen Sie den Namen der Ressourcengruppe und den Namen der VM fest:
   
    ```powershell
    $rgName = 'my-resource-group-name'
    $vmName = 'my-vm-name'
    ```

1. Rufen Sie einen Verweis auf die VM ab:
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

1. Halten Sie die VM vor dem Ändern der Größe des Datenträgers an:
   
    ```powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

1. Legen Sie die Größe des nicht verwalteten Betriebssystem-Datenträgers auf den gewünschten Wert fest, und aktualisieren Sie die VM:
   
    ```powershell
    $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
    Update-AzVM -ResourceGroupName $rgName -VM $vm
    ```
   
    > [!WARNING]
    > Die neue Größe sollte die Größe des vorhandenen Datenträgers überschreiten. Der zulässige Höchstwert für Betriebssystemdatenträger beträgt 2.048 GB. (Sie können auch einen größeren VHD-Blob einsetzen, doch das Betriebssystem kann nur mit den ersten 2.048 GB arbeiten.)
    > 
    > 
   
1. Das Aktualisieren der VM kann einige Sekunden dauern. Starten Sie die VM nach Abschluss der Befehlsausführung neu:
   
    ```powershell
    Start-AzVM -ResourceGroupName $rgName -Name $vmName
    ```


## <a name="scripts-for-os-disk"></a>Skripts für den Betriebssystem-Datenträger

Nachstehend finden Sie als Referenz das vollständige Skript sowohl für verwaltete als auch nicht verwaltete Datenträger:


**Verwaltete Datenträger**

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
Stop-AzVM -ResourceGroupName $rgName -Name $vmName
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
$disk.DiskSizeGB = 1023
Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

**Nicht verwaltete Datenträger**

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
Stop-AzVM -ResourceGroupName $rgName -Name $vmName
$vm.StorageProfile.OSDisk.DiskSizeGB = 1023
Update-AzVM -ResourceGroupName $rgName -VM $vm
Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="resizing-data-disks"></a>Ändern der Größe von Datenträgern

Dieser Artikel konzentriert sich hauptsächlich auf die Erweiterung des verwalteten/nicht verwalteten Betriebssystem-Datenträgers der VM. Das Skript kann aber auch zum Erweitern der Datenträger verwendet werden, die an die VM angefügt sind. Um beispielsweise den ersten Datenträger zu erweitern, der an den virtuellen Computer angefügt ist, ersetzen Sie das `OSDisk`-Objekt von `StorageProfile` mit dem `DataDisks`-Array, und verwenden Sie einen numerischen Index, um – wie unten dargestellt – einen Verweis auf den ersten angefügten Datenträger zu erhalten:

**Verwalteter Datenträger**

```powershell
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```

**Nicht verwalteter Datenträger**

```powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```

Entsprechend können Sie auf andere Datenträger verweisen, die an die VM angefügt sind, indem Sie entweder (wie oben gezeigt) einen Index oder die **Name**-Eigenschaft des Datenträgers verwenden:


**Verwalteter Datenträger**

```powershell
(Get-AzDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

**Nicht verwalteter Datenträger**

```powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).DiskSizeGB = 1023
```

## <a name="expand-the-volume-within-the-os"></a>Erweitern des Volumes innerhalb des Betriebssystems

Wenn Sie den Datenträger für die VM erweitert haben, müssen Sie in das Betriebssystem wechseln und das Volume erweitern, damit es den neuen Speicherplatz einschließen kann. Es gibt mehrere Methoden zum Erweitern einer Partition. Dieser Abschnitt behandelt das Verbinden der VM über eine RDP-Verbindung, um die Partition mit **DiskPart** zu erweitern.

1. Öffnen Sie eine RDP-Verbindung mit Ihrer VM.

1. Öffnen Sie eine Eingabeaufforderung, und geben Sie **diskpart** ein.

1. Geben Sie `list volume` an der Eingabeaufforderung von **DISKPART** ein. Notieren Sie sich das Volume, das Sie erweitern möchten.

1. Geben Sie `select volume <volumenumber>` an der Eingabeaufforderung von **DISKPART** ein. Hiermit wählen Sie das Volume *Volumenummer* aus, das Sie in zusammenhängenden, leeren Speicherplatz auf dem gleichen Datenträger erweitern möchten.

1. Geben Sie `extend [size=<size>]` an der Eingabeaufforderung von **DISKPART** ein. Hierdurch wird das ausgewählte Volume um *Größe* in Megabyte (MB) erweitert.


## <a name="next-steps"></a>Nächste Schritte

Datenträger können auch über das [Azure-Portal](attach-managed-disk-portal.md) angefügt werden.
