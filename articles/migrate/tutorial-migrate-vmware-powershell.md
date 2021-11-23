---
title: 'Migrieren virtueller VMware-Computer zu Azure (ohne Agent): PowerShell'
description: Hier erfahren Sie, wie Sie eine Migration virtueller VMware-Computer ohne Agent mit Azure Migrate über PowerShell durchführen.
author: rahulg1190
ms.author: rahugup
manager: bsiva
ms.topic: tutorial
ms.date: 08/20/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0f8b01e54fbb7173a6d9dfba8a99a9f9ffb1aeb9
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132292887"
---
# <a name="migrate-vmware-vms-to-azure-agentless---powershell"></a>Migrieren virtueller VMware-Computer zu Azure (ohne Agent): PowerShell

In diesem Artikel erfahren Sie, wie Sie ermittelte virtuelle VMware-Computer ohne Agent migrieren und dabei Azure PowerShell für [Azure Migrate: Servermigration](migrate-services-overview.md#azure-migrate-server-migration-tool) verwenden.

Folgendes wird vermittelt:

> [!div class="checklist"]
> * Abrufen ermittelter virtueller VMware-Computer in einem Azure Migrate-Projekt
> * Beginnen mit der Replikation von VMs
> * Aktualisieren von Eigenschaften für die Replikation virtueller Computer
> * Überwachen der Replikation
> * Führen Sie eine Testmigration aus, um sicherzustellen, dass alles wie erwartet funktioniert.
> * Durchführen einer vollständigen VM-Migration

> [!NOTE]
> In den Tutorials wird der einfachste Bereitstellungspfad für ein Szenario erläutert, damit Sie schnell einen Proof of Concept einrichten können. In den Tutorials werden nach Möglichkeit Standardoptionen verwendet und nicht alle möglichen Einstellungen und Pfade gezeigt.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen, bevor Sie beginnen.

## <a name="1-prerequisites"></a>1. Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen, sollten folgende Voraussetzungen erfüllt sein:

1. Absolvieren Sie [Tutorial: Ermitteln virtueller VMware-Computer mit der Serverbewertung](tutorial-discover-vmware.md), um Azure und VMware für die Migration vorzubereiten.
2. Absolvieren Sie [Tutorial: Bewerten von virtuellen VMware-Computern für die Migration zu virtuellen Azure-Computern](./tutorial-assess-vmware-azure-vm.md), bevor Sie sie zu Azure migrieren.
3. [Installieren Sie das Az-Modul von PowerShell.](/powershell/azure/install-az-ps)

## <a name="2-install-azure-migrate-powershell-module"></a>2. Installieren des Azure Migrate-PowerShell-Moduls

Das PowerShell-Modul für Azure Migrate ist als Teil von Azure PowerShell (`Az`) verfügbar. Führen Sie den `Get-InstalledModule -Name Az.Migrate`-Befehl aus, um zu überprüfen, ob das PowerShell-Modul für Azure Migrate auf Ihrem Computer installiert ist.  

## <a name="3-sign-in-to-your-microsoft-azure-subscription"></a>3. Anmelden bei Ihrem Microsoft Azure-Abonnement

Melden Sie sich mithilfe des Cmdlets [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) bei Ihrem Azure-Abonnement an.

```azurepowershell-interactive
Connect-AzAccount
```

### <a name="select-your-azure-subscription"></a>Auswählen des Azure-Abonnements

Rufen Sie mithilfe des Cmdlets [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) die Liste der Azure-Abonnements ab, auf die Sie Zugriff haben. Wählen Sie mithilfe des Cmdlets [Set-AzContext](/powershell/module/az.accounts/set-azcontext) das Azure-Abonnement aus, das das zu verwendende Azure Migrate-Projekt enthält.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="4-retrieve-the-azure-migrate-project"></a>4. Abrufen des Azure Migrate-Projekts

Ein Azure Migrate-Projekt wird zum Speichern von Ermittlungs-, Bewertungs- und Migrationsmetadaten verwendet, die in der von Ihnen bewerteten oder migrierten Umgebung gesammelt werden.
In einem Projekt können Sie ermittelte Ressourcen nachverfolgen, Bewertungen orchestrieren und Migrationen durchführen.

Im Rahmen der Vorbereitung haben Sie bereits ein Azure Migrate-Projekt erstellt. Verwenden Sie das Cmdlet [Get-AzMigrateProject](/powershell/module/az.migrate/get-azmigrateproject), um Details zu einem Azure Migrate-Projekt abzurufen. Sie müssen den Namen des Azure Migrate-Projekts (`Name`) und den Namen der Ressourcengruppe des Azure Migrate-Projekts (`ResourceGroupName`) angeben.

```azurepowershell-interactive
# Get resource group of the Azure Migrate project
$ResourceGroup = Get-AzResourceGroup -Name MyResourceGroup
```
```azurepowershell-interactive
# Get details of the Azure Migrate project
$MigrateProject = Get-AzMigrateProject -Name MyMigrateProject -ResourceGroupName $ResourceGroup.ResourceGroupName
```
```azurepowershell-interactive
# View Azure Migrate project details
Write-Output $MigrateProject
```

## <a name="5-retrieve-discovered-vms-in-an-azure-migrate-project"></a>5. Abrufen ermittelter virtueller Computer in einem Azure Migrate-Projekt

Von Azure Migrate wird eine einfache [Azure Migrate-Appliance](migrate-appliance-architecture.md) verwendet. Die Azure Migrate-Appliance wurde im Rahmen der Vorbereitung als virtueller VMware-Computer bereitgestellt.

Geben Sie zum Abrufen eines bestimmten virtuellen VMware-Computers in einem Azure Migrate-Projekt den Namen des Azure Migrate-Projekts (`ProjectName`), die Ressourcengruppe des Azure Migrate-Projekts (`ResourceGroupName`) und den Namen des virtuellen Computers (`DisplayName`) an.


```azurepowershell-interactive
# Get a specific VMware VM in an Azure Migrate project
$DiscoveredServer = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -DisplayName MyTestVM | Format-Table DisplayName, Name, Type

# View discovered server details
Write-Output $DiscoveredServer
```

Dieser virtuelle Computer wird im Rahmen dieses Tutorials migriert.

Mithilfe der Parameter `ProjectName` und `ResourceGroupName` können Sie auch alle VMware-VMs in einem Azure Migrate-Projekt abrufen.

```azurepowershell-interactive
# Get all VMware VMs in an Azure Migrate project
$DiscoveredServers = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName
```

Wenn ein Azure Migrate-Projekt mehrere Appliances enthält, können Sie die Parameter `ProjectName`, `ResourceGroupName` und `ApplianceName` verwenden, um alle ermittelten VMs unter Verwendung einer bestimmten Azure Migrate-Appliance abzurufen.

```azurepowershell-interactive
# Get all VMware VMs discovered by an Azure Migrate Appliance in an Azure Migrate project
$DiscoveredServers = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -ApplianceName MyMigrateAppliance

```

## <a name="6-initialize-replication-infrastructure"></a>6. Initialisieren der Replikationsinfrastruktur

[Azure Migrate: Servermigration](migrate-services-overview.md#azure-migrate-server-migration-tool) nutzt mehrere Azure-Ressourcen für die VM-Migration. Folgende Ressourcen werden von der Servermigration in der gleichen Ressourcengruppe bereitgestellt, in der sich auch das Projekt befindet:

- **Service Bus**: Bei der Servermigration wird der Service Bus verwendet, um Nachrichten zur Replikationsorchestrierung an die Appliance zu senden.
- **Gatewayspeicherkonto**: Bei der Servermigration wird das Gatewayspeicherkonto verwendet, um Statusinformationen zu den replizierten VMs zu speichern.
- **Protokollspeicherkonto**: Die Azure Migrate-Appliance lädt Replikationsprotokolle für VMs in ein Protokollspeicherkonto hoch. Azure Migrate wendet die Replikationsinformationen auf die verwalteten Replikatdatenträger an.
- **Schlüsseltresor**: Von der Azure Migrate-Appliance wird der Schlüsseltresor verwendet, um Verbindungszeichenfolgen für den Service Bus zu verwalten, und Zugriffsschlüssel werden für die Speicherkonten genutzt, die bei der Replikation eingesetzt werden.

Führen Sie vor dem Replizieren der ersten VM im Azure Migrate-Projekt den folgenden Befehl aus, um die Replikationsinfrastruktur bereitzustellen. Durch diesen Befehl werden die oben genannten Ressourcen bereitgestellt und konfiguriert, damit Sie mit der Migration Ihrer VMware-VMs beginnen können.

> [!NOTE]
> Pro Azure Migrate-Projekt werden nur Migrationen in eine einzelne Azure-Region unterstützt. Nachdem Sie dieses Skript ausgeführt haben, kann die Zielregion für die Migration Ihrer virtuellen VMware-Computer nicht mehr geändert werden.
> Wenn Sie in Ihrem Azure Migrate-Projekt eine neue Appliance konfigurieren, müssen Sie den Befehl `Initialize-AzMigrateReplicationInfrastructure` ausführen.

Im Artikel wird die Replikationsinfrastruktur initialisiert, um die Migration der virtuellen Computer in die Region `Central US` zu ermöglichen.

```azurepowershell-interactive
# Initialize replication infrastructure for the current Migrate project
Initialize-AzMigrateReplicationInfrastructure -ResourceGroupName $ResourceGroup.ResourceGroupName -ProjectName $MigrateProject. Name -Scenario agentlessVMware -TargetRegion "CentralUS" 

```

## <a name="7-replicate-vms"></a>7. Replizieren von VMs

Nach Abschluss der Ermittlung und der Initialisierung der Replikationsinfrastruktur können Sie mit der Replikation virtueller VMware-Computer in Azure beginnen. Sie können bis zu 500 Replizierungen gleichzeitig ausführen.

Die Replikationseigenschaften können wie folgt angegeben werden:

**Parameter** | **Typ** | **Beschreibung**
--- | --- | ---
 Zielabonnement und -ressourcengruppe |  Obligatorisch. | Geben Sie mithilfe des Parameters `TargetResourceGroupId` das Abonnement und die Ressourcengruppe an, zu dem bzw. der virtuelle Computer migriert werden soll.
 Virtuelles Zielnetzwerk und -subnetz | Obligatorisch. | Geben Sie mithilfe der Parameter `TargetNetworkId` und `TargetSubnetName` die ID der Azure Virtual Network-Instanz und den Namen des Subnetzes ein, zu der bzw. zu dem der virtuelle Computer migriert werden soll.
 Computer-ID | Obligatorisch. | Geben Sie die ID des ermittelten Computers an, der repliziert und migriert werden muss. Geben Sie mithilfe von `InputObject` das ermittelte VM-Objekt für die Replikation an.  
 Name des virtuellen Zielcomputers | Obligatorisch. | Geben Sie mithilfe des Parameters `TargetVMName` den Namen des zu erstellenden virtuellen Azure-Computers an. 
 Größe der Ziel-VM | Obligatorisch. | Geben Sie mithilfe des Parameters `TargetVMSize` die Größe des virtuellen Azure-Computers an, der für den replizierenden virtuellen Computer verwendet werden soll. Wenn Sie also beispielsweise eine VM zu „D2_v2“ in Azure migrieren möchten, geben Sie für `TargetVMSize` den Wert „Standard_D2_v2“ an. 
 Lizenz | Obligatorisch. | Wenn Sie den Azure-Hybridvorteil für Ihre Windows Server-Computer nutzen möchten, die durch aktive Software Assurance- oder Windows Server-Abonnements abgedeckt sind, geben Sie für den Parameter `LicenseType` den Wert **WindowsServer** an. Geben Sie andernfalls den Wert **NoLicenseType** an. 
 Betriebssystemdatenträger | Obligatorisch. | Geben Sie den eindeutigen Bezeichner des Datenträgers an, auf dem sich der Bootloader und das Installationsprogramm des Betriebssystems befinden. Die zu verwendende Datenträger-ID ist die UUID-Eigenschaft (Unique Identifier, eindeutiger Bezeichner) für den Datenträger, die mithilfe des Cmdlets [Get-AzMigrateDiscoveredServer](/powershell/module/az.migrate/get-azmigratediscoveredserver) abgerufen wurde.
 Datenträgertyp | Obligatorisch. | Geben Sie den Namen des zu erstellenden Lastenausgleichs an. 
 Redundanz bei der Infrastruktur | Optional | Geben Sie die Option für die Infrastrukturredundanz wie folgt an: <br/><br/> - **Verfügbarkeitszone**, um den migrierten Computer an eine bestimmte Verfügbarkeitszone in der Region anzuheften. Verteilen Sie mit dieser Option Server, die eine Anwendungsebene mit mehreren Knoten bilden, über Verfügbarkeitszonen. Diese Option ist nur verfügbar, wenn die für die Migration ausgewählte Zielregion Verfügbarkeitszonen unterstützt. Falls Sie Verfügbarkeitszonen verwenden möchten, geben Sie für den Parameter `TargetAvailabilityZone` den Wert der Verfügbarkeitszone an. <br/> - **Verfügbarkeitsgruppe**, um den migrierten Computer in einer Verfügbarkeitsgruppe zu platzieren. Um diese Option verwenden zu können, muss die ausgewählte Zielressourcengruppe über mindestens eine Verfügbarkeitsgruppe verfügen. Falls Sie eine Verfügbarkeitsgruppe verwenden möchten, geben Sie für den Parameter `TargetAvailabilitySet` die ID der Verfügbarkeitsgruppe an. 
 Speicherkonto für die Startdiagnose | Optional | Wenn Sie ein Startdiagnose-Speicherkonto verwenden möchten, geben Sie die ID für den Parameter `TargetBootDiagnosticStorageAccount` an. <br/> - Das für Startdiagnosen verwendete Speicherkonto sollte sich in dem Abonnement befinden, in das Sie auch Ihre virtuellen Computer migrieren. <br/> - Standardmäßig ist für diesen Parameter kein Wert festgelegt. 
 `Tags` | Optional | Fügen Sie Ihren migrierten virtuellen Computern, Datenträgern und NICs Tags hinzu. <br/>  Verwenden Sie (`Tag`), um virtuellen Computern, Datenträgern und NICs Tags hinzuzufügen. <br/> oder <br/> Verwenden Sie (`VMTag`), um Tags zu Ihren migrierten virtuellen Computern hinzuzufügen.<br/> Verwenden Sie (`DiskTag`), um Tags zu Datenträgern hinzuzufügen. <br/> Verwenden Sie (`NicTag`), um Tags zu Netzwerkschnittstellen hinzuzufügen. <br/> Fügen Sie beispielsweise die erforderlichen Tags einer Variablen $tags hinzu, und übergeben Sie die Variable im erforderlichen Parameter.  $tags = @{Organization="Contoso"}



### <a name="replicate-vms-with-all-disks"></a>Replizieren von virtuellen Computern mit allen Datenträgern

In diesem Tutorial werden alle Datenträger des ermittelten virtuellen Computers repliziert, und es wird ein neuer Name für den virtuellen Computer in Azure angegeben. Der erste Datenträger des ermittelten Servers wird als Betriebssystemdatenträger festgelegt, und alle Datenträger werden als „HDD Standard“ migriert. Der Betriebssystemdatenträger enthält den Bootloader und das Installationsprogramm des Betriebssystems. Das Cmdlet gibt einen Auftrag zurück, der zur Überwachung des Vorgangsstatus nachverfolgt werden kann.

```azurepowershell-interactive
# Retrieve the resource group that you want to migrate to
$TargetResourceGroup = Get-AzResourceGroup -Name MyTargetResourceGroup
```

```azurepowershell-interactive
# Retrieve the Azure virtual network and subnet that you want to migrate to
$TargetVirtualNetwork = Get-AzVirtualNetwork -Name MyVirtualNetwork
```
```azurepowershell-interactive
# Start replication for a discovered VM in an Azure Migrate project
$MigrateJob =  New-AzMigrateServerReplication -InputObject $DiscoveredServer -TargetResourceGroupId $TargetResourceGroup.ResourceId -TargetNetworkId $TargetVirtualNetwork.Id -LicenseType NoLicenseType -OSDiskID $DiscoveredServer.Disk[0].Uuid -TargetSubnetName $TargetVirtualNetwork.Subnets[0].Name -DiskType Standard_LRS -TargetVMName MyMigratedTestVM -TargetVMSize Standard_DS2_v2
```
```azurepowershell-interactive
# Track job status to check for completion
while (($MigrateJob.State -eq 'InProgress') -or ($MigrateJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $MigrateJob.State
```

### <a name="replicate-vms-with-select-disks"></a>Replizieren von virtuellen Computern mit ausgewählten Datenträgern

Die Datenträger der ermittelten VM können auch selektiv repliziert werden. Verwenden Sie dazu das Cmdlet [New-AzMigrateDiskMapping](/powershell/module/az.migrate/new-azmigratediskmapping), und geben Sie es als Eingabe für den Parameter `DiskToInclude` im Cmdlet [New-AzMigrateServerReplication](/powershell/module/az.migrate/new-azmigrateserverreplication) an. Sie können auch das Cmdlet [New-AzMigrateDiskMapping](/powershell/module/az.migrate/new-azmigratediskmapping) verwenden, um für die einzelnen zu replizierenden Datenträger jeweils andere Zieldatenträgertypen anzugeben.

Geben Sie Werte für die folgenden Parameter des Cmdlet [New-AzMigrateDiskMapping](/powershell/module/az.migrate/new-azmigratediskmapping) an.

- **DiskId**: Geben Sie den eindeutigen Bezeichner für den zu migrierenden Datenträger an. Die zu verwendende Datenträger-ID ist die UUID-Eigenschaft (Unique Identifier, eindeutiger Bezeichner) für den Datenträger, die mithilfe des Cmdlets [Get-AzMigrateDiscoveredServer](/powershell/module/az.migrate/get-azmigratediscoveredserver) abgerufen wurde.
- **IsOSDisk**: Geben Sie „true“ an, wenn es sich bei dem zu migrierenden Datenträger um den Betriebssystemdatenträger des virtuellen Computers handelt. Geben Sie andernfalls „false“ an.
- **DiskType**: Geben Sie die Art des Datenträgers an, die in Azure verwendet werden soll.

Im folgenden Beispiel werden nur zwei Datenträger des ermittelten virtuellen Computers repliziert. Der Betriebssystemdatenträger wird angegeben, und es werden unterschiedliche Datenträgertypen für die zu replizierenden Datenträger verwendet. Mit dem Cmdlet wird ein Auftrag zurückgegeben, der zur Überwachung des Vorgangsstatus nachverfolgt werden kann.

```azurepowershell-interactive
# View disk details of the discovered server
Write-Output $DiscoveredServer.Disk
```

```azurepowershell-interactive
# Create a new disk mapping for the disks to be replicated
$DisksToReplicate = @()
$OSDisk = New-AzMigrateDiskMapping -DiskID $DiscoveredServer.Disk[0].Uuid -DiskType StandardSSD_LRS -IsOSDisk true
$DataDisk = New-AzMigrateDiskMapping -DiskID $DiscoveredServer.Disk[1].Uuid -DiskType Premium_LRS -IsOSDisk false

$DisksToReplicate += $OSDisk
$DisksToReplicate += $DataDisk
```

```azurepowershell-interactive
# Retrieve the resource group that you want to migrate to
$TargetResourceGroup = Get-AzResourceGroup -Name MyTargetResourceGroup
```

```azurepowershell-interactive
# Retrieve the Azure virtual network and subnet that you want to migrate to
$TargetVirtualNetwork = Get-AzVirtualNetwork -Name MyVirtualNetwork
```

```azurepowershell-interactive
# Start replication for the VM
$MigrateJob =  New-AzMigrateServerReplication -InputObject $DiscoveredServer -TargetResourceGroupId $TargetResourceGroup.ResourceId -TargetNetworkId $TargetVirtualNetwork.Id -LicenseType NoLicenseType -DiskToInclude $DisksToReplicate -TargetSubnetName $TargetVirtualNetwork.Subnets[0].Name -TargetVMName MyMigratedTestVM -TargetVMSize Standard_DS2_v2
```

```azurepowershell-interactive
# Track job status to check for completion
while (($MigrateJob.State -eq 'InProgress') -or ($MigrateJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
# Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $MigrateJob.State
```

## <a name="8-monitor-replication"></a>8. Überwachen der Replikation

Die Replikation wird wie folgt durchgeführt:

- Nachdem der Auftrag „Replikation starten“ erfolgreich abgeschlossen wurde, beginnen die Computer mit der ersten Replikation in Azure.
- Während der ersten Replikation wird eine VM-Momentaufnahme erstellt. Datenträgerdaten der Momentaufnahme werden auf verwalteten Replikatdatenträgern in Azure repliziert.
- Nach Abschluss der ersten Replikation beginnt die Deltareplikation. Inkrementelle Änderungen an lokalen Datenträgern werden regelmäßig auf den Replikatdatenträgern in Azure repliziert.

Verfolgen Sie den Status der Replikation mithilfe des Cmdlets [Get-AzMigrateServerReplication](/powershell/module/az.migrate/get-azmigrateserverreplication) nach.



```azurepowershell-interactive
# List replicating VMs and filter the result for selecting a replicating VM. This cmdlet will not return all properties of the replicating VM.
$ReplicatingServer = Get-AzMigrateServerReplication -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -MachineName MyTestVM
```
```azurepowershell-interactive
# Retrieve all properties of a replicating VM 
$ReplicatingServer = Get-AzMigrateServerReplication -TargetObjectID $ReplicatingServer.Id
```

Sie können die Eigenschaften **MigrationState** und **MigrationStateDescription** in der Ausgabe nachverfolgen.

- Bei der ersten Replikation haben die Eigenschaften **MigrationState** und **MigrationStateDescription** den Wert `InitialSeedingInProgress` bzw. `Initial replication`.
- Während der Deltareplikation haben die Eigenschaften **MigrationState** und **MigrationStateDescription** den Wert `Replicating` bzw. `Ready to migrate`.
- Nach Abschluss der Migration haben die Eigenschaften **MigrationState** und **MigrationStateDescription** den Wert `Migration succeeded` bzw. `Migrated`.

```Output
AllowedOperation            : {DisableMigration, TestMigrate, Migrate}
CurrentJobId                : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationJobs/None
CurrentJobName              : None
CurrentJobStartTime         : 1/1/1753 1:01:01 AM
EventCorrelationId          : 9d435c55-4660-41a5-a8ed-dd74213d85fa
Health                      : Normal
HealthError                 : {}
Id                          : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationFabrics/xxx/replicationProtectionContainers/xxx/
                              replicationMigrationItems/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_5009e941-3e40-
                              39b2-1e14-f90584522703
LastTestMigrationStatus     :
LastTestMigrationTime       :
Location                    :
MachineName                 : MyTestVM
MigrationState              : InitialSeedingInProgress
MigrationStateDescription   : Initial replication
Name                        : 10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_5009e941-3e40-39b2-1e14-f90584522703
PolicyFriendlyName          : xxx
PolicyId                    : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationPolicies/xxx
ProviderSpecificDetail      : Microsoft.Azure.PowerShell.Cmdlets.Migrate.Models.Api20180110.VMwareCbtMigrationDetails
TestMigrateState            : None
TestMigrateStateDescription : None
Type                        : Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems
```

Details zum Replikationsfortschritt erhalten Sie durch Ausführen des folgenden Cmdlets:

```azurepowershell-interactive
Write-Output $replicatingserver.ProviderSpecificDetail
```

Sie können den Fortschritt der ersten Replikation anhand der Eigenschaft **InitialSeedingProgressPercentage** nachverfolgen.

```Output
    "DataMoverRunAsAccountId": "/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.OffAzure/VMwareSites/xxx/runasaccounts/xxx",
    "FirmwareType":  "BIOS",
    "InitialSeedingProgressPercentage": 20,
    "InstanceType":  "VMwareCbt",
    "LastRecoveryPointReceived":  "\/Date(1601733591427)\/",
    "LicenseType":  "NoLicenseType",
    "MigrationProgressPercentage":  null,
    "MigrationRecoveryPointId":  null,
    "OSType":  "Windows",
    "PerformAutoResync":  "true",
```

Die Replikation wird wie folgt durchgeführt:

- Nachdem der Auftrag „Replikation starten“ erfolgreich abgeschlossen wurde, beginnen die Computer mit der ersten Replikation in Azure.
- Während der ersten Replikation wird eine VM-Momentaufnahme erstellt. Datenträgerdaten der Momentaufnahme werden auf verwalteten Replikatdatenträgern in Azure repliziert.
- Nach Abschluss der ersten Replikation beginnt die Deltareplikation. Inkrementelle Änderungen an lokalen Datenträgern werden regelmäßig auf den Replikatdatenträgern in Azure repliziert.

## <a name="9-retrieve-the-status-of-a-job"></a>9. Abrufen des Status eines Auftrags

Sie können den Status eines Auftrags mithilfe des Cmdlets [Get-AzMigrateJob](/powershell/module/az.migrate/get-azmigratejob) überwachen.

```azurepowershell-interactive
# Retrieve the updated status for a job
$job = Get-AzMigrateJob -InputObject $job
```

## <a name="10-update-properties-of-a-replicating-vm"></a>10. Aktualisieren von Eigenschaften eines replizierenden virtuellen Computers

[Azure Migrate: Servermigration](migrate-services-overview.md#azure-migrate-server-migration-tool) ermöglicht das Ändern von Zieleigenschaften wie Name, Größe, Ressourcengruppe und NIC-Konfiguration für einen replizierenden virtuellen Computer.

Folgende Eigenschaften können für einen virtuellen Computer aktualisiert werden:


**Parameter** | **Typ** | **Beschreibung**
--- | --- | ---
VM-Name | Optional | Geben Sie mithilfe des Parameters `TargetVMName` den Namen des zu erstellenden virtuellen Azure-Computers an. 
Größe des virtuellen Computers | Optional | Geben Sie mithilfe des Parameters `TargetVMSize` die Größe des virtuellen Azure-Computers an, der für den replizierenden virtuellen Computer verwendet werden soll. Wenn Sie also beispielsweise eine VM zu „D2_v2“ in Azure migrieren möchten, geben Sie für `TargetVMSize` den Wert `Standard_D2_v2` an.
Virtual Network | Optional | Geben Sie mithilfe des Parameters `TargetNetworkId` die ID der Azure Virtual Network-Instanz ein, zu der der virtuelle Computer migriert werden soll. 
Ressourcengruppe | Optional | Die NIC-Konfiguration kann mithilfe des Cmdlets [New-AzMigrateNicMapping](/powershell/module/az.migrate/new-azmigratenicmapping) angegeben werden. Das Objekt wird dann als Eingabe an den Parameter `NicToUpdate` im Cmdlet [Set-AzMigrateServerReplication](/powershell/module/az.migrate/set-azmigrateserverreplication) übergeben. <br/><br/> - **Ändern der IP-Zuordnung:** Wenn Sie eine statische IP-Adresse für eine NIC angeben möchten, geben Sie mithilfe des Parameters `TargetNicIP` die IPv4-Adresse an, die als statische IP für den virtuellen Computer verwendet werden soll. Wenn für eine NIC dynamisch eine IP-Adresse zugewiesen werden soll, geben Sie für den Parameter **TargetNicIP** den Wert `auto` an. <br/> - Verwenden Sie für den Parameter `TargetNicSelectionType` den Wert `Primary`, `Secondary` oder `DoNotCreate`, um anzugeben, ob die NIC als primäre oder sekundäre NIC fungieren oder nicht auf dem migrierten virtuellen Computer erstellt werden soll. Es kann nur eine einzelne NIC als primäre NIC für den virtuellen Computer angegeben werden. <br/> - Wenn eine NIC als primäre NIC fungieren soll, müssen Sie auch die anderen NICs angeben, die als sekundäre NICs fungieren oder nicht auf dem migrierten virtuellen Computer erstellt werden sollen. <br/> - Wenn Sie das Subnetz für die NIC ändern möchten, geben Sie mithilfe des Parameters `TargetNicSubnet` den Namen des Subnetzes an.
Netzwerkschnittstelle | Optional | Geben Sie mithilfe des Parameters `TargetVMName` den Namen des zu erstellenden virtuellen Azure-Computers an. 
Verfügbarkeitszone | Optional | Falls Sie Verfügbarkeitszonen verwenden möchten, geben Sie für den Parameter `TargetAvailabilityZone` den Wert der Verfügbarkeitszone an. 
Verfügbarkeitsgruppe | Optional | Falls Sie eine Verfügbarkeitsgruppe verwenden möchten, geben Sie für den Parameter `TargetAvailabilitySet` die ID der Verfügbarkeitsgruppe an. 
`Tags` | Optional | Verwenden Sie zum Aktualisieren von Tags die Parameter [`UpdateTag`] oder [`UpdateVMTag`], [`UpdateDiskTag`], [`UpdateNicTag`] und [`UpdateTagOperation`] oder [`UpdateVMTagOperation`], [`UpdateDiskTagOperation`], [`UpdateNicTagOperation`] für den Typ des Tagaktualisierungsvorgangs.   Der Tagaktualisierungsvorgang übernimmt die folgenden Werte: „Merge“, „Delete“ und „Replace“. <br/> Verwenden Sie [`UpdateTag`], um alle Tags auf virtuellen Computern, Datenträgern und NICs zu aktualisieren. <br/> Verwenden Sie [`UpdateVMTag`], um Tags für virtuelle Computer zu aktualisieren. <br/> Verwenden Sie [`UpdateDiskTag`], um Tags für Datenträger zu aktualisieren. <br/> Verwenden Sie [`UpdateNicTag`], um Tags für NICs zu aktualisieren. <br/> Verwenden Sie [`UpdateTagOperation`], um den Vorgang für alle Tags von virtuellen Computern, Datenträgern und NICs zu aktualisieren. <br/>  Verwenden Sie [`UpdateVMTagOperation`], um Tags für virtuelle Computer zu aktualisieren. <br/> Verwenden Sie [`UpdateDiskTagOperation`], um Tags für Datenträger zu aktualisieren. <br/> Verwenden Sie [`UpdateNicTagOperation`], um Tags für NICs zu aktualisieren. <br/> <br/> Die *replace*-Option ersetzt den gesamten Satz vorhandener Tags durch einen neuen Satz. <br/> Die *merge*-Option ermöglicht das Hinzufügen von Tags mit neuen Namen und das Aktualisieren der Werte von Tags mit vorhandenen Namen. <br/> Die *delete*-Option ermöglicht das selektive Löschen von Tags basierend auf bestimmten Namen oder Name-Wert-Paaren. 
Datenträger | Optional | Für den Betriebssystemdatenträger: <br/> Aktualisieren Sie den Namen des Betriebssystemdatenträgers mit dem [`TargetDiskName`]-Parameter.  <br/><br/> Zum Aktualisieren mehrerer Datenträger: <br/>  Verwenden Sie [Set-AzMigrateDiskMapping](/powershell/module/az.migrate/set-azmigratediskmapping), um die Datenträgernamen auf eine Variable *$DiskMapping* festzulegen, verwenden Sie dann den [`DiskToUpdate`]-Parameter, und übergeben Sie die Variable. <br/> <br/> **Hinweis**: Die in [Set-AzMigrateDiskMapping](/powershell/module/az.migrate/set-azmigratediskmapping) zu verwendende Datenträger-ID ist die UUID-Eigenschaft (Unique Identifier, eindeutiger Bezeichner) für den Datenträger, die mithilfe des Cmdlets  [Get-AzMigrateDiscoveredServer](/powershell/module/az.migrate/get-azmigratediscoveredserver) abgerufen wurde. 
NIC-Name | Optional | Verwenden Sie [New-AzMigrateNicMapping](/powershell/module/az.migrate/new-azmigratenicmapping), um die NIC-Namen auf eine Variable *$NICMapping* festzulegen, verwenden Sie dann den [`NICToUpdate`]-Parameter, und übergeben Sie die Variable.

Das Cmdlet [Get-AzMigrateServerReplication](/powershell/module/az.migrate/get-azmigrateserverreplication) gibt einen Auftrag zurück, der zur Überwachung des Vorgangsstatus nachverfolgt werden kann.

```azurepowershell-interactive
# List replicating VMs and filter the result for selecting a replicating VM. This cmdlet will not return all properties of the replicating VM.
$ReplicatingServer = Get-AzMigrateServerReplication -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -MachineName MyTestVM
```

```azurepowershell-interactive
# Retrieve all properties of a replicating VM 
$ReplicatingServer = Get-AzMigrateServerReplication -TargetObjectID $ReplicatingServer.Id

# View NIC details of the replicating server
Write-Output $ReplicatingServer.ProviderSpecificDetail.VMNic
```

Im folgenden Beispiel wird die NIC-Konfiguration wie folgt geändert: Die erste NIC wird als primäre NIC konfiguriert, und ihr wird eine statische IP-Adresse zugewiesen. Die zweite NIC wird bei der Migration nicht erstellt. Weiterhin werden Name und Größe des virtuellen Zielcomputers aktualisiert sowie die NIC-Namen angepasst.

```azurepowershell-interactive
# Specify the NIC properties to be updated for a replicating VM.
$NicMapping = @()
$NicMapping1 = New-AzMigrateNicMapping -NicId $ReplicatingServer.ProviderSpecificDetail.VMNic[0].NicId -TargetNicIP ###.###.###.### -TargetNicSelectionType Primary TargetNicName "ContosoNic_1"
$NicMapping2 = New-AzMigrateNicMapping -NicId $ReplicatingServer.ProviderSpecificDetail.VMNic[1].NicId -TargetNicSelectionType DoNotCreate - TargetNicName "ContosoNic_2"

$NicMapping += $NicMapping1
$NicMapping += $NicMapping2
```
```azurepowershell-interactive
# Update the name, size and NIC configuration of a replicating server
$UpdateJob = Set-AzMigrateServerReplication -InputObject $ReplicatingServer -TargetVMSize Standard_DS13_v2 -TargetVMName MyMigratedVM -NicToUpdate $NicMapping
```

Im folgenden Beispiel wird der Datenträgername angepasst.

```azurepowershell-interactive
# Customize the Disk names for a replicating VM
$OSDisk = Set-AzMigrateDiskMapping -DiskID "6000C294-1217-dec3-bc18-81f117220424" -DiskName "ContosoDisk_1" 
$DataDisk1= Set-AzMigrateDiskMapping -DiskID "6000C292-79b9-bbdc-fb8a-f1fa8dbeff84" -DiskName "ContosoDisk_2" 
$DiskMapping = $OSDisk, $DataDisk1 
```

```azurepowershell-interactive
# Update the disk names for a replicating server
$UpdateJob = Set-AzMigrateServerReplication InputObject $ReplicatingServer DiskToUpdate $DiskMapping 
 ```

Im folgenden Beispiel werden den replizierenden VMs Tags hinzugefügt.

```azurepowershell-interactive
# Update all tags across virtual machines, disks, and NICs.
Set-azmigrateserverreplication UpdateTag $UpdateTag UpdateTagOperation Merge/Replace/Delete

# Update virtual machines tags
Set-azmigrateserverreplication UpdateVMTag $UpdateVMTag UpdateVMTagOperation Merge/Replace/Delete 
```
Verwenden Sie das folgende Beispiel, um den Auftragsstatus nachzuverfolgen.

```azurepowershell-interactive
# Track job status to check for completion
while (($UpdateJob.State -eq 'InProgress') -or ($UpdateJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $UpdateJob = Get-AzMigrateJob -InputObject $UpdateJob
}
# Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $UpdateJob.State
```

## <a name="11-run-a-test-migration"></a>11. Ausführen einer Testmigration

Wenn die Deltareplikation beginnt, können Sie zunächst eine Testmigration für die virtuellen Computer ausführen, bevor eine vollständige Migration zu Azure erfolgt. Wir empfehlen dringend, diese Testmigration mindestens einmal für jeden Computer auszuführen, bevor Sie ihn migrieren. Mit dem Cmdlet wird ein Auftrag zurückgegeben, der zur Überwachung des Vorgangsstatus nachverfolgt werden kann.

- Bei einer Testmigration wird überprüft, ob die Migration wie erwartet funktioniert. Der lokale Computer wird dadurch nicht beeinträchtigt, bleibt betriebsbereit und wird weiter repliziert.
- Mit der Testmigration wird die Migration simuliert, indem eine Azure-VM mit replizierten Daten erstellt wird (normalerweise per Migration zu einem nicht für die Produktion bestimmten VNET unter Ihrem Azure-Abonnement).
- Sie können den replizierten virtuellen Azure-Testcomputer verwenden, um die Migration zu überprüfen, App-Tests durchzuführen und Probleme zu beheben, bevor die vollständige Migration erfolgt.

Wählen Sie die Azure Virtual Network-Instanz aus, die zum Testen verwendet werden soll, indem Sie mithilfe des Parameters `TestNetworkID` die ID des virtuellen Netzwerks angeben.

```azurepowershell-interactive
# Retrieve the Azure virtual network created for testing
$TestVirtualNetwork = Get-AzVirtualNetwork -Name MyTestVirtualNetwork
```
```azurepowershell-interactive
# Start test migration for a replicating server
$TestMigrationJob = Start-AzMigrateTestMigration -InputObject $ReplicatingServer -TestNetworkID $TestVirtualNetwork.Id
```
```azurepowershell-interactive
# Track job status to check for completion
while (($TestMigrationJob.State -eq 'InProgress') -or ($TestMigrationJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $TestMigrationJob = Get-AzMigrateJob -InputObject $TestMigrationJob
}
# Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $TestMigrationJob.State
```

Bereinigen Sie die Testmigration nach Abschluss des Tests mithilfe des Cmdlets [Start-AzMigrateTestMigrationCleanup](/powershell/module/az.migrate/start-azmigratetestmigrationcleanup). Das Cmdlet gibt einen Auftrag zurück, der zur Überwachung des Vorgangsstatus nachverfolgt werden kann.

```azurepowershell-interactive
# Clean-up test migration for a replicating server
$CleanupTestMigrationJob = Start-AzMigrateTestMigrationCleanup -InputObject $ReplicatingServer
```
```azurepowershell-interactive
# Track job status to check for completion
while (($CleanupTestMigrationJob.State -eq "InProgress") -or ($CleanupTestMigrationJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $CleanupTestMigrationJob = Get-AzMigrateJob -InputObject $CleanupTestMigrationJob
}
# Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $CleanupTestMigrationJob.State
```

## <a name="12-migrate-vms"></a>12. Migrieren virtueller Computer

Nachdem Sie sich vergewissert haben, dass die Testmigration wie erwartet funktioniert, können Sie den replizierenden Server mithilfe des folgenden Cmdlets migrieren: Das Cmdlet gibt einen Auftrag zurück, der zur Überwachung des Vorgangsstatus nachverfolgt werden kann.

Soll der Quellserver nicht ausgeschaltet werden, lassen Sie den Parameter `TurnOffSourceServer` weg.

```azurepowershell-interactive
# Start migration for a replicating server and turn off source server as part of migration
$MigrateJob = Start-AzMigrateServerMigration -InputObject $ReplicatingServer -TurnOffSourceServer
```
```azurepowershell-interactive
# Track job status to check for completion
while (($MigrateJob.State -eq 'InProgress') -or ($MigrateJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $MigrateJob.State
```

## <a name="13-complete-the-migration"></a>13. Fertigstellen der Migration

1. Beenden Sie nach Abschluss der Migration die Replikation für den lokalen Computer, und bereinigen Sie die Informationen zum Replikationszustand für den virtuellen Computer mithilfe des folgenden Cmdlets: Das Cmdlet gibt einen Auftrag zurück, der zur Überwachung des Vorgangsstatus nachverfolgt werden kann.

   ```azurepowershell-interactive
   # Stop replication for a migrated server
   $StopReplicationJob = Remove-AzMigrateServerReplication -InputObject $ReplicatingServer
   ```
   ```azurepowershell-interactive
   # Track job status to check for completion
   while (($StopReplicationJob.State -eq 'InProgress') -or ($StopReplicationJob.State -eq 'NotStarted')){
           #If the job hasn't completed, sleep for 10 seconds before checking the job status again
           sleep 10;
           $StopReplicationJob = Get-AzMigrateJob -InputObject $StopReplicationJob
   }
   # Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
   Write-Output $StopReplicationJob.State
   ```

1. Installieren Sie den [Linux](../virtual-machines/extensions/agent-linux.md)-Agent auf den migrierten Computern, wenn der Computer über ein Linux-Betriebssystem verfügt. Der VM-Agent für Windows-VMs wird bei der Migration automatisch installiert.
1. Führen Sie App-Anpassungen nach der Migration durch, z.B. die Aktualisierung von Datenbankverbindungszeichenfolgen und Webserverkonfigurationen.
1. Führen Sie endgültige Anwendungs- und Migrationsakzeptanztests für die migrierte Anwendung durch, die nun in Azure ausgeführt wird.
1. Leiten Sie den Datenverkehr auf die migrierte Instanz der Azure-VM um.
1. Entfernen Sie die lokalen VMs aus Ihrem lokalen VM-Bestand.
1. Entfernen Sie die lokalen VMs aus lokalen Sicherungen.
1. Aktualisieren Sie die interne Dokumentation zum Anzeigen des neuen Speicherorts und der IP-Adresse der Azure-VMs.

## <a name="14-post-migration-best-practices"></a>14. Bewährte Methoden nach der Migration

- Beachten Sie zur Steigerung der Resilienz Folgendes:
    - Schützen Sie Daten, indem Sie Azure-VMs mit dem Azure Backup-Dienst sichern. [Weitere Informationen](../backup/quick-backup-vm-portal.md)
    - Sorgen Sie für die kontinuierliche Ausführung und Verfügbarkeit von Workloads, indem Sie Azure-VMs mithilfe von Site Recovery in eine sekundäre Region replizieren. [Weitere Informationen](../site-recovery/azure-to-azure-tutorial-enable-replication.md)
- Beachten Sie zur Steigerung der Sicherheit Folgendes:
    - Sperren und beschränken Sie den Zugriff von eingehendem Datenverkehr mit der [Just-In-Time-Verwaltung in Microsoft Defender für Cloud](../security-center/security-center-just-in-time.md).
    - Beschränken Sie den Netzwerkdatenverkehr mithilfe von [Netzwerksicherheitsgruppen](../virtual-network/network-security-groups-overview.md) auf Verwaltungsendpunkte.
    - Stellen Sie [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) bereit, um Datenträger und Daten vor Diebstahl und unbefugtem Zugriff zu schützen.
    - Erfahren Sie mehr über das [Schützen von IaaS-Ressourcen](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/), und besuchen Sie die Website [Microsoft Defender für Cloud](https://azure.microsoft.com/services/security-center/).
- Beachten Sie zur Überwachung und Verwaltung Folgendes:
-  Ziehen Sie die Bereitstellung von [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md) in Erwägung, um den Ressourceneinsatz und die Ausgaben zu überwachen.
