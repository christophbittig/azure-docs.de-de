---
title: Bereitstellen eines Azure-Datenträgerpools (Vorschau)
description: Hier wird beschrieben, wie Sie einen Azure-Datenträgerpool bereitstellen.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: ignite-fall-2021
ms.openlocfilehash: c4c010d1c142a2f2e09c0a60122f446ab7df6530
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132135386"
---
# <a name="deploy-an-azure-disk-pool-preview"></a>Bereitstellen eines Azure-Datenträgerpools (Vorschau)

In diesem Artikel wird beschrieben, wie Sie einen Azure-Datenträgerpool (Vorschau) bereitstellen und konfigurieren. Vor der Bereitstellung eines Datenträgerpools sollten Sie die Artikel zu [Konzepten](disks-pools.md) und [Planung](disks-pools-planning.md) lesen.

Für eine ordnungsgemäße Funktionsweise eines Datenträgerpools müssen die folgenden Schritte ausgeführt werden:
- Registrieren Sie Ihr Abonnement für die Vorschau.
- Delegieren Sie ein Subnetz an Ihren Datenträgerpool.
- Weisen Sie dem Ressourcenanbieter des Datenträgerpools RBAC-Berechtigungen (rollenbasierte Zugriffssteuerung) für die Verwaltung Ihrer Datenträgerressourcen zu.
- Erstellen Sie den Datenträgerpool.
    - Fügen Sie Ihrem Datenträgerpool Datenträger hinzu.


## <a name="prerequisites"></a>Voraussetzungen

Für eine erfolgreiche Bereitstellung eines Datenträgerpools benötigen Sie Folgendes:

