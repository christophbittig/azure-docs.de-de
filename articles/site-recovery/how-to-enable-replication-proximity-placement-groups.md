---
title: Replizieren von Azure-VMs, die in einer Näherungsplatzierungsgruppe ausgeführt werden
description: Hier erfahren Sie, wie Sie Azure-VMs, die in Näherungsplatzierungsgruppen ausgeführt werden, mithilfe von Azure Site Recovery replizieren können.
author: Sharmistha-Rai
manager: gaggupta
ms.topic: how-to
ms.date: 02/11/2021
ms.openlocfilehash: c721014e2f0a61e46ad599bccdc26388aa706a3a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131444950"
---
# <a name="replicate-virtual-machines-running-in-a-proximity-placement-group-to-another-region"></a>Replizieren von virtuellen Computern, die in einer Näherungsplatzierungsgruppe ausgeführt werden, in eine andere Region

In diesem Artikel wird beschrieben, wie virtuelle Azure-Computer (VMs), die in einer Näherungsplatzierungsgruppe ausgeführt werden, in eine sekundäre Region repliziert werden und wie ein Failover bzw. Failback durchgeführt wird.

[Näherungsplatzierungsgruppen](../virtual-machines/windows/proximity-placement-groups-portal.md) sind eine logische Gruppierungsfunktion in Azure Virtual Machines. Sie können damit die Netzwerklatenz zwischen den VMs verringern, die Ihren Anwendungen zugeordnet sind. 

Wenn VMs innerhalb derselben Näherungsplatzierungsgruppe bereitgestellt werden, sind sie physisch so nah wie möglich beieinander. Näherungsplatzierungsgruppen eignen sich besonders, um die Anforderungen von latenzempfindlichen Workloads zu erfüllen.

## <a name="disaster-recovery-with-proximity-placement-groups"></a>Notfallwiederherstellung mit Näherungsplatzierungsgruppen

In einem typischen Szenario könnten Ihre virtuellen Computer in einer Näherungsplatzierungsgruppe ausgeführt werden, um die Netzwerklatenz zwischen den Ebenen Ihrer Anwendung zu vermeiden. Obwohl dieser Ansatz eine optimale Netzwerklatenz für Ihre Anwendung bieten kann, möchten Sie diese Anwendungen möglicherweise mithilfe von Azure Site Recovery bei Ausfällen auf Regionsebene schützen. 

Site Recovery repliziert die Daten aus einer Azure-Region in eine andere Region. Dadurch werden die Computer bei einem Failoverereignis in der Notfallwiederherstellungsregion (Disaster Recovery-Region, DR-Region) verfügbar.

## <a name="considerations"></a>Überlegungen

- Der beste Ansatz ist ein Failover und Failback der virtuellen Computer in eine Näherungsplatzierungsgruppe. Wenn Kapazitätszuordnungseinschränkungen existieren, aufgrund derer wir die VMs nicht innerhalb einer Näherungsplatzierungsgruppe öffnen können, werden das Failover und das Failback weiterhin ausgeführt, aber VMs werden außerhalb der Näherungsplatzierungsgruppe erstellt.
- Wenn eine Verfügbarkeitsgruppe an eine Näherungsplatzierungsgruppe angeheftet ist und es bei VMs in der Verfügbarkeitsgruppe während des Failbacks oder Failovers eine Zuordnungseinschränkung gibt, werden die VMs außerhalb der Verfügbarkeitsgruppe und der Näherungsplatzierungsgruppe erstellt.
- Site Recovery für Näherungsplatzierungsgruppen wird bei nicht verwalteten Datenträgern nicht unterstützt.

> [!NOTE]
> Azure Site Recovery unterstützt kein Failback von verwalteten Datenträgern für Szenarios vom Typ „Verschieben von Hyper-V zu Azure“. Ein Failback von Näherungsplatzierungsgruppen in Azure zu Hyper-V wird nicht unterstützt.

## <a name="set-up-disaster-recovery-for-vms-in-proximity-placement-groups-via-the-azure-portal"></a>Einrichten der Notfallwiederherstellung für VMs in Näherungsplatzierungsgruppen über das Azure-Portal

### <a name="azure-to-azure"></a>Azure zu Azure