- eine Gruppe verwalteter Datenträger, die Sie einem Datenträgerpool hinzufügen möchten.
- ein virtuelles Netzwerk mit einem dedizierten Subnetz, das für Ihren Datenträgerpool bereitgestellt wird.
    - Die ausgehenden Ports 53, 443 und 5671 müssen geöffnet sein.
    - Stellen Sie sicher, dass Ihre Netzwerkeinstellungen keine der erforderlichen ausgehenden Abhängigkeiten Ihres Datenträgerpools blockiert. Sie können entweder das [Azure PowerShell-Modul](/powershell/module/az.diskpool/get-azdiskpooloutboundnetworkdependencyendpoint?view=azps-6.6.0) oder [Azure CLI,](/cli/azure/disk-pool?view=azure-cli-latest#az_disk_pool_list_outbound_network_dependency_endpoint) um die vollständige Liste aller ausgehenden Abhängigkeiten zu erhalten.

Wenn Sie das Azure PowerShell-Modul verwenden möchten, installieren Sie [Version 6.1.0 oder höher](/powershell/module/az.diskpool/?view=azps-6.1.0&preserve-view=true).

Falls Sie die Azure CLI verwenden möchten, installieren Sie [die aktuelle Version](/cli/azure/disk-pool).

## <a name="register-your-subscription-for-the-preview"></a>Registrieren Ihres Abonnements für die Vorschau

Registrieren Sie Ihr Abonnement beim **Microsoft.StoragePool**-Anbieter, um Datenträgerpools erstellen und verwenden zu können.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Wählen Sie im Azure-Portal den Menüeintrag **Abonnements** aus.
1. Wählen Sie das Abonnement aus, das Sie für Datenträgerpools verwenden möchten.
1. Wählen Sie im linken Menü unter **Einstellungen** die Option **Ressourcenanbieter** aus.
1. Suchen Sie nach dem Ressourcenanbieter **Microsoft.StoragePool**, und wählen Sie **Registrieren** aus.

Nachdem Ihr Abonnement registriert wurde, können Sie einen Datenträgerpool bereitstellen.

## <a name="delegate-subnet-permission"></a>Delegieren einer Subnetzberechtigung

Damit Ihr Datenträgerpool mit Ihren Clientcomputern funktioniert, müssen Sie ein Subnetz an Ihren Azure-Datenträgerpool delegieren. Beim Erstellen eines Datenträgerpools geben Sie ein virtuelles Netzwerk und das delegierte Subnetz an. Sie können entweder ein neues Subnetz erstellen oder ein vorhandenes Subnetz verwenden und an den Ressourcenanbieter **Microsoft.StoragePool/diskPools** delegieren.

1. Wechseln Sie im Azure-Portal zum Bereich für virtuelle Netzwerke, und wählen Sie das virtuelle Netzwerk aus, das für den Datenträgerpool verwendet werden soll.
1. Wählen Sie im Bereich „Virtuelles Netzwerk“ den Eintrag **Subnetze** und dann **+Subnetz** aus.
1. Erstellen Sie ein neues Subnetz, indem Sie die folgenden erforderlichen Felder im Bereich **Subnetz hinzufügen** ausfüllen: – „Subnetzdelegierung“: Wählen Sie „Microsoft.StoragePool“ aus.

Weitere Informationen zur Subnetzdelegierung finden Sie unter [Hinzufügen oder Entfernen einer Subnetzdelegierung](../virtual-network/manage-subnet-delegation.md).

## <a name="assign-storagepool-resource-provider-permissions"></a>Zuweisen von Berechtigungen für StoragePool-Ressourcenanbieter

Damit ein Datenträger in einem Datenträgerpool verwendet werden kann, muss er die folgenden Anforderungen erfüllen:

- Dem **StoragePool**-Ressourcenanbieter muss eine RBAC-Rolle mit den Berechtigungen **Lesen** und **Schreiben** für jeden verwalteten Datenträger im Datenträgerpool zugewiesen sein.
- Es muss sich entweder um einen SSD Premium-Datenträger, Standard-SSD-Datenträger oder einen Ultra-Datenträger in derselben Verfügbarkeitszone wie der Datenträgerpool handeln.
    - Bei Ultra-Datenträgern muss die Sektorgröße des Datenträgers 512 Byte betragen.
- Datenträgerpools können nicht so konfiguriert werden, dass sie sowohl Premium-/Standard-SSDs als auch Ultra-Datenträger enthalten. Ein für Ultra-Datenträger konfigurierter Datenträgerpool kann nur Ultra-Datenträger enthalten. Ebenso kann ein Datenträgerpool, der für Premium- oder Standard-SSDs konfiguriert ist, nur Premium- und Standard-SSDs enthalten.
- Es muss sich um einen freigegebenen Datenträger mit einem maxShares-Wert von mindestens 2 handeln.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Suchen Sie nach der Ressourcengruppe mit den Datenträgern, und wählen Sie sie aus, oder wählen Sie die einzelnen Datenträger aus.
1. Wählen Sie die Option **Zugriffssteuerung (IAM)** aus.
1. Wählen Sie in der Rollenauflistung **Rollenzuweisung hinzufügen (Vorschau)** und dann **Datenträgerpooloperator** aus.

    Alternativ können Sie auch eine benutzerdefinierte Rolle erstellen. Benutzerdefinierte Rollen für Datenträgerpools benötigen die folgenden RBAC-Berechtigungen: **Microsoft.Compute/disks/write** und **Microsoft.Compute/disks/read**.

1. Wählen Sie für **Zugriff zuweisen zu** die Option **Benutzer, Gruppe oder Dienstprinzipal** aus.
1. Wählen Sie **+ Mitglieder auswählen** aus, und suchen Sie dann nach **StoragePool Resource Provider** (StoragePool-Ressourcenanbieter). Wählen Sie diesen Ressourcenanbieter aus, und speichern Sie Ihre Auswahl.

## <a name="create-a-disk-pool"></a>Erstellen eines Datenträgerpools

Für eine optimale Leistung stellen Sie den Datenträgerpool in derselben Verfügbarkeitszone bereit wie Ihre Clients. Wenn Sie einen Datenträgerpool für eine Azure VMware Solution-Cloud bereitstellen und Anleitungen zum Ermitteln der Verfügbarkeitszone benötigen, füllen Sie dieses [Formular](https://aka.ms/DiskPoolCollocate) aus.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Suchen Sie nach **Datenträgerpool**, und wählen Sie diese Option aus.
1. Wählen Sie **+ Hinzufügen** aus, um einen neuen Datenträgerpool zu erstellen.
1. Geben Sie die angeforderten Details ein, und wählen Sie die Region und Verfügbarkeitszone der Clients aus, die den Datenträgerpool verwenden sollen.
1. Wählen Sie das Subnetz, das an den **StoragePool**-Ressourcenanbieter delegiert wurde, sowie das zugehörige virtuelle Netzwerk aus.
1. Wählen Sie **Weiter** aus, um Datenträger zu Ihrem Datenträgerpool hinzuzufügen.

    :::image type="content" source="media/disks-pools-deploy/create-a-disk-pool.png" alt-text="Screenshot des grundlegenden Bereichs zum Erstellen eines Datenträgerpools.":::

### <a name="add-disks"></a>Fügt Datenträger hinzu

#### <a name="prerequisites"></a>Voraussetzungen

Datenträger müssen die folgenden Anforderungen erfüllen, um hinzugefügt werden zu können:

- Es muss sich entweder um einen SSD Premium-Datenträger, Standard-SSD-Datenträger oder einen Ultra-Datenträger in derselben Verfügbarkeitszone wie der Datenträgerpool handeln.
    - Aktuell können im Portal ausschließlich SSD Premium-Datenträger und Standard-SSD-Datenträger hinzugefügt werden. Ultra-Datenträger müssen entweder mit dem Azure PowerShell-Modul oder über die Azure CLI hinzugefügt werden.
    - Bei Ultra-Datenträgern muss die Sektorgröße des Datenträgers 512 Byte betragen.
- Es muss sich um einen freigegebenen Datenträger mit einem maxShares-Wert von mindestens 2 handeln.
- Datenträgerpools können nicht so konfiguriert werden, dass sie sowohl Premium-/Standard-SSDs als auch Ultra-Datenträger enthalten. Ein für Ultra-Datenträger konfigurierter Datenträgerpool kann nur Ultra-Datenträger enthalten. Ebenso kann ein Datenträgerpool, der für Premium- oder Standard-SSDs konfiguriert ist, nur Premium- und Standard-SSDs enthalten.
- Sie müssen dem Ressourcenanbieter des Datenträgerpools RBAC-Berechtigungen zur Verwaltung des Datenträgers erteilen, der hinzugefügt werden soll.

Wenn Ihr Datenträger diese Anforderungen erfüllt, können Sie ihn zu einem Datenträgerpool hinzufügen. Wählen Sie dazu im Bereich des Datenträgerpools die Option **+Datenträger hinzufügen** aus.

:::image type="content" source="media/disks-pools-deploy/create-a-disk-pool-disks-blade.png" alt-text="Screenshot des Datenträgerbereichs zum Erstellen eines Datenträgerpools mit hervorgehobener Option zum Hinzufügen eines Datenträgers.":::

### <a name="enable-iscsi"></a>Aktivieren von iSCSI

1. Wählen Sie den Bereich **iSCSI** aus.
1. Wählen Sie **iSCSI aktivieren** aus.
1. Geben Sie den Namen des iSCSI-Ziels ein. Der iSCSI-Ziel-IQN wird basierend auf diesem Namen generiert.
    - Der ACL-Modus ist standardmäßig auf **Dynamisch** festgelegt. Wenn Ihr Datenträgerpool als Speicherlösung für Azure VMware Solution verwendet werden soll, muss der ACL-Modus auf **Dynamisch** festgelegt sein.
1. Klicken Sie auf **Überprüfen + erstellen**.

    :::image type="content" source="media/disks-pools-deploy/create-a-disk-pool-iscsi-blade.png" alt-text="Screenshot des iSCSI-Bereichs zum Erstellen eines Datenträgerpools.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Mit diesem Skript werden die folgenden Schritte ausgeführt:
- Installation des erforderlichen Moduls zum Erstellen und Verwenden von Datenträgerpools.
- Erstellen eines Datenträgers und Zuweisen von RBAC-Berechtigungen zu diesem Datenträger. Wenn Sie diesen Schritt bereits ausgeführt haben, können Sie diese Abschnitte des Skripts auskommentieren.
- Erstellen eines Datenträgerpools und Hinzufügen des Datenträgers zu diesem Pool.
- Erstellen und Aktivieren eines iSCSI-Ziels.

Ersetzen Sie die Variablen in diesem Skript vor der Ausführung durch Ihre eigenen Variablen. Wenn Sie das Formular für Ultra-Datenträger ausgefüllt haben, muss das Skript ebenfalls geändert werden, um einen vorhandenen Ultra-Datenträger zu verwenden.

```azurepowershell
# Install the required module for Disk Pool
Install-Module -Name Az.DiskPool -RequiredVersion 0.3.0 -Repository PSGallery

# Sign in to the Azure account and setup the variables
$subscriptionID = "<yourSubID>"
Set-AzContext -Subscription $subscriptionID
$resourceGroupName= "<yourResourceGroupName>"
$location = "<desiredRegion>"
$diskName = "<desiredDiskName>"
$availabilityZone = "<desiredAvailabilityZone>"
$subnetId='<yourSubnetID>'
$diskPoolName = "<desiredDiskPoolName>"
$iscsiTargetName = "<desirediSCSITargetName>" # This will be used to generate the iSCSI target IQN name
$lunName = "<desiredLunName>"

# You can skip this step if you have already created the disk and assigned proper RBAC permission to the resource group the disk is deployed to
$diskconfig = New-AzDiskConfig -Location $location -DiskSizeGB 1024 -AccountType Premium_LRS -CreateOption Empty -zone $availabilityZone -MaxSharesCount 2
$disk = New-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName -Disk $diskconfig
$diskId = $disk.Id
$scopeDef = "/subscriptions/" + $subscriptionId + "/resourceGroups/" + $resourceGroupName
$rpId = (Get-AzADServicePrincipal -SearchString "StoragePool Resource Provider").id

New-AzRoleAssignment -ObjectId $rpId -RoleDefinitionName "Virtual Machine Contributor" -Scope $scopeDef

# Create a Disk Pool
# If you want to create a disk pool configured for ultra disks, add -AdditionalCapability "DiskPool.Disk.Sku.UltraSSD_LRS" to the command
New-AzDiskPool -Name $diskPoolName -ResourceGroupName $resourceGroupName -Location $location -SubnetId $subnetId -AvailabilityZone $availabilityZone -SkuName Standard_S1
$diskpool = Get-AzDiskPool -ResourceGroupName $resourceGroupName -Name $DiskPoolName

# Add disks to the Disk Pool
Update-AzDiskPool -ResourceGroupName $resourceGroupName -Name $diskPoolName -DiskId $diskId
$lun = New-AzDiskPoolIscsiLunObject -ManagedDiskAzureResourceId $diskId -Name $lunName

# Create an iSCSI Target and expose the disks as iSCSI LUNs
New-AzDiskPoolIscsiTarget -DiskPoolName $diskPoolName -Name $iscsiTargetName -ResourceGroupName $resourceGroupName -Lun $lun -AclMode Dynamic

Write-Output "Print details of the iSCSI target exposed on Disk Pool"

Get-AzDiskPoolIscsiTarget -name $iscsiTargetName -DiskPoolName $diskPoolName -ResourceGroupName $resourceGroupName | fl
```


# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Mit diesem Skript werden die folgenden Schritte ausgeführt:
- Installation der erforderlichen Erweiterung zum Erstellen und Verwenden von Datenträgerpools.
- Erstellen eines Datenträgers und Zuweisen von RBAC-Berechtigungen zu diesem Datenträger. Wenn Sie diesen Schritt bereits ausgeführt haben, können Sie diese Abschnitte des Skripts auskommentieren.
- Erstellen eines Datenträgerpools und Hinzufügen des Datenträgers zu diesem Pool.
- Erstellen und Aktivieren eines iSCSI-Ziels.

Ersetzen Sie die Variablen in diesem Skript vor der Ausführung durch Ihre eigenen Variablen. Wenn Sie das Formular für Ultra-Datenträger ausgefüllt haben, muss das Skript ebenfalls geändert werden, um einen vorhandenen Ultra-Datenträger zu verwenden.

```azurecli
# Add disk pool CLI extension
az extension add -n diskpool

#az extension add -s https://azcliprod.blob.core.windows.net/cli-extensions/diskpool-0.2.0-py3-none-any.whl

#Select subscription
az account set --subscription "<yourSubscription>"

##Initialize input parameters
resourceGroupName='<yourRGName>'
location='<desiredRegion>'
zone=<desiredZone>
subnetId='<yourSubnetID>'
diskName='<desiredDiskName>'
diskPoolName='<desiredDiskPoolName>'
targetName='<desirediSCSITargetName>'
lunName='<desiredLunName>'

#You can skip this step if you have already created the disk and assigned permission in the prerequisite step. Below is an example for premium disks.
az disk create --name $diskName --resource-group $resourceGroupName --zone $zone --location $location --sku Premium_LRS --max-shares 2 --size-gb 1024

#You can deploy all your disks into one resource group and assign StoragePool Resource Provider permission to the group
storagePoolObjectId=$(az ad sp list --filter "displayName eq 'StoragePool Resource Provider'" --query "[0].objectId" -o json)
storagePoolObjectId="${storagePoolObjectId%"}"
storagePoolObjectId="${storagePoolObjectId#"}"

az role assignment create --assignee-object-id $storagePoolObjectId --role "Virtual Machine Contributor" --resource-group $resourceGroupName

#Create a disk pool
#To create a disk pool configured for ultra disks, add --additional-capabilities "DiskPool.Disk.Sku.UltraSSD_LRS" to your command
az disk-pool create --name $diskPoolName \
--resource-group $resourceGroupName \
--location $location \
--availability-zones $zone \
--subnet-id $subnetId \
--sku name="Standard_S1" \

#Initialize an iSCSI target. You can have 1 iSCSI target per disk pool
az disk-pool iscsi-target create --name $targetName \
--disk-pool-name $diskPoolName \
--resource-group $resourceGroupName \
--acl-mode Dynamic

#Add the disk to disk pool
diskId=$(az disk show --name $diskName --resource-group $resourceGroupName --query "id" -o json)
diskId="${diskId%"}"
diskId="${diskId#"}"

az disk-pool update --name $diskPoolName --resource-group $resourceGroupName --disks $diskId

#Expose disks added in the Disk Pool as iSCSI Lun 
az disk-pool iscsi-target update --name $targetName \
 --disk-pool-name $diskPoolName \
 --resource-group $resourceGroupName \
 --luns name=$lunName managed-disk-azure-resource-id=$diskId
```
---

## <a name="next-steps"></a>Nächste Schritte

- Wenn beim Bereitstellen eines Datenträgerpools Probleme auftreten, finden Sie weitere Informationen unter [Problembehandlung für Azure-Datenträgerpools (Vorschau)](disks-pools-troubleshoot.md).
- [Anfügen von Datenträgerpools an Azure VMware Solution-Hosts (Vorschau)](../azure-vmware/attach-disk-pools-to-azure-vmware-solution-hosts.md).
- [Verwalten eines Datenträgerpools](disks-pools-manage.md).