Sie können die Replikation für einen virtuellen Computer über die Seite für VM-Notfallwiederherstellung aktivieren. Oder Sie können die Replikation aktivieren, indem Sie zu einem vorab erstellten Tresor wechseln, darin zum Abschnitt „Site Recovery“ wechseln und dann die Replikation aktivieren. Sehen wir uns an, wie Sie über beide Ansätze Site Recovery-VMs in einer Näherungsplatzierungsgruppe einrichten können.

So wählen Sie eine Näherungsplatzierungsgruppe in der DR-Region aus, während Sie die Replikation über die IaaS-VM-DR-Seite (Infrastructure-as-a-Service) aktivieren:

1. Erstellen Sie den virtuellen Computer. Wählen Sie im linken Bereich unter **Vorgänge** die Option **Notfallwiederherstellung** aus.
2. Wählen Sie auf der Registerkarte **Grundlagen** die DR-Region aus, in die Sie die VM replizieren möchten. Wechseln Sie zu **Erweiterte Einstellungen**.
3. Die Näherungsplatzierungsgruppe Ihrer VM und die Option zum Auswählen einer Näherungsplatzierungsgruppe in der DR-Region wird angezeigt. Site Recovery bietet Ihnen auch die Option zur Verwendung einer neuen Näherungsplatzierungsgruppe, die für Sie erstellt wird, wenn Sie diese Standardoption verwenden möchten. 
 
   Wählen Sie die gewünschte Näherungsplatzierungsgruppe aus. Wählen Sie dann **Replikation überprüfen und starten** aus.

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-group-a2a-1.png" alt-text="Screenshot der erweiterten Einstellungen zum Aktivieren der Replikation":::

So wählen Sie beim Aktivieren der Replikation über die Tresorseite eine Näherungsplatzierungsgruppe in der DR-Region aus:

1. Wechseln Sie zu Ihrem Recovery Services-Tresor und dann zur Registerkarte **Site Recovery**.
2. Klicken Sie auf **+ Enable Site Recovery** (+ Site Recovery aktivieren). Wählen Sie dann unter **Virtuelle Azure-Computer** die Option **1: Replikation aktivieren** aus (weil Sie eine Azure-VM replizieren möchten).
3. Füllen Sie auf der Registerkarte **Quelle** die erforderlichen Felder aus, und wählen Sie **Weiter** aus.
4. Wählen Sie auf der Registerkarte **Virtuelle Computer** die Liste der VMs aus, bei denen Sie die Replikation aktivieren möchten, und wählen Sie **Weiter** aus.
5. Die Option zum Auswählen einer Näherungsplatzierungsgruppe in der DR-Region wird angezeigt. Site Recovery bietet Ihnen auch die Option zur Verwendung einer neuen Näherungsplatzierungsgruppe, die für Sie erstellt wird, wenn Sie diese Standardoption verwenden möchten. 

   Wählen Sie die gewünschte Näherungsplatzierungsgruppe aus, und setzen Sie den Vorgang dann mit dem Aktivieren der Replikation fort.

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-group-a2a-2.png" alt-text="Screenshot der Auswahl zum Anpassen von Zieleinstellungen":::

Sie können Ihre Auswahl einer Näherungsplatzierungsgruppe in der DR-Region problemlos aktualisieren, nachdem die Replikation für die VM aktiviert wurde:

1. Erstellen Sie den virtuellen Computer. Wählen Sie im linken Bereich unter **Vorgänge** die Option **Notfallwiederherstellung** aus.
2. Wechseln Sie zum Bereich **Compute** und wählen Sie **Bearbeiten** aus.
3. Die Optionen zum Bearbeiten von mehreren Zieleinstellungen, einschließlich der Näherungsplatzierungsgruppe, werden angezeigt. Wählen Sie die Näherungsplatzierungsgruppe aus, in die die VM ein Failover durchführen soll. Wählen Sie dann **Speichern** aus.

### <a name="vmware-to-azure"></a>VMware zu Azure

Sie können eine Näherungsplatzierungsgruppe für die Ziel-VM einrichten, nachdem Sie die Replikation für die VM aktiviert haben. Sorgen Sie dafür, dass Sie die Näherungsplatzierungsgruppe in der Zielregion entsprechend Ihren Anforderungen separat erstellen. 

Sie können Ihre Auswahl einer Näherungsplatzierungsgruppe in der DR-Region problemlos aktualisieren, nachdem die Replikation für die VM aktiviert wurde:

1. Wählen Sie den gewünschten virtuellen Computer aus dem Tresor aus. Wählen Sie im linken Bereich unter **Vorgänge** die Option **Notfallwiederherstellung** aus.
2. Wechseln Sie zum Bereich **Compute und Netzwerk**, und wählen Sie **Bearbeiten** aus.
3. Die Optionen zum Bearbeiten von mehreren Zieleinstellungen, einschließlich der Näherungsplatzierungsgruppe, werden angezeigt. Wählen Sie die Näherungsplatzierungsgruppe aus, in die die VM ein Failover durchführen soll. Wählen Sie dann **Speichern** aus.

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-groups-update-v2a.png" alt-text="Screenshot der Auswahl von Compute- und Netzwerkressourcen für VMware zu Azure":::

### <a name="hyper-v-to-azure"></a>Hyper-V in Azure

Sie können eine Näherungsplatzierungsgruppe für die Ziel-VM einrichten, nachdem Sie die Replikation für die VM aktiviert haben. Sorgen Sie dafür, dass Sie die Näherungsplatzierungsgruppe in der Zielregion entsprechend Ihren Anforderungen separat erstellen. 

Sie können Ihre Auswahl einer Näherungsplatzierungsgruppe in der DR-Region problemlos aktualisieren, nachdem die Replikation für die VM aktiviert wurde:

1. Wählen Sie den gewünschten virtuellen Computer aus dem Tresor aus. Wählen Sie im linken Bereich unter **Vorgänge** die Option **Notfallwiederherstellung** aus.
2. Wechseln Sie zum Bereich **Compute und Netzwerk**, und wählen Sie **Bearbeiten** aus.
3. Die Optionen zum Bearbeiten von mehreren Zieleinstellungen, einschließlich der Näherungsplatzierungsgruppe, werden angezeigt. Wählen Sie die Näherungsplatzierungsgruppe aus, in die die VM ein Failover durchführen soll. Wählen Sie dann **Speichern** aus.

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-groups-update-h2a.png" alt-text="Screenshot der Auswahl von Compute- und Netzwerkressourcen für Hyper-V zu Azure":::

## <a name="set-up-disaster-recovery-for-vms-in-proximity-placement-groups-via-powershell"></a>Einrichten der Notfallwiederherstellung für VMs in Näherungsplatzierungsgruppen über PowerShell

### <a name="prerequisites"></a>Voraussetzungen 

- Stellen Sie sicher, dass Sie über das PowerShell-Modul „Az“ verfügen. Wenn Sie Azure PowerShell installieren oder aktualisieren müssen, führen Sie die [Schritte zum Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/install-az-ps) aus.
- Die Mindestversion von Azure PowerShell Az sollte 4.1.0 sein. Verwenden Sie den folgenden Befehl zum Überprüfen der aktuellen Version:

    ```
    Get-InstalledModule -Name Az
    ```

> [!NOTE]
> Sorgen Sie dafür, dass Sie die eindeutige ID der Ziel-Näherungsplatzierungsgruppe zur Hand haben. Der von Ihnen verwendete Befehl ist davon abhängig, ob Sie [eine neue Näherungsplatzierungsgruppe erstellen](../virtual-machines/windows/proximity-placement-groups.md#create-a-proximity-placement-group) oder [eine vorhandene Näherungsplatzierungsgruppe verwenden](../virtual-machines/windows/proximity-placement-groups.md#list-proximity-placement-groups).

### <a name="azure-to-azure"></a>Azure zu Azure

1. [Melden Sie sich bei Ihrem Konto an, und legen Sie Ihr Abonnement fest](./azure-to-azure-powershell.md#sign-in-to-your-microsoft-azure-subscription).
2. [Rufen Sie die Details des virtuellen Computers ab, den Sie replizieren möchten](./azure-to-azure-powershell.md#get-details-of-the-virtual-machine-to-be-replicated).
3. [Erstellen Sie Ihren Recovery Services-Tresor](./azure-to-azure-powershell.md#create-a-recovery-services-vault), und [legen Sie den Tresorkontext fest](./azure-to-azure-powershell.md#set-the-vault-context).
4. Bereiten Sie den Tresor zum Starten der Replikation des virtuellen Computers vor. Dieser Schritt umfasst das [Erstellen eines Azure Service Fabric-Objekts](./azure-to-azure-powershell.md#create-a-site-recovery-fabric-object-to-represent-the-primary-source-region) sowohl für die primäre Region als auch die Wiederherstellungsregionen.
5. [Erstellen Sie einen Site Recovery-Schutzcontainer](./azure-to-azure-powershell.md#create-a-site-recovery-protection-container-in-the-primary-fabric) sowohl für den primären Fabric und als auch die Wiederherstellungsfabrics.
6. [Erstellen einer Replikationsrichtlinie](./azure-to-azure-powershell.md#create-a-replication-policy).
7. [Erstellen Sie eine Schutzcontainerzuordnung zwischen dem primären Container und den Wiederherstellungsschutzcontainern](./azure-to-azure-powershell.md#create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container), und [erstellen Sie eine Schutzcontainerzuordnung für das Failback](./azure-to-azure-powershell.md#create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover).
8. [Erstellen Sie ein Cachespeicherkonto](./azure-to-azure-powershell.md#create-cache-storage-account-and-target-storage-account).
9. [Erstellen Sie die erforderlichen Netzwerkzuordnungen](./azure-to-azure-powershell.md#create-network-mappings).
10. Replizieren Sie einen virtuellen Azure-Computer mit verwalteten Datenträgern mithilfe des folgenden PowerShell-Cmdlets:

    ```azurepowershell
    #Get the resource group that the virtual machine must be created in when it's failed over.
    $RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

    #Specify replication properties for each disk of the VM that will be replicated (create disk replication configuration).
    #Make sure to replace the variable $OSdiskName with the OS disk name.

    #OS Disk
    $OSdisk = Get-AzDisk -DiskName $OSdiskName -ResourceGroupName "A2AdemoRG"
    $OSdiskId = $OSdisk.Id
    $RecoveryOSDiskAccountType = $OSdisk.Sku.Name
    $RecoveryReplicaDiskAccountType = $OSdisk.Sku.Name

    $OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $OSdiskId -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

    #Make sure to replace the variable $datadiskName with the data disk name.

    #Data disk
    $datadisk = Get-AzDisk -DiskName $datadiskName -ResourceGroupName "A2AdemoRG"
    $datadiskId1 = $datadisk[0].Id
    $RecoveryReplicaDiskAccountType = $datadisk[0].Sku.Name
    $RecoveryTargetDiskAccountType = $datadisk[0].Sku.Name

    $DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $datadiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

    #Create a list of disk replication configuration objects for the disks of the virtual machine that will be replicated.

    $diskconfigs = @()
    $diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

    #Start replication by creating a replication protected item. Use a GUID for the name of the replication protected item to ensure uniqueness of the name.

    $TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id
    ```

    Wenn Sie die Replikation für mehrere Datenträger aktivieren, verwenden Sie das folgende PowerShell-Cmdlet:

    ```azurepowershell
    #Get the resource group that the virtual machine must be created in when it's failed over.
    $RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

    #Specify replication properties for each disk of the VM that will be replicated (create disk replication configuration).
    #Make sure to replace the variable $OSdiskName with the OS disk name.

    #OS Disk
    $OSdisk = Get-AzDisk -DiskName $OSdiskName -ResourceGroupName "A2AdemoRG"
    $OSdiskId = $OSdisk.Id
    $RecoveryOSDiskAccountType = $OSdisk.Sku.Name
    $RecoveryReplicaDiskAccountType = $OSdisk.Sku.Name

    $OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $OSdiskId -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

    $diskconfigs = @()
    $diskconfigs += $OSDiskReplicationConfig

    #Data disk

    # Add data disks
    Foreach( $disk in $VM.StorageProfile.DataDisks)
    {
        $datadisk = Get-AzDisk -DiskName $datadiskName -ResourceGroupName "A2AdemoRG"
        $dataDiskId1 = $datadisk[0].Id
        $RecoveryReplicaDiskAccountType = $datadisk[0].Sku.Name
        $RecoveryTargetDiskAccountType = $datadisk[0].Sku.Name
        $DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
             -DiskId $dataDiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType `
             -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType
        $diskconfigs += $DataDisk1ReplicationConfig
    }

    #Start replication by creating a replication protected item. Use a GUID for the name of the replication protected item to ensure uniqueness of the name.

    $TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id
    ```

    Beim Aktivieren der Replikation zwischen Zonen mithilfe einer Näherungsplatzierungsgruppe wird der Befehl zum Starten der Replikation mit dem PowerShell-Cmdlet ausgetauscht:

    ```azurepowershell
    $TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id -RecoveryAvailabilityZone "2"
    ```

    Nachdem der Replikationsvorgang erfolgreich gestartet wurde, werden Daten des virtuellen Computers in die Wiederherstellungsregion repliziert.

    Zu Beginn des Replikationsprozesses wird zunächst durch Seeding eine Kopie der zu replizierenden Datenträger des virtuellen Computers in die Wiederherstellungsregion übernommen. Diese Phase wird als *erste Replikation* bezeichnet.

    Nachdem die erste Replikation abgeschlossen ist, beginnt die Phase der *differenziellen Synchronisierung*. An diesem Punkt ist der virtuelle Computer geschützt, und Sie können ein Testfailover dafür durchführen. Nach Abschluss der ersten Replikation wechselt der Replikationsstatus des replizierten Elements, das den virtuellen Computer darstellt, in den Status „Geschützt“.

    Überwachen Sie den Replikationsstatus und die Replikationsintegrität für den virtuellen Computer, indem Sie Details des ihm entsprechenden replikationsgeschützten Elements abrufen:

    ```azurepowershell
    Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
    ```

11. [Führen Sie ein Testfailover durch, überprüfen und bereinigen Sie es](./azure-to-azure-powershell.md#do-a-test-failover-validate-and-cleanup-test-failover).
12. [Führen Sie ein Failover für den virtuellen Computer durch](./azure-to-azure-powershell.md#fail-over-to-azure).
13. Mithilfe des folgenden PowerShell-Cmdlets können Sie den Schutz erneut aktivieren und ein Failback zur Quellregion durchführen:

    ```azurepowershell
    #Create a cache storage account for replication logs in the primary region.
    $WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage


    #Use the recovery protection container, the new cache storage account in West US, and the source region VM resource group. 
    Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure -ProtectionContainerMapping $WusToEusPCMapping -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.ResourceId -RecoveryProximityPlacementGroupId $vm.ProximityPlacementGroup.Id
    ```

14. [Deaktivieren Sie die Replikation](./azure-to-azure-powershell.md#disable-replication).

### <a name="vmware-to-azure"></a>VMware zu Azure

1. [Bereiten Sie Ihre lokalen VMware-Server](./vmware-azure-tutorial-prepare-on-premises.md) für die Notfallwiederherstellung in Azure vor.
2. [Melden Sie sich bei Ihrem Konto an, und legen Sie Ihr Abonnement fest](./vmware-azure-disaster-recovery-powershell.md#log-into-azure).
3. [Richten Sie einen Recovery Services-Tresor ein](./vmware-azure-disaster-recovery-powershell.md#set-up-a-recovery-services-vault), und [legen Sie einen Tresorkontext fest](./vmware-azure-disaster-recovery-powershell.md#set-the-vault-context).
4. [Überprüfen Sie Ihre Tresorregistrierung](./vmware-azure-disaster-recovery-powershell.md#validate-vault-registration).
5. [Erstellen einer Replikationsrichtlinie](./vmware-azure-disaster-recovery-powershell.md#create-a-replication-policy).
6. [Fügen Sie einen vCenter-Server hinzu, ermitteln Sie virtuelle Computer](./vmware-azure-disaster-recovery-powershell.md#add-a-vcenter-server-and-discover-vms), und [erstellen Sie Speicherkonten für die Replikation](./vmware-azure-disaster-recovery-powershell.md#create-storage-accounts-for-replication).
7. Replizieren Sie virtuelle VMware-Computer, und überprüfen Sie die Details mithilfe des folgenden PowerShell-Cmdlets:

   ```azurepowershell
   #Get the target resource group to be used.
   $ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

   #Get the target virtual network to be used.
   $RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg"

   #Get the protection container mapping for the replication policy named ReplicationPolicy.
   $PolicyMap = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

   #Get the protectable item that corresponds to the virtual machine CentOSVM1.
   $VM1 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

   # Enable replication for virtual machine CentOSVM1 by using the Az.RecoveryServices module 2.0.0 onward to replicate to managed disks.
   # The name specified for the replicated item needs to be unique within the protection container. Use a random GUID to ensure uniqueness.
   $Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" -RecoveryProximityPlacementGroupId $targetPpg.Id
   ```

8. Überprüfen Sie den Replikationsstatus und die Replikationsintegrität des virtuellen Computers mithilfe des Cmdlets `Get-ASRReplicationProtectedItem`:

   ```azurepowershell
   Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
   ```

9. [Konfigurieren Sie die Failovereinstellungen](./vmware-azure-disaster-recovery-powershell.md#configure-failover-settings).
10. [Führen Sie ein Testfailover durch](./vmware-azure-disaster-recovery-powershell.md#run-a-test-failover).
11. [Führen Sie ein Failover zu Azure durch](./vmware-azure-disaster-recovery-powershell.md#fail-over-to-azure).

### <a name="hyper-v-to-azure"></a>Hyper-V in Azure

1. [Bereiten Sie Ihre lokalen Hyper-V-Server](./hyper-v-prepare-on-premises-tutorial.md) für die Notfallwiederherstellung in Azure vor.
2. [Melden Sie sich bei Azure an.](./hyper-v-azure-powershell-resource-manager.md#step-1-sign-in-to-your-azure-account)
3. [Richten Sie Ihren Tresor ein](./hyper-v-azure-powershell-resource-manager.md#step-2-set-up-the-vault), und [legen Sie den Recovery Services-Tresorkontext fest](./hyper-v-azure-powershell-resource-manager.md#step-3-set-the-recovery-services-vault-context).
4. [Erstellen Sie einen Hyper-V-Standort](./hyper-v-azure-powershell-resource-manager.md#step-4-create-a-hyper-v-site).
5. [Installieren Sie den Anbieter und den Agent](./hyper-v-azure-powershell-resource-manager.md#step-5-install-the-provider-and-agent).
6. [Erstellen einer Replikationsrichtlinie](./hyper-v-azure-powershell-resource-manager.md#step-6-create-a-replication-policy).
7. Aktivieren Sie die Replikation anhand der folgenden Schritte: 
    
   a. Rufen Sie das schützbare Element ab, das dem zu schützenden virtuellen Computer entspricht:

      ```azurepowershell
      $VMFriendlyName = "Fabrikam-app"          #Name of the VM
      $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
      ```
   b. Schützen Sie den virtuellen Computer. Wenn an den geschützten virtuellen Computer mehrere Datenträger angefügt sind, geben Sie den Betriebssystemdatenträger mit dem Parameter `OSDiskName` an:
    
      ```azurepowershell
      $OSType = "Windows"          # "Windows" or "Linux"
      $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId     $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID -RecoveryProximityPlacementGroupId $targetPpg.Id
      ```
   c. Warten Sie, bis die virtuellen Computer nach der ersten Replikation einen geschützten Zustand erreicht haben. Abhängig von Faktoren wie der Menge der zu replizierenden Daten und der verfügbaren Upstreambandbreite zu Azure kann dieser Prozess eine Weile dauern. 
   
      Wenn ein geschützter Status besteht, werden `State` und `StateDescription` für den Auftrag so aktualisiert: 
    
      ```azurepowershell
      $DRjob = Get-AzRecoveryServicesAsrJob -Job $DRjob
      $DRjob | Select-Object -ExpandProperty State

      $DRjob | Select-Object -ExpandProperty StateDescription
      ```
   d. Aktualisieren Sie die Wiederherstellungseigenschaften (z. B. die Größe der VM-Rolle) und das Azure-Netzwerk, an das die Netzwerkkarte (NIC) des virtuellen Computers nach dem Failover angefügt werden soll:

      ```azurepowershell
      $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

      $VMFriendlyName = "Fabrikam-App"

      $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

      $UpdateJob = Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

      $UpdateJob = Get-AzRecoveryServicesAsrJob -Job $UpdateJob

      $UpdateJob | Select-Object -ExpandProperty state

      Get-AzRecoveryServicesAsrJob -Job $job | Select-Object -ExpandProperty state
      ```
8. [Führen Sie ein Testfailover durch](./hyper-v-azure-powershell-resource-manager.md#step-8-run-a-test-failover).


## <a name="next-steps"></a>Nächste Schritte

Informationen zum erneuten Schützen und Durchführen eines Failbacks für VMware in Azure finden Sie unter [Vorbereiten auf erneuten Schutz und Failback von VMware-VMs](./vmware-azure-prepare-failback.md).

Informationen zum Durchführen eines Failovers von Hyper-V zu Azure finden Sie unter [Durchführen eines Failovers vom lokalen Standort zu Azure](./site-recovery-failover.md). Informationen zum Durchführen eines Failbacks finden Sie unter [Durchführen eines Failbacks für Hyper-V-VMs](./hyper-v-azure-failback.md).

Weitere Informationen finden Sie unter [Failover in Site Recovery](site-recovery-failover.md).
